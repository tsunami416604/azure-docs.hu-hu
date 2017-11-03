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
ms.openlocfilehash: 9d6132e1a01be489b8e8e28a0219cf8a0b50b318
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Azokkal a Mobile Engagement-REST API-k - manuális beállítása
Ez egy függelék dokumentációt, amelyik az [hitelesítés a Mobile Engagement REST API-k](mobile-engagement-api-authentication.md). Győződjön meg arról, hogy elolvasta, a környezet használatának. Ez azt ismerteti, hogy egy másik módja a hitelesítés a Mobile Engagement REST API-kat az Azure portál használata az egyszeri telepítőprogramja. 

> [!NOTE]
> Az alábbi utasításokat a alapuló [útmutató az Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) és testre szabható a Mobile Engagement API-k hitelesítéshez szükséges. Ezért tekintse meg, ha szeretné tudni, hogy az alábbi lépéseket részletesen. 
> 
> 

1. Jelentkezzen be az Azure-fiók keresztül a [klasszikus portál](https://manage.windowsazure.com/).
2. Válassza ki **Active Directory** a bal oldali ablaktáblán.
   
     ![az Active Directory kiválasztása][1]
3. Válassza ki a **Active Directory alapértelmezett** az Azure-portálon. 
   
     ![Válassza ki a könyvtár][2]
   
   > [!IMPORTANT]
   > Ez a módszer csak akkor, ha az Active Directory-fiókja alapértelmezett dolgozik, és nem fog működni, akkor használatos, ha ez egy Active Directory fiókjában létrehozott működik. 
   > 
   > 
4. A könyvtárban az alkalmazások megtekintéséhez kattintson a **alkalmazások**.
   
     ![alkalmazások megtekintése][3]
5. Kattintson a **hozzáadása**. 
   
     ![Alkalmazás hozzáadása][4]
6. Kattintson a **a szerveztem által fejlesztett alkalmazás hozzáadása**
   
     ![Új alkalmazás][5]
7. Adja meg az alkalmazás nevét, és adja meg, amelyeket **WEB APPLICATION AND/OR WEB API** , majd kattintson a Tovább gombra.
   
     ![alkalmazás neve][6]
8. Megadhatja, hogy bármilyen típusú URL-címéből **SIGN-ON URL** és **APP ID URI**. A mi esetünkben nem használhatók, és maguk URL-címeket a rendszer nem érvényesíti.  
   
     ![Alkalmazás tulajdonságai][7]
9. Ez végén kell egy AAD-alkalmazás a következő korábban megadott névvel. Ez a **AD\_APP\_neve** és jegyezze fel a azt.  
   
     ![Alkalmazás neve][8]
10. Kattintson az alkalmazás nevére, majd kattintson a **konfigurálása**.
    
      ![alkalmazás konfigurálása][9]
11. Jegyezze fel a használandó ügyfél-azonosító **ügyfél\_azonosító** az API-hívásokat. 
    
     ![alkalmazás konfigurálása][10]
12. Görgessen le a **kulcsok** szakaszt, és adja hozzá a 2 év (lejárati) időtartama lehetőleg kulcsot, és kattintson **mentése**. 
    
     ![alkalmazás konfigurálása][11]
13. Azonnal kimásolhatja az értéket, a kulcs csak akkor jelenik meg most és nem tárolja, nem fog megjelenni egyre újra látható. Ha az elveszített majd kell hozzon létre egy új kulcsot. Ez lesz a **CLIENT_SECRET** az API-hívásokat. 
    
     ![alkalmazás konfigurálása][12]
    
    > [!IMPORTANT]
    > Ezt a kulcsot a, ügyeljen rá, hogy újítsa meg, ha a idő ellenkező esetben az API-hitelesítés nem fog többé működni megadott időtartamot végén lejár. Töröl, és hozza létre újból ezt a kulcsot, ha úgy gondolja, hogy veszélyben van.
    > 
    > 
14. Kattintson a **VÉGPONTOK megtekintése** gombra kattint, most amely kattintva megnyílik a **App végpontok** párbeszédpanel megnyitásához. 
    
    ![][13]
15. Az alkalmazás végpontok párbeszédpanel, másolja a **OAUTH 2.0 TOKEN-VÉGPONT**. 
    
    ![][14]
16. A végpont a következő képernyőn a GUID az URL-cím lesz a **TENANT_ID** , jegyezze fel a azt: 
    
        https://login.microsoftonline.com/<GUID>/oauth2/token
17. Most azt folytatódik az engedélyek beállításához meg ezt az alkalmazást. Ehhez meg kell nyitnia a [Azure-portálon](https://portal.azure.com). 
18. Kattintson a **erőforráscsoportok** keresse meg a **a Mobile Engagement** erőforráscsoportot.  
    
    ![][15]
19. Kattintson a **a Mobile Engagement** erőforrás csoportban, és keresse meg a **beállítások** itt panelen. 
    
    ![][16]
20. Kattintson a **felhasználók** a beállítások panelen, és kattintson a **Hozzáadás** hozzáadni egy felhasználót. 
    
    ![][17]
21. Kattintson a **szerepkör kiválasztása**
    
    ![][18]
22. Kattintson a **tulajdonosa**
    
    ![][19]
23. Keresse meg az alkalmazás neve **AD\_APP\_neve** be a keresőmezőbe. Nem látják ezt itt alapértelmezés szerint. Miután megtalálta, válassza ki azt, majd kattintson a **válasszon** a panel alján. 
    
    ![][20]
24. Az a **hozzáférés hozzáadása** panelen megjelenik **1 felhasználó, a 0 csoportok**. Kattintson a **OK** a panel a módosítás megerősítéséhez. 
    
    ![][21]

Ezennel befejezte a szükséges AAD-konfigurációt, és a rendszer összes megadta az API-k meghívásához. 

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
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
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



