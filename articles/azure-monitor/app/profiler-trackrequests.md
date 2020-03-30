---
title: Kód írása a kérelmek nyomon követéséhez az Azure Application Insights segítségével | Microsoft dokumentumok
description: Kódot írhat a kérelmek nyomon követéséhez az Application Insights segítségével, így profilokat kaphat a kérésekhez.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c59cbe852a91a91c7b3adb4452328700ec718a82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671596"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Kód írása a kérelmek nyomon követéséhez az Application Insights segítségével

Az alkalmazás profilainak megtekintéséhez a Teljesítmény lapon az Azure Application Insights nak nyomon kell követnie az alkalmazásra vonatkozó kérelmeket. Az Application Insights automatikusan nyomon követheti a már műszeres keretrendszerekre épülő alkalmazások kérelmeit. Két példa a ASP.NET és ASP.NET Core. 

Más alkalmazások, például az Azure Cloud Services munkavégző szerepkörök és a Service Fabric állapotmentes API-k, kódot kell írnia, hogy megmondja application insights, ahol a kérelmek kezdődnek és végződnek. Miután megírta ezt a kódot, a kérelmek telemetriáját elküldi az Application Insights. Megtekintheti a telemetriai adatokat a Teljesítmény lapon, és a rendszer profilokat gyűjt ezekhez a kérelmekhez. 

A kérelmek manuális nyomon követéséhez tegye a következőket:

  1. Az alkalmazás élettartamának korai szakaszában adja hozzá a következő kódot:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      A globális instrumentationkulcs-konfigurációról a [Service Fabric használata az Application Insights szolgáltatással](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)című témakörben talál további információt.  

  1. Az instrumentumozni kívánt kódhoz adjon `StartOperation<RequestTelemetry>` hozzá egy **használati** utasítást, ahogy az a következő példában látható:

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

        Más `StartOperation<RequestTelemetry>` `StartOperation<RequestTelemetry>` hatókörön belüli hívás nem támogatott. Ehelyett a `StartOperation<DependencyTelemetry>` beágyazott hatókörben is használható. Példa:  
        
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
