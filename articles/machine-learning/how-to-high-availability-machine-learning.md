---
title: A rugalmasság fokozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan teheti elérhetővé a Azure Machine Learning kapcsolódó erőforrásait a magas rendelkezésre állású konfiguráció használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098908"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Növelje Azure Machine Learning rugalmasságát

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan teheti a Azure Machine Learning kapcsolódó erőforrásait rugalmasabban a magas rendelkezésre állású konfigurációk használatával. A Azure Machine Learningtől függő Azure-szolgáltatások a magas rendelkezésre állás érdekében konfigurálhatók. Ez a cikk információt nyújt arról, hogy mely szolgáltatások konfigurálhatók a magas rendelkezésre álláshoz, valamint az erőforrások konfigurálásával kapcsolatos információkra mutató hivatkozásokat.

> [!NOTE]
> Azure Machine Learning önmagában nem biztosít vész-helyreállítási lehetőséget.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Az Azure-szolgáltatások megismerése Azure Machine Learning

Azure Machine Learning több Azure-szolgáltatástól függ, és több réteget is tartalmaz. Némelyikük az Ön (ügyfél) előfizetésében van kiépítve. Ezek a szolgáltatások magas rendelkezésre állású konfigurációért felelősek. Néhányat Microsoft-előfizetésben hoztak létre, és a Microsoft felügyeli.

* **Azure Machine learning infrastruktúra**: a Microsoft által felügyelt környezet Azure Machine learning munkaterülethez.

* **Társított erőforrások**: az előfizetésben kiépített erőforrások Azure Machine learning munkaterület létrehozása során. Ezek közé tartoznak az Azure Storage, a Azure Key Vault, a Azure Container Registry (ACR) és az App bepillantások. Ezen erőforrások magas rendelkezésre állási beállításaiért felelős.
  * Az alapértelmezett tároló olyan adatokat tartalmaz, mint például a modell, a betanítási adat és az adatkészlet.
  * Key Vault rendelkezik a Storage, az ACR és az adattárak hitelesítő adataival.
  * Az ACR a betanítási és a következtetési környezetre vonatkozó Docker-rendszerképpel rendelkezik.
  * Az alkalmazás-bepillantást a Azure Machine Learning figyelésére.

* **Számítási erőforrások**: a munkaterület telepítése után létrehozott erőforrások. Létrehozhat például egy számítási példányt vagy számítási fürtöt egy Machine learning-modell betanításához.
  * Számítási példány és számítási fürt: a Microsoft által felügyelt modell-fejlesztési környezet.
  * Egyéb erőforrások: a számítástechnikai erőforrások olyan Azure Machine Learninghoz csatlakoztathatók, mint például az Azure Kubernetes Service (ak), a Azure Databricks, a Azure Container Instances (ACI) és a HDInsight. A magas rendelkezésre állási beállításért felelős.

* **További adattárak**: Azure Machine learning további adattárakat is csatlakoztathat, például az Azure Storage-t, a Azure Data Lake Storaget és a Azure SQL Database a betanítási információkhoz.  Ezek az Ön előfizetésén belül vannak, és a magas rendelkezésre állási beállításért felelős.

Az alábbi táblázat azt mutatja, hogy a Microsoft mely szolgáltatásokat felügyeli, amelyeket Ön kezel, és amelyek alapértelmezés szerint nagyon elérhetők:

| Szolgáltatás | Felügyeli | HA alapértelmezés szerint |
| ----- | ----- | ----- |
| **Azure Machine Learning infrastruktúra** | Microsoft | |
| **Társított erőforrások** |
| Azure Storage | Ön | |
| Azure Key Vault | Ön | ✓ |
| Azure Container Registry | Ön | |
| Application Insights | Ön | NA |
| **Számítási erőforrások** |
| Számítási példány | Microsoft |  |
| Számítási fürt | Microsoft |  |
| Más erőforrások, például az Azure Kubernetes szolgáltatás, <br>Azure Databricks, Azure Container instance, Azure HDInsight | Ön |  |
| **További adattárak** , például Azure Storage, Azure SQL Database,<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>Azure Databricks fájlrendszer | Ön | |

A dokumentum további részében található információk alapján megismerheti azokat a műveleteket, amelyek szükségesek ahhoz, hogy mindegyik szolgáltatás elérhető legyen.

