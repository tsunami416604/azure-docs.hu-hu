---
title: Azure IoT Edge modul előfeltételei | Azure piactér
description: IoT Edge modul közzétételének előfeltételei.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142373"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge modul közzétételi előfeltételei

>[!Important]
>2020. április 13-ától kezdődően megkezdjük a IoT Edge modul-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [IoT Edge modul létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) című témakör utasításait.

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
