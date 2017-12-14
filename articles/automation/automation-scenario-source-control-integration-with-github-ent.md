---
title: "Verziókövetés integrálása az Azure Automation a Githubon vállalati |} Microsoft Docs"
description: "A GitHub vállalati integrációs konfigurálása az Automation-forgatókönyv verziókövetési részleteit ismerteti."
services: automation
documentationCenter: 
authors: georgewallace
manager: jwhit
editor: 
ms.assetid: e01d817c-7d38-421c-adf5-647a4b526eb4
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: magoedte
ms.openlocfilehash: cf72c6d05e2872bea84b8a7218bd318d5b8c9694
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Azure Automation forgatókönyv - automatizálási verziókövetés integrálása a Githubon vállalati

Automatizálási jelenleg verziókövetés integrálása, amely lehetővé teszi az Automation-fiók egy GitHub verziókövetési tárházat a runbookot.  Azonban az ügyfelek, akik telepített [GitHub vállalati](https://enterprise.github.com/home) a DevOps eljárások támogatásához is szeretne az üzleti folyamatok automatizálása és felügyeleti műveletek szolgáltatás fejlesztett runbookok életciklus kezeléséhez.  

Ebben a forgatókönyvben a Windows rendszerű számítógépeken az Adatközpont konfigurálva, a hibrid forgatókönyv-feldolgozók az Azure Resource Manager modulok és a Git-eszközök telepítve van.  A hibrid feldolgozó gépnek klónját, a helyi Git-tárházba.  A runbook futásakor a hibrid feldolgozón a Git directory szinkronizálása és a runbook tartalmát a rendszer importálta az Automation-fiók.

A cikkből megtudhatja, hogyan állíthat be ezt a konfigurációt, az Azure Automation-környezetben. A biztonsági hitelesítő adataival, a runbookok a runbookok futtatásához és hozzáférni a vállalati GitHub-tárház runbookok szinkronizálása az Adatközpont ezt a forgatókönyvet, és a hibrid forgatókönyv-feldolgozók telepítését támogatásához szükséges Automation konfigurálásával először az Automation-fiókkal.  


## <a name="getting-the-scenario"></a>A forgatókönyv beszerzése

Ebben a forgatókönyvben két PowerShell-forgatókönyvek, amelyek közvetlenül importálhatók áll a [forgatókönyvek](automation-runbook-gallery.md) az Azure-portálon vagy letölthető a [PowerShell-galériában](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbookok

Forgatókönyv | Leírás| 
--------|------------|
Export-RunAsCertificateToHybridWorker | Runbook exportálása RunAs tanúsítványt az Automation-fiók a hibrid feldolgozók, hogy a dolgozó runbookokat is annak érdekében hitelesítik magukat Azure runbookok importálja az Automation-fiók.| 
Szinkronizálási-LocalGitFolderToAutomationAccount | Runbook szinkronizálja a helyi Git-mappa a hibrid gépen, és ezután a runbook-fájlok (*.ps1) importálása az Automation-fiók.|

### <a name="credentials"></a>Hitelesítő adatok

Hitelesítő adat | Leírás|
-----------|------------|
GitHRWCredential | Hitelesítőadat-eszköz hoz létre a felhasználónév és a hibrid feldolgozó engedélyekkel rendelkező felhasználó jelszavának tárolásához.|

## <a name="installing-and-configuring-this-scenario"></a>A forgatókönyv telepítése és konfigurálása

### <a name="prerequisites"></a>Előfeltételek

1. A Sync-LocalGitFolderToAutomationAccount runbook használatával hitelesít a [Azure-beli futtató fiók](automation-sec-configure-azure-runas-account.md). 

2. Az Azure Automation-megoldás engedélyezve és konfigurálva van a Microsoft Operations Management Suite (OMS) munkaterület is szükség.  Ha még nem rendelkezik ilyennel, amelyek telepítése és konfigurálása ebben a forgatókönyvben használt Automation-fiók van hozzárendelve, az azt létre és konfigurálta az Ön végrehajtásakor a **New-OnPremiseHybridWorker.ps1** parancsfájlt a hibrid forgatókönyv munkavégző.        

    > [!NOTE]
    > Jelenleg a következő régiókban csak támogatja OMS - automatizálás integrálását **Ausztrália délkeleti**, **USA keleti régiója 2**, **Délkelet-Ázsia**, és **nyugati régiója Európa**. 

3. Olyan számítógépre, amelyen a dedikált hibrid forgatókönyv-feldolgozók, amely szintén a Githubon szoftver a runbook fájlok karbantartása szolgálhatnak (*runbook*.ps1) a GitHub közötti szinkronizálás a fájlrendszerben forráskönyvtárat és a Automation-fiók.

### <a name="import-and-publish-the-runbooks"></a>Importálja és közzétenni a runbookot

Importálhatja a *Export-RunAsCertificateToHybridWorker* és *Sync-LocalGitFolderToAutomationAccount* forgatókönyvek az Automation-fiók az Azure portálon, a Runbook-galériából kövesse a az eljárások [importálási Runbookot a Runbook-galériából](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Tegye közzé a runbookokat, után azok sikeresen importálva lettek az Automation-fiók.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Telepítse és konfigurálja a hibrid forgatókönyv-feldolgozó

Ha nem rendelkezik a hibrid forgatókönyv-feldolgozók már telepítették az adatközpont, kell tekintse át a követelményeket és kövesse az automatikus telepítési lépéseket hajtsa végre az eljárást a [Azure Automation hibrid forgatókönyv-feldolgozók - telepítés automatizálása és Konfigurációs](automation-hybrid-runbook-worker.md#automated-deployment).  Miután sikeresen telepítette a hibrid feldolgozó egy számítógépen, a következő lépésekkel ennek támogatásához a konfigurálás befejezéséhez.

1. Jelentkezzen be egy olyan fiókkal, amely helyi rendszergazdai jogosultságokkal rendelkezik a hibrid forgatókönyv-feldolgozó szerepkört futtató számítógépen, és hozzon létre egy könyvtárat a Git runbook fájlok tárolásához.  A könyvtár a belső Git-tárház klónozása.
2. Ha még nem rendelkezik egy futtató fiókot, vagy hozzon létre egy új dedikált egy erre a célra, Azure-portálról navigáljon az Automation-fiók válassza ki az Automation-fiók, hozzon létre egy [hitelesítőadat-eszköz](automation-credentials.md) , a felhasználónév és a hibrid feldolgozó engedélyekkel rendelkező felhasználó jelszavának tartalmazza.  
3. Az Automation-fiók a [szerkeszteni a runbookot](automation-edit-textual-runbook.md)**Export-RunAsCertificateToHybridWorker** és a változó értékének módosításához *$Password* erős jelszóval.  Az érték módosítása után kattintson **közzététel** kell rendelkeznie a közzétett runbook vázlatverzióját. 
5. Elindítja a runbookot **Export-RunAsCertificateToHybridWorker**, majd a a **runbook meghívása** panelen, a beállítás a **futtatási beállítások** választja  **Hibridfeldolgozó** és a legördülő listában jelölje ki azokat a hibrid feldolgozócsoport ebben a forgatókönyvben a korábban létrehozott.  

    Ez exportálja a tanúsítványt a hibrid feldolgozó, hogy a munkavégző használatával a runbookok hitelesítéséhez az Azure-ban a Futtatás mint kapcsolat (különösen a forgatókönyv - importálási runbookokat az Automation-fiók) Azure-erőforrások kezeléséhez.

4. Az Automation-fiók, jelölje ki a korábban létrehozott hibrid feldolgozócsoport és [adja meg egy futtató fiókot](automation-hrw-run-runbooks.md#runas-account) a hibrid feldolgozócsoport, és válassza a hitelesítőadat-eszköz csak vagy már hozott létre.  Ez biztosítja, hogy a szinkronizálási runbook futtatható-e a Git-parancsokat. 
5. Elindítja a runbookot **Sync-LocalGitFolderToAutomationAccount**, adja meg a következő kötelező bemeneti paraméterérték és a **runbook meghívása** panelen, a beállítás a **futtatási beállítások**  választja **Hibridfeldolgozó** és a legördülő listában válassza ki a korábban létrehozott ebben a forgatókönyvben a hibrid feldolgozócsoport:
    * *Erőforráscsoport* – az Automation-fiók társított az erőforráscsoport neve
    * *AutomationAccountName* – az Automation-fiók neve
    * *GitPath* -a helyi mappát vagy fájlt a hibrid forgatókönyv-feldolgozó, ahol Git be van állítva legutóbbi változtatásokat lekéréses

    Ez szinkronizálni a hibrid feldolgozó számítógép helyi Git mappájába, és majd a forráskönyvtár a .ps1 fájlok az Automation-fiók importálását.

    ![Indítsa el a Sync-LocalGitFolderToAutomationAccount Runbook](media/automation-scenario-source-control-integration-with-github-ent/start-runbook-synclocalgitfoldertoautoacct.png)<br>

7. Kiválasztásával le a runbook a feladat összefoglaló információk megtekintése a **Runbookok** paneljén az Automation-fiók, és válassza a **feladatok** csempére.  Ellenőrizze, hogy sikeresen befejeződött-e választva a **összes napló** csempe, és tekintse át a részletes napló adatfolyam.  

## <a name="next-steps"></a>Következő lépések

-  További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
-  További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
