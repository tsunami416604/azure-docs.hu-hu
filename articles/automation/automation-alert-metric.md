---
title: Azure Automation-runbookok a metrikákhoz kapcsolódó riasztások figyelése
description: Ez a cikk végigvezeti a figyelési metrikákat engedményt-alapú Azure Automation-runbookok
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 65de18445f114f468dd42c5a7e7128dd2f63d44c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959826"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Figyelési forgatókönyvek az metrikákhoz kapcsolódó riasztások

Ebből a cikkből megismerheti, hogyan hozhat létre riasztásokat a runbookok befejezési állapota alapján.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen.

## <a name="create-alert"></a>Riasztás létrehozása

Riasztások lehetővé teszik olyan feltétellel, hogy figyelje a és a egy adott feltétel teljesülése esetén végrehajtandó műveletet.

Az Azure Portalon lépjen az Automation-fiók. A **figyelés**válassza **riasztások** kattintson **+ Új riasztási szabály**. A cél a hatókör már definiálva van az Automation-fiókhoz.

### <a name="configure-alert-criteria"></a>Riasztási feltételek konfigurálása

1. Kattintson a **+ feltétel hozzáadása**. Válassza ki **metrikák** a a **jel típusa**, és válassza ki **feladatok száma összesen** a táblából.

2. A **jellogika konfigurálása** lap, ahol megadhatja a logikát, amely aktiválja a riasztást. A korábbi grafikon, lehetősége lesz két dimenzió alatt **Runbook neve** és **állapot**. Dimenzió egy metrikát, szűrheti az eredményeket használható különböző tulajdonságai áll. A **Runbook neve**, válassza ki a riasztás, vagy hagyja üresen a riasztás a runbookok összes runbookot. A **állapot**, a figyelni kívánt legördülő listából válassza ki az állapotot. A runbook nevét és állapotát a legördülő listában megjelenő értékei csak a múlt héten futtatott feladatokhoz.

   Ha szeretne riasztást az állapot vagy a runbookot, amely nem jelenik meg a legördülő listában, kattintson a **\+** mellett a dimenzió. Ezzel megnyílik egy párbeszédpanel, amely lehetővé teszi, hogy az egyéni érték, amely nem ehhez a dimenzióhoz nemrégiben kibocsátott adja meg. Ha megad egy értéket, amely a riasztás nem aktiválódik, a tulajdonság nem létezik.

3. A **riasztási logika**, meghatározott feltétel és a riasztás küszöbértéke. A megadott feltétel előnézete alatt jelenik meg.

4. A **alapján Evaluated**, a lekérdezés az időszak kiválasztása, és milyen gyakran szeretné, hogy a lekérdezést futtatta. Például, ha úgy dönt, **az utolsó 5 percben** a **időszak** és **át 1 perces** a **gyakorisága**, a riasztás néz ki, maximális száma a runbookok az elmúlt 5 percben, amely teljesíti a feltételeket. Ez a lekérdezés percenként fut, és a egy 5 perces időszak már nem található a meghatározott riasztási kritériumok, ha a riasztás feloldása magát. Ha végzett, kattintson a **Kész** gombra.

   ![Válasszon ki egy erőforrást a riasztás](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Riasztás részleteinek megadása

1. A **2. Riasztás részleteinek megadása** szakaszban adja meg a riasztás rövid nevét és leírását. Állítsa be a **súlyossági** megfelelően a riasztási feltételt. Nincsenek öt súlyossági szint esetén csak 0 és 5 közötti. A riasztások kezeli az a súlyossági azonos független, egyeztetéséhez az üzleti logikára megfelelő súlyossági.

1. A szakasz alján van egy gombot, amely lehetővé teszi, hogy a telepítést a szabály engedélyezése. Alapértelmezés szerint a szabályok engedélyezve van létrehozásakor. Ha nem lehetőséget választja, a riasztást hozhat létre, és akkor jön létre egy **letiltott** állapota. Az a **szabályok** oldal az Azure monitorban kiválasztja, és kattintson a **engedélyezése** engedélyezéséhez a riasztás, ha készen áll.

### <a name="define-the-action-to-take"></a>Adja meg az elvégzendő műveletet

1. A **3. Műveleti csoport megadása** szakaszban kattintson az **+ Új műveletcsoport** gombra. Műveletcsoport olyan műveletek, amelyek között több riasztás is használhatja. Ezek tartalmazhatnak, de nem kizárólagosan, e-mail-értesítések, runbookok, webhookok és sok más. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../monitoring-and-diagnostics/monitoring-action-groups.md) szóló cikkben talál.

1. A **Műveletcsoport neve** mezőben adjon meg egy felhasználóbarát és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

1. Az a **műveletek** szakaszba **MŰVELETTÍPUS**válassza **e-mailek és SMS és leküldéses/Hangvétel**.

1. Az **E-mail/SMS/Leküldés/Hang** oldalon adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adja meg a használni kívánt érvényes e-mail-címet.

   ![E-mail konfigurálása műveletcsoport](./media/automation-alert-activity-log/add-action-group.png)

1. Kattintson az **OK** gombra az **E-mail/SMS/Leküldés/Hang** oldalon az oldal, majd az **OK** gombra a **Műveletcsoport hozzáadása** oldal bezárásához. Ezen a lapon megadott név a program menti a **MŰVELETNÉV**.

1. Amikor végzett, kattintson a **Mentés** gombra. Ez a művelet létrehozza a szabály, amely riasztást küld, amikor egy runbook egyes állapottal fejeződött be.

> [!NOTE]
> Való hozzáadásakor egy e-mail-cím műveletcsoportot, egy értesítés e-mailt küld arról, hogy a cím műveletcsoport bővült.

## <a name="notification"></a>Értesítés

Amikor a riasztási feltétel teljesül, a műveletcsoport fut definiált műveletet. Ez a cikk a példában egy e-mail lesz elküldve. Az alábbi képen egy példa egy e-mailt kap, miután a riasztás akkor aktiválódik,:

![Értesítő e-mail](./media/automation-alert-activity-log/alert-email.png)

Miután a metrika már nem kívül a meghatározott küszöbértéket, a riasztás inaktiválása, és a műveletcsoport fut definiált műveletet. Ha egy e-mail-művelet típusa van kijelölve, a feloldási e-mailt küld arról, hogy megoldódott.

## <a name="next-steps"></a>További lépések

Folytassa a következő cikket, hogy alertings integrálhatja azokat az Automation-fiók egyéb módjaival kapcsolatos.

> [!div class="nextstepaction"]
> [Használjon olyan riasztást, egy Azure Automation-runbook elindítása](automation-create-alert-triggered-runbook.md)
