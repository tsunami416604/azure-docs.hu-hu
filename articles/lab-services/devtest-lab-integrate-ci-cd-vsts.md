---
title: Az Azure DevTest Labs folyamatba való integrálásához VSTS folyamatos integrációt és teljesítést |} A Microsoft Docs
description: Ismerje meg, hogyan integrálhatja az Azure DevTest Labs a VSTS folyamatos integrációs és teljesítési folyamat
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1af195e644fe93e0c59f5e4402dd8942f5fe1aba
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635506"
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Az Azure DevTest Labs integrálása a VSTS folyamatos integrációs és teljesítési folyamat
Használhatja a *Azure DevTest Labs-feladatok* bővítmény, amely egyszerűen települ a Visual Studio Team Services (VSTS) hogy a build és kiadás folyamatos üzembe helyezési folyamat integrálása az Azure DevTest Labs szolgáltatásban. A bővítmény telepítését három feladatot: 
* Virtuális gép létrehozása
* Egyéni lemezkép készítése egy virtuális Gépről
* Virtuális gép törlése 

A folyamat megkönnyíti, például gyorsan üzembe helyezhet egy adott teszt tevékenység "arany lemezképpel", és törölje, ha a teszt befejeződött.

Ez a cikk bemutatja, hogyan hozzon létre és a virtuális gép üzembe helyezése, hozzon létre egy egyéni rendszerképet és törölje a virtuális Gépet, az összes, egy teljes körű folyamatot. Általában elvégezhető minden egyes tevékenységhez külön-külön saját egyéni buildelési, tesztelési és üzembe folyamat.

