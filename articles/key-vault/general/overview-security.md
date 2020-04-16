---
title: Az Azure Key Vault biztonsága | Microsoft dokumentumok
description: Az Azure Key Vault hozzáférési engedélyeit, a kulcsokat és a titkos kulcsokat kezelheti. Ismerteti a key vault hitelesítési és engedélyezési modelljét, és a kulcstartó biztonságossá tétele.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: dbe13b2d1291f212af7da9d1176bc3d90997978b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428965"
---
# <a name="azure-key-vault-security"></a>Az Azure Key Vault biztonsága

Az Azure Key Vault használatával meg kell védenie a titkosítási kulcsokat és a titkos kulcsokat, például a tanúsítványokat, a kapcsolati karakterláncokat és a jelszavakat. Mivel bizalmas és üzleti szempontból kritikus adatokat tárol, lépéseket kell tennie a tárolók és a bennük tárolt adatok biztonságának maximalizálása érdekében. Ez a cikk ismerteti az Azure Key Vault-biztonság tervezésekor figyelembe vehetővé kell tennie néhány fogalmat.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Amikor létrehoz egy kulcstartót egy Azure-előfizetésben, az automatikusan társítva lesz az előfizetés Azure AD-bérlőjéhez. Bárki, aki megpróbálja kezelni vagy lekérni a tartalmat egy tárolóból kell hitelesíteni az Azure AD.

- A hitelesítés határozza meg a hívó identitását.
- Az engedélyezés határozza meg, hogy a hívó milyen műveleteket hajthat végre. Engedélyezés a Key Vaultban [a szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md) (RBAC) és az Azure Key Vault hozzáférési szabályzatok kombinációját használja.

### <a name="access-model-overview"></a>Access modell – áttekintés

A tárolókhoz való hozzáférés két interfészen vagy síkon keresztül történik. Ezek a síkok a felügyeleti sík és az adatsík.

- A *felügyeleti sík,* ahol kezelheti key vault magát, és ez a felület létrehozásához használt és törlése tárolók. A kulcstartó tulajdonságai is olvashatók, és kezelheti a hozzáférési szabályzatokat.
- Az *adatsík* lehetővé teszi a key vaultban tárolt adatokkal való munkát. Kulcsokat, titkos kulcsokat és tanúsítványokat adhat hozzá, törölhet és módosíthat.

A kulcstartó eléréséhez mindkét síkon, minden hívó (felhasználók vagy alkalmazások) kell hitelesíteni és engedélyezni. Mindkét sík az Azure Active Directory (Azure AD) hitelesítést használ. Engedélyezéshez a felügyeleti sík szerepköralapú hozzáférés-vezérlést (RBAC) használ, az adatsík pedig key vault hozzáférési szabályzatot.

A mindkét sík on-már egyetlen hitelesítési mechanizmusának számos előnye van:

- A szervezetek központilag szabályozhatják a hozzáférést a szervezet összes kulcstartójához.
- Ha egy felhasználó távozik, azonnal elveszíti a hozzáférést a szervezet összes kulcstartójához.
- A szervezetek testre szabhatják a hitelesítést az Azure AD beállításaival, például a többtényezős hitelesítés engedélyezéséhez a nagyobb biztonság érdekében

### <a name="managing-administrative-access-to-key-vault"></a>Felügyeleti hozzáférés kezelése a Key Vaulthoz

Amikor létrehoz egy kulcstartót egy erőforráscsoportban, az Azure AD használatával kezelheti a hozzáférést. A felhasználók vagy csoportok lehetővé teszik a kulcstartók kezelését egy erőforráscsoportban. A hozzáférést egy adott hatókör szintjén a megfelelő RBAC-szerepkörök hozzárendelésével adhat meg. Ahhoz, hogy hozzáférést adjon egy felhasználónak a kulcstartók kezeléséhez, egy előre definiált szerepkört `key vault Contributor` rendel a felhasználóhoz egy adott hatókörben. RBAC szerepkörhöz a következő hatókörszintek rendelhetők hozzá:

- **Előfizetés:** Az előfizetés szintjén hozzárendelt RBAC-szerepkör az adott előfizetésen belüli összes erőforráscsoportra és erőforrásra vonatkozik.
- **Erőforráscsoport**: Az erőforráscsoport szintjén hozzárendelt RBAC-szerepkör az adott erőforráscsoport összes erőforrására vonatkozik.
- **Adott erőforrás**: Egy adott erőforráshoz rendelt RBAC-szerepkör vonatkozik az adott erőforrásra. Ebben az esetben az erőforrás egy adott key vault.

