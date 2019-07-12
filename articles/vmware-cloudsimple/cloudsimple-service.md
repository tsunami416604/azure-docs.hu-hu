---
title: VMware megoldást CloudSimple szolgáltatás
description: CloudSimple szolgáltatás és fogalmak áttekintését ismerteti.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bebc68129ee2ff79241bcefec1ce0c3ca0b472d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595319"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple szolgáltatás áttekintése

A CloudSimple szolgáltatással Azure VMware megoldás CloudSimple által használható. Miután létrehozta a szolgáltatást, csomópontok kiépítéséhez, csomópontok lefoglalása és magánfelhők létrehozása. Hol érhető el a CloudSimple szolgáltatás minden egyes Azure-régióban létrehozta a CloudSimple szolgáltatást. A szolgáltatás az Azure VMware megoldás által CloudSimple edge-hálózatot határoz meg. Az edge-hálózatot VPN-, Azure ExpressRoute és a magánfelhők internetkapcsolat-szolgáltatásokhoz támogatja.

## <a name="gateway-subnet"></a>Átjáró alhálózata

Egy átjáró-alhálózat CloudSimple szolgáltatásonként megadása kötelező, és a régió, amelyben létre egyedi. Az átjáró-alhálózat használatos, amikor az edge-hálózatot hoz létre, és a egy/28-as igényel CIDR-blokkja. Az átjáró-alhálózat címtere egyedinek kell lennie. Az olyan hálózathoz, amely kommunikál a CloudSimple környezet nem lehetnek átfedésben. A hálózatok CloudSimple kommunikáló közé tartozik, a helyszíni hálózatok és az Azure virtuális hálózatok. Egy átjáró-alhálózatot nem lehet törölni, a létrehozást követően. Törli a szolgáltatást az átjáró-alhálózat nem érhető el.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [CloudSimple szolgáltatás létrehozása az Azure-ban](quickstart-create-cloudsimple-service.md).
