---
title: Microsoft 365 védelme a helyszíni támadásoktól
description: Útmutató a helyszíni támadások biztosításához, amely nem érinti Microsoft 365
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97893dece068dfdde85159f734095401288231d2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201348"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Microsoft 365 védelme a helyszíni támadásoktól

Számos ügyfél a saját vállalati hálózatait úgy kapcsolja össze, hogy Microsoft 365 a felhasználók, eszközök és alkalmazások előnyeit. Azonban számos jól dokumentált módon lehet ezeket a magánhálózatok biztonsága. Mivel a Microsoft 365 számos szervezet számára "idegrendszeri rendszernek" minősül, elengedhetetlen, hogy megvédje a feltört helyszíni infrastruktúrától.

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a rendszereket a Microsoft 365 felhőalapú környezetének a helyszíni támadásokkal szembeni védeleméhez. Elsődlegesen az Azure AD-bérlői konfigurációs beállításokra koncentrálunk, és az Azure AD-bérlők biztonságosan csatlakoztathatók a helyszíni rendszerekhez, és a rendszerek üzemeltetéséhez szükséges kompromisszumok olyan módon, hogy a felhőalapú rendszerek a helyszíni támadásokkal szemben is védve legyenek.

Javasoljuk, hogy ezt az útmutatót a Microsoft 365 felhőalapú környezetének biztonságossá tételéhez implementálja.
> [!NOTE]
> Ez a cikk eredetileg blogbejegyzésként jelent meg. A rendszer áthelyezte itt a tartósságot és a karbantartást. <br>
A jelen cikk offline verziójának létrehozásához használja a böngésző Nyomtatás PDF-funkcióját. Látogasson vissza ide gyakran a frissítésekhez.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Elsődleges fenyegetési vektorok a feltört helyszíni környezetekben


A Microsoft 365 felhőalapú környezete széles körű megfigyelési és biztonsági infrastruktúrát kínál. A gépi tanulás és az emberi intelligencia, amely az egész világra kiterjedő forgalmat vizsgálja, gyorsan képes felderíteni a támadásokat, és lehetővé teszi a közel valós idejű újrakonfigurálást. A helyszíni infrastruktúrát Microsoft 365hoz csatlakozó hibrid környezetekben számos szervezet delegálja a megbízható hitelesítéshez és a címtár-objektumok állapotának kezelésére vonatkozó döntéseket a helyszíni összetevőkhöz.
Sajnos, ha a helyszíni környezet biztonsága sérül, a megbízhatósági kapcsolatok a támadók számára a Microsoft 365-környezet megtámadását eredményezik.

A két elsődleges veszélyforrás vektor az **összevonási megbízhatósági kapcsolatok** és a **fiók szinkronizálása.** Mindkét vektor megadhatja a támadónak a rendszergazdai hozzáférést a felhőhöz.

* Az **összevont megbízhatósági kapcsolatok**, például az SAML-hitelesítés a helyszíni identitás-infrastruktúrán keresztül történő Microsoft 365 hitelesítésére szolgálnak. Ha egy SAML-jogkivonat aláíró tanúsítványa sérült, az összevonás lehetővé teszi, hogy a tanúsítvány bárkivel megszemélyesítse a felhőben lévő felhasználókat. **Javasoljuk, hogy ha lehetséges, tiltsa le az összevonási megbízhatósági kapcsolatokat a hitelesítéshez Microsoft 365.**

* A **fiók szinkronizálásával** módosíthatók a Kiemelt felhasználók (beleértve a hitelesítő adataikat is), illetve a Microsoft 365 felügyeleti jogosultságokkal rendelkező csoportok is. **Azt javasoljuk, hogy a szinkronizált objektumok ne tartsanak jogosultságot a Microsoft 365 felhasználóján kívül közvetlenül,** vagy a megbízható szerepkörök vagy csoportok belefoglalása révén. Győződjön meg arról, hogy ezek az objektumok nem rendelkeznek közvetlen vagy beágyazott hozzárendeléssel a megbízható Felhőbeli szerepkörökben vagy csoportokban.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Microsoft 365 védelme a helyszíni támadásokkal szemben


A fentiekben ismertetett veszélyforrás-vektorok kezeléséhez javasoljuk, hogy tartsa be az alábbi ábrákat:

