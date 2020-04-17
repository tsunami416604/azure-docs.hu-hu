---
title: Felügyelt identitások az Azure-hoz
description: Ismerje meg a felügyelt identitások azure-beli Service Fabric használatával.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: a26f188ed2f5e18bdf775cd1fb21001495ffdc89
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461446"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Felügyelt identitások használata az Azure-hoz a Service Fabric

A felhőalapú alkalmazások létrehozásakor gyakori kihívás, hogy hogyan kezelheti biztonságosan a kódban lévő hitelesítő adatokat a különböző szolgáltatások hitelesítéséhez anélkül, hogy helyben mentené őket egy fejlesztői munkaállomáson vagy a forrásvezérlőben. *Felügyelt identitások az Azure-hoz* megoldja ezt a problémát az Azure Active Directoryban (Azure AD) lévő összes erőforráshoz azáltal, hogy automatikusan felügyelt identitásokat biztosít számukra az Azure AD-n belül. A szolgáltatás identitását használhatja hitelesítést minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault, anélkül, hogy a kódban tárolt hitelesítő adatok.

*Az Azure-erőforrások felügyelt identitásai* ingyenesek az Azure-előfizetésekhez való Azure-előfizetéssel. Nincs további költség.

> [!NOTE]
> *Felügyelt identitások* az Azure-ban az új neve a szolgáltatás korábbi nevén felügyelt szolgáltatás identitása (MSI).

## <a name="concepts"></a>Alapelvek

Az Azure felügyelt identitásai számos kulcsfontosságú koncepción alapulnak:

- **Ügyfélazonosító** – az Azure AD által létrehozott egyedi azonosító, amely a kezdeti kiépítés során egy alkalmazáshoz és egyszerű szolgáltatáshoz van kötve (lásd az [alkalmazásazonosítót](/azure/active-directory/develop/developer-glossary#application-id-client-id)is.)

- **Egyszerű azonosító** – a felügyelt identitás egyszerű szolgáltatásobjektumának objektumazonosítója, amely szerepköralapú hozzáférést biztosít egy Azure-erőforráshoz.

- **Egyszerű szolgáltatás** – egy Azure Active Directory-objektum, amely egy AAD-alkalmazás egy adott bérlőben történő kivetítését jelöli (lásd: [egyszerű szolgáltatás.)](../active-directory/develop/developer-glossary.md#service-principal-object)

A felügyelt identitásoknak két típusa létezik:

- A **rendszer által hozzárendelt felügyelt identitás** közvetlenül egy Azure-szolgáltatáspéldányon engedélyezett.  A rendszer által hozzárendelt identitás életciklusa egyedi az Azure-szolgáltatáspéldány, amely engedélyezve van.
- A **felhasználó által hozzárendelt felügyelt identitás** különálló Azure-erőforrásként jön létre. Az identitás hozzárendelhető egy vagy több Azure-szolgáltatáspéldányhoz, és a példányok életciklusától elkülönítve kezelhető.

A felügyelt identitástípusok közötti különbség további megértéséhez olvassa el a [Hogyan működnek az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>A Service Fabric-alkalmazások támogatott forgatókönyvei

A Service Fabric felügyelt identitásai csak az Azure által üzembe helyezett Service Fabric-fürtökben támogatottak, és csak az Azure-erőforrásokként üzembe helyezett alkalmazások esetében; egy alkalmazás, amely nincs telepítve, mint egy Azure-erőforrás nem rendelhető identitáshoz. Fogalmilag az Azure Service Fabric-fürt felügyelt identitásainak támogatása két fázisból áll:

1. Rendeljen egy vagy több felügyelt identitást az alkalmazás-erőforráshoz; egy alkalmazáshoz egyetlen rendszerhez rendelt identitás, illetve legfeljebb 32 felhasználó által hozzárendelt identitás rendelhető.

2. Az alkalmazás definíciójában az alkalmazáshoz rendelt identitások egyikét rendelje le az alkalmazással kapcsolatos bármely egyedi szolgáltatáshoz.

Az alkalmazás rendszeráltal hozzárendelt identitása az adott alkalmazásra egyedi; a felhasználó által hozzárendelt identitás önálló erőforrás, amely több alkalmazáshoz is hozzárendelhető. Az alkalmazáson belül egyetlen identitás (akár rendszerhez rendelt, akár felhasználóhoz rendelt) rendelhető az alkalmazás több szolgáltatásához, de minden egyes szolgáltatáshoz csak egy identitás rendelhető hozzá. Végül egy szolgáltatásnak explicit módon identitást kell rendelnie ahhoz, hogy hozzáférjen ehhez a funkcióhoz. Valójában az alkalmazás identitásainak az összetevő szolgáltatásaihoz való hozzárendelése lehetővé teszi az alkalmazáson belüli elkülönítést – a szolgáltatás csak a hozzá leképezett identitást használhatja.  

Jelenleg a következő forgatókönyvek támogatottak ehhez a funkcióhoz:

- Új alkalmazás telepítése egy vagy több szolgáltatással és egy vagy több hozzárendelt identitással

- Rendeljen hozzá egy vagy több felügyelt identitást egy meglévő (Azure által telepített) alkalmazáshoz az Azure-erőforrások eléréséhez

A következő forgatókönyvek nem támogatottak vagy nem ajánlottak; vegye figyelembe, hogy ezek a műveletek nem blokkolva vannak, de az alkalmazások kimaradásához vezethetnek:

- Távolítsa el vagy módosítsa az alkalmazáshoz rendelt identitásokat; ha módosításokat kell végrehajtania, küldjön külön központi telepítéseket, hogy először új identitás-hozzárendelést adjon hozzá, majd távolítsa el a korábban hozzárendeltet. Egy identitás eltávolítása egy meglévő alkalmazás nemkívánatos hatások, beleértve az alkalmazás nem frissíthető állapotban hagyása. A kérelem teljes törlése biztonságos, ha a személyazonosság eltávolítása szükséges; vegye figyelembe, hogy ez törli az alkalmazáshoz társított rendszeráltal hozzárendelt identitást (ha úgy van definiálva), és eltávolítja az alkalmazáshoz rendelt felhasználóhoz rendelt identitásokkal való társításokat.

- A felügyelt identitások Service Fabric-támogatása jelenleg nincs integrálva az [AzureServiceTokenProvider szolgáltatásba.](../key-vault/general/service-to-service-authentication.md)

## <a name="next-steps"></a>További lépések

- [Új Azure Service Fabric-fürt üzembe helyezése felügyelt identitástámogatással](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Felügyelt identitás támogatásengedélyezése meglévő Azure Service Fabric-fürtben](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [A Service Fabric-alkalmazások felügyelt identitásának kihasználása a szolgáltatáskódból](./how-to-managed-identity-service-fabric-app-code.md)
- [Azure Service Fabric-alkalmazás elérésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)
- [Alkalmazástitok deklarálása és használata KeyVaultReferences-ként](./service-fabric-keyvault-references.md)
