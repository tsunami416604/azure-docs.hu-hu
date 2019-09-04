---
title: Oktatóanyag a Azure Data Box Heavy visszaszállításához | Microsoft Docs
description: Ismerje meg, hogyan szállíthatja a Azure Data Box Heavyt a Microsoftnak
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: e438fb38afb649f6f4c7f595059ef64800977242
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240349"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Oktatóanyag: Az Azure-ba való adatfeltöltés Azure Data Box Heavyának visszaküldése és ellenőrzése

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Az Azure-ba való adatfeltöltés Azure Data Box Heavyának visszaküldése és ellenőrzése

::: zone-end

::: zone target = "docs"

Ez az oktatóanyag azt ismerteti, hogyan lehet visszaadni Azure Data Box Heavy és ellenőrizni az Azure-ba feltöltött információkat.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
> * Előfeltételek
> * Szállításra való előkészítés
> * Data Box Heavy szállítása a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése Data Box Heavy

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg róla, hogy:

- Elvégezte az [oktatóanyagot: Másolja az adatAzure Data Box és ellenőrizze](data-box-heavy-deploy-copy-data.md)a következőt:.
- A másolási feladatok készek. A szállításra való előkészítés nem futtatható, ha a másolási feladatok folyamatban vannak.


## <a name="prepare-to-ship"></a>Szállításra való előkészítés

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Szállításra való előkészítés

A szállítás előkészítése előtt győződjön meg arról, hogy a másolási feladatok készek.

1. Nyissa meg szállításra való előkészítés lapot a helyi webes felületen, és indítsa el a hajó előkészítését.
2. Kapcsolja ki az eszközt a helyi webes kezelőfelületen. Válassza le a kábeleket az eszközről.

Most már készen áll az eszköz újbóli szállítására.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Szállítás Data Box Heavy vissza

1. Győződjön meg arról, hogy az eszköz ki van kapcsolva, és a kábelek el lesznek távolítva. Az eszköz hátoldalán található 4 tápkábelt helyezze el biztonságosan a tálcába.
2. Az eszköz az USA-beli és a DHL-ben az EU-ban

    1. Lépjen kapcsolatba [Data Box műveletekkel](mailto:DataBoxOps@microsoft.com) , hogy tájékoztassa a pickupot és a visszaszállítási címkét.
    2. Hívja meg a szállítási szolgáltató helyi számát a pickup időzítéséhez.
    3. Győződjön meg arról, hogy a szállítási címke kiemelten jelenik meg a szállítás külsején.
    4. Győződjön meg arról, hogy az előző szállítmányból származó régi szállítási címkék el lesznek távolítva az eszközről.
3. Miután megtörtént a Data Box Heavy beolvasása és ellenőrzése a szolgáltatónál, a portálon a megrendelési állapotot a rendszer **felveszi**. A nyomkövetési azonosító is megjelenik.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Amikor a Microsoft megkapja és beolvassa az eszközt, a rendelés **Megérkezett** állapotra vált. Az eszközt ezután fizikai ellenőrzésnek vetjük alá, sérüléseket vagy illetéktelen hozzáférés jeleit kutatva.

Az ellenőrzés befejezése után a Data Box Heavy csatlakozik a hálózathoz az Azure-adatközpontban. Az adatok másolása automatikusan megkezdődik. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

Győződjön meg arról, hogy az adatok fel vannak töltve az Azure-ba, mielőtt törölné a forrásból. Az adatai a következőket vehetik fel:

- Azure Storage-fiókja (ke) t. A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- A felügyelt lemez erőforrás-csoportja (i). A felügyelt lemezek létrehozásakor a virtuális merevlemezeket blobként kell feltölteni, majd felügyelt lemezekre konvertáljuk. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoporthoz vannak csatolva. 

    - Ha az Azure-beli felügyelt lemezekre történő másolás sikeres volt, lépjen a Azure Portal **rendelés részleteibe** , és jegyezze fel a felügyelt lemezekhez megadott erőforráscsoportokat.

        ![Felügyelt lemezes erőforráscsoportok azonosítása](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Nyissa meg a feljegyzett erőforráscsoportot, és keresse meg a felügyelt lemezeket.

        ![Erőforráscsoporthoz csatolt felügyelt lemez](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Ha egy VHDX vagy egy dinamikus/különbséglemezek VHD-t másolt, akkor a VHDX/VHD-t oldal blobként kell feltölteni az átmeneti tárolóba, de a VHD-t felügyelt lemezre való átalakítás meghiúsul. Nyissa meg az átmeneti **tárolási fiókot > blobokat** , majd válassza ki a megfelelő tároló-standard SSD, standard HDD vagy prémium SSD. A virtuális merevlemezeket a rendszer az átmeneti tárolási fiókban lévő blobként töltse fel.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Ha a Data Box Heavy eszköz csatlakozik az Azure Datacenter Network szolgáltatáshoz, az Azure-ba történő adatfeltöltés automatikusan elindul. Data Box szolgáltatás értesíti arról, hogy az Adatmásolás befejeződött az Azure Portalon keresztül.

- Ellenőrizze a hibákat a hibanaplókban, és tegye meg a szükséges intézkedéseket.
- Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Adatok törlése Data Box Heavy
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően. A törlés befejezése után [letöltheti a megrendelési előzményeket](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Szállításra való előkészítés
> * Data Box Heavy szállítása a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése Data Box Heavy

A következő cikkből megtudhatja, hogyan kezelheti Data Box Heavy a helyi webes felületen keresztül.

> [!div class="nextstepaction"]
> [Az Azure Data Box felügyelete a helyi webes felhasználói felülettel](./data-box-local-web-ui-admin.md)

::: zone-end


