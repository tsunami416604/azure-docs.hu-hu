---
title: Kapcsolódás a szinapszis Studio Workspace-erőforráshoz egy korlátozott hálózatról
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat az Azure szinapszis Studio-munkaterület erőforrásaihoz egy korlátozott hálózatról
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: f2d8953ccae1057d7a7aa2d786fb7b641b3f6284
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392510"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>Kapcsolódás a szinapszis Studio-munkaterület erőforrásaihoz egy korlátozott hálózatról

Ez a cikk célzott olvasója a vállalati rendszergazda, aki a vállalat korlátozott hálózatát kezeli. A rendszergazda arra készül, hogy engedélyezze a hálózati kapcsolatot az Azure szinapszis Studio és az ezen a korlátozott hálózaton belüli munkaállomás között.

Ebből a cikkből megtudhatja, hogyan csatlakozhat az Azure-beli szinapszis-munkaterülethez egy korlátozott hálózati környezetben. 

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés** : Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) .
* **Azure szinapszis-munkaterület** : Ha nem rendelkezik a szinapszis Studióval, hozzon létre egy szinapszis-munkaterületet az Azure szinapszis Analytics szolgáltatásban. A munkaterület nevének megadása a 4. lépésben szükséges.
* **Korlátozott hálózat** : a korlátozott hálózatot a vállalat informatikai rendszergazdája tartja karban. a rendszergazda jogosult a hálózati házirend konfigurálására. A virtuális hálózat neve és alhálózata a 3. lépésben lesz szükséges.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>1. lépés: hálózati kimenő biztonsági szabályok hozzáadása a korlátozott hálózathoz

Négy szolgáltatási címkével rendelkező négy hálózati kimenő biztonsági szabályt kell felvennie. További információ a [szolgáltatási címkék áttekintéséről](/azure/virtual-network/service-tags-overview.md) 
* AzureResourceManager
* AzureFrontDoor. frontend
* AzureActiveDirectory
* AzureMonitor (nem kötelező. Ezt a típusú szabályt csak akkor adja hozzá, ha az adatmegosztást a Microsoftnak szeretné megosztani.)

**Azure Resource Manager** Kimenő szabály adatait az alábbiak szerint. A másik három szabály létrehozásakor cserélje le a " **rendeltetési szolgáltatás címkéje** " értéket a " **AzureFrontDoor. frontend** ", a " **AzureActiveDirectory** ", a " **AzureMonitor** " nevű szolgáltatási kódelem kiválasztásával a legördülő listából.

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>2. lépés: az Azure szinapszis Analytics (Private link hubok) létrehozása

Létre kell hoznia egy Azure szinapszis Analytics (magánhálózati kapcsolati hubok) Azure Portal. Keressen rá az " **Azure szinapszis Analytics (Private link hubok)** " kifejezésre a Azure Portalon keresztül, majd töltse ki a szükséges mezőt, és hozza létre. 

> [!Note]
> A régiónak azonosnak kell lennie, mint ahol a szinapszis-munkaterület van.

