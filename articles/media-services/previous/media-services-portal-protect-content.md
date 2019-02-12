---
title: A content protection-szabályzatok konfigurálása az Azure portal használatával |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan a content protection-szabályzatok konfigurálása az Azure portal használatával. A cikk azt is bemutatja, hogyan engedélyezheti a dinamikus titkosítás, az eszközök.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: dc5439c98b5abc69b6cb1ba144e015426bb887ce
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996944"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>A content protection-szabályzatok konfigurálása az Azure portal használatával
 Az Azure Media Services akkor hagyja, hogy a számítógép tárolási, feldolgozási és kézbesítési kezdve a media gondoskodhat. A Media Services segítségével a 128 bites titkosítás kulcsok használatával dinamikusan és az Advanced Encryption Standard (AES) titkosított tartalmat. Is használhatja az általános titkosítás (CENC) a PlayReady és/vagy Widevine digitális jogkezelési (technológia DRM), és az Apple FairPlay használatával. 

A Media Services DRM-licencek továbbításának szolgáltatást nyújt, és a titkosítatlan kulcsokat az arra jogosult ügyfelek. Az Azure portal használatával hozzon létre egy kulcs/engedélyezési házirend minden típusú titkosítás.

Ez a cikk bemutatja, hogyan egy content protection-házirend konfigurálása a portál használatával. A cikk azt is bemutatja, hogyan alkalmazhatja a dinamikus titkosítás az eszközökhöz.

## <a name="start-to-configure-content-protection"></a>A tartalomvédelem konfigurálása megkezdődött
A portál használatával globális a tartalomvédelem konfigurálása a Media Services-fiók használatával, az alábbi lépéseket:

1. Az a [portál](https://portal.azure.com/), válassza ki a Media Services-fiókját.

1. Válassza ki **beállítások** > **Content protection szolgáltatása**.

    ![Tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Kulcs-/engedélyezési házirend
A Media Services támogatja a több különböző módot a felhasználókat, akik vagy licencelési kérelmeket. Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfél ezután meg kell felelnie a házirendet ahhoz, hogy a kulcs/licenc kézbesíthető. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások is.

A portál használatával hozzon létre egy kulcs/engedélyezési házirend minden típusú titkosítás.

### <a name="open-authorization"></a>Megnyitás engedélyezése
Nyissa meg a korlátozás azt jelenti, hogy a rendszer továbbítja a kulcsot bárki, aki kulcs kérést küld. Ez a korlátozás tesztelési célokra hasznos lehet. 

### <a name="token-authorization"></a>Engedélyezési jogkivonat
A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a simple web Tokens (SWT) és a JSON webes jogkivonat (JWT) formátumú jogkivonatokat támogatja. A Media Services az STS szolgáltatással nem biztosít. Hozzon létre egy egyéni STS, vagy probléma jogkivonatok Azure Access Control Service szolgáltatást használja. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. Ha a jogkivonat érvényes, és a jogkivonat jogcímeiben megfelelnek a kulcs (vagy engedély-), a Media Services kulcstovábbítást visszatér a kért kulcs (vagy engedély-) az ügyfél.

Amikor a jogkivonattal korlátozott szabályzatokat konfigurál, az elsődleges ellenőrzőkulcs, a kibocsátó és a célközönség paramétereket kell megadnia. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e. A kibocsátó a biztonságos jogkivonat-szolgáltatás, amely a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcstovábbítást ellenőrzi, hogy ezeket az értékeket a jogkivonat egyezik a sablonban szereplő értékeket.

![Kulcs-/engedélyezési házirend](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licencsablon
A PlayReady-licencsablon beállítása, amelyen engedélyezve van a funkciót a PlayReady-licenc. A PlayReady-engedélysablonról további részletekért tekintse meg a [a Media Services PlayReady licencsablon áttekintése](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Nonpersistent
Ha egy licenc nonpersistent, adja meg, azt tárolt memória csak, míg a Windows Media player a licencet használ.  

![A content protection nonpersistent](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Tartós
Ha állandó, adja meg a licenc, elmentette állandó tárolóban az ügyfélen.

![A content protection állandó](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licencsablon
A Widevine-licencsablon, amelyen engedélyezve van a funkciót a Widevine-licencek állítja be.

### <a name="basic"></a>Alapszintű
Ha bejelöli **alapszintű**, a sablon jön létre minden alapértelmezett értékekkel.

### <a name="advanced"></a>Extra szintű
A Widevine jogosultsági sablon kapcsolatos további információkért lásd: a [Widevine-licencsablon áttekintése](media-services-widevine-license-template-overview.md).

![A content protection speciális](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-konfiguráció
FairPlay-titkosítás engedélyezéséhez jelölje be **FairPlay-konfiguráció**. Válassza ki a **alkalmazástanúsítvány** , és adja meg a **alkalmazás titkos kulcsát**. FairPlay-konfiguráció és követelményeivel kapcsolatos további információkért lásd: [HLS-tartalmak az Apple fairplay által vagy a Microsoft PlayReady védelme](media-services-protect-hls-with-FairPlay.md).

![FairPlay-konfiguráció](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>A dinamikus titkosítás alkalmazása az eszközre
A dinamikus titkosítás kihasználásához kódolja a forrásfájlt egy adaptív sávszélességű MP4-fájlokat.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Válassza ki az eszközt, hogy titkosítani szeretné
Az összes eszköz megtekintéséhez válasszon **beállítások** > **eszközök**.

![Eszközök lehetőség](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Az AES vagy DRM titkosítása
Ha bejelöli **titkosítása** az adott eszköz számára a két választási lehetőség láthatja: **AES** vagy **DRM**. 

#### <a name="aes"></a>AES
Az AES tiszta kulcs titkosítás engedélyezve van az összes streamelési protokollhoz: Smooth Streaming, HLS és MPEG-DASH.

![Titkosítási konfiguráció](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Miután kiválasztotta **DRM**, tekintse meg a különböző tartalomvédelmi házirendeket (amely kell állítani a ennél a pontnál) és a streamelési protokollok:

    a. **PlayReady és Widevine MPEG-dash protokollal** az MPEG-DASH adatfolyamok, a PlayReady és Widevine DRMs dinamikusan titkosítja.

    b. **PlayReady és Widevine MPEG-dash PROTOKOLLAL + FairPlay HLS protokollal** az MPEG-DASH adatfolyamok, a PlayReady és Widevine DRMs dinamikusan titkosítja. Ez a beállítás is titkosítja a FairPlay HLS-adatfolyamok.

    c. **A PlayReady csak Smooth Streaming, HLS és MPEG-DASH** PlayReady DRM-Smooth Streaming, HLS és MPEG-DASH adatfolyamok dinamikusan titkosítja.

    d. **Csak Widevine MPEG-dash PROTOKOLLAL** dinamikusan titkosítja az MPEG-DASH, a Widevine DRM-mel.
    
    e. **Csak FairPlay HLS protokollal** FairPlay a HLS adatfolyamot dinamikusan titkosítja.

1. A FairPlay-titkosítás engedélyezése a **Content Protection globális beállítások** panelen válassza ki **FairPlay-konfiguráció**. Válassza ki a **alkalmazástanúsítvány**, és adja meg a **alkalmazás titkos kulcsát**.

    ![Titkosítás típusa](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Miután elvégezte a kijelölt titkosítás, válassza ki a **alkalmaz**.

>[!NOTE] 
>Ha azt tervezi, az AES-sel titkosított HLS lejátszása a Safariban, tekintse meg a következő blogbejegyzésben: [titkosított HLS a Safariban](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

