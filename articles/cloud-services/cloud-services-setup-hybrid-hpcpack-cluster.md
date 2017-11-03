---
title: "Az Azure-ban hibrid HPC Pack fürt beállítása |} Microsoft Docs"
description: "Megtudhatja, hogyan használja a Microsoft HPC Pack és az Azure kis, hibrid nagy teljesítményű számítástechnikai (HPC) fürt beállítása"
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: hpc-pack
ms.assetid: 
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: f6dc9657e64160be1e68a7356863b53131e9b3c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>A hibrid nagy teljesítményű számítástechnikai igény Azure számítási csomópontok és a Microsoft HPC Pack (HPC) fürt beállítása
A Microsoft HPC Pack 2012 R2 és az Azure használatával állítson be egy kisméretű, hibrid nagyteljesítményű számítástechnikai (HPC) fürt. A fürt látható az ebben a cikkben egy helyszíni HPC Pack átjárócsomópont tartalmaz, és néhány számítási csomópont telepítése egy Azure-ban igény a felhőalapú szolgáltatás. Ezt követően futtathatja a számítási feladatok, a hibrid fürtön.

![Hibrid HPC-fürt][Overview] 

Ez az oktatóanyag egy módszert használja, más néven a fürt "kapacitásnövelés a felhőbe, a" számítási igényű alkalmazások futtatásához méretezhető, igény szerinti Azure-erőforrások használatára.

