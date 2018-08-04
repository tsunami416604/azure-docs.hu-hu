---
title: Az Azure Batch-feladatok futtatása – teljes körű-sablonokkal |} A Microsoft Docs
description: Sablon fájlok és az Azure CLI Batch-készletek, feladatok és tevékenységek létrehozása
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 08/02/2018
ms.author: danlep
ms.openlocfilehash: 50ed5a6b57c3c994f636db5cc975ad1908e50c7d
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493433"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Azure Batch parancssori felületi sablonjainak és fájlátviteli

Egy Azure Batch-bővítmény, az Azure CLI használatával, lehetőség a Batch-feladatok futtatása kód írása nélkül.

Létrehozhat és használhat a sablon JSON-fájlok az Azure CLI-vel Batch-készletek, feladatok és tevékenységek létrehozása. Használja a CLI-bővítmény parancsok könnyedén tölthet fel a feladatok bemeneti fájljait a storage-fiókhoz társított a Batch-fiókot, és a letöltéshez feladat kimeneti fájlokat.

## <a name="overview"></a>Áttekintés

Az Azure CLI-bővítmény lehetővé teszi, hogy a Batch szolgáltatást kell használt – teljes körű felhasználók, akik nem a fejlesztők által. Csak CLI-parancsokkal létrehoz egy készletet, bemeneti adatok feltöltése, feladatok és a kapcsolódó feladatok létrehozása és az eredményül kapott kimeneti adatok letöltéséhez. Nincsenek további kódra szükség. A CLI-parancsok futtatása közvetlenül, vagy parancsfájlok integrálhatja őket.

