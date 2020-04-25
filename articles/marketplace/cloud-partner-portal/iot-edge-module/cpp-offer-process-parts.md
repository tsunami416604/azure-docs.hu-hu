---
title: Azure IoT Edge modul ajánlat közzétételének áttekintése | Azure piactér
description: IoT Edge modul Azure Marketplace-en való közzétételének folyamatának áttekintése.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: e9116e5cdb3bd9ed61205ceabd4d51c96c6aadc2
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144674"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge modul ajánlat közzétételének áttekintése

>[!Important]
>2020. április 13-ától kezdődően megkezdjük a IoT Edge modul-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [IoT Edge modul létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) című témakör utasításait.

<table> <tr> <td>Ez a szakasz azt ismerteti, hogyan tehet közzé új Azure IoT Edge modult a Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>-en. Az IoT Edge modul egy IoT Edge eszközön futtatható Docker-kompatibilis tároló. A Azure IoT Edge modulok a IoT Edge által kezelt számítási egységek legkisebb egységei, és tartalmazhatnak Azure-szolgáltatásokat vagy egyéni megoldási kódokat. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Közzétételi folyamat

IoT Edge Modulos ajánlat közzétételének magas szintű lépései a következők:

1. Ajánlat létrehozása<br> Részletes információkat adhat meg az ajánlatról. Ezek az adatok a következők: az ajánlat leírása, a marketing-anyagok, a támogatási információk és az eszközök specifikációja.

2. Üzleti és technikai eszközök létrehozása<br> Hozza létre a kapcsolódó megoldáshoz tartozó üzleti eszközöket (jogi dokumentumok és marketinganyagok) és technikai eszközöket (a IoT Edge modul rendszerképeit Azure Container Registry.

3. Az SKU létrehozása<br> Hozza létre az ajánlathoz társított SKU (ka) t. Minden közzétenni kívánt rendszerképhez egyedi SKU szükséges.

4. Az ajánlat tanúsítása és közzététele <br>Az ajánlat és a technikai eszközök befejezése után elküldheti az ajánlatot. Ez a Küldés elindítja a közzétételi folyamatot. A folyamat során a megoldás tesztelése, ellenőrzése, minősítése, majd az Azure piactéren az "élő" állapot érhető el.

## <a name="parts-of-an-offer"></a>Egy ajánlat részei

A következő cikkek egy IoT Edge modul ajánlatának főbb részeit fedik le.

- [Előfeltételek](./cpp-prerequisites.md) <br>Ez a cikk a technikai és üzleti követelményeket sorolja fel, mielőtt IoT Edge modul-ajánlatot hozna létre vagy tehet közzé.
- [IoT Edge-modulok technikai eszközeinek előkészítése](./cpp-create-technical-assets.md) <br>Ez a cikk bemutatja, hogyan készítheti elő a technikai eszközöket egy IoT Edge modulhoz. Ezeknek az eszközöknek meg kell felelniük az összes szükséges technikai feltételnek, mielőtt közzé lehetne tenni az IoT Edge modult az Azure piactéren.
- [IoT Edge-modulajánlat létrehozása](./cpp-create-offer.md) <br>Ez a cikk azokat a lépéseket sorolja fel, amelyek szükségesek az új IoT Edge modul ajánlati bejegyzés létrehozásához a [Cloud Partner Portal](https://cloudpartner.azure.com)használatával.
- [IoT Edge-modulajánlat közzététele](./cpp-publish-offer.md)<br> Ez a cikk azt ismerteti, hogyan lehet elküldeni az ajánlatot közzétételre az Azure piactéren.

## <a name="next-steps"></a>További lépések

Tekintse át a IoT Edge modulnak a Microsoft Azure Marketplace való közzétételének [technikai és üzleti követelményeit](./cpp-prerequisites.md) .
