---
title: "Azure DevTest Labs integrálja a VSTS folyamatos integrációt és kézbesítési folyamat |} Microsoft Docs"
description: "Útmutató: Azure DevTest Labs integrálja a VSTS folyamatos integrációt és a szállítási folyamat"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: v-craic
ms.openlocfilehash: db2ee6a25626f0a47bf86c5ee286fddc2441d3f8
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Azure DevTest Labs integrálja a VSTS folyamatos integrációt és a szállítási folyamat
Használhatja a *Azure DevTest Labs feladatok* bővítményt, amely könnyen települ a Visual Studio Team Services (VSTS) számára a CI/CD build és kiadás folyamat integrálása Azure DevTest Labs szolgáltatásban. A bővítmény telepítését három feladatok: 
* Virtuális gép létrehozása
* Egy egyéni lemezképet létrehozni egy virtuális
* Virtuális gép törlése 

A folyamat egyszerűen, például a "mesterlemezzel" egy adott teszt tevékenység gyorsan üzembe helyezhet, és törölje a vizsgálat befejezésekor.

Ez a cikk bemutatja, hogyan hozzon létre egy virtuális gép üzembe helyezése, létrehozhat egyéni rendszerképeket és törölje a virtuális gép, mint egy teljes folyamat összes. Szokásos elvégezhető minden feladat egyenként a saját egyéni build-teszttelepítés folyamat.

