---
title: Azure Key Vault Azure Event Gridi esemény sémája
description: A Azure Key Vault eseményekhez megadott tulajdonságokat és sémát ismerteti Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082872"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Key Vault Azure Event Gridi esemény sémája (előzetes verzió)

Ez a cikk a [Azure Key Vault](../key-vault/index.yml), jelenleg előzetes verzióban elérhető események tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

## <a name="available-event-types"></a>Elérhető események típusai

Egy Azure Key Vault fiók a következő típusú eseményeket hozza létre:

| Esemény teljes neve | Esemény megjelenítendő neve | Leírás |
| ---------- | ----------- |---|
| Microsoft. kulcstartó. CertificateNewVersionCreated | Tanúsítvány új verziója létrehozva | Új tanúsítvány vagy új tanúsítvány-verzió létrehozásakor aktiválódik. |
| Microsoft. kulcstartó. CertificateNearExpiry | A tanúsítvány hamarosan lejár | Akkor aktiválódik, ha a tanúsítvány aktuális verziója hamarosan lejár. (Az alapértelmezett érték 30 nap a lejárati dátum előtt.) |
| Microsoft. kulcstartó. CertificateExpired | A tanúsítvány lejárt | A tanúsítvány lejártakor aktiválódik. |
| Microsoft. kulcstartó. KeyNewVersionCreated | A kulcs új verziója létrehozva | Új kulcs vagy új kulcs létrehozásakor aktiválódik. |
| Microsoft. kulcstartó. KeyNearExpiry | Kulcs közel lejárata | Akkor aktiválódik, ha a kulcs aktuális verziója hamarosan lejár. (Az alapértelmezett érték 30 nap a lejárati dátum előtt.) |
| A Microsoft. kulcstartó. kulcstartó lejárt | A kulcs lejárt | A kulcs lejártakor aktiválódik. |
| Microsoft. kulcstartó. SecretNewVersionCreated | A titkos új verzió létrehozva | Új titok vagy új titkos verzió létrehozásakor aktiválódik. |
| Microsoft. kulcstartó. SecretNearExpiry | Közel lejáró titkos kód | Akkor aktiválódik, ha a titkos kulcs aktuális verziója hamarosan lejár. (Az alapértelmezett érték 30 nap a lejárati dátum előtt.) |
| Microsoft. kulcstartó. SecretExpired | A titkos kód lejárt | A titkos kód lejártakor aktiválódik. |

## <a name="event-examples"></a>Példák az eseményekre

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

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Leírás |
| ---------- | ----------- |---|
| id | sztring | Az eseményt kiváltó objektum azonosítója |
| vaultName | sztring | Az eseményt kiváltó objektum kulcstárolójának neve |
| objectType | sztring | Az eseményt kiváltó objektum típusa |
| ObjectName | sztring | Az eseményt kiváltó objektum neve |
| version | sztring | Az eseményt kiváltó objektum verziója |
| nbf | szám | A nem az idő előtti dátum másodpercben 1970-01-01T00:00: az eseményt kiváltó objektum 00Z |
| exp | szám | Az eseményt kiváltó objektum lejárati dátuma (másodpercben) 1970-01-01T00:00:00Z |


## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* Ha többet szeretne megtudni a Event Gridekkel való Key Vault-integrációról, tekintse meg [a Key Vault Azure Event Grid (előzetes verzió) figyelése](../key-vault/event-grid-overview.md)című témakört.
* A Event Gridekkel való Key Vault integrációval kapcsolatos oktatóanyagért lásd: a [Key Vault-értesítések fogadása és reagálás a Azure Event Grid (előzetes verzió) használatával](../key-vault/event-grid-tutorial.md).
* A Key Vault és Azure Automation további útmutatását a következő témakörben tekintheti meg:
    - [Mi az Azure Key Vault?](../key-vault/key-vault-overview.md)
    - [Key Vault figyelése Azure Event Grid (előzetes verzió)](../key-vault/event-grid-overview.md)
    - [A Key Vault-értesítések fogadása és reagálás Azure Event Grid (előzetes verzió)](../key-vault/event-grid-tutorial.md)
    - [Az Azure Automation áttekintése](../automation/index.yml)
