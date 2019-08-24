---
title: Migrálás részletes szerepköralapú hozzáférésre fürt-konfigurációkhoz – Azure HDInsight
description: Ismerje meg az áttelepítés részeként szükséges módosításokat a HDInsight-fürtök részletes szerepköralapú hozzáféréséhez.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 03bea7b9df929914e25ca97b382dc5c120b5a769
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69983034"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrálás fürtkonfigurációk részletes szerepköralapú hozzáféréséhez

Néhány fontos módosítást vezetünk be, hogy a bizalmas adatok beszerzése érdekében részletesebb szerepköralapú hozzáférést lehessen támogatni. A változtatások részeként előfordulhat, hogy a 2019. **szeptember 3-án** kell végrehajtania egy műveletet, ha az [érintett entitások/forgatókönyvek](#am-i-affected-by-these-changes)valamelyikét használja.

## <a name="what-is-changing"></a>Mi változik?

Korábban a titkos kulcsokat a HDInsight API-n keresztül lehet megszerezni a tulajdonos, közreműködő vagy olvasó [RBAC szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)rendelkező felhasználók számára, mivel azok mindenki számára elérhetővé válnak `*/read` az engedéllyel. A titkos kulcs olyan értékként van meghatározva, amely a felhasználó szerepkörének magasabb szintű hozzáférésének megszerzéséhez használható. Ezek közé tartoznak például a fürt átjárójának HTTP-hitelesítő adatai, a Storage-fiók kulcsainak és az adatbázis hitelesítő adatai.

2019. szeptember 3-ától a `Microsoft.HDInsight/clusters/configurations/action` titkos kulcsokhoz való hozzáféréshez engedély szükséges, ami azt jelenti, hogy a felhasználók már nem férhetnek hozzá az olvasó szerepkörrel rendelkező felhasználókhoz. Az ezzel az engedéllyel rendelkező szerepkörök a közreműködők, a tulajdonosok és az új HDInsight-fürt szerepkör (továbbiak az alább láthatók).

Egy új [HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) -fürtszolgáltatási szerepkört is bevezetünk, amely lehetővé teszi a titkok beolvasását anélkül, hogy a közreműködő vagy a tulajdonos rendszergazdai engedélyeit kellene megadnia. Összefoglalás:

| Role                                  | Korábban                                                                                       | Továbbítás folyamatban       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Olvasó                                | – Olvasási hozzáférés, beleértve a titkos kulcsokat                                                                   | – Olvasási hozzáférés, a titkok **kivételével** |           |   |   |
| HDInsight-fürt operátora<br>(Új szerepkör) | –                                                                                              | – Olvasási/írási hozzáférés, beleértve a titkos kulcsokat         |   |   |
| Közreműködő                           | – Olvasási/írási hozzáférés, beleértve a titkos kulcsokat<br>– Az Azure-erőforrások összes típusának létrehozása és kezelése.     | Nincs változás |
| Tulajdonos                                 | – Olvasási/írási hozzáférés, beleértve a titkos kulcsokat<br>– Teljes hozzáférés az összes erőforráshoz<br>– Hozzáférés delegálása mások számára | Nincs változás |

Arról, hogy hogyan adhat hozzá a HDInsight-kezelő szerepkör-hozzárendelést egy felhasználóhoz, hogy olvasási/írási hozzáférést biztosítson a fürt titkos kulcsaihoz, tekintse meg az alábbi szakaszt, és [adja hozzá a HDInsight-kezelő szerepkör-hozzárendelést egy felhasználóhoz](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Érintem ezeket a módosításokat?

A következő entitások és forgatókönyvek érintettek:

- [API](#api): A `/configurations` vagy`/configurations/{configurationName}` végpontokat használó felhasználók.
- [Azure HDInsight-eszközök a Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1-es vagy újabb verziójához.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) 3.20.0 vagy újabb verzió.
- [A Visual studióhoz készült Azure Data Lake és stream Analytics eszközök a](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) 2.3.9000.1 verzióban.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) 3.15.0 vagy újabb verzió.
- [SDK a .NET-hez](#sdk-for-net)
    - [1. x vagy 2. x verzió](#versions-1x-and-2x): A `GetClusterConfigurations` ,`GetConnectivitySettings`, ,`DisableHttp` vagy metódusokat használó felhasználók a ConfigurationsOperationsExtensions osztályból. `EnableHttp` `ConfigureHttpSettings`
    - [3. x és újabb verziók](#versions-3x-and-up): A `Get` `ConfigurationsOperationsExtensions` osztályból a `Update` `EnableHttp`,, vagy `DisableHttp` metódust használó felhasználók.
- [Pythonhoz készült SDK](#sdk-for-python): A `ConfigurationsOperations` osztályból `get` a `update` vagy metódust használó felhasználók.
- [Javához készült SDK](#sdk-for-java): A `ConfigurationsInner` osztályból `update` a `get` vagy metódust használó felhasználók.
- [SDK for go](#sdk-for-go): A `Get` struct`ConfigurationsClient` -ból `Update` vagy metódusokat használó felhasználók.
- [Az az. HDInsight PowerShell](#azhdinsight-powershell) a verzió 2.0.0.
Az Ön forgatókönyvéhez használandó migrálási lépéseket az alábbi szakaszokban találja (vagy használja a fenti hivatkozásokat).

### <a name="api"></a>API

A következő API-k módosulnak vagy elavultak lesznek:

- [ **/Configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) beolvasása (bizalmas adatok eltávolítva)
    - Korábban az egyes konfigurációs típusok (például titkok) beszerzésére használták.
    - Az API-hívás 2019 szeptember 3-ától kezdődően az egyes konfigurációs típusokat is visszaadja a titkos kulcsokkal. Az összes konfiguráció, köztük a titkok beszerzéséhez használja az új POST/configurations hívást. Az átjáró beállításainak beszerzéséhez használja az új POST/getGatewaySettings hívást.
- [ **/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) beolvasása elavult
    - Korábban az összes konfiguráció beszerzésére használták (beleértve a titkokat is)
    - A 2019. szeptember 3-ától kezdődően ez az API-hívás elavult lesz, és már nem támogatott. Az összes elérhető konfiguráció beszerzéséhez használja az új POST/configurations hívást. A bizalmas paraméterekkel rendelkező konfigurációk beszerzéséhez használja a GET/configurations/{configurationName} hívást.
- [ **/Configurations/{configurationName} közzététele**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) elavult
    - Korábban az átjáró hitelesítő adatainak frissítésére használták.
    - A 2019. szeptember 3-ától kezdődően ez az API-hívás elavult lesz, és már nem támogatott. Ehelyett használja az új POST/updateGatewaySettings.

A következő helyettesítő API-k lettek hozzáadva:</span>

- [ **/Configurations közzététele**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Ezzel az API-val hozzájuthat az összes konfigurációhoz, beleértve a titkokat is.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Ezt az API-t használhatja az átjáró beállításainak beszerzéséhez.
- [ **/UpdateGatewaySettings közzététele**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Ezzel az API-val frissítheti az átjáró beállításait (Felhasználónév és/vagy jelszó).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>A Visual Studio Code-hoz készült Azure HDInsight Tools

Ha az 1.1.1-es vagy újabb verzióját használja, frissítsen az [Azure HDInsight Tools for Visual Studio Code legújabb verziójára](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) a megszakítások elkerülése érdekében.

### <a name="azure-toolkit-for-intellij"></a>IntelliJ-hez készült Azure-eszközkészlet

Ha a 3.20.0 vagy az alábbi verziót használja, a megszakítások elkerülése érdekében frissítsen az [Azure Toolkit for IntelliJ beépülő modul legújabb verziójára](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) .

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>A Visual studióhoz készült Azure Data Lake és Stream Analytics eszközök

A megszakítások elkerülése érdekében frissítsen a 2.3.9000.1 vagy újabb verzióra a [Azure Data Lake és a stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) használatával.  A frissítéssel kapcsolatos segítségért tekintse meg a dokumentációt és a [Visual studióhoz készült Data Lake Tools frissítést](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Eclipse-hez készült Azure-eszközkészlet

Ha a 3.15.0 vagy az alábbi verziót használja, a megszakítások elkerülése érdekében frissítsen a [Azure Toolkit for Eclipse legújabb verziójára](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) .

### <a name="sdk-for-net"></a>.NET-hez készült SDK

#### <a name="versions-1x-and-2x"></a>1\. x és 2. x verziók

Frissítse a .NET-hez készült HDInsight SDK [verziójának 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) . Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- `ClusterOperationsExtensions.GetClusterConfigurations`a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
    - Az összes konfiguráció beolvasásához, beleértve a bizalmas `ClusterOperationsExtensions.ListConfigurations` paramétereket is, használja a jövőt.  Vegye figyelembe, hogy az "olvasó" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a metódust. Ez lehetővé teszi, hogy részletesen szabályozható legyen, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz.
    - Csak a HTTP-átjáró hitelesítő adatainak lekéréséhez használja `ClusterOperationsExtensions.GetGatewaySettings`a következőt:.

- `ClusterOperationsExtensions.GetConnectivitySettings`a már elavult, és lecserélte `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings`a már elavult, és lecserélte `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp`és `DisableHttp` már elavultak. A HTTP mostantól mindig engedélyezve van, így ezek a metódusok már nem szükségesek.

#### <a name="versions-3x-and-up"></a>3\. x és újabb verziók

Frissítsen a .NET-hez készült HDInsight SDK [5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) vagy újabb verziójára. Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
    - Az összes konfiguráció beolvasásához, beleértve a bizalmas [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) paramétereket is, használja a jövőt.  Vegye figyelembe, hogy az "olvasó" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a metódust. Ez lehetővé teszi, hogy részletesen szabályozható legyen, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő adatainak lekéréséhez használja [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)a következőt:. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)a már elavult, és lecserélte [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)és [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) már elavultak. A HTTP mostantól mindig engedélyezve van, így ezek a metódusok már nem szükségesek.

### <a name="sdk-for-python"></a>Pythonhoz készült SDK

A Pythonhoz készült HDInsight SDK-hoz tartozó [1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) vagy újabb verzióra történő frissítés. Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
    - Az összes konfiguráció beolvasásához, beleértve a bizalmas [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) paramétereket is, használja a jövőt.  Vegye figyelembe, hogy az "olvasó" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a metódust. Ez lehetővé teszi, hogy részletesen szabályozható legyen, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő adatainak lekéréséhez használja [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)a következőt:.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)a már elavult, és lecserélte [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>Javához készült SDK

Frissítse a Java-hoz készült HDInsight SDK-val kapcsolatos [1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) vagy újabb verzióra. Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
    - Az összes konfiguráció beolvasásához, beleértve a bizalmas [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) paramétereket is, használja a jövőt.  Vegye figyelembe, hogy az "olvasó" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a metódust. Ez lehetővé teszi, hogy részletesen szabályozható legyen, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő adatainak lekéréséhez használja [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable)a következőt:.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)a már elavult, és lecserélte [`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK for go

Frissítsen a Go-hoz készült HDInsight SDK [27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) vagy újabb verziójára. Ha a változások által érintett módszert használ, minimális kód-módosításokra lehet szükség:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)a **többé nem ad vissza bizalmas paramétereket** , például a tárolási kulcsokat (Core-site) vagy a http hitelesítő adatokat (Gateway).
    - Az összes konfiguráció beolvasásához, beleértve a bizalmas [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) paramétereket is, használja a jövőt.  Vegye figyelembe, hogy az "olvasó" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a metódust. Ez lehetővé teszi, hogy részletesen szabályozható legyen, hogy mely felhasználók férhetnek hozzá a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő adatainak lekéréséhez használja [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)a következőt:.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)a már elavult, és lecserélte [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az az. HDInsight PowerShell
A megszakítások elkerülése érdekében frissítsen az [az PowerShell Version 2.0.0](https://www.powershellgallery.com/packages/Az) vagy újabb verzióra.  Ha a módosítások által érintett módszert használ, minimális kód-módosításokra lehet szükség.
- `Grant-AzHDInsightHttpServicesAccess`már elavult, és az új `Set-AzHDInsightGatewayCredential` parancsmag váltotta fel.
- `Get-AzHDInsightJobOutput`frissítve lett, hogy támogassa a részletes szerepköralapú hozzáférést a tárolási kulcshoz.
    - Ez nem érinti a HDInsight-fürt operátor, közreműködő vagy tulajdonos szerepkörrel rendelkező felhasználóit.
    - A csak az olvasó szerepkörrel rendelkező felhasználók számára explicit módon `DefaultStorageAccountKey` kell megadni a paramétert.
- `Revoke-AzHDInsightHttpServicesAccess`már elavult. A HTTP mostantól mindig engedélyezve van, ezért erre a parancsmagra már nincs szükség.
 Lásd az az [. ](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)További részleteket a HDInsight áttelepítési útmutatójában talál.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>A HDInsight-fürt szerepkör-hozzárendelésének hozzáadása egy felhasználóhoz

A [tulajdonosi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) szerepkörrel rendelkező felhasználók hozzárendelhet a [HDInsight-kezelő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) szerepkört azokhoz a felhasználókhoz, akik számára olvasási/írási hozzáférést szeretne adni a bizalmas HDInsight-fürt konfigurációs értékeihez (például a fürt átjárójának hitelesítő adatait és a Storage-fiók kulcsait).

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

A szerepkör-hozzárendelés hozzáadásának legegyszerűbb módja a parancs használata az `az role assignment create` Azure CLI-ben.

> [!NOTE]
> Ezt a parancsot a tulajdonosi szerepkörrel rendelkező felhasználónak kell futtatnia, mivel csak ezek az engedélyek adhatók meg. A `--assignee` azon felhasználó egyszerű szolgáltatásnév vagy e-mail-címe, akihez a HDInsight-kezelő szerepkört hozzá szeretné rendelni. Ha nem rendelkezik megfelelő engedélyekkel, tekintse meg az alábbi gyakori kérdéseket.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Szerepkör megadása az erőforrás (fürt) szintjén

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Szerepkör megadása az erőforráscsoport szintjén

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Szerepkör megadása az előfizetés szintjén

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A Azure Portal segítségével hozzáadhatja a HDInsight-fürt operátori szerepkör-hozzárendelését egy felhasználóhoz. Tekintse meg a dokumentációt, [és kezelje az Azure-erőforrásokhoz való hozzáférést a RBAC és a Azure Portal használatával – szerepkör-hozzárendelés hozzáadása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>GYIK

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Miért jelenik meg a 403 (tiltott) válasz az API-kérelmek és/vagy az eszközök frissítése után?

A fürt konfigurációi mostantól a részletes szerepköralapú hozzáférés-vezérlés mögött vannak `Microsoft.HDInsight/clusters/configurations/*` , és az engedélyek elérését igénylik. Az engedély beszerzéséhez rendelje hozzá a HDInsight-kezelőt, a közreműködőt vagy a tulajdonosi szerepkört a felhasználóhoz vagy az egyszerű szolgáltatáshoz, és próbálja meg elérni a konfigurációkat.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Miért tekintheti meg a "nincs elegendő jogosultság a művelet végrehajtásához" kifejezést, ha az Azure CLI-parancs futtatásával rendeli hozzá a HDInsight-kezelő szerepkört egy másik felhasználóhoz vagy egyszerű szolgáltatáshoz?

A tulajdonosi szerepkörön kívül a parancsot végrehajtó felhasználónak vagy egyszerű szolgáltatásnak megfelelő HRE engedélyekkel kell rendelkeznie a megbízott objektumazonosítók kereséséhez. Ez az üzenet nem megfelelő HRE engedélyeket jelez. Próbálja meg lecserélni `–assignee-object-id` az `-–assignee` argumentumot a (z) értékre, és adja meg a hozzárendelt objektum azonosítóját paraméterként a név helyett (vagy egy felügyelt identitás esetén a résztvevő azonosítóját). További információért tekintse meg az az [role hozzárendelés Create dokumentációjának](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) választható paraméterek szakaszát.

Ha ez továbbra sem működik, forduljon a HRE rendszergazdájához, és szerezze be a megfelelő engedélyeket.

### <a name="what-will-happen-if-i-take-no-action"></a>Mi történik, ha nem végezek műveletet?

A 2019 `GET /configurations` `GET /configurations/{configurationName}` szeptember 3-ától kezdődően a hívásoknemadnakvisszasemmilyeninformációt,ésahívástöbbénemadvisszabizalmasparamétereket,példáulaStorage-fiókkulcsaitvagyafürtjelszavát.`POST /configurations/gateway` Ugyanez vonatkozik a megfelelő SDK-módszerekre és a PowerShell-parancsmagokra is.

Ha a fent említett Visual Studio-, VSCode-, IntelliJ-vagy Eclipse-eszközök valamelyikének egy régebbi verzióját használja, a továbbiakban nem fog működni, amíg nem frissíti.

Részletesebb információkért tekintse meg a jelen dokumentum megfelelő szakaszát a forgatókönyvhöz.