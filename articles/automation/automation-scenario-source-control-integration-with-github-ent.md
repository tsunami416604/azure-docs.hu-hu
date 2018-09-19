---
title: Az Azure Automation Forráskezelés integrálása a GitHub Enterprise-zal
description: A történő integráció konfigurálása a GitHub Enterprise-zal a verziókövetéshez, az Automation-runbookok részleteit ismerteti.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8c7dc256b92252793545336ffc45a987054a5509
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "35645522"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Az Azure Automation-forgatókönyv – Automation forráskezelés integrálása a GitHub Enterprise-zal

Automation jelenleg támogatja a verziókövetés integrációja, amely lehetővé teszi a runbookok a GitHub verziókövetési adattár, az Automation-fiók társításához. Azonban ügyfelek, akik telepítették [GitHub Enterprise-zal](https://enterprise.github.com/home) a fejlesztési és üzemeltetési eljárások támogatása érdekében is szeretne által fejlesztett felügyeleti műveleteket és üzleti folyamatok automatizálása runbookok életciklusának kezelésére használhatja.

Ebben a forgatókönyvben egy Windows-számítógép az adatközpontban egy hibrid Runbook-feldolgozó az Azure Resource Manager-modulokat és a Git-eszközök telepítve és konfigurálva van. A hibrid feldolgozó gép rendelkezik a helyi Git-adattár klónja. Ha a runbookot a hibrid feldolgozón fut, a Git directory szinkronizálva van, és a runbook tartalmát is importálja az Automation-fiókot.

Ez a cikk ismerteti, hogyan állítható be ezt a konfigurációt, az Azure Automation-környezetben. Indítsa el a biztonsági hitelesítő adatokkal, a runbookokat az adatközpontban a runbookok futtatásához és eléréséhez a GitHub Enterprise-adattár runbookok szinkronizálása ebben a forgatókönyvben, és a egy hibrid Runbook-feldolgozó üzembe támogatnia kell az Automation konfigurálása az Automation-fiókját.

## <a name="getting-the-scenario"></a>A forgatókönyv beszerzése

Ebben a forgatókönyvben két PowerShell-forgatókönyvek, amelyet közvetlenül importálhat áll a [forgatókönyv-katalógusában](automation-runbook-gallery.md) az Azure Portalon, vagy letölthető a [PowerShell-galériából](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbookok

Forgatókönyv | Leírás|
--------|------------|
Export-RunAsCertificateToHybridWorker | Runbook exportálása futtató tanúsítvány egy Automation-fiók egy hibrid feldolgozó, hogy a feldolgozó runbookjainak hitelesíthető az Azure-ral, annak érdekében, hogy a runbookok importálja az Automation-fiókot.|
Sync-LocalGitFolderToAutomationAccount | Runbook szinkronizálja a helyi Git mappába, a hibrid gépen, és a runbook-fájlok (*.ps1) majd importálja az Automation-fiókot.|

### <a name="credentials"></a>Hitelesítő adatok

Hitelesítő adat | Leírás|
-----------|------------|
GitHRWCredential | Hitelesítőadat-eszköz hoz létre tartalmazzák a felhasználónevet és jelszót a hibrid feldolgozó engedéllyel rendelkező felhasználó számára.|

## <a name="installing-and-configuring-this-scenario"></a>A forgatókönyv telepítése és konfigurálása

### <a name="prerequisites"></a>Előfeltételek

1. A Sync-LocalGitFolderToAutomationAccount runbook használatával végez hitelesítést az [Azure-beli futtató fiók](automation-sec-configure-azure-runas-account.md).

2. Az Azure Automation-megoldás engedélyezni és konfigurálni a Log Analytics-munkaterület is szükség. Ha nem rendelkezik, amely az Automation-fiók telepítése és konfigurálása ebben a forgatókönyvben használt van társítva, az azt létrehozta és konfigurálta az Ön számára végrehajtásakor a **New-OnPremiseHybridWorker.ps1** parancsfájlt a hibrid runbook feldolgozó.

    > [!NOTE]
    > Jelenleg a következő régiókban csak támogatja a Log Analytics - integráció Automation **Délkelet-Ausztrália**, **USA keleti RÉGIÓJA 2**, **Délkelet-Ázsia**, és  **Nyugat-Európa**.

3. Egy számítógép, amely egy dedikált hibrid Runbook-feldolgozó a GitHub-szoftvert üzemeltető szolgál és karbantartása a runbook-fájlok (*runbook*.ps1) a GitHub és az Automation közötti szinkronizálását a fájlrendszer egy forrás könyvtár fiók.

### <a name="import-and-publish-the-runbooks"></a>Importálása és közzététele a runbookok

Importálása a *Export-RunAsCertificateToHybridWorker* és *Sync-LocalGitFolderToAutomationAccount* runbookok a Runbook-galériából, az Automation-fiók az Azure Portalon, kövesse a az eljárások [importálás Runbookot a Runbook-katalógus](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Tegye közzé a runbookok, miután azok sikeresen importálta az Automation-fiók.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Telepítse és konfigurálja a hibrid Runbook-feldolgozó

Ha nem rendelkezik egy hibrid Runbook-feldolgozó már üzembe helyezte az adatközpontban, kell tekintse át a követelményeket és kövesse az automatikus telepítési lépéseket hajtsa végre az eljárást az Azure Automation hibrid Runbook-feldolgozók – automatizálása telepítése és konfigurációja [Windows](automation-windows-hrw-install.md#automated-deployment) vagy [Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker). Miután sikeresen telepítette a hibrid feldolgozó egy számítógépen, hajtsa végre az alábbi lépéseket a forgatókönyv támogatása érdekében a konfigurálás befejezéséhez.

1. Jelentkezzen be egy olyan fiókkal, amely helyi rendszergazdai jogosultságokkal rendelkezik a hibrid Runbook-feldolgozó szerepkört futtató számítógépen, és hozzon létre egy könyvtárat a Git runbook fájlok tárolásához. Azt a könyvtárat a belső Git-tárház klónozásához.
1. Nem már rendelkezik egy létrehozott futtató fiókot, vagy hozzon létre egy új egy dedikált erre a célra szeretne, ha az Azure Portalon lépjen az Automation-fiókok, válassza ki az Automation-fiók, és hozzon létre egy [hitelesítőeszközt](automation-credentials.md) , tartalmazza a felhasználónevet és jelszót a hibrid feldolgozó engedéllyel rendelkező felhasználó számára.
1. Az Automation-fiók [szerkeszteni a runbookot](automation-edit-textual-runbook.md)**Export-RunAsCertificateToHybridWorker** és módosíthatja a változó értékét *$Password* erős jelszóval.    Miután az érték módosításához kattintson **közzététel** szeretné, hogy a közzétett runbook vázlatverzióját.
1. A runbook elindításához **Export-RunAsCertificateToHybridWorker**, majd a a **Runbook indítása** panelen lehetőség alatt **futtatási beállítások** választja  **Hibrid feldolgozó** és a legördülő listában válassza ki a korábban ehhez a forgatókönyvhöz létrehozott hibrid feldolgozócsoport.

    Ez exportálja a tanúsítványt a hibrid feldolgozó, hogy a feldolgozó használatával a runbookok hitelesítést az Azure-kapcsolattal futtató (különösen a forgatókönyv - importálási runbookokat az Automation-fiók) Azure-erőforrások kezeléséhez.

1. Az Automation-fiók, jelölje ki a korábban létrehozott hibrid feldolgozócsoport és [megadhat egy futtató fiókot](automation-hrw-run-runbooks.md#runas-account) a hibrid feldolgozócsoport, és válassza a hitelesítőadat-eszköz csak, vagy már hozott létre. Ez biztosítja, hogy a szinkronizálási runbook futtatható-e a Git-parancsokat. 
1. A runbook elindításához **Sync-LocalGitFolderToAutomationAccount**, adja meg a következő szükséges bemeneti paraméter értékét, és a a **Runbook indítása** panelen lehetőség alatt **futtatási beállítások**  választja **hibrid feldolgozó** és a legördülő listában válassza ki a korábban ehhez a forgatókönyvhöz létrehozott hibrid feldolgozócsoport:

   * *Erőforráscsoport* – az Automation-fiókhoz társított az erőforráscsoport nevét
   * *AutomationAccountName* – az Automation-fiók neve
   * *GitPath* – a helyi mappát vagy fájlt, ahol Git be van állítva lekérni a legfrissebb változásokat a hibrid Runbook-feldolgozón

    Ez szinkronizálja a helyi Git mappába, a hibrid feldolgozói számítógépen, és ezután importálja a .ps1 fájlokat a forráskönyvtár az Automation-fiókot.

1. Induljon el a runbook a feladat összefoglaló információk megtekintése a **Runbookok** panel az Automation-fiókot, és válassza ki a **feladatok** csempére. Ellenőrizze, hogy sikeresen befejeződött-e választva a **az összes napló** csempét, és a napló részletes stream áttekintése.

## <a name="next-steps"></a>További lépések

* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
