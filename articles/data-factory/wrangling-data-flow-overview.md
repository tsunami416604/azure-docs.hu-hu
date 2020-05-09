---
title: Huzavona-adatfolyamok Azure Data Factory
description: A Azure Data Factory huzavona-adatforgalmának áttekintése
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 3b8632eaf7662d705235adaad7cd03d2e7e4904b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626871"
---
# <a name="what-are-wrangling-data-flows"></a>Mik azok az huzavona-adatforgalom?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A szervezeteknek adatelőkészítést és huzavona kell végezniük az olyan összetett adatmennyiségek pontos elemzéséhez, amelyek napról napra folyamatosan növekednek. Az adatelőkészítésre azért van szükség, hogy a szervezetek különböző üzleti folyamatok során használhassák az adatmennyiséget, és csökkentsék az időt az értékre.

A Azure Data Factory huzavona adatforgalma lehetővé teszi a kód nélküli adatelőkészítést a felhőalapú méretezési iteratív. A huzavona-adatfolyamatok integrálva vannak [Power Query online](https://docs.microsoft.com/power-query/) -nal, és Power Query M funkciókat biztosítanak a adat-előállító felhasználók számára.

A huzavona adatáramlási folyamata az Power Query online mashup-szerkesztő által generált, a Felhőbeli méretezés végrehajtásához készült Spark-kódra.

A huzavona adatforgalmai különösen hasznosak az adatmérnökök vagy a Citizen-adatintegrátorok számára.

## <a name="use-cases"></a>Használati esetek

### <a name="fast-interactive-data-exploration-and-preparation"></a>Gyors interaktív adatfeltárás és-előkészítés

A több adatmérnökök és az állampolgárok adatintegrátorai interaktív módon tudják felderíteni és előkészíteni az adatkészleteket a Felhőbeli méretekben. Az adattavak mennyiségének, változatosságának és sebességének növekedésével a felhasználóknak hatékony módon kell megvizsgálniuk és előkészíteniük az adatkészleteket. Előfordulhat például, hogy létre kell hoznia egy adatkészletet, amely a "minden ügyfél demográfiai információt tartalmaz az új ügyfelek számára a 2017 óta". Nem egy ismert célhoz van hozzárendelve. Olyan adatkészleteket keres, huzavona és prepping, amelyek megfelelnek egy követelménynek, mielőtt közzéteszik azt a tóban. A huzavona-adatfolyamatokat gyakran használják kevésbé formális elemzési helyzetekben. A elő-adatkészletek felhasználhatók átalakítások és gépi tanulási műveletek végrehajtásához.

### <a name="code-free-agile-data-preparation"></a>Kód nélküli agilis adatelőkészítés

Az állampolgárok adatintegrátorai több mint 60%-ot költenek az adatgyűjtésre és-előkészítésre. Azt szeretnék, hogy a működés hatékonyságának javítása érdekében kód nélküli módon végezzék el. Lehetővé teszi az adatbányászati adatintegrátorok számára, hogy az olyan ismert eszközökkel bővítsék, formálják és tegyenek közzé az információkat, mint például a Power Query online, ami jelentősen javítja a termelékenységet. A Azure Data Factory huzavona-adatfolyama lehetővé teszi a jól ismert Power Query online adategyesítési szerkesztő számára, hogy lehetővé tegye a polgárok adatintegrátorai számára a hibák gyors javítását, a szabványosított adatmennyiséget és az üzleti döntések támogatásához magas színvonalú adatmennyiséget.

### <a name="data-validation"></a>Adatkiértékelés

Az adatokat kód nélküli módon vizsgálhatja, hogy eltávolítsa a kiugró értékeket, a rendellenességeket, és megegyezzen a gyors elemzések egy alakzatával.

## <a name="supported-sources"></a>Támogatott források

| Összekötő | Adatformátum | Hitelesítés típusa |
| -- | -- | --|
| [Azure-Blob Storage](connector-azure-blob-storage.md) | CSV, parketta | Fiók kulcsa |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md) | CSV | Szolgáltatásnév |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, parketta | Fiók kulcsa, egyszerű szolgáltatásnév |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL-hitelesítés |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-hitelesítés |

## <a name="the-mashup-editor"></a>Az összemashup-szerkesztő

Amikor létrehoz egy huzavona-adatfolyamot, az összes forrás-adatkészlet adatkészlet-lekérdezésekvé válik, és a **ADFResource** mappába kerül. Alapértelmezés szerint a UserQuery az első adatkészlet-lekérdezésre mutat. Az összes átalakítást a UserQuery kell elvégezni, mivel az adatkészlet-lekérdezések módosításai nem támogatottak, és nem maradnak meg. A lekérdezések átnevezése, hozzáadása és törlése jelenleg nem támogatott.

![Huzavona](media/wrangling-data-flow/editor.png)

Jelenleg nem minden Power Query M függvény támogatott az adatok huzavona, annak ellenére, hogy a létrehozás során elérhetővé válik. A huzavona-adatfolyamatok létrehozásakor a rendszer a következő hibaüzenetet fogja kérni, ha egy függvény nem támogatott:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

A támogatott átalakításokkal kapcsolatos további információkért lásd: [huzavona-adatfolyam függvények](wrangling-data-flow-functions.md).

Az huzavona-adatfolyam jelenleg csak egy fogadóba való írást támogatja.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [hozhat létre huzavona-adatfolyamokat](wrangling-data-flow-tutorial.md).