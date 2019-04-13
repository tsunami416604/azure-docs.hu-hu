---
title: Ciklikus lekérdezés hosszú ideig futó műveletek |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hosszú ideig futó műveletek lekérdezéséhez.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 752c502268ef53d3c0575d92e75ce6a965fccd9f
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59520816"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Az Azure Media Services élő Stream továbbítása

## <a name="overview"></a>Áttekintés

A Microsoft Azure Media Services kínál kérést küld a műveletek indítása a Media Services API-k (például: létrehozása, indítása, leállítása, vagy törli a csatornát). Ezek a műveletek olyan hosszan futó.

A Media Services .NET SDK biztosítja a kérelem elküldéséhez, és várjon, amíg a művelet végrehajtásához API-k (belsőleg, az API-k vannak lekérdezési művelet folyamatban van a bizonyos időközönként). Ha például meghívásakor csatorna. Start(), a metódus a csatorna indítása után adja vissza. Az aszinkron verziója is használhatja: await csatorna. StartAsync() (feladatalapú aszinkron minta kapcsolatos információkért lásd: [KOPPINTSON](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). Egy művelet kérelem küldése, és majd lekérdezi a állapotát, amíg a művelet be nem fejeződik API-k "lekérdezési módszerek" nevezzük. Ezek a metódusok (különösen az aszinkron verzió) használata akkor javasolt gazdagügyfél-alkalmazásokkal és/vagy állapotalapú szolgáltatások.

Vannak helyzetek, ahol egy alkalmazás egy hosszú ideig futó http-kérelem nem várja meg, és kérdezzen le a műveleti folyamatot manuálisan szeretné. Például lehet egy böngészőben, egy állapot nélküli webes szolgáltatással való interakcióhoz: amikor a böngésző csatornát létrehozni, a webszolgáltatás kezdeményezi egy hosszú ideig futó művelet, és adja vissza a művelet azonosítója a böngészőben. A böngésző majd kérheti a webszolgáltatást a azonosító alapján műveleti állapotának beolvasása A Media Services .NET SDK API-k, melyek ebben a forgatókönyvben biztosít. Ezen API-k "nem lekérdezési módszerek" nevezzük.
A "nem lekérdezési módszerek" rendelkezik a következő elnevezési mintának: Küldés*OperationName*műveletet (például SendCreateOperation). Küldés*OperationName*műveleti metódusoknál adja vissza a **IOperation** objektum; a visszaadott objektum tartalmaz információkat, amelyek segítségével nyomon követheti a műveletet. A Küldés*OperationName*OperationAsync módszerek is szolgálnak **feladat\<IOperation >**.

A következő osztályok jelenleg nem lekérdezési módszereket támogatja:  **Csatorna**, **Streamvégpontok**, és **Program**.

A műveleti állapotának lekérdezéséhez, használja a **GetOperation** metódust a **OperationBaseCollection** osztály. A következő időszakok használata a művelet állapotának: a **csatorna** és **Streamvégpontok** műveletei, pedig 30 másodperc; **Program** műveletei, pedig 10 másodperc.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint.

## <a name="example"></a>Példa

Az alábbi példa meghatározza nevű osztály **ChannelOperations**. Ez az osztály definícióját a web service osztálydefiníciót a kiindulási pont lehet. Az egyszerűség kedvéért az alábbi példák a módszerek nem aszinkron verzióját használja.

A példa azt is bemutatja, hogyan használja a az ügyfél lehet, hogy ez az osztály.

### <a name="channeloperations-class-definition"></a>Az osztálykiterjesztések definíciója ChannelOperations

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
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

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>Az Ügyfélkód

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

