---
title: Az Azure biztonsági szolgáltatásai, amelyek segítenek az identitáskezelésben | Microsoft dokumentumok
description: " Ez a cikk áttekintést nyújt az Azure alapvető biztonsági funkcióiról, amelyek segítenek az identitáskezelésben. A Microsoft identitás- és hozzáférés-kezelési megoldásai segítenek az informatikai szolgáltatásoknak az alkalmazásokhoz és erőforrásokhoz való hozzáférés védelmében a vállalati adatközpontban és a felhőben, lehetővé téve további szintű érvényesítést, például a többtényezős hitelesítést és a feltételes hozzáférést Politikák. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: f61b6193a0d2082296a17128b41d7220f9b7e05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565893"
---
# <a name="azure-identity-management-security-overview"></a>Az Azure identitáskezelési biztonság – áttekintés

 Az identitáskezelés a rendszerbiztonsági tagok hitelesítésének és [engedélyezésének folyamata.](/windows/security/identity-protection/access-control/security-principals) Ez magában foglalja az adott megbízókkal (identitásokkal) kapcsolatos információk ellenőrzését is. A rendszerbiztonsági tagok (identitások) lehetnek szolgáltatások, alkalmazások, felhasználók, csoportok stb. A Microsoft identitás- és hozzáférés-kezelési megoldásai segítenek az informatikai szolgáltatásoknak az alkalmazásokhoz és erőforrásokhoz való hozzáférés védelmében a vállalati adatközpontban és a felhőben. Ez a védelem további szintű érvényesítést tesz lehetővé, például a többtényezős hitelesítést és a feltételes hozzáférésházirendeket. A gyanús tevékenységek figyelése a fejlett biztonsági jelentések, naplózás és riasztás révén segít csökkenteni a potenciális biztonsági problémákat. [Az Azure Active Directory Premium](/azure/active-directory/active-directory-editions) egyszeri bejelentkezést (SSO) biztosít több ezer felhőalapú szoftver nek szolgáltatásként (SaaS) és hozzáférést a helyszíni webalkalmazásokhoz.
 
Az Azure Active Directory (Azure AD) biztonsági előnyeinek kihasználásával a következőket teheti:

* A hibrid vállalat minden felhasználójához létrehozhat és kezelhet egy-egy identitást, így a felhasználók, csoportok és eszközök folyamatosan szinkronizálva lesznek. 
* SSO-hozzáférést biztosíthat alkalmazásaihoz, köztük több ezer előre integrált SaaS-alkalmazáshoz.
* Szabályalapú többtényezős hitelesítés kikényszerítésével fokozhatja az alkalmazás-hozzáférés biztonságát a helyszíni és a felhőalkalmazásokban.
* Biztonságos távoli hozzáférést biztosít a helyszíni webalkalmazásokhoz az Azure AD alkalmazásproxyn keresztül.

A cikk célja, hogy áttekintést nyújtson az Azure alapvető biztonsági funkcióiról, amelyek segítenek az identitáskezelésben. Mi is nyújt linkeket cikkeket, amelyek részletezik az egyes funkciók, így többet.  

A cikk a következő alapvető Azure Identity-felügyeleti képességekre összpontosít:

* Egyszeri bejelentkezés
* Fordított proxy
* Multi-Factor Authentication
* Szerepköralapú hozzáférés-vezérlés (RBAC)
* Biztonsági figyelési, riasztások és gépi tanuláson alapuló jelentések
* A fogyasztók identitásának és hozzáférésének kezelése
* Eszközregisztráció
* Privileged Identity Management
* Identitásvédelem
* Hibrid identitáskezelés/Azure AD-csatlakozás
* Azure AD hozzáférési felülvizsgálatok

## <a name="single-sign-on"></a>Egyszeri bejelentkezés

Az egyszeri bejelentkezés azt jelenti, hogy hozzáférhet az összes alkalmazáshoz és erőforráshoz, amelyet üzleti tevékenységhez kell végeznie, ha csak egyszer jelentkezik be egyetlen felhasználói fiók használatával. Miután bejelentkezett, hozzáférhet az összes szükséges alkalmazáshoz anélkül, hogy másodszor is hitelesítenie kellene (például be kell írnia egy jelszót).

