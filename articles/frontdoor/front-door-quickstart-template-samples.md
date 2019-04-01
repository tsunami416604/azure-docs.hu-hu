---
title: Azure Resource Manager-sablonminták – Azure Front Door Service | Microsoft Docs
description: Azure Resource Manager-sablonminták az Azure Front Door Service-hez
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: 1d1f7eeb6f55fa19b94a1eb4d05520c76352a414
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756549"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Azure Resource Managerbeli üzembe helyezési modellek sablonjai a Front Doorhoz

A következő táblázat az Azure Front Door Service-nek az Azure Resource Manager üzembe helyezési modelljeihez tartozó sablonjaira mutató hivatkozásokat tartalmaz. 

| | |
| ---| ---|
| [Alapszintű Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Egy alapszintű, egyetlen háttéralkalmazással rendelkező Frontdoor-konfigurációt hoz létre. |
| [Több háttéralkalmazással és háttérkészlettel, valamint URL-alapú útválasztással rendelkező Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Egy több háttéralkalmazással és URL-útvonal alapú háttérkészletekkel rendelkező terheléselosztót tartalmazó Frontdoort hoz létre. |
| [HTTPS-t alkalmazó (Frontdoor által kezelt tanúsítvány) egyéni tartomány előkészítése](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Egy egyéni tartományt ad a Frontdoorhoz, és engedélyezi annak HTTPS-forgalmát egy Frontdoor által kezelt, DigiCerttel létrehozott tanúsítvánnyal. |
| [A geoszűrési bejárati ajtajának létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Létrehoz egy Frontdoort, amely engedélyezi vagy letiltja egyes országok/régiók forgalmát. |
| [A háttéralkalmazások állapotmintáinak vezérlése egy Frontdoorral](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Frissíti a Frontdoort, hogy az úgy módosítsa az állapotminták beállításait, hogy frissítse a minták útvonalát, valamint a minták küldésének időközeit. |
| [Aktív/készenlét háttéralkalmazás-konfigurációval rendelkező Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Létrehoz egy olyan Frontdoort, amely prioritásalapú útválasztást alkalmaz az aktív/készenléti alkalmazástopológiákhoz, azaz alapértelmezésként minden forgalmat az elsődleges (legnagyobb prioritással bíró) háttéralkalmazáshoz irányít, amíg az elérhetetlenné nem válik. |
| [Egyes útvonalakon engedélyezett gyorsítótárazással rendelkező Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Létrehoz egy olyan Frontdoort, amelynek meghatározott útválasztási konfigurációjában engedélyezve van a gyorsítótárazás, így az gyorsítótárazza az alkalmazások és szolgáltatások minden statikus eszközét. |
| [Munkamenet-affinitás konfigurálása a Frontdoor állomásneveihez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Frissít egy Frontdoort, hogy az engedélyezze az előtérbeli gazdagép munkamenet-affinitását, így az azonos felhasználói munkamenetből érkező további forgalmat ugyanazon háttéralkalmazáshoz küldi. |
| [Frontdoor konfigurálása ügyfelek IP-címének engedélyezéséhez vagy tiltásához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Úgy konfigurál egy Frontdoort, hogy az korlátozhassa az egyéni hozzáférés-vezérlést használó ügyfelek IP-címeinek forgalmát. |
| [Bejárati ajtajának műveletet az adott http-paraméterek konfigurálása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Úgy konfigurál egy Frontdoort, hogy az a bejövő kérelem http-paraméterei alapján egyéni hozzáférés-vezérlési szabályokkal engedélyezhessen vagy letilthasson bizonyos forgalmat. |
| [Konfigurálhatja a sebességkorlátozást bejárati ajtajának](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Úgy konfigurál egy Frontdoort, hogy az korlátozza egy adott előtérbeli gazdagép bejövő forgalmának sebességét. |
| | |

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.