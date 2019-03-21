---
title: Microsoft Azure Data Box visszaküldése | Microsoft Docs
description: Megtudhatja, hogyan küldheti vissza az Azure Data Boxot a Microsofthoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 72d6ce58a986ddd0d0976d99de5ca3426d78f0b9
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287162"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Oktatóanyag: Vissza az Azure Data Box, és ellenőrizze az adatok feltöltése az Azure-bA

Az oktatóanyagból megtudhatja, hogyan küldheti vissza az Azure Data Boxot, és ellenőrizheti az adatok az Azure-ba való feltöltését.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

- Már elvégezte a [oktatóanyag: Adatok másolása az Azure Data Box, és ellenőrizze](data-box-deploy-copy-data.md). 
- Másolás feladatok elkészültek. Szállításra való nem futtatható, ha a másolási feladat folyamatban van.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Data Box visszaküldése

1. Ellenőrizze, hogy az eszköz ki van-e kapcsolva, és a kábelek el lettek-e távolítva. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
2. Győződjön meg arról, hogy a fuvarlevélcímke látható az E-ink kijelzőn, és egyeztesse a csomagfelvételt a szállítóval. Ha a címke megsérült vagy elveszett vagy nem jelenik meg az E-ink megjelenő, forduljon a Microsoft Support. A támogatási javasol, akkor elvégezheti **áttekintés > levélcímke letöltése** az Azure Portalon. Töltse le a szállítási címkét, és elhelyezi az eszközön. 
3. Ha az eszközök visszatérő, ütemezhet a UPS begyűjtést. Ütemezhet begyűjtést:

    - Hívja meg a helyi UPS (országspecifikus díjmentesen ingyenes szám).
    - A hívás az ajánlat a fordított szállítmány követési szám az E-ink megjelenítési vagy a nyomtatott felirat látható módon.
    - A nyomkövetési azonosító szám není uveden v uvozovkách, ha a UPS kell, hogy külön díj fizessen a begyűjtés során.

    Ahelyett, hogy a begyűjtés ütemezés is húzhatja a Data Box, a legközelebbi gyűjtőhely ki.
4. Miután a futár felvette és beolvasta a Data Boxot, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Amikor a Microsoft megkapja és beolvassa az eszközt, a rendelés **Megérkezett** állapotra vált. Az eszközt ezután fizikai ellenőrzésnek vetjük alá, sérüléseket vagy illetéktelen hozzáférés jeleit kutatva.

Az ellenőrzés végeztével a Data Boxot csatlakoztatjuk a hálózatra az Azure-adatközpontban. Az adatok másolása automatikusan megkezdődik. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

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

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

A következő cikk a Data Box a helyi webes felületen keresztül történő felügyeletét mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box felügyelete a helyi webes felhasználói felülettel](./data-box-local-web-ui-admin.md)


