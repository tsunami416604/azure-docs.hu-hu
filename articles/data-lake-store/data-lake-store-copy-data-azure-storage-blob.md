---
title: Adatok másolása az Azure Storage blobs szolgáltatásban a Data Lake Store |} Microsoft Docs
description: Adatok másolása az Azure Storage Blobs Data Lake Store AdlCopy eszközzel
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 19657030c69d9d62fbbe0a8058e50238b2afa67f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750116"
---
# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Adatok másolása az Azure Storage-blobokból a Data Lake Store-ba
> [!div class="op_single_selector"]
> * [A DistCp használata](data-lake-store-copy-data-wasb-distcp.md)
> * [Az AdlCopy használata](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Store biztosítja a parancssori eszköz [AdlCopy](http://aka.ms/downloadadlcopy), a következő forrásokból származó adatok másolása:

* Tárolóblobokból az Azure Data Lake-tárolóba. Adatok másolása az Data Lake Store az Azure Storage blobs AdlCopy nem használható.
* Között két Azure Data Lake Store-fiókot.

Ezenkívül használhatja a AdlCopy eszköz két különböző módban:

* **Önálló**, ahol az eszköz Data Lake Store erőforrást használ a feladat végrehajtásához.
* **A Data Lake Analytics-fiókkal**, ahol a másolási művelet végrehajtásához az egység a Data Lake Analytics-fiókjához társított használják. Előfordulhat, hogy szeretné ezt a beállítást használja, ha szeretné a másolási feladatokat kiszámítható módon.

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Storage Blobs** néhány adat-tárolóban.
* **Egy Azure Data Lake Store-fiók**. Hogyan hozhat létre ilyet, lásd: [Ismerkedés az Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Az Azure Data Lake Analytics fiók (nem kötelező)** -lásd [Ismerkedés az Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) Data Lake Store-fiók létrehozásához útmutatást.
* **AdlCopy eszköz**. Telepítse a AdlCopy eszközt [ http://aka.ms/downloadadlcopy ](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>A AdlCopy eszköz szintaxisa
Az alábbi szintaxissal a AdlCopy eszköz használata

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

A paraméterek a szintaxist a leírása a következő:

| Beállítás | Leírás |
| --- | --- |
| Forrás |Adja meg az adatok helye az Azure storage-blobba. A forrás lehet egy blob-tároló, egy blobba vagy egy másik Data Lake Store-fiók. |
| Cél |Adja meg a Data Lake Store cél másolja. |
| SourceKey |Adja meg a hozzáférési kulcsot az Azure storage-blob adatforrásra vonatkozóan. Ez azért szükséges, csak ha a forrás egy blob-tároló vagy egy blobot. |
| Fiók |**Választható**. Akkor használja, ha azt szeretné, a másolási feladat futtatása az Azure Data Lake Analytics-fiók használatával. Ha használja a /Account lehetőséget a szintaxissal, de nem adja meg a Data Lake Analytics-fiók, AdlCopy egy alapértelmezett fiók használatával futtatni a feladatot. Is ha ezt a beállítást használja, hozzá kell adnia a forrás (Azure Storage-Blobba) és a cél (az Azure Data Lake Store) adatforrásaként a Data Lake Analytics-fiókhoz. |
| Egység |Itt adhatja meg, amely jelzi a másolási feladatot a Data Lake Analytics egységek száma. Ez a beállítás nem kötelező, ha a **/fiók** beállítással adhatja meg a Data Lake Analytics-fiók. |
| Mintázat |Adja meg a reguláris kifejezéssel mintát, amely azt jelzi, mely blobokkal vagy a fájlok másolása. AdlCopy használja a megfelelő kis-és nagybetűket. Az alapértelmezett használható, ha nincs minta van megadva a mintája, másolja az összes elemet. Több fájl minták megadása nem támogatott. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Használják AdlCopy (önálló) adatok másolása az Azure Storage-blobba
1. Nyisson meg egy parancssort, és keresse meg azt a könyvtárat, AdlCopy futtató, általában `%HOMEPATH%\Documents\adlcopy`.
2. A következő parancsot egy adott blob átmásolja a forrás-tároló egy Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Példa:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[AZURE.NOTE] A fenti szintaxist határozza meg, hogy a fájl másolása egy mappába a Data Lake Store-fiókban. AdlCopy eszköz egy mappát hoz létre, ha a megadott mappanév nem létezik.

    A rendszer bekéri a hitelesítő adatok megadása az Azure-előfizetés alapján, amely rendelkezik a Data Lake Store-fiók. Egy a következőhöz hasonló kimenetet fog látni:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Is átmásolhatja a blobokat több tároló a Data Lake Store-fiókba, a következő parancsot:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Példa:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Az Azure Blob Storage-fiók másolása, a lehetséges, hogy szabályozva, a blob storage oldalon másolása során. Ez csökkenti a teljesítményt, a másolat. Az Azure Blob Storage korlátai által megszabott kapcsolatos további információkért lásd: Azure Storage-korlátok, [Azure-előfizetés és a szolgáltatásra vonatkozó korlátozások](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Használják AdlCopy (önálló) adatok másolása másik Data Lake Store-fiók
AdlCopy használatával másolja az adatokat két Data Lake Store-fiókok között.

1. Nyisson meg egy parancssort, és keresse meg azt a könyvtárat, AdlCopy futtató, általában `%HOMEPATH%\Documents\adlcopy`.
2. A következő parancsot egy adott fájlt átmásolni egy Data Lake Store-fiók egy másikra.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Példa:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > A fenti szintaxist határozza meg, hogy a fájl másolása egy mappába a célként megadott Data Lake Store-fiók. AdlCopy eszköz egy mappát hoz létre, ha a megadott mappanév nem létezik.
   >
   >

    A rendszer bekéri a hitelesítő adatok megadása az Azure-előfizetés alapján, amely rendelkezik a Data Lake Store-fiók. Egy a következőhöz hasonló kimenetet fog látni:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. A következő parancsot egy adott mappából a forráshelyen Data Lake Store-fiók összes fájlt másolja a célként megadott Data Lake Store-fiók mappába.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

AdlCopy önálló eszközként használatakor a példány futtatja megosztott, az Azure által felügyelt erőforrások. A teljesítmény jelenhet meg ebben a környezetben a rendszerterhelés és a rendelkezésre álló erőforrások függ. Ebben a módban leginkább kis átvitelek eseti alapon szolgál. Paraméterek nélkül kell AdlCopy használata önálló eszközként kell beállítani.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Használja AdlCopy (Data Lake Analytics-fiók) adatok másolása
A Data Lake Analytics-fiók használatával a Data Lake Store-adatok másolása az Azure storage blobs AdlCopy feladat futtatása. Általában használja ezt a beállítást, amikor az adatok áthelyezésének gigabájt és terabájt a tartományban, és azt szeretné, hogy jobb és kiszámítható teljesítmény átviteli sebesség.

A Data Lake Analytics-fiók használata AdlCopy másolása az Azure Storage-Blobból, a forrás (Azure Storage-Blobba) a Data Lake Analytics-fiók adatforrásként hozzá kell adni. A Data Lake Analytics-fiókhoz való hozzáadását további adatforrások, lásd: [Data Lake Analytics kezelése az adatforrások fiók](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> A Data Lake Analytics-fiókkal forrásaként másolása az Azure Data Lake Store-fiókból, nem kell a Data Lake Store-fiók társítása a Data Lake Analytics-fiók. A követelmény a forrás tárolási társítja a Data Lake Analytics-fiók csak akkor, ha a forrás álljon az Azure Storage-fiók.
>
>

A következő parancsot egy Data Lake Store-fiók használatával a Data Lake Analytics-fiók egy Azure Storage-blob másolása:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Példa:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Hasonlóképpen futtassa a következő parancs futtatásával másolja az összes fájlt a forrásból Data Lake Store-fiók egy adott mappából egy mappához a cél Data Lake Store-fiók használatával a Data Lake Analytics-fiók:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Az adatok másolásának terabájt közé, amikor a AdlCopy a saját Azure Data Lake Analytics-fiók használatával hatékonyabb és sokkal kiszámíthatóbbá teljesítményt biztosít. Amely kell kell hangolni paraméter Azure Data Lake Analytics egység a másolási feladat használandó számát. Egységek számának növelésével növeli a teljesítményt, a másolat. A másolandó fájlok maximális egy egységet használhat. Fájlok másolásának számánál több egységek megadása nem növeli a teljesítményt.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Másolja az adatokat használ a mintamegfeleltetés AdlCopy segítségével
Ebben a szakaszban megismerheti, hogyan AdlCopy használatával másolja az adatokat egy forrásból (az alábbi példában használjuk Azure Storage-Blobba) használ a mintamegfeleltetés cél Data Lake Store-fiókra. Az alábbi lépéseket használhatja például a forrás blob összes .csv kiterjesztésű fájlt átmásolni a cél.

1. Nyisson meg egy parancssort, és keresse meg azt a könyvtárat, AdlCopy futtató, általában `%HOMEPATH%\Documents\adlcopy`.
2. A következő parancsot az összes *.csv kiterjesztésű fájlt átmásolni egy adott blob-forrás tárolójából. a Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Példa:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Számlázás
* Ha a AdlCopy eszközzel önálló számlázott kilépő költségek áthelyezésére adatokat, ha a forrás Azure Storage-fiók nem ugyanabban a régióban, mint a Data Lake Store.
* Ha a AdlCopy eszköz használata a Data Lake Analytics fiók, szabványos [Data Lake Analytics díjszabás számlázási](https://azure.microsoft.com/pricing/details/data-lake-analytics/) alkalmazza.

## <a name="considerations-for-using-adlcopy"></a>AdlCopy használatának szempontjai
* (Verziójához 1.0.5), AdlCopy támogatja, amelyek együttesen több ezer fájlok és mappák forrásokból származó adatok másolását. Azonban, hogy a felmerülő problémák nagy dataset másolása után a fájlok és mappák terjesztése különböző alárendelt mappákba is inkább az elérési út a alárendelt mappákra forrásaként.

## <a name="performance-considerations-for-using-adlcopy"></a>AdlCopy a teljesítménnyel kapcsolatos szempontok

AdlCopy támogatja az adatok másolását a fájlok és mappák ezer tartalmazó. Azonban ha problémák nagy dataset másolása, terjesztheti a fájlok és mappák kisebb alárendelt mappákba. AdlCopy alkalmi példány lett létrehozva. Ha az ismétlődő adatokat másolni kívánt, érdemes [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) , amely körül a másolási műveletek teljes felügyeletet biztosít.

## <a name="release-notes"></a>Kibocsátási megjegyzések
* 1.0.13 - másolása adatok a azonos Azure Data Lake Store-fiókba között több adlcopy parancs, akkor nem kell minden egyes futtatásához a hitelesítő adatok újbóli többé. Adlcopy között több futtatása most gyorsítótárazhatják az adatokat.

## <a name="next-steps"></a>További lépések
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
