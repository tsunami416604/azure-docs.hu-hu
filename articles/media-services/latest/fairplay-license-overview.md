---
title: Media Services és az Apple FairPlay-licenc-támogatás – Azure |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt az Apple FairPlay-licenc követelményeit és konfigurációját.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 66d816795ec06891aafce73036d7aea9bb52b2c8
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140532"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Apple FairPlay-licenc követelményeit és konfigurációját 

Az Azure Media Services lehetővé teszi, hogy a HLS-tartalmak **Apple fairplay által** (AES-128 CBC). Media Services modult FairPlay-licenc is biztosít. Ha a lejátszó próbál FairPlay által védett tartalom lejátszása, licencet beszereznie kérelmet küldött a licenctovábbítási szolgáltatása. Ha a szolgáltatás jóváhagyja a kérést, a licenc van elküldhetők az ügyfélprogramnak, és a megadott tartalom lejátszása és visszafejtésére szolgál kapcsolatos problémák.

A Media Services Ezenfelül API-k, amelyek segítségével konfigurálhatja a FairPlay-licenc. Ez a témakör ismerteti a FairPlay licenckövetelmények vonatkoznak, és bemutatja, hogyan konfigurálhat egy **FairPlay** licenc Media Sercies API-k használatával. 

## <a name="requirements"></a>Követelmények

A következők szükségesek, Media Services használata közben, a HLS-tartalmak **Apple fairplay által** és a FairPlay-licencek Media Services használatával:

* Regisztrálás a következővel [Apple fejlesztői programjában](https://developer.apple.com/).
* Apple szükséges a tartalom tulajdonosa beszerzése a [központi telepítési csomag](https://developer.apple.com/contact/fps/). Adja meg, hogy már megvalósította a kulcs biztonsági modul (KSM) a Media Services használatával, és, hogy a kért a végső FPS csomag. Nincsenek a végső FPS csomag létrehozása a hitelesítésszolgáltató, és szerezze be a titkos kulcs (?) témakör utasításait. Kérje meg a FairPlay konfigurálása használhatja.
* Media Services kulcs kézbesítési oldalon be kell állítani a az alábbiakat:

    * **Alkalmazás-tanúsítvány (AC)**: Ez az egy .pfx-fájlt, amely tartalmazza a titkos kulcsot. A fájl létrehozásához és a titkosítás, a jelszó. A .pfx fájl shoul Base64 formátumban kell.

        Az alábbi lépések bemutatják, hogyan hozhat létre egy .pfx formátumú tanúsítványfájlt a fairplay rendszerhez:

        1. Telepítse az OpenSSL https://slproweb.com/products/Win32OpenSSL.html.

            Lépjen abba a mappába, amelyeknél a FairPlay tanúsítvány és az Apple által biztosított egyéb fájlokat.
        2. Futtassa az alábbi parancsot a parancssorból. A .cer fájl konvertál egy .pem fájlra.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509-tájékoztatja der-a FairPlay.cer-FairPlay-out.pem ki
        3. Futtassa az alábbi parancsot a parancssorból. A titkos kulccsal is egy .pfx fájlba alakítja a .pem-fájlt. OpenSSL majd felkéri, a jelszót a .pfx fájl számára.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-exportálás - FairPlay-out.pfx ki-inkey privatekey.pem – a FairPlay-out.pem - passin file:privatekey-pem-pass.txt
            
    * **Alkalmazás tanúsítvány jelszava**: A jelszót a .pfx-fájl létrehozásához.
    * **Kérje meg**: ezt a kulcsot érkezik, amikor tanúsítványt generál az Apple fejlesztői portál használatával. Minden egyes fejlesztési csapat kap egy egyedi kérje meg. Másolat készítése a kérje meg a, és tárolja biztonságos helyen. Kérje meg a Media Services FairPlayAsk konfigurálni kell.
    
* A következőkre FPS ügyféloldali be kell állítania:

  * **Alkalmazás-tanúsítvány (AC)**: egy.cer/.der-fájl, amely tartalmazza a nyilvános kulcsot, amely néhány hasznos titkosításához használja az operációs rendszer. A Media Services kell ismernem, mert a a Windows Media Player. A kulcstovábbítást visszafejti a megfelelő titkos kulccsal.

* A FairPlay titkosított stream lejátszás, első beolvasása egy valódi kérje meg, és ezután hozza létre a tanúsítványnak valódi. A folyamat hoz létre minden három részből áll:

  * .der fájl
  * .pfx file
  * a .pfx jelszava

## <a name="fairplay-and-player-apps"></a>FairPlay és lejátszó alkalmazások

Ha a tartalom titkosított a **Apple fairplay által**, az egyes video- és minták segítségével lettek titkosítva a **AES-128 CBC** mód. **FairPlay Streaming** (FPS) integrálva van az eszköz operációs rendszerének támogatást biztosít az iOS és az Apple TV. OS x-en a Safari FPS az Encrypted Media Extensions (EME) felületen támogatási segítségével teszi lehetővé.

Az Azure Media Player támogatja a FairPlay lejátszási is. További információkért lásd: [dokumentáció az Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html).

Az iOS SDK használatával is fejleszthet saját player alkalmazásokat. Hogy a FairPlay lejátszani, kell megvalósítani a licenc az exchange-protokollt. Ez a protokoll nem adott meg az Apple. Esetén a minden alkalmazás kulcskézbesítési kérelem küldése. A Media Services FairPlay kulcstovábbítást vár a SPC lesz, mint egy www-form-url kódolt üzenet közzététele, a következő formátumban:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay-konfiguráció .NET-példa

Media Services API segítségével konfigurálhatja a FairPlay-licenc. FairPlay által védett tartalom lejátszása a Media player próbál, amikor kérelem érkezik a licenctovábbítási szolgáltatása a licenc beszerzéséhez. A szolgáltatás jóváhagyja a kérést, ha a szolgáltatásproblémák a licencet. Ez van az ügyfélnek küldött, és a megadott tartalom lejátszása és visszafejtésére szolgál.

> [!NOTE]
> Általában érdemes FairPlay házirend beállításainak konfigurálása csak egyszer, mert csak van egy tanúsítási és a egy kérje meg egy készletét.

Az alábbi példában [Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) konfigurálása a licencet.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>További lépések

Tekintse meg, hogy miként [DRM védelme](protect-with-drm.md)
