---
title: Azure-metrikák és naplóadatok archiválása az Azure Storage használatával
description: Az Azure-ban készült napló- és metrikaadatokat tárfiókba archiválhatja.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.component: metrics
ms.openlocfilehash: eb99718fb2b39ded01abb075990fccb25a482cab
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386129"
---
# <a name="archive-azure-metric-and-log-data-using-azure-storage"></a>Azure-metrikák és naplóadatok archiválása az Azure Storage használatával

Az Azure-környezet több rétege készít napló- és metrikaadatokat, amelyek Azure Storage-fiókban archiválhatók. Ezt azért érdemes elvégezni, hogy megőrizze a monitorozási adatok előzményeit egy kedvező árú, nem kereshető tárolóban, miután lejárt az adatok megőrzési ideje. 

- Az Azure Monitor platformra vonatkozó metrikák 93 napig érhetők el. 
- Az erőforrásokra vonatkozó diagnosztikai naplók csak akkor jelennek meg, ha a Log Analyticsbe vannak irányítva, ahol legalább 30 napos megőrzési időtartam van hozzájuk beállítva. 
- A tevékenységnapló-bejegyzések 90 napig érhetők el.  

Ez az oktatóanyag végigvezeti azon a folyamaton, amely során az Azure-környezetet az adatok tárfiókban való archiválásához konfigurálhatja.

