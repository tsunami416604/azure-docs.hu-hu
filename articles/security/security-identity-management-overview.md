---
title: Az Azure biztonsági funkciói segítenek az identity management alkalmazással |} Microsoft Docs
description: " Ez a cikk az alapvető áttekintést nyújt az Azure biztonsági funkciókat, amelyek segítenek az identity management alkalmazással. Microsoft identitások és hozzáférések felügyeleti megoldások Súgó informatikai védelme alkalmazásokhoz és erőforrásokhoz való hozzáférést az vállalati adatközpontban és a felhőben, például a többtényezős hitelesítés és a feltételes hozzáférési házirendek ellenőrzéséhez további szintek engedélyezése. "
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
ms.openlocfilehash: 46e4466085200bf9042c4342ea5e6b37fd2fb701
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-identity-management-security-overview"></a>Azure-identitás biztonsági – áttekintés
Microsoft identitások és hozzáférések felügyeleti megoldások Súgó informatikai védelme alkalmazásokhoz és erőforrásokhoz való hozzáférést az vállalati adatközpontban és a felhőben, például a többtényezős hitelesítés és a feltételes hozzáférési házirendek ellenőrzéséhez további szintek engedélyezése. Gyanús tevékenységek figyelése keresztül speciális biztonsági jelentések, a naplózás és a riasztási segít mérsékelni a potenciális biztonsági problémákat. [Az Azure Active Directory Premium](../active-directory/active-directory-editions.md) biztosítja az egyszeri bejelentkezés felhő több ezer (SaaS) alkalmazások és webalkalmazások hozzáférést a helyszíni futtatása.

Biztonsági szempontból előnyökkel járhat az Azure Active Directory (AD) megadhatják a:

* Létrehozásához és kezeléséhez minden felhasználó egy egyetlen identitást a hibrid vállalat, felhasználók, csoportok és az eszközök szinkronban tartása
* Adja meg az egyszeri bejelentkezéses hozzáférést több ezer előre integrált Szolgáltatottszoftver-alkalmazásoknál, beleértve az alkalmazások
* Engedélyezze a hozzáférést alkalmazásbiztonsági mind a helyszíni szabályalapú többtényezős hitelesítés kényszerítése, és a felhőalapú alkalmazásokhoz
* A helyszíni webalkalmazások Azure AD-proxyn keresztül történő biztonságos táveléréséhez kiépítése

Ez a cikk célja az identity management alkalmazással az Azure biztonsági szolgáltatásokat az alapvető áttekintés biztosítása. Azt adja meg egyes szolgáltatások az adatait, így további hivatkozásokat is adja meg.  

A cikk foglalkozik, a következő alapvető Azure identitáskezelési funkciói:

* Egyszeri bejelentkezés
* Fordított proxy
* Multi-Factor Authentication
* Biztonsági figyelést, a riasztások és a machine learning-alapú jelentések
* A felhasználói identitások és hozzáférés kezelése
* Eszközregisztráció
* Privileged identity management
* Identity Protection
* Hibrid Identitáskezelés

## <a name="single-sign-on"></a>Egyszeri bejelentkezés
Egyszeri bejelentkezés (SSO) azt jelenti, hogy nem tudnak hozzáférni az alkalmazások és az üzleti tevékenységet, egyszer csak egyetlen felhasználói fiókkal a bejelentkezéssel szükséges erőforrások. Miután bejelentkezett, van-e hozzáférési összes szolgáltatásbeli hitelesítéshez szükséges nélkül kell alkalmazást (például adjon meg egy jelszót) még egyszer.

Számos szervezet számítson arra, hogy szoftver alkalmazásokként egy szolgáltatott szoftverként (SaaS) például Office 365, a mezőben és a Salesforce a végfelhasználó hatékonyságát. Hagyományosan informatikai munkatársak külön-külön létrehozásához, és minden SaaS-alkalmazás a felhasználói fiókok frissítése szükséges, és a felhasználóknak kellett jegyezze meg a jelszót az egyes SaaS-alkalmazáshoz.

Az Azure AD kiterjeszti a helyszíni Active Directory-környezeteket kiterjeszti a felhőbe, így a felhasználók használhatják a saját elsődleges szervezeti nem csak a tartományhoz csatlakoztatott eszközökre bejelentkezéshez és a vállalati erőforrásokat, de szükséges is minden a web- és SaaS-alkalmazásokhoz a feladat.

Nem csak felhasználóknak nem kell több példányban felhasználónevek és jelszavak kezelése, alkalmazás-hozzáférés csak alapján automatikusan kiosztott vagy vonja kiosztott szervezeti csoportok és az állapotuk egy alkalmazott. Az Azure AD vezet be, biztonsági és hozzáférés-irányítási szabályozási, amelyek segítségével központilag kezelheti a felhasználói hozzáférés SaaS-alkalmazások között.

További információ:

