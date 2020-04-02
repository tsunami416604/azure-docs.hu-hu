---
title: Részletes szerepköralapú hozzáférés az Azure HDInsight-fürtkonfigurációkban
description: Ismerje meg a HDInsight-fürtkonfigurációk részletes szerepköralapú hozzáférésére való áttelepítés során szükséges módosításokat.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: bb78d84aa0f9a2832b6599edeac9d50e0e226437
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546342"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrálás fürtkonfigurációk részletes szerepköralapú hozzáféréséhez

Bevezetünk néhány fontos változtatást, hogy támogassuk a részletesebb szerepalapú hozzáférést a bizalmas információk megszerzéséhez. A módosítások részeként **2019.** [affected entities/scenarios](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Mi változik?

Korábban a hdinsight API-n keresztül a tulajdonosi, közreműködői vagy reader [RBAC-szerepkörökkel](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)rendelkező `*/read` fürtfelhasználók szerezhettek be titkos kulcsokat, mivel azok bárki számára elérhetők voltak az engedéllyel rendelkezők számára. A titkos kulcsok olyan értékek, amelyek a felhasználó szerepkörének által lehetővé tettnél emelt szintű bb hozzáférés elérésére használhatók. Ezek közé tartoznak például a fürtös átjáró HTTP-hitelesítő adatai, a tárfiók kulcsai és az adatbázis hitelesítő adatai.

2019. szeptember 3-tól kezdődően ezeknek `Microsoft.HDInsight/clusters/configurations/action` a titkoknak a eléréséhez engedély kell, ami azt jelenti, hogy az Olvasó szerepkörrel rendelkező felhasználók már nem férhetnek hozzá. Az ezzel az engedéllyel rendelkező szerepkörök a közreműködő, a tulajdonos és az új HDInsight fürtkezelő szerepkör (erről bővebben alább).

Egy új [HDInsight fürtkezelő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) szerepkört is bevezetünk, amely képes lesz a titkos kulcsok lekérését anélkül, hogy megkapnák a közreműködő vagy a tulajdonos rendszergazdai engedélyeit. Összegezve:

| Szerepkör                                  | Korábban                                                                                       | Továbbhaladva       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Olvasó                                | - Olvasási hozzáférés, beleértve a titkokat                                                                   | - Olvasási hozzáférés, **kivéve a titkokat** |           |   |   |
| HDInsight fürtoperátor<br>(Új szerepkör) | N/A                                                                                              | - Olvasási/írási hozzáférés, beleértve a titkokat         |   |   |
| Közreműködő                           | - Olvasási/írási hozzáférés, beleértve a titkokat<br>- Hozzon létre és kezelje az összes típusú Azure-erőforrások.     | Nincs változás besorolás |
| Tulajdonos                                 | - Olvasási/írási hozzáférés, beleértve a titkokat<br>- Teljes hozzáférés az összes erőforráshoz<br>- Delegált hozzáférést másoknak | Nincs változás besorolás |

Ha tudni szeretné, hogyan adhat hozzá hdinsight fürtoperátorszerepkör-hozzárendelést egy felhasználóhoz, hogy olvasási/írási hozzáférést biztosítson számukra a fürttitkos kulcsokhoz, az alábbi, [A HDInsight fürtfelelős szerepkör-hozzárendelés hozzáadása a felhasználóhoz](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)című szakaszban olvashat.

## <a name="am-i-affected-by-these-changes"></a>Érintve vannak ezek a változások?

A következő entitásokat és forgatókönyveket érinti:

- [API](#api): A `/configurations` `/configurations/{configurationName}` vagy a végpontokat használó felhasználók.
- [Azure HDInsight-eszközök a Visual Studio](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1-es vagy újabb verziójához.
- [Az Azure Toolkit az IntelliJ](#azure-toolkit-for-intellij) 3.20.0-s vagy újabb verziójához.
- [Az Azure Data Lake és a Stream Analytics Tools for Visual Studio a](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) 2.3.9000.1-es verzió alatt.
- [Azure Toolkit az Eclipse](#azure-toolkit-for-eclipse) 3.15.0-s vagy újabb verziójához.
- [.NET-hez készült SDK](#sdk-for-net)
    - [1.x vagy 2.x verziók](#versions-1x-and-2x) `GetConnectivitySettings`: `ConfigureHttpSettings` `EnableHttp` A `DisableHttp` `GetClusterConfigurations`, , vagy a ConfigurationsOperationsExtensions osztály metódusait használó felhasználók.
    - [3.x és újabb verziók](#versions-3x-and-up) `Update`: `EnableHttp`A `DisableHttp` `Get`, `ConfigurationsOperationsExtensions` , , vagy metódusokat használó felhasználók az osztályból.
- [SDK Python:](#sdk-for-python)Az `get` `update` `ConfigurationsOperations` osztályból származó metódusokat használó felhasználók.
- [SDK Java:](#sdk-for-java)Az `update` osztályból származó vagy `get` metódusokat `ConfigurationsInner` használó felhasználók.
- [SDK for Go](#sdk-for-go): `Get` `Update` A struct-ből származó vagy metódusokat `ConfigurationsClient` használó felhasználók.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) a 2.0.0-s verzió alatt.
Tekintse meg az alábbi szakaszokat (vagy használja a fenti hivatkozásokat) a forgatókönyv áttelepítési lépései megtekintéséhez.

### <a name="api"></a>API

A következő API-k módosulnak vagy elavultak:

- [**GET /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (bizalmas adatok eltávolítva)
    - Korábban az egyes konfigurációtípusok (beleértve a titkos kulcsokat is) beszerzésére használták.
    - 2019. szeptember 3-tól kezdődően ez az API-hívás mostantól az egyes konfigurációs típusokat adja vissza, amelyek ből kimaradtak a titkos kulcsok. Az összes konfiguráció beszerzéséhez, beleértve a titkos kulcsokat is, használja az új POST /configurations hívást. Csak az átjáró beállítások beszerzéséhez használja az új POST /getGatewaySettings hívást.
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (elavult)
    - Előzőleg az összes konfiguráció beszerzésére használt (beleértve a titkos kulcsokat is)
    - 2019. szeptember 3-tól ez az API-hívás elavult, és már nem támogatott. Az összes konfiguráció beszerzéséhez használja az új POST /configurations hívást. A kihagyott bizalmas paraméterekkel rendelkező konfigurációk beszerzéséhez használja a GET /configurations/{configurationName} metódust.
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (elavult)
    - Korábban az átjáró hitelesítő adatainak frissítésére használt.
    - 2019. szeptember 3-tól ez az API-hívás elavult, és már nem támogatott. Használja inkább az új POST /updateGatewaySettings kapcsolót.

A következő helyettesítő API-k kerültek hozzáadásra:</span>

- [**POST /konfigurációk**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Ezzel az API-val az összes konfigurációt, beleértve a titkos kulcsokat.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Ezzel az API-val beszerezheti az átjáróbeállításokat.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Ezzel az API-val frissítheti az átjáró beállításait (felhasználónév és/vagy jelszó).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight-eszközök a Visual Studio-kódhoz

Ha az 1.1.1-es vagy újabb verziót használja, frissítsen az Azure HDInsight Eszközök a [Visual Studio-kódhoz legújabb verziójára](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) a megszakítások elkerülése érdekében.

### <a name="azure-toolkit-for-intellij"></a>Az Azure Toolkit for IntelliJ

Ha a 3.20.0-s vagy újabb verziót használja, frissítsen [az Azure Toolkit for IntelliJ bővítmény legújabb verziójára a](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) megszakítások elkerülése érdekében.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake és Stream Analytics eszközök a Visual Studio számára

Frissítsen az Azure Data Lake és a Stream Analytics Tools for Visual Studio 2.3.9000.1-es vagy újabb [verziójára a](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) megszakítások elkerülése érdekében.  A frissítéssel kapcsolatban a [Visual Studio Data Lake Tools frissítésével](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)kapcsolatban található dokumentációban talál segítséget.

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Ha a 3.15.0-s vagy újabb verziót használja, frissítsen [az Azure Toolkit for Eclipse legújabb verziójára a](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) megszakítások elkerülése érdekében.

### <a name="sdk-for-net"></a>.NET-hez készült SDK

#### <a name="versions-1x-and-2x"></a>1.x és 2.x verziók

Frissítés a HDInsight SDK [2.1.0-s verziójára.](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) Minimális kódmódosításra lehet szükség, ha a módosítások által érintett módszert használ:

- `ClusterOperationsExtensions.GetClusterConfigurations`**a továbbiakban nem ad vissza bizalmas paramétereket,** például a tárolási kulcsokat (core-site) vagy a HTTP-hitelesítő adatokat (átjáró).
    - Az összes konfiguráció beolvasásához, beleértve `ClusterOperationsExtensions.ListConfigurations` a bizalmas paramétereket is, használja a jövőre nézve.  Ne feledje, hogy a "Reader" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a módszert. Ez lehetővé teszi a részletes szabályozást, amely felett a felhasználók hozzáférhetnek a fürt bizalmas adataihoz.
    - Csak a HTTP-átjáró hitelesítő `ClusterOperationsExtensions.GetGatewaySettings`adatainak beolvasásához használja a használatát.

- `ClusterOperationsExtensions.GetConnectivitySettings`a helyére a elavult, és a `ClusterOperationsExtensions.GetGatewaySettings`helyébe a .

- `ClusterOperationsExtensions.ConfigureHttpSettings`a helyére a elavult, és a `ClusterOperationsExtensions.UpdateGatewaySettings`helyébe a .

- `ConfigurationsOperationsExtensions.EnableHttp`és `DisableHttp` most elavult. A HTTP mostantól mindig engedélyezve van, így ezekre a módszerekre már nincs szükség.

#### <a name="versions-3x-and-up"></a>3.x verziók és újabb verziók

Frissítsen a HDInsight SDK [5.0.0-s](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) vagy újabb verziójára. Minimális kódmódosításra lehet szükség, ha a módosítások által érintett módszert használ:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**a továbbiakban nem ad vissza bizalmas paramétereket,** például a tárolási kulcsokat (core-site) vagy a HTTP-hitelesítő adatokat (átjáró).
    - Az összes konfiguráció beolvasásához, beleértve [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) a bizalmas paramétereket is, használja a jövőre nézve.Ne feledje, hogy a "Reader" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a módszert. Ez lehetővé teszi a részletes szabályozást, amely felett a felhasználók hozzáférhetnek a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)adatainak beolvasásához használja a használatát. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)a helyére a elavult, és a [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)helyébe a . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)és [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) most elavult. A HTTP mostantól mindig engedélyezve van, így ezekre a módszerekre már nincs szükség.

### <a name="sdk-for-python"></a>Pythonhoz készült SDK

Frissítsen a HDInsight SDK python-hoz [1.0.0-s](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) vagy újabb verziójára. Minimális kódmódosításra lehet szükség, ha a módosítások által érintett módszert használ:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**a továbbiakban nem ad vissza bizalmas paramétereket,** például a tárolási kulcsokat (core-site) vagy a HTTP-hitelesítő adatokat (átjáró).
    - Az összes konfiguráció beolvasásához, beleértve [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) a bizalmas paramétereket is, használja a jövőre nézve.Ne feledje, hogy a "Reader" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a módszert. Ez lehetővé teszi a részletes szabályozást, amely felett a felhasználók hozzáférhetnek a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)adatainak beolvasásához használja a használatát.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)a helyére a elavult, és a [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)helyébe a .

### <a name="sdk-for-java"></a>SDK Java-hoz

Frissítsen a HDInsight SDK Java-hoz [1.0.0-s](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) vagy újabb verziójára. Minimális kódmódosításra lehet szükség, ha a módosítások által érintett módszert használ:

- `ConfigurationsInner.get`**a továbbiakban nem ad vissza bizalmas paramétereket,** például a tárolási kulcsokat (core-site) vagy a HTTP-hitelesítő adatokat (átjáró).
- `ConfigurationsInner.update`most elavult.

### <a name="sdk-for-go"></a>SDK For Go

Frissítsen a HDInsight SDK for Go [27.1.0-s](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) vagy újabb verziójára. Minimális kódmódosításra lehet szükség, ha a módosítások által érintett módszert használ:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**a továbbiakban nem ad vissza bizalmas paramétereket,** például a tárolási kulcsokat (core-site) vagy a HTTP-hitelesítő adatokat (átjáró).
    - Az összes konfiguráció beolvasásához, beleértve [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) a bizalmas paramétereket is, használja a jövőre nézve.Ne feledje, hogy a "Reader" szerepkörrel rendelkező felhasználók nem fogják tudni használni ezt a módszert. Ez lehetővé teszi a részletes szabályozást, amely felett a felhasználók hozzáférhetnek a fürt bizalmas adataihoz. 
    - Csak a HTTP-átjáró hitelesítő [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)adatainak beolvasásához használja a használatát.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)a helyére a elavult, és a [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)helyébe a .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
A megszakítások elkerülése érdekében frissítsen [az Az PowerShell 2.0.0-s](https://www.powershellgallery.com/packages/Az) vagy újabb verziójára.  Minimális kódmódosításra lehet szükség, ha a módosítások által érintett módszert használ.
- `Grant-AzHDInsightHttpServicesAccess`most elavult, és az új `Set-AzHDInsightGatewayCredential` parancsmag váltotta fel.
- `Get-AzHDInsightJobOutput`a tárolókulcs részletes szerepköralapú elérésének támogatása érdekében frissült.
    - Ez nem érinti a HDInsight-fürt operátor, közreműködő vagy tulajdonos szerepkörrel rendelkező felhasználóit.
    - A csak Olvasó szerepkörrel rendelkező `DefaultStorageAccountKey` felhasználóknak explicit módon kell megadniuk a paramétert.
- `Revoke-AzHDInsightHttpServicesAccess`most elavult. A HTTP mostantól mindig engedélyezve van, így erre a parancsmagra már nincs szükség.
 Nézd meg [az az. HDInsight áttelepítési útmutató](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) további részletekért.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>A HDInsight fürtfelelős szerepkör-hozzárendelés hozzáadása felhasználóhoz

A [Tulajdonos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) szerepkörrel rendelkező felhasználók hozzárendelhetik a [HDInsight fürtoperátorszerepkört](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) azokhoz a felhasználókhoz, akiknek olvasási/írási hozzáféréssel kell rendelkeznie a bizalmas HDInsight-fürtkonfigurációs értékekhez (például a fürtátjáró hitelesítő adataihoz és a tárfiók kulcsaihoz).

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

A szerepkör-hozzárendelés hozzáadásának legegyszerűbb módja az `az role assignment create` Azure CLI parancsának használata.

> [!NOTE]
> Ezt a parancsot a Tulajdonos szerepkörrel rendelkező felhasználónak kell futtatnia, mivel csak ők adhatják meg ezeket az engedélyeket. A `--assignee` annak a felhasználónak a egyszerű szolgáltatás- vagy e-mail-címe, amelyhez hozzá szeretné rendelni a HDInsight fürtoperátor szerepkört. Ha nem megfelelő engedélyhiba jelenik meg, olvassa el az alábbi gyakori kérdéseket.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Szerep megadása erőforrás (fürt) szintjén

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Szerep megadása erőforráscsoport szintjén

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Szerepkör megadása az előfizetés szintjén

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Másik lehetőségként használhatja az Azure Portalon a HDInsight fürtoperátor szerepkör-hozzárendelés hozzáadása a felhasználóhoz. Tekintse meg a dokumentációt, [az Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával – Szerepkör-hozzárendelés hozzáadása.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)

## <a name="faq"></a>GYIK

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Miért jelenik meg a 403-as (tiltott művelet) válasz az API-kérelmek és/vagy az eszköz frissítésekor?

A fürtkonfigurációk most már a részletes szerepköralapú `Microsoft.HDInsight/clusters/configurations/*` hozzáférés-vezérlés mögött vannak, és a hozzáféréshez engedélyre van szükség. Az engedély megszerzéséhez rendelje hozzá a HDInsight fürtoperátor, közreműködő vagy tulajdonos szerepkört a konfigurációk elérését próbáló felhasználóhoz vagy egyszerű szolgáltatáshoz.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Miért jelenik meg az "Elégtelen jogosultságok a művelet végrehajtásához" az Azure CLI parancs futtatásakor, hogy a HDInsight fürtkezelő szerepkört egy másik felhasználóhoz vagy egyszerű szolgáltatáshoz rendelje?

Amellett, hogy a tulajdonos szerepkör, a felhasználó vagy a parancs egyszerű végrehajtó felhasználó vagy egyszerű szolgáltatás a parancsot kell rendelkeznie a megfelelő Azure AD-engedélyeket, hogy keresse meg a hozzárendelt objektumazonosítók. Ez az üzenet azt jelzi, hogy nem rendelkezik elegendő Azure AD-engedélyekkel. Próbálja meg `-–assignee` lecserélni az argumentumot, `–assignee-object-id` és adja meg a hozzárendelt objektumazonosítóját paraméterként a név helyett (vagy felügyelt identitás esetén a fő azonosítót). További információkért tekintse meg az [az szerepkör-hozzárendelés dokumentáció létrehozásának](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) választható paraméterekkel kapcsolatos szakaszát.

Ha ez továbbra sem működik, forduljon az Azure AD-rendszergazdához a megfelelő engedélyek beszerzéséhez.

### <a name="what-will-happen-if-i-take-no-action"></a>Mi történik, ha nem cselekszem?

2019. szeptember 3-tól `POST /configurations/gateway` kezdődően a hívások a `GET /configurations/{configurationName}` továbbiakban nem adnak vissza semmilyen információt, `GET /configurations` és a hívás a továbbiakban nem ad vissza bizalmas paramétereket, például a tárfiók kulcsait vagy a fürt jelszavát. Ugyanez igaz a megfelelő SDK-metódusokra és a PowerShell-parancsmagokra is.

Ha a fent említett Visual Studio, VSCode, IntelliJ vagy Eclipse egyik eszközének régebbi verzióját használja, azok a frissítésig nem fognak működni.

További információkért tekintse meg a megfelelő szakasza ebben a dokumentumban a forgatókönyvhöz.
