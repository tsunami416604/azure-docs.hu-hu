---
title: Az Azure IoT Edge-modul előfeltételei |} A Microsoft Docs
description: Előfeltételek az IoT Edge-modul közzétételéhez.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.openlocfilehash: 7271a97dc7ab9de1840d809ded0ba1c2940ed83f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431168"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge-modul közzétételi Előfeltételek

Ez a cikk ismerteti az előfeltételek az IoT Edge-modul ajánlat közzétételéhez.

IoT Edge-modulok és a egy modul az Azure piactéren való közzétételéhez előnyeinek kapcsolatos további információkért tekintse meg a [közzétételi útmutatóban IoT Edge-modulok](https://docs.microsoft.com/azure/marketplace/iot-edge-module).

## <a name="publishing-prerequisites"></a>Közzétételi Előfeltételek

Egy IoT Edge-modul az Azure piactéren való közzétételéhez rendelkeznie megfelelnek a következő előfeltételeknek:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- A hozzáférést a [Cloud Partner Portalon](https://cloudpartner.azure.com/). További információkért lásd: [közzétételi útmutató az Azure Marketplace és AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- A szerződés a [Azure Marketplace használati feltételei](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Az IoT Edge modul műszaki objektumot egy Azure Container Registry a gazdagép.  További információkért lásd: [az IoT Edge-modul műszaki eszköz előkészítése](./cpp-create-technical-assets.md)
- Metaadatoknak az IoT Edge modul készen áll a használatra. Ha például (nem teljes lista):
    - Cím
    - Egy leírást (HTML formátumban)
    - Emblémakép (PNG formátumú és 40x40px, 90x90px, 115x115px, 255x115px többek között a rögzített lemezkép mérete)
    - Egy kifejezés feltételei és adatvédelmi szabályzat
    - Egy alapértelmezett modul konfigurációja, amely tartalmazza: útvonalakat, ikereszköz kívánt tulajdonságait, createOptions és környezeti változókat.
    - Dokumentáció
    - Támogatási kapcsolattartók

## <a name="next-steps"></a>További lépések

- [Az IoT Edge-modul műszaki eszköz előkészítése](./cpp-create-technical-assets.md)
- [Az IoT Edge-modul ajánlat létrehozása](./cpp-create-offer.md)
