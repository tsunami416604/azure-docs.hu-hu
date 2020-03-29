---
title: Forrásvezérlésintegráció az Azure Automationben – Örökölt
description: Ez a cikk ismerteti a forrásvezérlés integrációja a GitHub az Azure Automationben.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 651b97dabfd3cce858ea1f905a39c10bd7d81c41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417442"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Forrásvezérlésintegráció az Azure Automationben – Legacy

> [!NOTE]
> Új felület áll a forrásellenőrzésre. Az új élményről a [Forrásvezérlés (előzetes verzió)](source-control-integration.md)című témakörben olvashat bővebben.

A forrásvezérlésintegráció lehetővé teszi, hogy az Automation-fiókban lévő runbookokat egy GitHub-forrásvezérlő tárházhoz társítsa. A forrásvezérlés lehetővé teszi, hogy könnyedén együttműködjön a csapatával, nyomon követhesse a változásokat, és visszaváltson a runbookok korábbi verzióira. A forrásvezérlés például lehetővé teszi, hogy a forrásvezérlés különböző ágait szinkronizálja a fejlesztési, tesztelési vagy éles automatizálási fiókokhoz, így egyszerűen előléptetheti a fejlesztői környezetben tesztelt kódot a termelési automatizálásra Számla.

A forrásvezérlés lehetővé teszi, hogy kódot nyomtasson az Azure Automationből a forrásvezérléshez, vagy lekéri a runbookokat a forrásvezérlésből az Azure Automationbe. Ez a cikk ismerteti, hogyan állíthatja be a forrásvezérlést az Azure Automation-környezetben. Azzal kezdjük, hogy konfiguráljuk az Azure Automationt a GitHub-tárház eléréséhez, és végigvezeti a különböző műveleteket, amelyek a forrásvezérlési integráció használatával elvégezhetők. 

