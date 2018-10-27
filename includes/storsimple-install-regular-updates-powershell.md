---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 36a014fbff30c32a7149a83907c9586bc3b2f01a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165747"
---
<!--author=SharS last changed: 11/18/16-->

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

