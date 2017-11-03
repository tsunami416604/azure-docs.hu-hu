---
title: "Azure napló Analytics riasztások események válaszolni |} Microsoft Docs"
description: "Ez az oktatóanyag segít megérteni a riasztásokat a fontos információk az OMS-adattárban lévő azonosítására proaktív problémák értesíti, vagy azokat kijavításának műveletek meghívása Naplóelemzési."
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
ms.openlocfilehash: 3ab8d32eb4b3f2748249f40139de76c8e7f4d971
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>A napló Analytics riasztásokkal kapcsolatos események válaszolni
Log Analytics riasztások határozza meg a Naplóelemzési tárházban fontos adatokat.  Riasztási szabályok, amelyek automatikusan futnak a napló keresések rendszeres időközönként létrehozzák őket, és ha a napló keresés eredményeit az adott feltételeknek megfelelő majd egy riasztási rekord jön létre, és beállítható automatikus válasz végrehajtásához.  Ez az oktatóanyag folytatása, akkor az a [Log Analytics-adatok létrehozása és a megosztáshoz irányítópultok](log-analytics-tutorial-dashboards.md) oktatóanyag.   

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Riasztási szabály létrehozása
> * E-mail értesítés küldése a riasztási szabály konfigurálása

A példa az oktatóanyag elvégzéséhez rendelkeznie kell egy meglévő virtuális gép [csatlakozik a Naplóelemzési munkaterület](log-analytics-quick-collect-azurevm.md).  

## <a name="log-in-to-azure-portal"></a>Jelentkezzen be Azure-portálon
Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Riasztások létrehozása

A riasztási szabályok, amelyek automatikusan futnak a napló keresések rendszeres időközönként riasztások jönnek létre.  Riasztások adott mérőszámok alapján is létrehozhat, illetve bizonyos események létrehozásakor egy eseményt, vagy adott számú esemény egy adott időpontban időszakban hozott létre.  Például riasztások segítségével értesítést küldenek, ha átlagos CPU-használat meghaladja a meghatározott küszöbérték és a Linux-démon nem fut vagy esemény jön létre, amikor egy adott Windows-szolgáltatás.   Ha a napló keresés eredményeit az adott feltételeknek megfelelő, egy riasztás rekord jön létre. A szabály úgy automatikusan futtatja egy vagy több műveletek proaktív értesítést küldenek, a figyelmeztetés vagy meg kíván hívni egy másik folyamat. 

A következő példában létrehozhatunk egy metrika mérési riasztási szabály, amely minden számítógép-objektum egy riasztást fog létrehozni a lekérdezés egy érték, amely meghaladja a 90 %-os küszöbértéket.

1. Az Azure portálon kattintson **további szolgáltatások** bal alsó sarokban található. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Analytics jelentkezzen**...
2. Indítsa el az OMS-portálon, és az OMS-portálon kiválasztásával a **áttekintése** lapon jelölje be **naplófájl-keresési**.  
3. Válassza ki **Kedvencek** a portál és a felső a **mentett keresések** a jobb oldali ablaktáblán válassza ki a lekérdezés *Azure virtuális gépek - processzorhasználat*.  
4. Kattintson a **riasztási** nyissa meg a lap tetején a **riasztási szabály hozzáadása** képernyő.  
5. A riasztási szabály konfigurálása a következő információkat:  
   a. Adjon meg egy **neve** a riasztás például *VM processzorhasználata túllépte > 90*  
   b. A **időkerete**, adjon meg egy időtartományt a lekérdezéshez, például *30*.  A lekérdezés visszaadja csak azt jelzi, hogy az aktuális időponthoz képest ebben a tartományban jöttek létre.  
   c. **Riasztási gyakoriságot** határozza meg, milyen gyakran kell futtatni a lekérdezést.  A jelen példában adja meg a *5* percben, amely a megadott időszak belül esedékes.  
   d. Válassza ki **metrikus** , és írja be *90* a **összesítve érték** , és írja be *3* a **eseményindító riasztás alapján**   
   e. A **műveletek**, e-mail értesítések letiltása.
6. Kattintson a **mentése** a riasztási szabály befejezéséhez. Futtatása azonnal elindul.<br><br> ![Riasztási szabály – példa](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

A Naplóelemzési riasztási szabályok által létrehozott riasztás rekordok típusa lehet **riasztás** és az egy SourceSystem **OMS**.<br><br> ![Riasztási eseményeit – példa](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>Riasztási műveletek
A riasztások például hozzon létre az e-mail értesítések, indítsa el a speciális műveleteket is végrehajthat egy [Automation-runbook](../automation/automation-runbook-types.md), egy webhook a ITSM incidenskezelés rendszerben, vagy a eseményrekord létrehozásához használja a [informatikai Service Management-összekötő megoldás](log-analytics-itsmc-overview.md) adott válaszként, ha a riasztási feltétel teljesülése esetén.   

E-mailek műveletek részleteit a riasztást e-mail küldése egy vagy több címzett. Megadhatja, hogy az e-mail tárgya, de annak tartalma Naplóelemzési által összeállított szabványos formátumban.  Most frissítés a riasztási szabály a korábban létrehozott, és konfigurálja úgy, hogy e-mail értesíti helyett a riasztási rekord, a naplófájl-keresési aktívan figyelését.     

1. Az OMS-portálon, a felső menüben válassza ki **beállítások** majd **riasztások**.
2. A riasztási szabályok a listából kattintson a ceruza ikonra a korábban létrehozott riasztás mellett.
3. A **műveletek** területen e-mail értesítések engedélyezése.
4. Adjon meg egy **tulajdonos** az e-mailek, például a *processzor kihasználtsága meghaladja küszöbérték > 90*.
5. A címzett egy vagy több e-mail címek hozzáadása a **címzettek** mező.  Ha több címet ad meg, majd külön a címeket pontosvesszővel (;).
6. Kattintson a **mentése** a riasztási szabály befejezéséhez. Futtatása azonnal elindul.<br><br> ![Az e-mailben értesítést riasztási szabálya](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan riasztás szabályok proaktív azonosítása és válaszolni a problémát, a napló keresések futtatásakor ütemezett időközönként, és egy adott feltételeknek.  

Kövesse a hivatkozásra kattintva megtekintheti az előre elkészített Naplóelemzési parancsfájl minták.  

> [!div class="nextstepaction"]
> [Naplófájl Analytics parancsfájl minták](powershell-samples.md)