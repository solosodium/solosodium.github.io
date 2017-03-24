---
layout: post
title: FancySpeechBubble - A Speech Bubble Based On Unity UI Text
published: true
---

If you want to make a dialogue based game, the first task to tackle is making a decent looking speech bubble. (If you just want to check out the Unity code, fast forward [FancySpeechBubble](https://github.com/solosodium/FancySpeechBubble) on GitHub)

Recently, our artist asked me to implement a speech bubble for a game we are developing similar in style as the speech bubble you see in the game [Night in the Woods](http://www.nightinthewoods.com/). There are two main features for this particular style: **characters appear one by one**, and **a character shows up with a scale-up animation**.

![Night In The Woods screen shot, source: https://static2.gamespot.com/uploads/original/1197/11970954/2563818-screenshot2.png](/custom/2017-03-23-fancy-speech-bubble/nitw.png "Night In The Woods speech bubble")

*Image source: https://static2.gamespot.com/uploads/original/1197/11970954/2563818-screenshot2.png*

In the context of Unity, my first thought is using Unity's own [UI Text](https://docs.unity3d.com/ScriptReference/UI.Text.html) element, which comes in handy with rich text support. The fact that *Night in the Woods (NITW)* is developed with Unity makes me wonder: **How Hard Can It Be**?

![HHCIB](/custom/2017-03-23-fancy-speech-bubble/hhcib.gif "HHCIB")

As it turned out, pretty hard...

Before I go into details, I need to explain that I used a [ContentSizeFitter](https://docs.unity3d.com/Manual/script-ContentSizeFitter.html) with **Vertical Fit** set to **Preferred** on a UI Text with fixed **Font Size**. The reason for that is I found the **Best Fit** feature of the UI Text not reliable and the attached **TextGenerate** not spitting out any useful information about the rendered text.

The set up made sense to me and I went on and write a coroutine to add characters one by one to the UI Text element. However, that's when I encountered my first and biggest problem.

![default behavior](/custom/2017-03-23-fancy-speech-bubble/default_behavior.gif "Unity UI Text default behavior")

If you look closely at the word *"long"* in the above GIF, you will find that the word stayed on the first line when only *"lo"* showed up, and was bumped to the second line when more characters appeared. This is not how the speech bubble in NITW looks, and it will make character scale-up animation messy.

If you used [NGUI](https://www.assetstore.unity3d.com/en/#!/content/2413) in Unity before, you will know that NGUI's Label class has an attribute called **processedText**. Once you set the Label's text, it will store a processed version of the raw text with **newline character ('\n')** inserted where text is broken into lines, which could solve the problem.
 Unfortunately, there isn't a counterpart in Unity's UI Text, so I have to make one myself.

```csharp
_processedText = "";
string buffer = "";
string line = "";
float currentHeight = -1f;
// yes, sorry multiple spaces
foreach (string word in _rawText.Split(' ')) {
	buffer += word + " ";
	label.text = buffer;
	yield return new WaitForEndOfFrame();
	if (currentHeight < 0f) {
		currentHeight = label.rectTransform.sizeDelta.y;
	}
	if (currentHeight != label.rectTransform.sizeDelta.y) {
		currentHeight = label.rectTransform.sizeDelta.y;
		_processedText += line.TrimEnd(' ') + "\n";
		line = "";
	}
	line += word + " ";
}
_processedText += line;
```

The idea is pretty simple. I basically break down the text into words by using a space delimiter (yes, I know this will ignore multiple spaces cases, but it's not important), and add them back to UI Text one by one while constantly checking if the height of the UI Text is changed. A changed height simply means current word belongs to the next line, so I add a '\n' before the word. There is a catch though, before I can check the height of UI Text, I do have to wait for a frame for the it to properly update the value, which is why this snippet of code has to run within a coroutine.

Now that I have a working copy of processedText, I moved on to implement the main features of the speech bubble, namely the character shows up and scale-up animation. Thankfully, UI Text does have the rich text support, which means I can use ``` <size=20>c</size> ``` to set one character's font size.

```csharp
private IEnumerator CharacterAnimation () 
{
	// prepare target
	Text label = GetComponent<Text>();
	
	// go through character in processed text
	string prefix = "";
	foreach (char c in _processedText.ToCharArray()) {
		// animate character size
		int size = characterStartSize;
		while (size < label.fontSize) {
			size += (int)(Time.deltaTime * characterAnimateSpeed);
			size = Mathf.Min(size, label.fontSize);
			label.text = prefix + "<size=" + size + ">" + c + "</size>";
			yield return new WaitForEndOfFrame();
		}
		prefix += c;
	}

	// set processed text
	label.text = _processedText;
}
```

All is well right? Not yet. There is one final things. Remember I have a ContentSizeFitter on the UI Text to handle the height fitting. When the above coroutine is running, the height will be always changing. As a result, an already printed line will be bumped up when the next line appears. My solution is enable content fitting when I test fit the words and disable it during character animation. Combined with previously mentioned processedText generation and some other minor stuff. Here is the complete coroutine that does the test fitting.

```csharp
private IEnumerator TestFit () 
{
	// prepare targets
	Text label = GetComponent<Text>();
	ContentSizeFitter fitter = GetComponent<ContentSizeFitter>();

	// change label alpha to zero to hide test fit
	float alpha = label.color.a;
	label.color = new Color(label.color.r, label.color.g, label.color.b, 0f);

	// configure fitter and set label text so label can auto resize height
	fitter.horizontalFit = ContentSizeFitter.FitMode.Unconstrained;
	fitter.verticalFit = ContentSizeFitter.FitMode.PreferredSize;
	label.text = _rawText;

	// need to wait for a frame before label's height is updated
	yield return new WaitForEndOfFrame();
	// make sure label is anchored to center to measure the correct height
	float totalHeight = label.rectTransform.sizeDelta.y;

	// (OPTIONAL) set bubble background
	if (bubbleBackground != null) {
		bubbleBackground.rectTransform.sizeDelta = new Vector2(
			bubbleBackground.rectTransform.sizeDelta.x, 
			Mathf.Max(totalHeight + backgroundVerticalMargin, backgroundMinimumHeight));
	}

	// now it's time to test word by word
	_processedText = "";
	string buffer = "";
	string line = "";
	float currentHeight = -1f;
	// yes, sorry multiple spaces
	foreach (string word in _rawText.Split(' ')) {
		buffer += word + " ";
		label.text = buffer;
		yield return new WaitForEndOfFrame();
		if (currentHeight < 0f) {
			currentHeight = label.rectTransform.sizeDelta.y;
		}
		if (currentHeight != label.rectTransform.sizeDelta.y) {
			currentHeight = label.rectTransform.sizeDelta.y;
			_processedText += line.TrimEnd(' ') + "\n";
			line = "";
		}
		line += word + " ";
	}
	_processedText += line;

	// prepare fitter and label for character animation
	fitter.horizontalFit = ContentSizeFitter.FitMode.Unconstrained;
	fitter.verticalFit = ContentSizeFitter.FitMode.Unconstrained;
	label.text = "";
	label.rectTransform.sizeDelta = new Vector2(label.rectTransform.sizeDelta.x, totalHeight);
	label.color = new Color(label.color.r, label.color.g, label.color.b, alpha);
}
```

And this is the **final result**.

![preferred behavior](/custom/2017-03-23-fancy-speech-bubble/preferred_behavior.gif "Unity UI Text preferred behavior")

PS: you can get the code and example scene at [FancySpeechBubble](https://github.com/solosodium/FancySpeechBubble) repository on GitHub.
