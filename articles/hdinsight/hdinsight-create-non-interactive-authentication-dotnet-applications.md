---
title: Nem interaktív hitelesítés .NET alkalmazás – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre nem interaktív hitelesítést a Microsoft .NET alkalmazások az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 5e6a0586bc750f8972586920c15dbb297295aa20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371273"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Nem interaktív hitelesítés .NET HDInsight alkalmazás létrehozása

A Microsoft .NET Azure HDInsight-alkalmazást az alkalmazás saját identitása (nem interaktív) vagy az alkalmazás bejelentkezett felhasználójának (interaktív) alatt futtathatja. Ez a cikk bemutatja, hogyan hozhat létre egy nem interaktív hitelesítési .NET alkalmazást az Azure-hoz való csatlakozáshoz és a HDInsight kezeléséhez. Egy interaktív alkalmazás mintáját az [Csatlakozzak az Azure HDInsighthoz (Csatlakozás az Azure HDInsighthoz) témakörben várja.](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)

A nem interaktív .NET alkalmazásból a következőkre van szükség:

* Az Azure-előfizetés bérlői azonosítója (más néven *címtárazonosító).* Lásd: [Bérlőazonosító beszereznie.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)
* Az Azure Active Directory (Azure AD) alkalmazásügyfél-azonosító. Lásd: [Azure Active Directory-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) és [alkalmazásazonosító beszereznie.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)
* Az Azure AD-alkalmazás titkos kulcsa. Lásd: [Alkalmazáshitelesítési kulcs beszereznie.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)

## <a name="prerequisites"></a>Előfeltételek

Egy HDInsight-fürt. Tekintse meg az [első lépések bemutatót](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Szerepkör hozzárendelése az Azure AD-alkalmazáshoz

Rendeljen hozzá az Azure AD-alkalmazásegy [szerepkört,](../role-based-access-control/built-in-roles.md)hogy adjon engedélyt a műveletek végrehajtására. A hatókört az előfizetés, az erőforráscsoport vagy az erőforrás szintjén állíthatja be. Az engedélyek alacsonyabb hatókörszintekre öröklődnek. Például egy alkalmazás hozzáadása az olvasó szerepköregy erőforráscsoport azt jelenti, hogy az alkalmazás képes olvasni az erőforráscsoport és az erőforrások benne. Ebben a cikkben a hatókört erőforráscsoport-szinten állíthatja be. További információ: [Szerepkör-hozzárendelések használata az Azure-előfizetési erőforrásokhoz való hozzáférés kezeléséhez.](../role-based-access-control/role-assignments-portal.md)

**A tulajdonosi szerepkör hozzáadása az Azure AD-alkalmazáshoz**

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg azt az erőforráscsoportot, amely rendelkezik azzal a HDInsight-fürttel, amelyen a cikk későbbi részében a Hive-lekérdezést futtatni fogja. Ha sok erőforráscsoporttal rendelkezik, a szűrő segítségével megkeresheti a kívánt erőforráscsoportot.
1. Kattintson az erőforráscsoport menü **Hozzáférés-vezérlés (IAM) parancsára.**
1. Az aktuális szerepkör-hozzárendelések megtekintéséhez jelölje ki a **Szerepkör-hozzárendelések** lapot.
1. A lap tetején válassza a **+ Hozzáadás lehetőséget.**
1. Kövesse az utasításokat a tulajdonosi szerepkör hozzáadása az Azure AD-alkalmazáshoz. Miután sikeresen hozzáadta a szerepkört, az alkalmazás a Tulajdonos szerepkör alatt jelenik meg.

## <a name="develop-an-hdinsight-client-application"></a>HDInsight-ügyfélalkalmazás fejlesztése

1. Hozzon létre egy C# konzolalkalmazást.
2. Adja hozzá a következő [NuGet](https://www.nuget.org/) csomagokat:

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

* [Hozzon létre egy Azure Active Directory-alkalmazást és egyszerű szolgáltatást az Azure Portalon.](../active-directory/develop/howto-create-service-principal-portal.md)
* Ismerje meg, hogyan [hitelesítheti az egyszerű szolgáltatást az Azure Resource Manager segítségével.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* További információ [az Azure szerepköralapú hozzáférés-vezérlésről (RBAC).](../role-based-access-control/role-assignments-portal.md)
