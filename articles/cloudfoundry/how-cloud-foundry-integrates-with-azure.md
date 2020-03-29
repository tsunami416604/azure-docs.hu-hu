---
title: Hogyan integrálódik a Cloud Foundry az Azure-ral | Microsoft dokumentumok
description: Bemutatja, hogy a Cloud Foundry hogyan használhatja az Azure-szolgáltatásokat a vállalati élmény fokozására
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 04ef72f7ec70b370305395ae8de8180f4594b43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277344"
---
# <a name="integrate-cloud-foundry-with-azure"></a>A Cloud Foundry Azure-ral való integrálása

[A Cloud Foundry](https://docs.cloudfoundry.org/) egy PaaS platform, amely a felhőszolgáltatók IaaS platformján fut. Konzisztens alkalmazástelepítési élményt nyújt a felhőszolgáltatók között. Emellett integrálható a különböző Azure-szolgáltatásokkal, nagyvállalati szintű HA-val, méretezhetőséggel és költségmegtakarítással.
A [Cloud Foundry 6 alrendszere](https://docs.cloudfoundry.org/concepts/architecture/)van, amelyek rugalmasan skálázhatók online, beleértve: Útválasztás, Hitelesítés, Alkalmazás életciklus-kezelés, szolgáltatáskezelés, üzenetküldés és figyelés. Az egyes alrendszerek konfigurálhatja cloud öntödei a levelező Azure-szolgáltatás használatára. 

![Felhőalapú öntöde az Azure integrációs architektúrán](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Magas rendelkezésre állás és méretezhetőség
### <a name="managed-disk"></a>Felügyelt lemez
A Bosh az Azure CPI (Cloud Provider Interface) segítségével hozza létre és szeretné a rutinokat létrehozni és törlésekor. Alapértelmezés szerint nem felügyelt lemezek et használ. Az ügyfélnek manuálisan kell létrehoznia a tárfiókokat, majd konfigurálnia kell a fiókokat a CF jegyzékfájlokban. Ennek oka a tárfiókonkénti lemezek számának korlátozása.
Most [felügyelt lemez](https://azure.microsoft.com/services/managed-disks/) áll rendelkezésre, kínál felügyelt biztonságos és megbízható lemeztároló virtuális gépek. Az ügyfélnek már nem kell foglalkoznia a méretezési és ha tárfiókkal. Az Azure automatikusan rendezi a lemezeket. Akár egy új, akár egy meglévő központi telepítésről van szó, az Azure CPI kezeli a felügyelt lemez létrehozását vagy áttelepítését a CF-telepítés során. A PCF 1.11 támogatja. Azt is vizsgálja meg a nyílt forráskódú Cloud Foundry [felügyelt lemez útmutató](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) referencia. 
### <a name="availability-zone-"></a>Elérhetőségi zóna *
A Cloud Foundry felhőalapú alkalmazásplatformként [négy magas rendelkezésre állási szinttel van kialakítva.](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html) Míg a szoftverhibák első három szintjét maga a CF-rendszer is eltudja látni, a platformhiba-toleranciát a felhőszolgáltatók biztosítják. A legfontosabb CF-összetevőket egy felhőszolgáltató platformOS HA megoldásával kell védeni. Ez magában foglalja a GoRouters, Diego Brains, CF adatbázis és szolgáltatás csempe. Alapértelmezés szerint az [Azure rendelkezésre állási készlet](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) e fürtök közötti hibatűrés egy adatközpontban.
A jó új, az [Azure availability Zone](https://docs.microsoft.com/azure/availability-zones/az-overview ) már megjelent, így a hibatűrés a következő szintre, alacsony késéses redundancia adatközpontok között.
Az Azure rendelkezésre állási zónája ha-t ér el azáltal, hogy virtuális gépek készletét 2+ adatközpontba helyezi, a virtuális gépek minden egyes készlete redundáns más készletekhez. Ha az egyik zóna nem, a többi készlet még mindig él, elszigetelve a katasztrófától.
> [!NOTE] 
> Az Azure rendelkezésre állási zónája még nem érhető el minden régióban, tekintse meg [a támogatott régiók listájának](https://docs.microsoft.com/azure/availability-zones/az-overview)legújabb bejelentését. A nyílt forráskódú felhőöntöde i. open source cloud foundry (Nyílt forráskódú felhőöntödei útmutató) esetén tekintse meg [az Azure rendelkezésre állási zónájában a nyílt forráskódú felhőöntödei útmutatást.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone)

## <a name="2-network-routing"></a>2. Hálózati útválasztás
Alapértelmezés szerint az Azure alapszintű terheléselosztó a bejövő CF API/apps kérelmekhez használatos, és továbbítja azokat a Goroutereknek. CF alkatrészek, mint a Diego Brain, MySQL, ERT is használhatja a terheléselosztó, hogy egyensúlyt a forgalom ha. Az Azure teljes körűen felügyelt terheléselosztási megoldásokat is kínál. Ha TLS-végződtetést ("SSL-kiszervezés") vagy HTTP/HTTPS kérelemalkalmazás-réteg feldolgozást keres, fontolja meg az Application Gateway alkalmazását. A 4- es réteg magas rendelkezésre állása és méretezhetősége érdekében vegye figyelembe a szabványos terheléselosztót.
### <a name="azure-application-gateway-"></a>Azure alkalmazásátjáró *
[Az Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) különböző 7-es réteges terheléselosztási lehetőségeket kínál, beleértve az SSL-kiszervezést, a végpontok közötti SSL-t, a webalkalmazás-tűzfalat, a cookie-alapú munkamenet-affinitást és még sok mást. Az [Alkalmazásátjáró t a nyílt forráskódú felhőalapú öntödében állíthatja be.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway) PCF esetén ellenőrizze a [PCF 2.1 kiadási megjegyzéseket](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) a POC-teszthez.

### <a name="azure-standard-load-balancer-"></a>Azure standard terheléselosztó *
Az Azure Load Balancer egy 4. A forgalom elosztása a szolgáltatások példányai között egy terheléselosztási halmazban. A standard verzió [fejlett funkciókat](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) biztosít az alapverzión felül. Például 1. A háttérkészlet maximális korlátja 100-ról 1000 virtuális gépre emelkedik.  2. A végpontok mostantól több rendelkezésre állási csoport támogatja helyett egyetlen rendelkezésre állási csoport.  3. További funkciók, mint a HA portok, gazdagabb figyelési adatok, és így tovább. Ha az Azure rendelkezésre állási zónájába költözik, szabványos terheléselosztóra van szükség. Új üzembe helyezés esetén azt javasoljuk, hogy kezdje az Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Hitelesítés 
[A Cloud Foundry Felhasználói Fiók és Hitelesítés](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) a CF és különböző összetevőinek központi identitáskezelési szolgáltatása. [Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) a Microsoft több-bérlős, felhőalapú címtár- és identitáskezelési szolgáltatása. Alapértelmezés szerint az UAA a cloud öntödei hitelesítéshez használatos. Speciális beállításként az UAA is támogatja az Azure AD külső felhasználói tárolóként. Az Azure AD-felhasználók hozzáférhetnek a Cloud Foundry-hoz az LDAP-identitásuk használatával, felhőalapú öntödei fiók nélkül. Az alábbi lépésekkel [konfigurálhatja az Azure AD uaa-t a PCF-ben.](https://docs.pivotal.io/p-identity/1-6/azure/index.html)

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Adattárolás a cloud foundry futásidejű rendszerhez
A Cloud Foundry nagyszerű bővíthetőséget kínál az Azure blobstore vagy az Azure MySQL/PostgreSQL szolgáltatások alkalmazásfutásidejű rendszertároláshoz való használatához.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure Blobstore a cloud foundry felhővezérlő blobstore-hoz
A Cloud Controller blobstore egy kritikus adattár buildcsomagok, cseppek, csomagok és erőforráskészletek. Alapértelmezés szerint az NFS-kiszolgáló a Cloud Controller blobstore.By default NFS server is used for Cloud Controller blobstore. Az egypontos hibák elkerülése érdekében használja az Azure Blob Storage külső tárolóként. Tekintse meg a [Cloud Foundry dokumentációját](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) a háttérről és [a Pivotal Cloud Foundry lehetőségeiről.](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html)

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL mint Cloud Foundry Elastic Run Time Database *
A CF Elastic Runtime két fő rendszeradatbázist igényel:
#### <a name="ccdb"></a>CCDB 
A Cloud Controller adatbázis.  A Cloud Controller REST API-végpontokat biztosít az ügyfelek számára a rendszer eléréséhez. A CCDB a szervezeti műveletek, a szóközök, a szolgáltatások, a felhasználói szerepkörök és egyebek tábláit tárolja a Felhővezérlő számára.
#### <a name="uaadb"></a>UAADB 
A felhasználói fiók és a hitelesítés adatbázisa. Ez tárolja a felhasználói hitelesítéssel kapcsolatos adatokat, például a titkosított felhasználónevek et és jelszavakat.

Alapértelmezés szerint helyi rendszeradatbázis (MySQL) használható. A HA és a méretezés, használja az Azure által kezelt MySQL vagy PostgreSQL szolgáltatások. Itt van az utasítás, amely engedélyezi az [Azure MySQL/PostgreSQL-t a CCDB, az UAADB és más rendszeradatbázisok számára a nyílt forráskódú felhőöntödével.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service)

## <a name="5-open-service-broker"></a>5. Nyílt szolgáltatás bróker
Az Azure szolgáltatásközvetítő egységes felületet kínál az alkalmazások Azure-szolgáltatásokhoz való hozzáférésének kezeléséhez. Az új [Open Service Broker for Azure projekt](https://github.com/Azure/open-service-broker-azure) egyetlen és egyszerű módot kínál arra, hogy szolgáltatásokat nyújtson az alkalmazásoknak a Cloud Foundry, az OpenShift és a Kubernetes alkalmazásokszámára. Tekintse meg az Azure Open Service Broker for PCF csempe üzembe helyezési utasításokat a PCF.See the [Azure Open Service Broker for PCF tile](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) for deployment instructions on PCF.

## <a name="6-metrics-and-logging"></a>6. Mérőszámok és naplózás
Az Azure Log Analytics-fúvóka egy cloud-öntödei összetevő, amely továbbítja a metrikákat a [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) [az Azure Monitor naplók.](https://azure.microsoft.com/services/log-analytics/) A fúvóka segítségével összegyűjtheti, megtekintheti és elemezheti a CF-rendszer állapotát és teljesítménymutatóit több telepítéssorán.
Ide [here](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) kattintva megtudhatja, hogyan telepítheti az Azure Log Analytics fúvókát a nyílt forráskódú és a pivotal cloud foundry környezetben, majd hozzáférhet az adatokhoz az Azure Monitor naplók konzolról. 
> [!NOTE]
> A PCF 2.0-ból a virtuális gépek BOSH állapotmetrikok alapértelmezés szerint a Loggregator Firehose,és integrálva vannak az Azure Monitor naplók konzol.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Költségmegtakarítás
### <a name="cost-saving-for-devtest-environments"></a>Költségmegtakarítás fejlesztési és tesztelési környezetekben
#### <a name="b-series-"></a>B-sorozat: *
Míg az F és D VM sorozatok általában ajánlottak a Pivotal Cloud Foundry termelési környezetben, az új "burstable" [B sorozat](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) új lehetőségeket kínál. A B sorozatú burstable virtuális gépek ideálisak olyan számítási feladatokhoz, amelyek nem igényelnek folyamatosan a CPU teljes teljesítményét, például webkiszolgálókat, kis adatbázisokat, valamint fejlesztési és tesztelési környezeteket. Ezek a számítási feladatok általában burstable teljesítménykövetelményekkel rendelkeznek. Ez $0.012/hour (B1) képest $0.05/hour (F1), tekintse meg a virtuális [gép méreteiés](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) [árai](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) teljes listáját a részletekért. 
#### <a name="managed-standard-disk"></a>Felügyelt szabványos lemez: 
Prémium szintű lemezek ajánlottak a megbízható teljesítmény éles környezetben.  A [Felügyelt lemez segítségével](https://azure.microsoft.com/services/managed-disks/)a szabványos tárhely hasonló megbízhatóságot is biztosíthat, eltérő teljesítménnyel. A nem teljesítményérzékeny, például a fejlesztési/tesztelési vagy nem kritikus környezetben a felügyelt szabványos lemezek alacsonyabb költségű alternatív megoldást kínálnak.  
### <a name="cost-saving-in-general"></a>Költségmegtakarítás általában 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Jelentős virtuális gépköltség-megtakarítás azure-foglalásokkal: 
Ma az összes CF-virtuális gépet "on-demand" díjszabással számlázunk, még akkor is, ha a környezetek általában határozatlan ideig fennmaradnak. Most már 1 vagy 3 éves időtartamra lefoglalhatja a virtuális gép kapacitását, és 45-65%-os kedvezményeket szerezhet. A számlázási rendszerben a kedvezmények nem változnak a környezetben. További információt az [Azure-foglalások működéséről.](https://azure.microsoft.com/pricing/reserved-vm-instances/) 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Felügyelt prémium lemez kisebb méretekkel: 
A felügyelt lemezek támogatják a kisebb lemezméreteket, például a P4(32 GB) és a P6(64 GB) lemezeket mind a prémium és standard lemezek esetében. Ha kis számítási feladatok, költségmegtakarítást takaríthat meg, ha a standard prémium szintű lemezekről felügyelt prémium szintű lemezekre való áttelepítése.
#### <a name="use-azure-first-party-services"></a>Azure First Party services használata: 
Az Azure első féltől származó szolgáltatásának kihasználása csökkenti a hosszú távú adminisztrációs költségeket a fenti szakaszokban említett HA és megbízhatóság mellett. 

Pivotal elindított egy [kis helyigényű ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) PCF ügyfelek számára, az összetevők közösen található mindössze 4 virtuális gépek, futó akár 2500 alkalmazáspéldányok. A próbaverzió már elérhető az [Azure Market helyen.](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)

## <a name="next-steps"></a>Következő lépések
Az Azure integrációs funkciói először a [Nyílt forráskódú cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/)szolgáltatással érhetők el, mielőtt elérhetővé válik a Pivotal Cloud Foundry-n. A * jellel jelölt funkciók továbbra sem érhetők el a PCF-en keresztül. Az Azure Stackkel való cloud öntödei integrációt ez a dokumentum sem tartalmazza.
A *-nal vagy az Azure Stackkel való Cloud Foundry-integrációval kapcsolatos PCF-támogatásért forduljon a Pivotal és a Microsoft fiókkezelőjéhez a legújabb állapotért. 

