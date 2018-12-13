---
title: Az Azure Service Fabric parancssori felület - sfctl háló code-package-log |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl háló code-package-log-parancsokat.
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
ms.openlocfilehash: 81ddcc8c5685a839afabc1e82ecf4246cb813c21
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285094"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl háló code-package-log
A naplók lekérése a tároló a megadott kód csomag az adott szolgáltatás replika.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Get | A naplók lekérdezi a tárolóból. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl-háló code-package-log get
A naplók lekérdezi a tárolóból.

Beolvassa a naplókat a tárolót a megadott kód csomag a service replika.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-neve – alkalmazás-neve [kötelező] | Az alkalmazás neve. |
| – [kötelező] code-package-neve | Kódcsomag a szolgáltatás neve. |
| – [kötelező] replika neve | A Service Fabric-replika nevét. |
| – [kötelező] szolgáltatás neve | A szolgáltatás neve. |
| --tail | A naplók végéről sorok száma. Alapértelmezett érték 100. az "összes" a teljes körű naplók megjelenítése. |

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