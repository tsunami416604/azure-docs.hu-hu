---
title: Media Services a .NET Development számítógép beállítása
description: További információk a Media Services .NET-keretrendszerhez készült Media Services SDK segítségével előfeltételeit. Is megtudhatja, hogyan hozzon létre egy Visual Studio alkalmazást.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 639d1d6af169a0bb459dd8d6c778503b60c48e2c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788717"
---
# <a name="media-services-development-with-net"></a>A .NET Media Services-fejlesztés
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

A cikkből megtudhatja, hogyan kell elindítani a .NET használatával Media Services-alkalmazások fejlesztésével.

A **Azure Media Services .NET SDK** library lehetővé teszi a Media Services .NET segítségével elleni programhoz. Még jobban könnyebb a használatával történő fejlesztést .NET, a **Azure Media Services .NET SDK-bővítmények** könyvtár valósul meg. Ezt a szalagtárat kiegészítő módszerek és segédfüggvények találhatók, amelyek egyszerűbbé teszik a .NET-kódot tartalmaz. Mindkét szalagtárak keresztül érhetők el **NuGet** és **GitHub**.

## <a name="prerequisites"></a>Előfeltételek
* Egy Media Services-fiók egy új vagy meglévő Azure-előfizetésben. A cikkben [Media Services-fiók létrehozása](media-services-portal-create-account.md).
* Operációs rendszerek: Windows 10, Windows 7, Windows 2008 R2 vagy Windows 8.
* .NET-keretrendszer 4.5-ös vagy újabb.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása
Ez a szakasz bemutatja, hogyan elvégezze a Media Services-fejlesztés és a Visual Studio-projekt létrehozása.  Ebben az esetben a projekt egy Windows C# konzolalkalmazást, de a telepítő lépéseket Itt hozhat létre a Media Services-alkalmazások (például a Windows Forms alkalmazások vagy az ASP.NET webalkalmazás) projektek más típusú vonatkozik.

Ez a szakasz bemutatja, hogyan használható **NuGet** Media Services .NET SDK-bővítmények és más függő kódtárak hozzáadása.

Azt is megteheti, a Media Services .NET SDK legújabb bits kaphat a Githubról ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) vagy [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), a megoldás felépítéséhez és az ügyfélprojekt hivatkozásokat adni. A szükséges függőségek letöltött és kibontott automatikusan.

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást. Adja meg a **neve**, **hely**, és **megoldásnév**, majd kattintson az OK gombra.
2. Hozza létre a megoldást.
3. Használjon **NuGet** telepítése és hozzáadása **Azure Media Services .NET SDK-bővítmények** (**windowsazure.mediaservices.extensions**). Ennek a csomagnak a telepítése a **Media Services .NET SDK**csomagot és az összes további szükséges függőséget is feltelepíti
   
    Győződjön meg arról, hogy rendelkezik-e telepítve NuGet legújabb verzióját. További információk és a telepítési utasításokért lásd: [NuGet](http://nuget.codeplex.com/).

    1. A Megoldáskezelőben kattintson a jobb gombbal a projekt nevét, és válassza a **NuGet-csomagok kezelése**.

    2. A NuGet-csomagok kezelése párbeszédpanel jelenik meg.

    3. Az Azure-bővítményekkel MediaServices, keressen Online oldalon válassza ki a **Azure Media Services .NET SDK-bővítmények** (**windowsazure.mediaservices.extensions**), majd kattintson a  **Telepítés** gombra.
   
    4. A projekt módosul, és a Media Services .NET SDK-bővítmények, a Media Services .NET SDK-t és a többi függő szerelvényeket kerülnek.
4. A tisztító környezet előléptetéséhez, fontolja meg a NuGet-csomagok visszaállításának engedélyezése. További információkért lásd: [NuGet-csomagok visszaállításának "](http://docs.nuget.org/consume/package-restore).
5. Adjon hozzá egy hivatkozást, **System.Configuration** szerelvény. Ez a szerelvény tartalmazza a System.Configuration. **ConfigurationManager** osztály, amely a konfigurációs fájlok (például App.config) elérésére szolgál.
   
    1. A hivatkozások kezelése párbeszédpanelen hivatkozások hozzáadásához kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben. Kattintson a **Hozzáadás**, majd kattintson a **hivatkozás...** .
   
    2. A hivatkozások kezelése párbeszédpanel jelenik meg.
    3. A .NET-keretrendszer szerelvényei közé, keresse meg és jelölje ki a System.Configuration szerelvényre, és nyomja le az **OK**.
6. Nyissa meg az App.config fájlt, és adja hozzá egy **appSettings** a fájl a szakaszban található. Állítsa be az értékeket, amelyek szükségesek ahhoz, hogy a Media Services API csatlakozni. További információkért lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

    Állítsa be az értékeket, amelyek szükségesek ahhoz, hogy használatával csatlakozzon a **egyszerű** hitelesítési módszert.

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. Adja hozzá a **System.Configuration** hivatkozás a projekthez.
8. Írja felül a meglévő **használatával** elején utasításokat a Program.cs fájl a következő kóddal:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    Ekkor készen áll a Media Services-alkalmazás elkezdje.    

## <a name="example"></a>Példa

Íme egy kis példa, az AMS API-hoz csatlakoztatja, és megjeleníti az összes elérhető Media processzor.

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

## <a name="next-steps"></a>További lépések

Most [csatlakozhat az AMS API](media-services-use-aad-auth-to-access-ams-api.md) máris [fejlesztése](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

