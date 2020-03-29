---
title: 'Végfelhasználói hitelesítés: .NET SDK az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával | Microsoft dokumentumok'
description: Megtudhatja, hogy miként érheti el a végfelhasználói hitelesítést az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával a .NET SDK használatával
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
ms.openlocfilehash: 215b839c21c2590c08ac2f4250086eaf97914ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66243710"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 használatával a .NET SDK használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ebben a cikkben megtudhatja, hogyan használhatja a .NET SDK-t az Azure Data Lake Storage Gen1 végfelhasználói hitelesítéséhez. A .NET SDK használatával a Data Lake Storage Gen1 szolgáltatás-szolgáltatás hitelesítése a [Szolgáltatás-szolgáltatás hitelesítése a Data Lake Storage Gen1 használatával a .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md)című témakörben található.

## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2013-as vagy újabb.** Az alábbi utasítások a Visual Studio 2019-et használják.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "natív" alkalmazást.** Az Azure Active Directory használatával el kell végeznie a végfelhasználói hitelesítés lépéseit a [Data Lake Storage Gen1 szolgáltatással.](data-lake-store-end-user-authenticate-using-active-directory.md)

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

5. **Nyitott Program.cs**
6. Cserélje le a használó utasításokra a következő sorokra:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
Adja hozzá ezt a kódrészletet a .NET ügyfélalkalmazáshoz. Cserélje le a helyőrző értékeket az Azure AD natív alkalmazásból beolvasott értékekkel (előfeltételként szerepel). Ez a kódrészlet lehetővé teszi az alkalmazás **interaktív** hitelesítését a Data Lake Storage Gen1 segítségével, ami azt jelenti, hogy a rendszer kéri az Azure-hitelesítő adatok megadását.

A könnyebb használat érdekében a következő kódrészlet az ügyfélazonosító alapértelmezett értékeit használja, és átirányítja az Azure-előfizetésre érvényes URI-t. A következő kódrészletben csak meg kell adnia a bérlői azonosító értékét. A bérlői azonosítót a [Bérlőazonosító beolvasása](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)című oldalon található utasítások segítségével kérheti be.
    
- Cserélje le a Main() függvényt a következő kódra:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Néhány tudnivaló az előző kódrészletről:

* Az előző kódrészlet segítő függvényeket használ, `GetTokenCache` és `GetCreds_User_Popup`. A segítő funkciók kódja itt érhető el [a GitHubon.](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache)
* Az oktatóanyag gyorsabb befejezése érdekében a kódrészlet egy natív alkalmazásügyfél-azonosítót használ, amely alapértelmezés szerint minden Azure-előfizetéshez elérhető. Így **a kódrészletet változtatás nélkül használhatja az alkalmazásában**.
* Ha azonban a saját Azure AD-tartományát és alkalmazásügyfél-azonosítóját szeretné használni, létre kell hoznia egy natív Azure AD-alkalmazást, majd a létrehozott alkalmazáshoz használnia kell az Azure AD-bérlőazonosítót, az ügyfél-azonosítót és az átirányítási URI-t. Az utasításokat a [Data Lake Storage Gen1 szolgáltatással való végfelhasználói hitelesítéshez](data-lake-store-end-user-authenticate-using-active-directory.md) olvassa el az Active Directory-alkalmazás létrehozása című témakörben.

  
## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhatja a végfelhasználói hitelesítést az Azure Data Lake Storage Gen1 használatával a .NET SDK használatával. Most már tekintse meg az alábbi cikkeket, amelyek arról beszélnek, hogyan használhatja a .NET SDK-t az Azure Data Lake Storage Gen1 használatához.

* [A Data Lake Storage Gen1 számlakezelési műveletei a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [Adatműveletek a Data Lake Storage Gen1-en a .NET SDK használatával](data-lake-store-data-operations-net-sdk.md)

