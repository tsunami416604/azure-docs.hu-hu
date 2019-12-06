---
title: Virtuális gép üzembe helyezésének automatizálása Amazon Web Services
description: Ez a cikk bemutatja, hogyan használható a Azure Automation az Amazon Web Service-alapú virtuális gépek létrehozásának automatizálására
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2a58d3e79301f277143d8c4b6e810a377a211b9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849632"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation forgatókönyv – AWS virtuális gép kiépítése
Ebből a cikkből megtudhatja, hogyan használhatja a Azure Automationt egy virtuális gép kiépítéséhez az Amazon Web Service-(AWS-) előfizetésben, és adjon egy adott nevet a virtuális gépnek, amely az AWS a virtuális gép "címkézési" kifejezésére utal.

## <a name="prerequisites"></a>Előfeltételek
Ennek a cikknek a értelmében rendelkeznie kell egy Azure Automation fiókkal és egy AWS-előfizetéssel. Azure Automation-fiók beállításával és az AWS-előfizetés hitelesítő adataival való konfigurálásával kapcsolatos további információkért tekintse át a következő témakört: a [hitelesítés konfigurálása a Amazon Web Services](automation-config-aws-account.md)használatával. Ezt a fiókot az AWS-előfizetés hitelesítő adataival kell létrehozni vagy frissíteni a továbblépés előtt, amikor erre a fiókra hivatkozik az alábbi lépésekben.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell-modul üzembe helyezése
A virtuális gép kiépítési runbook felhasználja az AWS PowerShell-modult, hogy elvégezze a munkáját. A következő lépések végrehajtásával adja hozzá a modult az Automation-fiókjához, amely az AWS-előfizetés hitelesítő adataival van konfigurálva.  

