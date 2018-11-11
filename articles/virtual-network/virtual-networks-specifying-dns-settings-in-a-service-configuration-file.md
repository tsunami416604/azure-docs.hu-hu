---
title: DNS-beállítások megadása egy szolgáltatáskonfigurációs fájlban |} A Microsoft Docs
description: virtual Network szolgáltatás konfigurációs fájl használatával egyéni DNS-beállítások megadása
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
ms.openlocfilehash: 0ac488a67d8b9debf6539d199395997cf44cf1e4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247177"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>DNS-beállítások megadása egy szolgáltatáskonfigurációs fájlban
## <a name="dns-elements"></a>DNS-elemek
Szolgáltatáskonfigurációs fájlt tartalmazhat egy DnsServers elem a tartománynévrendszer (DNS) kiszolgálók, a szolgáltatás által használt IPv4-címek listája. Beállítások a konfigurációs fájlban felülbírálják a hálózati konfigurációs fájlt a beállításokat. További információkért lásd: [Azure szolgáltatás konfigurációs sémáját (.cscfg fájl)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration elemet**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> A **neve** az attribútum a **DNS-kiszolgáló** elem csak egy hivatkozás névként szolgál. Nem tartozik a DNS-kiszolgáló állomásnevét. Minden egyes **DNS-kiszolgáló** attribútum értékének egyedinek kell lennie a teljes Microsoft Azure-előfizetés.
> 
> 

## <a name="see-also"></a>Lásd még:
[Az Azure szolgáltatás konfigurációs sémáját (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Az Azure Virtual Network konfigurációs séma](https://go.microsoft.com/fwlink/?LinkId=248093)

[Hálózati konfigurációs fájlok használatával virtuális hálózat konfigurálása](https://go.microsoft.com/fwlink/?LinkId=248094)

[Tudnivalók a virtuális hálózati beállításait a kezelési portálon](https://go.microsoft.com/fwlink/?LinkId=248092)

