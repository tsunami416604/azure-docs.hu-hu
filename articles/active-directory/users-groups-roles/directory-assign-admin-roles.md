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
ms.date: 08/21/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 6c97d7c2f901110421f9fc5d0a1d4468d832c472
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056083"
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

* **[Felhasználóifiók-adminisztrátor](#user-account-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók létrehozása és kezelése a felhasználók és csoportok minden aspektusát. Ez a szerepkör ezenkívül kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése is. Bizonyos korlátozások vonatkoznak. Például ez a szerepkör nem teszi lehetővé egy globális rendszergazda törlése. Felhasználói fiókok adminisztrátorai módosíthatja a felhasználók, az ügyfélszolgálat adminisztrátorai és egyéb felhasználói fiók csak rendszergazdák jelszavát.

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


### <a name="to-add-a-colleague-as-a-global-administrator"></a>Globális rendszergazdaként a munkatársak hozzáadása

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely egy globális rendszergazdai vagy a kiemelt szerepkörű rendszergazda számára a bérlő címtárát.

   ![Az azure AD felügyeleti központ megnyitása](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. Válassza ki **felhasználók**.

3. Keresse meg a felhasználó által kijelölt globális rendszergazdaként, és az adott felhasználó paneljének megnyitásához.

4. A felhasználói panelen válassza ki a **címtárbeli szerepkör**.
 
5. Címtár szerepkör panelen válassza ki a **globális rendszergazdai** szerepkört, és mentse.

## <a name="detailed-azure-active-directory-permissions"></a>Az Azure Active Directory-engedélyek részletes
Az alábbi táblázatok ismertetik az Azure Active Directory minden egyes szerepkörhöz megadott engedélyeket. Néhány szerepkört, például a globális rendszergazda, előfordulhat, hogy további engedélyekkel rendelkeznek a Microsoft services outide az Azure Active Directory.


### <a name="application-administrator"></a>Alkalmazás-rendszergazda
Az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Application objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Delete | Application objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Update | Alkalmazások általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Az Applications.DefaultPolicy tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Update/Owners | Az Applications.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/AppRoleAssignment/Create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/AppRoleAssignment/Delete | AppRoleAssignment objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/AppRoleAssignment/Update | Az alkalmazások szerepkör-hozzárendelései alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Create | Policy objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Delete | Policy objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Update | A Policy objektumok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Update/Owners | A Policies.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Minden felhasználó nevében minden erőforrást jóváhagyhat az Azure Active Directory kivételével (Azure AD Graph és Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | ServicePrincipal objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Delete | Szolgáltatásnevek törlése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | A ServicePrincipals.OwnedObjects tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | A ServicePrincipals.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | A ServicePrincipals.DefaultPolicy tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | A ServicePrincipals.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/AssignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| microsoft.aad.reports/AllEntities/Read | Azure AD-jelentések olvasása. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="application-developer"></a>Alkalmazásfejlesztő
Hozhat létre alkalmazást az alkalmazásregisztrációk független a **felhasználók regisztrálhatnak alkalmazásokat** beállítás.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Application objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | OAuth2PermissionGrant objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | ServicePrincipal objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |

### <a name="billing-administrator"></a>Számlázási adminisztrátor
Számlázással kapcsolatos általános feladatokat hajthat végre, például frissítheti a fizetési adatokat.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Tekintse meg a szerepkör leírása fent további információt.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Update | Szervezetek általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.billing/AllEntities/AllActions | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-application-administrator"></a>Felhőalkalmazás-rendszergazda
Az Application Proxy kivételével az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Application objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Delete | Application objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Update | Alkalmazások általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Az Applications.DefaultPolicy tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Update/Owners | Az Applications.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/AppRoleAssignment/Create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/AppRoleAssignment/Delete | AppRoleAssignment objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/AppRoleAssignment/Update | Az alkalmazások szerepkör-hozzárendelései alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Create | Policy objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Delete | Policy objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Update | A Policy objektumok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Update/Owners | A Policies.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Minden felhasználó nevében minden erőforrást jóváhagyhat az Azure Active Directory kivételével (Azure AD Graph és Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | ServicePrincipal objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Delete | Szolgáltatásnevek törlése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | A ServicePrincipals.OwnedObjects tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | A ServicePrincipals.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | A ServicePrincipals.DefaultPolicy tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | A ServicePrincipals.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/AssignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| microsoft.aad.reports/AllEntities/Read | Azure AD-jelentések olvasása. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="company-administrator"></a>Céges rendszergazda
Az Azure AD-identitásokat használó Azure AD- és Microsoft-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik. A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja a folyamat "Vállalati rendszergazdaként". Az "Globális rendszergazda" a [az Azure portal](https://portal.azure.com).

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | AdministrativeUnit objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Application/AllActions/AllProperties | Application objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | AppRoleAssignment objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | CollaborationSpace objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Contact objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Device/AllActions/AllProperties | Device objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | DirectoryRole objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | DirectoryRoleTemplate objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | DirectorySetting objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | DirectorySettingTemplate objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Tartományok létrehozása és törlése, illetve az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/AllActions/AllProperties | Group objektumok létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | LoginTenantBranding objektumok létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | OAuth2PermissionGrant objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Policy objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | Az összes erőforrás-kérelem jóváhagyása az összes felhasználó nevében, beleértve az Azure Active Directoryt (Azure AD Graph és Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | ServicePrincipal objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Szervezetek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/AllActions/AllProperties | Felhasználók létrehozása és törlése, illetve az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.aadconnect/AllEntities/AllActions | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.aad.aadconnect. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.billing/AllEntities/AllActions | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| microsoft.aad.compliance/AllEntities/AllActions | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a megfelelőségi központban. |
| microsoft.aad.directorysync/AllEntities/AllActions | Az összes művelet végrehajtása az Azure AD Connectben. |
| microsoft.aad.lockbox/AllEntities/AllActions | A Lockbox szolgáltatás teljes körű felügyelete. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | A Privileged Role Management szolgáltatás teljes körű felügyelete. |
| microsoft.aad.reports/AllEntities/AllActions | Azure AD-jelentések olvasása és konfigurálása. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.crm/AllEntities/AllActions | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |
| microsoft.exchange/AllEntities/AllActions | Az Exchange Online teljes körű felügyelete. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Az Information Protection teljes körű felügyelete. |
| microsoft.intune/AllEntities/AllActions | Az Intune teljes körű felügyelete. |
| microsoft.powerbi/AllEntities/AllActions | A Power BI teljes körű felügyelete. |
| microsoft.protectioncenter/AllEntities/AllActions | Az Office 365 Védelmi központ felügyelete. |
| microsoft.sharepoint/AllEntities/AllActions | A SharePoint Online felügyelete. |
| microsoft.skypeforbusiness/AllEntities/AllActions | A Skype Vállalati online verzió felügyelete. |

### <a name="compliance-administrator"></a>Szabályozási ügyintéző
Megtekintheti és kezelheti a megfelelőségi konfigurációt és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.compliance/AllEntities/AllActions | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a megfelelőségi központban. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.exchange/Compliance/AllActions | A megfelelőség felügyelete az Exchange Online-ban. |
| microsoft.sharepoint/Compliance/AllActions | A megfelelőség felügyelete a SharePoint Online-ban. |
| microsoft.skypeforbusiness/Compliance/AllActions | A megfelelőség felügyelete a Skype Vállalati online verzióban. |

### <a name="conditional-access-administrator"></a>Feltételes hozzáférésű rendszergazda
A feltételes hozzáférési lehetőségekkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | A ConditionalAccessPolicys létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | A ConditionalAccessPolicys törlése az Azure Active Directoryban. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | A ConditionalAccessPolicys általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | A ConditionalAccessPolicys.Owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | A ConditionalAccessPolicys.PolicyAppliedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | A ConditionalAccessPolicys általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | A ConditionalAccessPolicys.Owners tulajdonság frissítése az Azure Active Directoryban. |

### <a name="device-administrators"></a>Eszközadminisztrátorok

Az ehhez a szerepkörhöz tartozó felhasználók a helyi gépek rendszergazdái lesznek az Azure Active Directoryval összekapcsolt összes Windows 10-eszközön. Nem rendelkeznek képes kezelni az eszközobjektumok az Azure Active Directoryban.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

### <a name="directory-readers"></a>Directory-olvasók
Alapszintű directory információkat olvashatja. Alkalmazásokhoz való hozzáférés biztosításához.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Az AdministrativeUnit objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Az AdministrativeUnits.Members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Read | Alkalmazások általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Application/Read/Owners | Az Applications.Owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/CollaborationSpace/Read | A CollaborationSpace objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | A CollaborationSpaces.Owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Read | A Contact objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Read/MemberOf | A Contacts.MemberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Device/Read | A Device objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Device/Read/MemberOf | A Devices.MemberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Device/Read/RegisteredOwners | A Devices.RegisteredOwners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Device/Read/RegisteredUsers | A Devices.RegisteredUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/DirectoryRole/Read | A DirectoryRoles általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | A DirectoryRoles.EligibleMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/DirectoryRole/Read/Members | A DirectoryRoles.Members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/DirectorySetting/Read | A címtárbeállítások alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/DirectorySettingTemplate/Read | A DirectorySettingTemplate objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Domain/Read | A tartományok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read | csoportok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | A Groups.AppRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read/MemberOf | A Groups.MemberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read/Members | A Groups.Members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read/Owners | A Groups.Owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read/Settings | A Groups.Settings tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Az OAuth2PermissionGrant objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read | A szolgáltatásnevek Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | A ServicePrincipals.AppRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | A ServicePrincipals.AppRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | A ServicePrincipals.DefaultPolicy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | A ServicePrincipals.MemberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | A ServicePrincipals.OAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | A ServicePrincipals.Owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | A ServicePrincipals.OwnedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Read | Szervezetek általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read | A felhasználók általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | A Users.AppRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/DirectReports | A Users.DirectReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/InvitedBy | A Users.InvitedBy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/InvitedUsers | A Users.InvitedUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/Manager | A Users.Manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/MemberOf | A Users.MemberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | A Users.OAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/OwnedDevices | A Users.OwnedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/OwnedObjects | A Users.OwnedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/RegisteredDevices | A Users.RegisteredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="directory-synchronization-accounts"></a>Címtár-szinkronizálási fiókok
Csak Azure AD Connect szolgáltatást használják.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Policy objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Delete | Policy objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Read | A Policy objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Read/Owners | A Policies.Owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | A Policies.PolicyAppliedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Update | A Policy objektumok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Update/Owners | A Policies.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Create | ServicePrincipal objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read | A szolgáltatásnevek Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | A ServicePrincipals.AppRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | A ServicePrincipals.AppRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | A ServicePrincipals.DefaultPolicy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | A ServicePrincipals.MemberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | A ServicePrincipals.OAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | A ServicePrincipals.Owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | A ServicePrincipals.OwnedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | A ServicePrincipals.OwnedObjects tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | A ServicePrincipals.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | A ServicePrincipals.DefaultPolicy tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | A ServicePrincipals.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Update/DirSync | Az Organizations.DirSync tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directorysync/AllEntities/AllActions | Az összes művelet végrehajtása az Azure AD Connectben. |

### <a name="directory-writer"></a>Címtár-írója
Olvasás, és alapszintű directory adatokat írnak a. Az alkalmazásokhoz való hozzáférés biztosítása

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | DirectorySetting objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/DirectorySetting/Delete | DirectorySetting objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/DirectorySetting/Update | A DirectorySettingTemplate objektumok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/CreateAsOwner | Group objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/Group/Read | csoportok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update | Csoportok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | A Groups.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Members | A Groups.Members tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Owners | A Groups.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Settings | A Groups.Settings tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/AssignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update | Felhasználók általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | A Users.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/Manager | A Users.Manager tulajdonság frissítése az Azure Active Directoryban. |

### <a name="dynamics-365-service-administrator"></a>Dynamics 365 szolgáltatás-rendszergazda
A Dynamics 365 termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.crm/AllEntities/AllActions | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |

### <a name="exchange-service-administrator"></a>Exchange-szolgáltatások rendszergazdája
Az Exchange termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.exchange/AllEntities/AllActions | Az Exchange Online teljes körű felügyelete. |

### <a name="guest-inviter"></a>Vendég meghívója
Független a vendégfelhasználókat is meghívhat az **tagjai is meghívhatnak vendégeket** beállítás.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a Vendég-szerepkört.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Vendégfelhasználók meghívása az Azure Active Directoryba. |
| microsoft.aad.directory/User/Read | A felhasználók általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | A Users.AppRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/DirectReports | A Users.DirectReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/InvitedBy | A Users.InvitedBy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/InvitedUsers | A Users.InvitedUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/Manager | A Users.Manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/MemberOf | A Users.MemberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | A Users.OAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/OwnedDevices | A Users.OwnedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/OwnedObjects | A Users.OwnedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Read/RegisteredDevices | A Users.RegisteredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="helpdesk-administrator"></a>Ügyfélszolgálati adminisztrátor
Alaphelyzetbe állíthatja a nem rendszergazdák és a segélyszolgálat rendszergazdák jelszavát.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Frissítse a korlátozott rendszergazdák és más ügyfélszolgálati rendszergazdák az Azure Active Directoryban jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="information-protection-administrator"></a>Information Protection-rendszergazda
Az Azure Information Protection termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Group/Read | csoportok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Az Information Protection teljes körű felügyelete. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="intune-service-administrator"></a>Intune-szolgáltatásadminisztrátor
Az Intune termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Contact objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Delete | Contact objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Update | A Contact objektumok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Device/Create | Eszközök létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Device/Delete | Eszközök törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Device/Update | Az eszközök alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Device/Update/RegisteredOwners | A Devices.RegisteredOwners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Device/Update/RegisteredUsers | A Devices.RegisteredUsers tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/CreateAsOwner | Group objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/Group/Delete | Group objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read | csoportok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read/HiddenMembers | A Groups.HiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update | Csoportok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | A Groups.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Members | A Groups.Members tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Owners | A Groups.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Settings | A Groups.Settings tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update | Felhasználók általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | A Users.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/Manager | A Users.Manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.intune/AllEntities/AllActions | Az Intune teljes körű felügyelete. |

### <a name="lync-service-administrator"></a>Lync-szolgáltatások rendszergazdája
A Skype Vállalati verzió termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.skypeforbusiness/AllEntities/AllActions | A Skype Vállalati online verzió felügyelete. |

### <a name="message-center-reader"></a>Üzenetközpont-olvasó
Csak a szervezet Office 365 Üzenetközpontbeli üzeneteit és frissítéseit olvashatja. 

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Group/Read | csoportok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Üzenetközpontban. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |

### <a name="partner-tier1-support"></a>1. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.directory/Contact/Create | Contact objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Delete | Contact objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Update | A Contact objektumok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/CreateAsOwner | Group objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/Group/Read | csoportok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Members | A Groups.Members tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Owners | A Groups.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/AssignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Delete | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update | Felhasználók általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | A Users.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/Manager | A Users.Manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/PasswordUserScope | Frissítse a jelszavakat a nem rendszergazda az Azure Active Directoryban. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="partner-tier2-support"></a>2. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.directory/Contact/Create | Contact objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Delete | Contact objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Update | A Contact objektumok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Domain/AllActions | Domain objektumok létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Delete | Group objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read | csoportok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Members | A Groups.Members tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Update | Szervezetek általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/AssignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Delete | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update | Felhasználók általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | A Users.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/Manager | A Users.Manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/Password | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="power-bi-service-administrator"></a>Power BI-szolgáltatásadminisztrátor
A Power BI termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.powerbi/AllEntities/AllActions | A Power BI teljes körű felügyelete. |

### <a name="privileged-role-administrator"></a>Kiemelt szerepkörű rendszergazda
Szerepkör-hozzárendelésekkel kezelheti az Azure ad-ben

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | A DirectoryRoles általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | A Privileged Role Management szolgáltatás teljes körű felügyelete. |

### <a name="reports-reader"></a>Jelentésolvasó
Megtekintheti a bejelentkezési és a naplózási jelentéseket.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | Azure AD-jelentések olvasása. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.usagereports/AllEntities/Read | Az Office 365-beli használati jelentések olvasása. |

### <a name="security-administrator"></a>Biztonsági rendszergazda
Olvashatja a biztonsági információk és jelentések

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Az Applications.DefaultPolicy tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Create | Policy objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Delete | Policy objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Update | A Policy objektumok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Policy/Update/Owners | A Policies.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | A ServicePrincipals.DefaultPolicy tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Teljes körű olvasási hozzáférés a Privileged Identity Managementhez. |
| microsoft.protectioncenter/AllEntities/Read | Az Office 365 Védelmi központ minden aspektusának olvasása. |
| microsoft.protectioncenter/AllEntities/Update | Az Office 365 Védelmi központ felügyelete. |

### <a name="security-reader"></a>Biztonsági olvasó
Megtekintheti a biztonsági adatokat és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Teljes körű olvasási hozzáférés a Privileged Identity Managementhez. |
| microsoft.protectioncenter/AllEntities/Read | Az Office 365 Védelmi központ minden aspektusának olvasása. |

### <a name="service-support-administrator"></a>Szolgáltatástámogatási rendszergazda
Megtekintheti a szolgáltatásállapot-adatokat, és kezelheti a támogatási jegyeket.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="sharepoint-service-administrator"></a>SharePoint szolgáltatás-rendszergazda
A SharePoint-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. Szerepkör leírása fent további információt talál.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.sharepoint/AllEntities/AllActions | A SharePoint Online felügyelete. |

### <a name="user-account-administrator"></a>Felhasználóifiók-adminisztrátor
Felhasználók és csoportok minden aspektusát felügyelheti

  > [!NOTE]
  > Ez a szerepkör örökli a további engedélyeket a [felhasználói szerepkör](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/AppRoleAssignment/Delete | AppRoleAssignment objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/AppRoleAssignment/Update | Az alkalmazások szerepkör-hozzárendelései alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Create | Contact objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Delete | Contact objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Contact/Update | A Contact objektumok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Create | Group objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/CreateAsOwner | Group objektumok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/Group/Delete | Group objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read | csoportok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Read/HiddenMembers | A Groups.HiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update | Csoportok általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | A Groups.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Members | A Groups.Members tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Owners | A Groups.Owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Group/Update/Settings | A Groups.Settings tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Az Organizations.TrustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/User/AssignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Create | Felhasználók létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Delete | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update | Felhasználók általános tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | A Users.AppRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/Manager | A Users.Manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Frissítse a jelszót a korlátozott rendszergazdák, a segélyszolgálat rendszergazdák és egyéb felhasználói fiók a rendszergazdák az Azure Active Directoryban. További részleteket talál online dokumentációjában talál. |
| microsoft.aad.accessservice/AllEntities/AllActions | Minden erőforrás létrehozása és törlése, illetve az alapvető tulajdonságok írása és olvasása az Azure Access Control szolgáltatásban. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

## <a name="next-steps"></a>További lépések

* Az Azure-előfizetések rendszergazdáinak módosításáról további információ: [Azure-rendszergazdai szerepkörök felvétele vagy módosítása](../../billing/billing-add-change-azure-subscription-administrator.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
