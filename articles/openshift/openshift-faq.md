---
title: Gyakori kérdések az Azure Red Hat OpenShift-hez
description: Az alábbiakban válaszokat olvashat a Microsoft Azure Red Hat OpenShift szolgáltatással kapcsolatos gyakori kérdésekre
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619487"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift – gyakori kérdések

Ez a cikk a Microsoft Azure Red Hat OpenShift szolgáltatással kapcsolatos gyakori kérdésekkel foglalkozik.

## <a name="which-azure-regions-are-supported"></a>Mely Azure-régiók támogatottak?

[Lásd: Támogatott erőforrások](supported-resources.md#azure-regions) azon globális régiók listájáért, ahol az Azure Red Hat OpenShift támogatott.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Telepíthetek egy fürtöt egy meglévő virtuális hálózatba?

Nem. De csatlakoztathat egy Azure Red Hat OpenShift-fürtegy meglévő virtuális hálózat társviszony-létesítés. A részleteket a [Fürt virtuális hálózatának csatlakoztatása meglévő virtuális hálózathoz.See Connect a cluster's virtual network to a existing virtual network](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) for details.

## <a name="what-cluster-operations-are-available"></a>Milyen fürtműveletek érhetők el?

Csak a számítási csomópontok száma fel- vagy leskálázható. Az `Microsoft.ContainerService/openShiftManagedClusters` erőforrás létrehozása után semmilyen más módosítás nem engedélyezett. A számítási csomópontok maximális száma legfeljebb 20 lehet.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Milyen virtuálisgép-méreteket használhatok?

Tekintse meg [az Azure Red Hat OpenShift virtuálisgép-méretek](supported-resources.md#virtual-machine-sizes) listáját az Azure Red Hat OpenShift fürthöz használható virtuálisgép-méretek listájáért.

## <a name="is-data-on-my-cluster-encrypted"></a>Titkosítottak a fürtön lévő adatok?

Alapértelmezés szerint a titkosítás inaktív. Az Azure Storage platform automatikusan titkosítja az adatokat, mielőtt megőrizte volna azokat, és visszafejti az adatokat a lekérés előtt. A részleteket [az Azure Storage Service Encryption (Az inaktív adatokért)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) az Azure Storage Service Encryption webhelyen találja.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Használhatom a Prometheus/Grafana alkalmazást az alkalmazásaim figyelésére?

Igen, telepítheti a Prometheus-t a névtérben, és figyelheti az alkalmazásokat a névtérben.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Használhatom a Prometheus/Grafana-t a fürt állapotával és kapacitásával kapcsolatos metrikák figyelésére?

Nem, jelenleg nem.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>A Docker-beállításjegyzék kívülről is elérhető, így olyan eszközöket használhatok, mint a Jenkins?

A Docker-beállításjegyzék `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` érhető el Azonban erős tárolási tartóssági garancia nem biztosított. Használhatja [az Azure Container Registry](https://azure.microsoft.com/services/container-registry/)szolgáltatást is.

## <a name="is-cross-namespace-networking-supported"></a>Támogatott a keresztnévtér-hálózat?

Az ügyfél- és egyéni projektgazdák projektalapon, objektumokkal `NetworkPolicy` testre szabhatják a keresztnévtér-hálózatokat (beleértve a megtagadását is).

## <a name="can-an-admin-manage-users-and-quotas"></a>A rendszergazda kezelheti a felhasználókat és a kvótákat?

Igen. Az Azure Red Hat OpenShift-rendszergazda az összes felhasználó által létrehozott projekt elérése mellett kezelheti a felhasználókat és a kvótákat is.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Fürtöt csak bizonyos Azure AD-felhasználókra korlátozhatok?

Igen. Korlátozhatja, hogy mely Azure AD-felhasználók jelentkezhetnek be egy fürtbe az Azure AD alkalmazás konfigurálásával. További részletek: [Hogyan korlátozhatja az alkalmazást felhasználók egy számára](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Korlátozhatom a felhasználókat a projektek létrehozásában?

Igen. Jelentkezzen be a fürtbe Azure Red Hat OpenShift-rendszergazdaként, és hajtsa végre ezt a parancsot:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

További információt az OpenShift dokumentációjában talál az [önkiépítés letiltásáról.](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Egy fürt számítási csomópontokkal rendelkezhet több Azure-régióban?

Nem. Az Azure Red Hat OpenShift-fürt összes csomódának ugyanabból az Azure-régióból kell származnia.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>A fő- és az infrastruktúra-csomópontok absztrakt módon el vannak távolaz Azure Kubernetes-szolgáltatás (AKS)?

Nem. Minden erőforrás, beleértve a fürtfőkiszolgálót is, az ügyfél-előfizetésben fut. Az ilyen típusú erőforrások csak olvasható erőforráscsoportba kerülnek.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Támogatott az Open Service Broker for Azure (OSBA) ?

Igen. Az OSBA az Azure Red Hat OpenShift használatával is használható. További [információ: Open Service Broker for Azure.](https://github.com/Azure/open-service-broker-azure#openshift-project-template)

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Próbálok egy másik előfizetésben lévő virtuális hálózatba `Failed to get vnet CIDR` beilleszkedni, de hibaüzenetet kapunk.

A virtuális hálózattal rendelkező előfizetésben győződjön meg arról, hogy regisztrálja `Microsoft.ContainerService` a`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Mi az Azure Red Hat OpenShift (ARO) karbantartási folyamat?

Az ARO karbantartásának három típusa van: frissítések, etcd adatok biztonsági mentése és visszaállítása, valamint a felhőszolgáltató által kezdeményezett karbantartás.

+ Upgrades include software upgrades and CVEs. A CVE szervizelése `yum update` indításkor történik, és azonnali csökkentést biztosít.  Ezzel párhuzamosan egy új lemezkép jön létre a jövőbeli fürt létrehozása.

+ Az etcd-adatok biztonsági mentése és kezelése olyan automatizált folyamat, amely a művelettől függően fürtleállást igényelhet. Ha az etcd adatbázis visszaállítása egy biztonsági mentés lesz állásidő. Mi biztonsági másolatot készíteni etcd óránként, és megtartja az elmúlt 6 óra mentést.

+ A felhőszolgáltató által kezdeményezett karbantartás magában foglalja a hálózatot, a tárolást és a regionális kimaradásokat. A karbantartás a felhőszolgáltatótól függ, és a szolgáltató által biztosított frissítésektől függ.

## <a name="what-is-the-general-upgrade-process"></a>Mi az általános frissítési folyamat?

A frissítés futtatásának biztonságos folyamatnak kell lennie, és nem zavarhatja meg a fürtszolgáltatásokat. Az SRE akkor indíthatja el a frissítési folyamatot, ha új verziók érhetők el, vagy ha még folyamatban vannak a cve-k.

Az elérhető frissítéseket szakaszkörnyezetben teszteli, majd éles fürtökre alkalmazza. Alkalmazáskor egy új csomópont ideiglenesen hozzáadódik, és a csomópontok rotációs módon frissülnek, így a podok fenntartják a replikaszámát. Az ajánlott eljárások követésével minimális vagy semmilyen állásidőt biztosít.

A függőben lévő frissítés vagy frissítés súlyosságától függően a folyamat annyiban eltérő lehet, mivel a frissítések gyorsan alkalmazhatók a szolgáltatás CVE-nek való kitettségének csökkentése érdekében. Egy új lemezkép aszinkron módon épül fel, tesztelésre kerül, és fürtfrissítésként lesz elérhető. Más mint amit, nincs különbség a sürgősségi és a tervezett karbantartás. A tervezett karbantartás nincs előre ütemezve az ügyféllel.

Az értesítéseket ICM-en és e-mailben is el lehet küldeni, ha az ügyféllel való kommunikációra van szükség.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Mi a helyzet a vészhelyzeti vs. tervezett karbantartási időszakokkal?

Nem teszünk különbséget a két fajta karbantartás között. Csapataink a nap 24 órájában, a 365.000 óra korára állnak rendelkezésre, és nem használnak hagyományos ütemezett "munkaidőn kívüli" karbantartási ablakokat.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Hogyan frissítik a gazdaoperációs rendszert és az OpenShift szoftvert?

A gazdaoperációs rendszer és az OpenShift szoftver az általános frissítési és képépítési folyamatunkon keresztül frissül.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Mi a frissített csomópont újraindításának folyamata?

Ezt a frissítés részeként kell kezelni.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Az adatok tárolása etcd titkosított ARO?

Ez nem titkosítva a etcd szinten. A bekapcsolási lehetőség jelenleg nem támogatott. Az OpenShift támogatja ezt a funkciót, de mérnöki erőfeszítésekre van szükség ahhoz, hogy az megjelenjen az útitervben. Az adatok titkosítása lemezszinten történik. További információt az [Adattárolási rétegen tárolt adatok titkosítása](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) című dokumentumban talál.

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Az alapul szolgáló virtuális gépek naplói streamelhetők egy ügyfélnapló-elemző rendszerbe?

A Syslog, a docker-naplók, a napló és a dmesg kezelése a felügyelt szolgáltatás, és nem vannak kitéve az ügyfelek.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Hogyan férhet hozzá egy ügyfél olyan metrikákhoz, mint a CPU/memória a csomópont szintjén, hogy lépéseket tegyen a méretezéshez, a hibakeresési problémákhoz stb. Nem tudok úgy `kubectl top` tűnni, hogy fut egy ARO klaszter.

Az ügyfelek a csomópont szintjén érhetik el a CPU/memória metrikákat a parancs `oc adm top nodes` vagy az ügyfél-rendszergazdai fürtszerepkör `kubectl top nodes` használatával.  Az ügyfelek a CPU/memória mérőszámait is elérhetik `pods` a `oc adm top pods``kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Mi az ARO alapértelmezett pod-ütemező konfigurációja?

Az ARO az OpenShift ben szállítható alapértelmezett ütemezőt használja. Van néhány további mechanizmusok, amelyek nem támogatott ARO. További részletekért tekintse meg [az alapértelmezett ütemező dokumentációját](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) és a [fő ütemező dokumentációját.](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json)

A speciális/egyéni ütemezés jelenleg nem támogatott. További részleteket az [Ütemezés dokumentációjában](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) talál.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Ha a központi telepítés, hogyan Azure-tartalék tartományok pod elhelyezése annak biztosítása érdekében, hogy a szolgáltatás összes podok nem kap kiütötte egy hiba egyetlen tartalék tartományban?

Alapértelmezés szerint öt tartalék tartomány van, ha virtuálisgép-méretezési készleteket használ az Azure-ban. A méretezési csoport minden egyes virtuálisgép-példánya ezen tartalék tartományok egyikébe kerül. Ez biztosítja, hogy a fürt számítási csomópontjaira telepített alkalmazások külön tartalék tartományokba kerülnek.

További részletekért [tekintse meg a megfelelő számú tartalék tartomány t a virtuálisgép-méretezési csoporthoz.](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains)

## <a name="is-there-a-way-to-manage-pod-placement"></a>Van mód a pod elhelyezésének kezelésére?

Az ügyfelek képesek csomópontokat leszerezni és címkéket megtekinteni ügyfél-rendszergazdaként.  Ez leadja a módját, hogy a méretezési csoportban lévő virtuális gépeket célozza meg.

Bizonyos címkék használata esetén körültekintően kell eljárni:

- Az állomásnév nem használható. A gazdanév gyakran elfordul a frissítésekkel és a frissítésekkel, és garantáltan megváltozik.

- Ha az ügyfél egy adott címkékre vagy üzembe helyezési stratégiára vonatkozó kérést kér, ez megvalósítható, de mérnöki erőfeszítéseket igényel, és ma nem támogatott.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Mi a podok maximális száma egy ARO-fürtben?Mi a podok maximális száma csomópontonként az ARO-ban?

 Az Azure Red Hat OpenShift 3.11 csomópontonként50 podos korláttal rendelkezik, és az [ARO 20 számítási csomópontkorláttal rendelkezik,](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)így az ARO-fürtben támogatott podok maximális számát 50*20 = 1000-re korlátozza.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Megadhatunk IP-tartományokat a privát virtuális hálózaton való üzembe helyezéshez, elkerülve az összecsapásokat más vállalati virtuális vállalatokkal, miután társviszonyba került?

Az Azure Red Hat OpenShift támogatja a virtuális hálózat társviszony-létesítését, és lehetővé teszi az ügyfél számára, hogy egy virtuális hálózatot és egy vNET CIDR-t biztosítson, amelyben az OpenShift-hálózat működni fog.

Az ARO által létrehozott virtuális hálózat védett lesz, és nem fogadja el a konfigurációs módosításokat. A társviszonyban lévő virtuális hálózatot az ügyfél szabályozza, és az előfizetésben található.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>A fürt egy ügyfél-előfizetésben található? 

Az Azure felügyelt alkalmazás él egy zárolt erőforráscsoport az ügyfél-előfizetéssel. Az ügyfél megtekintheti az RG objektumait, de nem módosíthatja.

## <a name="is-the-sdn-module-configurable"></a>Konfigurálható az SDN modul?

Az SDN openshift-ovs-networkpolicy, és nem konfigurálható.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Milyen UNIX-jogok érhetők el (az IaaS-ben) a Masters/Infra/App Nodes számára?

Nem vonatkozik erre az ajánlatra. A csomóponthoz való hozzáférés tilos.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Milyen OCP jogokkal rendelkezünk? Fürt-admin? Projekt-admin?

További információt az Azure Red Hat OpenShift [fürtfelügyelet – áttekintés című témakörben talál.](https://docs.openshift.com/aro/admin_guide/index.html)

## <a name="which-kind-of-federation-with-ldap"></a>Milyen összevonásaz LDAP-val?

Ez az Azure AD-integráción keresztül érhető el. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Van-e olyan elem, amelyet az ARO megoszt más ügyfelekkel? Vagy minden független?

Minden Egyes Azure Red Hat OpenShift-fürt egy adott ügyfélnek van szentelve, és az ügyfél előfizetésén belül él. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Választhatunk-e bármilyen állandó tárolási megoldást, például az OCS-t? 

Két tárolási osztály közül választhat: Azure Disk és az Azure File.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Hogyan történik a fürt frissítése (beleértve a biztonsági rések miatt a szakokat és a kiskorúakat is)?

Lásd: [Mi az általános frissítési folyamat?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Milyen Azure Load balancer által használt ARO?Ez standard vagy basic, és ez konfigurálható?

Az ARO standard Azure load balancer-t használ, és nem konfigurálható.

## <a name="can-aro-use-netapp-based-storage"></a>Használhatja az ARO a NetApp-alapú tárhelyet?

Jelenleg az egyetlen támogatott tárolási lehetőségek az Azure Disk és az Azure File storage osztályok. 


