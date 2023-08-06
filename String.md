TCHAR 
UCS-2 | UTF-16

```c++
FString::Printf(TEXT("%05d"), num);
```
```c++
FString = TEXT("")
```

## Regex
```c++
 FRegexPattern RegexPattern(Pattern);
 FRegexMatcher TextMatcher(RegexPattern, Target);
 if (TextMatcher.FindNext())
 {
	 
 }
```