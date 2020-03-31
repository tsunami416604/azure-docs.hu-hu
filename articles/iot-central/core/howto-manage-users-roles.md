---
title: Felhasználók és szerepkörök kezelése az Azure IoT Central alkalmazásban | Microsoft dokumentumok
description: Rendszergazdaként a felhasználók és szerepkörök kezelése az Azure IoT Central alkalmazásban
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c00f9d8baa55ef0d0cf6322ee71f22e739e6acdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365502"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Felhasználók és szerepkörök kezelése az IoT Central alkalmazásban

Ez a cikk ismerteti, hogyan, rendszergazdaként, hogyan adhat hozzá, szerkeszthet és törölhet felhasználókat az Azure IoT Central alkalmazásban. A cikk azt is ismerteti, hogyan kezelheti a szerepköröket az Azure IoT Central alkalmazásban.

A **felügyeleti** szakasz eléréséhez és használatához egy Azure IoT Central alkalmazás **rendszergazdai** szerepkörben kell lennie. Ha létrehoz egy Azure IoT Central-alkalmazást, automatikusan hozzáadódik az adott alkalmazás **rendszergazdai** szerepköréhez.

## <a name="add-users"></a>Felhasználók hozzáadása

Minden felhasználónak rendelkeznie kell egy felhasználói fiókkal, mielőtt bejelentkezhetne, és hozzáférhetne egy Azure IoT Central alkalmazáshoz. A Microsoft-fiókok és az Azure Active Directory-fiókok az Azure IoT Central ban támogatottak. Az Azure Active Directory-csoportok jelenleg nem támogatottak az Azure IoT Central ban.

