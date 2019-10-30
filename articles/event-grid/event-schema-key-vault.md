---
title: Azure Event Grid Azure Key Vault esemény sémája
description: A Azure Key Vault eseményekhez megadott tulajdonságokat és sémát ismerteti Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: c92352dd28b870c5f58dec0b82a8000f14a8e62d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033523"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Key Vault Azure Event Gridi esemény sémája (előzetes verzió)

Ez a cikk a jelenleg előzetes verzióban elérhető [Azure Key Vault](../key-vault/index.yml) események tulajdonságait és sémáját tartalmazza. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

## <a name="available-event-types"></a>Elérhető események típusai

Egy Azure Key Vault fiók a következő típusú eseményeket bocsátja ki:

| Esemény teljes neve | Esemény megjelenítendő neve | leírás |
| ---------- | ----------- |---|
| Microsoft. kulcstartó. CertificateNewVersionCreated | Tanúsítvány új verziója létrehozva | Új tanúsítvány vagy új tanúsítvány verziójának létrehozásakor aktiválódik |
| Microsoft. kulcstartó. CertificateNearExpiry | A tanúsítvány hamarosan lejár | Akkor aktiválódik, ha a tanúsítvány aktuális verziója hamarosan lejár (az alapértelmezett érték a lejárati dátum előtti 30 nap). |
| Microsoft. kulcstartó. CertificateExpired | A tanúsítvány lejárt | A tanúsítvány lejártakor aktiválódik |
| Microsoft. kulcstartó. KeyNewVersionCreated | A kulcs új verziója létrehozva | Új kulcs vagy új kulcs létrehozásakor aktiválódik |
| Microsoft. kulcstartó. KeyNearExpiry | Kulcs közel lejárata | Akkor aktiválódik, ha a kulcs jelenlegi verziója hamarosan lejár (az alapértelmezett érték a lejárati dátum előtti 30 nap). |
| A Microsoft. kulcstartó. kulcstartó lejárt | A kulcs lejárt | A kulcs lejártakor aktiválódik |
| Microsoft. kulcstartó. SecretNewVersionCreated | A titkos új verzió létrehozva | Új titok vagy új titkos verzió létrehozásakor aktiválódik |
| Microsoft. kulcstartó. SecretNearExpiry | Közel lejáró titkos kód | Akkor aktiválódik, ha a titkos kulcs jelenlegi verziója hamarosan lejár (az alapértelmezett érték a lejárati dátum előtti 30 nap). |
| Microsoft. kulcstartó. SecretExpired | A titkos kód lejárt | A titkos kulcs lejártakor aktiválódik |

## <a name="event-examples"></a>Példák az eseményekre

Az alábbi példa a **Microsoft. kulcstartó. SecretNewVersionCreated**sémáját jeleníti meg.

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

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Type (Típus) | Leírás |
| ---------- | ----------- |---|
| id | sztring | Az eseményt kiváltó objektum azonosítója. |
| vaultName | sztring | Az eseményt kiváltó objektum kulcstárolójának neve. |
| Objektumtípus | sztring | Az eseményt kiváltó objektum típusa |
| ObjectName | sztring | Az eseményt kiváltó objektum neve |
| version | sztring | Az eseményt kiváltó objektum verziója |
| NBF | szám | A (z) 1970-01-01T00:00: az eseményt kiváltó objektum 00Z óta nem korábbi. |
| Exp | szám | Az eseményt kiváltó objektum lejárati dátuma (másodpercben) 1970-01-01T00:00:00Z |


## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* Ha többet szeretne megtudni a Key Vault/Event Grid integrációról, tekintse meg [a figyelési Key Vault a Azure Event Grid (előzetes verzió)](../key-vault/event-grid-overview.md) című témakört.
* A Key Vault/Event Grid integrációval kapcsolatos oktatóanyag megtekintéséhez lásd [: How to: Route Key Vault Events to Automation Runbook (előzetes verzió)](../key-vault/event-grid-tutorial.md).

- [Azure Key Vault áttekintése](../key-vault/key-vault-overview.md)
- [Azure Event Grid áttekintése](overview.md)
- [Key Vault figyelése Azure Event Grid (előzetes verzió)](../key-vault/event-grid-overview.md)
- [Útmutató: Key Vault-események átirányítása az Automation Runbook (előzetes verzió)](../key-vault/event-grid-tutorial.md).
- [Az Azure Automation áttekintése](../automation/index.yml)
