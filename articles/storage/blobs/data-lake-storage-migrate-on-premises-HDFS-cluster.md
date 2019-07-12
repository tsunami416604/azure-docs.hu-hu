---
title: Használja az Azure Data Box adatainak áttelepítéséhez a helyszíni HDFS tárolhatja az Azure Storage
description: A helyszíni HDFS adattárba adatok áttelepítése az Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595401"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Azure Data Box használatával a helyszíni HDFS adattárba adatok áttelepítése az Azure Storage

Áttelepítheti az adatokat a Hadoop-fürt (a blob storage vagy a Data Lake Storage Gen2) Azure Storage-bA a helyszíni HDFS adattárba egy Data Box-eszköz használatával. Egy 80 TB-os Data Box vagy a 770 TB-os Data Box (nagy erőforrásigényű) közül választhat.

Ez a cikk a feladatok elvégzéséhez nyújt segítséget:

> [!div class="checklist"]
> * Az adatok áttelepítésének előkészítése.
> * Másolja az adatokat a Data Box vagy a Data Box nehéz eszköz.
> * A Microsoftnak az eszköz szállításra.
> * Helyezze át az adatokat a Data Lake Storage Gen2-kiszolgálóra.

## <a name="prerequisites"></a>Előfeltételek

Ezeknek a dolgoknak az áttelepítés elvégzéséhez szüksége lesz.

* Két tárfiókot; hierarchikus névtér engedélyezve van rajta az egyet, és azt, amelyik nem.

* Egy helyi Hadoop-fürtöt, amely tartalmazza a forrásadatokat.

