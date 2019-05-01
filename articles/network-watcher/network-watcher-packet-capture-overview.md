---
title: Az Azure Network Watcher Packet-capture bemutatása |} A Microsoft Docs
description: Ez az oldal nyújt a Network Watcher packet capture funkció áttekintése
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 4cfbfc4bed5438ed901fca86d8c2939d3860c68e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684158"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Bevezetés az Azure Network Watcher változó csomagrögzítés

Network Watcher változó csomagrögzítés csomagrögzítési munkamenetet, és a virtuális gépről érkező forgalom nyomon létrehozását teszi lehetővé. Csomag rögzítési alapján diagnosztizálhatja a hálózat rendellenességek mindkét reaktív és proactivity. Más használati módjai többek között, hálózati statisztika, azonosítsa a hálózati behatolásokat, hibakeresése, ügyfél-kiszolgáló közötti kommunikációt, és még sok más információk összegyűjtéséhez.

A csomagrögzítés egy virtuálisgép-bővítmény, amely keretében a Network Watcher távoli elindult. Ez a funkció egyszerűsíti a csomagrögzítés értékes időt takarít meg használni kívánt virtuális gépet manuálisan futó terhe. A csomagrögzítés is elindítható a portal, PowerShell, CLI vagy REST API használatával. Például hogyan csomagrögzítés is elindítható a virtuális gép riasztások van. Szűrők a rögzítési munkamenet rögzíti a forgalmat a figyelni kívánt biztosítására szolgálnak. Szűrők alapulnak (protokoll, helyi IP-cím, távoli IP-cím, helyi port és a távoli port) 5-ször több információt. A rögzített adatok tárolódnak a helyi lemez vagy egy storage-blobba. A 10 csomagrögzítési munkamenetet régiónként és előfizetésenként korlátozva van. Ez a korlátozás csak azokra a munkamenetek, és nem vonatkozik a mentett csomag rögzítési fájlokat helyileg, a virtuális gép vagy a storage-fiókban.

> [!IMPORTANT]
> A csomagrögzítés szükséges virtuálisgép-bővítmények `AzureNetworkWatcherExtension`. A bővítmény telepítését egy Windows virtuális gépen látogasson el [Azure Network Watcher-ügynök virtuálisgép-bővítmény Windows](../virtual-machines/windows/extensions-nwa.md) és Linux rendszerű virtuális gép látogasson el a [Azure Network Watcher-ügynök virtuálisgép-bővítmény Linuxhoz](../virtual-machines/linux/extensions-nwa.md).

Az adatokat, csak a kívánt adatokat a rögzítés csökkentése érdekében az alábbi lehetőségek érhetők el a rögzítési munkamenet csomag:

**Konfigurációjának rögzítése**

|Tulajdonság|Leírás|
|---|---|
|**Bájtok maximális száma csomagonként (bájt)** | A szám (bájt) az egyes csomagok, amelyek rögzítve lesznek az összes bájt vannak rögzítve, ha üresen hagyja. A szám (bájt) az egyes csomagok, amelyek rögzítve lesznek az összes bájt vannak rögzítve, ha üresen hagyja. Ha csak az IPv4-fejléc – kell 34 Itt jelezheti |
|**Bájtok maximális száma munkamenetenként (bájt)** | Az adott bájtok teljes száma rögzítve lesznek a érték elérésekor a munkamenete véget nem ér.|
|**Időkorlát (másodperc)** | Csoportok időkorlát a csomagot a rögzítési munkamenet. Az alapértelmezett érték 18000 másodperc és 5 óra.|

**Szűrés (nem kötelező)**

|Tulajdonság|Leírás|
|---|---|
|**Protocol (Protokoll)** | A csomagrögzítés szűrését protokoll. Az elérhető értékek a következők: TCP, UDP és az összes.|
|**Helyi IP-cím** | Ezt az értéket a csomagrögzítés csomagok szűri, ha a helyi IP-cím megegyezik-e a szűrő értéke.|
|**Helyi port** | Ezt az értéket a csomagrögzítés csomagok szűri, ahol a helyi port egyezik-e a szűrő értéke.|
|**Távoli IP-cím** | Ezt az értéket a csomagrögzítés csomagok szűri, ha a távoli IP-cím megegyezik-e a szűrő értéke.|
|**Távoli port** | Ezt az értéket a csomagrögzítés csomagok szűri, ha a távoli port megegyezik-e a szűrő értéke.|

### <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kezelheti a portálon keresztül csomagrögzítés funkcionáló [az Azure Portalon csomagrögzítés kezelése](network-watcher-packet-capture-manage-portal.md) vagy a PowerShell-lel funkcionáló [csomag rögzítése kezelése a PowerShell-lel](network-watcher-packet-capture-manage-powershell.md).

Ismerje meg, hogyan hozhat létre a virtuális gép riasztásain alapuló funkcionáló proaktív csomagrögzítés [hozzon létre egy aktivált riasztás csomagrögzítés](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













