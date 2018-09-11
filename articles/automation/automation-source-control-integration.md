---
title: Verziókövetés integrálása az Azure Automationben
description: Ez a cikk ismerteti a forráskezelés integrálása a GitHub az Azure Automationben.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0b8f97103a892b0c9a3cb6f88298295a20caf302
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296396"
---
# <a name="source-control-integration-in-azure-automation"></a>Verziókövetés integrálása az Azure Automation szolgáltatásban
Verziókövetés integrációja lehetővé teszi runbookok a GitHub verziókövetési adattár, az Automation-fiók társításához. Verziókövetés együttműködhet a csapatával, követi a változásokat, és állítsa vissza a runbookok korábbi verzióira teszi lehetővé. Például verziókövetés lehetővé teszi, hogy a verziókövetés, a fejlesztési, tesztelési vagy éles Automation-fiókok, így könnyen előléptetni a kódot, amely a fejlesztési környezetben, az éles üzemben futó Automation tesztelve lett különböző ágak szinkronizálása fiók.

Verziókövetés lehetővé teszi leküldéses üzenet küldése kódot az Azure Automation a forráskezelőhöz, vagy kérje le a runbookok a forráskezelőből az Azure Automationhöz. Ez a cikk azt ismerteti, hogyan állítható be verziókövetés a környezetében az Azure Automation. Azure Automation és a GitHub-adattár eléréséhez, és különböző műveleteket is elvégezhető végig konfigurálásával kezdődik verziókövetés integrálása használatával. 

