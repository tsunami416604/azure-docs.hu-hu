---
title: Azure Service Fabric CLI – sfctl tároló
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A tárolók parancsainak listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 07861e2b67eea67740b341cbea994de978973664
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906117"
---
# <a name="sfctl-container"></a>sfctl-tároló
Tárolóval kapcsolatos parancsok futtatása fürtcsomóponton.

## <a name="commands"></a>Parancsok

|Parancs|Description|
| --- | --- |
| meghívás – API | A Container API meghívása egy Service Fabric csomóponton telepített tárolón az adott programkódhoz. |
| naplók | Beolvassa a Service Fabric csomóponton üzembe helyezett tároló naplófájljait. |

## <a name="sfctl-container-invoke-api"></a>sfctl-tároló meghívása – API
A Container API meghívása egy Service Fabric csomóponton telepített tárolón az adott programkódhoz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Code-Package-instance-ID [kötelező] | Olyan azonosító, amely egyedileg azonosítja a Service Fabric-csomóponton üzembe helyezett Code Package-példányt. <br><br> A "Service Code-Package-List" lekérdezéssel kérhető le. |
| --Code-Package-Name [kötelező] | A szolgáltatás jegyzékfájljában megadott, Service Fabric fürtben egy alkalmazás típusának részeként regisztrált kódlap neve. |
| --Container-API-URI-elérési_út [kötelező] | Tároló REST API URI elérési útja, használja a (z) {ID} nevet a tároló neve/azonosítója helyett. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-manifest-Name [kötelező] | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| --Container-API-Body | A tároló REST API HTTP-kérelem törzse. |
| --Container-API-Content-Type | A tároló REST API tartalomtípusa alapértelmezett értéke az "Application/JSON". |
| --Container-API-http-művelet | A tároló REST API HTTP-művelete alapértelmezett értéke a beolvasás. |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-container-logs"></a>sfctl-tároló naplói
Beolvassa a Service Fabric csomóponton üzembe helyezett tároló naplófájljait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Code-Package-instance-ID [kötelező] | Kód csomag példányának azonosítója, amelyet a "Service Code-Package-List" paranccsal kérhet le. |
| --Code-Package-Name [kötelező] | A szolgáltatás jegyzékfájljában megadott, Service Fabric fürtben egy alkalmazás típusának részeként regisztrált kódlap neve. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-manifest-Name [kötelező] | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| – farok | A naplók végétől megjelenítendő sorok száma. Az alapértelmezett érték a 100. az "all" (összes) a teljes naplókat jeleníti meg. |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.