---
title: "A különböző műveletek utasítás kapcsoló Azure Logic Apps |} Microsoft Docs"
description: "Válassza ki a különböző műveleteket hajthat végre a logic Apps alkalmazások kifejezés értékek alapján a switch utasítás használatával"
services: logic-apps
keywords: "Switch utasítás"
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a459fb131cb9f917f2b0cf79679b04da48d76c42
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>A logic apps kapcsoló utasítással különböző műveleteket

Szerzői munkafolyamat, ha gyakran kell más műveletek objektum vagy kifejezés értéke alapján. Például érdemes a Logic Apps alkalmazást úgy kezd viselkedni HTTP-kérelem, vagy egy e-mailben kiválasztott lehetőségnek állapotkódját menübeállításoktól függően.

A switch utasítás használatával ezek a forgatókönyvek megvalósításához. A Logic Apps alkalmazást egy token vagy kifejezés kiértékelése, és válassza ki a kódaláírásokra is ugyanazt az értéket a megadott műveletek végrehajtásához. Csak egyetlen esetet meg kell felelnie az switch utasításban.

> [!TIP]
> Minden programozási nyelv, például a switch utasítás csak egyenlőségi operátorok támogatja. Ha más relációs operátorok kell például a "nagyobb, mint", az állapot utasítás használható.
> Ahhoz, hogy determinisztikus végrehajtási viselkedésének, esetekben dinamikus jogkivonatok vagy kifejezés helyett egyedi és statikus értéket kell tartalmaznia.

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés. Ha nincs egy aktív Azure-előfizetéssel, [ingyenes fiók létrehozását](https://azure.microsoft.com/free/), vagy próbálja [logikai alkalmazások szabad](https://tryappservice.azure.com/).
- [A logic apps alapszintű ismerete](logic-apps-overview.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>A switch utasítás hozzá a munkafolyamathoz

A switch utasítás működése megjelenítéséhez az alábbi példa létrehoz egy logikai alkalmazás, amely figyeli a dropbox alkalmazásba feltöltött fájlok. Az új fájlok feltöltése után a logikai alkalmazást úgy dönt, hogy ezek a fájlok átvitele a SharePoint-e jóváhagyó e-mailt küld. Az alkalmazás használ egy kapcsoló utasítást, ami a jóváhagyó értéke alapján különböző műveleteket hajtja végre.

1. Logikai alkalmazás létrehozása, és válassza ki az ehhez az eseményindítóhoz: **Dropbox - fájl létrehozásakor**.

   ![Használja a Dropbox -, ha egy fájl jön létre eseményindító](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. Az eseményindító, adja hozzá ezt a műveletet: **Outlook.com - jóváhagyási e-mail küldése**

   > [!TIP]
   > A Logic apps egy Office 365 Outlook-fiókot küldő jóváhagyási e-mail lehetőségeket is támogatja.

   - Ha nincs meglévő kapcsolat, felszólítást hozzon létre egyet.
   - Töltse ki a kötelező mezőket. Például az **való**, adja meg az e-mail címet a jóváhagyó e-mailek küldéséhez.
   - A **felhasználói beállítások**, adja meg `Approve, Reject`.

   ![Kapcsolat beállítása](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Adjon hozzá egy kapcsoló utasítást.

   - Válassza ki **+ új lépés** > **... További** > **kapcsoló eset hozzáadása**. 
   - Most azt szeretnénk, a végrehajtandó művelet kiválasztásához alapján a `SelectedOptions` kimenetét a *jóváhagyása e-mailek küldése* művelet. 
   Ez a mező található az **dinamikus tartalom hozzáadása az** választó.
   - Használjon *1. eset* kezelésére, amikor kijelöli a jóváhagyó `Approve`.
     - Jóváhagyás esetén másolja az eredeti fájlt a SharePoint Online-hoz való a [ **SharePoint Online - fájl létrehozása** művelet](../connectors/connectors-create-api-sharepointonline.md).
     - Adja hozzá a eset értesítse a felhasználókat, hogy egy új fájl áll rendelkezésre a SharePoint-webhelyen belül egy másik művelet.
   - Adja hozzá szeretné kezelni a felhasználó kiválaszt egy másik helyzet `Reject`.
     - Ha vissza kell utasítani, hogy a fájl nem fogadja el, és nincs további teendője, amely arról tájékoztatja, hogy más jóváhagyóknak értesítő e-mail küldése.
   - `SelectedOptions`csak két lehetőséget biztosít, így azt is hagyhatja a **alapértelmezett** esetben üres.

   ![Switch utasítás](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > A switch utasítás kell legalább egy eset az alapértelmezett eset mellett.

4. A switch utasítás után törli az eredeti fájlt adja hozzá ezt a műveletet a dropbox alkalmazásba feltöltött: **Dropbox - fájl törlése**

5. Mentse a Logic Apps alkalmazást. Tesztelje az alkalmazás feltölteni a fájlt a dropbox alkalmazásba. Hamarosan jóváhagyása e-mailt kell kapnia. Jelöljön ki egy lehetőséget, és tekintse meg a viselkedést.

   > [!TIP]
   > Ellenőrizze, hogy miként [logikai alkalmazások figyelése](logic-apps-monitor-your-logic-apps.md).

## <a name="understand-the-code-behind-switch-statements"></a>A kapcsoló utasítások háttérkódot ismertetése

Most, hogy sikeresen létrehozta a logikai alkalmazás egy switch utasítás használatával, a kód megadása a switch utasítás mögött vizsgáljuk meg.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

* `"Switch"`a kapcsoló utasítást, ami az olvashatóság átnevezheti neve van. 
* `"type": "Switch"`azt jelzi, hogy a művelet egy switch utasításban. 
* `"expression"`Ebben a példában a jóváhagyó a kijelölt beállítást, és minden esetben később a definíció deklarált képest értékeli ki. 
* `"cases"`tetszőleges számú esetek is tartalmazhat. A minden esetben `"Case *"` az esetében, amelyek átnevezheti az olvashatóság alapértelmezett neve. 
`"case"`a case címke, amely a kapcsolókifejezés használja az összehasonlításhoz, határozza meg és állandó és egyedi értéknek kell lennie. Ha nem tartozik a kifejezéssel kapcsoló, a műveletek `"default"` hajtja végre a rendszer.

## <a name="get-help"></a>Segítségkérés

Látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps), ahol kérdéseket tehet fel és válaszolhat meg, valamint megtudhatja, mivel foglalkoznak az Azure Logic Apps más felhasználói.

Ha szeretné segíteni az Azure Logic Apps és összekötők fejlesztését, szavazzon az ötletekre az [Azure Logic Apps felhasználói visszajelzések oldalán](http://aka.ms/logicapps-wish), vagy küldje be saját javaslatait.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [feltételek hozzáadása](logic-apps-use-logic-app-features.md)
- További tudnivalók [hiba és kivételkezelést](logic-apps-exception-handling.md)
- Fedezze fel több [munkafolyamat nyelvi képességek](logic-apps-author-definitions.md)