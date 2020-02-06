---
title: Az Azure Private link kapcsolódási problémáinak elhárítása
description: Részletes útmutató a privát kapcsolati kapcsolatok diagnosztizálásához
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
ms.openlocfilehash: 667fa1c85c63ffb87e49c4bf99112f57d0c85a72
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031919"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>A magánhálózati kapcsolati szolgáltatás kapcsolódási problémáinak elhárítása

Ez az útmutató lépésről lépésre bemutatja, hogyan lehet érvényesíteni és diagnosztizálni a privát kapcsolati szolgáltatás beállítását. 

Az Azure Private link lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage, a Azure Cosmos DB és a SQL Database) és az Azure által üzemeltetett ügyfelek/partnerek szolgáltatásainak elérését a virtuális hálózat privát végpontján keresztül. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Létrehozhatja saját privát kapcsolati szolgáltatását is a virtuális hálózaton (VNet), és saját maga is elvégezheti az ügyfelek számára. 

Engedélyezheti az Azure standard Load Balancer mögött futó szolgáltatást a privát kapcsolatokhoz való hozzáféréshez. A szolgáltatás felhasználói létrehozhatnak egy privát végpontot a virtuális hálózaton belül, és leképezhetők a szolgáltatásba, hogy magánjellegű módon férhessenek hozzá.

A Private link Service-ben elérhető kapcsolódási forgatókönyvek
- azonos régióból származó virtuális hálózat 
- regionálisan egymással összekötött virtuális hálózatok
- globálisan egyenrangú virtuális hálózatok
- helyszíni ügyfelek VPN-vagy Express Route-áramkörökkel

## <a name="deployment-troubleshooting"></a>Üzembe helyezés hibaelhárítása

A hibaelhárítási esetekben tekintse át a [hálózati házirendek letiltásával kapcsolatos információkat a privát kapcsolat szolgáltatásban](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) , ahol nem tudja kijelölni a forrás IP-címet a privát kapcsolati szolgáltatáshoz választott alhálózatból.

Győződjön meg arról, hogy a **privateLinkServiceNetworkPolicies** beállítás le van tiltva azon alhálózat esetében, amelyből a forrás IP-címet kiválasztja.

## <a name="diagnosing-connectivity-problems"></a>Kapcsolódási problémák diagnosztizálása

Ha a magánhálózati kapcsolat beállításával kapcsolatban problémák léptek fel, az alábbi lépésekkel ellenőrizze, hogy az összes szokásos konfiguráció a várt módon működik-e.

1. Privát hivatkozás szolgáltatás konfigurációjának áttekintése az erőforrás tallózásával 

    a) nyissa meg a **Private link centert**

      ![Privát kapcsolati központ](./media/private-link-tsg/private-link-center.png)

    b) a bal oldali navigációs panelen válassza a **privát kapcsolati szolgáltatások** lehetőséget.

      ![Private link Services](./media/private-link-tsg/private-link-service.png)

    c) szűrő és válassza ki a diagnosztizálni kívánt privát kapcsolati szolgáltatást

    d) tekintse át a magánhálózati végpontok kapcsolatait
     - Győződjön meg arról, hogy az a magánhálózati végpont, amelyhez csatlakozni szeretne, szerepel a **jóváhagyott** kapcsolati állapot listáján. 
     - Ha **függőben**van, válassza ki és hagyja jóvá. 

       ![Magánhálózati végpontok kapcsolatai](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Navigáljon ahhoz a privát végponthoz, amelyhez csatlakozni kíván, ehhez kattintson a névre. Győződjön meg arról, hogy a kapcsolatok állapota **jóváhagyva**értékre van állítva.

       ![Privát végpontok kapcsolatainak áttekintése](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - A két oldal jóváhagyása után próbálkozzon újra a kapcsolattal.

    e) az Áttekintés lapon és az erőforrás- **azonosítóban** található **alias** áttekintése a Tulajdonságok lapon 
     - Győződjön meg arról, hogy az **alias/erőforrás-azonosító** megegyezik azzal az **alias/erőforrás-azonosítóval** , amelyet a szolgáltatáshoz tartozó privát végpont létrehozásához használ. 

       ![Alias ellenőrzése](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Erőforrás-azonosító ellenőrzése](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) tekintse át a láthatósági (áttekintés) információkat
     - Győződjön meg arról, hogy az előfizetése a **láthatósági** hatókör alá esik

       ![Láthatóság ellenőrzése](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) a Load Balancer (áttekintés) adatainak áttekintése
     - A Load Balancer hivatkozásra kattintva megnyithatja a terheléselosztó hivatkozást

       ![Load Balancer ellenőrzése](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Győződjön meg arról, hogy a Load Balancer beállítások a saját elvárásai szerint vannak konfigurálva
       - Előtér-IP-konfiguráció áttekintése
       - Háttérbeli készletek áttekintése
       - Terheléselosztási szabályok áttekintése

       ![Load Balancer tulajdonságainak ellenőrzése](./media/private-link-tsg/pls-ilb-properties.png)

     - Győződjön meg arról, hogy Load Balancer a fenti beállítások szerint működik
       - Válasszon ki egy virtuális gépet az alhálózaton kívül minden olyan alhálózaton, ahol a Load Balancer háttér-készlet elérhető
       - Próbálja meg elérni a terheléselosztó előtér-végpontját a fenti virtuális gépről
       - Ha a kapcsolat terheléselosztási szabályok szerint a háttér-készletre kerül, a terheléselosztó működőképes
       - Áttekintheti a Load Balancer metrikát a Azure Monitor segítségével, hogy megtudja, az adatok a terheléselosztó segítségével áramlanak-e

2. A [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) használatával áttekintheti az adatforgalmat

    a) a Private link SErvice-erőforráson válassza a **metrikák** lehetőséget.
     - **Bájtok** vagy **bájtok** kiválasztása
     - Áttekintheti az adatforgalmat, amikor megpróbál csatlakozni a privát kapcsolati szolgáltatáshoz. Körülbelül 10 perc késést várhat.

       ![Privát kapcsolati szolgáltatás metrikáinak ellenőrzése](./media/private-link-tsg/pls-metrics.png)

3. Ha a probléma továbbra is megoldatlan, és a kapcsolati probléma továbbra is fennáll, forduljon az [Azure támogatási](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) csapatához. 

## <a name="next-steps"></a>Következő lépések

 * [Privát kapcsolati szolgáltatás (CLI) létrehozása](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [Privát végpont hibaelhárítási útmutatója](https://docs.microsoft.com/azure/private-link/private-endpoint-connectivity-troubleshooting)
