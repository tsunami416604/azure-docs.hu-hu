---
title: "Futtassa az Azure Batch feladatok-végpont (előzetes verzió) kód írása nélkül |} Microsoft Docs"
description: "Hozzon létre sablonfájlokat a kötegelt készletek, feladatok és feladatok létrehozása az Azure parancssori felület."
services: batch
author: mscurrell
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 10/17/2017
ms.author: markscu
ms.openlocfilehash: 87ec0e1b6d01fc5d13e9b9f46987e416d8e1958f
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Az Azure Batch parancssori felületi sablonjainak és fájlátviteli funkciójának (előzetes verzió) használata

Az Azure parancssori felülettel is lehet kötegelt feladatok futtatása kód írása nélkül.

Létrehozhat és használhat a sablonfájlokat importálni az Azure parancssori felülettel kötegelt készletek, a feladatok és a feladatok létrehozásához. Feladat bemeneti fájlok könnyen feltölthető a tárfiókot, a fiók és a feladat kimenete parancsfájlokat letöltött társított.

## <a name="overview"></a>Áttekintés

Az Azure parancssori felület bővítménye lehetővé teszi, hogy a kötegben használt-végpont nem fejlesztők felhasználók kell. Készlet hozhatók létre, bemeneti adatokat feltölteni, feladatok és a kapcsolódó feladatok létrehozása, és az eredményül kapott kimeneti adatok letöltése – nincs kód szükséges, a parancssori felület közvetlenül használja, vagy parancsfájlok történő integrálását.

Kötegelt sablonok létrehozása a kiszolgálón a [meglévő kötegelt támogatást az Azure parancssori felület](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation) lehetővé teszi a JSON-fájlok tulajdonságértékek készletek, feladatok, feladatok és más elemek létrehozásához adja meg. Batch-sablonok az alábbi képességeket tesz lehetővé a JSON-fájlok Újdonságok keresztül lettek hozzáadva:

-   Paraméterekkel definiálható. Ha a sablont használ, csak a paraméter értékek megadva a konfigurációelem létrehozása az egyéb elemhez a sablon törzsében megadott tulajdonságok értékeit. A felhasználó, aki együttműködik a kötegelt és a köteg által futtatandó alkalmazásokat hozhatnak létre, készlet, feladat és egyéb tevékenység tulajdonságértékeket. A felhasználó kevesebb ismerős kötegelt és/vagy az alkalmazásokat csak nem adja meg a definiált paraméterek értékeit.

-   Feladat feladat hozzanak létre egy feladatot, így nem kell létrehozni sok feladat-definíciókat, és jelentősen egyszerűsítse a feladat elküldése társított egy vagy több feladat.


Adjon meg kell adni a feladatokat az adatfájlok és a kimeneti adatok fájlok gyakran. A storage-fiók hozzá van rendelve, alapértelmezés szerint az egyes Batch-fiók és fájlok könnyen átvihetők, és ezt a tárfiókot nem kódolási a CLI használata az és tárolás szükséges hitelesítő adatok nem szükséges.

