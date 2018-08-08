---
title: Az Azure HDInsight .NET-alkalmazások létrehozása a nem interaktív hitelesítés
description: Ismerje meg, hogyan hozhat létre a nem interaktív hitelesítés a Microsoft .NET-alkalmazások az Azure HDInsight.
editor: jasonwhowell
services: hdinsight
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: f5f1aae62d8f2959f26a361f3c1187037cafcca5
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591519"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Hozzon létre egy .NET HDInsight-alkalmazás nem interaktív hitelesítés
Az alkalmazás saját identitás (nem interaktív), vagy a bejelentkezett felhasználó az alkalmazás (interaktív) alatt a Microsoft .NET Azure HDInsight alkalmazását futtathatja. Ez a cikk bemutatja, hogyan hozhat létre egy .NET-alkalmazás csatlakoztatása az Azure és a HDInsight kezelése nem interaktív hitelesítéssel. A minta egy interaktív alkalmazás: [csatlakozhat az Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

A nem interaktív .NET-alkalmazás lesz szüksége:

* Az Azure-előfizetés Bérlőazonosító (más néven egy *címtár-azonosító*). Lásd: [Bérlőazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* Az Azure Active Directory (Azure AD-) alkalmazás ügyfél-azonosítóval. Lásd: [létrehozása az Azure Active Directory-alkalmazás](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) és [egy alkalmazás Azonosítójának lekéréséhez](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* Az Azure AD-alkalmazás titkos kulcsát. Lásd: [Get kérelem hitelesítési kulcs](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Előfeltételek
* Egy HDInsight-fürtön. Tekintse meg a [kezdeti lépéseket ismertető oktatóanyag](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Szerepkör hozzárendelése az Azure AD-alkalmazás
Az Azure AD-alkalmazás hozzárendelése egy [szerepkör](../role-based-access-control/built-in-roles.md), biztosítania jogosultságokkal műveletek végrehajtásához. Beállíthatja a hatókör szintjén is az előfizetés, erőforráscsoport vagy erőforrás. Az engedélyek öröklődnek hatókör alacsonyabb szintre. (Például egy alkalmazás az Olvasó szerepkörhöz, egy erőforráscsoport hozzáadása azt jelenti, hogy az alkalmazás olvashatja, az erőforráscsoportot és a benne erőforrást.) Ebben az oktatóanyagban, állítsa be a hatókört az erőforráscsoport szintjén. További információkért lásd: [az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése a szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md).

**A tulajdonosi szerepkör hozzáadása az Azure AD-alkalmazás**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **erőforráscsoport**.
3. Válassza ki az erőforráscsoportot, amely rendelkezik a HDInsight-fürt, amelyre később az oktatóanyagban a Hive-lekérdezést fog futni. Ha nagy számú erőforráscsoportok, a szűrő segítségével keresse meg a megfelelőt.
4. Válassza ki az erőforráscsoport menüben **hozzáférés-vezérlés (IAM)**.
5. A **felhasználók**válassza **Hozzáadás**.
6. Kövesse az utasításokat a tulajdonosi szerepkör hozzáadása az Azure AD-alkalmazást. Miután sikeresen hozzáadta a szerepkör, az alkalmazás részen **felhasználók**, a tulajdonosi szerepkörrel rendelkező. 

## <a name="develop-an-hdinsight-client-application"></a>Egy HDInsight-ügyfél alkalmazás fejlesztése

1. Hozzon létre egy C# konzolalkalmazást.
2. Adja hozzá a következő NuGet-csomagokat:

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


## <a name="next-steps"></a>További lépések
* [Az Azure Active Directory alkalmazás és -szolgáltatásnév létrehozása az Azure Portalon](../azure-resource-manager/resource-group-create-service-principal-portal.md).
* Ismerje meg, hogyan [hitelesítése egy egyszerű szolgáltatást az Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* Ismerje meg [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md).
