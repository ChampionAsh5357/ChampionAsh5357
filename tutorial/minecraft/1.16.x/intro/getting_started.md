# Getting Started
---

Hello Minecraft programmers! Welcome to modification making for 1.16.x.

If you have little to no experience in Java, please leave this tutorial and come back when you have some knowledge. All of the material covered here will assume you have a good understanding of Java and it's features/mechanics.

To get started, first you are going to need to download the following items:

## <a name="required"></a>Required <a href="#required"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

### <a name="jdk"></a>[Java Development Kit (JDK)](https://adoptopenjdk.net/) <a href="#jdk"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Minecraft is developed in Java 8. Although the latest release is for Java 15, Forge nor Minecraft were not developed with that version in mind and may not be compatible with everything provided in those versions. When deciding on a JDK version, I recommend using the Java 8 LTS release since it is the only version officially supported by Forge and Minecraft.

The link above directs you to OpenJDK, an open source licensed version of Java. You can select between the HotSpot or OpenJ9 JVM. Each has their own benefits and drawbacks which you can find by googling. You also can use an entirely different JVM as long as the language used is Java. Otherwise, you wil need to deveop some work around to deal with that situation.

### <a name="ide"></a>Java Integrated Development Environment ([Eclipse](https://www.eclipse.org/downloads/packages/release) or [IntelliJ IDEA](https://www.jetbrains.com/idea/download/)) <a href="#ide"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Minecraft Forge currently supports run configurations for Eclipse, IntelliJ IDEA, and VSCode. However, any IDE can be used. For this series, I will be developing using **Eclipse**. However, I will explain some processes on setting up in IntelliJ IDEA as well. Any other IDE is left as an exercise to the reader.

As of Eclipse 4.17 (2020-09), this IDE requires Java 11 or newer to run. There are two options to deal with this. The first is to download a newer version of Java to allow Eclipse to run and target Java 8 as the output. The other is to use a version older than 4.17. Either is up to the reader.

### <a name="forge"></a>[Minecraft Forge](https://files.minecraftforge.net/) <a href="#forge"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Whenever choosing an MDK for Forge, always develop with the latest release. The recommended is just used for stability. The MDK used during this tutorial will be updated as Forge progresses. Those commits will be named with **vX.X.X Push**. The information provided here will also be updated as necessary to accomodate this.

## <a name="optional"></a>Optional <a href="#optional"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

### <a name="file-extractor"></a>File Extractor <a href="#file-extractor"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

It is good to have a file extractor for .zip files to be able to extract the mdk onto your computer.

### <a name="photo-editing-software"></a>Photo Editing Software <a href="#photo-editing-software"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

When creating assets, it is a good idea to use a photo editing software of some sort to make textures easier to create and edit.

## <a name="starting-off"></a>Starting Off <a href="#starting-off"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Now that you have downloaded a JDK 8+, Eclipse, and Forge 3X.X.X, we can get started creating the workspace. Open the JDK .jar file and download the required contents. There is no need to move the default install location or change any of the download parameters.

### <a name="file-setup"></a>File Setup <a href="#file-setup"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

First, you will need to extract the Forge MDK to a folder. You should see something like the contents listed below:

```
...
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
│		└── resources
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

For Eclipse, you will also need to create another folder to point your workspace to. It doesn't matter what either of these folders are named.

For simplicity, I will condense down the file directory to those files that are necessary and can be copied over between projects:

```
...
├── gradle
├── src
├── build.gradle
├── gradle.properties
├── gradlew
└── gradlew.bat
```

If you are reusing a project from a previous version, then you can just copy over the `gradle` folder, `build.gradle`, `gradlew`, and `gradlew.bat`. This is the minimum requirement necessary to get a workspace setup for forge. You technically do not need the `build.gradle` file, but it's easier to reuse than rewrite from scratch.

### <a name="build-gradle"></a>`build.gradle` <a href="#build-gradle"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

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

As a quick note, we won't be covering all the information provided by gradle files or that Forge has added. For that information you can check out the [Gradle Documentation](https://docs.gradle.org/current/userguide/userguide.html).

> Note: To make our mod easier to update, we will be using the `gradle.properties` file to store some information about our project and reference those variables within `build.gradle`. Variables created within gradle can be reference directly by calling the variable name or by using a string reference wrapper like this: `"${<var_name>}"`.

To start, let's go over the major customizations that should be included in your project:

```gradle
...
apply plugin: 'eclipse'
...
```

This plugin should be the one specific to the IDE you are using (e.g. `eclipse` for Eclipse and `idea` for IntelliJ IDEA).

Next are these three variables are the most common ones within `build.gradle` to edit:  

```gradle
...
version = '1.0'
group = 'com.yourname.modid'
archivesBaseName = 'modid'
...
```

Variables | Description
--- | ---
**version** | The version of the mod you are on. For this, you might want to follow the [Forge standard](https://mcforge.readthedocs.io/en/latest/conventions/versioning/) provided on their docs.  
**group** | Usually, this is the reverse-DNS of your website. So, if I have a website named `championash5357.github.io`, then my group name would be `io.github.championash5357`. If you do not own a domain, then you can just use your username (e.g. `championash5357`).
**archivesBaseName** | The mod id of the project.  

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

Here we are going to look at a couple things:  

Variables | Description
--- | ---
**mappings channel** | Holds the current instance of the mappings to use to debobfuscate the Minecraft source code.  As of July 4th, 2020, Forge has been using a custom mapping system for 1.16 as they finish the new system MMS. The current mappings can be found on the [Forge Discord](https://discord.gg/UvedJ9m) in the pins of `#modder-support-116`. If you want to update your mappings, you can use `gradlew -PUPDATE_MAPPINGS="<mappings_version>" updateMappings` in a terminal of some kind.
**accessTransformer** | Allows you to set specific fields in the Minecraft source to different levels of access (e.g. public or protected). It provides a link to where the file is located within your current project. Uncomment this line if you would like to use access transformers within your project. I will be using reflection unless absolutely necessary, so I will leave this line commented.

