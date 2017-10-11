---
title: "Az Azure Batch számítási csomópont környezeti változók |} Microsoft Docs"
description: "Számítási csomópont környezeti változó hivatkozás az Azure Batch használatával."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/05/2017
ms.author: tamram
ms.openlocfilehash: 29f642754430957e77ef68946f721f8e15dba065
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-batch-compute-node-environment-variables"></a>Azure Batch számítási csomópont környezeti változók
A [Azure Batch szolgáltatás](https://azure.microsoft.com/services/batch/) állítja be az alábbi környezeti változókat a számítási csomópontok. Ezek a feladat parancssori parancsokat, és a Programok telepítése és a környezeti változók is hivatkozni, és parancsfájlok futtatásához a parancssorok.

Környezeti változók kötegelt használatával kapcsolatos további információkért lásd: [környezeti beállítások feladatok](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Környezeti változó látható

Ezek a környezeti változók csak kontextusában láthatók a **feladat felhasználói**, a felhasználói fiók a csomóponton, amely alatt a feladat végrehajtása. Ha RDP vagy SSH segítségével [távolról csatlakozik](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a számítási csomóponthoz, ezek a környezeti változók *nem* fognak megjelenni. Ez azért van, mert a távoli kapcsolat kialakításához használt felhasználói fiók nem egyezik a tevékenységhez használt fiókkal.

## <a name="command-line-expansion-of-environment-variables"></a>Környezeti változók parancssori bővítése

A parancssorok hajtja végre a feladatokat a számítási csomópontok tegye nem futtathatók a rendszerhéj. Ezért sorok az nem natív módon kihasználni a rendszerhéj-szolgáltatások, például a környezeti változók bővítésének (Ez magában foglalja a `PATH`). Ezek a funkciók előnyeit, kell **meghívása a rendszerhéj** a parancssorban. Például elindíthatja `cmd.exe` Windows a számítási csomópontok vagy `/bin/sh` Linux csomópontján:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Környezeti változók

| Változó neve                     | Leírás                                                              | Rendelkezésre állás | Példa |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | A Batch-fiók, amely a feladat tartozik neve.                  | Minden feladat.   | mybatchaccount |
| AZ_BATCH_CERTIFICATES_DIR       | A címtárhoz az a [feladatütemezési munkakönyvtár] [ files_dirs] Linux rendszer tárolja a tanúsítványok a számítási csomópontok. Vegye figyelembe, hogy a környezeti változó nem vonatkozik a Windows számítási csomópontjain.                                                  | Minden feladat.   |  /mnt/Batch/Tasks/workitems/batchjob001/Job-1/task001/certs |
| AZ_BATCH_JOB_ID                 | Annak a fióknak az azonosítója, amelyhez a feladat tartozik. | Minden olyan feladat, kivéve a feladat indítása. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | A teljes elérési útját a feladat előkészítése [feladat directory] [ files_dirs] a csomóponton. | Kezdő tevékenység és a feladat előkészítése tevékenységet kívül az összes feladatot. Csak akkor érhető el, ha a feladat a feladat előkészítése tevékenység van konfigurálva. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | A teljes elérési útját a feladat előkészítése [feladatütemezési munkakönyvtár] [ files_dirs] a csomóponton. | Kezdő tevékenység és a feladat előkészítése tevékenységet kívül az összes feladatot. Csak akkor érhető el, ha a feladat a feladat előkészítése tevékenység van konfigurálva. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | A feladat be van-e rendelve csomópont azonosítója. | Minden feladat. | TVM-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | A teljes elérési útját a legfelső szintű az összes [Batch-könyvtárak] [ files_dirs] a csomóponton. | Minden feladat. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | A teljes elérési útját a [megosztott könyvtár] [ files_dirs] a csomóponton. Minden olyan feladat, amely a csomóponton végre rendelkezik olvasási/írási hozzáférést a könyvtárhoz. Feladatokat, amelyek a többi csomóponton végre nem rendelkeznek táveléréssel (nincs olyan "megosztott" hálózati könyvtár) a könyvtárba. | Minden feladat. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | A teljes elérési útját a [indítsa el a feladat directory] [ files_dirs] a csomóponton. | Minden feladat. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Annak a készletnek azonosítója, amelyen a tevékenység fut. | Minden feladat. | batchpool001 |
| AZ_BATCH_TASK_DIR               | A teljes elérési útját a [feladat directory] [ files_dirs] a csomóponton. Ez a könyvtár tartalmaz a `stdout.txt` és `stderr.txt` a tevékenységhez, és a AZ_BATCH_TASK_WORKING_DIR. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Az aktuális tevékenység azonosítója. | Minden olyan feladat, kivéve a feladat indítása. | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | A teljes elérési útját a [feladatütemezési munkakönyvtár] [ files_dirs] a csomóponton. A jelenleg futó feladat rendelkezik olvasási/írási hozzáférést a könyvtárhoz. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Csomópont lefoglalt magok száma és csomópontok listáját egy [többpéldányos feladat][multi_instance]. Csomópontok és -magok felsorolt formátumban`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, ahol a csomópontok számát követi egy vagy több csomópont IP-címek és az magok száma az egyes. |  Többpéldányos elsődleges és részfeladatok. |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | A lefoglalt csomópontlista egy [többpéldányos feladat] [ multi_instance] formátumú `nodeIP;nodeIP`. | Többpéldányos elsődleges és részfeladatok. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | A lefoglalt csomópontlista egy [többpéldányos feladat] [ multi_instance] formátumú `nodeIP,nodeIP`. | Többpéldányos elsődleges és részfeladatok. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | Az IP-cím és port a számítási csomópont, amelyen az elsődleges feladatát egy [többpéldányos feladat] [ multi_instance] futtatja. | Többpéldányos elsődleges és részfeladatok. | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | A könyvtár elérési útja, amely azonos az elsődleges feladat és minden vonatkozó részfeladatnál annak regisztrálása egy [többpéldányos feladat][multi_instance]. Az elérési út létezik minden csomóponton, amelyen a többpéldányos feladat fut, és elérhető ezen a csomóponton futó feladat parancsok olvasási/írási (mind a [koordinációs parancs] [ coord_cmd] és a [ alkalmazás parancs][app_cmd]). Résztevékenység vagy egy elsődleges feladatot, amely a többi csomóponton végre nem rendelkezik távoli hozzáférést a könyvtárhoz (nincs "megosztott" hálózati könyvtárban). | Többpéldányos elsődleges és részfeladatok. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Megadja, hogy az aktuális csomópont-e a fő csomópont egy [többpéldányos feladat][multi_instance]. A lehetséges értékek: `true` és `false`.| Többpéldányos elsődleges és részfeladatok. | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | Ha `true`, az aktuális csomópont dedikált csomópontja. Ha `false`, ez egy [alacsony prioritású csomópont](batch-low-pri-vms.md). | Minden feladat. | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
