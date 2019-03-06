---
title: Oktatóanyag a Microsoft Azure Data Box Disk megrendeléséhez | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan regisztrálhat az Azure Data Box Disk szolgáltatásra, és hogyan rendelheti meg azt az adatok Azure-ba történő importálásához.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 67f4eb5383452a81ba288f5fe611242259217951
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404892"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Oktatóanyag: Az Azure Data Box-lemezek ORDER

Az Azure Data Box Disk egy hibrid felhőalapú megoldás, amellyel gyorsan, könnyen és megbízhatóan importálhat helyszíni adatokat az Azure-ba. Ön letölti az adatokat a Microsoft által SSD-lemezekre, majd visszaküldi azokat a Microsoftnak. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag leírja, hogyan rendelheti meg az Azure Data Box Disk szolgáltatást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box Disk megrendelése
> * A rendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt telepít, a következő előfeltételeknek konfigurációs Data Box szolgáltatás és a Data Box-lemezek.

### <a name="for-service"></a>A szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
- Győződjön meg róla, hogy a Data Box szolgáltatáshoz használt előfizetés a következő típusok valamelyikébe tartozik:
    - Microsoft nagyvállalati szerződés (EA). További információk az [EA-előfizetésekről](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Felhőszolgáltató (CSP). További információk az [Azure CSP programjáról](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- A Data Box-rendelések létrehozásához tulajdonosi vagy közreműködői jogosultsággal kell rendelkeznie az előfizetésen.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Rendelkezik egy ügyfélszámítógép érhető el, amelyről az adatokat másolja. Az ügyfélszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) kell futnia;
    - Windows-ügyfél esetén további [szükséges szoftvereknek](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) telepítve kell lennie.  

## <a name="order-data-box-disk"></a>A Data Box Disk megrendelése

A Data Box Disk megrendeléséhez a következő lépéseket kell végrehajtania az [Azure Portalon](https://aka.ms/azuredataboxfromdiskdocs).

1. Az Azure Portal bal felső sarkában kattintson a **+ Erőforrás létrehozása** elemre, és keresse meg az *Azure Data Box* lehetőséget. Kattintson az **Azure Data Box** lehetőségre.
    
   ![Keresse meg az Azure Data Box lehetőséget 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Kattintson a **Create** (Létrehozás) gombra.

3. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg, vagy jelölje ki a következő információkat, majd kattintson az **Alkalmaz** elemre.

    ![Data Box Disk lehetőség kiválasztása](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Beállítás|Érték|
    |---|---|
    |Előfizetés|Válassza ki azt az előfizetést, amely számára engedélyezve van a Data Box szolgáltatás.<br> Az előfizetés az Ön számlázási fiókjához lesz társítva. |
    |Átvitel típusa| Importálás az Azure-ba|
    |Forrásország | Válassza ki azt az országot, ahol az adatok jelenleg találhatók.|
    |Azure-beli célrégió|Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat.|

  
5.  Kattintson a **Data Box Disk** elemre. A megoldás maximális kapacitása megrendelésenként (5 lemez) 35 TB. Nagyobb adatmennyiséghez több megrendelést is létrehozhat.

     ![Data Box Disk lehetőség kiválasztása](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  A **Megrendelés** területen adja meg a **Megrendelés részleteit**. Adja meg vagy válassza ki a következő információkat.

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|Adjon meg egy rövid nevet a megrendelés nyomon követéséhez.<br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie. |
    |Erőforráscsoport| Használjon egy már létezőt, vagy hozzon létre újat. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. |
    |Azure-beli célrégió| Válasszon ki egy régiót a tárfiókhoz.<br> Jelenleg csak az USA régióiban, Nyugat-és Észak-Európában, Kanadában és Ausztráliában működő tárfiókok támogatottak. |
    |Adatok becsült mérete (TB)| Adjon meg egy becsült mennyiséget TB-ban. <br>Az adatmennyiség alapján a Microsoft kiküldi Önnek a megfelelő számú 8 TB-os SSD-t (7 TB felhasználható kapacitás). <br>5 lemez maximális felhasználható kapacitása 35 TB. |
    |Lemez hozzáférési kulcsa| Ha bejelölte az **Egyéni kulcs használata az Azure által létrehozott hozzáférési kulcs helyett** lehetőséget, adja meg a lemez hozzáférési kulcsát. <br> Adja meg egy 12 – 32 karakter alfanumerikus kulcsát, amelynek legalább egy numerikus és a egy különleges karakter. Az engedélyezett speciális karakterek a következők: `@?_+`. <br> Úgy is dönthet, hogy kihagyja ezt a lehetőséget, és az Azure által létrehozott hozzáférési kulcsot használja a lemezek feloldásához.|
    |A rendeltetési tárolási hely     | Válassza ki a tárfiókot, a felügyelt lemezek vagy mindkettőhöz. <br> A megadott Azure-régió alapján, válassza ki a tárfiók egy meglévő tárfiókot a szűrt listájából. A Data Box legfeljebb 10 tárfiókkal köthető össze. <br> Ezenkívül létrehozhat egy új **általános célú v1**, **általános célú v2**, vagy **Blob storage-fiók**. <br>Storage-fiókok, amelyek rendelkeznek a konfigurált szabályok nem használható. A storage-fiókok kell **engedélyezze a hozzáférést minden hálózatból elérhető** a tűzfalak és virtuális hálózatok szakaszban.|

    Ha a storage-fiókot használ a célhelyet, az alábbi képernyőképen láthatja:

    ![Data Box-lemezrendelését storage-fiók](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Data Box-lemezek használatával felügyelt lemez gyors létrehozásához a helyi VHD-ből, ha akkor is meg kell adja meg a következő információkat:

    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforráscsoport     | Hozzon létre egy új erőforráscsoportot, ha szeretne felügyelt lemez gyors létrehozásához a helyi VHD-ből. Használjon egy meglévő erőforráscsoportot, csak akkor, ha a Data Box szolgáltatás által létrehozott volt a Data Box-lemezrendelését felügyelt lemez. <br> Csak egy erőforráscsoport használata támogatott.|

    ![Data Box-lemezrendelését felügyelt lemez](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    Felügyelt lemezek megadott tárfiók egy átmeneti tárfiókban lesz. A Data Box szolgáltatás feltölti a virtuális merevlemezeket az előkészítési tárfiókból konvertálja azokat a felügyelt lemezek, és áthelyezi az erőforráscsoportokat. További információkért lásd: [adatok ellenőrzése a feltöltése az Azure-bA](data-box-disk-deploy-picked-up.md#verify-data-upload-to-azure).

13. Kattintson a **tovább**.

    ![A megrendelés részletei](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. A **Szállítási cím** lapon adja meg az utónevét és a vezetéknevét, a cége nevét és postai címét, valamint egy érvényes telefonszámot. Kattintson a **Cím ellenőrzése** elemre. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről. 

    ![Szállítási cím megadása](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Az **Értesítési adatok** területen adja meg az e-mail-címeket. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre. 

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

16. Tekintse át a megrendeléshez kapcsolódó **Összefoglalót** a kapcsolattartási adatokról, az értesítésekről és az adatvédelmi feltételekről. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

17. Kattintson a **Megrendelés** elemre. A megrendelés létrehozása néhány percet vesz igénybe.

 
## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Keresse meg a megrendelést, és nyissa meg az **Áttekintés** területet az állapot megtekintéséhez. A portál a **Megrendelve** állapotot mutatja.

![Data Box Disk állapot megrendelve](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Ha a lemezek nem érhetők el, értesítést kap. Ha a lemezek elérhetők, a Microsoft azonosítja a lemezeket a szállításhoz, és előkészíti a csomagot. A lemezek előkészítése a következőket foglalja magába:

- A lemezek titkosítása a AES-128 BitLocker használatával.  
- A lemezek zárolása az illetéktelen hozzáférés megakadályozására.
- A folyamat során létrejön a lemezek zárolását feloldó hozzáférési kulcs.

A lemezek előkészítésének lezárultával a portál a **Feldolgozott** állapotot mutatja.

A Microsoft ezután előkészíti, majd lebonyolítja a szállítást egy regionális szolgáltatón keresztül. A szállítás megkezdésekor Ön megkapja a nyomkövetési azonosító számot. A portál a **Feladva** állapotot mutatja.

## <a name="cancel-the-order"></a>A rendelés lemondása

A rendelés lemondásához az Azure Portalon lépjen az **Áttekintés** területre, és kattintson a **Megszakítás** gombra a parancssávon.

Csak akkor mondhatja le a rendelést, ha a lemezek megrendelése után a szállításra való előkészítés még folyamatban van. Ha a megrendelés feldolgozása már megtörtént, nem mondhatja le a rendelést.

![Rendelés lemondása](media/data-box-disk-deploy-ordered/cancel-order1.png)

A lemondott rendelés törléséhez nyissa meg az **Áttekintés** területet, és kattintson a parancssávon a **Törlés** elemre.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * A Data Box Disk megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

A következő oktatóanyag a Data Box Disk beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box Disk beállítása](./data-box-disk-deploy-set-up.md)
