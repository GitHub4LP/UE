# Log
## Color Control
1. Log
2. Warning
3. Error
## Build-in **LogTemp** Category
```c++
UE_LOG(LogTemp, Warning, TEXT(""));
```
## **Custom** Category
### .h
```c++
DECLARE_LOG_CATEGORY_EXTERN(LogMy, Log, All);
```
### .cpp
```c++
DEFINE_LOG_CATEGORY_STATIC(LogMy, Warning, All)
```
### Usage
```c++
UE_LOG(LogMy, Warning, TEXT(""));
```
1. FString. ( %s strings are wanted as TCHAR* by Log, so use *FString())
    ```c++
    UE_LOG(LogMy, Warning, TEXT(" %s "), *FString);
    ```
2. int
    ```c++
    %d or %i
    ```
3. float
    ```c++
    %f
    ```
4. FVector, FName, -> FString
    ```c++
    *<Var>.ToString()
## Other
1. OnScreenDebugMessage
```c++
GEngine->AddOnScreenDebugMessage(-1, 5.f, FColor::Red, TEXT(""));
```
2. FError
```c++
FError::Throwf(TEXT(""));
```
3. FMessageDialog
```c++
FMessageDialog::Open(EAppMsgType::Ok, FText::FromString(TEXT("")));
```

