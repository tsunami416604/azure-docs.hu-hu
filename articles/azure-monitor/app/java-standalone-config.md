---
title: Java-alkalmazások figyelése bárhol – Azure Monitor Application Insights
description: Kód nélküli alkalmazásteljesítmény-figyelés a Bármilyen környezetben futó Java alkalmazásokhoz az alkalmazás programozása nélkül. Keresse meg a d problémák kiváltó okát az elosztott nyomkövetés és az alkalmazásleképezés segítségével.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641887"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurációs beállítások – Az Azure Monitor Application Insights java önálló ügynöke



## <a name="connection-string-and-role-name"></a>Kapcsolati karakterlánc és szerepkör neve

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

A kapcsolati karakterlánc szükséges, és a szerepkör neve fontos minden alkalommal, amikor adatokat küld a különböző alkalmazások ugyanabba az Application Insights erőforrás.

További részleteket és további konfigurációs lehetőségeket alább talál.

## <a name="configuration-file-path"></a>Konfigurációs fájl elérési útja

Az Application Insights Java 3.0 Preview alapértelmezés szerint `ApplicationInsights.json`arra számít, hogy a konfigurációs fájl neve a rendszernek a címtárban `applicationinsights-agent-3.0.0-PREVIEW.jar`történik.

Megadhatja a saját konfigurációs fájl elérési útját a

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`környezeti változó, vagy
* `applicationinsights.configurationFile`Java rendszer tulajdonsága

Ha relatív elérési utat ad meg, az `applicationinsights-agent-3.0.0-PREVIEW.jar` a könyvtárhoz viszonyítva feloldódik.

## <a name="connection-string"></a>Kapcsolati sztring

Erre szükség van. A kapcsolati karakterláncot az Application Insights-erőforrásban találja meg:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights-kapcsolati karakterlánc":::

A kapcsolati karakterláncot a környezeti `APPLICATIONINSIGHTS_CONNECTION_STRING`változó használatával is beállíthatja.

## <a name="cloud-role-name"></a>Felhőbeli szerepkör neve

A felhőbeli szerepkör neve az összetevő címkézésére szolgál az alkalmazástérképen.

Ha be szeretné állítani a felhőbeli szerepkör nevét:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Ha a felhőbeli szerepkör neve nincs beállítva, az Application Insights-erőforrás neve lesz használva az összetevő az alkalmazástérképen.

A felhőbeli szerepkör nevét a környezeti `APPLICATIONINSIGHTS_ROLE_NAME`változó használatával is beállíthatja.

## <a name="cloud-role-instance"></a>Felhőalapú szerepkörpéldány

A felhőbeli szerepkörpéldány alapértelmezés szerint a számítógép nevét adja meg.

Ha a felhőbeli szerepkörpéldányt a gépnév helyett valami másra szeretné beállítani:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

A felhőalapú szerepkörpéldányt a környezeti `APPLICATIONINSIGHTS_ROLE_INSTANCE`változó használatával is beállíthatja.

## <a name="application-log-capture"></a>Alkalmazásnapló-rögzítés

Az Application Insights Java 3.0 Preview automatikusan rögzíti az alkalmazásnaplózást a Log4j, a Logback és a java.util.logging segítségével.

Alapértelmezés szerint rögzíti az összes `WARN` olyan naplózást, amelyet szinten vagy annál magasabb szinten végeznek.

Ha módosítani szeretné ezt a küszöbértéket:

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

Ezek azok `threshold` az érvényes értékek, `ApplicationInsights.json` amelyeket megadhat a fájlban, és hogyan felelnek meg a különböző naplózási keretek naplózási szintjeinek:

| `threshold`  | Log4j  | Visszamaradás | JÚL     |
|--------------|--------|---------|---------|
| KI          | KI    | KI     | KI     |
| Végzetes        | Végzetes  | HIBA   | Súlyos  |
| HIBA/SÚLYOS | HIBA  | HIBA   | Súlyos  |
| FIGYELMEZTETÉS/FIGYELMEZTETÉS | Figyelmeztet   | Figyelmeztet    | FIGYELMEZTETÉS |
| Info         | Info   | Info    | Info    |
| Config       | HIBAKERESÉS  | HIBAKERESÉS   | Config  |
| HIBAKERESÉS/FINOM   | HIBAKERESÉS  | HIBAKERESÉS   | Finom    |
| Finomabb        | HIBAKERESÉS  | HIBAKERESÉS   | Finomabb   |
| NYOM/LEGFINOMABB | Nyomkövetési  | Nyomkövetési   | Legjobb  |
| AZ ÖSSZES          | AZ ÖSSZES    | AZ ÖSSZES     | AZ ÖSSZES     |

## <a name="jmx-metrics"></a>JMX-mutatók

Ha van néhány JMX mérőszámod, amelyet szeretnél rögzíteni:

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

## <a name="micrometer"></a>Mikrométer

Alapértelmezés szerint ha az alkalmazás [használja a Mikrométer](https://micrometer.io), Application Insights 3.0 (kezdve Preview.2) most hozzáteszi magát a Mikrométer globális rendszerleíró adatbázis és rögzíti a mikrométer metrikák.

Ha le szeretné tiltani ezt a funkciót:

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

Alapértelmezés szerint az Application Insights Java 3.0 preview 15 percenként egyszer küld szívverés-metrikát. Ha a szívverésmérőt használja a riasztások aktiválásához, növelheti a szívverés gyakoriságát:

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
> A szívverés gyakorisága nem csökkenthető, mivel a szívverési adatok az Application Insights használatának nyomon követésére is használatot tartalmaznak.

## <a name="sampling"></a>Mintavételezés

Mintavételi akkor hasznos, ha csökkenteni kell a költségeket.
A mintavételezés funkcióként történik a műveletazonosítón (más néven nyomkövetési azonosító), így ugyanaz a műveletazonosító mindig ugyanazt a mintavételi döntést eredményezi. Ez biztosítja, hogy nem kap egy elosztott tranzakció egyes részeit, amíg annak más részei is mintavételezik.

Ha például a mintavételezést 10%-ra állítja, akkor csak a tranzakciók 10%-a jelenik meg, de a 10%-os tranzakciók mindegyike teljes körű tranzakciós adatokkal fog rendelkezni.

Íme egy példa arra, hogyan állíthatja be a mintavételezést az **összes tranzakció 10%-ára** - kérjük, győződjön meg arról, hogy a használati esetnek megfelelő mintavételi arányt állított be:

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

Ha az alkalmazás tűzfal mögött van, és nem tud közvetlenül csatlakozni az Application Insightshoz (lásd: [Az Application Insights által használt IP-címeket),](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)beállíthatja, hogy az Application Insights Java 3.0 Preview HTTP-proxyt használjon:

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

Az "öndiagnosztika" az Application Insights Java 3.0 Előzetes verziójából történő belső naplózásra vonatkozik.

Ez hasznos lehet az Application Insights problémák észleléséhez és diagnosztizálásához.

Alapértelmezés szerint a konzolra naplóz, amely a konfigurációnak megfelelő szinttel `warn`van eltasztaként:

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

Az érvényes `OFF` `ERROR`szintek `WARN` `INFO`a `DEBUG`, `TRACE`, , , , és .

Ha a konzolra való bejelentkezés helyett egy fájlba szeretne naplózni:

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

A fájlnaplózás használatakor, `maxSizeMB`amint a fájl eléri a fájlt, az átgördül, és az aktuális naplófájl mellett csak a legutóbb befejezett naplófájlt tartja meg.
