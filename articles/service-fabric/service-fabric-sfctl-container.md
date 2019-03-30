---
title: Az Azure Service Fabric parancssori felület - sfctl container |} A Microsoft Docs
description: A Service Fabric parancssori felület sfctl tárolóparancsok ismerteti.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: a5037c535737946a50d8af6fa60d0815120276d9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663706"
---
# <a name="sfctl-container"></a>sfctl-tároló
Tároló futtatási kapcsolatos parancsok egy fürtcsomóponton.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| invoke-api | Egy üzembe helyezett egy Service Fabric-csomóponton, az adott kódcsomaghoz tárolón a tároló API meghívásához. |
| naplók | A tároló üzembe helyezett egy Service Fabric-csomóponton, az adott kódcsomaghoz tároló naplóinak beolvasása. |

## <a name="sfctl-container-invoke-api"></a>sfctl tároló invoke-api
Egy üzembe helyezett egy Service Fabric-csomóponton, az adott kódcsomaghoz tárolón a tároló API meghívásához.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. <br><br> Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --code-package-példány-id [kötelező] | Amely egyedileg azonosítja az egy kódot csomag-példány üzembe helyezett egy service fabric-csomópont azonosítója. <br><br> "Szolgáltatás code-package-list" lekérhetők. |
| – [kötelező] code-package-neve | A szolgáltatásjegyzék-alkalmazástípus a Service Fabric-fürt részeként regisztrált megadott kód csomag neve. |
| --container-api-uri-elérési út [kötelező] | Tároló REST API-URI elérési út a(z) {id} használata helyett a tároló neve vagy azonosítója. |
| --csomópontnév [kötelező] | A csomópont neve. |
| --service-jegyzékfájl-name [kötelező] | A szolgáltatásjegyzék-alkalmazástípus a Service Fabric-fürt részeként regisztrált neve. |
| --container-api-body | HTTP-kérés törzse REST API-tárolóhoz. |
| --container-api-content-type | Typ obsahu tároló REST API-t alapértelmezés szerint az "application/json". |
| --container-api-http-verb | Alapértelmezés szerint GET REST API-t tároló HTTP-műveletet. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-container-logs"></a>sfctl-tároló naplóit
A tároló üzembe helyezett egy Service Fabric-csomóponton, az adott kódcsomaghoz tároló naplóinak beolvasása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. <br><br> Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --code-package-példány-id [kötelező] | Kód csomag példány azonosítója, amelyet a "service code-package-list" lekérheti. |
| – [kötelező] code-package-neve | A szolgáltatásjegyzék-alkalmazástípus a Service Fabric-fürt részeként regisztrált megadott kód csomag neve. |
| --csomópontnév [kötelező] | A csomópont neve. |
| --service-jegyzékfájl-name [kötelező] | A szolgáltatásjegyzék-alkalmazástípus a Service Fabric-fürt részeként regisztrált neve. |
| --tail | A naplók végéről sorok száma. Alapértelmezett érték 100. az "összes" a teljes körű naplók megjelenítése. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |


## <a name="next-steps"></a>További lépések
- [Állítsa be a](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).