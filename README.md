# Simon Says
[Live](http://nvizzutti.github.io/SimonLive)

[Raw Files](http://www.github.com/NVizzutti/SimonSays)

Basic simon says game developed in Unity 3D. The scripts for component classes, game UI, and game logic were written in C#.


## Game Logic 
The bulk of the game logic is contained within a single `GameManager` class inheriting from `MonoBehaviour` which is the case for all C# scripts in Unity. References to buttons are held in an Array, allowing for button activation from within the Game Manager. Unlike the buttons array, the game pattern is frequently updating and is therefore stored in a List.

Each round of the game begins by calling the `Coroutine` "Play", which allows Unity to pause execution of code for a defined duration, and continue where it left off. Each button referenced by the ID stored in `pattern` is activated, displaying the order to the user audibly and visually. 

```C#
	IEnumerator Play ()
	{
		pickIdx = 0;
		yield return new WaitForSeconds(delay);
		foreach (int patternIdx in pattern)
		{
			buttons[patternIdx].ActivateButton();
			yield return new WaitForSeconds(delay);
		}
			AddColor();
	}
```
As the pattern List grows in length, the defined delay is reduced by 20% each time, in order to increase difficulty. When the player clicks a button, the ID of compared to the ID stored at that index in the pattern List. If correct, the score is incremented and the pattern increases in length. 
```C#
	public void Choose(int chosen)
	{
		if(chosen == pattern[pickIdx])
		{
			pickIdx += 1;
			
			if(pickIdx == pattern.Count)
			{
				score.Increment();
				StartCoroutine("Play");
				delay = (delay * 0.8f);
			}
		}
		else
		{
			gameOverSound.Play();
			ResetGame();
		}
	}
```

If the game ends, the Start Menu component is activated tby invoking the `Activate` method on a stored reference. 
```C#
	void ResetGame()
	{
		pattern.Clear();
    	var scoreStr = score.GetComponent<Text>().text;
		retryText.text = string.Concat("Your score was ", scoreStr);
		panel.Activate();
		score.Reset();
		delay = 1.0f;
	}
  ```

