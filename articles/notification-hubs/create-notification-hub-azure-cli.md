---
title: Rövid útmutató – Azure-értesítési központ létrehozása az Azure CLI használatával | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy Azure-értesítési központot az Azure CLI használatával.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082450"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Rövid útmutató: Hozzon létre egy Azure értesítési központot az Azure CLI használatával

Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatásról további információt a [Mi az Azure Értesítési központ?](notification-hubs-push-notification-overview.md)

Ebben a rövid útmutatóban hozzon létre egy értesítési központot az Azure CLI használatával. Az első szakasz az értesítési központ névterének létrehozásához és a névtér hozzáférési házirend-adatainak lekérdezéséhez szükséges lépéseket tartalmazza. A második szakasz lépéseket ad egy értesítési központ létrehozásához egy meglévő névtérben.  Azt is megtudhatja, hogyan hozhat létre egyéni hozzáférési szabályzatot.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Az Értesítési központok hoz az Azure CLI 2.0.67-es vagy újabb verzióját igényli. Futtassa `az --version` a telepített verzió- és függő kódtárak megkereséséhez. A telepítéshez vagy frissítéshez olvassa [el az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört.

## <a name="prepare-your-environment"></a>A környezet előkészítése

1. Bejelentkezés lehetőséget.

   Jelentkezzen be az [az bejelentkezési](/cli/azure/reference-index#az-login) paranccsal, ha a CLI helyi telepítését használja.

    ```azurecli-interactive
    az login
    ```

    A hitelesítési folyamat befejezéséhez kövesse a terminálon megjelenő lépéseket.

2. Telepítse az Azure CLI-bővítményt.

   Az Azure CLI-parancsok értesítési központokhoz való futtatásához telepítse az Azure CLI [bővítményt az értesítési központokhoz.](/cli/azure/ext/notification-hub/notification-hub)  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Hozzon létre egy erőforráscsoportot.

   Az Azure értesítési központokat, az összes Azure-erőforráshoz hasonlóan, egy erőforráscsoportba kell telepíteni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

   Ehhez a rövid útmutatóhoz hozzon létre egy *spnhubrg* nevű erőforráscsoportot az *eastus* helyen a következő [az csoport létrehozási](/cli/azure/group#az-group-create) paranccsal:

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Értesítési központ névtere létrehozása

1. Névtér létrehozása az értesítési központokszámára

   A névtér egy vagy több hubot tartalmaz, és a névnek egyedinek kell lennie az összes Azure-előfizetésben.  Az adott szolgáltatásnévtér elérhetőségének ellenőrzéséhez használja az [az notification-hub névtér ellenőrzési-elérhetőségi](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) parancsot.  Futtassa az [az notification-hub névtér create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) parancsot egy névtér létrehozásához.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Listakulcsok és kapcsolati karakterláncok a névtér-hozzáférési házirendhez.

   A rendszer automatikusan létrehoz egy **RootManageSharedAccessKey** nevű hozzáférési házirendet egy új névtérhez.  Minden hozzáférési szabályzat két kulcskészlettel és kapcsolati karakterlánccal rendelkezik.  A névtér kulcsainak és kapcsolati karakterláncainak listázásához futtassa az [az notification-hub névtér engedélyezési szabály listakulcsok](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) parancsot.

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Értesítési központok létrehozása

1. Hozza létre az első értesítési központot.

   Most már létrehozhat egy értesítési központot az új névtérben.  Futtassa az [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) parancsot egy értesítési központ létrehozásához.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Hozzon létre egy második értesítési központot.

   Egyetlen névtérben több értesítési központ is létrehozható.  Ha ugyanabban a névtérben szeretne második `az notification-hub create` értesítési központot létrehozni, futtassa újra a parancsot egy másik hubnév használatával.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>A hozzáférési házirendek használata

1. Hozzon létre egy új engedélyezési szabályt egy értesítési központhoz.

   Minden új értesítési központhoz automatikusan létrejön egy hozzáférési szabályzat.  Saját hozzáférési szabályzat létrehozásához és testreszabásához használja az [az notification-hub engedélyezési szabály létrehozási](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) parancsát.

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Az értesítési központ hozzáférési házirendjeinek listázása.

   Ha le szeretné kérdezni, hogy milyen hozzáférési házirendek léteznek egy értesítési központhoz, használja az [az notification-hub engedélyezési szabály lista](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) parancsát.

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Ne használja a **DefaultFullSharedAccessSignature** házirendet az alkalmazásban. Ezt csak a háttérben használhatja.  Csak az ügyfélalkalmazásban használja a **figyelési** hozzáférési házirendeket.

3. Kulcsok és kapcsolati karakterláncok listázása értesítési központ hozzáférési házirendjéhez

   Minden hozzáférési házirendhez két kulcskészlet és kapcsolati karakterlánc tartoz.  A leküldéses értesítések kezeléséhez később szüksége lesz rájuk.  Az értesítési központ hozzáférési házirendjének kulcsai és kapcsolati karakterláncainak listázásához használja az [az notification-hub engedélyezési szabály listakulcsok](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) parancsát.

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Az [értesítési központ névtere](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) és [az értesítési központ](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) külön hozzáférési szabályzatokkal rendelkezik.  Győződjön meg arról, hogy a megfelelő Azure CLI-hivatkozást használja a kulcsok és a kapcsolati karakterláncok lekérdezésekekénél.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az csoport törlési](/cli/azure/group) parancsával távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Lásd még

Fedezze fel az értesítési központok azure CLI-vel való kezeléséhez szükséges teljes lehetőségeket.

* [Értesítési központok teljes Azure CLI-referencialista](/cli/azure/ext/notification-hub/notification-hub)
* [Értesítési központok névtere Az Azure CLI hivatkozási listája](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Értesítési központok engedélyezési szabály Az Azure CLI hivatkozási listája](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Értesítési központok hitelesítő adatok Azure CLI hivatkozási listája](/cli/azure/ext/notification-hub/notification-hub/credential)
