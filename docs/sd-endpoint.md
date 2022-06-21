# Exporting tokens as JSON definition

Supernova can export tokens as JSON definitions using our Javascript SDK and public API (coming soon). In this short document, you'll learn how you can get the token information from your design system to your CI/CD pipelines. Follow each step one by one in order of how they are written.

## SDK approach

### 1. Obtaining Supernova SDK

SDK is available on the following URL: [https://github.com/Supernova-Studio/sdk-typescript](https://github.com/Supernova-Studio/sdk-typescript). You can install it in any node-enabled project by simply pulling it from npm, like this:

```typescript
npm install @supernovaio/supernovasdk
```


### 2. Setting up connection to your design system

Supernova SDK uses authentication key to give you access to read the data from your design system, and so first you need to obtain the API key for your account. You can find the API key under your profile menu at the top right corner (if you are logged in, [https://cloud.supernova.io/user-profile/authentication](you can click here):

```
Supernova cloud > your profile > authentication > generate token
```

. Then, once you copy the token, in your code, create connection to our Supernova instance:

```typescript
import { Supernova } from "@supernovaio/supernova-sdk"
...
let supernova = new Supernova(YOUR_API_KEY, null, null)
```

Then, obtain your design system version from which you will be drawing the data:

```typescript
let version = await supernova.designSystemVersion(DS_ID, DS_VERSION_ID)
```

You can obtain both design system ID and version ID from the URL of your design system. For example, for our design system, the URL can look like this:

```
https://cloud.supernova.io/ws/supernova/ds/15-cloud-ds/latest/content/96-default/tokens/colors
```

In this case, `DS_ID = 15` and `DS_VERSION_ID = 96`. If you want to always be drawing from the latest version (recommended), use `DS_VERSION_ID = head` instead.


### 3. Obtaining the token representation

Using the version instance, you can now obtain the definition formatted exactly how you want it. To do it, simply copy paste the following code:

```typescript

import { SupernovaToolsStyleDictionary, SupernovaToolStyleDictionaryKeyNaming, SupernovaToolStyleDictionaryOptions } from "@supernovaio/supernova-sdk"

// Options
let options: SupernovaToolStyleDictionaryOptions = {
    naming: SupernovaToolStyleDictionaryKeyNaming.original, // Will preserve token/group names exactly as they were defined. Other options are "camelcase", "snakecase", "kebabcase"
    includeComments: true, // Will include token descriptions, if set
    includeBrandId: true, // Will include ID of the brand tokens belongs to
    brandId: null, // Will only fetch tokens from one specific brand (ID)
    includeType: true, // Will include type, like "color", to the output
    includeRootTypeNodes: false, // Will separate tokens by categories, each forming one root
    type: null, // Will filter output by token type. Null means all tokens, otherwise use "TokenType" enum instead
}

// Create the communication / formatting tool for the transformation of tokens using both supernova instance and targeted version
let sdTool = new SupernovaToolsStyleDictionary(supernova, version)

// Fetch tokens based on the configuration
let sdRepresentation = await sdTool.tokensToSD(options)
```

The above will format tokens and retrieve them as JSON representation, properly including groups etc. as part of the generated tree. You can use parameters to change the behavior of the output that gets generated.

## API approach

Our public API is currently being tested for the purposes of security and will be available in the upcoming days.

