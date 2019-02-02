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
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 64b8ca0412461ae1001eecce335f94d9f2f0825f
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659065"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Az Azure Active Directory rendszergazdája szerepkör engedélyei

Az Azure Active Directory (Azure AD), a különböző funkciók kiszolgálása érdekében külön rendszergazdák is kijelölhet. A rendszergazdák a feladatok végrehajtását, mint hozzáadása vagy módosítása a felhasználók, rendszergazdai szerepkörök hozzárendelése, felhasználói jelszó alaphelyzetbe állítása, a felhasználói licencek kezelése és tartománynevek kezelése az Azure AD portálon lehet kiválasztani.

A globális rendszergazda az összes rendszergazdai funkciójához hozzáfér. Alapértelmezés szerint az Azure-előfizetésre feliratkozó személy van rendelve a címtár globális rendszergazdai szerepkörrel. Csak a globális rendszergazdák és a kiemelt szerepkörű rendszergazdák delegálhatja a rendszergazdai szerepköröket.

## <a name="assign-or-remove-administrator-roles"></a>Vagy vonja meg a rendszergazdai szerepkörökről

Rendszergazdai szerepkörök hozzárendelése az Azure Active Directory felhasználó kapcsolatban lásd: [nézet és rendelhet hozzá rendszergazdai szerepköröket az Azure Active Directoryban](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Elérhető szerepkörök

A következő rendszergazdai szerepkörök érhetők el:

* **[Alkalmazás-rendszergazda](#application-administrator)**: Az ehhez a szerepkörhöz felhasználók létrehozhatják és vállalati alkalmazásokat, alkalmazásregisztrációkat és alkalmazásproxy-beállítások minden szempontjának kezeléséhez. Ez a szerepkör lehetővé teszi, hogy engedélyt adjanak az delegált engedélyeket, és az Alkalmazásengedélyek, kivéve a Microsoft Graph és az Azure AD Graph is nyújt. Ezen szerepkör tagjai nem adódnak tulajdonosként, amikor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozása.

  <b>Fontos</b>: A szerepkörök képes kezelni az alkalmazás hitelesítő adatait. Ehhez a szerepkörhöz tartozó felhasználók hitelesítő adatok hozzáadása egy alkalmazáshoz, és ezeket a hitelesítő adatokat használja a megszemélyesíteni az alkalmazás azonosítóját. Ha az alkalmazás azonosítóját hozzáférési engedélyt kapott az Azure Active Directoryhoz, például létrehozni vagy frissíteni a felhasználói és más objektumok, majd ehhez a szerepkörhöz hozzárendelt felhasználó sikerült végrehajtani az alkalmazás megszemélyesítése közben ezeket a műveleteket. Ezt a lehetőséget az alkalmazás identitás megszemélyesítése lehet egy megszerzését keresztül a felhasználó mit tehetnek a szerepkör-hozzárendeléseket keresztül az Azure ad-ben. Fontos megérteni, hogy a felhasználó az alkalmazás-rendszergazda szerepkör hozzárendelése biztosít az Alkalmazásidentitás megszemélyesítését.

* **[Alkalmazás fejlesztője](#application-developer)**: A szerepkör felhasználói alkalmazásregisztrációkat hozhat létre. Ha a "Felhasználók regisztrálhatnak alkalmazásokat" beállítás értéke nem. Ez a szerepkör lehetővé teszi a saját nevükben jóváhagyást tagok amikor a "Felhasználók engedélyezhetik, hogy az alkalmazások hozzáférjenek a céges adatok saját nevükben való" beállítás értéke nem. Ezen szerepkör tagjai tulajdonosként való hozzáadásakor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozásakor.

* **[Számlázási adminisztrátor](#billing-administrator)**: Vásárol, kezeli az előfizetéseket és a támogatási jegyeket, és figyeli a szolgáltatás állapotát.

* **[Felhőalkalmazás-rendszergazda](#cloud-application-administrator)**: A szerepkör felhasználói képes kezelni az application proxy kivételével az alkalmazás-rendszergazda szerepkörnek ugyanazokkal az engedélyekkel rendelkezik. A szerepkörök létrehozása és kezelése vállalati alkalmazásokkal és az alkalmazást az alkalmazásregisztrációk minden aspektusát. Ez a szerepkör lehetővé teszi, hogy engedélyt adjanak az delegált engedélyeket, és az Alkalmazásengedélyek, kivéve a Microsoft Graph és az Azure AD Graph is nyújt. Ezen szerepkör tagjai nem adódnak tulajdonosként, amikor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozása.

  <b>Fontos</b>: A szerepkörök képes kezelni az alkalmazás hitelesítő adatait. Ehhez a szerepkörhöz tartozó felhasználók hitelesítő adatok hozzáadása egy alkalmazáshoz, és ezeket a hitelesítő adatokat használja a megszemélyesíteni az alkalmazás azonosítóját. Ha az alkalmazás azonosítóját hozzáférési engedélyt kapott az Azure Active Directoryhoz, például létrehozni vagy frissíteni a felhasználói és más objektumok, majd ehhez a szerepkörhöz hozzárendelt felhasználó sikerült végrehajtani az alkalmazás megszemélyesítése közben ezeket a műveleteket. Ezt a lehetőséget az alkalmazás identitás megszemélyesítése lehet egy megszerzését keresztül a felhasználó mit tehetnek a szerepkör-hozzárendeléseket keresztül az Azure ad-ben. Fontos megérteni, hogy egy felhasználó hozzárendelése a Felhőalkalmazás-rendszergazda szerepkört biztosít az Alkalmazásidentitás megszemélyesítését.

* **[Felhőbeli Eszközrendszergazda](#cloud-device-administrator)**: A szerepkör felhasználói engedélyezheti, letilthatja, és eszközök törlése az Azure ad-ben és (ha van ilyen) olvassa el a Windows 10-es BitLocker-kulcsok az Azure Portalon. A szerepkör nem biztosítanak engedélyeket más tulajdonságokat, az eszköz kezelésére.

* **[Szabályozási ügyintéző](#compliance-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók a Microsoft 365-megfelelőségi központ, a Microsoft 365 felügyeleti központban, Azure-ban, és Office 365 biztonsági és megfelelőségi központban a megfelelőséggel kapcsolatos funkciók kezeléséhez engedélyekkel rendelkezik. A felhasználók is felügyeli az összes belül az Exchange felügyeleti központot és a csapatok & Skype for Business felügyeleti központban, és hozzon létre támogatási jegyeket Azure és a Microsoft 365. További információt [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  Eleme ennek | Teheti meg
  ----- | ----------
  [A Microsoft 365-megfelelőségi központ](https://protection.microsoft.com) | Védelme és kezelése a szervezet adatai között a Microsoft 365-szolgáltatásokhoz<br>Megfelelőségi riasztások kezelése
  [Megfelelőségi Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Nyomon követheti, hozzárendelése és ellenőrzése a szervezet a jogszabályoknak való megfelelőség tevékenységek
  [Az Office 365 Security & Compliance Centerben](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Adatirányítás kezelése<br>Jogi tudnivalók és adatok vizsgálat végrehajtása<br>Tulajdonos kérelem kezelése
  [Intune-ban](https://docs.microsoft.com/intune/role-based-access-control) | Az összes Intune-ban naplózási adatok megtekintése
  [A cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási engedélyekkel rendelkezik, és riasztásokat kezelhet<br>Hozzon létre, és módosíthatja a fájlhasználati szabályzatok és cégirányítási műveletek engedélyezése<br> Megtekintheti a beépített jelentések adatok felügyelete alatt

<!--* **[Compliance Data Administrator](#compliance-data-administrator)**: Users with this role have permissions to protect and track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Users can also manage all features within the Exchange admin center, Compliance Manager, and Teams & Skype for Business admin center and create support tickets for Azure and Microsoft 365.

  In | Can do
  ----- | ----------
  [Microsoft 365 compliance center](https://protection.microsoft.com) | Monitor compliance-related policies across Microsoft 365 services<br>Manage compliance alerts
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Track, assign, and verify your organization's regulatory compliance activities
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage data governance<br>Perform legal and data investigation<br>Manage Data Subject Request
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | View all Intune audit data
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Has read-only permissions and can manage alerts<br>Can create and modify file policies and allow file governance actions<br> Can view all the built-in reports under Data Management
-->
* **[Feltételes hozzáférésű rendszergazda](#conditional-access-administrator)**: Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik az Azure Active Directory feltételes hozzáférési beállításait.
  > [!NOTE]
  > Exchange ActiveSync feltételes hozzáférési szabályzatot az Azure-beli üzembe helyezéséhez a felhasználó is egy globális rendszergazdának kell lennie.
  
* **[Eszközadminisztrátorok](#device-administrators)**: Ez a szerepkör csak egy további helyi rendszergazdaként a hozzárendelés érhető el [eszközbeállítások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Az ehhez a szerepkörhöz tartozó felhasználók a helyi gépek rendszergazdái lesznek az Azure Active Directoryval összekapcsolt összes Windows 10-eszközön. Nem rendelkeznek képes kezelni az Azure Active Directoryban. 

* **[Címtárolvasók](#directory-readers)**: Ez egy örökölt szerepkör, amely hozzá kell rendelni az alkalmazásokat, amelyek nem támogatják a a [hozzájárulási keretrendszer](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Azt nem lehet hozzárendelni egy felhasználóhoz sem.

* **[Címtár-szinkronizálási fiókok](#directory-synchronization-accounts)**: Ne használja. Ez a szerepkör automatikusan hozzá lesz rendelve az Azure AD Connect szolgáltatást, és nem szándékos vagy bármely más használata támogatott.

* **[Címtárírók](#directory-writers)**: Ez egy örökölt szerepkör, amely hozzá kell rendelni az alkalmazásokat, amelyek nem támogatják a a [hozzájárulási keretrendszer](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Azt nem lehet hozzárendelni egy felhasználóhoz sem.

* **[Dynamics 365-rendszergazda vagy CRM-rendszergazda](#crm-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók globális engedélyekkel rendelkeznek a Microsoft Dynamics 365 online-ban, ha a szolgáltatás nem található, ezenkívül kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése. További információ: [a szolgáltatás-rendszergazdai szerepkör használata kezelheti a bérlő](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja "Dynamics 365-szolgáltatás-rendszergazda". Az "Dynamics 365-rendszergazda" a [az Azure portal](https://portal.azure.com).

* **[Exchange-rendszergazda](#exchange-service-administrator)**: Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Exchange Online-ban, ha van ilyen szolgáltatásuk. lehetővé teszi létrehozásához és kezeléséhez az összes Office 365-csoportokat, valamint kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "Exchange-szolgáltatások rendszergazdája". Az "Exchange-rendszergazda" a [az Azure portal](https://portal.azure.com).

* **[Globális / vállalati rendszergazda](#company-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók férhetnek hozzá az Azure Active Directoryval, valamint a szolgáltatások, például a security center a Microsoft 365, Azure Active Directory-identitásokat használó összes rendszergazdai funkciójához Microsoft 365 megfelelőségi központ, az Exchange Online, SharePoint online-hoz, és Skype vállalati Online verzióhoz. A személy, aki regisztrál az Azure Active Directory-bérlő globális rendszergazdája lesz. Csak a globális rendszergazdák más rendszergazdai szerepköröket rendelhet hozzá. A vállalat a egynél több globális rendszergazda is lehet. A globális rendszergazdák bármely felhasználó és minden más rendszergazdák jelszavát is alaphelyzetbe.

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja a folyamat "Vállalati rendszergazdaként". Az "Globális rendszergazda" a [az Azure portal](https://portal.azure.com).
  >
  >

* **[Vendég meghívója](#guest-inviter)**: Az ehhez a szerepkörhöz felhasználók kezelhetik az Azure Active Directory B2B vendégfelhasználói meghívókat során a **tagok küldhetnek meghívót** felhasználói beállítás értéke nem. További információ a B2B-együttműködés: [kapcsolatos Azure AD B2B együttműködés](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nem tartalmaz semmilyen más engedélyt.

* **[Information Protection-rendszergazda](#information-protection-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók minden engedéllyel rendelkezni az Azure Information Protection szolgáltatásban. Ez a szerepkör lehetővé teszi, hogy címkéket az Azure Information Protection-házirend konfigurálása, a védelmi sablonokat kezeléséhez és a védelem aktiválása. Ez a szerepkör nem biztosít az Identity Protection Centerben, Privileged Identity Management, Monitor Office 365 Service Healthben vagy Office 365 Security & Compliance Centerben engedélyeket.

* **[Intune-rendszergazda](#intune-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Intune Online globális engedélyekkel rendelkeznek, ha a szolgáltatás nem található. Ez a szerepkör ezenkívül tartalmazza a képes kezelni a felhasználók és eszközök számára annak érdekében, hogy társítja a házirendet, valamint csoportok létrehozásához és kezeléséhez. További információ: [szerepköralapú vezérlés (RBAC) a Microsoft Intune-nal](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "Intune-Szolgáltatásadminisztrátor". Az "Intune-rendszergazda" a [az Azure portal](https://portal.azure.com).

* **[Licencek adminisztrátora](#license-administrator)**: Az ehhez a szerepkörhöz felhasználók hozzáadhatnak, távolítsa el, és a frissítés licenc-hozzárendeléseket felhasználók, csoportok (Csoportalapú licencelést használ), és a felhasználási hely, a felhasználók kezeléséhez. A szerepkör nem teszi lehetővé a beszerzési vagy -előfizetések kezelése, hozzon létre vagy kezelheti a csoportokat, vagy hozzon létre vagy kezelni a felhasználókat a felhasználási hely túli biztosít. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[Üzenetközpont olvasó](#message-center-reader)**: Figyelheti, hogy a szerepkör felhasználói értesítések és a tanácsadás egészségügyi módosításokat [Office 365 üzenetközpontjában](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) konfigurált szolgáltatások, például az Exchange, az Intune-ban és a Microsoft Teams a szervezeten belül. Üzenetközpont heti e-mail emésztett hozzászólás, frissítések fogadása és oszthatnak meg üzenet center bejegyzések az Office 365-ben. Az Azure AD-ben ehhez a szerepkörhöz tartozó felhasználók csak van csak olvasási hozzáféréssel az Azure AD szolgáltatások, például a felhasználók és csoportok. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[1. rétegbeli támogatása partneri](#partner-tier1-support)**: Ne használja. Ez a szerepkör elavult és törlődni fog a későbbiekben az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partnerek általi használatra van, és nem célja általános használatra.

* **[2. rétegbeli támogatása partneri](#partner-tier2-support)**: Ne használja. Ez a szerepkör elavult és törlődni fog a későbbiekben az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partnerek általi használatra van, és nem célja általános használatra.

* **[Jelszókezelő / ügyfélszolgálati adminisztrátor](#helpdesk-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók is módosíthatja a jelszavát, érvényteleníti frissítési biztonsági jogkivonat, szolgáltatáskérések kezelése és szolgáltatások állapotának figyelése. A frissítési jogkivonatok érvénytelenítése kényszeríti a felhasználót, hogy jelentkezzen be újra. Segélyszolgálat rendszergazdák új jelszót és érvényteleníti a frissítési biztonsági jogkivonat, más felhasználók, akik nem rendszergazdák, vagy csak a következő szerepkörök tagjai:
  * Directory-olvasók
  * Vendég meghívója
  * Ügyfélszolgálati adminisztrátor
  * Üzenetközpont-olvasó
  * Jelentésolvasó
  
  <b>Fontos</b>: Ezzel a szerepkörrel rendelkező felhasználók jelszavait változtathatják felhasználók férhetnek hozzá a bizalmas vagy titkos információk vagy kritikus konfigurációs belüli és kívüli Azure Active Directory számára. A felhasználó jelszavának módosítása jelentheti azt feltételezik, hogy felhasználói identitás- és engedélyek lehetővé teszi. Példa:
  * Alkalmazás regisztrálása és a vállalati alkalmazás tulajdonosok, aki kezelheti az alkalmazásokat a saját hitelesítő adatait. Ezeket az alkalmazásokat lehet, hogy emelt szintű engedélyek az Azure ad-ben és az ügyfélszolgálat adminisztrátorai máshol nem kapja. Ez lehet, hogy egy ügyfélszolgálati adminisztrátor alkalmazás tulajdonosa identitását, és ezután további elérési útján feltételezik egy emelt szintű alkalmazáshoz a hitelesítő adatokat, az alkalmazás frissítésével.
  * Azure-előfizetéssel rendelkező, előfordulhat, hogy hozzáféréssel rendelkező bizalmas vagy titkos információk vagy kritikus fontosságú konfigurációját az Azure-ban.
  * Biztonsági csoport és az Office 365-csoport tulajdonosainak, csoport tagságának kezelésére jogosult. Ezeket a csoportokat a bizalmas vagy titkos információk, vagy a kritikus konfigurálása az Azure ad-ben és a máshol hozzáférést biztosíthat.
  * A rendszergazdák az Azure AD-en kívül más szolgáltatásokkal, például Exchange online-hoz, Office biztonsági és megfelelőségi központban és emberi erőforrások rendszerek.
  * A nem rendszergazdák, például a vezetők, a védőt és a személyzeti osztályon dolgozóknak, akik esetleg bizalmas vagy személyes információkhoz való hozzáférés.

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör "Ügyfélszolgálati adminisztrátor" azonosítja. Ezt "Jelszókezelő" szerepel a [az Azure portal](https://portal.azure.com/).
  >
  
* **[A Power BI rendszergazdai](#power-bi-service-administrator)**: Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Power BI-ban, ha van ilyen szolgáltatásuk, ezenkívül kezelhetik a támogatási jegyeket, és figyelhetik a szolgáltatás állapotát. További információ: [a Power BI rendszergazdai szerepkörét ismertető](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "a Power BI-Szolgáltatásadminisztrátor". Az "a Power BI rendszergazdája" a [az Azure portal](https://portal.azure.com).

* **[Kiemelt szerepkörű rendszergazda](#privileged-role-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendelések az Azure Active Directoryban, valamint az Azure AD Privileged Identity Management belül. Emellett ez a szerepkör lehetővé teszi minden aspektusát Privileged Identity Management kezelését.

  <b>Fontos</b>: A szerepkörök minden többek között a globális rendszergazdai szerepkörű Azure AD-szerepkörök csoporttagság kezelését. Ez a szerepkör nem tartalmaz semmilyen más emelt szintű képességek például létrehozása vagy frissítése a felhasználók Azure AD-ben. Azonban ebbe a szerepkörbe tartozó felhasználók adhat magukat vagy más további jogosultságok kiegészítő szerepkörök hozzárendelése.

* **[Jelentések olvasó](#reports-reader)**: Ezzel a szerepkörrel rendelkező felhasználók megtekinthetik a jelentéskészítési használati adatokat, és az Office 365 felügyeleti központban jelentések irányítópult és a bevezetési környezet csomag a Power bi-ban. Ezenkívül a szerepkör-bejelentkezés hozzáférést biztosít jelentéseket és a tevékenység az Azure ad-ben és a Microsoft Graph által visszaadott adatok jelentéskészítési API-t. A jelentések Olvasó szerepkörhöz rendelt felhasználó hozzáférhet, csak a releváns használati és mérőszámairól. Nincs szükség semmilyen konfigurálni vagy a hozzáférés a termékspecifikus felügyeleti központok, mint az Exchange-rendszergazdai jogosultságokkal. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[Biztonsági rendszergazda](#security-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók biztonsági vonatkozású szolgáltatásairól a security center a Microsoft 365, Azure Active Directory Identity Protection, Azure Information Protection, és Office 365 Security & Compliance Centerben kezeléséhez engedélyekkel rendelkezik. Office 365-engedélyekkel kapcsolatos további információt [az engedélyek az Office 365 Security & Compliance Centerben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  Eleme ennek | Teheti meg
  --- | ---
  [A security center a Microsoft 365](https://protection.microsoft.com) | Biztonsági szabályzatok figyelése a Microsoft 365-szolgáltatásokhoz<br>Biztonsági fenyegetések és a riasztások kezelése<br>Jelentések megtekintése
  Identity Protection Center | A biztonsági olvasó szerepkör összes engedélyt<br>Ezenkívül lehetővé teszi új jelszavak kivételével az összes Identity Protection Centerben műveletek végrehajtásához
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | A biztonsági olvasó szerepkör összes engedélyt<br>**Nem lehet** Azure ad-ben szerepkörtagságai és beállítások kezelése
  [Az Office 365 Security & Compliance Centerben](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági házirendek kezelése<br>Megtekintheti, vizsgálja meg, és a vállalatot érintő biztonsági fenyegetésekre<br>Jelentések megtekintése
  Azure Komplex veszélyforrások elleni védelem | Megfigyelése és válaszadás a biztonsági gyanús tevékenység
  A Windows Defender ATP-EDR | Szerepkörök hozzárendelése<br>Gép csoportok kezelése<br>Végpont fenyegetések észlelése és automatikus szervizelést konfigurálása<br>Megtekintheti, vizsgálja meg és válaszadás a riasztásokra
  [Intune-ban](https://docs.microsoft.com/intune/role-based-access-control) | Nézetek felhasználói, eszköz, regisztráció, konfigurációs és alkalmazással kapcsolatos adatok<br>Nem hajthat végre változtatásokat az Intune-hoz
  [A cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Adja hozzá a rendszergazdák, szabályzatokat és beállításokat, naplóadatokat tölthetnek fel és irányítási műveleteket hajthatnak végre hozzáadása
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Megtekintheti biztonsági házirendek, biztonsági állapotot, szerkessze a biztonsági szabályzatok, riasztások megtekintése és javaslatok, riasztások és javaslatok elvetése
  [Az Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365-szolgáltatások állapotának megtekintése

<!--* **[Security operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, as well as the ability to read Azure Active Directory sign-in reports and audit logs, and in Office 365 Security & Compliance Center.

  In | Can do
  --- | ---
  [Microsoft 365 security center](https://protection.microsoft.com) | All permissions of the Security Reader role<br>View, investigate, and respond to security threats alerts
  Identity Protection Center | All permissions of the Security Reader role<br>Additionally, the ability to perform all Identity Protection Center operations except for resetting passwords
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | All permissions of the Security Reader role
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  Windows Defender ATP and EDR | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | All permissions of the Security Reader role
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | All permissions of the Security Reader role
  [Office 365 service health](https://docs.microsoft.com/office365/enterprise/view-service-health) | View the health of Office 365 services
-->
* **[Biztonsági olvasó](#security-reader)**: Az ehhez a szerepkörhöz tartozó felhasználók globális csak olvasási hozzáféréssel rendelkezik a biztonsági funkció, többek között a Microsoft 365 a security center, Azure Active Directory, Identity Protection, Privileged Identity Management, valamint lehetővé teszi olvassa el az Azure Active Directory bejelentkezési jelentéseit és naplóit, és az Office 365 biztonsági és megfelelőségi központban. Office 365-engedélyekkel kapcsolatos további információt [az engedélyek az Office 365 Security & Compliance Centerben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  Eleme ennek | Teheti meg
  --- | ---
  [A security center a Microsoft 365](https://protection.microsoft.com) | Biztonsági szabályzatok megtekintése a Microsoft 365-szolgáltatások között<br>Nézet biztonsági fenyegetések és riasztások<br>Jelentések megtekintése
  Identity Protection Center | Olvassa el a biztonsági jelentések és a beállítási információk biztonsági funkciók<br><ul><li>Levélszemét<li>Titkosítás<li>Adatveszteség-megelőzés<li>Kártevőirtó<li>Komplex veszélyforrások elleni védelem<li>Adathalászat elleni<li>Mailflow szabályok
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Illesztett rendelkezik a csak olvasási hozzáférést minden információt az Azure AD PIM-ben: Szabályzatok és a jelentések az Azure AD szerepkör-hozzárendeléseket, biztonsági áttekinti, és a jövőben olvassa el a hozzáférési házirend adatok és jelentések forgatókönyvek mellett az Azure AD szerepkör-hozzárendelés.<br>**Nem lehet** iratkozzon fel az Azure AD PIM-ben, vagy ne módosítsa. A PIM-portálon vagy a Powershellen keresztül valaki ezt a szerepkört a további szerepkörök (például a globális rendszergazdai vagy a kiemelt szerepkörű rendszergazda), számukra egy jogosult felhasználó esetén is aktiválhatja.
  [Az Office 365 Security & Compliance Centerben](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági szabályzatok megtekintése<br>Megtekintheti, és a biztonsági fenyegetések kivizsgálása érdekében<br>Jelentések megtekintése
  A Windows Defender ATP-EDR | Riasztásainak megtekintése és vizsgálata
  [Intune-ban](https://docs.microsoft.com/intune/role-based-access-control) | Nézetek felhasználói, eszköz, regisztráció, konfigurációs és alkalmazással kapcsolatos adatok. Az Intune-hoz nem végezhet módosításokat.
  [A cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási engedélyekkel rendelkezik, és riasztásokat kezelhet
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Megtekintheti a javaslatok és riasztások, a biztonsági házirendek, a biztonsági állapotot, de nem végezhet módosításokat megtekintése
  [Az Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365-szolgáltatások állapotának megtekintése

* **[Szolgáltatástámogatási rendszergazda](#service-support-administrator)**: Az ehhez a szerepkörhöz tartozó felhasználók támogatási kérelmeket nyithatnak a Microsoft for Azure és az Office 365 szolgáltatásokban, és megtekinthetik a szolgáltatási irányítópultot és az üzenetközpontot az Azure Portalon és az Office 365 felügyeleti portálján. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "szolgáltatás-rendszergazda." Az "Szolgáltatás-rendszergazda" a [az Azure portal](https://portal.azure.com), az Office 365 felügyeleti portálon, és az Intune-portálon.

* **[SharePoint-rendszergazda](#sharepoint-service-administrator)**: Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft SharePoint online-ban, a szolgáltatás megléte esetén, ezenkívül létrehozása és kezelése az Office 365-csoportok, kezelhetik a támogatási jegyeket és szolgáltatások állapotának figyelése lehetővé teszi. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "a SharePoint szolgáltatás-rendszergazda." Az "A SharePoint-rendszergazda" a [az Azure portal](https://portal.azure.com).

* **[Skype vállalati verzió / Lync-rendszergazdája](#lync-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Skype vállalati verzió, globális engedélyekkel rendelkeznek a szolgáltatás megléte esetén, valamint kezelheti a Skype-specifikus felhasználói attribútumok az Azure Active Directoryban. Ezenkívül a szerepkörök a kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése, valamint a Teams és a Skype for Business felügyeleti központ eléréséhez. A fiók is licenccel kell rendelkezniük a csapatok vagy csoportokkal PowerShell-parancsmagok nem lesz futtatható. További információ: [kapcsolatos a Skype for Business rendszergazdai szerepkör](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) és a licencelési adatokat csapat [Skype for Business és a Microsoft Teams kiegészítő licencelési](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja "Lync-Szolgáltatásadminisztrátor". Az "Skype for Business rendszergazda" a [az Azure portal](https://portal.azure.com/).

* **[Rendszergazda csapatok](#teams-service-administrator)**: Az ehhez a szerepkörhöz felhasználók kezelhetik a Microsoft Teams munkaterhelés, a Microsoft Teams és a Skype vállalati felügyeleti központot és a megfelelő PowerShell-modulok minden aspektusát. Ez magában foglalja, többek között a más területeken, telefonos, üzenetkezelés, értekezletek és a csapatok maguk kapcsolatos összes felügyeleti eszközök. Ez a szerepkör emellett listázását teszi létrehozása és kezelése az Office 365-csoportok, kezelhetik a támogatási jegyeket és szolgáltatások állapotának figyelése.
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja, "Teams szolgáltatás-rendszergazda". Az "Csapatok rendszergazda" a [az Azure portal](https://portal.azure.com).

* **[Rendszergazda kommunikációs csapatok](#teams-communications-administrator)**: Az ehhez a szerepkörhöz felhasználók kezelhetik a hang- és a telefonos kapcsolatos Microsoft Teams számítási aspektusait. Ez magában foglalja a telefon száma hozzárendelés, hang- és értekezlet házirendek és a hívás analytics eszközkészlet teljes hozzáférést a felügyeleti eszközöket.

* **[Támogatási szakértő kommunikációs csapatok](#teams-communications-support-engineer)**: A szerepkör felhasználói belül a Microsoft Teams és a Skype vállalati verzió az eszközök a Microsoft Teams és a Skype for Business felügyeleti központ hibaelhárítás felhasználói hívás használatával kommunikációs problémák elhárításához. A szerepkör felhasználói tekintheti meg az összes érintett résztvevők a teljes hívás rekord adatait. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[Támogatási szakértő kommunikációs csapatok](#teams-communications-support-specialist)**: A szerepkör felhasználói belül a Microsoft Teams és a Skype vállalati verzió az eszközök a Microsoft Teams és a Skype for Business felügyeleti központ hibaelhárítás felhasználói hívás használatával kommunikációs problémák elhárításához. A szerepkör felhasználói csak tekintheti meg a felhasználói adatok hívásában az adott felhasználó rendelkezik kulcskeresési. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

* **[Felhasználóifiók-adminisztrátor](#user-account-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók felhasználók létrehozása, és bizonyos korlátozások (lásd alább) a felhasználók minden szempontjának kezeléséhez. Továbbá ezzel a szerepkörrel rendelkező felhasználók is csoportok létrehozásához és kezeléséhez minden. Ez a szerepkör is létrehozhatók és kezelhetők a felhasználói nézetek, kezelhetik a támogatási jegyeket, és szolgáltatások állapotának figyelése lehetővé teszi.

  | | |
  | --- | --- |
  |Általános engedélyek|<p>Felhasználók és csoportok létrehozása</p><p>Létre és kezelhet felhasználói nézetek</p><p>Office-támogatási jegyek kezelése|
  |<p>Az összes felhasználó többek között az összes rendszergazda</p>|<p>Licencek kezelése</p><p>Egyszerű felhasználónév kivételével az összes felhasználó tulajdonságainak kezelése</p>
  |Csak a nem rendszergazda jogosultságú és a következők egyikét a korlátozott rendszergazdai szerepkörök felhasználóknak:<ul><li>Directory-olvasók<li>Vendég meghívója<li>Ügyfélszolgálati adminisztrátor<li>Üzenetközpont-olvasó<li>Jelentésolvasó<li>Felhasználóifiók-adminisztrátor|<p>Törlés és helyreállítás</p><p>Letiltása és engedélyezése</p><p>Érvénytelenítéséhez frissítési jogkivonatok</p><p>Egyszerű felhasználónév többek között az összes felhasználói tulajdonságainak kezelése</p><p>Új jelszó létrehozása</p><p>Frissítés (FIDO) eszközkulcsok</p>
  
  <b>Fontos</b>: Ezzel a szerepkörrel rendelkező felhasználók jelszavait változtathatják felhasználók férhetnek hozzá a bizalmas vagy titkos információk vagy kritikus konfigurációs belüli és kívüli Azure Active Directory számára. A felhasználó jelszavának módosítása jelentheti azt feltételezik, hogy felhasználói identitás- és engedélyek lehetővé teszi. Példa:
  * Alkalmazás regisztrálása és a vállalati alkalmazás tulajdonosok, aki kezelheti az alkalmazásokat a saját hitelesítő adatait. Ezeket az alkalmazásokat lehet, hogy emelt szintű engedélyek az Azure AD és felhasználói rendszergazdák máshol nem kapja. Ez lehet, hogy a felhasználó rendszergazda az alkalmazás tulajdonosa identitását, és ezután további elérési útján feltételezik egy emelt szintű alkalmazáshoz a hitelesítő adatokat, az alkalmazás frissítésével.
  * Azure-előfizetéssel rendelkező, előfordulhat, hogy hozzáféréssel rendelkező bizalmas vagy titkos információk vagy kritikus fontosságú konfigurációját az Azure-ban.
  * Biztonsági csoport és az Office 365-csoport tulajdonosainak, csoport tagságának kezelésére jogosult. Ezeket a csoportokat a bizalmas vagy titkos információk, vagy a kritikus konfigurálása az Azure ad-ben és a máshol hozzáférést biztosíthat.
  * A rendszergazdák az Azure AD-en kívül más szolgáltatásokkal, például Exchange online-hoz, Office biztonsági és megfelelőségi központban és emberi erőforrások rendszerek.
  * A nem rendszergazdák, például a vezetők, a védőt és a személyzeti osztályon dolgozóknak, akik esetleg bizalmas vagy személyes információkhoz való hozzáférés.

## <a name="role-permissions"></a>Szerepkör engedélyei
Az alábbi táblázatok ismertetik az Azure Active Directory minden egyes szerepkörhöz megadott engedélyeket. Egyes szerepkörök lehet további engedélyek a Microsoft services, Azure Active Directory-en kívül.

### <a name="application-administrator"></a>Alkalmazás-rendszergazda
Az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Az applications.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/authentication/update | Az applications.authentication tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/basic/update | Az Application objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/create | Application objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/credentials/update | Az applications.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/delete | Az Application objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/owners/update | Az applications.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/permissions/update | Az applications.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/policies/update | A applications.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/create | AppRoleAssignment objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/read | Az AppRoleAssignment objektumok olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az AppRoleAssignment objektumok frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/delete | Az AppRoleAssignment objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Szabályzatok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | A servicePrincipals.appRoleAssignedTo tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | A servicePrincipals.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/audience/update | A servicePrincipals.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/authentication/update | A servicePrincipals.authentication tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/update | A ServicePrincipal objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/create | A servicePrincipals létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/credentials/update | A servicePrincipals.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/delete | A servicePrincipals törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | A servicePrincipals.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/permissions/update | A servicePrincipals.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | A servicePrincipals.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="application-developer"></a>Alkalmazásfejlesztő
Hozhat létre alkalmazást az alkalmazásregisztrációk független a "felhasználók regisztrálhatnak alkalmazásokat" beállítás.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Application objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | AppRoleAssignment objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Az oAuth2PermissionGrants létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | A servicePrincipals létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |

### <a name="billing-administrator"></a>Számlázási adminisztrátor
Számlázással kapcsolatos általános feladatokat hajthat végre, például frissítheti a fizetési adatokat.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Az Organization objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Az organization.trustedCAsForPasswordlessAuth tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.commerce.billing/allEntities/allTasks | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="desktop-analytics-administrator"></a>Asztali elemzési rendszergazda
Elérheti és kezelheti az asztali felügyeleti eszközöket és szolgáltatásokat, beleértve az Intune-ban.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Az Asztali elemzés teljes körű felügyelete. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-application-administrator"></a>Felhőalkalmazás-rendszergazda
Az Application Proxy kivételével az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Az applications.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/authentication/update | Az applications.authentication tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/basic/update | Az Application objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/create | Application objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/credentials/update | Az applications.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/delete | Az Application objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/owners/update | Az applications.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/permissions/update | Az applications.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/policies/update | A applications.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/create | AppRoleAssignment objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az AppRoleAssignment objektumok frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/delete | Az AppRoleAssignment objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Szabályzatok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | A servicePrincipals.appRoleAssignedTo tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | A servicePrincipals.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/audience/update | A servicePrincipals.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/authentication/update | A servicePrincipals.authentication tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/update | A ServicePrincipal objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/create | A servicePrincipals létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/credentials/update | A servicePrincipals.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/delete | A servicePrincipals törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | A servicePrincipals.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/permissions/update | A servicePrincipals.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | A servicePrincipals.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-device-administrator"></a>Felhőbeli eszközrendszergazda
Teljes körű jogosultság az eszközök Azure AD-beli kezelésére.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/DELETE | A Device objektumok törlése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/disable | Device objektumok letiltása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/enable | Eszközök engedélyezése az Azure Active Directoryban. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="company-administrator"></a>Céges rendszergazda
Az Azure AD-identitásokat használó Azure AD- és Microsoft-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | AdministrativeUnit objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/allProperties/allTasks | Application objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignment objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/contacts/allProperties/allTasks | Contact objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Contract objektumok létrehozása és törlése, illetve az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/devices/allProperties/allTasks | Device objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | DirectoryRole objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | DirectoryRoleTemplate objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/domains/allProperties/allTasks | Domain objektumok létrehozása és törlése, illetve az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/allProperties/allTasks | Group objektumok létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | GroupSetting objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | GroupSettingTemplate objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | LoginTenantBranding objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth2PermissionGrant objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/organization/allProperties/allTasks | Organization objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/allProperties/allTasks | Policy objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | RoleAssignment objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | A roleDefinitions létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMembership objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/serviceAction/activateService | Végrehajthatja az Activateservice szolgáltatásműveletet az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | A Disabledirectoryfeature szolgáltatásművelet végrehajtása az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Végrehajthatja az Enabledirectoryfeature szolgáltatásműveletet az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | A Getavailableextentionproperties szolgáltatásművelet végrehajtásaz az Azure Active Directoryban |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | ServicePrincipal objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | A subscribedSkus létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/allProperties/allTasks | User objektumok létrehozása és törlése, illetve az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directorySync/allEntities/allTasks | Az összes művelet végrehajtása az Azure AD Connectben. |
| microsoft.aad.identityProtection/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | A microsoft.azure.advancedThreatProtection összes erőforrásának olvasása. |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection teljes körű felügyelete. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.commerce.billing/allEntities/allTasks | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| microsoft.intune/allEntities/allTasks | Az Intune teljes körű felügyelete. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Megfelelőségkezelő teljes körű felügyelete |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Az Asztali elemzés teljes körű felügyelete. |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| microsoft.office365.lockbox/allEntities/allTasks | Az Office 365 Ügyfélszéf teljes körű felügyelete |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Read securityMessages in microsoft.office365.messageCenter. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI teljes körű felügyelete. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Az Office 365 Védelmi központ teljes körű felügyelete. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió teljes körű felügyelete. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | A microsoft.windows.defenderAdvancedThreatProtection összes erőforrásának olvasása. |

### <a name="compliance-administrator"></a>Szabályozási ügyintéző
Megtekintheti és kezelheti a megfelelőségi konfigurációt és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Megfelelőségkezelő teljes körű felügyelete |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió teljes körű felügyelete. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="conditional-access-administrator"></a>Feltételes hozzáférésű rendszergazda
A feltételes hozzáférési lehetőségekkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | A policies.conditionalAccess tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Szabályzatok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | A policies.conditionalAccess tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | A policies.conditionalAccess tulajdonság frissítése az Azure Active Directoryban. |

### <a name="crm-service-administrator"></a>CRM-szolgáltatásadminisztrátor
A Dynamics 365 termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="customer-lockbox-access-approver"></a>Ügyfélszéf hozzáférés-jóváhagyója
Jóváhagyhatja a szervezeti ügyféladatok elérésére vonatkozó Microsoft-támogatási kérelmeket. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.lockbox/allEntities/allTasks | Az Office 365 Ügyfélszéf teljes körű felügyelete |

### <a name="device-administrators"></a>Eszközadminisztrátorok
Ezen szerepkör tagjai a helyi Rendszergazdák csoport, az Azure AD-hez csatlakoztatott eszközök kerülnek.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | A GroupSetting objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | A GroupSettingTemplate objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |

### <a name="directory-readers"></a>Directory-olvasók
Alapszintű directory információkat olvashatja. Az alkalmazásokhoz való hozzáférés biztosítása, nem javasolt a felhasználók számára.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Az AdministrativeUnit objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/administrativeUnits/members/read | Az administrativeUnits.members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/basic/read | Az Application objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Owners/Read | Az applications.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/policies/read | Az applications.policies tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contacts/Basic/Read | A Contact objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/contacts/memberOf/read | A contacts.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contracts/Basic/Read | A Contract objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/Basic/Read | A Device objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/memberOf/read | A devices.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/read | A devices.registeredOwners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredUsers/read | A revices.registeredUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/basic/read | A DirectoryRole objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | A rirectoryRoles.eligibleMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/members/read | A directoryRoles.members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/domains/basic/read | A Domain objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/appRoleAssignments/read | A groups.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Basic/Read | A Group objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/memberOf/read | A groups.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Read | A groups.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Read | A groups.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/read | A groups.settings tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/basic/read | A GroupSetting objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | A GroupSettingTemplate objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Az oAuth2PermissionGrants alaptulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Organization/Basic/Read | Az Organization objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Az organization.trustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/roleAssignments/basic/read | A szerepkör-hozzárendelések alaptulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/roleDefinitions/basic/read | A szerepkör-definíciók alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/read | A ServicePrincipal objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | A servicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/read | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/read | A servicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/subscribedSkus/basic/read | A SubscribedSku objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Read | Felhasználók alaptulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/directReports/read | A users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | A users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="directory-synchronization-accounts"></a>Címtár-szinkronizálási fiókok
Csak Azure AD Connect szolgáltatást használják.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Az organization.dirSync tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/delete | Szabályzatok törlése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Basic/Read | A Policy objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Basic/Update | A Policy objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Owners/Read | A policies.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Owners/Update | A policies.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | A policies.policiesAppliedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/tenantDefault/update | A policies.tenantDefault tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | A servicePrincipals.appRoleAssignedTo tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | A servicePrincipals.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/audience/update | A servicePrincipals.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/authentication/update | A servicePrincipals.authentication tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/read | A ServicePrincipal objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/update | A ServicePrincipal objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/create | A servicePrincipals létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/credentials/update | A servicePrincipals.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/read | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | A servicePrincipals.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | A servicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/permissions/update | A servicePrincipals.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/read | A servicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | A servicePrincipals.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directorySync/allEntities/allTasks | Az összes művelet végrehajtása az Azure AD Connectben. |

### <a name="directory-writers"></a>Címtárírók
Olvasás, és alapszintű directory adatokat írnak a. Az alkalmazásokhoz való hozzáférés biztosítása, nem javasolt a felhasználók számára.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Group objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/appRoleAssignments/update | A groups.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Basic/Update | A Group objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/members/update | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Update | A groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/update | A groups.settings tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/basic/update | A GroupSetting objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/create | GroupSetting objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/delete | A GroupSetting objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Update | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | A users.userPrincipalName tulajdonság frissítése az Azure Active Directoryban. |

### <a name="exchange-service-administrator"></a>Exchange-szolgáltatások rendszergazdája
Az Exchange termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | A groups.unified tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/unified/basic/update | Az Office 365-csoportok alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/unified/create | Office 365-csoportok létrehozása. |
| microsoft.aad.directory/groups/unified/delete | Office 365-csoportok törlése. |
| microsoft.aad.directory/groups/unified/members/update | Az Office 365-csoportok tagságának frissítése. |
| microsoft.aad.directory/groups/unified/owners/update | Az Office 365-csoportok tulajdonosának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="guest-inviter"></a>Vendég meghívója
A tagok vendégmeghívási jogosultágát szabályozó beállítástól függetlenül meghívhat vendégeket.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Read | Felhasználók alaptulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/directReports/read | A users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/inviteGuest | Vendégfelhasználók meghívása az Azure Active Directoryba. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | A users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="helpdesk-administrator"></a>Ügyfélszolgálati adminisztrátor
Alaphelyzetbe állíthatja a nem rendszergazdák és a segélyszolgálat rendszergazdák jelszavát.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/password/update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="information-protection-administrator"></a>Information Protection-rendszergazda
Az Azure Information Protection termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection teljes körű felügyelete. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="intune-service-administrator"></a>Intune szolgáltatás rendszergazdája
Az Intune termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | A Contact objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/contacts/create | Contact objektumok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contacts/DELETE | A Contact objektumok törlése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/Basic/Update | A Device objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/Create | Device objektumok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/DELETE | A Device objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/update | A devices.registeredOwners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredUsers/update | A devices.registeredUsers tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/appRoleAssignments/update | A groups.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Basic/Update | A Group objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Group objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/delete | A Group objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/hiddenMembers/read | A groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/members/update | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Update | A groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/restore | A Group objektumok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/update | A groups.settings tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Update | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.intune/allEntities/allTasks | Az Intune teljes körű felügyelete. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |

### <a name="license-administrator"></a>Speciális licencadminisztrátor
A felhasználók és csoportok terméklicencek kezelheti.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| microsoft.aad.directory/users/usageLocation/update | A users.usageLocation tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="lync-service-administrator"></a>Lync-szolgáltatások rendszergazdája
A Skype Vállalati verzió termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió teljes körű felügyelete. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="message-center-reader"></a>Üzenetközpont-olvasó
Csak a szervezet Office 365 Üzenetközpontbeli üzeneteit és frissítéseit olvashatja. Ez a szerepkör nem fér megtekintését, hozzon létre és kezelhetik a támogatási jegyeket.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>1. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | A Contact objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/contacts/create | Contact objektumok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contacts/DELETE | A Contact objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Group objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/members/update | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Update | A groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Update | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/delete | A User objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/password/update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.directory/users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | A users.userPrincipalName tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="partner-tier2-support"></a>2. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | A Contact objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/contacts/create | Contact objektumok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contacts/DELETE | A Contact objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/domains/allTasks | Domain objektumok létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/delete | A Group objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/members/update | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/restore | A Group objektumok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/organization/basic/update | Az Organization objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Az organization.trustedCAsForPasswordlessAuth tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Update | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/delete | A User objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/password/update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.directory/users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | A users.userPrincipalName tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="power-bi-service-administrator"></a>Power BI-szolgáltatásadminisztrátor
A Power BI termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI teljes körű felügyelete. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="privileged-role-administrator"></a>Kiemelt szerepkörű rendszergazda
Az Azure AD szerepkör-hozzárendelések és a Privileged Identity Management minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | A DirectoryRole objektumok frissítése az Azure Active Directoryban. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Jelentésolvasó
Megtekintheti a bejelentkezési és a naplózási jelentéseket.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |

### <a name="security-administrator"></a>Biztonsági rendszergazda
Olvassa el a biztonsági adatokat és jelentéseket, és beállíthatja az Azure AD-konfiguráció és az Office 365.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | A applications.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Basic/Update | A Policy objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/delete | Szabályzatok törlése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Owners/Update | A policies.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/tenantDefault/update | A policies.tenantDefault tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | A servicePrincipals.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.identityProtection/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Az összes erőforrás frissítése a következőben: microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.protectionCenter/allEntities/read | Az Office 365 Védelmi központ minden aspektusának olvasása. |
| microsoft.office365.protectionCenter/allEntities/update | Az összes erőforrás frissítése a következőben: microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="security-reader"></a>Biztonsági olvasó
Megtekintheti a biztonsági információkat és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/signInReports/allProperties/read | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.identityProtection/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.protectionCenter/allEntities/read | Az Office 365 Védelmi központ minden aspektusának olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="service-support-administrator"></a>Szolgáltatástámogatási rendszergazda
Megtekintheti a szolgáltatásállapot-adatokat, és kezelheti a támogatási jegyeket.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="sharepoint-service-administrator"></a>SharePoint szolgáltatás-rendszergazda
A SharePoint-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | A groups.unified tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/unified/basic/update | Az Office 365-csoportok alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/unified/create | Office 365-csoportok létrehozása. |
| microsoft.aad.directory/groups/unified/delete | Office 365-csoportok törlése. |
| microsoft.aad.directory/groups/unified/members/update | Az Office 365-csoportok tagságának frissítése. |
| microsoft.aad.directory/groups/unified/owners/update | Az Office 365-csoportok tulajdonosának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="teams-communications-administrator"></a>Teams-kommunikációs rendszergazda
Kezelheti a Microsoft Teams szolgáltatás hívási és értekezletfunkcióit.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |

### <a name="teams-communications-support-engineer"></a>Teams-kommunikációs támogatási szakember
Speciális eszközökkel háríthatja el a Teams szolgáltatásban fellépő kommunikációs problémákat.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="teams-communications-support-specialist"></a>Teams-kommunikációs támogatási szakértő
Alapszintű eszközökkel háríthatja el a Teams szolgáltatásban fellépő kommunikációs problémákat.

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="teams-service-administrator"></a>Teams-szolgáltatásadminisztrátor
Kezelheti a Microsoft Teams szolgáltatást. 

  > [!NOTE]
  > Ez a szerepkör jogosult további Azure Active Directory-en kívül. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | A groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | A groups.unified tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/unified/basic/update | Az Office 365-csoportok alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/unified/create | Office 365-csoportok létrehozása. |
| microsoft.aad.directory/groups/unified/delete | Office 365-csoportok törlése. |
| microsoft.aad.directory/groups/unified/members/update | Az Office 365-csoportok tagságának frissítése. |
| microsoft.aad.directory/groups/unified/owners/update | Az Office 365-csoportok tulajdonosának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |

### <a name="user-account-administrator"></a>Felhasználóifiók-adminisztrátor
A felhasználókkal és a csoportokkal kapcsolatos összes felügyeleti rendelkezik, beleértve a korlátozott rendszergazdák jelszavának alaphelyzetbe állítását is.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | AppRoleAssignment objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/delete | Az AppRoleAssignment objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az AppRoleAssignment objektumok frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/contacts/basic/update | A Contact objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/contacts/create | Contact objektumok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contacts/DELETE | A Contact objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/appRoleAssignments/update | A groups.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Basic/Update | A Group objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Group objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/delete | A Group objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/hiddenMembers/read | A groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/members/update | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Update | A groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/restore | A Group objektumok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/update | A groups.settings tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Update | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Create | User objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/users/delete | A User objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/password/update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.directory/users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | A users.userPrincipalName tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-beli támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

## <a name="deprecated-roles"></a>Elavult szerepkörök

A következő szerepkörök nem használható. Ezeket a már elavult, és eltávolítja a későbbiekben az Azure AD-ből.

* Speciális licencek adminisztrátora
* Eszköz-csatlakoztatás
* Eszközkezelők
* Eszközök felhasználói számára
* Ellenőrzött e-mail című felhasználó-létrehozó
* Postaláda-adminisztrátor
* Munkahelyi eszköz-csatlakoztatás

## <a name="next-steps"></a>További lépések

* Felhasználók hozzárendelése egy Azure-előfizetés rendszergazdájaként kapcsolatos további információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
