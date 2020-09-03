---
title: Az Azure Key Vault hozzáférési szabályzatával kapcsolatos problémák elhárítása
description: Az Azure Key Vault hozzáférési szabályzatával kapcsolatos problémák elhárítása
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 57baeccc9f4644ec055de638254d4613a33ef68d
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378644"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Az Azure Key Vault hozzáférési szabályzatával kapcsolatos problémák elhárítása

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Hogyan állapítható meg, hogyan és mikor érhetők el a kulcstartók?

Egy vagy több kulcstartó létrehozása után valószínűleg figyelnie kell a kulcstartók elérésének módját és időpontját. A figyelést a Azure Key Vault naplózásának engedélyezésével végezheti el, részletes útmutató a naplózás engedélyezéséhez. [További információ](https://docs.microsoft.com/azure/key-vault/general/logging):.

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Hogyan figyelhető meg a tár rendelkezésre állása, a szolgáltatási késési időszakok vagy más teljesítménymutatók a Key Vault esetében?

A szolgáltatás skálázásának megkezdése után a Key vaultba küldött kérések száma megemelkedik. Az ilyen igénynek lehetősége van arra, hogy növelje a kérések késését és a szélsőséges esetekben, hogy a kérések szabályozása megtörténjen, ami hatással lesz a szolgáltatás teljesítményére. Nyomon követheti a Key Vault teljesítmény-metrikáit, és riasztást kaphat a meghatározott küszöbértékekről, a figyelés konfigurálásának lépésenkénti útmutatójában bővebben [olvashat](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Hogyan lehet hozzáférés-vezérlést rendelni Key Vault-objektumhoz? 

A titkos/kulcs/tanúsítvány hozzáférés-vezérlési funkciójának elérhetősége itt jelenik meg, [További információ](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Hogyan biztosíthatom a Key Vault hitelesítését a hozzáférés-vezérlési házirenddel?

A felhőalapú alkalmazások Key Vaulthoz való hitelesítésének legegyszerűbb módja felügyelt identitás; a részletekért lásd: [hitelesítés Azure Key Vault](authentication.md) .
Ha helyszíni alkalmazást hoz létre, helyi fejlesztést végez, vagy más módon nem tud felügyelt identitást használni, ehelyett manuálisan is regisztrálhat egy egyszerű szolgáltatásnevet, és hozzáférést biztosíthat a kulcstartóhoz egy hozzáférés-vezérlési házirend használatával. Lásd: [hozzáférés-vezérlési házirend társítása](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Hogyan biztosíthatom az AD-csoport számára a Key Vault elérését?

Adja meg az AD-csoport engedélyeit a kulcstartónak az Azure CLI az Key Vault set-Policy parancs vagy a Azure PowerShell set-AzKeyVaultAccessPolicy parancsmag használatával. Lásd: [hozzáférési szabályzat társítása –](assign-access-policy-cli.md) parancssori felület és [hozzáférési szabályzat társítása – PowerShell](assign-access-policy-powershell.md).

Az alkalmazásnak szüksége van legalább egy, a Key vaulthoz hozzárendelt identitás-és hozzáférés-kezelési (IAM) szerepkörre is. Ellenkező esetben nem fog tudni bejelentkezni, és nem lesz megfelelő jogosultsága az előfizetéshez való hozzáféréshez. A felügyelt identitásokkal rendelkező Azure AD-csoportok akár nyolc óráig is megkövetelhetik a tokenek frissítését, és érvénybe lépnek.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Hogyan telepíthetem újra Key Vault az ARM-sablonnal a meglévő hozzáférési szabályzatok törlése nélkül?

Jelenleg Key Vault ARM-újratelepítéskor a rendszer törli a Key Vault összes hozzáférési szabályzatát, és az ARM-sablon hozzáférési szabályzatával helyettesíti azokat. Nincs növekményes beállítás Key Vault hozzáférési házirendekhez. A Key Vault hozzáférési házirendjeinek megőrzéséhez olvassa el a meglévő hozzáférési szabályzatokat a Key Vault, és töltse ki az ARM-sablont ezekkel a szabályzatokkal, hogy elkerülje a hozzáférés kimaradását.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>A következő típusú hibák ajánlott hibaelhárítási lépései

* HTTP 401: Nem hitelesített kérelem – [Hibaelhárítási lépések](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Nem megfelelő engedélyek – [Hibaelhárítási lépések](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Túl sok kérelem – [Hibaelhárítási lépések](rest-error-codes.md#http-429-too-many-requests)
* Ellenőrizze, hogy van-e hozzáférési engedélye a Key vaulthoz: [hozzáférési házirend társítása](assign-access-policy-cli.md)– parancssori felület, [hozzáférési szabályzat társítása – PowerShell](assign-access-policy-powershell.md)vagy [hozzáférési szabályzat hozzárendelésének](assign-access-policy-portal.md)engedélyezése.
* Ha problémába ütközik kulcstartó kódban való hitelesítése során, használja az [Authentication SDK-t](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Milyen bevált eljárásokat kell megvalósítani a Key Vault szabályozásakor?
Kövesse az [itt](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits) dokumentált ajánlott eljárásokat

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan lehet elhárítani a Key Vault hitelesítési hibáit: [Key Vault hibaelhárítási útmutató](rest-error-codes.md).
