---
title: Feladatok futtatása végpontok között sablonok használatával - Azure Batch
description: Csak CLI-parancsokkal hozhat létre készletet, tölthet fel bemeneti adatokat, hozhat létre munkahelyeket és kapcsolódó feladatokat, és letöltheti az eredményül kapott kimeneti adatokat.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c7459c4dc700f034feafbf133b831a52b9233d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020165"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Azure Batch CLI-sablonok és fájlátvitel használata

Az Azure Batch-bővítmény használatával az Azure CLI, futtathatja a batch-feladatok kód írása nélkül.

JSON-sablonfájlokat hozhat létre és használhat az Azure CLI-vel kötegelt készletek, feladatok és feladatok létrehozásához. A CLI-bővítményparancsokkal egyszerűen feltöltheti a feladatbemeneti fájlokat a Batch-fiókhoz társított tárfiókba, és letöltheti a feladat kimeneti fájljait.

## <a name="overview"></a>Áttekintés

Az Azure CLI bővítménye lehetővé teszi, hogy a Batch-et végpontok között használják a nem fejlesztők által. Csak CLI-parancsokkal hozhat létre készletet, tölthet fel bemeneti adatokat, hozhat létre munkahelyeket és kapcsolódó feladatokat, és letöltheti az eredményül kapott kimeneti adatokat. Nincs szükség további kódra. Futtassa közvetlenül a CLI-parancsokat, vagy integrálja őket parancsfájlokba.

