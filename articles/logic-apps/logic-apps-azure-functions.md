---
title: "Az Azure Logic Apps az Azure Functions egyéni kód |} Microsoft Docs"
description: "Hozzon létre és futtasson egyéni kód az Azure Logic Apps az Azure Functions"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 18442c87b049200fac5ed41cc7034ba7a848b8d3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>Adja hozzá, és egyéni kódot a logic Apps alkalmazásokat futtasson Azure Functions használatával

C# vagy node.js egyéni kódtöredékek futtatásához a logic apps, az Azure Functions egyéni funkciókhoz is létrehozhat. 
[Az Azure Functions](../azure-functions/functions-overview.md) kínál a kiszolgáló-mentes számítógép-használatról a Microsoft Azure és a feladatok végrehajtásához használt hasznos:

* Speciális formázás vagy számítási a logic apps mezők
* A munkafolyamat számításokat.
* Bővíthetők a logic app C# vagy node.js által támogatott funkciók

## <a name="create-custom-functions-for-your-logic-apps"></a>A logic Apps alkalmazásokat a felhasználói függvények létrehozása

Azt javasoljuk, hogy az Azure Functions portálon függvény létrehozása a **általános Webhook - csomópont** vagy **általános Webhook - C#** sablonok. Az eredmény létrehoz egy automatikus feltöltve a sablont, amely elfogadja `application/json` a logikai alkalmazás. Függvények, amelyek ezeket a sablonokat hoz létre a rendszer automatikusan észleli és jelennek meg a Logic App Designer alatt **Azure Functions a régióban.**

Az Azure portálon a a **integráció** ablaktábla a függvénynél, a sablon jelenítsen meg, amely **mód** beállítása **Webhook** és **Webhook típus**értéke **általános JSON**. 

Webhook funkciók fogadja el a kérelmet, és adja át az keresztül metódusnak egy `data` változó. Érhető el a tartalom tulajdonságait pontjelöléssel például `data.function-name`. Például egy egyszerű JavaScript függvény DateTime típusú értékké alakít át egy dátumkarakterlánc néz ki a következő példa:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>Az Azure Functions hívható meg a logic Apps alkalmazásokból

Az előfizetésében szereplő tárolókhoz listában, és válassza ki a függvényt, amely felhívja a Logic App tervezőben, kattintson a **műveletek** menüt, és válassza ki a **Azure Functions régiómban**.

Miután kiválasztotta a funkciót, a rendszer felkéri bemeneti forgalma objektum. Ez az objektum az az üzenet, hogy a logikai alkalmazást a függvény küld, és JSON-objektumnak kell lennie. Például, ha a átadni kívánt a **utolsó módosítás** dátuma a Salesforce-eseményindítóval, a függvény forgalma nézhet ki ebben a példában:

![Utolsó módosítás dátuma][1]

## <a name="trigger-logic-apps-from-a-function"></a>Egy függvény eseményindító logikai alkalmazások

Indíthat el egy függvényen belül egy logikai alkalmazást. Lásd: [a Logic apps hívható végpontként](logic-apps-http-endpoint.md). Kézi indítási rendelkező logikai alkalmazás létrehozása, majd a a függvényen belül készítése egy HTTP POST URL-cím elé a tartalom kívánt küldi a logikai alkalmazás manuális eseményindító.

### <a name="create-a-function-from-logic-app-designer"></a>A Logic App Designer függvény létrehozása

A Tervező is létrehozhat egy node.js webhook függvény. Első lépésként válassza ki **régiómban, az Azure Functions** , és válassza ki a függvény tárolója. Ha még nem rendelkezik a tárolóhoz, szeretné-e létrehozhat egy a [Azure Functions portálon](https://functions.azure.com/signin). Válassza ki **hozzon létre új**.  

Egy sablon számítási kívánt adatok alapján történő létrehozásához adja meg a környezeti objektumot, amely azt tervezi, hogy egy függvény továbbítja. Ez az objektum JSON objektumnak kell lennie. Például ha egy FTP-művelet a teljesíti a fájl tartalma, a környezet forgalma a következőképpen néz ebben a példában:

![A környezetben hasznos][2]

> [!NOTE]
> Ez az objektum nem alakítható karakterláncként, mert a tartalmat közvetlenül a JSON-adattartalmat kerül. Azonban a hiba akkor jelentkezik, ha az objektum nincs a JSON-jogkivonat (Ez azt jelenti, hogy egy karakterlánc- vagy JSON objektum vagy tömb). Konvertálja az objektumot egy karakterlánc, vegye fel a idézőjelek között, ebben a cikkben az első ábrán látható módon.
> 

A Tervező akkor is létrehozhat beágyazott függvény sablont hoz létre. Változók előre az környezetet, amelyre a függvény át szeretne alapján hozzák létre.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png
