---
title: Gyakori kérdések – Azure Load Balancer
description: Válaszok a Azure Load Balancer kapcsolatos gyakori kérdésekre.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3be8ce241817b3b2fa03976eebe3147c1dc9c877
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005156"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

## <a name="what-types-of-load-balancer-exist"></a>Milyen típusú Load Balancer létezik?
Belső terheléselosztó, amely a VNET és a külső terheléselosztó adatforgalmát kiegyensúlyozza, és egy internetkapcsolattal rendelkező végpont felé irányuló forgalmat egyenlít ki. További információ: [Load Balancer típusok](components.md#frontend-ip-configurations). 

Mindkét típus esetében az Azure egy alapszintű SKU-t és egy standard SKU-t kínál, amely különböző funkcionális, teljesítménybeli, biztonsági és állapot-követési képességekkel rendelkezik. Ezeket a különbségeket az [SKU-összehasonlítási](skus.md) cikkben ismertetjük.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Hogyan frissíthetek alapszintről standard Load Balancerra?
Tekintse meg az [alapszintről standard verzióra való frissítést](upgrade-basic-standard.md) egy automatizált parancsfájl és egy Load Balancer SKU verziófrissítésének útmutatója alapján.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Mik a különböző terheléselosztási lehetőségek az Azure-ban?
Tekintse meg a terheléselosztó [technológiai útmutatóját](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) a rendelkezésre álló terheléselosztási szolgáltatásokhoz és a javasolt felhasználási módokhoz.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Hol találhatók Load Balancer ARM-sablonok?
Tekintse meg a gyakori központi telepítések ARM-sablonjaihoz tartozó Azure Load Balancer rövid útmutatók [listáját](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) .

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Miben különböznek a bejövő NAT-szabályok a terheléselosztási szabályoktól?
A NAT-szabályok olyan háttér-erőforrás megadására szolgálnak, amely a forgalmat irányítja át. Például egy adott terheléselosztó-port konfigurálása egy adott virtuális gépre irányuló RDP-forgalom küldéséhez. A terheléselosztási szabályok segítségével megadhatók a háttér-erőforrások készlete a forgalom átirányításához, valamint a terhelés elosztása az egyes példányok között. Egy terheléselosztó-szabály például átirányíthatja a TCP-csomagokat a terheléselosztó 80-es portjára a webkiszolgálók készletén keresztül.

## <a name="next-steps"></a>Következő lépések
Ha a kérdés nem szerepel a fentiekben, küldjön visszajelzést erről az oldalról a kérdésével kapcsolatban. Ez egy GitHub-problémát hoz létre a termékért felelős csapat számára, amely biztosítja, hogy az összes értékes vásárlói kérdés megválaszolva legyen.
