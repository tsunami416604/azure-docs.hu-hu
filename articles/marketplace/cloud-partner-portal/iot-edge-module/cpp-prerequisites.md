---
title: Az Azure IoT Edge modul előfeltételei | Azure Piactér
description: Az IoT Edge-modul közzétételének előfeltételei.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 9a54b31725d14a3ff54bd2d945cd69f4b8769b87
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983177"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge-modul közzétételi előfeltételei

>[!Important]
>2020. április 13-tól megkezdjük az IoT Edge modulajánlatok kezelését a Partner Centerbe. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [IoT Edge-modulajánlat létrehozása](https://aka.ms/AzureCreateIoT) az áttelepített ajánlatok kezeléséhez kövesse az utasításokat.

Ez a cikk ismerteti az IoT Edge-modulajánlat közzétételének előfeltételeit.  Ha még nem tette meg, tekintse át az [IoT Edge-modulok közzétételi útmutatóját.](../..//iot-edge-module.md)


## <a name="publishing-prerequisites"></a>Közzétételi előfeltételek

IoT Edge-modul közzététele az Azure Marketplace-en, meg kell felelnie az alábbi előfeltételeknek:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Hozzáférés a [Cloud Partner Portálhoz.](https://cloudpartner.azure.com/) További információ: [Azure Marketplace és AppSource közzétételi útmutató.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)
- Megállapodás az [Azure Piactér feltételeiről](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Az IoT Edge-modul technikai eszközének üzemeltetése egy Azure Container Registry.Host your IoT Edge module technical asset in an Azure Container Registry.  További információkért tekintse meg, [hogyan készítheti el az IoT Edge modul technikai eszköz](./cpp-create-technical-assets.md)
- Az IoT Edge-modul metaadatai készen állnak a használatra. Készítse elő például a következő eszközöket:
    - A cím
    - Leírás (HTML formátumban)
    - Emblémakép (PNG formátum és rögzített képméretek, beleértve a 40x40px, 90x90px, 115x115px, 255x115px)
    - A használati idő és az adatvédelmi politika
    - Alapértelmezett modulkonfiguráció, amely a következőket tartalmazza: útvonalak, iker kívánt tulajdonságok, createOptions és környezeti változók.
    - Modul dokumentációja
    - Támogatási kapcsolattartók


## <a name="next-steps"></a>További lépések

Miután [elkészítette az IoT Edge modul technikai eszközét,](./cpp-create-technical-assets.md)készen áll [az IoT Edge modulajánlat létrehozására.](./cpp-create-offer.md) 
