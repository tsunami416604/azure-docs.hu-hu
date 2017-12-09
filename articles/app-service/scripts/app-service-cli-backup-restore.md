---
title: "Az Azure CLI parancsfájl minta - visszaállítást egy biztonsági másolatból webalkalmazás |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - visszaállítást egy biztonsági másolatból webes alkalmazás"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 12/07/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: bec915f3e321cf2422f7cc2cad3ce1dfb6b3317a
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="restore-a-web-app-from-a-backup"></a>A webes alkalmazás visszaállítása biztonsági másolatból

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service azok kapcsolódó erőforrásait, és egyszeri biztonsági másolatot a hozza létre. 

A parancsfájl futtatásával szüksége meglévő biztonsági mentésből egy webalkalmazás. Szeretne létrehozni egyet, lásd: [kész a webalkalmazás biztonsági mentési](app-service-cli-backup-onetime.md) vagy [ütemezett biztonsági mentés egy webalkalmazás létrehozása](app-service-cli-backup-scheduled.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, az Azure parancssori felület verzió szüksége 2.0-s vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_list) | A webalkalmazás biztonsági mentések listájának lekérése. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_restore) | A webes alkalmazás visszaállítása egy biztonsági másolatból. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure App Service-dokumentáció](../app-service-cli-samples.md).
