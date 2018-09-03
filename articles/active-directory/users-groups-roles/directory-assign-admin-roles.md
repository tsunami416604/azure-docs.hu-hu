---
title: Rendszergazdai szerepkörök hozzárendelése az Azure Active Directory |} A Microsoft Docs
description: Rendszergazda szerepkörrel is adja hozzá a felhasználók, rendszergazdai szerepkörök hozzárendelése, felhasználók új jelszavainak létrehozására, a felhasználói licencek kezelése vagy tartományok kezelése. Egy rendszergazda szerepkörrel felruházott felhasználó összes felhőszolgáltatás, amely a szervezet előfizetett ugyanazokkal az engedélyekkel rendelkezik.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/31/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 384afb36f2a63fcbf290fa96ed15db2a1f469f55
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337851"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Rendszergazdai jogosultságok kiosztása az Azure Active Directoryban

Az Azure Active Directory (Azure AD), a különböző funkciók kiszolgálása érdekében külön rendszergazdák is kijelölhet. A rendszergazdák a feladatok végrehajtását, mint hozzáadása vagy módosítása a felhasználók, rendszergazdai szerepkörök hozzárendelése, felhasználói jelszó alaphelyzetbe állítása, a felhasználói licencek kezelése és tartománynevek kezelése az Azure AD portálon lehet kiválasztani.

## <a name="details-about-the-global-administrator-role"></a>A globális rendszergazdai szerepkör részleteit

A globális rendszergazda az összes rendszergazdai funkciójához hozzáfér. Alapértelmezés szerint az Azure-előfizetésre feliratkozó személy van rendelve a címtár globális rendszergazdai szerepkörrel. Csak a globális rendszergazdák más rendszergazdai szerepköröket rendelhet hozzá.

## <a name="assign-or-remove-administrator-roles"></a>Vagy vonja meg a rendszergazdai szerepkörökről

