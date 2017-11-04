---
title: "DNS-beállítások megadása a szolgáltatás konfigurációs fájljában |} Microsoft Docs"
description: "a virtuális hálózati szolgáltatás konfigurációs fájl segítségével egyéni DNS-beállítások megadása"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: jdial
ms.openlocfilehash: 0fba2ea06827aff29a7a092933edb8120d668b29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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

