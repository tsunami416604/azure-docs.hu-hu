---
title: Tudnivalók az Azure digitális Twins szerepköralapú hozzáférés-vezérlés |} A Microsoft Docs
description: Ismerje meg a hitelesítés digitális Twins a szerepköralapú hozzáférés-vezérléssel.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 7a6d8565a0f85b4cb81d9f5f23b04fe6b2edc53e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324131"
---
# <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure digitális Twins lehetővé teszi, hogy pontos hozzáférés-vezérlést az adott adatok, erőforrások és a térbeli grafikon a műveletek. Részletes szerepkör és az engedélyek kezelését nevű ilyeneket _szerepköralapú hozzáférés-vezérlés_. Szerepköralapú hozzáférés-vezérlés áll _szerepkörök_, vagy a jogosultsági szinteket, és _szerepkör-hozzárendelések_, vagy egy szerepkör egy felhasználó vagy eszköz számára a társítás.

Szerepköralapú hozzáférés-vezérlés használata esetén is engedélyt kapni a felhasználó, eszköz, egy egyszerű szolgáltatást, a felhasználó által definiált függvény egy tartományhoz, vagy egy bérlői tartozó összes felhasználó. Ezenkívül hozzáférést mértékét is finomhangolható.

Szerepköralapú hozzáférés-vezérlés sajátossága, hogy a térbeli graph le az engedélyek öröklődnek.

## <a name="what-can-i-do-with-role-based-access-control"></a>Mire használhatom a szerepköralapú hozzáférés-vezérléssel?

A fejlesztő szerepköralapú hozzáférés-vezérlést használhatja:

* Adja meg a felhasználó képes kezelni az eszközöket egy teljes épület, vagy csak egy adott szoba vagy emelet.
* Egy rendszergazdai globális hozzáférés az összes térbeli gráf csomópontjai egy teljes grafikon, vagy csak a gráf szakasz megadása.
* Adjon meg egy támogatási szakértő olvasási hozzáférést a grafikon, kivéve a tárelérési kulcsok.
* Adja meg egy tartományi olvasási hozzáférés a gráf összes objektum minden tagja.

## <a name="role-based-access-control-best-practices"></a>Szerepköralapú hozzáférés-vezérlés ajánlott eljárások

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Szerepkörök

### <a name="role-definitions"></a>Szerepkör-definíciók

A **szerepkör-definíció** gyűjteménye, engedélyek, és néha egy **szerepkör**. A szerepkör-definíció tartalmazza az engedélyezett műveleteket, köztük a *létrehozása*, *olvasási*, *frissítése*, és *törlése*. Azt is, hogy mely objektumtípusok ezeket az engedélyeket a alkalmazni.

A következő szerepkörök az Azure digitális Twins érhetők el:

* **Hely rendszergazdája**: létrehozása, olvasása, frissítése és a megadott hely és az alatta lévő összes csomópont engedély törlése. Globális engedélyt.
* **Felhasználói rendszergazda**: létrehozása, olvasása, frissítése és a felhasználók és felhasználói biztonsággal kapcsolatos objektumok engedély törlésére. Olvasási engedéllyel a szóközöket.
* **Eszköz-rendszergazdai**: létrehozása, olvasása, frissítése és az eszközök és az eszközzel kapcsolatos objektumok engedély törlése. Olvasási engedéllyel a szóközöket.
* **Kulcs rendszergazda**: létrehozása, olvasása, frissítése és törlése a tárelérési kulcsok engedély. Olvasási engedéllyel a szóközöket.
* **Jogkivonat rendszergazdák**: tárelérési kulcsok olvasási és frissítési engedély. Olvasási engedéllyel a szóközöket.
* **Felhasználói**: olvasási engedélyt tárolóhelyek eszközök, érzékelők és felhasználók számára, beleértve a kapcsolódó kapcsolódó objektumok.
* **Támogatási szakértő**: olvasási engedéllyel a tárelérési kulcsok kivételével mindent.
* **Eszköz telepítő**: eszközeit és érzékelőit, beleértve a kapcsolódó olvasási és frissítési engedély kapcsolódó objektumok. Olvasási engedéllyel a szóközöket.
* **Átjáróeszköz**: létrehozása engedély érzékelő számára. Olvasási engedély eszközeit és érzékelőit, beleértve a kapcsolódó kapcsolódó objektumok.

>[!NOTE]
> *A fenti teljes definíciókat lekérhetők a/szerepkörök API lekérdezésével.*

### <a name="object-types"></a>Objektumtípus

A `ObjectIdType` identity szerepkör adott típusára utal. Kivéve a `DeviceId` és `UserDefinedFunctionId` típusai, egy Azure Active Directory (Azure AD) objektum olyan osztályát megfelelnek a típusok:
  
* A `UserId` típus egy szerepkört rendel egy felhasználói.
* A `DeviceId` típus egy szerepkört rendel egy eszközt.
* A `DomainName` típus egy szerepkört rendel egy tartomány nevét. A megadott tartománynév rendelkező felhasználók számára a hozzáférési jogosultságokat a megfelelő szerepkör lesz.
* A `TenantId` típus egy szerepkört rendel egy bérlőt. Minden egyes felhasználó nyithatja meg a megadott Azure AD-bérlő azonosítója a hozzáférési jogosultságokat a megfelelő szerepkör lesz.
* A `ServicePrincipalId` típus egy szerepkört rendel egy szolgáltatásnév-objektum-azonosítót.
* A `UserDefinedFunctionId` típus hozzárendeli egy szerepkört, a felhasználó által megadott függvény (UDF).

> [!div class="nextstepaction"]
> [Lekérdezés vagy egy felhasználó Objektumazonosítója](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Egyszerű szolgáltatás Objektumazonosítójának beszerzése](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Az Azure AD-bérlő Objektumazonosítójának lekéréséhez](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Szerepkör-hozzárendelések

Engedélyek szerepkör-hozzárendelés létrehozása a címzetthez kapott, és visszavonta a szerepkör-hozzárendelés eltávolítása. Az Azure digitális Twins szerepkör-hozzárendelés hozzárendeli egy objektum (felhasználó, az Azure AD-bérlővel, stb.), a szerepkör és a egy szóközt. Valamennyi olyan objektumhoz tartozó, hogy a hely, beleértve a teljes térbeli graph alatta majd megadott engedélyekkel.

Például egy felhasználó van megadva a szerepkör egy szerepkör-hozzárendelés `DeviceInstaller` a legfelső szintű csomópontja által térbeli grafikon, amely egy épület jelöli. A felhasználó ezután nem olvashatja, és frissítheti eszközeit nemcsak a csomóponton, de minden más gyermek szóközt az épületben.

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins biztonságával kapcsolatban, olvassa el [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md).
