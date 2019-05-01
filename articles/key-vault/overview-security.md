---
title: Az Azure Key Vault biztonsági |} A Microsoft Docs
description: Hozzáférési engedélyek Azure Key Vault, a kulcsok és titkos kulcsok kezeléséhez. A Key Vault, és hogyan védheti meg a kulcstartó hitelesítési és engedélyezési modellt ismerteti.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 5b32e4897e718e0e411caf9ba76b036f1352bde0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715271"
---
# <a name="azure-key-vault-security"></a>Az Azure Key Vault biztonsági

Meg szeretné védeni a titkosítási kulcsokat és titkokat – például a tanúsítványok, kapcsolati karakterláncok és a felhőben jelszavakat, így használja az Azure Key Vault. Mivel bizalmas tárolja, és a kritikus fontosságú üzleti adatok, kell intézkedjen, hogy a tárolók és a bennük tárolt adatok biztonságának növelése. Ez a cikk azzal foglalkozik, azokat a fogalmakat, amelyek az Azure Key Vault-biztonság tervezése során megfontolandó.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Ha az Azure-előfizetésében kulcstartót hoz létre, azt automatikusan az előfizetés az Azure AD-bérlő társítva. Bárki, kezelése vagy tartalmat lekérjen egy tárolót az Azure AD által hitelesíteni kell.

- Hitelesítés a hívó azonosítását hoz létre.
- Engedélyezési meghatározza, hogy melyik a hívó hajthat végre műveleteket. A Key Vaultban engedélyezési kombinációját használja [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) és az Azure Key Vault hozzáférési házirendeket.

### <a name="access-model-overview"></a>Hozzáférés modell áttekintése

Tárolók való hozzáférés a két felület vagy síkok keresztül történik. Ezek síkok a felügyeleti sík és az adatsíkhoz.

- A *felügyeleti sík* felügyelt Key Vault magát, és azt az, hogy a felület hozhat létre és törölhetnek tárolókat. Olvassa el a key vault tulajdonságok is, és hozzáférési szabályzatok kezelése.
- A *adatsík* lehetővé teszi, hogy a key vaultban tárolt adatokat. Hozzáadhat, törlése és módosítása a kulcsok, titkos kódok és tanúsítványok.

Mindkét sík a key vault eléréséhez valamennyi hívót (felhasználók vagy alkalmazások) kell hitelesítését és engedélyezését. Mindkét sík Azure Active Directory (Azure AD) használja a hitelesítéshez. A hitelesítéshez a felügyeleti sík szerepköralapú hozzáférés-vezérlés (RBAC), és az adatsík a kulcstartó-hozzáférési szabályzat használja.

A modell egy egyetlen, mindkét sík hitelesítési mechanizmust alkalmaz a számos előnye van:

- Szervezetek szabályozhatja a hozzáférést a szervezeten belüli összes kulcstárolójának az központilag.
- Ha egy felhasználó távozik, azonnal elveszíti hozzáférést a szervezeten belüli összes kulcstartó.
- Szervezetek szabhatja testre a hitelesítési beállításai segítségével az Azure ad-ben, mint például a multi-factor authentication szolgáltatás engedélyezése a fokozott biztonság

### <a name="managing-administrative-access-to-key-vault"></a>Key Vault való rendszergazdai hozzáférés kezelése

Ha egy kulcstartót hoz létre egy erőforráscsoportot, hozzáférés kezelése az Azure AD használatával. Biztosítson a felhasználók vagy csoportok lehetővé teszi az erőforráscsoport kulcstartóit kezeljék. A megfelelő RBAC-szerepkörök hozzárendelésével biztosíthat hozzáférést egy adott hatókör szintjén. Hozzáférést biztosítani egy felhasználónak kulcstartóit kezeljék, hozzárendeli egy előre meghatározott `key vault Contributor` szerepkört a felhasználóhoz egy adott hatókörnél. A következő hatókörök szintek hozzárendelheti az RBAC-szerepkör:

