---
title: Azure Service Fabric parancssori felület –-sfctl háló hálózat |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl háló hálózati parancsait.
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
ms.openlocfilehash: 8828605241d55ac6f9ce789ccc55c30f2cd36434
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285206"
---
# <a name="sfctl-mesh-network"></a>sfctl-háló hálózati
GET, és törölje a háló hálózati erőforrásokhoz.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | A hálózati erőforrás törlése. |
| lista | A hálózati erőforrások listája. |
| Show | A megadott nevű hálózati erőforrás beolvasása. |

## <a name="sfctl-mesh-network-delete"></a>sfctl-háló hálózati delete
A hálózati erőforrás törlése.

Törli a hálózati erőforrás nevével.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --name -n [kötelező] | A hálózat nevét. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-network-list"></a>sfctl-háló hálózati listája
A hálózati erőforrások listája.

Az összes hálózati erőforrás adatainak beolvasása egy adott erőforráscsoportban. A leírás és a hálózat többi tulajdonságait tartalmazza.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-network-show"></a>sfctl-háló hálózati show
A megadott nevű hálózati erőforrás beolvasása.

A megadott nevű hálózati erőforrás adatainak beolvasása. A leírás és a hálózat többi tulajdonságait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --name -n [kötelező] | A hálózat nevét. |

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