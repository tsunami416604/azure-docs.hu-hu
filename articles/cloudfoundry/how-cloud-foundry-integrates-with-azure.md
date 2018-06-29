---
title: Hogyan integrálható a felhőalapú Foundry Azure |} Microsoft Docs
description: Ismerteti, hogyan felhő Foundry is utlize Azure szolgáltatásokat a Enterprice javítása érdekében
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 0959617185694d48c593996d5cd8c836098dd1cd
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062206"
---
# <a name="integrate-cloud-foundry-with-azure"></a>A Cloud Foundry Azure-ral való integrálása

[A felhő Foundry](https://docs.cloudfoundry.org/) egy felhőbeli szolgáltatók IaaS platform felett futó PaaS platform. Különböző szolgáltatók kínál konzisztens alkalmazás üzembe helyezését. Ezenkívül is integrálása különböző Azure-szolgáltatások enterprise osztályú magas rendelkezésre ÁLLÁSÚ, méretezhetőségi és költségmegtakarításhoz.
Nincsenek [a felhő Foundry 6 alrendszerek](https://docs.cloudfoundry.org/concepts/architecture/), amely lehet rugalmasan méretezési online, beleértve: útválasztási, hitelesítési, alkalmazáskezelés életciklusát, szolgáltatás-felügyeleti üzenetküldési és figyelését. Az egyes a alrendszereket konfigurálhatja a felhő Foundry kapcsolattartó Azure szolgáltatás használatára. 

![Felhő Foundry Azure integrációs architektúra](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Magas rendelkezésre állás és méretezhetőség
### <a name="managed-disk"></a>Felügyelt lemezes
Bosh Azure CPI (felhő felület) használja, a lemez létrehozása és törlése rutinok. Alapértelmezés szerint nem felügyelt lemezek használhatók. Manuálisan tárfiókok létrehozása, majd a fiókok beállítása CF fájlok az ügyfél igényel. A tárfiók / lemezek számát korlátozásai miatt nem.
Most [kezelt lemez](https://azure.microsoft.com/services/managed-disks/) érhető el, a virtuális gépek a biztonságos és megbízható felügyelt lemezes tárterületet biztosít. Ügyfél már nincs szüksége a tárfiókot, a méretezés és magas rendelkezésre ÁLLÁSÚ kezelésére. Azure automatikusan rendezi a lemezeket. Hogy egy új vagy meglévő telepítéshez, a Azure CPI fogja kezelni a létrehozása vagy áttelepítése a felügyelt lemezes CF központi telepítés során. Az PCF 1.11 használata támogatott. A nyílt forráskódú felhő Foundry is felfedezhet [kezelt lemez útmutatást](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) referenciaként. 
### <a name="availability-zone-"></a>Rendelkezésre állási zóna *
A felhő-natív alkalmazás platformként felhő Foundry kialakítása során [négy magas rendelkezésre állási szintet](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Az első három szintek szoftver hibák kezelhetik CF rendszert illeti, amíg platform hibatűrést szolgáltatók által biztosított. A legfontosabb CF összetevők magas rendelkezésre ÁLLÁSÚ megoldás egy felhőszolgáltató platformon kell védeni. Ez magában foglalja a GoRouters, Diego Brains, CF adatbázisának és csempék. Alapértelmezés szerint [Azure rendelkezésre állási csoport](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) szolgál a hibatűrés érdekében a fürt egy adott adatközpont között.
Az új jó, [Azure rendelkezésre állási zóna](https://docs.microsoft.com/azure/availability-zones/az-overview ) megjelenik, a következő szintre hibatűrést kapcsolásának kis késleltetésű redundanciával adatközpontok között.
Azure rendelkezésre állási zóna éri el a magas rendelkezésre ÁLLÁSÚ virtuális gépek halmaza 2 + adatközpontokban történő elhelyezésével, minden egyes virtuális gépek készletét redundáns más részhalmazához. Ha egy zóna nem működik, a más készletek még élő, különítve a katasztrófa.
> [!NOTE] 
> Azure rendelkezésre állási zóna nem ajánlja fel minden régióban még, tekintse meg a legutóbbi [közlemény listája a támogatott régiók](https://docs.microsoft.com/azure/availability-zones/az-overview). Nyissa meg a forrás felhő Foundry ellenőrizze [Azure rendelkezésre állási zóna nyílt forráskódú felhő Foundry útmutatót](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Hálózati útválasztás
Alapértelmezés szerint az alapszintű Azure terheléselosztó a bejövő kéréseket, CF API-alkalmazások továbbítja azokat a Gorouters szolgál. CF összetevők, például a Diego agy, MySQL, Beszúrása segítségével is a terheléselosztó a forgalmat osztani a magas rendelkezésre ÁLLÁSÚ. Emellett Azure biztosít teljes körűen felügyelt terheléselosztási megoldások. Ha a TLS-záráshoz ("SSL kiszervezési"), vagy a HTTP/HTTPS Kérelemfeldolgozás alkalmazás réteg, fontolja meg az Alkalmazásátjáró. Magas rendelkezésre állás és méretezhetőség terheléselosztási réteg 4 fontolja meg szabványos terheléselosztóhoz.
### <a name="azure-application-gateway-"></a>Az Azure Alkalmazásátjáró *
[Az Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) lehetőségekről, beleértve a történő kiszervezésével a végpontok közötti SSL, webalkalmazási tűzfal, a munkamenet cookie-alapú kapcsolat és több SSL terheléselosztási különböző réteg 7 kínál. Is [Application Gateway konfigurálásához a nyitott forrás felhő Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). A PCF, tekintse meg a [PCF 2.1 kibocsátási megjegyzések](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) a Koncepció teszteléséhez.

### <a name="azure-standard-load-balancer-"></a>Az Azure szabványos terheléselosztó *
Az Azure terheléselosztó a réteg 4 terheléselosztó. A forgalom egy elosztott terhelésű készlet szolgáltatáspéldányok között terjeszteni szolgál. A standard verzió biztosít [speciális szolgáltatások](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) az alapszintű verzióra. Például: 1. A háttér-készlet maximális értéket 100 jelenik meg az 1000 virtuális gépen.  2. A végpontok mostantól támogatják az egyetlen rendelkezésre állási csoport helyett több rendelkezésre állási csoporttal.  3. További funkciók, például magas rendelkezésre ÁLLÁSÚ portok, gazdagabb figyelési adatok, stb. Ha az Azure rendelkezésre állási zónához, a standard terheléselosztó megadása kötelező. Egy új központi telepítés kapcsán azt javasoljuk, hogy Azure Standard terheléselosztó kezdődhet. 

## <a name="3-authentication"></a>3. Hitelesítés 
[Felhő Foundry felhasználói fiók és a hitelesítési](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) CF és annak különböző összetevői az központi identity management szolgáltatás. [Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) a Microsoft több-bérlős, felhőalapú címtár- és identitáskezelési felügyeleti szolgáltatás. Alapértelmezés szerint UAA felhő Foundry hitelesítéshez használt. Speciális lehetőségként UAA is támogatja az Azure AD külső felhasználók tárolóként történő. Az Azure Active Directory-felhasználók férhetnek hozzá az LDAP identitásával felhő Foundry-fiók nélkül felhő Foundry. Az alábbi lépéseket követve [az Azure AD konfigurálása UAA PCF](http://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Adattárolás felhő Foundry futásidejű rendszere
Felhő Foundry kiváló bővítési Azure blobtárhely vagy alkalmazás futásidejű rendszerhez szükséges tárhelyet MySQL/PostgreSQL/Azure-szolgáltatások használatához nyújt.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>A felhő Foundry felhő vezérlő blobtárhely Azure Blobtárhely
A felhő vezérlő blobtárhely buildpacks, feldolgozó programok, csomagok és az erőforráskészletek a kritikus tárolóban. NFS-kiszolgáló alapértelmezés szerint felhő vezérlő blobtárhely szolgál. Kerülje a hibaérzékeny pontok kialakulását, használja az Azure Blob Storage külső tárolóként. Tekintse meg a [felhő Foundry dokumentáció](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) a háttérben, és [döntő felhő Foundry beállítások](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL felhő Foundry rugalmas, futtassa az idő adatbázis *
Rugalmas futásidejű CF két fő rendszeradatbázisokban igényel:
#### <a name="ccdb"></a>CCDB 
A felhő tartományvezérlő-adatbázis.  Felhő vezérlő biztosít az ügyfelek hozzáférhetnek a rendszerhez REST API-végpontokon. CCDB szervezethez, szóközöket, szolgáltatások, a felhasználói szerepkörök és a felhő vezérlő több táblázatok tárolja.
#### <a name="uaadb"></a>UAADB 
Az adatbázis felhasználói fiók és a hitelesítéshez. A felhasználói hitelesítés kapcsolódó tárol adatokat, például a felhasználói neveket és jelszavakat titkosítja.

Alapértelmezés szerint a helyi rendszeradatbázis (MySQL) is használható. A magas rendelkezésre ÁLLÁSÚ és a skála, kihasználhatja az Azure felügyelt MySQL vagy PostgreSQL-szolgáltatások. Ez az utasítás [engedélyezése az Azure-beli MySQL/PostgreSQL CCDB, UAADB és egyéb nyitott forrás felhő Foundry rendszer adatbázisokkal](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Nyissa meg a Service Broker
Az Azure service broker az Azure-szolgáltatásokhoz való alkalmazás-hozzáférés kezelése egységes felületet biztosít. Az új [Azure-projekt megnyitása Service Broker](https://github.com/Azure/open-service-broker-azure) szolgáltatások nyújtásához alkalmazások felhő Foundry, OpenShift és Kubernetes egyetlen és egyszerű módot nyújt. Tekintse meg a [PCF csempe Azure nyitott Service Broker](https://network.pivotal.io/products/azure-open-service-broker-pcf/) PCF az üzembe helyezési utasítások.

## <a name="6-metrics-and-logging"></a>6. Metrikák és naplózás
Az Azure napló Analytics dugulásellenőrzési összetevő felhő Foundry, amely továbbítja a metrikák a [felhő Foundry loggregator "firehose"](https://docs.cloudfoundry.org/loggregator/architecture.html) való [Azure Naplóelemzés](https://azure.microsoft.com/services/log-analytics/). A fúvóka rendelkező gyűjtése, megtekintése és elemzése a CF rendszer állapotának és teljesítményének metrikákat több központi telepítések egységességét.
Kattintson a [Itt](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) megtudhatja, hogyan telepítheti az Azure napló Analytics dugulásellenőrzési nyílt forráskódú és döntő felhő Foundry környezetben is, és majd érik el az adatokat az Azure napló Analytics OMS-konzolról. 
> [!NOTE]
> PCF 2.0 BOSH állapotfigyelő metrikák virtuális gépek esetén a rendszer továbbítja a Loggregator "firehose" alapértelmezés szerint, és integrálva vannak az Azure napló Analytics OMS-konzol.

## <a name="7-cost-saving"></a>7. Költség mentése
### <a name="cost-saving-for-devtest-environments"></a>Fejlesztési/tesztelési környezetben költségmegtakarítást
#### <a name="b-series-"></a>B sorozatú: *
Amíg F és D VM sorozat gyakran javasolt döntő felhő Foundry éles környezet érdekében az új "burstable" [B sorozatú](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) számos lehetőséget kínál az új beállítások. A B sorozatnak burstable virtuális gépek ideálisak munkaterhelések esetén, amelyek nem kell a Processzor teljes teljesítményének folyamatosan, például a webkiszolgálók, a kis adatbázisok és a fejlesztési és tesztelési környezetben. Az ilyen terhelések általában burstable teljesítmény követelményekkel rendelkezik. $ 0,012/óra (B1) $0,05 óránként (F1) képest, a teljes listájának [Virtuálisgép-méretek](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-general) és [árak](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) részleteiről. 
#### <a name="managed-standard-disk"></a>Standard szintű felügyelt lemezes: 
Prémium szintű lemezekhez megbízható teljesítmény éles környezetben javasolt.  A [kezelt lemez](https://azure.microsoft.com/services/managed-disks/), Standard szintű tárolást is biztosíthat más-más teljesítménybeli, hasonló megbízhatóságát. Amely nincs teljesítmény érzékeny terheléshez, például a fejlesztési és tesztelési célú vagy a nem kritikus környezetben felügyelt standard lemezek kínálnak, alacsonyabb költségű másik lehetséges.  
### <a name="cost-saving-in-general"></a>Általában mentése költsége 
#### <a name="significant-vm-cost-saving-with-reserved-instances"></a>Jelentős VM költsége mentése fenntartott osztályt: 
A mai CF virtuális gépeinek számlázása, használja az "igény" árképzési, annak ellenére, hogy a környezetben be általában határozatlan ideig maradnak. Most tartalékkapacitás 1-es vagy 3 éves távon a virtuális gép, és kedvezmények 45-65 % kapnak. A számlázási rendszer, az a környezetben nem történtek változások alkalmazzák. További információkért lásd: [hogyan fenntartott példányok works](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Prémium szintű lemez, amelyik kisebb méretű kezelése: 
Lemezek támogatási kisebb lemezméret, például a P4(32 GB) és a prémium és standard lemezek P6(64 GB) kezeli. Ha kis munkaterhelés, költség mentheti felügyelt prémium szintű lemezekhez standard premium lemezek áttelepítése.
#### <a name="utilizing-azure-first-party-services"></a>Az Azure első entitás szolgáltatások használata: 
Azure első szolgáltatás előnyeit, csökkenti a költségeket, magas rendelkezésre ÁLLÁSÚ és a fenti szakaszokban megbízhatóság mellett a hosszú távú felügyeleti. 

Döntő elindított egy [kis erőforrásigényét Beszúrása](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) PCF ügyfeleknek, az összetevők vannak közös elhelyezésű csupán 4 futó virtuális gépeket, akár 2500 alkalmazáspéldányok be. A próbaverzió mostantól keresztül [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>További lépések
Az Azure integrációs szolgáltatások válnak elérhetővé a [nyitott forrás felhő Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), mielőtt az elérhető a felhő döntő Foundry. Szolgáltatások jelölésű * PCF keresztül nem továbbra is elérhetők. Felhő Foundry integrálása Azure verem nem vagy jelez ebben a dokumentumban.
A megjelölve PCF támogatását a szolgáltatások a *, vagy felhő Foundry integrálása Azure verem, forduljon a Pivotal és a Microsoft-fiókkezelő legutóbbi állapota. 

