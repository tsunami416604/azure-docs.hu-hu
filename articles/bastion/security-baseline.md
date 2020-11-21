---
title: Azure Security-alaptervek az Azure-hoz
description: Az Azure Bastion biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 007b82b4466dfc2fbfc9c11340583175da45d92e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025815"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Azure Security-alaptervek az Azure-hoz

Ez a biztonsági alapterv az [Azure Security teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) az Azure Bastion-re vonatkozó útmutatást alkalmazza. Az Azure biztonsági teljesítményteszt az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. A tartalom az Azure Security benchmark által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure-ra vonatkozó kapcsolódó útmutatás. Az Azure Bastion-re nem alkalmazható **vezérlők** ki lettek zárva.

Ha szeretné megtekinteni, hogy az Azure Bastion hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg az Azure Security alapkonfiguráció- [hozzárendelési fájl](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security)című témakörben talál.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

**Útmutató**: az Azure-beli megerősített erőforrások üzembe helyezésekor létre kell hoznia vagy használnia kell egy meglévő virtuális hálózatot. Győződjön meg arról, hogy az összes Azure-beli virtuális hálózat olyan vállalati szegmentálási elvet követ, amely az üzleti kockázatokhoz igazodik. A szervezet számára nagyobb kockázatot jelentő rendszereknek el kell különíteni a saját virtuális hálózatán belül, és megfelelően biztonságossá kell tenniük egy hálózati biztonsági csoportot (NSG).

Az Azure Bastion szolgáltatás használatához a következő portokat kell megnyitni a szolgáltatás megfelelő működéséhez:

- Bejövő forgalom:
   - Forgalom beáramlása a nyilvános internetről: az Azure Bastion olyan nyilvános IP-címet hoz létre, amely a nyilvános IP-címen engedélyezve van a 443-as porton a bejövő forgalom számára. A AzureBastionSubnet nem szükséges megnyitni a 3389/22-es portot. 

   - Forgalom beáramlása az Azure-ban – az Azure Bastion-vezérlő síkja: a vezérlési sík kapcsolathoz engedélyezze a 443-es portot a GatewayManager szolgáltatás címkéjén. Ez lehetővé teszi, hogy az átjáró-kezelő képes legyen kommunikálni az Azure Bastion-szel.

- Kimenő forgalom:

   - Kimenő forgalom a célként megadott virtuális gépekre (VM): az Azure Bastion privát IP-címmel éri el a célként megadott virtuális gépeket. A NSG engedélyeznie kell a kimenő forgalmat más cél virtuálisgép-alhálózatokhoz a 3389-es és 22-es porton.

   - Kimenő forgalom az Azure más nyilvános végpontjai számára: az Azure Bastion-nek képesnek kell lennie az Azure-on belüli különböző nyilvános végpontokhoz való kapcsolódásra (például diagnosztikai naplók és mérési naplók tárolására). Ezért az Azure Bastion-nek 443-re kell AzureCloud a szolgáltatási címkéhez.

Az Azure-tanúsítványok az átjáró-kezelőhöz és az Azure-szolgáltatási címkéhez való kapcsolódást (zárolt) védik. A külső entitások, beleértve az erőforrások felhasználóit, nem tudnak kommunikálni ezeken a végpontokon. 

- [Hálózati biztonsági csoport létrehozása biztonsági szabályokkal](../virtual-network/tutorial-filter-network-traffic.md)

