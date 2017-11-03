---
title: "Az Azure hálózati figyelőt csomagrögzítéssel bemutatása |} Microsoft Docs"
description: "Ezen a lapon a hálózati figyelőt csomag rögzítése funkció áttekintése"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 152cc8fb61aa6115c7b5863e4d798db9e7aa5b7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Az Azure hálózati figyelőt változó csomagrögzítéssel bemutatása

Hálózati figyelő változó csomagrögzítéssel csomag rögzítési munkamenetek nyomon követéséhez forgalma és a virtuális gép létrehozását teszi lehetővé. Csomag rögzítési segítségével mindkét hálózati rendellenességeket reaktív diagnosztizálásához és proactivity. Egyéb felhasználásra tartalmazzák a hálózati statisztikákat, hálózati behatolások, ügyfél-kiszolgáló közötti kommunikációt, és még sok más hibakeresési információkat való összegyűjtéséhez.

Csomagrögzítéssel a virtuálisgép-bővítmény hálózati figyelőt keresztül távolról elindult. Ez a funkció megkönnyíti a csomagrögzítéssel manuálisan futó a használni kívánt virtuális gépet, amely értékes időt takaríthat meg okozta terheket. A portál, a PowerShell, a parancssori felület vagy a REST API-n keresztül is elindítható a csomagrögzítéssel. Például hogyan csomagrögzítéssel is elindítható a virtuális gép a riasztások van. A rögzítési munkamenet rögzíti a figyelni kívánt forgalom biztosításához szűrők célokat szolgálnak. 5 rekordos (protokoll, helyi IP-cím, távoli IP-cím, helyi port és távoli port) alapuló szűrők információkat. A rögzített adatok a helyi lemez vagy egy tárolási blob tárolja. A 10 csomag rögzítési munkamenetek régiónként korlátozva van. Ezt a határt csak a munkamenetek vonatkozik, és nem vonatkozik a mentett csomag rögzítési fájlok helyileg a virtuális gép vagy egy tárfiókot.

> [!IMPORTANT]
> Csomagrögzítéssel van szükség a virtuálisgép-bővítmény `AzureNetworkWatcherExtension`. A bővítmény telepítése a Windows virtuális gép a Microsoft [a Windows Azure hálózati figyelő ügynök virtuálisgép-bővítmény](../virtual-machines/windows/extensions-nwa.md) és a Linux virtuális gép helyezést [Azure hálózati figyelő ügynök virtuálisgép-bővítmény Linux](../virtual-machines/linux/extensions-nwa.md).

Az információk csak a szükséges információkkal rögzítené csökkentése érdekében az alábbi beállítások érhetők el egy csomag rögzítési munkamenet:

**Konfigurációjának rögzítése**

|Tulajdonság|Leírás|
|---|---|
|**Maximális bájtokat csomagot (bájt)** | A szám bájtok számát az egyes csomagok, amelyek rögzítve lesznek, az összes bájt rofilok rögzítésének, ha üresen hagyja. A szám bájtok számát az egyes csomagok, amelyek rögzítve lesznek, az összes bájt rofilok rögzítésének, ha üresen hagyja. Ha csak az IPv4-fejléc – kell jelző 34 Itt |
|**Maximális bájtokat munkamenet (bájt)** | Az adott bájtok teljes száma a rendszer rögzíti, az érték elérésekor a munkamenet véget ér.|
|**Időkorlát (másodperc)** | Beállítja a csomag időkorlát munkamenet rögzítése. Az alapértelmezett érték 18000 másodperceken vagy 5 óra.|

**Szűrés (nem kötelező)**

|Tulajdonság|Leírás|
|---|---|
|**Protocol (Protokoll)** | A csomagrögzítéssel szűrhet protokollt. A lehetséges értékek a következők: TCP és UDP összes.|
|**Helyi IP-cím** | Ez az érték a csomagrögzítéssel csomagok szűrése, ahol a helyi IP-cím megegyezik-e a szűrő.|
|**Helyi port** | Ez az érték a csomagrögzítéssel csomagok szűrése, ahol a helyi port megegyezik-e a szűrő.|
|**Távoli IP-cím** | Ez az érték a csomagrögzítéssel csomagok szűrése, ahol az távoli IP-cím megegyezik-e a szűrő.|
|**Távoli port** | Ez az érték a csomagrögzítéssel csomagok szűrése, ahol a távoli port megegyezik-e a szűrő.|

### <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan kezelheti a portálon keresztül csomag rögzítésekre ellátogatva [kezelése az Azure portálon csomagrögzítéssel](network-watcher-packet-capture-manage-portal.md) vagy a PowerShell-lel ellátogatva [csomag rögzítése kezelése a PowerShell-lel](network-watcher-packet-capture-manage-powershell.md).

Látogasson el a virtuális gép riasztások alapján proaktív csomag rögzítésekre létrehozásával [riasztási kiváltott csomagrögzítéssel létrehozása](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













