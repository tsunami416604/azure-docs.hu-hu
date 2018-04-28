---
title: DNS-beállítások megadása a virtuális hálózati konfigurációs fájlban |} Microsoft Docs
description: A klasszikus üzembe helyezési modellel virtuális hálózati konfigurációs fájl használatával egy virtuális hálózat DNS-kiszolgáló beállításainak módosítása
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: ed7f02d3e389db3bc772c4fcb00a7b3877d60173
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>DNS-beállítások megadása a virtuális hálózati konfigurációs fájlban
A hálózati konfigurációs fájl van két olyan elemet, a tartománynévrendszer (DNS) beállítások megadására használható: **DnsServers** és **DnsServerRef**. Adja hozzá a DNS-kiszolgálók listáját az IP-címek megadásával, és hivatkozzon a neveket a **DnsServers** elemet. Ezután egy **DnsServerRef** elemet adja meg, melyik DNS-kiszolgálóbejegyzéseik az DnsServers elemből különböző hálózati helyek, virtuális hálózaton belül használják.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

A hálózati konfigurációs fájlt a következő elemet tartalmazhatnak. A cím az egyes elemek csatolva van egy oldal, amely további információkat találhat az elem érték beállításait.

> [!IMPORTANT]
> A hálózati konfigurációs fájl konfigurálásával kapcsolatos további információkért lásd: [konfigurálja a virtuális hálózat használatával a hálózati konfigurációs fájl](virtual-networks-using-network-configuration-file.md). További információ az egyes elemei a hálózati konfigurációs fájlban: [Azure virtuális hálózat konfigurációs séma](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS-elem](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> A **neve** attribútumnak a **DNS-kiszolgáló** elem használata csak a hivatkozásként a **DnsServerRef** elemet. Mert nem felel meg a DNS-kiszolgáló állomásneve. Minden egyes **DNS-kiszolgáló** attribútumérték belül egyedinek kell lennie a teljes Microsoft Azure-előfizetés
> 
> 

[Virtuális hálózati helyek elem](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Ahhoz, hogy adja meg ezt a beállítást, a virtuális hálózati helyek elemhez, azt korábban definiálni kell a DNS-elemben. A DnsServerRef *neve* a virtuális hálózati helyek elem hivatkozhatnak egy DNS-kiszolgáló a DNS-elemben megadott név-érték *neve*.
> 
> 

## <a name="next-steps"></a>További lépések
* Megérteni a [Azure-beli virtuális hálózat konfigurációs séma](http://go.microsoft.com/fwlink/?LinkId=248093).
* Megérteni a [Azure szolgáltatás konfigurációs séma](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Konfigurálja a hálózati konfigurációs fájlokat használó virtuális hálózatot](virtual-networks-using-network-configuration-file.md).

