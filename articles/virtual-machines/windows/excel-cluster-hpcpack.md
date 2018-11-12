---
title: HPC Pack-fürthöz az Excel és SOA típusú |} A Microsoft Docs
description: Ismerkedés a nagyméretű Excel és SOA típusú számítási feladatokat futtat az Azure-beli HPC Pack-fürthöz
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: f5b8d3aa69d6a141394395f012e5cc57873cafaf
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235938"
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Ismerkedés az Excel és SOA típusú számítási feladatok futtatása az Azure-beli HPC Pack-fürthöz
Ez a cikk bemutatja, hogyan helyezhet üzembe Azure-beli virtuális gépeken a Microsoft HPC Pack 2012 R2-fürtöt az Azure gyorsindítási sablon, vagy igény szerint az Azure PowerShell telepítési parancsfájlt. A fürt használ az Azure piactér Virtuálisgép-rendszerképek a Microsoft Excel- vagy szolgáltatásorientált architektúra (SOA) számítási feladatok futnak a HPC Pack segítségével. A fürt használhatja egy a helyszíni ügyfélszámítógépről Excel a HPC- és SOA típusú szolgáltatások futtatásához. Az Excel a HPC-szolgáltatások közé tartoznak az Excel-munkafüzet kiürítéséhez, és az Excel-felhasználó által definiált függvények, vagy az UDF-EK.

> [!IMPORTANT] 
> Ez a cikk a HPC Pack 2012 R2 alapul funkciók, sablonok és parancsfájlokat. Ez a forgatókönyv jelenleg nem támogatott a HPC Pack 2016-ban.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Magas szinten a következő ábrán a HPC Pack-fürt létrehozása.

![HPC-fürt csomópontjainak Excel-alapú számítási feladatait][scenario]

