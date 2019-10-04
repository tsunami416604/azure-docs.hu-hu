---
title: DNS-beállítások megadása egy szolgáltatás konfigurációs fájljában | Microsoft Docs
description: Egyéni DNS-beállítások megadása a virtuális hálózat szolgáltatás-konfigurációs fájljának használatával
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: f27802d76a8b94a0d5f1eb0c35fd55c93712e557
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059071"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>DNS-beállítások megadása egy szolgáltatás konfigurációs fájljában
## <a name="dns-elements"></a>DNS-elemek
A szolgáltatás konfigurációs fájlja tartalmazhatja a szolgáltatás által használt DNS-kiszolgálók IPv4-címeinek listáját tartalmazó DnsServers elemet. A szolgáltatás konfigurációs fájljának beállításai elsőbbséget élveznek a hálózati konfigurációs fájlban lévő beállításokkal szemben. További információ: [Azure szolgáltatás konfigurációs sémája (. Cscfg fájl)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration elem**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> A **DNS** elem **Name** attribútuma csak hivatkozási névként használható. A DNS-kiszolgáló állomásneve nem. Minden **DNS** attribútum értékének egyedinek kell lennie a teljes Microsoft Azure-előfizetésen belül.
> 
> 

## <a name="see-also"></a>Lásd még:
[Azure-szolgáltatás konfigurációs sémája (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure Virtual Network konfigurációs séma](https://go.microsoft.com/fwlink/?LinkId=248093)

[Virtual Network konfigurálása hálózati konfigurációs fájlok használatával](https://go.microsoft.com/fwlink/?LinkId=248094)

[A felügyeleti portál Virtual Network beállításainak ismertetése](https://go.microsoft.com/fwlink/?LinkId=248092)

