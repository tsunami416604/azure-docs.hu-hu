---
title: Azure CLI-példaszkript – Webalkalmazás visszaállítása biztonsági másolatból | Microsoft Docs
description: Azure CLI-példaszkript – Webalkalmazás visszaállítása biztonsági másolatból
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu;cephalin
ms.custom: seodec18
ms.openlocfilehash: a2c8c3568dcdafee3039d6ec1a198fab255af528
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183335"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>Webalkalmazás visszaállítása biztonsági másolatból parancssori felület használatával

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd egyszeri biztonsági mentést hajt végre. 

A szkript futtatásához szükség van egy webalkalmazás meglévő biztonsági másolatára. Ennek létrehozásáról lásd: [Webalkalmazások biztonsági mentése](app-service-cli-backup-onetime.md) vagy [Ütemezett biztonsági másolat létrehozása egy webalkalmazásról](app-service-cli-backup-scheduled.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-restore) | Visszaállít egy webalkalmazást egy biztonsági másolatból. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../app-service-cli-samples.md) találhat.
