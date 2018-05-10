---
title: Ellenőrzési jelentés Azure verem |} Microsoft Docs
description: Az Azure verem készültségi ellenőrző jelentés segítségével tekintse át az ellenőrzés eredményét.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="azure-stack-validation-report"></a>Az Azure verem ellenőrzési jelentés
A Azure verem készültségi-ellenőrző eszközt futtatja, amely támogatja a központi telepítési és karbantartási egy Azure verem környezet érvényesítést. Az eszköz ellenőrzési eredmények jelentés .JSON kiterjesztésű fájlba írja. A jelentés adatait jeleníti meg részletes és összesített Azure verem telepítésének előfeltételei állapotát és a titkos kulcsok Elforgatás Azure verem telepítéseit.  

 ## <a name="where-to-find-the-report"></a>Hol található a jelentés
Az eszköz futtatásakor naplózza az eredményeket **AzsReadinessCheckerReport.json**. Az eszköz is létrehoz egy naplófájlt nevű **AzsReadinessChecker.log**. Az ellenőrzés eredményét a PowerShellben mutatja be a fájlok helyét.

![Futtatás-érvényesítése](./media/azure-stack-validation-report/validation.png)

Mindkét fájl továbbra is fennáll, további ellenőrzési ellenőrzések futása közben ugyanazon a számítógépen.  Például az eszköz futtatható tanúsítványok ellenőrzéséhez futtassa újra a Azure-identitás ellenőrzése, és ezután harmadszor a regisztráció érvényesítése. A három vonatkozó összes érvényesítést eredményei az eredményül kapott .JSON kiterjesztésű jelentés érhető el.  

Alapértelmezés szerint mindkettőt írt *C:\Users\<felhasználónév > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Használja a **- OutputPath** ***&lt;elérési&gt;*** paraméter parancssor futtatása a jelentés másik hely megadásához végén.   
- Használja a **- CleanReport** parancs futtatásával törölje a következőről végén paraméter *AzsReadinessCheckerReport.json*. tudnivalók az eszköz korábbi futtatásakor.

## <a name="view-the-report"></a>A jelentés elolvasása
A jelentés megtekintése a PowerShellben, adja meg a jelentés elérési útját értékeként **- ReportPath**. A parancs megjeleníti a jelentés tartalmát, és is azonosítják, amelyek még nincsenek eredmények érvényesítést.

Például egy PowerShell-parancssorból, amely meg van nyitva, a helyre, ahol a jelentés itt található a jelentés megtekintéséhez futtassa: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

A kimenet a következő kép hasonlít:

![jelentés megtekintése](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Az összefoglaló jelentés megtekintése
A jelentés összegzésének megtekintése, adja hozzá a **-összefoglaló** a PowerShell-parancssorból végén váltani. Példa: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Az összegzés jeleníti meg, amelyek nem rendelkeznek eredmények érvényesítést, és adja meg, illetve sikertelen lehet, amelyek a teljes ellenőrzések jelzi. A kimenet a következő kép hasonlít:

![a jelentés-összegzés](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>A szűrt jelentés megtekintése
Egy adott típusú érvényesítése a szűrt jelentés megtekintéséhez használja a **- ReportSections** paraméter, és adja meg, hogy az érvényesítés típusának meg szeretné tekinteni a következő értékek egyikét:
- Tanúsítvány
- AzureRegistration
- AzureIdentity
- Feladatok   
- Összes  

Például a jelentés megtekintéséhez összefoglalása-tanúsítványok csak, használja a következő PowerShell-parancsot: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Lásd még
[Start-AzsReadinessChecker parancsmag-referencia](azure-stack-azsreadiness-cmdlet.md)
