---
title: Alkalmazások megtekintése a Azure Active Directory Bérlővel az Identitáskezelés kezeléséhez
description: Ismerje meg, hogyan tekintheti meg az összes alkalmazást az Azure Active Directory Bérlővel az Identitáskezelés kezeléséhez.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: d83b69ea7c2a4459d30c4ceb90f62ab6a1a10cc2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648174"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Alkalmazások megtekintése az Azure AD-Bérlővel az Identitáskezelés kezeléséhez
Az [alkalmazások felügyeletének](view-applications-portal.md) gyors üzembe helyezési sorozata az alapismereteket mutatja be. Ez azt mutatja be, hogyan tekintheti meg az összes alkalmazást az Azure AD-Bérlővel az Identitáskezelés kezeléséhez. Ez a cikk egy kicsit mélyebben ismerteti a megtalált alkalmazások típusait.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Miért jelenik meg egy adott alkalmazás az összes alkalmazás listán?
Ha az **összes alkalmazásra** szűri, a **minden alkalmazás** **lista** megjeleníti a bérlő összes egyszerű szolgáltatásnév objektumát. A szolgáltatás egyszerű objektumai több módon is megjelenhetnek a listában:
- Ha bármely alkalmazást hozzáad az alkalmazás-katalógusból, beleértve a következőket:
   - **Azure ad-Enterprise Applications** – az Azure ad portál **vállalati alkalmazások** lehetőségével hozzáadott alkalmazások a bérlőhöz. Általában az SAML szabvány használatával integrált alkalmazások.
   - **Azure ad-Alkalmazásregisztrációk** – a bérlőhöz hozzáadott alkalmazások az Azure ad portálon található **Alkalmazásregisztrációk** lehetőséggel. Az Open ID csatlakozási és OAuth szabványokat használó, általában egyéni fejlesztésű alkalmazások.
   - **Alkalmazásproxy-alkalmazások** – egy olyan alkalmazás, amely a helyszíni környezetben fut, és biztonságos egyszeri bejelentkezést kíván biztosítani a külsőleg
- A szolgáltatásba való regisztráláskor vagy a szolgáltatásba való beléptetéskor egy, az Azure Active Directory-vel integrált, külső gyártótól származó alkalmazás. Az egyik példa a [Smartsheet](https://app.smartsheet.com/b/home) vagy a [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- Microsoft-alkalmazások, például Microsoft 365.
- Új alkalmazás regisztrációjának hozzáadásakor hozzon létre egy egyéni fejlesztésű alkalmazást az [alkalmazás-beállításjegyzék](../develop/quickstart-register-app.md) használatával
- Új alkalmazás regisztrációjának hozzáadásakor hozzon létre egy egyéni fejlesztésű alkalmazást a [v 2.0 alkalmazás regisztrációs portálján](../develop/quickstart-register-app.md)
- Alkalmazás hozzáadásakor a Visual Studio [ASP.net-hitelesítési módszereivel](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) vagy [csatlakoztatott szolgáltatásaival](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) fejleszt
- Egyszerű szolgáltatás létrehozásakor az [Azure ad PowerShell-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) használatával
- Ha az [alkalmazáshoz való beleegyezett](../develop/howto-convert-app-to-be-multi-tenant.md) , hogy rendszergazdaként használja a bérlő adatait
- Amikor egy [felhasználó beleegyezett az alkalmazásba](../develop/howto-convert-app-to-be-multi-tenant.md) , hogy a bérlőn lévő adatait használja
- Ha olyan szolgáltatásokat engedélyez, amelyek a bérlőn tárolt adatait tárolják. Az egyik példa a jelszó alaphelyzetbe állítása, amelyet egyszerű szolgáltatásnévként kell tárolni a jelszó-visszaállítási házirend biztonságos tárolásához.

További információ arról, hogy miként és miért történik az alkalmazások hozzáadása a címtárhoz: [Hogyan lesznek hozzáadva az alkalmazások az Azure ad](../develop/active-directory-how-applications-are-added.md)-hez.

## <a name="next-steps"></a>Következő lépések
[Alkalmazások kezelése Azure Active Directory](what-is-application-management.md)