---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512804"
---
Hozzunk létre egy **keresési kifejezésként**nevű titkot, amelynek a **sikere!**. A titkos kód lehet egy jelszó, egy SQL-kapcsolódási karakterlánc vagy bármely egyéb olyan információ, amelyet a biztonságos és az alkalmazás számára elérhetőnek kell tartania. 

Ha titkos kulcsot szeretne felvenni az újonnan létrehozott Key vaultba, használja az Azure CLI az kulcstartó [Secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) parancsot:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```