---
title: "A hosszú ideig futó műveletek lekérdezési |} Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan, és kérdezze le a hosszú futású műveleteket."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 7123a2d44d3b7c332afe30fb0fcea88ca29e313a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Az Azure Media Services élő adatfolyam továbbítása

## <a name="overview"></a>Áttekintés

A Microsoft Azure Media Services kínál API-kérelmeket küldjön a Media Services operations indításához (például: hozzon létre, elindítása, leállítása vagy csatorna törlése). Ezek a műveletek olyan hosszan futó.

A Media Services .NET SDK API-t elküldeni a kérelmet, és várjon, amíg a művelet elvégzéséhez biztosít (belsőleg, az API-k vannak ciklikus lekérdezés a művelet folyamatban van bizonyos időközönként). Például, ha meghívja a csatorna. Start(), a metódus visszaadja a csatorna indítása után. Is használhatja az aszinkron: await csatorna. StartAsync() (feladatalapú aszinkron mintát kapcsolatos információkért lásd: [KOPPINTSON](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). API-k művelet kérelmet küldött, és majd kérdezze le az állapot addig, amíg a művelet be nem fejeződött "lekérdezési módszerek" nevezzük. Ezek a módszerek (különösen a aszinkron verzió) gazdagügyfél-alkalmazásokkal és/vagy állapotalapú szolgáltatások használata ajánlott.

Vannak esetek, amikor egy alkalmazás nem várja meg a hosszú ideig futó HTTP-kérelmek, így szeretné manuálisan kérdezze le az a művelet folyamatban van. Egy jellemző példa erre egy böngészőt, egy állapot nélküli webszolgáltatás való interakció: amikor a böngésző csatornát létrehozni, a webszolgáltatás indít el egy hosszú ideig futó művelet, és a böngésző a művelet-Azonosítót adja vissza. A böngésző sikerült majd kérje meg a webszolgáltatás számára, hogy az az azonosítója alapján műveleti állapotának beolvasása A Media Services .NET SDK API-k, melyek hasznosak lehetnek a forgatókönyv biztosítja. Ezen API-k "nem lekérdezési módszerek" nevezzük.
A "nem lekérdezési módszerek" rendelkezik a következő elnevezési: küldése*OperationName*műveletet (például SendCreateOperation). Küldési*OperationName*művelet módszerek a **IOperation** objektum; a visszaadott objektumot információkat tartalmaznak, amelyek segítségével nyomon követheti a művelet. A Küldés*OperationName*OperationAsync módszerek **feladat<IOperation>**.

Jelenleg a következő osztályok támogatja-e a nem lekérdezési módszerek: **csatorna**, **StreamingEndpoint**, és **Program**.

Kérdezze le a műveletállapot, használja a **GetOperation** metódust a **OperationBaseCollection** osztály. A művelet állapotának ellenőrzéséhez a következő időszakok használja: a **csatorna** és **StreamingEndpoint** műveletei, pedig 30 másodperc; **Program** műveletei, pedig 10 másodperc.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint.

## <a name="example"></a>Példa

Az alábbi példa meghatározza nevű osztály **ChannelOperations**. Ez az osztály definícióját a webszolgáltatás-osztály definíciójának kiindulási pontot lehet. Az egyszerűség kedvéért az alábbi példák a módszerek nem aszinkron verzióját használja.

A példa azt is bemutatja, hogyan használhatja az ügyfél a Ez az osztály.

### <a name="channeloperations-class-definition"></a>ChannelOperations osztálydefiníció

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
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        public ChannelOperations()
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
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

### <a name="the-client-code"></a>Az Ügyfélkód
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



## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

