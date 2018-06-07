---
title: Az Azure biztonsági funkciói segítenek az identity management alkalmazással |} Microsoft Docs
description: " Ez a cikk az alapvető áttekintést nyújt az Azure biztonsági funkciókat, amelyek segítenek az identity management alkalmazással. Microsoft identitások és hozzáférések felügyeleti megoldások Súgó informatikai védelme alkalmazásokhoz és erőforrásokhoz való hozzáférést az vállalati adatközpontban és a felhőben, további szinteket gazdakép, például többtényezős hitelesítés és a feltételes hozzáférés engedélyezése házirendek. "
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
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 22b233e23c5c4609990bb6ba6148fe24d3d82c4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641349"
---
# <a name="azure-identity-management-security-overview"></a>Azure-identitás biztonsági – áttekintés
Microsoft identitások és hozzáférések felügyeleti megoldások Súgó informatikai védelme alkalmazásokhoz és erőforrásokhoz való hozzáférést, az vállalati adatközpontban és a felhőben. Ez a védelem lehetővé teszi, hogy a gazdakép, például többtényezős hitelesítés és a feltételes hozzáférési házirendek további szinteket. Gyanús tevékenységek figyelése keresztül speciális biztonsági jelentés, naplózásához és riasztási segít mérsékelni a potenciális biztonsági problémákat. [Az Azure Active Directory Premium](../active-directory/active-directory-editions.md) webalkalmazások hozzáférését, amikor a helyi és egyszeri bejelentkezés (SSO) egy szolgáltatott szoftverként (SaaS) alkalmazások, a felhő szoftver több ezer biztosít.

Alapul véve a biztonsági szempontból előnyökkel járhat az Azure Active Directory (Azure AD), a következő műveletek végezhetők el:

* Hozzon létre, és egy egyetlen identitást az egyes felhasználók kezeléséhez a hibrid vállalati, a felhasználók, csoportok és az eszközök szinkronban tartja.
* Az alkalmazások, beleértve a több ezer előre integrált Szolgáltatottszoftver-alkalmazásoknál SSO-hozzáférést biztosítson.
* Engedélyezze a hozzáférést alkalmazásbiztonsági mind a helyszíni szabályalapú többtényezős hitelesítés kényszerítése, és a felhőalapú alkalmazásokhoz.
* A helyszíni webalkalmazások Azure AD alkalmazásproxy segítségével biztonságos távoli hozzáférés kiépítéséhez.

Ez a cikk célja az identity management alkalmazással az Azure biztonsági szolgáltatásokat az alapvető áttekintés biztosítása. Azt adja meg egyes szolgáltatások az adatait, így további hivatkozásokat is adja meg.  

A cikk foglalkozik, a következő alapvető Azure identitáskezelési funkciói:

* Egyszeri bejelentkezés
* Fordított proxy
* Multi-Factor Authentication
* Biztonsági figyelést, a riasztások és a machine learning-alapú jelentések
* A felhasználói identitások és hozzáférés kezelése
* Eszközregisztráció
* Privileged identity management
* Identitásvédelem
* Hibrid Identitáskezelés

## <a name="single-sign-on"></a>Egyszeri bejelentkezés
Egyszeri bejelentkezés azt jelenti, hogy nem tudnak hozzáférni a alkalmazásokat és erőforrásokat, végre kell hajtani, üzleti által csak akkor, ha egyetlen felhasználói fiókkal jelentkezik be. Miután bejelentkezett, van-e hozzáférési összes szolgáltatásbeli hitelesítéshez szükséges nélkül kell alkalmazást (például adjon meg egy jelszót) még egyszer.

Számos szervezet számítson arra, hogy a felhasználói termelékenység például Office 365, a mezőbe, és a Salesforce SaaS-alkalmazásokhoz. Hagyományosan informatikai munkatársak külön-külön létrehozásához, és minden SaaS-alkalmazás a felhasználói fiókok frissítése szükséges, és a felhasználóknak kellett jegyezze meg a jelszót az egyes SaaS-alkalmazáshoz.

Az Azure AD kiterjeszti a helyszíni Active Directory-környezeteket kiterjeszti a felhőbe, így a felhasználók használhatják a saját elsődleges szervezeti nem csak a tartományhoz csatlakoztatott eszközökre és a munkahelyi erőforrásokhoz való bejelentkezéshez, de még a webes és a Szolgáltatottszoftver-alkalmazáshoz szükséges a feladatok.

Nem csak felhasználóknak nem kell több példányban felhasználónevek és jelszavak kezelése, kiépítése, vagy leépíti alkalmazás-hozzáférés automatikusan, a csoportokat és az alkalmazott állapota alapján. Az Azure AD vezet be, biztonsági és hozzáférés-irányítási vezérlést, amellyel központilag kezelheti a felhasználói hozzáférés SaaS-alkalmazások között.

