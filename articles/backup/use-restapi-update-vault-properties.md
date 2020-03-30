---
title: A Helyreállítási szolgáltatások tárolójának konfigurációjának frissítése REST API-val
description: Ebből a cikkből megtudhatja, hogyan frissítheti a tároló konfigurációját a REST API használatával.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252362"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Az Azure Recovery Services Vault-konfigurációk frissítése REST API használatával

Ez a cikk ismerteti, hogyan frissítheti a biztonsági mentéshez kapcsolódó konfigurációk at Azure Recovery Services vault rest API használatával.

## <a name="soft-delete-state"></a>Helyreállítható törlési állapot

A védett elemek biztonsági másolatainak törlése jelentős művelet, amelyet figyelni kell. A véletlen törlések elleni védelem érdekében az Azure Recovery Services-tároló egy helyreállítható törlési képességgel rendelkezik. Ez a funkció lehetővé teszi az ügyfelek számára, hogy szükség esetén a törlést követő időszakon belül visszaállítsák a törölt biztonsági másolatokat.

De vannak olyan forgatókönyvek, amelyekben ez a képesség nem szükséges. Az Azure Recovery Services-tároló nem törölhető, ha biztonsági mentési elemek vannak benne, még a helyreállíthatóan törölt is. Ez problémát okozhat, ha a tárolót azonnal törölni kell. Például: a telepítési műveletek gyakran törlik a létrehozott erőforrásokat ugyanabban a munkafolyamatban. A központi telepítés hozhat létre egy tárolót, konfigurálhatja egy elem biztonsági mentéseit, teszt-visszaállítást, majd folytathatja a biztonsági mentési elemek és a tároló törlését. Ha a tároló törlése sikertelen, a teljes központi telepítés sikertelen lehet. A helyreállítható törlés letiltása az egyetlen módja az azonnali törlés biztosításának.

Ezért az ügyfélnek gondosan meg kell választania, hogy letiltja-e a soft-delete egy adott tárolóhoz a forgatókönyvtől függően. További információt a [helyreállítható törlésről szóló cikkben](backup-azure-security-feature-cloud.md#soft-delete)talál.

### <a name="fetch-soft-delete-state-using-rest-api"></a>A helyreállítható törlési állapot lehívása rest api-val

Alapértelmezés szerint a helyreállítható törlési állapot minden újonnan létrehozott Helyreállítási szolgáltatás-tárolóesetében engedélyezve lesz. A tároló helyreállítható törlési állapotának lehívásához/frissítéséhez használja a biztonsági másolat tárolójának konfigurációval kapcsolatos [REST API-dokumentumát](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs)

A tároló helyreállítható törlésének aktuális állapotának beolvasásához használja a következő *GET-műveletet*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

A GET `{subscriptionId}`URI `{vaultName}` `{vaultresourceGroupName}` a , , paraméterekkel rendelkezik. Ebben `{vaultName}` a példában a "testVault" és `{vaultresourceGroupName}` a "testVaultRG". Mivel az összes szükséges paraméter meg van adva az URI-ban, nincs szükség külön kérelemtörzsre.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Válaszok

A "GET" művelet sikeres válasza az alábbiakban látható:

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Példaválasz

A "GET" kérelem beküldése után 200 (sikeres) válasz érkezik.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>A helyreállítható törlési állapot frissítése a REST API használatával

A helyreállítási szolgáltatások tárolójának helyreállítható törlési állapotának *PATCH* REST API használatával történő frissítéséhez használja a következő PATCH-műveletet

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

A PATCH `{subscriptionId}`URI-nak , `{vaultName}`paraméterei `{vaultresourceGroupName}` vannak. Ebben `{vaultName}` a példában a "testVault" és `{vaultresourceGroupName}` a "testVaultRG". Ha lecseréljük az URI-t a fenti értékekre, akkor az URI így fog kinézni.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>A kérelemtörzs létrehozása

A következő közös definíciók at egy kérelemtörzs létrehozására használják

További részletekért tekintse meg [a REST API dokumentációját](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Név  |Kötelező  |Típus  |Leírás  |
|---------|---------|---------|---------|
|Etag     |         |   Sztring      |  Opcionális eTag       |
|location     |  igaz       |Sztring         |   Erőforrás helye      |
|properties     |         | [Vaulttulajdonságai](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  A tároló tulajdonságai       |
|címkét     |         | Objektum        |     Erőforráscímkék    |

#### <a name="example-request-body"></a>Példa kérelem törzse

A következő példa a "letiltott" állapot frissítésére szolgál.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Válaszok

A "PATCH" művelet sikeres válasza az alábbiakban látható:

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Példaválasz

A "PATCH" kérelem benyújtását követően 200 (sikeres) válasz érkezik.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>További lépések

[Hozzon létre egy biztonsági mentési szabályzatot egy Azure virtuális gép biztonsági mentéséhez ebben a tárolóban.](backup-azure-arm-userestapi-createorupdatepolicy.md)

Az Azure REST API-król az alábbi dokumentumokban talál további információt:

- [Az Azure Recovery Services szolgáltatóREST API-ja](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
