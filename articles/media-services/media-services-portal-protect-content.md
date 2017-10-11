---
title: "Az Azure portál használatával tartalomvédelem-szabályzatok konfigurálása |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan használhatja az Azure-portálon tartalomvédelem szabályzatok konfigurálására. A cikk azt is bemutatja, hogyan engedélyezheti az eszközök dinamikus titkosítást."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 67b3fa9936daebeafb7e87fe3a7b0c7e0105b3b3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Az Azure portál használatával tartalomvédelem-szabályzatok konfigurálása
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> 
> 

## <a name="overview"></a>Áttekintés
A Microsoft Azure Media Services (AMS) lehetővé teszi a media a tárhely, feldolgozás és kézbesítési keresztül elhagyja óta. A Media Services lehetővé teszi, hogy a tartalom titkosított dinamikusan az Advanced Encryption Standard (AES) (a 128 bites titkosítási kulcsok használatával), közös titkosítás (CENC) segítségével PlayReady és/vagy a Widevine DRM-Védelemmel, és az Apple FairPlay. 

AMS DRM-licencek kézbesítéséhez szolgáltatást nyújt, és AES törölje az arra jogosult ügyfelek kulccsal. Az Azure-portálon lehetővé teszi, hogy hozzon létre egyet **kulcs/licenc engedélyezési házirend** minden típusú titkosítások használatára.

Ez a cikk bemutatja, hogyan tartalomvédelem-szabályzatok konfigurálása az Azure-portálon. A cikk azt is bemutatja, hogyan alkalmazni a dinamikus titkosítás.


