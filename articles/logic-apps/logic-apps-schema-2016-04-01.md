---
title: Séma frissítések június-1-2016
description: Frissített séma 2016-06-01-es verziója az Azure Logic Apps logikai alkalmazásdefinícióihoz
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: e2f65f1c52dc7dfb2e4e4bf66f5c7e82f4b802b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792877"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Sémafrissítések az Azure Logic Apps alkalmazáshoz – 2016.

Az Azure Logic Apps [frissített séma-](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) és API-verziója olyan kulcsfontosságú fejlesztéseket tartalmaz, amelyek megbízhatóbbá és könnyebben használhatóvá teszik a logikai alkalmazásokat:

* [A hatókörök](#scopes) lehetővé teszik a műveletek csoportosítását vagy beágyazását műveletek gyűjteményeként.
* [A feltételek és hurkok](#conditions-loops) mostantól első osztályú műveletek.
* Pontosabb rendelés a futó `runAfter` műveletek hez az ingatlannal,`dependsOn`

A 2015. [check out the upgrade section](#upgrade-your-schema)

<a name="scopes"></a>

## <a name="scopes"></a>Hatókörök

Ez a séma hatóköröket tartalmaz, amelyek lehetővé teszik a műveletek csoportosítását, illetve a műveletek egymásba ágyazását. Egy feltétel például tartalmazhat egy másik feltételt. További információ [a hatókör szintaxisáról](../logic-apps/logic-apps-loops-and-scopes.md), vagy tekintse át ezt az alapvető hatókört:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Feltételek és hurkok változásai

A korábbi sémaverziókban a feltételek és a hurkok egyetlen művelethez társított paraméterek voltak. Ez a séma megszünteti ezt a korlátozást, így a feltételek és hurkok már elérhetők művelettípusként. További információ [a hurkokról és a hatókörökről](../logic-apps/logic-apps-loops-and-scopes.md), [feltételekről,](../logic-apps/logic-apps-control-flow-conditional-statement.md)vagy tekintse át ezt az alapvető példát, amely feltételműveletet mutat be:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>'runAfter' tulajdonság

A `runAfter` tulajdonság `dependsOn`lecseréli a tulajdonságot, így nagyobb pontosságot biztosít, amikor a műveletek futtatási sorrendjét a korábbi műveletek állapota alapján adja meg. A `dependsOn` tulajdonság azt jelezte, hogy "a művelet futott és sikeres volt", attól függően, hogy az előző művelet sikeres volt, sikertelen vagy kimarad-e – nem pedig azt, hogy hányszor kívánta futtatni a műveletet. A `runAfter` tulajdonság rugalmasságot biztosít objektumként, amely megadja az összes olyan műveletnevet, amely után az objektum fut. Ez a tulajdonság olyan állapotok tömbjét is definiálja, amelyek eseményindítókként elfogadhatók. Ha például azt szeretné, hogy egy művelet az A művelet sikeres legyen, és `runAfter` a B művelet sikeres vagy sikertelen is legyen, állítsa be ezt a tulajdonságot:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>A séma frissítése

A legújabb [sémára](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)való frissítéshez csak néhány lépést kell tennie. A frissítési folyamat magában foglalja a frissítési parancsfájl futtatását, mentésúj logikai alkalmazásként, és ha szeretné, esetleg felülírja az előző logikai alkalmazást.

1. Nyissa meg a logikai alkalmazást az Azure Portalon.

2. Nyissa meg az **Áttekintés című témakört.** A logikai alkalmazás eszköztárán válassza a **Séma frissítése**lehetőséget.
   
   ![Séma frissítésének kiválasztása][1]
   
   A frissített definíciót adja vissza, amelyet szükség esetén átmásolhat és beilleszthet egy erőforrás-definícióba. 

   > [!IMPORTANT]
   > *Győződjön meg arról, hogy* a **Mentés másként** lehetőséget választotta, hogy az összes kapcsolathivatkozás érvényes maradjon a frissített logikai alkalmazásban.

3. A frissítési panel eszköztárán válassza a **Mentés másként**lehetőséget.

4. Adja meg a logika nevét és állapotát. A frissített logikai alkalmazás telepítéséhez válassza a **Létrehozás lehetőséget.**

5. Győződjön meg arról, hogy a frissített logikai alkalmazás a várt módon működik.
   
   > [!NOTE]
   > Ha kézi vagy kérelem eseményindítót használ, a visszahívási URL-cím megváltozik az új logikai alkalmazásban. Tesztelje az új URL-címet, és győződjön meg arról, hogy a végpontok között a felhasználói élmény működik. A korábbi URL-címek megőrzése érdekében klónozhatja a meglévő logikai alkalmazást.

6. *Nem kötelező* Ha felül szeretné írni az előző logikai alkalmazást az új sémaverzióval, az eszköztáron válassza a **Klónozás**lehetőséget a **Séma frissítése**gomb mellett. Ez a lépés csak akkor szükséges, ha meg szeretné tartani ugyanazt az erőforrás-azonosítót, vagy a logikai alkalmazás aktiválási URL-címét szeretné megtartani.

## <a name="upgrade-tool-notes"></a>A frissítési eszköz jegyzetei

### <a name="mapping-conditions"></a>Leképezési feltételek

A frissített definícióban az eszköz a lehető legjobban igyekszik a valós és hamis elágazási műveleteket hatókörként csoportosítani. Pontosabban, a tervező `@equals(actions('a').status, 'Skipped')` minta `else` jelenik meg, mint egy művelet. Ha azonban az eszköz felismerhetetlen mintákat észlel, az eszköz külön feltételeket teremthet mind az igaz, mind a hamis ághoz. Szükség esetén a frissítés után újra leképezheti a műveleteket.

#### <a name="foreach-loop-with-condition"></a>"foreach" hurok állapotban

Az új séma, használhatja a szűrő művelet replikálni a mintát, amely egy **minden** ciklushoz egy feltételt elemenként. A módosítás azonban automatikusan megtörténik a frissítés kor. A feltétel egy szűrőműveletté válik, amely az **egyes ciklusok** előtt jelenik meg, és csak egy tömbnyi elemet ad vissza, amelyek megfelelnek a feltételnek, és átadja a tömböt **az egyes** műveletekhez. Például lásd: [Hurkok és hatókörök](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Erőforráscímkék

A frissítés után az erőforráscímkék törlődnek, ezért alaphelyzetbe kell állítania őket a frissített munkafolyamathoz.

## <a name="other-changes"></a>További változások

### <a name="renamed-manual-trigger-to-request-trigger"></a>Átnevezett "manuális" eseményindító "kérés" eseményindítóra

Az `manual` eseményindító típuselavult, és átnevezték a `request` típusra. `http` Ez a módosítás több konzisztenciát hoz létre az eseményindító létrehozásához használt minta létrehozásához.

### <a name="new-filter-action"></a>Új "szűrő" művelet

Ha egy nagy tömböt kisebb elemcsoportokra szeretne `filter` szűrni, az új típus elfogad egy tömböt és egy feltételt, kiértékeli az egyes elemek feltételét, és egy olyan tömböt ad vissza, amelynek elemei megfelelnek a feltételnek.

### <a name="restrictions-for-foreach-and-until-actions"></a>Korlátozások a "foreach" és a "until" intézkedésekre

A `foreach` `until` és a ciklus egyetlen műveletre korlátozódik.

### <a name="new-trackedproperties-for-actions"></a>Új "nyomon követett tulajdonságok" a műveletekhez

A műveletek most már `trackedProperties`rendelkezhetnek egy további `runAfter` `type` tulajdonsággal, amely a és a tulajdonságok testvére. Ez az objektum bizonyos műveletbemeneteket vagy kimeneteket határoz meg, amelyeket az Azure diagnosztikai telemetriába szeretne felvenni, és amelyet egy munkafolyamat részeként ad ki. Példa:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>További lépések

* [Munkafolyamat-definíciók létrehozása logikai alkalmazásokhoz](../logic-apps/logic-apps-author-definitions.md)
* [A logikai alkalmazás üzemelő példányának automatizálása](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
