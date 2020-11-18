---
title: Content Delivery Network Azure biztonsági alapterve
description: A Content Delivery Network biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: azure-cdn
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64731ce443ddd806402efd33fe22300ab8362d18
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663521"
---
# <a name="azure-security-baseline-for-content-delivery-network"></a>Content Delivery Network Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) Content Delivery Networkre vonatkozó útmutatást alkalmaz. Az Azure biztonsági teljesítményteszt az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Content Delivery Network vonatkozó kapcsolódó útmutatás. A Content Delivery Network nem alkalmazható **vezérlők** ki vannak zárva.

Ha szeretné megtekinteni, hogyan Content Delivery Network teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Content Delivery Network biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identitáskezelés

*További információt az [Azure biztonsági teljesítményteszt: Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management)című témakörben talál.*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: az Azure erőforrás-hozzáférés korlátozása feltételek alapján

**Útmutató**: az Content Delivery Network a tartalom elérésének korlátozása ország vagy régió szerint. Szabályok létrehozása a CDN-végponton megadott elérési utakon, hogy engedélyezze vagy tiltsa le a kiválasztott országokban vagy régiókban lévő tartalmakat a Geo-szűrés funkció használatával.

- [Azure CDN tartalmának korlátozása ország vagy régió szerint](cdn-restrict-access-by-country.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információt az [Azure biztonsági teljesítményteszt: privilegizált hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)című témakörben talál.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata a Content Delivery Network az üzleti szempontból kritikus rendszerekhez való hozzáférés elkülönítését azáltal, hogy korlátozza, hogy mely fiókok emelt szintű hozzáférést kapnak az előfizetésekhez és a felügyeleti csoportokhoz.

Az üzleti szempontból kritikus fontosságú rendszerekre telepített ügynökökkel is korlátozhatja az olyan felügyeleti, identitási és biztonsági rendszerekhez való hozzáférést, amelyek rendszergazdai hozzáféréssel rendelkeznek az üzleti szempontból létfontosságú hozzáférésekhez, például a Active Directory-tartomány vezérlőkhöz (DCs), a biztonsági eszközökhöz és a Rendszerfelügyeleti eszközökhöz. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

Az egységes hozzáférés-vezérlés érdekében a hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access) 

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: a biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. A biztonságos felhasználói munkaállomásokat és/vagy az Azure Bastion-t a felügyeleti feladatokhoz használhatja. Az Azure Active Directory (Azure AD), a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és/vagy Microsoft Intune használatával biztonságos és felügyelt felhasználói munkaállomás helyezhető üzembe felügyeleti feladatokhoz. A biztonságos munkaállomások központilag felügyelhetők a biztonságos konfiguráció kényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver alapkonfigurációit, a korlátozott logikai és hálózati hozzáférést.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Emelt szintű hozzáférési munkaállomás üzembe helyezése](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: csak elég felügyelet (a legalacsonyabb jogosultsági elv) követése 

**Útmutató**: a Content Delivery Network integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC segítségével kezelheti az Azure-erőforrások hozzáférését a szerepkör-hozzárendeléseken keresztül. Rendelje hozzá ezeket a szerepköröket a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök tartoznak, és ezeket a szerepköröket az Azure CLI-vel, a Azure PowerShellsal vagy a Azure Portal eszközzel leltározott vagy lekérdezheti. 

Korlátozza az erőforrásokhoz hozzárendelt jogosultságokat az Azure RBAC, a szerepkörök által megkövetelt módon. Ez kiegészíti a Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) megközelítését, és rendszeresen felül kell vizsgálni. 

Emellett a beépített szerepkörök használatával is lefoglalhatja az engedélyt, és szükség esetén csak egyéni szerepkört hozhat létre.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md) 

- [A RBAC konfigurálása az Azure-ban](../role-based-access-control/role-assignments-portal.md) 

- [Az Azure AD-identitás és a hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információt az [Azure biztonsági teljesítményteszt: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)című témakörben talál.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Győződjön meg arról, hogy a biztonsági csapat az eszközök kockázataival rendelkezik

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok biztonsági olvasói engedélyeket kapjanak az Azure-bérlőben és-előfizetésekben, hogy a biztonsági kockázatokat a Azure Security Center használatával felügyeljék. 

A biztonsági csapat feladatainak strukturálása, a biztonsági kockázatok figyelése a központi biztonsági csapat vagy egy helyi csapat feladata lehet. A biztonsági ismereteket és a kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyei széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoportra), vagy a felügyeleti csoportokra vagy az adott előfizetésekre is. 

