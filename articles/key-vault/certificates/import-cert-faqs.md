---
title: Gyakori kérdések – Azure Key Vault tanúsítvány importálása
description: Gyakori kérdések – Azure Key Vault tanúsítvány importálása
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 402672d8eeaae8a5097e2ab2905997eb1f646ad6
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056346"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Gyakori kérdések – Azure Key Vault tanúsítvány importálása

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Hogyan importálhatók tanúsítvány Azure Key Vault?

Tanúsítvány importálása – importálási művelet esetén az Azure Key Vault két, PEM és PFX formátumú tanúsítványt fogad el. Bár a PEM-fájlok csak a nyilvános részből állnak, az Azure Key Vault megköveteli és csak a fájl mappájába és egy titkos kulccsal rendelkező PEM-vagy PFX-fájlt fogad el. [A tanúsítvány importálásához kövesse az oktatóanyagot](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>Miután importálta a jelszóval védett tanúsítványt a kulcstartóba, majd letölti, nem tudom megtekinteni a tanúsítványhoz tartozó jelszót?
    
A feltöltött védett tanúsítvány a Key vaultba való tárolás után nem fogja menteni a hozzá társított jelszót. Erre az importálási művelet során csak egyszer van szükség. Bár ez a kialakítási koncepció, a tanúsítványt mindig titkos fájlként szerezheti be, és a Base64-ről PFX-re alakíthatja az előző jelszót [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)használatával.

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Hogyan oldható fel a "hibás paraméteres hiba"? Mely tanúsítványformátumok importálását támogatja a Key Vault?

A tanúsítvány importálásakor győződjön meg arról, hogy a kulcs maga is szerepel a fájlban. Ha a titkos kulccsal külön formátumban van, akkor a kulcsot össze kell kapcsolni a tanúsítvánnyal. Egyes hitelesítésszolgáltatók különböző formátumú tanúsítványokat biztosítanak, ezért a tanúsítvány importálása előtt győződjön meg arról, hogy a. PEM vagy a. pfx formátumban vannak megadva, és hogy a használt kulcs RSA vagy ECC. Tekintse át ezeket a [tanúsítványokra vonatkozó követelmények](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) és a [tanúsítványokra vonatkozó követelmények](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection)áttekintéséhez.

###  <a name="can-i-import-certificate-using-arm-template"></a>Tudom importálni a tanúsítványt az ARM-sablon használatával?

Nem, az ARM-sablonok használatával nem lehet certifiate műveleteket végezni. Az ajánlott megkerülő megoldás az API-ban, a CLI-ben vagy a PowerShellben lévő tanúsítványalapú importálási módszerek használata. Ha rendelkezik meglévő-tanúsítvánnyal, akkor azt titkos fájlként is importálhatja.

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>„Hiba történt” hibaüzenet a tanúsítvány portálon keresztül történő importálásakor. Hogyan lehet további vizsgálatot végezni?
    
További leíró hiba megtekintéséhez importálja a tanúsítványfájl az [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) vagy a [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)használatával.

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Hogyan oldható fel a hiba típusa: hozzáférés megtagadva, vagy a felhasználó nem jogosult a tanúsítvány importálására?
    
Ehhez a művelethez a tanúsítványokra/importálásra vonatkozó engedély szükséges. Keresse meg a Key Vault helyét. A hozzáférési szabályzatok között meg kell adnia a felhasználónak a megfelelő engedélyeket. Navigáljon a Key Vault> hozzáférési szabályzatok > adja hozzá a hozzáférési házirendet > válassza a tanúsítványok engedélyei lehetőséget (vagy az engedélyek megadását) > elsődleges > keresse meg, majd adja hozzá a felhasználó e-mail címét. [További információ a tanúsítványokkal kapcsolatos hozzáférési házirendekről](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Hogyan oldható fel a hiba típusa: ütközés a tanúsítvány létrehozásakor?
    
A tanúsítvány nevének egyedinek kell lennie. Előfordulhat, hogy az azonos nevű tanúsítvány nem törölhető állapotban van, a tanúsítványnak az Azure Key vaultban való [összetételéhez](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) hasonlóan, ha van egy másik kulcs vagy titok a tanúsítványhoz használni kívánt Key Vault ugyanazzal a névvel, és el kell távolítania az adott kulcsot vagy titkot, vagy más nevet kell használnia a tanúsítványhoz. [törölt tanúsítvány megtekintése](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Miért kapok a "hiba típusa: karakter hossza túl hosszú"?
    
* A tanúsítvány tulajdonosának neve hossza 200 karakteres.
* A tanúsítvány jelszavának hossza 200 karakteres.

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Importálhatók a lejárt tanúsítványok az Azure Key vaultban?
    
Nem, a lejárt PFX-tanúsítványok nem lesznek importálva Azure Key Vaultba.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Hogyan válthatom be a tanúsítványt a megfelelő formátumba?

Megkérheti a hitelesítésszolgáltatót, hogy a szükséges formátumban adja meg a tanúsítványt, továbbá vannak olyan harmadik féltől származó eszközök is, amelyek segítségével a megfelelő formátumra alakíthatja át a tanúsítványt, azonban a Microsoft nem fog tudni további tanácsot adni a tanúsítvány kívánt formátumban való beszerzéséről.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Importálhatók a nem partneri hitelesítésszolgáltatóktól származó tanúsítványok?
Igen, a tanúsítványok bármely HITELESÍTÉSSZOLGÁLTATÓTÓL importálhatók, de a Key Vault nem fogja tudni automatikusan megújítani ezeket a tanúsítványokat. E-mail-értesítéseket állíthat be, hogy értesítést kapjon a tanúsítvány lejáratáról.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Ha importálok egy tanúsítványt egy partner HITELESÍTÉSSZOLGÁLTATÓTÓL, akkor is működik az automatikus megújítás funkció?
Igen, meg kell győződnie arról, hogy a feltöltés után meg kell adnia az autorotációt a tanúsítvány kiállítási házirendjében. A módosításokat a rendszer a következő ciklus vagy tanúsítvány verziójának megfelelően is megjeleníti.

### <a name="unable-to-see-the-app-service-certificate-imported-to-key-vault"></a>Nem látható a Key Vaultba importált App Service-tanúsítvány? 
Ha sikeresen importálta a tanúsítványt, tekintse át a titkok panel alatt.


## <a name="next-steps"></a>További lépések

- [Tanúsítványok Azure Key Vault](/azure/key-vault/certificates/about-certificates)
