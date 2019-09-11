---
title: Oktatóanyag az Azure Data Box Diskről a tárfiókra való adatfeltöltés ellenőrzéséhez | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan ellenőrizheti az Azure Data Box Diskről az Azure-tárfiókra feltöltött adatokat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/04/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: fc3145ee0b60402026389863b94d21da4b3e4123
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70307762"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Oktatóanyag: Az Azure Data Box Diskről végzett adatfeltöltés ellenőrzése

Ez a következő sorozat utolsó oktatóanyaga: Az Azure Data Box Disk üzembe helyezése. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Diskről

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, mindenképp végezze el az [ Az Azure Data Box Disk visszaküldése](data-box-disk-deploy-picked-up.md) című oktatóanyagot.


## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Miután a futár felvette a lemezeket, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

![Lemezek felvéve](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Amikor a Microsoft megkapja és átvizsgálja a lemezt, a feladat állapota **Megérkezett** értékre vált. 

![Lemezek átvéve](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Az adatok másolása automatikusan megtörténik, amint a lemezeket csatlakoztatják a kiszolgálóhoz az Azure-adatközpontban. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

![Adatok másolása befejezve](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Ha a másolás hibákkal fejeződik be, tekintse meg a [feltöltési hibák elhárításával](data-box-disk-troubleshoot-upload.md) kapcsolatos szakaszt.

Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról. Az adatok a következőkben lehetnek:

- Az Ön Azure Storage-fiókja(i). A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- Az Ön felügyelt lemezéhez csatolt erőforráscsoport(ok). Felügyelt lemezek létrehozásakor a VHD-k lapblobokként töltődnek fel, és ezután alakítja át őket a rendszer felügyelt lemezekké. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoportokhoz vannak csatolva.

  - Ha az Azure-ban a felügyelt lemezekre másolás sikeres volt, az Azure Portalon a **Megrendelések részletei** területre léphet, és feljegyezheti a felügyelt lemezekhez meghatározott erőforráscsoportot.

      ![Megrendelés részleteinek megtekintése](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Lépjen a feljegyzett erőforráscsoportra, és keresse meg a felügyelt lemezeket.

      ![Felügyelt lemezek erőforráscsoportja](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Ha egy VHDX-et vagy egy dinamikus/különbséglemez VHD-t másolt, akkor a VHDX/VHD-t a rendszer blokkblobként tölti fel az előkészítési tárfiókba. Lépjen az előkészítési **Tárfiók > Blobok** területre, majd válassza ki a megfelelő tárolót: StandardSSD, StandardHDD vagy PremiumSSD. A VHDX/VHD-knek blokkblobként kell megjelenniük az előkészítési tárfiókban.
  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Az Azure-ba való feltöltés után ellenőrizze, hogy az adatok megtalálhatók-e a tárfiók(ok)ban, mielőtt törölné őket a forrásról. Az adatok a következőkben lehetnek:

- Az Ön Azure Storage-fiókja(i). A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

    - **Blokkblobok és lapblobok esetében**: https://<tárfiók_neve>.blob.core.windows.net/<containername>/files/a.txt

    - **Azure Files esetében**: https://<tárfiók_neve>.file.core.windows.net/<sharename>/files/a.txt

- Az Ön felügyelt lemezéhez csatolt erőforráscsoport(ok). Felügyelt lemezek létrehozásakor a VHD-k lapblobokként töltődnek fel, és ezután alakítja át őket a rendszer felügyelt lemezekké. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoportokhoz vannak csatolva.

::: zone-end

Az adatok Azure-ba történő feltöltését az alábbi lépésekkel ellenőrizheti:

1. Lépjen a lemezrendeléshez kapcsolódó tárfiókra.
2. Lépjen a **Blob szolgáltatás > Blobok tallózása** elemre. Itt megjelenik a tárolók listája. A *BlockBlob* és *PageBlob* mappában létrehozott almappákhoz hasonlóan a tárfiókban azonos névvel ellátott tárolók jöttek létre.
    Ha a mappanevek nem felelnek meg az Azure elnevezési konvencióinak, az Azure-ba történő adatfeltöltés sikertelen lesz.

3. Ha szeretné ellenőrizni, hogy a teljes adatkészlet fel lett-e töltve, használja a Microsoft Azure Storage Explorert. Csatolja a Data Box Disk megrendeléséhez tartozó tárfiókot, majd tekintse meg a blobtárolók listáját. Válasszon ki egy tárolót, kattintson a **Továbbiak**, majd a **Mappastatisztikák** lehetőségre. A **Tevékenységek** ablaktáblán megjelennek a mappára vonatkozó statisztikák (pl. a blobok száma és azok teljes mérete). A blobok teljes méretének meg kell egyeznie az adatkészlet méretével.

    ![A mappa statisztikái a Storage Explorerben](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Adatok törlése a Data Box Diskről

Miután a másolás befejeződött, és meggyőződött róla, hogy az adatok megtalálhatók az Azure Storage-fiókban, a lemezekről az NIST szabványnak megfelelően minden adat biztonságosan törlődik.

::: zone target="docs"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Diskkel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Diskről


A következő oktatóanyag a Data Box Diskek Azure Portalon keresztül történő felügyeletét mutatja be.

> [!div class="nextstepaction"]
> [Azure Data Box Diskek kezelése az Azure Portal segítségével](./data-box-portal-ui-admin.md)

::: zone-end




