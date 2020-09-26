---
title: A VMware HCX telepítése
description: A VMware HCX-megoldás beállítása az Azure VMware-megoldás saját felhőhöz
ms.topic: how-to
ms.date: 09/24/2020
ms.openlocfilehash: a101712f2d80e0d8e70d37bd5b7b08931f62ba3d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356553"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Az Azure VMware-megoldás HCX telepítése

Ebből a cikkből megtudhatja, hogyan állíthatja be a VMWare HCX-megoldást az Azure VMWare-megoldás privát felhője számára. A HCX lehetővé teszi a VMware-alapú számítási feladatok felhőbe és más csatlakoztatott webhelyekre történő áttelepítését a különböző beépített HCX támogatott áttelepítési típusokon keresztül.

A HCX Advanced, az alapértelmezett telepítés, amely legfeljebb három hely kapcsolatát támogatja (a helyszínen vagy a felhőben). Ha több mint három kapcsolatra van szükség, az ügyfeleknek lehetősége van a HCX Enterprise bővítmény támogatására, amely jelenleg előzetes verzióban érhető el. A HCX Enterprise további díjakat biztosít az ügyfelek számára az általánosan elérhető verzió (GA) után, de [további funkciókat](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)is kínál.


Először alaposan [áttekintheti](#before-you-begin)a [szoftver verziójának követelményeit](#software-version-requirements)és [előfeltételeit](#prerequisites) . 

Ezután az összes szükséges eljáráson át fogunk járni:

> [!div class="checklist"]
> * Helyszíni HCX-petesejtek (Connector) üzembe helyezése
> * HCX aktiválása és konfigurálása
> * A hálózati kimenő és a szolgáltatási háló konfigurálása
> * A telepítés befejezése a készülék állapotának ellenőrzésével

A telepítés befejezése után kövesse a cikk végén elérhető, javasolt következő lépéseket.  

## <a name="before-you-begin"></a>Előkészületek
    
* Tekintse át az alapszintű Azure VMware Solution Datacenter (SDDC) [oktatóanyag-sorozatot](tutorial-network-checklist.md).
* Tekintse át és hivatkozzon a [VMware HCX dokumentációra](https://docs.vmware.com/en/VMware-HCX/index.html) , beleértve a HCX felhasználói útmutatóját.
* Tekintse át [a VMware docs Virtual Machines áttelepítését a VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Igény szerint áttekintheti a [VMware HCX üzembe helyezési szempontjait](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Ha szeretné áttekinteni a kapcsolódó VMware-anyagokat a HCX-on, mint például a VMware vSphere [blog sorozat](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) a HCX. 
* Azure VMware-megoldás HCX nagyvállalati aktiválást igényelhet az Azure VMware megoldás-támogatási csatornákon keresztül.

A számítási és tárolási erőforrások méretezése az Azure VMware Solution Private Cloud HCX megoldás használatának előkészítéséhez elengedhetetlen tervezési lépés. A méretezési lépést a saját felhőalapú környezet kezdeti megtervezésének részeként kell kezelni. 

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

* A ExpressRoute Global Reach a helyszíni és az Azure VMware megoldás SDDC ExpressRoute-áramkörök között kell konfigurálni.

* Az összes szükséges portot meg kell nyitni a helyszíni és az Azure VMware megoldás SDDC között (lásd a [VMware HCX dokumentációját](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* A helyszíni HCX-kezelő egy IP-címe, és legalább két IP-cím az InterConnect (IX) és a Network Extension (NE) készülékhez.

* A helyszíni HCX IX-es és a NE készülékeknek képesnek kell lenniük a vCenter és az ESXi-infrastruktúra elérésére.

* A WAN Interconnect-berendezés üzembe helyezéséhez a Azure Portal a SDDC való üzembe helyezéshez használt/22 CIDR-blokkon kívül a HCX egy/29 blokkot igényel. Ügyeljen arra, hogy ezt a követelményt a hálózat megtervezésében is figyelembe kell venni.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>A VMware HCX-petesejtek üzembe helyezése a helyszínen

1. Jelentkezzen be az Azure VMware Solution HCX Managerbe a `https://x.x.x.9` 443-es porton a **cloudadmin** felhasználói hitelesítő adataival, majd lépjen a **támogatás**elemre.

1. Válassza ki a VMware HCX PETESEJTJEI fájl letöltési hivatkozását. 

1. Jelentkezzen be az Azure VMware megoldás SDDC vCenter, és válassza a **HCX**lehetőséget.
   
1. Nyissa meg a helyszíni vCenter, és válassza ki a helyszíni vCenter üzembe helyezni kívánt OVF-sablont.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="Ezután nyissa meg a helyszíni vCenter, és válasszon ki egy OVF-sablont a helyszíni vCenter való üzembe helyezéshez.":::

1. Válassza ki a nevet és a helyet, majd válasszon ki egy erőforrást vagy fürtöt, ahol a HCX telepíteni kell. Ezután tekintse át a részleteket és a szükséges erőforrásokat.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text=" Válassza ki a nevet és a helyet, majd válasszon ki egy erőforrást vagy fürtöt, ahol a HCX telepíteni kell. Ezután tekintse át a részleteket és a szükséges erőforrásokat.":::

1. Tekintse át a licencfeltételeket, és ha elfogadja, válassza a szükséges tárterület és hálózat lehetőséget. Ezután kattintson a **Tovább** gombra.

1. A **sablon testreszabása**mezőben adja meg az összes szükséges információt. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="A sablon testreszabása mezőben adja meg az összes szükséges információt.":::

1. Kattintson a **tovább**gombra, ellenőrizze a konfigurációt, és válassza a **Befejezés** lehetőséget a HCX-petesejtek telepítéséhez.

## <a name="activate-hcx"></a>HCX aktiválása

A telepítést követően hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be a helyszíni HCX-kezelőbe `https://HCXManagerIP:9443` , és jelentkezzen be a **rendszergazdai** Felhasználónév hitelesítő adataival. 

   > [!IMPORTANT]
   > Ügyeljen arra, hogy a `9443` portszámot a HCX Manager IP-címével együtt adja meg.

1. A **Licencelés**területen adja meg a **HCX speciális kulcsát**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="A licencelés területen adja meg a HCX speciális kulcsát.":::
    
    > [!NOTE]
    > A HCX-kezelőnek meg kell nyitnia az internet-hozzáférést, vagy konfigurálnia kell egy proxyt.

1. Ha szükséges, a **vCenter**szerkessze a vCenter adatait.

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="Ha szükséges, a vCenter szerkessze a vCenter adatait.":::

1. Ha szükséges, az **adatközpont**helyen szerkessze az adatközpont helyét.

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="Ha szükséges, az adatközpont helyen szerkessze az adatközpont helyét.":::

## <a name="configure-hcx"></a>HCX konfigurálása 

1. Jelentkezzen be a helyszíni vCenter, és a **Home (Kezdőlap**) területen válassza a **HCX**lehetőséget.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="Jelentkezzen be a helyszíni vCenter, és a Home (Kezdőlap) területen válassza a HCX lehetőséget.":::

1. Az **infrastruktúra**területen válassza a **hely**párosítása lehetőséget  >  **a hely párosításának hozzáadásához**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="Az infrastruktúra területen válassza ki a hely párosítása > a hely párosításának hozzáadása lehetőséget.":::

1. Adja meg a távoli HCX URL-címét vagy IP-címét, az Azure VMware-megoldás cloudadmin felhasználónevét és jelszavát, majd válassza a **kapcsolat**lehetőséget.

   A rendszeren a csatlakoztatott hely látható.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="A rendszeren a csatlakoztatott hely látható.":::

1. Az **infrastruktúra**területen válassza az **összekötő**  >  **többhelyes szolgáltatás háló**  >  **hálózati profilok**  >  **hálózati profil létrehozása**lehetőséget.

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="Az infrastruktúra területen válassza az InterConnect > többhelyes szolgáltatás háló > hálózati profilok > a hálózati profil létrehozása lehetőséget.":::

1. Az új hálózati profilnál adja meg a IX-es és a NE IP-címtartományok HCX (a IX-es és a NE berendezések esetében legalább két IP-cím szükséges).

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="Az új hálózati profilnál adja meg a IX-es és a NE IP-címtartományok HCX (a IX-es és a NE berendezések esetében legalább két IP-cím szükséges).":::
  
   > [!NOTE]
   > A hálózati bővítmény berendezésének (HCX-NE) egy-az-egyhez kapcsolata van egy elosztott virtuális kapcsolóval (DVS).  

1. Most válassza a **számítási profil**  >  **Létrehozás számítási profil létrehozása**lehetőséget.

1. Adja meg a számítási profil nevét, és válassza a **Folytatás**lehetőséget.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="Adja meg a számítási profil nevét, és válassza a Folytatás lehetőséget.":::

1. Válassza ki az engedélyezni kívánt szolgáltatásokat, például az áttelepítést, a hálózati bővítményt vagy a vész-helyreállítást, majd válassza a **Folytatás**lehetőséget.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="Válassza ki az engedélyezni kívánt szolgáltatásokat, például az áttelepítést, a hálózati bővítményt vagy a vész-helyreállítást, majd válassza a Folytatás lehetőséget.":::

1. A **szolgáltatási erőforrások kiválasztása**területen válasszon ki egy vagy több olyan szolgáltatási erőforrást, amelyhez engedélyezni szeretné a kiválasztott HCX-szolgáltatásokat. Válassza a **Folytatás**lehetőséget.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="A szolgáltatási erőforrások kiválasztása területen válasszon ki egy vagy több olyan szolgáltatási erőforrást, amelyhez engedélyezni szeretné a kiválasztott HCX-szolgáltatásokat. Válassza a Folytatás lehetőséget.":::
  
   > [!NOTE]
   > Válassza ki azokat a fürtöket, amelyekben a forrásként szolgáló virtuális gépeket a HCX használatával kívánja áttelepíteni.

1. Válassza az **adattár** lehetőséget, majd válassza a **Folytatás**lehetőséget. 
      
   Az HCX Interconnect-berendezések üzembe helyezéséhez válassza ki az egyes számítási és tárolási erőforrásokat. Ha több erőforrás van kiválasztva, a HCX az elsőként kiválasztott erőforrást használja a kapacitás kimerítése előtt.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="Az HCX Interconnect-berendezések üzembe helyezéséhez válassza ki az egyes számítási és tárolási erőforrásokat. Ha több erőforrás van kiválasztva, a HCX az elsőként kiválasztott erőforrást használja a kapacitás kimerítése előtt.":::

1. Válassza ki a **hálózati profilokban** létrehozott felügyeleti hálózati profilt, majd válassza a **Folytatás**lehetőséget.  
      
   Válassza ki azt a hálózati profilt, amelyen keresztül a vCenter és az ESXi-gazdagépek felügyeleti felülete elérhető. Ha még nem adott meg ilyen hálózati profilt, itt is létrehozhatja.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="Válassza ki azt a hálózati profilt, amelyen keresztül a vCenter és az ESXi-gazdagépek felügyeleti felülete elérhető. Ha még nem adott meg ilyen hálózati profilt, itt is létrehozhatja.":::

1. Válassza a **hálózati kimenő hálózat** lehetőséget, majd válassza a **Folytatás**lehetőséget.
      
   Válasszon ki egy vagy több hálózati profilt, hogy a következők egyike igaz legyen:  
   * A távoli helyen található Interconnect-berendezések ezen a hálózaton keresztül érhetők el  
   * A távoli oldali berendezések ezen a hálózaton keresztül érhetik el a helyi összekötő berendezéseket.  
    
   Ha olyan pont-pont típusú hálózatokkal rendelkezik, mint például a közvetlen kapcsolódás, amely nem azonos több hely között, akkor kihagyhatja ezt a lépést, mivel a számítási profilok több hellyel is megoszthatók. Ilyen esetekben a kimenő hálózati profilok felülbírálható és megadhatók az InterConnect Service Mesh létrehozásakor.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="Válassza a hálózati kimenő hálózat lehetőséget, majd válassza a Folytatás lehetőséget.":::

1. Válassza a **VMotion hálózati profil** lehetőséget, majd válassza a **Folytatás**lehetőséget.
      
   Válassza ki azt a hálózati profilt, amelyen keresztül elérhetővé vált az ESXi-gazdagépek vMotion felülete. Ha még nem adott meg ilyen hálózati profilt, itt is létrehozhatja. Ha nem rendelkezik vMotion-hálózattal, válassza a **felügyeleti hálózati profil**lehetőséget.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="Válassza a vMotion hálózati profil lehetőséget, majd válassza a Folytatás lehetőséget.":::

1. A **vSphere-replikációs hálózati profil kiválasztása**lapon válassza ki a hálózati profilt az ESXi-gazdagépek vSphere replikációs felületén, majd válassza a **Folytatás**lehetőséget.
      
   A legtöbb esetben ez a profil megegyezik a felügyeleti hálózati profillal.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="A vSphere-replikációs hálózati profil kiválasztása lapon válassza ki a hálózati profilt az ESXi-gazdagépek vSphere replikációs felületén, majd válassza a Folytatás lehetőséget.":::

1. A **hálózati bővítmények számára válassza az elosztott kapcsolók lehetőséget**, válassza ki azt a DVS-t, amelyen a hálózatokat integrálni és csatlakoztatni kívánó virtuális gépek vannak.  Válassza a **Folytatás**lehetőséget.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="A hálózati bővítmények számára válassza az elosztott kapcsolók lehetőséget, válassza ki azt a DVS-t, amelyen a hálózatokat integrálni és csatlakoztatni kívánó virtuális gépek vannak.  Válassza a Folytatás lehetőséget.":::

1. Tekintse át a kapcsolatok szabályait, és válassza a **Folytatás**lehetőséget.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="Tekintse át a kapcsolatok szabályait, és válassza a Folytatás lehetőséget.":::

1.  A számítási profil létrehozásához válassza a **Befejezés** lehetőséget.

## <a name="configure-network-uplink"></a>Hálózati kimenő hálózat konfigurálása

Most konfigurálja a hálózati profil változását az Azure VMware-megoldás SDDC a hálózati kimenő hálózatra.

1. Jelentkezzen be a SDDC NSX-T-re egy új logikai kapcsoló létrehozásához, vagy használjon olyan meglévő logikai kapcsolót, amely a helyszíni és az Azure VMware megoldási SDDC közötti hálózati kimenő kapcsolatokhoz használható.

1. Hozzon létre egy hálózati profilt a HCX kimenő forgalomhoz az Azure VMware-megoldás SDDC, amely a helyszínen az Azure VMware megoldás SDDC kommunikációjában használható.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="Hozzon létre egy hálózati profilt a HCX kimenő forgalomhoz az Azure VMware-megoldás SDDC, amely a helyszínen az Azure VMware megoldás SDDC kommunikációjában használható.":::

1. Adja meg a hálózati profil nevét, valamint legalább 4-5 szabad IP-címet a szükséges L2 hálózati bővítmény alapján.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="Adja meg a hálózati profil nevét, valamint legalább 4-5 szabad IP-címet a szükséges L2 hálózati bővítmény alapján.":::

1. Válassza a **Létrehozás** lehetőséget az Azure VMware-megoldás SDDC konfigurációjának befejezéséhez

## <a name="configure-service-mesh"></a>A Service Mesh konfigurálása

Most konfigurálja a Service Mesh szolgáltatást a helyszíni és az Azure VMware megoldás SDDC között.

1. Jelentkezzen be az Azure VMware megoldás SDDC vCenter, és válassza a **HCX**lehetőséget.

2. Az **infrastruktúra**területen válassza az **összekötő**  >  **szolgáltatás hálójának**  >  **létrehozása szolgáltatás rácsvonal** lehetőséget az előző lépésekben létrehozott hálózati és számítási profilok konfigurálásához.    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="Az infrastruktúra területen válassza az InterConnect > Service Mesh > a Service Mesh létrehozása lehetőséget az előző lépésekben létrehozott hálózati és számítási profilok konfigurálásához.":::

3. Válassza a párosított helyek lehetőséget a hibrid képesség engedélyezéséhez, majd válassza a **Folytatás**lehetőséget.   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="Válassza a párosított helyek lehetőséget a hibrid képesség engedélyezéséhez, majd válassza a Folytatás lehetőséget.":::

4. Válassza ki a forrás-és távoli számítási profilokat a hibrid szolgáltatások engedélyezéséhez, majd válassza a **Folytatás**lehetőséget.
      
   A beállítások határozzák meg azokat az erőforrásokat, amelyekben a virtuális gépek használhatják a HCX szolgáltatásokat.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="A beállítások határozzák meg azokat az erőforrásokat, amelyekben a virtuális gépek használhatják a HCX szolgáltatásokat.":::

5. Válassza ki az engedélyezni kívánt szolgáltatásokat, majd válassza a **Folytatás**lehetőséget.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="Válassza ki az engedélyezni kívánt szolgáltatásokat, majd válassza a Folytatás lehetőséget.":::

6. A **speciális konfigurációban – a kimenő hálózati profilok felülbírálása** válassza a **Folytatás**lehetőséget.  
      
   A kimenő hálózati profilok használatával csatlakozhat ahhoz a hálózathoz, amelyen keresztül a távoli hely összekötő készülékei elérhetők.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="A kimenő hálózati profilok használatával csatlakozhat ahhoz a hálózathoz, amelyen keresztül a távoli hely összekötő készülékei elérhetők.":::

7. Válassza **a hálózati bővítmény berendezésének konfigurálása**lehetőséget. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="Válassza a hálózati bővítmény berendezésének konfigurálása lehetőséget.":::

8. Adja meg a DVS-kapcsolók számának megfelelő készülékek darabszámát.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="Adja meg a DVS-kapcsolók számának megfelelő készülékek darabszámát.":::

9. Válassza a **Folytatás** lehetőséget a kihagyáshoz.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="Válassza a Folytatás lehetőséget a kihagyáshoz.":::

10. Tekintse át a topológia előzetes verzióját, és válassza a **Folytatás**lehetőséget. 

11. Adja meg a szolgáltatás rácsvonalának felhasználóbarát nevét, és válassza a **Befejezés gombot a befejezéshez** .  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="A Service Mesh befejezése":::

   A szolgáltatás rácsvonala telepítve és konfigurálva van.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="Üzembe helyezett szolgáltatás hálója":::

## <a name="check-appliance-status"></a>Berendezés állapotának keresése
A készülék állapotának vizsgálatához válassza az **összekötő**  >  **berendezések**elemet. 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="A készülék állapotának ellenõrzése.":::

## <a name="next-steps"></a>Következő lépések

Ha a készülék összekötő **alagútjának állapota** felfelé és zöldre van **állítva** , készen áll arra, hogy HCX használatával áttelepítse és megvédje az Azure VMware megoldás virtuális gépeket. Tekintse meg a VMware [HCX dokumentációját](https://docs.vmware.com/en/VMware-HCX/index.html) és a VMware [HCX átVirtual Machines telepítését](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) a VMware technikai dokumentációjában.
