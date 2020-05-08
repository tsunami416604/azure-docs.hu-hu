---
title: Gyakori kérdések – Azure Load Balancer
description: Válaszok a Azure Load Balancer kapcsolatos gyakori kérdésekre.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3648bb102d539156067cdc259c1cf9b6e73d6a49
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884487"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

## <a name="what-types-of-load-balancer-exist"></a>Milyen típusú Load Balancer létezik?
Belső terheléselosztó, amely a VNET és a külső terheléselosztó adatforgalmát kiegyensúlyozza, és egy internetkapcsolattal rendelkező végpont felé irányuló forgalmat egyenlít ki. További információ: [Load Balancer types] (https://docs.microsoft.com/azure/load-balancer/concepts-limitations#load-balancer-types). 

Mindkét típus esetében az Azure egy alapszintű SKU-t és egy standard SKU-t kínál, amely különböző funkcionális, teljesítménybeli, biztonsági és állapot-követési képességekkel rendelkezik. Ezeket a különbségeket az [SKU-összehasonlítás] (https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus) cikk) ismerteti.

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
