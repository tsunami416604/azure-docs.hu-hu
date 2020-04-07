---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 44823ce888e97b308f29403612f598c0eb585ae5
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673367"
---
A várólista az [Azure Portalon](../articles/storage/queues/storage-quickstart-queues-portal.md) vagy a [Microsoft Azure Storage Explorerben](https://storageexplorer.com/)tekinthető meg. A várólista az Azure CLI-ben is megtekinthető, a következő lépésekben leírtak szerint:

1. Nyissa meg a függvényprojekt *local.setting.json* fájlját, és másolja a kapcsolati karakterlánc értékét. Terminál- vagy parancsablakban futtassa a következő `AZURE_STORAGE_CONNECTION_STRING`parancsot a program névre `<MY_CONNECTION_STRING>`szóló környezeti változó létrehozásához, és illessze be az adott kapcsolati karakterláncot a helyére. (Ez a környezeti változó azt jelenti, hogy nem kell megadnia a kapcsolati karakterláncot minden további parancshoz az `--connection-string` argumentum használatával.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Nem kötelező) A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) parancs segítségével megtekintheti a storage várólistákat a fiókjában. A parancs kimenetének tartalmaznia `outqueue`kell egy várólistát, amely akkor jött létre, amikor a függvény az első üzenetét a várólistára írta.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. A [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) parancs segítségével olvassa el az üzenetet ebből a várólistából, amely a funkció korábbi tesztelésekén használt első név nek kell lennie. A parancs beolvassa és eltávolítja az első üzenetet a várólistából. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Ez a parancsfájl a certutil segítségével dekódolja a base64 kódolású üzenetgyűjteményt egy helyi ideiglenes fájlból. Ha nincs kimenet, próbálja meg `> NUL` eltávolítani a parancsfájlból a certutil kimenet letiltásához, ha hiba történik. 
    
    ---
    
    Mivel az üzenet törzse [base64 kódolású,](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)az üzenetet dekódolni kell, mielőtt megjelenik. A végrehajtás `az storage message get`után az üzenet törlődik a várólistából. Ha csak egy üzenet `outqueue`volt a alkalmazásban, akkor a parancs második futtatásakor nem fog beolvasni egy üzenetet, és ehelyett hibaüzenetet kap.