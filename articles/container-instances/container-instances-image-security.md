---
title: Az Azure Container Instances biztonsági szempontok
description: Javaslatok a képek és titkos kulcsok biztonságos Azure Container Instances szolgáltatásban, és bármely tárolóplatform általános biztonsági szempontjai
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943996"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure Container Instances szolgáltatásban történő futtatásának biztonsági szempontjai

Ez a cikk bemutatja az Azure Container Instances használatával a tárolóalapú alkalmazások futtatásához kapcsolódó biztonsági szempontok. A témakörök a következők:

> [!div class="checklist"]
> * **Biztonsági javaslatok** képek és titkos kulcsok kezeléséhez az Azure Container Instances szolgáltatásban
> * **A tároló-ökoszisztéma szempontjai** tároló életciklusán bármely tároló platformhoz

## <a name="security-recommendations-for-azure-container-instances"></a>Biztonsági javaslatok az Azure Container Instances szolgáltatásban

### <a name="use-a-private-registry"></a>Privát beállításjegyzék

A tárolók egy vagy több adattárban található rendszerképekből állnak össze. Az alábbi két adattár tartozhatnak a nyilvános beállításjegyzék például [Docker Hub](https://hub.docker.com), vagy privát regisztrációs adatbázisba. Privát beállításjegyzék pedig például a [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), amely a helyszínen vagy virtuális magánfelhőbe is telepíthető. Felhőalapú privát tárolójegyzék-szolgáltatások, többek között is használhatja [Azure Container Registry](../container-registry/container-registry-intro.md). 

A nyilvánosan elérhető tárolórendszerképek sem garantálják a biztonságot. Tárolórendszerképek több áll, és mindegyik rétegben lehetnek előfordulhat, hogy a biztonsági rések. A veszélyforrások elleni támadások csökkentése érdekében tárolja és egy privát tárolójegyzék, például az Azure Container Registry vagy a Docker Trusted Registry rendszerképek lekéréséhez. Amellett, hogy egy felügyelt privát beállításjegyzéket, az Azure Container Registry támogatja a [szolgáltatás egyszerűszolgáltatás-alapú hitelesítést](../container-registry/container-registry-authentication.md) alapszintű hitelesítési folyamatok az Azure Active Directoryn keresztül. A hitelesítés szerepkörön alapuló hozzáférés a csak olvasható (lekéréses), (leküldés) írási és tulajdonosi engedélyekkel tartalmazza.

### <a name="monitor-and-scan-container-images"></a>Figyelheti, és tárolórendszerképeket vizsgálata

Biztonsági monitorozási és ellenőrzési megoldásokkal [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) és [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) érhetők el az Azure piactéren. Vizsgálat privát beállításjegyzékben lévő tárolórendszerképek, és azonosítja a potenciális biztonsági réseket használhatja őket. Fontos milyen részletességű beolvasásának, hogy a különböző megoldásokat biztosítson. 

### <a name="protect-credentials"></a>Hitelesítő adatok védelme

Tárolók több fürtök és az Azure-régiók között is terjednek. Tehát biztosítania kell bejelentkezések vagy API-hozzáférés, például jelszavak vagy jogkivonatok szükséges hitelesítő adatokat. Győződjön meg arról, hogy csak jogosultsággal rendelkező felhasználók férhetnek hozzá ezeket a tárolókat, az átvitel során, míg az inaktív. Készlet összes hitelesítő adat titkos kulcsot, és ezután a fejlesztők számára, hogy használja az újonnan felbukkanó-titkos kódok-felügyeleti eszközöket, amelyek célja a tárolóplatformokhoz igényli.  Győződjön meg arról, hogy a megoldás magában foglalja a titkosított adatbázis, a TLS-titkosítás az átvitel során, és minimális jogosultságon alapuló titkos adatok [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md). [Az Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) van egy felhőszolgáltatás, amely megvédi a titkosítási kulcsok és titkos kulcsokat (például a tanúsítványok, kapcsolati karakterláncok és jelszavak) a tárolóalapú alkalmazásokat. Mivel ezek az adatok bizalmas és a kritikus fontosságú üzleti, biztonságos hozzáférés a kulcshoz tárolók, így csak az engedélyezett alkalmazások és a felhasználók elérhetik őket.

## <a name="considerations-for-the-container-ecosystem"></a>A tároló-ökoszisztéma szempontjai

A következő biztonsági intézkedések, jól megvalósítva, és hatékonyan, felügyelt segítségével biztonságos és a tároló ökoszisztéma védelme. Ezek intézkedéseket a fejlesztéstől az éles üzembe helyezését, és számos olyan tárolóvezénylőt, a gazdagépek és a platformok a alkalmazni tároló életciklusa során. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>A tároló fejlesztési életciklus során részeként használja a biztonsági rések kezelése 

A hatékony biztonsági rések kezelése, a tároló fejlesztési életciklus során, a valószínűségét, hogy azonosítani és megoldani a biztonsági kérdések, mielőtt azok több súlyos hiba javítása. 

### <a name="scan-for-vulnerabilities"></a>Biztonsági rések keresése 

Új biztonsági rések felderítésekor mindig, így a keresése, és biztonsági rések azonosítása folyamatosan működő folyamat. Biztonsági rés tároló életciklusa során vizsgálata foglalják magukban:

* A fejlesztési folyamat utolsó ellenőrzése, végre kell hajtania egy biztonsági rések keresése a tárolók, mielőtt leküldené a képek egy nyilvános vagy privát regisztrációs adatbázisba. 
* Továbbra is keresni a beállításjegyzék minden hibát, amely valamilyen módon hiányzik a fejlesztés során azonosítása és megoldása, amelyek valószínűleg szerepelnek a tárolórendszerképeket használt kódot újonnan felfedezett biztonsági rések tárolórendszerképek.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Térkép rendszerkép biztonsági réseket a futó tárolók 

Szüksége lesz egy azt jelenti, hogy a leképezés biztonsági rések a futó tárolók, biztonsági problémák problémák elhárításáról is lehet, vagy megoldott tárolórendszerképek azonosítja.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Győződjön meg arról, hogy csak a jóváhagyott lemezképeket a környezetben használt 

Nincs elegendő változás- és a egy tároló-ökoszisztéma nem engedélyezi az ismeretlen tárolókhoz is volatilitás. Csak a jóváhagyott tárolórendszerképek engedélyezése. Figyelheti, és letilthatja az jóvá nem hagyott tárolórendszerképek tartalmaznak eszközökkel és folyamatokkal. 

Hatékony módszer a csökkentve a támadási felületet, és meggátolja, hogy a fejlesztők számára kritikus fontosságú biztonsági hibát vét, hogy irányíthatja a tárolórendszerképek a fejlesztési környezetben. Például előfordulhat, hogy akár alaprendszerképként, lehetőleg letisztult egyetlen Linux-disztribúció engedélyezését (Alpine CoreOS helyett vagy Ubuntu), a potenciális támadások felület minimalizálása érdekében. 

Lemezkép-aláírást vagy az ujjlenyomat, amely lehetővé teszi, hogy a tárolók sértetlenségének ellenőrzése felügyeleti lánc láncolatától biztosíthat. Például az Azure Container Registry támogatja a Docker [megbízhatósági tartalom](https://docs.docker.com/engine/security/trust/content_trust) modell, amely lehetővé teszi, hogy a rendszerkép-közzétevők rendszerkép található, akkor lesznek leküldve a beállításjegyzék aláírásához, és a lemezkép a felhasználók a lekéréses csak aláírt lemezképek.

### <a name="permit-only-approved-registries"></a>Csak a jóváhagyott beállításjegyzékek engedélyezése 

Egy bővítmény, biztosítsa, hogy a környezet használja csak a jóváhagyott lemezképeket, hogy csak a jóváhagyott tárolóregisztrációs adatbázis használatát teszi lehetővé. Nincs szükség a jóváhagyott tároló-beállításjegyzékek csökkenti a fenyegetéseknek való kockázati, korlátozza a potenciális ismeretlen biztonsági réseket, és a biztonsági problémák bevezetésével. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Képek a teljes életciklusán sértetlenségének biztosítása 

Tároló életciklusa során biztonság kezelése része a tárolórendszerképeket a beállításjegyzék integritásának biztosításához és, módosítani vagy éles környezetben helyezi üzembe. 

* Rendszerképek biztonsági résekről, akár kisebb, nem engedélyezhető egy éles környezetben való futtatásához. Ideális esetben az éles környezetben telepített összes rendszerkép elérhető kevesen privát beállításjegyzékben lévő kell menteni. Maradjon kicsi annak érdekében, hogy kezelhető legyen hatékonyan éles képek számát.

* Mivel a tárolórendszerképekben lévő szoftverek származása egy nyilvánosan elérhető tárolórendszerképek nehezen deríthető, hozhat létre képeket a forrás-, hogy ismerjük a réteg eredetét. Ha a saját készítésű tárolórendszerképben jelentkezik valamilyen biztonsági rés, könnyebben található rá megoldás. Nyilvános rendszerképek ügyfelek kellene javítást, vagy szerezze be egy másik, biztonságos rendszerképet a közzétevőtől nyilvános rendszerkép gyökerében található. 

* Egy éles környezetben üzembe helyezett alaposan ellenőrzött rendszerképek az alkalmazás teljes élettartama a vírusdefiníciók naprakészségének nem garantált. Biztonsági rések jelenhetnek meg a rendszerkép korábban nem ismert vagy az éles környezetben való üzembe helyezés után bevezetett rétegeiben. 

  Elavult vagy egy ideje nem frissített rendszerképek azonosítása érdekében éles környezetben üzembe helyezett rendszerképeket rendszeresen naplózza. Kék-zöld üzembe helyezéssel és működés közbeni frissítési mechanizmusokkal használatával lehet, hogy a tárolórendszerképek állásidő nélküli frissítése. A kiszűrhető az előző szakaszban leírt eszközök segítségével. 

* Használhat egy folyamatos integrációs (CI) folyamat integrált biztonsági biztonságos lemezképeket, és azok leküldése a privát tárolójegyzék ellenőrzése. A CI megoldásba beépített biztonságirés-ellenőrzési funkció biztosítja, hogy az összes teszten megfelelő rendszerképek kerüljenek a privát beállításjegyzékbe, ahonnan az éles számítási feladatok üzembe helyezése történik. 

  CI folyamat meghiúsul biztosítja, hogy a sebezhető rendszerképek nem kerülnek a privát beállításjegyzékbe, hogy éles számítási feladatok üzembe helyezéséhez használt. Automatizálja a rendszerképek biztonsági ellenőrzése, ha sok rendszerkép van is. Máskülönben a rendszerképek manuális vizsgálata rendkívül hosszadalmas és sok hibalehetőséget tartalmazó folyamat lenne. 

### <a name="enforce-least-privileges-in-runtime"></a>Minimálisan a futtatókörnyezeti kényszerítése 

A legalacsonyabb jogosultságok fogalma alapvető biztonsági szempontból ajánlott, hogy a tárolók is vonatkozik. Egy biztonsági rés van ki, általában lehetővé teszi a támadók hozzáférést és jogosultságokat a sérült alkalmazás vagy folyamat egyenlő. Annak biztosítása, hogy a tárolók a legalacsonyabb szintű jogosultságokat kapnak, és hozzáférés szükséges a feladatot a fenyegetéseknek való kockázati csökkenti. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>A tároló támadási felület csökkentése a szükségtelen jogosultságokkal eltávolításával 

A potenciális támadási felületet is minimalizálhatja a használaton kívüli vagy szükségtelen folyamatok vagy jogosultságokkal távolítsa el a tároló-futtatókörnyezet. Legfelső szintű rendszerjogosultságú tárolók futnak. Ha egy rosszindulatú felhasználó vagy a számítási feladat lehet kilépni egy emelt szintű tárolóban, a tárolót fog futni legfelső szintű az adott rendszerben.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Engedélyezett fájlokat és végrehajtható fájlok a tároló elérésére vagy futtatására van engedélyezve. 

A változók vagy unknowns számának csökkentését segítségével biztosítja a stabil és megbízható környezetet. Egy jól bevált módszerrel kitettségének kockázata, tárolók hozzáféréssel rendelkezzenek, vagy csak előzetesen Futtatás vagy engedélyezési listához hozzáadni kívánt fájlok és végrehajtható fájlok korlátozásával.  

Egy engedélyezési lista kezelése az elejétől megadásuk sokkal könnyebbé válik. Engedélyezett mérhető, ellenőrzési és kezelhetőség, megtudhatja, mely fájlokat és a végrehajtható fájlok az alkalmazás megfelelő működéséhez szükségesek. 

Egy engedélyezési lista nem csak csökkentve a támadási felületet, de is adja meg a referenciakonfiguráció a rendellenességeket, és megakadályozza a "zajos szomszédok" és a tároló kiscsoportos forgatókönyvek a használati esetek. 

### <a name="enforce-network-segmentation-on-running-containers"></a>A futó tárolók hálózati szegmentálást kényszerítése  

A biztonsági kockázatoktól egy másik alhálózatban az egyik alhálózat tárolók védelme érdekében karbantartása hálózati szegmentálást (vagy a nano-szegmentálást) vagy a futó tárolók között elkülönítése. Karbantartása hálózati szegmentálást is szükséges lehet a tárolók használatával az iparágban, amelyek megfelelnek a megfelelőségi megbízások szükségesek.  

Például a partner eszköz [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) egy automatizált módszert biztosít a nano-szegmentálást. Tengerkék futásidejű tároló hálózati tevékenységek figyelése. Azonosítja, hogy az összes bejövő és kimenő hálózati kapcsolatot és-tárolókról más tárolók, szolgáltatások, IP-címek és a nyilvános interneten. A nano-szegmentálást automatikusan létrejön a megfigyelt adatforgalomban alapján. 

### <a name="monitor-container-activity-and-user-access"></a>Tároló tevékenység és a felhasználói hozzáférés figyelése 

Csakúgy, mint bármely informatikai környezetben konzisztens módon a tároló-ökoszisztéma gyorsan azonosíthatja a gyanús vagy rosszindulatú tevékenység célszerű figyelemmel tevékenység és a felhasználói hozzáférés. Az Azure biztosít a tároló monitorozási megoldásokat, többek között:

* [A tárolók az Azure Monitor](../azure-monitor/insights/container-insights-overview.md) figyelése az Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes-környezetekből üzembe helyezett munkaterhelések teljesítményét. Tárolók az Azure Monitor lehetővé teszi gyűjtését memória és a mérőszámok a processzor teljesítménye látható-e a tartományvezérlők, a csomópontok és a Kubernetes, a metrikák API-n keresztül a rendelkezésre álló tárolók. 

* A [Azure Tárolómonitorozási megoldás](../azure-monitor/insights/containers.md) segít megtekinthető és kezelhető a Docker és a Windows más tárológazdagép egyetlen helyen. Példa:

  * Megtekintheti a részletes naplózási információkat, amelyek használják tárolókhoz parancsok láthatók. 
  * Végezzen hibaelhárítást a tárolók megtekintésével és központosított naplók keresése távolról megtekintheti a Docker és a Windows gazdagépekre nélkül.  
  * Tárolók, amelyek lehetnek zajos és a felhasználó felesleges erőforrások a gazdagépen található.
  * Központi Processzor, memória, tárolási és hálózati használatának és teljesítményének adatait tárolók megtekintéséhez.  

  A megoldás többek között a Docker Swarm, DC/OS, a nem felügyelt Kubernetes, a Service Fabric és Red Hat OpenShift tárolóvezénylőt támogat. 

### <a name="monitor-container-resource-activity"></a>Tároló-erőforrás tevékenység figyelése 

Figyelése az erőforrás, például fájlok, hálózati és más erőforrások, amelyek a tárolók eléréséhez. Felügyeleti erőforrás-tevékenység és a használat akkor hasznos, az alkalmazásteljesítmény-figyelő- és biztonsági intézkedésként. 

[Az Azure Monitor](../azure-monitor/overview.md) lehetővé teszi, hogy alapvető metrikákat, a vizsgálati naplók és a diagnosztikai naplók gyűjteményét azáltal, hogy az Azure-szolgáltatásokhoz figyelés. A tevékenységnaplók például elárulják, hogy mikor történt az új erőforrások létrehozása vagy módosítása. 

Rendelkezésre állnak olyan metrikák, amelyek a virtuális gépek különböző erőforrásairól, sőt a virtuális gépen futó operációs rendszerről biztosítanak teljesítménystatisztikát. Az adatokat megtekintheti az Azure Portal valamelyik böngészőjében, és riasztásokat hozhat létre a metrikák alapján. Az Azure Monitor biztosítja a leggyorsabb metrikák folyamat (1 perces lefelé 5 perc), így a kritikus fontosságú riasztások és értesítések kell használni. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Minden tároló rendszergazdai felhasználói hozzáférés naplózásának naplózása 

A tároló ökoszisztéma, a tároló-beállításjegyzék és a tárolórendszerkép rendszergazdai hozzáféréssel pontos auditnaplót karbantartása. Ezek a naplók naplózási célokra szükség lehet, és a biztonsági incidensek után lesz hasznos Törvényszéki bizonyítékaként. Használhatja a [Azure Tárolómonitorozási megoldás](../azure-monitor/insights/containers.md) e cél eléréséhez. 

## <a name="next-steps"></a>További lépések

* További információ az megoldásaival a tároló biztonsági rések kezelése [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) és [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* Tudjon meg többet [tárolóbiztonság az Azure-ban](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).