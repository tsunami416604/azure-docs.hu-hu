---
title: Azure IoT-eszköz SDK-k platformtámogatása | Microsoft dokumentumok
description: A nyílt forráskódú eszközök SDK-i c, .NET (C#), Java, Node.js és Python esetén érhetők el a GitHubon az Azure IoT Hub és az Eszközkiépítési szolgáltatáshoz (DPS) való csatlakoztatáshoz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045115"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Az Azure IoT-eszköz SDK-k platformtámogatása

A Microsoft arra törekszik, hogy folyamatosan bővítse az Azure IoT Hub-kompatibilis eszközök univerzumát. A Microsoft nyílt forráskódú eszköz SDK-kat tesz közzé a GitHubon, hogy segítsen az eszközök csatlakoztatásában az Azure IoT Hubhoz és az eszközkiépítési szolgáltatáshoz. Az eszköz SDK-k érhetők el a C, .NET (C#), Java, Node.js és Python. A Microsoft minden SDK-t tesztel annak érdekében, hogy az a [Microsoft SDK-k és](#microsoft-sdks-and-device-platform-support) az eszközplatform támogatási szakaszában részletezett támogatott konfigurációkon futjon.

Az eszköz SDK-k mellett a Microsoft számos más lehetőséget is kínál, amelyekkel az ügyfelek és a fejlesztők csatlakoztathatók az Azure IoT-hez:

* A Microsoft több partnervállalattal is együttműködik, hogy segítsen nekik az Azure IoT C SDK-n alapuló fejlesztői készletek közzétételében a hardverplatformjaikon.

* A Microsoft együttműködik a Microsoft megbízható partnereivel, hogy az Azure IoT-hez tesztelt és tanúsított eszközök folyamatosan bővülő készletét biztosítsa. Ezen eszközök aktuális listáját az [IoT-eszközkatalógushoz hitelesített Azure-tanúsítványban.](https://catalog.azureiotsolutions.com/)

* A Microsoft platformabsztrakciós réteget (PAL) biztosít az Azure IoT Hub Device C SDK-ban, amely segít a fejlesztőknek az SDK-t a platformjukra való egyszerű portolásában. További információ: [C SDK portolási útmutató.](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)

Ez a témakör a Microsoft SDK-król és az általuk támogatott platformkonfigurációkról, valamint a fent felsorolt egyéb beállításokról nyújt tájékoztatást.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK-k és az eszközplatform támogatása

A Microsoft nyílt forráskódú SDK-kat tesz közzé a GitHubon a következő nyelveken: C, .NET (C#), Node.js, Java és Python. Az SDK-k és függőségeik ebben a szakaszban találhatók. Az SDK-k minden olyan eszközplatformon támogatottak, amely megfelel ezeknek a függőségeknek.

A microsoft minden egyes felsorolt SDK esetében:

* Folyamatosan fejleszti és futtatja a githubon lévő megfelelő SDK főágával kapcsolatos végpontok között végzett teszteket számos népszerű platformon.  Annak érdekében, hogy a különböző fordítóverziók ban teszteljük a teszteket a legújabb LTS verzióval és a legnépszerűbb verzióval.

* Adott esetben telepítési útmutatást vagy telepítési csomagokat tartalmaz.

* Teljes mértékben támogatja az SDK-k a GitHub on nyílt forráskódú kód, az elérési út az ügyfelek hozzájárulásait, és a termék csapat elkötelezettségét A GitHub problémák.

### <a name="c-sdk"></a>C SDK

Az [Azure IoT Hub C sdk-eszköz](https://github.com/Azure/azure-iot-sdk-c) tesztelése és a következő konfigurációk támogatása.

| Operációs rendszer                  | TLS-könyvtár                  | További követelmények                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL vagy BearSSL | Berkeley aljzatok</br></br>Hordozható operációs rendszer interfész (POSIX)                       |
| iOS 12.2            | OpenSSL                      | XCode emulált OSX 10.13.4                                                               |
| Windows 10-család   | Schannel                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [MXChip IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere operációs rendszer     | Wolfssl között                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearsSL között                      | [ESP32 vagy ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

Az [Azure IoT Hub Python-eszköz SDK-t](https://github.com/Azure/azure-iot-sdk-python) tesztelik, és támogatja a következő konfigurációkat.

| Operációs rendszer                  | Fordító                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*, 3.5 vagy újabb |
| MacOS Magas Sierra   | Python 2.7.*, 3.5 vagy újabb |
| Windows 10-család   | Python 2.7.*, 3.5 vagy újabb |

Csak a Python 3.5.3-as vagy újabb verziója támogatja az aszinkron API-kat, javasoljuk a 3.7-es vagy újabb verzió használatát.

### <a name="net-sdk"></a>.NET SDK

Az [Azure IoT Hub .NET (C#) sdk-eszközt](https://github.com/Azure/azure-iot-sdk-csharp) a következő konfigurációkkal tesztelik, és támogatja azokat.

| Operációs rendszer                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 asztali és kiszolgálósink   | .NET Core 2.1, .NET Framework 4.5.1 vagy .NET Framework 4.7 |

A .NET SDK a Windows IoT Core-ral az [Azure Device Agent](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) del vagy egy egyéni NTService-szolgáltatással is [használható, amely az RPC segítségével kommunikálhat az UWP-alkalmazásokkal.](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)

### <a name="nodejs-sdk"></a>Node.js SDK

Az [Azure IoT Hub Node.js sdk-eszköz](https://github.com/Azure/azure-iot-sdk-node) tesztelése és a következő konfigurációk támogatása.

| Operációs rendszer                  | Csomópont verziója    |
|---------------------|-----------------|
| Linux               | LTS és áram |
| Windows 10-család   | LTS és áram |

### <a name="java-sdk"></a>Java SDK

Az [Azure IoT Hub Java-eszköz SDK-t](https://github.com/Azure/azure-iot-sdk-java) a következő konfigurációkkal tesztelik, és támogatja.

| Operációs rendszer                     | Java verzió |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 x64-es család  | Java 8       |

## <a name="partner-supported-development-kits"></a>Partner által támogatott fejlesztői készletek

A Microsoft különböző partnerekkel együttműködve több mikroprocesszor-architektúrához biztosít fejlesztői készleteket. Ezek a partnerek az Azure IoT C SDK-t a platformjukra portizálták. A partnerek létrehozzák és karbantartják az SDK platformabsztrakciós rétegét (PAL). A Microsoft ezekkel a partnerekkel együttműködve nyújt kiterjesztett támogatást.

| Partner             | Eszközök                            | Hivatkozás                     | Támogatás |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif között           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [Github](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE az IoT SDK-hoz](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST mikroelektronika | STM32L4 sorozat <br/> STM32F4 sorozat <br/>  STM32F7 sorozat <br/>  STM32L4 felderítési készlet IoT-csomóponthoz    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Támogatás](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF kilövőállás </br> CC3220S indítópult </br> CC3235SF kilövőállás </br> CC3235S kilövőállás </br> MSP432E4 kilövőállás | [Azure IoT beépülő modul a SimpleLink-hez](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E Fórum](https://e2e.ti.com) <br/> [TI E2E Fórum CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E Fórum MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>A Microsoft Azure IoT C SDK portolása

Ha az eszköz platform nem tartozik az előző szakaszok egyike, fontolja meg az Azure IoT C SDK portolása. A C SDK portolása elsősorban az SDK platformabsztrakciós rétegének (PAL) megvalósítását foglalja magában. A PAL olyan primitíveket határoz meg, amelyek az eszköz és az SDK magasabb szintű funkciói között biztosítanak kapcsolást. További információ: [Porting Guidance](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft-partnerek és minősített Azure IoT-eszközök

A Microsoft számos partnerrel együttműködve folyamatosan bővítheti az Azure IoT-univerzumot az Azure IoT által tesztelt és minősített eszközökkel.

* Az Azure IoT-tanúsítvánnyal rendelkező eszközök közötti böngészésről a [Microsoft Azure Certified for IoT Device Catalog (Microsoft Azure Certified for IoT Device Catalog) témakörben](https://catalog.azureiotsolutions.com/)látható.

* Ha többet szeretne megtudni az Azure Certified for IoT-ökoszisztémáról, [olvassa el a Join the Certified for IoT ecosystem című témakört.](https://catalog.azureiotsolutions.com/register)

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Csatlakozás az IoT Hubhoz SDK nélkül

Ha nem tudja használni az IoT Hub-eszköz SDK-k egyikét, közvetlenül csatlakozhat az IoT [Hub REST API-khoz](https://docs.microsoft.com/rest/api/iothub/) bármely olyan alkalmazásból, amely képes HTTPS-kérések és válaszok küldésére és fogadására.

## <a name="support-and-other-resources"></a>Támogatás és egyéb források

Ha problémákat tapasztal az Azure IoT-eszköz SDK-k használata közben, számos módon kérhet támogatást. Kipróbálhatja az alábbi csatornák egyikét:

**Hibák jelentése** – Az eszköz SDK-k ban lévő hibák at az adott GitHub-projekt problémaoldalán lehet jelenteni. A javítások gyorsan elhaladnak a projekttől a termékfrissítésekig.

* [Az Azure IoT Hub C SDK-val kapcsolatos problémák](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Az Azure IoT Hub .NET (C#) SDK-problémák](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Az Azure IoT Hub Java SDK-val kapcsolatos problémái](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Az Azure IoT Hub node.js SDK-val kapcsolatos problémák](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Az Azure IoT Hub Python SDK-val kapcsolatos problémák](https://github.com/Azure/azure-iot-sdk-python/issues)

**Microsoft ügyfélszolgálati csapat** – A [támogatási csomaggal](https://azure.microsoft.com/support/plans/) rendelkező felhasználók bevonhatják a Microsoft ügyfélszolgálati csapatát egy új támogatási kérelem közvetlenül az [Azure Portalról](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)történő létrehozásával.

**Szolgáltatáskérések** – Az Azure IoT-szolgáltatáskérelmeket a termék [Felhasználói hangoldalán](https://feedback.azure.com/forums/321918-azure-iot)keresztül követi nyomon a rendszer.

## <a name="next-steps"></a>További lépések

* [Eszköz- és szolgáltatásspecifikus SDK-k](iot-hub-devguide-sdks.md)
* [Portolási útmutató](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
