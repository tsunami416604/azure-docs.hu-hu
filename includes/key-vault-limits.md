---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: efa367157a8fd896cdc9680bf2ab6ba6a9e3dbb0
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429977"
---
Kulcs tranzakció (10 másodperc az engedélyezett maximális tranzakciók tároló régiónként<sup>1</sup>):

|Kulcs típusa|HSM-kulcs<br>Kulcs létrehozása|HSM-kulcs<br>Minden egyéb tranzakció|Szoftver-kulcs<br>Kulcs létrehozása|Szoftver-kulcs<br>Minden egyéb tranzakció|
|:---|---:|---:|---:|---:|
|RSA 2048-bites|5|1000|10|2000|
|3072-bites RSA|5|250|10|500|
|RSA 4096-bites|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

> [!NOTE]
> Ha az alábbi táblázat tekinti meg, láthatja, hogy a szoftver-alapú kulcsokkal lehetővé tesszük 2000 tranzakciók száma 10 másodpercet, és az HSM biztonsági kulcsok lehetővé tesszük az 1000 tranzakció / 10 másodperc. Szoftver-biztonsági tranzakciók 2048 bites kulcsok, 3072 kulcsok arány, 500-as/2000 vagy 0,4. Ez azt jelenti, ha egy ügyfél biztosítja 500 3072 kulcs tranzakció 10 másodpercben, hogy elérik a maximális korlátot, és bármilyen egyéb fő művelet nem hajtható végre. 
   
|Kulcs típusa  | Szoftverkulcs |HSM-kulcs  |
|---------|---------|---------|
|RSA 2048-bites     |    2000     |   1000    |
|3072-bites RSA     |     500    |    250     |
|RSA 4096-bites     |    125     |    250     |
|ECC P-256     |    2000     |  1000     |
|ECC P-384     |    2000     |  1000     |
|ECC P-521     |    2000     |  1000     |
|ECC SECP256K1     |    2000     |  1000     |


Titkos kódok, felügyelt Tárfiókkulcsok és vault-tranzakciókra:
| Tranzakció típusa | 10 másodperc az engedélyezett maximális tranzakciók tároló régiónként<sup>1</sup> |
| --- | --- |
| Minden tranzakció |2000 |
|

Lásd: [Azure Key Vault szabályozási útmutatójában](../articles/key-vault/key-vault-ovw-throttling.md) információkat, hogyan kezelheti a szabályozást a korlátok túllépése esetén.

<sup>1</sup> előfizetési szintű korlátja minden tranzakció esetében, 5 x a key vault-korlát is. Ha például HSM - más tranzakciók száma előfizetésenként legfeljebb 5000 tranzakciók előfizetésenként 10 másodperc múlva.
