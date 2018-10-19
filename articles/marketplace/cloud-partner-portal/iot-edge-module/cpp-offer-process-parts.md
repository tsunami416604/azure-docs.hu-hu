---
title: Az Azure IoT Edge-modul az ajánlat közzététele – áttekintés |} A Microsoft Docs
description: Egy IoT Edge-modul közzétételéhez a folyamat áttekintése az Azure Marketplace-en kínálnak.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 68c58c16d7083182d412adb6ed0d243a3291cc42
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431104"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge-modul ajánlat közzétételi áttekintése

Ez a cikk ismerteti a közzétételi folyamat és a egy IoT Edge-modul ajánlatot legfontosabb elemei. Ez a cikk használhatja kiindulási pontként való közzététel az ajánlat közzétételéhez a [Azure Marketplace-en](https://azuremarketplace.microsoft.com).

## <a name="publishing-process"></a>Közzétételi folyamat

Az IoT Edge-modul ajánlat közzétételéhez a magas szintű lépései a következők:

1. Az ajánlat létrehozása<br> Az ajánlat részletes adatainak megadása. Ezen információk közé tartozik: az ajánlat leírása, marketing-adategység-specifikációi, anyagokat és támogatási információk.

2. Az üzleti és technikai eszközök létrehozása<br> Hozza létre a vállalati eszközök (jogi dokumentumok és marketinganyagokat) és a társított (az IoT Edge modul képek egy Azure Container Registry lévő üzemeltetett megoldás technikai eszközök.

3. A Termékváltozat létrehozása<br> Hozza létre a termékváltozat(ok) társított ajánlat. Egy egyedi Termékváltozat megadása kötelező az egyes lemezképek szeretné közzétenni.

4. Igazolja, és az ajánlat közzététele <br>Az ajánlat és a technikai eszközök befejezése után elküldheti az ajánlatot. Az elküldés során először a közzétételi folyamat. A folyamat során a megoldás vizsgálják, érvényesíti, minősített, majd "élesíti" az Azure Marketplace-en.

## <a name="parts-of-an-offer"></a>Az ajánlat részeit

A következő cikkek az IoT Edge-modul ajánlat fő részét tárgyalja.

- [Előfeltételek](./cpp-prerequisites.md) <br>Ez a cikk felsorolja a műszaki és üzleti követelmények létrehozása vagy egy IoT Edge-modul közzététele előtt kínálnak.
- [IoT Edge-modul technikai eszközök előkészítése](./cpp-create-technical-assets.md) <br>Ez a cikk ismerteti, hogyan készülhet fel a technikai eszközök IoT Edge-modul. Ezeknek az eszközöknek meg kell felelnie az összes szükséges technikai feltétel, ahhoz az IoT Edge-modul az Azure piactéren közzétehető.
- [IoT Edge-modulajánlat létrehozása](./cpp-create-offer.md) <br>Ez a cikk felsorolja azokat a lépéseket, hozzon létre egy új IoT Edge modul ajánlat bejegyzés szükséges az [Cloud Partner Portalon](https://cloudpartner.azure.com).
- [IoT Edge-modul ajánlat közzététele](./cpp-publish-offer.md)<br> Ez a cikk ismerteti, hogyan lehet elküldeni az ajánlat közzététele az Azure Marketplace-en.

## <a name="next-steps"></a>További lépések

Tekintse át a [műszaki és üzleti igényeinek](./cpp-prerequisites.md) egy IoT Edge-modul a Microsoft Azure piactéren való közzétételéhez.
