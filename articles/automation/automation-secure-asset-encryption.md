---
title: Biztonságos eszközök titkosítása az Azure Automationben
description: Az Azure Automation több titkosítási szinttel védi a biztonságos eszközöket. Alapértelmezés szerint a titkosítás a Microsoft által felügyelt kulcsok használatával történik. Az ügyfelek beállíthatják, hogy az automatizálási fiókok ügyfél által felügyelt kulcsokat használjanak a titkosításhoz. Ez a cikk ismerteti a részleteket a két titkosítási mód, és hogyan válthat a kettő között.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: a82d2b6d9521ba7dd5e7b194c26ff8fe5a100871
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457484"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Biztonságos eszközök titkosítása az Azure Automationben

Az Azure Automation biztonságos eszközei közé tartoznak a hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. Ezek az eszközök az Azure Automation több titkosítási szinthasználatával védettek. A titkosításhoz használt legfelső szintű kulcs alapján két titkosítási modell létezik:
-    Microsoft által kezelt kulcsok használata
-    Ügyfél által kezelt kulcsok használata

## <a name="microsoft-managed-keys"></a>Microsoft által felügyelt kulcsok

Alapértelmezés szerint az Azure Automation-fiók Microsoft által felügyelt kulcsokat használ.

Minden egyes biztonságos eszköz titkosítva van, és az Azure Automationben egy egyedi kulcs (adattitkosítási kulcs) használatával történik, amely minden automatizálási fiókhoz létrejön. Ezek a kulcsok maguk is titkosítva vannak, és az Azure Automation-ben egy újabb egyedi kulcs használatával kerülnek tárolásra, amely minden fiókhoz, az úgynevezett fiókhoz fióktitkosítási kulcs (AEK) jön létre. Ezek a fióktitkosítási kulcsok titkosítva vannak, és a Microsoft által felügyelt kulcsok használatával tárolódnak az Azure Automationben. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Ügyfél által felügyelt kulcsok key vaultmal (előzetes verzió)

Az Automation-fiók biztonságos eszközeinek titkosítását saját kulcsaival kezelheti. Ha az Automation-fiók szintjén ad meg egy ügyfél által felügyelt kulcsot, a rendszer az Automation-fiók titkosítási kulcsához való hozzáférést védi és szabályozza. Ez viszont titkosítására és visszafejtésére használják az összes biztonságos eszközök. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlők létrehozásához, elforgatásához, letiltásához és visszavonásához. A biztonságos eszközök védelméhez használt titkosítási kulcsok at is naplózhatja.

Az Azure Key Vault használatával az ügyfelek által felügyelt kulcsokat tárolhatja. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat kulcsok létrehozásához.  Az Azure Key Vaultról a [Mi az Azure Key Vault?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Ügyfél által felügyelt kulcsok engedélyezése automatizálási fiókhoz

Ha engedélyezi a titkosítást egy Automation-fiók ügyfél által felügyelt kulcsaival, az Azure Automation a fiók titkosítási kulcsát a társított kulcstartóban lévő ügyfél által felügyelt kulccsal csomagolja. Az ügyfél által kezelt kulcsok engedélyezése nem befolyásolja a teljesítményt, és a fiók azonnal, késedelem nélkül titkosítva van az új kulccsal.

Az új Automation-fiók mindig titkosítva van a Microsoft által felügyelt kulcsokkal. A fiók létrehozásakor nem lehet engedélyezni az ügyfél által kezelt kulcsokat. Az ügyfél által felügyelt kulcsok az Azure Key Vaultban tárolódnak, és a key vaultot olyan hozzáférési szabályzatokkal kell kiépíteni, amelyek kulcsengedélyeket adnak az Automation-fiókhoz társított felügyelt identitásnak. A felügyelt identitás csak a tárfiók létrehozása után érhető el.

Ha módosítja az Azure Automation biztonságos eszköztitkosításához használt kulcsot az ügyfél által kezelt kulcsok engedélyezésével vagy letiltásával, a kulcsverzió frissítésével vagy egy másik kulcs megadásával, a fiók titkosítási kulcsának titkosítása megváltozik, de az Automation Azure-fiókjában lévő biztonságos eszközöket nem kell újra titkosítani.

A következő három szakasz ismerteti az Automation-fiók ügyfél által felügyelt kulcsainak engedélyezésének mechanikáját. 

> [!NOTE] 
> Az ügyfelek által felügyelt kulcsok engedélyezéséhez az Azure Automation REST API-hívásokat az API 2020-01-13-preview verziójával kell lebonyolítania.

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Az Ügyfél által felügyelt kulcsok azure Automation-ben való használatának előfeltételei

Az Automation-fiók ügyféláltal kezelt kulcsainak engedélyezése előtt gondoskodnia kell akövetkező előfeltételekről:

 - Az ügyfél által felügyelt kulcs egy Azure Key Vaultban tárolódik. 
 - Engedélyezze a **helyreállítható törlés** és a **Ne ürítse ki** a kulcstartó t. Ezek a funkciók szükségesek a kulcsok helyreállításához véletlen törlés esetén.
 - Csak rsa kulcsok at támogatott Azure Automation titkosítás. A kulcsokról az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok – című témakörben talál](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)további információt.
