---
title: Az Azure Service Fabric parancssori felület - sfctl háló üzembe helyezése |} A Microsoft Docs
description: A Service Fabric parancssori felület sfctl háló üzembe helyezési parancsok ismerteti.
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
ms.openlocfilehash: b25384d8f3c6e41b6c5cca723d41b79f00b17494
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285444"
---
# <a name="sfctl-mesh-deployment"></a>sfctl-háló üzembe helyezés
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
| --input-yaml-fájlok [kötelező] | Vesszővel tagolt relatív és abszolút Fájlelérési utak a yaml-fájlokat vagy a relatív és abszolút elérési útját a könyvtárat (a rekurzív) yaml fájlokat tartalmaznak. |
| – Paraméterek | Relatív és abszolút elérési útját a yaml-fájlt vagy egy json-objektum, amely tartalmazza a következő paramétereket kell Alkalmazásszint felülbírálását. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

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