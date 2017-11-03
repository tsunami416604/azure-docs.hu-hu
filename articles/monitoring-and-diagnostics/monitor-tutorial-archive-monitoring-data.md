---
title: "Azure figyelési adatok archiválása |} Microsoft Docs"
description: "Azure-ban létrehozott archív napló és a metrika adatok tárfiókba."
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: 445901a740920a74f259aaa9c6b862680c1c807e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="archive-azure-monitoring-data"></a>Figyelési adatok archiválása Azure

Az Azure-alapú környezetben több rétegből napló és a metrika értékét, amely egy Azure Storage-fiók archiválható eredményez. Érdemes lehet ehhez figyelési adatok időbeli alacsony költségű, nem kereshető tárolóban adatok Naplóelemzési vagy Azure figyelő a megőrzési időszak letelte után előzményeinek megőrzése érdekében. A folyamatot, amely az Azure-tárfiókba adatok archiválására környezet konfigurálása a Útmutató lépéseit.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Create a storage account

Először meg kell állítania egy tárfiókot, amelyhez a figyelési adatok archiválja a rendszer. Ehhez a [kövesse a lépéseket Itt](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>A tárolási fiók útvonal előfizetési naplók

Most már készen áll a figyelési adatok tárfiókba irányításához az Azure környezetben beállítása megkezdéséhez. (Az Azure tevékenységnapló szereplő) előfizetés-szintű adatokat először a tárolási fiók átirányítás konfiguráljuk. A [ **Azure tevékenységnapló** ](monitoring-overview-activity-logs.md) Azure előfizetés-szintű események előzményeit biztosítja. Tallózással is kikeresheti azt határozza meg az Azure portálon található *ki* létrehozott, frissített vagy törölt *mi* erőforrások és *amikor* tevékenységük azt.

1. Kattintson a **figyelő** gomb megtalálható a bal oldali navigációs listára, majd **tevékenységnapló**.

   ![Tevékenység napló szakasz](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. A megjelenített tevékenységnapló szakaszban kattintson a a **exportálása** gombra.

3. Az a **exportálási tevékenységnapló** szakaszt, amely akkor jelenik meg, jelölje be a **tárfiókba exportálása** kattintson **válasszon egy tárfiókot.**

   ![A Tevékenységnaplói exportálás](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. A területen megjelenő használja a **tárfiók** legördülő menüből válassza ki a nevét, az előző létrehozott tárfiók **hozzon létre egy tárfiókot** . lépés:, majd kattintson az **OK**.

   ![A storage-fiók kiválasztása](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Állítsa be a **megőrzés (nap)** 30 csúszkát. A csúszka a tárfiókban lévő figyelési adatok megőrzéséhez a napok számának beállítása. A figyelő az Azure automatikusan törli a számú napnál régebbi adatok megadott. Egy nulla napos megőrzési határozatlan ideig tárolja az adatokat.

6. Kattintson a **mentése** és zárja be az ebben a szakaszban.

A tárfiókhoz be most áramló a figyelési adatok az előfizetésből.

## <a name="route-resource-data-to-the-storage-account"></a>A tárolási fiók útvonal erőforrásadatok

Most azt konfigurálja, erőforrás-szintű adatokat (erőforrás metrikáit és a diagnosztikai naplók) a tárolási fiók beállításával átirányítás **erőforrás diagnosztikai beállításainak**.

1. Kattintson a **figyelő** gomb megtalálható a bal oldali navigációs listára, majd **diagnosztikai beállítások**. Itt láthatja az összes erőforrást az előfizetésében keresztül Azure figyelő figyelési adatokat előállító listáját. Ha nem rendelkezik olyan erőforrásokkal ebben a listában, akkor [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md) erőforrás diagnosztikai beállításának konfigurálható, hogy a folytatás előtt.

2. Kattintson a listában egy erőforráson, majd **a diagnosztika bekapcsolásához**.
   
   ![Diagnosztika engedélyezése](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Ha már létezik egy beállítást, inkább látható, a meglévő beállítások, és egy gombra kattintva **diagnosztikai beállítás hozzáadása**. A gombra kattintva.

   Egy erőforrás diagnosztikai beállításának definíciójának *mi* figyelési adatok irányítani kell egy adott erőforráshoz és *ahol* , hogy el kell-e a figyelési adatok.

3. A szakaszban, amely akkor jelenik meg, adjon meg a beállítás egy **neve** , és jelölje be a **tárfiókba archív**.

   ![Diagnosztikai beállítások szakasz](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Kattintson a a **konfigurálása** gombra kattint, a **tárfiókba archív** , és válassza ki az előző szakaszban létrehozott tárfiókot. Kattintson az **OK** gombra.

   ![Diagnosztikai beállítások storage-fiók](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Ellenőrizze az összes be **napló** és **metrika**. Attól függően, hogy az erőforrás típusa csak akkor lehet szükség ezen beállítások valamelyikét. E jelölőnégyzetek szabályozza, mely kategóriák napló és a metrika adatok elérhető erőforrástípus küldött a célra kijelölt, ebben az esetben egy tárfiókot.

   ![Diagnosztikai beállítások kategóriák](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)
   
6. Állítsa be a **megőrzés (nap)** 30 csúszkát. A csúszka a tárfiókban lévő figyelési adatok megőrzéséhez a napok számának beállítása. A figyelő az Azure automatikusan törli a számú napnál régebbi adatok megadott. Egy nulla napos megőrzési határozatlan ideig tárolja az adatokat.

7. Kattintson a **Save** (Mentés) gombra.

Az erőforrás adatait figyelési van most összekapcsolását a tárfiók.

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>A tárolási fiók útvonal (vendég operációs rendszer) virtuális gép adatai

1. Ha még nem rendelkezik a virtuális gépek az előfizetéshez [hozzon létre egy virtuális gépet](../virtual-machines/windows/quick-create-portal.md).

2. A portál bal oldali navigációs listájában kattintson a **virtuális gépek**.

3. A virtuális gépek akkor jelenik meg, kattintson a létrehozott virtuális gépen.

4. A megjelenő szakaszban kattintson a **diagnosztikai beállítások** a bal oldali navigációs. Ez a szakasz lehetővé teszi állíthat be a out-of-box monitorozási bővítményt az Azure-figyelő a virtuális gép és a Windows vagy Linux tárfiók mutatni útvonal adatai.

   ![Navigáljon a diagnosztikai beállítások](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. Kattintson a **vendégszintű a figyelés bekapcsolható** megjelenő szakaszában.

   ![Diagnosztikai beállítások engedélyezése](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. A diagnosztikai beállításának megfelelően mentette, miután a **áttekintése** fülre az összegyűjtött adatokat listáját tartalmazza, és folyamatban helyén. Kattintson a **teljesítményszámlálók** szakasz nézze át a Windows-teljesítmény teljesítményszámlálók gyűjtése.

   ![Teljesítményszámlálók beállításai](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)
   
7. Kattintson a a **naplók** fülre és ellenőrizze a tartozó jelölőnégyzeteket **információk** szint alkalmazás bejelentkezik, és a rendszer naplóz.

   ![Naplók beállításai](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Kattintson a a **ügynök** lapon és a **tárfiók** kattintson a látható a tárfiók nevére.

   ![A tárfiók módosítása](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. A megjelenő szakaszban válassza ki az előző létrehozott tárfiók **hozzon létre egy tárfiókot** lépés.

10. Kattintson a **Save** (Mentés) gombra.

A virtuális gépek adatainak figyelés van most összekapcsolását a tárfiók.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>A tárfiók a figyelési adatok megtekintése

Ha követte a fenti lépéseket, adatokat halad a tárfiók már megkezdődött.

1. Az egyes adattípusok, például a tevékenységnapló szükség van néhány tevékenység, amely a tárfiókban lévő generál egy eseményt. A műveletnapló tevékenység létrehozásához hajtsa végre a [ezeket az utasításokat](./monitor-quick-audit-notify-action-in-subscription.md). Várja meg legfeljebb öt percet az esemény jelenik meg a tárfiók szeretne.

2. A portálon lépjen a **Tárfiókok** szakaszban keresse meg azt a bal oldali navigációs sávon.

3. Azonosítsa az előző szakaszban létrehozott tárfiók, és kattintson rá.

4. Kattintson a **Blobok**, majd a tárolóra, címkével **insights műveleti naplókat** és végül a tároló címkéjű **neve = alapértelmezett**. Ez az a tárolóhoz, amelybe a tevékenység naplója van benne. Figyelési adatok vannak osztva tárolók erőforrás-azonosítója (csak az előfizetési Azonosítóját a műveletnapló), majd dátum és idő. A blobok teljes formátuma:

   insights-működési-naplók/name = alapértelmezett/resourceId = / SUBSCRIPTIONS / {előfizetés-azonosító} / y = {négyjegyű numerikus year} / m = {kétjegyű numerikus month} / d {kétjegyű számozott napja} = / h = {kétjegyű 24 órás hour}/m=00/PT1H.json

5. Keresse meg az erőforrás-azonosító, dátum és idő a tárolók kattintson a PT1H.json fájlt. Kattintson a PT1H.json fájlra, és kattintson a **letöltése**. Minden egyes PT1H.json blob tartalmaz a blob URL-címben megadott órán belül előforduló eseményeket a JSON blob (például h = 12). A jelenlegi órán belül események lesz hozzáfűzve a PT1H.json fájl azok bekövetkezésekor. A perc értéket (m = 00) mindig 00, mivel alkalmazásnapló-események az egyes blobok óránként van felosztva.

   Most már megtekintheti a tárfiókban tárolt JSON-esemény. Az erőforrás diagnosztikai naplók a BLOB formátuma:

   insights - logs-{napló kategória neve} / resourceId = / {erőforrás-azonosító} / y = {négyjegyű numerikus year} / m = {kétjegyű numerikus month} / d {kétjegyű számozott napja} = / h = {kétjegyű 24 órás hour}/m=00/PT1H.json

6. Vendég operációs rendszer figyelési adatok táblák tárolja. Lépjen vissza az otthoni tárfiók, és kattintson a **táblák**. Nincsenek metrikákat, a teljesítményszámlálók és az eseménynaplók táblák.

Most már sikeresen állított be figyelési adatok tárfiókba archiválását.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Lépjen vissza a **tevékenységnapló exportálása** át az előző szakaszban **előfizetési naplók átirányíthatja a tárfiók** lépést, és kattintson a **alaphelyzetbe**.

2. Keresse meg a **diagnosztikai beállítások** területen kattintson az erőforrás-készítési diagnosztikai beállításának az előző **útvonal-erőforrás adatait a tárolási fiók** . lépés:, majd a beállítás található meg létrehozott, kattintson a **beállításainak módosítása** gombra, majd kattintson a **törlése**.

3. Keresse meg a **diagnosztikai beállítások** a virtuális gépen, amelyet az előző szakaszban **virtuális gép (vendég operációs rendszer) adatok továbbításához a tárolási fiók** lépés, és a  **Ügynök** lapon kattintson **eltávolítása** (alatt a **távolítsa el az Azure diagnosztikai ügynök** szakaszban).

4. Lépjen a tárfiókhoz, az előző létrehozott **hozzon létre egy tárfiókot** . lépés:, és kattintson a **törölni a tárfiókot**. Írja be a tárfiók nevét, és kattintson **törlése**.

5. Ha létrehozott egy virtuális gép vagy a logikai alkalmazást az előző lépéseket, törölni is azokat.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtudta, hogyan figyelése az Azure környezetben (előfizetés, erőforrás és a vendég operációs rendszer) archiválását, a tárolási fiók adatait. Több jelentéssel kívül az adatok és elemzések származnia, próbálkozzon a adatküldés a Log Analyticshez való is.

> [!div class="nextstepaction"]
> [Log Analytics használatába](../log-analytics/log-analytics-get-started.md)
