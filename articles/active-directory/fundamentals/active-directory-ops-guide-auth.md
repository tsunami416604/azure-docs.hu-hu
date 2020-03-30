---
title: Az Azure Active Directory hitelesítéskezelési műveleteinek útmutatója
description: Ez a műveleti útmutató ismerteti azokat az ellenőrzéseket és műveleteket, amelyeket a hitelesítéskezelés biztonságossá tétele érdekében kell tennie
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: bc5824fcb62477d4e6dc6c2b7390b1bfa916094f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368056"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Az Azure Active Directory hitelesítéskezelési műveleteinek útmutatója

Az Azure [AD-műveletek referencia-útmutatójának](active-directory-ops-guide-intro.md) ez a szakasza ismerteti a hitelesítő adatok védelméhez és kezeléséhez, a hitelesítési élmény meghatározásához, a delegáláshoz, a használat méréséhez és a vállalati biztonsági állapot on alapuló hozzáférési szabályzatok meghatározásához szükséges ellenőrzéseket és műveleteket.

> [!NOTE]
> Ezek az ajánlások a közzététel időpontjától aktuálisak, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell identitáskezelési gyakorlatukat, ahogy a Microsoft-termékek és -szolgáltatások idővel fejlődnek.

## <a name="key-operational-processes"></a>Kulcsfontosságú működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok hozzárendelése a legfontosabb feladatokhoz

Az Azure Active Directory kezeléséhez a legfontosabb operatív feladatok és folyamatok folyamatos végrehajtására van szükség, amelyek nem feltétlenül részei a bevezetési projektnek. Továbbra is fontos, hogy ezeket a feladatokat a környezet optimalizálása érdekében állítsa be. A legfontosabb feladatok és az ajánlott tulajdonosok a következők:

| Tevékenység | Tulajdonos |
| :- | :- |
| Egyszeri bejelentkezés (SSO) konfiguráció életciklusának kezelése az Azure AD-ben | IAM műveleti csapat |
| Feltételes hozzáférési szabályzatok tervezése Az Azure AD-alkalmazásokhoz | InfoSec architektúra csapata |
| Bejelentkezési tevékenység archiválása SIEM-rendszerben | InfoSec műveleti csapat |
| Kockázati események archiválása SIEM rendszerben | InfoSec műveleti csapat |
| A biztonsági jelentések osztályozása és kivizsgálása | InfoSec műveleti csapat |
| Osztályozás és kockázati események vizsgálata | InfoSec műveleti csapat |
| Az Azure AD Identity Protection által kockázati és biztonsági résjelentésekmiatt megjelölt felhasználók osztályozása és vizsgálata | InfoSec műveleti csapat |

