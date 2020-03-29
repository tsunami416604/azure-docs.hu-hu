---
title: Virtuális gép telepítésének automatizálása az Amazon Web Services szolgáltatásban
description: Ez a cikk bemutatja, hogyan használhatja az Azure Automation segítségével az Amazon Web Service virtuális gép létrehozásának automatizálását
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: b149a21ef60b22c2e549c91007265ce99babdbe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420927"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation-forgatókönyv - AWS virtuális gép kiépítése
Ebben a cikkben megtudhatja, hogyan használhatja ki az Azure Automation-t egy virtuális gép kiépítéséhez az Amazon Web Service (AWS) előfizetésében, és adott nevet adhat a virtuális gépnek – amelyet az AWS a virtuális gép "címkézésének" nevez.

## <a name="prerequisites"></a>Előfeltételek
Ebben a cikkben az Azure Automation-fiókkal és egy AWS-előfizetéssel kell rendelkeznie. Az Azure Automation-fiók beállításával és az AWS-előfizetés hitelesítő adataival történő konfigurálásával kapcsolatos további információkért tekintse át [a Hitelesítés konfigurálása az Amazon Web Services szolgáltatással című témakört.](automation-config-aws-account.md) Ezt a fiókot a folytatás előtt létre kell hozni vagy frissíteni kell az AWS-előfizetés hitelesítő adataival, mivel az alábbi lépésekben hivatkozik erre a fiókra.

## <a name="deploy-amazon-web-services-powershell-module"></a>Az Amazon Web Services PowerShell-modul telepítése
A virtuális gép kiépítése runbook használja az AWS PowerShell-modul munkáját. Hajtsa végre az alábbi lépéseket, hogy hozzáadja a modult az AWS-előfizetéshitelesítő adataival konfigurált Automation-fiókhoz.  

