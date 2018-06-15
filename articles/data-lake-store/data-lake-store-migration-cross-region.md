---
title: Azure Data Lake Store kereszt-régió áttelepítési |} Microsoft Docs
description: További tudnivalók az Azure Data Lake Store kereszt-régió történő áttelepítés.
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
ms.openlocfilehash: 1199eca457c3f06fdd6a4b68a05da3210ea9a2c9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34197239"
---
# <a name="migrate-data-lake-store-across-regions"></a>Telepítse át a Data Lake Store régiók között

Az Azure Data Lake Store új régióban elérhetővé, választja előfordulhat, hogy egyszeri áttelepítés, az új régió kihasználásához. Megtudhatja, mit kell megfontolnia a terv és az áttelepítéshez.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. További információkért lásd: [ma létrehozása az ingyenes Azure-fiókjával](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Store-fiók két különböző régiókban**. További információkért lásd: [Ismerkedés az Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Az Azure Data Factory**. További információkért lásd: [Az Azure Data Factory bemutatása](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Az áttelepítés szempontjai

Először azonosítsa az áttelepítési stratégia legjobban az alkalmazáshoz, amely írja, beolvassa és feldolgozza a Data Lake Store-adatokat. Ha úgy dönt, hogy a stratégiát, fontolja meg az alkalmazás rendelkezésre állási követelményeinek, és az áttelepítés során előforduló állásidő. Például a legegyszerűbb módszer lehet a "növekedési-és-shift" felhő áttelepítési modellt használja. Ez a megközelítés a szüneteltetése az alkalmazás a meglévő régióban közben az új régió másolja az adatokat. A másolási folyamat befejeződése után folytathatja az alkalmazás új, és törölje a régi Data Lake Store-fiók. Az áttelepítés során állásidőre szükség.

Csökkentheti az állásidőt, előfordulhat, hogy azonnal indítása új új adatok bevitele. Ha a minimálisan szükséges adatokat, futtassa az alkalmazást az új régióban. A háttérben továbbra is régebbi adatok másolása a meglévő Data Lake Store-fiók új az új Data Lake Store-fiókot. Ezt a módszert használja, végezhet a kapcsoló az új régió minimális állásidővel. Ha a régebbi adatokat másolta, a régi Data Lake Store-fiók törlése.

Az áttelepítés tervezése során megfontolandó tényezőkről más fontos részleteket a következők:

* **Adatmennyiség**. (Gigabájtban, fájlok és mappák, és így tovább száma) adatok mennyisége az idő és az áttelepítés szükséges erőforrások hatással van.

* **Data Lake Store-fiók neve**. Az új fióknevet új globálisan egyedinek kell lennie. Előfordulhat például, hogy nevét a régi Data Lake Store-fiókot az USA keleti régiója 2 contosoeastus2.azuredatalakestore.net. Új Data Lake Store-fiókja az Észak-Európa contosonortheu.azuredatalakestore.net neve lehet.

* **Eszközök**. Azt javasoljuk, hogy használja a [Azure Data Factory másolási tevékenység](../data-factory/connector-azure-data-lake-store.md) Data Lake Store fájlok másolásához. Adat-előállító adatátvitel nagy teljesítményt és a megbízhatóság támogatja. Ne feledje, hogy a Data Factory másolja, csak a mappahierarchia és a fájlok tartalmát. Akkor manuálisan kell alkalmaznia a hozzáférés-vezérlési listák (ACL), amely az új fiók a régi fiókot használja. További információ hányad forgatókönyvek esetén a teljesítmény célokat is beleértve: a [másolási tevékenység teljesítmény- és hangolási útmutató](../data-factory/copy-activity-performance.md). Ha azt szeretné, hogy gyorsabban másolt adatokra, szükség lehet további Felhőbeli adatok adatátviteli egység használatára. Olyan eszközöket, például a AdlCopy, nem támogatják az adatok másolását a régiók között.  

* **Sávszélesség-költségek**. [Sávszélesség-költségek](https://azure.microsoft.com/pricing/details/bandwidth/) alkalmazni, mert egy Azure-régiót kivitt adatok.

* **Az adatok ACL-ek**. Az új adatok biztonságos hozzáférés-vezérlési listák alkalmaz a fájlok és mappák. További információkért lásd: [biztonságossá tétele az Azure Data Lake Store-ban tárolt adatok](data-lake-store-secure-data.md). Azt javasoljuk, hogy az áttelepítés használatával frissíti, és állítsa be a hozzáférés-vezérlési listák. Előfordulhat, hogy használni kívánt beállításokat a jelenlegi beállítások hasonlóak. Megtekintheti az ACL-ek minden olyan fájlt az Azure portál használatával alkalmazott [PowerShell-parancsmagok](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission), vagy az SDK-k.  

* **Az elemzés services helyének**. A legjobb teljesítmény érdekében az analytics-szolgáltatások, Azure Data Lake Analytics vagy az Azure HDInsight, például az adatok ugyanabban a régióban kell lennie.  

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
