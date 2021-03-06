---
title: Az Azure privát végpont kapcsolati problémáinak hibaelhárítása
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
ms.openlocfilehash: f861f9efa6ecc1886647ed6c460b6718ff97e8a1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522328"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Az Azure privát végpont kapcsolati problémáinak hibaelhárítása

Ez a cikk részletes útmutatást nyújt az Azure Private Endpoint connectivity telepítőjének ellenőrzéséhez és diagnosztizálásához.

Az Azure Private Endpoint egy olyan hálózati adapter, amely privát és biztonságos kapcsolatot létesít a privát kapcsolati szolgáltatással. Ez a megoldás segít az Azure-beli számítási feladatok biztonságossá tételében azáltal, hogy a virtuális hálózatról privát kapcsolatot biztosít az Azure-szolgáltatás erőforrásaihoz. Ez a megoldás hatékonyan hozza ezeket a szolgáltatásokat a virtuális hálózatra.

Itt láthatók a magánhálózati végponttal elérhető csatlakozási forgatókönyvek:

- Azonos régióból származó virtuális hálózat
- Regionálisan egymással összekötött virtuális hálózatok
- Globálisan egyenrangú virtuális hálózatok
- Helyszíni ügyfelek VPN-en vagy Azure ExpressRoute-áramkörökben

## <a name="diagnose-connectivity-problems"></a>Kapcsolódási problémák diagnosztizálása 

Tekintse át ezeket a lépéseket annak biztosításához, hogy az összes szokásos konfiguráció a várt módon oldja fel a kapcsolódási problémákat a privát végpontok beállításával.

1. Tekintse át a privát végpontok konfigurációját az erőforrás tallózásával.

    a. Nyissa meg a **Private link centert**.

      ![Privát kapcsolati központ](./media/private-endpoint-tsg/private-link-center.png)

    b. A bal oldali panelen válassza a **privát végpontok** lehetőséget.
    
      ![Privát végpontok](./media/private-endpoint-tsg/private-endpoints.png)

    c. Szűrje és jelölje ki a diagnosztizálni kívánt privát végpontot.

    d. Tekintse át a virtuális hálózat és a DNS-információkat.
     - Ellenőrizze, hogy a rendszer **jóváhagyja**-e a kapcsolatok állapotát.
     - Győződjön meg arról, hogy a virtuális gép rendelkezik a magánhálózati végpontokat üzemeltető virtuális hálózattal.
     - Győződjön meg arról, hogy a teljes tartománynév-információ (másolás) és a magánhálózati IP-cím hozzá van rendelve.
    
       ![Virtuális hálózat és DNS-konfiguráció](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. A [Azure monitor](../azure-monitor/overview.md) használatával megtekintheti, hogy folyik-e az adatforgalom.

    a. A privát végpont erőforráson válassza a **figyelés** lehetőséget.
     - Válassza ki az adatelemet vagy **a** **kimenő adatvesztést**. 
     - A magánhálózati végponthoz való kapcsolódáskor megtekintheti, hogy vannak-e adatfolyamok. Körülbelül 10 perc késés várható.
    
       ![Privát végpont telemetria ellenőrzése](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Virtuálisgép- **kapcsolatok hibakeresése** az Azure Network Watcher használatával.

    a. Válassza ki az ügyfél virtuális gépet.

    b. Válassza a **kapcsolódási hibák**, majd a **Kimenő kapcsolatok** fület.
    
      ![Network Watcher – kimenő kapcsolatok tesztelése](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Válassza **a Network Watcher használata lehetőséget a kapcsolatok részletes nyomkövetéséhez**.
    
      ![Network Watcher – csatlakoztatási hibák](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Válassza **a teszt a teljes tartománynév alapján** lehetőséget.
     - Illessze be a teljes tartománynevet a privát végponti erőforrásból.
     - Adjon meg egy portot. Az Azure Storage vagy a Azure Cosmos DB és a 1336 for SQL esetében általában az 443-et használja.

    e. Válassza a **teszt** lehetőséget, és ellenőrizze a teszt eredményeit.
    
      ![Network Watcher – teszteredmények](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. A teszt eredményeinek DNS-feloldásához ugyanazt a magánhálózati IP-címet kell rendelni, mint a privát végponthoz.

    a. Ha a DNS-beállítások helytelenek, kövesse az alábbi lépéseket:
     - Ha saját zónát használ: 
       - Győződjön meg arról, hogy az ügyfél virtuális gép virtuális hálózata hozzá van rendelve a saját zónához.
       - Ellenőrizze, hogy létezik-e a saját DNS-zóna rekordja. Ha nem létezik, hozza létre.
     - Ha egyéni DNS-t használ:
       - Tekintse át az egyéni DNS-beállításokat, és ellenőrizze, hogy helyes-e a DNS-konfiguráció.
       Útmutatásért lásd a [privát végpont áttekintése: DNS-konfiguráció](./private-endpoint-overview.md#dns-configuration)című témakört.

    b. Ha a hálózati biztonsági csoportok (NSG) vagy a felhasználó által megadott útvonalak miatt sikertelen volt a kapcsolat:
     - Tekintse át a NSG kimenő szabályait, és hozza létre a megfelelő kimenő szabályokat a forgalom engedélyezéséhez.
    
       ![NSG kimenő szabályai](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Ha a kapcsolat érvényesített eredményekkel rendelkezik, a csatlakozási probléma az alkalmazási rétegben található titkokkal, jogkivonatokkal és jelszavakkal kapcsolatos egyéb szempontokhoz is kapcsolódhat.
   - Ebben az esetben tekintse át a privát végponthoz társított magánhálózati kapcsolati erőforrás konfigurációját. További információt az [Azure Private link hibaelhárítási útmutatójában](troubleshoot-private-link-connectivity.md)talál.

1. Ha a probléma továbbra is megoldatlan, forduljon az [Azure ügyfélszolgálatához](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , és a kapcsolati probléma továbbra is fennáll.

## <a name="next-steps"></a>Következő lépések

 * [Hozzon létre egy privát végpontot a frissített alhálózaton (Azure Portal)](./create-private-endpoint-portal.md)
 * [Az Azure Private link hibaelhárítási útmutatója](troubleshoot-private-link-connectivity.md)