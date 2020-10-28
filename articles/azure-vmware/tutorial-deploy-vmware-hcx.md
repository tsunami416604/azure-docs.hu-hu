---
title: Oktatóanyag – VMware HCX üzembe helyezése és konfigurálása
description: Ismerje meg, hogyan helyezhet üzembe és konfigurálhat VMware HCX-megoldást az Azure VMware-megoldás privát felhője számára.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 424abeef567d88f7de37f7a7a4ab7a7a8b6ef3bc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791409"
---
# <a name="deploy-and-configure-vmware-hcx"></a>A VMware HCX üzembe helyezése és konfigurálása

Ebből a cikkből megtudhatja, hogyan helyezheti üzembe és konfigurálhatja a helyszíni VMware HCX-összekötőt az Azure VMware-megoldás privát felhője számára. A VMware HCX használatával áttelepítheti VMware-alapú számítási feladatait az Azure VMware-megoldásba és más csatlakoztatott webhelyekre különböző áttelepítési típusok használatával. Mivel az Azure VMware-megoldás üzembe helyezi és konfigurálja a HCX Cloud Managert, le kell töltenie, aktiválnia és konfigurálnia kell a HCX-összekötőt a helyszíni VMware Datacenter-ben.

A VMware HCX Advanced Connector előre telepítve van az Azure VMware megoldásban. Legfeljebb három helyszíni kapcsolatot támogat (a helyszínen a felhőben vagy a felhőben). Ha három helynél több kapcsolatra van szüksége, küldjön egy [támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support) a [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) bővítmény engedélyezéséhez. A bővítmény jelenleg előzetes verzióban érhető el. 

>[!NOTE]
>A VMware HCX Enterprise Edition (EE) előzetes verziójú szolgáltatásként érhető el az Azure VMware megoldásban. Ingyenes, és az előzetes verziójú szolgáltatás használati feltételeinek hatálya alá tartozik. A VMware HCX EE szolgáltatás általánosan elérhetővé válik, és 30 napos értesítést kap, hogy a számlázás átvált. Lehetősége van a szolgáltatás kikapcsolására vagy letiltására is. A borsó megjegyzése szerint jelenleg nincs egyszerű visszalépési útvonal a HCX Enterprise-ből a fejlett HCX, és a visszalépést kiválasztó ügyfeleknek újra kell telepíteniük a felmerülő állásidőt.

