---
title: Eseményekre való válaszadás Azure Log Analytics-riasztásokkal | Microsoft Docs
description: Az oktatóanyag segítségével megismerheti, hogyan lehet elérni, hogy a Log Analytics-riasztások azonosítsák a munkaterület fontos információit, és proaktívan értesítsék Önt a problémákról, vagy műveleteket indítsanak el ezek megoldására.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2018
ms.author: magoedte
ms.custom: mvc
ms.component: na
ms.openlocfilehash: d81a41a0012d4e0be4e812d48074e7af1e92213a
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391146"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Eseményekre való válaszadás Azure Monitor-riasztásokkal
Az Azure Alerts naplókeresési szabályokat hoz létre megadott naplólekérdezések rendszeres időközönként való automatikus futtatására.  Ha a naplólekérdezés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztásbejegyzés. A szabály ekkor automatikusan futtathat egy vagy több műveletet [Műveletcsoportok](../monitoring-and-diagnostics/monitoring-action-groups.md) használatával.  Ez az oktatóanyag a [Log Analytics-adatokat tartalmazó irányítópultok létrehozása és megosztása](log-analytics-tutorial-dashboards.md) oktatóanyag folytatása.   

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Riasztási szabály létrehozása
> * Műveletcsoport konfigurálása e-mail-értesítés küldésére

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre, amely [a Log Analytics-munkaterülethez csatlakozik](log-analytics-quick-collect-azurevm.md).

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.

## <a name="create-alerts"></a>Riasztások létrehozása
A riasztásokat riasztási szabályok hozzák létre az Azure Monitorban, és rendszeres időközönként automatikusan mentett lekérdezéseket vagy egyéni naplókereséseket futtathatnak.  Riasztásokat megadott teljesítménymetrikák alapján, illetve bizonyos események létrehozásakor vagy hiányakor hozhat létre, illetve akkor, ha egy adott időtartományon belül több esemény jön létre.  A riasztások segítségével értesülhet például arról, ha az átlagos processzorhasználat meghalad egy bizonyos küszöbértéket, ha hiányzik egy frissítés, vagy ha létrejön egy esemény, amikor egy adott Windows-szolgáltatás vagy Linux-démon nem fut.  Ha a naplókeresés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztás. A szabály ekkor automatikusan futtathat egy vagy több műveletet, például értesíti Önt a riasztásról, vagy meghív egy másik folyamatot.

A következő példában létrehoz egy metrikamérési riasztási szabályt az [adatok vizualizációját ismertető oktatóanyagban](log-analytics-tutorial-dashboards.md) mentett *Azure-beli virtuális gépek – Processzorhasználat* lekérdezés alapján. Létrejön egy riasztás minden egyes, a 90%-os küszöbértéket meghaladó virtuális géphez.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Monitorozás** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Monitorozás** lehetőséget.
2. Új riasztás létrehozásához a bal oldali panelen válassza a **Riasztások** elemet, majd kattintson az oldal tetején található **Új riasztási szabály** elemre.

    ![Új riasztási szabály létrehozása](./media/log-analytics-tutorial-response/alert-rule-02.png)

3. Az első lépésben a **Riasztás létrehozása** szakaszban ki fogja választani a Log Analytics-munkaterületet erőforrásként, mivel ez egy naplóalapú riasztási jelzés.  Az eredmények szűréséhez válassza ki az adott **előfizetést** a legördülő listából, ha több előfizetés is van, amely tartalmazza a korábban létrehozott virtuális gépet és Log Analytics-munkaterületet.  Szűrje az **erőforrástípusokat** a **Log Analytics** a legördülő menüből való kiválasztásával.  Végül válassza az **Erőforrás** **DefaultLAWorkspace** elemet, majd kattintson a **Kész** gombra.

    ![Riasztás létrehozása, 1. lépés](./media/log-analytics-tutorial-response/alert-rule-03.png)

4. A **Riasztási feltételek** szakaszban kattintson a **Feltétel hozzáadása** elemre a lekérdezés definiálásához, majd adja meg a logikát, amelyet a riasztási szabály követ. A **Jellogika konfigurálása** panelen válassza az **Egyéni naplókeresés** lehetőséget a jel nevénél, és adja meg a lekérdezést a **Keresési lekérdezés** mezőben.

    Például:
    ```
    Perf
    | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total"
    | summarize AggregatedValue=avg(CounterValue) by bin(TimeGenerated, 1m)
    ```

    A panel frissül, és a riasztás konfigurációs beállításait mutatja.  Felül a kiválasztott jel eredményeit mutatja az elmúlt 30 percre vonatkozóan.

