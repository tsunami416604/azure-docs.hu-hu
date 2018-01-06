---
title: "Tartalomvédelem-szabályzatok konfigurálása az Azure portál használatával |} Microsoft Docs"
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
ms.openlocfilehash: 805e1246dbc984582528d2b351d2f14ab2e811fc
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Tartalomvédelem-szabályzatok konfigurálása az Azure portál használatával
 Az Azure Media Services biztonságossá teheti a média a tárhely, feldolgozás és kézbesítési keresztül elhagyja óta. A Media Services segítségével a 128 bites titkosítási kulcsok használatával dinamikusan rendelkező az Advanced Encryption Standard (AES) titkosított tartalom. Is használhatja a közös titkosítás (CENC) PlayReady és/vagy Widevine digitális tartalomvédelmi (DRM) és az Apple FairPlay használatával. 

A Media Services biztosít egy szolgáltatás, amelynek segítségével a DRM-licencek és AES törölje az arra jogosult ügyfelek kulccsal. Az Azure-portálon hozhat létre egy kulcs/licenc engedélyezési házirend az összes titkosítások használatára.

Ez a cikk bemutatja, hogyan tartalomvédelem-házirendet konfigurálhat a portál használatával. A cikk azt is bemutatja, hogyan alkalmazni a dinamikus titkosítás.

## <a name="start-to-configure-content-protection"></a>Indítsa el a tartalom-védelem konfigurálása
A portál segítségével globális tartalomvédelem konfigurálása a Media Services-fiók használatával, a következő lépéseket:

