---
title: Media Services és Apple FairPlay licenctámogatás - Azure | Microsoft dokumentumok
description: Ez a témakör áttekintést nyújt az Apple FairPlay licenckövetelményeiről és konfigurációjáról.
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
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733572"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Apple FairPlay-licencek követelményei és konfigurálása 

Az Azure Media Services lehetővé teszi a HLS-tartalom titkosítását az **Apple FairPlay** (AES-128 CBC) segítségével. A Media Services a FairPlay licencek kézbesítésével is szolgál. Amikor egy játékos megpróbálja lejátszani a FairPlay által védett tartalmat, a rendszer kérelmet küld a licenckézbesítési szolgáltatásnak, hogy szerezzen licencet. Ha a licencszolgáltatás jóváhagyja a kérelmet, kiadja az ügyfélnek küldött licencet, amely a megadott tartalom visszafejtésére és lejátszására szolgál.

A Media Services olyan API-kat is biztosít, amelyekkel konfigurálhatja a FairPlay-licenceket. Ez a témakör a FairPlay licenckövetelményeit ismerteti, és bemutatja, hogyan konfigurálhatja a **FairPlay-licencet** a Media Services API-k használatával. 

## <a name="requirements"></a>Követelmények

A következőkre van szükség, ha a Media Services segítségével titkosítja a HLS-tartalmat az **Apple FairPlay** segítségével, és a Media Services segítségével FairPlay licenceket biztosít:

* Regisztráljon az [Apple Fejlesztési Programmal.](https://developer.apple.com/)
* Az Apple megköveteli a tartalom tulajdonosát, hogy szerezze be a [telepítési csomagot](https://developer.apple.com/contact/fps/). Adja meg, hogy már megvalósította a Key Security Module (KSM) szolgáltatást a Media Services szolgáltatással, és hogy a végső FPS-csomagot kéri. Vannak utasítások a végső FPS csomagot generálni tanúsítás és megszerezni az application secret key (ASK). Az ASK segítségével konfigurálhatja a FairPlay-t.
* A Media Services kulcs-/licenckézbesítési oldalán a következő dolgokat kell beállítani:

    * **App Cert (AC)**: Ez egy .pfx fájl, amely tartalmazza a személyes kulcsot. Ezt a fájlt létrehozza, és jelszóval titkosítja. A .pfx fájlnak Base64 formátumúnak kell lennie.

        Az alábbi lépések bemutatják, hogyan hozhat létre .pfx tanúsítványfájlt a FairPlay számára:

        1. Telepítse az OpenSSL-t a programból. https://slproweb.com/products/Win32OpenSSL.html

            Nyissa meg azt a mappát, ahol a FairPlay tanúsítvány és az Apple által szállított egyéb fájlok találhatók.
        2. Futtassa az alábbi parancsot a parancssorból. Ezzel a .cer fájlt .pem fájllá alakítja.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. Futtassa az alábbi parancsot a parancssorból. Ezzel a .pem fájlt .pfx fájllá alakítja a személyes kulccsal. A .pfx fájl jelszavát ezután az OpenSSL kéri.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin fájl:privatekey-pem-pass.txt
            
    * **Alkalmazástanúsítvány-jelszó**: A .pfx fájl létrehozásának jelszava.
    * **ASK**: Ez a kulcs akkor érkezik, amikor az Apple Developer portál on keresztül létrehozza a tanúsítványt. Minden fejlesztőcsapat egyedi ASK-ot kap. Mentse el az ASK egy példányát, és tárolja biztonságos helyen. Az ASK-ot FairPlayAsk-ként kell konfigurálnia a Media Services szolgáltatással.
    
* Az FPS ügyféloldalnak a következő dolgokat kell beállítania:

  * **Alkalmazás-tanúsítvány (AC)**: Ez egy .cer/.der fájl, amely tartalmazza a nyilvános kulcsot, amelyet az operációs rendszer használ bizonyos hasznos adatok titkosítására. A Media Services-nek tudnia kell róla, mert a lejátszónak szüksége van rá. A kulcskézbesítési szolgáltatás visszafejti azt a megfelelő személyes kulccsal.

* FairPlay titkosított adatfolyam lejátszásához először szerezzen be egy valódi ASK-ot, majd hozzon létre egy valódi tanúsítványt. Ez a folyamat mindhárom részt létrehozza:

  * .der fájl
  * .pfx fájl
  * jelszót a .pfx

## <a name="fairplay-and-player-apps"></a>FairPlay és player alkalmazások

Ha a tartalom az **Apple FairPlay**segítségével van titkosítva, az egyes video- és hangminták az **AES-128 CBC** mód használatával lesznek titkosítva. **A FairPlay Streaming** (FPS) integrálva van az eszköz operációs rendszereibe, natív támogatással az iOS és az Apple TV rendszeren. A Safari on OS X lehetővé teszi az FPS használatát a Titkosított adathordozó-bővítmények (EME) felület támogatásával.

Az Azure Media Player támogatja a FairPlay lejátszást is. További információt az [Azure Media Player dokumentációjában](https://amp.azure.net/libs/amp/latest/docs/index.html)talál.

Az iOS SDK használatával saját játékosalkalmazásokat fejleszthet. A FairPlay-tartalom lejátszásához végre kell hajtania a licenccsere protokollt. Ezt a protokollt az Apple nem adja meg. Az egyes alkalmazásoktól megkell tudni, hogyan küldhetnek kulcskézbesítési kérelmeket. A Media Services FairPlay kulcskézbesítési szolgáltatás a következő formában várja, hogy az Alkalmazási előírás www-form-url kódolású üzenetként érkezzen:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Példa a FairPlay konfigurációra .NET

A Media Services API segítségével konfigurálhatja a FairPlay-licenceket. Amikor a játékos megpróbálja lejátszani a FairPlay által védett tartalmat, a rendszer kérést küld a licenckézbesítési szolgáltatásnak a licenc megszerzéséhez. Ha a licencszolgáltatás jóváhagyja a kérelmet, a szolgáltatás kiadja a licencet. A rendszer elküldi az ügyfélnek, és a megadott tartalom visszafejtésére és lejátszására szolgál.

> [!NOTE]
> Általában csak egyszer szeretné beállítani a FairPlay házirend-beállításait, mert csak egy minősítési és egy ASK készlete lesz.

A következő példa [a Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) segítségével konfigurálja a licencet.

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

Nézze meg, hogyan [védhet a DRM-mel](protect-with-drm.md)
