---
title: Az Azure Cosmos DB etcd API bemutatása
description: Ez a cikk ismerteti az Azure Cosmos DB API etcd áttekintése és főbb előnyei
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205805"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Az Azure Cosmos DB etcd API (előzetes verzió) bemutatása

Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatás alapvető fontosságú alkalmazásokhoz. Kulcsrakész globális disztribúciót, rugalmas átviteli sebesség és tárterület, egyszámjegyű ezredmásodperces késés 99 százalékon méretezést biztosít, és azt magas rendelkezésre állású, piacvezető SLA által garantált.

[Etcd](https://github.com/etcd-io/etcd) egy elosztott kulcs/érték tároló. A [Kubernetes](https://kubernetes.io/), etcd állapota és a Kubernetes-fürtök konfigurációjának tárolására szolgál. Biztosítja a rendelkezésre állási, megbízhatóságát és teljesítményét etcd elengedhetetlen az általános fürt állapotát, méretezhetőség, rugalmasság rendelkezésre állási és Kubernetes-fürt teljesítményét. 

A etcd API az Azure Cosmos DB lehetővé teszi, hogy a háttérrendszer tárolóként az Azure Cosmos DB [Azure-beli Kubernetes](../aks/index.yml). az Azure Cosmos DB API etcd jelenleg előzetes verzióban érhető el. Az Azure Cosmos DB a etcd protokoll valósítja meg. Etcd API az Azure Cosmos DB, az automatikusan a fejlesztőket magas megbízhatóságú [elérhető](high-availability.md), [globálisan elosztott](distribute-data-globally.md) Kubernetes. Ez az API lehetővé teszi, hogy a Kubernetes állapot-felügyeleti méretezhetők egy teljes körűen felügyelt felhőbeli natív PaaS-szolgáltatás a fejlesztők számára. 

> [!NOTE]
> Ellentétben más az Azure Cosmos DB API etcd API-fiók az Azure Portalon, parancssori felület vagy az SDK-k nem használhatók. Csak a Resource Manager-sablon üzembe helyezésével helyezheti üzembe a etcd API-fiók részletes lépéseiért lásd: [kiépítéséről Azure-beli Kubernetes az Azure Cosmos DB](bootstrap-kubernetes-cluster.md) cikk. Az Azure Cosmos DB etcd API jelenleg korlátozott előzetes verzióban érhető el. Is [regisztráljon az előzetes verzióra](https://aka.ms/cosmosetcdapi-signup), bejelentkezési űrlap kitöltésével.

## <a name="wire-level-compatibility"></a>Átviteli szintű kompatibilitása

Az Azure Cosmos DB valósítja meg a 3-as verziójú etcd vonalprotokollt, és lehetővé teszi a [fő csomóponttal](https://kubernetes.io/docs/concepts/overview/components/) API-kiszolgálók által használt Azure Cosmos DB, ugyanúgy, mint a helyileg telepített etcd környezetben tenné azt. A etcd API támogatja a TLS kölcsönös hitelesítést. 

Az alábbi diagram egy Kubernetes-fürt összetevői láthatók. A fürt mester az API-kiszolgáló használ az Azure Cosmos DB etcd API, helyileg telepített etcd helyett. 

![Az Azure Cosmos DB a etcd-protokoll megvalósítása](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Főbb előnyök

### <a name="no-etcd-operations-management"></a>Nincs etcd műveletek kezelése

Teljes körűen felügyelt natív felhőalapú szolgáltatásként az Azure Cosmos DB szükségtelenné beállítása és kezelése etcd Kubernetes fejlesztők számára. Az Azure Cosmos DB a etcd API méretezhető, magas rendelkezésre állású, hibatűrő, és nagy teljesítményt tesz elérhetővé. Több csomóponton, a működés közbeni végrehajtása a replikáció beállítása járó többletterhelést frissíti, a biztonsági javítások, és a etcd állapotának monitorozása az Azure Cosmos DB által kezelt.

### <a name="global-distribution--high-availability"></a>Globális terjesztés és magas rendelkezésre állás 

Etcd API használatával az Azure Cosmos DB 99,99 %-os rendelkezésre állást garantál az adatok olvasásokhoz, és ír egy egyetlen régióban, valamint 99,999 %-os rendelkezésre állást több régióban. 

### <a name="elastic-scalability"></a>Rugalmas méretezhetőség

Az Azure Cosmos DB rugalmas méretezhetőséget kínál olvasási és írási kérelmeket a különböző régiók között.
A Kubernetes-fürt növekedésével a etcd API-fiókot az Azure Cosmos DB rugalmasan méretezhető állásidő nélkül. Etcd adatok tárolására az Azure Cosmos DB helyett a fő Kubernetes-csomópontokon is lehetővé teszi több rugalmas méretezés fő csomóponttal. 

### <a name="security--enterprise-readiness"></a>A biztonság és a vállalati készültségi

Etcd adatokat az Azure Cosmos DB tárolja, amikor Kubernetes fejlesztők automatikusan megkapja a [beépített titkosítás inaktív állapotban](database-encryption-at-rest.md), [tanúsítványok és -megfelelőségi](compliance.md), és [biztonsági mentése és visszaállítása képességek](online-backup-and-restore.md) Azure Cosmos DB által támogatott. 

## <a name="next-steps"></a>További lépések

* [Azure-beli Kubernetes az Azure Cosmos DB használatával](bootstrap-kubernetes-cluster.md)
* [Az Azure Cosmos DB előnyei](introduction.md)
* [Az AKS motor a rövid útmutató](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)