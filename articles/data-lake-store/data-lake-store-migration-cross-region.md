---
title: Az Azure Data Lake Storage Gen1 régiók közötti áttelepítés |} A Microsoft Docs
description: Ismerje meg az Azure Data Lake Storage Gen1 régiók közötti áttelepítése.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 0bf0843314f38c0de28820c82e95b7921297bf40
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233048"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Az Azure Data Lake Storage Gen1 át a régiók között

Az Azure Data Lake Storage Gen1 új régióban elérhetővé válik, mivel egy egyszeri áttelepítést, az új régióban kihasználásához ehhez választhatja. Ismerje meg, mit kell figyelembe venni, és a migrálás befejezésének megtervezése.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. További információkért lásd: [már ma létrehozhatja ingyenes Azure-fiók](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Storage Gen1 fiók két különböző régióban**. További információkért lásd: [Ismerkedés az Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Az Azure Data Factory**. További információkért lásd: [Az Azure Data Factory bemutatása](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Az áttelepítés szempontjai

Először azonosítsa a legjobban az Ön ír, olvassa be és dolgozza fel az adatokat a Data Lake Storage Gen1 stratégiájának. Ha úgy dönt, hogy olyan stratégiát, fontolja meg az alkalmazás rendelkezésre állási követelmények vonatkoznak, és az állásidő, amely során az áttelepítés történik. A legegyszerűbb megközelítés lehet például a "lift-and-shift" felhőbeli migrálás modell használatához. Ebben a megközelítésben az egérmutatót az alkalmazás a meglévő régióban közben minden adatát másolja az új régióban. A másolási folyamat befejeződése után folytathatja az alkalmazás az új régióban, és ezután törölje a régi Data Lake Storage Gen1 fiók. Az áttelepítés során állásidőre szükség.

Csökkentheti az állásidőt, akkor előfordulhat, hogy azonnal indítsa el az új régióban az új adatok feldolgozása. Ha a minimálisan szükséges adatokat, futtassa az alkalmazást az új régióban. A háttérben továbbra is a meglévő Data Lake Storage Gen1 fiók régebbi adatok másolása az új régióban az új Data Lake Storage Gen1 fiókot. Ez a módszer használata esetén győződjön meg a kapcsoló új régió minimális állásidővel. Amikor a rendszer átmásolt a régebbi adatokat, törölje a korábbi Data Lake Storage Gen1 fiókot.

Egyéb fontos részleteket, az áttelepítés tervezésekor a következők:

* **Adatmennyiség**. (Gigabájtban, a fájlok és mappák és így tovább számát) adatok mennyisége hatással van az idő és az áttelepítéshez szükséges erőforrásokat.

* **Data Lake Storage Gen1 fióknév**. Az új fiók nevét az új régióban globálisan egyedinek kell lennie. Például az USA keleti RÉGIÓJA 2, a régi Data Lake Storage Gen1 fiók neve lehet contosoeastus2.azuredatalakestore.net. Előfordulhat, hogy nevezze el az új Data Lake Storage Gen1 fiókjába contosonortheu.azuredatalakestore.net Észak-Európa.

* **Eszközök**. Azt javasoljuk, hogy használja a [Azure Data Factory másolási tevékenység](../data-factory/connector-azure-data-lake-store.md) Data Lake Storage Gen1 fájlokat másolni. A Data Factory támogatja az adatátvitel, nagy teljesítményű és megbízhatósággal. Ne feledje, hogy a Data Factory másolja, csak a mappahierarchiában és a fájlok tartalmát. Manuálisan telepítse minden olyan hozzáférés-vezérlési listák (ACL), az új fiókot a régi fiókot használó kell. További információk, beleértve a teljesítmény-határértékeit hányad forgatókönyvek esetén: a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](../data-factory/copy-activity-performance.md). Ha azt szeretné, hogy gyorsabban másolt adatokat, szüksége lehet további Felhőbeli adatáthelyezési egységek használatával. Néhány más eszközökkel, például az AdlCopy, nem támogatják az adatok másolását a régiók között.  

* **A sávszélesség-költségek**. [A sávszélesség-költségek](https://azure.microsoft.com/pricing/details/bandwidth/) alkalmazni, mert az adatok egy Azure-régión kívül történik.

* **Az adatok hozzáférés-vezérlési listák**. Az új régióban gondoskodhat a fájlok és mappák ACL-ek alkalmazása. További információkért lásd: [az Azure Data Lake Storage Gen1 tárolt adatok védelme](data-lake-store-secure-data.md). Azt javasoljuk, hogy az áttelepítés használatával frissíti, és állítsa be a hozzáférés-vezérlési listák. Előfordulhat, hogy használni kívánt beállításokat a jelenlegi beállítások hasonlóak. Megtekintheti a hozzáférés-vezérlési listák az Azure portal használatával minden olyan fájlt alkalmazott [PowerShell-parancsmagok](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission), és SDK-kkal.  

* **Elemzési szolgáltatások helyét**. A legjobb teljesítmény érdekében az elemzési szolgáltatások, például az Azure Data Lake Analytics vagy az Azure HDInsight, az adatokat ugyanabban a régióban kell lennie.  

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
