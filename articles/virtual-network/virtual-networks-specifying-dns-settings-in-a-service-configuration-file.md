---
title: DNS-beállítások megadása a szolgáltatás konfigurációs fájljában |} Microsoft Docs
description: a virtuális hálózati szolgáltatás konfigurációs fájl segítségével egyéni DNS-beállítások megadása
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 009206f1e0ba848538ed2c666032a63051d062e4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>DNS-beállítások megadása a szolgáltatás konfigurációs fájljában
## <a name="dns-elements"></a>DNS-elemek
A szolgáltatás konfigurációs fájlja tartalmazhat a DnsServers elemnek a tartománynévrendszer (DNS) kiszolgálók, a szolgáltatás által használt IPv4-címek listáját. A szolgáltatás konfigurációs fájljában érvényesülnek beállításokat a hálózati konfigurációs fájlban. További információkért lásd: [Azure szolgáltatás konfigurációs séma (.cscfg fájl)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration elemet**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> A **neve** attribútumnak a **DNS-kiszolgáló** elem csak egy hivatkozásnév használatos. Mert nem felel meg a DNS-kiszolgáló állomásneve. Minden egyes **DNS-kiszolgáló** attribútum értékének egyedinek kell lennie a teljes Microsoft Azure-előfizetésben.
> 
> 

## <a name="see-also"></a>Lásd még:
[Az Azure szolgáltatás konfigurációs séma (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure-beli virtuális hálózat konfigurációs séma](http://go.microsoft.com/fwlink/?LinkId=248093)

[Virtuális hálózat használatával a hálózati konfigurációs fájlok konfigurálása](http://go.microsoft.com/fwlink/?LinkId=248094)

[A kezelési portál virtuális hálózati beállításaival kapcsolatos](http://go.microsoft.com/fwlink/?LinkId=248092)

