---
title: OpenCensus a nyissa meg az Azure Application Insights nyomkövetés |} A Microsoft Docs
description: Utasítások OpenCensus integrálható a helyi továbbító és az Application Insights lépjen nyomkövetés biztosít
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 935119f2efe40ee22d2d11005fa79b12c712b7c2
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104573"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Elosztott nyomkövetések gyűjtéséhez a Góról (előzetes verzió)

Az Application Insights most már támogatja az elosztott nyomkövetést a Go-alkalmazásokba való integrációval [OpenCensus](https://opencensus.io) és az új [helyi továbbító](./opencensus-local-forwarder.md). Ez a cikk részletesen a folyamatot a Góhoz készült OpenCensus beállítását, és a nyomkövetési adatok az Application Insightsba.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell Azure-előfizetéssel.
- Go-nek telepítve kell lennie, ebben a cikkben 1.11-es verzió [Go letöltése](https://golang.org/dl/).
- Kövesse az utasításokat követve telepítse a [helyi továbbító Windows szolgáltatásként](./opencensus-local-forwarder.md#windows-service).

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Először meg kell létrehoznia az Application Insights-erőforrást egy kialakítási kulcsot (rendszerállapotkulcsot) hoz létre, amely. A rendszerállapotkulcsot szolgál majd a helyi továbbító elosztott nyomkövetési elküldeni a kialakítva OpenCensus alkalmazását, az Application Insights konfigurálása.   

1. Válassza ki **erőforrás létrehozása** > **fejlesztői eszközök** > **Application Insights**.

   ![Application Insights-erőforrások hozzáadása](./media/opencensus-Go/0001-create-resource.png)

   Megjelenik egy konfigurációs mező. Az adatbeviteli mezők kitöltéséhez használja az alábbi táblát.

    | Beállítások        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name (Név)**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Alkalmazás típusa** | Általános kérdések | A figyelt alkalmazás típusa |
   | **Erőforráscsoport**     | myResourceGroup      | Az App Insights-adatokat futtató új erőforráscsoport neve |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

2. Kattintson a **Create** (Létrehozás) gombra.

## <a name="configure-local-forwarder"></a>Helyi-továbbító konfigurálása

1. Válassza az **Áttekintés** > **Alapvető erőforrások** elemet, és másolja ki az alkalmazás **Kialakítási kulcsát**.

   ![Képernyőkép az eszközkulcs](./media/opencensus-Go/0003-instrumentation-key.png)

2. Szerkessze a `LocalForwarder.config` fájlt, és adja hozzá a kialakítási kulcsot. Ha követte a lépéseket a következő témakör utasításait a [előfeltételeként](./opencensus-local-forwarder.md#windows-service) a fájl `C:\LF-WindowsServiceHost`

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

3. Az alkalmazás újraindítása **helyi továbbító** szolgáltatás.

## <a name="opencensus-go-packages"></a>OpenCensus Go-csomagot

1. A megnyitott népszámlálási csomagok telepítése a parancssorból a Góhoz készült:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Adja hozzá a következő kódot egy .go fájlhoz, majd létrehozása, és futtassa. (Ebben a példában a hivatalos OpenCensus útmutató új kóddal, amely megkönnyíti az integrációt a helyi továbbító származik)

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

3. Miután az egyszerű alkalmazás fut-e lépjen `http://localhost:50030`. A szöveg "hello world" kifejezéssel span megfelelő adatok bejegyzéseit, amelyek a helyi továbbító átveszi a böngésző minden egyes frissítés hoz létre.

4. Ellenőrizheti, hogy a **helyi továbbító** vesz fel a nyomkövetések ellenőrzés a `LocalForwarder.config` fájlt. Ha követte a lépéseket a [előfeltétel](https://docs.microsoft.com/azure/application-insights/local-forwarder#windows-service), kell elhelyezni a `C:\LF-WindowsServiceHost`.

    Az alábbi ábrán a naplófájl, láthatja, hogy hol hozzáadtunk egy exportáló a második szkript futtatása előtt `OpenCensus input BatchesReceived` 0. Hogy megkezdése a frissített szkript futtatása után `BatchesReceived` azt a megadott értékek száma egyenlő a növekménye:
    
    ![Új App Insights-erőforrás űrlap](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Most megnyithatja ismét az Application Insights **áttekintése** a jelenleg futó alkalmazás részleteinek megtekintése az Azure Portalon lapot. Válassza ki **élő Metric Stream**.

   ![Az élő metrikai streamet vörös téglalappal jelölve áttekintő panel képernyőképe](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Ha futtassa újra a második Go-alkalmazást, és indítsa el frissíteni a böngészőt a `http://localhost:50030`, látni fogja élő nyomkövetési adatok, a helyi továbbító szolgáltatásból beérkező az Application Insightsban.

   ![Képernyőkép a teljesítményadatok jelenik meg az élő metrikai streamet](./media/opencensus-go/0006-stream.png)

3. Lépjen vissza a **áttekintése** lapon, és válassza **Alkalmazástérkép** mutató vizuális elrendezés az alkalmazás-összetevők közötti hívások időzítését és a függőségi viszonyok.

    ![Képernyőkép az alapvető alkalmazástérkép](./media/opencensus-go/0007-application-map.png)

    Azt is csak nyomkövetés egy metódus hívása, mert az alkalmazás-hozzárendelés nem is érdekes. De alkalmazástérkép méretezhető sokkal könnyebben elosztott alkalmazás megjelenítése:

   ![Alkalmazástérkép](media/opencensus-go/application-map.png)

4. Válassza ki **teljesítményének vizsgálata** részletes teljesítményelemzését végez, és a lassú teljesítmény okának meghatározásához.

    ![Teljesítmény panel képernyőképe](./media/opencensus-go/0008-performance.png)

5. Kiválasztásával **minták** és bármely, a minták jelennek meg a jobb oldali ablaktáblán, majd kattintson a végpontok közötti tranzakció részletei élmény elindul. Mintaalkalmazásunk ugyanúgy jelennek meg velünk a kapcsolatot egy adott eseményhez, miközben olyan összetettebb alkalmazást szeretne tárhatja fel az egyéni esemény hívási verem szintre a végpontok közötti tranzakció.

     ![Végpontok közötti tranzakció felület képernyőképe](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>A Góhoz készült OpenCensus nyomkövetési

Csak megismerte a Góhoz készült OpenCensus integrálása a helyi továbbító és az Application Insights alapjait. A [hivatalos OpenCensus Go használati útmutatásért](https://godoc.org/go.opencensus.io) speciális témaköröket tartalmazza.

## <a name="next-steps"></a>További lépések

* [Alkalmazástérkép](./../../azure-monitor/app/app-map.md)
* [Teljes körű alkalmazásteljesítmény-figyelés](./../../azure-monitor/learn/tutorial-performance.md)
