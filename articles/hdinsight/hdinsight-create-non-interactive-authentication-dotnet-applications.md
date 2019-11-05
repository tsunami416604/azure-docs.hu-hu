---
title: Nem interaktív hitelesítéssel rendelkező .NET-alkalmazás – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre nem interaktív hitelesítési Microsoft .NET alkalmazásokat az Azure HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 0781d9fd58e079517b3f3dc8fba06fb448a8fa19
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494917"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Nem interaktív hitelesítéssel rendelkező .NET HDInsight-alkalmazás létrehozása
A Microsoft .NET Azure HDInsight alkalmazást futtathatja az alkalmazás saját identitása (nem interaktív) vagy az alkalmazás bejelentkezett felhasználójának identitása alatt (interaktív). Ez a cikk bemutatja, hogyan hozhat létre egy nem interaktív hitelesítési .NET-alkalmazást az Azure-hoz való kapcsolódáshoz és a HDInsight kezeléséhez. Egy interaktív alkalmazás mintája: [Kapcsolódás az Azure HDInsight-hoz](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

A nem interaktív .NET-alkalmazásból a következőkre lesz szüksége:

* Az Azure-előfizetés bérlői azonosítója (más néven *címtár-azonosító*). Lásd: [bérlői azonosító lekérése](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Az Azure Active Directory (Azure AD) alkalmazás ügyfél-azonosítója. Lásd: [Azure Active Directory alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) és [alkalmazás-azonosító beszerzése](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Az Azure AD-alkalmazás titkos kulcsa. Lásd: [alkalmazás hitelesítési kulcsának beolvasása](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Előfeltételek
* An méretű HDInsight-fürt. Tekintse meg az [első lépéseket ismertető oktatóanyagot](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Szerepkör társítása az Azure AD-alkalmazáshoz
Rendeljen hozzá egy [szerepkört](../role-based-access-control/built-in-roles.md)az Azure ad-alkalmazáshoz, hogy engedélyeket adjon a műveletekhez. Megadhatja a hatókört az előfizetés, az erőforráscsoport vagy az erőforrás szintjén. Az engedélyek a hatókör alacsonyabb szintjein lesznek örökölve. (Ha például egy erőforráscsoport olvasó szerepköréhez ad hozzá egy alkalmazást, az azt jelenti, hogy az alkalmazás elolvashatja az erőforráscsoportot és a benne lévő erőforrásokat.) Ebben a cikkben a hatókört az erőforráscsoport szintjén állíthatja be. További információkért lásd: [szerepkör-hozzárendelések használata az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez](../role-based-access-control/role-assignments-portal.md).

**A tulajdonosi szerepkör hozzáadása az Azure AD-alkalmazáshoz**

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza az **Erőforráscsoportok** elemet.
3. Válassza ki azt az erőforráscsoportot, amelyen a HDInsight-fürt található, amelyen a jelen cikk későbbi részében futtatni fogja a kaptár-lekérdezést. Ha nagy számú erőforráscsoportot használ, a szűrőt használva megkeresheti a kívánt csoportot.
4. Az erőforráscsoport menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
5. Válassza ki a **szerepkör-hozzárendelések** lapot az aktuális szerepkör-hozzárendelések megtekintéséhez.
6. A lap tetején válassza a **szerepkör-hozzárendelés hozzáadása**elemet.
7. Kövesse az utasításokat, és adja hozzá a tulajdonosi szerepkört az Azure AD-alkalmazáshoz. Miután sikeresen hozzáadta a szerepkört, az alkalmazás megjelenik a tulajdonos szerepkör alatt. 

## <a name="develop-an-hdinsight-client-application"></a>HDInsight-ügyfélalkalmazás fejlesztése

1. Hozzon létre egy C# konzolalkalmazást.
2. Adja hozzá a következő [NuGet](https://www.nuget.org/) -csomagokat:

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
* [Hozzon létre egy Azure Active Directory alkalmazást és szolgáltatásnevet a Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).
* Megtudhatja, hogyan [hitelesítheti az egyszerű szolgáltatásokat Azure Resource Managerokkal](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Ismerje meg az [Azure szerepköralapú Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md)című témakört.
