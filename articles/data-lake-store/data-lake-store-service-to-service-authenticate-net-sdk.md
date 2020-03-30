---
title: 'Szolgáltatás-szolgáltatás hitelesítés: .NET SDK az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával | Microsoft dokumentumok'
description: Megtudhatja, hogy miként valósítható meg a szolgáltatás-szolgáltatás hitelesítés az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával a .NET SDK használatával
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 96c496ef67e26a3079577bf52e9d019d963467b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265531"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Szolgáltatás-szolgáltatás hitelesítés az Azure Data Lake Storage Gen1 használatával a .NET SDK használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Ebből a cikkből megtudhatja, hogyan használhatja a .NET SDK szolgáltatás-szolgáltatás hitelesítést az Azure Data Lake Storage Gen1 használatával. A .NET SDK használatával a Data Lake Storage Gen1 segítségével történő végfelhasználói hitelesítést a [Data Lake Storage Gen1 használatával a .NET SDK használatával című](data-lake-store-end-user-authenticate-net-sdk.md)témakörben található.

## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2013-as vagy újabb.** Az alábbi utasítások a Visual Studio 2019-et használják.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "web" alkalmazást.** Az Azure Active Directory használatával el kell végeznie a Szolgáltatás-szolgáltatás hitelesítés lépéseit a [Data Lake Storage Gen1 szolgáltatással.](data-lake-store-service-to-service-authenticate-using-active-directory.md)

## <a name="create-a-net-application"></a>.NET-alkalmazás létrehozása
1. A Visual Studio programban válassza a **Fájl** menü **Új**, majd **A Project**parancsot.
2. Válassza **a Console App (.NET Framework)** lehetőséget, majd a **Tovább**gombot.
3. A Project név `CreateADLApplication` **mezőbe**írja be a beírt lehetőséget, majd válassza a **Létrehozás lehetőséget.**

4. Adja hozzá a NuGet-csomagokat a projekthez.

   1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
   2. A **NuGet csomagkezelő** lapon győződjön meg arról, hogy **a Csomagforrás** **nuget.org,** és hogy **a Kiadás előtti belefoglalás** jelölőnégyzet be van jelölve.
   3. Keresse meg és telepítse az alábbi NuGet-csomagokat:

      * `Microsoft.Azure.Management.DataLake.Store` – Ez az oktatóanyag a 2.1.3-as előzetes verziót használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez az oktatóanyag a 2.2.12-es verziót használja.

        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Új Azure Data Lake-fiók létrehozása")
   4. Zárja be a **NuGet-csomagkezelőt**.

5. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Szolgáltatás-szolgáltatás hitelesítés ügyféltitkossszal
Adja hozzá ezt a kódrészletet a .NET ügyfélalkalmazáshoz. Cserélje le a helyőrző értékeket az Azure AD webalkalmazásból beolvasott értékekre (előfeltételként szerepel). Ez a kódrészlet lehetővé teszi, hogy **az azure-beli** AD-webalkalmazás ügyféltitkos/kulcs használatával nem interaktív módon hitelesítse az alkalmazást a Data Lake Storage Gen1 használatával.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

Az előző kódrészlet egy segítő `GetCreds_SPI_SecretKey`függvényt használ. A segítő funkció kódja itt érhető el [a GitHubon.](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey)

## <a name="service-to-service-authentication-with-certificate"></a>Szolgáltatás-szolgáltatás hitelesítés tanúsítvánnyal

Adja hozzá ezt a kódrészletet a .NET ügyfélalkalmazáshoz. Cserélje le a helyőrző értékeket az Azure AD webalkalmazásból beolvasott értékekre (előfeltételként szerepel). Ez a kódrészlet lehetővé teszi, hogy az alkalmazás **nem interaktív an-** és azure AD-webalkalmazás tanúsítványának használatával hitelesítse az alkalmazást. Az Azure AD-alkalmazások létrehozásáról az Egyszerű [szolgáltatás létrehozása tanúsítványokkal című](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)témakörben talál.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

Az előző kódrészlet egy segítő `GetCreds_SPI_Cert`függvényt használ. A segítő funkció kódja itt érhető el [a GitHubon.](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert)

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhatja a szolgáltatás-szolgáltatás hitelesítést a Data Lake Storage Gen1 használatával a .NET SDK használatával. Most már tekintse meg a következő cikkeket, amelyek arról beszélnek, hogyan használhatja a .NET SDK-t a Data Lake Storage Gen1 használatához.

* [A Data Lake Storage Gen1 számlakezelési műveletei a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [Adatműveletek a Data Lake Storage Gen1-en a .NET SDK használatával](data-lake-store-data-operations-net-sdk.md)
