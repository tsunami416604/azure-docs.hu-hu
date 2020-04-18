---
title: Virtuális gép telepítésének automatizálása az Amazon Web Services szolgáltatásban
description: Ez a cikk bemutatja, hogyan használhatja az Azure Automation segítségével az Amazon Web Service virtuális gép létrehozásának automatizálását
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604833"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation-forgatókönyv - AWS virtuális gép kiépítése
Ebben a cikkben megtudhatja, hogyan használhatja ki az Azure Automation-t egy virtuális gép kiépítéséhez az Amazon Web Service (AWS) előfizetésében, és adott nevet adhat a virtuális gépnek – amelyet az AWS a virtuális gép "címkézésének" nevez.

## <a name="prerequisites"></a>Előfeltételek
Rendelkeznie kell egy Azure Automation-fiókkal és egy Amazon Web Services (AWS) előfizetéssel. Az Azure Automation-fiók beállításával és az AWS-előfizetés hitelesítő adataival történő konfigurálásával kapcsolatos további információkért tekintse át [a Hitelesítés konfigurálása az Amazon Web Services szolgáltatással című témakört.](automation-config-aws-account.md) Ezt a fiókot a folytatás előtt létre kell hozni vagy frissíteni kell az AWS-előfizetés hitelesítő adataival, mivel az alábbi szakaszokban hivatkozik erre a fiókra.

## <a name="deploy-amazon-web-services-powershell-module"></a>Az Amazon Web Services PowerShell-modul telepítése
A virtuális gép kiépítése runbook az AWS PowerShell-modul munkáját használja. Az alábbi lépésekkel adja hozzá a modult az AWS-előfizetési hitelesítő adataival konfigurált Automation-fiókhoz.  

