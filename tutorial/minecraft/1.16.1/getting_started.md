# Getting Started

Hello Minecraft programmers! Welcome to modification making for 1.16.1.

To get started, first you are going to need to download the following items:

## Required

### [Java Development Kit (JDK)](https://www.oracle.com/java/technologies/javase-downloads.html)

Minecraft was originally developed in a language called Java developed by the Oracle Corporation. Currently, the latest commercial release is for Java 8 while the latest development release is for Java 14. When deciding on a JDK version, I recommend using the latest commercial relase since its most likely the one everyone has on their computer. Either way, you need at least Java 8 in some capacity to be able to run the Forge development environment on your computer.

### Java Integrated Development Environment ([Eclipse](https://www.eclipse.org/downloads/packages/release/2020-06/r/eclipse-ide-enterprise-java-developers) or [IntelliJ IDEA](https://www.jetbrains.com/idea/download/))

Minecraft Forge is currently supported by gradle importation within either Eclipse or IntelliJ. For the purpose of these tutorials, I will be using Eclipse, however, either environment works for the situation.

### [Minecraft Forge](https://files.minecraftforge.net/maven/net/minecraftforge/forge/index_1.16.1.html)

When deciding which version of forge to use, I prefer going with the recommended version. However, if there is no recommended version suggest, the latest version should do just fine. Make sure to download the mdk onto your computer.

## Optional

### File Extractor

It is good to have a file extractor for .zip files to be able to extract the mdk onto your computer.

### Photo Editing Software

When creating assets, it is a good idea to use a photo editing software of some sort to make textures easier to create and edit.

## Starting Off

Now that you have downloaded a JDK 8+, Eclipse, and Forge 32.x.x, we can get started creating the workspace. Open the JDK .jar file and download the required contents. There is no need to move the default install location or change any of the download parameters.

### File Setup

First, you will need to extract the Forge mdk to a folder. Preferably you should have a default folder with the name of the mod with a subfolder inside of it which contains the extracted Forge mdk. So if you have a mod folder named `forgemod`, you should extract the mdk to `forgemod/version` where version is the `1.16.1-32.x.x` for the current release of forge:

```
forgemod
└── 1.16.1-32.x.x
	├── gradle
	│	└── wrapper
	│		├── gradle-wrapper.jar
	│		└── gradle-wrapper.properties
	├── src
	│	└── main
	│		├── java
	│		│	└── com
	│		│		└── example
	│		│			└── examplemod
	│		│				└── ExampleMod.java
	│		│
	│		└───resources
	│			├── pack.mcmeta
	│			└── META-INF
	│				└── mods.toml
	├── .gitattributes
	├── .gitignore
	├── build.gradle
	├── changelog.txt
	├── CREDITS.txt
	├── gradle.properties
	├── gradlew
	├── gradlew.bat
	├── LICENSE.txt
	└── README.txt
```

Also within that `forgemod` folder, you should create another subfolder titled workspace (e.g. `forgemod/workspace`). This will be where you will set up the environment workspace for all versions of forge you decide to create this `forgemod` for:

```
forgemod
├── 1.16.1-32.x.x
│	├── gradle
│	│	└── wrapper
│	│		├── gradle-wrapper.jar
│	│		└── gradle-wrapper.properties
│	├── src
│	│	└── main
│	│		├── java
│	│		│	└── com
│	│		│		└── example
│	│		│			└── examplemod
│	│		│				└── ExampleMod.java
│	│		│
│	│		└───resources
│	│			├── pack.mcmeta
│	│			└── META-INF
│	│				└── mods.toml
│	├── .gitattributes
│	├── .gitignore
│	├── build.gradle
│	├── changelog.txt
│	├── CREDITS.txt
│	├── gradle.properties
│	├── gradlew
│	├── gradlew.bat
│	├── LICENSE.txt
│	└── README.txt
└── workspace
```

For simplicity, I will condense down the file directory to those files that are necessary and can be copied over between projects:

```
forgemod
├── 1.16.1-32.x.x
│	├── gradle
│	├── src
│	├── build.gradle
│	├── gradle.properties
│	├── gradlew
│	└── gradlew.bat
└── workspace
```

