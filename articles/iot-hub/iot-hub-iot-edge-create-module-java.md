---
title: "Hozzon létre egy Azure IoT peremhálózati modul Java |} Microsoft Docs"
description: "Ez az oktatóanyag a legújabb Azure IoT peremhálózati Maven-csomagok használata BLA adatok konverter modul írásával bővíthető."
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.openlocfilehash: eddeb5cc13aac7ab33305adcd266465a5b143462
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Hozzon létre egy Azure IoT peremhálózati modul Java

Ez az oktatóanyag bővíthető, hogyan egy előfordulhat, hogy olyan modul létrehozása az Azure IoT peremhálózati Java nyelven.

Ebben az oktatóanyagban végigvezetjük környezetben való telepítés és írásával egy [Gedélyezése](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) adatok konverter moduljának használatával. a legújabb Azure IoT peremhálózati Maven-csomagokat.

## <a name="prerequisites"></a>Előfeltételek

Ebben a szakaszban a IoT peremhálózati modul fejlesztői környezetben fog beállítani. Ez egyaránt vonatkozik *64 bites Windows* és *64 bites Linux (8 Ubuntu/Debian)* operációs rendszerek.

A következő szoftvereket is szükséges:

* [Git ügyfél](https://git-scm.com/downloads).
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* [Maven](https://maven.apache.org/install.html).

Nyisson meg egy parancssori terminálablakot, és a következő tárház klónozása:

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>Általános architektúrája

Az Azure IoT peremhálózati platform fokozottan elfogadja a [Von Neumann architektúra](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Ami azt jelenti, hogy a teljes Azure IoT-biztonsági architektúrája a rendszer, amely feldolgozza a bemeneti és a kimenetet; és győződjön meg arról, hogy minden egyes modul is egy nagyon kicsi bemeneti / kimeneti alrendszer. Az oktatóanyag azt vezeti be a következő két modulok:

1. A modul, amely fogadja a szimulált [Gedélyezése](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) jelezze, és konvertálja azt egy formázott [JSON](https://en.wikipedia.org/wiki/JSON) üzenet.
2. A modult, amely a fogadott kinyomtatja [JSON](https://en.wikipedia.org/wiki/JSON) üzenet.

Az alábbi kép az ebben a projektben tipikus végpont adatfolyamban jeleníti meg:

![Három modulok között Adatfolyamblokk](media/iot-hub-iot-edge-create-module/dataflow.png "bemenet: szimulált BLA modul; Processzor: Konverter modul; Kimenete: Nyomtató modul")

## <a name="understanding-the-code"></a>A kód ismertetése

### <a name="maven-project-structure"></a>Maven project struktúra

Mivel az Azure IoT peremhálózati csomagok Maven alapulnak, létre kell hoznunk egy tipikus Maven project struktúra, amely tartalmazza a `pom.xml` fájlt.

A POM örökli a `com.microsoft.azure.gateway.gateway-module-base` csomagot, amely a függőségeket, beleértve a futásidejű bináris fájljait, az átjáró konfigurációs fájl elérési útját és végrehajtási viselkedésének modul projekthez szükséges összes deklarál. Ez menti, rengeteg időt, és szükségtelenné teszik írásához és sornyi kód több száz újraírási többször.

A pom.xml fájlt is deklarálni kell a szükséges függőségek/beépülő modulok és a neve, ahogy az a következő kódrészletet a modul által használandó konfigurációs fájljának frissíteni kell.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Egy Azure IoT peremhálózati modul alapvető ismeretekkel

Egy Azure IoT Edge-modul is kezelheti, amelynek a feladata, feldolgozónak: fogadja, feldolgozni azt és eredménye.

Lehet, hogy a bemeneti hardverek (például egy mozgásérzékelő) adatait, az egyéb modulok vagy bármely más (például egy időzítő rendszeresen generálja véletlenszerűen) üzenet.

A kimenet a bemeneti hasonló, hogy elindíthatja hardver viselkedés (például a villogó LED), egy üzenetet, amely más modulok vagy bármely más (például a konzol nyomtatás).

Modulok kommunikálnak egymással használatával `com.microsoft.azure.gateway.messaging.Message` osztály. A **tartalom** , egy `Message` bájt tömb, amely bármilyen típusú adatok, például képes. **Tulajdonságok** is elérhetők a `Message` és egyszerűen egy karakterlánc-karakterlánc leképezése. Elképzelhető, hogy a **tulajdonságok** egy HTTPS-kérést, vagy a fájl metaadatait fejlécként.

Egy Azure IoT Edge-modul a Java elkészítéséhez hozzon létre egy új modul osztályt, amely örökli kell `com.microsoft.azure.gateway.core.GatewayModule` és a szükséges absztrakt metódusok végrehajtása `receive()` és `destroy()`. Ezen a ponton is választhatja, hogy a nem kötelező végrehajtásához `start()` vagy `create()` módszerek is. A következő kódrészletet Ismerkedés az Azure IoT peremhálózati modul szerzői mutatja.

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>Konverter modul

| Input (Bemenet)                    | Processzor                              | Kimenet                 | Forrásfájl            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Hőmérséklet-adatok üzenet | Elemzése és létrehozni egy új JSON-üzenet | Struktúra JSON üzenet | `ConverterModule.java` |

Ez a modul az egy tipikus Azure IoT peremhálózati modul. Hőmérséklet-üzenetek származó adatok elfogad (hardveres modult, vagy ilyen esetben a szimulált BLA modul); és majd normalizálja az hőmérséklet üzenet (beleértve a Hozzáfűzés az Üzenetazonosító, hogy igazolnia kell a hőmérséklet figyelmeztetést, és így tovább tulajdonságának beállítása) strukturált JSON-üzenethez.

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>Nyomtató modul

| Input (Bemenet)                          | Processzor | Kimenet                     | Forrásfájl          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Bármely más modulok érkezett üzenetet: | N/A       | Az üzenet naplózása konzolhoz | `PrinterModule.java` |

Ez az egy egyszerű, értetődő, modult, amely a fogadott üzenetek a terminálablakot kimenete.

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Azure IoT peremhálózati konfigurálása

Az utolsó lépés a modulok futtatása előtt a konfigurálása az Azure IoT él és a kapcsolatot létrehozni a modulok között.

Először a Java betöltő (óta Azure IoT peremhálózati támogatja betöltők különböző nyelvű) amely sikerült hivatkozhat deklarálnia kell annak `name` ezt követően a szakaszokban.

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

Miután a betöltők rendelkezik deklaráltuk, azt is kell a modulok, valamint deklarálja. Hasonló a betöltők deklaráló, azokat is hivatkozhat a `name` attribútum. Egy modul deklaráló, azt kell adnia a betöltő azt kell használnia (amely az legyen előtt meghatározott) és a belépési pont (a normalizált osztály neve a modul kellene lennie) minden modulhoz. A `simulated_device` modul az egy natív modult, amely az Azure IoT peremhálózati core runtime csomagban található. Meg kell adnia `args` a JSON-ban a fájl akkor is, ha `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

A konfigurációs végén kapcsolatot létesítünk a kapcsolatokat. Minden kapcsolat van kifejezve `source` és `sink`. Kell mindkét hivatkozó egy előre definiált modul. A kimeneti üzenetét `source` modul a rendszer továbbítja a bemeneti `sink` modul.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>A modulok fut

Használjon `mvn package` mindent történő létrehozásához a `target/` mappát. `mvn clean package`össze is ajánlott.

Használjon `mvn exec:exec` futhatnak az Azure IoT él, és figyelnie kell-e az hőmérséklet és a Tulajdonságok nyomtatott rögzített alapján a konzolhoz.

Ha azt szeretné, az alkalmazás befejezéséhez nyomja le az `<Enter>` kulcs.

> [!IMPORTANT]
> Nem ajánlott a Ctrl + C használatával az IoT-peremhálózati átjáró alkalmazás befejezéséhez. Mivel ez a folyamat rendellenesen eredményezheti.

