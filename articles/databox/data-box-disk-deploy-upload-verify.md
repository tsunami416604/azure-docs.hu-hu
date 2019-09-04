---
title: Oktatóanyag a Azure Data Box Diskról Storage-fiókba történő adatfeltöltés ellenőrzéséhez | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan ellenőrizheti a Azure Data Box Diskról az Azure Storage-fiókba feltöltött adatok ellenőrzését.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: a4d814ab5b1f26a6a2b871a850fd5e3153e256f5
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240277"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Oktatóanyag: Adatok feltöltésének ellenőrzése Azure Data Box Disk

Ez a sorozat utolsó oktatóanyaga: Azure Data Box Disk üzembe helyezése. Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Diskről

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta az [oktatóanyagot: Azure Data Box Disk](data-box-disk-deploy-picked-up.md)visszaadása.


## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Miután a futár felvette a lemezeket, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

![Lemezek felvéve](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Amikor a Microsoft megkapja és átvizsgálja a lemezt, a feladat állapota **Megérkezett** értékre vált. 

![Lemezek átvéve](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Az adatok másolása automatikusan megtörténik, amint a lemezeket csatlakoztatják a kiszolgálóhoz az Azure-adatközpontban. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

![Adatok másolása befejezve](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Ha a másolás hibákkal fejeződött be, tekintse meg a [feltöltési hibák elhárítása](data-box-disk-troubleshoot-upload.md)című témakört.

Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról. Az adatai a következőket vehetik fel:

- Azure Storage-fiókja (ke) t. A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- A felügyelt lemez erőforrás-csoportja (i). A felügyelt lemezek létrehozásakor a virtuális merevlemezeket blobként kell feltölteni, majd felügyelt lemezekre konvertáljuk. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoporthoz vannak csatolva.

  - Ha az Azure-beli felügyelt lemezekre történő másolás sikeres volt, lépjen a Azure Portal **rendelés részleteire** , és jegyezze fel a felügyelt lemezek számára megadott erőforráscsoportot.

      ![Megrendelés részleteinek megtekintése](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Nyissa meg a feljegyzett erőforráscsoportot, és keresse meg a felügyelt lemezeket.

      ![Felügyelt lemezek erőforráscsoport](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Ha egy VHDX vagy egy dinamikus/különbséglemezek VHD-t másolt, akkor a VHDX/VHD-t a rendszer egy blokk-blobként feltölti az átmeneti tárolási fiókba. Nyissa meg az átmeneti **tárolási fiókot > blobokat** , majd válassza ki a megfelelő StandardSSD, StandardHDD vagy PremiumSSD. A VHDX/VHD-k blokk-blobként jelennek meg az átmeneti tárolási fiókban.
  
::: zone-end

::: zone target="chromeless"

# <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Az adatok Azure-ba való feltöltése után ellenőrizze, hogy az adatok a Storage-fiók (ok) ban vannak-e, mielőtt törölné a forrásból. Az adatai a következőket vehetik fel:

- Azure Storage-fiókja (ke) t. A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

    - **A blobok és az oldal Blobok esetében**: https://< storage_account_name >. blob. Core. Windows<containername>. net//files/a.txt

    - **Azure Files esetén**: https://< storage_account_name >. file. Core. Windows. net/<sharename>/Files/a.txt

- A felügyelt lemez erőforrás-csoportja (i). A felügyelt lemezek létrehozásakor a virtuális merevlemezeket blobként kell feltölteni, majd felügyelt lemezekre konvertáljuk. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoporthoz vannak csatolva.

::: zone-end

Az alábbi lépéseket követve ellenőrizheti, hogy az adatfeltöltés az Azure-ba történt-e:

1. Lépjen a lemezrendeléshez kapcsolódó tárfiókra.
2. Lépjen a **Blob szolgáltatás > Blobok tallózása** elemre. Itt megjelenik a tárolók listája. A *BlockBlob* és *PageBlob* mappában létrehozott almappákhoz hasonlóan a tárfiókban azonos névvel ellátott tárolók jöttek létre.
    Ha a mappanevek nem felelnek meg az Azure elnevezési konvencióinak, az Azure-ba történő adatfeltöltés sikertelen lesz.

3. Ha szeretné ellenőrizni, hogy a teljes adatkészlet fel lett-e töltve, használja a Microsoft Azure Storage Explorert. Csatlakoztassa a Data Box Disk sorrendnek megfelelő Storage-fiókot, és tekintse meg a blob-tárolók listáját. Válasszon ki egy tárolót, kattintson a **Továbbiak**, majd a **Mappastatisztikák** lehetőségre. A **Tevékenységek** ablaktáblán megjelennek a mappára vonatkozó statisztikák (pl. a blobok száma és azok teljes mérete). A blobok teljes méretének meg kell egyeznie az adatkészlet méretével.

    ![A mappa statisztikái a Storage Explorerben](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Adatok törlése a Data Box Diskről

Miután befejeződött a másolás, és ellenőrizte, hogy az adat az Azure Storage-fiókban található, a lemezek biztonságosan törlődnek a NIST szabványnak megfelelően.

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




