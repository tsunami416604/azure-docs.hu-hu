---
title: "Az Azure Active Directory feltételes hozzáférési beállítások útmutatója |} Microsoft Docs"
description: "A támogatott beállítások áttekintést kaphat az Azure Active Directory feltételes hozzáférési házirendben."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 9d9680b22864e95c5953ddac50f2179b147b1e21
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Az Azure Active Directory feltételes hozzáférési beállításainak ismertetése

Használható [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md) vezérlésére hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az erőforrásokat.   

Ez a cikk azt ismerteti támogatja a feltételes hozzáférési házirendet a következő konfigurációs beállítások: 

- Felhőalapú alkalmazások hozzárendelések

- Eszköz platform feltétel 

- Ügyfél alkalmazások feltétel

- Jóváhagyott alkalmazás követelményeinek


Ha ez nem csak a keresett információkat, hozzászólásban Ez a cikk végén.

## <a name="cloud-apps-assignments"></a>Felhőalapú alkalmazások hozzárendelések

Feltételes hozzáférési házirendekkel, szabályozhatja, hogy a felhasználók hogyan férhetnek hozzá a [felhőalapú alkalmazásokba](active-directory-conditional-access-azure-portal.md#who). A feltételes hozzáférési házirendjének konfigurálásakor kell legalább egy felhő-alkalmazás kiválasztása. 

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

Feltételes hozzáférési szabályzatot konfigurálhatja a házirendet, az operációs rendszer összekötését egy ügyfél az eszköz platform feltételt. Az Azure AD feltételes hozzáférés a következő eszközplatformokat támogatja:

- Android

- iOS

- Windows Phone

- Windows

- macOS


![A hozzáférési házirend, az ügyfél operációs rendszer kötése](./media/active-directory-conditional-access-technical-reference/41.png)





## <a name="client-apps-condition"></a>Ügyfél alkalmazások feltétel 

A feltételes hozzáférési szabályzatot konfigurálhat a [ügyfélalkalmazások](active-directory-conditional-access-azure-portal.md#client-apps) feltétel összekötését a házirendet, az ügyfél-alkalmazást, amelynek már kezdeményezte a hozzáférési kísérlet. Állítsa be az ügyfél adja meg, vagy letiltja a hozzáférést, ha egy hozzáférési kísérlet a következő típusú ügyfél alkalmazásokat az alkalmazások feltétel:

- Böngésző
- Hordozható és asztali alkalmazások

![Az ügyfél alkalmazások elérés](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Támogatott böngészők 

A feltételes hozzáférési házirend kiválaszthatja **böngészők** ügyfél alkalmazásként.

![Támogatott böngészők elérés](./media/active-directory-conditional-access-technical-reference/05.png)

Ez a beállítás minden böngészőben működik. Azonban kielégítéséhez egy eszköz házirend megfelelő eszköz követelmény, például a következő operációs rendszerek és böngészők támogatottak:


| Operációs rendszer                     | Böngészők                            | Támogatás     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, a peremhálózati, Chrome     | ![Jelölőnégyzet][1] |
| Windows 8 / 8.1        | Internet Explorer, a Chrome           | ![Jelölőnégyzet][1] |
| Windows 7              | Internet Explorer, a Chrome           | ![Jelölőnégyzet][1] |
| iOS                    | Safari, Intune által felügyelt böngésző      | ![Jelölőnégyzet][1] |
| Android                | Chrome, Intune által felügyelt böngésző      | ![Jelölőnégyzet][1] |
| Windows Phone          | Internet Explorer, a peremhálózati             | ![Jelölőnégyzet][1] |
| Windows Server 2016    | Internet Explorer, a peremhálózati             | ![Jelölőnégyzet][1] |
| Windows Server 2016    | Chrome                              | Hamarosan |
| Windows Server 2012 R2 | Internet Explorer, a Chrome           | ![Jelölőnégyzet][1] |
| Windows Server 2008 R2 | Internet Explorer, a Chrome           | ![Jelölőnégyzet][1] |
| macOS                  | Chrome, Safari                      | ![Jelölőnégyzet][1] |


> [!NOTE]
> A Chrome támogatáshoz használnia kell a Windows 10 Creators frissítés (verzió: 1703) vagy újabb.<br>
> Telepítése [ehhez a kiterjesztéshez](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

A böngészők támogatja az eszközhitelesítés, lehetővé teszi az eszköz azonosítását és ellenőrizni a házirend alapján. Az eszköz ellenőrzése sikertelen lesz, ha a böngésző fut privát üzemmódban. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Támogatott mobil alkalmazások és asztali ügyfelek

A feltételes hozzáférési házirend kiválaszthatja **mobilalkalmazások és asztali ügyfelek** ügyfél alkalmazásként.


![Elérés támogatott mobilalkalmazások vagy asztali ügyfelek](./media/active-directory-conditional-access-technical-reference/06.png)


Ez a beállítás hatással van a következő mobilalkalmazások és az asztali ügyfelek hozzáférések: 


|Ügyfél-alkalmazások|Célként megadott szolgáltatás|Platform|
|---|---|---|
|Azure távoli alkalmazás|Az Azure távoli App service|Windows 10, Windows 8.1, Windows 7, iOS, Android és Mac OS X|
|Dynamics CRM-alkalmazás|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS és Android|
|Mail/naptár/személyek app, Outlook 2016, az Outlook 2013 (modern hitelesítést)|Az Office 365 Exchange online-hoz|Windows 10|
|Többtényezős hitelesítés és a hely házirend-alkalmazásokhoz. Eszköz alapú szabályzatok nem támogatottak. |Bármely saját alkalmazások app service|Android és iOS|
|Microsoft csapatok szolgáltatások – ezen lehetőség összes szolgáltatás, amely támogatja a Microsoft Teams és minden az ügyfél alkalmazások – Windows asztali, iOS, Android, WP és webes ügyfél|Microsoft Teams|Windows 10, Windows 8.1, Windows 7, iOS, Android és macOS |
|Office 2016, Office 2013 (a modern hitelesítést), a onedrive vállalati verzió alkalmazások szinkronizálása ügyfél (lásd: [megjegyzések](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Az Office 365 SharePoint online szolgáltatáshoz|Windows 8.1, Windows 7|
|Office 2016-alkalmazások, univerzális Office-alkalmazásokat, Office 2013 (modern hitelesítést), a onedrive vállalati verzió szinkronizálási ügyfél (lásd: [megjegyzések](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), Office csoportok támogatási tervezünk-e a jövőben, SharePoint-alkalmazások támogatása tervezünk-e a jövőben|Az Office 365 SharePoint online szolgáltatáshoz|Windows 10|
|Office 2016 macOS (Word, Excel, PowerPoint, csak a OneNote). A OneDrive vállalati verziójának ügyfélszolgálatával tervezett a jövőben|Az Office 365 SharePoint online szolgáltatáshoz|Mac OS X|
|Office-mobilalkalmazások|Az Office 365 SharePoint online szolgáltatáshoz|Android, iOS|
|Office Yammer-alkalmazás|Az Office 365 Yammer|Windows 10, iOS, Android|
|Outlook 2016 (Office macOS)|Az Office 365 Exchange online-hoz|Mac OS X|
|Outlook 2016, az Outlook 2013 (modern hitelesítést), a Skype vállalati verzió (a modern hitelesítést)|Az Office 365 Exchange online-hoz|Windows 8.1, Windows 7|
|Outlook mobilalkalmazás|Az Office 365 Exchange online-hoz|Android, iOS|
|Power bi alkalmazásról. Az Androidhoz készült Power BI alkalmazás jelenleg nem támogatja a eszközalapú feltételes hozzáférés.|Power bi szolgáltatás|Windows 10, Windows 8.1, Windows 7 és iOS|
|A Skype vállalati verzió|Az Office 365 Exchange online-hoz|Android, IOS |
|A Visual Studio Team Services-alkalmazás|Visual Studio Team Services|Windows 10, Windows 8.1, Windows 7, iOS és Android|



## <a name="approved-client-app-requirement"></a>Jóváhagyott app követelmény 

A feltételes hozzáférési házirendben megkövetelheti, hogy hozzáférjen megkísérli a kijelölt felhőalapú alkalmazásokat kell tenni az ügyfél jóváhagyott alkalmazásokból. 

![Jóváhagyott alkalmazások elérés](./media/active-directory-conditional-access-technical-reference/21.png)

Ez a beállítás a következő ügyfél alkalmazásokra vonatkoznak:


- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft onedrive vállalati verzió
- Microsoft OneNote
- A Microsoft Outlook
- Microsoft Planner
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