5. Konfigurálja a riasztást az alábbi információkkal:  
   a. A **Riasztás alapja* legördülő menüből válassza a **Metrikus egység** elemet.  A metrikamérés egy riasztást hoz létre a lekérdezés minden egyes objektumához, amelynek értéke meghaladja a megadott küszöbértéket.  
   b. A **Feltétel** értékeként válassza a **Nagyobb, mint** lehetőséget, majd adja meg a **Küszöbérték** mezőben a **90** értéket.  
   c. A Riasztás aktiválásának alapja részben válassza az **Egymás utáni incidensek** elemet, a legördülő listából válassza a **Nagyobb, mint** lehetőséget, és adja meg a 3 értéket.  
   d. Az Értékelés alapja részben fogadja el az alapértelmezett beállításokat. A szabály öt percenként fog futni, és visszaadja azokat a rekordokat, amelyek az aktuális idő ezen tartományában jöttek létre.  
6. Kattintson a **Kész** gombra a riasztási szabály létrehozásának befejezéséhez.

    ![Riasztási jel konfigurálása](./media/log-analytics-tutorial-response/alert-signal-logic-02.png)

7. A második lépésben adja meg a riasztás nevét a **Riasztási szabály neve** mezőben, például **Percentage CPU greater than 90 percent**.  Adjon meg egy **Leírást**, ahol részletezi a riasztás tulajdonságait, majd a **Súlyosság** értéknél a megadott lehetőségek közül válassza a **Kritikus (0. súlyosság)** lehetőséget.

    ![Riasztás részleteinek konfigurálása](./media/log-analytics-tutorial-response/alert-signal-logic-04.png)

8. Ha a létrehozásakor azonnal aktiválni kívánja a riasztási szabályt, fogadja el a **Szabály engedélyezése a létrehozásakor** alapértelmezett értékét.  
9. A harmadik és egyben végső lépésben adjon meg egy **műveletcsoportot**, amely biztosítja, hogy minden alkalommal, amikor egy riasztás aktiválódik ugyanazok a műveletek lesznek végrehajtva, és amely mindegyik meghatározott szabályra használható.  Hozzon létre egy új műveletcsoportot a következő információkkal:  
   a. Válassza az **Új műveletcsoport** lehetőséget, és megnyílik a **Műveletcsoport hozzáadása** panel.  
   b. A **Műveletcsoport neve** részben adjon meg egy nevet, például **IT Operations - Notify** és egy **Rövid nevet** például **itops-n**.  
   c. Ellenőrizze, hogy az alapértelmezett értékek helyesek-e az **Előfizetés** és az **Erőforráscsoport** mezőben. Ha nem, válassza ki a megfelelőt a legördülő listából.  
   d. A Műveletek részben adja meg a művelet nevét, például **E-mail küldése**, és a **Művelettípus** alatti legördülő menüben válassza az **E-mail/SMS/Leküldéses/Hang** lehetőséget. A jobb oldalon megnyílik az **E-mail/SMS/Leküldéses/Hang** tulajdonságait tartalmazó panel, amely további információkat biztosít.  
   e. Az **E-mail/SMS/Leküldéses/Hang** panelen engedélyezze az **e-mailt**, és adjon meg egy érvényes levelezési SMTP-címet az üzenet kézbesítéséhez.  
   f. Kattintson az **OK** gombra a módosítások mentéséhez.  
       ![Új műveletcsoport létrehozása](./media/log-analytics-tutorial-response/action-group-properties-01.png)

10. Kattintson az **OK** gombra a műveletcsoport létrehozásának befejezéséhez.
11. Kattintson a **Riasztási szabály létrehozás** gombra a riasztási szabály létrehozásának befejezéséhez. Azonnal el fog indulni.

    ![Új riasztási szabály létrehozásának befejezése](./media/log-analytics-tutorial-response/alert-rule-01.png)

## <a name="view-your-alerts-in-azure-portal"></a>Riasztások megtekintése az Azure Portalon
Most, hogy létrehozott egy riasztást, egyetlen panelen tekintheti meg az Azure-riasztásokat és kezelheti az Azure-előfizetésekben lévő összes riasztást. Felsorolja az összes riasztási szabályt (engedélyezett vagy letiltott) és a cél erőforrások, erőforráscsoportok, szabálynév vagy állapot alapján rendezhető. Tartalmazza az összes aktivált riasztás, valamint az összes konfigurált/engedélyezett riasztási szabály összegzését.

![Az Azure riasztások állapotát mutató oldala](./media/log-analytics-tutorial-response/azure-alerts-02.png)

Amikor egy riasztás aktiválódik, a táblázatban megjelenik az állapot, és hogy a választott időtartományon belül (alapértelmezés szerint az elmúlt hat óra) hányszor aktiválódott.  A beérkezett üzenetek között megjelenik egy, a következő példához hasonló e-mail, amely a küszöbértéket megsértő virtuális gépet mutatja és a keresési lekérdezésnek megfelelő első néhány eredményt.

![Minta riasztási e-mail művelet](./media/log-analytics-tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan képesek a riasztási szabályok proaktívan azonosítani a hibákat, valamint válaszolni rájuk, amikor az ütemezett naplókeresések futtatásakor az eredmények megfelelnek bizonyos feltételeknek.

Kövesse ezt a hivatkozást az előre felépített Log Analytics-szkriptminták megtekintéséhez.

> [!div class="nextstepaction"]
> [Log Analytics-szkriptminták](powershell-samples.md)
