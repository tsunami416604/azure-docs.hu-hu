---
title: Részletes, szerepkörön alapuló hozzáférés a fürtkonfigurációkat – Azure HDInsight áttelepítése
description: További információ a HDInsight-fürt konfigurációjának részletes szerepkör alapú hozzáférés való migrálás részeként szükséges módosításokat.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 56ba2dfebeb47f7e12a2693eae443e3c31e2a4dd
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203089"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrálás fürtkonfigurációk részletes szerepköralapú hozzáféréséhez

További részletes szerepkör alapú hozzáférés támogatásához bizalmas információ néhány fontos változásokat vezetünk be. Mivel ezek része megváltozik, néhány **művelet lehet szükség** egyik használata a [érintett entitások/forgatókönyvek](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Mi változik?

Korábban a titkos kulcsok sikerült beolvasni a HDInsight-API-n keresztül a fürt felhasználók rendelkeznek a tulajdonos, közreműködő vagy olvasó [RBAC-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles), amilyenek korábban voltak rendelkező a `*/read` engedély volt szükség.
Jövőben fér hozzá a titkos adatokat lesz szükség a `Microsoft.HDInsight/clusters/configurations/*` engedéllyel, ami azt jelenti, azok többé nem elérhetők a olvasói szerepkörrel rendelkező felhasználók által. Titkos kulcsok vannak meghatározva, értékeket, amelyek segítségével több emelt szintű hozzáférés, mint a felhasználói szerepkör lehetővé teszi. Ezek közé tartozik például a fürt átjáró HTTP hitelesítő adatait, a tárfiókkulcsok és adatbázis-hitelesítő adatok értékeit.

Is elérhetőkké váltak egy új [HDInsight-fürt operátor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) szerepkör, amely fogja tudni beolvasni a titkos kulcsok a rendszergazdai engedélyekkel, közreműködő vagy tulajdonos engedély megadása nélkül. Összefoglalásképpen:

| Szerepkör                                  | Korábban                                                                                       | A jövőben       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Olvasó                                | -Olvasási hozzáférés, beleértve a titkos kulcsok                                                                   | -Olvasási hozzáférést, **kivételével** titkos kulcsok |           |   |   |
| HDInsight-fürt operátor<br>(Új szerepkör) | –                                                                                              | -Olvasási/írási hozzáférést, beleértve a titkos kulcsok         |   |   |
| Közreműködő                           | -Olvasási/írási hozzáférést, beleértve a titkos kulcsok<br>– Létrehozása és kezelése minden típusú Azure-erőforrást.     | Nem változott |
| Tulajdonos                                 | -Olvasási/írási hozzáférést, beleértve a titkos kulcsok<br>-Az összes erőforrás teljes hozzáféréssel<br>-Adhat hozzáférést másoknak | Nem változott |

Információk a HDInsight-fürt operátor szerepkör-hozzárendelés hozzá kell adnia nekik egy felhasználó olvasási/írási hozzáférés fürt titkos kódokhoz való, tekintse meg az alábbi szakaszban [a HDInsight-fürt operátor szerepkör-hozzárendelés hozzáadása egy felhasználó](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Érint engem ezeket a módosításokat?

A következő entitásokat és forgatókönyvek érintettek:

- [API](#api): Felhasználók használják a `/configurations` vagy `/configurations/{configurationName}` végpontok.
- [Az Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1-es vagy az alábbi.
- [IntelliJ-hez készült Azure-eszközkészlet](#azure-toolkit-for-intellij) 3.20.0 verzióját vagy az alábbi.
- [Az Azure Data Lake és Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) verziónál régebbi verziójú 2.3.9000.1.
- [Eclipse-hez készült Azure-eszközkészlet](#azure-toolkit-for-eclipse) 3.15.0 verzióját vagy az alábbi.
- [SDK for .NET csomaggal](#sdk-for-net)
    - [verziók 1.x vagy 2.x](#versions-1x-and-2x): Felhasználók használják a `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` vagy `DisableHttp` ConfigurationsOperationsExtensions osztályból származó metódusokat.
    - [verziók 3.x, és akár](#versions-3x-and-up): Felhasználók használják a `Get`, `Update`, `EnableHttp`, vagy `DisableHttp` metódusokat a `ConfigurationsOperationsExtensions` osztály.
- [Pythonhoz készült SDK](#sdk-for-python): Felhasználók használják a `get` vagy `update` metódusokat a `ConfigurationsOperations` osztály.
- [SDK Java](#sdk-for-java): Felhasználók használják a `update` vagy `get` metódusokat a `ConfigurationsInner` osztály.
- [A Góhoz készült SDK](#sdk-for-go): Felhasználók használják a `Get` vagy `Update` metódusokat a `ConfigurationsClient` struct.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) 2.0.0-s verzió alatt.
Az Ön forgatókönyvéhez használandó migrálási lépéseket az alábbi szakaszokban találja (vagy használja a fenti hivatkozásokat).

### <a name="api"></a>API

A következő API-kat kell módosítani vagy elavult:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (bizalmas adatok eltávolításával)
    - Korábban már használt egyedi konfigurációs típusok (beleértve a titkos kulcsok) beszerzése.
    - Titkos kulcsok nincs megadva az API-hívás egyedi konfigurációs típusok most adja vissza. Az összes beállításokat, beleértve a titkos kulcsokat, beszerzéséhez használja az új bejegyzés /configurations hívás. Csak az átjáró beállításainak beszerzéséhez használja az új bejegyzés /getGatewaySettings hívás.
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (deprecated)
    - Korábban (beleértve a titkos kulcsok) minden konfigurációk beszerzése
    - Az API-hívás már nem lesz támogatott. A jövőben az összes konfiguráció beszerzéséhez használja az új bejegyzés /configurations hívás. Nincs megadva bizalmas paraméterekkel rendelkező konfigurációk beszerzéséhez használja a GET /configurations/ {configurationName}-hívás.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (elavult)
    - Korábban már használt átjáró hitelesítő adatainak frissítése.
    - Az API-hívás a rendszer elavult, és már nem támogatott. Használja helyette az új bejegyzés /updateGatewaySettings.

A következő helyettesítő API-k lettek hozzáadva:</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Ez az API használatával az összes beállításokat, beleértve a titkos kulcsok beszerzése.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Ez az API használatával szerezze be az átjáró beállításait.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Az API-val (felhasználónév és/vagy jelszó) átjáró-beállítások frissítése.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Az Azure HDInsight Tools for Visual Studio Code-ot

Ha használ 1.1.1-es vagy lent, frissítse a [legújabb verzióját az Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) megszakítások elkerülése érdekében.

### <a name="azure-toolkit-for-intellij"></a>IntelliJ-hez készült Azure-eszközkészlet

Ha 3.20.0 verzióját használja, vagy az alábbiakban, frissítse a [az Azure Toolkit for IntelliJ beépülő modul legújabb verziójának](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) megszakítások elkerülése érdekében.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Az Azure Data Lake és Stream Analytics Tools for Visual Studio

Frissítés vagy újabb verziójára 2.3.9000.1 [az Azure Data Lake és Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) megszakítások elkerülése érdekében.  Frissítésével kapcsolatos útmutatásért lásd: dokumentációban, [frissítés a Data Lake Tools for Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Eclipse-hez készült Azure-eszközkészlet

Ha 3.15.0 verzióját használja, vagy az alábbiakban, frissítse a [legújabb verzióját az Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) megszakítások elkerülése érdekében.

### <a name="sdk-for-net"></a>.NET-hez készült SDK

#### <a name="versions-1x-and-2x"></a>Verziók 1.x és a 2.x

Frissítse [2.1.0-ás](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) a HDInsight SDK a .NET-hez. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- `ClusterOperationsExtensions.GetClusterConfigurations` fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek `ClusterOperationsExtensions.ListConfigurations` a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat.
    - Csak HTTP-átjáró használt kérheti `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` most már elavult és felváltotta `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` most már elavult és felváltotta `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` és `DisableHttp` elavultak. HTTP mindig engedélyezve van, így ezek a metódusok már nincs szükség.

#### <a name="versions-3x-and-up"></a>Verziók 3.x, és akár

Frissítse [verzió 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) és újabb verzióiban a HDInsight SDK a .NET-hez. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat. 
    - Csak HTTP-átjáró használt kérheti [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) most már elavult és felváltotta [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) és [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) elavultak. HTTP mindig engedélyezve van, így ezek a metódusok már nincs szükség.

### <a name="sdk-for-python"></a>Pythonhoz készült SDK

Frissítse [1.0.0-s verziójának](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) és újabb verzióiban a HDInsight SDK Pythonhoz készült. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat. 
    - Csak HTTP-átjáró használt kérheti [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) most már elavult és felváltotta [ `ClusterOperations.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK For Java

Frissítse [1.0.0-s verziójának](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) és újabb verzióiban a HDInsight SDK a Javához készült. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat. 
    - Csak HTTP-átjáró használt kérheti [ `ClustersInner.getGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) most már elavult és felváltotta [ `ClustersInner.updateGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>A Góhoz készült SDK

Frissítse [verzió 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) és újabb verzióiban a Góhoz készült HDInsight SDK. Minimális kódmódosítás szükséges lehet, ha ezek a változások által érintett metódus használja:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) fog **már nem ad vissza a bizalmas paraméterek** , például tárelérési kulcsok (hely) vagy HTTP-hitelesítő adatok (átjáró).
    - Lekérdezheti az összes beállításokat, beleértve a bizalmas paraméterek [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) a jövőben.  Vegye figyelembe, hogy az "Olvasó" szerepkörrel rendelkező felhasználók nem fognak tudni ezt a módszert használja. Ez lehetővé teszi szabályozható, amelyen a felhasználók hozzáférhessenek a fürt bizalmas adatokat. 
    - Csak HTTP-átjáró használt kérheti [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) most már elavult és felváltotta [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Frissítse [Az PowerShell 2.0.0-s verzió](https://www.powershellgallery.com/packages/Az) vagy később a megszakítások elkerülése érdekében.  Ezek a változások által érintett metódus használatakor minimális kódmódosításra lehet szükség.
- `Grant-AzHDInsightHttpServicesAccess` most már elavult és felváltotta az új `Set-AzHDInsightGatewayCredential` parancsmagot.
- `Get-AzHDInsightJobOutput` frissítve lett a tárfiók hívóbetűjét, a részletes, szerepkörön alapuló hozzáférés támogatására.
    - Ez nem érinti a HDInsight-fürt operátor, közreműködő vagy tulajdonos szerepkörrel rendelkező felhasználóit.
    - Csak a olvasói szerepkörrel rendelkező felhasználók meg kell adnia a `DefaultStorageAccountKey` paraméter explicit módon.
- `Revoke-AzHDInsightHttpServicesAccess` most már elavult. HTTP mindig engedélyezve van, ezért már nem szükséges ezt a parancsmagot.
 Tekintse meg a [az. Áttelepítési útmutató HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) további részletekért.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>A HDInsight-fürt operátor szerepkör-hozzárendelés hozzáadása egy felhasználó számára

Rendelkező felhasználók a [közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) vagy [tulajdonosa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) szerepkör is hozzárendelhet a [HDInsight-fürt operátor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) a felhasználók számára, amely rendelkezik olvasási/írási hozzáféréssel a bizalmas szeretne szerepkör HDInsight fürt konfigurációs értékek (például a fürt átjáró hitelesítő adatait és a storage-fiók kulcsainak).

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

A legegyszerűbb módszer a szerepkör-hozzárendelés hozzáadása használatával, a `az role assignemnt create` parancs Azure CLI-ben.

> [!NOTE]
> Ezzel a paranccsal kell futnia a felhasználó által a közreműködő vagy tulajdonos szerepkörök, csak azokat az engedélyeket ezeket. A `--assignee` a felhasználó, akinek a HDInsight-fürt munkatárs szerepkör hozzárendelése szeretné az e-mail-cím.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Adja meg az erőforrás (fürt) szintjén szerepkör

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Adja meg az erőforráscsoport szintjén szerepkör

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Adja meg az előfizetés szintjén szerepkör

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Másik lehetőségként használhatja az Azure Portalon a HDInsight-fürt operátor szerepkör-hozzárendelés hozzáadása egy felhasználó. A dokumentációt, [rbac-RÓL és az Azure portal segítségével Azure-erőforrásokhoz való hozzáférés felügyelete – szerepkör-hozzárendelés hozzáadása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
