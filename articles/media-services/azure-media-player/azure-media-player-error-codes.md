---
title: Az Azure Media Player hibakódjai
description: Hibakód-hivatkozás az Azure Media Player hez.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727254"
---
# <a name="error-codes"></a>Hibakódok #

Ha a lejátszás nem indul el vagy nem állítható le, egy hibaesemény indul el, és a `error()` funkció egy kódot és egy opcionális üzenetet ad vissza, amely segít az alkalmazás fejlesztőjének további részletekhez jutni. `error().message`nem az üzenet jelenik meg a felhasználó számára.  A felhasználónak megjelenő üzenet `error().code` a 27-20 biteken alapul, lásd az alábbi táblázatot.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Hibakódok, bitek [31-28] (4 bit) ##

Írja le a hiba területet.

- 0 – ismeretlen
- 1 - ERŐSÍTŐ
- 2 - AzureHtml5JS
- 3 - Villog
- 4 - SilverlightSS
- 5 - Html5
- 6 - html5fairplayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Hibakódok, bitek [27-0] (28 bit) ##

Írja le a hiba részleteit, a 27-20 bitek magas szintet biztosítanak, a 19-0 bitek részletesebbek, ha rendelkezésre állnak.


| amp.errorCode. nem, nem, nem, nem, nem, nem | Kódok, bitek [27-0] (28 bit) | Leírás |
|---|---:|---|
| **MEDIA_ERR_ABORTED hibatartomány (0x0100000 - 0x01FFFFF)** | | |
| abortedErrIsmeretlen | 0x0100000 | Általános megszakítási hiba |
| abortedErrNotImplemented | 0x0100001 | Megszakítási hiba, nincs megvalósítva |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Megszakítási hiba, a vegyes tartalom blokkolva `http://` – `https://` általában akkor fordul elő, amikor adatfolyamot tölt be egy oldalról |
| **MEDIA_ERR_NETWORK hibák kezdőértéke (0x0200000 - 0x02FFFFF)** | | |
| networkErrIsmeretlen | 0x0200000 | Általános hálózati hiba |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400 hibaválasz |
| networkErrHttpUserAuthRequired | 0x0200191 | Http 401-es hibaválasz |
| networkErrHttpUserFortiltott | 0x0200193 | Http 403-as hibaválasz |
| networkErrHttpUrlNotFound | 0x0200194 | Http 404-es hibaválasz |
| networkErrHttpNotAllowed | 0x0200195 | Http 405 hibaválasz |
| networkErrHttpGone | 0x020019A | Http 410 hibaválasz |
| networkErrHttpPreconditionFailed | 0x020019C | Http 412-es hibaválasz |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500-as hibaválasz
| networkErrHttpBadGateway | 0x02001F6 | Http 502-es hibaválasz |
| networkErrHttpServiceUnavailable | 0x02001F7 | Http 503-as hibaválasz |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504-es hibaválasz |
| networkErrTimeout | 0x0200258 | Hálózati időmegt/ időkapcsolati hiba
| hálózati ErrErr | 0x0200259 | Hálózati kapcsolathibája válasz |
| **MEDIA_ERR_DECODE hibák (0x0300000 - 0x03FFFFF)** | | |
| dekódErrIsmeretlen | 0x0300000 | Általános dekódolási hiba |
| **MEDIA_ERR_SRC_NOT_SUPPORTED hibák (0x0400000 - 0x04FFFFF)** | | |
| srcErrIsmeretlen | 0x0400000 | Az általános forrás nem támogatott hiba |
| srcErrParse Bemutató | 0x0400001 | Bemutatóelemzési hiba |
| srcErrParseSzegmens | 0x0400002 | Szegmenselemzési hiba |
| srcErrUnsupportedPresentation | 0x0400003 | A bemutató nem támogatott |
| srcErrInvalidSegment | 0x0400004 | Érvénytelen szegmens |
| **MEDIA_ERR_ENCRYPTED hibák kezdőértéke(0x0500000 - 0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Általános titkosított hiba | 
| encryptErrDecrypterNotFound | 0x0500001 | A visszafejtő nem található |
| encryptErrDecrypterInit | 0x0500002 | Visszafejtő inicializálási hibája |
| encryptErrDecrypterNotSupported | 0x0500003 | A visszafejtő nem támogatott |
| encryptErrKeyMegszerzés | 0x0500004 | A kulcs beszerzése nem sikerült |
| titkosítás | 0x0500005 | A szegmens visszafejtése nem sikerült |
| encryptErrLicenseAcquire | 0x0500006 | A licenc beszerzése sikertelen |
| **SRC_PLAYER_MISMATCH hibák kezdőértéke(0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchIsmeretlen | 0x0600000 | Általános nem megfelelő tech player játszani a forrás |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Flash plugin nincs telepítve, ha telepítve van a forrás játszható. *VAGY* Flash 30 telepítve van, és lejátssza Az AES tartalmát.  Ebben az esetben próbálkozzon egy másik böngészővel. Flash 30 nem támogatott ma, mint a június 7. További részleteket az [ismert problémákról](azure-media-player-known-issues.md) talál. Megjegyzés: Ha 0x00600003, a Flash és a Silverlight nincs telepítve, ha a techOrder ben van megadva.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Silverlight plugin nincs telepítve, ha telepítve van a forrás játszhat. Megjegyzés: Ha 0x00600003, a Flash és a Silverlight nincs telepítve, ha a techOrder ben van megadva. |
| | 0x00600003 | A Flash és a Silverlight nincs telepítve, ha a techOrder ben van megadva. |
| **Ismeretlen hibák (0x0FF0000)** | | |
| errIsmeretlen | 0xFF00000 | Ismeretlen hibák |

## <a name="user-error-messages"></a>Felhasználói hibaüzenetek ##

A megjelenő felhasználói üzenet a hibakód 27-20-as bitjein alapul.

- MEDIA_ERR_ABORTED (1) -"Megszakította a videolejátszást"
- MEDIA_ERR_NETWORK (2) - "A hálózati hiba miatt a videó letöltés nem félúton."
- MEDIA_ERR_DECODE (3) - "A videó lejátszása meglett szakítva egy korrupciós probléma miatt, vagy azért, mert a használt videó olyan funkciókat tartalmaz, amelyeket a böngésző nem támogatott."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"A videó nem tölthető be, vagy azért, mert a kiszolgáló vagy a hálózat nem sikerült, vagy mert a formátum nem támogatott."
- MEDIA_ERR_ENCRYPTED (5) - "A videó titkosított, és nem rendelkezünk a kulcsokkal, hogy visszafejtsük."
- SRC_PLAYER_MISMATCH (6) - "Nem található kompatibilis forrás ehhez a videóhoz."
- MEDIA_ERR_UNKNOWN (0xFF) - "Ismeretlen hiba történt."

## <a name="examples"></a>Példák ##

### <a name="0x10600001"></a>0x10600001 ##

"Nem található kompatibilis forrás ehhez a videóhoz." jelenik meg a végfelhasználó számára.

Nincs tech player, amely képes lejátszani a kért forrásokat, de ha a Flash plugin telepítve van, akkor valószínű, hogy a forrás játszható.  

### <a name="0x20200194"></a>0x20200194 ###

"Egy hálózati hiba miatt a videó letöltése nem sikerült félúton." jelenik meg a végfelhasználó számára.

Az AzureHtml5JS nem tudott lejátszani egy http 404-es válaszból.

### <a name="categorizing-errors"></a>Kategorizálási hibák ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Egy adott hiba elfogása ###

A következő kód fogások csak 404 hibák:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)