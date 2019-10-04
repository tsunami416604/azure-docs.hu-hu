---
title: Módosítsa a DATA 0 beállításainak a StorSimple 8000 sorozatú eszköz |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálja újra a DATA 0 hálózati adapterén, a StorSimple eszköz storsimple-höz készült Windows PowerShell használatával.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 3cf136c5ddec8f4998d15c597914e1f806453945
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631583"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>A DATA 0 hálózati kapcsolati beállítások az a StorSimple 8000 sorozatú eszköz módosítása

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple-eszköz hat hálózati adapterek, a DATA 0 DATA 5 rendelkezik. Az adatok 0 adapter mindig van konfigurálva a Windows PowerShell felületén vagy a soros konzolon keresztül, és automatikusan felhő-t. Vegye figyelembe, hogy a DATA 0 hálózati adapter az Azure Portalon keresztül nem lehet konfigurálni.

Az adatok 0 adapter először során a telepítővarázsló keresztül lehet konfigurálni a kezdeti központi telepítését a StorSimple-eszköz. Ha az eszköz működési módja van, szükség lehet segítségével konfigurálja újra a DATA 0 beállításai. Ez az oktatóanyag ismerteti, hogy két módszer módosításához a DATA 0 hálózati beállítások, mindkét keresztül Windows PowerShell-bővítménye.

Ebben az oktatóanyagban elolvasásával fogja tudni:

* Módosítsa a DATA 0 hálózati beállítása a telepítő varázsló
* DATA 0 hálózati beállítások módosítása a `Set-HcsNetInterface` parancsmag

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>A telepítő varázsló DATA 0 hálózati beállítások módosítása
DATA 0 hálózati beállítások a Windows PowerShell felületét a StorSimple eszköz csatlakozik, és a telepítő varázsló munkamenet indítása újrakonfigurálhatja az. A következő lépésekkel módosíthatja a DATA 0 beállításai:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>A telepítő varázsló DATA 0 hálózati beállítások módosítása
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**. Amikor a rendszer kéri adja meg a **eszköz rendszergazdai jelszava**. Az alapértelmezett jelszó az `Password1`.
2. A parancssorba írja be a következőt:
   
    `Invoke-HcsSetupWizard`
3. Segítségével a konfigurálása a DATA 0 megjelenik egy telepítővarázsló az eszköz felületén. Adja meg a IP-cím, az átjáró és a hálózati maszk új értékeit.

> [!NOTE]
> A fix vezérlő IP-címek keresztül konfigurálni kell a **hálózati beállítások** panel az Azure Portalon a StorSimple-eszköz. További információért ugorjon [módosítása a hálózati adapterek](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Set-HcsNetInterface parancsmag DATA 0 hálózati beállítások módosítása
Konfigurálja újra a DATA 0 hálózati adapter használatával egy másik módszere a `Set-HcsNetInterface` parancsmagot. A parancsmagot a StorSimple eszköz a Windows PowerShell interfészről hajtja végre. Ez az eljárás használata esetén a vezérlő fix IP-cím is megadható itt. A következő lépésekkel módosíthatja a DATA 0 beállításai: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>A Set-HcsNetInterface parancsmag DATA 0 hálózati beállítások módosítása
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**. Amikor a rendszer kéri az eszköz rendszergazdai jelszava meg. Az alapértelmezett jelszó az `Password1`.
2. A parancssorba írja be a következőt:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    A csúcsos zárójelek között írja be a DATA 0 a következő értékeket:
   
   * IPv4 address
   * IPv4-átjáró
   * IPv4 subnet mask
   * 0\. vezérlő rögzített IPv4-cím
   * A vezérlő 1 fix IPv4-cím
     
     További információk a parancsmag [Windows PowerShell-parancsmag-referencia a StorSimple](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>További lépések
* Nem a DATA 0 hálózati adapterek konfigurálásához használja a [hálózati beállítások konfigurálása az Azure Portalon](storsimple-8000-modify-device-config.md). 
* Ha a hálózati adapterek konfigurálásához minden olyan problémákat tapasztal, tekintse meg [üzembe helyezés hibáinak elhárítása](storsimple-troubleshoot-deployment.md).

