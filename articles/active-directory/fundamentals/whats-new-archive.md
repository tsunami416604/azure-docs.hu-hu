---
title: Archiválás az Azure Active Directory újdonságaihoz? | Microsoft Docs
description: A tartalomkészlet Áttekintés szakaszában található Újdonságok kiadási megjegyzések 6 hónapos tevékenységet tartalmaznak. 6 hónap elteltére az elemeket eltávolítják a fő cikkből, és ebbe az archív cikkbe helyezik.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25191951472e30492606ad97c440a13359c8ef24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253169"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archiválás az Azure Active Directory újdonságaihoz?

Az azure [Active Directory újdonságai? release notes](whats-new.md) cikk az elmúlt hat hónap frissítéseit tartalmazza, míg ez a cikk az összes régebbi információt tartalmazza.

Az Azure Active Directory újdonságai? a kibocsátási megjegyzések a következőkről nyújtanak tájékoztatást:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Változások tervei

---

## <a name="september-2019"></a>2019. szeptember

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Változásterv: A Power BI-tartalomcsomagok eprecációja

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

2019. október 1-jétől kezdődően a Power BI elkezdi elavulttá tenni az összes tartalomcsomagot, beleértve az Azure AD Power BI tartalomcsomagot is. A tartalomcsomag alternatívájaként az Azure AD-munkafüzetek segítségével betekintést nyerhet az Azure AD-vel kapcsolatos szolgáltatásokba. További munkafüzetek érkeznek, beleértve a csak jelentési módban lévő feltételes hozzáférési szabályzatokról szóló munkafüzeteket, az alkalmazás beleegyezésen alapuló elemzési adatait stb.

A munkafüzetekről további információt az [Azure Active Directory-jelentésekhez használt Azure Monitor munkafüzetek című témakörben](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)talál. A tartalomcsomagok epredeprekációjáról további információt a [Power BI-sablonalkalmazások általános elérhetőségének bejelentése](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) című blogbejegyzésben talál.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>A Saját profil átnevezése és integrálása a Microsoft Office-fiók lapjára

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Saját profil/fiók  
**Termék képesség:** Együttműködés

Októbertől kezdődően a Saját profil élmény lesz a Fiókom. Ennek a változásnak a részeként, mindenhol, ahol jelenleg a Saját profil azt mondja, **a** **Saját fiók** lesz. Az elnevezési változáson és a tervezési fejlesztéseken felül a frissített felület további integrációt kínál a Microsoft Office-fiók lappal. Pontosabban az Office-telepítések és -előfizetések az **Áttekintő fiók** lapon érhetők el, **az** Adatvédelem lapon pedig az Office-hoz kapcsolódó kapcsolattartási beállításokkal.

