---
title: Azure Mobile Engagement Service API-k elérésére .NET SDK használatával
description: A Mobile Engagement .NET SDK használata az Azure Mobile Engagement Service API-k elérésére
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4a303db638b8ea304db5cba4075b0b102eedd0f3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Azure Mobile Engagement Service API-k elérésére .NET SDK használatával
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 

Az Azure Mobile Engagement tesz elérhetővé, kezelheti az eszközöket, az API-k Reach/leküldéses kampányokra stb. Ezen API-k kommunikál, azt is adja meg, egy [Swagger-fájl](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) használható az eszközök SDK-k létrehozására az a kívánt nyelvet. Azt javasoljuk, a [AutoRest](https://github.com/Azure/AutoRest) eszköz használatával hozzon létre az SDK a Swagger-fájl.

> [!NOTE]
> Az Azure Mobile Engagement szolgáltatást 2018 márciusától megszüntetjük, és jelenleg csak meglévő ügyfelek számára érhető el. További információkért lásd: [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

A .NET SDK létrehoztunk egy hasonló módon, amely teszi lehetővé ezen API-k együttműködhet egy C# burkoló használatával, és nem kell a hitelesítési jogkivonat egyeztetést és frissítse magát.  

Ez a minta végighalad lépést kell végrehajtani a .NET SDK használatához készlete:

1. Első lépésként kell beállítani az API-jainak az Azure Active Directory használatával, lásd a hitelesítési [Itt](mobile-engagement-api-authentication.md#authentication). Ezeket a lépéseket végén rendelkeznie kell egy érvényes **SubscriptionId**, **TenantId**, **ApplicationId** és **titkos**. 
2. Egy egyszerű Windows-Konzolalkalmazás használata a .NET SDK-val egy bejelentési kampány létrehozásához forgatókönyv bemutatásához használjuk. Ezért nyissa meg a Visual Studio, és hozzon létre egy **Konzolalkalmazás**.   
3. Ezután le kell töltenie a .NET SDK érhető el, amely **Microsoft Azure Engagement könyvtár** a Nuget-dokumentumtárban [Itt](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
   Ha a Visual Studio telepíti a Nuget, győződjön meg arról, hogy rendelkezik-e megjelölve ellenőrzése kell a **Include prerelease** lehetőséget a csomag keresése során:
   
    ![][1]
4. Az a `Program.cs` fájlt, a következő névterek:
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. Ezután definiálni kell a következő állandókat, amelyek a hitelesítés és a Mobile Engagement-alkalmazáshoz, amely a közlemény kampány létrehozásához való interakció használjuk:
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Adja meg a `EngagementManagementClient` változót, amely a Mobile Engagement SDK metódusok hívására használjuk:
   
        static EngagementManagementClient engagementClient; 
7. Adja hozzá a következőt a `Main` módszert:
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. Adja meg a következő metódust, amely gondoskodik inicializálása a `EngagementManagementClient` először hitelesíti, és majd magát a Mobile Engagement-alkalmazáshoz, amelyben a közlemény kampány létrehozásához tervezi társítása:
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > Vegye figyelembe, hogy kell használni a **erőforrás alkalmazásnév** az az Azure felügyeleti portálján a AppName paraméter. 
   > 
   > 
9. Végül adja meg a CreateCampaign metódust, amely kezeli a korábban már inicializált EngagementClient használatával hozhat létre egy egyszerű **bármikor** & **csak értesítési** kampány cím és üzenet: 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. Futtassa a konzolalkalmazást, és a következő jelenik meg a kampány sikeres létrehozása:
    
    **"159" Kampányazonosítóval sikeresen létrejött, és a "vázlat" állapotban van**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