> [!div class="checklist"]
> * Tárfiók létrehozása monitorozási adatok tárolásához
> * Előfizetési naplók átirányítása a tárfiókba
> * Erőforrásadatok átirányítása a tárfiókba
> * Virtuális gép (vendég operációs rendszer) adatainak átirányítása a tárfiókba
> * A tárfiókban lévő monitorozási adatok megtekintése
> * Az erőforrások törlése

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Először be kell állítania egy tárfiókot, amelybe a monitorozási adatokat archiválja. Ehhez [kövesse ezeket a lépéseket](../storage/common/storage-quickstart-create-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Előfizetési naplók átirányítása a tárfiókba

Most már megkezdheti az Azure-környezet beállítását a monitorozási adatok a tárfiókba való irányításához. Először beállítjuk, hogy az előfizetés-szintű adatok (amelyek az Azure-tevékenységnaplóban szerepelnek) a tárfiókba legyenek irányítva. Az [**Azure-tevékenységnapló**](monitoring-overview-activity-logs.md) az Azure előfizetés-szintű eseményeit tartalmazza. Az Azure Portalon ezt böngészve megállapíthatja, hogy *ki* és *milyen* erőforrásokat hozott létre, frissített vagy törölt, és a műveletekre *mikor* került sor.

1. Kattintson a bal oldali navigációs listán található **Figyelés** gombra, majd a **Tevékenységnapló** gombra.

   ![Tevékenységnapló szakasz](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. A megjelenő Tevékenységnapló szakaszban kattintson az **Exportálás** gombra.

3. A megjelenő **Tevékenységnapló exportálása** szakaszban jelölje be az **Exportálás tárfiókba** beállítást, és kattintson a **Válasszon egy tárfiókot** lehetőségre.

   ![Tevékenységnapló exportálása](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. A megjelenő szakasz **Tárfiók** legördülő listájában válassza ki az előző, **Tárfiók létrehozása** című lépésben létrehozott tárfiókot, majd kattintson az **OK** gombra.

   ![Tárfiók kiválasztása](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Állítsa a **Megőrzés (nap)** csúszkát a 30 értékre. Ez a csúszka beállítja a monitorozási adatok tárfiókban való megőrzésének időtartamát napokban. Az Azure Monitor automatikusan törli a megadott számú napnál régebbi adatokat. A nulla értékű megőrzési időszak határozatlan ideig tárolja az adatokat.

6. Kattintson a **Mentés** gombra, majd zárja be ezt a szakaszt.

Az előfizetés monitorozási adatai most a tárfiókba kerülnek.

## <a name="route-resource-data-to-the-storage-account"></a>Erőforrásadatok átirányítása a tárfiókba

Most úgy konfiguráljuk az erőforrásszintű adatokat (erőforrásmetrikákat és diagnosztikai naplókat), hogy a tárfiókba legyenek átirányítva. Ehhez beállítjuk az **erőforrás diagnosztikai beállításait**.

1. Kattintson a bal oldali navigációs listán található **Figyelés** gombra, majd a **Diagnosztikai beállítások** gombra. Itt láthatja az előfizetésben lévő összes olyan erőforrást, amely monitorozási adatokat készít az Azure Monitoron keresztül. Ha nincsenek erőforrások a listában, a továbblépés előtt [létrehozhat egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md), hogy rendelkezzen egy olyan erőforrással, amelyhez diagnosztikai beállítást konfigurálhat.

2. Kattintson egy erőforrásra a listában, majd kattintson a **Diagnosztika bekapcsolása** elemre.

   ![Diagnosztika bekapcsolása](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Ha már van konfigurált beállítás, ehelyett a meglévő beállításokat láthatja, valamint egy **Diagnosztikai beállítás hozzáadása** feliratú gombot is. Kattintson erre a gombra.

   Az erőforrások diagnosztikai beállítása annak definíciója, hogy *milyen* monitorozási adatokat kell átirányítani egy adott erőforrásból, és *hová* kell kerülniük ezeknek a monitorozási adatoknak.

3. A megjelenő szakaszban adjon egy **nevet** a beállításnak, és jelölje be az **Archiválás tárfiókba** jelölőnégyzetet.

   ![Diagnosztikai beállítások szakasz](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Kattintson az **Archiválás tárfiókba** szakasz alatt lévő **Konfigurálás** gombra, és válassza ki az előző szakaszban létrehozott tárfiókot. Kattintson az **OK** gombra.

   ![Diagnosztikai beállítások tárfiókhoz](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Jelölje be a **Napló** és a **Metrika** alatti jelölőnégyzeteket. Az erőforrás típusától függően előfordulhat, hogy csak az egyik lehetőség jelenik meg. Ezek a jelölőnégyzetek szabályozzák, hogy a napló- és metrikaadatok milyen, az erőforrástípushoz elérhető kategóriái kerülnek a kiválasztott célra, amely ebben az esetben a tárfiók.

   ![Diagnosztikai beállítások kategóriái](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)

6. Állítsa a **Megőrzés (nap)** csúszkát a 30 értékre. Ez a csúszka beállítja a monitorozási adatok tárfiókban való megőrzésének időtartamát napokban. Az Azure Monitor automatikusan törli a megadott számú napnál régebbi adatokat. A nulla értékű megőrzési időszak határozatlan ideig tárolja az adatokat.

7. Kattintson a **Save** (Mentés) gombra.

Az erőforrás monitorozási adatai mostantól a tárfiókba kerülnek.

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Eseményközpont "Bejövő üzenetek" metrikája fel és ábrázolható egy üzenetsoronként. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Virtuális gép (vendég operációs rendszer) adatainak átirányítása a tárfiókba

1. Ha még nincs virtuális gépe az előfizetésben, [hozzon létre egy virtuális gépet](../virtual-machines/windows/quick-create-portal.md).

2. A portál bal oldali navigációs listájában kattintson a **Virtuális gépek** lehetőségre.

3. A virtuális gépek megjelenő listájában kattintson a létrehozott virtuális gépre.

4. A megjelenő szakaszban kattintson a bal oldali navigációs listában lévő **Diagnosztikai beállítások** elemre. Ebben a szakaszban beállíthatja az Azure Monitor azonnal használható monitorozási bővítményét a virtuális gépen, és egy tárfiókba irányíthatja a Windows vagy Linux által létrehozott adatokat.

   ![Navigálás a diagnosztikai beállításokhoz](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. A megjelenő szakaszban kattintson a **Vendégszintű monitorozás engedélyezése** elemre.

   ![Diagnosztikai beállítások engedélyezése](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. A diagnosztikai beállítás megfelelő mentése után az **Áttekintés** lapon a begyűjtött adatok listája és a tárolási hely látható. Kattintson a **Teljesítményszámlálók** szakaszra a begyűjtött Windows-teljesítményszámlálók halmazának áttekintéséhez.

   ![Teljesítményszámlálók beállításai](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)

7. Kattintson a **Naplók** fülre, és jelölje be az alkalmazás- és rendszernaplókban lévő **Tájékoztatás** szintű naplók jelölőnégyzeteit.

   ![Naplók beállításai](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Kattintson az **Ügynök** lapra, majd a **Tárfiók** területen kattintson a megjelenő tárfiók nevére.

   ![Tárfiók frissítése](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. A megjelenő szakaszban válassza ki az előző, **Tárfiók létrehozása** című lépésben létrehozott tárfiókot.

10. Kattintson a **Save** (Mentés) gombra.

Az virtuális gépek monitorozási adatai mostantól a tárfiókba kerülnek.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>A tárfiókban lévő monitorozási adatok megtekintése

> [!WARNING]
> A tárfiókban lévő naplóadatok formátuma 2018. nov. 1-től JSON Lines lesz. [Ebben a cikkben olvashat ennek hatásairól, valamint arról, hogy hogyan frissítheti eszközeit az új formátum kezeléséhez.](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

Ha követte az előző lépéseket, az adatok elkezdtek a tárfiókba érkezni.

1. Néhány adattípus, például a tevékenységnapló esetében szükség van egy olyan tevékenységre, amely eseményt hoz létre a tárfiókban. Tevékenység létrehozásához tevékenységnaplóban kövesse [ezeket az utasításokat](./../azure-monitor/platform/quick-audit-notify-action-subscription.md). Előfordulhat, hogy akár öt percet is várnia kell, mielőtt az esemény megjelenik a tárfiókban.

2. A portálban navigáljon a bal oldali navigációs sávon található **Tárfiókok** szakaszhoz.

3. Azonosítsa az előző szakaszban létrehozott tárfiókot, és kattintson rá.

4. Kattintson a **Blobok** elemre, majd az **insights-operational-logs** címkéjű tárolóra, és végül a **name=default** címkéjű tárolóra. Ez az a tároló, amelyben a tevékenységnapló található. A monitorozási adatok erőforrás-azonosító (csak a tevékenységnapló előfizetés-azonosítója), majd a dátum és idő alapján vannak tárolókba osztva. A blobok teljes formátuma a következő:

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{előfizetés-azonosító}/y={négy számjegyű numerikus év}/m={két számjegyű numerikus hónap}/d={két számjegyű numerikus nap}/h={két számjegyű óra 24 órás formátumban}/m=00/PT1H.json

5. Az erőforrás-azonosító, dátum és idő tárolóiba kattintva keresse meg a PT1H.json fájlt. Kattintson a PT1H.json fájlra, majd a **Letöltés** gombra. Mindegyik PT1H.json blob tartalmazza a blob URL-jében meghatározott órában (például h=12) bekövetkezett események JSON-blobját. Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéke (m=00) mindig 00, mert a naplóesemények óránként vannak külön blobokba osztva.

   Most megtekintheti a tárfiókban tárolt JSON-eseményt. Erőforrás-diagnosztikai naplók esetében a blobok formátuma a következő:

   insights-logs-{naplókategória neve}/resourceId=/{erőforrás-azonosító}/y={négy számjegyű numerikus év}/m={két számjegyű numerikus hónap}/d={két számjegyű numerikus nap}/h={két számjegyű óra 24 órás formátumban}/m=00/PT1H.json

6. A vendég operációs rendszerek monitorozási adatai táblákban vannak tárolva. lépjen vissza a tárfiók kezdőlapjára, és kattintson a **Táblák** elemre. A metrikákhoz, teljesítményszámlálókhoz és eseménynaplókhoz érhetők el táblák.

Sikeresen beállította, hogy a monitorozási adatok tárfiókba legyenek archiválva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Lépjen vissza a **Tevékenységnapló exportálása** szakaszra az előző, **Előfizetési naplók átirányítása a tárfiókba** nevű lépésből, és kattintson a **Visszaállítás** gombra.

2. Keresse meg a **Diagnosztikai beállítások** szakaszt, kattintson arra az erőforrásra, amelyhez az előző, **Erőforrásadatok átirányítása a tárfiókba** nevű lépésben diagnosztikai beállítást hozott létre, majd keresse meg a létrehozott beállítást, kattintson a **Beállítás szerkesztése** gombra, és végül kattintson a **Törlés** elemre.

3. Keresse meg a **Diagnosztikai beállítások** szakaszt az előző, **Virtuális gép (vendég operációs rendszer) adatainak átirányítása a tárfiókba** nevű lépésben konfigurált virtuális gépen, majd az **Ügynök** lapon kattintson az **Eltávolítás** elemre (az **Azure Diagnostics-ügynök eltávolítása** szakasz alatt).

4. Keresse meg az előző, **Tárfiók létrehozása** nevű lépésben létrehozott tárfiókot, és kattintson a **Tárfiók törlése** elemre. Adja meg a fiók nevét, majd kattintson a **Törlés** gombra.

5. Ha az előző lépésekhez virtuális gépet vagy logikai alkalmazást is létrehozott, törölje ezeket is.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte, hogyan állíthatja be az Azure-környezetből (előfizetésből, erőforrásból és vendég operációs rendszerből) származó monitorozási adatok tárfiókba történő archiválását.


> [!div class="checklist"]
> * Tárfiók létrehozása monitorozási adatok tárolásához
> * Előfizetési naplók átirányítása a tárfiókba
> * Erőforrásadatok átirányítása a tárfiókba
> * Virtuális gép (vendég operációs rendszer) adatainak átirányítása a tárfiókba
> * A tárfiókban lévő monitorozási adatok megtekintése
> * Az erőforrások törlése

Ha többet szeretne kihozni az adatokból és további betekintést szeretne nyerni, a Log Analyticsnek is küldje el az adatokat.

> [!div class="nextstepaction"]
> [Ismerkedés a Log Analytics szolgáltatással](../azure-monitor/log-query/log-query-overview.md)
