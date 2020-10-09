---
title: Azure Key Vault naplózása | Microsoft Docs
description: Megtudhatja, hogyan figyelheti a kulcstartók hozzáférését úgy, hogy engedélyezi a Azure Key Vault naplózását, amely az Ön által megadott Azure Storage-fiókban tárolja az adatokat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: a51e9a628f67269357d42bd1d3af10c1d86f301a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739782"
---
# <a name="azure-key-vault-logging"></a>Az Azure Key Vault naplózása

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egy vagy több kulcstartó létrehozása után valószínűleg figyelnie kell a kulcstartók elérésének módját és időpontját. Ezt úgy teheti meg, hogy engedélyezi a Azure Key Vault naplózását, amely az Ön által megadott Azure Storage-fiókban tárolja az adatokat. A megadott Storage-fiókhoz automatikusan létrejön egy elemzések – **naplók – AuditEvent** nevű új tároló. Ugyanazt a Storage-fiókot használhatja a naplók több kulcstartóhoz való gyűjtéséhez.

A naplózási adatokat a Key Vault művelet után 10 perccel (legfeljebb) érheti el. A legtöbb esetben azonban ez nem fog ennyi ideig tartani.  A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.

További információ a Key Vaultről: [Mi az Azure Key Vault?](overview.md)). További információ a Key Vault rendelkezésre állásáról: [díjszabási oldal](https://azure.microsoft.com/pricing/details/key-vault/). További információ a [Key Vault Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview)használatáról.

## <a name="interpret-your-key-vault-logs"></a>A Key Vault naplóinak értelmezése

Az egyes blobok JSON-blobként, szöveges formában vannak tárolva. Nézzük meg egy példa naplóbejegyzést. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

A következő táblázat a mezőneveket és a leírásokat tartalmazza:

| Mező neve | Leírás |
| --- | --- |
| **idő** |Dátum és idő (UTC). |
| **resourceId** |Azure Resource Manager erőforrás-azonosító. Key Vault naplók esetében ez mindig a Key Vault erőforrás-azonosító. |
| **operationName** |A művelet neve, ahogy a következő táblázat is mutatja. |
| **operationVersion** |REST API az ügyfél által kért verziót. |
| **Kategória** |Az eredmény típusa. Key Vault naplók esetében `AuditEvent` az egyetlen elérhető érték. |
| **resultType** |A REST API kérelem eredménye. |
| **resultSignature** |A HTTP-állapot. |
| **resultDescription** |Az eredmény további leírása, amennyiben elérhető. |
| **Átl** |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ebbe nincs beleszámítva a hálózati késés, így az ügyféloldalon mért idő ettől eltérhet. |
| **callerIpAddress** |Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. |
| **correlationId** |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| **identitás** |Az REST API kérelemben bemutatott jogkivonat identitása. Ez általában a "felhasználó", "a" szolgáltatásnév "vagy" felhasználó + appId "kombinációja, amely egy Azure PowerShell-parancsmagból származó kérelem esetében van. |
| **Tulajdonságok** |A művelettől (**operationName**) függően változó információk. A legtöbb esetben ez a mező tartalmazza az ügyfél adatait (az ügyfél által átadott felhasználói ügynök sztringjét), a pontos REST API kérelem URI-JÁT és a HTTP-állapotkódot. Emellett, ha egy objektum egy kérelem eredményeképpen érkezik (például a Key **create** vagy a **VaultGet**), a kulcs URI-ját (as), a tároló `id` URI-ját vagy a titkos kódot is tartalmazza. |

A **OperationName** *ObjectVerb* formátumban vannak. Például:

* A Key Vault összes műveletének `Vault<action>` formátuma, például `VaultGet` és `VaultCreate` .
* Az összes kulcsfontosságú művelet `Key<action>` formátuma, például `KeySign` és `KeyList` .
* Minden titkos művelet `Secret<action>` formátuma, például `SecretGet` és `SecretListVersions` .

A következő táblázat felsorolja a **operationName** és a hozzá tartozó REST API parancsokat:

### <a name="operation-names-table"></a>Műveleti nevek tábla

| operationName | REST API parancs |
| --- | --- |
| **Hitelesítés** |Hitelesítés Azure Active Directory végponton keresztül |
| **VaultGet** |[Kulcstároló adatainak lekérése](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Kulcstároló létrehozása vagy frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Kulcstároló törlése](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Kulcstároló frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Az erőforráscsoport összes kulcstárolójának listázása](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Kulcs létrehozása](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Kulcs adatainak lekérése](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Kulcs importálása egy tárolóba](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Kulcs biztonsági mentése](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Kulcs törlése](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Kulcs helyreállítása](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Aláírás kulccsal](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Ellenőrzés kulccsal](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Kulcs becsomagolása](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Kulcs kicsomagolása](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Titkosítás kulccsal](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Visszafejtés kulccsal](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Kulcs frissítése](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Egy tároló kulcsainak listázása](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Kulcs verzióinak listázása](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Titkos kulcs létrehozása](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Titkos kód beszerzése](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Titkos kulcs frissítése](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Titkos kulcs törlése](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Egy tároló titkos kulcsainak listázása](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Titkos kulcs verzióinak listázása](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | A tár hozzáférési szabályzatának változási eseménye közzétéve |
| **SecretNearExpiryEventGridNotification** |Secret Near lejárat esemény közzététele |
| **SecretExpiredEventGridNotification** |Titokban lejárt esemény közzétéve |
| **KeyNearExpiryEventGridNotification** |Kulcs közel lejárati eseményének közzététele |
| **KeyExpiredEventGridNotification** |A kulcs lejárt eseményének közzététele |
| **CertificateNearExpiryEventGridNotification** |Tanúsítvány közel lejárati eseménye közzétéve |
| **CertificateExpiredEventGridNotification** |A tanúsítvány lejárt esemény közzétéve |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Azure Monitor naplók használata

A Key Vault naplók áttekintéséhez használhatja a Azure Monitor naplók Key Vault megoldását `AuditEvent` . Azure Monitor naplókban a naplók segítségével elemezheti az adatokat, és lekérheti a szükséges információkat. 

További információk, például a beállításának módja: [Azure Key Vault Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a><a id="next"></a>További lépések

A .NET-alapú webalkalmazásokban Azure Key Vaultt használó oktatóanyagért lásd: [Azure Key Vault használata webalkalmazásból](tutorial-net-create-vault-azure-web-app.md).

Programozási hivatkozások: [Azure Key Vault developer’s guide](developers-guide.md) (Az Azure Key Vault fejlesztői útmutatója).

Azure Key Vault Azure PowerShell 1,0-parancsmagok listáját itt tekintheti meg: [Azure Key Vault parancsmagok](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
