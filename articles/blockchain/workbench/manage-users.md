---
title: Felhasználók kezelése az Azure Blockchain Workbench szolgáltatásban
description: Hogyan kezelheti a felhasználókat az Azure Blockchain Workbench ben.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 573ec477a3e75beb91f90da0545fb7d4c0f9bf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252180"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Felhasználók kezelése az Azure Blockchain Workbench szolgáltatásban

Az Azure Blockchain Workbench felhasználói felügyeletet tartalmaz a konzorcium részét vevő személyek és szervezetek számára.

## <a name="prerequisites"></a>Előfeltételek

A Blockchain Workbench üzembe helyezése szükséges. Tekintse meg [az Azure Blockchain Workbench üzembe helyezését](deploy.md) a központi telepítésrészleteiért.

## <a name="add-azure-ad-users"></a>Azure AD-felhasználók hozzáadása

Az Azure Blockchain Workbench az Azure Active Directoryt (Azure AD) használja a hitelesítéshez, a hozzáférés-vezérléshez és a szerepkörökhöz. A blockchain workbench Azure AD-bérlő felhasználói hitelesíthetik és használhatják a Blockchain Workbench-et. Felhasználók hozzáadása a Rendszergazda alkalmazás szerepkörhöz a műveletek kezeléséhez és végrehajtásához.

A Blockchain Workbench-felhasználóknak létezniük kell az Azure AD-bérlőben, mielőtt hozzárendelheti őket az alkalmazásokhoz és szerepkörökhöz. Felhasználók hozzáadása az Azure AD-hez, kövesse az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2.  Válassza ki a fiók a jobb felső sarokban, és váltson az Azure AD-bérlő a Blockchain Workbench társított.
3.  Válassza az **Azure Active Directory > felhasználók lehetőséget.** Megjelenik a címtárban lévő felhasználók listája.
4.  Ha felhasználókat szeretne hozzáadni a címtárhoz, válassza az **Új felhasználó**lehetőséget. Külső felhasználók esetén válassza az **Új vendégfelhasználó lehetőséget.**

    ![Új felhasználó](./media/manage-users/add-ad-user.png)

5.  Töltse ki az új felhasználó szükséges mezőit. Kattintson a **Létrehozás** gombra.

Az [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) dokumentációjában további részleteket talál a felhasználók Azure AD-n belüli kezeléséről.

## <a name="manage-blockchain-workbench-administrators"></a>A Blockchain Workbench rendszergazdáinak kezelése

Miután a felhasználók hozzá lettek adva a címtárhoz, a következő lépés az, hogy melyik felhasználó blockchain workbench rendszergazdák. A **Rendszergazda** csoport felhasználói a Blockchain Workbench **Rendszergazda alkalmazásszerepköréhez** vannak társítva. A rendszergazdák felhasználókat vehetnek fel vagy távolíthatnak el, felhasználókat rendelhetnek adott esetekhez, és új alkalmazásokat hozhatnak létre.

Felhasználók hozzáadása a **Rendszergazda** csoporthoz az Azure AD könyvtárban:

1.  Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2.  Ellenőrizze, hogy a Blockchain Workbench-hez társított Azure AD-bérlőben van-e, ha a jobb felső sarokban kiválasztja a fiókját.
3.  Válassza az **Azure Active Directory > Enterprise alkalmazások lehetőséget.**
4.  Válassza ki az Azure AD-ügyfélalkalmazást a Blockchain Workbench-hez
    
    ![Minden vállalati alkalmazás regisztrációja](./media/manage-users/select-blockchain-client-app.png)

5.  Válassza a **Felhasználók és csoportok lehetőséget > Felhasználó hozzáadása lehetőséget.**
6.  A **Hozzárendelés felvétele** résznél válassza a **Felhasználók** elemet. Válassza ki vagy keresse meg azt a felhasználót, amelyet rendszergazdaként szeretne hozzáadni. Kattintson **a Kijelölés gombra,** ha végzett a választással.

    ![Hozzárendelés hozzáadása](./media/manage-users/add-user-assignment.png)

9.  Az **igazolási szerepkör** **rendszergazda**
10. Válassza a **Hozzárendelés** elemet. A hozzáadott felhasználók a rendszergazdai szerepkörrel ellátott listában jelennek meg.

    ![Blockchain ügyfélalkalmazás-felhasználók](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>A Blockchain Workbench-tagok kezelése

A Blockchain Workbench alkalmazás segítségével kezelheti a felhasználók és szervezetek, amelyek részét képezik a konzorcium. Felhasználókat vehet fel vagy távolíthat el az alkalmazásokhoz és szerepkörökhöz.

1. [Nyissa meg a Blockchain Workbench](deploy.md#blockchain-workbench-web-url) böngészőjét, és jelentkezzen be rendszergazdaként.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    A tagok minden alkalmazáshoz hozzáadódnak. A tagok nak egy vagy több alkalmazásszerepkörük lehet a szerződések kezdeményezéséhez vagy a műveletek hez.

2. Egy alkalmazás tagjainak kezeléséhez jelöljön ki egy alkalmazáscsempét az **Alkalmazások** ablaktáblán.

    A kijelölt alkalmazáshoz társított tagok száma megjelenik a tagok csempén.

    ![Alkalmazás kiválasztása](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Tag hozzáadása az alkalmazáshoz

1. Jelölje ki a tagcsempét az aktuális tagok listájának megjelenítéséhez.
2. Válassza **a Tagok hozzáadása**lehetőséget.

    ![Tagok hozzáadása](./media/manage-users/application-add-members.png)

3. Keresse meg a felhasználó nevét.  Csak a Blockchain Workbench-bérlőben létező Azure AD-felhasználók szerepelnek a listában. Ha a felhasználó nem található, hozzá kell [adnia az Azure AD-felhasználókat.](#add-azure-ad-users)

    ![Tagok hozzáadása](./media/manage-users/find-user.png)

4. Válasszon **egy szerepkört** a legördülő menüből.

    ![Szerepkör-tagok kijelölése](./media/manage-users/application-select-role.png)

5. Válassza a **Hozzáadás** lehetőséget, ha hozzá szeretné adni a társított szerepkörrel rendelkező tagot az alkalmazáshoz.

#### <a name="remove-member-from-application"></a>Tag eltávolítása az alkalmazásból

1. Jelölje ki a tagcsempét az aktuális tagok listájának megjelenítéséhez.
2. Az eltávolítani kívánt felhasználó esetében válassza az **Eltávolítás** a szerepkörből legördülő lehetőséget.

    ![Tag eltávolítása](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Szerepkör módosítása vagy hozzáadása

1. Jelölje ki a tagcsempét az aktuális tagok listájának megjelenítéséhez.
2. A módosítani kívánt felhasználó esetében kattintson a legördülő menüre, és válassza ki az új szerepkört.

    ![Szerepkör módosítása](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>További lépések

Ebben az útmutató cikkben megtanulta, hogyan kezelheti a felhasználókat az Azure Blockchain Workbench számára. Ha meg szeretné tudni, hogyan hozhat létre egy blockchain alkalmazást, folytassa a következő útmutató cikkel.

> [!div class="nextstepaction"]
> [Blockchain alkalmazás létrehozása az Azure Blockchain Workbench szolgáltatásban](create-app.md)
