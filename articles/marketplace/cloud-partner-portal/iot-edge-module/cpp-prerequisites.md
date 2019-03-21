---
title: Az Azure IoT Edge-modul előfeltételei |} A Microsoft Docs
description: Előfeltételek az IoT Edge-modul közzétételéhez.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: a4f1023bdf8a49fccbbda1fd0dc537f83a3acee1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013814"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge-modul közzétételi Előfeltételek

Ez a cikk ismerteti az előfeltételek az IoT Edge-modul ajánlat közzétételéhez.  Ha még nem tette meg, tekintse át a [közzétételi útmutatóban IoT Edge-modulok](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Közzétételi Előfeltételek

Egy IoT Edge-modul az Azure piactéren való közzétételéhez rendelkeznie megfelelnek a következő előfeltételeknek:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- A hozzáférést a [Cloud Partner Portalon](https://cloudpartner.azure.com/). További információkért lásd: [közzétételi útmutató az Azure Marketplace és AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- A szerződés a [Azure Marketplace használati feltételei](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Az IoT Edge modul műszaki objektumot egy Azure Container Registry a gazdagép.  További információkért lásd: [az IoT Edge-modul műszaki eszköz előkészítése](./cpp-create-technical-assets.md)
- Metaadatoknak az IoT Edge modul készen áll a használatra. Ha például előkészítése az alábbi eszközöket:
    - Cím
    - Egy leírást (HTML formátumban)
    - Emblémakép (PNG formátumú és 40x40px, 90x90px, 115x115px, 255x115px többek között a rögzített lemezkép mérete)
    - Egy kifejezés feltételei és adatvédelmi szabályzat
    - Egy alapértelmezett modul konfigurációja, amely tartalmazza: útvonalakat, ikereszköz kívánt tulajdonságait, createOptions és környezeti változókat.
    - A modul dokumentációja
    - Támogatási kapcsolattartók


## <a name="next-steps"></a>További lépések

Miután [az IoT Edge-modul műszaki eszköz előkészítve](./cpp-create-technical-assets.md), készen áll a [az IoT Edge-modul ajánlat létrehozása](./cpp-create-offer.md). 
