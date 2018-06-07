---
title: 'Végfelhasználói hitelesítési: .NET SDK-val Data Lake Store az Azure Active Directoryval |} Microsoft Docs'
description: Megtudhatja, hogyan végfelhasználói hitelesítési elérése a Data Lake Store .NET SDK-val az Azure Active Directoryval
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
ms.openlocfilehash: cbb0f703f61b6c15b3a827dc75821286b7914c21
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623961"
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Végfelhasználói hitelesítési a Data Lake Store .NET SDK használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ebből a cikkből megismerheti az Azure Data Lake Store végfelhasználói hitelesítés elvégzéséhez a .NET SDK használatával kapcsolatos. A szolgáltatások közötti hitelesítéshez a Data Lake Store .NET SDK használatával, lásd: [szolgáltatások közötti hitelesítés a Data Lake Store .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2013, 2015 vagy 2017** Az alábbi utasítások a Visual Studio 2017-et használják.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy "Natív" Azure Active Directory-alkalmazás**. Végrehajtotta a lépéseket [végfelhasználói hitelesítés az Azure Active Directory használatával a Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

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

6. Nyissa meg **Program.cs**
7. Cserélje le a használatával utasítások a következő sort:

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
Adja hozzá ezt a kódrészletet a .NET-ügyfélalkalmazást. Cserélje le a helyőrző értékeket egy natív Azure AD-alkalmazást, (előfeltétel szerepel) lekért értékekkel. Ezt a kódrészletet lehetővé teszi, hogy az alkalmazás hitelesítéséhez **interaktív** a Data Lake Store, ami azt jelenti, hogy a Azure hitelesítő adatainak megadását kéri.

Könnyű használatra az alábbi kódrészletben ügyfél-Azonosítóját és átirányítási URI-t, amelyek érvényesek a bármely Azure-előfizetéshez tartozó alapértelmezett értékeket használja. Az alábbi kódrészletben szereplő meg kell adnia az értéket a bérlő azonosítóját. Az utasításokat követve a bérlői azonosító le [a bérlőazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
- Cserélje le a Main() függvény a következő kódot:

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

Néhány tudnivaló az előző részlet kapcsolatban:

* A portáladatbázis előző részlet használ egy súgófunkciókat `GetTokenCache` és `GetCreds_User_Popup`. Ezek súgófunkciókat kódjának érhető [ide a Githubon](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Segítségre van szüksége az oktatóanyag gyorsabban befejeződjenek, a részlet használ egy natív alkalmazás ügyfél-Azonosítót, amely alapértelmezés szerint az összes Azure-előfizetések érhető el. Így **a kódrészletet változtatás nélkül használhatja az alkalmazásában**.
* Ha azonban a saját Azure AD-tartományát és alkalmazásügyfél-azonosítóját szeretné használni, létre kell hoznia egy natív Azure AD-alkalmazást, majd a létrehozott alkalmazáshoz használnia kell az Azure AD-bérlőazonosítót, az ügyfél-azonosítót és az átirányítási URI-t. Útmutatásért tekintse meg az [Active Directory-alkalmazás a Data Lake Store-ral, végfelhasználói hitelesítéshez való létrehozásával](data-lake-store-end-user-authenticate-using-active-directory.md) kapcsolatos témakört.

  
## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta végfelhasználói hitelesítés használata a hitelesítéshez az Azure Data Lake Store .NET SDK használatával. Most már megtekintheti a következő cikkekben talál, amely az Azure Data Lake Store működéséhez a .NET SDK használatával kapcsolatban.

* [Fiókkezelési műveletek a Data Lake Store-ban a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [Adatok műveletek a Data Lake Store .NET SDK használatával](data-lake-store-data-operations-net-sdk.md)

