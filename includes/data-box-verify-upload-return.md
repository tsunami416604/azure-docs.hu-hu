---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/19/2019
ms.author: alkohli
ms.openlocfilehash: a23b0b2c71207bf84a4938d54a78a62efb6cbcbd
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172633"
---
Amikor a Microsoft megkapja és beolvassa az eszközt, a rendelés **Megérkezett** állapotra vált. Az eszközt ezután fizikai ellenőrzésnek vetjük alá, sérüléseket vagy illetéktelen hozzáférés jeleit kutatva.

Az ellenőrzés végeztével a Data Boxot csatlakoztatjuk a hálózatra az Azure-adatközpontban. Az adatok másolása automatikusan megkezdődik. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

Az adatok forrásból történő törlése előtt ellenőrizze, hogy fel lettek-e töltve az Azure-ba. Az adatok a következőkben lehetnek:

- Az Ön Azure Storage-fiókja(i). A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- Az Ön felügyelt lemezéhez csatolt erőforráscsoport(ok). Felügyelt lemezek létrehozásakor a VHD-k lapblobokként töltődnek fel, és ezután alakítja át őket a rendszer felügyelt lemezekké. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoportokhoz vannak csatolva. 

    - Ha az Azure-ban a felügyelt lemezekre történő másolás sikeres volt, akkor az Azure Portalon a **Rendelés részletei** területre léphet, és feljegyezheti a felügyelt lemezekhez megadott erőforráscsoportokat.

        ![Felügyelt lemezekhez csatolt erőforráscsoportok azonosítása](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        Lépjen a feljegyzett erőforráscsoportra, és keresse meg a felügyelt lemezeket.

        ![Erőforráscsoportokhoz csatolt felügyelt lemez](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - Ha egy VHDX-fájlt, illetve egy dinamikus VHD-t vagy különbséglemezt másolt, akkor a VHDX-/VHD-fájlok lapblobként lesznek töltődnek fel az előkészítési tárfiókba, azonban a VHD-fájlok felügyelt lemezekké történő átalakítása meghiúsul. Lépjen az előkészítési **Tárfiók > Blobok** területre, majd válassza ki a megfelelő tárolót: – Standard SSD, Standard HDD vagy Prémium SSD. A VHD-fájlok lapblobként töltődnek fel az előkészítési tárfiókba.


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
> [Az Azure Data Box felügyelete a helyi webes felhasználói felülettel](../articles/databox/data-box-local-web-ui-admin.md)