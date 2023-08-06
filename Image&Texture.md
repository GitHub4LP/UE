# Image&Texture
## Load Data from File
### Check File Exist
```c++
#include "PlatformFilemanager.h"
```
> ```c++
>FString InFileName;
>```
```c++
FPlatformFileManager::Get().GetPlatformFile().FileExists(*InFileName)
```
### Load Data to TArray from File
```c++
#include "FileHelper.h"
```

```c++
TArray<uint8> InData;
```
```c++
FFileHelper::LoadFileToArray(InData, *InFileName)
```
## Parse Format
### Module Manager
```c++
#include "ModuleManager.h"
```
### Load IImageWrapperModule
```c++
#include "ImageWrapper/Public/IImageWrapperModule.h"
```
```c++
IImageWrapperModule& ImageWrapperModule = FModuleManager::LoadModuleChecked<IImageWrapperModule>(FName("ImageWrapper"));
```
### IImageWrapper
```c++
#include "ImageWrapper/Public/IImageWrapper.h"
```
```c++
TSharedPtr<IImageWrapper> ImageWrapper = ImageWrapperModule.CreateImageWrapper(EImageFormat::PNG);
```
### Data
```c++
ImageWrapper->SetCompressed(InData.GetData(),LoadData.Num())
ImageWrapper->GetRaw(ERGBFormat::RGBA,8,Data)
```
```c++
int32 Width, Height;
 ```
```c++
Height = ImageWrapper->GetHeight();
Width = ImageWrapper->GetWidth();
```

## Save
```c++
ImageWrapper->SetRaw(Data->GetData(),Data->Num(),Width,Height,ERGBFormat::RGBA,8)
```
```c++
TArray<uint8> InData;
```
```c++
OutData = ImageWrapper->GetCompressed()
```
```c++
#include "FileManagerGeneric.h"
```
```c++
FFileManagerGeneric::Get().DirectoryExists(*OutFile)
```
```c++
#include "Paths.h"
```
```c++
FFileManagerGeneric::Get().MakeDirectory(*FPaths::GetPath(OutFile), true)
```
```c++
FFileHelper::SaveArrayToFile(OutData, *OutFile)
```

```c++
TArray<FColor> uint8ToFColor(const TArray<uint8> origin)
{
	TArray<FColor> uncompressedFColor;
	FColor auxDst;
	for (int i = 0; i < origin.Num(); i++) 
	{
		auxDst.R = origin[i];
		i++;
		auxDst.G = origin[i];
		i++;
		auxDst.B = origin[i];
		i++;
		auxDst.A = origin[i];
		uncompressedFColor.Add(auxDst);
	}
	return uncompressedFColor;
}
```

```c++

bool PNG2JPG(const FString PNGFile, const FString JPGFile)
{
	check(PNGFile.EndsWith(TEXT(".png")) && JPGFile.EndsWith(TEXT(".jpg")));
	TArray<uint8> PNGData;
	if (!CheckFileAndLoadData(PNGFile, PNGData)) { return false; }
	IImageWrapperModule& ImageWrapperModule = FModuleManager::LoadModuleChecked<IImageWrapperModule>(FName("ImageWrapper"));
	TSharedPtr<IImageWrapper> PNGWrapper = ImageWrapperModule.CreateImageWrapper(EImageFormat::PNG);
	if (PNGWrapper&&PNGWrapper->SetCompressed(PNGData.GetData(),PNGData.Num()))
	{
		const TArray<uint8>* Data = nullptr;
		if (PNGWrapper->GetRaw(ERGBFormat::RGBA, 8, Data))
		{
			TSharedPtr<IImageWrapper> JPGWrapper = ImageWrapperModule.CreateImageWrapper(EImageFormat::JPEG);
			if (JPGWrapper->SetRaw(
				Data->GetData(),
				Data->Num(),
				PNGWrapper->GetWidth(),
				PNGWrapper->GetHeight(),
				ERGBFormat::RGBA,
				8
			))
			{
				if (!FFileManagerGeneric::Get().DirectoryExists(*JPGFile))
				{
					FFileManagerGeneric::Get().MakeDirectory(*FPaths::GetPath(JPGFile), true);
				}
				TArray<uint8> JPGData;
				JPGData = JPGWrapper->GetCompressed();
				return FFileHelper::SaveArrayToFile(JPGData, *JPGFile);
			}
		}
	}
	return false;
}
```
