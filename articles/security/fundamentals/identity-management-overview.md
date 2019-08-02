---
title: Azure-beli biztonsági funkciók, amelyek segítenek az Identitáskezelés kezelésében | Microsoft Docs
description: " Ez a cikk áttekintést nyújt az Azure alapvető biztonsági funkcióiról, amelyek segítenek az Identitáskezelés kezelésében. A Microsoft identitás-és hozzáférés-kezelési megoldásai segítenek a vállalati adatközpontban és a felhőben lévő alkalmazásokhoz és erőforrásokhoz való hozzáférés védelmében, így további ellenőrzési szintek (például többtényezős hitelesítés és feltételes hozzáférés) is elérhetők. szabályzatok. "
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
ms.openlocfilehash: ee9f771483b0478ec4b7d1bf92095d9dd07f45ad
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727237"
---
# <a name="azure-identity-management-security-overview"></a>Az Azure Identity Management biztonsági áttekintése

 Az Identitáskezelés a [rendszerbiztonsági tag](/windows/security/identity-protection/access-control/security-principals)hitelesítésének és engedélyezésének folyamata. Emellett magában foglalja az ilyen rendszerbiztonsági tagok (identitások) adatainak szabályozását is. A rendszerbiztonsági tag (identitások) tartalmazhatnak szolgáltatásokat, alkalmazásokat, felhasználókat, csoportokat stb. A Microsoft identitás-és hozzáférés-kezelési megoldásai segítenek a vállalati adatközpontban és a felhőben lévő alkalmazásokhoz és erőforrásokhoz való hozzáférés védelmében. Az ilyen védelem további ellenőrzési szinteket tesz lehetővé, például a többtényezős hitelesítést és a feltételes hozzáférési szabályzatokat. A gyanús tevékenységek speciális biztonsági jelentéskészítéssel, naplózással és riasztással való figyelése segít csökkenteni a lehetséges biztonsági problémákat. A [prémium szintű Azure Active Directory](/azure/active-directory/active-directory-editions) egyszeri bejelentkezést (SSO-t) biztosít több ezer felhőalapú szoftver-(SaaS-) alkalmazáshoz, és hozzáfér a helyszínen futtatott webalkalmazásokhoz.
 
Ha kihasználja Azure Active Directory (Azure AD) biztonsági előnyeit, a következőket teheti:

* Egyetlen identitás létrehozása és kezelése minden felhasználó számára a hibrid vállalaton belül, a felhasználók, csoportok és eszközök szinkronizálással való megőrzése. 
* Egyszeri bejelentkezéses hozzáférés biztosítása az alkalmazásokhoz, beleértve több ezer előre integrált SaaS-alkalmazást.
* Engedélyezze az alkalmazás-hozzáférési biztonságot a szabályokon alapuló többtényezős hitelesítésnek a helyszíni és a Felhőbeli alkalmazásokhoz való kényszerítésével.
* Biztonságos távoli hozzáférés biztosítása helyszíni webalkalmazásokhoz az Azure AD Application Proxy használatával.

Ennek a cikknek a célja, hogy áttekintést nyújtson az Azure-beli alapvető biztonsági funkciókról, amelyek segítik az Identitáskezelés kezelését. Olyan cikkekre mutató hivatkozásokat is biztosítunk, amelyek részletesen ismertetik az egyes funkciók részleteit, így többet is megtudhat.  

A cikk a következő alapvető Azure Identity Management-képességekre összpontosít:

* Egyszeri bejelentkezés
* Fordított proxy
* Multi-Factor Authentication
* Szerepköralapú hozzáférés-vezérlés (RBAC)
* Biztonsági monitorozás, riasztások és gépi tanuláson alapuló jelentések
* A felhasználói identitások és hozzáférés kezelése
* Eszközregisztráció
* Privilegizált identitások kezelése
* Identitásvédelem
* Hibrid Identitáskezelés/Azure AD-kapcsolat
* Azure AD hozzáférési felülvizsgálatok

## <a name="single-sign-on"></a>Egyszeri bejelentkezés

Az SSO azt jelenti, hogy az összes szükséges alkalmazáshoz és erőforráshoz csak egyszer kell bejelentkeznie egyetlen felhasználói fiók használatával. A bejelentkezést követően az összes szükséges alkalmazáshoz hozzáférhet a hitelesítéshez (például egy jelszó megadásához).