1. Nyissa meg a webböngészőt, és keresse meg a [PowerShell-galériát,](https://www.powershellgallery.com/packages/AWSPowerShell/) és kattintson az **Azure Automation telepítése gombra.**<br><br> ![AWS PS modul importálása](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Az Azure bejelentkezési lapjára kerül, és a hitelesítés után a rendszer átirányítja az Azure Portalra, és a következő lapot kapja meg:<br><br> ![Modul importálása lap](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Válassza ki a használni kívánt Automation-fiókot, és a telepítés elindításához kattintson az **OK** gombra.

   > [!NOTE]
   > Amikor az Azure Automation importál egy PowerShell-modult, kinyeri a parancsmagokat. A tevékenységek csak akkor jelennek meg, ha az Automation teljesen befejezte a modul importálását és a parancsmagok kibontását. Ez eltarthat pár percig.  
   > <br>

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Kattintson az **Eszközök** csempére, és az Eszközök ablaktáblán válassza a **Modulok**lehetőséget.
3. A modulok lapon az **AWSPowerShell** modul látható a listában.

## <a name="create-aws-deploy-vm-runbook"></a>AWS központi telepítésének virtuális gép-runbooklétrehozása
Az AWS PowerShell-modul üzembe helyezése után most már létrehozhat egy runbookot, amely egy PowerShell-parancsfájl használatával automatizálhatja a virtuális gépek kiépítését az AWS-ben. Az alábbi lépések bemutatják, hogyan használhatja a natív PowerShell-parancsfájlt az Azure Automationben.  

> [!NOTE]
> A parancsfájllal kapcsolatos további lehetőségekért és információkért látogasson el a [PowerShell-galériába.](https://www.powershellgallery.com/packages/New-AwsVM/)
> 

1. Töltse le a PowerShell-parancsfájlt: New-AwsVM a PowerShell-galériából egy PowerShell-munkamenet megnyitásával és a következő parancs beírásával:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Az Azure Portalon nyissa meg az Automation-fiókot, és válassza a **Runbookok** lehetőséget a **Folyamatautomatizálás**csoportban.  
3. A Runbooks lapon válassza **a Runbook hozzáadása**lehetőséget.
4. A Runbook hozzáadása ablaktáblán válassza a **Gyors létrehozás** lehetőséget új runbook létrehozásához.
5. A Runbook tulajdonságai ablaktáblán írja be a runbook nevét.
6. A **Runbook típus** legördülő listájában válassza a **PowerShell**elemet, majd kattintson a **Létrehozás gombra.**<br><br> ![Runbook-ablaktábla létrehozása](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Amikor megjelenik a PowerShell Runbook-lap szerkesztéseke, másolja és illessze be a PowerShell-parancsfájlt a runbook-készítő vászonba.<br><br> ![Runbook PowerShell-parancsfájl](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Vegye figyelembe az alábbiakat, ha a példa PowerShell-parancsfájlhasználatával dolgozik:
    > 
    > * A runbook számos alapértelmezett paraméterértéket tartalmaz. Értékelje ki az összes alapértelmezett értéket, és szükség esetén frissítse.
    > * Ha az AWS hitelesítő adatait a hitelesítő adatoktól `AWScred`eltérően elnevezett hitelesítő elemként tárolta, frissítenie kell a parancsfájlt az 57-es sorban, hogy ennek megfelelően egyezzen.  
    > * Ha a PowerShell AWS CLI parancsaival dolgozik, különösen ebben a példa runbookban, meg kell adnia az AWS régiót. Ellenkező esetben a parancsmagok sikertelenek. További részletekért tekintse meg az AWS-témakört: Adja meg az [AWS-régiót](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) az AWS Tools for PowerShell-dokumentumban.  
    >

7. Ha le szeretné kérni a képnevek listáját az AWS-előfizetésből, indítsa el a PowerShell ISE-t, és importálja az AWS PowerShell-modult. Hitelesítse magát `Get-AutomationPSCredential` az AWS-en úgy, hogy az ISE-környezetben a.i `AWScred = Get-Credential` Ez az utasítás kéri a hitelesítő adatokat, és meg adhat a hozzáférési kulcs azonosítóját a felhasználónévhez és a titkos hozzáférési kulcsa a jelszóhoz. 

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
8. Másolja és illessze be az egyik képnevet egy Automation változóban, ahogy a runbookban `$InstanceType`hivatkozik. Mivel ebben a példában az ingyenes AWS rétegzett előfizetést használja, a **t2.micro-t** használja a runbook-példához.  
9. Mentse a runbookot, majd kattintson a **Közzététel gombra** a runbook közzétételéhez, majd az **Igen** gombra kattintva.

### <a name="testing-the-aws-vm-runbook"></a>Az AWS virtuális gép runbookjának tesztelése
A runbook tesztelésének folytatása előtt ellenőriznie kell néhány dolgot:

* Létrejött `AWScred` egy aws-sel való hitelesítésre kért eszköz, vagy a parancsfájl frissült, hogy hivatkozzon a hitelesítő adatok eszköznevére.    
* Az AWS PowerShell-modul importálása megtörtént az Azure Automationben.  
* Új runbook jött létre, és a paraméterértékek et ellenőrizték és szükség esetén frissítették.  
* **Részletes rekordok naplózása** és szükség esetén **a folyamatrekordok naplózása** **és nyomon követése** a runbook-művelet alatt **be**van kapcsolva.<br><br> ![Runbook naplózása](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)és nyomon követése .  

1. Kattintson a **Start** gombra a runbook elindításához, majd kattintson az **OK** gombra, amikor megnyílik a Runbook indítása ablaktábla.
2. A Runbook indítása ablaktáblán adja meg a virtuális gép nevét. Fogadja el a parancsfájlban előre konfigurált egyéb paraméterek alapértelmezett értékeit. Kattintson **az OK** gombra a runbook-feladat elindításához.<br><br> ![Új-AwsVM runbook indítása](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Meg van nyitva egy feladat ablaktábla a létrehozott runbook-feladathoz. Zárja be ezt az ablaktáblát.
4. Megtekintheti a feladat előrehaladását, és megtekintheti a kimeneti adatfolyamokat a runbook feladat ablaktáblájának **Minden napló** lehetőséget választva.<br><br> ![Adatfolyam kimenete](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Annak ellenőrzéséhez, hogy a virtuális gép kiépítése folyamatban van, jelentkezzen be az AWS felügyeleti konzolra, ha jelenleg nincs bejelentkezve.<br><br> ![Az AWS konzol telepített virtuális gép](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>További lépések
* A grafikus runbookok első lépései [Az Első grafikus runbook című témakörben](automation-first-runbook-graphical.md)látható.
* A PowerShell-munkafolyamat-runbookok első [lépései: Az első PowerShell-munkafolyamat-runbook.](automation-first-runbook-textual.md)
* Ha többet szeretne tudni a runbook-típusokról, azok előnyeiről és korlátairól, olvassa el az [Azure Automation runbook-típusok című témakört.](automation-runbook-types.md)
* A PowerShell-parancsfájlok támogatásával kapcsolatos további információkért tekintse meg [a Natív PowerShell-parancsfájl-támogatást az Azure Automationben.](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)