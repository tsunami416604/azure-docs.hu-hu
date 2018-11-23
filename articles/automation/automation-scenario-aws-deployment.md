---
title: Az Amazon Web Services a virtuális gépek üzembe helyezésének automatizálása
description: Ez a cikk bemutatja, hogyan automatizálása az Amazon Web Service virtuális gép létrehozása az Azure Automation használatával
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8a5b34cd92f3afd166d5d67ca445c99a52c684e2
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290892"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Az Azure Automation-forgatókönyv – az AWS virtuális gép kiépítése
Ebből a cikkből megtudhatja, hogyan használhatók az Azure Automation és az Amazon Web Service (AWS) előfizetésben virtuális gép kiépítése, és nevezze el a virtuális gép adott – amely AWS, a virtuális gép "címkézés" hivatkozik.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk az alkalmazásában szüksége lesz egy Azure Automation-fiókot és a egy AWS-előfizetésre. További információ az Azure Automation-fiók beállításához és konfigurálásához, az AWS-előfizetés hitelesítő adataival, tekintse át a [hitelesítés konfigurálása az Amazon webszolgáltatásokkal](automation-config-aws-account.md). Ezt a fiókot kell létrehozni vagy frissíteni az AWS előfizetés hitelesítő adataival, a folytatás előtt, ahogy ezt a fiókot az alábbi lépéseket a hivatkozik.

## <a name="deploy-amazon-web-services-powershell-module"></a>Az Amazon Web Services PowerShell-modul üzembe helyezése
A virtuális gép kiépítése a runbook feladatának elvégzéséhez az AWS PowerShell-modult használja. A következő lépésekkel adja hozzá a modul az Automation-fiókhoz, amelynek része az AWS-előfizetés hitelesítő adataival.  

