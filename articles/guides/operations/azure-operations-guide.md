---
title: "Első lépések útmutató az Azure informatikai operátorok |} Microsoft Docs"
description: "Első lépésekről szóló útmutatót az Azure informatikai operátorok"
services: 
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: nepeters
ms.openlocfilehash: 4a913e188dd40b0306be375b016b9e8a3739ed72
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="introduction-to-cloud-computing-and-microsoft-azure"></a>Bevezetés a felhőalapú informatika és a Microsoft Azure

Ez az útmutató bemutatja a központi telepítés és a Microsoft Azure infrastruktúra kezelése kapcsolatos alapfogalmakat. Ha még nem ismeri a felhőalapú informatika vagy az Azure, az útmutató segítségével gyorsan megismerheti fogalmakat, telepítési és kezelési információkról. Sok szakaszt a jelen útmutató egy művelet, például a virtuális gépek telepítését tárgyalja, és majd adjon meg egy hivatkozást a részletes technikai részletei.


## <a name="cloud-computing-overview"></a>A felhőalapú megoldások áttekintése

A felhőalapú informatika egy modern alternatívát a hagyományos helyszíni adatközpontját a. Nyilvános felhő szállítók nyújtanak, és minden számítástechnikai infrastruktúra és a mögöttes felügyeleti szoftver kezelése. Ezen szállítóktól adja meg a felhőalapú szolgáltatások széles választékát. Egy felhőalapú szolgáltatás ebben az esetben előfordulhat, hogy lehet a virtuális gép, egy webkiszolgáló vagy a felhőben üzemeltetett adatbázismotor. A felhőbeli szolgáltató ügyfélként címbérlet ezek felhőszolgáltatás igény szerint alapon. Ennek során, akkor átalakítása hardver karbantartási beruházási költség egy üzemeltetési költségek mellett. Egy felhőalapú szolgáltatás a következő előnyökkel is rendelkezik:

-   Gyors üzembe helyezési a nagy számítási környezetek

-   Gyors felszabadítás rendszerek, amelyek már nem szükséges

-   Egyszerű telepítés hagyományosan összetett rendszerek, például terheléselosztók

-   Rugalmas számítási kapacitás, és ha szükséges

-   Több költséghatékony környezetekhez

-   Egy webes portál vagy programozott automation bárhonnan elérhető

-   A legtöbb számítási és az alkalmazás igényeinek felhő alapú szolgáltatások

A helyszíni infrastruktúrával rendelkezik a hardver és telepített szoftverek teljes ellenőrzését. Korábban ez egyes alkalmakkor hardver beszerzési döntésekhez, amelyek arra utalnak vertikális felskálázásával. Például egy kiszolgáló több maggal maximális teljesítmény igényeihez van megvásárlását. Sajnos ez az infrastruktúra előfordulhat, hogy fogadásában egy igény szerinti időszakon kívül. Az Azure-ral csak a szükséges infrastruktúra központi telepítéséhez, és ez felfelé vagy lefelé bármikor módosíthatja. Ennek eredménye egy fókuszt további számítási csomópontok központi felskálázással teljesítmény kell teljesíteni. Cloud services csomag kiterjesztése helyett keresztül költséges hardver vertikális felskálázásával.

A Microsoft több tervezett a világ minden táján sok Azure adatközpontjaiban telepítve van. Emellett a Microsoft azokban a régiókban, például a kínai és Németországban szuverén felhők növekszik. Csak a legnagyobb globális vállalatok adatközpontok ezen a módon kikapcsolja, telepítheti, így Azure használatával megkönnyíti az ügyfeleknek megközelíti a szolgáltatások telepítéséhez tetszőleges méretű vállalatok számára.

A kisméretű vállalkozások számára Azure lehetővé teszi, hogy egy alacsony költségű belépési pont az igény szerinti számítási növeli a leggyorsabban méretezési képességét. Ez megakadályozza, hogy az infrastruktúra egy nagy kezdeti tőkebefektetés szükségessége, és a tervezővel és Újratervezés rendszerek, igény szerint rugalmasan biztosít. A felhőalapú informatika meghatározására jól igazodik a skála-gyors és sikertelen-gyors modell indítási növekedési elfér használatát.

Az elérhető Azure-régiók további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-is-classified-into-three-categories-saas-paas-and-iaas"></a>A felhőalapú megoldások besorolásának három kategóriába soroltuk: SaaS, a PaaS és az infrastruktúra-szolgáltatási.

#### <a name="saas-software-as-a-service"></a>SaaS: Szolgáltatott szoftver

SaaS egy központilag tárolt és kezelt szoftverek. Általában a több-bérlős architektúrák alapján – az alkalmazás egyetlen verziójának használata az összes ügyfél számára. Azt is méretezhető több példány található összes hely a legjobb teljesítmény érdekében. SaaS általában szoftvert havi vagy éves előfizetéssel.

A Microsoft Office 365 egy SaaS ajánlat jól szemlélteti. Előfizetői havi vagy éves előfizetés díjat kell fizetnie, és a Microsoft Exchange, Microsoft OneDrive és a többi szolgáltatásként a Microsoft Office suite. Előfizetők mindig letöltheti a legfrissebb verziót, és az Exchange-kiszolgálóhoz van kezelve. Office telepítéséről és frissítéséről évente, ez kevésbé költséges, és kevesebb beavatkozást igényel.

#### <a name="paas-platform-as-a-service"></a>A PaaS: Platformok 

PaaS az olyan környezetben, amely a felhőalapú szolgáltatás szállítójával biztosít az alkalmazás telepítése. A szállító végzi az infrastruktúra felügyelete mindegyikét, alkalmazásfejlesztés összpontosíthat.

Azure biztosít több PaaS számítási ajánlatokat, beleértve az Azure Cloud Services (webes és feldolgozói szerepkörök) és az Azure App Service Web Apps szolgáltatása. Mindkét esetben a fejlesztők az alkalmazások telepítése anélkül, hogy tudnák, amelyek támogatják ezt anyacsavarok semmit több lehetőség is rendelkezik. Fejlesztők nem kell létrehozni a virtuális gépek (VM), minden bejelentkezés Remote Desktop Protocol (RDP) használatával, vagy telepítheti az alkalmazást. Ezek csak kattintson a gombra (vagy zárja be azt), és a Microsoft által biztosított eszközök kiépíteni a virtuális gépek telepítése és az alkalmazás telepítését.