Számos szervezet olyan SaaS-alkalmazásokra támaszkodik, mint az Office 365, a Box és a Salesforce a felhasználói hatékonyság érdekében. Az informatikai részlegnek az egyes SaaS-alkalmazásokban egyenként kell létrehoznia és frissítenie a felhasználói fiókokat, és a felhasználóknak minden SaaS-alkalmazáshoz meg kellett emlékezniük a jelszót.

Az Azure AD kiterjeszti a helyszíni Active Directory környezeteket a felhőbe, így a felhasználók az elsődleges szervezeti fiók használatával nem csak a tartományhoz csatlakoztatott eszközökre és a vállalati erőforrásokra, hanem a szükséges webes és SaaS-alkalmazásokra is bejelentkezhetnek. a feladatok elvégzéséhez.

Nem csak a felhasználóknak több felhasználónevet és jelszót kell kezelnie, a szervezeti csoportok és az alkalmazotti állapotuk alapján automatikusan kiépítheti vagy kiépítheti az alkalmazásokhoz való hozzáférést. Az Azure AD olyan biztonsági és hozzáférési irányítási ellenőrzéseket vezet be, amelyekkel központilag kezelheti a felhasználók hozzáférését az SaaS-alkalmazásokban.

További információ:

* [Az egyszeri bejelentkezés áttekintése](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Azure Active Directory egyszeri bejelentkezés integrálása SaaS-alkalmazásokkal](../../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Fordított proxy

Az Azure AD Application Proxy lehetővé teszi a helyszíni alkalmazások, például a [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) -webhelyek, az [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)és az [IIS](https://www.iis.net/)-alapú alkalmazások közzétételét a magánhálózaton belül, és biztonságos hozzáférést biztosít a hálózaton kívüli felhasználók számára. Az alkalmazásproxy távoli hozzáférést és egyszeri bejelentkezést biztosít számos különböző típusú helyszíni webalkalmazáshoz az Azure AD által támogatott több ezer SaaS-alkalmazással. Az alkalmazottak bejelentkezhetnek az alkalmazásaiba otthonról a saját eszközein, és ezen a felhőalapú proxyn keresztül hitelesíthetők.

További információ:

* [Az Azure AD Application Proxy engedélyezése](/azure/active-directory/manage-apps/application-proxy-enable)
* [Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](/azure/active-directory/active-directory-application-proxy-publish)
* [Egyszeri bejelentkezés az Application proxyval](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés használata](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Az Azure multi-Factor Authentication olyan hitelesítési módszer, amely több ellenőrzési módszer használatát igényli, és egy kritikus második biztonsági réteget vesz fel a felhasználói bejelentkezésekhez és tranzakciókra. A többtényezős hitelesítés segítségével biztosítható a hozzáférés az adatkezeléshez és az alkalmazásokhoz, miközben egy egyszerű bejelentkezési folyamatra vonatkozó felhasználói igényeket is kielégít. Erős hitelesítést biztosít számos ellenőrzési lehetőség használatával: telefonhívások, szöveges üzenetek vagy Mobile apps-értesítések, valamint ellenőrző kódok és külső OAuth-tokenek.

További információ:

* [Többtényezős hitelesítés](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Az Azure multi-Factor Authentication működése](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

A RBAC egy Azure Resource Managerra épülő engedélyezési rendszer, amely részletes hozzáférés-kezelést biztosít az Azure-beli erőforrásokhoz. A RBAC lehetővé teszi a felhasználók által használt hozzáférés szintjének részletes szabályozását. Például korlátozhatja, hogy egy felhasználó csak a virtuális hálózatokat és egy másik felhasználót kezelje az erőforráscsoport összes erőforrásának kezeléséhez. Az Azure számos beépített szerepkört tartalmaz, amelyeket használhat. Az alábbiakban négy alapvető beépített szerepkört sorolunk fel. Az első három minden erőforrástípusra alkalmazható.

További információ:

* [Mi az a szerepköralapú hozzáférés-vezérlés (RBAC)?](/azure/role-based-access-control/overview)
* [Beépített szerepkörök Azure-erőforrásokhoz](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Biztonsági monitorozás, riasztások és gépi tanuláson alapuló jelentések

A nem konzisztens hozzáférési mintákat azonosító biztonsági monitorozási, riasztási és gépi tanulási jelentések segíthetnek a vállalata védelmében. Az Azure AD-hozzáférési és-használati jelentések segítségével megtekintheti a szervezete címtárának integritását és biztonságát. Ezekkel az információkkal a címtár-rendszergazdák jobban meghatározhatják, hogy az esetleges biztonsági kockázatok hol lehetnek a kockázatok enyhítése érdekében.

A Azure Portal a jelentések a következő kategóriákba sorolhatók:

* **Anomália-jelentések**: A rendellenesnek talált bejelentkezési eseményeket tartalmazza. Célunk, hogy tisztában legyenek az ilyen tevékenységekkel, és lehetővé teszik annak meghatározását, hogy az esemény gyanús-e.
* **Integrált alkalmazás-jelentések**: Megtudhatja, hogyan használják a Felhőbeli alkalmazásokat a szervezetében. Az Azure AD több ezer felhőalapú alkalmazással való integrációt biztosít.
* **Hibajelentések**: A fiókok külső alkalmazásokba való kiosztásakor előforduló hibák jelzése.
* **Felhasználó-specifikus jelentések**: Az eszköz bejelentkezési tevékenységére vonatkozó információk megjelenítése egy adott felhasználó számára.
* **Tevékenységek naplói**: Az elmúlt 24 órában, az elmúlt 7 napban vagy az elmúlt 30 napban található összes naplózott esemény rekordját tartalmazza, valamint a csoport tevékenységeinek változásait, valamint a jelszó-visszaállítást és a regisztrációs tevékenységet.

További információ:

* [A hozzáférési és használati jelentések megtekintése](/azure/active-directory/active-directory-view-access-usage-reports)
* [Ismerkedés a Azure Active Directory jelentéskészítéssel](/azure/active-directory/active-directory-reporting-getting-started)
* [Azure Active Directory jelentéskészítési útmutató](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>A felhasználói identitások és hozzáférés kezelése

A Azure AD B2C egy nagyszámú, globális, identitás-kezelési szolgáltatás, amely több száz millió identitásra méretezhető. Mobil- és webes platformokba is integrálható. A felhasználók a meglévő közösségi fiókjaik használatával vagy új hitelesítő adatok létrehozásával jelentkezhetnek be az összes alkalmazásba.

A múltban az alkalmazások fejlesztői számára, akik szeretnék regisztrálni az ügyfeleket, és saját alkalmazásaikban is aláírják őket, saját programkódot kellett volna beírniuk. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Azure AD B2C a biztonságos, szabványon alapuló platform és a bővíthető házirendek nagy készletének segítségével a szervezetnek jobb megoldást kínál a fogyasztói Identitáskezelés alkalmazásba való integrálására.

Azure AD B2C használatakor a felhasználók a meglévő közösségi fiókjaik (Facebook, Google, Amazon, LinkedIn) használatával regisztrálhatják alkalmazásaikat, vagy új hitelesítő adatokat (e-mail-címet, jelszót vagy felhasználónevet és jelszót) hoznak létre.

További információ:

* [Mi az az Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C előzetes verzió: Felhasználók regisztrálása és bejelentkezés az alkalmazásokban](../../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C előzetes verzió: Alkalmazások típusai](../../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Eszközregisztráció

Az Azure AD-eszközök regisztrációja az eszköz alapú [feltételes hozzáférési](/azure/active-directory/active-directory-conditional-access-device-registration-overview) forgatókönyvek alapja. Ha egy eszköz regisztrálva van, az Azure AD-eszköz regisztrálása olyan identitást biztosít az eszköz számára, amelyet az eszköz hitelesítéséhez használ, amikor a felhasználó bejelentkezik. A rendszer ezután a hitelesített eszközt és az eszköz attribútumait felhasználhatja a felhőben és a helyszínen üzemeltetett alkalmazások feltételes hozzáférési házirendjeinek érvénybe léptetéséhez.

A mobileszköz-kezelési megoldással, például az Intune-nal kombinálva az Azure AD-ben az eszköz attribútumai frissülnek az eszközre vonatkozó további információkkal. Ezután létrehozhat olyan feltételes hozzáférési szabályokat, amelyek kikényszerítik az eszközök hozzáférését, hogy megfeleljenek a biztonsági és megfelelőségi szabványoknak.

További információ:

* [Ismerkedés az Azure AD-eszközök regisztrálásával](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Automatikus eszköz regisztrálása az Azure AD-vel Windows-tartományhoz csatlakoztatott eszközökhöz](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [A Windows-tartományhoz csatlakoztatott eszközök automatikus regisztrációjának beállítása az Azure AD-vel](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privilegizált identitások kezelése

A Azure AD Privileged Identity Management segítségével felügyelheti, felügyelheti és figyelheti a Kiemelt identitásokat, és hozzáférhet az Azure AD-beli erőforrásokhoz, valamint más Microsoft-online szolgáltatásokokhoz, például az Office 365-hez és a Microsoft Intunehoz.

A felhasználóknak időnként magas jogosultsági szintű műveleteket kell végrehajtaniuk az Azure-ban vagy az Office 365-erőforrásokban vagy más SaaS-alkalmazásokban. Ez gyakran azt jelenti, hogy a szervezeteknek állandó jogosultsági szintű hozzáférést kell biztosítaniuk a felhasználóknak az Azure AD-ben. Az ilyen hozzáférés egyre nagyobb biztonsági kockázatot jelent a felhőben üzemeltetett erőforrások számára, mivel a szervezetek nem tudják eléggé megfigyelni, hogy a felhasználók hogyan használják a rendszergazdai jogosultságokat. Emellett, ha egy emelt szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, az egyik megszegés befolyásolhatja a szervezet teljes Felhőbeli biztonságát. Azure AD Privileged Identity Management segít csökkenteni ezt a kockázatot.

A Azure AD Privileged Identity Management a következőket teheti:

* Megtekintheti, hogy mely felhasználók Azure AD-rendszergazdák.
* Igény szerinti (JIT) rendszergazdai hozzáférés engedélyezése a Microsoft-szolgáltatásokhoz, például az Office 365-hez és az Intune-hoz.
* A rendszergazdai hozzáférési előzményekkel és a rendszergazdai hozzárendelések változásaival kapcsolatos jelentések beolvasása.
* Riasztást kaphat a Kiemelt szerepkörhöz való hozzáférésről.

További információ:

* [Mi az Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identitásvédelem

A Azure AD Identity Protection egy biztonsági szolgáltatás, amely összevont nézetet biztosít a kockázati eseményekről és a szervezet identitásait érintő lehetséges sebezhetőségekről. Az Identity Protection kihasználja a meglévő Azure AD-anomáliák – észlelési képességeket, amelyek az Azure AD rendellenes tevékenységi jelentéseiben érhetők el. Az Identity Protection olyan új kockázati események típusait is bemutatja, amelyek valós időben tudják észlelni a rendellenességeket.

További információ:

* [Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD-és identitás-megjelenítés: Identity Protection – előzetes verzió](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hibrid Identitáskezelés/Azure AD-kapcsolat

A Microsoft identitáskezelési megoldásai kiterjednek a helyszíni és felhőalapú képességekre is, így egyetlen felhasználói identitás jön létre hitelesítés és engedélyezés céljából az összes erőforráshoz, helytől függetlenül. Ezt hibrid identitásnak nevezzük. A Microsoft Azure AD Connect eszköze segítségével teljesítheti a hibrid identitáskezelési célokat. Így közös identitást biztosíthat a felhasználóinak az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz. Ez a tevékenység a következő jellemzőkkel bír:

* Szinkronizáció
* AD FS és összevonás-integráció
* Hitelesítés továbbítása
* Állapotfigyelés

További információ:

* [Hibrid identitás – tanulmány](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD-csapat blogja](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD hozzáférési felülvizsgálatok

Az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokkal a cégek hatékonyan kezelhetik a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a kiemelt szerepkörök hozzárendeléseit.

További információ:

* [Azure AD-hozzáférési felülvizsgálatok](../../active-directory/governance/access-reviews-overview.md)
* [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](../../active-directory/governance/access-reviews-overview.md)
