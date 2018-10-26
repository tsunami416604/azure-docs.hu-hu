---
title: Az Azure DNS használata más Azure-szolgáltatások |} A Microsoft Docs
description: Megismerheti az Azure DNS használata más Azure-szolgáltatások nevek feloldása
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
ms.openlocfilehash: dcf209d2036d2686bea0b51380db3cd2473d04a6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094443"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Az Azure DNS más Azure-szolgáltatások működése

Az Azure DNS szolgáltatása egy üzemeltetett DNS felügyeleti és a név feloldása. Használhatja az alkalmazások és szolgáltatások üzembe helyezett nyilvános DNS-nevek létrehozása az Azure-ban. Az egyéni tartomány létrehozása egy Azure-szolgáltatás nevét használata egyszerű. Csak adjon hozzá egy megfelelő típusú rekordot a szolgáltatáshoz.

* A dinamikusan kiosztott IP-címek létrehozhat egy DNS CNAME-rekordot, amely a szolgáltatás létrehozása az Azure DNS-név. DNS-szabványok megakadályozzák a CNAME rekord a zóna felső pontja használatával. Helyette használhatja az alias rekord. További információkért lásd: [oktatóanyag: egy aliasrekordot lehet hivatkozni az Azure nyilvános IP-cím konfigurálása](tutorial-alias-pip.md).
* A statikusan kiosztott IP-címek, tetszőleges nevet, amely tartalmazza a hozhat létre egy DNS a-rekord egy *csupasz tartománynévnek* neve, a zóna legfelső pontján.

Az alábbi táblázat ismerteti a támogatott erőforrásrekord-típusok különböző Azure-szolgáltatások is használhat. Ahogy a táblázatban látható, az Azure DNS csak a DNS-rekordok az internetre irányuló hálózati erőforrások támogatja. Az Azure DNS nem használható belső, saját címek névfeloldásához.

| Azure-szolgáltatás | Hálózati illesztő | Leírás |
| --- | --- | --- |
| Azure Application Gateway |[Előtérbeli nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |A DNS A vagy CNAME rekordot is létrehozhat. |
| Azure Load Balancer |[Előtérbeli nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |A DNS A vagy CNAME rekordot is létrehozhat. Load Balancer rendelkezhet egy IPv6-alapú nyilvános IP-címet, amely a rendszer dinamikusan rendeli hozzá. Hozzon létre egy CNAME rekord, egy IPv6-cím. |
| Azure Traffic Manager |Nyilvános neve |Létrehozhat egy alias a rekordot, amely leképezi a Traffic Manager-profilhoz rendelt trafficmanager.net nevét. További információkért lásd: [oktatóanyag: egy aliasrekordot támogatásához apex-tartománynevek a Traffic Manager konfigurálása](tutorial-alias-tm.md). |
| Azure Cloud Services |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |A statikusan kiosztott IP-címek a DNS a-rekord is létrehozhat. A dinamikusan kiosztott IP-címek, létre kell hoznia egy CNAME rekordot, amely leképezi a *cloudapp.net* nevét.|
| Azure App Service | [Külső IP](dns-custom-domain.md#app-service-web-apps) |Külső IP-cím létrehozhat egy DNS a-rekord. Ellenkező esetben létre kell hoznia egy CNAME-rekordot, amely leképezi a azurewebsites.net nevét. További információkért lásd: [egy egyéni tartománynév hozzárendelése egy Azure-alkalmazáshoz](../app-service/app-service-web-tutorial-custom-domain.md). |
| Az Azure Resource Manager virtuális gépek |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |Resource Manager virtuális gépek nyilvános IP-címeket is rendelkezik. Nyilvános IP-címmel rendelkező virtuális gép is lehet egy terheléselosztó mögé. Létrehozhat egy DNS-beli A, CNAME vagy alias rekordot a nyilvános cím. Az egyéni nevet használhat megkerülhetik a VIP-címet a terheléselosztóhoz. |
| A klasszikus virtuális gépeket |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |Klasszikus virtuális gépeket, PowerShell vagy parancssori felület segítségével létrehozott egy dinamikus vagy statikus (fenntartott) virtuális-címmel rendelkező konfigurálható. Létrehozhat egy DNS CNAME rekord vagy egy A rekordot jelölik. |
