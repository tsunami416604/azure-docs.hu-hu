---
title: Az Azure privát végpont kapcsolati problémáinak hibaelhárítása
description: Részletes útmutatás a privát végpont-kapcsolat diagnosztizálásához
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538534"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Az Azure privát végpont kapcsolati problémáinak hibaelhárítása

Ez a cikk lépésenkénti útmutatást nyújt az Azure Private Endpoint-kapcsolat beállításának érvényesítéséhez és diagnosztizálásához.

Az Azure Private Endpoint egy hálózati adapter, amely privát és biztonságos an-európai kapcsolatszolgáltatáshoz kapcsolja. Ez a megoldás segít az Azure-beli számítási feladatok védelmében azáltal, hogy privát kapcsolatot biztosít az Azure-szolgáltatás erőforrásaihoz a virtuális hálózatról. Ez a megoldás hatékonyan hozza ezeket a szolgáltatásokat a virtuális hálózatra.

A privát végpontban elérhető kapcsolódási forgatókönyvek a következők:

- Virtuális hálózat ugyanabból a régióból
- Regionálisan társviszonyban lévő virtuális hálózatok
- Globálisan társviszonyban lévő virtuális hálózatok
- A helyszíni ügyfelek VPN- vagy Azure ExpressRoute-áramkörökön keresztül

## <a name="diagnose-connectivity-problems"></a>Kapcsolódási problémák diagnosztizálása 

Tekintse át ezeket a lépéseket, és győződjön meg arról, hogy a szokásos konfigurációk a szokásos módon oldják meg a kapcsolódási problémákat a privát végpont beállításával.

1. Tekintse át a privát végpont konfigurációját az erőforrás böngészésével.

    a. Nyissa meg a **Privát hivatkozási központot.**

      ![Privát hivatkozási központ](./media/private-endpoint-tsg/private-link-center.png)

    b. A bal oldali ablaktáblán válassza a **Privát végpontok lehetőséget.**
    
      ![Privát végpontok](./media/private-endpoint-tsg/private-endpoints.png)

    c. Szűrje ki, és válassza ki a diagnosztizálni kívánt privát végpontot.

    d. Tekintse át a virtuális hálózat és a DNS adatait.
     - Ellenőrizze, hogy a kapcsolat állapota **jóváhagyva van-e.**
     - Győződjön meg arról, hogy a virtuális gép rendelkezik kapcsolattal a virtuális hálózat, amely a magán-végpontok.
     - Ellenőrizze, hogy a teljes tartománynérték-adatok (másolás) és a privát IP-cím hozzá vannak-e rendelve.
    
       ![Virtuális hálózat és DNS-konfiguráció](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Az [Azure Monitor segítségével](https://docs.microsoft.com/azure/azure-monitor/overview) ellenőrizze, hogy az adatok áramlanak-e.

    a. A privát végponterőforráson válassza a **Monitor**lehetőséget.
     - Válassza **az Adatok be-** vagy **adatki-** és be. 
     - Annak megtekintése, hogy az adatok áramlanak-e, amikor megpróbál csatlakozni a privát végponthoz. Körülbelül 10 perces késésre számíthat.
    
       ![Magánvégpont-telemetria ellenőrzése](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Használja **a Virtuálisgép-kapcsolat hibaelhárítást** az Azure Network Watchertől.

    a. Válassza ki az ügyfél virtuális gép.

    b. Válassza a **Kapcsolat hibaelhárítása**lehetőséget, majd a **Kimenő kapcsolatok** lapot.
    
      ![Hálózati figyelő - Kimenő kapcsolatok tesztelése](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. A **kapcsolat nyomkövetéséhez**válassza a Hálózatfigyelő használata lehetőséget.
    
      ![Hálózatfigyelő – Kapcsolat – – Kapcsolat – – – – Kapcsolat – – – Hibaelhárítás](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Válassza **a Teszt teljes tartományonként**lehetőséget .
     - Illessze be a teljes tartománynhálózatot a privát végponterőforrásból.
     - Adjon meg egy portot. Általában használja a 443-as Azure Storage vagy az Azure Cosmos DB és 1336 SQL.

    e. Válassza **a Teszt**lehetőséget, és ellenőrizze a teszteredményeket.
    
      ![Network Watcher - Vizsgálati eredmények](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. A teszteredmények DNS-feloldásának ugyanazzal a privát IP-címmel kell rendelkeznie a privát végponthoz.

    a. Ha a DNS-beállítások helytelenek, kövesse az alábbi lépéseket:
     - Privát zóna használata esetén: 
       - Győződjön meg arról, hogy az ügyfél virtuális gép virtuális hálózat a privát zónához van társítva.
       - Ellenőrizze, hogy létezik-e a magánDNS-zónarekord. Ha nem létezik, hozd létre.
     - Egyéni DNS használata esetén:
       - Tekintse át az egyéni DNS-beállításokat, és ellenőrizze, hogy a DNS-konfiguráció helyes-e.
       További útmutatás: [Privát végpont – áttekintés: DNS-konfiguráció](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Ha a kapcsolat hálózati biztonsági csoportok (NSG-k) vagy a felhasználó által definiált útvonalak miatt nem működik:
     - Tekintse át az NSG kimenő szabályokat, és hozza létre a megfelelő kimenő szabályokat a forgalom engedélyezéséhez.
    
       ![NSG kimenő szabályok](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Ha a kapcsolat érvényesítette az eredményeket, a kapcsolódási probléma más szempontokhoz, például titkos kulcsokhoz, jogkivonatokhoz és jelszavakhoz kapcsolódhat az alkalmazásrétegen.
   - Ebben az esetben tekintse át a magánhálózati erőforrás hoz társított magán-végpont konfigurációját. További információt az [Azure Private Link hibaelhárítási útmutatójában](troubleshoot-private-link-connectivity.md)talál.

1. Lépjen kapcsolatba az [Azure támogatási csapatával,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ha a probléma továbbra is megoldatlan, és továbbra is fennáll kapcsolódási probléma.

## <a name="next-steps"></a>További lépések

 * [Privát végpont létrehozása a frissített alhálózaton (Azure Portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Útmutató az Azure Private Link hibaelhárítási útmutatójához](troubleshoot-private-link-connectivity.md)
