---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 9a39abf77a7396302f93e5a423271402b7c3edb3
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54084004"
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
> A fenti küszöbértékek vannak súlyozott és kényszerítési az összegük. 125 RSA-HSM - 4k műveletek és a 0 RSA-HSM - 2k, vagy a 124 RSA-HSM - 4k és 16 RSA-HSM - 2k teheti meg. Azonos 10 másodperces intervallumban, a későbbiekben minden egyéb művelet hatására AKV ügyfél kivételt.

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
