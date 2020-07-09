---
title: A Azure Cosmos DB etcd API bemutatása
description: Ez a cikk a etcd API áttekintését és főbb előnyeit ismerteti Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85118168"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>A Azure Cosmos DB etcd API bemutatása (előzetes verzió)

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Kulcsrakész globális elosztást, rugalmas skálázást biztosít az átviteli sebesség és a tárolás terén, a esetek 99% percentilis egyszámjegyű ezredmásodperces késését és a magas rendelkezésre állást, és mindezt piacvezető SLA-k biztosítják.

A [Etcd](https://github.com/etcd-io/etcd) egy elosztott kulcs/érték tároló. A [Kubernetes](https://kubernetes.io/)-ben a Etcd a Kubernetes-fürtök állapotának és konfigurációjának tárolására szolgál. A etcd rendelkezésre állásának, megbízhatóságának és teljesítményének biztosítása létfontosságú a Kubernetes-fürtök általános állapotának, méretezhetőségének, rugalmasságának és teljesítményének biztosításához.

A Azure Cosmos DB etcd API-val a Azure Cosmos DB használhatja a háttérbeli tárolóként az Azure Kubernetes. a Azure Cosmos DB etcd API jelenleg előzetes verzióban érhető el. A Azure Cosmos DB megvalósítja a etcd átviteli protokollt. A etcd API-val Azure Cosmos DB a fejlesztők automatikusan megbízható, [elérhető](high-availability.md), [globálisan elosztott](distribute-data-globally.md) Kubernetes kapnak. Ez az API lehetővé teszi a fejlesztők számára, hogy a teljes körűen felügyelt Felhőbeli natív Pásti szolgáltatáson keresztül Kubernetes az állami felügyeletet. 

> [!NOTE]
> A Azure Cosmos DB egyéb API-jai eltérően a etcd API-fiók nem helyezhető üzembe a Azure Portal, a CLI vagy az SDK-kon keresztül. Etcd API-fiókot csak a Resource Manager-sablon üzembe helyezésével lehet kiépíteni; a részletes lépésekért lásd: [Az Azure Kubernetes](bootstrap-kubernetes-cluster.md) üzembe helyezése Azure Cosmos db cikkben. Azure Cosmos DB etcd API jelenleg korlátozott előzetes verzióban érhető el. A regisztrációs űrlap kitöltésével regisztrálhat [az előzetes](https://aka.ms/cosmosetcdapi-signup)verzióra.

## <a name="wire-level-compatibility"></a>Vezetékes szintű kompatibilitás

Azure Cosmos DB megvalósítja a etcd 3-as verziójának vezetékes protokollját, és lehetővé teszi, hogy a [főcsomópont](https://kubernetes.io/docs/concepts/overview/components/) API-kiszolgálói a Azure Cosmos db ugyanúgy használják, mint a helyileg telepített etcd-környezetekben. A etcd API támogatja a TLS kölcsönös hitelesítést. 

Az alábbi ábrán egy Kubernetes-fürt összetevői láthatók. A fürt főkiszolgálóján az API-kiszolgáló a helyileg telepített etcd helyett a Azure Cosmos DB etcd API-t használja. 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="A etcd-protokoll implementálása Azure Cosmos DB" border="false":::

## <a name="key-benefits"></a>Főbb előnyök

### <a name="no-etcd-operations-management"></a>Nincs etcd Operations Management

Teljes körűen felügyelt natív felhőalapú szolgáltatásként Azure Cosmos DB eltávolítja a Kubernetes-fejlesztőket a etcd beállításához és kezeléséhez. A Azure Cosmos DB etcd API méretezhető, magas rendelkezésre állású, hibatűrő, és nagy teljesítményt nyújt. A replikáció több csomóponton történő beállítása, a működés közbeni frissítések, a biztonsági javítások és a etcd állapotának figyelése Azure Cosmos DB segítségével történik.

### <a name="global-distribution--high-availability"></a>Globális terjesztés & magas rendelkezésre állás 

A etcd API használatával a Azure Cosmos DB 99,99%-os rendelkezésre állást biztosít az adatolvasások és-írások számára egyetlen régióban, és 99,999%-os rendelkezésre állást több régióban. 

### <a name="elastic-scalability"></a>Rugalmas méretezhetőség

Azure Cosmos DB rugalmas méretezhetőséget biztosít az olvasási és írási kérelmekhez különböző régiókban.
Mivel a Kubernetes-fürt növekszik, a etcd API Azure Cosmos DB-fiók rugalmasan méretezhető, állásidő nélkül. A etcd-adatok Azure Cosmos DBban való tárolása a Kubernetes főcsomópontok helyett a főcsomópontok közötti rugalmasabb skálázást is lehetővé teszi. 

### <a name="security--enterprise-readiness"></a>Biztonság & nagyvállalati készültség

Ha a etcd-adatok tárolása Azure Cosmos DB történik, a Kubernetes-fejlesztők automatikusan beszerezhetik a [beépített titkosítást a REST, a](database-encryption-at-rest.md) [minősítések és a megfelelőség szempontjából](compliance.md), valamint a Azure Cosmos db által támogatott [biztonsági mentési és visszaállítási képességeket](online-backup-and-restore.md) . 

## <a name="next-steps"></a>További lépések

* [Az Azure Kubernetes használata Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [A Azure Cosmos DB főbb előnyei](introduction.md)
* [Az KABAi motor rövid útmutatója](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)