---
title: Kezelheti a frissítéseket az Azure Stack – áttekintés |} A Microsoft Docs
description: Ismerje meg az update management Azure Stack integrált rendszerek.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5b0dbf5ad78ff345d386024ff873618a781917ac
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579037"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Kezelheti a frissítéseket az Azure Stack áttekintése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

A Microsoft update csomagok az Azure Stack integrált rendszerek általában kiadás minden hónap negyedik keddjén körül. Az OEM érdeklődjön az adott értesítési folyamat frissítési értesítések eléréséhez a szervezet biztosításához. A jelen dokumentációs könyvtárban alapján is ellenőrizheti **áttekintése** > **kibocsátási megjegyzések** verziókról lévő aktív támogatási információkat. 

A Microsoft különböző kiadásai egyetlen frissítési csomag részét képezi. Az Azure Stack operátorait szerint importálhatja, telepítése és a figyelő ezeket a telepítési folyamat frissítési csomagok a felügyeleti portálról. 

A számítógépgyártó (OEM) hardver szállítójával is frissítéseket, például az illesztőprogram- és belsővezérlőprogram-frissítések. Bár ezek a frissítések a számítógép-Gyártói hardver gyártója által lépnek különálló csomagként, azok importált, telepített és felügyelhető az azonos módon frissítési csomagokat a Microsoft frissítési csomagok importálása, telepített és felügyelt.

Hogy a rendszer a támogatás keretében, Azure Stack egy adott verzió szintre frissíteni kell hagynia. Győződjön meg arról, hogy tekintse át a [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> Azure Stack csomagok az Azure Stack Development Kit nem alkalmazhat. A frissítési csomagok integrált rendszerek lettek kialakítva. További információ: [ismételt üzembe helyezése a ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Az erőforrás-szolgáltató frissítése

Az Azure Stack-frissítés erőforrás-szolgáltató a Microsoft szoftverfrissítések alkalmazásának vezénylő tartalmazza. Az erőforrás-szolgáltató biztosítja, hogy a frissítések alkalmazása minden fizikai gazdagép, Service Fabric-alkalmazásokat és futásidejének tekintetében, és az összes virtuális gépeket és a társított szolgáltatások között.

Telepíti a frissítéseket, mert a frissítési folyamat célként az Azure Stackben (például fizikai gazdagépeket és virtuális gépeket) a különböző alrendszereket megtekintheti általános állapotát.

## <a name="plan-for-updates"></a>Frissítések tervezése

Javasoljuk, hogy a karbantartási műveleteket felhasználók értesítése, és hogy úgy ütemezze a normál karbantartási időszakok során munkaidőn kívüli Ha lehetséges. Karbantartási műveletek hatással lehetnek a bérlői terhelések és a webportálos műveletek.

## <a name="using-the-update-tile-to-manage-updates"></a>A frissítés csempe használata kezelheti a frissítéseket
A felügyeleti portálon kezelheti a frissítéseket. Az Azure Stack operátorait szerint használhatja a frissítés csempét az irányítópulton:

- fontos információkat, például a jelenlegi verzió megtekintéséhez.
- Telepítse a frissítéseket, és a folyamat állapotának monitorozásához.
- Tekintse át a korábban telepített frissítések frissítési előzményeket.
 
## <a name="determine-the-current-version"></a>Határozza meg az aktuális verzió

A frissítés csempe az Azure Stack aktuális verzióját mutatja. A felügyeleti portálon a következő módszerek egyikével férhetnek hozzá a frissítési csempe:

- Az irányítópulton megtekintheti a jelenlegi verziót a **frissítés** csempére.
 
   ![Frissítéseket alapértelmezett irányítópult csempéjén.](./media/azure-stack-updates/image1.png)
 
- Az a **régiók kezelése** csempét, és kattintson a régió neve. Megtekintheti a jelenlegi verziót a **frissítés** csempére.

## <a name="next-steps"></a>További lépések

- [Az Azure Stack karbantartási szabályzat](azure-stack-servicing-policy.md) 
- [Régiók kezelése az Azure Stackben](azure-stack-region-management.md)     