A Saját profil (előzetes verzió) élményről a [Saját profil (előzetes verzió) portál – áttekintés című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Csoportok és tagok tömeges kezelése CSV-fájlok használatával az Azure AD portálon (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

Örömmel jelentjük be, hogy az Azure AD-portálon elérhetők a tömeges csoportkezelési élmények nyilvános előzetes verziói. Most már használhatja a CSV-fájlt és az Azure AD portált a csoportok és a taglisták kezeléséhez, beleértve a következőket:

- Tagok hozzáadása vagy eltávolítása egy csoportból.

- A csoportok listájának letöltése a könyvtárból.

- Egy adott csoport csoporttagjainak listájának letöltése.

További információt a [Tagok tömeges hozzáadása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), A tagok tömeges [eltávolítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), a Tagok [tömeges letöltése lista](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)és a Tömeges [letöltési csoportok lista című témakörben talál.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>A dinamikus hozzájárulás mostantól egy új rendszergazdai hozzájárulási végponton keresztül támogatott

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

Létrehoztunk egy új rendszergazdai hozzájárulási végpontot a dinamikus hozzájárulás támogatására, amely hasznos azon alkalmazások számára, amelyek a dinamikus jóváhagyási modellt szeretnék használni a Microsoft Identity platformon.

Az új végpont használatáról a Rendszergazda [hozzájárulási végpont használata című](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)témakörben talál további információt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Új összevont alkalmazások érhetők el az Azure AD Alkalmazásgalériában – 2019.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2019 szeptemberében hozzáadtuk ezt a 29, Federation támogatással rendelkező új alkalmazást az alkalmazásgalériához:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access&trade; for Ethidex Compliance Office - Single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur Travel and Expense](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial) `https://apps.yeeflow.com/`, ARC [Facilities](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), Wide [Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRA KU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), `https://app.penneo.com/`, `https://app.testhtm.com/settings/email-integration`, [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [Hosted Heritage Online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), Discovery Előnyei [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/),`https://itask.yipinapp.com/`

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="new-azure-ad-global-reader-role"></a>Új Azure AD globális olvasószerepkör

**Típus:** Új funkció  
**Szolgáltatás kategória:** Rbac  
**Termék képesség:** Hozzáférés-vezérlés

2019. szeptember 24-től kezdődően megkezdjük az Azure Active Directory (AD) globális olvasó nevű szerepkörének beindítását. Ez a bevezetés a termeléssel és a globális felhőbeli ügyfelekkel (GCC) kezdődik, amelyek októberben világszerte véget érnek.

A Globális olvasó szerepkör a globális rendszergazda írásvédett megfelelője. A szerepkörben lévő felhasználók olvashatják a beállításokat és a felügyeleti információkat a Microsoft 365-szolgáltatásokban, de nem tudnak felügyeleti műveleteket eljárni. Létrehoztuk a Globális olvasó szerepkört, hogy csökkentsük a globális rendszergazdák számát a szervezetben. Mivel a globális rendszergazdai fiókok hatékonyak és sebezhetőek a támadásokkal szemben, azt javasoljuk, hogy ötnél kevesebb globális rendszergazdával rendelkezik. Javasoljuk, hogy a Globális olvasó szerepkört használja a tervezéshez, auditokhoz vagy vizsgálatokhoz. Azt is javasoljuk, hogy a Globális olvasó szerepkört más korlátozott rendszergazdai szerepkörökkel, például az Exchange-rendszergazdával együtt is használja, hogy a globális rendszergazdai szerepkör megkövetelése nélkül végezhassa el a munkát.

A Globális olvasó szerepkör együttműködik az új Microsoft 365 Felügyeleti központtal, az Exchange Felügyeleti központtal, a Teams Felügyeleti központtal, a Biztonsági központtal, a Megfelelőségi központtal, az Azure AD Felügyeleti központtal és az Eszközkezelési felügyeleti központtal.

>[!NOTE]
> A nyilvános előzetes verzió kezdetén a Globális olvasó szerepkör nem működik együtt: SharePoint, Kiemelt hozzáférés-kezelés, Ügyfélszéf, érzékenységi címkék, Teams-életciklus, Teams Reporting & Híváselemzés, Teams IP-telefoneszköz-kezelés és Teams-alkalmazáskatalógus. 

További információt a [Rendszergazdai szerepkör-engedélyek az Azure Active Directoryban című témakörben talál.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Helyszíni jelentéskészítő kiszolgáló elérése a Power BI Mobile-alkalmazásból az Azure Active Directory alkalmazásproxy használatával

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Hozzáférés-vezérlés

A Power BI mobilalkalmazás és az Azure AD alkalmazásproxy közötti új integráció lehetővé teszi, hogy biztonságosan jelentkezzen be a Power BI mobilalkalmazásba, és tekintse meg a szervezet helyszíni Power BI jelentéskészítő kiszolgálón tárolt jelentéseit.

A Power BI Mobile alkalmazásról , például az alkalmazás letöltési helyéről a [Power BI webhelyen](https://powerbi.microsoft.com/mobile/)talál további információt. A Power BI mobilalkalmazás Azure AD alkalmazásproxyval történő beállításáról a [Power BI Mobile-hoz való távelérés engedélyezése az Azure AD alkalmazásproxyval](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)című témakörben talál további információt.

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Az AzureADPreview PowerShell-modul új verziója elérhető

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Taglista

Új parancsmagok kerültek az AzureADPreview modulba, amely segít az egyéni szerepkörök definiálásában és hozzárendelésében az Azure AD-ben, többek között:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Az Azure AD Connect új verziója

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Taglista

Kiadtuk az Azure AD Connect frissített verzióját az automatikus frissítéssel kapcsolatos ügyfelek számára. Ez az új verzió számos új funkciót, fejlesztést és hibajavítást tartalmaz. Az új verzióról az [Azure AD Connect: Version release history](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)című témakörben talál további információt.

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Az Azure Multi-Factor Authentication (MFA) Server 8.0.2-es verziója már elérhető

**Típus:** Rögzített  
**Szolgáltatás kategória:** Mfa  
**Termék képesség:** Identitásbiztonsági & védelem

Ha Ön már meglévő ügyfél, aki 2019. Ebben az új verzióban:

- Kijavítottuk azt a problémát, hogy amikor az Azure AD szinkronizálása letiltottról engedélyezve lévőre módosítja a felhasználót, a rendszer e-mailt küld a felhasználónak.

- Kijavítottunk egy problémát, hogy az ügyfelek sikeresen frissíthessenek, miközben továbbra is használhatják a Címkék funkciót.

- Hozzáadva a koszovói (+383) országkód.

- Egyszeri megkerülő naplózás hozzáadása a MultiFactorAuthSvc.log fájlhoz.

- A webszolgáltatás SDK-jának jobb teljesítménye.

- Javítottunk más kisebb hibákat.

2019. július 1-jétől a Microsoft leállította az MFA Server új telepítésekhez való felajánlását. A többtényezős hitelesítést igénylő új ügyfeleknek felhőalapú Azure többtényezős hitelesítést kell használniuk. További információ: [Felhőalapú Azure többtényezős hitelesítés isztikai üzembe helyezése.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

---

## <a name="august-2019"></a>2019. augusztus

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>A csoportok továbbfejlesztett keresése, szűrése és rendezése az Azure AD portálon érhető el (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

Örömmel jelentjük be, hogy az Azure AD-portálon elérhetők a továbbfejlesztett csoportokkal kapcsolatos élmények nyilvános előzetes verziói. Ezek a fejlesztések a következők biztosításával segítik a csoportok és a taglisták jobb kezelését:

- Speciális keresési lehetőségek, például karakterlánc-részhalmazok keresése a csoportlistákon.
- Speciális szűrési és rendezési beállítások a tag- és tulajdonoslistákon.
- Új keresési lehetőségek a tagok és a tulajdonosok listáihoz.
- Pontosabb csoportszámok nagy csoportok esetében.

További információ: [Csoportok kezelése az Azure Portalon.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Új egyéni szerepkörök érhetők el az alkalmazásregisztráció kezeléséhez (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Rbac  
**Termék képesség:** Hozzáférés-vezérlés

Egyéni szerepkörök (elérhető egy Azure AD P1 vagy P2 előfizetés) most már segít önnek a részletes hozzáférést, lehetővé teszi, hogy hozzon létre szerepkör-definíciók adott engedélyekkel, majd hozzárendeli ezeket a szerepköröket adott erőforrásokhoz. Jelenleg egyéni szerepköröket hozhat létre az alkalmazásregisztrációk kezelésére vonatkozó engedélyek használatával, majd a szerepkör hozzárendelésével egy adott alkalmazáshoz. Az egyéni szerepkörökről további információt az [Egyéni rendszergazdai szerepkörök az Azure Active Directoryban (előzetes verzió) című témakörben talál.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)

Ha további támogatott engedélyekre vagy erőforrásokra van szüksége, amelyekjelenleg nem láthatók, visszajelzést küldhet [az Azure visszajelzési webhelyére,](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) és mi hozzáadjuk kérését a frissítési ütemtervünkhöz.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Az új kiépítési naplók segítségével figyelheti és elháríthatja az alkalmazáskiépítési központi telepítést (Nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termék képesség:** Identitáséletciklus-kezelés

Új létesítési naplók állnak rendelkezésre, amelyek segítségével figyelheti és elháríthatja a felhasználói és csoportkiépítési központi telepítést. Ezek az új naplófájlok a következőkre vonatkozó információkat tartalmaznak:

- Milyen csoportok jöttek létre sikeresen a [ServiceNow szolgáltatásban?](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Milyen szerepköröket importált az [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Milyen alkalmazottak at nem importáltak a [Workday programból?](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

További információ: [Jelentések kiépítése az Azure Active Directory portálon (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Új biztonsági jelentések az összes Azure AD-rendszergazdához (általános elérhetőség)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

Alapértelmezés szerint az Összes Azure AD-rendszergazda hamarosan hozzáférhet a modern biztonsági jelentésekhez az Azure AD-n belül. Szeptember végéig használhatja a modern biztonsági jelentések tetején lévő transzparenst, hogy visszatérjen a régi jelentésekhez.

A modern biztonsági jelentések további lehetőségeket biztosítanak a régebbi verziókból, többek között a következőket:

- Speciális szűrés és rendezés
- Tömeges műveletek, például a felhasználói kockázat elvetése
- A feltört vagy biztonságos entitások megerősítése
- Kockázati állapot, amely: Veszélyben, Elutasítva, Helyreállítva és megerősítve
- Új, kockázattal kapcsolatos észlelések (elérhető az Azure AD Premium-előfizetők számára)

További információ: [Kockázatos felhasználók](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [Kockázatos bejelentkezések](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)és [kockázatészlelések.](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections)

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A felhasználó által hozzárendelt felügyelt identitás elérhető a virtuális gépekhez és a virtuálisgép-méretezési csoportokhoz (általános elérhetőség)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Felügyelt identitások az Azure-erőforrásokhoz  
**Termék képesség:** Fejlesztői élmény

A felhasználó által hozzárendelt felügyelt identitások mostantól általánosan elérhetők a virtuális gépek és a virtuálisgép-méretezési csoportok számára. Ennek részeként az Azure létrehozhat egy identitást az Azure AD-bérlőben, amelyet a használatban lévő előfizetés megbízható, és egy vagy több Azure-szolgáltatáspéldányhoz rendelhet hozzá. A felhasználó által hozzárendelt felügyelt identitásokról a [Mi az Azure-erőforrások felügyelt identitásai című témakörben](https://aka.ms/azuremanagedidentity)talál további információt.

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>A felhasználók visszaállíthatják jelszavukat mobilalkalmazás vagy hardvertoken (Általános elérhetőség) használatával

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Önkiszolgáló jelszó alaphelyzetbe állítása  
**Termék képesség:** Felhasználói hitelesítés

Azok a felhasználók, akik regisztráltak egy mobilalkalmazást a szervezetnél, most antól visszaállíthatják saját jelszavukat a Microsoft Authenticator alkalmazásértesítés jóváhagyásával, vagy a mobilalkalmazásukból vagy hardvertokenjükből származó kód megadásával.

További információ: [Hogyan működik: Az Azure AD önkiszolgáló jelszó-visszaállítás.](https://aka.ms/authappsspr) A felhasználói élményről további információt a [Saját munkahelyi vagy iskolai jelszó áttekintése című témakörben talál.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET figyelmen kívül hagyja a megosztott MSAL.NET gyorsítótárat a forgatókönyvek nevében

**Típus:** Rögzített  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

Az Azure AD hitelesítési könyvtárával (ADAL.NET) az 5.0.0-preview verzióval kezdve az alkalmazásfejlesztőknek [fiókonként egy gyorsítótárat kell szeriasziba foglalniuk a webes alkalmazások és webes API-k számára.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api) Ellenkező esetben a [folyamat nevében](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)történő használatával kapcsolatos egyes esetek, valamint a `UserAssertion`speciális használati esetek a használatával magasabb szintű jogosultságmegszerzését eredményezhetik. A biztonsági rés elkerülése érdekében ADAL.NET most figyelmen kívül hagyja a Microsoft MSAL.NET megosztott gyorsítótárának Microsoft hitelesítési könyvtárát a forgatókönyvek nevében.

A problémáról további információt az [Azure Active Directory hitelesítési könyvtár jogosultságok kiterjesztését lehetővé tévő biztonsági résében talál.](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Új összevont alkalmazások érhetők el az Azure AD Alkalmazásgalériában – 2019.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2019 augusztusában hozzáadtuk ezt a 26 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Polgári Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport's Inativ Portal (Európa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Akadémia Részvétel](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Prioritás Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), CP [QQSync a Cincom,](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial) [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [deliver.media&trade; Portál](https://portal.deliver.media), Frontline [Oktatás](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink,](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial) [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), Watch by [Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Hám](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigate Stratégiai Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Az AzureAD PowerShell és az AzureADPreview PowerShell-modulok új verziói elérhetők

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Taglista

Az AzureAD és az AzureAD Preview PowerShell-modulok új frissítései érhetők el:

- Egy `-Filter` új paraméter tadhozzá az `Get-AzureADDirectoryRole` AzureAD modul paraméter. Ez a paraméter segít a parancsmag által visszaadott címtárszerepkörök szűréséhez.
- Új parancsmagok kerültek az AzureADPreview modulba, amely segít az egyéni szerepkörök definiálásában és hozzárendelésében az Azure AD-ben, többek között:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>A dinamikus csoportszabály-szerkesztő felhasználói felületének fejlesztései az Azure Portalon

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

Az Azure Portalon elérhető dinamikus csoportszabály-szerkesztőnéhány fejlesztést hajtott végre a felhasználói felületen, hogy könnyebben beállíthasson egy új szabályt, vagy módosíthassa a meglévő szabályokat. Ez a kialakítás-fejlesztés lehetővé teszi, hogy egyetlen kifejezés helyett legfeljebb öt kifejezéssel hozzon létre szabályokat. Frissítettük az eszköz tulajdonságlistáját is az elavult eszköztulajdonságok eltávolításához.

További információt a [Dinamikus tagsági szabályok kezelése című](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)témakörben talál.

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Új Microsoft Graph alkalmazásengedély érhető el a hozzáférés-felülvizsgálatokhoz

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Access vélemények  
**Termék képesség:** Identitás-irányítás

Bevezettünk egy új Microsoft Graph `AccessReview.ReadWrite.Membership`alkalmazásengedélyt, amely lehetővé teszi az alkalmazások számára, hogy automatikusan létrehozzák és lekérjék a hozzáférés-felülvizsgálatokat a csoporttagságokhoz és az alkalmazás-hozzárendelésekhez. Ezt az engedélyt az ütemezett feladatok vagy az automatizálás részeként is használhatja, anélkül, hogy bejelentkezett felhasználói környezetet kellene igényelnie.

További információt a Példa az [Azure AD-hozzáférés-felülvizsgálatok létrehozása a Microsoft Graph alkalmazás engedélyekkel a PowerShell-blog használatával című témakörben talál.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Az Azure AD-tevékenységnaplók már elérhetők a kormányzati felhőpéldányokhoz az Azure Monitorban

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Örömmel jelentjük be, hogy az Azure AD-tevékenységnaplók már elérhetők a kormányzati felhőpéldányok hoz az Azure Monitorban. Most már elküldheti az Azure AD-naplókat a tárfiókba vagy egy eseményközpontba, hogy integrálja a SIEM-eszközökkel, például [a Sumologic,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic) [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)és [az ArcSight.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight) 

Az Azure Monitor beállításáról az [Azure AD-tevékenységnaplók az Azure Monitorban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)című témakörben talál további információt.

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Frissítse a felhasználókat az új, továbbfejlesztett biztonsági adatokra

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:**  Hitelesítések (bejelentkezések)   
**Termék képesség:** Felhasználói hitelesítés

2019. szeptember 25-én kikapcsoljuk a régi, nem továbbfejlesztett biztonsági adatokat a felhasználói biztonsági adatok regisztrálásához és kezeléséhez, és csak az új, [továbbfejlesztett verziót](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)kapcsoljuk be. Ez azt jelenti, hogy a felhasználók a továbbiakban nem használhatják a régi élményt.

A továbbfejlesztett biztonsági adatokról további információt a [rendszergazdai dokumentációban](https://aka.ms/securityinfodocs) és [a felhasználói dokumentációban](https://aka.ms/securityinfoguide)talál.

#### <a name="to-turn-on-this-new-experience-you-must"></a>Az új élmény bekapcsolásához a következőket kell tennie:

1. Jelentkezzen be az Azure Portalon globális rendszergazdaként vagy felhasználói rendszergazdaként.

2. Nyissa meg az **Azure Active Directory > Felhasználói beállítások > A hozzáférési panel előnézeti funkcióinak beállításainak kezelése című lapban.**

3. A **Felhasználók használhatja előnézeti funkciók regisztrálására és kezelésére biztonsági információk - bővített** terület, válassza a **Kiválasztott**lehetőséget, majd válassza ki a felhasználók egy csoportját, vagy válassza az **Összes,** hogy kapcsolja be ezt a funkciót az összes felhasználó a bérlőben.

4. A **A felhasználók az előzetes verziójú funkciókat használhatják a biztonsági **info**** terület regisztrálásához és kezeléséhez, és válassza a **Nincs**lehetőséget.

5. Mentse a beállításokat.

    A beállítások mentése után már nem férhet hozzá a régi biztonsági adatokhoz.

>[!Important]
>Ha 2019. szeptember 25-e előtt nem hajtja végre ezeket a lépéseket, az Azure Active Directory-bérlő automatikusan engedélyezve lesz a továbbfejlesztett élmény érdekében. Ha kérdése van, kérjük, lépjen kapcsolatba velünk registrationpreview@microsoft.coma .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>A POST bejelentkezéseket használó hitelesítési kérelmek szigorúbban lesznek érvényesítve

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Szabványok

2019. szeptember 2-tól kezdődően a POST metódust használó hitelesítési kérelmeket szigorúbban érvényesítik a HTTP-szabványokkal szemben. Pontosabban a szóközök és a dupla idézőjelek (") a továbbiakban nem lesznek eltávolítva a kéreleműrlap-értékekből. Ezek a módosítások várhatóan nem szakítják meg a meglévő ügyfeleket, és segítenek abban, hogy az Azure AD-nek küldött kérelmeket minden alkalommal megbízhatóan kezelje.

További információ: az [Azure AD-törési módosításokról szóló értesítések.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)

---

## <a name="july-2019"></a>2019. július

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Változásmegtervezése: Az alkalmazásproxy szolgáltatás frissítése csak a TLS 1.2-t támogatja

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Hozzáférés-vezérlés

Annak érdekében, hogy a legerősebb titkosítást, kezdjük korlátozni Application Proxy szolgáltatás hozzáférést csak TLS 1.2 protokollok. Ez a korlátozás kezdetben a TLS 1.2 protokollokat már használó ügyfelek számára kerül bevezetésre, így nem fogja látni a hatást. 2019. augusztus 31-én befejeződik a TLS 1.0 és a TLS 1.1 protokollteljes eprecikációja. Azok az ügyfelek, akik még mindig a TLS 1.0-t és a TLS 1.1-et használják, előzetes értesítést kapnak a változásra való felkészülésről.

Az alkalmazásproxy-szolgáltatással való kapcsolat fenntartása érdekében a módosítás során azt javasoljuk, hogy az ügyfél-kiszolgáló és a böngésző-kiszolgáló kombinációk frissítése a TLS 1.2 használatára frissüljön. Azt is javasoljuk, hogy győződjön meg arról, hogy az alkalmazottak által az alkalmazásproxy szolgáltatáson keresztül közzétett alkalmazások eléréséhez használt ügyfélrendszereket is tartalmazza.

További információ: [Add an on-premises application for remote access through Application Proxy in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Változásterv: Tervezési frissítések érkeznek az Alkalmazástárba

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

Új felhasználói felület változások jönnek a tervezés a **Hozzáadás a galéria** területén az **alkalmazás** hozzáadása panel. Ezek a módosítások megkönnyítik az automatikus kiépítést, az OpenID Connect, a security assertion markup language (SAML) és a Password single sign-on (SSO) alkalmazást.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Változásmegtervezése: Az MFA-kiszolgáló IP-címének eltávolítása az Office 365 IP-címéről

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Mfa  
**Termék képesség:** Identitásbiztonsági & védelem

Eltávolítjuk az MFA-kiszolgáló IP-címét az [Office 365 IP-cím- és URL-webszolgáltatásából.](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service) Ha jelenleg ezeka lapok a tűzfal beállításainak frissítéséhez, győződjön meg arról, hogy az **Azure többtényezős hitelesítési kiszolgáló tűzfalkövetelményei** című, [az Azure multi-factor authentication server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) cikkben dokumentált IP-címek listáját is tartalmazza.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>A csak alkalmazásjogkivonatokkal az ügyfélalkalmazásnak léteznie kell az erőforrás-bérlőben

**Típus:** Rögzített  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

2019. július 26-án megváltoztattuk, hogyan biztosítjuk a csak alkalmazásalapú tokeneket az [ügyfél hitelesítő adatainak megadásával.](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) Korábban az alkalmazások jogkivonatokat kaphatnak más alkalmazások hívásához, függetlenül attól, hogy az ügyfélalkalmazás a bérlőben volt-e. Frissítettük ezt a viselkedést, így az egybérlős erőforrásokat, más néven webes API-kat, csak az erőforrás-bérlőben létező ügyfélalkalmazások hívhatják meg.

Ha az alkalmazás nem található az erőforrás-bérlő, megjelenik egy `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` hibaüzenet, amely azt mondja: A probléma megoldásához létre kell hoznia az ügyfélalkalmazás szolgáltatás egyszerű a bérlőben, vagy a [rendszergazdai hozzájárulás végpont,](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) vagy a [PowerShell,](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)amely biztosítja, hogy a bérlő adott az alkalmazás engedélyt a bérlőn belül való működésre.

További információ: [Újdonságok a hitelesítéshez?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)

> [!NOTE]
> Az ügyfél és az API közötti meglévő hozzájárulás továbbra sem szükséges. Az alkalmazásoknak továbbra is saját engedélyezési ellenőrzéseket kell végezniük.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Új jelszó nélküli bejelentkezés az Azure AD-be a FIDO2 biztonsági kulcsokkal

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

Az Azure AD-ügyfelek mostantól beállíthatják a FIDO2 biztonsági kulcsok kezelését a szervezet felhasználói és csoportjai számára. A végfelhasználók is saját maguk regisztrálhatják biztonsági kulcsaikat, a kulcsokkal bejelentkezhetnek a Microsoft-fiókjukba a webwebhelyeken a FIDO-képes eszközökön, valamint bejelentkezhetnek az Azure AD-hez csatlakozó Windows 10-es eszközeikre.

További információ: [Jelszó nélküli bejelentkezés engedélyezése az Azure AD-hez (előzetes verzió)](/azure/active-directory/authentication/concept-authentication-passwordless) a rendszergazdai vonatkozású információkért, és a biztonsági adatok beállítása a végfelhasználóval kapcsolatos információk [biztonsági kulcs (előzetes használata) használatához című témakörben található.](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Új összevont alkalmazások érhetők el az Azure AD Alkalmazásgalériában – 2019.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2019 júliusában hozzáadtuk ezt a 18 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), Clever [Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex Compliance Office&trade;](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [ZsinegSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiók kiépítésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** A & jelentés ének nyomon követése

Most már automatizálhatja a felhasználói fiókok létrehozását, frissítését és törlését az újonnan integrált alkalmazásokhoz:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Összevont címtár](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Ha többet szeretne tudni arról, hogyan biztosíthatja hatékonyabban a szervezetet a felhasználói fiókok automatikus kiépítésével, olvassa el [a felhasználói kiépítés automatizálása saas-alkalmazásokba az Azure AD-vel című témakört.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Új Azure AD tartományi szolgáltatások szolgáltatáscímke a hálózati biztonsági csoporthoz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure AD tartományi szolgáltatások  
**Termék képesség:** Azure AD tartományi szolgáltatások

Ha elege van az IP-címek és tartományok hosszú listáinak kezeléséből, az Azure hálózati biztonsági csoportjában található új **AzureActiveDirectoryDomainServices** hálózati szolgáltatáscímke segítségével biztonságossá teheti az Azure AD Tartományi szolgáltatások virtuális hálózati alhálózatára irányuló bejövő forgalmat.

Az új szolgáltatáscímkéről további információt az [Azure AD tartományi szolgáltatások hálózati biztonsági csoportjai című témakörben talál.](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózás az Azure AD tartományi szolgáltatásokhoz (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure AD tartományi szolgáltatások  
**Termék képesség:** Azure AD tartományi szolgáltatások

Örömmel jelentjük be az Azure AD Domain Service Security Auditing nyilvános előzetes verzióban történő kiadását. A biztonsági naplózás az Azure AD tartományszolgáltatás portálhasználatával kritikus betekintést nyújt a hitelesítési szolgáltatásokba azáltal, hogy a biztonsági naplózási eseményeket a célzott erőforrásokba, például az Azure Storageba, az Azure Log Analytics-munkaterületekre és az Azure Event Hubra streameli.

További információ: [Biztonsági naplózás engedélyezése az Azure AD tartományi szolgáltatásokhoz (előzetes verzió)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)című témakörben talál.

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Új hitelesítési módszerek használata & elemzési adatok (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Önkiszolgáló jelszó alaphelyzetbe állítása  
**Termék képesség:** A & jelentés ének nyomon követése

Az új hitelesítési módszerek használatának & elemzési jelentések segítségével megismerheti, hogy az Olyan funkciók, mint az Azure multi-factor authentication és az önkiszolgáló jelszó-visszaállítás hogyan regisztrálva és használva vannak regisztrálva és használva a szervezetben, beleértve az egyes funkciók regisztrált felhasználóinak számát, az önkiszolgáló jelszó-visszaállítás milyen gyakran használják a jelszavak alaphelyzetbe állítását, és milyen módszerrel történik az alaphelyzetbe állítás.

További információ: [Hitelesítési módszerek használata & elemzések (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)című témakörben található.

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Új biztonsági jelentések érhetők el az összes Azure AD-rendszergazdászámára (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

Az Összes Azure AD-rendszergazda most kiválaszthatja a szalagcímet a meglévő biztonsági jelentések tetején, például a **kockázati jelentésként megjelölt felhasználók** számára, hogy elkezdhesse használni az új biztonsági élményt, ahogy az a **kockázatos felhasználók** és a **kockázatos bejelentkezések** jelentésekben látható. Idővel az összes biztonsági jelentés a régebbi verziókról az új verziókra kerül, az új jelentések pedig a következő további lehetőségeket biztosítják:

- Speciális szűrés és rendezés

- Tömeges műveletek, például a felhasználói kockázat elvetése

- A feltört vagy biztonságos entitások megerősítése

- Kockázati állapot, amely: Veszélyben, Elutasítva, Helyreállítva és megerősítve

További információ: [Kockázatos felhasználók jelentése](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) és [Kockázatos bejelentkezések jelentés.](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózás az Azure AD tartományi szolgáltatásokhoz (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure AD tartományi szolgáltatások  
**Termék képesség:** Azure AD tartományi szolgáltatások

Örömmel jelentjük be az Azure AD Domain Service Security Auditing nyilvános előzetes verzióban történő kiadását. A biztonsági naplózás az Azure AD tartományszolgáltatás portálhasználatával kritikus betekintést nyújt a hitelesítési szolgáltatásokba azáltal, hogy a biztonsági naplózási eseményeket a célzott erőforrásokba, például az Azure Storageba, az Azure Log Analytics-munkaterületekre és az Azure Event Hubra streameli.

További információ: [Biztonsági naplózás engedélyezése az Azure AD tartományi szolgáltatásokhoz (előzetes verzió)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)című témakörben talál.

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Új B2B közvetlen összevonás SAML/WS-Fed használatával (Nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C

A közvetlen összevonás megkönnyíti a munkát azokkal a partnerekkel, akiknek az IT által felügyelt identitáskezelési megoldása nem az Azure AD, az SAML vagy a WS-Fed szabványokat támogató identitásrendszerek használatával. Miután közvetlen összevonási kapcsolatot állított be egy partnerrel, az adott tartományból meghívott új vendégfelhasználók együttműködhetnek Önnel a meglévő szervezeti fiókhasználatával, így a vendégek felhasználói élménye zökkenőmentesebb lesz.

További információ: [Közvetlen összevonás az AD FS-sel és a vendégfelhasználók külső szolgáltatóival (előzetes verzió).](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiók kiépítésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** A & jelentés ének nyomon követése

Most már automatizálhatja a felhasználói fiókok létrehozását, frissítését és törlését az újonnan integrált alkalmazásokhoz:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Összevont címtár](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Ha többet szeretne tudni arról, hogyan biztosíthatja hatékonyabban a szervezetet a felhasználói fiókok automatikus kiépítésével, olvassa el [a felhasználói kiépítés automatizálása saas-alkalmazásokba az Azure AD-vel](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)című témakört.

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Ismétlődő csoportnevek új ellenőrzése az Azure AD portálon

**Típus:** Új funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

Most, amikor létrehoz vagy frissít egy csoportnevet az Azure AD portálról, ellenőrizzük, hogy egy meglévő csoportnevet duplikál-e az erőforrásban. Ha megállapítjuk, hogy a nevet már használja egy másik csoport, a rendszer megkéri, hogy módosítsa a nevét.

További információ: [Csoportok kezelése az Azure AD portálon.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Az Azure AD mostantól támogatja a statikus lekérdezési paramétereket a válasz (átirányítás) URI-kban

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

Az Azure AD-alkalmazások most már regisztrálhatnak és használhatnak válasz (átirányítási) URI-kat statikus lekérdezési paraméterekkel (például `https://contoso.com/oauth2?idp=microsoft`) az OAuth 2.0-s kérelmekhez. A statikus lekérdezési paraméter feltételegyező a válasz URI-k, mint bármely más része a válasz URI.The static query parameter is subject to string matching for reply URI, like any other part of the reply URI. Ha nincs olyan regisztrált karakterlánc, amely megfelel az URL-dekódolt redirect-uri-nak, a rendszer elutasítja a kérést. Ha a válasz URI található, a teljes karakterlánc átirányítja a felhasználót, beleértve a statikus lekérdezési paraméter.

A dinamikus válasz URI-k továbbra is tiltottak, mert biztonsági kockázatot jelentenek, és nem használhatók állapotadatok megőrzésére a hitelesítési kérelmekben. Erre a célra `state` használja a paramétert.

Jelenleg az Azure Portal alkalmazásregisztrációs képernyői továbbra is blokkolják a lekérdezési paramétereket. Az alkalmazásjegyzék manuálisszerkesztésével azonban lekérdezési paramétereket adhat hozzá és tesztelhet az alkalmazásban. További információ: [Újdonságok a hitelesítéshez?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Az Azure AD-hez való tevékenységnaplók (MS Graph API-k) már elérhetők a PowerShell-parancsmagokon keresztül

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Örömmel jelentjük be, hogy az Azure AD-tevékenységnaplók (naplózási és bejelentkezési jelentések) már elérhetők az Azure AD PowerShell-modulon keresztül. Korábban létrehozhatja saját parancsfájljait az MS Graph API-végpontok használatával, és most már kiterjesztettük ezt a képességet a PowerShell-parancsmagokra.

A parancsmagok használatáról további információt az [Azure AD PowerShell-parancsmagok jelentéskészítési.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Frissített szűrővezérlők az Azure AD naplózási és bejelentkezési naplóihoz

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Frissítettük a naplózási és bejelentkezési naplójelentéseket, így mostantól különböző szűrőket alkalmazhat anélkül, hogy oszlopokként kellene hozzáadnia őket a jelentésképernyőkön. Ezenkívül most már eldöntheti, hogy hány szűrőt szeretne megjeleníteni a képernyőn. Ezek a frissítések együttesen teszik leteheti őket a jelentések könnyebben olvashatóvá és az igényeinek megfelelőbb hatókörükkel.

A frissítésekről a [Naplók szűrése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) és [a Bejelentkezési tevékenységek szűrése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)című témakörben talál további információt.

---

## <a name="june-2019"></a>2019. június

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Új riskDetections API a Microsoft Graph számára (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

Örömmel jelentjük be, hogy a Microsoft Graph új riskDetections API-ja nyilvános előzetes verzióban érhető el. Ezzel az új API-val megtekintheti a szervezet identitásvédelemmel kapcsolatos felhasználói és bejelentkezési kockázatészlelései nek listáját. Az API-val hatékonyabban is lekérdezheti a kockázatészleléseket, beleértve az észlelési típusra, állapotra, szintre és egyebek rekedésének részleteit.

További információt a [Kockázatészlelési API referenciadokumentációjában](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)talál.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériájában – 2019.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2019 júniusában hozzáadtuk ezt a 22 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (啦ンン)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN-ügyfél](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Retail Mercishanding, Oracle Access Manager for Oracle Retail Mercishanding, Oracle Access Manager for Oracle Retail Mercishanding, Oracle Access Manager for Oracle Retail Mercishanding, Oracle Access Manager for Oracle Retail Mercishanding, Oracle Access Manager for Oracle Retail Mercishanding, Oracle Access Manager for Oracle Retail Retail Mercishanding, Oracle Access Manager for Oracle Retail Mercishanding, Oracle Access Manager for Oracle Retail Retail Mercishanding, Oracle Access Manager for Oracle Retail Retail Mercishanding, Oracle Access Manager for Oracle Retail Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiók kiépítésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** A & jelentés ének nyomon követése

Most már automatizálhatja a felhasználói fiókok létrehozását, frissítését és törlését az újonnan integrált alkalmazásokhoz:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Ha többet szeretne tudni arról, hogyan biztosíthatja hatékonyabban a szervezetet a felhasználói fiókok automatikus kiépítésével, olvassa el [a felhasználói kiépítés automatizálása saas-alkalmazásokba az Azure AD-vel című témakört.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Az Azure AD-kiépítési szolgáltatás valós idejű folyamatainak megtekintése

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termék képesség:** Identitáséletciklus-kezelés

Frissítettük az Azure AD kiépítési élményt, hogy egy új folyamatjelző sávot tartalmazzon, amely megmutatja, hogy milyen messze van a felhasználói kiépítési folyamatban. Ez a frissített felület az aktuális ciklus ban üzembe foglalt felhasználók számáról, valamint arról is tartalmaz információt, hogy eddig hány felhasználó lett kiépítve.

További információ: [A felhasználói kiépítés állapotának ellenőrzése.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>A vállalati márkajelzés mostantól megjelenik a kijelentkezési és hibaképernyőkön

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

Frissítettük az Azure AD-t, hogy a vállalati márkajelzés most megjelenjen a kijelentkezési és hibaképernyőkön, valamint a bejelentkezési oldalon. Nem kell semmit tennie, hogy bekapcsolja ezt a funkciót, az Azure AD egyszerűen csak az Azure Portal **vállalati márkajelzési** területén már beállított eszközöket használja.

A vállalati márkajelzés beállításáról a [Márkajelzés hozzáadása a szervezet Azure Active Directory-lapjaihoz című témakörben](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)talál további információt.

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Az Azure többtényezős hitelesítési (MFA) kiszolgálója már nem érhető el új telepítésekhez

**Típus:** Elavult  
**Szolgáltatás kategória:** Mfa  
**Termék képesség:** Identitásbiztonsági & védelem

2019. július 1-jéig a Microsoft a továbbiakban nem ajánlja fel az MFA Server alkalmazást az új telepítésekhez. Azoknak az új ügyfeleknek, akik többtényezős hitelesítést szeretnének megkövetelni a szervezetükben, most antól felhőalapú Azure többtényezős hitelesítést kell használniuk. Azok az ügyfelek, akik július 1-je előtt aktiválták az MFA-kiszolgálót, nem látják a változást. Továbbra is letöltheti a legújabb verziót, beszerezheti a jövőbeli frissítéseket, és aktiváló hitelesítő adatokat hozhat létre.

További információ: [Első lépések az Azure többtényezős hitelesítési kiszolgálóval.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy) A felhőalapú Azure többtényezős hitelesítésről a [Felhőalapú Azure többtényezős hitelesítés telepítésének megtervezése című témakörben](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)talál további információt.

---

## <a name="may-2019"></a>2019. május

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Szolgáltatásváltozás: Az alkalmazásproxy szolgáltatás csak TLS 1.2 protokolljainak jövőbeli támogatása

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Hozzáférés-vezérlés

Annak érdekében, hogy a legjobb titkosítást biztosítsuk ügyfeleink számára, csak a TLS 1.2 protokollokra korlátozzuk a hozzáférést az alkalmazásproxy szolgáltatáson. Ezt a módosítást fokozatosan vezetik be azoknak az ügyfeleknek, akik már csak a TLS 1.2 protokollokat használják, így nem kell semmilyen változást látnia.

2019. augusztus 31-én a TLS 1.0 és a TLS 1.1 evesztése történik, de további előzetes értesítést küldünk, így lesz ideje felkészülni erre a változásra. A módosításra való felkészüléshez győződjön meg arról, hogy az ügyfél-kiszolgáló és a böngésző-kiszolgáló kombinációk, beleértve azokat az ügyfeleket is, amelyeket a felhasználók az alkalmazásproxyn keresztül közzétett alkalmazások eléréséhez használnak, frissülnek a TLS 1.2 protokoll használatával az alkalmazásproxy szolgáltatással való kapcsolat fenntartásához. További információ: [Add an on-premises application for remote access through Application Proxy in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>A használat- és elemzési jelentés segítségével megtekintheti az alkalmazással kapcsolatos bejelentkezési adatokat

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** A & jelentés ének nyomon követése

Most már használhatja a használati és elemzési jelentés, található az Azure Portal **vállalati alkalmazások** területén, hogy egy alkalmazás-központú nézet a bejelentkezési adatok, beleértve a következő információkat:

- A szervezet legnépszerűbb használt alkalmazásai

- A legtöbb sikertelen bejelentkezéssel rendelkező alkalmazások

- Az egyes alkalmazások legfontosabb bejelentkezési hibái

A funkcióról további információt a [Használati és elemzési jelentés az Azure Active Directory portálon című témakörben](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) talál.

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>A felhőalapú alkalmazásokba való felhasználói kiépítés automatizálása az Azure AD használatával

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** A & jelentés ének nyomon követése

Kövesse az alábbi új oktatóanyagokat az Azure AD-kiépítési szolgáltatás használatához a felhasználói fiókok létrehozásának, törlésének és frissítésének automatizálásához a következő felhőalapú alkalmazásokhoz:

- [Üstökös](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [Dinamikus jel](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity (ÁllattartóBiztonság)](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Ezt az új [Dropbox-oktatóanyagot is követheti,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)amely a csoportobjektumok kiépítéséről nyújt tájékoztatást.

Ha többet szeretne tudni arról, hogyan biztosíthatja hatékonyabban a szervezetet a felhasználói fiókok automatikus kiépítése révén, olvassa el [a Felhasználói kiépítés automatizálása saas-alkalmazásokba az Azure AD-vel](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)című témakört.

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Az identitásbiztonságos pontszám már elérhető az Azure AD-ben (általános elérhetőség)

**Típus:** Új funkció  
**Szolgáltatás kategória:** N/a  
**Termék képesség:** Identitásbiztonsági & védelem

Most már figyelheti és javíthatja az identitás biztonsági állapotát az Azure AD biztonságos pontszám funkciójával. Az identitásbiztonságos pontszám szolgáltatás egyetlen irányítópultot használ, hogy segítsen:

- Objektíven mérje meg személyazonosságát biztonsági helyzet, egy pontszám alapján 1 és 223.

- Tervezze meg az identitásbiztonsági fejlesztéseket

- A biztonsági fejlesztések sikeresse

Az identitásbiztonsági pontszám funkcióról további információt [a Mi az identitás biztonságos pontszáma az Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Az új alkalmazásregisztrációk mostantól elérhetők (Általános elérhetőség)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Fejlesztői élmény

Az új [alkalmazásregisztrációk](https://aka.ms/appregistrations) most már általánosan elérhető. Ez az új felület tartalmazza az Azure Portalon és az alkalmazásregisztrációs portálon ismertetett összes kulcsfontosságú funkciót, és az:

- **Jobb alkalmazáskezelés.** Ahelyett, hogy az alkalmazásokat különböző portálokon látná, mostantól az összes alkalmazást egy helyen láthatja.

- **Egyszerűsített alkalmazásregisztráció.** A továbbfejlesztett navigációs élménytől kezdve a megújult engedélykiválasztási élményig mostantól egyszerűbben regisztrálhatja és kezelheti alkalmazásait.

- **Részletesebb információ.** További részleteket az alkalmazásról, beleértve a rövid útmutatókat és egyebeket.

További információ: [Microsoft identity platform](https://docs.microsoft.com/azure/active-directory/develop/) és az [alkalmazásregisztrációk mostantól általánosan elérhető!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blog bejelentését.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Új képességek érhetők el a Kockázatos felhasználók API-ban az identitásvédelemhez

**Típus:** Új funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

Örömmel jelentjük be, hogy most már használhatja a Kockázatos felhasználók API-t a felhasználók kockázati előzményeinek lekéréséhez, a kockázatos felhasználók elbocsátását és a felhasználók feltörtként való megerősítéséhez. Ez a módosítás segít a felhasználók kockázati állapotának hatékonyabb frissítésében és a kockázati előzmények megértésében.

További információt a [Kockázatos felhasználók API referenciadokumentációjában](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)talál.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériában – 2019.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2019 májusában hozzáadtuk ezt a 21 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage,](https://toutapp.com/login) [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), Quantum [Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Továbbfejlesztett csoportok létrehozása és felügyeleti élménye az Azure AD portálon

**Típus:** Új funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

Az Azure AD-portálon javítottuk a csoportokkal kapcsolatos élményeket. Ezek a fejlesztések lehetővé teszik a rendszergazdák számára, hogy jobban kezeljék a csoportlistákat, a taglistákat, és további létrehozási lehetőségeket biztosítsanak.

A fejlesztések a következők:

- Alapvető szűrés tagsági és csoporttípus szerint.

- Új oszlopok hozzáadása, például Forrás és E-mail cím.

- A csoportok, tagok és tulajdonosok listájának többszörös kijelölése a egyszerű törlés érdekében.

- Képes e-mail cím kiválasztására és tulajdonosok hozzáadására a csoport létrehozása során.

További információ: [Hozzon létre egy alapszintű csoportot, és adjon hozzá tagokat az Azure Active Directory használatával.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Az Office 365-csoportok elnevezési házirendjének konfigurálása az Azure AD portálon (általános elérhetőség)

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

A rendszergazdák mostantól konfigurálhatnak egy elnevezési szabályzatot az Office 365-csoportokhoz az Azure AD portál használatával. Ez a módosítás segít a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok egységes elnevezési konvencióinak kényszerítésében.

Az Office 365-csoportok elnevezési szabályzatát kétféleképpen állíthatja be:

- Adja meg azokat az előtagokat vagy utótagokat, amelyek automatikusan hozzáadódnak a csoportnévhez.

- Töltsön fel egy személyre szabott letiltott szavakat a szervezet számára, amelyek nem engedélyezettek a csoportnevekben (például "CEO, Payroll, HR").

További információt az [Office 365-csoportok elnevezési szabályzatának érvényesítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)című témakörben talál.

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>A Microsoft Graph API-végpontjai már elérhetők az Azure AD-tevékenységnaplókhoz (általános elérhetőség)

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Örömmel jelentjük be a Microsoft Graph API-végpontok támogatásának általános elérhetőségét az Azure AD tevékenységnaplóihoz. Ezzel a kiadással most már használhatja az 1.0-s verziót az Azure AD naplózási naplók, valamint a bejelentkezési naplók API-k.

További információ: [Azure AD naplózási napló API- áttekintés.](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>A rendszergazdák mostantól használhatják a feltételes hozzáférést a kombinált regisztrációs folyamathoz (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Identitásbiztonsági & védelem  

A rendszergazdák mostantól feltételes hozzáférési házirendeket hozhatnak létre a kombinált regisztrációs lap számára. Ez magában foglalja a regisztrációt lehetővé tévő házirendek alkalmazását, ha:

- A felhasználók megbízható hálózaton vannak.

- A felhasználók alacsony bejelentkezési kockázatot jelentenek.

- A felhasználók felügyelt eszközön vannak.

- A felhasználók elfogadják a szervezet használati feltételeit (TOU).

A feltételes hozzáférésről és a jelszó alaphelyzetbe állításáról további információt az [Azure AD-hez kombinált Többfa-hitelesítés és jelszó-visszaállítási regisztrációs élmény blogbejegyzése](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)talál. A kombinált regisztrációs folyamat feltételes hozzáférési házirendjeiről a [feltételes hozzáférésű házirendek kombinált regisztrációhoz című témakörben](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)talál további információt. Az Azure AD használati feltételeiről az [Azure Active Directory használati feltételei szolgáltatásban talál](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)további információt.

---

## <a name="april-2019"></a>2019. április

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Az Azure AD-től új fenyegetésfelderítési észlelése már elérhető az Azure AD Identity Protection részeként

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure AD identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

Az Azure AD fenyegetésfelderítési észlelése már elérhető a frissített Azure AD Identity Protection szolgáltatás részeként. Ez az új funkció segít egy adott felhasználó vagy tevékenység szokatlan felhasználói tevékenységének jelzését, amely összhangban van a Microsoft belső és külső fenyegetésfelderítési forrásain alapuló ismert támadási mintákkal.

Az Azure AD Identity Protection frissített verziójáról további információt az [Azure AD Identity Protection négy fő fejlesztése a nyilvános előzetes verzióban](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) található blogban és a [Mi az Azure Active Directory-identitásvédelem (frissítve)című](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) témakörben talál? cikket. Az Azure AD-fenyegetésfelderítési észlelésről az [Azure Active Directory-identitásvédelem kockázatészlelési](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) cikkében olvashat bővebben.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Az Azure AD-jogosultságok kezelése már elérhető (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Identitás-irányítás  
**Termék képesség:** Identitás-irányítás

Az Azure AD jogosultságkezelés, most nyilvános előzetes verzióban, segít az ügyfeleknek a hozzáférési csomagok kezelésének delegálásában, amely meghatározza, hogy az alkalmazottak és az üzleti partnerek hogyan kérhetnek hozzáférést, kinek kell jóváhagynia, és mennyi ideig férhetnek hozzá. Az Access-csomagok kezelhetik az Azure AD és az Office 365-csoportok tagságát, a vállalati alkalmazásokszerepkör-hozzárendeléseit és a SharePoint Online-webhelyek szerepkör-hozzárendeléseit. A jogosultságkezelésről az [Azure AD-jogosultságok kezelésének áttekintésén olvashat bővebben.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) Ha többet szeretne megtudni az Azure AD-identitáskezelési funkciók széles körű, beleértve a kiemelt identitáskezelés, a hozzáférési felülvizsgálatok és a használati feltételek, [olvassa el az Azure AD-identitáskezelő?](../governance/identity-governance-overview.md)

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Az Office 365-csoportok elnevezési házirendjének konfigurálása az Azure AD portálon (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

A rendszergazdák mostantól konfigurálhatnak egy elnevezési szabályzatot az Office 365-csoportokhoz az Azure AD portál használatával. Ez a módosítás segít a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok egységes elnevezési konvencióinak kényszerítésében.

Az Office 365-csoportok elnevezési szabályzatát kétféleképpen állíthatja be:

- Adja meg azokat az előtagokat vagy utótagokat, amelyek automatikusan hozzáadódnak a csoportnévhez.

- Töltsön fel egy személyre szabott letiltott szavakat a szervezet számára, amelyek nem engedélyezettek a csoportnevekben (például "CEO, Payroll, HR").

További információt az [Office 365-csoportok elnevezési szabályzatának érvényesítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)című témakörben talál.

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Az Azure AD-tevékenységnaplók már elérhetők az Azure Monitorban (általános elérhetőség)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Az Azure AD-tevékenységnaplókkal kapcsolatos vizualizációkkal kapcsolatos visszajelzések kezeléséhez egy új Insights-funkciót vezetünk be a Log Analytics szolgáltatásban. Ez a funkció segítségével betekintést nyerhet az Azure AD-erőforrásokba a munkafüzetek nevű interaktív sablonjaink használatával. Ezek az előre elkészített munkafüzetek az alkalmazások vagy felhasználók adatait tartalmazzák, és a következőket tartalmazzák:

- **Bejelentkezések.** Részleteket tartalmaz az alkalmazások és a felhasználók számára, beleértve a bejelentkezéshelyét, a használatban lévő operációs rendszert vagy böngészőügyfelet és -verziót, valamint a sikeres vagy sikertelen bejelentkezések számát.

- **Örökölt hitelesítés és feltételes hozzáférés.** Az örökölt hitelesítést használó alkalmazások és felhasználók adatait tartalmazza, beleértve a feltételes hozzáférési házirendek által kiváltott többtényezős hitelesítéshasználatát, a feltételes hozzáférési házirendeket használó alkalmazásokat és így tovább.

- **Bejelentkezési hiba elemzés.** Segít meghatározni, hogy a bejelentkezési hibák felhasználói művelet, házirend-problémák vagy az infrastruktúra miatt fordulnak-e elő.

- **Egyéni jelentések.** Létrehozhat új munkafüzeteket, vagy szerkesztheti a meglévő munkafüzeteket, hogy testre szabhassa a szervezet Insights funkcióját.

További információ: [Az Azure Monitor munkafüzeteinek használata az Azure Active Directory-jelentésekhez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)című témakörben talál.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériájában – 2019.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2019 áprilisában a 21 federation támogatással rendelkező új alkalmazást hozzáadtuk az alkalmazásgalériához:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (Szerepalapú SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Új hozzáférési felülvizsgálatok gyakorisági beállítás és több szerepkör kiválasztása

**Típus:** Új funkció  
**Szolgáltatás kategória:** Access vélemények  
**Termék képesség:** Identitás-irányítás

Az Azure AD-hozzáférés-felülvizsgálatok új frissítései lehetővé teszik a következőket:

- Módosítsa a hozzáférési felülvizsgálatok gyakoriságát **félévente**, a korábban létező heti, havi, negyedéves és éves lehetőségek mellett.

- Egyetlen hozzáférés-felülvizsgálat létrehozásakor válasszon ki több Azure AD- és Azure-erőforrás-szerepkört. Ebben az esetben minden szerepkör azonos beállításokkal van beállítva, és az összes ellenőrző egyidejűleg értesítést kap.

A hozzáférés-felülvizsgálat létrehozásáról a [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának létrehozása az Azure AD-hozzáférés-felülvizsgálatokban című témakörben](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)talál további információt.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Az Azure AD Connect e-mail riasztási rendszere(i) átalakulnak, és új e-mail feladói információkat küldenek egyes ügyfeleknek

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** AD-szinkronizálás  
**Termék képesség:** Platform

Az Azure AD Connect az e-mail riasztási rendszer(ek) átváltásának folyamatában van, és egyes ügyfelek számára új e-mail-feladót jelenít meg. Ennek megoldásához hozzá `azure-noreply@microsoft.com` kell adnia a szervezet engedélyezési listájához, különben nem kaphat továbbra is fontos riasztásokat az Office 365-ből, az Azure-ból vagy a szinkronizálási szolgáltatásokból.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Az UPN utótag módosításai sikeresek az Összevont tartományok között az Azure AD Connectben

**Típus:** Rögzített  
**Szolgáltatás kategória:** AD-szinkronizálás  
**Termék képesség:** Platform

Most már sikeresen módosíthatja a felhasználó UPN-utótagot egy összevont tartományról egy másik összevont tartományra az Azure AD Connectben. Ez a javítás azt jelenti, hogy a szinkronizálási ciklus során már nem kell tapasztalnia a FederatedDomainChangeError hibaüzenetet, vagy értesítést kap akövetkezőben: "Nem lehet frissíteni ezt az objektumot az Azure Active Directoryban, mert a [FederatedUser.UserPrincipalName] attribútum érvénytelen. Frissítse az értéket a helyi címtárszolgáltatásokban".

További információt a Szinkronizálás [során fellépő hibák elhárítása](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)című témakörben talál.

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Fokozott biztonság az alkalmazásvédelemen alapuló feltételes hozzáférés-szabályzat használatával az Azure AD-ben (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Identitásbiztonsági & védelem

Az alkalmazásvédelemen alapuló feltételes hozzáférés már elérhető az **alkalmazásvédelmi szabályzat megkövetelése** használatával. Ez az új házirend a következők megelőzésével segít növelni a szervezet biztonságát:

- A felhasználók Microsoft Intune-licenc nélkül férnek hozzá az alkalmazásokhoz.

- A felhasználók nem tudnak beszerezni egy Microsoft Intune-alkalmazásvédelmi szabályzatot.

- A felhasználók a Microsoft Intune alkalmazásvédelmi szabályzata nélkül férnek hozzá az alkalmazásokhoz.

További információ: [Az alkalmazásvédelmi szabályzat megkövetelése a felhőalapú alkalmazások hozzáféréséhez feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)című témakörben talál.

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Új támogatás az Azure AD egyszeri bejelentkezéshez és feltételes hozzáféréshez a Microsoft Edge-ben (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Identitásbiztonsági & védelem

Bővítettük a Microsoft Edge Azure AD-támogatását, beleértve az Azure AD egyszeri bejelentkezéshez és a feltételes hozzáféréshez nyújtott új támogatást. Ha korábban már használta a Microsoft Intune felügyelt böngészőjét, most már használhatja a Microsoft Edge-et.

Az eszközök és alkalmazások feltételes hozzáféréssel történő beállításáról és kezeléséről a [Felügyelt eszközök megkövetelése a felhőalapú alkalmazáshoz feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) és [a jóváhagyott ügyfélalkalmazások megkövetelése a feltételes hozzáféréssel rendelkező felhőalapú alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)című témakörben talál további információt. Ha többet szeretne tudni arról, hogy miként kezelheti a Microsoft Edge segítségével a Microsoft Intune-házirendekkel való hozzáférést, olvassa el az [Internet-hozzáférés kezelése Microsoft Intune-alapú, házirenddel védett böngészővel című témakört.](https://docs.microsoft.com/intune/app-configuration-managed-browser)

---

## <a name="march-2019"></a>2019. március

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Az Azure Active Directory B2C-ben elérhető az Identitáskezelési keretrendszer és az egyéni házirendek támogatása

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C

Most már létrehozhat egyéni szabályzatokat az Azure AD B2C-ben, beleértve a következő feladatokat, amelyek et nagy léptékben és az Azure SLA keretében támogatnak:

- Egyéni hitelesítési felhasználói utak létrehozása és feltöltése egyéni házirendek használatával.

- Írja le a felhasználói utakat lépésről lépésre a jogcímszolgáltatók közötti adatcsereként.

- Feltételes elágazás definiálása a felhasználói utakban.

- A jogcímek átalakítása és leképezése valós idejű döntésekhez és kommunikációhoz.

- Használja a REST API-kompatibilis szolgáltatásokat az egyéni hitelesítési felhasználói utakban. Például az e-mail szolgáltatók, CRM-ek és saját engedélyezési rendszerek.

- Az OpenIDConnect protokollnak megfelelő identitásszolgáltatókkal. Például a több-bérlős Azure AD, közösségi fiók szolgáltatók, vagy kétfaktoros ellenőrző szolgáltatók.

Az egyéni szabályzatok létrehozásáról további információt az [Azure Active Directory B2C egyéni szabályzataihoz szükséges Fejlesztői megjegyzések](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) című témakörben talál, és olvassa el Alex Simon [blogbejegyzését, beleértve az esettanulmányokat is.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériájában – 2019.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2019 márciusában hozzáadtuk ezt a 14 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[ISEC7 Mobil tőzsdeküldött](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Magyarázat-alapú auditáló rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Új Zscaler és Atlassian kiépítési összekötők az Azure AD-galériában – 2019 márciusa

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termékképesség:** harmadik fél integrációja

A következő alkalmazások felhasználói fiókjainak létrehozásának, frissítésének és törlésének automatizálása:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler 2](https://aka.ms/ZscalerTwoProvisioning), [Zscaler Three](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Ha többet szeretne tudni arról, hogyan biztosíthatja hatékonyabban a szervezetet a felhasználói fiókok automatikus kiépítése révén, olvassa el [a Felhasználói kiépítés automatizálása saas-alkalmazásokba az Azure AD-vel](https://aka.ms/ProvisioningDocumentation)című témakört.

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>A törölt Office 365-csoportok visszaállítása és kezelése az Azure AD portálon

**Típus:** Új funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

Most már megtekintheti és kezelheti a törölt Office 365-csoportokat az Azure AD portálról. Ez a módosítás segít annak megtekintésében, hogy mely csoportok állíthatók vissza, valamint lehetővé teszi, hogy véglegesen törölje azokat a csoportokat, amelyekre a szervezetnek nincs szüksége.

További információt a Lejárt vagy törölt csoportok visszaállítása című [témakörben talál.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Egyszeri bejelentkezés már elérhető az Azure AD SAML által védett helyszíni alkalmazások alkalmazásproxyn keresztül (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Hozzáférés-vezérlés

Most már egyetlen bejelentkezési (SSO) élményt biztosíthat a helyszíni, SAML-hitelesítésű alkalmazásokhoz, valamint távoli hozzáférést ezekhez az alkalmazásokhoz az alkalmazásproxyn keresztül. Az SAML Egyszeri bejelentkezés helyszíni alkalmazásokkal való beállításáról további információt az [AMA-bejelentkezés a helyszíni alkalmazásokalkalmazás-proxyval (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)című témakörben talál.

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>A kérelemhurkokban lévő ügyfélalkalmazások megszakadnak a megbízhatóság és a felhasználói élmény javítása érdekében

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

Az ügyfélalkalmazások helytelenül több száz azonos bejelentkezési kérelmet adhatnak ki rövid idő alatt. Ezek a kérések, függetlenül attól, hogy sikeresek-e vagy sem, mind hozzájárulnak a gyenge felhasználói élményhez és az IDP fokozott munkaterheléséhez, növelve a késést az összes felhasználó számára, és csökkentve az IDP rendelkezésre állását.

A frissítés `invalid_grant` hibaüzenetet `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` küld: olyan ügyfélalkalmazásoknak, amelyek rövid időn belül többször is ismétlődő kérelmeket adnak ki, a normál működés hatókörén kívül. A problémával szembesülő ügyfélalkalmazásoknak interaktív kérdést kell megjeleníteniük, amely nek újra be kell jelentkeznie. A módosításról és arról, hogy miként javíthatja ki az alkalmazást, ha ez a hiba lép fel, olvassa el [a Hitelesítés újdonságait.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)

---

### <a name="new-audit-logs-user-experience-now-available"></a>Az új naplózási naplók felhasználói élménye már elérhető

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Létrehoztunk egy új Azure AD naplózási naplók lapot, hogy **javítsuk** az olvashatóságot és az adatok keresésének módját. Az új **naplózási naplók** lap megtekintéséhez válassza **a naplók naplózása** az Azure AD **Tevékenység** szakaszában.

![Új naplónaplók lap, mintaadatokkal](media/whats-new/audit-logs-page.png)

Az új **naplózási naplók** lapról további információt a [Naplózási tevékenységjelentések az Azure Active Directory portálon című témakörben talál.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Új figyelmeztetések és útmutatásak a helytelenül konfigurált feltételes hozzáférési házirendek véletlen rendszergazdai zárolásának megakadályozásához

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Identitásbiztonsági & védelem

Annak érdekében, hogy a rendszergazdák véletlenül zárják ki magukat a saját bérlők a helytelenül konfigurált feltételes hozzáférési szabályzatok, hoztunk létre új figyelmeztetések et és frissített útmutatást az Azure Portalon. Az új útmutatásról a [Mik azok a szolgáltatásfüggőségek](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)az Azure Active Directory feltételes hozzáférésében című témakörben.

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Továbbfejlesztett végfelhasználói használati feltételek mobileszközökön

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Kormányzás

Frissítettük a meglévő használati feltételeinket, hogy javítsuk a mobileszközön történő használati feltételek áttekintését és beleegyezését. Most már nagyíthat és kicsinyíthet, visszatérhet, letöltheti az információkat, és kiválaszthatja a hivatkozásokat. A frissített használati feltételekről az [Azure Active Directory használati feltételeinek szolgáltatásában talál](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)további információt.

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Új Azure AD-tevékenységnaplók letöltési élmény érhető el

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Most már nagy mennyiségű tevékenységnaplók at közvetlenül az Azure Portalon. Ez a frissítés lehetővé teszi:

- Töltsön le akár 250 000 sort.

- Értesítést kap a letöltés befejezése után.

- A fájlnév testreszabása.

- Határozza meg a kimeneti formátumot JSON vagy CSV.

A funkcióról további információt a [Rövid útmutató: Naplózási jelentés letöltése az Azure Portalon című](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report) témakörben talál.

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Változás betörése: Az Exchange ActiveSync (EAS) állapotkiértékelésének frissítései

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Hozzáférés-vezérlés

Folyamatban van annak frissítése, hogy az Exchange ActiveSync (EAS) hogyan értékeli ki a következő feltételeket:

- A felhasználó helye az ország, a régió vagy az IP-cím alapján

- Bejelentkezési kockázat

- Eszközplatform

Ha korábban már használta ezeket a feltételeket a feltételes hozzáférési szabályzatokban, vegye figyelembe, hogy a feltétel viselkedése változhat. Ha például korábban egy házirendben használta a felhasználói hely feltételét, előfordulhat, hogy a rendszer a felhasználó helye alapján kihagyja a házirendet.

---

## <a name="february-2019"></a>2019. február

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurálható Azure AD SAML token titkosítás (nyilvános előzetes verzió) 

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

Most már konfigurálhatja bármelyik támogatott SAML alkalmazást a titkosított SAML-tokenek fogadására. Ha konfigurálva van, és egy alkalmazással együtt használja, az Azure AD titkosítja a kibocsátott SAML-állításokat egy nyilvános kulcs az Azure AD-ben tárolt tanúsítvány használatával.

Az SAML tokentitkosítás konfigurálásáról az [Azure AD SAML tokentitkosítás konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)című témakörben talál további információt.

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Hozzáférés-felülvizsgálat létrehozása csoportokhoz vagy alkalmazásokhoz az Azure AD Access-vélemények használatával

**Típus:** Új funkció  
**Szolgáltatás kategória:** Access vélemények  
**Termék képesség:** Kormányzás

Mostantól több csoportot vagy alkalmazást is felvehet egyetlen Azure AD-hozzáférés-felülvizsgálatba a csoporttagsághoz vagy az alkalmazás-hozzárendeléshez. A több csoporttal vagy alkalmazással rendelkező hozzáférési ellenőrzések ugyanazokat a beállításokat használják, és az összes belefoglalt ellenőrző egyidejűleg értesítést kap.

Ha többet szeretne tudni arról, hogyan hozhat létre hozzáférési felülvizsgálatot az Azure AD Access-értékelések használatával, olvassa [el a Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának létrehozása az Azure AD Access-értékelésekben című témakört.](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériájában - 2019 február

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2019 februárjában hozzáadtuk ezt a 27 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack,](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial) [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Engedély Kattintson, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [Szeizmikus,](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial) [Share A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), Tudás Bárhol [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope Adatok](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portál](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud a Genesy](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Productivity Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="enhanced-combined-mfasspr-registration"></a>Továbbfejlesztett kombinált MFA/SSPR regisztráció

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Önkiszolgáló jelszó alaphelyzetbe állítása  
**Termék képesség:** Felhasználói hitelesítés

Az ügyfelek visszajelzéseire adott válaszként bővítettük az MFA/SSPR regisztrációs előzetes verzió együttes élményét, segítve a felhasználókat abban, hogy gyorsabban regisztrálhassák biztonsági adataikat mind az MFA, mind az SSPR számára. 

**Ha ma be szeretné kapcsolni a felhasználók továbbfejlesztett élményét, kövesse az alábbi lépéseket:**

1. Globális rendszergazdaként vagy felhasználói rendszergazdaként jelentkezzen be az Azure Portalra, és nyissa meg az **Azure Active Directory > Felhasználói beállítások > A hozzáférési panel előzetes verziójának beállításainak kezelése .** 

2. A **Felhasználók, akik használhatják az előnézeti funkciók at regisztráció és kezelése biztonsági információk – frissítési** lehetőség, válassza a szolgáltatások bekapcsolását a **kiválasztott felhasználói csoport** vagy az összes **felhasználó.**

A következő hetekben eltávolítjuk a régi kombinált MFA/SSPR regisztrációs előzetes verzió t a bérlők, akik még nem rendelkeznek vele be kapcsolva.

**Ha meg szeretné tudni, hogy a rendszer eltávolítja-e a vezérlőt a bérlőhöz, kövesse az alábbi lépéseket:**

1. Globális rendszergazdaként vagy felhasználói rendszergazdaként jelentkezzen be az Azure Portalra, és nyissa meg az **Azure Active Directory > Felhasználói beállítások > A hozzáférési panel előzetes verziójának beállításainak kezelése .**  

2. Ha a **Felhasználók, akik használhatják az előnézeti funkciók at regisztráció és kezelése biztonsági adatok** beállítás van meg: **Nincs,** a beállítás törlődik a bérlő.

Függetlenül attól, hogy korábban bekapcsolta-e a régi kombinált MFA/SSPR regisztrációs előzetes verziót a felhasználók számára, vagy sem, a régi élmény egy későbbi időpontban ki lesz kapcsolva. Emiatt azt javasoljuk, hogy lépjen a lehető leghamarabb az új, továbbfejlesztett élményre.

A továbbfejlesztett regisztrációs élményről további információt az [Azure AD kombinált Többfa-hitelesítés és jelszó-visszaállítási regisztrációs felület ének javítása című témakörben talál.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Frissített házirend-kezelési élmény a felhasználói folyamatokhoz

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C

Frissítettük a felhasználói folyamatok (korábbi nevén beépített szabályzatok) házirend-létrehozási és -kezelési folyamatát. Ez az új felület most antól az összes Azure AD-bérlő alapértelmezett szolgáltatása.

További visszajelzéseket és javaslatokat adhat a portál képernyő tetején található **Visszajelzés küldése** területen található mosoly- vagy homlokráncolás ikonok használatával.

Az új házirend-kezelési élményről az [Azure AD B2C most már JavaScript-testreszabással és még sok más új funkcióblogkal rendelkezik.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Az Azure AD B2C által biztosított adott oldalelem-verziók kiválasztása

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C

Most már kiválaszthatja az Azure AD B2C által biztosított oldalelemek egy adott verzióját. Egy adott verzió kiválasztásával tesztelheti a frissítéseket, mielőtt azok megjelennének az oldalon, és kiszámítható viselkedést érhet el. Ezenkívül mostantól engedélyezheti bizonyos lapverziók kényszerítése a JavaScript-testreszabások engedélyezéséhez. A funkció bekapcsolásához nyissa meg a felhasználói folyamatok **Tulajdonságok** lapját.

Az oldalelemek adott verzióinak kiválasztásáról az [Azure AD B2C most már rendelkezik JavaScript-testreszabással és még sok más új funkcióblogkal.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurálható végfelhasználói jelszókövetelmények a B2C (GA) rendszerhez

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C

Most már beállíthatja a szervezet jelszó összetettségét a végfelhasználók számára, ahelyett, hogy a natív Azure AD jelszóházirendet kellene használnia. A **felhasználói** folyamatok Tulajdonságok paneljén (korábbi nevén a beépített szabályzatok) kiválaszthatja **az egyszerű** vagy az **erős**jelszó összetettségét, vagy létrehozhat **egyéni** követelmények készletét.

A jelszó összetettségi követelményének konfigurálásáról az [Azure Active Directory B2C-ben a jelszavak összetettségi követelményeinek konfigurálása című témakörben talál](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)további információt.

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Új alapértelmezett sablonok az egyéni márkajelzéssel végzett hitelesítési élményekhez

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C

A felhasználói folyamatok **lapelrendezések** paneljén található új alapértelmezett sablonjaink (korábbi nevén beépített házirendek) segítségével egyéni márkajelzéssel végzett hitelesítési élményt hozhat létre a felhasználók számára.

A sablonok használatáról további információt az [Azure AD B2C now is JavaScript-testreszabással és még sok más új funkcióval talál.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

## <a name="january-2019"></a>2019. január

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-együttműködés egyszeri jelkód-hitelesítéssel (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C

Bevezettük az egyszeri jelkód-hitelesítést (OTP) a B2B vendégfelhasználók számára, akik nem hitelesíthetők más eszközökkel, például az Azure AD-vel, egy Microsoft-fiókkal (MSA) vagy a Google-összevonással. Ez az új hitelesítési módszer azt jelenti, hogy a vendégfelhasználóknak nem kell új Microsoft-fiókot létrehozniuk. Ehelyett egy meghívó beváltása vagy egy megosztott erőforrás elérése közben a vendégfelhasználó kérheti egy ideiglenes kód elküldését egy e-mail címre. Ezzel az ideiglenes kóddal a vendégfelhasználó továbbra is bejelentkezhet.

További információ: [E-mail egyszeri jelkód-hitelesítés (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) és a blog, [az Azure AD zökkenőmentessé teszi a megosztást és az együttműködést bármely felhasználó számára bármilyen fiókkal.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Új Azure AD alkalmazásproxy-cookie-beállítások

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Hozzáférés-vezérlés

Három új cookie-beállítást vezettünk be, amelyek az alkalmazásproxyn keresztül közzétett alkalmazásokhoz érhetők el:

- **Csak HTTP-s cookie használata.** Beállítja a **HTTPOnly** jelzőt az alkalmazásproxy-hozzáférésés a munkamenet-cookie-k között. A beállítás bekapcsolása további biztonsági előnyökkel jár, például segít megelőzni a cookie-k ügyféloldali parancsfájlok on keresztüli másolását vagy módosítását. Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen)** lehetőséget a további előnyök érdekében.

- **Használjon biztonságos cookie-t.** Beállítja a **Biztonságos** jelzőt az alkalmazásproxy-hozzáférésés a munkamenet-cookie-k között. A beállítás bekapcsolása további biztonsági előnyökkel jár, mivel gondoskodik arról, hogy a cookie-k csak A TLS biztonságos csatornákon, például a HTTPS-csatornákon keresztül továbbítódnak. Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen)** lehetőséget a további előnyök érdekében.

- **Használjon állandó cookie-t.** Megakadályozza a cookie-k elérését, amikor a böngésző be van zárva. Ezek a cookie-k a hozzáférési jogkivonat élettartamáig tartanak. A cookie-k azonban alaphelyzetbe állnak, ha a lejárati idő teljesül, vagy ha a felhasználó manuálisan törli a cookie-t. Javasoljuk, hogy tartsa meg az alapértelmezett **Nem**beállítást, és csak azon régebbi alkalmazások beállítását kapcsolja be, amelyek nem osztják meg a cookie-kat a folyamatok között.

Az új cookie-król további információt a [Cookie-beállítások a helyszíni alkalmazások Azure Active Directoryban való eléréséhez című témakörben talál.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériában - 2019 január

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2019 januárjában hozzáadtuk ezt a 35 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), Expiration [Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Működőképes](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial) [, CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Új Azure AD-identitásvédelem-fejlesztések (nyilvános előzetes verzió)

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

Örömmel jelentjük be, hogy az alábbi fejlesztéseket adtuk hozzá az Azure AD Identity Protection nyilvános előzetes verzióajánlatához, többek között:

- Frissített és integráltabb felhasználói felület

- További API-k

- Továbbfejlesztett kockázatértékelés gépi tanulással

- Termékszintű beállítás a kockázatos felhasználók és a kockázatos bejelentkezések között

A fejlesztésekről a Mi [az Azure Active Directory identity protection (frissítve)?](https://aka.ms/IdentityProtectionDocs) hogy többet tudjon meg, és ossza meg gondolatait a terméken keresztül kéri.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Új Alkalmazászárolási funkció a Microsoft Authenticator alkalmazáshoz iOS és Android rendszerű eszközökön

**Típus:** Új funkció  
**Szolgáltatás kategória:** Microsoft Hitelesítő alkalmazás  
**Termék képesség:** Identitásbiztonsági & védelem

Az egyszeri jelkódok, az alkalmazásadatok és az alkalmazásbeállítások biztonságának megőrzéséhez bekapcsolhatja az Alkalmazászárolás funkciót a Microsoft Authenticator alkalmazásban. Az App Lock bekapcsolása azt jelenti, hogy a Microsoft Authenticator alkalmazás minden megnyitásakor a PIN-kód vagy a biometrikus adatok használatával kell hitelesítenie magát.

További információt a [Microsoft Authenticator alkalmazás gyakori kérdések című témakörben talál.](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq)

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Továbbfejlesztett Azure AD kiemelt identitáskezelés (PIM) exportálási képességek

**Típus:** Új funkció  
**Szolgáltatás kategória:** Kiemelt identitáskezelés  
**Termék képesség:** Kiemelt identitáskezelés

A kiemelt identitáskezelés (PIM) rendszergazdák most már exportálhatja az összes aktív és jogosult szerepkör-hozzárendelések egy adott erőforrás, amely magában foglalja a szerepkör-hozzárendelések az összes gyermek erőforrás. Korábban a rendszergazdák nehezen kapták meg az előfizetés szerepkör-hozzárendeléseinek teljes listáját, és minden egyes erőforráshoz szerepkör-hozzárendeléseket kellett exportálniuk.

További információ: [Tevékenység- és naplózási előzmények megtekintése az Azure-erőforrás-szerepkörökhöz a PIM-ben című témakörben.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

---

## <a name="novemberdecember-2018"></a>2018. november/december

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>A szinkronizálási hatókörből eltávolított felhasználók már nem váltanak csak felhőalapú fiókokra

**Típus:** Rögzített  
**Szolgáltatás kategória:** Felhasználókezelés  
**Termék képesség:** Taglista

>[!Important]
>Hallottuk és megértjük a frusztrációt, mert ez a javítás. Ezért visszaállítottuk ezt a módosítást addig, amíg meg nem könnyítjük a javítás megvalósítását a szervezetben.

Kijavítottunk egy hibát, amelyben a felhasználó DirSyncEnabled jelzője tévesen **hamisra** vált, amikor az Active Directory tartományi szolgáltatások (AD DS) objektumot kizárták a szinkronizálási hatókörből, majd a következő szinkronizálási ciklusban áthelyezték az Azure AD-ben lévő Lomtárba. Ennek a javításnak az eredményeként, ha a felhasználó ki van zárva a szinkronizálási hatókörből, és ezt követően visszaállítja az Azure AD Lomtárból, a felhasználói fiók a várt módon szinkronizálva marad a helyszíni AD-ből, és nem kezelhető a felhőben, mivel a jogosultságforrása (SoA) a helyszíni AD marad.

A javítás előtt hiba történt, amikor a DirSyncEnabled jelző hamis ra váltott. Rossz benyomást keltett, hogy ezeket a fiókokat csak felhőalapú objektumokká alakították át, és hogy a fiókok kezelhetők a felhőben. A fiókok azonban továbbra is megőrizték a soa-t helyszíni és a helyszíni AD-ből érkező összes szinkronizált tulajdonság (árnyékattribútum). Ez a feltétel több problémát okozott az Azure AD-ben és más felhőbeli számítási feladatokban (például az Exchange Online-ban), amelyek várhatóan ezeket a fiókokat szinkronizáltakként kezelik az AD-ből, de most antól csak felhőalapú fiókokként viselkednek.

Jelenleg az egyetlen módja annak, hogy valóban konvertálni egy szinkronizált-from-AD-fiók csak felhőalapú fiók letiltásával DirSync a bérlői szinten, amely elindítja a háttér-művelet et a SoA átviteléhez. Ez a fajta SoA-módosítás megköveteli (de nem kizárólagosan) az összes helyszíni kapcsolódó attribútumok (például LastDirSyncTime és árnyék attribútumok) tisztítására és a jel küldése más felhőbeli számítási feladatok, hogy a megfelelő objektumot konvertálni egy csak felhőbeli fiók is.

Ez a javítás ezért megakadályozza az AD-ből szinkronizált felhasználó ImmutableID attribútumának közvetlen frissítését, amelyre a múltban bizonyos esetekben szükség volt. Az Azure AD-ben egy objektum immutableID-je, ahogy a neve is mutatja, úgy van, hogy nem módosítható. Az Azure AD Connect Health és az Azure AD Connect szinkronizálási ügyfélprogramban megvalósított új funkciók az ilyen forgatókönyvek kezelésére érhetők el:

- **Nagyméretű ImmutableID-frissítés sok felhasználó számára szakaszos megközelítésben**
  
  Például hosszú AD DS erdők közötti áttelepítést kell végeznie. Megoldás: Használja az Azure AD Connect **a Forráshorgony konfigurálásához,** és a felhasználó áttelepítések, másolja a meglévő ImmutableID értékeket az Azure AD-ből a helyi AD DS-felhasználó ms-DS-konzisztencia-Guid attribútum az új erdő. További információ: [Az ms-DS-ConsistencyGuid használata sourceAnchor néven](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)című témakörben talál.

- **Nagy méretű ImmutableID frissítések sok felhasználó számára egy lövés**

  Például az Azure AD Connect megvalósítása közben hibát követ el, és most módosítania kell a SourceAnchor attribútumot. Megoldás: Tiltsa le a DirSync-et bérlői szinten, és törölje az összes érvénytelen ImmutableID-értéket. További információt az [Office 365 címtár-szinkronizálásának kikapcsolása című témakörben talál.](/office365/enterprise/turn-off-directory-synchronization)

- **A helyszíni felhasználó és egy meglévő felhasználó újraegyeztetése az Azure AD-ben** Például egy felhasználó, amely újra létrehozott Az AD DS létrehoz egy duplikált az Azure AD-fiók helyett egy meglévő Azure AD-fiók (árva objektum). Megoldás: Használja az Azure AD Connect Health az Azure Portalon a forráshorgony/ImmutableID újraleképezéséhez. További információ: [Orphaned object scenario](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Breaking Change: A naplózási és bejelentkezési naplók sémájának frissítése az Azure Monitoron keresztül

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Jelenleg a naplózási és a bejelentkezési naplóadatfolyamokat az Azure Monitoron keresztül tesszük közzé, így zökkenőmentesen integrálhatja a naplófájlokat a SIEM-eszközökkel vagy a Log Analytics szolgáltatással. A visszajelzések alapján, valamint a funkció általános rendelkezésre állási bejelentésére való felkészülés során a következő módosításokat hajtjuk végre a sémán. Ezek a sémamódosítások és a kapcsolódó dokumentációfrissítések január első hetében történnek.

#### <a name="new-fields-in-the-audit-schema"></a>A Naplózásséma új mezői
Új **Művelettípus** mezőt adunk hozzá, hogy biztosítsuk az erőforráson végrehajtott művelet típusát. Például **Hozzáadás**, **Frissítés**vagy **Törlés**.

#### <a name="changed-fields-in-the-audit-schema"></a>A Naplózás séma módosított mezői
A naplózási sémában a következő mezők változnak:

|Mező neve|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|Kategória|Ez volt a **Szolgáltatás neve** mező. Ez most a **Naplózási kategóriák** mező. **A szolgáltatásnév** át lett nevezve a **loggedByService** mezőre.|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Önkiszolgáló jelszó-visszaállítás</li></ul>|<ul><li>Felhasználókezelés</li><li>Csoportkezelés</li><li>Alkalmazáskezelés</li></ul>|
|célforrások|Tartalmazza **TargetResourceType** a legfelső szinten.|&nbsp;|<ul><li>Szabályzat</li><li>Alkalmazás</li><li>Felhasználó</li><li>Csoport</li></ul>|
|bejelentkezettSzolgáltatás|A naplót létrehozó szolgáltatás nevét adja meg.|Null|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Új jelszó önkiszolgáló kérése</li></ul>|
|Eredmény|A naplónaplók eredményét tartalmazza. Korábban ezt felsorolták, de most megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li></ul>|<ul><li>Sikeres</li><li>Hiba</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>A Bejelentkezési séma módosított mezői
A bejelentkezési sémában a következő mezők változnak:

|Mező neve|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|alkalmazott Feltételes hozzáférésházirend-házirendek|Ez volt a **conditionalaccessPolicies** mező. Ez most az **appliedConditionalAccessPolicies** mező.|Nincs változás besorolás|Nincs változás besorolás|
|conditionalAccessStatus|A feltételes hozzáférési házirend állapotának eredményét adja meg bejelentkezéskor. Korábban ezt felsorolták, de most megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sikeres</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Letiltva</li></ul>|
|alkalmazott Feltételes hozzáférés-házirendek: eredmény|Az egyes feltételes hozzáférési házirend-állapot oka a bejelentkezéskor. Korábban ezt felsorolták, de most megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sikeres</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Letiltva</li></ul>|

A sémáról további információt az [Azure AD naplózási naplóinak sémájának értelmezése az Azure Monitorban című témakörben talál (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Identity Protection fejlesztések a felügyelt gépi tanulási modell és a kockázati pontszám motor

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Kockázati pontszámok

Az Identitásvédelemmel kapcsolatos felhasználói és bejelentkezési kockázatértékelési motor fejlesztései segíthetnek a felhasználói kockázatok pontosságának és lefedettségének javításában. A rendszergazdák észrevehetik, hogy a felhasználói kockázati szint már nem kapcsolódik közvetlenül az adott észlelések kockázati szintjéhez, és hogy nő a kockázatos bejelentkezési események száma és szintje.

A kockázatészleléseket most a felügyelt gépi tanulási modell értékeli ki, amely a felhasználó bejelentkezéseinek további funkcióival és az észlelések mintájával számítja ki a felhasználói kockázatot. A modell alapján a rendszergazda előfordulhat, hogy a felhasználók magas kockázati pontszámok, akkor is, ha a felhasználóhoz társított észlelések alacsony vagy közepes kockázatú. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>A rendszergazdák alaphelyzetbe állíthatják saját jelszavukat a Microsoft Authenticator alkalmazással (nyilvános előzetes verzió)

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Önkiszolgáló jelszó alaphelyzetbe állítása  
**Termék képesség:** Felhasználói hitelesítés

Az Azure AD-rendszergazdák mostantól visszaállíthatják saját jelszavukat a Microsoft Authenticator alkalmazás értesítései vagy bármely mobilhitelesítő alkalmazás vagy hardvertoken kódjával. A saját jelszó alaphelyzetbe állításához a rendszergazdák az alábbi két módszert használhatják:

- A Microsoft Hitelesítő alkalmazás értesítése

- Egyéb mobil hitelesítő alkalmazás / Hardvertoken kód

- E-mail

- Telefonhívás

- Szöveges üzenet

A jelszavak visszaállításához a Microsoft Authenticator alkalmazás használatával kapcsolatos további információkért lásd: [Azure AD önkiszolgáló jelszó-visszaállítás – Mobilalkalmazás és SSPR (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Új Azure AD-s felhőalapú eszközfelügyelői szerepkör (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Eszközregisztráció és -kezelés  
**Termék képesség:** Hozzáférés-vezérlés

A rendszergazdák hozzárendelhetik a felhasználókat az új Felhőeszköz-rendszergazda szerepkörhöz a felhőeszköz-rendszergazdai feladatok elvégzéséhez. A felhőalapú eszközök rendszergazdái szerepkörrel rendelkező felhasználók engedélyezhetik, letilthatják és törölhetik az eszközöket az Azure AD-ben, valamint olvashatják a Windows 10 BitLocker-kulcsokat (ha vannak ilyenek) az Azure Portalon.

A szerepkörökről és engedélyekről további információt a [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban című témakörben talál.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Az eszközök kezelése az azure-beli AD új tevékenységidőbélyegével (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Eszközregisztráció és -kezelés  
**Termék képesség:** Eszközéletciklus-kezelés

Tisztában vagyunk azzal, hogy idővel frissítenie kell, és ki kell vonnia a szervezetek eszközeit az Azure AD-ben, hogy elkerülje az elavult eszközök környezetében. A folyamat segítése érdekében az Azure AD most antól frissíti az eszközöket egy új tevékenységidőbélyeggel, amely segít az eszköz életciklusának kezelésében.

Az időbélyeg be- és használatáról további információt [a Hogyan: Az elavult eszközök kezelése az Azure AD-ben című témakörben talál.](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>A rendszergazdák megkövetelhetik a felhasználóktól, hogy minden eszközön elfogadják a használati feltételeket

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Kormányzás
 
A rendszergazdák most bekapcsolhatják a **Felhasználók beleegyezésének megkövetelése minden eszközön** lehetőséget, hogy a felhasználók számára, hogy fogadja el a használati feltételeket minden eszközön, amit használnak a bérlőn.

További információt az [Azure Active Directory használati feltételei szolgáltatás eszközönkénti használati feltételei című részében talál.](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>A rendszergazdák beállíthatják a használati feltételeket, hogy lejárjanak egy ismétlődő ütemezés alapján

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Kormányzás
 

A rendszergazdák most antól bekapcsolhatják a **Lejáratos hozzájárulás i.** beállítást, hogy a használati feltételek a megadott ismétlődő ütemezés alapján az összes felhasználó számára lejárjanak. A menetrend lehet évente, kétévente, negyedévente vagy havonta. A használati feltételek lejárta után a felhasználóknak újra el kell fogadniuk.

További információt az [Azure Active Directory használati feltételeinek (Add terms of terms of use) című szakaszában talál.](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>A rendszergazdák beállíthatják a használati feltételeket, hogy lejárjanak az egyes felhasználók ütemezése alapján

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Kormányzás

A rendszergazdák mostantól megadhatják azt az időtartamot, amelyet a felhasználónak újra el kell fogadnia a használati feltételeknek. A rendszergazdák például megadhatják, hogy a felhasználóknak 90 naponta újra el kell fogadniuk a használati feltételeket.

További információt az [Azure Active Directory használati feltételeinek (Add terms of terms of use) című szakaszában talál.](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Új Azure AD kiemelt identitáskezelés (PIM) e-mailek az Azure Active Directory-szerepkörökhöz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Kiemelt identitáskezelés  
**Termék képesség:** Kiemelt identitáskezelés
 
Az Azure AD kiemelt identitáskezelést (PIM) használó ügyfelek mostantól heti kivonatoló e-mailt kaphatnak, amely az elmúlt hét nap következő információit tartalmazza:

- A legnépszerűbb jogosult és állandó szerepkör-hozzárendelések áttekintése

- Szerepköröket aktiváló felhasználók száma

- A PIM-ben szerepkörökhöz rendelt felhasználók száma

- A PIM-en kívüli szerepkörökhöz rendelt felhasználók száma

- A PIM-ben "állandóvá tett" felhasználók száma

A PIM-ről és a rendelkezésre álló e-mail értesítésekről további információt az [E-mail értesítések a PIM-ben című témakörben talál.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)

---

### <a name="group-based-licensing-is-now-generally-available"></a>A csoportalapú licencelés már általánosan elérhető

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Taglista

A csoportalapú licencelés nyilvános előzetes verzión kívüli, és mostantól általánosan elérhető. Az általános kiadás részeként skálázhatóbbá tettük ezt a funkciót, és lehetővé tettük a csoportalapú licencelési feladatok újrafeldolgozását egyetlen felhasználó számára, valamint a csoportalapú licencelés használatát az Office 365 E3/A3 licencekkel.

A csoportalapú licencelésről a [Mi a csoportalapú licencelés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériában – 2018.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2018 novemberében hozzáadtuk ezt a 26 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus,](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial) [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge member Portál,](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial) [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), Plex Apps - [Classic,](https://www.plexonline.com/signon) [Plex Apps - UX Teszt,](https://test.cloud.plex.com/sso) [Plex Apps - UX](https://cloud.plex.com/sso), Plex Apps [- IAM](https://accounts.plex.com/), [CRAFTS - Gyermekgondozási Records, Jelenlét, & pénzügyi nyomkövető rendszer](https://getcrafts.ca/craftsregistration) 

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

## <a name="october-2018"></a>2018. október

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Az Azure AD-naplók mostantól együttműködnek az Azure Log Analytics szolgáltatással (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Örömmel jelentjük be, hogy most már továbbíthatja az Azure AD-naplókat az Azure Log Analytics szolgáltatásba! Ez a legkeresettebb funkció még jobb hozzáférést biztosít az elemzésekhez a vállalkozás, a műveletek és a biztonság számára, valamint segít az infrastruktúra figyelésében. További információkért tekintse meg az [Azure Active Directory-tevékenységnaplók az Azure Log Analytics már elérhető](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériában – 2018 október

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2018 októberében hozzáadtuk ezt a 14 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Saját Award Pontok](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox,](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial)Dialpad, [ON24 virtuális környezet,](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial) [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Három](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="azure-ad-domain-services-email-notifications"></a>Az Azure AD tartományi szolgáltatások e-mail értesítései

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure AD tartományi szolgáltatások  
**Termék képesség:** Azure AD tartományi szolgáltatások

Az Azure AD tartományi szolgáltatások riasztásokat biztosít az Azure Portalon a helytelen konfigurációkról vagy a felügyelt tartománnyal kapcsolatos problémákról. Ezek a riasztások részletes útmutatókat tartalmaznak, így megpróbálhatja kijavítani a problémákat anélkül, hogy kapcsolatba kellene lépnie az ügyfélszolgálattal.

Októbertől testreszabhatja a felügyelt tartomány értesítési beállításait, így amikor új riasztások jelennek meg, e-mailt küld egy kijelölt csoportnak, így nincs szükség a portál on-re vonatkozó frissítésekre.

További információt az [Értesítési beállítások az Azure AD tartományi szolgáltatásokban című témakörben talál.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications)

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Az Azure AD portal támogatja a ForceDelete tartomány API-t az egyéni tartományok törléséhez 

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Címtár kezelése  
**Termék képesség:** Taglista

Örömmel jelentjük be, hogy most már használhatja a ForceDelete domain API-t az egyéni tartománynevek törléséhez aszinkron átnevezési hivatkozásokkal, például felhasználókkal, csoportokkal és alkalmazásokkal az egyéni tartománynévből (contoso.com) vissza a kezdeti alapértelmezett tartománynévre (contoso.onmicrosoft.com).

Ez a módosítás segít az egyéni tartománynevek gyorsabb törlésében, ha a szervezet már nem használja a nevet, vagy ha a tartománynevet egy másik Azure AD-vel kell használnia.

További információt az [Egyéni tartománynév törlése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name)című témakörben talál.

---

## <a name="september-2018"></a>2018. szeptember
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Frissített rendszergazdai szerepkör-engedélyek dinamikus csoportokhoz

**Típus:** Rögzített  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

Kijavítottunk egy problémát, így a konkrét rendszergazdai szerepkörök mostmár dinamikus tagsági szabályokat hozhatnak létre és frissíthetnek anélkül, hogy a csoport tulajdonosának kellene lenniük.

A szerepek a következők:

- Globális rendszergazda

- Intune-rendszergazda

- Rendszergazda

További információ: [Dinamikus csoport létrehozása és állapot ellenőrzése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Egyszerűsített egyszeri bejelentkezési (SSO) konfigurációs beállítások egyes külső alkalmazásokhoz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

Tisztában vagyunk azzal, hogy az egyszeri bejelentkezés (SSO) beállítása a szoftver szolgáltatásként (SaaS) alkalmazások számára kihívást jelenthet az egyes alkalmazások konfigurációinak egyedi jellege miatt. Egyszerűsített konfigurációs élményt hoztunk létre a következő külső SaaS-alkalmazások egyszeri szolgáltatás konfigurációs beállításainak automatikus feltöltéséhez:

- Zendesk

- ArcGis Online

- Jamf Pro

Az egy kattintásos élmény használatának megkezdéséhez nyissa meg az **alkalmazás hoz** > az Azure Portal**egyszeri szolgáltatás konfigurációs** lapját. További információ: [SaaS-alkalmazások integrációja az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – Hol találhatók az adatok? Oldalala

**Típus:** Új funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** GoLocal között

Válassza ki a vállalat régióját az **Azure Active Directoryból – Hol található az adatok** lap, ahol megtekintheti, hogy mely Azure-adatközpont ban található az Azure AD-adatok az összes Azure AD-szolgáltatás ban. Az adatokat a vállalat régiójához tartozó azure AD-szolgáltatások alapján szűrheti.

A szolgáltatás eléréséhez és további információkért lásd: [Azure Active Directory – Hol találhatók az adatok.](https://aka.ms/AADDataMap)

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Új telepítési terv érhető el a Saját alkalmazások hozzáférés e panelen

**Típus:** Új funkció  
**Szolgáltatás kategória:** Saját alkalmazások  
**Termék képesség:** Sso

Tekintse meg a My Apps Access panelen elérhetőhttps://aka.ms/deploymentplans)új telepítési tervet ( .
A Saját alkalmazások hozzáférése panelen a felhasználók egyetlen helyen találhatják meg és érhetik el alkalmazásaikat. Ez a portál önkiszolgáló lehetőségeket is biztosít a felhasználóknak, például hozzáférést kér alkalmazásokhoz és csoportokhoz, vagy mások nevében kezeli a hozzáférést ezekhez az erőforrásokhoz.

További információ: [Mi a My Apps portál?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Új hibaelhárítás és támogatás lap az Azure Portal Bejelentkezési naplók lapján

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Az Azure **Portal Bejelentkezések** lapján található új **Hibaelhárítás és támogatás** lap célja, hogy segítse a rendszergazdákat és a támogatási mérnököket az Azure AD-bejelentkezésekkel kapcsolatos problémák megoldásában. Ez az új lap a hibakódot, a hibaüzenetet és a javítási javaslatokat (ha van ilyen) tartalmazza a probléma megoldásához. Ha nem tudja megoldani a problémát, új módot is adunk arra, hogy a Másolás a **vágólapra** szolgáltatással hozzon létre egy támogatási jegyet, amely feltölti a támogatási jegyben lévő naplófájl **kérelemazonosító** és **dátum (UTC)** mezőit.  

![Az új lapot megjelenítő bejelentkezési naplók](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>A dinamikus tagsági szabályok létrehozásához használt egyéni bővítménytulajdonságok továbbfejlesztett támogatása

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

Ezzel a frissítéssel most már a dinamikus felhasználói csoportszabályszerkesztőegyéni **bővítménytulajdonságainak betöltése** hivatkozásra kattinthat, megadhatja az egyedi alkalmazásazonosítót, és megkaphatja a felhasználók dinamikus tagsági szabályának létrehozásakor használandó egyéni bővítménytulajdonságok teljes listáját. Ez a lista is frissíthető, hogy az alkalmazás új egyéni bővítménytulajdonságait is lekérhesse.

A dinamikus tagsági szabályok egyéni bővítménytulajdonságainak használatáról a [Bővítmény tulajdonságai és az egyéni bővítmény tulajdonságai című](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties) témakörben talál további információt.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD-alkalmazásalapú feltételes hozzáféréshez

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Személyazonosság-biztonság és -védelem

A következő alkalmazások szerepelnek a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)listáján:

- Microsoft To-Do

- Microsoft Stream

További információkért lásd:

- [Azure AD-alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Új támogatás az önkiszolgáló jelszó-visszaállításhoz a Windows 7/8/8.1 zárolási képernyőről

**Típus:** Új funkció  
**Szolgáltatás kategória:** SSPR  
**Termék képesség:** Felhasználói hitelesítés

Az új funkció beállítása után a felhasználók egy hivatkozást fognak látni a Windows 7, Windows 8 vagy Windows 8.1 rendszert futtató eszközök **zárolási** képernyőjén. A hivatkozásra kattintva a felhasználó ugyanazon a jelszó-visszaállítási folyamaton keresztül vezeti végig a felhasználót, mint a webböngészőn keresztül.

További információ: [Jelszó-visszaállítás engedélyezése Windows 7, 8 és 8.1 rendszerről](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Módosítási értesítés: Az engedélyezési kódok a továbbiakban nem lesznek újrafelhasználhatók 

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

2018. november 15-től kezdődően az Azure AD leállítja az alkalmazások korábban használt hitelesítési kódjainak fogadását. Ez a biztonsági változás segít abban, hogy az Azure AD összhangba hozza az OAuth specifikációt, és a v1 és a v2 végpontokon is érvénybe lép.

Ha az alkalmazás újrahasználja az engedélyezési kódokat több erőforrás jogkivonatának lekéréséhez, azt javasoljuk, hogy a kód használatával kapjon egy frissítési jogkivonatot, majd használja ezt a frissítési jogkivonatot további jogkivonatok beszerzéséhez más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési jogkivonatok többször is használhatók több erőforrásközött. Egy alkalmazás, amely megpróbálja újra használni a hitelesítési kódot az OAuth kódfolyamat során invalid_grant hibaüzenetet kap.

Ehhez és más protokollokkal kapcsolatos módosításokhoz tekintse meg [a hitelesítéssel kapcsolatos újdonságok teljes listáját.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériában – 2018.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2018 szeptemberében hozzáadtuk ezt a 16 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Toborzó Szoftver](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Összehívása](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="support-for-additional-claims-transformations-methods"></a>További jogcímátalakítási módszerek támogatása

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

Bevezettük az új jogcímátalakítási módszereket, a ToLower() és a ToUpper() metódust, amelyek az SAML-alapú egyszeri bejelentkezési konfiguráció lapról alkalmazhatók az SAML-alapú **egyszeri bejelentkezési konfigurációlapjáról.**

További információ: [Az Azure AD-ben a vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Frissített SAML-alapú alkalmazáskonfigurációs felhasználói felület (előzetes verzió)

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

A frissített SAML-alapú alkalmazáskonfigurációs felhasználói felület részeként a következőket kapja:

- Frissített forgatókönyv-élmény az SAML-alapú alkalmazások konfigurálásához.

- Jobban látható, hogy mi hiányzik vagy helytelen a konfigurációban.

- Több e-mail cím hozzáadásának lehetősége az elévülési tanúsítvány értesítéséhez.

- Új jogcímátalakítási módszerek, ToLower() és ToUpper(), és így tovább.

- Saját jogkivonat-aláíró tanúsítvány feltöltésének egyik módja a vállalati alkalmazásokhoz.

- Az SAML-alkalmazások NameID formátumának beállításának módja, valamint a NameID érték címtárbővítményekként való beállításának módja.

A frissített nézet bekapcsolásához kattintson az **Új élmény kipróbálása** hivatkozásra az **Egyszeri bejelentkezés** lap tetején. További információ: [Oktatóanyag: SAML-alapú egyszeri bejelentkezés konfigurálása egy alkalmazáshoz az Azure Active Directoryval.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)

---

## <a name="august-2018"></a>2018. augusztus

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Az Azure Active Directory IP-címtartományaimódosításai

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Platform

Nagyobb IP-tartományokat vezetünk be az Azure AD-be, ami azt jelenti, hogy ha konfigurálta az Azure AD IP-címtartományait a tűzfalakhoz, útválasztókhoz vagy hálózati biztonsági csoportokhoz, frissítenie kell őket. Ezt a frissítést azért végezzük el, hogy ne kelljen újra módosítania a tűzfalat, az útválasztót vagy a hálózati biztonsági csoportok IP-tartományának konfigurációját, amikor az Azure AD új végpontokat ad hozzá. 

A hálózati forgalom a következő két hónapban ezekre az új tartományokra terjed át. A megszakítás nélküli szolgáltatás folytatásához 2018.

- 20.190.128.0/18 

- 40.126.0.0/18 

Javasoljuk, hogy ne távolítsa el a régi IP-címtartományokat, amíg az összes hálózati forgalom át nem kerül az új tartományokba. Az áthelyezéssel kapcsolatos frissítésekről és a régi tartományok eltávolításának időpontjáról az [Office 365 URL-címei és IP-címtartományai](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)című témakörben olvashat.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Módosítási értesítés: Az engedélyezési kódok a továbbiakban nem lesznek újrafelhasználhatók 

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

2018. november 15-től kezdődően az Azure AD leállítja az alkalmazások korábban használt hitelesítési kódjainak fogadását. Ez a biztonsági változás segít abban, hogy az Azure AD összhangba hozza az OAuth specifikációt, és a v1 és a v2 végpontokon is érvénybe lép.

Ha az alkalmazás újrahasználja az engedélyezési kódokat több erőforrás jogkivonatának lekéréséhez, azt javasoljuk, hogy a kód használatával kapjon egy frissítési jogkivonatot, majd használja ezt a frissítési jogkivonatot további jogkivonatok beszerzéséhez más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési jogkivonatok többször is használhatók több erőforrásközött. Egy alkalmazás, amely megpróbálja újra használni a hitelesítési kódot az OAuth kódfolyamat során invalid_grant hibaüzenetet kap.

Ehhez és más protokollokkal kapcsolatos módosításokhoz tekintse meg [a hitelesítéssel kapcsolatos újdonságok teljes listáját.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Konvergens biztonsági adatok kezelése az önkiszolgáló jelszóhoz (SSPR) és a többtényezős hitelesítéshez (MFA)

**Típus:** Új funkció  
**Szolgáltatás kategória:** SSPR  
**Termék képesség:** Felhasználói hitelesítés
 
Ez az új funkció egyetlen helyen és felületen segíti az embereket az SSPR és az MFA biztonsági adatainak (például telefonszám, mobilalkalmazás stb.) kezelésében; mint korábban, ahol két különböző helyen végezték.

Ez a konvergens tapasztalat az SSPR vagy az MFA használatával is működik. Továbbá, ha a szervezet nem kényszeríti az MFA vagy az SSPR regisztrációját, a személyek továbbra is regisztrálhatnak a szervezet által engedélyezett MFA vagy SSPR biztonsági információs módszereket a Saját alkalmazások portálról.

Ez egy opt-in nyilvános előzetes verzió. A rendszergazdák bekapcsolhatják az új felületet (ha szükséges) egy kiválasztott csoporthoz vagy a bérlő összes felhasználójához. A konvergens élményről további információt a [Konvergens élmény blogban](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/) talál.

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Új CSAK HTTP-s cookie-k beállítása az Azure AD alkalmazás proxyalkalmazásaiban

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Hozzáférés-vezérlés

Van egy új beállítás, amelynek **neve, HTTP-only cookie-k** az alkalmazásproxy-alkalmazásokban. Ez a beállítás nagyobb biztonságot nyújt azáltal, hogy a HTTPOnly jelzőt a HTTP-válasz fejlécébe is beilleszti mind az alkalmazásproxy-hozzáféréshez, mind a munkamenet-cookie-khoz, leállítja a cookie-hoz való hozzáférést egy ügyféloldali parancsfájlból, és további megakadályozza az olyan műveleteket, mint a cookie másolása vagy módosítása. Bár ezt a jelzőt korábban még nem használták, a cookie-kat mindig titkosítva és TLS-kapcsolaton keresztül továbbítottuk a helytelen módosítások elleni védelem érdekében.

Ez a beállítás nem kompatibilis az ActiveX-vezérlőket használó alkalmazásokkal, például a Távoli asztallal. Ha ilyen helyzetben van, javasoljuk, hogy kapcsolja ki ezt a beállítást.

A csak HTTP-cookie-k beállításáról az Alkalmazások közzététele az [Azure AD alkalmazásproxy használatával című](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)témakörben olvashat bővebben.

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Az Azure-erőforrások privilegizált identitáskezelése (PIM) támogatja a Felügyeleti csoport erőforrástípusait

**Típus:** Új funkció  
**Szolgáltatás kategória:** Kiemelt identitáskezelés  
**Termék képesség:** Kiemelt identitáskezelés
 
A just-in-time aktiválási és hozzárendelési beállítások mostantól ugyanúgy alkalmazhatók a Felügyeleti csoport erőforrástípusokra, mint az Előfizetések, az Erőforráscsoportok és az Erőforrások (például a virtuális gépek, az App Services és egyebek) esetében. Ezenkívül bárki, aki rendszergazdai hozzáférést biztosít egy felügyeleti csoport számára, felderítheti és kezelheti az erőforrást a PIM-ben.

A PIM- és az Azure-erőforrásokról további információt az [Azure-erőforrások felderítése és kezelése a Kiemelt identitáskezelés használatával című](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources) témakörben talál.
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Az alkalmazás-hozzáférés (előzetes verzió) gyorsabb hozzáférést biztosít az Azure AD-portálhoz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Kiemelt identitáskezelés  
**Termék képesség:** Kiemelt identitáskezelés
 
Ma, ha pim használatával aktivál egy szerepkört, az engedélyek érvénybe léptetéséhez több mint 10 percet is igénybe vehet. Ha úgy dönt, hogy az alkalmazás-hozzáférés, amely jelenleg nyilvános előzetes verzióban, a rendszergazdák hozzáférhetnek az Azure AD portál, amint az aktiválási kérelem befejeződik.

Jelenleg az alkalmazás-hozzáférés csak az Azure AD portal-élményt és az Azure-erőforrásokat támogatja. A PIM- és alkalmazáshozzáférésről a [Mi az Azure AD kiemelt identitáskezelés?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériában – 2018.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2018 augusztusában hozzáadtuk ezt a 16 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Kötetlen](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobil és webes tesztelés,](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial) [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), Way We [Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dosszié](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Költségjelentések](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>A natív tablótámogatás már elérhető az Azure AD alkalmazásproxyban

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Hozzáférés-vezérlés

Az OpenID Connect és az OAuth 2.0 Code Grant protokoll frissítésével a hitelesítés előtti protokollunkhoz, többé nem kell további konfigurációt végeznie a Tableau alkalmazásproxyval való használatához. Ez a protokollmódosítás abban is segít, hogy az alkalmazásproxy jobban támogassa a modernebb alkalmazásokat, ha csak A HTTP-átirányításokat használja, amelyeket a JavaScript és a HTML-címkék általában támogatnak.

A Tableau natív támogatásáról az [Azure AD alkalmazásproxy most natív Tableau-támogatással című](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)témakörben talál további információt.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Új támogatás a Google mint a B2B vendégfelhasználók identitásszolgáltatójaként való hozzáadásához az Azure Active Directoryban (előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C

Ha a szervezetben beállítja az összevonást a Google-lal, lehetővé teheti a meghívott Gmail-felhasználók számára, hogy a meglévő Google-fiókjukkal jelentkezzenek be a megosztott alkalmazásokba és erőforrásokba anélkül, hogy személyes Microsoft-fiókot (MSA-t) vagy Azure AD-fiókot kellene létrehozniuk.

Ez egy opt-in nyilvános előzetes verzió. A Google-szövetségről a [Google hozzáadása a B2B vendégfelhasználók identitásszolgáltatójaként](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)című témakörben talál további információt.

---

## <a name="july-2018"></a>2018. július

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Az Azure Active Directory e-mail értesítéseinek fejlesztései

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Identitáséletciklus-kezelés
 
Az Azure Active Directory (Azure AD) e-mailjei mostantól frissített kialakítással, valamint a feladó e-mail címének és a feladó megjelenítendő nevének módosításával rendelkeznek, amikor azalábbi szolgáltatásokból küldik őket:
 
- Az Azure AD Access értékelései
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Vállalati alkalmazáslejáró tanúsítványértesítések
- Vállalati alkalmazáskiépítési szolgáltatás értesítések
 
Az e-mail értesítéseket a következő e-mail címről és megjelenítendő névről küldjük el:

- E-mail cím:azure-noreply@microsoft.com
- Megjelenítendő név: Microsoft Azure
 
Az új e-mail tervek és további információk megtekintéséhez tekintse meg [az E-mail értesítések az Azure AD PIM-ben című témakört.](https://go.microsoft.com/fwlink/?linkid=2005832)

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Az Azure AD-tevékenységnaplók már elérhetők az Azure Monitoron keresztül

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Az Azure AD-tevékenységnaplók már elérhető nyilvános előzetes verzióban az Azure Monitor (Az Azure platformszintű figyelési szolgáltatás). Az Azure Monitor hosszú távú megőrzést és zökkenőmentes integrációt kínál a következő fejlesztések mellett:

- Hosszú távú megőrzésa a naplófájlok at a saját Azure storage-fiók.

- Zökkenőmentes SIEM-integráció, anélkül, hogy egyéni parancsfájlok írását vagy karbantartását igényelte volna.

- Zökkenőmentes integráció saját egyéni megoldásaival, elemzési eszközeivel vagy incidenskezelési megoldásaival.

Az új képességekről további információt az [Azure AD-tevékenységnaplók az Azure Monitor diagnosztikájában című blogunkban talál, amely most már nyilvános előzetes verzióban érhető el,](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) és dokumentációnk, [az Azure Active Directory tevékenységnaplói az Azure Monitorban (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Az Azure AD bejelentkezések jelentéséhez hozzáadott feltételes hozzáférési adatok

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** Identitásbiztonsági & védelem
 
Ez a frissítés lehetővé teszi, hogy mely szabályzatok kiértékelése, amikor a felhasználó bejelentkezik együtt a szabályzat eredményét. Emellett a jelentés most már tartalmazza a felhasználó által használt ügyfélalkalmazás típusát, így azonosíthatja az örökölt protokollforgalmat. A jelentésbejegyzések mostantól korrelációs azonosítót is kereshetnek, amely megtalálható a felhasználó felé néző hibaüzenetben, és a megfelelő bejelentkezési kérelem azonosítására és elhárítására használható.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Örökölt hitelesítések megtekintése a Bejelentkezések tevékenységnaplókon keresztül

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése
 
Az **Ügyfélalkalmazás** mező nek a bejelentkezési tevékenységnaplókban való bevezetésével az ügyfelek most már láthatják az örökölt hitelesítéseket használó felhasználókat. Az ügyfelek hozzáférhetnek ezekhez az információkhoz a Bejelentkezések Microsoft Graph API-val vagy az Azure AD portál bejelentkezési tevékenységnaplóival, ahol az **ügyfélalkalmazás-vezérlővel** szűrheti az örökölt hitelesítéseket. További részletekért tekintse meg a dokumentációt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériájában – 2018.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2018 júliusában hozzáadtuk ezt a 16 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Innovációs Központ](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Bizonyos Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom, [Eli Bevezetés](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Új felhasználói kiépítési SaaS-alkalmazások integrációi – 2018.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termékképesség:** harmadik fél integrációja
 
Az Azure AD lehetővé teszi a felhasználói identitások létrehozását, karbantartását és eltávolítását a SaaS-alkalmazásokban, például a Dropboxban, a Salesforce-ban, a ServiceNow-ban és egyebekben. 2018 júliusában az Azure AD alkalmazásgalériában a következő alkalmazások felhasználói kiépítési támogatását adtuk hozzá:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Az Azure AD-katalógusban a felhasználók kiépítést támogató alkalmazások listáját az [Azure Active Directoryval való SaaS-alkalmazások integrációja](https://aka.ms/appstutorial)című témakörben ismeri el.

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Az állapot szinkronizáláshoz való csatlakoztatása – Az árva és ismétlődő attribútumszinkronizálási hibák javításának egyszerűbb módja

**Típus:** Új funkció  
**Szolgáltatás kategória:** AD-csatlakozás  
**Termék képesség:** A & jelentés ének nyomon követése
 
Az Azure AD Connect Health önkiszolgáló szervizelést vezet be, amely segít a szinkronizálási hibák kiemelésében és javításában. Ez a funkció elhárítja a duplikált attribútumszinkronizálási hibákat, és javítja az Azure AD-ből árván maradt objektumokat. Ez a diagnózis a következő előnyökkel jár:

- Leszűkíti a duplikált attribútumszinkronizálási hibákat, konkrét javításokat biztosítva

- Fix alkalmazása dedikált Azure AD-forgatókönyvekhez, a hibák egyetlen lépésben történő megoldása

- Nincs szükség frissítésre vagy konfigurációra a funkció bekapcsolásához és használatához

További [információ: Duplikált attribútumszinkronizálási hibák diagnosztizálása és javítása](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Vizuális frissítések az Azure AD és MSA bejelentkezési élményéhez

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Azure Hirdetés  
**Termék képesség:** Felhasználói hitelesítés

Frissítettük a Microsoft online szolgáltatásokba való bejelentkezési élményének felhasználói felületét, például az Office 365-ben és az Azure-ban. Ez a változás teszi a képernyők kevésbé zsúfolt és egyszerűbb. Erről a változásról további információt az [Azure AD bejelentkezési élmény blogközelgő fejlesztései](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) című témakörben talál.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Az Azure AD Connect új kiadása – 2018.

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termék képesség:** Identitáséletciklus-kezelés

Az Azure AD Connect legújabb kiadása a következőket tartalmazza: 

- Hibajavítások és támogatási frissítések 

- A Ping-Federate integráció általános elérhetősége

- Frissítések a legújabb SQL 2012-ügyfélhez 

A frissítéssel kapcsolatos további információkért lásd: [Azure AD Connect: Version release history](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>A felhasználási feltételek frissítése végfelhasználói felhasználói felület

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Kormányzás

Frissítjük az elfogadási karakterláncot a TOU végfelhasználói felhasználói felületén.

**Aktuális szöveg.** A [tenantName] erőforrások eléréséhez el kell fogadnia a használati feltételeket.<br>**Új szöveg.** A[tenantName] erőforrás eléréséhez el kell olvasnia a használati feltételeket.

**Jelenlegi szöveg:** Ha úgy dönt, hogy elfogadja, az azt jelenti, hogy elfogadja a fenti felhasználási feltételekmindegyikét.<br>**Új szöveg:** Kérjük, kattintson az Elfogadás gombra annak megerősítéséhez, hogy elolvasta és megértette a használati feltételeket.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Az átmenő hitelesítés támogatja az örökölt protokollokat és alkalmazásokat

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
Az átmenő hitelesítés mostantól támogatja az örökölt protokollokat és alkalmazásokat. A következő korlátozások most már teljes mértékben támogatottak:

- A felhasználók modern hitelesítés nélkül jelentkeznek be a régebbi Office-ügyfélalkalmazásokba, az Office 2010-be és az Office 2013-ba.

- Hozzáférés a naptármegosztáshoz és az elfoglaltsági adatokhoz az Exchange hibrid környezetében csak az Office 2010-ben.

- A felhasználók modern hitelesítés nélkül jelentkeznek be a Skype Vállalati verzió ügyfélalkalmazásaiba.

- Felhasználói bejelentkezések a PowerShell 1.0-s verziójába.

- Az Apple Device Enrollment Program (Apple DEP) az iOS telepítősegédjét használva. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Konvergens biztonsági adatok kezelése az önkiszolgáló jelszó-visszaállításhoz és a többtényezős hitelesítéshez

**Típus:** Új funkció  
**Szolgáltatás kategória:** SSPR  
**Termék képesség:** Felhasználói hitelesítés

Ez az új funkció lehetővé teszi a felhasználók számára, hogy egyetlen felületen kezeljék biztonsági adataikat (például telefonszámukat, e-mail-címüket, mobilalkalmazásaikat és így tovább) az önkiszolgáló jelszó-visszaállítás (SSPR) és a többtényezős hitelesítés (MFA) esetében. A felhasználóknak a továbbiakban nem kell ugyanazt a biztonsági adatokat regisztrálniuk az SSPR-hez és az MFA-hoz két különböző élményben. Ez az új felület azokra a felhasználókra is vonatkozik, akik SSPR-t vagy Többfa-t rendelkeznek.

Ha egy szervezet nem kényszeríti az MFA vagy az SSPR regisztrációját, a felhasználók regisztrálhatják biztonsági adataikat a **Saját alkalmazások** portálon keresztül. Innen a felhasználók regisztrálhatnak bármilyen, az MFA vagy az SSPR számára engedélyezett metódust. 

Ez egy opt-in nyilvános előzetes verzió. A rendszergazdák bekapcsolhatják az új felületet (ha szükséges) a felhasználók egy kiválasztott csoportjához vagy a bérlő összes felhasználójához.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>A Jelszó visszaállításakor a Microsoft Authenticator alkalmazás segítségével ellenőrizheti személyazonosságát

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** SSPR  
**Termék képesség:** Felhasználói hitelesítés

Ez a funkció lehetővé teszi, hogy a nem rendszergazdák ellenőrizzék identitásukat, miközben alaphelyzetbe állítanak egy jelszót a Microsoft Authenticator (vagy bármely más hitelesítő alkalmazás) értesítésével vagy kódjával. Miután a rendszergazdák bekapcsolták ezt az önkiszolgáló jelszó-visszaállítási módszert, azok a felhasználók, akik aka.ms/mfasetup vagy aka.ms/setupsecurityinfo keresztül regisztráltak egy mobilalkalmazást, a jelszavuk alaphelyzetbe állítása korhasználhatják a mobilalkalmazásukat ellenőrzési módszerként.

A mobilalkalmazás-értesítések csak olyan szabályzat részeként kapcsolhatók be, amely két módszert igényel a jelszó alaphelyzetbe állításához.

---

## <a name="june-2018"></a>2018. június

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Értesítés módosítása: Biztonsági javítás az Azure AD-tevékenységnaplók API-t használó alkalmazások delegált engedélyezési folyamatához

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Az erősebb biztonsági kényszerítésnek köszönhetően módosítanunk kellett az [Azure AD-tevékenységnaplók API-jaihoz](https://aka.ms/aadreportsapi)delegált engedélyezési folyamatot használó alkalmazások engedélyeit. Ez a változás **2018.**

Ha bármelyik alkalmazás az Azure AD-tevékenységnapló API-kat használja, kövesse az alábbi lépéseket annak érdekében, hogy az alkalmazás ne szakadjon meg a módosítás után.

**Az alkalmazásengedélyek frissítése**

1. Jelentkezzen be az Azure Portalra, válassza az **Azure Active Directory**t, majd válassza az **Alkalmazásregisztrációk**lehetőséget.
2. Válassza ki az Azure AD tevékenységnaplók API-t használó alkalmazást, válassza a **Beállítások**lehetőséget, válassza a **Szükséges engedélyek**lehetőséget, majd válassza a **Windows Azure Active Directory** API-t.
3. A **Hozzáférés engedélyezése** panel **Dedemált engedélyek** területén jelölje be a **Címtáradatok olvasása** jelölőnégyzetét, majd kattintson a **Mentés gombra.**
4. Válassza **az Engedélyek megadása**lehetőséget, majd az **Igen**lehetőséget.
    
    >[!Note]
    >Az alkalmazás engedélyeinek megadásához globális rendszergazdának kell lennie.

További információkért tekintse meg az Azure AD jelentéskészítési API-cikk az előfeltételek hozzáférési terület engedélyek [megadása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) területen.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>TLS-beállítások konfigurálása az Azure AD-szolgáltatásokhoz való csatlakozáshoz a PCI DSS-megfelelőséghez

**Típus:** Új funkció  
**Szolgáltatás kategória:** N/a  
**Termék képesség:** Platform

A Transport Layer Security (TLS) egy olyan protokoll, amely két kommunikációs alkalmazás között biztosítja az adatvédelmet és az adatok integritását, és a ma használt legszélesebb körben alkalmazott biztonsági protokoll.

A [PCI Biztonsági Szabványügyi Tanács](https://www.pcisecuritystandards.org/) megállapította, hogy a TLS és a Secure Sockets Layer (SSL) korai verzióit le kell tiltani az új és biztonságosabb alkalmazásprotokollok engedélyezése érdekében, a megfelelőség **2018.** Ez a módosítás azt jelenti, hogy ha csatlakozik az Azure AD-szolgáltatásokhoz, és pci DSS-megfelelőséget igényel, le kell tiltania a TLS 1.0-s. A TLS több verziója is elérhető, de a TLS 1.2 az Azure Active Directory-szolgáltatások legújabb verziója. Javasoljuk, hogy közvetlenül a TLS 1.2-re költözzön az ügyfél/kiszolgáló és a böngésző/kiszolgáló kombinációk esetében.

Előfordulhat, hogy az elavult böngészők nem támogatják az újabb TLS-verziókat, például a TLS 1.2-t. Ha meg szeretné tudni, hogy a Böngésző támogatja a TLS mely verzióit, nyissa meg a [Qualys SSL Labs](https://www.ssllabs.com/) webhelyet, és kattintson **a Böngésző tesztelése**gombra. Javasoljuk, hogy frissítsen a böngésző legújabb verziójára, és lehetőleg csak a TLS 1.2-t engedélyezze.

**A TLS 1.2 engedélyezése böngésző szerint**

- **Microsoft Edge és Internet Explorer (mindkettő az Internet Explorer használatával van beállítva)**

    1. Nyissa meg az Internet Explorert, és válassza az**Internetbeállítások** > **– Speciális**eszközök **lehetőséget.** > 
    2. A **Biztonság** területen válassza a **TLS 1.2 parancsot,** majd az **OK**gombot.
    3. Zárja be az összes böngészőablakot, és indítsa újra az Internet Explorer t. 

- **Google Chrome**

    1. Nyissa meg a Google Chrome-ot, írja be *chrome://settings/* a címsorba, és nyomja le az **Enter billentyűt.**
    2. Bontsa ki a **Speciális** beállításokat, nyissa meg a **Rendszer** területet, és válassza **a Proxybeállítások megnyitása**lehetőséget .
    3. Az **Internet tulajdonságai** párbeszédpanelen válassza a **Speciális** lapot, lépjen a **Biztonság** területre, válassza a **TLS 1.2-es használata**lehetőséget, majd kattintson az **OK gombra.**
    4. Zárja be az összes böngészőablakot, és indítsa újra a Google Chrome-ot.

- **Mozilla Firefox**

    1. Nyissa meg a Firefoxot, írja be a *about:config billentyűt* a címsorba, majd nyomja **le az Enter billentyűt.**
    2. Keresse meg a *TLS*kifejezést, majd válassza ki a **security.tls.version.max** bejegyzést.
    3. Állítsa az **3** értéket 3-ra, hogy a böngésző a TLS 1.2-es verzióig használható legyen, majd kattintson az **OK gombra.**

        >[!NOTE]
        >A Firefox 60.0-s verziója támogatja a TLS 1.3-at, így a **4**security.tls.version.max értéket is beállíthatja 4-re.

    4. Zárja be az összes böngésző ablakot, és indítsa újra a Mozilla Firefoxot.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériában – 2018.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2018 júniusában hozzáadtuk ezt a 15 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Settling zene](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token engedélyezve LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint helyszíni](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakörben talál további információt. 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Az Azure AD password protection nyilvános előzetes verzióban érhető el

**Típus:** Új funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Felhasználói hitelesítés

Az Azure AD password protection használatával kiküszöbölheti a környezetből könnyen kitalálható jelszavakat. Ezeknek a jelszavaknak a megszüntetése segít csökkenteni a jelszóspray-típusú támadások feltörésének kockázatát.

Pontosabban az Azure AD password protection segít:

- A szervezet fiókjainak védelme az Azure AD és a Windows Server Active Directory (AD) szolgáltatásban egyaránt. 
- Megakadályozza, hogy a felhasználók jelszavakat használjanak a leggyakrabban használt jelszavak közül több mint 500-at tartalmazó listán, és több mint 1 millió karakterhelyettesítési változatot.
- Az Azure AD password protection felügyelete egyetlen helyről az Azure AD portálon, az Azure AD és a helyszíni Windows Server AD.

Az Azure AD password protection szolgáltatásról a [Rossz jelszavak kiküszöbölése a szervezetben](https://aka.ms/aadpasswordprotectiondocs)című témakörben talál további információt.

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Új "minden vendég" feltételes hozzáférés házirendsablon a használati feltételek létrehozása során

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Kormányzás

A használati feltételek létrehozása során egy új feltételes hozzáférés-házirendsablon is létrejön a "minden vendég" és az "összes alkalmazás" számára. Ez az új házirendsablon az újonnan létrehozott felhasználói felületet alkalmazza, egyszerűsítve a vendégek létrehozási és kényszerítési folyamatát.

További információ: [Azure Active Directory használati feltételek szolgáltatás.](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Új "egyéni" feltételes hozzáférésű házirendsablon a használati feltételek létrehozása során

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Kormányzás

A használati feltételek létrehozása során egy új "egyéni" feltételes hozzáférésű házirendsablon is létrejön. Ez az új házirendsablon lehetővé teszi a felhasználói felület létrehozását, majd azonnal lépjen a feltételes hozzáférés házirend-létrehozási panelre anélkül, hogy manuálisan kellene navigálnia a portálon.

További információ: [Azure Active Directory használati feltételek szolgáltatás.](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Új és átfogó útmutató az Azure többtényezős hitelesítés üzembe helyezéséhez

**Típus:** Új funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Identitásbiztonsági & védelem
 
Új, részletes útmutatást adtunk ki az Azure többtényezős hitelesítés (MFA) szervezetben történő üzembe helyezéséről.

Az MFA üzembe helyezési útmutatójának megtekintéséhez nyissa meg az [identitástelepítési útmutatótár](https://aka.ms/DeploymentPlans) a GitHubon. Ha visszajelzést szeretne küldeni a telepítési útmutatókról, használja a [Telepítési terv visszajelzési képernyőt.](https://aka.ms/deploymentplanfeedback) Ha bármilyen kérdése van a telepítési útmutatók, lépjen kapcsolatba velünk [iDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Az Azure AD delegált alkalmazásfelügyeleti szerepkörei nyilvános előzetes verzióban vannak

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Hozzáférés-vezérlés

A rendszergazdák mostantól delegálhatják az alkalmazáskezelési feladatokat a globális rendszergazdai szerepkör hozzárendelése nélkül. Az új szerepkörök és képességek a következők:

- **Új szabványos Azure AD-rendszergazdai szerepkörök:**

    - **Alkalmazás-rendszergazda.** Lehetővé teszi az összes alkalmazás összes aspektusának kezelését, beleértve a regisztrációt, az SSO-beállításokat, az alkalmazás-hozzárendeléseket és a licencelést, az alkalmazásproxy-beállításokat és a beleegyezést (kivéve az Azure AD-erőforrásokat).

    - **Felhőalkalmazás-rendszergazda.** Az alkalmazás-rendszergazdai képességek et biztosítja, kivéve az alkalmazásproxyt, mert nem biztosít helyszíni hozzáférést.

    - **Alkalmazásfejlesztő.** Lehetővé teszi az alkalmazásregisztrációk létrehozását, még akkor is, ha a felhasználók alkalmazások **regisztrálásának engedélyezése** beállítás ki van kapcsolva.

- **Tulajdonjog (alkalmazásonkénti regisztráció és vállalati alkalmazásonkénti beállítás, hasonlóan a csoport tulajdonosi folyamatához:**
 
    - **Alkalmazásregisztrációs tulajdonos.** Lehetővé teszi a tulajdonában lévő alkalmazásregisztráció minden aspektusának kezelését, beleértve az alkalmazásjegyzéket és további tulajdonosok hozzáadását.

    - **Vállalati alkalmazás tulajdonosa.** Lehetővé teszi a vállalati alkalmazások számos aspektusának kezelését, beleértve az SSO-beállításokat, az alkalmazás-hozzárendeléseket és a beleegyezést (kivéve az Azure AD-erőforrásokat).

A nyilvános előzetes verzióról további információt az [Azure AD delegált alkalmazásfelügyeleti szerepkörei nyilvános előzetes verzióban találhatók!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blog. A szerepkörökről és engedélyekről további információt a [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)

---

## <a name="may-2018"></a>2018. május

### <a name="expressroute-support-changes"></a>Az ExpressRoute támogatási módosításai

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Platform  

A szoftver, mint szolgáltatásajánlat, például az Azure Active Directory (Azure AD) úgy vannak kialakítva, hogy a legjobban működjenek az interneten keresztül, expressroute vagy más privát VPN-alagutak nélkül. **2018. augusztus 1-jén**leállítjuk az ExpressRoute azure-beli AD-szolgáltatások támogatásának támogatását az Azure nyilvános társviszony-létesítési és Azure-közösségei használatával a Microsoft-társviszony-létesítésben. A változás által érintett szolgáltatások észlelhetik, hogy az Azure AD-forgalom fokozatosan átvált az ExpressRoute-ról az internetre.

Miközben módosítjuk a támogatásunkat, azt is tudjuk, hogy még mindig vannak olyan helyzetek, amikor szükség lehet egy dedikált áramkörkészlet használatára a hitelesítési forgalomhoz. Emiatt az Azure AD továbbra is támogatja a bérlőnkénti IP-tartomány korlátozások at ExpressRoute és a Microsoft társviszony-létesítés az "Egyéb Office 365 Online-szolgáltatások" közösség használatával. Ha a szolgáltatások érintettek, de szüksége van az ExpressRoute-ra, a következőket kell tennie:

- **Ha az Azure nyilvános társviszony-létesítés.** Lépjen a Microsoft-társviszony-létesítési webhelyre, és regisztráljon az **Egyéb Office 365 Online-szolgáltatások (12076:5100)** közösségre. Az Azure nyilvános társviszony-létesítéséről a Microsoft-társviszony-létesítésre való áttérésről a [Nyilvános társviszony-létesítés áthelyezése a Microsoft-társviszony-létesítési](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) cikkben olvashat bővebben.

- **Ha microsoftos társviszony-létesítést használ.** Regisztráljon az **Egyéb Office 365 Online szolgáltatás (12076:5100)** közösségre. Az útválasztási követelményekről az ExpressRoute útválasztási követelmények ről szóló cikk [BGP-közösségek támogatása című részében](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) olvashat bővebben.

Ha továbbra is dedikált áramköröket kell használnia, meg kell beszélnie a Microsoft-fiók csapatával, hogyan kaphat engedélyt az **Egyéb Office 365 Online szolgáltatás (12076:5100)** közösség használatára. Az MS Office által felügyelt felülvizsgálati testület ellenőrzi, hogy szüksége van-e ezekre az áramkörökre, és győződjön meg róla, hogy megértette azok megtartásának technikai következményeit. Az Office 365-höz útvonalszűrőket létrehozni próbáló jogosulatlan előfizetések hibaüzenetet kapnak. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API-k a teljes jogú felhasználók felügyeleti forgatókönyvéhez

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Fejlesztői élmény
 
Az Azure AD használati feltételeinek adminisztrációs működéséhez hozzáadtuk a Microsoft Graph API-kat. A használati feltételeket objektumot hozhat létre, frissítheti, törölheti.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD több-bérlős végpont hozzáadása identitásszolgáltatóként az Azure AD B2C-ben

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C
 
Az egyéni szabályzatok használatával most már hozzáadhatja az Azure AD közös végpontot identitásszolgáltatóként az Azure AD B2C-ben. Ez lehetővé teszi, hogy egyetlen belépési pontot az összes Azure AD-felhasználók, amelyek bejelentkeznek az alkalmazásokba. További információ: [Azure Active Directory B2C: A felhasználók bejelentkezhetnek egy több-bérlős Azure AD-identitásszolgáltató egyéni szabályzatok használatával.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Belső URL-ek használatával bárhonnan elérheti az alkalmazásokat a Saját alkalmazások bejelentkezési bővítményünkkel és az Azure AD-alkalmazásproxyval

**Típus:** Új funkció  
**Szolgáltatás kategória:** Saját alkalmazások  
**Termék képesség:** Sso
 
A felhasználók mostantól belső URL-címeken keresztül is elérhetik az alkalmazásokat a vállalati hálózaton kívül, az Azure AD My Apps Secure bejelentkezési bővítményhasználatával. Ez minden olyan alkalmazással együtt működik, amelyet az Azure AD alkalmazásproxy használatával tett közzé, minden olyan böngészőben, amelyen telepítve van az Access Panel böngészőbővítménye is. Az URL-átirányítási funkció automatikusan engedélyezve lesz, amint a felhasználó bejelentkezik a bővítménybe. A kiterjesztés letölthető a [Microsoft Edge,](https://go.microsoft.com/fwlink/?linkid=845176) [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), és a [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – Adatok Európában az európai ügyfelek számára

**Típus:** Új funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** GoLocal között

Az európai ügyfelek nek szükségük van arra, hogy adataik Európában maradjanak, és ne replikáljanak az európai adatközpontokon kívül, hogy megfeleljenek az adatvédelmi és európai jogszabályoknak. Ez a [cikk](https://go.microsoft.com/fwlink/?linkid=872328) részletesen ismerteti, hogy milyen identitásadatokat fog tárolni Európában, valamint részletes információkat, amelyeket az európai adatközpontokon kívül tárolnak. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Új felhasználói kiépítési SaaS-alkalmazások integrációi – 2018 május

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termékképesség:** harmadik fél integrációja
 
Az Azure AD lehetővé teszi a felhasználói identitások létrehozását, karbantartását és eltávolítását a SaaS-alkalmazásokban, például a Dropboxban, a Salesforce-ban, a ServiceNow-ban és egyebekben. 2018 májusában az Azure AD alkalmazásgalériában a következő alkalmazások felhasználói kiépítési támogatását adtuk hozzá:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Az Azure AD-katalógusban a felhasználók kiépítést támogató [https://aka.ms/appstutorial](https://aka.ms/appstutorial)alkalmazások listáját a.

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>A csoportok Azure AD-hozzáférés-felülvizsgálata és az alkalmazás-hozzáférés most ismétlődő értékeléseket biztosít

**Típus:** Új funkció  
**Szolgáltatás kategória:** Access vélemények  
**Termék képesség:** Kormányzás
 
A csoportok és alkalmazások access-felülvizsgálata már általánosan elérhető az Azure AD Premium P2 részeként.  A rendszergazdák beállíthatják a csoporttagságok és az alkalmazás-hozzárendelések hozzáférési felülvizsgálatát, hogy rendszeres időközönként, például havonta vagy negyedévente automatikusan ismétlődjenek.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Az Azure AD-tevékenységnaplók (bejelentkezések és naplózás) már elérhetők az MS Graph-on keresztül

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése
 
Az Azure AD-tevékenységnaplók, amelyek bejelentkezéseket és naplózási naplókat tartalmaznak, már elérhetők a Microsoft Graph API-n keresztül. A Microsoft Graph API-n keresztül két végpontot tettünk elérhetővé a naplók eléréséhez. Az első lépésekhez tekintse meg [dokumentumainkat](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) az Azure AD reporting API-khoz való programozott hozzáférésért. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>A B2B megváltási élmény ének javítása és a szervezet

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C

**Éppen időben megváltás:** Miután megosztott egy erőforrást egy vendégfelhasználóval a B2B API használatával – nem kell külön meghívó e-mailt küldenie. A legtöbb esetben a vendégfelhasználó hozzáférhet az erőforráshoz, és a beváltási élményéppen időben kerül át. Nincs több hatás a kihagyott e-mailek miatt. Nincs több kérdezés -a vendég használók " ön csattanó -ra amit megváltás láncszem a rendszer küldött ön?". Ez azt jelenti, hogy ha az SPO a meghíváskezelőt használja – a felhős mellékletek a beváltás bármely állapotában minden felhasználó számára ugyanazt a kanonikus URL-t kaphatják – belső és külső .

**Modern megváltási élmény:** Nincs több osztott képernyőbeváltási céloldal. A felhasználók a meghívó szervezet adatvédelmi nyilatkozatával együtt modern hozzájárulási élményt fognak látni, ugyanúgy, mint a harmadik féltől származó alkalmazások esetében.

**A vendégfelhasználók elhagyhatják a szervezet:** Ha a felhasználó kapcsolata egy szervezettel véget ért, önkiszolgálóan távozhat a szervezettől. Nincs több hívás a meghívó szervezet admin ,, hogy "el kell távolítani", nincs több emelése támogatási jegyeket.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériában – 2018 májusa

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2018 májusában hozzáadtuk ezt a 18 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériánkhoz:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[啦ンンンンンンンンンンン](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Vélemények](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál.

Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakörben talál további információt.

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Új, részletes telepítési útmutatók az Azure Active Directoryhoz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Taglista
 
Új, lépésenkénti útmutató az Azure Active Directory (Azure AD) telepítéséről, beleértve az önkiszolgáló jelszó-visszaállítást (SSPR), az egyszeri bejelentkezést (SSO), a feltételes hozzáférést (CA), az alkalmazásproxyt, a felhasználói kiépítést, az Active Directory-összevonási szolgáltatásokat (ADFS) az átmenő hitelesítésre (PTA) és az ADFS jelszó-kivonatra (PHS).

A telepítési útmutatók megtekintéséhez nyissa meg az [identitás-telepítési útmutatók](https://aka.ms/DeploymentPlans) tártára a GitHubon. Ha visszajelzést szeretne küldeni a telepítési útmutatókról, használja a [Telepítési terv visszajelzési képernyőt.](https://aka.ms/deploymentplanfeedback) Ha bármilyen kérdése van a telepítési útmutatók, lépjen kapcsolatba velünk [iDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vállalati alkalmazások keresése – További alkalmazások betöltése

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso
 
Problémái vannak az alkalmazások / szolgáltatástagok megtalálásával? Több alkalmazást is betölthetünk a vállalati alkalmazások listájára az összes alkalmazáslistában. Alapértelmezés szerint 20 alkalmazást jelenítünk meg. Most már kattintson, **Betöltése több** megtekintéséhez további alkalmazásokat. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>A Május mentesít -ból AADConnect tartalmaz egy nyilvános sajtóbemutató -ból egységbe rendezés -val PingFederate, fontos biztonság korszerűsít, sok hiba megalszik, és új nagy új hibaelhárítás szerszámok. 

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** AD-csatlakozás  
**Termék képesség:** Identitáséletciklus-kezelés
 
A Május mentesít -ból AADConnect tartalmaz egy nyilvános sajtóbemutató -ból egységbe rendezés -val PingFederate, fontos biztonság korszerűsít, sok hiba megalszik, és új nagy új hibaelhárítás szerszámok. A kibocsátási megjegyzéseket [itt](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)találja.

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-hozzáférési vélemények: automatikus alkalmazás

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Access vélemények  
**Termék képesség:** Kormányzás

A csoportok és alkalmazások hozzáférési felülvizsgálatai már általánosan elérhetők az Azure AD Premium P2 részeként. A rendszergazda beállíthatja, hogy a hozzáférés-ellenőrzés befejezésekor automatikusan alkalmazza a véleményező módosításait az adott csoportra vagy alkalmazásra. A rendszergazda azt is megadhatja, hogy mi történjen a felhasználó folyamatos hozzáférésével, ha az ellenőrzők nem válaszoltak, nem távolítják el a hozzáférést, nem tartják meg a hozzáférést, vagy nem fogadnak el rendszerjavaslatokat. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Az azonosító jogkivonatokat már nem lehet visszaadni az új alkalmazások lekérdezési response_mode használatával. 

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
2018. április 25-én vagy azt követően létrehozott alkalmazások a továbbiakban nem kérhetnek **id_token** a **lekérdezés i.** response_mode használatával.  Ez hozza az Azure AD inline az OIDC specifikációk, és segít csökkenteni az alkalmazások támadási felületét.  a 201 **id_token**response_type **response_mode8.**  A visszaadott hiba, amikor id_token kér az AAD-től, **AADSTS70007: a "lekérdezés" nem támogatott "response_mode" érték, amikor jogkivonatot kér.**

A **töredék** és **form_post** response_modes továbbra is működik - új alkalmazásobjektumok létrehozásakor (például az alkalmazásproxy használata), biztosítja az egyik ilyen response_modes, mielőtt új alkalmazást hozna létre.  

---
 
## <a name="april-2018"></a>2018. április 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Az Azure AD B2C hozzáférési jogkivonat ga

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C 

Most már elérheti az Azure AD B2C által védett webes API-kat hozzáférési jogkivonatok használatával. A funkció a nyilvános előzetes verzióról a GA-ra kerül. Az Azure AD B2C-alkalmazások és webes API-k konfigurálásához szükséges felhasználói felületi élmény tovább lett fejlesztve, és egyéb kisebb fejlesztések történtek.
 
További információ: [Azure AD B2C: Access tokenek kérése.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Egyszeri bejelentkezési konfiguráció tesztelése SAML-alapú alkalmazásokhoz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

Saml-alapú egyszeri szolgáltatás alkalmazások konfigurálásakor tesztelheti az integrációt a konfigurációs lapon. Ha a bejelentkezés során hibát észlel, a tesztelési élményben adhatja meg a hibát, és az Azure AD megoldási lépéseket biztosít az adott probléma megoldásához.

További információkért lásd:

- [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Saml-alapú egyszeri bejelentkezés az Azure Active Directoryban lévő alkalmazásokba való hibakeresés](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Az Azure AD használati feltételei most már felhasználónkénti jelentéskészítéssel rendelkeznek

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Megfelelés
 
A rendszergazdák most antól kiválaszthatják az adott felhasználólehetőséget, és megtekinthetik az összes olyan felhasználót, aki hozzájárult az adott kezdőfelhasználóhoz, és hogy milyen dátum/idő történt.

További információt az [Azure AD használati feltételei szolgáltatásban talál.](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect állapota: Kockázatos IP az AD FS extranet zárolás elleni védelemhez 

**Típus:** Új funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** A & jelentés ének nyomon követése

A Connect Health mostantól támogatja a sikertelen U/P bejelentkezések küszöbértékét óránként vagy naponta túllépő IP-címek észlelését. A szolgáltatás által biztosított képességek a következők:

- Átfogó jelentés, amely bemutatja az IP-címet és az óránként/naponta létrehozott sikertelen bejelentkezések számát a testreszabható küszöbértékkel.
- E-mail alapú riasztások, amelyek azt mutatják, hogy egy adott IP-cím óránként/naponta túllépte-e a sikertelen U/P bejelentkezések küszöbértékét.
- A letöltési lehetőség, hogy csinál egy részletes elemzést az adatok

További információ: [Kockázatos IP-jelentés.](https://aka.ms/aadchriskyip)

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Egyszerű alkalmazáskonfiguráció metaadatfájllal vagy URL-címmel

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

A Vállalati alkalmazások lapon a rendszergazdák feltölthetnek egy SAML metaadatfájlt az AAD-tár és a nem galéria alkalmazás SAML alapú bejelentkezésének konfigurálásához.

Emellett használhatja az Azure AD-alkalmazás összevonásának metaadat-URL-cím beállításához sso a célzott alkalmazással.

További [információ: Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokba.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Az Azure AD használati feltételei már általánosan elérhetők

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Megfelelés
 

Az Azure AD használati feltételei a nyilvános előzetes verzióról az általánosan elérhetőre kerültek.

További információt az [Azure AD használati feltételei szolgáltatásban talál.](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Meghatározott B2B-felhasználóktól érkező meghívások engedélyezése vagy letiltása

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C
 

Most már megadhatja, hogy mely partnerszervezeteket szeretné megosztani és együttműködni az Azure AD B2B együttműködésben. Ehhez létrehozhatja az adott engedélyezett vagy megtagadási tartományok listáját. Ha egy tartomány le van tiltva ezekkel a képességekkel, az alkalmazottak már nem küldhetnek meghívókat az adott tartományban lévő személyeknek.

Ez segít az erőforrásokhoz való hozzáférés szabályozásával, miközben zökkenőmentes felhasználói élményt biztosít a jóváhagyott felhasználók számára.

Ez a B2B együttműködési funkció minden Azure Active Directory-ügyfél számára elérhető, és az Azure AD Premium-funkciókkal, például a feltételes hozzáféréssel és az identitásvédelemmel együtt használható a külső üzleti felhasználók bejelentkezésének és elérésének részletesebb szabályozásához.

További információt az [Adott szervezetek B2B-felhasználóinak szóló meghívók engedélyezése vagy letiltása című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazásgalériában

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2018 áprilisában hozzáadtuk ezt a 13 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériánkhoz:

Kritérium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (Helyszíni)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), Dinamikus [jel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), Cisco [Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Polc, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál.

Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakörben talál további információt.

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>B2B-felhasználók nak az Azure AD-ben való hozzáférés megadása a helyszíni alkalmazásokhoz (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C

Az Azure Active Directory (Azure AD) B2B együttműködési képességeit használó szervezetként a partnerszervezetek vendégfelhasználóit az Azure AD-be hívhatja meg, most már hozzáférést biztosíthat ezeknek a B2B-felhasználóknak a helyszíni alkalmazásokhoz. Ezek a helyszíni alkalmazások saml-alapú hitelesítést vagy integrált Windows-hitelesítést (IWA) használhatnak a Kerberos korlátozott delegálásával (KCD).

További információ: [B2B-felhasználók megadása az Azure AD-ben a helyszíni alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)való hozzáférés.

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>SSO-integrációs oktatóanyagok beszereznie az Azure Piactérről

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Más  
**Termékképesség:** harmadik fél integrációja

Ha egy alkalmazás, amely szerepel az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) támogatja az SAML alapú egyszeri bejelentkezés, kattintson a **Beszerezni most** biztosít önnek az alkalmazáshoz társított integrációs oktatóanyag. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Az Azure AD automatikus felhasználói kiépítésének gyorsabb teljesítménye saas-alkalmazásokba

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termékképesség:** harmadik fél integrációja
 
Korábban az Azure Active Directory-felhasználók üzembe helyezési összekötőit használó ügyfelek (például salesforce, ServiceNow és Box) lassú teljesítményt tapasztalhatnak, ha az Azure AD-bérlők több mint 100 000 kombinált felhasználót és csoportot tartalmaznak, és felhasználói és csoport-hozzárendeléseket használtak annak meghatározására, hogy mely felhasználókat kell kiépíteni.

2018. április 2-án jelentős teljesítménybeli fejlesztéseket telepítettek az Azure AD-kiépítési szolgáltatásba, amelyek jelentősen csökkentik az Azure Active Directory és a cél SaaS-alkalmazások közötti kezdeti szinkronizálások elvégzéséhez szükséges időt.

Ennek eredményeképpen sok olyan ügyfél, amely nek kezdeti szinkronizálása olyan alkalmazásokkal történt, amelyek sok napot vettek igénybe, vagy soha nem fejeződtek be, perceken vagy órákon belül befejeződnek.

További információ: [Mi történik a kiépítés során?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Önkiszolgáló jelszó-visszaállítás a Windows 10 zárolási képernyőjéről hibrid Azure AD-hez csatlakozó gépek esetén

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Önkiszolgáló jelszó alaphelyzetbe állítása  
**Termék képesség:** Felhasználói hitelesítés
 
Frissítettük a Windows 10 SSPR funkciót, hogy tartalmazza a hibrid Azure AD-hez csatlakozott gépek támogatását. Ez a funkció elérhető a Windows 10 RS4 lehetővé teszi a felhasználók számára, hogy állítsa vissza a jelszót a zár képernyőn a Windows 10 gép. Az önkiszolgáló jelszó-visszaállításra regisztrált és regisztrált felhasználók használhatják ezt a funkciót.

További információ: [Azure AD password reset from the login screen](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>2018. március
 
### <a name="certificate-expire-notification"></a>A tanúsítvány lejár értesítése

**Típus:** Rögzített  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso
 
Az Azure AD értesítést küld, ha egy katalógus vagy nem katalógus alkalmazás tanúsítványa hamarosan lejár. 

Egyes felhasználók nem kaptak értesítést az SAML-alapú egyszeri bejelentkezéshez konfigurált vállalati alkalmazásokról. A probléma megoldódott. Az Azure AD értesítést küld a 7, 30 és 60 napon belül lejáró tanúsítványokról. Ezt az eseményt a naplókban láthatja. 

További információkért lásd:

- [Tanúsítványok kezelése az összevont egyszeri bejelentkezéshez az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter- és GitHub-identitásszolgáltatók az Azure AD B2C-ben

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C
 
Most már hozzáadhatja a Twittert vagy a GitHubot identitásszolgáltatóként az Azure AD B2C-ben. Twitter mozog a nyilvános előnézet a GA. A GitHub nyilvános előzetes verzióban jelenik meg.

További információ: [Mi az Azure AD B2B együttműködés?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Böngészőhozzáférés korlátozása az Intune felügyelt böngészőjével az Azure AD alkalmazásalapú feltételes hozzáféréssel iOS és Android rendszeren

**Típus:** Új funkció  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Identitásbiztonsági & védelem
 
**Most nyilvános előnézetben!**

**Intune felügyelt böngésző-sso-ja:** Az alkalmazottak egyetlen bejelentkezést használhatnak a natív ügyfelek (például a Microsoft Outlook) és az Intune felügyelt böngészője között az összes Azure AD-hez kapcsolódó alkalmazáshoz.

**Az Intune felügyelt böngésző feltételes hozzáférésének támogatása:** Most már megkövetelheti az alkalmazottaktól, hogy az Intune felügyelt böngészőjét használják alkalmazásalapú feltételes hozzáférési házirendek használatával.

Erről bővebben blogbejegyzésünkben [olvashat.](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)

További információkért lásd:

- [Alkalmazásalapú feltételes hozzáférés beállítása](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Felügyelt böngészőházirendek konfigurálása](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Alkalmazásproxy-parancsmagok a PowerShell GA-modulban

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Hozzáférés-vezérlés
 
Az alkalmazásproxy-parancsmagok támogatása most már a PowerShell GA modulban található! Ez megköveteli, hogy naprakész maradjon a PowerShell-modulok - ha lesz több mint egy évvel lemaradva, egyes parancsmagok leállhat. 

További információ: [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Az Office 365 natív ügyfeleit a zökkenőmentes egyszeri bejelentkezés nem interaktív protokollhasználatával támogatja

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
Az Office 365 natív ügyfeleit (16.0.8730.xxxx vagy újabb verzió) használó felhasználó csendes bejelentkezési élményt élvez a zökkenőmentes egyszeri bejelentkezés használatával. Ezt a támogatást egy nem interaktív protokoll (WS-Trust) hozzáadása biztosítja az Azure AD-hez.

További információ: [Hogyan működik a bejelentkezés egy natív ügyfélzökkenőmentes egyszeri bejelentkezéssel?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>A felhasználók csendes bejelentkezési élményt kapnak a zökkenőmentes egyszeri bejelentkezéssel, ha egy alkalmazás bejelentkezési kérelmeket küld az Azure AD bérlői végpontjainak

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
A `https://contoso.sharepoint.com`felhasználók csendes bejelentkezési élményt kapnak a zökkenőmentes egyszeri bejelentkezéssel, ha egy alkalmazás (például ) bejelentkezési kérelmeket küld az Azure AD bérlői végpontjainak – azaz `https://login.microsoftonline.com/contoso.com/<..>` vagy `https://login.microsoftonline.com/<tenant_ID>/<..>` - az Azure AD közös végpontja (`https://login.microsoftonline.com/common/<...>`helyett).

További információt az [Azure Active Directory zökkenőmentes egyszeri bejelentkezés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>A zökkenőmentes egyszeri bejelentkezés bevezetéséhez a felhasználók intranetzóna-beállításaihelyett csak egy Azure AD-URL-címet kell hozzáadnia a felhasználók intranetes zónabeállításaihelyett.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
A zökkenőmentes egyszeri bejelentkezés felhasználók számára történő bevezetéséhez csak egy Azure AD URL-címet kell hozzáadnia `https://autologon.microsoftazuread-sso.com`a felhasználók intranetes zónabeállításaihoz az Active Directory csoportházirendjének használatával: . Korábban az ügyfeleknek két URL-t kellett hozzáadniuk.

További információt az [Azure Active Directory zökkenőmentes egyszeri bejelentkezés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD alkalmazásgalériában elérhető új összevont alkalmazások

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2018 márciusában hozzáadtuk ezt a 15 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériánkhoz:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitúdó](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál.

Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakörben talál további információt. 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>A PIM for Azure Resources általánosan elérhető

**Típus:** Új funkció  
**Szolgáltatás kategória:** Kiemelt identitáskezelés  
**Termék képesség:** Kiemelt identitáskezelés
 
Ha az Azure AD emelt szintű identitáskezelés címtárszerepkörök, most már használhatja a PIM időhöz kötött hozzáférési és hozzárendelési képességek az Azure Resource-szerepkörök, például előfizetések, erőforráscsoportok, virtuális gépek, és bármely más erőforrás által támogatott Azure Resource Manager. Többtényezős hitelesítés kényszerítése a just-in-time szerepkörök aktiválásakor, és az aktiválások ütemezése a jóváhagyott módosítási ablakokkal együttműködésben. Emellett ez a kiadás olyan fejlesztéseket is hozzáad, amelyek nem érhetők el a nyilvános előzetes verzióban, beleértve a frissített felhasználói felületet, a jóváhagyási munkafolyamatokat, valamint a hamarosan lejáró szerepkörök meghosszabbítását és a lejárt szerepkörök megújítását.

További információ: [PIM for Azure resources (Előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Választható jogcímek hozzáadása az alkalmazások tokenjeihez (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
Az Azure AD-alkalmazás mostantól egyéni vagy választható jogcímeket kérhet jwt-kben vagy SAML-jogkivonatokban.  Ezek a felhasználóval vagy bérlővel kapcsolatos jogcímek, amelyek alapértelmezés szerint nem szerepelnek a jogkivonatban a méret vagy az alkalmazhatósági megkötések miatt.  Ez jelenleg nyilvános előzetes verzióban az Azure AD-alkalmazások a v1.0 és a 2.0-s végpontok.  Tekintse meg a dokumentációt arról, hogy milyen jogcímek adhatók hozzá, és hogyan szerkesztheti az alkalmazásjegyzéket a kéréshez.  

További információ: [Választható jogcímek az Azure AD-ben.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Az Azure AD támogatja a PKCE-t a biztonságosabb OAuth-folyamatok érdekében

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
Az Azure AD-dokumentumok frissültek, hogy vegye figyelembe a PKCE támogatását, amely biztonságosabb kommunikációt tesz lehetővé az OAuth 2.0 engedélyezési kód engedélyezési folyamata során.  Az S256 és az egyszerű szöveges code_challenges egyaránt támogatottak az 1.0-s és a 2.0-s és a 2.0-s végpontokon. 

További információt az [Engedélyezési kód kérése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code)című témakörben talál. 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>A Workday Get_Workers API-ban elérhető összes felhasználói attribútumérték kiépítésének támogatása

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termékképesség:** harmadik fél integrációja
 
A Workday és az Active Directory és az Azure AD bejövő kiépítési verziója mostantól támogatja a Workday Get_Workers API-ban elérhető összes attribútumérték kibontását és kiépítését. Ez több száz további szabványos és egyéni attribútumot ad hozzá a Workday bejövő létesítési összekötő kezdeti verziójával szállítottakon túl.

További információ: [A Workday felhasználói attribútumainak testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>A csoporttagság dinamikusról statikusra váltása, és fordítva

**Típus:** Új funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés
 
Lehetőség van a tagság kezelésének módosítására egy csoportban. Ez akkor hasznos, ha ugyanazt a csoportnevet és azonosítót szeretné megtartani a rendszerben, így a csoportra mutató meglévő hivatkozások továbbra is érvényesek; egy új csoport létrehozása esetén frissíteni kell ezeket a hivatkozásokat.
Frissítettük az Azure AD Felügyeleti központot, hogy támogassa ezt a funkciót. Mostantól az ügyfelek a meglévő csoportokat dinamikus tagságból hozzárendelt tagsággá alakíthatják át, és fordítva. A meglévő PowerShell-parancsmagok is elérhetők.

További információ: [Dinamikus tagsági szabályok csoportokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Továbbfejlesztett kijelentkezési viselkedés a zökkenőmentes egyszeri bejelentkezéssel

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
Korábban, még akkor is, ha a felhasználók kifejezetten kijelentkezett egy alkalmazás által védett Azure AD, akkor automatikusan újra bejelentkezik a zökkenőmentes egyszeri bejelentkezés, ha megpróbálnak hozzáférni egy Azure AD-alkalmazás ismét a corpnet a tartományhoz csatlakozott eszközök. Ezzel a módosítással a kijelentkezés támogatott.  Ez lehetővé teszi a felhasználók számára, hogy ugyanazt vagy a különböző Azure AD-fiókot válasszanak, amelyekkel újra bejelentkeznek, ahelyett, hogy automatikusan bejelentkeznek a zökkenőmentes egyszeri bejelentkezés használatával.

További információ: [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Alkalmazásproxy-összekötő 1.5.402.0-s verziója

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Identitásbiztonsági & védelem
 
Ez az összekötő verzió fokozatosan gördült ki novemberben. Ez az új összekötő verzió a következő módosításokat tartalmazza:

- Az összekötő mostantól tartományszintű cookie-kat állít be az altartomány szintjén. Ez biztosítja a zökkenőmentesebb SSO-élményt, és elkerüli a redundáns hitelesítési utasításokat.
- Darabolt kódolási kérelmek támogatása
- Továbbfejlesztett összekötő állapotfigyelés 
- Számos hibajavítás és stabilitási fejlesztés

További információ: [Az Azure AD alkalmazásproxy-összekötők megismerése.](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
 
---

## <a name="february-2018"></a>2018. február
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Továbbfejlesztett navigáció a felhasználók és csoportok kezeléséhez

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Címtár kezelése  
**Termék képesség:** Taglista

A felhasználók és csoportok kezeléséhez a navigációs élmény egyszerűsödött. Most már közvetlenül a könyvtár áttekintéséről az összes felhasználó listájára navigálhat, így könnyebben hozzáférhet a törölt felhasználók listájához. A címtár áttekintéséről közvetlenül az összes csoport listájához is navigálhat, és könnyebben hozzáférhet a csoportkezelési beállításokhoz. A címtár áttekintő lapján is kereshet felhasználót, csoportot, vállalati alkalmazást vagy alkalmazásregisztrációt. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>A 21Vianet (Azure China 21Vianet) által üzemeltetett Microsoft Azure-ban való bejelentkezések és naplózási jelentések elérhetősége

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure-verem  
**Termék képesség:** A & jelentés ének nyomon követése

Az Azure AD-tevékenységnapló-jelentések már elérhetők a 21Vianet (Azure China 21Vianet) példányok által üzemeltetett Microsoft Azure-ban. A következő naplók szerepelnek:

- **Bejelentkezési tevékenységnaplók** – tartalmazza a bérlőhöz társított összes bejelentkezési bejelentkezési bejelentkezést.

- **Önkiszolgáló jelszónapló –** tartalmazza az összes SSPR-naplót.

- **Címtárkezelési naplózási naplók** – tartalmazza az összes címtárkezeléssel kapcsolatos naplót, például a Felhasználókezelést, az Alkalmazáskezelést és másokat.

Ezekkel a naplókkal betekintést nyerhet a környezet működésébe. A megadott adatokkal a következőket teheti:

- Határozza meg, hogy az alkalmazásokat és szolgáltatásokat hogyan használják a felhasználók.

- Hárítsa el azolyan problémákat, amelyek megakadályozzák, hogy a felhasználók elvégezzék a munkájukat.

A jelentések használatáról az Azure [Active Directory jelentéskészítéscímű témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)talál további információt.

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Az Azure AD-tevékenységjelentések megtekintéséhez használja a "Jelentésolvasó" szerepkört (nem rendszergazdai szerepkört).

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése

Az ügyfelek visszajelzéseinek részeként, amelyek lehetővé teszik a nem rendszergazdai szerepkörök számára az Azure AD-tevékenységnaplókhoz való hozzáférést, lehetővé tettük a "Jelentésolvasó" szerepkörben lévő felhasználók számára a bejelentkezések és a naplózási tevékenységek elérését az Azure Portalon belül, valamint a Microsoft Graph API használatát. 

A jelentések használatáról az Azure [Active Directory jelentéskészítés című témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)talál további információt. 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Az Alkalmazottazonosító jogcím felhasználói attribútumként és felhasználói azonosítóként érhető el

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso
 
**Az EmployeeID-t** a vállalati alkalmazás felhasználói felületéről származó SAML-alapú bejelentkezési alkalmazások ban a tagfelhasználók és a B2B-vendégek felhasználói azonosítójaként és felhasználói attribútumaként konfigurálhatja.

További információ: [Az Azure Active Directory saml-tokenben kiállított jogcímek testreszabása nagyvállalati alkalmazásokhoz című témakörben található.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Egyszerűsített alkalmazáskezelés helyettesítő karakterek használatával az Azure AD alkalmazásproxyban

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Felhasználói hitelesítés
 
Az alkalmazások üzembe helyezésének megkönnyítése és a felügyeleti terhelés csökkentése érdekében mostantól támogatjuk az alkalmazások helyettesítő karakterek használatával történő közzétételét. Helyettesítő karakteres alkalmazás közzétételéhez kövesse a szabványos alkalmazásközzétételi folyamatot, de használjon helyettesítő karaktert a belső és külső URL-címekben.

További információ: [Helyettesítő alkalmazások az Azure Active Directory alkalmazásproxyjában](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Új parancsmagok az alkalmazásproxy konfigurációjának támogatásához

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Platform

Az AzureAD PowerShell előzetes modul legújabb kiadása új parancsmagokat tartalmaz, amelyek lehetővé teszik az ügyfelek számára az alkalmazásproxy-alkalmazások konfigurálását a PowerShell használatával.

Az új parancsmagok a következők: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberof
- Új-AzureADApplicationProxyapplication
- Új-AzureADApplicationProxyConnectorGroup
- Eltávolítás-AzureADApplicationProxyApplication
- Eltávolítás-AzureADApplicationProxyApplicationConnectorGroup
- Eltávolítás-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignon
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Új parancsmagok a csoportok konfigurációjának támogatásához

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Platform

Az AzureAD PowerShell-modul legújabb kiadása az Azure AD-ben lévő csoportok kezeléséhez parancsmagokat tartalmaz. Ezek a parancsmagok korábban elérhetők voltak az AzureADPreview modulban, és most már hozzá adódanak az AzureAD modulhoz

Az általános elérhetőséghez kiadott csoportparancsmagok a következők: 

- Get-AzureADMSGroup
- Új-AzureADMSGroup
- Az AzureADMS-csoport eltávolítása
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- Új-AzureADMSGroupLifecyclePolicy
- Eltávolítás-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSÉletciklus-alapúszakpolitikai csoport
- Eltávolítás-AzureADMSÉletciklus-alapú házirendcsoport
- Alaphelyzetbe állítás-AzureADMSéletciklus-csoport   
- Get-AzureADMSÉletciklus-alapú házirendcsoport

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Elérhető az Azure AD Connect új kiadása

**Típus:** Új funkció  
**Szolgáltatás kategória:** AD-szinkronizálás  
**Termék képesség:** Platform
 
Az Azure AD Connect az azure AD és a helyszíni adatforrások, például a Windows Server Active Directory és az LDAP közötti adatok szinkronizálásának előnyben részesített eszköze.

>[!Important]
>Ez a build séma- és szinkronizálási szabálymódosításokat vezet be. Az Azure AD Connect szinkronizálási szolgáltatás elindítja a teljes importálási és teljes szinkronizálási lépéseket a frissítés után. A viselkedés módosításáról a [Teljes szinkronizálás elhalasztása frissítés után](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade)című témakörben talál további információt.

Ez a kiadás a következő frissítéseket és módosításokat tartalmaz:

**Hibák kijavítva:**

- A következő oldalra való váltáskor javítsa ki az időzítési ablakot a partíciószűrés lapjának háttérfeladatain.

- Kijavítottunk egy hibát, amely az Access megsértését okozta a ConfigDB egyéni művelet során.

- Javítva egy hiba, hogy visszaszerezze az SQL-kapcsolat időtúllépés.

- Javítva egy hiba, amely miatt a SAN helyettesítő karakterekkel rendelkező tanúsítványok nem tudtak megnem a req előtti ellenőrzésen.

- Kijavítottunk egy hibát, amely a miiserver.exe összeomlását okozza az AAD-összekötő exportálása során.

- Javítva egy hiba, amely miatt az AAD-csatlakozás varázsló konfigurációját rossz jelszókísérlet miatt jelentkezett be a DC-be.

**Új funkciók és fejlesztések**
 
- Alkalmazástelemetria – A rendszergazdák be- és kikapcsolhatják ezt az adatosztályt.

- Azure AD-állapot adatai – A rendszergazdáknak meg kell látogatniuk az állapotkezelési portált az állapotbeállítások szabályozásához. A szolgáltatásházirend módosítása után az ügynökök elolvassák és kényszerítik azt.

- Eszközvisszaírási konfigurációs műveletek et és folyamatjelző sávot adott hozzá az oldal inicializálásához.

- Továbbfejlesztett általános diagnosztika HTML-jelentéssel és teljes adatgyűjtéssel zip-text / HTML jelentésben.

- Az automatikus frissítés megbízhatóságának javítása és további telemetriai adatok hozzáadása a kiszolgáló állapotának meghatározásához.

- Korlátozza az AD Connector-fiók kiemelt jogosultságú fiókjaiszámára elérhető engedélyeket. Új telepítések esetén a varázsló korlátozza az MSOL-fiókhoz szükséges jogosultságokkal rendelkező fiókok engedélyeit az MSOL-fiók létrehozása után. A módosítások az automatikus létrehozási fiókkal rendelkező expressz és egyéni telepítéseket érintik.

- Az AADConnect tiszta telepítésekor módosította a telepítőt, hogy az AADConnect tiszta telepítésekor ne legyen szükség SA-jogosultságra.

- Egy adott objektum szinkronizálási problémáinak elhárítására szolgáló új segédprogram. Jelenleg a segédprogram a következő dolgokat ellenőrzi:

    - UserPrincipalName eltérés a szinkronizált felhasználói objektum és a felhasználói fiók az Azure AD tenant.
  
    - Ha az objektumot tartományszűrés miatt szűrik a szinkronizálásból
  
    - Ha az objektumot a szervezeti egység (OU) szűrése miatt szűrik a szinkronizálásról

- Új segédprogram a helyszíni Active Directoryban egy adott felhasználói fiókhoz tárolt aktuális jelszókivonat szinkronizálására. A segédprogram nem igényel jelszómódosítást. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Az Azure AD-alkalmazásalapú feltételes hozzáféréshez hozzáadott Intune-alkalmazásvédelmi szabályzatokat támogató alkalmazások

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Identitásbiztonsági & védelem

További alkalmazásokkal bővült, amelyek támogatják az alkalmazásalapú feltételes hozzáférést. Mostantól hozzáférhet az Office 365-höz és más Azure AD-hez kapcsolódó felhőalkalmazásokhoz ezekkel a jóváhagyott ügyfélalkalmazásokkal.

A következő pályázatok február végéig kerülnek hozzáadásra:

- Microsoft Power BI

- Microsoft indítórakéta

- Microsoft számlázás

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD-alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>A mobilélmény használati feltételeinek frissítése 

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Megfelelés

Amikor megjelennek a használati feltételek, most már a **Megtekintési problémák miatt kattinthat? Kattintson ide**. Erre a hivatkozásra kattintva natív módon megnyílik a használati feltételek az eszközön. A dokumentum betűméretétől vagy az eszköz képernyőméretétől függetlenül szükség szerint nagyíthatja és elolvashatja a dokumentumot. 

---
 
## <a name="january-2018"></a>2018. január
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD alkalmazásgalériában elérhető új összevont alkalmazások 

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2018 januárjában a következő, összevonási támogatással rendelkező új alkalmazások kerültek be az alkalmazásgalériába:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory és [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál.

Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakörben talál további információt. 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Bejelentkezés további észlelt kockázattal

**Típus:** Új funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

Az észlelt kockázatészleléshez betekintés az Azure AD-előfizetéshez van kötve. Az Azure AD Premium P2 kiadás, a legrészletesebb információkat az összes alapul szolgáló észlelések.

Az Azure AD Premium P1 kiadás, észlelések, amelyek nem tartoznak a licenc jelenik meg a kockázatészlelés i d.

További információ: [Azure Active Directory-kockázatészlelések.](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Az Office 365-alkalmazások elrejtése a végfelhasználó hozzáférési paneljei elől

**Típus:** Új funkció  
**Szolgáltatás kategória:** Saját alkalmazások  
**Termék képesség:** Sso

Mostantól egy új felhasználói beállítással jobban kezelheti, hogy az Office 365-alkalmazások hogyan jelenjenek meg a felhasználó hozzáférési paneljein. Ez a beállítás akkor hasznos, ha csökkenti a felhasználó hozzáférési paneljein lévő alkalmazások számát, ha csak az Office-alkalmazásokat szeretné megjeleníteni az Office-portálon. A beállítás a **Felhasználói beállítások** címkéjén található, és a címkén a **Felhasználók csak az Office 365-alkalmazásokat láthatják az Office 365 portálon.**

További információ: [Alkalmazás elrejtése a felhasználói élmény elől az Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Zökkenőmentes bejelentkezés a jelszóegyszeri bejelentkezéshez engedélyezett alkalmazásokba közvetlenül az alkalmazás URL-címéről 

**Típus:** Új funkció  
**Szolgáltatás kategória:** Saját alkalmazások  
**Termék képesség:** Sso

A My Apps böngészőbővítmény már elérhető egy kényelmes eszköz, amely megadja a My Apps egyszeri bejelentkezési képesség, mint egy parancsikont a böngészőben. A telepítés után a felhasználó egy gofri ikont fog látni a böngészőben, amely gyors hozzáférést biztosít számukra az alkalmazásokhoz. A felhasználók most már kihasználhatják a következőket:

- Az alkalmazás bejelentkezési lapjáról közvetlenül bejelentkezhet a jelszóalapú SSO-alapú alkalmazásokba
- Bármilyen alkalmazás indítása a gyorskeresési funkcióval
- Parancsikonok a legutóbb használt alkalmazásokhoz a bővítményből
- A bővítmény a Microsoft Edge, a Chrome és a Firefox számára érhető el.
 
További információt a [Saját alkalmazások biztonságos bejelentkezési bővítményében](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)talál.

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Az Azure AD felügyeleti élménye az Azure Classic Portalon megszüntetve van

**Típus:** Elavult   
**Szolgáltatás kategória:** Azure Hirdetés  
**Termék képesség:** Taglista

2018. január 8-tól az Azure AD felügyeleti felülete az Azure klasszikus portálon kilett vonva. Ez az Azure klasszikus portál levonásával együtt történt. A jövőben az Azure [AD felügyeleti központot](https://aad.portal.azure.com) kell használnia az Azure AD összes portálalapú felügyeletéhez.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>A PhoneFactor webportál kilett vonva

**Típus:** Elavult  
**Szolgáltatás kategória:** Azure Hirdetés  
**Termék képesség:** Taglista
 
2018. január 8-tól a PhoneFactor internetes portál kivisszavonult. Ezt a portált az MFA-kiszolgáló felügyeletére használták, de ezeket a függvényeket áthelyezték az Azure Portalra portal.azure.com. 

Az MFA-konfiguráció a következő helyen található: **Azure Active Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések elavultak

**Típus:** Elavult  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** Identitáséletciklus-kezelés  


Az új Azure Active Directory-felügyelet konzol általános rendelkezésre állásával és az új API-k már elérhetők mind a tevékenységi, mind a biztonsági jelentésekhez, a jelentés API-k a "/reports" végpont alatt 2017.

**Mi áll rendelkezésre?**

Az új felügyeleti konzolra való áttérés részeként 2 új API-t tettünk elérhetővé az Azure AD-tevékenységnaplók lekéréséhez. Az API-k új készlete gazdagabb szűrési és rendezési funkciókat biztosít a gazdagabb naplózási és bejelentkezési tevékenységek mellett. A biztonsági jelentéseken keresztül korábban elérhető adatok most már elérhetők a Microsoft Graph Identity Protection kockázatészlelési API-n keresztül.

További információkért lásd:

- [Ismerkedés az Azure Active Directory jelentéskészítési API-jával](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Ismerkedés az Azure Active Directory identitásvédelemmel és a Microsoft Graph-tal](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>2017. december

### <a name="terms-of-use-in-the-access-panel"></a>Használati feltételek a hozzáférési panelen

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Megfelelés
 
Most már megnyílik a Hozzáférési panel, és megtekintheti a korábban elfogadott használati feltételeket.

Kövesse az alábbi lépéseket:

1. Nyissa meg a [MyApps portált](https://myapps.microsoft.com), és jelentkezzen be.

2. A jobb felső sarokban jelölje ki a nevét, majd válassza a **Profil** elemet a listából. 

3. A **Profilban**válassza **a Használati feltételek áttekintése**lehetőséget. 

4. Most már áttekintheti az elfogadott használati feltételeket. 

További információ: az [Azure AD használati feltételei funkció (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Új Azure AD bejelentkezési élmény

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure Hirdetés  
**Termék képesség:** Felhasználói hitelesítés
 
Az Azure AD és a Microsoft-fiók identitásrendszer felhasználói felületét úgy alakították át, hogy egységes megjelenést és érzetet kapjanak. Emellett az Azure AD bejelentkezési lap gyűjti a felhasználónevet először, majd a hitelesítő adatokat a második képernyőn.

További információ: [Az új Azure AD bejelentkezési élmény most nyilvános előzetes verzióban.](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Kevesebb bejelentkezési kérdés: Egy új "bejelentkezve maradok" élmény az Azure AD bejelentkezéshez

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure Hirdetés  
**Termék képesség:** Felhasználói hitelesítés
 
Az Azure AD bejelentkezési lapján a **Bejelentkezve bejelentkezve** tartása jelölőnégyzet egy új üzenetre cserélődött, amely a sikeres hitelesítés után jelenik meg. 

Ha **igennel** válaszol erre a kérdésre, a szolgáltatás állandó frissítési jogkivonatot ad. Ez a viselkedés ugyanaz, mint amikor bejelölte a **Bejelentkezett megtartása** jelölőnégyzetet a régi élményben. Összevont bérlők esetén ez a kérdés azt követően jelenik meg, hogy sikeresen hitelesítette az összevont szolgáltatással.

További információ: [Kevesebb bejelentkezési kérdés: Az Azure AD új "bejelentkezve marad" szolgáltatása előzetes verzióban érhető el.](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Konfiguráció hozzáadása a használati feltételek kibontásához az elfogadás előtt

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Megfelelés
 
A rendszergazdák számára a használati feltételek kibővítését követelik meg a feltételek nek a feltételek elfogadása előtt.

Válassza a **Be** vagy **a Ki** lehetőséget, ha azt szeretné, hogy a felhasználók bővítsék ki a használati feltételeket. A **Bekapcsol** beállítás megköveteli a felhasználóktól, hogy az elfogadás uk előtt megtekintsék a használati feltételeket.

További információ: az [Azure AD használati feltételei funkció (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Hatókörrel történő aktiválás a jogosult szerepkör-hozzárendelések számára

**Típus:** Új funkció  
**Szolgáltatás kategória:** Kiemelt identitáskezelés  
**Termék képesség:** Kiemelt identitáskezelés
 
A hatókörrel rendelkező aktiválással aktiválhatja a jogosult Azure-erőforrásszerepkör-hozzárendeléseket, az eredeti hozzárendelés alapértékénél kisebb önállósággal. Egy példa, ha a bérlőben egy előfizetés tulajdonosaként van hozzárendelve. A hatókörrel rendelkező aktiválással aktiválhatja a tulajdonosi szerepkört legfeljebb öt, az előfizetésben található erőforrás (például erőforráscsoportok és virtuális gépek) számára. Az aktiválás hatókörének hatóköre csökkentheti a kritikus Azure-erőforrások nem kívánt módosításainak végrehajtásának lehetőségét.

További információ: [Mi az Azure AD kiemelt identitáskezelés?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Új összevont alkalmazások az Azure AD alkalmazásgyűjteményében

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2017 decemberében hozzáadtuk ezeket az új alkalmazásokat a Federation támogatással az alkalmazásgalériánkhoz:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integráció](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zen egy Azure AD Integration.

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál.

Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakörben talál további információt. 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Az Azure AD címtárszerepkörök jóváhagyási munkafolyamatai

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Kiemelt identitáskezelés  
**Termék képesség:** Kiemelt identitáskezelés
 
Az Azure AD címtárszerepkörök jóváhagyási munkafolyamata általánosan elérhető.

A jóváhagyási munkafolyamat, a kiemelt szerepkör-rendszergazdák megkövetelhetik jogosult szerepkör-tagok szerepkör aktiválása előtt használhatják a kiemelt szerepkörhasználatát. Több felhasználó és csoport delegálható jóváhagyási feladatokat. A jogosult szerepkör-tagok értesítést kapnak, ha a jóváhagyás befejeződött, és szerepük aktív.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Átmenő hitelesítés: A Skype Vállalati verzió támogatása

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

Az áteredési hitelesítés mostantól támogatja a felhasználók bejelentkezését a Skype Vállalati verzió ügyfélalkalmazásaiba, amelyek támogatják a modern hitelesítést, amely online és hibrid topológiákat is tartalmaz. 

További információt a [Modern hitelesítéssel támogatott Skype Vállalati verzió topológiája](https://technet.microsoft.com/library/mt803262.aspx)című témakörben talál.
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Az Azure AD kiemelt identitáskezelésének frissítései az Azure RBAC-hoz (előzetes verzió)

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Kiemelt identitáskezelés  
**Termék képesség:** Kiemelt identitáskezelés
 
Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) Azure AD kiemelt identitáskezelés (PIM) nyilvános előzetes verziójával most már:

* Használja csak elég adminisztráció.
* Az erőforrás-szerepkörök aktiválásához jóváhagyás szükséges.
* Ütemezze be egy szerepkör jövőbeli aktiválását, amely jóváhagyást igényel mind az Azure AD, mind az Azure RBAC szerepkörökhöz.
 
További információ: [Privileged Identity Management for Azure resources (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>2017. november
 
### <a name="access-control-service-retirement"></a>Hozzáférés-vezérlési szolgáltatás kivonása

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Hozzáférés-vezérlési szolgáltatás  
**Termék képesség:** Hozzáférés-vezérlési szolgáltatás 

Az Azure Active Directory hozzáférés-vezérlés (más néven a hozzáférés-vezérlési szolgáltatás) 2018 végén megszűnik. A részletes ütemtervet és a magas szintű áttelepítési útmutatást tartalmazó további információk a következő hetekben jelennek meg. Ezen az oldalon megjegyzéseket fűzhet a hozzáférés-vezérlési szolgáltatással kapcsolatos kérdéseihez, és egy csapattag válaszol rájuk.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>A böngésző hozzáférésének korlátozása az Intune felügyelt böngészőjéhez 

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Személyazonosság-biztonság és -védelem

Korlátozhatja a böngésző hozzáférést az Office 365-höz és más Azure AD-hez kapcsolódó felhőalkalmazásokhoz az Intune felügyelt böngészőjének jóváhagyott alkalmazásként való használatával. 

Most már konfigurálhatja a következő feltételt az alkalmazásalapú feltételes hozzáféréshez:

**Ügyfélalkalmazások:** Böngésző

**Mi a változás hatása?**

Ma a hozzáférés le van tiltva, ha ezt a feltételt használja. Ha az előzetes verzió elérhetővé válik, minden hozzáféréshez a felügyelt böngészőalkalmazás használatára lesz szükség. 

Keresse meg ezt a képességet, és több információt a közelgő blogok és kibocsátási megjegyzések. 

További információ: [Feltételes hozzáférés az Azure AD-ben.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD-alkalmazásalapú feltételes hozzáféréshez

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Személyazonosság-biztonság és -védelem

A következő alkalmazások szerepelnek a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)listáján:

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD-alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Több nyelv használati feltételeinek támogatása

**Típus:** Új funkció    
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Megfelelés

A rendszergazdák mostantól több PDF-dokumentumot tartalmazó új használati feltételeket hozhatnak létre. Ezeket a PDF-dokumentumokat megjelölheti a megfelelő nyelvvel. A felhasználók a preferenciáik alapján a megfelelő nyelvvel együtt jelennek meg a PDF-dokumentummal. Ha nincs egyezés, az alapértelmezett nyelv jelenik meg.

---
 
### <a name="real-time-password-writeback-client-status"></a>Valós idejű jelszó-visszaíró ügyfél állapota

**Típus:** Új funkció  
**Szolgáltatás kategória:** Önkiszolgáló jelszó alaphelyzetbe állítása  
**Termék képesség:** Felhasználói hitelesítés

Most már megtekintheti a helyszíni jelszó-visszaíró ügyfél állapotát. Ez a beállítás a [Jelszó-visszaállítás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) lap **helyszíni integrációs** szakaszában érhető el. 

Ha problémák merülnek fel a helyszíni visszaíró ügyféllel való kapcsolattal kapcsolatban, megjelenik egy hibaüzenet, amely a következőket tartalmazza:

- Információ arról, hogy miért nem tud csatlakozni a helyszíni visszaíró ügyfélhez.
- A probléma megoldásában segítséget nyújtó dokumentációra mutató hivatkozás. 

További információt a helyszíni integráció című [témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD-alkalmazásalapú feltételes hozzáférés 
 
**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure Hirdetés  
**Termék képesség:** Személyazonosság-biztonság és -védelem

Mostantól korlátozhatja az Office 365-höz és más Azure AD-hez kapcsolódó felhőalkalmazásokhoz való hozzáférést az Okat [tartalmazó, jóváhagyott ügyfélalkalmazásokra,](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) amelyek támogatják az Intune alkalmazásvédelmi szabályzatait az [Azure AD-alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)használatával. Az Intune alkalmazásvédelmi szabályzatai az ilyen ügyfélalkalmazások vállalati adatainak konfigurálására és védelmére szolgálnak.

Az [alkalmazásalapú](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) és [az eszközalapú](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) feltételes hozzáférési szabályzatok kombinálásával rugalmasan védheti a személyes és vállalati eszközök adatait.

Az alkalmazásalapú feltételes hozzáféréssel már elérhetők a következő feltételek és vezérlők:

**Támogatott platformfeltétel**

- iOS
- Android

**Ügyfélalkalmazások feltétele**

- Mobilalkalmazások és asztali ügyfelek

**Hozzáférés-vezérlés**

- Jóváhagyott ügyfélalkalmazás megkövetelése

További információ: [Azure AD app-based conditional access.](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure AD-eszközök kezelése az Azure Portalon

**Típus:** Új funkció  
**Szolgáltatás kategória:** Eszközregisztráció és -kezelés  
**Termék képesség:** Személyazonosság-biztonság és -védelem

Most már megtalálhatja az Azure AD-hez és az eszközzel kapcsolatos tevékenységeket egy helyen. Van egy új felügyeleti felület az összes eszköz identitások és beállítások az Azure Portalon. Ebben a kiadásban a következőket teheti:

- Tekintse meg az azure-beli AD feltételes hozzáféréshez elérhető összes eszközét.
- Tekintse meg a tulajdonságokat, amelyek magukban foglalják a hibrid Azure AD-hez csatlakozott eszközöket.
- BitLocker-kulcsokat kereshet az Azure AD-hez csatlakozó eszközökhöz, kezelheti az eszközt az Intune-nal stb.
- Az Azure AD-eszközzel kapcsolatos beállítások kezelése.

További információ: [Eszközök kezelése az Azure Portal használatával](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)című témakörben talál.

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>A macOS támogatása az Azure AD feltételes hozzáféréseszközplatformjaként 

**Típus:** Új funkció    
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Személyazonosság-biztonság és -védelem 

Most már felveheti (vagy kizárhatja) macOS eszközplatform-feltételként az Azure AD feltételes hozzáférési szabályzatában. A macOS alkalmazással a támogatott eszközplatformokon a következőket teheti:

- **MacOS-eszközök regisztrálása és kezelése az Intune használatával.** Más platformokhoz, például az iOS-hez és az Androidhoz hasonlóan a macOS számára is elérhető egy vállalati portálalkalmazás az egységes regisztrációk hoz. A macOS-hez való új vállalati portál alkalmazás segítségével regisztrálhat egy eszközt az Intune-nal, és regisztrálhatja azt az Azure AD-vel.
- **Győződjön meg arról, hogy a macOS-eszközök megfelelnek a szervezet Intune-ban meghatározott megfelelőségi szabályzatainak.** Az Azure Portalin az Intune-ban most már megfelelőségi szabályzatokat állíthat be a macOS-eszközökhöz. 
- **Korlátozza az Azure AD-ben az alkalmazásokhoz való hozzáférést csak a megfelelő macOS-eszközökre.** A feltételes hozzáférés házirend-szerzői rendelkezik macOS külön eszköz platform lehetőség. Most már macOS-specifikus feltételes hozzáférési szabályzatokat hozhat létre a megcélzott alkalmazáskészlethez az Azure-ban.

További információkért lásd:

- [Megfelelőségi szabályzat létrehozása MacOS-eszközökhöz az Intune-nal](https://aka.ms/macoscompliancepolicy)
- [Feltételes hozzáférés az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Hálózati házirend-kiszolgáló bővítmény az Azure többtényezős hitelesítéséhez 

**Típus:** Új funkció    
**Szolgáltatás kategória:**  Többtényezős hitelesítés  
**Termék képesség:** Felhasználói hitelesítés

Az Azure többtényezős hitelesítéshálózati házirend-kiszolgálóbővítménye felhőalapú többtényezős hitelesítési lehetőségeket ad a hitelesítési infrastruktúrához a meglévő kiszolgálók használatával. A Hálózati házirend-kiszolgáló bővítménysegítségével telefonhívást, sms-t vagy telefonos alkalmazás-ellenőrzést adhat hozzá a meglévő hitelesítési folyamathoz. Nem kell új kiszolgálókat telepíteni, konfigurálni és karbantartania. 

Ez a bővítmény olyan szervezetek számára készült, amelyek az Azure többtényezős hitelesítési kiszolgáló telepítése nélkül szeretnék megvédeni a virtuális magánhálózati kapcsolatokat. A Hálózati házirend-kiszolgáló bővítmény adapterként működik a RADIUS és a felhőalapú Azure többtényezős hitelesítés között, hogy egy második hitelesítési tényezőt biztosítson az összevont vagy szinkronizált felhasználók számára.

További információt [a Meglévő hálózati házirend-kiszolgáló infrastruktúrájának integrálása az Azure többtényezős hitelesítésével című témakörben](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension)talál.
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Törölt felhasználók visszaállítása vagy végleges eltávolítása

**Típus:** Új funkció    
**Szolgáltatás kategória:** Felhasználókezelés  
**Termék képesség:** Taglista 

Az Azure AD felügyeleti központban most már:

- Törölt felhasználó visszaállítása. 
- Felhasználó végleges törlése.

**Próbálja ki:**

1. Az Azure AD felügyeleti központban válassza a [Minden felhasználó](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) a **Kezelés** szakaszban. 

2. A **Megjelenítés** listában válassza a **Legutóbb törölt felhasználók lehetőséget.** 

3. Jelöljön ki egy vagy több nemrég törölt felhasználót, majd állítsa vissza őket, vagy véglegesen törölje őket.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD-alkalmazásalapú feltételes hozzáféréshez
 
**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Személyazonosság-biztonság és -védelem

A következő alkalmazások kerültek fel a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)listájára:

- Microsoft Planner
- Azure Information Protection 

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD-alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>"OR" használata vezérlők között a feltételes hozzáférési házirendben 

**Típus:** Megváltozott funkció    
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Személyazonosság-biztonság és -védelem
 
Most már használhatja a "OR" (szükséges az egyik a kiválasztott vezérlők) a feltételes hozzáférés vezérlők. Ezzel a funkcióval "OR" házirendeket hozhat létre a hozzáférés-vezérlések között. Ezzel a funkcióval például létrehozhat egy olyan házirendet, amely megköveteli, hogy a felhasználó a többtényezős hitelesítés "OR" használatával jelentkezzen be egy megfelelő eszközön való bejelentkezéshez.

További információ: [Vezérlők az Azure AD feltételes hozzáférésben.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>A valós idejű kockázatészlelések összesítése

**Típus:** Megváltozott funkció    
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Személyazonosság-biztonság és -védelem

Az Azure AD Identity Protection, minden valós idejű kockázatészlelések, amelyek egy adott napon az azonos IP-cím ről származó most összesítve minden egyes kockázatészlelési típus. Ez a módosítás korlátozza a felhasználói biztonság módosítása nélkül megjelenített kockázatészlelések mennyiségét.

Az alapul szolgáló valós idejű észlelés működik minden alkalommal, amikor a felhasználó bejelentkezik. Ha rendelkezik egy többtényezős hitelesítésre vagy blokkhozzáférésre beállított bejelentkezési kockázati biztonsági házirenddel, akkor továbbra is aktiválódik az egyes kockázatos bejelentkezések során.
 
---
 
## <a name="october-2017"></a>2017. október

### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések elavultak

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** Identitáséletciklus-kezelés  

Az Azure Portal a következőket biztosítja:

- Egy új Azure AD felügyeleti konzol.
- Új API-k a tevékenység- és biztonsági jelentésekhez.
 
Ezen új képességek miatt a /reports végpont alatti jelentési API-k 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatikus bejelentkezési mező észlelése

**Típus:** Rögzített   
**Szolgáltatás kategória:** Saját alkalmazások  
**Termék képesség:** Egyszeri bejelentkezés  

Az Azure AD támogatja az automatikus bejelentkezési mező észlelését olyan alkalmazásokhoz, amelyek HTML-felhasználónevet és jelszót jelenítmeg. Ezeket a lépéseket az [alkalmazás bejelentkezési mezőinek automatikus rögzítése](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app)ismerteti. Ezt a lehetőséget úgy találhatja meg, ha nem *galéria alkalmazást* ad hozzá az Azure Portal Enterprise **Applications (Vállalati alkalmazások)** [lapján.](https://aad.portal.azure.com) Ezenkívül az új alkalmazás **egyszeri bejelentkezési** módját **jelszóalapú egyszeri bejelentkezésre**is konfigurálhatja, megadhat egy webes URL-címet, majd mentheti a lapot.
 
Szolgáltatáshiba miatt ez a funkció ideiglenesen le lett tiltva. A probléma megoldódott, és az automatikus bejelentkezési mező észlelése ismét elérhető.

---

### <a name="new-multi-factor-authentication-features"></a>Új többtényezős hitelesítési funkciók

**Típus:** Új funkció  
**Szolgáltatás kategória:** Többtényezős hitelesítés  
**Termék képesség:** Személyazonosság-biztonság és -védelem  

A többtényezős hitelesítés (MFA) a szervezet védelmének alapvető része. A hitelesítő adatok adaptívabbá tétele és a zökkenőmentesebb működés érdekében a következő szolgáltatások kerültek hozzáadásra: 

- A többtényezős kihívás eredményei közvetlenül integrálva vannak az Azure AD bejelentkezési jelentésébe, amely programozott hozzáférést biztosít az MFA-eredményekhez.
- Az MFA-konfiguráció mélyebben integrálva van az Azure AD konfigurációs élményébe az Azure Portalon.

Ezzel a nyilvános előzetes verzióval az MFA-kezelés és a jelentéskészítés az Azure AD alapvető konfigurációs élményének integrált részét képezik. Most már kezelheti az MFA felügyeleti portál funkcióit az Azure AD-élményben.

További információ: [Reference for MFA reporting in the Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Használati feltételek

**Típus:** Új funkció  
**Szolgáltatás kategória:** Használati feltételek  
**Termék képesség:** Megfelelés  

Az Azure AD használati feltételei segítségével olyan információkat mutathat be, mint például a jogi vagy megfelelőségi követelményekre vonatkozó felelősségi nyilatkozatok a felhasználók számára.

Az Azure AD használati feltételeit a következő esetekben használhatja:

- Általános használati feltételek a szervezet összes felhasználója számára
- A felhasználó tulajdonságain alapuló konkrét felhasználási feltételek (például orvosok vs. ápolók vagy hazai és nemzetközi alkalmazottak, dinamikus csoportok által végzett)
- Konkrét használati feltételek a nagy hatású üzleti alkalmazások, például a Salesforce eléréséhez

További információ: [Azure AD használati feltételek](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>A kiemelt identitáskezelés fejlesztései

**Típus:** Új funkció  
**Szolgáltatás kategória:** Kiemelt identitáskezelés  
**Termék képesség:** Kiemelt identitáskezelés  

Az Azure AD kiemelt identitáskezelés, kezelheti, szabályozhatja és figyelheti az Azure-erőforrásokhoz való hozzáférés (előzetes verzió) a szervezeten belül:

- Előfizetések
- Erőforráscsoportok
- Virtual machines (Virtuális gépek) 

Az Azure-portálon belül az Azure RBAC funkciót használó összes erőforrás kihasználhatja az Azure AD kiemelt identitáskezelési által kínált összes biztonsági és életciklus-kezelési képességet.

További információ: [Privileged Identity Management for Azure resources.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

---

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hozzáférési vélemények  
**Termék képesség:** Megfelelés  

A szervezetek a hozzáférés-felülvizsgálatok (előzetes verzió) segítségével hatékonyan kezelhetik a csoporttagságokat és a vállalati alkalmazásokhoz való hozzáférést: 

- Az alkalmazásokhoz való hozzáférések és a csoporttagságok felülvizsgálatával újrahitelesítheti a vendégfelhasználói hozzáférést. A véleményezők hatékonyan dönthetnek arról, hogy engedélyezik-e a vendégek számára a folyamatos hozzáférést a hozzáférési ellenőrzések által biztosított elemzések alapján.
- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információ: [Azure AD access reviews.](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Külső alkalmazások elrejtése a Saját alkalmazások és az Office 365 alkalmazásindítója elől

**Típus:** Új funkció  
**Szolgáltatás kategória:** Saját alkalmazások  
**Termék képesség:** Egyszeri bejelentkezés  

Most már jobban kezelheti azokat az alkalmazásokat, amelyek megjelennek a felhasználók portáljain egy új **elrejtési alkalmazástulajdonságon** keresztül. Elrejtheti az alkalmazásokat, hogy segítsen azokban az esetekben, ahol az alkalmazáscsempék megjelennek a háttérszolgáltatásokhoz, vagy ismétlődő csempéket és áttetve a felhasználók alkalmazásindítóit. A kapcsoló a külső gyártótól származó alkalmazás **Tulajdonságok** szakaszában található, és a felhasználó számára látható címkével van **ellátva?** Az alkalmazásokat programozott módon is elrejtheti a PowerShellen keresztül. 

További információ: [Egy külső alkalmazás elrejtése a felhasználó azure AD-ben.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app) 


**Mi áll rendelkezésre?**

 Az új felügyeleti konzolra való áttérés részeként két új API érhető el az Azure AD-tevékenységnaplók lekéréséhez. Az API-k új készlete gazdagabb szűrési és rendezési funkciókat biztosít a gazdagabb naplózási és bejelentkezési tevékenységek mellett. A biztonsági jelentéseken keresztül korábban elérhető adatok a Microsoft Graph Identitásvédelmi kockázatészlelési API-n keresztül érhetők el.


## <a name="september-2017"></a>2017. szeptember

### <a name="hotfix-for-identity-manager"></a>Gyorsjavítás az Identity Manager alkalmazáshoz

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Identitáskezelő  
**Termék képesség:** Identitáséletciklus-kezelés  

2017. szeptember 25-től elérhető egy gyorsjavító összesítő csomag (a 4.4.1642.0-s verzió) az Identity Manager 2016 Service Pack 1 szervizcsomaghoz. Ez a roll-up csomag:

- Megoldja a problémákat, és további fejlesztéseket ad hozzá.
- Összegző frissítés, amely lecseréli az összes Identity Manager 2016 Service Pack 1 frissítést a 4.4.1459.0-s identitáskezelő 2016-os létrehozásához. 
- Ehhez az Identity Manager 2016-ot kell készítenie a 4.4.1302.0-ra. 

További információ: [Gyorsjavítás-összesítő csomag (build 4.4.1642.0) az Identity Manager 2016 Service Pack 1 szervizcsomaghoz érhető el.](https://support.microsoft.com/help/4021562) 

---
