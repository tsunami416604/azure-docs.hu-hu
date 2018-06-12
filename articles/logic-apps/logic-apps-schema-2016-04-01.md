---
title: Séma frissítések 2016. június-1-- Azure Logic Apps |} Microsoft Docs
description: Az Azure Logic Apps JSON-meghatározások létrehozása séma 2016-06-01-es verziójával
author: jeffhollan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 1cbb20a96f39ffa6f4c7375a38b3bf8046d0bd3a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299691"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Az Azure Logic Apps – 2016. június 1 sémafrissítések

A [sémája frissítve](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) és az Azure Logic Apps az API-verziót tartalmazza a fontos fejlesztést tartalmaz, amelyek a logic apps megbízhatóbb és könnyebben használható:

* [Hatókörök](#scopes) lehetővé teszik, hogy a csoport- vagy beágyazni műveletek műveletek gyűjteményeként.
* [Feltételek és a hurkok](#conditions-loops) első osztályú műveletek is.
* Pontosabb rendezést a műveletek futtatása a `runAfter` tulajdonság, cseréje `dependsOn`

A logic apps a 2015. augusztus 1 preview séma frissítéséhez a 2016. június 1 séma [tekintse meg a verziófrissítés szakasz](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Hatókörök

A séma tartalmaz a hatókörök, amelyek lehetővé teszik, hogy együtt műveleteit, vagy a nest műveletek belül egymással. Egy feltétel tartalmazhat például egy másik feltétel. További információ [szintaxis hatókör](../logic-apps/logic-apps-loops-and-scopes.md), vagy tekintse át a alapvető hatókör példa:

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
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

## <a name="conditions-and-loops-changes"></a>Feltételek és a hurkok módosítása

Előző séma verziója, a feltételek és a hurkok voltak egyetlen művelettel társított paraméter. Ebben a sémában liftek ezt a korlátozást, így a feltételek és a hurkok megjelenni művelettípusok. További információ [hurkok és hatókörök](../logic-apps/logic-apps-loops-and-scopes.md), vagy tekintse át az egyszerű példában feltétel művelethez:

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>"runAfter" tulajdonság

A `runAfter` tulajdonság cserél `dependsOn`, nyújtó további pontosság megadása a futtatási ahhoz, hogy a műveletek előző műveletek állapota alapján.

A `dependsOn` azonos a "a művelet futtatása és sikeres volt-e" tulajdonsága, nem számít, hány alkalommal szeretné alapján, hogy az előző művelet sikeres volt, a művelet végrehajtása sikertelen volt, vagy kihagyott. A `runAfter` tulajdonság olyan objektum, amely után az objektum fut művelet nevét adja meg, hogy rugalmasságot biztosít. Ez a tulajdonság is megadja elfogadható eseményindítóként használható állapotait tömbjét. Például ha végezze el a lépést A sikeres és is után B lépésben sikeres vagy sikertelen lesz, hozhat létre a `runAfter` tulajdonság:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>A séma frissítése

Frissítéséhez a [legutóbbi séma](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), akkor kell csak néhány lépéseket fogja végrehajtani. A frissítési folyamat magában foglalja a frissítési parancsprogram futtatása mentése új logikai alkalmazás, és ha azt szeretné, valószínűleg felülírja az előző logikai alkalmazást.

1. Nyissa meg a Logic Apps alkalmazást az Azure-portálon.

2. Ugrás a **áttekintése**. A logic app eszköztáron válassza **frissítés séma**.
   
    ![Válassza ki a séma frissítése][1]
   
    A frissített definícióban ad vissza, amelyet másolja és illessze be az erőforrás-definíció szükség esetén. 
    Azonban azt **erősen ajánlott** választja **Mentés másként** győződjön meg arról, hogy az összes kapcsolat érvényesek a frissített logikai alkalmazás.

3. A frissítési panel eszköztáron válassza **Mentés másként**.

4. Adja meg a logikai nevét és állapotát. A frissített Logic Apps alkalmazást telepíteni, válassza ki a **létrehozása**.

5. Győződjön meg arról, hogy a frissített logikai alkalmazás megfelelően működik-e.
   
   > [!NOTE]
   > Manuális vagy kérelem eseményindítót használ, ha a visszahívás URL-címet az új logikai alkalmazás változik. Teszteléséhez győződjön meg arról, hogy a végpont élmény működik az új URL-címet. Előző URL-címek megőrzéséhez a meglévő Logic Apps alkalmazást keresztül tud klónozni.

6. *Nem kötelező* felülírja az előző logikai alkalmazást az új verzióval séma, az eszköztáron válassza **Klónozás**mellett található **frissítés séma**. Ez a lépés szükség, csak akkor, ha szeretné megtartani a azonos erőforrás-azonosító, vagy kérje meg a Logic Apps alkalmazást indítási URL-CÍMÉT.

### <a name="upgrade-tool-notes"></a>Frissítési eszköz megjegyzések

#### <a name="mapping-conditions"></a>Leképezési feltételek

A frissített definícióban az eszköz lehetővé teszi egy, a true és false fiókirodai műveletek csoportosítása hatóköreként a lehető legkedvezőbb módon. Pontosabban, a Tervező mintáját `@equals(actions('a').status, 'Skipped')` meg kell jelennie egy `else` művelet. Azonban az eszköz felismerhetetlen minták észleli, ha az eszköz lehet, hogy hozzon létre külön feltételek a true és a hamis ág. Műveletek leképezheti a frissítés után is, ha szükséges.

#### <a name="foreach-loop-with-condition"></a>feltételt tartalmazó "foreach" hurok

Az új sémával, használhatja a szűrési művelet replikálja a mintát egy `foreach` elemenként, eltérő feltételt tartalmazó hurok, de ezt a változtatást automatikusan történjen, amikor frissít. A feltételnek megfelelő elemek csak egy tömb visszaküldésére használatos a foreach hurok előtt szűrőművelet lesz, és, hogy tömb átad a foreach műveletet. Egy vonatkozó példáért lásd: [hurkok és hatókörök](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Az erőforráscímkék

A frissítés befejezése után az erőforráscímkék eltávolítja, így azokat a frissített munkafolyamat kell visszaállítani.

## <a name="other-changes"></a>Az egyéb módosítások

### <a name="renamed-manual-trigger-to-request-trigger"></a>A "kérelem" eseményindító átnevezett "manual" eseményindító

A `manual` indítási típus elavult, és a átnevezésre `request` típusú `http`. Ez a változás hoz létre további konzisztencia mintát típusú, amely az eseményindító segítségével hozhatók létre.

### <a name="new-filter-action"></a>Új "filter" művelet

Elemek körét le egy nagy tömböt szűrése az új `filter` típus elfogadja a tömb és egy feltétel, az egyes elemekhez tartozó feltétel eredménye és tömböt ad vissza a feltétel értekezlet elemekhez.

### <a name="restrictions-for-foreach-and-until-actions"></a>A "foreach" és "csak" műveletek korlátozása

A `foreach` és `until` hurok csak egyetlen művelettel.

### <a name="new-trackedproperties-for-actions"></a>Új "trackedProperties" műveletek

Műveletek is most már rendelkezik egy további tulajdonság nevű `trackedProperties`, ez az a testvér a `runAfter` és `type` tulajdonságok. Ezt az objektumot határozza meg az egyes művelet bemeneti vagy kimeneti munkafolyamat részeként kibocsátott Azure diagnosztikai telemetriai adatok szerepeltetni kívánt. Példa:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
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
* [A logic Apps alkalmazások központi telepítési sablonok létrehozása](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
