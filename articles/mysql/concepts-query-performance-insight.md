---
title: Lekérdezési terheléselemző a Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL Lekérdezési terheléselemző szolgáltatását ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 38299a8ffd72722c2d2c1dda9227b7fbf76c0386
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603110"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Lekérdezési terheléselemző a Azure Database for MySQL

**A következőkre vonatkozik:** Azure Database for MySQL 5,7

Lekérdezési terheléselemző segítségével gyorsan azonosíthatja a leghosszabb ideig futó lekérdezéseket, hogyan változnak az idő múlásával, és mi vár rájuk.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

### <a name="long-running-queries"></a>Hosszú ideig futó lekérdezések

- A leghosszabb ideig futó lekérdezések azonosítása az elmúlt X órában
- Az erőforrásokra váró legfontosabb N lekérdezések azonosítása
 
### <a name="wait-statistics"></a>Várakozási statisztika

- A lekérdezés várakozási természetének megértése
- Az erőforrás-várakozások trendjeinek és az erőforrás-tartalom létezésének ismertetése

## <a name="permissions"></a>Engedélyek

A Lekérdezési terheléselemző lekérdezéseinek szövegét **Tulajdonos** vagy **Közreműködő** jogosultsággal lehet megjeleníteni. **Olvasó** jogosultsággal a diagramok és táblázatok megtekinthetők, de a lekérdezés szövege nem.

## <a name="prerequisites"></a>Előfeltételek

A Lekérdezési terheléselemző működéséhez az adatnak léteznie kell a [lekérdezési tárolóban](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>A teljesítmény-áttekintés megtekintése

A Microsoft Azure Portal [Lekérdezési terheléselemző](concepts-query-performance-insight.md) nézete a Lekérdezéstárból származó fontos információk vizualizációit jeleníti meg.

A Azure Database for MySQL-kiszolgáló portál lapján válassza az **lekérdezési terheléselemző** lehetőséget a menüsáv **intelligens teljesítmény** szakaszában.

### <a name="long-running-queries"></a>Hosszú ideig futó lekérdezések

A **hosszú ideig futó lekérdezések** lapon az első 5 lekérdezés az átlagos időtartam szerint, 15 perces időközökben összesítve jelenik meg. A **lekérdezések számából** való kiválasztásával több lekérdezést is megtekintheti. A diagram színei ekkor egy adott lekérdezésazonosító tekintetében változhatnak.

Kattintással és húzással egy adott időablakra szűkítheti a diagramot. Azt is megteheti, hogy a nagyítás és kicsinyítés ikon használatával kisebb vagy nagyobb időszakot jelenít meg.

![Hosszú ideig futó lekérdezések Lekérdezési terheléselemző](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Várakozási statisztika

> [!NOTE]
> A várakozási statisztikák a lekérdezések teljesítményével kapcsolatos problémák elhárításához szükségesek. Javasoljuk, hogy csak hibaelhárítási célokra legyen bekapcsolva. <br>Ha a következő hibaüzenet jelenik meg: "*a Microsoft. DBforMySQL" hibába ütközött "a probléma" Azure Portal. a kérés nem teljesíthető. Ha a probléma továbbra is fennáll, vagy váratlan, forduljon az ügyfélszolgálathoz az információval.* " a várakozási statisztikák megtekintése közben használjon kisebb időtartamot.

A várakozási statisztikák áttekintést nyújtanak az adott lekérdezés végrehajtása során bekövetkezett várakozási eseményekről. További információ a várakozási események típusairól a [MySQL-motor dokumentációjában](https://go.microsoft.com/fwlink/?linkid=2098206).

A kiszolgálóval kapcsolatos megfelelő várakozási adatok vizualizációinak megjelenítéséhez válassza a **Várakozási statisztikák** fület.

A várakozási Statisztika nézetben megjelenő lekérdezések csoportosítása a megadott időintervallumon belül a legnagyobb várakozásokat kiállító lekérdezések szerint történik.

![Lekérdezési terheléselemző vár statisztikái](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>További lépések

- További információ a Azure Database for MySQL [figyeléséről és hangolásáról](concepts-monitoring.md) .