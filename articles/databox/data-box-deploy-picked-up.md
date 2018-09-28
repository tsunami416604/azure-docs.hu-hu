---
title: Microsoft Azure Data Box visszaküldése | Microsoft Docs
description: Megtudhatja, hogyan küldheti vissza az Azure Data Boxot a Microsofthoz.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: d649095a6b1b9f692a6795e96c9f15631d36e3e2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974510"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Oktatóanyag: Azure Data Box visszaküldése, majd az Azure-ba történő adatfeltöltés ellenőrzése

Az oktatóanyagból megtudhatja, hogyan küldheti vissza az Azure Data Boxot, és ellenőrizheti az adatok az Azure-ba való feltöltését.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg arról, hogy elvégezte az [adatok az Azure Data Boxra történő másolását, majd azok ellenőrzését ismertető oktatóanyagot](data-box-deploy-copy-data.md).

## <a name="ship-data-box-back"></a>Data Box visszaküldése

1. Ellenőrizze, hogy az eszköz ki van-e kapcsolva, és a kábelek el lettek-e távolítva. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
2. Győződjön meg arról, hogy a fuvarlevélcímke látható az E-ink kijelzőn, és egyeztesse a csomagfelvételt a szállítóval. Ha a címke sérült, elveszett vagy nem jelenik meg az E-ink kijelzőn, töltse le a fuvarlevélcímkét az Azure Portalról, és rögzítse az eszközre. Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra.
3. Egyeztessen egy csomagfelvételi időpontot a UPS-szel, ha az eszközt az Egyesült Államokban szeretné visszaküldeni. Ha az eszközt Európán belül, DHL-lel szeretné visszaküldeni, látogasson el a DHL webhelyére, kérjen csomagfelvételt, és adja meg a légi fuvarlevél számát. Lépjen a DHL Express webhelyére, és válassza az **Online futárrendelés > Visszáru online megrendelése** lehetőséget. 

    Adja meg a fuvarlevél számát, és kattintson a **Futárrendelés** gombra a csomagfelvétel lefoglalásához.

4. Miután a futár felvette és beolvasta a Data Boxot, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Amikor a Microsoft megkapja és beolvassa az eszközt, a rendelés **Megérkezett** állapotra vált. Az eszközt ezután fizikai ellenőrzésnek vetjük alá, sérüléseket vagy illetéktelen hozzáférés jeleit kutatva. 

Az ellenőrzés végeztével a Data Boxot csatlakoztatjuk a hálózatra az Azure-adatközpontban. Az adatok másolása automatikusan megkezdődik. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról. 

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
 Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

A következő cikk a Data Box a helyi webes felületen keresztül történő felügyeletét mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box felügyelete a helyi webes felhasználói felülettel](./data-box-local-web-ui-admin.md)


