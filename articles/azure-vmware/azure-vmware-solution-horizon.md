---
title: Horizont üzembe helyezése az Azure VMware-megoldáson
description: Ismerje meg, hogyan helyezhet üzembe VMware horizontot az Azure VMware-megoldáson.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: bda4be049e360670cb7038bfbb3070c2a5f262c4
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91729049"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Horizont üzembe helyezése az Azure VMware-megoldáson 

>[!NOTE]
>Ez a dokumentum a VMware Horizon termékre koncentrál. Ez korábbi nevén a Horizon 7, mielőtt a termék neve a Horizon 7-es verzióról a horizontra módosult. A Horizon egy másik megoldás, mint a Horizon Cloud az Azure-ban, de vannak megosztott összetevők. Az Azure VMware-megoldás fő előnyei közé tartozik az egyszerűbb méretezési módszer és a VMware Cloud Foundation Management integrálva a Azure Portalba.

A [VMware Horizon](https://www.vmware.com/products/horizon.html)® egy virtuális asztali és alkalmazások platform, amely az adatközpontban fut, és egyszerű és központosított felügyeletet biztosít. Virtuális asztalokat és alkalmazásokat biztosít a végfelhasználók számára bármilyen eszközön, bárhol. A Horizon lehetővé teszi a Windows rendszerű virtuális asztalok, linuxos virtuális asztalok, Távoli asztal-kiszolgáló (RDS) által üzemeltetett alkalmazások, asztali számítógépek és fizikai gépek kapcsolatainak létrehozását és közvetítését.

Itt kifejezetten a horizont Azure VMware-megoldáson való üzembe helyezésére koncentrálunk. A VMware horizonttal kapcsolatos általános információkért tekintse meg a horizont üzemi dokumentációját:

* [Mi az a VMware Horizon?](https://www.vmware.com/products/horizon.html)

* [További információ a VMware Horizontról](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Horizont hivatkozási architektúrája](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

A Horizon Azure VMware-megoldással való bemutatása mostantól két virtuális asztali infrastruktúra-(VDI-) megoldást kínál az Azure platformon. A következő ábra összefoglalja a legfontosabb különbségeket a magas szinten.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Az Azure-beli VMware-megoldás és a Horizon-felhő közötti különbségek az Azure-ban" border="false":::

A Horizon 2006 és újabb verziói a Horizon 8 kiadási vonalon a helyszíni és az Azure VMware megoldás üzembe helyezését is támogatják. Van néhány, a helyszínen támogatott, az Azure VMware-megoldáson kívüli szolgáltatás. A horizont ökoszisztémájában további termékek is támogatottak. További információ: [szolgáltatások paritása és együttműködési képesség](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Horizont üzembe helyezése hibrid felhőben

A horizontot hibrid felhőalapú környezetben is üzembe helyezheti, ha a Horizon Cloud Pod Architecture (CPA) használatával összeköti a helyszíni adatközpontokat és az Azure-adatközpontokat. A CPA jellemzően az üzemelő példány vertikális felskálázására, hibrid felhő kialakítására, valamint redundancia biztosítására szolgál az üzletmenet folytonossága és a vész-helyreállítás érdekében. A VMware Horizon üzletmenet folytonossági útmutatójának részletes ismertetését lásd: a [meglévő Horizon 7-környezetek kiterjesztése](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>A CPA nem kiterjesztett üzembe helyezés; mindegyik Horizon Pod különálló, és az egyes hüvelyekhez tartozó összes kapcsolódási kiszolgálónak egyetlen helyen kell lennie, és ugyanazon a szórási tartományon kell futnia a hálózat szemszögéből.

A helyszíni vagy privát adatközponthoz hasonlóan a horizont is üzembe helyezhető egy Azure VMware-megoldás saját felhőben. A következő részben ismertetjük a főbb különbségeket a helyszíni és az Azure VMware-megoldás üzembe helyezésével kapcsolatban.

Az Azure Private Cloud fogalma megegyezik a VMware SDDC, amely általában a horizont dokumentációjában használatos. A dokumentum hátralévő része az Azure Private Cloud és a VMware SDDC feltételeit is felhasználja.

Az előfizetési licencek kezeléséhez a Horizon Cloud Connector szükséges az Azure VMware-megoldásban. A Cloud Connector üzembe helyezhető az Azure Virtual Network a Horizon-kapcsolatok kiszolgálói mellett.

>[!IMPORTANT]
>A horizont Control Plant Azure VMware-megoldáson alapuló távlati támogatása még nem érhető el. Ügyeljen arra, hogy letöltse a Horizon Cloud Connector VHD-verzióját.

## <a name="vcenter-cloud-admin-role"></a>vCenter Felhőbeli rendszergazdai szerepkör

Mivel az Azure VMware megoldás egy SDDC szolgáltatás, és az Azure felügyeli az Azure VMware-megoldás SDDC életciklusát, az Azure VMware-megoldáson alapuló vCenter-engedélyezési modellt a tervezés korlátozza.

Az ügyfeleknek a felhő-rendszergazdai szerepkör használatára van szükségük, amely korlátozott vCenter engedélyekkel rendelkezik. A Horizon termék úgy lett módosítva, hogy működjön együtt a felhőalapú rendszergazdai szerepkörrel az Azure VMware-megoldáson, különösen:

* Az azonnali klónozási kiépítés úgy lett módosítva, hogy az Azure VMware-megoldáson fusson.

* Egy adott vSAN szabályzat (VMware_Horizon) lett létrehozva az Azure VMware-megoldásban a horizonttal való együttműködéshez, amelynek elérhetőnek kell lennie, és a horizonton üzembe helyezett SDDCs kell használni.

* a vSphere-alapú olvasási gyorsítótár (CBRC), más néven tárolási gyorssegéd, le van tiltva az Azure VMware-megoldás futtatásakor.

>[!IMPORTANT]
>A CBRC nem lehet újra bekapcsolni.

>[!NOTE]
>Az Azure VMware-megoldás automatikusan beállítja a megadott horizont-beállításokat, ha a Horizon 2006 (más néven Horizon 8) és újabb verziókat a Horizon 8 ágban helyezi üzembe, és kiválasztja az **Azure** lehetőséget a Horizon-kapcsolatok kiszolgálói telepítőben.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Az Azure VMware megoldás üzembe helyezési architektúrájának horizontja

Egy tipikus horizont architektúra-kialakítás egy Pod és egy blokkos stratégiát használ. A blokk egyetlen vCenter, míg több blokk kombinálja a pod-t. A Horizon Pod a szervezeti egység, amelyet a horizont skálázhatósági korlátai határoznak meg. A Horizon Pod külön felügyeleti portált tartalmaz, ezért a szokásos tervezési gyakorlat a hüvelyek számának csökkentése.

Minden felhő saját hálózati kapcsolati sémával rendelkezik. A VMware SDDC Networking/NSX Edge szolgáltatással kombinálva az Azure VMware megoldás hálózati kapcsolata egyedi követelményeket támaszt a helyi környezettől eltérő horizont üzembe helyezéséhez.

Mindegyik Azure Private Cloud/SDDC képes a 4 000 asztali vagy alkalmazás-munkamenetek kezelésére, amelyek a következőket feltételezik:

* A munkaterhelés-forgalom a LoginVSI Task Worker-profilhoz igazodik.

* A rendszer csak a protokollok forgalmát veszi figyelembe, felhasználói adatokat nem.

* Az NSX Edge nagy méretűre van konfigurálva.

>[!NOTE]
>A munkaterhelés-profil és a szükségletek eltérőek lehetnek, így az eredmények a használati esettől függően változhatnak. A felhasználói adatok kötetei csökkenthetik a méretezési korlátokat a számítási feladatok kontextusában. Ennek megfelelően tervezze meg a telepítést. További információkért tekintse meg a méretezési irányelvek az [Azure VMware-megoldás gazdagépek a horizonton üzembe helyezéséhez](#size-azure-vmware-solution-hosts-for-horizon-deployments) című szakaszt.

Az Azure Private Cloud/SDDC maximális korlátja miatt ajánlott üzembe helyezési architektúra, ahol a Horizon-kapcsolati kiszolgálók és a VMware Unified Access Gateway (UAGs) az Azure Virtual Networkon belül futnak. Ez gyakorlatilag minden egyes Azure-beli privát felhőt/SDDC bekapcsol egy blokkba. Ez viszont maximalizálja az Azure VMware-megoldáson futó horizont méretezhetőségét.

Az Azure Virtual Networkról az Azure privát felhőkre/SDDCs való kapcsolódást ExpressRoute FastPath kell konfigurálni. Az alábbi ábrán egy alapszintű Horizon Pod üzemelő példány látható.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Az Azure-beli VMware-megoldás és a Horizon-felhő közötti különbségek az Azure-ban" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Hálózati kapcsolat az Azure VMware-megoldás méretezési horizontjának használatával

Ez a szakasz a hálózati architektúrát magas szinten mutatja be az Azure VMware-megoldás távlati skálázásával, néhány gyakori telepítési példával. A hangsúly itt kifejezetten a kritikus hálózati elemekre vonatkozik.

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Egy Horizon Pod az Azure VMware-megoldásban

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Az Azure-beli VMware-megoldás és a Horizon-felhő közötti különbségek az Azure-ban" border="false":::

Egyetlen Horizontos Pod a legegyenesebb továbbítási forgatókönyv. Ebben a példában úgy dönt, hogy csak egy Horizon Pod-t kíván üzembe helyezni az USA keleti régiójában. Mivel minden egyes privát felhő-vagy SDDC körülbelül a 4 000 asztali munkamenetek forgalmát kezeli, a maximális horizont Pod-méret üzembe helyezéséhez akár három privát felhők/SDDCs üzembe helyezését is megtervezheti.

Tehát ebben a példában az Azure-Virtual Network üzembe helyezett Horizon Infrastructure Virtual Machines (VM-EK) együttes használatával a számítási feladatok és az adatok igényei alapján elérheti a 12 000-es munkamenetek számát. Az egyes privát felhők és SDDC közötti kapcsolat az Azure-Virtual Network ExpressRoute gyors elérési útja, így a privát felhők közötti kelet-nyugati forgalomra nincs szükség.

Az alapszintű központi telepítési példához tartozó főbb feltételezések a következők:

* A Cloud Pod Architecture (CPA) használatával nem rendelkezik olyan helyszíni Horizon Pod-vel, amelyhez ehhez az új Pod-hez szeretne csatlakozni.

* A végfelhasználók az interneten keresztül csatlakoznak a virtuális asztalokhoz (a kapcsolat a helyszíni adatközponton keresztül).

Ebben az alapszintű példában a helyszíni Active Directory VPN-en vagy ExpressRoute áramkörön keresztül összekapcsolhatja az AD-tartományvezérlőt az Azure Virtual Networkban.

A tárgyalt alapszintű példa egy változata lehet a helyszíni erőforrások kapcsolatának támogatása. Lehet, hogy a felhasználók hozzáférnek az asztalokhoz, és a virtuális asztali alkalmazások forgalmát generálják, vagy a CPA használatával csatlakoznak a helyszíni Horizon Pod-hoz.

Az alábbi ábra azt mutatja be, hogyan teheti meg.A vállalati hálózat Azure Virtual Networkhoz való összekapcsolásához szüksége lesz egy ExpressRoute.Emellett a Global Reach használatával csatlakoztatnia kell a vállalati hálózatot a saját felhőalapú/SDDCs, ami lehetővé teszi, hogy a SDDC a ExpressRoute és a helyszíni erőforrásokhoz kapcsolódjon.

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Az Azure-beli VMware-megoldás és a Horizon-felhő közötti különbségek az Azure-ban" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Több Horizontos hüvely az Azure VMware megoldásban több régióban

Egy másik horizont Pod-Példaként nézzük meg egy példát, amely a skálázási horizontot mutatja több hüvely között.Ebben a példában két horizont-hüvelyt helyez üzembe két különböző régióban, és egyesítő azokat a CPA használatával.A hálózati konfiguráció hasonló az előző példához, és néhány további régiók közötti kapcsolat. 

Az Azure-Virtual Network az egyes régiókban csatlakoztatnia kell a másik régióban lévő privát felhőkhöz/SDDCs, így a CPA-összevonás részét képező, a felügyelet alatt álló összes asztalhoz való kapcsolódást lehetővé tevő horizont-kapcsolati kiszolgálókat is be kell kapcsolni.Ha további privát felhőket/SDDCs ad hozzá ehhez a konfigurációhoz, a teljes skálára méretezheti a 24 000-es munkameneteket. 

Habár ez a példa több régiót mutat be, ugyanez az elv vonatkozik arra az esetre, ha két horizont-hüvelyt szeretne üzembe helyezni ugyanabban a régióban. Vegye figyelembe, hogy a második Horizon Pod-t *külön Azure-Virtual Network*kell telepíteni.Végül, ahogy az előző egy Pod-példában is, a vállalati hálózatot és a helyszíni Pod-t összekapcsolhatja ehhez a több-Pod/régióhoz tartozó példához az ügyfél-ExpressRoute és a Global Reach használatával.

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Az Azure-beli VMware-megoldás és a Horizon-felhő közötti különbségek az Azure-ban" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Azure VMware-megoldási gazdagépek mérete horizonton üzemelő példányokhoz 

Az Azure VMware megoldásban futó gazdagép méretezési módszertana egyszerűbb, mint a helyi horizont, mivel az Azure VMware Solution Host példánya szabványosítva van. A gazdagépek pontos méretezése segít meghatározni, hogy hány gazdagépre van szükség a VDI-követelmények támogatásához, és a központi megoldás az asztali költségek meghatározása.

### <a name="azure-vmware-solution-host-instance"></a>Azure VMware Solution Host-példány

* PowerEdge R640-kiszolgáló – DSS korlátozás

* 36 mag \@ 2.3 GHz

* 576 – GB RAM

* HBA330 12 Gbps SAS HBA vezérlő (nem RAID)

* 1,92 TB SSD SATA-kombináció 6 GB/s 512 2.5-ös verzióban használható a gyors plug AG-meghajtón, 3 DWPD, 10512 TBW

* Intel 1,6 TB, NVMe, vegyes használat Express Flash, 2,5 SFF Drive, U. 2, P4600 a Carriertel

* 2 vSAN: 1,6 x 4 (1.92 TB)

### <a name="horizon-sizing-inputs"></a>Horizont méretezési bemenetei

Tekintse meg az alábbiakat a tervezett munkaterheléshez:

* Egyidejű asztali számítógépek száma

* Szükséges vCPU asztali gépenként

* Szükséges vRAM/asztali

* Szükséges tárterület asztali gépenként

A VDI-telepítések általában a CPU vagy a RAM korlátozottak, mivel ezek a tényezők határozzák meg a gazdagép méretét. Vegyük például a következő példát a LoginVSI Knowledge Worker típusú számítási feladathoz, amely a teljesítmény tesztelésével lett érvényesítve:

* 2 000 egyidejű asztali telepítés

* 2vCPU.

* 4 GB vRAM/asztali.

* 50 GB tárterület/asztali

Ebben a példában a gazdagépek teljes száma 18-ra, amely a virtuális gép által üzemeltetett 111-es sűrűséget eredményez.

>[!IMPORTANT]
>Az ügyfél-munkaterhelések ettől a példától különböznek a LoginVSI Knowledge Worker szolgáltatástól. Az üzembe helyezés megtervezésének részeként a VMware EUC SEs-vel dolgozhat a kívánt méretezési és teljesítményi igényekkel. Győződjön meg arról, hogy a tényleges, tervezett munkaterhelés használatával futtatja a saját teljesítménytesztet, mielőtt véglegesíti a gazdagép méretezését, és ennek megfelelően módosítja.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Az Azure VMware megoldás licencelésének horizontja 

Négy összetevőből áll az Azure VMware-megoldás futtatási horizontjának általános költségei. 

### <a name="azure-vmware-solution-capacity-cost"></a>Azure VMware-megoldás kapacitásának díja

A díjszabással kapcsolatos információkért tekintse meg az [Azure VMware megoldás díjszabását](https://azure.microsoft.com/pricing/details/azure-vmware/) ismertető oldalt.

### <a name="horizon-licensing-cost"></a>Horizont licencelési díja

Két elérhető licenc létezik az Azure VMware-megoldással, amely akár egyidejű felhasználó (CCU) vagy nevesített felhasználó (NU) lehet:

* Horizon-előfizetés licence

* Horizon univerzális előfizetési licenc

Ha a közeljövőben csak az Azure VMware-megoldáson helyezi üzembe a horizontot, akkor a Horizon-előfizetés licencét kell használnia, mert alacsonyabb a díja.

Ha az Azure VMware megoldásban és a helyszínen egyaránt üzembe helyezi a horizontot, a vész-helyreállítási használati esethez hasonlóan, válassza a Horizon Universal előfizetés licencét. Az univerzális licenc magasabb, mert vSphere-licencet tartalmaz a helyszíni üzembe helyezéshez.

Működjön együtt a VMware EUC értékesítési csapatával, és határozza meg a horizont licencelési költségeit az igényei alapján.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>A Horizon infrastruktúra virtuális gépei díja az Azure-Virtual Network

A standard telepítési architektúra alapján a Horizon infrastruktúra virtuális gépei a kapcsolódási kiszolgálók, a UAGs, az alkalmazások kötet-kezelői, és az ügyfél Azure-Virtual Network üzembe helyezhetők. További Azure-beli natív példányokra van szükség a magas rendelkezésre állás (HA), a Microsoft SQL vagy a Microsoft Active Directory (AD) szolgáltatások támogatásához az Azure-ban. Az alábbi lista az Azure-példányok listáját tartalmazza egy 2 000 – asztali telepítési példa alapján. 

| Horizont infrastruktúra-összetevő | Azure-példány | Szükséges példányok száma (2 000-asztali gépekhez)    | Megjegyzés  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Kapcsolatkezelő                | D4sv3          | 2       | *1 példányt tartalmaz a HA*             |    
| UAG                              | F2sv2          | 2       | *1 példányt tartalmaz a HA*             |
| Az alkalmazás köteteinek kezelője              | D4sv3          | 2       | *1 példányt tartalmaz a HA*             |
| Felhőalapú összekötő                  | D4sv3          | 1       |                                          |
| AD-vezérlő                    | D4sv3          | 2       | *Az MSFT AD szolgáltatás használatának lehetősége az Azure-ban* |
| MS-SQL Database                  | D4sv3          | 2       | *Az Azure-beli SQL-szolgáltatás használatának lehetősége*     |
| Windows-fájlmegosztás               | D4sv3          |         | *Nem kötelező*                               |

Az infrastruktúra virtuálisgép \$ -díja havonta 0,36, a fenti példában szereplő 2 000-Desktop üzembe helyezés esetén. Vegye figyelembe, hogy ez a példa az USA keleti Azure-példányának díjszabását használja a 2020-as verzióra. A díjszabás a régiótól, a kiválasztott beállításoktól és az Időzítéstől függően változhat.
