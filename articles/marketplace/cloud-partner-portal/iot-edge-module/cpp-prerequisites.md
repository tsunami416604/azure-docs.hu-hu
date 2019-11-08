---
title: Azure IoT Edge modul előfeltételei | Azure piactér
description: IoT Edge modul közzétételének előfeltételei.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 610f9bc8d87ac75e2868e247fe1332574480b498
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813862"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge modul közzétételi előfeltételei

Ez a cikk a IoT Edge modul ajánlatának közzétételének előfeltételeit ismerteti.  Ha még nem tette meg, tekintse át a [IoT Edge-modulok közzétételi útmutatóját](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Közzétételi előfeltételek

IoT Edge modul Azure Marketplace-en való közzétételéhez meg kell felelnie a következő előfeltételeknek:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Hozzáférés a [Cloud Partner Portalhoz](https://cloudpartner.azure.com/). További információ: az [Azure Marketplace és a AppSource közzétételi útmutatója](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Az [Azure Marketplace használati feltételeinek](https://azure.microsoft.com/support/legal/marketplace-terms/) megkötése
- IoT Edge modul technikai eszközének üzemeltetése egy Azure Container Registry.  További információ: [a IoT Edge modul technikai eszközének előkészítése](./cpp-create-technical-assets.md)
- A IoT Edge modul metaadatai használatra készek. Például készítse elő a következő eszközöket:
    - Egy cím
    - Leírás (HTML formátumban)
    - Egy embléma képe (PNG-formátum és rögzített képméret, beleértve az 40x40px, a 90x90px, a 115x115px, a 255x115px)
    - Használati feltételek és adatvédelmi szabályzat
    - Alapértelmezett modul-konfiguráció, amely a következőket tartalmazza: útvonalak, dupla kívánt tulajdonságok, createOptions és környezeti változók.
    - Modul dokumentációja
    - Támogatási kapcsolattartók


## <a name="next-steps"></a>További lépések

Miután [előkészítette a IoT Edge modul technikai eszközét](./cpp-create-technical-assets.md), készen áll a [IoT Edge modul-ajánlat létrehozására](./cpp-create-offer.md). 
