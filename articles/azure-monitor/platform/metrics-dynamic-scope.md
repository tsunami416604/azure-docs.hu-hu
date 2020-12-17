---
title: Több erőforrás megtekintése Metrikaböngésző
description: Ismerje meg, hogyan jelenítheti meg több erőforrást Azure Monitor Metrikaböngésző
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 9d1460a8bebe75a3ee471eb304fcf642d566b5dd
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614642"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>Több erőforrás megtekintése Metrikaböngésző

Az erőforrás-hatókör-választó lehetővé teszi, hogy az azonos előfizetésben és régióban található több erőforrás mérőszámait is megtekinthesse. Az alábbiakban a Azure Monitor Metrikaböngésző több erőforrásának megtekintését ismertető cikkben talál útmutatást. 

## <a name="selecting-a-resource"></a>Erőforrás kiválasztása 

Válassza a **metrikák** lehetőséget a **Azure monitor** menüből vagy az erőforrás menüjének **figyelés** szakaszában. Kattintson a "hatókör kiválasztása" gombra az erőforrás hatókör-választójának megnyitásához, amely lehetővé teszi, hogy kiválassza azokat az erőforrásokat, amelyeknek a mérőszámait meg szeretné jeleníteni. Ha az erőforrás menüjéből megnyitotta a metrikák Explorert, ezt már fel kell töltenie. 

![A piros színnel jelölt erőforrás-hatókör választó képernyőképe](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>Több erőforrás kiválasztása 

Egyes erőforrástípusok lehetővé tették a metrikák lekérdezését több erőforráson keresztül, feltéve, hogy ugyanazon az előfizetésen és helyen belül vannak. Ezek az erőforrástípusok az "erőforrástípusok" legördülő lista tetején találhatók. 

![Képernyőkép, amely a többerőforrásos kompatibilitású erőforrások legördülő listáját jeleníti meg ](./media/metrics-charts/020.png)

> [!WARNING] 
> A mérőszámok több erőforrás, erőforráscsoport vagy előfizetés alapján történő megjelenítéséhez az előfizetés szintjén kell figyelnie az olvasói jogosultságot. Ennek elvégzéséhez kövesse a [jelen dokumentum](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)utasításait.

Több erőforrásra vonatkozó mérőszámok megjelenítéséhez először válassza ki az erőforrás hatókör-választóján belül több erőforrást. 

![A több erőforrás kijelölését bemutató képernyőkép](./media/metrics-charts/021.png)

> [!NOTE]
> Csak több erőforrást lehet kijelölni ugyanazon az erőforrás-típuson, helyen és előfizetésen belül. A feltételen kívüli erőforrások nem választhatók ki. 

Ha végzett a kijelöléssel, kattintson az "alkalmaz" gombra a választás mentéséhez. 

## <a name="selecting-a-resource-group-or-subscription"></a>Erőforráscsoport vagy előfizetés kiválasztása 

> [!WARNING]
> A mérőszámok több erőforrás, erőforráscsoport vagy előfizetés alapján történő megjelenítéséhez az előfizetés szintjén kell figyelnie az olvasói jogosultságot. 

A többerőforrásos kompatibilis típusok esetében lekérdezheti a metrikákat egy előfizetésben vagy több erőforráscsoport között is. Először válasszon ki egy előfizetést, vagy válasszon ki egy vagy több erőforráscsoportot: 

![A több erőforráscsoport közötti lekérdezés módját bemutató képernyőkép ](./media/metrics-charts/022.png)

Ezután ki kell választania egy erőforrástípust és egy helyet az új hatókör alkalmazásának folytatásához. 

![A kiválasztott erőforráscsoportokat bemutató képernyőkép ](./media/metrics-charts/023.png)

A kijelölt hatókörök kibontásával is ellenőrizheti, hogy mely erőforrások lesznek érvényesek a alkalmazásra.

![A csoportok kiválasztott erőforrásait megjelenítő képernyőkép ](./media/metrics-charts/024.png)

Ha befejezte a hatókörök kiválasztását, kattintson az "alkalmaz" gombra a beállítások mentéséhez. 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>Felosztás és szűrés erőforráscsoport vagy erőforrások alapján

Az erőforrások ábrázolása után az elosztás és szűrés eszközzel további információkhoz juthat az adataihoz. 

A felosztás lehetővé teszi, hogy megjelenítse, hogy a metrika különböző szakaszai hogyan hasonlíthatók össze egymással. Ha például több erőforráshoz szeretne mérőszámot ábrázolni, használhatja az "elosztás alkalmazása" eszközt az erőforrás-azonosító vagy az erőforráscsoport felosztásához. Ez lehetővé teszi, hogy egyszerűen hasonlítson össze egy mérőszámot több erőforrás vagy erőforráscsoport között.  

Például az alábbi táblázat a százalékos CPU-t mutatja a 9VMs között. Az erőforrás-azonosító alapján történő felosztással könnyedén megtekintheti, hogy a CPU hány százalékban különbözik a virtuális gépektől. 

![Képernyőkép, amely bemutatja, hogyan használhatja a felosztást a PROCESSZORok százalékos arányának megjelenítéséhez virtuális gépenként](./media/metrics-charts/026.png)

A felosztás mellett a szűrés funkció használatával csak a megtekinteni kívánt erőforráscsoportok jeleníthetők meg.  Ha például meg szeretné tekinteni a virtuális gépek százalékos CPU-értékét egy adott erőforráscsoport esetében, a "szűrő hozzáadása" eszközzel szűrheti az erőforráscsoportot. Ebben a példában a TailspinToysDemo alapján szűrünk, amely eltávolítja az erőforrásokhoz társított metrikákat a keljfeljancsi-ben. 

![Az erőforráscsoport szerinti szűrést bemutató képernyőkép](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>Több erőforrást tartalmazó diagramok rögzítése 

> [!WARNING] 
> A mérőszámok több erőforrás, erőforráscsoport vagy előfizetés alapján történő megjelenítéséhez az előfizetés szintjén kell figyelnie az olvasói jogosultságot. Ennek elvégzéséhez kövesse a [jelen dokumentum](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)utasításait. 

A multi-Resource diagram rögzítéséhez kövesse az [itt](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#pin-charts-to-dashboards)található utasításokat. 

## <a name="next-steps"></a>További lépések

* [A Metrikaböngésző hibaelhárítása](metrics-troubleshoot.md)
* [Az Azure-szolgáltatásokhoz elérhető metrikák listájának megtekintése](metrics-supported.md)
* [A konfigurált diagramok példáinak megtekintése](metric-chart-samples.md)

