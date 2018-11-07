---
title: DNS-beállítások megadása egy virtuális hálózat konfigurációs fájljában |} A Microsoft Docs
description: Egy virtuális hálózat konfigurációs fájljában a klasszikus üzemi modellben a virtuális hálózat DNS-kiszolgáló beállításainak módosítása
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
ms.openlocfilehash: 36f7ed9b02b66718327c1a05a6cf29eedf39e7a5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248385"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>DNS-beállítások megadása egy virtuális hálózat konfigurációs fájljában
A hálózati konfigurációs fájlt tartalmaz két elemet, adja meg a tartománynévrendszer (DNS) beállítások segítségével: **DnsServers** és **DnsServerRef**. DNS-kiszolgálók listáját hozzáadhat saját IP-címek megadásával és nevek hivatkozhat a **DnsServers** elemet. Ezután egy **DnsServerRef** elemet adja meg, melyik DNS-kiszolgálói bejegyzésekkel az DnsServers elemből használják a virtuális hálózaton belüli különböző hálózati helyek.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

A hálózati konfigurációs fájlt az alábbi elemeket is tartalmazhat. Minden elem címe csatolva lesz egy oldal, amely további információkat találhat az elem érték beállításait.

> [!IMPORTANT]
> A hálózati konfigurációs fájlt konfigurálásával kapcsolatos további információkért lásd: [konfigurálása egy virtuális hálózatot a hálózati konfigurációs fájl](virtual-networks-using-network-configuration-file.md). A hálózati konfigurációs fájlban található minden elem kapcsolatos információkért lásd: [Azure Virtual Network konfigurációs séma](https://msdn.microsoft.com/library/azure/jj157100.aspx).
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
> A **neve** az attribútum a **DNS-kiszolgáló** elem csak referenciaként szolgál a **DnsServerRef** elemet. Nem tartozik a DNS-kiszolgáló állomásnevét. Minden egyes **DNS-kiszolgáló** attribútum értékének egyedinek kell lennie a teljes Microsoft Azure-előfizetés
> 
> 

[Virtuális hálózati helyek elem](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Annak érdekében, hogy adja meg ezt a beállítást, a virtuális hálózati telephelyek elemhez, azt korábban definiálni kell a DNS-elemben. A DnsServerRef *neve* a virtuális hálózati helyeken elem egy DNS-kiszolgáló a DNS-elemben megadott név-érték kell hivatkoznia *neve*.
> 
> 

## <a name="next-steps"></a>További lépések
* Megismerheti a [az Azure Virtual Network konfigurációs séma](https://go.microsoft.com/fwlink/?LinkId=248093).
* Megismerheti a [Azure-szolgáltatás konfigurációs sémáját](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Konfigurálja a virtuális hálózatot hálózati konfigurációs fájlok használatával](virtual-networks-using-network-configuration-file.md).

