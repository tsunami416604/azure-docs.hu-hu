---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/13/2020
ms.author: dacoulte
ms.openlocfilehash: 81783becd727eb1aef415f539ddb10a5a124fd70
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367574"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |Forrás |
|---|---|---|---|
|[A tároló-beállításjegyzékeket ügyfél által felügyelt kulccsal kell titkosítani (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Az ügyfél által felügyelt kulcsokkal (CMK) rendelkező tároló-beállításjegyzékek naplózása, amelyeken nincs engedélyezve a titkosítás. A CMK-titkosítással kapcsolatos további információkért látogasson el ide: https://aka.ms/acr/CMK. |Naplózás, letiltva |1.0.0 – előzetes verzió |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[A tároló-beállításjegyzékek nem engedélyezhetik a nem korlátozott hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Naplózza a hálózati (IP-vagy VNET-) szabályokkal nem rendelkező tároló-nyilvántartásokat, és alapértelmezés szerint engedélyezze az összes hálózati hozzáférést. A legalább egy IP-/tűzfalszabály-vagy konfigurált virtuális hálózattal rendelkező tároló-nyilvántartások megfelelőnek tekintendők. Container Registry hálózati szabályokkal kapcsolatos további információkért tekintse meg a következőt: https://aka.ms/acr/vnet. |Naplózás, letiltva |1.0.0 – előzetes verzió |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
