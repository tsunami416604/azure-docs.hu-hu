---
title: Kapcsolódás a munkaterület-erőforrásokhoz az Azure szinapszis Analytics Studióban egy korlátozott hálózatról
description: Ez a cikk bemutatja, hogyan csatlakozhat a munkaterület-erőforrásokhoz egy korlátozott hálózatról
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 7cff2d8245095489fbba3b7af24b416885995e4d
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637132"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Kapcsolódás a munkaterület erőforrásaihoz egy korlátozott hálózatról

Tegyük fel, hogy Ön rendszergazda, aki a szervezete korlátozott hálózatát kezeli. Engedélyezni szeretné az Azure szinapszis Analytics Studio és a korlátozott hálózaton belüli munkaállomás közötti hálózati kapcsolatot. Ez a cikk bemutatja, hogyan.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés** : Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) .
* **Azure szinapszis Analytics-munkaterület** : létrehozhat egyet az Azure szinapszis Analytics használatával. A munkaterület nevét a 4. lépésben kell megadnia.
* **Korlátozott hálózat** : a rendszergazda megtartja a szervezet korlátozott hálózatát, és jogosult a hálózati házirend konfigurálására. A 3. lépésben a virtuális hálózat neve és az alhálózata szükséges.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>1. lépés: hálózati kimenő biztonsági szabályok hozzáadása a korlátozott hálózathoz

Négy szolgáltatási címkével rendelkező négy hálózati kimenő biztonsági szabályt kell felvennie. 
* AzureResourceManager
* AzureFrontDoor. frontend
* AzureActiveDirectory
* AzureMonitor (az ilyen típusú szabály nem kötelező. Csak akkor adja hozzá, ha az adatmegosztást a Microsofttal szeretné megosztani.)

Az alábbi képernyőképen a Azure Resource Manager Kimenő szabály részletei láthatók.

