---
title: Helyreállítási szolgáltatások tárolóinak létrehozása rest API-val
description: Ebből a cikkből megtudhatja, hogyan kezelheti az Azure VM biztonsági mentés biztonsági mentési műveleteit a REST API használatával.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173419"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Az Azure Recovery Services Vault létrehozása rest API használatával

Az Azure Recovery Services Vault REST API használatával történő létrehozásának lépéseit a [Vault REST API-dokumentáció létrehozása](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) ismerteti. Használjuk ezt a dokumentumot hivatkozásként a "testVault" nevű trezor létrehozásához az USA nyugati részén.

Az Azure Recovery Services-tároló létrehozásához *PUT* vagy frissítéséhez használja a következő PUT-műveletet.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Kérelem létrehozása

A *PUT-kérelem* létrehozásához a `{subscription-id}` paraméter szükséges. Ha több előfizetéssel rendelkezik, olvassa el [a Több előfizetés sel végzett témakört.](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) Ön határozza `{resourceGroupName}` `{vaultName}` meg a és az `api-version` erőforrások, valamint a paraméter. Ez a `api-version=2016-06-01`cikk a .

A következő fejlécek megadása kötelező:

| Kérelem fejléce   | Leírás |
|------------------|-----------------|
| *Content-Type* (Tartalomtípus):  | Kötelező. Állítsa `application/json` értékre. |
| *Authorization* (Engedélyezés): | Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

A kérelem létrehozásáról további információt a [REST API-kérelem/válasz összetevői című témakörben talál.](/rest/api/azure/#components-of-a-rest-api-requestresponse)

## <a name="create-the-request-body"></a>A kérelemtörzs létrehozása

A következő közös definíciók segítségével hozzon létre egy kérelem törzs:

|Név  |Kötelező  |Típus  |Leírás  |
|---------|---------|---------|---------|
|Etag     |         |   Sztring      |  Opcionális eTag       |
|location     |  igaz       |Sztring         |   Erőforrás helye      |
|properties     |         | [Vaulttulajdonságai](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  A tároló tulajdonságai       |
|Sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Az egyes Azure-erőforrások egyedi rendszerazonosítóját azonosítja     |
|címkét     |         | Objektum        |     Erőforráscímkék    |

Vegye figyelembe, hogy a tároló neve és az erőforráscsoport neve a PUT URI-ban található. A kérelem törzse határozza meg a helyet.

## <a name="example-request-body"></a>Példa kérelem törzse

A következő példa törzs segítségével hozzon létre egy boltozat "USA nyugati részén". Adja meg a helyet. A termékváltozat mindig "Standard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Válaszok

A helyreállítási szolgáltatások tárolójának létrehozására vagy frissítésére a műveletre két sikeres válasz érkezett:

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [Vault](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Létrehozva     | [Vault](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Létrehozva      |

A REST API-válaszokról a [Válaszüzenet feldolgozása](/rest/api/azure/#process-the-response-message)című témakörben talál további információt.

### <a name="example-response"></a>Példaválasz

Egy tömörített *201 Létrehozott* válasz az előző példa kérelem törzsazt *mutatja, hogy* egy azonosító van rendelve, és a *provisioningState* *sikerült:*

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>További lépések

[Hozzon létre egy biztonsági mentési szabályzatot egy Azure virtuális gép biztonsági mentéséhez ebben a tárolóban.](backup-azure-arm-userestapi-createorupdatepolicy.md)

Az Azure REST API-król az alábbi dokumentumokban talál további információt:

- [Az Azure Recovery Services szolgáltatóREST API-ja](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
