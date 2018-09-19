---
title: 'Szolgáltatások közötti hitelesítés: .NET SDK-val rendelkező Azure Data Lake Storage Gen1 az Azure Active Directory |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 .NET SDK használatával az Azure Active Directory használatával
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: bd03c0801fed0da6d9a87466bc33819f6afa4578
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296923"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 .NET SDK használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Ebben a cikkben megismerkedhet a .NET SDK használata a szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 tennie. Végfelhasználói hitelesítés a Data Lake Storage Gen1 .NET SDK használatával, lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md).


## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2013, 2015 vagy 2017** Az alábbi utasítások a Visual Studio 2017-et használják.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Az Azure Active Directory "" webalkalmazás létrehozásához**. El kell végeznie a lépések [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>.NET-alkalmazás létrehozása
1. Nyissa meg a Visual Studiót, és hozzon létre egy konzolalkalmazást.
2. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
3. Az **Új projekt** területen írja be vagy válassza ki az alábbi értékeket:

   | Tulajdonság | Érték |
   | --- | --- |
   | Kategória |Sablonok/Visual C#/Windows |
   | Sablon |Konzolalkalmazás |
   | Name (Név) |CreateADLApplication |
4. A projekt létrehozásához kattintson az **OK** gombra.

5. Adja hozzá a NuGet-csomagokat a projekthez.

   1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
   2. Győződjön meg arról, hogy a **NuGet Package Manager** (NuGet-csomagkezelő) lapon a **Package source** (Csomag forrása) értéke **nuget.org**, és az **Include prerelease** (Előzetes verzió belefoglalása) jelölőnégyzet be van jelölve.
   3. Keresse meg és telepítse az alábbi NuGet-csomagokat:

      * `Microsoft.Azure.Management.DataLake.Store` – Ez az oktatóanyag a 2.1.3-as előzetes verziót használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez az oktatóanyag a 2.2.12-es verziót használja.

        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Új Azure Data Lake-fiók létrehozása")
   4. Zárja be a **NuGet-csomagkezelőt**.

6. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

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

## <a name="service-to-service-authentication-with-client-secret"></a>Szolgáltatások közötti hitelesítés a titkos Ügyfélkód
Ez a kódrészlet adja hozzá a .NET-ügyfélalkalmazás. A helyőrző értékeket cserélje le az értékeket (az előfeltételként felsorolt) egy Azure AD-webalkalmazás lekért.  Ez a kódrészlet lehetővé teszi az alkalmazás hitelesítéséhez **nem interaktív módon** Data Lake Storage Gen1 használja az ügyfél titkos/kulcsát az Azure AD-webalkalmazás. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
        var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
        var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
    }

A portáladatbázis előző kódrészlet egy segédfüggvény `GetCreds_SPI_SecretKey`. Ez a segédfüggvény a kódját érhető el [Itt a Githubon](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Szolgáltatások közötti hitelesítés tanúsítvánnyal

Ez a kódrészlet adja hozzá a .NET-ügyfélalkalmazás. A helyőrző értékeket cserélje le az értékeket (az előfeltételként felsorolt) egy Azure AD-webalkalmazás lekért. Ez a kódrészlet lehetővé teszi az alkalmazás hitelesítéséhez **nem interaktív módon** Data Lake Storage Gen1 használja a tanúsítvány egy Azure AD-webalkalmazás. Az Azure AD-alkalmazás létrehozása az utasításokért lásd: [egyszerű szolgáltatás létrehozása a tanúsítványok](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    
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

A portáladatbázis előző kódrészlet egy segédfüggvény `GetCreds_SPI_Cert`. Ez a segédfüggvény a kódját érhető el [Itt a Githubon](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan service-to-service-hitelesítés használatára való hitelesítés a Data Lake Storage Gen1 .NET SDK használatával. Most már megtekintheti a következő cikkeket, hogy hogyan használhatja a .NET SDK használata a Data Lake Storage Gen1 beszélni.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [A Data Lake Storage Gen1 Adatműveletek .NET SDK használatával](data-lake-store-data-operations-net-sdk.md)