## <a name="prerequisites"></a>Előfeltételek
* **Ügyfélszámítógép** -minta Excel és SOA-feladatok elküldése a fürthöz az ügyfél Windows-alapú számítógépre van szüksége. Egy Windows-számítógépen, hogy az Azure PowerShell-fürt üzembe helyezési parancsfájl futtatása (Ha úgy dönt, hogy a központi telepítési módszer) is szükséges.
* **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.
* **Magkvóta** -magok száma, a kvóta növeléséhez előfordulhat, hogy szüksége, különösen akkor, ha telepít több fürtcsomópont, Többmagos Virtuálisgép-mérettel. Az Azure gyorsindítási sablon használ, a Resource Managerben magkvóta-e Azure-régiónként. Ebben az esetben szüksége lehet egy adott régióban a kvóta növeléséhez. Lásd: [Azure-előfizetés korlátai, kvótái és megkötései](../../azure-subscription-service-limits.md). A kvóta növeléséhez [nyisson meg egy támogatási kérést online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) díjmentesen.
* **A Microsoft Office-licenc** – Ha a számítási csomópontok Marketplace HPC Pack 2012 R2 Virtuálisgép-lemezkép használatával a Microsoft Excel, a Microsoft Excel Professional Plus 2013 egy 30 napos próbaidőszakában verziója van telepítve. A próbaidőszak után meg kell adnia egy érvényes Microsoft Office-licencet aktiválásához továbbra is számítási feladatok futtatásához az Excel. Lásd: [Excel-aktiválási](#excel-activation) a cikk későbbi részében. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>1. lépés Az Azure-beli HPC Pack-fürt beállítása
Bemutatjuk a HPC Pack 2012 R2-fürt beállítása két alternatívája közül választhat: első, az Azure gyors üzembe helyezés sablon és az Azure Portalon: és a második, az Azure PowerShell telepítési parancsfájlt használ.

### <a name="option-1-use-a-quickstart-template"></a>1. lehetőség: Gyorsindítási sablon használata
Az Azure gyorsindítási sablon használatával gyorsan üzembe helyezhet a HPC Pack-fürthöz az Azure Portalon. A portálon nyissa meg a sablont, kap egy egyszerű felhasználói felület, ahol Ön adja meg a beállításokat a fürt számára. A lépések a következők. 

> [!TIP]
> Ha szeretne, használja az [Azure Marketplace-sablon](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) kifejezetten egy Excel-munkaterhelések hasonló fürtöt hoz létre. A lépések némileg eltér a következő.
> 
> 

1. Látogasson el a [HPC-fürt létrehozása a sablon lapját a githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Ha azt szeretné, tekintse át a sablon és a forráskód adatait.
1. Kattintson a **üzembe helyezés az Azure** elindítani egy központi telepítést a sablont az Azure Portalon.
   
   ![Sablon üzembe helyezése az Azure-bA][github]
1. A portálon kövesse ezeket a lépéseket adja meg a paramétereket a HPC-fürt sablon.
   
   a. Az a **paraméterek** lapon adja meg vagy módosítsa a sablon paramétereihez tartozó értékek. (Kattintson a súgó információit minden beállítás melletti ikonra.) Az alábbi képernyőn mintaértékek láthatók. Ebben a példában egy nevű fürtöt hoz létre *hpc01* a a *hpc.local* a számítási csomópontok egy fő csomópontot és 2 tartományban. A számítási csomópontok Virtuálisgép-rendszerképből egy HPC Pack, amely tartalmazza a Microsoft Excel jönnek létre.
   
   ![Paraméterek megadása][parameters-new-portal]
   
   > [!NOTE]
   > A virtuális gép automatikusan létrejön a fő csomópontot a [legújabb Piactéri lemezképhez](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) a HPC Pack 2012 R2, Windows Server 2012 R2 rendszeren. Jelenleg a lemezképet a HPC Pack 2012 R2 Update 3 alapján történik.
   > 
   > Számítási csomópont virtuális gépek a kiválasztott számítási csomópont termékcsalád legújabb rendszerképből jönnek létre. Válassza ki a **ComputeNodeWithExcel** lehetőség a legújabb HPC Pack számítási csomópont lemezképével, amely tartalmazza a Microsoft Excel Professional Plus 2013 próbaverziójának. Általános SOA-munkamenetet, vagy az Excel UDF kiürítésének, helyezhet üzembe egy fürtöt, válassza ki a **Átjárócsomópontján** (nélkül telepítve Excel) lehetőséget.
   > 
   > 
   
   b. Válassza ki az előfizetést.
   
   c. Például hozzon létre egy erőforráscsoportot, a fürt *hpc01RG*.
   
   d. Válassza ki például az USA középső RÉGIÓJA, az erőforráscsoport helyét.
   
   e. Az a **jogi feltételek** lapon, olvassa el a feltételeket. Ha elfogadja, kattintson a **beszerzési**. Ha elkészült, az értékének megadása a sablont, kattintson a **létrehozás**.
1. Amikor befejeződik az üzembe helyezés (általában körülbelül 30 percet vesz), a fürt tanúsítványfájl exportálhat a fürt fő csomópontjának. Egy későbbi lépésben a kiszolgálóoldali hitelesíthető biztonságos HTTP-kötést az ügyfélszámítógépen a nyilvános tanúsítvány importálása.
   
   a. Az Azure Portalon nyissa meg az irányítópultot, válassza ki a fő csomópontot, majd kattintson **Connect** távoli asztali kapcsolattal csatlakozni az oldal tetején.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Standard eljárásokat követve a tanúsítványkezelőben a fő csomópont (a Cert: \LocalMachine\My alatt található) a titkos kulcs nélküli tanúsítvány exportálása. Ebben a példában exportálása *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![A tanúsítvány exportálása][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>2. lehetőség: A HPC Pack IaaS telepítési szkripttel
A HPC Pack IaaS telepítési szkripttel HPC Pack-fürt üzembe helyezése egy másik sokoldalú megoldást kínál. A fürt létrehoz a klasszikus üzemi modellben, mivel a sablont használja az Azure Resource Manager üzemi modell. A parancsfájl is kompatibilis az Azure globális vagy Azure China szolgáltatásban egy előfizetést.

**További Előfeltételek**

* **Az Azure PowerShell** - [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview) (0.8.10 verzió vagy újabb) az ügyfélszámítógépre.
* **HPC Pack IaaS telepítési szkripttel** – töltse le és csomagolja ki a legújabb verzióját a szkriptet a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). A parancsfájl a verzió ellenőrzéséhez futtassa `New-HPCIaaSCluster.ps1 –Version`. Ez a cikk 4.5.0 verzió vagy újabb verzióiban a szkript alapján.

**A konfigurációs fájl létrehozása**

 A HPC Pack IaaS telepítési szkripttel egy konfigurációs XML-fájl, amely leírja a HPC-fürtöt, az infrastruktúra bemenetként használja. A fürt fő csomópontot és a számítási csomópont lemezképével, amely tartalmazza a Microsoft Excel készített 18 számítási csomópontok üzembe helyezéséhez helyére helyettesítse be a környezetnek az alábbi minta konfigurációs fájlba értékeit. A konfigurációs fájl kapcsolatos további információkért lásd: a parancsfájl mappában található fájl Manual.rtf és [HPC-fürt létrehozása HPC Pack IaaS telepítési szkripttel](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**A konfigurációs fájl kapcsolatos megjegyzések**

* A **VMName** a fő csomópont **kell** megegyezik a **ServiceName**, vagy az SOA-feladatok futtatásához.
* Győződjön meg arról, hogy adja meg **EnableWebPortal** , hogy a fő csomópontot tanúsítvány jön létre, és exportálja.
* A fájl adja meg a konfigurációt követő PowerShell-parancsprogram, amely a fő csomópontot futtat PostConfig.ps1. Az alábbi parancsprogram konfigurálja az Azure storage kapcsolati karakterláncot, a számítási csomópont szerepkör távolít el a fő csomópontot, és lehetővé az összes csomópont online, legyenek üzembe helyezve. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**A parancsfájl futtatása**

1. Nyissa meg az ügyfélszámítógépen a PowerShell-konzolt rendszergazdaként.
1. Módosítsa a könyvtárat a script mappát (ebben a példában E:\IaaSClusterScript).
   
   ```
   cd E:\IaaSClusterScript
   ```
1. A HPC Pack-fürt üzembe helyezéséhez futtassa a következő parancsot. Ez a példa feltételezi, hogy a konfigurációs fájlban található E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

A HPC Pack üzembe helyezési parancsfájl futtatása egy kis ideig. A parancsprogram egy dolog, hogy exportálja, és töltse le a fürt tanúsítványt, és mentse az ügyfélszámítógépen a jelenlegi felhasználó Dokumentumok mappájának. A szkript egy a következőhöz hasonló üzenetet hoz létre. Egy következő lépésben importálja a tanúsítványt a megfelelő tárolóba.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>2. lépés Excel-munkafüzetek kiszervezheti, és felhasználói függvények futtatása egy helyi ügyfélről
### <a name="excel-activation"></a>Az Excel-aktiválás
A termelési számítási feladatokhoz a ComputeNodeWithExcel Virtuálisgép-lemezkép használatakor meg kell adnia egy érvényes Microsoft Office licenckulcs aktiválásához Excel a számítási csomópontokon. Ellenkező esetben Excel próbaverzióját 30 nap után lejár, és a COMException (0x800AC472) rendelkező Excel-munkafüzetek futó sikertelen lesz. 

Kiértékelés időpontja egy másik 30 napig is állíthatnak alaphelyzetbe Excel: Jelentkezzen be a fő csomópontot és clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` összes Excel számítási csomópontok HPC Cluster Manager keresztül. Legfeljebb kétszer is aktiválási adatok alaphelyzetbe állításának. Ezt követően meg kell adnia egy érvényes Office licenckulcsot.

Az Office Professional Plus 2013 a Virtuálisgép-lemezkép telepítve az általános mennyiségi licenc kulcsot (GVLK) mennyiségi kiadását. Kulcskezelő szolgáltatás (KMS) használatával is aktiválhatja vagy Active Directory-alapú aktiválás (AD-BA) vagy a többször használható aktiválási kulcs (MAK). 

    * Használja a KMS/AD-BA, meglévő KMS-kiszolgálót használ, vagy hozzon létre egy újat a Microsoft Office 2013 mennyiségi licenc-csomag használatával. (Ha szeretne, állítsa be a kiszolgálót, az átjárócsomópont.) Ezután aktiválja a KMS-állomás kulcsát az interneten vagy telefonon keresztül. Ezután clusrun `ospp.vbs` adja meg a KMS-kiszolgáló és a portot és az összes Office aktiválása az Excel számítási csomópontokon. 

    * MAK-ot, első clusrun használandó `ospp.vbs` adja meg a kulcsot, és ezután aktiválja az összes az Excel számítási csomópontokon az interneten vagy telefonon keresztül. 

> [!NOTE]
> Kiskereskedelmi termékazonosító kulcsok az Office Professional Plus 2013 a Virtuálisgép-lemezkép nem használható. Ha rendelkezik érvényes kulcsokat és a telepítési adathordozó Office vagy az Excel kiadások kívül az Office Professional Plus 2013 mennyiségi kiadását, ehelyett használhatók. Először távolítsa el a mennyiségi kiadását, és telepítse a kiadása, amely rendelkezik. Az újratelepített Excel számítási csomópont testre szabott Virtuálisgép-lemezkép a nagy mennyiségű központi telepítés használata, rögzíthetők.
> 
> 

### <a name="offload-excel-workbooks"></a>Excel-munkafüzetek kiürítési
Kövesse az alábbi lépéseket egy Excel-munkafüzet kiszervezzük, hogy fusson a HPC Pack-fürtön az Azure-ban. Ehhez az Excel 2010 vagy 2013 már telepítve van az ügyfélszámítógépen kell rendelkeznie.

1. Az Excel a HPC Pack-fürt üzembe helyezése 1. lépésben a lehetőségek egyikét használja a számítási csomópont lemezképével. Szerezze be a fürt tanúsítványfájlt (.cer) és a fürt felhasználónevet és jelszót.
1. Az ügyfélszámítógépen a Cert: \CurrentUser\Root fürt tanúsítványt importálni.
1. Ellenőrizze, hogy telepítve van az Excelben. Hozzon létre egy Excel.exe.config fájl ugyanabban a mappában, Excel.exe az ügyfélszámítógépen a következő tartalmakat. Ez a lépés biztosítja, hogy a HPC Pack 2012 R2 Excel COM-beépülő modul sikeresen betöltődik.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
1. Állítsa be az ügyfél küldhetők be feladatok a HPC Pack-fürthöz. Az egyik lehetőség, hogy töltse le a teljes [HPC Pack 2012 R2 Update 3 telepítési](https://www.microsoft.com/download/details.aspx?id=49922) és a HPC Pack-ügyfél telepítéséhez. Másik lehetőségként töltse le és telepítse a [HPC Pack 2012 R2 Update 3 ügyfél segédprogramok](https://www.microsoft.com/download/details.aspx?id=49923) és a megfelelő Visual C++ 2010 újraterjeszthető csomag a számítógép ([x64](https://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555) ).
1. Ebben a példában egy Excel-munkafüzetmintát ConvertiblePricing_Complete.xlsb nevű használjuk. Letöltheti a [Itt](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
1. Másolja az Excel-munkafüzet D:\Excel\Run például a munkamappában.
1. Nyissa meg az Excel-munkafüzet. Az a **Develop** menüszalagra, majd **COM-bővítmények** , és győződjön meg arról, hogy a HPC Pack Excel COM-az adatok sikeresen betöltve.
   
   ![Excel-bővítmény a HPC Pack][addin]
1. A VBA makró az Excelben HPCControlMacros szerkesztése megjegyzésekkel sorok módosításával, ahogyan az az alábbi parancsfájlt. A környezet megfelelő értékekkel helyettesítse.
   
   ![A HPC Pack Excel-makrók][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
1. Másolja ki az Excel-munkafüzet D:\Excel\Upload például feltöltés könyvtárba. Ez a könyvtár a VBA makró a HPC_DependsFiles állandó van megadva.
1. A munkafüzet futtatunk a fürtön az Azure-ban, kattintson a **fürt** gomb a munkalapon.

### <a name="run-excel-udfs"></a>Excel univerzális Lemezformátumokat futtatása
Excel univerzális Lemezformátumokat futtatásához kövesse az előző lépések az ügyfélszámítógép beállítása 1 – 3. Excel univerzális Lemezformátumokat nem kell rendelkeznie az Excel-alkalmazás telepítése a számítási csomópontok. Tehát a fürt létrehozása számítási csomópontokra, amikor dönthet a normál számítási csomópont lemezképével helyett az Excelbe, a számítási csomópont lemezképével.

> [!NOTE]
> Nincs olyan 34 karakteres korlátot, az Excel 2010 és 2013 fürt összekötő párbeszédpanel. Ez a párbeszédpanel segítségével adja meg a fürtöt, amely a felhasználói függvényeket. Ha a teljes fürt neve hosszabb (például hpcexcelhn01.southeastasia.cloudapp.azure.com), nem fér el a párbeszédpanel bezárásához. A megoldás az, hogy egy olyan gépre kiterjedő változó megadásához például *CCP_IAASHN* a hosszú fürt nevének értékét. Ezután írja be a *CCP_IAASHN %* neveként a fürt fő csomópontjának párbeszédpanel. 
> 
> 

A fürt sikeres telepítése után folytassa a következő lépéseket egy beépített minta futtatásához az Excel UDF. Tekintse át ezeket a testre szabott Excel univerzális Lemezformátumokat [erőforrások](https://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) a XLL-EK készíthet és helyezhet üzembe a őket az IaaS-fürtön.

1. Nyisson meg egy új Excel-munkafüzet. Az a **Develop** menüszalagra, majd **bővítmények**. Majd kattintson a párbeszédpanelen **Tallózás**, keresse meg a %CCP_HOME%Bin\XLL32 mappát, és válassza ki a minta ClusterUDF32.xll. Ha a ClusterUDF32 nem létezik az ügyfélszámítógépen, másolja a fő csomópontot %CCP_HOME%Bin\XLL32 mappájából.
   
   ![Válassza ki az UDF-ben][udf]
1. Kattintson a **fájl** > **beállítások** > **speciális**. A **képletek**, ellenőrizze **engedélyezése a felhasználó által definiált XLL függvények futtatása egy számítási fürt**. Kattintson a **beállítások** , és adja meg a teljes fürt nevét a **fürt fő csomópontjának neve**. (Amint korábban a beviteli mezőt korlátozódik 34 karakter hosszúságú lehet, ezért előfordulhat, hogy nem férnek el a hosszú fürt nevét. Használhat egy gépre kiterjedő változó egy hosszú fürt nevéhez.)
   
   ![Az UDF konfigurálása][options]
1. Az UDF számítási fürtön futnak, kattintson a cellára, amely érték =XllGetComputerNameC(), és nyomja le az Enter billentyűt. A függvényt egyszerűen lekéri a számítási csomóponton, amelyen fut az UDF nevét. Az első futtatáskor a hitelesítő adatok párbeszédpanel kérni fogja a felhasználónevet és jelszót az IaaS-fürthöz való csatlakozáshoz.
   
   ![UDF futtatása][run]
   
   Ha sok cellák kiszámításához, nyomja le az Alt-Shift-Ctrl + F9 a számítás futtathatók az összes cellát.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>3. lépés A SOA típusú számítási feladatok futtatásához egy helyszíni ügyfélről
Általános SOA-alkalmazások futtatása a HPC Pack IaaS-fürtön, először használja a módszerek közül az 1. lépésben a fürt üzembe helyezéséhez. Adja meg, egy általános számítási csomópont lemezképével ebben az esetben, mert az Excel nem szükséges a számítási csomópontokon. Ezután kövesse az alábbi lépéseket.

1. A fürttanúsítvány beolvasása, után importálja a Cert: \CurrentUser\Root az ügyfélszámítógépen.
1. Telepítse a [HPC Pack 2012 R2 Update 3 SDK](https://www.microsoft.com/download/details.aspx?id=49921) és [HPC Pack 2012 R2 Update 3 ügyfél segédprogramok](https://www.microsoft.com/download/details.aspx?id=49923). Ezek az eszközök lehetővé teszik a SOA típusú ügyfél alkalmazások fejlesztése és futtatása.
1. Töltse le a HelloWorldR2 [mintakód](https://www.microsoft.com/download/details.aspx?id=41633). Nyissa meg a HelloWorldR2.sln a Visual Studio 2010- vagy 2012. (Ez a minta nem áll jelenleg kompatibilis a Visual Studio legújabb verziói.)
1. Először hozza létre a EchoService projektet. Ezt követően a szolgáltatás üzembe helyezése az IaaS-fürt központi telepítése a helyi fürthöz ugyanolyan módon. A részletes lépéseket lásd: a Readme.doc HelloWordR2 a. Módosíthatja, és az SOA-ügyfélalkalmazások az Azure IaaS-fürtön futó létrehozni a HellWorldR2 és más projektek, a következő szakaszban leírtak szerint hozhat létre.

### <a name="use-http-binding-with-azure-storage-queue"></a>Http-kötést használja az Azure storage-üzenetsorba
Az Azure storage üzenetsorába Http-kötést használja, győződjön meg arról, néhány változás történt a mintakódhoz.

* Frissítse a fürt nevét.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Szükség esetén használja az alapértelmezett TransportScheme SessionStartInfo, vagy explicit módon állítsa be a Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* A BrokerClient alapértelmezett kötés használja.
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    Vagy állítsa be a basicHttpBinding használatát.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Szükség esetén a UseAzureQueue jelzőt igaz értékre van állítva a SessionStartInfo. Ha nincs beállítva, akkor állítja be igaz, amikor a fürt nevét az Azure tartományi utótagokat és a TransportScheme Http alapértelmezés szerint.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Használja a Http-kötést az Azure storage-üzenetsor nélkül
Szeretné használni a Http-kötést az Azure storage üzenetsorába nélkül, explicit módon a UseAzureQueue jelzőt "false" értékűre a SessionStartInfo a.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>NetTcp kötés használja.
NetTcp kötés használatához a konfiguráció hasonlít a csatlakozás a helyi fürthöz. Meg kell nyitnia a fő csomópont Virtuálisgép-pár végpontok. Ha a fürt létrehozásához használt a HPC Pack IaaS telepítési szkripttel, például állítsa be a végpontok az Azure Portalon a következő.

1. A virtuális gép leállítása.
1. Adja hozzá a TCP-portok 9090, 9087, 9091, a munkamenet 9094 közvetítse, feldolgozói és adatszolgáltatások, közvetítő jelölik
   
    ![Végpontok konfigurálása][endpoint-new-portal]
1. Indítsa el a virtuális gépet.

A SOA típusú ügyfélalkalmazás nem kell módosítani az IaaS-fürt teljes nevét a fő nevének módosítása kivételével.

## <a name="next-steps"></a>További lépések
* Lásd: [ezeket az erőforrásokat](https://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) Excel számítási feladatok futtatása HPC packkel további információt.
* Lásd: [SOA-szolgáltatások kezelése a Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) további információt a üzembe helyezése és kezelése a SOA típusú szolgáltatások a HPC Pack segítségével.

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
