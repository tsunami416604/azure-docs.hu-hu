---
title: Az Azure-eszközök biztonsági gyakorlati tanácsai
titleSuffix: Azure security
description: Ez a cikk az adatok, alkalmazások és egyéb eszközök azure-beli védelmére vonatkozó gyakorlati tanácsok készletét tartalmazza.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 5724a9e4308f05a82df84ae6a7d5602747f5a140
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757373"
---
# <a name="azure-operational-security-best-practices"></a>Az Azure Operational Security gyakorlati tanácsok
Ez a cikk az adatok, alkalmazások és egyéb eszközök azure-beli védelmére vonatkozó gyakorlati tanácsok készletét tartalmazza.

Az ajánlott eljárások a véleménykonszenzuson alapulnak, és a jelenlegi Azure platform-képességekkel és szolgáltatáskészletekkel működnek. A vélemények és a technológiák idővel változnak, és ezt a cikket rendszeresen frissítik, hogy tükrözze ezeket a változásokat.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Erős működési biztonsági gyakorlatok meghatározása és üzembe helyezése
Az Azure működési biztonsága a felhasználók számára elérhető szolgáltatásokra, vezérlőkre és funkciókra vonatkozik adataik, alkalmazásaik és egyéb eszközeik azure-beli védelmére. Az Azure működési biztonsága egy olyan keretrendszerre épül, amely magában foglalja a Microsoft egyedi képességeinek, többek között a [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl)és a [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) program, valamint a kiberbiztonsági fenyegetési környezet mély ismertségét.

## <a name="manage-and-monitor-user-passwords"></a>Felhasználói jelszavak kezelése és figyelése
Az alábbi táblázat a felhasználói jelszavak kezelésével kapcsolatos gyakorlati tanácsokat sorolja fel:

