---
title: Azure Automation-runbook tevékenység naplók riasztások figyelése
description: Ez a cikk végigvezeti a műveletnapló az Azure Automation-runbook figyelése
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a0bd291af98477308cda898580fd52e33b1e6bbd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360377"
---
# <a name="monitoring-runbooks-with-azure-activity-log-alerts"></a>Runbookok Azure tevékenység napló riasztások figyelése

Ebből a cikkből megtanulhatja a riasztások alapján a befejezettség állapotát a runbookok létrehozása.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure: https://portal.azure.com

## <a name="create-alert"></a>Riasztás létrehozása

Riasztások engedélyezi annak meghatározását, figyelje a feltételt és egy adott feltétel teljesülése esetén végrehajtandó műveletet.

Az Azure-portálon lépjen a **minden szolgáltatás** válassza **figyelő**. A figyelő lapon válassza az **riasztások** kattintson **+ Új riasztási szabály**.

### <a name="define-the-alert-condition"></a>A riasztási feltétel megadása

1. A **1. Adja meg a riasztási feltétel**, kattintson a **+ válassza célként**. Válassza ki az előfizetését, majd a **erőforrástípus szűrés**, jelölje be **Automation-fiók**. Válassza ki az Automation-fiók, és kattintson a **végzett**.

   ![Válasszon ki egy erőforrástípust a riasztás](./media/automation-alert-activity-log/select-resource.png)

### <a name="configure-alert-criteria"></a>Riasztási feltételek

1. Kattintson a **+ adja meg a feltételeket**. Válassza ki **metrikák** a a **típus jelezze**, és válassza a **feladatok teljes száma** a táblából.

1. A **konfigurálása jel logika** lap, ahol megadhatja a programot, amely elindítja a riasztást. A korábbi grafikon, lehetősége lesz két dimenzió alatt **Runbook neve** és **állapot**. Dimenziók egy olyan metrikajelentés eredmények szűrését használható különböző tulajdonságai. A **Runbook neve**, válassza ki a riasztás, vagy hagyja üresen a riasztás a runbookok összes runbookot. A **állapot**, jelölje ki a figyelni kívánt legördülő egy állapotot. A runbook nevét és állapotát, amelyek szerepelnek a legördülő értékei csak az elmúlt héten futtatott feladatokhoz.

   Ha azt szeretné, riasztást küld, egy állapot vagy a runbook, amely nem jelenik meg a legördülő menüből, kattintson a **\+** a dimenzió mellett. Ekkor megnyílik egy párbeszédpanel, amely lehetővé teszi, hogy meg egy egyéni értéket, amely még nem kibocsátott, hogy a dimenzió nemrég. Ha megad egy értéket, amely a riasztás nem indul a tulajdonság nem létezik.

1. A **logika riasztási**, adja meg a feltétel és a riasztás küszöbértéke. A megadott feltétel előnézete alatt jelenik meg.

1. A **alapján Evaluated** válassza ki a lekérdezés timespan, és hogy milyen gyakran szeretné, hogy a lekérdezés futott. Például, ha úgy dönt, **az elmúlt 5 percben** a **időszak** és **minden 1 perces** a **gyakorisága**, a riasztás keresi a számot a runbookok az elmúlt 5 percben, amely megfelel a feltételeknek. Ez a lekérdezés percenként futtatott, és ha meghatározott riasztási kritériumok már nem található egy 5 perces ablakban, a riasztás oldja fel saját magát. Ha befejezte, kattintson a **végzett**.

   ![Válasszon ki egy erőforrástípust a riasztás](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>A riasztás részleteinek megadása

1. A **2. Adja meg a riasztás részleteinek**, a riasztást adjon egy rövid nevet és leírást. Állítsa be a **súlyossági** a riasztási feltétel kereséséhez. 0 és 5 közötti öt fokozatok van. A riasztások kezelik a súlyosságú ugyanazt független, az üzleti logikát megfelelő súlyossági párosítható.

1. A szakasz alján van, amely lehetővé teszi, hogy a szabály létrehozása után engedélyezése gomb. Alapértelmezés szerint a szabályok engedélyezve van a létrehozásakor. Ha nem választja, a riasztás hozhat létre, és a jön létre egy **letiltott** állapotát. Az a **szabályok** lap Azure figyelőben, válasszon ki és kattintson **engedélyezése** engedélyezése a riasztást, amikor készen áll.

### <a name="define-the-action-to-take"></a>Adja meg a műveletet

1. A **3. Adja meg a művelet csoport**, kattintson a **+ új művelet csoport**. Egy olyan műveletek között több riasztás is használhatja. Ezek lehetnek, de nem korlátozódnak, e-mail-értesítések, a runbookok, webhookok és sok más. Művelet csoportokkal kapcsolatos további tudnivalókért lásd: [létrehozása és művelet csoportok kezelése](../monitoring-and-diagnostics/monitoring-action-groups.md)

1. Az a **művelet csoportnév** mezőbe adjon neki egy rövid nevét és rövid nevét. A rövid nevét használja a teljes műveletet csoport neve helyett amikor ez a csoport értesítések küldése.

1. Az a **műveletek** szakaszában **MŰVELETTÍPUS**, jelölje be **e-mailek és SMS/leküldéses/szóbeli**.

1. Az a **e-mailek és SMS/leküldéses/szóbeli** lapján adjon neki egy nevet. Ellenőrizze a **E-mail** jelölőnégyzetet, és adja meg egy érvényes e-mail címet használni.

   ![E-mailek művelet csoport konfigurálása](./media/automation-alert-activity-log/add-action-group.png)

1. Kattintson a **OK** a a **e-mailek és SMS/leküldéses/szóbeli** zárja be, és kattintson a lap **OK** bezárásához a **művelet csoport hozzáadása** lap. Ezen a lapon megadott név-es értékként menti a **MŰVELETNÉV**.

1. Amikor végzett, kattintson a **Mentés** gombra. Ezzel létrehozza a szabály riasztást küld, amikor egy runbook egy adott állapotban befejeződött.

> [!NOTE]
> Ha egy művelet csoporthoz ad hozzá egy e-mail címet, e-mailben értesítést küld arról, hogy a cím művelet csoporthoz hozzáadva.

## <a name="notification"></a>Értesítés

A figyelmeztetési feltétel teljesülése esetén, a művelet csoport fusson az általa meghatározott művelet. Ez a cikk a példában az e-mail elküldésekor történik. Az alábbi képen látható egy példa egy e-mailt kap, miután a figyelmeztetés jelenik meg:

![E-mail értesítés](./media/automation-alert-activity-log/alert-email.png)

Amennyiben az a mérték már nem kívül meghatározott küszöbértéket, a riasztás inaktív állapotú, és a művelet csoport futtatása az általa meghatározott művelet. Ha egy e-mailek művelettípust be van jelölve, egy megoldási e-mailt küld arról, hogy megoldódott.

## <a name="next-steps"></a>További lépések

Folytassa a következő cikkben tájékozódhat az egyéb módon, hogy alertings integrálhatja az Automation-fiók be.

> [!div class="nextstepaction"]
> [Használja a riasztást egy Azure Automation-runbook elindítása](automation-create-alert-triggered-runbook.md)