---
title: VMware-megoldások által CloudSimple - szolgáltatás
description: CloudSimple szolgáltatás és fogalmak áttekintését ismerteti.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358108"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple szolgáltatás áttekintése

A CloudSimple szolgáltatás lehetővé teszi, hogy Azure VMware megoldás által CloudSimple felhasználásához.  A szolgáltatás létrehozása lehetővé teszi, hogy a csomópontok beszerzési, a csomópontok lefoglalása és a Magánfelhők létrehozása.  Hol érhető el a CloudSimple szolgáltatás minden egyes Azure-régióban létrehozta a CloudSimple szolgáltatást.  A szolgáltatás az Azure VMware megoldás által CloudSimple edge-hálózatot határoz meg.  Az edge-hálózatot VPN, ExpressRoute és a Magánfelhők internetkapcsolat-szolgáltatásokhoz támogatja.

## <a name="gateway-subnet"></a>Átjáró alhálózata

Egy átjáró-alhálózat CloudSimple szolgáltatásonként megadása kötelező, és a régió, amelyben létre egyedi. Az átjáró-alhálózat létrehozásához az edge-hálózatot, és a egy/28-as igényel CIDR-blokkja.  Az átjáró-alhálózat címtere egyedinek kell lennie. Az olyan hálózathoz, amely kommunikál a CloudSimple környezet nem lehetnek átfedésben. A hálózatok CloudSimple kommunikáló közé tartozik, a helyszíni hálózatok és az Azure virtual network.  Egy átjáró-alhálózatot nem lehet törölni, a létrehozást követően.  Törli a szolgáltatást az átjáró-alhálózat nem érhető el.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [CloudSimple szolgáltatás létrehozása az Azure-ban](quickstart-create-cloudsimple-service.md)