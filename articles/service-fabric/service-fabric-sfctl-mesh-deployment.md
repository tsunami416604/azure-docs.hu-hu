---
title: Az Azure Service Fabric parancssori felület - sfctl háló üzembe helyezése |} A Microsoft Docs
description: A Service Fabric parancssori felület sfctl háló üzembe helyezési parancsok ismerteti.
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
ms.openlocfilehash: e6b484dabd77a142961db2d97242896790fa3d8b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668466"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh üzembe helyezése
Service Fabric-háló az erőforrások létrehozásához.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | Hozza létre a Service Fabric-háló erőforrások üzembe helyezése. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl-háló üzembe helyezés létrehozása
Hozza létre a Service Fabric-háló erőforrások üzembe helyezése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --input-yaml-files [Required] | Vesszővel tagolt relatív és abszolút Fájlelérési utak a yaml-fájlokat vagy a relatív és abszolút elérési útját a könyvtárat (a rekurzív) yaml fájlokat tartalmaznak. |
| – Paraméterek | Relatív és abszolút elérési útját a yaml-fájlt vagy egy json-objektum, amely tartalmazza a következő paramétereket kell Alkalmazásszint felülbírálását. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

### <a name="examples"></a>Példák

Vonja össze és üzembe helyezi a fürt letiltásával a paramétereket a yaml-fájlt az említett összes erőforrás

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Összesíti és telepít egy könyvtárban található összes erőforrást fürt letiltásával a yaml-fájl paraméterekkel.

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Vonja össze és helyez üzembe egy könyvtárban található összes erőforrást fürthöz letiltásával a paraméterek, amelyek adhatók közvetlenül be a json-objektum

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>További lépések
- [Állítsa be a](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).