---
title: Azure Data Factory leképezési adatfolyam parancsfájlja
description: A Data Factory adatáramlási parancsfájl-kódjának áttekintése – a nyelv mögött
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/10/2019
ms.openlocfilehash: 4ff5a05fd40ef086c1f2332443ca03d5e872e9a8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010163"
---
# <a name="data-flow-script-dfs"></a>Adatfolyam-parancsfájl (DFS)

Az adatáramlási parancsfájl (DFS) a kódolási nyelvhez hasonló, alapul szolgáló metaadatok, amely a leképezési adatfolyamban található átalakítások végrehajtásához használható. Minden átalakítást olyan tulajdonságok alkotnak, amelyek biztosítják a feladatok megfelelő futtatásához szükséges információkat. A szkript látható és szerkeszthető az ADF-ből a böngésző felhasználói felületének felső menüszalagján a "script" (szkript) gombra kattintva.

![Parancsfájl gomb](media/data-flow/scriptbutton.png "Parancsfájl gomb")

A forrás-átalakítás `allowSchemaDrift: true,` például azt jelzi, hogy a szolgáltatás a forrás adatkészletből származó összes oszlopot tartalmazza, még akkor is, ha azokat nem tartalmazza a séma kivetítése.

## <a name="use-cases"></a>Használati esetek
Az elosztott fájlrendszert automatikusan a felhasználói felület állítja elő. A parancsfájl gombra kattintva megtekintheti és testreszabhatja a parancsfájlt. Parancsfájlokat az ADF felhasználói felületén kívül is létrehozhat, majd átadhatja a PowerShell-parancsmagnak. Az összetett adatfolyamatok hibakeresése során könnyebben áttekintheti a szkript kódját – a folyamatok felhasználói felületi diagramjának megjelenítése helyett.

Íme néhány példa a használati esetekre:
- A programozott módon számos olyan adatfolyamatot hoz létre, amelyek meglehetősen hasonlóak, például "kibélyegzés" típusú adatforgalom.
- A felhasználói felületen nehezen kezelhető összetett kifejezések, amelyek érvényesítési problémákat eredményeznek.
- Hibakeresés és a végrehajtás során visszaadott különféle hibák jobb megértése.

Ha egy PowerShell-lel vagy API-val használandó adatfolyam-szkriptet hoz létre, a formázott szöveget egyetlen sorba kell összecsukni. A lapokat és a sortöréseket Escape-karakterként is megtarthatja. A szöveget azonban úgy kell formázni, hogy az illeszkedjen a JSON-tulajdonsághoz. A parancsfájl-szerkesztő felhasználói felületének alján egy gomb jelenik meg, amely a parancsfájlt egyetlen sorba formázza.

![Másolás gomb](media/data-flow/copybutton.png "Másolás gomb")

## <a name="how-to-add-transforms"></a>Átalakítások hozzáadása
Az átalakítások hozzáadásához három alapvető lépés szükséges: az alapvető átalakítási adatok hozzáadása, a bemeneti adatfolyam átirányítása, majd a kimeneti adatfolyam átirányítása. Ez a legkönnyebb példaként látható.
Tegyük fel, hogy egy egyszerű forrással kezdi el az adatforgalom elfogadását, például az alábbihoz hasonló módon:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Ha úgy döntöttünk, hogy hozzáadunk egy származtatott átalakítást, először létre kell hozni az alaptranszformáció szövegét, amely egy egyszerű kifejezéssel adja hozzá a `upperCaseTitle`nevű új nagybetűs oszlopot:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Ezután megtesszük a meglévő DFS-t, és hozzáadjuk a transzformációt:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

És most átirányítjuk a bejövő adatfolyamot úgy, hogy megismerjük, hogy melyik átalakítást szeretnék elindulni (ebben az esetben `source1`), majd a stream nevét az új átalakításba másolja:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Végül azonosítjuk az új transzformáció után elérhető átalakítást, és lecseréljük a bemeneti streamet (ebben az esetben `sink1`) az új transzformáció kimeneti stream-nevével:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Elosztott fájlrendszer alapjai
Az elosztott fájlrendszer a csatlakoztatott átalakítások sorozatából áll, beleértve a forrásokat, a mosogatókat és számos más, az új oszlopokat, az adatszűrést, az adategyesítést és sok mást. Általában a parancsfájl egy vagy több forrással kezdődik, amelyet számos átalakítás követ, és egy vagy több mosogatóval végződik.

A források mindegyike ugyanazzal az alapszintű szerkezettel rendelkezik:
```
source(
  source properties
) ~> source_name
```

Például egy egyszerű forrás három oszloppal (movieId, title, műfaj) a következő lenne:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

A forrásokon kívül minden átalakításnak ugyanaz az alapszintű szerkezete:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Például egy egyszerű származtatott átalakítás, amely egy oszlopot (címet) vesz fel, és felülírja egy nagybetűs változattal, a következő:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

És egy sémát nem tartalmazó fogadó egyszerűen:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>Következő lépések

Az adatfolyamatok megismerése az [adatfolyamatok áttekintése című cikkben](concepts-data-flow-overview.md) leírtak szerint
