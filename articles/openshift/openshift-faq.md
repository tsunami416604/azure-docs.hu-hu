---
title: Gyakori kérdések az Azure Red Hat OpenShift
description: A Microsoft Azure Red Hat OpenShift kapcsolatos gyakori kérdésekre adott válaszok
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 92529c2d60b32e9c8b57b897008b5333adc2a4d4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594967"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift – gyakori kérdések

Ez a cikk a Microsoft Azure Red Hat OpenShift kapcsolatos gyakori kérdéseket (GYIK) tárgyalja.

## <a name="which-azure-regions-are-supported"></a>Mely Azure-régiók támogatottak?

Tekintse meg a [támogatott forrásokat](supported-resources.md#azure-regions) azon globális régiók listájához, ahol az Azure Red Hat OpenShift támogatott.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Telepíthetek fürtöt meglévő virtuális hálózatra?

Nem. Egy Azure Red Hat OpenShift-fürtöt pedig összekapcsolhatók egy meglévő virtuális hálózathoz. A részletekért lásd: [fürt virtuális hálózatának összekötése meglévő virtuális hálózathoz](tutorial-create-cluster.md#create-the-cluster) .

## <a name="what-cluster-operations-are-available"></a>Milyen fürtözött műveletek érhetők el?

Csak a számítási csomópontok számának vertikális fel-és leskálázására van lehetőség. A létrehozás után más módosítások nem engedélyezettek `Microsoft.ContainerService/openShiftManagedClusters` az erőforrásban. A számítási csomópontok maximális száma 20-ra van korlátozva.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Milyen virtuálisgép-méreteket használhatok?

Tekintse meg az [Azure Red Hat OpenShift virtuálisgép](supported-resources.md#virtual-machine-sizes) -méreteit az Azure Red Hat OpenShift-fürtökkel használható virtuálisgép-méretek listájáért.

## <a name="is-data-on-my-cluster-encrypted"></a>A fürtön lévő adatai titkosítva vannak?

Alapértelmezés szerint a titkosítás inaktív állapotban van. Az Azure Storage platform automatikusan titkosítja az adatait, mielőtt megőrzi azt, és visszafejti az adatok beolvasása előtt. A részletekért tekintse meg [Az Azure Storage Service encryption for](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) inaktív adatokat.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Használhatom a Prometheus/Grafana-t az alkalmazásaim figyelésére?

Igen, üzembe helyezheti a Prometheus-t a névtérben, és figyelheti a névterében lévő alkalmazásokat.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Használhatom a Prometheus/Grafana-t a fürt állapotával és kapacitásával kapcsolatos metrikák figyelésére?

Nem, aktuális időpontban nem.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>A Docker-beállításjegyzék külsőleg is elérhető, így olyan eszközöket is használhatok, mint például a Jenkins?

A Docker- `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` beállításjegyzék elérhető, de nincs megadva erős tárolási tartóssági garancia. [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)is használhatja.

## <a name="is-cross-namespace-networking-supported"></a>Támogatott-e a névterek közötti hálózatkezelés?

Az ügyfél és az egyéni projekt-rendszergazdák a különböző névtérbeli hálózatkezelést (beleértve a megtagadást is) az `NetworkPolicy` objektumok használatával, projektenként is testre szabhatják.

## <a name="can-an-admin-manage-users-and-quotas"></a>A rendszergazdák kezelhetik a felhasználókat és a kvótákat?

Igen. Az Azure Red Hat OpenShift rendszergazdája a felhasználók és a kvóták kezelését is képes kezelni az összes felhasználó által létrehozott projekthez való hozzáférés mellett.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Korlátozható a fürt csak bizonyos Azure AD-felhasználókra?

Igen. Az Azure ad-alkalmazás konfigurálásával korlátozhatja, hogy mely Azure AD-felhasználók tudnak bejelentkezni a fürtbe. További információ [: How to: az alkalmazás korlátozása felhasználói csoportra](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Korlátozható a felhasználók projekt létrehozása?

Igen. Jelentkezzen be a fürtbe Azure Red Hat OpenShift-rendszergazdaként, és hajtsa végre a következő parancsot:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

További információkért tekintse meg a OpenShift dokumentációját a [saját kiépítés letiltásához](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning).

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Lehet egy fürt több Azure-régióban is számítási csomópontokkal rendelkezik?

Nem. Egy Azure Red Hat OpenShift-fürt minden csomópontjának ugyanabból az Azure-régióból kell származnia.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>A fő-és infrastruktúra-csomópontok el vannak-e látva az Azure Kubernetes szolgáltatással (ak)?

Nem. Az összes erőforrás, beleértve a fürt főkiszolgálóját is, az ügyfél-előfizetésben fut. Az ilyen típusú erőforrások írásvédett erőforráscsoporthoz kerülnek.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Támogatott-e az Azure-hoz (OSBA) készült Open Service Broker?

Igen. A OSBA az Azure Red Hat OpenShift használatával is használhatja. További információért lásd a [Service Broker megnyitása az Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) -ban című témakört.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Egy másik előfizetésben lévő virtuális hálózatba próbálok csatlakozni, de hibaüzenetet kapok `Failed to get vnet CIDR` .

Ügyeljen arra, hogy a virtuális hálózattal rendelkező előfizetésben regisztrálja `Microsoft.ContainerService` a szolgáltatót a következővel:`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Mi az Azure Red Hat OpenShift (ARO) karbantartási folyamata?

Az ARO-ban háromféle karbantartási lehetőség létezik: a etcd-és a felhő-szolgáltató által kezdeményezett karbantartás, valamint a biztonsági mentés és helyreállítás.

+ A frissítések közé tartoznak a szoftverfrissítések és a CVEs. A CVE szervizelése indításkor a futtatásával `yum update` történik, és azonnali mérséklést tesz lehetővé.  Párhuzamosan új rendszerkép-Build jön létre a jövőbeli fürt létrehozásához.

+ A etcd-adatbázis biztonsági mentése és kezelése egy automatizált folyamat, amely a művelettől függően a fürt leállását igényli. Ha a etcd-adatbázis visszaállítása biztonsági másolatból történik, a rendszer leállást eredményez. Óránként biztonsági mentést készítünk a etcd, és megtartjuk az elmúlt 6 órányi biztonsági mentést.

+ A felhőalapú szolgáltató által kezdeményezett karbantartás magában foglalja a hálózat, a tárolás és a regionális kimaradásokat. A karbantartás a felhőalapú szolgáltatótól függ, és a szolgáltató által biztosított frissítésekre támaszkodik.

## <a name="what-is-the-general-upgrade-process"></a>Mi az általános frissítési folyamat?

A frissítés futtatásának biztonságos folyamatnak kell lennie, és nem szabad megszakítani a fürtszolgáltatásokat. A SRE kiválthatja a frissítési folyamatot, ha új verzió érhető el, vagy a CVEs is fennáll.

Az elérhető frissítések tesztelése egy fázis-környezetben történik, majd az üzemi fürtökre lesz alkalmazva. Alkalmazása esetén az új csomópontot átmenetileg hozzáadja a rendszer, és a csomópontok egy forgó módon frissülnek, így a hüvelyek megőrzik a replikák számát. Az ajánlott eljárások követésével minimálisan nem lehet leállást biztosítani.

A függőben lévő frissítés vagy frissítés súlyossága alapján a folyamat eltérhet abban, hogy a frissítéseket gyorsan alkalmazhatja, hogy enyhítse a szolgáltatásnak a CVE-ra való kitettségét. Az új rendszerképeket aszinkron módon, tesztelve és a fürt frissítése során kell kiépíteni. Ettől függetlenül nincs különbség a vészhelyzet és a tervezett karbantartás között. A tervezett karbantartás nincs előre beütemezhetve az ügyféllel.

Az értesítések elküldése az ICM-n keresztül és e-mailben történik, ha az ügyféllel való kommunikációra van szükség.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Mi a helyzet a sürgősségi és a tervezett karbantartási időszakokkal?

A két karbantartási típus között nem kell különbséget tenni. Csapatunk 24/7/365, és nem használja a hagyományos ütemezett "munkaidőn kívüli" karbantartási időszakokat.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Hogyan fogja frissíteni a gazdagép operációs rendszer-és OpenShift szoftverét?

A gazda operációs rendszer és a OpenShift szoftver az általános frissítési és rendszerkép-létrehozási folyamaton keresztül frissül.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Mi a folyamat a frissített csomópont újraindítására?

Ezt a frissítés részeként kell kezelni.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>A etcd-ben tárolt adatforgalom ARO-ra van titkosítva?

A etcd szintjén nincs titkosítva. A bekapcsolásának lehetősége jelenleg nem támogatott. A OpenShift támogatja ezt a funkciót, de mérnöki erőfeszítésekre van szükség a közúti térképen való működéshez. Az adatátvitel a lemez szintjén történik. További információkért tekintse meg az [adatok titkosítása az adattár rétegében](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) című témakört.

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Az alapul szolgáló virtuális gépek naplófájljai továbbítva lesznek egy ügyfél-naplózási elemzési rendszerbe?

A syslog, a Docker-naplók, a Journal és a dmesg kezelése a felügyelt szolgáltatással történik, és az ügyfelek számára nem érhető el.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Hogyan érhetik el az ügyfél az olyan mérőszámokat, mint a CPU/memória a csomópont szintjén, hogy a méretezési, hibakeresési és egyéb műveleteket is végrehajtsa. Nem úgy tűnik, hogy `kubectl top` egy ARO-fürtön fusson.

Az ügyfelek a (z) parancs `oc adm top nodes` használatával vagy `kubectl top nodes` az ügyfél-rendszergazda clusterrole érhetik el a CPU-/memória-metrikákat a csomópont szintjén.  Az ügyfelek a (z) parancshoz `pods` `oc adm top pods` tartozó CPU-/memória-metrikákat is használhatják`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Mi az az alapértelmezett Pod Scheduler-konfiguráció az ARO-hoz?

Az ARO a OpenShift lévő alapértelmezett ütemező szolgáltatást használja. Az ARO-ban nem támogatott néhány további mechanizmus. További részletekért tekintse meg az [alapértelmezett Scheduler-dokumentációt](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) és a [fő Scheduler dokumentációját](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v16/data/master/etc/origin/master/scheduler.json) .

A speciális/egyéni ütemezés jelenleg nem támogatott. További részletekért tekintse meg az [ütemezési dokumentációt](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) .

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Ha felskálázást végez az üzembe helyezés során, hogyan képezhetők le az Azure-beli tartalék tartományok a pod-elhelyezésbe, hogy a szolgáltatáshoz tartozó összes hüvely ne legyen kiütéssel egyetlen tartalék tartomány meghibásodása esetén?

A virtuálisgép-méretezési csoportok az Azure-ban való használatakor alapértelmezés szerint öt tartalék tartomány van. A méretezési csoportokban lévő virtuálisgép-példányok az egyik tartalék tartományba kerülnek. Ez biztosítja, hogy a fürt számítási csomópontjain üzembe helyezett alkalmazások külön tartalék tartományokban legyenek elhelyezve.

További részletekért tekintse meg [a virtuális gépek méretezési csoportjának megfelelő számú tartalék tartomány kiválasztása](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) című témakört.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Van mód a pod elhelyezés kezelésére?

Ügyfeleinknek lehetősége van a csomópontok beszerzésére és a címkék megtekintésére ügyfél-rendszergazdaként.  Ez lehetőséget biztosít a méretezési csoportba tartozó bármely virtuális gép célzására.

Adott címkék használatakor körültekintően kell eljárni:

- Az állomásnév nem használható. Az állomásnév gyakran a frissítésekkel és frissítésekkel lesz elforgatva, és a változás garantált.

- Ha az ügyfél egy adott címkére vagy egy központi telepítési stratégiára vonatkozó kéréssel rendelkezik, akkor ez a megoldás a mérnöki erőfeszítésekhez szükséges, és jelenleg nem támogatott.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Mi a hüvelyek maximális száma egy ARO-fürtben?Mekkora számú hüvely/csomópont az ARO-ban?

 Az Azure Red Hat OpenShift 3,11 egy 50-Pod/Node korláttal rendelkezik, és az [ARO 20 számítási csomópontos korlátot](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)tartalmaz, így az ARO-fürtökben legfeljebb 50 * 20 = 1000-ra támogatott hüvelyek maximális száma adható meg.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Megadhatjuk az IP-címtartományok üzembe helyezését a privát VNET, így elkerülhetők a többi vállalati virtuális hálózatok való ütközések?

Az Azure Red Hat OpenShift támogatja a VNET-társítást, és lehetővé teszi az ügyfél számára, hogy VNET biztosítson a társának és egy olyan VNET-CIDR, amelyben a OpenShift-hálózat működni fog.

Az ARO által létrehozott VNET védettek lesznek, és nem fogadják el a konfigurációs módosításokat. A VNET az ügyfél vezérli, és az előfizetésében található.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>A fürt egy ügyfél-előfizetésben található? 

Az Azure által felügyelt alkalmazás egy zárolt erőforráscsoport és az ügyfél-előfizetés között él. Az ügyfél megtekintheti az adott RG objektumait, de nem módosítható.

## <a name="is-the-sdn-module-configurable"></a>Konfigurálható az SDN-modul?

Az SDN a openshift-OVS-networkpolicy, és nem konfigurálható.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Mely UNIX-jogosultságok érhetők el a Masters/infra/app-csomópontok számára a IaaS-ben?

Nem alkalmazható erre az ajánlatra. A csomópont-hozzáférés tiltott.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Milyen OCP-jogosultságokkal rendelkezik? Fürt – rendszergazda? Projekt – rendszergazda?

Részletekért tekintse meg az Azure Red Hat OpenShift- [fürt felügyeletének áttekintése](https://docs.openshift.com/aro/admin_guide/index.html)című témakört.

## <a name="which-kind-of-federation-with-ldap"></a>Milyen típusú összevonás van az LDAP-sel?

Ez az Azure AD-integráción keresztül érhető el. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Van olyan elem az ARO-ban, amelyet más ügyfelek is megosztanak? Vagy minden független?

Minden egyes Azure Red Hat OpenShift-fürt egy adott ügyfélhez van hozzárendelve, és az ügyfél előfizetésén belül él. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Választhatunk bármilyen állandó tárolási megoldást, például a OCS-t? 

A következő két tárolási osztály közül választhat: Azure Disk és Azure-fájl.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Hogyan frissül a fürt (beleértve a főbb és a kiskorúakat a sebezhetőségek miatt)?

Lásd: [Mi az általános frissítési folyamat?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Milyen Azure Load balancert használ az ARO?Standard vagy alapszintű, és ez konfigurálható?

Az ARO szabványos Azure Load Balancer használ, és nem konfigurálható.

## <a name="can-aro-use-netapp-based-storage"></a>Az ARO a NetApp-alapú tárolást is használhatja?

Jelenleg az egyetlen támogatott tárolási lehetőség az Azure Disk és az Azure file Storage-osztályok. 


