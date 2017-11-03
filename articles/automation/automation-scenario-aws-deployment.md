---
title: "A virtuális gépek az Amazon Web Services telepítés automatizálásáról |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan használható az Azure Automation automatizálhatja az Amazon Web Service virtuális gép létrehozása"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 1d85c01a-d795-4523-8194-84fc15b53838
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: tiandert; bwren
ms.openlocfilehash: 828f9e2cc9a39e54933cd0e0db7273efa460d0c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation forgatókönyv - kiépítés az AWS virtuális gép
Ebben a témakörben bemutatjuk, hogyan használhatják az Azure Automation-rendszerű virtuális gép az Amazon Web Service (AWS) előfizetésben, és nevezze el ezt a virtuális Gépet egy adott – ami AWS utal, mint a "címkézés" a virtuális gép.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk alkalmazásában kell egy Azure Automation-fiók és az AWS előfizetéssel rendelkezik. További információ az Azure Automation-fiók beállításával, és konfigurálni kell az AWS-előfizetés hitelesítő adatait, tekintse át a [hitelesítés beállítása az Amazon Web Services](automation-config-aws-account.md).  Ezt a fiókot kell hozható létre vagy frissül az AWS előfizetés hitelesítő adatait, mielőtt továbblépne, azt fogja hivatkozási ehhez a fiókhoz az alábbi lépéseket.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell-modul telepítése
A Virtuálisgép-létrehozásnál runbook fogja használni, az AWS PowerShell-modul a munkájuk elvégzéséhez. Hajtsa végre a következő lépések végrehajtásával adja hozzá a modul az Automation-fiók, amelynek része az AWS-előfizetés hitelesítő adatait.  

