---
title: Azure Service Fabric CLI- sfctl tároló
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A tárolók parancsainak listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 07861e2b67eea67740b341cbea994de978973664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906117"
---
# <a name="sfctl-container"></a>sfctl-tároló
Tárolóval kapcsolatos parancsok futtatása fürtcsomóponton.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| meghívás-api | A tároló API-t egy adott kódcsomag service fabric-csomópontján üzembe helyezett tárolón hívja meg. |
| naplók | Lekéri a tároló naplók egy Service Fabric-csomóponton üzembe helyezett tároló. |

## <a name="sfctl-container-invoke-api"></a>sfctl tároló meghívás-api
A tároló API-t egy adott kódcsomag service fabric-csomópontján üzembe helyezett tárolón hívja meg.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --code-package-instance-id [Kötelező] | A szolgáltatásháló-csomóponton telepített kódcsomag-példány egyedi azonosítására azonosító. <br><br> A "szolgáltatáskód-csomaglista" segítségével visszakereshető. |
| --code-package-name [Kötelező] | A service manifest-ban megadott kódcsomag neve egy Service Fabric-fürtben egy alkalmazástípus részeként regisztrálva. |
| --container-api-uri-path [Kötelező] | A CONTAINER REST API URI elérési útjának használata a tároló név/azonosító helyett használja a "{ID}" értéket. |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --szolgáltatás-jegyzéknév [Kötelező] | A Service Fabric-fürtben egy alkalmazástípus részeként regisztrált szolgáltatásjegyzék neve. |
| --konténer-api-test | HTTP-kérelem törzse a TÁROLÓ REST API-hoz. |
| --konténer-api-tartalom-típus | A REST API tároló tartalomtípusa, alapértelmezés szerint "application/json". |
| --container-api-http-ige | HTTP-ige a TÁROLÓ REST API-hoz, alapértelmezés szerint GET. |
| --idő-out -t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-container-logs"></a>sfctl konténernaplók
Lekéri a tároló naplók egy Service Fabric-csomóponton üzembe helyezett tároló.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --code-package-instance-id [Kötelező] | Kódcsomag példányazonosítója, amelyet a "szolgáltatáskód-csomaglista" beolvas. |
| --code-package-name [Kötelező] | A service manifest-ban megadott kódcsomag neve egy Service Fabric-fürtben egy alkalmazástípus részeként regisztrálva. |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --szolgáltatás-jegyzéknév [Kötelező] | A Service Fabric-fürtben egy alkalmazástípus részeként regisztrált szolgáltatásjegyzék neve. |
| --farok | A naplók végétől megjelenítandó sorok száma. Az alapértelmezett érték 100. "all", hogy megmutassa a teljes naplókat. |
| --idő-out -t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.