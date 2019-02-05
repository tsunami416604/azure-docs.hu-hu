---
title: 'Azure Backup: Hozzon létre a Recovery Services-tárolót a REST API használatával'
description: biztonsági mentés kezelése és a visszaállítási műveleteket az Azure VM Backup – REST API használatával
services: backup
author: pvrk
manager: shivamg
keywords: REST API-JA; Az Azure virtuális gép biztonsági mentése; Az Azure virtuális gép visszaállítási;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 4f18b10ee3f4148badc8e53a9660c9f5c998aef7
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734334"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Hozzon létre az Azure Recovery Services-tárolót a REST API használatával

Az Azure Recovery Services-tárolót a REST API használatával létrehozásának lépéseit leírt [tároló REST API létrehozása](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) dokumentációját. Ossza meg velünk referenciaként Ez a dokumentum segítségével hozzon létre egy "West US" a "testVault" nevű tárolót.

Létrehozása vagy frissítése egy Azure helyreállítási tárat, használja a következő *PUT* műveletet.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Kérés létrehozása

Hozhat létre a *PUT* kérelem, a `{subscription-id}` paraméter megadása kötelező. Ha több előfizetéssel rendelkezik, tekintse meg [több előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Megadhat egy `{resourceGroupName}` és `{vaultName}` az erőforrások és a `api-version` paraméter. Ez a cikk `api-version=2016-06-01`.

A következő fejléceket szükség:

| Kérelem fejléce   | Leírás |
|------------------|-----------------|
| *A Content-Type:*  | Kötelező. Állítsa be `application/json`. |
| *Hitelesítés:* | Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

A kérés létrehozásával kapcsolatos további információkért lásd: [egy REST API-kérés/válasz összetevői](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

A következő gyakori definíciókat hozhat létre a kéréstörzs használhatók:

|Name (Név)  |Szükséges  |Típus  |Leírás  |
|---------|---------|---------|---------|
|az eTag     |         |   String      |  Az eTag nem kötelező       |
|location     |  true       |String         |   Erőforrás helye      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  A tároló tulajdonságai       |
|termékváltozat     |         |  [Termékváltozat](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Azonosítja az egyes Azure-erőforrások egyedi rendszerazonosítója     |
|tags     |         | Objektum        |     Erőforráscímkék    |

Vegye figyelembe, hogy a tároló neve és erőforráscsoport-név megtalálható-e a PUT URI-t. A kérelem törzsében a hely határozza meg.

## <a name="example-request-body"></a>Példa kérelem törzse

A következő példa törzsében segítségével hozzon létre egy tárolót az "USA nyugati RÉGIÓJA". Adja meg a helyet. A Termékváltozat a "Standard".

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

Nincsenek a művelethez létrehozni vagy frissíteni a Recovery Services-tároló két sikeres válaszok:

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [Tároló](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Created     | [Tároló](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Létrehozva      |

További információ a REST API-válaszok: [feldolgozni a válaszüzenet](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Példaválasz

Egy tömörített *201 Created* válasz az előző kérelem (példa) a body mutat be egy *azonosító* hozzá lett rendelve, és a *provisioningState* van *sikeres* :

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

[Ebben a tárban egy Azure virtuális gép biztonsági mentésével biztonsági mentési szabályzat létrehozása](backup-azure-arm-userestapi-createorupdatepolicy.md).

További információ az Azure REST API-k a következő dokumentumokban talál:

- [Az Azure Recovery Services-szolgáltató REST API-val](/rest/api/recoveryservices/)
- [Azure REST API használatának első lépései](/rest/api/azure/)
