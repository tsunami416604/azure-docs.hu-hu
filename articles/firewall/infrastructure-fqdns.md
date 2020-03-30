---
title: Infrastruktúra teljes tartományhálózata az Azure tűzfalhoz
description: Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168982"
---
# <a name="infrastructure-fqdns"></a>Infrastruktúra FQDN-jei

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. 

A beépített szabálygyűjtemény a következő szolgáltatásokat tartalmazza:

- Számítási hozzáférés a tárolási platform képtárához (PIR)
- Felügyelt lemezek állapottárolási hozzáférése
- Azure-diagnosztika és naplózás (MDS)

## <a name="overriding"></a>Felülírása 

Ezt a beépített infrastruktúraszabály-gyűjteményt felülbírálhatja, ha megtagadja az összes utoljára feldolgozott alkalmazásszabály-gyűjteményt. Ez mindig az infrastruktúra szabálygyűjteménye előtt lesz feldolgozva. Az infrastruktúra szabálygyűjteményében nem szereplő elemeket a rendszer alapértelmezés szerint elutasítja.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [telepítheti és konfigurálhatja az Azure-tűzfalat.](tutorial-firewall-deploy-portal.md)