---
title: A StorSimple tárfiók hitelesítő adatainak a Microsoft Azure StorSimple 8000 sorozatú eszközök felügyelete |} A Microsoft Docs
description: Azt ismerteti, hogyan használhatja a StorSimple-Eszközkezelő konfigurálása lap hozzáadása, szerkesztése, törlése, vagy storage-fiókok a biztonsági kulcsok rotálására.
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
ms.openlocfilehash: afaa4c090508ee5a2306c2be1515076e6bd4f100
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225725"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>A StorSimple-Eszközkezelő szolgáltatás segítségével a tárfiók hitelesítő adatainak kezelése

## <a name="overview"></a>Áttekintés

A **konfigurációs** a StorSimple-Eszközkezelő szolgáltatás paneljén fejezet ismerteti a StorSimple-Eszközkezelő szolgáltatásban létrehozott összes globális szolgáltatás paramétert. Ezeket a paramétereket a szolgáltatáshoz csatlakoztatott összes eszközre alkalmazhatók, és a következők:

* Tárfiók hitelesítő adatai
* Sávszélességsablonok 
* Hozzáférés-vezérlési rekordok 

Ez az oktatóanyag azt ismerteti, hogyan hozzáadása, szerkesztése, vagy törölje a tárfiók hitelesítő adatait, vagy a storage-fiók biztonsági kulcsainak rotálása.

 ![Tárfiók hitelesítő adatainak listája](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Storage-fiókok tartalmazhat a hitelesítő adatokat, amelyek a StorSimple-eszközt használ, a tárfiók eléréséhez a felhőszolgáltatóval. A Microsoft Azure storage-fiókok esetében ezek a hitelesítő adatok, például a fiók nevét és az elsődleges elérési kulcsot. 

Az a **Tárfiók hitelesítő adatainak** panelen, a számlázási előfizetés a következő információkat tartalmazó táblázatos formában jelenik meg a létrehozott összes storage-fiókok:

* **Név** – az egyedi nevet a fiókhoz rendelt létrehozásakor.
* **SSL engedélyezve** – e az SSL protokoll engedélyezve van, és a biztonságos csatornán az eszközről a felhőbe való kommunikációt.
* **Által használt** – a storage-fiók használatával kötetek számát.

A végrehajtható storage-fiókokhoz kapcsolódó leggyakoribb feladatok a következők:

* Tárfiók hozzáadása 
* Storage-fiók szerkesztése 
* Tárfiók törlése 
* A tárfiókok kulcsrotálás 

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

A tárfiókok a StorSimple-eszközre használható három típusa van.

* **Automatikusan létrehozott tárfiókok** – javasol a neve, ahogy ez a tárfióktípus automatikusan jön létre a szolgáltatás első létrehozásakor. Ez a tárfiók létrehozását kapcsolatos további információkért lásd: [1. lépés: hozzon létre egy új szolgáltatást](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md). 
* **A szolgáltatási előfizetés tárfiókjai** – ezek olyan, a szolgáltatás, amely ugyanahhoz az előfizetéshez társított Azure storage-fiókok. Ha többet szeretne megtudni arról, hogy ezen storage fiókokat hoznak létre, tekintse meg a [kapcsolatos Azure-Tárfiókok](../storage/common/storage-create-storage-account.md). 
* **Storage-fiókok a szolgáltatási előfizetés kívül** – ezek azok az Azure storage-fiókok nem a szolgáltatáshoz rendelt és valószínűleg már létezett a szolgáltatás létrehozása előtt.

## <a name="add-a-storage-account"></a>Tárfiók hozzáadása

Egy storage-fiókot is hozzáadhat egy egyedi rövid nevet és a hozzáférési hitelesítő adatokat, amely kapcsolódik a storage-fiók (a megadott felhő-szolgáltató) megadásával. Lehetősége is van, a secure sockets layer (SSL) mód engedélyezése az eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni.

Egy adott felhőre szolgáltatónál több fiókot is létrehozhat. Ne feledje azonban, a storage-fiók létrehozása után nem módosíthatja a felhőszolgáltató.

A storage-fiók mentése folyamatban van, amíg a szolgáltatás próbál meg kommunikálni a felhőszolgáltatást. A hitelesítő adatokat, és a hozzáférés-referenciaanyag megadott hitelesítése jelenleg. Storage-fiók jön létre, csak akkor, ha a hitelesítés sikeres. Ha a hitelesítés nem sikerül, egy megfelelő hibaüzenetet fog megjelenni.

A következő eljárások használatával adja hozzá az Azure storage-fiók hitelesítő adatait:

* A tárfiók-hitelesítő adat hozzáadása, amely rendelkezik az Azure-előfizetéshez a Device Manager szolgáltatás
* Egy Azure tárfiók-hitelesítő adat, amely kívül esik a Device Manager szolgáltatási előfizetés hozzáadása

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>A StorSimple-Eszközkezelő szolgáltatási előfizetés kívül egy Azure storage tárfiók-hitelesítő adat hozzáadása

1. Keresse meg a StorSimple-Eszközkezelő szolgáltatáshoz, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakaszban. A StorSimple-Eszközkezelő szolgáltatáshoz társított storage-fiókhitelesítő felsorolja.
3. Kattintson a **Hozzáadás** parancsra.
4. Az a **adja hozzá a tárfiók-hitelesítő adat** panelen tegye a következőket:
   
    1. A **előfizetés**válassza **más**.
   
    2. Adja meg az Azure storage tárfiók-hitelesítő adat nevét.
   
    3. Az a **Tárfiók hozzáférési kulcsát** szöveget adja meg az elsődleges hívóbetűjét az Azure storage-fiók hitelesítő adatait. Ezt a kulcsot, keresse fel az Azure Storage szolgáltatásba, válassza ki a tárfiók-hitelesítő adat, és kattintson a **kezelheti a fiókkulcsokat**. Ekkor átmásolhatja az elsődleges elérési kulcsot.
   
    4. Az SSL engedélyezéséhez kattintson a **engedélyezése** gombra kattintva hozzon létre egy biztonságos csatornát a StorSimple-Eszközkezelő szolgáltatás és a felhő közötti hálózati kommunikációhoz. Kattintson a **letiltása** gomb csak akkor, ha a magánfelhő belül működik.
   
    5. Kattintson a **Hozzáadás** parancsra. A tárfiók-hitelesítő adat sikeres létrehozása után értesítést kap.

5. Az újonnan létrehozott tárfiók-hitelesítő adat jelenik meg a StorSimple-Eszközkezelő konfigurálása service panel **Tárfiók hitelesítő adatainak**.
   


## <a name="edit-a-storage-account"></a>Storage-fiók szerkesztése

A kötettároló által használt tárfiók szerkesztheti. Ha módosít egy tárfiókot, amelyet jelenleg használatban van, az egyetlen módosítható mező a tárfiók hozzáférési kulcsára. Adja meg az új tárelérési kulcs, és mentse a frissített beállításokat.

#### <a name="to-edit-a-storage-account"></a>Storage-fiók szerkesztése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

    ![Tárfiók hitelesítő adatai](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Az a **Tárfiók hitelesítő adatainak** panelen a tárfiók hitelesítő adatait, listájából válassza ki, majd kattintson a szerkeszteni kívánt. 

3. Módosíthatja a **SSL engedélyezése** kiválasztása. Is **több...**  majd **szinkronizálási hozzáférési kulcs rotálása** a storage-fiók hozzáférési kulcsait. Lépjen a [elforgatási szögét tárfiókok kulcs](#key-rotation-of-storage-accounts) kulcsrotálás végrehajtásával kapcsolatos további információt. Miután módosította a beállításokat, kattintson a **mentése**. 

    ![Szerkesztett tárfiók hitelesítő adatainak mentése](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. 

    ![Módosítás megerősítése](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

A beállítások módosítása és a storage-fiókjába menti. 

## <a name="delete-a-storage-account"></a>Tárfiók törlése

> [!IMPORTANT]
> Csak akkor, ha nem használta a kötettároló tárfiók törölheti. A kötettároló tárfiók használja, ha először törölje a kötettárolót, és ezután törölje a társított storage-fiók.

#### <a name="to-delete-a-storage-account"></a>A tárfiók törlése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

2. Storage-fiókok táblázatos listájában a kurzort a fiókot, amelyet törölni kíván. Indítja el a helyi menüt, és kattintson a jobb gombbal **törlése**.

    ![Tárfiók hitelesítő adatainak törlése](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Amikor a rendszer megerősítést kér, kattintson a **Igen** a törlés folytatásához. Táblázatos frissül az elvégzett módosításoknak megfelelően.

    ![Törlés megerősítése](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>A tárfiókok kulcsrotálás

Biztonsági okokból kulcsrotálás akkor gyakran szükséges az adatközpontokban. Minden Microsoft Azure-előfizetéssel rendelkezhet egy vagy több társított storage-fiókok. Az előfizetés és a hozzáférési kulcsok minden olyan tárfiókhoz vezérli a hozzáférést ezekhez a fiókokhoz. 

Amikor létrehoz egy tárfiókot, a Microsoft Azure két 512 bites tárelérési kulcsot a hitelesítéshez a tárfiók eléréséhez használt állít elő. Két tárelérési kulcsot lehetővé teszi, hogy a kulcsok a storage szolgáltatás megszakítás nélkül, vagy a szolgáltatáshoz való hozzáférést. A kulcs, amely jelenleg használatban van a *elsődleges* és a biztonsági mentési kulcs nevezzük a *másodlagos* kulcsot. E két kulcsok egyikét kell megadni, ha a Microsoft Azure StorSimple-eszköz hozzáfér a tároló felhőszolgáltatást.

## <a name="what-is-key-rotation"></a>Mi az a kulcsforgatás?

Általában alkalmazások használata csak az egyik a kulcsokat az adatok eléréséhez. Egy bizonyos idő után az alkalmazásokat, váltson a második kulcsot is használhat. Miután az alkalmazások a másodlagos kulcsot vált, vonja ki az első kulcsot, és majd hozzon létre egy új kulcsot. A két kulcs ezzel a módszerrel lehetővé teszi az alkalmazások az adatokhoz való hozzáférés állásidő nélkül.

A tárfiók kulcsaihoz mindig a szolgáltatás titkosított formában vannak tárolva. Azonban ezeket is lehet alaphelyzetbe állítani a StorSimple-Eszközkezelő szolgáltatáson keresztül. A szolgáltatás kérheti le az elsődleges és másodlagos kulcsot a storage-fiókok ugyanahhoz az előfizetéshez, beleértve a Storage szolgáltatásban létrehozott fiókokat, valamint a jönnek létre, ha a StorSimple-Eszközkezelő szolgáltatás első nem alapértelmezett storage-fiókok létrehozott. A StorSimple-Eszközkezelő szolgáltatás minden esetben ezek a kulcsok lekérése a klasszikus Azure portálon, és biztonságosan tárolja a titkosított módon.

## <a name="rotation-workflow"></a>Elforgatás munkafolyamat

A Microsoft Azure-rendszergazdák is vagy az elsődleges vagy másodlagos kulcsot megváltoztatták a storage-fiókban (a Microsoft Azure Storage szolgáltatás) használatával közvetlenül elérésével. A StorSimple-Eszközkezelő szolgáltatás nem jelenik meg ez a változás automatikusan.

Tájékoztatja a változás a StorSimple-Eszközkezelő szolgáltatás, meg fog a StorSimple-Eszközkezelő szolgáltatás eléréséhez a tárfiók eléréséhez, és ezután szinkronizálnia kell az elsődleges vagy másodlagos kulcsot (attól függően, melyiket megváltozott). A szolgáltatás ezután lekérdezi a legújabb kulcsot, a kulcsokkal titkosítja, és elküldi az eszköznek a titkosított kulcsot.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>A szolgáltatás ugyanabban az előfizetésben a tárfiókok hívóbetűinek szinkronizálása 
1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.
2. Storage-fiókok táblázatos listájából, kattintson a módosítani kívánt. 

    ![kulcsok szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Kattintson a **... További** majd **szinkronizálási hozzáférési kulcs rotálása**.   

    ![kulcsok szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. A StorSimple-Eszközkezelő szolgáltatásban frissíteni szeretné a kulcsot, amely korábban módosult a Microsoft Azure Storage szolgáltatásban. Ha az elsődleges elérési kulcsot (sikeres újragenerálás) megváltozott, válassza ki a **elsődleges** kulcsot. Ha a másodlagos kulcs megváltozott, válassza ki a **másodlagos** kulcsot. Kattintson a **kulcs szinkronizálása**.
      
      ![kulcsok szinkronizálása](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Miután a kulcs sikeresen sycnhronized figyelmeztetésekhez.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>A szolgáltatási előfizetés kívül tárfiókok hívóbetűinek szinkronizálása
1. Az a **szolgáltatások** lap, kattintson a **konfigurálása** fülre.
2. Kattintson a **Storage-fiókok hozzáadása/szerkesztése**.
3. A párbeszédpanelen tegye a következőket:
   
   1. Válassza ki a tárfiók hozzáférési kulcsára, amelyet frissíteni szeretne.
   2. A StorSimple-Eszközkezelő szolgáltatásban a tárelérési kulcs frissíteni kell. Ebben az esetben megjelenik a tárelérési kulcs. Adja meg az új kulcsot a **Tárfiók hozzáférési kulcsát** mezőbe. 
   3. Mentse a módosításokat. A tárfiók hozzáférési kulcsát most frissíteni kell.

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [StorSimple biztonsági](storsimple-8000-security.md).
* Tudjon meg többet [a StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