1. Nyissa meg a webböngészőt, és keresse meg a [PowerShell-galériát,](https://www.powershellgallery.com/packages/AWSPowerShell/) és kattintson az **Azure Automation telepítése gombra.**<br><br> ![AWS PS modul importálása](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Az Azure bejelentkezési lapjára kerül, és a hitelesítés után a rendszer átirányítja az Azure Portalra, és a következő lapot kapja meg:<br><br> ![Modul importálása lap](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Válassza ki a használni kívánt automatizálási fiókot, és a telepítés elindításához kattintson az **OK** gombra.

   > [!NOTE]
   > A PowerShell-modul Azure Automation-be importálása közben a parancsmagok kibontása is, és ezek a tevékenységek nem jelennek meg, amíg a modul teljesen befejezte a parancsmagok importálását és kibontását. Ez a folyamat eltarthat néhány percig.  
   > <br>

1. Az Azure Portalon nyissa meg a 3.
2. Kattintson az **Eszközök** csempére, és az **Eszközök** ablaktáblán válassza a **Modulok csempét.**
3. A **modulok** lapon az **AWSPowerShell** modul látható a listában.

## <a name="create-aws-deploy-vm-runbook"></a>AWS központi telepítésének virtuális gép-runbooklétrehozása
Az AWS PowerShell-modul üzembe helyezése után most már létrehozhat egy runbookot, amely egy PowerShell-parancsfájl használatával automatizálhatja a virtuális gépek kiépítését az AWS-ben. Az alábbi lépések bemutatják, hogyan használhatja ki a natív PowerShell-parancsfájlt az Azure Automationben.  

> [!NOTE]
> A parancsfájllal kapcsolatos további lehetőségekért és információkért látogasson el a [PowerShell-galériába.](https://www.powershellgallery.com/packages/New-AwsVM/)
> 

1. Töltse le az Új-AwsVM PowerShell-parancsfájlt a PowerShell-galériából egy PowerShell-munkamenet megnyitásával és a következő beírással:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Az Azure Portalon nyissa meg az Automation-fiókot, és válassza a **Runbookok** a bal oldali **Folyamatautomatizálás** című szakaszban.  
3. A **Runbooks** lapon válassza **a Runbook hozzáadása**lehetőséget.
4. A **Runbook hozzáadása** ablaktáblán válassza a **Gyors létrehozás** (Új runbook létrehozása) lehetőséget.
5. A **Runbook** tulajdonságai ablaktáblán írjon be egy nevet a runbook Név mezőjébe, és a **Runbook típus** legördülő listájából válassza a **PowerShell**elemet, majd kattintson a **Létrehozás gombra.**<br><br> ![Runbook-ablaktábla létrehozása](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Amikor megjelenik a PowerShell Runbook-lap szerkesztéseke, másolja és illessze be a PowerShell-parancsfájlt a runbook-készítő vászonba.<br><br> ![Runbook PowerShell-parancsfájl](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Vegye figyelembe az alábbiakat, ha a példa PowerShell-parancsfájlhasználatával dolgozik:
    > 
    > * A runbook számos alapértelmezett paraméterértéket tartalmaz. Értékelje ki az összes alapértelmezett értéket, és szükség esetén frissítse.
    > * Ha az AWS hitelesítő adatait az **AWScred-től**eltérő nevű hitelesítő egységként tárolta, frissítenie kell a parancsfájlt az 57-es sorban, hogy ennek megfelelően egyezzen.  
    > * Ha a PowerShell AWS CLI parancsaival dolgozik, különösen ebben a példa runbookban, meg kell adnia az AWS régiót. Ellenkező esetben a parancsmagok sikertelenek. További részletekért tekintse meg az AWS-témakört: Adja meg az [AWS-régiót](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) az AWS Tools for PowerShell-dokumentumban.  
    >

7. Ha le szeretné kérni a képnevek listáját az AWS-előfizetésből, indítsa el a PowerShell ISE-t, és importálja az AWS PowerShell-modult. Az AWS-hez kapcsolódó hitelesítéssel az ISE-környezetben lévő **Get-AutomationPSCredential** szolgáltatást a **AWScred = Get-Credential értékre cserélve.** Ez kéri a hitelesítő adatokat, és meg adhatja a **hozzáférési kulcs azonosítóját** a jelszóhoz a felhasználónévhez és a **titkos hozzáférési kulcshoz.** Lásd az alábbi példát:  

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
        
    A következő kimenetet adja vissza:<br><br>
   ![AWS-képek beszerezése](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Másolja és illessze be az egyik képnevet egy Automation változóban, ahogy a runbookban hivatkozik, mint **$InstanceType.** Mivel ebben a példában az ingyenes AWS rétegzett előfizetést használja, a **t2.micro-t** használja a runbook-példához.  
9. Mentse a runbookot, majd kattintson a **Közzététel gombra** a runbook közzétételéhez, majd az **Igen** gombra kattintva.

### <a name="testing-the-aws-vm-runbook"></a>Az AWS virtuális gép runbookjának tesztelése
Mielőtt folytatná a runbook tesztelését, ellenőriznie kell néhány dolgot. Ezek a következők:  

* Az AWS-hez való hitelesítéshez egy eszköz jött létre **AWScred** néven, vagy a parancsfájl frissítve lett, hogy a hitelesítő eszköz nevére hivatkozzon.    
* Az AWS PowerShell modul importálása megtörtént az Azure Automationben  
* Új runbook jött létre, és szükség esetén ellenőrizték és frissítették a paraméterértékeket  
* **Részletes rekordok naplózása** és szükség esetén **a folyamatrekordok naplózása** és nyomon követése című runbook-beállítás alatt a napló és **a nyomkövetés** naplója **be**van kapcsolva.<br><br> ![Runbook naplózása és nyomon követése](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. El szeretné indítani a runbookot, ezért kattintson a **Start** gombra, majd kattintson az **OK** gombra, amikor megnyílik a Runbook indítása ablaktábla.
2. A Runbook indítása ablaktáblán adjon meg egy **virtuális gépnevet.** Fogadja el a parancsfájlban korábban konfigurált egyéb paraméterek alapértelmezett értékeit. Kattintson **az OK** gombra a runbook-feladat elindításához.<br><br> ![Új-AwsVM runbook indítása](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Meg van nyitva egy feladatablaktábla a létrehozott runbook-feladathoz. Zárja be ezt az ablaktáblát.
4. Megtekintheti a feladat előrehaladását, és megtekintheti a kimeneti **adatfolyamokat** a Runbook-feladat lap **Minden napló** csempéjének kiválasztásával.<br><br> ![Adatfolyam kimenete](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. A virtuális gép kiépítésének ellenőrzéséhez jelentkezzen be az AWS felügyeleti konzolra, ha jelenleg nincs bejelentkezve.<br><br> ![Az AWS konzol telepített virtuális gép](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>További lépések
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-munkafolyamat-runbookok első lépései: [Az első PowerShell-munkafolyamat-runbook](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


