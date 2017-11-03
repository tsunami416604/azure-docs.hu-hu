---
title: "Az Azure CLI parancsfájl minta - fut egy feladat kötegelt |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - kötegelt feladat fut"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: 73d93622d418359be421e043d0af4e4befc6f4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Azure Batch Azure parancssori felülettel futó feladatok

Ezt a parancsfájlt hoz létre egy kötegelt és feladatok sorozata ad hozzá a feladatot. Azt is bemutatja, hogyan kell egy feladat és a feladatok figyelése. Végül azt jeleníti meg a Batch szolgáltatás hatékonyan kapcsolatos feladatokról további információk a feladat lekérdezése.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse az Azure parancssori felület használatával a utasításokat a [Azure parancssori felület telepítési útmutató](https://docs.microsoft.com/cli/azure/install-azure-cli), ha még nem tette meg.
- Batch-fiók létrehozása, ha még nem rendelkezik. Lásd: [Batch-fiók létrehozása az Azure parancssori felülettel](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) számára egy fiókot hoz létre parancsfájlt.
- Ha még nem még meg egy kezdő tevékenység-ről futtatva alkalmazások konfigurálása. Lásd: [hozzáadása az Azure CLI Azure Batch-alkalmazások](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) egy minta parancsfájlt, amelynek alkalmazást hoz létre, és egy alkalmazáscsomag feltöltését az Azure-bA.
- Konfigurálja a készletben, amelyen a feladat futni fog. Lásd: [kezelése Azure Batch készletek Azure parancssori felülettel](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool) egy parancsfájlt hoz létre a készlet egy felhőalapú szolgáltatás konfigurációja vagy a virtuálisgép-konfiguráció számára.

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-job"></a>Feladat tisztítása

Miután lefuttatta a fenti minta parancsfájlt, a következő paranccsal távolítsa el a feladatot, és a feladatokat. Vegye figyelembe, hogy a készlet külön törölni kell. Lásd: [kezelése Azure Batch készletek Azure parancssori felülettel](./batch-cli-sample-manage-pool.md) létrehozása és törlése készletek olvashat.

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy kötegelt és a feladatok létrehozásához. Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az batch-fiók bejelentkezési](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Batch-fiók hitelesítése.  |
| [hozza létre az kötegelt](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | Létrehoz egy kötegelt feladatot.  |
| [az kötegelt feladat beállítása](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | A kötegelt frissítések tulajdonságai.  |
| [az kötegelt feladat megjelenítése](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | Lekérdezi a megadott kötegelt részleteit.  |
| [az kötegelt feladat létrehozása](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | A megadott kötegelt ad hozzá egy feladatot.  |
| [az kötegelt feladat megjelenítése](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | Lekérdezi a megadott kötegelt feladat részletes adatait.  |
| [az kötegelt tevékenység listája](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_list) | A megadott feladathoz tartozó feladatokat sorolja fel.  |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További kötegelt CLI parancsfájl minták megtalálhatók a [Azure Batch CLI dokumentáció](../batch-cli-samples.md).
