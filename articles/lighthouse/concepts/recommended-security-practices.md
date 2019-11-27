---
title: Ajánlott biztonsági eljárások
description: Az Azure-beli delegált erőforrás-kezelés használatakor fontos figyelembe venni a biztonsági és hozzáférés-vezérlést.
ms.date: 07/11/2019
ms.topic: conceptual
ms.openlocfilehash: 18decc337722c1dc64fac94679d783dd55915ee6
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463901"
---
# <a name="recommended-security-practices"></a>Ajánlott biztonsági eljárások

Az Azure-beli delegált erőforrás-kezelés használatakor fontos figyelembe venni a biztonsági és hozzáférés-vezérlést. A bérlő felhasználóinak közvetlen hozzáférésük lesz az ügyfél-előfizetésekhez és az erőforráscsoportokhoz, ezért a bérlő biztonságának fenntartásához lépéseket kell végrehajtania. Azt is érdemes biztosítani, hogy csak az ügyfelek erőforrásainak hatékony kezeléséhez szükséges hozzáférést engedélyezze. Ez a témakör ajánlásokat nyújt a segítségére.

## <a name="require-azure-multi-factor-authentication"></a>Azure-Multi-Factor Authentication megkövetelése

Az [Azure multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (más néven kétlépéses ellenőrzés) segít megakadályozni, hogy a támadók több hitelesítési lépés megkövetelésével hozzáférjenek a fiókokhoz. A szolgáltatói bérlő összes felhasználója számára szükséges Multi-Factor Authentication, beleértve az ügyfelek erőforrásaihoz hozzáférő felhasználókat is.

Javasoljuk, hogy kérje ügyfeleit, hogy az Azure-Multi-Factor Authentication is implementálják a bérlők számára.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Engedélyek társítása csoportokhoz a legalacsonyabb jogosultsági szint elve alapján

A felügyelet egyszerűbbé tételéhez ajánlott az Azure AD felhasználói csoportok használata az ügyfelek erőforrásainak kezeléséhez szükséges minden egyes szerepkörhöz. Ez lehetővé teszi, hogy az egyes felhasználókat szükség szerint vegye fel vagy távolítsa el a csoportba ahelyett, hogy közvetlenül az adott felhasználóhoz rendeljen engedélyeket.

Az engedélyezési struktúra létrehozásakor ügyeljen arra, hogy kövesse a legalacsonyabb jogosultsági szint elvét, hogy a felhasználók csak a feladat elvégzéséhez szükséges engedélyekkel rendelkezzenek, ami segít csökkenteni a véletlen hibák esélyét.

Előfordulhat például, hogy a következőhöz hasonló struktúrát szeretne használni:

|Csoport neve  |Típus  |principalId  |Szerepkör-definíció  |Szerepkör-definíció azonosítója  |
|---------|---------|---------|---------|---------|
|Építészek     |Felhasználói csoport         |\<principalId\>         |Közreműködő         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Értékelés     |Felhasználói csoport         |\<principalId\>         |Olvasó         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM-szakemberek     |Felhasználói csoport         |\<principalId\>         |VIRTUÁLIS gépek közreműködője         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automatizálás     |Egyszerű szolgáltatásnév (SPN)         |\<principalId\>         |Közreműködő         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Miután létrehozta ezeket a csoportokat, igény szerint rendelhet hozzá felhasználókat. Csak azokat a felhasználókat adja hozzá, akiknek valóban szükségük van a hozzáférésre. Mindenképpen tekintse át a csoporttagság rendszeres áttekintését, és távolítsa el azokat a felhasználókat, akik már nem megfelelőek vagy szükségesek a belefoglalásához.

Ne feledje, hogy ha az [ügyfeleket nyilvános felügyelt szolgáltatáson keresztül](../how-to/publish-managed-services-offers.md)kívánja bevezetni, az Ön által kínált összes csoport (vagy felhasználó vagy szolgáltatásnév) ugyanazokkal az engedélyekkel fog rendelkezni minden olyan ügyfélnél, aki megvásárolja a csomagot. Ha különböző csoportokat szeretne hozzárendelni az egyes ügyfelekkel való munkavégzéshez, közzé kell tennie egy külön privát csomagot, amely kizárólag az egyes ügyfelekre vonatkozik, vagy egyénileg, Azure Resource Manager-sablonok használatával készíti elő az ügyfeleket. Közzétehet például egy nagyon korlátozott hozzáféréssel rendelkező nyilvános csomagot, majd a felhasználóval közvetlenül is felhasználhatja az erőforrásait a további hozzáféréshez egy testreszabott Azure-erőforrás-sablon használatával, igény szerint további hozzáférést biztosítva.


## <a name="next-steps"></a>Következő lépések

- Az [Azure multi-Factor Authentication üzembe helyezése](../../active-directory/authentication/howto-mfa-getstarted.md).
- További információ a [bérlők közötti felügyeleti élményekről](cross-tenant-management-experience.md).
