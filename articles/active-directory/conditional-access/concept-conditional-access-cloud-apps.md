---
title: Felhőalapú alkalmazások és műveletek a feltételes hozzáférési szabályzatban – Azure Active Directory
description: Mik azok a felhőalapú alkalmazások vagy műveletek az Azure AD feltételes hozzáférési szabályzatában?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69bdd2d6825427597e9030a03aae7d219361ba25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671947"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Feltételes hozzáférés: Felhőalapú alkalmazások és műveletek

A felhőalapú alkalmazások vagy műveletek kulcsfontosságú jelzést jelentenek a feltételes hozzáférési szabályzatban. A feltételes hozzáférés házirendjei lehetővé teszik a rendszergazdák számára, hogy vezérlőket rendeljenek adott alkalmazásokhoz vagy műveletekhez.

- A rendszergazdák választhatnak az alkalmazások listájából, amelyek beépített Microsoft-alkalmazásokat és az [Azure AD-vel integrált alkalmazásokat](../manage-apps/what-is-application-management.md) tartalmaznak, beleértve a galériát, a nem galériát és az [alkalmazásproxyn](../manage-apps/what-is-application-proxy.md)keresztül közzétett alkalmazásokat.
- A rendszergazdák dönthetnek úgy, hogy nem egy felhőalapú alkalmazás, hanem egy felhasználói művelet alapján határozzák meg a házirendet. Az egyetlen támogatott művelet a Biztonsági adatok regisztrálása (előzetes verzió), amely lehetővé teszi a feltételes hozzáférés számára a [kombinált biztonsági adatok regisztrációs felületének](../authentication/howto-registration-mfa-sspr-combined.md)ellenőrzését.

![Feltételes hozzáférési szabályzat definiálása és felhőalapú alkalmazások megadása](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft felhőalapú alkalmazások

A microsoftos felhőalkalmazások közül sok szerepel a választható alkalmazások listájában. 

A rendszergazdák feltételes hozzáférési szabályzatot rendelhetnek a Microsoft következő felhőalapú alkalmazásaihoz. Egyes alkalmazások, például az Office 365 (előzetes verzió) és a Microsoft Azure Management több kapcsolódó gyermekalkalmazást vagy -szolgáltatást is tartalmaznak. Az alábbi lista nem teljes, és változhat.

- [Office 365 (előzetes verzió)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database és Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure információvédelem](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure Management](#microsoft-azure-management)
- Microsoft Azure előfizetés-kezelés
- Microsoft Cloud App Security
- Microsoft Commerce Tools hozzáférés-vezérlési portál
- A Microsoft Commerce Tools hitelesítési szolgáltatása
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune-regisztráció](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Keresés a Bingben
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Irodai sway
- Outlook Groups
- A Power BI szolgáltatás
- Project Online
- Skype Vállalati online verzió
- Virtuális magánhálózat (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (előzetes verzió)

Az Office 365 felhőalapú hatékonyságnövelő és együttműködési szolgáltatásokat nyújt, például az Exchange, a SharePoint és a Microsoft Teams szolgáltatást. Az Office 365 felhőszolgáltatásai mélyen integráltak a zökkenőmentes és együttműködő élmény biztosítása érdekében. Ez az integráció zavart okozhat a házirendek létrehozásakor, mivel egyes alkalmazások, például a Microsoft Teams függőséget okoznak másoktól, például a SharePointtól vagy az Exchange-től.

Az Office 365 (előzetes verzió) alkalmazás lehetővé teszi, hogy ezeket a szolgáltatásokat egyszerre célozza meg. Azt javasoljuk, hogy az új Office 365 (előzetes verzió) alkalmazást használja az egyes felhőalkalmazások célzása helyett. Az alkalmazások ezen csoportjának megcélzásával elkerülhetők az inkonzisztens házirendek és függőségek miatt esetlegesen felmerülő problémák.

A rendszergazdák úgy is dönthetnek, hogy bizonyos alkalmazásokat kizárnak a szabályzatból, ha szeretnék, ha az Office 365 (előzetes verzió) alkalmazást is beírják, és kizárják az általuk választott alkalmazásokat a szabályzatban.

Az Office 365 (előzetes verzió) ügyfélalkalmazásában található legfontosabb alkalmazások:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Office 365 keresési szolgáltatás
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype Vállalati online verzió
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure Management

A Microsoft Azure Management alkalmazás több mögöttes szolgáltatást is tartalmaz. 

   - Azure portál
   - Azure Resource Manager-szolgáltató
   - Klasszikus üzembe helyezési modell API-k
   - Azure PowerShell
   - Visual Studio-előfizetések rendszergazdai portálja
   - Azure DevOps
   - Az Azure Data Factory portálja

> [!NOTE]
> A Microsoft Azure Management alkalmazás az Azure PowerShellre vonatkozik, amely meghívja az Azure Resource Manager API-t. Nem vonatkozik az Azure AD PowerShell, amely felhívja a Microsoft Graph.

## <a name="other-applications"></a>Egyéb alkalmazások

A Microsoft-alkalmazások mellett a rendszergazdák bármilyen Azure AD regisztrált alkalmazást hozzáadhatnak a feltételes hozzáférés-szabályzatokhoz. Ezek az alkalmazások a következőklehetnek: 

- [Az Azure AD alkalmazásproxyn](../manage-apps/what-is-application-proxy.md) keresztül közzétett alkalmazások
- [A galériából hozzáadott alkalmazások](../manage-apps/add-application-portal.md)
- [A katalógusban nem található egyéni alkalmazások](../manage-apps/add-non-gallery-app.md)
- [Az alkalmazáskézbesítési vezérlőkön és hálózatokon közzétett örökölt alkalmazások](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Felhasználói műveletek

A felhasználói műveletek olyan feladatok, amelyeket a felhasználó elvégezhet. Az egyetlen jelenleg támogatott művelet a **Biztonsági adatok regisztrálása (előzetes verzió),** amely lehetővé teszi a feltételes hozzáférési házirend kényszerítése, ha a kombinált regisztrációra engedélyezett felhasználók megkísérlik regisztrálni biztonsági adataikat. További információ a [Kombinált biztonsági adatok regisztrálása (előzetes verzió)](../authentication/concept-registration-mfa-sspr-combined.md)című cikkben található.

## <a name="next-steps"></a>További lépések

- [Feltételes hozzáférés: Feltételek](concept-conditional-access-conditions.md)

- [Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)
- [Ügyfélalkalmazás-függőségek](service-dependencies.md)
