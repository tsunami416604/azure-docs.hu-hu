---
title: A Microsoft Flow Azure Log Analytics-folyamatok automatizálása
description: Ismerje meg, hogyan használhatja a Microsoft Flow gyorsan automatizálása ismételhető az Azure Log Analytics-összekötő használatával.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.component: na
ms.openlocfilehash: 21cf7cf3d12902b02fcbf650a1623e78004d28b4
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131700"
---
# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>Összekötő Log Analytics-folyamatok automatizálása a Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) lehetővé teszi automatizált munkafolyamatok műveletek több száz segítségével különböző szolgáltatások létrehozásához. Egy művelet kimenetében használható bemenetként a másikra, hogy lehetővé teszi különböző szolgáltatások közötti integrációt.  Az Azure Log Analytics-összekötő Microsoft Flow engedélyezi, hogy a napló megkeresi a Naplóelemzési által beolvasott adatokat tartalmazó munkafolyamatok építhetők fel.

Microsoft Flow segítségével például az Office 365 e-mailben értesítést Naplóelemzési adatok használja, hozzon létre egy hibajelentést a Visual Studio Team Services vagy közzététele a Slack üzenetet.  Olyan egyszerű ütemezés, vagy az egyes művelet egy csatlakoztatott szolgáltatással, például amikor egy e-mail vagy egy tweetet érkezik a munkafolyamat indíthat el.  

Ebben a cikkben az oktatóanyag bemutatja, hogyan hozzon létre egy folyamatot, amely automatikusan elküldi e-mailben, hogyan használhatja a Microsoft Flow Naplóelemzési csak egy példa Naplóelemzési napló keresés eredményeit. 


## <a name="step-1-create-a-flow"></a>1. lépés: A folyamat létrehozása
1. Jelentkezzen be [Microsoft Flow](http://flow.microsoft.com), és válassza ki **saját Forgalomáramlás**.
2. Kattintson a **+ hozza létre az üres**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>2. lépés: A folyamat eseményindító létrehozása
1. Kattintson a **keresés több száz összekötők és eseményindítók**.
2. Típus **ütemezés** be a keresőmezőbe.
3. Válassza ki **ütemezés**, majd válassza ki **ütemezés - ismétlődési**.
4. Az a **gyakoriság** mezőben jelölje be **nap** és a a **időköz** adja meg a **1**.<br><br>![Microsoft Flow eseményindító párbeszédpanel](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>3. lépés: A Naplóelemzési művelet hozzáadása
1. Kattintson a **+ új lépés**, és kattintson a **művelet hozzáadása**.
2. Keresse meg **Analytics jelentkezzen**.
3. Kattintson a **Azure Log Analytics-lekérdezés futtatása, és eredményeinek képi megjelenítése**.<br><br>![A Naplóelemzési lekérdezési ablakban futtassa](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>4. lépés: Konfigurálja a Naplóelemzési művelet

1. Adja meg a munkaterület, beleértve az előfizetési azonosító, erőforráscsoport, és a munkaterület neve adatait.
2. Adja hozzá a következő Log Analytics-lekérdezés futtatásával a **lekérdezés** ablak.  Ez csak a mintalekérdezést, és lecserélheti bármely más, hogy adatokat ad vissza.
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
```

2. Válassza ki **HTML tábla** a a **diagramtípus**.<br><br>![Napló Analytics művelet](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>5. lépés: az e-mail üzenetek küldéséhez a folyamat konfigurálása

1. Kattintson a **új lépés**, és kattintson a **+ új művelet**.
2. Keresse meg **Office 365 Outlook**.
3. Kattintson a **Office 365 Outlook – az e-mailek küldése**.<br><br>![Az Office 365 Outlook kiválasztási ablaka](media/log-analytics-flow-tutorial/flow04.png)

4. A címzett e-mail címét adja meg a **való** ablakot, és az e-mail tárgyát **tulajdonos**.
5. Kattintson bárhova a **törzs** mezőbe.  A **dinamikus tartalom** értékeket az előző műveletekből ablak nyílik meg.  
6. Válassza ki **törzs**.  Ez az a Naplóelemzési művelet: a lekérdezés eredményeit.
6. Kattintson a **speciális beállítások megjelenítése**.
7. Az a **HTML** mezőben válassza **Igen**.<br><br>![Az Office 365 e-mailek konfigurációs ablak](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>6. lépés: Mentéséhez és a folyamat tesztelése
1. Az a **folyamat nevének** mezőbe, vegye fel a folyamat nevét, majd kattintson **folyamat létrehozása**.<br><br>![Mentés folyamata](media/log-analytics-flow-tutorial/flow06.png)
2. A folyamat megtörtént, és egy napon, amely a megadott ütemezés fog futni. 
3. A folyamat azonnal teszteléséhez kattintson **Futtatás most** , majd **folyamat futtatása**.<br><br>![Futtatási folyamata](media/log-analytics-flow-tutorial/flow07.png)
3. A folyamat befejezése után ellenőrizze a megadott címzett e-mail.  Kell egy a következőhöz hasonló szervezethez mail érkezett:<br><br>![Példa e-mailre](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>További lépések

- További információ [Log Analytics-e jelentkezni a keresések](log-analytics-log-search-new.md).
- További információ [Microsoft Flow](https://ms.flow.microsoft.com).



