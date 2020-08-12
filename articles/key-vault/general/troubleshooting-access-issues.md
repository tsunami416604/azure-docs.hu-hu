---
title: Az Azure Key Vault hozzáférési szabályzatával kapcsolatos problémák elhárítása
description: Az Azure Key Vault hozzáférési szabályzatával kapcsolatos problémák elhárítása
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.openlocfilehash: e52fe8e738f2a35204078b27019ea3742c608560
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122171"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Az Azure Key Vault hozzáférési szabályzatával kapcsolatos problémák elhárítása

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Hogyan állapítható meg, hogyan és mikor érhetők el a kulcstartók?
Egy vagy több kulcstartó létrehozása után valószínűleg figyelnie kell a kulcstartók elérésének módját és időpontját. Ezt úgy teheti meg, hogy engedélyezi a Azure Key Vault naplózását, a naplózás engedélyezésének lépésenkénti útmutatóját. [További információ](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Hogyan figyelhető meg a tár rendelkezésre állása, a szolgáltatási késési időszakok vagy más teljesítménymutatók a Key Vault esetében?
A szolgáltatás skálázásának megkezdése után a Key vaultba küldött kérések száma növekedni fog. Ez potenciálisan növelheti a kérések késését, és szélsőséges esetekben a kérések szabályozása is lehetséges, ami hatással lehet a szolgáltatás teljesítményére. Nyomon követheti a Key Vault teljesítmény-metrikáit, és riasztást kaphat a meghatározott küszöbértékekről, a figyelés konfigurálásának lépésenkénti útmutatójában bővebben [olvashat](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Hogyan lehet hozzáférés-vezérlést rendelni Key Vault-objektumhoz? 
A titkos/kulcs/tanúsítvány hozzáférés-vezérlési funkciójának elérhetősége itt jelenik meg, [További információ](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/.32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Hogyan biztosíthatom a Key Vault hitelesítését a hozzáférés-vezérlési házirenddel?
A felhőalapú alkalmazások Key Vaulthoz való hitelesítésének legegyszerűbb módja felügyelt identitás; a részletekért tekintse meg a [app Service felügyelt identitás használata a Azure Key Vault eléréséhez]( https://docs.microsoft.com/azure/key-vault/general/managed-identity) című témakört.
Ha helyszíni alkalmazást hoz létre, helyi fejlesztést végez, vagy más módon nem tud felügyelt identitást használni, manuálisan is regisztrálhat egy egyszerű szolgáltatásnevet, és hozzáférést biztosíthat a kulcstartóhoz egy hozzáférés-vezérlési házirend használatával. [további](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)információk.


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Hogyan biztosíthatom az AD-csoport számára a Key Vault elérését?
Adja meg az AD-csoport engedélyeit a kulcstartónak az Azure CLI az Key Vault set-Policy parancs vagy a Azure PowerShell set-AzKeyVaultAccessPolicy parancsmag használatával. Példaként tekintse át [az alkalmazás, az Azure ad-csoport vagy a Key Vault felhasználói hozzáférése lehetőséget](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault).

Az alkalmazásnak szüksége van legalább egy, a Key vaulthoz hozzárendelt identitás-és hozzáférés-kezelési (IAM) szerepkörre is. Ellenkező esetben nem fog tudni bejelentkezni, és nem lesz megfelelő jogosultsága az előfizetéshez való hozzáféréshez. A felügyelt identitásokkal rendelkező Azure AD-csoportok a token frissítéséhez és a 8hr szükségesek lehetnek.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>A következő típusú hibák ajánlott hibaelhárítási lépései
* HTTP 401: nem hitelesített kérelem – [hibaelhárítási lépések](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403: nem megfelelő engedélyek – [hibaelhárítási lépések](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429: túl sok kérelem – [hibaelhárítási lépések](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* Ellenőrizze, hogy törölte-e a Key Vault hozzáférési engedélyét: [Kulcstartó-hozzáférési szabályzatok](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* Ha problémába ütközik kulcstartó kódban való hitelesítése során, használja az [Authentication SDK-t](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Milyen bevált eljárásokat kell megvalósítani a Key Vault szabályozásakor?
Kövesse az [itt](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits) dokumentált ajánlott eljárásokat

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan lehet elhárítani a Key Vault hitelesítési hibáit. [Key Vault hibaelhárítási útmutató](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
