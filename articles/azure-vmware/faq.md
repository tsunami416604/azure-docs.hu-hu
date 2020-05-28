---
title: Gyakori kérdések
description: Az Azure VMware-megoldással (AVS) kapcsolatos gyakori kérdésekre ad választ.
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 1649b5649bd18b7ab53f3cc0196d7dff0f6f5b2c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112684"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Gyakran ismételt kérdések az Azure VMware Solution (AVS) előzetes verziójáról

Válaszok az Azure VMware-megoldással (AVS) kapcsolatos gyakori kérdésekre.

## <a name="general"></a>Általános kérdések

**Mi az Azure VMware-megoldás (AVS)?**

Mivel a vállalatok az informatikai modernizációs stratégiákat követve javítják az üzleti rugalmasságot, csökkentik a költségeket, és felgyorsítják az innovációt, a hibrid felhőalapú platformok az ügyfelek digitális átalakításának kulcsfontosságú segítői jelentek meg. Az AVS a VMware szoftveres adatközpont (SDDC) szoftverét kombinálja Microsoft Azure globális felhőalapú szolgáltatás-ökoszisztémával. Az AVS-megoldás a teljesítményre, a rendelkezésre állásra, a biztonságra és a megfelelőségre vonatkozó követelmények teljesítéséhez van kezelve.

## <a name="avs-service"></a>AVS-szolgáltatás

**Hol érhető el még ma az AVS?**

Az előzetes verzió ideje alatt az Egyesült Államok keleti régiójában, Észak-Amerika és Amszterdamban, Nyugat-Európában érhető el.

**Az Azure VMware Solution-(AVS-) példányokban futó munkaterhelések az Azure-szolgáltatásokkal is felhasználhatók vagy integrálva vannak?**

Az összes Azure-szolgáltatás elérhető lesz az AVS-megoldás ügyfelei számára. Az egyes szolgáltatások teljesítmény-és rendelkezésre állási korlátozásait eseti alapon kell kezelni.

**Használhatom ugyanazt az eszközt, amelyet most használok a saját Felhőbeli erőforrások kezeléséhez?**

Igen. A Azure Portal az üzembe helyezéshez és számos felügyeleti művelethez használatos. a vCenter és a NSX Manager a vSphere és a NSX-T erőforrások kezelésére szolgál.

**Kezelhetem a helyszíni vCenter rendelkező privát felhőket?**

Indításkor az AVS nem támogat egyetlen felügyeleti környezetet a helyszíni és a saját felhőalapú környezetekben. A privát Felhőbeli fürtöket a vCenter és a NSX Manager fogja kezelni a privát felhőben.

**Használhatom a helyszíni vRealize Suite szolgáltatást?** 

Bizonyos integrációk és használati esetek eseti alapon is kiértékelhető.

**Áttelepíthetem a vSphere virtuális gépeket a helyszíni környezetből az AVS Private felhőkbe?**

