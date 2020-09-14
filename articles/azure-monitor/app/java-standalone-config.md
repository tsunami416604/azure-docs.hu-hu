---
title: Java-alkalmazások bárhol figyelése – Azure Monitor Application Insights
description: Kód nélküli alkalmazások teljesítményének figyelése bármilyen környezetben futó Java-alkalmazásokhoz az alkalmazás kialakítása nélkül. Az elosztott nyomkövetési és az alkalmazás-hozzárendelés használatával megkeresheti a d problémák kiváltó okát.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 561a6405a49d8f15affbf6d8d4de1a7f4886826a
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056098"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurációs lehetőségek – Java önálló ügynök a Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>A kapcsolatok karakterlánca és a szerepkör neve

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

A kapcsolódási karakterláncot kötelező megadni, és a szerepkör neve fontos minden alkalommal, amikor különböző alkalmazásokból küld adatokat ugyanarra a Application Insights erőforrásra.

További részleteket és további konfigurációs beállításokat alább talál.

## <a name="configuration-file-path"></a>Konfigurációs fájl elérési útja

Alapértelmezés szerint a Application Insights Java 3,0 előzetes verziója elvárja, hogy a konfigurációs fájl legyen elnevezve `ApplicationInsights.json` , és hogy ugyanabban a könyvtárban legyen, mint a `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

Megadhatja saját konfigurációs fájljának elérési útját a következők használatával

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` környezeti változó, vagy
* `applicationinsights.configurationFile` Java rendszertulajdonság

Ha relatív elérési utat ad meg, a rendszer a helyen található könyvtárhoz viszonyítva megoldódik `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

## <a name="connection-string"></a>Kapcsolati sztring

Erre szükség van. A Application Insights erőforrásban található a kapcsolatok karakterlánca:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights a kapcsolatok karakterlánca":::

A környezeti változó használatával is beállíthatja a kapcsolatok karakterláncát `APPLICATIONINSIGHTS_CONNECTION_STRING` .

## <a name="cloud-role-name"></a>Felhőbeli szerepkör neve

A Felhőbeli szerepkör neve az alkalmazás térképén található összetevő címkézésére szolgál.

Ha be szeretné állítani a Felhőbeli szerepkör nevét:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Ha nincs beállítva a Felhőbeli szerepkör neve, a rendszer a Application Insights erőforrás nevét fogja használni az alkalmazás térképén lévő összetevő címkézéséhez.

A Felhőbeli szerepkör nevét a környezeti változó használatával is beállíthatja `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Felhőalapú szerepkör-példány

A Felhőbeli szerepkör példánya alapértelmezés szerint a gép nevét adja meg.

Ha a Felhőbeli szerepkör-példányt a gép neve helyett más értékre szeretné beállítani:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

