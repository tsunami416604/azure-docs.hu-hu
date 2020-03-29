---
title: Az Azure Service Fabric CLI- sfctl hálótelepítés
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A Service Fabric Mesh erőforrások létrehozására vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906027"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh üzembe helyezése
Hozzon létre service fabric hálós erőforrásokat.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | Létrehozza a Service Fabric hálóerőforrásainak központi telepítését. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl háló központi telepítése
Létrehozza a Service Fabric hálóerőforrásainak központi telepítését.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --input-yaml-fájlok [Kötelező] | Vesszővel elválasztott relatív vagy abszolút fájl elérési utak az összes yaml fájlokat vagy relatív vagy abszolút elérési utat a könyvtár (rekurzív), amelyek yaml fájlokat. |
| --paraméterek | Egy yaml fájl vagy egy jsonobjektum relatív vagy abszolút elérési útja, amely a felülbírálandó paramétereket tartalmazza. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

Összesíti és telepíti az összes erőforrást a fürthöz a yaml fájlban említett paraméterek felülbírálásával
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

A címtárban lévő összes erőforrás takarásba helyezése és telepítése a yaml fájlban említett paraméterek felülbírálásával

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konszolidálja és telepíti a címtár összes erőforrását a fürtbe a közvetlenül json objektumként átadott paraméterek felülbírálásával
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.
