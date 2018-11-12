---
title: 'Az Azure Backup: Hozzon létre biztonsági mentési szabályzatok REST API használatával'
description: (Ütemezése és megőrzése) biztonsági mentési házirendek kezelése a REST API használatával
services: backup
author: pvrk
manager: shivamg
keywords: REST API-JA; Az Azure virtuális gép biztonsági mentése; Az Azure virtuális gép visszaállítási;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 657a777da0e984a145c1c617a6194bf4ef56306e
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289833"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Hozzon létre az Azure Recovery Services biztonsági mentési szabályzatok REST API használatával

A lépéseket az Azure Recovery Services-tárolót a biztonsági mentési szabályzat létrehozása a [házirend REST API-dokumentumban](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate). Ossza meg velünk referenciaként a dokumentum segítségével hozzon létre egy Azure virtuális gép biztonsági mentési szabályzatot.

## <a name="backup-policy-essentials"></a>A biztonsági mentési szabályzat alapjai

- Biztonsági mentési szabályzat tárolónként jön létre.
- Biztonsági mentési szabályzat hozható létre a következő számítási feladatok biztonsági mentése
  - Azure VM
  - Az SQL Azure-beli virtuális gépen
  - Azure-fájlmegosztás
- Egy házirend sok erőforrás is hozzárendelhető. Egy Azure virtuális gép biztonsági mentési szabályzat számos Azure-beli virtuális gépek védelmére használható.
- Két összetevőből áll: egy házirend
  - Ütemezés: A biztonsági mentés időpontjának kiválasztásakor
  - Adatmegőrzés: Mennyi ideig fenn kell tartani minden egyes biztonsági másolat.
- Ütemezés "naponta" vagy "hetente", az adott időpontra idő adható meg.
- A "naponta", "hetente", "havi", "éves" biztonsági mentési pontok megőrzése lehet definiálni.
- "hetente" hivatkozik egy biztonsági mentés a hét egy bizonyos napon, "havi" azt jelenti, hogy a biztonsági mentés, a hónap adott napon, "éves" hivatkozik egy biztonsági mentés az év egyes napjaira vonatkozó.
- A "havi", "éves" biztonsági mentési pontok megőrzése "LongTermRetention" nevezzük.
- A tároló létrejött, ha egy szabályzatot az Azure virtuális gép biztonsági mentéseinek "DefaultPolicy" nevű is létrejön, és használható az Azure virtuális gépek biztonsági mentése.

Azure biztonsági mentési szabályzat létrehozása vagy, használja a következő *PUT* művelet

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

A `{policyName}` és `{vaultName}` szerepelnek az URI-t. További információ a kérelem törzsében.

## <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

Például az Azure virtuális gépek biztonsági mentését a szabályzat létrehozásához a következők a kérelem törzsében összetevői.

|Name (Név)  |Szükséges  |Típus  |Leírás  |
|---------|---------|---------|---------|
|properties     |   True (Igaz)      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource tulajdonságai        |
|tags     |         | Objektum        |  Erőforráscímkék       |

A kérelem törzsében szereplő definíciók teljes listájáért tekintse meg a [biztonsági mentési szabályzat REST API-dokumentumban](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Példa kérelem törzse

A következő kérés törzse egy Azure virtuális gép biztonsági mentésekhez biztonsági mentési szabályzat határozza meg.

A szabályzat szerint:

- Heti biztonsági másolatok igénybe minden hétfőn, szerda, csütörtök, 10:00 órakor csendes-óceáni téli idő.
- Az egy hétig minden hétfőn, szerda, csütörtök készített biztonsági mentések megőrzési idejét.
- A biztonsági másolatokat minden első szerda és a egy hónap két hónapig (felülbírálások az előző megőrzési feltételek, ha van ilyen) harmadik csütörtök megőrzése.
- A negyedik hétfőjén és február és November negyedik csütörtökén négy évre (felülbírálások az előző megőrzési feltételek, ha van ilyen) végrehajtott biztonsági mentések megőrzési idejét.

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Az ütemezési és megőrzési időformátumok csak DateTime támogatja. Önálló időformátum nem támogatják.

## <a name="responses"></a>Válaszok

A biztonsági mentési szabályzat létrehozása és frissítése az egy [aszinkron művelet](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Ez azt jelenti, hogy a művelet létrehoz egy másik művelet, külön-külön nyomon kell követni.

Két választ adja vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd a 200 (OK), hogy a művelet befejeződésekor.

|Name (Név)  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |    [Védelem PolicyResource](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202-es elfogadva     |         |     Elfogadva    |

### <a name="example-responses"></a>Példa válaszok

Miután a *PUT* -kérést a szabályzat létrehozása vagy frissítése során, az első válaszig a 202 (elfogadva) egy helyre vagy az Azure-aszinkron-fejléc.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Powered-By: ASP.NET
```

Az eredményül kapott művelet a location fejlécet, vagy az Azure-AsyncOperation fejlécet használja egy egyszerű, majd nyomon követheti *első* parancsot.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
```

A művelet befejezését követően a házirend tartalommal, a válasz törzsében ad vissza 200 (OK).

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Egy házirend már használja egy elem védelmét, ha a szabályzat minden olyan frissítés eredményez [védelmi módosítása](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) minden ilyen kapcsolódó elemek.

## <a name="next-steps"></a>További lépések

[Egy nem védett Azure virtuális gép védelmének engedélyezése](backup-azure-arm-userestapi-backupazurevms.md).

Az Azure Backup – REST API-k további információkért tekintse meg a következő dokumentumokat:

- [Az Azure Recovery Services-szolgáltató REST API-val](/rest/api/recoveryservices/)
- [Azure REST API használatának első lépései](/rest/api/azure/)