> [!NOTE]
> Ha a klasszikus Azure portálon adatvédelmi szabályzatok létrehozásához használt, a házirendek nem szerepelhet a [Azure-portálon](https://portal.azure.com/). Azonban az összes a régi házirendek továbbra is létezik. Tekintse meg őket az Azure Media Services .NET SDK használatával vagy a [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases) eszköz (a szabályzatok megtekintéséhez kattintson a jobb gombbal az eszközre a megjelenített információk (F4) -> -> tartalomkulcs lapon kattintson a -> kattintson a kulcs). 
> 
> Ha szeretné titkosítani az eszköz új szabályzatokkal, konfigurálja őket az Azure portálon, kattintson a Mentés gombra, és alkalmazza újra a dinamikus titkosítás. 
> 
> 

## <a name="start-configuring-content-protection"></a>Indítsa el a tartalom védelmének beállítása
A portál segítségével indítsa el a tartalom védelmének konfigurálása, globális az AMS-fiók, a következő módon:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Válassza ki **beállítások** > **védelmi tartalom**.

![Tartalom védelme](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Kulcs/licenc engedélyezési házirend
AMS több módon azok a felhasználók, akik vagy licencelési kérelmeket támogatja. A tartalomkulcs-hitelesítési szabályzatot kell Ön által konfigurált és érheti el, ha az ügyfél delived kell ahhoz, hogy a kulcs/licencfeltételeket ügyfélprogramba. A tartalomkulcs-hitelesítési házirend rendelkezhet egy vagy több engedélyezési korlátozás: **nyissa meg a** vagy **token** korlátozás.

Az Azure-portálon lehetővé teszi, hogy hozzon létre egyet **kulcs/licenc engedélyezési házirend** minden típusú titkosítások használatára.

### <a name="open"></a>Nyílt
Nyissa meg a szoftverkorlátozó azt jelenti, hogy a rendszer számára, akik egy kulcs kérést fog továbbítani a kulcsot. Ez a korlátozás tesztelési célokra hasznos lehet. 

### <a name="token"></a>Token
A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. A Media Services Simple Web Tokens (SWT) és a JSON webes jogkivonat (JWT) formátumú tokeneket támogatja. A Media Services nem nyújt Secure Token szolgáltatásokat. Hozzon létre egy egyéni STS, vagy probléma jogkivonatokat a Microsoft Azure ACS kihasználja. Az STS be kell állítani a megadott kulcs és a probléma JOGCÍMEKKEL, amely a token korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcs kézbesítési szolgáltatás visszaáll a kért (vagy licencelési) az ügyfél, ha a jogkivonat érvényes, és a jogcímek, az token találat azokat konfigurált kulcsot (vagy licenc).

Ha a házirend konfigurálása a token korlátozott, az elsődleges hitelesítési kulcs, a kibocsátó és a célközönség paramétereket kell megadnia. Az elsődleges hitelesítési kulcs, amely a token aláírt kulcsot tartalmazza, a kibocsátó a biztonságos biztonságijogkivonat-szolgáltatás, amely kibocsátja a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket.

![Tartalom védelme](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>PlayReady-jogosultságsablont
A PlayReady jogosultságsablont kapcsolatos részletes információkért lásd: [Media Services PlayReady licenc sablon áttekintése](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Nem állandó
Licenc nem állandó, konfigurálása, ha azt csak használatban van memória amíg a Windows Media player használja a licenc.  

![Tartalom védelme](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Állandó
Ha a licenc állandó, konfigurálása, a Mentés az állandó tároló a az ügyfél.

![Tartalom védelme](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Widevine-jogosultságsablont
A Widevine jogosultságsablont kapcsolatos részletes információkért lásd: [Widevine-licenc sablon áttekintése](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Basic
Ha bejelöli **alapvető**, a sablon összes alapértelmezett érték jön létre.

### <a name="advanced"></a>Extra szintű
Widevine-konfigurációk előzetes lehetőségekről részletes ismertetése [ez](media-services-widevine-license-template-overview.md) témakör.

![Tartalom védelme](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-konfiguráció
FairPlay-titkosítás engedélyezéséhez meg kell adnia a App tanúsítvány és a alkalmazás titkos kulcs (KÉRJEN) keresztül a FairPlay konfigurációs beállítás. FairPlay konfigurációs és követelmények kapcsolatos részletes információkért lásd: [ez](media-services-protect-hls-with-fairplay.md) cikk.

![Tartalom védelme](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>A dinamikus titkosítás alkalmazása az eszközre
A dinamikus titkosítás előnyeit, a forrásfájl kódolása adaptív sávszélességű MP4-fájlokká be kell.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Válassza ki egy eszközt, hogy titkosítani szeretné
Az eszközök megtekintéséhez válasszon **beállítások** > **eszközök**.

![Tartalom védelme](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Az AES vagy DRM titkosítása
Ha lenyomja az **titkosítása** egy eszköz, lehetősége lesz két választási lehetőség: **AES** vagy **DRM**. 

#### <a name="aes"></a>AES
Törölje a jelet titkosítás engedélyezve lesz az összes adatfolyam-továbbítási protokollok AES: Smooth Streaming, HLS vagy MPEG-DASH.

![Tartalom védelme](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
A DRM lapon kiválasztásakor lehetősége lesz a tartalomvédelem házirendek más lehetőségek (amely konfigurálnia kell mostanra) + protokollok streameléshez készlete.

* **PlayReady és Widevine rendelkező MPEG-DASH** -rendszer dinamikus titkosítást a PlayReady és Widevine DRMs MPEG-DASH adatfolyam.
* **PlayReady és MPEG-DASH végzett Widevine + a HLS FairPlay** -dinamikusan titkosítja, MPEG-DASH-adatfolyam a PlayReady vagy Widevine DRMs. A HLS-adatfolyamok FairPlay is titkosítja.
* **Csak a Smooth Streaming, HLS, és MPEG-DASH PlayReady** -dinamikusan titkosítani fog Smooth Streaming, HLS, MPEG-DASH-adatfolyamok PlayReady DRM.
* **Csak a MPEG-DASH Widevine** -dinamikusan titkosítani, MPEG-DASH a Widevine DRM-Védelemmel.
* **Csak a HLS FairPlay** -dinamikusan titkosítja a HLS adatfolyam FairPlay.

FairPlay-titkosítás engedélyezéséhez meg kell adnia a App tanúsítvány és a alkalmazás titkos kulcs (KÉRJEN) keresztül a FairPlay konfigurációs beállítást, a tartalom védelmi beállítások panelről.

![Tartalom védelme](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Miután elvégezte a kijelölt titkosítás, nyomja le az **alkalmaz**.

>[!NOTE] 
>Ha azt tervezi, számára, hogy az AES titkosított HLS a Safari című [ebben a blogban](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Következő lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

