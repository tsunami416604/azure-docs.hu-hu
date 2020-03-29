---
title: Biztonsági mentési házirendek létrehozása rest api-val
description: Ebben a cikkben megtudhatja, hogyan hozhat létre és kezelhet biztonsági mentési szabályzatokat (ütemezés és megőrzés) a REST API használatával.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 0718ebc3612f53f1c2cc279096dd92de69bb5ef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963852"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Azure Recovery Services biztonsági mentési szabályzatainak létrehozása REST API használatával

Az Azure Recovery Services-tároló biztonsági mentési szabályzatának létrehozásának lépéseit a [REST API-dokumentum](/rest/api/backup/protectionpolicies/createorupdate)ismerteti. Használja ezt a dokumentumot hivatkozásként az Azure vm biztonsági mentésszabályzatának létrehozásához.

## <a name="create-or-update-a-policy"></a>Házirend létrehozása vagy frissítése

Azure Backup-szabályzat létrehozásához vagy frissítéséhez használja a következő *PUT-műveletet*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

A `{policyName}` `{vaultName}` és az URI-ban vannak megadva. További információk a kérelem törzsében található.

## <a name="create-the-request-body"></a>A kérelemtörzs létrehozása

Például hozzon létre egy szabályzatot az Azure virtuális gép biztonsági mentéséhez, az alábbiakban a kérelem törzsösszetevői.

|Név  |Kötelező  |Típus  |Leírás  |
|---------|---------|---------|---------|
|properties     |   True (Igaz)      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource tulajdonságai        |
|címkét     |         | Objektum        |  Erőforráscímkék       |

A kérelemtörzsben található definíciók teljes listáját a [REST API-dokumentum biztonsági mentési házirendje című dokumentumban tájékírja.](/rest/api/backup/protectionpolicies/createorupdate)

### <a name="example-request-body"></a>Példa kérelem törzse

A következő kérelem törzse meghatározza az Azure virtuális gépek biztonsági mentési szabályzatát.

A politika azt mondja:

- Vegyen egy heti biztonsági mentést minden hétfőn, szerdán, csütörtökön 10:00-kor csendes-óceáni téli idő szerint.
- Őrizze meg a biztonsági mentések hozott minden hétfőn, szerdán, csütörtökön egy hétig.
- A hónap minden egyes szerdáján és harmadik csütörtökén két hónapon keresztül megőrzi a biztonsági mentéseket (felülírja a korábbi megőrzési feltételeket, ha vannak ilyenek).
- Őrizze meg a biztonsági mentések hozott negyedik hétfőn és negyedik csütörtökfebruárban és novemberben négy évig (felülírja a korábbi megőrzési feltételek, ha van ilyen).

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
> Az ütemezés és megőrzés időformátumai csak a DateTime-ot támogatják. Nem támogatják a Time formátumot egyedül.

## <a name="responses"></a>Válaszok

A biztonsági mentési házirend létrehozása/frissítése [aszinkron művelet.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Ez azt jelenti, hogy ez a művelet létrehoz egy másik műveletet, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (Elfogadva) egy másik művelet létrehozásakor, majd 200 (OK) értéket, amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |    [Védelmi házirenderőforrás](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 Elfogadva     |         |     Elfogadva    |

### <a name="example-responses"></a>Példa válaszok

Miután elküldte a PUT-kérelmet a szabályzat létrehozására vagy frissítésére, a kezdeti válasz 202 (Elfogadva) egy helyfejléccel vagy az Azure-async-header.Once you submit the *PUT* request for policy creation or frissítés, the initial response is 202 (Accepted) with a location header or Azure-async-header.

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
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ezután kövesse nyomon az eredményül kapott műveletet a helyfejléc vagy az Azure-AsyncOperation fejléc használatával egy egyszerű *GET* paranccsal.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Miután a művelet befejeződött, 200 -ot (OK) ad vissza a választörzsben lévő házirend tartalommal.

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

Ha egy házirend már használatban van egy elem védelmére, a házirend bármely frissítése az összes ilyen társított elem [védelmét módosítja.](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection)

## <a name="next-steps"></a>További lépések

[Engedélyezze a védelem nélküli Azure virtuális gép védelmét.](backup-azure-arm-userestapi-backupazurevms.md)

Az Azure Backup REST API-król az alábbi dokumentumokban talál további információt:

- [Az Azure Recovery Services szolgáltatóREST API-ja](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
