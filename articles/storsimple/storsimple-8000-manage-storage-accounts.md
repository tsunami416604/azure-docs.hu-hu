---
title: "A StorSimple tárfiók hitelesítő adatait a Microsoft Azure StorSimple 8000 sorozat eszközeire kezelése |} Microsoft Docs"
description: "Azt ismerteti, hogyan a StorSimple Device Manager konfigurálása lap segítségével hozzáadása, szerkesztése, törlése, vagy forgassa el a biztonsági kulcsok egy tárfiókot."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 36058ad69ea670998b50cf9038741c294a5b79ab
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>A StorSimple Device Manager szolgáltatás segítségével a tárfiók hitelesítő adatainak kezelése

## <a name="overview"></a>Áttekintés

A **konfigurációs** hozhatja létre a StorSimple Device Manager szolgáltatás globális szolgáltatási paraméterek szakaszban a StorSimple Device Manager szolgáltatás a panelen. Ezek a paraméterek kapcsolódik a szolgáltatáshoz minden olyan eszközre alkalmazhatók, és tartalmazza:

* Tárfiók hitelesítő adatai
* Sávszélesség-sablonok 
* Hozzáférés-vezérlési rekordokat 

Ez az oktatóanyag ismerteti, hogyan hozzáadásához, szerkesztéséhez, vagy törölje a tárfiók hitelesítő adatainak, vagy forgassa el a biztonsági kulcsok egy tárfiókot.

 ![Tárfiók hitelesítő adatainak listája](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Tárfiókok a StorSimple eszközt a tárfiók a felhőszolgáltatóval elérésére használt hitelesítő adatokat tartalmaz. A Microsoft Azure storage-fiókok ezek a hitelesítő adatok, például a fiók nevét és az elsődleges elérési kulcsot. 

Az a **Tárfiók hitelesítő adatainak** panelen, a következő információkat tartalmazó táblázatos formában jelennek meg a számlázási előfizetés létrehozott összes storage-fiókok:

* **Név** – létrehozásakor a fiókhoz rendelt egyedi nevét.
* **Az SSL engedélyezett** – hogy az SSL protokoll engedélyezve van, és eszköz-felhő kommunikációs van a biztonságos csatornán keresztül.
* **Által használt** – a storage-fiók használatával kötetek számát.

Storage-fiókok végrehajtható kapcsolatos leggyakoribb feladatokat a következők:

* Tárfiók hozzáadása 
* A storage-fiók szerkesztése 
* Tárfiók törlése 
* Kulcs elforgatási szögét storage-fiókok 

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

A StorSimple eszköz használható storage-fiókok három típusa van.

* **Automatikusan létrehozott tárfiókok** –, a nevet javasol, a tárfióknak a típusa automatikusan előállított, a szolgáltatás első létrehozásakor. Ez a tárfiók létrehozását kapcsolatos további információkért lásd: [1. lépés: új szolgáltatás létrehozása](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md). 
* **A szolgáltatás előfizetés tárfiókjai** – ezek azok a szolgáltatás, amely ugyanahhoz az előfizetéshez társított Azure storage-fiókok. További információ arról, hogy ezen tárolásról fiókok létrehozásához szükségesek, lásd: [kapcsolatos Azure Storage-fiókok](../storage/common/storage-create-storage-account.md). 
* **A szolgáltatás előfizetésével kívül tárfiókok** – ezek azok az Azure storage-fiókok, amelyek nem a szolgáltatáshoz tartozó, és valószínűleg már létezett a szolgáltatás létrehozása előtt.

## <a name="add-a-storage-account"></a>Tárfiók hozzáadása

A storage-fiók is hozzáadhat egy egyedi nevet és a tárfiókot (a megadott felhő-szolgáltató) tartozó hozzáférési hitelesítő adatok megadásával. Lehetősége is van, a secure sockets layer (SSL) mód engedélyezése az eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni.

Egy adott felhőre szolgáltató több fiók is létrehozhat. Ne feledje azonban, a tárfiók létrehozása után nem módosítható a felhőbeli szolgáltatás szolgáltatója.

A storage-fiók mentése folyamatban van, amíg a szolgáltatás megkísérli a felhőbeli szolgáltatás szolgáltatója kommunikálni. A hitelesítő adatokat és az access anyag megadott hitelesítése most. A storage-fiók jön létre, csak akkor, ha a hitelesítés sikeres. Ha a hitelesítés nem sikerül, majd a megfelelő hibaüzenet jelenik meg.

A következő eljárásokat követve adja hozzá az Azure storage-fiók hitelesítő adatait:

* A tárolási fiók hitelesítő adatait, amely rendelkezik az Eszközkezelő szolgáltatásként az azonos Azure-előfizetés hozzáadása
* Egy Azure storage fiók hitelesítő adatait, amely kívül az Eszközkezelő szolgáltatás előfizetés hozzáadása

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Egy Azure storage fiók hitelesítő adatait, a StorSimple Device Manager szolgáltatási előfizetés kívül hozzáadása

1. Keresse meg a StorSimple eszköz kezelő szolgáltatás, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakasz. A parancs megjeleníti az összes meglévő tárfiók hitelesítő adatainak a StorSimple Device Manager szolgáltatással kapcsolatos.
3. Kattintson az **Add** (Hozzáadás) parancsra.
4. Az a **adja hozzá a tárolási fiók hitelesítő adatait** panelen tegye a következőket:
   
    1. A **előfizetés**, jelölje be **más**.
   
    2. Adja meg az Azure storage-fiók hitelesítő adatait nevét.
   
    3. Az a **tárfiók_elérési_kulcsa** szöveget adja meg az Azure storage-fiók hitelesítő adatait az elsődleges hozzáférési kulcsot. Ezt a kulcsot, keresse meg az Azure Storage szolgáltatást a tárolási fiók hitelesítő adatait, és kattintson **fiók kulcsok kezelése**. Ekkor átmásolhatja az elsődleges elérési kulcsot.
   
    4. Az SSL engedélyezéséhez kattintson a **engedélyezése** a StorSimple eszköz Manager szolgáltatás és a felhő közötti hálózati kommunikációhoz biztonságos csatorna létrehozása gombra. Kattintson a **letiltása** gomb csak akkor, ha magánfelhőben tevékenykedik.
   
    5. Kattintson az **Add** (Hozzáadás) parancsra. Értesítés jelenik meg a tárolási fiók hitelesítő adatait sikeres létrehozása után.

5. Az újonnan létrehozott tárolási fiók hitelesítő adatait a StorSimple-eszköz Manager konfigurálása szolgáltatás panel alatt jelenik meg **Tárfiók hitelesítő adatainak**.
   


## <a name="edit-a-storage-account"></a>A storage-fiók szerkesztése

Szerkesztheti, melynek használatával a kötettároló tárfiók. Ha manuálisan szerkeszti, amelyek jelenleg használatban van, az egyetlen módosítható mezője a tárfiók elérési kulcsának. Adja meg az új tárelérési kulcs, és mentse a frissített beállításokat.

#### <a name="to-edit-a-storage-account"></a>A storage-fiók szerkesztése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

    ![Tárfiók hitelesítő adatai](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Az a **Tárfiók hitelesítő adatainak** panelen, a tárfiók hitelesítő adatait, listájából válassza ki, és kattintson a szerkeszteni kívánt. 

3. Módosíthatja a **SSL engedélyezése** kijelölés. Is **több...**  majd **szinkronizálási hozzáférési kulcs elforgatása** a fiók tárelérési kulcsok. Nyissa meg a [elforgatási szögét tárfiókok kulcs](#key-rotation-of-storage-accounts) kulcs Elforgatás elvégzéséhez további információt. Kattintson a beállítások módosítása után **mentése**. 

    ![Mentse a módosított tárfiók hitelesítő adatait](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. 

    ![Módosítások megerősítése](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

A beállítások módosítása és a tárfiók mentve. 

## <a name="delete-a-storage-account"></a>Tárfiók törlése

> [!IMPORTANT]
> A tárfiók törlése csak akkor, ha nem használják a kötettároló. Ha a kötettároló tárfiók használja, először törölje a kötettároló, majd a kapcsolódó tárfiók.

#### <a name="to-delete-a-storage-account"></a>A tárfiók törlése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

2. A táblázatos listában tárfiókok mutasson a fiókot, amelyet törölni szeretne. Kattintson a jobb gombbal a helyi menü meghívni, és kattintson a **törlése**.

    ![Tárfiók hitelesítő adatainak törlése](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Amikor felszólítja a megerősítésre, kattintson **Igen** a törlés folytatásához. A táblázatos felsorolása frissíti a módosításokkal.

    ![Törlés megerősítése](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Kulcs elforgatási szögét storage-fiókok

Biztonsági okokból kulcs Elforgatás feltétele gyakran adatközpontokban. Minden Microsoft Azure-előfizetéssel társított tárfiókokat rendelkezhet. A hozzáférést ezekhez a fiókokhoz vezérli minden tárfiók az előfizetés és a hozzáférési kulcsainak listázása. 

Amikor létrehoz egy tárfiókot, a Microsoft Azure két 512 bites tárelérési kulcsokat használnak a hitelesítéshez a tárfiók elérésekor állít elő. Két tárelérési kulcsot lehetővé teszi, hogy a megszakítás nélkül a társzolgáltatás vagy való hozzáférés a kulcsok újragenerálását. A kulcs jelenleg használatban van a *elsődleges* és a biztonsági kulcs a neve a *másodlagos* kulcs. Ezek a két kulcsok egyikét kell megadni, ha a Microsoft Azure StorSimple eszköz éri el a felhő tárolási szolgáltató.

## <a name="what-is-key-rotation"></a>Mi az, hogy a kulcs Elforgatás?

Általában alkalmazások segítségével csak egyik kulcsának hozzáférhet az adatokhoz. Egy bizonyos idő után akkor az alkalmazások átválthat a második kulcs használatával. Miután az alkalmazásait, és a másodlagos kulcs váltottunk, az első kulcsát kivonása és majd hozzon létre egy új kulcsot. A két kulcs használatával, így lehetővé teszi, hogy az alkalmazások férhetnek hozzá az adatokhoz állásidő nélkül.

A tárfiók kulcsait mindig a szolgáltatás titkosított formában tárolja. Azonban ezek állítható vissza a StorSimple eszköz kezelő szolgáltatásával. A szolgáltatás kérheti le az elsődleges és másodlagos kulcsot az összes tárfiók ugyanahhoz az előfizetéshez, beleértve a tárolási szolgáltatás fiókjainak, valamint a jönnek létre, ha a StorSimple Device Manager service szolgáltatás nem alapértelmezett storage-fiókok először létre. A StorSimple Device Manager szolgáltatás mindig beszerezni ezeket a kulcsokat a klasszikus Azure portálra, és majd a titkosított módon történő tárolásához.

## <a name="rotation-workflow"></a>Elforgatás munkafolyamat

A Microsoft Azure felügyeleti generálja újra, vagy módosítsa az elsődleges vagy másodlagos kulcsot elérésével közvetlenül a tárfiók (keresztül a Microsoft Azure Storage szolgáltatás). Automatikusan a StorSimple Device Manager szolgáltatás nem látja ezt a módosítást.

Tájékoztatja a StorSimple Device Manager szolgáltatás a változás, meg fog StorSimple Device Manager szolgáltatás eléréséhez a tárfiók eléréséhez, és ezt követően szinkronizálja az elsődleges vagy másodlagos kulcsot (attól függően, melyiket megváltozott). A szolgáltatás ezután lekérdezi a legutóbbi kulcs, a kulcsokkal titkosítja, és a titkosított kulcs elküldi az eszköznek.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Kulcsok ugyanazt az előfizetést, a szolgáltatás storage-fiókok szinkronizálása 
1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.
2. A táblázatos átjáróra a listában tárfiókok, kattintson a módosítani kívánt. 

    ![kulcsok szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Kattintson a **... További** majd **szinkronizálási hozzáférési kulcs elforgatása**.   

    ![kulcsok szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. A StorSimple Device Manager szolgáltatásban kell frissíteni a kulcsot, amely korábban a Microsoft Azure Storage szolgáltatás megváltozott. Ha az elsődleges elérési kulcsot (újragenerált) módosult, válassza ki a **elsődleges** kulcs. Ha a másodlagos kulcs módosult, válassza ki a **másodlagos** kulcs. Kattintson a **szinkronizálási kulcs**.
      
      ![kulcsok szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Értesítést fog kapni a kulcsa sikeresen sycnhronized után.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>A szolgáltatás előfizetésével kívül tárfiókok kulcsok szinkronizálása
1. Az a **szolgáltatások** lapján kattintson a **konfigurálása** fülre.
2. Kattintson a **Tárfiók hozzáadása/szerkesztése**.
3. A párbeszédpanelen tegye a következőket:
   
   1. Válassza ki a tárfiók a frissíteni kívánt elérési kulcsával.
   2. Szüksége lesz a tárelérési kulcs a StorSimple Device Manager szolgáltatás frissítése. Ebben az esetben a tárelérési kulcs látható. Adja meg az új kulcsot a **Tárfiók_elérési_kulcsa** mezőbe. 
   3. Mentse a módosításokat. A tárfiók hozzáférési kulcsának most frissíteni kell.

## <a name="next-steps"></a>Következő lépések
* További információ [StorSimple biztonsági](storsimple-8000-security.md).
* További információ [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

