---
title: Az Azure Key Vault eseményrács forrásaként
description: Az Azure Key Vault-események és az Azure Event Grid szolgáltatásban biztosított tulajdonságok és séma ismertetése
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 36b7b81a18c8725929ab5676b844e1ee319e287f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393311"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Az Azure Key Vault eseményrács forrásaként

Ez a cikk az Azure Key [Vault](../key-vault/index.yml)eseményeinek tulajdonságait és sémáját tartalmazza, jelenleg előzetes verzióban. Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md)

## <a name="event-grid-event-schema"></a>Eseményrács eseménysémája

### <a name="available-event-types"></a>Elérhető eseménytípusok

Az Azure Key Vault-fiók a következő eseménytípusokat hozza létre:

| Esemény teljes neve | Esemény megjelenítendő neve | Leírás |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Új tanúsítvány létrehozása | Új tanúsítvány vagy új tanúsítványverzió létrehozásakor aktiválódik. |
| Microsoft.KeyVault.CertificateNearExpiry | Tanúsítvány a lejárat közelében | A tanúsítvány jelenlegi verziójának lejáratakor aktiválódik. (Az esemény 30 nappal a lejárati dátum előtt következik be.) |
| Microsoft.KeyVault.CertificateExpired | A tanúsítvány lejárt | A tanúsítvány lejártakor aktiválódik. |
| Microsoft.KeyVault.KeyNewVersionCreated | A kulcs új verziója létrehozva | Új kulcs vagy új kulcsverzió létrehozásakor aktiválódik. |
| Microsoft.KeyVault.KeyLey közelében | Kulcs lejárata közelében | Akkor aktiválódik, amikor egy kulcs aktuális verziója hamarosan lejár. (Az esemény 30 nappal a lejárati dátum előtt következik be.) |
| Microsoft.KeyVault.KeyExpired | Lejárt kulcs | Egy kulcs lejártakor aktiválódik. |
| Microsoft.KeyVault.SecretNewVersionCreated | Titkos új verzió létrehozva | Új titkos vagy új titkos verzió létrehozásakor aktiválódik. |
| Microsoft.KeyVault.SecretLey közelében | Titkos lejárat közelében | Akkor aktiválódik, amikor egy titkos titok aktuális verziója hamarosan lejár. (Az esemény 30 nappal a lejárati dátum előtt következik be.) |
| Microsoft.KeyVault.SecretExpired | Titkos lejárt | Egy titkos titok lejártakor aktiválódik. |

### <a name="event-examples"></a>Példák eseményre

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

### <a name="event-properties"></a>Esemény tulajdonságai

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

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím  |Leírás  |
|---------|---------|
| [Key Vault-események figyelése az Azure Event Griddel](../key-vault/event-grid-overview.md) | A Key Vault és az Event Grid integrálásának áttekintése. |
| [Oktatóanyag: Key Vault-események létrehozása és figyelése az Event Grid segítségével](../key-vault/event-grid-tutorial.md) | További információ az Event Grid-értesítések key vaulthoz való beállításáról. |


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