## <a name="before-you-begin"></a>Előkészületek
A CI/CD-folyamat integrálható az Azure DevTest Labs szolgáltatással, telepítenie kell a bővítményt a Visual Studio Marketplace-ről.
1. Lépjen a [Azure DevTest Labs-feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Válassza az **Install** (Telepítés) lehetőséget.
1. A varázsló befejezése.

## <a name="create-a-resource-manager-template"></a>Resource Manager-sablon létrehozása
Ez a szakasz ismerteti, hogyan hozhat létre az Azure Resource Manager-sablon, amellyel igény szerint hozhat létre egy Azure virtuális gépen.

1. Az előfizetés egy Resource Manager-sablon létrehozásához hajtsa végre az eljárást a [egy Resource Manager-sablonnal](devtest-lab-use-resource-manager-template.md).
1. Mielőtt létrehozza a Resource Manager-sablon, adja hozzá a [WinRM összetevő](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) a virtuális gép létrehozása során.

   A WinRM-elérés kell használnia például a telepítési feladatok *Azure fájlmásolás* és *PowerShellben célszámítógépekre*.

   > [!NOTE]
   > Amikor a Rendszerfelügyeleti webszolgáltatások egy megosztott IP-címet használja, hozzá kell adnia egy NAT-szabályt egy külső port leképezése a WinRM-port. Ebben a lépésben nincs szükség, ha a virtuális gép nyilvános IP-címet hoz létre.
   >
   >

1. Mentse a sablont egy fájlba a számítógépen. A fájl neve **CreateVMTemplate.json**.
1. Ellenőrizze a sablont, hogy a forráskódú verziókezelő rendszer.
1. Nyisson meg egy szövegszerkesztőt, és illessze be az alábbi parancsfájlt.

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

1. Ellenőrizze a parancsfájl, hogy a forráskódú verziókezelő rendszer. Nevezze el például **GetLabVMParams.ps1**.

   A parancsfájl futtatásakor az ügynök a kiadási definíció részeként, és ha a feladat lépéseket használhatja például a *Azure File Copy* vagy *PowerShellben célszámítógépekre*, a parancsfájl gyűjti az értékeket, amelyeket szeretne az alkalmazás üzembe helyezése a virtuális géphez. Ezek a feladatok az alkalmazások üzembe helyezése az Azure virtuális gép általában használna. A feladatokat a virtuális gép erőforráscsoportja nevét, IP-címet, és teljesen minősített tartománynevét (FQDN) például értékekre van szükség.

## <a name="create-a-release-definition-in-release-management"></a>A kiadási felügyeleti kiadási definíció létrehozása
A kiadási definíció létrehozása, tegye a következőket:

1. Az a **kiadásokban** lapján a **Build & Release** hub, válassza a plusz jelre (+) gombot.
2. Az a **kiadási definíció létrehozása** ablakban válassza ki a **üres** sablont, és válassza ki **tovább**.
3. Válassza ki **válasszon később**, majd válassza ki **létrehozás** új kiadási definíció létrehozása egy alapértelmezett környezetet, és nem társított összetevők.
4. A helyi menü megnyitásához, az új kiadás definíciója, a környezet neve melletti három pontra (...), majd válassza ki és **változók konfigurálása**. 
5. Az a **konfigurálása - környezet** ablakban, a változók a kiadási definíció feladatok során használt, adja meg a következő értékeket:

   a. A **vmName**, adja meg a nevét, amely a virtuális gép az Azure Portalon a Resource Manager-sablon létrehozásakor hozzárendelt.

   b. A **felhasználónév**, adja meg a felhasználónevét, amely a virtuális gép az Azure Portalon a Resource Manager-sablon létrehozásakor hozzárendelt.

   c. A **jelszó**, írja be a virtuális gép az Azure Portalon a Resource Manager-sablon létrehozásakor hozzárendelt jelszót. A "lakat" ikon használatával elrejtéséhez, és a jelszót biztonságos.

### <a name="create-a-vm"></a>Virtuális gép létrehozása

A központi telepítés a következő szintre, ha a virtuális Géphez használandó "arany lemezképpel" az ezt követő központi telepítések. A virtuális gép az Azure DevTest Labs-példány belül kifejezetten erre a célra fejlesztik feladat használatával hoz létre. 

1. Válassza ki a kiadási definíció **tevékenységek hozzáadása a**.
2. Az a **telepítés** lapon maradva adja hozzá egy *Azure DevTest Labs szolgáltatásban hozzon létre virtuális gép* feladat. A feladat a következőképpen konfigurálja:

   > [!NOTE]
   > Az ezt követő központi telepítéséhez használandó virtuális gép létrehozásához lásd: [Azure DevTest Labs-feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. A **Azure RM-előfizetés**, válassza ki a kapcsolat a **elérhető Azure-szolgáltatás kapcsolatok** listában, vagy hozzon létre több korlátozott engedélyek kapcsolat az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager-szolgáltatásvégpont](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. A **labor nevét**, válassza ki a korábban létrehozott példány nevét.

   c. A **sablonnevet**, adja meg a teljes elérési útja és neve a sablon mentett fájlt, a forráskód tárházából. Használhatja a beépített tulajdonságai között a Kiadáskezelési egyszerűsítése érdekében az elérési út, például:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. A **Sablonparaméterek**, adja meg a paramétereket a sablonban meghatározott változók. A változók, például meghatározott a környezetben, a neveket használja:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. A **kimeneti változók – a labor virtuális gép azonosítója**, későbbi lépésekben szüksége lesz az újonnan létrehozott virtuális gép azonosítója. Beállíthatja az alapértelmezett nevet, a környezeti változó, amely ezt az Azonosítót a rendszer automatikusan kitölti a **kimeneti változók** szakaszban. Módosítsa a változót, ha szükséges, de a vágólapra a későbbi feladatok helyes nevét használja. A labor virtuális gép azonosítója íródik a következő formátumban:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Hajtsa végre a gyűjtése a DevTest Labs virtuális gép részletei korábban létrehozott parancsfájlt. 
4. Válassza ki a kiadási definíció **tevékenységek hozzáadása a** , majd a a **telepítés** lapon maradva adja hozzá egy *Azure PowerShell-lel* feladat. A feladat a következőképpen konfigurálja:

   > [!NOTE]
   > Gyűjteni a DevTest Labs virtuális gép adatait, tekintse meg [üzembe helyezés: Azure PowerShell-lel](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) , és hajtsa végre a parancsprogramot.

   a. A **Azure kapcsolattípus**válassza **Azure Resource Manager**.

   b. A **Azure RM-előfizetés**, ki egy kapcsolatot a listában **elérhető Azure-szolgáltatás kapcsolatok**, vagy hozzon létre több korlátozott engedélyek kapcsolat az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager-szolgáltatásvégpont](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   c. A **Szkripttípus**válassza **parancsfájl**.
 
   d. A **parancsfájl elérési útján**, adja meg a teljes elérési útját és nevét, a forráskód tárházából mentett parancsfájlt. Használhatja a beépített tulajdonságai között a Kiadáskezelési egyszerűsítése érdekében az elérési út, például:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. A **Argumenty Skriptu**, adja meg a nevét, amely a rendszer automatikusan kitölti a labor virtuális gép azonosítója az előző tevékenység által például környezeti változó: 
      ```
      -labVmId '$(labVMId)'
      ```
    A parancsfájl összegyűjti a szükséges értékeket, és az környezeti változókat a kiadási definíció belül tárolja őket, így könnyen hivatkozhat rájuk a későbbi lépésekben.

5. Az alkalmazás üzembe helyezése az új DevTest Labs virtuális gépre. A feladatok általában használatával az alkalmazás üzembe helyezése *Azure fájlmásolás* és *PowerShellben célszámítógépekre*.
   A virtuális Gépet, ezeket a feladatokat a paraméterek szükséges információ nevű három konfigurációs változókban tárolt **labVmRgName**, **labVMIpAddress**, és **labVMFqdn**a kiadási definíció belül. Ha csak szeretné létrehozása a DevTest Labs virtuális gép és a egy egyéni rendszerképet, az alkalmazás üzembe helyezése nélkül kísérletezhet, kihagyhatja ezt a lépést.

### <a name="create-an-image"></a>Rendszerkép létrehozása

A következő szakaszban, hogy az újonnan üzembe helyezett virtuális gép rendszerképének létrehozása az Azure DevTest Labs-példányban. Ezután használhatja a lemezképet, igény szerint hozhat létre a virtuális gép másolatát, ha szeretné végrehajtani egy fejlesztési feladatot, vagy egyes tesztek futtatása. 

1. Válassza ki a kiadási definíció **tevékenységek hozzáadása a**.
2. Az a **telepítés** lapon maradva adja hozzá egy **Azure DevTest Labs szolgáltatásban hozzon létre egyéni lemezkép** feladat. Ez a következőképpen konfigurálja:

   > [!NOTE]
   > A kép létrehozásához lásd: [Azure DevTest Labs-feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. A **Azure RM-előfizetés**, a a **elérhető Azure-szolgáltatás kapcsolatok** listában válassza ki a kapcsolat a listából, vagy hozzon létre több korlátozott engedélyek kapcsolat az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager-szolgáltatásvégpont](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. A **labor nevét**, válassza ki a korábban létrehozott példány nevét.

   c. A **egyéni rendszerkép neve**, adjon meg egy nevet az egyéni rendszerképet.

   d. (Nem kötelező) A **leírás**, adjon meg egy leírást, amely megkönnyíti a későbbi válassza ki a megfelelő lemezképet.

   e. A **forrás Lab VM - Lab VM Forrásazonosítója**, ha a labor virtuális gép egy korábbi feladat azonosítójú a környezeti változó, amely a rendszer automatikusan kitölti a rendszer alapértelmezett neve módosította Itt szerkesztheti. Az alapértelmezett érték **$(labVMId)**.

   f. A **kimeneti változók – egyéni lemezkép-azonosító**, kezelése vagy törlése, ha az újonnan létrehozott rendszerkép Azonosítóját kell. Az alapértelmezett nevet, a környezeti változó, amely a rendszer automatikusan kitölti ezt az Azonosítót értéke a **kimeneti változók** szakaszban. Szükség esetén szerkesztheti a változót.

### <a name="delete-the-vm"></a>A virtuális gép törlése

Az utolsó fáziséit, hogy törli a virtuális Gépet üzembe helyezett Azure DevTest Labs-példányában. A virtuális gép után hajtsa végre a fejlesztési feladatokat, vagy a telepített virtuális gépen futtatni a teszteket, amelyekre szüksége parancsmagokéval töröljük. 

1. Válassza ki a kiadási definíció **tevékenységek hozzáadása a** , majd a a **telepítés** lapon maradva adja hozzá egy *Azure DevTest Labs törlése VM* feladat. Ez a következőképpen konfigurálja:

      > [!NOTE]
      > Törölje a virtuális Gépet, tekintse meg [Azure DevTest Labs-feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. A **Azure RM-előfizetés**, válassza ki a kapcsolat a **elérhető Azure-szolgáltatás kapcsolatok** listában, vagy hozzon létre több korlátozott engedélyek kapcsolat az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager-szolgáltatásvégpont](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
 
   b. A **tesztlabor virtuális gép azonosítója**, ha a labor virtuális gép egy korábbi feladat azonosítójú a környezeti változó, amely a rendszer automatikusan kitölti a rendszer alapértelmezett neve módosította Itt szerkesztheti. Az alapértelmezett érték **$(labVMId)**.

2. Adja meg a kiadási definíció nevét, és mentse azt.
3. Hozzon létre egy új kiadás, válassza ki a legújabb buildre és telepítheti a definícióban az egyetlen környezetre.

Minden egyes fázisában a nézet frissítése a DevTest Labs-példány az Azure Portalon a virtuális gép és a lemezkép létrehozása és a virtuális gép által törlése folyamatban van újra megtekintéséhez.

Mostantól használhatja az egyéni rendszerképet hozhat létre virtuális gépeket, ha szükséges.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [több virtuális gépes környezet létrehozása a Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md).
* Ismerje meg a további a rövid útmutató Resource Manager-sablonokkal a DevTest Labs automation a [nyilvános DevTest Labs GitHub-adattárat](https://github.com/Azure/azure-quickstart-templates).
* Ha szükséges, tekintse meg a [VSTS hibaelhárítási](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) lapot.
 