A kötegsablonok az Azure CLI JSON-fájlokhoz [meglévő Batch-támogatására](batch-cli-get-started.md#json-files-for-resource-creation) épülnek, így tulajdonságértékeket adhat meg készletek, feladatok, feladatok és egyéb elemek létrehozásakor. A kötegsablonok a következő képességeket adják hozzá:

-   Paraméterek definiálhatók. A sablon használata esetén csak a paraméterértékek vannak megadva az elem létrehozásához, a sablon törzsében megadott egyéb elemtulajdonság-értékekkel. Az a felhasználó, aki megérti a Batch-et és a Batch által futtatandó alkalmazásokat, sablonokat hozhat létre, készlet- és feladattulajdonság-értékeket adva meg. A Batch-et kevésbé ismerő felhasználónak és/vagy az alkalmazásoknak csak a megadott paraméterek értékeit kell megadniuk.

-   A feladatfeladat-gyárak egy vagy több feladatot hoznak létre egy feladathoz társítva, így nincs szükség számos feladatdefiníció létrehozására, és jelentősen leegyszerűsítik a feladatok beküldését.


A feladatok általában bemeneti adatfájlokat használnak, és kimeneti adatfájlokat hoznak létre. Alapértelmezés szerint egy tárfiók van társítva minden Batch-fiókhoz. Fájlok átvitele erre a tárfiókra a CLI használatával, kódolás és tárolási hitelesítő adatok nélkül.

Például, [ffmpeg](https://ffmpeg.org/) egy népszerű alkalmazás, amely feldolgozza a hang-és videofájlokat. Az alábbiakban lépéseket az Azure Batch CLI-vel a forrás videofájlok különböző felbontásokba való átkódolásához ffmpeg meghívására.

-   Készletsablon létrehozása. A sablont létrehozó felhasználó tudja, hogyan hívja meg az ffmpeg alkalmazást és annak követelményeit; megadják a megfelelő operációs rendszert, a virtuális gép méretét, az ffmpeg telepítésének módját (például egy alkalmazáscsomagból vagy például egy csomagkezelőt használva), valamint egyéb készlettulajdonság-értékeket. A paraméterek úgy jönnek létre, hogy a sablon használatakor csak a készletazonosítót és a virtuális gépek számát kell megadni.

-   Feladatsablon létrehozása. A sablont létrehozó felhasználó tudja, hogyan kell az ffmpeg-et meghívni a forrásvideó másik felbontásra való átkódolásához, és megadja a feladat parancssorát; azt is tudják, hogy van egy mappa, amely tartalmazza a forrás video fájlokat, a feladat szükséges egy bemeneti fájlt.

-   A felhasználó egy sor videofájlok átkódolni először létrehoz egy készlet et a készlet sablon, amely csak a készlet azonosítóját és a szükséges virtuális gépek számát. Ezután feltölthetik a forrásfájlokat az átkódoláshoz. Ezután egy feladat elküldhető a feladatsablon használatával, amely csak a készletazonosítót és a feltöltött forrásfájlok helyét adja meg. A kötegelt feladat jön létre, és bemeneti fájlonként egy feladat jön létre. Végül az átkódolt kimeneti fájlok letölthetők.

## <a name="installation"></a>Telepítés

Az Azure Batch CLI bővítmény telepítéséhez először [telepítse az Azure CLI 2.0-s t,](/cli/azure/install-azure-cli)vagy futtassa az Azure CLI-t az [Azure Cloud Shellben.](../cloud-shell/overview.md)

Telepítse a Batch-bővítmény legújabb verzióját a következő Azure CLI paranccsal:

```azurecli
az extension add --name azure-batch-cli-extensions
```

A Batch CLI bővítményről és a további telepítési lehetőségekről a [GitHub-tártárban](https://github.com/Azure/azure-batch-cli-extensions)talál további információt.


A CLI-bővítmény szolgáltatásainak használatához szüksége van egy Azure Batch-fiókra, és a tárolóba fájlokat átadó parancsokhoz egy csatolt tárfiókra.

Ha az Azure CLI-vel szeretne bejelentkezni egy Batch-fiókba, olvassa [el a Batch-erőforrások kezelése az Azure CLI-vel című témakört.](batch-cli-get-started.md)

## <a name="templates"></a>Sablonok

Az Azure Batch-sablonok hasonlóak az Azure Resource Manager-sablonokhoz, a funkcionalitás és a szintaxis terén. Ezek olyan JSON-fájlok, amelyek elemtulajdonság-neveket és -értékeket tartalmaznak, de a következő fő fogalmakat adják hozzá:

-   **Paraméterek**

    -   A tulajdonságértékek megadása egy törzsszakaszban, és csak a paraméterértékeket kell megadni a sablon használatakor. Például egy készlet teljes definíciója elhelyezhető a törzsben, és csak egy paraméter definiálva a készletazonosítóhoz; ezért a készlet létrehozásához csak egy készletazonosító karakterláncot kell mellékolni.
        
    -   A sablontörzset olyan személy is létrehozhatja, aki ismeri a Batch-et és a Batch által futtatandó alkalmazásokat; a sablon használatakor csak a szerző által definiált paraméterek értékeit kell megadni. A részletes batch és/vagy alkalmazásismeretekkel nem rendelkező felhasználók ezért használhatják a sablonokat.

-   **Változók**

    -   Egyszerű vagy összetett paraméterértékek megadása egy helyen, és a sablontörzs egy vagy több helyén való használat. A változók egyszerűsíthetik és csökkenthetik a sablon méretét, valamint karbantarthatóbbá tehetik, ha egy hely van a tulajdonságok módosításához.

-   **Magasabb szintű konstrukciók**

    -   Néhány magasabb szintű konstrukciók érhetők el a sablonban, amelyek még nem érhetők el a Batch API-k. Például egy feladat-előállító definiálható egy feladatsablonban, amely több feladatot hoz létre a feladathoz egy közös feladatdefiníció használatával. Ezek a konstrukciók ne kelljen kódolni, hogy dinamikusan hozzon létre több JSON fájlokat, mint például egy fájlt feladatonként, valamint hozzon létre script fájlokat telepíteni alkalmazásokat egy csomagkezelő.

    -   Egy bizonyos ponton ezek a konstrukciók hozzáadhatók a Batch szolgáltatáshoz, és elérhetők a Batch API-kban, ui-kban stb.

### <a name="pool-templates"></a>Készletsablonok

A készletsablonok támogatják a paraméterek és változók szabványos sablonképességeit. A következő magasabb szintű konstrukciót is támogatják:

-   **Csomagreferenciák**

    -   Opcionálisan lehetővé teszi a szoftverek készletcsomópontokba másolását csomagkezelők segítségével. A csomagkezelő és a csomagazonosító meg van adva. Egy vagy több csomag deklarálásával elkerülheti a szükséges csomagokat begyűjtő parancsfájl létrehozását, a parancsfájl telepítését és a parancsfájl futtatását az egyes készletcsomópontokon.

Az alábbiakban egy sablont, amely létrehoz egy készlet Linux virtuális gépek ffmpeg telepítve. A használatához csak egy készletazonosító karakterláncot és a készletben lévő virtuális gépek számát adja meg:

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

Ha a sablonfájl neve _pool-ffmpeg.json,_ akkor hívja meg a sablont az alábbiak szerint:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

A CLI kéri, hogy adja `poolId` `nodeCount` meg az értékeket a és a paramétereket. A json-fájlban is megadhatja a paramétereket. Példa:

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

Ha a JSON-fájl paraméterei *készlet-parameters.json*néven vannak elnevezve, akkor hívja meg a sablont az alábbiak szerint:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Feladatsablonok

A feladatsablonok támogatják a paraméterek és változók szabványos sablonképességeit. A következő magasabb szintű konstrukciót is támogatják:

-   **Feladat-gyár**

    -   Több feladatot hoz létre egy feladathoz egy feladatdefinícióból. A feladat-előállítók három típusa támogatott : parametrikus takarítás, fájlonkénti feladat és feladatgyűjtemény.

Az alábbi példa egy sablont, amely létrehoz egy feladatot átkódolni MP4 video fájlok ffmpeg az egyik két kisebb felbontású. Forrásonként egy feladatot hoz létre videofájlonként. A feladatbevitelhez és -kimenethez a [Fájlcsoportok és a fájlátvitel](#file-groups-and-file-transfer) további témakörében.

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

Ha a sablonfájl neve _job-ffmpeg.json,_ akkor hívja meg a sablont az alábbiak szerint:

```azurecli
az batch job create --template job-ffmpeg.json
```

A CLI a korábhoz képest a paraméterek értékeinek megadását kéri. A json-fájlban is megadhatja a paramétereket.

### <a name="use-templates-in-batch-explorer"></a>Sablonok használata a Batch Explorerben

Kötegelt CLI-sablont tölthet fel a [Batch Explorer](https://github.com/Azure/BatchExplorer) asztali alkalmazásba (korábbi nevén BatchLabs) kötegkészlet vagy feladat létrehozásához. Előre definiált készlet- és feladatsablonok közül is választhat a Batch Explorer gyűjteményben.

Sablon feltöltése:

1. A Batch Explorerben válassza **a Helyi** > **sablonok**gyűjteménye lehetőséget.

2. Jelöljön ki vagy húzzon át egy helyi készletet vagy feladatsablont.

3. Válassza **a Sablon használata**lehetőséget, és kövesse a képernyőn megjelenő utasításokat.

## <a name="file-groups-and-file-transfer"></a>Fájlcsoportok és fájlátvitel

A legtöbb feladathoz és feladathoz bemeneti fájlokra van szükség, és kimeneti fájlokat kell létrehozniuk. A bemeneti és kimeneti fájlok általában átkerülnek az ügyfélről a csomópontra, vagy a csomópontról az ügyfélre. Az Azure Batch CLI-bővítmény absztrakt el fájlátvitel, és használja a tárfiókot, amely az egyes Batch-fiók társítható.

A fájlcsoport az Azure storage-fiókban létrehozott tárolónak felel meg. Lehet, hogy a fájlcsoportalmappákkal rendelkezik.

A Batch CLI kiterjesztés parancsokat biztosít a fájlok feltöltéséhez az ügyfélről egy adott fájlcsoportba, és fájlok letöltése a megadott fájlcsoportból egy ügyfélre.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

A készlet- és feladatsablonok lehetővé teszik a fájlcsoportokban tárolt fájlok fájlcsoportokban tárolt fájlok fájlcsoportokba történő másolását a készletcsomópontokra vagy a készletcsomópontokon kívüli csomópontokra való visszamásoláshoz. A korábban megadott feladatsablonban például az *ffmpeg-bemenetfájlcsoport* a feladat-előállítóhoz van megadva, mint az átkódolásra a csomópontra másolt forrásvideofájlok helye. Az *ffmpeg-output* fájlcsoport az a hely, ahol az átkódolt kimeneti fájlokat az egyes feladatokat futtató csomópontról másolja a program.

## <a name="summary"></a>Összefoglalás

A sablon- és fájlátviteli támogatás jelenleg csak az Azure CLI-hez lett hozzáadva. A cél az, hogy a Batch-et használó közönséget kibővítse azokra a felhasználókra, akiknek nem kell kódot fejleszteniük a Batch API-k használatával, például kutatók és informatikai felhasználók. Kódolás nélkül az Azure, a Batch és a Batch által futtatandó alkalmazásokat ismerő felhasználók sablonokat hozhatnak létre a készlethez és a munkahelyteremtéshez. A sablonparaméterekkel a Batch és az alkalmazások részletes ismerete nélkül a felhasználók használhatják a sablonokat.

Próbálja ki az Azure CLI Batch bővítményét, és adjon meg nekünk visszajelzést vagy javaslatokat, akár a cikkhez fűzött megjegyzésekben, akár a [Batch Community repo-n](https://github.com/Azure/Batch)keresztül.

## <a name="next-steps"></a>További lépések

- Részletes telepítési és használati dokumentáció, minták és forráskód érhetők el az [Azure GitHub-tárházban.](https://github.com/Azure/azure-batch-cli-extensions)

- További információ a [Batch Explorer](https://github.com/Azure/BatchExplorer) használatával a Batch-erőforrások létrehozásáról és kezeléséről.
