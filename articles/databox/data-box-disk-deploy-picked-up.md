---
title: Microsoft Azure Data Box Disk visszaküldése | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan küldheti vissza Azure Data Box Diskjét a Microsofthoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 357fa8a34afc8b426d308940462e22895130169f
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158771"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Oktatóanyag: Vissza az Azure Data Box-lemezek, és ellenőrizze az adatok feltöltése az Azure-bA

Ez az a sorozat utolsó oktatóanyaga: Helyezze üzembe az Azure Data Box-lemezek. Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A Data Box Disk elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Diskről

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta a [oktatóanyag: Adatok másolása az Azure Data Box-lemezek, és ellenőrizze](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Data Box Disk visszaküldése

1. Miután az adatok érvényesítése befejeződött, válassza le a lemezeket. Távolítsa el a csatlakoztatott kábeleket.
2. Csomagolja be az összes lemezt és kábelt buborékfóliába, és helyezze őket a szállítási dobozba.
3. Helyezze fel a fuvarlevélcímkét, amelyet a dobozhoz rögzített átlátszó műanyagzsebben talál. Ha a címke sérült vagy elveszett, töltsön le egy új fuvarlevélcímkét az Azure Portalról, és rögzítse doboz külsejére. Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra. 

    ![Fuvarlevélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    Ez a művelet egy, az alábbiakban is látható, a visszaküldéshez szükséges fuvarlevélcímkét tölt le.

    ![Példa a fuvarlevélcímkére](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. Zárja le a szállítási dobozt, és győződjön meg arról, hogy a visszaküldési fuvarlevélcímke jól látható.
5. Egyeztessen egy csomagfelvételi időpontot a UPS-szel, ha az eszközt az Egyesült Államokban szeretné visszaküldeni. Ha az eszközt Európán belül, DHL-lel szeretné visszaküldeni, látogasson el a DHL webhelyére, kérjen csomagfelvételt, és adja meg a légi fuvarlevél számát. Lépjen a DHL Express webhelyére, és válassza az **Online futárrendelés > Visszáru online megrendelése** lehetőséget.

    ![DHL – Visszáru online megrendelése](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    Adja meg a fuvarlevél számát, és kattintson a **Futárrendelés** gombra a csomagfelvétel lefoglalásához.

      ![Csomagfelvétel ütemezése](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. Miután a futár felvette a lemezeket, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

    ![Lemezek felvéve](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Amikor a Microsoft megkapja és átvizsgálja a lemezt, a feladat állapota **Megérkezett** értékre vált. 

![Lemezek átvéve](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Az adatok másolása automatikusan megtörténik, amint a lemezeket csatlakoztatják a kiszolgálóhoz az Azure-adatközpontban. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

![Adatok másolása befejezve](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról. Az adatok Azure-ba történő feltöltését az alábbi lépésekkel ellenőrizheti:

1. Lépjen a lemezrendeléshez kapcsolódó tárfiókra.
2. Lépjen a **Blob szolgáltatás > Blobok tallózása** elemre. Itt megjelenik a tárolók listája. A *BlockBlob* és *PageBlob* mappában létrehozott almappákhoz hasonlóan a tárfiókban azonos névvel ellátott tárolók jöttek létre.
    Ha a mappanevek nem felelnek meg az Azure elnevezési konvencióinak, az Azure-ba történő adatfeltöltés sikertelen lesz.

4. Ha szeretné ellenőrizni, hogy a teljes adatkészlet fel lett-e töltve, használja a Microsoft Azure Storage Explorert. Csatlakoztassa a lemezbérlési rendeléshez tartozó tárfiókot, majd tekintse meg a blobtárolók listáját. Válasszon ki egy tárolót, kattintson a **Továbbiak**, majd a **Mappastatisztikák** lehetőségre. A **Tevékenységek** ablaktáblán megjelennek a mappára vonatkozó statisztikák (pl. a blobok száma és azok teljes mérete). A blobok teljes méretének meg kell egyeznie az adatkészlet méretével.

    ![A mappa statisztikái a Storage Explorerben](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Adatok törlése a Data Box Diskről

Miután a másolás befejeződött, és meggyőződött róla, hogy az adatok megtalálhatók az Azure Storage-fiókban, a lemezekről az NIST szabványnak megfelelően minden adat biztonságosan törlődik. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Diskkel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * A Data Box Disk elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Diskről


A következő oktatóanyag a Data Box Diskek Azure Portalon keresztül történő felügyeletét mutatja be.

> [!div class="nextstepaction"]
> [Azure Data Box Diskek kezelése az Azure Portal segítségével](./data-box-portal-ui-admin.md)


