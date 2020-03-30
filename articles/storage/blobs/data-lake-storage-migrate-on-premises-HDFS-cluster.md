---
title: Áttérhet a prem HDFS áruházból az Azure Storage-ba az Azure Data Box segítségével
description: Adatok áttelepítése a helyszíni HDFS-áruházból az Azure Storage-ba
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302000"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Áttérhet a prem HDFS áruházból az Azure Storage-ba az Azure Data Box segítségével

Adatok áttelepítése a helyszíni HDFS-tárolóaak a Hadoop-fürt az Azure Storage (blob storage vagy Data Lake Storage Gen2) egy Data Box-eszköz használatával. Választhat egy 80 TB-os Data Box vagy egy 770-TB Data Box Heavy.

Ez a cikk az alábbi feladatok elvégzését segíti:

> [!div class="checklist"]
> * Felkészülés az adatok áttelepítésére.
> * Másolja adatait egy Data Box vagy egy Data Box Heavy eszközre.
> * Az eszköz visszaszállítása a Microsoftnak.
> * Hozzáférési engedélyek alkalmazása fájlokra és könyvtárakra (csak Data Lake Storage Gen2)

## <a name="prerequisites"></a>Előfeltételek

Ezekre a dolgokra szükség van az áttelepítés befejezéséhez.

* Egy Azure Storage-fiók.

* A helyszíni Hadoop-fürt, amely a forrásadatokat tartalmazza.