![Képernyőkép a Azure Resource Manager szolgáltatás címkéjének részleteiről.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

A másik három szabály létrehozásakor cserélje le a **AzureFrontDoor. frontend** , a **AzureActiveDirectory** vagy a **AzureMonitor** értékre a **célként megadott szolgáltatási címkét** a listáról.

További információ: szolgáltatás- [címkék áttekintése](/azure/virtual-network/service-tags-overview.md).

## <a name="step-2-create-private-link-hubs"></a>2. lépés: magánhálózati kapcsolati hubok létrehozása

Következő lépésként hozzon létre privát kapcsolati hubokat a Azure Portal. A portálon megkeresheti az *Azure szinapszis Analytics (Private link hubok) szolgáltatást* , majd kitöltheti a szükséges információkat a létrehozásához. 

> [!Note]
> Győződjön meg arról, hogy a **régió** értéke ugyanaz, mint az Azure szinapszis Analytics-munkaterülete.

![Képernyőfelvétel a szinapszis Private link hub létrehozásáról.](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-gateway"></a>3. lépés: hozzon létre egy privát végpontot az átjáróhoz

Az Azure szinapszis Analytics Studio-átjáró eléréséhez létre kell hoznia egy privát végpontot a Azure Portalból. Ha szeretné megkeresni a portálon, keresse meg a *privát hivatkozás* kifejezést. A **privát kapcsolat központban** válassza a **privát végpont létrehozása** lehetőséget, majd adja meg a szükséges adatokat a létrehozásához. 

> [!Note]
> Győződjön meg arról, hogy a **régió** értéke ugyanaz, mint az Azure szinapszis Analytics-munkaterülete.

![Képernyőkép a privát végpontok létrehozásáról: alapbeállítások lap.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Az **erőforrás** lapon válassza a 2. lépésben létrehozott Private link hub elemet.

![Képernyőkép a privát végpont, Erőforrás lap létrehozásáról.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

A **konfiguráció** lapon: 
* A **virtuális hálózat** területen válassza a korlátozott virtuális hálózat nevét.
* Az **alhálózat** területen válassza ki a korlátozott virtuális hálózat alhálózatát. 
* A **magánhálózati DNS-zónával való integrációhoz** válassza az **Igen** lehetőséget.

![Képernyőkép a privát végpontok, a konfiguráció lap létrehozásáról.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

A magánhálózati kapcsolat végpontjának létrehozása után elérheti az Azure szinapszis Analytics Studio webeszközének bejelentkezési lapját. Azonban még nem fér hozzá a munkaterületen belüli erőforrásokhoz. Ehhez végre kell hajtania a következő lépést.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>4. lépés: privát végpontok létrehozása a munkaterület-erőforráshoz

Az Azure szinapszis Analytics Studio Workspace-erőforráson belüli erőforrások eléréséhez létre kell hoznia a következőket:

- Legalább egy titkos kapcsolati végpont, amely a **cél alerőforrásának** **fejlesztői** típusát adja meg.
- Két további opcionális privát csatolási végpont **SQL** -vagy **SqlOnDemand** -típussal attól függően, hogy milyen erőforrásokat szeretne elérni a munkaterületen.

A létrehozás hasonló ahhoz, ahogy az előző lépésben létrehozza a végpontot.  

Az **erőforrás** lapon:

* Az **erőforrástípus** mezőben válassza a **Microsoft. szinapszis/munkaterületek** lehetőséget.
* Az **erőforrás** mezőben válassza ki a korábban létrehozott munkaterület nevét.
* A **cél alerőforrásnál** válassza ki a végpont típusát:
  * Az **SQL az SQL** -lekérdezés futtatására szolgál az SQL-készletben.
  * A **SqlOnDemand** az SQL beépített lekérdezés-végrehajtásához használható.
  * A **dev** az Azure szinapszis Analytics Studio-munkaterületein minden más szolgáltatáshoz való hozzáférés. Létre kell hoznia legalább egy ilyen típusú privát kapcsolati végpontot.

![Képernyőkép a privát végpont, Erőforrás lap és munkaterület létrehozásáról.](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>5. lépés: magánhálózati végpontok létrehozása a munkaterület csatolt tárolójához

Ha a társított tárolót az Azure szinapszis Analytics Studio munkaterületen található Storage Explorerrel szeretné elérni, létre kell hoznia egy privát végpontot. Az ehhez hasonló lépések a 3. lépésben láthatók. 

Az **erőforrás** lapon:
* Az **erőforrástípus** mezőben válassza a **Microsoft. szinapszis/storageAccounts** lehetőséget.
* Az **erőforrás** mezőben válassza ki a korábban létrehozott Storage-fiók nevét.
* A **cél alerőforrásnál** válassza ki a végpont típusát:
  * a **blob** az Azure Blob Storage.
  * a **dfs** Azure Data Lake Storage Gen2.

![Képernyőkép a privát végpont, Erőforrás lap, tárterület létrehozásáról.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Most elérheti a társított tárolási erőforrást. A virtuális hálózaton belül az Azure szinapszis Analytics Studio-munkaterületen a Storage Explorer használatával férhet hozzá a társított tárolási erőforráshoz.

Engedélyezheti a felügyelt virtuális hálózatot a munkaterülethez, ahogy az a következő képernyőképen is látható:

![Képernyőfelvétel a szinapszis-munkaterület létrehozásáról, a felügyelt virtuális hálózat engedélyezése lehetőség kiemelve.](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Ha azt szeretné, hogy a notebookja egy bizonyos Storage-fiókhoz tartozó társított tárolási erőforrásokhoz férhessen hozzá, vegyen fel felügyelt magánhálózati végpontokat az Azure szinapszis Analytics Studióban. A Storage-fiók nevének kell lennie, amelyet a notebooknak el kell érnie. További információ: [felügyelt privát végpont létrehozása az adatforráshoz](./how-to-create-managed-private-endpoints.md).

A végpont létrehozása után a jóváhagyási állapot **függőben** állapotot mutat. A Storage-fiók tulajdonosának jóváhagyása a Azure Portal a Storage-fiók **Private Endpoint Connections** lapján. A jóváhagyást követően a notebookja hozzáférhet a Storage-fiókban lévő társított tárolási erőforrásokhoz.

Most pedig minden beállítás. Elérheti az Azure szinapszis Analytics Studio Workspace-erőforrást.

## <a name="next-steps"></a>További lépések

További információ a [felügyelt munkaterület virtuális hálózatáról](./synapse-workspace-managed-vnet.md).

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md).
