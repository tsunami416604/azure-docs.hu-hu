---
title: Traffic Manager üzembe helyezése az Azure VMware Solution (AVS) számítási feladatok kiegyensúlyozásához
description: Ismerje meg, hogyan integrálhatja a Traffic Managert az Azure VMware-megoldással (AVS) az alkalmazások számítási feladatainak különböző régiókban lévő több végponton való kiegyensúlyozásához.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: dc3107c6a237273b103fe52a91b3569d9e694e1a
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642985"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Traffic Manager üzembe helyezése az Azure VMware Solution (AVS) számítási feladatok kiegyensúlyozásához

Ez a cikk végigvezeti a Traffic Manager és az Azure VMware-megoldás (AVS) integrálásán az alkalmazások számítási feladatainak több végponton keresztüli kiegyensúlyozásához. Egy olyan forgatókönyvet vizsgálunk, amelyben a Traffic Manager a következő három alkalmazás-átjáró között irányítja át a forgalmat: az USA nyugati régiója, Nyugat-Európa és a helyszínen az USA keleti régiójában. 

Az Azure Traffic Manager egy DNS-alapú forgalom terheléselosztó, amely lehetővé teszi a forgalom optimális elosztását a globális Azure-régiókban lévő szolgáltatásokhoz. Az alkalmazások forgalmának elosztása az Azure-on futó munkaterhelések és a külső nyilvános végpontok között történik. További információ a Traffic Managerről: [Mi az Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

Először tekintse át az [előfeltételeket](#prerequisites) ; Ezután a következő eljárásokkal fogunk járni:

> [!div class="checklist"]
> * Az Application Gateway-átjárók konfigurációjának ellenőrzése
> * A NSX-T szegmens konfigurációjának ellenőrzése
> * A Traffic Manager profil létrehozása
> * Külső végpontok hozzáadása a Traffic Manager profilhoz

## <a name="topology"></a>Topológia

Ahogy az az alábbi ábrán is látható, az Azure Traffic Manager terheléselosztást biztosít az alkalmazások számára a regionális végpontok közötti DNS-szinten. Az Application Gateway-átjárók a háttérbeli készlet tagjai IIS-kiszolgálóként vannak konfigurálva, és a rendszer AVS külső végpontként hivatkozik rá.

A virtuális hálózaton keresztüli kapcsolat a két AVS Private Cloud region, az USA nyugati régiója és Nyugat-Európa, valamint az USA keleti régiójában található helyszíni kiszolgáló között egy ExpressRoute-átjárót használ.   

![Traffic Manager integráció az AVS-vel](media/traffic-manager/traffic-manager-topology.png)
 
## <a name="prerequisites"></a>Előfeltételek

- Három virtuális gép, amelyek különböző AVS-régiókban futó Microsoft IIS-kiszolgálóként vannak konfigurálva: az USA nyugati régiója, Nyugat-Európa és a helyszínen. 

- Application Gateway külső végpontokkal az USA nyugati régiójában, Nyugat-Európában és a helyszínen.

- Az internetes kapcsolattal rendelkező gazdagép ellenőrzése. 

## <a name="verify-configuration-of-your-application-gateways"></a>Az Application Gateway-átjárók konfigurációjának ellenőrzése

Az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) egy 7. rétegbeli webes forgalmi terheléselosztó, amely lehetővé teszi a webes alkalmazások forgalmának kezelését. További információ a Application Gatewayről: [Mi az az Azure Application Gateway?](../application-gateway/overview.md) 

Ebben az esetben három Application Gateway-példány van konfigurálva külső AVS-végpontként. Az Application Gateway-átjárók a háttérbeli készlet tagjaiként konfigurált AVS virtuális gépekkel rendelkeznek a bejövő 7. rétegbeli kérések terheléselosztásához. (Ha meg szeretné tudni, hogyan konfigurálhatja a Application Gatewayt AVS-alapú virtuális gépekkel a háttér-készletekként, tekintse meg az [azure Application Gateway használata a webalkalmazások Azure VMware-megoldásban való védeleméhez](protect-avs-web-apps-with-app-gateway.md)  

A következő lépésekkel ellenőrizheti az Application Gateway-átjárók helyes konfigurációját.

1. Az aktuális Application Gateway-átjárók listájának megtekintéséhez nyissa meg a Azure Portal, és válassza az **Application Gateways (alkalmazás-átjárók** ) lehetőséget. 

    Ebben az esetben a következő három Application Gateway-t konfiguráltuk:
    - AVS-GW-WUS
    - AVS-GW-EUS (helyszíni)
    - AVS-GW-NYEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Az Application Gateway-példányok listája." lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. Válassza ki a korábban telepített Application Gateway-átjárók egyikét. Megnyílik egy ablak, amely az Application Gateway különböző információit jeleníti meg. Válassza ki a **háttér-készletek** lehetőséget a háttérbeli készletek egyikének a konfigurációjának ellenőrzéséhez.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Application Gateway – részletek." lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. Ebben az esetben egy, a 172.29.1.10 IP-címét tartalmazó webkiszolgálóként konfigurált virtuálisgép-háttérbeli készletet látunk.
 
     :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Háttér-készlet szerkesztése.":::

    Hasonlóképpen ellenőrizheti a többi Application Gateway és a háttérbeli készlet tagjainak konfigurációját is. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>A NSX-T szegmens konfigurációjának ellenőrzése

A NSX-T kezelőjében létrehozott hálózati szegmensek hálózatokként használatosak a vCenter-ben lévő virtuális gépekhez. További információkért tekintse meg az oktatóanyagot, [hozzon létre egy NSX-T hálózati szegmenst az Azure VMware Solution (AVS) megoldásban](tutorial-nsx-t-network-segment.md).

Ebben az esetben egy NSX-T szegmens van konfigurálva az AVS-környezetben, ahol a háttérrendszer-készlet tagja virtuális gép csatlakoztatva van.

1. Válassza ki a **szegmenseket** a konfigurált szegmensek megtekintéséhez. Ebben az esetben azt láthatjuk, hogy a contoso-segment1 egy 1. rétegbeli rugalmas útválasztóhoz csatlakozik a contoso-T01 átjáróhoz.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Szegmens profilok a NSX-T kezelőjében.":::    

2. Válassza az **1. rétegbeli átjárók** lehetőséget az 1. rétegbeli átjárók listájának megtekintéséhez a csatolt szegmensek számával. Válassza ki a contoso-T01 társított szegmenst. Megnyílik egy ablak, amely a réteg-01 útválasztón konfigurált logikai felületet mutatja. Ez átjáróként szolgál a szegmenshez csatlakoztatott háttérbeli készlethez tartozó tag virtuális géphez.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Csatolt szegmensek.":::    

3. A virtuálisgép-vSphere ügyfélen válassza ki a virtuális gépet a részletek megtekintéséhez. Vegye figyelembe, hogy az IP-címe megegyezik az előző szakasz 3. lépésében szereplővel: 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Virtuális gép adatai.":::    

4. Válassza ki a virtuális gépet, majd kattintson a **műveletek > beállítások szerkesztése** lehetőségre a NSX-T szegmenshez való kapcsolódás ellenőrzéséhez.

## <a name="create-your-traffic-manager-profile"></a>A Traffic Manager profil létrehozása

1. Jelentkezzen be az [Azure portálra](https://rc.portal.azure.com/#home). Az **Azure-szolgáltatások > hálózatkezelés**területen válassza a **Traffic Manager profilok**lehetőséget.

2. Új Traffic Manager-profil létrehozásához válassza a **+ Hozzáadás** lehetőséget.
 
3. Adja meg a profil nevét, az útválasztási módszert (ezt a forgatókönyvet fogjuk használni, lásd: [Traffic Manager útválasztási módszerek](../traffic-manager/traffic-manager-routing-methods.md)), előfizetés és erőforráscsoport, majd válassza a **Létrehozás**lehetőséget.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Külső végpontok hozzáadása a Traffic Manager profilhoz

1. Válassza ki a Traffic Manager profilt a keresési eredmények ablaktáblán, válassza a **végpontok** , majd a **+ Hozzáadás**lehetőséget.

2. Adja meg a szükséges adatokat: típus, név, teljes tartománynév (FQDN) vagy IP-cím és súlyozás (ebben a forgatókönyvben az egyes végpontok 1. súlyozását rendeli hozzá). Válassza a **Hozzáadás** elemet.

    :::image type="content" source="media/traffic-manager/traffic-manager-profile.png" alt-text="Traffic Manager profil – végpont hozzáadása.":::  
 
    Ez létrehozza a külső végpontot. A figyelő állapotának **online**állapotúnak kell lennie. 

    Ugyanezen lépések megismétlésével hozzon létre két további külső végpontot, egyet egy másik régióban és a másikat a helyszínen. A létrehozás után mindhárom megjelenik a Traffic Manager profilban, és mindhárom állapotnak **online**állapotúnak kell lennie.

3. Válassza az **Áttekintés** lehetőséget. Másolja az URL-címet a **DNS-név**területen.

    :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Traffic Manager áttekintése a DNS-névvel."::: 

4. Illessze be a DNS-név URL-címét egy böngészőben. A következő képernyőfelvételen a Nyugat-európai régióra irányított forgalom látható.

    :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Nyugat-Európába irányított forgalom."::: 

5. Frissítse a böngészőjét. Az alábbi képernyőfelvételen az USA nyugati régiójában lévő háttérbeli készlet tagjainak egy másik készletére irányítja át a forgalmat.

    :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Az USA nyugati régiójában irányított forgalom."::: 

6. Frissítse újra a böngészőt. A következő képernyőfelvételen a háttérrendszer-készlet tagjainak a helyszínen történő üzembe helyezett forgalma látható.

    :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="A forgalom a helyszíni környezetbe irányítva.":::

## <a name="next-steps"></a>Következő lépések

További információk:

- [Az Azure Application Gateway használata Azure VMware-megoldással (AVS)](protect-avs-web-apps-with-app-gateway.md)
- [A Traffic Manager útválasztási módszerei](../traffic-manager/traffic-manager-routing-methods.md)
- [Terheléselosztási szolgáltatások kombinálása az Azure-ban](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Traffic Manager teljesítmény mérése](../traffic-manager/traffic-manager-performance-considerations.md)
