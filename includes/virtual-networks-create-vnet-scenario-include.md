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
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179035"
---
## <a name="scenario"></a>Forgatókönyv

Ez a dokumentum bemutatják, hogyan hozhat létre virtuális hálózat és alhálózatok, használja az alábbi forgatókönyvet:

![VNet-forgatókönyv](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

Ebben a forgatókönyvben nevű virtuális hálózat létrehozása **TestVNet**, és a egy fenntartott CIDR-blokk **192.168.0.0./16**. A VNet a következő alhálózatokat tartalmaz: 

* **FrontEnd**, amelynek a CIDR-blokkja **192.168.1.0/24** lesz.
* **BackEnd**, amelynek a CIDR-blokkja **192.168.2.0/24** lesz.

