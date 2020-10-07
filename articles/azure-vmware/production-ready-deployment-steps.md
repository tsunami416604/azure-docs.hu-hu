---
title: Az Azure VMware-megoldás üzembe helyezésének megtervezése
description: Ez a cikk egy Azure VMware-megoldás üzembe helyezési munkafolyamatát ismerteti.  A végeredmény egy olyan környezet, amely készen áll a virtuális gép (VM) létrehozására és áttelepítésére.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: e279f14406d464171f0879d85cc33f9844d22ec3
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802208"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Az Azure VMware-megoldás üzembe helyezésének megtervezése

Ebben a cikkben az üzembe helyezés során használt adatok azonosításának és gyűjtésének tervezési folyamatát biztosítjuk. Az üzembe helyezés [előtti ellenőrzőlista használatával](pre-deployment-checklist.md) dokumentálhatja az információkat, és az egyszerű hivatkozásokat a telepítés során.  

Ennek a rövid útmutatónak a folyamatai a virtuális gépek (VM-EK) és a Migrálás létrehozására szolgáló, éles használatra kész környezetet eredményeznek. 

>[!IMPORTANT]
>Az Azure VMware-megoldási erőforrás létrehozása előtt egy támogatási jegyet kell benyújtania a csomópontok lefoglalásához. Miután a támogatási csapat megkapja a kérést, akár öt munkanapot is igénybe vesz, hogy erősítse meg a kérését, és lefoglalja a csomópontjait. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több csomópontot szeretne lefoglalni, akkor ugyanezt a folyamatot kell megtennie. További információ: [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md). 

## <a name="subscription"></a>Előfizetés

Azonosítsa az Azure VMware-megoldás üzembe helyezéséhez használni kívánt előfizetést.  Létrehozhat egy új előfizetést, vagy felhasználhat egy meglévőt.

>[!NOTE]
>Az előfizetést egy Microsoft Nagyvállalati Szerződéshoz kell társítani.

## <a name="resource-group"></a>Erőforráscsoport

Azonosítsa az Azure VMware-megoldáshoz használni kívánt erőforráscsoportot.  Általában egy erőforráscsoport jön létre kifejezetten az Azure VMware megoldáshoz, de használhat egy meglévő erőforráscsoportot is.

## <a name="region"></a>Régió

