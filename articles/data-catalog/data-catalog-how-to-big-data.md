---
title: Az Azure Data Catalog "big Data típusú adatok" adatforrások használata
description: Útmutató a cikk minták az Azure Data Catalog használatával "big Data típusú adatok" adatforráshoz, beleértve az Azure Blob Storage, Azure Data Lake és a Hadoop HDFS kiemelése.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 42b002e3494d84084979f2f5b27e9679e61cbeb5
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407722"
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Az Azure Data Catalog big data típusú adatforrások használata
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely egy regisztrációs és felderítőrendszert biztosít a vállalati adatforrások. Fontos információt segítséget nyújt az összes felderíteni, és ismertetése és használati adatforrások, segítve a szervezeteket, azok meglévő adatforrások, beleértve a big Data típusú adatok kihasználása.

**Az Azure Data Catalog** támogatja a regisztráció Azure Blog Storage-blobok és a címtárak, valamint a Hadoop a HDFS fájlok és könyvtárak. Ezeknek az adatforrásoknak a szolgáltatásban tárolt részben strukturált jellegét a nagyfokú rugalmasságot biztosít. Azonban a legtöbb értéket beszerezni regisztrálja őket a **Azure Data Catalog**, felhasználók figyelembe kell venni, hogyan vannak rendszerezve az adatforrásokat.

## <a name="directories-as-logical-data-sets"></a>Könyvtárak, logikai adatkészletek
A big data típusú adatforrások rendszerezéséhez egyik kezelni a könyvtárak logikai adatkészletek. Legfelső szintű könyvtárak segítségével határozhatók meg egy adatkészletet, almappák definiáljon partíciókat, és a fájlokat tartalmazó magát az adatok tárolásához.

Ez a minta egy példát a következő lehet:

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

Ebben a példában a vehicle_maintenance_events és location_tracking_events logikai adatkészletek képviselik. Ezek a mappák mindegyike tartalmaz, amely az év és hónap szerint vannak rendszerezve almappák adatfájlokat. Ezek a mappák mindegyike potenciálisan tartalmazhat több száz vagy ezer.

Ebben a mintában az egyes fájlok regisztrálása **Azure Data Catalog** valószínűleg nem értelmezhető. Ehelyett regisztrálja a könyvtárakat, amelyek fontosak az adatok használata a felhasználók, az adatkészletek képviselik.

## <a name="reference-data-files"></a>Referencia-adatfájlok
Egy kiegészítő egyik különálló fájlokként hivatkozási adatkészletek tárolására. Ezek az adatkészletek is értelmezhetők, a "kis" oldalon, a big Data, és gyakran dimenziók, az analitikus adatok modell hasonló. Referencia az adatfájlokat tartalmaz rekordokat, amelyek a környezetet biztosítanak a big data Store máshol tárolt adatok fájlok a tömeges.

Ez a minta egy példát a következő lehet:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Egy elemző vagy az adatok adatszakértő az található a nagyobb könyvtárstruktúrák használatakor az adatok a referencia-fájlokban segítségével részletesebb információkat az entitások között említett név vagy azonosító alapján a nagyobb adatkészletben.

Ebben a mintában logikus regisztrálja az egyes referencia adatfájlokat a **Azure Data Catalog**. Minden fájl képviseli egy adatkészletet, és mindegyikhez feliratozva, és külön-külön felderített.

## <a name="alternate-patterns"></a>Alternatív minták
A minták az előző szakaszban leírt módon csak két nagy adattárban előfordulhat, hogy rendszerezhetők, de minden implementáció különböző. Függetlenül attól, milyen adatforrást vannak strukturált, a big data típusú adatforrások regisztrálásakor **Azure Data Catalog**, összpontosíthat a fájlok és könyvtárak, az adatkészleteket, amelyek tartozó többi értéket képviselő regisztrálása a szervezet. A fájlok és könyvtárak regisztrációja is telezsúfolni megnehezíti a felhasználók számára való, amit a katalógusban.

## <a name="summary"></a>Összegzés
Az adatforrások nyilvántartására **Azure Data Catalog** könnyebben megtalálhatóvá és értelmezhetővé teszi őket. Való regisztrálásával és a big Data típusú adatok fájlok és könyvtárak képviselő logikai adatkészletek jegyzetkészítés, segítséget nyújthat a felhasználók kereséséhez és használatához a big data típusú adatforrások szükségük van.