Rendszergazdai szerepkörök hozzárendelése az Azure Active Directory felhasználó kapcsolatban lásd: [felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Elérhető szerepkörök

A következő rendszergazdai szerepkörök érhetők el:

* **[Alkalmazás-rendszergazda](#application-administrator)**: az ehhez a szerepkörhöz felhasználók létrehozhatják és vállalati alkalmazásokat, alkalmazásregisztrációkat és alkalmazásproxy-beállítások minden szempontjának kezeléséhez. Ez a szerepkör lehetővé teszi, hogy engedélyt adjanak az delegált engedélyeket, és az Alkalmazásengedélyek, kivéve a Microsoft Graph és az Azure AD Graph is nyújt. Ezen szerepkör tagjai nem adódnak tulajdonosként, amikor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozása.

* **[Alkalmazás fejlesztője](#application-developer)**: a szerepkör felhasználói alkalmazásregisztrációkat hozhat létre. Ha a "Felhasználók regisztrálhatnak alkalmazásokat" beállítás értéke nem. Ez a szerepkör lehetővé teszi a saját nevükben jóváhagyást tagok amikor a "Felhasználók engedélyezhetik, hogy az alkalmazások hozzáférjenek a céges adatok saját nevükben való" beállítás értéke nem. Ezen szerepkör tagjai tulajdonosként való hozzáadásakor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozásakor.

* **[Számlázási adminisztrátor](#billing-administrator)**: lebonyolítja a vásárlásokat, kezeli az előfizetéseket, támogatási jegyeket, és figyeli a szolgáltatás állapotát.

* **[Felhőalkalmazás-rendszergazda](#cloud-application-administrator)**: a szerepkör felhasználói ugyanazokkal az engedélyekkel rendelkeznek az alkalmazás-rendszergazda szerepkörnek képes kezelni az application proxy kivételével. A szerepkörök létrehozása és kezelése vállalati alkalmazásokkal és az alkalmazást az alkalmazásregisztrációk minden aspektusát. Ez a szerepkör lehetővé teszi, hogy engedélyt adjanak az delegált engedélyeket, és az Alkalmazásengedélyek, kivéve a Microsoft Graph és az Azure AD Graph is nyújt. Ezen szerepkör tagjai nem adódnak tulajdonosként, amikor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozása.

* **[Szabályozási ügyintéző](#compliance-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók az Office 365 Security & Compliance Centerben és Exchange felügyeleti központban a felügyeleti engedélyekkel rendelkeznek. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Feltételes hozzáférésű rendszergazda](#conditional-access-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók képesek az Azure Active Directory feltételes hozzáférés beállításainak ellenőrzése.
  > [!NOTE]
  > Exchange ActiveSync feltételes hozzáférési szabályzatot az Azure-beli üzembe helyezéséhez a felhasználó is egy globális rendszergazdának kell lennie.
  
* **[Eszközadminisztrátorok](#device-administrators)**: érhető el ezt a szerepkört csak egy további helyi rendszergazdaként a hozzárendelés [eszközbeállítások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Az ehhez a szerepkörhöz tartozó felhasználók a helyi gépek rendszergazdái lesznek az Azure Active Directoryval összekapcsolt összes Windows 10-eszközön. Nem rendelkeznek képes kezelni az Azure Active Directoryban. 

* **[Címtárolvasók](#directory-readers)**: egy örökölt szerepkör, amely hozzá kell rendelni az alkalmazásokat, amelyek nem támogatják a [hozzájárulási keretrendszer](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Azt nem lehet hozzárendelni egy felhasználóhoz sem.

* **[Címtár-szinkronizálási fiókok](#directory-synchronization-accounts)**: ne használja. Ez a szerepkör automatikusan hozzá lesz rendelve az Azure AD Connect szolgáltatást, és nem szándékos vagy bármely más használata támogatott.

* **[Címtárírók](#directory-writers)**: egy örökölt szerepkör, amely hozzá kell rendelni az alkalmazásokat, amelyek nem támogatják a [hozzájárulási keretrendszer](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Azt nem lehet hozzárendelni egy felhasználóhoz sem.

* **[Szolgáltatás-rendszergazda Dynamics 365 és CRM-Szolgáltatásadminisztrátor](#dynamics-365-service-administrator)**: az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Dynamics 365 online-ban, ha a szolgáltatás nem található, ezenkívül lehetővé teszi a támogatási jegyek kezelését és szolgáltatás állapotának figyelése. További információ: [a szolgáltatás-rendszergazdai szerepkör használata kezelheti a bérlő](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Exchange-szolgáltatások rendszergazdája](#exchange-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Exchange online-ban, globális engedélyekkel rendelkeznek, a szolgáltatás megléte esetén. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Globális / vállalati rendszergazda](#company-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók férhetnek hozzá az Azure Active Directoryval, valamint a szolgáltatások, például az Exchange online-hoz, az Azure Active Directory-identitásokat használó összes rendszergazdai funkciójához SharePoint Online és Skype vállalati Online verzióhoz. A személy, aki regisztrál az Azure Active Directory-bérlő globális rendszergazdája lesz. Csak a globális rendszergazdák más rendszergazdai szerepköröket rendelhet hozzá. A vállalat a egynél több globális rendszergazda is lehet. A globális rendszergazdák bármely felhasználó és minden más rendszergazdák jelszavát is alaphelyzetbe.

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja a folyamat "Vállalati rendszergazdaként". Az "Globális rendszergazda" a [az Azure portal](https://portal.azure.com).
  >
  >

* **[Vendég meghívója](#guest-inviter)**: az ehhez a szerepkörhöz felhasználók kezelhetik az Azure Active Directory B2B vendégfelhasználói meghívókat során a **tagok küldhetnek meghívót** felhasználói beállítás értéke nem. További információ a B2B-együttműködés: [kapcsolatos Azure AD B2B együttműködés](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nem tartalmaz semmilyen más engedélyt.

* **[Information Protection-rendszergazda](#information-protection-administrator)**: az ehhez a szerepkörhöz tartozó felhasználók minden engedéllyel rendelkezni az Azure Information Protection szolgáltatásban. Ez a szerepkör lehetővé teszi, hogy címkéket az Azure Information Protection-házirend konfigurálása, a védelmi sablonokat kezeléséhez és a védelem aktiválása. Ez a szerepkör nem biztosít az Identity Protection Centerben, Privileged Identity Management, Monitor Office 365 Service Healthben vagy Office 365 Security & Compliance Centerben engedélyeket.

* **[Intune-Szolgáltatásadminisztrátor](#intune-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Intune Online globális engedélyekkel rendelkeznek, ha a szolgáltatás nem található. Ez a szerepkör ezenkívül tartalmazza a képes kezelni a felhasználók és eszközök számára annak érdekében, hogy társítja a házirendet, valamint csoportok létrehozásához és kezeléséhez. További információ: [szerepköralapú vezérlés (RBAC) a Microsoft Intune-nal](https://docs.microsoft.com/intune/role-based-access-control)

* **[Licencek adminisztrátora](#license-administrator)**: a szerepkör felhasználói hozzáadása, eltávolítása, és a felhasználók, csoportok (Csoportalapú licencelést használ) a licenc-hozzárendelések frissítése, és beállíthatja a felhasználási hely, a felhasználók. A szerepkör nem teszi lehetővé a beszerzési vagy -előfizetések kezelése, hozzon létre vagy kezelheti a csoportokat, vagy hozzon létre vagy kezelni a felhasználókat a felhasználási hely túli biztosít.

* **[Üzenet Center olvasó](#message-center-reader)**: figyelheti, hogy a szerepkör felhasználói értesítések és a tanácsadás egészségügyi módosításokat [Office 365 üzenetközpontjában](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) a cégük számára konfigurált szolgáltatások, például az Exchange, Intune-ban és a Microsoft Teams. Üzenetközpont heti e-mail emésztett hozzászólás, frissítések fogadása és oszthatnak meg üzenet center bejegyzések az Office 365-ben. Az Azure AD-ben ehhez a szerepkörhöz tartozó felhasználók csak van csak olvasási hozzáféréssel az Azure AD szolgáltatások, például a felhasználók és csoportok. 

* **[1. rétegbeli támogatása partneri](#partner-tier1-support)**: ne használja. Ez a szerepkör elavult és törlődni fog a későbbiekben az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partnerek általi használatra van, és nem célja általános használatra.

* **[2. rétegbeli támogatása partneri](#partner-tier2-support)**: ne használja. Ez a szerepkör elavult és törlődni fog a későbbiekben az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partnerek általi használatra van, és nem célja általános használatra.

* **[Jelszókezelő / ügyfélszolgálati adminisztrátor](#helpdesk-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók is módosíthatja a jelszavát, kezelhetik és szolgáltatások állapotának figyelése. Az ügyfélszolgálat adminisztrátorai csak a felhasználók és más segélyszolgálat rendszergazdák jelszavát módosíthatja. 

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör "Ügyfélszolgálati adminisztrátor" azonosítja. Ezt "Jelszókezelő" szerepel a [az Azure portal](https://portal.azure.com/).
  >
  >
  
* **[A Power BI-Szolgáltatásadminisztrátor](#power-bi-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése szolgáltatásuk, ezenkívül a Microsoft Power BI, a szolgáltatás megléte esetén globális engedélyekkel rendelkeznek. További információ: [a Power BI rendszergazdai szerepkörét ismertető](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Kiemelt szerepkörű rendszergazda](#privileged-role-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendelések az Azure Active Directoryban, valamint az Azure AD Privileged Identity Management belül. Emellett ez a szerepkör lehetővé teszi minden aspektusát Privileged Identity Management kezelését.

* **[Jelentések olvasó](#reports-reader)**: Ezzel a szerepkörrel rendelkező felhasználók megtekinthetik a használati adatok és az Office 365 felügyeleti központot, és a bevezetési környezeti jelentések irányítópult a Power bi csomag jelentéskészítés. Ezenkívül a szerepkör bejelentkezés hozzáférést biztosít jelentéseket és a tevékenység az Azure ad-ben és a Microsoft Graph által visszaadott adatok jelentéskészítési API-t. A jelentések Olvasó szerepkörhöz rendelt felhasználó hozzáférhet, csak a releváns használati és mérőszámairól. Nincs szükség semmilyen konfigurálni vagy a hozzáférés a termék meghatározott felügyeleti központok, mint az Exchange-rendszergazdai jogosultságokkal. 

* **[Biztonsági rendszergazda](#security-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók rendelkeznek a biztonsági olvasói szerepkör, valamint a lehetővé teszi a biztonsággal kapcsolatos szolgáltatások konfigurációjának kezelését csak olvasási engedélyei: Azure Active Directory Identity Protection, Az Azure Information Protection, Privileged Identity Management és az Office 365 Security & Compliance Centerben. Office 365-engedélyekkel kapcsolatos további információt [az engedélyek az Office 365 Security & Compliance Centerben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | Eleme ennek | Teheti meg |
  | --- | --- |
  | Identity Protection Center |<ul><li>A biztonsági olvasó szerepkör összes engedélyt.<li>Ezenkívül lehetővé teszi új jelszavak kivételével az összes IPC műveletek végrehajtásához. |
  | Privileged Identity Management |<ul><li>A biztonsági olvasó szerepkör összes engedélyt.<li>**Nem lehet** kezelése az Azure ad-ben szerepkörtagságai és -beállítások. |
  | <p>A figyelő az Office 365 szolgáltatás állapota</p><p>Office 365 Biztonsági és megfelelőségi központ |<ul><li>A biztonsági olvasó szerepkör összes engedélyt.<li>Konfigurálhatja a komplex veszélyforrások elleni védelem szolgáltatást (kártevők és vírusok védelmi, rosszindulatú URL-cím konfigurációs, URL-cím nyomkövetés, stb.) az összes beállítást. |
  
* **[Biztonsági olvasó](#security-reader)**: Ezzel a szerepkörrel rendelkező felhasználók globális csak olvasási hozzáférése van, többek között az Azure Active Directory, a Identity Protection, a Privileged Identity Management, valamint lehetővé teszi az Azure Active Directory olvasási Jelentkezzen be a jelentések és naplók. A szerepkör csak olvasási jogosultság az Office 365 Security & Compliance Centerben is nyújt. Office 365-engedélyekkel kapcsolatos további információt [az engedélyek az Office 365 Security & Compliance Centerben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | Eleme ennek | Teheti meg |
  | --- | --- |
  | Identity Protection Center |Olvassa el a biztonsági jelentések és a beállítási információk biztonsági funkciók<ul><li>Levélszemét<li>Titkosítás<li>Adatveszteség-megelőzés<li>Kártevőirtó<li>Komplex veszélyforrások elleni védelem<li>Adathalászat elleni<li>Mailflow szabályok |
  | Privileged Identity Management |<p>Csak olvasási hozzáférést minden információt illesztett rendelkezik az Azure AD PIM-ben: szabályzatok és a jelentések az Azure AD szerepkör-hozzárendeléseket, biztonsági ellenőrzi, és a jövőben olvassa el a hozzáférési házirend adatok és jelentések forgatókönyvek mellett az Azure AD szerepkör-hozzárendelés.<p>**Nem lehet** iratkozzon fel az Azure AD PIM-ben, vagy ne módosítsa. A PIM a portálon vagy a Powershellen keresztül valaki ezt a szerepkört a további szerepkörök (például a globális rendszergazdai vagy a kiemelt szerepkörű rendszergazda), számukra egy jelölt felhasználó esetén is aktiválhatja. |
  | <p>A figyelő az Office 365 szolgáltatás állapota</p><p>Office 365 Biztonsági és megfelelőségi központ</p> |<ul><li>Olvasás és riasztáskezelés<li>Biztonsági szabályzatok olvasása<li>Fenyegetések felderítése, a Cloud App Discovery és a Keresés és vizsgálat karantén olvasása<li>Az összes jelentés olvasása |

* **[A szolgáltatástámogatási rendszergazda](#service-support-administrator)**: az ehhez a szerepkörhöz tartozó felhasználók támogatási kérelmeket nyithatnak a Microsoft Azure és az Office 365 szolgáltatásokban, és a szolgáltatás irányítópultját és üzenetközpontját center az Azure Portalon és az Office 365 felügyeleti portálján megtekinthetik a. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[A SharePoint szolgáltatás-rendszergazda](#sharepoint-service-administrator)**: az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft SharePoint online-ban, a szolgáltatás megléte esetén, ezenkívül kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Skype vállalati verzió / Lync-szolgáltatások rendszergazdája](#lync-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Skype vállalati verzió, globális engedélyekkel rendelkeznek a szolgáltatás megléte esetén, valamint kezelheti a Skype-specifikus felhasználói attribútumok az Azure Active A könyvtár. Ez a szerepkör ezenkívül listázását teszi kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése. További információ: [kapcsolatos a Skype for Business rendszergazdai szerepkör](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5).

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja "Lync-Szolgáltatásadminisztrátor". Az "Skype for Business szolgáltatás-rendszergazda" a [az Azure portal](https://portal.azure.com/).
  >
  >

* **[Felhasználóifiók-adminisztrátor](#user-account-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók létrehozása és kezelése a felhasználók és csoportok minden aspektusát. Ez a szerepkör ezenkívül kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése is. Bizonyos korlátozások vonatkoznak. Például ez a szerepkör nem teszi lehetővé egy globális rendszergazda törlése. Felhasználói fiókok adminisztrátorai módosíthatja a felhasználóknak, az ügyfélszolgálat adminisztrátorai és más felhasználói fiókok adminisztrátorai csak jelszavakat.

| Teheti meg | Nem hajtható végre |
| --- | --- |
| <p>Vállalati és felhasználói adatok megtekintése</p><p>Office-támogatási jegyek kezelése</p><p>A felhasználóknak, az ügyfélszolgálat adminisztrátorai és más felhasználói fiókadminisztrátorok csak-jelszavaikat</p><p>Létre és kezelhet felhasználói nézetek</p><p>Létrehozása, szerkesztése, és törli a felhasználók és csoportok és felhasználói licencek korlátozásokkal kezelése. Ő nem törölhet globális rendszergazdát vagy hozhat létre más rendszergazdákat.</p> |<p>Office-termékek számlázási és beszerzési műveletek végrehajtása</p><p>Tartományok kezelése</p><p>Vállalati adatok kezelése</p><p>Rendszergazdai szerepkörök delegálása</p><p>Címtár-szinkronizálás</p><p>Engedélyezheti vagy tilthatja le a többtényezős hitelesítés</p><p>Auditnaplók megtekintése</p> |

## <a name="deprecated-roles"></a>Elavult szerepkörök

A következő szerepkörök nem használható. Ezek már elavult, és a jövőben az Azure AD-ből törlődni fog.

* Speciális licencek adminisztrátora
* Eszköz-csatlakoztatás
* Eszközkezelők
* Eszközök felhasználói számára
* Ellenőrzött e-mail című felhasználó-létrehozó
* Postaláda-adminisztrátor
* Munkahelyi eszköz-csatlakoztatás

## <a name="detailed-azure-active-directory-permissions"></a>Az Azure Active Directory-engedélyek részletes
Az alábbi táblázatok ismertetik az Azure Active Directory minden egyes szerepkörhöz megadott engedélyeket. Néhány szerepkört, például a globális rendszergazda, előfordulhat, hogy további engedélyekkel rendelkeznek a Microsoft services outide az Azure Active Directory.

### <a name="adhoc-license-administrator"></a>Speciális licencek adminisztrátora
Az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Domains/default/Read | Az Azure Active Directory-tartományokban alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Groups.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/default/Read | A csoportok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/memberOf/read | Groups.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Read | Groups.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Read | Olvassa el a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Read | Groups.settings tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Az Azure Active Directoryban az oauth2-engedélyezések alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Frissítse az oauth2-engedélyezések az Azure Active Directoryban. |
| Microsoft.aad.Directory/Organization/default/Read | A szervezet az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Organization.trustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Default/Read | A felhasználók az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/directReports/read | Users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedBy/read | A users.invitedBy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedUsers/read | Users.invitedUsers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="application-administrator"></a>Alkalmazás-rendszergazda
Az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Create | Alkalmazások létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/default/Update | Alkalmazások az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Applications/DELETE | Törölje az alkalmazást az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Owners/Update | Az applications.owners tulajdonság az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Applications/Policies/Update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| microsoft.aad.directory/appRoleAssignments/create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/read | Olvassa el az alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az Azure Active Directoryban hozzárendeléseinek frissítése. |
| microsoft.aad.directory/appRoleAssignments/delete | Alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban törölje. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/default/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/delete | Szolgáltatásnevek törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| microsoft.aad.reports/allEntities/read | Azure AD-jelentések olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="application-developer"></a>Alkalmazásfejlesztő
Független a felhasználók regisztrálhatnak alkalmazásokat, beállítás alkalmazásregisztrációkat hozhat létre.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Alkalmazások létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Az oauth2-engedélyezések létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Szolgáltatásnevek létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |

### <a name="billing-administrator"></a>Számlázási adminisztrátor
Számlázással kapcsolatos általános feladatokat hajthat végre, például frissítheti a fizetési adatokat.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Organization/default/Update | Az Azure Active Directory szervezeti alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Frissítés az Azure Active Directoryban organization.trustedCAsForPasswordlessAuth tulajdonság. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.commerce.billing/allEntities/allTasks | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-application-administrator"></a>Felhőalkalmazás-rendszergazda
Az Application Proxy kivételével az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Create | Alkalmazások létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/default/Update | Alkalmazások az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Applications/DELETE | Törölje az alkalmazást az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Owners/Update | Az applications.owners tulajdonság az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Applications/Policies/Update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| microsoft.aad.directory/appRoleAssignments/create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az Azure Active Directoryban hozzárendeléseinek frissítése. |
| microsoft.aad.directory/appRoleAssignments/delete | Alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban törölje. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/default/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/delete | Szolgáltatásnevek törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| microsoft.aad.reports/allEntities/read | Azure AD-jelentések olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="company-administrator"></a>Céges rendszergazda
Az Azure AD-identitásokat használó Azure AD- és Microsoft-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a szerepkört.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Hozzon létre és administrativeUnits, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/applications/allProperties/allTasks | Hozzon létre, és törölje az alkalmazást, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Hozzon létre és hozzárendeléseinek, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
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
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Hozzon létre és subscribedSkus, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/users/allProperties/allTasks | Hozzon létre, és törli a felhasználókat, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directorySync/allEntities/allTasks | Az összes művelet végrehajtása az Azure AD Connectben. |
| microsoft.aad.identityProtection/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.aad.identityProtection az alapvető tulajdonságainak frissítése. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.aad.reports/allEntities/allTasks | Azure AD-jelentések olvasása és konfigurálása. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.commerce.billing/allEntities/allTasks | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Office 365-megfelelőségi vezető minden szempontjának kezeléséhez |
| Microsoft.office365.Exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| microsoft.intune/allEntities/allTasks | Az Intune teljes körű felügyelete. |
| Microsoft.office365.lockbox/allEntities/allTasks | Office 365-ügyfél kulcstároló minden szempontjának kezeléséhez |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI teljes körű felügyelete. |
| Microsoft.office365.protectionCenter/allEntities/allTasks | Az Office 365 védelmi központ minden szempontjának kezeléséhez. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |

### <a name="compliance-administrator"></a>Szabályozási ügyintéző
Megtekintheti és kezelheti a megfelelőségi konfigurációt és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Office 365-megfelelőségi vezető minden szempontjának kezeléséhez |
| Microsoft.office365.Exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="conditional-access-administrator"></a>Feltételes hozzáférésű rendszergazda
A feltételes hozzáférési lehetőségekkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/default/read | Policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/default/update | Frissítés az Azure Active Directoryban policies.conditionalAccess tulajdonság. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Frissítés az Azure Active Directoryban policies.conditionalAccess tulajdonság. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |

### <a name="crm-service-administrator"></a>CRM-szolgáltatásadminisztrátor
A Dynamics 365 termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="customer-lockbox-access-approver"></a>Ügyfélszéf hozzáférés-jóváhagyója
Jóváhagyhatja a szervezeti ügyféladatok elérésére vonatkozó Microsoft-támogatási kérelmeket.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| Microsoft.office365.lockbox/allEntities/allTasks | Office 365-ügyfél kulcstároló minden szempontjának kezeléséhez |

### <a name="device-administrators"></a>Eszközadminisztrátorok
Ezen szerepkör tagjai a helyi Rendszergazdák csoport, az Azure AD-hez csatlakoztatott eszközök kerülnek.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groupSettings/default/read | Az Azure Active Directoryban groupSettings a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Az Azure Active Directoryban groupSettingTemplates a alapvető tulajdonságainak olvasása. |

### <a name="device-managers"></a>Eszközkezelők
Jóváhagyhatja a szervezeti ügyféladatok elérésére vonatkozó Microsoft-támogatási kérelmeket.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Devices/default/Read | Az eszközökön az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Devices/default/Update | Az Azure Active Directoryban eszközök alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/devices/memberOf/read | A devices.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/read | Devices.registeredOwners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/update | Frissítés az Azure Active Directoryban devices.registeredOwners tulajdonság. |
| microsoft.aad.directory/devices/registeredUsers/read | Devices.registeredUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredUsers/update | Frissítés az Azure Active Directoryban devices.registeredUsers tulajdonság. |

### <a name="directory-readers"></a>Directory-olvasók
Alapszintű directory információkat olvashatja. Az alkalmazásokhoz való hozzáférés biztosítása

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a szerepkört.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/default/read | Az Azure Active Directoryban administrativeUnits a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/administrativeUnits/members/read | Az administrativeUnits.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/default/Read | Az alkalmazások az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Applications/Owners/Read | Az applications.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contacts/default/Read | A partnerek az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/contacts/memberOf/read | Contacts.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contracts/default/Read | A szerződés az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Devices/default/Read | Az eszközökön az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/devices/memberOf/read | A devices.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/read | Devices.registeredOwners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredUsers/read | Devices.registeredUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/default/read | Az Azure Active Directoryban directoryRoles a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | DirectoryRoles.eligibleMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/members/read | DirectoryRoles.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Domains/default/Read | Az Azure Active Directory-tartományokban alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Groups.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/default/Read | A csoportok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/memberOf/read | Groups.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Read | Groups.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Read | Olvassa el a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Read | Groups.settings tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/default/read | Az Azure Active Directoryban groupSettings a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Az Azure Active Directoryban groupSettingTemplates a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Az Azure Active Directoryban az oauth2-engedélyezések alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Organization/default/Read | A szervezet az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Organization.trustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/default/read | A szolgáltatásnevek Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | ServicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/read | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/read | ServicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/subscribedSkus/default/read | Az Azure Active Directoryban subscribedSkus a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Default/Read | A felhasználók az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/directReports/read | Users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedBy/read | A users.invitedBy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedUsers/read | Users.invitedUsers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="directory-synchronization-accounts"></a>Címtár-szinkronizálási fiókok
Csak Azure AD Connect szolgáltatást használják.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a szerepkört.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Frissítés az Azure Active Directoryban organization.dirSync tulajdonság. |
| Microsoft.aad.Directory/Policies/Create | Szabályzatok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/DELETE | Törölje a szabályzatok az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/default/Read | A szabályzatok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Policies/default/Update | Házirendek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Policies/Owners/Read | Policies.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Owners/Update | Frissítés az Azure Active Directoryban policies.owners tulajdonság. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Policies.policiesAppliedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/default/read | A szolgáltatásnevek Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/servicePrincipals/default/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/read | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | ServicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/read | ServicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directorySync/allEntities/allTasks | Az összes művelet végrehajtása az Azure AD Connectben. |

### <a name="directory-writers"></a>Directory-írók
Olvasás, és alapszintű directory adatokat írnak a. Az alkalmazásokhoz való hozzáférés biztosítása

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/default/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| microsoft.aad.directory/groupSettings/create | GroupSettings létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/default/update | Az Azure Active Directoryban groupSettings alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groupSettings/delete | Az Azure Active Directoryban groupSettings törlése. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Default/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |

### <a name="exchange-service-administrator"></a>Exchange-szolgáltatások rendszergazdája
Az Exchange termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.Exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="guest"></a>Vendég
Alapértelmezett szerepkör a vendégfelhasználók számára. Korlátozott számú directory-információk olvashatók.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a szerepkört.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Applications/default/Read | Az alkalmazások az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Applications/Owners/Read | Az applications.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Domains/default/Read | Az Azure Active Directory-tartományokban alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Groups.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/default/Read | A csoportok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/memberOf/read | Groups.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Read | Groups.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Read | Olvassa el a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Read | Groups.settings tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/organization/basicProfile/read | Szervezetek alapvető profiladatainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/default/read | A szolgáltatásnevek Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/members/read | ServicePrincipals.members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/read | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | ServicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/read | ServicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/basicProfile/read | Users.basicProfile tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Default/Read | A felhasználók az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/directReports/read | Users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/eligibleMemberOf/read | Users.eligibleMemberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedBy/read | A users.invitedBy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedUsers/read | Users.invitedUsers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Password/Update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.directory/users/pendingMemberOf/read | Users.pendingMemberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/scopedAdministratorOf/read | Users.scopedAdministratorOf tulajdonság olvasása az Azure Active Directoryban. |

### <a name="guest-inviter"></a>Vendég meghívója
Meghívás vendégfelhasználókat független a tagok is meghívhatja a vendégek beállítást.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a szerepkört.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Default/Read | A felhasználók az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/directReports/read | Users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedBy/read | A users.invitedBy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/inviteGuest | Vendégfelhasználók meghívása az Azure Active Directoryba. |
| microsoft.aad.directory/users/invitedUsers/read | Users.invitedUsers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="helpdesk-administrator"></a>Ügyfélszolgálati adminisztrátor
Alaphelyzetbe állíthatja a nem rendszergazdák és a segélyszolgálat rendszergazdák jelszavát.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Password/Update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="information-protection-administrator"></a>Information Protection-rendszergazda
Az Azure Information Protection termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="intune-service-administrator"></a>Intune-szolgáltatásadminisztrátor
Az Intune termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/default/Update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| Microsoft.aad.Directory/Devices/Create | Eszközök létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/default/Update | Az Azure Active Directoryban eszközök alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Devices/DELETE | Eszközök törlése az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/update | Frissítés az Azure Active Directoryban devices.registeredOwners tulajdonság. |
| microsoft.aad.directory/devices/registeredUsers/update | Frissítés az Azure Active Directoryban devices.registeredUsers tulajdonság. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| Microsoft.aad.Directory/groups/default/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/groups/DELETE | Az Azure Active Directoryban csoportok törlése. |
| microsoft.aad.directory/groups/hiddenMembers/read | Groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Default/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.intune/allEntities/allTasks | Az Intune teljes körű felügyelete. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="license-administrator"></a>Speciális licencadminisztrátor
A felhasználók és csoportok terméklicencek kezelheti.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| microsoft.aad.directory/users/usageLocation/update | Frissítse a users.usageLocation tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="lync-service-administrator"></a>Lync-szolgáltatások rendszergazdája
A Skype Vállalati verzió termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="message-center-reader"></a>Üzenetközpont-olvasó
Csak a szervezet Office 365 Üzenetközpontbeli üzeneteit és frissítéseit olvashatja. 

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.accessmessagecenter/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Üzenetközpontban. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |

### <a name="partner-tier1-support"></a>1. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/default/Update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Default/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Users/DELETE | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Users/Password/Update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| Microsoft.aad.Directory/Users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="partner-tier2-support"></a>2. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/default/Update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| microsoft.aad.directory/domains/allTasks | Hozzon létre és tartományok törlése, és olvassa el és az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/DELETE | Az Azure Active Directoryban csoportok törlése. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Organization/default/Update | Az Azure Active Directory szervezeti alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Frissítés az Azure Active Directoryban organization.trustedCAsForPasswordlessAuth tulajdonság. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Default/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Users/DELETE | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Users/Password/Update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| Microsoft.aad.Directory/Users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="power-bi-service-administrator"></a>Power BI-szolgáltatásadminisztrátor
A Power BI termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI teljes körű felügyelete. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="privileged-role-administrator"></a>Kiemelt szerepkörű rendszergazda
Szerepkör-hozzárendelésekkel kezelheti az Azure ad-ben

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Az Azure Active Directoryban directoryRoles frissítése. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.aad.privilegedIdentityManagement az alapvető tulajdonságainak frissítése. |

### <a name="reports-reader"></a>Jelentésolvasó
Megtekintheti a bejelentkezési és a naplózási jelentéseket.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.reports/allEntities/read | Azure AD-jelentések olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.usageReports/allEntities/Read | Az Office 365-beli használati jelentések olvasása. |

### <a name="security-administrator"></a>Biztonsági rendszergazda
Olvashatja a biztonsági információk és jelentések

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Policies/Update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| Microsoft.aad.Directory/Policies/Create | Szabályzatok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/default/Update | Házirendek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Policies/DELETE | Törölje a szabályzatok az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Owners/Update | Frissítés az Azure Active Directoryban policies.owners tulajdonság. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.identityProtection/allEntities/update | Frissítés microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.protectionCenter/allEntities/Read | Az Office 365 Védelmi központ minden aspektusának olvasása. |
| Microsoft.office365.protectionCenter/allEntities/Update | Frissítés microsoft.office365.protectionCenter található összes erőforrást. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="security-reader"></a>Biztonsági olvasó
Megtekintheti a biztonsági információkat és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.protectionCenter/allEntities/Read | Az Office 365 Védelmi központ minden aspektusának olvasása. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="service-support-administrator"></a>Szolgáltatástámogatási rendszergazda
Megtekintheti a szolgáltatásállapot-adatokat, és kezelheti a támogatási jegyeket.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="sharepoint-service-administrator"></a>SharePoint szolgáltatás-rendszergazda
A SharePoint-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör jogosult addditonal Azure Active Directory-en kívül. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="user-account-administrator"></a>Felhasználóifiók-adminisztrátor
Felhasználók és csoportok minden aspektusát felügyelheti

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/delete | Alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban törölje. |
| microsoft.aad.directory/appRoleAssignments/update | Az Azure Active Directoryban hozzárendeléseinek frissítése. |
| Microsoft.aad.Directory/Contacts/Create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/default/Update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| Microsoft.aad.Directory/groups/default/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/groups/DELETE | Az Azure Active Directoryban csoportok törlése. |
| microsoft.aad.directory/groups/hiddenMembers/read | Groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Create | Felhasználók létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Default/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Users/DELETE | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Users/Password/Update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| Microsoft.aad.Directory/Users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="user"></a>Felhasználó
Alapértelmezett szerepkör tagja számára. Az összes olvashat és írhat a korlátozott számú directory adatait.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Alkalmazások létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| Microsoft.aad.Directory/groups/default/Read | A csoportok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/oAuth2PermissionGrants/create | Az oauth2-engedélyezések létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/oAuth2PermissionGrants/delete | Törölje az oauth2-engedélyezések az Azure Active Directoryban. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Frissítse az oauth2-engedélyezések az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Szolgáltatásnevek létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/users/activateServicePlan | Az Azure Active Directory felhasználók Activateserviceplan. |
| microsoft.aad.directory/users/inviteGuest | Vendégfelhasználók meghívása az Azure Active Directoryba. |
| Microsoft.aad.Directory/Applications/DELETE | Törölje az alkalmazást az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/restore | Állítsa vissza az alkalmazások az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/default/Update | Alkalmazások az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Applications/Owners/Update | Az applications.owners tulajdonság az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Applications/Policies/Update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| Microsoft.aad.Directory/Devices/disable | Letilthatja az eszközöket az Azure Active Directoryban. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/DELETE | Az Azure Active Directoryban csoportok törlése. |
| Microsoft.aad.Directory/groups/default/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groups/dynamicMembershipRule/update | Frissítés az Azure Active Directoryban groups.dynamicMembershipRule tulajdonság. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| Microsoft.aad.Directory/Policies/DELETE | Törölje a szabályzatok az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/default/Update | Házirendek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Policies/Owners/Update | Frissítés az Azure Active Directoryban policies.owners tulajdonság. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/default/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/delete | Szolgáltatásnevek törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directory/users/changePassword | Módosítsa az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/basicProfile/update | Frissítés az Azure Active Directoryban users.basicProfile tulajdonság. |
| Microsoft.aad.Directory/Users/Mobile/Update | Frissítés az Azure Active Directoryban users.mobile tulajdonság. |
| microsoft.aad.directory/users/searchableDeviceKey/update | Frissítés az Azure Active Directoryban users.searchableDeviceKey tulajdonság. |

## <a name="next-steps"></a>További lépések

* Az Azure-előfizetések rendszergazdáinak módosításáról további információ: [Azure-rendszergazdai szerepkörök felvétele vagy módosítása](../../billing/billing-add-change-azure-subscription-administrator.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
