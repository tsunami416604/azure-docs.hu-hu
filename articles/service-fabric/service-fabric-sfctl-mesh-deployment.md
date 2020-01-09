---
title: Azure Service Fabric CLI – sfctl Mesh üzembe helyezése
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A Service Fabric Mesh-erőforrások létrehozására szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 6512cce075906fc8708a39fa0747ec27be33e961
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645361"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh üzembe helyezése
Hozzon létre Service Fabric Mesh-erőforrásokat.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | Service Fabric Mesh-erőforrások központi telepítését hozza létre. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl háló üzembe helyezésének létrehozása
Service Fabric Mesh-erőforrások központi telepítését hozza létre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --input-YAML-Files [kötelező] | Az összes YAML-fájl vagy a YAML-fájlokat tartalmazó könyvtár relatív/abszolút elérési útjának vesszővel elválasztott relatív/abszolút fájlelérési útja. |
| – paraméterek | Egy relatív/abszolút elérési út a YAML fájlhoz vagy egy JSON-objektumhoz, amely tartalmazza azokat a paramétereket, amelyeket felül kell bírálni. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
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

Egy címtárban lévő összes erőforrás konszolidálása és telepítése a fürthöz a közvetlenül JSON-objektumként átadott paraméterek felülbírálásával
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.