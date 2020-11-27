---
title: Content Delivery Network Azure biztonsági alapterve
description: A Content Delivery Network biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: azure-cdn
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7865562a8161e6fc7bed52791b42b867a3dac6f8
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302425"
---
# <a name="azure-security-baseline-for-content-delivery-network"></a>Content Delivery Network Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) Content Delivery Networkre vonatkozó útmutatást alkalmaz. Az Azure biztonsági teljesítménytesztje javaslatokat nyújt ahhoz, hogyan védheti meg felhőalapú megoldásait az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Content Delivery Network vonatkozó kapcsolódó útmutatás. A Content Delivery Network nem alkalmazható **vezérlők** ki vannak zárva.

Ha szeretné megtekinteni, hogyan Content Delivery Network teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Content Delivery Network biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identitáskezelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: az Azure erőforrás-hozzáférés korlátozása feltételek alapján

**Útmutató**: az Content Delivery Network a tartalom elérésének korlátozása ország vagy régió szerint. Szabályok létrehozása a CDN-végponton megadott elérési utakon, hogy engedélyezze vagy tiltsa le a kiválasztott országokban vagy régiókban lévő tartalmakat a Geo-szűrés funkció használatával.

- [Azure CDN tartalmának korlátozása ország vagy régió szerint](cdn-restrict-access-by-country.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: emelt szintű hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata a Content Delivery Network az üzleti szempontból kritikus rendszerekhez való hozzáférés elkülönítését azáltal, hogy korlátozza, hogy mely fiókok emelt szintű hozzáférést kapnak az előfizetésekhez és a felügyeleti csoportokhoz.

Az üzleti szempontból kritikus fontosságú rendszerekre telepített ügynökökkel is korlátozhatja az olyan felügyeleti, identitási és biztonsági rendszerekhez való hozzáférést, amelyek rendszergazdai hozzáféréssel rendelkeznek az üzleti szempontból létfontosságú hozzáférésekhez, például a Active Directory-tartomány vezérlőkhöz (DCs), a biztonsági eszközökhöz és a Rendszerfelügyeleti eszközökhöz. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

Az egységes hozzáférés-vezérlés érdekében a hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access) 

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: a biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. A biztonságos felhasználói munkaállomásokat és/vagy az Azure Bastion-t a felügyeleti feladatokhoz használhatja. Az Azure Active Directory (Azure AD), a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és/vagy Microsoft Intune használatával biztonságos és felügyelt felhasználói munkaállomás helyezhető üzembe felügyeleti feladatokhoz. A biztonságos munkaállomások központilag felügyelhetők a biztonságos konfiguráció kényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver alapkonfigurációit, a korlátozott logikai és hálózati hozzáférést.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Emelt szintű hozzáférési munkaállomás üzembe helyezése](../active-directory/devices/howto-azure-managed-workstation.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: a Content Delivery Network integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC segítségével kezelheti az Azure-erőforrások hozzáférését a szerepkör-hozzárendeléseken keresztül. Rendelje hozzá ezeket a szerepköröket a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal. 

Korlátozza az erőforrásokhoz hozzárendelt jogosultságokat az Azure RBAC, a szerepkörök által megkövetelt módon. Ez a megközelítés megfelel az Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) módszerének, és a jogosultságok rendszeres felülvizsgálatával jár. 

Emellett a beépített szerepkörök használatával is lefoglalhatja az engedélyt, és szükség esetén csak egyéni szerepkört hozhat létre.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md) 

