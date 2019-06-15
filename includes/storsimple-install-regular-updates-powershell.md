---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
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

