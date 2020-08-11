---
title: Öt lépés az összes alkalmazás integrálásához az Azure AD-vel
description: Ez az útmutató ismerteti, hogyan integrálható az összes alkalmazás az Azure AD-vel. Az egyes lépésekben leírjuk az értéket, és olyan erőforrásokra mutató hivatkozásokat adunk meg, amelyek leírják a technikai részleteket.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057254"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Öt lépés az összes alkalmazás integrálásához az Azure AD-vel

Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. Az Azure AD biztonságos hitelesítési és engedélyezési megoldásokat biztosít ahhoz, hogy az ügyfelek, partnerek és alkalmazottak hozzáférhessenek a szükséges alkalmazásokhoz. Az Azure AD-vel, a [feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), a [többtényezős hitelesítéssel](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks), az [egyszeri bejelentkezéssel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)és az [automatikus felhasználó-kiépítési](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) szolgáltatással egyszerűen és biztonságossá teheti az identitás-és hozzáférés-kezelést.

Ha a vállalata Microsoft 365-előfizetéssel rendelkezik, akkor valószínűleg [már használja](https://docs.microsoft.com/office365/enterprise/about-office-365-identity) az Azure ad-t. Az Azure AD azonban az összes alkalmazásához használható, és az [alkalmazások kezelésének központosításával](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios) a teljes alkalmazás-portfólióban ugyanazokat az Identitáskezelés-felügyeleti funkciókat, eszközöket és házirendeket használhatja. Ez egy egységes megoldást biztosít, amely javítja a biztonságot, csökkenti a költségeket, növeli a termelékenységet, és lehetővé teszi a megfelelőség biztosítását. A helyszíni alkalmazások távoli elérését is elérhetővé teszi.

Ez az útmutató ismerteti, hogyan integrálható az összes alkalmazás az Azure AD-vel. Az egyes lépésekben leírjuk az értéket, és olyan erőforrásokra mutató hivatkozásokat adunk meg, amelyek leírják a technikai részleteket. Ezeket a lépéseket a javasolt sorrendben mutatjuk be. Megkezdheti azonban a folyamat bármely részének megkezdését, ami a legértékesebb értéket adja meg Önnek.

A témakör további erőforrásai, beleértve az üzleti folyamatok részletes tanulmányait is, amelyek az [alkalmazások Azure Active Directory lapra való áttelepítésének forrásaiban](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) találhatók.

## <a name="1-use-azure-ad-for-new-applications"></a>1. az Azure AD használata új alkalmazásokhoz

Először is koncentráljon az újonnan beszerzett alkalmazásokra. Ha a vállalat új alkalmazást kezd használni, azonnal [adja hozzá az Azure ad-bérlőhöz](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) . Állítson be egy vállalati szabályzatot, hogy az új alkalmazások az Azure AD-be való hozzáadásával a szervezete szokásos gyakorlata legyen. Ez minimálisan zavaró a meglévő üzleti folyamatok esetében, és lehetővé teszi az alkalmazások integrálásának megvizsgálása és bizonyítása anélkül, hogy az emberek a környezetben használják az üzleti tevékenységet.

Azure Active Directory (Azure AD) gyűjteménye több ezer előre integrált alkalmazást tartalmaz, amelyek megkönnyítik az első lépéseket. Az [Azure ad-szervezethez hozzáadhat egy Gallery-alkalmazást](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) , amely részletes [oktatóanyagokat](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) biztosít a népszerű alkalmazások integrálásához, például:

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

Emellett a katalógusban [nem szereplő alkalmazásokat is integrálhat](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app), beleértve a szervezetében már létező alkalmazásait, vagy bármely olyan gyártótól származó alkalmazást, amely még nem része az Azure ad-katalógusnak. Azt is megteheti, [hogy hozzáadta az alkalmazást a](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing) katalógushoz, ha az nem létezik.

Végül integrálhatja a házon belül fejlesztett alkalmazásokat is. Ez a jelen útmutató ötödik lépésében szerepel.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. a meglévő alkalmazás-használat és a feladatok rangsorolásának meghatározása

Ezután Fedezze fel az alkalmazások alkalmazottai gyakran használják, és rangsorolja a munkáját, hogy integrálja őket az Azure AD-vel.

A Microsoft Cloud App Security&#39;s [Cloud Discovery Tools](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) használatával elkezdheti a &quot; hálózatban lévő árnyékok felderítését és kezelését &quot; (azaz az IT-részleg által nem kezelt alkalmazásokat). A felderítési folyamat egyszerűsítéséhez és bővítéséhez [használhatja a Microsoft Defender Advanced Threat Protection (ATP)](https://docs.microsoft.com/cloud-app-security/wdatp-integration) eszközt.

Emellett a Azure Portal [AD FS alkalmazás tevékenység jelentésével](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) felderítheti a szervezetben lévő összes AD FS alkalmazást, a számukra bejelentkezett egyedi felhasználók számát, és az Azure ad-vel való integráláshoz való kompatibilitást is.

Miután felfedezte a meglévő tájat, [létre kell hoznia egy tervet](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) , és rangsorolnia kell a legmagasabb prioritású alkalmazásokat az integráláshoz. Néhány példa arra, hogy a folyamat útmutatója a következő:

- Mely alkalmazásokat használják leginkább?
- Melyek a legkockázatosabb?
- Mely alkalmazások lesznek leszerelve a jövőben, így szükségtelen a költözés?
- Mely alkalmazásoknak kell a helyszínen maradni, és nem helyezhetők át a felhőbe?

Az alkalmazások integrálása után a legnagyobb előnyök és költségmegtakarítások jelennek meg, és már nem támaszkodhat több Identity megoldásra. Ugyanakkor könnyebb az Identitáskezelés és a fokozott biztonság, ahogy az a cél irányába mozdul el. Ezt az időt szeretné használni, hogy rangsorolja a munkáját, és döntse el, hogy milyen értelme van a helyzetnek.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. a más identitás-szolgáltatókra támaszkodó alkalmazások integrálása

A felderítési folyamat során előfordulhat, hogy az informatikai részleg által nyomon követett alkalmazásokat talált, így az adatai és erőforrásai sebezhetőek maradnak. Olyan alkalmazásokat is használhat, amelyek alternatív identitási megoldásokat használnak, beleértve a Active Directory összevonási szolgáltatások (AD FS) (ADFS) vagy más identitás-szolgáltatókat. Gondolja át, hogyan tudja összevonni az identitás-és hozzáférés-kezelést, hogy pénzt takarítson meg, és növelje a biztonságot. Az Ön által birtokolt személyazonossági megoldások számának csökkentése:

- A helyszíni felhasználók kiépítésének és hitelesítésének, valamint az ugyanazon szolgáltatáshoz tartozó más felhőalapú identitás-szolgáltatóknak fizetett licencelési díjaknak a megtakarításával pénzt takaríthat meg.
- Csökkentse az adminisztrációs terhelést, és engedélyezze a szigorúbb biztonságot az identitás-és hozzáférés-kezelési folyamat kevesebb redundanciával.
- Lehetővé teheti az alkalmazottak számára, hogy az [MyApps-portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)keresztül biztonságosan egyszeri bejelentkezéses hozzáférést kapjanak az összes szükséges alkalmazáshoz.
- Javítsa az Azure AD&#39;s [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) szolgáltatáshoz kapcsolódó szolgáltatásokat, például a feltételes hozzáférést, ha növeli az alkalmazás használatából származó adatok mennyiségét, és kibővíti az újonnan hozzáadott alkalmazások előnyeit.

Tettünk közzé útmutatást az alkalmazások Azure AD-vel való integrálásának üzleti folyamatának kezeléséhez, beleértve a [plakátot](https://aka.ms/AppOnePager) és a [bemutatót](https://aka.ms/AppGuideline) , amellyel az üzleti és az alkalmazás-tulajdonosok tudatában és érdeklik. Ezeket a mintákat saját arculatával módosíthatja, és közzéteheti azokat a céges portálon, a hírlevélen vagy más médiumon keresztül, amikor befejezi a folyamat befejezését.

A kezdéshez jó kiindulópont a Active Directory összevonási szolgáltatások (AD FS) (ADFS) használatának kiértékelése. Számos szervezet használja az ADFS-t az SaaS-alkalmazásokkal, az egyéni üzletági alkalmazásokkal és az Office 365 és az Azure AD-alapú alkalmazásokkal való hitelesítéshez:

![A diagramon a helyszíni alkalmazások, az üzletági alkalmazások, az SaaS-alkalmazások, valamint az Azure AD-n keresztül az Office 365 minden pontokkal összekapcsolható Active Directory és AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Ezt a konfigurációt úgy frissítheti, ha [lecseréli az ADFS](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) -t az Azure ad-be az identitáskezelési megoldás központjaként. Ez lehetővé teszi a bejelentkezést minden olyan alkalmazáshoz, amelyet az alkalmazottak szeretne elérni, és megkönnyíti az alkalmazottak számára a [MyApps-portálon](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)keresztül igénybe venni kívánt üzleti alkalmazások megtalálását a fent említett egyéb előnyök mellett.

![A diagram a helyi alkalmazásokat Active Directory és AD FS, üzletági alkalmazások, SaaS-alkalmazások és az Office 365 használatával jeleníti meg, amelyek mindegyike pontozott vonallal csatlakozik Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Ha az Azure AD a központi identitás-szolgáltató lesz, akkor lehetséges, hogy összevont megoldás helyett teljesen át tud váltani az ADFS-ből. Az olyan alkalmazások, amelyek korábban az ADFS-t használják a hitelesítéshez, mostantól használhatják az Azure AD-t.

![A diagram a helyszínen, az üzletági alkalmazásokban, az SaaS-alkalmazásokban és az Office 365-ben jeleníti meg az összes pontozott vonallal összekapcsolt Azure Active Directory. Active Directory és AD FS nincs jelen.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Áttelepíthet olyan alkalmazásokat is, amelyek eltérő felhőalapú identitás-szolgáltatót használnak az Azure AD-ben. Előfordulhat, hogy a szervezete több Identity Access-felügyeleti (IAM) megoldással rendelkezik. Az egyik Azure AD-infrastruktúrába való Migrálás lehetősége van arra, hogy csökkentse az IAM-licencek (helyszíni vagy Felhőbeli) és az infrastrukturális költségek függőségeit. Azokban az esetekben, amikor az Azure AD-t a M365-licenceken keresztül már kifizette, nem kell fizetnie egy másik IAM megoldás hozzáadott költségeinek.

## <a name="4-integrate-on-premises-applications"></a>4. helyszíni alkalmazások integrálása

Az alkalmazások védelme hagyományosan a vállalati hálózathoz való csatlakozáshoz való hozzáférés engedélyezésével történt. Az egyre szorosabban összekapcsolt világban azonban az ügyfelek, partnerek és/vagy alkalmazottak számára elérhetővé szeretnénk tenni az alkalmazásokhoz való hozzáférést, függetlenül attól, hogy hol vannak a világon. Az Azure [ad Application proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) az Azure ad egyik funkciója, amely összekapcsolja a meglévő helyszíni alkalmazásokat az Azure ad-vel, és nem igényli az Edge-kiszolgálók vagy más további infrastruktúra fenntartását.

![A diagram az alkalmazásproxy szolgáltatást mutatja működés közben. A felhasználó a (z) "", és a hozzá https://sales.contoso.com tartozó kérést átirányítja a (z) Azure Active Directory a helyszíni címen található " https://sales-contoso.msappproxy.net http://sales " helyre.](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Használhatja az [oktatóanyagot: helyi alkalmazás hozzáadása a távoli eléréshez](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) az alkalmazásproxy használatával Azure Active Directory az alkalmazásproxy engedélyezéséhez és egy helyszíni alkalmazás Azure ad-bérlőhöz való hozzáadásához.

Emellett integrálhatja az alkalmazások kézbesítési vezérlőit, például az F5 Big-IP APM vagy a Zscaler privát elérését. Ezeknek az Azure AD-val való integrálásával az Azure AD modern hitelesítését és identitás-kezelését a partner termék Traffic Management és Security funkciói mellett érheti el. Ezt a megoldást [biztonságos hibrid hozzáférésnek](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)nevezzük. Ha jelenleg a következő szolgáltatások valamelyikét használja, oktatóanyagokat talál, amelyek végigvezetik az Azure AD-vel való integrálás lépésein.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Citrix Application Deliver Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (korábbi nevén Citrix NetScaler)
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. a fejlesztők által felépített alkalmazások integrálása

A vállalaton belül létrehozott alkalmazások esetében a fejlesztők a [Microsoft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) segítségével implementálják a hitelesítést és az engedélyezést. A platformmal integrált alkalmazások [regisztrálva lesznek az Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) -ben, és ugyanúgy kezelhetők, mint bármely más alkalmazás a portfólióban.

A fejlesztők a platformot használhatják a belső használatra szánt alkalmazások és az ügyfelek felé irányuló alkalmazások számára is, és a platform használatának más előnyei is vannak. A platform részét képező [Microsoft Authentication librarys (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)lehetővé teszi a fejlesztők számára, hogy a többtényezős hitelesítéssel és a biztonsági kulcsok használatával hozzáférjenek az alkalmazásaihoz anélkül, hogy azt maguknak kellene implementálni. Emellett a Microsoft Identity platformmal integrált alkalmazások is hozzáférhetnek [Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) – egy egységes API-végpontot, amely a munkahelyen a termelékenység, az identitás és a biztonság mintáit leíró Microsoft 365-adatokkal rendelkezik. A fejlesztők ezeket az információkat olyan funkciók megvalósítására használhatják, amelyek növelhetik a hatékonyságot a felhasználók számára. Például azonosíthatja azokat a személyeket, akikkel a felhasználó a közelmúltban kommunikált, és az alkalmazás&#39;s felhasználói felületén felszínre került.

Egy videó- [sorozatot](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) biztosítunk, amely átfogó bevezetést nyújt a platformhoz, valamint számos, a támogatott nyelveken és platformokon elérhető [kódrészletet](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) .

## <a name="next-steps"></a>További lépések

- [Az alkalmazások Azure Active Directoryba való áttelepítéséhez szükséges erőforrások](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
