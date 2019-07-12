---
title: A MySQL-hez készült Azure Database lekérdezési Terheléselemző
description: Ez a cikk ismerteti a lekérdezési Terheléselemző funkció az Azure Database for MySQL-hez
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8f142933ebf955cbe3aa119f42779109fb6ef7db
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67589071"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>A MySQL-hez készült Azure Database lekérdezési Terheléselemző

**A következőkre vonatkozik:**  Azure Database for MySQL 5.7.

> [!NOTE]
> Lekérdezési Terheléselemző az előzetes verzióban.

Lekérdezési Terheléselemző segít gyorsan azonosítani a leghosszabban futó lekérdezések vannak, hogyan változnak idővel és milyen feladatot hatással vannak, azokat.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

### <a name="long-running-queries"></a>Hosszú ideig futó lekérdezések

- Leghosszabb futó lekérdezések azonosítása az elmúlt óra X
- Erőforrások várakozik a legfontosabb N lekérdezések azonosítása
 
### <a name="wait-statistics"></a>Várjon statisztika

- Várjon jellegű ismertetése, lekérdezés
- Az erőforrás várakozik és ott, ahol az erőforrás-versengés trendek ismertetése

## <a name="permissions"></a>Engedélyek

**Tulajdonos** vagy **közreműködői** a lekérdezési Terheléselemző lekérdezések szövege megtekintéséhez szükséges engedélyekkel. ** Olvasó** diagramok és táblázatok megtekintheti, de nem a lekérdezés szövege.

## <a name="prerequisites"></a>Előfeltételek

A lekérdezési Terheléselemző függvénynek, adatok szerepelniük kell a [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Teljesítményelemzés megtekintése

A Microsoft Azure Portal [Lekérdezési terheléselemző](concepts-query-performance-insight.md) nézete a Lekérdezéstárból származó fontos információk vizualizációit jeleníti meg.

Az Azure Database for MySQL-kiszolgáló portáloldalán válassza **lekérdezési Terheléselemző** alatt a **intelligens teljesítmény** a menüsávon szakaszában.

### <a name="long-running-queries"></a>Hosszú ideig futó lekérdezések

A **hosszú ideig futó lekérdezések** lapon látható a 5 leggyakoribb lekérdezések által végrehajtásonkénti, átlagos időtartam összesített 15 perces időközönként. Több lekérdezés megtekintéséhez válassza ki a **száma a lekérdezések** legördülő menü. A diagram színei ekkor egy adott lekérdezésazonosító tekintetében változhatnak.

Kattintással és húzással egy adott időablakra szűkítheti a diagramot. Használhatja a bejövő és kimenő ikonok nagyítás egy kisebb vagy nagyobb időszakban rendre megtekintéséhez.

![Lekérdezési Terheléselemző hosszú ideig futó lekérdezések](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Várjon statisztika

> [!NOTE]
> Várjon statisztika céljára lekérdezési teljesítménnyel kapcsolatos problémák elhárítása. Javasoljuk, hogy csak a hibaelhárításhoz kapcsolható be.

Várjon statisztika a várakozási eseményeket, amelyek egy adott lekérdezés végrehajtása közben nézetét jeleníti meg. További információ a várakozási eseménytípusokat a [MySQL-motor dokumentációja](https://go.microsoft.com/fwlink/?linkid=2098206).

Válassza ki a **várjon statisztika** fülre kattintva megtekintheti a megfelelő Vizualizációk a várakozik a kiszolgálón.

Lekérdezések jelennek meg a várakozási Statisztika nézetben a lekérdezések, amelyeket a legnagyobb vár a megadott időintervallumban szerint vannak csoportosítva.

![Lekérdezési Terheléselemző vár statisztika](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [megfigyelés és finomhangolás](concepts-monitoring.md) az Azure Database for MySQL-hez.