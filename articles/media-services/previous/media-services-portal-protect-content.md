---
title: Tartalomvédelmi házirendek konfigurálása az Azure Portal használatával | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan használhatja az Azure Portaltartalom-védelmi szabályzatok konfigurálása. A cikk azt is bemutatja, hogyan engedélyezheti a dinamikus titkosítást az eszközök számára.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0c2a9612fab6c685cbc690aa9bbc12d1c7b7b746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978202"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Tartalomvédelmi házirendek konfigurálása az Azure Portal használatával

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).   > A Media Services v2-höz nem ad nak hozzá új szolgáltatásokat vagy funkciókat. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)
>

 Az Azure Media Services segítségével a tárhely, a feldolgozás és a kézbesítés révén biztosíthatja az adathordozót attól az időponttól kezdve, amikor elhagyja a számítógépet. A Media Services segítségével dinamikusan kézbesítheti a tartalmat az Advanced Encryption Standard (AES) szabványnak 128 bites titkosítási kulcsokkal. A PlayReady és/vagy a Widevine digitális jogkezelés (DRM) és az Apple FairPlay használatával is használhatja. 

A Media Services szolgáltatást nyújt a DRM-licencek és az AES egyértelmű kulcsok biztosításához a jogosult ügyfelek számára. Az Azure Portal használatával egyetlen kulcs/licenc engedélyezési szabályzatot hozhat létre minden típusú titkosításhoz.

Ez a cikk bemutatja, hogyan konfigurálhatja a tartalomvédelmi szabályzat a portál használatával. A cikk azt is bemutatja, hogyan alkalmazhat dinamikus titkosítást az eszközökre.

## <a name="start-to-configure-content-protection"></a>Tartalomvédelem konfigurálásának megkezdése
Ha a portált a Media Services-fiók használatával szeretné konfigurálni a globális tartalomvédelem konfigurálásához, kövesse az alábbi lépéseket:

