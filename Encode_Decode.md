# UE4 编码

## 最简单的解决办法

修改系统编码到Unicode(UTF-8)

## 与系统编码转换

东亚语系编码通常是双字节(DB)

下面仅为windows

```c++
#include "CoreMinimal.h"


#if PLATFORM_WINDOWS
#include "Windows/AllowWindowsPlatformTypes.h"
#include "Windows/MinWindows.h"
#endif

FString uint8_to_TCHAR(const TArray<uint8>& InBuffer)
{
    FString Result;
    bool bNeedUEConvert = true;
    const uint8* Buffer = InBuffer.GetData();
    int32 Size = InBuffer.Num();
#ifdef PLATFORM_WINDOWS
    int32 UnicodeLen = ::MultiByteToWideChar(CP_ACP, 0, reinterpret_cast<const ANSICHAR*>(Buffer), Size, nullptr, 0);
    if (UnicodeLen > 0)
    {
        TArray<TCHAR>& ResultArray = Result.GetCharArray();
        ResultArray.Empty();
        ResultArray.AddUninitialized(UnicodeLen + 1); // +1 for the null terminator

        int32 WritedTCharLens = ::MultiByteToWideChar(CP_ACP, 0, reinterpret_cast<const ANSICHAR*>(Buffer), Size
                                                      , ResultArray.GetData(), UnicodeLen);
        if (WritedTCharLens > 0)
        {
            ResultArray[UnicodeLen] = TEXT('\0');
            bNeedUEConvert = false;
        }
        else
        {
            ResultArray.Empty();
        }
    }
#endif
    if (bNeedUEConvert)
    {
        FFileHelper::BufferToString(Result, Buffer, Size);
    }
    return Result;
}
```
