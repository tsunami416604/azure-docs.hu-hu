---
title: Fájlmegosztás csatlakoztatása Python függvényalkalmazásba – Azure CLI
description: Hozzon létre egy kiszolgáló nélküli Python-függvényalkalmazást, és csatoljon egy meglévő fájlmegosztást az Azure CLI használatával.
ms.topic: sample
ms.date: 03/01/2020
ms.openlocfilehash: 3d66f84d124b36f1be335c3a2204f21690510ee8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79086459"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Fájlmegosztás csatlakoztatása Python-függvényalkalmazásba az Azure CLI használatával

Ez az Azure Functions mintaparancsfájl létrehoz egy függvényalkalmazást, és létrehoz egy megosztást az Azure Files-ban. Ez őket csatlakoztatja a megosztást, hogy az adatok elérhetők legyenek a funkciók által.  

>[!NOTE]
>A létrehozott függvényalkalmazás a Python 3.7-es verzióján fut. Az Azure Functions [támogatja a Python 3.6-os és 3.8-as verzióját](../functions-reference-python.md#python-version)is.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. Példák vannak írva a Bash shell, és módosítani kell futtatni a Windows parancssorba. 

## <a name="sample-script"></a>Példaszkript

Ez a parancsfájl létrehoz egy Azure Függvényalkalmazást a [felhasználási terv](../functions-scale.md#consumption-plan)használatával.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy Azure Storage-fiókot. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Létrehoz egy Azure Files-megosztást a tárfiókban. | 
| [az tárolókönyvtár létrehozása](/cli/azure/storage/directory#az-storage-directory-create) | Könyvtárat hoz létre a megosztásban. |
| [az webapp config storage-fiók hozzáadása](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | A megosztást a függvényalkalmazáshoz csatlakoztatja. |
| [az webapp konfigurációs tárfiók listája](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | A függvényalkalmazáshoz csatlakoztatott fájlmegosztásokat jeleníti meg. | 

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