## <a name="associated-resources"></a>Társított erőforrások

> [!IMPORTANT]
> A Azure Machine Learning nem támogatja az alapértelmezett Storage-fiók feladatátvételét a Geo-redundáns tárolás (GRS) vagy a Geo-zóna-redundáns tárolás (GZRS) vagy az olvasási hozzáférésű geo-redundáns tárolás (RA-GRS) vagy az olvasási hozzáférésű geo-zóna-redundáns tárolás (RA-GZRS) használatával.

Győződjön meg arról, hogy minden erőforrás magas rendelkezésre állású beállítása az alábbi információkkal.

* **Azure Storage**: a magas rendelkezésre állási beállítások konfigurálásához tekintse meg az [Azure Storage redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy)című témakört.
* **Azure Key Vault**: az alapértelmezett magas rendelkezésre állási szolgáltatást biztosítja, és nincs szükség felhasználói beavatkozásra.  Lásd: [Azure Key Vault rendelkezésre állás és redundancia](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Azure Container Registry**: válasszon prémium SKU-t a Geo-replikáláshoz. Lásd: [geo-replikáció a Azure Container Registryban](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights**: nem biztosít magas rendelkezésre állási beállítást. Megcsípheti az adatmegőrzési időszakot és az [adatgyűjtési, adatmegőrzési és-tárolási adatokat Application Insightsban](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Számítási erőforrások

Győződjön meg arról, hogy az alábbi dokumentáció tartalmazza az egyes erőforrások magas rendelkezésre állási beállítását.

* **Azure Kubernetes szolgáltatás**: az [Azure Kubernetes szolgáltatásban (ak) az üzletmenet folytonosságával és a vész-helyreállítással kapcsolatos ajánlott eljárások](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) , valamint a [rendelkezésre állási zónákat használó Azure Kubernetes Service (ak)-fürt létrehozása](https://docs.microsoft.com/azure/aks/availability-zones)című témakörben talál. Ha az AK-fürtöt Azure Machine Learning (Studio, SDK vagy ML CLI) hozta létre, a régiók közötti magas rendelkezésre állás nem támogatott.
* **Azure Databricks**: tekintse [meg a Azure Databricks fürtökre vonatkozó regionális vész-helyreállítást](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Azure Container instance**: az ACI Orchestrator feladata a feladatátvétel. Lásd: [Azure Container instances és Container-](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship)szervezők.
* **Azure HDInsight**: Tekintse meg [Az Azure HDInsight által támogatott magas rendelkezésre állású szolgáltatásokat](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>További adattárak

Győződjön meg arról, hogy az alábbi dokumentáció tartalmazza az egyes erőforrások magas rendelkezésre állási beállítását.

* **Azure Blob Container/Azure file share/Azure Data Lake Gen2**: ugyanaz, mint az alapértelmezett tároló.
* **Azure Data Lake Gen1**: olvassa el a vész[-helyreállítási útmutatót a Azure Data Lake Storage Gen1 lévő adatkezelési útmutatóban](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Azure SQL Database**: Tekintse meg [a magas rendelkezésre állást és a Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Azure Database for PostgreSQL**: tekintse [meg a Azure Database for PostgreSQL-egyetlen kiszolgáló magas rendelkezésre állási fogalmait](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Azure Database for MySQL**: tekintse [meg az üzletmenet folytonosságának megismerése Azure Database for MySQL-ben](https://docs.microsoft.com/azure/mysql/concepts-business-continuity)című témakört.
* **Databricks fájlrendszer**: Azure Databricks- [fürtökre vonatkozó regionális](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)vész-helyreállítási feladat.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Ha a saját kulcsát (ügyfél által felügyelt kulcs) adja meg Azure Machine Learning munkaterület telepítéséhez, Cosmos DB az előfizetésében is kiépíthető. Ebben az esetben Ön felelős a magas rendelkezésre állásért. [A Azure Cosmos db magas rendelkezésre állásának](https://docs.microsoft.com/azure/cosmos-db/high-availability) megjelenítése

## <a name="next-steps"></a>További lépések

Ha a magas rendelkezésre állási beállításokkal rendelkező Azure Machine Learningt szeretne telepíteni a kapcsolódó erőforrásokkal, használjon egy [Azure Resource Manager sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).