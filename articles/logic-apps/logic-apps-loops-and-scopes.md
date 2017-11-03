---
title: "Hurok- és hatókörök létrehozása vagy adatok a munkafolyamatokban - Azure Logic Apps debatch |} Microsoft Docs"
description: "Az adatok, a hatókörök, műveleteit iterációt hurkok létrehozása, vagy adatokat az Azure Logic Apps további munkafolyamatok indítására debatch."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 413a2ba9107ca259ed577825bf0a17ff5622f1ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Logic Apps-hurkok, -hatókörök és -kibontás
  
A Logic Apps használata tömbök, gyűjtemények, kötegekben, számos, és fut a hurokban, a munkafolyamaton belül.
  
## <a name="foreach-loop-and-arrays"></a>ForEach hurok és tömbök
  
A Logic Apps segítségével hurokba építhető adatok, és egy műveletet minden elemhez.  Ez az alkalmazáson keresztül a `foreach` művelet.  A tervezőben, megadhat hozzáadása egy for-each ciklus.  A tömb kívánja az ismétlés kiválasztása, után elkezdhet műveletek.  Jelenleg csak egy művelet foreach hurok száma korlátozott, de ez a korlátozás az elkövetkező hetektől azonnal fel kell oldani.  Egyszer a hurkon belül elkezdheti adja meg, hogy mi történjen a tömb egyes értéken.

Kód-nézet használata esetén is megadhat egy for-each ciklus például alatt.  Itt látható egy példa egy for-each ciklus, amelyet az összes e-mail címet, amely tartalmazza a "microsoft.com" az e-mailt küld:

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
  
  A `foreach` művelet is többször over tömbállandó legfeljebb 5000 sort.  Alapértelmezés szerint minden egyes ismétlés párhuzamosan fogja végrehajtani.  

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
  
  Művelet vagy műveletsorozattal hajthat végre, amíg egy feltétel nem teljesül.  A leggyakoribb forgatókönyvhöz, amelyben ez a végpont hívja meg addig, amíg a keresett választ kapott.  A tervezőben, megadhat hozzáadása egy hurok-ig.  Miután hozzáadta a hurok található műveletek, beállíthatja a kilépési feltételt, valamint a hurok korlátok.  Van egy 1 perc késleltetéssel a hurok ciklusok között.
  
  Kód-nézet használata esetén is megadhat egy amíg hurok, például alatt.  Itt látható egy példa egy HTTP-végpont meghívása mindaddig, amíg az adott válasz törzsének értéke "Befejezve".  A feladat befejeződik mikor vagy 
  
  * HTTP-válasz "Befejezve" állapota
  * 1 óráig próbált
  * Az rendelkezik beállításpanelen 100 alkalommal
  
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

Néha egy eseményindító kaphat debatch és elindítsanak egy munkafolyamatot elemenként kívánt elemek tömbjét.  Keresztül ehhez a `spliton` parancsot.  Alapértelmezés szerint az eseményindító swagger határozza meg a hasznos adatok között, amely tömb, ha egy `spliton` megjelenik, és indítsa el a elemenként futását.  SplitOn csak egy eseményindító lehet hozzáadni.  Ez manuálisan konfigurálhatók, vagy definition kódnézetben felülbírálható.  Jelenleg is debatch SplitOn tömbállandó legfeljebb 5 000 elemnél.  Nem lehet egy `spliton` és a szinkron válasz mintát is megvalósíthatja.  Minden munkafolyamat neve, amely rendelkezik egy `response` művelet kívül `spliton` aszinkron módon futnak le és azonnali küldés `202 Accepted` választ.  

Az alábbi példa-kódnézetben SplitOn adhat meg.  Ez elemek tömbje fogad, és az egyes sorokkal debatches.

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

A hatókör együttes használatával műveletsorozat csoportosításához lehetőség.  Ez különösen fontos kivételkezelést megvalósításához.  A tervezőben új hatókör hozzáadása, és megkezdheti saját műveletek hozzáadását.  Meghatározhatja a hatókörök kódnézetben a következőhöz hasonló:


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