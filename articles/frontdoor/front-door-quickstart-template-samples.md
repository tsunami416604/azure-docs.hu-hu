---
title: Az Azure Resource Manager sablonmintái – Azure bejárati ajtó
description: Az Azure Resource Manager sablonmintái az Azure Bejárati ajtajához
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: de3b13162e7828542331e7e7a34d0101f7feaf71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79471653"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Azure Resource Managerbeli üzembe helyezési modellek sablonjai a Front Doorhoz

Az alábbi táblázat az Azure Resource Manager telepítési modellsablonjaira mutató hivatkozásokat tartalmaz az Azure Front Door alkalmazáshoz. 

| | |
| ---| ---|
| [Alapszintű Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Egy alapszintű, egyetlen háttéralkalmazással rendelkező Frontdoor-konfigurációt hoz létre. |
| [Több háttéralkalmazással és háttérkészlettel, valamint URL-alapú útválasztással rendelkező Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Egy több háttéralkalmazással és URL-útvonal alapú háttérkészletekkel rendelkező terheléselosztót tartalmazó Frontdoort hoz létre. |
| [HTTPS-t alkalmazó (Frontdoor által kezelt tanúsítvány) egyéni tartomány előkészítése](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Egy egyéni tartományt ad a Frontdoorhoz, és engedélyezi annak HTTPS-forgalmát egy Frontdoor által kezelt, DigiCerttel létrehozott tanúsítvánnyal. |
| [Bejárati ajtó létrehozása geoszűréssel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Hozzon létre egy bejárati ajtót, amely lehetővé teszi/blokkolja a forgalmat bizonyos országokból /régiókból. |
| [A háttéralkalmazások állapotmintáinak vezérlése egy Frontdoorral](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Frissíti a Frontdoort, hogy az úgy módosítsa az állapotminták beállításait, hogy frissítse a minták útvonalát, valamint a minták küldésének időközeit. |
| [Aktív/készenlét háttéralkalmazás-konfigurációval rendelkező Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Létrehoz egy olyan Frontdoort, amely prioritásalapú útválasztást alkalmaz az aktív/készenléti alkalmazástopológiákhoz, azaz alapértelmezésként minden forgalmat az elsődleges (legnagyobb prioritással bíró) háttéralkalmazáshoz irányít, amíg az elérhetetlenné nem válik. |
| [Egyes útvonalakon engedélyezett gyorsítótárazással rendelkező Frontdoor létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Létrehoz egy olyan Frontdoort, amelynek meghatározott útválasztási konfigurációjában engedélyezve van a gyorsítótárazás, így az gyorsítótárazza az alkalmazások és szolgáltatások minden statikus eszközét. |
| [Munkamenet-affinitás konfigurálása a Frontdoor állomásneveihez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Frissít egy Frontdoort, hogy az engedélyezze az előtérbeli gazdagép munkamenet-affinitását, így az azonos felhasználói munkamenetből érkező további forgalmat ugyanazon háttéralkalmazáshoz küldi. |
| [Frontdoor konfigurálása ügyfelek IP-címének engedélyezéséhez vagy tiltásához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Úgy konfigurál egy Frontdoort, hogy az korlátozhassa az egyéni hozzáférés-vezérlést használó ügyfelek IP-címeinek forgalmát. |
| [A Bejárati ajtó konfigurálása adott http-paraméterekkel való művelethez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Úgy konfigurál egy Frontdoort, hogy az a bejövő kérelem http-paraméterei alapján egyéni hozzáférés-vezérlési szabályokkal engedélyezhessen vagy letilthasson bizonyos forgalmat. |
| [A bejárati ajtó sebességének korlátozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Úgy konfigurál egy Frontdoort, hogy az korlátozza egy adott előtérbeli gazdagép bejövő forgalmának sebességét. |
| | |

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.