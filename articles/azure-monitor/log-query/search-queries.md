---
title: Keresési lekérdezések az Azure Monitor naplóiban | Microsoft dokumentumok
description: Ez a cikk egy oktatóanyagot tartalmaz az Azure Monitor naplólekérdezései ben végzett keresés használatának megkezdéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660240"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Keresési lekérdezések az Azure Monitor naplóiban
Az Azure Monitor naplólekérdezései egy táblanévvel vagy egy keresési paranccsal kezdődhetnek. Ez az oktatóanyag a keresésalapú lekérdezésekkel foglalkozik. Az egyes módszereknek vannak előnyei.

A táblaalapú lekérdezések a lekérdezés hatókörének hatókörével kezdődnek, ezért általában hatékonyabbak, mint a keresési lekérdezések. A keresési lekérdezések kevésbé strukturáltak, így jobb választás, ha egy adott értéket keresnek oszlopokban vagy táblákban. **a keresés** beszkutathatja egy adott tábla összes oszlopát, vagy az összes táblában a megadott értéket. A feldolgozott adatok mennyisége hatalmas lehet, ezért ezek a lekérdezések hosszabb időt vehet igénybe, és nagyon nagy eredményhalmazokat adhatnak vissza.

## <a name="search-a-term"></a>Kifejezés keresése
A **keresési** parancs általában egy adott kifejezés keresésére szolgál. A következő példában az összes tábla összes oszlopát a rendszer a "hiba" kifejezésre vizsgálja:

```Kusto
search "error"
| take 100
```

Bár könnyen használhatóak, a fentiekhez hasonló, hatókör nélküli lekérdezések nem hatékonyak, és valószínűleg sok irreleváns eredményt adnak vissza. Jobb gyakorlat lenne a megfelelő táblázatban, vagy akár egy adott oszlopban keresni.

### <a name="table-scoping"></a>Tábla hatóköre
Ha egy adott táblában szeretne `in (table-name)` keresni egy kifejezést, közvetlenül a keresési operátor után vegye fel a **hozzá:**

```Kusto
search in (Event) "error"
| take 100
```

vagy több táblában:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Táblázat- és oszlophatókör
Alapértelmezés szerint a **keresés** kiértékeli az adatkészlet összes oszlopát. Ha csak egy adott oszlopban szeretne keresni (az alábbi példában a *Forrás* nevet kapta), használja ezt a szintaxist:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Ha a `==` helyett `:`használja, az eredmények olyan rekordokat tartalmaznak, amelyekben a *Forrás* oszlop pontosértéke "hiba", és ebben a pontos esetben. A ':' használata olyan rekordokat tartalmaz, amelyekben a *Forrás* értékei például "404-es hibakód" vagy "Hiba".

## <a name="case-sensitivity"></a>Kis- és nagybetűk megkülönböztetése
Alapértelmezés szerint a kifejezéskeresés nem megkülönbözteti a kis- és nagybetűket, így a "dns" keresés olyan eredményeket eredményezhet, mint a "DNS", "dns" vagy "Dns". A kis- és nagybetűk `kind` megkülönböztetéséhez használja a következő lehetőséget:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Helyettesítő karakterek használata
A **keresési** parancs támogatja a helyettesítő karaktereket a kifejezés elején, végén vagy közepén.

A "win" kezdetű kifejezések keresése:
```Kusto
search in (Event) "win*"
| take 100
```

A ".com" végződésű kifejezések keresése:
```Kusto
search in (Event) "*.com"
| take 100
```

A "www" szót tartalmazó kifejezések keresése:
```Kusto
search in (Event) "*www*"
| take 100
```

"corp" kezdetű és ".com" végződésű kifejezések keresése, például "corp.mydomain.com""

```Kusto
search in (Event) "corp*.com"
| take 100
```

Azt is kap mindent egy táblázatban segítségével `search in (Event) *`csak egy wild card: , `Event`de ez ugyanaz lenne, mint írásban csak .

> [!TIP]
> Bár minden `search *` táblából minden oszlopot beszerezhet, ajánlott, hogy a lekérdezéseket mindig adott táblákra kell scope.While you can use to get every column from every table, it's recommended to you always scope your queries to specific tables. A nem hatókörített lekérdezések befejezése eltarthat egy ideig, és túl sok eredményt adhat vissza.

## <a name="add-and--or-to-search-queries"></a>Lekérdezések hozzáadása *és* / *keresése*
Több kifejezést tartalmazó rekordokat használhat **és** kereshet:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Olyan rekordokat használhat **vagy** kaphat le, amelyek legalább egy kifejezést tartalmaznak:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Ha több keresési feltétele van, zárójelben egyesítheti őket ugyanabba a lekérdezésbe:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Ebben a példában olyan rekordokat kell felmutatni, amelyek tartalmazzák a "hiba" kifejezést, és vagy "regisztert" vagy valami olyan szót tartalmaznak, amely "átadás-előkészítés" kezdetű.

## <a name="pipe-search-queries"></a>Csőkeresési lekérdezések
Ugyanúgy, mint bármely más parancs, **a keresés** is átirányítható, így a keresési eredmények szűrhetők, rendezhetők és összesíthetők. Például a "win" azonosítót tartalmazó *eseményrekordok* számának lehívásához:

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>További lépések

- További útmutatók a [Kusto lekérdezés nyelvi oldalon](/azure/kusto/query/).
