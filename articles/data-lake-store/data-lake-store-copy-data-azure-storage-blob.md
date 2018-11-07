---
title: Adatok másolása az Azure Storage-Blobokból az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Adatok másolása az Azure Storage-blobokat az Azure Data Lake Storage Gen1 AdlCopy eszközzel
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
ms.openlocfilehash: 7e218cea543b48371647531cf6d62d8c10a07978
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230750"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Adatok másolása az Azure Storage-blobokat az Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [A DistCp használata](data-lake-store-copy-data-wasb-distcp.md)
> * [Az AdlCopy használata](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Az Azure Data Lake Storage Gen1 biztosít egy parancssori eszköz [AdlCopy](https://aka.ms/downloadadlcopy)a következő forrásokból származó adatokat másolja:

* Az Azure Storage-Blobokból Data Lake Storage Gen1 be. Adatok másolása az Data Lake Storage Gen1 az Azure Storage-blobokat AdlCopy nem használható.
* Az Azure Data Lake Storage Gen1 két-fiókokba.

Az AdlCopy eszköz is használható két különböző módban:

* **Önálló**, ahol az eszköz használ a Data Lake Storage Gen1 erőforrások elvégezni a feladatot.
* **Data Lake Analytics-fiók használatával**, ahol a másolási művelet végrehajtásához az egységek a Data Lake Analytics-fiókhoz rendelt használják. Érdemes ezt a beállítást használja, amikor a másolási feladatokat kiszámítható módon szeretne.

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Storage-blobokat** adatokat tároló.
* **Az Azure Data Lake Storage Gen1 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Az Azure Data Lake Analytics-fiók (nem kötelező)** – lásd: [Ismerkedés az Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) létrehozása a Data Lake Analytics-fiók létrehozásával kapcsolatos útmutatást.
* **Az AdlCopy eszköz**. Telepítse az AdlCopy eszközt [ http://aka.ms/downloadadlcopy ](https://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Az AdlCopy eszköz szintaxisa
A következő szintaxis használatával az AdlCopy eszköz használata

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

A szintaxist a paramétereket az alábbiakban tekintheti át:

| Beállítás | Leírás |
| --- | --- |
| Forrás |A forrásadatok helyét adja meg az Azure storage-blobba. A forrás blob-tároló, blob vagy egy másik Data Lake Storage Gen1 fiók is lehet. |
| Cél |Itt adhatja meg a Data Lake Storage Gen1 célhelyre másolja. |
| SourceKey |Megadja a tárelérési kulcs az Azure storage blob forrás. Ez azért szükséges, csak ha a forrás blob-tároló vagy egy blobot. |
| Fiók |**Választható**. Akkor használja, ha a másolási feladat futtatása az Azure Data Lake Analytics-fiókhoz használni kívánt. Ha a szintaxis a /Account beállítást használják, de ne adjon meg egy Data Lake Analytics-fiók, a AdlCopy egy alapértelmezett fiókot használja a feladat futtatásához. Is ha ezt a beállítást használja, hozzá kell adnia a forrás (Azure Storage-Blob) és a cél (az Azure Data Lake Storage Gen1) adatforrásként szolgálnak a Data Lake Analytics-fiók. |
| Egység |Meghatározza a másolási feladathoz használandó Data Lake Analytics-egységek számát. Ez a beállítás akkor kötelező, ha a **illetve fiók** beállítással azt adhatja meg a Data Lake Analytics-fiók. |
| Mintázat |Megadja egy Reguláriskifejezés-minta, amely azt jelzi, hogy mely blobok vagy a fájlok másolásához. Az AdlCopy használja a megfelelő kis-és nagybetűket. Ha nincs minta meg van adva alapértelmezett mintázatot, hogy az összes elem másolása. Több fájlminták megadása nem támogatott. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>A szolgáltatásnévben AdlCopy (önálló) adatokat másol egy Azure Storage-blobba
1. Nyisson meg egy parancssort, és navigáljon ahhoz a könyvtárhoz, ahol az AdlCopy telepítve van, általában `%HOMEPATH%\Documents\adlcopy`.
2. Futtassa a következő parancsot egy adott blob másolása a forrás-tárolóból egy Data Lake Storage Gen1 mappába:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Példa:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE] 
    >A fenti szintaxisban megadja a fájlt a Data Lake Storage Gen1 fiók egy mappájába kell másolni. Az AdlCopy eszköz mappát hoz létre, ha a megadott mappa nem létezik.

    A rendszer bekéri a hitelesítő adatokat adja meg az Azure-előfizetés alapján, amelyeket a Data Lake Storage Gen1 fiókkal rendelkezik. Az alábbihoz hasonló kimenet jelenik meg:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. A Data Lake Storage Gen1-fiókjába a következő parancsot egy tárolóból is másolhatja az összes BLOB:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Példa:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Másolása az Azure Blob Storage-fiókot, a lehetséges, hogy szabályozva, a blob storage oldalon másolása során. Ez csökkenti a másolási feladat teljesítményét. Az Azure Blob Storage a korlátozásokkal kapcsolatos további tudnivalókért tekintse meg az Azure Storage-korlátok, [Azure-előfizetés- és Szolgáltatáskorlátok](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Az AdlCopy használata (a különálló) adatokat másol egy másik Data Lake Storage Gen1 fiók
Az AdlCopy használatával másolja az adatokat a Data Lake Storage Gen1 két fiók között.

1. Nyisson meg egy parancssort, és navigáljon ahhoz a könyvtárhoz, ahol az AdlCopy telepítve van, általában `%HOMEPATH%\Documents\adlcopy`.
2. Futtassa a következő parancsot egy adott fájl másolása egy Data Lake Storage Gen1 fiókból egy másikba.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Példa:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > A fenti szintaxisban megadja a fájlt a cél Data Lake Storage Gen1 fiók egy mappájába kell másolni. Az AdlCopy eszköz mappát hoz létre, ha a megadott mappa nem létezik.
   >
   >

    A rendszer bekéri a hitelesítő adatokat adja meg az Azure-előfizetés alapján, amelyeket a Data Lake Storage Gen1 fiókkal rendelkezik. Az alábbihoz hasonló kimenet jelenik meg:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. A következő parancsot a Data Lake Storage Gen1 fiók forrás egy adott mappából összes fájlt a cél Data Lake Storage Gen1 fiók egy mappájába másolja.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Az AdlCopy használata önálló eszközként, amikor a másolási futtatja megosztott, az Azure által felügyelt erőforrások. Ebben a környezetben kaphat a teljesítmény rendszerterheléstől és a rendelkezésre álló erőforrások függ. Ebben a módban az ad hoc alapon kisebb átvitelek legjobb szolgál. Paraméterek nélkül kell beállítani, amikor az AdlCopy használata önálló eszközként.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Az AdlCopy használata (a Data Lake Analytics-fiók) adatmásolás céljából
A Data Lake Analytics-fiók használatával az adatok másolása az Azure storage-blobokból a Data Lake Storage Gen1 AdlCopy feladat futtatásával. Általában használja ezt a beállítást amikor az adatok áthelyezésének gigabájt és több terabájtnyi a tartományban, és azt szeretné, hogy jobb és kiszámítható teljesítmény átviteli sebességet.

A Data Lake Analytics-fiók használata az AdlCopy egy Azure Storage-Blobból másol, a forrás (Azure Storage-Blobba) hozzá kell adni a Data Lake Analytics-fiók adatforrásként. A Data Lake Analytics-fiókhoz további adatforrások hozzáadására vonatkozó utasításokért lásd: [kezelheti a Data Lake Analytics-fiók adatforrások](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Ha a Data Lake Analytics-fiók használatával forrásaként másol egy Azure Data Lake Storage Gen1 fiókból, nem kell a Data Lake Storage Gen1 fiókot a Data Lake Analytics-fiókhoz társít. A követelmény a forrástár társítása a Data Lake Analytics-fiók csak akkor, ha a forrás, az Azure Storage-fiókot.
>
>

Futtassa a következő parancsot egy Azure Storage-blobból egy Data Lake Storage Gen1 fiókot a Data Lake Analytics-fiók átmásolása:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Példa:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Hasonlóképpen futtassa a következő parancsot minden fájl másolásához a Data Lake Storage Gen1 fiók forrás egy adott mappából a cél Data Lake Storage Gen1 fiók használatával a Data Lake Analytics-fiók egy mappájába:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Ha több terabájtnyi tartományán másol adatokat, jobb és kiszámíthatóbb teljesítmény az AdlCopy használata a saját Azure Data Lake Analytics-fiókkal biztosít. Úgy kell beállítani, hogy paraméter az Azure Data Lake Analytics egységek a másolási feladathoz használandó számát. Egységek számának növelése növeli a másolási feladat teljesítményét. Az egyes fájlokat másolni egy egység maximális használhatja. Adja meg a fájlok másolását számánál több egység nem javítható a teljesítmény.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Az AdlCopy használatával adatmásolás a minták egyeztetése
Ebben a szakaszban megismerheti, hogyan AdlCopy használatával adatokat másol egy forrásadattárból (ebben a példában az alábbi használjuk az Azure Storage-Blobba) használ a mintamegfeleltetés cél Data Lake Storage Gen1 fiókra. Például használhatja az alábbi lépéseket az összes .csv kiterjesztésű fájlt a forrás blobból a célhelyre.

1. Nyisson meg egy parancssort, és navigáljon ahhoz a könyvtárhoz, ahol az AdlCopy telepítve van, általában `%HOMEPATH%\Documents\adlcopy`.
2. Futtassa a következő parancsot az összes *.csv kiterjesztésű fájlt blobból egy adott a forrás-tárolóból egy Data Lake Storage Gen1 mappába:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Példa:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Számlázás
* Az AdlCopy eszközzel ÖNÁLLÓKÉNT, fog kell fizetnie kimenő forgalom költségeit, az adatmozgatás, ha a forrás Azure Storage-fiók nem a Data Lake Storage Gen1 fiókkal ugyanabban a régióban.
* Ha az AdlCopy eszközzel a Data Lake Analytics-fiók, standard [számlázási díjait a Data Lake Analytics](https://azure.microsoft.com/pricing/details/data-lake-analytics/) érvényes lesz.

## <a name="considerations-for-using-adlcopy"></a>Szempontok az AdlCopy használata
* AdlCopy (a 1.0.5) esetén támogatja a adatmásolásra forrásokból, amelyek együttesen több mint ezer fájlokat és mappákat. Azonban egy nagy méretű adathalmazt másolása problémák merülnek fel, ha a fájlok és mappák elosztása a különböző alárendelt mappákba szabadon inkább az elérési utat az adott almappákra forrásaként.

## <a name="performance-considerations-for-using-adlcopy"></a>Az AdlCopy használata teljesítménnyel kapcsolatos szempontok

Az AdlCopy másolhatja az adatokat tartalmazó fájlok és mappák ezer támogatja. Azonban egy nagy méretű adathalmazt másolása problémák merülnek fel, ha terjeszthető fájlok és mappák kisebb alárendelt mappákba. Az AdlCopy alkalmi másolási lett létrehozva. Ha rendszeres adatokat másolni, akkor érdemes megfontolni [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) , amely körül a másolási műveletek teljes kezelést biztosít.

## <a name="release-notes"></a>Kibocsátási megjegyzések
* 1.0.13 - adatokat másolhat az Azure Data Lake Storage Gen1 ugyanazzal a fiókkal között több adlcopy parancs, ha Ön nem kell adnia a hitelesítő adatait az egyes futtatások többé. Az Adlcopy most ezt az információt fogja gyorsítótárazni több futtatás során.

## <a name="next-steps"></a>További lépések
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
