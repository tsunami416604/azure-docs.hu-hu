---
title: Az Azure digitális Twins szerepköralapú hozzáférés-vezérlés megismerése |} A Microsoft Docs
description: Ismerje meg a hitelesítés digitális Twins a szerepköralapú hozzáférés-vezérléssel.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014788"
---
# <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure digitális Twins lehetővé teszi, hogy pontos hozzáférés-vezérlést az adott adatok, erőforrások és a térbeli grafikon a műveletek. Így részletes szerepkör és a szerepköralapú hozzáférés-vezérlés (RBAC) nevű engedélykezelés keresztül hajtja végre. Az RBAC áll _szerepkörök_ és _szerepkör-hozzárendelések_. Szerepkörök a jogosultsági szinteket, azonosíthatja. Szerepkör-hozzárendelések szerepkör társítása egy felhasználóhoz vagy eszközhöz.

RBAC használata esetén is engedélyt kapni:

- Egy felhasználó.
- Egy eszköz.
- Egy egyszerű szolgáltatást.
- Felhasználó által definiált függvény. 
- A tartományhoz tartozó összes felhasználó. 
- Egy bérlő.
 
A hozzáférés mértékét is finomhangolni lehet.

Az RBAC sajátossága, hogy a térbeli graph le az engedélyek öröklődnek.

## <a name="what-can-i-do-with-rbac"></a>Mire használhatom az RBAC-t?

A fejlesztő rbac-t használhatja:

* Adja meg a felhasználó képes kezelni az eszközöket egy teljes épület, vagy csak egy adott szoba vagy emelet.
* Egy rendszergazdai globális hozzáférés az összes térbeli gráf csomópontjai egy teljes grafikon, vagy csak a gráf szakasz megadása.
* Adjon meg egy támogatási szakértő olvasási hozzáférést a grafikon, kivéve a tárelérési kulcsok.
* Adja meg egy tartományi olvasási hozzáférés a gráf összes objektum minden tagja.

## <a name="rbac-best-practices"></a>Az RBAC-ajánlott eljárások

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Szerepkörök

### <a name="role-definitions"></a>Szerepkör-definíciók

Egy szerepkör-definíció gyűjteménye, engedélyek, és néha egy szerepkört. A szerepkör definíciójának listák létrehozása az engedélyezett műveleteket, többek között, olvasási, frissítési és törlési. Azt is, hogy mely objektumtípusok ezeket az engedélyeket a alkalmazni.

A következő szerepkörök az Azure digitális Twins érhetők el:

* **Hely rendszergazdája**: létrehozása, olvasása, frissítése és a megadott hely és az alatta lévő összes csomópont engedély törlése. Globális engedélyt.
* **Felhasználói rendszergazda**: létrehozása, olvasása, frissítése és a felhasználók és felhasználói biztonsággal kapcsolatos objektumok engedély törlésére. Olvasási engedéllyel a szóközöket.
* **Eszköz-rendszergazdai**: létrehozása, olvasása, frissítése és az eszközök és az eszközzel kapcsolatos objektumok engedély törlése. Olvasási engedéllyel a szóközöket.
* **Kulcs rendszergazda**: létrehozása, olvasása, frissítése és törlése a tárelérési kulcsok engedély. Olvasási engedéllyel a szóközöket.
* **Jogkivonat rendszergazdák**: tárelérési kulcsok olvasási és frissítési engedély. Olvasási engedéllyel a szóközöket.
* **Felhasználói**: olvasási engedélyt tárolóhelyek eszközök, érzékelők és felhasználók számára, amely tartalmazza a kapcsolódó kapcsolódó objektumok.
* **Támogatási szakértő**: olvasási engedéllyel a tárelérési kulcsok kivételével mindent.
* **Eszköz telepítő**: olvasási és frissítési engedély eszközeit és érzékelőit, amely tartalmazza a kapcsolódó kapcsolódó objektumok. Olvasási engedéllyel a szóközöket.
* **Átjáróeszköz**: létrehozása engedély érzékelő számára. Olvasási engedély eszközeit és érzékelőit, amely tartalmazza a kapcsolódó kapcsolódó objektumok.

>[!NOTE]
> A teljes definíciók az előző szerepkörök lekéréséhez a/szerepkörök API lekérdezése.

### <a name="object-types"></a>Objektumtípus

A `ObjectIdType` identitás, amely az adott szerepkör típusára utal. Kivéve a `DeviceId` és `UserDefinedFunctionId` típusai, egy Azure Active Directory (Azure AD) objektum olyan osztályát megfelelnek a típusok:
  
* A `UserId` típus egy szerepkört rendel egy felhasználói.
* A `DeviceId` típus egy szerepkört rendel egy eszközt.
* A `DomainName` típus egy szerepkört rendel egy tartomány nevét. Minden felhasználónak, a megadott tartomány nevét a hozzáférési jogosultságokat a megfelelő szerepkör van.
* A `TenantId` típus egy szerepkört rendel egy bérlőt. Minden felhasználónak, aki tagja a megadott Azure AD-bérlő azonosítója a hozzáférési jogosultságokat a megfelelő szerepkör van.
* A `ServicePrincipalId` típus egy szerepkört rendel egy szolgáltatásnév-objektum-azonosítót.
* A `UserDefinedFunctionId` típus egy szerepkört rendel egy felhasználói függvény (UDF).

> [!div class="nextstepaction"]
> [Lekérdezés vagy egy felhasználó Objektumazonosítója](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Egyszerű szolgáltatás Objektumazonosítójának beszerzése](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Az Azure AD-bérlő Objektumazonosítójának lekéréséhez](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Szerepkör-hozzárendelések

Egy címzettnek engedélyeket, szerepkör-hozzárendelés létrehozása. Visszavonni az engedélyeket, a szerepkör-hozzárendelés eltávolítása. Egy digitális Twins Azure szerepkör-hozzárendelés egy objektumot, például egy felhasználó vagy az Azure AD-bérlővel, hozzárendeli egy szerepkör és a egy szóközt. Engedélyek valamennyi olyan objektumhoz tartozó, hogy a hely. A terület a teljes térbeli graph alatta tartalmazza.

Például egy felhasználó van megadva a szerepkör egy szerepkör-hozzárendelés `DeviceInstaller` a legfelső szintű csomópontja által térbeli grafikon, amely egy épület jelöli. A felhasználó ezután olvasása és frissítése az adott csomópont és a az épület más gyermek szóközöket.

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins biztonságával kapcsolatban, olvassa el [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md).