További információ:

* [Az egyszeri bejelentkezés – áttekintés](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Azure Active Directoryval az egyszeri bejelentkezés integrálása SaaS-alkalmazásokhoz](../active-directory/active-directory-enterprise-apps-manage-sso.md)

## <a name="reverse-proxy"></a>Fordított proxy
Az Azure AD-alkalmazásproxy lehetővé teszi, hogy a helyszíni alkalmazások, például közzététele [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) helyek, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), és [IIS](http://www.iis.net/)-alapú alkalmazások a magánhálózaton belül és biztonságos hozzáférést biztosít a felhasználók a hálózaton kívülről. Alkalmazásproxy távoli hozzáférést biztosít, és számos különböző típusú helyszíni az SSO webalkalmazásokat a több ezer, amely támogatja az Azure AD SaaS-alkalmazásokhoz. Az alkalmazottak jelentkezhetnek be az alkalmazásokat a saját eszközükön otthoni és a felhő alapú proxyn keresztül történő hitelesítéséhez.

További információ:

* [Az Azure AD-alkalmazásproxy engedélyezése](../active-directory/manage-apps/application-proxy-enable.md)
* [Az Azure AD-alkalmazásproxy használó alkalmazások közzététele](../active-directory/active-directory-application-proxy-publish.md)
* [Egyszeri bejelentkezés az alkalmazásproxy](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés használata](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Az Azure multi-factor Authentication a hitelesítési mód, amely egynél több ellenőrzési módszer használatát igényli, és a kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat. Többtényezős hitelesítés segítségével hozzáférés biztonságossá tételét adatokhoz és alkalmazásokhoz egyszerű bejelentkezési folyamatot a felhasználó igény szerint betartása mellett. Erős hitelesítés, ellenőrzési lehetőségek széles keresztül biztosítja: telefonhívásokat, a szöveges üzenetek, vagy a mobilalkalmazáson keresztüli értesítések vagy a ellenőrző kódok kezelésére és a külső OAuth-jogkivonatokat.

További információ:

* [Többtényezős hitelesítés](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Azure multi-factor Authentication működése](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Biztonsági figyelést, a riasztások és a machine learning-alapú jelentések
Biztonsági figyelést, a riasztások és a machine learning-alapú jelentések, amelyek azonosítják az inkonzisztens hozzáférési mintázatokat segítségével védelmet az üzleti. Használhatja az Azure AD hozzáférési és használati jelentések hogy lássák az integritásra és a munkahely címtárában biztonságát. Ezt az információt a címtár rendszergazdája is jobban meghatározhatja, ahol előfordulhat, hogy lennie lehetséges biztonsági kockázatokat, hogy azok megfelelően megtervezheti kockázatok csökkentésének lehetőségeit.

Az Azure-portálon jelentések a következő kategóriákba sorolhatók:

* **Az anomáliadetektálási jelentések**: bejelentkezési események észleltünk a rendellenes kell tartalmaznia. Célunk, ellenőrizze, hogy tisztában legyen ilyen tevékenység, és lehetővé teszik annak megállapításához, hogy az esemény gyanús.
* **Integrált alkalmazás jelentések**: betekintést, hogyan használja a szervezet a felhőalapú alkalmazásokhoz. Az Azure AD felhőalapú alkalmazások ezer integrálható.
* **Hibajelentések**: a külső alkalmazásokba fiókok kiépítése során felmerülő hibákat.
* **Felhasználó-specifikus jelentések**: egy adott felhasználó eszköz bejelentkezési tevékenység adatok jelennek meg.
* **Tevékenységi naplóit**: belül a elmúlt 24 óra, a legutóbbi 7 nap, vagy a utolsó 30 nap során, és a csoport tevékenység módosításainak és a jelszó alaphelyzetbe állítása és nyilvántartási tevékenység feljegyzés az összes naplózott eseményeket tartalmazza.

További információ:

* [A hozzáférési és használati jelentések megtekintése](../active-directory/active-directory-view-access-usage-reports.md)
* [Ismerkedés az Azure Active Directory reportingban](../active-directory/active-directory-reporting-getting-started.md)
* [Az Azure Active Directory jelentéskészítés – útmutató](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>A felhasználói identitások és hozzáférés kezelése
Az Azure AD B2C egy magas rendelkezésre állású, globális, identitás szolgáltatást a felhasználók felé néző alkalmazások száz millió identitások alkalmazkodnak. Mobil- és webes platformokba is integrálható. A felhasználók bármikor beléphet, testre szabható felhasználói élmény mellett az alkalmazások új vagy meglévő közösségi fiókjaik használatával.

A múltban alkalmazásfejlesztők számára vevők és az alkalmazások jelentkezzen be őket volna írt a saját kódot. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Az Azure AD B2C integrálniuk a felhasználói Identitáskezelés alkalmazások segítségével biztonságos, szabványokon alapuló platformja és bővíthető szabályzatainak számos fejlettebb módszert kínál a szervezete.

Az Azure AD B2C használata esetén a felhasználók regisztrálhatnak az alkalmazások új hitelesítő adatok (e-mail címet és jelszót, vagy felhasználónév és jelszó) létrehozásával vagy meglévő közösségi fiókjaik használatával (Facebook, Google, Amazon, LinkedIn).

További információ:

* [Mi az Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C előzetes verzió: bejelentkezés regisztrálása és beléptetése az alkalmazások a fogyasztói](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C előzetes verzió: Típusú alkalmazások](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Eszközregisztráció
Az Azure AD eszközregisztrációval az alapja az eszközalapú [feltételes hozzáférés](../active-directory/active-directory-conditional-access-device-registration-overview.md) forgatókönyvek. Amikor regisztrál egy eszközt, az Azure AD eszközregisztrációval látja el az eszközt, amelyet az eszköz hitelesítésére, ha egy felhasználó bejelentkezik használ identitással. A hitelesített eszköz és az eszköz attribútumai – a feltételes hozzáférési szabályzatok érvényesítését a felhőben és a helyszínen tárolt alkalmazások majd használható.

Például az Intune mobileszköz-kezelési megoldás kombinálva az Azure AD-tulajdonságok frissítése eszközzel kapcsolatos további információkat. Feltételes hozzáférési szabályok, amelyeket a biztonsági és megfelelőségi előírások teljesítéséhez eszközeiről is létrehozhat.

További információ:

* [Ismerkedés az Azure AD eszközregisztrációval](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatikus eszközregisztráció az Azure ad-val a Windows-tartományhoz csatlakozó eszközök](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Tartományhoz csatlakozó eszközök az Azure ad-val a Windows automatikus regisztráció beállítása](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged identity management
Az Azure AD Privileged Identity Management kezelése, szabályozása és figyelheti az emelt szintű identitások és erőforrások az Azure ad-ben, valamint más Microsoft online szolgáltatások, például az Office 365 és az Intune-hoz való hozzáférés.

Felhasználók néha el kell végezniük a jogosultságokhoz kötött műveletek Azure vagy az Office 365 erőforrásokat, vagy más SaaS-alkalmazásokhoz. Ezt gyakran azt jelenti, hogy a szervezetek felhasználók állandó privilegizált hozzáférést biztosíthat az Azure ad-ben. Ilyen hozzáférést nem a felhőben üzemeltetett erőforrásokhoz az egyre növekvő biztonsági kockázatot jelent, mert a szervezeteknek elég nem tud figyelni a felhasználók tevékenységeit a rendszergazdai jogosultságokkal. Ha jogosultsági szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, emellett egy megsértésének hatással lehet a szervezet általános felhő biztonsági. Az Azure AD Privileged Identity Management segít a kockázat csökkentése érdekében.

Az Azure AD Privileged Identity Management a következőket teheti:

* Itt megtekintheti a felhasználók az Azure AD-rendszergazdák.
* Engedélyezze az igény szerint közvetlenül az igény (szerinti JIT) rendszergazdai hozzáféréssel Microsoft szolgáltatásaihoz, például az Office 365 és az Intune-ban.
* Rendszergazda-hozzárendelések rendszergazdai hozzáférési műveleteiről és a módosítások lekérése.
* Egy kiemelt szerepkörhöz való hozzáféréssel kapcsolatos riasztásokat kaphat.

További információ:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Szerepkörök az Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Az Azure AD Privileged Identity Management: Hogyan lehet hozzáadni vagy eltávolítani egy felhasználói szerepkör](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identitásvédelem
Az Azure AD Identity Protection olyan biztonsági szolgáltatás, amely a kockázati eseményekről és a szervezet identitásait érintő lehetséges biztonsági résekről egyesített nézetét biztosítja. Azonosító adatok védelmét a kihasználja a meglévő Azure AD-anomáliadetektálás képességek, amelyek elérhetők az Azure AD rendellenes tevékenységet jelentéseket. Azonosító adatok védelmét is vezet be új kockázat típusait, amely valós idejű rendellenességek észlelését.

További információ:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [9. csatornán: Az Azure AD és az Identity: Identity Protection előzetes kiadásának](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hibrid Identitáskezelés
A Microsoft identity módja is a helyszíni és a felhő létrehozása a hitelesítés és engedélyezés az összes erőforráshoz, függetlenül a hely egyetlen felhasználói azonosítót.

További információ:

* [Hibrid identitáskezelési dokumentáció](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Az Azure AD-csapat blogja](https://blogs.technet.microsoft.com/ad/)
