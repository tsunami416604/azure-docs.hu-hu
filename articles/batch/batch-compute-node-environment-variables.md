---
title: Számítási csomópont környezeti változói – Azure Batch |} A Microsoft Docs
description: Számítási csomópont környezeti változó referencia az Azure Batch használatával.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 01/03/2019
ms.author: lahugh
ms.openlocfilehash: 48c2172e02e935dde28ac323c776c8895b1d36b2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017361"
---
# <a name="azure-batch-compute-node-environment-variables"></a>Az Azure Batch számítási csomópont környezeti változói

A [Azure Batch szolgáltatás](https://azure.microsoft.com/services/batch/) állítja be az alábbi környezeti változókat a számítási csomópontokon. Ezeket a környezeti változókat a tevékenységek parancssorában, és a Programok telepítése és törlése is lehet hivatkozni, és a parancssorok által futtatott parancsfájlok.

A Batch környezeti változók használatával kapcsolatos további információkért lásd: [környezeti beállítások tevékenységekhez](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Környezeti változó láthatósága

Ezeket a környezeti változókat csak kontextusában láthatók a **tevékenység felhasználójának**, a csomóponton, amelyben a feladat végrehajtása a felhasználói fiókot. Ha RDP vagy SSH segítségével [távolról csatlakozik](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a számítási csomóponthoz, ezek a környezeti változók *nem* fognak megjelenni. Ez azért van, mert a távoli kapcsolat kialakításához használt felhasználói fiók nem egyezik a tevékenységhez használt fiókkal.

## <a name="command-line-expansion-of-environment-variables"></a>A környezeti változók parancssori kiegészítése

A feladatok által futtatott parancsokat a számítási csomópontok csak akkor futtassa rendszerhéj alatt. Ezért ezek parancssorok nem tudja natívan kihasználni a rendszerhéj előnyeit, például a környezeti változók bővítésének (Ez magában foglalja a `PATH`). Ezek a funkciók előnyeit, kell **meghívnia a rendszerhéjat** a parancssorban. Például, indítsa el a `cmd.exe` Windows a számítási csomópontok vagy `/bin/sh` a Linux-csomópontokat:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Környezeti változók

| Változó neve                     | Leírás                                                              | Rendelkezésre állás | Példa |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | A Batch-fiókhoz tartozó a feladat neve.                  | Minden feladat.   | mybatchaccount |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Egy hitelesítési tokent, amely hozzáférést biztosít a Batch szolgáltatás műveletek korlátozott számú. Ez a környezeti változó jelenleg csak akkor, ha a [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) vannak beállítva, ha a [feladatot ad hozzá](/rest/api/batchservice/task/add#request-body). A token érték szolgál a Batch API-k hitelesítő adatként hozzon létre egy Batch-ügyfél, például az a [BatchClient.Open() .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Minden feladat. | Az OAuth2 hozzáférési jogkivonat |
| AZ_BATCH_CERTIFICATES_DIR       | A címtárhoz a [tevékenység munkakönyvtárának] [ files_dirs] linuxos mely tanúsítványokat tárolja a számítási csomópontokon. Vegye figyelembe, hogy ez a környezeti változó nem vonatkozik a Windows a számítási csomópontokon.                                                  | Minden feladat.   |  /mnt/Batch/Tasks/workitems/batchjob001/Job-1/task001/certs |
| AZ_BATCH_JOB_ID                 | Annak a fióknak az azonosítója, amelyhez a feladat tartozik. | Minden olyan feladat, kivéve az indítási tevékenység. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | A teljes elérési útját a feladat-előkészítési [tevékenység könyvtárának] [ files_dirs] a csomóponton. | Minden feladat indítási és a feladat-előkészítési tevékenység kivételével. Csak akkor érhető el, ha a feladat úgy van konfigurálva, a feladat-előkészítési tevékenységet. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | A teljes elérési útját a feladat-előkészítési [tevékenység munkakönyvtárának] [ files_dirs] a csomóponton. | Minden feladat indítási és a feladat-előkészítési tevékenység kivételével. Csak akkor érhető el, ha a feladat úgy van konfigurálva, a feladat-előkészítési tevékenységet. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | A csomópont a tevékenységhez rendelt azonosítója. | Minden feladat. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | A teljes elérési útját a legfelső szintű, az összes [Batch-könyvtárak] [ files_dirs] a csomóponton. | Minden feladat. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | A teljes elérési útját a [megosztott könyvtár] [ files_dirs] a csomóponton. A csomóponton végrehajtott összes tevékenység rendelkezik, ez a könyvtár olvasási/írási hozzáférést. A többi csomóponton végrehajtott feladatok nem rendelkezik távoli hozzáférés a címtárhoz (már nem egy "megosztott" hálózati könyvtár). | Minden feladat. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | A teljes elérési útját a [indítsa el a tevékenység könyvtárának] [ files_dirs] a csomóponton. | Minden feladat. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Annak a készletnek azonosítója, amelyen a tevékenység fut. | Minden feladat. | batchpool001 |
| AZ_BATCH_TASK_DIR               | A teljes elérési útját a [tevékenység könyvtárának] [ files_dirs] a csomóponton. Ez a könyvtár tartalmazza a `stdout.txt` és `stderr.txt` a tevékenységhez, és a AZ_BATCH_TASK_WORKING_DIR. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Az aktuális tevékenység azonosítója. | Minden olyan feladat, kivéve az indítási tevékenység. | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | A teljes elérési útját a [tevékenység munkakönyvtárának] [ files_dirs] a csomóponton. A jelenleg futó feladat Ez a könyvtár olvasási/írási hozzáféréssel rendelkezik. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | A csomópontok és magok száma csomópontonként kiosztott listáját egy [többpéldányos tevékenység][multi_instance]. Csomópontok és magok a formátum szerepel `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, ahol a csomópontok számát követ egy vagy több csomópont IP-címek és a magok számát az egyes. |  Többpéldányos elsődleges és részfeladatok gyakran. |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | A lefoglalt csomópontok listáját egy [többpéldányos tevékenység] [ multi_instance] formátumban `nodeIP;nodeIP`. | Többpéldányos elsődleges és részfeladatok gyakran. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | A lefoglalt csomópontok listáját egy [többpéldányos tevékenység] [ multi_instance] formátumban `nodeIP,nodeIP`. | Többpéldányos elsődleges és részfeladatok gyakran. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | Az IP-cím és port, a számítási csomóponton, amelyen az elsődleges feladatát egy [többpéldányos tevékenység] [ multi_instance] futtatja. | Többpéldányos elsődleges és részfeladatok gyakran. | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | A könyvtár elérési útja, amely azonos az elsődleges feladat, és minden alfeladat, egy [többpéldányos tevékenység][multi_instance]. Az elérési út létezik minden csomóponton, amelyen a többpéldányos tevékenység lefut, és elérhető-e a leállt csomóponton a feladat parancsokat olvasási/írási (mind a [koordináció parancs] [ coord_cmd] és a [ alkalmazás parancs][app_cmd]). Altevékenységek vagy a többi csomóponton végrehajtott elsődleges feladat nem rendelkezik távoli hozzáférés a címtárhoz (már nem egy "megosztott" hálózati könyvtár). | Többpéldányos elsődleges és részfeladatok gyakran. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Megadja, hogy az aktuális csomópont-e a fő csomópont egy [többpéldányos tevékenység][multi_instance]. Lehetséges értékek a következők `true` és `false`.| Többpéldányos elsődleges és részfeladatok gyakran. | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | Ha `true`, az aktuális csomópont a dedikált csomópont. Ha `false`, van egy [alacsony prioritású csomópontot](batch-low-pri-vms.md). | Minden feladat. | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