Például [ffmpeg](http://ffmpeg.org/) népszerű alkalmazás, amely feldolgozza a hang- és fájlokat. Az Azure Batch parancssori felület segítségével átkódolására videó forrásfájlokra mutató eltérő a megoldásuk ffmpeg meghívni.

-   Egy készlet sablon jön létre. A felhasználó a sablonok létrehozásának tudja hogyan hívhatja meg a ffmpeg alkalmazás és a követelmények vonatkoznak; akkor adja meg a megfelelő operációs rendszer, a virtuális gép mérete, hogyan ffmpeg telepítve (az alkalmazás csomagot vagy a Csomagkezelő például használata), és más tárolókészlet tulajdonság értékével. Paraméterek jönnek létre, ha a sablont használ, csak az alkalmazáskészlet azonosítója és a virtuális gépek száma kell megadni.

-   Egy feladat sablon jön létre. A felhasználó a sablonok létrehozásának tudja, hogyan ffmpeg átkódolására forrás egy másik megoldás videó meg kell, és adja meg a feladat parancssori; tudják is, hogy nincs-e a mappát, amely tartalmazza a forrás videofájlok lejátszását, a bemeneti fájl szükséges feladatokkal.

-   Nem alakítható át videofájlok állítja be a felhasználó először hoz létre egy készletet, a készlet sablonnal, csak az alkalmazáskészlet azonosítója és egyéb szükséges virtuális gépek száma. A forrásfájlok azok majd feltölteni, nem alakítható át. Egy feladat majd küldheti el a feladat sablont használja, csak az alkalmazáskészlet azonosítója és a feltöltött forrásfájljainak helyét. A kötegelt hozza létre, egy feladathoz egy bemeneti fájl létrehozása folyamatban. Végezetül engedélyezi az átalakítását a fájlok letöltési lehet.

## <a name="installation"></a>Telepítés

A sablon és a fájl átviteli lehetőségeket bővítménye, telepíteni kell.

További információ az Azure parancssori felület telepítésével [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

Az Azure parancssori felület telepítése után a kötegelt bővítmény legújabb verziója is telepíthető a következő parancssori parancsot:

```azurecli
az extension add --source https://github.com/Azure/azure-batch-cli-extensions/releases/download/azure-batch-cli-extensions-2.0.0/azure_batch_cli_extensions-2.0.0-py2.py3-none-any.whl
```

A kötegelt bővítmény kapcsolatos további információkért lásd: [Microsoft Azure Batch CLI Extensions for Windows, Mac és Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux).

## <a name="templates"></a>Sablonok

Az Azure Batch parancssori felület lehetővé teszi, hogy az elemek, például készletek, feladatok és létrehozni egy JSON-fájl nevét és értékeit tartalmazó megadásával tevékenységeket. Példa:

```azurecli
az batch pool create –-json-file AppPool.json
```

Azure Batch-sablonok hasonlóak Azure Resource Manager-sablonok, funkcióit és szintaxist. Azok az elemek nevét és értékeit tartalmazza, de vegye fel a következő fő alapelv JSON-fájlok:

-   **Paraméterek**

    -   Engedélyezi a adható meg a szervezet szakasz csak kell megadni, ha a sablont paraméterértékekkel tulajdonság értékével. Például egy alkalmazáskészlet teljes definíciója sikerült elhelyezni a törzs és az alkalmazáskészlet azonosítója; meghatározott csak egy paraméter csak egy alkalmazáskészlet azonosító karakterláncot ezért meg kell adni egy készlet létrehozása.
        
    -   A sablon törzsében hozhatóak létre valaki ismeretében kötegelt és az alkalmazások futtatását, kötegelt; csak a szerző által megadott paraméterek értékét meg kell adni, ha a sablont használ. A részletes kötegelt és/vagy az alkalmazás ismeretek nélkül is ezért használni a sablonokat.

-   **Változók**

    -   Lehetővé teszi egy helyen vannak megadva, és a sablon szervezet egy vagy több helyen használt egyszerű vagy összetett paraméterértékeket. Változók is egyszerűbbé és a sablon méretének csökkentése, valamint azzal, hogy, amelynek az értéke módosíthatja tulajdonságait módosítani szeretné egy helyen több fenntarthatóvá ellenőrizze.

-   **Magasabb szintű szerkezeteket**

    -   Néhány magasabb szintű szerkezeteket a sablonban, amelyek még nem érhető el a kötegelt API-k érhetők el. Például egy feladat gyári adható meg a feladat-sablont, amely használatával a közös feladatdefiníció feladat több feladatot hoz létre. A fentiek kelljen dinamikusan hozzon létre több JSON-fájlt, például egy fájl feladat, valamint a parancsfájl-fájlok létrehozása kód például telepíthet alkalmazásokat a Csomagkezelő keresztül.

    -   Bármikor ha alkalmazható, a fentiek lehet hozzáadott, a Batch szolgáltatás, és elérhető legyen a kötegelt API-k, UI, stb.

### <a name="pool-templates"></a>Alkalmazáskészlet-sablonok

Paraméterek és változók standard sablon képességek mellett a következő magasabb szintű szerkezeteket a készlet sablon támogatja:

-   **Csomaghivatkozásokhoz**

    -   Opcionálisan lehetővé teszi a szoftverfrissítési csomag kezelők használatával készlet csomópontok átmásolni. A Csomagkezelő és Csomagazonosító meg van adva. Egy vagy több csomagot kiküszöböli, hogy hozzon létre olyan parancsfájlt, amely lekérdezi a szükséges csomagokat deklarálható igényt, a parancsfájl telepítse, és futtassa a parancsfájlt minden alkalmazáskészlet-csomóponton.

A következő egy példa a sablont, amely ffmpeg hoz létre egy Linux virtuális gépek készletét telepítve, és csak egy alkalmazáskészlet-azonosító karakterláncot és a használatához meg kell adni a virtuális gépek száma igényel:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04.0-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Ha a sablonfájl nevet kapta _alkalmazáskészlet-ffmpeg.json_, majd a sablon volna hívható meg az alábbiak szerint:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Feladatsablonok

Paraméterek és változók standard sablon képességek mellett a következő magasabb szintű szerkezeteket a feladat sablon támogatja:

-   **A feladat gyári**

    -   Egy feladat meghatározása a feladat több feladat jön létre. Három típusú feladatot gyári támogatja – paraméteres sweep, feladathoz egy fájlt, és tevékenység-gyűjtemény.

A következő egy példa a sablont, amely létrehoz egy feladatot, amely ffmpeg átkódolására MP4 videofájlok egy két alsó megoldások, a szolgáltatással létrehozott forrás videó fájlonként egy tevékenység:

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Ha a sablonfájl nevet kapta _feladat-ffmpeg.json_, majd a sablon volna hívható meg az alábbiak szerint:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Fájlcsoportok és fájlátvitel

A legtöbb feladatot és feladatok esetében a bemeneti fájlokat, és a kimeneti fájlok előállításához. Mind a bemeneti fájlokból, és a kimeneti fájlok általában továbbítani kell, vagy az ügyfél és a csomópont, vagy az ügyfél az csomópontból. Az Azure Batch CLI kiegészítő kötelező fájlátvitel kivonatolja, és használja a storage-fiók, amely alapértelmezés szerint minden Batch-fiók jön létre.

Fájlcsoport olyan tároló, amely az Azure storage-fiók létrehozása megfelel. A fájl csoport almappákat is.

A kötegelt CLI bővítmény parancsok biztosít a megadott fájlcsoport ügyfélről feltöltése fájlokat és a fájlok letöltésére a megadott fájl csoportból ügyfél számára.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Készlet és a feladat sablonok fájlcsoportok másolási készlet csomópontok vagy készlet csomópontok vissza a fájlcsoport ki kell adni a tárolt engedélyezése. Például a feladat-sablonban a korábban megadott, a fájl csoport "ffmpeg-bevitel" van megadva a feladat a beépített az átkódolás; csomópont másolható videó forrásfájljainak helye a hely, ahol a engedélyezi az átalakítását kimeneti fájlok kerülnek a csomópont minden feladatot a fájl csoport "ffmpeg-kimeneti" lesz.

## <a name="summary"></a>Összefoglalás

Sablon és a fájl adatátviteli támogatása jelenleg hozzáadott csak az Azure parancssori felület. A cél, hogy a célközönséget, hogy a felhasználók számára, akik nem kívánnak által fejlesztett kód megadása a kötegelt API-k, például a kutatók informatikai felhasználók, és így tovább használhatja a kötegelt bontsa ki. Nélkül kódolási, az Azure Batch és a köteg által futtatandó alkalmazásokat ismeretét rendelkező felhasználók hozhatnak létre a készlet és a feladat létrehozásához. Sablon paraméterek a felhasználók kötegelt és az alkalmazások részletes ismerete nélkül használhatják a sablonok.

A kötegelt bővítmény kipróbálhatja az Azure parancssori felület, és meg kell adnia a visszajelzést vagy a javaslatok, vagy a megjegyzések, ez a cikk vagy keresztül a [Azure Batch fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>Következő lépések

- A kötegelt sablonok blogbejegyzésben található: [Azure Batch futó feladatok az Azure parancssori felülettel – nem szükséges kód](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Részletes telepítési és használati dokumentációt, példák és forráskód érhetők el a [Azure GitHub-tárházban](https://github.com/Azure/azure-batch-cli-extensions).