- **Előfizetés**: Az RBAC-szerepkör az előfizetés szintjén hozzárendelt összes erőforráscsoportra és erőforrások adott előfizetésen belül vonatkozik.
- **Erőforráscsoport**: Az erőforráscsoport szintjén hozzárendelt egy RBAC szerepkör az erőforráscsoport valamennyi erőforrására vonatkozik.
- **Adott erőforrás**: Az RBAC-szerepkör a meghatározott erőforráshoz rendelt adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Számos előre definiált szerepkörök tartoznak. Ha egy előre meghatározott szerepkör nem fér el az igényeinek, megadhatja saját szerepkört. További információkért lásd: [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Ha egy felhasználó `Contributor` a kulcstartó felügyeleti síkjához engedélyeket, a felhasználó lehet hozzáférést magukat az adatsík a kulcstartó hozzáférési házirend beállításával. Meg kell mértékben vezérelhetik kinek van `Contributor` a kulcstartók eléréséhez. Győződjön meg arról, hogy csak az arra jogosult személyek eléréséhez, és a kulcstartók, kulcsok, titkos kódok és tanúsítványok kezelése.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>A Key Vault-adatokhoz való hozzáférés szabályozása

A Key Vault hozzáférési házirendekkel külön-külön kulcsok, titkos kódok vagy tanúsítvány engedélyezheti. Felhasználói hozzáférés kizárólag a kulcsokhoz és titkos kódok, nem adhat meg. Hozzáférési engedélyeket kulcsok, titkos kódok és tanúsítványok kezelése tárolószinten engedélyezett.

> [!IMPORTANT]
> Kulcstartó-hozzáférési szabályzatok nem támogatottak egy adott kulcs, a titkos kulcsot vagy a tanúsítvány részletes, az Objektumszintű engedélyeket. Amikor egy felhasználó kulcsok létrehozására és törlésére engedélyt kap, minden kulcs ezeket a műveleteket, hogy a key vault végrehajtható.

Kulcstartó hozzáférési házirendjeinek beállításához használja a [az Azure portal](https://portal.azure.com/), a [Azure CLI-vel](../cli-install-nodejs.md), [Azure PowerShell-lel](/powershell/azureps-cmdlets-docs), vagy a [Key Vault felügyeleti REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Adatsík-hozzáférés használatával korlátozhatja [virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Konfigurálható [tűzfalak és virtuális hálózati szabályok](key-vault-network-security.md) számára egy további biztonsági réteget.

## <a name="network-access"></a>Hálózati hozzáférés

A tárolók kitettségének csökkentheti megadásával, mely IP-címek tudja elérni őket. A virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault lehetővé teszi, hogy korlátozza a hozzáférést egy adott virtuális hálózatban. A végpontok is lehetővé teszik az IPv4-címtartományokat (az internet protocol version 4) listáját való hozzáférés korlátozásához. A key vault kívül tudja a forrásokhoz való csatlakozás bármely felhasználó hozzáférése megtagadva.

Tűzfal után szabályok érvényben, felhasználók is csak olvasási adatok a Key Vaultból, ha az ügyfélkérések engedélyezett virtuális hálózatok vagy IPv4-címtartományokat. Ez vonatkozik a Key Vault elérése az Azure Portalról. Bár az Azure Portalon, egy kulcstárolóba is böngésző felhasználók számára, akkor előfordulhat, hogy nem tudják kulcsok listázása, titkos kódok és tanúsítványok, ha az ügyfélszámítógép nem szerepel az engedélyezési listán. Ez is hatással van a Key Vault-választó más Azure-szolgáltatások. Lehet, hogy a felhasználók tudni kulcstartók listája látható, de nem kulcsok, listázását, ha a tűzfal-szabályok megakadályozzák fejlesztőkörnyezetükben.

További információ az Azure Key Vault hálózati cím felülvizsgálati [virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Figyelés

Key Vault naplózásának menti a tárfiók a tevékenységekre vonatkozó adatokat. Key Vault-naplók:

- Az összes hitelesített REST API-kérelmek, többek között a sikertelen kérelmek
  - Műveletek a key vaultban. Ezek a műveletek közé tartozik a létrehozás, törlés, hozzáférési szabályzatok beállítása és kulcstartó-attribútumokat, például címkék frissítése.
  - Műveletek a kulcsok és titkos kulcsok a key vaultban, többek között:
    - Létrehozása, módosítása vagy törlése, ezek a kulcsok vagy titkos kulcsok.
    - Aláírása, ellenőrzése, titkosítása, visszafejtése, alkalmazásburkoló és kicsomagolási kulcsok, titkos adatait, és a listaelem kulcsok és titkos kulcsok (és azok verzióinak).
- A 401-es választ eredményező, nem hitelesített kérelmek. Például a kérelmek, amelyek nem rendelkeznek tulajdonosi jogkivonattal, helytelen formátumúak vagy lejártak, vagy, amelyek érvénytelen jogkivonatot.

Naplózási információk érhetők el a key vaulttal műveletet követő 10 percen belül. Szolgáltatás kezelése a tárfiók naplófájljait Önnek. 

- Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
- Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.

Javaslat a biztonságos kezelése a storage fiókok tekintse át a [Azure Storage biztonsági útmutatóját](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>További lépések

- [Virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md)
- [Virtuális hálózati Szolgáltatásvégpontok az Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
