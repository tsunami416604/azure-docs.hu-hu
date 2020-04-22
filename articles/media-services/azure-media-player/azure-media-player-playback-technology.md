---
title: Az Azure Media Player lejátszási technológiája
description: További információ a video- vagy hanglejátszáshoz használt lejátszási technológiáról.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 85eaa04836774b838da67e073017f4af3d2fe179
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726486"
---
# <a name="playback-technology-tech"></a>Lejátszási technológia ("tech") #

A lejátszási technológia a videó vagy hang lejátszásához használt böngészővagy bővítmény technológiára vonatkozik.

- **azureHtml5JS**: az MSE és az EME szabványokat használja a videóelemmel együtt a DASH-tartalom plugin nélküli lejátszásához, az AES-128 bites borítékos titkosított tartalom vagy a DRM közös titkosított tartalom (PlayReady és Widevine segítségével, ha a böngésző támogatja) használatát az Azure Media Services-ből
- **flashSS**: flash player technológiát használ a Smooth tartalom lejátszásához az AES-128 bites borítékvisszafejtés támogatásával az Azure Media Services-ből - a Flash 11.4-es vagy újabb verziójára van szükség
- **html5FairPlayHLS**: használ Safari specifikus böngésző-alapú lejátszástechnológia keresztül HLS a videó elem. Ez a technológia az Azure Media Services-ből származó FairPlay-védelemmel ellátott tartalmak lejátszásához szükséges, és 10/19/16-tól került be a techOrder-be.
- **silverlightSS:** silverlight technológiát használ a Smooth tartalmak lejátszásához az Azure Media Services PlayReady által védett tartalmainak támogatásával.
- **html5**: használja a böngésző-alapú lejátszási technológia a videó elem.  Apple iOS vagy Android rendszerű eszközön ez a technológia lehetővé teszi a HLS-adatfolyamok lejátszását az AES-128 bites borítéktitkosítás vagy a DRM-tartalom (a FairPlay-en keresztül, ha a böngésző támogatja).

## <a name="tech-order"></a>Technikai rendelés ##

Annak érdekében, hogy az eszköz játszható a legkülönbözőbb eszközök, a következő tech `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` érdekében ajánlott, `<video>` és az alapértelmezett, ha: és lehet beállítani közvetlenül a vagy programozottan a lehetőségek:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

vagy

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Kompatibilitási mátrix ##

Az Azure Media Services-ből származó streamelési tartalommal kapcsolatos ajánlott technikai rendelés miatt a következő kompatibilitási lejátszási mátrix várható

| Böngésző        | Operációs rendszer                                                       | Várt technológia (clear)  | Várt technológia (AES)  | Várt technológia (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| Él 11      | Windows 10, Windows 8.1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11IE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | ezüstfény (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | nem támogatott                |
| Edge           | Xbox One<sup>1</sup> (2015. novemberi frissítés)                   | azureHtml5JS           | azureHtml5JS         | nem támogatott                |
| Chrome 37+     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | ezüstfény (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashSS                | flashSS              | ezüstfény (PlayReady)    |
| Safari         | Legalább iOS 6                                                   | html5                  | html5 (nincs token)3    | nem támogatott                |
| Szafari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Szafari 6       | OS X hegyi oroszlán<sup>1</sup>                           | flashSS                | flashSS              | ezüstfény (PlayReady)    |
| Chrome 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5                  | html5 (nincs token)<sup>3</sup>    | nem támogatott                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | nem támogatott                |
| IE 8           | Windows                                                  | nem támogatott          | nem támogatott        | nem támogatott                |

<sup>1</sup> A konfiguráció nem támogatott vagy tesztelt; a befejezéshez referenciaként kell felsorolni.

<sup>2</sup> Az Android-eszközökön történő sikeres lejátszáshoz az eszközképességek, a grafikus támogatás, a kodekrenderelés, az operációs rendszer támogatása és így tovább. Mivel az Android egy nyílt forráskódú platform, amely lehetővé teszi a telefongyártók számára, hogy megváltoztassák a Google által biztosított Vanília Android operációs rendszert, ez némi töredezettséget okoz az Android térben, és egyes eszközök nem támogatottak a funkciók hiánya miatt. Emellett egyes Android-eszközök nem támogatják az összes kodeket.  

<sup>3</sup> Azokban az esetekben, ahol nincs támogatás a jogkivonathoz, egy proxy használható a funkció hozzáadásához. Nézze meg ezt a [blogot,](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) hogy többet tudjon meg ezt a megoldást.

> [!NOTE]
> Ha a várt tech választott igényel plugin kell telepíteni, mint a Flash, és ez nincs telepítve a felhasználó gépén, AMP továbbra is ellenőrzi a képességeit a következő tech, együtt forrástípusok és védelmi információk, a tech listán. Ha például egy nem védett, igény szerinti streamet próbál meg tekinteni a Safari 8-ban az OS X Yosemite rendszeren, és a Flash és a Silverlight sincs telepítve, az AMP kiválasztja a natív Html5 technológiát a lejátszáshoz.<br/><br/>Új böngésző technológiák jelennek meg naponta, és mint ilyen, hatással lehet erre a mátrixra.

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)