* [Az egyszeri bejelentkezés – áttekintés](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Azure Active Directoryval az egyszeri bejelentkezés integrálása SaaS-alkalmazásokhoz](../active-directory/active-directory-enterprise-apps-manage-sso.md)

## <a name="reverse-proxy"></a>Fordított proxy
Az Azure AD-alkalmazásproxy lehetővé teszi, hogy a helyszíni alkalmazások, például közzététele [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) helyek, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), és [IIS](http://www.iis.net/)-alapú alkalmazások a magánhálózaton belül és biztonságos hozzáférést biztosít a felhasználók a hálózaton kívülről. Alkalmazásproxy távoli hozzáférést és egyszeri bejelentkezés (SSO) biztosít számos különböző típusú, amely támogatja az Azure AD SaaS-alkalmazásokhoz több ezer a helyszíni alkalmazások. Az alkalmazottak bejelentkezhet az alkalmazások a saját eszközükön otthoni és a felhő alapú proxyn keresztül történő hitelesítéséhez.

További információ:

* [Az Azure AD-alkalmazásproxy engedélyezése](../active-directory/manage-apps/application-proxy-enable.md)
* [Az Azure AD-alkalmazásproxy használó alkalmazások közzététele](../active-directory/active-directory-application-proxy-publish.md)
* [Single-sign-on a Proxy](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés használata](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Az Azure többtényezős hitelesítés (MFA), amely egynél több ellenőrzési módszer használatát igényli, és a kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat hitelesítési mód. Többtényezős hitelesítés segítségével hozzáférés biztonságossá tételét adatokhoz és alkalmazásokhoz egyszerű bejelentkezési folyamatot a felhasználó igény szerint betartása mellett. Erős hitelesítés, ellenőrzési lehetőségek széles keresztül biztosítja – a telefonhívás, szöveges üzenet vagy mobilalkalmazás értesítés vagy ellenőrző kód és a külső OAuth jogkivonatokat.

További információ:

* [Többtényezős hitelesítés](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Azure multi-factor Authentication működése](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Biztonsági figyelést, a riasztások és a machine learning-alapú jelentések
A biztonság ellenőrzése és a riasztások és a machine learning-alapú jelentések, amelyek azonosítják az inkonzisztens hozzáférési mintázatokat segítségével védelmet az üzleti. Azure Active Directory hozzáférési és használati jelentések segítségével hogy lássák az integritásra és a munkahely címtárában biztonságát. Ezt az információt a directory-rendszergazda is jobban meghatározhatja, ahol lehetséges biztonsági kockázatokat a vizsgálandó, hogy azok megfelelően megtervezheti kockázatok csökkentésének lehetőségeit.

Az Azure-portálon, a jelentések szerint vannak kategóriába sorolva a következőképpen:

* Az anomáliadetektálási jelentések – eseményeket, amelyek jelenleg található a rendellenes bejelentkezési tartalmaznak. Célunk, ellenőrizze, hogy tisztában legyen ilyen tevékenység, és lehetővé teszik a tudni győződjön meg arról, hogy az esemény gyanúsnak meghatározása.
* Integrált alkalmazás jelentések – betekintést, hogyan használja a szervezet a felhőalapú alkalmazásokhoz. Az Azure Active Directory integrálható a felhőalapú alkalmazások ezer.
* Hibajelentések – azt jelzi, hogy a külső alkalmazásokba fiókok létesítésekor előforduló hibákat.
* Felhasználó-specifikus jelentései – eszköz/sign tevékenységek adatai egy adott felhasználó jelenítenek meg.
* Tevékenység – tartalmaznak minden naplózott eseményeket rögzíti a elmúlt 24 óra, a legutóbbi 7 nap, vagy a utolsó 30 nap során, és a csoport tevékenység módosításainak és a jelszó alaphelyzetbe állítása és nyilvántartási tevékenység belül.

További információ:

* [A hozzáférési és használati jelentések megtekintése](../active-directory/active-directory-view-access-usage-reports.md)
* [Ismerkedés az Azure Active Directory-jelentéskészítés](../active-directory/active-directory-reporting-getting-started.md)
* [Az Azure Active Directory-jelentéskészítés – útmutató](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>A felhasználói identitások és hozzáférés kezelése
Az Azure Active Directory B2C egy magas rendelkezésre állású, globális, identitás szolgáltatást a felhasználók felé néző alkalmazások száz millió identitások alkalmazkodnak. Mobil- és webes platformokba is integrálható. A felhasználók testreszabható felületeken, meglévő közösségi hálózati fiókjaikkal vagy új hitelesítő adatok létrehozásával jelentkezhetnek be minden alkalmazásába.

A múltban gyakori volt, hogy a felhasználók alkalmazásokra való regisztrációját és bejelentkezését megvalósítani kívánó alkalmazásfejlesztők maguk írták meg az ehhez szükséges kódot. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Az Azure Active Directory B2C integrálniuk a felhasználói Identitáskezelés alkalmazások segítségével biztonságos, szabványokon alapuló platformja és bővíthető szabályzatainak számos fejlettebb módszert kínál a szervezete.

Azure Active Directory B2C használata esetén a felhasználók regisztrálhatnak az alkalmazások új hitelesítő adatok (e-mail címet és jelszót, vagy felhasználónév és jelszó) létrehozásával vagy meglévő közösségi fiókjaik használatával (Facebook, Google, Amazon, LinkedIn).

További információ:

* [Mi az Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C előzetes verzió: bejelentkezés regisztrálása és beléptetése az alkalmazások a fogyasztói](../active-directory-b2c/active-directory-b2c-overview.md)
* [Az Azure Active Directory B2C előzetes verziója: Típusú alkalmazások](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Eszközregisztráció
Az Azure AD Eszközregisztrációval az alapja az eszközalapú [feltételes hozzáférés](../active-directory/active-directory-conditional-access-device-registration-overview.md) forgatókönyvek. Amikor regisztrál egy eszközt, az Azure Active Directory Eszközregisztráció látja el az eszközt, amikor a felhasználó bejelentkezik az eszköz hitelesítésére használt identitással. A hitelesített eszköz és az eszköz attribútumai ezután a feltételes hozzáférési házirendek betartatásához használhatók a felhőben és a helyszínen tárolt alkalmazások esetében.

Például az Intune mobileszköz-kezelési (MDM) megoldás kombinálva az Azure Active Directoryban tulajdonságok frissítése eszközzel kapcsolatos további információkat. Ez lehetővé teszi további feltételes hozzáférési szabályok létrehozását, amelyek arra kényszerítik az eszközhozzáféréseket, hogy megfeleljenek a biztonsági és megfelelőségi szabványoknak.

További információ:

* [Ismerkedés az Azure Active Directory Eszközregisztrációjával](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatikus eszközregisztráció az Azure Active Directory for Windows-tartományhoz csatlakozó eszközök](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Állítsa be az automatikus regisztráció, a Windows Azure Active Directory tartományhoz csatlakozó eszközök](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged identity management
Az Azure Active Directory (AD) Privileged Identity Management lehetővé teszi az emelt szintű identitások kezelését, irányítását és felügyeletét, és hozzáférést biztosít az Azure AD és más online szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz.

Néha felhasználók el kell végezniük a jogosultságokhoz kötött műveletek Azure vagy az Office 365 erőforrásokat, vagy más SaaS-alkalmazásokhoz. Ez gyakran azt jelenti, hogy a szervezetek meg kell adni nekik az Azure AD állandó privilegizált hozzáférési jogosultsága. Ez a felhőben üzemeltetett erőforrásokhoz az egyre növekvő biztonsági kockázatot jelent, mert a szervezeteknek elég nem tud figyelni, ezek a felhasználók tevékenységeit a rendszergazda jogosultságokkal. Továbbá ha jogosultsági szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, egy megsértésének jelentős hatással lehet a felhő átfogó biztonsági. Az Azure AD Privileged Identity Management segít a kockázat megoldásához.

Az Azure AD Privileged Identity Management lehetővé teszi:

* Mely felhasználók vannak-e az Azure AD-rendszergazdák
* Engedélyezze az igény, "igény szerint" a Microsoft Online Services rendszergazdai hozzáféréssel, például Office 365 és az Intune-ban
* Rendszergazda-hozzárendelések beolvasása a rendszergazdai hozzáférési műveleteiről és a változások
* Egy kiemelt szerepkörhöz való hozzáféréssel kapcsolatos riasztásokat kaphat

További információ:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Szerepkörök az Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Az Azure AD Privileged Identity Management: Hogyan lehet hozzáadni vagy eltávolítani egy felhasználói szerepkör](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identity Protection
Az Azure AD Identity Protection olyan biztonsági szolgáltatás, amely a kockázati eseményekről és a szervezet identitásait érintő lehetséges biztonsági rések egyesített nézetét biztosítja. Azonosító adatok védelmét kihasználja a meglévő Azure Active Directory anomáliadetektálási az észlelési képességek (az Azure AD rendellenes Tevékenységjelentések keresztül elérhető), és vezet be új kockázat típusait, amely valós idejű rendellenességek észlelését.

További információ:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [9. csatornán: Az Azure AD és az Identity: Identity Protection előzetes kiadásának](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hibrid Identitáskezelés
A Microsoft identity megközelítése is a helyszíni és a felhő létrehozása a hitelesítés és engedélyezés az összes erőforráshoz, függetlenül a hely egyetlen felhasználói azonosítót.

További információ:

* [Hibrid identitáskezelési dokumentáció](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Active Directory csapat blogja](https://blogs.technet.microsoft.com/ad/)
