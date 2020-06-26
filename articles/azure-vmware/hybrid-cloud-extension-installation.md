---
title: Hibrid felhőalapú bővítmény (HCX) telepítése
description: A VMware Hybrid Cloud Extension (HCX) megoldás beállítása az Azure VMware-megoldás (AVS) privát felhőhöz
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 3037d12ebbb036098cfc00a42521513bc2df6170
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85367546"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Az Azure VMware-megoldás HCX telepítése

Ebből a cikkből megtudhatja, hogyan állíthatja be a VMWare Hybrid Cloud Extension (HCX) megoldást az Azure VMWare-megoldás (AVS) privát felhőbe. A HCX lehetővé teszi a VMware-alapú számítási feladatok felhőbe és más csatlakoztatott webhelyekre történő áttelepítését a különböző beépített HCX támogatott áttelepítési típusokon keresztül.

A HCX Advanced, az alapértelmezett telepítés legfeljebb három külső helyet támogat. Ha több mint három hely szükséges, az ügyfeleknek lehetővé kell tenniük a HCX Enterprise bővítmény támogatását. A HCX Enterprise telepítése további díjakat biztosít az ügyfelek számára az általánosan elérhetővé tétel (GA) után, de [további funkciókat](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)is kínál.


Először alaposan [áttekintheti](#before-you-begin)a [szoftver verziójának követelményeit](#software-version-requirements)és [előfeltételeit](#prerequisites) . 

Ezután az összes szükséges eljárást bemutatjuk:

> [!div class="checklist"]
> * Helyszíni HCX-petesejtek üzembe helyezése
> * HCX aktiválása és konfigurálása
> * A hálózati kimenő és a szolgáltatási háló konfigurálása
> * A telepítés befejezése a készülék állapotának ellenőrzésével

A telepítés befejezése után kövesse a cikk végén elérhető, javasolt következő lépéseket.  

## <a name="before-you-begin"></a>Előkészületek
    
* Tekintse át az alapszintű AVS szoftverrel definiált adatközpont (SDDC) [oktatóanyag-sorozatot](tutorial-network-checklist.md)
* Tekintse át és hivatkozzon a [VMware HCX dokumentációra](https://docs.vmware.com/en/VMware-HCX/index.html) , beleértve a HCX felhasználói útmutatóját
* A VMware docs [Virtual Machines áttelepítése VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* A [VMware HCX üzembe helyezési szempontjainak](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html) opcionális áttekintése
* Ha szeretné áttekinteni a kapcsolódó VMware-anyagokat a HCX-on, mint például a VMware vSphere [blog sorozat](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) a HCX. 
* Az AVS HCX Enterprise aktiválása AVS-támogatási csatornákon keresztül történik.

A számítási és tárolási erőforrások méretezése alapvető tervezési lépés az AVS Private Cloud HCX megoldás használatának előkészítése során. A méretezési lépést a saját felhőalapú környezet kezdeti megtervezésének részeként kell kezelni.   

## <a name="software-version-requirements"></a>A szoftver verziójának követelményei
Az infrastruktúra-összetevőknek a szükséges minimális verziót kell futtatniuk. 
                                                         
| Összetevő típusa    | Forrás-környezeti követelmények    | A célként megadott környezeti követelmények   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Ha 5,5 U1-es vagy korábbi verzióját használ, használja az önálló HCX felhasználói felületet a HCX műveletekhez.  | 6,0 U2 és újabb verziók   |
| ESXi   | 5.0    | ESXi 6,0 és újabb verziók   |
| NSX    | A logikai kapcsolók HCX hálózati kiterjesztése a következő forrásnál: NSXv 6.2 + vagy NSX-T 2.4 +   | NSXv 6.2 + vagy NSX-T 2.4 +<br/><br/>A HCX közelségének útválasztásához: NSXv 6.4 + (a közelségi útválasztás nem támogatott a NSX-T-vel) |
| vCloud igazgató   | Nem kötelező – nincs együttműködés a vCloud igazgatóval a forrás helyén | A cél környezet vCloud-igazgatóval való integrálásakor a minimális érték a 9.1.0.2.  |

## <a name="prerequisites"></a>Előfeltételek

* A globális elérhetőséget a helyszíni és az AVS SDDC ER áramkörök között kell konfigurálni.

* Az összes szükséges portot nyitva kell lennie a helyszíni és az AVS-SDDC között (lásd a [VMware HCX dokumentációját](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* A helyszíni HCX-kezelő egy IP-címe, és legalább két IP-cím az InterConnect (IX) és a Network Extension (NE) készülékhez.

* A helyszíni HCX IX-es és a NE készülékeknek képesnek kell lenniük a vCenter és az ESXi-infrastruktúra elérésére.

* A WAN Interconnect-berendezés üzembe helyezéséhez a Azure Portal a SDDC való üzembe helyezéshez használt/22 CIDR-blokkon kívül a HCX egy/29 blokkot igényel. Ügyeljen arra, hogy ezt a követelményt a hálózat megtervezésében is figyelembe kell venni.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>A VMware HCX-petesejtek üzembe helyezése a helyszínen

1. Jelentkezzen be az AVS SDDC vCenter, és válassza a **HCX**lehetőséget.

    ![HCX kiválasztása az AVS vCenter](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Az **Adminisztráció**területen válassza a **rendszerfrissítések** lehetőséget, majd a VMware HCX-petesejt fájl letöltéséhez válassza a **kérelem letöltési hivatkozását** .

    ![Rendszerfrissítések beolvasása](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Ezután nyissa meg a helyszíni vCenter, és válasszon ki egy OVF-sablont a helyszíni vCenter való üzembe helyezéshez.  

    ![OVF-sablon kiválasztása](./media/hybrid-cloud-extension-installation/select-template.png)

1. Válassza ki a nevet és a helyet, majd válasszon ki egy erőforrást vagy fürtöt, ahol a HCX telepíteni kell. Ezután tekintse át a részleteket és a szükséges erőforrásokat.  

    ![Sablon részleteinek áttekintése](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Tekintse át a licencfeltételeket, és ha elfogadja, válassza a szükséges tárterület és hálózat lehetőséget. Ezután válassza a **tovább**lehetőséget.

1. A **sablon testreszabása**mezőben adja meg az összes szükséges információt. 

    ![Sablon testreszabása](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Kattintson a **tovább**gombra, ellenőrizze a konfigurációt, és válassza a **Befejezés** lehetőséget a HCX-petesejtek telepítéséhez.

## <a name="activate-hcx"></a>HCX aktiválása

A telepítést követően hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be a helyszíni HCX-kezelőbe `https://HCXManagerIP:9443` , és jelentkezzen be felhasználónevével és jelszavával. 

   > [!IMPORTANT]
   > Ügyeljen arra, hogy a `9443` portszámot a HCX Manager IP-címével együtt adja meg.

1. A **Licencelés**területen adja meg a **HCX speciális kulcsát**.  

    ![HCX kulcs megadása](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > A HCX-kezelőnek meg kell nyitnia az internet-hozzáférést, vagy konfigurálnia kell egy proxyt.

1. Ha szükséges, a **vCenter**szerkessze a vCenter adatait.

    ![VCenter konfigurálása](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. Ha szükséges, az **adatközpont**helyen szerkessze az adatközpont helyét.

    ![Adatbázis helye](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>HCX konfigurálása 

1. Jelentkezzen be a helyszíni vCenter, és a **Home (Kezdőlap**) területen válassza a **HCX**lehetőséget.

    ![HCX a VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Az **infrastruktúra**területen válassza a **hely**párosítása lehetőséget  >  **a hely párosításának hozzáadásához**.

    ![Hely párosításának hozzáadása](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Adja meg a távoli HCX URL-címét vagy IP-címét, az AVS cloudadmin felhasználónevét és jelszavát, majd kattintson a **kapcsolat**elemre.

   A rendszeren a csatlakoztatott hely látható.
   
    ![Hely kapcsolata](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Az **infrastruktúra**területen válassza az **összekötő**  >  **többhelyes szolgáltatás háló**  >  **hálózati profilok**  >  **hálózati profil létrehozása**lehetőséget.

    ![Hálózati profil létrehozása](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Az új hálózati profilnál adja meg a IX-es és a NE IP-címtartományok HCX (a IX-es és a NE berendezések esetében legalább két IP-cím szükséges).
    
   ![Adja meg az IP-címtartományok](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > A hálózati bővítmény berendezésének (HCX-NE) egy-az-egyhez kapcsolata van egy elosztott virtuális kapcsolóval (DVS).  

1. Most válassza a **számítási profil**  >  **Létrehozás számítási profil létrehozása**lehetőséget.

1. Adja meg a számítási profil nevét, és válassza a **Folytatás**lehetőséget.  

    ![Számítási profil létrehozása](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Válassza ki az engedélyezni kívánt szolgáltatásokat, például az áttelepítést, a hálózati bővítményt vagy a vész-helyreállítást, majd válassza a **Folytatás**lehetőséget.

    ![Szolgáltatások kiválasztása](./media/hybrid-cloud-extension-installation/select-services.png)

1. A **szolgáltatási erőforrások kiválasztása**területen válasszon ki egy vagy több olyan szolgáltatási erőforrást, amelyhez engedélyezni szeretné a kiválasztott HCX-szolgáltatásokat. Válassza a **Folytatás** elemet.
    
   ![Szolgáltatási erőforrások kiválasztása](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Válassza ki azokat a fürtöket, amelyekben a forrásként szolgáló virtuális gépeket a HCX használatával kívánja áttelepíteni.

1. Válassza az **adattár** lehetőséget, majd válassza a **Folytatás**lehetőséget. 
      
    Az HCX Interconnect-berendezések üzembe helyezéséhez válassza ki az egyes számítási és tárolási erőforrásokat. Ha több erőforrás van kiválasztva, a HCX az elsőként kiválasztott erőforrást használja a kapacitás kimerítése előtt.  
    
    ![Központi telepítési erőforrások kiválasztása](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Válassza ki a **hálózati profilokban** létrehozott felügyeleti hálózati profilt, majd válassza a **Folytatás**lehetőséget.  
      
    Válassza ki azt a hálózati profilt, amelyen keresztül a vCenter és az ESXi-gazdagépek felügyeleti felülete elérhető. Ha még nem adott meg ilyen hálózati profilt, itt is létrehozhatja.  
    
    ![Felügyeleti hálózati profil kiválasztása](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Válassza a **hálózati kimenő hálózat** lehetőséget, majd válassza a **Folytatás**lehetőséget.
      
    Válasszon ki egy vagy több hálózati profilt, hogy a következők egyike igaz legyen:  
    * A távoli helyen található Interconnect-berendezések ezen a hálózaton keresztül érhetők el  
    * A távoli oldali berendezések ezen a hálózaton keresztül érhetik el a helyi összekötő berendezéseket.  
    
    Ha olyan pont-pont típusú hálózatokkal rendelkezik, mint például a közvetlen kapcsolódás, amely nem azonos több hely között, akkor kihagyhatja ezt a lépést, mivel a számítási profilok több hellyel is megoszthatók. Ilyen esetekben a kimenő hálózati profilok felülbírálható és megadhatók az InterConnect Service Mesh létrehozásakor.  
    
    ![Kimenő hálózati profil kiválasztása](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Válassza a **VMotion hálózati profil** lehetőséget, majd válassza a **Folytatás**lehetőséget.
      
   Válassza ki azt a hálózati profilt, amelyen keresztül elérhetővé vált az ESXi-gazdagépek vMotion felülete. Ha még nem adott meg ilyen hálózati profilt, itt is létrehozhatja. Ha nem rendelkezik vMotion-hálózattal, válassza a **felügyeleti hálózati profil**lehetőséget.  
    
   ![VMotion hálózati profil kiválasztása](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. A **vSphere-replikációs hálózati profil kiválasztása**lapon válassza ki a hálózati profilt az ESXi-gazdagépek vSphere replikációs felületén, majd válassza a **Folytatás**lehetőséget.
      
   A legtöbb esetben ez a profil megegyezik a felügyeleti hálózati profillal.  
    
   ![VSphere-replikációs hálózati profil kiválasztása](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. A **hálózati bővítmények számára válassza az elosztott kapcsolók lehetőséget**, válassza ki azt a DVS-t, amelyen a hálózatokat integrálni és csatlakoztatni kívánó virtuális gépek vannak.  Válassza a **Folytatás** elemet.  
      
    ![Elosztott virtuális kapcsolók kiválasztása](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Tekintse át a kapcsolatok szabályait, és válassza a **Folytatás**lehetőséget.  

    ![Számítási profil létrehozása](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  A számítási profil létrehozásához válassza a **Befejezés** lehetőséget.

## <a name="configure-network-uplink"></a>Hálózati kimenő hálózat konfigurálása

Most konfigurálja a hálózati profil megváltozását az AVS SDDC-ben a hálózati kimenő hálózat számára.

1. Jelentkezzen be a SDDC NSX-T-re egy új logikai kapcsoló létrehozásához, vagy használjon olyan meglévő logikai kapcsolót, amely a helyszíni és az AVS SDDC közötti hálózati kimenő kapcsolathoz használható.

1. Hozzon létre egy hálózati profilt a HCX kimenő forgalomhoz az AVS SDDC, amely a helyszínen, az AVS SDDC-kommunikációhoz használható.  
    
   ![Hálózati profil létrehozása a kimenő hálózat számára](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Adja meg a hálózati profil nevét, valamint legalább 4-5 szabad IP-címet a szükséges L2 hálózati bővítmény alapján.  
    
   ![Hálózati profil konfigurálása a kimenő hálózat számára](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Válassza a **Létrehozás** lehetőséget az AVS SDDC konfigurációjának befejezéséhez

## <a name="configure-service-mesh"></a>A Service Mesh konfigurálása

Most konfigurálja a Service Mesh szolgáltatást a helyszíni és az AVS SDDC között.

1. Jelentkezzen be az AVS SDDC vCenter, és válassza a **HCX**lehetőséget.

2. Az **infrastruktúra**területen válassza az **összekötő**  >  **szolgáltatás hálójának**  >  **létrehozása szolgáltatás rácsvonal** lehetőséget az előző lépésekben létrehozott hálózati és számítási profilok konfigurálásához.    
      
    ![A Service Mesh konfigurálása](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. Válassza a párosított helyek lehetőséget a hibrid képesség engedélyezéséhez, majd válassza a **Folytatás**lehetőséget.   
    
    ![Párosított helyek kiválasztása](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. Válassza ki a forrás-és távoli számítási profilokat a hibrid szolgáltatások engedélyezéséhez, majd válassza a **Folytatás**lehetőséget.
      
    A beállítások határozzák meg azokat az erőforrásokat, amelyekben a virtuális gépek használhatják a HCX szolgáltatásokat.  
      
    ![Hibrid szolgáltatások engedélyezése](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. Válassza ki az engedélyezni kívánt szolgáltatásokat, majd válassza a **Folytatás**lehetőséget.  
      
    ![HCX-szolgáltatások kiválasztása](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. A **speciális konfigurációban – a kimenő hálózati profilok felülbírálása** válassza a **Folytatás**lehetőséget.  
      
    A kimenő hálózati profilok használatával csatlakozhat ahhoz a hálózathoz, amelyen keresztül a távoli hely összekötő készülékei elérhetők.  
      
    ![Kimenő profilok felülbírálása](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. Válassza **a hálózati bővítmény berendezésének konfigurálása**lehetőséget. 
      
    ![Hálózati bővítmény felskálázása](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. Adja meg a DVS-kapcsolók számának megfelelő készülékek darabszámát.  
      
    ![Berendezések számának konfigurálása](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. Válassza a **Folytatás** lehetőséget a kihagyáshoz.  
      
    ![Traffic Engineering konfigurálása](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. Tekintse át a topológia előzetes verzióját, és válassza a **Folytatás**lehetőséget. 

11. Adja meg a szolgáltatás rácsvonalának felhasználóbarát nevét, és válassza a **Befejezés gombot a befejezéshez** .  
      
    ![A Service Mesh befejezése](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    A szolgáltatás rácsvonala telepítve és konfigurálva van.  
      
    ![Üzembe helyezett szolgáltatás hálója](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Berendezés állapotának keresése
A készülék állapotának vizsgálatához válassza az **összekötő**  >  **berendezések**elemet. 
      
![Berendezés állapota](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Következő lépések

Ha a készülék összekötő **alagútjának állapota** felfelé és zöldre van **állítva** , készen áll az AVS virtuális gépek áttelepítésére és védelemre a HCX használatával. Tekintse meg a VMware [HCX dokumentációját](https://docs.vmware.com/en/VMware-HCX/index.html) és a VMware [HCX átVirtual Machines telepítését](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) a VMware technikai dokumentációjában.
