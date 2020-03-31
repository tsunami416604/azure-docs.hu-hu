---
title: Az Azure DNS használata más Azure-szolgáltatásokkal
description: Ebben a tanulási útvonalon ismerkedjen meg azzal, hogyan oldhatja fel más Azure-szolgáltatások neveit az Azure DNS használatával
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937251"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Hogyan működik az Azure DNS más Azure-szolgáltatásokkal?

Az Azure DNS egy üzemeltetett DNS-felügyeleti és névfeloldási szolgáltatás. Segítségével hozhat létre nyilvános DNS-neveket az Azure-ban üzembe helyezett más alkalmazásokhoz és szolgáltatásokhoz. Az Egyéni tartományban egy Azure-szolgáltatás nevének létrehozása egyszerű. Csak a szolgáltatásmegfelelő típusát adja hozzá.

* A dinamikusan lefoglalt IP-címek hez létrehozhat egy DNS CNAME rekordot, amely leképezi az Azure által a szolgáltatáshoz létrehozott DNS-nevet. A DNS-szabványok megakadályozzák, hogy CNAME rekordot használjon a zóna csúcsához. Használhat helyette aliasrekordot. További információ: [Oktatóanyag: Aliasrekord konfigurálása egy Azure nyilvános IP-címhez való hivatkozáshoz.](tutorial-alias-pip.md)
* A statikusan lefoglalt IP-címek hez bármilyen névvel létrehozhat DNS A rekordot, amely a zóna csúcsán *egy meztelen tartománynevet* tartalmaz.

Az alábbi táblázat ismerteti a támogatott bejegyzéstípusokat, amelyeket különböző Azure-szolgáltatásokhoz használhat. Amint azt a táblázat mutatja, az Azure DNS csak az internetes hálózati erőforrások DNS-rekordjait támogatja. Az Azure DNS nem használható a belső, privát címek névfeloldásához.

| Azure-szolgáltatás | Hálózati illesztő | Leírás |
| --- | --- | --- |
| Azure Application Gateway |[Előtér nyilvános IP](dns-custom-domain.md#public-ip-address) |DNS A vagy CNAME rekordot is létrehozhat. |
| Azure Load Balancer |[Előtér nyilvános IP](dns-custom-domain.md#public-ip-address) |DNS A vagy CNAME rekordot is létrehozhat. A terheléselosztó dinamikusan hozzárendelt IPv6 nyilvános IP-címmel rendelkezhet. CNAME rekord létrehozása IPv6-címhez. |
| Azure Traffic Manager |Nyilvános név |Létrehozhat egy aliasrekordot, amely leképezi a Traffic Manager-profilhoz rendelt trafficmanager.net nevet. További információ: [Oktatóanyag: Aliasrekord konfigurálása a csúcstartománynevek támogatására a Traffic Manager segítségével.](tutorial-alias-tm.md) |
| Azure Cloud Services |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |Statikusan lefoglalt IP-címek esetén dns A rekordot hozhat létre. Dinamikusan lefoglalt IP-címek esetén létre kell hoznia egy CNAME rekordot, amely a *cloudapp.net* névre van leképezve.|
| Azure App Service | [Külső IP-cím](dns-custom-domain.md#app-service-web-apps) |Külső IP-címekhez DNS A rekordot hozhat létre. Ellenkező esetben létre kell hoznia egy CNAME rekordot, amely a azurewebsites.net névre van leképezve. További információt az [Egyéni tartománynév hozzárendelése Azure-alkalmazáshoz](../app-service/app-service-web-tutorial-custom-domain.md)című témakörben talál. |
| Azure Erőforrás-kezelő virtuális gépei |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |Az Erőforrás-kezelő virtuális gépei nyilvános IP-címekkel rendelkezhetnek. A nyilvános IP-címmel rendelkező virtuális gép is lehet egy terheléselosztó mögött. A nyilvános címhez DNS A, CNAME vagy alias rekordot hozhat létre. Ezzel az egyéni névvel megkerülheti a VIP-t a terheléselosztón. |
| A klasszikus virtuális gépeket |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |A PowerShell vagy CLI használatával létrehozott klasszikus virtuális gépek dinamikus vagy statikus (fenntartott) virtuális címmel konfigurálhatók. Létrehozhat DNS CNAME vagy A rekordot. |