Azonosítsa az Azure VMware-megoldás üzembe helyezéséhez használni kívánt régiót.  További információkért tekintse meg az [Azure-termékek régiónként elérhető útmutatót](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Erőforrás neve

Adja meg az üzembe helyezés során használni kívánt erőforrás nevét.  Az erőforrás neve egy felhasználóbarát és leíró név, amelyben az Azure VMware-megoldás saját felhőjét adja meg.

## <a name="size-nodes"></a>Csomópontok mérete

Azonosítsa az Azure VMware-megoldás telepítésekor használni kívánt méretű csomópontokat.  A teljes listát az [Azure VMware megoldás privát felhők és fürtök](concepts-private-clouds-clusters.md#hosts) dokumentációjában találja.

## <a name="number-of-hosts"></a>Gazdagépek száma

Adja meg az Azure VMware-megoldás saját felhőbe telepíteni kívánt gazdagépek számát.  A csomópontok minimális száma három, a maximális érték pedig a fürt 16.  További információkért tekintse meg az [Azure VMware Solution Private Cloud és a Clusters](concepts-private-clouds-clusters.md#clusters) dokumentációját.

Később is kiterjesztheti a fürtöt, ha a kezdeti üzembe helyezési számon túl kell lépnie.

## <a name="vcenter-admin-password"></a>vCenter rendszergazdai jelszava
Adja meg a vCenter rendszergazdai jelszavát.  Az üzembe helyezés során létre kell hoznia egy vCenter-rendszergazdai jelszót. A jelszó a cloudadmin@vsphere.local vCenter létrehozásakor a rendszergazdai fiókra vonatkozik. Ezzel bejelentkezhet a vCenter-be.

## <a name="nsx-t-admin-password"></a>NSX-T rendszergazdai jelszó
Adja meg a NSX-T rendszergazdai jelszót.  Az üzembe helyezés során létre fog hozni egy NSX-T rendszergazdai jelszót. A jelszó a NSX-fiókban a rendszergazda felhasználóhoz van hozzárendelve a NSX Build során. Ezzel bejelentkezik a NSX-T kezelőjébe.

## <a name="ip-address-segment"></a>IP-cím szegmense

Az üzembe helyezés megtervezésének első lépése az IP-szegmentálás megtervezése.  Az Azure VMware-megoldás az Ön által megadott/22 hálózat betöltését teszi lehetővé. Ezután kisebb szegmensekre Faragja, majd ezeket az IP-szegmenseket használja a vCenter, a VMware HCX, a NSX-T és a vMotion.

Az Azure VMware-megoldás egy belső ExpressRoute áramkör használatával csatlakozik a Microsoft Azure Virtual Networkhoz. A legtöbb esetben a ExpressRoute Global Reach használatával csatlakozik az adatközponthoz. 

Azure VMware-megoldás, a meglévő Azure-környezet és a helyszíni környezet minden Exchange-útvonal (jellemzően). Ebben az esetben az ebben a lépésben definiált/22 CIDR hálózati címterület nem fedi át a helyszíni vagy az Azure-ban már meglévőket.

**Példa:** 10.0.0.0/22

További információ: [Network Planning ellenőrzőlista](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Azonosítsa az IP-címek szegmensét" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>A virtuális gépek számítási feladataihoz tartozó IP-címek szegmense

Azonosítson egy IP-szegmenst az első hálózat (NSX szegmens) létrehozásához a privát felhőben.  Más szóval egy hálózati szegmenst szeretne létrehozni az Azure VMware-megoldásban, hogy virtuális gépeket helyezzen üzembe az Azure VMware megoldásban.   

Akkor is hozzon létre egy olyan hálózati szegmenst, amely hasznos lehet a környezet érvényesítéséhez, még akkor is, ha az L2 hálózatok kibővítését tervezi.

Ne feledje, hogy a létrehozott IP-szegmenseknek egyedinek kell lenniük az Azure-ban és a helyszíni adatlábnyomban.  

**Példa:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Azonosítsa az IP-címek szegmensét" border="false":::     

## <a name="optional-extend-networks"></a>Választható Hálózatok kiterjesztése

Kiterjesztheti a hálózati szegmenseket a helyszíni rendszerből az Azure VMware megoldásba, és ha így tesz, ezeket a hálózatokat is azonosíthatja.  

Ne feledje, hogy:

- Ha a helyi hálózatok kibővítését tervezi, ezeknek a hálózatoknak csatlakozniuk kell egy [vSphere elosztott kapcsolóhoz (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) a helyszíni VMware-környezetben.  
- Ha a hálózat (ok) [vSphere standard kapcsolón](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html)szeretné kiterjeszteni az élő hálózatot, akkor nem bővíthetők.

## <a name="expressroute-global-reach-peering-network"></a>ExpressRoute Global Reach társ hálózat

Azonosítson egy `/29` hálózati CIDR, amely szükséges a ExpressRoute Global REACH a társításhoz. Ne feledje, hogy a létrehozott IP-szegmenseknek egyedinek kell lenniük az Azure VMware-megoldásban és a helyszíni lábnyomban. Az ebben a szegmensben lévő IP-címeket a rendszer a ExpressRoute minden egyes végén használja Global Reach kapcsolattal az Azure VMware Solution ExpressRoute áramkört a helyszíni ExpressRoute-áramkörhöz csatlakoztatni. 

**Példa:** 10.1.0.0/29

:::image type="content" source="media/pre-deployment/expressroute-global-reach-ip-diagram.png" alt-text="Azonosítsa az IP-címek szegmensét" border="false":::

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Azure-Virtual Network az Azure VMware-megoldás csatlakoztatásához

Az Azure VMware-megoldás saját felhőhöz való hozzáféréséhez az Azure VMware-megoldáshoz tartozó ExpressRoute-áramkörnek egy Azure Virtual Networkhoz kell csatlakoznia.  Az üzembe helyezés során megadhat egy új virtuális hálózatot, vagy kiválaszthat egy meglévőt.

Az Azure VMware-megoldásból származó ExpressRoute áramkör csatlakozik egy ExpressRoute-átjáróhoz az ebben a lépésben meghatározott Azure-Virtual Network.  

>[!IMPORTANT]
>Ha meglévő virtuális hálózatot választ, ki kell választania egyet, amely nem rendelkezik már meglévő átjáró-alhálózattal.  

Ha a ExpressRoute áramkört az Azure VMware megoldásból egy meglévő ExpressRoute-átjáróhoz szeretné kapcsolni, az üzembe helyezés után megteheti.  

Tehát Összefoglalva, szeretné összekötni az Azure VMware-megoldást egy meglévő Express Route Gateway-átjáróval?  

* **Igen** = azonosítsa az üzembe helyezés során nem használt virtuális hálózatot.
* **Nem** = meglévő virtuális hálózat azonosítása vagy új létrehozása az üzembe helyezés során.

Mindkét esetben dokumentálja, hogy mit szeretne tenni ebben a lépésben.

>[!NOTE]
>Ezt a virtuális hálózatot a helyszíni környezet és az Azure VMware-megoldás látja, ezért ügyeljen arra, hogy a virtuális hálózatban használt IP-szegmensek és alhálózatok ne fedjék át egymást.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Azonosítsa az IP-címek szegmensét" border="false":::

## <a name="vmware-hcx-network-segments"></a>VMware HCX hálózati szegmensek

A VMware HCX egy olyan technológia, amely az Azure VMware megoldással együtt van kiépítve. A VMware HCX elsődleges használati esetei a munkaterhelések áttelepítése és a vész-helyreállítás. Ha azt tervezi, hogy ezt a lehetőséget választja, érdemes megtervezni a hálózatkezelést most.   Ellenkező esetben kihagyhatja és folytathatja a következő lépéssel.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>További lépések
Most, hogy összegyűjtötte és dokumentálta a szükséges információkat, folytassa a következő szakasszal az Azure VMware-megoldás saját Felhőbeli létrehozásához.

> [!div class="nextstepaction"]
> [Az Azure VMware Solution üzembe helyezése](deploy-azure-vmware-solution.md)