A Felhőbeli szerepkör-példányt a környezeti változó használatával is beállíthatja `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="application-log-capture"></a>Alkalmazásnapló-rögzítés

Application Insights Java 3,0 előzetes verzió automatikusan rögzíti az alkalmazások naplózását a Log4j, a Logback és a Java. util. Logging használatával.

Alapértelmezés szerint a rendszer az összes, szinten vagy felül végrehajtott naplózást rögzíti `WARN` .

Ha módosítani kívánja ezt a küszöbértéket:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Ezek az érvényes `threshold` értékek, amelyeket megadhat a `ApplicationInsights.json` fájlban, és hogyan felelnek meg a naplózási szintnek a különböző naplózási keretrendszerek között:

| küszöbérték   | Log4j  | Logback | JÚL     |
|-------------------|--------|---------|---------|
| KI               | KI    | KI     | KI     |
| VÉGZETES             | VÉGZETES  | HIBA   | SÚLYOS  |
| HIBA (vagy súlyos) | HIBA  | HIBA   | SÚLYOS  |
| Figyelmeztetés (vagy figyelmeztetés) | FIGYELMEZTETI   | FIGYELMEZTETI    | FIGYELMEZTETÉS |
| INFORMÁCIÓ              | INFORMÁCIÓ   | INFORMÁCIÓ    | INFORMÁCIÓ    |
| CONFIG            | HIBAKERESÉS  | HIBAKERESÉS   | CONFIG  |
| HIBAKERESÉS (vagy kiváló)   | HIBAKERESÉS  | HIBAKERESÉS   | RÉSZLETES    |
| KIFINOMULTABBAN             | HIBAKERESÉS  | HIBAKERESÉS   | KIFINOMULTABBAN   |
| NYOMKÖVETÉS (vagy legfinomabb) | NYOMKÖVETÉSI  | NYOMKÖVETÉSI   | LEGJOBB  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX metrikák

Ha van olyan JMX mérőszáma, amelyet szeretne a rögzítéshez:

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Mikrométer (beleértve a Spring boot indítószerkezet mérőszámait)

Ha az alkalmazás mikrométert [használ,](https://micrometer.io)Application Insights 3,0 (az előzetes verziótól kezdődően), most már rögzíti a Mikrométer globális beállításjegyzékbe eljuttatott metrikákat.

Ha az alkalmazás [Spring boot-működtetőt](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)használ, Application Insights 3,0 (az előzetes verziótól kezdődően) most már rögzíti a Spring boot indítószerkezet által konfigurált mérőszámokat (amely a mikrométert használja, de nem használja a Mikrométer globális beállításjegyzékét).

Ha le szeretné tiltani ezeket a funkciókat:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Szívverés

Alapértelmezés szerint a Application Insights Java 3,0 előnézet 15 percenként egyszer elküld egy szívverési metrikát. Ha a szívverés metrikáját használja a riasztások elindításához, növelheti a szívverés gyakoriságát:

```json
{
  "instrumentationSettings": {
    "preview": {
      "heartbeat": {
        "intervalSeconds": 60
      }
    }
  }
}
```

> [!NOTE]
> A szívverés gyakorisága nem csökkenthető, mivel a szívverési adatok a Application Insights használatának nyomon követésére is használhatók.

## <a name="sampling"></a>Mintavételezés

A mintavétel hasznos lehet, ha csökkenteni kell a költségeket.
A mintavétel függvényként van elvégezve a műveleti AZONOSÍTÓban (más néven nyomkövetési azonosító), így ugyanaz a műveleti azonosító mindig ugyanazt a mintavételi döntést fogja eredményezni. Ez biztosítja, hogy az elosztott tranzakciók részei ne legyenek kiszámítva, míg más részeinek mintavételezése nem történik meg.

Ha például 10%-ra állítja be a mintavételezést, akkor csak a tranzakciók 10%-át fogja látni, de ezek mindegyike a teljes végpontok közötti tranzakció részleteit tartalmazza.

Az alábbi példa bemutatja, hogyan állíthatja be a mintavételezést az **összes tranzakció 10%-ában** – ügyeljen arra, hogy a használati esetnek megfelelő mintavételi sebességet adja meg:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10
        }
      }
    }
  }
}
```

## <a name="http-proxy"></a>HTTP-proxy

Ha az alkalmazás tűzfal mögött található, és nem tud közvetlenül kapcsolódni a Application Insightshoz (lásd: [Application Insights által használt IP-címek](./ip-addresses.md)), akkor a Java 3,0 előzetes verziójának Application Insights konfigurálásával HTTP-proxyt használhat:

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Öndiagnosztika

Az "öndiagnosztika" a Application Insights Java 3,0 előzetes verziójának belső naplózására utal.

Ez hasznos lehet a Application Insights saját maga által felmerülő problémák felderítésére és diagnosztizálására.

Alapértelmezés szerint a konzolhoz a `warn` következő konfigurációnak megfelelő szintet naplóz:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "console",
        "level": "WARN"
      }
    }
  }
}
```

Az érvényes szintek a következők:,,,, `OFF` `ERROR` `WARN` `INFO` `DEBUG` és `TRACE` .

Ha a konzolra való bejelentkezés helyett egy fájlba szeretne bejelentkezni:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "file",
        "directory": "/var/log/applicationinsights",
        "level": "WARN",
        "maxSizeMB": 10
      }
    }
  }
}
```

A fájlok naplózása, ha a fájl látogatottsága `maxSizeMB` befejeződik, a rendszer az aktuális naplófájl mellett csak a legutóbb befejezett naplófájlt fogja használni.
