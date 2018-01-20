---
title: "\"A big data\" adatforrások használata |} Microsoft Docs"
description: "A cikk kiemelés az Azure Data Catalog használatával \"big data\" adatforrások, beleértve az Azure Blob Storage tárolóban, az Azure Data Lake és a Hadoop HDFS minták – útmutató."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 826676600094b956ff84cc88c61e667841043837
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Munkavégzés big Data típusú adatok Azure Data Catalog források
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely a regisztráció és a rendszer a vállalati adatforrások felderítési funkcionál. Összes segítve a személyek felderítése, megismeréséhez és használatához adatforrások, és segítséget nyújt a szervezetek kihasználása érdekében a meglévő adatforrásokból, beleértve a big Data típusú adatok is.

**Az Azure Data Catalog** támogatja-e a regisztrációt Azure Blog Storage-BLOB és a könyvtárak, valamint a Hadoop HDFS fájlok és könyvtárak. Ezeknek az adatforrásoknak félig strukturált jellege nagyfokú rugalmasságot biztosít. Azonban a legtöbb érték lekérése regisztrálja őket a **Azure Data Catalog**, felhasználók figyelembe kell venni, hogyan vannak rendszerezve az adatforrásokat.

## <a name="directories-as-logical-data-sets"></a>Könyvtárak, logikai adatkészletek
Az általános mintázatát big Data típusú adatok forrásból szervezése kezelni a könyvtárak logikai adatkészletek. Legfelső szintű könyvtárak segítségével határozni, miközben almappák definiáljon partíciókat, és tartalmazzák a fájlokat tárolni az adatokat mozgatná.

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

Ebben a példában a vehicle_maintenance_events és location_tracking_events logikai adatkészletek jelölik. Ezek a mappák mindegyikének hónap és év szerint vannak csoportosítva almappák adatok fájlokat tartalmazza. Ezek a mappák mindegyikének tartalmazhat több száz vagy ezer.

Ebben a mintában az egyes fájlok regisztrálása **Azure Data Catalog** valószínűleg nem értelmezhető. Regisztrálja a könyvtárakat, amelyek fontosak, a felhasználók a adatokkal végzett munka adatkészletek képviseli.

## <a name="reference-data-files"></a>Hivatkozás az adatfájlok
Egy kiegészítő a mintája, különálló fájlokként hivatkozási adatkészletek tárolására. Ezek az adathalmazok is értelmezhető big Data típusú adatok "kicsi" oldalán, és gyakran hasonlóak az analitikus adatok modell dimenziókat. Hivatkozás az adatfájlok azt jelzi, hogy a tömeges belül máshol tárolódnak a big data store adatfájlok biztosítja a környezet tartalmaz.

Ez a minta egy példát a következő lehet:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Amikor egy elemző vagy az adatok tudósok dolgoznak a nagyobb könyvtárstruktúrák szereplő adatokat, az adatokat a referencia-fájlokban részletesebb információkat, amelyekre csak név vagy azonosító alapján a következő nagyobb adatkészletben entitások használható.

Ebben a mintában érdemes regisztrálni az egyes referencia adatfájlok a **Azure Data Catalog**. Minden fájl jelöl egy adatokat, és minden egyes feliratozva, és egyenként felderített.

## <a name="alternate-patterns"></a>Alternatív minták
A minta az előző szakaszban leírt a big Data típusú adatok tárolási rendezi lehetséges, hogy csak két lehetséges módjait, de minden megvalósítása nem egyezik. Függetlenül attól, hogy az adatforrások felépítése, amikor nagy adatforrások regisztrálása **Azure Data Catalog**, regisztrálja a fájlok és könyvtárak, amelyek megfelelnek az adatkészleteket, amelyek belül érték összpontosítani a szervezet. A fájlok és könyvtárak regisztrálása is megzavarhatják a katalógusban, így nehezebb, hogy a felhasználók megtalálják a szükséges.

## <a name="summary"></a>Összegzés
Az adatforrások nyilvántartására **Azure Data Catalog** könnyebben megtalálhatóvá és értelmezhetővé teszi őket. Regisztráció és a big Data típusú adatok fájlok és könyvtárak, amelyek megfelelnek a logikai adatkészletek ellátása megjegyzésekkel, segíthet felhasználók megkeresheti, a nagy számukra szükséges adatforrásokat.
