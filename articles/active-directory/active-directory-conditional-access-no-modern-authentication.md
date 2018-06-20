---
title: Az Azure Active Directory feltételes hozzáférés SharePoint Online és Exchange Online beállítása |} Microsoft Docs
description: Útmutató az Azure Active Directory feltételes hozzáférés SharePoint Online és Exchange Online beállítása.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: e666d23cec4ec6d987084bf5dc33bdb7c3db0f66
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231196"
---
# <a name="set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Az Azure Active Directory feltételes hozzáférés SharePoint Online és Exchange Online beállítása 

A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy a felhasználók miként férhetnek hozzá a felhőalapú alkalmazásokat. Ha való hozzáférést a SharePoint és az Exchange online feltételes hozzáférési használni kívánt, kell:

- Ellenőrizze, hogy támogatott-e a feltételes hozzáférési forgatókönyv
- Megakadályozhatja, hogy a ügyfél alkalmazásai megkerüljék a feltételes hozzáférési házirendek érvénybe léptetése.   

Ez a cikk azt ismerteti, hogyan lehet oldani mindkét esetben.


## <a name="what-you-need-to-know"></a>Mit kell tudnia

Az Azure AD feltételes hozzáférés segítségével felhőalapú alkalmazások védelme, ha a hitelesítési kísérletet származik:

- Webböngésző

- Egy ügyfélalkalmazás használó [modern hitelesítést](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Néhány felhőalapú alkalmazásokat is támogatja a hagyományos hitelesítési protokollok megvalósítását végzi. Ez vonatkozik, például a SharePoint Online és Exchange online-hoz. Amikor egy ügyfél-alkalmazás egy régebbi hitelesítési protokoll használatával is hozzáférhetnek a felhőalapú alkalmazások, az Azure AD a hozzáférési kísérlet a feltételes hozzáférési házirend nem érvényesíthetők. Egy ügyfélalkalmazás házirendek érvénybe léptetése megkerülése érdekében ellenőrizni kell, hogy lehetőség csak a az érintett felhőalkalmazások a modern hitelesítés engedélyezése. 

Az ügyfél alkalmazások feltételes hozzáférés nem vonatkozik például a következők:

- Office 2010-es és korábbi verziók

- Office 2013, ha nincs engedélyezve a modern hitelesítés



 
## <a name="control-access-to-sharepoint-online"></a>SharePoint online-hoz való hozzáférés szabályozása

A modern hitelesítés mellett SharePoint online-hoz is támogatja örökölt hitelesítési protokollok megvalósítását végzi. A régebbi hitelesítési protokoll engedélyezett, ha a feltételes hozzáférési házirendek a SharePoint nem kényszeríti ki a nem modern hitelesítést használó ügyfelek.

Letilthatja a SharePoint-hozzáféréshez régebbi hitelesítési protokollok használatával a **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)** parancsmagot: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Exchange online-hoz való hozzáférés szabályozása

Ha a feltételes hozzáférés szabályzatainak beállítása az Exchange online-hoz, át kell tekintenie a következő:

- Exchange ActiveSync

- A hagyományos hitelesítési protokollok



### <a name="exchange-activesync"></a>Exchange ActiveSync

Míg az Exchange Active Sync modern hitelesítést támogatja, van néhány korlátozást vonatkozó feltételes hozzáférési forgatókönyvek támogatása:

- Csak az eszköz platformok feltétel lehet beállítani  

    ![Eszközplatformok](./media/active-directory-conditional-access-no-modern-authentication/05.png)

- A multi-factor authentication követelményeinek beállítása nem támogatott  

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-no-modern-authentication/01.png)

Hatékony védelmére a hozzáférést az Exchange Online az Exchange ActiveSync, a következőket teheti:

- Támogatott feltételes hozzáférési házirend konfigurálása a következő lépések végrehajtásával:

    a. Válassza ki az imént **Office 365 Exchange Online** felhő alkalmazásként.  

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-no-modern-authentication/04.png)

    b. Válassza ki **az Exchange Active Sync** , **ügyfélalkalmazás**, majd válassza ki **házirend alkalmazása csak a támogatott platformok**.  

    ![Eszközplatformok](./media/active-directory-conditional-access-no-modern-authentication/03.png)

- Letiltja az Exchange ActiveSync Active Directory összevonási szolgáltatások (AD FS) vonatkozó szabályok használatával.

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>A hagyományos hitelesítési protokollok

A modern hitelesítés mellett az Exchange Online is támogatja örökölt hitelesítési protokollok megvalósítását végzi. Régebbi hitelesítési protokoll engedélyezett, ha az Exchange online feltételes hozzáférési házirendek nem kényszeríti ki a nem modern hitelesítést használó ügyfelek.

Az AD FS-szabályok beállítása az Exchange Online régebbi hitelesítési protokollok letilthatja. Ez a blokkok hozzáférést:

- Régebbi Office-ügyfelek, például az Office 2013, amelyek nem rendelkeznek a modern hitelesítés engedélyezve van 

- Az Office korábbi verziói


## <a name="set-up-ad-fs-rules"></a>Az AD FS-szabályok beállítása

A következő kiállítás-engedélyezési szabályok segítségével engedélyezze, vagy az AD FS szintjén adatforgalmat. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Az extranetről érkező örökölt kommunikációt blokkoló

A következő három szabályok alkalmazásával: 

- Engedélyezze a hozzáférést:
    - Exchange ActiveSync-forgalom
    - Böngésző-forgalom
    - Modern hitelesítési forgalom
- Hozzáférés letiltása: 
    - Az extranetről érkező korábbi ügyfél alkalmazások

**1. szabály:**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**2. szabály:**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**3. szabály:**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>A hagyományos kommunikációt blokkoló bárhonnan

A következő három szabályok alkalmazásával:

- Engedélyezze a hozzáférést: 
    - Exchange ActiveSync-forgalom
    - Böngésző-forgalom
    - Modern hitelesítési forgalom
- Hozzáférés letiltása: 
    - Az örökölt alkalmazások bárhonnan

##### <a name="rule-1"></a>1. szabály
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>A 2. szabály
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>3. szabály
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>További lépések

További információkért lásd: [Mi az az Azure Active Directoryban feltételes hozzáférés](active-directory-conditional-access-azure-portal.md).

Konfigurálásával kapcsolatos utasításokat a jogcímszabályokba, lásd: [Jogcímszabályok konfigurálása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules). 






