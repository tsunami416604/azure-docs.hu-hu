---
title: Feladat futásidejű környezeti változói – Azure Batch | Microsoft Docs
description: A feladat futásidejű környezeti változóinak útmutatója és referenciája Azure Batch elemzésekhez.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/12/2019
ms.author: jushiman
ms.openlocfilehash: fd3c8ac9e65f7f77be070e1d1d108490e61eb248
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027186"
---
# <a name="azure-batch-runtime-environment-variables"></a>Azure Batch futtatókörnyezet környezeti változói

A [Azure batch szolgáltatás](https://azure.microsoft.com/services/batch/) az alábbi környezeti változókat állítja be a számítási csomópontokon. Ezeket a környezeti változókat a feladatok parancssorában, illetve a parancssorok által futtatott programokban és parancsfájlokban lehet hivatkozni.

A környezeti változók batch használatával történő használatáról további információt a [környezeti beállítások a feladatokhoz](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks)című témakörben talál.

## <a name="environment-variable-visibility"></a>Környezeti változó láthatósága

Ezek a környezeti változók csak a **feladat felhasználójának**kontextusában láthatók, azon a csomóponton lévő felhasználói fióknál, amely alatt a feladat végre lett hajtva. Ha RDP vagy SSH segítségével [távolról csatlakozik](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a számítási csomóponthoz, ezek a környezeti változók *nem* fognak megjelenni. Ez azért van, mert a távoli kapcsolat kialakításához használt felhasználói fiók nem egyezik a tevékenységhez használt fiókkal.

Egy környezeti változó aktuális értékének lekéréséhez indítsa el `cmd.exe` egy Windows számítási csomóponton, vagy `/bin/sh` egy Linux-csomóponton:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Környezeti változók parancssori bővítése

A számítási csomópontokon végzett feladatok által végrehajtott parancssorok nem egy rendszerhéj alatt futnak. Ezért ezek a parancssorok nem képesek natív módon kihasználni a rendszerhéj funkcióit, például a környezeti változók kiterjesztését (Ez magában foglalja a `PATH`). Az ilyen funkciók kihasználásához a rendszerhéjt a parancssorban kell **meghívni** . Például indítsa el `cmd.exe` a Windows számítási csomópontokon, vagy `/bin/sh` Linux-csomópontokon:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Környezeti változók

| Változó neve                     | Leírás                                                              | Elérhetőség | Példa |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Annak a Batch-fióknak a neve, amelyhez a feladat tartozik.                  | Minden feladat.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | A Batch-fiók URL-címe. | Minden feladat. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Az alkalmazáscsomag környezeti változóinak előtagja. Ha például a "FOO" "1" nevű alkalmazás egy készletre van telepítve, akkor a környezeti változó AZ_BATCH_APP_PACKAGE_FOO_1. AZ_BATCH_APP_PACKAGE_FOO_1 mutat a csomag letöltésének helyére (egy mappába). Az alkalmazáscsomag alapértelmezett verziójának használatakor használja a AZ_BATCH_APP_PACKAGE környezeti változót a verziószámok nélkül. | A társított alkalmazáscsomag feladatainak egyike. Az összes feladathoz is elérhető, ha a csomópontnak van alkalmazáscsomag. | AZ_BATCH_APP_PACKAGE_FOO_1 |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Egy hitelesítési jogkivonat, amely hozzáférést biztosít a Batch szolgáltatási műveleteinek korlátozott készletéhez. Ez a környezeti változó csak akkor jelenik meg, ha a [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) be van állítva a [feladat hozzáadásakor](/rest/api/batchservice/task/add#request-body). A jogkivonat értékét a Batch API-k hitelesítő adatként használják a Batch-ügyfél létrehozásához, például a [BatchClient. Open () .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_)-ban. | Minden feladat. | OAuth2 hozzáférési token |
| AZ_BATCH_CERTIFICATES_DIR       | Egy könyvtár a [feladat munkakönyvtárában][files_dirs] , amelyben a rendszer a Linux számítási csomópontjain tárolja a tanúsítványokat. Ez a környezeti változó nem vonatkozik a Windows számítási csomópontjaira.                                                  | Minden feladat.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | A [többpéldányos feladat][multi_instance] számára a `nodeIP,nodeIP`formátumban lefoglalt csomópontok listája. | Több példányos elsődleges és alfeladatok. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Megadja, hogy az aktuális csomópont a [többpéldányos feladat][multi_instance]fő csomópontja-e. A lehetséges értékek a következők: `true` és `false`.| Több példányos elsődleges és alfeladatok. | `true` |
| AZ_BATCH_JOB_ID                 | Annak a fióknak az azonosítója, amelyhez a feladat tartozik. | Minden feladat, kivéve a kezdési feladatot. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | A csomóponton a feladat-előkészítési [feladat könyvtárának][files_dirs] teljes elérési útja. | Minden feladat, a kezdési feladat és a feladat-előkészítési feladat kivételével. Csak akkor érhető el, ha a feladat feladat-előkészítési feladattal van konfigurálva. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | A csomóponton a feladat-előkészítési [feladat munkakönyvtárának][files_dirs] teljes elérési útja. | Minden feladat, a kezdési feladat és a feladat-előkészítési feladat kivételével. Csak akkor érhető el, ha a feladat feladat-előkészítési feladattal van konfigurálva. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | Annak a számítási csomópontnak az IP-címe és portja, amelyen egy [többpéldányos feladat][multi_instance] elsődleges feladata fut. | Több példányos elsődleges és alfeladatok. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | Annak a csomópontnak az azonosítója, amelyhez a feladat hozzá van rendelve. | Minden feladat. | TVM-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Ha `true`, az aktuális csomópont egy dedikált csomópont. Ha `false`, ez egy [alacsony prioritású csomópont](batch-low-pri-vms.md). | Minden feladat. | `true` |
| AZ_BATCH_NODE_LIST              | A [többpéldányos feladat][multi_instance] számára a `nodeIP;nodeIP`formátumban lefoglalt csomópontok listája. | Több példányos elsődleges és alfeladatok. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | A csomópont szintű [fájlrendszer csatlakoztatási](virtual-file-mount.md) helyének teljes elérési útja, ahol az összes csatlakoztatási könyvtár található. A Windows-fájlmegosztás meghajtóbetűjelet használ, így a Windows esetében a csatlakoztatási meghajtó az eszközök és meghajtók részét képezi.  |  Minden feladat, beleértve az indítási feladatot, hozzáférhet a felhasználóhoz, mivel a felhasználó ismeri a csatlakoztatott könyvtár csatlakoztatási engedélyeit. | Az Ubuntuban például a hely a következő: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | A csomóponton lévő összes [Batch-könyvtár][files_dirs] gyökerének teljes elérési útja. | Minden feladat. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | A csomóponton a [megosztott könyvtár][files_dirs] teljes elérési útja. A csomóponton végrehajtandó összes feladat olvasási/írási hozzáféréssel rendelkezik ehhez a címtárhoz. A más csomópontokon végrehajtandó feladatok nem rendelkeznek távoli hozzáféréssel ehhez a címtárhoz (nem "megosztott" hálózati könyvtár). | Minden feladat. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | A csomóponton a [Start feladat könyvtárának][files_dirs] teljes elérési útja. | Minden feladat. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Annak a készletnek azonosítója, amelyen a tevékenység fut. | Minden feladat. | batchpool001 |
| AZ_BATCH_TASK_DIR               | A csomóponton a [feladat könyvtárának][files_dirs] teljes elérési útja. Ez a könyvtár tartalmazza a feladat `stdout.txt` és `stderr.txt`, valamint a AZ_BATCH_TASK_WORKING_DIR. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Az aktuális tevékenység azonosítója. | Minden feladat, kivéve a kezdési feladatot. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Egy [többpéldányos feladat][multi_instance]elsődleges feladatához és minden feladatához azonos könyvtár elérési útja. Az elérési út minden olyan csomóponton létezik, amelyen a többpéldányos feladat fut, és az olvasási/írási hozzáférés az adott csomóponton futó feladatok parancsai számára (a [koordinációs parancs][coord_cmd] és az [alkalmazási parancs][app_cmd]is). Az alfeladatok vagy a más csomópontokon végrehajtandó elsődleges feladatok nem rendelkeznek távoli hozzáféréssel ehhez a címtárhoz (nem "megosztott" hálózati könyvtár). | Több példányos elsődleges és alfeladatok. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | A csomóponton a [feladat munkakönyvtárának][files_dirs] teljes elérési útja. A jelenleg futó feladat írási/olvasási hozzáféréssel rendelkezik ehhez a címtárhoz. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | A [többpéldányos feladathoz][multi_instance]lefoglalt csomópontok és magok száma. A csomópontok és a magok formátuma `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, ahol a csomópontok számát egy vagy több csomópont IP-címe, valamint a magok száma követi. |  Több példányos elsődleges és alfeladatok. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
