---
title: Hibaelhárítás Azure Cosmos DB HTTP 408 vagy időtúllépési hibák a .NET SDK-val
description: .NET SDK-kérelmek időtúllépési kivételének diagnosztizálása és kijavítása
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 29b0c6237ae04ea5da9ec496498fc7c20890b173
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294395"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Azure Cosmos DB .NET SDK-kérelem időtúllépésének diagnosztizálása és megoldása
A HTTP 408-hiba akkor fordul elő, ha az SDK nem tudta befejezni a kérelmet az időtúllépési korlát megkezdése előtt.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Az alábbi lista a kérelmek időtúllépési kivételeinek ismert okait és megoldásait tartalmazza.

### <a name="1-high-cpu-utilization-most-common-case"></a>1. magas CPU-kihasználtság (leggyakoribb eset)
Az optimális késés érdekében javasolt, hogy a CPU-használat körülbelül 40% legyen. Azt javasoljuk, hogy a maximális (nem átlagos) CPU-kihasználtság figyeléséhez használja a 10 másodperces időintervallumot. A CPU-tüskék gyakoribbak a több partíciós lekérdezésekben, ahol több kapcsolat is lehetséges egyetlen lekérdezéshez.

#### <a name="solution"></a>Megoldás:
Az SDK-t használó ügyfélalkalmazás méretezését fel kell mérni.

### <a name="2-socket--port-availability-might-be-low"></a>2. lehet, hogy a szoftvercsatorna/port rendelkezésre állása alacsony
Az Azure-ban való futtatáskor a .NET SDK-t használó ügyfelek elérhetik az Azure SNAT (PAT) portjának kimerülését.

#### <a name="solution-1"></a>1. megoldás:
Kövesse a [SNAT-porthoz tartozó kimerültség útmutatóját](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>2. megoldás:
Ha HTTP-proxyt használ, győződjön meg arról, hogy az képes támogatni az SDK-ban konfigurált kapcsolatok számát `ConnectionPolicy` .
Ellenkező esetben a csatlakoztatási problémákkal szembesül.

### <a name="3-creating-multiple-client-instances"></a>3. több ügyfél-példány létrehozása
Több ügyfél-példány létrehozása a kapcsolati problémákhoz és időtúllépéshez vezethet.

#### <a name="solution"></a>Megoldás:
Kövesse a [teljesítménnyel kapcsolatos tippeket](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage), és használjon egyetlen CosmosClient-példányt egy teljes folyamaton belül.

### <a name="4-hot-partition-key"></a>4. gyors partíciós kulcs
A Azure Cosmos DB a teljes kiosztott átviteli sebességet egyenletesen osztja el a fizikai partíciók között. Ha van egy gyakori partíció, a fizikai partíciók egy vagy több logikai partíciója a fizikai partíció RU/s-t használja fel, míg az egyéb fizikai partíciókon lévő RU/s nem lesz használatban. Ez azt eredményezi, hogy a felhasznált RU/s mennyisége kevesebb lesz, mint az adatbázis vagy a tároló összesített kiépített RU/s-száma, de továbbra is megtekintheti a szabályozást (429s) a gyors logikai partíciós kulcsra vonatkozó kérelmeknél. A [normalizált ru](monitor-normalized-request-units.md) -használati metrika használatával ellenőrizze, hogy a számítási feladatok egy gyors partícióval találkoznak-e. 

#### <a name="solution"></a>Megoldás:
Válasszon egy jó partíciós kulcsot, amely egyenletesen osztja el a kérelmek mennyiségét és tárolását. Megtudhatja, hogyan [módosíthatja a partíciós kulcsot](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="5-high-degree-of-concurrency"></a>5. magas fokú párhuzamosság
Az alkalmazás magas szintű párhuzamosságot eredményez, ami a csatornán való versenyhez vezethet

#### <a name="solution"></a>Megoldás:
Az SDK-t használó ügyfélalkalmazás méretezését fel kell mérni.

### <a name="6-large-requests-andor-responses"></a>6. nagyméretű kérelmek és/vagy válaszok
A nagyméretű kérelmek vagy válaszok a csatornán kívüli blokkolást okozhatnak a csatornán, és súlyosbítják a versengés mértékét, akár viszonylag alacsony fokú párhuzamosságtal is.

#### <a name="solution"></a>Megoldás:
Az SDK-t használó ügyfélalkalmazás méretezését fel kell mérni.

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. a meghibásodási arány Cosmos DB SLA-n belül van
Az alkalmazásnak képesnek kell lennie az átmeneti hibák kezelésére, és szükség esetén újra kell próbálkoznia. 408 a rendszer nem próbálkozik újra, mert a létrehozási útvonalakon nem lehet tudni, hogy a szolgáltatás hozta-e létre az adott tételt, vagy sem. Ha ugyanezt az elemeket újra elküldi a létrehozáshoz, a rendszer ütközést okoz. A felhasználói alkalmazások üzleti logikája egyéni logikával rendelkezhet az ütközések kezeléséhez, ami egy meglévő elem és a Create újrapróbálkozás közötti ütközések miatt megszakadhat.

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. a meghibásodási arány sérti a Cosmos DB SLA-t
Vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

## <a name="next-steps"></a>További lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md)
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md) teljesítményével kapcsolatos irányelvek ismertetése