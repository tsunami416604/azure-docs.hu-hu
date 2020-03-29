---
title: Hosszú távú műveletek lekérdezése | Microsoft dokumentumok
description: Az Azure Media Services olyan API-kat kínál, amelyek a műveletek indításához (például egy csatorna létrehozásához, indításához, leállításához vagy törléséhez) kérelmeket küldenek a Media Services-nek, ezek a műveletek hosszú ideig futnak. Ez a témakör bemutatja, hogyan lehet lehallgatni a hosszú ideig futó műveleteket.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 43d9a6adc935010eab6e5e52d73f2019c8afcf5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887158"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Élő közvetítés kézbesítése az Azure Media Services szolgáltatással

## <a name="overview"></a>Áttekintés

A Microsoft Azure Media Services olyan API-kat kínál, amelyek kéréseket küldenek a Media Services-nek műveletek indítására (például csatorna létrehozása, indítása, leállítása vagy törlése). Ezek a műveletek már régóta futnak.

A Media Services .NET SDK API-kat biztosít, amelyek elküldik a kérelmet, és megvárják a művelet befejezését (belsőleg az API-k bizonyos időközönként lekérdezést végeznek a művelet előrehaladásáról). Például, ha hívja csatorna. Start(), a metódus a csatorna indítása után tér vissza. Használhatja az aszinkron verziót is: a(z) a channel. StartAsync() (A feladatalapú aszinkron mintázatról a TAP című témakörben talál [információt.](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx) Azok az API-k, amelyek műveletkérelmet küldenek, majd lekérdezik az állapotot a művelet befejezéséig, "lekérdezési módszereknek" nevezzük. Ezek a módszerek (különösen az Async verzió) gazdag ügyfélalkalmazásokhoz és/vagy állapotalapú szolgáltatásokhoz ajánlottak.

Vannak olyan esetek, amikor egy alkalmazás nem tud várni egy hosszú ideig futó http-kérelmet, és azt szeretné, hogy a lekérdezés a művelet előrehaladását manuálisan. Egy tipikus példa egy állapotmentes webszolgáltatással kommunikáló böngésző: amikor a böngésző csatornát kér, a webszolgáltatás hosszú ideig futó műveletet kezdeményez, és visszaadja a műveletazonosítót a böngészőnek. A böngésző ezután kérheti a webszolgáltatás, hogy a művelet állapotát az azonosító alapján. A Media Services .NET SDK az ebben az esetben hasznos API-kat biztosít. Ezeket az API-kat "nem lekérdezési módszereknek" nevezzük.
A "nem lekérdezési módszerek" a következő elnevezési minta: Send*OperationName*operation (például SendCreateOperation). A Send*OperationName*műveletmetódusok az **IOperation** objektumot adják vissza; a visszaadott objektum olyan információkat tartalmaz, amelyek a művelet nyomon követésére használhatók. A Send*OperationName*OperationAsync metódusok visszaadják **az\<IOperation>.Task IOperation>**.

Jelenleg a következő osztályok támogatják a nem lekérdezési módszereket: **Csatorna**, **StreamingEndpoint**és **Program**.

A művelet állapotának lekérdezéséhez használja a **GetOperation** metódust az **OperationBaseCollection** osztályon. A művelet állapotának ellenőrzéséhez használja a következő időközöket: **Csatorna-** és **StreamingVégpont-műveletek** esetén 30 másodpercet használjon; **A Program** műveletekhez 10 másodpercet használjon.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztői környezetet, és népesítse be az app.config fájlt a kapcsolatadataival, ahogy azt a Media Services fejlesztése a [.NET fájlban leírta.](media-services-dotnet-how-to-use.md)

## <a name="example"></a>Példa

A következő példa a **ChannelOperations**nevű osztályt határozza meg. Ez az osztálydefiníció lehet a webszolgáltatásosztály-definíció kiindulópontja. Az egyszerűség kedvéért az alábbi példák a módszerek nem aszinkron verzióit használják.

A példa azt is bemutatja, hogy az ügyfél hogyan használhatja ezt az osztályt.

### <a name="channeloperations-class-definition"></a>ChannelOperations osztálydefiníció

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

### <a name="the-client-code"></a>Az ügyfélkód

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

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

