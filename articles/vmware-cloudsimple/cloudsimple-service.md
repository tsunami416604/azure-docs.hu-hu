---
title: Azure VMware-megoldás a CloudSimple-től – szolgáltatás
description: Áttekintést nyújt a CloudSimple szolgáltatásról és fogalmakról.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024953"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple szolgáltatás – áttekintés

A CloudSimple szolgáltatás lehetővé teszi, hogy a CloudSimple által az Azure VMware-megoldás felhasználását használja.  A szolgáltatás létrehozása lehetővé teszi, hogy csomópontokat vásároljon, csomópontokat foglaljon le, és hozzon létre privát felhőket.  A CloudSimple szolgáltatást minden olyan Azure-régióban hozza létre, ahol a CloudSimple szolgáltatás elérhető. A szolgáltatás határozza meg az Azure VMware-megoldás felhőalapú hálózatát. A peremhálózati hálózat olyan szolgáltatásokat támogat, amelyek magukban foglalják a VPN-t, az ExpressRoute-ot és a privát felhőkhöz való internetkapcsolatot.

## <a name="gateway-subnet"></a>Átjáró alhálózata

Az átjáró-alhálózat szükséges a CloudSimple szolgáltatás, és egyedi a régióban, amelyben létrehozták. Az átjáró alhálózat a peremhálózati hálózat létrehozásakor használatos, és /28 CIDR blokkot igényel.  Az átjáró alhálózati címterének egyedinek kell lennie. Nem fedheti át a CloudSimple környezettel kommunikáló hálózatot. A CloudSimple-lel kommunikáló hálózatok közé tartoznak a helyszíni hálózatok és az Azure virtuális hálózatai.  Az átjáró alhálózata nem törölhető a létrehozása után.  Az átjáró alhálózata a szolgáltatás törlésekor törlődik.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre CloudSimple szolgáltatást az Azure-ban.](quickstart-create-cloudsimple-service.md)
