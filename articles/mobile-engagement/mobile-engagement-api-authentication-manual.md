---
title: 'Mobile Engagement REST API-k a hitelesítést: manuális beállítása'
description: Ismerteti, hogyan lehet manuálisan a Mobile Engagement REST API-k hitelesítés beállítása
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0d71908b34ddf8313aa45014420c9e63a00078c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Azokkal a Mobile Engagement-REST API-k - manuális beállítása
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ez a dokumentáció az ad [hitelesítés a Mobile Engagement REST API-k](mobile-engagement-api-authentication.md). Ellenőrizze, hogy elolvasta a cikkben először segítségével értheti meg a környezetben. Azt is bemutatja egy másik módja a egyszeri hitelesítés beállítása a Mobile Engagement REST API-k számára az Azure portál használatával.

> [!NOTE]
> Az alábbi utasításokat a alapuló [Ez az útmutató az Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md). Azok a hitelesítésének követelményei a Mobile Engagement API-k testreszabott. Tekintse meg őket, ha szeretné tudni, hogy az alábbi lépéseket részletesen.

1. Az Azure-fiók használatával jelentkezzen be a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **Active Directory** a bal oldali ablaktáblán.

   ![Select Active Directory][1]

3. Válassza ki, ha az alkalmazások a címtárban, **App regisztrációk**.

   ![alkalmazások megtekintése][3]

4. Válassza ki **új alkalmazás regisztrációja**.

   ![Alkalmazás hozzáadása][4]

5. Töltse ki az alkalmazás nevében. Hagyja meg az alkalmazás típusú **Web app/API**, majd válassza ki a **következő** gombra. Megadhatja, hogy bármilyen típusú URL-címéből **SIGN-ON URL**. Nem használhatók ebben a forgatókönyvben, és az URL-címek magukat a rendszer nem érvényesíti.

   Befejezése után egy Azure Active Directory (Azure AD) alkalmazást, a megadott név. Ez a **AD\_APP\_neve**, ezért mindenképpen jegyezze fel, akkor a.

   ![Alkalmazásnév][8]

7. Válassza ki az alkalmazás nevét.

8. Található **Alkalmazásazonosító** és jegyezze fel a azt. A használandó ügyfél-azonosító **ügyfél\_azonosító** az API-hívásokat.

   ![Az alkalmazás-azonosító található][10]

9. Keresés a **kulcsok** szakaszban kattintson a jobb.

   ![Kulcsok szakasz][11]

10. Hozzon létre egy új kulcsot, és azonnal másolja a azt. Ez nem látható újra.

    ![Kulcs adatait a kulcsok panelen][12]

    > [!IMPORTANT]
    > Ez a kulcs lejár a megadott időtartam végén. Győződjön meg arról, hogy újítsa meg, ha az idő, ellenkező esetben a API authentication nem többé működni. Ha úgy gondolja, hogy ez a kulcs biztonsága sérült, törlése, és hozza létre újból.
    >
    
11. Válassza ki a **végpontok** gombra az oldal tetején. Másolja a **OAUTH 2.0 TOKEN-VÉGPONT**.

    ![Másolja a végpont][14]

16. A végpont a következő formátumban: az URL-címben GUID van a **TENANT_ID**: `https://login.microsoftonline.com/<GUID>/oauth2/token`

17. Ezt követően az engedélyek konfigurálnia ezt az alkalmazást. A folyamat indításához lépjen a [Azure-portálon](https://portal.azure.com).

18. Válassza ki **erőforráscsoportok**, majd keresse meg a **MobileEngagement** erőforráscsoportot.

    ![MobileEngagement keresése][15]

19. Válassza ki a **MobileEngagement** erőforrás csoportot, és adja **összes beállítás**.

    ![Keresse meg a MobileEngagement beállításai][16]

20. Válassza ki **felhasználók** a a **beállítások** szakasz. Adja hozzá a felhasználót, majd válassza ki a **Hozzáadás**.

    ![Felhasználó hozzáadása][17]

21. Kattintson a **Szerepkörválasztás**.

    ![Szerepkör kiválasztása][18]

22. Válassza ki **tulajdonos**.

    ![Jelölje ki a szerepkör tulajdonosa][19]

23. Keresse meg az alkalmazás neve **AD\_APP\_neve**, a keresési mezőbe. Ez a név nincs itt alapértelmezés szerint. Miután megtalálta, válassza ki azt. Kattintson a **válasszon** szakasz alján.

    ![Válassza ki a nevét][20]

24. Az a **hozzáférés hozzáadása** területen jelenik meg **1 felhasználó, a 0 csoportok**. A módosítás megerősítéséhez jelölje ki a **OK**.

    ![Erősítse meg a hozzáadott felhasználó][21]

Ezennel befejezte a kívánt Azure AD konfigurálása és a lehetőségek egyaránt be vannak beállítva az API-k meghívásához.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