- [További információt a megerősített NSG-követelményről itt talál](bastion-nsg.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása

**Útmutató**: az Azure Bastion nem tesz elérhetővé olyan végpontokat, amelyek egy magánhálózaton keresztül érhetők el. Az Azure Bastion támogatja az üzembe helyezést egy társ hálózatban, hogy központosítsa a megerősített üzembe helyezést, és engedélyezze a hálózat közötti kapcsolatot.

- [Az Azure Bastion és a virtuális hálózat társítása](vnet-peering.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információt az [Azure biztonsági teljesítményteszt: Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management)című témakörben talál.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: szabványosított Azure Active Directory központi identitás-és hitelesítési rendszerrel

**Útmutató**: az Azure Bastion integrálva van Azure Active Directory (Azure ad) szolgáltatással, amely az Azure alapértelmezett identitás-és hozzáférés-kezelési szolgáltatása. A felhasználók az Azure AD-hitelesítés használatával férhetnek hozzá a Azure Portalhoz az Azure Bastion szolgáltatás kezeléséhez (a megerősített erőforrások létrehozásához, frissítéséhez és törléséhez).

Az Azure Bastion használatával a virtuális gépekhez való csatlakozás SSH-kulcson vagy felhasználónévn/jelszón alapul, és jelenleg nem támogatja az Azure AD-beli hitelesítő adatok használatát.

Egy SSH-kulcson vagy felhasználónéven/jelszón kívül, amikor a virtuális gépekhez az Azure Bastion használatával csatlakozik, a felhasználónak a következő szerepkör-hozzárendelésekre lesz szüksége:
- Olvasó szerepkör a cél virtuális gépen
- Olvasó szerepkör a hálózati adapteren a cél virtuális gép magánhálózati IP-címével
- Olvasó szerepkör az Azure Bastion-erőforrásban

További információkért tekintse meg a következő referenciákat:

- [Kapcsolódás Linux rendszerű virtuális géphez az Azure Bastion használatával](bastion-connect-vm-ssh.md)

- [Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával](bastion-connect-vm-rdp.md)

- [Beépített Azure-szerepkörök](../role-based-access-control/built-in-roles.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: az Azure AD egyszeri bejelentkezés (SSO) használata az alkalmazásokhoz való hozzáféréshez

**Útmutató**: az Azure Bastion nem támogatja az egyszeri bejelentkezést a virtuális gépi erőforrásokhoz való hitelesítéskor, csak az ssh vagy a username/Password támogatott. Az Azure Bastion azonban Azure Active Directory (Azure AD) használatával biztosítja az identitás-és hozzáférés-kezelést a teljes szolgáltatáshoz. A felhasználók hitelesíthetők az Azure AD-ben az Azure-beli megerősített erőforrásaik eléréséhez és kezeléséhez, és zökkenőmentes egyszeri bejelentkezést végezhetnek saját szinkronizált vállalati identitásokkal Azure AD Connect használatával. 

- [Alkalmazás egyszeri bejelentkezésének megismerése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure AD-kapcsolat](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [Kapcsolódás Linux rendszerű virtuális géphez az Azure Bastion használatával](bastion-connect-vm-ssh.md)

- [Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával](bastion-connect-vm-rdp.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: erős hitelesítési vezérlők használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure Bastion integrálva van Azure Active Directory (Azure ad) szolgáltatással a szolgáltatás eléréséhez és kezeléséhez. Konfigurálja az Azure-Multi-Factor Authentication az Azure AD-bérlőhöz. Az Azure AD a többtényezős hitelesítéssel (MFA) és az erős, jelszóval nem rendelkező metódusokkal támogatja az erős hitelesítési vezérlést.  
- Többtényezős hitelesítés: engedélyezze az Azure AD MFA-t, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait az MFA-telepítéshez. Az MFA kikényszeríthető minden felhasználóra, kiválaszthatja a felhasználókat vagy a felhasználónkénti szinten a bejelentkezési feltételek és kockázati tényezők alapján. 

- Jelszóval nem rendelkező hitelesítés: három jelszóval nem rendelkező hitelesítési lehetőség érhető el: a vállalati Windows Hello, Microsoft Authenticator alkalmazás és a helyszíni hitelesítési módszerek, például az intelligens kártyák. 

- [Az MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Bevezetés a Azure Active Directory jelszavas hitelesítési lehetőségeibe](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: a fiókok rendellenességeit figyelő és riasztások

**Útmutató**: engedélyezze a diagnosztikai naplókat az Azure Bastion távoli munkamenetekhez, és ezeket a naplókat használva megtekintheti, hogy mely felhasználók mely munkaterhelésekhez kapcsolódnak, milyen időpontban, hol és más hasonló naplózási információkkal. Riasztásokat hozhat létre a naplókban észlelt rendellenességek észlelése esetén, Azure Monitor használatával értesítést kaphat az egyes naplózott megerősített munkamenetekről.

- [A diagnosztikai naplózás engedélyezéséről itt talál további információt](diagnostic-logs.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: az Azure erőforrás-hozzáférés korlátozása feltételek alapján

**Útmutató**: csak az Azure Bastion szolgáltatáshoz férhet hozzá a Azure Portalon keresztül, Azure Portal hozzáférését korlátozhatja Azure Active Directory (Azure ad) feltételes hozzáférés használatával. Az Azure AD feltételes hozzáférés használatával részletesebb hozzáférés-vezérlést használhat a felhasználó által definiált feltételek alapján, például bizonyos IP-címtartományok felhasználói bejelentkezésének megkövetelése az MFA használatára.

Az ügyfél a tartományhoz csatlakoztatott virtuális gépek szintjén eltérő szerepköralapú hozzáférés-vezérlési házirendeket is használhat a virtuális géphez való hozzáférés további korlátozásához.

- [Az Azure AD feltételes hozzáférésről itt olvashat bővebben](../active-directory/conditional-access/overview.md)

- [Az Azure feltételes hozzáférés áttekintése](../active-directory/conditional-access/overview.md)

- [Általános feltételes hozzáférési szabályzatok](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [A hitelesítési munkamenet felügyeletének konfigurálása feltételes hozzáféréssel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információt az [Azure biztonsági teljesítményteszt: privilegizált hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)című témakörben talál.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató**: az Azure Bastion az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával különíti el az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok férhetnek hozzá az egyes virtuális gépekhez való kapcsolódáshoz. Ügyeljen arra, hogy kövesse a legalacsonyabb jogosultsági szint elvét, hogy a felhasználók csak az adott feladatok elvégzéséhez szükséges engedélyekkel rendelkezzenek.

Győződjön meg arról, hogy az üzleti szempontból kritikus fontosságú rendszerekre telepített ügynökökkel való rendszergazdai hozzáféréssel rendelkező felügyeleti, identitási és biztonsági rendszerekhez is korlátozza a hozzáférést, például Active Directory-tartomány vezérlőket (DCs), biztonsági eszközöket és rendszerfelügyeleti eszközöket. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Virtuális gépek Azure Bastion-vel való eléréséhez szükséges szerepkörök](bastion-faq.md#roles)

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access)

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az azure Bastion Azure Active Directory (Azure ad) fiókokat és Azure RBAC használ az erőforrások kezeléséhez. Rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést, hogy a fiókok és a hozzáférésük érvényes legyen. Az Azure AD hozzáférési felülvizsgálatok segítségével áttekintheti a csoporttagság, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések áttekintését. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A felülvizsgálati folyamat megkönnyítése érdekében a Azure AD Privileged Identity Management használatával hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat.

Emellett az Azure Privileged Identity Management is beállítható úgy, hogy riasztást hozzon létre túl sok rendszergazdai fiók létrehozásakor, valamint a elavult vagy helytelenül konfigurált rendszergazdai fiókok azonosításához.

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Delegáláshoz való hozzáférés eltávolítása](../lighthouse/how-to/remove-delegation.md)

- [Az Azure AD-identitás és a hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: az Azure Bastion integrálva van a Azure Active Directory és az Azure RBAC az erőforrások kezeléséhez. Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas jogosultsággal rendelkeznek, és nem rendelhetők hozzá konkrét személyekhez. A vészhelyzeti hozzáférési fiókok olyan vészhelyzeti vagy "break Glass" forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok.

Győződjön meg arról, hogy a vészhelyzeti hozzáférési fiókokhoz tartozó hitelesítő adatok (például jelszó, tanúsítvány vagy intelligens kártya) biztonságosak és ismertek, csak azokra a személyekre, akik jogosultak arra, hogy csak vészhelyzetben használják őket.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: az Azure Bastion integrálva van a Azure Active Directory (Azure ad) és az Azure RBAC, hogy kezelje az erőforrásait. Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott.

- [Az Azure AD hozzáférési felülvizsgálatai](../active-directory/governance/access-reviews-overview.md)

- [Mi az Azure AD-jogosultságok kezelése?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: a biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. A követelményektől függően a fokozottan biztonságos felhasználói munkaállomások segítségével felügyeleti feladatokat hajthat végre az Azure-beli megerősített erőforrásokkal éles környezetben. A biztonságos és felügyelt felhasználói munkaállomás felügyeleti feladatokhoz való telepítéséhez Azure Active Directory, a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és/vagy Microsoft Intune használható. A biztonságos munkaállomások központilag felügyelhetők a biztonságos konfiguráció kikényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver-alapterveket, valamint a korlátozott logikai és hálózati hozzáférést is. 

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](../active-directory/devices/concept-azure-managed-workstation.md)

- [Emelt szintű hozzáférési munkaállomás üzembe helyezése](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: csak elég felügyelet (a legalacsonyabb jogosultsági elv) követése 

**Útmutató**: az Azure Bastion integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi az Azure-erőforrások hozzáférésének kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a beépített szerepköröket hozzárendelheti a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz és felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök tartoznak, és ezeket a szerepköröket az Azure CLI-vel, a Azure PowerShellsal vagy a Azure Portal eszközzel leltározott vagy lekérdezheti. Az erőforrásokhoz az Azure RBAC-on keresztül hozzárendelt jogosultságokat mindig a szerepkörök által igényelt értékre kell korlátozni. Ez kiegészíti a Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) megközelítését, és rendszeresen felül kell vizsgálni. A beépített szerepkörök használatával lefoglalhatja az engedélyt, és szükség esetén csak egyéni szerepköröket hozhat létre.

Ha az Azure Bastion használatával csatlakozik a virtuális gépekhez, a felhasználónak a következő szerepkör-hozzárendelésekre lesz szüksége:
- Olvasó szerepkör a cél virtuális gépen
- Olvasó szerepkör a hálózati adapteren a cél virtuális gép magánhálózati IP-címével
- Olvasó szerepkör az Azure Bastion-erőforrásban

További információkért tekintse meg a következő referenciákat:

- [Kapcsolódás Linux rendszerű virtuális géphez az Azure Bastion használatával](bastion-connect-vm-ssh.md)

- [Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával](bastion-connect-vm-rdp.md)

- [Beépített Azure-szerepkörök](../role-based-access-control/built-in-roles.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információt az [Azure biztonsági teljesítményteszt: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)című témakörben talál.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Győződjön meg arról, hogy a biztonsági csapat az eszközök kockázataival rendelkezik

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok biztonsági olvasói engedélyeket kapjanak az Azure-bérlőben és-előfizetésekben, hogy a biztonsági kockázatokat a Azure Security Center használatával tudják figyelni. 

A biztonsági csapat feladatainak strukturálása, a biztonsági kockázatok figyelése a központi biztonsági csapat vagy egy helyi csapat feladata lehet. Azt is mondta, hogy a biztonsági ismereteket és a kockázatokat mindig központilag összesíteni kell egy szervezeten belül. 

A biztonsági olvasó engedélyei széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoportra), vagy a felügyeleti csoportokra vagy az adott előfizetésekre is. 

Megjegyzés: Előfordulhat, hogy további engedélyek szükségesek a számítási feladatok és szolgáltatások láthatóságának megismeréséhez. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure Management Groups áttekintése](../governance/management-groups/overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

**Útmutató**: címkék alkalmazása az Azure-beli megerősített erőforrásokra, erőforráscsoportok és előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra. Győződjön meg arról, hogy a biztonsági csapatok hozzáférhetnek az Azure-beli adategységek folyamatosan frissített leltározásához. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetések összes erőforrását, beleértve az Azure-szolgáltatásokat, az alkalmazásokat és a hálózati erőforrásokat.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával naplózhatja és korlátozhatja, hogy mely szolgáltatások a felhasználók rendelkezhetnek a környezetében. Ez magában foglalja az Azure Bastion-erőforrások üzembe helyezésének engedélyezését vagy megtagadását. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat. A Azure Monitor használatával olyan szabályokat is létrehozhat, amelyek riasztásokat aktiválnak, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: az eszközök életciklus-kezelésének biztonsága

**Útmutató**: távolítsa el az Azure-beli megerősített erőforrásokhoz való hozzáférést, amelyek már nem szükségesek a támadási felület minimalizálásához. A felhasználók a Azure Portal, a CLI vagy a REST API-k segítségével kezelhetik az Azure-beli megerősített erőforrásaikat. Törölheti vagy kényszerítheti a folyamatban lévő távoli munkamenet leválasztását is, ha már nincs rá szükség, vagy lehetséges fenyegetésként azonosítható.

- [Távoli munkamenet törlésének kényszerítése](session-monitoring.md#view)

- [Azure hálózati CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure Bastion integrálva van Azure Active Directory (Azure ad) identitás-és hitelesítési szolgáltatásokhoz. Az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure erőforrás-kezelővel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetések észlelése

*További információt az [Azure biztonsági teljesítményteszt: naplózás és fenyegetések észlelése](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)című témakörben talál.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: a fenyegetések észlelésének engedélyezése az Azure identitás-és hozzáférés-kezeléséhez

**Útmutató**: az Azure Bastion integrálva van Azure Active Directory (Azure ad) szolgáltatással, és a szolgáltatás a Azure Portalon keresztül érhető el. A szolgáltatáshoz (például Létrehozás, frissítés és törlés) tartozó alapértelmezett felügyeleti műveletek rögzítése az Azure-tevékenység naplóján keresztül történik. A felhasználóknak engedélyeznie kell az Azure-alapú megerősített erőforrás-naplókat is, például a munkamenet BastionAuditLogs a megerősített munkamenetek nyomon követéséhez.

Az Azure AD a következő felhasználói naplókat tartalmazza, amelyeket megtekintheti az Azure AD Reporting szolgáltatásban, vagy integrálható a Azure Monitor, az Azure Sentinel vagy más SIEM/monitoring eszközeivel a kifinomultabb monitorozási és elemzési felhasználási esetekben: 
-   Bejelentkezések – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.

-   Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók például a felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos, az Azure AD-ban található összes erőforráson végrehajtott módosítások.

-   Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

-   Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekről, például a sikertelen hitelesítési kísérletekről, valamint az előfizetésben elavult fiókokról. Az alapszintű biztonsági higiéniai monitorozás mellett Azure Security Center veszélyforrások elleni védelmi modulja több részletes biztonsági riasztást is gyűjthet az egyes Azure-beli számítási erőforrásokból (például a Virtual Machines, a containers, az App Service szolgáltatásból), az adatforrásokból (például az SQL-ADATBÁZISból és a tárolóból) és az Azure szolgáltatási rétegeiből. Ez a funkció lehetővé teszi, hogy az egyes erőforrásokon belül megtekintse a fiókok rendellenességeit.

- [Azure megerősített erőforrás-naplók](diagnostic-logs.md)

- [Tevékenység-jelentések naplózása az Azure AD-ben](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: az Azure hálózati tevékenységek naplózásának engedélyezése

**Útmutató**: az Azure-alapú megerősített erőforrás-naplók engedélyezése ezekkel a diagnosztikai naplókkal megtekintheti, hogy mely felhasználókhoz kapcsolódott a munkaterhelések, milyen időpontban, hol és más hasonló naplózási információk. A felhasználók ezeket a naplókat a hosszú távú adatmegőrzés és a naplózás érdekében a Storage-fiókba is beállíthatják.

A hálózati biztonsági csoport (NSG) erőforrás-naplófájljainak és NSG-naplófájljainak engedélyezése és gyűjtése a virtuális hálózatokra alkalmazott hálózati biztonsági csoportokon, amelyeket az Azure-beli megerősített erőforrás telepített. Ezek a naplók a hálózati biztonság elemzéséhez, valamint az incidensek vizsgálatához, a fenyegetések vadászatához és a biztonsági riasztások létrehozásához használhatók fel. A folyamat naplóit elküldheti egy Azure Monitor Log Analytics munkaterületre, majd a Traffic Analytics használatával betekintést biztosíthat.

- [Azure Bastion-naplók engedélyezése és használata](diagnostic-logs.md)

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Az Azure Firewall naplói és metrikái](../firewall/logs-and-metrics.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [Figyelés Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a tevékenységi naplók automatikusan elérhetők, az olvasási műveletek (Get) kivételével az összes írási műveletet (Put, post, DELETE) tartalmazzák az Azure-beli megerősített erőforrásokhoz. A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md)

- [Azure-erőforrás-naplók engedélyezése az Azure Bastion számára ](diagnostic-logs.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: a biztonsági naplók központosított kezelése és elemzése

**Útmutató**: a korreláció engedélyezéséhez központosítsa a naplózási tárolást és az elemzést. Minden naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz.

Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését.

Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: a naplózási tár megőrzésének konfigurálása

**Útmutató**: gondoskodjon arról, hogy az Azure Bastion-naplók tárolásához használt összes Storage-fiók vagy log Analytics-munkaterület a szervezet megfelelőségi szabályainak megfelelően állítsa be a napló megőrzési időtartamát.

Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be.

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/platform/manage-cost-storage.md)

- [Erőforrás-naplók tárolása Azure Storage-fiókban](/azure/azure-monitor/platform/resource-logs-collect-storage)

- [Azure-beli megerősített naplók engedélyezése és használata](diagnostic-logs.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](/azure/security/benchmarks/security-controls-v2-incident-response)című témakörben talál.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: előkészítés – az incidensek frissítési folyamatának frissítése az Azure-ban

**Útmutató**: Ellenőrizze, hogy a szervezet rendelkezik-e olyan folyamatokkal, amelyek reagálnak a biztonsági incidensekre, és frissítette ezeket a folyamatokat az Azure-ban, és rendszeresen gyakorolja őket a készültség biztosítására.

- [A biztonság megvalósítása a nagyvállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidens-válasz referenciájának útmutatója](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: előkészítés – telepítési incidens értesítése

**Útmutató**: biztonsági incidensek elérhetőségi adatainak beállítása Azure Security Centerban. A Microsoft a kapcsolattartási adatokat arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. Lehetőség van az incidensek riasztásának és az értesítések különböző Azure-szolgáltatásokban való testreszabására is az incidensek igényei alapján. 

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: észlelés és elemzés – incidensek létrehozása magas minőségi riasztások alapján

**Útmutató**: Ellenőrizze, hogy van-e olyan folyamat, amely kiváló minőségű riasztásokat hoz létre, és mérje fel a riasztások minőségét. Ez lehetővé teszi, hogy megismerje a múltbeli incidensek tanulságait, és rangsorolja a riasztásokat az elemzők számára, hogy ne pazarolja az időt a hamis pozitív értékekre.

A kiváló minőségű riasztások a múltbeli incidensek, a hitelesített közösségi források és a riasztások létrehozásához és tisztításához tervezett eszközök alapján hozhatók létre a különböző jelforrások elutasításával és korrelációs megoldásával. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. Az Azure Sentinel lehetővé teszi, hogy speciális riasztási szabályokat hozzon létre az incidensek automatikus létrehozásához a vizsgálathoz. 

A Azure Security Center-riasztások és-javaslatok exportálásával az Exportálás funkcióval azonosíthatók az Azure-erőforrásokkal kapcsolatos kockázatok. Riasztásokat és javaslatokat manuálisan vagy folyamatos, folyamatos módon exportálhat.

- [Az Exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások továbbítása az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: észlelés és elemzés – incidens vizsgálata

**Útmutató**: az elemzők különböző adatforrásokat tudnak lekérdezni és használni a lehetséges incidensek kivizsgálásával, így teljes képet kaphatnak arról, hogy mi történt. Különböző naplókat kell gyűjteni a lehetséges támadók tevékenységének nyomon követéséhez a támadási láncon belül a vak foltok elkerülése érdekében.  Gondoskodjon arról is, hogy az elemzések és a megszerzett ismeretek rögzítése más elemzők és a későbbi korábbi referenciák esetében is megtörténjen.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következőket is tartalmazhatja:

- Hálózati adatok – hálózati biztonsági csoportok folyamatábrája, Azure Network Watcher és Azure Monitor a hálózati folyamat naplói és egyéb elemzési információk rögzítésére. 

- Futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-funkciója segítségével pillanatkép készíthető a futó rendszer lemezéről. 

    - A futó rendszer memóriájának pillanatképének létrehozásához használja az operációs rendszer natív memóriaképének képességét.

    - Használja az Azure-szolgáltatások pillanatkép-szolgáltatását, vagy az Ön szoftverének saját funkciója a futó rendszerek pillanatképének létrehozásához.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé gyakorlatilag bármely naplózási forrás és egy eseti felügyeleti portál között az incidensek teljes életciklusának kezeléséhez. A nyomozás során felmerülő hírszerzési információk követési és jelentéskészítési célokra is társíthatók. 

- [Windows-gép lemezének pillanatképe](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [A Linux rendszerű gép lemezének pillanatképe](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure a diagnosztikai információk és a memóriakép-gyűjtés támogatása](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentineltel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: észlelés és elemzés – az incidensek rangsorolása

**Útmutató**: az elemzők környezetének biztosítása, amelyeken a riasztás súlyossága és az eszközök érzékenysége alapján az elsőre összpontosíthat. 

A Azure Security Center az egyes riasztásokhoz súlyosságot rendel, így rangsorolhatja, hogy a rendszer mely riasztásokat vizsgálja meg először. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosítására és kategorizálására, különösen a bizalmas adatok feldolgozására.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: tárolás, mentesítés és helyreállítás – az incidensek kezelésének automatizálása

**Útmutató**: automatizálja a manuális ismétlődő feladatokat a válaszidő felgyorsításához és az elemzők terhének csökkentéséhez. A manuális feladatok végrehajtása hosszabb időt vehet igénybe, és lelassítja az egyes incidenseket, és csökkenti az elemző által kezelhető incidensek számát. A manuális feladatok növelik az elemzői fáradtságot is, ami növeli a késést okozó emberi hibák kockázatát, és csökkenti az elemzők azon képességét, hogy az összetett feladatokon hatékonyan összpontosítsanak. A Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióinak használatával automatikusan aktiválhatja a műveleteket, vagy futtathat egy forgatókönyvet a bejövő biztonsági riasztásokra való reagáláshoz. A forgatókönyv végrehajtja a műveleteket, például értesítések küldését, fiókok letiltását és a problémás hálózatok elkülönítését. 

- [Munkafolyamat-automatizálás konfigurálása Security Centerban](../security-center/workflow-automation.md)

- [Automatizált veszélyforrásokkal kapcsolatos válaszok beállítása Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="posture-and-vulnerability-management"></a>A testtartás és a sebezhetőségek kezelése

*További információt az [Azure biztonsági teljesítményteszt: testtartás és sebezhetőség kezelése](/azure/security/benchmarks/security-controls-v2-vulnerability-management)című témakörben talál.*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: biztonságos konfigurációk létrehozása az Azure-szolgáltatásokhoz 

**Útmutató**: az Azure Bastion szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Network" névtérben egyéni szabályzatok létrehozásához az Azure-beli megerősített hálózati konfigurációjának naplózásához vagy betartatásához. Az ügyfelek biztonságos konfigurációkat is létrehozhatnak azáltal, hogy az Azure-tervezeteket vagy ARM-sablonokat a megerősített erőforrások biztonságos és konzisztens üzembe helyezésére is felhasználják.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Tudnivalók az ARM-sablonokról](../azure-resource-manager/templates/overview.md)

- [Az Azure tervrajzai – áttekintés](../governance/blueprints/overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: biztonságos konfigurációk fenntartása az Azure-szolgáltatásokhoz

**Útmutató**: az Azure Bastion szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Network" névtérben, hogy egyéni szabályzatokat hozzon létre a megerősített erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: rendszeres támadási szimulációk végrehajtása

**Útmutatás**: igény szerint az Azure-erőforrásokon bevezetési teszteket vagy Red Team-tevékenységeket hajthat végre, és biztosíthatja az összes kritikus biztonsági vizsgálat szervizelését.

Kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolás-tesztelés az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolás-tesztelési szabályok engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud vörös összevonása](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információt az [Azure biztonsági teljesítményteszt: kormányzás és stratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy)című témakörben talál.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: eszközkezelés és adatvédelmi stratégia meghatározása 

**Útmutató**: a rendszerek és az információk folyamatos monitorozására és védelmére vonatkozó egyértelmű stratégia dokumentálása és közlése. Az üzleti szempontból kritikus fontosságú adatmennyiségek és rendszerek felderítésének, értékelésének, védelmének és figyelésének rangsorolása. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   Az adatbesorolási szabvány az üzleti kockázatoknak megfelelően

-   A biztonsági szervezet betekintést nyerhet a kockázatokra és az eszközök leltározására 

-   Az Azure-szolgáltatások használatának biztonsági szervezet általi jóváhagyása 

-   Az eszközök biztonsága életcikluson keresztül

-   A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően

-   Az Azure Native és a harmadik féltől származó adatvédelmi képességek használata

-   Adattitkosítási követelmények átvitel közbeni és nyugalmi használati esetekben

-   Megfelelő titkosítási szabványok

További információkért tekintse meg a következő referenciákat:
- [Azure biztonsági architektúra – javaslat – tárolás, adatkezelés és titkosítás](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Az Azure biztonsági alapjai – Azure-adatbiztonság, titkosítás és tárolás](../security/fundamentals/encryption-overview.md)

- [Felhőalapú bevezetési keretrendszer – az Azure adatbiztonsági és-titkosítási ajánlott eljárásai](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure biztonsági teljesítményteszt – eszközkezelés](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure biztonsági teljesítményteszt – adatvédelem](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: vállalati szegmentálási stratégia definiálása 

**Útmutató**: hozzon létre egy nagyvállalati stratégiát, amely az eszközök hozzáférésének szegmentálására szolgál az identitás, a hálózat, az alkalmazás, az előfizetés, a felügyeleti csoport és más vezérlők kombinációjával.

Körültekintően kell elkülöníteni a biztonsági elkülönítés szükségességét annak érdekében, hogy lehetővé váljon a rendszerek napi működésének engedélyezése, amelyeknek kommunikálnia kell egymással, és az adathozzáférésre van szükségük.

Győződjön meg arról, hogy a szegmentálási stratégia következetesen van implementálva a különböző típusú vezérlőkben, beleértve a hálózati biztonságot, az identitás-és hozzáférési modelleket, valamint az alkalmazás engedély/hozzáférési modelljeit, valamint az emberi folyamatok vezérlés

- [Útmutató a szegmentálási stratégiához az Azure-ban (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató a szegmentálási stratégiához az Azure-ban (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás igazítása nagyvállalati szegmentálási stratégiával](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: a biztonsági testhelyzet kezelési stratégiájának meghatározása

**Útmutató**: az egyes eszközökre és a szolgáltatásban üzemeltetett környezetre vonatkozó kockázatok folyamatos mérése és enyhítése. Rangsorolja a nagy értékű eszközöket és a nagy teljesítményű támadási felületeket, például a közzétett alkalmazásokat, a hálózati bejövő és kimenő pontokat, a felhasználói és rendszergazdai végpontokat stb.

- [Azure biztonsági teljesítményteszt – a testtartás és a sebezhetőségek kezelése](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: szervezeti szerepkörök, felelősségek és elszámoltathatóság igazítása

**Útmutató**: gondoskodjon arról, hogy a biztonsági szervezetében a szerepkörökkel és a felelősségekkel kapcsolatos egyértelmű stratégiát dokumentáljon és tájékoztasson. Rangsorolja a biztonsági döntések egyértelmű elszámoltathatóságát, mindenki számára elérhetővé teszi a megosztott felelősségi modellt, és megtanítja a technikai csapatokat a felhő védelméhez.

- [Azure Security – ajánlott eljárás 1 – emberek: a csapatok képzése a Felhőbeli biztonsági úton](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security – ajánlott eljárás 2 – emberek: a csapatok képzése a Felhőbeli biztonsági technológiában](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security – ajánlott eljárás 3 – folyamat: elszámoltathatóság kiosztása a Felhőbeli biztonsági döntésekhez](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-5-define-network-security-strategy"></a>GS-5: hálózati biztonsági stratégia definiálása

**Útmutató**: hozzon létre egy Azure-alapú hálózati biztonsági megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   Központosított Hálózatkezelés és biztonsági felelősség

-   A virtuális hálózat szegmentálási modellje a nagyvállalati szegmentálási stratégiához igazodik

-   Szervizelési stratégia a különböző veszélyforrások és támadási helyzetekben

-   Az Internet Edge és a bejövő forgalom és a kimenő forgalom stratégiája

-   Hibrid felhő és helyszíni összekapcsolási stratégia

-   Naprakész hálózati biztonsági összetevők (például hálózati diagramok, hivatkozási hálózati architektúra)

További információkért tekintse meg a következő referenciákat:
- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure biztonsági teljesítményteszt – hálózati biztonság](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Az Azure hálózati biztonság áttekintése](../security/fundamentals/network-overview.md)

- [Vállalati hálózati architektúra stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: identitás-és privilegizált hozzáférési stratégia meghatározása

**Útmutató**: hozzon létre egy Azure-identitást és egy emelt szintű hozzáférési megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   Központi identitás-és hitelesítési rendszer, valamint az egyéb belső és külső identitási rendszerekkel való kapcsolat

-   Erős hitelesítési módszerek különböző használati esetekben és kikötésekben

-   Magas jogosultsági szintű felhasználók védelme

-   Felhasználói tevékenységek rendellenességének figyelése és feldolgozása  

-   Felhasználói identitás és hozzáférés felülvizsgálata és egyeztetési folyamata

További információkért tekintse meg a következő referenciákat:

- [Azure biztonsági teljesítményteszt – Identitáskezelés](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure biztonsági teljesítményteszt – emelt szintű hozzáférés](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure Identity Management biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: a naplózási és a veszélyforrások reagálási stratégiájának meghatározása

**Útmutató**: naplózási és veszélyforrási reagálási stratégia létrehozása a fenyegetések gyors észlelése és elhárítása érdekében a megfelelőségi követelmények teljesítése mellett. Kiváló minőségű riasztásokkal és zökkenőmentes tapasztalatokkal látja el az elemzőket, hogy az integráció és a manuális lépések helyett a fenyegetésekre összpontosítsanak. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   A biztonsági műveletek (SecOps) szervezet szerepköre és feladatai 

-   Egy jól definiált incidens-reagálási folyamat NIST vagy más iparági keretrendszerrel való összehangolása 

-   A fenyegetések észlelését, az incidensek megválaszolását és a megfelelőségi igényeket támogató napló rögzítése és megőrzése

-   A fenyegetésekkel kapcsolatos központosított láthatóság és korrelációs információk a SIEM, a natív Azure-képességek és más források használatával 

-   Kommunikációs és értesítési terv ügyfeleivel, szállítókkal és az érdekelt felekkel

-   Az Azure Native és a harmadik féltől származó platformok használata az incidensek kezelésére, mint például a naplózás és a veszélyforrások észlelése, a kriminalisztika, valamint a támadási problémák elhárítása és felszámolása

-   Az incidensek és az incidens utáni tevékenységek kezelésének folyamatai, például a tanulságok és a bizonyítékok megőrzése

További információkért tekintse meg a következő referenciákat:

- [Azure biztonsági teljesítményteszt – naplózás és fenyegetések észlelése](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure biztonsági teljesítményteszt – incidens válasza](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security – ajánlott eljárás 4 – folyamat. Incidensek frissítési folyamatainak frissítése a felhőben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure bevezetési keretrendszer, naplózás és jelentéskészítési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Az Azure nagyvállalati szintű skálázása, kezelése és monitorozása](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure Security teljesítményteszt v2 áttekintése](/azure/security/benchmarks/overview) című témakört.
- További információ az [Azure biztonsági alaptervekről](/azure/security/benchmarks/security-baselines-overview)
