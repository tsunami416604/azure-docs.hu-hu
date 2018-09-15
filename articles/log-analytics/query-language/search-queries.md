---
title: Keresési lekérdezéseket a Log Analytics Rendszereben |} A Microsoft Docs
description: Ez a cikk ismerteti az első lépések oktatóanyag keresési lekérdezések írása a Log Analyticsben.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 2ccef960378190f10e64318f91039871657a1a46
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603753"
---
# <a name="search-queries-in-log-analytics"></a>A Log Analytics keresési lekérdezések

> [!NOTE]
> Hajtsa végre [Ismerkedés a Log Analytics lekérdezéseinek](get-started-queries.md) ebben a leckében befejezése előtt.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Az Azure Log Analytics-lekérdezések egy Táblanév vagy a keresési parancs indítható. Ez az oktatóanyag bemutatja a search-alapú lekérdezések. Nincsenek előnye az, hogy az egyes módszerek.

Tábla-alapú lekérdezések első lépésként hatókörének beállítása a lekérdezést, és ezért általában a hatékonyabb, mint a keresési lekérdezések. Keresési lekérdezések olyan kisebb strukturált, ami lehetővé teszi őket a jobb választás egy adott érték keresése oszlopok vagy táblázatok között. **Keresés** beolvashatja az összes oszlop az adott táblában, vagy az összes tábla megadott értéket. Feldolgozott adatok mennyisége lehet hatalmas, ezért ezeket a lekérdezéseket sikerült hosszabb időt vesz igénybe, ezért előfordulhat, hogy a nagyon nagy eredményhalmazt visszaadása.

## <a name="search-a-term"></a>Kifejezés keresése
A **keresési** parancs általában szolgál adott kifejezés keresése. A következő példában az összes tábla összes oszlopa tartalomvizsgálatnak kifejezés "error":

```KQL
search "error"
| take 100
```

Míg az egyszerűen használható, szerint a fent egy hasonló szűkített lekérdezések nem hatékony és várhatóan sok jelentősége eredményeket adja vissza. Jobb lenne, a kapcsolódó tábla, vagy akár egy adott oszlopban lévő keresése.

### <a name="table-scoping"></a>Tábla hatókörének beállítása
A megadott tábla kifejezés keresése, adjon hozzá `in (table-name)` után csak a **keresési** operátor:

```KQL
search in (Event) "error"
| take 100
```

vagy több tábla:
```KQL
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Tábla és oszlop hatókörének beállítása
Alapértelmezés szerint **keresési** kiértékelik az adatkészlet összes oszlopa. Keresés csak egy adott oszlop, használja ezt a szintaxist:

```KQL
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Ha `==` helyett `:`, az eredmények rekordok tartalmazhat, amelyben a *forrás* oszlopnak a pontos érték "hiba", és pontos ebben az esetben. Használatával ":" nem fogja tartalmazni a rekordok ahol *forrás* értékeket tartalmaznak, például a "404-es hibakód:" vagy "Error".

## <a name="case-sensitivity"></a>Kisbetű/nagybetű megkülönböztetése
Alapértelmezés szerint kifejezés keresése a kis-és nagybetűket, így a "dns" keresése például a "DNS", "dns" vagy "Dns" eredményeket sikerült adnak. Ahhoz, hogy a keresés kis-és nagybetűket, használja a `kind` lehetőséget:

```KQL
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Helyettesítő karakterek használata
A **keresési** parancs támogatja a helyettesítő karakterek használata az elején, teljes vagy közel egy kifejezés.

A keresési feltételek "win" kezdetű:
```KQL
search in (Event) "win*"
| take 100
```

A keresési feltételek, amelyek a ".com" végződik:
```KQL
search in (Event) "*.com"
| take 100
```

Keresés a feltételeket, amelyek tartalmazzák a "www":
```KQL
search in (Event) "*www*"
| take 100
```

A keresési kifejezéseket, amely "corp" karakterlánccal kezdődik és ér véget, a ".com", például a "corp.mydomain.com" "

```KQL
search in (Event) "corp*.com"
| take 100
```

Is kaphat minden egy tábla csak egy helyettesítő karakter használatával: `search in (Event) *`, de ez lehet ugyanaz, mint csak írás `Event`.

> [!TIP]
> Bár használhatja `search *` minden tábla összes oszlopát lekéréséhez ajánlott mindig az adott táblák lekérdezéseket hatókörét. Szűkített lekérdezéseket is igénybe vehet igénybe, és előfordulhat, hogy túl sok eredményeket adja vissza.

## <a name="add-and--or-to-search-queries"></a>Adjon hozzá *és* / *vagy* keresés lekérdezése
Használat **és** rekord, amely több használati kereséséhez:

```KQL
search in (Event) "error" and "register"
| take 100
```

Használat **vagy** beolvasni a rekordokat, amelyek tartalmazzák a feltételek legalább egyikének:

```KQL
search in (Event) "error" or "register"
| take 100
```

Ha több keresési feltétel, hogy kombinálhatja azokat zárójelek használatával ugyanabból a lekérdezés:

```KQL
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Az ebben a példában az eredmények lenne, amely tartalmazza a kisbetűs "error" és "regisztrálása" vagy "átadásra" kezdetű valami is tartalmaz rekordokat.

## <a name="pipe-search-queries"></a>Függőleges vonal keresési lekérdezések
Hasonlóan a többi parancs **keresési** átadható olyan parancsoknak, a keresési eredmények is szűrhetők, rendezve, és összesítve. Például számú *esemény* "win" tartalmazó rekordok:

```KQL
search in (Event) "win"
| count
```




## <a name="next-steps"></a>További lépések

- További oktatóanyagok tekintse meg a [Log Analytics lekérdezési nyelv hely ](https://docs.loganalytics.io)