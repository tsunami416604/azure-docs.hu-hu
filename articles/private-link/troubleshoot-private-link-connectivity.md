---
title: Az Azure privát kapcsolati szolgáltatás csatlakoztathatósági problémáinak elhárítása
description: Részletes útmutatás a privát kapcsolat diagnosztizálásához
services: private-link
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
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539467"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Az Azure privát kapcsolati szolgáltatás csatlakoztathatósági problémáinak elhárítása

Ez a cikk lépésenkénti útmutatást nyújt az Azure Private Link beállításhoz való kapcsolódás érvényesítéséhez és diagnosztizálásához.

Az Azure Private Link segítségével az Azure platformszolgáltatás (PaaS) szolgáltatásként érhető el, például az Azure Storage, az Azure Cosmos DB és az Azure SQL Database, valamint az Azure által üzemeltetett ügyfél- vagy partnerszolgáltatások a virtuális hálózat egy privát végpontján keresztül. A virtuális hálózat és a szolgáltatás közötti forgalom áthalad a Microsoft gerinchálózaton keresztül, ami kiküszöböli a nyilvános internetről származó kitettséget. Saját privát kapcsolatszolgáltatást is létrehozhat a virtuális hálózatban, és magánúton is eljuttathatja az ügyfelekhez.

Engedélyezheti a szolgáltatás, amely fut a standard szint az Azure Load Balancer a private link hozzáférés. A szolgáltatás fogyasztói létrehozhatnak egy privát végpontot a virtuális hálózatukon belül, és leképezhetik a szolgáltatáshoz, hogy privát módon érhessék el.

A Privát hivatkozásban elérhető kapcsolódási forgatókönyvek a következők:

- Virtuális hálózat ugyanabból a régióból
- Regionálisan társviszonyban lévő virtuális hálózatok
- Globálisan társviszonyban lévő virtuális hálózatok
- A helyszíni ügyfelek VPN- vagy Azure ExpressRoute-áramkörökön keresztül

## <a name="deployment-troubleshooting"></a>Központi telepítés – hibaelhárítás

Tekintse át a [hálózati házirendek letiltásával](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) kapcsolatos információkat a privát kapcsolatszolgáltatáson olyan esetek hibaelhárításához, amikor nem tudja kiválasztani a forrás IP-címét a privát kapcsolatszolgáltatáshoz választott alhálózatból.

Győződjön meg arról, hogy a **privateLinkServiceNetworkPolicies** beállítás le van tiltva ahhoz az alhálózathoz, amelyről a forrás IP-címét választja.

## <a name="diagnose-connectivity-problems"></a>Kapcsolódási problémák diagnosztizálása

Ha kapcsolódási problémákat tapasztal a privát kapcsolat beállításával kapcsolatban, tekintse át ezeket a lépéseket, és győződjön meg arról, hogy a szokásos konfigurációk a várt módon vannak.If you experience connectivity problems with your private link setup, review these steps to make sure the all the usual configurations are as expected.

1. Tekintse át a Privát hivatkozás konfigurációját az erőforrás böngészésével.

    a. Nyissa meg a **Privát hivatkozási központot.**

      ![Privát hivatkozási központ](./media/private-link-tsg/private-link-center.png)

    b. A bal oldali ablaktáblán válassza a **Privát csatolási szolgáltatások lehetőséget.**

      ![Privát kapcsolati szolgáltatások](./media/private-link-tsg/private-link-service.png)

    c. Szűrje ki és válassza ki a diagnosztizálni kívánt privát kapcsolatszolgáltatást.

    d. Tekintse át a privát végpontkapcsolatokat.
     - Győződjön meg arról, hogy a privát végpont, amelyről kapcsolatot keres, **jóváhagyott** kapcsolatállapottal van felsorolva.
     - Ha az állapot **függőben**van, jelölje ki, és hagyja jóvá.

       ![Privát végpontkapcsolatok](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Lépjen a név kiválasztásával arra a privát végpontra, amelyből csatlakozik. Győződjön meg arról, hogy a kapcsolat állapota jóváhagyva ként jelenik **meg.**

       ![Privát végpontkapcsolat – áttekintés](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - A jóváhagyás után próbálkozzon újra a kapcsolattal.

    e. Tekintse át az **Alias** lapot az **Áttekintés** lapon és **az Erőforrás-azonosítót** a **Tulajdonságok** lapon.
     - Győződjön meg arról, hogy az **alias-** és **erőforrás-azonosító** adatai megegyeznek a szolgáltatás privát végpontjának létrehozásához használt **alias-** és **erőforrás-azonosítóval.**

       ![Aliasadatok ellenőrzése](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Erőforrás-azonosító adatainak ellenőrzése](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Tekintse át a **Láthatósági** információkat az **Áttekintés** lapon.
     - Győződjön meg arról, hogy az előfizetés a **Láthatóság** hatóköralá tartozik.

       ![Láthatósági adatok ellenőrzése](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Tekintse át a **Terheléselosztás** adatait az **Áttekintés** lapon.
     - A terheléselosztó link kiválasztásával a terheléselosztóhoz léphet.

       ![Terheléselosztási adatok ellenőrzése](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Győződjön meg arról, hogy a terheléselosztó beállításai az ön elvárásainak megfelelően vannak konfigurálva.
       - Tekintse át **az előtér IP-konfigurációját.**
       - Tekintse át **a háttérmedencéket.**
       - Tekintse át **a terheléselosztási szabályokat.**

       ![A terheléselosztó tulajdonságainak ellenőrzése](./media/private-link-tsg/pls-ilb-properties.png)

     - Győződjön meg arról, hogy a terheléselosztó az előző beállításoknak a szerint működik.
       - Válasszon ki egy virtuális gép bármely alhálózat más, mint az alhálózat, ahol a terheléselosztó háttérkészlet érhető el.
       - Próbálja meg elérni a terheléselosztó előtér-elejét az előző virtuális gépről.
       - Ha a kapcsolat a terheléselosztási szabályok szerint a háttérkészlethez kerül, a terheléselosztó működőképes.
       - Tekintse át a terheléselosztó metrikát az Azure Monitoron keresztül, és ellenőrizze, hogy az adatok a terheléselosztón keresztül áramlanak-e.

1. Az [Azure Monitor segítségével](https://docs.microsoft.com/azure/azure-monitor/overview) ellenőrizze, hogy az adatok áramlanak-e.

    a. A privát kapcsolat szolgáltatás erőforrás, válassza **metrikák**.
     - Válassza **a Be-** vagy **A belegabasi vagy a Kimenő bájtok lehetőséget.**
     - Annak megtekintése, hogy az adatok áramlanak-e, amikor megpróbál csatlakozni a privát kapcsolat szolgáltatáshoz. Körülbelül 10 perces késésre számíthat.

       ![Privát kapcsolatszolgáltatás mutatóinak ellenőrzése](./media/private-link-tsg/pls-metrics.png)

1. Lépjen kapcsolatba az [Azure támogatási csapatával,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ha a probléma továbbra is megoldatlan, és továbbra is fennáll kapcsolódási probléma.

## <a name="next-steps"></a>További lépések

 * [Privát kapcsolatszolgáltatás (CLI) létrehozása](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Az Azure Private Endpoint hibaelhárítási útmutatója](troubleshoot-private-endpoint-connectivity.md)
