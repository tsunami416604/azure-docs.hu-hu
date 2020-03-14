---
title: Recovery Services tároló konfigurációjának frissítése REST API
description: Ebből a cikkből megtudhatja, hogyan frissítheti a tár konfigurációját a REST API használatával.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252362"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Az Azure Recovery Services-tároló konfigurációinak frissítése REST API használatával

Ez a cikk azt ismerteti, hogyan frissítheti az Azure Recovery Services-tároló biztonsági másolatokkal kapcsolatos konfigurációit REST API használatával.

## <a name="soft-delete-state"></a>Helyreállítható törlés állapota

A védett elemek biztonsági másolatának törlése olyan jelentős művelet, amelyet figyelni kell. A véletlen törlésekkel szembeni védelem érdekében az Azure Recovery Services-tárolónak van egy puha törlési képessége. Ez a funkció lehetővé teszi, hogy az ügyfelek szükség esetén visszaállítsák a törölt biztonsági másolatokat a törlést követő időszakon belül.

Vannak azonban olyan forgatókönyvek, amelyekben ez a képesség nem szükséges. Az Azure Recovery Services-tároló nem törölhető, ha olyan biztonsági másolati elemek találhatók benne, amelyeken még nem is törlődnek. Ez problémát jelenthet, ha a tárolót azonnal törölni kell. Például: az üzembe helyezési műveletek gyakran törlik a létrehozott erőforrásokat ugyanabban a munkafolyamatban. A központi telepítés létrehozhat egy tárolót, konfigurálhatja a biztonsági másolatokat egy elemhez, tesztet állíthat vissza, majd folytathatja a biztonsági mentési elemek és a tár törlését. Ha a tár törlése sikertelen, a teljes telepítés sikertelen lehet. A törlés letiltásával garantálható az azonnali törlés.

Ezért az ügyfélnek gondosan ki kell választania, hogy letiltsa-e az adott tár helyreállítható törlését a forgatókönyvtől függően. További információ: [Soft-delete cikk](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Helyreállítható törlési állapot beolvasása REST API használatával

Alapértelmezés szerint a rendszer minden újonnan létrehozott Recovery Services-tárolóra engedélyezi a helyreállítható törlési állapotot. Egy tár helyreállítható törlési állapotának lekéréséhez/frissítéséhez használja a Backup-tároló konfigurációval kapcsolatos [REST API dokumentumát](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) .

A tárolóhoz tartozó helyreállítható törlés aktuális állapotának beolvasásához használja a következő *lekérési* műveletet

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

A GET URI `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}` paramétereket tartalmaz. Ebben a példában a `{vaultName}` a "testVault", a `{vaultresourceGroupName}` pedig "testVaultRG". Mivel az összes szükséges paraméter meg van adva az URI-ban, nincs szükség külön kérelem törzsére.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Válaszok

A "GET" művelet sikeres válasza alább látható:

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Példaválasz

A "GET" kérés elküldése után a rendszer 200 (sikeres) választ ad vissza.

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

### <a name="update-soft-delete-state-using-rest-api"></a>Helyreállítható törlési állapot frissítése REST API használatával

A Recovery Services-tároló törlési állapotának REST API használatával történő frissítéséhez használja a következő *javítási* műveletet

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

A javítás URI-ja `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}` paramétereket tartalmaz. Ebben a példában a `{vaultName}` a "testVault", a `{vaultresourceGroupName}` pedig "testVaultRG". Ha az URI-t a fenti értékekre cseréljük, akkor az URI a következőképpen fog kinézni.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

A kérelem törzsének létrehozásához a következő általános definíciók használhatók

További részletekért tekintse meg [a REST API dokumentációját](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body) .

|Name (Név)  |Kötelező  |Típus  |Leírás  |
|---------|---------|---------|---------|
|eTag     |         |   Sztring      |  Opcionális eTag       |
|location     |  true       |Sztring         |   Erőforrás helye      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  A tár tulajdonságai       |
|tags     |         | Objektum        |     Erőforráscímkék    |

#### <a name="example-request-body"></a>Példa kérelem törzsére

A következő példa a helyreállítható törlési állapot frissítésére szolgál a "Letiltva" értékre.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Válaszok

A "PATCH" művelet sikeres válasza alább látható:

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Példaválasz

A "javítás" kérés elküldése után a rendszer egy 200 (sikeres) választ ad vissza.

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

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy biztonsági mentési szabályzatot egy Azure-beli virtuális gép biztonsági mentéséhez ebben a tárolóban](backup-azure-arm-userestapi-createorupdatepolicy.md).

Az Azure REST API-kkal kapcsolatos további információkért tekintse meg a következő dokumentumokat:

- [Azure Recovery Services-szolgáltató REST API](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
