---
title: "Alkalmazások, engedélyek és jóváhagyás az Azure Active Directoryban | Microsoft Docs"
description: "Az Azure AD Connect integrálja a helyszíni címtárakat az Azure Active Directoryval. Így közös identitást biztosíthat az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz."
keywords: "az Azure AD bemutatása, alkalmazások, mi az Azure AD Connect, az Active Directory telepítése"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/20/2017
ms.author: billmath
ms.reviewer: jesakowi
ms.translationtype: Human Translation
ms.sourcegitcommit: 095497656a1c6ed1fe5322b6015de750d8fd6f15
ms.openlocfilehash: 3706ed729a525526a415d3a3367e6b8245ead812
ms.contentlocale: hu-hu
ms.lasthandoff: 12/06/2016

---

# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Alkalmazások, engedélyek és jóváhagyás az Azure Active Directoryban
Az Azure Active Directoryban hozzáadhat alkalmazásokat a címtárhoz.  Az alkalmazások az alkalmazás típusától függően eltérőek lehetnek.  Az alkalmazások megtekintéséhez a klasszikus portálon válasszon ki egy címtárat, és válasszon alkalmazásokat.

![](media/active-directory-apps-permissions-consent/apps1.png)


## <a name="types-of-apps"></a>Alkalmazástípusok

1. **Egybérlős alkalmazások** </br>
    - **Egybérlős alkalmazások** – Gyakran üzletági (line-of-business, LOB) alkalmazásokként utalnak rájuk. Ilyen alkalmazásról akkor beszélünk, amikor egy szervezet saját alkalmazást fejleszt, és azt szeretné, hogy a felhasználói be tudjanak rá jelentkezni.
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **Alkalmazásproxy-alkalmazások** – Amikor közzétesz egy helyszíni alkalmazást az Azure AD-alkalmazásproxyval, egy egybérlős alkalmazást regisztrál a bérlőben (az alkalmazásproxy-szolgáltatás mellett). Ez az alkalmazás képviseli a helyszíni alkalmazást minden felhőbeli interakció (például hitelesítés) során. (Az alkalmazásproxy használatához Basic vagy magasabb szintű Azure AD-licenc szükséges.)


2. **Több-bérlős alkalmazások**
    - **Mások által jóváhagyott több-bérlős alkalmazások** – hasonlók, mint „a szervezet által fejlesztett egybérlős alkalmazások”. A fő különbség (az alkalmazás belső logikáján kívül) az, hogy más bérlők felhasználói is jóváhagyhatják az alkalmazást, és be is jelentkezhetnek.</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **Mások által fejlesztett több-bérlős alkalmazások, amelyeket a Contoso jóváhagyhat**. (Röviden „jóváhagyott alkalmazások”.) Ezen alkalmazások a „szervezet által fejlesztett több-bérlős alkalmazások” ellentettjei. Ha egy másik szervezet fejleszt egy több-bérlős alkalmazást, az Ön üzleti szervezetének felhasználói jóváhagyhatják az alkalmazást, és be is jelentkezhetnek.
    - **Belső Microsoft-alkalmazások** – A Microsoft szolgáltatásait képviselő alkalmazások. Az alkalmazások használatát a szolgáltatásra való regisztrációval lehet jóváhagyni. Néha különleges felhasználói felület és logika jellemez bizonyos belső alkalmazásokat, amelyeknek a hatása az alkalmazások hozzáférésére vonatkozó szabályzatokban is érvényesül.</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **Előre integrált alkalmazások** – Az Azure AD alkalmazáskatalógusában elérhető alkalmazások, amelyeket hozzáadhat a címtárhoz, hogy egyszeri bejelentkezést (és esetenként jogosultságkiosztást) biztosíthasson a népszerű SaaS-alkalmazásokhoz.
    - **Azure AD egyszeri bejelentkezés**: „Valódi” egyszeri bejelentkezés (SSO) olyan alkalmazások számára, amelyek integrálhatók az Azure AD-vel egy támogatott bejelentkezési protokollon, például az SAML 2.0-n vagy az OpenID Connecten keresztül. A varázsló végigvezeti ennek beállításán.
    - **Jelszavas egyszeri bejelentkezés**: Az Azure AD biztonságosan tárolja a felhasználó alkalmazásra vonatkozó hitelesítő adatait, amelyeket az Azure AD App Access böngészőbővítmény ad meg a bejelentkezési űrlapon. Ez a bejelentkezési módszer „jelszótárolásként” is ismert.

## <a name="permissions"></a>Engedélyek

Egy alkalmazás regisztrálásakor az alkalmazásregisztrációt végrehajtó felhasználó (vagyis a fejlesztő) határozza meg azon engedélyeket és erőforrásokat, amelyeket az alkalmazásnak el kell érnie. (Maguk az erőforrások más alkalmazásokként vannak meghatározva.) Egy levélolvasó alkalmazás fejlesztője megadhatja például, hogy az alkalmazásnak a „Postaládák elérése bejelentkezett felhasználóként” engedélyre van szüksége az „Office 365 Exchange Online” erőforrásban:
    
