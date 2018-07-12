---
title: A StorSimple eszköz frissítése |} A Microsoft Docs
description: Ismerteti a StorSimple update szolgáltatás használatával normál és a karbantartási módú frissítések és gyorsjavítások telepítése.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: v-sharos
ms.openlocfilehash: 412978d2c343394f295e336690ec72153dda4b79
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452617"
---
# <a name="update-your-storsimple-8000-series-device"></a>A StorSimple 8000 sorozatú eszköz frissítése
> [!NOTE]
> A StorSimple klasszikus portálja elavult. A StorSimple-eszközkezelők automatikusan átkerülnek az új Azure Portalra az elavulási ütemezésnek megfelelően. Erről az áthelyezésről kapni fog egy e-mailt és egy Portal-értesítést. Ez a dokumentum hamarosan el lesz távolítva. Ha kérdései vannak az áthelyezéssel kapcsolatban, tekintse meg a [Gyakori kérdések: Váltás az Azure Portalra](storsimple-8000-move-azure-portal-faq.md) szakaszt.

## <a name="overview"></a>Áttekintés
A StorSimple frissítések funkciói lehetővé teszik, hogy egyszerűen tartsa naprakészen a StorSimple-eszköz. A frissítés típusától függően az eszközt a klasszikus Azure portálon keresztül vagy a Windows PowerShell felületéről frissítéseket alkalmazhat. Ez az oktatóanyag leírja a frissítések típusának és azok telepítése.

Kétféle típusú eszköz frissítéseit alkalmazhatja: 

* Normál (vagy normál módú) frissítések
* Karbantartási módú frissítések

A klasszikus Azure portálon vagy a Windows PowerShell; segítségével általános frissítések telepítése Windows PowerShell a karbantartási módú frissítések telepítése kell használnia. 

Minden egyes frissítés típus leírása alább külön-külön.

### <a name="regular-updates"></a>Általános frissítések
Általános frissítések zavart nem okozó frissítések, amely telepíthető, amennyiben az eszköz normál módban. Ezeket a frissítéseket a Microsoft Update webhelyen keresztül mindkét eszközvezérlő lépnek. 

> [!IMPORTANT]
> A frissítési folyamat alatt a vezérlő feladatátvétele fordulhat elő. Azonban ez nem érinti rendszer rendelkezésre állás vagy a műveletet.
> 
> 

