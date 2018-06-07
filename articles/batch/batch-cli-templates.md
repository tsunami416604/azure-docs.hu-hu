---
title: Futtassa az Azure Batch feladatok-végpont (előzetes verzió) kód írása nélkül |} Microsoft Docs
description: Hozzon létre sablonfájlokat a kötegelt készletek, feladatok és feladatok létrehozása az Azure parancssori felület.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/18/2017
ms.author: markscu
ms.openlocfilehash: 565ceb179d8cf749842bb58ab25a8b3d946efa12
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608647"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Az Azure Batch parancssori felületi sablonjainak és fájlátviteli funkciójának (előzetes verzió) használata

Az Azure parancssori felülettel is lehet kötegelt feladatok futtatása kód írása nélkül.

Létrehozhat és használhat a sablonfájlokat importálni az Azure parancssori felülettel kötegelt készletek, a feladatok és a feladatok létrehozásához. Könnyen feltöltési feladat bemeneti fájlok a tárfiókhoz a Batch-fiók és társított letöltési feladat kimeneti fájlokat.

## <a name="overview"></a>Áttekintés

Az Azure parancssori felület bővítménye lehetővé teszi, hogy a kötegben használt-végpont nem fejlesztők felhasználók kell. Csak a parancssori felület parancsait, a készlet létrehozása, bemeneti adatokat feltölteni, feladatok és a kapcsolódó feladatok létrehozása és töltse le az eredményül kapott adatokat. Nincs további kód megadása kötelező. Közvetlenül a parancssori parancsokat, vagy azokat a parancsfájlok integrálja.

