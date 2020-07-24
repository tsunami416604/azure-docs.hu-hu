---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 07f856994b57e415ece063050b76dcd891698943
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013109"
---
A felhőalapú Python-alkalmazások hitelesítésének legegyszerűbb módja a felügyelt identitás; a részletekért tekintse meg a [app Service felügyelt identitás használata a Azure Key Vault eléréséhez](/azure/key-vault/general/managed-identity) című témakört. 

Az egyszerűség kedvéért azonban ez a rövid útmutató egy asztali alkalmazást hoz létre, amely egy egyszerű szolgáltatásnév és egy hozzáférés-vezérlési házirend használatát igényli. Az egyszerű szolgáltatásnév egyedi nevet igényel a "http:// &lt; My-Unique-Service-principal-name &gt; " formátumban.

Hozzon létre egy egyszerű szolgáltatást az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs használatával:

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

A művelet a kulcs/érték párok sorozatát fogja visszaadni.

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```