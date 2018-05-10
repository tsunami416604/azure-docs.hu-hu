---
title: A tartalomkulcs-hitelesítési házirend konfigurálása az Azure portál használatával |} Microsoft Docs
description: 'Útmutató: a tartalomkulcs hitelesítési szabályzatának konfigurálása.'
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: db0117b0b4ddee002fc69d71e78eca2b9008e4f6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-content-key-authorization-policy"></a>A tartalomkulcs-hitelesítési házirend konfigurálása
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 Azure Media Services segítségével MPEG-DASH, Smooth Streaming vagy HTTP-Live Streaming (HLS) streamjeit az Advanced Encryption Standard (AES) technológia 128 bites titkosítási kulcsok használatával vagy [PlayReady digitális tartalomvédelmi (DRM)](https://www.microsoft.com/playready/overview/). A Media Services is biztosíthat a Widevine DRM titkosított DASH-streameket. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

Media Services is biztosít, a kulcs/licenctovábbítási szolgáltatása, amelyből az ügyfelek beszerezhetnek AES-kulcsok vagy a PlayReady vagy Widevine-licencek számára, hogy a titkosított tartalmat.

Ez a cikk bemutatja, hogyan használható az Azure-portálon konfigurálja a tartalomkulcs-hitelesítési házirendet. A kulcs később használható a dinamikusan titkosítani. Titkosíthatja jelenleg, HLS, MPEG-DASH vagy Smooth Streaming formátumban. Progresszív letöltés nem titkosítható.

Amikor egy player olyan adatfolyamra, amely dinamikusan legyen titkosítva van beállítva, a Media Services a konfigurált kulcs segítségével dinamikusan titkosítani az AES vagy DRM titkosítással. Az adatfolyam visszafejtése, a Windows Media player kér a kulcsot a fő kézbesítési szolgáltatás. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs eléréséhez, a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

Ha több tartalomkulcs esetleg egy kulcs/licenc kézbesítési szolgáltatás URL-cím a Media Services kulcs kézbesítési szolgáltatás nem tervezi, használja a Media Services .NET SDK-t vagy a REST API-k. További információkért lásd:

* [A Media Services .NET SDK használatával a tartalomkulcs-hitelesítési házirend konfigurálása](media-services-dotnet-configure-content-key-auth-policy.md)
* [A Media Services REST API használatával a tartalomkulcs-hitelesítési házirend konfigurálása](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Vegye figyelembe a következőket
* A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. Indítsa el a tartalom streaming és a dinamikus csomagolás és a dinamikus titkosítás, az adatfolyam-továbbítási végpontjának csak a "Fut" állapotú. 
* Az eszköz tartalmaznia kell egy adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá készletét. További információkért lásd: [kódolása egy eszköz](media-services-encode-asset.md).
* A kulcs kézbesítési szolgáltatás 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és a kapcsolódó objektumok (házirend-beállítások és korlátozásai). Hozzon létre egy ContentKeyAuthorizationPolicy, és adja meg, hogy a token korlátozás, tesztelheti, és módosítsa a házirendet a nyitott korlátozás. Ez a folyamat verzió megnyitása a házirend kapcsolók nagyjából 15 percet vesz igénybe.
* A Media Services adatfolyam-továbbítási végpontra beállítja a CORS hozzáférés-vezérlési-engedélyezése-forrás-fejléc értékének elővizsgálati választ, mint a helyettesítő karakter "\*". Ez az érték jól működik a legtöbb szereplő, köztük az Azure Media Player Roku és JWPlayer és mások számára. Azonban nem működnek néhány lejátszó dash.js használó, mert a hitelesítő adatok módban "tartalmaz", a saját dash.js XMLHttpRequest nem engedélyezi a helyettesítő karakter "\*" a hozzáférés-vezérlési-engedélyezése-forrás értékeként. Ezt a korlátozást a dash.js a megoldás Ha az ügyfél egy egyetlen tartományból Media Services adhat meg, hogy a tartomány a elővizsgálati válaszfejlécet. Ha segítségre van szüksége az Azure portálon keresztül támogatási jegy megnyitása.

## <a name="configure-the-key-authorization-policy"></a>A hitelesítési házirend konfigurálása
A hitelesítési házirend konfigurálásához jelölje ki a **TARTALOMVÉDELEM** lap.

Media Services hitelesíti a felhasználókat, akik kulcs kérelmeket, több lehetőség is támogatja. A tartalomkulcs-hitelesítési házirend rendelkezhet a megnyitva, lexikális elem, vagy az IP-engedélyezési korlátozásokat. (IP konfigurálható a többi vagy a .NET SDK.)

### <a name="open-restriction"></a>Nyissa meg a szoftverkorlátozó
Nyissa meg a szoftverkorlátozó azt jelenti, hogy a rendszer a kulcs továbbítja bárki, aki egy kulcs kérést. Ez a korlátozás tesztelési célokra hasznos lehet.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Token korlátozása
Válassza ki a token korlátozott szabályzatot, jelölje be a **TOKEN** gombra.

A token korlátozott házirend biztonságijogkivonat-szolgáltatás (STS) által kiadott tokennek kell csatolni. Media Services jogkivonatokat támogatja az egyszerű webes jogkivonat ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) és az JSON webes jogkivonat (JWT) formátumú. További információkért lásd: [JWT hitelesítési](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

A Media Services STS nem biztosít. Létrehozhat egy egyéni STS jogkivonatokat kibocsátani. Az STS be kell állítani a megadott kulcs és a probléma JOGCÍMEKKEL, amely a token korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. Ha a jogkivonat érvényes, és a jogkivonatában lévő jogcímeket megegyezzenek a tartalomkulcsot, a Media Services kulcs kézbesítési szolgáltatás visszaadja az ügyfélnek a titkosítási kulcs.

A token által korlátozott házirendjének konfigurálásakor adjon meg, hogy az elsődleges hitelesítési kulcs, a kibocsátó és a célközönség paramétereket. Az elsődleges hitelesítési kulcs, amely a token aláírt kulcsot tartalmazza. A nem az STS, amely kibocsátja a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket.

### <a name="playready"></a>PlayReady
Ha a tartalmaknak a PlayReady-védelemmel, az egyikét meg kell adni a hitelesítési házirend a PlayReady licencsablon definiáló XML-karakterlánc. Alapértelmezés szerint állítsa be a következő házirendet:

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

Kiválaszthatja a **importálni a házirend XML-** gombra, és adjon meg egy másik XML, amelyik megfelel a megadott XML-sémája a [Media Services PlayReady licenc sablon áttekintése](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

