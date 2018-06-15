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
ms.openlocfilehash: b91ae155761f6357e286f4742d57b97cf96d909a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805170"
---
## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum jobban vnetek létrehozásának udr-EK, használja a következő forgatókönyvet:

![KÉPLEÍRÁS](./media/virtual-network-create-udr-scenario-include/figure1.png)

Ebben a forgatókönyvben a egy UDR létrehozása a *előtér alhálózati* és az egy másik UDR a *háttér-alhálózat*, az alábbiak szerint: 

* **UDR-előtérbeli**. Az előtér-UDR alkalmazott a *előtér* alhálózatot, és egy útvonalat tartalmaznak:    
  * **RouteToBackend**. Ez az útvonal az összes forgalom küldése a háttér-alhálózat a **FW1** virtuális gépet.
* **UDR-háttérrendszer**. A háttér-UDR alkalmazott a *háttér* alhálózatot, és egy útvonalat tartalmaznak:    
  * **RouteToFrontend**. Ez az útvonal az összes forgalom küldése az előtér-alhálózat a **FW1** virtuális gépet.

Ezeket az útvonalakat kombinációja annak biztosítására, hogy irányuló teljes forgalomra egy alhálózatból másik futtatna a **FW1** virtuális gépet, mely a virtuális készülék használatos. Szükség IP-továbbítást a kapcsolja a **FW1** virtuális Gépet, győződjön meg arról, megkaphatja a többi virtuális gépe irányuló forgalmat.