![Hivatkozási architektúra a Microsoft 365 védelméhez ](media/protect-m365/protect-m365-principles.png)

*  **A Microsoft 365-rendszergazdai fiókok teljes elkülönítése.** A

    * Az Azure AD-ben elsajátítva.

     * Hitelesítés többtényezős hitelesítéssel (MFA).

     *  Az Azure AD feltételes hozzáférése biztosítja.

     *  Csak az Azure által felügyelt munkaállomások használatával érhető el.

Ezek korlátozott használati fiókok. **A Microsoft 365 rendszergazdai jogosultságokkal nem rendelkező helyszíni fiókoknak kell lenniük.** További információ: [Microsoft 365 rendszergazdai szerepkörök áttekintése](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).
Tekintse meg a [Azure Active Directory Microsoft 365 szerepkörei](../roles/m365-workload-docs.md)című témakört is.

*  **Eszközök kezelése Microsoft 365ról.** Az Azure AD JOIN és a felhőalapú mobileszköz-kezelés (MDM) használatával megkerülheti a helyszíni eszközkezelés infrastruktúrájának függőségeit, amelyek veszélyeztethetik az eszköz-és biztonsági vezérlőket.

* **Egy helyszíni fiók nem rendelkezik emelt szintű jogosultságokkal a Microsoft 365hoz.**
    Azok a fiókok, amelyek NTLM-, LDAP-vagy Kerberos-hitelesítést igénylő helyszíni alkalmazásokhoz férnek hozzá, szükségük van egy fiókra a szervezet helyi identitás-infrastruktúrájában. Győződjön meg arról, hogy ezek a fiókok, köztük a szolgáltatásfiókok, nem szerepelnek a Kiemelt Felhőbeli szerepkörökben vagy csoportokban, és hogy ezek a fiókok módosításai nem befolyásolhatják a felhőalapú környezet integritását. A Kiemelt helyszíni szoftverek nem képesek befolyásolni Microsoft 365 emelt szintű fiókokat vagy szerepköröket.

*  Az **Azure ad Felhőbeli hitelesítéssel** távolítsa el a helyszíni hitelesítő adataival kapcsolatos függőségeket. Mindig erős hitelesítést használjon, például a Windows Hello, a vagy a Microsoft Authenticator vagy az Azure AD MFA-t.

## <a name="specific-recommendations"></a>Konkrét javaslatok


A következő szakaszokban részletes útmutatást talál a fent ismertetett alapelvek megvalósítására vonatkozóan.

### <a name="isolate-privileged-identities"></a>Emelt szintű identitások elkülönítése


Az Azure AD-ben az olyan Kiemelt szerepkörökkel rendelkező felhasználók, mint a rendszergazdák, a többi környezet létrehozásához és kezeléséhez szükséges megbízhatósági kapcsolatok. A kompromisszum hatásának minimalizálásához hajtsa végre az alábbi eljárásokat.

* Csak felhőalapú fiókokat használjon az Azure AD-hez és Microsoft 365 Kiemelt szerepkörökhöz. d

* Az Microsoft 365 és az Azure AD felügyeletéhez emelt szintű jogosultságú hozzáférési [eszközök](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) helyezhetők üzembe.

*  [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) üzembe helyezése az összes Kiemelt szerepkörrel rendelkező emberi fiókhoz (JIT), és erős hitelesítés szükséges a szerepkörök aktiválásához.

* Adja meg a felügyeleti szerepköröket, amelyek a [lehető legkevesebb jogosultsággal rendelkeznek a feladatok elvégzéséhez](../roles/delegate-by-task.md).

* Egy olyan sokoldalú szerepkör-hozzárendelési élmény engedélyezéséhez, amely egyszerre több delegálást és több szerepkört is tartalmaz, érdemes lehet Azure AD-alapú biztonsági csoportokat vagy Microsoft 365 csoportokat (együttesen "felhőalapú csoportok") használni, és [engedélyezni a szerepköralapú hozzáférés-vezérlést](../roles/groups-assign-role.md). A szerepkörök hatókörét [felügyeleti egységekkel](../roles/administrative-units.md) is korlátozhatja a szervezet egy részére.

* Helyezzen üzembe [vészhelyzeti hozzáférési fiókokat](../roles/security-emergency-access.md) , és ne használjon helyszíni jelszavas tárolókat a hitelesítő adatok tárolásához.

