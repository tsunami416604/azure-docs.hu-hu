---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: deb2eb877743d533c5daeee8b6636edd62418fe0
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89494303"
---
A felhőalapú alkalmazások hitelesítésének legegyszerűbb módja a felügyelt identitás; a részletekért lásd: [hitelesítés Key Vault](/azure/key-vault/general/authentication) .

Az egyszerűség kedvéért azonban ez a rövid útmutató egy asztali alkalmazást hoz létre, amely egy egyszerű szolgáltatásnév és egy hozzáférés-vezérlési házirend használatát igényli. Az egyszerű szolgáltatásnév egyedi nevet igényel a "http:// &lt; My-Unique-Service-principal-name &gt; " formátumban.

Hozzon létre egy egyszerű szolgáltatást az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs használatával:

```azurecli
az ad sp create-for-rbac --skip-assignment -n "http://<my-unique-service-principal-name>" --sdk-auth
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
