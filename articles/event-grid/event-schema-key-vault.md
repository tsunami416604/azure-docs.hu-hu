---
title: Azure Event Grid eseménysémája az Azure Key Vaulthoz
description: Az Azure Key Vault-események és az Azure Event Grid szolgáltatásban biztosított tulajdonságok és séma ismertetése
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082872"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Event Grid eseménysémája az Azure Key Vaulthoz (előzetes verzió)

Ez a cikk az Azure Key [Vault](../key-vault/index.yml)eseményeinek tulajdonságait és sémáját tartalmazza, jelenleg előzetes verzióban. Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md)

## <a name="available-event-types"></a>Elérhető eseménytípusok

Az Azure Key Vault-fiók a következő eseménytípusokat hozza létre:

| Esemény teljes neve | Esemény megjelenítendő neve | Leírás |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Új tanúsítvány létrehozása | Új tanúsítvány vagy új tanúsítványverzió létrehozásakor aktiválódik. |
| Microsoft.KeyVault.CertificateNearExpiry | Tanúsítvány a lejárat közelében | A tanúsítvány jelenlegi verziójának lejáratakor aktiválódik. (Az alapértelmezett érték 30 nappal a lejárati dátum előtt van.) |
| Microsoft.KeyVault.CertificateExpired | A tanúsítvány lejárt | A tanúsítvány lejártakor aktiválódik. |
| Microsoft.KeyVault.KeyNewVersionCreated | A kulcs új verziója létrehozva | Új kulcs vagy új kulcsverzió létrehozásakor aktiválódik. |
| Microsoft.KeyVault.KeyLey közelében | Kulcs lejárata közelében | Akkor aktiválódik, amikor egy kulcs aktuális verziója hamarosan lejár. (Az alapértelmezett érték 30 nappal a lejárati dátum előtt van.) |
| Microsoft.KeyVault.KeyExpired | Lejárt kulcs | Egy kulcs lejártakor aktiválódik. |
| Microsoft.KeyVault.SecretNewVersionCreated | Titkos új verzió létrehozva | Új titkos vagy új titkos verzió létrehozásakor aktiválódik. |
| Microsoft.KeyVault.SecretLey közelében | Titkos lejárat közelében | Akkor aktiválódik, amikor egy titkos titok aktuális verziója hamarosan lejár. (Az alapértelmezett érték 30 nappal a lejárati dátum előtt van.) |
| Microsoft.KeyVault.SecretExpired | Titkos lejárt | Egy titkos titok lejártakor aktiválódik. |

## <a name="event-examples"></a>Példák eseményre

A következő példa a **Microsoft.KeyVault.SecretNewVersionCreated**sémáját mutatja be:

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

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| ---------- | ----------- |---|
| id | sztring | Az eseményt kiváltó objektum azonosítója |
| vaultName | sztring | Az eseményt kiváltó objektum kulcstartójának neve |
| objectType | sztring | Az eseményt kiváltó objektum típusa |
| objektumneve | sztring | Az eseményt kiváltó objektum neve |
| version | sztring | Az eseményt kiváltó objektum verziója |
| nbf | szám | Az eseményt kiváltó objektum 1970-01-01T00:00:00Z óta eltelt másodpercek előtti dátuma |
| Exp | szám | Az eseményt kiváltó objektum 1970-01-01T00:00:00Z ideje másodpercben |


## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md).
* Az Azure Event Grid-előfizetések létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
* Ha többet szeretne tudni a Key Vault és az Event Grid használatával való integrációról, olvassa el [a Key Vault figyelése az Azure Event Griddel című témakört (előzetes verzió).](../key-vault/event-grid-overview.md)
* A Key Vault és az Event Grid integrációjáról az [Azure Event Griddel kapcsolatos kulcstartó-értesítések fogadása és megválaszolása című témakörben (előzetes verzió) című témakörben](../key-vault/event-grid-tutorial.md)látható.
* További útmutatást a Key Vault és az Azure Automation, lásd:
    - [Mi az Azure Key Vault?](../key-vault/key-vault-overview.md)
    - [Key Vault figyelése az Azure Event Griddel (előzetes verzió)](../key-vault/event-grid-overview.md)
    - [A kulcstartó-értesítések fogadása és megválaszolása az Azure Event Grid del (előzetes verzió)](../key-vault/event-grid-tutorial.md)
    - [Az Azure Automation áttekintése](../automation/index.yml)