> [!NOTE]
> A forrásvezérlés támogatja a [PowerShell-munkafolyamat runbookjainak,](automation-runbook-types.md#powershell-workflow-runbooks) valamint a [PowerShell runbookjainak lehúzását](automation-runbook-types.md#powershell-runbooks)és lenyomását. [A grafikus runbookok](automation-runbook-types.md#graphical-runbooks) még nem támogatottak.

Két egyszerű lépés szükséges az Automation-fiók forrásvezérlésének konfigurálásához, és csak egy, ha már rendelkezik Egy GitHub-fiókkal. Ezek a következők:

## <a name="step-1--create-a-github-repository"></a>1. lépés – GitHub-tárház létrehozása

Ha már rendelkezik egy GitHub-fiókkal és egy tárházzal, amelyet az Azure Automationhez szeretne kapcsolni, majd jelentkezzen be a meglévő fiókjába, és kezdje az alábbi 2. Ellenkező esetben keresse meg a [GitHubot,](https://github.com/)regisztráljon egy új fiókot, és [hozzon létre egy új tárházat.](https://help.github.com/articles/create-a-repo/)

## <a name="step-2--set-up-source-control-in-azure-automation"></a>2. lépés – Forrásvezérlés beállítása az Azure Automationben

1. Az Azure Portal Automation-fiók lapján a **Fiókbeállítások**csoportban kattintson a **Forrásvezérlés elemre.**

2. Megnyílik **a Forrásvezérlő** lap, ahol konfigurálhatja a GitHub-fiók adatait. Alább látható a konfigurálandó paraméterek listája:  

   | **Paraméter** | **Leírás** |
   |:--- |:--- |
   | Forrás kiválasztása |Jelölje ki a forrást. Jelenleg csak a **GitHub** támogatott. |
   | Engedélyezés |Kattintson az **Engedélyezés** gombra, ha hozzáférést biztosít az Azure Automation-nek a GitHub-tárházhoz. Ha már be van jelentkezve a GitHub-fiókjába egy másik ablakban, majd a fiók hitelesítő adatait használja. Ha az engedélyezés sikeres, a lap a GitHub-felhasználónevét az **Engedélyezési tulajdonság alatt**jeleníti meg. |
   | Tárház kiválasztása |Válasszon ki egy GitHub-tárházat az elérhető tárházak listájából. |
   | Ág kiválasztása |Válasszon ki egy ágat az elérhető ágak listájából. Csak a **főág** jelenik meg, ha még nem hozott létre ágakat. |
   | Runbook mappa elérési útja |A runbook mappa elérési útja megadja az elérési utat a GitHub-tárházban, amelyből le szeretné nyomni vagy le szeretné húzni a kódot. A **/foldername/subfoldername**formátumban kell megadni. Csak a runbook mappa elérési útján lévő runbookok lesznek szinkronizálva az Automation-fiókkal. A runbook mappa elérési útjának almappáiban lévő runbookok **nem** lesznek szinkronizálva. A **/** tárház alatti összes runbook szinkronizálására használható. |
3. Ha például van egy **PowerShellScripts** nevű tárháza, amely egy **Gyökérmappa**nevű mappát tartalmaz, amely egy **Almappa**nevű mappát tartalmaz. Az egyes mappaszinteket a következő karakterláncokkal szinkronizálhatja:

   1. A runbookok **tárházból**való szinkronizálásához a runbook-mappa elérési útja*/*
   2. Runbookok **RootFolder**mappából történő szinkronizálásához a runbook mappa elérési útja */RootFolder*
   3. A runbookok **Almappából**történő szinkronizálásához a runbook mappa elérési útja *a /RootFolder/AlFolder*.
4. A paraméterek konfigurálása után azok a **Forrásvezérlő beállítása** lapon jelennek meg.  

    ![A Beállításokat megjelenítő Forrásvezérlő lap](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Miután az **OK**gombra kattintott, a forrásvezérlési integráció most már konfigurálva van az Automation-fiókhoz, és frissíteni kell a GitHub-adatokkal. Most már erre a részre kattintva megtekintheti az összes forrásvezérlő szinkronizálási feladatelőzményét.  

    ![Az aktuálisan konfigurált forrásvezérlő konfigurációjának értékei](media/source-control-integration-legacy/automation-RepoValues.png)
6. A forrásellenőrzés beállítása után két [változóeszköz](automation-variables.md) jön létre az Automation-fiókban. Emellett egy engedélyezett alkalmazás hozzáadódik a GitHub-fiókhoz.

   * A **Microsoft.Azure.Automation.SourceControl.Connection** változó a kapcsolati karakterlánc értékeit tartalmazza, az alábbiak szerint.  

     | **Paraméter** | **Érték** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |Sztring |
     | `Value` |{"Ág":\<*Az ág neve*>,"RunbookFolderPath":\<*Runbook mappa elérési útja*>,"ProviderType":\<*1-es értékkel rendelkezik a GitHub*>,"Repository":\<A*tárház neve*>,"Felhasználónév":\<A*GitHub felhasználói neve*>} |

   * A **Microsoft.Azure.Automation.SourceControl.OAuthToken**változó tartalmazza az OAuthToken biztonságos titkosított értékét.  

     |**Paraméter**            |**Érték** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
     | `Type`  | Ismeretlen (titkosított) |
     | `Value` | <*Titkosított OAuthToken*> |  

     ![Forrásvezérlő változókkal rendelkező ablak](media/source-control-integration-legacy/automation-Variables.png)  

   * **Automation Source Control** hozzá adódik egy engedélyezett alkalmazás a GitHub-fiókhoz. Az alkalmazás megtekintéséhez: A GitHub kezdőlapján keresse meg a **profil** > **beállítások** > **alkalmazások**. Ez az alkalmazás lehetővé teszi, hogy az Azure Automation szinkronizálja a GitHub-tárházat egy Automation-fiókkal.  

     ![Alkalmazásbeállítások a GitHubon](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>A forrásvezérlés használata az automatizálásban

### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Runbook beadása az Azure Automationből a forrásvezérlőbe

A Runbook-beadás lehetővé teszi, hogy az Azure Automationben egy runbookon végrehajtott módosításokat a forrásvezérlő tárházba tolja le. Az alábbiakban a runbook okán kell beadni a lépéseket:

1. Az Automation-fiókból [hozzon létre egy új szöveges runbookot,](automation-first-runbook-textual.md)vagy [szerkesztsen egy meglévő, szöveges runbookot.](automation-edit-textual-runbook.md) Ez a runbook lehet egy PowerShell-munkafolyamat vagy egy PowerShell-parancsfájl runbook.  
2. A runbook szerkesztése után mentse, és kattintson a **Beadás** **gombra** a Szerkesztés lapon.  

    ![A beadás a GitHubra gombbal megjelenítő ablak](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Az Azure Automation-ből történő bejelentkezés felülírja a forrásvezérlőben jelenleg létező kódot. A Git egyenértékű parancssori utasítás a check-in **a git add + git commit + git push**  

3. Amikor **a beadásgombra**kattint, a program megerősítő üzenetet kér, a folytatáshoz kattintson az **Igen** gombra.  

    ![A forrásvezérlőbe való befizetést megerősítő párbeszédpanel](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. A beadás elindítja a forrásvezérlő runbookját: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Ez a runbook csatlakozik a GitHubhoz, és leküldéses módosításokat az Azure Automation a tárház. A beadott feladatelőzmények megtekintéséhez lépjen vissza a **Forrásvezérlés integrációja** lapra, és kattintson ide a Tártár szinkronizálása lap megnyitásához. Ezen a lapon az összes forrásvezérlő feladat látható.  Jelölje ki a megtekinteni kívánt feladatot, és kattintson a részletek megtekintéséhez.  

    ![Szinkronizálási feladat eredményeit megjelenítő ablak](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > A forrásvezérlő runbookok speciális Automation runbookok, amelyeket nem tekinthet meg és nem szerkeszthet. Bár nem jelennek meg a runbook-listán, a feladatok szinkronizálási listájában megjelennek a szinkronizálási feladatok.

5. A módosított runbook nevét a rendszer a beadott runbook bemeneti paramétereként küldi el. A [feladat részleteit a](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) Runbook kibontásával tekintheti meg a **Tárház szinkronizálása** lapon.  

    ![Egy szinkronizálási feladat bemenetét megjelenítő ablak](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Frissítse a GitHub-tárházat, amint a feladat befejeződik a módosítások megtekintéséhez.  A tárházban véglegesítési üzenettel kell elkövetni: **Frissített *Runbook-név* az Azure Automationben.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Runbookok szinkronizálása a forrásvezérlőtől az Azure Automationig

A szinkronizálás gomb a repository szinkronizálási lapon lehetővé teszi, hogy lekéri az összes runbookok a tárház elérési útja a tárház az Automation-fiók. Ugyanaz a tárház több Automation-fiókkal is szinkronizálható. Az alábbiakban a runbookok szinkronizálásának lépéseit olvashatja:

1. Az Automation-fiókból, amelybe forrásvezérlőt állított be, nyissa meg a **Forrásvezérlés integrációs/adattár-szinkronizálási lapját,** és kattintson a **Szinkronizálás gombra.**  A folytatáshoz a rendszer megerősítő üzenetet küld, és kattintson az **Igen** gombra.  

    ![Szinkronizálás gomb üzenettel, amely megerősíti, hogy az összes runbook szinkronizálva lesz](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. A Szinkronizálás elindítja a runbookot: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Ez a runbook csatlakozik a GitHubhoz, és lekéri a módosításokat a tárházból az Azure Automationbe. Ekkor megjelenik egy új feladat a **tárház szinkronizálása** lapon ehhez a művelethez. A szinkronizálási feladat részleteinek megtekintéséhez kattintson ide a feladat részletei lap megnyitásához.  

    ![Egy szinkronizálási feladat szinkronizálási eredményeit megjelenítő ablak egy GitHub-tárházban](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > A forrásvezérlőből származó szinkronizálás felülírja a runbookok vázlatverzióját, amely jelenleg az Automation-fiókban létezik a forrásvezérlésben lévő **összes** runbookhoz. A Git egyenértékű parancssori utasítás a **git pull**

![Felfüggesztett forrásvezérlő szinkronizálási feladat összes naplóját megjelenítő ablak](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Forrásvezérlő leválasztása

Ha le szeretne választani a GitHub-fiókról, nyissa meg a Tárház szinkronizálása lapot, és kattintson a **Kapcsolat bontása**gombra. A forrásvezérlő leválasztása után a korábban szinkronizált runbookok továbbra is az Automation-fiókban maradnak, de a Tárház szinkronizálási lapja nem lesz engedélyezve.  

  ![A forrásvezérlő leválasztásához a Kapcsolat bontása gombbal](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>További lépések

A forrásvezérlés integrációjáról az alábbi forrásokban talál további információt:  

* [Azure Automation: Forrásvezérlésintegráció az Azure Automationben](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Azure Automation: A Runbook-forrásvezérlés integrálása az Azure DevOps használatával](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
