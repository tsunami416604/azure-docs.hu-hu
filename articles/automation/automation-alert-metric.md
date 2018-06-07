---
title: Azure Automation-runbook metrika riasztások figyelése
description: Ez a cikk bemutatja, hogyan figyelési metrikákat kijelentkezés alapú Azure Automation-forgatókönyv
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a8a4b24e6b2503f64cc3fd7f4fd8c7400c547d4d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655076"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>A metrika riasztásokkal kapcsolatos figyelési forgatókönyvek

Ebből a cikkből megtanulhatja a riasztások alapján a befejezettség állapotát a runbookok létrehozása.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen

## <a name="create-alert"></a>Riasztás létrehozása

Riasztások engedélyezi annak meghatározását, figyelje a feltételt és egy adott feltétel teljesülése esetén végrehajtandó műveletet.

Az Azure-portálon lépjen a **minden szolgáltatás** válassza **figyelő**. A figyelő lapon válassza az **riasztások** kattintson **+ Új riasztási szabály**.

### <a name="define-the-alert-condition"></a>A riasztási feltétel megadása

1. Az **1. Riasztási feltétel megadása** szakaszban kattintson a **+ Cél kiválasztása** gombra. Válassza ki az előfizetését, majd a **erőforrástípus szűrés**, jelölje be **Automation-fiók**. Válassza ki az Automation-fiók, és kattintson a **végzett**.

   ![Válasszon ki egy erőforrástípust a riasztás](./media/automation-alert-activity-log/select-resource.png)

### <a name="configure-alert-criteria"></a>Riasztási feltételek

1. Kattintson a **+ adja meg a feltételeket**. Válassza ki **metrikák** a a **típus jelezze**, és válassza a **feladatok teljes száma** a táblából.

1. A **konfigurálása jel logika** lap, ahol megadhatja a programot, amely elindítja a riasztást. A korábbi grafikon, lehetősége lesz két dimenzió alatt **Runbook neve** és **állapot**. Dimenziók egy olyan metrikajelentés eredmények szűrését használható különböző tulajdonságai. A **Runbook neve**, válassza ki a riasztás, vagy hagyja üresen a riasztás a runbookok összes runbookot. A **állapot**, jelölje ki a figyelni kívánt legördülő egy állapotot. A runbook nevét és állapotát, amelyek szerepelnek a legördülő értékei csak az elmúlt héten futtatott feladatokhoz.

   Ha azt szeretné, riasztást küld, egy állapot vagy a runbook, amely nem jelenik meg a legördülő menüből, kattintson a **\+** a dimenzió mellett. Ekkor megnyílik egy párbeszédpanel, amely lehetővé teszi, hogy meg egy egyéni értéket, amely még nem kibocsátott, hogy a dimenzió nemrég. Ha megad egy értéket, amely a riasztás nem indul a tulajdonság nem létezik.

1. A **logika riasztási**, adja meg a feltétel és a riasztás küszöbértéke. A megadott feltétel előnézete alatt jelenik meg.

1. A **alapján Evaluated** válassza ki a lekérdezés timespan, és hogy milyen gyakran szeretné, hogy a lekérdezés futott. Például, ha úgy dönt, **az elmúlt 5 percben** a **időszak** és **minden 1 perces** a **gyakorisága**, a riasztás keresi a számot a runbookok az elmúlt 5 percben, amely megfelel a feltételeknek. Ez a lekérdezés percenként futtatott, és ha meghatározott riasztási kritériumok már nem található egy 5 perces ablakban, a riasztás oldja fel saját magát. Ha végzett, kattintson a **Kész** gombra.

   ![Válasszon ki egy erőforrástípust a riasztás](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>A riasztás részleteinek megadása

1. A **2. Riasztás részleteinek megadása** szakaszban adja meg a riasztás rövid nevét és leírását. Állítsa be a **súlyossági** a riasztási feltétel kereséséhez. 0 és 5 közötti öt fokozatok van. A riasztások kezelik a súlyosságú ugyanazt független, az üzleti logikát megfelelő súlyossági párosítható.

1. A szakasz alján van, amely lehetővé teszi, hogy a szabály létrehozása után engedélyezése gomb. Alapértelmezés szerint a szabályok engedélyezve van a létrehozásakor. Ha nem választja, a riasztás hozhat létre, és a jön létre egy **letiltott** állapotát. Az a **szabályok** lap Azure figyelőben, válasszon ki és kattintson **engedélyezése** engedélyezése a riasztást, amikor készen áll.

### <a name="define-the-action-to-take"></a>Adja meg a műveletet

1. A **3. Műveleti csoport megadása** szakaszban kattintson az **+ Új műveletcsoport** gombra. A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. Ezek a teljesség igénye nélkül a következők lehetnek: e-mail-értesítések, runbookok, webhookok stb. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../monitoring-and-diagnostics/monitoring-action-groups.md) szóló cikkben talál.

1. A **Műveletcsoport neve** mezőben adjon meg egy felhasználóbarát és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

1. Az a **műveletek** szakaszában **MŰVELETTÍPUS**, jelölje be **e-mailek és SMS/leküldéses/szóbeli**.

1. Az **E-mail/SMS/Leküldés/Hang** oldalon adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adja meg a használni kívánt érvényes e-mail-címet.

   ![E-mail konfigurálása műveletcsoport](./media/automation-alert-activity-log/add-action-group.png)

1. Kattintson az **OK** gombra az **E-mail/SMS/Leküldés/Hang** oldalon az oldal, majd az **OK** gombra a **Műveletcsoport hozzáadása** oldal bezárásához. Ezen a lapon megadott név-es értékként menti a **MŰVELETNÉV**.

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