Ez az oktatóanyag azt feltételezi, hogy a számítási fürtök vagy HPC Pack nincs korábbi tapasztalata kapcsolatban. Az csak segítségével rendelheti hozzá egy hibrid számítási fürt gyors bemutatási célokra szolgál. Szempontjait és lépéseit, nagyobb léptékű termelési környezetben hibrid HPC Pack fürt központi telepítése, vagy HPC Pack 2016 használ, tekintse meg a [részletes útmutatást](http://go.microsoft.com/fwlink/p/?LinkID=200493). HPC Pack egyéb forgatókönyvek, beleértve a fürtöt tartalmazó környezetben az Azure virtuális gépeken automatikus című [HPC-fürt beállítások Microsoft HPC Pack az Azure-ban](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) néhány percig.
* **Egy Windows Server 2012 R2 vagy Windows Server 2012 rendszert futtató helyszíni számítógép** -ezt a számítógépet használja, a HPC-fürt átjárócsomópontjához. Ha már nem futtat Windows Server, töltse le és telepítse az [próbaverzió](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * A számítógép Active Directory-tartományhoz kell csatlakoztatni. Tesztelési célokra konfigurálhatja az átjárócsomópont számítógép tartományvezérlőként működik. Vegye fel az Active Directory tartományi szolgáltatások szerepkört, és léptesse elő tartományvezérlővé a átjárócsomópont számítógépet, a Windows Server dokumentációjában találhat.
  * HPC Pack támogatásához az operációs rendszer telepítenie kell az ezeken a nyelveken egyik: angol, japán vagy kínai (egyszerűsített).
  * Győződjön meg arról, hogy a fontos és kritikus frissítések telepítve vannak-e.
* **HPC Pack 2012 R2** - [letöltése](http://go.microsoft.com/fwlink/p/?linkid=328024) a telepítési csomag ingyenesen elérhető legfrissebb, és másolja a fájlokat a head csomópont-számítógépén. Válassza ki a telepítési fájlok a Windows Server telepítési nyelvével azonos nyelven.

    >[!NOTE]
    > Ha szeretné használni a HPC Pack 2016 helyett HPC Pack 2012 R2, további konfigurációra van szükség. Tekintse meg a [részletes útmutatást](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Tartományi fiók** -ennek a fióknak helyi rendszergazdai engedélyekkel rendelkező átjárócsomópontjához HPC csomag telepítéséhez be kell állítani.
* **TCP-kapcsolatot a 443-as porton** Azure központi csomópontjából.

## <a name="install-hpc-pack-on-the-head-node"></a>HPC Pack telepítését az átjárócsomópont
Először telepítenie kell a Microsoft HPC Pack a helyszíni Windows Server rendszert futtató számítógépen. Ez a számítógép elérhetővé válik a fürt átjárócsomópontjához.

1. Jelentkezzen be az átjárócsomóponthoz, amely helyi rendszergazdai engedélyekkel rendelkezik tartományi fiókkal.

2. A HPC Pack telepítési varázsló elindításához futtassa a Setup.exe a HPC Pack fájlokból.

3. Az a **HPC Pack 2012 R2 telepítő** kattintson **új telepítés vagy az új szolgáltatások hozzáadása egy meglévő telepítéshez**.

    ![HPC Pack 2012 telepítése][install_hpc1]

4. Az a **Microsoft szoftver felhasználói oldal**, kattintson a **következő**.

5. Az a **telepítés típusának kiválasztása** kattintson **hozzon létre egy új HPC-fürtöt hozzon létre egy átjárócsomóponttal**, és kattintson a **következő**.

6. A varázsló futtat több telepítés előtti vizsgálat. Kattintson a **következő** a a **telepítési szabályokat** lapon, ha az összes teszt sikeresen lezajlott. Ellenkező esetben tekintse át a megadott adatokat, és a szükséges módosításokat a környezetben. Majd futtassa újból a vizsgálatot, vagy szükség esetén a telepítési varázsló ismételt futtatása.
7. Az a **HPC DB konfigurációs** lapon, győződjön meg arról, hogy **Átjárócsomópont** van kiválasztva a HPC-adatbázisok, és kattintson **következő**. 

    ![Adatbázis-konfiguráció][install_hpc4]

8. Fogadja el az alapértelmezett beállításokat a varázsló többi lapja. Az a **szükséges összetevők telepítése** kattintson **telepítése**.
   
    ![Telepítés][install_hpc6]

9. A telepítés befejezése után törölje a jelet **indítsa el a HPC Cluster Manager** majd **Befejezés**. (A start HPC Cluster Manager egy későbbi lépésben.)
   
    ![Befejezés][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Az Azure-előfizetés előkészítése
Hajtsa végre a következő lépéseket a [Azure-portálon](https://portal.azure.com) Azure-előfizetéséhez. A lépések elvégzése után telepíthet a helyszíni átjárócsomópont az Azure csomópontokat. 
  
  > [!NOTE]
  > Továbbá jegyezze fel a Azure-előfizetése Azonosítóját, ami később szüksége. Keresse meg a Azonosítóját **előfizetések** a portálon.
  > 

### <a name="upload-the-default-management-certificate"></a>Az alapértelmezett felügyeleti tanúsítvány feltöltése
HPC Pack egy önaláírt tanúsítványt telepít az átjárócsomóponthoz, az alapértelmezett Microsoft HPC Azure felügyeleti tanúsítványt feltöltheti az Azure felügyeleti tanúsítvány neve. Ez a tanúsítvány előírt tesztelési és a koncepció igazolása központi telepítésére, az átjárócsomópont és az Azure közötti kapcsolat biztonságos.

1. Az átjárócsomópont a számítógépről, jelentkezzen be a [Azure-portálon](https://portal.azure.com).

2. Kattintson a **előfizetések** > *your_subscription_name*.

3. Kattintson a **felügyeleti tanúsítványok** > **feltöltése**.4. Keresse meg a fájl C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer a head csomóponton. Kattintson a **feltöltése**.

   
A **alapértelmezett HPC Azure felügyeleti** tanúsítvány megjelenik a felügyeleti tanúsítványok listájában.

### <a name="create-an-azure-cloud-service"></a>Azure-felhőszolgáltatás létrehozása
> [!NOTE]
> A legjobb teljesítmény érdekében hozzon létre a felhőalapú szolgáltatáshoz és a storage-fiók (egy későbbi lépésben) ugyanabban a földrajzi régióban.
> 
> 

1. Kattintson a portál **Felhőszolgáltatásokhoz (klasszikus)** > **+ Hozzáadás**.

2.  A szolgáltatás DNS-nevet, válasszon egy erőforráscsoportot és egy helyet, és kattintson **létrehozása**.


### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
1. Kattintson a portál **tárfiókok (klasszikus)** > **+ Hozzáadás**.

2. Írja be a fiók nevét, majd válassza ki a **klasszikus** üzembe helyezési modellben.

3. Válasszon egy erőforráscsoportot és egy helyet, és egyéb beállításokat hagyja alapértelmezett értékeket. Ezt követően kattintson a **Create** (Létrehozás) gombra.

## <a name="configure-the-head-node"></a>Az átjárócsomópont konfigurálása
Telepítse az Azure csomópontokat és feladatok elküldéséhez a HPC Cluster Manager, először hajtsa végre néhány szükséges konfigurációs lépéseket.

1. A head csomóponton indítsa el a HPC Cluster Manager. Ha a **Head csomópont kiválasztása** párbeszédpanel, kattintson a **helyi számítógép**. A **telepítési feladatlista** jelenik meg.

2. A **szükséges telepítési feladatok**, kattintson a **a hálózat konfigurálásához**.
   
    ![Hálózat konfigurálása][config_hpc2]

3. Válassza ki a hálózat beállítása varázsló **minden csomópont csak a vállalati hálózaton** (topológia 5). A hálózati konfigurációt a legegyszerűbb bemutatási célokra.
   
    ![5 topológia][config_hpc3]
   
4. Kattintson a **tovább** a varázsló fennmaradó oldalain alapértelmezett értékek elfogadásához. Ezt követően a **felülvizsgálati** lapra, majd **konfigurálása** a hálózati konfiguráció befejezéséhez.

5. Az a **telepítési feladatlista**, kattintson a **telepítési hitelesítő adatok**.

6. Az a **telepítés hitelesítő adatainál** párbeszédpanelen írja be a HPC csomag telepítéséhez használt tartományi fiók hitelesítő adatait. Ezután kattintson az **OK** gombra. 
   
    ![Telepítési hitelesítő adatok][config_hpc6]
   
7. Az a **telepítési feladatlista**, kattintson a **konfigurálása az új csomópontok elnevezési**.

8. Az a **csomópont adja meg az adatsorozat elnevezési** párbeszédpanel mezőben fogadja el az alapértelmezett névhasználati adatsorozat, és kattintson **OK**. E lépés elvégzése után, annak ellenére, hogy ebben az oktatóanyagban hozzá Azure-csomópontok automatikusan néven szerepelnek.
   
    ![Csomópont elnevezése][config_hpc8]
   
9. Az a **telepítési feladatlista**, kattintson a **csomópont-sablon létrehozása**. Az oktatóanyag későbbi részében, a csomópont sablon segítségével Azure-csomópontok hozzáadása a fürthöz.

10. A csomópont-sablon létrehozása varázsló tegye a következőket:
    
    a. Az a **csomópont-sablon típusának kiválasztása** kattintson **csomópontsablonhoz a Windows Azure**, és kattintson a **következő**.
    
    ![Csomópont sablon][config_hpc10]
    
    b. Kattintson a **következő** fogadja el az alapértelmezett sablont.
    
    c. Az a **előfizetés adatainak megadása** lapján adja meg az Azure előfizetés-Azonosítóval (az Azure-fiók adatainak érhető el). Ezt követően a **felügyeleti tanúsítvány**, tallózással keresse meg **alapértelmezett Microsoft HPC Azure Management.** Ezután kattintson a **Next** (Tovább) gombra.
    
    ![Csomópont sablon][config_hpc12]
    
    d. Az a **szolgáltatás adatainak megadása** lapon, válassza ki a felhőszolgáltatás és a storage-fiók, amelyet az előző lépésben hozott létre. Ezután kattintson a **Next** (Tovább) gombra.
    
    ![Csomópont sablon][config_hpc13]
    
    e. Kattintson a **tovább** a varázsló fennmaradó oldalain alapértelmezett értékek elfogadásához. Ezt követően a **felülvizsgálati** lapra, majd **létrehozása** csomópont-sablon létrehozásához.
    
    > [!NOTE]
    > Alapértelmezés szerint az Azure csomópont sablon beállításait tartalmazza (kiépíteni) elindításához, és állítsa le manuálisan, a csomópontok HPC Cluster Manager használatával. Ütemezés szerint elindíthassák és az Azure-csomópontok automatikusan konfigurálhatja.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Azure-csomópontok hozzáadása a fürthöz
A csomópont sablon segítségével Azure-csomópontok hozzáadása a fürthöz. A csomópontok hozzáadása a fürt tárolja a konfigurációs adatokat, így (kiépíteni) megkezdheti a felhőalapú szolgáltatás bármikor őket. Az előfizetés csak lekérdezi többletfizetésre volna szükség Azure-csomópontok után a példányok futnak a felhőalapú szolgáltatáshoz.

Kövesse az alábbi lépéseket, ha két kis fürtcsomópontokat szeretne hozzáadni.

1. A HPC-kezelőben kattintson **csomópont-felügyelet** (nevű **erőforrás-kezelés** HPC Pack aktuális verziói) > **csomópont hozzáadása**.
   
    ![Csomópont hozzáadása][add_node1]

2. Csomópont hozzáadása varázsló a a **módszer kiválasztása** kattintson **hozzáadása Windows Azure-csomópontokra**, és kattintson a **következő**.
   
    ![Az Azure csomópont hozzáadása][add_node1_1]

3. Az a **adja meg az új csomópontok** lapon, válassza ki a korábban létrehozott Azure csomópont sablont (alapértelmezés szerint nevű **alapértelmezett AzureNode sablon**). Adja meg **2** méretű csomópontok **kis**, és kattintson a **következő**.
   
    ![Adja meg a csomópontok][add_node2]
   
4. Az a **csomópont hozzáadása varázsló-Befejezés** kattintson **Befejezés**.
    
     Két Azure-csomópontok, nevű **AzureCN-0001** és **AzureCN-0002**, mostantól a HPC Cluster Manager jelenik meg. A mindkettő a **nem telepített** állapotát.
   
    ![Felvett csomópontjain][add_node3]

## <a name="start-the-azure-nodes"></a>Indítsa el az Azure-csomópontok
Ha azt szeretné, a fürt erőforrásainak használatához az Azure-ban, használja a HPC Cluster Manager start (kiépíteni) az Azure-csomópontok és online állapotba kerüljön.

1. A HPC-kezelőben kattintson **csomópont-felügyelet** (nevű **erőforrás-kezelés** HPC Pack aktuális verziói), és válassza ki az Azure-csomópontok.

2. Kattintson a **Start**, és kattintson a **OK**.
   
   ![Kiinduló csomópont][add_node4]
   
    Áttérés a csomópontok a **kiépítési** állapotát. Megtekintheti a telepítési naplót a kiépítési folyamat előrehaladását.
   
    ![Kiépítés csomópontok][add_node6]

3. Néhány perc elteltével az Azure-csomópontok üzembe helyezése, és a **Offline** állapotát. Ebben az állapotban lévő szerepkör-példányok futnak, de még nem a fürt feladatok fogadására.

4. Győződjön meg arról, hogy a szerepkörpéldányok futnak, az Azure portálon kattintson a **Felhőszolgáltatások (klasszikus)** > *your_cloud_service_name*.
   
   Két kell megjelennie **HpcWorkerRole** a szolgáltatásban futó példányok (csomópontok). HPC Pack is automatikusan telepíti a két **HpcProxy** példányok (közepes méretű), az átjárócsomópont és az Azure közötti kommunikáció kezelésére.

   ![Futó példányát tekintve][view_instances1]

5. Ahhoz, hogy az Azure-csomópontok online fürtben feladatok futtatásához, válassza ki a csomópontot, kattintson a jobb gombbal, és végül **online állapotba hozás**.
   
    ![Kapcsolat nélküli csomópontok][add_node7]
   
    HPC Fürtkezelő azt jelzi, hogy a csomópontok a a **Online** állapotát.

## <a name="run-a-command-across-the-cluster"></a>Futtassa a parancsot a fürtön
A telepítés ellenőrzéséhez használja a HPC Pack **clusrun** parancsot kell futtatni egy parancsot vagy az alkalmazás egy vagy több fürtcsomóponton. Egyszerű példaként használható **clusrun** beolvasni az Azure-csomópontok IP-konfigurációja.

1. A head csomóponton nyisson meg egy parancssort rendszergazdaként.

2. Írja be a következő parancsot:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Ha a rendszer kéri, adja meg a fürt rendszergazdai jelszót. Meg kell jelennie a következőhöz hasonló parancs kimenetét.
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>Egy tesztelési feladat futtatása
Most már a hibrid fürtön futó teszt feladat elküldése. Ebben a példában egy egyszerű paraméteres ismétlés feladat (belsőleg párhuzamos számítási típusú). Ebben a példában résztevékenység, vegyen fel egy egész számot maga segítségével futtatja a **/a beállítása** parancsot. A fürt összes csomópontjának hozzájárul az egész számok résztevékenység befejezési 1 és 100.

1. A HPC-kezelőben kattintson **feladatkezelés** > **új paraméteres Sweep feladat**.
   
    ![Új feladat][test_job1]

2. Az a **új paraméteres Sweep feladat** párbeszédpanel **parancssori**, típus `set /a *+*` (felülírja az alapértelmezett parancssor, amely akkor jelenik meg). Hagyja meg a többi beállítást tartozó alapértelmezett értékeket, és kattintson **Submit** elküldeni a feladatot.
   
    ![Paraméteres ismétlés][param_sweep1]

3. Ha a feladat befejeződött, kattintson duplán a **saját Sweep feladat** feladat.

4. Kattintson a **nézet feladatai**, majd kattintson egy részfeladatnál annak regisztrálása adott részfeladatnál annak regisztrálása számított eredményének megtekintéséhez.
   
    ![A feladat eredménye][view_job361]

5. Melyik csomópontján elvégzi a számítást, hogy részfeladatnál annak regisztrálása megtekintéséhez kattintson **lefoglalt csomópontok**. (A fürt előfordulhat, hogy megjelenítése egy másik csomópont neve).
   
    ![A feladat eredménye][view_job362]

## <a name="stop-the-azure-nodes"></a>Állítsa le az Azure-csomópontok
Próbálja ki a fürtöt, az Azure-fiókjába felesleges költségek elkerülése érdekében csomópontok le. Ez a lépés leállítja a felhőszolgáltatást, és eltávolítja a Azure szerepkörpéldányokat.

1. A HPC Cluster Manager, a **csomópont-felügyelet** (nevű **erőforrás-kezelés** HPC Pack korábbi verzióiban), válassza ki mindkét Azure-csomópontok. Kattintson a **leállítása**.
   
    ![Csomópont leállítása][stop_node1]

2. Az a **állítsa le a Windows Azure-csomópontokra** párbeszédpanel, kattintson a **leállítása**. 
   
3. Áttérés a csomópontok a **leállítása** állapotát. Néhány perc elteltével HPC Cluster Manager azt mutatja, hogy a csomópontok **nem telepített**.
   
    ![Nem telepített csomópontok][stop_node4]

4. Győződjön meg róla, hogy a szerepkörpéldányok már nem fut, az Azure-ban az Azure portálon kattintson a **Felhőszolgáltatásokhoz (klasszikus)** > *your_cloud_service_name*. Nincs példány az éles környezetben vannak telepítve. 
   
    Ezzel befejezte az oktatóanyag.

## <a name="next-steps"></a>Következő lépések
* Megismerkedhet a dokumentációja [HPC Pack](https://technet.microsoft.com/library/cc514029).
* HPC Pack fürttelepítés nagyobb léptékű hibrid beállításához tekintse meg a [Azure feldolgozói szerepkör példányokhoz Microsoft HPC Pack kapacitásnövelés](http://go.microsoft.com/fwlink/p/?LinkID=200493).
* Egyéb módon hozhat létre a HPC Pack-fürtöt az Azure-ban, beleértve az Azure Resource Manager-sablonokkal, lásd: [HPC-fürt beállítások Microsoft HPC Pack az Azure-ban](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png
