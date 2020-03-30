---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179152"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Rendszeres frissítések telepítése a Windows PowerShell for StorSimple rendszeren keresztül
1. Nyissa meg az eszköz soros konzolját, és válassza az 1-es lehetőséget, **a Bejelentkezés teljes hozzáféréssel**. Írja be a jelszót. Az alapértelmezett jelszó a *Password1*. 
2. A parancssorba írja be a következőt:
   
     `Get-HcsUpdateAvailability`
   
    Értesítést kap, ha a frissítések elérhetők, és hogy a frissítések zavaróak vagy nem zavaróak.You will be be available if updates are available and whether the updates are disruptive or non-disruptive.
3. A parancssorba írja be a következőt:
   
     `Start-HcsUpdate`
   
    A frissítési folyamat elindul.

> [!IMPORTANT]
> * Ez a parancs csak a rendszeres frissítésekre vonatkozik. Ezt a parancsot csak egy vezérlőn futtatja, de mindkét vezérlő frissülni fog. 
> * Előfordulhat, hogy a frissítési folyamat során vezérlőfeladat-átvételt észlel; a feladatátvétel azonban nem befolyásolja a rendszer rendelkezésre állását vagy működését.
> 
> 