1. Az a [portal](https://portal.azure.com/), válassza ki a Media Services-fiókját.

2. Válassza ki **beállítások** > **védelmi tartalom**.

    ![Tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Kulcs-/engedélyezési házirend
Media Services több módon azok a felhasználók, akik vagy licencelési kérelmeket támogatja. Konfigurálnia kell a tartalomkulcs-hitelesítési házirendet. Az ügyfél majd meg kell felelnie a házirend a kulcs/licencfeltételeket továbbítható előtt. A tartalomkulcs-hitelesítési házirend egy vagy több engedélyezési korlátozások, nyitott vagy token korlátozások is rendelkezhetnek.

A portál segítségével hozzon létre egy kulcs/licenc engedélyezési házirend az összes titkosítások használatára.

### <a name="open-authorization"></a>Megnyitás engedélyezése
Nyissa meg a szoftverkorlátozó azt jelenti, hogy a rendszer továbbítja a kulcsot bárki, aki egy kulcs kérést. Ez a korlátozás tesztelési célokra hasznos lehet. 

### <a name="token-authorization"></a>Engedélyezési jogkivonat
A token által korlátozott házirend biztonságijogkivonat-szolgáltatás (STS) által kiadott tokennek kell csatolni. A Media Services jogkivonatokat támogatja az egyszerű webes jogkivonat (SWT) és a JSON webes jogkivonat (JWT) formátumban. A Media Services biztonságijogkivonat-szolgáltatás nem biztosít. Hozzon létre egy egyéni STS, vagy probléma jogkivonatok hozzáférés-vezérlési szolgáltatásban Azure segítségével. Az STS be kell állítani a megadott kulcs és a probléma JOGCÍMEKKEL, amely a token korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. Ha a jogkivonat érvényes, és a jogkivonatában lévő jogcímeket pontosan megegyeznek a (vagy licencelési) beállítást, a Media Services kulcs kézbesítési szolgáltatás visszatér a kért (vagy licencelési) az ügyfél.

A token által korlátozott házirendjének konfigurálásakor adjon meg, hogy az elsődleges hitelesítési kulcs, a kibocsátó és a célközönség paramétereket. Az elsődleges hitelesítési kulcs, amely a token aláírt kulcsot tartalmazza. A nem a biztonságos biztonságijogkivonat-szolgáltatás, amely kibocsátja a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket.

![Kulcs-/engedélyezési házirend](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licenc sablonja
A PlayReady licencsablon beállítása, amelyen engedélyezve van a funkciót a PlayReady-licenc. A PlayReady licencsablon kapcsolatos további információkért tekintse meg a [Media Services PlayReady licenc sablon áttekintése](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Nem állandó
A licenc nem állandó, konfigurálása, ha az használatban van memória csak, míg a Windows Media player használja a licenc.  

![Nem állandó tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Tartós
Ha a konfigurált licenc állandó, a Mentés az állandó tároló a az ügyfél.

![Állandó tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licencsablon
A Widevine-licencsablon a funkciót, amelyen engedélyezve van a Widevine-licencek beállítása.

### <a name="basic"></a>Alapszintű
Ha bejelöli **alapvető**, a sablon összes alapértelmezett értékekkel jön létre.

### <a name="advanced"></a>Extra szintű
A Widevine jogosultságsablont kapcsolatos további információkért tekintse meg a [Widevine-licenc sablon áttekintése](media-services-widevine-license-template-overview.md).

![Speciális tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-konfiguráció
FairPlay-titkosítás engedélyezéséhez jelölje be **FairPlay konfigurációs**. Válassza ki a **App tanúsítvány** , és írja be a **alkalmazás titkos kulcs**. FairPlay konfigurációs és követelményeiről további információkért lásd: [a HLS Apple FairPlay vagy a Microsoft PlayReady tartalom védelméhez](media-services-protect-hls-with-FairPlay.md).

![FairPlay-konfiguráció](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>A dinamikus titkosítás alkalmazása az eszközre
A dinamikus titkosítás előnyeit kódolja a forrásfájlt az adaptív sávszélességű MP4-fájlsorozattá.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Válassza ki egy eszközt, hogy titkosítani szeretné
Az eszközök megtekintéséhez válasszon **beállítások** > **eszközök**.

![Eszközök beállítás](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Az AES vagy DRM titkosítása
Ha bejelöli **titkosítása** eszköz, két választási lehetőség látja: **AES** vagy **DRM**. 

#### <a name="aes"></a>AES
Törölje a jelet titkosítás engedélyezve van az összes adatfolyam-továbbítási protokollok AES: Smooth Streaming, HLS vagy MPEG-DASH.

![Titkosítási konfiguráció](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Miután kiválasztotta a **DRM**, tekintse meg a tartalom különböző védelmi házirendek (amely be kell állítani a pont) és a folyamatos átviteli protokollok:

    a. **PlayReady és Widevine rendelkező MPEG-DASH** dinamikusan titkosítja a PlayReady és Widevine DRMs MPEG-DASH adatfolyam.

    b. **PlayReady és MPEG-DASH végzett Widevine + a HLS FairPlay** dinamikus titkosítást a PlayReady és Widevine DRMs MPEG-DASH adatfolyam. Ez a beállítás is titkosítja a HLS-adatfolyamok FairPlay.

    c. **Csak a Smooth Streaming, HLS, és MPEG-DASH PlayReady** dinamikusan a PlayReady DRM Smooth Streaming, HLS vagy MPEG-DASH-adatfolyamok titkosítja.

    d. **Csak a MPEG-DASH Widevine** dinamikusan titkosítja a Widevine DRM-Védelemmel rendelkező MPEG-DASH.
    
    e. **Csak a HLS FairPlay** dinamikusan titkosítja a HLS adatfolyam FairPlay.

2. FairPlay titkosítás engedélyezéséhez kattintson a **védelmi globális tartalombeállításait** panelen válassza **FairPlay konfigurációs**. Válassza ki a **App tanúsítvány**, és írja be a **alkalmazás titkos kulcs**.

    ![Titkosítás típusa](./media/media-services-portal-content-protection/media-services-content-protection009.png)

3. Miután elvégezte a kijelölt titkosítás, válassza ki a **alkalmaz**.

>[!NOTE] 
>Ha azt tervezi, egy AES által titkosított HLS játszanak Safari, lásd a következő blogbejegyzésben [titkosított HLS a Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

