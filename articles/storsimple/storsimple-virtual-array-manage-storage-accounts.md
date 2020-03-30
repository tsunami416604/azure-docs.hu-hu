---
title: StorSimple virtual array tárfiók hitelesítő adatainak kezelése | Microsoft dokumentumok
description: A storsimple-i eszközkezelő konfigurálása lap használatával hogyan adhat hozzá, szerkeszthet, törölhet vagy forgathat el a StorSimple virtuális tömbhöz társított tárfiók-hitelesítő adatok biztonsági kulcsait.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 5cedde1e7daa49aaa7a2786c9ad8a65fb8e452f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297574"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>A StorSimple Eszközkezelő használata a StorSimple virtuális tömb tárfiók-hitelesítő adatainak kezeléséhez

## <a name="overview"></a>Áttekintés
A StorSimple virtuális tömb StorSimple Eszközkezelő **szolgáltatáspanelkonfigurációs** szakasza a StorSimple Manager szolgáltatásban létrehozható globális szolgáltatásparamétereket mutatja be. Ezek a paraméterek a szolgáltatáshoz csatlakoztatott összes eszközre alkalmazhatók, és a következőket tartalmazzák:

* Tárfiók hitelesítő adatai
* Hozzáférés-vezérlési rekordok
  
  ![Az Eszközkezelő szolgáltatás irányítópultja](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Ez az oktatóanyag bemutatja, hogyan adhat hozzá, szerkeszthet vagy törölhet tárfiók hitelesítő adatait a StorSimple virtuális tömbhöz. Az oktatóanyagban szereplő információk csak a StorSimple virtuális tömbre vonatkoznak. A 8000-es sorozattárfiókok kezeléséről [a Tárfiók kezelése a StorSimple Manager szolgáltatás használata című](storsimple-manage-storage-accounts.md)témakörben talál további információt.

A tárfiók hitelesítő adatai tartalmazzák azokat a hitelesítő adatokat, amelyeket az eszköz a felhőszolgáltatóval való tárfiók eléréséhez használ. A Microsoft Azure storage-fiókok, ezek hitelesítő adatok, például a fiók nevét és az elsődleges hozzáférési kulcs.

A **Tárfiók hitelesítő adatai** panelen a számlázási előfizetéshez létrehozott összes tárfiók-hitelesítő adat a következő információkat tartalmazó táblázatos formátumban jelenik meg:

* **Név** – a fiókhoz a létrehozáskor hozzárendelt egyedi név.
* **SSL engedélyezve** – Függetlenül attól, hogy a TLS engedélyezve van-e, és az eszközről a felhőbe irányuló kommunikáció a biztonságos csatornán keresztül történik.
  
  ![Konfigurációs szakasz](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

A tárfiók hitelesítő adataival kapcsolatos leggyakoribb feladatok, amelyek a **Tárfiók hitelesítő adatai** panelen hajthatók végre:

* Tárfiók hitelesítő adatainak hozzáadása
* Tárfiók hitelesítő adatainak szerkesztése
* Tárfiók hitelesítő adatainak törlése

## <a name="types-of-storage-account-credentials"></a>A tárfiók hitelesítő adatainak típusai
A StorSimple-eszközzel háromféle tárfiók-hitelesítő adatok használhatók.

* **Automatikusan létrehozott tárfiók hitelesítő adatok** – Ahogy a neve is sugallja, az ilyen típusú tárfiók hitelesítő adatok automatikusan létrejön, amikor a szolgáltatás először jön létre. Ha többet szeretne tudni atárfiók hitelesítő adatainak létrehozásáról, olvassa el az [Új szolgáltatás létrehozása című témakört.](storsimple-virtual-array-manage-service.md#create-a-service)
* **tárfiók hitelesítő adatait a szolgáltatás-előfizetés** – Ezek az Azure storage-fiók hitelesítő adatait, amelyek ugyanahhoz az előfizetéshez kapcsolódnak, mint a szolgáltatás. Ha többet szeretne tudni arról, hogyan jönnek létre ezek a tárfiók-hitelesítő adatok, olvassa el [az Azure Storage-fiókok – ismertet.](../storage/common/storage-create-storage-account.md)
* **tárfiók hitelesítő adatok a szolgáltatás-előfizetésen kívül** – Ezek az Azure storage-fiók hitelesítő adatait, amelyek nincsenek társítva a szolgáltatáshoz, és valószínűleg létezett a szolgáltatás létrehozása előtt.

## <a name="add-a-storage-account-credential"></a>Tárfiók hitelesítő adatainak hozzáadása
A StorSimple Eszközkezelő szolgáltatáskonfigurációjához hozzáadhat egy tárfiók hitelesítő adatait, ha egyedi rövid nevet és hozzáférési hitelesítő adatokat ad meg, amelyek a tárfiókhoz kapcsolódnak. Lehetősége van arra is, hogy a Transport Layer Security (TLS) (Security Layer, korábban Secure Sockets Layer , SSL) mód biztonságos csatornát hozzon létre az eszköz és a felhő közötti hálózati kommunikációhoz.

Egy adott felhőszolgáltatóhoz több fiókot is létrehozhat. A tárfiók hitelesítő adatainak mentése közben a szolgáltatás megpróbál kommunikálni a felhőszolgáltatóval. A hitelesítő adatok és a megadott hozzáférési anyagok jelenleg hitelesítve vannak. A tárfiók hitelesítő adatai csak akkor jönnek létre, ha a hitelesítés sikeres. Ha a hitelesítés sikertelen, megjelenik a megfelelő hibaüzenet.

Az alábbi eljárásokkal adja hozzá az Azure storage-fiók hitelesítő adatait:

* Olyan tárfiók hitelesítő adatainak hozzáadása, amelyek ugyanazt az Azure-előfizetést rendelkezik, mint az Eszközkezelő szolgáltatás
* Az Eszközkezelő szolgáltatás-előfizetésen kívüli Azure storage-fiók hitelesítő adatainak hozzáadása

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Olyan tárfiók hitelesítő adatainak hozzáadása, amelyek ugyanazt az Azure-előfizetést rendelkezik, mint az Eszközkezelő szolgáltatás

1. Nyissa meg az Eszközkezelő szolgáltatást, jelölje ki és kattintson rá duplán. Ez megnyitja az **Áttekintő** panelt.
2. Válassza a **Tárfiók hitelesítő adatai a** Konfiguráció szakaszban válassza ki a **tárfiók** hitelesítő adatait.
3. Kattintson a **Hozzáadás** gombra.
4. A **Tárfiók hozzáadása** panelen tegye a következőket:
   
    1. **Előfizetés esetén**válassza az **Aktuális**lehetőséget.
    2. Adja meg az Azure-tárfiók nevét.
    3. Az **Engedélyezés** lehetőséget választva biztonságos csatornát hozhat létre a StorSimple-eszköz és a felhő közötti hálózati kommunikációhoz. Válassza **a Letiltás csak** akkor, ha magánfelhőben működik.
    4. Kattintson a **Hozzáadás** gombra. Értesítést kap a tárfiók sikeres létrehozása után.<br></br>
   
        ![Meglévő tárfiók hitelesítő adatainak hozzáadása](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Az Eszközkezelő szolgáltatás-előfizetésen kívüli Azure storage-fiók hitelesítő adatainak hozzáadása

1. Nyissa meg az Eszközkezelő szolgáltatást, jelölje ki és kattintson rá duplán. Ez megnyitja az **Áttekintő** panelt.
2. Válassza a **Tárfiók hitelesítő adatai a** Konfiguráció szakaszban válassza ki a **tárfiók** hitelesítő adatait. Ez felsorolja a StorSimple Eszközkezelő szolgáltatáshoz társított meglévő tárfiók-hitelesítő adatokat.
3. Kattintson a **Hozzáadás** gombra.
4. A **Tárfiók hozzáadása** panelen tegye a következőket:
   
    1. **Előfizetés esetén**válassza az **Egyéb**lehetőséget.
   
    2. Adja meg az Azure storage-fiók hitelesítő adatainak nevét.
   
    3. A **Storage-fiók hozzáférési kulcs** szövegmezőjében adja meg az Azure storage-fiók hitelesítő adatainak elsődleges hozzáférési kulcsát. A kulcs beszerezéséhez nyissa meg az Azure Storage szolgáltatást, válassza ki a tárfiók hitelesítő adatait, és kattintson **a Fiókkulcsok kezelése gombra.** Most már másolhatja az elsődleges hozzáférési kulcsot.
   
    4. A TLS engedélyezéséhez kattintson az **Engedélyezés** gombra, és hozzon létre egy biztonságos csatornát a StorSimple Eszközkezelő szolgáltatás és a felhő közötti hálózati kommunikációhoz. Csak akkor kattintson a **Letiltás** gombra, ha magánfelhőben működik.
   
    5. Kattintson a **Hozzáadás** gombra. Értesítést kap a tárfiók hitelesítő adatainak sikeres létrehozása után.

5. Az újonnan létrehozott tárfiók hitelesítő adatai a **Tárfiók hitelesítő adatai**csoport StorSimple Konfigurálása eszközkezelő szolgáltatáspanelen jelennek meg.
   
    ![Tárfiók hitelesítő adatainak hozzáadása az Eszközkezelő szolgáltatás-előfizetésen kívül](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Tárfiók hitelesítő adatainak szerkesztése
Az eszköz által használt tárfiók hitelesítő adatait szerkesztheti. Ha szerkeszti a tárfiók hitelesítő adatait, amely jelenleg használatban van, a módosítható mezők a hozzáférési kulcs és a TLS mód a tárfiók hitelesítő adataihoz. Megadhatja az új tárolóelérési kulcsot, vagy módosíthatja az **SSL-mód engedélyezése** beállítást, és mentheti a frissített beállításokat.

#### <a name="to-edit-a-storage-account-credential"></a>Tárfiók hitelesítő adatainak szerkesztése
1. Nyissa meg az Eszközkezelő szolgáltatást, jelölje ki és kattintson rá duplán. Ez megnyitja az **Áttekintő** panelt.
2. Válassza a **Tárfiók hitelesítő adatai a** Konfiguráció szakaszban válassza ki a **tárfiók** hitelesítő adatait. Ez felsorolja a StorSimple Eszközkezelő szolgáltatáshoz társított meglévő tárfiók-hitelesítő adatokat.
3. A tárfiók hitelesítő adatainak táblázatos listájában jelölje ki és kattintson duplán a módosítani kívánt fiókra.
4. A tárfiók hitelesítő **adatainak tulajdonságai** panelen tegye a következőket:
   
   1. Szükség esetén módosíthatja az **SSL-mód engedélyezése** beállítást.
   2. Dönthet úgy, hogy újra generálja a tárfiók hitelesítő adatok hozzáférési kulcsokat. További információt a [Tárfiók hozzáférési kulcsainak kezelése című témakörben talál.](../storage/common/storage-account-keys-manage.md) Adja meg az új tárfiók hitelesítő kulcsát. Egy Azure storage-fiók esetén ez az elsődleges hozzáférési kulcs.
   3. A beállítások mentéséhez kattintson a **Tulajdonságok** panel tetején található **Mentés** gombra. A beállítások frissülnek a **tárfiók hitelesítő adatait** panelen.
      
      ![Tárfiók hitelesítő adatainak szerkesztése](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Tárfiók hitelesítő adatainak törlése
> [!IMPORTANT]
> A tárfiók hitelesítő adatait csak akkor törölheti, ha nincs használatban. Ha egy tárfiók hitelesítő adatait használja, értesítést kap.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Tárfiók hitelesítő adatainak törlése
1. Nyissa meg az Eszközkezelő szolgáltatást, jelölje ki és kattintson rá duplán. Ez megnyitja az **Áttekintő** panelt.
2. Válassza a **Tárfiók hitelesítő adatai a** Konfiguráció szakaszban válassza ki a **tárfiók** hitelesítő adatait. Ez felsorolja a StorSimple Eszközkezelő szolgáltatáshoz társított meglévő tárfiók-hitelesítő adatokat.
3. A tárfiók hitelesítő adatainak táblázatos listájában jelölje ki és kattintson duplán a törölni kívánt fiókra.
4. A tárfiók hitelesítő **adatainak tulajdonságai** panelen tegye a következőket:
   
   1. A hitelesítő adatok törléséhez kattintson a **Törlés** gombra.
   2. Amikor megerősítést kér, a törlés folytatásához kattintson az **Igen** gombra. A táblázatos lista frissül, hogy tükrözze a változásokat.
      
      ![Tárfiók hitelesítő adatainak törlése](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Tárfiók hitelesítő kulcsainak szinkronizálása
Biztonsági okokból a kulcselforgatás gyakran követelmény az adatközpontokban. A Microsoft Azure-rendszergazda újragenerálhatja vagy módosíthatja az elsődleges vagy másodlagos kulcsot a tárfiók hitelesítő adatainak közvetlen elérésével (a Microsoft Azure Storage szolgáltatáson keresztül). A StorSimple Eszközkezelő szolgáltatás nem látja automatikusan ezt a módosítást.

A StorSimple Eszközkezelő szolgáltatás változásról való tájékoztatásához hozzá kell férned a StorSimple Eszközkezelő szolgáltatáshoz, el kell érnie a tárfiók hitelesítő adatait, majd szinkronizálnia kell az elsődleges vagy másodlagos kulcsot (attól függően, hogy melyik módosult). A szolgáltatás ezután leveszi a legújabb kulcsot, titkosítja a kulcsokat, és elküldi a titkosított kulcsot az eszköznek.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>A tárfiók hitelesítő adatainak szinkronizálása ugyanabban az előfizetésben, mint a szolgáltatás (csak Azure)
1. A szolgáltatás leszállópaneljén válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **Konfiguráció** csoportban kattintson a **Tárfiók hitelesítő adatai**elemre.
2. A **Tárfiók hitelesítő adatai** panelen a storage-fiók hitelesítő adatai nak listájában válassza ki a tárfiók hitelesítő adatait, amelynek kulcsait szinkronizálni szeretné.
3. A kijelölt tárfiók hitelesítő adatainak **Tulajdonságok** paneljén tegye a következőket:
   
    1. Kattintson **az Egyebek**, majd a **Hozzáférés kulcs szinkronizálása parancsra.**
   
    2. Amikor megerősítést kér, kattintson a **Szinkronizálás gombra** a szinkronizálás befejezéséhez.
    
4. A StorSimple Eszközkezelő szolgáltatásban frissítenie kell a microsoft Azure Storage szolgáltatásban korábban módosított kulcsot. A **Tárfiók kulcsának szinkronizálása** panelen, ha az elsődleges hozzáférési kulcs módosult (újragenerálódott), kattintson az Elemi, majd a **Kulcs szinkronizálása**gombra. Ha a másodlagos kulcs módosult, kattintson a **Másodlagos**gombra, majd a **Kulcs szinkronizálása**parancsra.
   
    ![Hozzáférési kulcs szinkronizálása](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>További lépések
* További információ [a StorSimple virtuális tömb felügyeletéről.](storsimple-ova-web-ui-admin.md)