1. A [portálon](https://portal.azure.com/)válassza ki a Media Services-fiókot.

1. Válassza a **Beállítások** > **tartalomvédelem lehetőséget.**

    ![Tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Kulcs/licenc engedélyezési házirendje
A Media Services többféle módon is támogatja a kulcs- vagy licenckérelmeket benyújtó felhasználók hitelesítését. Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek ezután meg kell felelnie a szabályzatnak, mielőtt a kulcsot/licencet kézbesítené. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások is.

A portál segítségével létrehozhat egy kulcs/licenc engedélyezési házirendet minden típusú titkosításhoz.

### <a name="open-authorization"></a>Nyílt engedélyezés
A nyílt korlátozás azt jelenti, hogy a rendszer mindenkinek átadja a kulcsot, aki kulcskérést tesz. Ez a korlátozás tesztcélokra hasznos lehet. 

### <a name="token-authorization"></a>Token engedélyezése
A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a Simple Web Tokens (SWT) és a JSON Web Token (JWT) formátumú jogkivonatokat támogatja. A Media Services nem biztosít STS-t. Létrehozhat egy egyéni STS-t, vagy használhatja az Azure Access Control Service-t jogkivonatok kiállításához. Az STS-t úgy kell konfigurálni, hogy hozzon létre egy jogkivonatot a megadott kulccsal, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket. Ha a jogkivonat érvényes, és a jogkivonatban szereplő jogcímek megegyeznek a kulcshoz (vagy licenchez) konfigurált jogcímek, a Media Services-kulcs kézbesítési szolgáltatása visszaadja a kért kulcsot (vagy licencet) az ügyfélnek.

A jogkivonat-korlátozott házirend konfigurálásakor meg kell adnia az elsődleges ellenőrzési kulcsot, a kibocsátót és a közönség paramétereit. Az elsődleges ellenőrzési kulcs tartalmazza azt a kulcsot, amelyhez a jogkivonat ot aláírták. A kibocsátó a biztonságos jogkivonat-szolgáltatás, amely kiadja a jogkivonatot. A közönség (más néven hatókör) a jogkivonat vagy az erőforrás szándékát írja le, amelyhez a jogkivonat engedélyezi a hozzáférést. A Media Services-kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a jogkivonatban megegyeznek-e a sablonban lévő értékekkel.

![Kulcs/licenc engedélyezési házirendje](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licencsablon
A PlayReady licencsablon beállítja a PlayReady-licencen engedélyezett funkciókat. A PlayReady licencsablonról a [Media Services PlayReady licencsablon – áttekintés című témakörben olvashat bővebben.](media-services-playready-license-template-overview.md)

### <a name="nonpersistent"></a>Nem állandó
Ha a licencet nem állandóként állítja be, az csak akkor marad a memóriában, amikor a lejátszó a licencet használja.  

![Nem állandó tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Állandó
Ha egy licencet állandóként konfigurál, az az ügyfél állandó tárolójában kerül mentésre.

![Állandó tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licencsablon
A Widevine licencsablon beállítja a Widevine-licenceken engedélyezett funkciókat.

### <a name="basic"></a>Basic
Ha az **Alap szintű**lehetőséget választja, a sablon az összes alapértelmezett értékkel együtt jön létre.

### <a name="advanced"></a>Speciális
A Widevine jogmegadási sablonról a [Widevine licencsablon áttekintése című témakörben olvashat bővebben.](media-services-widevine-license-template-overview.md)

![Speciális tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay konfiguráció
A FairPlay titkosítás engedélyezéséhez válassza a **FairPlay konfiguráció**lehetőséget. Ezután jelölje ki az **alkalmazástanúsítványt,** és adja meg az **alkalmazástitkos kulcsot.** A FairPlay konfigurációjáról és követelményeiről a [HLS-tartalom védelme apple fairplay vagy Microsoft PlayReady alkalmazással](media-services-protect-hls-with-FairPlay.md)című témakörben talál további információt.

![FairPlay konfiguráció](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Dinamikus titkosítás alkalmazása az eszközre
A dinamikus titkosítás előnyeinek kihasználásához kódolja a forrásfájlt adaptív sávszélességű MP4-fájlokba.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>A titkosítani kívánt eszköz kiválasztása
Az összes eszköz megtekintéséhez válassza a **Beállítások** > **eszközök**lehetőséget.

![Eszközök opció](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Titkosítás AES-sel vagy DRM-mel
Ha egy eszköz titkosítása lehetőséget **választja,** két lehetőség közül választhat: **AES** vagy **DRM**. 

#### <a name="aes"></a>AES
Az AES titkosítása minden streamelési protokollon engedélyezve van: sima streamelés, HLS és MPEG-DASH.

![Titkosítási konfiguráció](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>Drm
1. A **DRM**kiválasztása után különböző tartalomvédelmi házirendek (amelyeket ezen a ponton kell konfigurálni) és a streamelési protokollok készlete jelenik meg:

    a. **A PlayReady és a Widevine és az MPEG-DASH** segítségével dinamikusan titkosítja az MPEG-DASH streamet playready és Widevine DRM-ekkel.

    b. **PlayReady és Widevine MPEG-DASH + FairPlay HLS-sel** dinamikusan titkosíthatja az MPEG-DASH streamet PlayReady és Widevine DRM-ekkel. Ez a beállítás a HLS-adatfolyamokat is titkosítja a FairPlay segítségével.

    c. **PlayReady csak smooth streaming, HLS és MPEG-DASH** dinamikusan titkosítja smooth streaming, HLS és MPEG-DASH streamek PlayReady DRM.

    d. **Widevine csak MPEG-DASH** dinamikusan titkosítja az MPEG-DASH a Widevine DRM.
    
    e. **A FairPlay csak a HLS-sel** dinamikusan titkosítja a HLS-adatfolyamot a FairPlay segítségével.

1. A FairPlay titkosítás engedélyezéséhez a **Content Protection Global Settings** panelen válassza a **FairPlay konfiguráció lehetőséget.** Ezután jelölje ki az **alkalmazástanúsítványt**, és adja meg az **alkalmazástitkos kulcsot.**

    ![Titkosítás típusa](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. A titkosítási kijelölés után válassza az **Alkalmaz**lehetőséget.

>[!NOTE] 
>Ha azt tervezi, hogy játszani egy AES-titkosított HLS safari, lásd a [blogbejegyzést titkosított HLS safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

