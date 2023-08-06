# File Operate

```c++
#include "PlatformFilemanager.h"
```
```c++
IPlatformFile& PlatformFile = FPlatformFileManager::Get().GetPlatformFile();
```
```c++
PlatformFile.OpenRead(*FileName, true)
```
```c++
FileHandle->Seek(index);
Tell()
```
```c++
FileHandle->Read(Bytes, ByteNum)
```