---
title: Az Azure Blockchain munkaterület felhasználók kezelése
description: Hogyan kezelheti a felhasználók Azure Blockchain munkaterület.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 80f20e438b1d923e688aeef9e6b353642bd74c27
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158935"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Az Azure Blockchain munkaterület felhasználók kezelése

Azure Blockchain munkaterület tartalmazza a személyek és a konzorcium részét képező szervezetek felhasználói kezelése.

## <a name="prerequisites"></a>Előfeltételek

A munkaterület Blockchain üzembe helyezésre szükség. Lásd: [Azure Blockchain munkaterület központi telepítési](blockchain-workbench-deploy.md) központi telepítés leírását.

## <a name="add-azure-ad-users"></a>Az Azure Active Directory-felhasználók hozzáadása

Az Azure-Blockchain munkaterület hitelesítés, a hozzáférés-vezérlés és a szerepkörök az Azure Active Directory (Azure AD). A Blockchain munkaterület az Azure AD bérlő felhasználók hitelesítéséhez és Blockchain munkaterületet használja. Felhasználók hozzáadása a felügyeleti alkalmazás-szerepkör interakciót és a műveleteket.

Blockchain munkaterület felhasználóknak kell léteznie kell az Azure AD-bérlő az alkalmazásokhoz és szerepkörökhöz rendelheti. Az Azure AD-felhasználók hozzáadásához használja az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  Válassza ki a fiókját a jobb felső sarokban, és váltson a Blockchain munkaterület társított Azure AD-bérlő.
3.  Válassza ki **az Azure Active Directory > felhasználók**. Megjelenik azoknak a felhasználóknak a címtárban.
4.  Felhasználók hozzáadása a könyvtárat, válassza ki a **új felhasználó**. Válassza ki a külső felhasználók **új Vendég felhasználó**.

    ![Új felhasználó](media/blockchain-workbench-manage-users/add-ad-user.png)

5.  Töltse ki a kötelező mezőket az új felhasználó. Kattintson a **Létrehozás** gombra.

Látogasson el [az Azure AD](../active-directory/add-users-azure-active-directory.md) további részleteket a felhasználók kezelése belül az Azure AD dokumentációjában.

## <a name="manage-blockchain-workbench-administrators"></a>Blockchain munkaterület a rendszergazdák kezelése

Miután a könyvtár hozzáadott felhasználók, a következő lépés, hogy válassza körültekintően mely felhasználók Blockchain munkaterület a rendszergazdák. A felhasználók a **rendszergazda** csoportba tartoznak a **rendszergazda alkalmazás-szerepkör** Blockchain munkaterület. Rendszergazdák hozzáadása vagy eltávolítása a, felhasználók hozzárendelése az adott forgatókönyveket, és hozzon létre új alkalmazásokat.

Felhasználók hozzáadása a **rendszergazda** az Azure AD-címtár csoporthoz:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  Ellenőrizze, hogy a fiókja kiválasztja a jobb felső sarokban található Blockchain munkaterület társított Azure AD-bérlő.
3.  Válassza ki **Azure Active Directory > Vállalati alkalmazások**.
4.  Válassza ki az Azure AD ügyfélalkalmazás a Blockchain munkaterület
    
    ![Minden vállalati alkalmazás-regisztráció](media/blockchain-workbench-manage-users/select-blockchain-client-app.png)

5.  Válassza ki **felhasználók és csoportok > felhasználó hozzáadása**.
6.  A **hozzáadása hozzárendelés**, jelölje be **felhasználók**. Válassza ki, vagy keresse meg a felhasználó hozzáadása rendszergazdaként szeretné. Kattintson a **válasszon** kiválasztása befejezésekor.

    ![Hozzárendelés felvétele](media/blockchain-workbench-manage-users/add-user-assignment.png)

9.  Győződjön meg arról **szerepkör** értéke **rendszergazda**
10. Válassza a **Hozzárendelés** elemet. A felvett felhasználók jelennek meg a listában hozzárendelt rendszergazdai szerepkörrel.

    ![Blockchain ügyfél alkalmazás felhasználói](media/blockchain-workbench-manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Blockchain munkaterület tagok kezelése

A Blockchain munkaterület alkalmazás segítségével kezelheti a felhasználók és a szervezetek, amelyek a konzorcium részei. Adja hozzá, vagy távolítsa el az alkalmazások és a szerepkörök a felhasználók számára.

1. [Nyissa meg a Blockchain munkaterület](blockchain-workbench-deploy.md#blockchain-workbench-web-url) a böngésző és a jelentkezzen be rendszergazdaként.

    ![Blockchain Workbench](media/blockchain-workbench-manage-users/blockchain-workbench-applications.png)

    Tagok tagjává válnak az egyes alkalmazásokhoz. Egy vagy több alkalmazás szerepkörök szerződések ill. műveletek is rendelkezik.

2. Kezelheti egy alkalmazás tagok, válassza ki az alkalmazás csempe az a **alkalmazások** ablaktáblán.

    A kijelölt alkalmazáshoz tartozó tagok száma a tagok csempe is megjelenik.

    ![Alkalmazás kijelölése](media/blockchain-workbench-manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Alkalmazás-tag hozzáadása

1. Válassza ki a tagot csempe aktuális tagot listájának megjelenítéséhez.
2. Válassza ki **tagok hozzáadása**.

    ![Tagok hozzáadása](media/blockchain-workbench-manage-users/application-add-members.png)

3. Keresse meg a felhasználónevet.  Csak a Blockchain munkaterület bérlő szerepel az Azure AD-felhasználók szerepelnek. Ha a felhasználó nem található, akkor [adja hozzá az Azure Active Directory-felhasználók](#add-azure-ad-users).

    ![Tagok hozzáadása](media/blockchain-workbench-manage-users/find-user.png)

4. Válassza ki a **szerepkör** a a legördülő listán.

    ![Válassza ki a szerepkör tagjai](media/blockchain-workbench-manage-users/application-select-role.png)

5. Válassza ki **Hozzáadás** a tag a kapcsolódó szerepkör hozzáadása az alkalmazáshoz.

#### <a name="remove-member-from-application"></a>Alkalmazás-tag eltávolítása

1. Válassza ki a tagot csempe aktuális tagot listájának megjelenítéséhez.
2. A felhasználó el szeretné távolítani, válassza a **eltávolítása** legördülő szerepkörből.

    ![Tag eltávolítása](media/blockchain-workbench-manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Módosítsa vagy szerepkör hozzáadása

1. Válassza ki a tagot csempe aktuális tagot listájának megjelenítéséhez.
2. A módosítani kívánt felhasználóhoz kattintson a legördülő listán, és válassza ki az új szerepkör.

    ![Szerepkör módosítása](media/blockchain-workbench-manage-users/application-change-role.png)

## <a name="next-steps"></a>További lépések

Útmutató cikkben rendelkezik megtudta, hogyan kezelheti a felhasználók Azure Blockchain munkaterületet. Megtudhatja, hogyan hozhat létre egy blockchain alkalmazást, folytassa a következő cikkben található útmutató.

> [!div class="nextstepaction"]
> [Azure Blockchain munkaterület blockchain-alkalmazás létrehozása](blockchain-workbench-create-app.md)