> [!NOTE]
> Az Azure AD Identity Protection használatához Azure AD Premium P2 licenc szükséges. A követelményeknek megfelelő licenc [megkereséséhez olvassa el az Azure AD Free és az Azure AD Premium kiadások általánosan elérhető funkcióinak összehasonlítása című témakört.](https://azure.microsoft.com/pricing/details/active-directory/)

A lista áttekintésekor előfordulhat, hogy tulajdonost kell hozzárendelnie a tulajdonosból hiányzó feladatokhoz, vagy módosítania kell a fenti javaslatokhoz nem igazodó tulajdonossal rendelkező feladatok tulajdonjogát.

#### <a name="owner-recommended-reading"></a>Tulajdonos ajánlott olvasás

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Irányítás az Azure-ban](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Hitelesítő adatok kezelése

### <a name="password-policies"></a>Jelszószabályzatok

A jelszavak biztonságos kezelése az identitás- és hozzáférés-kezelés egyik legkritikusabb része, és gyakran a támadások legnagyobb célpontja. Az Azure AD számos olyan funkciót támogat, amelyek segíthetnek megakadályozni a támadások sikeresek kialakulását.

Az alábbi táblázat segítségével megtalálhatja a megoldandó probléma enyhítésére ajánlott megoldást:

| Probléma | Ajánlás |
| :- | :- |
| Nincs mechanizmus a gyenge jelszavak elleni védelemre | Az Azure AD [önkiszolgáló jelszó-visszaállítás (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) és [jelszavas védelem](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) engedélyezése |
| Nincs mechanizmus a kiszivárgott jelszavak észlelésére | [A jelszókivonat-szinkronizálás](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) engedélyezése az elemzési adatok megszerzéséhez |
| Az AD FS használata, és nem lehet áthelyezni a felügyelt hitelesítésre | [Az AD FS Extranet intelligens zárolás](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) és/vagy Azure [AD Smart Lockout](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) engedélyezése |
| A jelszóházirend összetettségen alapuló szabályokat használ, például hosszt, több karakterkészletet vagy lejárati | Gondolja át újra a [Microsoft ajánlott gyakorlatait, és váltson](https://aka.ms/passwordguidance) a jelszókezeléssel kapcsolatos módszerére, és telepítse az Azure [AD jelszavas védelmét.](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) |
| A felhasználók nincsenek regisztrálva a többtényezős hitelesítés (MFA) használatára | [Regisztrálja a felhasználó összes biztonsági információját,](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) hogy az a felhasználó személyazonosságának és jelszavának ellenőrzésére szolgáló mechanizmusként használható legyen. |
| A jelszavak nem vonják vissza a felhasználói kockázat alapján | Az Azure AD [Identity Protection felhasználói kockázati szabályzatok](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) üzembe helyezése a kiszivárgott hitelesítő adatok jelszómódosításának kényszerítéséhez az SSPR használatával |
| Nincs intelligens zárolási mechanizmus, amely megvédi a rosszindulatú hitelesítést az azonosított IP-címekről érkező hibás szereplőktől | Felhőalapú hitelesítés telepítése jelszókivonat-szinkronizálással vagy [átmenő hitelesítéssel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Jelszóházirendajánlott olvasás

- [Az Azure AD és az AD FS bevált módszerei: Védelem a jelszószórásos támadások ellen - Vállalati mobilitás + Biztonság](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Önkiszolgáló jelszó-visszaállítás és jelszavas védelem engedélyezése

Azok a felhasználók, akiknek meg kell változtatniuk vagy vissza kell állítaniuk jelszavukat, az egyik legnagyobb mennyiségi forrás és az ügyfélszolgálati hívások költsége. A költségek mellett a jelszó módosítása a felhasználói kockázat csökkentésének eszközeként alapvető lépés a szervezet biztonsági állapotának javításában.

Legalább az Azure AD [önkiszolgáló jelszó-visszaállítás](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR) és a helyszíni [jelszavas védelem](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) telepítése ajánlott a következők elvégzéséhez:

- Elterelje az ügyfélszolgálati hívásokat.
- Cserélje le az ideiglenes jelszavak használatát.
- Cserélje le a meglévő önkiszolgáló jelszókezelési megoldásokat, amelyek egy helyszíni megoldásra támaszkodnak.
- [Távolítsuk el](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) a gyenge jelszavakat a szervezetben.

> [!NOTE]
> Az Azure AD Premium P2-előfizetéssel rendelkező szervezetek számára ajánlott az SSPR telepítése és [az Identitásvédelem felhasználói kockázati szabályzatának](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)részeként való használata.

### <a name="strong-credential-management"></a>Erős hitelesítő adatok kezelése

A jelszavak önmagukban nem elég biztonságosak ahhoz, hogy megakadályozzák a rossz szereplők hozzáférését a környezethez. Legalább minden kiemelt jogosultságú fiókkal rendelkező felhasználónak engedélyeznie kell a többtényezős hitelesítést (MFA). Ideális esetben engedélyeznie kell a [kombinált regisztrációt,](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) és minden felhasználótól meg kell követelnie, hogy regisztráljon az MFA-ra és az SSPR-re a [kombinált regisztrációs élmény](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview)használatával. Végül azt javasoljuk, hogy fogadjon el egy stratégiát, amely [rugalmasságot biztosít](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) az előre nem látható körülmények miatti zárolás kockázatának csökkentése érdekében.

![Kombinált felhasználói élmény folyamata](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Helyszíni kimaradáshitelesítéshitelesítési rugalmassága

Az egyszerűség és a kiszivárgott hitelesítő adatok észlelésének lehetővé tétele mellett az Azure AD Password Hash Sync (PHS) és az Azure MFA lehetővé teszi a felhasználók számára a SaaS-alkalmazások és az Office 365 elérését a kibertámadások, például a [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)miatti helyszíni kimaradások ellenére. Lehetőség van a PHS engedélyezésére is, miközben az összevonással együtt. A PHS engedélyezése lehetővé teszi a hitelesítés tartalék, ha az összevonási szolgáltatások nem érhetők el.

Ha a helyszíni szervezet hiányzik a kimaradás rugalmassági stratégia, vagy rendelkezik az Azure AD-vel nincs integrálva, telepítenie kell az Azure AD PHS-t, és meg kell határoznia egy vész-helyreállítási tervet, amely tartalmazza a PHS-t. Az Azure AD PHS engedélyezése lehetővé teszi a felhasználók számára, hogy hitelesítsék magukat az Azure AD-vel szemben, ha a helyszíni Active Directory nem érhető el.

![jelszókivonat-szinkronizálási folyamat](./media/active-directory-ops-guide/active-directory-ops-img5.png)

A hitelesítési beállítások jobb megértéséhez [olvassa el A megfelelő hitelesítési módszer kiválasztása az Azure Active Directory hibrid identitáskezelési megoldásához című témakört.](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)

### <a name="programmatic-usage-of-credentials"></a>Hitelesítő adatok automatizált használata

A PowerShellt vagy a Microsoft Graph API-t használó alkalmazásokat használó Azure AD-parancsfájlok biztonságos hitelesítést igényelnek. A parancsfájlok és eszközök gyenge hitelesítő adatok kezelése növeli a hitelesítő adatok ellopásának kockázatát. Ha olyan parancsfájlokat vagy alkalmazásokat használ, amelyek kódolva lévő jelszavakra vagy jelszóutasításokra támaszkodnak, először tekintse át a hitelesítő fájlokban vagy a forráskódban lévő jelszavakat, majd cserélje le ezeket a függőségeket, és használja az Azure Felügyelt identitásokat, az integrált Windows-hitelesítést vagy [a tanúsítványokat,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates) amikor csak lehetséges. Olyan alkalmazások esetében, ahol az előző megoldások nem lehetségesek, fontolja meg az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)használatát.

Ha úgy dönt, hogy vannak jelszóhitelesítő adatokkal rendelkező rendszerbiztonsági tagok, és nem biztos abban, hogy ezeket a jelszóhitelesítő adatokat parancsfájlok vagy alkalmazások hogyan biztosítják, lépjen kapcsolatba az alkalmazás tulajdonosával a használati minták jobb megértéséhez.

A Microsoft azt is javasolja, hogy lépjen kapcsolatba az alkalmazástulajdonosaival a használati minták megismeréséhez, ha jelszóhitelesítő adatokkal rendelkező szolgáltatásnévi hitelesítő adatokkal rendelkezik.

## <a name="authentication-experience"></a>Hitelesítési élmény

### <a name="on-premises-authentication"></a>Helyszíni hitelesítés

Az integrált Windows-hitelesítéssel (IWA) vagy a zökkenőmentes egyszeri bejelentkezéssel (SSO) kezelt hitelesítés jelszókivonat-szinkronizálással vagy átadó hitelesítéssel a legjobb felhasználói élmény a vállalati hálózaton belül helyszíni tartományvezérlők számára. Minimalizálja a hitelesítő adatok gyors fáradtságát, és csökkenti annak kockázatát, hogy a felhasználók adathalász támadások áldozatául essenek. Ha már használ felhőalapú hitelesítést PHS-sel vagy PTA-val, de a felhasználóknak továbbra is be kell írniuk a jelszavukat a helyszíni hitelesítéshez, akkor azonnal [telepítenie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)kell a zökkenőmentes egyszeri bejelentkezést. Másrészt, ha jelenleg összevont tervek et, hogy végül áttérni a felhőáltal felügyelt hitelesítés, majd végre kell hajtania a zökkenőmentes egyszeri bejelentkezés részeként az áttelepítési projekt.

### <a name="device-trust-access-policies"></a>Eszközmegbízhatósági hozzáférési szabályzatok

A szervezet egyik felhasználójához hasonlóan az eszköz is egy alapvető identitás, amelyet védeni szeretne. Az eszköz identitásával bármikor és bárhol megvédheti az erőforrásokat.Az eszköz hitelesítése és a megbízhatósági típus elszámolása javítja a biztonsági pozíciót és a használhatóságot a következők szerint:

- A súrlódás elkerülése, például az MFA-val, ha az eszköz megbízható
- Hozzáférés letiltása nem megbízható eszközökről
- Windows 10-es eszközök esetén [zökkenőmentesen biztosíthassa a helyszíni erőforrásokba való egyszeri bejelentkezést.](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso)

Ezt a célt úgy hajthatja végre, hogy eszközidentitásokat hoz létre, és kezeli őket az Azure AD-ben az alábbi módszerek egyikével:

- A szervezetek a [Microsoft Intune segítségével](https://docs.microsoft.com/intune/what-is-intune) kezelhetik az eszközt, megfelelőségi szabályzatokat kényszeríthetnek, tanúsíthatják az eszközök állapotát, és feltételes hozzáférési szabályzatokat állíthatnak be attól függően, hogy az eszköz megfelelő-e. A Microsoft Intune képes kezelni az iOS-eszközöket, a Mac asztali számítógépeket (a JAMF-integráció révén), a Windows asztali számítógépeket (natív módon a Windows 10 Mobile Device Management használatával, valamint a Microsoft Endpoint Configuration Managerrel közösen kezeli) és az Android mobileszközöket.
- [A hibrid Azure AD-csatlakozás](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) csoportházirendekkel vagy Microsoft Endpoint-konfigurációkezelővel biztosít felügyeletet olyan környezetben, ahol az Active Directoryhoz tartományhoz csatlakozott számítógépek eszközei vel rendelkezik. A szervezetek felügyelt környezetet helyezhetnek üzembe PHS-en vagy PTA-n keresztül zökkenőmentes egyszeri bejelentkezéssel. Az eszközök Azure AD-be való bejuttatása maximalizálja a felhasználók hatékonyságát a felhőbeli és helyszíni erőforrásokon keresztül, miközben lehetővé teszi a felhőbeli és helyszíni erőforrásokhoz való hozzáférés biztosítását [feltételes hozzáféréssel.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) 

Ha olyan tartományhoz csatlakozott Windows-eszközökkel rendelkezik, amelyek nincsenek regisztrálva a felhőben, vagy tartományhoz csatlakozott Windows-eszközök, amelyek a felhőben vannak regisztrálva, de feltételes hozzáférési szabályzatok nélkül vannak regisztrálva, akkor regisztrálnia kell a nem regisztrált eszközöket, és mindkét esetben [a hibrid Azure AD-csatlakozást kell használnia a](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) feltételes hozzáférési szabályzatok vezérlőjeként.

![A feltételes hozzáférési szabályzatban a hibrid eszközt igénylő feltételes hozzáférési szabályzatban lévő támogatás képernyőképe](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Ha az eszközöket MDM vagy Microsoft Intune használatával kezeli, de nem használja az eszközvezérlőket a feltételes hozzáférési szabályzatokban, akkor azt javasoljuk, hogy az [eszköz megkövetelése megfelelőként legyen megjelölve](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) a házirendekben.

![Az eszközmegfelelőséget igénylő feltételes hozzáférési szabályzatban a támogatás ról készült képernyőkép](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Az eszközmegbízhatósági hozzáférési házirendek ajánlott olvasása

- [Útmutató: A hibrid Azure Active Directory-csatlakozás megvalósításának megtervezése](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [Identitás- és eszköz-hozzáférési konfigurációk](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Vállalati Windows Hello

A Windows 10-ben [a Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) lecseréli a jelszavakat erős kétfaktoros hitelesítésre a számítógépeken. A Windows Hello for Business lehetővé teszi a felhasználók számára az MFA egyszerűbb, egyszerűbb éivel való elköteleződést, és csökkenti a jelszavaktól való függőséget. Ha még nem kezdte el a Windows 10-es eszközök bevezetését, vagy csak részben telepítette őket, javasoljuk, hogy frissítsen a Windows 10-re, és [engedélyezze](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) a Windows Hello for Business alkalmazást minden eszközön.

Ha többet szeretne megtudni a jelszó nélküli hitelesítésről, olvassa el [a Jelszavak nélküli világ az Azure Active Directoryval](https://aka.ms/passwordlessdoc)című témakört.

## <a name="application-authentication-and-assignment"></a>Alkalmazás hitelesítése és hozzárendelése

### <a name="single-sign-on-for-apps"></a>Egyszeri bejelentkezés az alkalmazásokhoz

A legjobb felhasználói élmény, a kockázatcsökkentés, a jelentési képesség és a cégirányítási képesség érdekében elengedhetetlen, hogy szabványos egyszeri bejelentkezési mechanizmust biztosítson a teljes vállalat számára. Ha olyan alkalmazásokat használ, amelyek támogatják az SSO-t az Azure AD-vel, de jelenleg helyi fiókok használatára vannak konfigurálva, újra kell konfigurálnia ezeket az alkalmazásokat az SSO azure AD-vel való használatához. Hasonlóképpen, ha olyan alkalmazásokat használ, amelyek támogatják az SSO-t az Azure AD-vel, de egy másik identitásszolgáltatót használnak, újra kell konfigurálnia ezeket az alkalmazásokat az SSO azure AD-vel való használatára is. Olyan alkalmazások esetében, amelyek nem támogatják az összevonási protokollokat, de támogatják az űrlapalapú hitelesítést, azt javasoljuk, hogy konfigurálja az alkalmazást [jelszó-tároló használata](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) az Azure AD alkalmazásproxyval.

![AppProxy jelszóalapú bejelentkezés](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Ha nem rendelkezik olyan mechanizmussal, amely felderíti a szervezetben a nem felügyelt alkalmazásokat, javasoljuk, hogy egy felderítési folyamatot hajtson végre egy felhőalapú hozzáférés-biztonsági közvetítő megoldás (CASB) használatával, például a [Microsoft Cloud App Security használatával.](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)

Végül, ha rendelkezik egy Azure AD-alkalmazáskatalógussal, és olyan alkalmazásokat használ, amelyek támogatják az SSO-t az Azure AD-vel, javasoljuk, hogy [az alkalmazást az alkalmazásgalériában sorolja fel.](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)

#### <a name="single-sign-on-recommended-reading"></a>Egyszeri bejelentkezés ajánlott olvasás

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>AD FS-alkalmazások áttelepítése az Azure AD-be

[Az alkalmazások AD FS-ből az Azure AD-be való áttelepítése](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) további biztonsági lehetőségeket, egységesebb kezelhetőséget és jobb együttműködési élményt tesz lehetővé. Ha az AD FS-ben konfigurált, az Azure AD-vel rendelkező sso-t támogató alkalmazásokat használ, akkor újra kell konfigurálnia ezeket az alkalmazásokat az SSO azure-beli AD-vel való használatára. Ha az AD FS-ben konfigurált alkalmazások nem támogatott azure AD, lépjen kapcsolatba az alkalmazás tulajdonosai, hogy tudja, ha a speciális konfiguráció az alkalmazás abszolút követelménye. Ha nem szükséges, majd újra kell konfigurálnia az alkalmazást az SSO azure AD használatával.

![Az Azure AD mint elsődleges identitásszolgáltató](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Az Azure AD Connect Health for ADFS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) segítségével összegyűjtheti az Azure AD-be potenciálisan áttelepíthető alkalmazások konfigurációs adatait.

### <a name="assign-users-to-applications"></a>Felhasználók hozzárendelése alkalmazásokhoz

[A felhasználók alkalmazásokhoz való hozzárendelése](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) a legjobb a csoportok használatával, mivel nagyobb rugalmasságot és nagyfokú kezelhetőséget tesz lehetővé. A csoportok használatának előnyei közé tartozik [az attribútumalapú dinamikus csoporttagság](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) és [az alkalmazástulajdonosoknak történő delegálás.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners) Ezért ha már használ és kezel csoportokat, javasoljuk, hogy tegye meg a következő műveleteket a nagy léptékű felügyelet javítása érdekében:

- Csoportkezelés és -irányítás delegálása az alkalmazástulajdonosoknak.
- Engedélyezze az önkiszolgáló hozzáférést az alkalmazáshoz.
- Dinamikus csoportok definiálása, ha a felhasználói attribútumok következetesen meghatározhatják az alkalmazásokhoz való hozzáférést.
- Az [Azure AD-hozzáférés-felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)használatával valósítsa meg az igazolást az alkalmazás-hozzáféréshez használt csoportok számára.

Másrészt, ha úgy találja, hogy az egyes felhasználókhoz rendelt alkalmazások at talál, ügyeljen arra, hogy az alkalmazások körül [vállalatirányítási](https://docs.microsoft.com/azure/active-directory/governance/index) megoldásokat valósítson meg.

#### <a name="assign-users-to-applications-recommended-reading"></a>Felhasználók hozzárendelése az ajánlott alkalmazásokhoz

- [Felhasználók és csoportok hozzárendelése egy alkalmazáshoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Alkalmazásregisztrációs engedélyek delegálása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Dinamikus tagsági szabályok csoportokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Hozzáférési szabályzatok

### <a name="named-locations"></a>Nevesített helyek

Az Azure [AD-ben elnevezett helyekkel](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) címkézheti a szervezet megbízható IP-címtartományait. Az Azure AD a nevesített helyeket a következő célokra használja:

- A kockázati eseményekben a hamis pozitív értékek megelőzése. A megbízható hálózati helyről történő bejelentkezés csökkenti a felhasználó bejelentkezési kockázatát.
- [Helyalapú feltételes hozzáférés konfigurálása](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations).

![Névvel ellátott helyek](./media/active-directory-ops-guide/active-directory-ops-img10.png)

A prioritás alapján az alábbi táblázat segítségével keresse meg a szervezet igényeinek leginkább megfelelő ajánlott megoldást:

| **Prioritás** | **Forgatókönyv** | **Ajánlás** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Ha PHS-t vagy PTA-t használ, és az elnevezett helyek nincsenek definiálva | Elnevezett helyek meghatározása a kockázati események észlelésének javítása érdekében |
| 2 | Ha össze van kötve, és nem használja az "insideCorporateNetwork" jogcímet és a megnevezett helyeket, nincs definiálva | Elnevezett helyek meghatározása a kockázati események észlelésének javítása érdekében |
| 3 | Ha nem használ elnevezett helyeket a feltételes hozzáférési házirendekben, és nincs kockázat vagy eszközvezérlők a feltételes hozzáférési házirendekben | A feltételes hozzáférési házirend beállítása elnevezett helyekkel |
| 4 | Ha össze van minősítve, és használja az "insideCorporateNetwork" jogcímet és a megnevezett helyeket, nincs definiálva | Elnevezett helyek meghatározása a kockázati események észlelésének javítása érdekében |
| 5 | Ha névalatt elnevezett helyek helyett többmint másként használt megbízható IP-címeket használ, és megbízhatóként jelöli meg őket | Elnevezett helyek definiálása és megbízhatóként való megjelölésük a kockázati események észlelésének javítása érdekében |

### <a name="risk-based-access-policies"></a>Kockázatalapú hozzáférési házirendek

Az Azure AD kiszámíthatja a kockázatot minden bejelentkezéskor és minden felhasználó. A kockázat feltételként való használata a hozzáférési házirendekben jobb felhasználói élményt biztosíthat, például kevesebb hitelesítési kérdést, és nagyobb biztonságot, például csak akkor kéri a felhasználókat, amikor szükség van rájuk, és automatizálja a választ és a szervizelést.

![Bejelentkezési kockázati szabályzat](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Ha már rendelkezik Azure AD Premium P2-licencekkel, amelyek támogatják a kockázati feltételek használatát a hozzáférési szabályzatokban, de nincsenek használatban, javasoljuk, hogy adja hozzá a kockázatot a biztonsági állapothoz.

#### <a name="risk-based-access-policies-recommended-reading"></a>A kockázatalapú hozzáférési házirendek ajánlott olvasása

- [Útmutató: A bejelentkezési kockázati szabályzat konfigurálása](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [Útmutató: A felhasználói kockázati házirend konfigurálása](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>Ügyfélalkalmazás-hozzáférési házirendek

A Microsoft Intune Application Management (MAM) lehetővé teszi az adatvédelmi vezérlők, például a tárolás titkosításának, a PIN-kód, a távtároló-karbantartás stb. Ezenkívül feltételes hozzáférési szabályzatok hozhatók létre a felhőszolgáltatásokhoz, például az Exchange Online-hoz [való hozzáférés korlátozására](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) a jóváhagyott vagy kompatibilis alkalmazásokból.

Ha az alkalmazottak MAM-kompatibilis alkalmazásokat, például Office-mobilalkalmazásokat telepítenek a vállalati erőforrások, például az Exchange Online vagy a SharePoint Online eléréséhez, és ön is támogatja a BYOD-ot (saját eszközzel), javasoljuk, hogy telepítsen alkalmazásMAM-szabályzatokat a kezeléshez az alkalmazás konfigurációja a személyes tulajdonú eszközök nélkül MDM regisztráció, majd frissítse a feltételes hozzáférési szabályzatok csak a hozzáférést mam-képes ügyfelek.

![Feltételes hozzáférés-jogosultság i. vezérlő](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Ha az alkalmazottak mam-kompatibilis alkalmazásokat telepítenek a vállalati erőforrások ellen, és a hozzáférés korlátozott az Intune felügyelt eszközein, akkor érdemes megfontolnia az alkalmazás MAM-házirendek telepítését a személyes eszközök alkalmazáskonfigurációjának kezeléséhez, és a feltételes hozzáférési házirendek frissítése, hogy csak a MAM-képes ügyfelek től lehessen hozzáférni.

### <a name="conditional-access-implementation"></a>Feltételes hozzáférés megvalósítása

A feltételes hozzáférés a szervezet biztonsági állapotának javításához nélkülözhetetlen eszköz. Ezért fontos, hogy kövesse az alábbi ajánlott eljárásokat:

- Annak biztosítása, hogy minden SaaS-alkalmazás legalább egy házirendet alkalmaz
- Kerülje a **Minden alkalmazás** szűrő és a **blokkvezérlő** kombinálását a kizárási kockázat elkerülése érdekében
- Ne használja a **Minden felhasználó szűrőként,** és véletlenül adja hozzá **a Vendégeket**
- **Az összes "örökölt" szabályzat áttelepítése az Azure Portalra**
- A felhasználókra, eszközökre és alkalmazásokra vonatkozó összes feltétel lefedése
- Feltételes hozzáférési házirendek használata az [MFA megvalósításához,](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)nem pedig **felhasználónkénti többszintű többszintű hozzáférési**
- Rendelkezik egy kis alapvető szabályzatokkal, amelyek több alkalmazásra is alkalmazhatók
- Üres kivételcsoportok definiálása és hozzáadása a házirendekhez, hogy legyen kivételstratégiája
- [Üvegtörési](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency) fiókok tervezése Több-ellenőrző program ellenőrzése nélkül
- Egységes felhasználói élmény biztosítása az Office 365-beli ügyfélalkalmazásokban, például a Teamsben, a OneDrive Vállalati verzióban, az Outlookban stb.) az Exchange Online és a Sharepoint Online szolgáltatásokhoz hasonló szolgáltatások ugyanazon vezérlőkészletének
- A politikákhoz való hozzárendelést csoportokon keresztül kell végrehajtani, nem egyéneken keresztül
- Rendszeresen vizsgálja felül a házirendekben használt kivételcsoportokat, hogy korlátozza a felhasználók biztonsági helyzetből való ki, a biztonsági állapotkorlátját. Ha ön az Azure AD P2, akkor a hozzáférési felülvizsgálatok segítségével automatizálhatja a folyamatot

#### <a name="conditional-access-recommended-reading"></a>Feltételes hozzáférés ajánlott olvasása

- [Gyakorlati tanácsok a feltételes hozzáféréshez az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Identitás- és eszköz-hozzáférési konfigurációk](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Az Azure Active Directory feltételes hozzáférési beállításainak hivatkozása](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Gyakori feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Hozzáférési felület

### <a name="legacy-authentication"></a>Örökölt hitelesítés

Az erős hitelesítő adatok, például az MFA nem védhetik meg az örökölt hitelesítési protokollokkal használó alkalmazásokat, így a rosszindulatú szereplők előnyben részesített támadási vektora. Az örökölt hitelesítés zárolása elengedhetetlen a hozzáférési biztonsági állapot javításához.

Az örökölt hitelesítés olyan kifejezés, amely olyan alkalmazások által használt hitelesítési protokollokra vonatkozik, mint például:

- Régebbi Office-ügyfelek, amelyek nem használnak modern hitelesítést (például Office 2010-ügyfél)
- Levelezőprotokollokat, például IMAP/SMTP/POP protokollt használó ügyfelek

A támadók erősen kedvelik ezeket a protokollokat - valójában [a jelszószórásos támadások közel 100% -a](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) örökölt hitelesítési protokollokat használ! A hackerek örökölt hitelesítési protokollokat használnak, mert nem támogatják az interaktív bejelentkezést, ami további biztonsági kihívásokhoz, például a többtényezős hitelesítéshez és az eszközhitelesítéshez szükséges.

Ha az örökölt hitelesítést széles körben használják a környezetben, a lehető leghamarabb át kell telepítenie az örökölt ügyfeleket a [modern hitelesítést](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) támogató ügyfelekre. Ugyanebben a jogkivonatban, ha egyes felhasználók már modern hitelesítést használnak, mások azonban még mindig örökölt hitelesítést használnak, a következő lépéseket kell tennie az örökölt hitelesítési ügyfelek zárolásához:

1. [A Bejelentkezési tevékenység jelentések](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) segítségével azonosíthatja azokat a felhasználókat, akik még mindig használják az örökölt hitelesítést és a tervezés szervizelését:

   a. Frissítsen modern hitelesítésre alkalmas ügyfelekre az érintett felhasználókra.
   
   b. Tervezze nő, hogy az alábbi lépésekre lezárható.
   
   c. Azonosítsa, hogy milyen örökölt alkalmazások függnek az örökölt hitelesítéstől. Lásd az alábbi 3.

2. Tiltsa le az örökölt protokollokat a forrásnál (például exchange-postaláda) azon felhasználók számára, akik nem használnak örökölt hitelesítést a további expozíció elkerülése érdekében.
3. A fennmaradó fiókok (ideális esetben nem emberi identitások, például a szolgáltatás fiókok), [feltételes hozzáférés használatával korlátozza az örökölt protokollok](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) hitelesítés utáni.

#### <a name="legacy-authentication-recommended-reading"></a>Örökölt hitelesítés ajánlott olvasása

- [Pop3- vagy IMAP4-hozzáférés engedélyezése vagy letiltása postaládákhoz az Exchange Server kiszolgálón](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Hozzájárulási támogatás

Egy tiltott hozzájárulási engedély támadás, a támadó létrehoz egy Azure AD regisztrált alkalmazás, amely hozzáférést kér az adatokhoz, például kapcsolattartási adatok, e-mail, vagy dokumentumok. Előfordulhat, hogy a felhasználók adathalász támadások révén engedélyezik a rosszindulatú alkalmazásokat, amikor rosszindulatú webhelyeken landolnak.

Az alábbi lista azokat az alkalmazásokat tartalmazza, amelyek engedélyeit érdemes megvizsgálni a Microsoft felhőszolgáltatásaihoz:

- Alkalmazással rendelkező vagy \*delegált alkalmazások. ReadWrite engedélyek
- A delegált engedélyekkel rendelkező alkalmazások a felhasználó nevében olvashatják, küldhetik vagy kezelhetik az e-maileket
- A következő engedélyeket használó alkalmazások:

| Erőforrás | Engedély |
| :- | :- |
| Office 365 Exchange Online | Eas. AccessAsUser.All |
| | EwS. AccessAsUser.All |
| | E-mail.Read |
| Microsoft Graph API | E-mail.Read |
| | Levelek.Read.Megosztott |
| | Mail.ReadWrite |

- Az alkalmazások teljes felhasználói megszemélyesítést kaptak a bejelentkezett felhasználónak. Példa:

|Erőforrás | Engedély |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsuser.All |
| Azure REST API | user_impersonation |

Ennek a forgatókönyvnek a elkerülése érdekében olvassa el az [Office 365-ben a tiltott hozzájárulási támogatások észlelését és kijavítását](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) a szükségesnél több támogatással rendelkező, tiltott támogatással rendelkező alkalmazások vagy alkalmazások azonosításához és javításához. Ezután [távolítsa el az önkiszolgáló teljesen,](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) és [hozzon létre irányítási eljárásokat](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow). Végül ütemezze be az alkalmazásengedélyek rendszeres felülvizsgálatát, és távolítsa el őket, ha nincs rájuk szükség.

#### <a name="consent-grants-recommended-reading"></a>Hozzájárulási támogatás ajánlott olvasása

- [Microsoft Graph API-engedélyek](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Felhasználói és csoportbeállítások

Az alábbiakban felsoroljuk azon felhasználói és csoportbeállításokat, amelyek zárolhatók, ha nincs kifejezett üzleti igény:

#### <a name="user-settings"></a>Felhasználói beállítások

- **Külső felhasználók** – a külső együttműködés szervesen történhet a vállalaton belül olyan szolgáltatásokkal, mint a Teams, a Power BI, a Sharepoint Online és az Azure Information Protection. Ha explicit korlátozásokkal rendelkezik a felhasználó által kezdeményezett külső együttműködés szabályozásához, ajánlott engedélyezni a külső felhasználókat [az Azure AD-jogosultságok kezelésével](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) vagy egy ellenőrzött művelettel, például az ügyfélszolgálaton keresztül. Ha nem szeretné engedélyezni a szolgáltatások szerves külső együttműködését, letilthatja a tagok számára, hogy [teljesen külső felhasználókat hívjanak meg.](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations) Azt is megteheti, hogy [engedélyezi vagy letiltja az egyes tartományokat](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) a külső felhasználói meghívókban.
- **Alkalmazásregisztrációk** – ha az alkalmazásregisztrációk engedélyezve vannak, a végfelhasználók maguk is bevihetik az alkalmazásokat, és hozzáférést biztosíthatnak az adataikhoz. Az alkalmazásregisztráció tipikus példája, hogy a felhasználók engedélyezik az Outlook beépülő moduljait, vagy olyan hangasszisztensek, mint az Alexa és a Siri, elolvashatják e-mailjeiket és naptárukat, vagy e-maileket küldhetnek a nevükben. Ha az ügyfél úgy dönt, hogy kikapcsolja az alkalmazásregisztrációt, az InfoSec és az IAM-csapatoknak részt kell venniük a kivételek kezelésében (az üzleti követelmények alapján szükséges alkalmazásregisztrációk), mivel az alkalmazásokat rendszergazdai fiókkal kell regisztrálniuk, és valószínűleg a folyamat működőképessé tétele érdekében egy folyamat megtervezését igényli.
- **Felügyeleti portál** – a szervezetek zárolhatják az Azure AD-panelt az Azure Portalon, így a nem rendszergazdák nem férhetnek hozzá az Azure AD-kezeléshez az Azure Portalon, és összezavarodhatnak. Lépjen a felhasználói beállítások az Azure AD felügyeleti portálon a hozzáférés korlátozásához:

![Felügyeleti portál korlátozott hozzáférés](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> A nem rendszergazdák továbbra is hozzáférhetnek az Azure AD felügyeleti felületekhez parancssori és más programozott felületeken keresztül.

#### <a name="group-settings"></a>Csoportbeállítások

**Önkiszolgáló csoportkezelés / A felhasználók biztonsági csoportokat / O365 csoportokat hozhatnak létre.** Ha a felhőben lévő csoportok számára nincs aktuális önkiszolgáló kezdeményezés, az ügyfelek dönthetnek úgy, hogy kikapcsolják, amíg készen nem állnak a funkció használatára.

#### <a name="groups-recommended-reading"></a>Csoportok ajánlott olvasása

- [Mi az Azure Active Directory B2B együttműködés?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Alkalmazások integrálása az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Alkalmazások, engedélyek és hozzájárulás az Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Csoportok használata az erőforrásokhoz való hozzáférés kezeléséhez az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Önkiszolgáló alkalmazáshozzáférés-kezelés beállítása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Váratlan helyekről érkező forgalom

A támadók a világ különböző részeiről származnak. Ezt a kockázatot feltételes hozzáférési házirendek használatával kezelheti, amelynek feltétele a hely. A feltételes hozzáférési szabályzat [helyfeltétele](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) lehetővé teszi, hogy letiltsa a hozzáférést azon helyekhez, ahonnan nincs üzleti ok a bejelentkezésre.

![Új elnevezett hely létrehozása](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Ha rendelkezésre áll, használjon biztonsági információk és eseménykezelési (SIEM) megoldás segítségével a régiók közötti hozzáférési minták elemzéséhez és kereséséhez. Ha nem használ SIEM-terméket, vagy nem használja a hitelesítési adatokat az Azure AD-ből, azt javasoljuk, hogy az [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) használatával azonosítsa a különböző régiók közötti hozzáférési mintákat.

## <a name="access-usage"></a>Hozzáférés használata

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Az Azure AD-naplók archiválva és integrálva az incidens-választervekkel

A bejelentkezési tevékenységhez, az Azure AD naplózásához és kockázati eseményeihez való hozzáférés elengedhetetlen a hibaelhárításhoz, a használati elemzésekhez és a kriminalisztikai vizsgálatokhoz. Az Azure AD korlátozott megőrzési időszakkal rendelkező REST API-kon keresztül biztosít hozzáférést ezekhez a forrásokhoz. A biztonsági információs és eseménykezelési (SIEM) rendszer, vagy azzal egyenértékű archiválási technológia kulcsfontosságú az auditok és a támogathatóság hosszú távú tárolásához. Az Azure AD-naplók hosszú távú tárolásának engedélyezéséhez vagy hozzá kell adnia őket a meglévő SIEM-megoldáshoz, vagy használnia kell az [Azure Monitort.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) Archiválási naplók, amelyek az incidens-elhárítási tervek és vizsgálatok részeként használhatók.

#### <a name="logs-recommended-reading"></a>Naplók ajánlott olvasás

- [Az Azure Active Directory naplózási API-hivatkozása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Az Azure Active Directory bejelentkezési tevékenységjelentés API-jának hivatkozása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Adatok lekérése az Azure AD Reporting API és tanúsítványok használatával](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph az Azure Active Directory identitásvédelméhez](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Office 365 Felügyeleti tevékenység API–– referencia](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Az Azure Active Directory Power BI-tartalomcsomag használata](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúra 12 szempontot jelent. Ez a lista segít a hitelesítő adatok további védelmében és kezelésében, a hitelesítési élmény meghatározásában, a hozzárendelés delegálásában, a használat mérésében és a hozzáférési házirendek vállalati biztonsági helyzetalapján való meghatározásában.

- Rendeljen tulajdonosokat a legfontosabb feladatokhoz.
- Megoldásokat valósíthat meg a gyenge vagy kiszivárgott jelszavak észlelésére, a jelszókezelés és -védelem javítására, valamint a felhasználók erőforrásokhoz való további hozzáférésének további biztosítására.
- Az eszközök identitásának kezelése az erőforrások védelme érdekében bármikor és bárhol.
- Jelszó nélküli hitelesítés megvalósítása.
- Szabványosított egyszeri bejelentkezési mechanizmus biztosítása a szervezeten belül.
- Alkalmazások áttelepítése az AD FS-ről az Azure AD-be a nagyobb biztonság és a következetesebb kezelhetőség érdekében.
- Rendeljen felhasználókat az alkalmazásokhoz a csoportok használatával, hogy nagyobb rugalmasságot és képes kezelni a nagy méreteket.
- Konfigurálja a kockázatalapú hozzáférési házirendeket.
- Régi hitelesítési protokollok zárolása.
- A tiltott hozzájárulási támogatások felderítése és kiújítása.
- Felhasználói és csoportbeállítások zárolása.
- Az Azure AD-naplók hosszú távú tárolásának engedélyezése hibaelhárítási, használati elemzési és kriminalisztikai vizsgálatokhoz.

## <a name="next-steps"></a>További lépések

Ismerkedés az [identitás-irányítási működési ellenőrzésekkel és műveletekkel.](active-directory-ops-guide-govern.md)