1. Nyissa meg a webböngészőt, és keresse meg a [PowerShell-galériában](http://www.powershellgallery.com/packages/AWSPowerShell/) , majd kattintson a a **az Azure Automation gomb telepítéséhez**.<br><br> ![AWS PS modul importálása](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. A következő lépés az Azure bejelentkezési lapra és a hitelesítés után, akkor a rendszer átirányítja az Azure-portálon, majd jelenik meg a következő oldalon.<br><br> ![A modul lap importálása](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Válassza ki az erőforráscsoport a **erőforráscsoport** legördülő listában, és a paraméterek panelen adja meg a következő információkat:
   
   * Az a **új vagy meglévő Automation-fiók (karakterlánc)** legördülő listában válassza ki **meglévő**.  
   * Az a **Automation-fiók neve (karakterlánc)** írja be a pontos nevében az Automation-fiók, amely tartalmazza az AWS-előfizetés hitelesítő adatait.  Például, ha létrehozott egy külön fiókot nevű **AWSAutomation**, akkor az írja a mezőbe.
   * Válassza ki a megfelelő régió a **Automation-fiók helyével** legördülő listából.
4. Ha befejezte, írja be a szükséges adatokat, kattintson a **létrehozása**.
   
   > [!NOTE]
   > A PowerShell modul importálása az Azure Automation, akkor van is kibontása a parancsmagok, és ezek a tevékenységek nem jelennek meg, amíg a modul importálása, és a parancsmagok kinyeréséhez teljesen befejeződött. A folyamat eltarthat néhány percig.  
   > <br>
   > 
   > 
5. Nyissa meg a 3. lépésben hivatkozott az Automation-fiók az Azure-portálon.
6. Kattintson a a **eszközök** csempe és az a **eszközök** ablaktáblán válassza előbb a **modulok** csempére.
7. Az a **modulok** lapon látni fogja a **AWSPowerShell** modul a listában.

## <a name="create-aws-deploy-vm-runbook"></a>Hozzon létre AWS VM runbook központi telepítése
Az AWS PowerShell-modul telepítve lett, ha azt egy runbook automatizálása a PowerShell parancsfájl használatával AWS a virtuális gépek kiépítése most hozhat létre. Az alábbi lépéseket mutatni, hogyan használhatók ki az Azure Automationben natív PowerShell-parancsfájlt.  

> [!NOTE]
> További beállítások és a parancsfájl vonatkozó információkat, olvassa el a [PowerShell-galériában](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. A PowerShell parancsfájl New-AwsVM letöltése a PowerShell-galériában nyisson meg egy PowerShell-munkamenetet, és írja be a következő:<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Azure-portálról, nyissa meg az Automation-fiók, és válassza ki **Runbookok** szakaszban **folyamat** a bal oldalon.  
3. Az a **Runbookok** lapon jelölje be **hozzáadása egy runbook**.
4. Az a **hozzáadása egy runbook** ablaktáblán válassza előbb **Gyorslétrehozás** (hozzon létre egy új runbookot).
5. A a **Runbook** tulajdonságok panelen, írja be a runbook és a név mezőben az nevét a **runbooktípusba** legördülő listában válassza ki **PowerShell**, és kattintson a **Létrehozása**.<br><br> ![Runbook ablaktábla létrehozása](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. A PowerShell Runbook szerkesztése lap jelenik meg, amikor másolja és illessze be a PowerShell parancsfájl vászonra szerzői a runbook.<br><br> ![Runbook PowerShell-parancsfájl](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > A példa PowerShell-parancsfájlt az használatakor vegye figyelembe a következőket:
    > 
    > * A runbook tartalmaz egy alapértelmezett paraméterértékek száma. Az összes alapértelmezett értékeit értékelheti ki, és szükség esetén frissítse.
    > * Ha a tárolt AWS hitelesítő adatait, mint más néven hitelesítőadat-eszköz **AWScred**, a parancsfájl sor 57 egyező ennek megfelelően frissíteni kell.  
    > * Ez a példa runbook, különösen a PowerShell, a AWS parancssori felület parancsai az használatakor meg kell adnia az AWS régióban. Ellenkező esetben a parancsmagok sikertelen lesz.  Nézet AWS témakör [meg AWS régió](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) PowerShell dokumentum további részleteket az AWS eszközei.  
    >

7. Az AWS előfizetés lekérdezni kép neveinek listáját, indítsa el a PowerShell ISE, és importálja az AWS PowerShell modult.  Azáltal, hogy az AWS hitelesítése **Get-AutomationPSCredential** a ISE környezetében lévő **AWScred = Get-Credential**.  Ez kérni fogja a hitelesítő adatokat, és megadhatja a **hozzáférési kulcs azonosító** a felhasználónévhez és **titkos hívóbetű** a jelszót.  Lásd az alábbi példát:  

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

    A következő kimenetet visszaadja:<br><br>
   ![AWS képek beolvasása](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Másolja és illessze be a képek nevének közül egy automatizálási változó a runbookot, amint a **$InstanceType**. Mivel ebben a példában vagy folyamatban van a szabad AWS használatával rétegzett előfizetés, fogjuk használni **t2.micro** runbook példa.  
9. Mentse el a runbookot, majd kattintson az **közzététel** közzétenni a runbookot, majd **Igen** megjelenésekor.

### <a name="testing-the-aws-vm-runbook"></a>Az AWS VM runbook tesztelése
A runbook tesztelése a folytatás előtt igazolnia kell néhány dolgot. Konkrétan:  

* Az eszköz az AWS hitelesítést hívott létrejött **AWScred** vagy a parancsfájl nevét a hitelesítőadat-eszköz hivatkozhat környezetébe.    
* Az AWS PowerShell modul importálva van az Azure Automationben  
* Létrejött egy új runbookot, és a paraméterértékek ellenőrzése és frissítése, amennyiben szükséges  
* **Részletes rekordok naplózására** , illetve opcionálisan **naplózza az állapotrekordokat** a runbook beállítás alatt **naplózás és nyomkövetés** állították be, hogy **a**.<br><br> ![Runbook-naplózás és nyomkövetés](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Azt szeretnénk, hogy a elindítja a runbookot, kattintson a **Start** , majd **OK** a runbook meghívása ablak megnyitásakor.
2. A runbook meghívása panelen adjon meg egy **VMname**.  Fogadja el a többi paraméter, amely korábban előre a parancsfájl az alapértelmezett értékeit.  Kattintson a **OK** a runbook feladatának elindításához.<br><br> ![Új-AwsVM runbook elindítása](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Megnyílik egy feladatpanel az imént létrehozott forgatókönyv-feladathoz. Zárja be az ezen az ablaktáblán.
4. Azt megtekintheti a feladatot és tekintse meg a kimeneti előrehaladását **adatfolyamok** kiválasztásával a **összes napló** csempére a runbook-feladat oldalról.<br><br> ![Adatfolyam-kimenetét](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Annak ellenőrzéséhez, hogy a virtuális gép telepítése folyamatban van, jelentkezzen be az AWS felügyeleti konzolon, ha jelenleg nem jelentkezik.<br><br> ![AWS konzol telepített virtuális gép](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Következő lépések
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

