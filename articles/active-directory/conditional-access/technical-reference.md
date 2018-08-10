---
title: Az Azure Active Directory feltételes hozzáférési beállítások referenciája |} A Microsoft Docs
description: A támogatott beállítások áttekintést kaphat az Azure Active Directory feltételes hozzáférési házirendben.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 0055968e4333f5ac7653a37fcd728b040024ee55
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629847"
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

- Az Azure Information Protection - [további](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- A Microsoft Office 365 Yammeren

- A Microsoft Office 365 Exchange online-hoz

- A Microsoft Office 365 SharePoint Online (onedrive vállalati verzió tartalmazza az üzleti és a Project Online-ban)

- Microsoft Power BI 

- A Microsoft Visual Studio Team Services

- Microsoft Teams


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


| Operációs rendszer                     | Böngészők                            | Támogatás     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, Edge, Chrome     | ![Jelölőnégyzet][1] |
| A Windows 8 / 8.1        | Internet Explorer, Chrome           | ![Jelölőnégyzet][1] |
| Windows 7              | Internet Explorer, Chrome           | ![Jelölőnégyzet][1] |
| iOS                    | A Safari, az Intune Managed Browser      | ![Jelölőnégyzet][1] |
| Android                | Chrome-ban, az Intune Managed Browser      | ![Jelölőnégyzet][1] |
| Windows Phone          | Internet Explorer, Edge             | ![Jelölőnégyzet][1] |
| Windows Server 2016    | Internet Explorer, Edge             | ![Jelölőnégyzet][1] |
| Windows Server 2016    | Chrome                              | Hamarosan elérhető |
| Windows Server 2012 R2 | Internet Explorer, Chrome           | ![Jelölőnégyzet][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome           | ![Jelölőnégyzet][1] |
| macOS                  | Chrome, a Safari                      | ![Jelölőnégyzet][1] |



#### <a name="chrome-support"></a>Chrome-támogatás

Támogatja a Chrome **Windows 10 alkotói frissítésével (1703-as)** vagy újabb telepítése [Ez a bővítmény](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

A bővítmény telepítéséről automatikusan Chrome böngészők, hozzon létre a következő beállításkulcsot:

|    |    |
|--- | ---|
|Útvonal | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
|Name (Név) | 1 |
|Típus | REG_SZ (String) |
|Adatok | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx

Támogatja a Chrome **7 és Windows 8.1**, hozza létre a következő beállításkulcsot:

|    |    |
|--- | ---|
|Útvonal | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|Name (Név) | 1 |
|Típus | REG_SZ (String) |
|Adatok | {"a minta": "https://device.login.microsoftonline.com","filter": {"Kiállító": {"CN": "Hozzáférés-MS-szervezet"}}}|

A böngészők eszközhitelesítés lehetővé teszi az eszköz azonosítani és egy házirend kialakításnak támogatja. Az eszköz-ellenőrzés sikertelen lesz, ha a böngészőt privát üzemmódban fut-e. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Támogatott a mobilalkalmazások és asztali ügyfelek

A feltételes hozzáférési szabályzat kiválaszthatja **mobilalkalmazások és asztali ügyfelek** ügyfélalkalmazás szerint.


![Hozzáférés vezérlése támogatott mobilalkalmazások és asztali ügyfelek](./media/technical-reference/06.png)


Ez a beállítás hatással van a hozzáférési kísérletek a következő mobilalkalmazások és asztali ügyfelek: 


|Ügyfélalkalmazások|Célként megadott szolgáltatás|Platform|
|---|---|---|
|Az Azure távoli alkalmazás|Az Azure távoli App Service-ben|A Windows 10, Windows 8.1, Windows 7, iOS, Android és Mac OS X|
|Dynamics CRM-alkalmazás|Dynamics CRM|A Windows 10, Windows 8.1, iOS és Android|
|Mail/naptár/személyek alkalmazást, az Outlook 2016, az Outlook 2013 (a modern hitelesítést)|Az Office 365 Exchange online-hoz|Windows 10|
|MFA- és helyszabályzat alkalmazásokhoz. Eszköz alapú szabályzatok nem támogatottak. |Bármely saját alkalmazások app service|Android és iOS|
|Microsoft Teams-szolgáltatások – ez vezérli a minden szolgáltatás, amely támogatja a Microsoft Teams és minden az ügyfélalkalmazások – Windows Desktop, iOS, Android, WP és webes ügyféllel|Microsoft Teams|A Windows 10, Windows 8.1, Windows 7, iOS, Android és macOS |
|Office 2016-alkalmazásokat, Office 2013 (a modern hitelesítést), a onedrive vállalati verzió szinkronizálása ügyfél (lásd: [megjegyzések](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Az Office 365 SharePoint Online|Windows 8.1, Windows 7|
|Office 2016-os, univerzális Office-alkalmazásokat, Office 2013 (a modern hitelesítést), onedrive-on a Szinkronizáló ügyfél (lásd: [megjegyzések](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), az Office-csoportok támogatása a jövőben tervezünk, SharePoint-alkalmazások támogatása a jövőben tervezünk|Az Office 365 SharePoint Online|Windows 10|
|Office 2016 MacOS (a Word, Excel, PowerPoint, csak a OneNote). A jövőben tervezünk OneDrive for Business-támogatás|Az Office 365 SharePoint Online|Mac OS X|
|Office-mobilalkalmazások|Az Office 365 SharePoint Online|Android, iOS|
|Office Yammer-alkalmazás|Az Office 365 Yammeren|A Windows 10, iOS, Android|
|Az Outlook 2016 (a macOS-hez készült Office)|Az Office 365 Exchange online-hoz|Mac OS X|
|Az Outlook 2016, az Outlook 2013 (a modern hitelesítést), a Skype vállalati verzió (a modern hitelesítést)|Az Office 365 Exchange online-hoz|Windows 8.1, Windows 7|
|Az Outlook mobilalkalmazás|Az Office 365 Exchange online-hoz|Android, iOS|
|A Power bi alkalmazás|Power bi szolgáltatásban|A Windows 10, Windows 8.1, Windows 7, Android és iOS|
|Skype Vállalati verzió|Az Office 365 Exchange online-hoz|Android, IOS |
|A Visual Studio Team Services-alkalmazás|Visual Studio Team Services|A Windows 10, Windows 8.1, Windows 7, iOS és Android|



## <a name="approved-client-app-requirement"></a>Jóváhagyott alkalmazás megkövetelése 

A feltételes hozzáférési házirendben megkövetelheti, hozzáférési kísérelje meg a kiválasztott felhőalapú alkalmazások igényeinek megfelelően történő áruházi vásárlást egy jóváhagyott ügyfélalkalmazás. 

![A jóváhagyott ügyfélalkalmazások hozzáférés](./media/technical-reference/21.png)

Ez a beállítás a következő ügyfél-alkalmazásokra vonatkozik:


- A Microsoft Intune Managed Browser
- Microsoft PowerBI
- A Microsoft számlázás
- A Microsoft indítója
- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- A Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- A Microsoft Skype vállalati verzió
- Microsoft StaffHub
- Microsoft Teams
- Microsoft Visio
- Microsoft Word



**Megjegyzések**

- A jóváhagyott ügyfélalkalmazások a Intune mobilalkalmazás-felügyeleti funkció támogatja.

- A **jóváhagyott ügyfélalkalmazás megkövetelése** követelmény:

    - Csak támogatja az iOS és Android for [eszköz platform feltétel](#device-platforms-condition).


## <a name="next-steps"></a>További lépések

- A feltételes hozzáférés áttekintéséhez lásd: [Mi az az Azure Active Directory feltételes hozzáférés?](../active-directory-conditional-access-azure-portal.md)
- Ha készen áll a feltételes hozzáférési szabályzatok konfigurálhatók az adott környezetben, tekintse meg a [ajánlott tanácsok az Azure Active Directory feltételes hozzáférés](best-practices.md).



<!--Image references-->
[1]: ./media/technical-reference/01.png


