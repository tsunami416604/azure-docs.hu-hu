---
title: Azure Monitor naplózási folyamatok automatizálása a Microsoft Flow
description: Megtudhatja, hogyan használhatja a Microsoft Flow az ismételhető folyamatok gyors automatizálására az Azure Log Analytics-összekötő használatával.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: 3c1df04e96aff55ae5b9f7130b1593638e3ea1b3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75395080"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Azure Monitor-naplózási folyamatok automatizálása a Microsoft Flow összekötővel
A [Microsoft flow](https://ms.flow.microsoft.com) lehetővé teszi, hogy több száz műveletet használó automatizált munkafolyamatokat hozzon létre számos szolgáltatáshoz. Az egyik művelet kimenete bemenetként is használható, így a különböző szolgáltatások közötti integrációt hozhat létre.  A Microsoft Flow Azure Log Analytics-összekötője lehetővé teszi olyan munkafolyamatok összeállítását, amelyek egy Azure Monitor Log Analytics munkaterületéről származó lekérdezések által beolvasott adatokkal rendelkeznek.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Használhatja például a Microsoft Flow-t az Office 365-e-mailes értesítésben szereplő Azure Monitor naplózási adatok használatára, az Azure DevOps-beli hiba létrehozására, vagy egy Slack-üzenet közzétételére.  A munkafolyamatot egy egyszerű vagy egy csatlakoztatott szolgáltatás egyes műveleteiből is elindíthatja, például e-mail vagy Tweet érkezésekor.  

A cikkben szereplő oktatóanyag bemutatja, hogyan hozhat létre olyan folyamatot, amely automatikusan elküldi egy Azure Monitor log-lekérdezés eredményét e-mailben, csupán egy példát arra, hogyan használhatja a Log Analytics-összekötőt a Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>1\. lépés: folyamat létrehozása
1. Jelentkezzen be [Microsoft Flowba](https://flow.microsoft.com), és válassza **a saját folyamatok**lehetőséget.
2. Kattintson **a + létrehozás üresből**elemre.

## <a name="step-2-create-a-trigger-for-your-flow"></a>2\. lépés: trigger létrehozása a folyamathoz
1. Kattintson **a Keresés több száz összekötő és eseményindító között**lehetőségre.
2. Írja be az **Schedule** kifejezést a keresőmezőbe.
3. Válassza az **ütemezése**, majd az **ütemezése-ismétlődés**lehetőséget.
4. A **gyakoriság** mezőben válassza a **nap** lehetőséget, majd az **intervallum** mezőben adja meg az **1**értéket.<br><br>![Microsoft Flow trigger párbeszédpanel](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>3\. lépés: Log Analytics művelet hozzáadása
1. Kattintson a **+ Új lépés**, majd a **Művelet hozzáadása** lehetőségre.
2. **Log Analytics**keresése.
3. Kattintson **Az Azure log Analytics – lekérdezés futtatása elemre, és jelenítse meg az eredményeket**.<br><br>![Log Analytics a lekérdezési ablak futtatása](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>4\. lépés: a Log Analytics művelet konfigurálása

1. Adja meg a munkaterület részleteit, beleértve az előfizetés-azonosítót, az erőforráscsoportot és a munkaterület nevét.
2. Adja hozzá a következő naplózási lekérdezést a **lekérdezési** ablakhoz.  Ez csak egy minta lekérdezés, és bármely más, az adatok visszaadása után helyettesíthető.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Válassza ki a **diagram típusához**tartozó **HTML-táblázatot** .<br><br>![Log Analytics művelet](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>5\. lépés: a folyamat konfigurálása e-mailek küldéséhez

1. Kattintson az **új lépés**, majd **a + művelet hozzáadása**lehetőségre.
2. Keresse meg az **Office 365 Outlook alkalmazást**.
3. Kattintson **az Office 365 Outlook – E-mail küldése**elemre.<br><br>![Office 365 Outlook kiválasztási ablak](media/flow-tutorial/flow04.png)

4. **A címzett ablakban és** **a tárgyban**lévő e-mail-cím tulajdonosának megadása.
5. Kattintson bárhová a **törzs** mezőben.  Megnyílik egy **dinamikus tartalom** ablak a korábbi műveletek értékeivel.  
6. Válassza a **törzs**lehetőséget.  Ez a lekérdezés eredménye a Log Analytics műveletben.
6. Kattintson a **Speciális beállítások megjelenítése**elemre.
7. A **HTML** mezőben válassza az **Igen**lehetőséget.<br><br>![Office 365 e-mail konfigurációs ablak](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>6\. lépés: a folyamat mentése és tesztelése
1. A **folyamat neve** mezőben adja meg a folyamat nevét, majd kattintson a **folyamat létrehozása**elemre.<br><br>![folyamat mentése](media/flow-tutorial/flow06.png)
2. A folyamat már létre lett hozva, és egy nap után fog futni, amely a megadott ütemterv. 
3. A folyamat azonnali teszteléséhez kattintson a **Futtatás most** lehetőségre, majd a **folyamat futtatása**parancsra.<br><br>![folyamat futtatása](media/flow-tutorial/flow07.png)
3. Amikor a folyamat befejeződik, tekintse meg a megadott címzett e-mail-címeit.  A következőhöz hasonló törzstel rendelkező e-mailt kell kapnia:<br><br>![Minta e-mail](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor lévő naplók lekérdezéséről](../log-query/log-query-overview.md).
- További információ a [Microsoft Flowról](https://ms.flow.microsoft.com).



