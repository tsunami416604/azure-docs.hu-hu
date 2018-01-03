---
title: "Azure DevTest Labs integrálja a VSTS folyamatos integrációt és kézbesítési folyamat |} Microsoft Docs"
description: "Útmutató: Azure DevTest Labs integrálja a VSTS folyamatos integrációt és a szállítási folyamat"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: tarcher
ms.openlocfilehash: 3cd6939e890f59e7c5b188edd8620da5c9fcc935
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Azure DevTest Labs integrálja a VSTS folyamatos integrációt és a szállítási folyamat
Használhatja a **Azure DevTest Labs feladatok bővítmény** telepítve a Visual Studio Team Services (VSTS) segítségével könnyen integrálható a CI/CD build, és felszabadíthatja a folyamatot az Azure DevTest Labs szolgáltatásban. A bővítmény telepítését három feladatokat hozzon létre egy virtuális Gépet, egy egyéni lemezképet létrehozni egy virtuális és egy virtuális gép törlése. Ez a folyamat egyszerűen, például gyorsan központi telepítése a "mesterlemezzel" bizonyos teszt feladat, majd törölje a vizsgálat befejezésekor.

Ez a cikk bemutatja, hogyan hozzon létre egy virtuális gép üzembe helyezése, létrehozhat egyéni rendszerképeket, majd törölje a virtuális Gépet, az összes, egy teljes folyamat. Általában azonban használhatja a feladatok egyenként a saját egyéni build-teszttelepítés folyamat.

