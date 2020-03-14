---
title: Az eszközökre vonatkozó fogalmak az Azure-eszközök kiépítés terén | Microsoft Docs
description: Az eszközök kiépítési szolgáltatásával (DPS) és IoT Hubával kapcsolatos eszköz-kiépítési fogalmakat ismerteti.
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79285161"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Az eszközök IoT Hub Device Provisioning Service kapcsolatos fogalmak

A IoT Hub Device Provisioning Service egy olyan IoT Hub segítő szolgáltatás, amellyel a nulla érintéses eszköz kiépíthető a megadott IoT hubhoz. A Device Provisioning Service-szel több millió eszköz kiépítését végezheti el biztonságosan és skálázható módon.

Ez a cikk áttekintést nyújt az eszközök kiépítés során felmerülő *fogalmakról* . Ez a cikk a legfontosabb, hogy az eszköz üzembe helyezésének előkészítéséhez szükséges [gyártási lépésben](about-iot-dps.md#manufacturing-step) milyen personák szerepelnek.

## <a name="attestation-mechanism"></a>Igazolási mechanizmus

Az igazolási mechanizmus az eszköz identitásának megerősítésére szolgál. Az igazolási mechanizmus a beléptetési listához is kapcsolódik, amely közli a kiépítési szolgáltatással, hogy az adott eszközzel milyen igazolást kell használni.

> [!NOTE]
> IoT Hub a "hitelesítési séma" kifejezést használja az adott szolgáltatáshoz hasonló fogalomhoz.

A Device kiépítési szolgáltatás a következő igazolási formákat támogatja:
* **X. 509 tanúsítványok** a szabványos x. 509 tanúsítvány-hitelesítési folyamat alapján.
* **Platformmegbízhatósági modul (TPM)** egy egyszeres kihívás alapján, a kulcsokhoz tartozó TPM standard használatával egy aláírt közös hozzáférésű aláírási (SAS-) tokent mutat be. Ehhez nincs szükség fizikai TPM-re az eszközön, de a szolgáltatás a [TPM-specifikáció](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)alapján tanúsítja, hogy a hátirat kulcsát használja.
* A **szimmetrikus kulcs** a közös hozzáférésű aláírás (SAS) [biztonsági jogkivonatok](../iot-hub/iot-hub-devguide-security.md#security-tokens)alapján, amelyek egy kivonatoló aláírást és egy beágyazott lejáratot foglalnak magukban. További információ: [szimmetrikus kulcs igazolása](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A hardveres biztonsági modul, vagy a HSM biztonságos, hardveres tárolást biztosít az eszköz titkai számára, és a titkos tároló legbiztonságosabb formája. Az X. 509 tanúsítványok és az SAS-tokenek a HSM-ben is tárolhatók. A HSM a kiépítési szolgáltatás által támogatott igazolási mechanizmusokkal is használható.

> [!TIP]
> Javasoljuk, hogy használjon egy HSM-et az eszközökkel, hogy biztonságosan tárolja az eszközein a titkokat.

Az eszköz titkai a szoftverben (memóriában) is tárolhatók, de ez a tárterület kevésbé biztonságos, mint a HSM.

## <a name="registration-id"></a>Regisztrációs azonosító

A regisztrációs azonosító az eszköz kiépítési szolgáltatásban való egyedi azonosítására szolgál. Az eszköz AZONOSÍTÓjának egyedinek kell lennie a létesítési szolgáltatás [azonosítójának hatókörében](#id-scope). Minden eszköznek regisztrációs AZONOSÍTÓval kell rendelkeznie. A regisztrációs azonosító alfanumerikus, kis-és nagybetű nem különbözik, és tartalmazhat speciális karaktereket, beleértve a kettőspontot, az időtartamot, az aláhúzást és a kötőjelet.

* TPM esetén a regisztrációs azonosítót maga a TPM is megadja.
* X. 509-alapú igazolás esetén a regisztrációs azonosító a tanúsítvány tulajdonosának neveként van megadva.

## <a name="device-id"></a>Eszközazonosító

Az eszköz azonosítója a IoT Hubban megjelenő azonosító. Előfordulhat, hogy a kívánt eszköz azonosítója be van állítva a beléptetési bejegyzésben, de nem kötelező megadni. A kívánt eszköz AZONOSÍTÓjának beállítása csak az egyéni regisztrációk esetében támogatott. Ha nincs megadva a kívánt eszköz azonosítója a beléptetési listán, a rendszer a regisztrációs azonosítót használja az eszköz AZONOSÍTÓJAként az eszköz regisztrálása során. További információ a [IoT hub eszköz-azonosítókkal](../iot-hub/iot-hub-devguide-identity-registry.md)kapcsolatban.

## <a name="id-scope"></a>AZONOSÍTÓ hatóköre

Az azonosító hatóköre egy eszköz kiépítési szolgáltatásához van hozzárendelve, amikor a felhasználó hozza létre, és az az eszköz, amelyet az adott kiépítési szolgáltatás egyedi azonosítására használ, az eszköz regisztrálva lesz. Az azonosító hatókörét a szolgáltatás hozza létre, és nem módosítható, ami garantálja az egyediséget.

> [!NOTE]
> Az egyediség fontos a hosszan futó üzembe helyezési műveletek, az egyesítési és a beszerzési forgatókönyvek esetében.
