---
title: Oktatóanyag – VMware HCX üzembe helyezése és konfigurálása
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a VMware HCX-megoldást az Azure VMware-megoldás privát felhője számára.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 69832d1537f0f1be95d3283f543ef6e54187b58d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583347"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX üzembe helyezése és konfigurálása

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe és konfigurálhat VMware-HCX az Azure VMWare-megoldás privát felhője számára. A VMware HCX lehetővé teszi a VMware-alapú számítási feladatok áttelepítését az Azure VMware-megoldásba és más csatlakoztatott webhelyekre különböző áttelepítési típusok használatával.

A VMware HCX Advanced (az Azure VMware megoldásban előre üzembe helyezett) legfeljebb három helyszíni kapcsolatot támogat (a helyszínen a felhőben vagy a felhőből a felhőbe). Ha a VMware HCX Enterprise-hoz több, mint három hellyel kapcsolatos kapcsolat szükséges, a [támogatási kérelem](https://rc.portal.azure.com/#create/Microsoft.Support)elküldésével engedélyezheti a [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) bővítmény (amely jelenleg *előzetes*verzióban érhető el). A VMware HCX Enterprise Edition (EE) elérhető az Azure VMware-megoldással, amely *előzetes* funkcióként vagy szolgáltatásként érhető el. Habár az Azure VMware-megoldáshoz készült VMware HCX EE *előzetes*verzióban érhető el, ingyenes funkció vagy szolgáltatás, és az előzetes verziójú szolgáltatási feltételek és kikötések érvényesek. Miután a VMware HCX EE szolgáltatás elérhetővé válik, 30 napos értesítést kap arról, hogy a számlázás átvált. Lehetősége van a szolgáltatás kikapcsolására/letiltására is.

A Kezdés előtt alaposan [áttekintheti a](#before-you-begin) [szoftver verziójának követelményeit](#software-version-requirements)és [előfeltételeit](#prerequisites). 

Ezután végigvezeti az összes szükséges eljáráson:

> [!div class="checklist"]
> * Helyszíni VMware HCX-PETESEJT (összekötő) üzembe helyezése
> * VMware-HCX aktiválása
> * A helyszíni környezet és az Azure VMware megoldás környezetének párosítása.
> * Az összekötő konfigurálása (számítási profil, hálózati profil (ok) és Service mesh)
> * Fejezze be a telepítést a készülék állapotának ellenőrzésével.

Ha elkészült, a cikk végén a javasolt következő lépések közül választhat.  

## <a name="before-you-begin"></a>Előkészületek
   
* Tekintse át az alapszintű Azure VMware Solution Datacenter (SDDC) [oktatóanyag-sorozatot](tutorial-network-checklist.md).
* Tekintse át és hivatkozzon a [VMware HCX dokumentációra](https://docs.vmware.com/en/VMware-HCX/index.html), beleértve a HCX felhasználói útmutatóját.
* Tekintse át [a VMware docs Virtual Machines áttelepítését a VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Igény szerint áttekintheti a [VMware HCX üzembe helyezési szempontjait](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Ha szeretné áttekinteni a kapcsolódó VMware-anyagokat a HCX-on, mint például a VMware vSphere [blog sorozat](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) a HCX. 
* Az Azure VMware-megoldás HCX nagyvállalati aktiválást is igényelhet az Azure VMware megoldás-támogatási csatornákon keresztül.
* A WAN Interconnect-berendezés üzembe helyezéséhez az adott CIDR-tartomány már le van foglalva a `\22` saját felhő létrehozásához megadott ügyfélből.

A számítási és tárolási erőforrások méretezése alapvető tervezési lépés. A méretezési lépést a saját felhőalapú környezet kezdeti megtervezésének részeként kell kezelni. 

A számítási feladatokat a Azure Migrate-portálon az [Azure VMware-megoldás értékelésének](../migrate/how-to-create-azure-vmware-solution-assessment.md) elvégzésével is elvégezheti.

## <a name="software-version-requirements"></a>A szoftver verziójának követelményei

Az infrastruktúra-összetevőknek a szükséges minimális verziót kell futtatniuk. 
                                                         
| Összetevő típusa    | Forrás-környezeti követelmények    | A célként megadott környezeti követelmények   |
| --- | --- | --- |
| vCenter Server   | 5,1<br/><br/>Ha 5,5 U1-es vagy korábbi verzióját használ, használja az önálló HCX felhasználói felületet a HCX műveletekhez.  | 6,0 U2 és újabb verziók   |
| ESXi   | 5,0    | ESXi 6,0 és újabb verziók   |
| NSX    | A logikai kapcsolók HCX hálózati kiterjesztése a következő forrásnál: NSXv 6.2 + vagy NSX-T 2.4 +   | NSXv 6.2 + vagy NSX-T 2.4 +<br/><br/>A HCX közelségének útválasztásához: NSXv 6.4 + (a közelségi útválasztás nem támogatott a NSX-T esetében) |
| vCloud igazgató   | Nem kötelező – nincs együttműködés a vCloud igazgatóval a forrás helyén | A cél környezet vCloud-igazgatóval való integrálásakor a minimális érték a 9.1.0.2.  |

## <a name="prerequisites"></a>Előfeltételek

### <a name="network-and-ports"></a>Hálózat és portok

* [ExpressRoute Global REACH](tutorial-expressroute-global-reach-private-cloud.md) konfigurálva a helyszíni és az Azure VMware megoldás SDDC ExpressRoute-áramkörök között.

* A helyszíni összetevők és a helyszíni Azure VMware megoldás SDDC közötti kommunikációhoz minden szükséges portot meg kell nyitni (lásd a [VMware HCX dokumentációját](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* A helyszíni HCX IX-es és a NE készülékeknek képesnek kell lenniük a vCenter és az ESXi-infrastruktúra elérésére.

### <a name="ip-addresses"></a>IP-címek

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>A VMware HCX-petesejtek üzembe helyezése a helyszínen

>[!NOTE]
>Mielőtt telepítené a virtuális berendezést a helyszíni vCenter, le kell töltenie a VMware HCX-PETESEJTeket. 

1. Jelentkezzen be az Azure VMware megoldás HCX-kezelőjébe az `https://x.x.x.9` 443-es porton a **cloudadmin** felhasználói hitelesítő adataival, majd lépjen a **támogatás**elemre.

   >[!TIP]
   >A HCX-kezelő IP-címének azonosításához az Azure VMware-megoldás panelen lépjen a kapcsolat **kezelése**elemre,  >  **Connectivity** majd válassza a **HCX** lapot. 
   >
   >A vCenter jelszava a saját felhő beállításakor lett meghatározva.

1. Kattintson a **Letöltés** hivatkozásra a VMware HCX-petesejt fájl letöltéséhez.

1. Nyissa meg a helyszíni vCenter, és válasszon ki egy OVF-sablont, amely a letöltött PETESEJT-fájl a helyszíni vCenter való üzembe helyezéshez.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Válassza ki a nevet és a helyet, egy erőforrást/fürtöt, ahol a HCX telepíti, majd tekintse át a részleteket és a szükséges erőforrásokat.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Tekintse át a licencfeltételeket, és ha elfogadja, válassza ki a szükséges tárterületet és hálózatot, majd kattintson a **tovább**gombra.

1. A **sablon testreszabása**mezőben adja meg az összes szükséges információt. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Kattintson a **tovább**gombra, ellenőrizze a konfigurációt, majd válassza a **Befejezés** lehetőséget a HCX-petesejtek telepítéséhez.
     
   >[!NOTE]
   >Az üzembe helyezett VMware HCX Manager általában a fürt felügyeleti hálózatára lesz telepítve.  
   
   > [!IMPORTANT]
   > A telepítés befejeződése után manuálisan kell bekapcsolni a virtuális berendezést.
   > Várjon 10-15 percet, miután bekapcsolta a HCX berendezést a következő lépésre való áttéréshez.

Ennek a lépésnek a teljes körű áttekintéséhez tekintse meg az [Azure VMware Solution-VMware HCX készülék üzembe helyezési](https://www.youtube.com/embed/BwSnQeefnso) videóját. 


## <a name="activate-vmware-hcx"></a>VMware-HCX aktiválása

A VMware HCX-petesejtek helyszíni üzembe helyezése után készen áll a VMware HCX aktiválására. A VMware HCX aktiválása előtt le kell kérnie a licencet.

1. Az Azure VMware megoldásban lépjen a kapcsolat **kezelése**  >  **Connectivity**lapra, válassza a **HCX** lapot, majd kattintson a **Hozzáadás**gombra.

1. Jelentkezzen be a helyszíni VMware HCX-kezelőbe `https://HCXManagerIP:9443` , és jelentkezzen be a **rendszergazda** felhasználó hitelesítő adataival. 

   > [!IMPORTANT]
   > Ügyeljen arra, hogy tartalmazza a `9443` portszámot a VMware HCX Manager IP-címével.

1. A **Licencelés**területen adja meg a **HCX speciális kulcsát**.  
   
    > [!NOTE]
    > A VMware HCX-kezelőnek nyitva kell lennie az internet-hozzáféréssel vagy a proxy konfigurálásával.

1. Az **Adatközpont helye mezőben**adja meg a legközelebbi helyet, ahová a VMware HCX Managert telepíti a helyszínen.

1. Módosítsa a **rendszer nevét** , vagy fogadja el az alapértelmezett értéket.
   
1. A befejezést követően vagy a folytatáshoz válassza az **Igen lehetőséget, folytassa** a folytatást.
    
1. A **vCenter összekapcsolásához**adja meg a vCenter-kiszolgáló teljes tartománynevét vagy IP-címét, valamint a megfelelő hitelesítő adatokat, majd válassza a **Folytatás**lehetőséget.
   
1. Az **SSO/PSC konfigurálása**lapon adja meg a PSC teljes tartománynevét vagy IP-címét, majd válassza a **Folytatás**lehetőséget.
   
   >[!NOTE]
   >Általában ugyanaz, mint az vCenter teljes tartományneve/IP-címe.

1. Ellenőrizze, hogy az összes bemenet helyes-e, majd válassza az **Újraindítás**lehetőséget.
    
   > [!NOTE]
   > Az újraindítás után késleltetést tapasztal, mielőtt a rendszer megkéri a következő lépéssel.
   >
   >A szolgáltatások újraindítása után kritikus fontosságú, hogy a megjelenő képernyőn látható vCenter zöld színnel jelenik meg. Mind a vCenter, mind az SSO rendelkezik a megfelelő konfigurációs paraméterekkel, amelyeknek meg kell egyezniük az előző képernyővel.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Ennek a lépésnek a teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldás – VMware HCX aktiválási](https://www.youtube.com/embed/BkAV_TNYxdE) videóját.


## <a name="configure-vmware-hcx"></a>A VMware HCX konfigurálása

Most már készen áll a hely párosításának hozzáadására, a hálózat és a számítási profil létrehozására, valamint a szolgáltatások, például az áttelepítés, a hálózati bővítmény vagy a vész-helyreállítás engedélyezésére. 

### <a name="add-a-site-pairing"></a>Hely párosításának hozzáadása

A VMware HCX Managert az adatközpont VMware HCX kezelőjével is összekapcsolhatjuk az Azure VMware megoldásban. 

1. Jelentkezzen be a helyszíni vCenter, és a Home ( **Kezdőlap**) területen válassza a **HCX**lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Az **infrastruktúra**területen válassza a **hely párosítása**elemet, majd válassza a **Kapcsolódás távoli helyhez** lehetőséget (a képernyő közepén). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Adja meg a **távoli HCX URL-címét vagy IP-címét**, az Azure VMware-megoldás cloudadmin@vsphere.local felhasználónevét és **jelszavát**, majd válassza a **kapcsolat**lehetőséget.

   > [!NOTE]
   > A **távoli HCX URL-** cím az Azure VMware Solution Private Cloud HCX Manager, amely általában a felügyeleti hálózat ". 9" címe.  Ha például a vCenter 192.168.4.2, akkor a HCX URL-címe 192.168.4.9 lesz.
   >
   > A **jelszó** ugyanazt a jelszót fogja használni, amelyet a vCenter való bejelentkezéshez használt. Ezt a jelszót a kezdeti üzembe helyezési képernyőn határozta meg.

   Megjelenik egy képernyő, amely a HCX-kezelőt mutatja be az Azure VMware megoldásban és a helyszíni HCX-kezelőben (párosítva).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont.":::

Ennek a lépésnek a teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldás – VMware HCX-hely párosítási](https://www.youtube.com/embed/sKizDCRHOko) videóját.



### <a name="create-network-profiles"></a>Hálózati profilok létrehozása

A VMware HCX üzembe helyez néhány virtuális készüléket (automatizált), de több IP-szegmensre van szüksége.  A hálózati profilok létrehozásakor meg kell határoznia azokat az IP-szegmenseket, amelyeket a [VMware HCX hálózati szegmensek előtti üzembe helyezés előkészítése és a tervezési fázis](production-ready-deployment-steps.md#vmware-hcx-network-segments)során azonosított.

Négy hálózati profilt fog létrehozni:

   - Kezelés
   - vMotion
   - Replikáció
   - Uplink

1. Az **infrastruktúra**területen válassza az **összekötő**  >  **többhelyes szolgáltatás háló**  >  **hálózati profilok**  >  **hálózati profil létrehozása**lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Minden hálózati profilnál válassza ki a hálózatot, a portszámot, adja meg a nevet, hozza létre az adott szegmenshez tartozó IP-készletet, majd válassza a **Létrehozás**lehetőséget. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont.":::

Ennek a lépésnek a teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldás – VMware HCX Create Network Profile](https://www.youtube.com/embed/NhyEcLco4JY) videót.


### <a name="create-compute-profile"></a>Számítási profil létrehozása

1. Válassza a **számítási profilok**  >  **számítási profil létrehozása**lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Adja meg a profil nevét, és válassza a **Folytatás**lehetőséget.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Válassza ki az engedélyezni kívánt szolgáltatásokat, például az áttelepítést, a hálózati bővítményt vagy a vész-helyreállítást, majd válassza a **Folytatás**lehetőséget.
  
   > [!NOTE]
   > Általában semmi sem változik itt.

1. A **szolgáltatás erőforrásainak kiválasztása**területen válasszon ki egy vagy több olyan szolgáltatási erőforrást (fürtöt), amely lehetővé teszi a kiválasztott VMware HCX-szolgáltatások engedélyezését.  

1. Ha a helyszíni adatközpontban látja a fürtöket, válassza a **Folytatás**lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Az **adattár kiválasztása**lapon válassza ki az adattár tárolási erőforrását a VMware HCX Interconnect berendezések telepítéséhez, majd válassza a **Folytatás**lehetőséget.

   Ha több erőforrás van kiválasztva, a VMware HCX az elsőként kiválasztott erőforrást használja a kapacitás kimerülése előtt.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Válassza ki a **felügyeleti hálózati profil**lehetőséget, az előző lépésekben létrehozott felügyeleti hálózati profilt, majd kattintson a **Folytatás**gombra.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > A felügyeleti hálózati profil lehetővé teszi, hogy a VMware HCX készülék (ek) kommunikáljon a vCenter, és az ESXi-gazdagépek is elérhetők legyenek.

1. Válassza ki a **kimenő hálózati profil**lehetőséget, az előző lépésekben létrehozott kimenő hálózati profilt, majd kattintson a **Folytatás**gombra.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Válassza a **VMotion hálózati profil**lehetőséget, az előző lépések során létrehozott vMotion hálózati profilt, majd kattintson a **Folytatás**gombra.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Válassza a **vSphere replikációs hálózati profil**, az előző lépések során létrehozott replikációs hálózati profil lehetőséget, majd kattintson a **Folytatás**gombra.

   A legtöbb esetben a replikációs hálózati profil megegyezik a felügyeleti hálózati profillal.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Válassza ki a **hálózati bővítmények elosztott kapcsolóit**, az elosztott virtuális kapcsolókat, amelyek tartalmazzák az Azure VMware-megoldásba áttelepíteni kívánt virtuális gépeket egy 2. rétegbeli kiterjesztett hálózaton, majd válassza a **Folytatás**lehetőséget.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Tekintse át a kapcsolatok szabályait, és válassza a **Folytatás**lehetőséget.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. A számítási profil létrehozásához válassza a **Befejezés** lehetőséget.

   Az alább láthatóhoz hasonló képernyő jelenik meg.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Ennek a lépésnek a végpontok közötti áttekintéséhez tekintse meg az [Azure VMware-megoldást – VMware HCX Create számítási Profile](https://www.youtube.com/embed/qASXi5xrFzM) videó.




### <a name="create-service-mesh"></a>Szolgáltatás hálójának létrehozása

Itt az ideje, hogy konfigurálja a Service Mesh szolgáltatást a helyszíni és az Azure VMware megoldás SDDC között.

1. Az **infrastruktúra**területen válassza az **összekötő**  >  **szolgáltatás háló**  >  **Létrehozás szolgáltatás háló létrehozása**lehetőséget.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Tekintse át az előre feltöltött helyeket, majd válassza a **Folytatás**lehetőséget. 

   >[!NOTE]
   >Ha ez az első Service Mesh-konfiguráció, nem kell módosítania ezt a képernyőt.  

1. Válassza ki a forrás és a távoli számítási profilok legördülő listát, majd válassza a **Folytatás**lehetőséget.  

   A beállítások határozzák meg azokat az erőforrásokat, amelyeken a virtuális gépek a VMware HCX-szolgáltatásokat használhatják.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Tekintse át azokat a szolgáltatásokat, amelyek engedélyezve lesznek, majd válassza a **Folytatás**lehetőséget.  

1. A **speciális konfigurációban – a kimenő hálózati profilok felülbírálása** válassza a **Folytatás**lehetőséget.  A kimenő hálózati profilok olyan hálózathoz csatlakoznak, amelyen keresztül a távoli hely összekötő készülékei elérhetők.  
  
1. A **Speciális konfiguráció-hálózati bővítmények Felskálázása**lapon tekintse át, majd válassza a **Folytatás**lehetőséget. 

1. A **Speciális konfiguráció – Traffic Engineering**szolgáltatásban tekintse át és végezze el a szükséges módosításokat, majd kattintson a **Continue (folytatás**) gombra.

1. Tekintse át a topológia előzetes verzióját, és válassza a **Folytatás**lehetőséget.

1. Adja meg a szolgáltatás rácsvonalának felhasználóbarát nevét, és válassza a **Befejezés gombot a befejezéshez** .  

1. A központi telepítés figyeléséhez válassza a **feladatok megtekintése** lehetőséget. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont.":::

   Ha a szolgáltatás hálójának üzembe helyezése sikeresen befejeződött, a szolgáltatások zöldként jelennek meg.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Ellenőrizze a szolgáltatás hálójának állapotát a készülék állapotának ellenőrzésével, majd válassza az **összekötő**  >  **berendezések**elemet.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Ennek a lépésnek a teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldás-VMware HCX Create Service Mesh](https://www.youtube.com/embed/FyZ0d3P_T24) videót.



### <a name="optional-create-a-network-extension"></a>Választható Hálózati bővítmény létrehozása

Ha a helyszíni környezetből az Azure VMware megoldásba kívánja terjeszteni a hálózatokat, az alábbi lépéseket követve bővítheti ezeket a hálózatokat.

1. A **szolgáltatások**területen válassza a **hálózati kiterjesztés**lehetőséget, majd válassza **a hálózati bővítmény létrehozása**lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Válassza ki azokat a hálózatokat, amelyeket ki szeretne terjeszteni az Azure VMware-megoldásra, majd válassza a **tovább**lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont.":::

1. Adja meg a helyszíni átjáró IP-címét minden kibővíteni kívánt hálózat esetében, majd válassza a **Küldés**lehetőséget. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont.":::

   A hálózati bővítmény elvégzése néhány percet vesz igénybe. Ha igen, akkor a bővítmény állapotának változása **befejeződött**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Ennek a lépésnek a teljes körű áttekintéséhez tekintse meg az [Azure VMware Solution-VMware HCX hálózati bővítmény](https://www.youtube.com/embed/cNlp0f_tTr0) videóját.


## <a name="next-steps"></a>További lépések

Ha elérte ezt a pontot, és a készülék összekötő-alagútjának állapota felfelé és zöldre van **állítva** , a VMware HCX használatával áttelepítheti és védetté teheti az Azure VMware megoldás virtuális gépeket.  Az Azure VMware-megoldás támogatja a munkaterhelések áttelepítését (vagy egy hálózati bővítménnyel).  Továbbra is végezheti a munkaterhelések áttelepítését a vSphere-környezetben a hálózatok helyszíni létrehozása és a virtuális gépek üzembe helyezése között.  További információ: [VMware HCX dokumentáció](https://docs.vmware.com/en/VMware-HCX/index.html) és [Virtual Machines ÁTtelepítése VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) a VMware technikai dokumentációjában a HCX használatával kapcsolatos részletekért.
