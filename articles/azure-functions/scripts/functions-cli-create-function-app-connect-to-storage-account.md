---
title: Function-alkalmazás létrehozása csatlakoztatott tárolóval – Azure CLI
description: Azure CLI-példaszkript – Egy Azure Storage-hoz kapcsolódó Azure-függvény létrehozása
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc
ms.openlocfilehash: 9c2a70066e673bfa164a58884891f29e220c7bd6
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532833"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Function-alkalmazás létrehozása névvel ellátott Storage-fiókkal létesített kapcsolatban 

Ez az Azure Functions-példaszkript létrehoz egy függvényalkalmazást, amelyet hozzákapcsol egy Azure Storage-fiókhoz. A létrehozott alkalmazásbeállítás, amely a kapcsolatot tartalmazza, egy [tárolási eseményindítóval vagy kötéssel](../functions-bindings-storage-blob.md) együtt használható. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a helyi CLI-t használja, akkor az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

Ez a példa létrehoz egy Azure-függvényalkalmazást, és hozzáadja a tároló kapcsolati sztringjét egy alkalmazásbeállításhoz.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot egy helyen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tárfiók létrehozása. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást a kiszolgáló nélküli [használatalapú csomagban](../functions-scale.md#consumption-plan). |
| [az Storage Account show-kapcsolat-string](/cli/azure/storage/account#az-storage-account-show-connection-string) | Lekéri a fiók kapcsolati sztringjét. |
| [az functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | A függvény alkalmazásban beállíthatja a kapcsolatok karakterláncát. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
