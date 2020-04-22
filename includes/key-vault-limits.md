---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 8247b1cedc2c5ebc8577af6be485aed0fcd5d6af
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768734"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Kulcstranzakciók (maximális tranzakciók 10 másodperc alatt engedélyezettek, tárolónként régiónként<sup>1):</sup>

|Kulcs típusa|HSM-kulcs<br>CREATE kulcs|HSM-kulcs<br>Minden egyéb tranzakció|Szoftverkulcs<br>CREATE kulcs|Szoftverkulcs<br>Minden egyéb tranzakció|
|:---|---:|---:|---:|---:|
|RSA 2048 bites|5|1,000|10|2000|
|RSA 3072 bites|5|250|10|500|
|RSA 4096 bites|5|125|10|250|
|ECC P-256|5|1,000|10|2000|
|ECC P-384|5|1,000|10|2000|
|ECC P-521|5|1,000|10|2000|
|ECC SECP256K1|5|1,000|10|2000|

> [!NOTE]
> Az előző táblázatban azt látjuk, hogy az RSA 2048 bites szoftverkulcsok esetén 10 másodpercenként 2000 GET tranzakció engedélyezett. RsA 2048 bites HSM-kulcsok esetén 10másodpercenként 1000 GET tranzakció engedélyezett.
>
> A fojtási küszöbértékek súlyozása, és a végrehajtás az azok összegét. Például, ahogy az előző táblázatban látható, amikor GET műveleteket hajt végre az RSA HSM-kulcsokon, a 4096 bites kulcsok használata nyolcszor drágább, mint a 2048 bites kulcsok. Ez azért van, mert 1000/125 = 8.
>
> Egy adott 10 másodperces időközben az Azure Key Vault-ügyfél csak az `429` alábbi műveletek *egyikét* tudja megtenni, mielőtt http-állapotkódot kapna:
> - 2000 RSA 2048 bites szoftverkulcsOS GET tranzakció
> - 1000 RSA 2048 bites HSM-kulcs GET tranzakció
> - 125 RSA 4096 bites HSM-kulcs GET tranzakció
> - 124 RSA 4096 bites HSM-kulcs GET tranzakció és 8 RSA 2048 bites HSM-kulcs GET tranzakció

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Titkos kulcsok, felügyelt tárfiókkulcsok és tárolótranzakciók:

| Tranzakciók típusa | Maximális tranzakciók engedélyezett 10 másodperc, egy tároló régiónként<sup>1</sup> |
| --- | --- |
| Minden tranzakció |2000 |

A rról, hogyan kell kezelni a szabályozás, ha ezeket a korlátokat túllépi, lásd: [Azure Key Vault szabályozásútmutatást.](../articles/key-vault/key-vault-ovw-throttling.md)

<sup>1</sup> Az összes tranzakciótípusra vonatkozó előfizetési szintű korlát a kulcstároló-korlátonként ötszörösebb. Például hsm-egyéb tranzakciók előfizetésenként legfeljebb 5000 tranzakció 10 másodperc előfizetésenként.

### <a name="azure-private-link-integration"></a>Az Azure Private Link integrációja

| Erőforrás | Korlát |
| -------- | ----- |
| Személyes végpontok kulcstárolónként | 64 |
| Kulcstartók előfizetésenként privát végpontokkal | 64 |