Kötegelt sablonok létrehozása a kiszolgálón a [meglévő kötegelt támogatást az Azure parancssori felület](batch-cli-get-started.md#json-files-for-resource-creation) JSON-fájlok készletek, feladatok, feladatok és más elemek létrehozásakor tulajdonság értéket is. Batch-sablonok az alábbi képességeket tesz lehetővé a JSON-fájlok Újdonságok keresztül lettek hozzáadva:

-   Paraméterekkel definiálható. Ha a sablont használ, csak a paraméter értékek megadva a konfigurációelem létrehozása az egyéb elemhez a sablon törzsében megadott tulajdonságok értékeit. A felhasználó, aki együttműködik a kötegelt és a köteg által futtatandó alkalmazásokat hozhatnak létre, készlet, feladat és egyéb tevékenység tulajdonságértékeket. A felhasználó kevesebb ismerős kötegelt és/vagy az alkalmazásokat csak nem adja meg a definiált paraméterek értékeit.

-   Feladat feladat hozzanak létre egy feladatot, így nem kell létrehozni sok feladat-definíciókat, és jelentősen egyszerűsítse a feladat elküldése társított egy vagy több feladat.


Feladatok általában bemeneti adatok fájlokat használ, és kimeneti adatok fájlok előállításához. A storage-fiók hozzá van rendelve, alapértelmezés szerint az egyes Batch-fiókhoz. Ez a tárfiók a CLI használata nem kódolási és a szükséges hitelesítő adatok nem tárolási érkező vagy oda irányuló fájlok átvitele.

Például [ffmpeg](http://ffmpeg.org/) népszerű alkalmazás, amely feldolgozza a hang- és fájlokat. Az alábbiakban az Azure Batch CLI meghívni ffmpeg átkódolására videó forrásfájlokra mutató eltérő a megoldásuk a lépéseket.

-   A készlet sablont létrehozni. A felhasználó a sablonok létrehozásának tudja hogyan hívhatja meg a ffmpeg alkalmazás és a követelmények vonatkoznak; akkor adja meg a megfelelő operációs rendszer, a virtuális gép mérete, hogyan ffmpeg telepítve (az alkalmazás csomagot vagy a Csomagkezelő például használata), és más tárolókészlet tulajdonság értékével. Paraméterek jönnek létre, ha a sablont használ, csak az alkalmazáskészlet azonosítója és a virtuális gépek száma kell megadni.

-   A feladat-sablont létrehozni. A felhasználó a sablonok létrehozásának tudja, hogyan ffmpeg átkódolására forrás egy másik megoldás videó meg kell, és adja meg a feladat parancssori; tudják is, hogy nincs-e a mappát, amely tartalmazza a forrás videofájlok lejátszását, a bemeneti fájl szükséges feladatokkal.

-   Nem alakítható át videofájlok állítja be a felhasználó először hoz létre egy készletet, a készlet sablonnal, csak az alkalmazáskészlet azonosítója és egyéb szükséges virtuális gépek száma. A forrásfájlok azok majd feltölteni, nem alakítható át. Egy feladat majd küldheti el a feladat sablont használja, csak az alkalmazáskészlet azonosítója és a feltöltött forrásfájljainak helyét. A kötegelt hozza létre, egy feladathoz egy bemeneti fájl létrehozása folyamatban. Végezetül a engedélyezi az átalakítását kimeneti fájlok tölthető le.

## <a name="installation"></a>Telepítés

Telepítse az Azure Batch CLI bővítménye, használhatja a sablont, és átviteli lehetőségeket fájl.

Az Azure parancssori felület telepítése, lásd: [Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

Az Azure parancssori felület telepítése után telepítse a legújabb verzióját a kötegelt bővítmény a következő parancssori parancsot:

```azurecli
az extension add --name azure-batch-cli-extensions
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

    -   Lehetővé teszi egy helyen vannak megadva, és a sablon szervezet egy vagy több helyen használt egyszerű vagy összetett paraméterértékeket. Változók is egyszerűbbé és a sablon méretének csökkentése, valamint teszik több fenntarthatóvá azzal, hogy egy hely tulajdonságainak módosításához.

-   **Magasabb szintű szerkezeteket**

    -   Néhány magasabb szintű szerkezeteket a sablonban, amelyek még nem érhető el a kötegelt API-k érhetők el. Például egy feladat gyári adható meg a feladat-sablont, amely használatával a közös feladatdefiníció feladat több feladatot hoz létre. A fentiek kelljen kód dinamikusan hozzon létre több JSON-fájlt, például egy fájl feladat, valamint a parancsfájl-fájlok létrehozása a Csomagkezelő keresztül alkalmazásokat telepíteni.

    -   Egy bizonyos ponton a fentiek lehet hozzáadott, a Batch szolgáltatás, és elérhető legyen a kötegelt API-k, UI, stb.

### <a name="pool-templates"></a>Alkalmazáskészlet-sablonok

Készlet Sablonok támogatja a paraméterek és változók standard sablon képességeit. A következő magasabb szintű szerkezet is támogatják:

-   **Csomaghivatkozásokhoz**

    -   Opcionálisan lehetővé teszi a szoftverfrissítési csomag kezelők használatával készlet csomópontok átmásolni. A Csomagkezelő és Csomagazonosító meg van adva. Is deklarálni kell egy vagy több csomagot, elkerülheti a létrehozása olyan parancsfájlt, amely lekérdezi a szükséges csomagokat, a parancsfájl telepítése és a parancsfájl futtatása minden alkalmazáskészlet-csomóponton.

A következő egy példa a sablont, amely hoz létre a Linux virtuális gépek készletét ffmpeg telepítve. A használatához adja meg a csak egy alkalmazáskészlet azonosító karakterláncot és a készletben található virtuális gépek száma:

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
                    "sku": "16.04-LTS",
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

Ha a sablonfájl nevet kapta _alkalmazáskészlet-ffmpeg.json_, majd meghívása az a sablon az alábbiak szerint:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Feladatsablonok

Feladatsablonok támogatja a paraméterek és változók standard sablon képességeit. A következő magasabb szintű szerkezet is támogatják:

-   **A feladat gyári**

    -   Egy feladat meghatározása a feladat több feladat jön létre. Három típusú feladatot gyári támogatja – paraméteres sweep, feladathoz egy fájlt, és tevékenység-gyűjtemény.

A sablont, amely két alsó megoldások egyikét ffmpeg hoz létre egy feladatot, amely azért MP4 videofájlok példát a következő: Létrehoz egy feladat minden forrás videó fájlban:

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

Ha a sablonfájl nevet kapta _feladat-ffmpeg.json_, majd meghívása az a sablon az alábbiak szerint:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Fájlcsoportok és fájlátvitel

A legtöbb feladatot és feladatok esetében a bemeneti fájlokat, és a kimeneti fájlok előállításához. Általában bemeneti és kimeneti fájlok kerülnek, az ügyfél és a csomópont vagy a csomópont az ügyfélnek. Az Azure Batch CLI kiegészítő kötelező fájlátvitel kivonatolja, és használja a storage-fiók, amely alapértelmezés szerint minden Batch-fiók jön létre.

Fájlcsoport olyan tároló, amely az Azure storage-fiók létrehozása megfelel. A fájl csoport almappákat is.

A kötegelt CLI bővítmény biztosít a fájlok az ügyfél a megadott fájl csoporthoz fájlok feltöltését és letöltését a megadott fájl csoportból ügyfélnek parancsokat.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Készlet és a feladat sablonok fájlcsoportok másolási készlet csomópontok vagy készlet csomópontok vissza a fájlcsoport ki kell adni a tárolt engedélyezése. Például a feladat-sablonban a korábban megadott, a fájl csoport "ffmpeg-bevitel" van megadva a feladat a beépített az átkódolás; csomópont másolható videó forrásfájljainak helye a fájl csoport "ffmpeg-" eredménye a helyet, ahol a engedélyezi az átalakítását kimeneti fájlok kerülnek a csomópont minden feladat futtatásakor.

## <a name="summary"></a>Összegzés

Sablon és a fájl adatátviteli támogatása jelenleg hozzáadott csak az Azure parancssori felület. A cél, hogy a célközönséget, hogy a felhasználók számára, akik nem kívánnak által fejlesztett kód megadása a kötegelt API-k, például a kutatók informatikai felhasználók, és így tovább használhatja a kötegelt bontsa ki. Nélkül kódolási, az Azure Batch és a köteg által futtatandó alkalmazásokat ismeretét rendelkező felhasználók hozhatnak létre a készlet és a feladat létrehozásához. Sablon paraméterek a felhasználók kötegelt és az alkalmazások részletes ismerete nélkül használhatják a sablonok.

A kötegelt bővítmény kipróbálhatja az Azure parancssori felület, és meg kell adnia a visszajelzést vagy a javaslatok, vagy a megjegyzések, ez a cikk vagy keresztül a [Azure Batch fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>További lépések

- A kötegelt sablonok blogbejegyzésben található: [Azure Batch futó feladatok az Azure parancssori felülettel – nem szükséges kód](https://azure.microsoft.com/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Részletes telepítési és használati dokumentációt, példák és forráskód érhetők el a [Azure GitHub-tárházban](https://github.com/Azure/azure-batch-cli-extensions).