**Ajánlott eljárás:** Győződjön meg arról, hogy a megfelelő szintű jelszavas védelem a felhőben.   
**Részletesen**: Kövesse a [Microsoft Password Guidance (Microsoft password guidance)](https://www.microsoft.com/research/publication/password-guidance/)útmutatóját, amely a Microsoft identitásplatformok (Azure Active Directory, Active Directory és Microsoft-fiók) felhasználóiszámára terjed ki.

**Ajánlott eljárás:** A felhasználói fiókokhoz kapcsolódó gyanús műveletek figyelése.   
**Részletes:** Figyelheti a kockázatos és [kockázatos bejelentkezések](../../active-directory/reports-monitoring/concept-risk-events.md) [a kockázatos felhasználók](/azure/active-directory/reports-monitoring/concept-user-at-risk) az Azure AD biztonsági jelentések használatával.

**Ajánlott eljárás:** A magas kockázatú jelszavak automatikus észlelése és kiújítása.   
**Részlet:** [Az Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) az Azure AD Premium P2 kiadás egyik szolgáltatása, amely lehetővé teszi a következőket:

- A szervezet identitását érintő potenciális biztonsági rések észlelése
- Automatikus válaszok konfigurálása a szervezet identitásával kapcsolatos észlelt gyanús műveletekre
- Vizsgálja ki a gyanús eseményeket, és tegye meg a megfelelő lépéseket azok megoldására

## <a name="receive-incident-notifications-from-microsoft"></a>Incidensértesítések fogadása a Microsofttól
Győződjön meg arról, hogy a biztonsági műveleti csapat megkapja az Azure incidensértesítéseket a Microsofttól. Az incidensértesítés tudatja a biztonsági csapatgal, hogy feltörték az Azure-erőforrásokat, így azok gyorsan reagálhatnak a lehetséges biztonsági kockázatokra, és kitudják újítani azokat.

Az Azure regisztrációs portálon biztosíthatja, hogy a rendszergazdai kapcsolattartási adatok tartalmazzák a biztonsági műveleteket értesítő adatokat. A kapcsolattartási adatok egy e-mail cím és egy telefonszám.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure-előfizetések rendszerezése felügyeleti csoportokba
Ha a cég több előfizetéssel rendelkezik, szüksége lehet egy hatékony módszerre az előfizetések hozzáférésének, szabályzatainak és megfelelőségének kezelésére. [Az Azure felügyeleti csoportok](/azure/governance/management-groups/create) olyan hatókört biztosítanak, amely meghaladja az előfizetéseket. Az előfizetéseket felügyeleti csoportoknak nevezett tárolókba rendezheti, és a cégirányítási feltételeket a felügyeleti csoportokra alkalmazhatja. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket.

A felügyeleti csoportok és előfizetések rugalmas struktúráját létrehozhatja egy címtárba. Minden könyvtár kap egy legfelső szintű felügyeleti csoportot, amelyet gyökérfelügyeleti csoportnak neveznek. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. A gyökérfelügyeleti csoport lehetővé teszi a globális házirendek és RBAC-hozzárendelések könyvtárszinten történő alkalmazását.

Íme néhány gyakorlati tanács a felügyeleti csoportok használatával kapcsolatosan:

**Ajánlott eljárás:** Győződjön meg arról, hogy az új előfizetések olyan cégirányítási elemeket alkalmaznak, mint a szabályzatok és az engedélyek hozzáadásakor.   
**Részlet:** A gyökérfelügyeleti csoport használatával rendeljehozzá az összes Azure-eszközre vonatkozó vállalati szintű biztonsági elemeket. A házirendek és engedélyek példák az elemekre.

**Ajánlott eljárás:** A felügyeleti csoportok felső szintjeit a szegmentálási stratégiához igazítsa, hogy az egyes szegmenseken belüli ellenőrzés és házirend-konzisztencia pontot biztosítson.   
**Részlet**: Hozzon létre egy felügyeleti csoportot minden szegmenshez a gyökérfelügyeleti csoport ban. Ne hozzon létre más felügyeleti csoportokat a gyökér alatt.

**Ajánlott eljárás:** Korlátozza a felügyeleti csoport mélységét, hogy elkerülje a zavart, amely akadályozza mind a műveleteket, mind a biztonságot.   
**Részlet:** Korlátozza a hierarchiát három szintre, beleértve a gyökeret is.

**Ajánlott eljárás:** Gondosan válassza ki, hogy mely elemeket kell alkalmazni a teljes vállalatra a gyökérfelügyeleti csoporttal.   
**Részlet:** Győződjön meg arról, hogy a gyökérfelügyeleti csoport elemeinek egyértelműen alkalmaznikell az összes erőforrást, és hogy alacsony hatásúak.

Jó jelöltek közé tartozik:

- Egyértelmű üzleti hatással járó szabályozási követelmények (például az adatok szuverenitásával kapcsolatos korlátozások)
- A közel nulla potenciális negatív hatással bíró követelmények a műveletekre, például a naplózási hatású házirendre vagy a gondosan áttekintett RBAC-engedély-hozzárendelésre

**Ajánlott eljárás:** Gondosan tervezze meg és tesztelje az összes vállalati szintű módosítást a gyökérfelügyeleti csoporton, mielőtt alkalmazna (házirend, RBAC-modell és így tovább).   
**Részletes:** A legfelső szintű felügyeleti csoport változásai hatással lehetnek az Azure minden erőforrására. Bár hatékony módot biztosítanak a vállalaton belül a konzisztencia biztosítására, a hibák vagy a helytelen használat negatívan befolyásolhatja a termelési műveleteket. Tesztelje a gyökérfelügyeleti csoport összes módosítását egy tesztkörnyezetben vagy éles tesztpilotban.

## <a name="streamline-environment-creation-with-blueprints"></a>A környezet létrehozásának egyszerűsítése tervrajzokkal
[Az Azure Blueprints](/azure/governance/blueprints/overview) szolgáltatás lehetővé teszi a felhőalapú építészek és a központi informatikai csoportok számára, hogy olyan megismételhető Azure-erőforrásokat határozzanak meg, amelyek megvalósítják és betartják a szervezet szabványait, mintáit és követelményeit. Az Azure Blueprints lehetővé teszi a fejlesztői csapatok számára, hogy gyorsan építsenek ki és állítsanak fel új környezeteket beépített összetevőkkészletével és azzal a bizalommal, hogy ezeket a környezeteket szervezeti megfelelőségen belül hozják létre.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>A tárolási szolgáltatások figyelése a viselkedés váratlan változásaira
A felhőalapú környezetben üzemeltetett elosztott alkalmazások ban felmerülő problémák diagnosztizálása és elhárítása összetettebb lehet, mint a hagyományos környezetekben. Az alkalmazások paaS- vagy IaaS-infrastruktúrában, a helyszínen, a mobileszközön vagy ezek a környezetek valamilyen kombinációjában telepíthetők. Előfordulhat, hogy az alkalmazás hálózati forgalma áthalad a nyilvános és a magánhálózatokon, és az alkalmazás több tárolási technológiát is használhat.

Folyamatosan figyelje az alkalmazás által a viselkedés váratlan változásaira (például a lassabb válaszidőkre) használt tárolási szolgáltatásokat. A naplózás segítségével részletesebb adatokat gyűjthet, és részletesen elemezheti a problémát. A figyelési és naplózási diagnosztikai információk segítségével meghatározhatja az alkalmazás által észlelt probléma kiváltó okát. Ezután elháríthatja a problémát, és meghatározhatja a javításhoz szükséges lépéseket.

[Az Azure Storage Analytics](../../storage/common/storage-analytics.md) naplózást hajt végre, és metrikaadatokat biztosít egy Azure-tárfiókhoz. Azt javasoljuk, hogy használja ezeket az adatokat a kérelmek nyomon követéséhez, a használati trendek elemzéséhez és a tárfiókkal kapcsolatos problémák diagnosztizálásához.

## <a name="prevent-detect-and-respond-to-threats"></a>Fenyegetések megelőzése, észlelése és az azokra való reagálás
[Az Azure Security Center](../../security-center/security-center-intro.md) segítségével megelőzheti, észlelheti és reagálhat a fenyegetésekre azáltal, hogy nagyobb betekintést nyújt az Azure-erőforrások biztonságába (és annak szabályozására). Integrált biztonsági figyelést és házirend-kezelést biztosít az Azure-előfizetésekben, segít észlelni az egyébként észrevétlenül megjelenő fenyegetéseket, és különböző biztonsági megoldásokkal működik.

A Security Center ingyenes szintje csak az Azure-erőforrások korlátozott biztonságot nyújt. A Standard szint kiterjeszti ezeket a képességeket a helyszíni és egyéb felhőkre. A Security Center Standard segítségével megkeresheti és kijavíthatja a biztonsági réseket, hozzáférés- és alkalmazásvezérlőket alkalmazhat a rosszindulatú tevékenységek blokkolására, a fenyegetések elemzésés intelligencia használatával történő észleléséhez, valamint gyors reagáláshoz, ha támadás alatt áll. Az első 60 napban díjmentesen próbálhatja ki a Security Center Standard verzióját. Javasoljuk, hogy [frissítse Azure-előfizetését security center standardra.](../../security-center/security-center-get-started.md)

A Security Center segítségével az Összes Azure-erőforrás biztonsági állapotának központi megtekintéséhez. Egy pillantással ellenőrizze, hogy a megfelelő biztonsági vezérlők megfelelően vannak-e konfigurálva és konfigurálva, és gyorsan azonosítsa a figyelmet igénylő erőforrásokat.

A Security Center integrálható a [Microsoft Defender komplex veszélyforrások elleni védelemmel (ATP),](../../security-center/security-center-wdatp.md)amely átfogó végpontészlelési és -reagálási (EDR) képességeket biztosít. A Microsoft Defender ATP-integrációval észlelheti a rendellenességeket. A Security Center által figyelt kiszolgálóvégpontok speciális támadásait is észlelheti és reagálhatja azokra.

Szinte minden vállalati szervezet rendelkezik biztonsági információ- és eseménykezelési (SIEM) rendszerrel, amely a különböző jelgyűjtő eszközök naplóinformációinak konszolidálásával segít azonosítani az újonnan megjelenő fenyegetéseket. A naplókezután elemzi egy adatelemzési rendszer segítségével azonosítani, mi az "érdekes" a zaj, amely elkerülhetetlen minden napló gyűjtési és elemzési megoldások.

[Az Azure Sentinel](/azure/sentinel/overview) egy méretezhető, felhőalapú, biztonsági információk és eseménykezelés (SIEM) és biztonsági vezénylési automatizált válasz (SZÁRNYLatás) megoldás. Az Azure Sentinel intelligens biztonsági elemzést és fenyegetésfelderítést biztosít a riasztások észlelése, a fenyegetések láthatósága, a proaktív vadászat és az automatikus fenyegetésre adott válasz révén.

Íme néhány gyakorlati tanács a fenyegetések megelőzésére, észlelésére és az azokra való reagálásra:

**Ajánlott eljárás:** Növelje a SIEM-megoldás sebességét és méretezhetőségét egy felhőalapú SIEM használatával.   
**Részletesen:** Vizsgálja meg az [Azure Sentinel](/azure/sentinel/overview) funkcióit és képességeit, és hasonlítsa össze őket a jelenleg a helyszíni használattal rendelkező funkciókkal. Fontolja meg az Azure Sentinel bevezetését, ha megfelel a szervezet SIEM követelményeinek.

**Ajánlott eljárás:** Keresse meg a legsúlyosabb biztonsági réseket, hogy rangsorolhassa a vizsgálatot.   
**Részletes:** Tekintse át az [Azure biztonságos pontszám](../../security-center/secure-score-security-controls.md) áttekintheti az Azure-szabályzatok és az Azure Security Center beépített kezdeményezésekből származó javaslatokat. Ezek a javaslatok segítenek a legfontosabb kockázatok, például a biztonsági frissítések, a végpontvédelem, a titkosítás, a biztonsági konfigurációk, a hiányzó WAF, az internethez csatlakoztatott virtuális gépek és még sok más kezelésére.

A Biztonságos pontszám, amely az Internet-biztonsági Központ (CIS) vezérlőkön alapul, lehetővé teszi a szervezet Azure-biztonságának külső forrásokra való összehasonlítását. A külső ellenőrzés segít a csapat biztonsági stratégiájának érvényesítésében és bővítésében.

**Ajánlott eljárás:** A gépek, hálózatok, tárolási és adatszolgáltatások és alkalmazások biztonsági állapotának figyelése a lehetséges biztonsági problémák felderítése és rangsorolása érdekében.  
**Részlet**: Kövesse a Biztonsági központ [biztonsági ajánlásait,](../../security-center/security-center-recommendations.md) kezdve a legmagasabb prioritású elemekkel.

**Ajánlott eljárás:** Integrálja a Security Center riasztásait a biztonsági információkés eseménykezelési (SIEM) megoldásba.   
**Részletesen**: A SIEM-mel rendelkező szervezetek többsége központi elszámolóházként használja az elemzői választ igénylő biztonsági riasztásokhoz. A Security Center által létrehozott feldolgozott események et közzéteszik az Azure-tevékenységnaplóban, amely az Azure Monitoron keresztül elérhető naplók egyike. Az Azure Monitor egy konszolidált folyamatot kínál a figyelési adatok siem-eszközbe történő útválasztásához. Az utasításokat a [Biztonsági riasztások és javaslatok exportálása(b) (Biztonsági riasztások és javaslatok exportálása) (Információ: Biztonsági riasztások és javaslatok](../../security-center/continuous-export.md#configuring-siem-integration-via-azure-event-hubs) exportálása) (Információ: Biztonsági riasztások Ha Azure Sentinelt használ, olvassa el [az Azure Security Center csatlakoztatása című témakört.](../../sentinel/connect-azure-security-center.md)

**Ajánlott eljárás:** Integrálja az Azure-naplókat a SIEM-be.   
**Részletek**: Adatok [gyűjtéséhez és exportálásához](/azure/azure-monitor/overview#integrate-and-export-data)használja az Azure Monitort . Ez a gyakorlat kritikus fontosságú a biztonsági incidensek kivizsgálásának engedélyezéséhez, és az online naplómegőrzés korlátozott. Ha Az Azure Sentinelt használja, olvassa el az [Adatforrások csatlakoztatása című témakört.](../../sentinel/connect-data-sources.md)

**Ajánlott eljárás:** Gyorsítsa fel a vizsgálati és vadászati folyamatokat, és csökkentse a hamis pozitív a végpontészlelési és -válasz (EDR) képességek integrálásával a támadás vizsgálat.   
**Részletek:** [Engedélyezze a Microsoft Defender ATP-integrációt](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) a Security Center biztonsági házirendjében. Fontolja meg az Azure Sentinel használatát a fenyegetések vadászatára és az incidensekre adott válaszként.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Végpontok között a forgatókönyv-alapú hálózatfigyelés figyelése
Az ügyfelek az Azure-ban a hálózati erőforrások, például a virtuális hálózat, az ExpressRoute, az Application Gateway és a terheléselosztók kombinálásával építenek létre egy végpontok közötti hálózatot az Azure-ban. A figyelés minden hálózati erőforráson elérhető.

[Az Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy regionális szolgáltatás. Diagnosztikai és vizualizációs eszközeivel figyelheti és diagnosztizálhatja a feltételeket az Azure-ban, az Azure-ban és az Azure-ból hálózati forgatókönyv szintjén.

Az alábbiakban a hálózatfigyelés és a rendelkezésre álló eszközök ajánlott eljárásai találhatók.

**Ajánlott eljárás:** A távoli hálózatfigyelés automatizálása csomagrögzítéssel.  
**Részletes:** A hálózati problémák figyelése és diagnosztizálása anélkül, hogy bejelentkezne a virtuális gépekbe a Hálózati figyelő használatával. Riasztások beállításával indítsa el a [csomagrögzítést,](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) és férhessen hozzá a valós idejű teljesítményadatokhoz a csomag szintjén. Ha problémát lát, részletesen megvizsgálhatja a jobb diagnózisok érdekében.

**Ajánlott eljárás:** Folyamatnaplók használatával betekintést nyerhet a hálózati forgalomba.  
**Részletesen**: A hálózati biztonsági csoport [folyamatnaplóinak](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)használatával jobban megismerheti a hálózati forgalmi mintákat. A folyamatnaplókban lévő információk segítségével adatokat gyűjthet a megfelelőséghez, a naplózáshoz és a hálózati biztonsági profil figyeléséhez.

**Ajánlott eljárás:** A VPN-kapcsolattal kapcsolatos problémák diagnosztizálása.  
**Részletesen**: A Hálózatfigyelő vel [diagnosztizálhatja a leggyakoribb VPN-átjáró- és kapcsolati problémákat.](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md) Nem csak azonosítani a problémát, hanem részletes naplókat is használhat a további vizsgálathoz.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Biztonságos üzembe helyezés bevált DevOps-eszközökkel
Az alábbi DevOps-gyakorlati tanácsok segítségével biztosíthatja, hogy a vállalat és a csapatok hatékonyak és hatékonyak legyenek.

**Ajánlott eljárás:** A szolgáltatások összeállításának és telepítésének automatizálása.  
**Részlet**: [Az infrastruktúra mint kód](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) olyan technikák és gyakorlatok készlete, amelyek segítenek az informatikai szakembereknek eltávolítani a moduláris infrastruktúra napi felépítésének és felügyeletének terhét. Lehetővé teszi az informatikai szakemberek számára, hogy modern kiszolgálói környezetüket olyan módon építsék ki és tartsák karban, mint a szoftverfejlesztők az alkalmazáskód összeállításához és karbantartásához.

Az [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) használatával deklaratív sablon használatával kiépítheti az alkalmazásokat. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont használja az alkalmazás ismételt üzembe helyezéséhez az alkalmazás életciklusának minden szakaszában.

**Ajánlott eljárás:** Automatikusan hozhat létre és üzembe helyezheti az Azure-webalkalmazásokat vagy felhőszolgáltatásokat.  
**Részletek:** Beállíthatja az Azure DevOps-projekteket az Azure-webalkalmazások vagy felhőszolgáltatások [automatikus létrehozásához és üzembe helyezéséhez.](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) Az Azure DevOps automatikusan telepíti a bináris fájlokat, miután minden kódbeadás után buildelaz Azure-ba. A csomag összeállítási folyamata megegyezik a Visual Studio Csomag parancsával, és a közzétételi lépések egyenértékűek a Visual Studio Közzététel parancsával.

**Ajánlott eljárás:** A kiadások kezelésének automatizálása.  
**Részletesen:** [Az Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) egy olyan megoldás, amely automatizálja a többlépcsős üzembe helyezést és a kiadási folyamat kezelését. Felügyelt folyamatos üzembe helyezési folyamatok létrehozása gyors, egyszerű és gyakran történő felszabadításhoz. Az Azure Pipelines segítségével automatizálhatja a kiadási folyamatot, és előre definiált jóváhagyási munkafolyamatokat is létrehozhat. Üzembe helyezése a helyszínen és a felhőben, kiterjesztése és testreszabása, ha szükséges.

**Ajánlott eljárás:** Az alkalmazás teljesítményének ellenőrzése az indítás vagy az éles környezetben történő frissítések telepítése előtt.  
**Részletes :** Futtasson felhőalapú [terhelési teszteket](/azure/devops/test/load-test/overview#alternatives) a következőkre:

- Teljesítményproblémák keresése az alkalmazásban.
- Javítsa a telepítés minőségét.
- Győződjön meg arról, hogy az alkalmazás mindig elérhető.
- Győződjön meg arról, hogy az alkalmazás képes kezelni a forgalmat a következő indítási vagy marketingkampányhoz.

[Az Apache JMeter](https://jmeter.apache.org/) egy ingyenes, népszerű nyílt forráskódú eszköz, erős közösségi támogatással.

**Ajánlott eljárás:** Az alkalmazások teljesítményének figyelése.  
**Részletesen:** [Az Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető alkalmazásteljesítmény-kezelési (APM) szolgáltatás több platformon a webfejlesztők számára. Az Application Insights segítségével figyelheti az élő webalkalmazást. Automatikusan észleli a teljesítményanomáliákat. Olyan elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és annak megértésében, hogy a felhasználók valójában mit csinálnak az alkalmazással. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="mitigate-and-protect-against-ddos"></a>A DDoS csökkentése és védelme
Elosztott szolgáltatásmegtagadási (DDoS) egy olyan típusú támadás, amely megpróbálja kimeríteni az alkalmazás erőforrásait. A cél az, hogy befolyásolja az alkalmazás rendelkezésre állását és a jogos kérések kezelésére való képességét. Ezek a támadások egyre kifinomultabbés nagyobb méretű és hatása. Ezek célzott bármely végpont, amely nyilvánosan elérhető az interneten keresztül.

A DDoS rugalmasság tervezése és építése számos hibamód tervezését és tervezését igényli. Az alábbiakban a DDoS-rugalmas szolgáltatások Azure-beli létrehozásához ajánlott eljárásokat kell végezni.

**Ajánlott eljárás:** Győződjön meg arról, hogy a biztonság prioritást élvez az alkalmazás teljes életciklusa során, a tervezéstől és megvalósítástól az üzembe helyezésig és a műveletekig. Az alkalmazások olyan hibákat tartalmaznak, amelyek lehetővé teszik, hogy a kérelmek viszonylag kis mennyiségű erőforrást használjanak, ami szolgáltatáskimaradást eredményez.  
**Részletesen**: A Microsoft Azure-on futó szolgáltatások védelme érdekében jól meg kell ismernie az alkalmazásarchitektúrát, és a [szoftverminőség öt pillérére kell összpontosítania.](https://docs.microsoft.com/azure/architecture/guide/pillars) Ismernie kell a tipikus forgalmi kötetek, az alkalmazás és más alkalmazások közötti kapcsolati modell, valamint a szolgáltatás végpontok, amelyek ki vannak téve a nyilvános interneten.

Annak biztosítása, hogy egy alkalmazás elég rugalmas ahhoz, hogy kezelje a szolgáltatásmegtagadás, amely az alkalmazás maga a legfontosabb. A biztonság és az adatvédelem be van építve az Azure platformba, kezdve a [biztonsági fejlesztési életciklussal (SDL).](https://www.microsoft.com/sdl) Az SDL minden fejlesztési fázisban kezeli a biztonságot, és biztosítja, hogy az Azure folyamatosan frissüljön, hogy még biztonságosabb legyen.

**Ajánlott eljárás:** Az alkalmazások horizontális méretezése a felerősített terhelés igényének [megfelelően,](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) különösen DDoS-támadás esetén. Ha az alkalmazás egy szolgáltatás egyetlen példányától függ, egyetlen meghibásodási pontot hoz létre. Több példány kiépítése rugalmasabbá és méretezhetőbbé teszi a rendszert.  
**Részletek**: Az [Azure App Service,](/azure/app-service/app-service-value-prop-what-is)válasszon egy [App Service-csomag,](../../app-service/overview-hosting-plans.md) amely több példányt kínál.

Az Azure Cloud Services esetében konfigurálja az egyes szerepköröket úgy, hogy [több példányt használjanak.](../../cloud-services/cloud-services-choose-me.md)

[Az Azure virtuális gépek,](/azure/virtual-machines/windows/overview)győződjön meg arról, hogy a virtuális gép architektúra több virtuális gépet tartalmaz, és hogy minden virtuális gép szerepel egy [rendelkezésre állási készlet.](/azure/virtual-machines/virtual-machines-windows-manage-availability) Azt javasoljuk, hogy a virtuális gép méretezési készletek automatikus skálázási képességek.

**Ajánlott eljárás:** A biztonsági védelem rétegezése egy alkalmazásban csökkenti a sikeres támadás esélyét. Az Azure platform beépített funkcióinak használatával biztonságos terveket valósíthatja meg alkalmazásaiszámára.  
**Részlet**: A támadás kockázata az alkalmazás méretével (felületével) nő. Csökkentheti a felületet az engedélyezési lista használatával a terheléselosztók[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) és [Azure Application Gateway)](/azure/application-gateway/application-gateway-create-probe-portal)által nem szükséges IP-címterület és figyelőportok bezárásához.

[A hálózati biztonsági csoportok](../../virtual-network/security-overview.md) egy másik módja a támadási felület csökkentésének. [A szolgáltatáscímkék](../../virtual-network/security-overview.md#service-tags) és [az alkalmazásbiztonsági csoportok](../../virtual-network/security-overview.md#application-security-groups) segítségével minimalizálhatja a biztonsági szabályok létrehozásának összetettségét és konfigurálhatja a hálózati biztonságot, mint az alkalmazás szerkezetének természetes kiterjesztését.

Amikor csak lehetséges, üzembe kell helyeznie az Azure-szolgáltatásokat egy [virtuális hálózatban.](../../virtual-network/virtual-networks-overview.md) Ez a gyakorlat lehetővé teszi, hogy a szolgáltatás erőforrások privát IP-címeken keresztül kommunikáljon. A virtuális hálózatról származó Azure-szolgáltatásforgalom alapértelmezés szerint nyilvános IP-címeket használ forrás IP-címként.

A [szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) használata szolgáltatásforgalmat vált a virtuális hálózati magáncímek, mint a forrás IP-címek, amikor az Azure-szolgáltatás elérése egy virtuális hálózatról.

Gyakran látjuk, hogy az ügyfelek helyszíni erőforrásait az Azure-beli erőforrásokkal együtt támadják meg. Ha helyszíni környezetet csatlakoztat az Azure-hoz, minimalizálja a helyszíni erőforrások nyilvános internettel való kitettségét.

Az Azure két [DDoS-szolgáltatásajánlattal rendelkezik,](../../virtual-network/ddos-protection-overview.md) amelyek védelmet nyújtanak a hálózati támadásokkal szemben:

- Az alapszintű védelem alapértelmezés szerint további költségek nélkül integrálódik az Azure-ba. A globálisan üzembe helyezett Azure-hálózat méretezése és kapacitása védelmet nyújt a gyakori hálózati szintű támadások ellen a forgalom folyamatos figyelése és a valós idejű megoldások révén. Az Alapszintű szolgáltatás nem igényel felhasználói konfigurációt vagy alkalmazásmódosításokat, és segít megvédeni az összes Azure-szolgáltatást, beleértve a PaaS-szolgáltatásokat, például az Azure DNS-t.
- A szabványos védelem speciális DDoS-kockázatcsökkentési képességeket biztosít a hálózati támadások ellen. A rendszer automatikusan bevan hangolva az adott Azure-erőforrások védelme érdekében. A védelem egyszerűen engedélyezhetővé teszi a virtuális hálózatok létrehozása során. A létrehozás után is elvégezhető, és nem igényel alkalmazás- vagy erőforrás-módosításokat.

## <a name="enable-azure-policy"></a>Azure-szabályzat engedélyezése
[Az Azure Policy](/azure/governance/policy/overview) egy azure-beli szolgáltatás, amelyet szabályzatok létrehozására, hozzárendeléséhez és kezeléséhez használ. Ezek a szabályzatok szabályokat és effektusokat kényszerítenek ki az erőforrásokra, így ezek az erőforrások megfelelnek a vállalati szabványoknak és a szolgáltatásiszint-szerződéseknek. Az Azure Policy úgy tesz eleget ezeknek az elvárásoknak, hogy kiértékeli, megfelelnek-e az erőforrások a hozzájuk rendelt szabályzatoknak.

Engedélyezze az Azure-szabályzatot a szervezet írásbeli szabályzatának figyeléséhez és érvényesítéséhez. Ez biztosítja a vállalat vagy a szabályozási biztonsági követelmények nek való megfelelést azáltal, hogy központilag kezeli a biztonsági házirendeket a hibrid felhőbeli számítási feladatok ban. További információ a [megfelelőség irásának kikényszerítésére vonatkozó szabályzatok létrehozásáról és kezeléséről.](../../governance/policy/tutorials/create-and-manage.md) Tekintse meg [az Azure-szabályzat definíciós struktúráját](../../governance/policy/concepts/definition-structure.md) a szabályzat elemeinek áttekintéséhez.

Íme néhány ajánlott biztonsági gyakorlat az Azure-szabályzat elfogadása után:

**Ajánlott eljárás**: A szabályzat többféle effektust támogat. Ezekről az [Azure Policy definition struktúrájában](../../governance/policy/concepts/definition-structure.md#policy-rule)olvashat. Az üzleti műveleteket negatívan befolyásolhatja a **megtagadási** hatás és a **javító** hatás, ezért kezdje a **naplózási** hatással, hogy korlátozza a házirend negatív hatásának kockázatát.   
**Részletes :** [Indítsa el a házirend-telepítéseket vizsgálati módban,](../../governance/policy/concepts/definition-structure.md#policy-rule) majd később indítsa el a **megtagadást** vagy **a javítást**. Tesztelje és tekintse át az ellenőrzési hatás eredményeit, mielőtt a **megtagadásra** vagy **a javításra térne át.**

További információt a [Megfelelőségi házirendek létrehozása és kezelése](../../governance/policy/tutorials/create-and-manage.md)című témakörben talál.

**Ajánlott eljárás:** Azonosítsa az okat a szerepköröket, amelyek az irányelvek megsértésének figyeléséért és a megfelelő javítási művelet gyors biztosításáért felelősek.   
**Részlet:** A hozzárendelt szerepkör-figyelő megfelelőségaz [Azure Portalon](../../governance/policy/how-to/get-compliance-data.md#portal) vagy a [parancssoron](../../governance/policy/how-to/get-compliance-data.md#command-line)keresztül.

**Ajánlott eljárás:** Az Azure-szabályzat a szervezet írásbeli szabályzatainak technikai ábrázolása. Az összes Azure-szabályzatot szervezeti szabályzathoz rendelje hozzá a félreértések csökkentése és a konzisztencia növelése érdekében.   
**Részlet:** Dokumentum-leképezés a szervezet dokumentációjában vagy magában az Azure-szabályzatban azáltal, hogy hivatkozást ad hozzá a szervezeti szabályzathoz az [Azure-szabályzat leírásában](../../governance/policy/concepts/definition-structure.md#display-name-and-description) vagy az Azure-szabályzat [kezdeményezés](../../governance/policy/concepts/definition-structure.md#initiatives) leírásában.

## <a name="monitor-azure-ad-risk-reports"></a>Az Azure AD kockázati jelentésének figyelése
A biztonsági rések túlnyomó többsége akkor történik, amikor a támadók a felhasználó személyazonosságának ellopásával férnek hozzá egy környezethez. A feltört identitások felfedezése nem könnyű feladat. Az Azure AD adaptív gépi tanulási algoritmusokat és heurisztikát használ a felhasználói fiókokhoz kapcsolódó gyanús műveletek észlelésére. Minden észlelt gyanús művelet et egy [kockázatészlelésnek](../../active-directory/reports-monitoring/concept-risk-events.md)nevezett rekord tárol. A kockázatészlelések az Azure AD biztonsági jelentéseiben kerülnek rögzítésre. További információt a [kockázatnak kitett biztonsági jelentésről](../../active-directory/reports-monitoring/concept-user-at-risk.md) és a [kockázatos bejelentkezések biztonsági jelentéséről](../../active-directory/reports-monitoring/concept-risky-sign-ins.md)talál.

## <a name="next-steps"></a>További lépések
Tekintse meg [az Azure biztonsági gyakorlati tanácsait és mintáit,](best-practices-and-patterns.md) amelyek további biztonsági gyakorlati tanácsokat tartalmaznak a felhőbeli megoldások tervezése, üzembe helyezése és kezelése során az Azure használatával.

Az alábbi források az Azure biztonságával és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általánosabb információk biztosításához érhetők el:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) – naprakész információk az Azure Security legújabb adatairól
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – ahol a Microsoft biztonsági rései, beleértve az Azure-ral kapcsolatos problémákat is, jelenthetők, vagy e-mailben asecure@microsoft.com
