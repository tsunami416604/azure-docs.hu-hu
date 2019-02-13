---
title: A tartalomkulcs-hitelesítési szabályzat konfigurálása az Azure portal használatával |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálja a tartalomkulcs-engedélyezési házirendet.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 7dd056042b841e54c18ee1a667c44cfa11d77a61
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164600"
---
# <a name="configure-a-content-key-authorization-policy"></a>A tartalomkulcs-hitelesítési szabályzat konfigurálása
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 Az Azure Media Services segítségével MPEG-DASH, Smooth Streaming vagy HTTP Live Streaming (HLS), az Advanced Encryption Standard (AES) védett kulcsokkal 128 bites titkosítás Streamek biztosításához vagy [PlayReady digitális jogkezelési technológia (DRM)](https://www.microsoft.com/playready/overview/). A Media Services emellett eljuttatható Widevine DRM-mel az titkosított DASH-streameket. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

Media Services egy kulcs/licenckézbesítési szolgáltatást, amelyből az ügyfelek szerezheti be az AES-kulcsok vagy a titkosított tartalom lejátszása PlayReady/Widevine-licencek is biztosít.

Ez a cikk bemutatja, hogyan konfigurálja a tartalomkulcs-hitelesítési szabályzatot az Azure portal használatával. A kulcsot később segítségével dinamikusan titkosítani a tartalmat. Jelenleg hogy titkosítsa, HLS, MPEG-DASH és Smooth Streaming formátumban. A progresszív letöltés nem titkosítható.

Amikor egy olyan adatfolyamra, amely dinamikusan legyen titkosítva van beállítva, a Media Services dinamikus titkosítást tartalmait az AES vagy DRM-titkosítás segítségével a konfigurált kulcsot használja. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult kulcs lekérése, a szolgáltatás kiértékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

Ha több tartalomkulcs vagy egy kulcs kézbesítési szolgáltatás URL-címet a Media Services kulcs-kézbesítési szolgáltatás nem szeretné, akkor a Media Services .NET SDK vagy a REST API-k. További információkért lásd:

* [Konfigurálja a tartalomkulcs-hitelesítési házirend a Media Services .NET SDK használatával](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurálja a tartalomkulcs-hitelesítési házirend a Media Services REST API használatával](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Bizonyos szempontokat kell figyelembe venni
* A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom streamelésének megkezdéséhez, és a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpont kell "Fut" állapotban. 
* Az objektumnak tartalmaznia kell egy adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá készletét. További információkért lásd: [kódolása](media-services-encode-asset.md).
* A kulcstovábbítást 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és a kapcsolódó objektumok (házirend-beállításokat, és korlátozásai). Hozzon létre egy ContentKeyAuthorizationPolicy, és adja meg, hogy a jogkivonat korlátozás, tesztelheti, és frissíti a szabályzatot a megnyitott korlátozás. Ez a folyamat verzió megnyitása a szabályzat kapcsolók nagyjából 15 percet vesz igénybe.
* A Media Services streaming endpoint előzetes válaszként, mint a helyettesítő karaktert a CORS Access-Control-Allow-Origin fejléc értékének beállítása "\*". Ez az érték nagyszerűen működik a legtöbb szereplő, köztük az Azure Media Player Roku és JWPlayer és mások. Azonban nem működnek bizonyos lejátszók dash.js-sel használó, mert a hitelesítő adatok mód "include" értékre, azok dash.js-sel az XMLHttpRequest nem teszi lehetővé a helyettesítő karakteres "\*" Access-Control-Allow-Origin értékeként. Ezt a korlátozást a dash.js-sel való Áthidaló megoldásként az ügyfél egy egyetlen tartományból, ha a Media Services megadhatja tartomány az előzetes válaszfejléc. Segítségért nyisson meg egy támogatási jegyet az Azure Portalon keresztül.

## <a name="configure-the-key-authorization-policy"></a>A hitelesítési szabályzat konfigurálása
A hitelesítési szabályzat konfigurálásához válassza a **CONTENT PROTECTION** lapot.

A Media Services támogatja a több lehetőség is hitelesítheti a felhasználókat, akik key kéréseket. A tartalomkulcs-hitelesítési szabályzatot is van nyitva, jogkivonat vagy IP-engedélyezési korlátozások. (IP konfigurálható REST vagy a .NET SDK-t.)

### <a name="open-restriction"></a>Nyissa meg a korlátozás
A megnyitott korlátozás azt jelenti, hogy a rendszer továbbítja a kulcsot bárki számára legfontosabb kérést küld. Ez a korlátozás tesztelési célokra hasznos lehet.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Token korlátozás
A tokennel korlátozott szabályzatokhoz kiválasztásához jelölje ki a **TOKEN** gombra.

A tokennel korlátozott szabályzatokhoz a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell mellékelni. A Media Services jogkivonatokat támogatja az egyszerű webes jogkivonat ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) és a JSON webes jogkivonat (JWT) formátumokat. További információkért lásd: [JWT hitelesítési](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

A Media Services STS nem biztosít. Létrehozhat egy egyéni STS jogkivonatok kiállításához. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. Ha a jogkivonat érvényes, és a jogkivonat jogcímeiben megfelelnek a tartalomkulcs, a Media Services kulcstovábbítást a titkosítási kulcsot az ügyfélnek ad vissza.

Amikor a jogkivonattal korlátozott szabályzatokat konfigurál, az elsődleges ellenőrzőkulcs, a kibocsátó és a célközönség paramétereket kell megadnia. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e. A nem az STS-ben a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcstovábbítást ellenőrzi, hogy ezeket az értékeket a jogkivonat egyezik a sablonban szereplő értékeket.

### <a name="playready"></a>PlayReady
Amikor Ön tartalmai védelmét a PlayReady, a dolgokat, meg kell adnia az engedélyezési házirendben egyik XML-karakterlánc, amely meghatározza a PlayReady-licencsablon. Alapértelmezés szerint állítsa be a következő szabályzatot:

    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

Kiválaszthatja a **importálja a házirend xml** gombra, és adjon meg egy másik XML-t, amely megfelel a megadott XML-séma a [a Media Services PlayReady licencsablon áttekintése](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

