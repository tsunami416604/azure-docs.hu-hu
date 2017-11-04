---
title: "Az Azure HDInsight .NET-alkalmazások létrehozása a nem interaktív hitelesítés |} Microsoft Docs"
description: "Útmutató az Azure HDInsight a Microsoft .NET-alkalmazások létrehozása a nem interaktív hitelesítés."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jgao
ms.openlocfilehash: 00eb9f6e13035ba5827b70e911be4afeafd442a2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>A nem interaktív hitelesítés .NET HDInsight-alkalmazások létrehozása
A Microsoft .NET Azure HDInsight-alkalmazásokat, az alkalmazás saját identitással (nem interaktív) vagy a bejelentkezett felhasználó az alkalmazás (interaktív) alapján is futtathatja. Ez a cikk bemutatja, hogyan hozzon létre egy nem interaktív hitelesítés az Azure-bA csatlakoztatni és kezelni a HDInsight .NET-alkalmazásokat. Interaktív alkalmazás mintát, lásd: [csatlakozás az Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

A nem interaktív .NET-alkalmazás lesz szüksége:

* Az Azure-előfizetés-bérlőazonosító beszerzése (más néven a *könyvtár-azonosítója*). Lásd: [-bérlőazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* Az Azure Active Directory (Azure AD) alkalmazás ügyfél-azonosítót. Lásd: [hozzon létre egy Azure Active Directory-alkalmazás](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) és [az alkalmazás azonosítót](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* Az Azure AD-alkalmazás titkos kulcs. Lásd: [Get alkalmazás hitelesítési kulcs](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Előfeltételek
* HDInsight-fürtöt. Tekintse meg a [használatába bevezető oktatóanyagot](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Szerepkör hozzárendelése az Azure AD-alkalmazás
Az Azure AD alkalmazás hozzárendelése egy [szerepkör](../active-directory/role-based-access-built-in-roles.md), biztosítania jogosultságokkal műveletek végrehajtásához. A hatókör szintjén található az előfizetés, erőforráscsoportból vagy erőforrás állíthatja be. Az engedélyek hatóköre alacsonyabb szintű származnak. (Például egy alkalmazást az olvasó szerepkört erőforráscsoport hozzáadása azt jelenti, hogy az alkalmazás elolvashatják az erőforráscsoport és bármely-erőforrásokat.) Ebben az oktatóanyagban hatókör, az erőforráscsoport szintjén beállítása. További információkért lásd: [az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése a szerepkör-hozzárendelések segítségével](../active-directory/role-based-access-control-configure.md).

**A tulajdonosi szerepkört az Azure AD-alkalmazás hozzáadása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **erőforráscsoport**.
3. Válassza ki az erőforráscsoportot, amely rendelkezik a HDInsight-fürt, amelyen futtatja a Hive-lekérdezést az oktatóanyag későbbi részében. Ha nagy számú erőforráscsoporttal rendelkezik, a szűrő segítségével találja a megfelelőt.
4. Válassza ki az erőforrás csoport menü **hozzáférés-vezérlés (IAM)**.
5. A **felhasználók**, jelölje be **Hozzáadás**.
6. Kövesse az utasításokat a tulajdonosi szerepkört hozzá az Azure AD-alkalmazáshoz. Miután sikeresen hozzáadta a szerepkör, az alkalmazás megtalálható-e **felhasználók**, a tulajdonos szerepkörrel. 

## <a name="develop-an-hdinsight-client-application"></a>Ügyfél HDInsight-alkalmazások fejlesztése

1. Hozzon létre egy C# konzolalkalmazást.
2. Adja hozzá a következő NuGet-csomagok:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Futtassa a következő kódot:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>Következő lépések
* [Hozzon létre egy Azure Active Directory alkalmazás és szolgáltatás egyszerű Azure-portálon](../azure-resource-manager/resource-group-create-service-principal-portal.md).
* Megtudhatja, hogyan [hitelesítéséhez az Azure Resource Manager szolgáltatásnevet](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* További tudnivalók [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../active-directory/role-based-access-control-configure.md).
