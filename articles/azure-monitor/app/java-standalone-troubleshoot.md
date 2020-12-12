---
title: A Java-Azure Monitor Application Insights hibaelhárítása
description: Ismerje meg, hogyan lehet elhárítani a Java-ügynököt a Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1ccfd583b58d129268af2a94e3072200e58308cd
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347830"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Hibaelhárítási útmutató: Azure Monitor Application Insights Javához

Ebben a cikkben néhány olyan gyakori problémát ismertetünk, amelyek a Java-alkalmazások a Application Insights Java-ügynökkel való kiszerelése során merülhetnek fel. Emellett a problémák megoldásának lépéseit is tárgyaljuk. A Application Insights a Azure Monitor platform szolgáltatás egyik funkciója.

## <a name="check-the-self-diagnostic-log-file"></a>Az öndiagnosztika naplófájljának ellenőrzéséhez

Alapértelmezés szerint a Application Insights Java 3,0-ügynöke létrehoz egy nevű naplófájlt, `applicationinsights.log` amely ugyanabban a könyvtárban található, amely a `applicationinsights-agent-3.0.0.jar` fájlt tartalmazza.

Ez a naplófájl az első hely, amely az esetlegesen felmerülő problémákra utal.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Frissítés a Application Insights Java 2. x SDK-ból

Ha már használja a Application Insights Java 2. x SDK-t az alkalmazásban, továbbra is használhatja azt. A Java 3,0-ügynök felismeri. További információ: [frissítés a Java 2. x SDK-ból](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Frissítés Application Insights Java 3,0 előzetes verzióról

Ha a Java 3,0 Preview-ügynökről frissít, gondosan tekintse át az összes [konfigurációs beállítást](./java-standalone-config.md) . A JSON-struktúra teljes mértékben megváltozott az 3,0-es általánosan elérhető kiadásban.

Ezek a változások a következők:

-  A konfigurációs fájl neve módosult a következőre: `ApplicationInsights.json` `applicationinsights.json` .
-  A `instrumentationSettings` csomópont már nem létezik. A rendszer az összes tartalmat `instrumentationSettings` áthelyezi a gyökér szintjére. 
-  A (z `sampling` ),, `jmxMetrics` és rendszerű konfigurációs csomópontok `instrumentation` `heartbeat` kikerülnek a `preview` gyökér szintjére.

## <a name="import-ssl-certificates"></a>SSL-tanúsítványok importálása

Ha az alapértelmezett Java-tárolót használja, akkor már rendelkezik az összes HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvánnyal. Nem kell további SSL-tanúsítványokat importálnia.

Ha egyéni Java-tárolót használ, előfordulhat, hogy importálnia kell az Application Insights Endpoint SSL-tanúsítványait.

### <a name="key-terminology"></a>Kulcsfontosságú terminológia
A kulcstartó a tanúsítványok, a nyilvános kulcsok és a titkos kulcsok tárházát *tárolja* . A Java Development Kit-disztribúciók általában egy végrehajtható fájllal rendelkeznek a kezeléséhez: `keytool` .

Az alábbi példa egy egyszerű parancs, amely egy SSL-tanúsítványt importál a tárolóba:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>SSL-tanúsítvány letöltésének és hozzáadásának lépései

1.  Nyissa meg a kedvenc böngészőjét, és keresse `IngestionEndpoint` meg az alkalmazásához használt kapcsolódási karakterláncban található URL-címet.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="A Application Insightsi kapcsolatok karakterláncát megjelenítő képernyőkép.":::

2.  Válassza a **hely adatainak megtekintése** (zárolás) ikont a böngészőben, majd válassza a **tanúsítvány** lehetőséget.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Képernyőfelvétel a tanúsítvány beállításról a hely adataiban.":::

3.  Lépjen a **részletek** lapra, és válassza a **Másolás fájlba** lehetőséget.
4.  Válassza a **tovább** gombot, válassza a **Base-64 kódolású X. 509 (. CER)** formátuma, majd kattintson ismét a **tovább** gombra.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="A tanúsítvány-Exportálás varázsló képernyőképe, a kiválasztott formátummal.":::

5.  Válassza ki azt a fájlt, ahová menteni szeretné az SSL-tanúsítványt. Ezután válassza a **következő**  >  **Befejezés** lehetőséget. "Az Exportálás sikeres volt" üzenetnek kell megjelennie.
6.  A tanúsítvány létrehozása után itt az ideje, hogy importálja a tanúsítványt egy Java-tárolóba. A tanúsítványok importálásához használja az [előző parancsot](#key-terminology) .

> [!WARNING]
> Az aktuális tanúsítvány lejárta előtt meg kell ismételnie ezeket a lépéseket az új tanúsítvány beszerzéséhez. A lejárati információkat a **tanúsítvány** párbeszédpanel **részletek** lapján tekintheti meg.
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Az SSL-tanúsítvány részleteit megjelenítő képernyőkép.":::
