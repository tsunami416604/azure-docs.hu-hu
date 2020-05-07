---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 20b78a883b71e7ea6d24097a5166f6d4a12d4d77
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838057"
---
|Name |Description |Hatás (ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A tároló-beállításjegyzékeket ügyfél által felügyelt kulccsal kell titkosítani (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Az ügyfél által felügyelt kulcsokkal (CMK) rendelkező tároló-beállításjegyzékek naplózása, amelyeken nincs engedélyezve a titkosítás. A CMK-titkosítással kapcsolatos további információkért látogasson [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)el ide:. |Naplózás, letiltva |1.0.0 – előzetes verzió |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[A tároló-beállításjegyzékek nem engedélyezhetik a nem korlátozott hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Naplózza a hálózati (IP-vagy VNET-) szabályokkal nem rendelkező tároló-nyilvántartásokat, és alapértelmezés szerint engedélyezze az összes hálózati hozzáférést. A legalább egy IP-/tűzfalszabály-vagy konfigurált virtuális hálózattal rendelkező tároló-nyilvántartások megfelelőnek tekintendők. Container Registry hálózati szabályokkal kapcsolatos további információkért látogasson el ide [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet):. |Naplózás, letiltva |1.0.0 – előzetes verzió |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[A tároló-beállításjegyzékeknek privát hivatkozásokat kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Naplózza azokat a tároló-beállításjegyzékeket, amelyek nem rendelkeznek legalább egy jóváhagyott privát végponti kapcsolatban. A virtuális hálózatban lévő ügyfelek biztonságosan érhetik el a magánhálózati kapcsolatokon keresztül privát végponti kapcsolattal rendelkező erőforrásokat. További információért látogasson el a [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link)következő webhelyre:. |Naplózás, letiltva |1.0.0 – előzetes verzió |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
