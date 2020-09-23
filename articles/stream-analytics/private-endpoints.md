---
title: Privát végpontok létrehozása és törlése egy Azure Stream Analytics-fürtben
description: Ismerje meg, hogyan felügyelheti a privát végpontokat egy Azure Stream Analytics-fürtben.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: baf80e3d543bee455dd8dfa5bc09bc5bf43c3453
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947086"
---
# <a name="create-and-delete-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Privát végpontok létrehozása és törlése egy Azure Stream Analytics-fürtben

A fürtön futó Azure Stream Analytics-feladatokat a tűzfalon vagy az Azure Virtual Network (VNet) mögött lévő erőforrások bemeneti és kimeneti erőforrásaihoz is összekapcsolhatja. Először létre kell hoznia egy magánhálózati végpontot egy erőforráshoz, például az Azure Event hub vagy a Azure SQL Database a Stream Analytics-fürtben. Ezt követően hagyja jóvá a saját végponti kapcsolatokat a bemenetből vagy kimenetből.

Miután jóváhagyta a kapcsolatot, a Stream Analytics-fürtön futó összes feladatokhoz a magánhálózati végponton keresztül férhet hozzá az erőforráshoz. Ebből a cikkből megtudhatja, hogyan hozhat létre és törölhet privát végpontokat egy Stream Analytics fürtben.

## <a name="create-private-endpoint-in-stream-analytics-cluster"></a>Privát végpont létrehozása Stream Analytics fürtben

Ebből a szakaszból megtudhatja, hogyan hozhat létre privát végpontot egy Stream Analytics-fürtben.

1. A Azure Portal keresse meg és válassza ki a Stream Analytics-fürtöt.

1. A **Beállítások**területen válassza a **privát végpontok**lehetőséget.

1. Válassza a **privát végpont hozzáadása** lehetőséget, és adja meg az alábbi adatokat, és válassza ki azt az erőforrást, amelyet egy privát végponton keresztül biztonságosan szeretne elérni.

   |Beállítás|Érték|
   |---|---|
   |Név|Adja meg a privát végpont nevét. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
   |Kapcsolati módszer|Válassza a **Kapcsolódás egy Azure-erőforráshoz a címtárban**lehetőséget.<br><br>Kiválaszthatja az egyik erőforrását, hogy biztonságosan kapcsolódjon a privát végponthoz, vagy más erőforrásokhoz is csatlakozhat, ha az Ön által megosztott erőforrás-azonosítót vagy aliast használ.|
   |Előfizetés|Válassza ki előfizetését.|
   |Erőforrás típusa|Válassza ki az erőforráshoz [hozzárendelni kívánt erőforrás típusát](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Erőforrás|Válassza ki azt az erőforrást, amelyhez csatlakozni szeretne a privát végpont használatával.|
   |Cél alerőforrás|A fent kiválasztott erőforrás alerőforrásának típusa, amelyet a privát végpont elérhet.|

   ![A privát végpontok létrehozásának élménye](./media/private-endpoints/create-private-endpoint.png)

1. Hagyja jóvá a hozzáférést a cél erőforrásból. Ha például az előző lépésben egy Azure SQL Database-példányhoz hozott létre egy privát végpontot, lépjen a SQL Database példányra, és tekintse meg a függőben lévő, jóvá nem hagyott kapcsolatokat. Eltarthat néhány percig, amíg a kapcsolódási kérelem megjelennek.

    ![privát végpont jóváhagyása](./media/private-endpoints/approve-private-endpoint.png)

1. Visszatérhet a Stream Analytics-fürthöz, és megtekintheti, hogy az állapot változása függőben van-e a függőben lévő **ügyfél-jóváhagyásról** a **függőben lévő DNS-beállításra** , hogy a **Befejezés**

## <a name="delete-a-private-endpoint-in-a-stream-analytics-cluster"></a>Privát végpont törlése Stream Analytics fürtben

1. A Azure Portal keresse meg és válassza ki a Stream Analytics-fürtöt.

1. A **Beállítások**területen válassza a **privát végpontok**lehetőséget.

1. Válassza ki a törölni kívánt privát végpontot, és válassza a **Törlés**lehetőséget.

   ![privát végpont törlése](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Következő lépések

Most már áttekintheti, hogyan kezelhetők a Azure Stream Analytics fürtben található privát végpontok. Ezután megtudhatja, hogyan méretezheti a fürtöket, és hogyan futtathat feladatokat a fürtben:

* [Azure Stream Analytics-fürt méretezése](scale-cluster.md)
* [Stream Analytics feladatok kezelése Stream Analytics-fürtben](manage-jobs-cluster.md)