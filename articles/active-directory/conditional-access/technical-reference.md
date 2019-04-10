---
title: Az Azure Active Directory feltételes hozzáférési beállítások referenciája |} A Microsoft Docs
description: A támogatott beállítások áttekintést kaphat az Azure Active Directory feltételes hozzáférési házirendben.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e87a4c7ebafd8ddcfa54c87b189316b0ce98b0f
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359001"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Az Azure Active Directory feltételes hozzáférés beállításainak ismertetése

Használhat [Azure Active Directory (Azure AD) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) szabályozásához, hogyan engedéllyel rendelkező felhasználók hozzáférhetnek az erőforrásokhoz.

Ez a cikk a feltételes hozzáférési szabályzat az alábbi konfigurációs lehetőségeket a támogatási információkat nyújt:

- Felhőalapú alkalmazás-hozzárendelések
- Eszköz platform feltétel
- Ügyfél alkalmazások feltétel
- Jóváhagyott alkalmazás követelményeinek

Ha ez nem az Ön által keresett információkat,. Ez a cikk végén található Megjegyzés írása.

## <a name="cloud-apps-assignments"></a>Felhőalapú alkalmazás-hozzárendelések

Feltételes hozzáférési szabályzatok, szabályozhatja, hogy a felhasználók hogyan férhetnek hozzá a [felhőalkalmazások](conditions.md#cloud-apps). Egy feltételes hozzáférési házirendjének konfigurálásakor kell választania legalább egy felhőalkalmazást. 

![Válassza ki a felhőalkalmazásokat a szabályzat](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>A Microsoft felhőalapú alkalmazások

Feltételes hozzáférési szabályzatot rendelhet a következő felhőalapú alkalmazások a Microsoft:

- Azure Analysis Services
- Azure DevOps
- Az Azure SQL Database és az adatraktár - [további](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights-elemzési
- A Microsoft Azure Information Protection - [további](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- A Microsoft Azure Management - [további](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- A Microsoft Azure RemoteApp
- A Microsoft Azure-előfizetések kezelése
- Microsoft Cloud App Security
- A Microsoft kereskedelmi eszközök, Access Control portál
- A Microsoft kereskedelmi eszközök hitelesítési szolgáltatás
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- A Microsoft Intune-regisztráció
- A Microsoft Planner
- Microsoft Power BI
- Microsoft PowerApps
- A Microsoft Bing keresés
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Az Office 365 Exchange online-hoz
- Az Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook-csoportok
- Project Online
- Skype Vállalati online verzió
- Virtuális magánhálózat (VPN)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Más alkalmazások

A Microsoft felhőalapú alkalmazások mellett feltételes hozzáférési szabályzatot rendelhet a következő típusú felhőalapú alkalmazásokat:

- Az Azure AD-hoz csatlakoztatott alkalmazások
- Előre integrált összevont szoftver (saas biztosított) alkalmazás
- Jelszavas egyszeri bejelentkezés (SSO) használó alkalmazások
- Az üzletági alkalmazások
- Azure AD Application proxyt használó alkalmazások

## <a name="device-platform-condition"></a>Eszköz platform feltétel

A feltételes hozzáférési szabályzatot konfigurálhatja a eszköz-platform feltételt, és elősegítsék a házirendet, az operációs rendszer az ügyfélen. Az Azure AD feltételes hozzáférés a következő eszközplatformokat támogatja:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![A hozzáférési szabályzat az ügyfél operációs rendszer kötése](./media/technical-reference/41.png)

## <a name="client-apps-condition"></a>Ügyfél alkalmazások feltétel

A feltételes hozzáférési szabályzatot konfigurálhat a [ügyfélalkalmazás](conditions.md#client-apps) elősegítsék a házirendet, az ügyfélalkalmazás, amely egy hozzáférési kísérlet kezdeményezte az állapotot. Állítsa be az ügyfél megadásáról vagy letiltja a hozzáférést, ha a hozzáférési kísérlet történik az ügyfélalkalmazások a következő típusú alkalmazások feltétel:

- Böngésző
- A mobilalkalmazások és asztali alkalmazások

![Hozzáférés vezérlése az ügyfélalkalmazások](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Támogatott böngészők

A feltételes hozzáférési szabályzat kiválaszthatja **böngészők** ügyfélalkalmazás szerint.

![Támogatott böngészők hozzáférés](./media/technical-reference/05.png)

Ez a beállítás minden böngésző együttműködik. Azonban teljesítéséhez egy szabályzatot, például a szabályzatnak megfelelő eszköz követelmény, a következő operációs rendszerek és böngészők támogatottak:

| Operációs rendszer                     | Böngészők                                      |
| :--                    | :--                                           |
| Windows 10             | Az Internet Explorer, a Microsoft Edge, Chrome-ban     |
| Windows 8 / 8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | A Safari, a Microsoft Edge, Intune által felügyelt böngészőben |
| Android                | A Microsoft Edge, Chrome, Intune által felügyelt böngészőben |
| Windows Phone          | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, a Safari                                |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Miért látok egy tanúsítvány kérése a böngészőben

A Windows 7, iOS, Android és macOS Azure ad-ben azonosítja az eszközt, amikor az eszköz regisztrálva van az Azure ad-vel kiosztott ügyféltanúsítvány használatával.  Egy felhasználó először jelentkezik be a böngészőből a felhasználót a rendszer kér, válassza ki a tanúsítványt. A felhasználónak ki kell választania ezt a tanúsítványt a böngésző használata előtt.

#### <a name="chrome-support"></a>Chrome-támogatás

Támogatja a Chrome **Windows 10 alkotói frissítésével (1703-as)** vagy újabb telepítése [Ez a bővítmény](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

A bővítmény telepítéséről automatikusan Chrome böngészők, hozzon létre a következő beállításkulcsot:

|    |    |
| --- | --- |
| Útvonal | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name (Név) | 1 |
| Typo | REG_SZ (String) |
| Adatok | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx |

Támogatja a Chrome **7 és Windows 8.1**, hozza létre a következő beállításkulcsot:

|    |    |
| --- | --- |
| Útvonal | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name (Név) | 1 |
| Typo | REG_SZ (String) |
| Adatok | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

A böngészők eszközhitelesítés lehetővé teszi az eszköz azonosítani és egy házirend kialakításnak támogatja. Az eszköz-ellenőrzés sikertelen lesz, ha a böngészőt privát üzemmódban fut-e.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Támogatott a mobilalkalmazások és asztali ügyfelek

A feltételes hozzáférési szabályzat kiválaszthatja **mobilalkalmazások és asztali ügyfelek** ügyfélalkalmazás szerint.

![Hozzáférés vezérlése támogatott mobilalkalmazások és asztali ügyfelek](./media/technical-reference/06.png)

Ez a beállítás hatással van a hozzáférési kísérletek a következő mobilalkalmazások és asztali ügyfelek:

| Ügyfélalkalmazások | Célként megadott szolgáltatás | Platform |
| --- | --- | --- |
| Az Azure távoli alkalmazás | Az Azure távoli App Service-ben | A Windows 10, Windows 8.1, Windows 7, iOS, Android és macOS |
| Dynamics CRM-alkalmazás | Dynamics CRM | A Windows 10, Windows 8.1, iOS és Android |
| Mail/naptár/személyek alkalmazást, az Outlook 2016, az Outlook 2013 (a modern hitelesítést)| Az Office 365 Exchange online-hoz | Windows 10 |
| MFA- és helyszabályzat alkalmazásokhoz. Eszköz alapú szabályzatok nem támogatottak.| Bármely saját alkalmazások app service| Android és iOS |
| Microsoft Teams-szolgáltatások – ez vezérli a minden szolgáltatás, amely támogatja a Microsoft Teams és minden az ügyfélalkalmazások – Windows Desktop, iOS, Android, WP és webes ügyféllel | Microsoft Teams | A Windows 10, Windows 8.1, Windows 7, iOS, Android és macOS |
| Office 2016-alkalmazásokat, Office 2013 (a modern hitelesítést), a onedrive vállalati verzió szinkronizálása ügyfél (lásd: [megjegyzések](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Az Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Office 2016-os, univerzális Office-alkalmazásokat, Office 2013 (a modern hitelesítést), onedrive-on a Szinkronizáló ügyfél (lásd: [megjegyzések](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), az Office-csoportok támogatása a jövőben tervezünk, SharePoint-alkalmazások támogatása a jövőben tervezünk | Az Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint és OneNote csak). A jövőben tervezünk OneDrive for Business-támogatás| Az Office 365 SharePoint Online| macOS|
| Office 2019| Az Office 365 SharePoint Online | Windows 10, macOS |
| Office-mobilalkalmazások | Az Office 365 SharePoint Online | Android, iOS |
| Office Yammer-alkalmazás | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Az Office 365 SharePoint Online | Windows 10, macOS |
| Az Outlook 2016 (a macOS-hez készült Office) | Az Office 365 Exchange online-hoz | macOS |
| Az Outlook 2016, az Outlook 2013 (a modern hitelesítést), a Skype vállalati verzió (a modern hitelesítést) | Az Office 365 Exchange online-hoz | Windows 8.1, Windows 7 |
| Az Outlook mobilalkalmazás | Az Office 365 Exchange online-hoz | Android, iOS |
| Power BI-alkalmazás | A Power BI szolgáltatásban | A Windows 10, Windows 8.1, Windows 7, Android és iOS |
| Skype Vállalati verzió | Az Office 365 Exchange online-hoz| Android, IOS |
| A Visual Studio Team Services-alkalmazás | Visual Studio Team Services | A Windows 10, Windows 8.1, Windows 7, iOS és Android |

## <a name="support-for-legacy-authentication"></a>Az örökölt hitelesítés támogatása

Kiválasztásával **más ügyfelek**, megadhat egy feltételt, amely hatással van a levelezési protokollok IMAP, MAPI, a jelenléti pontra Irányíthatja, SMTP és ne a modern hitelesítést használó régebbi Office-alkalmazások például az alapszintű hitelesítést használó alkalmazásokhoz.  

![Más ügyfelek](./media/technical-reference/11.png)

További információkért lásd: [ügyfélalkalmazás](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Jóváhagyott alkalmazás megkövetelése

A feltételes hozzáférési házirendben megkövetelheti, hozzáférési kísérelje meg a kiválasztott felhőalapú alkalmazások igényeinek megfelelően történő áruházi vásárlást egy jóváhagyott ügyfélalkalmazás. 

![A jóváhagyott ügyfélalkalmazások hozzáférés](./media/technical-reference/21.png)

Ez a beállítás a következő ügyfél-alkalmazásokra vonatkozik:

- Microsoft Azure Information Protection
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- A Microsoft Intune Managed Browser
- A Microsoft számlázás
- Microsoft Kaizala
- A Microsoft indítója
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- A Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- A Microsoft Skype vállalati verzió
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Megjegyzések**

- A jóváhagyott ügyfélalkalmazások a Intune mobilalkalmazás-felügyeleti funkció támogatja.
- A **jóváhagyott ügyfélalkalmazás megkövetelése** követelmény:
   - Csak támogatja az iOS és Android for [eszköz platform feltétel](#device-platform-condition).

## <a name="app-protection-policy-requirement"></a>Alkalmazás alkalmazásvédelmi szabályzat megkövetelése 

A feltételes hozzáférési szabályzat lehet szükség, az alkalmazásvédelmi szabályzat megtalálható az ügyfélalkalmazás előtt érhető el a kiválasztott felhőalapú alkalmazásokhoz. 

![Hozzáférés vezérlése az alkalmazásvédelmi szabályzat](./media/technical-reference/22.png)

Ez a beállítás a következő ügyfél-alkalmazásokra vonatkozik:

- Microsoft OneDrive
- Microsoft Outlook

**Megjegyzések**

- Alkalmazásvédelmi szabályzat alkalmazások támogatják az Intune mobilalkalmazás szolgáltatást a védelem.
- A **megkövetelése alkalmazásvédelmi szabályzat** követelmények:
    - Csak támogatja az iOS és Android for [eszköz platform feltétel](#device-platform-condition).

## <a name="next-steps"></a>További lépések

- A feltételes hozzáférés áttekintéséhez lásd: [Mi az az Azure Active Directory feltételes hozzáférés?](../active-directory-conditional-access-azure-portal.md)
- Ha készen áll a feltételes hozzáférési szabályzatok konfigurálhatók az adott környezetben, tekintse meg a [ajánlott tanácsok az Azure Active Directory feltételes hozzáférés](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