Megjegyzés: a számítási feladatok és szolgáltatások láthatóságához további engedélyekre lehet szükség. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure Management Groups áttekintése](../governance/management-groups/overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra,-csoportokra és-előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Útmutató az erőforrások elnevezésével és címkézésével kapcsolatos döntésekhez](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával naplózhatja és korlátozhatja, hogy a felhasználók mely szolgáltatásokat tudják kiépíteni a környezetben. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat. A Azure Monitor használatával olyan szabályokat is létrehozhat, amelyek riasztásokat aktiválnak, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: az eszközök életciklus-kezelésének biztonsága

**Útmutató**: a Content Network nem használható az eszközök biztonságának biztosítására az életciklus-kezelési folyamatokban. Az ügyfél feladata, hogy fenntartsa a nagy hatásnak számító eszközök attribútumait és hálózati konfigurációit. Azt javasoljuk, hogy az ügyfél hozzon létre egy folyamatot az attribútum és a hálózat konfigurációjának módosításainak rögzítéséhez, mérje fel a változást érintő hatásokat, és hozzon létre szervizelési feladatokat a megfelelő módon.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetések észlelése

*További információt az [Azure biztonsági teljesítményteszt: naplózás és fenyegetések észlelése](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)című témakörben talál.*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: a Content Delivery Network nem biztosít natív képességeket az erőforrásaihoz kapcsolódó biztonsági fenyegetések figyelésére.

Továbbítsa a naplókat a Content Delivery Networkról a biztonsági információk és az Event Management (SIEM) megoldásra, amely az egyéni veszélyforrások észlelésének beállítására használható. 

Különböző típusú Azure-eszközöket figyelheti a potenciális fenyegetésekre és rendellenességekre összpontosítva, hogy magas színvonalú riasztásokat kapjon, hogy csökkentse a téves pozitív állapotot az elemzők számára a rendezéshez. A riasztások naplózási adatokból, ügynökökből vagy más adatokból is származnak.

- [Egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](../sentinel/tutorial-detect-threats-custom.md) 

- [A Cyber Threat Intelligence és az Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: az Azure hálózati tevékenységek naplózásának engedélyezése

**Útmutató**: a Content Delivery Network nem telepíthető virtuális hálózatokra, ezért nem engedélyezheti a hálózati biztonsági csoport folyamatának naplózását, tűzfalon keresztül irányíthatja át a forgalmat, vagy elvégezheti a csomagok rögzítését.

Content Delivery Network naplózza az összes olyan hálózati forgalmat, amelyet az ügyfél-hozzáféréshez dolgoz fel. Engedélyezze a hálózati flow funkciót az üzembe helyezett ajánlat erőforrásain belül, és konfigurálja ezeket a naplókat, hogy a hosszú távú megőrzés és ellenőrzés céljából elküldjék a Storage-fiókba.

- [Oktatóanyag – diagnosztikai napló beállítása az Azure-ban Content Delivery Network](cdn-azure-diagnostic-logs.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a tevékenységi naplók, amelyek automatikusan elérhetők, tartalmazzák a Content Delivery Network erőforrásainak összes írási műveletét (Put, post, DELETE), kivéve az olvasási MŰVELETEKET (Get). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

Engedélyezze az Azure-beli erőforrás-naplókat a CDN-hez, Azure Security Center és Azure Policy használatával engedélyezheti az erőforrás-naplókat és naplózhatja az adatgyűjtést. Ezek a naplók kritikus fontosságúak lehetnek a biztonsági incidensek kivizsgálásához és a kriminalisztikai gyakorlatok végrehajtásához.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md) 

- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: biztonságos konfigurációk létrehozása számítási erőforrásokhoz

**Útmutató**: nem alkalmazható; A Content Delivery Network nem biztosít semmilyen biztonsági konfigurációs funkciót.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: a szoftveres biztonsági rések gyors és automatikus szervizelése

**Útmutató**: a szoftverfrissítések gyors központi telepítése a szoftverek sebezhetőségének javításához operációs rendszerekben és alkalmazásokban. 

A szervizelést a közös kockázati pontozási program (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítések alapján rangsorolhatja. Testre szabhatja környezetét, hogy az alkalmazások milyen magas biztonsági kockázatot jelentenek, és melyekhez magas üzemidő szükséges.

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

További információ a hivatkozott hivatkozásokon érhető el.

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

További információ a hivatkozott hivatkozásokon érhető el.

- [Azure biztonsági teljesítményteszt – Identitáskezelés](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure biztonsági teljesítményteszt – emelt szintű hozzáférés](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure Identity Management biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: a naplózási és a veszélyforrások reagálási stratégiájának meghatározása

**Útmutató**: naplózási és veszélyforrási reagálási stratégia létrehozása a fenyegetések gyors észlelése és elhárítása érdekében a megfelelőségi követelmények teljesítése mellett. Rangsorolja az elemzőket kiváló minőségű riasztásokkal és zökkenőmentes tapasztalatokkal, hogy az integráció és a manuális lépések helyett a fenyegetésekre összpontosítsanak. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait:

-   A biztonsági műveletek (SecOps) szervezet szerepköre és feladatai 

-   Egy jól definiált incidens-reagálási folyamat NIST vagy más iparági keretrendszerrel való összehangolása 

-   A fenyegetések észlelését, az incidensek megválaszolását és a megfelelőségi igényeket támogató napló rögzítése és megőrzése

-   A fenyegetésekkel kapcsolatos központosított láthatóság és korrelációs információk a SIEM, a natív Azure-képességek és más források használatával 

-   Kommunikációs és értesítési terv ügyfeleivel, szállítókkal és az érdekelt felekkel

-   Az Azure Native és a harmadik féltől származó platformok használata az incidensek kezelésére, mint például a naplózás és a veszélyforrások észlelése, a kriminalisztika, valamint a támadási problémák elhárítása és felszámolása

-   Az incidensek és az incidens utáni tevékenységek kezelésének folyamatai, például a tanulságok és a bizonyítékok megőrzése

További információ a hivatkozott hivatkozásokon érhető el.

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