További információ: a [privilegizált hozzáférés biztonságossá tétele](https://aka.ms/SPA), amely részletes útmutatást tartalmaz ebben a témakörben. Lásd még: [biztonságos hozzáférési eljárások a rendszergazdák számára az Azure ad-ben](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Felhőalapú hitelesítés használata 

A hitelesítő adatok elsődleges támadási vektorok. A hitelesítő adatok biztonságosabbá tételéhez hajtsa végre az alábbi eljárásokat.

* [Jelszóval nem rendelkező hitelesítés üzembe helyezése](../authentication/howto-authentication-passwordless-deployment.md): a lehető legnagyobb mértékben csökkentse a jelszavak használatát a jelszóval nem rendelkező hitelesítő adatok telepítésével. Ezek a hitelesítő adatok a felhőben natív módon kezelhetők és ellenőrizhetők. A következő lehetőségek közül választhat:

   * [Vállalati Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Authenticator alkalmazás](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 biztonsági kulcsok](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Multi-Factor Authentication üzembe helyezése](https://aka.ms/deploymentplans/mfa): [több erős hitelesítő adat kiépítése az Azure ad MFA használatával](../fundamentals/resilience-in-credentials.md). Így a felhőalapú erőforrásokhoz való hozzáféréshez az Azure AD-ben felügyelt hitelesítő adatokra is szükség van, valamint egy helyi jelszó, amelyet kezelhet.

   * További információ: [rugalmas hozzáférés-vezérlési kezelési stratégia létrehozása az Azure Active Directoryval](https://aka.ms/resilientaad).

**Korlátozások és kompromisszumok**

* A hibrid fiók jelszavas kezelése olyan hibrid összetevőket igényel, mint például a jelszavas védelem ügynökei és a jelszó-visszaírási ügynökök. Ha a helyszíni infrastruktúra biztonsága sérül, a támadók vezérelhetik azokat a gépeket, amelyeken ezek az ügynökök találhatók. Habár ez nem veszélyezteti a felhőalapú infrastruktúrát, a felhőalapú fiókok nem védik ezeket az összetevőket a helyszíni kompromisszumból.

*  A Active Directoryról szinkronizált helyszíni fiókok soha nem járnak le az Azure AD-ben, azon feltételezés alapján, hogy a helyszíni AD-jelszavak szabályzata csökkenti ezt. Ha a helyszíni AD-t feltörték, és az AD-kapcsolatról való szinkronizálást le kell tiltani, be kell állítania a [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md)beállítást.

## <a name="provision-user-access-from-the-cloud"></a>Felhasználói hozzáférés kiépítése a felhőből

A kiépítés a felhasználói fiókok és csoportok alkalmazásokban vagy identitás-szolgáltatókban való létrehozására utal.

![A kiépítési architektúra ábrája](media/protect-m365/protect-m365-provision.png)

* **FELHŐALAPÚ HR-alkalmazások kiépítése az Azure ad-be:** Ez lehetővé teszi a helyszíni kompromisszumok elkülönítését anélkül, hogy a Felhőbeli HR-alkalmazásaiból az Azure AD-be nem zavarja az összekötő-mozgató-elhagyó ciklust.

* **Felhőalapú alkalmazások:** Ahol lehetséges, [Azure ad alkalmazás](../app-provisioning/user-provisioning.md) üzembe helyezést a helyszíni kiépítési megoldásokkal szemben. Ezzel biztosíthatja, hogy az SaaS-alkalmazások némelyike rosszindulatú felhasználói profilokkal legyen megmérgezve a helyszíni behatolások miatt. 

* **Külső identitások:** Az [Azure ad B2B együttműködés](../external-identities/what-is-b2b.md)használata.
    Ez csökkenti a helyszíni fiókoktól való függőséget a partnerekkel, ügyfelekkel és szállítókkal való külső együttműködés érdekében. Alaposan értékelje ki a közvetlen összevonást más identitás-szolgáltatókkal. Javasoljuk, hogy az alábbi módokon korlátozzák a B2B-vendég fiókjainak korlátozását.

   *  A címtárban lévő tallózási csoportok és egyéb tulajdonságok elérésének korlátozása. A külső együttműködési beállítások használatával korlátozhatja a vendég azon csoportok olvasását, amelyek nem tagjai a csoportnak. 

    *   A Azure Portal elérésének letiltása. Ritkán szükséges kivételeket hozhat.  Hozzon létre egy feltételes hozzáférési szabályzatot, amely tartalmazza az összes vendéget és a külső felhasználókat, majd [a hozzáférés letiltásához alkalmazza a szabályzatot](/azure/role-based-access-control/conditional-access-azure-management). 

* **Leválasztott erdők:** Az [Azure ad Cloud kiépítés](../cloud-provisioning/what-is-cloud-provisioning.md)használata. Ez lehetővé teszi a leválasztott erdőkhöz való csatlakozást, így nincs szükség erdők közötti kapcsolat vagy megbízhatósági kapcsolatok létrehozására, ami kibővítheti a helyszíni szabálysértés hatását. * 
 
**Korlátozások és kompromisszumok:**

* Hibrid fiókok kiépítéséhez a Felhőbeli HR rendszerekről származó Azure AD-t a helyszíni szinkronizálás alapján kell végrehajtani az AD-ből az Azure AD-be való adatforgalom befejezéséhez. Ha a szinkronizálás megszakad, az új alkalmazotti rekordok nem lesznek elérhetők az Azure AD-ben.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Felhőalapú csoportok használata együttműködéshez és hozzáféréshez

A felhőalapú csoportok lehetővé teszik az együttműködés és a helyszíni infrastruktúrák elérését.

* **Együttműködés:** A modern együttműködéshez Microsoft 365 csoportokat és Microsoft Teams-t használhat. Kiszerelheti a helyszíni terjesztési listát, és [frissítheti a terjesztési listát az outlookban Microsoft 365 csoportokba](https://docs.microsoft.com/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Hozzáférés:** Azure AD-beli biztonsági csoportokat vagy Microsoft 365 csoportokat használhat az Azure AD-beli alkalmazásokhoz való hozzáférés engedélyezéséhez.
* **Office 365 licencelés:** A csoport alapú licencelés használatával kiépítheti az Office 365-et csak felhőalapú csoportok használatával. Ez leválasztja a csoporttagság felügyeletét a helyszíni infrastruktúrából.

A hozzáféréshez használt csoportok tulajdonosait emelt szintű identitásnak kell tekinteni, hogy elkerülje a tagság a helyszíni kompromisszumból való átvételét.
Az átvétel magában foglalja a csoporttagság közvetlen kezelését a helyszíni vagy a helyszíni attribútumok manipulációját, amely hatással lehet a dinamikus csoporttagság Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Eszközök kezelése a felhőből


Az Azure AD-funkciókkal biztonságosan felügyelheti az eszközöket.

-   **Windows 10 rendszerű munkaállomások használata:** az [Azure ad-hez csatlakoztatott eszközök üzembe helyezése](../devices/azureadjoin-plan.md) Mdm-házirendekkel. A [Windows Autopilot](https://docs.microsoft.com/mem/autopilot/windows-autopilot) engedélyezése teljesen automatizált üzembe helyezési élményhez.

    -   A Windows 8,1 és a korábbi gépek elavultak.

    -   A kiszolgáló operációs rendszerű gépeket ne telepítse munkaállomásként.

    -   Használja a [Microsoft Intunet](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) az összes Eszközkezelő számítási feladat forrásaként.

-   Az Microsoft 365 és az Azure AD felügyeletéhez emelt szintű jogosultságú hozzáférési [**eszközök helyezhetők üzembe**](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) .

 ## <a name="workloads-applications-and-resources"></a>Munkaterhelések, alkalmazások és erőforrások 

-   Helyszíni **SSO-rendszerek:** A helyszíni összevonási és webes hozzáférés-kezelési infrastruktúra elavult, és az alkalmazások konfigurálását az Azure AD használatára.  

-   A **modern hitelesítési protokollokat támogató SaaS-és LOB-alkalmazások:** az [Azure ad használata az egyszeri bejelentkezéshez](../manage-apps/what-is-single-sign-on.md). Minél több alkalmazás van konfigurálva az Azure AD hitelesítéshez való használatára, annál kisebb a kockázata a helyszíni kompromisszumok esetén.


* **Örökölt alkalmazások** 

   * A modern hitelesítést nem támogató örökölt alkalmazásokhoz való hitelesítés, engedélyezés és távelérés az [Azure ad Application proxy](../manage-apps/application-proxy.md)használatával engedélyezhető. A  [biztonságos hibrid hozzáférési partner-integrációk](../manage-apps/secure-hybrid-access.md)használatával hálózati vagy Application Delivery Controller-megoldáson keresztül is engedélyezhetők.   

   * Válasszon olyan VPN-gyártót, amely támogatja a modern hitelesítést, és integrálja a hitelesítését az Azure AD-vel. Az Anon helyszíni biztonsága esetén az Azure AD használatával letilthatja vagy letilthatja a hozzáférést a VPN letiltásával.

*  **Alkalmazás-és munkaterhelés-kiszolgálók**

   * Azok az alkalmazások vagy erőforrások, amelyeket a szükséges kiszolgálók áttelepíthetnek az Azure IaaS, és a [Azure ad Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) (Azure AD DS) használatával elválaszthatja a megbízhatóságot és a helyszíni ad-függőséget. A leválasztás eléréséhez az Azure AD DS használt virtuális hálózatok nem rendelkezhetnek a vállalati hálózatokhoz való csatlakozással.

   * Kövesse a [hitelesítő adatokra vonatkozó rétegek](https://aka.ms/TierModel)útmutatását. Az alkalmazás-kiszolgálók általában 1. szintű eszközöknek tekintendők.

 ## <a name="conditional-access-policies"></a>Feltételes hozzáférési szabályzatok

Az Azure AD feltételes hozzáférés használatával a jeleket értelmezheti, és rajtuk alapuló hitelesítési döntéseket hozhat. További információkért lásd a [feltételes hozzáférés telepítési tervét.](https://aka.ms/deploymentplans/ca)

* [Örökölt hitelesítési protokollok](../fundamentals/auth-sync-overview.md): ha lehetséges, a feltételes hozzáférés használatával [blokkolhatja az örökölt hitelesítési](../conditional-access/howto-conditional-access-policy-block-legacy.md) protokollokat. Emellett az alkalmazás szintjén tiltsa le az örökölt hitelesítési protokollokat az alkalmazásspecifikus konfigurációk használatával.

   * Tekintse meg az [Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) és a [SharePoint Online](https://docs.microsoft.com/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps)adott részleteit.

* A javasolt [identitás-és eszköz-hozzáférési konfigurációk](https://docs.microsoft.com/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide) implementálása.

* Ha olyan Azure AD-verziót használ, amely nem tartalmaz feltételes hozzáférést, győződjön meg arról, hogy az [Azure ad biztonsági alapértékeit](../fundamentals/concept-fundamentals-security-defaults.md)használja.

   * Az Azure AD szolgáltatás licencelésével kapcsolatos további információkért tekintse meg az [Azure ad díjszabási útmutatóját](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitoring"></a>Figyelés 

Ha úgy konfigurálta a környezetet, hogy az Microsoft 365t a helyszíni támadásokkal szemben, [proaktív módon figyelje](../reports-monitoring/overview-monitoring.md) a környezetet.
### <a name="scenarios-to-monitor"></a>Figyelési forgatókönyvek

Figyelje meg a következő, a szervezetre vonatkozó forgatókönyvek melletti forgatókönyveket. Például proaktívan figyelnie kell az üzleti szempontból kritikus fontosságú alkalmazásaihoz és erőforrásaihoz való hozzáférést.

* **Gyanús tevékenység**: az összes [Azure ad kockázati eseményt](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#risk-detection-and-remediation) figyelni kell a gyanús tevékenységekre. A [Azure ad Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) natív módon van integrálva a Azure Security Centerval.

   * A hely alapú jelek zajos észlelésének elkerülése érdekében adja meg a [helyek nevű](../reports-monitoring/quickstart-configure-named-locations.md) hálózatot. 
*  **Felhasználói entitás viselkedés-elemzési (UEBA) riasztásai** A UEBA használatával bepillantást nyerhet a anomáliák észlelésére.
   * Microsoft Cloud app Discovery (MCAS) [a felhőben biztosít UEBA](https://docs.microsoft.com/cloud-app-security/tutorial-ueba).

   * A helyszíni [UEBA az Azure ATP-ből is integrálhatja](https://docs.microsoft.com/defender-for-identity/install-step2). A MCAS beolvassa a jeleket a Azure AD Identity Protectionból. 

* **Vészhelyzeti hozzáférési fiókok tevékenység**: a [vészhelyzeti hozzáférési fiókokat](../roles/security-emergency-access.md) használó hozzáféréseket figyelni kell, és riasztásokat kell létrehozni a vizsgálatokhoz. A figyelésnek a következőket kell tartalmaznia: 

   * Bejelentkezések. 

   * Hitelesítőadat-kezelés. 

   * A csoporttagságok frissítései. 

   *    Alkalmazás-hozzárendelések. 
* **Kiemelt szerepköri tevékenység**: [Az Azure ad PIM által létrehozott biztonsági riasztások](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-configure-security-alerts?tabs=new#security-alerts)konfigurálása és áttekintése.
    A rendszerjogosultságú szerepkörök a PIM-n kívüli közvetlen hozzárendelésének figyelése riasztások létrehozásával, amikor egy felhasználó közvetlenül van hozzárendelve.
* **Azure ad-bérlőre kiterjedő konfigurációk**: a bérlői szintű konfigurációk bármilyen módosítása esetén riasztásokat kell készíteni a rendszeren. Ilyenek például a következők:
  *  Egyéni tartományok frissítése  

  * Az Azure AD B2B engedélyezése/tiltása lista módosításai.
  * Az Azure AD B2B által engedélyezett Identitáskezelés (SAML-IDP közvetlen összevonással vagy közösségi bejelentkezéssel).  
  * Feltételes hozzáférés vagy kockázati szabályzat módosításai 

* **Alkalmazás-és egyszerű szolgáltatások objektumai**:
   * Új alkalmazások vagy egyszerű szolgáltatások, amelyek feltételes hozzáférési szabályzatokat igényelhetnek. 

   * Az egyszerű szolgáltatásokhoz hozzáadott további hitelesítő adatok.
   * Alkalmazás-engedélyezési tevékenység. 

* **Egyéni szerepkörök**:
   * Az egyéni szerepkör-definíciók frissítései. 

   * Új egyéni szerepkörök létrehozva. 

### <a name="log-management"></a>Naplózás kezelése

Meghatározhatja a naplózási tárolási és adatmegőrzési stratégiát, a tervezést és a megvalósítást, hogy olyan egységes eszközkészletet hozzon létre, mint például az olyan SIEM-rendszerek, mint például az Azure Sentinel, a gyakori lekérdezések, valamint a nyomozás

* **Azure ad-naplók** Az egységes ajánlott eljárások, például a diagnosztikai beállítások, a naplózási megőrzés és az SIEM-feldolgozás követése során létrehozott naplók és jelek betöltése. A naplózási stratégiának tartalmaznia kell a következő Azure AD-naplókat:
   * Bejelentkezési tevékenység 

   * Naplók 

   * Kockázati események 

Az Azure AD [Azure monitor integrációt](../reports-monitoring/concept-activity-logs-azure-monitor.md) biztosít a bejelentkezési tevékenység naplójának és a naplóinak. A kockázati események [Microsoft Graph API](https://aka.ms/AzureADSecuredAzure/32b)-n keresztül tölthetők be. [Az Azure ad-naplókat az Azure monitor naplóiba is továbbíthatja](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **A hibrid infrastruktúra operációs rendszerének biztonsági naplói.** Az összes hibrid identitás-infrastruktúra operációsrendszer-naplóját archiválni kell, és alaposan figyelni kell <br>A 0. rétegű rendszerek a felszín területtel kapcsolatos következményeiket tekintve. Ide tartoznak az alábbiak: 

   *  Azure AD Connect. Az identitás-szinkronizálás figyeléséhez [Azure ad Connect Health](https://aka.ms/AzureADSecuredAzure/32e) kell telepíteni.

   *  Alkalmazásproxy-ügynökök 


   * Jelszó-visszaírást támogató ügynökök 

   * Jelszavas védelem átjárójának gépei  

   * Az Azure MFA RADIUS-bővítményt tartalmazó hálózati házirend-kiszolgáló 

## <a name="next-steps"></a>Következő lépések
* [A rugalmasság beépítése az identitás- és hozzáférés-kezelésbe az Azure AD használatával](resilience-overview.md)

* [Biztonságos külső hozzáférés az erőforrásokhoz](secure-external-access-resources.md) 
* [Az összes alkalmazás integrálása az Azure AD-vel](five-steps-to-full-application-integration-with-azure-ad.md)
