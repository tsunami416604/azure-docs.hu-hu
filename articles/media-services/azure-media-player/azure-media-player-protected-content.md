---
title: Azure Media Player által védett tartalom
description: Az Azure Media Player jelenleg támogatja az AES-128 bites borítéktitkosított tartalmat és a közös titkosított tartalmat.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 64414d3ec31e8763b7c576af93374bf514141fd4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726493"
---
# <a name="protected-content"></a>Védett tartalom #

Az Azure Media Player jelenleg támogatja az AES-128 bites borítékban titkosított tartalmat és a közös titkosított tartalmat (PlayReady és Widevine-on keresztül) vagy a FairPlay-en keresztül titkosított tartalmat. A védett tartalom helyes lejátszásához meg kell mondania `protectionInfo`az Azure Media Player nek a . Ez az információ forrásonként létezik, és közvetlenül a `<source>` címkén keresztül adható hozzá a `data-setup`címkéhez.  Közvetlenül paraméterként `protectionInfo` is hozzáadhatja a forrást, ha dinamikusan állítja be a forrást.

`protectionInfo`elfogad egy JSON-objektumot, és a következőket tartalmazza:

- `type`: `AES` `PlayReady` vagy `Widevine` vagy`FairPlay`
- `certificateUrl`: ennek közvetlen kapcsolatnak kell lennie a házigazdája FairPlay tanúsítványával

- `authenticationToken`: ez egy kódolatlan hitelesítési jogkivonat hozzáadására szolgál.

> [!IMPORTANT]
> A **certificateUrl** objektum csak a FairPlay DRM-hez szükséges.***
>[!NOTE]
> Az alapértelmezett techOrder megváltozott, hogy alkalmazkodjon `html5FairPlayHLS` az új tech- kifejezetten a FairPlay tartalom natív lejátszásához az azt támogató böngészőkben (Safari AZ OSX 8+-on). Ha fairplay tartalmat kell lejátszania, **és** megváltoztatta az alapértelmezett techOrder-t az alkalmazásban lévő egyénire, akkor hozzá kell adnia ezt az új technológiát a techOrder objektumhoz. Javasoljuk, hogy a silverlightSS előtt is adja meg, hogy a tartalom ne legyen lejátszva a PlayReady-en keresztül.

## <a name="code-sample"></a>Kódminta ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

vagy több DRM-mel

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Nem minden böngésző/platform képes védett tartalom lejátszására. A [támogatott arról,](azure-media-player-playback-technology.md) hogy mi ről a Lejátszási technológia című részben talál további információt.
> [!IMPORTANT]
> A lejátszónak átadott token biztonságos tartalomra szolgál, és csak hitelesített felhasználók számára használható. Feltételezzük, hogy az alkalmazás SSL-t vagy más biztonsági intézkedést használ. Emellett a végfelhasználó megbízhatónak minősül, hogy ne éljen vissza a jogkivonattal; ha nem ez a helyzet, kérjük, vonja be a biztonsági szakértők.

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)