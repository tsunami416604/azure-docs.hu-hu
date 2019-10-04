---
title: Azure Service Fabric CLI – sfctl Mesh üzembe helyezése | Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl Mesh telepítési parancsait.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b3f506b46ef563f47fc7c67b759d3fcd08b7509d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035191"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh üzembe helyezése
Hozzon létre Service Fabric Mesh-erőforrásokat.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| create | Service Fabric Mesh-erőforrások központi telepítését hozza létre. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl háló üzembe helyezésének létrehozása
Service Fabric Mesh-erőforrások központi telepítését hozza létre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --input-YAML-Files [kötelező] | Az összes YAML-fájl vagy a YAML-fájlokat tartalmazó könyvtár relatív/abszolút elérési útjának vesszővel tagolt relatív/abszolút fájlelérési útja. |
| – paraméterek | Egy relatív/abszolút elérési út a YAML fájlhoz vagy egy JSON-objektumhoz, amely tartalmazza azokat a paramétereket, amelyeket felül kell bírálni. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

Összevonja és telepíti az összes erőforrást a fürtbe úgy, hogy felülbírálja a YAML fájlban említett paramétereket.

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

A YAML fájlban említett paraméterek felülbírálásával összevonja és telepíti a címtárban lévő összes erőforrást a fürtbe.

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Egy címtárban lévő összes erőforrás konszolidálása és üzembe helyezése a fürthöz a közvetlenül JSON-objektumként továbbított paraméterek felülbírálásával

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.