---
title: Az Azure Service Fabric parancssori felület - sfctl háló és a replikakiszolgáló |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl háló és a replikakiszolgáló parancsokat.
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
ms.openlocfilehash: 1e0955ef2a52a6313f0449b956229e03f0a5b5e8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662431"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh szolgáltatásreplika
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
| --app-name --application-name [Required] | Az alkalmazás neve. |
| – [kötelező] szolgáltatás neve | A szolgáltatás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl-háló és a replikakiszolgáló show
A szolgáltatás egy alkalmazás az adott replika beolvasása.

A megadott nevű a szolgáltatás replika adatainak beolvasása. A leírás és a szolgáltatás replikája egyéb tulajdonságait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-name --application-name [Required] | Az alkalmazás neve. |
| --name -n [kötelező] | A szolgáltatás-replika nevét. |
| – [kötelező] szolgáltatás neve | A szolgáltatás neve. |

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