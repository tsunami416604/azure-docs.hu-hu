---
title: Runbook tesztelése az Azure Automationben
description: Runbook közzététele az Azure Automationben, előtt tesztelheti, hogy győződjön meg arról, hogy a várt módon működik.  Ez a cikk ismerteti egy runbook tesztelése, és megtekinti a kimenetét.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 95e3f4426fab8ed3ff28877607dee8694962e79f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422473"
---
# <a name="testing-a-runbook-in-azure-automation"></a>Runbook tesztelése az Azure Automationben
Egy runbook tesztelésekor a [vázlatként megjelölt verziót](automation-creating-importing-runbook.md#publishing-a-runbook) hajtja végre, és minden olyan műveleteket, amelyeket végrehajt a végezhető el. Az előzményeket nem feladat jön létre, de a [kimeneti](automation-runbook-output-and-messages.md#output-stream) és [figyelmeztetési és](automation-runbook-output-and-messages.md#message-streams) adatfolyamok megjelennek a teszt panel kimenete. Az üzenetek a [részletes Stream](automation-runbook-output-and-messages.md#message-streams) megjelennek a Tesztkimenet ablaktáblán csak akkor, ha a [$VerbosePreference változó](automation-runbook-output-and-messages.md#preference-variables) Folytatás értékre van állítva.

Annak ellenére, hogy a vázlatként megjelölt verziót fut, a runbook továbbra is a munkafolyamat végrehajtása általában, és minden műveletet erőforrásokon elvégez a környezetben. Ezért soha ne tesztelje a runbookok nem éles erőforrásokat.

Az eljárást minden egyes teszteléséhez [típusú forgatókönyvet](automation-runbook-types.md) , az azonos, és hogy nem tesztelése a szöveges szerkesztő és az Azure Portalon a grafikus szerkesztő között nincs különbség.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Egy runbook tesztelése az Azure Portalon
Bármelyik működhet [runbook típusa](automation-runbook-types.md) az Azure Portalon.

1. Nyissa meg a runbook vázlatverzióját vagy a [szöveges szerkesztő](automation-edit-textual-runbook.md) vagy [grafikus szerkesztő](automation-graphical-authoring-intro.md).
2. Kattintson a **teszt** gombra kattintva nyissa meg a teszt panelt.
3. Ha a runbook paraméterekkel rendelkezik, akkor megjelenik a bal oldali panelen, ahol megadhat értékeket a teszt használandó.
4. Ha a vizsgálat futtatása egy [hibrid Runbook-feldolgozó](automation-hybrid-runbook-worker.md), majd módosítsa **beállításai** való **hibrid feldolgozó** válassza ki a nevét, a célcsoport.  Ellenkező esetben hagyja meg az alapértelmezett **Azure** a teszt futtatása a felhőben.
5. Kattintson a **Start** gombra kattintva indítsa el a vizsgálatot.
6. Ha a runbook [PowerShell-munkafolyamat](automation-runbook-types.md#powershell-workflow-runbooks) vagy [grafikus](automation-runbook-types.md#graphical-runbooks), akkor állítsa le, vagy felfüggesztésére, amíg a kimeneti ablaktábla alatti gombokkal tesztelését. A runbook felfüggesztésekor előtt felfüggeszti végrehajtja az aktuális tevékenységet. Ha a runbook fel van függesztve, állítsa le, vagy újraindíthatja.
7. Tekintse meg a kimenetet, a runbook a tesztkimenet ablaktáblán.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan hozzon létre vagy importáljon egy runbookot, lásd: [létrehozása vagy importálása az Azure Automation-runbook](automation-creating-importing-runbook.md)
* További információk a Grafikus létrehozásról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* Állapotüzenetek és a hibák visszaadása a runbookok konfigurálásával kapcsolatos további tudnivalókért köztük ajánlott eljárásokat, lásd: [Runbook-kimenet és üzenetek az Azure Automationben](automation-runbook-output-and-messages.md)


