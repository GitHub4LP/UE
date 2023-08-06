# Rename Project
## Duplicate
Duplicate the **OldName** project folder. Rename the duplicate folder to **NewName**.
Rename this OldName folder to NewName as well.
## Replace **.uproject**
1. Open the **NewName** folder.
2. Rename **OldName.uproject** to **NewName.uproject**.
3. Open **NewName.uproject** in a text editor, and replace all instances of **OldName** with **NewName**. Save and close the file.
5. If you have an **OldName.png** thumbnail, rename it to **NewName.png**.
## Replace **.Target.cs**
1. Open the **Source** folder.
2. Rename **OldName.Target.cs** to **NewName.Target.cs**.
3. Open **NewName.Target.cs**. Find all instances of **OldName** in this file with **NewName**. There may be some partial word matches. Save and close the file.
4. Repeat step _2_ and _3_ for **OldNameEditor.Target.cs** (renaming it to **NewNameEditor.Target.cs**.
## Rename **.Build.cs**
1. Open the **NewName** folder.
2. Rename **OldName.Build.cs** to **NewName.Build.cs**.
3. Open **NewName.Build.cs**. Find all instances of **OldName** in this file with **NewName**. Save and close the file.
## Rename Moudle
1. Go back to the main project folder, and right-click on the **NewName.uproject** file. Select *Generate Visual Studio Project* files. (If you don't have this option, run Engine/Binaries/Win64/UnrealVersionSelector-Win64-Shipping.exe once).
2. Open **NewName.sln**. Open **OldName.cpp**. Change ```IMPLEMENT_PRIMARY_GAME_MODULE( FDefaultGameModuleImpl, OldName, "OldName" );``` to ```IMPLEMENT_PRIMARY_GAME_MODULE( FDefaultGameModuleImpl, NewName, "NewName" );```
## Replace Config
1. Open the Config folder, and check all the configuration files for **OldName** references to change to **NewName**. For example, change ```GlobalDefaultGameMode=/Script/OldName.OldNameGameMode``` to ```GlobalDefaultGameMode=/Script/NewName.OldNameGameMode```.
2. Add the following to **DefaultEngine.ini** (under an existing or new [/Script/Engine.Engine] header): ```
+ActiveGameNameRedirects=(OldGameName="/Script/OldName", NewGameName="/Script/NewName")```
## Replace **API**(Dll)
If you have any **OLDNAME_API** in your project's header files, change those instances to **NEWNAME_API**.
## Clean up
Delete Saved and Intermediate folders in your project directory.
Compile, and your project should now open without errors.