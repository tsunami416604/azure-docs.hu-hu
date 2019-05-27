---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171943"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Windows PowerShell-lel rendszeres frissítések telepítése a storsimple-höz készült
1. Nyissa meg az eszköz soros konzoljához és select 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**. Írja be a jelszót. Az alapértelmezett jelszó az *jelszó1*. 
2. A parancssorba írja be a következőt:
   
     `Get-HcsUpdateAvailability`
   
    Akkor kap értesítést, ha elérhető frissítések, és hogy a frissítések zavart okozó vagy zavart nem okozó legyenek-e.
3. A parancssorba írja be a következőt:
   
     `Start-HcsUpdate`
   
    A frissítési folyamat indul el.

> [!IMPORTANT]
> * Ez a parancs csak a rendszeres frissítések vonatkozik. Ez a parancs csak egy tartományvezérlőn futtatja, de mindkét vezérlő frissülni fog. 
> * A frissítés során; előfordulhat, hogy figyelje meg a vezérlő feladatátvétele a feladatátvétel azonban rendszer rendelkezésre állását vagy művelet nincs hatással.
> 
> 

