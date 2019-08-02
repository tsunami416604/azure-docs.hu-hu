---
title: OpenCensus go-nyomkövetés az Azure Application Insightskal | Microsoft Docs
description: Útmutatást nyújt a OpenCensus go-nyomkövetés és a helyi továbbító integrálásához, Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 56e66f17e9ce1d2482463f619e82dfd29d48f191
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990304"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Elosztott Nyomkövetések gyűjtése a go-ból (előzetes verzió)

A Application Insights mostantól támogatja a go-alkalmazások elosztott nyomkövetését a [OpenCensus](https://opencensus.io) és az új [helyi továbbítóval](./opencensus-local-forwarder.md)való integráción keresztül. Ez a cikk lépésről lépésre bemutatja, hogyan állíthatja be a OpenCensus for Go-t, és hogyan Application Insights a nyomkövetési adatait.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell Azure-előfizetéssel.
- A Go-t telepíteni kell, ez a cikk a 1,11 [Go Download](https://golang.org/dl/)verziót használja.
- Kövesse az utasításokat a [helyi továbbító Windows](./opencensus-local-forwarder.md)-szolgáltatásként való telepítéséhez.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Application Insights erőforrás létrehozása

Először létre kell hoznia egy Application Insights-erőforrást, amely a kialakítási kulcsot (rendszerállapotkulcsot) fogja létrehozni. A rendszerállapotkulcsot ezt követően úgy konfigurálja a helyi továbbítót, hogy elküldje az elosztott nyomkövetéseket a OpenCensus-eszközön lévő alkalmazásból Application Insights.   

1. Válassza **az erőforrás** > létrehozása**fejlesztői eszközök** > **Application Insights**lehetőséget.

   ![Application Insights-erőforrások hozzáadása](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >Ha első alkalommal hoz létre Application Insights-erőforrást, további információt az [Application Insights-erőforrás létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) című cikkben talál.

   Megjelenik egy konfigurációs mező. Az adatbeviteli mezők kitöltéséhez használja az alábbi táblát.

    | Beállítások        | Value           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az App Insights-adatokat futtató új erőforráscsoport neve |
   | **Location** | East US | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

2. Kattintson a **Create** (Létrehozás) gombra.

## <a name="configure-local-forwarder"></a>Helyi továbbító konfigurálása

1. Válassza az **Áttekintés** > **Alapvető erőforrások** elemet, és másolja ki az alkalmazás **Kialakítási kulcsát**.

   ![A kialakítási kulcs képernyőképe](./media/opencensus-Go/0003-instrumentation-key.png)

2. Szerkessze `LocalForwarder.config` a fájlt, és adja hozzá a kialakítási kulcsot. Ha követte az előfeltételként [](./opencensus-local-forwarder.md) szereplő utasításokat, a fájl a következő helyen található:`C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Indítsa újra az alkalmazás **helyi továbbító** szolgáltatását.

## <a name="opencensus-go-packages"></a>OpenCensus go-csomagok

1. Telepítse a Go-hoz készült Open Census csomagokat a parancssorból:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Adja hozzá a következő kódot egy. go-fájlhoz, majd hozza létre és futtassa a parancsot. (Ez a példa a hivatalos OpenCensus útmutatóból származik, amely a helyi továbbítóval való integrációt megkönnyítő hozzáadott kóddal rendelkezik)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Ha az egyszerű Go-alkalmazás fut, keresse `http://localhost:50030`meg a következőt:. A böngésző minden frissítése a "Hello World" szöveget hozza létre a helyi továbbító által kiválasztott megfelelő span-adataival együtt.

4. Annak ellenőrzéséhez, hogy a **helyi továbbító** felvette-e a nyomkövetést `LocalForwarder.config` , ellenőrizze a fájlt. Ha követte az előfeltételekben [](https://docs.microsoft.com/azure/application-insights/local-forwarder)leírt lépéseket, akkor a következő helyen `C:\LF-WindowsServiceHost`található:.

    A naplófájl alábbi ábráján láthatja, hogy a második parancsfájl futtatása előtt, ahol az exportőrt `OpenCensus input BatchesReceived` hozzáadták, 0. A frissített szkript `BatchesReceived` futtatásának megkezdése után a megadott értékek számával megnövelve:
    
    ![Új App Insights-erőforrás űrlap](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Most újra megnyithatja a Azure Portal Application Insights **Áttekintés** lapját, ahol megtekintheti az aktuálisan futó alkalmazás részleteit. Válassza az **élő metrika stream**lehetőséget.

   ![Képernyőkép az áttekintő panelről a Vörös mezőben kiválasztott élő metrikai streamtel](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Ha újra futtatja a második go alkalmazást `http://localhost:50030`, és megkezdi a böngésző frissítését, akkor az élő nyomkövetési adatok megjelennek a helyi továbbító szolgáltatásban Application Insights.

   ![Képernyőfelvétel az élő metrika streamről a megjelenített teljesítményadatokat tartalmazó adatokkal](./media/opencensus-go/0006-stream.png)

3. Térjen vissza az **Áttekintés** lapra, és válassza ki az **alkalmazás** -hozzárendelést a függőségi kapcsolatok vizualizációs elrendezéséhez, és hívja meg az időzítést az alkalmazás-összetevők között.

    ![Az alapszintű alkalmazás-hozzárendelés képernyőképe](./media/opencensus-go/0007-application-map.png)

    Mivel csak egyetlen metódust hívunk meg, az alkalmazásunk térképe nem annyira érdekes. Az alkalmazás-hozzárendelés azonban a sokkal több elosztott alkalmazás megjelenítésére is méretezhető:

   ![Alkalmazástérkép](media/opencensus-go/application-map.png)

4. A teljesítmény **vizsgálata** elemre kattintva részletes teljesítmény-elemzést végezhet, és meghatározhatja a lassú teljesítmény okát.

    ![A teljesítmény panel képernyőképe](./media/opencensus-go/0008-performance.png)

5. A **minták** kiválasztásával, majd a jobb oldali panelen megjelenő minták bármelyikére kattintva elindíthatja a végpontok közötti tranzakció részleteinek élményét. Habár a minta alkalmazásunk egyetlen eseményt mutat be, egy összetettebb alkalmazás lehetővé teszi, hogy a végpontok közötti tranzakciót egy adott esemény hívási verem szintjére tárja fel.

     ![Képernyőkép a végpontok közötti tranzakciós kezelőfelületről](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Ugráshoz tartozó OpenCensus-nyomkövetés

Csak a helyi továbbító és a Application Insights OpenCensus integrálásának alapjait tárgyaljuk. A [hivatalos OpenCensus go használati útmutatója](https://godoc.org/go.opencensus.io) részletesebb témákat is magában foglalhat.

## <a name="next-steps"></a>További lépések

* [Alkalmazás-hozzárendelés](./../../azure-monitor/app/app-map.md)
* [Végpontok közötti teljesítmény figyelése](./../../azure-monitor/learn/tutorial-performance.md)
