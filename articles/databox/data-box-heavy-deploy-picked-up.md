---
title: Az oktatóanyag az Azure Data Box nehéz küldje vissza |} A Microsoft Docs
description: Ismerje meg, hogyan tehetnek elérhetővé az Azure Data Box (nagy erőforrásigényű) a Microsoftnak
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 84db33e4c7ac612353c590ac9d2904ac3bc48d38
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592393"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Oktatóanyag: Vissza az Azure Data Box (nagy erőforrásigényű), és ellenőrizze az adatok feltöltése az Azure-bA


Ebben az oktatóanyagban adja vissza az Azure Data Box (nagy erőforrásigényű), és ellenőrizze, hogy az adatokat az Azure-bA feltöltött módját ismerteti.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * Szállítási adatok Box nehéz a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * A Data Box nehéz törlését

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

- Ön teljesítette a [oktatóanyag: Adatok másolása az Azure Data Box, és ellenőrizze](data-box-heavy-deploy-copy-data.md).
- Másolás feladatok elkészültek. Szállításra való nem futtatható, ha a másolási feladat folyamatban van.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>Szállítási adatok Box nehéz vissza

1. Győződjön meg arról, hogy az eszköz be van kapcsolva, és minden kábel el lesznek távolítva. Várólistában hozzá tartozó, és a 4 tápkábelek biztonságosan érheti el az eszközt vissza a tálca helyezze.
2. Az eszköz mobilplatform LTL árufuvarozási FedEx az Egyesült Államokban és DHL az EU-n keresztül

    1. Kapcsolatfelvétel [Box Adatműveletek](mailto:DataBoxOps@microsoft.com) tájékoztatja a begyűjtés kapcsolatban, valamint hogy a visszaszállítási címkét.
    2. Hívja meg a helyi számot a szállítási szolgáltató a begyűjtés ütemezéséhez.
    3. Győződjön meg arról, hogy a szállítási címkét a szállítmány külső ezzel a beállítással hangsúlyosan megjelenik-e.
    4. Győződjön meg arról, hogy a korábbi szállítmány a régi szállítási címkéit az eszközről törlődnek.
3. A Data Box nehéz mértékének és a szolgáltató által beolvasott, miután a rendelés állapota a portálon frissítései **mértékének**. A nyomkövetési azonosító is megjelenik.

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Amikor a Microsoft megkapja és beolvassa az eszközt, a rendelés **Megérkezett** állapotra vált. Az eszközt ezután fizikai ellenőrzésnek vetjük alá, sérüléseket vagy illetéktelen hozzáférés jeleit kutatva.

Az ellenőrzés befejezése után a Data Box nehéz csatlakozik a hálózathoz az Azure-adatközpontban. Az adatok másolása automatikusan megkezdődik. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

Győződjön meg arról, hogy az adatok feltöltése az Azure-ba, a forrás-törlés előtt. Az adatok lehetnek:

- Az Azure Storage-fiókok. A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- A felügyelt lemez erőforráscsoport(ok). Felügyelt lemezek létrehozásakor a virtuális merevlemezeket lapblobként feltöltött és majd a felügyelt lemezekké való konvertálása. A felügyelt lemezek vannak csatolva az erőforráscsoportok, a megadott sorrendben létrehozásának időpontjában. 

    - Ha a példány az Azure-ban felügyelt lemezekre sikeres volt, megnyithatja a **rendelés részletei** az Azure Portalon, és jegyezze fel az erőforráscsoportokat a felügyelt lemezek megadott ügyeljen.

        ![Felügyelt lemez az erőforrás-csoportok azonosítása](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Nyissa meg a feljegyzett erőforráscsoportot, és keresse meg a felügyelt lemezek.

        ![Felügyelt erőforráscsoport csatlakoztatott lemez](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Ha egy dinamikus vagy különbséglemez VHD vagy vhdx-fájlt másolja, majd a VHDX-/ VHD töltenek fel egy lapblob, de a virtuális merevlemez átalakítása felügyelt lemez meghibásodik, az előkészítési tárfiókból. Nyissa meg az átmeneti **tárfiók > Blobok** , és válassza ki a megfelelő tárolót – Standard SSD, Standard HDD vagy prémium SSD-re. A virtuális merevlemezeket lapblobként az átmeneti tárfiókban lesznek feltöltve.

## <a name="erasure-of-data-from-data-box-heavy"></a>A Data Box nehéz törlését
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően. A törlés után is [töltse le a rendelési előzmények](data-box-portal-admin.md#download-order-history).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * Szállítási adatok Box nehéz a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * A Data Box nehéz törlését

Folytassa a következő cikkben megtudhatja, hogyan kezelheti a Data Box nehéz a helyi webes felhasználói felületen.

> [!div class="nextstepaction"]
> [Az Azure Data Box felügyelete a helyi webes felhasználói felülettel](./data-box-local-web-ui-admin.md)