Számos előre definiált szerepkör létezik. Ha egy előre definiált szerepkör nem felel meg az igényeinek, megadhatja a saját szerepkörét. További információ: [RBAC: Built-in roles](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Ha egy `Contributor` felhasználó rendelkezik engedélyekkel egy key vault felügyeleti síkhoz, a felhasználó hozzáférést biztosíthat magának az adatsíkhoz egy Key Vault hozzáférési házirend beállításával. Szorosan szabályozhatja, `Contributor` hogy kinek van szerepkör-hozzáférése a kulcstartókhoz. Győződjön meg arról, hogy csak arra jogosult személyek férhetnek hozzá és kezelhetik a kulcstartókat, kulcsokat, titkos kulcsokat és tanúsítványokat.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>A Key Vault-adatokhoz való hozzáférés szabályozása

A Key Vault hozzáférési szabályzatok külön-külön adnak engedélyeket a kulcsoknak, titkos kulcsoknak vagy tanúsítványoknak. A felhasználók csak a kulcsokhoz férhetnek hozzá, a titkos kulcsokhoz nem. A kulcsok, titkos kulcsok és tanúsítványok hozzáférési engedélyeit a tároló szintjén kezeli a rendszer.

> [!IMPORTANT]
> A Key Vault hozzáférési szabályzatai nem támogatják a részletes, objektumszintű engedélyeket, például egy adott kulcsot, titkos kulcsot vagy tanúsítványt. Ha egy felhasználó engedélyt kap kulcsok létrehozására és törlésére, ezeket a műveleteket a kulcstartó összes kulcsán végrehajthatja.

A kulcstartó hozzáférési szabályzatainak beállításához használja az [Azure Portalt,](https://portal.azure.com/)az [Azure CLI-t](/cli/azure/install-azure-cli?view=azure-cli-latest), az [Azure PowerShellt](/powershell/azureps-cmdlets-docs)vagy a [Key Vault Felügyeleti REST API-kat.](/rest/api/keyvault/)

Korlátozhatja az adatsík-hozzáférést az [Azure Key Vault virtuális hálózati szolgáltatásvégpontjainak](overview-vnet-service-endpoints.md)használatával). [Tűzfalak at és virtuális hálózati szabályokat](network-security.md) állíthat be egy további biztonsági réteghez.

## <a name="network-access"></a>Hálózati hozzáférés

Csökkentheti a tárolók expozícióját, ha megadja, hogy mely IP-címek férhetnek hozzá. Az Azure Key Vault virtuális hálózati szolgáltatásvégpontjai lehetővé teszik a hozzáférés korlátozását egy adott virtuális hálózathoz. A végpontok azt is lehetővé teszik, hogy korlátozza a hozzáférést az IPv4 (internet protokoll 4-es verzió) címtartományok listájához. A kulcstartóhoz ezeken a forrásokon kívülről csatlakozó felhasználók számára a rendszer megtagadja a hozzáférést.

A tűzfalszabályok érvénybe lépése után a felhasználók csak akkor olvashatják be az adatokat a Key Vaultból, ha a kéréseik engedélyezett virtuális hálózatokból vagy IPv4-címtartományokból származnak. Ez az Azure Portalról való key vault elérésére is vonatkozik. Bár a felhasználók az Azure Portalról is megkereshetik a kulcstartót, előfordulhat, hogy nem tudják felsorolni a kulcsokat, titkos kulcsokat vagy tanúsítványokat, ha az ügyfélgépük nem szerepel az engedélyezett listában. Ez hatással van a Key Vault-választó más Azure-szolgáltatások által. Előfordulhat, hogy a felhasználók láthatják a kulcstartók listáját, de a kulcsokat nem, ha a tűzfalszabályok megakadályozzák az ügyfélgépüket.

Az Azure Key Vault hálózati címével kapcsolatos további információkért tekintse át [az Azure Key Vault virtuális hálózati szolgáltatásának végpontjait)](overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Figyelés

A Key Vault naplózása információkat ment a tárolón végrehajtott tevékenységekről. Key Vault-naplók:

- Minden hitelesített REST API-kérelem, beleértve a sikertelen kérelmeket is
  - A kulcstartón végzett műveletek. Ezek a műveletek közé tartozik a létrehozás, törlés, hozzáférési házirendek beállítása és a key vault attribútumok, például a címkék frissítése.
  - A kulcstartóban lévő kulcsokés titkos kulcsok műveletei, többek között a következők:
    - Kulcsok vagy titkos kulcsok létrehozása, módosítása vagy törlése.
    - Kulcsok aláírása, ellenőrzése, titkosítása, visszafejtése, csomagolása és kicsomagolása, titkos kulcsok beolvasása, valamint kulcsok és titkos kulcsok (és azok verziói) listázása.
- A 401-es választ eredményező, nem hitelesített kérelmek. Példák olyan kérések, amelyek nem rendelkeznek tulajdonosi jogkivonatot, amelyek hibásan formázottak vagy lejárt, vagy amelyek érvénytelen jogkivonatot tartalmaznak.

A naplózási adatok a key vault-művelet után 10 percen belül érhetők el. A tárfiókban lévő naplók kezelése önön múlik.

- Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
- Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.

A tárfiókok biztonságos kezelésével kapcsolatos javaslatért tekintse át az [Azure Storage biztonsági útmutatóját](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Következő lépések

- [Virtuális hálózati szolgáltatás végpontjai az Azure Key Vaulthoz)](overview-vnet-service-endpoints.md)
- [RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md)
- [virtuális hálózati szolgáltatás végpontjai az Azure Key Vault hoz)](overview-vnet-service-endpoints.md)
