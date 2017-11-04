---
title: "Technikai útmutató az Azure Active Directory feltételes hozzáférés |} Microsoft Docs"
description: "Ismerje meg, hogyan használhatja a feltételes hozzáférés-vezérlés az Azure Active Directoryban. Adja meg a feltételeket a felhasználók hitelesítéséhez és az alkalmazásokhoz való hozzáférés szabályozása. Ha a megadott feltételek teljesülnek, a felhasználók hitelesítése, és hozzáférést kap az alkalmazáshoz."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 8cde8e10b6789e930ea1deda2f4aa0d4a5e7ea97
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Technikai útmutató az Azure Active Directory feltételes hozzáférés

Használhat [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md) annak finomhangolásához, hogy hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az erőforrásokat.  

Ez a témakör az alábbi konfigurációs elemek egy feltételes hozzáférési házirend támogatási információkat: 

- Felhőalapú alkalmazások hozzárendelések

- Eszköz platform feltétel 

- Ügyfél alkalmazások feltétel

- Jóváhagyott alkalmazás követelményeinek



## <a name="cloud-apps-assignments"></a>Felhőalapú alkalmazások hozzárendelések

A feltételes hozzáférési házirendjének konfigurálásakor kell [válassza ki a felhőalapú alkalmazásokat, amelyek a házirend-](active-directory-conditional-access-azure-portal.md#who). 

![Válassza ki a felhőalapú alkalmazások, a házirend](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>A Microsoft felhőalapú alkalmazásokhoz

A feltételes hozzáférési házirend a Microsoft hozzárendelheti a következő felhőalapú alkalmazásokat:

- Az Azure Information Protection - [további](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Azure RemoteApp

- Microsoft Dynamics 365

- A Microsoft Office 365 Yammer

- A Microsoft Office 365 Exchange online-hoz

- A Microsoft Office 365 SharePoint online-hoz (tartalmazza a onedrive vállalati verzió)

- Microsoft Power BI 

- A Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>Más alkalmazások 

Mellett a Microsoft felhőalapú alkalmazásokat feltételes hozzáférési házirendet rendelhet a következő típusú felhőalapú alkalmazásokat:

- Az Azure AD-kompatibilis alkalmazásokat

- A szolgáltatott szoftverként (SaaS) kezelése előre integrált összevont szoftver

- Jelszó az egyszeri bejelentkezés (SSO) használó alkalmazások

- Az üzletági alkalmazások

- Az Azure AD-alkalmazásproxy használó alkalmazások


## <a name="device-platform-condition"></a>Eszköz platform feltétel

Feltételes hozzáférési szabályzatot konfigurálhatja a házirendet, az operációs rendszer összekötését egy ügyfél az eszköz platform feltételt.

![A hozzáférési házirend, az ügyfél operációs rendszer kötése](./media/active-directory-conditional-access-technical-reference/41.png)

Az Azure AD feltételes hozzáférés a következő eszközplatformokat támogatja:

- Android

- iOS

- Windows Phone

- Windows

- macOS (előzetes verzió)



## <a name="client-apps-condition"></a>Ügyfél alkalmazások feltétel 

A feltételes hozzáférési házirendjének konfigurálásakor is [ügyfél alkalmazások kiválasztása](active-directory-conditional-access-azure-portal.md#client-apps) ügyfél app feltételét. Állítsa be az ügyfél adja meg, vagy letiltja a hozzáférést, ha egy hozzáférési kísérlet a következő típusú ügyfél alkalmazásokat az alkalmazások feltétel:

- Böngésző
- Hordozható és asztali alkalmazások

![Az ügyfél alkalmazások elérés](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Támogatott böngészők 

Böngésző-hozzáférés kezelése a használatával a **böngésző** a feltételes hozzáférési szabályzat beállítása. Hozzáférés csak akkor, ha a hozzáférési kísérlet egy támogatott böngésző. Ha egy hozzáférési kísérlet nem támogatott böngészőt, a kísérlet le van tiltva.

![Támogatott böngészők elérés](./media/active-directory-conditional-access-technical-reference/05.png)

A feltételes hozzáférési házirend az alábbi böngészők támogatottak: 


| Operációs rendszer                     | Böngészők                    | Támogatás     |
| :--                    | :--                         | :-:         |
| Windows 10             | Internet Explorer, a peremhálózati     | ![Jelölőnégyzet][1] |
| Windows 10             | Chrome                      | ![Jelölőnégyzet][1] |
| Windows 8 / 8.1        | Internet Explorer, a Chrome   | ![Jelölőnégyzet][1] |
| Windows 7              | Internet Explorer, a Chrome   | ![Jelölőnégyzet][1] |
| iOS                    | Safari, Intune által felügyelt böngésző                      | ![Jelölőnégyzet][1] |
| Android                | Chrome, Intune által felügyelt böngésző                      | ![Jelölőnégyzet][1] |
| Windows Phone          | Internet Explorer, a peremhálózati     | ![Jelölőnégyzet][1] |
| Windows Server 2016    | Internet Explorer, a peremhálózati     | ![Jelölőnégyzet][1] |
| Windows Server 2016    | Chrome                      | Hamarosan |
| Windows Server 2012 R2 | Internet Explorer, a Chrome   | ![Jelölőnégyzet][1] |
| Windows Server 2008 R2 | Internet Explorer, a Chrome   | ![Jelölőnégyzet][1] |
| macOS                  | Safari                      | ![Jelölőnégyzet][1] |
| macOS                  | Chrome                      | Hamarosan |

> [!NOTE]
> A Chrome támogatáshoz használnia kell a Windows 10 Creators frissítés (verzió: 1703) vagy újabb.<br>
> Telepítése [ehhez a kiterjesztéshez](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="supported-mobile-applications-and-desktop-clients"></a>Támogatott mobil alkalmazások és asztali ügyfelek

Alkalmazás és az ügyfél-hozzáférési használatával szabályozhatja a **mobilalkalmazások és asztali ügyfelek** a feltételes hozzáférési házirend beállítása. Hozzáférés csak akkor, ha a hozzáférési kísérlet egy támogatott mobilalkalmazás vagy asztali ügyfél. Ha egy hozzáférési kísérlet egy nem támogatott alkalmazást vagy az ügyfél, a kísérlet le van tiltva.

![Elérés támogatott mobilalkalmazások vagy asztali ügyfelek](./media/active-directory-conditional-access-technical-reference/06.png)

A következő mobilalkalmazások és asztali ügyfelek támogatja a feltételes hozzáférés az Office 365 és más Azure AD-csatlakoztatott szolgáltatásalkalmazások:


| Ügyfél-alkalmazások| Célként megadott szolgáltatás| Platform |
| --- | --- | --- |
| Azure távoli alkalmazás| Az Azure távoli App service| Windows 10, Windows 8.1, Windows 7, iOS, Android és Mac OS X|
| Dynamics CRM-alkalmazás| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS és Android|
| Mail/naptár/személyek app, Outlook 2016, az Outlook 2013 (modern hitelesítést)| Az Office 365 Exchange online-hoz| Windows 10|
| Többtényezős hitelesítés és a hely házirend-alkalmazásokhoz. Eszköz alapú szabályzatok nem támogatottak.| Bármely saját alkalmazások app service| Android és iOS|
| Microsoft csapatok szolgáltatások – ezen lehetőség összes szolgáltatás, amely támogatja a Microsoft Teams és minden az ügyfél alkalmazások – Windows asztali, iOS, Android, WP és webes ügyfél| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS és Android|
| Office 2016, Office 2013 (a modern hitelesítést), a onedrive vállalati verzió alkalmazások szinkronizálása ügyfél (lásd: [megjegyzések](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Az Office 365 SharePoint online szolgáltatáshoz| Windows 8.1, Windows 7|
| Office 2016-alkalmazások, univerzális Office-alkalmazásokat, Office 2013 (modern hitelesítést), a onedrive vállalati verzió szinkronizálási ügyfél (lásd: [megjegyzések](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), Office csoportok támogatási tervezünk-e a jövőben, SharePoint-alkalmazások támogatása tervezünk-e a jövőben| Az Office 365 SharePoint online szolgáltatáshoz| Windows 10|
| Office 2016 macOS (Word, Excel, PowerPoint, csak a OneNote). A OneDrive vállalati verziójának ügyfélszolgálatával tervezett a jövőben| Az Office 365 SharePoint online szolgáltatáshoz| Mac OS X|
| Office-mobilalkalmazások| Az Office 365 SharePoint online szolgáltatáshoz| iOS, Android|
| Office Yammer-alkalmazás| Az Office 365 Yammer| Windows 10, iOS, Android|
| Outlook 2016 (Office macOS)| Az Office 365 Exchange online-hoz| Mac OS X|
| Outlook 2016, az Outlook 2013 (modern hitelesítést), a Skype vállalati verzió (a modern hitelesítést)| Az Office 365 Exchange online-hoz| Windows 8.1, Windows 7|
| Outlook mobilalkalmazás| Az Office 365 Exchange online-hoz| iOS|
| Power bi alkalmazásról. Az Androidhoz készült Power BI alkalmazás jelenleg nem támogatja a eszközalapú feltételes hozzáférés.| Power bi szolgáltatás| Windows 10, Windows 8.1, Windows 7 és iOS|
| A Skype vállalati verzió| Az Office 365 Exchange online-hoz| Android, IOS|
| A Visual Studio Team Services-alkalmazás| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS és Android|



## <a name="approved-client-app-requirement"></a>Jóváhagyott app követelmény 

Ügyfélkapcsolatok használatával szabályozhatja a **jóváhagyott ügyfélalkalmazás szükséges** a feltételes hozzáférési szabályzat beállítása. Hozzáférés csak akkor, ha a kapcsolat kísérlet által jóváhagyott alkalmazások.

![Jóváhagyott alkalmazások elérés](./media/active-directory-conditional-access-technical-reference/21.png)

A következő ügyfél-alkalmazásokat is használható a jóváhagyott alkalmazás követelményeinek:

- Microsoft Excel

- Microsoft onedrive vállalati verzió

- A Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype vállalati verzió

- Microsoft Teams

- A Microsoft Visio

- A Microsoft Word


**Megjegyzések**

- A jóváhagyott alkalmazások támogatják az Intune mobilalkalmazás-felügyeleti funkció.

- A **jóváhagyott ügyfélalkalmazás szükséges** követelmény:

    - Csak támogatja az iOS és az Android [eszköz platform feltétel](#device-platforms-condition).

    - Nem támogatja a **böngésző** választás a [ügyfél alkalmazások feltétel](#supported-browsers).
    
    - Írja felül a **mobilalkalmazások és asztali ügyfelek** választás a [ügyfél alkalmazások feltétel](#supported-mobile-apps-and-desktop-clients) amikor ez a lehetőség van kiválasztva.


## <a name="next-steps"></a>Következő lépések

- A feltételes hozzáférés áttekintéséért lásd: [feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md).
- Ha készen áll a konfigurálhat feltételes hozzáférési szabályzatokat a környezetben, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


