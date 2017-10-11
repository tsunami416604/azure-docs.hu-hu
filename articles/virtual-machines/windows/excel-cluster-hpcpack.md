---
title: "Az Excel és SOA HPC Pack fürt |} Microsoft Docs"
description: "Ismerkedés a nagyméretű Excel és a SOA munkaterhelések egy HPC Pack fürtben futó Azure-ban"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 63babd94fdab15217cfb0757e4cd6efe458a628d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Ismerkedés az Excel és a SOA munkaterhelések egy HPC Pack fürtben futó Azure-ban
Ez a cikk bemutatja, hogyan Azure virtuális gépeken futó Microsoft HPC Pack 2012 R2 fürt központi telepítése egy Azure gyors üzembe helyezés sablont, vagy opcionálisan egy Azure PowerShell telepítési parancsfájlt. A fürt úgy tervezték, hogy a Microsoft Excel alkalmazást vagy szolgáltatásorientált architektúra (SOA) munkaterhelések futtatása HPC Pack Azure piactér Virtuálisgép-rendszerképekről használja. A fürt használhatja a helyszíni ügyfél-számítógépről Excel HPC és SOA szolgáltatások futtatásához. Excel-munkafüzet kiürítéséhez és a felhasználó által definiált függvények Excel vagy a felhasználó által megadott függvények közé tartoznak az Excelhez készült HPC-szolgáltatások

> [!IMPORTANT] 
> Ez a cikk a HPC Pack 2012 R2 alapul funkciók, sablonok és parancsfájlok. Ebben a forgatókönyvben jelenleg nem támogatott a HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Magas szinten a következő ábrán a HPC Pack fürtöt hoz létre.

![Excel munkaterheket futtatnak csomópontok HPC-fürt][scenario]

