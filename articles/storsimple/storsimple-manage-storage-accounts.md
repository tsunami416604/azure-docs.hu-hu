---
title: "A StorSimple tárfiók kezelése |} Microsoft Docs"
description: "Azt ismerteti, hogyan a StorSimple Manager konfigurálása lap segítségével hozzáadása, szerkesztése, törlése, vagy forgassa el a biztonsági kulcsok egy tárfiókot."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 93207c40-e0eb-489e-8724-59fb94907081
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 4bf8b397d81e12bc48fe3d0ce16d5fff705cedbc
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>A StorSimple Manager szolgáltatás segítségével a tárfiók kezelése
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [a StorSimple Manager szolgáltatás segítségével a tárfiók kezelése](storsimple-8000-manage-storage-accounts.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
A **konfigurálása** lapon megadja hozhatja létre a StorSimple Manager szolgáltatás globális szolgáltatási paramétereket. Ezek a paraméterek kapcsolódik a szolgáltatáshoz minden olyan eszközre alkalmazhatók, és tartalmazza:

* Tárfiókok 
* Sávszélesség-sablonok 
* Hozzáférés-vezérlési rekordokat 

Ez az oktatóanyag azt ismerteti, hogyan használhatja a **konfigurálása** hozzáadása, szerkesztése vagy törlése a storage-fiókok lapján, vagy forgassa el a biztonsági kulcsok egy tárfiókot.

 ![Lapjának konfigurálása](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

Storage-fiókok tartalmaz a hitelesítő adatokkal, amelyek az eszközt a felhőszolgáltatóval a tárfiók eléréséhez. A Microsoft Azure storage-fiókok ezek a hitelesítő adatok, például a fiók nevét és az elsődleges elérési kulcsot. 

Az a **konfigurálása** lapon, a következő információkat tartalmazó táblázatos formában jelennek meg a számlázási előfizetés létrehozott összes storage-fiókok:

* **Név** – létrehozásakor a fiókhoz rendelt egyedi nevét.
* **Az SSL engedélyezett** – hogy az SSL protokoll engedélyezve van, és eszköz-felhő kommunikációs van a biztonságos csatornán keresztül.
* **Által használt** – a storage-fiók használatával kötetek számát.

A végrehajtható storage-fiókok kapcsolatos leggyakoribb feladatokat a **konfigurálása** lapon:

* Tárfiók hozzáadása 
* A storage-fiók szerkesztése 
* Tárfiók törlése 
* Kulcs elforgatási szögét storage-fiókok 

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai
A StorSimple eszköz használható storage-fiókok három típusa van.

* **Automatikusan létrehozott tárfiókok** –, a nevet javasol, a tárfióknak a típusa automatikusan előállított, a szolgáltatás első létrehozásakor. Ez a tárfiók létrehozását kapcsolatos további információkért lásd: [1. lépés: új szolgáltatás létrehozása](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough.md). 
* **A szolgáltatás előfizetés tárfiókjai** – ezek azok a szolgáltatás, amely ugyanahhoz az előfizetéshez társított Azure storage-fiókok. További információ arról, hogy ezen tárolásról fiókok létrehozásához szükségesek, lásd: [kapcsolatos Azure Storage-fiókok](../storage/common/storage-create-storage-account.md). 
* **A szolgáltatás előfizetésével kívül tárfiókok** – ezek azok az Azure storage-fiókok, amelyek nem a szolgáltatáshoz tartozó, és valószínűleg már létezett a szolgáltatás létrehozása előtt.

## <a name="add-a-storage-account"></a>Tárfiók hozzáadása
A storage-fiók is hozzáadhat egy egyedi nevet és a tárfiókot (a megadott felhő-szolgáltató) tartozó hozzáférési hitelesítő adatok megadásával. Lehetősége is van, a secure sockets layer (SSL) mód engedélyezése az eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni.

Egy adott felhőre szolgáltató több fiók is létrehozhat. Ne feledje azonban, a tárfiók létrehozása után nem módosítható a felhőbeli szolgáltatás szolgáltatója.

A storage-fiók mentése folyamatban van, amíg a szolgáltatás megkísérli a felhőbeli szolgáltatás szolgáltatója kommunikálni. A hitelesítő adatokat és az access anyag megadott hitelesítése most. A storage-fiók jön létre, csak akkor, ha a hitelesítés sikeres. Ha a hitelesítés nem sikerül, majd a megfelelő hibaüzenet jelenik meg.

Azure-portálon létrehozott erőforrás-kezelő tárfiókok a StorSimple használata is támogatott. Az erőforrás-kezelő tárfiókok nem fog megjelenni a legördülő listából válassza ki lehet választani a jelenik meg a kötettároló, csak a klasszikus Azure-portálon létrehozott storage-fiókok létrehozására tett kísérlet során. Erőforrás-kezelő tárfiókok kell adni, hajtsa végre az eljárást az alább ismertetett tárfiók hozzáadása.

> [!NOTE]
> A tárfiók hozzáadása eljárása alapján a StorSimple szoftververzió használ. Győződjön meg arról, hogy kövesse a helyes-e a StorSimple-verzió.
> 
> 

[!INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[!INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>A storage-fiók szerkesztése
Szerkesztheti, melynek használatával a kötettároló tárfiók. Ha manuálisan szerkeszti, amelyek jelenleg használatban van, az egyetlen módosítható mezője a tárfiók elérési kulcsának. Adja meg az új tárelérési kulcs, és mentse a frissített beállításokat.

#### <a name="to-edit-a-storage-account"></a>A storage-fiók szerkesztése
1. A szolgáltatás kezdőlapja, válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, és kattintson **konfigurálása**.
2. Kattintson a **Tárfiók hozzáadása/szerkesztése**.
3. Az a **hozzáadása/szerkesztése Tárfiókok** párbeszédpanel:
   
   1. A legördülő listában a **Tárfiókok**, válasszon ki egy meglévő fiókot, amely módosítani szeretne. Ez a szolgáltatás első létrehozásakor automatikusan létrehozott tárfiókok is lehetnek.
   2. Ha szükséges, módosíthatja a **SSL-mód engedélyezése** kijelölés.
   3. Ha szeretné, a fiók tárelérési kulcsok elforgatása. Lásd: [elforgatási szögét tárfiókok kulcs](#key-rotation-of-storage-accounts) kulcs Elforgatás áttelepítésről további információt.
   4. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) menti a beállításokat. A beállítások fog frissülni a **konfigurálása** lap. Kattintson a **mentése** újonnan frissített beállításainak mentéséhez.
      
      ![A storage-fiók szerkesztése](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)

## <a name="delete-a-storage-account"></a>Tárfiók törlése
> [!IMPORTANT]
> A tárfiók törlése csak akkor, ha nem használják a kötettároló. Ha a kötettároló tárfiók használja, először törölje a kötettároló, majd a kapcsolódó tárfiók.
> 
> 

#### <a name="to-delete-a-storage-account"></a>A tárfiók törlése
1. A StorSimple Manager szolgáltatás kezdőlapja, válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, és kattintson **konfigurálása**.
2. A táblázatos listában tárfiókok mutasson a fiókot, amelyet törölni szeretne.
3. A Törlés ikonra (**x**) jelennek meg a tárolási fiók szélsőséges jobb oldali oszlopban. Kattintson a **x** ikonra kattintva törölje a hitelesítő adatait.
4. Amikor felszólítja a megerősítésre, kattintson **Igen** a törlés folytatásához. A táblázatos felsorolása frissíti a módosításokkal.

## <a name="key-rotation-of-storage-accounts"></a>Kulcs elforgatási szögét storage-fiókok
Biztonsági okokból kulcs Elforgatás feltétele gyakran adatközpontokban. 

> [!NOTE]
> A következő kulcs Elforgatás információkat és mechanizmusnak csak a Microsoft Azure-tárfiókok vonatkoznak. Ha egy másik felhőszolgáltatóval használ, kezelheti ezt a szolgáltatót irányítópulton keresztül történő tárfiókkulcsok.
> 
> 

Minden Microsoft Azure-előfizetéssel társított tárfiókokat rendelkezhet. A hozzáférést ezekhez a fiókokhoz vezérli minden tárfiók az előfizetés és a hozzáférési kulcsainak listázása. 

Amikor létrehoz egy tárfiókot, a Microsoft Azure két 512 bites tárelérési kulcsokat használnak a hitelesítéshez a tárfiók elérésekor állít elő. Két tárelérési kulcsot lehetővé teszi, hogy a megszakítás nélkül a társzolgáltatás vagy való hozzáférés a kulcsok újragenerálását. A kulcs jelenleg használatban van a *elsődleges* és a biztonsági kulcs a neve a *másodlagos* kulcs. Ezek a két kulcsok egyikét kell megadni, ha a Microsoft Azure StorSimple eszköz éri el a felhő tárolási szolgáltató.

## <a name="what-is-key-rotation"></a>Mi az, hogy a kulcs Elforgatás?
Általában alkalmazások segítségével csak egyik kulcsának hozzáférhet az adatokhoz. Egy bizonyos idő után akkor az alkalmazások átválthat a második kulcs használatával. Miután az alkalmazásait, és a másodlagos kulcs váltottunk, az első kulcsát kivonása és majd hozzon létre egy új kulcsot. A két kulcs használatával, így lehetővé teszi, hogy az alkalmazások férhetnek hozzá az adatokhoz állásidő nélkül.

A tárfiók kulcsait mindig a szolgáltatás titkosított formában tárolja. Azonban ezek állítható vissza a StorSimple Manager szolgáltatással. A szolgáltatás kérheti le az elsődleges és másodlagos kulcsot az összes tárfiók ugyanahhoz az előfizetéshez, beleértve a tárolási szolgáltatás fiókjainak, valamint a jönnek létre, ha először a StorSimple Manager szolgáltatás alapértelmezett storage-fiókok létre. A StorSimple Manager szolgáltatás a rendszer mindig beszerezni ezeket a kulcsokat a klasszikus Azure portálra, és majd tárolja őket titkosított módon.

## <a name="rotation-workflow"></a>Elforgatás munkafolyamat
A Microsoft Azure felügyeleti generálja újra, vagy módosítsa az elsődleges vagy másodlagos kulcsot elérésével közvetlenül a tárfiók (keresztül a Microsoft Azure Storage szolgáltatás). Automatikusan a StorSimple Manager szolgáltatás nem látja ezt a módosítást.

Tájékoztatja a StorSimple Manager szolgáltatás a változás, meg fog a StorSimple Manager szolgáltatás eléréséhez a tárfiók eléréséhez, és ezt követően szinkronizálja az elsődleges vagy másodlagos kulcsot (attól függően, melyiket megváltozott). A szolgáltatás ezután lekérdezi a legutóbbi kulcs, a kulcsokkal titkosítja, és a titkosított kulcs elküldi az eszköznek.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Kulcsok ugyanazt az előfizetést, a szolgáltatás (csak Azure) storage-fiókok szinkronizálása
1. Az a **szolgáltatások** lapján kattintson a **konfigurálása** fülre.
2. Kattintson a **Tárfiók hozzáadása/szerkesztése**.
3. A párbeszédpanelen tegye a következőket:
   
   1. Válassza ki a tárfiókot, amely szeretné szinkronizálni a kulccsal. A tárfiók kulcsait titkosíthatók jelennek meg.
   2. A StorSimple Manager szolgáltatásban kell frissíteni a kulcsot, amely korábban a Microsoft Azure Storage szolgáltatás megváltozott. Ha az elsődleges elérési kulcsot (újragenerált) módosult, kattintson a **elsődleges kulcsának szinkronizálása**. Ha a másodlagos kulcs módosult, kattintson a **másodlagos kulcsának szinkronizálása**.
      
      ![kulcsok szinkronizálása](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>A szolgáltatás előfizetésével kívül tárfiókok kulcsok szinkronizálása
1. Az a **szolgáltatások** lapján kattintson a **konfigurálása** fülre.
2. Kattintson a **Tárfiók hozzáadása/szerkesztése**.
3. A párbeszédpanelen tegye a következőket:
   
   1. Válassza ki a tárfiók a frissíteni kívánt elérési kulcsával.
   2. Szüksége lesz a tárelérési kulcs a StorSimple Manager szolgáltatás frissítése. Ebben az esetben a tárelérési kulcs látható. Adja meg az új kulcsot a **Tárfiók_elérési_kulcsa**y mezőbe. 
   3. Mentse a módosításokat. A tárfiók hozzáférési kulcsának most frissíteni kell.

## <a name="next-steps"></a>Következő lépések
* További információ [StorSimple biztonsági](storsimple-security.md).
* További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).

