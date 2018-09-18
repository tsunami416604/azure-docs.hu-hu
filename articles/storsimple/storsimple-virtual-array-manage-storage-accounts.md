---
title: A StorSimple Virtual Array tárfiók hitelesítő adatainak kezelése |} A Microsoft Docs
description: Ismerteti, hogyan használhatja a StorSimple-Eszközkezelő konfigurálás lapról való hozzáadása, szerkesztése, törlése vagy a StorSimple Virtual Array társított storage-fiók hitelesítő adatai számára a biztonsági kulcsok rotálására.
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
ms.openlocfilehash: d2b6b7a5d2baf9e46cca18aec97f31beda1e2a38
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736733"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Tárfiók hitelesítő adatainak kezelése a StorSimple Virtual Array StorSimple Eszközkezelő

## <a name="overview"></a>Áttekintés
A **konfigurációs** a StorSimple-Eszközkezelő szolgáltatás paneljén a StorSimple Virtual Array szakasza bemutatja a globális szolgáltatás paraméterek, amelyek a StorSimple Manager szolgáltatásban hozható létre. Ezeket a paramétereket a szolgáltatáshoz csatlakoztatott összes eszközre alkalmazhatók, és a következők:

* Tárfiók hitelesítő adatai
* Hozzáférés-vezérlési rekordok
  
  ![Az Eszközkezelő szolgáltatási irányítópult](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Ez az oktatóanyag azt ismerteti, hogyan hozzáadása, szerkesztése vagy törlése a StorSimple Virtual Array tárfiók hitelesítő adatait. Ebben az oktatóanyagban szereplő információk csak a StorSimple Virtual Array vonatkozik. Információk a storage-fiókok a 8000-es sorozat felügyelete: [a StorSimple Manager szolgáltatás használata kezelheti a tárfiók](storsimple-manage-storage-accounts.md).

Tárfiók hitelesítő adatait, amely a felhőbeli szolgáltató a tárfiók eléréséhez használja az eszköz hitelesítő adatait tartalmazza. A Microsoft Azure storage-fiókok esetében ezek a hitelesítő adatok, például a fiók nevét és az elsődleges elérési kulcsot.

Az a **Tárfiók hitelesítő adatainak** panelen, és az összes tárfiók hitelesítő adatait, amely jönnek létre a következő információkat tartalmazó táblázatos formában jelennek meg a számlázási előfizetés esetében:

* **Név** – az egyedi nevet a fiókhoz rendelt létrehozásakor.
* **SSL engedélyezve** – e az SSL protokoll engedélyezve van, és a biztonságos csatornán az eszközről a felhőbe való kommunikációt.
  
  ![A konfigurációs szakasz](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Tárfiók hitelesítő adatait, amely végrehajtható kapcsolatos leggyakoribb feladatokat az **Tárfiók hitelesítő adatainak** panel vannak:

* Tárfiók hitelesítő adatainak hozzáadása
* A tárfiók-hitelesítő adatok szerkesztése
* A tárfiók-hitelesítő adat törlése

## <a name="types-of-storage-account-credentials"></a>Tárfiók hitelesítő adatainak típusa
Tárfiók hitelesítő adatait, amely a StorSimple-eszköz használható három típusa van.

* **Automatikusan létrehozott tárfiók hitelesítő adatainak** – javasol a nevét, mert az ilyen típusú tárfiók-hitelesítő adat automatikusan jön létre a szolgáltatás első létrehozásakor. A tárfiók-hitelesítő adat létrehozása hogyan kapcsolatos további információkért lásd: [hozzon létre egy új szolgáltatást](storsimple-virtual-array-manage-service.md#create-a-service).
* **tárfiók hitelesítő adatait a szolgáltatás az előfizetésben** – ezek olyan, a szolgáltatás, amely ugyanahhoz az előfizetéshez társított Azure storage fiók hitelesítő adatait. Tudjon meg többet arról, hogy ezen a storage-ról jönnek létre fiók hitelesítő adatait, tekintse meg [kapcsolatos Azure-Tárfiókok](../storage/common/storage-create-storage-account.md).
* **tárfiók hitelesítő adatainak kívül a szolgáltatási előfizetés** – ezek azok a az Azure-tárfiók hitelesítő adatai nem a szolgáltatáshoz rendelt és valószínűleg már létezett a szolgáltatás létrehozása előtt.

## <a name="add-a-storage-account-credential"></a>Tárfiók hitelesítő adatainak hozzáadása
Adhat hozzá egy tárfiók-hitelesítő adatot a StorSimple-Eszközkezelő szolgáltatás konfigurációjának azáltal, hogy egy egyedi rövid nevet és a hozzáférési hitelesítő adatokat, amely a storage-fiókhoz van csatolva. Lehetősége is van, a secure sockets layer (SSL) mód engedélyezése az eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni.

Egy adott felhőre szolgáltatónál több fiókot is létrehozhat. A tárfiók-hitelesítő adat mentése folyamatban van, amíg a szolgáltatás próbál meg kommunikálni a felhőszolgáltatást. A hitelesítő adatokat, és a hozzáférés-referenciaanyag megadott jelenleg hitelesítése. A tárfiók-hitelesítő adatot csak akkor, ha a hitelesítés sikeres jön létre. Ha a hitelesítés nem sikerül, majd egy megfelelő hibaüzenetet jelenik meg.

A következő eljárások használatával adja hozzá az Azure storage-fiók hitelesítő adatait:

* A tárfiók-hitelesítő adat hozzáadása, amely rendelkezik az Azure-előfizetéshez a Device Manager szolgáltatás
* Egy Azure tárfiók-hitelesítő adat, amely kívül esik a Device Manager szolgáltatási előfizetés hozzáadása

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>A tárfiók-hitelesítő adat hozzáadása, amely rendelkezik az Azure-előfizetéshez a Device Manager szolgáltatás

1. Keresse meg a Device Manager szolgáltatáshoz, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakaszban.
3. Kattintson a **Hozzáadás** parancsra.
4. Az a **tárfiók hozzáadása** panelen tegye a következőket:
   
    1. A **előfizetés**válassza **aktuális**.
    2. Adja meg az Azure storage-fiók nevét.
    3. Válassza ki **engedélyezése** a StorSimple-eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni. Válassza ki **letiltása** csak akkor, ha a magánfelhő belül működik.
    4. Kattintson a **Hozzáadás** parancsra. A storage-fiók sikeres létrehozása után értesítést kap.<br></br>
   
        ![Egy meglévő tárfiók-hitelesítő adat hozzáadása](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Egy Azure tárfiók-hitelesítő adat, amely kívül esik a Device Manager szolgáltatási előfizetés hozzáadása

1. Keresse meg a Device Manager szolgáltatáshoz, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakaszban. A StorSimple-Eszközkezelő szolgáltatáshoz társított storage-fiókhitelesítő felsorolja.
3. Kattintson a **Hozzáadás** parancsra.
4. Az a **tárfiók hozzáadása** panelen tegye a következőket:
   
    1. A **előfizetés**válassza **más**.
   
    2. Adja meg az Azure storage tárfiók-hitelesítő adat nevét.
   
    3. Az a **Tárfiók hozzáférési kulcsát** szöveget adja meg az elsődleges hívóbetűjét az Azure storage-fiók hitelesítő adatait. Ezt a kulcsot, keresse fel az Azure Storage szolgáltatásba, válassza ki a tárfiók-hitelesítő adat, és kattintson a **kezelheti a fiókkulcsokat**. Ekkor átmásolhatja az elsődleges elérési kulcsot.
   
    4. Az SSL engedélyezéséhez kattintson a **engedélyezése** gombra kattintva hozzon létre egy biztonságos csatornát a StorSimple-Eszközkezelő szolgáltatás és a felhő közötti hálózati kommunikációhoz. Kattintson a **letiltása** gomb csak akkor, ha a magánfelhő belül működik.
   
    5. Kattintson a **Hozzáadás** parancsra. A tárfiók-hitelesítő adat sikeres létrehozása után értesítést kap.

5. Az újonnan létrehozott tárfiók-hitelesítő adat jelenik meg a StorSimple-Eszközkezelő konfigurálása service panel **Tárfiók hitelesítő adatainak**.
   
    ![A tárfiók-hitelesítő adat kívül a Device Manager szolgáltatási előfizetés hozzáadása](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>A tárfiók-hitelesítő adatok szerkesztése
A tárfiók-hitelesítő adat az eszköz által használt szerkesztheti. Szerkeszti a tárfiók-hitelesítő adat, amely jelenleg használatban van, a mezők módosítható-e a hozzáférési kulcsot, és a tárfiók-hitelesítő adat az SSL-mód. Adja meg az új tárelérési kulcs, vagy módosítsa a **engedélyezi SSL-mód** kiválasztása, és mentse a frissített beállításokat.

#### <a name="to-edit-a-storage-account-credential"></a>A tárfiók-hitelesítő adatok szerkesztése
1. Keresse meg a Device Manager szolgáltatáshoz, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakaszban. A StorSimple-Eszközkezelő szolgáltatáshoz társított storage-fiókhitelesítő felsorolja.
3. A tárfiók hitelesítő adatainak táblázatos listájában válassza ki, és kattintson duplán a fiókot, amelyet módosítani szeretne.
4. A tárfiók-hitelesítő adat a **tulajdonságok** panelen tegye a következőket:
   
   1. Ha szükséges, módosíthatja a **SSL engedélyezése** mód kiválasztása.
   2. Ha szeretné, a fiók hitelesítő adat tárelérési kulcsok újragenerálása. További információkért lásd: [újragenerálni a tárfiókkulcsokat](../storage/common/storage-account-manage.md#access-keys). Adja meg a új tárfiók-hitelesítő adat kulcsot. Azure storage-fiókkal Ez az elsődleges elérési kulcsát.
   3. Kattintson a **mentése** felső részén a **tulajdonságok** panelen a beállítások mentéséhez. A beállítások biztosan frissüljenek a a **Tárfiók hitelesítő adatainak** panelen.
      
      ![A tárfiók-hitelesítő adatok szerkesztése](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>A tárfiók-hitelesítő adat törlése
> [!IMPORTANT]
> Törölheti a tárfiók-hitelesítő adat, csak ha nem használja. Ha a tárfiók-hitelesítő adat használatban van, értesítést kap.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>A tárfiók-hitelesítő adat törlése
1. Keresse meg a Device Manager szolgáltatáshoz, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakaszban. A StorSimple-Eszközkezelő szolgáltatáshoz társított storage-fiókhitelesítő felsorolja.
3. A tárfiók hitelesítő adatainak táblázatos listájában válassza ki, és kattintson duplán a fiókot, amelyet törölni szeretne.
4. A tárfiók-hitelesítő adat a **tulajdonságok** panelen tegye a következőket:
   
   1. Kattintson a **törlése** hitelesítő adat törlése.
   2. Amikor a rendszer megerősítést kér, kattintson a **Igen** a törlés folytatásához. Táblázatos frissül az elvégzett módosításoknak megfelelően.
      
      ![A tárfiók-hitelesítő adat törlése](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Hitelesítő adatok tárfiókkulcsainak szinkronizálása
Biztonsági okokból kulcsrotálás akkor gyakran szükséges az adatközpontokban. A Microsoft Azure rendszergazda újbóli létrehozása vagy az elsődleges vagy másodlagos kulcsot módosítani a tárfiók-hitelesítő adat (a Microsoft Azure Storage szolgáltatás) használatával való közvetlen hozzáféréssel. A StorSimple-Eszközkezelő szolgáltatás nem jelenik meg ez a változás automatikusan.

Tájékoztatja a változás a StorSimple-Eszközkezelő szolgáltatás, meg kell a StorSimple-Eszközkezelő szolgáltatás eléréséhez a tárfiók-hitelesítő adat eléréséhez, és ezután szinkronizálnia kell az elsődleges vagy másodlagos kulcsot (attól függően, melyiket megváltozott). A szolgáltatás ezután lekérdezi a legújabb kulcsot, a kulcsokkal titkosítja, és elküldi az eszköznek a titkosított kulcsot.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>A tárfiók hitelesítő adatainak ugyanahhoz az előfizetéshez tartozik, mint a szolgáltatás (csak Azure-) kulcsok szinkronizálása
1. A szolgáltatás alkotóelemeit panelen válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **konfigurációs** területén kattintson **Tárfiók hitelesítő adatainak**.
2. Az a **Tárfiók hitelesítő adatainak** panelen, a Tárfiók hitelesítő adatait, válassza ki a tárfiók hitelesítőadat-amelynek szinkronizálni kívánt kulcsok listája.
3. Az a **tulajdonságok** panelen a kiválasztott tárfiók-hitelesítő adat, tegye a következőket:
   
    1. Kattintson a **további**, és kattintson a **elérési kulcsának szinkronizálása**.
   
    2. Amikor a rendszer megerősítést kér, kattintson a **kulcs szinkronizálása** a szinkronizálás végrehajtásához.
    
4. A StorSimple-Eszközkezelő szolgáltatásban frissíteni szeretné a kulcsot, amely korábban módosult a Microsoft Azure Storage szolgáltatásban. Az a **Synchronize tárfiókkulcs** panelen, ha az elsődleges elérési kulcs megváltozott (sikeres újragenerálás), kattintson az elsődleges, és kattintson **kulcs szinkronizálása**. Ha a másodlagos kulcs megváltozott, kattintson a **másodlagos**, és kattintson a **kulcs szinkronizálása**.
   
    ![Elérési kulcs szinkronizálása](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

