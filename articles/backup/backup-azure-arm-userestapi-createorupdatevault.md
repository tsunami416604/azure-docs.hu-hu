---
title: 'Azure Backup: Recovery Services-tárolók létrehozása REST API használatával'
description: Azure-beli virtuális gépek biztonsági mentési és visszaállítási műveleteinek kezelése REST API használatával
author: pvrk
manager: shivamg
keywords: REST API; Azure-beli virtuális gép biztonsági mentése; Azure-beli virtuális gép visszaállítása;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 0373098dd344df79be79871227f20c8a995958fa
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466943"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Azure Recovery Services-tároló létrehozása REST API használatával

Az Azure Recovery Services-tárolók REST API használatával történő létrehozásának lépései a tár [létrehozása REST API](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) dokumentációjában találhatók. Ezt a dokumentumot hivatkozásként használjuk a "testVault" nevű tár létrehozásához az "USA nyugati régiójában".

Azure Recovery Services-tároló létrehozásához vagy frissítéséhez használja a következő *put* műveletet.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Kérés létrehozása

A *put* kérelem létrehozásához a `{subscription-id}` paraméter megadása kötelező. Ha több előfizetéssel rendelkezik, tekintse meg [a több előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)című témakört. Megadhatja a `{vaultName}` `api-version` és a erőforrásait, valamint a paramétert. `{resourceGroupName}` Ez a cikk `api-version=2016-06-01`a következőt használja:.

A következő fejlécek szükségesek:

| Kérelem fejléce   | Leírás |
|------------------|-----------------|
| *Content-Type:*  | Kötelező. Állítsa a `application/json`következőre:. |
| *Authorization:* | Kötelező. Érvényes `Bearer` [hozzáférési](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)jogkivonatra van beállítva. |

További információ a kérelem létrehozásáról: [REST API kérelem/válasz összetevői](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

A kérelem törzsének létrehozásához a következő általános definíciók használhatók:

|Name (Név)  |Kötelező  |Típus  |Leírás  |
|---------|---------|---------|---------|
|eTag     |         |   Sztring      |  Opcionális eTag       |
|location     |  true       |Sztring         |   Erőforrás helye      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  A tár tulajdonságai       |
|sku     |         |  [Termékváltozat](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Az egyes Azure-erőforrások egyedi rendszerazonosítójának azonosítása     |
|címkék     |         | Object        |     Erőforráscímkék    |

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

## <a name="responses"></a>Responses

Két sikeres válasz van a művelethez egy Recovery Services-tároló létrehozásához vagy frissítéséhez:

|Name (Név)  |Típus  |Leírás  |
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

[Hozzon létre egy biztonsági mentési szabályzatot egy Azure-beli virtuális gép biztonsági mentéséhez ebben](backup-azure-arm-userestapi-createorupdatepolicy.md)a tárolóban.

Az Azure REST API-kkal kapcsolatos további információkért tekintse meg a következő dokumentumokat:

- [Azure Recovery Services-szolgáltató REST API](/rest/api/recoveryservices/)
- [Ismerkedés az Azure REST API](/rest/api/azure/)
