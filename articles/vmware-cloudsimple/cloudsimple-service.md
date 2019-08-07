---
title: VMware-megoldás CloudSimple-szolgáltatással
description: A CloudSimple szolgáltatás és fogalmak áttekintését ismerteti.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a43fbebb21be82fd751778d6fec95e0ee9c346ad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812507"
---
# <a name="cloudsimple-service-overview"></a>A CloudSimple szolgáltatás áttekintése

A CloudSimple szolgáltatással az Azure VMware megoldás a CloudSimple használatával használható. A szolgáltatás létrehozása után kiépítheti a csomópontokat, lefoglalhatja a csomópontokat, és létrehozhat privát felhőket is. A CloudSimple szolgáltatást minden olyan Azure-régióban létre kell hoznia, ahol a CloudSimple szolgáltatás elérhető. A szolgáltatás a CloudSimple által definiált Azure VMware-megoldás peremhálózati hálózatát határozza meg. A peremhálózati hálózat támogatja a VPN-t, az Azure ExpressRoute és az internetkapcsolatot a privát felhőkhöz.

## <a name="gateway-subnet"></a>Átjáróalhálózat

Az átjáró-alhálózatok CloudSimple szolgáltatáshoz szükségesek, és egyediek ahhoz a régióhoz, amelyben létrehozták. Az átjáró-alhálózat akkor használatos, amikor létrehozza a peremhálózati hálózatot, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal. A CloudSimple kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák. Az átjáró-alhálózat létrehozása után nem törölhető. Az átjáró-alhálózat törlődik a szolgáltatás törlésekor.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre CloudSimple szolgáltatást az Azure](quickstart-create-cloudsimple-service.md)-ban.