Számos szervezet támaszkodik saas-alkalmazásokra, például az Office 365,Box és a Salesforce a felhasználók hatékonyságára. Korábban az informatikai személyzetnek egyénileg kellett létrehoznia és frissítenie a felhasználói fiókokat az egyes SaaS-alkalmazásokban, és a felhasználóknak minden Egyes SaaS-alkalmazáshoz meg kellett emlékezniük egy jelszóra.

Az Azure AD kiterjeszti a helyszíni Active Directory-környezeteket a felhőbe, lehetővé téve a felhasználók számára, hogy elsődleges szervezeti fiókjukkal jelentkezzenek be nem csak a tartományhoz csatlakozott eszközökre és vállalati erőforrásokba, hanem az összes szükséges webes és SaaS-alkalmazásra is. munkájukért.

A felhasználóknak nem csak több felhasználónevet és jelszót kell kezelniük, hanem a szervezeti csoportok és az alkalmazotti állapotuk alapján automatikusan kiépítheti vagy megszüntetheti az alkalmazáshozzáférést. Az Azure AD bevezeti a biztonsági és hozzáférés-irányítási vezérlőket, amelyekkel központilag kezelheti a felhasználók hozzáférését az SaaS-alkalmazások között.

További információ:

* [Egyszeri bejelentkezés – áttekintés](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Az Azure Active Directory egyszeri bejelentkezésének integrálása saas-alkalmazásokkal](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md)

## <a name="reverse-proxy"></a>Fordított proxy

