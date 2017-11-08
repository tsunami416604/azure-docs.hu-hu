---
title: "Adatok másolása az Azure Data Factory használatával HDFS |} Microsoft Docs"
description: "Útmutató: adatok másolása egy felhőalapú és helyszíni HDFS forrás támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: jingwang
ms.openlocfilehash: a81d3264964b2433a2c93034ab815493548f0753
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-and-to-hdfs-using-azure-data-factory"></a>Másolja az adatokat, a kezdő és a HDFS Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-hdfs-connector.md)
> * [2. verzió – Előzetes verzió](connector-hdfs.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, a kezdő és a HDFS. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [HDFS-összekötőt a V1](v1/data-factory-hdfs-connector.md).


## <a name="supported-capabilities"></a>Támogatott képességei

HDFS adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott adatforrások/mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a HDFS összekötő támogatja:

- Fájlokat másol használatával **Windows** (Kerberos) vagy **névtelen** hitelesítés.
- Fájlokat másol használatával **webhdfs** protokoll vagy **beépített ból a DistCp** támogatja.
- Mint a fájlok másolása-fájlok elemzése/létrehozásakor, vagy a [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Előfeltételek

Az adatok másolása az vagy egy HDFS, amely nincs nyilvánosan elérhető, akkor be kell állítania egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](concepts-integration-runtime.md) cikk további részleteket.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják való HDFS adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok HDFS kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Hdfs**. | Igen |
| URL-címe |A HDFS URL-címe |Igen |
| AuthenticationType | Két érték engedélyezett: **névtelen**, vagy **Windows**. <br><br> Használandó **Kerberos-hitelesítés** HDFS-összekötőhöz, tekintse meg [ebben a szakaszban](#use-kerberos-authentication-for-hdfs-connector) ennek megfelelően a helyszíni környezet beállítása. |Igen |
| Felhasználónév |Felhasználónév a Windows-hitelesítést. A Kerberos-hitelesítés meg `<username>@<domain>.com`. |Igen (a Windows-hitelesítés) |
| jelszó |A Windows-hitelesítés jelszót. Ez a mező megjelölése SecureString. |Igen (a Windows-hitelesítés) |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa: a névtelen hitelesítést használó**

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

**Példa: a Windows-hitelesítés használatával**

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz a HDFS dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása HDFS, az adatkészlet típus tulajdonságának beállítása **fájlmegosztási**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **fájlmegosztás** |Igen |
| folderPath | A mappa elérési útját. Például: mappát vagy almappát / |Igen |
| fileName | Adja meg a fájl nevét a **folderPath** Ha át kívánja másolni a megadott fájlt. Ha nem ad meg ehhez a tulajdonsághoz értéket, a DataSet adatkészlet mutat a mappában lévő összes fájl forrásaként. |Nem |
| Formátumban | Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha szeretne elemezni egy adott formátumú fájlok, a következő formátumban típusú támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [az Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszok. |Nem (csak a bináris másolásának esetéhez) |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. További információkért lásd: [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimal** és **leggyorsabb**. |Nem |

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a HDFS-forrás és a fogadó által támogatott tulajdonságokról listáját tartalmazza.

### <a name="hdfs-as-source"></a>HDFS forrásaként

Adatok másolása HDFS, állítsa be a forrás típusa a másolási tevékenység **HdfsSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **HdfsSource** |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát.<br/>Két érték engedélyezett: **igaz** (alapértelmezett), **hamis** | Nem |
| distcpSettings | A tulajdonságcsoport HDFS ból a DistCp használatakor. | Nem |
| resourceManagerEndpoint | A Yarn erőforrás-kezelő végpont | Igen, ha ból a DistCp használatával |
| tempScriptPath | Egy ideiglenes ból a DistCp parancsfájl tárolására használt mappa elérési útja A parancsfájl az ADF elő és másolási feladat befejezése után törlődnek. | Igen, ha ból a DistCp használatával |
| distcpOptions | További beállítások ból a DistCp parancs megadott. | Nem |

**Példa: HDFS forrás a másolási tevékenység UNLOAD használatával**

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

További adatok másolása a következő szakaszban hatékonyan a HDFS ból a DistCp használatával.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Adatok másolása HDFS ból a DistCp segítségével

[Ból a DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) Hadoop natív parancssori eszköz a Hadoop-fürt elosztott lemásolni. Egy ból a Distcp parancs futtatása azt először felsorolja a összes fájlt másolja, és hozzon létre több leképezés feladatok a Hadoop-fürthöz, és minden egyes térkép feladat fog lemásolni bináris gyűjtése forrásból.

Tevékenység támogatás ból a DistCp használatával másolja a fájlokat másolja-van az Azure Blob (beleértve a [másolási előkészített](copy-activity-performance.md) vagy az Azure Data Lake Store, ebben az esetben azt teljes mértékben kihasználhatják a Self-hosted integrációs Runtime futtatása helyett a fürt power . Jobb másolási átviteli azt biztosítja, különösen akkor, ha a fürt nagyon hatékonyak. Az Azure Data Factory a konfigurációtól függően másolási tevékenység automatikusan ból a distcp parancs összeállításához, küldje el a Hadoop-fürthöz és a másolat állapotának figyelése.

### <a name="prerequsites"></a>Prerequsites

Fájlok másolása ból a DistCp használandó-van az Azure Blob (beleértve az előkészített másolatot) vagy az Azure Data Lake Store HDFS, ellenőrizze, hogy a Hadoop-fürt megfelel az alábbi követelményeknek:

1. MapReduce és a Yarn szolgáltatás be legyen kapcsolva.
2. Yarn verziója 2.5-ös vagy újabb.
3. HDFS server integrálva van a cél adattár - Azure Blob vagy az Azure Data Lake Store:

    - Az Azure Blob-fájlrendszer natívan támogatott Hadoop 2.7 óta. Csak meg kell adnia a jar-elérési út a Hadoop env konfigurációban.
    - Azure Data Lake Store-fájlrendszer csomagolt Hadoop 3.0.0-alpha1 kezdve. A Hadoop-fürt nem éri el, hogy verziót, ha a kapcsolódó ADLS manuálisan importálnia kell jar csomagok (azure-datalake-store.jar) a fürtbe [Itt](https://hadoop.apache.org/releases.html), és a Hadoop env config jar elérési útvonalát adja meg.

4. Készítsen elő egy ideiglenes mappa HDFS-ben. Az ideiglenes mappa ból a DistCp héjparancsfájlt, tárolására, így azt a KB szintű hely elhelyezkedése szolgál.
5. Győződjön meg arról, hogy a HDFS hivatkozott szolgáltatásban található a megadott felhasználói fiók rendelkezik engedéllyel a) küldje el az alkalmazás a Yarn; b) hozzon létre almappát, a fájlokat az ideiglenes mappa felett olvasási/írási engedéllyel rendelkezik.

### <a name="configurations"></a>Konfigurációk

Alább példája másolási tevékenység konfigurációs adatok másolása HDFS Azure Blob-ból a DistCp használatával:

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>A HDFS-összekötő Kerberos-hitelesítés használata

A helyszíni környezet beállítása úgy, hogy a Kerberos-hitelesítés használatát a HDFS connector két lehetőség áll rendelkezésre. Kiválaszthatja a legjobban az esethez.
* 1. lehetőség: [illesztési Self-hosted integrációs futásidejű gépről a Kerberos-tartomány](#kerberos-join-realm)
* 2. lehetőség: [kölcsönös, a Windows-tartomány és a Kerberos-tartomány közötti megbízhatósági kapcsolat engedélyezése](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>1. lehetőség: Self-hosted integrációs futásidejű számítógépet csatlakoztatni a Kerberos-tartomány

#### <a name="requirements"></a>Követelmények

* A Self-hosted integrációs futásidejű gép kell a Kerberos-tartomány csatlakozni, és nem tud csatlakozni a Windows-tartományhoz.

#### <a name="how-to-configure"></a>Konfigurálása

**Self-hosted integrációs futásidejű gépen:**

1.  Futtassa a **Ksetup** segédprogram a Kerberos Kulcsszolgáltató kiszolgáló és a tartomány beállításához.

    A számítógép egy munkacsoport tagjaként kell konfigurálni óta egy Kerberos-tartomány eltér a Windows-tartományhoz. Ez a Kerberos-tartomány és a KDC-kiszolgáló hozzáadása az alábbiak szerint elérhető. Cserélje le *REALM.COM* a saját megfelelő terület szükséges.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Indítsa újra a** 2 parancsok végrehajtása után a gép.

2.  A konfiguráció ellenőrzése a **Ksetup** parancsot. A kimeneti kell lennie, például:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Az Azure Data Factoryben:**

* Konfigurálhatja a HDFS összekötő segítségével **Windows-hitelesítés** együtt a Kerberos egyszerű neve és a jelszót a HDFS-adatforráshoz való kapcsolódáshoz. Ellenőrizze [HDFS társított szolgáltatás Tulajdonságok](#linked-service-properties) konfiguráció részletei című szakaszban.

### <a name="kerberos-mutual-trust"></a>2. lehetőség: A Windows-tartomány és a Kerberos-tartomány közötti kölcsönös megbízhatósági engedélyezése

#### <a name="requirements"></a>Követelmények

*   A Self-hosted integrációs futásidejű gép Windows-tartományhoz kell csatlakoztatni.
*   A tartományvezérlő-beállítások frissítése engedéllyel kell rendelkezni.

#### <a name="how-to-configure"></a>Konfigurálása

> [!NOTE]
> Cserélje le REALM.COM és AD.COM az alábbi oktatóanyag saját megfelelő tartomány és a tartományvezérlő igény szerint.

**KDC-kiszolgálón:**

1.  A KDC konfigurációjának szerkesztése **krb5.conf** fájlt, hogy a KDC se hivatkozzon a következő konfigurációs Windows-tartományt. Alapértelmezés szerint a konfigurációja itt található: **/etc/krb5.conf**.

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

2.  Készítse elő a rendszerbiztonsági tag nevű  **krbtgt/REALM.COM@AD.COM**  a KDC-kiszolgáló a következő paranccsal:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  A **hadoop.security.auth_to_local** HDFS-szolgáltatás konfigurációs fájlt, adja hozzá `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**A tartományvezérlőn:**

1.  Futtassa a következő **Ksetup** parancsok tartomány bejegyzés hozzáadása:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Windows-tartomány Kerberos-tartomány bizalmi kapcsolat létrehozásához. [jelszó] pedig a jelszót a rendszerbiztonsági tag  **krbtgt/REALM.COM@AD.COM** .

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Válassza ki a Kerberos használt titkosítási algoritmus.

    1. Nyissa meg a Kiszolgálókezelő > csoportházirend-kezelés > tartomány > csoportházirend-objektumok > alapértelmezett vagy az aktív tartományi házirend és a Szerkesztés.

    2. A a **Csoportházirendkezelés-szerkesztő** előugró ablakban, keresse fel a számítógép konfigurációja > házirendek > Windows-beállítások > biztonsági beállítások > helyi házirend > biztonsági beállítások, és konfigurálja **hálózati biztonság: konfigurálása titkosítási típusok engedélyezett Kerberos**.

    3. Válassza ki a titkosítási algoritmust szeretné használni, ha a KDC csatlakozni. Gyakran egyszerűen kiválaszthatja a beállításokat.

        ![A Kerberos config titkosítási típusok](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Használjon **Ksetup** parancs használatával adja meg a titkosítási algoritmus az adott tartomány kell használni.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  A tartományi fiókot és a Kerberos egyszerű közötti leképezést létrehozása a Windows-tartomány Kerberos egyszerű használatához.

    1. Indítsa el a felügyeleti eszközök > **Active Directory – felhasználók és számítógépek**.

    2. Kattintson a speciális szolgáltatások konfigurálása **nézet** > **speciális funkciók**.

    3. Keresse meg a fiókot, amelyhez hozzárendelések létrehozásához, és a jobb gombbal kattintva **a felhasználónév-leképezések** > kattintson **Kerberos-nevek** fülre.

    4. Adja hozzá a tartományi rendszerbiztonsági tag.

        ![Térkép biztonsági azonosító](media/connector-hdfs/map-security-identity.png)

**Self-hosted integrációs futásidejű gépen:**

* Futtassa a következő **Ksetup** parancs használatával adja hozzá egy tartományi bejegyzést.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Az Azure Data Factoryben:**

* Konfigurálhatja a HDFS összekötő segítségével **Windows-hitelesítés** együtt tartományi fiók vagy a Kerberos egyszerű a HDFS-adatforráshoz való kapcsolódáshoz. Ellenőrizze [HDFS társított szolgáltatás Tulajdonságok](#linked-service-properties) konfiguráció részletei című szakaszban.


## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).