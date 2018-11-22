---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ed0c387f9785336fbf18b3fd3c0cd9a7b09df633
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279682"
---
Kulcs tranzakció (10 másodperc az engedélyezett maximális tranzakciók tároló régiónként<sup>1</sup>):

|Kulcs típusa|HSM-Key<br>Kulcs létrehozása|HSM-kulcs<br>Minden egyéb tranzakció|Szoftver-kulcs<br>Kulcs létrehozása|Szoftver-kulcs<br>Minden egyéb tranzakció|
|:---|---:|---:|---:|---:|
|RSA 2048-bites|5|1000|10|2000|
|3072-bites RSA|5|250|10|500|
|RSA 4096-bites|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

Titkos kódok, felügyelt Tárfiókkulcsok és vault-tranzakciókra:
| Tranzakció típusa | 10 másodperc az engedélyezett maximális tranzakciók tároló régiónként<sup>1</sup> |
| --- | --- |
| Minden tranzakció |2000 |
|

Lásd: [Azure Key Vault szabályozási útmutatójában](../articles/key-vault/key-vault-ovw-throttling.md) információkat, hogyan kezelheti a szabályozást a korlátok túllépése esetén.

<sup>1</sup> előfizetési szintű korlátja minden tranzakció esetében, 5 x a key vault-korlát is. Ha például HSM - más tranzakciók száma előfizetésenként legfeljebb 5000 tranzakciók előfizetésenként 10 másodperc múlva.
