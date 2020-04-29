---
title: Biztonságos eszközök titkosítása az Azure Automationben
description: Azure Automation a biztonságos eszközöket több titkosítási szint használatával védi. Alapértelmezés szerint a titkosítás a Microsoft által felügyelt kulcsok használatával történik. Az ügyfelek az Automation-fiókjaikat úgy konfigurálhatják, hogy az ügyfél által felügyelt kulcsokat használják a titkosításhoz. Ez a cikk mindkét titkosítási mód részleteit ismerteti, valamint azt, hogy hogyan válthat a kettő között.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 594bac257c2b9739f1ece276c881348b35d2f704
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604818"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Biztonságos eszközök titkosítása az Azure Automationben

Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a Azure Automation több titkosítási szinttel védik. A titkosításhoz használt legfelső szintű kulcs alapján két titkosítási modell létezik:
-    Microsoft által felügyelt kulcsok használata
-    Ügyfél által felügyelt kulcsok használata

## <a name="microsoft-managed-keys"></a>Microsoft által felügyelt kulcsok

Alapértelmezés szerint a Azure Automation-fiók a Microsoft által felügyelt kulcsokat használja.

Minden biztonságos eszköz titkosítva van, és a Azure Automation tárolja az egyes Automation-fiókokhoz generált egyedi kulcs (adattitkosítási kulcs) használatával. Ezeket a kulcsokat a rendszer titkosítja és tárolja Azure Automation a fiók titkosítási kulcsa (AEK) nevű fiókhoz létrehozott egy másik egyedi kulccsal. Ezek a fiók titkosítási kulcsainak titkosítása és tárolása Azure Automation a Microsoft által felügyelt kulcsok használatával történik. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Ügyfél által felügyelt kulcsok Key Vaultsal (előzetes verzió)

Az Automation-fiókhoz tartozó biztonságos eszközök titkosítását a saját kulcsaival kezelheti. Amikor az Automation-fiók szintjén megad egy ügyfél által felügyelt kulcsot, a kulcs az Automation-fiókhoz tartozó fiók titkosítási kulcsának megvédésére és hozzáférés-vezérlésére szolgál. Ez a funkció az összes biztonságos eszköz titkosítására és visszafejtésére szolgál. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. A biztonságos eszközök védelméhez használt titkosítási kulcsokat is naplózhatja.

Az ügyfél által felügyelt kulcsok tárolásához használja a Azure Key Vault. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához.  További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Ügyfél által felügyelt kulcsok engedélyezése Automation-fiókhoz

Ha az Automation-fiókhoz az ügyfél által felügyelt kulcsokkal engedélyezi a titkosítást, a Azure Automation a társított kulcstartóban lévő ügyfél által felügyelt kulccsal becsomagolja a fiók titkosítási kulcsát. Az ügyfél által felügyelt kulcsok engedélyezése nem befolyásolja a teljesítményt, és a fiók azonnal, késedelem nélkül titkosítva lesz az új kulccsal.

Az új Automation-fiók mindig a Microsoft által felügyelt kulcsokkal van titkosítva. A fiók létrehozásakor nem lehet engedélyezni az ügyfél által felügyelt kulcsokat. Az ügyfél által felügyelt kulcsok Azure Key Vault tárolódnak, és a kulcstárolót olyan hozzáférési házirendekkel kell kiépíteni, amelyek kulcsfontosságú engedélyeket biztosítanak az Automation-fiókhoz társított felügyelt identitásnak. A felügyelt identitás csak a Storage-fiók létrehozása után érhető el.

Ha módosítja a Azure Automation biztonságos adattitkosításhoz használt kulcsot, az ügyfél által felügyelt kulcsok engedélyezésével vagy letiltásával, a kulcs verziójának frissítésével vagy egy másik kulcs megadásával, a fiók titkosítási kulcsának titkosítása megváltozik, de a Azure Automation fiókjában lévő biztonságos eszközök nem szükségesek újra titkosítva.

