---
title: Munkaterületek létrehozása a portálon
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre, tekinthet meg és törölhet Azure Machine Learning munkaterületeket a Azure Portalban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: d2885c6cc259cba74ab991ecf5046856984824f1
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631246"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure Machine Learning munkaterületek létrehozása és kezelése a Azure Portal


Ebben a cikkben [**Azure Machine learning munkaterületeket**](concept-workspace.md) hoz létre, tekinthet meg és törölhet a [Azure Machine learning](overview-what-is-azure-ml.md)Azure Portal.  A portál a legegyszerűbb módszer a munkaterületek használatának megkezdésére, de az igények változásának vagy az automatizálási követelmények növelésének megkezdéséhez létrehozhat és törölhet munkaterületeket [a parancssori felület használatával](reference-azure-machine-learning-cli.md), [Python-kóddal](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) vagy [a vs Code bővítménnyel](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Munkaterület létrehozásához Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure-előfizetéséhez tartozó hitelesítő adatok használatával. 

1. A Azure Portal bal felső sarkában válassza az **+ erőforrás létrehozása**lehetőséget.

      ![Új erőforrás létrehozása](./media/how-to-manage-workspace/create-workspace.gif)

1. A keresősáv használatával megkeresheti **Machine learning**.

1. Válassza a **Machine learning**lehetőséget.

1. A **Machine learning** ablaktáblán kattintson a **Létrehozás** elemre a kezdéshez.

1. Adja meg az alábbi adatokat az új munkaterület konfigurálásához:

   Mező|Leírás 
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterületet. Ebben a példában a **docs-ws-** t használjuk. A névnek egyedinek kell lennie az erőforráscsoport között. Használjon könnyen felhívható nevet, és a mások által létrehozott munkaterületek megkülönböztetését. A munkaterület neve megkülönbözteti a kis-és nagybetűket.
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Az előfizetés valamelyik meglévő erőforráscsoportját használja, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Egy erőforráscsoport kapcsolódó erőforrásokat tárol egy Azure-megoldáshoz. Ebben a példában a **docs-pénzmosást**használjuk. Meglévő erőforráscsoport használatához *közreműködői* vagy *tulajdonosi* szerepkörre van szükség.  További információ a hozzáférésről: [Azure Machine learning munkaterület hozzáférésének kezelése](how-to-assign-roles.md).
   Region | Válassza ki a felhasználókhoz legközelebb eső Azure-régiót, valamint az adatforrásokat a munkaterület létrehozásához.
   Munkaterület kiadása | Válassza az **Alap** vagy a **vállalat**lehetőséget.  Ez a munkaterület-kiadás határozza meg azokat a szolgáltatásokat, amelyekhez hozzáférésre és díjszabásra van szüksége. További információ a [Azure Machine Learningról](overview-what-is-azure-ml.md). 

    ![Munkaterület konfigurálása](./media/how-to-manage-workspace/select-edition.png)

1. Ha befejezte a munkaterület konfigurálását, válassza a **felülvizsgálat + létrehozás**lehetőséget. Igény szerint a [hálózatkezelés](#networking) és a [speciális](#advanced) szakaszban is konfigurálhatja a munkaterület további beállításait.

1. Tekintse át a beállításokat, és végezze el a további módosításokat vagy helyesbítéseket. Ha elégedett a beállításokkal, válassza a **Létrehozás**lehetőséget.

   > [!Warning] 
   > Több percet is igénybe vehet, hogy a munkaterületet a felhőben hozza létre.

   Ha a folyamat elkészült, megjelenik egy központi telepítés sikerességét jelző üzenet. 
 
 1. Az új munkaterület megtekintéséhez válassza az **Ugrás erőforráshoz**lehetőséget.

### <a name="networking"></a>Hálózatkezelés  

> [!IMPORTANT]  
> További információ a saját munkaterülettel rendelkező privát végpontok és virtuális hálózatok használatáról: [hálózati elkülönítés és adatvédelem](how-to-enable-virtual-network.md).
    
1. Az alapértelmezett hálózati konfiguráció egy __nyilvános végpont__használata, amely a nyilvános interneten érhető el. Ha a munkaterülethez való hozzáférést egy Ön által létrehozott Azure-Virtual Network szeretné korlátozni, válassza a __magánhálózati végpont__ lehetőséget a __kapcsolati módszerként__, majd a __+ Hozzáadás__ paranccsal konfigurálja a végpontot. 
    
   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Privát végpont kiválasztása":::  

1. A __privát végpont létrehozása__ űrlapon állítsa be a használni kívánt helyet, nevet és virtuális hálózatot. Ha saját DNS zónával szeretné használni a végpontot, válassza a __saját DNS-zóna integrálása__ lehetőséget, majd a __saját DNS zóna__ mező használatával válassza ki a zónát. A végpont létrehozásához kattintson __az OK gombra__ .   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Privát végpont kiválasztása":::   

1. Ha befejezte a hálózatkezelés konfigurálását, válassza a __felülvizsgálat + létrehozás__lehetőséget, vagy lépjen a választható __speciális__ konfigurációra. 

    > [!WARNING]    
    > Privát végpont létrehozásakor létrejön egy új, __privatelink.API.azureml.MS__ nevű saját DNS zóna. Ez a virtuális hálózatra mutató hivatkozást tartalmaz. Ha több munkaterületet hoz létre egy privát végponttal ugyanabban az erőforráscsoporthoz, akkor csak az első privát végpont virtuális hálózata adható hozzá a DNS-zónához. A további munkaterületek/privát végpontok által használt virtuális hálózatok bejegyzéseinek hozzáadásához kövesse az alábbi lépéseket: 
    >   
    > 1. A [Azure Portal](https://portal.azure.com)válassza ki a munkaterületet tartalmazó erőforráscsoportot. Ezután válassza ki a __privatelink.API.azureml.MS__nevű saját DNS zóna erőforrást.    
    > 2. A __Beállítások__területen válassza a __virtuális hálózati kapcsolatok__elemet. 
    > 3. Válassza a __Hozzáadás__ lehetőséget. A __virtuális hálózat hozzáadása hivatkozás__ lapon adjon meg egy egyedi __hivatkozási nevet__, majd válassza ki a hozzáadni kívánt __virtuális hálózatot__ . A hálózati kapcsolat hozzáadásához kattintson __az OK gombra__ .    
    >   
    > További információ: [Azure Private Endpoint DNS-konfiguráció](/azure/private-link/private-endpoint-dns).   

### <a name="vulnerability-scanning"></a>Sebezhetőségi vizsgálat

Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Az erőforrások vizsgálatához és a javaslatainak követéséhez engedélyeznie kell Azure Security Center. További információ:  [Azure Container Registry rendszerképek vizsgálata Security Center](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) és az [Azure Kubernetes Services integrációja Security Center](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)használatával.

### <a name="advanced"></a>Felsőfokú    

Alapértelmezés szerint a munkaterülethez tartozó metrikák és metaadatok a Microsoft által fenntartott Azure Cosmos DB-példányban vannak tárolva. Ezeket az adatfájlokat a Microsoft által felügyelt kulcsokkal titkosítjuk.  

Ha korlátozni szeretné a Microsoft által a munkaterületre összegyűjtött adatokat, válassza a __magas üzleti hatás munkaterületet__. További információ erről a beállításról: inaktív [titkosítás](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]  
> A magas üzleti hatás kiválasztása csak munkaterületek létrehozásakor végezhető el. Ez a beállítás a munkaterület létrehozása után nem módosítható.   
Ha a Azure Machine Learning __vállalati__ verzióját használja, a saját kulcsát is megadhatja. Ezzel létrehozza az Azure-előfizetésében szereplő mérőszámokat és metaadatokat tároló Azure Cosmos DB-példányt. A saját kulcsának használatához kövesse az alábbi lépéseket:    

> [!IMPORTANT]  
> Mielőtt végrehajtaná ezeket a lépéseket, először el kell végeznie a következő műveleteket:   
>   
> 1. Engedélyezze az előfizetéshez tartozó közreműködői engedélyekkel rendelkező __Machine learning alkalmazást__ (az identitás-és hozzáférés-kezelésben).  
> 1. Kövesse az [ügyfél által felügyelt kulcsok konfigurálása](/azure/cosmos-db/how-to-setup-cmk) a következőhöz című témakör lépéseit:   
>     * A Azure Cosmos DB-szolgáltató regisztrálása   
>     * Azure Key Vault létrehozása és konfigurálása 
>     * Kulcs létrehozása  
>   
>     Nem kell manuálisan létrehoznia a Azure Cosmos DB példányt, a rendszer létrehoz egyet a munkaterület létrehozása során. Ez a Azure Cosmos DB-példány egy külön erőforráscsoporthoz jön létre a következő minta alapján: `<your-workspace-resource-name>_<GUID>` .   
>   
> Ez a beállítás a munkaterület létrehozása után nem módosítható. Ha törli a munkaterület által használt Azure Cosmos DB, törölnie kell az azt használó munkaterületet is.

1. Válassza az __ügyfél által felügyelt kulcsok__lehetőséget, majd __kattintson a kulcs kiválasztásához__.   

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Privát végpont kiválasztása":::   

1. A __válasszon kulcsot Azure Key Vault__ űrlapon válassza ki a meglévő Azure Key Vault, a benne található kulcsot, valamint a kulcs verzióját. Ezzel a kulccsal titkosíthatja a Azure Cosmos DB tárolt adataikat. Végül használja a __Select (kiválasztás__ ) gombot a kulcs használatához. 

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Privát végpont kiválasztása":::

### <a name="download-a-configuration-file"></a>Konfigurációs fájl letöltése

1. Ha [számítási példányt](tutorial-1st-experiment-sdk-setup.md#azure)fog létrehozni, hagyja ki ezt a lépést.

1. Ha azt tervezi, hogy a munkaterületre hivatkozó helyi környezet programkódját használja, válassza a  **config.jsletöltése** elemet a munkaterület **Áttekintés** szakaszában.  

   ![config.jsletöltése](./media/how-to-manage-workspace/configure.png)
   
   Helyezze a fájlt a címtár-struktúrába a Python-szkriptekkel vagy a Jupyter notebookokkal. Ez lehet ugyanabban a címtárban, egy *. azureml*nevű alkönyvtár vagy egy szülő könyvtárban. Számítási példány létrehozásakor a rendszer hozzáadja ezt a fájlt a virtuális gép megfelelő könyvtárába.
## <a name="find-a-workspace"></a><a name="view"></a>Munkaterület keresése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső Keresés mezőbe írja be a következőt: **Machine learning**.  

1. Válassza a **Machine learning**lehetőséget.

   ![Azure Machine Learning munkaterület keresése](./media/how-to-manage-workspace/find-workspaces.png)

1. Tekintse át a talált munkaterületek listáját. Az előfizetés, az erőforráscsoportok és a helyszínek alapján szűrhet.  

1. Válasszon ki egy munkaterületet a tulajdonságainak megjelenítéséhez.

## <a name="delete-a-workspace"></a>Munkaterület törlése

A [Azure Portal](https://portal.azure.com/)a törölni kívánt munkaterület tetején válassza a **Törlés**  lehetőséget.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Privát végpont kiválasztása":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> Ha áthelyezi a Azure Machine Learning munkaterületet egy másik előfizetésbe, vagy áthelyezi a tulajdonosi előfizetést egy új bérlőre, nem támogatott. Ez hibákhoz vezethet.

### <a name="deleting-the-azure-container-registry"></a>A Azure Container Registry törlése

A Azure Machine Learning munkaterület egyes műveletekhez Azure Container Registry (ACR) használ. Automatikusan létrehoz egy ACR-példányt, amikor először szüksége lesz rá.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>További lépések

Kövesse a teljes hosszúságú oktatóanyagot, amelyből megtudhatja, hogyan hozhat létre, taníthat és helyezhet üzembe modelleket Azure Machine Learning használatával a munkaterületen.

> [!div class="nextstepaction"]
> [Oktatóanyag: modellek betanítása](tutorial-train-models-with-aml.md)
