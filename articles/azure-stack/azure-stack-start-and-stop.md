---
title: "Elindítása és leállítása Azure verem |} Microsoft Docs"
description: "Megtudhatja, hogyan indítása és leállítása Azure verem."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: mabrigg
ms.openlocfilehash: 98bf75f5883b734c785ed1a3ed924afca1737c56
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="start-and-stop-azure-stack"></a>Elindítása és leállítása Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek (verzió: 1712 és újabb verziók)*

## <a name="stop-azure-stack"></a>Állítsa le az Azure verem 

Azure verem leállítani a következő lépéseket:

1. Nyissa meg a kiemelt végpont munkamenet (EGP) a virtuális gép hálózati hozzáférés az Azure verem ERCS virtuális gépekhez. Útmutatásért lásd: [használatával a privilegizált végpont Azure verem](azure-stack-privileged-endpoint.md).

2. Futtassa az EGP:

    ```powershell
      Stop-AzureStack
    ```

3. Várjon, amíg minden fizikai Azure csomópontok energiagazdálkodással ki.

> [!Note]  
> Ellenőrizheti a fizikai csomópont power állapotának a az eredeti hardvergyártó (OEM) számára az Azure-verem hardver megadott utasítások alapján. 

## <a name="start-azure-stack"></a>Indítsa el az Azure verem 

Azure verem kezdje az alábbi lépéseket. Kövesse az alábbi lépéseket, függetlenül attól, milyen Azure verem leállt.

1. Bekapcsolás a fizikai csomópontok a verem Azure környezetben. Utasításokat a fizikai csomópontok bekapcsolás ellenőrizze a az eredeti hardvergyártó (OEM) számára megadott a hardver az Azure-verem cikk utasításait követve.

2. Várjon, amíg elindítja a verem Azure infrastruktúra-szolgáltatásokat. Verem Azure infrastruktúra-szolgáltatásokat is a folyamat befejezése két órát igénybe vehet. Azure verem és a kezdő állapotát ellenőrizheti a [ **Get-ActionStatus** parancsmag](#get-the-startup-status-for-azure-stack).


## <a name="get-the-startup-status-for-azure-stack"></a>Azure verem indítási állapotának beolvasása

Az indítási érhető el az Azure verem közben a következő lépéseket:

1. Munkamenetet nyit meg egy Rendszerjogosultságú végpont a virtuális gép hálózati hozzáférés az Azure verem ERCS virtuális gépekhez.

2. Futtassa az EGP:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Indítási és leállítási Azure verem hibaelhárítása

Hajtsa végre az alábbi lépéseket, ha az infrastrukturális és bérlői szolgáltatások nem sikeresen indul el, energiagazdálkodási után 2 óra Azure verem környezetben. 

1. Munkamenetet nyit meg egy Rendszerjogosultságú végpont a virtuális gép hálózati hozzáférés az Azure verem ERCS virtuális gépekhez.

2. Futtatás: 

    ```powershell
      Test-AzureStack
      ```

3. Tekintse át a kimenetet, és javítsa ki a rendszerállapot-hibákat. További információkért lásd: [Azure verem teszt futtatása](azure-stack-diagnostic-test.md).

4. Futtatás:

    ```powershell
      Start-AzureStack
    ```

5. Ha fut **Start-AzureStack** , hibát okoz, lépjen kapcsolatba a Microsoft ügyfélszolgálata szolgáltatások. 

## <a name="next-steps"></a>További lépések 

További tudnivalók az Azure-verem diagnosztikai eszköz és naplózása mellett tekintse meg az [Azure verem diagnosztikai eszközök. Azure-verem diagnosztikai eszközök. Azure-verem diagnosztikai eszközök. Azure-verem diagnosztikai eszközök.
