---
title: "Eseményekre való válaszadás Azure Log Analytics-riasztásokkal | Microsoft Docs"
description: "Az oktatóanyag segítségével megismerheti, hogyan lehet elérni, hogy a Log Analytics-riasztások azonosítsák az OMS-adattár fontos információit, és proaktívan értesítsék Önt a problémákról, vagy műveleteket indítsanak el ezek megoldására."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: fcfaa849f67ffcfa69672d116837e96d318c2124
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>Eseményekre való válaszadás Log Analytics-riasztásokkal
A Log Analytics-riasztások azonosítják a Log Analytics-adattárban található fontos információkat. A riasztásokat riasztási szabályok hozzák létre, amelyek rendszeres időközönként automatikusan naplókereséseket futtatnak. Ha a naplókeresés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztásbejegyzés, amelyet konfigurálni lehet egy automatikus válasz végrehajtására.  Ez az oktatóanyag a [Log Analytics-adatokat tartalmazó irányítópultok létrehozása és megosztása](log-analytics-tutorial-dashboards.md) oktatóanyag folytatása.   

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Riasztási szabály létrehozása
> * Riasztási szabály konfigurálása e-mail-értesítés küldésére

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre, amely [a Log Analytics-munkaterülethez csatlakozik](log-analytics-quick-collect-azurevm.md).  

## <a name="log-in-to-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen. 

## <a name="create-alerts"></a>Riasztások létrehozása

A riasztásokat riasztási szabályok hozzák létre, amelyek rendszeres időközönként automatikus naplókereséseket futtatnak.  Riasztásokat megadott teljesítménymetrikák alapján, illetve bizonyos események létrehozásakor vagy hiányakor hozhat létre, illetve akkor, ha egy adott időtartományon belül több esemény jön létre.  A riasztások segítségével értesülhet például arról, ha az átlagos processzorhasználat meghalad egy bizonyos küszöbértéket, vagy ha létrejön egy esemény, amikor egy adott Windows-szolgáltatás vagy Linux-démon nem fut.   Ha a naplókeresés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztásbejegyzés. A szabály ekkor automatikusan lefuttathat egy vagy több műveletet, hogy proaktívan értesítse Önt a riasztásról, vagy meghívjon egy másik folyamatot. 

A következő példában létre fog hozni egy metrikamérési riasztási szabályt, amely a lekérdezés összes olyan számítógép-objektumához létrehoz egy riasztást, amelynek értéke meghaladja a 90%-os küszöbértéket.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. Nyissa meg az OMS-portált az OMS-portál lehetőség kiválasztásával, majd az **Áttekintés** lapon válassza a **Naplókeresés** elemet.  
3. A portál tetején válassza a **Kedvencek** lehetőséget, majd a jobb oldalon található **Mentett keresések** panelen válassza ki az *Azure-beli virtuális gépek – Processzorhasználat* lekérdezést.  
4. Kattintson az oldal tetején található **Riasztás** lehetőségre a **Riasztási szabály hozzáadása** képernyő megnyitásához.  
5. Konfigurálja a riasztási szabályt az alábbi információkkal:  
   a. Adjon meg egy **Nevet** a riasztás számára, például *Virtuális gép processzorhasználata túllépve: >90*  
   b. Az **Időtartomány** mezőben adjon meg egy időtartományt a lekérdezés számára, például a *30* értéket.  A lekérdezés csak azokat a rekordokat adja vissza, amelyek az aktuális idő ezen tartományában jöttek létre.  
   c. A **Riasztási időköz** határozza meg, hogy milyen gyakran kell futtatni a lekérdezést.  Ebben a példában adjon meg *5* percet, amely még benne van a megadott időtartományban.  
   d. Válassza ki a **Metrikamérés** lehetőséget, majd az **Aggregált érték** mezőben adja meg a *90* értéket, a **Riasztás aktiválásának oka**  mezőben pedig a *3* értéket.  
   e. A **Műveletek** területen tiltsa le az e-mail-értesítéseket.
6. Kattintson a **Mentés** gombra a riasztási szabály jóváhagyásához. Azonnal el fog indulni.<br><br> ![Példa a riasztási szabályra](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

A Log Analytics riasztási szabályai által létrehozott riasztásbejegyzésekhez **Riasztás** típus és **OMS** SourceSystem van beállítva.<br><br> ![Példa a létrehozott riasztási eseményekre](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>Riasztási műveletek
A riasztásokkal speciális műveleteket is végrehajthat, például létrehozhat egy e-mail-értesítést, elindíthat egy [Automation-runbookot](../automation/automation-runbook-types.md), valamint létrehozhat egy incidensrekordot az ITSM incidenskezelő rendszerben egy webhook használatával, vagy az [Informatikai szolgáltatásfelügyeleti összekötő megoldással](log-analytics-itsmc-overview.md) a riasztási feltételek teljesülésére adott válaszként.   

Az e-mailes műveletek e-mailt küldenek egy vagy több címzettnek a riasztás részleteiről. Az e-mail tárgyát megadhatja, a tartalma azonban szabványos, és a Log Analytics hozza létre.  Most pedig frissítsük a korábban létrehozott riasztási szabályt, és konfiguráljuk úgy, hogy e-mailben értesítse Önt ahelyett, hogy aktívan figyelné a riasztásbejegyzést egy naplókereséssel.     

1. Az OMS-portálon a felső menüben válassza a **Beállítások**, majd a **Riasztások** elemet.
2. A riasztási szabályok listájában kattintson a korábban létrehozott riasztás melletti ceruza ikonra.
3. A **Műveletek** területen engedélyezze az e-mail-értesítéseket.
4. Adja meg az e-mail **tárgyát**, például *A processzorhasználat meghaladta a küszöbértéket: >90*.
5. A **Címzettek** mezőben adja meg egy vagy több címzett e-mail-címét.  Ha több címet ad meg, pontosvesszővel (;) válassza el őket.
6. Kattintson a **Mentés** gombra a riasztási szabály jóváhagyásához. Azonnal el fog indulni.<br><br> ![Riasztási szabály e-mail-értesítéssel](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan képesek a riasztási szabályok proaktívan azonosítani a hibákat, valamint válaszolni rájuk, amikor az ütemezett naplókeresések futtatásakor az eredmények megfelelnek bizonyos feltételeknek.  

Kövesse ezt a hivatkozást az előre felépített Log Analytics-szkriptminták megtekintéséhez.  

> [!div class="nextstepaction"]
> [Log Analytics-szkriptminták](powershell-samples.md)