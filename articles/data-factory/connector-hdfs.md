---
title: Adatok másolása a HDFS-ből az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat egy felhőbeli vagy helyszíni HDFS-forrásból a támogatott fogadó adattárak egy Azure Data Factory-folyamat másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 2cd76afa9412e89c57cfb6c357eb164ce5d3d1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830428"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Adatok másolása a HDFS-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hdfs-connector.md)
> * [Aktuális verzió](connector-hdfs.md)

Ez a cikk bemutatja, hogyan lehet adatokat másolni a HDFS-kiszolgálóról. Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a HDFS-csatlakozó a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Ez a HDFS-csatlakozó különösen a következőket támogatja:

- Fájlok másolása **Windows** (Kerberos) vagy **Névtelen** hitelesítés használatával.
- Fájlok másolása **webhdfs** protokoll vagy **beépített DistCp-támogatás** használatával.
- Fájlok másolása a támogatott fájlformátumok és [tömörítési kodekek](supported-file-formats-and-compression-codecs.md)segítségével.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Győződjön meg arról, hogy az integrációs futásidő hozzáférhet a Hadoop-fürt **összes** [name node kiszolgálójához]:[névcsomópont-port] és a Hadoop-fürt [adatcsomópont-kiszolgálók] számára. Az alapértelmezett [névcsomópont-port] 50070, az alapértelmezett [adatcsomópont-port] pedig 50075.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a HdFS-re jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A hdfs-hez csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak a következőre kell állítania: **Hdfs**. | Igen |
| url |A HDFS URL-címe |Igen |
| authenticationType | Az engedélyezett értékek a következők: **Névtelen**vagy **Windows**. <br><br> **Kerberos-hitelesítés** hdfs-összekötő használatához tekintse meg ezt a [szakaszt](#use-kerberos-authentication-for-hdfs-connector) a helyszíni környezet ennek megfelelő beállításához. |Igen |
| userName (Felhasználónév) |Felhasználónév a Windows-hitelesítéshez. Kerberos-hitelesítésesetén `<username>@<domain>.com`adja meg a mezőt. |Igen (Windows-hitelesítéshez) |
| jelszó |Jelszó a Windows-hitelesítéshez. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Igen (Windows-hitelesítéshez) |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

**Példa: névtelen hitelesítés használata**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: Windows-hitelesítés használata**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A hdfs-ek a `location` következő tulajdonságokat támogatják a formátumalapú adatkészlet beállításai között:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészlet `location` típustulajdonságát **HdfsLocation**beállításra kell állítani. | Igen      |
| folderPath | A mappa elérési útja. Ha helyettesítő karaktert szeretne használni a mappa szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |
| fileName   | A fájlnév a megadott mappaPath. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |

**Példa:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a HDFS-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hdfs-as-source"></a>HDFS forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A hdfs a formátumalapú `storeSettings` másolási forrás beállításai között a következő tulajdonságokat támogatja:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A típustulajdonságot `storeSettings` **hdfsReadSettings (HdfsReadSettings )** tulajdonságra kell állítani. | Igen                                           |
| Rekurzív                | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ne feledje, hogy ha a rekurzív érték igaz, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban. Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem                                            |
| helyettesítő mappaelérési út       | A forrásmappák szűréséhez helyettesítő karakterekkel ellátott mappaelérési út. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne. <br>További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Nem                                            |
| helyettesítő fájlneve         | A forrásfájlok szűréséhez helyettesítő karakterekkel rendelkező fájlnév a kívánt/helyettesítő folderFolderPath mappában. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne.  További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint fent.                                               | Nem                                            |
| distcpBeállítások | Tulajdonságcsoport a HDFS DistCp használatakor. | Nem |
| resourceManagerEndpoint | A Fonal erőforrás-kezelő végpontja | Igen, ha a DistCp |
| tempScriptPath | Az ideiglenes DistCp parancsparancsfájl tárolására használt mappaelérési út. A parancsfájlt a Data Factory hozza létre, és a másolási feladat befejezése után törlődik. | Igen, ha a DistCp |
| distcpOptions | A DistCp parancs további lehetőségei. | Nem |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem                                            |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák mappa- és fájlszűrőre

Ez a szakasz a mappa elérési útjának és a helyettesítő szűrőkkel rendelkező fájlnévnek a viselkedését ismerteti.

| folderPath | fileName             | Rekurzív | A forrásmappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (üres, használja az alapértelmezettet) | hamis     | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*`  | (üres, használja az alapértelmezettet) | igaz      | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*`  | `*.csv`              | hamis     | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*`  | `*.csv`              | igaz      | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Adatok másolása a HDFS-ből a DistCp használatával

[A DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) egy Hadoop natív parancssori eszköz, amely elosztott másolatot készít egy Hadoop-fürtben. A Distcp parancs futtatásakor először felsorolja az összes másolandó fájlt, több térképmunkát hoz létre a Hadoop-fürtbe, és minden egyes map feladat bináris másolatot készít a forrástól a fogadóig.

A DistCp használatával a DistCp használatával a fájlok másolása az Azure Blobba (beleértve [a szakaszos másolást](copy-activity-performance.md)is) vagy az Azure Data Lake Store-ba, amely esetben teljes mértékben kihasználhatja a fürt erejét, ahelyett, hogy az önkiszolgáló integrációs futtatókörnyezeten futna. Ez biztosítja a jobb másolási átviteli, különösen, ha a fürt nagyon erős. Az Azure Data Factory konfigurációja alapján a másolási tevékenység automatikusan létrehoz egy distcp parancsot, elküldi a Hadoop-fürtnek, és figyeli a másolás állapotát.

### <a name="prerequisites"></a>Előfeltételek

Ha a DistCp használatával a HDFS-ből az Azure Blobba (beleértve a szakaszos másolást is) vagy az Azure Data Lake Store-ba való fájlok másolásához a DistCp használatával győződjön meg arról, hogy a Hadoop-fürt megfelel az alábbi követelményeknek:

1. A MapReduce és a Yarn szolgáltatások engedélyezve vannak.
2. Fonal változat 2.5 vagy annál magasabb.
3. A HDFS-kiszolgáló integrálva van a céladattárba – Az Azure Blob vagy az Azure Data Lake Store:

    - Az Azure Blob FileSystem natívan támogatott a Hadoop 2.7 óta. Csak meg kell adnia jar elérési útját a Hadoop env config.You only need to specify jar path in Hadoop env config.
    - Az Azure Data Lake Store fájlrendszer a Hadoop 3.0.0-alpha1-től kezdve van csomagolva. Ha a Hadoop-fürt alacsonyabb, mint az adott verzió, manuálisan kell importálnia az ADLS-hez kapcsolódó jar csomagokat (azure-datalake-store.jar) a fürtbe [innen,](https://hadoop.apache.org/releases.html)és meg kell adnia a jar elérési útját a Hadoop env config-ban.

4. Ideiglenes mappa előkészítése a HDFS fájlban. Ez az ideiglenes mappa a DistCp rendszerhéj-parancsfájl tárolására szolgál, így kb-szintű helyet foglal el.
5. Győződjön meg arról, hogy a HDFS csatolt szolgáltatásban megadott felhasználói fiók rendelkezik engedéllyel a) kérelem benyújtására fonalban; b) az engedélyt, hogy hozzon létre almappát, olvasási /írási fájlok at fenti temp mappát.

### <a name="configurations"></a>Konfigurációk

Lásd: DistCp kapcsolódó konfigurációk és példák [a HDFS forrásszakaszban.](#hdfs-as-source)

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-hitelesítés használata A HDFS-összekötőhöz

Két lehetőség van a helyszíni környezet beállítására, hogy a Kerberos-hitelesítést használhassa a HDFS-összekötőben. Kiválaszthatja azt, aki jobban illik az ön esetéhez.
* 1. lehetőség: [Csatlakozás az önkiszolgáló integrációs futásidejű géphez a Kerberos-birodalomban](#kerberos-join-realm)
* 2. lehetőség: [A Windows-tartomány és a Kerberos tartomány közötti kölcsönös bizalom engedélyezése](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>1. lehetőség: Csatlakozás az önkiszolgáló integrációs futásidejű géphez a Kerberos-birodalomban

#### <a name="requirements"></a>Követelmények

* A saját üzemeltetésű integrációs futásidejű gépnek csatlakoznia kell a Kerberos tartományhoz, és nem tud csatlakozni egyetlen Windows-tartományhoz sem.

#### <a name="how-to-configure"></a>Hogyan kell beállítani

**Saját üzemeltetésű integrációs futásidejű gépen:**

1.  Futtassa a **Ksetup** segédprogramot a Kerberos KDC-kiszolgáló és a birodalom konfigurálásához.

    A számítógépet munkacsoport tagjaként kell konfigurálni, mivel a Kerberos tartomány eltér a Windows tartománytól. Ez a Kerberos-birodalom beállításával és a KDC-kiszolgáló hozzáadásával érhető el az alábbiak szerint. Cserélje *ki REALM.COM* a saját birodalmába, ha szükséges.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **A** 2 parancs végrehajtása után indítsa újra a számítógépet.

2.  Ellenőrizze a konfigurációt a **Ksetup** paranccsal. A kimenetnek a következőnek kell lennie:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Az Azure Data Factoryban:**

* Konfigurálja a HDFS-összekötőt **a Windows-hitelesítés** használatával a Kerberos egyszerű nevével és jelszavával együtt a HDFS adatforráshoz való csatlakozáshoz. Ellenőrizze [a HDFS csatolt szolgáltatás tulajdonságai](#linked-service-properties) szakaszt a konfigurációs részletekről.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>2. lehetőség: A Windows-tartomány és a Kerberos tartomány közötti kölcsönös bizalom engedélyezése

#### <a name="requirements"></a>Követelmények

*   A saját üzemeltetésű integrációs futásidejű gépnek csatlakoznia kell egy Windows-tartományhoz.
*   A tartományvezérlő beállításainak frissítéséhez engedély szükséges.

#### <a name="how-to-configure"></a>Hogyan kell beállítani

> [!NOTE]
> Cserélje le REALM.COM és AD.COM a következő oktatóanyagban a saját saját tartományára és tartományvezérlőjére, ha szükséges.

**KDC-kiszolgálón:**

1. A KDC-konfiguráció szerkesztése a **krb5.conf** fájlban, hogy a KDC megbízzon a következő konfigurációs sablonra hivatkozó Windows-tartományban. Alapértelmezés szerint a konfiguráció az **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **A** konfigurálás után indítsa újra a KDC szolgáltatást.

2. Készítsen elő egy **\@krbtgt/REALM.COM AD.COM** nevű megbízót a KDC kiszolgálón a következő paranccsal:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. A **hadoop.security.auth_to_local** HDFS szolgáltatás `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`konfigurációs fájljában adja hozzá a t.

**Tartományvezérlőn:**

1.  A következő **Ksetup-parancsok** futtatásával adjon hozzá egy béna bejegyzést:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Bizalmi kapcsolat létrehozása a Windows tartományból a Kerberos-tartományba. [jelszó] a jelszó a fő **\@krbtgt/REALM.COM AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Válassza ki a Kerberos-ban használt titkosítási algoritmust.

    1. Nyissa meg a Kiszolgálókezelő > csoportházirend-kezelés > a tartomány> csoportházirend-objektumok at > alapértelmezett vagy az aktív tartományi házirendet, és szerkessze.

    2. A **Csoportházirend kezelése szerkesztő** előugró ablakban nyissa meg a Számítógép konfigurációs > házirendek > A Windows beállításai > a helyi házirendek > > a biztonsági beállítások ablakot, és konfigurálja a **Hálózati biztonságot: A Kerberos számára engedélyezett titkosítási típusok konfigurálása**.

    3. Válassza ki a kdc-hez való csatlakozáskor használni kívánt titkosítási algoritmust. Általában egyszerűen kiválaszthatja az összes lehetőséget.

        ![Konfigurációs titkosítási típusok a Kerberos hoz](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. A **Ksetup** paranccsal adja meg az adott REALM-en használandó titkosítási algoritmust.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Hozza létre a leképezést a tartományi fiók és a Kerberos egyszerű felhasználó között a Kerberos egyszerű felhasználó használata érdekében a Windows-tartományban.

    1. Indítsa el az **Active Directory – felhasználók és számítógépek beépülő modul**> felügyeleti eszközöket.

    2. A speciális szolgáltatások konfigurálása a**Speciális funkciók** **megtekintése** > gombra kattintva.

    3. Keresse meg azt a fiókot, amelyhez leképezéseket szeretne létrehozni, és kattintson a jobb gombbal a **Név-hozzárendelések** megtekintéséhez > a **Kerberos-nevek** fülre kattintva.

    4. Adjon hozzá egy megbízóakat a bazsúrából.

        ![Biztonsági identitás leképezése](media/connector-hdfs/map-security-identity.png)

**Saját üzemeltetésű integrációs futásidejű gépen:**

* A következő **Ksetup-parancsok** futtatásával adjon hozzá egy béna bejegyzést.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Az Azure Data Factoryban:**

* Konfigurálja a HDFS-összekötőt **a Windows-hitelesítés** használatával a tartományi fiókkal vagy a Kerberos Egyszerű Kerberos-fiókkal együtt a HDFS adatforráshoz való csatlakozáshoz. Ellenőrizze [a HDFS csatolt szolgáltatás tulajdonságai](#linked-service-properties) szakaszt a konfigurációs részletekről.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak, mint a visszamenőleges kompatibilitás. Javasoljuk, hogy a fenti szakaszokban említett új modellt használja, és az ADF szerzői felhasználói felülete áttért az új modell létrehozására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészletmodell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **FileShare** |Igen |
| folderPath | A mappa elérési útja. A helyettesítő karakterek szűrője támogatott, `*` az engedélyezett helyettesítő karakterek `?` a következők: (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a tényleges fájlnévben helyettesítő karakter szerepel, vagy ez az escape karakter van benne. <br/><br/>Példák: rootfolder/subfolder/, további példák a [Mappa- és fájlszűrőben.](#folder-and-file-filter-examples) |Igen |
| fileName |  A megadott "folderPath" alatt található fájl(ok) **neve vagy helyettesítő karaktere.** Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában lévő összes fájlra mutat. <br/><br/>Szűrő esetén az engedélyezett `*` helyettesítő karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg).<br/>- 1. példa:`"fileName": "*.csv"`<br/>- 2. példa:`"fileName": "???20180427.txt"`<br/>Menekülésre használható, `^` ha a tényleges mappanévben helyettesítő karakter szerepel, vagy ez az escape karakter van benne. |Nem |
| modifiedDatetimeStart | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| modifiedDatetimeEnd | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| Formátum | Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha adott formátumú fájlokat szeretne elemezni, a következő fájlformátum-típusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [a Json formátum,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [az Avro formátum,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [az Orc Formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és a [Parkettaformátum](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) című szakaszban talál. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. További információt a [Támogatott fájlformátumok és tömörítési kodekek című témakörben talál.](supported-file-formats-and-compression-codecs-legacy.md#compression-support)<br/>A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**.<br/>Támogatott szintek: **Optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>A mappa összes fájljának másolásához adja meg a csak **mappaPath** parancsot.<br>Egyetlen fájl adott nevű másolásához adja meg a **folderPath** mappát tartalmazó részét, **a fájlnév** pedig a fájlnevet.<br>Ha egy mappába szeretné másolni a fájlok egy részét, adja meg a **folderPath** mapparészlel és **a fileName** helyettesítő karakterrel című elemét.

**Példa:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásmodellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **HdfsSource** |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. Megjegyzés: Ha a rekurzív érték igaz, a fogadó pedig fájlalapú tároló, az üres mappa/almappa nem lesz másolva/hozva a fogadóban.<br/>Az engedélyezett értékek a következők: **true** (alapértelmezett), **hamis** | Nem |
| distcpBeállítások | Tulajdonságcsoport a HDFS DistCp használatakor. | Nem |
| resourceManagerEndpoint | A Fonal erőforrás-kezelő végpontja | Igen, ha a DistCp |
| tempScriptPath | Az ideiglenes DistCp parancsparancsfájl tárolására használt mappaelérési út. A parancsfájlt a Data Factory hozza létre, és a másolási feladat befejezése után törlődik. | Igen, ha a DistCp |
| distcpOptions | A DistCp parancs további lehetőségei. | Nem |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa: HDFS-forrás másolási tevékenységben a DistCp használatával**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
