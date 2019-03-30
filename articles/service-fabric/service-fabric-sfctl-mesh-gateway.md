---
title: Azure Service Fabric parancssori felület –-sfctl háló átjáró |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl háló átjáró parancsokat.
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
ms.openlocfilehash: 27cfe380e5988f17ca79c430cde7defc7f7bc440
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661360"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh átjárója
GET és delete háló az átjáró-erőforrásokat.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Az átjáró-erőforrás törlése. |
| lista | Az átjáró-erőforrásokat sorolja fel. |
| Show | Lekérdezi a megadott nevű Gateway-erőforráshoz. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl-háló átjáró törlése
Az átjáró-erőforrás törlése.

A név által azonosított Gateway-erőforrás törlése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --name -n [kötelező] | Az átjáró-erőforrás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl-háló gateway list
Az átjáró-erőforrásokat sorolja fel.

Az összes átjáró-erőforrás adatainak beolvasása egy adott erőforráscsoportban. A leírás és egyéb az átjáró tulajdonságait tartalmazza.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl-háló gateway show
Lekérdezi a megadott nevű Gateway-erőforráshoz.

A megadott nevű Gateway-erőforrás adatainak beolvasása. A leírás és egyéb az átjáró tulajdonságait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --name -n [kötelező] | Az átjáró-erőforrás neve. |

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