### build.gradle

When setting up your project for the first time, you might want to  customize your mod information on build. For that, we have the `build.gradle` file:

```gradle
buildscript {
    repositories {
        maven { url = 'https://files.minecraftforge.net/maven' }
        jcenter()
        mavenCentral()
    }
    dependencies {
        classpath group: 'net.minecraftforge.gradle', name: 'ForgeGradle', version: '3.+', changing: true
    }
}
apply plugin: 'net.minecraftforge.gradle'
// Only edit below this line, the above code adds and enables the necessary things for Forge to be setup.
apply plugin: 'eclipse'
apply plugin: 'maven-publish'

version = '1.0'
group = 'com.yourname.modid' // http://maven.apache.org/guides/mini/guide-naming-conventions.html
archivesBaseName = 'modid'

sourceCompatibility = targetCompatibility = compileJava.sourceCompatibility = compileJava.targetCompatibility = '1.8' // Need this here so eclipse task generates correctly.

println('Java: ' + System.getProperty('java.version') + ' JVM: ' + System.getProperty('java.vm.version') + '(' + System.getProperty('java.vendor') + ') Arch: ' + System.getProperty('os.arch'))
minecraft {
    // The mappings can be changed at any time, and must be in the following format.
    // snapshot_YYYYMMDD   Snapshot are built nightly.
    // stable_#            Stables are built at the discretion of the MCP team.
    // Use non-default mappings at your own risk. they may not always work.
    // Simply re-run your setup task after changing the mappings to update your workspace.
    mappings channel: 'snapshot', version: '20200514-1.16'
    // makeObfSourceJar = false // an Srg named sources jar is made by default. uncomment this to disable.
    
    // accessTransformer = file('src/main/resources/META-INF/accesstransformer.cfg')

    // Default run configurations.
    // These can be tweaked, removed, or duplicated as needed.
    runs {
        client {
            workingDirectory project.file('run')

            // Recommended logging data for a userdev environment
            property 'forge.logging.markers', 'SCAN,REGISTRIES,REGISTRYDUMP'

            // Recommended logging level for the console
            property 'forge.logging.console.level', 'debug'

            mods {
                examplemod {
                    source sourceSets.main
                }
            }
        }

        server {
            workingDirectory project.file('run')

            // Recommended logging data for a userdev environment
            property 'forge.logging.markers', 'SCAN,REGISTRIES,REGISTRYDUMP'

            // Recommended logging level for the console
            property 'forge.logging.console.level', 'debug'

            mods {
                examplemod {
                    source sourceSets.main
                }
            }
        }

        data {
            workingDirectory project.file('run')

            // Recommended logging data for a userdev environment
            property 'forge.logging.markers', 'SCAN,REGISTRIES,REGISTRYDUMP'

            // Recommended logging level for the console
            property 'forge.logging.console.level', 'debug'

            args '--mod', 'examplemod', '--all', '--output', file('src/generated/resources/')

            mods {
                examplemod {
                    source sourceSets.main
                }
            }
        }
    }
}

dependencies {
    // Specify the version of Minecraft to use, If this is any group other then 'net.minecraft' it is assumed
    // that the dep is a ForgeGradle 'patcher' dependency. And it's patches will be applied.
    // The userdev artifact is a special name and will get all sorts of transformations applied to it.
    minecraft 'net.minecraftforge:forge:1.16.1-32.0.47'

    // You may put jars on which you depend on in ./libs or you may define them like so..
    // compile "some.group:artifact:version:classifier"
    // compile "some.group:artifact:version"

    // Real examples
    // compile 'com.mod-buildcraft:buildcraft:6.0.8:dev'  // adds buildcraft to the dev env
    // compile 'com.googlecode.efficient-java-matrix-library:ejml:0.24' // adds ejml to the dev env

    // The 'provided' configuration is for optional dependencies that exist at compile-time but might not at runtime.
    // provided 'com.mod-buildcraft:buildcraft:6.0.8:dev'

    // These dependencies get remapped to your current MCP mappings
    // deobf 'com.mod-buildcraft:buildcraft:6.0.8:dev'

    // For more info...
    // http://www.gradle.org/docs/current/userguide/artifact_dependencies_tutorial.html
    // http://www.gradle.org/docs/current/userguide/dependency_management.html

}

// Example for how to get properties into the manifest for reading by the runtime..
jar {
    manifest {
        attributes([
            "Specification-Title": "examplemod",
            "Specification-Vendor": "examplemodsareus",
            "Specification-Version": "1", // We are version 1 of ourselves
            "Implementation-Title": project.name,
            "Implementation-Version": "${version}",
            "Implementation-Vendor" :"examplemodsareus",
            "Implementation-Timestamp": new Date().format("yyyy-MM-dd'T'HH:mm:ssZ")
        ])
    }
}

// Example configuration to allow publishing using the maven-publish task
// This is the preferred method to reobfuscate your jar file
jar.finalizedBy('reobfJar') 
// However if you are in a multi-project build, dev time needs unobfed jar files, so you can delay the obfuscation until publishing by doing
//publish.dependsOn('reobfJar')

publishing {
    publications {
        mavenJava(MavenPublication) {
            artifact jar
        }
    }
    repositories {
        maven {
            url "file:///${project.projectDir}/mcmodsrepo"
        }
    }
}
```

