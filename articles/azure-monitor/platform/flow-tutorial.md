---
title: Az Azure Monitor log Microsoft Flow-folyamatok automatizálása
description: Ismerje meg, hogyan használhatja fel a Microsoft Flow megismételhető folyamatok gyors automatizálása az Azure Log Analytics-összekötő használatával.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: c3732dd2fa87b00eec38f88ab828605b33567235
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123147"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Az összekötő az Azure Monitor log-folyamatok automatizálása a Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) lehetővé teszi, hogy több száz műveletek használatával a különböző szolgáltatások, automatizált munkafolyamatokat hozhat létre. Egy művelet kimenete egy másik lehetővé teszi, hogy hozzon létre a különböző szolgáltatások közötti integráció bemeneteként használható.  Az Azure Log Analytics-összekötő a Microsoft Flow lehetővé teszi a Log Analytics-munkaterületet az Azure Monitor log-lekérdezések által beolvasott adatokat tartalmazó munkafolyamatokat építhet rájuk.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Használhatja például a Microsoft Flow LAzure figyelő naplóadatok használja az Office 365 e-mailben értesítést, hibajelentés létrehozása az Azure DevOps, vagy egy Slack-üzenet küldése.  Egy munkafolyamat olyan egyszerű ütemezés szerint vagy néhány művelet, például a levelezés, vagy a tweet érkezésekor egy csatlakoztatott szolgáltatásban is indíthat.  

Ebben a cikkben az oktatóanyag bemutatja, hogyan hozhat létre egy folyamatot, amely automatikusan elküldi az e-mailhez, csupán egy példa, hogyan használhatja a Log Analytics-összekötő a Microsoft Flow az Azure Monitor log-lekérdezés eredményeit. 


## <a name="step-1-create-a-flow"></a>1. lépés: Folyamat létrehozása
1. Jelentkezzen be a [Microsoft Flow](https://flow.microsoft.com), és válassza ki **saját folyamatok**.
2. Kattintson a **+ üres folyamat létrehozása**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>2. lépés: A folyamat eseményindító létrehozása
1. Kattintson a **keresés több száz összekötő és trigger között**.
2. Típus **ütemezés** kifejezést a keresőmezőbe.
3. Válassza ki **ütemezés**, majd válassza ki **ütemezés – ismétlődés**.
4. Az a **gyakorisága** mezőben jelölje be **nap** és a a **időköz** mezőbe írja be **1**.<br><br>![Microsoft Flow-eseményindító párbeszédpanel](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>3. lépés: A Log Analytics művelet hozzáadása
1. Kattintson a **+ új lépés**, és kattintson a **művelet hozzáadása**.
2. Keresse meg **Log Analytics**.
3. Kattintson a **Azure Log Analytics-lekérdezés futtatásához és eredményeinek képi megjelenítése**.<br><br>![A log Analytics lekérdezési ablakban futtassa](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>4. lépés: Konfigurálja a Log Analytics-műveletet

1. Adja meg a munkaterület, beleértve az előfizetés Azonosítóját, erőforráscsoport, és a munkaterület neve.
2. Adja hozzá a következő log lekérdezés a **lekérdezés** ablak.  Ez a mintalekérdezés, és lecserélheti bármely más, amely adatokat ad vissza.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Válassza ki **HTML-táblázat** számára a **diagramtípus**.<br><br>![Log Analytics művelet](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>5. lépés: E-mail küldése a flow konfigurálása

1. Kattintson a **új lépés**, és kattintson a **+ művelet hozzáadása**.
2. Keresse meg **az Office 365 Outlook**.
3. Kattintson a **Office 365 Outlook – e-mail küldése**.<br><br>![Az Office 365 Outlook-kiválasztási ablaka](media/flow-tutorial/flow04.png)

4. A címzett e-mail-címét adja meg a **való** ablakot, és az e-mail tárgyát **tulajdonos**.
5. Kattintson bárhová a **törzs** mezőbe.  A **dinamikus tartalom** ablak nyílik meg a korábbi műveletek értékeket.  
6. Válassza ki **törzs**.  Ez az a Log Analytics-műveletet a lekérdezés eredményeit.
6. Kattintson a **speciális beállítások megjelenítése**.
7. Az a **HTML** jelölje ki **Igen**.<br><br>![Az Office 365 e-mailek konfigurációs ablaka](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>6. lépés: Mentés és a folyamat tesztelése
1. Az a **Folyamatnév** mezőben, adja hozzá a folyamat nevét, és kattintson **folyamat létrehozása**.<br><br>![Folyamat mentése](media/flow-tutorial/flow06.png)
2. A folyamat létrehozása megtörtént, és a egy nap, amely megadott ütemezés fog futni. 
3. A folyamat teszteléséhez azonnal, kattintson a **Futtatás most** , majd **folyamat futtatása**.<br><br>![Folyamat futtatása](media/flow-tutorial/flow07.png)
3. A folyamat befejezését követően ellenőrizze az e-mailt a megadott címzettnek.  Kell egy e-mail az alábbihoz hasonló szervezethez érkezett:<br><br>![Példa e-mailre](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>További lépések

- Tudjon meg többet [lekérdezések jelentkezzen be az Azure Monitor](../log-query/log-query-overview.md).
- Tudjon meg többet [Microsoft Flow](https://ms.flow.microsoft.com).



