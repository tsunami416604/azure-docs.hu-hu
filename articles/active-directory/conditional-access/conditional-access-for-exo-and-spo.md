---
title: Az Azure Active Directory feltételes hozzáférés beállítása a SharePoint Online és Exchange online-hoz |} A Microsoft Docs
description: Ismerje meg, hogyan beállítása az Azure Active Directory feltételes hozzáférés SharePoint Online és Exchange online-hoz.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 79d6efafba2411bea85ab8c959716ebc38817490
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080412"
---
# <a name="how-to-set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>kézikönyv: Az Azure Active Directory feltételes hozzáférés beállítása a SharePoint Online és Exchange online-hoz 

A [Azure Active Directory (Azure AD) feltételes hozzáférés](overview.md), szabályozhatja, hogy a felhasználók hogyan férhetnek hozzá a felhőalapú alkalmazások. Ha azt szeretné, hogy ki férhet hozzá a SharePoint és Exchange online feltételes hozzáférés használatának kell tennie:

- Ellenőrizze, hogy támogatott-e a feltételes hozzáférési forgatókönyv
- Megakadályozza, hogy ügyfélalkalmazások megkerüli a feltételes hozzáférési szabályzatok érvénybe léptetése.   

Ez a cikk bemutatja, miként kezelheti mindkét esetben.


## <a name="what-you-need-to-know"></a>Mit kell tudni

Használhatja az Azure AD feltételes hozzáféréssel felhőalapú alkalmazások védelmére, ha a helyi hitelesítési kísérlet:

- Egy webes böngésző

- Egy ügyfélalkalmazás által használt [modern hitelesítéssel](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Néhány felhőalkalmazások is támogatják az örökölt hitelesítési protokollok. Ez vonatkozik, például a SharePoint Online és Exchange online-hoz. Amikor egy ügyfélalkalmazás egy régebbi hitelesítési protokoll használatával egy felhőalapú alkalmazás eléréséhez, az Azure AD a hozzáférési kísérlet a feltételes hozzáférési szabályzat nem érvényesíthetők. Akadályozni, hogy egy ügyfélalkalmazás kihagyásával házirendek kényszerítését, ellenőrizni kell, hogy lehetőség a csak az érintett felhőalkalmazásokat a modern hitelesítés engedélyezése. 

Példák az alkalmazások feltételes hozzáférés nem vonatkozik az ügyfél a következők:

- Office 2010-es és korábbi verziók

- Office 2013-at, ha nincs engedélyezve a modern hitelesítés



 
## <a name="control-access-to-sharepoint-online"></a>A SharePoint online-hoz való hozzáférés vezérlése

Modern hitelesítés mellett a SharePoint online-hoz is támogatja az örökölt hitelesítési protokollok. Az örökölt hitelesítési protokollok engedélyezettek, ha a rendszer nem tartatja a feltételes hozzáférési szabályzatokat, a SharePoint, ne a modern hitelesítést használó ügyfelek számára.

Letilthatja a SharePoint-hozzáférés az örökölt hitelesítési protokollok használatával a **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)** parancsmagot: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Az Exchange online-hoz való hozzáférés vezérlése

Feltételes hozzáférési szabályzatok beállítása az Exchange online-hoz, ha át kell tekintenie a következőket:

- Exchange ActiveSync

- Az örökölt hitelesítési protokollok



### <a name="exchange-activesync"></a>Exchange ActiveSync

Míg az Exchange Active Sync szolgáltatással támogatja a modern hitelesítést, akkor korlátozásokba vonatkozó feltételes hozzáférési forgatókönyvek támogatása:

- Csak lehet beállítani az eszköz platformok feltétel  

    ![Eszközplatformok](./media/conditional-access-for-exo-and-spo/05.png)

- A multi-factor authentication követelményeinek beállítása nem támogatott  

    ![Feltételes hozzáférés](./media/conditional-access-for-exo-and-spo/01.png)

Hatékony védelmére a hozzáférést az Exchange online-hoz az Exchange ActiveSync, a következőket teheti:

- Támogatott a feltételes hozzáférési szabályzat konfigurálása az alábbi lépéseket:

    a. Válassza ki az imént **Office 365 Exchange Online** felhőalapú alkalmazásként.  

    ![Feltételes hozzáférés](./media/conditional-access-for-exo-and-spo/04.png)

    b. Válassza ki **Exchange Active Sync** , **ügyfélalkalmazás**, majd válassza ki **házirend alkalmazása csak a támogatott platformok**.  

    ![Eszközplatformok](./media/conditional-access-for-exo-and-spo/03.png)

- Letiltja az Exchange ActiveSync az Active Directory összevonási szolgáltatások (AD FS) szabályok segítségével.

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Az örökölt hitelesítési protokollok

Modern hitelesítés mellett az Exchange online-hoz is támogatja az örökölt hitelesítési protokollok. Az örökölt hitelesítési protokollok engedélyezettek, ha a rendszer nem tartatja az Exchange Online feltételes hozzáférési szabályzatot a nem modern hitelesítést használó ügyfelek számára.

Az AD FS szabályok beállításával letilthatja az örökölt hitelesítési protokollok Exchange online-hoz. Ezt az blokkolja a hozzáférést:

- Régebbi Office-ügyfelek, például az Office 2013, amelyek nem rendelkeznek a modern hitelesítés engedélyezve 

- Az Office korábbi verziói


## <a name="set-up-ad-fs-rules"></a>Az AD FS-szabályok beállítása

A következő kiállításengedélyezési szabályok használatával engedélyezi vagy letiltja az AD FS-szintű forgalom. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Az extranetről érkező örökölt forgalom blokkolása

A következő három szabályok alkalmazásával: 

- Engedélyezze a hozzáférést:
    - Exchange ActiveSync-forgalom
    - Böngésző-forgalom
    - Modern hitelesítési forgalom
- Hozzáférésének blokkolása: 
    - Az extranetről érkező régebbi ügyfélalkalmazások

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

### <a name="block-legacy-traffic-from-anywhere"></a>Bárhonnan örökölt forgalom blokkolása

A következő három szabályok alkalmazásával:

- Engedélyezze a hozzáférést: 
    - Exchange ActiveSync-forgalom
    - Böngésző-forgalom
    - Modern hitelesítési forgalom
- Hozzáférésének blokkolása: 
    - Örökölt alkalmazások bárhonnan

##### <a name="rule-1"></a>1. számú szabály
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>2. szabály
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>3. szabály
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>További lépések

További információkért lásd: [Mi a feltételes hozzáférés az Azure Active Directory](overview.md).

Konfigurálásával kapcsolatos utasításokat jogcímszabályokat, lásd: [Jogcímszabályok konfigurálása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules). 






