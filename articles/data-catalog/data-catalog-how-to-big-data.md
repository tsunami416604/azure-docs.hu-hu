---
title: Big data katalogizálása az Azure Data Catalogban
description: Útmutató cikk, amely kiemeli az Azure Data Catalog "big data" adatforrásokkal való használatának mintáit, beleértve az Azure Blob Storage-t, az Azure Data Lake-et és a Hadoop HDFS-t.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 88dc85003fa2a3e41d8a31055ff8ba9b0fcc7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300571"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Big data katalogizálása az Azure Data Catalogban

## <a name="introduction"></a>Bevezetés

**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely a vállalati adatforrások regisztrációs és felderítési rendszereként szolgál. Az egész arról szól, hogy segítsen a embereknek az adatforrások felderítésénél, megértésében és használatában, és segítse a szervezeteket abban, hogy nagyobb értéket kapjanak meglévő adatforrásaikból, beleértve a big data-adatokat is.

**Az Azure Data Catalog** támogatja az Azure Blog Storage blobok és -könyvtárak, valamint a Hadoop HDFS-fájlok és -könyvtárak regisztrációját. Ezeknek az adatforrásoknak a félig strukturált jellege nagy rugalmasságot biztosít. Ahhoz azonban, hogy a legtöbbet hozhassa ki az **Azure Data Catalog**szolgáltatással való regisztrálásból, a felhasználóknak figyelembe kell venniük az adatforrások rendszerezésének módját.

## <a name="directories-as-logical-data-sets"></a>Könyvtárak logikai adatkészletként

A big data-források rendszerezésének gyakori mintája a könyvtárak logikai adatkészletként való kezelése. A legfelső szintű könyvtárak az adatkészletek definiálására szolgálnak, míg az almappák partíciókat határoznak meg, és a benne lévő fájlok magát az adatokat tárolják.

Egy példa erre a mintára:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

Ebben a példában vehicle_maintenance_events és location_tracking_events logikai adatkészleteket jelölnek. Ezek a mappák mindegyike év és hónap szerint almappákba rendezett adatfájlokat tartalmaz. Ezek a mappák több száz vagy több ezer fájlt tartalmazhatnak.

Ebben a mintában az egyes fájlok regisztrálása az **Azure Data Catalog** valószínűleg nincs értelme. Ehelyett regisztrálja azokat a könyvtárakat, amelyek az adatokkal dolgozó felhasználók számára jelentőségteljes adatkészleteket képviselnek.

## <a name="reference-data-files"></a>Referencia adatfájlok

A kiegészítő minta a referenciaadatkészletek egyedi fájlokként való tárolása. Ezek az adatkészletek a big data "kis" oldalának tekinthetők, és gyakran hasonlítanak az analitikus adatmodell dimenzióihoz. A referenciaadat-fájlok olyan rekordokat tartalmaznak, amelyek a big data-tárolóban máshol tárolt adatfájlok nagy részének kontextusát biztosítják.

Egy példa erre a mintára:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Ha egy elemző vagy adatelemző a nagyobb könyvtárstruktúrákban található adatokkal dolgozik, a referenciafájlokban lévő adatok segítségével részletesebb információkat nyújthat olyan entitások számára, amelyekre csak név vagy azonosító szerepel a nagyobb adatkészletben.

Ebben a mintában célszerű regisztrálni az egyes referenciaadat-fájlokat az **Azure Data Catalog**segítségével. Minden fájl egy adatkészletet jelöl, és mindegyik egyenként jegyzetelhető és felderíthető.

## <a name="alternate-patterns"></a>Alternatív minták

Az előző szakaszban leírt minták csak két lehetséges módja a big data-tároló lehet szervezni, de minden egyes megvalósítás eltérő. Függetlenül attól, hogy az adatforrások hogyan épülnek fel, amikor big data sources-t regisztrál az **Azure Data Catalog-ban,** összpontosítson a fájlok és könyvtárak regisztrálására, amelyek a szervezeten belüli mások számára értékes adatkészleteket képviselik. Az összes fájl és könyvtár regisztrálása összezavarhatja a katalógust, ami megnehezíti a felhasználók számára, hogy megtalálják, amire szükségük van.

## <a name="summary"></a>Összefoglalás

Az adatforrások **Regisztrálása** az Azure Data Catalog segítségével megkönnyíti a felderítést és a megértést. A logikai adatkészleteket képviselő big data-fájlok és -könyvtárak regisztrálásával és jegyzetelésével segíthet a felhasználóknak megtalálni és használni a szükséges big data-forrásokat.