## <a name="before-you-begin"></a>Előkészületek
A CI/CD folyamat integrálható Azure DevTest Labs, előtt telepítenie kell a bővítményt a Visual Studio piactérről.
1. Ugrás a [Azure DevTest Labs feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Válassza ki **telepítése**.
1. A varázsló befejezéséhez.

## <a name="create-a-resource-manager-template"></a>A Resource Manager-sablon létrehozása
Ez a szakasz ismerteti, hogyan hozhat létre egy Azure virtuális gépen az igény szerinti Azure Resource Manager-sablon létrehozásához.

1. Az előfizetés egy Resource Manager-sablon létrehozásához hajtsa végre az eljárást, a [Resource Manager sablon](devtest-lab-use-resource-manager-template.md).
1. A Resource Manager-sablon elkészítése előtt adja hozzá a [WinRM összetevő](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) a virtuális gép létrehozásának részeként.

   A Rendszerfelügyeleti webszolgáltatások hozzáférési kell használnia, mint a telepítési feladatok *Azure fájlmásolással* és *a célszámítógépen PowerShell*.

   > [!NOTE]
   > WinRM megosztott IP-címek használatakor hozzá kell adnia egy NAT-szabály a Rendszerfelügyeleti webszolgáltatások port egy külső port hozzárendelését. Ez a lépés nincs szükség, ha a virtuális Gépet hoz létre egy nyilvános IP-címmel.
   >
   >

1. Menti a sablont egy fájlba a számítógépen. A fájl neve **CreateVMTemplate.json**.
1. A sablon jelentkezik be, hogy a vezérlő forrásrendszer.
1. Nyisson meg egy szövegszerkesztőt, és a következő parancsfájl illessze be azt.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. A parancsfájl jelentkezik be, hogy a vezérlő forrásrendszer. Nevezze el hasonlót **GetLabVMParams.ps1**.

   Ezt a parancsfájlt a futtatásakor az ügynök a kiadási definíciójának részeként, és ha feladatütemezési lépéseket használhatja például a *Azure fájlmásolással* vagy *a célszámítógépen PowerShell*, a parancsfájl gyűjti az értékeket, amelyeket szeretne az alkalmazás a virtuális gép üzembe helyezése. Alkalmazások telepítése egy Azure virtuális gép szokásos használja ezeket a feladatokat. A feladatokhoz szükséges értékeket, például a virtuális gép erőforráscsoport nevét, IP-cím, és teljesen minősített tartománynevét (FQDN).

## <a name="create-a-release-definition-in-release-management"></a>Egy kiadási definíció létrehozása felügyeleti kiadás
A kiadási definition létrehozásához tegye a következőket:

1. Az a **kiadásokban** lapján a **Build & kiadási** hub, kattintson a plusz jelre (+) gombra.
2. Az a **létrehozás kiadás definition** ablakban válassza ki a **üres** sablont, és válassza **következő**.
3. Válassza ki **válasszon később**, majd válassza ki **létrehozása** új kiadási-definíció létrehozása egy alapértelmezett környezet és a nem kapcsolódó összetevők.
4. A helyi menü megnyitásához, az új kiadási definition, környezet neve melletti három pont (…), majd válassza ki és **változók konfigurálását**. 
5. Az a **konfigurálása - környezet** ablakban, a változók a kiadási definition feladatokat, amelyekkel adja meg a következő értékeket:

   a. A **vmName**, adja meg, hogy a virtuális Gépet az Azure-portálon a Resource Manager-sablon létrehozásakor hozzárendelt nevét.

   b. A **felhasználónév**, megadja a felhasználónevét, amely az Azure-portálon a Resource Manager-sablon létrehozásakor az a virtuális géphez rendelt.

   c. A **jelszó**, írja be a jelszót, amely az Azure-portálon a Resource Manager-sablon létrehozásakor az a virtuális géphez rendelt. Használja a "lakat" ikont elrejtéséhez, és a jelszót biztonságos.

### <a name="create-a-vm"></a>Virtuális gép létrehozása

A központi telepítés következő szakasza, ha a virtuális gép használja a "mesterlemezzel" további központi telepítéseket. A virtuális Gépet hoz létre Azure DevTest Lab-példány belül kifejezetten erre a célra kifejlesztő a feladattal. 

1. Válassza ki a kiadási-definícióban **tevékenységek hozzáadása az**.
2. Az a **telepítés** lapon maradva adja hozzá egy *Azure DevTest Labs hozzon létre VM* feladat. Adja meg a feladat az alábbiak szerint:

   > [!NOTE]
   > A virtuális gép használata a következő központi telepítések elvégzéséhez létrehozásához lásd: [Azure DevTest Labs szolgáltatásban feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. A **Azure RM előfizetés**, válassza ki a kapcsolat a **Azure-szolgáltatás rendelkezésre álló kapcsolatok** listában, vagy hozzon létre egy szűkebb engedélyek az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager szolgáltatási végpont](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. A **tesztkörnyezet nevének**, válassza ki a korábban létrehozott példányának nevét.

   c. A **sablonnév**, adja meg a teljes elérési útja és neve a sablon fájlt a forráskódraktárban mentett. Egyszerűbbé teheti az elérési út, például a beépített tulajdonságok kiadáskezelés használhatók:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. A **Sablonparaméterek**, adja meg a paramétereket a sablonban definiált változókat. A megadott a környezetben, például a változók nevében használja:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. A **kimeneti változók - tesztlabor virtuális gép azonosítója**, az újonnan létrehozott virtuális gép Azonosítóját a következő lépéseket kell. A környezeti változó, amely automatikusan kitölti a rendszer az ilyen azonosítójú alapértelmezett neve állítható be a **kimeneti változók** szakasz. Módosítsa a változót, ha szükséges, de fontos, hogy megfelelő nevet a következő feladatok. A labor virtuális gép azonosítója íródik a következő formában:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Hajtsa végre a gyűjtése a DevTest Labs szolgáltatásban virtuális gép adatait korábban létrehozott parancsfájlt. 
4. Válassza ki a kiadási-definícióban **tevékenységek hozzáadása az** , majd a a **telepítés** lapon maradva adja hozzá egy *Azure PowerShell* feladat. Adja meg a feladat az alábbiak szerint:

   > [!NOTE]
   > Gyűjthet a DevTest Labs szolgáltatásban virtuális gép adatait, tekintse meg a [telepítés: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) és futtassa a parancsfájlt.

   a. A **Azure kapcsolattípus**, jelölje be **Azure Resource Manager**.

   b. A **Azure RM előfizetés**, alatti listából válasszon ki egy kapcsolatot **Azure-szolgáltatás rendelkezésre álló kapcsolatok**, vagy hozzon létre egy szűkebb engedélyek az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager szolgáltatási végpont](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   c. A **parancsfájl**, jelölje be **parancsfájl**.
 
   d. A **parancsfájl elérési útján**, adja meg a teljes elérési útja és a forráskódraktárban mentett parancsfájl nevét. Egyszerűbbé teheti az elérési út, például a beépített tulajdonságok kiadáskezelés használhatók:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. A **parancsfájl argumentumai**, adja meg a nevét, a környezeti változó, amely automatikusan fel – a labor virtuális gép azonosítójú az előző feladat, például: 
      ```
      -labVmId '$(labVMId)'
      ```
    A parancsfájl összegyűjti a szükséges értékeket, és tárolja a környezeti változók kiadás meghatározása, hogy Ön könnyen elérhesse azokat a későbbi lépésekben.

5. Az alkalmazás telepítése az új DevTest Labs szolgáltatásban virtuális Gépet. A feladatok általában használatával történő telepítése esetén az alkalmazás *Azure fájlmásolással* és *a célszámítógépen PowerShell*.
   A virtuális gép ezeket a feladatokat a paraméterek szükséges információ tárolódik nevű három konfigurációs változók **labVmRgName**, **labVMIpAddress**, és **labVMFqdn**kiadás meghatározása. Ha szeretné létrehozása a DevTest Labs szolgáltatásban virtuális gép és egy egyéni lemezképet, azt egy alkalmazás telepítése nélkül kísérletezhet, kihagyhatja ezt a lépést.

### <a name="create-an-image"></a>Lemezkép létrehozása

A következő szakaszban, ha az újonnan telepített virtuális gép lemezképét az Azure DevTest Labs-példányban. Másolja a virtuális gép létrehozása az igény szerinti, amikor le kívánja végrehajtani egy fejlesztői feladatot, vagy bizonyos tesztek futtatásához használhatja a lemezképet. 

1. Válassza ki a kiadási-definícióban **tevékenységek hozzáadása az**.
2. Az a **telepítés** lapon maradva adja hozzá egy **Azure DevTest Labs hozzon létre egyéni lemezkép** feladat. Konfigurálja úgy az alábbiak szerint:

   > [!NOTE]
   > A lemezkép létrehozásához lásd: [Azure DevTest Labs szolgáltatásban feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. A **Azure RM előfizetés**, a a **Azure-szolgáltatás rendelkezésre álló kapcsolatok** listán, válassza ki a kapcsolat a listából, vagy hozzon létre egy szűkebb engedélyek az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager szolgáltatási végpont](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. A **tesztkörnyezet nevének**, válassza ki a korábban létrehozott példány nevét.

   c. A **egyéni lemezkép neve**, adjon meg egy nevet az egyéni lemezképet.

   d. (Választható) A **leírás**, adjon meg egy leírást, hogy könnyebben később válassza ki a megfelelő lemezképet.

   e. A **forrás labor VM - forrás tesztlabor virtuális gép azonosítója**, ha módosította az alapértelmezett nevet, a környezeti változó, amely automatikusan ki van töltve a labor virtuális gép egy korábbi feladat azonosítója Itt szerkesztheti. Az alapértelmezett érték **$(labVMId)**.

   f. A **kimeneti változók - egyéni lemezkép-azonosító**, ha meg szeretné kezelni, vagy törölje az újonnan létrehozott kép azonosítója szüksége. Az alapértelmezett nevet, a környezeti változó, amely ezt az Azonosítót automatikusan töltődik be a **kimeneti változók** szakasz. A változó szerkesztheti, ha szükséges.

### <a name="delete-the-vm"></a>A virtuális gép törlése

A végső szakasz törlése a virtuális Gépet az Azure DevTest Labs-példányt központilag telepített. Így rendszerint törölni a virtuális gép, amelyekre szüksége van a tesztek futtatása a központilag telepített virtuális Gépen vagy a fejlesztői feladatok végrehajtása után. 

1. Válassza ki a kiadási-definícióban **tevékenységek hozzáadása az** , majd a a **telepítés** lapon maradva adja hozzá egy *Azure DevTest Labs törlése VM* feladat. Konfigurálja úgy az alábbiak szerint:

      > [!NOTE]
      > A virtuális gép törlése, lásd: [Azure DevTest Labs feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. A **Azure RM előfizetés**, válassza ki a kapcsolat a **Azure-szolgáltatás rendelkezésre álló kapcsolatok** listában, vagy hozzon létre egy szűkebb engedélyek az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager szolgáltatási végpont](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
 
   b. A **tesztlabor virtuális gép azonosítója**, ha módosította az alapértelmezett nevet, a környezeti változó, amely automatikusan ki van töltve a labor virtuális gép egy korábbi feladat azonosítója Itt szerkesztheti. Az alapértelmezett érték **$(labVMId)**.

2. Adjon meg egy nevet a kiadási definíciójához, és mentse azt.
3. Hozzon létre egy új kiadását, válassza ki a legújabb buildjével és telepíteni kell a egyetlen környezet definíciójában.

Minden egyes szakaszában a nézet az Azure portálon megtekintheti a virtuális gép és a lemezkép létrehozása, valamint a virtuális Gépet, amely újra törlődik a DevTest Labs példány frissítéséhez.

Most már használhatja az egyéni rendszerképet virtuális gépek létrehozására, ha azok szükséges.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [virtuális Gépre kiterjedő környezetek létrehozása a Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md).
* További gyors üzembe helyezés Resource Manager-sablonok a DevTest Labs automatizálásra megismerkedhet a [nyilvános DevTest Labs GitHub-tárház](https://github.com/Azure/azure-quickstart-templates).
* Ha szükséges, tekintse meg a [VSTS hibaelhárítási](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) lap.
 
