---
title: "DATA 0 módosítása a StorSimple 8000 series eszközön található beállítások |} Microsoft Docs"
description: "Ismerje meg, hogyan használható Windows PowerShell-lel konfigurálja újra a StorSimple eszköz DATA 0 hálózati adapterén."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 90df43e22f17fd32fe642514df098b72700e77af
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>A DATA 0 hálózati kapcsolati beállítások a StorSimple 8000 series eszközön módosítása

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple eszköz hat hálózati adapterrel, a DATA 0 DATA 5 rendelkezik. A DATA 0 felület mindig konfigurálva van a Windows PowerShell felületéről, sem a soros konzolon keresztül, és automatikusan felhő-kompatibilis. Vegye figyelembe, hogy nem konfigurálhat az Azure portálon keresztül DATA 0 hálózati adapterén.

A DATA 0, a telepítővarázslót során először konfigurálja a felület telepítési a StorSimple eszköz kezdeti. Ha az eszköz egy üzemeltetési módban van, szükség lehet, hogy engedélyezzék a DATA 0 beállításait. Ez az oktatóanyag biztosít két módszer módosításához a DATA 0 hálózati beállításai, mind a Windows PowerShell-lel.

Ez az oktatóanyag elolvasása, után fogja tudni:

* Módosítsa a DATA 0 hálózati beállítás a telepítővarázslót
* DATA 0 hálózati beállítások módosítása a `Set-HcsNetInterface` parancsmag

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>A telepítő varázsló lépéseinek DATA 0 hálózati beállításainak módosítása
Olyan módon konfigurálhatja újra DATA 0 hálózati beállítások a Windows PowerShell-felületet a StorSimple eszköz csatlakozik, és a telepítő varázsló munkamenet elindításához. A következő lépésekkel módosíthatja a DATA 0 beállítások:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>A telepítő varázsló lépéseinek DATA 0 hálózati beállításainak módosítása
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**. Amikor a rendszer kéri adja meg a **eszköz rendszergazdai jelszava**. Az alapértelmezett jelszó `Password1`.
2. A parancssorba írja be:
   
    `Invoke-HcsSetupWizard`
3. Konfigurálhatja a DATA 0 megjelenik egy telepítővarázsló az eszköz felületén. Adjon meg új értékeket az IP cím, az átjáró és a hálózati maszk.

> [!NOTE]
> A rögzített vezérlők IP-címek használatával újra kell konfigurálni kell a **hálózati beállításai** panel a StorSimple eszköz az Azure portálon. További információkért látogasson el [módosítása a hálózati adapterek](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Set-HcsNetInterface parancsmaggal DATA 0 hálózati beállításainak módosítása
Konfigurálja újra a DATA 0 hálózati adapter használatával megadva is a `Set-HcsNetInterface` parancsmag. A parancsmag végrehajtásának a Windows PowerShell-felületet a StorSimple eszköz. Ez az eljárás használata esetén a vezérlő fix IP-címek is konfigurálható itt. A következő lépésekkel módosíthatja az adatokat 0 beállítások: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>A Set-HcsNetInterface parancsmaggal DATA 0 hálózati beállításainak módosítása
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**. Amikor a rendszer kéri adja meg az eszköz rendszergazdai jelszava. Az alapértelmezett jelszó `Password1`.
2. A parancssorba írja be:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    A csúcsos zárójelek közé írja be a következő értékeket a DATA 0:
   
   * IPv4-cím
   * IPv4-átjáró
   * IPv4 alhálózati maszk
   * 0. vezérlő rögzített IPv4-cím
   * A vezérlő 1 fix IPv4-cím
     
     További információt ennek a parancsmagnak [StorSimple parancsmag-referencia a Windows PowerShell](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Következő lépések
* DATA 0 kivételével a hálózati adapterek konfigurálásához használja a [hálózati beállítások konfigurálása az Azure-portálon a](storsimple-8000-modify-device-config.md). 
* Ha a hálózati adapterek konfigurálásakor esetleges problémákat tapasztal, tekintse meg a [telepítési problémák elhárításához](storsimple-troubleshoot-deployment.md).

