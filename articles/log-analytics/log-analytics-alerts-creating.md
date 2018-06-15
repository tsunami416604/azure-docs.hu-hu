---
title: Riasztásokat hoznak létre az OMS szolgáltatáshoz |} Microsoft Docs
description: Naplóelemzési riasztások határozza meg az OMS-adattárban lévő fontos adatokat és is proaktív értesítést küldenek, problémák vagy meghívása műveletek kijavításának őket.  Ez a cikk ismerteti a riasztási szabály és a részletek a különböző általuk végezhető műveletek létrehozása.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: b692822660ab12f89b274cea75727fb808d673f8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31519579"
---
# <a name="working-with-alert-rules-in-log-analytics"></a>A Naplóelemzési riasztási szabályok használata

> [!NOTE]
> Ez a cikk ismerteti az OMS-portálon kezelt klasszikus Log Analytics-riasztásokat.  A Naplóelemzési riasztások [az Azure kiterjesztendő](../monitoring-and-diagnostics/monitoring-alerts-extend.md).  Ha ezzel végzett, majd létrehoz, és a riasztási szabályok az Azure portálon szerkesztése és Megjegyzés: Ebben a cikkben ismertetett eljárások használhatók.


A riasztásokat riasztási szabályok hozzák létre, amelyek rendszeres időközönként automatikus naplókereséseket futtatnak.  Akkor hozzon létre egy riasztási rekordot, ha az eredmények bizonyos feltételeknek.  A szabály ekkor automatikusan lefuttathat egy vagy több műveletet, hogy proaktívan értesítse Önt a riasztásról, vagy meghívjon egy másik folyamatot.   

Ez a cikk ismerteti a folyamatok létrehozhatja és szerkesztheti a riasztási szabályok az OMS-portálon.  A különböző beállításokról és konfigurálásukról megvalósításához szükséges logikát, lásd: [Naplóelemzési ismertetése riasztások](log-analytics-alerts.md).


## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása

Az OMS-portálon riasztási szabályt létrehozni, akkor először hozzon létre az azt jelzi, hogy a riasztás kell meghívnia napló keresése.  A **riasztás** gomb lesz elérhető, hozhat létre és konfigurálja a riasztási szabályt.

>[!NOTE]
> Legfeljebb 250 riasztási szabályok jelenleg a Naplóelemzési munkaterület hozható létre. 

