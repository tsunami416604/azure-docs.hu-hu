---
title: Gyakori kérdések
description: Választ ad az Azure VMware megoldással kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 7b4abc2d711a3da6a6df125854759e083d7e04a7
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817836"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-preview"></a>Gyakran ismételt kérdések az Azure VMware Solution előzetes verziójáról

Válaszok az Azure VMware megoldással kapcsolatos gyakori kérdésekre.

## <a name="general"></a>Általános

### <a name="what-is-azure-vmware-solution"></a>Mi az az Azure VMware Solution?

Mivel a vállalatok az informatikai modernizációs stratégiákat követve javítják az üzleti rugalmasságot, csökkentik a költségeket, és felgyorsítják az innovációt, a hibrid felhőalapú platformok az ügyfelek digitális átalakításának kulcsfontosságú segítői jelentek meg. Az Azure VMware megoldás a VMware szoftveres adatközpont-(SDDC-) szoftverét kombinálja Microsoft Azure globális felhőalapú szolgáltatás-ökoszisztémával. Az Azure VMware megoldás a teljesítményre, a rendelkezésre állásra, a biztonságra és a megfelelőségre vonatkozó követelmények teljesítése érdekében van kezelve.

## <a name="azure-vmware-solution-service"></a>Azure VMware megoldás szolgáltatás

### <a name="where-is-azure-vmware-solution-available-today"></a>Hol érhető el a jelenleg elérhető Azure VMware-megoldás?

A szolgáltatás folyamatosan bővül az új régiókban, ezért további részletekért tekintse meg a [legújabb szolgáltatás elérhetőségi információit](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) . 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Az Azure VMware-megoldás példányain futó munkaterhelések az Azure-szolgáltatásokkal is felhasználhatók vagy integrálva vannak?

Minden Azure-szolgáltatás elérhető lesz az Azure VMware megoldás ügyfelei számára. Az egyes szolgáltatások teljesítmény-és rendelkezésre állási korlátozásait eseti alapon kell kezelni.

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Használhatom ugyanazt az eszközt, amelyet most használok a saját Felhőbeli erőforrások kezeléséhez?

Igen. A Azure Portal az üzembe helyezéshez és számos felügyeleti művelethez használatos. a vCenter és a NSX Manager a vSphere és a NSX-T erőforrások kezelésére szolgál.

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Kezelhetem a helyszíni vCenter rendelkező privát felhőket?

Az Azure VMware-megoldás elindítása esetén egyetlen felügyeleti környezetet sem támogat a helyszíni és a saját felhőalapú környezetekben. A privát Felhőbeli fürtöket a vCenter és a NSX Manager fogja kezelni a privát felhőben.

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Használhatom a helyszíni vRealize Suite szolgáltatást? 

Bizonyos integrációk és használati esetek eseti alapon is kiértékelhető.

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Áttelepíthetem a vSphere virtuális gépeket a helyszíni környezetből az Azure VMware-megoldás privát felhőkbe?

