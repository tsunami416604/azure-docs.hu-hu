---
title: A Azure Security Center újdonságai archívuma
description: A Azure Security Center újdonságait és változásait hat hónappal ezelőtt és korábban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: cebc2c3d364cdb93c4b528c186dcef79c58ac6ea
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167011"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archiválja a Azure Security Center újdonságait?

A [Azure Security Center legfontosabb újdonságai?](release-notes.md) a kiadási megjegyzések oldal az elmúlt hat hónapban tartalmaz frissítéseket, míg ez az oldal régebbi elemeket tartalmaz.

Ez az oldal a következő információkat tartalmazza:

- Új funkciók
- Hibajavítások
- Elavult funkciók


## <a name="february-2020"></a>2020. február

### <a name="fileless-attack-detection-for-linux-preview"></a>Fájl alapú támadás észlelése Linux rendszerhez (előzetes verzió)

Mivel a támadók egyre nagyobb mértékben alkalmaznak legrejtettebb módszereket az észlelés elkerülésére, Azure Security Center a Windows rendszereken kívül kiterjeszti a fájl-alapú támadási észlelést Linux rendszeren. A szoftveres biztonsági réseket rosszindulatú támadások okozják, a kártékony hasznos adatokat a jóindulatú rendszerfolyamatok szúrják be, és elrejtik a memóriában. Ezek a technikák:

- a kártevők nyomkövetésének csökkentése vagy megszüntetése lemezen
- nagy mértékben csökkentheti a lemezes kártevő-ellenőrzési megoldások általi észlelés esélyét

Ennek a fenyegetésnek a ellensúlyozására a 2018-es, a Windows rendszerhez készült, a fájloktól mentesen észlelhető támadás észlelését Azure Security Center, és a Linuxon is kiterjesztette a fájlokkal való támadás észlelését. 



## <a name="january-2020"></a>2020. január

### <a name="enhanced-secure-score-preview"></a>Bővített biztonságos pontszám (előzetes verzió)

A Azure Security Center biztonságos pontszám funkciójának továbbfejlesztett verziója már előzetes verzióban érhető el. Ebben a verzióban a több javaslat olyan biztonsági vezérlőkbe van csoportosítva, amelyek jobban tükrözik a sebezhető támadási felületet (például a felügyeleti portokhoz való hozzáférés korlátozása).

Ismerkedjen meg a biztonsági pontszám változásaival az előzetes verzió fázisában, és határozza meg azokat a további szervizeléseket, amelyek segítséget nyújtanak a környezet további védelméhez.

További információ a [bővített biztonságos pontszámról (előzetes verzió)](secure-score-security-controls.md).



## <a name="november-2019"></a>2019. november

