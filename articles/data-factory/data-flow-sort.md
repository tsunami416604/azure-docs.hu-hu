---
title: Adatfolyamat leképezése Rendezési transzformáció
description: Azure-adat-előképezési adatok rendezésének átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930226"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure-adat-előállító adatfolyamat rendezési transzformációi



![Beállítások rendezése](media/data-flow/sort.png "Rendezés")

A Rendezés transzformáció lehetővé teszi az aktuális adatfolyam bejövő sorainak rendezését. A rendezési átalakítás kimenő sorai ezt követően a beállított rendezési szabályokat követik. Az egyes oszlopokat kiválaszthatja, és rendezheti őket ASC vagy DEC, az egyes mező melletti nyíljelző segítségével. Ha módosítania kell az oszlopot a rendezés alkalmazása előtt, kattintson a "Számított oszlopok" gombra a kifejezésszerkesztő elindításához. Ez lehetőséget ad arra, hogy a rendezési művelethez kifejezést hozzon létre ahelyett, hogy egyszerűen egy oszlopot alkalmazna a rendezéshez.

## <a name="case-insensitive"></a>Kis- és nagybetűk megkülönböztetése nélkül
Bekapcsolhatja a "Kis- és nagybetűk megkülönböztetése" lehetőséget, ha a karakterlánc- vagy szövegmezők rendezésekor figyelmen kívül szeretné hagyni a kis- és nagybetűket.

A "Csak partíciókon belül rendezés" a Spark-adatok particionálását használja. A bejövő adatok csak az egyes partíciókon belül rendezésével az adatfolyamok rendezheti particionált adatok helyett a teljes adatfolyam rendezése.

A Rendezési transzformáció minden egyes rendezési feltétele átrendezhető. Ha tehát magasabbra kell helyeznie egy oszlopot a rendezési sorrendben, fogja meg a sort az egérrel, és mozgassa magasabbra vagy lejjebb a rendezési listában.

Particionálási effektusok rendezése

Az ADF-adatfolyam a big data Spark-fürtökön történik, több csomópont és partíció között elosztott adatokkal. Fontos, hogy ezt szem előtt tartsa az adatfolyam megtervezésekor, ha a Rendezés átalakítástól függ, hogy az adatok ugyanabban a sorrendben maradjanak. Ha úgy dönt, hogy egy későbbi átalakítás során újraparticionálja az adatokat, az adatok átrendezése miatt elveszhet a rendezés.

## <a name="next-steps"></a>További lépések

A rendezés után érdemes lehet az [Összesítő transzformációt](data-flow-aggregate.md)
