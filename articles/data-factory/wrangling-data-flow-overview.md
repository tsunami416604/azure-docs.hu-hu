---
title: Huzavona-adatfolyamok a Azure Data Factoryban | Microsoft Docs
description: A Azure Data Factory huzavona-adatforgalmának áttekintése
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 05119adfa5b254297fd656854a027ede05c5ef26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518579"
---
# <a name="what-are-wrangling-data-flows"></a>Mik azok az huzavona-adatforgalom?

A szervezeteknek adatelőkészítést és huzavona kell végezniük az olyan összetett adatmennyiségek pontos elemzéséhez, amelyek napról napra folyamatosan növekednek. Az adatelőkészítésre azért van szükség, hogy a szervezetek különböző üzleti folyamatok során használhassák az adatmennyiséget, és csökkentsék az időt az értékre.

A Azure Data Factory huzavona adatforgalma lehetővé teszi a kód nélküli adatelőkészítést a felhőalapú méretezési iteratív. A huzavona-adatfolyamatok integrálva vannak [Power Query online](https://docs.microsoft.com/power-query/) -nal, és Power Query M funkciókat biztosítanak a adat-előállító felhasználók számára.

A huzavona adatáramlási folyamata az Power Query online mashup-szerkesztő által generált, a Felhőbeli méretezés végrehajtásához készült Spark-kódra.

A huzavona adatforgalmai különösen hasznosak az adatmérnökök vagy a Citizen-adatintegrátorok számára.

## <a name="use-cases"></a>Használati esetek

### <a name="fast-interactive-data-exploration-and-preparation"></a>Gyors interaktív adatfeltárás és-előkészítés

A több adatmérnökök és az állampolgárok adatintegrátorai interaktív módon tudják felderíteni és előkészíteni az adatkészleteket a Felhőbeli méretekben. Az adattavak mennyiségének, változatosságának és sebességének növekedésével a felhasználóknak hatékony módon kell megvizsgálniuk és előkészíteniük az adatkészleteket. Előfordulhat például, hogy létre kell hoznia egy adatkészletet, amely a "minden ügyfél demográfiai információt tartalmaz az új ügyfelek számára a 2017 óta". Nem egy ismert célhoz van hozzárendelve. Olyan adatkészleteket keres, huzavona és prepping, amelyek megfelelnek egy követelménynek, mielőtt közzéteszik azt a tóban. A huzavona-adatfolyamatokat gyakran használják kevésbé formális elemzési helyzetekben. A elő-adatkészletek felhasználhatók átalakítások és gépi tanulási műveletek végrehajtásához.

### <a name="code-free-agile-data-preparation"></a>Kód nélküli agilis adatelőkészítés

Az állampolgárok adatintegrátorai több mint 60%-ot költenek az adatgyűjtésre és-előkészítésre. Azt szeretnék, hogy a működés hatékonyságának javítása érdekében kód nélküli módon végezzék el. Lehetővé teszi az adatbányászati adatintegrátorok számára, hogy az olyan ismert eszközökkel bővítsék, formálják és tegyenek közzé az információkat, mint például a Power Query online, ami jelentősen javítja a termelékenységet. A Azure Data Factory huzavona-adatfolyama lehetővé teszi a jól ismert Power Query online adategyesítési szerkesztő számára, hogy lehetővé tegye a polgárok adatintegrátorai számára a hibák gyors javítását, a szabványosított adatmennyiséget és az üzleti döntések támogatásához magas színvonalú adatmennyiséget.

### <a name="data-validation"></a>Adatérvényesítés

Az adatokat kód nélküli módon vizsgálhatja, hogy eltávolítsa a kiugró értékeket, a rendellenességeket, és megegyezzen a gyors elemzések egy alakzatával.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [hozhat létre huzavona-adatfolyamokat](wrangling-data-flow-tutorial.md).