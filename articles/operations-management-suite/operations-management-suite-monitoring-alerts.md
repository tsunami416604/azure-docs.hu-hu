---
title: Riasztás kezelése a Microsoft-termékek figyelése |} Microsoft Docs
description: Riasztás néhány problémát jelöl, amely a rendszergazda figyelmet igényel.  Ez a cikk különbségeket mutatja be, hogyan riasztások létrehozása és kezelése a System Center Operations Manager (SCOM) és a Naplóelemzési, és a két termék egy hibrid riasztáskezelési stratégia kihasználva a gyakorlati tanácsokat.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865770"
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>A Microsoft figyelési riasztások kezelése
Riasztás néhány problémát jelöl, amely a rendszergazda figyelmet igényel.  Nincsenek a köztük lévő különbségeket System Center Operations Manager (SCOM) és a Naplóelemzési Operations Management Suite (OMS) riasztások létrehozását, hogyan kezeli, és elemezni, és hogyan értesítés jelenik meg, hogy egy kritikus problémát észlelt.

## <a name="alerts-in-operations-manager"></a>A riasztásokat az Operations Manager programban
Scom riasztások akkor jönnek létre egyedi szabályok vagy figyelők egy adott probléma jelzi.  A figyelő riasztást generálhat, amikor azt hiba állapotba kerül, amíg egy szabály riasztás előfordulhat, hogy néhány kritikus problémát, amely nem közvetlenül kapcsolódik a felügyelt objektumok állapotát jelzi.  Felügyeleti csomagok különböző munkafolyamatokat, hogy az alkalmazás vagy szolgáltatás, amely az általuk kezelt tartalmaznak.  Az új felügyeleti csomag konfigurálásának folyamata az hangolása ezzel biztosíthatja, hogy túl sok riasztás a problémákra, amelyek nem a kritikus fontolja meg nem érkezik.

![SCOM riasztás nézet](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM teljes riasztáskezelési biztosít, amelyek módosíthatók a rendszergazdák által a probléma megoldásához munkánk állapotú riasztásokat.  Ha a probléma megoldódott, a rendszergazda az aktív riasztások megjelenítése nézetekben állítja be a riasztás bezárására ekkor már nem jelenik meg.  A figyelők által generált riasztások automatikusan feloldható, amikor a monitor visszatér megfelelő állapotba.

![Riasztási állapot](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>A Naplóelemzési riasztások
A Naplóelemzési riasztást a napló lekérdezésből, amely rendszeres időközönként automatikusan fut jön létre.  A napló lekérdezésből riasztási szabályt hozhat létre.  A lekérdezés a megadott feltételeknek megfelelő eredményeket ad vissza, ha riasztás jön létre.  Ennek oka lehet egy adott lekérdezés, amely riasztást hoz létre, ha egy adott esemény észlelhető, vagy használhat egy általános lekérdezés, amely egy adott alkalmazáshoz kapcsolódó hiba mindenképpen keresi.

Napló Analytics riasztások az OMS-tárházba eseményként vannak megírva, és a napló lekérdezéssel lehet beolvasni.  Nem rendelkeznek egy állapot SCOM események például, hogy adhatja meg, ha a probléma megoldódott.

![OMS riasztás](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Amikor a SCOM Naplóelemzési használ adatforrásként, SCOM riasztások írja az OMS-tárház módon hozhatók létre és módosíthatók.  

![SCOM-riasztás](media/operations-management-suite-monitoring-alerts/scom-alert.png)

A [Riasztáskezelési megoldás](http://technet.microsoft.com/library/mt484092.aspx) aktív és több közös lekérdezéseket, amelyekkel különböző készleteinek riasztások összegzését tartalmazza.  Ez lehetővé teszi az scom jelentés mint a riasztások hatékonyabb elemzése.  Részletekbe menően tárhatják meg az összesítéseket a részletes adatok, és a riasztások különböző készleteinek alkalmi lekérdezések létrehozása.

![Riasztási felügyeleti megoldás](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Értesítések
Scom értesítéseket küldeni levelezőkiszolgáló vagy szöveges riasztás bizonyos feltételeknek.  Attól függően, hogy a figyelt objektumként ilyen feltételek, a riasztás súlyossága, milyen típusú problémát észlelt az értesítés más személyek rendelkező másik értesítés-előfizetés vagy az időpontot is létrehozhat.

Néhány előfizetések segítségével számos felügyeleti csomagok teljes értesítési végrehajtása.

![SCOM-riasztások](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Naplóelemzési értesítheti, levelezési keresztül, hogy létrejött-e riasztást úgy, hogy az e-mail értesítési műveletet minden egyes [riasztási szabály](http://technet.microsoft.com/library/mt614775.aspx).  Nincs SCOM képességét az előfizetés kezelhető egyetlen szabállyal több riasztás.  Is saját riasztási szabályok létrehozásához, mert OMS nem tartalmaz előre konfigurált szükséges.

![Log Analytics-riasztások](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Meg nem teljesen SCOM riasztások kezelése a Naplóelemzési azonban csak akkor módosíthatja azokat az operatív konzolon óta.  A Naplóelemzési akkor hasznos, egy riasztás kezelés feldolgozni, ha a tartozó elemzésére szolgáló eszközöket biztosít, hogy SCOM önmagában nem rendelkezik.

## <a name="alert-remediation"></a>Riasztási szervizelés
[Szervizelés](http://technet.microsoft.com/library/mt614775.aspx) automatikusan kijavítja a hibát, egy riasztás által azonosított kísérlet hivatkozik.

SCOM lehetővé teszi a diagnosztikai és helyreállítási műveletek futtatásához írja be a nem megfelelő állapot figyelő adott válaszként.  Ez akkor fordul elő egyidejű riasztás létrehozása a képernyőn.  Diagnosztikai és helyreállítási műveletek általában valósul meg egy az ügynökön futó parancsfájlhoz.  A diagnosztika megkísérli gyűjtsön további információt a probléma, amíg a helyreállítási művelet megkísérli a probléma megoldásához.

Naplóelemzési lehetővé teszi a elindítani egy [Azure Automation-runbook](https://azure.microsoft.com/documentation/services/automation/) , vagy hívja a webhook a Log Analytics-riasztások adott válaszként.  Runbookok PowerShell megvalósított összetett logikát is tartalmazhat.  A parancsfájl az Azure-ban fut, és elérhető bármely Azure-erőforrások vagy külső forrásanyag is elérhető a felhő.  Azure Automation szolgáltatásbeli eltávolíthatnak-e a runbookok végrehajtása a kiszolgálón a helyi adatközpontban, de ez a funkció már nem érhető el, a runbook indítása a Naplóelemzési riasztás esetén.

A helyreállítások scom és OMS runbookjai tartalmazhatnak PowerShell-parancsfájlok, de helyreállítások nehezebb létrehozása és kezelése, mert azok tartalmaznia kell egy felügyeleti csomagot.  A Runbookok szerzői, tesztelése és a runbookok kezelésére funkciókat biztosító Azure Automation vannak tárolva.

Ha SCOM adatforrásként Naplóelemzési használja, létrehozhat egy olyan napló lekérdezéssel lekérni az OMS-tárházban tárolt SCOM riasztásokat Naplóelemzési riasztás.  Ez lehetővé teszi a SCOM-riasztások válaszul egy Azure Automation-runbook futtatásához.  Természetesen a runbook fog futni az Azure-ban, mert nem lenne a helyszíni problémák helyreállítások életképes stratégiáját.

## <a name="next-steps"></a>Következő lépések
* További részleteit [riasztások a System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).

