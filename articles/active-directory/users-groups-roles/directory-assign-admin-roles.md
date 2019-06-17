---
title: Rendszergazdai szerepköreinek leírása és az engedélyeket – Azure Active Directory |} A Microsoft Docs
description: Rendszergazda szerepkörrel is adja hozzá a felhasználók, rendszergazdai szerepkörök hozzárendelése, felhasználók új jelszavainak létrehozására, a felhasználói licencek kezelése vagy tartományok kezelése.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 06/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ca740a4dcca3bdbb1951e55df4061364a5c646
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083923"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Az Azure Active Directory rendszergazdája szerepkör engedélyei

Az Azure Active Directory (Azure AD), kisebb szintű jogosultsággal rendelkező szerepkörök az identitás a feladatokat a korlátozott rendszergazdák is kijelölhet. Rendszergazdák hozzáadása vagy módosítása a felhasználók, rendszergazdai szerepkörök hozzárendelése, felhasználói jelszó alaphelyzetbe állítása, a felhasználói licencek kezelése és tartománynevek kezelése, az ilyen célokra is hozzárendelhető. Az alapértelmezett felhasználói engedélyek csak a felhasználói beállítások módosítható az Azure ad-ben.

A globális rendszergazda az összes rendszergazdai funkciójához hozzáfér. Alapértelmezés szerint az Azure-előfizetésre feliratkozó személy van rendelve a címtár globális rendszergazdai szerepkörrel. Csak a globális rendszergazdák és a kiemelt szerepkörű rendszergazdák delegálhatja a rendszergazdai szerepköröket. Az Ön számára azt javasoljuk, hogy ehhez a szerepkörhöz való hozzárendelése csak néhány személy a vállalat kockázatának csökkentése érdekében.


## <a name="assign-or-remove-administrator-roles"></a>Vagy vonja meg a rendszergazdai szerepkörökről

