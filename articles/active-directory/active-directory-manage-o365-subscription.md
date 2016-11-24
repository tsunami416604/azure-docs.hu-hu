---
title: "Office 365-előfizetéshez tartozó címtár kezelése az Azure-ban | Microsoft Docs"
description: "Egy Office 365-előfizetés címtárának kezelése az Azure Active Directoryval és a klasszikus Azure portállal"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 746987b7-2dfd-4b35-819d-37c1b65c5c81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/09/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: ba3690084439aac83c91a1b4cfb7171b74c814f8
ms.openlocfilehash: f0d882509abde7f5dd3d380087e38557ed0163a6


---
# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Manage the directory for your Office 365 subscription in Azure (Office 365-előfizetéshez tartozó címtár kezelése az Azure-ban)
Ez a cikk ismerteti, hogyan kezelheti az Office 365-előfizetéshez létrehozott címtárakat a klasszikus Azure portál használatával. Az Azure-előfizetés szolgáltatás-rendszergazdájának vagy társadminisztrátorának kell lennie, hogy bejelentkezhessen a klasszikus Azure Portalra. Ha még nem rendelkezik Azure-előfizetéssel, regisztrálhat egy [30 napos ingyenes próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/), és kevesebb mint 5 perc alatt üzembe helyezheti az első felhőalapú megoldást ezen hivatkozás használatával. Azt a munkahelyi vagy iskolai fiókot használja, amelyet az Office 365-ba történő bejelentkezéshez használ.

Az Azure-előfizetés elvégzése után bejelentkezhet a klasszikus Azure portálra, és elérheti az Azure-szolgáltatásokat. Kattintson az Active Directory-bővítményre az Office 365-felhasználókat hitelesítő címtár kezeléséhez.

Ha már van Azure-előfizetése, a további címtárak kezelésének folyamata is egyszerű. Michael Smith például rendelkezhet egy Office 365-előfizetéssel a Contoso.com tartományhoz. Azure-előfizetése is lehet, amelyre az msmith@hotmail.com. Microsoft-fiókjával regisztrált. Ebben az esetben két címtárat kezel.

| Előfizetést | Office 365 | Azure |
| --- | --- | --- |
|   Megjelenített név |Contoso |Alapértelmezett Azure Active Directory (Azure AD) címtár |
|   Tartománynév |contoso.com |msmithhotmail.onmicrosoft.com |

Kezelni szeretné a felhasználói identitásokat a Contoso címtárban, mialatt a Microsoft-fiókjával van bejelentkezve az Azure-ba, hogy olyan Azure AD-funkciókat engedélyezhessen, mint a többtényezős hitelesítés. A következő diagram segíthet ábrázolni a folyamatot.

![Két független címtár kezelésének diagramja](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

Ebben az esetben a két címtár egymástól független.

## <a name="to-manage-two-independent-directories"></a>Két független címtár kezelése
Ahhoz, hogy Michael Smith mindkét címtárat kezelhesse, mialatt az msmith@hotmail.com, fiókkal van bejelentkezve az Azure-ba, a következő lépéseket kell elvégeznie:

> [!NOTE]
> Ezek a lépések csak akkor végezhetők el, ha egy felhasználó Microsoft-fiókkal van bejelentkezve. Ha a felhasználó munkahelyi vagy iskolai fiókkal jelentkezik be, a **Meglévő címtár használata** lehetőség nem érhető el. A munkahelyi vagy iskolai fiókok csak a saját címtárukkal hitelesíthetők (vagyis azzal a címtárral, ahol a munkahelyi vagy iskolai fiók tárolva van, és amelynek a munkahely vagy az iskola a tulajdonosa).
>
>

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) a következőként: msmith@hotmail.com.
2. Kattintson az **Új** > **Alkalmazásszolgáltatások** > **Active Directory** > **Címtár** > **Egyéni létrehozása** lehetőségre.
3. Kattintson a Meglévő címtár használata elemre, és jelölje be a **Készen állok a kijelentkezésre** jelölőnégyzetet.
4. Jelentkezzen be a klasszikus Azure portálra a Contoso.onmicrosoft.com globális rendszergazdájaként (például az msmith@contoso.com fiókkal).
5. A **Használhatom a Contoso címtárát az Azure-ral?** kérdésnél kattintson a **Folytatás** gombra.
6. Kattintson az **Azonnali kijelentkezés** parancsra.
7. Jelentkezzen be a klasszikus Azure portálra az msmith@hotmail.com. fiókkal. A Contoso-címtár és az alapértelmezett címtár megjelenik az Active Directory-bővítményben.

Ezen lépések elvégzése után az msmith@hotmail.com a Contoso címtár globális rendszergazdája lesz.

## <a name="to-administer-resources-as-the-global-admin"></a>Erőforrások felügyelete globális rendszergazdaként
Most tegyük fel, hogy Jane Doe-nak az msmith@hotmail.com. fiók Azure-előfizetésével társított webhelyeket és adatbázis-erőforrásokat kell felügyelnie. Mielőtt ezt megtehetné, Michael Smith-nek el kell végeznie ezeket a további lépéseket:

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) az Azure-előfizetés Szolgáltatás-rendszergazdai fiókjával (ebben a példában az msmith@hotmail.com). fiókkal)
2. Vigye át az előfizetést a Contoso címtárra: kattintson a **Beállítások** > **Előfizetések** elemre > válassza ki az előfizetést > **Címtár szerkesztése** > válassza a **Contoso (Contoso.com)** elemet. Az átvitel részeként eltávolít minden olyan munkahelyi vagy iskolai fiókot, amely az előfizetés társadminisztrátora.
3. Adja hozzá Jane Doe-t az előfizetés társadminisztrátoraként: kattintson a **Beállítások** > **Rendszergazdák** elemre > válassza ki az előfizetést > **Hozzáadás** > írja be a **JohnDoe@Contoso.com** értéket.

## <a name="next-steps"></a>Következő lépések
Az előfizetések és a címtárak közötti kapcsolatról további információ: [Hogyan vannak társítva az előfizetések a címtárakkal](active-directory-how-subscriptions-associated-directory.md).



<!--HONumber=Nov16_HO2-->


