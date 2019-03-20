---
title: Séma frissítése – 1 – 2016. június – Azure Logic Apps |} A Microsoft Docs
description: Logikaialkalmazás-definíciók az Azure Logic Appsben a frissített sémaverzió 2016-06-01
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 6df29543df2b7b2609582f7e8dd9a0629182760c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849779"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>A sémafrissítések Azure Logic Apps – 2016. június 1-én

A [séma frissítése](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) és Azure Logic Apps API-verzió magában foglalja a kulcsfontosságú fejlesztések, amelyek a logic apps megbízhatóbb és könnyebben használható:

* [Hatókörök](#scopes) lehetővé teszik a csoport vagy a műveletek beágyazása műveletek gyűjteménye.
* [Feltételek és ciklusok](#conditions-loops) immár első osztályú műveletek.
* A futó műveleteket a pontosabb rendezése a `runAfter` tulajdonságot, és cserélje le `dependsOn`

A logic apps a 2015. augusztus 1-én előnézeti séma frissítése a 2016. június 1-én sémájának [tekintse meg a verziófrissítés szakasz](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Hatókörök

Ebben a sémában a hatókörök, amelyek segítségével a beágyazott műveletek belül egymással, és együtt műveleteit tartalmazza. Ha például egy feltétel egy másik feltétel is tartalmazhat. Tudjon meg többet [szintaxis hatókör](../logic-apps/logic-apps-loops-and-scopes.md), vagy tekintse át az ebben a példában alapszintű hatókör:

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

## <a name="conditions-and-loops-changes"></a>Feltételek és ciklusok módosítások

Előző séma verziója, a feltételek és a hurkok voltak kapcsolódó egyetlen művelettel. Ebben a sémában a korlátozás csoportosítását, így a feltételek és ciklusok most művelettípusok érhető el. Tudjon meg többet [ciklusok és hatóköröket](../logic-apps/logic-apps-loops-and-scopes.md), [feltételek](../logic-apps/logic-apps-control-flow-conditional-statement.md), vagy tekintse át az alapszintű példa bemutatja egy feltétel művelet:

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

## <a name="runafter-property"></a>"runAfter" tulajdonsága

A `runAfter` tulajdonság cserél `dependsOn`, a futtatási műveletek sorrendjét megadásakor pontossággal alapján az előző műveletek állapotának biztosítása. A `dependsOn` tulajdonság jelzi-e "a művelet futott, és sikeres volt-e", alapján-e az előző művelet sikeres, sikertelen, vagy kihagyott - nem hányszor szeretné futtatni a műveletet. A `runAfter` tulajdonság biztosítja a rugalmasságot olyan objektum, amely meghatározza az összes műveletet-neveket az objektum futtatása után. Ez a tulajdonság azt is meghatározza, elfogadható eseményindítóként állapotait tömbjét. Például, ha szeretné a futtatását, miután A művelet sikeres lesz, és is utáni művelet B sikeres vagy sikertelen művelet, állítsa be ennek `runAfter` tulajdonság:

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

Frissítés a [legutóbbi séma](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), kell csak igénybe vehet néhány lépést. A frissítési folyamat magában foglalja a frissítési parancsprogram futtatása egy új logikai alkalmazás mentése, és ha azt szeretné, valószínűleg a korábbi logikai alkalmazás felülírása.

1. Az Azure Portalon nyissa meg a logikai alkalmazást.

2. Lépjen a **áttekintése**. A logikai alkalmazás eszköztáron válassza **Update Schema**.
   
   ![Válassza ki a séma frissítése][1]
   
   A frissített definíció adja vissza, amely, másolja és illessze be egy erőforrás-definíció szükség esetén. 

   > [!IMPORTANT]
   > *Győződjön meg arról,* választja **Mentés másként** így minden kapcsolatreferencia továbbra is érvényesek a frissített logikai alkalmazásban.

3. A frissítési panel eszköztárán válassza a **Mentés másként**.

4. Adja meg a logikai nevét és állapotát. A frissített logikai alkalmazás üzembe helyezéséhez válassza **létrehozás**.

5. Győződjön meg arról, hogy a frissített logikai alkalmazás a várt módon működik-e.
   
   > [!NOTE]
   > Ha egy manuális vagy a kérelem eseményindítót használ, a visszahívási URL-Címének módosítja az új logikai alkalmazás. Tesztelje az új URL-címet, hogy a teljes körű felhasználói élményt működését. Előző URL-címek megőrzéséhez klónozhat keresztül a meglévő logikai alkalmazás.

6. *Nem kötelező* írja felül a korábbi logikai alkalmazást az új sémaverzióra, az eszköztáron válassza a **Klónozás**mellett található **Update Schema**. Ebben a lépésben szükség, csak ha az erőforrás-Azonosítóját megőrizni, vagy kérje meg a logikai alkalmazás aktiváló URL-címe.

## <a name="upgrade-tool-notes"></a>Eszköz megjegyzések frissítése

### <a name="mapping-conditions"></a>Leképezési feltételek

A frissített definíciójában az eszköz lehetővé teszi a legjobb sikeresebbé: true és false ág műveletek csoportosítása hatóköreként. Pontosabban, a Tervező mintáját `@equals(actions('a').status, 'Skipped')` jelenik meg egy `else` művelet. Azonban az eszköz nem felismerhető minták észleli, ha az eszköz lehet, hogy hozzon létre külön feltételek az igaz értékre mind a False (hamis) ágat. A frissítés után is újramegfeleltetése műveletek, ha szükséges.

#### <a name="foreach-loop-with-condition"></a>a feltétel "foreach" ciklus

Az új sémában, használhatja a szűrési művelet replikálni, amely a minta egy **minden** hurok elemenként egy feltétellel. Azonban a változás automatikusan megtörténik, amikor frissít. Szűrőművelet előtt megjelenő lesz a **minden** ciklus, csak a feltételnek megfelelő elemek tömbjét visszaadása, és átadja a tömböt az **minden** művelet. Egy vonatkozó példáért lásd: [ciklusok és hatóköröket](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Erőforráscímkék

A frissítés befejezése után az erőforráscímkék törlődnek, így a frissített munkafolyamat alaphelyzetbe kell azokat.

## <a name="other-changes"></a>Egyéb módosítások

### <a name="renamed-manual-trigger-to-request-trigger"></a>A "request" eseményindító átnevezve: "manual" eseményindító

A `manual` típusú trigger elavult, és a nevet kapott: `request` típusú `http`. Ez a változás létrehoz több konzisztencia minta típusának, amely az eseményindító segítségével hozhatók létre.

### <a name="new-filter-action"></a>Új "filter" művelet

Egy nagy tömbbel kisebb tételcsoport, lefelé szűrése az új `filter` típus fogad el, egy tömböt és egy feltétel, kiértékeli a feltételt, az egyes elemekhez és egy tömböt ad vissza, az elemek felel meg a feltételnek.

### <a name="restrictions-for-foreach-and-until-actions"></a>Korlátozások a "foreach" és "until" műveletek

A `foreach` és `until` hurok egyetlen művelettel korlátozódnak.

### <a name="new-trackedproperties-for-actions"></a>Új "trackedProperties" műveletek esetében

Műveletek is most már rendelkezik egy új tulajdonság nevű `trackedProperties`, testvére, azaz a `runAfter` és `type` tulajdonságait. Ez az objektum meghatározza bizonyos művelet bemenetei között meg vagy az Azure diagnosztikai telemetriát, munkafolyamat részeként kibocsátott szerepeltetni kívánt kimenetek. Példa:

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
* [A logic apps munkafolyamat-meghatározások létrehozása](../logic-apps/logic-apps-author-definitions.md)
* [A logic apps üzembehelyezési sablonok létrehozása](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
