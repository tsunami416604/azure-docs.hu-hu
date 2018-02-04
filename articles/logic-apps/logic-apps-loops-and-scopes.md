---
title: "Hurok- és hatókörök létrehozása vagy adatok a munkafolyamatokban - Azure Logic Apps debatch |} Microsoft Docs"
description: "Az adatok, a hatókörök, műveleteit iterációt hurkok létrehozása, vagy adatokat az Azure Logic Apps további munkafolyamatok indítására debatch."
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 64b8f414efe8cd886589084f05e04486c9a0d05c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Logic Apps-hurkok, -hatókörök és -kibontás
  
A Logic Apps használata tömbök, gyűjtemények, kötegekben, számos, és fut a hurokban, a munkafolyamaton belül.
  
## <a name="foreach-loop-and-arrays"></a>ForEach hurok és tömbök
  
A Logic Apps segítségével hurokba építhető adatok, és egy műveletet minden elemhez.  Egy gyűjtemény ismétlési keresztül lehetséges a `foreach` művelet.  A Tervező adhat hozzá egy for-each ciklus.  A tömb kívánja az ismétlés kiválasztása, után elkezdhet műveletek.  Több műveletek másodpercenkénti foreach hurok adhat hozzá.  Egyszer a hurkon belül elkezdheti adja meg, hogy mi történjen a tömb egyes értéken.

  Ebben a példában az összes e-mail címet, amely tartalmazza a "microsoft.com" e-mail küldése. Kód-nézet használata esetén is megadhat egy for-each ciklus az alábbi példához hasonló:

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` művelet is ismétlés tömbök több ezer entitást.  Alapértelmezés szerint az ismétlés végre párhuzamosan.  Lásd: [korlátozásai és konfigurációja](logic-apps-limits-and-config.md) részleteinek a tömb és feldolgozási korlátok.

### <a name="sequential-foreach-loops"></a>Szekvenciális ForEach hurkok

Foreach hurkot hajtható végre az egymás után, hogy a `Sequential` műveletet hozzá kell adni.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Hurok-ig
  
  Művelet vagy műveletsorozattal hajthat végre, amíg egy feltétel nem teljesül.  A leggyakoribb helyzetben használhat egy amíg hurok hívja a következő végpont csak a keresett választ kapott.  A tervezőben, megadhat hozzáadása egy hurok-ig.  Miután hozzáadta a hurok található műveletek, beállíthatja a kilépési feltételt, valamint a hurok korlátok.
  
  Ez a példa egy HTTP-végpont meghívja a mindaddig, amíg az adott válasz törzsének értéke "Befejezve".  Ha befejeződött vagy: 
  
  * HTTP-válasz "Befejezve" állapota
  * Egy órára próbált
  * Az rendelkezik beállításpanelen 100 alkalommal
  
  Kód-nézet használata esetén is megadhat egy csak az alábbi példához hasonló hurok:
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn és debatching

Néha egy eseményindító kaphat debatch és elindítsanak egy munkafolyamatot elemenként kívánt elemek tömbjét.  A debatching pedig segítségével konfigurálhatók a `spliton` parancsot.  Alapértelmezés szerint az eseményindító swagger határozza meg a hasznos adatok között, amely tömb, ha egy `spliton` jelenik meg. A `spliton` indítja el a elemenként futtató a tömbben.  SplitOn csak egy eseményindítót, amely kézzel konfigurált vagy felül lehet hozzáadni. Nem lehet egy `spliton` és a szinkron válasz mintát is megvalósíthatja.  Minden munkafolyamat neve, amely rendelkezik egy `response` művelet kívül `spliton` aszinkron módon futtatja, és elküldi egy azonnali `202 Accepted` választ.  

  Ebben a példában egy tömb elemek fogad, és az egyes sorokkal debatches. Az alábbi példa-kódnézetben SplitOn adhat meg:

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Hatókörök

A hatókör együttes használatával műveletsorozat csoportosításához lehetőség.  Hatókörök hasznosak kivételkezelést megvalósításához.  A tervezőben új hatókör hozzáadása, és megkezdheti saját műveletek hozzáadását.  Az alábbi példához hasonló kódnézetben hatókörök adhatók meg:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
