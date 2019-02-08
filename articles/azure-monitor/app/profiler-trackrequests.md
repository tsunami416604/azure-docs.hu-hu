---
title: Az Azure Application Insights kérelmek kód írása |} A Microsoft Docs
description: Írja be a kódot, így profilok számára a kérelmek nyomon követéséhez a kérelmeket az Application insights segítségével.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 4782e560b580b7f565724dbb35ed9876bffdc256
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882063"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Az Application Insights kérelmek kód írása

A teljesítmény oldalon profilok az alkalmazás megtekintéséhez az Azure Application Insights nyomon kell követni az alkalmazásra vonatkozó kérések. Az Application Insights automatikusan követheti a beépített keretrendszereket már kiépített alkalmazások kéréseit. Két példa olyan ASP.NET és az ASP.NET Core. 

Más alkalmazások, például az Azure Cloud Services feldolgozói szerepkörei és a Service Fabric állapotmentes API-k mondja el, ahol a kérelmek megkezdése az Application Insights és a záró kód írása kell. Miután ezt a kódot, kérelmek telemetriai adatokat küld az Application Insights. A Teljesítmény lapon megtekintheti a telemetriát, és a profilokat összegyűjtött ezeket a kérelmeket. 

A manuális kérések nyomon követésére, tegye a következőket:

  1. Az alkalmazás élettartamának korai szakaszában adja meg a következő kódot:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      A globális kialakítási kulcs konfigurálásával kapcsolatban további információkért lásd: [az Application Insights használata a Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. A bármely alakítsa ki, a hozzáadni kívánt kódrészleteket egy `StartOperation<RequestTelemetry>` **használatával** utasítás körülötte, az alábbi példában látható módon:

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
