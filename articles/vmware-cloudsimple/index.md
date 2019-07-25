---
title: A CloudSimple Azure VMware-megoldása
description: A CloudSimple Azure VMware-megoldásainak ismertetése
author: sharaths-cs
ms.author: dikamath
ms.date: 04/23/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 38181a4ccd5b651bc3268cff1fa998aa9ac2e6d2
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444539"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>A CloudSimple Azure VMware-megoldása

Üdvözli a CloudSimple Azure VMware-megoldása. Itt részletesen megismerkedhet a CloudSimple használatával és azzal, hogyan történik a megoldások engedélyezése a szolgáltatásban. Cikkeket kereshet, vagy tallózhat a tartalomjegyzékben.

- Az [Áttekintés](cloudsimple-vmware-solutions-overview.md) részben további információkat talál a CloudSimple Azure VMware-megoldásáról.
- [A CloudSimple-magánfelhő áttekintését ismertető részben](cloudsimple-private-cloud.md) további információkat talál a CloudSimple magánfelhő-megoldásáról.
- A [Csomópontok](cloudsimple-node.md) részben további információkat talál a CloudSimple-csomópontokról.
- Az [Inicializálás és kapacitás kiépítése](quickstart-create-cloudsimple-service.md) lehetőség kiválasztásával megkezdheti a CloudSimple Azure VMware-megoldásának használatát.

| Fogalmak áttekintése     | Erőforrás                    | Cél                                                                       | Kezelés         |
|--------------------------|-----------------------------|-------------------------------------------------------------------------------|--------------------|
| **Szolgáltatás létrehozása**     | CloudSimple szolgáltatás         | A CloudSimple szolgáltatás peremhálózati konfigurálása                                    | Azure Portal       |
|                          | CloudSimple-csomópont            | CloudSimple szolgáltatás által használt kapacitás kiépítése                          | Azure Portal       |
| **Szolgáltatás használata**  | Magánfelhő               | VMware-környezetek létrehozása                                                    | CloudSimple-portál |
|                          | VLAN/alhálózat                 | Alapul szolgáló hálózatok létrehozása VMware-felhasználáshoz                               | CloudSimple-portál |
|                          | Tűzfaltábla              | Északi/déli és keleti/nyugati biztonsági szabályok konfigurálása az alapul szolgáló hálózatok között | CloudSimple-portál |
|                          | Nyilvános IP-cím           | Nyilvános címek beszerzése VMware-alapú számítási feladatokhoz                           | CloudSimple-portál |
|                          | Azure-kapcsolat            | Helyszíni vagy Azure-beli virtuális hálózatokhoz való kapcsolat létrehozása         | CloudSimple-portál |
| **Egyesített kezelés**   | CloudSimple virtuális gép | VMware virtuális gépek kezelése az Azure Portalon                                | Azure Portal       |