Batch-sablonok létrehozása a kiszolgálón a [meglévő Batch támogatás az Azure CLI-ben](batch-cli-get-started.md#json-files-for-resource-creation) JSON-fájlok létrehozásakor, készletek, feladatok, tevékenységek és egyéb elemek tulajdonság értéket is. Batch-sablonok hozzáadása a következő képességekkel:

-   Paraméterek lehet definiálni. A sablon használata esetén csak a paraméterek értékeit az elem létrehozása és a többi elem tulajdonságértékek sablon törzsében megadott vannak megadva. A felhasználó, aki ismeri a Batch és a Batch által futtatandó alkalmazásokat hozhat létre sablonokat, adja meg a készletet, a feladat és a tevékenység tulajdonságértékeket. A felhasználó kevesebb ismerős a Batch-és/vagy az alkalmazásokat csak nem adja meg a definiált paraméterek értékeit.

-   Feladat feladat előállítók hozzon létre egy feladatot, nem kell létrehozni számos feladat definíciók és jelentősen leegyszerűsíti a feladatküldéshez társított egy vagy több feladat.


Feladatok általában használni a bemeneti adatfájlt és a kimeneti adatok fájlokat. Storage-fiók van társítva, alapértelmezésben minden Batch-fiókkal. A fájlátvitel és onnan ezt a tárfiókot, a parancssori felületről, a nem kódolási és nem a tároló hitelesítő adatait.

Ha például [ffmpeg](http://ffmpeg.org/) népszerű alkalmazás, amely feldolgozza a hang- és videofájlok. Az alábbiakban a lépéseket az Azure Batch parancssori ffmpeg átkódolása videó forrásfájljainak, a különböző meghívásához.

-   Hozzon létre egy készlet sablont. A sablont létrehozó felhasználónak tudja, hogyan hívhat meg az ffmpeg alkalmazás és a rájuk vonatkozó követelményekről; akkor adja meg a megfelelő operációs rendszer, a virtuális gép mérete, hogyan ffmpeg segítségével telepítve (az alkalmazáscsomag vagy egy Csomagkezelő, például használatával), és más tulajdonság értékek alkalmazáskészletet. Paraméterek jönnek létre, így a sablon használata esetén csak a készlet azonosítója és a virtuális gépek számának meg kell adni.

-   Hozzon létre egy feladatot sablont. A sablont létrehozó felhasználónak tudja, hogyan kell ffmpeg átkódolása forrásához videó különböző felbontású hívható, és adja meg a tevékenység parancssora; akkor is, hogy, hogy nincs-e egy tevékenységhez, a bemeneti fájl szükséges a videó forrásfájlokat tartalmazó mappa.

-   Videofájlok azért vannak beállítva a felhasználó először egy készletet hoz létre a készletet sablonnal, csak a készlet azonosítója és a szükséges virtuális gépek számának megadása. Ezután feltöltik a forrásfájlok nem alakítható át. Ezután beküldhető egy feladat csak a készlet azonosítója és a feltöltött forrásfájljainak helyét megadó feladat-sablon használatával. A Batch-feladat jön létre, a létrehozott bemeneti fájlonként egy feladat. Végül a átkódolt kimeneti fájlok lehet letölteni.

## <a name="installation"></a>Telepítés

Az Azure Batch CLI-bővítmény telepítéséhez először [Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli), vagy futtassa az Azure CLI [Azure Cloud Shell](../cloud-shell/overview.md).

Telepítse a legújabb verziót a Batch-bővítmény a következő Azure CLI-paranccsal:

```azurecli
az extension add --name azure-batch-cli-extensions
```

A Batch CLI-bővítmény és a további telepítési beállítások kapcsolatos további információkért lásd: a [GitHub-adattárat](https://github.com/Azure/azure-batch-cli-extensions).


Azure Batch-fiókot kell használni a CLI bővítmény szolgáltatásai, és a parancsok, amelyek a fájlátvitel és a storage, a társított storage-fiók.

Jelentkezzen be az Azure CLI-vel Batch-fiók, lásd: [Batch-erőforrások kezelése az Azure CLI-vel](batch-cli-get-started.md).

## <a name="templates"></a>Sablonok

Az Azure Batch-sablonok hasonlóak az Azure Resource Manager-sablonok, a funkciók és szintaxist. Azok, amelyek tartalmazzák a konfigurációelem nevét és értékeit, de adja hozzá a következő főbb fogalmak JSON-fájlokat:

-   **Paraméterek**

    -   Lehetővé teszik egy szervezet szakaszban adható meg kellene adni a sablon használatakor csak paraméter értékekkel tulajdonságértékeket. Például a teljes készlet definíciója sikerült kell elhelyezni a szervezet, és csak az egyik paraméter meghatározott készlet azonosítója; csak egy készlet azonosító karakterláncot ezért kell adni egy készlet létrehozásához.
        
    -   A sablon törzsét hozhatóak létre, amelyet a Batch és a Batch; által futtatandó alkalmazások csak a szerző által megadott paraméterek értékeit kell megadni, ha a sablont használ. A felhasználó a részletesebb kötegelt és/vagy az alkalmazás ismeretek nélkül ezért sablonjai segítségével.

-   **Változók**

    -   Lehetővé teszi egyszerű vagy összetett paraméterértékek egy helyen megadott és a egy vagy több helyen, a sablon törzsében használja. Változók is egyszerűsítése és csökkentheti a sablon méretét, valamint, hogy ez több fenntartható tulajdonságainak módosításához egyetlen helyen kellene.

-   **A magasabb szintű szerkezeteket.**

    -   A sablon, amely még nem állnak rendelkezésre az a Batch API-k néhány magasabb szintű szerkezeteket érhetők el. Például egy feladat gyári lehet definiálni egy feladat sablont, amely több tevékenységet a feladathoz, közös feladatdefiníció használatával hoz létre. A fentiek elkerülése érdekében dinamikusan hozhat létre több JSON-fájlok, például egy fájl egy feladat, valamint parancsfájl-fájlok létrehozása kód alkalmazásokat Csomagkezelő használatával telepíteni kell.

    -   Később a fentiek lehet hozzáadva a Batch szolgáltatásnak, és elérhető a Batch API-k, előkészíthetik, stb.

### <a name="pool-templates"></a>Készlet Sablonok

Készlet Sablonok támogatja a paraméterek és változók standard sablon képességeit. A következő magasabb szintű szerkezet is támogatja:

-   **Alkalmazáscsomag-hivatkozások**

    -   Igény szerint lehetővé teszi a szoftverfrissítési csomag-kezelők használatával készletcsomópontokért kell másolni. A Csomagkezelő és a csomag azonosítója meg van adva. Egy vagy több csomagot deklarálásával kerülje a létrehozása egy parancsfájlt, amely lekérdezi a szükséges csomagokat, a parancsfájl telepítése és a szkript futtatása a készlet csomópontokon.

Az alábbiakban látható egy példa egy sablont, amely létrehozza a Linux rendszerű virtuális gépek készletét ffmpeg segítségével telepítve. A használatához adja meg a készlet azonosító karakterláncot, és a készletben található virtuális gépek számát:

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

Ha a neve a sablonfájlt _készlet-ffmpeg.json_, ezután meghívja az a sablon a következő:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

A parancssori felület felszólítja, hogy adja meg az értékeket a `poolId` és `nodeCount` paramétereket. Megadhat egy JSON-fájlt a paramétereket is. Példa:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Ha a paramétereket JSON-fájl neve lett *készlet-parameters.json*, ezután meghívja az a sablon a következő:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Feladat-sablonok

Feladat sablonok támogatja a paraméterek és változók standard sablon képességeit. A következő magasabb szintű szerkezet is támogatja:

-   **A feladat gyári**

    -   Egy feladat definíciója egy feladat több feladatot hoz létre. A feladat factory három típusú támogatottak – paraméteres Szögtartomány, feladathoz egy fájlt, és tevékenység-gyűjtemény.

Az alábbiakban látható egy példa egy sablont, amely létrehoz egy feladatot, MP4 videó fájlok átkódolása ffmpeg segítségével az egyik két kisebb felbontások esetén. Létrehoz egy feladathoz egy videó forrásfájl. Lásd: [csoportok fájlt, és fájlátvitel](#file-groups-and-file-transfer) további információt a feladat bemeneti és kimeneti fájlcsoportok.

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

Ha a neve a sablonfájlt _feladat-ffmpeg.json_, ezután meghívja az a sablon a következő:

```azurecli
az batch job create --template job-ffmpeg.json
```

Mint előtt, a parancssori felület felszólítja, hogy adja meg a paraméterek értékeit. Megadhat egy JSON-fájlt a paramétereket is.

### <a name="use-templates-in-batch-explorer"></a>Sablonok használata a Batch Explorer

A Batch CLI sablon feltöltheti a [Batch Explorer](https://github.com/Azure/BatchExplorer) asztali alkalmazást (korábbi nevükön BatchLabs) Batch-készlet vagy feladat létrehozása. A Batch Explorer katalógusban készlet és -feladat előre definiált sablonok közül is választhat.

A sablon feltöltése:

1. Válassza ki a Batch Explorer **katalógus** > **helyi sablonok**.

2. Válassza ki, vagy húzza, egy helyi készlet vagy a feladat-sablont.

3. Válassza ki **ezzel a sablonnal**, és kövesse a képernyőn megjelenő utasításokat.

## <a name="file-groups-and-file-transfer"></a>Fájlcsoportok és fájlátvitel

A legtöbb feladatok és tevékenységek esetében a bemeneti fájlokat és a kimeneti fájlokat. Általában a bemeneti fájlok és a kimeneti fájlok kerülnek, vagy az ügyfél és a csomópont, vagy az ügyfél a csomópontról. Az Azure Batch CLI-bővítmény távolléti fájlátvitel kivonatolja, és használja a tárfiókot, amely minden egyes Batch-fiókhoz is társíthat.

Egy adatbázisnak felel meg az Azure storage-fiókban létrehozott tárolóba. A fájlcsoport almappák rendelkezhet.

A Batch CLI-bővítmény nyújt egy megadott fájl csoporthoz ügyfélről fájlok feltöltése és a fájlok letöltése a megadott fájl csoportból egy ügyfél a parancsok.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Készlet és -feladat sablonok lehetővé teszik a fájlcsoportok másolási alakzatot a készlet csomópontjain, és térjen vissza egy készlet csomópontjainak ki kell adni a tárolt fájljaihoz. Ha például a feladat sablon korábban megadott, a fájlcsoport *ffmpeg-bemenet* másolja le a csomópontot átkódolása videó forrásfájljainak helyét, a feladat Factory van megadva. A fájlcsoport *ffmpeg-kimenet* az a hely, ahol a átkódolt kimeneti fájlok másolását a csomópont minden egyes feladat futtatása.

## <a name="summary"></a>Összegzés

Sablon és a fájl átvitele támogatási jelenleg csak az Azure CLI-vel való lettek hozzáadva. A cél, hogy a felhasználók számára, akiknek nincs szükségük a Batch API-k, például kutatói és informatikai felhasználók kód fejlesztéséhez használhatja a kötegelt célközönség bontsa ki. Nélkül kódolási, az Azure Batch és a Batch által futtatandó alkalmazások felhasználók hozhatnak létre a készlet és -feladat létrehozásához. A sablon paramétereit a felhasználók kötegelt és a részletes ismerete nélkül használhatják a sablonok.

Próbálja ki a Batch-bővítmény az Azure CLI-hez, és biztosítja a számunkra visszajelzés vagy javaslatok, vagy a megjegyzések, ez a cikk vagy keresztül a [Batch közösségi tárház](https://github.com/Azure/Batch).

## <a name="next-steps"></a>További lépések

- Részletes telepítési és használati dokumentáció, minták és forráskód érhetők el a [Azure GitHub-adattárat](https://github.com/Azure/azure-batch-cli-extensions).

- További információ [Batch Explorer](https://github.com/Azure/BatchExplorer) létrehozása és kezelése a Batch-erőforrásokat.