Rendszergazdai szerepkörök hozzárendelése az Azure Active Directory felhasználó kapcsolatban lásd: [nézet és rendelhet hozzá rendszergazdai szerepköröket az Azure Active Directoryban](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Elérhető szerepkörök

A következő rendszergazdai szerepkörök érhetők el:

* **[Alkalmazás-rendszergazda](#application-administrator)** : Az ehhez a szerepkörhöz felhasználók létrehozhatják és vállalati alkalmazásokat, alkalmazásregisztrációkat és alkalmazásproxy-beállítások minden szempontjának kezeléséhez. Ez a szerepkör lehetővé teszi, hogy engedélyt adjanak az delegált engedélyeket, és az Alkalmazásengedélyek, kivéve a Microsoft Graph és az Azure AD Graph is nyújt. Ehhez a szerepkörhöz tartozó felhasználók nem kerülnek tulajdonosként, amikor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozása.

  <b>Fontos</b>: A szerepkörök képes kezelni az alkalmazás hitelesítő adatait. Ehhez a szerepkörhöz tartozó felhasználók hitelesítő adatok hozzáadása egy alkalmazáshoz, és ezeket a hitelesítő adatokat használja a megszemélyesíteni az alkalmazás azonosítóját. Ha az alkalmazás azonosítóját hozzáférési engedélyt kapott az Azure Active Directoryhoz, például létrehozni vagy frissíteni a felhasználói és más objektumok, majd ehhez a szerepkörhöz hozzárendelt felhasználó sikerült végrehajtani az alkalmazás megszemélyesítése közben ezeket a műveleteket. Ezt a lehetőséget az alkalmazás identitás megszemélyesítése lehet egy megszerzését keresztül a felhasználó mit tehetnek a szerepkör-hozzárendeléseket keresztül az Azure ad-ben. Fontos megérteni, hogy a felhasználó az alkalmazás-rendszergazda szerepkör hozzárendelése biztosít az Alkalmazásidentitás megszemélyesítését.

* **[Alkalmazás fejlesztője](#application-developer)** : A szerepkör felhasználói alkalmazásregisztrációkat hozhat létre. Ha a "Felhasználók regisztrálhatnak alkalmazásokat" beállítás értéke nem. Ez a szerepkör is megadja az engedélyt adni a saját nevében amikor a "Felhasználók engedélyezhetik, hogy az alkalmazások hozzáférjenek a céges adatok saját nevükben való" beállítás értéke nem. Ehhez a szerepkörhöz tartozó felhasználók tulajdonosként való hozzáadásakor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozásakor.

* **[Hitelesítési rendszergazda](#authentication-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók beállíthatja vagy a jelszó-hitelesítő adatok alaphelyzetbe állítása. Hitelesítés a rendszergazdák megkövetelhetik regisztrálja újra a meglévő jelszó-hitelesítő adat (például a többtényezős hitelesítés vagy a FIDO) ellen, és visszavonását felhasználók **MFA megjegyzése az eszközön**, amely felszólítja a multi-factor Authentication a következő bejelentkezéskor a felhasználók, akik a nem rendszergazdai vagy csak a következő szerepkörök:
  * Hitelesítési rendszergazda
  * Címtárolvasók
  * Vendég meghívója
  * Üzenetközpont olvasó
  * Jelentésolvasó

  A hitelesítést rendszergazda szerepkör jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Fontos</b>: Ezzel a szerepkörrel rendelkező felhasználók személyek férhetnek hozzá a bizalmas vagy titkos információk vagy kritikus konfigurációs belüli és kívüli Azure Active Directory hitelesítő adatok módosításához. A felhasználó hitelesítő adatainak módosítása jelentheti azt feltételezik, hogy felhasználói identitás- és engedélyek lehetővé teszi. Példa:

  * Alkalmazás regisztrálása és a vállalati alkalmazás tulajdonosok, aki kezelheti az alkalmazásokat a saját hitelesítő adatait. Ezeknek az alkalmazásoknak előfordulhat, hogy emelt szintű engedélyek az Azure ad-ben és a hitelesítési rendszergazdák máshol nem kapott. Ez lehet, hogy az alkalmazás tulajdonosa identitását, és ezután további hitelesítést rendszergazda elérési útján feltételezik egy emelt szintű alkalmazáshoz a hitelesítő adatokat, az alkalmazás frissítésével.
  * Azure-előfizetéssel rendelkező, előfordulhat, hogy hozzáféréssel rendelkező bizalmas vagy titkos információk vagy kritikus fontosságú konfigurációját az Azure-ban.
  * Biztonsági csoport és az Office 365-csoport tulajdonosainak, csoport tagságának kezelésére jogosult. Ezeket a csoportokat a bizalmas vagy titkos információk, vagy a kritikus konfigurálása az Azure ad-ben és a máshol hozzáférést biztosíthat.
  * A rendszergazdák az Azure AD-en kívül más szolgáltatásokkal, például Exchange online-hoz, Office biztonsági és megfelelőségi központban és emberi erőforrások rendszerek.
  * A nem rendszergazdák, például a vezetők, a védőt és a személyzeti osztályon dolgozóknak, akik esetleg bizalmas vagy személyes információkhoz való hozzáférés.

* **[Az Azure Information Protection-rendszergazda](#azure-information-protection-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók minden engedéllyel rendelkezni az Azure Information Protection szolgáltatásban. Ez a szerepkör lehetővé teszi, hogy címkéket az Azure Information Protection-házirend konfigurálása, a védelmi sablonokat kezeléséhez és a védelem aktiválása. Ez a szerepkör nem biztosít az Identity Protection Centerben, Privileged Identity Management, Monitor Office 365 Service Healthben vagy Office 365 Security & Compliance Centerben engedélyeket.

* **[B2C felhasználói folyamat rendszergazdája](#b2c-user-flow-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók létrehozhatják és B2C felhasználói folyamatok (más néven "beépített" szabályzatokat) az Azure Portalon kezelheti. Létrehozásával, vagy a felhasználói folyamatok szerkesztésével, ezek a felhasználók a felhasználói élmény a html/CSS és javascript tartalmának módosítása, módosítsa a felhasználói folyamat / az MFA-követelmények, módosíthatja a jogkivonat jogcímeiben és a bérlő összes házirend beállítása a munkamenet. Másrészről ez a szerepkör nem ízelítő a tekintse át a felhasználói adatok, vagy módosítja az attribútumokat, amelyek szerepelnek a bérlői séma. Identitás-kezelőfelületi keretrendszer változik (más néven egyéni) házirendeket is ez a szerepkör hatókörén kívül van.

* **[B2C felhasználói folyamat attribútum rendszergazdája](#b2c-user-flow-attribute-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók hozzáadása vagy törlése az egyéni attribútumokat a bérlőben lévő összes felhasználói folyamatok számára elérhető. Mint ilyen ezzel a szerepkörrel rendelkező felhasználók módosíthatja vagy új elemeket ad hozzá a végfelhasználó sémát, és hatással van az összes felhasználói folyamatok viselkedését és közvetve módosítását eredményezi milyen adatokat előfordulhat, hogy a végfelhasználók a gyakori és végső soron alkalmazások jogcímekként küld. Ez a szerepkör felhasználói folyamatok nem szerkeszthető.

* **[B2C IEF kulcskészlet rendszergazda](#b2c-ief-keyset-administrator)** :    Felhasználó létrehozása és kezelése szabályzatbejegyzések, és titkos kulcsok a jogkivonat-titkosításhoz, jogkivonat-aláírás és jogcím-titkosítási/visszafejtési. Új kulcsok hozzáadása meglévő kulcstárolók, a korlátozott rendszergazda képes helyettesítő titkos kulcsok meglévő alkalmazások befolyásolása nélkül igény szerint. Ez a felhasználó teljes tartalmát, a titkos kulcsok és a lejárati dátumát, a létrehozás után is látható.
    
  <b>Fontos:</b> bizalmas szerepkör. A kulcskészletet rendszergazda szerepkör gondosan kell naplózni és körültekintően és az éles üzem előtti gyűjteményben kiosztott.

* **[B2C IEF csoportházirendet felügyelő rendszergazda](#b2c-ief-policy-administrator)** : A szerepkör felhasználói jogosultak a létrehozását, olvasását, frissítése, és törölje az összes egyéni szabályzatok Azure AD B2C-ben és teljes körűen felügyelve az identitás-kezelőfelületi keretrendszer ezért rendelkezik a megfelelő Azure AD B2C-bérlő. Házirendek szerkesztésével a felhasználó is külső identitásszolgáltatókkal közvetlen összevonás létesítéséhez, váltson a címtársémának, módosítsa a felhasználó által használt összes tartalmat (HTML, CSS, JavaScript-), végezze el a hitelesítést, az új felhasználók létrehozása, küldése követelményei a külső rendszerekkel, beleértve a felhasználói adatok teljes áttelepítés, és szerkesztheti az összes olyan felhasználó adatai, beleértve a bizalmas mezőket is, például a jelszavak és telefonszámokat. Ezzel szemben ez a szerepkör nem módosítható a titkosítási kulcsokat, és a titkos kulcsok a bérlő összevonási használt szerkesztése.

  <b>Fontos:</b> B2 IEF csoportházirendet felügyelő rendszergazda egy szigorúan bizalmas szerepkört, amely hozzá kell rendelni egy rendkívül szűk körben bocsátjuk rendelkezésre az éles környezetben a bérlők számára. Ezek a felhasználók által tevékenységek szorosan naplózni, különösen az éles üzemi bérlők esetén.

* **[Számlázási adminisztrátor](#billing-administrator)** : Lebonyolítja a vásárlásokat, kezeli az előfizetéseket, támogatási jegyeket, és figyeli a szolgáltatás állapotát.

* **[Felhőalkalmazás-rendszergazda](#cloud-application-administrator)** : A szerepkör felhasználói képes kezelni az application proxy kivételével az alkalmazás-rendszergazda szerepkörnek ugyanazokkal az engedélyekkel rendelkezik. A szerepkörök létrehozása és kezelése vállalati alkalmazásokkal és az alkalmazást az alkalmazásregisztrációk minden aspektusát. Ez a szerepkör lehetővé teszi, hogy engedélyt adjanak az delegált engedélyeket, és az Alkalmazásengedélyek, kivéve a Microsoft Graph és az Azure AD Graph is nyújt. Ehhez a szerepkörhöz tartozó felhasználók nem kerülnek tulajdonosként, amikor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozása.

  <b>Fontos</b>: A szerepkörök képes kezelni az alkalmazás hitelesítő adatait. Ehhez a szerepkörhöz tartozó felhasználók hitelesítő adatok hozzáadása egy alkalmazáshoz, és ezeket a hitelesítő adatokat használja a megszemélyesíteni az alkalmazás azonosítóját. Ha az alkalmazás azonosítóját hozzáférési engedélyt kapott az Azure Active Directoryhoz, például létrehozni vagy frissíteni a felhasználói és más objektumok, majd ehhez a szerepkörhöz hozzárendelt felhasználó sikerült végrehajtani az alkalmazás megszemélyesítése közben ezeket a műveleteket. Ezt a lehetőséget az alkalmazás identitás megszemélyesítése lehet egy megszerzését keresztül a felhasználó mit tehetnek a szerepkör-hozzárendeléseket keresztül az Azure ad-ben. Fontos megérteni, hogy egy felhasználó hozzárendelése a Felhőalkalmazás-rendszergazda szerepkört biztosít az Alkalmazásidentitás megszemélyesítését.

* **[Felhőbeli Eszközrendszergazda](#cloud-device-administrator)** : A szerepkör felhasználói engedélyezheti, letilthatja, és eszközök törlése az Azure ad-ben és (ha van ilyen) olvassa el a Windows 10-es BitLocker-kulcsok az Azure Portalon. A szerepkör nem biztosítanak engedélyeket más tulajdonságokat, az eszköz kezelésére.

* **[Szabályozási ügyintéző](#compliance-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók a Microsoft 365-megfelelőségi központ, a Microsoft 365 felügyeleti központban, Azure-ban, és Office 365 biztonsági és megfelelőségi központban a megfelelőséggel kapcsolatos funkciók kezeléséhez engedélyekkel rendelkezik. A felhasználók is felügyeli az összes belül az Exchange felügyeleti központot és a csapatok & Skype for Business felügyeleti központban, és hozzon létre támogatási jegyeket Azure és a Microsoft 365. További információt [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  A | Teheti meg
  ----- | ----------
  [A Microsoft 365-megfelelőségi központ](https://protection.office.com) | Védelme és kezelése a szervezet adatai között a Microsoft 365-szolgáltatásokhoz<br>Megfelelőségi riasztások kezelése
  [Megfelelőségi Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Nyomon követheti, hozzárendelése és ellenőrzése a szervezet a jogszabályoknak való megfelelőség tevékenységek
  [Az Office 365 Security & Compliance Centerben](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Adatirányítás kezelése<br>Jogi tudnivalók és adatok vizsgálat végrehajtása<br>Tulajdonos kérelem kezelése
  [Intune-ban](https://docs.microsoft.com/intune/role-based-access-control) | Az összes Intune-ban naplózási adatok megtekintése
  [A cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási engedélyekkel rendelkezik, és riasztásokat kezelhet<br>Hozzon létre, és módosíthatja a fájlhasználati szabályzatok és cégirányítási műveletek engedélyezése<br> Megtekintheti a beépített jelentések adatok felügyelete alatt

* **[Megfelelőségi adatok rendszergazdai](#compliance-data-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók védelmét, és nyomon követheti a a Microsoft 365-megfelelőségi központ, a Microsoft 365 felügyeleti központ és az Azure data engedélye. A felhasználók is kezelheti az Exchange felügyeleti központban, Compliance Manager, és a Teams és a Skype for Business felügyeleti központban minden szolgáltatásokat és hozzon létre támogatási jegyeket Azure és a Microsoft 365.

  A | Teheti meg
  ----- | ----------
  [A Microsoft 365-megfelelőségi központ](https://protection.office.com) | Megfelelőséggel kapcsolatos szabályzatok figyelése a Microsoft 365-szolgáltatásokhoz<br>Megfelelőségi riasztások kezelése
  [Megfelelőségi Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Nyomon követheti, hozzárendelése és ellenőrzése a szervezet a jogszabályoknak való megfelelőség tevékenységek
  [Az Office 365 Security & Compliance Centerben](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Adatirányítás kezelése<br>Jogi tudnivalók és adatok vizsgálat végrehajtása<br>Tulajdonos kérelem kezelése
  [Intune-ban](https://docs.microsoft.com/intune/role-based-access-control) | Az összes Intune-ban naplózási adatok megtekintése
  [A cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási engedélyekkel rendelkezik, és riasztásokat kezelhet<br>Hozzon létre, és módosíthatja a fájlhasználati szabályzatok és cégirányítási műveletek engedélyezése<br> Megtekintheti a beépített jelentések adatok felügyelete alatt

* **[Feltételes hozzáférésű rendszergazda](#conditional-access-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók képesek az Azure Active Directory feltételes hozzáférés beállításainak ellenőrzése.
  > [!NOTE]
  > Az Azure-ban az Exchange ActiveSync feltételes hozzáférési házirend telepítése, a felhasználó is egy globális rendszergazdának kell lennie.
  
* **[Ügyfél Ügyfélszéf hozzáférés-jóváhagyója](#customer-lockbox-access-approver)** : Kezeli a [ügyfél kulcstároló-kérelmek](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) a szervezetben. Azok a vásárlói kulcstároló kéréseket email értesítéseket kapni és hagyhat jóvá és kérések elutasítása a Microsoft 365 felügyeleti központban. Az ügyfél kulcstároló funkció azokat is be, és ki. Csak a globális rendszergazdák ehhez a szerepkörhöz rendelt felhasználók visszaállíthatják.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Asztali Analytics rendszergazdája](#desktop-analytics-administrator)** : A szerepkör felhasználói az asztali Analytics és az Office testreszabási & házirend szolgáltatások kezelheti. Asztali Analytics például a eszközleltár megtekintése, központi telepítési csomagok létrehozása, üzembe helyezési és egészségügyi állapotának megtekintéséhez. Az Office testreszabási & szabályzat szolgáltatáshoz Ez a szerepkör lehetővé teszi a felhasználók Office házirendek kezelése.

* **[Eszköz-rendszergazdai](#device-administrators)** : Ez a szerepkör csak egy további helyi rendszergazdaként a hozzárendelés érhető el [eszközbeállítások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Ezzel a szerepkörrel rendelkező felhasználók minden, az Azure Active Directoryhoz csatlakoztatott Windows 10 rendszerű eszközökön a helyi gépek rendszergazdái lesznek. Nem rendelkeznek képes kezelni az Azure Active Directoryban. 

* **[Címtárolvasók](#directory-readers)** : Ez a szerepkör, amely csak az örökölt alkalmazásokat, amelyek nem támogatják hozzá kell rendelni a [hozzájárulási keretrendszer](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ne rendeljen a felhasználók számára.

* **[Címtár-szinkronizálási fiókok](#directory-synchronization-accounts)** : Ne használja. Ez a szerepkör automatikusan hozzá lesz rendelve az Azure AD Connect szolgáltatást, és nem szándékos vagy bármely más használata támogatott.

* **[Címtárírók](#directory-writers)** : Ez egy örökölt szerepkör, amely hozzá kell rendelni az alkalmazásokat, amelyek nem támogatják a a [hozzájárulási keretrendszer](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Azt nem lehet hozzárendelni egy felhasználóhoz sem.

* **[Dynamics 365-rendszergazda vagy CRM-rendszergazda](#crm-service-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók globális engedélyekkel rendelkeznek a Microsoft Dynamics 365 online-ban, ha a szolgáltatás nem található, ezenkívül kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése. További információ: [a szolgáltatás-rendszergazdai szerepkör használata kezelheti a bérlő](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja "Dynamics 365-Szolgáltatásadminisztrátor". Az "Dynamics 365-rendszergazda" a [az Azure portal](https://portal.azure.com).

* **[Exchange-rendszergazda](#exchange-service-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Exchange online-ban, globális engedélyekkel rendelkeznek, a szolgáltatás megléte esetén. Is van lehetősége, létrehozása és kezelése az Office 365-csoportok, kezelhetik a támogatási jegyeket és szolgáltatások állapotának figyelése. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "Exchange-szolgáltatások rendszergazdája". Az "Exchange-rendszergazda" a [az Azure portal](https://portal.azure.com). Az "Az Exchange Online rendszergazdai" a [Exchange felügyeleti központban](https://go.microsoft.com/fwlink/p/?LinkID=529144). 

* **[Külső azonosító nyújtó rendszergazdája](#external-identity-provider-administrator)** : Ez a rendszergazda kezeli az összevonást az Azure Active Directory-bérlők és a külső Identitásszolgáltatók között. Ezzel a szerepkörrel rendelkező felhasználók új Identitásszolgáltatók felveheti és konfigurálhatja az összes rendelkezésre álló beállítások (például a hitelesítési útvonal, szolgáltatásazonosító kulcstárolók hozzárendelt). Ez a felhasználó engedélyezheti a bérlő számára, hogy megbízzon a külső identitás-szolgáltatóktól származó hitelesítések. Az eredményül kapott végfelhasználói élményt hatása attól függ, hogy a bérlő típusát:
  * Az Azure Active Directory bérlők számára az alkalmazottak és partnerek számára: Egy összevonási (például a Gmail) is azonnal hatással van minden Vendég meghívók még nem válthatók be. Lásd: [Identitásszolgáltatóként B2B vendégfelhasználó hozzáadása Google](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Az Azure Active Directory B2C-bérlők: Egy összevonási (pl. a Facebookkal vagy egy másik Azure Active Directoryval) is nem befolyásolja közvetlenül a végfelhasználói folyamatok mindaddig, amíg az identitásszolgáltató adnak lehetőséget a felhasználói folyamat (más néven beépített házirend). Lásd: [Microsoft-fiók konfigurálása identitás-szolgáltatóként](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) példaként. Felhasználói folyamatok módosításához a "B2C felhasználói folyamat rendszergazda" korlátozott szerepe szükség.

* **[Globális / vállalati rendszergazda](#company-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók férhetnek hozzá az Azure Active Directoryval, valamint a szolgáltatások, például a security center a Microsoft 365, Azure Active Directory-identitásokat használó összes rendszergazdai funkciójához Microsoft 365 megfelelőségi központ, az Exchange Online, SharePoint online-hoz, és Skype vállalati Online verzióhoz. A személy, aki regisztrál az Azure Active Directory-bérlő globális rendszergazdája lesz. Csak a globális rendszergazdák más rendszergazdai szerepköröket rendelhet hozzá. A vállalat a egynél több globális rendszergazda is lehet. A globális rendszergazdák bármely felhasználó és minden más rendszergazdák jelszavát is alaphelyzetbe.

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja a folyamat "Vállalati rendszergazdaként". Az "Globális rendszergazda" a [az Azure portal](https://portal.azure.com).
  >
  >

* **[Vendég meghívója](#guest-inviter)** : Az ehhez a szerepkörhöz felhasználók kezelhetik az Azure Active Directory B2B vendégfelhasználói meghívókat során a **tagok küldhetnek meghívót** felhasználói beállítás értéke nem. További információ a B2B-együttműködés: [kapcsolatos Azure AD B2B együttműködés](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nem tartalmaz semmilyen más engedélyt.

* **[Intune-rendszergazda](#intune-service-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Intune Online globális engedélyekkel rendelkeznek, ha a szolgáltatás nem található. Ez a szerepkör ezenkívül tartalmazza a képes kezelni a felhasználók és eszközök számára annak érdekében, hogy társítja a házirendet, valamint csoportok létrehozásához és kezeléséhez. További információ: [szerepköralapú vezérlés (RBAC) a Microsoft Intune-nal](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "Intune-Szolgáltatásadminisztrátor". Az "Intune-rendszergazda" a [az Azure portal](https://portal.azure.com).
  
 * **[Kaizala rendszergazda](#kaizala-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók kezeléséhez a Microsoft Kaizala, ha jelen a szolgáltatás beállításainak szolgáltatásuk, ezenkívül kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése globális engedélyekkel rendelkeznek.
Emellett a felhasználó hozzáférhessen a szervezet tagjai és üzleti jelentéseket a Kaizala műveletek használatával létrehozott Kaizala elfogadása és kapcsolatos jelentéseket. 

* **[Licencek adminisztrátora](#license-administrator)** : Az ehhez a szerepkörhöz felhasználók hozzáadhatnak, távolítsa el, és a frissítés licenc-hozzárendeléseket felhasználók, csoportok (Csoportalapú licencelést használ), és a felhasználási hely, a felhasználók kezeléséhez. A szerepkör nem teszi lehetővé a beszerzési vagy -előfizetések kezelése, hozzon létre vagy kezelheti a csoportokat, vagy hozzon létre vagy kezelni a felhasználókat a felhasználási hely túli biztosít. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[Üzenetközpont adatvédelmi olvasó](#message-center-privacy-reader)** : A szerepkör felhasználói minden az üzenetközpont, beleértve az adatvédelmi adatüzenetek figyelje is. Üzenetközpont adatvédelmi olvasók többek között a kapcsolatos, az adatvédelem e-mail értesítéseket kaphat, és leiratkozhatnak üzenetközpont beállításainak használatával. Csak a globális rendszergazda és az üzenet Center adatvédelmi olvasó adatvédelmi adatüzenetek olvashatja. Ez a szerepkör ezenkívül tartalmazza a csoportok, a tartományok és az előfizetések megtekintése. Ez a szerepkör nem jogosult az megtekintése, létrehozása és kezelhetik.

* **[Üzenetközpont olvasó](#message-center-reader)** : Figyelheti, hogy a szerepkör felhasználói értesítések és a tanácsadás egészségügyi módosításokat [Office 365 üzenetközpontjában](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) konfigurált szolgáltatások, például az Exchange, az Intune-ban és a Microsoft Teams a szervezeten belül. Üzenetközpont heti e-mail emésztett hozzászólás, frissítések fogadása és oszthatnak meg üzenet center bejegyzések az Office 365-ben. Az Azure AD-ben ehhez a szerepkörhöz tartozó felhasználók csak van csak olvasási hozzáféréssel az Azure AD szolgáltatások, például a felhasználók és csoportok. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[1. rétegbeli támogatása partneri](#partner-tier1-support)** : Ne használja. Ez a szerepkör elavult és törlődni fog a későbbiekben az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partnerek általi használatra van, és nem célja általános használatra.

* **[2. rétegbeli támogatása partneri](#partner-tier2-support)** : Ne használja. Ez a szerepkör elavult és törlődni fog a későbbiekben az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partnerek általi használatra van, és nem célja általános használatra.

* **[(Jelszó) ügyfélszolgálati adminisztrátor](#helpdesk-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók is módosíthatja a jelszavát, érvényteleníti frissítési biztonsági jogkivonat, szolgáltatáskérések kezelése és szolgáltatások állapotának figyelése. A frissítési jogkivonatok érvénytelenítése kényszeríti a felhasználót, hogy jelentkezzen be újra. Segélyszolgálat rendszergazdák új jelszót és más felhasználók, akik nem rendszergazdák, vagy csak a következő szerepkörökhöz rendelt frissítési biztonsági jogkivonat érvénytelenné:
  * Címtárolvasók
  * Vendég meghívója
  * Ügyfélszolgálati adminisztrátor
  * Üzenetközpont olvasó
  * Jelentésolvasó
  
  <b>Fontos</b>: Ezzel a szerepkörrel rendelkező felhasználók jelszavait változtathatják felhasználók férhetnek hozzá a bizalmas vagy titkos információk vagy kritikus konfigurációs belüli és kívüli Azure Active Directory számára. A felhasználó jelszavának módosítása jelentheti azt feltételezik, hogy felhasználói identitás- és engedélyek lehetővé teszi. Példa:
  * Alkalmazás regisztrálása és a vállalati alkalmazás tulajdonosok, aki kezelheti az alkalmazásokat a saját hitelesítő adatait. Ezeket az alkalmazásokat lehet, hogy emelt szintű engedélyek az Azure ad-ben és az ügyfélszolgálat adminisztrátorai máshol nem kapja. Ez lehet, hogy egy ügyfélszolgálati adminisztrátor alkalmazás tulajdonosa identitását, és ezután további elérési útján feltételezik egy emelt szintű alkalmazáshoz a hitelesítő adatokat, az alkalmazás frissítésével.
  * Azure-előfizetéssel rendelkező, előfordulhat, hogy hozzáféréssel rendelkező bizalmas vagy titkos információk vagy kritikus fontosságú konfigurációját az Azure-ban.
  * Biztonsági csoport és az Office 365-csoport tulajdonosainak, csoport tagságának kezelésére jogosult. Ezeket a csoportokat a bizalmas vagy titkos információk, vagy a kritikus konfigurálása az Azure ad-ben és a máshol hozzáférést biztosíthat.
  * A rendszergazdák az Azure AD-en kívül más szolgáltatásokkal, például Exchange online-hoz, Office biztonsági és megfelelőségi központban és emberi erőforrások rendszerek.
  * A nem rendszergazdák, például a vezetők, a védőt és a személyzeti osztályon dolgozóknak, akik esetleg bizalmas vagy személyes információkhoz való hozzáférés.


  > [!NOTE]
  > Rendszergazdai engedélyek delegálása felhasználók alkészletek keresztül, és házirendek alkalmazása a felhasználók egy alhalmazára minden lehetséges a [felügyeleti egységek (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).


  > [!NOTE]
  > Ez a szerepkör korábbi neve "jelszókezelő" [az Azure portal](https://portal.azure.com/). Azért módosítottuk a neve "Ügyfélszolgálati adminisztrátor" egyeznie kell az Azure AD PowerShell-lel, az Azure AD Graph API és a Microsoft Graph API a nevével. Egy rövid ideig módosítjuk a neve "(jelszó) ügyfélszolgálati adminisztrátor", "Ügyfélszolgálati adminisztrátor" a változtatás előtt az Azure Portalon.


* **[A Power BI rendszergazdai](#power-bi-service-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése szolgáltatásuk, ezenkívül a Microsoft Power BI, a szolgáltatás megléte esetén globális engedélyekkel rendelkeznek. További információ: [a Power BI rendszergazdai szerepkörét ismertető](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "a Power BI-Szolgáltatásadminisztrátor". Az "a Power BI rendszergazdája" a [az Azure portal](https://portal.azure.com).

* **[Az emelt szintű hitelesítést rendszergazda](#privileged-authentication-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók beállíthatja vagy az összes felhasználó számára, beleértve a globális rendszergazdák a jelszó-hitelesítő adatok alaphelyzetbe állítása. Hitelesítési szintű jogosultsággal rendelkező rendszergazdák kényszerítheti a felhasználókat, regisztrálja újra a meglévő jelszó-hitelesítő adat (pl. MFA, FIDO) ellen, és visszavonhatja a "MFA megjegyzése az eszközön", a multi-factor Authentication a felhasználók a következő bejelentkezéshez kérő üzenet. Hitelesítési szintű jogosultsággal rendelkező rendszergazdák a következőket teheti:
  * Regisztrálja újra a meglévő jelszó-hitelesítő adat (pl. MFA, FIDO) szemben a felhasználókat
  * "MFA megjegyzése az eszközön", visszavonhatja a következő bejelentkezéshez a multi-factor Authentication értesítése

* **[Kiemelt szerepkörű rendszergazda](#privileged-role-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendelések az Azure Active Directoryban, valamint az Azure AD Privileged Identity Management belül. Emellett ez a szerepkör lehetővé teszi minden aspektusát Privileged Identity Management és a felügyeleti egységek kezelését.

  <b>Fontos</b>: A szerepkörök a minden Azure AD-szerepkörök, többek között a globális rendszergazdai szerepkör-hozzárendelések kezeléséhez. Ez a szerepkör nem tartalmaz semmilyen más emelt szintű képességek például létrehozása vagy frissítése a felhasználók Azure AD-ben. Azonban ebbe a szerepkörbe tartozó felhasználók adhat magukat vagy más további jogosultságok kiegészítő szerepkörök hozzárendelése.

* **[Jelentések olvasó](#reports-reader)** : Ezzel a szerepkörrel rendelkező felhasználók megtekinthetik a jelentéskészítési használati adatokat, és a Microsoft 365 felügyeleti központban, a jelentések irányítópultján, és a bevezetési környezeti csomag a Power bi-ban. Ezenkívül a szerepkör-bejelentkezés hozzáférést biztosít jelentéseket és a tevékenység az Azure ad-ben és a Microsoft Graph által visszaadott adatok jelentéskészítési API-t. A jelentések Olvasó szerepkörhöz rendelt felhasználó hozzáférhet, csak a releváns használati és mérőszámairól. Nincs szükség semmilyen konfigurálni vagy a hozzáférés a termékspecifikus felügyeleti központok, mint az Exchange-rendszergazdai jogosultságokkal. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[Keresés rendszergazda](#search-administrator)** : A szerepkör felhasználói minden Microsoft Search felügyeleti szolgáltatás teljes hozzáféréssel rendelkezik, a Microsoft 365 felügyeleti központban. Keresés a rendszergazdák delegálása, a Keresés a rendszergazdák és a keresési szerkesztő szerepköröket a felhasználókhoz, és hozzon létre és kezelheti a tartalmakat, például a könyvjelzőket, a Q & a, és a helyek. Ezek a felhasználók emellett az üzenetközpont megtekintéséhez, szolgáltatásállapot figyeléséhez és szolgáltatás-létrehozási kérések.

* **[Keresés szerkesztő](#search-editor)** : Az ehhez a szerepkörhöz felhasználók létrehozása, kezelése és törölhet tartalmakat a Microsoft 365 felügyeleti központban, beleértve a könyvjelzőket, a Q & a, a Microsoft Search és a helyek.

* **[Biztonsági rendszergazda](#security-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók biztonsági vonatkozású szolgáltatásairól a security center a Microsoft 365, Azure Active Directory Identity Protection, Azure Information Protection, és Office 365 Security & Compliance Centerben kezeléséhez engedélyekkel rendelkezik. Office 365-engedélyekkel kapcsolatos további információt [az engedélyek az Office 365 Security & Compliance Centerben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  A | Teheti meg
  --- | ---
  [A security center a Microsoft 365](https://protection.office.com) | Biztonsági szabályzatok figyelése a Microsoft 365-szolgáltatásokhoz<br>Biztonsági fenyegetések és a riasztások kezelése<br>Jelentések megtekintése
  Identity Protection Center | A biztonsági olvasó szerepkör összes engedélyt<br>Ezenkívül lehetővé teszi új jelszavak kivételével az összes Identity Protection Centerben műveletek végrehajtásához
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | A biztonsági olvasó szerepkör összes engedélyt<br>**Nem lehet** az Azure AD szerepkör-hozzárendelések és beállítások kezelése
  [Az Office 365 Security & Compliance Centerben](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági házirendek kezelése<br>Megtekintheti, vizsgálja meg, és a vállalatot érintő biztonsági fenyegetésekre<br>Jelentések megtekintése
  Azure Komplex veszélyforrások elleni védelem | Megfigyelése és válaszadás a biztonsági gyanús tevékenység
  A Windows Defender ATP-EDR | Szerepkörök hozzárendelése<br>Gép csoportok kezelése<br>Végpont fenyegetések észlelése és automatikus szervizelést konfigurálása<br>Megtekintheti, vizsgálja meg és válaszadás a riasztásokra
  [Intune-ban](https://docs.microsoft.com/intune/role-based-access-control) | Nézetek felhasználói, eszköz, regisztráció, konfigurációs és alkalmazással kapcsolatos adatok<br>Nem hajthat végre változtatásokat az Intune-hoz
  [A cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Adja hozzá a rendszergazdák, szabályzatokat és beállításokat, naplóadatokat tölthetnek fel és irányítási műveleteket hajthatnak végre hozzáadása
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Megtekintheti biztonsági házirendek, biztonsági állapotot, szerkessze a biztonsági szabályzatok, riasztások megtekintése és javaslatok, riasztások és javaslatok elvetése
  [Az Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365-szolgáltatások állapotának megtekintése

* **[Biztonsági operátor](#security-operator)** : Ezzel a szerepkörrel rendelkező felhasználók riasztások kezelése és biztonsági funkció, többek között a Microsoft 365 security Centerben az Azure Active Directory, az Identity Protection, a Privileged Identity Management és a Office 365 globális csak olvasási hozzáféréssel rendelkezik Biztonsági és megfelelőségi központban. Office 365-engedélyekkel kapcsolatos további információt [az engedélyek az Office 365 Security & Compliance Centerben](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

  A | Teheti meg
  --- | ---
  [A security center a Microsoft 365](https://protection.office.com) | A biztonsági olvasó szerepkör összes engedélyt<br>Megtekintheti, vizsgálja meg és válaszadás a biztonsági fenyegetések riasztásokra
  Identity Protection Center | A biztonsági olvasó szerepkör összes engedélyt<br>Ezenkívül lehetővé teszi új jelszavak kivételével az összes Identity Protection Centerben műveletek végrehajtásához
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | A biztonsági olvasó szerepkör összes engedélyt
  [Az Office 365 Security & Compliance Centerben](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | A biztonsági olvasó szerepkör összes engedélyt<br>Megtekintheti, vizsgálja meg és válaszadás a biztonsági riasztásokra
  A Windows Defender ATP-EDR | A biztonsági olvasó szerepkör összes engedélyt<br>Megtekintheti, vizsgálja meg és válaszadás a biztonsági riasztásokra
  [Intune-ban](https://docs.microsoft.com/intune/role-based-access-control) | A biztonsági olvasó szerepkör összes engedélyt
  [A cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | A biztonsági olvasó szerepkör összes engedélyt
  [Az Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365-szolgáltatások állapotának megtekintése
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[Biztonsági olvasó](#security-reader)** : Az ehhez a szerepkörhöz tartozó felhasználók globális csak olvasási hozzáféréssel rendelkezik a biztonsági funkció, többek között a Microsoft 365 a security center, Azure Active Directory, Identity Protection, Privileged Identity Management, valamint lehetővé teszi olvassa el az Azure Active Directory bejelentkezési jelentéseit és naplóit, és az Office 365 biztonsági és megfelelőségi központban. Office 365-engedélyekkel kapcsolatos további információt [az engedélyek az Office 365 Security & Compliance Centerben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  A | Teheti meg
  --- | ---
  [A security center a Microsoft 365](https://protection.office.com) | Biztonsági szabályzatok megtekintése a Microsoft 365-szolgáltatások között<br>Nézet biztonsági fenyegetések és riasztások<br>Jelentések megtekintése
  Identity Protection Center | Olvassa el a biztonsági jelentések és a beállítási információk biztonsági funkciók<br><ul><li>Levélszemét<li>Encryption<li>Adatveszteség-megelőzés<li>Kártevőirtó<li>Speciális fenyegetésvédelem<li>Adathalászat elleni<li>Mailflow szabályok
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Illesztett rendelkezik a csak olvasási hozzáférést minden információt az Azure AD PIM-ben: Szabályzatok és a jelentések az Azure AD szerepkör-hozzárendeléseket, biztonsági áttekinti, és a jövőben olvassa el a hozzáférési házirend adatok és jelentések forgatókönyvek mellett az Azure AD szerepkör-hozzárendelés.<br>**Nem lehet** iratkozzon fel az Azure AD PIM-ben, vagy ne módosítsa. A PIM-portálon vagy a Powershellen keresztül valaki ezt a szerepkört a további szerepkörök (például a globális rendszergazdai vagy a kiemelt szerepkörű rendszergazda), ha a felhasználó nem jogosult a számukra aktiválhatja.
  [Az Office 365 Security & Compliance Centerben](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági szabályzatok megtekintése<br>Megtekintheti, és a biztonsági fenyegetések kivizsgálása érdekében<br>Jelentések megtekintése
  A Windows Defender ATP-EDR | Riasztásainak megtekintése és vizsgálata
  [Intune-ban](https://docs.microsoft.com/intune/role-based-access-control) | Nézetek felhasználói, eszköz, regisztráció, konfigurációs és alkalmazással kapcsolatos adatok. Az Intune-hoz nem végezhet módosításokat.
  [A cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási engedélyekkel rendelkezik, és riasztásokat kezelhet
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Megtekintheti a javaslatok és riasztások, a biztonsági házirendek, a biztonsági állapotot, de nem végezhet módosításokat megtekintése
  [Az Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365-szolgáltatások állapotának megtekintése

* **[Szolgáltatástámogatási rendszergazda](#service-support-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók támogatási kérelmeket nyithatnak a Microsoft Azure és az Office 365-szolgáltatások és a szolgáltatás irányítópultját és üzenetközpontját center a nézetek a [az Azure portal](https://portal.azure.com) és [Microsoft 365 felügyeleti központban](https://admin.microsoft.com). További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "szolgáltatás-rendszergazda." Az "Szolgáltatás-rendszergazda" a [az Azure portal](https://portal.azure.com), a [Microsoft 365 felügyeleti központban](https://admin.microsoft.com), és az Intune-portálon.

* **[SharePoint-rendszergazda](#sharepoint-service-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft SharePoint online-ban, a szolgáltatás megléte esetén, ezenkívül létrehozása és kezelése az Office 365-csoportok, kezelhetik a támogatási jegyeket és szolgáltatások állapotának figyelése lehetővé teszi. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "a SharePoint szolgáltatás-rendszergazda." Az "A SharePoint-rendszergazda" a [az Azure portal](https://portal.azure.com).

* **[Skype vállalati verzió / Lync-rendszergazdája](#lync-service-administrator)** : Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Skype vállalati verzió, globális engedélyekkel rendelkeznek a szolgáltatás megléte esetén, valamint kezelheti a Skype-specifikus felhasználói attribútumok az Azure Active Directoryban. Ezenkívül a szerepkörök a kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése, valamint a Teams és a Skype for Business felügyeleti központ eléréséhez. A fiók is licenccel kell rendelkezniük a csapatok vagy csoportokkal PowerShell-parancsmagok nem lesz futtatható. További információ: [kapcsolatos a Skype for Business rendszergazdai szerepkör](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) és a licencelési adatokat csapat [Skype for Business és a Microsoft Teams kiegészítő licencelési](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja "Lync-Szolgáltatásadminisztrátor". Az "Skype for Business rendszergazda" a [az Azure portal](https://portal.azure.com/).

* **[Rendszergazda csapatok](#teams-service-administrator)** : Az ehhez a szerepkörhöz felhasználók kezelhetik a Microsoft Teams munkaterhelés, a Microsoft Teams és a Skype vállalati felügyeleti központot és a megfelelő PowerShell-modulok minden aspektusát. Ez magában foglalja, többek között a más területeken, telefonos, üzenetkezelés, értekezletek és a csapatok maguk kapcsolatos összes felügyeleti eszközök. Ez a szerepkör emellett listázását teszi létrehozása és kezelése az Office 365-csoportok, kezelhetik a támogatási jegyeket és szolgáltatások állapotának figyelése.
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "Teams szolgáltatás-rendszergazda". Az "Csapatok rendszergazda" a [az Azure portal](https://portal.azure.com).

* **[Rendszergazda kommunikációs csapatok](#teams-communications-administrator)** : Az ehhez a szerepkörhöz felhasználók kezelhetik a hang- és a telefonos kapcsolatos Microsoft Teams számítási aspektusait. Ez magában foglalja a telefon száma hozzárendelés, hang- és értekezlet házirendek és a hívás analytics eszközkészlet teljes hozzáférést a felügyeleti eszközöket.

* **[Támogatási szakértő kommunikációs csapatok](#teams-communications-support-engineer)** : A szerepkör felhasználói belül a Microsoft Teams és a Skype vállalati verzió az eszközök a Microsoft Teams és a Skype for Business felügyeleti központ hibaelhárítás felhasználói hívás használatával kommunikációs problémák elhárításához. A szerepkör felhasználói tekintheti meg az összes érintett résztvevők a teljes hívás rekord adatait. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[Támogatási szakértő kommunikációs csapatok](#teams-communications-support-specialist)** : A szerepkör felhasználói belül a Microsoft Teams és a Skype vállalati verzió az eszközök a Microsoft Teams és a Skype for Business felügyeleti központ hibaelhárítás felhasználói hívás használatával kommunikációs problémák elhárításához. A szerepkör felhasználói csak tekintheti meg a felhasználói adatok hívásában az adott felhasználó rendelkezik kulcskeresési. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[Felhasználói rendszergazda](#user-administrator)** : A szerepkörrel rendelkező felhasználók létrehozása, felhasználók, és bizonyos korlátozásokkal (lásd alább) a felhasználók minden aspektusa kezelhető, és frissítheti a jelszó-elévülési házirendnek. Továbbá ezzel a szerepkörrel rendelkező felhasználók is csoportok létrehozásához és kezeléséhez minden. Ez a szerepkör is létrehozhatók és kezelhetők a felhasználói nézetek, kezelhetik a támogatási jegyeket, és szolgáltatások állapotának figyelése lehetővé teszi.

  | | |
  | --- | --- |
  |Általános engedélyek|<p>Felhasználók és csoportok létrehozása</p><p>Létre és kezelhet felhasználói nézetek</p><p>Office-támogatási jegyek kezelése<p>Jelszó-elévülési házirendnek frissítése|
  |<p>Az összes felhasználó többek között az összes rendszergazda</p>|<p>Licencek kezelése</p><p>Egyszerű felhasználónév kivételével az összes felhasználó tulajdonságainak kezelése</p>
  |Csak a nem rendszergazda jogosultságú és a következők egyikét a korlátozott rendszergazdai szerepkörök felhasználóknak:<ul><li>Címtárolvasók<li>Vendég meghívója<li>Ügyfélszolgálati adminisztrátor<li>Üzenetközpont olvasó<li>Jelentésolvasó<li>Felhasználói adminisztrátor|<p>Törlés és helyreállítás</p><p>Letiltása és engedélyezése</p><p>Érvénytelenítéséhez frissítési jogkivonatok</p><p>Egyszerű felhasználónév többek között az összes felhasználói tulajdonságainak kezelése</p><p>Másik jelszó kérése</p><p>Frissítés (FIDO) eszközkulcsok</p>
  
  <b>Fontos</b>: Ezzel a szerepkörrel rendelkező felhasználók jelszavait változtathatják felhasználók férhetnek hozzá a bizalmas vagy titkos információk vagy kritikus konfigurációs belüli és kívüli Azure Active Directory számára. A felhasználó jelszavának módosítása jelentheti azt feltételezik, hogy felhasználói identitás- és engedélyek lehetővé teszi. Példa:
  * Alkalmazás regisztrálása és a vállalati alkalmazás tulajdonosok, aki kezelheti az alkalmazásokat a saját hitelesítő adatait. Ezeket az alkalmazásokat lehet, hogy emelt szintű engedélyek az Azure AD és felhasználói rendszergazdák máshol nem kapja. Ez lehet, hogy a felhasználó rendszergazda az alkalmazás tulajdonosa identitását, és ezután további elérési útján feltételezik egy emelt szintű alkalmazáshoz a hitelesítő adatokat, az alkalmazás frissítésével.
  * Azure-előfizetéssel rendelkező, előfordulhat, hogy hozzáféréssel rendelkező bizalmas vagy titkos információk vagy kritikus fontosságú konfigurációját az Azure-ban.
  * Biztonsági csoport és az Office 365-csoport tulajdonosainak, csoport tagságának kezelésére jogosult. Ezeket a csoportokat a bizalmas vagy titkos információk, vagy a kritikus konfigurálása az Azure ad-ben és a máshol hozzáférést biztosíthat.
  * A rendszergazdák az Azure AD-en kívül más szolgáltatásokkal, például Exchange online-hoz, Office biztonsági és megfelelőségi központban és emberi erőforrások rendszerek.
  * A nem rendszergazdák, például a vezetők, a védőt és a személyzeti osztályon dolgozóknak, akik esetleg bizalmas vagy személyes információkhoz való hozzáférés.

## <a name="role-permissions"></a>Szerepkör engedélyei
Az alábbi táblázatok ismertetik az Azure Active Directory minden egyes szerepkörhöz megadott engedélyeket. Egyes szerepkörök lehet további engedélyek a Microsoft services, Azure Active Directory-en kívül.

### <a name="application-administrator"></a>Alkalmazás-rendszergazda
Létrehozhat és alkalmazásregisztrációk és a vállalati alkalmazásokkal kapcsolatos összes felügyeleti rendelkezik.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Frissítés az Azure Active Directoryban applications.audience tulajdonság. |
| microsoft.aad.directory/applications/authentication/update | Frissítés az Azure Active Directoryban applications.authentication tulajdonság. |
| microsoft.aad.directory/applications/basic/update | Alkalmazások az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/applications/create | Alkalmazások létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/credentials/update | Frissítés az Azure Active Directoryban applications.credentials tulajdonság. |
| microsoft.aad.directory/applications/delete | Törölje az alkalmazást az Azure Active Directoryban. |
| microsoft.aad.directory/applications/owners/update | Az applications.owners tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/applications/permissions/update | Frissítés az Azure Active Directoryban applications.permissions tulajdonság. |
| microsoft.aad.directory/applications/policies/update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| microsoft.aad.directory/appRoleAssignments/create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/read | Olvassa el az alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az Azure Active Directoryban hozzárendeléseinek frissítése. |
| microsoft.aad.directory/appRoleAssignments/delete | Alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban törölje. |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directoryban Adatmodelltáblához (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/audience/update | Frissítés az Azure Active Directoryban servicePrincipals.audience tulajdonság. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Frissítés az Azure Active Directoryban servicePrincipals.authentication tulajdonság. |
| microsoft.aad.directory/servicePrincipals/basic/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Frissítés az Azure Active Directoryban servicePrincipals.credentials tulajdonság. |
| microsoft.aad.directory/servicePrincipals/delete | Szolgáltatásnevek törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Frissítés az Azure Active Directoryban servicePrincipals.permissions tulajdonság. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directoryban signInReports (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="application-developer"></a>Alkalmazásfejlesztő
Hozhat létre alkalmazást az alkalmazásregisztrációk független a "felhasználók regisztrálhatnak alkalmazásokat" beállítás.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Alkalmazások létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Az oauth2-engedélyezések létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Szolgáltatásnevek létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |

### <a name="authentication-administrator"></a>Hitelesítési rendszergazda
Megtekintheti, állítsa be, és alaphelyzetbe állítása hitelesítési módszerre vonatkozó adatok bármely nem rendszergazdai felhasználó számára engedélyezett.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az Azure Active Directoryban az összes felhasználói frissítési jogkivonatok érvénytelenítéséhez. |
| microsoft.aad.directory/users/strongAuthentication/update | Erős hitelesítés tulajdonságainak, például többtényezős hitelesítés hitelesítő adatokat. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="azure-information-protection-administrator"></a>Azure Information Protection Administrator
Az Azure Information Protection szolgáltatás minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="b2c-user-flow-administrator"></a>B2C felhasználói folyamat rendszergazda
Létrehozhat és kezelhet felhasználói folyamatok minden aspektusát.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Olvassa el, és a felhasználói folyamatok konfigurálása az Azure Active Directory B2C-t. |

### <a name="b2c-user-flow-attribute-administrator"></a>B2C felhasználói folyamat attribútum rendszergazda
Hozzon létre, és az összes felhasználói folyamatok számára elérhető attribútum séma kezelése.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Olvassa el, és a felhasználói attribútumok konfigurálása az Azure Active Directory B2C-t. |

### <a name="b2c-ief-keyset-administrator"></a>B2C IEF kulcskészlet rendszergazda
Összevonási és a titkosítás az identitás-kezelőfelületi keretrendszer a titkos kulcsok kezeléséhez.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Olvassa el, és az Azure Active Directory B2C kulcsfontosságú csoportjainak konfigurálása. |

### <a name="b2c-ief-policy-administrator"></a>B2C IEF szabályzat rendszergazda
Szabályzatok létrehozása és kezelése bizalmi keretrendszer az az identitás-kezelőfelületi keretrendszer.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Olvassa el, és az Azure Active Directory B2C-vel egyéni szabályzatok konfigurálása. |

### <a name="billing-administrator"></a>Számlázási adminisztrátor
Számlázással kapcsolatos általános feladatokat, például frissítheti a fizetési adatokat hajthat végre.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Az Azure Active Directory szervezeti alapvető tulajdonságainak frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.commerce.billing/allEntities/allTasks | Office 365 számlázási minden szempontjának kezeléséhez. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |


### <a name="cloud-application-administrator"></a>Felhőalkalmazás-rendszergazda
Létrehozhat és alkalmazásregisztrációkkal és a vállalati alkalmazásokhoz az Application Proxy kivételével minden szempontjának kezeléséhez.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Frissítés az Azure Active Directoryban applications.audience tulajdonság. |
| microsoft.aad.directory/applications/authentication/update | Frissítés az Azure Active Directoryban applications.authentication tulajdonság. |
| microsoft.aad.directory/applications/basic/update | Alkalmazások az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/applications/create | Alkalmazások létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/credentials/update | Frissítés az Azure Active Directoryban applications.credentials tulajdonság. |
| microsoft.aad.directory/applications/delete | Törölje az alkalmazást az Azure Active Directoryban. |
| microsoft.aad.directory/applications/owners/update | Az applications.owners tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/applications/permissions/update | Frissítés az Azure Active Directoryban applications.permissions tulajdonság. |
| microsoft.aad.directory/applications/policies/update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| microsoft.aad.directory/appRoleAssignments/create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az Azure Active Directoryban hozzárendeléseinek frissítése. |
| microsoft.aad.directory/appRoleAssignments/delete | Alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban törölje. |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directoryban Adatmodelltáblához (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/audience/update | Frissítés az Azure Active Directoryban servicePrincipals.audience tulajdonság. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Frissítés az Azure Active Directoryban servicePrincipals.authentication tulajdonság. |
| microsoft.aad.directory/servicePrincipals/basic/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Frissítés az Azure Active Directoryban servicePrincipals.credentials tulajdonság. |
| microsoft.aad.directory/servicePrincipals/delete | Szolgáltatásnevek törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Frissítés az Azure Active Directoryban servicePrincipals.permissions tulajdonság. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directoryban signInReports (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="cloud-device-administrator"></a>Felhőbeli Eszközrendszergazda
Teljes hozzáférés az Azure AD-eszközök felügyeletére.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directoryban Adatmodelltáblához (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/DELETE | Eszközök törlése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/disable | Letilthatja az eszközöket az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/enable | Engedélyezze az eszközök az Azure Active Directoryban. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directoryban signInReports (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |

### <a name="company-administrator"></a>Céges rendszergazda
Az Azure AD bármilyen elemét felügyelheti, és a Microsoft-szolgáltatásokkal, hogy az Azure AD-identitásokat.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.aad.cloudAppSecurity az alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Hozzon létre és administrativeUnits, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/applications/allProperties/allTasks | Hozzon létre, és törölje az alkalmazást, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Hozzon létre és hozzárendeléseinek, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directoryban Adatmodelltáblához (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Hozzon létre és törléséhez, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Hozzon létre és szerződések törlése és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/devices/allProperties/allTasks | Hozzon létre, és törölje az eszközt, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Hozzon létre és directoryRoles, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Hozzon létre és directoryRoleTemplates, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/domains/allProperties/allTasks | És törölje a tartományok, és olvassa el tulajdonságok létrehozása és frissítése az összes az Azure Active Directoryban. |
| microsoft.aad.directory/groups/allProperties/allTasks | És törölje a csoportokat, és olvassa el tulajdonságok létrehozása és frissítése az összes az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Hozzon létre és groupSettings, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Hozzon létre és groupSettingTemplates, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Hozzon létre és loginTenantBranding, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Hozzon létre és törölni az oauth2-engedélyezések, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/organization/allProperties/allTasks | Hozzon létre és törlése a szervezeten belül, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/policies/allProperties/allTasks | És törli a szabályzatokat, és olvassa el tulajdonságok létrehozása és frissítése az összes az Azure Active Directoryban. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | És törölje a visszaadandó szerepkörkiosztások száma, és olvassa el tulajdonságok létrehozása és frissítése az összes az Azure Active Directoryban. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Hozzon létre és roleDefinitions, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Hozzon létre és scopedRoleMemberships, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/serviceAction/activateService | A Activateservice szolgáltatási művelet hajtható végre, az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | A Disabledirectoryfeature szolgáltatási művelet hajtható végre, az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | A Enabledirectoryfeature szolgáltatási művelet hajtható végre, az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | A Getavailableextentionproperties szolgáltatási művelet hajtható végre, az Azure Active Directoryban |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Hozzon létre és szolgáltatásnevek, törlése és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directoryban signInReports (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Hozzon létre és subscribedSkus, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/users/allProperties/allTasks | Hozzon létre, és törli a felhasználókat, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directorySync/allEntities/allTasks | Az Azure AD Connectben minden művelet végrehajtására. |
| microsoft.aad.identityProtection/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.aad.identityProtection az alapvető tulajdonságainak frissítése. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Olvassa el a microsoft.azure.advancedThreatProtection található összes erőforrást. |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.commerce.billing/allEntities/allTasks | Office 365 számlázási minden szempontjának kezeléséhez. |
| microsoft.intune/allEntities/allTasks | Az Intune minden szempontjának kezeléséhez. |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365-megfelelőségi vezető minden szempontjának kezeléséhez |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Asztali Analytics minden szempontjának kezeléséhez. |
| microsoft.office365.exchange/allEntities/allTasks | Exchange online-ban minden szempontjának kezeléséhez. |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365-ügyfél kulcstároló minden szempontjának kezeléséhez |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Read securityMessages in microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Az Office 365 védelmi központ minden szempontjának kezeléséhez. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.securityComplianceCenter az alapvető tulajdonságainak frissítése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.sharepoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365-használati jelentéseket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365 minden szempontjának kezeléséhez. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI minden szempontjának kezeléséhez. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Olvassa el a microsoft.windows.defenderAdvancedThreatProtection található összes erőforrást. |

### <a name="compliance-administrator"></a>Szabályozási ügyintéző
Megtekintheti és kezelheti a megfelelőségi konfigurációt és jelentéseket az Azure ad-ben és az Office 365.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365-megfelelőségi vezető minden szempontjának kezeléséhez |
| microsoft.office365.exchange/allEntities/allTasks | Exchange online-ban minden szempontjának kezeléséhez. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.sharepoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="compliance-data-administrator"></a>Megfelelőségi adatok rendszergazda
Hoz létre, és megfelelőségi tartalmat kezel.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Olvassa el, és a Microsoft Cloud App Security konfigurálása. |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365-megfelelőségi vezető minden szempontjának kezeléséhez |
| microsoft.office365.exchange/allEntities/allTasks | Exchange online-ban minden szempontjának kezeléséhez. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.sharepoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="conditional-access-administrator"></a>Feltételes hozzáférésű rendszergazda
Feltételes hozzáférési képességeit is kezelheti.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Frissítés az Azure Active Directoryban policies.conditionalAccess tulajdonság. |
| microsoft.aad.directory/policies/conditionalAccess/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Frissítés az Azure Active Directoryban policies.conditionalAccess tulajdonság. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Frissítés az Azure Active Directoryban policies.conditionalAccess tulajdonság. |

### <a name="crm-service-administrator"></a>CRM-Szolgáltatásadminisztrátor
A Dynamics 365-termék minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365 minden szempontjának kezeléséhez. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="customer-lockbox-access-approver"></a>Ügyfélszéf hozzáférés-jóváhagyója
Jóváhagyhatja a Microsoft támogatási kérések vevő szervezeti adatok eléréséhez.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365-ügyfél kulcstároló minden szempontjának kezeléséhez |

### <a name="desktop-analytics-administrator"></a>Asztali Analytics rendszergazdája
A Desktop Analytics és az Office testreszabási & házirend szolgáltatások is kezelheti. Asztali Analytics például a eszközleltár megtekintése, központi telepítési csomagok létrehozása, üzembe helyezési és egészségügyi állapotának megtekintéséhez. Az Office testreszabási & szabályzat szolgáltatáshoz Ez a szerepkör lehetővé teszi a felhasználók Office házirendek kezelése.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Asztali Analytics minden szempontjának kezeléséhez. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="device-administrators"></a>Eszközadminisztrátorok
Ehhez a szerepkörhöz tartozó felhasználók hozzáadódnak a helyi Rendszergazdák csoport, az Azure AD-hez csatlakoztatott eszközökön.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Az Azure Active Directoryban groupSettings a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Az Azure Active Directoryban groupSettingTemplates a alapvető tulajdonságainak olvasása. |

### <a name="directory-readers"></a>Címtárolvasók
Alapszintű directory információkat olvashatja. Az alkalmazásokhoz való hozzáférés biztosítása, nem javasolt a felhasználók számára.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Az Azure Active Directoryban administrativeUnits a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/administrativeUnits/members/read | Az administrativeUnits.members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/basic/read | Az alkalmazások az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Applications/Owners/Read | Az applications.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/policies/read | Applications.policies tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contacts/Basic/Read | A partnerek az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/contacts/memberOf/read | Contacts.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contracts/Basic/Read | A szerződés az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Devices/Basic/Read | Az eszközökön az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/devices/memberOf/read | A devices.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/read | Devices.registeredOwners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredUsers/read | Devices.registeredUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/basic/read | Az Azure Active Directoryban directoryRoles a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | DirectoryRoles.eligibleMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/members/read | DirectoryRoles.members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/domains/basic/read | Az Azure Active Directory-tartományokban alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Groups.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Basic/Read | A csoportok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/memberOf/read | Groups.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Read | Groups.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Read | Olvassa el a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/read | Groups.settings tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/basic/read | Az Azure Active Directoryban groupSettings a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Az Azure Active Directoryban groupSettingTemplates a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Az Azure Active Directoryban az oauth2-engedélyezések alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Organization/Basic/Read | A szervezet az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Organization.trustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/roleAssignments/basic/read | A visszaadandó szerepkörkiosztások száma az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/roleDefinitions/basic/read | Az Azure Active Directoryban roleDefinitions a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/read | A szolgáltatásnevek Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | ServicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/read | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/read | ServicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/subscribedSkus/basic/read | Az Azure Active Directoryban subscribedSkus a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Read | A felhasználók az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/directReports/read | Users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="directory-synchronization-accounts"></a>Címtár-szinkronizálási fiókok
Csak Azure AD Connect szolgáltatást használják.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Frissítés az Azure Active Directoryban organization.dirSync tulajdonság. |
| microsoft.aad.directory/policies/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Basic/Read | A szabályzatok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Policies/Basic/Update | Házirendek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Policies/Owners/Read | Policies.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Owners/Update | Frissítés az Azure Active Directoryban policies.owners tulajdonság. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Policies.policiesAppliedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/tenantDefault/update | Frissítés az Azure Active Directoryban policies.tenantDefault tulajdonság. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/audience/update | Frissítés az Azure Active Directoryban servicePrincipals.audience tulajdonság. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Frissítés az Azure Active Directoryban servicePrincipals.authentication tulajdonság. |
| microsoft.aad.directory/servicePrincipals/basic/read | A szolgáltatásnevek Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/servicePrincipals/basic/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Frissítés az Azure Active Directoryban servicePrincipals.credentials tulajdonság. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/read | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | ServicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Frissítés az Azure Active Directoryban servicePrincipals.permissions tulajdonság. |
| microsoft.aad.directory/servicePrincipals/policies/read | ServicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directorySync/allEntities/allTasks | Az Azure AD Connectben minden művelet végrehajtására. |

### <a name="directory-writers"></a>Címtárírók
Olvasás, és alapszintű directory adatokat írnak a. Az alkalmazásokhoz való hozzáférés biztosítása, nem javasolt a felhasználók számára.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/Basic/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/members/update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| microsoft.aad.directory/groupSettings/basic/update | Az Azure Active Directoryban groupSettings alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groupSettings/create | GroupSettings létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/delete | Az Azure Active Directoryban groupSettings törlése. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az Azure Active Directoryban az összes felhasználói frissítési jogkivonatok érvénytelenítéséhez. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |

### <a name="exchange-service-administrator"></a>Exchange-szolgáltatások rendszergazdája
Az Exchange termékkel minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.unified tulajdonság. |
| microsoft.aad.directory/groups/unified/basic/update | Office 365-csoportok alapszintű tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/unified/create | Office 365-csoportok létrehozásához. |
| microsoft.aad.directory/groups/unified/delete | Office 365-csoportok törlése. |
| microsoft.aad.directory/groups/unified/members/update | Frissítse az Office 365-csoportok tagságát. |
| microsoft.aad.directory/groups/unified/owners/update | Frissítse az Office 365-csoportok tulajdonjogát. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.exchange/allEntities/allTasks | Exchange online-ban minden szempontjának kezeléséhez. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="external-identity-provider-administrator"></a>Külső azonosító nyújtó rendszergazdája
Közvetlen összevonási Identitásszolgáltatók használatra konfigurálja.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Olvassa el, és az Azure Active Directory B2C Identitásszolgáltatók konfigurálása. |

### <a name="guest-inviter"></a>Vendég meghívója
Meghívhat vendégfelhasználókat független a "tagok küldhetnek meghívót vendégek" beállítást.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Read | A felhasználók az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/directReports/read | Users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/inviteGuest | Vendégfelhasználók meghívása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="helpdesk-administrator"></a>Ügyfélszolgálati adminisztrátor
Alaphelyzetbe állíthatja a nem rendszergazdák és a segélyszolgálat rendszergazdák jelszavát.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az Azure Active Directoryban az összes felhasználói frissítési jogkivonatok érvénytelenítéséhez. |
| microsoft.aad.directory/users/password/update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="intune-service-administrator"></a>Intune szolgáltatás rendszergazdája
Az Intune termékben minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/contacts/create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| Microsoft.aad.Directory/Devices/Basic/Update | Az Azure Active Directoryban eszközök alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/Create | Eszközök létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/DELETE | Eszközök törlése az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/update | Frissítés az Azure Active Directoryban devices.registeredOwners tulajdonság. |
| microsoft.aad.directory/devices/registeredUsers/update | Frissítés az Azure Active Directoryban devices.registeredUsers tulajdonság. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/Basic/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/delete | Az Azure Active Directoryban csoportok törlése. |
| microsoft.aad.directory/groups/hiddenMembers/read | Groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/members/update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.intune/allEntities/allTasks | Az Intune minden szempontjának kezeléséhez. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |

### <a name="kaizala-administrator"></a>Kaizala-rendszergazda
A Microsoft Kaizala beállításait kezelheti.  

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >  
  
| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Olvassa el az Office 365 felügyeleti központban. |

### <a name="license-administrator"></a>Licencek adminisztrátora
A felhasználók és csoportok terméklicencek kezelheti.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| microsoft.aad.directory/users/usageLocation/update | Frissítse a users.usageLocation tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |

### <a name="lync-service-administrator"></a>Lync-szolgáltatások rendszergazdája
A Skype for Business termék minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="message-center-privacy-reader"></a>Üzenetközpont adatvédelmi olvasó
Olvashatja az üzenetközpont bejegyzések, adatvédelmi adatüzenetek, csoportok, tartományok és az előfizetések.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Read securityMessages in microsoft.office365.messageCenter. |

### <a name="message-center-reader"></a>Üzenetközpont olvasó
Olvashatja az üzeneteket és a cégük számára csak az Office 365 Üzenetközpontjában frissítéseket. 

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>1\. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/contacts/create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| microsoft.aad.directory/groups/create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/members/update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| microsoft.aad.directory/users/delete | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az Azure Active Directoryban az összes felhasználói frissítési jogkivonatok érvénytelenítéséhez. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/users/password/update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.directory/users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="partner-tier2-support"></a>2\. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/contacts/create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| microsoft.aad.directory/domains/allTasks | Hozzon létre és tartományok törlése, és olvassa el és az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/delete | Az Azure Active Directoryban csoportok törlése. |
| microsoft.aad.directory/groups/members/update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| microsoft.aad.directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/organization/basic/update | Az Azure Active Directory szervezeti alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| microsoft.aad.directory/users/delete | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az Azure Active Directoryban az összes felhasználói frissítési jogkivonatok érvénytelenítéséhez. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/users/password/update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.directory/users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="power-bi-service-administrator"></a>A Power BI-Szolgáltatásadminisztrátor
A Power BI terméket minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI minden szempontjának kezeléséhez. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="privileged-authentication-administrator"></a>A kiemelt hitelesítést rendszergazda
Megtekintheti, állítsa be, és alaphelyzetbe állítása hitelesítési módszerre vonatkozó adatok (a rendszergazda vagy a nem rendszergazda jogosultságú) bármely felhasználó számára engedélyezett.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az Azure Active Directoryban az összes felhasználói frissítési jogkivonatok érvénytelenítéséhez. |
| microsoft.aad.directory/users/strongAuthentication/update | Erős hitelesítés tulajdonságainak, például többtényezős hitelesítés hitelesítő adatokat. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="privileged-role-administrator"></a>A kiemelt szerepkörű rendszergazda
Az Azure AD szerepkör-hozzárendelések és a Privileged Identity Management minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.aad.privilegedIdentityManagement az alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Olvassa el, és konfigurálja a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Olvassa el, és konfigurálja a servicePrincipals.oAuth2PermissionGrants tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Létrehozása és kezelése a felügyeleti egységek (beleértve a tagokat) |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Hozzon létre, és kezelhetik a szerepkör-hozzárendeléseket. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Létrehozása és kezelése a szerepkör-definíciók. |

### <a name="reports-reader"></a>Jelentésolvasó
Jelentkezzen elolvashatja és naplózási jelentések.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directoryban Adatmodelltáblához (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directoryban signInReports (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365-használati jelentéseket. |

### <a name="search-administrator"></a>Keresés rendszergazda
Létrehozhat és a Microsoft Search beállítások minden szempontjának kezeléséhez.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Hozzon létre és törölje az összes erőforrást, és olvassa el, és microsoft.office365.search összes tulajdonságainak frissítéséhez. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365-használati jelentéseket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |

### <a name="search-editor"></a>Keresés szerkesztő
Hozhat létre, és a Szerkesztői olyan tartalmakat, például a könyvjelzők, Q és, helyek, floorplan kezelhet.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Hozzon létre és törölhet tartalmakat, és olvassa el és microsoft.office365.search összes tulajdonságainak frissítéséhez. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365-használati jelentéseket. |

### <a name="security-administrator"></a>Biztonsági rendszergazda
Olvassa el a biztonsági adatokat és jelentéseket, és beállíthatja az Azure AD-konfiguráció és az Office 365.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directoryban Adatmodelltáblához (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Basic/Update | Házirendek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/policies/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Owners/Update | Frissítés az Azure Active Directoryban policies.owners tulajdonság. |
| microsoft.aad.directory/policies/tenantDefault/update | Frissítés az Azure Active Directoryban policies.tenantDefault tulajdonság. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directoryban signInReports (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.identityProtection/allEntities/update | Frissítés microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.protectionCenter/allEntities/read | Az Office 365 védelmi központ minden aspektusának olvasása. |
| microsoft.office365.protectionCenter/allEntities/update | Frissítés microsoft.office365.protectionCenter található összes erőforrást. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |

### <a name="security-operator"></a>Biztonsági operátor
Létrehozza és kezeli a biztonsági események.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Olvassa el, és a Microsoft Cloud App Security konfigurálása. |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Olvassa el, és az Azure AD komplex veszélyforrások elleni védelem konfigurálása. |
| microsoft.intune/allEntities/allTasks | Az Intune minden szempontjának kezeléséhez. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Olvassa el, és konfigurálja a biztonsági és megfelelőségi központban. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365-használati jelentéseket. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Olvassa el, és a Windows Defender komplex veszélyforrások elleni védelem konfigurálása. |

### <a name="security-reader"></a>Biztonsági olvasó
Olvashatja az biztonsági információk és jelentések az Azure ad-ben és az Office 365.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directoryban Adatmodelltáblához (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directoryban signInReports (beleértve a rendszerjogosultságú tulajdonságai) az összes tulajdonság olvasása. |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.protectionCenter/allEntities/read | Az Office 365 védelmi központ minden aspektusának olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |

### <a name="service-support-administrator"></a>Szolgáltatástámogatási rendszergazda
Szolgáltatásállapot-adatokat, és kezelhetik a támogatási jegyeket.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="sharepoint-service-administrator"></a>A SharePoint szolgáltatás-rendszergazda
A SharePoint szolgáltatás minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.unified tulajdonság. |
| microsoft.aad.directory/groups/unified/basic/update | Office 365-csoportok alapszintű tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/unified/create | Office 365-csoportok létrehozásához. |
| microsoft.aad.directory/groups/unified/delete | Office 365-csoportok törlése. |
| microsoft.aad.directory/groups/unified/members/update | Frissítse az Office 365-csoportok tagságát. |
| microsoft.aad.directory/groups/unified/owners/update | Frissítse az Office 365-csoportok tulajdonjogát. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.sharepoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

### <a name="teams-communications-administrator"></a>Csapatok kommunikáció rendszergazda
Kezelheti a hívási és értekezletek funkciók a Microsoft Teams szolgáltatásban.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365-használati jelentéseket. |

### <a name="teams-communications-support-engineer"></a>Csapatok kommunikáció támogatási szakértő
Kommunikációs csapatok fejlett eszközökkel kapcsolatos problémák elhárításához.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |

### <a name="teams-communications-support-specialist"></a>Csapatok kommunikáció támogatási szakértő
Kommunikációs csapatok alapvető eszközökkel kapcsolatos problémák elhárításához.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |

### <a name="teams-service-administrator"></a>Csapatok szolgáltatás-rendszergazda
Kezelheti a Microsoft Teams szolgáltatás.

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.unified tulajdonság. |
| microsoft.aad.directory/groups/unified/basic/update | Office 365-csoportok alapszintű tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/unified/create | Office 365-csoportok létrehozásához. |
| microsoft.aad.directory/groups/unified/delete | Office 365-csoportok törlése. |
| microsoft.aad.directory/groups/unified/members/update | Frissítse az Office 365-csoportok tagságát. |
| microsoft.aad.directory/groups/unified/owners/update | Frissítse az Office 365-csoportok tulajdonjogát. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365-használati jelentéseket. |

### <a name="user-administrator"></a>Felhasználói adminisztrátor
A felhasználók és csoportok, köztük a korlátozott rendszergazdák jelszavának alaphelyzetbe állítását bármilyen elemét felügyelheti.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/delete | Alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban törölje. |
| microsoft.aad.directory/appRoleAssignments/update | Az Azure Active Directoryban hozzárendeléseinek frissítése. |
| microsoft.aad.directory/contacts/basic/update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/contacts/create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/Basic/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/delete | Az Azure Active Directoryban csoportok törlése. |
| microsoft.aad.directory/groups/hiddenMembers/read | Groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/members/update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Users/Create | Felhasználók létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/users/delete | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az Azure Active Directoryban az összes felhasználói frissítési jogkivonatok érvénytelenítéséhez. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/users/password/update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.directory/users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.office365.webPortal/allEntities/basic/read | Az összes erőforrás microsoft.office365.webPortal az alapvető tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Office 365 Service Healthben. |
| microsoft.office365.supportTickets/allEntities/allTasks | Hozzon létre és kezelheti az Office 365 támogatási jegyeket. |

## <a name="role-template-ids"></a>Szerepkör sablonok azonosítók

Szerepkörsablonok azonosítók főként felhasználók Graph API vagy a PowerShell segítségével.

Graph-displayName | Az Azure portal megjelenített neve | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Alkalmazás-rendszergazda | Alkalmazás-rendszergazda | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Alkalmazásfejlesztő | Alkalmazásfejlesztő | CF1C38E5-3621-4004-A7CB-879624DCED7C
Hitelesítési rendszergazda | Hitelesítési rendszergazda | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure Information Protection Administrator | Az Azure Information Protection-rendszergazda | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C felhasználói folyamat rendszergazda | B2C felhasználói folyamat rendszergazda | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C felhasználói folyamat attribútum rendszergazda | B2C felhasználói folyamat attribútum rendszergazda | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF kulcskészlet rendszergazda | B2C IEF kulcskészlet rendszergazda | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF szabályzat rendszergazda | B2C IEF szabályzat rendszergazda | 3edaf663-341e-4475-9f94-5c398ef6c070
Számlázási adminisztrátor | Számlázási rendszergazda | b0f54661-2d74-4c50-afa3-1ec803f12efe
Felhőalkalmazás-rendszergazda | Felhőalkalmazás-rendszergazda | 158c047a-c907-4556-b7ef-446551a6b5f7
Felhőbeli Eszközrendszergazda | Felhőbeli eszközrendszergazda | 7698a772-787b-4ac8-901f-60d6b08affd2
Céges rendszergazda | Globális rendszergazda | 62e90394-69f5-4237-9190-012177145e10
Szabályozási ügyintéző | Szabályozási ügyintéző | 17315797-102d-40b4-93e0-432062caca18
Megfelelőségi adatok rendszergazda | Megfelelőségi adatok rendszergazda | e6d1a23a-da11-4be4-9570-befc86d067a7
Feltételes hozzáférésű rendszergazda | Feltételes hozzáférésű rendszergazda | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM-Szolgáltatásadminisztrátor | Dynamics 365-rendszergazda | 44367163-eba1-44c3-98af-f5787879f96a
Ügyfélszéf hozzáférés-jóváhagyója | Ügyfél Ügyfélszéf hozzáférés-jóváhagyója | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Asztali Analytics rendszergazdája | Asztali Analytics rendszergazdája | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Eszközadminisztrátorok | Eszközadminisztrátorok | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Eszköz-csatlakoztatás | Eszköz-csatlakoztatás | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Eszközkezelők | Eszközkezelők | 2b499bcd-da44-4968-8aec-78e1674fa64d
Eszközök felhasználói számára | Eszközök felhasználói számára | d405c6df-0af8-4e3b-95e4-4d06e542189e
Címtárolvasók | Címtárolvasók | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Címtár-szinkronizálási fiókok | Címtár-szinkronizálási fiókok | d29b2b05-8046-44ba-8758-1e26182fcf32
Címtárírók | Címtárírók | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange-szolgáltatások rendszergazdája | Exchange-rendszergazda | 29232cdf-9323-42fd-ade2-1d097af3e4de
Külső azonosító nyújtó rendszergazdája | Külső azonosító nyújtó rendszergazdája | be2f45a1-457d-42af-a067-6ec1fa63bc45
Vendég meghívója | Vendég meghívója | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Ügyfélszolgálati adminisztrátor | Jelszókezelő | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Intune szolgáltatás rendszergazdája | Intune-rendszergazda | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala-rendszergazda | Kaizala-rendszergazda | 74ef975b-6605-40af-a5d2-b9539d836353
Licencek adminisztrátora | Licencek adminisztrátora | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync-szolgáltatások rendszergazdája | Skype vállalati rendszergazda | 75941009-915a-4869-abe7-691bff18279e
Üzenetközpont adatvédelmi olvasó | Üzenet center adatvédelmi olvasó | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Üzenetközpont olvasó | Üzenet center-Adatolvasó | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
1\. szintű partnertámogatás | Partnerek 1. rétegbeli támogatása | 4ba39ca4-527c-499a-b93d-d9b492c50246
2\. szintű partnertámogatás | Partnerek 2. rétegbeli támogatása | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
A Power BI-Szolgáltatásadminisztrátor | A Power BI rendszergazdája | a9ea8996-122f-4c74-9520-8edcd192826c
A kiemelt hitelesítést rendszergazda | Rendszergazdai jogosultsággal rendelkező hitelesítés | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
A kiemelt szerepkörű rendszergazda | Kiemelt szerepkörű rendszergazda | e8611ab8-c189-46e8-94e1-60213ab1f814
Jelentésolvasó | Jelentésolvasó | 4a5d8f65-41da-4de4-8968-e035b65339cf
Keresés rendszergazda | Keresés rendszergazda | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Keresés szerkesztő | Keresés szerkesztő | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Biztonsági rendszergazda | Biztonsági rendszergazda | 194ae4cb-b126-40b2-bd5b-6091b380977d
Biztonsági operátor | Biztonsági operátor | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Biztonsági olvasó | Biztonsági olvasó | 5d6b6bb7-de71-4623-b4af-96380a352509
Szolgáltatástámogatási rendszergazda | Szolgáltatás-rendszergazda | f023fd81-a637-4b56-95fd-791ac0226033
A SharePoint szolgáltatás-rendszergazda | SharePoint-rendszergazda | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Csapatok kommunikáció rendszergazda | Csapatok kommunikáció rendszergazda | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Csapatok kommunikáció támogatási szakértő | Csapatok kommunikáció támogatási szakértő | f70938a0-fc10-4177-9e90-2178f8765737
Csapatok kommunikáció támogatási szakértő | Csapatok kommunikáció támogatási szakértő | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Csapatok szolgáltatás-rendszergazda | Csapatok szolgáltatás-rendszergazda | 69091246-20e8-4a56-aa4d-066075b2a7a8
Felhasználó | Felhasználó | a0b1b346-4d3e-4e8b-98f8-753987be4970
Felhasználóifiók-adminisztrátor | Felhasználói adminisztrátor | fe930be7-5e62-47db-91af-98c3a49a38b1
Munkahelyi eszköz-csatlakoztatás | Munkahelyi eszköz-csatlakoztatás | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Elavult szerepkörök

A következő szerepkörök nem használható. Ezeket a már elavult, és eltávolítja a későbbiekben az Azure AD-ből.

* Speciális licencek adminisztrátora
* Eszköz-csatlakoztatás
* Eszközkezelők
* Eszközök felhasználói számára
* E-mailben ellenőrzött felhasználó-létrehozó
* Postaláda-adminisztrátor
* Munkahelyi eszköz-csatlakoztatás

## <a name="next-steps"></a>További lépések

* Felhasználók hozzárendelése egy Azure-előfizetés rendszergazdájaként kapcsolatos további információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
