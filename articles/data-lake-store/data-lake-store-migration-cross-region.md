---
title: Azure Data Lake Storage Gen1 régiók közötti áttelepítés | Microsoft dokumentumok
description: Ismerje meg az Azure Data Lake Storage Gen1 régiók közötti áttelepítését.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60518463"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Az Azure Data Lake Storage Gen1 áttelepítése régiók között

Ahogy az Azure Data Lake Storage Gen1 elérhetővé válik az új régiókban, előfordulhat, hogy egyszeri áttelepítést végez, hogy kihasználja az új régió előnyeit. Ismerje meg, hogy mit kell figyelembe venni az áttelepítés megtervezése és befejezése során.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. További információ: [Create your free Azure-fiók még ma.](https://azure.microsoft.com/pricing/free-trial/)
* **A Data Lake Storage Gen1 fiók két különböző régióban.** További információ: [Az Azure Data Lake Storage Gen1 – Első lépések.](data-lake-store-get-started-portal.md)
* **Az Azure Data Factory**. További információkért lásd: [Az Azure Data Factory bemutatása](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Migrálási szempontok

Először azonosítsa azt az áttelepítési stratégiát, amely a data-tároló gen1-ben írja, olvassa vagy dolgozza fel az adatokat az alkalmazás számára a legjobban. Amikor kiválaszt egy stratégiát, vegye figyelembe az alkalmazás rendelkezésre állási követelményeit, és az áttelepítés során fellépő állásidőt. A legegyszerűbb megoldás például a "lift-and-shift" felhőmigrálási modell használata lehet. Ebben a megközelítésben szünetelteti az alkalmazást a meglévő régióban, miközben az összes adatot másolja az új régióba. Amikor a másolási folyamat befejeződött, folytatja az alkalmazást az új régióban, majd törölje a régi Data Lake Storage Gen1 fiókot. Az áttelepítés során állásidőre van szükség.

Az állásidő csökkentése érdekében előfordulhat, hogy azonnal megkezdi az új adatok betöltését az új régióban. Ha rendelkezik a szükséges minimális adatokkal, futtassa az alkalmazást az új régióban. A háttérben továbbra is másolja a régebbi adatokat a meglévő Data Lake Storage Gen1 fiókból az új Data Lake Storage Gen1 fiókba az új régióban. Ezzel a módszerrel, akkor a váltásaz új régióra kevés állásidővel. Miután az összes régebbi adatok másolása, törölje a régi Data Lake Storage Gen1 fiók.

Az áttelepítés tervezésekor további fontos részleteket kell figyelembe venni:

* **Adatmennyiség**. Az adatok mennyisége (gigabájtban, a fájlok és mappák száma és így tovább) befolyásolja az áttelepítéshez szükséges időt és erőforrásokat.

* **Data Lake Storage Gen1 fióknév**. Az új fióknévnek az új régióban globálisan egyedinek kell lennie. Előfordulhat például, hogy a régi Data Lake Storage Gen1-fiók neve az USA keleti részén 2 contosoeastus2.azuredatalakestore.net. Az új Data Lake Storage Gen1 fiókját elnevezheti észak-eu contosonortheu.azuredatalakestore.net.

* **Eszközök**. Azt javasoljuk, hogy az [Azure Data Factory Copy Activity](../data-factory/connector-azure-data-lake-store.md) használatával másolja a Data Lake Storage Gen1 fájlokat. A Data Factory nagy teljesítménnyel és megbízhatósággal támogatja az adatmozgást. Ne feledje, hogy a Data Factory csak a mappahierarchiát és a fájlok tartalmát másolja. A régi fiókban használt hozzáférés-vezérlési listákat (ACL-eket) manuálisan kell alkalmaznia az új fiókra. További információkért, beleértve a legjobb esetben megadott teljesítménycélokat, olvassa el a [Tevékenység teljesítményének másolása és finomhangolása útmutatót.](../data-factory/copy-activity-performance.md) Ha azt szeretné, hogy az adatok gyorsabban másolhatók, előfordulhat, hogy további felhőbeli adatmozgatási egységeket kell használnia. Néhány más eszköz, például az AdlCopy, nem támogatja az adatok régiók közötti másolását.  

* **Sávszélesség díjak**. [Sávszélesség-díjak](https://azure.microsoft.com/pricing/details/bandwidth/) at, mert az adatok egy Azure-régióból.

* **ACL-k az adatokon.** Az adatok védelme az új régióban az ACL-ok fájlokra és mappákra való alkalmazásával. További információ: [Az Azure Data Lake Storage Gen1-ben tárolt adatok védelme.](data-lake-store-secure-data.md) Azt javasoljuk, hogy az áttelepítés segítségével frissítse és módosítsa az ACL-k. Előfordulhat, hogy a jelenlegi beállításokhoz hasonló beállításokat szeretne használni. Megtekintheti az ACL-k, amelyek bármely fájlra az Azure Portalon, a [PowerShell-parancsmagok](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)vagy SDK-k használatával.  

* **Az elemzési szolgáltatások helye**. A legjobb teljesítmény érdekében az elemzési szolgáltatásoknak, például az Azure Data Lake Analytics vagy az Azure HDInsight, ugyanabban a régióban kell lenniük, mint az adatok.  

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake storage gen1 áttekintése](data-lake-store-overview.md)