## <a name="before-you-begin"></a>Előkészületek
Mielőtt Azure DevTest Labs szolgáltatásban a CI/CD folyamat integrálható, először telepítenie kell a bővítményt a Visual Studio piactérről.
1. Ugrás a [Azure DevTest Labs feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
1. Válassza ki a telepítést
1. A varázsló befejezése

## <a name="create-a-resource-manager-template"></a>A Resource Manager-sablon létrehozása
Ez a szakasz ismerteti, hogyan hozhat létre egy Azure virtuális gép az igény szerinti Azure Resource Manager-sablon létrehozásához.

1. Kövesse a [Resource Manager sablon](devtest-lab-use-resource-manager-template.md) Resource Manager-sablon létrehozása az előfizetésben.
1. Adja hozzá a [WinRM összetevő](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) (a Resource Manager-sablon létrehozása) előtt a virtuális gép létrehozásának részeként.

   A Rendszerfelügyeleti webszolgáltatások hozzáférési szükség használja, mint telepítése feladatok **Azure fájlmásolással** és **a célszámítógépen PowerShell**.

   > [!NOTE]
   > WinRM megosztott IP-címek használatakor hozzá kell adnia egy NAT-szabály a Rendszerfelügyeleti webszolgáltatások port egy külső port hozzárendelését. Ez pedig nem szükséges a virtuális gép létrehozása egy nyilvános IP-címmel.
   >
   >

1. Menti a sablont egy fájlba a számítógépen. A fájl neve **CreateVMTemplate.json**.
1. Ellenőrizze a sablont a forrás vezérlő rendszerbe.
1. Nyisson meg egy szövegszerkesztőt, és másolja a következő bele.

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

1. Ellenőrizze a parancsfájl a forrás vezérlő rendszerbe. Nevezze el hasonló **GetLabVMParams.ps1**.

   Ezt a parancsfájlt, amikor az ügynökön a kiadási definíciójának részeként gyűjti az értékeket, amelyeket az alkalmazás telepítése feladatütemezési lépéseket használhatja, mint ha a virtuális gép kell **Azure fájlmásolással** vagy **a célszámítógépen PowerShell**. Alkalmazások telepítéséhez egy Azure virtuális gépre általában használja ezeket a feladatokat, és az értékeket, például a virtuális gép erőforráscsoport nevét, IP-cím, és teljesen minősített tartománynevét (FQDN).

## <a name="create-the-release-definition-in-release-management"></a>A kiadási definíció létrehozása felügyeleti kiadás
Hajtsa végre ezeket a lépéseket, a kiadás definition létrehozásához.

1. Nyissa meg a **kiadásokban** lapján a **Build & kiadási** hub, és válassza a "**+**" ikonra.
1. A a **létrehozás kiadás definition** párbeszédpanelen válassza a **üres** sablont, és válassza ki **tovább**.
1. A következő oldalon válassza ki a **válasszon később** majd **létrehozása** új kiadási-definíció létrehozása egy alapértelmezett környezet és a nem kapcsolódó összetevők.
1. Az új kiadási definícióját, válassza a folytatást jelző pontokra (...) a helyi menü megnyitásához, és válassza ki a környezet neve melletti **változók konfigurálását**. 
1. Az a **konfigurálása - környezet** párbeszédpanelen adja meg az alábbi értékeket a változók a kiadási definition feladatok használata:
   - **vmName**: Adja meg a virtuális Gépet az Azure-portálon a Resource Manager-sablon létrehozásakor hozzárendelt nevét.
   - **Felhasználónév**: Adja meg a felhasználónevet, a virtuális Gépet az Azure-portálon a Resource Manager-sablon létrehozásakor hozzárendelt.
   - **jelszó**: Adja meg a jelszót, amelyet a virtuális gép az Azure-portálon a Resource Manager-sablon létrehozása után. Használja a "lakat" ikont elrejtéséhez, és a jelszót biztonságos.

   <a name="create-a-vm"></a>Virtuális gép létrehozása
   ---

   Ebben a telepítésben az első fázist, ha a virtuális gép használja a "mesterlemezzel" további központi telepítéseket. A virtuális Gépen belül kifejezetten erre a célra létrehozott feladat Azure DevTest Lab-példány létrehozásakor. Válassza ki a kiadási-definícióban **+ Hozzáadás feladatok** , és adja hozzá egy **Azure DevTest Labs hozzon létre virtuális** a telepítés lapon a feladatot. Konfigurálja úgy az alábbiak szerint:

   Lásd: [Azure DevTest Labs feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) az ezt követő központi telepítéséhez használandó virtuális gép létrehozása.
   - **Az Azure erőforrás-kezelő előfizetési**: alatti listából válasszon ki egy kapcsolatot **Azure-szolgáltatás rendelkezésre álló kapcsolatok** vagy hozzon létre egy szűkebb engedélyek az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager szolgáltatási végpont](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Tesztkörnyezet nevének**: válassza ki a korábban létrehozott példány nevét.
   - **Sablon neve**: Adja meg a teljes elérési útja és neve a sablon mentett fájlt, a forráskódraktárban be. Egyszerűbbé teheti az elérési út, például a beépített tulajdonságok kiadáskezelés használhatók:
      ```
      $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
      ```
   - **Sablonparaméterek**: Adja meg a paramétereket a sablonban definiált változókat. Használja a neveket például definiált a környezetben:
      ```
      -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
      ```
   - **A kimeneti változók – a labor virtuális gép azonosítója**: az újonnan létrehozott virtuális gép Azonosítóját a következő lépéseket kell. Az alapértelmezett nevet, a környezeti változó, amely ezt az Azonosítót automatikusan töltődik be a **kimeneti változók** szakasz. Módosítsa a változót, ha szükséges, de fontos, hogy megfelelő nevet a következő feladatok. A labor virtuális gép azonosítója a következő formában:
      ```
      /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
      ```
1. Hajtsa végre a gyűjtése a DevTest Labs szolgáltatásban virtuális gép adatait korábban létrehozott parancsfájlt. Válassza ki a kiadási-definícióban **+ Hozzáadás feladatok** , és adja hozzá egy **Azure PowerShell** a feladat a **telepítés** lapon. Adja meg a feladat az alábbiak szerint:

   Lásd: [központi telepítése: az Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) és gyűjthet a DevTest Labs szolgáltatásban virtuális gép adatait a parancsfájlt.

   - **Az Azure kapcsolattípus**: az Azure Resource Manager.
   - **Az Azure erőforrás-kezelő előfizetési**: alatti listából válasszon ki egy kapcsolatot **Azure-szolgáltatás rendelkezésre álló kapcsolatok** vagy hozzon létre egy szűkebb engedélyek az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager szolgáltatási végpont](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Parancsprogram típusa**: parancsfájlt.
   - **Parancsfájl elérési útja**: Adja meg a teljes elérési útja és mentettük azokat a forráskódraktárban parancsfájl nevét. Egyszerűbbé teheti az elérési út, például a beépített tulajdonságok kiadáskezelés használhatók:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   - **Parancsfájl argumentumai**: parancsfájl argumentumként adja meg a nevét, a környezeti változó, amely automatikusan fel – a labor virtuális gép azonosítójú az előző feladat, például: 
      ```
      -labVmId '$(labVMId)'
      ```
   A parancsfájl összegyűjti a szükséges értékeket, és tárolja a környezeti változók kiadás meghatározása, hogy Ön könnyen elérhesse azokat a későbbi lépésekben.

1. Most az alkalmazást telepítheti az új DevTest Labs szolgáltatásban virtuális Gépet. A feladatok általában használatával történő telepítése esetén **Azure fájlmásolással** és **a célszámítógépen PowerShell**.
   - A virtuális gép ezeket a feladatokat a paraméterek szükséges információ tárolódik nevű három konfigurációs változók **labVmRgName**, **labVMIpAddress**, és **labVMFqdn**kiadás meghatározása.
   - Ha csak létrehozása a DevTest Labs szolgáltatásban virtuális gép és egy egyéni lemezképet, azt egy alkalmazás telepítése nélkül kísérletezhet, kihagyhatja ezt a lépést.

   <a name="create-an-image"></a>Lemezkép létrehozása
   ---

   A következő szakaszban, ha az újonnan telepített virtuális gép lemezképét az Azure DevTest Labs-példányban. Ez a rendszerkép másolja a virtuális gép létrehozása az igény szerinti, amikor le kívánja végrehajtani egy fejlesztői feladatot, vagy bizonyos tesztek futtatásához használhatja. Válassza ki a kiadási-definícióban **+ Hozzáadás feladatok** , és adja hozzá egy **Azure DevTest Labs hozzon létre egyéni lemezkép** a feladat a **telepítés** lapon. Konfigurálja úgy az alábbiak szerint:

   Lásd: [Azure DevTest Labs feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) a lemezkép létrehozásához.
   - **Az Azure erőforrás-kezelő előfizetési**: alatti listából válasszon ki egy kapcsolatot **Azure-szolgáltatás rendelkezésre álló kapcsolatok** vagy hozzon létre egy szűkebb engedélyek az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager szolgáltatási végpont](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Tesztkörnyezet nevének**: válassza ki a korábban létrehozott példány nevét.
   - **Egyéni lemezkép neve**: Adjon meg egy nevet az egyéni lemezképet.
   - **Leírás**: Adja meg egy leírást, hogy könnyebben később válassza ki a megfelelő lemezképet.
   - **Forrás-tesztkörnyezet VM - forrás tesztlabor virtuális gép azonosítója**: Ha módosította az alapértelmezett nevet, a környezeti változó, amely automatikusan ki van töltve a labor virtuális gép egy korábbi feladat azonosítója, szerkesztheti itt. Az alapértelmezett érték *$(labVMId)*.
   - **A kimeneti változók - egyéni lemezkép-azonosító**: helyzet kezeléséhez, vagy törölni kell az újonnan létrehozott kép azonosítója. Az alapértelmezett nevet, a környezeti változó, amely ezt az Azonosítót automatikusan töltődik be a **kimeneti változók** szakasz. A változó szerkesztheti, ha szükséges.
   
   <a name="delete-the-vm"></a>A virtuális gép törlése
   ---
   
   Ebben a példában az utolsó szakasza törölni a virtuális Gépen központilag telepített Azure DevTest Labs-példány. Általában törölje a virtuális gép meghagy a van szüksége a telepített virtuális gép vagy a fejlesztői feladatok végrehajtása után. Válassza ki a kiadási-definícióban **+ Hozzáadás feladatok** , és adja hozzá egy **Azure DevTest Labs törlése VM** a feladat a **telepítés** lapon. Konfigurálja úgy az alábbiak szerint:

   Lásd: [Azure DevTest Labs feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) törölni a virtuális Gépet.
   - **Az Azure erőforrás-kezelő előfizetési**: alatti listából válasszon ki egy kapcsolatot **Azure-szolgáltatás rendelkezésre álló kapcsolatok** vagy hozzon létre egy szűkebb engedélyek az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager szolgáltatási végpont](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Labor virtuális gép azonosítója**: Ha módosította az alapértelmezett nevet, a környezeti változó, amely automatikusan ki van töltve a labor virtuális gép egy korábbi feladat azonosítója, szerkesztheti itt. Az alapértelmezett érték *$(labVMId)*.
1. Adjon meg egy nevet a kiadási definíciójának, majd mentse.
1. Hozzon létre egy új kiadását, válassza ki a legújabb buildjével és telepíteni kell a egyetlen környezet definíciójában.

Minden egyes szakaszában a nézet frissítése az Azure portálon, a virtuális gép, és a lemezkép létrehozása a DevTest Labs példányát, és a virtuális gép újra törlés alatt áll.

Szükség esetén virtuális gépek létrehozásához használhatja az egyéni lemezképet.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [virtuális Gépre kiterjedő környezetek létrehozása a Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md).
* További gyors üzembe helyezés Resource Manager-sablonok a DevTest Labs automatizálásra megismerkedhet a [nyilvános DevTest Labs GitHub-tárház](https://github.com/Azure/azure-quickstart-templates).
* Ha szükséges, tekintse meg a [VSTS hibaelhárítási](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) lap.
