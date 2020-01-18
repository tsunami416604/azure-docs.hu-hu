---
title: Ügyfélalkalmazások a feltételes hozzáférési házirendben – Azure Active Directory
description: ''
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb291f33d2b757ca381e1d675ddc386c78a55d52
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170835"
---
# <a name="conditional-access-cloud-apps-and-actions"></a>Feltételes hozzáférés: felhőalapú alkalmazások és műveletek

A felhőalapú alkalmazások vagy műveletek egy feltételes hozzáférési szabályzat kulcsfontosságú részét képezik. A feltételes hozzáférési házirendek lehetővé teszik a rendszergazdák számára, hogy bizonyos alkalmazásokhoz vagy műveletekhez rendeljenek vezérlőket. 

- A rendszergazdák választhatnak a beépített Microsoft-alkalmazásokat és bármely [Azure ad integrált alkalmazást](../manage-apps/what-is-application-management.md) , beleértve a katalógust, a nem katalógust és az [Application proxyn](../manage-apps/what-is-application-proxy.md)keresztül közzétett alkalmazásokat is.
- A rendszergazdák dönthetnek úgy, hogy nem felhőalapú alkalmazáson, hanem felhasználói műveleten alapuló szabályzatot határoznak meg. Az egyetlen támogatott művelet a biztonsági adatok (előzetes verzió) regisztrálása, amely lehetővé teszi, hogy a feltételes hozzáférés a [kombinált biztonsági információk regisztrációs felülete](../authentication/howto-registration-mfa-sspr-combined.md)körüli ellenőrzéseket kényszerítse ki.

![Feltételes hozzáférési szabályzat definiálása és felhőalapú alkalmazások megadása](./media/concept-conditional-access-cloud-apps/conditional-access-define-policy-specify-cloud-apps.png)

## <a name="microsoft-cloud-applications"></a>Microsoft Cloud-alkalmazások

Számos meglévő Microsoft Cloud-alkalmazás szerepel azon alkalmazások listáján, amelyek közül választhat. 

A rendszergazdák feltételes hozzáférési szabályzatot rendelhetnek a Microsoft következő felhőalapú alkalmazásaihoz. Egyes alkalmazások, például az Office 365 (előzetes verzió) és a Microsoft Azure felügyelet több kapcsolódó alárendelt alkalmazást vagy szolgáltatást tartalmaznak. A következő lista nem teljes, és változhat.

- [Office 365 (előzetes verzió)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database és adattárház](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights Analitika
- [Microsoft Azure Information Protection](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure-kezelés](#microsoft-azure-management)
- Előfizetés-kezelés Microsoft Azure
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control portál
- Microsoft Commerce Tools hitelesítési szolgáltatás
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune regisztráció](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Search a Bingben
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office-Sway
- Outlook Groups
- A Power BI szolgáltatás
- Project Online
- Skype Vállalati online verzió
- Virtuális magánhálózat (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (előzetes verzió)

Az Office 365 olyan felhőalapú hatékonyságnövelő és együttműködési szolgáltatásokat nyújt, mint például az Exchange, a SharePoint és a Microsoft teams. Az Office 365 Cloud Services szorosan integrált a zökkenőmentes és együttműködő élmény biztosítása érdekében. Ez az integráció zavart okozhat a házirendek létrehozásakor, mivel egyes alkalmazások, például a Microsoft-csapatok függőségekkel rendelkeznek másokkal, például a SharePoint vagy az Exchange használatával.

Az Office 365 (előzetes verzió) alkalmazás lehetővé teszi, hogy egyszerre lehessen megcélozni ezeket a szolgáltatásokat. Javasoljuk, hogy az új Office 365 (előzetes verzió) alkalmazást az egyes felhőalapú alkalmazások célzása helyett használja. Az alkalmazások csoportjának megcélzása segít elkerülni a nem konzisztens házirendek és függőségek miatt felmerülő problémákat.

A rendszergazdák dönthetnek úgy, hogy kizárják az adott alkalmazásokat a szabályzatból, ha azokat az Office 365 (előzetes verzió) alkalmazással kívánják kizárni, és kizárják a házirendben választott alkalmazásokat.

Az Office 365 (előzetes verzió) ügyfélalkalmazás részét képező legfontosabb alkalmazások:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Office 365-portál
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Office 365 Search Service
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - OneDrive
   - PowerApps
   - Skype Vállalati online verzió
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure-kezelés

A Microsoft Azure felügyeleti alkalmazás több mögöttes szolgáltatást is tartalmaz. 

   - Azure Portal
   - Azure Resource Manager szolgáltató
   - Klasszikus üzembe helyezési modell API-k
   - Azure PowerShell
   - Visual Studio-előfizetések felügyeleti portál
   - Azure DevOps
   - Azure Data Factory portál

> [!NOTE]
> A Microsoft Azure felügyeleti alkalmazás a Azure PowerShellra vonatkozik, amely meghívja a Azure Resource Manager API-t. Ez nem vonatkozik az Azure AD PowerShellre, amely meghívja a Microsoft Graph.

## <a name="other-applications"></a>Egyéb alkalmazások

A Microsoft-alkalmazások mellett a rendszergazdák bármilyen Azure AD-beli regisztrált alkalmazást hozzáadhatnak a feltételes hozzáférési házirendekhez. Ezek az alkalmazások a következőket tartalmazhatják: 

- Az [Azure ad Application proxy](../manage-apps/what-is-application-proxy.md) használatával közzétett alkalmazások
- [A gyűjteményből hozzáadott alkalmazások](../manage-apps/add-application-portal.md)
- [A katalógusban nem szereplő egyéni alkalmazások](../manage-apps/add-non-gallery-app.md)
- [Az App Delivery Controllers és Networks szolgáltatáson keresztül közzétett örökölt alkalmazások](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Felhasználói műveletek

A felhasználói műveletek olyan feladatok, amelyeket a felhasználók elvégezhetnek. Az egyetlen jelenleg támogatott művelet a **biztonsági adatok (előzetes verzió) regisztrálása**, amely lehetővé teszi, hogy a feltételes hozzáférési szabályzat kikényszerítse azokat a felhasználókat, akik a kombinált regisztrációhoz engedélyezve vannak a biztonsági információik regisztrálásához. További információt a következő cikkben talál: [kombinált biztonsági információk regisztrálása (előzetes verzió)](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférési szabályzat összetevői](concept-conditional-access-policies.md)
- [Ügyfélalkalmazás függőségei](service-dependencies.md)
- [Microsoft Intune: MFA megkövetelése az eszközök regisztrálásához](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
