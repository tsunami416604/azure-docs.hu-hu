---
title: Az Azure Data Box Heavy visszaküldését ismertető oktatóanyag | Microsoft Docs
description: Megtudhatja, hogyan küldheti vissza az Azure Data Box Heavyt a Microsoftnak
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: cfb9f54f5ba219a4db87144ab1e7ebff2b72b69e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514442"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Oktatóanyag: Az Azure Data Box Heavy visszaküldése, majd az Azure-ba történő adatfeltöltés ellenőrzése

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Az Azure Data Box Heavy visszaküldése, majd az Azure-ba történő adatfeltöltés ellenőrzése

::: zone-end

::: zone target = "docs"

Az oktatóanyagból megtudhatja, hogyan küldheti vissza az Azure Data Box Heavyt, és ellenőrizheti az adatok Azure-ba való feltöltését.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box Heavy elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Heavyről

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az [ Adatok másolása az Azure Data Boxra, majd ellenőrzése](data-box-heavy-deploy-copy-data.md) lépéseit.
- A másolási feladatok befejeződtek. A szállítás előkészítése nem futtatható, ha másolási feladatok vannak folyamatban.


## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>A szállítás előkészítése

A szállítás előkészítése előtt győződjön meg arról, hogy a másolási feladatok befejeződtek.

1. Nyissa meg a helyi webes kezelőfelület Prepare to ship (Szállítás előkészítése) lapját, és kezdje meg az előkészületeket a szállításra.
2. Kapcsolja ki az eszközt a helyi webes kezelőfelületen. Válassza le a kábeleket az eszközről.

Most már készen áll az eszköz visszaküldésére.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>A Data Box Heavy visszaküldése

1. Ellenőrizze, hogy az eszköz ki van-e kapcsolva, és a kábelek el lettek-e távolítva. Tekerje fel és biztonságosan helyezze el a 4 tápkábelt az eszköz hátuljáról elérhető tálcán.
2. Az eszköz a FedExszel küldhető el az USA-ban és DHL-lel az EU-ban

    1. Lépjen kapcsolatba a [Data Box üzemeltetési csapatával](mailto:DataBoxOps@microsoft.com) a csomagfelvétellel kapcsolatos információk megadása és a visszaküldési fuvarlevélcímke beszerzése érdekében.
    2. Hívja a futárcég helyi telefonszámát a csomagfelvétel ütemezéséhez.
    3. Ügyeljen arra, hogy a fuvarlevélcímke jól látható legyen a csomag külsején.
    4. Győződjön meg arról, hogy az előző szállításból megmaradt régi fuvarlevélcímkéket eltávolította az eszközről.
3. Miután a futár felvette és beolvasta a Data Box Heavyt, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Amikor a Microsoft megkapja és beolvassa az eszközt, a rendelés **Megérkezett** állapotra vált. Az eszközt ezután fizikai ellenőrzésnek vetjük alá, sérüléseket vagy illetéktelen hozzáférés jeleit kutatva.

Az ellenőrzés végeztével a Data Box Heavyt csatlakoztatjuk a hálózatra az Azure-adatközpontban. Az adatok másolása automatikusan megkezdődik. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

Az adatok forrásból történő törlése előtt ellenőrizze, hogy fel lettek-e töltve az Azure-ba. Az adatok a következőkben lehetnek:

- Az Ön Azure Storage-fiókja(i). A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- Az Ön felügyelt lemezéhez csatolt erőforráscsoport(ok). Felügyelt lemezek létrehozásakor a VHD-k lapblobokként töltődnek fel, és ezután alakítja át őket a rendszer felügyelt lemezekké. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoportokhoz vannak csatolva. 

    - Ha az Azure-ban a felügyelt lemezekre történő másolás sikeres volt, akkor az Azure Portalon a **Rendelés részletei** területre léphet, és feljegyezheti a felügyelt lemezekhez megadott erőforráscsoportokat.

        ![Felügyelt lemezekhez csatolt erőforráscsoportok azonosítása](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Lépjen a feljegyzett erőforráscsoportra, és keresse meg a felügyelt lemezeket.

        ![Erőforráscsoportokhoz csatolt felügyelt lemez](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Ha egy VHDX-fájlt, illetve egy dinamikus VHD-t vagy különbséglemezt másolt, akkor a VHDX-/VHD-fájlok lapblobként lesznek töltődnek fel az előkészítési tárfiókba, azonban a VHD-fájlok felügyelt lemezekké történő átalakítása meghiúsul. Lépjen az előkészítési **Tárfiók > Blobok** területre, majd válassza ki a megfelelő tárolót: – Standard SSD, Standard HDD vagy Prémium SSD. A VHD-fájlok lapblobként töltődnek fel az előkészítési tárfiókba.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Amikor csatlakoztatja a Data Box Heavy eszközt az Azure-adatközpont hálózatához, az adatok Azure-ba való feltöltése automatikusan megkezdődik. A Data Box szolgáltatás az Azure Portalon értesíti, ha az adatok másolása befejeződött.

- Ellenőrizze a hibákat a hibanaplókban, és tegye meg a szükséges intézkedéseket.
- Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Adatok törlése a Data Box Heavyről
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően. A törlés befejezése után [letöltheti a rendelési előzményeket](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box Heavy elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Heavyről

A következő cikk a Data Box Heavy helyi webes felületen keresztül történő felügyeletét mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box felügyelete a helyi webes felhasználói felülettel](./data-box-local-web-ui-admin.md)

::: zone-end