Első lépésként alaposan tekintse [át a jelen](#before-you-begin)cikk a [szoftver verziószámára vonatkozó követelményeket](#software-version-requirements)és az [Előfeltételek](#prerequisites) című szakaszt. 

Ezután végigvezeti az összes szükséges eljáráson:

> [!div class="checklist"]
> * Telepítse a helyszíni VMware HCX-PETESEJTeket (HCX-összekötőt).
> * Aktiválja a VMware HCX-összekötőt.
> * Párosítsa a helyszíni HCX-összekötőt az Azure VMware-megoldás HCX Cloud Managerrel.
> * Konfigurálja az összekötőt (hálózati profil, számítási profil és szolgáltatás háló).
> * A telepítés befejezéséhez ellenőrizze a berendezés állapotát, és ellenőrizze, hogy az áttelepítés lehetséges-e.

Ha elkészült, kövesse a cikk végén a javasolt következő lépéseket.  

## <a name="before-you-begin"></a>Előkészületek
   
* Tekintse át az Azure VMware Solution Software-Defined Datacenter (SDDC) [oktatóanyag-sorozatot](tutorial-network-checklist.md).
* Tekintse át és hivatkozzon a [VMware HCX dokumentációra](https://docs.vmware.com/en/VMware-HCX/index.html), beleértve a HCX felhasználói útmutatóját.
* Tekintse át [Virtual Machines áttelepítését VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) VMware docs-on.
* Igény szerint áttekintheti a [VMware HCX üzembe helyezési szempontjait](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Ha szeretné áttekinteni a kapcsolódó VMware-anyagokat a HCX, például a VMware vSphere [blog-sorozatot](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html). 
* Az Azure VMware-megoldás HCX nagyvállalati aktiválást is igényelhet az Azure VMware megoldás-támogatási csatornákon keresztül.
* [Szükség esetén áttekintheti a HCX szükséges hálózati portokat](https://ports.vmware.com/home/VMware-HCX).
* Bár az Azure VMware-megoldás HCX a Cloud Manager előre konfigurálva van az Azure VMware-megoldás privát felhőhöz megadott/22 hálózatból, a helyszíni HCX-összekötőnek szüksége van hálózati tartományok lefoglalására a helyszíni hálózatról. Ezeket a hálózatokat és tartományokat a cikk későbbi részében ismertetjük.

A számítási és tárolási erőforrások méretezése alapvető tervezési lépés. A méretezési lépést a saját felhőalapú környezet kezdeti megtervezésének részeként kell kezelni. 

A számítási feladatokat a Azure Migrate-portálon az [Azure VMware-megoldás értékelésének](../migrate/how-to-create-azure-vmware-solution-assessment.md) elvégzésével lehet méretezni.

## <a name="software-version-requirements"></a>A szoftver verziójának követelményei

Az infrastruktúra-összetevőknek a szükséges minimális verziót kell futtatniuk. 
                                                         
| Összetevő típusa    | Forrás-környezeti követelmények    | A célként megadott környezeti követelmények   |
| --- | --- | --- |
| vCenter Server   | 5,1<br/><br/>Ha az 5,5 U1-es vagy korábbi verzióját használja, használja az önálló HCX felhasználói felületet a HCX műveletekhez.  | 6,0 U2 és újabb verziók   |
| ESXi   | 5,0    | ESXi 6,0 és újabb verziók   |
| NSX    | A logikai kapcsolók HCX hálózati kiterjesztése a következő forrásnál: NSXv 6.2 + vagy NSX-T 2.4 +.   | NSXv 6.2 + vagy NSX-T 2.4 +<br/><br/>A HCX közelségének útválasztásához: NSXv 6.4 +. (A közelségi útválasztás nem támogatott a NSX-T-vel.) |
| vCloud igazgató   | Nem kötelező. Nincs együttműködés a vCloud igazgatóval a forrás webhelyén. | Ha a vCloud Director környezettel integrálja a célszámítógépet, a minimum a 9.1.0.2.  |

## <a name="prerequisites"></a>Előfeltételek

### <a name="network-and-ports"></a>Hálózat és portok

* Konfigurálja az [Azure ExpressRoute Global REACH](tutorial-expressroute-global-reach-private-cloud.md) a helyszíni és az Azure VMware megoldás SDDC ExpressRoute-áramkörök között.

* [Minden szükséges portnak](https://ports.vmware.com/home/VMware-HCX) nyitva kell lennie a helyszíni összetevők és az Azure VMware megoldás SDDC közötti kommunikációhoz.

További információt a [VMware HCX dokumentációjában](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)talál.


### <a name="ip-addresses"></a>IP-címek

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>A VMware HCX-összekötő PETESEJTJEInek üzembe helyezése a helyszínen

> [!NOTE]
> Mielőtt telepítené a virtuális berendezést a helyszíni vCenter, le kell töltenie a VMware HCX-összekötő PETESEJTJEIt. 

1. Nyisson meg egy böngészőablakot, és jelentkezzen be az Azure VMware Solution HCX Managerbe a `https://x.x.x.9` 443-es porton a **cloudadmin** felhasználói hitelesítő adataival, majd lépjen a **support (támogatás** ) elemre.

   > [!TIP]
   > Jegyezze fel a HCX Cloud Manager IP-címét az Azure VMware megoldásban. Az IP-cím azonosításához az Azure VMware-megoldás panelen lépjen a kapcsolat **kezelése** ,  >  **Connectivity** majd a **HCX** lapra. 
   >
   >A vCenter jelszava a saját felhő beállításakor lett meghatározva.

1. A **Letöltés** hivatkozásra kattintva letöltheti a VMware HCX-összekötő petesejtjeinek fájlját.

1. Lépjen a helyszíni vCenter. Válasszon ki egy OVF-sablont, amely a letöltött PETESEJT-fájl, hogy a HCX-összekötőt a helyszíni vCenter telepítse.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Válassza ki a nevet és a helyet, majd válasszon ki egy erőforrást vagy fürtöt, ahol üzembe helyezi az HCX-összekötőt. Ezután tekintse át a részleteket és a szükséges erőforrásokat.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Tekintse át a licencfeltételeket. Ha elfogadja, válassza ki a szükséges tárterületet és hálózatot, majd válassza a **tovább** lehetőséget.

1. A **sablon testreszabása** mezőben adja meg az összes szükséges információt. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Kattintson a **tovább** gombra, ellenőrizze a konfigurációt, majd válassza a **Befejezés** lehetőséget az HCX-összekötő petesejtjeinek telepítéséhez.
     
   > [!NOTE]
   > A jelenleg üzembe helyezett VMware HCX-összekötőt általában a fürt felügyeleti hálózatára telepíti a rendszer.  
   
   > [!IMPORTANT]
   > Előfordulhat, hogy manuálisan kell bekapcsolnia a virtuális berendezést.  Ha ez a helyzet, várjon 10-15 percet, mielőtt továbblép a következő lépésre.

Az eljárás teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldást: HCX Appliance üzembe helyezési](https://www.youtube.com/embed/BwSnQeefnso) videó. 


## <a name="activate-vmware-hcx"></a>VMware-HCX aktiválása

Miután telepítette a VMware HCX Connector-összekötőt a helyszínen, és elindítja a készüléket, készen áll az aktiválásra. Először is be kell szereznie egy licenckulcs az Azure VMware-megoldás portálján.

1. Az Azure VMware-megoldás portálján lépjen a kapcsolat **kezelése**  >  **Connectivity** elemre, válassza a **HCX** lapot, majd kattintson a **Hozzáadás** gombra.

1. A **rendszergazdai** hitelesítő adataival jelentkezzen be a helyszíni VMware HCX Managerbe `https://HCXManagerIP:9443` . 

   > [!IMPORTANT]
   > Ügyeljen arra, hogy tartalmazza a `9443` portszámot a VMware HCX Manager IP-címével.

1. A **Licencelés** mezőben adja meg a **HCX speciális kulcsának** kulcsát.  
   
    > [!NOTE]
    > A VMware HCX-kezelőnek meg kell nyitnia az internet-hozzáférést, vagy konfigurálnia kell egy proxyt.

1. Az **Adatközpont helye mezőben** adja meg a legközelebbi helyet a VMware HCX Manager helyszíni telepítéséhez.

1. A **rendszer neve** területen módosítsa a nevet, vagy fogadja el az alapértelmezett értéket.
   
1. Válassza **az igen, a folytatás** lehetőséget.
    
1. A **vCenter összekapcsolásához** adja meg a vCenter-kiszolgáló teljes tartománynevét vagy IP-címét, valamint a megfelelő hitelesítő adatokat, majd válassza a **Folytatás** lehetőséget.
   
1. Az **SSO/PSC konfigurálása** lapon adja meg a platform Services-vezérlő teljes tartománynevét vagy IP-címét, majd válassza a **Folytatás** lehetőséget.
   
   >[!NOTE]
   >Ez a bejegyzés általában megegyezik a vCenter teljes tartománynevével vagy IP-címével.

1. Ellenőrizze, hogy az összes bemenet helyes-e, majd válassza az **Újraindítás** lehetőséget.
    
   > [!NOTE]
   > Az újraindítás után késleltetést tapasztal, mielőtt a rendszer megkéri a következő lépéssel.

A szolgáltatások újraindítása után kritikus fontosságú, hogy a megjelenő képernyőn látható vCenter zöld színnel jelenik meg. Mind a vCenter, mind az SSO-nak rendelkeznie kell a megfelelő konfigurációs paraméterekkel, amelyeknek meg kell egyezniük az előző képernyővel.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Az eljárás végpontok közötti áttekintéséhez tekintse meg az [Azure VMware-megoldást: aktiválja a HCX](https://www.youtube.com/embed/BkAV_TNYxdE) videót.


## <a name="configure-the-vmware-hcx-connector"></a>A VMware HCX-összekötő konfigurálása

Most már készen áll egy hely párosításának hozzáadására, egy hálózati és számítási profil létrehozására, valamint olyan szolgáltatások engedélyezésére, mint például az áttelepítés, a hálózati bővítmény vagy a vész-helyreállítás. 

### <a name="add-a-site-pairing"></a>Hely párosításának hozzáadása

A VMware HCX Cloud Managert az adatközpont VMware HCX-összekötője segítségével csatlakoztathatja az Azure VMware megoldásban. 

1. Jelentkezzen be a helyszíni vCenter, és a Home ( **Kezdőlap** ) területen válassza a **HCX** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Az **infrastruktúra** területen válassza a **hely párosítása** elemet, majd válassza a **Kapcsolódás távoli helyhez** lehetőséget (a képernyő közepén). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Adja meg a korábban feljegyzett távoli HCX URL-címet vagy IP-címet, az Azure VMware-megoldás cloudadmin@vsphere.local felhasználónevét és jelszavát. Ezután válassza a **Csatlakozás** lehetőséget.

   > [!NOTE]
   > Ha sikeresen létre szeretne hozni egy helyet, a HCX-összekötőnek képesnek kell lennie arra, hogy a 443-as porton keresztül irányítsa a HCX Cloud Manager IP-címét
   >
   > A jelszó ugyanaz a jelszó, amelyet a vCenter való bejelentkezéshez használt. Ezt a jelszót a kezdeti üzembe helyezési képernyőn határozta meg.

   Megjelenik egy képernyő, amelyen látható, hogy a HCX Cloud Manager az Azure VMware megoldásban és a helyszíni HCX-összekötő csatlakoztatva van (párosítva).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Képernyőkép a OVF-sablon tallózásáról.":::

Az eljárás végpontok közötti áttekintéséhez tekintse meg az [Azure VMware-megoldást: HCX-hely párosítási](https://www.youtube.com/embed/sKizDCRHOko) videó.



### <a name="create-network-profiles"></a>Hálózati profilok létrehozása

A VMware HCX több IP-szegmenst igénylő virtuális berendezések (automatizált) egy részhalmazát telepíti. A hálózati profilok létrehozásakor meg kell határoznia azokat az IP-szegmenseket, amelyeket azonosított a [VMware HCX hálózati szegmensek előtti üzembe helyezés előkészítése és tervezési szakasza](production-ready-deployment-steps.md#vmware-hcx-network-segments)során.

Négy hálózati profilt fog létrehozni:

   - Kezelés
   - vMotion
   - Replikáció
   - Uplink

1. Az **infrastruktúra** területen válassza az **összekötő**  >  **többhelyes szolgáltatás háló**  >  **hálózati profilok**  >  **hálózati profil létrehozása** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Minden hálózati profilnál válassza ki a hálózat és a port csoportot, adjon meg egy nevet, és hozza létre az adott szegmenshez tartozó IP-készletet. Ezután válassza a **Létrehozás** elemet. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Képernyőkép a OVF-sablon tallózásáról.":::

Az eljárás végpontok közötti áttekintéséhez tekintse meg az [Azure VMware-megoldást: HCX Network Profile](https://www.youtube.com/embed/NhyEcLco4JY) videó.


### <a name="create-a-compute-profile"></a>Számítási profil létrehozása

1. Válassza a **számítási profilok**  >  **számítási profil létrehozása** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Adja meg a profil nevét, és válassza a **Folytatás** lehetőséget.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Válassza ki az engedélyezni kívánt szolgáltatásokat, például az áttelepítést, a hálózati bővítményt vagy a vész-helyreállítást, majd válassza a **Folytatás** lehetőséget.
  
   > [!NOTE]
   > Általában semmi sem változik.

1. A **szolgáltatás erőforrásainak kiválasztása** területen válasszon ki egy vagy több szolgáltatási erőforrást (fürtöket) a kiválasztott VMware HCX-szolgáltatások engedélyezéséhez.  

1. Ha a helyszíni adatközpontban látja a fürtöket, válassza a **Folytatás** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Az **adattár kiválasztása** lapon válassza ki az adattár tárolási erőforrását a VMware HCX Interconnect berendezések telepítéséhez. Ezután válassza a **Folytatás** elemet.

   Ha több erőforrás van kiválasztva, a VMware HCX az elsőként kiválasztott erőforrást használja a kapacitás kimerülése előtt.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. A **felügyeleti hálózati profil kiválasztása** lapon válassza ki az előző lépésekben létrehozott felügyeleti hálózati profilt. Ezután válassza a **Folytatás** elemet.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > A felügyeleti hálózati profil lehetővé teszi, hogy a VMware HCX készülékek kommunikálhassanak a vCenter. Az ESXi-gazdagépek elérhetők a profilon keresztül.

1. A **kimenő hálózati profil kiválasztása** lapon válassza ki az előző lépésekben létrehozott kimenő hálózati profilt. Ezután válassza a **Folytatás** elemet.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. A **VMotion hálózati profil kiválasztása** lapon válassza ki az előző lépések során létrehozott vMotion hálózati profilt. Ezután válassza a **Folytatás** elemet.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. A **vSphere-replikációs hálózati profil kiválasztása** lapon válassza ki az előző lépések során létrehozott replikációs hálózati profilt. Ezután válassza a **Folytatás** elemet.

   A legtöbb esetben a replikációs hálózati profil megegyezik a felügyeleti hálózati profillal.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. A **hálózati bővítmények számára válassza az elosztott kapcsolók lehetőséget** , válassza ki azokat az elosztott virtuális kapcsolókat, amelyek az Azure VMware-megoldásba áttelepíteni kívánt virtuális gépeket tartalmazzák egy 2. rétegbeli kiterjesztett hálózaton. Ezután válassza a **Folytatás** elemet.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Tekintse át a kapcsolatok szabályait, és válassza a **Folytatás** lehetőséget.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. A számítási profil létrehozásához válassza a **Befejezés** lehetőséget.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Az eljárás teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldást: számítási profil](https://www.youtube.com/embed/qASXi5xrFzM) videó.

### <a name="create-a-service-mesh"></a>Szolgáltatás hálójának létrehozása

Itt az ideje, hogy konfiguráljon egy Service meshot a helyszíni és az Azure VMware megoldás SDDC között.

   > [!NOTE]
   > A Service Mesh Azure VMware-megoldással való sikeres létrehozásához:
   >
   > Az 500/4500-as UDP-portok nyitva vannak a helyszíni HCX-összekötő által meghatározott "kimenő" hálózati profil címei és az Azure VMware-megoldás HCX Cloud "kimenő" hálózati profiljának címei között.
   >
   > Ügyeljen arra, hogy ellenőrizze a [HCX szükséges portokat](https://ports.vmware.com/home/VMware-HCX).

1. Az **infrastruktúra** területen válassza az **összekötő**  >  **szolgáltatás háló**  >  **Létrehozás szolgáltatás háló létrehozása** lehetőséget.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Tekintse át az előre feltöltött helyeket, majd válassza a **Folytatás** lehetőséget. 

   >[!NOTE]
   >Ha ez az első Service Mesh-konfiguráció, nem kell módosítania ezt a képernyőt.  

1. Válassza ki a forrás-és távoli számítási profilokat a legördülő listából, majd válassza a **Folytatás** lehetőséget.  

   A beállítások határozzák meg azokat az erőforrásokat, amelyeken a virtuális gépek a VMware HCX-szolgáltatásokat használhatják.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Tekintse át azokat a szolgáltatásokat, amelyek engedélyezve lesznek, majd válassza a **Folytatás** lehetőséget.  

1. A **speciális konfigurációban – a kimenő hálózati profilok felülbírálása** lehetőségnél válassza a **Folytatás** lehetőséget.  

   A kimenő hálózati profilok csatlakoznak ahhoz a hálózathoz, amelyen keresztül a távoli hely összekötő készülékei elérhetők.  
  
1. A **Speciális konfiguráció-hálózati bővítmények Felskálázása** lapon tekintse át, majd válassza a **Folytatás** lehetőséget. 

1. A **Speciális konfiguráció-Traffic Engineering** szolgáltatásban tekintse át és végezze el a szükséges módosításokat, majd kattintson a **Continue (folytatás** ) gombra.

1. Tekintse át a topológia előzetes verzióját, és válassza a **Folytatás** lehetőséget.

1. Adja meg a szolgáltatás rácsvonalának felhasználóbarát nevét, és válassza a **Befejezés gombot a befejezéshez** .  

1. A központi telepítés figyeléséhez válassza a **feladatok megtekintése** lehetőséget. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Képernyőkép a OVF-sablon tallózásáról.":::

   Ha a szolgáltatás hálójának üzembe helyezése sikeresen befejeződött, a szolgáltatások zöldként jelennek meg.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Ellenőrizze a szolgáltatás rácsvonalának állapotát a készülék állapotának ellenőrzésével. 
1. Válassza az **összekötő**  >  **berendezések** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Az eljárás teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldást: Service Mesh](https://www.youtube.com/embed/FyZ0d3P_T24) videó.



### <a name="optional-create-a-network-extension"></a>Választható Hálózati bővítmény létrehozása

Ha a helyszíni környezetből származó hálózatokat az Azure VMware megoldásba kívánja terjeszteni, kövesse az alábbi lépéseket:

1. A **szolgáltatások** területen válassza a **hálózati kiterjesztés** lehetőséget, majd válassza **a hálózati bővítmény létrehozása** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Válassza ki az Azure VMware-megoldásra kiterjeszteni kívánt hálózatokat, majd kattintson a **tovább** gombra.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Képernyőkép a OVF-sablon tallózásáról.":::

1. Adja meg a helyszíni átjáró IP-címét minden kibővíteni kívánt hálózat esetében, majd válassza a **Küldés** lehetőséget. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Képernyőkép a OVF-sablon tallózásáról.":::

   A hálózati bővítmény befejeződése néhány percet vesz igénybe. Ha igen, akkor a bővítmény állapotának változása **befejeződött** .

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Ennek a lépésnek a teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldás: hálózati bővítmény](https://www.youtube.com/embed/cNlp0f_tTr0) videóját.


## <a name="next-steps"></a>Következő lépések

Ha elérte ezt a pontot, és a készülék összekötő-alagútjának állapota felfelé és zöldre van **állítva** , a VMware HCX használatával áttelepítheti és védetté teheti az Azure VMware megoldás virtuális gépeket. Az Azure VMware-megoldás támogatja a munkaterhelések áttelepítését (hálózati bővítménnyel vagy anélkül). A számítási feladatokat továbbra is áttelepítheti a vSphere-környezetbe, valamint a hálózatok helyszíni létrehozását és a virtuális gépek központi telepítését a hálózatokra.  

A HCX használatával kapcsolatos további információkért látogasson el a VMware technikai dokumentációra:

* [A VMware HCX dokumentációja](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Virtual Machines migrálása VMware-HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* [HCX szükséges portok](https://ports.vmware.com/home/VMware-HCX)
