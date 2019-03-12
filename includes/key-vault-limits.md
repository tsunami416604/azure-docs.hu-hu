---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0e55c372c6f5dc3484bd64cf4f328479d2d0b245
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553580"
---
## <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Kulcs tranzakció (10 másodpercen belül a engedélyezett tranzakciók maximális száma régiónként tároló<sup>1</sup>):

|Kulcs típusa|HSM-kulcsig<br>Kulcs létrehozása|HSM-kulcsig<br>Minden egyéb tranzakció|Szoftverkulcs<br>Kulcs létrehozása|Szoftverkulcs<br>Minden egyéb tranzakció|
|:---|---:|---:|---:|---:|
|RSA 2,048-bit|5|1,000|10|2,000|
|3072-bites RSA|5|250|10|500|
|RSA 4096-bites|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> Az előző táblázatban látható, hogy az RSA 2048 bites szoftverkulcsok, 10 másodpercenként 2000 GET tranzakciók engedélyezettek. RSA 2048 bites HSM-kulcsok 1000 GET tranzakció / 10 másodperc engedélyezettek.
>
> A szabályozási határértékek súlyozott vannak, és az összegük kényszerítési van. Például az előző táblázatban látható, RSA HSM-kulcsokat, a GET műveletet hajt végre esetén 8 alkalommal drágább 2048 bites kulcsok képest 4096-bites kulcsok használatához. Ez azért van, 1000/125 = 8.
>
> Egy Azure Key Vault ügyfélnél is végrehajthatja egy adott időszakban 10 másodperc *csak egy* a következő műveleteket, mielőtt tapasztal egy `429` szabályozás HTTP-állapotkód:
> - 2000 RSA 2048 bites szoftverkulcs GET-tranzakciók
> - RSA 2048 bites HSM-kulcs LEKÉRÉSE 1000 tranzakció
> - 125 RSA 4,096-bit HSM-key GET transactions
> - 124 RSA 4,096-bit HSM-key GET transactions and 8 RSA 2,048-bit HSM-key GET transactions

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Titkos kódok, a felügyelt tárfiókok hozzáférési kulcsainak és a tároló tranzakció:
| Tranzakció típusa | Engedélyezett 10 másodpercen belül a tranzakciók maximális száma régiónként tároló<sup>1</sup> |
| --- | --- |
| Minden tranzakció |2,000 |

Hogyan kezelheti a szabályozást a korlátok túllépése esetén további információkért lásd: [Azure Key Vault szabályozási útmutatójában](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> minden tranzakció esetében előfizetési szintű korlátozva van ötször a key vault-korlát. HSM-egyéb tranzakció / előfizetés például korlátozva, összesen 5 000 tranzakció előfizetésenként 10 másodperc.