Az alábbi három szakasz ismerteti az Automation-fiókokhoz tartozó ügyfél által felügyelt kulcsok engedélyezésének mechanikaát. 

> [!NOTE] 
> Az ügyfél által felügyelt kulcsok engedélyezéséhez Azure Automation REST API hívásokat kell tennie az 2020-01-13-es API-verzióval – előzetes verzió

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Az ügyfél által felügyelt kulcsok Azure Automation való használatának előfeltételei

Az ügyfél által felügyelt kulcsok Automation-fiókhoz való engedélyezése előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

 - Az ügyfél-összefoglalt kulcsot egy Azure Key Vault tárolja. 
 - Engedélyezze mind a helyreállítható **törlést** , sem a kulcstárolóban lévő tulajdonságok **eltávolítását** . Ezek a funkciók a kulcsok helyreállításának engedélyezéséhez szükségesek a véletlen törlés esetén.
 - Azure Automation titkosításhoz csak RSA-kulcsok támogatottak. A kulcsokkal kapcsolatos további információkért tekintse meg [a Azure Key Vault kulcsok, titkos kódok és tanúsítványok](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)című témakört.
- Az Automation-fiók és a kulcstartó különböző előfizetésekben lehet, de ugyanabban a Azure Active Directory bérlőben kell lennie.

### <a name="assign-an-identity-to-the-automation-account"></a>Identitás kiosztása az Automation-fiókhoz

Ha az ügyfél által felügyelt kulcsokat Automation-fiókkal szeretné használni, az Automation-fióknak hitelesítenie kell az ügyfél által felügyelt kulcsokat tároló kulcstartón. A Azure Automation rendszerhez rendelt felügyelt identitásokat használ a fiók hitelesítéséhez Azure Key Vault használatával. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

A következő REST API hívásával konfigurálja a rendszerhez rendelt felügyelt identitást az Automation-fiókhoz:

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

Az Automation-fiók rendszerhez rendelt identitását a következőhöz hasonló válasz adja vissza:

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

### <a name="configure-the-key-vault-access-policy"></a>A Key Vault hozzáférési házirend konfigurálása

Ha egy felügyelt identitás hozzá van rendelve az Automation-fiókhoz, az ügyfél által felügyelt kulcsokat tároló kulcstartó elérését konfigurálja. Azure Automation az ügyfél által felügyelt kulcsokra vonatkozó **Get**, **Recover**, **wrapKey**és **UnwrapKey** szükséges.

Az ilyen hozzáférési szabályzatok a következő REST API hívásával állíthatók be:

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
> A **tenantId** és a **objectId** mezőket az **Identity. tenantId** és **Identity. principalId** értékekkel kell megadni az Automation-fiók felügyelt identitásának válasza alapján.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Az Automation-fiók konfigurációjának módosítása az ügyfél által felügyelt kulcs használatára

Végezetül átválthatja az Automation-fiókját a Microsoft által felügyelt kulcsokból az ügyfél által felügyelt kulcsokra az alábbi REST API hívás használatával:

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

## <a name="manage-customer-managed-keys-lifecycle"></a>Az ügyfél által felügyelt kulcsok életciklusának kezelése

### <a name="rotate-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok elforgatása

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. A kulcs elforgatásakor frissítenie kell az Automation-fiókot az új kulcs URI-azonosítójának használatára.

A kulcs elforgatása nem aktiválja a biztonságos eszközök újratitkosítását az Automation-fiókban. Nincs szükség további műveletre.

### <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShellt vagy az Azure CLI-t. További információ: [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) vagy [Azure Key Vault parancssori](https://docs.microsoft.com/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja az Automation-fiókban található összes biztonságos objektum elérését, mivel a titkosítási kulcs nem érhető el Azure Automation.

## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault?](../key-vault/general/overview.md)
- [Az Azure Automation tanúsítvány adategységei](shared-resources/certificates.md)
- [Az Azure Automation hitelesítési adategységei](shared-resources/credentials.md)
- [Az Azure Automation változó adategységei](shared-resources/variables.md)