* A klasszikus Azure portálon keresztül általános frissítések telepítésével kapcsolatos részletekért lásd: [a klasszikus Azure portálon keresztül rendszeres frissítések telepítése](#install-regular-updates-via-the-azure-classic-portal).
* Általános frissítések a Windows PowerShell storsimple-höz készült is telepíthet. További információkért lásd: [Windows Powershellen keresztül rendszeres frissítések telepítése a storsimple-höz készült](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Karbantartási módú frissítések
Karbantartási módú frissítések zavart okozó frissítések, például a lemez belső vezérlőprogramok frissítése is. A frissítések megkövetelik az eszköz karbantartási módba kell helyezni. További információkért lásd: [2. lépés: Adja meg a karbantartási mód](#step2). A klasszikus Azure portál használatával nem karbantartási módú frissítések telepítése. Ehelyett a storsimple Windows PowerShell kell használnia. 

Karbantartási módú frissítések telepítésével kapcsolatos részletekért lásd: [telepítése karbantartási módú frissítések storsimple-höz készült Windows PowerShell segítségével](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Karbantartási módú frissítések külön-külön kell alkalmazható minden vezérlő. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>A klasszikus Azure portálon keresztül rendszeres frissítések telepítése
A klasszikus Azure portál segítségével a frissítések alkalmazása a StorSimple-eszköz.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>A StorSimple Windows PowerShell-lel rendszeres frissítéseinek telepítése
Storsimple-höz készült Windows PowerShell segítségével azt is megteheti, normál (normál módú) frissítések alkalmazásához.

> [!IMPORTANT]
> Bár a rendszeres frissítések storsimple-höz készült Windows PowerShell használatával is telepíthető, javasoljuk, hogy a klasszikus Azure portálon keresztül rendszeres frissítések telepítése. Frissítés 1-es verziótól kezdve előzetes ellenőrzése a portálon a frissítések telepítése előtt kell elvégezni. Az előzetes ellenőrzések megelőzik a hibákat, és a egyenletesebb élményt nyújt. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Storsimple-höz készült Windows Powershellen keresztül a karbantartási módú frissítések telepítése
Storsimple-höz készült Windows PowerShell használatával a StorSimple-eszköz karbantartási módú frissítések vonatkoznak. Ebben a módban az összes i/o-kérések fel van függesztve. Nem felejtő közvetlen elérésű memória (NVRAM) például szolgáltatások vagy a fürtözési szolgáltatás is le lesz állítva. Mindkét vezérlő beírásakor, vagy lépjen ki az ebben a módban indulnak újra. Ebben a módban való kilépéskor összes szolgáltatás folytatódik, és kifogástalan állapotban kell lennie. (Ez eltarthat néhány percig.)

Ha a alkalmazni a karbantartási módú frissítések van szüksége, kapni fog egy riasztást, hogy a frissítések, amelyeket telepíteni kell a klasszikus Azure portálon keresztül. Ez a riasztás storsimple-höz készült Windows PowerShell használata a frissítések telepítésére vonatkozó utasításokat tartalmazza. Miután frissítette az eszközt, ugyanazt az eljárást használatával állítsa át az eszköz rendszeres módra. Lépésenkénti útmutatásért lásd: [4. lépés: kilépési karbantartási módba](#step4).

> [!IMPORTANT]
> * Mielőtt karbantartási üzemmódba, győződjön meg arról, hogy mindkét eszközvezérlő kifogástalan állapotú ellenőrzésével a **hardverállapot** a a **karbantartási** oldalon a klasszikus Azure portálon. Ha a vezérlő állapota nem kifogástalan, forduljon a Microsoft Support a következő lépésekhez. További információkért látogasson el, forduljon a Microsoft ügyfélszolgálatához. 
> * Ha karbantartási módban van, először telepítse a frissítést egy tartományvezérlőre, majd a másik vezérlőre kell.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>1. lépés: Kapcsolódás a soros konzol <a name="step1">
Első lépésként használja egy alkalmazás, például a PuTTY a soros konzol eléréséhez. Az alábbi eljárás ismerteti, hogyan PuTTY a soros konzoljához való csatlakozáshoz.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>2. lépés: Adja meg a karbantartási mód <a name="step2">
Miután csatlakozott a konzolon, határozza meg, hogy vannak-e a frissítések telepítését, és adja meg a karbantartási módban, hogy telepítse őket.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>3. lépés: A frissítések telepítése <a name="step3">
Ezután telepítse a frissítéseket.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>4. lépés: Kilépési karbantartási mód <a name="step4">
Végül kilépett a karbantartási módból.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Storsimple-höz készült Windows Powershellen keresztül gyorsjavítások telepítése
Frissítések a Microsoft Azure storsimple-höz készült, ellentétben a gyorsjavítás telepítve van egy megosztott mappából. Frissítések, a két típusa van gyorsjavítások: 

* Rendszeres gyorsjavítások 
* Karbantartási módú gyorsjavítások  

A következő eljárások azt ismertetik, hogyan használhatja a storsimple-höz készült Windows PowerShell normál és a karbantartási módú gyorsjavítások telepítése.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Mi történik a frissítések, amennyiben az eszköz gyári beállítások visszaállítása?
Ha egy eszköz gyári alaphelyzetbe állításához, majd a frissítések elvesznek. Miután az előállító-visszaállítási eszköz regisztrálva és konfigurálva van, szüksége lesz a manuális telepítéséhez frissítéseit a klasszikus Azure portál és/vagy a Windows PowerShell storsimple-höz készült. További információ a gyári beállítások visszaállítása: [állítsa alaphelyzetbe az eszközt a gyári alapértelmezett beállításokra](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [storsimple-höz készült Windows PowerShell használata a StorSimple-eszköz felügyeletéhez](storsimple-windows-powershell-administration.md).
* Tudjon meg többet [a StorSimple Manager szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-manager-service-administration.md).

