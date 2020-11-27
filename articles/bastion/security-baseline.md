---
title: Azure Security-alaptervek az Azure-hoz
description: Az Azure Bastion biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 461d3800491dbe382beecbcdfe1f2a93bd6b2e5c
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301567"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Azure Security-alaptervek az Azure-hoz

Ez a biztonsági alapterv az [Azure Security teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) az Azure Bastion-re vonatkozó útmutatást alkalmazza. Az Azure biztonsági teljesítménytesztje javaslatokat nyújt ahhoz, hogyan védheti meg felhőalapú megoldásait az Azure-ban. A tartalom az Azure Security benchmark által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure-ra vonatkozó kapcsolódó útmutatás. Az Azure Bastion-re nem alkalmazható **vezérlők** ki lettek zárva.

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

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása

**Útmutató**: az Azure Bastion nem tesz elérhetővé olyan végpontokat, amelyek egy magánhálózaton keresztül érhetők el. Az Azure Bastion támogatja az üzembe helyezést egy társ hálózatban, hogy központosítsa a megerősített üzembe helyezést, és engedélyezze a hálózat közötti kapcsolatot.

- [Az Azure Bastion és a virtuális hálózat társítása](vnet-peering.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Az Azure Active Directory szabványosítása központi identitáskezelési és hitelesítési rendszerként

**Útmutató**: az Azure Bastion integrálva van Azure Active Directory (Azure ad) szolgáltatással, amely az Azure alapértelmezett identitás-és hozzáférés-kezelési szolgáltatása. A felhasználók az Azure AD-hitelesítés használatával férhetnek hozzá a Azure Portalhoz az Azure Bastion szolgáltatás kezeléséhez (a megerősített erőforrások létrehozásához, frissítéséhez és törléséhez).

Az Azure Bastion használatával a virtuális gépekhez való csatlakozás SSH-kulcson vagy felhasználónévn/jelszón alapul, és jelenleg nem támogatja az Azure AD-beli hitelesítő adatok használatát.

Egy SSH-kulcson vagy felhasználónéven/jelszón kívül, amikor a virtuális gépekhez az Azure Bastion használatával csatlakozik, a felhasználónak a következő szerepkör-hozzárendelésekre lesz szüksége:
- Olvasó szerepkör a cél virtuális gépen
- Olvasó szerepkör a hálózati adapteren a cél virtuális gép magánhálózati IP-címével
- Olvasó szerepkör az Azure Bastion-erőforrásban

További információkért tekintse meg az alábbi hivatkozásokat:

- [Kapcsolódás Linux rendszerű virtuális géphez az Azure Bastion használatával](bastion-connect-vm-ssh.md)

- [Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával](bastion-connect-vm-rdp.md)

- [Beépített Azure-szerepkörök](../role-based-access-control/built-in-roles.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Azure AD egyszeri bejelentkezés (SSO) használata az alkalmazások hozzáféréséhez

**Útmutató**: az Azure Bastion nem támogatja az egyszeri bejelentkezést a virtuális gépi erőforrásokhoz való hitelesítéskor, csak az ssh vagy a username/Password támogatott. Az Azure Bastion azonban Azure Active Directory (Azure AD) használatával biztosítja az identitás-és hozzáférés-kezelést a teljes szolgáltatáshoz. A felhasználók hitelesíthetők az Azure AD-ben az Azure-beli megerősített erőforrásaik eléréséhez és kezeléséhez, és zökkenőmentes egyszeri bejelentkezést végezhetnek saját szinkronizált vállalati identitásokkal Azure AD Connect használatával. 

- [SSO alkalmazásokhoz az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure AD-kapcsolat](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [Kapcsolódás Linux rendszerű virtuális géphez az Azure Bastion használatával](bastion-connect-vm-ssh.md)

- [Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával](bastion-connect-vm-rdp.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítésvezérlők használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure Bastion integrálva van Azure Active Directory (Azure ad) szolgáltatással a szolgáltatás eléréséhez és kezeléséhez. Konfigurálja az Azure-Multi-Factor Authentication az Azure AD-bérlőhöz. Az Azure AD a többtényezős hitelesítéssel (MFA) és az erős, jelszóval nem rendelkező metódusokkal támogatja az erős hitelesítési vezérlést.  
- Többtényezős hitelesítés: engedélyezze az Azure AD MFA-t, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait az MFA-telepítéshez. Az MFA kikényszeríthető minden felhasználóra, kiválaszthatja a felhasználókat vagy a felhasználónkénti szinten a bejelentkezési feltételek és kockázati tényezők alapján. 

- Jelszóval nem rendelkező hitelesítés: három jelszóval nem rendelkező hitelesítési lehetőség érhető el: a vállalati Windows Hello, Microsoft Authenticator alkalmazás és a helyszíni hitelesítési módszerek, például az intelligens kártyák. 

- [Az MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Az Azure Active Directory jelszó nélküli hitelesítési lehetőségeinek ismertetése](../active-directory/authentication/concept-authentication-passwordless.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Fiókok rendellenességeinek monitorozása és a hozzájuk kapcsolódó riasztások

**Útmutató**: engedélyezze a diagnosztikai naplókat az Azure Bastion távoli munkamenetekhez, és ezeket a naplókat használva megtekintheti, hogy mely felhasználók mely munkaterhelésekhez kapcsolódnak, milyen időpontban, hol és más hasonló naplózási információkkal. Riasztásokat hozhat létre a naplókban észlelt rendellenességek észlelése esetén, Azure Monitor használatával értesítést kaphat az egyes naplózott megerősített munkamenetekről.

- [A diagnosztikai naplózás engedélyezéséről itt talál további információt](diagnostic-logs.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: az Azure erőforrás-hozzáférés korlátozása feltételek alapján

**Útmutató**: csak az Azure Bastion szolgáltatáshoz férhet hozzá a Azure Portalon keresztül, Azure Portal hozzáférését korlátozhatja Azure Active Directory (Azure ad) feltételes hozzáférés használatával. Az Azure AD feltételes hozzáférés használatával részletesebb hozzáférés-vezérlést használhat a felhasználó által definiált feltételek alapján, például bizonyos IP-címtartományok felhasználói bejelentkezésének megkövetelése az MFA használatára.

Az ügyfél a tartományhoz csatlakoztatott virtuális gépek szintjén eltérő szerepköralapú hozzáférés-vezérlési házirendeket is használhat a virtuális géphez való hozzáférés további korlátozásához.

- [Az Azure AD feltételes hozzáférésről itt olvashat bővebben](../active-directory/conditional-access/overview.md)

- [Az Azure feltételes hozzáférés áttekintése](../active-directory/conditional-access/overview.md)

- [Általános feltételes hozzáférési szabályzatok](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [A hitelesítési munkamenet felügyeletének konfigurálása feltételes hozzáféréssel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: emelt szintű hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató**: az Azure Bastion az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával különíti el az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok férhetnek hozzá az egyes virtuális gépekhez való kapcsolódáshoz. Ügyeljen arra, hogy kövesse a legalacsonyabb jogosultsági szint elvét, hogy a felhasználók csak az adott feladatok elvégzéséhez szükséges engedélyekkel rendelkezzenek.

Győződjön meg arról, hogy az üzleti szempontból kritikus fontosságú rendszerekre telepített ügynökökkel való rendszergazdai hozzáféréssel rendelkező felügyeleti, identitási és biztonsági rendszerekhez is korlátozza a hozzáférést, például Active Directory-tartomány vezérlőket (DCs), biztonsági eszközöket és rendszerfelügyeleti eszközöket. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Virtuális gépek Azure Bastion-vel való eléréséhez szükséges szerepkörök](bastion-faq.md#roles)

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access)

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az azure Bastion Azure Active Directory (Azure ad) fiókokat és Azure RBAC használ az erőforrások kezeléséhez. Rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést, hogy a fiókok és a hozzáférésük érvényes legyen. Az Azure AD hozzáférési felülvizsgálatok segítségével áttekintheti a csoporttagság, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések áttekintését. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A felülvizsgálati folyamat megkönnyítése érdekében a Azure AD Privileged Identity Management használatával hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat.

Emellett az Azure Privileged Identity Management is beállítható úgy, hogy riasztást hozzon létre túl sok rendszergazdai fiók létrehozásakor, valamint a elavult vagy helytelenül konfigurált rendszergazdai fiókok azonosításához.

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Delegáláshoz való hozzáférés eltávolítása](../lighthouse/how-to/remove-delegation.md)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: az Azure Bastion integrálva van a Azure Active Directory és az Azure RBAC az erőforrások kezeléséhez. Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas jogosultsággal rendelkeznek, és nem rendelhetők hozzá konkrét személyekhez. A vészhelyzeti hozzáférési fiókok olyan vészhelyzeti vagy "break Glass" forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok.

Győződjön meg arról, hogy a vészhelyzeti hozzáférési fiókokhoz tartozó hitelesítő adatok (például jelszó, tanúsítvány vagy intelligens kártya) biztonságosak és ismertek, csak azokra a személyekre, akik jogosultak arra, hogy csak vészhelyzetben használják őket.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: az Azure Bastion integrálva van a Azure Active Directory (Azure ad) és az Azure RBAC, hogy kezelje az erőforrásait. Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott.

- [Az Azure AD hozzáférési felülvizsgálatai](../active-directory/governance/access-reviews-overview.md)

- [Mi az Azure AD-jogosultságok kezelése?](../active-directory/governance/entitlement-management-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: a biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. A követelményektől függően a fokozottan biztonságos felhasználói munkaállomások segítségével felügyeleti feladatokat hajthat végre az Azure-beli megerősített erőforrásokkal éles környezetben. A biztonságos és felügyelt felhasználói munkaállomás felügyeleti feladatokhoz való telepítéséhez Azure Active Directory, a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és/vagy Microsoft Intune használható. A biztonságos munkaállomások központilag felügyelhetők a biztonságos konfiguráció kikényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver-alapterveket, valamint a korlátozott logikai és hálózati hozzáférést is. 

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](../active-directory/devices/concept-azure-managed-workstation.md)

- [Emelt szintű hozzáférési munkaállomás üzembe helyezése](../active-directory/devices/howto-azure-managed-workstation.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: az Azure Bastion integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi, hogy szerepkörök hozzárendelésével felügyelje az Azure-erőforrások hozzáférését. Ezeket a beépített szerepköröket hozzárendelheti a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz és felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal. Az erőforrásokhoz az Azure RBAC-n keresztül hozzárendelt jogosultságokat mindig arra kell korlátozni, amit a szerepkörök megkövetelnek. Ez a megközelítés megfelel az Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) módszerének, és a jogosultságok rendszeres felülvizsgálatával jár. A beépített szerepkörök használatával lefoglalhatja az engedélyt, és szükség esetén csak egyéni szerepköröket hozhat létre.

Ha az Azure Bastion használatával csatlakozik a virtuális gépekhez, a felhasználónak a következő szerepkör-hozzárendelésekre lesz szüksége:
- Olvasó szerepkör a cél virtuális gépen
- Olvasó szerepkör a hálózati adapteren a cél virtuális gép magánhálózati IP-címével
- Olvasó szerepkör az Azure Bastion-erőforrásban

További információkért tekintse meg az alábbi hivatkozásokat:

- [Kapcsolódás Linux rendszerű virtuális géphez az Azure Bastion használatával](bastion-connect-vm-ssh.md)

- [Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával](bastion-connect-vm-rdp.md)

- [Beépített Azure-szerepkörök](../role-based-access-control/built-in-roles.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: eszközkezelés](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: Győződjön meg arról, hogy a biztonsági csapatok biztonsági olvasó engedélyekkel rendelkeznek az Azure-bérlőben és -előfizetésekben, hogy monitorozni tudják a biztonsági kockázatokat az Azure Security Centerrel. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. A biztonsági megállapításokat és kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

Megjegyzés: A számítási feladatok és a szolgáltatások átláthatóvá tételéhez további engedélyek lehetnek szükségesek. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

**Útmutató**: címkék alkalmazása az Azure-beli megerősített erőforrásokra, erőforráscsoportok és előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra. Győződjön meg arról, hogy a biztonsági csapatok hozzáférhetnek az Azure-beli adategységek folyamatosan frissített leltározásához. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetések összes erőforrását, beleértve az Azure-szolgáltatásokat, az alkalmazásokat és a hálózati erőforrásokat.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával naplózhatja és korlátozhatja, hogy mely szolgáltatások a felhasználók rendelkezhetnek a környezetében. Ez magában foglalja az Azure Bastion-erőforrások üzembe helyezésének engedélyezését vagy megtagadását. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat. A Azure Monitor használatával olyan szabályokat is létrehozhat, amelyek riasztásokat aktiválnak, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Az objektuméletciklus-kezelés biztonságának garantálása

**Útmutató**: távolítsa el az Azure-beli megerősített erőforrásokhoz való hozzáférést, amelyek már nem szükségesek a támadási felület minimalizálásához. A felhasználók a Azure Portal, a CLI vagy a REST API-k segítségével kezelhetik az Azure-beli megerősített erőforrásaikat. Törölheti vagy kényszerítheti a folyamatban lévő távoli munkamenet leválasztását is, ha már nincs rá szükség, vagy lehetséges fenyegetésként azonosítható.

- [Távoli munkamenet törlésének kényszerítése](session-monitoring.md#view)

- [Azure hálózati CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure Bastion integrálva van Azure Active Directory (Azure ad) identitás-és hitelesítési szolgáltatásokhoz. Az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure erőforrás-kezelővel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetésészlelés engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

**Útmutató**: az Azure Bastion integrálva van Azure Active Directory (Azure ad) szolgáltatással, és a szolgáltatás a Azure Portalon keresztül érhető el. A szolgáltatáshoz (például Létrehozás, frissítés és törlés) tartozó alapértelmezett felügyeleti műveletek rögzítése az Azure-tevékenység naplóján keresztül történik. A felhasználóknak engedélyeznie kell az Azure-alapú megerősített erőforrás-naplókat is, például a munkamenet BastionAuditLogs a megerősített munkamenetek nyomon követéséhez.

Az Azure AD a következő felhasználói naplókat tartalmazza, amelyeket megtekintheti az Azure AD Reporting szolgáltatásban, vagy integrálható a Azure Monitor, az Azure Sentinel vagy más SIEM/monitoring eszközeivel a kifinomultabb monitorozási és elemzési felhasználási esetekben: 
-   Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.

-   Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.

-   Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

-   Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekről, például a sikertelen hitelesítési kísérletekről, valamint az előfizetésben elavult fiókokról. Az alapszintű biztonsági higiéniai monitorozás mellett Azure Security Center veszélyforrások elleni védelmi modulja több részletes biztonsági riasztást is gyűjthet az egyes Azure-beli számítási erőforrásokból (például a Virtual Machines, a containers, az App Service szolgáltatásból), az adatforrásokból (például az SQL-ADATBÁZISból és a tárolóból) és az Azure szolgáltatási rétegeiből. Ez a funkció lehetővé teszi, hogy az egyes erőforrásokon belül megtekintse a fiókok rendellenességeit.

- [Azure megerősített erőforrás-naplók](diagnostic-logs.md)

- [Tevékenység-jelentések naplózása az Azure AD-ben](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: az Azure hálózati tevékenységek naplózásának engedélyezése

**Útmutató**: az Azure-alapú megerősített erőforrás-naplók engedélyezése ezekkel a diagnosztikai naplókkal megtekintheti, hogy mely felhasználókhoz kapcsolódott a munkaterhelések, milyen időpontban, hol és más hasonló naplózási információk. A felhasználók ezeket a naplókat a hosszú távú adatmegőrzés és a naplózás érdekében a Storage-fiókba is beállíthatják.

A hálózati biztonsági csoport (NSG) erőforrás-naplófájljainak és NSG-naplófájljainak engedélyezése és gyűjtése a virtuális hálózatokra alkalmazott hálózati biztonsági csoportokon, amelyeket az Azure-beli megerősített erőforrás telepített. Ezek a naplók a hálózati biztonság elemzéséhez, valamint az incidensek vizsgálatához, a fenyegetések vadászatához és a biztonsági riasztások létrehozásához használhatók fel. A folyamat naplóit elküldheti egy Azure Monitor Log Analytics munkaterületre, majd a Traffic Analytics használatával betekintést biztosíthat.

- [Azure Bastion-naplók engedélyezése és használata](diagnostic-logs.md)

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Az Azure Firewall naplói és metrikái](../firewall/logs-and-metrics.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [Figyelés Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a tevékenységi naplók automatikusan elérhetők, az olvasási műveletek (Get) kivételével az összes írási műveletet (Put, post, DELETE) tartalmazzák az Azure-beli megerősített erőforrásokhoz. A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md)

- [Azure-erőforrás-naplók engedélyezése az Azure Bastion számára ](diagnostic-logs.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: a biztonsági naplók központosított kezelése és elemzése

**Útmutató**: a korreláció engedélyezéséhez központosítsa a naplózási tárolást és az elemzést. Minden naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz.

Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését.

Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: a naplózási tár megőrzésének konfigurálása

**Útmutató**: gondoskodjon arról, hogy az Azure Bastion-naplók tárolásához használt összes Storage-fiók vagy log Analytics-munkaterület a szervezet megfelelőségi szabályainak megfelelően állítsa be a napló megőrzési időtartamát.

Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be.

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/platform/manage-cost-storage.md)

- [Erőforrás-naplók tárolása Azure Storage-fiókban](/azure/azure-monitor/platform/resource-logs-collect-storage)

- [Azure-beli megerősített naplók engedélyezése és használata](diagnostic-logs.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Incidensmegoldás](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Előkészítés – incidensmegoldási folyamat frissítése az Azure-ban

**Útmutató**: Győződjön meg arról, hogy a szervezet rendelkezik a biztonsági incidensek megoldásához szükséges folyamatokkal, frissítette ezeket a folyamatokat az Azure-ral való használatra, valamint rendszeresen teszteli őket, hogy készen álljanak a használatra.

- [A biztonság implementálása a vállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidensmegoldás – gyorsútmutató](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Előkészítés – incidensértesítések beállítása

**Útmutató**: Kapcsolattartási adatokat állíthat be a biztonsági incidensekhez az Azure Security Centerben. A Microsoft arra használja ezeket a kapcsolattartási adatokat, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy egy jogosulatlan vagy illetéktelen fél hozzáfért az Ön adataihoz. Az incidensekkel kapcsolatos riasztások és értesítések testreszabhatók a különböző Azure-szolgáltatásokban az incidensmegoldási követelmények alapján. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Észlelés és elemzés – incidensek létrehozása kiváló minőségű riasztások alapján

**Útmutató**: Ellenőrizze, hogy van-e olyan folyamat, amely kiváló minőségű riasztásokat hoz létre, és mérje fel a riasztások minőségét. Ezzel tanulhat a korábbi incidensekből, és megadhatja a riasztások fontossági sorrendjét az elemzők számára, akik így nem pazarolnak időt a vakriasztásokra.

A kiváló minőségű riasztások a múltbeli incidensek, a hitelesített közösségi források és a riasztások létrehozásához és tisztításához tervezett eszközök alapján hozhatók létre a különböző jelforrások elutasításával és korrelációs megoldásával. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

Az exportálási funkcióval exportálhatja az Azure Security Center riasztásait és javaslatait, amelyek segítenek az Azure-erőforrások kockázatainak azonosításában. A riasztásokat és javaslatokat manuálisan vagy folyamatosan is exportálhatja.

- [Az exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Észlelés és elemzés – incidens vizsgálata

**Útmutató**: Győződjön meg arról, hogy az elemzők különböző adatforrásokat kérdezhetnek le és használhatnak a lehetséges incidensek vizsgálata során, és átfogó képet adhatnak a történtekről. A vakfoltok elkerülése érdekében különböző naplókat kell gyűjteni a potenciális támadók tevékenységének követéséhez a támadási útvonalon.  A megállapítások és eredmények rögzítéséről is érdemes gondoskodnia, hogy később más elemzők referenciaként használhassák őket.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következők is lehetnek:

- Hálózati adatok – az Azure Network Watcher, az Azure Monitor és a hálózati biztonsági csoportok forgalmi naplóinak használata a hálózati forgalom naplóinak és más elemzési adatok rögzítéséhez. 

- A futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-készítési funkciójával pillanatképet készíthet a futó rendszer lemezéről. 

    - Az operációs rendszer natív memóriakép-készítési funkciójával pillanatképet készíthet a futó rendszer memóriájáról.

    - Az Azure-szolgáltatások vagy saját szoftvere pillanatkép-készítési funkciójával pillanatképeket készíthet a futó rendszerekről.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé szinte bármely naplózási forráson és egy esetfelügyeleti portálon az incidensek teljes életciklusának kezeléséhez. A vizsgálatok során kapott információk társíthatók egy incidenssel nyomkövetés és jelentéskészítés céljából. 

- [Windows rendszerű gép lemezének pillanatképe](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux rendszerű gép lemezének pillanatképe](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-támogatás – diagnosztikai információk és memóriaképek gyűjtése](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentinellel](../sentinel/tutorial-investigate-cases.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Észlelés és elemzés – incidensek rangsorolása

**Útmutató**: Megadhat arra vonatkozó információkat az elemzők számára, hogy melyik incidensekre kell először összpontosítaniuk a súlyosság és az objektum bizalmassága alapján. 

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Elkülönítés, megsemmisítés és helyreállítás – incidensek kezelésének automatizálása

**Útmutató**: Automatizálhatja a manuális, ismétlődő feladatokat a válaszidő lerövidítéséhez és az elemzők terheinek csökkentéséhez. A manuális feladatok végrehajtása hosszabb időt vesz igénybe, lassítja az egyes incidensek kezelését, és csökkenti az egyetlen elemző által kezelhető incidensek számát. A manuális feladatok ki is merítik az elemzőket, ami növeli az emberi hiba lehetőségét, és az elemzők kevésbé tudnak hatékonyan összpontosítani az összetett feladatokra. Az Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióival automatikusan indíthat el műveleteket és futtathat forgatókönyveket, hogy reagáljon a bejövő biztonsági riasztásokra. A forgatókönyv műveleteket hajt végre, például értesítéseket küld, fiókokat tilt le, és elkülöníti a problémás hálózatokat. 

- [Munkafolyamat-automatizálás konfigurálása a Security Centerben](../security-center/workflow-automation.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Security Centerben](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="posture-and-vulnerability-management"></a>Állapot- és biztonságirés-kezelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Állapot- és biztonságirés-kezelés](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: biztonságos konfigurációk létrehozása az Azure-szolgáltatásokhoz 

**Útmutató**: az Azure Bastion szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Network" névtérben egyéni szabályzatok létrehozásához az Azure-beli megerősített hálózati konfigurációjának naplózásához vagy betartatásához. Az ügyfelek biztonságos konfigurációkat is létrehozhatnak azáltal, hogy az Azure-tervezeteket vagy ARM-sablonokat a megerősített erőforrások biztonságos és konzisztens üzembe helyezésére is felhasználják.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Tudnivalók az ARM-sablonokról](../azure-resource-manager/templates/overview.md)

- [Az Azure tervrajzai – áttekintés](../governance/blueprints/overview.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: biztonságos konfigurációk fenntartása az Azure-szolgáltatásokhoz

**Útmutató**: az Azure Bastion szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Network" névtérben, hogy egyéni szabályzatokat hozzon létre a megerősített erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Támadások rendszeres szimulálása

**Útmutató**: Szükség szerint behatolási teszteket vagy támadásszimulációs tevékenységeket hajthat végre az Azure-erőforrásokon, és gondoskodhat az észlelt kritikus biztonsági rések javításáról.

Kövesse a Microsoft Cloud behatolástesztelési szabályait, így biztosíthatja, hogy a behatolási tesztek nem sértik meg a Microsoft-szabályzatokat. Használja a Microsoft stratégiáját, és hajtson végre támadásszimulációt, valamint végezze el a Microsoft által felügyelt felhő-infrastruktúra, szolgáltatások és alkalmazások éles webhelyen történő behatolástesztelését.

- [Behatolástesztelés az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolástesztelési szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud – támadásszimuláció](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Irányítás és stratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Objektumkezelési és adatvédelmi stratégia meghatározása 

**Útmutató**: Győződjön meg arról, hogy egyértelmű stratégiát dokumentál és kommunikál a rendszerek és adatok folyamatos monitorozásához és védelméhez. Priorizálja az üzletileg kritikus adatok és rendszerek felderítését, értékelését, védelmét és monitorozását. 

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   Adatbesorolási szabvány az üzleti kockázatokkal összhangban

-   A biztonsági szervezet betekintése a kockázatokba és az objektumleltárba 

-   A biztonsági szervezet jóváhagyása a használandó Azure-szolgáltatásokhoz 

-   Az objektumok biztonsága életciklusuk során

-   A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően

-   Az Azure Native és a harmadik féltől származó adatvédelmi képességek használata

-   Adattitkosítási követelmények a használatban lévő és a jelenleg nem használt használati esetekhez

-   Megfelelő titkosítási szabványok

További információkért tekintse meg az alábbi hivatkozásokat:
- [Az Azure biztonsági architektúrájára vonatkozó javaslatok – Tárolás, adatok és titkosítás](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Az Azure biztonsági alapjai – Az Azure-beli adatok biztonsága, titkosítása és tárolása](../security/fundamentals/encryption-overview.md)

- [Felhőbevezetési keretrendszer – Az Azure adatbiztonsággal és titkosítással kapcsolatos ajánlott eljárásai](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure-biztonsági teljesítményteszt – Objektumkezelés](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure-biztonsági teljesítményteszt – Adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Vállalati szegmentálási stratégia meghatározása 

**Útmutató**: Hozzon létre egy vállalati szintű stratégiát az objektumokhoz való hozzáférés szegmentálásához identitás, hálózat, alkalmazás, előfizetés, felügyeleti csoport és egyéb vezérlők kombinációjának használatával.

Ügyeljen az egyensúlyra a biztonsági elkülönítés és az egymással kommunikáló, az adatok elérését biztosító rendszerek napi működése között.

Győződjön meg arról, hogy a szegmentálási stratégia következetesen van implementálva a különböző típusú vezérlőkben, beleértve a hálózati biztonságot, az identitás- és hozzáférési modelleket, az alkalmazásengedély- és hozzáférési modelleket, valamint az emberi folyamatvezérlést.

- [Útmutató a szegmentálási stratégiához az Azure-ban (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató a szegmentálási stratégiához az Azure-ban (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [A hálózati szegmentálás igazítása a vállalati szegmentálási stratégiához](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Biztonsági helyzet kezelési stratégiájának meghatározása

**Útmutató**: Folyamatosan mérje fel és mérsékelje az egyes objektumok és az ezeket tartalmazó környezetek kockázatait. Priorizálja a nagy értékű objektumokat és a támadásoknak nagy mértékben kitett felületeket, például a közzétett alkalmazásokat, a hálózat bemeneti és kimeneti pontjait és a felhasználói és rendszergazdai végpontokat.

- [Azure-biztonsági teljesítményteszt – Állapot- és biztonságirés-kezelés](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Szervezeti szerepkörök, felelősségek és elszámoltathatóság összehangolása

**Útmutató**: Győződjön meg arról, hogy egyértelmű stratégiát dokumentál és kommunikál a biztonsági szervezet szerepköreivel és felelősségeivel kapcsolatban. Biztosítson egyértelmű elszámoltathatóságot a biztonsági döntésekhez, tájékoztasson mindenkit a megosztott felelősségi modellről, és tájékoztassa a műszaki csapatokat a technológiáról a felhő védelme érdekében.

- [Az Azure ajánlott biztonsági eljárásai 1 – Személyek: A csapatok tájékoztatása a felhőbiztonság felé vezető útról](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Az Azure ajánlott biztonsági eljárásai 2 – Személyek: A csapatok tájékoztatása a felhőbiztonsági technológiáról](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Az Azure ajánlott biztonsági eljárásai 3 – Folyamat: A felhőbiztonsági döntésekért való elszámoltathatóság biztosítása](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Hálózati biztonsági stratégia meghatározása

**Útmutató**: Hozzon létre egy Azure-beli hálózati biztonsági megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   Központosított hálózatkezelés és biztonsági felelősség

-   A vállalat szegmentálási stratégiájához igazított virtuálishálózat-szegmentálási modell

-   Javítási stratégia a különböző fenyegetési és támadási forgatókönyvekben

-   Internetes peremhálózatra, valamint bejövő és kimenő adatforgalomra vonatkozó stratégia

-   A felhőbeli és a helyszíni infrastruktúra összekapcsolásának hibrid stratégiája

-   Naprakész hálózati biztonsági összetevők (például hálózati diagramok, hálózati referenciaarchitektúra)

További információkért tekintse meg az alábbi hivatkozásokat:
- [Az Azure ajánlott biztonsági eljárásai 11 – Architektúra. Egyetlen, egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure-biztonsági teljesítményteszt – Hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security)

- [Az Azure-hálózat biztonsági áttekintése](../security/fundamentals/network-overview.md)

- [Vállalati hálózati architektúra stratégiája](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Identitás- és emelt szintű hozzáférési stratégia meghatározása

**Útmutató**: Hozzon létre egy Azure-beli identitás- és emelt szintű hozzáférési megközelítést a szervezete általános biztonság hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   A központosított identitás- és hitelesítési rendszer, valamint a rendszer más belső és külső rendszerekkel való összekapcsolhatósága

-   Erős hitelesítési módszerek a különböző használati esetekben és különböző feltételek esetén

-   Magas szintű jogosultságokkal rendelkező felhasználók védelme

-   Rendellenes felhasználói tevékenységek monitorozása és kezelése  

-   Felhasználói identitás és hozzáférés ellenőrzési és egyeztetési folyamata

További információkért tekintse meg az alábbi hivatkozásokat:

- [Azure-biztonsági teljesítményteszt – Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure-biztonsági teljesítményteszt – Emelt szintű hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Az Azure ajánlott biztonsági eljárásai 11 – Architektúra. Egyetlen, egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure-identitáskezelés biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Naplózási stratégia és a fenyegetésekre adott válaszok stratégiájának meghatározása

**Útmutató**: Hozzon létre egy stratégiát a naplózáshoz és a fenyegetésekre adott válaszokhoz, amelyekkel gyorsan észlelheti és elháríthatja a fenyegetéseket, a megfelelőségi követelmények betartása mellett. Kiváló minőségű riasztásokkal és zökkenőmentes tapasztalatokkal látja el az elemzőket, hogy az integráció és a manuális lépések helyett a fenyegetésekre összpontosítsanak. 

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   A biztonsági műveletekért (SecOps) felelős szervezet szerepköre és felelősségei 

-   Az incidensre adott, jól definiált válaszfolyamat, amely igazodik az NIST-hez és más iparági keretrendszerekhez 

-   Naplók rögzítése és megőrzése a fenyegetésészlelés, az incidensmegoldás és a megfelelőségi célkitűzések támogatásához

-   A fenyegetések SIEM, natív Azure-képességek és más erőforrások használatával biztosított, központosított láthatósága, illetve korrelációs adatai 

-   Az ügyfelek, a beszállítók és az érdekelt közszektorbeli felekkel folytatott értesítésének, valamint a velük folytatott kommunikáció terve

-   Natív Azure-beli és külső platformok használata az incidensek kezeléséhez, például a naplózáshoz és a fenyegetésészleléshez, a vizsgálathoz, és a támadások elhárításához és megsemmisítéséhez

-   Az incidenseket és az incidensek utáni tevékenységeket, például a levont következtetéseket és a bizonyítékmegőrzést kezelő folyamatok

További információkért tekintse meg az alábbi hivatkozásokat:

- [Azure-biztonsági teljesítményteszt – Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure-biztonsági teljesítményteszt – Incidensmegoldás](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Az Azure ajánlott biztonsági eljárásai 4 – Folyamat. A felhőre vonatkozó incidensmegoldási folyamatok frissítése](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Útmutató az Azure-bevezetési keretrendszerrel, a naplózással és a jelentéskészítéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Az Azure vállalati skálázása, kezelése és monitorozása](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- Lásd: [Az Azure biztonsági teljesítményteszt 2-es verziójának áttekintése](/azure/security/benchmarks/overview)
- További tudnivalók az [Azure biztonsági alapkonfigurációiról](/azure/security/benchmarks/security-baselines-overview)
