---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a8979edf94c0dd0271293feb28c18530faeba09c
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56660135"
---
## <a name="key-transactions-max-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Kulcs tranzakció (10 másodperc az engedélyezett maximális tranzakciók tároló régiónként<sup>1</sup>):

|Kulcs típusa|HSM-key<br>Kulcs létrehozása|HSM-key<br>Minden egyéb tranzakció|Szoftver-kulcs<br>Kulcs létrehozása|Szoftver-kulcs<br>Minden egyéb tranzakció|
|:---|---:|---:|---:|---:|
|RSA 2048-bit|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096-bites|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|

> [!NOTE]
> A fenti táblázatban láthatjuk, hogy RSA 2048-bites szoftver-kulcsok, hogy 10 másodpercenként 2000 GET-tranzakciókat, és, hogy RSA 2048-bites HSM-kulcsok, azt engedélyezhetik 1000 GET tranzakció / 10 másodperc.
>
> Vegye figyelembe, hogy a szabályozási határértékek súlyozott vannak, és az összegük kényszerítési van. Ha például a fenti táblázatban, láthatjuk, hogy a RSA HSM-kulcsok GET műveletek végrehajtása esetén 8-szorosának drágább 2048 bites kulcsok képest 4096-bites kulcsok használatára (1000/125 óta = 8). Így az egy adott időszakban 10 másodperc az AKV-ügyfél sikerült közül pontosan elolvasása előtt a következő egy `429` szabályozás HTTP-állapotkód:
> - 2000 RSA 2048-bites szoftverkulcs GET-tranzakciók, **vagy**
> - 1000 RSA 2048-bites HSM-kulcs LEKÉRÉSE tranzakció, **vagy**
> - RSA 4096-bites HSM-kulcs LEKÉRÉSE tranzakció 125, **vagy**
> - 124 RSA 4096-bites HSM-kulcs LEKÉRÉSE és 8 RSA 2048-bites HSM-kulcs LEKÉRÉSE tranzakciókat.

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Titkos kódok, felügyelt Tárfiókkulcsok és vault-tranzakciókra:
| Tranzakció típusa | 10 másodperc az engedélyezett maximális tranzakciók tároló régiónként<sup>1</sup> |
| --- | --- |
| Minden tranzakció |2000 |

Lásd: [Azure Key Vault szabályozási útmutatójában](../articles/key-vault/key-vault-ovw-throttling.md) információkat, hogyan kezelheti a szabályozást a korlátok túllépése esetén.

<sup>1</sup> előfizetési szintű korlátja minden tranzakció esetében, 5 x a key vault-korlát is. Ha például HSM - más tranzakciók száma előfizetésenként legfeljebb 5000 tranzakciók előfizetésenként 10 másodperc múlva.
