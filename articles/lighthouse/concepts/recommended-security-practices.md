---
title: Ajánlott biztonsági eljárások az Azure világítótoronyig
description: Ha az erőforrás-kezelés az Azure-meghatalmazott, fontos fontos a biztonság és hozzáférés-vezérlés.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 843b965e6ea74a7c11dc11459ff5d30ddbe5c987
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809868"
---
# <a name="recommended-security-practices"></a>Ajánlott biztonsági eljárások

Ha az erőforrás-kezelés az Azure-meghatalmazott, fontos fontos a biztonság és hozzáférés-vezérlés. A bérlő felhasználói ügyfél-előfizetések és -erőforráscsoportok, a közvetlen hozzáférést kap, hogy intézkedjen, hogy a bérlője biztonságának fenntartása érdekében érdemes. Érdemes csak engedélyezze a hozzáférést, amely hatékonyan kezelheti az ügyfelek erőforrások szükséges is. Ez a témakör ehhez segítséget nyújt.

## <a name="require-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication megkövetelése

[Az Azure multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (más néven a kétlépéses ellenőrzés) segítségével megakadályozhatja, hogy a támadók azzal, hogy több hitelesítési lépések egy fiók hozzáférését. A bérlőben service provider, beleértve az ügyfél-erőforrásokhoz való hozzáféréssel rendelkező felhasználók a multi-factor Authentication szolgáltatás követelje meg az összes felhasználó számára.

Javasoljuk, hogy kérje meg az Azure multi-factor Authentication megvalósítása a bérlőik számára, valamint az ügyfelek.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Engedélyek hozzárendelése a csoportokhoz, használja a legalacsonyabb jogosultsági szint elve

Felügyeleti megkönnyítése érdekében javasoljuk, az Azure AD felhasználói csoportokat használ az egyes szerepkörökhöz, az ügyfelek-erőforrások kezeléséhez szükséges. Ez lehetővé teszi a csoport szükség szerint helyett közvetlenül az adott felhasználói engedélyek hozzárendelése az egyes felhasználók hozzáadásához és eltávolításához.

Az engedély struktúra létrehozásakor mindenképpen hajtsa végre a legalacsonyabb jogosultsági szint elve az, hogy a felhasználók csak a feladat végrehajtásához szükséges engedélyekkel való nem szándékos hibák esélyének csökkentése érdekében.

Például előfordulhat, hogy használni kívánt ilyen struktúra:

|Csoport neve  |Type  |principalId  |Szerepkör-definíció  |Szerepkör-definíció Azonosítóját  |
|---------|---------|---------|---------|---------|
|Tervezők     |Felhasználói csoport         |\<principalId\>         |Közreműködő         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Értékelés     |Felhasználói csoport         |\<principalId\>         |Olvasó         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Virtuális gép szakértőitől     |Felhasználói csoport         |\<principalId\>         |Virtuális gép közreműködő         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Egyszerű szolgáltatásnév (SPN)         |\<principalId\>         |Közreműködő         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Ezek a csoportok létrehozása után a felhasználók igény szerint rendelhet. Csak adja hozzá a felhasználókat, akiknek valóban hozzáféréssel kell rendelkeznie. Mindenképpen tekintse át a csoporttagság rendszeresen, és távolítsa el azokat a felhasználókat, amelyek már nem megfelelő vagy nem feltétlenül szükséges belefoglalni.

Ne feledje, hogy amikor, [keresztül egy felügyelt ajánlat nyilvános ügyfelek](../how-to/publish-managed-services-offers.md), minden olyan csoport (vagy a felhasználónév vagy szolgáltatásnév), akkor minden ügyfél számára a csomag vásárlása ugyanazon engedélyekkel fog rendelkezni. Minden egyes ügyfél együttműködni különböző csoportokhoz rendeléséhez kell közzétenni külön privát csomagot minden ügyfél vagy ügyfelek kizárólagos külön-külön az Azure Resource Manager-sablonok használatával. Például tudott közzétenni egy nyilvános terv erősen korlátozott hozzáféréssel, és ezt követően az ügyfél közvetlenül együttműködve előkészíti az erőforrásaik testre szabott Azure erőforrás-sablon használatával további hozzáférés biztosítása igény szerint további hozzáférés.


## <a name="next-steps"></a>További lépések

- [Üzembe helyezése az Azure multi-factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Ismerje meg [több-bérlős felhasználói élmény](cross-tenant-management-experience.md).
