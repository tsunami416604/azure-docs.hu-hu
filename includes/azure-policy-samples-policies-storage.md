---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172883"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |
|---|---|---|---|
|[Engedélyezett tárfiók-termékváltozatok](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |Ez a szabályzat lehetővé teszi, hogy megadhatja a szervezet által üzembe helyezhető Storage Account SKU-készletet. |Megtagadás |1.0.0 |
|[Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Naplózza a nem korlátozott hálózati hozzáférést a Storage-fiók tűzfala beállításaiban. Ehelyett konfigurálja úgy a hálózati szabályokat, hogy csak az engedélyezett hálózatokból származó alkalmazások férhessenek hozzá a Storage-fiókhoz. Az adott internetről vagy helyi ügyfelekről érkező kapcsolatok engedélyezéséhez hozzáférés adható a megadott Azure-beli virtuális hálózatoktól vagy a nyilvános internetes IP-címtartományok felé irányuló forgalom számára. |Naplózás, letiltva |1.0.0 |
|[Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Ez a szabályzat lehetővé teszi a komplex veszélyforrások elleni védelmet a Storage-fiókokban. |DeployIfNotExists, letiltva |1.0.0 |
|[A Geo-redundáns tárterületet engedélyezni kell a Storage-fiókokhoz](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Ez a házirend minden olyan Storage-fiókot naplóz, amely nem engedélyezett a földrajzilag redundáns tárolással. |Naplózás, letiltva |1.0.0 |
|[A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Naplózza a biztonságos átvitelt a Storage-fiókban. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |1.0.0 |
|[A Storage-fiókoknak engedélyezniük kell a megbízható Microsoft-szolgáltatások elérését](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Bizonyos Microsoft-szolgáltatások, amelyek a storage-fiókok kezelése, amelyek nem adható hozzáférés hálózati jogcímszabályai hálózatokról működnek. Az ilyen típusú szolgáltatás munkahelyi helyesen érdekében lehetővé teszi a megbízható Microsoft-szolgáltatások megkerülése a hálózati szabályok készletét. Ezek a szolgáltatások fogja használni a tárfiók eléréséhez erős hitelesítés. |Naplózás, megtagadás, letiltva |1.0.0 |
|[A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |A Storage-fiókok új Azure Resource Manager használatával olyan biztonsági fejlesztéseket biztosíthat, mint például a következők: erősebb hozzáférés-vezérlés (RBAC), jobb auditálás, Azure Resource Manager-alapú üzembe helyezés és irányítás, felügyelt identitásokhoz való hozzáférés, hozzáférés a Key vaulthoz Secrets, Azure AD-alapú hitelesítés és a címkék és erőforráscsoportok támogatása a könnyebb biztonság kezeléséhez |Naplózás, megtagadás, letiltva |1.0.0 |
