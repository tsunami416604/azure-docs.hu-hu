---
title: Azure Service Fabric parancssori felület –-sfctl háló szolgáltatás |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl háló szolgáltatás parancsaihoz.
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
ms.openlocfilehash: e8b735780f4ed3402845d9d401f8e37701b9a1a6
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667582"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh szolgáltatás
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
| --app-name --application-name [Required] | Az alkalmazás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-service-show"></a>sfctl-háló service show
Lekérdezi a szolgáltatás-erőforrás a megadott névvel.

A megadott nevű a szolgáltatás-erőforrás adatainak beolvasása. A leírás és a szolgáltatás egyéb tulajdonságait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-name --application-name [Required] | Az alkalmazás neve. |
| --name -n [kötelező] | A szolgáltatás neve. |

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