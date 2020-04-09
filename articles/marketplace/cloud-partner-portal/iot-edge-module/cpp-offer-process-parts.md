---
title: Az Azure IoT Edge-modul közzétételi áttekintést nyújt | Azure Piactér
description: Az IoT Edge-modulajánlat azure Marketplace-en való közzétételének folyamatának áttekintése.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 20c76cec82944568c1b16694bef2838626b90b03
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983347"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Az IoT Edge modul közzétételi áttekintést kínál

>[!Important]
>2020. április 13-tól megkezdjük az IoT Edge modulajánlatok kezelését a Partner Centerbe. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [IoT Edge-modulajánlat létrehozása](https://aka.ms/AzureCreateIoT) az áttelepített ajánlatok kezeléséhez kövesse az utasításokat.

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
- [IoT Edge-modulok technikai eszközeinek előkészítése](./cpp-create-technical-assets.md) <br>Ez a cikk ismerteti, hogyan készítse elő a technikai eszközöket egy IoT Edge-modul. Ezeknek az eszközöknek meg kell felelniük az összes szükséges technikai feltételnek, mielőtt az IoT Edge-modul közzétehető az Azure Marketplace-en.
- [IoT Edge-modulajánlat létrehozása](./cpp-create-offer.md) <br>Ez a cikk felsorolja az új IoT Edge-modul ajánlati bejegyzésének létrehozásához szükséges lépéseket a [Cloud Partner Portal](https://cloudpartner.azure.com)használatával.
- [IoT Edge-modulajánlat közzététele](./cpp-publish-offer.md)<br> Ez a cikk ismerteti, hogyan küldheti el az ajánlatot az Azure Marketplace-en való közzétételre.

## <a name="next-steps"></a>További lépések

Tekintse át az IoT Edge-modulok Microsoft Azure Piactéren való közzétételének [technikai és üzleti követelményeit.](./cpp-prerequisites.md)
