---
title: VMware megoldást CloudSimple szolgáltatás
description: CloudSimple szolgáltatás és fogalmak áttekintését ismerteti.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6a4c0bc6070d372a279b74f81ac1f84f565559c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165243"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple szolgáltatás áttekintése

A CloudSimple szolgáltatással Azure VMware megoldás CloudSimple által használható. Miután létrehozta a szolgáltatást, csomópontok kiépítéséhez, csomópontok lefoglalása és magánfelhők létrehozása. Hol érhető el a CloudSimple szolgáltatás minden egyes Azure-régióban létrehozta a CloudSimple szolgáltatást. A szolgáltatás az Azure VMware megoldás által CloudSimple edge-hálózatot határoz meg. Az edge-hálózatot VPN-, Azure ExpressRoute és a magánfelhők internetkapcsolat-szolgáltatásokhoz támogatja.

## <a name="gateway-subnet"></a>Átjáró alhálózata

Egy átjáró-alhálózat CloudSimple szolgáltatásonként megadása kötelező, és a régió, amelyben létre egyedi. Az átjáró-alhálózat használatos, amikor az edge-hálózatot hoz létre, és a egy/28-as igényel CIDR-blokkja. Az átjáró-alhálózat címtere egyedinek kell lennie. Az olyan hálózathoz, amely kommunikál a CloudSimple környezet nem lehetnek átfedésben. A hálózatok CloudSimple kommunikáló közé tartozik, a helyszíni hálózatok és az Azure virtuális hálózatok. Egy átjáró-alhálózatot nem lehet törölni, a létrehozást követően. Törli a szolgáltatást az átjáró-alhálózat nem érhető el.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [CloudSimple szolgáltatás létrehozása az Azure-ban](quickstart-create-cloudsimple-service.md).
