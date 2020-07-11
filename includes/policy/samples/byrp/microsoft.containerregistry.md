---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a55cadf455de947698843bb80a3eed0564ac9891
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276471"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A tároló-beállításjegyzékeket ügyfél által felügyelt kulccsal kell titkosítani (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Az ügyfél által felügyelt kulcsokkal (CMK) rendelkező tároló-beállításjegyzékek naplózása, amelyeken nincs engedélyezve a titkosítás. A CMK-titkosítással kapcsolatos további információkért látogasson el ide: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[A tároló-beállításjegyzékek nem engedélyezhetik a nem korlátozott hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Naplózza a hálózati (IP-vagy VNET-) szabályokkal nem rendelkező tároló-nyilvántartásokat, és alapértelmezés szerint engedélyezze az összes hálózati hozzáférést. A legalább egy IP-/tűzfalszabály-vagy konfigurált virtuális hálózattal rendelkező tároló-nyilvántartások megfelelőnek tekintendők. Container Registry hálózati szabályokkal kapcsolatos további információkért látogasson el ide: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[A tároló-beállításjegyzékeknek privát hivatkozásokat kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Naplózza azokat a tároló-beállításjegyzékeket, amelyek nem rendelkeznek legalább egy jóváhagyott privát végponti kapcsolatban. A virtuális hálózatban lévő ügyfelek biztonságosan érhetik el a magánhálózati kapcsolatokon keresztül privát végponti kapcsolattal rendelkező erőforrásokat. További információért látogasson el a következő webhelyre: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ez a házirend minden olyan Container Registry naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