1. Az OMS áttekintése lapon kattintson a **naplófájl-keresési**.
2. Hozzon létre egy új naplófájl keresési lekérdezést, vagy válasszon ki egy mentett napló keresést. 
3. Kattintson az oldal tetején található **Riasztás** lehetőségre a **Riasztási szabály hozzáadása** képernyő megnyitásához.
4. A témakörben található információk alapján riasztási szabály konfigurálása [riasztási szabályok részletei](#details-of-alert-rules) alatt.
6. Kattintson a **Mentés** gombra a riasztási szabály jóváhagyásához.  Futtatása azonnal elindul.


## <a name="edit-an-alert-rule"></a>Riasztási szabály szerkesztése
Kaphat az összes riasztási szabályok listáját a **riasztások** Naplóelemzési menüjében **beállítások**.  

![Riasztások kezelése](./media/log-analytics-alerts/configure.png)

1. Az OMS-konzol válassza ki a **beállítások** csempére.
2. Válassza ki **riasztások**.

Ez a nézet több műveletet is elvégezheti.

* A szabály letiltása kiválasztásával **ki** látható.
* Riasztási szabály szerkesztése látható ceruza ikonra kattintva.
* Riasztási szabály törlése gombra kattintva a **X** ikon látható. 

## <a name="details-of-alert-rules"></a>Riasztási szabályok részletei
Hozzon létre vagy szerkesztésekor a riasztási szabály az OMS-portálon, akivel együttműködik a **riasztás szabály hozzáadása** vagy **riasztást szabály szerkesztése** lap.  Az alábbi táblázatban láthatók a mezők ezen a képernyőn.

![Riasztási szabály](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Riasztási információkat
Ezek a alapbeállításainak megadása a riasztási szabály és a riasztásokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:---|
| Name (Név) | A riasztási szabályt azonosító egyedi nevet. Ez a név szerepel az a szabály által létrehozott riasztások.  |
| Leírás | A riasztási szabály opcionális leírása. |
| Súlyosság |Az a szabály által létrehozott riasztások súlyossága. |

### <a name="search-query-and-time-window"></a>Keresési lekérdezés- és ablak
Annak meghatározásához, ha minden riasztást kell létrehozni a rekordot ad vissza a keresési lekérdezés- és ablak értékeli ki.

| Tulajdonság | Leírás |
|:--- |:---|
| Keresési lekérdezés | Ez az a futtatni kívánt lekérdezés.  Ez a lekérdezés által visszaadott rekordok használható annak meghatározásához, hogy riasztás jöjjön létre.<br><br>Válassza ki **használata aktuális keresési lekérdezés** az aktuális lekérdezés, vagy válasszon egy meglévő mentett keresés a listából.  A lekérdezés szintaxisa valósul meg a szövegmezőben, ahol ezt módosíthatja, ha szükséges. |
| Időablak |Adja meg a lekérdezés időintervallumát.  A lekérdezés visszaadja csak azt jelzi, hogy az aktuális időponthoz képest ebben a tartományban jöttek létre.  Ez bármilyen 5 perc és 24 óra közötti érték lehet.  Nagyobb vagy egyenlő a riasztások gyakorisága kell.  <br><br> Például ha a időszak 60 percre van beállítva, és a lekérdezés futtatása, 1:15 előtti, csak a 12:15 előtti és 1:15 előtti között létrejövő rekordok visszatér. |

Ha a riasztási szabály a időszak ad meg, a keresési feltételeknek időtartományt a meglévő rekordok száma jelenik meg.  Ez segít meghatározni, amely erre azért van szükség a kívánt eredmények számát.

### <a name="schedule"></a>Ütemezés
Határozza meg, milyen gyakran a keresési lekérdezés futtatása.

| Tulajdonság | Leírás |
|:--- |:---|
| A riasztások gyakorisága | Meghatározza, hogy milyen gyakran kell futtatni a lekérdezést. Bármely érték 5 perc és 24 óra közötti lehet. A időszak kisebbnek vagy azzal egyenlőnek kell lennie.  Ha az érték nagyobb, mint az időszak, majd azzal kockáztatja alatt nem talált rekordokat.<br><br>Vegye figyelembe például egy olyan időkeretet, 30 perc és 60 perc gyakorisága.  Ha a lekérdezés futtatása, 1:00, 12:30 és 1:00 PM rekordok adja vissza.  A következő szeretné futtatni a lekérdezést ideje 2:00 amikor meghaladná a 1:30 és 2:00 között rögzíti.  1:00 és 1:30 között létrejövő rekordok volna soha nem értékelhető ki. |


### <a name="generate-alert-based-on"></a>Riasztás létrehozása ez alapján
Határozza meg a feltételeket, amelyek fogja értékelni a meghatározásához, ha a riasztás létrejöjjön keresési lekérdezés eredményeit.  Ezen adatok kiválasztott riasztási szabály típusától függően eltérőek lesznek.  Részletek kaphat a különböző riasztási szabály esetében a [Naplóelemzési ismertetése riasztások](log-analytics-alerts.md).

| Tulajdonság | Leírás |
|:--- |:---|
| Végfelhasználói értesítések letiltása | A riasztási szabály tiltási bekapcsolásakor műveletek a szabály új riasztás létrehozása után egy meghatározott ideig le vannak tiltva. A szabály mindig fut, és riasztási rekordokat hoz létre, a feltétel teljesülése esetén. Ez egy duplikált műveletek futtatása nélkül a probléma elhárításához idő engedélyezése. |

#### <a name="number-of-results-alert-rules"></a>Eredmények riasztási szabályok száma

| Tulajdonság | Leírás |
|:--- |:---|
| Eredmények száma |Riasztás jön létre, ha a lekérdezés által visszaadott rekordok számát **nagyobb, mint** vagy **kisebb, mint** megadja az értéket.  |

#### <a name="metric-measurement-alert-rules"></a>Metrika mérési riasztási szabályok

| Tulajdonság | Leírás |
|:--- |:---|
| Összesített érték | Az küszöbérték, amely minden összesített a eredmények meghaladható figyelembe kell venni a sérülése. |
| Triggerriasztás alapja | Létrejön egy riasztás megszegése száma.  Megadhat **megszegése teljes** megszegése az eredmények között bármilyen kombinációját beállítása vagy **egymást követő megszegése** megkövetelése, hogy a behatolás egymást követő mintákban kell-e magukat. |

### <a name="actions"></a>Műveletek
A riasztási szabályok mindig létrehoz egy [rekord riasztási](#alert-records) a küszöbérték elérése esetén.  Megadhatja, hogy egy vagy több válaszokat kell futtatnia, például egy e-mailek küldéséhez, vagy a runbook indítása.



#### <a name="email-actions"></a>E-mailek műveletek
E-mailek műveletek a riasztás részleteit e-mail küldése egy vagy több címzett.

| Tulajdonság | Leírás |
|:--- |:---|
| E-mailes értesítés |Adja meg **Igen** Ha azt szeretné, hogy egy e-mailt kell küldeni a figyelmeztetés jelenik meg. |
| Tárgy |A tulajdonos e-mailben.  Az üzenet törzse nem módosítható. |
| Címzettek |Címzett e-mail címét.  Ha több címet ad meg, pontosvesszővel (;) válassza el őket. |

#### <a name="webhook-actions"></a>Webhookműveletek
Webhookműveletek lehetővé teszi egy külső folyamatban egy HTTP POST kérelemben keresztül.

| Tulajdonság | Leírás |
|:--- |:---|
| Webhook |Adja meg **Igen** Ha azt szeretné, hogy a webhook hívására a figyelmeztetés jelenik meg. |
| Webhook URL-CÍMÉT |A webhook URL-CÍMÉT. |
| Egyéni JSON-adattartalmat tartalmazza |Válassza ezt a lehetőséget, ha azt szeretné, hogy az alapértelmezett adattartalom lecseréli a egyéni hasznos adatok között. |
| Adjon meg egyéni JSON-adattartalmat |A webhook egyéni hasznos.  Lásd az előző szakaszát. |

#### <a name="runbook-actions"></a>Runbook-műveletek
Runbook műveletek az Azure Automationben runbook indítása. 

>[!NOTE]
> A munkaterület engedélyezni kell a művelet telepíti az Automation-megoldást kell rendelkeznie. 


| Tulajdonság | Leírás |
|:--- |:---|
| Forgatókönyv | Adja meg **Igen** Ha egy Azure Automation-runbook elindítja a riasztás aktiválása.  |
| Automation-fiók | Adja meg a runbookok a kijelölt Automation-fiók.  Ez az a műveleti fiók, amely csatolva van a munkaterületen. |
| Válassza ki a runbookot | Válassza ki a runbookot, amely elindítja a riasztás létrehozásakor. |
| Futtassa a | Válassza ki **Azure** a runbook futtatásához a felhőben.  Válassza ki **hibridfeldolgozó** a runbook futhat az ügynök [hibrid forgatókönyv-feldolgozó](../automation/automation-hybrid-runbook-worker.md ) telepítve.  |




## <a name="next-steps"></a>További lépések
* Telepítse a [Riasztáskezelési megoldás](log-analytics-solution-alert-management.md) Naplóelemzési riasztások gyűjtése a System Center Operations Manager (SCOM) együtt létrehozott riasztások elemzéséhez.
* Tudjon meg többet az [keresések jelentkezzen](log-analytics-log-searches.md) , amely riasztást generál.
* A forgatókönyv a [konfigurálása egy webook](log-analytics-alerts-webhooks.md) a riasztási szabályt.  
* Ismerje meg, hogyan írhat [az Azure Automation runbookjai](https://azure.microsoft.com/documentation/services/automation) riasztások által azonosított problémák megoldásáról.

