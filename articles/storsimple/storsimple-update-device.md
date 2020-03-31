---
title: A StorSimple eszköz frissítése | Microsoft dokumentumok
description: A cikk azt ismerteti, hogy a StorSimple frissítési szolgáltatással hogyan lehet rendszeres és karbantartási módú frissítéseket és gyorsjavításokat telepíteni.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: twooley
ms.openlocfilehash: c9451afaefdd220b5f87d4650c7844f06926b03a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933420"
---
# <a name="update-your-storsimple-8000-series-device"></a>A StorSimple 8000 sorozatú eszköz frissítése
> [!NOTE]
> A StorSimple klasszikus portálja elavult. A StorSimple-eszközkezelők automatikusan átkerülnek az új Azure Portalra az elavulási ütemezésnek megfelelően. Erről az áthelyezésről kapni fog egy e-mailt és egy Portal-értesítést. Ez a dokumentum hamarosan el lesz távolítva. Ha kérdései vannak az áthelyezéssel kapcsolatban, tekintse meg a [Gyakori kérdések: Váltás az Azure Portalra](storsimple-8000-move-azure-portal-faq.md) szakaszt.

## <a name="overview"></a>Áttekintés
A StorSimple frissítési funkciók lehetővé teszik, hogy könnyen tartsa a StorSimple eszköz up-to-date. A frissítés típusától függően az Azure klasszikus portálon vagy a Windows PowerShell-felületen keresztül alkalmazhat frissítéseket az eszközre. Ez az oktatóanyag ismerteti a frissítéstípusokat és azok telepítésének módját.

Kétféle eszközfrissítést alkalmazhat: 

* Normál (vagy normál módú) frissítések
* Karbantartási mód frissítései

Rendszeres frissítéseket telepíthet a klasszikus Azure portálon vagy a Windows PowerShellen keresztül; a Karbantartási mód frissítéseinek telepítéséhez azonban a Windows PowerShell használatával kell rendelkeznie. 

Az egyes frissítéstípusokat külön ismertetjük, az alábbiakban.

### <a name="regular-updates"></a>Rendszeres frissítések
A rendszeres frissítések nem zavaró frissítések, amelyek akkor telepíthetők, ha az eszköz normál módban van. Ezek a frissítések a Microsoft Update webhelyen keresztül minden eszközvezérlőre érvényesek. 

> [!IMPORTANT]
> A frissítési folyamat során vezérlőfeladat-átvétel történhet. Ez azonban nem befolyásolja a rendszer rendelkezésre állását vagy működését.
> 
> 