- [Az RBAC konfigurálása az Azure-ban](../role-based-access-control/role-assignments-portal.md) 

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: eszközkezelés](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok biztonsági olvasói engedélyeket kapjanak az Azure-bérlőben és-előfizetésekben, hogy a biztonsági kockázatokat a Azure Security Center használatával felügyeljék. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. A biztonsági ismereteket és a kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

Megjegyzés: a számítási feladatok és szolgáltatások láthatóságához további engedélyekre lehet szükség. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra,-csoportokra és-előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Útmutató az erőforrások elnevezésével és címkézésével kapcsolatos döntésekhez](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával naplózhatja és korlátozhatja, hogy a felhasználók mely szolgáltatásokat tudják kiépíteni a környezetben. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat. A Azure Monitor használatával olyan szabályokat is létrehozhat, amelyek riasztásokat aktiválnak, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Az objektuméletciklus-kezelés biztonságának garantálása

**Útmutató**: a Content Network nem használható az eszközök biztonságának biztosítására az életciklus-kezelési folyamatokban. Az ügyfél feladata, hogy fenntartsa a nagy hatásnak számító eszközök attribútumait és hálózati konfigurációit. Azt javasoljuk, hogy az ügyfél hozzon létre egy folyamatot az attribútum és a hálózat konfigurációjának módosításainak rögzítéséhez, mérje fel a változást érintő hatásokat, és hozzon létre szervizelési feladatokat a megfelelő módon.

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: a Content Delivery Network nem biztosít natív képességeket az erőforrásaihoz kapcsolódó biztonsági fenyegetések figyelésére.

Továbbítsa a naplókat a Content Delivery Networkról a biztonsági információk és az Event Management (SIEM) megoldásra, amely az egyéni veszélyforrások észlelésének beállítására használható. 

Különböző típusú Azure-eszközöket figyelheti a potenciális fenyegetésekre és rendellenességekre összpontosítva, hogy magas színvonalú riasztásokat kapjon, hogy csökkentse a téves pozitív állapotot az elemzők számára a rendezéshez. A riasztások naplózási adatokból, ügynökökből vagy más adatokból is származnak.

- [Egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](../sentinel/tutorial-detect-threats-custom.md) 

- [A Cyber Threat Intelligence és az Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: az Azure hálózati tevékenységek naplózásának engedélyezése

**Útmutató**: a Content Delivery Network nem telepíthető virtuális hálózatokra, ezért nem engedélyezheti a hálózati biztonsági csoport folyamatának naplózását, tűzfalon keresztül irányíthatja át a forgalmat, vagy elvégezheti a csomagok rögzítését.

Content Delivery Network naplózza az összes olyan hálózati forgalmat, amelyet az ügyfél-hozzáféréshez dolgoz fel. Engedélyezze a hálózati flow funkciót az üzembe helyezett ajánlat erőforrásain belül, és konfigurálja ezeket a naplókat, hogy a hosszú távú megőrzés és ellenőrzés céljából elküldjék a Storage-fiókba.

- [Oktatóanyag – diagnosztikai napló beállítása az Azure-ban Content Delivery Network](cdn-azure-diagnostic-logs.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a tevékenységi naplók, amelyek automatikusan elérhetők, tartalmazzák a Content Delivery Network erőforrásainak összes írási műveletét (Put, post, DELETE), kivéve az olvasási MŰVELETEKET (Get). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

Engedélyezze az Azure-beli erőforrás-naplókat a CDN-hez, Azure Security Center és Azure Policy használatával engedélyezheti az erőforrás-naplókat és naplózhatja az adatgyűjtést. Ezek a naplók kritikus fontosságúak lehetnek a biztonsági incidensek kivizsgálásához és a kriminalisztikai gyakorlatok végrehajtásához.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md) 

- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

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

**Útmutató**: Győződjön meg arról, hogy rendelkezik a kiváló minőségű riasztások létrehozásához és a riasztások minőségének felméréséhez szükséges folyamattal. Ezzel tanulhat a korábbi incidensekből, és megadhatja a riasztások fontossági sorrendjét az elemzők számára, akik így nem pazarolnak időt a vakriasztásokra. 

Kiváló minőségű riasztások a korábbi incidensek tapasztalatai és ellenőrzött közösségi források alapján, valamint a riasztások létrehozásához és tisztításához tervezett eszközökkel hozhatók létre a különböző jelforrások egyesítésével és összehasonlításával. 

Az Azure Security Center számos Azure-objektum esetében kiváló minőségű riasztásokat biztosít. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

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

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy a Security Center mennyire biztos a találatban vagy a riasztás kibocsátásához használt elemzésben, valamint abban, hogy a riasztáshoz vezető tevékenység rosszindulatú volt.

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: biztonságos konfigurációk létrehozása számítási erőforrásokhoz

**Útmutató**: nem alkalmazható; A Content Delivery Network nem biztosít semmilyen biztonsági konfigurációs funkciót.

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Szoftveres biztonsági rések gyors és automatikus javítása

**Útmutató**: Gyorsan telepítheti a szoftverfrissítéseket az operációs rendszerek és alkalmazások szoftveres biztonsági réseinek javításához. 

A szervizelést a közös kockázati pontozási program (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítések alapján rangsorolhatja. Testre szabhatja környezetét, hogy az alkalmazások milyen magas biztonsági kockázatot jelentenek, és melyekhez magas üzemidő szükséges.

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

-   Az Azure natív védelmi funkcióinak és külső védelmi funkciók használata

-   Adattitkosítási követelmények a használatban lévő és a jelenleg nem használt használati esetekhez

-   Megfelelő titkosítási szabványok

További információ a hivatkozott hivatkozásokon érhető el.

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

További információ a hivatkozott hivatkozásokon érhető el.

- [Azure-biztonsági teljesítményteszt – Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure-biztonsági teljesítményteszt – Emelt szintű hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Az Azure ajánlott biztonsági eljárásai 11 – Architektúra. Egyetlen, egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure-identitáskezelés biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Naplózási stratégia és a fenyegetésekre adott válaszok stratégiájának meghatározása

**Útmutató**: Hozzon létre egy stratégiát a naplózáshoz és a fenyegetésekre adott válaszokhoz, amelyekkel gyorsan észlelheti és elháríthatja a fenyegetéseket, a megfelelőségi követelmények betartása mellett. Biztosítson jó minőségű riasztásokat és zökkenőmentes élményt az elemzőknek, így a fenyegetésekre összpontosíthatnak az integráció és a manuális lépések helyett. 

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait:

-   A biztonsági műveletekért (SecOps) felelős szervezet szerepköre és felelősségei 

-   Az incidensre adott, jól definiált válaszfolyamat, amely igazodik az NIST-hez és más iparági keretrendszerekhez 

-   Naplók rögzítése és megőrzése a fenyegetésészlelés, az incidensmegoldás és a megfelelőségi célkitűzések támogatásához

-   A fenyegetések SIEM, natív Azure-képességek és más erőforrások használatával biztosított, központosított láthatósága, illetve korrelációs adatai 

-   Az ügyfelek, a beszállítók és az érdekelt közszektorbeli felekkel folytatott értesítésének, valamint a velük folytatott kommunikáció terve

-   Natív Azure-beli és külső platformok használata az incidensek kezeléséhez, például a naplózáshoz és a fenyegetésészleléshez, a vizsgálathoz, és a támadások elhárításához és megsemmisítéséhez

-   Az incidenseket és az incidensek utáni tevékenységeket, például a levont következtetéseket és a bizonyítékmegőrzést kezelő folyamatok

További információ a hivatkozott hivatkozásokon érhető el.

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
