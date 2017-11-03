---
title: "Runbook tesztelése az Azure Automationben |} Microsoft Docs"
description: "Runbook közzététele az Azure Automationben, mielőtt a teszteléshez le annak érdekében, hogy megfelelően működik-e.  Ez a cikk ismerteti egy runbook tesztelése, és tekintse meg a kimenetet."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 7f7db785-52c0-4613-aa12-b02fd32a5182
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;bwren
ms.openlocfilehash: 2c811cc8c8277e9840babcf1043cde44238b8661
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="testing-a-runbook-in-azure-automation"></a>Runbook tesztelése az Azure Automationben
Egy runbook tesztelésekor a [vázlatként megjelölt verziót](automation-creating-importing-runbook.md#publishing-a-runbook) végrehajtása és minden elvégzett műveletet végrehajt befejezését. Nincs feladatelőzményekben jön létre, de a [kimeneti](automation-runbook-output-and-messages.md#output-stream) és [figyelmeztetés és hiba](automation-runbook-output-and-messages.md#message-streams) adatfolyamok megjelennek a vizsgálat kimeneti ablaktáblában. Az üzenetek a [részletes adatfolyam](automation-runbook-output-and-messages.md#message-streams) megjelennek a Tesztkimenet ablaktáblán csak akkor, ha a [$VerbosePreference változó](automation-runbook-output-and-messages.md#preference-variables) Folytatás értékre van állítva.

Annak ellenére, hogy a vázlatként megjelölt verziót futtatása, a runbook még végrehajtja a munkafolyamatot a szokásos módon, és erőforrások minden műveletet elvégez a környezetben. Ezért csak nem éles erőforrások mappaszinten kell tesztelni.

Az eljárást minden egyes [típusú forgatókönyvet](automation-runbook-types.md) azonos, és ott tesztelése a szöveges szerkesztő és az Azure portálon grafikus szerkesztő között nincs különbség.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Egy runbook tesztelése az Azure-portálon
Használhat egyetlen [runbooktípusba](automation-runbook-types.md) az Azure portálon.

1. Nyissa meg a runbook vázlatverzióját vagy a [szöveges szerkesztő](automation-edit-textual-runbook.md) vagy [grafikus szerkesztő](automation-graphical-authoring-intro.md).
2. Kattintson a **teszt** gombra kattintva nyissa meg a teszt panelt.
3. Ha a runbook paraméterekkel rendelkezik, azok megjelennek a bal oldali ablaktáblán, ahol megadhatja a vizsgálathoz használt értékek.
4. Ha azt szeretné, hogy a teszt futtatásáról egy [hibrid forgatókönyv-feldolgozó](automation-hybrid-runbook-worker.md), majd módosítsa **futtatása beállítások** való **Hibridfeldolgozó** válassza ki a nevét, a célcsoport.  Ellenkező esetben hagyja meg az alapértelmezett **Azure** a teszt végrehajtása a felhőben.
5. Kattintson a **Start** gombra kattintva indítsa el a vizsgálatot.
6. Ha a runbook állapota [PowerShell munkafolyamat](automation-runbook-types.md#powershell-workflow-runbooks) vagy [grafikus](automation-runbook-types.md#graphical-runbooks), majd állítsa le, vagy felfüggeszti, amíg a kimeneti ablaktábla alatti gombokkal tesztelését. A runbook felfüggesztésekor felfüggesztés előtt végrehajtja az aktuális tevékenységet. Ha a runbook fel van függesztve, állítsa le, vagy indítsa újra.
7. Vizsgálja meg a kimeneti ablaktábla a runbook kimenete.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan hozzon létre vagy importáljon egy runbookot, lásd: [létrehozása vagy egy Azure Automation forgatókönyv importálása](automation-creating-importing-runbook.md)
* További információk a Grafikus létrehozásról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* Állapotüzenetek és a hibák vissza runboks konfigurálásával kapcsolatos további tudnivalókért beleértve az ajánlott eljárásokat, lásd: [Runbook-kimenet és üzenetek az Azure Automationben](automation-runbook-output-and-messages.md)

