---
title: Az Azure Data Factory ban zajló adatfolyamok
description: Az Azure Data Factory ban zajló adatfolyamatok áttekintése
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 71df574602518fdc252d9d86859ed772a17be443
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408982"
---
# <a name="what-are-wrangling-data-flows"></a>Mik azok a huzavona adatfolyamok?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A szervezeteknek adatelőkészítést és huzavonát kell végezniük az összetett adatok pontos elemzéséhez, amelyek napról napra növekednek. Az adatok előkészítése szükséges ahhoz, hogy a szervezetek felhasználhassák az adatokat a különböző üzleti folyamatokban, és csökkenthessék az értékhez szükséges időt.

Az Azure Data Factory ban zajló adatfolyamatok lehetővé teszik, hogy a kódmentes adatelőkészítést felhőalapú, iteratív módon végezze el. A vonódsás adatfolyamok integrálhatók a [Power Query Online-nal,](https://docs.microsoft.com/power-query/) és elérhetővé teszik a Power Query M függvényeket az adatfeldolgozó felhasználók számára.

A huzavona adatfolyam a Power Query Online Mashup Editor által generált M-et szikrakóddaként fordítja le a felhőalapú méretezés végrehajtásához.

A vonóds adatfolyamok különösen hasznosak az adatmérnökök vagy a "polgári adatintegrátorok" számára.

## <a name="use-cases"></a>Használati esetek

### <a name="fast-interactive-data-exploration-and-preparation"></a>Gyors interaktív adatfeltárás és -előkészítés

Több adatmérnök és a polgárok adatintetegrátorai interaktívmódon feltárhatják és előkészíthetik az adatkészleteket felhőméretekben. Az adattavakban lévő adatok mennyiségének, változatosságának és sebességének növekedésével a felhasználóknak hatékony módon kell feltárniuk és előkészíteniük az adatkészleteket. Előfordulhat például, hogy olyan adatkészletet kell létrehoznia, amely "2017 óta rendelkezik az új ügyfelek összes ügyféldemográfiai adatával". Nem egy ismert célponthoz mész. Az adatkészletek feltárása, huzavona és előkészítése, hogy megfeleljen egy követelménynek, mielőtt közzétenné azt a tóban. A huzavonaadat-folyamatokat gyakran használják kevésbé formális elemzési forgatókönyvekhez. Az előkészített adatkészletek átalakítási és gépi tanulási műveletek downstream.

### <a name="code-free-agile-data-preparation"></a>Kódmentes agilis adatelőkészítés

A polgári adatintegrátorok idejük több mint 60%-át adatkeresést és -előkészítést töltenek. Kódmentes módon szeretnék megtenni, hogy javítsák a működési termelékenységet. A polgárok adatintetegrátorai számára lehetővé teszi az adatok gazdagítását, alakítását és közzétételét ismert eszközök, például a Power Query Online méretezhető módon történő skálázható módon, ami jelentősen javítja a hatékonyságot. Az Azure Data Factory ban található cigis adatfolyam lehetővé teszi, hogy a jól ismert Power Query Online-egyesítési szerkesztő lehetővé tegye a polgárok adatintegrátorai számára a hibák gyors javítását, az adatok szabványosítását és az üzleti döntések támogatásához kiváló minőségű adatok előállítását.

### <a name="data-validation"></a>Adatkiértékelés

Vizuálisan bekéselheti az adatokat kódmentes módon, hogy eltávolítson minden kiugró, anomáliát, és a gyors elemzéshez használható alakzatnak feleljen meg.

## <a name="supported-sources"></a>Támogatott források

| Összekötő | Adatformátum | Hitelesítés típusa |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parketta | Fiókkulcs |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md) | CSV | Szolgáltatásnév |
| [2. generációs Azure Data Lake Storage](connector-azure-data-lake-storage.md) | CSV, Parketta | Fiókkulcs, egyszerű szolgáltatás |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL-hitelesítés |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-hitelesítés |

## <a name="the-mashup-editor"></a>A mashup szerkesztő

Amikor wrangling adatfolyamatot hoz létre, az összes forrásadatkészlet adatkészlet-lekérdezéssé válik, és az **ADFResource** mappába kerül. Alapértelmezés szerint a UserQuery az első adatkészlet-lekérdezésre mutat. A UserQuery en minden átalakítást el kell végezni, mivel az adatkészlet-lekérdezések módosításai nem támogatottak, és nem is maradnak meg. A lekérdezések átnevezése, hozzáadása és törlése jelenleg nem támogatott.

![Vonagló](media/wrangling-data-flow/editor.png)

Jelenleg nem minden Power Query M függvény támogatott az adatveréshez, annak ellenére, hogy a szerzői jog során elérhető. A huzavona adatfolyamainak létrehozása során a következő hibaüzenet jelenik meg, ha egy függvény nem támogatott:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

A támogatott átalakításokkal kapcsolatos további információkért tekintse meg az [adatfolyam-függvények összekulcsos funkcióját.](wrangling-data-flow-functions.md)

Jelenleg az adatfolyam csak egy fogadóba való írást támogat.

## <a name="next-steps"></a>További lépések

További információ a rról, hogyan [hozhat létre vonó adatfolyamot.](wrangling-data-flow-tutorial.md)