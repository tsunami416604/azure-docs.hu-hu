---
title: Azure Automation runbookok figyelése metrikus riasztásokkal
description: Ez a cikk azt ismerteti, hogyan figyelheti a runbookok a mérőszámok alapján.
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: c5f51ec34ea0b31f4c8fc349246b439425112104
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715851"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Runbookok monitorozása metrikariasztásokkal

Ebből a cikkből megtudhatja, hogyan hozhat létre riasztásokat a runbookok befejezési állapota alapján.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen.

## <a name="create-alert"></a>Riasztás létrehozása

A riasztások lehetővé teszik, hogy megadjon egy feltételt a figyeléshez, valamint egy műveletet, amely a feltétel teljesülése esetén végrehajtandó.

A Azure Portal navigáljon az Automation-fiókjához. A **figyelés**területen válassza a **riasztások** elemet, majd kattintson az **+ új riasztási szabály**elemre. A cél hatóköre már definiálva van az Automation-fiókhoz.

### <a name="configure-alert-criteria"></a>Riasztási feltételek konfigurálása

1. Kattintson a **+ feltételek hozzáadása**lehetőségre. Válassza a **mérőszámok** lehetőséget a **jel típusához**, és válassza a **teljes feladatok** elemet a táblából.

2. A **jel logikai beállítása** oldalon megadhatja a riasztást kiváltó logikát. A korábbi gráfban két dimenziót, a **Runbook nevét** és **állapotát**mutatjuk be. A méretek az eredmények szűrésére szolgáló metrika különböző tulajdonságai. A **Runbook neve**mezőben válassza ki azt a Runbook, amelyről riasztást szeretne kapni, vagy hagyja üresen a riasztást az összes runbookok. Az **állapot**beállításnál válasszon ki egy állapotot a figyelni kívánt legördülő listából. A legördülő listában megjelenő runbook neve és állapota csak az elmúlt héten futtatott feladatokhoz használható.

   Ha olyan állapotra vagy runbook szeretne riasztást kapni, amely nem jelenik meg a legördülő listában, kattintson a **\+** dimenzió melletti elemre. Ez a művelet egy olyan párbeszédpanelt nyit meg, amely lehetővé teszi egy egyéni érték megadását, amely az adott dimenzióhoz még nem lett kibocsátva a közelmúltban. Ha olyan értéket ad meg, amely nem létezik olyan tulajdonsághoz, amelyet a riasztás nem indít el.

   > [!NOTE]
   > Ha nem alkalmaz nevet a **RunbookName** -dimenzióhoz, ha vannak olyan runbookok, amelyek megfelelnek az állapot feltételeinek, ami magában foglalja a rejtett rendszer runbookok, riasztást fog kapni.

3. A **riasztási logika**szakaszban adja meg a riasztás feltételét és küszöbértékét. A feltétel megadott előnézete az alábbi ábrán látható.

4. A **kiértékelve**alapján területen válassza ki a lekérdezés TimeSpan, és azt, hogy milyen gyakran szeretné futtatni a lekérdezést. Ha például **az elmúlt 5 percben** az **időtartam** és **1 percenként** lehetőséget **választja, a**riasztás a feltételnek az elmúlt 5 percben teljesített runbookok számát keresi. A lekérdezés percenként fut, és ha a megadott riasztási feltételek már nem találhatók meg egy 5 perces ablakban, a riasztás feloldja magát. Ha végzett, kattintson a **Kész** gombra.

   ![Válasszon ki egy erőforrást a riasztáshoz](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Riasztás részleteinek megadása

1. A **2. résznél. Adja meg a riasztás részleteit**, adjon meg egy rövid nevet és egy leírást. Állítsa be a **súlyosságot** a riasztási feltételnek megfelelően. A 0 és 5 közötti értékben öt megszakítás áll fenn. A riasztásokat a súlyosságtól függetlenül kezeli a rendszer, így a súlyossága megegyezik az üzleti logikával.

1. A szakasz alján egy gomb, amely lehetővé teszi, hogy a szabályt a befejezés után engedélyezze. Alapértelmezés szerint a szabályok engedélyezve vannak a létrehozáskor. Ha a nem lehetőséget választja, létrehozhatja a riasztást, és **letiltott** állapotban is létrehozhatja. A Azure Monitor **szabályok** lapján kiválaszthatja, majd az **Engedélyezés** lehetőségre kattintva engedélyezheti a riasztást, ha elkészült.

### <a name="define-the-action-to-take"></a>A végrehajtandó művelet megadása

1. **3. alatt. Adja meg a műveleti csoportot**, majd kattintson az **+ új műveleti csoport**elemre. A műveleti csoport olyan műveletek csoportja, amelyek több riasztáson is használhatók. Ezek lehetnek például a következők: e-mail-értesítések, runbookok, webhookok és sok más. A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése](../azure-monitor/platform/action-groups.md)

1. A **Műveletcsoport neve** mezőben adjon meg egy felhasználóbarát és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

1. A **műveletek** szakasz **Művelettípus**területén válassza az **E-mail/SMS/leküldés/hang**lehetőséget.

1. Az **E-mail/SMS/Leküldés/Hang** oldalon adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adja meg a használni kívánt érvényes e-mail-címet.

   ![E-mail konfigurálása műveletcsoport](./media/automation-alert-activity-log/add-action-group.png)

1. Kattintson az **OK** gombra az **E-mail/SMS/Leküldés/Hang** oldalon az oldal, majd az **OK** gombra a **Műveletcsoport hozzáadása** oldal bezárásához. Az ezen a lapon megadott nevet a **művelet neveként**menti a rendszer.

1. Amikor végzett, kattintson a **Mentés** gombra. Ez a művelet létrehozza azt a szabályt, amely riasztást küld, ha egy runbook bizonyos állapottal fejeződött be.

> [!NOTE]
> E-mail-cím műveleti csoportba való felvételekor a rendszer értesítő e-mailt küld, amely megadja, hogy a cím hozzá lett adva egy műveleti csoporthoz.

## <a name="receive-notification"></a>Értesítés fogadása

Ha a riasztási feltételek teljesülnek, a műveleti csoport futtatja a megadott műveletet. Ebben a cikkben egy e-mailt küldünk. Az alábbi képen egy példa látható a riasztás elindítása után kapott e-mailekre:

![E-mail riasztás](./media/automation-alert-activity-log/alert-email.png)

Ha a metrika már nem a meghatározott küszöbértéken kívül esik, a rendszer inaktiválja a riasztást, és a műveleti csoport futtatja a definiált műveletet. Ha egy e-mail-Művelettípus van kiválasztva, a rendszer elküld egy feloldási e-mailt, amely megoldódott.

## <a name="next-steps"></a>Következő lépések

* [Azure Automation runbook elindítására szolgáló riasztás használata](automation-create-alert-triggered-runbook.md)
