---
title: 'Azure Backup: Recovery Services-tárolók létrehozása a REST API használatával'
description: Ebből a cikkből megtudhatja, hogyan kezelheti az Azure-beli virtuális gépek biztonsági mentési és visszaállítási műveleteit REST API használatával.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: REST API; Azure-beli virtuális gép biztonsági mentése; Azure-beli virtuális gép visszaállítása;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: dacurwin
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 7c9d165f623367d1c888b90f76e96ce8fa2e8f89
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747565"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Azure Recovery Services-tároló létrehozása REST API használatával

Az Azure Recovery Services-tárolók REST API használatával történő létrehozásának lépései a tár [létrehozása REST API](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) dokumentációjában találhatók. Ezt a dokumentumot hivatkozásként használjuk a "testVault" nevű tár létrehozásához az "USA nyugati régiójában".

Azure Recovery Services-tároló létrehozásához vagy frissítéséhez használja a következő *put* műveletet.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Kérelem létrehozása

Az *put* -kérelem létrehozásához a `{subscription-id}` paraméter szükséges. Ha több előfizetéssel rendelkezik, tekintse meg [a több előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)című témakört. Az erőforrásokhoz `{resourceGroupName}` és `{vaultName}` adhat meg, a `api-version` paraméterrel együtt. Ez a cikk `api-version=2016-06-01`t használ.

A következő fejlécek megadása kötelező:

| Kérelem fejléce   | Leírás |
|------------------|-----------------|
| *Content-Type* (Tartalomtípus):  | Kötelező. Állítsa `application/json` értékre. |
| *Authorization* (Engedélyezés): | Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

További információ a kérelem létrehozásáról: [REST API kérelem/válasz összetevői](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

A kérelem törzsének létrehozásához a következő általános definíciók használhatók:

|Név  |Kötelező  |Típus  |Leírás  |
|---------|---------|---------|---------|
|eTag     |         |   Sztring      |  Opcionális eTag       |
|location     |  igaz       |Sztring         |   Erőforrás helye      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  A tár tulajdonságai       |
|SKU     |         |  [Termékváltozat](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Az egyes Azure-erőforrások egyedi rendszerazonosítójának azonosítása     |
|címkét     |         | Objektum        |     Erőforráscímkék    |

Vegye figyelembe, hogy a tár neve és az erőforráscsoport neve a PUT URI-ban van megadva. A kérelem törzse határozza meg a helyet.

## <a name="example-request-body"></a>Példa kérelem törzsére

A következő példában egy tárolót hoz létre a "West US"-ben. Itt adhatja meg a helyet. Az SKU mindig "standard".

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

Két sikeres válasz van a művelethez egy Recovery Services-tároló létrehozásához vagy frissítéséhez:

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [Tároló](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 létrehozva     | [Tároló](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Létrehozva      |

További információ a REST API válaszokról: [a válaszüzenet feldolgozása](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Példaválasz

Az előző példában szereplő kérelem törzsében létrehozott, sűrített *201* -válasz egy *azonosítót* rendelt hozzá, és a *provisioningState* *sikeres*:

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

[Hozzon létre egy biztonsági mentési szabályzatot egy Azure-beli virtuális gép biztonsági mentéséhez ebben a tárolóban](backup-azure-arm-userestapi-createorupdatepolicy.md).

Az Azure REST API-kkal kapcsolatos további információkért tekintse meg a következő dokumentumokat:

- [Azure Recovery Services-szolgáltató REST API](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
