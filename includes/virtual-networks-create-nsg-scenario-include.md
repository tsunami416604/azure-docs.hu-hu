---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 873549442284ede2e9f020bd90879f721b9c1a18
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38760360"
---
## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum jobban bemutatják, hogyan hozhat létre NSG-ket, használja az alábbi forgatókönyvet:

![VNet-forgatókönyv](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Ebben a forgatókönyvben minden alhálózathoz, az NSG-t hoz létre a **TestVNet** virtuális hálózatban, az alábbiak szerint: 

* **NSG-FrontEnd**. Az előtérbeli NSG vonatkozik a *előtérbeli* alhálózathoz, és két szabályt tartalmaz:    
  * **RDP-szabály**. Engedélyezi az RDP-forgalmat a *előtérbeli* alhálózat.
  * **webalkalmazás-szabály**. Lehetővé teszi a HTTP-forgalom a *előtérbeli* alhálózat.
* **NSG-háttérrendszer**. A háttérbeli NSG vonatkozik a *háttérrendszer* alhálózathoz, és két szabályt tartalmaz:    
  * **SQL-szabály**. Lehetővé teszi, hogy csak az SQL-forgalmat a *előtérbeli* alhálózat.
  * **webalkalmazás-szabály**. Az összes internetre irányuló forgalmat a megtagadja a *háttérrendszer* alhálózat.

Ezek a szabályok kombinációját, hozzon létre egy DMZ-hez hasonló forgatókönyv, ahol a háttérbeli alhálózat is csak bejövő forgalom fogadásához meg SQL esetén az előtéri alhálózatról érkező, és nem fér hozzá az internethez, miközben az előtérbeli alhálózat kommunikáljon az internettel, és fogadása a bejövő HTTP-kérések csak.

