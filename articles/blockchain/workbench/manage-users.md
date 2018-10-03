---
title: Az Azure Blockchain Workbench használatával a felhasználók kezelése
description: Hogyan kezelheti a felhasználókat az Azure Blockchain Workbench alkalmazásban.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: adb4b57ce304a3c6e369d6cc68dbb7c5857fb9fe
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242510"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Az Azure Blockchain Workbench használatával a felhasználók kezelése

Az Azure Blockchain Workbench felhasználókezelés emberek és szervezetek, amelyek részei a consortium tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

A Blockchain Workbench üzembe helyezésre szükség. Lásd: [Azure Blockchain Workbench üzembe helyezési](deploy.md) üzembe helyezési részleteiért.

## <a name="add-azure-ad-users"></a>Az Azure AD-felhasználók hozzáadása

Az Azure Blockchain Workbench az Azure Active Directory (Azure AD-) hitelesítés, hozzáférés-vezérlés és szerepkörök. A Blockchain Workbench az Azure AD-bérlőben található felhasználók hitelesítéséhez és Blockchain Workbench használata. Felhasználók hozzáadása és műveleteket, alkalmazás-rendszergazda szerepköréhez.

Blockchain Workbenchet felhasználóknak, azok hozzárendelését is elvégezheti az alkalmazások és a szerepkörök az Azure AD-bérlőjében léteznie kell. Az Azure AD-felhasználók hozzáadásához használja az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  Jelölje ki a fiók jobb felső sarokban, és váltson át a Blockchain Workbenchet társított Azure AD-bérlővel.
3.  Válassza ki **az Azure Active Directory > felhasználók**. A címtár felhasználók listája látható.
4.  Felhasználók hozzáadása a címtárhoz, jelölje be **új felhasználó**. A külső felhasználók számára, válassza ki a **új vendégfelhasználó**.

    ![Új felhasználó](./media/manage-users/add-ad-user.png)

5.  Töltse ki a kötelező mezőket az új felhasználó. Kattintson a **Létrehozás** gombra.

Látogasson el [Azure ad-ben](../../active-directory/fundamentals/add-users-azure-active-directory.md) dokumentációjában olvashat felhasználók kezelése az Azure AD-ben.

## <a name="manage-blockchain-workbench-administrators"></a>Blockchain Workbenchet rendszergazdák kezelése

Miután a felhasználók vannak hozzáadva a könyvtárba, a következő lépés az válassza ki, hogy mely felhasználók tartoznak a Blockchain Workbench használatával a rendszergazdák. A felhasználók a **rendszergazda** csoporthoz társított a **alkalmazás-rendszergazda szerepkör** a Blockchain Workbenchet. Rendszergazdák hozzáadása vagy távolítsa el a felhasználókat, hozzárendelhet felhasználókat az adott forgatókönyveket, és hozzon létre új alkalmazásokat.

Felhasználók hozzáadása a **rendszergazda** csoportot az Azure AD-címtárban:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  Ellenőrizze, hogy biztosan megfeleljen az a Blockchain Workbenchet a jobb felső sarokban válassza ki a fiók társított Azure AD-bérlővel.
3.  Válassza ki **Azure Active Directory > Vállalati alkalmazások**.
4.  Válassza ki az Azure ad-ben ügyfélalkalmazás Blockchain Workbench használatával
    
    ![Az összes vállalati alkalmazást az alkalmazásregisztrációk](./media/manage-users/select-blockchain-client-app.png)

5.  Válassza ki **felhasználók és csoportok > felhasználó hozzáadása**.
6.  A **hozzárendelés hozzáadása**válassza **felhasználók**. Válassza ki, vagy keresse meg a felhasználó hozzáadása rendszergazdaként szeretné. Kattintson a **kiválasztása** kiválasztása befejezésekor.

    ![Hozzárendelés felvétele](./media/manage-users/add-user-assignment.png)

9.  Győződjön meg arról **szerepkör** értékre van állítva **rendszergazda**
10. Válassza a **Hozzárendelés** elemet. A felvett felhasználók jelennek meg a listában a rendszergazdai szerepkörrel.

    ![Blockchain-ügyfél. alkalmazás felhasználóinak](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Blockchain Workbenchet tagok kezelése

A Blockchain Workbench alkalmazás használatával kezelheti a felhasználókat és a szervezetek számára, amelyek a consortium részét képezik. Adja hozzá, vagy távolítsa el a felhasználók alkalmazásokat és a szerepköröket.

1. [Nyissa meg a Blockchain Workbenchet](deploy.md#blockchain-workbench-web-url) a böngészőben, és jelentkezzen be rendszergazdaként.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Minden alkalmazás tagok kerülnek. Szerződések kezdeményezni, vagy műveleteket egy vagy több alkalmazás-szerepkörök is rendelkezik.

2. Kezelheti egy alkalmazás tagok, válassza ki egy alkalmazás csempét a **alkalmazások** ablaktáblán.

    A kijelölt alkalmazáshoz tartozó tagok számát tükrözi a tagok csempét.

    ![Alkalmazás kijelölése](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Tag hozzáadása az alkalmazáshoz

1. Válassza ki a tag csempére, és a jelenlegi tagok listájának megjelenítéséhez.
2. Válassza ki **tagok hozzáadása**.

    ![Tagok hozzáadása](./media/manage-users/application-add-members.png)

3. Keresse meg a felhasználó nevét.  Csak a Blockchain Workbenchet bérlő szerepel az Azure AD-felhasználók jelennek meg. Ha a felhasználó nem található, akkor [adja hozzá az Azure AD-felhasználók](#add-azure-ad-users).

    ![Tagok hozzáadása](./media/manage-users/find-user.png)

4. Válassza ki a **szerepkör** a legördülő listából.

    ![Válassza ki a szerepkör tagjai](./media/manage-users/application-select-role.png)

5. Válassza ki **Hozzáadás** hozzárendelt szerepkörrel rendelkező tag hozzáadása az alkalmazáshoz.

#### <a name="remove-member-from-application"></a>Tag eltávolítása az alkalmazásból

1. Válassza ki a tag csempére, és a jelenlegi tagok listájának megjelenítéséhez.
2. A felhasználó számára el szeretné távolítani, válassza ki a **eltávolítása** legördülő szerepkörből.

    ![Tag eltávolítása](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Módosítása vagy hozzáadása szerepkörhöz

1. Válassza ki a tag csempére, és a jelenlegi tagok listájának megjelenítéséhez.
2. A módosítani kívánt felhasználóhoz kattintson a legördülő, és válassza ki az új szerepkör.

    ![Szerepkör módosítása](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>További lépések

Útmutató cikkben megtanulhatta, hogyan kezelheti a felhasználók az Azure Blockchain Workbench használatával. Ismerje meg, hogyan hozhat létre a blockchain-alkalmazások, folytassa a következő cikkben található útmutató.

> [!div class="nextstepaction"]
> [A blockchain-alkalmazások létrehozása az Azure Blockchain Workbench használatával](create-app.md)
