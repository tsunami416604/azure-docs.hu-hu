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
ms.date: 07/11/2017
ms.author: mabrigg
ms.openlocfilehash: e80c114b06390a72dcdcc614dffaa97b9761381e
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988908"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Kezelheti a frissítéseket az Azure Stack áttekintése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Minden hónap negyedik keddjén körül az Azure Stack integrált rendszerek általában kiadás új frissítési csomagokat. Az OEM érdeklődjön az adott értesítési folyamat frissítési értesítések eléréséhez a szervezet biztosításához. A jelen dokumentációs könyvtárban alatt alapján is ellenőrizheti **áttekintése** > **kibocsátási megjegyzések** verziókról lévő aktív támogatási információkat. 

A Microsoft különböző kiadásai egyetlen frissítési csomag részét képezi. Az Azure Stack operátorait szerint importálhatja, telepítése és a figyelő ezeket a telepítési folyamat frissítési csomagok a felügyeleti portálról. 

A számítógépgyártó (OEM) hardver szállítójával is frissítéseket, például az illesztőprogram- és belsővezérlőprogram-frissítések. Ezek a frissítések különálló csomagként az OEM hardver gyártója által lépnek, és a Microsoft-frissítésekből külön-külön történik.

Hogy a rendszer a támogatás keretében, Azure Stack egy adott verzió szintre frissíteni kell hagynia. Győződjön meg arról, hogy tekintse át a [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> Azure Stack csomagok az Azure Stack Development Kit nem alkalmazhat. A frissítési csomagok integrált rendszerek lettek kialakítva. További információ: [ismételt üzembe helyezése a ASDK](https://docs.microsoft.com/en-us/azure/azure-stack/asdk).

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