> [!NOTE]
> Verziókövetés támogatja lekérése és leküldése [PowerShell-munkafolyamati runbookok](automation-runbook-types.md#powershell-workflow-runbooks) , valamint [PowerShell-forgatókönyvek](automation-runbook-types.md#powershell-runbooks). [Grafikus runbookok](automation-runbook-types.md#graphical-runbooks) még nem támogatottak.<br><br>
> 
> 

Nincsenek két egyszerű lépést, az Automation-fiókját, és csak egy verziókövetési konfigurálásához, ha már rendelkezik egy GitHub-fiók szükséges. Ezek a következők:

## <a name="step-1--create-a-github-repository"></a>1. lépés – a GitHub-adattár létrehozása
Ha már rendelkezik egy GitHub-fiók és a egy Azure Automation mutató hivatkozás kívánt adattár, majd jelentkezzen be a meglévő fiókot, és indítsa el az alábbi 2. lépés. Ellenkező esetben lépjen [GitHub](https://github.com/), regisztrálhat egy új fiókot, és [hozzon létre egy új adattár](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>A 2 – az Azure Automationben a verziókövetés beállítása
1. Az Automation-fiók lap az Azure Portalon a **fiókbeállításokat**, kattintson a **verziókövetés.** 
   
1. A **verziókövetés** lap megnyitásakor, ahol konfigurálhatja a GitHub-fiók adatait. Alább látható a konfigurálandó paraméterek listája:  
   
   | **A paraméter** | **Leírás** |
   |:--- |:--- |
   | Forrás választása |Válassza ki a forrás. Jelenleg csak **GitHub** támogatott. |
   | Engedélyezés |Kattintson a **engedélyezés** gomb az Azure Automation hozzáférést a GitHub-tárban. Ha már jelentkezett, a GitHub-fiók egy másik ablakban a fiók hitelesítő adatait használja a rendszer. Sikeres engedélyezés után a lapon jelennek meg a GitHub-felhasználóneve alapján **engedélyezési tulajdonság**. |
   | Tárház kiválasztása |Válassza ki egy GitHub-tárházat a rendelkezésre álló tárházak listáját. |
   | Fiókiroda választása |Ágat kiválasztani és az elérhető ágak listáját. Csak a **fő** Ha még nem hozott létre összes ág jelenik meg. |
   | Runbook-mappa elérési útja |A forgatókönyvek mappájának elérési útja az elérési utat a GitHub-tárházban, ahonnan leküldéses, vagy kérje le a kódot adja meg. A következő formátumban kell megadni **/mappanév/subfoldername**. Csak a forgatókönyvek mappájának elérési útja a forgatókönyvek az Automation-fiókjába lesznek szinkronizálva. A forgatókönyvek mappájának elérési útja almappáiban lévő Runbookok fog **nem** szinkronizálható. Használat **/** szinkronizálás alatt a tárház minden runbookot. |
3. Például, ha rendelkezik a tárházhoz **PowerShellScripts** mappát tartalmazó **a gyökérmappa**, amely tartalmaz egy nevű mappát **almappa**. A következő karakterláncok használatával minden egyes mappaszinten szinkronizálása:
   
   1. A szinkronizálási runbookok **tárház**, runbook-mappa elérési útja */*
   2. A szinkronizálási runbookok **a gyökérmappa**, runbook-mappa elérési útja */RootFolder*
   3. A szinkronizálási runbookok **almappa**, runbook-mappa elérési útja */a gyökérmappa/almappa*.
4. Miután konfigurálta a paramétereket, jelennek meg a **Verziókövetés beállítása** lapot.  
   
    ![Forrás vezérlő lapjának konfigurálása](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)
5. Miután rákattint **OK**, verziókövetés integrálása lett konfigurálva az Automation-fiók és a GitHub-adatait frissíteni kell. Rákattinthat a erre a részre, mind a forrás vezérlő szinkronizálási feladat előzményeinek megtekintése.  
   
    ![Tárház-értékek](media/automation-source-control-integration/automation_03_RepoValues.png)
6. Verziókövetés beállítása után a következő Automation-erőforrások létrejön az Automation-fiók:  
   Két [változó adategységek](automation-variables.md) jönnek létre.  
   
   * A változó **Microsoft.Azure.Automation.SourceControl.Connection** a kapcsolati karakterlánc értékeit tartalmazza a lent látható módon.  
     
     | **A paraméter** | **Érték** |
     |:--- |:--- |
     | Name (Név) |Microsoft.Azure.Automation.SourceControl.Connection |
     | Típus |Sztring |
     | Érték |{"Ág":\<*az ág nevének*>, "RunbookFolderPath":\<*forgatókönyvek mappájának elérési útja*>, "Szolgáltatótípus":\<*1 értékkel rendelkezik GitHub*>, "Tárház":\<*a tárház nevét*>, "Felhasználónév":\<*a GitHub felhasználói név*>} |

    * A változó **Microsoft.Azure.Automation.SourceControl.OAuthToken**, az OAuthToken biztonságos titkosított értékét tartalmazza.  

    |**A paraméter**            |**Érték** |
    |:---|:---|
    | Name (Név)  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Típus | Unknown(Encrypted) |
    | Érték | <*Titkosított OAuthToken*> |  

    ![Változók](media/automation-source-control-integration/automation_04_Variables.png)  

    * **Automation Source-Control** kerülnek be a GitHub-fiók engedéllyel rendelkező alkalmazások. Az alkalmazás megtekintéséhez: a GitHub kezdőlapján nyissa meg a **profil** > **beállítások** > **alkalmazások**. Ez az alkalmazás lehetővé teszi, hogy az Azure Automation és a egy Automation-fiókot a GitHub-adattárban szinkronizálása.  

    ![Git-alkalmazás](media/automation-source-control-integration/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Verziókövetés Automation használatával
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Azure Automation a runbookot a forráskezelőhöz
Runbook-ellenőrzés lehetővé teszi a verziókövetési tárházat az Azure Automation-runbook rendelkezik végrehajtott módosításokat. A runbookot lépései a következők:

1. Az Automation-fiók [hozzon létre egy új szöveges runbookot](automation-first-runbook-textual.md), vagy [egy meglévő, szöveges alapú runbook szerkesztése](automation-edit-textual-runbook.md). Ez a forgatókönyv egy PowerShell-munkafolyamat vagy a PowerShell-parancsfájl runbook lehet.  
2. Miután szerkeszti a runbookban, mentse, majd kattintson a **bejelentkezési** a a **szerkesztése** lap.  
   
    ![A beadni gomb](media/automation-source-control-integration/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Bejelentkezés az Azure Automation felülírja a kódot, amely jelenleg megtalálható a verziókövetés. A Git egyenértékű parancssori utasítás, hogy az **adja hozzá a git és a git commit + git push**  

1. Amikor rákattint **bejelentkezési**, megjelenik egy megerősítő üzenetet, kattintson a **Igen** folytatja.  
   
    ![Beadási üzenet](media/automation-source-control-integration/automation_07_CheckinMessage.png)
2. Bejelentkezés elindítja a forrás-vezérlő runbook: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Ez a forgatókönyv a Githubhoz csatlakozhat, és leküldéses értesítések a módosításokat az Azure Automation a tárházhoz. A feladatelőzmények megtekintéséhez a az ellenőrzött, lépjen vissza a **verziókövetés integrálása** lapot, majd a tárház-szinkronizálás lap megnyitásához. Ez az oldal mutatja a verziókövetési feladatok.  Válassza ki a feladat megtekintéséhez, és kattintson a részletek megtekintéséhez.  
   
    ![A beadni Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > Forrás ellenőrzési forgatókönyvek olyan speciális Automation-runbookok, amelyek tudja megtekinteni vagy szerkeszteni. Amíg azok nem jelennek meg a runbook listáján, a feladatok listája megjelenítő szinkronizálási feladatokat láthatja.
   > 
   > 
3. Bemeneti paraméterként a beadott runbook zajlik a módosított runbook nevére. Is [a Feladatrészletek megtekintéséhez](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) runbook kibontásával **tárház-szinkronizálás** lapot.  
   
    ![A beadni bemenet](media/automation-source-control-integration/automation_09_CheckinInput.png)
4. Miután a feladat befejeződik, a módosítások megtekintéséhez, frissítse a GitHub-tárban.  Kell lennie egy véglegesítés az adattár egy véglegesítési üzenetet: **frissített *Runbook neve* az Azure Automationben.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Szinkronizálási runbookok a forráskezelőből az Azure Automationhöz
A tárház-szinkronizálás oldalon a szinkronizálás gomb lehetővé teszi a forgatókönyvek mappájának elérési útja a tárház minden runbookot lekérése az Automation-fiók. Ugyanabban az adattárban több Automation-fiókhoz is szinkronizálható. A runbookok szinkronizálása lépései a következők:

1. Nyissa meg az Automation-fiók hol állítható be verziókövetés a **verziókövetést integrációs/tárház** lapot, és kattintson **szinkronizálási**.  Megjelenik egy megerősítő üzenetet, kattintson a **Igen** folytatásához.  
   
    ![Szinkronizálás gomb](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
2. Szinkronizálási elindítja a runbookot: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Ez a forgatókönyv a Githubhoz csatlakozhat, és lekéri a módosításokat a tárházból az Azure Automationhöz. Új feladat megjelenik a **tárház-szinkronizálás** művelet lapot. A szinkronizálási feladat részleteinek megtekintéséhez kattintson a feladat részletei lap megnyitásához.  
   
    ![Szinkronizálási forgatókönyv](media/automation-source-control-integration/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > A verziókövetésből szinkronizálást felülírja a runbookokat, amelyek az Automation-fiókban a jelenleg létező vázlatverzióját **összes** runbookokat, amelyek jelenleg a verziókövetés. A Git egyenértékű parancssori utasítás szinkronizálni **git lekéréses**


## <a name="troubleshooting-source-control-problems"></a>Forrás-ellenőrzési problémák hibaelhárítása
Ha esetleges hibák be- vagy a szinkronizálási feladat, ezért célszerű felfüggeszteni a feladat állapotát, és megtekintheti a hibával kapcsolatos további részleteket a feladat oldalát.  A **az összes napló** része azt mutatja be, a feladathoz hozzárendelt összes PowerShell-adatfolyamot. Ezzel biztosítható az szükséges, javítsa ki az ellenőrzéssel a probléma vagy szinkronizálás részleteit. Bemutatja azt is, amely történt szinkronizálás, vagy egy runbook-ellenőrzési műveletek sorrendjét.  

![AllLogs kép](media/automation-source-control-integration/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>Verziókezelő leválasztása
Bontsa a kapcsolatot a GitHub-fiókot, nyissa meg a tárház-szinkronizálás lapját, és kattintson a **Disconnect**. Verziókezelő leválasztása, ha korábban szinkronizált runbookok továbbra is elérhetők az Automation-fiók, de a tárház-szinkronizálás lap nem lesz engedélyezve.  

  ![Kapcsolat bontása gomb](media/automation-source-control-integration/automation_12_Disconnect.png)

## <a name="next-steps"></a>További lépések
Forráskezelés integrálása kapcsolatos további információkért lásd a következőket:  

* [Az Azure Automation: Verziókövetés integrálása az Azure Automationben](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [A kedvenc forráskódú verziókezelő rendszer szavazzon](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Az Azure Automation: Azure DevOps használatával Runbook verziókövetés integrálása](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