Igen. A virtuális gépek áttelepítése és vMotion használatával a virtuális gépek áthelyezhetők egy privát felhőbe, ha a standard szintű Cross vCenter [vMotion-követelmények] [ https://kb.vmware.com/s/article/210695 ] teljesülnek.

**A vSphere adott verziója szükséges a helyszíni környezetekben?**

Mivel az összes felhőalapú környezet a HCX, a vSphere 5,5-es vagy újabb verziójával rendelkezik a helyszíni környezetekben a vMotion számára.

**Mire hasonlít a változás-ellenőrzési folyamat?**

A szolgáltatásban végzett frissítések a Microsoft Azure szokásos módosítás-felügyeleti folyamatát követik. Az ügyfelek felelősek a munkaterhelés-felügyeleti feladatokért és a kapcsolódó módosítási felügyeleti folyamatokkal kapcsolatban.

**Miben különbözik a CloudSimple Azure VMware-megoldástól?**

Az új Azure VMware-megoldással a Microsoft és a VMware közvetlen felhőalapú szolgáltatói partnerséggel rendelkezik. Az új megoldás teljes mértékben a Microsoft által készített, készített és támogatott, és a VMware támogatja. Építészeti szempontból a megoldások konzisztensek, és a VMware Technology stack egy Azure dedikált infrastruktúrán fut.

**Ha meglévő Azure VMware-megoldási ügyfél vagyok, mit jelent ez az előzetes verzió?**

A CloudSimple nem változtatja meg a meglévő Azure VMware-megoldást. Továbbra is támogatjuk a megoldást az Azure-ban. Az Azure VMware-megoldást szolgáltatói szerződés [(SLA)](https://aka.ms/CSVMwareSLA)támogatja. Az ügyfeleknek továbbra is használniuk kell a szolgáltatást az éles számítási feladatokhoz; Ez egy elérhető megoldás a [Microsoft szolgáltatási feltételeinek](https://azure.microsoft.com/support/legal/)hatálya alá.

**Áttelepíthetem az Azure VMware-megoldásról az új megoldás CloudSimple?**

Igen, az Azure VMware-megoldás támogatja az áttelepítést a jól ismert VMware-eszközökkel, például a HCX-vel. Az új megoldásba való Migrálás iránt érdeklődő ügyfeleknek a Microsoft-fiók csapatával együttműködve megismerheti a lehetőségeket és a rendelkezésre álló támogatást.



## <a name="compute-network-and-storage"></a>Számítás, hálózat és tárterület

**Több típusú gazdagép is elérhető?**

A gazdagépnek csak egy típusa érhető el.

**Mik a CPU-specifikációk az egyes típusú gazdagépeken?**

A kiszolgálók kettős 18 Magos 2,3 GHz-es Intel processzorokkal rendelkeznek.

**Mennyi memória van az egyes gazdagépeken?**

A kiszolgálók 576 GB RAM-mal rendelkeznek.

**Mi az egyes gazdagépek tárolási kapacitása?**

Az ESXi-gazdagépek két VSAN diskgroups rendelkeznek, amelyek kapacitása 15,2 TB, a 3,2 TB NVMe cache-réteg (1,6 TB az egyes diskgroup).

**Mennyi hálózati sávszélesség érhető el az egyes ESXi-gazdagépeken?**

Az ESXi-gazdagépek 25 GB/s-ig támogatják a csatlakozási sávszélességet.

**Titkosítva vannak-e a VSAN-adattárolókban tárolt adatok a nyugalmi állapotban?**

Igen, a rendszer alapértelmezés szerint titkosítja az összes VSAN-adatfájlt a Azure Key Vaultban tárolt kulcsok használatával.

## <a name="hosts-clusters-and-private-clouds"></a>Gazdagépek, fürtök és privát felhők

**A mögöttes infrastruktúra meg van osztva?**

Nem, a saját Felhőbeli gazdagépek és fürtök dedikált és biztonságos törlésre kerülnek a használat előtt és után.

**A gazdagépek minimális és maximális száma egy fürtön**

A fürtök 3 és 16 ESXi-gazdagép között is méretezhetők. A próbaverziós fürtök három gazdagépre korlátozódnak.

**Méretezhetők a saját felhőalapú fürtök?**

Igen, a fürtök az ESXi-gazdagépek minimális és maximális száma között méretezhetők. A próbaverziós fürtök három gazdagépre korlátozódnak.

**Mik azok a próbaverziós fürtök?**

A próbaverziós fürtök három, az AVS privát felhők egy hónapos értékeléséhez használt gazdagép-fürt.

**Használhatok magas szintű gazdagépeket a próbaverziós fürtökhöz?**

Nem. A csúcsminőségű ESXi-gazdagépek az üzemi fürtökön való használatra vannak fenntartva.

## <a name="avs-and-vmware-software"></a>AVS és VMware szoftverek

**A VMware-szoftverek milyen verzióit használják a privát felhők?**

A privát felhők a vSphere 6,7, a vSAN 6,7, a HCX és a NSX-T 2,5-es verzióját használják.  

**A privát felhők a VMware NSX-t használják?**

Igen, a NSX-T 2,5 az AVS privát felhőkben definiált szoftveres hálózatkezeléshez használatos.

**Használhatom a VMware NSX-V-t egy privát felhőben?**

Nem. A NSX-T a NSX egyetlen támogatott verziója.

**NSX szükséges a helyszíni környezetekben vagy a privát felhőhöz csatlakozó hálózatokban.**

Nem, nem szükséges a helyszíni NSX használata.

**Mi a VMware-szoftverek frissítési és frissítési ütemterve egy privát felhőben?**

A Private Cloud szoftvercsomag frissítései a szoftvernek a VMware szoftverből származó legújabb kiadásának egy-egy verzióján belül maradnak. A Private Cloud Software-verziók eltérőek lehetnek az egyes szoftver-összetevők legújabb verzióiban (ESXi, NSX-T, vCenter, VSAN).

**Milyen gyakran frissül a Private Cloud Software stack?**

A privát felhőalapú szoftverek olyan ütemterv szerint frissülnek, amely nyomon követi a VMware-től származó szoftveres csomag kiadását. A saját felhő nem igényel állásidőt a frissítésekhez.

## <a name="connectivity"></a>Kapcsolatok

**Milyen hálózati IP-cím megtervezésére van szükség a privát felhők helyszíni környezetekben való beépítéséhez?**

Az AVS Private Cloud telepítéséhez magánhálózat/22 címterület szükséges. Ez a magánhálózati címterület nem fedi át az előfizetésben lévő más virtuális hálózatokat, illetve a helyszíni hálózatokat.
 
**Hogyan csatlakozni a helyszíni környezetekhez egy AVS Private-felhőbe?**

A szolgáltatáshoz a következő két módszer egyikét használhatja: 

- Egy olyan virtuális géppel vagy Application Gateway-mel, amely a ExpressRoute-on keresztül a privát felhőbe van társítva.
- ExpressRoute-Global Reach a helyszíni adatközpontból egy Azure ExpressRoute-áramkörre.

**Hogyan csatlakoztasson egy számítási feladatot tartalmazó virtuális gépet az internethez vagy egy Azure szolgáltatásbeli végponthoz?**

A Azure Portalban engedélyezze az internetkapcsolatot egy privát felhőben. A NSX-T kezelőjével hozzon létre egy NSX-T T1 útválasztót és egy logikai kapcsolót. Ezután a vCenter használatával telepítheti a virtuális gépet a logikai kapcsoló által definiált hálózati szegmensen. A virtuális gép hálózati hozzáféréssel fog rendelkezni az internethez és az Azure-szolgáltatásokhoz.

**Korlátozni kell az internetről a virtuális gépekre való hozzáférést a privát felhőben található logikai hálózatokon?**

Nem. Az internetről közvetlenül a privát felhőkbe bejövő hálózati forgalom nem engedélyezett.

**Korlátozni kell az internetről a virtuális gépekről a logikai hálózatokon az internetre való hozzáférést?**

Igen. A NSX-T Manager használatával olyan tűzfalat kell létrehoznia, amely korlátozza a virtuális gépek internet-hozzáférését.

## <a name="accounts-and-privileges"></a>Fiókok és jogosultságok

**Milyen fiókokat és jogosultságokat kapok az új AVS Private Cloud-hoz?**

Hitelesítő adatokat ad meg egy cloudadmin-felhasználó számára a vCenter-ben, és rendszergazdai hozzáféréssel rendelkezik a NSX-T kezelőjében. Létezik egy CloudAdmin-csoport is, amely a Azure Active Directory beépítésére használható. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

**Rendszergazdai hozzáféréssel rendelkezhet az ESXi-gazdagépekhez?**

Nem, az ESXi-hez való rendszergazdai hozzáférés korlátozott a megoldás biztonsági követelményeinek kielégítése érdekében.

**Milyen jogosultságokkal és engedélyekkel rendelkezem a vCenter?**

CloudAdmin-csoport jogosultságokkal fog rendelkezni. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

**Milyen jogosultságokkal és engedélyekkel rendelkezem a NSX-T kezelőn?**

A NSX-T teljes körű rendszergazdai jogosultságokkal rendelkezik, és a szerepköralapú hozzáférés-vezérlést ugyanúgy kezelheti, mint a helyszíni NSX-T adatközpontot. További információ: [hozzáférés és identitás fogalmai](concepts-identity.md).

> [!NOTE]
> A rendszer létrehoz egy T0-útválasztót, és konfigurálja a saját felhőalapú telepítés részeként. A logikai útválasztó vagy a NSX-T peremhálózati virtuális gépek bármely módosítása hatással lehet a privát felhőhöz való kapcsolódásra.

## <a name="billing-and-support"></a>Számlázás és támogatás

**Hogyan történik a számlázás az AVS előzetes verziójában**

Az előzetes verzióban az AVS számlázása az utólagos elszámolású szolgáltatásokban havonta történik. A további lehetőségek az általános rendelkezésre állásban lesznek elérhetők.

**Hogyan történik az árképzés felépítése az AVS előzetes verziójában?**

A díjszabással kapcsolatos általános kérdésekben tekintse meg az Azure VMware megoldás [díjszabását](https://azure.microsoft.com/pricing/details/azure-vmware) ismertető oldalt. Az előzetes verzió díjszabása igény szerint érhető el, forduljon a fiók csapatához, vagy kövesse a díjszabási oldalon található hivatkozást az értékesítésekhez.

**Ki támogatja az AVS-t?**

Az AVS támogatását a Microsoft továbbítja. Kérjük, vegye figyelembe, hogy az előzetes verzióra vonatkozó irányelvek alapján a rendszer 9 és 5 óra között biztosítja a támogatást a hétfőtől péntekig. [Ebből a hivatkozásból](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) támogatási jegyet is létrehozhat

**Milyen fiókokra van szükségem egy AVS Private Cloud létrehozásához?**

Egy Azure-előfizetésben Azure-fiókra lesz szüksége.

<a name="how-to-request-a-quota-increase-for-avs"></a>**Hogyan az Azure VMware-megoldáshoz tartozó gazdagép-kvóta növelését?**

Egy [támogatási kérelem elküldésével](..\azure-portal\supportability\how-to-create-azure-support-request.md)kérheti a kvóta növelését. A kvóta-felügyeleti csapat kiértékeli a kérelmet, és három munkanapon belül jóváhagyja azt.  

> [!IMPORTANT]
> A kvóta növelésének megkezdése előtt győződjön meg arról, hogy regisztrálja a **Microsoft. AVS** erőforrás-szolgáltatót a Azure Portal.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> Az erőforrás-szolgáltató regisztrálásának további módjaiért tekintse meg az [Azure erőforrás-szolgáltatók és-típusok](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)című témakört.

1. A Azure Portal a Súgó és **támogatás**területen hozzon létre egy **új támogatási kérést** , és adja meg a következő információkat a jegyhez:
   - **Probléma típusa:** Technikai
   - **Előfizetés:** Az előfizetés azonosítója
   - **Szolgáltatás:**  Azure VMware-megoldás 
   - **Összefoglalás:** Kvóta növekedése
   - **Probléma típusa:** Kapacitás-felügyeleti problémák
   - **Probléma altípusa:** Ügyfél iránti kérelem további gazdagép-kvótához/kapacitáshoz

1. A támogatási jegy leírásában a Részletek lapon adja meg a következőket:
   - További csomópontok száma   
   - Csomópont SKU
   - Régió

   > [!NOTE] 
   > Alapértelmezés szerint a rendszer legalább négy csomópontot biztosít.

1. A kérelem elküldéséhez kattintson a **felülvizsgálat + létrehozás** gombra.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
