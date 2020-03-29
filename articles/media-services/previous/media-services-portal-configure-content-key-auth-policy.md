---
title: Tartalomkulcs-engedélyezési házirend konfigurálása az Azure Portal használatával | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan konfigurálhat egy tartalomkulcs engedélyezési házirendjét.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 8580bafd4d68ef6567b09fefcaa01c682ae2cafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968790"
---
# <a name="configure-a-content-key-authorization-policy"></a>Tartalomkulcs-engedélyezési házirend konfigurálása
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 Az Azure Media Services segítségével MPEG-DASH, Smooth Streaming és HTTP Live Streaming (HLS) streameket biztosíthat advanced encryption Standard (AES) titkosítási szabványsal védve 128 bites titkosítási kulcsok vagy [PlayReady digitális jogkezelés (DRM)](https://www.microsoft.com/playready/overview/)használatával. A Media Services segítségével a Widevine DRM-mel titkosított DASH-adatfolyamokat is szállíthatja. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

A Media Services egy kulcs-/licenckézbesítési szolgáltatást is biztosít, amelyből az ügyfelek AES-kulcsokat vagy PlayReady/Widevine licenceket szerezhetnek a titkosított tartalom lejátszásához.

Ez a cikk bemutatja, hogyan használhatja az Azure Portalon a tartalomkulcs-engedélyezési szabályzat konfigurálásához. A kulcs később a tartalom dinamikus titkosítására használható. Jelenleg titkosíthatja a HLS, MPEG-DASH és Smooth Streaming formátumokat. A progresszív letöltéseket nem lehet titkosítani.

Amikor egy lejátszó dinamikusan titkosított adatfolyamot kér, a Media Services a konfigurált kulcsot használja a tartalom dinamikus titkosításához AES vagy DRM titkosítással. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs beakése, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

Ha több tartalomkulcsgal kíván rendelkezni, vagy a Media Services kulcskézbesítési szolgáltatástól eltérő kulcs-/licenckézbesítési szolgáltatás URL-címét szeretné megadni, használja a Media Services .NET SDK vagy REST API-kat. További információkért lásd:

* [Tartalomkulcs-engedélyezési házirend konfigurálása a Media Services .NET SDK használatával](media-services-dotnet-configure-content-key-auth-policy.md)
* [Tartalomkulcs-engedélyezési házirend konfigurálása a Media Services REST API használatával](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Néhány szempont vonatkozik
* A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom streamelésének megkezdéséhez és a dinamikus csomagolás és a dinamikus titkosítás előnyeinek kihasználásához a streamelési végpontnak "Futás" állapotban kell lennie. 
* Az eszköznek adaptív sávszélességű MP4-fájlokat vagy adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia. További információt az [Eszköz kódolása](media-services-encode-asset.md)című témakörben talál.
* A kulcskézbesítési szolgáltatás 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és a hozzá kapcsolódó objektumokat (házirend-beállítások és korlátozások). Létrehozhat egy ContentKeyAuthorizationPolicy házirendet, és megadhatja, hogy jogkivonat-korlátozást használjon, tesztelje, majd frissítse a házirendet a nyílt korlátozásra. Ez a folyamat nagyjából 15 percet vesz igénybe, mielőtt a házirend átvált a megnyitott verzióra.
* A Media Services streamelési végpontja a CORS Access-Control-Allow-Origin fejléc értékét\*az elővizsgálati válaszban helyettesítő karakterként " ".A Media Services streaming endpoint sets the value of the CORS Access-Control-Allow-Origin header in preflight response as the wildcard " ". Ez az érték jól működik a legtöbb játékos, beleértve az Azure Media Player, Roku és JWPlayer, és mások. A dash.js fájlt használó lejátszók némelyike azonban nem működik, mert a hitelesítő adatok mód "include" értékre\*van állítva, az XMLHttpRequest a dash.js fájlban nem engedélyezi a " helyettesítő karaktert access-control-allow-origin értékként. A dash.js e korlátozásának megkerüléseként, ha az ügyfelet egyetlen tartományból üzemelteti, a Media Services megadhatja az adott tartományt az elővizsgálati válasz fejlécében. Ha segítségre van szüksége, nyisson meg egy támogatási jegyet az Azure Portalon keresztül.

## <a name="configure-the-key-authorization-policy"></a>A kulcsengedélyezési házirend konfigurálása
A kulcsengedélyezési házirend konfigurálásához válassza a **CONTENT PROTECTION** lapot.

A Media Services többféle módon is támogatja a kulcskéréseket benyújtó felhasználók hitelesítését. A tartalomkulcs-engedélyezési házirend nyitott, jogkivonat- vagy IP-engedélyezési korlátozásokkal rendelkezhet. (Az IP konfigurálható a REST vagy a .NET SDK használatával.)

### <a name="open-restriction"></a>Nyílt korlátozás
A nyílt korlátozás azt jelenti, hogy a rendszer mindenkinek átadja a kulcsot, aki kulcskérést tesz. Ez a korlátozás tesztelési célokra hasznos lehet.

![OpenPolicy (Házirend megnyitása)][open_policy]

### <a name="token-restriction"></a>Token korlátozása
A token korlátozott házirendjének kiválasztásához válassza a **TOKEN gombot.**

A korlátozott jogkivonat-házirendet egy biztonsági jogkivonat-szolgáltatás (STS) által kibocsátott jogkivonatnak kell kísérnie. A Media Services támogatja az egyszerű webes jogkivonat ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) és json webtoken (JWT) formátumban lévő jogkivonatokat. További információ: [JWT-hitelesítés.](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

A Media Services nem biztosít STS-t. Létrehozhat egy egyéni STS-t a tokenek kiállításához. Az STS-t úgy kell konfigurálni, hogy hozzon létre egy jogkivonatot a megadott kulccsal, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket. Ha a jogkivonat érvényes, és a jogkivonatban lévő jogcímek megegyeznek a tartalomkulcshoz konfigurált jogcímekkel, a Media Services-kulcskézbesítési szolgáltatás visszaadja a titkosítási kulcsot az ügyfélnek.

A jogkivonat-korlátozott házirend konfigurálásakor meg kell adnia az elsődleges ellenőrzési kulcsot, a kibocsátót és a közönség paramétereit. Az elsődleges ellenőrzési kulcs tartalmazza azt a kulcsot, amelyhez a jogkivonat ot aláírták. A kibocsátó az STS, amely kiadja a jogkivonatot. A közönség (más néven hatókör) a jogkivonat vagy az erőforrás szándékát írja le, amelyhez a jogkivonat engedélyezi a hozzáférést. A Media Services-kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a jogkivonatban megegyeznek-e a sablonban lévő értékekkel.

### <a name="playready"></a>PlayReady
Ha a PlayReady segítségével védi a tartalmat, az engedélyezési házirendben megkell adnia az egyik olyan XML-karakterláncot, amely meghatározza a PlayReady licencsablont. Alapértelmezés szerint a következő házirend van beállítva:

    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
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

Kiválaszthatja az **importálási házirend xml** gombját, és egy másik XML-t adhat meg, amely megfelel a Media Services [PlayReady licencsablon áttekintésében](media-services-playready-license-template-overview.md)meghatározott XML-sémának.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

