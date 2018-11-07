---
title: Az Azure-ban hibrid HPC Pack-fürt telepítése |} A Microsoft Docs
description: Útmutató a kisméretű, hibrid nagy teljesítményű feldolgozási (HPC-) fürt beállítása Microsoft HPC Pack és az Azure használatával
services: cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: hpc-pack
ms.assetid: ''
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: 55dfd7e5ea93ae941d73612cc70ed82d48db725a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236738"
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Nagy teljesítményű feldolgozási (HPC-) fürt beállítása a Microsoft HPC Pack és igény szerinti Azure-beli számítási csomópontok használatával
A Microsoft HPC Pack 2012 R2 és az Azure segítségével egy kisméretű, hibrid nagy teljesítményű (HPC-) fürt beállítása. A jelen cikkben ismertetett fürt tartalmaz egy helyszíni HPC Pack átjárócsomóponthoz, és egyes számítási csomópontok üzembe az Azure-ban igény a felhőalapú szolgáltatás. Futtathatja a számítási feladatok a hibrid fürtön.

![Hibrid HPC-fürt][Overview] 

Ez az oktatóanyag bemutatja egy módszert használja, más néven "adatlöket a felhőbe," fürt számításigényes alkalmazásokat futtathat a skálázható, igény szerinti Azure-erőforrások használatával.

