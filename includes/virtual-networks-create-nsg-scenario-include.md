---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 09c6871fc5243296da2f2defd594afb80c62ac95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Forgatókönyv
Jobb szemléltetéséhez NSG-k létrehozása, ez a dokumentum az alábbi forgatókönyvet használja:

![VNet-forgatókönyv](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Ebben a forgatókönyvben egy NSG-t az egyes alhálózatokon hoz létre a **TestVNet** virtuális hálózat, az alábbiak szerint: 

* **NSG-előtérbeli**. Az előtér-NSG alkalmazott a *előtér* alhálózatot, és két szabályt tartalmaz:    
  * **RDP-szabály**. RDP-forgalmát engedélyezi a *előtér* alhálózat.
  * **webalkalmazás-szabály**. Lehetővé teszi a HTTP-forgalom a *előtér* alhálózat.
* **NSG-háttérrendszer**. A háttér-NSG alkalmazott a *háttér* alhálózatot, és két szabályt tartalmaz:    
  * **SQL-szabály**. Csak az SQL-forgalmát engedélyezi a *előtér* alhálózat.
  * **webalkalmazás-szabály**. Az összes internetes forgalom kötött megtagadja a *háttér* alhálózat.

Ezek a szabályok kombinációját, hozzon létre egy DMZ-szerű forgatókönyv, ahol a háttér-alhálózat is csak kapnak bejövő forgalom az SQL az előtér-alhálózatot, és nem fér hozzá az internethez az előtér-alhálózatot az internettel kommunikál, és fogadni bejövő HTTP-kérelmek csak.