Igen. A VM-Migrálás és a vMotion segítségével a virtuális gépek áthelyezhetők a privát felhőbe, ha a standard szintű vCenter [vMotion](https://kb.vmware.com/s/article/210695) teljesülnek.

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>A vSphere adott verziója szükséges a helyszíni környezetekben?

Mivel az összes felhőalapú környezet a HCX, a vSphere 5,5-es vagy újabb verziójával rendelkezik a helyszíni környezetekben a vMotion számára.

### <a name="what-does-the-change-control-process-look-like"></a>Mire hasonlít a változás-ellenőrzési folyamat?

A szolgáltatásban végzett frissítések a Microsoft Azure szokásos módosítás-felügyeleti folyamatát követik. Az ügyfelek felelősek a munkaterhelés-felügyeleti feladatokért és a kapcsolódó módosítási felügyeleti folyamatokkal kapcsolatban.

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Miben különbözik a CloudSimple Azure VMware-megoldástól?

Az új Azure VMware-megoldással a Microsoft és a VMware közvetlen felhőalapú szolgáltatói partnerséggel rendelkezik. Az új megoldást a Microsoft tervezte, építi és támogatja, és a VMware támogatja. Építészeti szempontból a megoldások konzisztensek, és a VMware Technology stack egy Azure dedikált infrastruktúrán fut.

### <a name="if-im-an-existing-azure-vmware-solution-customer-what-does-this-preview-mean-for-me"></a>Ha meglévő Azure VMware-megoldási ügyfél vagyok, mit jelent ez az előzetes verzió?

A CloudSimple nem változtatja meg a meglévő Azure VMware-megoldást. Továbbra is támogatjuk a megoldást az Azure-ban. Az Azure VMware-megoldást szolgáltatói szerződés [(SLA)](https://aka.ms/CSVMwareSLA)támogatja. Az ügyfeleknek továbbra is használniuk kell a szolgáltatást az éles számítási feladatokhoz; Ez egy elérhető megoldás a [Microsoft szolgáltatási feltételeinek](https://azure.microsoft.com/support/legal/)hatálya alá.

### <a name="can-i-migrate-from-azure-vmware-solution-by-cloudsimple-to-this-new-solution"></a>Áttelepíthetem az Azure VMware-megoldásról az új megoldás CloudSimple?

Igen, az Azure VMware-megoldás támogatja az áttelepítést a jól ismert VMware-eszközökkel, például a HCX-vel. Az új megoldásba való Migrálás iránt érdeklődő ügyfeleknek a Microsoft-fiók csapatával megismerheti a lehetőségeket és a rendelkezésre álló támogatást.



## <a name="compute-network-and-storage"></a>Számítás, hálózat és tárterület

### <a name="is-there-more-than-one-type-of-host-available"></a>Több típusú gazdagép is elérhető?

A gazdagépnek csak egy típusa érhető el.

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Mik a CPU-specifikációk az egyes típusú gazdagépeken?

A kiszolgálók kettős 18 Magos 2,3 GHz-es Intel processzorokkal rendelkeznek.

### <a name="how-much-memory-is-in-each-host"></a>Mennyi memória van az egyes gazdagépeken?

A kiszolgálók 576 GB RAM-mal rendelkeznek.

### <a name="what-is-the-storage-capacity-of-each-host"></a>Mi az egyes gazdagépek tárolási kapacitása?

Az ESXi-gazdagépek két vSAN diskgroups rendelkeznek, amelyek kapacitása 15,2 TB, a 3,2 TB NVMe cache-réteg (1,6 TB az egyes diskgroup).

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Mennyi hálózati sávszélesség érhető el az egyes ESXi-gazdagépeken?

Mindegyik ESXi-gazdagép az Azure VMware-megoldás 4 25 Gbps hálózati adapterrel van konfigurálva, és két hálózati adapterrel van kiépítve az ESXi rendszerforgalmához és két, a számítási feladatok forgalmára kiépített hálózati adapterhez. 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Titkosítva vannak-e a vSAN-adattárolókban tárolt adatok a nyugalmi állapotban?

Igen, a rendszer alapértelmezés szerint titkosítja az összes vSAN-adatfájlt a Azure Key Vaultban tárolt kulcsok használatával.

## <a name="hosts-clusters-and-private-clouds"></a>Gazdagépek, fürtök és privát felhők

### <a name="is-the-underlying-infrastructure-shared"></a>A mögöttes infrastruktúra meg van osztva?

Nem, a saját Felhőbeli gazdagépek és fürtök dedikált és biztonságos törlésre kerülnek a használat előtt és után.

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>A gazdagépek minimális és maximális száma egy fürtön

A fürtök 3 és 16 ESXi-gazdagép között is méretezhetők. A próbaverziós fürtök három gazdagépre korlátozódnak.

### <a name="can-i-scale-my-private-cloud-clusters"></a>Méretezhetők a saját felhőalapú fürtök?

Igen, a fürtök az ESXi-gazdagépek minimális és maximális száma között méretezhetők. A próbaverziós fürtök három gazdagépre korlátozódnak.

### <a name="what-are-trial-clusters"></a>Mik azok a próbaverziós fürtök?

A próbaverziós fürtök három, az Azure VMware-megoldás privát felhőkének egy hónapos értékeléséhez használt gazdagép-fürt.

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Használhatok magas szintű gazdagépeket a próbaverziós fürtökhöz?

Nem. A csúcsminőségű ESXi-gazdagépek az üzemi fürtökön való használatra vannak fenntartva.

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware-megoldás és VMware szoftver

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>A VMware-szoftverek milyen verzióit használják a privát felhők?

A privát felhők a vSphere 6,7, a vSAN 6,7, a HCX és a NSX-T 2,5-es verzióját használják.  

### <a name="do-private-clouds-use-vmware-nsx"></a>A privát felhők a VMware NSX-t használják?

Igen, a NSX-T 2,5 az Azure VMware megoldás privát felhőkben definiált szoftveres hálózatkezeléshez használatos.

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Használhatom a VMware NSX-V-t egy privát felhőben?

Nem. A NSX-T a NSX egyetlen támogatott verziója.

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>NSX szükséges a helyszíni környezetekben vagy a privát felhőhöz csatlakozó hálózatokban?

Nem, nem szükséges a helyszíni NSX használata.

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Mi a VMware-szoftverek frissítési és frissítési ütemterve egy privát felhőben?

A Private Cloud szoftvercsomag frissítései a szoftvernek a VMware szoftverből származó legújabb kiadásának egy-egy verzióján belül maradnak. A Private Cloud Software-verziók eltérőek lehetnek az egyes szoftver-összetevők legújabb verzióiban (ESXi, NSX-T, vCenter, vSAN).

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Milyen gyakran frissül a Private Cloud Software stack?

A privát felhőalapú szoftverek olyan ütemterv szerint frissülnek, amely nyomon követi a VMware-től származó szoftveres csomag kiadását. A saját felhő nem igényel állásidőt a frissítésekhez.

## <a name="connectivity"></a>Kapcsolatok

### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Milyen hálózati IP-cím megtervezésére van szükség a privát felhők helyszíni környezetekben való beépítéséhez?

Az Azure VMware-megoldás saját Felhőbeli üzembe helyezéséhez magánhálózat/22 címterület szükséges. Ez a magánhálózati címterület nem fedi át az előfizetésben lévő más virtuális hálózatokat, illetve a helyszíni hálózatokat.
 
### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Hogyan csatlakozni a helyszíni környezetekről egy Azure VMware-megoldás privát felhőbe?

A szolgáltatáshoz a következő két módszer egyikét használhatja: 

- Egy olyan virtuális géppel vagy Application Gateway-mel, amely a ExpressRoute-on keresztül a privát felhőbe van társítva.
- ExpressRoute-Global Reach a helyszíni adatközpontból egy Azure ExpressRoute-áramkörre.

### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Hogyan csatlakoztasson egy számítási feladatot tartalmazó virtuális gépet az internethez vagy egy Azure szolgáltatásbeli végponthoz?

A Azure Portalban engedélyezze az internetkapcsolatot egy privát felhőben. A NSX-T kezelőjével hozzon létre egy NSX-T T1 útválasztót és egy logikai kapcsolót. Ezután a vCenter használatával telepítheti a virtuális gépet a logikai kapcsoló által definiált hálózati szegmensen. A virtuális gép hálózati hozzáféréssel fog rendelkezni az internethez és az Azure-szolgáltatásokhoz.

### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Korlátozni kell az internetről a virtuális gépekre való hozzáférést a privát felhőben található logikai hálózatokon?

Nem. Az internetről közvetlenül a privát felhőkbe bejövő hálózati forgalom nem engedélyezett.

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Korlátozni kell az internetről a virtuális gépekről a logikai hálózatokon az internetre való hozzáférést?

Igen. A NSX-T Manager használatával olyan tűzfalat kell létrehoznia, amely korlátozza a virtuális gépek internet-hozzáférését.

## <a name="accounts-and-privileges"></a>Fiókok és jogosultságok

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Milyen fiókokat és jogosultságokat kapok az új Azure VMware-megoldás privát felhővel?

Hitelesítő adatokat ad meg egy cloudadmin-felhasználó számára a vCenter-ben, és rendszergazdai hozzáféréssel rendelkezik a NSX-T kezelőjében. Létezik egy CloudAdmin-csoport is, amely a Azure Active Directory beépítésére használható. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

### <a name="can-have-administrator-access-to-esxi-hosts"></a>Rendszergazdai hozzáféréssel rendelkezhet az ESXi-gazdagépekhez?

Nem, az ESXi-hez való rendszergazdai hozzáférés korlátozott a megoldás biztonsági követelményeinek kielégítése érdekében.

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Milyen jogosultságokkal és engedélyekkel rendelkezem a vCenter?

CloudAdmin-csoport jogosultságokkal fog rendelkezni. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Milyen jogosultságokkal és engedélyekkel rendelkezem a NSX-T kezelőn?

A NSX-T teljes körű rendszergazdai jogosultságokkal rendelkezik, és a szerepköralapú hozzáférés-vezérlést ugyanúgy kezelheti, mint a helyszíni NSX-T adatközpontot. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

> [!NOTE]
> A rendszer létrehoz egy T0-útválasztót, és konfigurálja a saját felhőalapú telepítés részeként. A logikai útválasztó vagy a NSX-T peremhálózati virtuális gépek bármely módosítása hatással lehet a privát felhőhöz való kapcsolódásra.

## <a name="billing-and-support"></a>Számlázás és támogatás

### <a name="how-will-i-be-billed-during-the-preview-of-azure-vmware-solution"></a>Hogyan történik az Azure VMware-megoldás előzetes verziójának számlázása?

Az előzetes verzióban az Azure VMware-megoldás számlázása havonta, utólagos elszámolású módon történik. A további lehetőségek az általános rendelkezésre állásban lesznek elérhetők.

### <a name="how-will-pricing-be-structured-during-the-preview-of-azure-vmware-solution"></a>Hogyan történik a díjszabás strukturálása az Azure VMware-megoldás előzetes verziójában?

A díjszabással kapcsolatos általános kérdésekben tekintse meg az Azure VMware megoldás [díjszabását](https://azure.microsoft.com/pricing/details/azure-vmware) ismertető oldalt. Az előzetes verzió díjszabása igény szerint érhető el, lépjen kapcsolatba a fiók csapatával, vagy kövesse a díjszabási oldalon található hivatkozást az értékesítéshez.

### <a name="who-supports-azure-vmware-solution"></a>Ki támogatja az Azure VMware-megoldást?

Az Azure VMware-megoldás támogatását a Microsoft továbbítja. Vegye figyelembe, hogy az előzetes verzióra vonatkozó irányelvek szerint a rendszer 9 és 5 óra között is biztosít támogatást hétfőtől péntekig. [Ebből a hivatkozásból](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) támogatási jegyet is létrehozhat

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Milyen fiókokra van szükségem egy Azure VMware-megoldás saját Felhőbeli létrehozásához?

Egy Azure-előfizetésben Azure-fiókra lesz szüksége.

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Hogyan az Azure VMware-megoldáshoz tartozó gazdagép-kvóta növelését?

* Szüksége lesz egy [Azure nagyvállalati Szerződésra (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) a Microsofttal.
* Egy Azure-előfizetésben Azure-fiókra lesz szüksége.

Az Azure VMware-megoldási erőforrás létrehozása előtt egy támogatási jegyet kell benyújtania a csomópontok lefoglalásához. Miután a támogatási csapat megkapja a kérést, akár öt munkanapot is igénybe vesz, hogy erősítse meg a kérését, és lefoglalja a csomópontjait. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több csomópontot szeretne lefoglalni, akkor ugyanezt a folyamatot kell megtennie.


1. A Azure Portal a Súgó és **támogatás**területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
   - **Probléma típusa:** Technikai
   - **Előfizetés:** Előfizetés kiválasztása
   - **Szolgáltatás:** Minden szolgáltatás > Azure VMware-megoldás
   - **Erőforrás:** Általános kérdés 
   - **Összefoglalás:** Kapacitás szükséges
   - **Probléma típusa:** Kapacitás-felügyeleti problémák
   - **Probléma altípusa:** Ügyfél iránti kérelem további gazdagép-kvótához/kapacitáshoz

1. A támogatási jegy **leírásában** a **részletek** lapon adja meg a következőt:

   - POC vagy éles üzem 
   - Régiónév
   - Csomópontok száma
   - Bármilyen más részlet

   >[!NOTE]
   >Az Azure VMware-megoldás legalább három csomópontot javasol a saját felhő és a redundancia N + 1 csomópontok kiépítéséhez. 

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.

   A támogatási képviselők akár öt munkanapot is igénybe vesznek, hogy erősítse a kérelmét.

   >[!IMPORTANT] 
   >Ha már rendelkezik egy meglévő Azure VMware-megoldással, és további csomópontokra van szüksége, vegye figyelembe, hogy öt munkanapra van szükség a csomópontok lefoglalásához. 

1. A csomópontok kiépítése előtt győződjön meg arról, hogy regisztrálja a **Microsoft. AVS** erőforrás-szolgáltatót a Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Az erőforrás-szolgáltató regisztrálásának további módjaiért tekintse meg az [Azure erőforrás-szolgáltatók és-típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
