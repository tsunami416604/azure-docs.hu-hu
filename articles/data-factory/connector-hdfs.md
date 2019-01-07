---
title: Adatok másolása az Azure Data Factory használatával HDFS |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat egy felhőbeli vagy helyszíni HDFS forrás támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: a5df9d4d323158ee52c872b0122fdd28d9f74979
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019860"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával HDFS-ből
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-hdfs-connector.md)
> * [Aktuális verzió](connector-hdfs.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolása a HDFS-ből. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat HDFS bármely támogatott fogadó adattárba. A másolási tevékenység által források/fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

A HDFS-összekötőt, a következőket támogatja:

- Másolás a fájlok **Windows** (Kerberos) vagy **névtelen** hitelesítést.
- Másolás a fájlok **webhdfs** protokoll vagy **beépített DistCp** támogatja.
- Másolja a fájlokat,-fájlok az elemzés/létrehozása, vagy a [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Előfeltételek

Adatokat másol egy HDFS, amely nem érhető el nyilvánosan, akkor be kell állítania egy helyi Integration Runtime. Lásd: [helyi Integration Runtime](concepts-integration-runtime.md) további részleteket a cikkben.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások HDFS-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

HDFS-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Hdfs**. | Igen |
| url |A HDFS URL-címe |Igen |
| authenticationType | Engedélyezett értékek a következők: **Névtelen**, vagy **Windows**. <br><br> Használandó **Kerberos-hitelesítés** HDFS-összekötőhöz, tekintse meg [ebben a szakaszban](#use-kerberos-authentication-for-hdfs-connector) , ennek megfelelően állítsa be a helyszíni környezetet. |Igen |
| Felhasználónév |Felhasználónév a Windows-hitelesítés. A Kerberos-hitelesítéshez, adja meg a `<username>@<domain>.com`. |Igen (a Windows-hitelesítés) |
| jelszó |Windows-hitelesítés jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen (a Windows-hitelesítés) |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa: névtelen hitelesítés használatával**

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

**Példa: Windows-hitelesítés használatával**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a HDFS-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a HDFS-ből, állítsa be a type tulajdonság, az adatkészlet **FileShare**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **Fájlmegosztás** |Igen |
| folderPath | A mappa elérési útját. Helyettesítő karaktert tartalmazó szűrő nem támogatott. Például: mappát vagy almappát / |Igen |
| fileName |  **Név vagy helyettesítő karaktert tartalmazó szűrő** az fájl(ok) a megadott "folderPath" alatt. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>Szűrő esetén engedélyezett a helyettesítő karaktereket: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Használat `^` elkerülésére, ha a fájl tényleges nevét helyettesítő elemet vagy a escape karaktere belül. |Nem |
| Formátum | Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha meg szeretné elemezni az adott formátumú fájlok, formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquetformátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **folderPath** csak.<br>Adja meg a megadott nevű egyetlen fájl másolásához **folderPath** mappára vonatkozó részt a és **fileName** nevére.<br>Másolja a fájlokat egy mappában egy részét, adja meg a **folderPath** mappára vonatkozó részt a és **fileName** helyettesítő szűrővel.

**Példa**

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
            "fileName": "myfile.csv.gz",
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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a HDFS-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hdfs-as-source"></a>HDFS forrásként

Adatok másolása a HDFS-ből, állítsa be a forrás típusaként a másolási tevékenység **HdfsSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **HdfsSource** |Igen |
| a rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. Megjegyzés: Ha a rekurzív értéke igaz, és a fogadó fájlalapú tároló, üres mappa/alárendelt-folder nem lesz másolva vagy hozható létre, a fogadó.<br/>Engedélyezett értékek a következők: **igaz** (alapértelmezett), **false (hamis)** | Nem |
| distcpSettings | A tulajdonságcsoport HDFS a DistCp használata esetén. | Nem |
| resourceManagerEndpoint | A Yarn ResourceManager végpont | Igen, ha a DistCp használata |
| tempScriptPath | Egy mappa elérési útja, tárolja az ideiglenes a DistCp parancs parancsfájlját. A parancsfájl a Data Factory által létrehozott, és másolási feladat befejezése után törlődnek. | Igen, ha a DistCp használata |
| distcpOptions | A DistCp parancs számára biztosított további beállításokat. | Nem |

**Példa: A másolási tevékenység használatával UNLOAD HDFS-forrás**

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

További információ a DistCp használatával hatékonyan a következő szakaszban a HDFS-ből adatmásolás céljából.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Adatok másolása a HDFS-ből a DistCp használatával

[A DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) Hadoop natív parancssori eszköz ehhez elosztott másolja be egy Hadoop-fürtöt. A Distcp parancs futtatása, azt először listázza másolni, hozzon létre több térkép feladatok, a Hadoop-fürt összes fájlt, és a térkép feladatonként bináris másolat fog tenni a fogadó-forrásból.

Másolja a DistCp használata, a fájlok másolása tevékenység támogatási-van az Azure Blob (beleértve a [szakaszos Másolás](copy-activity-performance.md) vagy az Azure Data Lake Store ebben az esetben azt teljes körűen kihasználhatják a fürt power helyett a helyi Integration Runtime futó . Ez különösen akkor, ha a fürt nagyon hatékony biztosít jobb másolási teljesítmény. Az Azure Data Factoryban a konfiguráció alapján, a másolási tevékenység automatikusan hozza létre a distcp parancsot, az elküldés elemre kattintva a Hadoop-fürt és a másolat állapotának figyelése.

### <a name="prerequsites"></a>Prerequsites

Fájlok másolása a DistCp használatával – a HDFS-ből az Azure Blob (beleértve a szakaszos másolás) vagy az Azure Data Lake Store, ellenőrizze, hogy a Hadoop-fürt megfelel az alábbi követelményeknek:

1. A MapReduce és a Yarn-szolgáltatások engedélyezve vannak.
2. Yarn-verzió: 2.5-ös vagy újabb.
3. HDFS-kiszolgálóra a célzott adattár – Azure Blob- vagy Azure Data Lake Store van integrálva:

    - Az Azure Blob fájlrendszer Hadoop 2.7 óta natív módon támogatott. Csak akkor kell Hadoop env config jar elérési útvonalát adja meg.
    - Az Azure Data Lake Store fájlrendszer van csomagolva, Hadoop 3.0.0-alpha1 kezdve. A Hadoop-fürt nem éri el azt a verziót, ha a kapcsolódó ADLS manuálisan importálnia kell a fürtbe való csomagok (az azure-datalake-store.jar) jar [Itt](https://hadoop.apache.org/releases.html), és a Hadoop env config jar elérési útvonalát adja meg.

4. Készítsen elő egy ideiglenes mappába a HDFS-ben. A temp mappa a DistCp héjparancsfájlt, tárolására, így azt által elfoglalt lemezterület KB-os szintű szolgál.
5. Ellenőrizze, hogy a HDFS társított szolgáltatásban megadott felhasználói fiók rendelkezik engedéllyel a) benyújtja az alkalmazást a Yarn; (b) hozzon létre almappát, a fenti temp mappa alatt álló fájlokat olvasási/írási engedéllyel rendelkezik.

### <a name="configurations"></a>Konfigurációk

Alább példaként szolgál a másolási tevékenység konfigurációs másolhat adatokat HDFS-ből Azure-Blobokból a DistCp használatával:

**Példa**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>HDFS-összekötő Kerberos-hitelesítés használata

A helyszíni környezet beállítása úgy, hogy a Kerberos-hitelesítés használatát a HDFS-összekötőben két lehetőség van. Kiválaszthatja, hogy a legjobban az esetet.
* 1. lehetőség: [Helyi integrációs modul gép csatlakozzon a Kerberos-tartomány](#kerberos-join-realm)
* 2. lehetőség: [Windows-tartomány és a Kerberos-tartomány közötti kölcsönös megbízhatósági kapcsolat engedélyezése](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>1. lehetőség: Helyi integrációs modul gép csatlakozzon a Kerberos-tartomány

#### <a name="requirements"></a>Követelmények

* A helyi Integration Runtime gép csatlakozzon a Kerberos-tartomány kell, és bármely Windows-tartomány nem tud csatlakozni.

#### <a name="how-to-configure"></a>Konfigurálása

**Helyi integrációs modul gépen:**

1.  Futtassa a **Ksetup** segédprogram a Kerberos KDC-kiszolgáló és a kezdőtartomány konfigurálása.

    A gép egy munkacsoport tagjaként kell konfigurálni, mivel egy Kerberos-tartomány eltér egy Windows-tartományhoz. Ez a beállítás a Kerberos-tartomány és a KDC-kiszolgáló hozzáadása a következő megvalósítható. Cserélje le *REALM.COM* a saját megfelelő terület szükséges.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Indítsa újra a** 2 parancsok végrehajtása után a gép.

2.  Ellenőrizze a konfigurációt a **Ksetup** parancsot. A kimenet hasonló lesz:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Az Azure Data Factoryban:**

* Konfigurálás a HDFS összekötő használatával **Windows-hitelesítés** együtt a Kerberos egyszerű neve és a jelszót a HDFS-adatforráshoz való kapcsolódáshoz. Ellenőrizze [HDFS társított szolgáltatás Tulajdonságok](#linked-service-properties) konfiguráció részletei című szakaszát.

### <a name="kerberos-mutual-trust"></a>2. lehetőség: Windows-tartomány és a Kerberos-tartomány közötti kölcsönös megbízhatósági kapcsolat engedélyezése

#### <a name="requirements"></a>Követelmények

*   A helyi Integration Runtime gép csatlakoztatnia kell egy Windows-tartományhoz.
*   A tartományvezérlő-beállítások frissítése engedéllyel is kell rendelkeznie.

#### <a name="how-to-configure"></a>Konfigurálása

> [!NOTE]
> Cserélje le REALM.COM és AD.COM a következő oktatóanyagban a saját megfelelő tartomány és a tartományvezérlő igény szerint.

**KDC-kiszolgálón:**

1.  A KDC-konfiguráció szerkesztése **krb5.conf** lehetővé teszik a KDC-fájlt a következő konfigurációs sablon hivatkozó Windows-tartományt. Alapértelmezés szerint a konfiguráció a következő helyen található **/etc/krb5.conf**.

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

  **Indítsa újra a** a KDC-szolgáltatás konfigurálása után.

2.  Készítse elő a nevű egyszerű **krbtgt/REALM.COM@AD.COM** KDC-kiszolgálón a következő paranccsal:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  A **hadoop.security.auth_to_local** HDFS-szolgáltatás konfigurációs fájlt, adja hozzá `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Tartományvezérlő:**

1.  Futtassa a következő **Ksetup** parancsok egy kezdőtartomány-bejegyzés hozzáadásához:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  A Kerberos-tartomány Windows-tartomány megbízhatósági kapcsolatot hoz létre. [jelszó] az a jelszó a rendszerbiztonsági tag **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Válassza ki a Kerberos használt titkosítási algoritmus.

    1. Nyissa meg a Kiszolgálókezelő > csoportházirend-kezelés > tartomány > csoportházirend-objektumok > alapértelmezett vagy az aktív tartományi házirend, és a szerkesztése.

    2. A a **Csoportházirendkezelés-szerkesztő** előugró ablakban nyissa meg a számítógép konfigurációja > házirendek > Windows-beállítások > biztonsági beállítások > helyi házirend > biztonsági beállítások, és konfigurálása **hálózati biztonság: A Kerberos használható titkosítási típusok konfigurálása**.

    3. Válassza ki a titkosítási algoritmus szeretne használni, amikor szeretne csatlakozni a KDC. Gyakran egyszerűen választhatja lehetőségekről.

        ![A Kerberos konfiguráció titkosítási típusok](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Használat **Ksetup** parancs használatával adja meg a titkosítási algoritmus az adott tartomány esetében használható.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Hozzon létre a tartományi fiók és a Kerberos egyszerű közötti leképezést, és a Kerberos egyszerű használata Windows-tartományban.

    1. Indítsa el a felügyeleti eszközök > **Active Directory – felhasználók és számítógépek**.

    2. A speciális szolgáltatások konfigurálása kattintva **nézet** > **speciális szolgáltatások**.

    3. Keresse meg a fiókot, amelyhez létre szeretné hozni a leképezéseket, és a jobb gombbal kattintva **a felhasználónév-leképezések** > kattintson **Kerberos-nevek** fülre.

    4. Rendszerbiztonsági tag hozzáadása a tartományt.

        ![Térkép biztonsági identitás](media/connector-hdfs/map-security-identity.png)

**Helyi integrációs modul gépen:**

* Futtassa a következő **Ksetup** parancsok egy kezdőtartomány-bejegyzés hozzáadására.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Az Azure Data Factoryban:**

* Konfigurálás a HDFS összekötő használatával **Windows-hitelesítés** együtt a tartományi fiók vagy a Kerberos egyszerű a HDFS-adatforráshoz való kapcsolódáshoz. Ellenőrizze [HDFS társított szolgáltatás Tulajdonságok](#linked-service-properties) konfiguráció részletei című szakaszát.


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
