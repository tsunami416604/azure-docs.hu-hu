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
ms.openlocfilehash: cff737b8c79c44494cb0151d6a6281550401b26e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
## <a name="scenario"></a>Forgatókönyv

Hozzon létre egy Vnetet és alhálózatok létrehozásával, ez a dokumentum az alábbi forgatókönyvet használja:

![VNet-forgatókönyv](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

Ebben a forgatókönyvben a neve VNet létrehozása **TestVNet**, és egy fenntartott CIDR-blokkja a **192.168.0.0./16**. A VNet a következő alhálózatokat tartalmaz: 

* **FrontEnd**, amelynek a CIDR-blokkja **192.168.1.0/24** lesz.
* **BackEnd**, amelynek a CIDR-blokkja **192.168.2.0/24** lesz.