Ez az oktatóanyag feltételezi, hogy nincs korábbi tapasztalata az számítási fürtök és a HPC Pack. Célja, hogy csak, amelyek segítenek a gyors bemutató célokra hibrid számítási fürt üzembe helyezése. Megfontolandó szempontok és a nagyobb léptékű termelési környezetben hibrid HPC Pack-fürt üzembe helyezése, vagy használja a HPC Pack 2016 lépéseket lásd: a [részletes útmutatót](https://go.microsoft.com/fwlink/p/?LinkID=200493). Más esetekben a HPC Pack segítségével, beleértve a automatikus fürt üzembe helyezése Azure virtual machines szolgáltatásban, lásd: [a Microsoft HPC Packkal Azure-beli HPC fürtbeállítások](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) mindössze néhány perc alatt.
* **Egy Windows Server 2012 R2 vagy Windows Server 2012 rendszert futtató helyszíni számítógép** – ezen a számítógépen használja a HPC-fürt főcsomópontjához. Ha már nem fut a Windows Server, töltse le és telepítse egy [próbaverzió](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * A számítógép Active Directory-tartományhoz kell csatlakoznia. Tesztelési célokra beállíthatja a fő csomópont-számítógépén tartományvezérlőként. Az Active Directory tartományi szolgáltatások kiszolgálói szerepkör hozzáadása, és léptesse elő a fő csomópontot számítógépet tartományvezérlőként, a Windows Server dokumentációjában talál.
  * HPC Pack támogatásához az operációs rendszer telepítenie kell az ezeken a nyelveken egyik: angol, japán és kínai (egyszerűsített).
  * Győződjön meg arról, hogy a kritikus frissítések telepítve vannak-e.
* **HPC Pack 2012 R2** - [letöltése](https://go.microsoft.com/fwlink/p/?linkid=328024) a telepítési csomag legújabb verziójának díjmentes, és másolja a fájlokat a fő csomópont-számítógépén. Válassza ki ugyanazt a nyelvet, mint a telepített Windows Server telepítési fájljait.

    >[!NOTE]
    > Ha szeretné használni a HPC Pack 2016 helyett a HPC Pack 2012 R2, a további konfigurációs van szükség. Tekintse meg a [részletes útmutatót](https://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Tartományi fiók** – ennek a fióknak helyi rendszergazdai engedélyekkel az átjárócsomópont telepítése a HPC Pack kell konfigurálni.
* **A 443-as TCP-kapcsolat** az Azure-bA a fő csomópontot.

## <a name="install-hpc-pack-on-the-head-node"></a>HPC Pack telepíthető az átjárócsomóponthoz
Először telepítenie kell a Microsoft HPC Pack a Windows Servert futtató helyszíni számítógép. Ez a számítógép lesz a fürt fő csomópontjának.

1. Jelentkezzen be egy tartományi fiókkal, amely helyi rendszergazdai engedélyekkel rendelkezik a fő csomópontot.

2. A HPC-csomag telepítése varázsló elindításához futtassa a Setup.exe a HPC Pack telepítési fájlokból.

3. Az a **HPC Pack 2012 R2 telepítő** kattintson **új telepítést vagy új szolgáltatások hozzáadása egy meglévő telepítéshez**.

    ![HPC Pack 2012 telepítése][install_hpc1]

4. Az a **Microsoft szoftver felhasználói vonatkozó megállapodás lapján**, kattintson a **tovább**.

5. Az a **telepítés típusának kiválasztása** kattintson **hozzon létre egy új HPC-fürt fő csomópontjának létrehozása**, és kattintson a **tovább**.

6. A varázsló a futtatása több telepítés előtti vizsgálat. Kattintson a **tovább** a a **telepítési szabályok** lapon, ha minden teszt sikeresen lezajlott. Ellenkező esetben tekintse át a megadott adatokat, és végezze el a szükséges módosításokat a környezetben. Majd futtassa újból a vizsgálatot, vagy szükség esetén a telepítési varázsló elindításához ismét.
7. A a **HPC DB konfigurációs** lapon, győződjön meg arról, hogy **Átjárócsomópont** van kiválasztva a HPC-adatbázisok, és kattintson **tovább**. 

    ![DB-konfigurációja][install_hpc4]

8. Fogadja el a varázsló további lapjain az alapértelmezett beállításokat. Az a **szükséges összetevők telepítése** kattintson **telepítése**.
   
    ![Telepítés][install_hpc6]

9. A telepítés befejezése után törölje a jelet **indítsa el a HPC Cluster Manager** majd **Befejezés**. (A start HPC Cluster Manager egy későbbi lépésben.)
   
    ![Befejezés][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Készítse elő az Azure-előfizetés
Hajtsa végre a következő lépéseket a [az Azure portal](https://portal.azure.com) az Azure-előfizetésében. Ezek a lépések elvégzése után telepítheti az Azure-csomópontok a helyszíni fő csomópontból. 
  
  > [!NOTE]
  > Továbbá jegyezze fel az Azure-előfizetés azonosítója, amelyre később szüksége lesz. Az azonosító a található **előfizetések** a portálon.
  > 

### <a name="upload-the-default-management-certificate"></a>Az alapértelmezett felügyeleti tanúsítvány feltöltése
HPC Pack egy önaláírt tanúsítványt az átjárócsomóponthoz, az alapértelmezett Microsoft HPC az Azure felügyeleti tanúsítványt, mint az Azure felügyeleti tanúsítványt is feltölthet nevű telepíti. Ez a tanúsítvány lett megadva tesztelési és proof-of-concept-központitelepítése biztonságossá tenni a kapcsolatot a fő csomópontot és az Azure között.

1. Az átjárócsomópont számítógépről, jelentkezzen be a [az Azure portal](https://portal.azure.com).

2. Kattintson a **előfizetések** > *your_subscription_name*.

3. Kattintson a **felügyeleti tanúsítványok** > **feltöltése**.

4. Keresse meg a fájl C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer a központi csomóponton. Kattintson a **feltöltése**.

   
A **HPC Azure Management alapértelmezett** tanúsítvány megjelenik a felügyeleti tanúsítványok listája.

### <a name="create-an-azure-cloud-service"></a>Azure-felhőszolgáltatás létrehozása
> [!NOTE]
> A legjobb teljesítmény érdekében hozzon létre a felhőszolgáltatás és a storage-fiók (a későbbiekben) ugyanabban a földrajzi régióban.
> 
> 

1. Kattintson a portál **Cloud services (klasszikus)** > **+ Hozzáadás**.

2.  A szolgáltatás DNS-nevet, válassza ki az erőforráscsoportot és a egy olyan helyre, és kattintson **létrehozás**.


### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
1. Kattintson a portál **tárfiókok (klasszikus)** > **+ Hozzáadás**.

2. Adjon meg egy nevet a fiókhoz, és válassza ki a **klasszikus** üzemi modellt.

3. Válasszon egy erőforráscsoportot és a egy olyan helyre, és az egyéb beállításokat hagyja alapértelmezett értéken. Ezt követően kattintson a **Create** (Létrehozás) gombra.

## <a name="configure-the-head-node"></a>Az átjárócsomópont konfigurálása
Azure-csomópontok üzembe helyezéséhez és küldhetők be feladatok a HPC Cluster Manager használatához hajtsa végre néhány szükséges konfigurációs lépéseket.

1. A központi csomóponton indítsa el a HPC Cluster Manager. Ha a **válassza ki a fő csomópont** párbeszédpanel megjelenik, kattintson a **helyi számítógép**. A **telepítési feladatlista** jelenik meg.

2. A **szükséges telepítési feladatok**, kattintson a **a hálózat konfigurálásához**.
   
    ![Hálózat konfigurálása][config_hpc2]

3. Válassza ki a hálózati konfigurációs varázslójában **csak egy vállalati hálózaton lévő összes csomópont** (topológia 5). A hálózati konfigurációt a legegyszerűbb bemutatási célokra.
   
    ![Topológia 5][config_hpc3]
   
4. Kattintson a **tovább** gombra a varázsló további lapjain az alapértelmezett értékek elfogadásához. Ezután a a **felülvizsgálati** lapra, majd **konfigurálása** a hálózati konfiguráció befejezéséhez.

5. Az a **telepítési feladatlista**, kattintson a **telepítési hitelesítő adatok**.

6. Az a **telepítési hitelesítő adatok** párbeszédpanelen írja be a HPC Pack telepítéséhez használt tartományi fiók hitelesítő adatait. Ezután kattintson az **OK** gombra. 
   
    ![Telepítési hitelesítő adatok][config_hpc6]
   
7. Az a **telepítési feladatlista**, kattintson a **konfigurálása az új csomópontok elnevezési**.

8. A a **csomópont adja meg az adatsorozat elnevezési** párbeszédpanelen fogadja el az alapértelmezett névhasználati sorozat, és kattintson **OK**. E lépés elvégzése után, annak ellenére adja hozzá a ebben az oktatóanyagban az Azure-csomópontok automatikusan elnevezett.
   
    ![Csomópont elnevezése][config_hpc8]
   
9. Az a **telepítési feladatlista**, kattintson a **egy csomópont-sablon létrehozása**. Az oktatóanyag későbbi részében, a csomópont sablon használatával Azure-csomópontok hozzáadása a fürthöz.

10. A csomópont-sablon létrehozása varázslóban tegye a következőket:
    
    a. Az a **csomópont sablon típusának kiválasztása** kattintson **Windows Azure-csomópont sablon**, és kattintson a **tovább**.
    
    ![Csomópont-sablon][config_hpc10]
    
    b. Kattintson a **tovább** fogadja el az alapértelmezett sablont.
    
    c. Az a **adja meg az előfizetési adatok** lap, adja meg az Azure-előfizetése Azonosítóját (az Azure-fiókjának adatait érhető el). Ezt követően a **felügyeleti tanúsítvány**, keresse meg **alapértelmezett Microsoft HPC Azure Management.** Ezután kattintson a **Next** (Tovább) gombra.
    
    ![Csomópont-sablon][config_hpc12]
    
    d. Az a **szolgáltatás adatainak megadása** lapra, jelölje be a felhőszolgáltatás és a storage-fiók, amely az előző lépésben létrehozott. Ezután kattintson a **Next** (Tovább) gombra.
    
    ![Csomópont-sablon][config_hpc13]
    
    e. Kattintson a **tovább** gombra a varázsló további lapjain az alapértelmezett értékek elfogadásához. Ezután a a **felülvizsgálati** lapra, majd **létrehozás** a csomópont-sablon létrehozásához.
    
    > [!NOTE]
    > Alapértelmezés szerint az Azure-csomópont sablon tartalmazza a beállításait, hogy indítsa el a (kiépíteni), és állítsa le manuálisan, a csomópontok HPC Cluster Manager segítségével. Igény szerint konfigurálható kezdődik és az Azure-csomópontok automatikusan, ütemezés szerint.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Azure-csomópontok hozzáadása a fürthöz
Most már a csomópont-sablon használatával Azure-csomópontok hozzáadása a fürthöz. A csomópontok felvétele a fürtbe tárolja a konfigurációs adatokat, így (kiépíteni) elindíthatja azokat a felhőben lévő tetszőleges időpontban. Az előfizetés csak lekérdezi alapján számítjuk fel az Azure-csomópontok után a példányokon futnak, a cloud service-ben.

Kövesse az alábbi lépéseket két kis csomópontokat.

1. Kattintson a HPC Cluster Manager **csomópont-felügyelet** (nevű **erőforrás-kezelés** HPC Pack aktuális verziója) > **csomópont hozzáadása**.
   
    ![Csomópont hozzáadása][add_node1]

2. Csomópont hozzáadása varázsló a a **üzembe helyezési módszer kiválasztása** kattintson **hozzáadása Windows Azure-csomópontok**, és kattintson a **tovább**.
   
    ![Azure-csomópont hozzáadása][add_node1_1]

3. Az a **adja meg az új csomópontok** lapon, válassza ki a korábban létrehozott Azure-csomópont sablon (alapértelmezés szerint nevű **alapértelmezett AzureNode sablon**). Adja meg **2** méretű csomópontok **kis**, és kattintson a **tovább**.
   
    ![Adja meg a csomópontok][add_node2]
   
4. Az a **csomópont hozzáadása varázsló-Befejezés** kattintson **Befejezés**.
    
     Két Azure-csomópontok, nevű **AzureCN-0001** és **AzureCN-0002**, most már a HPC Cluster Manager jelennek meg. A mindkettő a **nem telepített** állapota.
   
    ![Hozzáadott csomópontokon][add_node3]

## <a name="start-the-azure-nodes"></a>Indítsa el az Azure-csomópontok
Ha meg szeretné használni a fürt erőforrásainak az Azure-ban, használja a HPC Cluster Manager indítása (kiépíteni) az Azure-csomópontok és online állapotba helyezés.

1. A HPC-kezelőben kattintson **csomópont-felügyelet** (nevű **erőforrás-kezelés** HPC Pack jelenlegi verzióiban), és válassza ki az Azure-csomópontok.

2. Kattintson a **Start**, és kattintson a **OK**.
   
   ![Indítsa el a csomópontok][add_node4]
   
    Áttérés a csomópontok a **kiépítési** állapota. Az üzembe helyezési folyamat nyomon kiépítési napló megtekintéséhez.
   
    ![Csomópontok üzembe helyezése][add_node6]

3. Néhány perc elteltével az Azure-csomópontok üzembe helyezése és a rendszer a **Offline** állapota. Ebben az állapotban a szerepkör példányai futnak, de még nem tudja elfogadni a fürt feladatok.

4. Győződjön meg arról, hogy a szerepkör példányai futnak, az Azure Portalon kattintson a **Felhőszolgáltatások (klasszikus)** > *your_cloud_service_name*.
   
   Két kell megjelennie **HpcWorkerRole** példányai (csomópontok) a szolgáltatásban. HPC Pack is automatikusan telepíti a két **HpcProxy** kezeli a fő csomópontot és Azure közötti kommunikáció-példányok (közepes méretű).

   ![Futó példányát tekintve][view_instances1]

5. Ahhoz, hogy a fürt-feladatok futtatása az Azure-csomópontok online, válassza a csomópontot, kattintson a jobb gombbal, és kattintson **online állapotba hozás**.
   
    ![A kapcsolat nélküli csomópont][add_node7]
   
    HPC Cluster Manager, az azt jelzi, hogy a csomópontok vannak a **Online** állapota.

## <a name="run-a-command-across-the-cluster"></a>Futtassa a parancsot a fürtön
A telepítés ellenőrzéséhez használja a HPC Pack **clusrun** parancs futtatása egy parancs vagy az alkalmazás egy vagy több fürtcsomóponton. Egyszerű példaként, használhatja **clusrun** IP-konfigurációja, az Azure-csomópontok eléréséhez.

1. A fő csomópontot nyissa meg egy parancssort rendszergazdaként.

2. Írja be a következő parancsot:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Ha a rendszer kéri, adja meg a fürt rendszergazdai jelszót. Parancs kimenete az alábbihoz hasonlóan kell megjelennie.
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>Vizsgálati feladat futtatása
Most küldje el egy teszt futó feladatot, a hibrid fürtön. Ebben a példában egy egyszerű paraméteres feladatot (a belsőleg párhuzamos számítási típus). Ebben a példában, amelyeket egy egész szám hozzáadása a saját maga segítségével futtatja a **/a beállítása** parancsot. A fürt összes csomópontja járul hozzá az egész számok altevékenységhez befejezése 1 és 100.

1. Kattintson a HPC Cluster Manager **feladatkezelés** > **új paraméteres Szögtartomány feladat**.
   
    ![Új feladat][test_job1]

2. Az a **új paraméteres Szögtartomány feladat** párbeszédpanel **parancssori**, típus `set /a *+*` (felülírják az alapértelmezett parancssor, amely akkor jelenik meg). Hagyja bejelölve az alapértelmezett értékeket a többi beállításnál, és kattintson **küldés** a feladat elküldéséhez.
   
    ![Paraméteres ismétlés][param_sweep1]

3. Ha a feladat elkészült, kattintson duplán a **saját Szögtartomány feladat** feladat.

4. Kattintson a **nézet feladatai**, majd kattintson az egy adott alfeladat számított eredményének megtekintéséhez alfeladat.
   
    ![Feladat eredményei][view_job361]

5. Tekintse meg, hogy melyik csomópont adott alfeladat hajtottak végre a számítást, kattintson a **lefoglalt csomópontok**. (A fürt előfordulhat, hogy megjelenítése egy másik csomópont nevét).
   
    ![Feladat eredményei][view_job362]

## <a name="stop-the-azure-nodes"></a>Állítsa le az Azure-csomópontok
Próbálja ki a fürtöt, miután állítsa le a fiókjához a felesleges költségek elkerülése érdekében az Azure-csomópontok. Ebben a lépésben leállítja a felhőszolgáltatást, és eltávolítja az Azure szerepkör példányai.

1. A HPC Cluster Manager, a **csomópont-felügyelet** (nevű **erőforrás-kezelés** HPC Pack korábbi verzióiban), válassza ki mindkét Azure-csomópontok. Kattintson a **leállítása**.
   
    ![Csomópont leállítása][stop_node1]

2. Az a **állítsa le a Windows Azure-csomópontok** párbeszédpanelen kattintson a **leállítása**. 
   
3. Áttérés a csomópontok a **leállítása** állapota. Néhány perc múlva HPC Cluster Manager azt mutatja, hogy a csomópontok **nem telepített**.
   
    ![Nem telepített csomópontok][stop_node4]

4. Győződjön meg róla, hogy a szerepkörpéldányok már nem fut az Azure-ban, az Azure Portalon kattintson a **Cloud services (klasszikus)** > *your_cloud_service_name*. Az éles környezetben telepített példány sem. 
   
    Ezzel befejezte az oktatóanyag.

## <a name="next-steps"></a>További lépések
* Fedezze fel a dokumentációban [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Hibrid HPC Pack fürtök üzembe helyezése nagyobb léptékű beállításával kapcsolatban lásd: [a csúcsterhelések átirányítása az Azure feldolgozói szerepkör példányai a Microsoft HPC Packkel](https://go.microsoft.com/fwlink/p/?LinkID=200493).
* HPC Pack-fürt létrehozása az Azure egyéb módjai, beleértve az Azure Resource Manager-sablonok használatával lásd: [a Microsoft HPC Packkal Azure-beli HPC fürtbeállítások](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


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
