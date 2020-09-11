---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a49c45600096da787e4ef33d0930fd3d5099d297
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90005493"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A tároló-beállításjegyzékeket ügyfél által felügyelt kulccsal kell titkosítani (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Az ügyfél által felügyelt kulcsokkal (CMK) rendelkező tároló-beállításjegyzékek naplózása, amelyeken nincs engedélyezve a titkosítás. Az Azure automatikusan titkosítja a beállításjegyzék tartalmát a szolgáltatás által felügyelt kulcsokkal. Egy további titkosítási réteggel kiegészítheti az alapértelmezett titkosítást a Azure Key Vault-ben létrehozott és kezelt kulcs használatával. A CMK-titkosítással kapcsolatos további információkért látogasson el ide: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[A tároló-beállításjegyzékek nem engedélyezhetik a nem korlátozott hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Naplózza azokat a tároló-beállításjegyzékeket, amelyek nincsenek konfigurálva hálózati vagy tűzfal-(IP-) szabályok, így alapértelmezés szerint minden hálózati hozzáférés engedélyezve lesz. A hálózati hozzáférés korlátozása védi a tároló-beállításjegyzéket az esetleges fenyegetésektől. A legalább egy IP-/tűzfalszabály-vagy konfigurált virtuális hálózattal rendelkező tároló-nyilvántartások megfelelőnek tekintendők. A Container Registry hálózati szabályokkal kapcsolatos további információkért látogasson el a következő webhelyre: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) és [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[A tároló-beállításjegyzékeknek privát hivatkozásokat kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Naplózza azokat a tároló-beállításjegyzékeket, amelyek nem rendelkeznek legalább egy jóváhagyott privát végponti kapcsolatban. A virtuális hálózatban lévő ügyfelek biztonságosan érhetik el a magánhálózati kapcsolatokon keresztül privát végponti kapcsolattal rendelkező erőforrásokat. A nyilvános hozzáférés letiltható, így biztosítható, hogy csak a magánhálózati kapcsolatok legyenek használhatók a beállításjegyzékhez való csatlakozáshoz. További információért látogasson el a következő webhelyre: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
