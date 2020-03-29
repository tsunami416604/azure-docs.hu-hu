---
title: Az Azure Automation runbookjainak figyelése metrikariasztásokkal
description: Ez a cikk végigvezeti az Azure Automation-runbookok metrikákon alapuló figyelésén
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 9bd028157b33817898ef69f9e47cb8b5d9b8f381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75367093"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Runbookok figyelése metrikariasztásokkal

Ebben a cikkben megtudhatja, hogyan hozhat létre riasztásokat a runbookok befejezési állapota alapján.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen.

## <a name="create-alert"></a>Riasztás létrehozása

A riasztások lehetővé teszik, hogy megadjon egy feltételt, amelyet figyelni kell, és egy műveletet, amelyet a feltétel teljesülése esetén kell végrehajtani.

Az Azure Portalon keresse meg az Automation-fiók. A **Figyelés**csoportban válassza a **Riasztások** lehetőséget, és kattintson **a + Új riasztási szabály gombra.** A cél hatóköre már definiálva van az Automation-fiókhoz.

### <a name="configure-alert-criteria"></a>Riasztási feltételek konfigurálása

1. Kattintson **a + Feltétel hozzáadása gombra.** Válassza ki a **Metrikák a** **Jel típus**, és válassza az Összes **feladat** a táblában.

2. A **Jellogika konfigurálása** lap, ahol megadhatja a riasztást kiváltó logikát. Az előzménydiagram alatt két dimenzió jelenik meg: **Runbook Name** és **Status**. A dimenziók különböző tulajdonságok egy metrika, amely az eredmények szűrésére használható. Runbook **neve**esetén jelölje ki azt a runbookot, amelyről figyelmeztetni szeretne, vagy hagyja üresen az összes runbookra vonatkozó riasztást. Az **Állapot területen**válassza ki azt az állapotot a legördülő menüből, amelyet figyelni szeretne. A runbook neve és állapotértékei, amelyek megjelennek a legördülő csak a feladatok, amelyek futottak az elmúlt héten.

   Ha olyan állapotról vagy runbookról szeretne értesítést kapni, amely nem **\+** jelenik meg a legördülő menüben, kattintson a dimenzió melletti elemre. Ez a művelet megnyit egy párbeszédpanelt, amely lehetővé teszi, hogy egyéni értéket adjon meg, amely nem adott ki a dimenzióhoz a közelmúltban. Ha olyan értéket ad meg, amely nem létezik egy tulajdonsághoz, a riasztás nem aktiválódik.

   > [!NOTE]
   > Ha nem alkalmaz nevet a **RunbookName** dimenzióhoz, ha vannak olyan runbookok, amelyek megfelelnek az állapotfeltételeknek, beleértve a rejtett rendszer runbookokat, riasztást fog kapni.

3. A **Riasztási logika**csoportban adja meg a riasztás feltételét és küszöbértékét. Az állapotod előnézete látható alatta.

4. A **Kiértékelt alapján csoportban**válassza ki a lekérdezés időtartományát, és azt, hogy milyen gyakran szeretné a lekérdezést. Ha például **az elmúlt 5 percben az** **időszak** és minden **1 perc** **gyakorisága,** a riasztás megkeresi a runbookok száma, amelyek megfelelnek a feltételeknek az elmúlt 5 percben. Ez a lekérdezés percenként fut, és ha a megadott riasztási feltételek már nem található egy 5 perces ablakban, a riasztás feloldja magát. Ha végzett, kattintson a **Kész** gombra.

   ![Erőforrás kiválasztása a riasztáshoz](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Riasztás részleteinek megadása

1. **2 alatt. Adja meg**a riasztás részleteit, adjon a riasztásnak egy rövid nevet és leírást. Állítsa be a **súlyossági szint,** hogy megfeleljen a riasztási feltétel. Öt severities kezdve 0-5. A riasztások kezelése azonos a súlyosságtól függetlenül, akkor megfelela súlyossága, hogy megfeleljen az üzleti logika.

1. A szakasz alján található egy gomb, amely lehetővé teszi a szabály befejezését követően. Alapértelmezés szerint a szabályok engedélyezve vannak a létrehozáskor. Ha a Nem lehetőséget választja, létrehozhatja a riasztást, és **letiltott** állapotban jön létre. Az Azure Monitor **szabályok** lapján kiválaszthatja, és az **Engedélyezés** gombra kattintva engedélyezheti a riasztást, ha készen áll.

### <a name="define-the-action-to-take"></a>A végrehajtandó művelet meghatározása

1. **3 alatt. Műveletcsoport definiálása**, kattintson **a + Új műveletcsoport gombra.** A műveletcsoport olyan műveletek csoportja, amelyek et több riasztásban is használhat. Ezek közé tartozhatnak, de nem kizárólagosan, e-mail értesítések, runbookok, webhooks, és még sok más. A műveletcsoportokról a [Műveletcsoportok létrehozása és kezelése](../azure-monitor/platform/action-groups.md) című témakörben olvashat bővebben.

1. A **Műveletcsoport neve** mezőben adjon meg egy felhasználóbarát és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

1. A Műveletek típusa **csoportMűveletek** **típusa**csoportjában válassza az **E-mail/SMS/Push/Voice**lehetőséget.

1. Az **E-mail/SMS/Leküldés/Hang** oldalon adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adja meg a használni kívánt érvényes e-mail-címet.

   ![E-mail konfigurálása műveletcsoport](./media/automation-alert-activity-log/add-action-group.png)

1. Kattintson az **OK** gombra az **E-mail/SMS/Leküldés/Hang** oldalon az oldal, majd az **OK** gombra a **Műveletcsoport hozzáadása** oldal bezárásához. A lapon megadott név **műveletnévként kerül mentésre.**

1. Amikor végzett, kattintson a **Mentés** gombra. Ez a művelet létrehozza a szabályt, amely figyelmezteti Önt, ha egy runbook egy bizonyos állapottal fejeződött be.

> [!NOTE]
> Amikor e-mail címet ad hozzá egy műveletcsoporthoz, a rendszer értesítést küld arról, hogy a címet hozzáadta egy műveletcsoporthoz.

## <a name="notification"></a>Értesítés

A riasztási feltételek teljesülése esetén a műveletcsoport futtatja a definiált műveletet. Ebben a cikkben a példa, egy e-mailt küld. Az alábbi kép egy példa egy e-mailt kap, miután a riasztás aktiválódik:

![E-mail értesítés](./media/automation-alert-activity-log/alert-email.png)

Ha a metrika már nem esik túl a megadott küszöbértéken, a riasztás inaktívvá válik, és a műveletcsoport futtatja a megadott műveletet. Ha egy e-mail művelettípus van kiválasztva, a rendszer egy megoldási e-mailt küld, amely jelzi, hogy megoldódott.

## <a name="next-steps"></a>További lépések

Folytassa az alábbi cikkben, hogy megismerhesse, hogyan integrálhatja a riasztásokat az Automation-fiókba.

> [!div class="nextstepaction"]
> [Egy Azure Automation-runbook aktiválásához használjon riasztást](automation-create-alert-triggered-runbook.md)
