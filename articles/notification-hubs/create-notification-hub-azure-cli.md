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
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: d32bae48348e482e0e175760a416097ffbc17a1c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080947"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Rövid útmutató: Azure Notification hub létrehozása az Azure CLI használatával

Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatással kapcsolatos további információkért lásd: [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Ebben a rövid útmutatóban egy értesítési központot hoz létre az Azure CLI használatával. Az első szakasz az értesítési központ névterének létrehozásának lépéseit ismerteti.  A második szakasz egy értesítési központ meglévő névtérben való létrehozásának lépéseit ismerteti.  Azt is megtudhatja, hogyan hozhat létre egyéni hozzáférési szabályzatot.  

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs az Azure CLI 2.0.67 vagy újabb verzióját igényli. Futtassa `az --version` a parancsot a telepített verzió és a függő kódtárak megkereséséhez. A telepítéshez vagy a frissítéshez lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>A környezet előkészítése

1. Bejelentkezés lehetőséget.

   Jelentkezzen be az az [login](/cli/azure/reference-index#az-login) paranccsal, ha a parancssori felület helyi telepítését használja.

    ```azurecli
    az login
    ```

    A hitelesítési folyamat befejezéséhez kövesse a terminálban megjelenő lépéseket.

2. Telepítse az Azure CLI-bővítményt.

   Ha az Azure CLI-hez készült bővítmény-referenciákkal dolgozik, először telepítenie kell a bővítményt.  Az Azure CLI-bővítmények hozzáférést biztosítanak olyan kísérleti és előzetes kiadású parancsokhoz, amelyeket még nem szállítottak el az alapszintű CLI részeként.  További információ a bővítmények frissítéséről és eltávolításáról: [bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).

   Telepítse a [Notification Hubs bővítményét](/cli/azure/ext/notification-hub/notification-hub) a következő parancs futtatásával:

    ```azurecli
    az extension add --name notification-hub
   ```

3. Hozzon létre egy erőforráscsoportot.

   Az Azure Notification Hubsokat, például az összes Azure-erőforrást, egy erőforráscsoporthoz kell telepíteni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

   Ebben a rövid útmutatóban hozzon létre egy _spnhubrg_ nevű erőforráscsoportot a _eastus_ helyen a következő az [Group Create](/cli/azure/group#az-group-create) paranccsal:

   ```azurecli
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Értesítési központ névterének létrehozása

1. Hozzon létre egy névteret az értesítési hubok számára.

   A névtér egy vagy több hubokat tartalmaz, és **a névnek egyedinek kell lennie az összes Azure-előfizetésben, és legalább hat karakter hosszúnak**kell lennie.  A név rendelkezésre állásának ellenőrzését az az [Notification-hub névtér-ellenőrzési](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) szolgáltatás elérhetősége paranccsal tekintheti meg.

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Az Azure CLI a következő konzol kimenetének megjelenítésével válaszol a rendelkezésre állásra vonatkozó kérelmére:

   ```output
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Figyelje meg az Azure CLI válaszának második sorát `"isAvailable": true` .  A sor azt fogja olvasni, hogy `false` a névtérhez megadott kívánt név elérhető-e.  Miután megerősítette a név rendelkezésre állását, futtassa az az [Notification-hub Namespace Create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) parancsot a névtér létrehozásához.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Ha az `--name` Ön által megadott `az notification-hub namespace create` parancs nem érhető el, vagy nem felel meg az [Azure-erőforrások elnevezési szabályainak és korlátozásainak](../azure-resource-manager/management/resource-name-rules.md), az Azure CLI a következő kimeneti kimenettel fog válaszolni:

   ```output
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalied
   The specified service namespace is invalid.
   ```

   Ha az utónév nem sikerült, válasszon másik nevet az új névtérnek, és futtassa `az notification-hub namespace create` újra a parancsot.

   > [!NOTE]
   > Ebből a lépésből a jelen rövid útmutatóból `--namespace` másolt összes Azure CLI-parancsban le kell cserélnie a paraméter értékét.

2. A névterek listájának beolvasása.

   Az új névtér részleteinek megtekintéséhez használja az az [Notification-hub Namespace List](/cli/azure/ext/notification-hub/notification-hub/namespace?view=azure-cli-latest#ext-notification-hub-az-notification-hub-namespace-list) parancsot.  A `--resource-group` paraméter megadása nem kötelező, ha az előfizetéshez tartozó összes névteret meg szeretné jeleníteni.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Értesítési központok létrehozása

1. Hozza létre első értesítési központját.

   Mostantól egy vagy több értesítési központ is létrehozható az új névtérben.  Futtassa az az [Notification-hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) parancsot egy értesítési központ létrehozásához.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Hozzon létre egy második értesítési központot.

   Egyetlen névtérben több értesítési központ is létrehozható.  Ha egy második értesítési központot szeretne létrehozni ugyanabban a névtérben, futtassa `az notification-hub create` újra a parancsot egy másik hub-névvel.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Az értesítési központok listájának beolvasása.

   Az Azure CLI az egyes végrehajtott parancsokkal kapcsolatos sikeres vagy hibaüzenetet ad vissza. az értesítési központok listájának lekérdezése azonban megnyugtató lehet.  Az az [Notification-hub List](/cli/azure/ext/notification-hub/notification-hub?view=azure-cli-latest#ext-notification-hub-az-notification-hub-list) parancs erre a célra lett tervezve.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-notification-hub-access-policies"></a>Az értesítési központ hozzáférési házirendjeinek használata

1. Egy értesítési központ hozzáférési szabályzatának listázása.

   Az Azure Notification Hubs a hozzáférési házirendek használatával [közös hozzáférés-aláírási biztonságot](./notification-hubs-push-notification-security.md) használ.  Értesítési központ létrehozásakor a rendszer automatikusan létrehoz két házirendet.  A leküldéses értesítések konfigurálásához a szabályzatok kapcsolódási karakterláncai szükségesek.  Az az [Notification-hub Authorization-Rule List](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) parancs a szabályzatok és a hozzájuk tartozó erőforráscsoportok listáját tartalmazza.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Ne használja az _DefaultFullSharedAccessSignature_ szabályzatot az alkalmazásban. Ezt csak a háttérben használhatja.  Csak `Listen` hozzáférési házirendeket használjon az ügyfélalkalmazás számára.

2. Új engedélyezési szabály létrehozása az értesítési Központ számára.

   Ha szeretne további engedélyezési szabályokat létrehozni értelmes nevekkel, a saját hozzáférési házirendjét az az [Notification-hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) paranccsal hozhatja létre és szabhatja testre.  A `--rights` paraméter a hozzárendelni kívánt engedélyek szóközzel tagolt listája.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Értesítési központ hozzáférési házirendjéhez tartozó kulcsok és kapcsolati karakterláncok listázása

   Az egyes hozzáférési házirendekhez két kulcs és kapcsolati karakterlánc van.  Az [értesítési központ konfigurálásához](./configure-notification-hub-portal-pns-settings.md)később szüksége lesz rájuk.  Az értesítési központ hozzáférési házirendjéhez tartozó kulcsok és kapcsolatok karakterláncok listázásához használja az az [Notification-hub Authorization-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) parancsot.

   ```azurecli
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   #query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Az [értesítési központ névtere](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) és az [értesítési központ](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) külön hozzáférési házirendekkel rendelkezik.  Győződjön meg arról, hogy a megfelelő Azure CLI-hivatkozást használja a kulcsok és a kapcsolódási karakterláncok lekérdezéséhez.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást.

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>További lépések

* Ebben a rövid útmutatóban létrehozott egy értesítési központot. Ha szeretné megtudni, hogyan konfigurálhatja az elosztót a platform Notification System (PNS) beállításokkal, olvassa el a [leküldéses értesítések beállítása értesítési központban](configure-notification-hub-portal-pns-settings.md) című témakört.

* Ismerje meg az értesítési központok Azure CLI-vel való felügyeletének széles körű képességeit.

  [Notification Hubs teljes referenciák listája](/cli/azure/ext/notification-hub/notification-hub)

  [Notification Hubs névtér-hivatkozási lista](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Notification Hubs engedélyezési szabály hivatkozási listája](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Notification Hubs hitelesítő adatok hivatkozási listája](/cli/azure/ext/notification-hub/notification-hub/credential)
