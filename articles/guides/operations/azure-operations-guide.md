---
title: Útmutató az Azure-operátorok első lépésekhez |} A Microsoft Docs
description: Első lépésekhez készült Azure informatikai dolgozói útmutató
services: ''
documentationcenter: ''
author: themichaelbender-ms
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 08/24/2018
ms.author: mibender
ms.openlocfilehash: 28eeae8906480a5a160bfe11386da96b646f7427
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296856"
---
# <a name="get-started-for-azure-it-operators"></a>Ismerkedés az Azure-operátorok

Ez az útmutató a telepítése és felügyelete a Microsoft Azure-infrastruktúra kapcsolatos alapfogalmakat mutatja be. Ha most ismerkedik a felhőalapú számítástechnika, vagy az Azure, az útmutató segítségével gyorsan elsajátíthatja a fogalmak, üzembe helyezési és kezelési információkról. Ez az útmutató számos szakaszok tárgyalják egy művelet, például a virtuális gép üzembe helyezése, és részletes technical részletességgel adja meg egy hivatkozást.

## <a name="cloud-computing-overview"></a>A felhő-számítástechnika – áttekintés

A felhő-számítástechnika a hagyományos helyszíni adatközpontba modern alternatívát kínál. Nyilvános felhő adja meg, és az összes számítási infrastruktúrát és a mögöttes felügyeleti szoftver kezelése. Ezen szállítóktól számos felhőalapú szolgáltatások. Egy felhőalapú szolgáltatás ebben az esetben lehet egy virtuális gép, egy webkiszolgáló, vagy a felhőben futtatott adatbázis-kezelő. A felhőbeli szolgáltató ügyfélként bérletbe a felhőszolgáltatások révén igény szerint. Ennek során meg átalakítása hardverkarbantartás beruházna egy üzemeltetési költségek mellett. Egy cloud service emellett biztosítja ezeket az előnyöket:

- A nagy számítási környezetek gyors üzembe helyezési

- Gyors felszabadítási rendszerek, amelyek már nem szükséges

- Egyszerű telepítés hagyományosan komplex rendszerek, például terheléselosztók

- Adja meg a rugalmas számítási kapacitását, vagy szükség esetén skálázhatja

- Több költséghatékony számítási környezetek

- A webes portálon vagy programozható automatizálási bárhonnan elérhető

- A legtöbb számítási és az alkalmazás igényeinek felhőalapú szolgáltatásokhoz

A helyszíni infrastruktúrával rendelkezik teljes körű, a hardver- és központilag telepített szoftverek. Korábban ez egyes alkalmakkor teljesítetlen hardver beszerzési döntéseket, amelyek vertikális felskálázásával. Például egy csúcs teljesítményigények kielégítése érdekében több maggal rendelkező kiszolgáló van megvásárlásával. Sajnos ez az infrastruktúra előfordulhat, hogy eredményeztek igény szerint időszakon kívül. Az Azure-ban csak a szükséges infrastruktúra üzembe helyezését, és ez felfelé vagy lefelé bármikor módosíthatja. Ez vezet a fókusz a horizontális felskálázása a számítási csomópontot is üzembe helyezési keresztül egy teljesítmény igényeinek kielégítése. Költséghatékonyabb, mint a drága hardverek keresztül vertikális horizontális felskálázás a cloud services.

A Microsoft több tervezett számos Azure-adatközpontokban világszerte, telepítve van. Ezenkívül a Microsoft független felhőkben a régiókban, mint az Kínában és Németországban növekszik. Csak a legnagyobb globális vállalatok adatközpontok ily módon telepíthet, így az az Azure megkönnyíti az ügyfelek közelében a szolgáltatások üzembe helyezéséhez bármilyen méretű vállalatok számára.

A kisméretű vállalkozások számára az Azure lehetővé teszi, hogy egy alacsony költségű belépési ponthoz együtt növekszik a számítási igény szerint gyorsan méretezhető. Ez megakadályozza, hogy egy nagy méretű kezdeti tőkebefektetés infrastruktúrában, és tervezhet és hozzáláttunk rendszerek, igény szerint rugalmasan biztosít. A felhő-számítástechnika a méretezési csoport-gyors és hibatűrő modell indítási növekedési a megfelelő használatát.

Az elérhető Azure-régiók további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Felhőalapú számítástechnika modell

