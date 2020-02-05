---
title: Azure VMware-megoldások (AVS) – szolgáltatás
description: Áttekintést nyújt az AVS szolgáltatásról és fogalmakról.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024953"
---
# <a name="avs-service-overview"></a>Az AVS szolgáltatás áttekintése

Az AVS szolgáltatás lehetővé teszi, hogy az AVS használatával Azure VMware-megoldást fogyasszon. A szolgáltatás létrehozása lehetővé teszi a csomópontok megvásárlását, a csomópontok fenntartását és az AVS privát felhők létrehozását. Az AVS szolgáltatást minden olyan Azure-régióban létre kell hoznia, ahol az AVS szolgáltatás elérhető. A szolgáltatás az Azure VMware-megoldás peremhálózati hálózatát az AVS használatával határozza meg. A peremhálózati hálózat támogatja a VPN-t, a ExpressRoute-t és az Internet-kapcsolatot az AVS privát Felhőkhöz.

## <a name="gateway-subnet"></a>Átjáró alhálózata

Az átjáró-alhálózatot az AVS szolgáltatásnak kell megadnia, és egyedinek kell lennie abban a régióban, amelyben létrehozták. Az átjáró-alhálózat a peremhálózati hálózat létrehozásakor használatos, és egy/28 CIDR blokkot igényel. Az átjáró alhálózatának címtartomány egyedinek kell lennie. Nem lehet átfedésben az AVS-környezettel kommunikáló hálózattal. Az AVS-vel kommunikáló hálózatok közé tartoznak a helyszíni hálózatok és az Azure Virtual Network. Az átjáró-alhálózat nem törölhető A létrehozásuk után. Az átjáró-alhálózat törlődik a szolgáltatás törlésekor.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre egy AVS-szolgáltatást az Azure](quickstart-create-cloudsimple-service.md)-ban.
