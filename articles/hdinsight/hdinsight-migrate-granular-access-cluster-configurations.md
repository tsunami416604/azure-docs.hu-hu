---
title: Részletes, szerepkörön alapuló hozzáférés a fürtkonfigurációkat – Azure HDInsight áttelepítése
description: Ismerje meg a részletes, szerepkörön alapuló hozzáférés a fürt konfigurációjának áttelepítéséhez szükséges módosításokat.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0422d848ccdf9ba82e68813de64eec863ee4ad29
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006233"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Részletes, szerepkörön alapuló hozzáférés a fürt konfigurációjának áttelepítése

További részletes szerepkör alapú hozzáférés támogatásához bizalmas információ néhány fontos változásokat vezetünk be. Mivel ezek része megváltozik, néhány **művelet lehet szükség** egyik használata a [érintett entitások/forgatókönyvek](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Mi változik?

Korábban a titkos kulcsok sikerült beolvasni a HDInsight-API-n keresztül a fürt felhasználók rendelkeznek a tulajdonos, közreműködő vagy olvasó [RBAC-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
Továbbítja, titkos adatokat már nem lesz a olvasói szerepkörrel rendelkező felhasználók számára is elérhető. Jelenleg is, amelyekhez a titkos kódok lekérése a rendszergazdai engedélyekkel, közreműködő vagy tulajdonos engedély megadása nélkül új "Futtató HDInisght fürt kezelő" szerepkör lesz bevezetve. Összefoglalásképpen:

| Szerepkör                                  | Korábban                                                                                       | Most       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Olvasó                                | -Olvasási hozzáférés, beleértve a titkos kulcsok                                                                   | -Olvasási hozzáférést, **kivételével** titkos kulcsok |           |   |   |
| HDInsight-fürt operátor<br>(Új szerepkör) | –                                                                                              | -Olvasási/írási hozzáférést, beleértve a titkos kulcsok         |   |   |
| Közreműködő                           | -Olvasási/írási hozzáférést, beleértve a titkos kulcsok<br>– Létrehozása és kezelése minden típusú Azure-erőforrást.     | Nem változott |
| Tulajdonos                                 | -Olvasási/írási hozzáférést, beleértve a titkos kulcsok<br>-Az összes erőforrás teljes hozzáféréssel<br>-Adhat hozzáférést másoknak | Nem változott |

Információk a HDInsight-fürt operátor szerepkör-hozzárendelés hozzá kell adnia nekik egy felhasználó olvasási/írási hozzáférés fürt titkos kódokhoz való, tekintse meg az alábbi szakaszban [a HDInsight-fürt operátor szerepkör-hozzárendelés hozzáadása egy felhasználó](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Érint engem ezeket a módosításokat?

A következő entitásokat és forgatókönyvek érintettek:

- [API](#api): Felhasználók használják a `/configurations` vagy `/configurations/{configurationName}` végpontok.
- [Az Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) verzió ___ vagy régebbi verzió.
- [IntelliJ-hez készült Azure-eszközkészlet](#azure-toolkit-for-intellij) verzió __ vagy régebbi verzió.
- [Eclipse-hez készült Azure-eszközkészlet](#azure-toolkit-for-eclipse) verzió __ vagy régebbi verzió.
- [SDK for .NET csomaggal](#sdk-for-net)
    - [verziók 1.x vagy 2.x](#versions-1x-and-2x): Felhasználók használják a `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` vagy `DisableHttp` ConfigurationsOperationsExtensions osztályból származó metódusokat.
    - [verziók 3.x, és akár](#versions-3x-and-up): Felhasználók használják a `EnableHttp`, `DisableHttp`, `Update`, vagy `Get` metódusokat a `ConfigurationsOperationsExtensions` osztály.
- [Pythonhoz készült SDK](#sdk-for-python): Felhasználók használják a `get` vagy `update` ConfigurationsOperations osztályból származó metódusokat.
- [SDK Java](#sdk-for-java): Felhasználók használják a `update` vagy `get` ConfigurationsInner osztályból származó metódusokat.
- [A Góhoz készült SDK](#sdk-for-go): Felhasználók használják a `Get` vagy `Update` a ConfigurationsClient struct metódusokat.

Lásd az alábbi szakaszok (vagy használja a fenti hivatkozások) megtekintéséhez az áttelepítés a forgatókönyvtől lépések.

### <a name="api"></a>API

A következő API-kat kell módosítani vagy elavult:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (bizalmas adatok eltávolításával) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Korábban már használt egyedi konfigurációs típusok (beleértve a titkos kulcsok) beszerzése.
    - Titkos kulcsok nincs megadva az API-hívás egyedi konfigurációs típusok most adja vissza. Az összes beállításokat, beleértve a titkos kulcsokat, beszerzéséhez használja az új [POST /configurations]() hívja. Csak az átjáró beállításainak beszerzéséhez használja az új [POST /getGatewaySettings]() hívja.
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (deprecated) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Korábban (beleértve a titkos kulcsok) minden konfigurációk beszerzése
    - Az API-hívás már nem lesz támogatott. A jövőben az összes konfiguráció beszerzéséhez használja az új [POST /configurations]() hívja. Nincs megadva bizalmas paraméterekkel rendelkező konfigurációk beszerzéséhez használja a [GET /configurations/ {configurationName}]() hívja.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (elavult) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Korábban már használt átjáró hitelesítő adatainak frissítése.
    - Az API-hívás a rendszer elavult, és már nem támogatott. Az új [POST /updateGatewaySettings]() helyette.

A következő helyettesítő API-k lettek hozzáadva:</span>

- **POST /configurations** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Ez az API használatával az összes beállításokat, beleértve a titkos kulcsok beszerzése.
- **POST /getGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/getGatewaySettings?api-version={api-version}
    - Ez az API használatával szerezze be az átjáró beállításait.
- **POST /updateGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/updateGatewaySettings?api-version={api-version}
    - Az API-val (felhasználónév és/vagy jelszó) átjáró-beállítások frissítése.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Az Azure HDInsight Tools for Visual Studio Code-ot

Ha 1.1.1-es használja vagy régebbi, kérjük, frissítsen a [legújabb verzióját az Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) megszakítások elkerülése érdekében.

### <a name="azure-toolkit-for-intellij"></a>IntelliJ-hez készült Azure-eszközkészlet

Ha 3.21.0 verzióját használja vagy régebbi, kérjük, frissítsen a [az Azure Toolkit for IntelliJ beépülő modul legújabb verziójának](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) megszakítások elkerülése érdekében.

### <a name="azure-toolkit-for-eclipse"></a>Eclipse-hez készült Azure-eszközkészlet

Ha használja a 2019-03-29 kiadás vagy annál régebbi, kérjük, frissítsen a legújabb Azure Toolkit for Eclipse megszakítások elkerülése érdekében.

### <a name="sdk-for-net"></a>.NET-hez készült SDK

#### <a name="versions-1x-and-2x"></a>Verziók 1.x és a 2.x

Kérjük, frissítsen [2.1.0-ás](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) a HDInsight SDK a .NET-hez. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- `ClusterOperationsExtensions.GetClusterConfigurations` fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek `ClusterOperationsExtensions.ListConfigurations` a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat.
    - Csak HTTP-átjáró használt kérheti `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` most már elavult és felváltotta `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` most már elavult és felváltotta `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` és `DisableHttp` elavultak. HTTP mindig engedélyezve van, így ezek a metódusok már nincs szükség.

#### <a name="versions-3x-and-up"></a>Verziók 3.x, és akár

Kérjük, frissítsen [verzió 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) a HDInsight SDK a .NET-hez. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek `ConfigurationOperationsExtensions.List` a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat. 
    - Csak HTTP-átjáró használt kérheti `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) most már elavult és felváltotta `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) és [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) elavultak. HTTP mindig engedélyezve van, így ezek a metódusok már nincs szükség.

### <a name="sdk-for-python"></a>Pythonhoz készült SDK

Kérjük, frissítsen [1.0.0-s verziójának](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) a HDInsight SDK Pythonhoz készült. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat. 
    - Csak HTTP-átjáró használt kérheti [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) most már elavult és felváltotta [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK For Java

Kérjük, frissítsen [1.0.0-s verziójának](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) a HDInsight SDK a Javához készült. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek `ConfigurationsInner.list` a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat. 
    - Csak HTTP-átjáró használt kérheti `ConfigurationsOperations.get_gateway_settings`.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) most már elavult és felváltotta `ClusterOperationsExtensions.update_gateway_settings`.

### <a name="sdk-for-go"></a>A Góhoz készült SDK

Kérjük, frissítsen [verzió 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) Góhoz készült HDInsight SDK. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat. 
    - Csak HTTP-átjáró használt kérheti [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) most már elavult és felváltotta [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>A HDInsight-fürt operátor szerepkör-hozzárendelés hozzáadása egy felhasználó számára

A felhasználó a [közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) vagy [tulajdonosa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) szerepkör biztosíthat a HDInsight-fürt operátor szerepkör, a felhasználók számára, hogy szeretné-e olvasási/írási hozzáférése HDInsight fürt titkok, a fürt átjáró hitelesítő adatait és a tárfiók kulcsait.

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

A legegyszerűbb módszer a szerepkör-hozzárendelés hozzáadása, a következő parancsot az Azure CLI használatával:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

> [!NOTE]
> Ezzel a paranccsal kell futnia a felhasználó által a közreműködő vagy tulajdonos szerepkörök, csak azokat az engedélyeket ezeket. A `--assignee` a felhasználó, akinek a HDInsight-fürt munkatárs szerepkör hozzárendelése szeretné az e-mail-cím.

A fenti parancsban megadja ezt a szerepkört az előfizetés szintjén. Ehelyett csak megadni ezt a szerepkört az erőforráscsoport szintjén, módosíthatja a parancsot, például így:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Másik lehetőségként használhatja az Azure Portalon a HDInsight-fürt operátor szerepkör-hozzárendelés hozzáadása egy felhasználó. A dokumentációt, [rbac-RÓL és az Azure portal segítségével Azure-erőforrásokhoz való hozzáférés felügyelete – szerepkör-hozzárendelés hozzáadása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