- Az Automation-fiók és a key vault különböző előfizetésekben lehet, de ugyanabban az Azure Active Directory-bérlőben kell lennie.

### <a name="assign-an-identity-to-the-automation-account"></a>Identitás hozzárendelése az automatizálási fiókhoz

Az ügyfél által felügyelt kulcsok automatizálási fiókkal való használatához az Automation-fióknak hitelesítenie kell magát az ügyfél által felügyelt kulcsokat tároló kulcstartón. Az Azure Automation rendszerhez rendelt felügyelt identitások használatával hitelesíti a fiókot az Azure Key Vault szolgáltatással. A felügyelt identitásokról a [Mi az Azure-erőforrások felügyelt identitásai című](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) témakörben talál további információt.

Konfiguráljon egy felügyelt identitást az automation-fiókhoz hozzárendelt rendszerkonfigurálása a következő REST API-hívással:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Kérés törzse:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Az Automation-fiók rendszerhez rendelt identitása a következőhöz hasonló válaszban jelenik meg:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>A Key Vault hozzáférési házirendjének konfigurálása

Miután egy felügyelt identitás hozzá van rendelve az Automation-fiókhoz, konfigurálja az ügyfél által felügyelt kulcsokat tároló kulcstartóhoz való hozzáférést. Az Azure Automation **beszerzése**, **recover**, **wrapKey**, **UnwrapKey** az ügyfél által felügyelt kulcsokat igényel.

Az ilyen hozzáférési szabályzat a következő REST API-hívással állítható be:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Kérés törzse:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> A **tenantId** és **objectId** mezőket az Automation-fiók felügyelt identitásának válaszából kell megadni az **identity.tenantId** és az **identity.principalId** értékekkel.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Az Automation-fiók konfigurációjának módosítása az ügyfél által kezelt kulcs használatára

Végül az Automation-fiók Microsft által kezelt kulcsokról az ügyfél által felügyelt kulcsokra válthat a következő REST API-hívás használatával:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Kérés törzse:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Mintaválasz

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>Ügyfél által kezelt kulcsok életciklusának kezelése

### <a name="rotate-customer-managed-keys"></a>Ügyfél által kezelt kulcsok elforgatása

Az azure-key vaultban az ügyfél által felügyelt kulcsot a megfelelőségi szabályzatok szerint forgathatja. A kulcs elforgatása után frissítenie kell az Automation-fiókot az új kulcs URI használatához.

A kulcs elforgatása nem indítja el a biztonságos eszközök újratitkosítását az Automation-fiókban. Nincs szükség további intézkedésre.

### <a name="revoke-access-to-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShellt vagy az Azure CLI-t. További információ: [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) vagy [Azure Key Vault CLI.](https://docs.microsoft.com/cli/azure/keyvault) A hozzáférés visszavonása hatékonyan blokkolja a hozzáférést az Automation-fiók összes biztonságos eszközéhez, mivel a titkosítási kulcs az Azure Automation által nem érhető el.

## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault?](../key-vault/general/overview.md)

- [Az Azure Automation tanúsítvány adategységei](shared-resources/certificates.md)

- [Az Azure Automation hitelesítési adategységei](shared-resources/credentials.md)

- [Az Azure Automation változó adategységei](shared-resources/variables.md)
