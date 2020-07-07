---
title: Infrastruktúra teljes tartományneve Azure Firewall
description: Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74168982"
---
# <a name="infrastructure-fqdns"></a>Infrastruktúra FQDN-jei

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. 

A beépített szabály gyűjtemény a következő szolgáltatásokat tartalmazza:

- A Storage platform rendszerkép-tárházának (PIR) számítási hozzáférése
- Felügyelt lemezek állapotának tárolási hozzáférése
- Azure Diagnostics és naplózás (MDS)

## <a name="overriding"></a>Felülbírálása 

A beépített infrastruktúra-szabálygyűjtemény felülbírálásához hozzon létre egy megtagadva az összes feldolgozott alkalmazás-szabály gyűjteményt. Ez mindig az infrastruktúra szabálygyűjteménye előtt lesz feldolgozva. Az infrastruktúra szabálygyűjteményében nem szereplő elemeket a rendszer alapértelmezés szerint elutasítja.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md).