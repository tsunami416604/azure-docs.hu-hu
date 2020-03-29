---
title: Az Azure IoT Edge-modul közzétételi áttekintést nyújt | Azure Piactér
description: Az IoT Edge-modulajánlat azure Marketplace-en való közzétételének folyamatának áttekintése.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: dsindona
ms.openlocfilehash: 6b44d9fd0bdd6e4b41013373472b5882a7ca1434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286591"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Az IoT Edge modul közzétételi áttekintést kínál

<table> <tr> <td>Ez a szakasz bemutatja, hogyan tehet közzé egy új Azure IoT Edge-modulajánlatot a Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Piactéren.</a> Az IoT Edge-modul egy Docker-kompatibilis tároló, amely ioT Edge-eszközön való futtatásra készült. Az Azure IoT Edge-modulok az IoT Edge által kezelt legkisebb számítási egység, és tartalmazhatnak Azure-szolgáltatásokat vagy egyéni megoldáskódot. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Közzétételi folyamat

Az IoT Edge-modulajánlat közzétételének magas szintű lépései a következők:

1. Az ajánlat létrehozása<br> Adjon részletes tájékoztatást az ajánlatról. Ez az információ a következőket tartalmazza: az ajánlat leírása, marketing anyagok, támogatási információk és az eszköz specifikációi.

2. Üzleti és műszaki eszközök létrehozása<br> Hozza létre az üzleti eszközöket (jogi dokumentumokat és marketinganyagokat) és a technikai eszközöket a kapcsolódó megoldáshoz (az IoT Edge modulrendszer-lemezképeket egy Azure Container Registry-ben.

3. A termékváltozat létrehozása<br> Hozza létre az ajánlathoz társított termékváltozat(oka)t. Minden közzétenni kívánt lemezképhez egyedi termékváltozat szükséges.

4. Az ajánlat hitelesítése és közzététele <br>Az ajánlat és a technikai eszközök befejezése után benyújthatja az ajánlatot. Ez a beküldés elindítja a közzétételi folyamatot. A folyamat során a megoldás tesztelt, érvényesített, hitelesített, majd "éles" az Azure Marketplace-en.

## <a name="parts-of-an-offer"></a>Az ajánlat részei

Az alábbi cikkek az IoT Edge-modulajánlat kulcsfontosságú részeit ismertetik.

- [Előfeltételek](./cpp-prerequisites.md) <br>Ez a cikk felsorolja a technikai és üzleti követelményeket, mielőtt létrehozna vagy közzétehetne egy IoT Edge-modulajánlatot.
- [IoT Edge modul technikai eszközeinek előkészítése](./cpp-create-technical-assets.md) <br>Ez a cikk ismerteti, hogyan készítse elő a technikai eszközöket egy IoT Edge-modul. Ezeknek az eszközöknek meg kell felelniük az összes szükséges technikai feltételnek, mielőtt az IoT Edge-modul közzétehető az Azure Marketplace-en.
- [IoT Edge-modulajánlat létrehozása](./cpp-create-offer.md) <br>Ez a cikk felsorolja az új IoT Edge-modul ajánlati bejegyzésének létrehozásához szükséges lépéseket a [Cloud Partner Portal](https://cloudpartner.azure.com)használatával.
- [IoT Edge-modulajánlat közzététele](./cpp-publish-offer.md)<br> Ez a cikk ismerteti, hogyan küldheti el az ajánlatot az Azure Marketplace-en való közzétételre.

## <a name="next-steps"></a>További lépések

Tekintse át az IoT Edge-modulok Microsoft Azure Piactéren való közzétételének [technikai és üzleti követelményeit.](./cpp-prerequisites.md)
