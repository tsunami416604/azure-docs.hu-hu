---
title: Lekérdezési teljesítmény insight - Azure-adatbázis a MySQL-hez
description: Ez a cikk a Query Performance Insight szolgáltatást ismerteti a MySQL Azure Database szolgáltatásában
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a924f51fac6d43ae4a4530ac4f61f2e8b1f4e8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537057"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Lekérdezési teljesítmény elemzése az Azure Database for MySQL-ben

**A következőkre vonatkozik:** Azure-adatbázis a MySQL 5.7-hez

A Lekérdezési teljesítmény betekintés segítségével gyorsan azonosíthatja a leghosszabb ideig futó lekérdezéseket, hogyan változnak az idő múlásával, és milyen várakozások befolyásolják őket.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

### <a name="long-running-queries"></a>Hosszú ideig futó lekérdezések

- Az elmúlt X óra leghosszabb ideig futó lekérdezéseinek azonosítása
- Az erőforrásokra várakozó legfontosabb N-lekérdezések azonosítása
 
### <a name="wait-statistics"></a>Várakozási statisztika

- A lekérdezés várakozási jellegének ismertetése
- Az erőforrás-várakozások trendjeinek és az erőforrás-versengés helyének ismertetése

## <a name="permissions"></a>Engedélyek

A Lekérdezési terheléselemző lekérdezéseinek szövegét **Tulajdonos** vagy **Közreműködő** jogosultsággal lehet megjeleníteni. **Olvasó** jogosultsággal a diagramok és táblázatok megtekinthetők, de a lekérdezés szövege nem.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Lekérdezési teljesítmény insight működjön, az adatoknak létezniük kell a [Lekérdezéstárolóban.](concepts-query-store.md)

## <a name="viewing-performance-insights"></a>Teljesítményelemzésmegtekintése

A Microsoft Azure Portal [Lekérdezési terheléselemző](concepts-query-performance-insight.md) nézete a Lekérdezéstárból származó fontos információk vizualizációit jeleníti meg.

Az Azure Database for MySQL-kiszolgáló portállapján válassza a **Lekérdezési teljesítmény betekintés** lehetőséget a menüsor **Intelligens teljesítmény** szakaszában.

### <a name="long-running-queries"></a>Hosszú ideig futó lekérdezések

A **Hosszú futó lekérdezések** lap az 5 legfontosabb lekérdezést jeleníti meg a végrehajtásonkénti átlagos időtartam szerint, 15 perces időközönként összesítve. További lekérdezéseket a **Lekérdezések száma** legördülő lista kiválasztásával tekinthet meg. A diagram színei ekkor egy adott lekérdezésazonosító tekintetében változhatnak.

Kattintással és húzással egy adott időablakra szűkítheti a diagramot. Másik lehetőségként használja a nagyítási és kicsinyítési ikonokat egy kisebb vagy nagyobb időszak megtekintéséhez.

![Lekérdezési teljesítmény insight hosszú futó lekérdezések](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Várakozási statisztika

> [!NOTE]
> A várakozási statisztikák a lekérdezési teljesítményproblémák elhárítására vannak szánva. Javasoljuk, hogy csak hibaelhárítási célból kapcsolják be. <br>Ha megjelenik a hibaüzenet az Azure Portalon "*A "Microsoft.DBforMySQL" esetén észlelt probléma; nem tudja teljesíteni a kérést. Ha a probléma továbbra is fennáll vagy váratlan, kérjük, forduljon az ügyfélszolgálathoz ezzel az információval kapcsolatban."* várakozási statisztikák megtekintése közben használjon kisebb időtartamot.

A Várakozási statisztika egy adott lekérdezés végrehajtása során előforduló várakozási eseményeket tartalmazza. További információ a várakozási esemény típusairól a [MySQL motor dokumentációjában.](https://go.microsoft.com/fwlink/?linkid=2098206)

A kiszolgálóval kapcsolatos megfelelő várakozási adatok vizualizációinak megjelenítéséhez válassza a **Várakozási statisztikák** fület.

A várakozási statisztika nézetben megjelenített lekérdezések azon lekérdezések szerint vannak csoportosítva, amelyek a megadott időintervallumban a legnagyobb várakozási időt mutatják.

![A Lekérdezési teljesítmény betekintése statisztikát vár](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>További lépések

- További információ az Azure Database for MySQL [figyeléséről és hangolásáról.](concepts-monitoring.md)