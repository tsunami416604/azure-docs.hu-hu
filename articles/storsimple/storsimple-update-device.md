---
title: "A StorSimple eszköz frissítése |} Microsoft Docs"
description: "Ismerteti a StorSimple update szolgáltatás használatával normál és a karbantartási mód frissítéseket és gyorsjavításokat telepíti."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 247c422d7ea3feeec1342bb20f99390a07eb4f7a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="update-your-storsimple-8000-series-device"></a>A StorSimple 8000 Series eszköz frissítése
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
A StorSimple frissítések szolgáltatások lehetővé teszik, egyszerűen a StorSimple eszköz naprakész állapotban tartása érdekében. Attól függően, hogy a frissítés típusa frissítéseket alkalmazhat az eszköz a Windows PowerShell felületén keresztül vagy a klasszikus Azure portálon keresztül. Ez az oktatóanyag leírja a frissítések típusának és azok telepítése.

Kétféle eszközfrissítésekhez alkalmazhatja: 

* Rendszeres (vagy normál módú) frissítéseket
* Karbantartási mód frissítések

A klasszikus Azure portálon vagy a Windows PowerShell; rendszeres frissítések telepítése a Windows PowerShell a karbantartási mód frissítések telepítése kell használnia. 

Minden egyes frissítés típus külön-külön alább.

### <a name="regular-updates"></a>Rendszeres frissítések
Rendszeres frissítéseket tudja telepíteni, ha az eszköz normál üzemmódban van nem zavaró frissítések érhetők el. Ezeket a frissítéseket a Microsoft Update webhelyen keresztül minden eszköz vezérlő érvényesek. 

> [!IMPORTANT]
> A vezérlő feladatátvétel fordulhat elő, a frissítési folyamat alatt. Azonban ez nem érinti rendszer rendelkezésre állás vagy a műveletet.
> 
> 

* A klasszikus Azure portálon keresztül rendszeres frissítések telepítésével, lásd: [a klasszikus Azure portálon keresztül rendszeres frissítések telepítése](#install-regular-updates-via-the-azure-classic-portal).
* A StorSimple rendszeres frissítéseket a Windows PowerShell használatával is telepíthet. További információkért lásd: [telepítse a Windows PowerShell segítségével rendszeres frissítéseket StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Karbantartási mód frissítések
Karbantartási mód frissítések például a belső vezérlőprogram-frissítések lemez zavaró frissítések érhetők el. Ezek a frissítések karbantartási módba kell állítani az eszköz szükséges. További információkért lásd: [2. lépés: Adja meg a karbantartási mód](#step2). A klasszikus Azure portálra a karbantartási mód frissítések telepítése nem használható. Ehelyett a StorSimple Windows PowerShell kell használnia. 

A karbantartási mód frissítések telepítése, lásd: [telepítése karbantartási mód frissítések keresztül a Windows PowerShell-lel](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Karbantartási mód frissítések külön-külön kell alkalmazható minden tartományvezérlő. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>A klasszikus Azure portálon keresztül rendszeres frissítések telepítése
Frissítések alkalmazása a StorSimple eszközt használhatja a klasszikus Azure portálon.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Telepítse a Windows PowerShell segítségével rendszeres frissítéseket StorSimple
Azt is megteheti használhatja a Windows PowerShell-lel (normál mód) rendszeres frissítések alkalmazásához.

> [!IMPORTANT]
> Bár a Windows PowerShell használatával a StorSimple rendszeres frissítések telepítése erősen ajánlott a klasszikus Azure portálon keresztül rendszeres frissítések telepítése. Verziótól kezdődően Update 1 előzetes ellenőrzése a portálról frissítések telepítése előtt történik. Az előzetes ellenőrzések megelőzik az hibák, és a gördülékenyebb élményt nyújtsanak. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Telepítse a StorSimple karbantartási mód frissítéseket a Windows PowerShell segítségével
Karbantartási mód frissítések alkalmazása a StorSimple eszközt használhatja a Windows PowerShell-lel. Az összes i/o-kérelmek fel van függesztve, ebben a módban. Például nem felejtő közvetlen elérésű memória (NVRAM) vagy a fürtözött szolgáltatás is le van állítva. Mindkét tartományvezérlők újraindítása van, adja meg, vagy lépjen ki az ebben a módban. Ebben a módban való kilépéskor a szolgáltatások folytatódik, és megfelelő kell lennie. (Ez eltarthat néhány percig.)

Ha a karbantartási mód frissítések alkalmazására van szüksége, kapni fog egy riasztást, hogy rendelkezik-e telepíteni kell a klasszikus Azure portálon keresztül. Ez a riasztás Windows PowerShell-lel használata a frissítések telepítésére vonatkozó utasításokat tartalmazza. Miután frissítette az eszközt, a ugyanazzal az eljárással segítségével módosíthatja az eszköz rendszeres módra. Részletes útmutatásért lásd: [4. lépés: Kilépés a karbantartási mód](#step4).

> [!IMPORTANT]
> * Mielőtt karbantartási módba, ellenőrizze, hogy mindkét eszközvezérlők kifogástalan ellenőrzésével a **hardverállapot** a a **karbantartási** a klasszikus Azure portálra a lap. Ha a tartományvezérlő nem működik megfelelően, a következő lépéseket illetően forduljon a Microsoft Support. További információkért látogasson el forduljon a Microsoft ügyfélszolgálatához. 
> * Ha karbantartási módban, a frissítés először egy tartományvezérlő és a többi tartományvezérlőn kell.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>1. lépés: Csatlakozás soros konzolon<a name="step1">
Először egy alkalmazás, például a PuTTY használatával a soros konzol eléréséhez. Az alábbi eljárás ismerteti, hogyan PuTTY a soros konzoljához való csatlakozáshoz.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>2. lépés: Adja meg a karbantartási mód<a name="step2">
A konzol csatlakozik, határozza meg, hogy vannak-e a frissítések telepítéséhez, és adja meg a karbantartási módból, hogy telepítse őket.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>3. lépés: A frissítések telepítése<a name="step3">
Következő lépésként telepítse a frissítéseket.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>4. lépés: Kilépés a karbantartási mód<a name="step4">
Végezetül kilép karbantartási módból.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>A StorSimple gyorsjavítások Windows PowerShell telepítése
Frissítések a Microsoft Azure StorSimple, ellentétben a gyorsjavítás telepítve van egy megosztott mappából. Csakúgy, mint a frissítések két típusa van gyorsjavítások: 

* Rendszeres gyorsjavítások 
* Karbantartási mód gyorsjavítások  

A következő eljárások bemutatják, hogyan rendszeres és karbantartási mód gyorsjavítások telepítése a Windows PowerShell-lel segítségével.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Mi történik a frissítéseket, ha az eszköz gyári beállításainak visszaállítását?
Ha az eszköz visszaáll a gyári beállításokat, majd a frissítések elvesznek. A gyári alaphelyzetbe történő visszaállításhoz eszköz bejegyzés és konfigurálás után kell manuálisan telepítse a frissítéseket a klasszikus Azure portálon és/vagy a Windows PowerShell segítségével StorSimple. További információ a gyári beállítások visszaállítása: [visszaállítani az eszközt a gyári alapértelmezett beállításokra](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Következő lépések
* További információ [használata a Windows PowerShell-lel a StorSimple eszköz felügyeletéhez](storsimple-windows-powershell-administration.md).
* További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).

