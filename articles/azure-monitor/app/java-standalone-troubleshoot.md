---
title: Hibaelhárítás – Azure Monitor Application Insights Java
description: Hibaelhárítás Azure Monitor Application Insights Java-ban
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855617"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Hibaelhárítás Azure Monitor Application Insights Java-ban

Ebben a cikkben néhány olyan gyakori problémát ismertetünk, amelyekkel a felhasználók a Java-ügynökkel is rendelkezhetnek, miközben a Java-ügynököt használják a problémák megoldásához szükséges lépésekkel együtt.

## <a name="self-diagnostic-log-file"></a>Önálló diagnosztikai naplófájl

Alapértelmezés szerint a Application Insights Java 3,0 egy olyan naplófájlt hoz létre `applicationinsights.log` , amely ugyanabban a könyvtárban van, ahol a `applicationinsights-agent-3.0.0.jar` fájl található.

Ez a naplófájl az első hely, amely az esetlegesen felmerülő problémákra utal.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Frissítés Application Insights Java 2. x SDK-ból

Lásd: [frissítés 2. x SDK-ból](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Frissítés 3,0 előzetes verzióról

Ha 3,0 előzetes verzióról frissít, tekintse át az összes [konfigurációs beállítást](./java-standalone-config.md) , mivel a JSON-struktúra teljes mértékben megváltozott a 3,0-es kiadásban.

Ezek a változások a következők:

1.  Maga a konfigurációs fájl neve módosult a verzióról a verzióra `ApplicationInsights.json` `applicationinsights.json` .
2.  A `instrumentationSettings` csomópont már nem létezik. A rendszer az összes tartalmat `instrumentationSettings` áthelyezi a gyökér szintjére. 
3.  A (z `sampling` `jmxMetrics` ), `instrumentation` és rendszerű konfigurációs csomópontok `heartbeat` kikerülnek a `preview` gyökér szintjére.

## <a name="ssl-certificate-issues"></a>SSL-tanúsítványokkal kapcsolatos problémák

Ha az alapértelmezett Java-tárolót használja, akkor már rendelkezik az összes HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvánnyal, és nem kell további SSL-tanúsítványokat importálnia.

Ha egyéni Java-tárolót használ, előfordulhat, hogy importálnia kell az Application Insights Endpoint SSL-tanúsítványait.

### <a name="some-key-terminology"></a>Néhány kulcsfontosságú terminológia:
*A kulcstartó* a tanúsítványok, a nyilvános és a titkos kulcsok tárháza. Általában a JDK-eloszlások rendelkeznek egy végrehajtható fájllal a kezeléséhez `keytool` .

Az alábbi példa egy egyszerű parancs, amely egy SSL-tanúsítványt importál a tárolóba:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Az SSL-tanúsítvány letöltésének és hozzáadásának lépései:

1.  Nyissa meg kedvenc böngészőjét, és nyissa meg az `IngestionEndpoint` alkalmazás az alábbi ábrán látható kapcsolódási karakterláncban található URL-címét

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights a kapcsolatok karakterlánca":::

2.  Kattintson a "hely adatainak megtekintése" (zárolás) ikonra a böngészőben, majd kattintson a "tanúsítvány" lehetőségre az alábbi ábrán látható módon.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="SSL-tanúsítvány rögzítése":::

3.  Lépjen a Részletek lapra, és kattintson a Másolás fájlba elemre.
4.  Kattintson a Tovább gombra, és válassza a "Base-64 kódolású X. 509 (. CER) "formátum és kattintson a Tovább gombra.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="SSL-tanúsítvány ExportWizard":::

5.  Válassza ki azt a fájlt, amelybe menteni szeretné az SSL-tanúsítványt. Végül kattintson a Tovább gombra, és fejezze be. Az "az Exportálás sikeres volt" üzenetnek kell megjelennie.
6.  Miután a tanúsítványa elvégezte a tanúsítvány importálását egy Java-tárolóba. A fenti [parancs](#some-key-terminology) használatával importálhatja a tanúsítványokat.

> [!WARNING]
> Az aktuális tanúsítvány lejárta előtt meg kell ismételnie ezeket a lépéseket az új tanúsítvány beszerzéséhez. A lejárati információkat a tanúsítvány előugró ablakának részletek lapján találja, ahogy az alábbi ábrán is látható.

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL-tanúsítvány részletei":::
