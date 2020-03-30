---
title: Ajánlott biztonsági eljárások
description: Az Azure delegált erőforrás-kezelése során fontos figyelembe venni a biztonság és a hozzáférés-vezérlés.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246908"
---
# <a name="recommended-security-practices"></a>Ajánlott biztonsági eljárások

Az [Azure delegált erőforrás-kezelése](azure-delegated-resource-management.md)során fontos figyelembe venni a biztonság és a hozzáférés-vezérlés. A bérlő felhasználói közvetlen hozzáféréssel rendelkeznek az ügyfél-előfizetésekhez és erőforráscsoportokhoz, ezért lépéseket kell tennie a bérlő biztonságának fenntartása érdekében. Győződjön meg arról is, hogy csak az ügyfelek erőforrásainak hatékony kezeléséhez szükséges hozzáférést engedélyezi. Ez a témakör javaslatokat tartalmaz az erre vonatkozó anameddig.

## <a name="require-azure-multi-factor-authentication"></a>Azure többtényezős hitelesítésének megkövetelése

[Az Azure többtényezős hitelesítés](../../active-directory/authentication/concept-mfa-howitworks.md) (más néven kétlépéses ellenőrzés) segít megakadályozni, hogy a támadók hozzáférjenek egy fiókhoz több hitelesítési lépés megkövetelésével. A szolgáltató bérlőjének minden felhasználójához többtényezős hitelesítést kell igényelnie, beleértve azügyfeleket is, akik hozzáférnek az ügyfélerőforrásokhoz.

Azt javasoljuk, hogy kérje meg ügyfeleit, hogy valósítsák meg az Azure többtényezős hitelesítést a bérlőkis.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Engedélyek hozzárendelése csoportokhoz a minimális jogosultság elve alapján

A felügyelet megkönnyítése érdekében azt javasoljuk, hogy az Azure AD felhasználói csoportok használata minden szerepkör kezeléséhez szükséges az ügyfelek erőforrásait. Ez lehetővé teszi, hogy szükség szerint egyéni felhasználókat vegyen fel vagy távolítson el a csoporthoz, ahelyett, hogy közvetlenül az adott felhasználóhoz rendelné az engedélyeket.

> [!IMPORTANT]
> Az Azure AD-csoport engedélyeinek hozzáadásához a **csoporttípusnak** **biztonságnak,** nem pedig **Office 365-nek**kell lennie. Ez a beállítás a csoport létrehozásakor van bejelölve. További információ: [Hozzon létre egy alapszintű csoportot, és adjon hozzá tagokat az Azure Active Directory használatával.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

Az engedélystruktúra létrehozásakor ügyeljen arra, hogy kövesse a minimális jogosultság elvét, hogy a felhasználók csak a feladat uk elvégzéséhez szükséges engedélyekkel rendelkezhessenek, így csökkentve a véletlen hibák esélyét.

Előfordulhat például, hogy egy ilyen struktúrát szeretne használni:

|Csoport neve  |Típus  |principalId között  |Szerepkör-definíció  |Szerepkör-definíció azonosítója  |
|---------|---------|---------|---------|---------|
|Építészek     |Felhasználói csoport         |\<principalId között\>         |Közreműködő         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Értékelés     |Felhasználói csoport         |\<principalId között\>         |Olvasó         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Virtuális gép specialistái     |Felhasználói csoport         |\<principalId között\>         |Virtuális gép közreműködője         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Egyszerű szolgáltatásnév (SPN)         |\<principalId között\>         |Közreműködő         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Miután létrehozta ezeket a csoportokat, szükség szerint hozzárendelhet felhasználókat. Csak azokkal a felhasználókkal adjon hozzá, akiknek valóban szükségük van a hozzáférésre. Ügyeljen arra, hogy rendszeresen ellenőrizze a csoporttagságot, és távolítsa el azon felhasználókat, akik et már nem megfelelőek vagy szükségesek a felvételhez.

Ne feledje, hogy ha [nyilvános szolgáltatásajánlaton keresztül vonja be az ügyfeleket,](../how-to/publish-managed-services-offers.md)a benne lévő bármely csoport (vagy felhasználó vagy egyszerű szolgáltatás) minden olyan ügyfél számára azonos engedélyekkel fog rendelkezni, aki megvásárolja a csomagot. Ha különböző csoportokat szeretne hozzárendelni az egyes ügyfelekkel való munkához, külön privát csomagot kell közzétennie, amely kizárólag az egyes ügyfelek számára, vagy a fedélzeti ügyfelek egyénileg az Azure Resource Manager-sablonok használatával. Közzétehet például egy nyilvános csomagot, amely nagyon korlátozott hozzáféréssel rendelkezik, majd az ügyféllel együttműködve közvetlenül az erőforrások fedélzeti beszervezéséhez további hozzáférést biztosít egy testre szabott Azure-erőforrássablon használatával, amely szükség szerint további hozzáférést biztosít.


## <a name="next-steps"></a>További lépések

- [Telepítse az Azure többtényezős hitelesítést.](../../active-directory/authentication/howto-mfa-getstarted.md)
- További információ a [bérlők közötti felügyeleti élményekről.](cross-tenant-management-experience.md)
