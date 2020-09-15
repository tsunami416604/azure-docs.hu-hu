---
title: Azure Key Vault Event Grid forrásként
description: A Azure Key Vault eseményekhez megadott tulajdonságokat és sémát ismerteti Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: f6e2bdfb2000f3a4c4a8f91eee23348d9cc9c766
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090397"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault Event Grid forrásként

Ez a cikk a [Azure Key Vault](../key-vault/index.yml)eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

### <a name="available-event-types"></a>Elérhető események típusai

Egy Azure Key Vault fiók a következő típusú eseményeket hozza létre:

| Esemény teljes neve | Esemény megjelenítendő neve | Description |
| ---------- | ----------- |---|
| Microsoft. kulcstartó. CertificateNewVersionCreated | Tanúsítvány új verziója létrehozva | Új tanúsítvány vagy új tanúsítvány-verzió létrehozásakor aktiválódik. |
| Microsoft. kulcstartó. CertificateNearExpiry | A tanúsítvány hamarosan lejár | Akkor aktiválódik, ha a tanúsítvány aktuális verziója hamarosan lejár. (Az esemény a lejárati dátum előtt 30 nappal aktiválódik.) |
| Microsoft. kulcstartó. CertificateExpired | A tanúsítvány lejárt | A tanúsítvány lejártakor aktiválódik. |
| Microsoft. kulcstartó. KeyNewVersionCreated | A kulcs új verziója létrehozva | Új kulcs vagy új kulcs létrehozásakor aktiválódik. |
| Microsoft. kulcstartó. KeyNearExpiry | Kulcs közel lejárata | Akkor aktiválódik, ha a kulcs aktuális verziója hamarosan lejár. (Az esemény a lejárati dátum előtt 30 nappal aktiválódik.) |
| A Microsoft. kulcstartó. kulcstartó lejárt | A kulcs lejárt | A kulcs lejártakor aktiválódik. |
| Microsoft. kulcstartó. SecretNewVersionCreated | A titkos új verzió létrehozva | Új titok vagy új titkos verzió létrehozásakor aktiválódik. |
| Microsoft. kulcstartó. SecretNearExpiry | Közel lejáró titkos kód | Akkor aktiválódik, ha a titkos kulcs aktuális verziója hamarosan lejár. (Az esemény a lejárati dátum előtt 30 nappal aktiválódik.) |
| Microsoft. kulcstartó. SecretExpired | A titkos kód lejárt | A titkos kód lejártakor aktiválódik. |
| Microsoft. kulcstartó. VaultAccessPolicyChanged | A tár hozzáférési szabályzata megváltozott | Akkor aktiválódik, ha egy Key Vault hozzáférési szabályzata megváltozott. Olyan forgatókönyvet tartalmaz, amikor Key Vault engedélyezési modell módosul az Azure RBAC  |

### <a name="event-examples"></a>Példák az eseményekre

Az alábbi példa a **Microsoft. kulcstartó. SecretNewVersionCreated**sémáját mutatja be:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| ---------- | ----------- |---|
| id | sztring | Az eseményt kiváltó objektum azonosítója |
| vaultName | sztring | Az eseményt kiváltó objektum kulcstárolójának neve |
| Objektumtípus | sztring | Az eseményt kiváltó objektum típusa |
| objectName | sztring | Az eseményt kiváltó objektum neve |
| version | sztring | Az eseményt kiváltó objektum verziója |
| NBF | szám | A nem az idő előtti dátum másodpercben 1970-01-01T00:00: az eseményt kiváltó objektum 00Z |
| exp | szám | Az eseményt kiváltó objektum lejárati dátuma (másodpercben) 1970-01-01T00:00:00Z |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím  |Leírás  |
|---------|---------|
| [Key Vault események figyelése a Azure Event Grid](../key-vault/general/event-grid-overview.md) | A Key Vault és a Event Grid integrálásának áttekintése. |
| [Oktatóanyag: Key Vault események létrehozása és figyelése a Event Grid](../key-vault/general/event-grid-tutorial.md) | Megtudhatja, hogyan állíthatja be a Key Vault Event Grid értesítéseit. |


## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* Ha többet szeretne megtudni a Event Gridekkel való integrációról, tekintse Key Vault meg [a Key Vault Azure Event Grid figyelésével foglalkozó](../key-vault/general/event-grid-overview.md)témakört.
* A Event Gridekkel való Key Vault integrációval kapcsolatos oktatóanyagért lásd: a [Key Vault-értesítések fogadása és reagálás a Azure Event Grid](../key-vault/general/event-grid-tutorial.md)használatával.
* A Key Vault és Azure Automation további útmutatását a következő témakörben tekintheti meg:
    - [Mi az Azure Key Vault?](../key-vault/general/overview.md)
    - [Key Vault figyelése Azure Event Grid](../key-vault/general/event-grid-overview.md)
    - [A Key Vault-értesítések fogadása és reagálás Azure Event Grid](../key-vault/general/event-grid-tutorial.md)
    - [Azure Automation áttekintése](../automation/index.yml)