<!-- TODO: Update 'data generator' text when applicable -->
Anything else in this section will be mentioned once the data generator tutorials get released.

The next part of the file we will talk about briefly is the `dependencies` section. If you want to create mods that contain a dependency, you would use this section to compile and import them into your project. All of the methods needed can be found once again in the gradle documentation; however, this will be gone over later as well.

The final part of note to discuss  currently is the jar information. This stores the title, vendor, and version of your jar when processed and built. You can set these values as you please to match your information.

> Note: Everything in gradle will not be covered in-depth here. This is not a tutorial for how to use gradle. I will only revisit this file as necessary to set up data as necessary for the current task.

Everything that I've said can also be found in a simpler format within the [Forge Docs](https://mcforge.readthedocs.io/en/latest/gettingstarted/).

### <a name="within-the-ide"></a>Within the IDE <a href="#within-the-ide"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Once you have set up your `build.gradle` file, navigate over to your IDE and open it up. Depending on which one you are using, you will have to follow a different set of steps.
**Eclipse** - Open eclipse within your workspace folder. Then, go to `File`->`Import...`->`Existing Gradle Project` and click next. You will see a box that says `Project root directory` to which you will browse to your actual MDK folder, and then click `Finish`.  
**IntelliJ** - Go to `File`->`Open` and navigate to your MDK folder and click `OK`.
From there, wait for gradle to finish importing your project and viola, the workspace is setup!

If you want to generate your runs, find the gradle tab located in your IDE, find the folder that says `fg_runs` and run the task specific for your IDE.

## <a name="additional-setup"></a>Additional Setup <a href="#additional-setup"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

After you have set up the workspace, there are some additional features that might make programming in Eclipse a tiny bit easier for you.

### <a name="hierarchial-presentation"></a>Hierarchial Presentation <a href="#hierarchial-presentation"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>


The default setting in Eclipse is to list all the packages by their full name such as `net.minecraft.inventory.CraftingInventory` and `net.minecraft.inventory.DoubleSidedInventory` instead of using a subdirectory system like: 
```
net
└── minecraft
	└── inventory
		├── CraftingInventory
		└── DoubleSidedInventory
```
To be able to set it up in this way, go to the left side of the screen where you will find the `Project Explorer`. To the right of the tab is a drop down arrow. Go to `Package Presentation` and switch it from `Flat` to `Hierarchial` and it will be much easier to find whatever you are looking for.

### <a name="run-favorites"></a>Run Favorites <a href="#run-favorites"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>


Since the system in FG3 does not run the Eclipse setup through the mdk, it might be a bit more difficult to select the `runClient`, `runData`, and `runServer` application used to test your mod in game. To set these as your favorites to easily find them, go to the Green Play button underneath the toolbar and click the drop down arrow to the right. Click `Organize Favorites`->`Add...`->`Select All`->`Ok`->`Ok` to set the applications as your favorites for easy access.

### <a name="removing-todo-auto-generated-messages"></a>Removing TODO Auto Generated Messages <a href="#removing-todo-auto-generated-messages"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Sometimes when auto-completing a method, the TODO message auto generates within as a comment. To disable this, go to `Window`->`Preferences`->`Java`->`Code Style`->`Code Templates` and edit the `Method body`, `Constructor body`, and `Catch block body` to remove all instances of this message. Then click `Apply and Close` and you should no longer have any auto generated messages appearing when auto-completing.

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial) under **Getting Started**.

<!-- Now that you have setup the workspace, it is now time to begin the [Main Mod File](./main_file). -->

Back to [Minecraft Tutorials](../../index)  
Back to [Home Page](../../../../index)  