További információt a [Microsoft-fiók súgója](https://support.microsoft.com/products/microsoft-account?category=manage-account) és [rövid útmutatója: Új felhasználók hozzáadása az Azure Active Directoryhoz című témakörben talál.](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)

1. Ha hozzá szeretne adni egy felhasználót egy IoT Central-alkalmazáshoz, nyissa meg a **Felügyelet** szakasz **Felhasználók** lapját.
    
    > [!div class="mx-imgBorder"]
    >![Felhasználók kezelése](media/howto-manage-users-roles/manage-users-pnp.png)

1. Felhasználó hozzáadásához a **Felhasználók** lapon válassza a **+ Felhasználó hozzáadása**lehetőséget.

1. Válasszon szerepkört a felhasználószámára a **Szerepkör** legördülő menüből. További információ a szerepkörökről a cikk [Szerepkörök kezelése](#manage-roles) szakaszában.

    > [!div class="mx-imgBorder"]
    >![Felhasználó hozzáadása és szerepkör kiválasztása](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Az a felhasználó, aki olyan egyéni szerepkörben van, amely engedélyt ad más felhasználók hozzáadására, csak a saját szerepkörénél azonos vagy kevesebb jogosultsággal rendelkező felhasználókat vehet fel.

Ha egy IoT-központi felhasználói azonosító törlődik az Azure Active Directoryból, majd újra hozzáadódik, a felhasználó nem fog tudni bejelentkezni az IoT Central alkalmazásba. A hozzáférés újbóli engedélyezéséhez az IoT Központi rendszergazdájának törölnie és olvasnia kell a felhasználót az alkalmazásban.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>A felhasználókhoz rendelt szerepkörök szerkesztése

A szerepkörök nem módosíthatók a hozzárendelésük után. A felhasználóhoz rendelt szerepkör módosításához törölje a felhasználót, majd adja hozzá újra a felhasználót egy másik szerepkörrel.

> [!NOTE]
> A hozzárendelt szerepkörök az IoT Central alkalmazásra jellemzőek, és nem kezelhetők az Azure Portalon.

## <a name="delete-users"></a>Felhasználók törlése

Felhasználók törléséhez jelöljön ki egy vagy több jelölőnégyzetet a **Felhasználók** lapon. Ezután válassza a **Törlés** elemet.

## <a name="manage-roles"></a>Szerepkörök kezelése

A szerepkörök lehetővé teszik annak szabályozását, hogy a szervezeten belül ki végezhet el különböző feladatokat az IoT Centralban. Az alkalmazás felhasználóihoz három beépített szerepkörrendelhető. [Egyéni szerepköröket](#create-a-custom-role) is létrehozhat, ha finomabb szemcsés vezérlésre van szüksége.

> [!div class="mx-imgBorder"]
> ![Szerepkörök kijelölése kezelése](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Rendszergazda

A **Rendszergazda** szerepkörben lévő felhasználók kezelhetik és szabályozhatják az alkalmazás minden részét, beleértve a számlázást is.

Az alkalmazást létrehozó felhasználó automatikusan hozzá van rendelve a **Rendszergazda** szerepkörhöz. Mindig legyen legalább egy felhasználó a **rendszergazdai** szerepkörben.

### <a name="builder"></a>Szerkesztő

A **Szerkesztő** szerepkörben lévő felhasználók kezelhetik az alkalmazás minden részét, de nem módosíthatják az Adminisztráció vagy a Folyamatos adatexportálás lapot.

### <a name="operator"></a>Művelet

Az **Operátor** szerepkörben lévő felhasználók figyelhetik az eszköz állapotát és állapotát. Nem módosíthatják az eszközsablonokat, és nem felügyelhetik az alkalmazást. Az operátorok eszközöket adhatnak hozzá és törölhetnek, kezelhetik az eszközkészleteket, valamint elemzéseket és feladatokat futtathatnak. 

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Ha a megoldás hoz létre finomabb szemcsés hozzáférés-vezérlés, egyéni szerepkörök egyéni engedélyek et hozhat létre. Egyéni szerepkör létrehozásához keresse meg az alkalmazás **Felügyeleti** szakaszában található **Szerepkörök** lapot. Ezután válassza **a + Új szerepkör**lehetőséget , és adjon nevet és leírást a szerepkörhöz. Jelölje ki a szerepkör által igényelt engedélyeket, majd válassza a **Mentés gombot.**

Az egyéni szerepkörhöz ugyanúgy adhat hozzá felhasználókat, mint ahogy a beépített szerepkörhöz.

> [!div class="mx-imgBorder"]
> ![Egyéni szerepkör létrehozása](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Egyéni szerepkör-beállítások

Amikor egyéni szerepkört ad meg, kiválaszthatja azokat az engedélyeket, amelyeket a felhasználó kap, ha a szerepkör tagja. Egyes engedélyek másoktól függenek. Ha például hozzáadja az **Alkalmazás irányítópultjainak frissítése** engedélyt egy szerepkörhöz, a Rendszer automatikusan hozzáadja az **Alkalmazás-irányítópultok megtekintése** engedélyt. Az alábbi táblázatok összefoglalja a rendelkezésre álló engedélyeket és azok függőségeit, amelyeket egyéni szerepkörök létrehozásakor használhat.

#### <a name="managing-devices"></a>Eszközök kezelése

**Eszközsablon-engedélyek**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None     |
| Kezelés | Nézet <br/> Egyéb függőségek: Eszközpéldányok megtekintése  |
| Teljes hozzáférés | Nézet, Kezelés <br/> Egyéb függőségek: Eszközpéldányok megtekintése |

**Eszközpéldány engedélyei**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése |
| Frissítés | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |
| Létrehozás | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |
| Törlés | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |
| Parancsok végrehajtása | Frissítés, Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |
| Teljes hozzáférés | Nézetek, Frissítés, Létrehozás, Törlés, Parancsok végrehajtása <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |

**Eszközcsoportok engedélyei**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése |
| Frissítés | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése   |
| Létrehozás | Nézet, Frissítés <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése   |
| Törlés | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése   |
| Teljes hozzáférés | Nézet, Frissítés, Létrehozás, Törlés <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése |

**Eszközkapcsolat-kezelési engedélyek**

| Név | Függőségek |
| ---- | -------- |
| Például olvasása | None <br/> Egyéb függőségek: Eszközsablonok, eszközcsoportok, eszközpéldányok megtekintése |
| Példány kezelése | None |
| Globális olvasás | None   |
| Globális kezelése | Globális olvasás |
| Teljes hozzáférés | Olvasott példány, Példány kezelése, Globális olvasás, Globális kezelése. <br/> Egyéb függőségek: Eszközsablonok, eszközcsoportok, eszközpéldányok megtekintése |

**Feladatok engedélyei**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése |
| Frissítés | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése |
| Létrehozás | Nézet, Frissítés <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése |
| Törlés | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése |
| Végrehajtás | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése; Eszközpéldányok frissítése; Parancsok végrehajtása eszközpéldányokon |
| Teljes hozzáférés | Nézet, Frissítés, Létrehozás, Törlés, Végrehajtás <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése; Eszközpéldányok frissítése; Parancsok végrehajtása eszközpéldányokon |

**Szabályok engedélyei**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None <br/> Egyéb függőségek: Eszközsablonok megtekintése |
| Frissítés | Nézet <br/> Egyéb függőségek: Eszközsablonok megtekintése |
| Létrehozás | Nézet, Frissítés <br/> Egyéb függőségek: Eszközsablonok megtekintése |
| Törlés | Nézet <br/> Egyéb függőségek: Eszközsablonok megtekintése |
| Teljes hozzáférés | Nézet, Frissítés, Létrehozás, Törlés <br/> Egyéb függőségek: Eszközsablonok megtekintése |

#### <a name="managing-the-app"></a>Az alkalmazás kezelése

**Alkalmazásbeállítások engedélyei**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None     |
| Frissítés | Nézet   |
| Másolás | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok, eszközcsoportok, irányítópultok, adatexportálás, márkajelzés, súgóhivatkozások, egyéni szerepkörök, szabályok megtekintése |
| Törlés | Nézet   |
| Teljes hozzáférés | Nézet, Frissítés, Másolás, Törlés <br/> Egyéb függőségek: Eszközsablonok, eszközcsoportok, alkalmazás-irányítópultok, adatexportálás, márkajelzés, súgóhivatkozások, egyéni szerepkörök, szabályok megtekintése |

**Alkalmazássablon exportálási engedélyei**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None     |
| Exportálás | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok, eszközcsoportok, irányítópultok, adatexportálás, márkajelzés, súgóhivatkozások, egyéni szerepkörök, szabályok megtekintése |
| Teljes hozzáférés | Nézet, Exportálás <br/> Egyéb függőségek: Eszközsablonok, eszközcsoportok, alkalmazás-irányítópultok, adatexportálás, márkajelzés, súgóhivatkozások, egyéni szerepkörök, szabályok megtekintése |

**Számlázási engedélyek**

| Név | Függőségek |
| ---- | -------- |
| Kezelés | None     |
| Teljes hozzáférés | Kezelés |

#### <a name="managing-users-and-roles"></a>Felhasználók és szerepkörök kezelése

**Egyéni szerepkörök engedélyei**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None |
| Frissítés | Nézet |
| Létrehozás | Nézet, Frissítés |
| Törlés | Nézet |
| Teljes hozzáférés | Nézet, Frissítés, Létrehozás, Törlés |

**Felhasználókezelési engedélyek**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |
| Hozzáadás | Nézet <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |
| Törlés | Nézet <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |
| Teljes hozzáférés | Nézet, Hozzáadás, Törlés <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |

> [!NOTE]
> Az a felhasználó, aki olyan egyéni szerepkörben van, amely engedélyt ad más felhasználók hozzáadására, csak a saját szerepkörénél azonos vagy kevesebb jogosultsággal rendelkező felhasználókat vehet fel.

#### <a name="customizing-the-app"></a>Az alkalmazás testreszabása

**Alkalmazás-irányítópult-engedélyek**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None     |
| Frissítés | Nézet   |
| Létrehozás | Nézet, Frissítés |
| Törlés | Nézet   |
| Teljes hozzáférés | Nézet, Frissítés, Létrehozás, Törlés |

**Személyes irányítópultok engedélyei**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None     |
| Frissítés | Nézet   |
| Létrehozás | Nézet, Frissítés   |
| Törlés | Nézet   |
| Teljes hozzáférés | Nézet, Frissítés, Létrehozás, Törlés |

**Márkajelzési, favicon- és színengedélyek**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None     |
| Frissítés | Nézet   |
| Teljes hozzáférés | Nézet, Frissítés |

**Súgóhivatkozások engedélyei**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None     |
| Frissítés | Nézet   |
| Teljes hozzáférés | Nézet, Frissítés |

#### <a name="extending-the-app"></a>Az alkalmazás bővítése

**Adatexportálási engedélyek**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None     |
| Frissítés | Nézet   |
| Létrehozás | Nézet, Frissítés  |
| Törlés | Nézet   |
| Teljes hozzáférés | Nézet, Frissítés, Létrehozás, Törlés |

**API-tokenengedélyek**

| Név | Függőségek |
| ---- | -------- |
| Nézet | None     |
| Létrehozás | Nézet   |
| Törlés | Nézet   |
| Teljes hozzáférés | Nézet, Létrehozás, Törlés |

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett a felhasználók és szerepkörök azure IoT Central-alkalmazásában történő kezelésével, a javasolt következő lépés a [számla kezelésének megismerése.](howto-view-bill.md)
