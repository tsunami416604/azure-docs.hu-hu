---
title: Tárfiók hitelesítő adatainak kezelése, StorSimple 8000 sorozatú eszköz
description: A storSimple eszközkezelő konfigurálása lap használatával hogyan adhat hozzá, szerkeszthet, törölhet vagy forgathat el egy tárfiók biztonsági kulcsait.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 65aa83731be97b59a36a5baf60ea308690a0dcf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297748"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>A StorSimple Eszközkezelő szolgáltatás használata a tárfiók hitelesítő adatainak kezeléséhez

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás panel **konfigurációja** szakasza bemutatja a StorSimple Eszközkezelő szolgáltatásban létrehozható összes globális szolgáltatásparamétert. Ezek a paraméterek a szolgáltatáshoz csatlakoztatott összes eszközre alkalmazhatók, és a következőket tartalmazzák:

* Tárfiók hitelesítő adatai
* Sávszélesség-sablonok 
* Hozzáférés-vezérlési rekordok 

Ez az oktatóanyag bemutatja, hogyan adhat hozzá, szerkeszthet vagy törölhet tárfiók hitelesítő adatait, illetve hogyan forgathatja el a tárfiók biztonsági kulcsait.

 ![Tárfiók hitelesítő adatainak listája](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

A tárfiókok tartalmazzák a StorSimple-eszköz által a felhőszolgáltatóval való tárfiók eléréséhez használt hitelesítő adatokat. A Microsoft Azure storage-fiókok, ezek hitelesítő adatok, például a fiók nevét és az elsődleges hozzáférési kulcs. 

A **Tárfiók hitelesítő adatai** panelen a számlázási előfizetéshez létrehozott összes tárfiók a következő információkat tartalmazó táblázatos formátumban jelenik meg:

* **Név** – a fiókhoz a létrehozáskor hozzárendelt egyedi név.
* **SSL engedélyezve** – Függetlenül attól, hogy a TLS engedélyezve van-e, és az eszközről a felhőbe irányuló kommunikáció a biztonságos csatornán keresztül történik.
* **Által használt** – A tárfiókot használó kötetek száma.

A tárfiókokhoz kapcsolódó leggyakoribb feladatok a következők:

* Tárfiók hozzáadása 
* Tárfiók szerkesztése 
* Tárfiók törlése 
* A tárfiókok kulcsos elforgatása 

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

A StorSimple-eszközzel három típusú tárfiók használható.

* **Automatikusan létrehozott tárfiókok** – Ahogy a neve is sugallja, az ilyen típusú tárfiók automatikusan jön létre, amikor a szolgáltatás először jön létre. A tárfiók létrehozásáról az [1.](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) [Deploy your on-premises StorSimple device](storsimple-8000-deployment-walkthrough-u2.md) 
* **A szolgáltatás-előfizetésben lévő tárfiókok** – Ezek azok az Azure storage-fiókok, amelyek ugyanahhoz az előfizetéshez vannak társítva, mint a szolgáltatás. Ha többet szeretne tudni arról, hogyan jönnek létre ezek a tárfiókok, olvassa el [az Azure Storage-fiókok – ismertet.](../storage/common/storage-create-storage-account.md) 
* **A szolgáltatás-előfizetésen kívüli tárfiókok** – Ezek azok az Azure storage-fiókok, amelyek nincsenek társítva a szolgáltatáshoz, és valószínűleg a szolgáltatás létrehozása előtt léteztek.

## <a name="add-a-storage-account"></a>Tárfiók hozzáadása

A tárfiókot egyedi rövid név és hozzáférési hitelesítő adatok megadásával, amelyek kapcsolódnak a tárfiókhoz (a megadott felhőszolgáltatóval). Lehetősége van arra is, hogy a Transport Layer Security (TLS) (Security Layer, korábban Secure Sockets Layer , SSL) mód biztonságos csatornát hozzon létre az eszköz és a felhő közötti hálózati kommunikációhoz.

Egy adott felhőszolgáltatóhoz több fiókot is létrehozhat. Ne feledje azonban, hogy a tárfiók létrehozása után nem módosíthatja a felhőszolgáltatót.

A tárfiók mentése közben a szolgáltatás megpróbál kommunikálni a felhőszolgáltatóval. A hitelesítő adatok és a megadott hozzáférési anyag jelenleg hitelesítve lesz. A tárfiók csak akkor jön létre, ha a hitelesítés sikeres. Ha a hitelesítés sikertelen, akkor egy megfelelő hibaüzenet jelenik meg.

Az alábbi eljárásokkal adja hozzá az Azure storage-fiók hitelesítő adatait:

* Olyan tárfiók hitelesítő adatainak hozzáadása, amelyek ugyanazt az Azure-előfizetést rendelkezik, mint az Eszközkezelő szolgáltatás
* Az Eszközkezelő szolgáltatás-előfizetésen kívüli Azure storage-fiók hitelesítő adatainak hozzáadása

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Azure-tárfiók hitelesítő adatainak hozzáadása a StorSimple Eszközkezelő szolgáltatás-előfizetésén kívül

1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és kattintson rá duplán. Ez megnyitja az **Áttekintő** panelt.
2. Válassza a **Tárfiók hitelesítő adatai a** Konfiguráció szakaszban válassza ki a **tárfiók** hitelesítő adatait. Ez felsorolja a StorSimple Eszközkezelő szolgáltatáshoz társított meglévő tárfiók-hitelesítő adatokat.
3. Kattintson a **Hozzáadás** gombra.
4. A **Tárfiók hitelesítő adatainak hozzáadása** panelen tegye a következőket:
   
    1. **Előfizetés esetén**válassza az **Egyéb**lehetőséget.
   
    2. Adja meg az Azure storage-fiók hitelesítő adatainak nevét.
   
    3. A **Storage-fiók hozzáférési kulcs** szövegmezőjében adja meg az Azure storage-fiók hitelesítő adatainak elsődleges hozzáférési kulcsát. A kulcs beszerezéséhez nyissa meg az Azure Storage szolgáltatást, válassza ki a tárfiók hitelesítő adatait, és kattintson **a Fiókkulcsok kezelése gombra.** Most már másolhatja az elsődleges hozzáférési kulcsot.
   
    4. A TLS engedélyezéséhez kattintson az **Engedélyezés** gombra, és hozzon létre egy biztonságos csatornát a StorSimple Eszközkezelő szolgáltatás és a felhő közötti hálózati kommunikációhoz. Csak akkor kattintson a **Letiltás** gombra, ha magánfelhőben működik.
   
    5. Kattintson a **Hozzáadás** gombra. Értesítést kap a tárfiók hitelesítő adatainak sikeres létrehozása után.

5. Az újonnan létrehozott tárfiók hitelesítő adatai a **Tárfiók hitelesítő adatai**csoport StorSimple Konfigurálása eszközkezelő szolgáltatáspanelen jelennek meg.
   


## <a name="edit-a-storage-account"></a>Tárfiók szerkesztése

A kötettároló által használt tárfiókot szerkesztheti. Ha jelenleg használatban lévő tárfiókot szerkeszt, az egyetlen módosítható mező a tárfiók hozzáférési kulcsa. Megadhatja az új tárolóelérési kulcsot, és mentheti a frissített beállításokat.

#### <a name="to-edit-a-storage-account"></a>Tárfiók szerkesztése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

    ![Tárfiók hitelesítő adatai](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. A **Tárfiók hitelesítő adatai** panelen a tárfiók hitelesítő adatainak listájából válassza ki, és kattintson a szerkesztni kívánt elemre. 

3. Az **SSL engedélyezése** beállítás módosítható. Az **Egyebek** gombra is kattinthat, majd a **Hozzáférés szinkronizálása kulcs kiválasztásával elforgathatja** a tárfiók hozzáférési kulcsait. A kulcselforgatás végrehajtásával kapcsolatos további információkért látogasson el [a tárfiókok kulcselforgatása](#key-rotation-of-storage-accounts) című oldalra. A beállítások módosítása után kattintson a **Mentés gombra.** 

    ![Szerkesztett tárfiók hitelesítő adatainak mentése](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. 

    ![Módosítások megerősítése](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

A rendszer frissíti és menti a beállításokat a tárfiókhoz. 

## <a name="delete-a-storage-account"></a>Tárfiók törlése

> [!IMPORTANT]
> A tárfiókot csak akkor törölheti, ha azt nem használja egy kötettároló. Ha egy kötettároló egy tárfiókot használ, először törölje a kötettárolót, majd törölje a társított tárfiókot.

#### <a name="to-delete-a-storage-account"></a>Tárfiók törlése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

2. A tárfiókok táblázatos listájában mutasson a törölni kívánt fiókra. Kattintson a jobb gombbal a helyi menü meghívásához, majd kattintson a **Törlés parancsra.**

    ![Tárfiók hitelesítő adatainak törlése](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Amikor megerősítést kér, a törlés folytatásához kattintson az **Igen** gombra. A táblázatos lista frissül, hogy tükrözze a változásokat.

    ![Törlés megerősítése](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>A tárfiókok kulcsos elforgatása

Biztonsági okokból a kulcselforgatás gyakran követelmény az adatközpontokban. Minden Microsoft Azure-előfizetés rendelkezhet egy vagy több társított tárfiókkal. Ezekhez a fiókokhoz való hozzáférést az egyes tárfiókok előfizetési és hozzáférési kulcsai szabályozzák. 

Amikor létrehoz egy tárfiókot, a Microsoft Azure két 512 bites tárelérési kulcsot hoz létre, amelyek a tárfiók elérésekor hitelesítésre szolgálnak. A két tároló-hozzáférési kulcs lehetővé teszi a kulcsok újragenerálását a tárolási szolgáltatás vagy a szolgáltatáshoz való hozzáférés megszakítása nélkül. A jelenleg használt kulcs az *elsődleges* kulcs, és a biztonsági másolat kulcs *a* másodlagos kulcs. A két kulcs egyikét meg kell adni, amikor a Microsoft Azure StorSimple eszköz hozzáfér a felhőalapú tárolási szolgáltatóhoz.

## <a name="what-is-key-rotation"></a>Mi a kulcsváltás?

Az alkalmazások általában csak az egyik kulcsot használják az adatok eléréséhez. Egy bizonyos idő elteltével az alkalmazások átválthatnak a második billentyű használatára. Miután átváltotta az alkalmazásokat a másodlagos kulcsra, kivonhatja az első kulcsot, majd létrehozhat egy új kulcsot. A két kulcs így lehetővé teszi az alkalmazások számára az adatokhoz való hozzáférést anélkül, hogy bármilyen állásidő.

A tárfiók kulcsai mindig titkosított formában tárolódnak a szolgáltatásban. Ezek azonban alaphelyzetbe állíthatók a StorSimple Eszközkezelő szolgáltatáson keresztül. A szolgáltatás letudja kérni az elsődleges kulcsot és a másodlagos kulcsot az azonos előfizetésben lévő összes tárfiókhoz, beleértve a Storage szolgáltatásban létrehozott fiókokat, valamint a StorSimple Eszközkezelő szolgáltatás első szolgáltatása kor létrehozott alapértelmezett tárfiókokat Létrehozott. A StorSimple Eszközkezelő szolgáltatás mindig lefogja ezeket a kulcsokat az Azure klasszikus portálról, majd titkosított módon tárolja őket.

## <a name="rotation-workflow"></a>Rotációs munkafolyamat

A Microsoft Azure-rendszergazda újragenerálhatja vagy módosíthatja az elsődleges vagy másodlagos kulcsot a tárfiók közvetlen elérésével (a Microsoft Azure Storage szolgáltatáson keresztül). A StorSimple Eszközkezelő szolgáltatás nem látja automatikusan ezt a módosítást.

A StorSimple Eszközkezelő szolgáltatás változásról való tájékoztatásához hozzá kell férned a StorSimple Eszközkezelő szolgáltatáshoz, el kell érnie a tárfiókot, majd szinkronizálnia kell az elsődleges vagy másodlagos kulcsot (attól függően, hogy melyik et módosították). A szolgáltatás ezután leveszi a legújabb kulcsot, titkosítja a kulcsokat, és elküldi a titkosított kulcsot az eszköznek.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>A szolgáltatással azonos előfizetésben lévő tárfiókok kulcsainak szinkronizálása 
1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.
2. A tárfiókok táblázatos listájában kattintson a módosítani kívánt elemre. 

    ![billentyűk szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Kattintson **a ... Egyezik,** majd válassza **a Hozzáférés szinkronizálása gombot az elforgatáshoz.**   

    ![billentyűk szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. A StorSimple Eszközkezelő szolgáltatásban frissítenie kell a microsoft Azure Storage szolgáltatásban korábban módosított kulcsot. Ha az elsődleges hozzáférési kulcs módosult (újragenerálva), válassza az **elsődleges** kulcsot. Ha a másodlagos kulcs megváltozott, válassza a **másodlagos** kulcsot. Kattintson **a Szinkronizálás gombra.**
      
      ![billentyűk szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

A kulcs sikeres szinkronizálása után értesítést kap.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>A szolgáltatás-előfizetésen kívüli tárfiókok kulcsainak szinkronizálása
1. A **Szolgáltatások** lapon kattintson a **Konfigurálás** fülre.
2. Kattintson **a Tárfiókok hozzáadása/szerkesztése gombra.**
3. A párbeszédpanelen tegye a következőket:
   
   1. Válassza ki a frissíteni kívánt hozzáférési kulccsal rendelkező tárfiókot.
   2. Frissítenie kell a tárolási hozzáférési kulcsot a StorSimple Eszközkezelő szolgáltatásban. Ebben az esetben láthatja a tárolási hozzáférési kulcsot. Írja be az új kulcsot a **Tárfiók-hozzáférési kulcs** mezőbe. 
   3. Mentse a módosításokat. A tárfiók hozzáférési kulcsát most frissíteni kell.

## <a name="next-steps"></a>További lépések
* További információ a [StorSimple biztonságáról.](storsimple-8000-security.md)
* További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)