Az Azure AD alkalmazásproxy lehetővé teszi a helyszíni alkalmazások, például a [SharePoint-webhelyek,](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) az [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)és az [IIS-alapú](https://www.iis.net/)alkalmazások közzétételét a magánhálózaton belül, és biztonságos hozzáférést biztosít a hálózaton kívüli felhasználók számára. Az alkalmazásproxy távoli hozzáférést és egyszeri szolgáltatást biztosít a helyszíni webalkalmazások számos típusához az Azure AD által támogatott több ezer SaaS-alkalmazással. Az alkalmazottak saját eszközeiken jelentkezhetnek be az alkalmazásokba, és ezzel a felhőalapú proxyval hitelesíthetik magukat.

További információ:

* [Az Azure AD alkalmazásproxy engedélyezése](/azure/active-directory/manage-apps/application-proxy-enable)
* [Alkalmazások közzététele az Azure AD-alkalmazásproxyval](/azure/active-directory/active-directory-application-proxy-publish)
* [Egyszeri bejelentkezés alkalmazásproxyval](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés sel](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Az Azure többtényezős hitelesítés olyan hitelesítési módszer, amely több ellenőrzési módszert igényel, és egy kritikus második biztonsági réteget ad hozzá a felhasználói bejelentkezésekhez és tranzakciókhoz. A többtényezős hitelesítés segít biztosítani az adatokhoz és alkalmazásokhoz való hozzáférést, miközben kielégíti a felhasználói igényeket egy egyszerű bejelentkezési folyamathoz. Erős hitelesítést biztosít számos ellenőrzési lehetőségen keresztül: telefonhívások, szöveges üzenetek, mobilalkalmazás-értesítések vagy ellenőrző kódok és külső OAuth tokenek.

További információ:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Az Azure Multi-Factor Authentication működése](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

Az RBAC az Azure Resource Managerre épülő engedélyezési rendszer, amely részletes hozzáférés-kezelési lehetőségeket nyújt az Azure-beli erőforrásokhoz. Az RBAC lehetővé teszi a felhasználók hozzáférésének részletes szabályozását. Korlátozhatja például, hogy egy felhasználó csak a virtuális hálózatokat kezelje, egy másik felhasználó pedig egy erőforráscsoport összes erőforrását. Az Azure számos beépített szerepkört biztosít, amelyeket használhat. Az alábbiakban négy alapvető beépített szerepkört sorolunk fel. Az első három minden erőforrástípusra alkalmazható.

További információ:

* [Mi az a szerepköralapú hozzáférés-vezérlés (RBAC)?](/azure/role-based-access-control/overview)
* [Beépített szerepkörök Azure-erőforrásokhoz](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Biztonsági figyelési, riasztások és gépi tanuláson alapuló jelentések

A nem konzisztens hozzáférési mintákat azonosító biztonsági figyelési, riasztások és gépi tanuláson alapuló jelentések segíthetnek a vállalkozás védelmében. Az Azure AD-hozzáférési és használati jelentések használatával betekintést nyerhet a szervezet címtárának integritásába és biztonságába. Ezzel az információval a címtár-rendszergazda jobban meghatározhatja, hogy hol lehetnek a lehetséges biztonsági kockázatok, hogy megfelelően tervezhessék meg ezeket a kockázatokat.

Az Azure Portalon a jelentések a következő kategóriákba sorolhatók:

* **Anomáliajelentések:** Olyan bejelentkezési eseményeket tartalmaznak, amelyeket rendellenesnek találtunk. Célunk, hogy tudatosítsuk Önben az ilyen tevékenységeket, és lehetővé tegyük annak megállapítását, hogy egy esemény gyanús-e.
* **Integrált alkalmazásjelentések:** Betekintést nyújt a felhőalapú alkalmazások szervezetben történő használatba kerülésébe. Az Azure AD több ezer felhőalkalmazással kínál integrációt.
* **Hibajelentések**: Jelezze azokat a hibákat, amelyek akkor fordulhatnak elő, amikor fiókokat épít külső alkalmazásokba.
* **Felhasználóspecifikus jelentések:** Egy adott felhasználó eszközbejelentkezési tevékenységének adatainak megjelenítése.
* **Tevékenységnaplók:** Az elmúlt 24 órában, az elmúlt 7 napban vagy az elmúlt 30 napban naplózott összes ellenőrzött esemény, valamint a csoporttevékenység-módosítások, valamint a jelszó-visszaállítási és regisztrációs tevékenység rekordját tartalmazza.

További információ:

* [A hozzáférési és használati jelentések megtekintése](/azure/active-directory/active-directory-view-access-usage-reports)
* [Ismerkedés az Azure Active Directory jelentéskészítésével](/azure/active-directory/active-directory-reporting-getting-started)
* [Az Azure Active Directory jelentéskészítési útmutatója](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>A fogyasztók identitásának és hozzáférésének kezelése

Az Azure AD B2C egy magas rendelkezésre állású, globális, identitáskezelési szolgáltatás a több százmillió identitásra skálázható, fogyasztói alkalmazások számára. Mobil- és webes platformokba is integrálható. A fogyasztók a meglévő közösségi fiókjaik használatával vagy új hitelesítő adatok létrehozásával bejelentkezhetnek az összes alkalmazásba testre szabható élmények segítségével.

A múltban az alkalmazásfejlesztők, akik fel akarták iratkozni az ügyfeleket, és be akarták jelentkezni az alkalmazásukba, saját kódot írtak volna. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Az Azure AD B2C egy biztonságos, szabványokon alapuló platform és bővíthető szabályzatok széles választéka segítségével jobb an-felelek a szervezet számára, hogy integrálja a fogyasztói identitáskezelést az alkalmazásokba.

Az Azure AD B2C használatakor a fogyasztók a meglévő közösségi fiókjaik (Facebook, Google, Amazon, LinkedIn) vagy új hitelesítő adatok (e-mail cím és jelszó, felhasználónév és jelszó) használatával regisztrálhatnak az alkalmazásokra.

További információ:

* [Mi az az Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Az Azure Active Directory B2C előzetes verziója: felhasználók regisztrálása és beléptetése az alkalmazásokba ](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C előzetes verzió: Alkalmazástípusok](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Eszközregisztráció

Az Azure AD-eszköz regisztrációja az eszközalapú [feltételes hozzáférés-forgatókönyvek](/azure/active-directory/active-directory-conditional-access-device-registration-overview) alapja. Ha egy eszköz regisztrálva van, az Azure AD-eszköz regisztrációja biztosítja az eszköz egy identitást, amely et használ az eszköz hitelesítéséhez, amikor a felhasználó bejelentkezik. A hitelesített eszköz és az eszköz attribútumai ezután feltételes hozzáférési szabályzatok kényszerítésére használható a felhőben és a helyszíni üzemeltetett alkalmazások.

Mobileszköz-felügyeleti megoldással, például az Intune-nal kombinálva az Azure AD-ben az eszköz attribútumai frissülnek az eszközre vonatkozó további információkkal. Ezután feltételes hozzáférési szabályokat hozhat létre, amelyek kényszerítik az eszközökről való hozzáférést a biztonsági és megfelelőségi szabványoknak való megfelelés érdekében.

További információ:

* [Ismerkedés az Azure AD-eszközregisztrációval](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Automatikus eszközregisztráció az Azure AD-vel Windows tartományhoz csatlakozó eszközökhöz](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [A Windows tartományhoz csatlakozó eszközök automatikus regisztrációjának beállítása az Azure AD-vel](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Az Azure AD privilegizált identitáskezelés, kezelheti, szabályozhatja és figyelheti a kiemelt identitások és az erőforrásokhoz való hozzáférés az Azure AD, valamint más Microsoft online szolgáltatások, például az Office 365 és a Microsoft Intune.

A felhasználóknak néha kiemelt műveleteket kell végrehajtaniuk az Azure-ban vagy az Office 365-erőforrásokban, illetve más SaaS-alkalmazásokban. Ez az igény gyakran azt jelenti, hogy a szervezeteknek állandó kiemelt hozzáférést kell biztosítaniuk a felhasználóknak az Azure AD-ben. Az ilyen hozzáférés egyre nagyobb biztonsági kockázatot jelent a felhőben üzemeltetett erőforrások számára, mivel a szervezetek nem tudják megfelelően figyelni, hogy a felhasználók mit csinálnak a rendszergazdai jogosultságaikkal. Emellett ha egy kiemelt hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, hogy egy megsértése hatással lehet a szervezet teljes felhőbiztonság. Az Azure AD kiemelt identitáskezelés segít csökkenteni ezt a kockázatot.

Az Azure AD kiemelt identitáskezelés, a következőket teheti:

* Tekintse meg, hogy mely felhasználók az Azure AD-rendszergazdák.
* Igény szerinti, just-in-time (JIT) rendszergazdai hozzáférés engedélyezése a Microsoft szolgáltatásaihoz, például az Office 365-höz és az Intune-hoz.
* Jelentéseket kaphat a rendszergazdai hozzáférési előzményekről és a rendszergazdai hozzárendelések változásairól.
* A kiemelt szerepkörhöz való hozzáféréssel kapcsolatos riasztások.

További információ:

* [Mi az az Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Azure AD címtárszerepkörök hozzárendelése a PIM-ben](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identitásvédelem

Az Azure AD Identity Protection egy olyan biztonsági szolgáltatás, amely a szervezet identitását érintő kockázatészlelések és potenciális biztonsági rések összevont nézetét biztosítja. Az Identity Protection kihasználja a meglévő Azure AD anomáliadetektálási képességeket, amelyek az Azure AD rendellenes tevékenységjelentéseken keresztül érhetők el. Az Identity Protection új kockázatészlelési típusokat is bevezet, amelyek valós időben észlelik az anomáliákat.

További információ:

* [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)
* [9. csatorna: Azure AD és identitásmegjelenítés: Identitásvédelem előzetes verziója](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hibrid identitáskezelés/Azure AD-csatlakozás

A Microsoft identitáskezelési megoldásai kiterjednek a helyszíni és felhőalapú képességekre is, így egyetlen felhasználói identitás jön létre hitelesítés és engedélyezés céljából az összes erőforráshoz, helytől függetlenül. Ezt hibrid identitásnak nevezzük. A Microsoft Azure AD Connect eszköze segítségével teljesítheti a hibrid identitáskezelési célokat. Így közös identitást biztosíthat a felhasználóinak az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz. Ez a tevékenység a következő jellemzőkkel bír:

* Szinkronizálás
* AD FS és összevonási integráció
* Áthaladás hitelesítésen
* Állapotfigyelés

További információ:

* [Hibrid identitásról szóló tanulmány](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Az Azure AD-csapat blogja](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD hozzáférési felülvizsgálatok

Az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokkal a cégek hatékonyan kezelhetik a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a kiemelt szerepkörök hozzárendeléseit.

További információ:

* [Azure AD hozzáférési felülvizsgálatok](../../active-directory/governance/access-reviews-overview.md)
* [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](../../active-directory/governance/access-reviews-overview.md)
