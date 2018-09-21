---
title: Az Azure security funkcióit, amelyek segítenek az identity management alkalmazással |} A Microsoft Docs
description: " Ez a cikk az alapvető áttekintést nyújt az Azure biztonsági funkciókat, amelyeket az identity management segítségével. A Microsoft identitás- és hozzáférés felügyeleti megoldások segítséget nyújt az informatikai való hozzáférés védelme az alkalmazások és erőforrások a vállalati adatközpontok és a felhőben, további szinteket érvényesítése, például a többtényezős hitelesítés és feltételes hozzáférés engedélyezése házirendek. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As a IT Pro or decision maker I am trying to learn about identity mangement capabilities in Azure
ms.openlocfilehash: f15846f3de7162c61edce8aaf5c493bb1444df36
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498555"
---
# <a name="azure-identity-management-security-overview"></a>Az Azure identity management biztonsági áttekintése

 Identitáskezelés az a folyamat, hitelesítés és engedélyezés [rendszerbiztonsági](https://docs.microsoft.com/windows/security/identity-protection/access-control/security-principals). Ellenőrző információ ezen rendszerbiztonsági tagok (azonosítók) is magában foglalja. Rendszerbiztonsági tagok (azonosítók) tartalmazhatnak, szolgáltatások, alkalmazások, felhasználók, csoportok stb. A Microsoft identitás- és hozzáférés felügyeleti megoldások segítséget nyújt az informatikai alkalmazásokhoz és erőforrásokhoz való hozzáférés védelme, a vállalati adatközpontok és a felhőben. Az ilyen jellegű védelem lehetővé teszi, hogy további szinteket érvényesítése, például a többtényezős hitelesítés és feltételes hozzáférési szabályzatokat. Gyanús tevékenységek figyelése keresztül a speciális biztonsági jelentések, a naplózás és a riasztási segít mérsékelni a potenciális biztonsági problémákat. [Az Azure Active Directory Premium](../active-directory/active-directory-editions.md) biztosít egyszeri bejelentkezés (SSO) és több ezer felhőalapú szoftver-szoftverszolgáltatások (SaaS) alkalmazásokat és hozzáférési web Apps, amikor a helyi futtatásához.
 
Az Azure Active Directory (Azure AD) biztonsági előnyeit kihasználva, a következőket teheti:

* Hozzon létre és kezelheti az egyes felhasználók egyetlen identitással a hibrid vállalati, a felhasználók, csoportok és eszközök szinkronban tartja. 
* Adja meg az alkalmazások, beleértve a több ezer előre integrált Szolgáltatottszoftver-alkalmazásoknál, SSO-hozzáférést.
* Alkalmazás-hozzáférés biztonságának engedélyezése által a helyszínen – szabályalapú többtényezős hitelesítés kényszerítése és a felhőbeli alkalmazásokhoz.
* Építhet ki biztonságos távoli hozzáférést a helyszíni webalkalmazásokhoz az Azure AD-alkalmazásproxyn keresztül.

Ez a cikk célja az alapvető áttekintést biztosít az Azure biztonsági funkciókat, amelyeket az identity management segítségével. Azt is, hogy egyes szolgáltatások részletei adjon, hogy további cikkek mutató hivatkozásokat tartalmaznak.  

A cikk a következő alapvető Azure identitáskezelési funkciói összpontosít:

* Egyszeri bejelentkezés
* Fordított proxy
* Multi-Factor Authentication
* Szerepköralapú hozzáférés-vezérlés (RBAC)
* A biztonság monitorozása, értesítések és gépi tanuláson alapuló jelentésekkel
* A felhasználói identitások és hozzáférés kezelése
* Eszközregisztráció
* Privileged identity management
* Identitásvédelem
* Hibrid identity management vagy az Azure AD connect
* Azure AD hozzáférési felülvizsgálatok

## <a name="single-sign-on"></a>Egyszeri bejelentkezés

Egyszeri bejelentkezés azt jelenti, hogy nem tudnak hozzáférni az alkalmazásokhoz és erőforrásokhoz, amelyek az üzleti tevékenységet, jelentkezzen be a csak egyszer, egyetlen felhasználói fiókkal kell. Miután bejelentkezett, elérheti összes alkalmazás hitelesítéséhez szükséges nélkül (például adjon meg egy jelszót) egy második alkalommal.

Számos szervezet felhasználói termelékenység csökkenését SaaS-alkalmazásokat például az Office 365-höz, a Box vagy a Salesforce támaszkodnak. Hagyományosan informatikai munkatársak külön-külön létrehozásához, és az egyes SaaS-alkalmazás felhasználói fiókok frissítése szükséges, és a felhasználóknak kellett telepíteniük azt ne felejtse el a jelszót az egyes SaaS-alkalmazás.

Az Azure AD kiterjeszti a felhőbe, a helyszíni Active Directory-környezetek felhasználók a saját elsődleges szervezeti fiókjukba nemcsak a tartományhoz csatlakoztatott eszközök és a vállalati erőforrásokhoz való bejelentkezéshez, de emellett a webes és a SaaS-alkalmazásokhoz szükségük van a feladatok.

Nem csak felhasználó nem rendelkezik több példányban felhasználónevek és jelszavak kezelése, üzembe helyezése, és vonhatnak ki az alkalmazás-hozzáférés automatikusan, azok munkahelyi csoportok és az alkalmazott állapota alapján. Az Azure AD vezet be, biztonság és hozzáférés-irányítási vezérlők, amellyel központilag kezelheti felhasználói hozzáférés SaaS-alkalmazások között.

További információ:

* [Egyszeri bejelentkezés áttekintése](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integráció SaaS-alkalmazások Azure Active Directory egyszeri bejelentkezés](../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Fordított proxy

Az Azure AD-alkalmazásproxy lehetővé teszi a helyszíni alkalmazások, például közzététele [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) helyek, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), és [IIS](http://www.iis.net/)-alapú alkalmazások a magánhálózaton belülről és biztonságos hozzáférést biztosít a hálózaton kívüli felhasználók számára. Az alkalmazásproxy lehetővé teszi a távoli hozzáférést és egyszeri Bejelentkezést tesz lehetővé a helyszíni webalkalmazásokat a több ezer SaaS-alkalmazás, amely támogatja az Azure ad-ben. Alkalmazottak jelentkezhetnek be az alkalmazásokat a saját eszközükön otthoni és a felhő alapú proxyn keresztül történő hitelesítéséhez.

További információ:

* [Az Azure AD-alkalmazásproxy engedélyezése](../active-directory/manage-apps/application-proxy-enable.md)
* [Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](../active-directory/active-directory-application-proxy-publish.md)
* [Egyszeri bejelentkezés alkalmazásproxyval való](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés használata](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Az Azure multi-factor Authentication olyan hitelesítési mód, amely egynél több ellenőrzési módszer használatát igényli, és a egy kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat. Multi-factor Authentication hitelesítés révén biztonságosabb a hozzáférés az adatokhoz és alkalmazásokhoz, miközben a felhasználó igény szerint egy egyszerű bejelentkezési folyamat. Szigorú hitelesítést többféle hitelesítési lehetőséget kínál: telefonhívást, szöveges üzenetek, vagy mobilapp-értesítések vagy ellenőrző kódok és külső OAuth-jogkivonatok.

További információ:

* [Többtényezős hitelesítés](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Az Azure multi-factor Authentication működése](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

Az RBAC az Azure Resource Managerrel, amely az Azure-erőforrások, részletes hozzáférés-vezérlést biztosít beépített engedélyezési rendszert. Az RBAC lehetővé teszi, hogy kínálja vezérelheti a felhasználók hozzáférési szintjét. Például korlátozhatja a felhasználót, hogy csak a virtuális hálózatok kezelése és a egy másik felhasználó kezelheti egy erőforráscsoportba tartozó összes erőforrást. Az Azure számos beépített szerepkört, amelyet használhat tartalmaz. Az alábbiakban négy alapvető beépített szerepkört sorolunk fel. Az első három minden erőforrástípusra alkalmazható.

További információ:

* [Mi az a szerepköralapú hozzáférés-vezérlés (RBAC)?](../role-based-access-control/overview.md)
* [Az Azure-erőforrások beépített szerepkörök](../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>A biztonság monitorozása, értesítések és gépi tanuláson alapuló jelentésekkel

A biztonság monitorozása, értesítések és gépi tanuláson alapuló jelentésekkel, amelyek felismerő segíthet a üzleti adatai védelméről. Használhatja az Azure AD hozzáférési és használati jelentések értékes információkhoz juthat az integritásra és a munkahely címtárában biztonságát. Ezekkel az információkkal a directory-rendszergazda jobban meghatározhatja, előfordulhat, hogy képességein biztonsági kockázatokat, úgy, hogy azok megfelelően tervezi, hogy ezek a kockázatok enyhítéséhez.

Az Azure Portalon jelentések a következő kategóriákba sorolhatók:

* **Anomáliadetektálási jelentések**: bejelentkezési eseményeket találtunk rendellenes kell tartalmaznia. Célunk, győződjön meg arról, hogy tisztában az ilyen tevékenység, és lehetővé teszi a gyanús esemény meghatározásához.
* **Integrált alkalmazás jelentések**: hogyan használja a szervezet a felhőalapú alkalmazások betekintést nyújtson. Az Azure AD kínál több ezer felhőalapú alkalmazások integrációja.
* **Hibajelentések**: a külső alkalmazások fiókok üzembe helyezésekor jelentkező hibákat jelölnek.
* **Felhasználó-specifikus jelentések**: eszköz egy adott felhasználó bejelentkezési tevékenységek adatainak megjelenítéséhez.
* **A Tevékenységnaplók**: az elmúlt 24 órában, legutóbbi 7 nap, vagy elmúlt 30 napban, és a tevékenység módosításainak és jelszó alaphelyzetbe állítása és regisztrációs tevékenység belül az összes naplózott esemény rekordot tartalmaz.

További információ:

* [A hozzáférési és használati jelentések megtekintése](../active-directory/active-directory-view-access-usage-reports.md)
* [Ismerkedés az Azure Active Directory jelentéskészítési](../active-directory/active-directory-reporting-getting-started.md)
* [Az Azure Active Directory jelentéskészítési útmutató](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>A felhasználói identitások és hozzáférés kezelése

Az Azure AD B2C egy magas rendelkezésre állású, globális identitáskezelő szolgáltatást nyújt felhasználók felé néző alkalmazások számára, amely több százmillió identitás kezelésére méretezhető. Mobil- és webes platformokba is integrálható. A felhasználók jelentkezhetnek be minden a testreszabható felületeken, meglévő közösségi hálózati fiókjaikkal vagy új hitelesítő adatok létrehozásával.

Múltbeli időpont az alkalmazásfejlesztők számára ügyfelek regisztráljon, és jelentkezzen be azokat az alkalmazásokat írt saját kódját szeretné rendelkezik. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Az Azure AD B2C kínál a szervezet felhasználói identitáskezelést integrálása egy biztonságos, szabványokon alapuló platform és bővíthető házirendek rengeteg segítségével alkalmazások hatékony megoldást.

Ha az Azure AD B2C-t használja, a felhasználók regisztrálhatnak az alkalmazások meglévő közösségi fiókjaik használatával (Facebook, Google, Amazon, LinkedIn), vagy új hitelesítő adatok (e-mail cím és jelszó vagy felhasználónév és jelszó) létrehozásával.

További információ:

* [Mi az az Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Az Azure Active Directory B2C előzetes verziója: Jelentkezzen be, és jelentkezzen be a fogyasztói alkalmazásokba](../active-directory-b2c/active-directory-b2c-overview.md)
* [Az Azure Active Directory B2C előzetes verzió: Alkalmazástípusok](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Eszközregisztráció

Azure AD-eszközregisztráció alapját az eszköz alapú [feltételes hozzáférési](../active-directory/active-directory-conditional-access-device-registration-overview.md) forgatókönyveket. Amikor regisztrál egy eszközt, az Azure AD eszközregisztrációval biztosít az identitás, amely segítségével hitelesíti az eszközt, amikor egy felhasználó bejelentkezik az eszközre. A hitelesített eszköz és az eszköz attribútumai ezután használható a felhőben és a helyszínen tárolt alkalmazások feltételes hozzáférési szabályzatok kényszerítése.

Például az Intune mobileszköz-felügyeleti megoldás kombinálva, az eszköz attribútumait az Azure ad-ben frissülnek az eszköz további információival. Ezután létrehozhat, amelyeket a biztonsági és megfelelőségi szabványoknak megfelelő eszközök hozzáférését a feltételes hozzáférési szabályok.

További információ:

* [Ismerkedés az Azure AD eszközregisztrációval](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatikus eszközregisztráció az Azure AD-hez Windows-tartományhoz csatlakozott eszközökkel](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Windows automatikus regisztrációjának beállítása az Azure AD-tartományhoz csatlakozott eszközökkel](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged identity management

Az Azure AD Privileged Identity Management kezelése, szabályozása és figyelheti az emelt szintű identitások és hozzáférés az Azure AD-erőforrások, valamint más Microsoft online szolgáltatásaihoz, például az Office 365 és a Microsoft Intune.

Felhasználók néha kell erőforrások Azure vagy Office 365 vagy más SaaS-alkalmazások a privilegizált műveleteket végezni. Ennek az igénynek gyakran azt jelenti, hogy szervezetek felhasználók állandó kiemelt jogosultságú hozzáférés biztosítása az Azure ad-ben. Ilyen hozzáférést egy egyre bővülő biztonsági kockázatot jelent a felhőben futó erőforrások azért, mert a szervezetek nem eléggé figyelheti, mi a felhasználók általi használatát a rendszergazdai jogosultságaikat. Ha emelt szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, ezenkívül egy megsértésének hatással lehetnek a szervezet teljes felhőalapú biztonsági. Az Azure AD Privileged Identity Management segít a kockázat csökkentése érdekében.

Az Azure AD Privileged Identity Management a következőket teheti:

* Tekintse meg, hogy mely felhasználók tartoznak a rendszergazdák Azure ad-ben.
* Igény szerinti, just-in-time (JIT) rendszergazdai hozzáférés engedélyezése a Microsoft-szolgáltatásokba, például az Office 365 és Intune.
* Rendszergazdák hozzáférési műveleteiről és a változások jelentéseket rendszergazdája hozzárendeléseinek beolvasása.
* A kiemelt szerepkörű való hozzáféréssel kapcsolatos riasztásokat kaphat.

További információ:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Szerepkörök az Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-roles.md)
* [Az Azure AD Privileged Identity Management: Hogyan lehet egy felhasználói szerepkör hozzáadása vagy eltávolítása](../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identitásvédelem

Az Azure AD Identity Protection egy biztonsági szolgáltatás, amely a kockázati eseményekről és a szervezet identitásait érintő lehetséges biztonsági résekről összesített nézetet jelenít meg. Identity Protection kihasználja a meglévő Azure ad-ben anomáliadetektálási képességeket, amelyek elérhetők az Azure AD rendellenes tevékenységekre vonatkozó jelentések. Identity Protection is vezet be új kockázati események típusai, amelyek valós időben észlelheti a rendellenességeket.

További információ:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9-on: Az Azure AD és az identitás: Identity Protection előzetes verziója](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hibrid identity management vagy az Azure AD connect

A Microsoft identitáskezelési megoldásai kiterjednek a helyszíni és felhőalapú képességekre is, így egyetlen felhasználói identitás jön létre hitelesítés és engedélyezés céljából az összes erőforráshoz, helytől függetlenül. Ezt hibrid identitásnak nevezzük. Az Azure AD Connect a Microsoft eszköz felel meg, és hibrid identitás céljai elvégzéséhez. Így közös identitást biztosíthat a felhasználóinak az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz. Ez a tevékenység a következő jellemzőkkel bír:

* Szinkronizálás
* AD FS és összevonás-integráció
* Hitelesítési továbbítása
* Állapotfigyelés

További információ:

* [Hibrid identitás (tanulmány)](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Az Azure AD-csapat blogja](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD hozzáférési felülvizsgálatok

Az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokkal a cégek hatékonyan kezelhetik a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a kiemelt szerepkörök hozzárendeléseit.

További információ:

* [Az Azure AD hozzáférési felülvizsgálatok](../active-directory/governance/access-reviews-overview.md)
* [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](../active-directory/governance/access-reviews-overview.md)
