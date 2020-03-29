---
title: Data 0 beállítások módosítása a StorSimple 8000 sorozatú eszközön | Microsoft dokumentumok
description: Megtudhatja, hogy a StorSimple-hez való Windows PowerShell használatával hogyan konfigurálhatja újra a DATA 0 hálózati felületet a StorSimple-eszközön.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631583"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>A DATA 0 hálózati illesztő beállításainak módosítása a StorSimple 8000 sorozatú eszközön

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple eszköz hat hálózati felülettel rendelkezik, a DATA 0-tól a DATA 5-ig. A DATA 0 felület mindig a Windows PowerShell-felületen vagy a soros konzolon keresztül van konfigurálva, és automatikusan felhőalapú. Vegye figyelembe, hogy a DATA 0 hálózati felület nem konfigurálható az Azure Portalon keresztül.

A DATA 0 felület először a telepítő varázslón keresztül konfigurálható a StorSimple eszköz kezdeti üzembe helyezése során. Ha az eszköz üzemi üzemmódban van, előfordulhat, hogy újra kell konfigurálnia a DATA 0 beállításait. Ez az oktatóanyag két módszert biztosít a DATA 0 hálózati beállítások módosítására, mind a Windows PowerShell for StorSimple rendszeren keresztül.

Elolvasása után ez a bemutató, akkor képes lesz arra, hogy:

* A DATA 0 hálózati beállítás módosítása a telepítővarázslón keresztül
* Data 0 hálózati beállítások `Set-HcsNetInterface` módosítása a parancsmagon keresztül

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Data 0 hálózati beállítások módosítása a telepítővarázslón keresztül
A DATA 0 hálózati beállításait újrakonfigurálhatja a StorSimple-eszköz Windows PowerShell-felületéhez való csatlakozással és a telepítővarázsló munkamenetének elindításával. A DATA 0 beállítások módosításához hajtsa végre az alábbi lépéseket:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>A DATA 0 hálózati beállításainak módosítása a telepítővarázslón keresztül
1. A soros konzol menüjében **Log in with full access**válassza az 1. Amikor a rendszer kéri, adja meg az **eszköz rendszergazdai jelszavát.** Az alapértelmezett `Password1`jelszó a .
2. A parancssorba írja be a következőt:
   
    `Invoke-HcsSetupWizard`
3. Megjelenik egy telepítővarázsló, amely segít az eszköz DATA 0 felületének konfigurálásában. Adjon meg új értékeket az IP-címhez, az átjáróhoz és a hálózati maszkhoz.

> [!NOTE]
> A rögzített vezérlők IP-címeket újra kell konfigurálni a StorSimple-eszköz **hálózati beállítások** paneljén keresztül az Azure Portalon. További információt a [Hálózati adapterek módosítása](storsimple-8000-modify-device-config.md#modify-network-interfaces)című területen talál.

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Data 0 hálózati beállítások módosítása set-HcsNetInterface parancsmagon keresztül
A DATA 0 hálózati interfész újrakonfigurálásának másik `Set-HcsNetInterface` módja a parancsmag használata. A parancsmag végrehajtása a StorSimple-eszköz Windows PowerShell felületéről történik. Ha ezt az eljárást használja, a vezérlő rögzített IP-címeket is konfigurálhatja itt. Hajtsa végre az alábbi lépéseket a DATA 0 beállítások módosításához: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>A DATA 0 hálózati beállítások módosítása a Set-HcsNetInterface parancsmagon keresztül
1. A soros konzol menüjében **Log in with full access**válassza az 1. Amikor a rendszer kéri, adja meg az eszköz rendszergazdai jelszavát. Az alapértelmezett `Password1`jelszó a .
2. A parancssorba írja be a következőt:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    A szöges zárójelek mezőbe írja be a data 0 következő értékeit:
   
   * IPv4-cím
   * IPv4-átjáró
   * IPv4-alhálózati maszk
   * Fix IPv4-cím a 0 vezérlőhöz
   * Rögzített IPv4-cím az 1-es vezérlőhöz
     
     A parancsmag használatával kapcsolatos további információkért látogasson el [a Windows PowerShell for StorSimple parancsmag hivatkozási lapra.](https://technet.microsoft.com/library/dn688161.aspx)

## <a name="next-steps"></a>További lépések
* A DATA 0-tól eltérő hálózati csatolók konfigurálásához használja a [Hálózati beállítások konfigurálását az Azure Portalon.](storsimple-8000-modify-device-config.md) 
* Ha bármilyen problémát tapasztal a hálózati adapterek konfigurálásakor, olvassa el a [Telepítési problémák elhárítása című témakört.](storsimple-troubleshoot-deployment.md)

