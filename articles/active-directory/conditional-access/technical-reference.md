---
title: A feltételes hozzáférési beállítások referenciája Azure Active Directory | Microsoft Docs
description: Tekintse át a Azure Active Directory feltételes hozzáférési szabályzat támogatott beállításait.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 216316249197071bdd7a175dfc5339caeddee50a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086831"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Azure Active Directory feltételes hozzáférési beállítások ismertetése

[Azure Active Directory (Azure ad) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) használatával szabályozhatja, hogy a jogosult felhasználók hozzáférhessenek az erőforrásokhoz.

Ez a cikk a feltételes hozzáférési szabályzat következő konfigurációs lehetőségeivel kapcsolatos támogatási információkat tartalmazza:

- Cloud Applications-hozzárendelések
- Eszköz platformjának feltétele
- Ügyfélalkalmazások feltétele
- Jóváhagyott ügyfélalkalmazás-követelmény

Ha nem az Ön által keresett információ, akkor a cikk végén hagyjon megjegyzést.

## <a name="cloud-apps-assignments"></a>Cloud apps-hozzárendelések

A feltételes hozzáférési szabályzatokkal szabályozhatja, hogy a felhasználók hogyan férhessenek hozzá a [felhőalapú alkalmazásokhoz](conditions.md#cloud-apps-and-actions). Feltételes hozzáférési szabályzat konfigurálásakor ki kell választania legalább egy felhőalapú alkalmazást. 

![A Szabályzathoz tartozó felhőalapú alkalmazások kiválasztása](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Microsoft Cloud-alkalmazások

Feltételes hozzáférési szabályzatot rendelhet a Microsoft következő felhőalapú alkalmazásaihoz:

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database és adattárház – [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analitika
- Microsoft Azure Information Protection – [További információ](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure-kezelés – [További információ](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Előfizetés-kezelés Microsoft Azure
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control portál
- Microsoft Commerce Tools hitelesítési szolgáltatás
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- A Microsoft Intune-regisztráció
- A Microsoft Planner
- Microsoft PowerApps
- Microsoft Search a Bingben
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook-csoportok
- Power BI szolgáltatás
- Project Online
- Skype Vállalati online verzió
- Virtuális magánhálózat (VPN)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Egyéb alkalmazások

A Microsoft Cloud Apps mellett feltételes hozzáférési szabályzatot is hozzárendelhet a következő típusú felhőalapú alkalmazásokhoz:

- Azure AD-hez csatlakoztatott alkalmazások
- Előre integrált összevont szolgáltatott szoftver (SaaS) alkalmazás
- Jelszó egyszeri bejelentkezést (SSO) használó alkalmazások
- Az üzletági alkalmazások
- Az Azure AD Application Proxyt használó alkalmazások

## <a name="device-platform-condition"></a>Eszköz platformjának feltétele

A feltételes hozzáférési szabályzatban konfigurálhatja az eszköz platformjának feltételeit, hogy a házirendet egy ügyfél operációs rendszeréhez kösse. Az Azure AD feltételes hozzáférés a következő eszköz platformokat támogatja:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![A hozzáférési szabályzat összekapcsolása az ügyfél operációs rendszerével](./media/technical-reference/41.png)

Ha letiltja az örökölt hitelesítést a **többi ügyfél** feltételének használatával, beállíthatja az eszköz platformjának feltételeit is.

## <a name="client-apps-condition"></a>Ügyfélalkalmazások állapota

A feltételes hozzáférési szabályzatban beállíthatja az [ügyfélalkalmazások](conditions.md#client-apps) feltételét, hogy a szabályzatot a hozzáférési kísérletet kezdeményező ügyfélalkalmazás számára kösse. Állítsa be a Client apps-feltételt, hogy engedélyezze vagy tiltsa le a hozzáférést, amikor a következő típusú ügyfélalkalmazások hozzáférési kísérletet végeznek:

- Browser
- Mobile apps és asztali alkalmazások

![Az ügyfélalkalmazások hozzáférésének szabályozása](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Támogatott böngészők

A feltételes hozzáférési szabályzatban megadhatja a **böngészők** ügyfélprogramként lehetőséget.

![A támogatott böngészők hozzáférésének vezérlése](./media/technical-reference/05.png)

Ez a beállítás minden böngészővel működik. Ahhoz azonban, hogy kielégítse az eszköz házirendjét, például a megfelelő eszköz követelményeit, a következő operációs rendszerek és böngészők támogatottak:

| OS                     | Böngészők                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge, Internet Explorer, Chrome       |
| Windows 8/8,1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Microsoft Edge, Intune Managed Browser, Safari  |
| Android                | Microsoft Edge, Intune Managed Browser, Chrome  |
| Windows Phone          | Microsoft Edge, Internet Explorer               |
| A Windows Server 2019    | Microsoft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Miért jelenik meg a tanúsítvány Rákérdezés a böngészőben

Windows 7 rendszeren az iOS, az Android és a macOS Azure AD az eszközt az Azure AD-vel való regisztráláskor kiépített ügyféltanúsítvány használatával azonosítja.  Amikor a felhasználó először jelentkezik be a böngészőben, a rendszer a felhasználótól kéri a tanúsítvány kiválasztását. A felhasználónak a böngésző használata előtt ki kell választania ezt a tanúsítványt.

#### <a name="chrome-support"></a>Chrome-támogatás

A **Windows 10-es Creators Update (1703-es verzió)** vagy újabb verziók Chrome-támogatásához telepítse a [Windows 10-es fiókok bővítményt](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Erre a bővítményre akkor van szükség, ha egy feltételes hozzáférési szabályzat az eszközre vonatkozó adatokat igényel.

A bővítmény Chrome böngészőkbe való automatikus telepítéséhez hozza létre a következő beállításkulcsot:

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (String) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; HTTPS\://clients2.Google.com/Service/Update2/CRX |

A **Windows 8,1 és 7 rendszerhez**készült Chrome-támogatáshoz hozza létre a következő beállításkulcsot:

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (String) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Ezek a böngészők támogatják az eszköz hitelesítését, amely lehetővé teszi az eszköz azonosítását és érvényesítését egy szabályzattal szemben. Az eszköz-ellenőrzés sikertelen, ha a böngésző privát módban fut.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Támogatott mobil alkalmazások és asztali ügyfelek

A feltételes hozzáférési szabályzatban a **Mobile apps és az asztali ügyfelek** is kiválaszthatók ügyfélalkalmazásként.

![A támogatott Mobile apps-vagy asztali ügyfelek hozzáférésének vezérlése](./media/technical-reference/06.png)

Ez a beállítás hatással van a következő Mobile apps és asztali ügyfelek által kezdeményezett hozzáférési kísérletekre:

| Ügyfélalkalmazások | Cél szolgáltatás | Platform |
| --- | --- | --- |
| Dynamics CRM-alkalmazás | Dynamics CRM | Windows 10, Windows 8,1, iOS és Android |
| Mail/Calendar/People app, Outlook 2016, Outlook 2013 (modern hitelesítéssel)| Office 365 Exchange Online | Windows 10 |
| MFA-és tartózkodási hely szabályzata alkalmazások számára. Az eszközökön alapuló házirendek nem támogatottak.| A saját alkalmazások app Service| Android és iOS |
| Microsoft Teams Services – Ez vezérli a Microsoft Teams és az összes ügyfélalkalmazás használatát támogató összes szolgáltatást – Windows Desktop, iOS, Android, WP és Web Client | Microsoft Teams | Windows 10, Windows 8,1, Windows 7, iOS, Android és macOS |
| Office 2016-alkalmazások, Office 2013 (modern hitelesítéssel), OneDrive-szinkronizálási ügyfél (lásd a [megjegyzéseket](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8,1, Windows 7 |
| Office 2016-alkalmazások, univerzális Office-alkalmazások, Office 2013 (modern hitelesítéssel), OneDrive-szinkronizálási ügyfél (lásd a [megjegyzéseket](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), az Office-csoportok támogatását a jövőben tervezték, a SharePoint-alkalmazások támogatása a jövőben tervezett | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, csak OneNote). A jövőben tervezett OneDrive for Business-támogatás| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Office Mobile-alkalmazások | Office 365 SharePoint Online | Android, iOS |
| Office Yammer-alkalmazás | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (macOS-es iroda) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (modern hitelesítéssel), Skype vállalati verzió (modern hitelesítéssel) | Office 365 Exchange Online | Windows 8,1, Windows 7 |
| Outlook Mobile alkalmazás | Office 365 Exchange Online | Android, iOS |
| Power BI alkalmazás | Power BI szolgáltatás | Windows 10, Windows 8,1, Windows 7, Android és iOS |
| Skype Vállalati verzió | Office 365 Exchange Online| Android, IOS |
| Visual Studio Team Services-alkalmazás | Visual Studio Team Services | Windows 10, Windows 8,1, Windows 7, iOS és Android |

## <a name="support-for-legacy-authentication"></a>Örökölt hitelesítés támogatása

**Más ügyfelek**kiválasztásával megadhat egy olyan feltételt, amely az alapszintű hitelesítést használó alkalmazásokat, például az IMAP, a MAPI, a pop, az SMTP és a régebbi Office-alkalmazásokat használja, amelyek nem használnak modern hitelesítést.  

![Egyéb ügyfelek](./media/technical-reference/11.png)

További információ: [ügyfélalkalmazások](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Jóváhagyott ügyfélalkalmazás-követelmény

A feltételes hozzáférési házirendben megkövetelheti, hogy a kiválasztott felhőalapú alkalmazásokhoz való hozzáférési kísérletet egy jóváhagyott ügyfélalkalmazás alapján kell végrehajtani. 

![A jóváhagyott ügyfélalkalmazások hozzáférésének vezérlése](./media/technical-reference/21.png)

Ez a beállítás a következő ügyfélalkalmazások esetében érvényes:

- Microsoft Azure Information Protection
- Microsoft-foglalások
- Microsoft-Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
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
- Microsoft Skype vállalati verzió
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft-Yammer

**Megjegyzések**

- A jóváhagyott ügyfélalkalmazások támogatják az Intune Mobile Application Management szolgáltatást.
- A **jóváhagyott ügyfélalkalmazás megkövetelése** :
   - A csak az iOS és az Android for [Device platform feltételeit](#device-platform-condition)támogatja.

## <a name="app-protection-policy-requirement"></a>Alkalmazás-védelmi szabályzat követelménye 

A feltételes hozzáférési házirendben megkövetelheti, hogy az alkalmazás védelmi szabályzata elérhető legyen az ügyfélalkalmazás számára, mielőtt a kiválasztott felhőalapú alkalmazások hozzáférhessenek a hozzáféréshez. 

![Hozzáférés vezérlése az App Protection-házirenddel](./media/technical-reference/22.png)

Ez a beállítás a következő ügyfélalkalmazások esetében érvényes:

- Microsoft-Cortana
- Microsoft Edge
- Microsoft OneDrive
- Microsoft Outlook
- A Microsoft Planner

**Megjegyzések**

- Az App Protection-házirend alkalmazásai támogatják az Intune Mobile Application Management szolgáltatást a házirend-védelemmel.
- Az **alkalmazás-védelmi házirend követelményeinek megkövetelése** :
    - A csak az iOS és az Android for [Device platform feltételeit](#device-platform-condition)támogatja.

## <a name="next-steps"></a>További lépések

- A feltételes hozzáférés áttekintése: [Mi a feltételes hozzáférés a Azure Active Directoryban?](../active-directory-conditional-access-azure-portal.md)
- Ha készen áll a feltételes hozzáférési szabályzatok konfigurálására a környezetben, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
