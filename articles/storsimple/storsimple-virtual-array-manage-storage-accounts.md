---
title: "A StorSimple virtuális tömb tárfiók hitelesítő adatainak kezelése |} Microsoft Docs"
description: "Ismerteti, hogyan használhatja a StorSimple Device Manager konfigurálás lapról való hozzáadása, szerkesztése, törlése vagy a tárfiók hitelesítő adatainak a StorSimple virtuális tömb társított biztonsági kulcsok elforgatása."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: a4ce2d329d0e1399cffaf886adf2b95e34b9cd7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>StorSimple az Eszközkezelő a StorSimple virtuális tömb tárfiók hitelesítő adatainak kezelése

## <a name="overview"></a>Áttekintés
A **konfigurációs** a StorSimple Device Manager szolgáltatás panelre a StorSimple virtuális tömb szakasza bemutatja a globális szolgáltatás paraméterei, amely a StorSimple Manager szolgáltatás hozhatók létre. Ezek a paraméterek kapcsolódik a szolgáltatáshoz minden olyan eszközre alkalmazhatók, és tartalmazza:

* Tárfiók hitelesítő adatai
* Hozzáférés-vezérlési rekordokat
  
  ![Eszközkezelő szolgáltatás irányítópult](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Ez az oktatóanyag azt ismerteti, hogyan hozzáadásához, szerkesztéséhez vagy törléséhez a tárfiók hitelesítő adatainak a StorSimple virtuális tömbhöz. Ebben az oktatóanyagban szereplő információk csak a StorSimple virtuális tömb vonatkozik. 8000 sorozat storage-fiókok kezeléséhez további információkért lásd: [a StorSimple Manager szolgáltatás segítségével a tárfiók kezelése](storsimple-manage-storage-accounts.md).

Tárfiók hitelesítő adatait az eszköz a tárfiók a felhőszolgáltatóval elérésére használt hitelesítő adatokat tartalmaz. A Microsoft Azure storage-fiókok ezek a hitelesítő adatok, például a fiók nevét és az elsődleges elérési kulcsot.

Az a **Tárfiók hitelesítő adatainak** panel, az összes tárfiók hitelesítő adatainak a számlázási előfizetés a következő információkat tartalmazó táblázatos formában jelennek meg a létrehozott:

* **Név** – létrehozásakor a fiókhoz rendelt egyedi nevét.
* **Az SSL engedélyezett** – hogy az SSL protokoll engedélyezve van, és eszköz-felhő kommunikációs van a biztonságos csatornán keresztül.
  
  ![A konfigurációs szakasz](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Tárfiók hitelesítő adatait, amely a végrehajtható kapcsolatos leggyakoribb feladatokat a **Tárfiók hitelesítő adatainak** panel vannak:

* Tárfiók hitelesítő adatainak hozzáadása
* A tárolási fiók hitelesítő adatok szerkesztése
* Törölje a tárolási fiók hitelesítő adatait

## <a name="types-of-storage-account-credentials"></a>Tárfiók hitelesítő adatainak típusa
Tárfiók hitelesítő adatait, amely a StorSimple eszköz használható három típusa van.

* **Automatikusan létrehozott tárfiók hitelesítő adatainak** –, a nevet javasol, a tárolási fiók hitelesítő adatait az ilyen típusú automatikusan előállított, a szolgáltatás első létrehozásakor. Hogyan jön létre a tárolási fiók hitelesítő adatait kapcsolatos további információkért lásd: [hozzon létre egy új](storsimple-virtual-array-manage-service.md#create-a-service).
* **tárfiók hitelesítő adatait a szolgáltatás az előfizetéshez** – ezek azok a szolgáltatás, amely ugyanahhoz az előfizetéshez társított Azure storage fiók hitelesítő adatait. További információ arról, hogy ezen tárolásról létrejönnek a fiók hitelesítő adatait, tekintse meg a [kapcsolatos Azure Storage-fiókok](../storage/common/storage-create-storage-account.md).
* **tárfiók hitelesítő adatait a szolgáltatás előfizetésével kívül** – ezek azok a az Azure storage fiók hitelesítő adatait, amely nem a szolgáltatáshoz tartozó, és valószínűleg már létezett a szolgáltatás létrehozása előtt.

## <a name="add-a-storage-account-credential"></a>Tárfiók hitelesítő adatainak hozzáadása
Adhat hozzá a tárolási fiók hitelesítő adatait a StorSimple Device Manager konfigurációjának egy egyedi nevet és a storage-fiókjához tartozó hozzáférési hitelesítő adatok megadásával. Lehetősége is van, a secure sockets layer (SSL) mód engedélyezése az eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni.

Egy adott felhőre szolgáltató több fiók is létrehozhat. A tárolási fiók hitelesítő adatait mentése folyamatban van, amíg a szolgáltatás megkísérli a felhőbeli szolgáltatás szolgáltatója kommunikálni. A hitelesítő adatokat és az access anyag megadott jelenleg hitelesítése. A tárolási fiók hitelesítő adatait csak akkor, ha a hitelesítés sikeres jön létre. Ha a hitelesítés nem sikerül, majd megfelelő hibaüzenet jelenik meg.

A következő eljárásokat követve adja hozzá az Azure storage-fiók hitelesítő adatait:

* A tárolási fiók hitelesítő adatait, amely rendelkezik az Eszközkezelő szolgáltatásként az azonos Azure-előfizetés hozzáadása
* Egy Azure storage fiók hitelesítő adatait, amely kívül az Eszközkezelő szolgáltatás előfizetés hozzáadása

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>A tárolási fiók hitelesítő adatait, amely rendelkezik az Eszközkezelő szolgáltatásként az azonos Azure-előfizetés hozzáadása

1. Keresse meg az Eszközkezelő szolgáltatás, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakasz.
3. Kattintson az **Add** (Hozzáadás) parancsra.
4. Az a **tárfiók hozzáadása** panelen tegye a következőket:
   
    1. A **előfizetés**, jelölje be **aktuális**.
    2. Adja meg az Azure storage-fiók nevét.
    3. Válassza ki **engedélyezése** a StorSimple eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni. Válassza ki **letiltása** csak akkor, ha magánfelhőben tevékenykedik.
    4. Kattintson az **Add** (Hozzáadás) parancsra. Értesítés jelenik meg a tárfiók sikeres létrehozása után.<br></br>
   
        ![Adja hozzá egy meglévő storage-fiók hitelesítő adatait](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Egy Azure storage fiók hitelesítő adatait, amely kívül az Eszközkezelő szolgáltatás előfizetés hozzáadása

1. Keresse meg az Eszközkezelő szolgáltatás, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakasz. A parancs megjeleníti az összes meglévő tárfiók hitelesítő adatainak a StorSimple Device Manager szolgáltatással kapcsolatos.
3. Kattintson az **Add** (Hozzáadás) parancsra.
4. Az a **tárfiók hozzáadása** panelen tegye a következőket:
   
    1. A **előfizetés**, jelölje be **más**.
   
    2. Adja meg az Azure storage-fiók hitelesítő adatait nevét.
   
    3. Az a **tárfiók_elérési_kulcsa** szöveget adja meg az Azure storage-fiók hitelesítő adatait az elsődleges hozzáférési kulcsot. Ezt a kulcsot, keresse meg az Azure Storage szolgáltatást a tárolási fiók hitelesítő adatait, és kattintson **fiók kulcsok kezelése**. Ekkor átmásolhatja az elsődleges elérési kulcsot.
   
    4. Az SSL engedélyezéséhez kattintson a **engedélyezése** a StorSimple eszköz Manager szolgáltatás és a felhő közötti hálózati kommunikációhoz biztonságos csatorna létrehozása gombra. Kattintson a **letiltása** gomb csak akkor, ha magánfelhőben tevékenykedik.
   
    5. Kattintson az **Add** (Hozzáadás) parancsra. Értesítés jelenik meg a tárolási fiók hitelesítő adatait sikeres létrehozása után.

5. Az újonnan létrehozott tárolási fiók hitelesítő adatait a StorSimple-eszköz Manager konfigurálása szolgáltatás panel alatt jelenik meg **Tárfiók hitelesítő adatainak**.
   
    ![Adja hozzá a tárolási fiók hitelesítő adatait, a Device Manager szolgáltatási előfizetés kívül](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>A tárolási fiók hitelesítő adatok szerkesztése
A tárolási fiók hitelesítő adatait az eszköz által használt szerkesztheti. Ha manuálisan szerkeszti a tárolási fiók hitelesítő adatait, amely jelenleg használatban van, a módosítható mezők a következők a hozzáférési kulcsot és a tárolási fiók hitelesítő adatait az SSL-mód. Adja meg az új tárelérési kulcs, vagy módosítsa a **engedélyezése SSL-mód** kijelölés, és mentse a frissített beállításokat.

#### <a name="to-edit-a-storage-account-credential"></a>A tárolási fiók hitelesítő adatok szerkesztése
1. Keresse meg az Eszközkezelő szolgáltatás, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakasz. A parancs megjeleníti az összes meglévő tárfiók hitelesítő adatainak a StorSimple Device Manager szolgáltatással kapcsolatos.
3. A tárfiók hitelesítő adatainak táblázatos közül válassza ki, és kattintson duplán a fiókot, amelyet módosítani szeretne.
4. A tárolási fiók hitelesítő adatait a **tulajdonságok** panelen tegye a következőket:
   
   1. Ha szükséges, módosíthatja a **SSL engedélyezése** üzemmód kiválasztása.
   2. Dönthet úgy, hogy a fiók hitelesítő adat tárelérési kulcsok újragenerálása. További információkért lásd: [újragenerálni a tárfiókkulcsokat](../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys). Adja meg a tárfiók hitelesítő adatok új kulcsot. Az Azure storage-fiók esetén ez az elsődleges elérési kulcsát.
   3. Kattintson a **mentése** tetején a **tulajdonságok** panelt, és a beállítások mentéséhez. A beállítások frissítése a a **Tárfiók hitelesítő adatainak** panelen.
      
      ![A tárolási fiók hitelesítő adatok szerkesztése](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Törölje a tárolási fiók hitelesítő adatait
> [!IMPORTANT]
> Törölheti a tárolási fiók hitelesítő adatait csak akkor, ha nincs használatban. Ha a tárolási fiók hitelesítő adatait használja, értesítés jelenik meg.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Törli a tárolási fiók hitelesítő adatait
1. Keresse meg az Eszközkezelő szolgáltatás, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakasz. A parancs megjeleníti az összes meglévő tárfiók hitelesítő adatainak a StorSimple Device Manager szolgáltatással kapcsolatos.
3. A tárfiók hitelesítő adatainak táblázatos listájában válassza ki, és kattintson duplán a fiókot, amely törli.
4. A tárolási fiók hitelesítő adatait a **tulajdonságok** panelen tegye a következőket:
   
   1. Kattintson a **törlése** hitelesítő adat törlése.
   2. Amikor felszólítja a megerősítésre, kattintson **Igen** a törlés folytatásához. A táblázatos felsorolása változtatásoknak megfelelően frissül.
      
      ![Törölje a tárolási fiók hitelesítő adatait](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Hitelesítő adatok tárfiókkulcsainak szinkronizálása
Biztonsági okokból kulcs Elforgatás feltétele gyakran adatközpontokban. A Microsoft Azure felügyeleti generálja újra, vagy módosítsa az elsődleges vagy másodlagos kulcsot elérésével közvetlenül a tárolási fiók hitelesítő adatait (keresztül a Microsoft Azure Storage szolgáltatás). Automatikusan a StorSimple Device Manager szolgáltatás nem látja ezt a módosítást.

Tájékoztatja a StorSimple Device Manager szolgáltatás a változás, akkor a StorSimple Device Manager szolgáltatás eléréséhez hozzáférni a tárolási fiók hitelesítő adatait, és majd szinkronizálni az elsődleges vagy másodlagos kulcsot (attól függően, melyiket megváltozott). A szolgáltatás ezután lekérdezi a legutóbbi kulcs, a kulcsokkal titkosítja, és a titkosított kulcs elküldi az eszköznek.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Tárfiók hitelesítő adatainak a szolgáltatást (csak Azure) ugyanahhoz az előfizetéshez tartozó kulcsok szinkronizálása
1. A szolgáltatás követően panelen válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a a **konfigurációs** területén kattintson **Tárfiók hitelesítő adatainak**.
2. Az a **Tárfiók hitelesítő adatainak** panelen, a Tárfiók hitelesítő adatait, jelölje be a tárfiók hitelesítési adatot, amelynek kulcsait szinkronizálni kívánt listájában.
3. Az a **tulajdonságok** panelen a kiválasztott tárolási fiók hitelesítő adatait, hajtsa végre a következőket:
   
    1. Kattintson a **további**, és kattintson a **szinkronizálási hozzáférési kulcs**.
   
    2. Amikor felszólítja a megerősítésre, kattintson **szinkronizálási kulcs** a szinkronizáláshoz.
    
4. A StorSimple Device Manager szolgáltatásban kell frissíteni a kulcsot, amely korábban a Microsoft Azure Storage szolgáltatás megváltozott. Az a **szinkronizálás tárfiók kulcsa** panelt, ha az elsődleges elérési kulcsot megváltozott (újragenerált), kattintson az elsődleges, és kattintson **szinkronizálási kulcs**. Ha a másodlagos kulcs módosult, kattintson a **másodlagos**, és kattintson a **szinkronizálási kulcs**.
   
    ![A szinkronizálási hozzáférési kulcsot](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [felügyelete a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

