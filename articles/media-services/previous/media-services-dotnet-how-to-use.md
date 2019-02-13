---
title: Számítógép beállítása a .NET-keretrendszerrel történő Media Services-fejlesztés
description: Ismerje meg, a Media Services SDK használatával a .NET-keretrendszerhez készült Media Services előfeltételeit. Is megtudhatja, hogyan hozhat létre egy Visual Studio alkalmazást.
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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 36350f8f359c6ad5cc5637ce0ce38838698cd68b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177444"
---
# <a name="media-services-development-with-net"></a>.NET-keretrendszerrel történő Media Services-fejlesztés 
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

Ez a cikk leírja, hogyan elindításához a .NET használatával a Media Services-alkalmazások fejlesztéséhez használható.

A **Azure Media Services .NET SDK** kódtára lehetővé teszi, hogy a Media Services .NET-tel programozni. Az, hogy azokat még egyszerűbben fejleszthet a .NET-tel, a **Azure Media Services .NET SDK-bővítmények** kódtár biztosítja. Ez a kódtár tartalmaz kiegészítő módszerek és segédfüggvények találhatók, amelyek egyszerűbbé teszik a .NET-kódolást. Mindkét kódtárak érhetők el **NuGet** és **GitHub**.

## <a name="prerequisites"></a>Előfeltételek
* Egy Media Services-fiók egy új vagy meglévő Azure-előfizetésben. Tekintse meg a cikket [Media Services-fiók létrehozása](media-services-portal-create-account.md).
* Operációs rendszerek: A Windows 10, Windows 7, Windows 2008 R2 vagy Windows 8-ban.
* .NET-keretrendszer 4.5-ös vagy újabb.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása
Ez a szakasz bemutatja, hogyan állíthatja be a Media Services-fejlesztést, és hozzon létre egy projektet a Visual Studióban.  Ebben az esetben a projekt van egy C# Windows-konzolalkalmazást, de a telepítő lépéseket itt látható a alkalmazni a többi projekteket hozhat létre Media Services-alkalmazások (például egy Windows Forms-alkalmazást vagy egy ASP.NET-webalkalmazás).

Ez a szakasz bemutatja, hogyan **NuGet** hozzáadása a Media Services .NET SDK-bővítmények és a függő könyvtárak.

Azt is megteheti, beszerezheti a legfrissebb Media Services .NET SDK a bits a Githubról ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) vagy [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), állítsa össze a megoldást, és adja hozzá az ügyfélprojekt a hivatkozásokat. Az összes szükséges függőséget letöltött és kibontott automatikusan.

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást. Adja meg a **neve**, **hely**, és **megoldásnevet**, majd kattintson az OK gombra.
2. Hozza létre a megoldást.
3. Használat **NuGet** telepítéséhez, és adja hozzá **Azure Media Services .NET SDK-bővítmények** (**windowsazure.mediaservices.extensions**). Ennek a csomagnak a telepítése a **Media Services .NET SDK**csomagot és az összes további szükséges függőséget is feltelepíti
   
    Győződjön meg arról, hogy van-e telepítve a legújabb verzió. További információk és a telepítési utasításokért lásd: [NuGet](http://nuget.codeplex.com/).

    1. A Megoldáskezelőben kattintson a jobb gombbal a projekt nevét, és válassza a **NuGet-csomagok kezelése**.

    2. A NuGet-csomagok kezelése párbeszédpanel jelenik meg.

    3. Válassza ki az Online katalógusban, keresse meg az Azure MediaServices bővítményeket, **Azure Media Services .NET SDK-bővítmények** (**windowsazure.mediaservices.extensions**), majd kattintson a  **Telepítés** gombra.
   
    4. A projekt módosul, és hozzáadja a Media Services .NET SDK-bővítményeket, a Media Services .NET SDK-t és más függő szerelvényei mutató hivatkozásokat.
4. A tisztább fejlesztési környezet előléptetni, fontolja meg a NuGet-csomagok visszaállításának engedélyezése. További információkért lásd: [NuGet-csomagok visszaállításának "](http://docs.nuget.org/consume/package-restore).
5. Vegyen fel egy hivatkozást **System.Configuration** sestavení. Ez a szerelvény tartalmazza a System.Configuration. **ConfigurationManager** osztály, amely a konfigurációs fájlok (például App.config) eléréséhez használatos.
   
    1. A hivatkozások kezelése párbeszédpanelen hivatkozások hozzáadásához kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben. Kattintson a **Hozzáadás**, majd kattintson az **hivatkozás...** .
   
    2. A hivatkozások kezelése párbeszédpanel jelenik meg.
    3. A .NET-keretrendszer szerelvényeket, keresse meg és válassza ki a System.Configuration szerelvényre, és nyomja meg **OK**.
6. Nyissa meg az App.config fájlt, és adjon hozzá egy **appSettings** szakasz a fájlt. Állítsa az értékeket, melyek szükségesek ahhoz, hogy a Media Services API-t kapcsolódás. További információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

    Állítsa az értékeket, melyek szükségesek ahhoz, hogy érdemesebb a **szolgáltatásnév** hitelesítési módszert.

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

7. Adja hozzá a **System.Configuration** referencia a projekthez.
8. Írja felül a meglévő **használatával** elején található utasításokat a Program.cs fájl a következő kóddal:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    Ezen a ponton készen áll egy Media Services-alkalmazás fejlesztése elindításához.    

## <a name="example"></a>Példa

Íme egy, az AMS API-hoz csatlakozik, és megjeleníti az összes rendelkezésre álló Médiafeldolgozók kis példa.

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

Most már [az AMS API-t kapcsolódás](media-services-use-aad-auth-to-access-ams-api.md) és start [fejlesztésének](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

