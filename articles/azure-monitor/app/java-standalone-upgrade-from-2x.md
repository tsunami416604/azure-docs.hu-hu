---
title: Frissítés 2. x-Azure Monitor Application Insights Java-ból
description: Frissítés Azure Monitor Application Insights Java 2. x verzióról
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355045"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Frissítés Application Insights Java SDK 2. x verzióról

Ha már használja a Application Insights Java SDK 2. x verzióját az alkalmazásban, nem kell eltávolítania.
A Java 3,0-ügynök észlelni fogja, és rögzíti és korrelálja a Java SDK 2. x által küldött összes egyéni telemetria, miközben letiltja a Java SDK 2. x által végrehajtott automatikus gyűjtést a duplikált telemetria elkerülése érdekében.

Ha Application Insights 2. x ügynököt használta, el kell távolítania a JVM ARG-t, `-javaagent:` amely a 2. x ügynökre mutat.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers és TelemetryProcessors

A Java SDK 2. x TelemetryInitializers és TelemetryProcessors nem fog futni az 3,0-ügynök használata esetén.
A korábban megkövetelt használati esetek többsége 3,0-ban oldható meg az [Egyéni dimenziók](./java-standalone-config.md#custom-dimensions) konfigurálásával vagy a [telemetria processzorok](./java-standalone-telemetry-processors.md)konfigurálásával.

## <a name="multiple-applications-in-a-single-jvm"></a>Több alkalmazás egyetlen JVM

Jelenleg a 3,0 csak egyetlen [kapcsolatok sztringjét és a szerepkör nevét](./java-standalone-config.md#connection-string-and-role-name) támogatja a futtatási folyamat során. Különösen nem rendelkezhet több tomcat-webalkalmazással ugyanazon a Tomcat-telepítésben eltérő kapcsolati karakterláncok vagy eltérő szerepkör-nevek használatával.

## <a name="http-request-telemetry-names"></a>HTTP-kérelem telemetria neve

Az 3,0-as HTTP-kérelem telemetria-nevei általában az U/X Application Insights portálon jobb összesített nézetet biztosítanak.

Egyes alkalmazások esetében azonban továbbra is használhatja a korábbi telemetria-nevek által biztosított összesített nézetet az U/X-ben, amely esetben a 3,0-es telemetria-processzorok előzetes verziójával térhet vissza az előző nevekhez.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>A telemetria nevének előtagja a http-metódussal ( `GET` , `POST` stb.):

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>A telemetria nevének beállítása a teljes URL elérési útra

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
