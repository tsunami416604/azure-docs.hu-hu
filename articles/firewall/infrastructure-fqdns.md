---
title: Infrastruktúra Azure tűzfal teljes Tartományneve
description: Ismerje meg az infrastruktúra teljes tartománynevek az Azure-tűzfal
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 1369a82829b2c80768d746ba92daf2482c1fd7f8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994139"
---
# <a name="infrastructure-fqdns"></a>Infrastruktúra teljes tartománynevek

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. 

A beépített szabálygyűjtemény a következő szolgáltatásokat tartalmazza:

- Számítási Platform kép tárház (PIR) storage-hozzáférés
- Felügyelt lemezek állapot storage-hozzáférés
- Az Azure diagnosztikai és naplózási (MDS)
- Azure Active Directory

## <a name="overriding"></a>Felülbírálása 

A beépített infrastruktúra szabálygyűjtemény megtagadási létrehozásával legutóbb feldolgozott összes alkalmazás szabályok gyűjteményéhez felül lehet bírálni. Ez mindig az infrastruktúra szabálygyűjteménye előtt lesz feldolgozva. Az infrastruktúra szabálygyűjteményében nem szereplő elemeket a rendszer alapértelmezés szerint elutasítja.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [üzembe helyezése és konfigurálása az Azure-tűzfalak](tutorial-firewall-deploy-portal.md).