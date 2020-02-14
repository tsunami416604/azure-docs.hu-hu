---
title: Az Azure Private Endpoint kapcsolódási problémáinak elhárítása
description: Részletes útmutató a privát végpontok kapcsolatának diagnosztizálásához
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
ms.openlocfilehash: df4ec6ddbba029eb29d2440717697968f8c79302
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191067"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>Privátvégpont-kapcsolati problémák hibaelhárítása

Ez az útmutató részletes útmutatást nyújt a privát végponti kapcsolat beállításának ellenőrzéséhez és diagnosztizálásához. 

Az Azure Private Endpoint egy olyan hálózati adapter, amely privát és biztonságos kapcsolatot létesít a privát kapcsolati szolgáltatással. Ez a megoldás segít az Azure-beli számítási feladatok biztonságossá tételében azáltal, hogy a virtuális hálózatról privát kapcsolatot biztosít az Azure-szolgáltatás erőforrásaihoz. Ez hatékonyan hozza a szolgáltatásokat a virtuális hálózatba. 

A privát végpontokkal elérhető kapcsolódási forgatókönyvek 
- azonos régióból származó virtuális hálózat 
- regionálisan egymással összekötött virtuális hálózatok
- globálisan egyenrangú virtuális hálózatok
- helyszíni ügyfelek VPN-vagy Express Route-áramkörökkel

## <a name="diagnosing-connectivity-problems"></a>Kapcsolódási problémák diagnosztizálása 
Az alábbi lépések elvégzésével győződjön meg arról, hogy az összes szokásos konfiguráció a várt módon oldja meg a kapcsolati problémákat a privát végpontok beállításával.

1. Privát végpont konfigurációjának áttekintése az erőforrás tallózásával 

    a) nyissa meg a **Private link centert**

      ![Privát kapcsolati központ](./media/private-endpoint-tsg/private-link-center.png)

    b) a bal oldali navigációs panelen válassza a privát végpontok lehetőséget.
    
      ![Privát végpontok](./media/private-endpoint-tsg/private-endpoints.png)

    c) szűrje és jelölje ki a diagnosztizálni kívánt privát végpontot.

    d) a virtuális hálózat és a DNS-információk áttekintése
    
     - A kapcsolatok állapotának ellenőrzése **jóváhagyva**
     - Ellenőrizze, hogy a virtuális gép rendelkezik-e kapcsolattal a privát végpontokat üzemeltető VNet
     - Kiosztott FQDN-információ (másolás) és magánhálózati IP-cím
    
       ![VNet és DNS-konfiguráció](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. A [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) használatával áttekintheti az adatforgalmat

    a) privát végponti erőforráson válassza a **figyelő** lehetőséget.
     - Válassza ki az adatforgalom vagy a kijelentkezés lehetőséget, és tekintse át, hogy az adatai áramlanak-e a magánhálózati végponthoz való kapcsolódási kísérlet során. Körülbelül 10 perc késést várhat.
    
       ![Privát végpont telemetria ellenőrzése](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. Virtuálisgép-kapcsolatok hibáinak használata **Network Watcher**

    a) válassza ki az ügyfél virtuális gépet

    b) válassza a **kapcsolatok hibakeresése** szakaszt, majd a **Kimenő kapcsolatok** fület
    
      ![Network Watcher – kimenő kapcsolatok tesztelése](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) válassza **a Network Watcher használata a részletes kapcsolatok nyomon követéséhez** lehetőséget.
    
      ![Network Watcher – csatlakoztatási hibák](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) válassza **a teszt a teljes tartománynév alapján** lehetőséget
     - A teljes tartománynév beillesztése a magánhálózati végponti erőforrásból
     - Adjon meg egy portot (*általában 443 az Azure Storage vagy a Cosmos, 1336 for SQL..* .)

    e) kattintson a **tesztelés** gombra, és ellenőrizze a teszt eredményeit
    
      ![Network Watcher – teszteredmények](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. A tesztelési eredmények DNS-feloldásához a magánhálózati végponthoz rendelt magánhálózati IP-címnek kell tartoznia.

    a) Ha a DNS-beállítások helytelenek, tegye a következőket
     - Saját zóna használata: 
       - Győződjön meg arról, hogy az ügyfél virtuális VNet társítva van a privát zónához
       - Saját DNS zóna rekordjának áttekintése, létrehozás, ha nem létező
    
     - Egyéni DNS használata:
       - Tekintse át az ügyfél DNS-beállításait, és ellenőrizze, hogy helyes-e a DNS-konfiguráció.
       Tekintse át a [privát végpont áttekintése – DNS-konfiguráció](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration) útmutatáshoz című témakört.

    b) Ha a NSG/UDR miatt sikertelen volt a kapcsolat
     - Tekintse át a NSG kimenő szabályait, és hozzon létre megfelelő kimenő szabályokat a forgalom engedélyezéséhez
    
       ![NSG kimenő szabályai](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. Ha a kapcsolat érvényesítve lett, a csatlakozási probléma az alkalmazási rétegben található titkokkal, jogkivonatokkal és jelszavakkal kapcsolatos egyéb szempontokhoz is kapcsolódhat.
   - Ebben az esetben tekintse át a privát végponthoz társított magánhálózati kapcsolati erőforrás konfigurációját. Tekintse meg a [privát hivatkozás hibaelhárítási útmutatóját](troubleshoot-private-link-connectivity.md). 

6. Ha a probléma továbbra is megoldatlan, és a kapcsolati probléma továbbra is fennáll, forduljon az [Azure támogatási](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) csapatához. 

## <a name="next-steps"></a>Következő lépések

 * [Hozzon létre egy privát végpontot a frissített alhálózaton (Azure Portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Privát hivatkozás hibaelhárítási útmutatója](troubleshoot-private-link-connectivity.md)