* A klasszikus Azure-portálon keresztül a rendszeres frissítések telepítéséről a [Rendszeres frissítések telepítése az Azure klasszikus portálon keresztül](#install-regular-updates-via-the-azure-classic-portal)című témakörben talál részleteket.
* Rendszeres frissítéseket is telepíthet a Windows PowerShell for StorSimple rendszeren keresztül. További információt a [Rendszeres frissítések telepítése a StorSimple windows PowerShell en keresztül című témakörben talál.](#install-regular-updates-via-windows-powershell-for-storsimple)

### <a name="maintenance-mode-updates"></a>Karbantartási mód frissítései
A karbantartási mód frissítései olyan zavaró frissítések, mint például a lemez belső vezérlőprogramjának frissítése. Ezek a frissítések megkövetelik, hogy az eszközt karbantartási módba helyezze. További információt a [2.](#step2) Az Azure klasszikus portál on maintenance mode frissítések telepítéséhez nem használható. Ehelyett a Windows PowerShell storSimple. 

A karbantartási mód frissítéseinek telepítéséről a [Karbantartási mód frissítéseinek telepítése a StorSimple windows PowerShell szolgáltatáson keresztül](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)című témakörben talál.

> [!IMPORTANT]
> A karbantartási mód frissítéseit minden vezérlőre külön kell alkalmazni. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Rendszeres frissítések telepítése a klasszikus Azure-portálon keresztül
Az Azure klasszikus portál használatával frissítéseket alkalmazhat a StorSimple-eszközre.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Rendszeres frissítések telepítése a StorSimple-hez használható Windows PowerShell en keresztül
Azt is megteheti, hogy a Windows PowerShell for StorSimple rendszeres (normál mód) frissítések et alkalmazhat.

> [!IMPORTANT]
> Bár a Windows PowerShell for StorSimple használatával rendszeres frissítéseket telepíthet, javasoljuk, hogy rendszeres frissítéseket telepítsen a klasszikus Azure-portálon keresztül. Az 1. Ezek az előzetes ellenőrzések megelőzik a hibákat, és zökkenőmentesebb élményt biztosítanak. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Karbantartási mód frissítéseinek telepítése a StorSimple-hez letölthető Windows PowerShell szolgáltatáson keresztül
A StorSimple windows PowerShell használatával alkalmazhatja a Karbantartási mód frissítéseit a StorSimple-eszközre. Ebben a módban minden I/O-kérés szünetel. Az olyan szolgáltatások, mint a nem felejtő véletlen hozzáférésű memória (NVRAM) vagy a fürtözési szolgáltatás is leállnak. Mindkét vezérlő újraindul, amikor belép vagy kilép ebből a módból. Amikor kilép ebből a módból, az összes szolgáltatás folytatódik, és kifogástalan állapotúnak kell lennie. (Ez eltarthat néhány percig.)

Ha karbantartási mód frissítéseit kell alkalmaznia, értesítést kap az Azure klasszikus portálon keresztül, hogy a frissítéseket telepíteni kell. Ez a riasztás utasításokat tartalmaz a Windows PowerShell for StorSimple használatával a frissítések telepítéséhez. Az eszköz frissítése után ugyanezt az eljárást követve módosítsa az eszközt Normál módra. A részletes utasításokat a [4.](#step4)

> [!IMPORTANT]
> * A Karbantartási módba való belépés előtt ellenőrizze, hogy mindkét eszközvezérlő kifogástalan állapotban van-e, ha ellenőrzi a **hardver állapotát** a klasszikus Azure-portál **Karbantartás** lapján. Ha a vezérlő nem kifogástalan, a következő lépésekért forduljon a Microsoft támogatási szolgálatához. További információt a Microsoft támogatási szolgálatának megkeresése című oldalon talál. 
> * Ha karbantartási módban van, először az egyik, majd a másik vezérlőn kell alkalmaznia a frissítést.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>1. lépés: Csatlakozás a soros konzolhoz<a name="step1">
Először használjon egy alkalmazást, például a PuTTY-t a soros konzol eléréséhez. Az alábbi eljárás bemutatja, hogyan lehet a PuTTY használatával csatlakozni a soros konzolhoz.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>2. lépés: Karbantartási módba lépés<a name="step2">
Miután csatlakozott a konzolhoz, állapítsa meg, hogy vannak-e telepítandó frissítések, és lépjen be a telepítésükhöz a Karbantartási módba.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>3. lépés: A frissítések telepítése<a name="step3">
Ezután telepítse a frissítéseket.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>4. lépés: Kilépés a karbantartási módból<a name="step4">
Végül lépjen ki a Karbantartási módból.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Gyorsjavítások telepítése a StorSimple-hez használható Windows PowerShell en keresztül
A Microsoft Azure StorSimple frissítéseivel ellentétben a gyorsjavítások egy megosztott mappából vannak telepítve. A frissítésekhez is kétféle gyorsjavítás létezik: 

* Rendszeres gyorsjavítások 
* Karbantartási mód gyorsjavításai  

Az alábbi eljárások bemutatják, hogyan használhatja a Windows PowerShell for StorSimple rendszeres és karbantartási mód gyorsjavítások telepítéséhez.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Mi történik a frissítésekkel, ha gyári alaphelyzetbe állítja az eszközt?
Ha egy eszköz gyári beállításokra áll vissza, akkor az összes frissítés elveszik. A gyári alaphelyzetbe állítási eszköz regisztrálása és konfigurálása után manuálisan kell telepítenie a frissítéseket az Azure klasszikus portálján és/vagy a Windows PowerShell storSimple-hez. A gyári beállítások visszaállításáról az [Eszköz gyári alaphelyzetbe állítása című](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)témakörben olvashat bővebben.

## <a name="next-steps"></a>További lépések
* További információ a [StorSimple-eszköz felügyeletéhez a Windows PowerShell for StorSimple használatával kapcsolatban.](storsimple-windows-powershell-administration.md)
* További információ [a StorSimple-kezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-manager-service-administration.md)

