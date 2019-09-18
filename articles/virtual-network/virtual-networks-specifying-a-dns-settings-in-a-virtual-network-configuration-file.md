---
title: DNS-beállítások megadása egy virtuális hálózat konfigurációs fájljában | Microsoft Docs
description: A DNS-kiszolgáló beállításainak módosítása virtuális hálózatban a klasszikus üzemi modellben található virtuális hálózati konfigurációs fájl használatával
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: c15d73673c19383deabe15ef30026990dfd138b9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059091"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>DNS-beállítások megadása egy virtuális hálózat konfigurációs fájljában
A hálózati konfigurációs fájl két elemet tartalmaz, amelyek segítségével megadhatja a tartománynévrendszer (DNS) beállításait: **DnsServers** és **DnsServerRef**. A DNS-kiszolgálók listáját a **DnsServers** elemhez tartozó IP-címek és hivatkozási nevek megadásával veheti fel. Ezután egy **DnsServerRef** elem használatával megadhatja, hogy a rendszer mely DNS-kiszolgáló bejegyzéseket használja a DnsServers elemben a virtuális hálózaton belüli különböző hálózati helyekhez.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

A hálózati konfigurációs fájl a következő elemeket tartalmazza. Az egyes elemek címe egy olyan oldalhoz van csatolva, amely további információkat tartalmaz az elem értékének beállításairól.

> [!IMPORTANT]
> További információ a hálózati konfigurációs fájl konfigurálásáról: [Virtual Network konfigurálása hálózati konfigurációs fájl használatával](virtual-networks-using-network-configuration-file.md). A hálózati konfigurációs fájlban található egyes elemekről további információt az [Azure Virtual Network konfigurációs sémája](https://msdn.microsoft.com/library/azure/jj157100.aspx)című témakörben talál.
> 
> 

[DNS-elem](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> A **DNS** elem **Name** attribútuma csak a **DnsServerRef** elemre mutató hivatkozásként szolgál. A DNS-kiszolgáló állomásneve nem. Minden **DNS** attribútum értékének egyedinek kell lennie a teljes Microsoft Azure-előfizetésen belül
> 
> 

[Virtual Network helyek elem](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Ha ezt a beállítást szeretné megadni a Virtual Network helyek elemhez, azt előzőleg meg kell határozni a DNS-elemben. A Virtual Network helyek elem DnsServerRef *neve* csak a DNS-elemben megadott DNS- *név*értékre hivatkozik.
> 
> 

## <a name="next-steps"></a>További lépések
* Az [Azure Virtual Network konfigurációs sémájának](https://go.microsoft.com/fwlink/?LinkId=248093)ismertetése.
* Az [Azure szolgáltatás konfigurációs sémájának](https://msdn.microsoft.com/library/windowsazure/ee758710)ismertetése.
* [Virtuális hálózat konfigurálása hálózati konfigurációs fájlok használatával](virtual-networks-using-network-configuration-file.md).

