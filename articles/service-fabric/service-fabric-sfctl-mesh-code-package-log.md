---
title: Azure Service Fabric CLI-sfctl Mesh Code-Package-log | Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl Mesh Code-Package-log parancsokat.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: d1f0e34389a48b79c049f26e8b04c870f0f1a9a7
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901265"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh kódcsomagnaplója
Szerezze be a naplófájlokat az adott szolgáltatás replikájának megadott csomagjának tárolójában.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Get | Lekéri a tárolóból a naplókat. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl Mesh Code-Package-log Get
Lekéri a tárolóból a naplókat.

Beolvassa a szolgáltatás replikájának megadott kódjának tárolóját tartalmazó naplókat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --App-Name--Application-Name [kötelező] | Az alkalmazás neve. |
| --Code-Package-Name [kötelező] | A szolgáltatás kódjának neve. |
| --replika-név [kötelező] | Service Fabric replika neve. |
| – szolgáltatásnév [kötelező] | A szolgáltatás neve. |
| – farok | A naplók végétől megjelenítendő sorok száma. Az alapértelmezett érték a 100. az "all" (összes) a teljes naplókat jeleníti meg. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.