---
title: Keresési lekérdezések Azure Monitor naplókban | Microsoft Docs
description: Ez a cikk útmutatást nyújt az első lépések végrehajtásához a Azure Monitor log lekérdezésekben.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77660240"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Keresési lekérdezések Azure Monitor naplókban
Azure Monitor a naplózási lekérdezések táblázatos névvel vagy keresési paranccsal kezdődhetnek. Ez az oktatóanyag a keresési alapú lekérdezéseket ismerteti. Az egyes módszerek előnyei vannak.

A tábla alapú lekérdezések a lekérdezés hatókörével kezdődnek, ezért a keresési lekérdezéseknél hatékonyabbak lesznek. A keresési lekérdezések kevésbé strukturáltak, így jobb választást tesznek lehetővé az oszlopok vagy táblák adott értékének keresésekor. a **Keresés** megkeresheti az adott tábla összes oszlopát, illetve az összes táblában a megadott értéket. A feldolgozott adatmennyiség óriási lehet, ezért előfordulhat, hogy ezek a lekérdezések hosszabb időt vehetnek igénybe, és nagy mennyiségű eredményhalmazt adnak vissza.

## <a name="search-a-term"></a>Kifejezés keresése
A **Search** parancs általában egy adott kifejezés keresésére szolgál. A következő példában az összes tábla összes oszlopát a rendszer a "hiba" kifejezésre vizsgálja:

```Kusto
search "error"
| take 100
```

Habár egyszerűen használhatók, a nem hatókörű lekérdezések, mint például a fentiekben láthatók, nem hatékonyak, és sok lényegtelen eredményt adnak vissza. A jobb gyakorlat a megfelelő táblában, vagy akár egy adott oszlopban is megkereshető.

### <a name="table-scoping"></a>Táblázat hatóköre
Egy adott tábla kifejezésének kereséséhez vegye fel a következőt `in (table-name)` közvetlenül a **keresési** operátor után:

```Kusto
search in (Event) "error"
| take 100
```

vagy több táblában:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Táblázat és oszlop hatóköre
Alapértelmezés szerint a **Keresés** az adathalmaz összes oszlopát kiértékeli. Ha csak egy adott oszlopot szeretne keresni ( *forrás* neve az alábbi példában), használja a következő szintaxist:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Ha a `==` helyett `:` a-t használja, az eredmények olyan rekordokat is tartalmaznak, amelyekben a *forrás* oszlop pontos értéke "Error", és ebben a pontos esetben. A ":" használatával olyan rekordokat fog tartalmazni, amelyekben a *forrás* olyan értékekkel rendelkezik, mint például a "hibakód 404" vagy a "hiba".

## <a name="case-sensitivity"></a>Kis-és nagybetűk megkülönböztetése
Alapértelmezés szerint a kifejezéses keresés a kis-és nagybetűk megkülönböztetése, ezért a "DNS" keresése olyan eredményeket eredményezhet, mint például a "DNS", a "DNS" vagy a "DNS". A keresési kis-és nagybetűk megkülönböztetéséhez használja a következő `kind` lehetőséget:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Helyettesítő karakterek használata
A **Search** parancs a Wild kártyákat a kifejezés elején, végén vagy közepén támogatja.

A "win" kezdetű kifejezések keresése:
```Kusto
search in (Event) "win*"
| take 100
```

A ". com" végződésű kifejezések keresése:
```Kusto
search in (Event) "*.com"
| take 100
```

A "www" kifejezést tartalmazó kifejezések keresése:
```Kusto
search in (Event) "*www*"
| take 100
```

A "Corp" kezdetű kifejezésekre és a ". com" végződésre, például "corp.mydomain.com"

```Kusto
search in (Event) "corp*.com"
| take 100
```

Egy táblában is megtudhatja, hogy csak egy Wild kártyát használ: `search in (Event) *` , de az csak az írást fogja használni `Event` .

> [!TIP]
> Habár a segítségével minden `search *` oszlopból lekérheti az összes oszlopot, azt javasoljuk, hogy a lekérdezéseket mindig az adott táblákra szűkítse. A nem hatókörű lekérdezések végrehajtása hosszabb időt is igénybe vehet, és előfordulhat, hogy túl sok eredményt ad vissza.

## <a name="add-and--or-to-search-queries"></a>Lekérdezések hozzáadása *és* / *or* keresése
A **és** a használatával több kifejezést tartalmazó rekordokat kereshet:

```Kusto
search in (Event) "error" and "register"
| take 100
```

A **vagy** a használatával lekérheti a feltételek legalább egyikét tartalmazó rekordokat:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Ha több keresési feltételt is tartalmaz, a zárójelek használatával kombinálhatja őket a lekérdezéssel:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

A példa eredményei olyan rekordokat tartalmaznak, amelyek tartalmazzák a "hiba" kifejezést, és a "regisztráció" vagy a "Marshal" kezdetű bejegyzést is tartalmazzák.

## <a name="pipe-search-queries"></a>Pipe-keresési lekérdezések
Ugyanúgy, mint bármely más parancs, a **Keresés** megadható, így a keresési eredmények szűrhetők, rendezhetők és összevonhatók. Például a "win" kifejezést tartalmazó *Event* Records-rekordok számának lekéréséhez:

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>További lépések

- A [Kusto lekérdezési nyelvi webhelyén](/azure/kusto/query/)további oktatóanyagokat talál.
