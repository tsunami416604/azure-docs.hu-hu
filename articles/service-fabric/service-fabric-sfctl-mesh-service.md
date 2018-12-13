---
title: Azure Service Fabric parancssori felület –-sfctl háló szolgáltatás |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl háló szolgáltatás parancsaihoz.
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 6c40cf2340568e704a5c76ad367c07c85826943b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284800"
---
# <a name="sfctl-mesh-service"></a>sfctl-háló szolgáltatás
Szolgáltatás részleteinek és a szolgáltatások listája egy alkalmazás-erőforrás lekérése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| lista | A szolgáltatás erőforrásainak listája. |
| Show | Lekérdezi a szolgáltatás-erőforrás a megadott névvel. |

## <a name="sfctl-mesh-service-list"></a>sfctl-háló szolgáltatás listája
A szolgáltatás erőforrásainak listája.

Az összes szolgáltatás egy alkalmazás-erőforrás adatainak beolvasása. A leírás és a szolgáltatás egyéb tulajdonságait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-neve – alkalmazás-neve [kötelező] | Az alkalmazás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-service-show"></a>sfctl-háló service show
Lekérdezi a szolgáltatás-erőforrás a megadott névvel.

A megadott nevű a szolgáltatás-erőforrás adatainak beolvasása. A leírás és a szolgáltatás egyéb tulajdonságait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-neve – alkalmazás-neve [kötelező] | Az alkalmazás neve. |
| --name -n [kötelező] | A szolgáltatás neve. |

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