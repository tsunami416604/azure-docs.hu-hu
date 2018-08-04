---
title: Az Azure Service Fabric parancssori felület - sfctl container |} A Microsoft Docs
description: A Service Fabric parancssori felület sfctl tárolóparancsok ismerteti.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 27108d27ee27346e4cba44e6778faff56df70a36
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495128"
---
# <a name="sfctl-container"></a>sfctl-tároló
Tároló futtatási kapcsolatos parancsok egy fürtcsomóponton.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| api meghívása | Tároló REST API meghívásához. |
| naplók | Tároló naplóinak beolvasása során. |

## <a name="sfctl-container-invoke-api"></a>sfctl tároló invoke-api
Tároló REST API meghívásához.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Identita aplikace. |
| --code-package-példány-id [kötelező] | Kód csomag példány azonosítója, amelyet a "service code-package-list" lekérheti. |
| – [kötelező] code-package-neve | Csomag neve. |
| --container-api-uri-elérési út [kötelező] | Tároló REST API-URI elérési út a(z) {id} használata helyett a tároló neve vagy azonosítója. |
| --csomópontnév [kötelező] | A csomópont neve. |
| --service-jegyzékfájl-name [kötelező] | Jegyzékfájl neve. |
| --container-api – törzs | HTTP-kérés törzse REST API-tárolóhoz. |
| --container-api-content-type | Typ obsahu tároló REST API-t alapértelmezés szerint az "application/json". |
| --container-api-http-művelet | Alapértelmezés szerint GET REST API-t tároló HTTP-műveletet. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-container-logs"></a>sfctl-tároló naplóit
Tároló naplóinak beolvasása során.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Identita aplikace. |
| --code-package-példány-id [kötelező] | Kód csomag példány azonosítója, amelyet a "service code-package-list" lekérheti. |
| – [kötelező] code-package-neve | Csomag neve. |
| --csomópontnév [kötelező] | A csomópont neve. |
| --service-jegyzékfájl-name [kötelező] | Jegyzékfájl neve. |
| --tail | Csak a naplókat végéről a napló sorok számát adja vissza. Adja meg egy egész számot vagy az összes kimeneti minden napló sort. Alapértelmezés szerint az "all". |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |


## <a name="next-steps"></a>További lépések
- [Állítsa be a](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).