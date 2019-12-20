# Getting Started

Hello Minecraft programmers! Welcome to modification making for 1.15.1. This guide is quite similar to the 1.14.4 version, so not much porting is needed. Still, I will help to guide you to create your modification for 1.15.1 and try to provide a useful source for updating your code to this version.

To get started, first you are going to need to download the following items:

## Required

### [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

Minecraft was originally developed in a language called Java developed by the Oracle Corporation. Currently, the latest commercial release is for Java 8 while the latest development release is for Java 13. When deciding on a JDK version, I recommend using the latest commercial relase since its most likely the one everyone has on their computer. Either way, you need at least Java 8 in some capacity to be able to run the Forge development environment on your computer.

### Java Integrated Development Environment ([Eclipse](https://www.eclipse.org/downloads/packages/release/2019-03/r/eclipse-ide-enterprise-java-developers) or [IntelliJ IDEA](https://www.jetbrains.com/idea/download/))

Minecraft Forge is currently supported by gradle importation within either Eclipse or IntelliJ. For the purpose of these tutorials, I will be using Eclipse, however, either environment works for the situation.

### [Minecraft Forge](https://files.minecraftforge.net/maven/net/minecraftforge/forge/index_1.15.1.html)

When deciding which version of forge to use, I prefer going with the recommended version. However, if there is no recommended version suggest, the latest version should do just fine. Make sure to download the mdk onto your computer.

## Optional

### File Extractor

It is good to have a file extractor for .zip files to be able to extract the mdk onto your computer.

### Photo Editing Software

When creating assets, it is a good idea to use a photo editing software of some sort to make textures easier to create and edit.

## Starting Off

Now that you have downloaded a JDK 8+, Eclipse, and Forge 30.0.1+, we can get started creating the workspace. First, open the JDK .jar file and download the required contents. There is no need to move the default install location or change any of the download parameters. 

Next, you will need to extract the Forge mdk to a folder. Preferably you should have a default folder with the name of the mod with a subfolder inside of it which contains the extracted Forge mdk. So if you have a mod folder named "forgemod", you should extract the mdk to "forgemod/version" where version is the number in between the Minecraft version and the text "mdk". Also within that "forgemod" folder, you should create another subfolder titled workspace e.g. "forgemod/workspace". This will be where you will set up the Eclipse environment workspace. Next, you will need to open up a command line to where that "forgemod/version" folder is. This can be obtained by either shift + right clicking in the folder and opening the command line or using "cd" to navigate there.

Depending on what command line you use (CMD, PowerShell, Terminal), you will need a different variation of the command. For the Windows Command Prompt, you will execute the command "gradlew genEclipseRuns" to set up the folder for Eclipse use. Using Windows PowerShell, the command is "./gradlew genEclipseRuns". For Mac Terminal, the command is "bash gradlew genEclipseRuns". If you are using IntelliJ instead, "genEclipseRuns" would be replaced with "genIntellijRuns".

After the workspace is successfully setup, you will now need to extract the Eclipse .zip file somewhere on your computer. It is recommended you keep all the files in a folder as to not mix them up with anything. After extraction, you will need to browse to your "forgemod/workspace" and launch it. Once this is finished, you will be greeted by the Getting Started page. You can middle click this tab in the center to close it and get into the actual workspace. From there, go to File->Import...->Existing Gradle Project and click next. You will see a box that says "Project root directory" to which you will browse to your "forgemod/version" and then click Finish. From there, wait for gradle to finish importing your project and viola, the workspace is setup!

## Additional Setup

After you have set up the workspace, there are some additional features that might make programming in Eclipse a tiny bit easier for you.

### Hierarchial Presentation

The default setting in Eclipse is to list all the packages by their full name such as "net.minecraft.init.Blocks" and "net.minecraft.init.Items" instead of using a subdirectory system like: net->minecraft->init->{Blocks, Items}. To be able to set it up in this way, go to the left side of the screen where you will find the Project Explorer. To the right of the tab is a drop down arrow. Go to Package Presentation and switch it from Flat to Hierarchial and it will be much easier to find whatever you are looking for.

### Run Favorites

Since the system in 1.15.1 does not run the Eclipse setup through the mdk, it might be a bit more difficult to select the runClient and runServer application used to test your mod in game. To set these as your favorites to easily find them, go to the Green Play button underneath the toolbar and click the drop down arrow to the right. Click Organize Run Favorites->Add...->Select All->Ok->Ok to set the applications as your favorites for easy access.

### Removing TODO Auto Generated Messages

Sometimes when auto-completing a method, the TODO message auto generates within as a comment. To disable this, go to Window->Preferences->Java->Code Style->Code Templates and Edit the Method body, Constructor body, and Catch block body to remove all instances of this message. Then click Apply and Close and you should no longer have any auto generated messages appearing when auto-completing.

Now that you have setup the workspace, it is now time to begin the [Main Mod File](https://championash5357.github.io/ChampionAsh5357/tutorial/minecraft/1.15.1/main_file).
