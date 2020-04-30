---
title: Rövid útmutató – Azure Notification hub létrehozása az Azure CLI használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure Notification hub-t az Azure CLI használatával.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082450"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Rövid útmutató: Azure Notification hub létrehozása az Azure CLI használatával

Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatással kapcsolatos további információkért lásd: [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Ebben a rövid útmutatóban egy értesítési központot hoz létre az Azure CLI használatával. Az első szakaszban megtudhatja, hogyan hozhat létre egy Notification hub-névteret, és hogyan kérdezheti le a névtér hozzáférési szabályzatának adatait. A második szakasz egy értesítési központ meglévő névtérben való létrehozásának lépéseit ismerteti.  Azt is megtudhatja, hogyan hozhat létre egyéni hozzáférési szabályzatot.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs az Azure CLI 2.0.67 vagy újabb verzióját igényli. Futtassa `az --version` a parancsot a telepített verzió és a függő kódtárak megkereséséhez. A telepítéshez vagy a frissítéshez lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>A környezet előkészítése

1. Bejelentkezés lehetőséget.

   Jelentkezzen be az az [login](/cli/azure/reference-index#az-login) paranccsal, ha a parancssori felület helyi telepítését használja.

    ```azurecli-interactive
    az login
    ```

    A hitelesítési folyamat befejezéséhez kövesse a terminálban megjelenő lépéseket.

2. Telepítse az Azure CLI-bővítményt.

   Az értesítési központokhoz tartozó Azure CLI-parancsok futtatásához telepítse a Notification Hubs Azure CLI- [bővítményét](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Hozzon létre egy erőforráscsoportot.

   Az Azure értesítési központokat, például az összes Azure-erőforrást egy erőforráscsoporthoz kell telepíteni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

   Ebben a rövid útmutatóban hozzon létre egy *spnhubrg* nevű erőforráscsoportot a *eastus* helyen a következő az [Group Create](/cli/azure/group#az-group-create) paranccsal:

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Értesítési központ névterének létrehozása

1. Névtér létrehozása az értesítési központok számára

   A névtér egy vagy több hubokat tartalmaz, és a névnek egyedinek kell lennie az összes Azure-előfizetésben.  Az adott szolgáltatás névterének rendelkezésre állásának vizsgálatához használja az az [Notification-hub Namespace sakk-elérhetőség](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) parancsot.  Hozzon létre egy névteret az az [Notification-hub névtér-létrehozási](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) parancs futtatásával.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. A névtér-hozzáférési házirend kulcsainak és kapcsolati karakterláncának listázása.

   A rendszer automatikusan létrehoz egy **RootManageSharedAccessKey** nevű hozzáférési szabályzatot egy új névtérhez.  Minden hozzáférési házirend két kulccsal és kapcsolati karakterláncmal rendelkezik.  A névtér kulcsai és a kapcsolódási karakterláncok listázásához futtassa az az [Notification-hub Namespace Authorization-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) parancsot.

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Értesítési központok létrehozása

1. Hozza létre első értesítési központját.

   Most már létrehozhat egy értesítési központot az új névtérben.  Futtassa az az [Notification-hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) parancsot egy értesítési központ létrehozásához.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Hozzon létre egy második értesítési központot.

   Egyetlen névtérben több értesítési központ is létrehozható.  Ha egy második értesítési központot szeretne létrehozni ugyanabban a névtérben, futtassa újra a `az notification-hub create` parancsot egy másik hub-névvel.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Hozzáférési szabályzatok használata

1. Új engedélyezési szabály létrehozása az értesítési Központ számára.

   Minden új értesítési központ automatikusan létrejön egy hozzáférési szabályzat.  Saját hozzáférési szabályzat létrehozásához és testreszabásához használja az az [Notification-hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) parancsot.

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Egy értesítési központ hozzáférési szabályzatának listázása.

   Ha meg szeretné kérdezni, hogy milyen hozzáférési házirendek léteznek az értesítési központhoz, használja az az [Notification-hub Authorization-Rule List](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) parancsot.

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Ne használja az **DefaultFullSharedAccessSignature** szabályzatot az alkalmazásban. Ezt csak a háttérben használhatja.  Az ügyfélalkalmazás csak a **figyelési** hozzáférési szabályzatokat használja.

3. Értesítési központ hozzáférési házirendjéhez tartozó kulcsok és kapcsolati karakterláncok listázása

   Az egyes hozzáférési házirendekhez két kulcs és kapcsolati karakterlánc van.  A leküldéses értesítések kezeléséhez később szüksége lesz rájuk.  Az értesítési központ hozzáférési házirendjéhez tartozó kulcsok és kapcsolatok karakterláncok listázásához használja az az [Notification-hub Authorization-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) parancsot.

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Az [értesítési központ névtere](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) és az [értesítési központ](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) külön hozzáférési házirendekkel rendelkezik.  Győződjön meg arról, hogy a megfelelő Azure CLI-hivatkozást használja a kulcsok és a kapcsolódási karakterláncok lekérdezéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Lásd még

Ismerje meg az értesítési központok Azure CLI-vel való kezelésének teljes képességeit.

* [Notification Hubs teljes Azure CLI-hivatkozási lista](/cli/azure/ext/notification-hub/notification-hub)
* [Notification Hubs névtér Azure CLI-hivatkozási listája](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Notification Hubs engedélyezési szabály Azure CLI-referenciák listája](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Notification Hubs hitelesítő adatok Azure CLI-referenciák listája](/cli/azure/ext/notification-hub/notification-hub/credential)