As a quick note, we won't be covering all the information provided by gradle files or that forge has added. For that information you can either check out the [ForgeGradle Cookbook](https://forgegradle.readthedocs.io/en/latest/cookbook/) and the [Gradle Documentation](https://docs.gradle.org/current/userguide/userguide.html).

To start, let's go over the major customizations that should be included in your project:

```gradle
...
version = '1.0'
group = 'com.yourname.modid' // http://maven.apache.org/guides/mini/guide-naming-conventions.html
archivesBaseName = 'modid'
...
```

These three variables are the most common ones within `build.gradle` to edit:  
**version** - The version of the mod you are on. For this, you might want to follow the [Forge standard](https://mcforge.readthedocs.io/en/latest/conventions/versioning/) provided on their docs.  
**group** - The starting file directory of your mod. Usually this is done by taking the domain of a website you own, reversing it, and appending the mod id. In my case, the domain I use now is `championash5357.github.io` which would translate to `io.github.championash5357.tutorial` where `tutorial` is my current mod id.  
**archivesBaseName** - The mod id of the project.  

You should also replace every instance of `examplemod` within the file with your mod id as well.

The next thing we are going to take a look at is the `minecraft` section:

```gradle
...
minecraft {
    // The mappings can be changed at any time, and must be in the following format.
    // snapshot_YYYYMMDD   Snapshot are built nightly.
    // stable_#            Stables are built at the discretion of the MCP team.
    // Use non-default mappings at your own risk. they may not always work.
    // Simply re-run your setup task after changing the mappings to update your workspace.
    mappings channel: 'snapshot', version: '20200514-1.16'
    // makeObfSourceJar = false // an Srg named sources jar is made by default. uncomment this to disable.
    
    // accessTransformer = file('src/main/resources/META-INF/accesstransformer.cfg')

    // Default run configurations.
    // These can be tweaked, removed, or duplicated as needed.
    runs {
        client {
            workingDirectory project.file('run')

            // Recommended logging data for a userdev environment
            property 'forge.logging.markers', 'SCAN,REGISTRIES,REGISTRYDUMP'

            // Recommended logging level for the console
            property 'forge.logging.console.level', 'debug'

            mods {
                tutorial {
                    source sourceSets.main
                }
            }
        }

        server {
            workingDirectory project.file('run')

            // Recommended logging data for a userdev environment
            property 'forge.logging.markers', 'SCAN,REGISTRIES,REGISTRYDUMP'

            // Recommended logging level for the console
            property 'forge.logging.console.level', 'debug'

            mods {
                tutorial {
                    source sourceSets.main
                }
            }
        }

        data {
            workingDirectory project.file('run')

            // Recommended logging data for a userdev environment
            property 'forge.logging.markers', 'SCAN,REGISTRIES,REGISTRYDUMP'

            // Recommended logging level for the console
            property 'forge.logging.console.level', 'debug'

            args '--mod', 'dawnoffire', '--all', '--output', file('src/generated/resources/')

            mods {
                tutorial {
                    source sourceSets.main
                }
            }
        }
    }
}
...
```

Here we are going to look at three things:  
**mappings channel** - Holds the current instance of the mappings to use to debobfuscate the Minecraft source code. You can change the `version` value formatted by `YYYYMMDD-MCVERSION` to the current date or any mapping provided by [MCPBot](http://export.mcpbot.bspk.rs/). As of July 4th, 2020, Forge has been using a custom mapping system for 1.16 as they finish their new mapping system, so you shouldn't change this variable.  
**accessTransformer** - Allows you to set specific fields in the Minecraft source to different levels of access (e.g. public or protected). It provides a link to where the file is located within your current project. Uncomment this line if you would like to use access transformers within your project. I will be using reflection unless absolutely necessary, so I will leave this line commented.  
**data** - This section holds how the `runData` gradle task will execute. Append to the end of the `args` method `, '--existing', file('src/main/resources/')`. This will be useful later on when we start to go over [Data Generators](#).  

The final part of the file we will talk about briefly is the `dependencies` section. If you want to create mods that contain a dependency, you would use this section to compile and import them into your project. We will talk about this at a later time as well.

### The Terminal Line

To be able to generate the run configurations used by your mod, you will need to open up a command line to where your `forgemod/version` folder is. This can be obtained by either shift + right clicking in the folder and opening the command line or using `cd` to navigate there depending on your operating system.

Depending on what command line you use (CMD, PowerShell, Terminal), you will need a different variation of the command:  
**Windows Command Prompt** - `gradlew genEclipseRuns`  
**Windows PowerShell** - `./gradlew genEclipseRuns`  
**Mac Terminal** - `bash gradlew genEclipseRuns`  
If you are using IntelliJ instead, `genEclipseRuns` would be replaced with `genIntellijRuns`.

### Within the IDE

After executing the above commands, you will need to browse to your `forgemod/workspace` directory and launch it within your IDE. Depending on your IDE, you will need to do different steps to open your project workspace:  
**Eclipse** - Go to `File`->`Import...`->`Existing Gradle Project` and click next. You will see a box that says `Project root directory` to which you will browse to your `forgemod/version` and then click `Finish`.  
**IntelliJ** - Go to `File`->`Open` and navigate to your `forgemod/version` and click `OK`.
From there, wait for gradle to finish importing your project and viola, the workspace is setup!

## Additional Setup

After you have set up the workspace, there are some additional features that might make programming in Eclipse a tiny bit easier for you.

### Hierarchial Presentation

The default setting in Eclipse is to list all the packages by their full name such as `net.minecraft.inventory.CraftingInventory` and `net.minecraft.inventory.DoubleSidedInventory` instead of using a subdirectory system like: 
```
net
└── minecraft
	└── inventory
		├── CraftingInventory
		└── DoubleSidedInventory
```
To be able to set it up in this way, go to the left side of the screen where you will find the `Project Explorer`. To the right of the tab is a drop down arrow. Go to `Package Presentation` and switch it from `Flat` to `Hierarchial` and it will be much easier to find whatever you are looking for.

### Run Favorites

Since the system in 1.16.1 does not run the Eclipse setup through the mdk, it might be a bit more difficult to select the `runClient`, `runData`, and `runServer` application used to test your mod in game. To set these as your favorites to easily find them, go to the Green Play button underneath the toolbar and click the drop down arrow to the right. Click `Organize Favorites`->`Add...`->`Select All`->`Ok`->`Ok` to set the applications as your favorites for easy access.

### Removing TODO Auto Generated Messages

Sometimes when auto-completing a method, the TODO message auto generates within as a comment. To disable this, go to `Window`->`Preferences`->`Java`->`Code Style`->`Code Templates` and edit the `Method body`, `Constructor body`, and `Catch block body` to remove all instances of this message. Then click `Apply and Close` and you should no longer have any auto generated messages appearing when auto-completing.

All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial) under **Getting Started**.

Now that you have setup the workspace, it is now time to begin the [Main Mod File](./main_file).

Back to [Minecraft Tutorials](../)