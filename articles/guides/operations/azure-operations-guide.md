---
title: Útmutató az Azure IT-operátoraihoz | Microsoft dokumentumok
description: Útmutató az Azure IT-operátoraihoz
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 4f9da6cbfe8d1e6b92c39148b275de193730c8f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623580"
---
# <a name="get-started-for-azure-it-operators"></a>Első lépések az Azure IT-operátorai számára

Ez az útmutató a Microsoft Azure-infrastruktúra üzembe helyezésével és kezelésével kapcsolatos alapvető fogalmakat ismerteti. Ha most ismerkedik a felhőalapú számítástechnikával vagy magával az Azure-ral, ez az útmutató gyorsan hozzátud kezdeni a fogalmak, az üzembe helyezés és a felügyeleti részletek kezelésével. Az útmutató számos szakasza egy műveletet vitat meg, például egy virtuális gép üzembe helyezését, majd egy részletes technikai részleteket tartalmazó hivatkozást biztosít.

## <a name="cloud-computing-overview"></a>A felhőalapú számítógép-használat áttekintése

A felhőalapú számítástechnika modern alternatívát kínál a hagyományos helyszíni adatközponthoz képest. A nyilvános felhőszolgáltatók biztosítják és kezelik az összes számítástechnikai infrastruktúrát és az alapul szolgáló felügyeleti szoftvereket. Ezek a szállítók a felhőszolgáltatások széles skáláját biztosítják. A felhőszolgáltatás ebben az esetben lehet egy virtuális gép, egy webkiszolgáló vagy a felhőben üzemeltetett adatbázis-motor. Felhőszolgáltatóként szükség szerint bérelheti ezeket a felhőszolgáltatásokat. Ennek során a hardverkarbantartás tőkeköltségeit működési költséggé alakítja. A felhőszolgáltatás a következő előnyöket is biztosítja:

- Nagy számítási környezetek gyors üzembe helyezése

- A már nem szükséges rendszerek gyors elosztása

- A hagyományosan összetett rendszerek, például a terheléselosztók egyszerű üzembe helyezése

- Rugalmas számítási kapacitás vagy méretezés biztosítása szükség esetén

- Költséghatékonyabb számítástechnikai környezetek

- Bárhonnan hozzáférhet webalapú portállal vagy programautomatizálással

- Felhőalapú szolgáltatások a legtöbb számítási és alkalmazási igény kielégítésére

A helyszíni infrastruktúrával teljes mértékben szabályozhatja a telepített hardvert és szoftvert. Történelmileg ez olyan hardverbeszerzési döntésekhez vezetett, amelyek a felskálázásra összpontosítanak. Erre példa egy több maggal rendelkező kiszolgáló megvásárlása a csúcsteljesítmény-igények kielégítése érdekében. Sajnos előfordulhat, hogy ez az infrastruktúra kihasználatlan az igényi időszakon kívül. Az Azure-ral csak a szükséges infrastruktúrát helyezheti üzembe, és ezt bármikor módosíthatja. Ez azt eredményezi, hogy a hangsúly a horizontális felskálázás további számítási csomópontok üzembe helyezésével, hogy megfeleljen a teljesítmény igény. A felhőszolgáltatások skálázása költséghatékonyabb, mint a költséges hardvereken keresztüli skálázás.

A Microsoft számos Azure-adatközpontot telepített világszerte, több tervezettsel. Emellett a Microsoft növeli a szuverén felhőket olyan régiókban, mint Kína és Németország. Ilyen módon csak a legnagyobb globális vállalatok helyezhetnek üzembe adatközpontokat, így az Azure használatával bármilyen méretű vállalat számára megkönnyíti a szolgáltatások ügyfeleikhez közel történő üzembe helyezését.

A kisvállalkozások számára az Azure lehetővé teszi az alacsony költségű belépési pontot, amely lehetővé teszi a gyors méretezést a számítási igény növekedésével. Ez megakadályozza az infrastruktúrába történő nagy kezdeti tőkebefektetést, és szükség esetén rugalmasságot biztosít a rendszerek megtervezése és újratervezése számára. A felhőalapú számítástechnika használata jól illeszkedik az indítási növekedés méretarányos és hibamentes modelljéhez.

