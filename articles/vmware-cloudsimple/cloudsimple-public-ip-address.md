---
title: VMware megoldást CloudSimple – Azure nyilvános IP-cím szerint
description: További információ a nyilvános IP-címek és a VMware-megoldások CloudSimple előnyeiket
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a366ec821e5e9663908f790ab3a88424fa956075
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576970"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple nyilvános IP-cím áttekintése

Nyilvános IP-cím lehetővé teszi, hogy való kommunikációhoz az internetes erőforrások bejövő, a magánfelhő-alapú erőforrásokhoz egy magánhálózati IP-címen. Magánhálózati IP-címe, vagy egy virtuális gépet, vagy egy szoftveres terheléselosztót. A magánhálózati IP-címet a magánfelhő vcenter van. A nyilvános IP-cím lehetővé teszi, hogy az interneten a magánfelhőben futó szolgáltatások.

Nyilvános IP-cím addig, amíg Ön hozzárendeltem dedikált magánhálózati IP-címet. Nyilvános IP-cím csak egy magánhálózati IP-cím lehet hozzárendelni.

Mindig társított nyilvános IP-cím erőforrás nyilvános IP-címet használja az internet-hozzáférés. Alapértelmezés szerint csak kimenő internet-hozzáférés engedélyezett nyilvános IP-címet.  A nyilvános IP-cím a bejövő forgalmat a rendszer megtagadja.  Bejövő forgalom engedélyezéséhez hozzon létre egy tűzfalszabályt az adott portot a nyilvános IP-címet.

## <a name="benefits"></a>Előnyök

Nyilvános IP-cím segítségével bejövő kommunikációt biztosít:

* Szolgáltatás (DDoS) támadások megelőzésére elosztott szolgáltatásmegtagadásos. Ez a védelem automatikusan engedélyezve van a nyilvános IP-címet.
* – A forgalom figyelési és valós idejű csökkenti a hálózati szintű gyakori támadásoktól. Ezek a védelem a Microsoft online szolgáltatások által használt azonos védelem.
* A teljes méretezési, az Azure globális hálózatán. A hálózat használható, és csökkentheti a támadási forgalom régiók között elosztva.  

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [foglaljon le egy nyilvános IP-cím](https://docs.azure.cloudsimple.com/publicips/)
