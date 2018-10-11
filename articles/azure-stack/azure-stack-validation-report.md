---
title: Ellenőrzési jelentés az Azure Stackhez |} A Microsoft Docs
description: Az Azure Stack készültségi ellenőrző jelentés segítségével tekintse át az ellenőrzési eredmények.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 156b84e4941363716721b5cee6c19333ffe7594c
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079446"
---
# <a name="azure-stack-validation-report"></a>Az Azure Stack érvényesítési jelentés
Az Azure Stack készültségi ellenőrző eszközt használja, amelyek támogatják az üzembe helyezés és a egy Azure Stack-környezet karbantartás ellenőrzések futtatásához. Az eszköz eredmények jelentés .JSON kiterjesztésű fájlba írja. A jelentés üzembe helyezhető Azure Stacket előfeltételei állapotával kapcsolatos részletes és összesített adatokat jeleníti meg. A jelentés is a meglévő Azure Stack üzembe helyezéséhez titkos kódok rotációja kapcsolatos információkat jeleníti meg.  

 ## <a name="where-to-find-the-report"></a>Hol található a jelentés
Az eszköz futtatásakor naplózza az eredményeket **AzsReadinessCheckerReport.json**. Az eszközt is létrehoz egy naplófájlt nevű **AzsReadinessChecker.log**. Ezek a fájlok helyét jeleníti meg az ellenőrzés eredményét a PowerShellben.

![Futtatás-ellenőrzés](./media/azure-stack-validation-report/validation.png)

Mindkét fájl továbbra is fennáll, további érvényességi ellenőrzések üzemidejének a ugyanazon a számítógépen.  Ha például az eszköz futtatható tanúsítványok érvényesítésére, és futtassa újra az Azure-identitás ellenőrzése, majd harmadszor a regisztráció érvényesítése. Az összes három ellenőrzések eredményeit az eredményül kapott .json jelentés érhető el.  

Alapértelmezés szerint mindkét fájlt írt *C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Használja a **- OutputPath** ***&lt;elérési&gt;*** végén található egy másik jelentés helyét adja meg a futtatási parancssori paraméter.   
- Használja a **- CleanReport** paraméter adatainak törlése a futtatási parancs végén *AzsReadinessCheckerReport.json*. az eszköz előző futtatások.

## <a name="view-the-report"></a>A jelentés elolvasása
A jelentés megtekintése a PowerShellben, adja meg a jelentés elérési útja értéket **- ReportPath**. Ez a parancs megjeleníti a jelentés tartalmát, és azonosítja az ellenőrzés eredménye még nem rendelkező.

Például a jelentés egy PowerShell-parancssort, amely meg van nyitva a helyet, ahol a jelentés megtalálható a megtekintéséhez futtassa: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

A kimenet a következő képhez hasonlít:

![jelentés megtekintése](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Összesítő jelentés megtekintése
A jelentés megtekintésére, hozzáadhatja a **-összefoglaló** váltson át PowerShell-parancssorból végén. Példa: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Az összefoglalás ellenőrzések, amelyek nem rendelkeznek a következő eredményeket jeleníti meg, és azt jelzi, hogy továbbítja vagy, amelyek a teljes ellenőrzések sikertelenek. A kimenet a következő képhez hasonlít:

![a jelentés-összegzés](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Szűrt jelentés megtekintése
Szűrt jelentés egy adott típusú érvényesítési megtekintéséhez használja a **- ReportSections** paramétert a következő értékek egyikét:
- Tanúsítvány
- AzureRegistration
- AzureIdentity
- Feladatok   
- Összes  

Például a jelentés megtekintéséhez a tanúsítványok összegzése csak, használja a következő PowerShell-parancssorból: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Lásd még
[Start-AzsReadinessChecker parancsmag-referencia](azure-stack-azsreadiness-cmdlet.md)
