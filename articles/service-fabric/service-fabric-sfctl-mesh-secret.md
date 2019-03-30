---
title: Az Azure Service Fabric parancssori felület - sfctl háló titkos kulcs |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl háló titkos parancsokat.
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
ms.openlocfilehash: acc5862a8794da4fde991025eacfcccf6e16916a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661207"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh titkos kódja
Töltse le és titkos háló-erőforrások törlése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | A titkos erőforrás törlése. |
| lista | A titkos erőforrások listája. |
| Show | Lekérdezi a titkos erőforrás a megadott névvel. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl-háló secret delete
A titkos erőforrás törlése.

Törli a megadott titkos erőforrás és az összes hozzá tartozó névvel ellátott értékek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --name -n [kötelező] | A titkos kulcs neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-secret-list"></a>sfctl háló secret list
A titkos erőforrások listája.

Az összes titkos erőforrás adatainak beolvasása egy adott erőforráscsoportban. A leírás és egyéb tulajdonságait a titkos kulcsot tartalmazza.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-secret-show"></a>sfctl-háló secret show
Lekérdezi a titkos erőforrás a megadott névvel.

A megadott nevű a titkos erőforrás adatainak beolvasása. A leírás és egyéb tulajdonságait a titkos kulcsot tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --name -n [kötelező] | A titkos kulcs neve. |

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