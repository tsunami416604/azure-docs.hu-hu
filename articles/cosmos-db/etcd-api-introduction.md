---
title: Bevezetés az Azure Cosmos DB etcd API-ba
description: Ez a cikk áttekintést és az etcd API legfontosabb előnyeit tartalmazza az Azure Cosmos DB-ben
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498596"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Bevezetés az Azure Cosmos DB etcd API-ba (előzetes verzió)

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Kulcsrakész globális disztribúciót, az átviteli és tárolási műveletek rugalmas méretezését, a 99. percentilis egyszámjegyű ezredmásodperces késéseit és a garantált magas rendelkezésre állást kínálja, amelyeket az iparágvezető SLA-k támogatnak.

[Etcd](https://github.com/etcd-io/etcd) egy elosztott kulcs/érték tároló. A [Kubernetes,](https://kubernetes.io/)etcd tárolja az állam és a konfiguráció a Kubernetes fürtök. Az etcd rendelkezésre állásának, megbízhatóságának és teljesítményének biztosítása elengedhetetlen a fürt általános állapotához, méretezhetőségéhez, rugalmasságához és a Kubernetes-fürtök teljesítményéhez. 

Az Azure Cosmos DB etcd API-ja lehetővé teszi, hogy az Azure Cosmos DB-t használja az [Azure Kubernetes háttértárolójaként.](../aks/index.yml) etcd API az Azure Cosmos DB jelenleg előzetes verzióban. Az Azure Cosmos DB megvalósítja az etcd vezetékes protokollt. Az Azure Cosmos DB etcd API-jával a fejlesztők automatikusan rendkívül megbízható, [elérhető](high-availability.md), [globálisan elosztott](distribute-data-globally.md) Kubernetes-t kapnak. Ez az API lehetővé teszi a fejlesztők számára a Kubernetes állapotfelügyeletének méretezését egy teljes körűen felügyelt natív PaaS-szolgáltatáson. 

> [!NOTE]
> Az Azure Cosmos DB más API-któl eltérően nem hozhat létre egy etcd API-fiókot az Azure Portalon, CLI-n vagy SDK-kon keresztül. Egy etcd API-fiók kiépítése csak az Erőforrás-kezelő sablon telepítésével; Részletes lépések: [Azure Kubernetes kiépítése az Azure Cosmos DB cikkével.](bootstrap-kubernetes-cluster.md) Az Azure Cosmos DB etcd API jelenleg korlátozott előzetes verzióban érhető el. Az [előzetes verzióra](https://aka.ms/cosmosetcdapi-signup)a regisztrációs űrlap kitöltésével regisztrálhat.

## <a name="wire-level-compatibility"></a>Vezetékszint-kompatibilitás

Az Azure Cosmos DB megvalósítja az etcd 3-as verzió vezetékprotokollját, és lehetővé teszi, hogy a [fő csomópont](https://kubernetes.io/docs/concepts/overview/components/) API-kiszolgálói ugyanúgy használják az Azure Cosmos DB-t, mint egy helyileg telepített etcd környezetben. Az etcd API támogatja a TLS kölcsönös hitelesítést. 

Az alábbi ábra egy Kubernetes-fürt összetevőit mutatja be. A fürtfőkiszolgálón az API-kiszolgáló az Azure Cosmos DB etcd API-t használja a helyileg telepített etcd helyett. 

![Az Azure Cosmos DB megvalósítja az etcd vezetékprotokollt](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Főbb előnyök

### <a name="no-etcd-operations-management"></a>Nincs etcd műveletek kezelése

Teljes körűen felügyelt natív felhőszolgáltatásként az Azure Cosmos DB szükségtelenné teszi a Kubernetes-fejlesztők számára a beállításés a kezelés stb. Az Azure Cosmos DB etcd API-ja méretezhető, magas rendelkezésre állású, hibatűrő, és nagy teljesítményt nyújt. A több csomópont on-felelek, a folyamatos frissítések, a biztonsági javítások és az etcd állapotának figyelése az Azure Cosmos DB által felügyelt többletterhelést.

### <a name="global-distribution--high-availability"></a>Globális disztribúció & magas rendelkezésre állás 

Az etcd API használatával az Azure Cosmos DB 99,99%-os rendelkezésre állást garantál az adatok egyetlen régióban történő olvasásához és írásához, és 99,999%-os rendelkezésre állást több régióban. 

### <a name="elastic-scalability"></a>Rugalmas méretezhetőség

Az Azure Cosmos DB rugalmas méretezhetőséget biztosít a különböző régiók olvasási és írási kérelmeihez.
A Kubernetes-fürt növekedésével az Azure Cosmos DB etcd API-fiókja rugalmasan, állásidő nélkül skálázódik. Etcd-adatok tárolása az Azure Cosmos DB, a Kubernetes fő csomópontok helyett is lehetővé teszi a rugalmasabb fő csomópontok méretezése is lehetővé teszi, hogy rugalmasabb fő csomópont méretezése is lehetővé teszi. 

### <a name="security--enterprise-readiness"></a>A vállalati biztonság & a vállalati készenlét

Ha az etcd-adatokat az Azure Cosmos DB tárolja, a Kubernetes fejlesztői automatikusan leállítják a [beépített titkosítást ,](database-encryption-at-rest.md)a [tanúsítványokat és a megfelelőséget,](compliance.md)valamint az Azure Cosmos DB által támogatott [biztonsági mentési és visszaállítási funkciókat.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) 

## <a name="next-steps"></a>További lépések

* [Az Azure Kubernetes használata az Azure Cosmos DB-vel](bootstrap-kubernetes-cluster.md)
* [Az Azure Cosmos DB legfontosabb előnyei](introduction.md)
* [Útmutató az AKS-motor rövid útmutatójának](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)