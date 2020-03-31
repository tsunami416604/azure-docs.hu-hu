---
title: Feladatfutásidejű környezeti változók - Azure Batch | Microsoft dokumentumok
description: Feladat futásidejű környezeti változó útmutatást és hivatkozást az Azure Batch Analytics.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/12/2019
ms.author: labrenne
ms.openlocfilehash: ebaa06acf309a0f941b8b4efd76fa4e9e7460810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053952"
---
# <a name="azure-batch-runtime-environment-variables"></a>Azure Batch futásidejű környezeti változók

Az [Azure Batch szolgáltatás](https://azure.microsoft.com/services/batch/) a következő környezeti változókat állítja be a számítási csomópontokon. Ezeket a környezeti változókat a feladatparancssorokban, valamint a parancssorok által futtatott programokban és parancsfájlokban is használhatja.

A környezeti változók Batch használatával kapcsolatos további tudnivalókért olvassa el a Környezeti beállítások a feladatokhoz című [témakört.](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks)

## <a name="environment-variable-visibility"></a>Környezeti változó láthatósága

Ezek a környezeti változók csak a **feladat felhasználójának**, a feladat végrehajtásának alapjául tartozó csomópont felhasználói fiókjának környezetében láthatók. Ha RDP vagy SSH segítségével [távolról csatlakozik](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a számítási csomóponthoz, ezek a környezeti változók *nem* fognak megjelenni. Ez azért van, mert a távoli kapcsolat kialakításához használt felhasználói fiók nem egyezik a tevékenységhez használt fiókkal.

Egy környezeti változó aktuális értékének `cmd.exe` lekérése windowsos számítási `/bin/sh` csomóponton vagy Linux-csomóponton:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Környezeti változók parancssori bővítése

A számítási csomópontokon végzett feladatok által végrehajtott parancssorok nem parancsrendszeralatt futnak. Ezért ezek a parancssorok nem tudják natív módon kihasználni az `PATH`olyan rendszerhéj-funkciókat, mint a környezeti változók bővítése (ez magában foglalja a). Az ilyen funkciók kihasználásához meg kell **hívnia a parancsértelmezőt** a parancssorban. Indítsa el `cmd.exe` például a Windows számítási `/bin/sh` csomópontjain vagy Linux-csomópontokon:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Környezeti változók

| Változó neve                     | Leírás                                                              | Rendelkezésre állás | Példa |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Annak a Batch-fióknak a neve, amelyhez a feladat tartozik.                  | Minden feladat.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | A Batch-fiók URL-címe. | Minden feladat. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Az alkalmazáscsomag összes környezeti változójának előtagja. Ha például a "FOO" "1" alkalmazás verziója egy készletre van telepítve, a környezeti változó AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) vagy AZ_BATCH_APP_PACKAGE_FOO#1 (Windows rendszeren). AZ_BATCH_APP_PACKAGE_FOO_1 a csomag letöltésének helyére (egy mappára) mutat. Az alkalmazáscsomag alapértelmezett verziójának használataesetén használja a AZ_BATCH_APP_PACKAGE környezeti változót a verziószámok nélkül. Ha a Linux, és az alkalmazás csomag neve "Agent-linux-x64", és a verzió "1.1.46.0, a környezet neve valójában: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, aláhúzásjel és kisbetűs. További információt [itt](https://docs.microsoft.com/azure/batch/batch-application-packages#execute-the-installed-applications) talál. | Bármilyen feladat egy társított alkalmazáscsomaggal. Minden feladathoz is elérhető, ha maga a csomópont rendelkezik alkalmazáscsomagokkal. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) vagy AZ_BATCH_APP_PACKAGE_FOO#1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Hitelesítési jogkivonat, amely hozzáférést biztosít a Batch szolgáltatás műveletek korlátozott készletéhez. Ez a környezeti változó csak akkor van jelen meg, ha a [feladat hozzáadásakor](/rest/api/batchservice/task/add#request-body)a [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) van beállítva. A tokenérték a Batch API-kban hitelesítő adatként szolgál egy batch-ügyfél létrehozásához, például a [BatchClient.Open() .NET API-ban.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_) | Minden feladat. | OAuth2 hozzáférési jogkivonat |
| AZ_BATCH_CERTIFICATES_DIR       | A feladat [munkakönyvtárában][files_dirs] lévő könyvtár, amelyben a Linux-számítási csomópontok tanúsítványai tárolódnak. Ez a környezeti változó nem vonatkozik a Windows számítási csomópontjaira.                                                  | Minden feladat.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | A [többpéldányos feladatokhoz][multi_instance] a formátumban lefoglalt csomópontok `nodeIP,nodeIP`listája . | Többpéldányos elsődleges és altevékenységek. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Itt adható meg, hogy az aktuális csomópont egy [többpéldányos feladat főcsomópontja-e.][multi_instance] A lehetséges `true` `false`értékek a és a.| Többpéldányos elsődleges és altevékenységek. | `true` |
| AZ_BATCH_JOB_ID                 | Annak a fióknak az azonosítója, amelyhez a feladat tartozik. | Az indítási tevékenység kivételével minden tevékenység. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | A feladat-előkészítési [feladatkönyvtár][files_dirs] teljes elérési útja a csomóponton. | Minden feladat, kivéve az indítási tevékenységet és a feladat-előkészítési feladatot. Csak akkor érhető el, ha a feladat feladat-előkészítési feladattal van konfigurálva. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | A feladat-előkészítési [feladat munkakönyvtárának][files_dirs] teljes elérési útja a csomóponton. | Minden feladat, kivéve az indítási tevékenységet és a feladat-előkészítési feladatot. Csak akkor érhető el, ha a feladat feladat-előkészítési feladattal van konfigurálva. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | A számítási csomópont IP-címe és portja, amelyen egy [többpéldányos feladat][multi_instance] elsődleges feladata fut. | Többpéldányos elsődleges és altevékenységek. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | Annak a csomópontnak az azonosítója, amelyhez a feladat hozzá van rendelve. | Minden feladat. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Ha `true`a jelenlegi csomópont egy dedikált csomópont. Ha `false`alacsony [prioritású csomópont.](batch-low-pri-vms.md) | Minden feladat. | `true` |
| AZ_BATCH_NODE_LIST              | A [többpéldányos feladatokhoz][multi_instance] a formátumban lefoglalt csomópontok `nodeIP;nodeIP`listája . | Többpéldányos elsődleges és altevékenységek. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | A csomópontszintű [fájlrendszer csatlakoztatási](virtual-file-mount.md) helyének teljes elérési útja, ahol az összes csatlakoztatási könyvtár található. A Windows fájlmegosztások meghajtóbetűjelet használnak, így a Windows esetében a csatlakoztatási meghajtó eszközök és meghajtók része.  |  Minden feladat, beleértve az indítási feladatot is, hozzáférhet a felhasználóhoz, mivel a felhasználó tisztában van a csatlakoztatott könyvtár csatlakoztatási engedélyeivel. | Az Ubuntuban például a hely:`/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | A csomópont összes [kötegkönyvtára][files_dirs] kinikettkönyvtárának teljes elérési útja. | Minden feladat. | C:\felhasználó\feladatok |
| AZ_BATCH_NODE_SHARED_DIR        | A [megosztott könyvtár][files_dirs] teljes elérési útja a csomóponton. A csomóponton végrehajtott összes feladat olvasási/írási hozzáféréssel rendelkezik ehhez a könyvtárhoz. A más csomópontokon végrehajtott feladatok nem rendelkeznek távoli hozzáféréssel ehhez a könyvtárhoz (ez nem "megosztott" hálózati könyvtár). | Minden feladat. | C:\felhasználó\feladatok\megosztott |
| AZ_BATCH_NODE_STARTUP_DIR       | A [csomópont kezdő feladatkönyvtárának][files_dirs] teljes elérési útja. | Minden feladat. | C:\felhasználó\feladatok\indítás |
| AZ_BATCH_POOL_ID                | Annak a készletnek azonosítója, amelyen a tevékenység fut. | Minden feladat. | batchpool001 |
| AZ_BATCH_TASK_DIR               | A [feladatkönyvtár][files_dirs] teljes elérési útja a csomóponton. Ez a `stdout.txt` könyvtár `stderr.txt` tartalmazza a és a feladathoz, valamint a AZ_BATCH_TASK_WORKING_DIR. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Az aktuális tevékenység azonosítója. | Az indítási tevékenység kivételével minden tevékenység. | feladat001 |
| AZ_BATCH_TASK_SHARED_DIR | Olyan könyvtárelérési út, amely megegyezik az elsődleges feladattal és a [többpéldányos feladat][multi_instance]minden alfeladatával. Az elérési út minden olyan csomóponton megtalálható, amelyen a többpéldányos feladat fut, és az adott csomóponton futó feladatparancsok (a [koordinációs parancs][coord_cmd] és az [alkalmazás parancsa)][app_cmd]számára elérhető olvasási/írási. A más csomópontokon végrehajtott altevékenységek vagy elsődleges feladatok nem rendelkeznek távoli hozzáféréssel ehhez a könyvtárhoz (ez nem "megosztott" hálózati könyvtár). | Többpéldányos elsődleges és altevékenységek. | C:\user\tasks\workitems\multiinstancesample job\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | A [feladat munkakönyvtárának][files_dirs] teljes elérési útja a csomóponton. Az éppen futó feladat olvasási/írási hozzáféréssel rendelkezik ehhez a könyvtárhoz. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | A [többpéldányos feladathoz][multi_instance]lefoglalt csomópontok és csomópontonkénti magok száma . A csomópontok és a magok a formátumban vannak felsorolva`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, ahol a csomópontok számát egy vagy több csomópont IP-címe és az egyes csomópontok száma követi. |  Többpéldányos elsődleges és altevékenységek. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