![](media/active-directory-apps-permissions-consent/apps6.png)

Ahhoz, hogy egy alkalmazás (az ügyfél) adott engedélyt igényelhessen egy másik alkalmazástól (az erőforrástól), az erőforrás-alkalmazás fejlesztője határozza meg a meglévő engedélyeket. A példánkban a Microsoft, az „Office 365 Exchange Online” erőforrás-alkalmazás tulajdonosa meghatározta a „Postaládák elérése bejelentkezett felhasználóként” nevű engedélyt.

Az engedélyek meghatározásakor az alkalmazás fejlesztőjének meg kell adnia, hogy az engedélyt jóvá lehet-e hagyni, vagy pedig rendszergazdai jóváhagyásra van-e szükség. A fejlesztők így lehetővé tehetik a felhasználók számára alacsony szintű engedélyeket kérő alkalmazásaik jóváhagyását, magasabb szintű engedélyek esetén pedig rendszergazdai jóváhagyást követelhetnek meg. Az „Azure Active Directory” erőforrás-alkalmazás például úgy lett meghatározva, hogy a felhasználók maguk is jóváhagyhatják az alkalmazásokat, ha csak korlátozott írásvédett engedélyeket kérnek.  A teljes olvasási és minden írási engedélyhez azonban rendszergazdai jóváhagyás szükséges.

Mivel a natív ügyfelek nincsenek hitelesítve, a natív ügyfélalkalmazásként meghatározott alkalmazások csak delegált engedélyeket kérhetnek. Ez azt jelenti, hogy a tokenek beszerzését egy tényleges felhasználónak kell intéznie. A webes alkalmazásoknak és webes API-knak (bizalmas ügyfeleknek) mindig hitelesíteniük kell az Azure AD-vel, amikor hozzáférési tokent igényelnek. Ezek az ügyfelek csak az alkalmazásra vonatkozó engedélyeket is kérhetnek. Ilyen eset például, amikor egy háttérszolgáltatásnak hitelesítenie kell magát egy másik háttérszolgáltatás előtt. A csak az alkalmazásra vonatkozó engedélyeket kérő alkalmazások mindig rendszergazdai jóváhagyást igényelnek.

Összefoglalva:



- Az alkalmazás (ügyfél) határozza meg azon engedélyeket, amelyekre szüksége van a többi alkalmazáshoz (erőforráshoz).
- Az alkalmazás (erőforrás) határozza meg, hogy milyen engedélyek tehetők közzé a többi alkalmazás (ügyfél) számára.
- Az engedély lehet csak az alkalmazásra vonatkozó vagy delegált engedély is.
- A delegált engedély megjelölhető „felhasználói jóváhagyást engedélyező” vagy „rendszergazdai jóváhagyást igénylő” engedélyként.
- Egy alkalmazás működhet ügyfélként (ha kijelenti, hogy engedélyekre van szüksége egy erőforráshoz), erőforrásként (ha megnevezi az általa közzétett engedélyeket) vagy mindkettőként.

## <a name="controls"></a>Vezérlők

Alább az ilyen működési módokhoz elérhető különböző rendszergazdai vezérlők listája látható. A rendszergazdai vezérlők a klasszikus portálon a címtár konfigurálási szakaszánál érhetők el.

![](media/active-directory-apps-permissions-consent/apps7.png)

Az Azure Portalon a vezérlőket a **Felhasználói beállítások** **Kezelés** menüpontjában találja meg.

![](media/active-directory-apps-permissions-consent/apps11.png)



- Szabályozhatja, hogy a felhasználók jóváhagyhatnak-e alkalmazásokat:

A klasszikus portálon válassza a **Users may give applications permissions to access their data**
![](media/active-directory-apps-permissions-consent/apps8.png) (A felhasználók engedélyt adhatnak az alkalmazásoknak az adataik elérésére) elemet.

Az Azure Portalon válassza **A felhasználók engedélyezhetik alkalmazások számára az adataikhoz való hozzáférést** elemet.
![](media/active-directory-apps-permissions-consent/apps12.png)



- Szabályozhatja, hogy a felhasználók regisztrálhatják-e saját egybérlős LOB-alkalmazásaikat. A klasszikus portálon válassza a **Users may add integrated applications**
![](media/active-directory-apps-permissions-consent/apps9.png) (A felhasználók hozzáadhatnak integrált alkalmazásokat) elemet.

