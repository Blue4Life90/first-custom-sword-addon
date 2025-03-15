# How to Run and Test the AverySword Add-on

## Method 1: Development Environment (For Add-on Creators)

The development environment approach uses Node.js as the foundation for a modern development workflow. This approach offers powerful tools that make creating and updating add-ons much easier in the long run, especially for larger projects.

### What is Node.js?

Node.js is a JavaScript runtime environment that allows developers to run JavaScript code outside of a web browser. In this context, it provides:

1. A way to run the build scripts that compile and package your add-on
2. Package management through npm (Node Package Manager) to install and manage dependencies
3. Development tools like TypeScript compilers, bundlers, and linters

### Setup and Installation

1. **Install Node.js and npm**:
   First, you need to have Node.js installed on your computer. Download and install it from [nodejs.org](https://nodejs.org/). This automatically installs npm as well.

2. **Clone or download the repository**:
   ```
   git clone [repository-url]
   ```
   Or download and extract the ZIP file from the repository page.

3. **Navigate to the project directory and install dependencies**:
   ```
   cd AverySword
   npm install
   ```
   
   This command reads the package.json file and installs all the required development tools and libraries. These include TypeScript compiler, build utilities, and Minecraft-specific development packages.

4. **Set up environment variables**:
   Create a `.env` file in the root directory with:
   ```
   PROJECT_NAME=aver_as
   ```
   
   This tells the build system which folder name to use when creating or updating the add-on packages. The value "aver_as" should match the folder names in behavior_packs/ and resource_packs/.

5. **Build the add-on**:
   ```
   npm run mcaddon
   ```
   
   When you run this command, several things happen behind the scenes:
   - TypeScript files in the scripts/ folder are compiled to JavaScript
   - The necessary files are copied to the correct locations
   - Everything is packaged into a .mcaddon file in the dist/packages directory

   This command uses the scripts defined in package.json, which in turn use the build configuration in just.config.ts to determine exactly what files to process and how.

6. **Enable local testing** (Windows only):
   ```
   npm run enablemcloopback
   ```
   
   This Windows-specific command adjusts security settings to allow Minecraft to access content from your local development folders. It only needs to be run once per Windows installation.

7. **Set up continuous development**:
   ```
   npm run local-deploy
   ```
   
   This powerful command:
   - Watches for changes in your source files
   - Automatically rebuilds the add-on whenever you save changes
   - Updates the files in your Minecraft development folders
   - Allows you to see changes quickly without manually rebuilding and reinstalling

   For this to work properly, the build system needs to know where your Minecraft development folders are located. The paths are configured in just.config.ts through the copyTaskOptions.

### Why Use the Development Environment?

Using this more complex setup provides several advantages:

- You can write in TypeScript instead of JavaScript, giving you better code completion and error detection
- Changes are automatically detected and rebuilt
- Code quality tools help prevent bugs before testing
- The build process automates repetitive packaging tasks
- The development environment ensures consistency and correctness

For beginners, this approach might seem overwhelming, but it becomes very valuable as your add-ons grow in complexity.

## Method 2: Direct Installation (For Players)

If you just want to use the add-on without modifying it:

1. **Locate the .mcaddon file**:
   - If using a pre-built add-on, use the provided .mcaddon file
   - If using repository code, first follow Steps 1-5 from Method 1 to generate the file
   - The file will be located at `dist/packages/AverySword.mcaddon` after building

2. **Install the add-on**:
   - Double-click the .mcaddon file (should automatically open Minecraft)
   - Or manually import in Minecraft:
     - Open Minecraft Bedrock Edition
     - Go to Settings → Global Resources
     - Click "Import" and select your .mcaddon file

3. **Create or edit a world to use the add-on**:
   - Go to "Create New World" or edit an existing world
   - Go to "Resource Packs" and activate "AverySword"
   - Go to "Behavior Packs" and activate "AverySword"
   - Make sure to enable "Additional Modding Capabilities" under "Experiments" if required by your Minecraft version

## Testing the Add-on

1. **Verify the add-on is loaded**:
   Open your chat/command window (press T or /) and type:
   ```
   /give @s demo:my_sword
   ```
   
   You should receive the "Secret Weapon" sword if the add-on is working correctly.

2. **Test the crafting recipe**:
   Gather:
   - 1 Stick
   - 1 Ender Eye
   - 1 Ender Pearl
   
   Place them in a crafting table in this pattern:
   - Top: Ender Pearl
   - Middle: Ender Eye
   - Bottom: Stick

3. **Test the item's properties**:
   - Check durability by using the sword (should have 36 durability)
   - Test as fuel by placing in a furnace (should burn for 3 seconds)

Note that the Secret Weapon doesn't have combat damage values defined in the current configuration. If you want it to function as a proper weapon, you'll need to modify the `my_sword.json` file.

## Troubleshooting

### Command Syntax Error
If you get an error when using `/give @s demo:my_sword`:

1. Ensure both behavior and resource packs are activated in your world
2. Check if experiments/beta APIs are enabled if required
3. Verify the add-on was properly imported
4. Try restarting Minecraft completely
5. If using Method 1, check for build process errors

### Missing Textures
If the sword appears as a purple and black checkerboard:

1. Make sure the resource pack is activated
2. Verify texture files exist in the correct location
3. Check if your Minecraft version supports the texture format

### Add-on Not Appearing
If the add-on doesn't show in your packs list:

1. Confirm you're using a supported version of Minecraft Bedrock
2. Try manually importing the .mcaddon file again
3. Check build output for packaging errors
4. Verify manifest files have the correct format version

## Next Steps

After getting the add-on working, you might want to:

1. Modify the sword properties in `behavior_packs/aver_as/items/my_sword.json` to add combat functionality
2. Add custom behaviors using `scripts/main.ts`
3. Create additional items using the existing structure as a template
4. Experiment with different textures

Remember to run `npm run build` and `npm run mcaddon` (or `npm run local-deploy` for continuous development) after making changes to see them reflected in the game.

# Detailed Walkthrough of the AverySword Add-on Files

## Behavior Pack Files

### 1. `behavior_packs/aver_as/items/my_sword.json`

```json
{
  "format_version": "1.20.30",
  "minecraft:item": {
    "description": {
      "identifier": "demo:my_sword",
      "category": "equipment"
    },
    "components": {
      "minecraft:icon": {
        "texture": "demo:my_sword"
      },
      "minecraft:display_name": {
        "value": "Secret Weapon"
      },
      "minecraft:durability": {
        "damage_chance": {
          "min": 10,
          "max": 50
        },
        "max_durability": 36
      },
      "minecraft:fuel": {
        "duration": 3
      }
    }
  }
}
```

This file defines the core item properties for your sword:

- **Format Version**: "1.20.30" tells Minecraft which version of the item format to use, ensuring compatibility with specific game versions.
- **Identifier**: "demo:my_sword" is how the game identifies your item. The "demo" part is the namespace, and "my_sword" is the item ID.
- **Category**: "equipment" categorizes the item for inventory organization.
- **Components**: These define the item's behaviors and properties:
  - **Icon**: References the texture with "demo:my_sword", linking to the resource pack.
  - **Display Name**: "Secret Weapon" is the in-game name players will see.
  - **Durability**: Sets the item's lifespan (36 uses) with a damage chance between 10% and 50% per use.
  - **Fuel**: Allows the item to be used as fuel in furnaces for 3 seconds.

Notice that this sword lacks typical weapon components like damage values, which would be needed for a functional weapon.

### 2. `behavior_packs/aver_as/manifest.json`

```json
{
  "format_version": 2,
  "header": {
    "name": "AverySword",
    "description": "Custom sword",
    "uuid": "7e75417d-0cf5-4747-8da6-d42f9d1bd7a0",
    "version": [1, 0, 0],
    "min_engine_version": [1, 21, 60]
  },
  "modules": [
    {
      "description": "Behavior",
      "version": [1, 0, 0],
      "uuid": "9810eef8-4d38-4218-945f-f82ccba73bde",
      "type": "data"
    },
    {
      "description": "Script resources",
      "language": "javascript",
      "type": "script",
      "uuid": "ba038528-7a1f-41e3-9e9d-76f4fd7f9ca7",
      "version": [0, 0, 1],
      "entry": "scripts/main.js"
    }
  ],
  "dependencies": [
    {
      "uuid": "65904843-9d55-4705-845f-5398f0aee2e1",
      "version": [1, 0, 0]
    },
    {
      "module_name": "@minecraft/server",
      "version": [1, 17, 0]
    },
    {
      "module_name": "@minecraft/server-ui",
      "version": [1, 3, 0]
    }
  ]
}
```

This manifest file serves as the ID card for your behavior pack:

- **Format Version**: Specifies the manifest format version.
- **Header**: Contains basic identification information:
  - **Name and Description**: Human-readable identifiers.
  - **UUID**: A unique identifier for this specific behavior pack (this must be unique across all add-ons).
  - **Version**: Your pack's version number.
  - **Min Engine Version**: Minimum Minecraft version required (1.21.60).
- **Modules**: Describes what components make up this pack:
  - A data module for behaviors (items, entities, etc.).
  - A JavaScript script module that points to main.js as the entry point.
- **Dependencies**: Lists what this pack depends on:
  - The resource pack (via its UUID).
  - The Minecraft scripting API modules needed for scripts to function.

This manifest is crucial as it links together all components and establishes compatibility requirements.

### 3. `behavior_packs/aver_as/recipes/my_sword.json`

```json
{
  "format_version": "1.12.0",
  "minecraft:recipe_shaped": {
    "description": {
      "identifier": "demo:my_sword"
    },
    "tags": ["crafting_table"],
    "pattern": ["e", "E", "#"],
    "key": {
      "#": { "item": "minecraft:stick" },
      "E": { "item": "minecraft:ender_eye" },
      "e": { "item": "minecraft:ender_pearl" }
    },
    "result": {
      "item": "demo:my_sword"
    }
  }
}
```

This file defines how players can craft your sword:

- **Format Version**: Uses an older format (1.12.0) which is still valid for recipes.
- **Recipe Type**: "recipe_shaped" means ingredients must be placed in a specific pattern.
- **Description**: Identifies the recipe by the same identifier as the item.
- **Tags**: Specifies this recipe can only be crafted at a crafting table.
- **Pattern**: Defines the shape of the crafting recipe:
  - "e" in top slot (ender pearl)
  - "E" in middle slot (ender eye)
  - "#" in bottom slot (stick)
- **Key**: Maps the pattern symbols to actual Minecraft items.
- **Result**: Specifies that crafting produces one "demo:my_sword" item.

This recipe creates a vertical stack of ender pearl, ender eye, and stick to craft the sword.

## Resource Pack Files

### 4. `resource_packs/aver_as/manifest.json`

```json
{
  "format_version": 2,
  "header": {
    "name": "AverySword",
    "description": "Custom sword",
    "uuid": "65904843-9d55-4705-845f-5398f0aee2e1",
    "version": [1, 0, 0],
    "min_engine_version": [1, 21, 60]
  },
  "modules": [
    {
      "description": "Add-on Pack Resources",
      "type": "resources",
      "uuid": "601c9e39-2219-4afc-9c76-a7d44609f5a3",
      "version": [1, 0, 0]
    }
  ],
  "dependencies": [
    {
      "uuid": "7e75417d-0cf5-4747-8da6-d42f9d1bd7a0",
      "version": [1, 0, 0]
    }
  ]
}
```

This resource pack manifest mirrors the behavior pack manifest but for visual elements:

- **Header**: Contains the pack's identity information:
  - **UUID**: This value (65904843-9d55-4705-845f-5398f0aee2e1) is referenced in the behavior pack's dependencies.
- **Modules**: Lists just one resources module.
- **Dependencies**: Crucially, this references the behavior pack's UUID, creating a bi-directional link between the packs.

This two-way dependency ensures that when one pack is activated, Minecraft knows to activate the other.

### 5. `resource_packs/aver_as/texts/en_US.lang`

```
item.demo:crown=Fancy Crown
item.demo:goo=Weird Goo
```

This language file provides translations for item names:

- Each line follows the format `item.[identifier]=[display name]`.
- Interestingly, this file doesn't include a translation for "demo:my_sword" because that's handled directly in the item definition.
- The file includes translations for other items ("crown" and "goo") that aren't implemented in the behavior files we've seen.

Language files allow for localization (translating content into different languages) and can override display names defined in item files.

### 6. `resource_packs/aver_as/textures/item_texture.json`

```json
{
  "resource_pack_name": "custom_item_resource_pack",
  "texture_name": "atlas.items",
  "texture_data": {
    "demo:goo": {
      "textures": "textures/items/goo"
    },
    "demo:crown": {
      "textures": "textures/items/crown_ico"
    },
    "demo:chestplate": {
      "textures": "textures/items/diamond_chestplate_purple"
    },
    "demo:my_boots": {
      "textures": "textures/items/diamond_boots_purple"
    },
    "demo:my_helm": {
      "textures": "textures/items/diamond_helmet_purple"
    },
    "demo:my_leggings": {
      "textures": "textures/items/diamond_leggings_purple"
    },
    "demo:my_sword": {
      "textures": "textures/items/my_sword"
    },
    "demo:my_sword1": {
      "textures": "textures/items/my_sword1"
    },
    "demo:my_sword2": {
      "textures": "textures/items/my_sword2"
    },
    "demo:wrench": {
      "textures": "textures/items/wrench_ico"
    }
  }
}
```

This file maps item identifiers to their respective texture files:

- **Resource Pack Name and Texture Name**: Identify this texture atlas within Minecraft.
- **Texture Data**: Maps each item identifier to its texture file path.
  - The "demo:my_sword" entry points to "textures/items/my_sword" (note: the actual image file would have a .png extension not shown here).
  - This file includes mappings for multiple items beyond just the sword, suggesting this pack could be expanded.

This file is essential for connecting item definitions to their visual appearance in-game.

## Script Files

### 7. `scripts/main.ts`

```typescript
import { world, system } from "@minecraft/server";

function mainTick() {
  // add main loop code here.
  system.run(mainTick);
}

// Uncomment the line below to ensure your main tick code is called from the start.
// system.run(mainTick);
```

This TypeScript file provides a framework for adding custom behavior:

- **Imports**: Brings in the world and system modules from the Minecraft scripting API.
- **MainTick Function**: A recursive function that would run repeatedly, allowing for continuous operations.
- **Commented Code**: The function isn't actually started because the call to system.run is commented out.

Even though this file doesn't actively do anything, it's set up to be the entry point for custom behaviors that could be added to the sword or other aspects of your add-on.

### 8. `dist/scripts/main.js`

```javascript
// ct:/main.js
import { system } from "@minecraft/server";
```

This is the compiled/bundled JavaScript version of your script, which Minecraft actually runs:

- It's very minimal because the TypeScript code didn't include any actual behaviors.
- The comment and import statement show it's been processed by the build system.
- This file is what's actually referenced in the behavior pack's manifest under the script module.

## Build Configuration Files

### 9. `package.json`

```json
{
  "name": "AverySword",
  "version": "0.1.0",
  "productName": "Minecraft Add-On Starter Project",
  "description": "Custom sword",
  "private": true,
  "devDependencies": {
    "@minecraft/core-build-tasks": "^1.1.3",
    "eslint-plugin-minecraft-linting": "^1.2.1",
    "source-map": "^0.7.4",
    "ts-node": "^10.9.1",
    "typescript": "^5.0.2"
  },
  "scripts": {
    "lint": "just-scripts lint",
    "build": "just-scripts build",
    "clean": "just-scripts clean",
    "local-deploy": "just-scripts local-deploy",
    "mcaddon": "just-scripts mcaddon",
    "enablemcloopback": "CheckNetIsolation.exe LoopbackExempt -a -p=S-1-15-2-1958404141-86561845-1752920682-3514627264-368642714-62675701-733520436",
    "enablemcpreviewloopback": "CheckNetIsolation.exe LoopbackExempt -a -p=S-1-15-2-424268864-5579737-879501358-346833251-474568803-887069379-4040235476"
  },
  "dependencies": {
    "@minecraft/math": "^1.1.0",
    "@minecraft/server": "^1.8.0",
    "@minecraft/server-ui": "^1.1.0",
    "@minecraft/vanilla-data": "^1.20.60"
  }
}
```

This Node.js package configuration manages the development environment:

- **Project Metadata**: Name, version, and description of your add-on.
- **DevDependencies**: Tools needed for development but not at runtime:
  - TypeScript compiler
  - Minecraft build tools
  - Linting plugins for code quality
- **Scripts**: Commands for common development tasks:
  - Building the add-on
  - Creating an mcaddon package
  - Setting up local testing
- **Dependencies**: Libraries your add-on uses at runtime:
  - Minecraft server API for scripts
  - UI components
  - Math utilities

This sophisticated setup allows for modern development practices when creating add-ons.

### 10. `just.config.ts`

```typescript
import { argv, parallel, series, task, tscTask } from "just-scripts";
import {
  BundleTaskParameters,
  CopyTaskParameters,
  bundleTask,
  cleanTask,
  cleanCollateralTask,
  copyTask,
  coreLint,
  mcaddonTask,
  setupEnvironment,
  ZipTaskParameters,
  STANDARD_CLEAN_PATHS,
  DEFAULT_CLEAN_DIRECTORIES,
  getOrThrowFromProcess,
  watchTask,
} from "@minecraft/core-build-tasks";
import path from "path";
setupEnvironment(path.resolve(__dirname, ".env"));
const projectName = getOrThrowFromProcess("PROJECT_NAME");
const bundleTaskOptions: BundleTaskParameters = {
  entryPoint: path.join(__dirname, "./scripts/main.ts"),
  external: ["@minecraft/server", "@minecraft/server-ui"],
  outfile: path.resolve(__dirname, "./dist/scripts/main.js"),
  minifyWhitespace: false,
  sourcemap: true,
  outputSourcemapPath: path.resolve(__dirname, "./dist/debug"),
};
const copyTaskOptions: CopyTaskParameters = {
  copyToBehaviorPacks: [`./behavior_packs/${projectName}`],
  copyToScripts: ["./dist/scripts"],
  copyToResourcePacks: [`./resource_packs/${projectName}`],
};
const mcaddonTaskOptions: ZipTaskParameters = {
  ...copyTaskOptions,
  outputFile: `./dist/packages/${projectName}.mcaddon`,
};
task("lint", coreLint(["scripts/**/*.ts"], argv().fix));
task("typescript", tscTask());
task("bundle", bundleTask(bundleTaskOptions));
task("build", series("typescript", "bundle"));
task("clean-local", cleanTask(DEFAULT_CLEAN_DIRECTORIES));
task("clean-collateral", cleanCollateralTask(STANDARD_CLEAN_PATHS));
task("clean", parallel("clean-local", "clean-collateral"));
task("copyArtifacts", copyTask(copyTaskOptions));
task("package", series("clean-collateral", "copyArtifacts"));
task(
  "local-deploy",
  watchTask(
    ["scripts/**/*.ts", "behavior_packs/**/*.{json,lang,tga,ogg,png}", "resource_packs/**/*.{json,lang,tga,ogg,png}"],
    series("clean-local", "build", "package")
  )
);
task("createMcaddonFile", mcaddonTask(mcaddonTaskOptions));
task("mcaddon", series("clean-local", "build", "createMcaddonFile"));
```

This configuration file defines the build process:

- **Setup**: Configures the environment and loads project settings.
- **Bundle Configuration**: Specifies how TypeScript files should be compiled into JavaScript.
- **Copy Configuration**: Determines where files should be placed during building.
- **Task Definitions**: Creates various build tasks:
  - Compiling TypeScript
  - Creating the .mcaddon file
  - Watching for file changes during development
  - Cleaning up temporary files

This creates an automated workflow that ensures your add-on is correctly packaged and ready for import into Minecraft.

### 11. `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "es6",
    "moduleResolution": "Node",
    "module": "ES2020",
    "declaration": false,
    "noLib": false,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "sourceMap": true,
    "pretty": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "allowUnreachableCode": true,
    "allowUnusedLabels": true,
    "noImplicitAny": true,
    "noImplicitReturns": false,
    "noImplicitUseStrict": false,
    "outDir": "lib",
    "rootDir": ".",
    "baseUrl": "behavior_packs/",
    "listFiles": false,
    "noEmitHelpers": true,
    "skipLibCheck": true
  },
  "include": ["scripts/**/*"],
  "exclude": ["lib", "dist", "node_modules"],
  "compileOnSave": false
}
```

This configures the TypeScript compiler:

- **Compiler Options**: Sets rules for how TypeScript should be compiled:
  - Target ES6 JavaScript standard
  - Use modern module resolution
  - Generate source maps for debugging
  - Enforce strict type checking
- **Include/Exclude**: Specifies which files to compile:
  - Includes all files in the scripts directory
  - Excludes output directories and node_modules

This ensures your TypeScript code is properly converted to JavaScript that Minecraft can execute.

### 12. `eslint.config.mjs`

```javascript
import minecraftLinting from "eslint-plugin-minecraft-linting";
import tsParser from "@typescript-eslint/parser";
import ts from "@typescript-eslint/eslint-plugin";
export default [
  {
    files: ["scripts/**/*.ts"],
    languageOptions: {
      parser: tsParser,
      ecmaVersion: "latest",
    },
    plugins: {
      ts,
      "minecraft-linting": minecraftLinting,
    },
    rules: {
      "minecraft-linting/avoid-unnecessary-command": "error",
    },
  },
];
```

This configures the code linting tool:

- **File Pattern**: Targets TypeScript files in the scripts directory.
- **Parser**: Uses the TypeScript parser for analyzing code.
- **Plugins**: Loads Minecraft-specific linting rules.
- **Rules**: Enforces best practices for Minecraft add-on development.

Linting helps catch potential issues and enforces code quality standards.

## How Everything Works Together

Now that we've examined each file individually, let's understand how they all work together to create a functioning add-on:

1. **Development Workflow**:
   - You write TypeScript code in `scripts/main.ts`
   - You define item behaviors in `behavior_packs/aver_as/items/my_sword.json`
   - You define visual elements in the resource pack
   - The build system (`just.config.ts`, `package.json`) compiles and packages everything

2. **Packaging and Deployment**:
   - Running `npm run mcaddon` creates a `.mcaddon` file
   - This file includes both behavior and resource packs
   - The manifest files ensure they're linked together correctly

3. **In-Game Functionality**:
   - When installed, Minecraft registers the "demo" namespace
   - It loads item definitions, recipes, and textures
   - If scripts are enabled, it runs the JavaScript code
   - Players can now craft and use the custom sword

4. **Key Connections**:
   - Item identifier (`demo:my_sword`) connects the item definition to the recipe
   - Texture reference connects the item to its visual appearance
   - UUID references in manifests connect the behavior and resource packs
   - Entry point in manifest connects to the compiled script

## Essential Minecraft Add-on Concepts

Understanding these key concepts will help you build successful add-ons:

1. **Namespaces**: The prefix before item names (like "demo:" or "minecraft:") determines which add-on "owns" that content. Your namespace needs to be consistently used and properly registered.

2. **UUID Linking**: Behavior and resource packs are connected through their UUIDs in the manifest dependencies sections. Both must reference each other correctly.

3. **Format Versions**: Different components use different format versions that must be compatible with your Minecraft version.

4. **Component-Based Design**: Minecraft items are built from individual components ("minecraft:durability", "minecraft:fuel", etc.) that define their behavior.

5. **Build Pipeline**: Modern add-ons use build tools to transform source files into the format Minecraft expects.

The AverySword tutorial provides a complete, working example of these concepts in action, which is why it functions correctly in-game.