A rendelkezésre álló Azure-régiókról az [Azure-régiók című témakörben](https://azure.microsoft.com/regions/)talál további információt.

### <a name="cloud-computing-model"></a>Felhőalapú számítástechnikai modell

Az Azure egy felhőalapú számítástechnikai modellt használ az ügyfeleknek nyújtott szolgáltatáskategóriák alapján. A szolgáltatás három kategóriája az Infrastructure as a Service (IaaS), a Platform as a Service (PaaS) és a Software as a Service (SaaS). A szállítók az egyes kategóriákban osztoznak a számítási verem összetevőiért való felelősség egy részében vagy egészén. Vessünk egy pillantást a felhőalapú számítástechnika egyes kategóriáira.
![Felhőalapú számítástechnikai verem összehasonlítása](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktúra mint szolgáltatás

Az IaaS felhőszállítója futtatja és kezeli az összes fizikai számítási erőforrást és a számítógépes virtualizáció engedélyezéséhez szükséges szoftvert. A szolgáltatás egyik ügyfele virtuális gépeket telepít ezekben a tárolt adatközpontokban. Bár a virtuális gépek egy külső adatközpontban találhatók, az IaaS-fogyasztó szabályozhatja az operációs rendszer konfigurációját és felügyeletét, így az alapul szolgáló infrastruktúra a felhőszállítóra marad.

Az Azure számos IaaS-megoldást tartalmaz, beleértve a virtuális gépeket, a virtuálisgép-méretezési készleteket és a kapcsolódó hálózati infrastruktúrát. A virtuális gépek népszerű választás az azure-ba való kezdetben történő áttelepítéshez, mivel lehetővé teszi a "lift and shift" áttelepítési modellt. Konfigurálhatja a virtuális gép, mint az infrastruktúra jelenleg futó az adatközpontban, és majd telepítse át a szoftvert az új virtuális gép. Előfordulhat, hogy konfigurációs frissítéseket kell készítenie, például URL-címeket más szolgáltatásokhoz vagy tárolókhoz, de így sok alkalmazást áttelepíthet.

A virtuálisgép-méretezési csoportok az Azure virtuális gépekre épülnek, és egyszerű módot biztosítanak az azonos virtuális gépek fürtjeinek üzembe helyezésére. Virtuálisgép-méretezési csoportok is támogatja az automatikus skálázás, hogy az új virtuális gépek automatikusan telepíthető, ha szükséges. Ez leállítja a virtuális gép méretezési készletei ideális platformot a magasabb szintű mikroszolgáltatás-számítási fürtök, például az Azure Service Fabric és az Azure Container Service üzemeltetéséhez.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platform mint szolgáltatás

A PaaS segítségével üzembe helyezheti az alkalmazást egy olyan környezetben, amelyet a felhőszolgáltatás szállítója biztosít. A szállító végzi az infrastruktúra-kezelést, így ön az alkalmazásfejlesztésre és az adatkezelésre összpontosíthat.

Az Azure számos PaaS-számítási ajánlatot kínál, többek között az Azure App Service és az Azure Cloud Services (webes és feldolgozói szerepkörök) Web Apps funkcióját. Mindkét esetben a fejlesztők többféle módon is üzembe helyezhetik alkalmazásukat anélkül, hogy bármit is tudnának az azt támogató anyákról és csavarokról. A fejlesztőknek nem kell virtuális gépeket (VM-eket) létrehozniuk, a Távoli asztali protokoll (RDP) segítségével mindegyikbe be kell jelentkezniük, vagy telepíteniük kell az alkalmazást. Ők csak megüt egy gomb (vagy annak közelében), és a Microsoft által biztosított eszközök a virtuális gépek, majd telepítse és telepítse az alkalmazást rájuk.

#### <a name="saas-software-as-a-service"></a>SaaS: Szoftver mint szolgáltatás

SaaS olyan szoftver, amely központilag üzemeltetett és felügyelt. Általában egy több-bérlős architektúrán alapul – az alkalmazás egyetlen verziója minden ügyfél számára használatos. Több példányra is kinagyodhat, hogy a legjobb teljesítményt biztosítsa minden helyen. A SaaS-szoftverek licence általában havi vagy éves előfizetéssel történik. Az SaaS-szoftvergyártók felelősek a szoftververem minden összetevőjért, így csak a nyújtott szolgáltatásokat kezeli.

A Microsoft Office 365 jó példa a SaaS-ajánlatokra. Az előfizetők havi vagy éves előfizetési díjat fizetnek, és szolgáltatásként megkapják a Microsoft Exchange, a Microsoft OneDrive és a Microsoft Office programcsomag többi részét. Az előfizetők mindig a legfrissebb verziót kapják, és az Exchange-kiszolgálót kezelik. Az Office minden évben történő telepítéséhez és frissítéséhez képest ez olcsóbb, és kevesebb erőfeszítést igényel.

## <a name="azure-services"></a>Azure-szolgáltatások

Az Azure számos szolgáltatást kínál felhőalapú számítástechnikai platformján. Ezek a szolgáltatások a következőket tartalmazzák.

### <a name="compute-services"></a>Számítási szolgáltatások

Szolgáltatások az alkalmazások üzemeltetéséhez és futtatásához:

- Azure virtuális gépek – Linux és Windows

- Alkalmazásszolgáltatások (webalkalmazások, mobilalkalmazások, logikai alkalmazások, API-alkalmazások és függvényalkalmazások)

- Azure Batch (nagyméretű párhuzamos és kötegelt számítási feladatokhoz)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Adatszolgáltatások

Szolgáltatások az adatok tárolására és kezelésére:

- Azure Storage (tartalmazza az Azure Blob, Várólista, Tábla és Fájl szolgáltatások)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Alkalmazási szolgáltatások

Építési és üzemeltetési szolgáltatások:

- Azure Active Directory (Azure AD)

- Azure Service Bus az elosztott rendszerek csatlakoztatásához

- Azure HDInsight big data feldolgozásához

- Azure Logic Apps integrációs és vezénylési munkafolyamatokhoz

- Azure Media Services

### <a name="network-services"></a>Hálózati szolgáltatások

Szolgáltatások az Azure-on belüli, valamint az Azure és a helyszíni adatközpontok közötti hálózatépítéshez:

- Azure Virtual Network

- Azure ExpressRoute

- Az Azure által biztosított DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Az Azure-szolgáltatások részletes dokumentációját az [Azure-szolgáltatások dokumentációjában](https://docs.microsoft.com/azure)találja.

## <a name="azure-key-concepts"></a>Az Azure legfontosabb fogalmai

### <a name="datacenters-and-regions"></a>Adatközpontok és régiók

Az Azure egy globális felhőplatform, amely a világ számos régiójában általánosan elérhető. Amikor üzembe létesít egy szolgáltatást, alkalmazást vagy virtuális gép az Azure-ban, a rendszer kéri, hogy válasszon ki egy régiót. A kijelölt terület egy adott adatközpontot jelöl, ahol az alkalmazás fut. További információ: [Azure-régiók](https://azure.microsoft.com/regions/).

Az Azure használatának egyik előnye, hogy alkalmazásait világszerte különböző adatközpontokba telepítheti. A kiválasztott régió hatással lehet az alkalmazás teljesítményére. Optimális, ha olyan régiót választ, amely közelebb áll a legtöbb ügyfélhez, hogy csökkentse a hálózati kérelmek késését. Előfordulhat, hogy olyan régiót is kiválaszthat, amely megfelel az alkalmazás bizonyos országokban/régiókban való terjesztésére vonatkozó jogi követelményeknek.

### <a name="azure-portal"></a>Azure portál

Az Azure Portal egy webalapú alkalmazás, amely azure-erőforrások és -szolgáltatások létrehozására, kezelésére és eltávolítására használható. Az Azure Portal a [portal.azure.com](https://portal.azure.com)helyen található. Ez magában foglalja a testreszabható irányítópultot és az Azure-erőforrások kezeléséhez szükséges eszközök. Számlázási és előfizetési adatokat is biztosít. További információt a [Microsoft Azure Portal áttekintése](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) és az [Azure-erőforrások kezelése a portálon keresztül](https://docs.microsoft.com/azure/azure-portal/resource-group-portal)című témakörben talál.

### <a name="resources"></a>Források

Az Azure-erőforrások olyan egyedi számítási, hálózati, adat- vagy alkalmazásüzemeltetési szolgáltatások, amelyeket egy Azure-előfizetésbe telepítettek. Egyes gyakori erőforrások virtuális gépek, tárfiókok vagy SQL-adatbázisok. Az Azure-szolgáltatások gyakran több kapcsolódó Azure-erőforrásból állnak. Például egy Azure virtuális gép tartalmazhat egy virtuális gépet, tárfiókot, hálózati adaptert és nyilvános IP-címet. Ezek az erőforrások külön-külön vagy csoportként hozhatók létre, kezelhetők és törölhetők. Az Azure-erőforrások részletesebben ismertetik az útmutató későbbi részében.

### <a name="resource-groups"></a>Erőforráscsoportok

Az Azure-erőforráscsoport egy tároló, amely egy Azure-megoldás kapcsolódó erőforrásait tárolja. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azerőforrásokat, amelyeket csoportként szeretne kezelni. Az Azure-erőforráscsoportok részletesebben ismertetik az útmutató későbbi részében.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

Az Azure Resource Manager-sablon egy JavaScript-objektumjelölési (JSON) fájl, amely egy vagy több erőforrást határoz meg egy erőforráscsoportba való üzembe helyezéshez. Azt is meghatározza a függőségek között üzembe helyezett erőforrások. Az Erőforrás-kezelő sablonjait az útmutató későbbi részében részletesebben ismertetjük.

### <a name="automation"></a>Automation

Az erőforrások létrehozása, kezelése és törlése az Azure Portalhasználatával, automatizálhatja ezeket a tevékenységeket a PowerShell vagy az Azure parancssori felület (CLI) használatával.

#### <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell olyan modulok, amelyek az Azure kezeléséhez parancsmagokat biztosítanak. A parancsmagok segítségével azure-szolgáltatások létrehozása, kezelése és eltávolítása. A parancsmagok segítségével konzisztens, megismételhető és kéz-off központi telepítések eléréséhez. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/install-Az-ps) foglalkozó témakörben talál.

#### <a name="azure-command-line-interface"></a>Azure parancssori felületén

Az Azure parancssori felület egy olyan eszköz, amellyel azure-erőforrásokat hozhat létre, kezelhet és távolíthat el a parancssorból. Az Azure CLI Linux, Mac OS X és Windows rendszeren érhető el. További információ és technikai részletek az [Azure CLI telepítése](/cli/azure/install-azure-cli)című témakörben talál.

#### <a name="rest-apis"></a>REST API-k

Az Azure rest API-k készletére épül, amelyek támogatják az Azure portal felhasználói felületét. A REST API-k többsége is támogatott, hogy lehetővé tegye az Azure-erőforrások és -alkalmazások programozással történő kiépítését és kezelését bármely internetes eszközről. További információ: [az Azure REST SDK-referencia.](https://docs.microsoft.com/rest/api/index)

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

A rendszergazdák az Azure PowerShell és az Azure CLI eléréséhez az Azure Cloud Shell nevű böngészőhöz elérhető felületen keresztül férhetnek hozzá. Ez az interaktív felület egy rugalmas eszköz a Linux és a Windows rendszergazdák számára, hogy használja a parancssori felület a választás, vagy Bash vagy PowerShell. Az Azure Cloud Shell a portálon keresztül érhető el, shell.azure.com önálló webes felületként [vagy](https://shell.azure.com)számos más hozzáférési pontról. További információ: [Az Azure Cloud Shell áttekintése.](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="azure-subscriptions"></a>Azure-előfizetések

Az előfizetés az Azure-szolgáltatások logikai csoportosítása, amely egy Azure-fiókhoz kapcsolódik. Egyetlen Azure-fiók több előfizetést is tartalmazhat. Az Azure-szolgáltatások számlázása előfizetésenként történik. Az Azure-előfizetések rendelkeznek egy fiókrendszergazdával, aki teljes hozzáféréssel rendelkezik az előfizetés felett, és egy szolgáltatás-rendszergazda, aki az előfizetés ben lévő összes szolgáltatást felügyeli. A klasszikus előfizetés-rendszergazdákról az [Azure-előfizetés-rendszergazdák hozzáadása vagy módosítása](../../cost-management-billing/manage/add-change-subscription-administrator.md)című témakörben talál további információt. A rendszergazdák mellett az egyes fiókok is kaphatnak részletes irányítást az [Azure-erőforrások használatával szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Azure-előfizetés kiválasztása és engedélyezése

Az Azure-szolgáltatásokkal való munka előtt előfizetésre van szüksége. Számos előfizetési típus érhető el.

**Ingyenes fiókok**: Az ingyenes fiókregisztráláshoz szükséges hivatkozás az [Azure webhelyén](https://azure.microsoft.com/)található. Ez 30 nap alatt kreditet ad az Azure-beli erőforrások bármilyen kombinációjának kipróbálásához. Ha túllépi a hitelösszegét, fiókját felfüggesztjük. A próbaidőszak végén a szolgáltatások lelesznek szerelve, és a továbbiakban nem fognak működni. A felosztó-kiosztó előfizetésre bármikor frissíthet.

**MSDN-előfizetések:** Ha MSDN-előfizetéssel rendelkezik, havonta egy adott összeget kap az Azure-kreditben. Ha például egy Microsoft Visual Studio Enterprise-t HASZNÁL \$MSDN-előfizetéssel, havi 150 Azure-kreditet kap.

Ha túllépi a hitelösszeget, a szolgáltatás le lesz tiltva a következő hónap kezdetéig. Kikapcsolhatja a költségkeretet, és hozzáadhat egy hitelkártyát, amelyet a további költségekhez használhat. E költségek egy része az MSDN-fiókok esetében diszkontálva van. Például a Windows Server rendszert futtató virtuális gépek Linux-árát kell fizetnie, és a Microsoft kiszolgálóiért, például a Microsoft SQL Serverért nem kell külön díjat fizetni. Ez ideálissá teszi az MSDN-fiókokat fejlesztési és tesztelési forgatókönyvekhez.

**BizSpark-fiókok**: A Microsoft BizSpark program számos előnnyel jár az induló vállalkozások számára. Az egyik ilyen előny az összes Microsoft-szoftverhez való hozzáférés fejlesztési és tesztelési környezetekhez legfeljebb öt MSDN-fiók hoz. $150-t kap az Azure-kreditben az öt MSDN-fiók mindegyikéhez, és kedvezményes díjakat fizet számos Azure-szolgáltatásért, például a virtuális gépekért.

**Használatalapú fizetés:** Ezzel az előfizetéssel a hitelkártyát vagy bankkártyát a fiókhoz csatolva kell kifizetnie. Ha Ön szervezet, akkor számlázásra is jóváhagyható.

**Vállalati szerződések**: Nagyvállalati szerződéssel kötelezettséget vállal arra, hogy a következő évben bizonyos számú szolgáltatást használ az Azure-ban, és ezt az összeget előre kifizeti. Az ön által vállalt kötelezettség egész évben felhasználásra kerül. Ha túllépi a kötelezettségvállalás összegét, akkor a hátralékot is kifizetheti. A kötelezettségvállalás összegétől függően kedvezményt kap az Azure-beli szolgáltatásokból.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Rendszergazdai hozzáférés megadása Azure-előfizetéshez

Az RBAC számos beépített szerepkörrel rendelkezik, amelyek segítségével engedélyeket rendelhet hozzá. Ha egy felhasználót egy Azure-előfizetés rendszergazdájának szeretne tenni, rendelje hozzá a [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkört az előfizetéshatókörben. A Tulajdonos szerepkör teljes hozzáférést biztosít a felhasználónak az előfizetés összes erőforrásához, beleértve a hozzáférés mások számára való delegálásának jogát is.

További információkért lásd [a hozzáférés az RBAC és az Azure Portal használatával történő kezelését](../../role-based-access-control/role-assignments-portal.md) ismertető cikket.

### <a name="view-billing-information-in-the-azure-portal"></a>Számlázási adatok megtekintése az Azure Portalon

Az Azure használatának fontos eleme a számlázási adatok megtekintése. Az Azure portal részletes betekintést nyújt az Azure számlázási információiba.

További információ: [Az Azure számlázási számlájának és a napi használati adatoknak a letöltése.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

### <a name="get-billing-information-from-billing-apis"></a>Számlázási adatok beszerezése számlázási API-kból

A számlázás portálon való megtekintése mellett parancsfájl vagy program használatával is elérheti a számlázási adatokat az Azure Billing REST API-kon keresztül:

- Az Azure Usage API segítségével lekérheti a használati adatokat. A számlázási használati adatok finomhangolásával a kapcsolódó Azure-erőforrások. Megcímkézheti például egy erőforráscsoport minden erőforrását egy részleg névvel vagy projektnévvel, majd nyomon követheti a költségeket kifejezetten az adott címkéhez.

- Az Azure Rate Card API segítségével felsorolhatja az összes rendelkezésre álló erőforrást, valamint az egyes erőforrások metaadatait és díjszabási adatait.

További információkért tekintse meg az [Információk a Microsoft Azure-erőforrások igénybevételéről](../../cost-management-billing/manage/usage-rate-card-overview.md) szakaszt.

### <a name="forecast-cost-with-the-pricing-calculator"></a>Költség előrejelzése az árképzési kalkulátorral

Az Azure-beli szolgáltatások díjszabása eltérő. Számos Azure-szolgáltatás alapszintű, standard és prémium szintű szolgáltatásokat nyújt. Általában minden szint több ár- és teljesítményszinttel rendelkezik. Az [online díjkalkulátor](https://azure.microsoft.com/pricing/calculator)segítségével árképzési becsléseket hozhat létre. A kalkulátor rugalmasságot biztosít egyetlen erőforrás vagy erőforráscsoport költségének becsléséhez.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Az Azure Resource Manager egy üzembe helyezési, felügyeleti és szervezeti mechanizmus az Azure-erőforrásokhoz. Az Erőforrás-kezelő használatával számos erőforrást helyezhet össze egy erőforráscsoportban.

Az Erőforrás-kezelő olyan telepítési lehetőségeket is tartalmaz, amelyek lehetővé teszik a kapcsolódó erőforrások testre szabható telepítését és konfigurálását. Például az Erőforrás-kezelő használatával telepíthet egy alkalmazást, amely több virtuális gépből, egy terheléselosztóból és egy SQL-adatbázisból áll egyetlen egységként. Ezeket a központi telepítéseket egy Erőforrás-kezelő sablon használatával fejlesztheti.

A Resource Manager számos előnyt kínál:

- A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

- A megoldás a fejlesztési életciklus teljes időtartama alatt többször is üzembe helyezhető, és biztos lehet abban, hogy az erőforrások konzisztens állapotban vannak üzembe helyezve.

- Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

- Meghatározhatja az erőforrások közti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

- Az erőforráscsoport összes szolgáltatására alkalmazhat hozzáférés-vezérlést, mivel az RBAC natív módon integrálva van a felügyeleti platformba.

- Címkéket alkalmazhat az erőforrásokra, hogy logikusan rendszerezhesse az előfizetésösszes erőforrását.

- A szervezet számlázását az azonos címkével megegyező erőforráscsoport költségeinek megtekintésével tisztázhatja.

### <a name="tips-for-creating-resource-groups"></a>Tippek erőforráscsoportok létrehozásához

Amikor az erőforráscsoportokkal kapcsolatos döntéseket hoz, vegye figyelembe az alábbi tippeket:

- Az erőforráscsoport összes erőforrásának azonos életciklussal kell rendelkeznie.

- Az erőforrásokat egyszerre csak egy csoporthoz rendelheti hozzá.

- Erőforrást bármikor hozzáadhat vagy eltávolíthat egy erőforráscsoportból. Minden erőforrásnak egy erőforráscsoporthoz kell tartoznia. Ha tehát eltávolít egy erőforrást az egyik csoportból, hozzá kell adnia egy másikhoz.

- A legtöbb típusú erőforrást bármikor áthelyezheti egy másik erőforráscsoportba.

- Az erőforráscsoport erőforrásai különböző régiókban lehetnek.

- Az erőforráscsoport segítségével szabályozhatja a benne lévő erőforrások hoz való hozzáférést.

### <a name="building-resource-manager-templates"></a>Erőforrás-kezelő sablonok létrehozása

Az Erőforrás-kezelő sablonjai deklaratív módon határozzák meg azokat az erőforrásokat és erőforrás-konfigurációkat, amelyek egyetlen erőforráscsoportba lesznek telepítve. Az Erőforrás-kezelő sablonjaival összetett központi telepítéseket vezényelhet anélkül, hogy felesleges parancsfájlokat vagy manuális konfigurációt kellene alkalmaznia. A sablon kifejlesztése után többször is üzembe helyezheti – minden alkalommal azonos eredménnyel.

Az Erőforrás-kezelő sablon négy szakaszból áll:

- **Paraméterek**: Ezek a központi telepítés bemenetei. A paraméterértékeket emberi vagy automatizált folyamat tal is megadhatók. Példa paraméter lehet egy rendszergazdai felhasználónév és jelszó egy Windows virtuális gép. A paraméterértékek a központi telepítés során, ha meg vannak adva.

- **Változók**: Ezek a központi telepítés során használt értékek tárolására szolgálnak. A paraméterekkel ellentétben a rendszer nem ad meg változó értéket a telepítés időpontjában. Ehelyett ez kemény kódolt vagy dinamikusan generált.

- **Erőforrások**: A sablon ezen szakasza határozza meg a telepítendő erőforrásokat, például a virtuális gépeket, a tárfiókokat és a virtuális hálózatokat.

- **Kimenet**: A központi telepítés befejezése után az Erőforrás-kezelő adatokat, például dinamikusan generált kapcsolati karakterláncokat adhat vissza.

A következő mechanizmusok érhetők el a telepítés automatizálásához:

- **Függvények**: Az Erőforrás-kezelő sablonokban több függvényt is használhat. Ezek közé tartoznak például a karakterlánc kisbetűssé alakítása, egy meghatározott erőforrás több példányának üzembe helyezése és a célerőforrás-csoport dinamikus visszaadása. Az Erőforrás-kezelő funkciói segítenek dinamikus központi telepítések létrehozásában.

- **Erőforrás-függőségek:** Ha több erőforrást telepít, egyes erőforrások függnek másoktól. A telepítés megkönnyítése érdekében függőségi deklarációt használhat, hogy a függő erőforrások a többi előtt vessüljenek üzembe.

- **Sabloncsatolás**: Az egyik Erőforrás-kezelő sablonból egy másik sablonra mutató hivatkozással rendelkezhet. Ez lehetővé teszi a központi telepítés felbontását egy sor célzott, cél-specifikus sablonok.

Erőforrás-kezelő sablonokat bármely szövegszerkesztőben létrehozhat. Az Azure SDK for Visual Studio azonban olyan eszközöket is tartalmaz, amelyek segítséget nyújtanak. A Visual Studio segítségével erőforrásokat adhat a sablonhoz egy varázslón keresztül, majd közvetlenül a Visual Studióból telepítheti és debugolhatja a sablont. További információ: [Authoring Azure Resource Manager templates](../../resource-group-authoring-templates.md).

Végül a meglévő erőforráscsoportokat újrafelhasználható sablonná alakíthatja az Azure Portalról. Ez akkor lehet hasznos, ha egy meglévő erőforráscsoport telepíthető sablonját szeretné létrehozni, vagy csak meg szeretné vizsgálni az alapul szolgáló JSON-t. Erőforráscsoport exportálásához válassza az **Automatizálási parancsfájl** gombot az erőforráscsoport beállításai között.

## <a name="security-of-azure-resources-rbac"></a>Az Azure-erőforrások biztonsága (RBAC)

Működési hozzáférést adhat a felhasználói fiókokhoz egy megadott hatókörön: előfizetés, erőforráscsoport vagy egyedi erőforrás. Ez azt jelenti, hogy erőforrások egy készletét telepítheti egy erőforráscsoportba, például egy virtuális gépbe és az összes kapcsolódó erőforrásba, és engedélyeket adhat egy adott felhasználónak vagy csoportnak. Ez a megközelítés csak a célerőforrás-csoporthoz tartozó erőforrásokra korlátozza a hozzáférést. Egyetlen erőforráshoz, például virtuális géphez vagy virtuális hálózathoz is hozzáférést adhat.

A hozzáférés engedélyezéséhez szerepkört kell rendelnie a felhasználóhoz vagy a felhasználói csoporthoz. Sok előre definiált szerepkör van. Saját egyéni szerepköröket is definiálhat.

Íme néhány példa [az Azure beépített szerepköreire:](../../role-based-access-control/built-in-roles.md)

- **Tulajdonos:** Az ezzel a szerepkörrel rendelkező felhasználók mindent kezelhetnek, beleértve a hozzáférést is.

- **Olvasó:** Az ilyen szerepkörrel rendelkező felhasználók minden típusú erőforrást olvashatnak (kivéve a titkos kulcsokat), de nem módosíthatják.

- **Virtuálisgép-közreműködő:** Az ezzel a szerepkörrel rendelkező felhasználó kezelheti a virtuális gépeket, de nem tudja kezelni azt a virtuális hálózatot, amelyhez csatlakoznak, vagy azt a tárfiókot, amelyhez a Virtuális merevlemez-fájl található.

- **SQL DB közreműködő:** Az ilyen szerepkörrel rendelkező felhasználók kezelhetik az SQL-adatbázisokat, de a biztonsággal kapcsolatos házirendjeiket nem.

- **SQL Security Manager**: Az ilyen szerepkörrel rendelkező felhasználók kezelhetik az SQL-kiszolgálók és -adatbázisok biztonsággal kapcsolatos házirendjeit.

- **Tárfiók közreműködője:** Egy ilyen szerepkörrel rendelkező felhasználó kezelheti a tárfiókokat, de nem tudja kezelni a tárfiókokhoz való hozzáférést.

További információkért lásd [a hozzáférés az RBAC és az Azure Portal használatával történő kezelését](../../role-based-access-control/role-assignments-portal.md) ismertető cikket.

## <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

Az Azure virtuális gépek az Azure egyik központi IaaS-szolgáltatása. Az Azure virtuális gépek támogatják a Windows vagy Linux alapú virtuális gépek telepítését egy Microsoft Azure adatközpontban. Az Azure virtuális gépek, ön teljes mértékben szabályozhatja a virtuális gép konfigurációját, és felelős az összes szoftver telepítése, konfigurációja és karbantartása.

Azure-beli virtuális gép telepítésekor kiválaszthat egy lemezképet az Azure Piactérről, vagy saját általános ítást is biztosíthat. Ez a lemezkép az operációs rendszer és a kezdeti konfiguráció alkalmazására szolgál. A telepítés során az Erőforrás-kezelő bizonyos konfigurációs beállításokat kezel, például a számítógép nevét, a rendszergazdai hitelesítő adatokat és a hálózati konfigurációt. Az Azure virtuálisgép-bővítmények segítségével tovább automatizálhatja a konfigurációkat, például a szoftvertelepítést, a víruskereső konfigurációt és a figyelési megoldásokat.

Virtuális gépeket számos különböző méretben hozhat létre. A virtuális gép mérete erőforrás-lefoglalást, például feldolgozást, memóriát és tárolókapacitást diktál. Bizonyos esetekben bizonyos szolgáltatások, például rdma-kompatibilis hálózati adapterek és SSD-lemezek csak bizonyos virtuálisgép-méretekkel érhetők el. A virtuális gépek méretének és képességeinek teljes listáját a Windows és [Linux](../../virtual-machines/windows/sizes.md) "Virtuális gépek méreteaz Azure-ban" című témakörben [tartalmazza.](../../virtual-machines/linux/sizes.md)

### <a name="use-cases"></a>Használati esetek

Mivel az Azure virtuális gépei teljes körű vezérlést biztosítanak a konfiguráció felett, ideálisak a PaaS-modellbe nem illeszkedő kiszolgálói munkaterhelések széles köréhez. A Microsoft Azure platformon kiszolgálói , például adatbázis-kiszolgálók (SQL Server, Oracle vagy MongoDB), Windows Server Active Directory, Microsoft SharePoint és még sok más kiszolgálói is futtathatóvá válnak. Ha szükséges, az ilyen számítási feladatok at egy helyszíni adatközpontból egy vagy több Azure-régióba helyezheti át, nagy mennyiségű újrakonfigurálás nélkül.

### <a name="deployment-of-virtual-machines"></a>Virtuális gépek telepítése

Az Azure virtuális gépek üzembe helyezhetők az Azure Portalhasználatával, az Azure PowerShell-modul automatizálásával vagy a platformfüggetlen CLI automatizálásával.

#### <a name="portal"></a>Portál

Virtuális gép üzembe helyezése az Azure Portalhasználatával csak egy aktív Azure-előfizetésés egy webböngésző eléréséhez. Különböző konfigurációkkal számos különböző operációsrendszer-lemezképet választhat. A központi telepítés során minden tárolási és hálózati követelmény konfigurálva van. További információ: "Virtuális gép létrehozása az Azure Portalon" [For Windows](../../virtual-machines/windows/quick-create-portal.md) és [Linux](../../virtual-machines/linux/quick-create-portal.md)rendszeren.

A virtuális gép üzembe helyezése mellett az Azure Portalon üzembe helyezhetegy Azure Resource Manager-sablont a portálról. Ez telepíti és konfigurálja az összes erőforrást a sablonban meghatározottmódon. További információ: [Erőforrások telepítése erőforrás-kezelői sablonokkal és az Azure Portalon.](../../azure-resource-manager/templates/deploy-portal.md)

#### <a name="powershell"></a>PowerShell

Az Azure virtuális gépek üzembe helyezése a PowerShell használatával lehetővé teszi az összes kapcsolódó virtuálisgép-erőforrás teljes üzembe helyezését, beleértve a tárolást és a hálózatépítést is. További információt a [Windows virtuális gép létrehozása az Erőforrás-kezelő és a PowerShell használatával című témakörben talál.](../../virtual-machines/windows/quick-create-powershell.md)

Az Azure számítási erőforrásainak külön-külön történő üzembe helyezése mellett az Azure PowerShell-modul segítségével üzembe helyezhet egy Azure Resource Manager-sablont is. További információ: [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure PowerShell használatával](../../azure-resource-manager/templates/deploy-powershell.md)című témakörben olvashat.

#### <a name="command-line-interface-cli"></a>Parancssori felület (CLI)

A PowerShell-modulhoz, az Azure parancssori felülete is biztosítja a központi telepítés automatizálását, és Windows, OS X vagy Linux rendszereken is használható. Az Azure CLI **vm gyorslétrehozási** parancs használatakor az összes kapcsolódó virtuálisgép-erőforrás (beleértve a tárolást és a hálózatot is) és maga a virtuális gép üzembe helyezése. További információ: [Linuxos virtuális gép létrehozása az Azure-ban a CLI használatával.](../../virtual-machines/linux/quick-create-cli.md)

Hasonlóképpen használhatja az Azure CLI-t egy Azure Resource Manager-sablon üzembe helyezéséhez. További információ: [Erőforrások telepítése erőforrás-kezelői sablonokkal és Az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)című témakörben olvashat.

### <a name="access-and-security-for-virtual-machines"></a>Hozzáférés és biztonság a virtuális gépekhez

A virtuális gép internetről történő eléréséhez a társított hálózati adaptert vagy adott esetben a terheléselosztót nyilvános IP-címmel kell konfigurálni. A nyilvános IP-cím tartalmaz egy DNS-nevet, amely feloldódik a virtuális gép vagy a terheléselosztó. További információt az [Azure-beli IP-címek című témakörben talál.](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)

A virtuális géphez való hozzáférést a nyilvános IP-címen keresztül kezelheti egy hálózati biztonsági csoport (NSG) erőforrás használatával. Az NSG tűzfalként működik, és engedélyezi vagy megtagadja a forgalmat a hálózati adapteren vagy alhálózaton egy meghatározott porton. Például egy távoli asztali munkamenet létrehozásához egy Azure virtuális gép, konfigurálnia kell az NSG,hogy a bejövő forgalom a 3389-es porton. További információ: [Portok megnyitása virtuális géphez az Azure-ban az Azure Portalon.](../../virtual-machines/windows/nsg-quickstart-portal.md)

Végül, mint bármely számítógépes rendszer felügyeletét, biztonsági hitelesítő adatok és szoftveres tűzfalak használatával kell biztosítania az Azure virtuális gép az operációs rendszer biztonságát.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage egy Microsoft által felügyelt szolgáltatás, amely tartós, méretezhető és redundáns tárolást biztosít. Az Azure storage-fiók erőforrásként bármely erőforráscsoport bármely erőforrás-telepítési módszer használatával adhat hozzá. Az Azure négy tárolási típust tartalmaz: Blob storage, File Storage, Table Storage és Queue storage. Egy tárfiók üzembe helyezésekor két fióktípus érhető el, általános célú és blob storage. Az általános célú tárfiók mind a négy tárolási típushoz hozzáférést biztosít. A Blob storage-fiókok hasonlóak az általános célú fiókokhoz, de speciális blobokat tartalmaznak, amelyek gyakori és ritka elérésű hozzáférési szinteket tartalmaznak. A blobstorage-ról az [Azure Blob storage című témakörben](../../storage/blobs/storage-blob-storage-tiers.md)talál további információt.

Az Azure storage-fiókok különböző redundanciaszintekkel konfigurálhatók:

- **A helyileg redundáns tárolás** magas rendelkezésre állást biztosít annak biztosításával, hogy az összes adat három példánya szinkron módon készül, mielőtt az írás sikeresnek minősülne. Ezeket a másolatokat egyetlen létesítményben, egyetlen régióban tárolják. A replikák külön tartalék tartományokban és frissítési tartományokban vannak. Ez azt jelenti, hogy az adatok akkor is elérhetők, ha egy tárolócsomópont, amely az adatokat tartja, meghibásodik, vagy offline állapotba kerül a frissítéshez.

- **Georedundáns tárolás** három szinkron másolatot készít az adatokról az elsődleges régióban a magas rendelkezésre állás érdekében, majd aszinkron módon három replikát készít egy párosított régióban a vész-helyreállításhoz.

- **Az olvasási hozzáférésű georedundáns tárolás** georedundáns tárolás, valamint a másodlagos régióban lévő adatok olvasása. Ez a képesség teszi alkalmassá részleges vészhelyreállításra. Ha probléma van az elsődleges régióval, módosíthatja az alkalmazást, hogy csak olvasható hozzáféréssel rendelkezzon a párosított régióhoz.

### <a name="use-cases"></a>Használati esetek

Minden tárolótípusnak más-más használati esete van.

#### <a name="blob-storage"></a>Blob Storage

A *word blob* a *bináris nagy objektum*rövidítése. A blobok a számítógépen tárolthoz hasonló strukturálatlan fájlok. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható. Az Azure Blob storage az Azure Virtual Machines adatlemezeket is tartalmazza.

Az Azure Storage háromféle blobot támogat:

- **A blokkblobok** legfeljebb 195 GB méretű (4 MB × 50 000 blokk) tárolására szolgálnak. A blokkblobok elsődleges felhasználási módja olyan fájlok tárolása, amelyeket az elejüktől a végükig olvasnak, ilyenek például a médiafájlok vagy a képfájlok webhelyekhez. Ezek neve blokk blobok, mert a fájlok nagyobb, mint 64 MB-os kell feltölteni a kis blokkokat. Ezek a blokkok ezután konszolidált (vagy véglegesített) a végső blobba.

- **A lapblobok** legfeljebb 1 TB méretű véletlen elérésű fájlok tárolására szolgálnak. A lapblobok elsősorban a virtuális merevlemezek háttértárolójaként használatosak, amelyek tartós lemezeket biztosítanak az Azure virtuális gépekhez, az Azure-beli IaaS számítási szolgáltatáshoz. Azért hívják őket lapbloboknak, mert 512 bájtos oldalakhoz biztosítanak véletlenszerű olvasási/írási hozzáférést.

- **Hozzáfűző blobok** blokkok, például blokk blobok, de hozzáfűző műveletek vannak optimalizálva. Ezek gyakran használják az adatok naplózására egy vagy több forrásból ugyanabba a blobba. Például előfordulhat, hogy írja az összes nyomkövetési naplózás ugyanabba a hozzáfűző blob egy alkalmazás, amely több virtuális gépen futó. Egy hozzáfűző blob akár 195 GB méretű is lehet.

További információ: [Az Azure Blob storage használatának első lépései a .NET használatával](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)című témakörben talál.

#### <a name="file-storage"></a>File Storage

Az Azure File storage egy olyan szolgáltatás, amely a szabványos Server Message Block (SMB) protokoll használatával kínál fájlmegosztásokat a felhőben. A szolgáltatás támogatja mind az SMB 2.1, mind az SMB 3.0 szolgáltatást. Az Azure File storage segítségével gyorsan és költséges újraírásnélkül telepítheti át a fájlmegosztásokra támaszkodó alkalmazásokat az Azure-ba. Az Azure virtuális gépeken, a felhőszolgáltatásokban vagy a helyszíni ügyfeleken futó alkalmazások fájlmegosztást csatlakoztathatnak a felhőben. Ez hasonló ahhoz, ahogyan egy asztali alkalmazás egy tipikus SMB-megosztást csatlakoztat. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Mivel a fájltárolási megosztás egy szabványos SMB-fájlmegosztás, az Azure-ban futó alkalmazások fájlrendszeri API-kon keresztül férhetnek hozzá a megosztásban lévő adatokhoz. A fejlesztők ezért meglévő kódjuk és szakértelmük segítségével áttelepíthetik a meglévő alkalmazásokat. Az informatikai szakemberek a PowerShell-parancsmagokkal hozhatnak létre, csatlakoztathatnak és kezelhetnek fájltárolási megosztásokat az Azure-alkalmazások felügyeletének részeként.

További információ: [Az Azure File storage használatának megkezdése Windows rendszeren](../../storage/files/storage-how-to-use-files-windows.md) vagy Az Azure File storage használata Linux on című [témakörben](../../storage/files/storage-how-to-use-files-linux.md)talál.

#### <a name="table-storage"></a>Table Storage

Az Azure Table Storage egy olyan szolgáltatás, amely strukturált NoSQL-adatokat tárol a felhőben. A táblatárolás egy séma nélküli kialakítású kulcs-/attribútumtároló. Mivel a Table storage séma nélküli, az alkalmazás igényeinek fejlődésével egyszerűen adaptálhatja az adatokat. Az adatok hozzáférése gyors és költséghatékony, bármilyen alkalmazásról legyen is szó. Hasonló adatmennyiséggel számolva a Table Storage általában határozottan kevesebb költséggel jár, mint egy hagyományos SQL.

A Table Storage segítségével rugalmas adatkészleteket tárolhat, például webalkalmazások felhasználói adatait, címtárakat, eszközadatokat és bármilyen egyéb metaadatot, amelyre a szolgáltatásnak szüksége van. A táblában tetszőleges számú entitás tárolható. A tárfiók tetszőleges számú táblát tartalmazhat, a tárfiók kapacitáskorlátjáig.

További információ: [Az Azure Table storage – Első lépések.](../../cosmos-db/table-storage-how-to-use-dotnet.md)

#### <a name="queue-storage"></a>Queue Storage

Az Azure Queue Storage felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezési alkalmazások tervezésekor az alkalmazás-összetevőket gyakran leválasztják egymástól, hogy egymástól függetlenül méretezhessenek. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

További információ: [Az Azure Queue storage – Első lépések.](../../storage/queues/storage-dotnet-how-to-use-queues.md)

### <a name="deploying-a-storage-account"></a>Tárfiók telepítése

A tárfiók üzembe helyezésére számos lehetőség közül választhat.

#### <a name="portal"></a>Portál

A tárfiók üzembe helyezése az Azure Portalhasználatával csak egy aktív Azure-előfizetést és egy webböngészőhöz való hozzáférést igényel. Új tárfiókot telepíthet egy új vagy meglévő erőforráscsoportba. Miután létrehozta a tárfiókot, létrehozhat egy blob tárolót vagy fájlmegosztást a portál használatával. Programozott módon hozhat létre tábla- és várólista-tárolási entitásokat. További információ: [Tárfiók létrehozása](../../storage/common/storage-account-create.md).

Az Azure Portalon lévő tárfiók üzembe helyezése mellett üzembe helyezhet egy Azure Resource Manager-sablont a portálról is. Ez üzembe helyezi és konfigurálja a sablonban meghatározott összes erőforrást, beleértve a tárfiókokat is. További információ: [Erőforrások telepítése erőforrás-kezelői sablonokkal és az Azure Portalon.](../../azure-resource-manager/templates/deploy-portal.md)

#### <a name="powershell"></a>PowerShell

Az Azure storage-fiók üzembe helyezése a PowerShell használatával lehetővé teszi a tárfiók teljes üzembe helyezését. További információ: [Az Azure PowerShell használata az Azure Storage szolgáltatással](../../storage/common/storage-powershell-guide-full.md)című témakörben talál.

Az Azure-erőforrások külön-külön történő üzembe helyezése mellett az Azure PowerShell-modul segítségével üzembe helyezhet egy Azure Resource Manager-sablont is. További információ: [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure PowerShell használatával](../../azure-resource-manager/templates/deploy-powershell.md)című témakörben olvashat.

#### <a name="command-line-interface-cli"></a>Parancssori felület (CLI)

A PowerShell-modulhoz, az Azure parancssori felülete is biztosítja a telepítés automatizálását, és Windows, OS X vagy Linux rendszereken is használható. Az Azure CLI **storage-fiók létrehozása** parancs használatával létrehozhat egy tárfiókot. További információ: [Az Azure CLI használata az Azure Storage-szal.](../../storage/common/storage-azure-cli.md)

Hasonlóképpen használhatja az Azure CLI-t egy Azure Resource Manager-sablon üzembe helyezéséhez. További információ: [Erőforrások telepítése erőforrás-kezelői sablonokkal és Az Azure CLI-vel](../../resource-group-template-deploy-cli.md)című témakörben olvashat.

### <a name="access-and-security-for-azure-storage"></a>Hozzáférés és biztonság az Azure Storage-hoz

Az Azure Storage különböző módokon érhető el, többek között az Azure Portalon keresztül, a virtuális gép létrehozása és üzemeltetése során, valamint a Storage-ügyfélkódtárak.

#### <a name="virtual-machine-disks"></a>Virtuálisgép-lemezek

Virtuális gép telepítésekor is létre kell hoznia egy tárfiókot a virtuális gép operációs rendszer lemezének és minden további adatlemeznek a tárolásához. Kiválaszthat egy meglévő tárfiókot, vagy létrehozhat egy újat. Mivel egy blob maximális mérete 1024 GB, egyetlen virtuális gép lemezének maximális mérete 1023 GB. Nagyobb adatlemez konfigurálásához több adatlemezt is bemutathat a virtuális gépnek, és egyetlen logikai lemezként egyesítheti őket. További információt a Windows [és](../../virtual-machines/windows/tutorial-manage-data-disk.md) Linux "Azure-lemezek kezelése" című témakörben [talál.](../../virtual-machines/linux/tutorial-manage-disks.md)

#### <a name="storage-tools"></a>Tárolóeszközök

Az Azure storage-fiókok számos különböző tárolókezelőn, például a Visual Studio Cloud Exploreren keresztül érhetők el. Ezekkel az eszközökkel böngészhet a tárfiókok és az adatok között. További információt és az elérhető tárkezelők listáját az [Azure Storage ügyféleszközei című](../../storage/common/storage-explorers.md)témakörben talál.

#### <a name="storage-api"></a>Tárolási API

A tárolási erőforrások bármely olyan nyelven elérhetők, amely HTTP/HTTPS-kérelmeket tud letenni. Ezenfelül az Azure Storage számos népszerű nyelvhez biztosít programozási kódtárakat. Ezek a kódtárak leegyszerűsítik az Azure Storage-szal való munkát olyan részletek kezelésével, mint a szinkron és aszinkron meghívás, a műveletek kötegelése, a kivételkezelés és az automatikus újrapróbálkozások. További információ: [Azure Storage service REST API reference.](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference)

#### <a name="storage-access-keys"></a>Tárolási hozzáférési kulcsok

Minden tárfiók két hitelesítési kulcs, egy elsődleges és egy másodlagos rendelkezik. Bármelyik használható tárolási hozzáférési műveletekhez. Ezek a tárolókulcsok a tárfiók biztonságossá tétele érdekében használatosak, és az adatok programozott eléréséhez szükségesek. A biztonság növelése érdekében két kulcs teszi lehetővé a kulcsok alkalmi átütemezését. Fontos, hogy a kulcsok biztonságos, mert a birtoklása, valamint a fiók neve, lehetővé teszi a korlátlan hozzáférést biztosít a tárfiókban lévő adatokhoz.

#### <a name="shared-access-signatures"></a>Megosztott hozzáférésű aláírások

Ha engedélyeznie kell a felhasználóknak, hogy szabályozott hozzáféréssel rendelkezhessenek a tárolási erőforrásokhoz, létrehozhat egy közös hozzáférési aláírást. A megosztott hozzáférésű aláírás olyan jogkivonat, amely egy olyan URL-címhez csatolható, amely lehetővé teszi a tárolási erőforrások delegált elérését. Bárki, aki rendelkezik a jogkivonattal, hozzáférhet az erőforráshoz, amelyre rámutat, az általa megadott engedélyekkel, az érvényesség idáig. További információt a Megosztott hozzáférésű aláírások használata című [témakörben talál.](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuális hálózatok szükségesek a virtuális gépek közötti kommunikáció támogatásához. Megadhatja az alhálózatokat, az egyéni IP-címet, a DNS-beállításokat, a biztonsági szűrést és a terheléselosztást. Az Azure támogatja a különböző használati esetek: csak felhőalapú hálózatok vagy hibrid virtuális hálózatok.

### <a name="cloud-only-virtual-networks"></a>Csak felhőalapú virtuális hálózatok

Az Azure virtuális hálózat alapértelmezés szerint csak az Azure-ban tárolt erőforrások számára érhető el. Az ugyanahhoz a virtuális hálózathoz kapcsolódó erőforrások kommunikálhatnak egymással. A virtuálisgépek hálózati csatolóit és terheléselosztóit nyilvános IP-címmel társíthatja, hogy a virtuális gép elérhető legyen az interneten keresztül. Hálózati biztonsági csoport használatával biztonságos hozzáférést biztosíthat a nyilvánosan elérhető erőforrásokhoz.

![Azure virtuális hálózat kétrétegű webalkalmazáshoz](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hibrid virtuális hálózatok

A helyszíni hálózatot az ExpressRoute vagy a helyek közötti VPN-kapcsolat használatával csatlakoztathatja egy Azure virtuális hálózathoz. Ebben a konfigurációban az Azure virtuális hálózat lényegében a helyszíni hálózat felhőalapú kiterjesztése.

Mivel az Azure virtuális hálózat csatlakozik a helyszíni hálózathoz, a létesítmények közötti virtuális hálózatoknak a szervezet által használt címterület egy egyedi részét kell használniuk. Ugyanúgy, ahogy a különböző vállalati helyek egy adott IP-alhálózathoz vannak rendelve, az Azure a hálózat bővítésekor egy másik hely lesz.
A virtuális hálózat üzembe helyezésének számos lehetősége van.

- [Portál](../..//virtual-network/quick-create-portal.md)

- [Powershell](../../virtual-network/quick-create-powershell.md)

- [Parancssori illesztő (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager-sablonok

> **Mikor kell használni:** Bármikor dolgozik virtuális gépekkel az Azure-ban, virtuális hálózatokkal fog dolgozni. Ez lehetővé teszi a virtuális gépek felosztását nyilvános és privát alhálózatokhasonló helyszíni adatközpontok.
>
> **Első lépések:** Egy Azure virtuális hálózat üzembe helyezése az Azure Portal használatával csak egy aktív Azure-előfizetést és egy webböngészőhöz való hozzáférést igényel. Új virtuális hálózatot telepíthet egy új vagy meglévő erőforráscsoportba. Amikor új virtuális gépet hoz létre a portálról, kiválaszthat egy meglévő virtuális hálózatot, vagy létrehozhat egy újat. Első lépések és [virtuális hálózat létrehozása az Azure Portal használatával.](../../virtual-network/quick-create-portal.md)

### <a name="access-and-security-for-virtual-networks"></a>Hozzáférés és biztonság a virtuális hálózatokhoz

Az Azure virtuális hálózatok biztonságossá tétele egy hálózati biztonsági csoport használatával. Az NSG-k tartalmazzák a hozzáférés-vezérlési lista (ACL) szabályok listáját, amelyek engedélyezik vagy megtagadják a virtuális hálózat virtuális géppéldányainak hálózati forgalmát. Az NSG-k alhálózatokkal vagy az adott alhálózaton belüli egyedi virtuálisgép-példányokkal társíthatók. Amikor egy NSG-t társít egy alhálózathoz, az ACL-szabályok az adott alhálózat összes virtuálisgép-példányára vonatkoznak. Emellett tovább korlátozhatja a forgalmat egy adott virtuális géphez, ha egy NSG-t közvetlenül az adott virtuális géphez társít. További információ: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>További lépések

- [Windows rendszerű virtuális gép létrehozása](../../virtual-machines/windows/quick-create-portal.md)
- [Linux rendszerű virtuális gép létrehozása](../../virtual-machines/linux/quick-create-portal.md)