## <a name="prerequisites"></a>Előfeltételek
* **Ügyfélszámítógép** -elküldeni az Excel és SOA mintafeladatok a fürthöz ügyfél Windows-alapú számítógépre van szüksége. Szükség (ha az adott központi telepítési módszer választása) az Azure PowerShell-fürt üzembe helyezési parancsfájl futtatásához egy Windows-számítógép.
* **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány percig.
* **Magok kvóta** -előfordulhat, hogy növelnie kell a beállított kvótát mag, különösen akkor, ha több fürtcsomóponton az multicore Virtuálisgép-méretek telepít. Egy Azure gyors üzembe helyezés sablont használ, ha a magok kvótája a Resource Manager egy Azure-régióban van. Ebben az esetben szükség lehet egy adott régióban a kvóta növeléséhez. Lásd: [Azure-előfizetésre vonatkozó korlátok, kvóták és megkötések](../../azure-subscription-service-limits.md). A kvóta növeléséhez [nyissa meg az online támogatás ügyfélkérés](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) díjmentesen.
* **A Microsoft Office-licencet** – Ha a számítási csomópontok piactér HPC Pack 2012 R2 Virtuálisgép-lemezkép használatával a Microsoft Excel, a 30 napos próbaverzióját Microsoft Excel Professional Plus 2013 telepítve van. A próbaidőszak után meg kell adnia a Microsoft Office Excel továbbra is alkalmazásokat és szolgáltatásokat futtathatnak aktiválásához érvényes licenc. Lásd: [Excel-aktiválási](#excel-activation) című cikkben. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>1. lépés Az Azure-ban egy HPC Pack fürt beállítása
Megmutatjuk, két lehetőség közül választhat a HPC Pack 2012 R2-fürt beállításához: első, egy Azure gyors üzembe helyezés sablont és az Azure-portálon; és a második, az Azure PowerShell telepítési parancsfájlt használ.

### <a name="option-1-use-a-quickstart-template"></a>1. lehetőség. A következő gyorsindítási sablonon használata
Egy Azure gyors üzembe helyezés sablon használatával gyorsan HPC Pack-fürt üzembe helyezése az Azure portálon. A sablon a portálon megnyitásakor egy egyszerű felhasználói felület, ahol a beállítások megadása a fürt kap. A lépések a következők. 

> [!TIP]
> Ha azt szeretné, egy [Azure piactér sablon](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) , amely kifejezetten a Excel munkaterhelések hasonló fürt hoz létre. A lépések némileg eltér a következő.
> 
> 

1. Látogasson el a [sablonlap HPC-fürt létrehozása a Githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Ha azt szeretné, tekintse át a sablon és a forráskódot.
2. Kattintson a **az Azure telepítéséhez** a központi telepítés elindítása a sablon az Azure portálon.
   
   ![Az Azure-sablon üzembe helyezése][github]
3. A portál lépések végrehajtásával adja meg a paramétereket a HPC-fürt sablon.
   
   a. Az a **paraméterek** lapon adja meg, vagy módosítsa a sablon paraméter értékét. (Kattintson a beállítások mellett lévő ikonra súgójában talál.) A következő képernyő mintaértékek láthatók. Ez a példa egy nevű fürtöt hoz létre *hpc01* a a *hpc.local* tartomány egy átjárócsomóponttal és 2 álló számítási csomópontokat. A számítási csomópontok HPC Pack VM-lemezkép, amely tartalmazza a Microsoft Excel készített.
   
   ![Adja meg a paraméterek][parameters-new-portal]
   
   > [!NOTE]
   > A virtuális gép alapján automatikusan létrehozott átjárócsomópont a [legújabb Piactéri lemezképhez](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) HPC Pack 2012 R2 Windows Server 2012 R2 rendszeren. Jelenleg a kép HPC Pack 2012 R2 Update 3 alapul.
   > 
   > A kijelölt számítási csomópont termékcsalád a legújabb lemezképből számítási csomópont virtuális gépek jönnek létre. Válassza ki a **ComputeNodeWithExcel** beállítás megadása a legújabb HPC Pack számítási csomópont kép, amely tartalmazza a Microsoft Excel Professional Plus 2013 próbaverzióját. Általános SOA munkamenetek vagy Excel UDF kiszervezésével fürt központi telepítése, válassza ki a **Átjárócsomópontján** (nélkül telepített Excel) lehetőséget.
   > 
   > 
   
   b. Válassza ki az előfizetést.
   
   c. Hozzon létre például egy erőforráscsoportot a fürt *hpc01RG*.
   
   d. Válassza ki az erőforráscsoportot, például az USA középső RÉGIÓJA helyét.
   
   e. Az a **jogi feltételeket** lapon, olvassa el a feltételeket. Ha elfogadja, kattintson a **beszerzési**. Ha elkészült, a beállítás a sablon értéke, kattintson a **létrehozása**.
4. A telepítés befejezésekor (általában tart körülbelül 30 percig), a fürt tanúsítványfájl exportálása a fürt átjárócsomópontjából. Egy későbbi lépésben importálni a nyilvános tanúsítvány biztonságos HTTP-kötés a kiszolgálóoldali hitelesítése az ügyfélszámítógépen.
   
   a. Az Azure portálon, nyissa meg az irányítópultot, válassza ki az átjárócsomóponthoz, és kattintson a **Connect** csatlakozni a távoli asztal segítségével a lap tetején.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Standard eljárások segítségével a Tanúsítványkezelő az átjárócsomópont (az található a Cert: \LocalMachine\My) a titkos kulcs nélküli tanúsítvány exportálása. Ebben a példában exportálása *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![A tanúsítvány exportálása][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>2. lehetőség. A HPC Pack IaaS telepítési parancsfájl használata
A HPC Pack IaaS telepítési parancsfájl segítségével egy másik sokoldalú HPC Pack-fürt üzembe helyezése. A fürt létrehoz a klasszikus üzembe helyezési modellel, mivel a sablon az Azure Resource Manager telepítési modellt használ. A parancsfájl is kompatibilis Azure globális vagy Azure Kína szolgáltatásban előfizetés.

**További Előfeltételek**

* **Az Azure PowerShell** - [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview) (0.8.10 verzió vagy újabb) az ügyfélszámítógépen.
* **HPC Pack IaaS telepítési parancsfájl** - töltse le és csomagolja ki a parancsfájlt a legújabb verzióját a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). A verziószám a parancsfájl futtatásával `New-HPCIaaSCluster.ps1 –Version`. Ez a cikk verzió 4.5.0 vagy később, a parancsfájl alapján.

**A konfigurációs fájl létrehozása**

 A HPC Pack IaaS telepítési parancsfájl egy konfigurációs XML-fájl, amely az infrastruktúra a HPC-fürt bemenetként használja. Egy átjárócsomóponttal és 18 számítási csomópontokat, amely tartalmazza a Microsoft Excel számítási csomópont lemezkép alapján létre álló fürt központi telepítése, helyettesítse be az értékek környezetnek az alábbi minta konfigurációs fájlba. A konfigurációs fájl kapcsolatos további információkért tekintse meg a Manual.rtf a parancsfájl mappában és [HPC-fürt létrehozása a HPC Pack IaaS telepítési parancsfájl](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

* A **VMName** , az átjárócsomópont **kell** lehet azonos a **szolgáltatásnév**, vagy SOA-feladatok nem indulnak el.
* Győződjön meg arról, hogy a megadott **EnableWebPortal** így átjárócsomópontjához tanúsítvány jön létre, és exportálja.
* A fájl egy konfigurációt követő PowerShell-parancsfájlt a központi csomóponton futó PostConfig.ps1 határozza meg. Az alábbi mintaparancsfájl konfigurálása az Azure storage kapcsolati karakterlánc, a számítási csomópont szerepkör eltávolítása az átjárócsomóponthoz, és az összes csomópont online elérését, amikor központilag telepítették őket. 

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

1. Nyissa meg rendszergazdaként a PowerShell-konzolon, az ügyfélszámítógépen.
2. Módosítsa a könyvtárat a parancsfájl mappába (E:\IaaSClusterScript ebben a példában).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. A következő parancsot a HPC Pack fürt telepítéséhez. Ez a példa feltételezi, hogy a konfigurációs fájlban található E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

A HPC Pack telepítési parancsfájlt futtatja egy kis ideig. A parancsprogram egyetlen művelet exportálni, és töltse le a fürt tanúsítványt, és mentse az ügyfélszámítógépen az aktuális felhasználó Dokumentumok mappájának-hoz. A parancsfájl egy a következőhöz hasonló üzenetet hoz létre. Egy következő lépésben importálja a tanúsítványt a megfelelő tanúsítványtárolójában.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>2. lépés Excel-munkafüzetek kiszervezése és a felhasználó által megadott függvények futtatása egy helyszíni ügyfélről
### <a name="excel-activation"></a>Excel-aktiválás
A termelési számítási feladatokhoz a ComputeNodeWithExcel Virtuálisgép-lemezkép használatakor meg kell adnia egy érvényes Microsoft Office licenckulcs aktiválásához Excel a számítási csomóponton. Ellenkező esetben Excel próbaverzióját 30 nap múlva lejár, és a COMException (0x800AC472) Excel-munkafüzetek futtatása meghiúsul. 

Az értékelés időpontjához további 30 napra is állíthatnak alaphelyzetbe Excel: Jelentkezzen be az átjárócsomópont és clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` összes Excel a számítási csomópontok HPC Cluster Manager keresztül. Legfeljebb kétszer is újra. Ezt követően meg kell adnia egy érvényes Office licenckulcs.

Az Office Professional Plus 2013 a Virtuálisgép-lemezkép telepítve az általános mennyiségi licenc kulcsot (GVLK) mennyiségi kiadását. Kulcskezelő szolgáltatás (KMS) keresztül is aktiválhatja vagy Active Directory-alapú aktiválás (AD-BA), vagy a többször használható aktiválási kulcs (MAK). 

    * Használja a KMS/AD-BA, meglévő KMS-kiszolgáló használata, vagy állítson be egy új Microsoft Office 2013 mennyiségi licenc csomag használatával. (Ha kívánja, a kiszolgáló beállítása a head csomóponton.) Ezután aktiválja a KMS-állomás kulcsát az interneten vagy telefonon keresztül. Majd clusrun `ospp.vbs` a KMS-kiszolgáló és a port és az összes Office aktiválása az Excel számítási csomópontjain. 

    * MAK-ot, első clusrun használandó `ospp.vbs` adja meg a kulcs, és az összes majd aktiválja az Excel számítási csomópontok az interneten vagy telefonon keresztül. 

> [!NOTE]
> Ez a Virtuálisgép-lemezkép nem használható Office Professional Plus 2013 kereskedelmi termékkulcsokat. Ha van érvényes kulcsok és a telepítési adathordozó Office vagy az Excel kiadás kivételével az Office Professional Plus 2013 mennyiségi kiadását, használhatja őket helyette. Először távolítsa el a mennyiségi kiadását, és telepítse a verziót, hogy rendelkezik. Az újratelepített Excel számítási csomópont virtuális gép testreszabott egy telepítéseihez léptékű képként rögzíthetők.
> 
> 

### <a name="offload-excel-workbooks"></a>Excel-munkafüzetek kiszervezése
Kövesse az alábbi lépéseket, hogy az Azure-ban a HPC Pack fürtön fut egy Excel-munkafüzet-kiszervezés. Ehhez az szükséges, az Excel 2010 vagy 2013 már telepítve van az ügyfélszámítógép kell rendelkeznie.

1. Használja az 1. lépésben lehetőségek HPC Pack-fürt üzembe helyezése a Excel számítási csomópont kép. Szerezze be a fürt tanúsítványfájlt (.cer) és a fürt felhasználónevet és jelszót.
2. Az ügyfélszámítógépen a fürt tanúsítványt a Cert: \CurrentUser\Root importálni.
3. Győződjön meg arról, hogy telepítve van a Excel. Hozzon létre egy Excel.exe.config fájlt az ügyfélszámítógépen Excel.exe megegyező mappában található a következő tartalommal. Ez a lépés biztosítja, hogy a HPC Pack 2012 R2 Excel COM beépülő modul sikeresen betöltődik.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Állítsa be az ügyfél a HPC Pack fürthöz feladatok küldéséhez. Egy elem letölteni a teljes [HPC Pack 2012 R2 Update 3 telepítési](http://www.microsoft.com/download/details.aspx?id=49922) és a HPC Pack ügyfél telepítéséhez. Azt is megteheti, töltse le és telepítse a [HPC Pack 2012 R2 Update 3 ügyfél segédprogramok](https://www.microsoft.com/download/details.aspx?id=49923) és a megfelelő Visual C++ 2010 terjeszthető változatának a számítógép ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555) ).
5. A jelen példában használjuk ConvertiblePricing_Complete.xlsb nevű minta Excel-munkafüzet. Letöltheti a [Itt](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. A munkamappa D:\Excel\Run például az Excel-munkafüzet másolja.
7. Nyissa meg az Excel-munkafüzet. A a **Develop** menüszalag, kattintson a **COM-bővítmények** és ellenőrizze, hogy a HPC Pack Excel COM-bővítmény sikeresen megtörtént.
   
   ![Excel-bővítmény HPC Pack][addin]
8. A VBA makró Excel HPCControlMacros szerkesztése a megjegyzésként sorok módosításával, ahogy az az alábbi parancsfájlt. Helyettesítse be a környezetének megfelelő értékeket.
   
   ![HPC Pack Excel makró][macro]
   
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
9. Másolja az Excel-munkafüzet egy feltöltési címtár, például D:\Excel\Upload. Ez a könyvtár a HPC_DependsFiles állandó a VBA makróban van megadva.
10. A munkafüzet futtatásához a fürtön, az Azure-ban kattintson a **fürt** gomb a munkalapon.

### <a name="run-excel-udfs"></a>Excel univerzális Lemezformátumokat futtatása
Excel univerzális Lemezformátumokat futtatásához kövesse az előző lépések 1 – 3 állíthatja be az ügyfélszámítógépen. Excel univerzális Lemezformátumokat nincs szükség van a számítási csomópontok telepítve Excel-alkalmazás. Ezért amikor a fürt létrehozása számítási csomópontokat, megadhatja, normál számítási csomópont kép helyett a számítási csomópont rendszerképének Excel.

> [!NOTE]
> Létrejön egy 34 karakteres korlátot, az Excel 2010 és 2013 fürt összekötő párbeszédpanel. Ez a párbeszédpanel segítségével adja meg a fürtöt, amely a felhasználó által megadott függvények futtatja. Ha a teljes fürt neve hosszabb (például hpcexcelhn01.southeastasia.cloudapp.azure.com), nem fér el a párbeszédpanel bezárásához. A megoldás is, hogy a gépre kiterjedő változó például *CCP_IAASHN* a hosszú fürtnév értékét. Ezután írja be a *CCP_IAASHN %* neveként a fürt átjárócsomópontjához párbeszédpanel. 
> 
> 

A fürt sikeres telepítése után folytassa a következő lépéseket egy beépített minta futtatásához Excel UDF. A testre szabott Excel univerzális Lemezformátumokat tapasztalja [erőforrások](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) a XLL-EK építsenek, és telepítheti őket az IaaS-fürtön.

1. Nyisson meg egy új Excel-munkafüzet. Az a **Develop** menüszalag, kattintson a **bővítmények**. Majd kattintson a párbeszédpanelen **Tallózás**, keresse meg a %CCP_HOME%Bin\XLL32 mappát, és válassza ki a minta ClusterUDF32.xll. Ha a ClusterUDF32 nem létezik az ügyfélszámítógépen, másolja az átjárócsomópont %CCP_HOME%Bin\XLL32 mappájából.
   
   ![Válassza ki az UDF-ben][udf]
2. Kattintson a **fájl** > **beállítások** > **speciális**. A **képletek**, ellenőrizze **engedélyezése a felhasználó által definiált XLL függvények futtatásához a számítási fürt**. Kattintson a **beállítások** és adja meg a teljes fürt nevét a **fürt átjárócsomópontjához neve**. (Részben ismertetett beállításértékeket korábban a beviteli mezőbe korlátozódik 34 karakter hosszúságú lehet, ezért előfordulhat, hogy nem felelnek meg egy hosszú neve. Használhatja a gépre kiterjedő változó nevét egy hosszú fürtnév.)
   
   ![Az UDF konfigurálása][options]
3. Az UDF számítási futtatása a fürtön, kattintson a cellára, amelynek értéke =XllGetComputerNameC(), és nyomja le az ENTER billentyűt. A függvény egyszerűen lekéri a számítási csomópont, amelyen fut az UDF nevét. Az első alkalommal történő futtatásakor a hitelesítő adatok párbeszédpanel megadását kéri a felhasználónevet és jelszót csatlakozni az IaaS-fürthöz.
   
   ![Futtassa az UDF-ben][run]
   
   Ha sok cellát kiszámításához, nyomja le az Alt-Shift-Ctrl + F9 a számítás futniuk valamennyi cellájában.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>3. lépés A SOA munkaterhelések futtatásához egy helyszíni ügyfélről
Általános SOA-alkalmazások futtatása a HPC Pack IaaS fürtön, először a módszerek valamelyikével 1. lépésben a fürt telepítéséhez. Adja meg, általános számítási csomópont kép ebben az esetben, mert az Excel nem szükséges a számítási csomópontok. Ezután kövesse az alábbi lépéseket.

1. A fürt tanúsítvány beolvasása, után importálja a Cert: \CurrentUser\Root az ügyfélszámítógépen.
2. Telepítse a [HPC Pack 2012 R2 Update 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) és [HPC Pack 2012 R2 Update 3 ügyfél segédprogramok](https://www.microsoft.com/download/details.aspx?id=49923). Ezek az eszközök lehetővé teszik fejlesztéséhez és SOA ügyfélalkalmazások futtatását.
3. Töltse le a HelloWorldR2 [példakód](https://www.microsoft.com/download/details.aspx?id=41633). Nyissa meg a HelloWorldR2.sln a Visual Studio 2010 vagy a 2012. (Ez a minta nincs jelenleg kompatibilis a Visual Studio legújabb verziói.)
4. Először hozza létre a EchoService projekt. Ugyanúgy telepít a helyi fürthöz, majd telepítse a szolgáltatást az IaaS-fürt. Részletes útmutató: a HelloWordR2 Readme.doc. Módosítsa, majd létre a HellWorldR2 és más projektek a következő szakaszban leírtak a SOA ügyfél Azure IaaS fürtökön futó alkalmazások létrehozásához.

### <a name="use-http-binding-with-azure-storage-queue"></a>Http-kötés használata az Azure storage üzenetsorába
Egy Azure storage üzenetsorába használandó Http-kötés, módosításokat néhány példakód.

* Frissítse a fürt nevét.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Szükség esetén használja az alapértelmezett TransportScheme SessionStartInfo, vagy explicit módon állítsa be azt a Http.

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
  
    Vagy állítsa be explicit módon használja a basicHttpBinding.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Beállíthatja a UseAzureQueue jelző SessionStartInfo igaz értékű. Ha nincs megadva, akkor úgy lesz beállítva, amikor a fürt neve van Azure-tartomány utótag, és a TransportScheme Http alapértelmezés szerint true.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Http-kötés nélkül az Azure storage-várólista használata
Explicit módon használja a Http-kötés nélkül az Azure storage üzenetsorába UseAzureQueue jelzőt a SessionStartInfo a FALSE értékre kell beállítani.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>NetTcp kötés használata
NetTcp kötés használatához a konfigurációs hasonlít a csatlakozás a helyi fürthöz. Nyissa meg az átjárócsomóponthoz VM néhány végpontja van szüksége. Ha a HPC Pack IaaS telepítési parancsfájlt a fürt létrehozásához használt, például a végpontok Azure-portálon az alábbiak szerint állíthatja.

1. Állítsa le a virtuális Gépet.
2. Adja hozzá a TCP-portok 9090, 9087, 9091, a munkamenet 9094 Replikaszervező, munkavégző és adatszolgáltatások, illetve Replikaszervező
   
    ![Végpontok konfigurálása][endpoint-new-portal]
3. Indítsa el a virtuális Gépet.

A SOA ügyfélalkalmazás nem kell módosítani a módosítása a központi nevét az IaaS-fürt teljes név kivételével.

## <a name="next-steps"></a>Következő lépések
* Lásd: [ezeket az erőforrásokat](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) HPC Pack Excel munkaterhelések futtatásával kapcsolatos további információt.
* Lásd: [SOA-szolgáltatások kezelése a Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) központi telepítésére és felügyeletére SOA szolgáltatások HPC Pack bővebben.

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
