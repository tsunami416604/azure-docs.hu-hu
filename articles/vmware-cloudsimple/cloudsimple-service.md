---
title: Azure VMware-megoldás CloudSimple-szolgáltatással
description: Áttekintést nyújt a CloudSimple szolgáltatásról és fogalmakról.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877662"
---
# <a name="cloudsimple-service-overview"></a>A CloudSimple szolgáltatás áttekintése

A CloudSimple szolgáltatás lehetővé teszi, hogy a CloudSimple az Azure VMware-megoldást használja.  A szolgáltatás létrehozása lehetővé teszi a csomópontok megvásárlását, a csomópontok fenntartását és a privát felhők létrehozását.  A CloudSimple szolgáltatást minden olyan Azure-régióban létre kell hoznia, ahol a CloudSimple szolgáltatás elérhető. A szolgáltatás a CloudSimple által definiált Azure VMware-megoldás peremhálózati hálózatát határozza meg. A peremhálózati hálózat olyan szolgáltatásokat támogat, amelyek VPN-, ExpressRoute-és internetkapcsolattal rendelkeznek a privát Felhőkhöz.

## <a name="gateway-subnet"></a>Átjáróalhálózat

Az átjáró-alhálózatok CloudSimple szolgáltatáshoz szükségesek, és egyediek ahhoz a régióhoz, amelyben létrehozták. Az átjáró-alhálózat a peremhálózati hálózat létrehozásakor használatos, és egy/28 CIDR blokkot igényel.  Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal. A CloudSimple-mel kommunikáló hálózatok közé tartoznak a helyszíni hálózatok és az Azure Virtual Network.  Az átjáró-alhálózat nem törölhető A létrehozásuk után.  Az átjáró-alhálózat törlődik a szolgáltatás törlésekor.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre CloudSimple szolgáltatást az Azure](quickstart-create-cloudsimple-service.md)-ban.