* Egy [Azure Data Box-eszköz.](https://azure.microsoft.com/services/storage/databox/)

  * [Rendelje meg a Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) vagy data box [Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). 

  * Kábelezés a [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) vagy a Data [Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) kábellel egy helyszíni hálózathoz.

Ha készen állsz, kezdjük.

## <a name="copy-your-data-to-a-data-box-device"></a>Adatok másolása Adatdoboz-eszközre

Ha az adatok egyetlen Data Box-eszközbe illeszkednek, akkor az adatokat a Data Box eszközre másolja. 

Ha az adatméret meghaladja a Data Box eszköz kapacitását, használja a [választható eljárást az adatok több Adatdoboz-eszközön történő felosztásához,](#appendix-split-data-across-multiple-data-box-devices) majd hajtsa végre ezt a lépést. 

Ha a helyszíni HDFS-tárolóból szeretné másolni az adatokat egy Data Box-eszközre, be kell állítania néhány dolgot, majd a [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) eszközt kell használnia.

Az alábbi lépésekkel másolhatja az adatokat a Blob/Object storage REST API-kon keresztül a Data Box-eszközre. A REST API-felület az eszközt HDFS-tárolóként fogja megjeleníteni a fürt számára.

1. Mielőtt átmásolja az adatokat a REST-en keresztül, azonosítsa a biztonsági és kapcsolatprimitíveket, hogy csatlakozzon a DATA Box vagy a Data Box Heavy REST-kapcsolatához. Jelentkezzen be a Data Box helyi webes felhasználói felületére, és lépjen a **Csatlakozás és másolás** lapra. Az eszköz Azure-tárfiókjában az **Access beállításai**csoportban keresse meg a REST elemet, és válassza a **REST**lehetőséget.

    !["Csatlakozás és másolás" oldal](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Az Access tárfiók és a feltöltési adatok párbeszédpanelen másolja a **Blob szolgáltatás végpontját** és a **Storage-fiók kulcsot.** A blob szolgáltatás végpont, hagyja `https://` ki a és a záró perjel.

    Ebben az esetben a végpont `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`a következő: . Az URI gazdagéprésze a következő: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Például tekintse meg, hogyan [csatlakozhat a REST-hez http-n keresztül.](/azure/databox/data-box-deploy-copy-data-via-rest) 

     !["Tárfiók elérése és adatok feltöltése" párbeszédpanel](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Adja hozzá a végpontot és a Data Box `/etc/hosts` vagy a Data Box Heavy csomópont IP-címét az egyes csomópontokhoz.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Ha valamilyen más mechanizmust használ a DNS-hez, győződjön meg arról, hogy a Data Box végpont feloldható.

4. Állítsa be `azjars` a rendszerhéj-változót a `hadoop-azure` jar `azure-storage` fájlok helyére. Ezeket a fájlokat a Hadoop telepítési könyvtárában találja.

    Annak megállapításához, hogy ezek a `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`fájlok léteznek-e, használja a következő parancsot: . Cserélje `<hadoop_install_dir>` le a helyőrzőt a Hadoop telepítéséhez vezető könyvtár elérési parancsára. Ügyeljen arra, hogy teljesen minősített elérési utakat használjon.

    Példák:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Hozza létre az adatmásoláshoz használni kívánt tárolótárolót. A parancs részeként meg kell adnia egy célkönyvtárat is. Ez lehet egy dummy cél könyvtár ezen a ponton.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Cserélje `<blob_service_endpoint>` le a helyőrzőt a blob szolgáltatás végpontjának nevére.

    * Cserélje `<account_key>` le a helyőrzőt a fiók hozzáférési kulcsára.

    * Cserélje `<container-name>` le a helyőrzőt a tároló nevére.

    * Cserélje `<destination_directory>` le a helyőrzőt annak a könyvtárnak a nevére, amelybe az adatokat másolni szeretné.

6. Futtasson listaparancsot a tároló és a könyvtár létrehozásához.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Cserélje `<blob_service_endpoint>` le a helyőrzőt a blob szolgáltatás végpontjának nevére.

   * Cserélje `<account_key>` le a helyőrzőt a fiók hozzáférési kulcsára.

   * Cserélje `<container-name>` le a helyőrzőt a tároló nevére.

7. Adatok másolása a Hadoop HDFS-ből a Data Box Blob storage-ba, a korábban létrehozott tárolóba. Ha a program nem találja azt a könyvtárat, amelybe a másol, a parancs automatikusan létrehozza azt.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Cserélje `<blob_service_endpoint>` le a helyőrzőt a blob szolgáltatás végpontjának nevére.

    * Cserélje `<account_key>` le a helyőrzőt a fiók hozzáférési kulcsára.

    * Cserélje `<container-name>` le a helyőrzőt a tároló nevére.

    * Cserélje `<exlusion_filelist_file>` le a helyőrzőt a fájlkizárások listáját tartalmazó fájl nevére.

    * Cserélje `<source_directory>` le a helyőrzőt a másolni kívánt adatokat tartalmazó könyvtár nevére.

    * Cserélje `<destination_directory>` le a helyőrzőt annak a könyvtárnak a nevére, amelybe az adatokat másolni szeretné.

    A `-libjars` beállítás sal elérhetővé teheti a `hadoop-azure*.jar` és a függő `azure-storage*.jar` fájlokat a számára. `distcp` Ez egyes fürtökesetében már előfordulhat.

    A következő példa `distcp` bemutatja, hogyan használja a parancsot az adatok másolása.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    A másolási sebesség növelése:

    * Próbálja meg módosítani a leképezők számát. (A fenti `m` példa a = 4 mappers.)

    * Próbáljon `distcp` meg több párhuzamosan futtatni.

    * Ne feledje, hogy a nagy fájlok jobban teljesítenek, mint a kis fájlok.

## <a name="ship-the-data-box-to-microsoft"></a>Az adatdoboz szállítása a Microsoftnak

A Data Box-eszköz előkészítéséhez és a Microsofthoz való elszállításához kövesse az alábbi lépéseket.

1. Először [is, Felkészülés a szállításra a Data Box vagy Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Az eszköz előkészítése befejezése után töltse le az anyagjegyzékfájlokat. Ezeket az anyagjegyzék- vagy jegyzékfájlokat később az Azure-ba feltöltött adatok ellenőrzéséhez fogja használni.

3. Állítsa le a készüléket, és távolítsa el a kábeleket.

4. Egyeztessen egy csomagfelvételi időpontot a UPS-szel.

    * Adatdoboz-eszközök esetén olvassa el [Az adatdoboz szállítása](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * A Data Box Heavy eszközökről a [Data Box Heavy szállítása](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Miután a Microsoft megkapta az eszközt, csatlakozik az adatközpont-hálózathoz, és az adatok feltöltésre kerülnek az eszközrendelés lerendelésekénél megadott tárfiókba. Ellenőrizze az anyagjegyzékfájlok kal szemben, hogy az összes adat feltöltése az Azure-ba. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Hozzáférési engedélyek alkalmazása fájlokra és könyvtárakra (csak Data Lake Storage Gen2)

Már rendelkezik az adatokkal az Azure Storage-fiókjában. Most antól hozzáférési engedélyeket fog alkalmazni a fájlokra és a könyvtárakra.

> [!NOTE]
> Erre a lépésre csak akkor van szükség, ha az Azure Data Lake Storage Gen2-t használja adattárként. Ha csak egy hierarchikus névtér nélküli blobtár-fiókot használ adattárként, kihagyhatja ezt a szakaszt.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Egyszerű szolgáltatás létrehozása az Azure Data Lake Storage Gen2-fiókjához

Egyszerű szolgáltatás létrehozása, [Hogyan: A portál használata az Erőforrások eléréséhez elérhető Azure AD-alkalmazás és egyszerű szolgáltatás létrehozása című témakörben.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

* Amikor végrehajtja az [alkalmazás hozzárendelése](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) a cikk szerepkörszakaszában leírt lépéseket, győződjön meg arról, hogy a **Storage Blob Data Contributor** szerepkört hozzárendeli az egyszerű szolgáltatáshoz.

* A cikk [bejelentkezési értékeinek betöltése](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) című szakaszlépéseinek végrehajtásakor mentse az alkalmazásazonosítót és az ügyféltitkos értékeket egy szöveges fájlba. Hamarosan szükséged lesz rá.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>A másolt fájlok listájának létrehozása az engedélyekkel

A helyszíni Hadoop-fürtből futtassa a következő parancsot:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Ez a parancs létrehozza a másolt fájlok listáját az engedélyekkel.

> [!NOTE]
> A HDFS fájljainak számától függően ez a parancs futtatása hosszú időt vehet igénybe.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Identitások listájának létrehozása és hozzárendelése az Azure Active Directory (ADD) identitásokhoz

1. Töltse `copy-acls.py` le a forgatókönyvet. Tekintse meg a [Segítő parancsfájlok letöltése parancsfájlokat, és állítsa be a peremhálózati csomópontot](#download-helper-scripts) a cikk szakaszának futtatásához.

2. Futtassa ezt a parancsot az egyedi identitások listájának létrehozásához.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Ez a parancsfájl létrehoz `id_map.json` egy nevű fájlt, amely tartalmazza az ADD-alapú identitások hozzárendeléséhez szükséges identitásokat.

3. Nyissa `id_map.json` meg a fájlt egy szövegszerkesztőben.

4. A fájlban megjelenő minden Egyes JSON-objektumhoz frissítse az `target` AAD egyszerű felhasználónév (UPN) vagy az ObjectId (OID) attribútumát a megfelelő leképezett identitással. Miután elkészült, mentse a fájlt. A következő lépésben szüksége lesz erre a fájlra.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Engedélyek alkalmazása másolt fájlokra és identitásleképezések alkalmazása

Ezzel a paranccsal engedélyeket alkalmazhat a Data Lake Storage Gen2 fiókba másolt adatokra:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Cserélje `<storage-account-name>` le a helyőrzőt a tárfiók nevére.

* Cserélje `<container-name>` le a helyőrzőt a tároló nevére.

* Cserélje `<application-id>` le `<client-secret>` a helyőrzőket az alkalmazásazonosítóra és az ügyféltitokra, amelyet a szolgáltatásnév létrehozásakor gyűjtött.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Függelék: Adatok felosztása több Data Box eszközön

Mielőtt áthelyezné az adatokat egy Data Box-eszközre, le kell töltenie néhány segítő szkriptet, biztosítania kell, hogy az adatok úgy legyenek rendszerezve, hogy elférjenek egy Data Box-eszközre, és ki kell zárnia a felesleges fájlokat.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Töltse le a segítő parancsfájlokat, és állítsa be a peremcsomópontot a futtatásukhoz

1. A helyszíni Hadoop-fürt pereméről vagy főcsomópontjáról futtassa ezt a parancsot:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Ez a parancs klónozza a GitHub-tárházat, amely a segítő parancsfájlokat tartalmazza.

2. Győződjön meg arról, hogy a [jq](https://stedolan.github.io/jq/) csomag telepítve van a helyi számítógépen.

   ```bash
   
   sudo apt-get install jq
   ```

3. Telepítse a [Kérelmek](https://2.python-requests.org/en/master/) python csomagot.

   ```bash
   
   pip install requests
   ```

4. Állítsa be a szükséges parancsfájlok végrehajtási engedélyeit.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Annak ellenőrzése, hogy az adatok úgy vannak-e rendszerezve, hogy elférjenek egy Adatdoboz-eszközre

Ha az adatok mérete meghaladja az egyetlen Data Box-eszköz méretét, a fájlokat csoportokra oszthatja, amelyeket több Data Box eszközön is tárolhat.

Ha az adatok nem haladják meg az singe Data Box-eszköz méretét, továbbléphet a következő szakaszra.

1. Emelt szintű engedélyekkel `generate-file-list` futtassa a letöltött parancsfájlt az előző szakaszban található útmutatás szerint.

   A parancs paraméterek leírása:

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

2. Másolja a létrehozott fájllistákat a HDFS fájlba, hogy azok elérhetők legyenek a [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) feladat számára.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Szükségtelen fájlok kizárása

Ki kell zárnia néhány könyvtárat a DisCp-feladatból. Például zárja ki azokat a könyvtárakat, amelyek állapotinformációkat tartalmaznak, amelyek a fürt ötlöttek.

A helyszíni Hadoop-fürtön, ahol a DistCp-feladat elindítását tervezi, hozzon létre egy fájlt, amely megadja a kizárni kívánt könyvtárak listáját.

Például:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan működik a Data Lake Storage Gen2 a HDInsight-fürtökkel. Lásd: [Azure Data Lake Storage Gen2 használata Az Azure HDInsight-fürtökkel.](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
