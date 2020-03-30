---
title: Adatok másolása az Azure Storage-blobokból a Data Lake Storage Gen1 szolgáltatásba
description: Adatok másolása az Azure Storage Blobokból az Azure Data Lake Storage Gen1 szolgáltatásba az AdlCopy eszközzel
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: ad408df140be49da2e50ef810285dd850e9da6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638868"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Adatok másolása az Azure Storage Blobs szolgáltatásból az Azure Data Lake Storage Gen1 szolgáltatásba

> [!div class="op_single_selector"]
> * [A DistCp használata](data-lake-store-copy-data-wasb-distcp.md)
> * [Az AdlCopy használata](data-lake-store-copy-data-azure-storage-blob.md)
>
>

A Data Lake Storage Gen1 parancssori eszközt, az [AdlCopy parancsot](https://www.microsoft.com/download/details.aspx?id=50358)biztosít a következő forrásokból származó adatok másolásához:

* Az Azure Storage-blobokból a Data Lake Storage Gen1-be. Az AdlCopy nem használhatja adatok másolása a Data Lake Storage Gen1 az Azure Storage blobok.
* Két Data Lake Storage Gen1-fiók között.

Az AdlCopy eszközt két különböző módban is használhatja:

* **Önálló**, ahol az eszköz a Data Lake Storage Gen1 erőforrásokat használja a feladat végrehajtásához.
* **A Data Lake Analytics-fiók használatával,** ahol a Data Lake Analytics-fiókhoz rendelt egységeket használják a másolási művelet végrehajtásához. Ezt a beállítást akkor is használhatja, ha a másolási feladatokat kiszámítható módon szeretné végrehajtani.

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Storage blobok** tároló néhány adatot.
* **A Data Lake Storage Gen1 fiók**. Az azure [Data Lake Storage gen1](data-lake-store-get-started-portal.md) című témakörben talál útmutatást a létrehozásról.
* **Data Lake Analytics-fiók (nem kötelező)** – Az [Azure Data Lake Analytics létrehozásának megkezdéséhez](../data-lake-analytics/data-lake-analytics-get-started-portal.md) olvassa el az Azure Data Lake Analytics létrehozásának lépéseit.
* **AdlCopy eszköz**. Telepítse az [AdlCopy eszközt](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Az AdlCopy eszköz szintaxisa

Az AdlCopy eszközzel való munka a következő szintaxissal

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

A szintaxis paramétereit az alábbiakban ismertetjük:

| Beállítás | Leírás |
| --- | --- |
| Forrás |A forrásadatok helyét adja meg az Azure storage blobban. A forrás lehet egy blob tároló, egy blob, vagy egy másik Data Lake Storage Gen1 fiók. |
| A deszt |Megadja azt a Data Lake Storage Gen1 célt, amelybe másolni szeretne. |
| Forráskulcs |Megadja az Azure storage blob forrásának tárolási hozzáférési kulcsát. Ez csak akkor szükséges, ha a forrás egy blob tároló vagy egy blob. |
| Fiók |**Nem kötelező**. Ezt akkor használja, ha azure Data Lake Analytics-fiókot szeretne használni a másolási feladat futtatásához. Ha a szintaxisban a /Fiók kapcsolót használja, de nem ad meg Data Lake Analytics-fiókot, az AdlCopy egy alapértelmezett fiókot használ a feladat futtatásához. Továbbá, ha ezt a lehetőséget használja, hozzá kell adnia a forrást (Azure Storage Blob) és a cél (Azure Data Lake Storage Gen1) adatforrásként a Data Lake Analytics-fiókhoz. |
| Egység |Megadja a copy feladathoz használt Data Lake Analytics-egységek számát. Ez a beállítás kötelező, ha a **/Account** kapcsolóval adja meg a Data Lake Analytics-fiókot. |
| Mintázat |Megadja a regex mintát, amely jelzi, hogy mely blobokat vagy fájlokat kell másolni. Az AdlCopy a kis- és nagybetűk megkülönböztetését használja. Ha nincs megadva minta, az alapértelmezett minta az összes elem másolása. Több fájlminta megadása nem támogatott. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Adatok másolása Azure Storage-blobból az AdlCopy használatával (önállóként)

1. Nyisson meg egy parancssort, és keresse meg azt `%HOMEPATH%\Documents\adlcopy`a könyvtárat, ahol az AdlCopy telepítve van, általában .
1. A következő paranccsal másolhat egy adott blobot a forrástárolóból egy Data Lake Storage Gen1 mappába:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Példa:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE]
    >A fenti szintaxis a Data Lake Storage Gen1 fiók egy mappájába másolandó fájlt adja meg. Az AdlCopy eszköz létrehoz egy mappát, ha a megadott mappanév nem létezik.

    A rendszer kérni fogja, hogy adja meg az Azure-előfizetés hitelesítő adatait, amely alatt a Data Lake Storage Gen1 fiókkal rendelkezik. A következőhöz hasonló kimenet jelenik meg:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Az összes blobot egy tárolóból is átmásolhatja a Data Lake Storage Gen1 fiókba a következő paranccsal:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Példa:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Ha egy Azure Blob Storage-fiókból másolás, előfordulhat, hogy a blob storage oldalán másolás közben szabályozás alatt. Ez rontja a másolási feladat teljesítményét. Ha többet szeretne megtudni az Azure Blob Storage korlátairól, olvassa el az Azure Storage-korlátozások at [Azure-előfizetési és szolgáltatáskorlátok című témakört.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Adatok másolása másik Data Lake Storage Gen1 fiókból az AdlCopy használatával (önállóként)

Az AdlCopy segítségével adatokat másolhat két Data Lake Storage Gen1 fiók között.

1. Nyisson meg egy parancssort, és keresse meg azt `%HOMEPATH%\Documents\adlcopy`a könyvtárat, ahol az AdlCopy telepítve van, általában .
1. Futtassa a következő parancsot egy adott fájl egyik Data Lake Storage Gen1 fiókból a másikba történő másolásához.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Példa:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > A fenti szintaxis megadja a célData Lake Storage Gen1 fiók egy mappájába másolandó fájlt. Az AdlCopy eszköz létrehoz egy mappát, ha a megadott mappanév nem létezik.
   >
   >

    A rendszer kérni fogja, hogy adja meg az Azure-előfizetés hitelesítő adatait, amely alatt a Data Lake Storage Gen1 fiókkal rendelkezik. A következőhöz hasonló kimenet jelenik meg:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
1. A következő parancs a forrás Data Lake Storage Gen1 fiók egy adott mappájából az összes fájlt átmásolja a célData Lake Storage Gen1 fiók egyik mappájába.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Ha az AdlCopy-t önálló eszközként használja, a másolat megosztott, Azure által felügyelt erőforrásokon fut. Az ebben a környezetben elérhető teljesítmény a rendszer terhelése és a rendelkezésre álló erőforrások függ. Ez a mód a legjobban a kis transzferek eseti alapon. Az AdlCopy önálló eszközként való használatakor nem kell paramétereket hangolni.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Adatok másolása az AdlCopy segítségével (Data Lake Analytics-fiókkal)

A Data Lake Analytics-fiók használatával is futtathatja az AdlCopy-feladatot az Azure storage-blobokból származó adatok átmásolásához a Data Lake Storage Gen1 szolgáltatásba. Ezt a beállítást általában akkor használja, ha az áthelyezendő adatok gigabájt és terabájt tartományban vannak, és jobb és kiszámíthatóbb teljesítményátviteli teljesítményre van szüksége.

Ahhoz, hogy a Data Lake Analytics-fiókot az AdlCopy-rel egy Azure Storage Blobból másolja, a forrást (Azure Storage Blob) hozzá kell adni adatforrásként a Data Lake Analytics-fiókjához. További adatforrások Hozzáadása a Data Lake Analytics-fiókhoz a [Data Lake Analytics-fiók adatforrásai kezelése (Manage Lake Analytics) (Data Lake Analytics-fiókadatforrások kezelése) témakörben](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources)talál.

> [!NOTE]
> Ha egy Azure Data Lake Storage Gen1-fiókból másolja a forrást egy Data Lake Analytics-fiók használatával, nem kell társítania a Data Lake Storage Gen1 fiókot a Data Lake Analytics-fiókkal. A forrástároló nak a Data Lake Analytics-fiókkal való társításának követelménye csak akkor követelmény, ha a forrás egy Azure Storage-fiók.
>
>

Futtassa a következő parancsot, hogy egy Azure Storage-blobból egy Data Lake Storage Gen1-fiókba másoljon a Data Lake Analytics-fiók használatával:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Példa:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Hasonlóképpen futtassa a következő parancsot a forrásData Lake Storage Gen1 fiók egy adott mappájából származó összes fájl átmásolásához a célData Lake Storage Gen1 fiók egyik mappájába a Data Lake Analytics-fiók használatával:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

A terabájttartományban lévő adatok másolásakor az AdlCopy saját Azure Data Lake Analytics-fiókjával való használata jobb és kiszámíthatóbb teljesítményt nyújt. A paraméter, amelyet be kell hangolni az Azure Data Lake Analytics-egységek számát kell használni a másolási feladathoz. Az egységek számának növelése növeli a másolási feladat teljesítményét. Minden másolandó fájl legfeljebb egy egységet használhat. Ha a másolt fájlok számánál több egységet ad meg, az nem növeli a teljesítményt.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Adatok másolása mintaegyeztetéssel az AdlCopy segítségével

Ebben a szakaszban megtudhatja, hogyan használhatja az AdlCopy segítségével adatok másolása egy forrásból (az alábbi példában az Azure Storage Blob) egy cél Data Lake Storage Gen1 fiók minta egyeztetés használatával. Az alábbi lépésekkel például átmásolhatja az összes .csv kiterjesztésű fájlt a forrásblobból a célhelyre.

1. Nyisson meg egy parancssort, és keresse meg azt `%HOMEPATH%\Documents\adlcopy`a könyvtárat, ahol az AdlCopy telepítve van, általában .
1. A következő paranccsal másolja az összes *.csv kiterjesztésű fájlt egy adott blobból a forrástárolóból egy Data Lake Storage Gen1 mappába:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Példa:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Számlázás

* Ha az AdlCopy eszközt önállóként használja, akkor az adatok áthelyezési költségeiért díjat kell fizetnie, ha a forrás Azure Storage-fiók nem ugyanabban a régióban van, mint a Data Lake Storage Gen1 fiók.
* Ha az AdlCopy eszközt használja a Data Lake Analytics-fiókjával, a Data Lake Analytics normál [számlázási díjai](https://azure.microsoft.com/pricing/details/data-lake-analytics/) lesznek érvényben.

## <a name="considerations-for-using-adlcopy"></a>Az AdlCopy használatával kapcsolatos szempontok

* Az AdlCopy (az 1.0.5-ös verzióhoz) támogatja az adatok másolását olyan forrásokból, amelyek együttesen több mint több ezer fájlt és mappát tartalmaznak. Ha azonban nagy adatkészlet másolásával kapcsolatos problémák merülnek fel, a fájlokat/mappákat különböző almappákba terjesztheti, és helyette az almappák elérési útját használhatja forrásként.

## <a name="performance-considerations-for-using-adlcopy"></a>Az AdlCopy használatának teljesítményszempontjai

Az AdlCopy támogatja a több ezer fájlt és mappát tartalmazó adatok másolását. Ha azonban problémákat tapasztal egy nagy adatkészlet másolásával kapcsolatban, a fájlokat/mappákat kisebb almappákba terjesztheti. Az AdlCopy ad hoc másolatokra készült. Ha ismétlődő alapon próbál adatokat másolni, fontolja meg az [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) használatát, amely teljes körű felügyeletet biztosít a másolási műveletek körül.

## <a name="release-notes"></a>Kibocsátási megjegyzések

* 1.0.13 – Ha ugyanarra az Azure Data Lake Storage Gen1-fiókra másolja az adatokat több adlcopy-parancson keresztül, többé nem kell újra megadnia a hitelesítő adatait minden egyes futtatáshoz. Az Adlcopy mostantól több futtatáson keresztül gyorsítótárazza ezeket az információkat.

## <a name="next-steps"></a>További lépések

* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)