A novemberi frissítések a következők:
 - [Veszélyforrások elleni védelem Észak-Amerika régiók Azure Key Vault számára (előzetes verzió)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Az Azure Storage veszélyforrások elleni védelme magában foglalja a kártevők ismertségének szűrését](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Munkafolyamat-automatizálás Logic Appssal (előzetes verzió)](#workflow-automation-with-logic-apps-preview)
 - [Általánosan elérhető a tömeges erőforrások gyors javítása](#quick-fix-for-bulk-resources-generally-available)
 - [Biztonsági rések keresése a tárolók rendszerképein (előzetes verzió)](#scan-container-images-for-vulnerabilities-preview)
 - [További szabályozási megfelelőségi szabványok (előzetes verzió)](#additional-regulatory-compliance-standards-preview)
 - [Veszélyforrások elleni védelem az Azure Kubernetes szolgáltatásban (előzetes verzió)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Virtuális gépek sebezhetőségének felmérése (előzetes verzió)](#virtual-machine-vulnerability-assessment-preview)
 - [Speciális adatbiztonság az Azure-beli SQL-kiszolgálókon Virtual Machines (előzetes verzió)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Egyéni szabályzatok támogatása (előzetes verzió)](#support-for-custom-policies-preview)
 - [Azure Security Center lefedettség kiterjesztése közösségi és partneri platformmal](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Speciális integrációk javaslatok és riasztások exportálásával (előzetes verzió)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Helyszíni helyszíni kiszolgálók Security Center a Windows felügyeleti központból (előzetes verzió)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Veszélyforrások elleni védelem Észak-Amerika régiók Azure Key Vault számára (előzetes verzió)

Azure Key Vault egy alapvető szolgáltatás az adatok védelméhez és a felhőalapú alkalmazások teljesítményének javításához azáltal, hogy központilag kezeli a kulcsokat, a titkokat, a titkosítási kulcsokat és a szabályzatokat a felhőben. Mivel Azure Key Vault bizalmas és üzleti szempontból kritikus fontosságú adatokat tárol, a kulcstartók és a bennük tárolt adatok maximális biztonsága szükséges.

Azure Security Center a veszélyforrások elleni védelem támogatása Azure Key Vault a biztonsági intelligencia egy további rétegét nyújtja, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a kulcstartók eléréséhez vagy kihasználásához. Ez az új védelmi réteg lehetővé teszi az ügyfelek számára, hogy biztonsági szakértő nélkül kezeljék a kulcstárolókkal szembeni fenyegetéseket, és nem rendelkeznek biztonsági szakértőkkel. A funkció nyilvános előzetes verzióban érhető el Észak-Amerika régióban.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Az Azure Storage veszélyforrások elleni védelme magában foglalja a kártevők ismertségének szűrését

Az Azure Storage veszélyforrások elleni védelme olyan új észleléseket kínál, amelyek a Microsoft fenyegetésekkel kapcsolatos intelligenciával észlelik a kártevők feltöltését az Azure Storage-ba a kivonatok hírnevének elemzése és az aktív Tor kilépési csomópontról (egy anonimizálásával-proxy) gyanús hozzáférés Mostantól a Azure Security Center használatával megtekintheti az észlelt kártevőket a Storage-fiókok között.


### <a name="workflow-automation-with-logic-apps-preview"></a>Munkafolyamat-automatizálás Logic Appssal (előzetes verzió)

A központilag felügyelt biztonságot és IT-műveleteket végző szervezetek belső munkafolyamat-folyamatokat vezetnek be a szükséges műveletek elvégzéséhez a szervezeten belül, ha eltérések észlelhetők a környezetében. Sok esetben ezek a munkafolyamatok ismételhető folyamatok, az automatizálás pedig nagy mértékben egyszerűsítheti a szervezeten belüli folyamatokat.

Ma egy új képességet vezetünk be Security Center, amely lehetővé teszi, hogy az ügyfelek automatizálási konfigurációkat hozzanak létre Azure Logic Apps és olyan házirendeket hozzanak létre, amelyek automatikusan elindítják őket az adott ASC-megállapítások, például javaslatok vagy riasztások alapján. Az Azure Logic app beállítható úgy, hogy a logikai alkalmazás-összekötők hatalmas közössége által támogatott egyéni műveleteket hajtsa végre, vagy használja a Security Center által biztosított sablonok valamelyikét, például e-mailek küldését vagy ServiceNow™ jegy megnyitását.

A munkafolyamatok futtatásának automatikus és manuális Security Center funkcióival kapcsolatos további információkért lásd: [munkafolyamat-automatizálás](workflow-automation.md).

Logic Apps létrehozásával kapcsolatos további tudnivalókért lásd: [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Általánosan elérhető a tömeges erőforrások gyors javítása

Számos feladattal, amelyet a felhasználók a biztonságos pontszám részeként adnak meg, nagy kihívást jelenthet a jelentős flották problémáinak hatékony elhárítása.

A biztonsági hibák elhárításának leegyszerűsítése és az erőforrások nagy mennyiségű és biztonságos pontszámának javítása érdekében gyorsan javíthatja az ajánlásokat.

Ezzel a művelettel kiválaszthatja azokat az erőforrásokat, amelyekre alkalmazni kívánja a szervizelést, és elindít egy szervizelési műveletet, amely az Ön nevében konfigurálja a beállítást.

A gyors javítás általánosan elérhető a Security Center javaslatok oldalának részeként.

Tekintse meg, hogy mely javaslatok esetében van engedélyezve a gyors javítás a útmutatóban a [biztonsági javaslatok eléréséhez](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Biztonsági rések keresése a tárolók rendszerképein (előzetes verzió)

A Azure Security Center mostantól megvizsgálhatja a tároló lemezképeit Azure Container Registry a biztonsági rések érdekében.

A képvizsgálat a tároló képfájljának elemzésével működik, majd ellenőrzi, hogy vannak-e ismert sebezhetőségek (Qualys-alapú).

A vizsgálat automatikusan elindul, amikor az új tároló lemezképeit Azure Container Registryba küldi. A biztonsági rések felszíne Security Center javaslatként, és az Azure-os biztonsági pontszámban is szerepel, valamint arról, hogy miként lehet javítani őket a támadási felület méretének csökkentése érdekében.


### <a name="additional-regulatory-compliance-standards-preview"></a>További szabályozási megfelelőségi szabványok (előzetes verzió)

A megfelelőségi irányítópult Security Center értékelések alapján betekintést nyújt a megfelelőségi helyzetbe. Az irányítópult azt mutatja be, hogy a környezet megfelel-e az egyes szabályozási szabványok és iparági referenciaértékek által meghatározott szabályozásoknak és követelményeknek, és előírásos ajánlásokat biztosít a követelmények megoldásához.

A szabályozási megfelelőségi irányítópult eddig négy beépített szabványt támogat: az Azure CIS 1.1.0, a PCI-DSS, az ISO 27001 és a SOC-TSP. Most bejelentjük a további támogatott szabványok nyilvános előzetes kiadását: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Kanada szövetségi PBMM és az Egyesült Királyság hivatalos tagja, valamint az Egyesült Királysági NHS. Emellett az Azure CIS 1.1.0 frissített verzióját is kiadjuk, amely a standard és a bővíthetőség növelését is magában foglalja.

[További információ a szabványok készletének testreszabásáról a szabályozási megfelelőségi irányítópulton](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Veszélyforrások elleni védelem az Azure Kubernetes szolgáltatásban (előzetes verzió)

A Kubernetes gyorsan megkezdi a szoftverek Felhőbeli üzembe helyezésének és felügyeletének új szabványát. A Kubernetes széles körű tapasztalattal rendelkeznek, és számos az általános mérnöki és adminisztrációs funkciókra koncentrál, és a biztonsági szempontokat is figyelembe kell venni. A Kubernetes-környezetet körültekintően kell konfigurálni, így biztos lehet abban, hogy a tárolók célzott támadási felülete nem marad nyitva a támadók számára. Security Center kibővíti a tároló területén található támogatását az Azure-beli Kubernetes szolgáltatás (ak) egyik leggyorsabb növekedési szolgáltatásával.

A nyilvános előzetes kiadás új funkciói a következők:

- **Felderítés & láthatóság** – a felügyelt AK-példányok folyamatos észlelése Security Center regisztrált előfizetései között.
- Biztonsági **pontszámokra vonatkozó javaslatok** – a gyakorlatban használható elemek biztosítják, hogy az ügyfelek az ügyfelek biztonságos pontszámának részeként megfeleljenek az AK biztonsággal kapcsolatos ajánlott eljárásainak, például a "szerepköralapú Access Control a Kubernetes való hozzáférés korlátozására."
- **Fenyegetések észlelése** – gazdagép-és fürt-alapú elemzések, például "a rendszerjogosultságú tároló észlelve".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Virtuális gépek sebezhetőségének felmérése (előzetes verzió)

A virtuális gépekre telepített alkalmazások gyakran olyan biztonsági réseket tartalmazhatnak, amelyek a virtuális gép megszegéséhez vezethetnek. Bejelentjük, hogy a Security Center standard szintű csomag beépített sebezhetőségi felmérést tartalmaz a virtuális gépekhez, felár nélkül. A nyilvános előzetes verzióban a Qualys által biztosított sebezhetőségi felmérés lehetővé teszi, hogy folyamatosan megvizsgálja a virtuális gépen telepített összes alkalmazást, hogy megkeresse a sebezhető alkalmazásokat, és megmutassa az eredményeket a Security Center portál felületén. Security Center gondoskodik az összes üzembe helyezési műveletről, így a felhasználó nem igényel további munkát. A jövőben a sebezhetőségi felmérési lehetőségek megadását tervezzük az ügyfeleink egyedi üzleti igényeinek kielégítése érdekében.

[További információ az Azure-Virtual Machines sebezhetőségi felméréséről](security-center-vulnerability-assessment-recommendations.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Speciális adatbiztonság az Azure-beli SQL-kiszolgálókon Virtual Machines (előzetes verzió)

A IaaS-alapú virtuális gépeken futó SQL-adatbázisok biztonsági rések elleni védelme és a sebezhetőségi felmérés Azure Security Center támogatása már előzetes verzióban érhető el.

A [sebezhetőségi felmérés](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni és követni az adatbázisok lehetséges biztonsági réseit, és segít orvosolni azokat. Az Azure biztonságos pontszámának részeként biztosítja a biztonsági helyzet láthatóságát, és tartalmazza a biztonsági problémák megoldásához és az adatbázis-erődítmények fejlesztéséhez szükséges lépéseket.

A komplex [veszélyforrások elleni védelem](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket mutatnak az SQL Server eléréséhez vagy kiaknázásához. A szolgáltatás folyamatosan figyeli az adatbázist a gyanús tevékenységekhez, és a rendellenes adatbázis-hozzáférési mintákon biztosít műveleti alapú biztonsági riasztásokat. Ezek a riasztások biztosítják a gyanús tevékenység részleteit, valamint az ajánlott műveleteket a fenyegetés kivizsgálásához és enyhítéséhez.


### <a name="support-for-custom-policies-preview"></a>Egyéni szabályzatok támogatása (előzetes verzió)

A Azure Security Center mostantól támogatja az egyéni házirendeket (előzetes verzió).

Ügyfeleinknek a Azure Policy-ban létrehozott szabályzatok alapján a saját biztonsági értékeléseit Szeretnék kiterjeszteni a Security Centerra. Az egyéni szabályzatok támogatása mostantól lehetséges.

Ezek az új szabályzatok a Security Center javaslatok, a biztonságos pontszám és a szabályozási megfelelőségi szabványok irányítópultjának részei lesznek. Az egyéni szabályzatok támogatásával mostantól létrehozhat egy egyéni kezdeményezést Azure Policyban, majd hozzáadhatja a szabályzatként Security Center és megjelenítheti azt javaslatként.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Azure Security Center lefedettség kiterjesztése közösségi és partneri platformmal

A Security Center használatával nem csupán a Microsofttól, hanem a partnerek meglévő megoldásaitól, például az ellenőrzési ponttól, a tartástól és a CyberArk származó megoldásoktól is kaphat javaslatokat.  Security Center egyszerű bevezetési folyamata összekapcsolhatja meglévő megoldásait Security Center, így egyetlen helyen tekintheti meg a biztonsági helyzetekre vonatkozó javaslatokat, egységes jelentéseket futtathat, és az összes Security Center funkcióját kihasználhatja a beépített és a partneri javaslatokkal szemben. A partnerek termékeire Security Center javaslatokat is exportálhat.

[További információ a Microsoft intelligens biztonsági társításáról](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Speciális integrációk javaslatok és riasztások exportálásával (előzetes verzió)

A nagyvállalati szintű forgatókönyvek Security Centeron való engedélyezéséhez mostantól lehetőség van Security Center riasztások és javaslatok használatára a Azure Portal vagy API kivételével további helyeken. Ezek közvetlenül exportálhatók egy Event hubhoz és Log Analytics munkaterületekre. Íme néhány munkafolyamat, amely az új funkciók köré hozható létre:

- Az Log Analytics munkaterületre való exportálással egyéni irányítópultokat hozhat létre Power BI használatával.
- Az Event hub-ba való exportálással Security Center riasztásokat és ajánlásokat exportálhat a harmadik féltől származó SIEM-re, egy harmadik féltől származó megoldásra valós időben vagy az Azure Adatkezelő.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Helyszíni helyszíni kiszolgálók Security Center a Windows felügyeleti központból (előzetes verzió)

A Windows felügyeleti központ egy olyan Windows-kiszolgálókhoz készült felügyeleti portál, amely nem az Azure-ban van üzembe helyezve, és több Azure-felügyeleti funkciót is kínál, például biztonsági mentési és rendszerfrissítéseket. Mostanában lehetőség van arra, hogy a nem Azure-beli kiszolgálókat közvetlenül a Windows felügyeleti központ felhasználói felületéről lehessen védeni.

Ezzel az új felhasználói felülettel a WAC-kiszolgáló bekerül a Azure Security Centerba, és lehetővé teszi a biztonsági riasztások és javaslatok közvetlen megtekintését a Windows felügyeleti központ felhasználói felületén.


## <a name="september-2019"></a>2019. szeptember

A szeptemberben elérhető frissítések a következők:

 - [Szabályok kezelése adaptív alkalmazás-vezérlőkkel – tökéletesítések](#managing-rules-with-adaptive-application-controls-improvements)
 - [A tárolók biztonsági javaslatának szabályozása Azure Policy használatával](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Szabályok kezelése adaptív alkalmazás-vezérlőkkel – tökéletesítések

Továbbfejlesztettük a virtuális gépekre vonatkozó szabályok kezelésének élményét az adaptív alkalmazások vezérlőinek használatával. Azure Security Center adaptív alkalmazás-vezérlői segítségével szabályozhatja, hogy mely alkalmazások futhatnak a virtuális gépeken. A szabályok kezelésének általános fejlesztésén kívül az új előny lehetővé teszi annak szabályozását, hogy az új szabályok hozzáadásakor milyen fájltípusok lesznek védve.

[További információ az adaptív alkalmazások vezérlőinek használatáról](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>A tárolók biztonsági javaslatának szabályozása Azure Policy használatával

Azure Security Center a biztonsági rések szervizelése a tárolók biztonsága szolgáltatásban mostantól engedélyezhető vagy letiltható a Azure Policy használatával.

Az engedélyezett biztonsági szabályzatok megtekintéséhez Security Center nyissa meg a biztonsági házirend lapot.


## <a name="august-2019"></a>2019. augusztus

Az augusztusi frissítések a következők:

 - [Igény szerinti (JIT) VM-hozzáférés Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Egyszeri kattintás szervizelése a biztonsági helyzet fokozása érdekében (előzetes verzió)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Bérlők közötti felügyelet](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Igény szerinti (JIT) VM-hozzáférés Azure Firewall 

Az igény szerinti (JIT) virtuálisgép-hozzáférés Azure Firewall már általánosan elérhető. Ezzel biztosíthatja Azure Firewall védett környezetek védelmét a NSG által védett környezetek mellett.

A JIT VM-hozzáférés csökkenti a hálózati sávszélesség-alapú támadásoknak való kitettséget azáltal, hogy az NSG és a Azure Firewall szabályok segítségével csak szükség esetén felügyelt hozzáférést biztosít a virtuális gépekhez.

Ha engedélyezi a JIT-t a virtuális gépek számára, létre kell hoznia egy házirendet, amely meghatározza a védeni kívánt portokat, valamint azt, hogy mennyi ideig legyenek nyitva a portok, és hogy mely IP-címek érhetők el. Ez a szabályzat segít megtekinteni, hogy mit tehetnek a felhasználók, amikor hozzáférést igényelnek.

A rendszer naplózza a kérelmeket az Azure-tevékenység naplójában, így egyszerűen figyelheti és naplózhatja a hozzáférést. Az igény szerinti oldal segítségével gyorsan azonosíthatja azokat a meglévő virtuális gépeket, amelyeken a JIT engedélyezve van, és a virtuális gépek, amelyeken a JIT ajánlott.

[További információ a Azure Firewallról](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Egyszeri kattintás szervizelése a biztonsági helyzet fokozása érdekében (előzetes verzió)

A biztonságos pontszám egy olyan eszköz, amely segít felmérni a munkaterhelés biztonsági helyzetét. Megtekinti a biztonsági javaslatokat, és rangsorolja őket az Ön számára, így biztos lehet benne, hogy mely ajánlásokat kell elvégeznie. Ez segít megtalálni a legsúlyosabb biztonsági réseket a vizsgálat rangsorolása érdekében.

A biztonsági hibák elhárításának leegyszerűsítése és a biztonságos pontszám gyors javítása érdekében új képességgel bővült, amely lehetővé teszi, hogy egy kattintással több erőforrásra vonatkozó ajánlást javítsanak ki.

Ezzel a művelettel kiválaszthatja azokat az erőforrásokat, amelyekre alkalmazni kívánja a szervizelést, és elindít egy szervizelési műveletet, amely az Ön nevében konfigurálja a beállítást.

Tekintse meg, hogy mely javaslatok esetében van engedélyezve a gyors javítás a útmutatóban a [biztonsági javaslatok eléréséhez](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Bérlők közötti felügyelet

A Security Center mostantól támogatja a több-bérlős felügyeleti forgatókönyveket az Azure Lighthouse részeként. Ez lehetővé teszi, hogy átláthatóbb és felügyelje Security Center több bérlő biztonsági állapotát. 

[További információ a több-bérlős felügyeleti](security-center-cross-tenant-management.md)környezetekről.


## <a name="july-2019"></a>2019. július

### <a name="updates-to-network-recommendations"></a>Hálózati javaslatok frissítései

A Azure Security Center (ASC) új hálózatkezelési ajánlásokat indított, és bizonyos meglévőket fejlesztett ki. Mostantól a Security Center használatával még nagyobb hálózati védelmet biztosíthat az erőforrásai számára. 

[További információ a hálózati javaslatokról](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>2019. június

### <a name="adaptive-network-hardening---generally-available"></a>Adaptív hálózat megerősítése – általánosan elérhető

A nyilvános felhőben futó számítási feladatok egyik legnagyobb támadási felülete a nyilvános internetről érkező és a felé irányuló kapcsolat. Ügyfeleink nehezen tudják, hogy mely hálózati biztonsági csoportokra (NSG) vonatkozó szabályokra van szükség ahhoz, hogy az Azure-beli munkaterhelések csak a szükséges forrástartomány számára legyenek elérhetők. Ezzel a szolgáltatással a Security Center megismerheti az Azure-beli számítási feladatok hálózati forgalmát és kapcsolati mintáit, és NSG-szabályokra vonatkozó ajánlásokat biztosít az internet felé irányuló virtuális gépekhez. Így az ügyfél jobban konfigurálhatja a hálózati hozzáférési házirendeket, és korlátozhatja a támadásoknak való kitettséget. 

[További információ az adaptív hálózat megerősítéséről](security-center-adaptive-network-hardening.md).