#### <a name="iaas-infrastructure-as-a-service"></a>Infrastruktúra-szolgáltatási: Szolgáltatott infrastruktúra

A felhő infrastruktúra-szolgáltatótól fut, és kezeli az összes fizikai számítási erőforrások és a szükséges szoftverek a számítógép-virtualizálás engedélyezése. A szolgáltatás az ügyfél telepíti a virtuális gépek ezek üzemeltetett adatközpontokban. Bár a virtuális gépek egy külső helyszíni adatközpontban találhatók, az IaaS-felhasználó rendelkezik a konfigurációs és felügyeleti közülük.

Azure virtuális gépeket, a virtuálisgép-méretezési csoportok és a kapcsolódó hálózati infrastruktúra több IaaS-megoldásokat tartalmazza. Virtuális gépek a népszerű választás az eredetileg áttelepítése az Azure szolgáltatások, mivel lehetővé teszi, hogy a "növekedési és az eltolás mértékét megadó" áttelepítési modell. Például az infrastruktúra jelenleg fut a szolgáltatás az adatközpontban található virtuális gép konfigurálása, és telepítse át a szoftver az új virtuális Gépet. Szükség lehet a konfigurációs frissítések, például az URL-címek más szolgáltatások vagy a tárolást, de ezzel a módszerrel több alkalmazásokat telepíthet át.

Virtuálisgép-méretezési csoportok Azure virtuális gépek platformra épülnek, és azonos virtuális fürtök üzembe helyezésekor könnyű megoldást biztosítson. Virtuálisgép-méretezési csoportok is támogatja az automatikus skálázás új virtuális gépekre telepíthető automatikusan szükség esetén. Ez megnehezíti a virtuálisgép-méretezési csoportok ideális platform magasabb szintű mikroszolgáltatási számítási gazdagépfürtökre, például az Azure Service Fabric és az Azure Tárolószolgáltatás.

## <a name="azure-services"></a>Azure-szolgáltatások

Azure a felhő számítási platform sok szolgáltatásokat biztosít. Ezek a szolgáltatások a következők:

### <a name="compute-services"></a>Számítási szolgáltatások

Szolgáltatások üzemeltetésére és alkalmazás-munkaterhelések futtatásával:

-   Az Azure Virtual Machines – Linux és a Windows

-   Alkalmazásszolgáltatások (Web Apps, mobilalkalmazások, a Logic Apps, az API Apps és függvény alkalmazások)

-   Az Azure Batch (a nagy méretű párhuzamos és kötegelt számítási feladat)

-   Azure Service Fabric

-   Azure Container Service

### <a name="data-services"></a>Adatszolgáltatások

Szolgáltatások tárolásához és adatok kezelése:

-   Az Azure Storage (az Azure Blob, a várólista, a tábla és a fájl szolgáltatások tartalmazza)

-   Azure SQL Database

-   Azure Cosmos DB

-   Microsoft Azure StorSimple

-   Azure Redis Cache

### <a name="application-services"></a>Alkalmazásszolgáltatások

Szolgáltatások létrehozása és alkalmazások működő:

-   Azure Active Directory (Azure AD)

-   Kapcsolódás Azure Service Bus elosztott rendszerek

-   Az Azure HDInsight big Data típusú adatok feldolgozására

-   Azure Schedulerrel

-   Az Azure Media Services

### <a name="network-services"></a>A hálózati szolgáltatások

Azure-ban, és az Azure és a helyszíni adatközpont között hálózati szolgáltatások:

-   Azure Virtual Network

-   Azure ExpressRoute

-   Azure által biztosított DNS-

-   Azure Traffic Manager

-   Azure Content Delivery Network

Az Azure-szolgáltatásokat a részletes dokumentációjáért lásd: [Azure szolgáltatás dokumentációja](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Az Azure kulcsfogalmak

### <a name="datacenters-and-regions"></a>Adatközpontokon és régiók


Azure a világszerte több régióban általánosan elérhető globális felhőalapú platformot. Ha egy szolgáltatás, alkalmazás vagy egy Azure-ban, meg kell adnia válasszon ki egy régiót. A kiválasztott régióban az alkalmazást futtató speciﬁc adatközpontban jelöli. További információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

Az Azure használatával beneﬁts egyike, hogy az alkalmazások különböző adatközpontokban a világ minden táján telepítheti. A választott régió aﬀect az alkalmazás teljesítményét is. Válasszon egy régiót, amely leginkább az ügyfelek számára, a hálózati kérelmek késés csökkentése érdekében közelebb van az optimális. Is kiválaszthatja egy régió egyes országokban az alkalmazás terjesztésére szolgáló a jogi követelmények teljesítéséhez.

### <a name="azure-portal"></a>Azure Portal


Az Azure portál egy webes alkalmazás, amely segítségével létrehozására, kezelésére, és távolítsa el az Azure-erőforrások és szolgáltatások. Az Azure-portál itt található: https://portal.azure.com. Egy testre szabható irányítópult és az Azure-erőforrások kezelése tooling tartalmaz. Számlázás és előfizetés információkat is biztosít. További információkért lásd: [Microsoft Azure portál áttekintése](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) és [kezelése Azure-erőforrások portálon keresztül](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Erőforrások

Azure-erőforrások az egyes számítási, hálózati, adatok vagy az Azure-előfizetés telepített szolgáltatásokat üzemeltető alkalmazás. Néhány közös erőforrások virtuális gépek, a storage-fiókok vagy az SQL-adatbázisok. Az Azure szolgáltatások gyakran több kapcsolódó Azure-erőforrások állnak. Például egy Azure virtuális gép közé tartozik a virtuális gép, a tárfiókot, a hálózati adapter és a nyilvános IP-cím. Ezek resourcres hozható létre, felügyelt, és egyesével vagy csoportosan törölt. Azure-erőforrások tartoznak, az útmutató későbbi részében részletesebben.

### <a name="resource-groups"></a>Erőforráscsoportok

Egy Azure erőforráscsoport egy olyan tároló, amely egy Azure megoldás kapcsolódó erőforrásokat tárol. Az erőforráscsoport a megoldás összes erőforrást, vagy csak a csoport kezelni kívánt erőforrások tartalmazhatnak. Azure erőforráscsoport-sablonok az útmutató későbbi részében részletesebben tartoznak.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

Az Azure Resource Manager sablon a JavaScript Object Notation (JSON) fájl, amely meghatározza egy vagy több erőforrásokat egy erőforráscsoportba telepítése. Telepített erőforrások közti függőségeket is meghatározza. Az útmutató későbbi részében részletesebben lásd Resource Manager-sablonok.

### <a name="automation"></a>Automatizálás


Mellett létrehozása, kezelése és törlése erőforrásokat az Azure portál használatával ezek a tevékenységek automatizálható PowerShell vagy az Azure parancssori felület (CLI) használatával.

**Azure PowerShell**

Az Azure PowerShell olyan modulok biztosító Azure kezelésére szolgáló parancsmagok készlete. A parancsmagok segítségével létrehozására, kezelésére, és távolítsa el az Azure-szolgáltatásokhoz. A parancsmagok segítségével konzisztens, megismételhető és hands-off központi telepítések érhető el. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/install-azurerm-ps) foglalkozó témakörben talál.

