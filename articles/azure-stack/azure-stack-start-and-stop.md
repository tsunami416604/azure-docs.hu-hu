---
title: Elindítása és leállítása Azure verem |} Microsoft Docs
description: Megtudhatja, hogyan indítása és leállítása Azure verem.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 53015ba5c282bbe9c7b8185b080ffb6d834b6c75
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="start-and-stop-azure-stack"></a>Elindítása és leállítása Azure verem
Kövesse a cikk megfelelően állítsa le és indítsa újra az Azure-verem szolgáltatások eljárásokat. 

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

További tudnivalók az Azure-verem diagnosztikai eszköz és naplózási ki, lásd: [Azure verem diagnosztikai eszközök](azure-stack-diagnostics.md).
