---
title: Az Azure Service Fabric parancssori felület - sfctl háló és a replikakiszolgáló |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl háló és a replikakiszolgáló parancsokat.
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
ms.openlocfilehash: bcf4b8d013783a9fbdb62bcdb8737680bfce7640
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285087"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl-háló szolgáltatás-replika
Kérje le egy alkalmazás-erőforrást replikáinak részleteit és a egy adott szolgáltatás lista replikáját.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| lista | A szolgáltatás a replikák listája. |
| Show | A szolgáltatás egy alkalmazás az adott replika beolvasása. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl-háló és a replikakiszolgáló listája
A szolgáltatás a replikák listája.

A szolgáltatás összes replika adatainak beolvasása. A leírás és a szolgáltatás replikája egyéb tulajdonságait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-neve – alkalmazás-neve [kötelező] | Az alkalmazás neve. |
| – [kötelező] szolgáltatás neve | A szolgáltatás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl-háló és a replikakiszolgáló show
A szolgáltatás egy alkalmazás az adott replika beolvasása.

A megadott nevű a szolgáltatás replika adatainak beolvasása. A leírás és a szolgáltatás replikája egyéb tulajdonságait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-neve – alkalmazás-neve [kötelező] | Az alkalmazás neve. |
| --name -n [kötelező] | A szolgáltatás-replika nevét. |
| – [kötelező] szolgáltatás neve | A szolgáltatás neve. |

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