![A szinapszis Analytics Private link hubok létrehozása](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-endpoint-for-synapse-studio-gateway"></a>3. lépés: privát végpont létrehozása a szinapszis Studio-átjáróhoz

A szinapszis Studio-átjáró eléréséhez létre kell hoznia egy privát végpontot a Azure Portalból. A " **privát hivatkozás** " kifejezés a Azure Portalon keresztül érhető el. Válassza a "privát **végpont létrehozása** " lehetőséget a " **privát kapcsolat központban** ", majd töltse ki a szükséges mezőt, és hozza létre. 

> [!Note]
> A régiónak azonosnak kell lennie, mint ahol a szinapszis-munkaterület van.

![Privát végpont létrehozása a szinapszis Studio 1 számára](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

A " **Resource** " következő lapján válassza ki a privát kapcsolat hubot, amelyet a fenti 2. lépésben hozott létre.

![Privát végpont létrehozása a szinapszis Studio 2 számára](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

A " **Konfigurálás** " következő lapján 
* Válassza ki a virtuális **hálózathoz** tartozó korlátozott virtuális hálózat nevét.
* Válassza ki az " **alhálózat** " számára a korlátozott virtuális hálózat alhálózatát. 
* Válassza az **Igen** lehetőséget a " **saját DNS-zóna integrálása** " elemre.

![Privát végpont létrehozása a szinapszis Studio 3 számára](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

A privát kapcsolat végpontjának létrehozása után elérheti a szinapszis Studio Web Tool bejelentkezési lapját. A szinapszis munkaterületen belül azonban nem férhet hozzá az erőforrásokhoz, amíg el nem végzi a következő lépést.

## <a name="step-4-create-private-endpoints-for-synapse-studio-workspace-resource"></a>4. lépés: magánhálózati végpontok létrehozása a szinapszis Studio Workspace-erőforráshoz

A szinapszis Studio Workspace-erőforráson belüli erőforrások eléréséhez létre kell hoznia legalább egy privát kapcsolati végpontot **a "****cél alerőforrás** " típussal, és két további opcionális, " **SQL** " vagy " **SqlOnDemand** " típusú privát kapcsolati végpont attól függ, hogy milyen erőforrásokra van szükség a szinapszis Studio munkaterület eléréséhez. Ez a privát hivatkozás végpontjának létrehozása a szinapszis Studio-munkaterülethez hasonló, mint a végpontok létrehozásakor.  

Ügyeljen az " **erőforrás** " lapon az alábbi területekre:
* Válassza a " **Microsoft. szinapszis/munkaterületek** " lehetőséget az " **Erőforrás típusa** " értékre.
* Válassza a " **YourWorkSpaceName** " elemet a korábban létrehozott **erőforráshoz**.
* Válassza ki a végpont típusát a **cél alerőforrásban** :
  * **SQL** : SQL-lekérdezés végrehajtása SQL-készletben.
  * **SqlOnDemand** : az SQL beépített lekérdezés-végrehajtásához használható.
  * **Fejlesztői** : a szinapszis Studio-munkaterületeken található minden más szolgáltatáshoz való hozzáférés. Létre kell hoznia legalább egy ilyen típusú magánhálózati kapcsolati végpontot.

![Privát végpont létrehozása a szinapszis Studio-munkaterülethez](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-synapse-studio-workspace-linked-storage"></a>5. lépés: magánhálózati végpontok létrehozása a szinapszis Studio-munkaterülethez társított tároláshoz

A kapcsolódó tárolónak a szinapszis Studio munkaterületen található Storage Explorerrel való eléréséhez létre kell hoznia egy privát végpontot a 3. lépésben említett hasonló lépésekkel. 

Ügyeljen az " **erőforrás** " lapon az alábbi területekre:
* Válassza a " **Microsoft. szinapszis/storageAccounts** " értéket az " **erőforrástípus** " értékre.
* Válassza a " **YourWorkSpaceName** " elemet a korábban létrehozott **erőforráshoz**.
* Válassza ki a végpont típusát a **cél alerőforrásban** :
  * **blob** : az Azure Blob Storage-hoz.
  * **DFS** : Azure Data Lake Storage Gen2.

![Privát végpont létrehozása a szinapszis Studio-munkaterület társított tárterületéhez](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Most már elérheti a csatolt Storage-erőforrást a vNet-ben található szinapszis Studio-munkaterületen a Storage Explorerben.

Ha a munkaterület " **felügyelt virtuális hálózat engedélyezése** " lehetőséggel rendelkezik a munkaterület létrehozása során az alábbi módon:

![Privát végpont létrehozása a szinapszis Studio-munkaterület csatolt tárterületéhez 1](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

És azt szeretné, hogy a jegyzetfüzet egy bizonyos Storage-fiókhoz tartozó társított tárolási erőforrásokat is hozzáférhessen, hozzá kell adnia egy " **felügyelt privát végpontot** " a szinapszis Studióban. A " **Storage-fiók neve** " legyen a notebookhoz való hozzáféréshez szükséges egyik. Ismerje meg a [felügyelt privát végpontok adatforráshoz való létrehozásának](./how-to-create-managed-private-endpoints.md)részletes lépéseit.

A végpont létrehozása után a "jóváhagyás állapota" " **függőben** " **állapotú** lesz, ennek a Storage-fióknak a tulajdonosát kell megkérnie, hogy hagyja jóvá a " **privát végponti kapcsolatok** " lapon a Azure Portal. A jóváhagyást követően a jegyzetfüzet hozzáférhet a Storage-fiókhoz tartozó társított tárolási erőforrásokhoz.

Most pedig minden beállítás. Elérheti a szinapszis Studio-munkaterület erőforrását.

## <a name="next-steps"></a>További lépések

További információ a [felügyelt munkaterületről Virtual Network](./synapse-workspace-managed-vnet.md)

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)