1. Nyissa meg a webböngészőt, és navigáljon a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AWSPowerShell/) , és kattintson a **telepítés a Azure Automation gombra**.<br><br> ![AWS PS-modul importálása](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. A hitelesítés után a rendszer átAzure Portal irányítja az Azure-beli bejelentkezési oldalra, majd a következő oldalon jelenik meg:<br><br> ![Modul importálása lap](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Válassza ki a használni kívánt Automation-fiókot, majd kattintson **az OK** gombra az üzembe helyezés megkezdéséhez.

   > [!NOTE]
   > Egy PowerShell-modulnak a Azure Automationba való importálásakor a rendszer kinyeri a parancsmagokat is, és ezek a tevékenységek addig nem jelennek meg, amíg a modul nem végezte el a parancsmagok importálását és kicsomagolását. Ez a folyamat néhány percet is igénybe vehet.  
   > <br>

1. A Azure Portal nyissa meg a 3. lépésben hivatkozott Automation-fiókot.
2. Kattintson az **eszközök** csempére, és az **eszközök** ablaktáblán válassza a **modulok** csempét.
3. A **modulok** oldalon láthatja a **AWSPowerShell** modult a listában.

## <a name="create-aws-deploy-vm-runbook"></a>AWS üzembe helyezése VM-runbook létrehozása
Miután telepítette az AWS PowerShell-modult, létrehozhat egy runbook, amely egy PowerShell-parancsfájl használatával automatizálja a virtuális gépeket az AWS-ben. Az alábbi lépések bemutatják, hogyan használhat natív PowerShell-parancsfájlokat a Azure Automationban.  

> [!NOTE]
> A parancsfájlra vonatkozó további beállítások és információk a [PowerShell-galériaban](https://www.powershellgallery.com/packages/New-AwsVM/)érhetők el.
> 

1. Töltse le a New-AwsVM PowerShell-szkriptet a PowerShell-galéria egy PowerShell-munkamenet megnyitásával, és írja be a következőt:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. A Azure Portal nyissa meg Automation-fiókját, és a bal oldali **folyamat automatizálása** szakaszban válassza a **runbookok** lehetőséget.  
3. A **runbookok** lapon válassza a **runbook hozzáadása**elemet.
4. A **Runbook hozzáadása** panelen válassza a **gyors létrehozás** lehetőséget (hozzon létre egy új runbook).
5. A **Runbook** tulajdonságok ablaktábláján írjon be egy nevet a Runbook név mezőjébe, majd a **Runbook típusa** legördülő listából válassza a **PowerShell**elemet, majd kattintson a **Létrehozás**gombra.<br><br> ![runbook panel létrehozása](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Amikor megjelenik a PowerShell-Runbook szerkesztése lap, másolja és illessze be a PowerShell-szkriptet a Runbook authoring vászonba.<br><br> ![Runbook PowerShell-parancsfájl](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > A példaként szolgáló PowerShell-parancsfájl használatakor vegye figyelembe a következőket:
    > 
    > * A runbook számos alapértelmezett paraméter értéket tartalmaz. Szükség esetén értékelje ki az összes alapértelmezett értéket és frissítést.
    > * Ha az AWS hitelesítő adatait a **AWScred**eltérően megnevezett hitelesítőadat-eszközként tárolta, frissítenie kell a parancsfájlt az 57-es sorban, hogy az megfeleljen ennek megfelelően.  
    > * Ha az AWS CLI-parancsokkal dolgozik a PowerShellben, különösen ebben a példában runbook, meg kell adnia az AWS régiót. Ellenkező esetben a parancsmagok meghiúsulnak. Az AWS megtekintése témakörben az AWS- [régiót](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) az AWS-eszközök PowerShell-dokumentumában találhatja meg további részletekért.  
    >

7. Az AWS-előfizetésből származó rendszerkép-nevek listájának lekéréséhez indítsa el a PowerShell ISE-t, és importálja az AWS PowerShell-modult. Az AWS-ben való hitelesítéshez cserélje le a **Get-AutomationPSCredential** -t az ISE-környezetbe a **AWScred = Get-hitelesítőadat**használatával. Ezzel megkéri a hitelesítő adatait, és megadhatja a jelszóhoz tartozó felhasználónévhez és **titkos hozzáférési kulcshoz** tartozó **hozzáférési kulcs azonosítóját** . Lásd az alábbi példát:  

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    A rendszer a következő kimenetet adja vissza:<br><br>
   AWS-lemezképek ![beolvasása](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Másolja és illessze be az egyik rendszerkép nevét egy Automation-változóba úgy, ahogy az a runbook hivatkozik **$InstanceTypeként**. Mivel ebben a példában az ingyenes AWS rétegű előfizetést használja, a **T2. microt** használjuk a runbook példához.  
9. Mentse a runbook, majd kattintson a **Közzététel** gombra a runbook közzétételéhez, majd az **Igen** gombra, amikor a rendszer kéri.

### <a name="testing-the-aws-vm-runbook"></a>Az AWS virtuális gép runbook tesztelése
Mielőtt folytatná a runbook tesztelését, néhány dolgot ellenőriznie kell. Konkrétan:  

* Az AWS-vel való hitelesítésre szolgáló adategység létrejött a **AWScred** néven, vagy a szkript frissült, hogy az a hitelesítő eszköz nevére hivatkozzon.    
* Az AWS PowerShell-modul importálása Azure Automation  
* Új runbook lett létrehozva, és szükség esetén a paraméterek értékeinek ellenőrzése és frissítése megtörtént  
* **Naplózza a részletes rekordokat** , és szükség esetén **naplózhatja** a runbook beállítás **naplózása és** **a**nyomkövetés beállítás értékét.<br><br> ![Runbook naplózása és nyomkövetése](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. El szeretné indítani a runbook, kattintson a **Start** gombra, majd kattintson az **OK** gombra, amikor megnyílik a runbook indítása panel.
2. A Runbook indítása panelen adjon meg egy **VMname**. Fogadja el a korábbi parancsfájlban előre konfigurált egyéb paraméterek alapértelmezett értékeit. A runbook-feladatok elindításához kattintson **az OK** gombra.<br><br> ![Start New-AwsVM runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. A panel bezárásához.
4. Megtekintheti a feladatok előrehaladását, és megtekintheti a kimeneti **streameket** a runbook-feladatokhoz tartozó **összes napló** csempére kattintva.<br><br> ![stream kimenete](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. A virtuális gép üzembe helyezésének ellenőrzéséhez jelentkezzen be az AWS felügyeleti konzolba, ha jelenleg nincs bejelentkezve.<br><br> ![AWS-konzol üzembe helyezett virtuális gép](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Következő lépések
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