* Egy [Azure Data Box-eszköz](https://azure.microsoft.com/services/storage/databox/).

  * [A Data Box ORDER](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) vagy [Data Box-(nagy erőforrásigényű)](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Miközben az eszköz rendezése, ne felejtse el válasszon egy tárfiókot, amely **nem** hierarchikus névtér engedélyezve van rajta. Ennek az az oka a Data Box-eszköz még nem támogatják a közvetlen feltöltése az Azure Data Lake Storage Gen2. Másolja be egy storage-fiókot, és hajtsa végre a másodpéldány az ADLS Gen2-fiókba kell. Ehhez útmutatást az alábbi lépések szerepelnek.

  * Kábelek, és csatlakozzon a [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) vagy [Data Box nehéz](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a helyszíni hálózathoz.

Ha készen áll, kezdjük.

## <a name="copy-your-data-to-a-data-box-device"></a>Másolja át az adatokat a Data Box-eszköz

Ha az adatok egy Data Box-eszköz illeszkedik, majd, fog másolja az adatokat a Data Box-eszközre. 

Ha az adatok mérete meghaladja a Data Box-eszköz kapacitását, használja a [választható eljárás az adatok felosztása több Data Box-eszközre](#appendix-split-data-across-multiple-data-box-devices) és végezze el ezt a lépést. 

Az adatok másolása a helyszíni HDFS áruházból származó, egy Data Box-eszköz meg fogja beállítása néhány dolgot, majd használja a [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) eszközt.

Kövesse az alábbi lépéseket a REST API -k a Blob/objektumtár keresztül adatok másolása a Data Box-eszközre. A REST API-felület fogja elérhetővé tenni az eszköz és a fürt egy HDFS tárolóként jelennek meg.

1. Előtt másolja az adatokat a REST-en keresztül, azonosíthatja a biztonsági és kapcsolati primitívek a REST-felület, a Data Box vagy a Data Box nehéz csatlakozni. Jelentkezzen be a helyi webes felhasználói felület a Data Box, és nyissa meg **csatlakozás és másolás** lapot. Ellen az Azure storage-fiók az eszköz a **hozzáférési beállítások**, keresse meg és jelölje ki **REST**.

    !["Csatlakozás és másolás" lap](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Hozzáférés a tárfiókban és az adatok feltöltése párbeszédpanel, másolja a **Blob-szolgáltatásvégpont** és a **tárfiókkulcs**. A blob-szolgáltatásvégpont nincs megadva a `https://` és a záró perjellel.

    Ebben az esetben a végpont nem: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Az URI-t fogja használni a gazdagép része: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Egy vonatkozó példáért lásd: hogyan [REST http protokollon keresztüli csatlakozás](/azure/databox/data-box-deploy-copy-data-via-rest). 

     !["A tárfiók eléréséhez és adatok feltöltése" párbeszédpanel](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. A végpont és a Data Box vagy a Data Box nehéz csomópont IP-cím hozzáadása `/etc/hosts` minden egyes csomóponton.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Ha a DNS-néhány egyéb mechanizmust használ, győződjön meg, amely a Data Box végpont feloldható.

4. A rendszerhéj-változó beállítása `azjars` helyére a `hadoop-azure` és `azure-storage` jar-fájlok. Ezeket a fájlokat, a Hadoop a telepítési könyvtárban találja.

    Annak megállapításához, hogy vannak-e ezeket a fájlokat, a következő paranccsal: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Cserélje le a `<hadoop_install_dir>` helyőrzőt a címtárban, hogy hol telepítette Hadoop elérési útját. Győződjön meg arról, az abszolút elérési utakat.

    Példák:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Hozzon létre az adatok másolása használni kívánt tárolót. Meg kell adni a célkönyvtárat Ez a parancs részeként. Ez egy helyőrző célkönyvtárban duplikátum ezen a ponton lehet.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Cserélje le a `<blob_service_endpoint>` helyőrzőt a blob service-végpont nevét.

    * Cserélje le a `<account_key>` helyőrző a fiók hozzáférési kulcsára.

    * Cserélje le a `<container-name>` helyőrzőt a tároló neve.

    * Cserélje le a `<destination_directory>` helyőrzőt a neve annak a címtárnak, amelyhez, az adatokat másolni kívánja.

6. Győződjön meg arról, hogy a tároló és a directory létrehozott lista-parancs futtatása.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Cserélje le a `<blob_service_endpoint>` helyőrzőt a blob service-végpont nevét.

   * Cserélje le a `<account_key>` helyőrző a fiók hozzáférési kulcsára.

   * Cserélje le a `<container-name>` helyőrzőt a tároló neve.

7. Adatok másolása a Hadoop a HDFS-ből a Data Box Blob storage, a tárolóba, amelyet korábban hozott létre. Ha a címtárnak, amelyhez való másolása nem található, a parancs automatikusan létrehozza.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Cserélje le a `<blob_service_endpoint>` helyőrzőt a blob service-végpont nevét.

    * Cserélje le a `<account_key>` helyőrző a fiók hozzáférési kulcsára.

    * Cserélje le a `<container-name>` helyőrzőt a tároló neve.

    * Cserélje le a `<exlusion_filelist_file>` helyőrzőt a fájl kizárások listáját tartalmazó fájl neve.

    * Cserélje le a `<source_directory>` helyőrzőt a neve annak a címtárnak, amelyhez másolni kívánt adatokat tartalmazza.

    * Cserélje le a `<destination_directory>` helyőrzőt a neve annak a címtárnak, amelyhez, az adatokat másolni kívánja.

    A `-libjars` kapcsolóval, hogy a `hadoop-azure*.jar` és a függő `azure-storage*.jar` elérhető fájlok `distcp`. Ez már előfordulhat egyes fürtökben.

    A következő példa bemutatja a `distcp` parancs adatok másolása szolgál.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    A másolási sebesség javításához:

    * Próbálja meg módosítani a leképező számát. (A fenti példában `m` = 4 leképező.)

    * Próbálja meg futtatni több `distcp` párhuzamosan.

    * Ne feledje, hogy a nagy méretű fájlok jobban, mint a kisméretű fájlok hajtható végre.

## <a name="ship-the-data-box-to-microsoft"></a>A Data Box, a Microsoft szállításra

Kövesse az alábbi lépéseket, előkészítése és a Microsoft Data Box-eszköze szállításra.

1. Először [a Data Box vagy a Data Box nehéz szállításra való](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Miután az eszköz előkészítése befejeződött, töltse le a AJ fájlokat. Ezek AJ használja, vagy később, ellenőrizze az adatokat az Azure-bA feltöltött fájlok manifest lesz.

3. Állítsa le az eszközt, és távolítsa el a kábelek.

4. Ütemezhet begyűjtést a UPS.

    * A Data Box-eszközök esetében lásd: [szállításra, a Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * A Data Box nehéz eszközök esetében lásd: [szállításra, a Data Box nehéz](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Microsoft megkapja ezt az eszközt, az Adatközpont-hálózatához csatlakozik, és az adatok a storage-fiók (a hierarchikus névterek le van tiltva) a megadott töltenek fel amikor Ön helyezi az eszközt ahhoz. Ellenőrizze a AJ fájlokra vonatkozóan, hogy az Azure-bA feltöltött összes adatát. Most már továbbléphet az adatok Data Lake Storage Gen2-tárfiókra.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Az adatok áthelyezése az Azure Data Lake Storage Gen2

Már rendelkezik az adatok az Azure Storage-fiókba. Most lesz az adatok másolása az Azure Data Lake storage-fiókba, és hozzáférési engedélyek alkalmazása a fájlok és könyvtárak.

> [!NOTE]
> Ez a lépés akkor szükséges, ha használata Azure Data Lake Storage Gen2 lesz az adattároló. Ha csak egy blob storage-fiók nélkül hierarchikus névtér lesz az adattároló használ, ez a szakasz kihagyhatja.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Adatok másolása az Azure Data Lake Storage általános 2-fiókba

Az Azure Data Factory használatával, illetve az Azure-alapú Hadoop-fürt használatával másolhat adatokat.

* Azure Data Factory használatához lásd: [Azure Data Factoryben az adatok áthelyezése az ADLS Gen2-re](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Ellenőrizze, hogy **Azure Blob Storage** forrásaként.

* Az Azure-alapú Hadoop-fürtöt használ, futtassa a DistCp parancs:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Cserélje le a `<source_account>` és `<dest_account>` helyőrzőket a forrás- és storage-fiókok nevére.

    * Cserélje le a `<source_container>` és `<dest_container>` helyőrzőt a forrás- és tárolók nevei.

    * Cserélje le a `<source_path>` és `<dest_path>` a forrás- és elérési utak helyőrzőt.

    * Cserélje le a `<source_account_key>` helyőrző, amely tartalmazza az adatokat a tárfiók hozzáférési kulcsára.

    Ez a parancs másol adatokat és a metaadatokat a tárfiók a Data Lake Storage Gen2 tárfiókba.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Az Azure Data Lake Storage Gen2-fiókhoz tartozó egyszerű szolgáltatás létrehozása

Egyszerű szolgáltatás létrehozásához lásd: [hogyan: A portál használatával hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* A lépések végrehajtásakor a [alkalmazások szerepkörhöz rendeléséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) szakaszt a cikk, ügyeljen arra, hogy rendelje hozzá a **Storage-Blobadatok Közreműködője** szerepkört a szolgáltatásnévhez.

* A lépések végrehajtásakor a [értékek beolvasása bejelentkezés](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) című cikk, Alkalmazásazonosítót és titkos értékkel ügyfél egy szövegfájlba. Kell azokat, hamarosan.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Másolt fájlok a hozzájuk tartozó jogosultságokat álló lista létrehozása

A helyszíni Hadoop-fürtön a következő parancs futtatásával:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Ez a parancs létrehoz az engedélyeiket másolt fájlok listája.

> [!NOTE]
> A HDFS-ben fájlok számától függően ez a parancs futtatása hosszú időt vehet igénybe.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Lista készítése az identitások, és megfeleltet az identitások Azure Active Directory (Hozzáadás)

1. Töltse le a `copy-acls.py` parancsfájlt. Tekintse meg a [töltse le a segítő szkripteket, és állítsa be az élcsomópont futtatási](#download-helper-scripts) című szakaszát.

2. Az alábbi paranccsal létrehozni az egyedi azonosítók listáját.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Ez a szkript létrehoz egy fájlt `id_map.json` , amely tartalmazza az identitások, amelyek leképezése az ADD-alapú identitások kell.

3. Nyissa meg a `id_map.json` fájlt egy szövegszerkesztőben.

4. Minden JSON-objektum, amely megjelenik a fájlban, frissítse a `target` attribútuma az AAD felhasználói egyszerű név (UPN) vagy az objektumazonosító (OID), a megfelelő identitás leképezve. Miután elkészült, mentse a fájlt. Ez a fájl a következő lépésben lesz szüksége.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Engedélyek alkalmazása a másolt fájlokat, és a alkalmazni identitás-leképezések

Engedélyek alkalmazása a Data Lake Storage Gen2 fiók másolt adatokat a következő parancs futtatásával:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Cserélje le a `<storage-account-name>` helyőrzőt a tárfiók nevére.

* Cserélje le a `<container-name>` helyőrzőt a tároló neve.

* Cserélje le a `<application-id>` és `<client-secret>` az alkalmazás Azonosítóját és az ügyfél titkos az egyszerű szolgáltatás létrehozása során összegyűjtött helyőrzőt.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>A függelék: Adatok felosztása több Data Box-eszközre

Helyezi át az adatokat a Data Box-eszközre, mielőtt szüksége, töltse le az egyes segítő szkripteket, győződjön meg arról, hogy az adatok felépítéséről illeszkedjen a Data Box-eszköz, és kizárja a felesleges fájlokat.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Töltse le a segítő szkripteket, és állítsa be az élcsomópont futtatási

1. Az edge vagy a helyszíni Hadoop-fürt fő csomópontjának, a következő parancs futtatásával:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Ez a parancs klónozza a GitHub-adattár, amely a segítő szkripteket tartalmaz.

2. Győződjön meg arról, hogy rendelkezik a [jq](https://stedolan.github.io/jq/) csomag telepítve van a helyi számítógépen.

   ```bash
   
   sudo apt-get install jq
   ```

3. Telepítse a [kérelmek](http://docs.python-requests.org/en/master/) python-csomag.

   ```bash
   
   pip install requests
   ```

4. Set szükséges szkriptek a végrehajtási engedélyeket.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Győződjön meg arról, hogy az adatok Data Box-eszköz illeszkedjen van rendezve

Ha az adatok mérete meghaladja a egyetlen Data Box-eszköz, feloszthatja fájlok csoportokat, amelyek több Data Box-eszköz arra is tárolhatja.

Ha az adatok egy konfigurált replikához Data Box-eszköz mérete nem haladja meg, folytassa a következő szakaszra.

1. Futtassa emelt szintű engedélyekkel a `generate-file-list` útmutatást az előző szakaszban letöltött szkript.

   A következő parancs paraméter leírása:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Másolás a létrehozott fájl a HDFS sorolja fel, így azok számára is elérhető a [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) feladat.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>A felesleges fájlok kizárása

Egyes könyvtárak kizárása a DisCp feladatot kell. Például kizárandó könyvtárak állapot adatokat tartalmazó, hogy az a rendszert futtató fürtre.

A helyszíni Hadoop-fürt tervezi a DistCp feladatot kezdeményez hozzon létre egy fájlt, amely meghatározza a kizárni kívánt könyvtárak listáját.

Például:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy a Data Lake Storage Gen2 hogyan használható a HDInsight-fürtökkel. Lásd: [használata Azure Data Lake Storage Gen2 Azure HDInsight-fürtök](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
