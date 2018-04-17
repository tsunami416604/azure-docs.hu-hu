---
title: Elindítása és leállítása a Azure verem Development Kit (ASDK) |} Microsoft Docs
description: Megtudhatja, hogyan indítsa el, és állítsa le az Azure verem Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: dfb565803746ecdda9b36a4e12a3c3f2b4d9e0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Elindítása és leállítása a Azure verem Development Kit (ASDK)
Nem ajánlott a ASDK számítógép egyszerűen újraindítására. Ehelyett kövesse a cikk megfelelően állítsa le és indítsa újra ASDK szolgáltatásokat eljárásokat. 

## <a name="stop-azure-stack"></a>Állítsa le az Azure verem 
Megfelelően állítsa le az Azure-verem szolgáltatások, és a ASDK számítógép, a következő PowerShell-parancsokat használja:

1. Jelentkezzen be az ASDK állomáson AzureStack\CloudAdmin.
2. Nyissa meg a Powershellt rendszergazdaként (nem a PowerShell ISE).
3. A következő parancsokat egy rendszerjogosultságú végpont (EGP) munkamenetet létrehozni: 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Ezután a EGP-munkamenetben, használhatja a **Stop-AzureStack** Azure verem services leállítása, és állítsa le a ASDK számítógép parancsmagot:

   ```powershell
   Stop-AzureStack
   ```
5. Tekintse át a PowerShell kimenete annak érdekében, hogy az összes Azure verem services sikeresen leállt a ASDK számítógép leállítása előtt. A leállítási folyamat néhány percet vesz igénybe.

## <a name="start-azure-stack"></a>Indítsa el az Azure verem 
ASDK szolgáltatások a gazdaszámítógép elkezdésének automatikusan elindul. Azonban ASDK infrastruktúra szolgáltatások indítási idő a ASDK fogadó számítógép hardverkonfigurációja teljesítményétől függ. Szolgáltatások újraindítása sikertelen bizonyos esetekben több órát is igénybe vehet.

Függetlenül attól, hogy a ASDK le volt állítva és ellenőrizze, hogy az összes Azure verem szolgáltatás elindult, és rendesen működik után a számítógép be van kapcsolva kell tegye a következőket: 

1. Kapcsolja a ASDK gazdaszámítógépen. 
2. Jelentkezzen be az ASDK állomáson AzureStack\CloudAdmin.
3. Nyissa meg a Powershellt rendszergazdaként (nem a PowerShell ISE).
4. A következő parancsokat egy rendszerjogosultságú végpont (EGP) munkamenetet létrehozni:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Ezután a EGP-munkamenetben a következő parancsokat Azure verem szolgáltatások indítási állapotának ellenőrzéséhez:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Tekintse át a kimenetet, annak érdekében, hogy Azure verem szolgáltatások újraindítása sikeresen megtörtént.

Megfelelően állítsa le és indítsa újra az Azure-verem szolgáltatások az ajánlott eljárásokkal kapcsolatos további tudnivalókért lásd: [kezdési és befejezési Azure verem](.\.\azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Hibaelhárítás indítása és leállítása 
Hajtsa végre ezeket a lépéseket, ha Azure verem szolgáltatások nem sikeresen elindítani a ASDK gazdaszámítógépen power két órán belül:

1. Jelentkezzen be az ASDK állomáson AzureStack\CloudAdmin.
2. Nyissa meg a Powershellt rendszergazdaként (nem a PowerShell ISE).
3. A következő parancsokat egy rendszerjogosultságú végpont (EGP) munkamenetet létrehozni:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Ezután a EGP-munkamenetben a következő parancsokat Azure verem szolgáltatások indítási állapotának ellenőrzéséhez:

   ```powershell
   Test-AzureStack
   ```
5. Tekintse át a kimenetet, és javítsa ki a hibákat. További információkért lásd: [Azure verem teszt futtatása](.\.\azure-stack-diagnostic-test.md).
6. Indítsa újra a verem Azure-szolgáltatásokat a EGP-munkameneten belül futtatásával a **Start-AzureStack** parancsmagot:

   ```powershell
   Start-AzureStack
   ```

Ha fut **Start-AzureStack** eredmények hibát, keresse fel a [Azure verem támogatási fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) ASDK hibaelhárítási támogatás eléréséhez. 

## <a name="next-steps"></a>További lépések 
További tudnivalók az Azure-verem diagnosztikai eszköz és naplózási ki, lásd: [Azure verem diagnosztikai eszközök](.\.\azure-stack-diagnostics.md).
