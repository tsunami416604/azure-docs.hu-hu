---
title: Számítógép beállítása a Médiaszolgáltatások fejlesztéséhez a .NET segítségével
description: Ismerje meg a Media Services SDK for .NET szolgáltatás ának előfeltételeit. Azt is megtudhatja, hogyan hozhat létre Visual Studio-alkalmazást.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 51fffbd170daecfec6fcea95caa0526e6d881407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724115"
---
# <a name="media-services-development-with-net"></a>Media Services fejlesztés a .NET segítségével 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Ez a cikk a Media Services-alkalmazások .NET használatával történő fejlesztésének megkezdését ismerteti.

Az **Azure Media Services .NET SDK-könyvtár** lehetővé teszi, hogy a .NET használatával programozzon a Media Services szolgáltatással. A .NET használatával való fejlesztés megkönnyítése érdekében az **Azure Media Services .NET SDK Extensions** könyvtár a biztosított. Ez a tár a .NET-kódot leegyszerűsítő bővítménymetódusok és segédfunkciók készletét tartalmazza. Mindkét könyvtár elérhető a **NuGet** és a **GitHub segítségével.**

## <a name="prerequisites"></a>Előfeltételek
* Egy Media Services-fiók egy új vagy meglévő Azure-előfizetésben. Lásd a [Media Services-fiók létrehozása című cikket.](media-services-portal-create-account.md)
* Operációs rendszerek: Windows 10, Windows 7, Windows 2008 R2 vagy Windows 8.
* . NET Framework 4.5 vagy újabb.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása
Ez a szakasz bemutatja, hogyan hozhat létre projektet a Visual Studióban, és hogyan állíthatja be a Media Services fejlesztéséhez.  Ebben az esetben a projekt egy C# Windows konzolalkalmazás, de az itt látható telepítési lépések más típusú projektekre is vonatkoznak, amelyeket Media Services-alkalmazásokhoz hozhat létre (például egy Windows Forms vagy egy ASP.NET webalkalmazáshoz).

Ez a szakasz bemutatja, hogyan vehet fel Media Services .NET SDK-bővítményeket és más függő könyvtárakat a **NuGet** segítségével.

Másik lehetőségként beszerezheti a legújabb Media Services .NET SDK biteket a GitHubról ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) vagy [github.com/Azure/azure-sdk-for-media-services-extensions),](https://github.com/Azure/azure-sdk-for-media-services-extensions)létrehozhatja a megoldást, és hozzáadhatja a hivatkozásokat az ügyfélprojekthez. Az összes szükséges függőség automatikusan letöltődik és kibontódik.

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást. Írja be a **Név**, **Hely**és **Megoldás nevet,** majd kattintson az OK gombra.
2. Hozza létre a megoldást.
3. A **NuGet** segítségével telepítheti és hozzáadhatja az **Azure Media Services .NET SDK-bővítményeket** (**windowsazure.mediaservices.extensions**). Ennek a csomagnak a telepítése a **Media Services .NET SDK**csomagot és az összes további szükséges függőséget is feltelepíti
   
    Győződjön meg arról, hogy a NuGet legújabb verziója van telepítve. További információ és telepítési útmutató: [NuGet](https://nuget.codeplex.com/).

    1. A Megoldáskezelőben kattintson a jobb gombbal a projekt nevére, és válassza a **NuGet-csomagok kezelése parancsot.**

    2. Megjelenik a Manage NuGet Packages (NuGet-csomagok kezelése) párbeszédpanel.

    3. Az Online gyűjteményben keresse meg az Azure MediaServices-bővítményeket, válassza az **Azure Media Services .NET SDK Extensions** (**windowsazure.mediaservices.extensions**) lehetőséget, majd kattintson a **Telepítés** gombra.
   
    4. A projekt módosul, és a Media Services .NET SDK extensions, a Media Services .NET SDK és más függő szerelvények hivatkozásai kerülnek hozzáadásra.
4. A tisztább fejlesztői környezet előmozdítása érdekében engedélyezd a NuGet csomag-visszaállítást. További információ: [NuGet csomagvisszaállítás"](https://docs.nuget.org/consume/package-restore).
5. Hivatkozás hozzáadása a **System.Configuration** szerelvényhez. Ez a szerelvény a System.Configuration elemet tartalmazza. **ConfigurationManager** osztály, amely a konfigurációs fájlok (például Az App.config) elérésére szolgál.
   
    1. Ha hivatkozásokat szeretne hozzáadni a Hivatkozások kezelése párbeszédpanelen, kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben. Ezután kattintson a **Hozzáadás**gombra, majd a **Hivatkozás... parancsra.**
   
    2. Megjelenik a Hivatkozások kezelése párbeszédpanel.
    3. A .NET keretszerelvények csoportban keresse meg és jelölje ki a System.Configuration szerelvényt, és nyomja **le az OK gombot.**
6. Nyissa meg az App.config fájlt, és adjon hozzá egy **appBeállítások** szakaszt a fájlhoz. Állítsa be a Media Services API-hoz való csatlakozáshoz szükséges értékeket. További információ: [Access the Azure Media Services API with Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md). 

    Állítsa be az okat az értékeket, amelyek a **service egyszerű** hitelesítési módszerrel való csatlakozáshoz szükségesek.

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

7. Adja hozzá a **System.Configuration** hivatkozást a projekthez.
8. A **meglévők felülírása** a Program.cs fájl elején lévő utasítások használatával a következő kóddal:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    Ezen a ponton készen áll a Media Services-alkalmazások fejlesztésének megkezdésére.    

## <a name="example"></a>Példa

Íme egy kis példa, amely csatlakozik az AMS API-hoz, és felsorolja az összes rendelkezésre álló médiaprocesszort.

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

Most [már csatlakozhat az AMS API-hoz,](media-services-use-aad-auth-to-access-ams-api.md) és [elkezdheti a fejlesztést.](media-services-dotnet-get-started.md)


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

