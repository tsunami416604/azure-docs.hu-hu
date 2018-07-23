---
title: Azure DNS használata más Azure-szolgáltatások |} A Microsoft Docs
description: Az Azure DNS használatával oldja meg a nevet más Azure-szolgáltatások ismertetése
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: 2f5ff425eadc4572f5e109f503c57969ab310f6b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171806"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Az Azure DNS más Azure-szolgáltatások működése

Az Azure DNS szolgáltatása egy üzemeltetett DNS felügyeleti és a név feloldása. Ez lehetővé teszi, hogy az egyéb alkalmazások és szolgáltatások telepítése a nyilvános DNS-nevek létrehozása az Azure-ban. Az egyéni tartomány létrehozása egy Azure-szolgáltatás nevét az szúrni a szolgáltatás megfelelő típusú rekord.

* A dinamikusan kiosztott IP-címek létre kell hoznia egy DNS CNAME-rekordot, amely a DNS-név, amely a szolgáltatás számára létrehozott Azure van leképezve. DNS-szabványok megakadályozzák a CNAME rekord a zóna felső pontja használatával.
* A statikusan kiosztott IP-címek, a DNS a-rekord bármely név használatával hozhat létre például egy *csupasz tartománynévnek* neve, a zóna legfelső pontján.

Az alábbi táblázat ismerteti a támogatott bejegyzéstípusokat, különböző Azure-szolgáltatások is használható. Amint láthatja, ebből a táblázatból, az Azure DNS DNS-rekordok csak internetkapcsolattal rendelkező hálózati erőforrások támogatja. Az Azure DNS nem használható belső, saját címek névfeloldásához.

| Azure-szolgáltatás | Hálózati adapter | Leírás |
| --- | --- | --- |
| Application Gateway |[Előtérbeli nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |A DNS A vagy CNAME rekordot is létrehozhat. |
| Load Balancer |[Előtérbeli nyilvános IP-cím](dns-custom-domain.md#public-ip-address)  |A DNS A vagy CNAME rekordot is létrehozhat. Load Balancer dinamikusan hozzárendelt IPv6-alapú nyilvános IP-cím lehet. Ezért létre kell hoznia egy CNAME rekord, egy IPv6-cím. |
| Traffic Manager |Nyilvános neve |Csak hozhat létre egy CNAME REKORDOT, amely leképezi a Traffic Manager-profilhoz rendelt trafficmanager.net nevét. További információkért lásd: [működik, hogy a Traffic Manager](../traffic-manager/traffic-manager-overview.md#traffic-manager-example). |
| Felhőszolgáltatás |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |A statikusan kiosztott IP-címek a DNS a-rekord is létrehozhat. A dinamikusan kiosztott IP-címek, létre kell hoznia egy CNAME rekordot, amely leképezi a *cloudapp.net* nevét.|
| App Service | [Külső IP](dns-custom-domain.md#app-service-web-apps) |Külső IP-cím létrehozhat egy DNS a-rekord. Ellenkező esetben létre kell hoznia egy CNAME-rekordot, amely leképezi a azurewebsites.net nevét. További információkért lásd: [egy egyéni tartománynév hozzárendelése egy Azure-alkalmazáshoz](../app-service/app-service-web-tutorial-custom-domain.md) |
| Resource Manager virtuális gépek |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |Resource Manager virtuális gépek nyilvános IP-cím lehet. Nyilvános IP-címmel rendelkező virtuális gép is lehet egy terheléselosztó mögé. Létrehozhat egy DNS-beli A vagy CNAME rekordot a nyilvános cím. Az egyéni nevet a VIP-címet a terheléselosztó megkerülésére használható. |
| A klasszikus virtuális gépeket |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |Klasszikus virtuális gépeket létrehozni a PowerShell használatával, vagy a parancssori felület is konfigurálhatók, dinamikus vagy statikus (fenntartott) virtuális cím. Létrehozhat egy DNS CNAME rekord vagy egy rekordot, jelölik. |