Az Azure felhőalapú számítástechnikai az ügyfelek számára nyújtott szolgáltatás kategóriák alapján modellt használ. A szolgáltatás három kategória közé tartozik az infrastruktúra-szolgáltatás (IaaS), Platformszolgáltatás (PaaS) és szoftverszolgáltatás (SaaS). Szállítók ossza meg néhány vagy összes feladata a számítástechnikai halomként az egyes kategóriákban lévő összetevőnél. Vessünk egy pillantást az egyes kategóriák felhőalapú számítástechnika.
![A felhő-számítástechnikai Stack összehasonlítása](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktúra-szolgáltatás

Egy felhőalapú IaaS-szolgáltatón fut, és kezeli az összes fizikai számítási erőforrások és a szükséges szoftverek a számítógép-virtualizálás engedélyezése. Ez a szolgáltatás ügyfele ezek üzemeltetett adatközpontok virtuális gépeket helyez üzembe. Bár a virtuális gépek egy külső helyszíni adatközpontban találhatók, az IaaS-felhasználó rendelkezik a konfigurációs és az operációs rendszer az alapul szolgáló infrastruktúra és a felhő szállító felügyeleti felett.

Az Azure számos IaaS-megoldások, beleértve a virtual machines, a virtual machine scale sets és a kapcsolódó hálózati infrastruktúrát tartalmaz. Virtuális gépek egy népszerű, kezdetben áttelepítéshez választott services az Azure-ba, mivel lehetővé teszi, hogy a "lift and shift" migrálási modellben. Konfiguráljon egy virtuális Gépet, mint a saját adatközpontjában futó jelenleg a szolgáltatások infrastruktúrát, és majd telepítse át a szoftvert az új virtuális gépre. Szüksége lehet az URL-címek használatával más szolgáltatásokba vagy tárolókba, például a konfigurációs frissítések végrehajtására, de ezzel a módszerrel több alkalmazásokat telepíthet át.

A Virtual machine scale sets beépített épülő Azure Virtual machines szolgáltatásban, és azonos virtuális gépek fürtjeinek üzembe helyezéséhez egyszerű módot biztosítanak. Virtuálisgép-méretezési csoportok automatikus skálázást is támogatják, úgy, hogy az új virtuális gépet is üzembe helyezhetők automatikusan szükség esetén. Így a virtual machine scale sets ideális platformot jelentenek a magasabb szintű mikroszolgáltatás számítási gazdagépfürtökre, például az Azure Service Fabric és Azure Container Service-ben.

#### <a name="paas-platform-as-a-service"></a>PaaS: Szolgáltatásként nyújtott platformon

A paas az alkalmazást, amely a felhőalapú szolgáltatás szállítójával biztosít környezetbe helyezi üzembe. A szállító hajtja végre az összes infrastruktúra-felügyelettel, így Ön az alkalmazásfejlesztéshez és az adatkezelést.

Az Azure biztosít több PaaS számítási ajánlatokat, többek között az Azure Cloud Services (webes és feldolgozói szerepkörök) és az Azure App Service Web Apps funkcióját. Mindkét esetben a fejlesztők saját-alkalmazás üzembe helyezése anélkül, hogy semmit, amelyek azt támogatják és alapelemek több lehetőség is rendelkezik. A fejlesztők hozhat létre virtuális gépeket (VM), jelentkezzen be minden egyes egy Remote Desktop Protocol (RDP) használatával vagy az alkalmazás telepítéséhez nem kell. Ezek csak nyomja le a gomb (vagy zárja be azt), és a Microsoft által biztosított eszközök a virtuális gépek kiépítése üzembe helyezése és az alkalmazás telepíthető.

#### <a name="saas-software-as-a-service"></a>SaaS: Szolgáltatott szoftver

SaaS olyan szoftver, amely központilag üzemeltetett és kezelt. Általában egy több-bérlős architektúra alapján – az alkalmazás egy verzióját minden ügyfelünk esetében használatos. Ez kiterjeszthető az összes helyen a legjobb teljesítmény érdekében több példányra. SaaS általában szoftvert egy havi vagy éves előfizetés keretében. SaaS általában szoftvert egy havi vagy éves előfizetés keretében. SaaS-szoftverszállítók felelős a a szoftververem összes összetevőjét, így az összes Ön által kezelt nyújtott szolgáltatások.

A Microsoft Office 365 egy jó példa egy SaaS-ajánlatok. Előfizetők havi vagy éves előfizetési díjat kell fizetnie, és a Microsoft Exchange, a Microsoft OneDrive és a többi szolgáltatás a Microsoft Office programcsomag kapnak. -Előfizetők mindig a legújabb verzióra, és az Exchange-kiszolgálóhoz van kezelve. Telepítés és frissítés Office évente képest, ez a kevésbé költséges és kevesebb beavatkozást igényel.

## <a name="azure-services"></a>Azure-szolgáltatások

Az Azure a saját felhő-számítástechnikai platform számos szolgáltatás biztosít. Ezek a szolgáltatások a következők:

### <a name="compute-services"></a>Számítási szolgáltatások

Alkalmazás számítási feladatait és a szolgáltatások:

- Az Azure Virtual Machines – Linuxos és a Windows

- App Services (Web Apps, Mobile Apps, Logic Apps, az API Apps és Függvényalkalmazások)

- Az Azure Batch (a nagy méretű párhuzamos és kötegelt számítási feladatok)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Adatszolgáltatások

NFS-adatok tárolására és kezelésére:

- Az Azure Storage (az Azure Blob, üzenetsor, tábla és fájl szolgáltatásból áll)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Redis Cache

### <a name="application-services"></a>Alkalmazásszolgáltatások

Működő alkalmazások és szolgáltatások:

- Azure Active Directory (Azure AD)

- Kapcsolódás az Azure Service Bus elosztott rendszerek

- Az Azure HDInsight a big Data jellegű adatok feldolgozása

- Az Azure Scheduler

- Az Azure Media Services

### <a name="network-services"></a>A hálózati szolgáltatások

Azure-ban, és az Azure és a helyszíni adatközpontok között hálózati szolgáltatások:

- Azure Virtual Network

- Azure ExpressRoute

- Az Azure által biztosított DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Az Azure-szolgáltatások részletes dokumentációjáért lásd: [Azure szolgáltatás dokumentációja](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Az Azure fő fogalmak

### <a name="datacenters-and-regions"></a>Adatközpontok és régiók

Az Azure világszerte számos régióban általánosan elérhető globális felhő platformon el. Egy szolgáltatás, alkalmazás vagy az Azure-beli virtuális gép üzembe helyezésekor, a rendszer felkéri a válasszon ki egy régiót. A kiválasztott régióban egy speciﬁc adatközpontba, ahol az alkalmazás fut jelöli. További információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

Az Azure használatának beneﬁts egyike, hogy a világ különböző pontjain azokat az alkalmazásokat helyezhet üzembe. A választott régióban is aﬀect az alkalmazás teljesítményét. Válasszon régiót, amely leginkább az ügyfelek hálózati kéréseket a késés csökkentése érdekében közelebb van az optimális. Is választhat egy régió esetében az egyes országokban az alkalmazás terjesztése a jogi követelmények teljesítéséhez.

### <a name="azure-portal"></a>Azure Portal

Az Azure Portalon egy webalapú alkalmazás, amely segítségével létrehozása, kezelése és Azure-erőforrások és szolgáltatások eltávolítása. Az Azure Portalon a következő helyen található [portal.azure.com](https://portal.azure.com). Tartalmaz egy testreszabható irányítópulton és az Azure-erőforrások kezeléséhez. Számlázási és előfizetés-információkat is biztosít. További információkért lásd: [Microsoft Azure portal áttekintése](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) és [Azure-erőforrások a portál](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>További források

Azure-erőforrások az egyes számítási, hálózatkezelési, adatok vagy alkalmazásszolgáltatási szolgáltatásokat, amelyek az Azure-előfizetéssel van telepítve. Néhány általános erőforrás például a virtuális gépek, a storage-fiókok vagy az SQL-adatbázisok. Azure-szolgáltatások gyakran több kapcsolódó Azure-erőforrások állnak. Például egy Azure virtuális gépet tartalmazhat egy virtuális gép, a storage-fiók, a hálózati adapter és a nyilvános IP-címet. Ezeket az erőforrásokat is létrehozott, felügyelt és egyesével vagy csoportosan törölt. Ez az útmutató későbbi része részletesen ismertetett Azure-erőforrások.

### <a name="resource-groups"></a>Erőforráscsoportok

Azure-erőforráscsoport egy tároló, amely az Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport a megoldás összes erőforrását, vagy csak a csoportos felügyelni kívánt erőforrásokat is tartalmazhat. Ez az útmutató későbbi része részletesen ismertetett Azure-erőforráscsoportot.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

Az Azure Resource Manager-sablon egy JavaScript Object Notation (JSON) fájlt, amely meghatározza egy erőforráscsoport központi telepítése egy vagy több erőforrás. A telepített erőforrások közti függőségeket is meghatározza. Resource Manager-sablonok az útmutató későbbi részében részletesebben terjed ki.

### <a name="automation"></a>Automation

Mellett létrehozása, kezelése és törlése erőforrásokat az Azure portal használatával PowerShell vagy az Azure parancssori felület (CLI) használatával automatizálható a ezeket a tevékenységeket.

#### <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell-modulok biztosító Azure kezelésére szolgáló parancsmagok olyan. A parancsmagok használatával létrehozása, kezelése és az Azure-szolgáltatások eltávolítása. A parancsmagok segítségével érheti el, egységes, megismételhető és hands-off központi telepítések. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/install-azurerm-ps) foglalkozó témakörben talál.

#### <a name="azure-command-line-interface"></a>Azure parancssori felületén

Az Azure parancssori felület egy olyan eszköz, létrehozása, kezelése és Azure-erőforrások eltávolítása a parancssor segítségével. Az Azure CLI Windows, Linux és Mac OS X érhető el. További információkért és a technikai részletekért, lásd: [az Azure CLI telepítése](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>REST API-k

Azure REST API-k, amelyek támogatják az Azure portal felhasználói felületén egy készletét épül. A legtöbb REST API-k lehetővé teszi, hogy programozott módon üzembe helyezése és kezelése az Azure-erőforrások és az alkalmazások internetes bármely eszközről is támogatottak. További információkért lásd: a [Azure REST SDK-leírás](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Rendszergazdák hozzáférhetnek az Azure PowerShell és az Azure CLI a böngésző-akadálymentes használatot biztosít az Azure Cloud Shell nevű keresztül. Az interaktív felület a választott, vagy a Bash vagy a PowerShell parancssori felület használata a Linux és Windows rendszergazdák rugalmas eszköz biztosít. Az Azure Cloud Shell a portálon keresztül érhetők el, egy különálló webes felület, [shell.azure.com](https://shell.azure.com), vagy egy másik hozzáférési pontok számát. További információkért lásd: [áttekintése az Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Azure-előfizetések

Egy előfizetés az Azure-szolgáltatások logikai csoportosítása, amely egy Azure-fiókra van csatolva. Egyetlen Azure-fiók több előfizetést is tartalmazhat. Azure-szolgáltatások használati díjának felszámolása előfizetésenként alapon történik. Azure-előfizetéssel rendelkezik, az előfizetés teljes hozzáféréssel rendelkező fiókot a rendszergazdák és az előfizetés az összes szolgáltatás felett rendelkező szolgáltatás-rendszergazdák. További információ a hagyományos előfizetés-rendszergazda: [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái](../../billing/billing-add-change-azure-subscription-administrator.md). A rendszergazdák, amellett az egyes fiókok is megadható az Azure-erőforrások kézben részletes [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Válassza ki, és a egy Azure-előfizetés engedélyezése

Használhatja az Azure-szolgáltatásokkal, mielőtt egy előfizetés szükséges. Több előfizetés-típus érhető el.

**Az ingyenes fiókok**: A hivatkozásra kattintva regisztrálhat egy ingyenes fiók van a [Azure-webhelyen](https://azure.microsoft.com/). Ez lehetővé teszi a kredit szeretné kipróbálni az Azure-erőforrások tetszőleges kombinációját 30 nap folyamán. Ha túllépi a hitel összegét, a fiók fel van függesztve. A próbaidőszak végén a szolgáltatások leselejtezésekor, és nem fog működni. Használatalapú fizetéses előfizetésre, bármikor frissítheti.

**Az MSDN-előfizetéseket**: Ha rendelkezik MSDN-előfizetéssel, kap egy adott mennyiség az Azure-kredit minden hónapban. Például, ha a Microsoft Visual Studio Enterprise with MSDN előfizetéssel rendelkezik, kap \$Azure-kredit havi 150.

Ha túllépi a kreditösszeg erejéig, a szolgáltatás le vannak tiltva, amíg el nem kezdi a következő hónapra. Kikapcsolja a költségkeretet, és adja hozzá a további költségek használandó hitelkártya. MSDN-fiókok néhány költségekhez képest. Például hogy kezdem meg Linux virtuális gépek Windows Server rendszert futtató, és semmilyen további díjat nem Microsoft-kiszolgálók, például a Microsoft SQL Server. Ez lehetővé teszi az MSDN-fiókok ideális fejlesztési és tesztelési forgatókönyvekhez.

**BizSpark-fiókok**: A Microsoft BizSpark programról üzenetcsere számos előnnyel jár, a startupok számára. Ezek az előnyök egyik hozzáférés minden Microsoft-szoftverek fejlesztési és tesztelési környezetek legfeljebb öt MSDN-fiókok. 150 USD első öt MSDN fiók minden egyes Azure-kredit, és kedvezményes fizet az Azure-szolgáltatások, például a virtuális gépek közül.

**Használatalapú fizetés**: az ezzel az előfizetéssel használt hitelkártya vagy bankkártya csatlakoztatásával a fiók fizet. Ha egy szervezet, akkor is lehet jóváhagyni számlázása.

**Nagyvállalati szerződések**: nagyvállalati szerződéssel, bizonyos számú services használata az Azure-ban a következő évben véglegesítés, és időben összeg után kell fizetni. Az előzetes kötelezettségvállalást, hogy az az év folyamán felhasznált. Ha túllépi a kötelezettségvállalás összege, a kerettúllépést utólag fizethet. Az előzetes kötelezettségvállalást mennyiségétől függően a kedvezményes a szolgáltatások az Azure-ban kap.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Rendszergazdai hozzáférés engedélyezése az Azure-előfizetés

Az RBAC, amelyek segítségével engedélyek hozzárendelése több beépített szerepkörrel rendelkezik. Ahhoz, hogy a felhasználó az Azure-előfizetés rendszergazdájának, hozzárendelheti azokat a [tulajdonosa](../../role-based-access-control/built-in-roles.md#owner) szerepkör az előfizetések szintjén. A tulajdonosi szerepkör a felhasználó teljes hozzáférést biztosít, az összes erőforrást az előfizetésben, beleértve a jogot arra, hogy mások való hozzáférés delegálására.

További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Számlázási adatok megtekintése az Azure Portalon

Fontos része az Azure-rendszer azon képessége, számlázási információk megtekintéséhez. Az Azure Portalon az Azure számlázási információihoz részletes betekintést nyújt.

További információkért lásd: [az Azure számlázási és napi használati adatok letöltése](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Számlázási API-jai számlázási információinak lekérése

A számlázás a portálon való megtekintése mellett egy parancsfájl vagy a program az Azure számlázási REST API-kon keresztül férhetnek hozzá a számlázási információkat:

- Az Azure-használati API segítségével a használati adatok lekéréséhez. Kapcsolódó Azure-erőforrások megtekintett finomhangolhatja a számlázási használati adatokat. Például címke mindegyik erőforrás egy erőforráscsoportban, részleg vagy projekt nevét, és a költségek kifejezetten az adott egy címkét, majd nyomon követheti.

- Az Azure sebessége kártya API segítségével a listában az összes rendelkezésre álló erőforrások, valamint a metaadatok és a díjszabás információkkal szolgál azokhoz az erőforrásokhoz.

További információkért lásd: [betekintést nyerhet a Microsoft Azure erőforrás-használat](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>A díjkalkulátor az előre jelzett költség

Az Azure-ban minden egyes szolgáltatás díjszabása eltér. Sok Azure-szolgáltatás alapszintű, Standard és Premium szintet biztosítanak. Általában minden szint tartalmaz ára és teljesítménye szintekre. Használatával a [online díjkalkulátor](http://azure.microsoft.com/pricing/calculator), díjszabási becslések hozhat létre. A díjkalkulátorban feltüntetett tartalmaz egy erőforrás vagy erőforráscsoport költségek becsléséhez rugalmasságot.

### <a name="set-up-billing-alerts"></a>Számlázási értesítések beállítása

Miután telepítette az alkalmazáshoz vagy megoldáshoz az Azure-ban, hozhat létre riasztásokat, amelyek az Ön e-mailt, ha a riasztásban szereplő definiálva megközelítést, a költségkorlátozási küldenie. További információkért lásd: [elszámolási értesítések a Microsoft Azure-előfizetések beállítása](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Azure Resource Manager

Az Azure Resource Manager az Azure-erőforrások egy központi telepítési, kezelési és szervezet mechanizmusa. Resource Manager használatával összeállíthatja az egyes erőforrások száma egy erőforráscsoportban.

Resource Manager üzembe helyezési funkciók lehetővé teszik a testre szabható telepítésre és a kapcsolódó erőforrások is tartalmaz. Például Resource Manager használatával telepítheti az alkalmazást, amely több virtuális gépet, a load balancer és a egy SQL database egyetlen egységként áll. Ezeket az üzemelő példányokat fejlesztése a Resource Manager-sablon használatával.

A Resource Manager számos előnyt kínál:

- A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

- Ismételt üzembe helyezheti megoldását, a fejlesztési életciklus során, és biztos lehet benne, hogy az erőforrások telepítése konzisztens lesz.

- Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

- Meghatározhatja az erőforrások közti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

- Alkalmazhat hozzáférés-vezérlés az összes szolgáltatás az erőforráscsoportban, mert az RBAC natív módon integrálva van a felügyeleti platformba.

- Címkékkel láthatja az erőforrásokat, hogy logikusan rendszerezhesse az összes erőforrást az előfizetésében.

- Az azonos címkén osztozó erőforrások csoportjának költségeit megtekintve jól átláthatók a szervezet számlái.

### <a name="tips-for-creating-resource-groups"></a>Erőforráscsoportok létrehozásával kapcsolatos tippek

Az erőforráscsoportokkal kapcsolatos döntések hajt végre, amikor vegye figyelembe a következő tippeket:

- Egy erőforráscsoportba tartozó összes erőforrást az azonos életciklussal kell rendelkeznie.

- Csak egy csoport egy erőforráshoz rendelhet egyszerre.

- Adja hozzá, vagy eltávolít egy erőforrást egy erőforráscsoportból, tetszőleges időpontban. Minden erőforrás egy erőforráscsoportba kell tartoznia. Így ha egy erőforrás eltávolítása egy csoportból, hozzá kell adnia azt a másikra.

- A legtöbb típusú erőforrások bármikor áthelyezheti egy másik erőforráscsoportban található.

- Az erőforrásokat egy erőforráscsoportba tartozó különböző régiókban is lehetnek.

- Használhatja egy erőforráscsoportot az erőforrások elérésének szabályozására.

### <a name="building-resource-manager-templates"></a>Resource Manager-sablonok létrehozása

Resource Manager-sablonok deklaratív határozza meg, az erőforrások és a egy egységes erőforrás-csoportba telepített erőforrás-konfigurációk. Resource Manager-sablonok segítségével koordinálhatja az összetett központi telepítések felesleges parancsfájlok vagy manuális konfigurálása nélkül. Után, ha egy sablon fejlesztése, üzembe helyezheti többször – minden alkalommal, amikor az egy azonos serkenti az eredményt.

Resource Manager-sablonnal négy részből áll:

- **Paraméterek**: ezek a bemeneti adatokat a központi telepítést. Paraméterértékek egy emberi vagy egy automatikus folyamat adható meg. Egy példa a paraméter egy rendszergazdai felhasználónevet és jelszót egy Windows virtuális gép lehet. A paraméterértékek az üzembe helyezés során használatosak, amikor azok van megadva.

- **Változók**: ezek a központi telepítés során használt értékek tárolására használatosak. Ellentétben a paramétereket egy változó értéke nincs megadva üzembe helyezéskor. Ehelyett meglehetősen nehéz kódolt vagy dinamikusan hozzuk létre.

- **Erőforrások**: Ez a szakasz a sablon meghatározza a szükséges erőforrásokat kell telepíteni, mint például a virtuális gépek, tárfiókok és a virtuális hálózatok.

- **Kimeneti**: egy központi telepítés befejezése után, a Resource Manager a képes például dinamikusan létrehozott kapcsolati karakterláncokat adatokat adja vissza.

Az üzembe helyezési automatizálást az alábbi módszerek érhetők el:

- **Függvények**: Resource Manager-sablonok a számos funkciót is használhatja. Ezek közé tartozik az műveletek, például a sztring átalakítása, alakítása, egy meghatározott erőforrás több példányának telepítése, és dinamikusan a célként megadott erőforráscsoportja visszaadása. Erőforrás-kezelő funkciók segítségével hozhat létre dinamikus központi telepítések.

- **Erőforrás-függőségek**: Ha több olyan erőforrást, az egyes erőforrások architektúráknak fog függőségi viszonyban vannak másokkal. Üzembe helyezés megkönnyítéséhez használhatja, hogy a függő erőforrások telepítése előtt a többi függőségi határozza meg.

- **Sablon linking**: belül Resource Manager-sablon egy kapcsolat egy másik sablonba. Ez lehetővé teszi a központi telepítési felbontása a megcélzott, a cél-specifikus sablonok egy csoportba.

Resource Manager-sablonok bármilyen szövegszerkesztővel létrehozható. Azonban a Visual Studióhoz készült Azure SDK eszközöket tartalmaz. A Visual Studio használatával, is erőforrások hozzáadása a sablont a varázsló lépéseit, majd üzembe helyezése és hibakeresése a sablont, közvetlenül a Visual Studión belül. További információkért lásd: [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md).

Végül meglévő erőforráscsoportok is átalakítása egy újrafelhasználható sablonok az Azure Portalról. Ez hasznos lehet, ha szeretne létrehozni egy meglévő erőforráscsoportot, egy üzembe helyezhető sablont, vagy csak szeretné megvizsgálja a mögöttes JSON. Szeretné exportálni egy erőforráscsoportot, válassza ki a **Automation-szkript** gomb az erőforráscsoport beállításai közül.

## <a name="security-of-azure-resources-rbac"></a>Az Azure-erőforrások (RBAC)

A megadott hatókörben felhasználói fiókokhoz működési hozzáférést biztosíthat: előfizetés, erőforráscsoport vagy egyéni erőforrás. Ez azt jelenti, hogy az erőforrások erőforrás-csoportba, például egy virtuális gép és minden kapcsolódó erőforrás üzembe helyezésének, és engedélyek egy adott felhasználó vagy csoport számára. Ez a módszer csak a célként megadott erőforráscsoportja tartozó erőforrásokhoz való hozzáférést korlátozza. A hozzáférést egy egyetlen erőforrást, például egy virtuális gépet vagy egy virtuális hálózatot.

Hozzáférést biztosítani, hozzárendelhet egy szerepkört a felhasználó vagy felhasználói csoportot. Számos előre definiált szerepkörök tartoznak. Saját egyéni szerepköröket is meghatározhat.

Íme néhány példa [beépített szerepkörök az Azure-ban](../../role-based-access-control/built-in-roles.md):

- **Tulajdonos**: Ezzel a szerepkörrel rendelkező felhasználók mindent felügyelhetnek, beleértve a hozzáférést.

- **Olvasó**: Ezzel a szerepkörrel rendelkező felhasználók (kivéve a titkos kulcsok) minden típusú erőforrásokat tudja olvasni, de nem végezhet módosításokat.

- **Virtuális gépek Közreműködője**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a virtuális gépek, de nem kezelheti a virtuális hálózatot, amelyhez csatlakoznak, vagy a tárfiókot, ahol a VHD-fájl található.

- **SQL-Adatbázisok Közreműködője**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik az SQL Database-adatbázisok, de nem a biztonsággal kapcsolatos házirendjeiket.

- **SQL-biztonságkezelő**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik az SQL Server-kiszolgálók és adatbázisok biztonsági házirendeket.

- **Tárfiók-közreműködő**: Ezzel a szerepkörrel rendelkező felhasználók is tárfiókok kezelését, de nem tudja kezelni a storage-fiókok hozzáférést.

További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

Azure-beli virtuális gépek egyike a központi IaaS-szolgáltatások az Azure-ban. Azure-beli virtuális gépek Windows vagy Linux rendszerű virtuális gépek telepítését támogatja a Microsoft Azure-adatközpontba. Az Azure Virtual Machines, a virtuális gép konfigurációja teljes felett rendelkezik, és felelősek az összes szoftver telepítése, konfigurálása és karbantartása.

Amikor telepít egy Azure virtuális Gépen, kép kiválaszthatja az Azure Marketplace-ről, vagy általános saját rendszerképek is biztosítanak. A lemezképet a alkalmazni az operációs rendszer és a kezdeti konfigurációt használja. Az üzembe helyezés során a Resource Manager bizonyos beállításait, például a számítógép neve, a rendszergazdai hitelesítő adatokkal és a hálózati konfiguráció hozzárendelése fogja kezelni. Használhatja az Azure virtuálisgép-bővítmények további automatizálásához a konfigurációkat, például a szoftver telepítését, a víruskereső konfigurációs és figyelési megoldások.

Számos különböző méretű virtuális gépeket hozhat létre. A virtuális gép mérete szabja meg, például feldolgozási, memória és a tárolókapacitás erőforrás-kiosztást. Bizonyos esetekben például az RDMA-kompatibilis hálózati adapterek és SSD-lemezeket a funkciók érhetők el csak az egyes Virtuálisgép-méretek. Virtuálisgép-méretek és képességek teljes listáját lásd: "Az Azure-beli virtuális gépek méretei" a [Windows](../../virtual-machines/windows/sizes.md) és [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Használati esetek

Azure-beli virtuális gépek teljes körű, konfigurációs kínálnak, mivel ezek ideálisak, a server számítási feladatok, amely nem illik a PaaS-modell számos. Server számítási feladatainak adatbázis-kiszolgálók (SQL Server, Oracle vagy mongodb-hez), például a Windows Server Active Directory, Microsoft SharePoint, és számos további lehetségessé válik futtatásához a Microsoft Azure platformon. Ha szükséges, egy vagy több Azure-régióban, nagy mennyiségű újrakonfigurálás nélkül az ilyen számítási feladatok áthelyezheti a helyszíni adatközpontból.

### <a name="deployment-of-virtual-machines"></a>Virtuális gépek telepítésének

Telepítheti az Azure-beli virtuális gépek az Azure portal használatával, az Azure PowerShell modullal automation használatával vagy a platformfüggetlen parancssori felülettel automation használatával.

#### <a name="portal"></a>Portál

Virtuális gép üzembe helyezése az Azure portal használatával van szükség, csak egy aktív Azure-előfizetés és a egy webböngészőben való hozzáférést. Kiválaszthatja, hogy számos különböző operációsrendszer-lemezképek, a különböző konfigurációkat. Az üzembe helyezés során minden tárolási és hálózati követelményei vannak konfigurálva. További információkért lásd: "A virtuális gép létrehozása az Azure Portalon" a [Windows](../../virtual-machines/windows/quick-create-portal.md) és [Linux](../../virtual-machines/linux/quick-create-portal.md).

Mellett az Azure Portalon a virtuális gép üzembe helyezése, telepíthet egy Azure Resource Manager-sablon a portálon. Ezzel üzembe helyezése és konfigurálása az összes erőforrás a sablonban meghatározott. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Egy Azure virtuális gép üzembe helyezése a PowerShell használatával lehetővé teszi, hogy minden kapcsolódó virtuális gép erőforrás, beleértve a tárolási és hálózati teljes üzembe helyezés automatizálása. További információkért lásd: [Windows virtuális gép létrehozása Resource Manager és a PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Azure számítási erőforrásokat külön-külön telepítése, mellett az Azure PowerShell-modul segítségével egy Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Parancssori felület (CLI)

Csakúgy, mint a PowerShell-modult az Azure parancssori felületének biztosít az üzembe helyezési automatizálást, és a Windows, OS X vagy Linux rendszerek is használhatók. Ha az Azure CLI a **virtuális gép – gyorslétrehozási** paranccsal, az összes kapcsolódó virtuálisgép-erőforrások (beleértve a tárolási és hálózatkezelési) és a virtuális gép saját maga telepít. További információkért lásd: [Linux rendszerű virtuális gép létrehozása az Azure-ban a parancssori felület használatával](../../virtual-machines/linux/quick-create-cli.md).

Hasonlóképpen az Azure CLI használatával egy Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Hozzáférés és biztonság virtuális gépeken

Az internetről a virtuális gépek elérése szükséges a társított hálózati interfész vagy terheléselosztó, ha van ilyen, nyilvános IP-címmel kell konfigurálni. A nyilvános IP-címet fogja oldani a virtuális gép vagy terheléselosztó DNS-név tartalmaz. További információkért lásd: [IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

A virtuális géphez való hozzáférés a nyilvános IP-címen keresztül, egy hálózati biztonsági csoport (NSG) erőforrás használatával kezelheti. Az NSG-KET gyakorlatilag tűzfalként funkcionál, és lehetővé teszi, hogy vagy a hálózati adapter vagy alhálózat konfigurálásában a meghatározott portokon keresztül a forgalom megtagadásához. Például egy távoli asztali munkamenetet létrehozni egy Azure virtuális Gépen, kell konfigurálni az NSG-t, hogy a bejövő forgalmat a 3389-es porton. További információkért lásd: [egy virtuális gép portjainak megnyitása az Azure-ban az Azure portal használatával](../../virtual-machines/windows/nsg-quickstart-portal.md).

Végül minden olyan számítógép rendszer felügyeletét, a kell adnia biztonsági egy Azure virtuális gépen, az operációs rendszer biztonsági hitelesítő adatok és a szoftver tűzfalak használatával.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage szolgáltatás egy Microsoft által felügyelt szolgáltatás, amely tartós, méretezhető és redundáns tárolást biztosít. Hozzáadhat egy Azure storage-fiók erőforrásként bármely erőforráscsoportban bármelyik erőforrás üzembe helyezési módszer használatával. Az Azure négyféle tárolási típust tartalmaz: Blob storage, File Storage, Table storage és Queue storage. Egy tárfiók üzembe helyezésekor, két fióktípus esetében elérhető, általános célú és blob storage-ban. Általános célú tárfiók hozzáférést biztosít az összes négyféle tárolási típust. BLOB storage-fiókok hasonlóak az általános célú fiókokhoz, de speciális blobok, amelyek tartalmazzák a gyakori és ritka elérésű hozzáférési szint tartalmaz. Blob Storage-további információ: [Azure Blob storage](../../storage/blobs/storage-blob-storage-tiers.md).

Az Azure storage-fiókok konfigurálhatók különböző szintű redundancia:

- **Helyileg redundáns tárolás** biztosításával, hogy az adatok három példányban végzett szinkron írási sikeres tekinteni, mielőtt a magas rendelkezésre állást biztosít. Ezek a másolatok egy régió egyetlen létesítményén vannak tárolva. A replikák külön tartalék tartományokban és frissítési tartományokban tárolja. Ez azt jelenti, hogy az adatok érhető el, még akkor is, ha egy tárolócsomópont, amely az adatokat sikertelen rendelkező vagy frissítendő offline állapotba helyeznek.

- **Georedundáns tárolás** lehetővé teszi az adatok három szinkron másolatát az elsődleges régióban a magas rendelkezésre állás érdekében, ezért úgy majd aszinkron módon három replikával vész-helyreállítási egy párosított régióban.

- **Írásvédett georedundáns tárolás** georedundáns tárolás plusz képes olvasni az adatokat a másodlagos régióban. Ez a képesség megkönnyíti a részleges vész-helyreállítási alkalmas. Ha az elsődleges régió problémájára, módosíthatja az alkalmazás csak olvasási hozzáféréssel rendelkeznek a párosított régióba.

### <a name="use-cases"></a>Használati esetek

Az egyes tárolási típusok különböző használati esetek rendelkezik.

#### <a name="blob-storage"></a>Blob Storage

A word *blob* rövidítése: *nagyméretű bináris objektumok*. Blobok mint amilyeneket a számítógépén tárolni, strukturálatlan fájlok legyenek. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható. Az Azure Blob storage az Azure Virtual Machines adatlemezek is tartalmazza.

Az Azure Storage háromféle blobot támogatja:

- **Blokkblobok** blokkblobokban normál 195 GB-os fájlok mérete (4 MB x 50 000 blokk). Az elsődleges blokkblobok funkcióban az elejétől a végéig, például médiafájlok vagy webhelyek képfájlok olvasható fájlok tárolására. Mivel 64 MB-nál nagyobb méretű fájlokat fel kell tölteni, kis blokkok néven szerepelnek a blokkblobok használatát támogatják. Ezek a blokkok majd konszolidált (vagy véglegesített) a végső blobba.

- **Lapblobok** blokkblobokban véletlenszerű hozzáférést fájlok akár 1 TB-nál. Lapblobok elsősorban a biztonsági tárolóként szolgálnak a VHD lemezek tartós biztosítanak az Azure Virtual Machines, az IaaS számítási szolgáltatás az Azure-ban. Mivel 512 bájtos oldalak véletlenszerű olvasási/írási hozzáférést nyújtanak a lapblobok néven szerepelnek.

- **Hozzáfűző blobok** áll, például blokkolja a blokkblobok használatát támogatják, de vannak optimalizálva műveletek hozzáfűzésére. Ezek gyakran használják a naplózási információk egy vagy több forrásból származó egyazon blobba. Például előfordulhat, hogy írt összes a nyomkövetési adatainak naplózása az ugyanazon a hozzáfűző blob egy több virtuális gépen futó alkalmazás. Egyetlen hozzáfűző blob akár 195 GB is lehet.

További információkért lásd: [.NET használatával az Azure Blob storage használatának első lépései](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Az Azure File storage egy szolgáltatása, amely a felhőbeli fájlmegosztásokat kínál a szabványos Server Message Block (SMB) protokoll használatával. A szolgáltatás támogatja az SMB 2.1 és az SMB 3.0-s. Az Azure File storage segítségével gyorsan és költséges újraírások nélkül, Azure-ra épülő alkalmazások áttelepíthetők. Azure-beli virtuális gépeken futó alkalmazások, a cloud services vagy a helyi ügyfelek csatlakoztathatja egy a felhőben. Ez hasonlít hogyan egy asztali alkalmazás csatlakoztatja-e a csatlakoztatott SMB-megosztáshoz. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Mivel a File storage-megosztás egy szabványos SMB-fájlmegosztás, az Azure-ban futó alkalmazások férhetnek hozzá a fájlrendszer adatátviteli API-jain keresztül a megosztás adataihoz. A fejlesztők ezért a saját meglévő kódjaik és képességeik felhasználásával áttelepíthetik az alkalmazásokat. Informatikai szakemberek a PowerShell-parancsmagok segítségével hozzon létre, csatlakoztathatja és kezelheti a File storage-megosztásokat az Azure-alkalmazások felügyeletének részeként.

További információkért lásd: [a Windows Azure File storage használatának első lépései](../../storage/files/storage-how-to-use-files-windows.md) vagy [Azure File storage használata linuxszal](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Table Storage

Az Azure Table Storage egy olyan szolgáltatás, amely strukturált NoSQL-adatokat tárol a felhőben. A TABLE storage séma nélküli kialakítás egy kulcs-/ attribútumtár. Mivel a Table storage séma nélküli, könnyebbé vált az adatok alkalmazkodni az alkalmazás változásával igényeinek. Az adatok hozzáférése gyors és költséghatékony, bármilyen alkalmazásról legyen is szó. Hasonló adatmennyiséggel számolva a Table Storage általában határozottan kevesebb költséggel jár, mint egy hagyományos SQL.

A Table Storage segítségével rugalmas adatkészleteket tárolhat, például webalkalmazások felhasználói adatait, címtárakat, eszközadatokat és bármilyen egyéb metaadatot, amelyre a szolgáltatásnak szüksége van. Korlátlan számú entitást tárolhat egy tábla. Egy tárfiók korlátlan számú táblát, a tárfiók kapacitásán belül is tartalmazhat.

További információkért lásd: [Azure Table storage használatának első lépései](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Az Azure Queue Storage felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. Méretezhető alkalmazások tervezésekor az alkalmazás-összetevőket sokszor szétválasztják, úgy, hogy ezek egymástól függetlenül lehessen méretezni. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

További információkért lásd: [Azure Queue storage használatának első lépései](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Storage-fiók üzembe helyezése

Storage-fiók üzembe helyezésének számos lehetőség van.

#### <a name="portal"></a>Portál

Storage-fiók az Azure Portallal való központi telepítéséhez szükséges csak egy aktív Azure-előfizetés és a egy webböngészőben való hozzáférést. Új tárfiók üzembe helyezhető egy új vagy meglévő erőforráscsoportot. Miután létrehozta a tárfiókot, létrehozhat egy blob tárolókhoz vagy fájlmegosztásokhoz a portál használatával. Tábla létrehozása, és a várólista-entitások programozott módon. További információkért lásd: [hozzon létre egy tárfiókot](../../storage/common/storage-quickstart-create-account.md).

Mellett egy storage-fiókot az Azure Portalról üzembe helyezése Azure Resource Manager-sablon a portálról is telepítheti. Ez telepítését és konfigurálását összes erőforrást, mert definiálva a sablonban, beleértve az összes tárfiókot. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Azure storage-fiók üzembe helyezése a PowerShell használatával lehetővé teszi a teljes üzembe helyezés automatizálása a storage-fiókot. További információkért lásd: [az Azure PowerShell az Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Külön-külön telepítése az Azure-erőforrások, mellett az Azure PowerShell-modul segítségével egy Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Parancssori felület (CLI)

Csakúgy, mint a PowerShell-modult az Azure parancssori felületének biztosít az üzembe helyezési automatizálást, és a Windows, OS X vagy Linux rendszerek is használhatók. Használhatja az Azure CLI **storage-fiók létrehozása** parancs használatával hozzon létre egy tárfiókot. További információkért lásd: [az Azure Storage az Azure CLI használatával.](../../storage/common/storage-azure-cli.md)

Hasonlóképpen az Azure CLI használatával egy Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Hozzáférés és biztonság az Azure Storage

Az Azure Storage számos lehetőséget kínál, ha az Azure Portalon, beleértve a virtuális gép létrehozása és a művelet során, és a tároló ügyfélkódtárai érhető el.

#### <a name="virtual-machine-disks"></a>Virtuálisgép-lemezek

Amikor telepít egy virtuális gépet, is hozzon létre egy tárfiókot, amely tárolja a virtuális gép operációsrendszer-lemezt és bármelyik adatlemeznek kell. Válasszon ki egy meglévő tárfiókot, vagy hozzon létre egy újat. A blob maximális mérete 1 024 GB, mert az egyetlen virtuális gép lemezének 1,023 GB maximális mérettel rendelkezik. Nagyobb adatlemez konfigurálásához nyújt a virtuális géphez több adatlemez, és a tárolókészlet őket össze egyetlen logikai lemez. További információkért lásd: "kezelése Azure-lemezek" számára [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) és [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Storage-eszközökkel

Az Azure storage-fiókok számos különböző storage-tallózók, például a Visual Studio Cloud Explorer keresztül érhetők el. Ezek az eszközök lehetővé teszik a storage-fiókok és az adatok között. További információ és elérhető tártallózók listáját lásd: [Azure Storage-ügyféleszközök](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Storage API

Storage-erőforrások bármely, amelyekkel HTTP/HTTPS-kérelmek létrehozására alkalmas nyelven elérhető lesz. Ezenfelül az Azure Storage számos népszerű nyelvhez biztosít programozási kódtárakat. Ezek a kódtárak alfolyamatot például a szinkron és aszinkron hívás, műveletek, kivételek kezelése és automatikus újrapróbálkozásokat kötegelése egyszerűsítése érdekében az Azure Storage használatához. További információkért lásd: [Azure Storage szolgáltatás REST API-referencia](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Tárelérési kulcsok

Mindegyik tárfiók két hitelesítési kulccsal, egy elsődleges és másodlagos rendelkezik. Vagy a storage access műveletekhez használható. Ezek a kulcsok tárolási segítségével egy tárfiókot, és a szükséges adatok programozott elérése. A biztonság növelése érdekében a kulcsok időnkénti átvitel engedélyezése két kulcs létezi. Rendkívül fontos biztonsága érdekében a kulcsok, mivel birtokában, a fiók nevével együtt korlátlan hozzáférést az adatokhoz a storage-fiókban.

#### <a name="shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok

Ha szeretne engedélyezése a felhasználók számára, hogy rendelkezik a tárolási erőforrásokhoz való hozzáférését szabályozza, létrehozhat egy közös hozzáférésű jogosultságkód. Közös hozzáférésű jogosultságkód URL-címre, amely meghatalmazott hozzáférést biztosít a tároló egyik erőforrásához hozzáfűzhető jogkivonatot. Bárki, aki ismeri a token hozzáférhet az erőforrást, amelyekre az mutat az engedélyekkel, hogy meghatározza, az az időtartam, hogy a érvényes. További információkért lásd: [a közös hozzáférésű jogosultságkódot](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuális hálózatok olyan virtuális gépek közötti kommunikáció támogatásához szükséges. Határozza meg, alhálózatok, a egyéni IP-címet, a DNS-beállítások, a biztonsági szűrést, és a terheléselosztás. Az Azure támogatja különböző használati esetek: hálózatok csak felhőalapú vagy hibrid virtuális hálózatok.

### <a name="cloud-only-virtual-networks"></a>Kizárólag felhőalapú virtuális hálózatok

Egy Azure virtuális hálózatra, alapértelmezés szerint csak az Azure-ban tárolt erőforrások érhető el. Az azonos virtuális hálózathoz csatlakozó erőforrás kommunikálhatnak egymással. Társítsa a virtuális géphez tartozó hálózati adapter és a egy nyilvános IP-címet a virtuális gép az interneten keresztül elérhető legyen a terheléselosztók. A nyilvánosan elérhető erőforrásokhoz való biztonságos hozzáférést segíthet a hálózati biztonsági csoport használatával.

![Az Azure Virtual Network 2 szintű webalkalmazás számára](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hibrid virtuális hálózatok

Egy helyszíni hálózat csatlakozhat egy Azure virtuális hálózaton, ExpressRoute és a egy helyek közötti VPN-kapcsolat használatával. Ebben a konfigurációban az Azure virtuális hálózat lényegében egy felhőalapú bővítmény a helyszíni hálózat.
![Hibrid virtuális hálózati VPN használatával](https://docs.microsoft.com/azure/architecture/reference-architectures/_images/blueprints/hybrid-network-vpn.png)

Az Azure virtuális hálózat a helyszíni hálózathoz csatlakozik, mert a létesítmények közötti virtuális hálózatok kell használnia a címtér a szervezet által használt egyedi részéhez. Azonos módon, amely a különböző vállalati helyek vannak hozzárendelve egy adott IP-alhálózatot Azure, a hálózat kiterjesztésére válik egy másik helyre.
Többféle módon is üzembe helyezése egy virtuális hálózatot.

- [Portál](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Parancssori felület (CLI)](../../virtual-network/quick-create-cli.md)

- Az Azure Resource Manager-sablonok

>**Mikor érdemes használni**: dolgozik, az Azure-beli virtuális gépekkel, bármikor, virtuális hálózatokkal működnek. Ez lehetővé teszi a nyilvános és privát alhálózat hasonló a helyszíni adatközpontjaik között, a virtuális gépek szegmentálja.

>**Első lépések**: Azure-beli virtuális hálózathoz az Azure Portallal való központi telepítéséhez szükséges csak egy aktív Azure-előfizetés és a egy webböngészőben való hozzáférés. Új virtuális hálózat üzembe helyezhető egy új vagy meglévő erőforráscsoportot. Új virtuális gép létrehozásakor a portálon válassza ki a meglévő virtuális hálózattal, vagy hozzon létre egy újat. Első lépések és [hozzon létre egy virtuális hálózatot az Azure portal használatával](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Hozzáférés és biztonság a virtuális hálózatokban

Segíthet az Azure virtuális hálózatok biztonságos hálózati biztonsági csoport használatával. NSG tartalmaz egy hozzáférés-vezérlési lista (ACL) szabályok, amelyek engedélyezik vagy megtagadják a hálózati forgalmat a virtuális hálózatban futó Virtuálisgép-példányokat listája. NSG-ket társíthat-ket alhálózatokhoz vagy az alhálózaton belüli Virtuálisgép-példányokhoz. Ha alhálózattal társít egy NSG-t, az ACL-szabályok érvényesek lesznek az alhálózatban lévő Virtuálisgép-példányok. Emellett további korlátozhatja a forgalmat egy adott virtuális gépre úgy NGS közvetlenül a virtuális gép. További információkért lásd: [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>További lépések

- [Egy Windows virtuális gép létrehozása](../../virtual-machines/windows/quick-create-portal.md)
- [Linux rendszerű virtuális gép létrehozása](../../virtual-machines/linux/quick-create-portal.md)
