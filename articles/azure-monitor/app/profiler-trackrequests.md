---
title: Az Azure Application Insights kérelmek kód írása |} A Microsoft Docs
description: Az Application Insights kérelmek nyomon követése, így a reqeusts profilok is igénybe kód írása
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 20f408d9dd32c3fd7a0e319e4051483e3aa54dd9
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082925"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Az Application Insights kérelmek kód írása

A Teljesítmény lapon látható az alkalmazás profilokat, az Application Insights kell lennie nyomon követése a kérelmeket az alkalmazás. Az Application Insights automatikusan nyomon követheti, hogy a rendszer már kiépített, mint például az ASP.net és az ASP.Net Core keretrendszereket beépített alkalmazások kéréseit. De a más alkalmazások, például az Azure-Felhőszolgáltatás munkavégző szerepköreit és a Service Fabric állapotmentes API-k, az kell, hogy az Application Insights, ahol a kérelmek kezdődik és ér véget kódot írni. Ez a kód többször kérelmek telemetriai adatokat küld az Application Insights és a teljesítmény oldalon láthatja a telemetriai adatokat és profilokat összegyűjtött ezeket a kérelmeket a korábban írt. 

Önnek kell tennie a manuálisan a kérések nyomon követésére lépései a következők:


  1. Az alkalmazás élettartamának korai szakaszában adja meg a következő kódot:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      A globális kialakítási kulcs konfigurálásával kapcsolatban további információkért lásd: [az Application Insights használata a Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. A bármely alakítsa ki, a hozzáadni kívánt kódrészleteket egy `StartOperation<RequestTelemetry>` **USING** utasítás körülötte, az alábbi példában látható módon:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Hívó `StartOperation<RequestTelemetry>` belül egy másik `StartOperation<RequestTelemetry>` hatókör nem támogatott. Használhat `StartOperation<DependencyTelemetry>` beágyazott hatókör helyette. Példa:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