1. Nyissa meg a webböngészőjét, és keresse meg a [PowerShell-galériából](https://www.powershellgallery.com/packages/AWSPowerShell/) , majd kattintson a a **üzembe helyezés az Azure Automation gomb**.<br><br> ![AWS – PS modul importálása](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Ekkor megnyílik az Azure bejelentkezési oldal és a hitelesítés után, fogja lesz irányítva, az Azure portal és a következő oldal jelenik meg:<br><br> ![A modul oldal importálása](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Válassza ki az Automation-fiók használhatja, és kattintson a **OK** üzembe helyezésének megkezdéséhez.

   > [!NOTE]
   > Egy PowerShell-modul importálása az Azure Automationbe, hogy van is kibontása a parancsmagok, és ezek a tevékenységek nem jelennek meg, amíg a modul importálása, és a parancsmagok kinyeréséhez teljesen befejeződött. Ez a folyamat pár percet is igénybe vehet.  
   > <br>

1. Az Azure Portalon nyissa meg a 3. lépésben hivatkozott az Automation-fiók.
2. Kattintson a a **eszközök** csempét és a a **eszközök** panelen válassza a **modulok** csempére.
3. Az a **modulok** lapon láthatja a **AWSPowerShell** modul a listában.

## <a name="create-aws-deploy-vm-runbook"></a>Hozzon létre az AWS virtuális gép runbook telepítését
Az AWS PowerShell-modul üzembe helyezése után most már hozhat létre egy runbookot, az AWS-ben a PowerShell-szkripttel virtuális gép üzembe helyezésének automatizálása. Az alábbi lépések bemutatják, hogyan használhatja az Azure Automationben natív PowerShell-szkript.  

> [!NOTE]
> További beállítások és a szkript vonatkozó információkat talál a [PowerShell-galériából](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. New-AwsVM PowerShell-szkript letöltése a PowerShell-galériából, egy PowerShell-munkamenetet, és írja be a következőket:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Az Azure Portalról nyissa meg az Automation-fiókját, és válassza ki **Runbookok** a szakaszában **Folyamatautomatizálás** a bal oldalon.  
3. Az a **Runbookok** lapon jelölje be **forgatókönyv hozzáadása**.
4. Az a **forgatókönyv hozzáadása** ablaktáblán válassza **Gyorslétrehozás** (hozzon létre egy új runbookot).
5. A a **Runbook** tulajdonságait tartalmazó ablaktáblán írja be a forgatókönyv és a nevét (a neve) mezőben a **Runbook típusa** legördülő listában válassza ki **PowerShell**, majd kattintson a **Létrehozása**.<br><br> ![Runbook ablaktábla létrehozása](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Amikor a PowerShell-Runbook szerkesztése oldal megjelenik, másolja és illessze be a PowerShell-parancsfájlt a runbook szerzői vásznon.<br><br> ![Forgatókönyv PowerShell-parancsfájl](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > A példában a PowerShell-szkript használata során, vegye figyelembe a következőket:
    > 
    > * A forgatókönyv az alapértelmezett paraméterértékeket számát tartalmazza. Az összes alapértelmezett értékeit értékelheti ki, és szükség esetén frissítse.
    > * Ha a tárolt AWS hitelesítő adatait, mint más néven hitelesítőadat-eszköz **AWScred**, akkor frissítenie kell a parancsfájl sor 57 ennek megfelelően.  
    > * Az AWS CLI-parancsokkal a PowerShellben, különösen a Ez a példa runbook használatakor meg kell adnia az AWS régiót. Ellenkező esetben a parancsmagok sikertelen. Az AWS-témakör megtekintése [adja meg az AWS-régió](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) az AWS eszközök PowerShell dokumentum további tájékoztatást talál.  
    >

7. A rendszerképnevek listájának lekéréséhez az AWS-előfizetésből, indítsa el a PowerShell ISE-ben, és az AWS PowerShell-modul importálása. AWS hitelesítő lecserélésével **Get-AutomationPSCredential** az ISE környezetben **AWScred = Get-Credential**. Ez a hitelesítő adatokat kér, és megadhatja a **elérési kulcs Azonosítóját** felhasználónév és a **titkos elérési kulcsát** a jelszót. Lásd az alábbi példát:  

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
        
    A következő eredményt adja vissza:<br><br>
   ![AWS – képek bekérése](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Másolja és illessze be az egyik a rendszerképnevek egy Automation-változóban, mint a runbookban hivatkozott **$InstanceType**. Mivel ebben a példában az előfizetés ingyenes az AWS használatával rétegzett, használhatja **t2.micro** a runbook például.  
9. Mentse a forgatókönyvet, majd kattintson a **közzététel** közzétenni a runbookot, majd **Igen** amikor a rendszer kéri.

### <a name="testing-the-aws-vm-runbook"></a>Az AWS-VM runbook tesztelése
Mielőtt folytatná a forgatókönyv tesztelését, azt szeretné megtudni, hogy néhány dolgot. Konkrétan:  

* Egy AWS hitelesítést az eszköz nevű létrejött **AWScred** vagy a parancsfájl frissítve lett, hogy a hitelesítőadat-eszköz nevére hivatkozhatnak.    
* Az AWS PowerShell-modul importálása az Azure Automationben  
* Új runbook létrehozása és a paraméterértékek ellenőrizve és szükség esetén frissítése  
* **Részletes rekordok naplózására** és opcionálisan **állapotrekordok naplózására van** a runbook beállítás alatt **naplózás és nyomkövetés** beállított **a**.<br><br> ![Runbook-naplózás és nyomkövetés](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. El szeretné indítani a runbookot, tehát kattintson **Start** majd **OK** amikor a Runbook indítása panel megnyílik.
2. A Runbook indítása panelen adjon meg egy **VMname**. Fogadja el az alapértelmezett értékeket, amelyek korábban előre a szkriptben a többi paraméter esetén. Kattintson a **OK** a runbook-feladat elindításához.<br><br> ![New-AwsVM runbook elindítása](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Egy feladatpanel a létrehozott runbook-feladathoz. Zárja be ezt a panelt.
4. Előrehaladását a feladatok és a nézet kimenetének megtekintéséhez **adatfolyamok** kiválasztásával a **az összes napló** csempére a runbookfeladat oldalán.<br><br> ![A kimeneti Stream](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Ellenőrizze, hogy a virtuális gép kiépítése folyamatban van, ha nem jelenleg jelentkezett jelentkezzen be az AWS-felügyeleti konzolon.<br><br> ![Az AWS konzol üzembe helyezett virtuális gép](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>További lépések
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

