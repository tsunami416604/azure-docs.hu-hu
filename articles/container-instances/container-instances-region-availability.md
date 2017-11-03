---
title: "Az Azure tároló példányok régió és erőforrás rendelkezésre állási |} Az Azure Docs"
description: "Fedezze fel, mely Azure-régiók tároló példányokat, és a Processzor- és a központi telepítés támogatása ezeken a példányokon."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 2b9b1b864bbfd73383759212dd7d91f8e4941544
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Azure-tároló példányok régiónkénti elérhetőség

Előzetes Azure tároló példányok érhetők el a megadott Processzor- és memóriakorlátokat a következő régiókban.

| Hely | Operációs rendszer | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Nyugat-Európa, USA nyugati régiója, USA keleti régiója | Linux | 2 | 7 |
| Nyugat-Európa, USA nyugati régiója, USA keleti régiója | Windows | 2 | 3.5 |

## <a name="resource-availability"></a>Erőforrás-elérhetőséget

Ezen erőforrás határokon belül létrehozott tároló példányok használata a központi telepítés régión belül rendelkezésre állási tekintetében. A régió van terhelve, ha hibát tapasztalhat, példányok telepítése során.

Központi telepítési hiba mérséklése érdekében próbálkozzon a alacsonyabb CPU és memória-beállításokkal példányok telepítése, vagy egy későbbi időpontban a központi telepítés.

## <a name="next-steps"></a>Következő lépések

Tároló-példány központi telepítés hibaelhárításával kapcsolatos további információkért lásd: [Azure tároló osztályt telepítési problémák elhárításához](container-instances-troubleshooting.md).