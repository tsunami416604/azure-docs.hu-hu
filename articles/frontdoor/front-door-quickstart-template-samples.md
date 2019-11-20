---
title: Azure Resource Manager sablon minták – Azure bejárati ajtó szolgáltatás
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
ms.openlocfilehash: 2684c9852cb3e946d8ec1840fc166053204dfae0
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184595"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Azure Resource Managerbeli üzembe helyezési modellek sablonjai a Front Doorhoz

A következő táblázat az Azure Front Door Service-nek az Azure Resource Manager üzembe helyezési modelljeihez tartozó sablonjaira mutató hivatkozásokat tartalmaz. 

| | |
| ---| ---|
| [Alapszintű Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Egy alapszintű, egyetlen háttéralkalmazással rendelkező Frontdoor-konfigurációt hoz létre. |
| [Több háttéralkalmazással és háttérkészlettel, valamint URL-alapú útválasztással rendelkező Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Egy több háttéralkalmazással és URL-útvonal alapú háttérkészletekkel rendelkező terheléselosztót tartalmazó Frontdoort hoz létre. |
| [HTTPS-t alkalmazó (Frontdoor által kezelt tanúsítvány) egyéni tartomány előkészítése](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Egy egyéni tartományt ad a Frontdoorhoz, és engedélyezi annak HTTPS-forgalmát egy Frontdoor által kezelt, DigiCerttel létrehozott tanúsítvánnyal. |
| [A bejárati ajtó létrehozása geo-szűréssel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Hozzon létre egy bejárati ajtót, amely engedélyezi/blokkolja bizonyos országok/régiók forgalmát. |
| [A háttéralkalmazások állapotmintáinak vezérlése egy Frontdoorral](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Frissíti a Frontdoort, hogy az úgy módosítsa az állapotminták beállításait, hogy frissítse a minták útvonalát, valamint a minták küldésének időközeit. |
| [Aktív/készenlét háttéralkalmazás-konfigurációval rendelkező Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Létrehoz egy olyan Frontdoort, amely prioritásalapú útválasztást alkalmaz az aktív/készenléti alkalmazástopológiákhoz, azaz alapértelmezésként minden forgalmat az elsődleges (legnagyobb prioritással bíró) háttéralkalmazáshoz irányít, amíg az elérhetetlenné nem válik. |
| [Egyes útvonalakon engedélyezett gyorsítótárazással rendelkező Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Létrehoz egy olyan Frontdoort, amelynek meghatározott útválasztási konfigurációjában engedélyezve van a gyorsítótárazás, így az gyorsítótárazza az alkalmazások és szolgáltatások minden statikus eszközét. |
| [Munkamenet-affinitás konfigurálása a Frontdoor állomásneveihez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Frissít egy Frontdoort, hogy az engedélyezze az előtérbeli gazdagép munkamenet-affinitását, így az azonos felhasználói munkamenetből érkező további forgalmat ugyanazon háttéralkalmazáshoz küldi. |
| [Frontdoor konfigurálása ügyfelek IP-címének engedélyezéséhez vagy tiltásához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Úgy konfigurál egy Frontdoort, hogy az korlátozhassa az egyéni hozzáférés-vezérlést használó ügyfelek IP-címeinek forgalmát. |
| [A bejárati ajtó konfigurálása az adott http-paraméterekkel való művelet elvégzéséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Úgy konfigurál egy Frontdoort, hogy az a bejövő kérelem http-paraméterei alapján egyéni hozzáférés-vezérlési szabályokkal engedélyezhessen vagy letilthasson bizonyos forgalmat. |
| [A bejárati ajtó sebességének korlátozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Úgy konfigurál egy Frontdoort, hogy az korlátozza egy adott előtérbeli gazdagép bejövő forgalmának sebességét. |
| | |

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.