---
title: A Cloud Foundry az Azure-ral együttműködéséről |} A Microsoft Docs
description: Ismerteti, hogyan képes használni a Cloud Foundry Azure-szolgáltatások a Enterprice élmény
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
ms.openlocfilehash: 908b7e40c0509d7034b86985ac0775635726a6b9
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329803"
---
# <a name="integrate-cloud-foundry-with-azure"></a>A Cloud Foundry Azure-ral való integrálása

[Cloud Foundry](https://docs.cloudfoundry.org/) egy PaaS-platformra épülő felhőalapú szolgáltatók IaaS-platformon futó. A felhőszolgáltatók konzisztens alkalmazás központi telepítési élmény, kínál. Emellett is integrálható különböző Azure-szolgáltatásokat a magas rendelkezésre ÁLLÁSÚ, nagyvállalati szintű skálázhatóságot és költségek.
Nincsenek [a Cloud Foundry 6 alrendszerek](https://docs.cloudfoundry.org/concepts/architecture/), amely lehet rugalmas méretezési online, többek között: Útválasztás, a hitelesítés, alkalmazás-életciklus-kezelést, a Service management, üzenetkezelés és figyelése. Az alrendszerek mindegyike esetében konfigurálhatja úgy a Cloud Foundry kapcsolattartó Azure-szolgáltatás vehető igénybe. 

![Cloud Foundry az Azure-beli integrációs architektúra](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Magas rendelkezésre állás és méretezhetőség
### <a name="managed-disk"></a>Managed Disk
Bosh lemez létrehozásához és törlése rutinok Azure CPI (Felhőbeli Provider Interface) használja. Alapértelmezés szerint nem felügyelt lemezeket használnak. Az ügyfél manuálisan storage-fiókok létrehozásához, majd konfigurálja a fiókokat a CF-hez jegyzékfájlok van szükség. Lemezek tárfiókonkénti számának korlátozása miatt nem lehetséges.
Most már [Managed Disk](https://azure.microsoft.com/services/managed-disks/) érhető el, a virtuális gépek felügyelt biztonságos és megbízható lemezes tárolást kínál. Ügyfél már nem kell foglalkozni a tárfiókot, a méretezés és magas rendelkezésre ÁLLÁS. Az Azure automatikusan rendezi a lemezeket. Hogy egy új vagy meglévő környezet, az Azure CPI fogja kezelni, a létrehozás vagy az áttelepítés a felügyelt lemez egy CF üzembe helyezés során. A PCF 1.11 támogatott. Emellett megismerheti a nyílt forráskódú Cloud Foundry [Managed Disk útmutatást](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) referenciaként. 
### <a name="availability-zone-"></a>Availability Zone *
Natív alkalmazásplatform, mint a Cloud Foundry célja a [négy magas szintű elérhetőséget](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Szoftver hibák első három szintek magát a CF-rendszer tudja kezelni, miközben platform hibatűrés felhőszolgáltatók által biztosított. A legfontosabb CF összetevők egy felhőszolgáltató platform magas rendelkezésre ÁLLÁSÚ megoldás használatával kell védeni. Ez magában foglalja a GoRouters, Diego Brains, CF adatbázisának és csempék. Alapértelmezés szerint [Azure rendelkezésre állási csoport](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) egy adatközpontban, fürtök között a hibatűrés szolgál.
Az új jó, [Azure rendelkezésre állási zónában](https://docs.microsoft.com/azure/availability-zones/az-overview ) akkor szabadul fel, a hibatűrést, a következő szintre, és közel valós idejű redundanciával adatközpontból.
Azure rendelkezésre állási zónában éri el a magas rendelkezésre ÁLLÁSÚ virtuális gépek elhelyezését a 2 + data centers által, az egyes virtuálisgép redundáns más csoportjaihoz. Ha egy zóna nem működik, az egyéb csoportok továbbra is az élő, elkülönítve a vészhelyreállítási.
> [!NOTE] 
> Az Azure rendelkezésre állási zóna nem érhető el minden régióban még, ellenőrizze a legújabb [támogatott régiók listáját közleményt](https://docs.microsoft.com/azure/availability-zones/az-overview). Nyissa meg a forrás a Cloud Foundry ellenőrizze [rendelkezésre állási zónában az Azure a nyílt forráskódú Cloud Foundry útmutatást](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Hálózati útválasztás
Alapértelmezés szerint az Azure alapszintű load balancer bejövő CF API-alkalmazások kérelmek, továbbítja azokat a Gorouters szolgál. CF-összetevők, például a Diego agy, MySQL, ERT használatával is a terheléselosztó elosztja a forgalmat a magas rendelkezésre ÁLLÁS. Emellett az Azure teljes körűen felügyelt terheléselosztási megoldások ismertet. Ha a TLS jogcímei ("SSL-alapú kiszervezéshez") vagy HTTP/HTTPS Kérelemfeldolgozás application layer, fontolja meg az Application Gateway. Magas rendelkezésre állás és méretezhetőség terheléselosztási a 4. réteg fontolja meg a standard load balancer.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Az Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) különböző 7. rétegbeli terheléselosztási funkciókat, beleértve az SSL-kiürítés, teljes körű SSL, a webalkalmazási tűzfal, cookie-alapú munkamenet-affinitást és egyéb kínál. Is [nyílt forráskódú Cloud Foundry az Application Gateway konfigurálása](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). A PCF, ellenőrizze a [PCF 2.1 kibocsátási megjegyzések](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) a POC-tesztelése.

### <a name="azure-standard-load-balancer-"></a>Az Azure Standard Load Balancer *
Az Azure Load Balancer 4. szintű terheléselosztó. A forgalmat egy elosztott terhelésű készlet szolgáltatás példányai között osztja szolgál. A standard verzióban biztosít [speciális szolgáltatások](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) az alapszintű verzióra. Például: 1. A háttérkiszolgáló-készlet maximális korlátját 100 jelenik meg, hogy 1000 virtuális gépet.  2. A végpontok mostantól támogatja a több rendelkezésre állási csoportok helyett egyetlen rendelkezésre állási csoportban.  3. További funkciók, például magas rendelkezésre ÁLLÁSÚ portok, gazdagabb monitorozási adatok, stb. Az Azure rendelkezésre állási zónában történő áthelyezéséhez, a standard load balancer szükség. Egy új központi telepítés kapcsán azt javasoljuk, hogy az Azure Standard Load Balancer kipróbálása. 

## <a name="3-authentication"></a>3. Hitelesítés 
[Cloud Foundry felhasználói fiók és a hitelesítési](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) a központi identitáskezelő szolgáltatást nyújt a CF-hez és különböző összetevőjét. [Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) szolgáltatás a Microsoft több-bérlős felhőalapú címtár és Identitáskezelés felügyeleti szolgáltatás. Alapértelmezés szerint a Cloud Foundry hitelesítéshez UAA szolgál ki. Speciális beállítás UAA is támogatja az Azure AD egy külső felhasználó tárolóként. Az Azure AD-felhasználók hozzáférhetnek a Cloud Foundry használata az LDAP-azonosítót fog kérni, a Cloud Foundry-fiók nélkül. Az alábbi lépéseket követve [konfigurálása az Azure ad-ben a PCF UAA](http://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Adattárolás a Cloud Foundry futásidejű rendszer esetében
A cloud Foundry nagyszerű megnyithatja az utat az Azure blobtárhely vagy MySQL/PostgreSQL/Azure-szolgáltatások alkalmazás futtatókörnyezeti rendszerhez szükséges tárhelyet kínál.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>A Cloud Foundry felhőalapú adatkezelő blobtárhely Azure Blobtárhely
A felhőalapú adatkezelő blobtárhely buildpacks, a feldolgozó programok, a csomagok és az erőforráskészletek a kritikus fontosságú tárolóban. NFS-kiszolgáló alapértelmezés szerint felhőalapú adatkezelő blobtárhely szolgál. A külső tároló Azure Blob Storage használjuk rendszerkritikus meghibásodási pontot elkerülése érdekében. Tekintse meg a [Cloud Foundry – dokumentáció](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) a háttér-információkért és [a Pivotal Cloud Foundry-beállításai](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL Cloud Foundry rugalmas, futtatás ideje adatbázis *
Rugalmas modul a CF két fő rendszeradatbázisok van szükség:
#### <a name="ccdb"></a>CCDB 
A felhő Controller adatbázisába.  Felhőalapú adatkezelő biztosít az ügyfelek hozzáférhetnek a rendszerhez tartozó REST API-végpontokon. CCDB cégek vagy intézmények, a tárolóhelyek, a szolgáltatások, a felhasználói szerepköröket, valamint több felhőalapú adatkezelő táblák tárolja.
#### <a name="uaadb"></a>UAADB 
Az adatbázis felhasználói fiók és a hitelesítéshez. Tárolja a felhasználói hitelesítés kapcsolatos adatokat, például titkosított felhasználónevek és jelszavak.

Alapértelmezés szerint a helyi rendszer-adatbázisok (MySQL) használható. Magas rendelkezésre ÁLLÁS és a méretezési csoport, a kihasználhatja az Azure felügyelt MySQL vagy a PostgreSQL-szolgáltatások. Az utasítás a következő [CCDB, UAADB és más rendszeradatbázisok nyílt forráskódú Cloud Foundry az Azure MySQL/PostgreSQL engedélyezése](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Az Open Service Broker
Az Azure service broker alkalmazás elérése az Azure-szolgáltatások egységes felületet biztosít. Az új [Open Service Broker for Azure projekt](https://github.com/Azure/open-service-broker-azure) szolgáltatások továbbítására az alkalmazások között a Cloud Foundry-hoz, az OpenShift és a Kubernetes egységes és egyszerű módot biztosít. Tekintse meg a [Azure Open Service Broker a PCF csempe](https://network.pivotal.io/products/azure-open-service-broker-pcf/) üzembe helyezési utasítások a PCF.

## <a name="6-metrics-and-logging"></a>6. Metrikák és naplózás
Az Azure Log Analytics Nozzle egy Cloud Foundry összetevő, amely továbbítja a metrikák a [Cloud Foundry loggregator "firehose"](https://docs.cloudfoundry.org/loggregator/architecture.html) való [Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/). A Nozzle a collect, megtekintheti, és a CF-hez rendszer állapotának és teljesítményének metrikát elemezhet, több üzemelő alkalmazáspéldányok között.
Kattintson a [Itt](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) való üzembe helyezése az Azure Log Analytics Nozzle a nyílt forráskódú és a Pivotal Cloud Foundry-környezetben is, és majd elérni az adatokat az Azure Log Analytics-konzolról. 
> [!NOTE]
> A PCF 2.0-s BOSH mérőszámok a virtuális gépek alapértelmezés szerint a Loggregator "firehose" továbbítja, és integrálva vannak az Azure Log Analytics-konzolon.

## <a name="7-cost-saving"></a>7. Költségmegtakarítás
### <a name="cost-saving-for-devtest-environments"></a>Költségmegtakarítás a fejlesztési és tesztelési környezetek
#### <a name="b-series-"></a>A B sorozat: *
F és a D VM sorozat általában javasolt a Pivotal Cloud Foundry éles környezetben, amíg az új – "adatlöket-kezelés" [a B sorozat](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) új lehetőségeket biztosít. A B sorozat – adatlöket-kezelés virtuális gépek ideálisak a nagy számítási feladatokhoz, amelyek nem a teljes teljesítmény, a processzor folyamatos kell például a webkiszolgálók, kis méretű adatbázisokhoz, és a fejlesztési és tesztelési környezetek. Ezeket a feladatokat jellemzően rendelkeznek – adatlöket-kezelés teljesítményre vonatkozó követelmények. $ 0,05/óra (F1) képest $0.012%-a./nA/óra (B1), a teljes listájának megtekintéséhez [Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) és [árak](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) részleteiről. 
#### <a name="managed-standard-disk"></a>Managed Standard Disk: 
Prémium szintű lemezeket is megbízható teljesítmény, éles környezetben ajánlott.  A [Managed Disk](https://azure.microsoft.com/services/managed-disks/), Standard szintű tárolást is biztosít hasonló megbízhatóságát, különböző teljesítmény. Amely nem teljesítményigényes számítási feladatokhoz, például fejlesztési, tesztelési vagy a nem kritikus fontosságú környezet a standard szintű felügyelt lemezek és az alacsonyabb költségek egy másik lehetőséget kínálnak.  
### <a name="cost-saving-in-general"></a>Az általános költségmegtakarítás 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Jelentős VM költsége mentése az Azure-foglalások: 
Mai összes CF virtuális számolunk fel "igény szerinti" díjszabás, bár a környezetek általában kapcsolatos információk határozatlan időre. Most egy 1 vagy 3 éves távon a Virtuálisgép-kapacitás foglalása, és 45-65 %-os engedményt kapnak. Kedvezmények a számlázási rendszerben a környezet módosítása nélkül. További információkért lásd: [Azure foglalások works](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>A felügyelt kisebb méretű prémium szintű lemez: 
A felügyelt lemezek támogatása kisebb lemezméretet, például a P4(32 GB) és a prémium és standard szintű lemezek P6(64 GB). Ha kis méretű számítási feladatokat, mentheti költség, standard, prémium szintű lemezekből felügyelt premium lemezekre való migrálás során.
#### <a name="utilizing-azure-first-party-services"></a>Első nyilvános Azure Services használatával: 
Kihasználhatja az Azure belső szolgáltatás csökkenti a hosszú távú felügyeleti költség, magas rendelkezésre ÁLLÁS és megbízhatóság a fenti szakaszokban mellett. 

Pivotal elindított egy [kis erőforrás-igényű ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) PCF azon ügyfeleink esetében, az összetevők van elhelyezve, akár 2500 alkalmazáspéldányok futó csupán 4 virtuális gépeket. A próbaverzió már keresztül elérhető [Azure Marketplace-beli](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>További lépések
Azure-beli integrációs funkciók érhetők el először [nyílt forráskódú Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), mielőtt érhető el a Pivotal Cloud Foundry. Szolgáltatások jelölése * továbbra sem érhetők el PCF-en keresztül. Cloud Foundry-integráció az Azure Stack nem terjed ki a dokumentum vagy.
A PCF támogatásához az a funkciók jelölése *, vagy a Cloud Foundry-integráció az Azure Stack, lépjen kapcsolatba a Pivotal és a Microsoft ügyfélmenedzserével, legfrissebb állapotát. 

