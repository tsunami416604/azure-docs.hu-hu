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
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170292"
---
## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum jobban bemutatják, hogyan hozhat létre NSG-ket, használja az alábbi forgatókönyvet:

![VNet-forgatókönyv](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Ebben a forgatókönyvben minden alhálózathoz, az NSG-t hoz létre a **TestVNet** virtuális hálózatban, az alábbiak szerint: 

* **NSG-FrontEnd**. Az előtérbeli NSG vonatkozik a *előtérbeli* alhálózathoz, és két szabályt tartalmaz:    
  * **rdp-rule**. Engedélyezi az RDP-forgalmat a *előtérbeli* alhálózat.
  * **webalkalmazás-szabály**. Lehetővé teszi a HTTP-forgalom a *előtérbeli* alhálózat.
* **NSG-háttérrendszer**. A háttérbeli NSG vonatkozik a *háttérrendszer* alhálózathoz, és két szabályt tartalmaz:    
  * **sql-rule**. Lehetővé teszi, hogy csak az SQL-forgalmat a *előtérbeli* alhálózat.
  * **webalkalmazás-szabály**. Az összes internetre irányuló forgalmat a megtagadja a *háttérrendszer* alhálózat.

Ezek a szabályok kombinációját, hozzon létre egy DMZ-hez hasonló forgatókönyv, ahol a háttérbeli alhálózat is csak bejövő forgalom fogadásához meg SQL esetén az előtéri alhálózatról érkező, és nem fér hozzá az internethez, miközben az előtérbeli alhálózat kommunikáljon az internettel, és fogadása a bejövő HTTP-kérések csak.

