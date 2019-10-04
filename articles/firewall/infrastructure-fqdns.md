---
title: Infrastruktúra teljes tartományneve Azure Firewall
description: Tudnivalók a Azure Firewall infrastruktúra teljes tartománynevéről
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 5b134058e1924bae030338411226a9a6aff46557
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130206"
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