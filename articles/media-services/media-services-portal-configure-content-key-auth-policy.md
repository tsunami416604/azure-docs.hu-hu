---
title: "Az Azure portál használatával Tartalomkulcs hitelesítési szabályzatának konfigurálása |} Microsoft Docs"
description: "Útmutató: a tartalomkulcs hitelesítési szabályzatának konfigurálása."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 5a35c7255a1c30a693862589c14f6a22a1900790
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="configure-content-key-authorization-policy"></a>Konfigurálja a Tartalomkulcs-hitelesítési házirend
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
A Microsoft Azure Media Services lehetővé teszi, hogy MPEG-DASH, Smooth Streaming vagy HTTP-Live-Streaming (HLS) streamjeit az Advanced Encryption Standard (AES) (a 128 bites titkosítási kulcsok használatával) vagy [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS azt is lehetővé teszi, hogy a Widevine DRM titkosított DASH-streameket. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

A Media Services is biztosít a **kulcs/Licenctovábbítási szolgáltatása** , amelyekről az ügyfelek úgy szerezheti be AES-kulcsok vagy a PlayReady vagy Widevine-licencek számára, hogy a titkosított tartalmat.

Ez a témakör bemutatja, hogyan használható az Azure-portálon konfigurálja a tartalomkulcs-hitelesítési házirendet. A kulcs később használható a dinamikusan titkosítani. Vegye figyelembe, hogy jelenleg titkosíthatja a következő adatfolyam-formátumok: HLS, MPEG DASH vagy Smooth Streaming. Progresszív letöltés nem titkosítható.

Amikor egy player olyan adatfolyamra, amely dinamikusan legyen titkosítva van beállítva, a Media Services a konfigurált kulcs segítségével dinamikusan titkosítani az AES vagy DRM titkosítással. Az adatfolyam visszafejtése, a Windows Media player a kulcs kézbesítési szolgáltatás fog igényelni a kulcsot. Döntse el, hogy a felhasználó jogosult-e a kulcs eléréséhez, hogy a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

Ha azt tervezi, hogy több tartalomkulcs esetleg adjon meg egy **kulcs/Licenctovábbítási szolgáltatása** eltérő a Media Services kulcs kézbesítési szolgáltatás URL-CÍMÉT használja a Media Services .NET SDK-t vagy a REST API-k.

[Media Services .NET SDK használatával Tartalomkulcs hitelesítési szabályzatának konfigurálása](media-services-dotnet-configure-content-key-auth-policy.md)

[Media Services REST API használatával Tartalomkulcs hitelesítési szabályzatának konfigurálása](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Vegye figyelembe a következőket:
* Az AMS-fiók létrehozásakor egy **alapértelmezett** adatfolyam-továbbítási végpontra a fiókja hozzáadódik a **leállítva** állapotát. Start streaming a tartalmat, és használja fel a dinamikus csomagolás és a dinamikus titkosítás, az adatfolyam-továbbítási végpontjának tartalmaznia kell a **futtató** állapotát. 
* Az eszköz tartalmaznia kell egy adaptív sávszélességű MP4 vagy Smooth Streaming-fájlsorozattá készletét. További információkért lásd: [kódolása egy eszköz](media-services-encode-asset.md).
* A kulcs kézbesítési szolgáltatás 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és a kapcsolódó objektumok (házirend-beállítások és korlátozásai).  Ha létrehoz egy ContentKeyAuthorizationPolicy, és adja meg, hogy a "Token" korlátozás, tesztelje azt, és majd a házirend "Megnyitás" korlátozás, előtt a házirend vált, a "Megnyitás" verziójának a házirend nagyjából 15 percig tart.

## <a name="how-to-configure-the-key-authorization-policy"></a>Útmutató: a hitelesítési házirend konfigurálása
A hitelesítési házirend konfigurálásához jelölje ki a **TARTALOMVÉDELEM** lap.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-hitelesítési házirend rendelkezhet **nyissa meg a**, **token**, vagy **IP** engedélyezési korlátozások (**IP** konfigurálható REST- vagy .NET SDK-val).

### <a name="open-restriction"></a>Nyissa meg a szoftverkorlátozó
A **nyissa meg a** korlátozás azt jelenti, hogy a rendszer számára, akik egy kulcs kérést fog továbbítani a kulcsot. Ez a korlátozás tesztelési célokra hasznos lehet.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Token korlátozása
A token korlátozott házirend kiválasztásához nyomja le az **TOKEN** gombra.

A **token** korlátozott házirend által kiadott tokennek kell fűzni a **Secure Token Service** (STS). A Media Services tokeneket támogatja a **Simple Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) formátumú és **JSON Web Token** (JWT) formátumú. További információ: [JWT jogkivonat hitelesítési](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Nem biztosít a Media Services **Token szolgáltatások biztonságos**. Hozzon létre egy egyéni STS, vagy probléma jogkivonatokat a Microsoft Azure ACS kihasználja. Az STS be kell állítani a megadott kulcs és a probléma JOGCÍMEKKEL, amely a token korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcs kézbesítési szolgáltatás a titkosítási kulcs visszatér az ügyfélhez, ha a jogkivonat érvényes, és a jogkivonatában lévő jogcímeket megegyezzenek a tartalomkulcsot. További információkért lásd: [használata Azure ACS probléma jogkivonatokat a](http://mingfeiy.com/acs-with-key-services).

Konfigurálásakor a **TOKEN** korlátozott házirend, meg kell adni az értékeket **ellenőrző kulcs**, **kibocsátó** és **célközönség**. Az elsődleges hitelesítési kulcs, amely a token aláírt kulcsot tartalmazza, a kibocsátó a biztonságos biztonságijogkivonat-szolgáltatás, amely kibocsátja a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket.

### <a name="playready"></a>PlayReady
Ha az a tartalmak védelmére **PlayReady**, az egyikét meg kell adni a hitelesítési házirend-e a PlayReady licencsablon definiáló XML-karakterlánc. Alapértelmezés szerint állítsa be a következő házirendet:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"><LicenseTemplates> <PlayReadyLicenseTemplate> <AllowTestDevices>igaz</AllowTestDevices> <ContentKey i:type="ContentEncryptionKeyFromHeader" /> <LicenseType>Nonpersistent</LicenseType> <PlayRight> <AllowPassingVideoContentToUnknownOutput>engedélyezett</AllowPassingVideoContentToUnknownOutput> </PlayRight> </PlayReadyLicenseTemplate> </LicenseTemplates></PlayReadyLicenseResponseTemplate>

Kattintson a **importálni a házirend XML-** gombra, és adja meg a különböző XML, amely megfelel a megadott XML-séma [Itt](media-services-playready-license-template-overview.md).

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

