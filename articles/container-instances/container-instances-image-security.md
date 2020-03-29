---
title: Tárolópéldányok biztonsága
description: Javaslatok az Azure Container-példányok lemezképeinek és titkos titkainak védelmére, valamint általános biztonsági szempontok bármely tárolóplatformhoz
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: b5f2c4d9ca80318574e288110fd4ce7f490af00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76260497"
---
# <a name="security-considerations-for-azure-container-instances"></a>Az Azure Container Instances biztonsági szempontjai

Ez a cikk biztonsági szempontokat tartalmaz az Azure Container Instances használatával a tárolóalkalmazások futtatásához. A témakörök a következők:

> [!div class="checklist"]
> * **Biztonsági javaslatok** az Azure Container-példányok lemezképeinek és titkos titkainak kezelésére
> * **A tároló-ökoszisztémával kapcsolatos szempontok a** tároló teljes életciklusa során, bármely tárolóplatformesetében

## <a name="security-recommendations-for-azure-container-instances"></a>Biztonsági javaslatok az Azure Container-példányokhoz

### <a name="use-a-private-registry"></a>Privát rendszerleíró adatbázis használata

A tárolók egy vagy több adattárban található rendszerképekből állnak össze. Ezek az adattárak egy nyilvános beállításjegyzékhez, például a [Docker Hubhoz](https://hub.docker.com)vagy egy privát beállításjegyzékhez tartozhatnak. Privát beállításjegyzék pedig például a [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), amely a helyszínen vagy virtuális magánfelhőbe is telepíthető. Felhőalapú magántároló-beállításjegyzék-szolgáltatásokat is használhat, beleértve az [Azure Container Registry szolgáltatást](../container-registry/container-registry-intro.md)is. 

A nyilvánosan elérhető tárolórendszerkép nem garantálja a biztonságot. A tárolóképek több szoftverrétegből állnak, és minden szoftverrétegnek lehetnek biztonsági rései. A támadások veszélyének csökkentése érdekében tárolja és kérje le a rendszerképeket egy privát beállításjegyzékből, például az Azure Container Registry vagy a Docker Trusted Registry rendszerből. A felügyelt magánbeállításjegyzék biztosítása mellett az Azure Container Registry támogatja [az](../container-registry/container-registry-authentication.md) egyszerű hitelesítésszolgáltatást az Azure Active Directoryn keresztül az alapvető hitelesítési folyamatokhoz. Ez a hitelesítés magában foglalja a szerepköralapú hozzáférést csak olvasható (lekéréses), írási (leküldéses) és egyéb engedélyekhez.

### <a name="monitor-and-scan-container-images"></a>Tárolóképek figyelése és bekése

Használja ki a megoldásokkal a tárolóképek bekéseléséhez és a potenciális biztonsági rések azonosításához. Fontos megérteni a fenyegetésészlelés mélységét, amelyet a különböző megoldások nyújtanak.

Például az Azure Container Registry opcionálisan [integrálható az Azure Security Center](../security-center/azure-container-registry-integration.md) automatikusan beszivárga az összes Linux-lemezképek egy beállításjegyzékbe. Az Azure Security Center integrált Qualys-szkennere észleli a lemezképek biztonsági réseit, osztályosítja azokat, és szervizelési útmutatást nyújt.

A biztonsági figyelési és képolvasási megoldások, például a [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) és az [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) az Azure Piactéren is elérhetők.  

### <a name="protect-credentials"></a>Hitelesítő adatok védelme

A tárolók több fürtben és Azure-régióban is eloszthatók. Ezért biztonságossá kell tennia a bejelentkezésekhez vagy az API-hozzáféréshez szükséges hitelesítő adatokat, például a jelszavakat vagy a jogkivonatokat. Győződjön meg arról, hogy csak a kiemelt jogosultságú felhasználók férhetnek hozzá a tárolók hoz átvitel közben és inaktív. Leltározzon minden hitelesítő adattitkos titkot, majd a fejlesztőknek a tárolóplatformokhoz tervezett újonnan megjelenő titkos kulcsok-kezelési eszközöket kell használniuk.  Győződjön meg arról, hogy a megoldás titkosított adatbázisokat, tls-titkosítást tartalmaz az átvitel alatt lévő titkos kulcsokhoz és a legkisebb jogosultságú [szerepköralapú hozzáférés-vezérléshez.](../role-based-access-control/overview.md) [Az Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) egy felhőalapú szolgáltatás, amely védi a titkosítási kulcsokat és titkos kulcsokat (például tanúsítványokat, kapcsolati karakterláncokat és jelszavakat) a tárolóba kötött alkalmazások számára. Mivel ezek az adatok bizalmas és üzleti szempontból kritikus fontosságúak, biztonságos hozzáférést biztosít a kulcstartókhoz, így csak az engedélyezett alkalmazások és a felhasználók férhetnek hozzá.

## <a name="considerations-for-the-container-ecosystem"></a>A konténer-ökoszisztémával kapcsolatos szempontok

A következő, jól és hatékonyan kezelt biztonsági intézkedések segítségével megvédheti és megvédheti a tárolóökoszisztémát. Ezek az intézkedések a tároló teljes életciklusa során, a fejlesztéstől az éles környezetben, és a tároló vezénylők, gazdagépek és platformok számos. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>A biztonsági rés kezelésének használata a tárolófejlesztési életciklus részeként 

A hatékony biztonsági réskezelés használatával a tároló fejlesztési életciklusa során javíthatja az esélyeket, hogy azonosítsa és megoldja a biztonsági problémákat, mielőtt azok komolyabb problémává válnak. 

### <a name="scan-for-vulnerabilities"></a>Biztonsági rések vizsgálata 

A rendszer folyamatosan új biztonsági réseket fedez fel, ezért a biztonsági rések felderítése és azonosítása folyamatos folyamat. A biztonsági rés vizsgálatának beépítése a tároló teljes életciklusa során:

* A fejlesztési folyamat utolsó ellenőrzéseként biztonsági résvizsgálattal kell végeznie a tárolókon, mielőtt a rendszerképeket nyilvános vagy privát rendszerleíró adatbázisba szeretné helyezni. 
* Folytassa a tárolórendszerképek bevizsgálatát a rendszerleíró adatbázisban, hogy azonosítsa azokat a hibákat, amelyek valahogy kimaradtak a fejlesztés során, és hogy orvosolja az újonnan felfedezett biztonsági réseket, amelyek a tárolórendszerképekben használt kódban lehetnek.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>A lemezkép biztonsági rései leképezése futó tárolókhoz 

A tárolórendszerképekben azonosított biztonsági réseket tárolókhoz kell leképezni, így a biztonsági problémák mérsékelhetők vagy megoldhatók.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Győződjön meg arról, hogy csak jóváhagyott képek et használ a környezetben 

Van elég változás és a volatilitás egy konténer ökoszisztéma anélkül, hogy ismeretlen konténerek is. Csak jóváhagyott tárolóképek engedélyezése. Olyan eszközökkel és folyamatokkal rendelkezik, amelyek figyelik és megakadályozzák a nem jóváhagyott tárolóképek használatát. 

A támadási felület csökkentésének és a fejlesztők kritikus biztonsági hibák elhárításának hatékony módja a tárolóképek folyamatának szabályozása a fejlesztői környezetbe. Például előfordulhat, hogy egyetlen Linux-disztribúciót szankcionál egy alapképként, lehetőleg egy olyan, amely sovány (Alpine vagy CoreOS az Ubuntu helyett), hogy minimalizálja a felületet a potenciális támadásokhoz. 

A képaláírás vagy az ujjlenyomat-vétel felügyeleti láncot biztosít, amely lehetővé teszi a tárolók integritásának ellenőrzését. Például az Azure Container Registry támogatja a Docker [tartalommegbízhatósági](https://docs.docker.com/engine/security/trust/content_trust) modelljét, amely lehetővé teszi a rendszerképek közzétevői számára, hogy aláírja a beállításjegyzékbe leadott lemezképeket, és a rendszerképek fogyasztói csak aláírt lemezképeket kérnek le.

### <a name="permit-only-approved-registries"></a>Csak jóváhagyott kibocsátásijegyzékek engedélyezése 

Annak biztosítása, hogy a környezet csak jóváhagyott lemezképeket használjon, csak jóváhagyott tárolójegyzékek használatát engedélyezi. A jóváhagyott tárolónyilvántartások használatának megkövetelése csökkenti a kockázatnak való kitettséget azáltal, hogy korlátozza az ismeretlen biztonsági rések vagy biztonsági problémák kialakulásának lehetőségét. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>A képek integritásának biztosítása a teljes életciklus során 

A biztonság kezelése a tároló teljes életciklusa során része a tárolórendszerképek integritásának biztosítása a beállításjegyzékben, és ahogy azok módosulnak vagy éles környezetben vannak telepítve. 

* A biztonsági résű, még kisebb biztonsági rendszerű képek nem futtathatók éles környezetben. Ideális esetben az éles környezetben telepített összes lemezképet egy kiválasztott néhány számára elérhető privát beállításjegyzékbe kell menteni. Tartsa a termelési képek számát kicsiben, hogy hatékonyan lehessen kezelni őket.

* Mivel nehéz pontosan meghatározni a szoftver eredetét egy nyilvánosan elérhető tárolórendszerképből, hozzon létre képeket a forrásból, hogy a réteg eredetének ismerete. Ha a saját készítésű tárolórendszerképben jelentkezik valamilyen biztonsági rés, könnyebben található rá megoldás. Nyilvános képpel az ügyfeleknek meg kell találniuk a nyilvános kép gyökerét a javításhoz, vagy egy másik biztonságos képet kell kapniuk a közzétevőtől. 

* Az éles környezetben telepített, alaposan beolvasott lemezkép nem garantált, hogy az alkalmazás teljes élettartama alatt naprakész legyen. Biztonsági rések jelenhetnek meg a rendszerkép korábban nem ismert vagy az éles környezetben való üzembe helyezés után bevezetett rétegeiben. 

  Rendszeresen naplózza az éles környezetben telepített lemezképeket, hogy azonosítsa az elavult vagy egy ideje nem frissített lemezképeket. Előfordulhat, hogy a kék-zöld üzembe helyezési módszerek és a működés közbeni frissítési mechanizmusok segítségével frissítheti a tárolórendszerképeket állásidő nélkül. A képeket az előző szakaszban ismertetett eszközökkel lehet beszkacaba. 

* Használjon folyamatos integrációs (CI) folyamatot integrált biztonsági ellenőrzéssel a biztonságos lemezképek létrehozásához és a privát beállításjegyzékbe való leküldése érdekében. A CI megoldásba beépített biztonságirés-ellenőrzési funkció biztosítja, hogy az összes teszten megfelelő rendszerképek kerüljenek a privát beállításjegyzékbe, ahonnan az éles számítási feladatok üzembe helyezése történik. 

  A CI-folyamat hibája biztosítja, hogy a sebezhető lemezképek ne legyenek leküldéses a privát beállításjegyzékbe, amely et éles számítási feladatok üzembe helyezéséhez használnak. Emellett automatizálja a képbiztonsági szkennelést, ha jelentős számú kép van. Máskülönben a rendszerképek manuális vizsgálata rendkívül hosszadalmas és sok hibalehetőséget tartalmazó folyamat lenne. 

### <a name="enforce-least-privileges-in-runtime"></a>A legkisebb jogosultságok kényszerítése futásidőben 

A legkisebb jogosultságok fogalma alapvető biztonsági ajánlott eljárás, amely a tárolókra is vonatkozik. A biztonsági rés kihasználódása általában hozzáférést és a feltört alkalmazás vagy folyamat jogosultságaihoz biztosít hozzáférést a támadónak. Annak biztosítása, hogy a tárolók a legalacsonyabb jogosultságokkal működjenek, és a feladat elvégzéséhez szükséges hozzáférés csökkenti a kockázatnak való kitettséget. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Csökkentse a tárolótámadási felületet a szükségtelen jogosultságok eltávolításával 

A potenciális támadási felület et is minimalizálhatja, ha eltávolítja a nem használt vagy szükségtelen folyamatokat vagy jogosultságokat a tároló futásidejéből. A kiemelt tárolók gyökérként futnak. Ha egy rosszindulatú felhasználó vagy számítási feladat megszökik egy kiemelt tárolóban, a tároló majd root ként fut a rendszeren.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>A tároló által hozzáférhető vagy futtatható fájlok és végrehajtható fájlok előzetes jóváhagyása 

A változók vagy ismeretlenek számának csökkentése segít a stabil, megbízható környezet fenntartásában. A tárolók korlátozása, hogy csak előre jóváhagyott vagy biztonságos listán szereplő fájlokat és végrehajtható fájlokat és végrehajtható fájlokat tudjanak elérni vagy futtatni, bizonyított módszer a kockázatnak való kitettség korlátozására.  

Sokkal könnyebb kezelni egy safelist, ha végre a kezdetektől fogva. A biztonságos lista az ellenőrzés és a kezelhetőség mérését biztosítja, amikor megszeretné tudni, hogy milyen fájlokra és végrehajtható fájlokra van szükség az alkalmazás megfelelő működéséhez. 

A biztonságos lista nem csak csökkenti a támadási felületet, hanem egy alapkonfigurációt is biztosíthat az anomáliákhoz, és megakadályozhatja a "zajos szomszéd" és a tárolókitörési forgatókönyvek használati eseteit. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Hálózati szegmentálás kényszerítése futó tárolókon  

Az egyik alhálózat ban lévő tárolók védelme egy másik alhálózat biztonsági kockázataival szemben, a hálózati szegmentáció (vagy nano-szegmentálás) vagy a futó tárolók közötti elkülönítés fenntartása érdekében. A hálózati szegmentáció fenntartása is szükséges lehet a tárolók használatához olyan iparágakban, amelyek a megfelelőségi előírások teljesítéséhez szükségesek.  

Az [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) partnereszköz például automatizált megközelítést biztosít a nanoszegmentáláshoz. Az Aqua futásidőben figyeli a konténerhálózati tevékenységeket. Azonosítja az összes bejövő és kimenő hálózati kapcsolatot más tárolókhoz, szolgáltatásokhoz, IP-címekhez és a nyilvános internethez. A nanoszegmentálás automatikusan létrejön a figyelt forgalom alapján. 

### <a name="monitor-container-activity-and-user-access"></a>Tárolótevékenység és felhasználói hozzáférés figyelése 

Mint minden informatikai környezetben, következetesen figyelje a tevékenységet és a felhasználói hozzáférést a tároló ökoszisztémájához, hogy gyorsan azonosíthassa a gyanús vagy rosszindulatú tevékenységeket. Az Azure tárolófigyelési megoldásokat kínál, többek között:

* [Az Azure Monitor tárolók figyeli](../azure-monitor/insights/container-insights-overview.md) a kubernetes-környezetekben az Azure Kubernetes-szolgáltatás (AKS) üzemeltetett számítási feladatok teljesítményét. Az Azure Monitor tárolók hoz a teljesítmény láthatóságát a memória és a processzor metrikák a vezérlők, csomópontok és tárolók, amelyek a Kubernetes a Metrikák API-n keresztül érhetők el. 

* Az [Azure Container Monitoring megoldás](../azure-monitor/insights/containers.md) segítségével megtekintheti és kezelheti a többi Docker- és Windows-tárolóállomást egyetlen helyen. Példa:

  * Részletes naplózási információk megtekintése, amelyek a tárolókkal használt parancsokat jelenítik meg. 
  * A tárolók hibaelhárítása a központosított naplók megtekintésével és keresésével, a Docker- vagy Windows-gazdagépek távoli megtekintése nélkül.  
  * Keresse meg a tárolók, amelyek zajosak lehetnek, és a felesleges erőforrásokat a gazdagépen.
  * Megtekintheti a központi processzor-, memória-, tárolási és hálózati használati és teljesítményadatokat a tárolókhoz.  

  A megoldás támogatja a tároló vezénylőket, beleértve a Docker Swarm, DC/OS, a nem felügyelt Kubernetes, a Service Fabric és a Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Konténererőforrás-tevékenység figyelése 

Figyelje az erőforrás-tevékenységet, például a fájlokat, a hálózatot és a tárolók által elért egyéb erőforrásokat. Az erőforrás-tevékenység és -felhasználás figyelése hasznos mind a teljesítmény figyelése és biztonsági intézkedésként. 

[Az Azure Monitor](../azure-monitor/overview.md) lehetővé teszi az Azure-szolgáltatások alapvető figyelését a metrikák, a tevékenységnaplók és a diagnosztikai naplók gyűjtésének engedélyezésével. A tevékenységnaplók például elárulják, hogy mikor történt az új erőforrások létrehozása vagy módosítása. 

  Rendelkezésre állnak olyan metrikák, amelyek a virtuális gépek különböző erőforrásairól, sőt a virtuális gépen futó operációs rendszerről biztosítanak teljesítménystatisztikát. Az adatokat megtekintheti az Azure Portal valamelyik böngészőjében, és riasztásokat hozhat létre a metrikák alapján. Az Azure Monitor biztosítja a leggyorsabb metrikafolyamatot (5 perctől akár 1 percig), így az idő szempontjából kritikus fontosságú riasztásokhoz és értesítésekhez ezt a szolgáltatást érdemes használnia. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Az összes tárolófelügyeleti felhasználói hozzáférés naplózása 

A tároló-ökoszisztémához való rendszergazdai hozzáférés, beleértve a Kubernetes-fürthöz, a tároló-beállításjegyzékhez és a tárolórendszerképekhez való rendszergazdai hozzáférés pontos naplózási nyomvonalát. Ezek a naplók naplózási célokra lehet szükség, és hasznos lesz a törvényszéki bizonyíték után bármilyen biztonsági esemény. Az Azure-megoldások a következők:

* [Az Azure Kubernetes szolgáltatás integrációja az Azure Security Centerrel](../security-center/azure-kubernetes-service-integration.md) a fürtkörnyezet biztonsági konfigurációjának figyeléséhez és biztonsági javaslatok létrehozásához
* [Azure container figyelési megoldás](../azure-monitor/insights/containers.md)
* Erőforrásnaplók az [Azure Container-példányok](container-instances-log-analytics.md) és az [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>További lépések

* További információ az [Azure Security Center](../security-center/container-security.md) valós idejű fenyegetésészlelési célokra való használatáról a tárolóba helyezett környezetekben.

* Tudjon meg többet a tárolók sebezhetőségének kezeléséről a [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) és az [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/)megoldásaival.