Az Azure Portalon válassza **A felhasználók engedélyezhetik alkalmazások számára az adataikhoz való hozzáférést** elemet.
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>A regisztrálható alkalmazások köre még akkor is korlátozva van, ha engedélyezi a felhasználók számára az egybérlős LOB-alkalmazások regisztrálását.  
>A korlátozások vonatkoznak például olyan fejlesztőkre, akik nem címtár-rendszergazdák.
>
>- A felhasználók nem alakíthatnak át egy egybérlős alkalmazást több-bérlőssé.
>- Egybérlős LOB-alkalmazások regisztrálásakor a felhasználók nem kérhetnek csak az alkalmazásra vonatkozó engedélyeket más alkalmazásokhoz.
>- Egybérlős LOB-alkalmazások regisztrálásakor a felhasználók nem kérhetnek delegált engedélyeket más alkalmazásokhoz, ha az engedélyekhez rendszergazdai jóváhagyásra van szükség.
>- A felhasználók nem módosíthatnak olyan alkalmazásokat, amelyeknek nem a tulajdonosaik.



- Szabályozhatja, hogy a felhasználók hozzáadhatnak-e előre integrált, jelszavas egyszeri bejelentkezést (más néven „jelszótárolást”) használó alkalmazásokat. ![](media/active-directory-apps-permissions-consent/apps10.png)



- Szabályozhatja, hogy az alkalmazások milyen feltételek esetén férhetők hozzá (azaz feltételes hozzáférést is megadhat). Ügyeljen arra, hogy ez az ügyfélalkalmazásra és az erőforrás-alkalmazásra egyaránt vonatkozik. Tegyük fel például, hogy olyan feltételes hozzáférési szabályzatot állít be, amely alapján az „Office 365 Exchange Online” alkalmazás csak a szabályzatnak megfelelő gépekről érhető el.  A szabályzat akkor is érvényesül, ha egy felhasználó olyan ügyfélalkalmazást próbál használni, amely engedélyeket kért az Exchange Online-hoz.



- Láthatja, hogy mely alkalmazásokat hagytak jóvá, és mely alkalmazásokat használják éppen.

1.  Amikor egy felhasználó jóváhagy egy alkalmazást, a rendszer létrehoz egy ServicePrincipal objektumot a bérlőben. A ServicePrincipal létrehozásának ténye megjelenik a naplózási jelentésben.
2.  A felhasználói bejelentkezési tevékenységről szóló jelentések tájékoztatják arról, hogy a felhasználó mely alkalmazásba jelentkezik be. 

## <a name="example"></a>Példa

Példaként a „FabrikamMail for Office 365” alkalmazást használjuk. Ön észleli, hogy a bérlő felhasználói bejelentkeznek az alkalmazásba. A „FabrikamMail” egy levélolvasó alkalmazás Android rendszerhez, amelyet a „Fabrikam, Inc.” tett közzé. Az alkalmazás így a „mások által fejlesztett több-bérlős alkalmazások, amelyeket a Contoso jóváhagyhat” típusba tartozik.

Ha a felhasználók számára engedélyezett a jóváhagyás, a rendszer a jóváhagyásukat kéri az első bejelentkezéskor: ![](media/active-directory-apps-permissions-consent/apps14.png)

Az „Access your mailboxes” (Postaládák hozzáférése) az „Office 365 Exchange Online” (vagyis az Exchange) által közzétett „Postaládák elérése bejelentkezett felhasználóként” engedély felhasználóknak megjelenő jóváhagyási karakterlánca.

Az engedélyeket az Exchange (az erőforrás) ServicePrincipal objektumának ellenőrzésével tekintheti meg. Az objektum hozzáadására az Office 365-re való regisztráláskor került sor. A ServicePrincipal objektumot tekintheti az alkalmazás különböző beállítások és konfigurációk rögzítésére szolgáló egyik „példányának” a bérlőben.  Az objektumot a `Get-AzureADServicePrincipal` paranccsal tekintheti meg a PowerShellben.

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

Ha a felhasználó az „Accept” (Elfogadás) gombra kattint, a rendszer kezdeményezi a jóváhagyást. Először is létrehozza a „FabrikamMail for Office 365” ServicePrincipal objektumát a bérlőben. A ServicePrincipal nagyjából így néz ki:

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

Az alkalmazás jóváhagyása egy Oauth2PermissionGrant hivatkozást hoz létre az alábbiak között:
  
- a felhasználói objektum;
- az ügyfélalkalmazás ServicePrincipalName (SPN) eleme;
- az erőforrás-alkalmazás ServicePrincipalName (SPN) eleme;
- az erőforrás-alkalmazásban található engedélyek.  

A FabrikamMail esetében ez nagyjából a következők szerint néz ki:

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(A **ClientId** a FabrikamMail ServicePrincipal objektumának azonosítója (amelyet a rendszer éppen létrehozott), a **PrincipalId** a felhasználói objektum azonosítója (a jóváhagyást adó felhasználóé), a **ResourceId** az Exchange ServicePrincipal objektumának azonosítója, a Scope pedig az Exchange azon engedélye, amelyet a felhasználó jóváhagy.)

Ha a felhasználók nem adhatnak maguktól jóváhagyást, egy olyan képernyő jelenik meg, amely közli, hogy engedélyre van szükség.


