---
title: "Azokkal a Mobile Engagement-REST API-k - manuális beállítása"
description: "Ismerteti, hogyan lehet manuálisan a Mobile Engagement REST API-k hitelesítés beállítása"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Azokkal a Mobile Engagement-REST API-k - manuális beállítása
Ebben a dokumentációban egy függelék dokumentációt, amelyik [hitelesítés a Mobile Engagement REST API-k](mobile-engagement-api-authentication.md). Győződjön meg arról, hogy elolvasta, a környezet használatának.
Egy másik módja a hitelesítés a Mobile Engagement REST API-kat az Azure portál használata az egyszeri telepítőprogramja ismerteti.

> [!NOTE]
> Az alábbi utasításokat a alapuló [útmutató az Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) és testre szabható a Mobile Engagement API-k hitelesítéshez szükséges. Ezért tekintse meg, ha szeretné tudni, hogy az alábbi lépéseket részletesen.

1. Az Azure-fiók használatával jelentkezzen be a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **Active Directory** a bal oldali ablaktáblán.

     ![az Active Directory kiválasztása][1]

3. A könyvtárban az alkalmazások megtekintéséhez kattintson a **App regisztrációk**.

     ![alkalmazások megtekintése][3]

4. Kattintson a **új alkalmazás regisztrációja**.

     ![Alkalmazás hozzáadása][4]

5. Adja meg az alkalmazás nevét, és hagyja meg az alkalmazás típusú **Web app/API** , majd kattintson a Tovább gombra. Megadhatja, hogy bármilyen típusú URL-címéből **SIGN-ON URL**: nem használhatók ebben a forgatókönyvben, és az URL-címek magukat a rendszer nem érvényesíti.
6. Ha végzett, az Azure AD-alkalmazáshoz megadott névvel rendelkezik. Ez a **AD\_APP\_neve**, adjon jegyezze fel az azt.

     ![alkalmazás neve][8]

7. Kattintson az alkalmazás nevére.
8. Található **Alkalmazásazonosító**, jegyezze fel a azt, hogy az használ az ügyfél-azonosító lehet **ügyfél\_azonosító** az API-hívásokat.

     ![alkalmazás konfigurálása][10]

9. Keresés a **kulcsok** szakaszban kattintson a jobb.

     ![alkalmazás konfigurálása][11]

10. Hozzon létre egy új kulcsot, azonnal másolja, és mentse való használatra. Ez soha nem jelenik meg újra.

     ![alkalmazás konfigurálása][12]

    > [!IMPORTANT]
    > Ezt a kulcsot a, ügyeljen rá, hogy újítsa meg, ha a idő ellenkező esetben az API-hitelesítés nem fog többé működni megadott időtartamot végén lejár. Töröl, és hozza létre újból ezt a kulcsot, ha úgy gondolja, hogy veszélyben van.
    >
    >
11. Kattintson a **végpontok** gomb felső részén a lapjáról, és másolja a **OAUTH 2.0 TOKEN-VÉGPONT**.

    ![][14]

16. A végpont a következő képernyőn a GUID az URL-cím lesz a **TENANT_ID** , jegyezze fel a azt:`https://login.microsoftonline.com/<GUID>/oauth2/token`
17. Most azt folytatódik az engedélyek beállításához meg ezt az alkalmazást. Ehhez meg kell nyitnia a [Azure-portálon](https://portal.azure.com). 
18. Kattintson a **erőforráscsoportok** keresse meg a **a Mobile Engagement** erőforráscsoportot.

    ![][15]

19. Kattintson a **a Mobile Engagement** erőforrás csoportban, és keresse meg a **beállítások** szakasz itt.

    ![][16]

20. Kattintson a **felhasználók** beállítások szakaszban, majd kattintson a **Hozzáadás** hozzáadni egy felhasználót.

    ![][17]

21. Kattintson a **Szerepkörválasztás**.

    ![][18]

22. Kattintson a **tulajdonos**.

    ![][19]

23. Keresse meg az alkalmazás neve **AD\_APP\_neve** be a keresőmezőbe. Nem látják ezt itt alapértelmezés szerint. Miután megtalálta, válassza ki azt, majd kattintson a **válasszon** szakasz alján.

    ![][20]

24. Az a **hozzáférés hozzáadása** területen megjelenik **1 felhasználó, a 0 csoportok**. Kattintson a **OK** az ebben a szakaszban a módosítás megerősítéséhez.

    ![][21]

Ezennel befejezte a szükséges az Azure Active Directory beállítása, és mindegyikhez hívja az API-kat.

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