**Azure parancssori felületén**

Az Azure parancssori felület olyan eszköz, amely hozhat létre, kezelhetnek és Azure-erőforrások eltávolítása a parancssorból. Az Azure CLI Windows, Linux és Mac OS X érhető el. További információt és technikai részleteket [az Azure parancssori felület telepítése](/cli/azure/install-azure-cli.md).

**REST API-k** Azure épül, amely támogatja az Azure portál felhasználói felületének REST API-készlet. A REST API-k a legtöbb lehetővé teszi, hogy programozott módon kiépítése és kezelése az Azure-erőforrások és az alkalmazások az Internet használatára képes bármely eszközről is támogatottak. További információkért lásd: a [Azure REST SDK-referencia](https://docs.microsoft.com/rest/api/index).


## <a name="azure-subscriptions"></a>Azure-előfizetések


Az előfizetés egy Azure-szolgáltatások logikai csoportosítása, amely csatolva van egy Azure-fiókra. Azure-fiókot konfigurál egy több előfizetéssel is tartalmazhat. Az Azure szolgáltatások számlázási előfizetésenként alapon történik. Azure-előfizetések rendelkezik, az előfizetés teljes hozzáféréssel rendelkező fiókot a rendszergazdák és a szolgáltatás-rendszergazda, irányító keresztül az összes szolgáltatás az előfizetéshez. Rendszergazdák, valamint az egyes fiókok engedélyezhetők részletes vezérlés az Azure-erőforrások RBAC keresztül.

### <a name="select-and-enable-an-azure-subscription"></a>Válassza ki, és az Azure-előfizetés engedélyezése

Azure-szolgáltatások használata előtt kell egy előfizetést. Több előfizetés típusú érhetők el.

**Ingyenes fiók**: A hivatkozásra kattintva regisztrálhat egy ingyenes fiókot az érték a [Azure-webhelyen](https://azure.microsoft.com/). Ez lehetővé teszi a jóváírás az állomásokon 30 napos kipróbálásához az Azure-erőforrások bármely kombinációját. Ha a jóváírás összege túllépi a fiók fel van függesztve. A próbaidőszak végén a szolgáltatás le van szerelve, és nem fog többé működni. Használatalapú fizetéses előfizetésre bármikor frissítheti.

**MSDN előfizetések**: Ha MSDN-előfizetéssel rendelkezik, kap egy meghatározott RAM az Azure-kreditjeinek minden hónapban. Például, ha a Microsoft Visual Studio Enterprise MSDN-előfizetés, kapott \$az Azure-kreditjeinek havi 150.

Ha követel túllépi a szolgáltatás le vannak tiltva, amíg el nem kezdi a következő hónap. Kapcsolja ki a költségkeret maximumát, és adja hozzá a további költségeket használandó hitelkártya. Ezek a költségek némelyike vannak kedvezményes MSDN-fiókokat. Például kell fizetnie a Linux ár virtuális gépek Windows Server rendszert futtató, és nincs a nem Microsoft-kiszolgálók, például a Microsoft SQL Server kell külön fizetni. Így az MSDN fiókok ideális fejlesztési és tesztelési forgatókönyvekhez.

**BizSpark fiókok**: A Microsoft BizSpark program való indítások számos előnyt biztosít. Egyik ezen előny az, hogy a Microsoft-szoftverek fejlesztési és tesztkörnyezetek legfeljebb öt MSDN-fiókok hozzáférést. Havi 150 beolvasása az egyes öt MSDN néhánynak Azure-kreditjeinek, és kedvezményes több Azure-szolgáltatás, például a virtuális gépek kell fizetnie.

**Használatalapú fizetés**: az ehhez az előfizetéshez kell fizetnie a fiókhoz egy hitelkártyával vagy bankkártyával csatolásával használja. Ha egy szervezet, akkor is is jóvá kell hagyni a számlázásra.

**Nagyvállalati szerződés**: bizonyos számú szolgáltatások használata az Azure-ban a következő évben véglegesíteni egy nagyvállalati szerződéshez, és időben összeg kell fizetnie. A kötelezettségvállalás végrehajtott felhasznált az év során. Ha a vállalt összege túllépi meg kell fizetnie a túlhasználati elmaradt. A kötelezettségvállalás mennyiségétől függően kedvezményes az Azure-szolgáltatásoknak kap.



### <a name="grant-administrative-access-to-an-azure-subscription"></a>Adjon rendszergazdai hozzáférést a Azure-előfizetés


Több fiók rendszergazdai szerepkör érhetők el, és bármikor módosíthatja. Két fő szerepkörök a következők:

-   **Szolgáltatás-rendszergazda**: ezt a szerepkört Azure-szolgáltatások kezelésére jogosult. Alapértelmezés szerint engedélyezett a fiókadminisztrátora ugyanazt a fiókot a hozzáférést.

-   **Társadminisztrátoraként**: Ez a szerepkör hozzáfér a szolgáltatás-rendszergazdaként. Azonban az ezt a szerepkört nem módosítható az előfizetés társítása Azure címtárakba.

További információkért lásd: [hozzáadása vagy módosítása az Azure-rendszergazdai szerepkörök](../../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Számlázási tekintse meg az Azure-portálon


Azure használatával fontos része a számlázási adatokat megtekintésének képességét. Az Azure portál Azure számlázási adatokat részletes betekintést biztosít.

További információkért lásd: [az Azure számlázási számla és a napi használati adatok letöltése](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>API-k számlázási beszerezni a számlázási adatokat


Portálon is megtekintheti a számlázási a, a számlázási adatokat egy parancsfájl vagy a program az Azure számlázási REST API-k segítségével végezheti el:

-   Az Azure használati API segítségével a használati adatok beolvasása. A számlázási használati adatokat úgy finomhangolhatja, ha a kapcsolódó Azure-erőforrások címkézést. Például mindegyik erőforrás egy erőforráscsoportban található egy részleg vagy projekt nevét a címkét, és ezután nyomon követheti a kifejezetten a költségeit, hogy egy címke.

-   Az Azure arány kártya API segítségével listában az összes elérhető erőforrást, valamint a metaadatok és a díjszabási információkat ezeket az erőforrásokat.

További információkért lásd: [betekintést nyerhet a Microsoft Azure erőforrás-felhasználás](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>A díjszabási Számológép előrejelzési költség

Az Azure-ban minden egyes szolgáltatás díjszabása nem egyezik. Sok Azure-szolgáltatások adja meg a Basic, Standard és Premium rétegek. Általában minden szinthez tartozik több ára és teljesítménye szintet. Használatával a [online árképzési Számológép](http://azure.microsoft.com/pricing/calculator), létrehozhat árképzési becslése. A Számológép rugalmasan, a költség, amely egyetlen erőforrásra vagy rendelkező erőforrások csoportjának becslése tartalmazza.

### <a name="set-up-billing-alerts"></a>Számlázási értesítések beállítása

Miután telepítette az alkalmazáshoz vagy megoldáshoz az Azure-on, által küldött e-mailben elküldi Ha megközelítést, költségkeretek meg van adva a figyelmeztető riasztásokat hozhat létre. További információkért lásd: [beállítása a Microsoft Azure-előfizetések riasztások számlázási](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Azure Resource Manager

Az Azure Resource Manager egy telepítés, a felügyeleti és a szervezet Azure-erőforrások mechanizmust. Erőforrás-kezelő használatával összeállíthatja sok egyéni erőforrás egy erőforráscsoportban.

Erőforrás-kezelő is tartalmaz, amelyek lehetővé teszik a kapcsolódó erőforrások testre szabható telepítését és konfigurálását a központi telepítési lehetőségeket. Például erőforrás-kezelő használatával helyezhet üzembe egy alkalmazás, amely több virtuális gép egy terheléselosztó és egy SQL-adatbázis egyetlen egységként áll. A Resource Manager-sablon használatával történő központi telepítését fejleszt.

A Resource Manager számos előnyt kínál:

-   A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

-   Ismételten telepítheti a megoldás a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

-   Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

-   Meghatározhatja az erőforrások közti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

-   Alkalmazhat hozzáférés-vezérlés az összes szolgáltatáshoz az erőforráscsoportban mert RBAC natív módon integrálva van a felügyeleti platformba.

-   Az erőforrás, hogy logikusan rendszerezhesse az összes erőforrást az előfizetésében címkékkel láthatja.

-   Tisztázhatja a szervezete az azonos címkével rendelkező erőforrások csoportjának költségeinek megtekintésével.

### <a name="tips-for-creating-resource-groups"></a>Erőforráscsoport-sablonok létrehozásával kapcsolatos tippek


Az erőforráscsoportok kapcsolatos döntések van, ezek a tippek vegye figyelembe:

-   Egy erőforráscsoportban található összes erőforrást azonos életciklussal kell rendelkeznie.

-   Rendelhet egy erőforrás csak egyetlen csoport egyszerre.

-   Adja hozzá, vagy eltávolít egy erőforrást egy erőforráscsoportból, tetszőleges időpontban. Minden erőforrás egy erőforráscsoportban található kell tartoznia. Így ha egy erőforrás eltávolítása egy csoportból, hozzá kell adnia azt a másikra.

-   A legtöbb típusú erőforrás bármikor áthelyezheti egy másik erőforráscsoportban található.

-   Az erőforrások erőforráscsoportban különböző régiókban lehet.

-   Azt az erőforrások hozzáférés-vezérlésének használhatja egy erőforráscsoportot.

### <a name="building-resource-manager-templates"></a>Épület Resource Manager-sablonok

Resource Manager-sablonok deklaratív módon adja meg az erőforrások és a központilag telepített egyetlen erőforráscsoportként működnek az erőforrás-konfigurációk. Resource Manager-sablonok segítségével levezényelni a felesleges parancsfájlok vagy kézi konfigurálás nélkül összetett telepítések. Egy sablon fejlesztése, miután Ezután telepítheti azt többször – minden alkalommal, amikor azonos kimenetelére.

A Resource Manager-sablon négy részből áll:

-   **Paraméterek**: ezek a bemeneti adatokat a központi telepítéshez. A paraméterértékek megadható egy emberi vagy egy automatikus folyamat. Egy példa paraméter lehet egy rendszergazda felhasználó nevét és jelszavát a Windows virtuális gépek. A paraméterértékek használata a központi telepítés során, amikor beállítások van megadva.

-   **Változók**: ezek használhatók a központi telepítés során használt értékek tárolásához. Eltérően paraméterek egy változó értéke nincs megadva, a központi telepítéskor. Ehelyett már rögzített kódolt vagy dinamikusan generált.

-   **Erőforrások**: Ez a szakasz a sablon határozza meg az erőforrásokat a telepíthető, például a virtuális gépek, a storage-fiókok és a virtuális hálózatok.

-   **Kimeneti**: a központi telepítés befejeződését követően az erőforrás-kezelő adatok, például dinamikusan generált kapcsolati karakterláncok adhat vissza.

Az alábbi mechanizmusok érhetők el a központi telepítés automation:

-   **Funkciók**: több funkciókat használhatja a Resource Manager-sablonok. Ezek közé tartoznak az műveletek, például egy karakterláncot kisbetűssé, konvertálása, egy meghatározott erőforrás több példány telepítésével, és dinamikusan vissza a célként megadott erőforráscsoportja. Erőforrás-kezelő funkciók építhető dinamikus központi telepítések.

-   **Erőforrás-függőségek**: Ha több erőforrást, bizonyos erőforrások telepít lesz függőség a többi. Lehetővé teszi a központi telepítés, használhatja, hogy a függő erőforrások telepítése előtt a többi függőségi nyilatkozatot.

-   **Sablon linking**: A Resource Manager-sablon egy belül hozzákapcsolhatja egy másik sablont. Ez lehetővé teszi, hogy a célként megadott konkrét, Célspecifikus sablonok készletére a központi telepítés felbontás ellen.

Bármilyen szövegszerkesztővel Resource Manager-sablonok hozhat létre. Az Azure SDK for Visual Studio azonban eszközt tartalmaz. Visual Studio használatával erőforrások hozzáadása a sablont a varázsló, majd telepítheti és hibakeresése a Visual Studio közvetlenül a sablont. További információkért lásd: [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md).

Végezetül átalakíthatja meglévő erőforráscsoportok az Azure portálról újrafelhasználható sablont. Ez akkor lehet hasznos, ha azt szeretné, a központilag telepíthető egy meglévő erőforráscsoportot-sablonok létrehozásához, vagy szeretné megvizsgálja a mögöttes JSON. Erőforráscsoport exportálása, válassza ki a **automatizálási parancsfájl** gomb az erőforráscsoport beállításai közül.

## <a name="security-of-azure-resources-rbac"></a>Az Azure-erőforrások (RBAC)

A megadott hatókörben felhasználói fiókokhoz működési hozzáférést biztosíthat: előfizetés, erőforrás vagy az egyéni erőforrás. Ez azt jelenti, hogy egy erőforráscsoport, például a virtuális gép az erőforrások és az összes kapcsolódó erőforrások telepítheti, és a hozzáférési jogot egy adott felhasználó vagy csoport. Ez a módszer csak a célként megadott erőforráscsoportja tartozó erőforrásokhoz való hozzáférést korlátozza. A hozzáférést egyetlen erőforrásra, például egy virtuális gép vagy egy virtuális hálózatot.

Hozzáférés biztosításához meg rendelhet hozzá egy szerepkört a felhasználó vagy felhasználói csoport. Számos előre meghatározott szerepkör is létezik. A saját egyéni szerepkörök is meghatározhat.

Íme néhány példa szerepkörök Azure épített:

-   **Tulajdonos**: Ezzel a szerepkörrel rendelkező felhasználók mindent felügyelhetnek, beleértve a hozzáférést.

-   **Olvasó**: A felhasználói szerephez (titkos adatok) kivételével az összes típusú erőforrásokat tudja olvasni, de nem módosíthatja.

-   **Virtuális gép közreműködő**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a virtuális gépek, de nem tudja kezelni a virtuális hálózaton, amelyhez csatlakoznak, vagy a tárfiók, amelyben a VHD-fájl található.

-   **SQL DB contributor**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik SQL-adatbázisok, de nem a biztonsági házirendjét.

-   **SQL biztonságkezelő**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik az SQL Server-kiszolgálók és adatbázisok biztonsági házirendeket.

-   **Tárolási fiók közreműködői**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a storage-fiókok, de nem tudja kezelni a storage-fiókok hozzáférést.

További információkért lásd: [az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése a szerepkör-hozzárendelések segítségével](../../active-directory/role-based-access-control-configure.md).

## <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

Az Azure virtuális gépek egyike a központi IaaS-szolgáltatásokat az Azure-ban. Az Azure virtuális gépek támogatja a Windows vagy Linux rendszerű virtuális gépek telepítését egy Microsoft Azure-adatközpontban. Azure virtuális gépekkel, akkor teljes szabályozhatják a Virtuálisgép-konfiguráció és felelősek az összes szoftver telepítése, konfigurálása és karbantartása.

Amikor telepít egy Azure virtuális Gépen, kiválaszthatja a lemezkép az Azure piactérről, vagy megadhatja azt saját általánosított lemezkép. A lemezkép az operációs rendszer és a kezdeti konfigurációs alkalmazására szolgál. A telepítés során az erőforrás-kezelő bizonyos konfigurációs beállításokat, például a számítógép nevét, a rendszergazdai hitelesítő adatokat és a hálózati konfiguráció hozzárendeléséhez fogja kezelni. Az Azure virtuálisgép-bővítmények konfigurációk, mint például a Szoftvertelepítés, a víruskereső konfiguráció és a figyelési megoldásoknak további automatizálásához használhatja.

Számos különböző méretű virtuális gépeket hozhat létre. A virtuális gép méretét határozzák meg, hogy az erőforrás-elosztás, például a feldolgozás, a memória és a tárolási kapacitást. Bizonyos esetekben funkciók, például az RDMA-kompatibilis hálózati adapterek és SSD-lemezek közül csak bizonyos Virtuálisgép-méretek érhető el. Virtuálisgép-méretek és képességek teljes listáját lásd "Az Azure virtuális gépek méretei" [Windows](../../virtual-machines/windows/sizes.md) és [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Használati esetek

Mivel az Azure virtuális gépek teljes felügyeletet gyakorolhat konfigurációs biztosít, ideálisak széles körét, amelyek nem felelnek meg a PaaS modell kiszolgáló-munkaterhelések. Kiszolgáló-munkaterhelések például adatbázis-kiszolgálók (SQL Server, Oracle vagy MongoDB), Windows Server Active Directory, Microsoft SharePoint, és számos további lehetségessé válik a Microsoft Azure platformon futtatásához. Igény szerint áthelyezheti az ilyen munkaterhelések az egy helyszíni adatközpontot egy vagy több Azure-régiók, nagy mennyiségű újrakonfigurálás nélkül.

### <a name="deployment-of-virtual-machines"></a>Virtuális gépek telepítésének

Azure virtuális gépeken telepítheti az Azure portál használatával, az Azure PowerShell modulra automatizálás segítségével vagy automation a platformfüggetlen parancssori felület használatával.

**Portál**

Csak egy aktív Azure-előfizetéssel, és egy webes böngésző hozzáférést egy virtuális gépet az Azure portál segítségével történő telepítéséhez szükséges. Számos különböző operációsrendszer-lemezképek különböző konfigurációjú választhatja ki. A telepítés során az összes tárolási és hálózati követelménnyel vannak konfigurálva. További információkért lásd "A virtuális gép létrehozása az Azure portálon" [Windows](../../virtual-machines/windows/quick-create-portal.md) és [Linux](../../virtual-machines/linux/quick-create-portal.md).

Az Azure Resource Manager-sablon a portálról telepítése mellett az Azure-portálról egy virtuális gép telepítését. A központi telepítése és konfigurálása az összes erőforrást, a sablonban definiált. További információkért lásd: [erőforrások a Resource Manager-sablonok és az Azure-portál telepítése](../../azure-resource-manager/resource-group-template-deploy-portal.md).


**PowerShell**

PowerShell segítségével történő telepítéséhez egy Azure virtuális gépen lehetővé teszi minden kapcsolódó virtuális gép erőforrásai, beleértve a tárolási és hálózati teljes telepítési automatizálását. További információkért lásd: [erőforrás-kezelő és a PowerShell használatával Windows virtuális gép létrehozása](../../virtual-machines/windows/quick-create-powershell.md).

Mellett az Azure számítási erőforrásokat üzembe helyezi a külön-külön, az Azure PowerShell modul segítségével az Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](../../azure-resource-manager/resource-group-template-deploy.md).

**Parancssori felület (CLI)**

Csakúgy, mint a PowerShell-modult, az Azure parancssori felület telepítési automation biztosít, és a Windows, OS X vagy Linux rendszereken is használható. Ha az Azure parancssori felület a **vm Gyorslétrehozás** parancs, az összes kapcsolódó virtuálisgép-erőforrások (beleértve a tárolási és hálózatkezelési) és a virtuális gép maga vannak telepítve. További információkért lásd: [Linux virtuális gép létrehozása az Azure-ban a parancssori felület használatával](../../virtual-machines/linux/quick-create-cli.md).

Hasonlóképpen az Azure parancssori felület használatával az Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások a Resource Manager-sablonok és az Azure parancssori felület telepítése](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Hozzáférés és biztonság a virtuális gépek

A virtuális gépek használata az internetről van szükség a társított hálózati interfész vagy terheléselosztóhoz, ha lehetséges, egy nyilvános IP-címmel kell konfigurálni. A nyilvános IP-cím egy DNS-nevet, amely hárítsa el a virtuális géphez, vagy belső terheléselosztót tartalmaz. További információkért lásd: [IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

A virtuális géphez való hozzáférés során a nyilvános IP-cím segítségével kezeli a hálózati biztonsági csoport (NSG) erőforrás. Egy NSG például egy tűzfal működik, és lehetővé teszi, vagy letiltja a forgalom a hálózati adapter vagy a meghatározott portok a megfelelő alhálózati. Például egy Azure virtuális gép távoli asztali munkamenetet létrehozni, kell az NSG bejövő forgalmat engedélyezi a 3389-es port konfigurálása. További információkért lásd: [egy virtuális gép portok megnyitása az Azure-ban az Azure-portálon](../../virtual-machines/windows/nsg-quickstart-portal.md).

Végezetül csakúgy, mint a felügyeleti bármely számítógépes rendszer, akkor biztosítania kell biztonsági egy Azure virtuális gépen, az operációs rendszer hitelesítő adatokat és a tűzfal szoftver használatával.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage egy Microsoft által felügyelt szolgáltatás biztosítja a tartós, méretezhető és redundáns tárolás. Adhat hozzá Azure-tárfiók erőforrásként bármely erőforráscsoport bármelyik erőforrás központi telepítési módszer használatával. Azure a következő négy tárolási: Blob-tároló, a File Storage, Table storage és a Queue storage. A storage-fiók telepítésekor két fióktípus érhető el, az általános célú és blob-tároló. Általános célú tárfiókot hozzáférést biztosító mindegyik tárolási típusát. BLOB storage-fiókok hasonlóak általános célú, de és meleg hozzáférési rétegek tartalmazó speciális blobot tartalmaz. A blob-tároló további információkért lásd: [Azure Blob Storage tárolóban](../../storage/blobs/storage-blob-storage-tiers.md).

Az Azure storage-fiókok konfigurálható különböző szintű redundancia:

-   **Helyileg redundáns tárolás** magas rendelkezésre állást biztosít, hogy az összes adat három példányban végrehajtott szinkron módon előtt írási akkor számít elértnek sikeres biztosításával. Egy régió egyetlen létesítményén másolatait tárolják. A replikák külön tartalék tartományokban találhatók, és a frissítési tartományt. Ez azt jelenti, hogy az adatok érhető el, akkor is, ha a tárolási csomópontnak, hogy az adatokat sikertelen rendelkező vagy frissítendő offline állapotba kerül.

-   **Georedundáns tárolás** az adatok három szinkron másolatot készít az elsődleges régióban, a magas rendelkezésre állású, ezért úgy majd aszinkron módon három replikák vész-helyreállítási párosított régióban.

-   **Írásvédett georedundáns tárolás** georedundáns tárolás és a másodlagos régióba adatainak olvasás. Ez a lehetőség kiválóan alkalmas a részleges katasztrófa utáni helyreállítás. Ha az elsődleges régióban problémáját, módosíthatja az alkalmazás csak olvasási hozzáféréssel rendelkeznek a párosított régióban.

### <a name="use-cases"></a>Használati esetek 

Minden tárolási különféle használati eset rendelkezik.

**Blob Storage** 

A word *blob* rövidítése: *nagy bináris objektum*. Blobok olyan strukturálatlan fájlokban, például az alábbiakhoz a számítógépén tárolni. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható. Az Azure Blob storage az Azure virtuális gépek adatlemezek is tartalmazza.

Az Azure Storage háromféle blobot támogatja:

-   **Blokkblobokat** elhelyezésére szolgáló beállítás fájlok 195 GB (4 MB x 50 000 blokk) mérete. Az elsődleges használati eset a blokkblobokhoz pedig a tárhely az elejétől a végéig, például médiafájlok vagy képfájlok webhelyekhez olvasni-fájlokat. Mivel 64 MB-nál nagyobb fájlokat fel kell tölteni, mint kis blokkok blokkblobokat néven szerepelnek. Ezek a blokkok majd konszolidált (vagy véglegesített) azokat a végső blob.

-   **Lapblobok** elhelyezésére szolgáló elérésű fájlok akár 1 TB-nál. A VHD-k, amelyek tartós lemezek biztosítanak az Azure virtuális gépek lapblobokat szolgálnak elsősorban a biztonsági tárolóként, az infrastruktúra-szolgáltatási számítási szolgáltatás az Azure-ban. Mivel az 512 bájtos lapok véletlenszerű olvasási/írási hozzáférést nyújtanak lapblobokat néven szerepelnek.

-   **Hozzáfűző blobokat** áll blokkok blokkblobokhoz hasonló, de vannak optimalizálva műveletek hozzáfűzésére. Ezek gyakran használják az azonos blob egy vagy több forrásból származó naplózási adatok. Például előfordulhat, hogy írt összes a nyomkövetési adatainak naplózása az ugyanazon a hozzáfűző blob több virtuális gépeken futó alkalmazás. Egy hozzáfűző blob akár 195 GB lehet.

További információkért lásd: [az Azure Blob storage .NET használatának első lépései](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

**A File storage**

Az Azure File storage olyan szolgáltatás, amely a felhőben fájlmegosztásokat kínál a szabványos Server Message Block (SMB) protokoll használatával. A szolgáltatás támogatja az SMB 2.1 és az SMB 3.0. Az Azure File storage alkalmazások az Azure fájlmegosztások gyorsan és költséges újraírások nélkül telepítheti át. Az Azure virtuális gépeken futó alkalmazások, a felhőalapú szolgáltatások, vagy a helyszíni ügyfelek csatlakoztathatnak fájlmegosztást a felhőben. Ez hasonlít hogyan egy asztali alkalmazás csatlakoztatja SMB-megosztáshoz. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Mivel a File storage-megosztás egy szabványos SMB-fájlmegosztás, az Azure-ban futó alkalmazások férhetnek a fájlrendszer adatátviteli API-jain keresztül a megosztás adataihoz. A fejlesztők ezért használhatja a meglévő kódjaik és képességeik felhasználásával áttelepíthetik az alkalmazásokat. Informatikai szakemberek a PowerShell-parancsmagok segítségével hozzon létre, csatlakoztathatják és File storage-megosztásokat kezelése az Azure-alkalmazások felügyeleti részeként.

További információkért lásd: [Ismerkedés az Azure File storage on Windows](../../storage/files/storage-how-to-use-files-windows.md) vagy [Azure File storage használata Linux](../../storage/files/storage-how-to-use-files-linux.md).

**Table Storage**

Az Azure Table Storage egy olyan szolgáltatás, amely strukturált NoSQL-adatokat tárol a felhőben. A TABLE storage séma nélküli kialakítás egy kulcs-/ attribútumtár. Mivel a Table storage séma nélküli, az alkalmazás igényeinek változásával igényeinek könnyen adatait. Az adatok hozzáférése gyors és költséghatékony, bármilyen alkalmazásról legyen is szó. Hasonló adatmennyiséggel számolva a Table Storage általában határozottan kevesebb költséggel jár, mint egy hagyományos SQL.

A Table Storage segítségével rugalmas adatkészleteket tárolhat, például webalkalmazások felhasználói adatait, címtárakat, eszközadatokat és bármilyen egyéb metaadatot, amelyre a szolgáltatásnak szüksége van. Egy tábla korlátlan számú entitást tárolhat. A storage-fiók korlátlan számú táblát, a tárfiók a kapacitásán tartalmazhat.

További információkért lásd: [Ismerkedés az Azure Table storage](../../cosmos-db/table-storage-how-to-use-dotnet.md).

**Queue Storage**

Az Azure Queue Storage felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások kialakításában, alkalmazás-összetevői gyakran le vannak választva, hogy lehessen őket méretezni egymástól függetlenül. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

További információkért lásd: [Ismerkedés az Azure Queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>A storage-fiók üzembe helyezése

A storage-fiók üzembe helyezésének több lehetőség áll rendelkezésre.

**Portál**

Csak egy aktív Azure-előfizetéssel, és egy webes böngésző hozzáférést az Azure portál segítségével történő telepítéséhez a storage-fiók szükséges. Új tárfiók be egy új vagy meglévő erőforráscsoportot is telepíthet. Miután létrehozta a tárfiókot, a portál használatával is létrehozhat egy blob tároló vagy a fájlmegosztásnak. Tábla létrehozása, és a tárolási entitások programozott módon várólistára. További információkért lásd: [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

Telepítése az Azure-portálról egy tárfiókot, mellett telepítheti egy Azure Resource Manager-sablon a portálon. A központi telepítése és konfigurálása összes erőforrást a sablonban, beleértve a storage-fiókok meghatározott. További információkért lásd: [erőforrások a Resource Manager-sablonok és az Azure-portál telepítése](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Egy Azure storage-fiók üzembe helyezése a PowerShell használatával lehetővé teszi, hogy a teljes telepítési automatizálásához a tárfiók. További információkért lásd: [Azure PowerShell használata az Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Amellett, külön-külön telepítésére használható Azure-erőforrások, az Azure PowerShell modul segítségével az Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](../../azure-resource-manager/resource-group-template-deploy.md).

**Parancssori felület (CLI)**

Csakúgy, mint a PowerShell-modult, az Azure parancssori felület telepítési automation biztosít, és a Windows, OS X vagy Linux rendszereken is használható. Használhatja az Azure parancssori felület **storage-fiók létrehozása** parancs futtatásával hozzon létre egy tárfiókot. További információkért lásd: [az Azure Storage az Azure parancssori felület használatával.](../../storage/common/storage-azure-cli.md)

Hasonlóképpen az Azure parancssori felület használatával az Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások a Resource Manager-sablonok és az Azure parancssori felület telepítése](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Az Azure Storage Access és

Az Azure Storage számos lehetőséget kínál, ha az Azure-portálon, beleértve a virtuális gép létrehozása és a művelet során, és a Storage ügyfélkódtáraival érhető el. 

**Virtuális gépek lemezei**

Amikor telepít egy virtuális gépet, akkor is meg kell ahhoz, hogy a virtuális gép operációsrendszer-lemez, és bármelyik adatlemeznek storage-fiók létrehozása. Válasszon egy meglévő tárfiókot, vagy hozzon létre egy újat. A blob maximális mérete 1024 GB, mert egyetlen lemezt a virtuális gép rendelkezik 1,023 GB maximális mérettel. Nagyobb adatlemezt konfigurálásához a virtuális gép lemezét több adat, és a tárolókészlet azokat együtt, egyetlen logikai lemez. További információkért lásd: "Manage Azure-lemezeket" a [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) és [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

**Storage-eszközök**

Az Azure storage-fiókok számos különböző tártallózók, például a Visual Studio Cloud Explorer is elérhetőek. Ezek az eszközök lehetővé teszik, hogy a Tallózás storage-fiókok és az adatok között. További információ és a rendelkezésre álló tár szoftverkategóriák listáját lásd: [Azure Storage ügyféleszközök elől](../../storage/common/storage-explorers.md).

**Storage API**

Bármely olyan nyelvvel, amelyekkel HTTP/HTTPS-kéréseket a tárolási erőforrások elérhetők legyenek. Ezenfelül az Azure Storage számos népszerű nyelvhez biztosít programozási kódtárakat. Ezeket a kódtárakat az Azure Storage egyszerűbbé kezelése részletei, például a szinkron és aszinkron hívás, műveletek, kivételek kezelése és automatikus újrapróbálkozások kötegelés. További információkért lásd: [Azure Storage szolgáltatás REST API-referenciában](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

**Tárelérési kulcsok**

Minden tárfiók két hitelesítési kulcsokat, egy elsődleges és másodlagos rendelkezik. Vagy a storage access műveletekhez használható. Ezek a kulcsok tárolási biztonságossá tárfiókot használja, és programokon keresztül az adatok eléréséhez szükséges. Engedélyezi a megfelelő biztonság érdekében a kulcsok alkalmi helyettesítő két kulcs van. Nagyon fontos a biztonságos kulcsok, mivel a fiók neve, valamint rendelkezésükre adatokat korlátlan hozzáféréssel a tárfiókban.

**Megosztott hozzáférési aláírásokkal**

Ha a felhasználó rendelkezik a tárolási erőforrásokhoz való hozzáférés ellenőrzött van szüksége, létrehozhat egy közös hozzáférésű jogosultságkódot. A közös hozzáférésű jogosultságkód egy jogkivonatot, amely meghatalmazott hozzáférést biztosít a tároló egyik erőforrásához URL-Címeket is bővül. Bárki, aki birtokában lehet az erőforrás elérésére, kódban az engedélyeket, hogy meghatározza, az az időtartam, általa tartozó érvényes. További információkért lásd: [használata közös hozzáférésű jogosultságkód](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network


Virtuális hálózatok virtuális gépek közötti kommunikáció támogatásához szükséges. Adja meg az alhálózat, a egyéni IP-cím, a DNS-beállítások, a biztonsági szűrést, és a terheléselosztás. VPN-átjáró vagy ExpressRoute-kapcsolatcsoportot segítségével, az Azure virtuális hálózatokhoz csatlakozhat a helyszíni hálózatokhoz.

### <a name="use-cases"></a>Használati esetek

Nincsenek Azure hálózati különféle használati eseteket.

**Csak felhőalapú virtuális hálózatok**

Egy Azure virtuális hálózatra, alapértelmezés szerint csak az Azure-ban tárolt erőforrások érhető el. Ugyanahhoz a virtuális hálózathoz csatlakoztatott kommunikálhatnak egymással. Társítsa a virtuális gépek hálózati illesztői, és terheléselosztók nyilvános IP-cím ahhoz, hogy a virtuális gép elérhető az interneten keresztül. Biztonságos hozzáférést a hálózati biztonsági csoportok használatával segíthet az nyilvánosan elérhetővé erőforrásokat.

**Virtuális hálózatok létesítmények közötti**

Csatlakozhat egy helyi hálózaton egy Azure virtuális hálózatra expressroute-on vagy a pont-pont VPN-kapcsolat használatával. Ebben a konfigurációban az Azure virtuális hálózat lényegében egy felhőalapú bővítmény a helyszíni hálózat.

Mivel az Azure virtuális hálózat a helyszíni hálózathoz csatlakozik, létesítmények közötti virtuális hálózatok kell használnia a címtér a szervezet által használt egyedi része. Azonos módon, hogy a vállalat különböző helyszínein vannak hozzárendelve a megadott IP-alhálózat Azure kiterjesztheti a hálózatát, lesz egy másik helyre.

###<a name="deploying-a-virtual-network"></a>Virtuális hálózat üzembe helyezése

A virtuális hálózat üzembe helyezésekor számos lehetőség áll rendelkezésre.

**Portál**

Egy Azure virtuális hálózatot az Azure portál segítségével történő telepítéséhez szükséges csak egy aktív Azure-előfizetéssel, és egy webes böngésző hozzáférést. Telepíthet új virtuális hálózat be egy új vagy meglévő erőforráscsoportot. Új virtuális gép létrehozásakor a portálról válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat. További információkért lásd: [hozzon létre egy virtuális hálózatot az Azure portál használatával](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

Azure-portálról egy Azure virtuális hálózatra való telepítése mellett telepítheti egy Azure Resource Manager-sablon a portálon. Ez fogja telepíteni, és minden erőforrások konfigurálása az a sablon, beleértve a virtuális hálózati erőforrásokhoz. További információkért lásd: [erőforrások a Resource Manager-sablonok és az Azure-portál telepítése](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Egy Azure virtuális hálózat üzembe helyezése a PowerShell használatával lehetővé teszi, hogy a teljes telepítési automatizálásához a tárfiók. További információkért lásd: [virtuális hálózat létrehozása a PowerShell használatával](../../virtual-network/virtual-networks-create-vnet-arm-ps.md).

Amellett, külön-külön telepítésére használható Azure-erőforrások, az Azure PowerShell modul segítségével az Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](../../azure-resource-manager/resource-group-template-deploy.md).

**Parancssori felület (CLI)**

Csakúgy, mint a PowerShell-modult, az Azure parancssori felület telepítési automation biztosít, és a Windows, OS X vagy Linux rendszereken is használható. Használhatja az Azure parancssori felület **hálózati vnet létrehozása** parancs futtatásával hozzon létre egy virtuális hálózatot. További információkért lásd: [virtuális hálózat létrehozása az Azure parancssori felület használatával](../../virtual-network/virtual-networks-create-vnet-arm-cli.md).

Hasonlóképpen az Azure parancssori felület használatával az Azure Resource Manager-sablon üzembe helyezése. További információkért lásd: [erőforrások a Resource Manager-sablonok és az Azure parancssori felület telepítése](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-networks"></a>Hozzáférés és biztonság a virtuális hálózatok

Biztonságos egy Azure virtuális hálózatot a hálózati biztonsági csoportok használatával segíthet. Az NSG-k hozzáférés-vezérlési lista (ACL) szabályok, amelyek engedélyezik vagy megtagadják a Virtuálisgép-példány egy virtuális hálózat hálózati forgalmának listáját tartalmazzák. NSG-ket társíthat ket alhálózatokhoz vagy az alhálózaton belüli Virtuálisgép-példányokhoz. Amikor társít egy NSG alhálózattal, az ACL szabályok érvényessé alhálózat összes Virtuálisgép-példány. Emellett tovább korlátozhatja forgalmat egy adott virtuális gépre NGS társítása közvetlenül az adott virtuális gép által. További információkért lásd: [hálózati forgalmat hálózati biztonsági csoportokkal](../../virtual-network/virtual-networks-nsg.md).

## <a name="next-steps"></a>Következő lépések

- [Windows virtuális gép létrehozása](/virtual-machines/windows/quick-create-portal.md)
- [Linux virtuális gép létrehozása](../../virtual-machines/linux/quick-create-portal.md)
