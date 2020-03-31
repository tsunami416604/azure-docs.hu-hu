---
title: Bevezetés a csomagrögzítésbe az Azure Network Watcherben | Microsoft dokumentumok
description: Ez az oldal áttekintést nyújt a Network Watcher csomagrögzítési képességéről
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1f17463125cead64bd58a2d07e53eee4d3cfcd70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840808"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Bevezetés a változó csomagrögzítésbe az Azure Network Watcherben

A Network Watcher változócsomag-rögzítése lehetővé teszi csomagrögzítési munkamenetek létrehozását a virtuális gépekre irányuló és onnan érkező forgalom nyomon követéséhez. A csomagrögzítés segít a hálózati anomáliák diagnosztizálásában mind a reaktív, mind a proaktív módon. Más felhasználások közé tartozik a hálózati statisztikák gyűjtése, a hálózati behatolásokkal kapcsolatos információk megszerzése, az ügyfél-kiszolgáló kommunikáció hibakeresése és még sok más.

A csomagrögzítés egy virtuálisgép-bővítmény, amely távolról indul el a Network Watcher segítségével. Ez a képesség megkönnyíti a csomagrögzítés manuális futtatásának terhét a kívánt virtuális gépen, ami értékes időt takarít meg. A csomagrögzítés a portálon, a PowerShellen, a CLI-n vagy a REST API-n keresztül indítható el. Egy példa arra, hogyan csomag rögzítése lehet aktiválni a Virtuálisgép riasztások. A rögzítési munkamenethez szűrők vannak biztosítva, hogy rögzítse a figyelni kívánt forgalmat. A szűrők az 5-tap szintű (protokoll, helyi IP-cím, távoli IP-cím, helyi port és távoli port) adatokon alapulnak. A rögzített adatok a helyi lemezen vagy egy tárolóblobban tárolódnak. Régiónként és előfizetésenként legfeljebb 10 csomagrögzítési munkamenet lehet. Ez a korlát csak a munkamenetekre vonatkozik, és nem vonatkozik a mentett csomagrögzítő fájlokra helyileg a virtuális gépen vagy egy tárfiókban.

> [!IMPORTANT]
> A csomagrögzítéshez virtuálisgép-bővítmény `AzureNetworkWatcherExtension`szükséges. A bővítmény Windows virtuális gépen történő telepítéséhez látogasson el az [Azure Network Watcher Agent windowsos](../virtual-machines/windows/extensions-nwa.md) és Linuxos virtuális gépbővítménybe, és látogasson el az [Azure Network Watcher Agent linuxos virtuálisgép-bővítménybe.](../virtual-machines/linux/extensions-nwa.md)

Ha a rögzített adatokat csak a kívánt információra szeretné csökkenteni, a csomagrögzítési munkamenethez a következő lehetőségek állnak rendelkezésre:

**Rögzítési konfiguráció**

|Tulajdonság|Leírás|
|---|---|
|**Maximális bájt csomagonként (bájt)** | A rögzített csomagok bájtjainak száma, az összes bájt rögzítése, ha üresen marad. A rögzített csomagok bájtjainak száma, az összes bájt rögzítése, ha üresen marad. Ha csak az IPv4 fejlécére van szüksége – itt adja meg a 34-et |
|**Munkamenetenkénti bájt (bájt) maximális bájt** | A rögzített bájtok teljes száma, amint az érték eléri a munkamenet végét.|
|**Időkorlát (másodperc)** | Időmegkötést állít be a csomagrögzítési munkamenethez. Az alapértelmezett érték 18000 másodperc vagy 5 óra.|

**Szűrés (nem kötelező)**

|Tulajdonság|Leírás|
|---|---|
|**Protocol (Protokoll)** | A csomagrögzítéshez szűrni rekedő protokoll. A rendelkezésre álló értékek: TCP, UDP és All.|
|**Helyi IP-cím** | Ez az érték szűri a csomagrögzítést azokra a csomagokra, amelyekhelyi IP-címe megegyezik ezzel a szűrőértékkel.|
|**Helyi port** | Ez az érték azokra a csomagokra szűri a csomagrögzítést, amelyeknél a helyi port megegyezik ezzel a szűrőértékkel.|
|**Távoli IP-cím** | Ez az érték szűri a csomagrögzítést azokra a csomagokra, amelyekhez a távoli IP megfelel ennek a szűrőértéknek.|
|**Távoli port** | Ez az érték azokra a csomagokra szűri a csomagrögzítést, amelyekhez a távoli port megfelel ennek a szűrőértéknek.|

### <a name="next-steps"></a>További lépések

Megtudhatja, hogyan kezelheti a csomagrögzítéseket a portálon keresztül, ha az [Azure Portalon a Csomagrögzítés kezelése](network-watcher-packet-capture-manage-portal.md) vagy a PowerShell segítségével a [Csomagrögzítés kezelése a PowerShell segítségével](network-watcher-packet-capture-manage-powershell.md)című webhelyen keresztül kezelhető.

Ismerje meg, hogyan hozhat létre proaktív csomagrögzítéseket a virtuálisgép-riasztások alapján, ha ellátogat [A riasztás aktivált csomagrögzítésének létrehozása című ellátogat](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













