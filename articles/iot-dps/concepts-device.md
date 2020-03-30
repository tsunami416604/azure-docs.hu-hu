---
title: Eszközfogalmak az Azure-eszközök kiépítésében | Microsoft dokumentumok
description: Az eszközkiépítési szolgáltatással (DPS) és az IoT Hubmal rendelkező eszközök eszközkiépítési fogalmai
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285161"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Az IoT Hub Device Provisioning Service-re vonatkozó alapvető információk

Az IoT Hub-eszközkiépítési szolgáltatás egy segítő szolgáltatás az IoT Hub, amely segítségével konfigurálja a nulla érintésű eszköz kiépítése egy adott IoT hub. A Device Provisioning Service-szel több millió eszköz kiépítését végezheti el biztonságosan és skálázható módon.

Ez a cikk áttekintést nyújt az *eszköz* kiépítéssel kapcsolatos eszközfogalmakról. Ez a cikk leginkább a gyártási [lépésben](about-iot-dps.md#manufacturing-step) részt vevő személyiségek számára fontos, hogy az eszköz készen álljon a telepítésre.

## <a name="attestation-mechanism"></a>Igazolási mechanizmus

Az igazolási mechanizmus az eszköz identitásának megerősítésére használt módszer. Az igazolási mechanizmus is fontos a regisztrációs lista, amely megmondja a létesítési szolgáltatás, amely egy adott eszköz höz használt tanúsítvány milyen módszerrel.

> [!NOTE]
> Az IoT Hub "hitelesítési sémát" használ egy hasonló koncepcióhoz az adott szolgáltatásban.

Az eszközkiépítési szolgáltatás a következő igazolási formákat támogatja:
* **X.509 tanúsítványok** a szabványos X.509 tanúsítványhitelesítési folyamat alapján.
* **Platformmegbízhatósági modul (TPM)** egy nonce kihívás alapján, a TPM-szabvány használatával a kulcsok egy aláírt megosztott hozzáférésű aláírási (SAS) jogkivonat bemutatásához. Ehhez nincs szükség fizikai TPM-re az eszközön, de a szolgáltatás elvárja, hogy tanúsítsa az ellenőrző kulcs használatát a [TPM-specifikáció szerint.](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)
* **A** megosztott hozzáférésű aláírás (SAS) [biztonsági jogkivonatain](../iot-hub/iot-hub-devguide-security.md#security-tokens)alapuló szimmetrikus kulcs , amely kivonatolt aláírást és beágyazott lejárati rendszert tartalmaz. További információ: [Symmetric key attestation](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A hardveres biztonsági modul (HSM) az eszköztitkok biztonságos, hardveralapú tárolására szolgál, és a titkos tárolás legbiztonságosabb formája. Mind az X.509-es tanúsítványok, mind a SAS-jogkivonatok tárolhatók a HSM-ben. A HSM-ek a kiépítési szolgáltatás által támogatott mindkét tanúsítványmechanizmussal használható.

> [!TIP]
> Javasoljuk, hogy használjon HSM-et olyan eszközökkel, amelyek biztonságosan tárolják a titkos kulcsokat az eszközökön.

Az eszköztitkok szoftverben (memóriában) is tárolhatók, de kevésbé biztonságos tárolási forma, mint a HSM.

## <a name="registration-id"></a>Regisztrációs azonosító

A regisztrációs azonosító segítségével egyedileg azonosíthatja az eszközt az eszközkiépítési szolgáltatásban. Az eszközazonosítónak egyedinek kell lennie a létesítési szolgáltatás [azonosítójának hatókörében.](#id-scope) Minden eszköznek rendelkeznie kell regisztrációs azonosítóval. A regisztrációs azonosító alfanumerikus, a kis- és nagybetűket nem szokja, és tartalmazhat speciális karaktereket, például kettőspontot, pontot, aláhúzást és kötőjelet.

* A TPM esetében a regisztrációs azonosítót maga a TPM biztosítja.
* Az X.509-alapú tanúsítvány esetében a regisztrációs azonosító t a tanúsítvány tulajdonosának neveként adja meg.

## <a name="device-id"></a>Eszközazonosító

Az eszközazonosító az IoT Hubban megjelenő azonosító. Lehet, hogy a kívánt eszközazonosító be van állítva a regisztrációs bejegyzésben, de nem szükséges beállítani. A kívánt eszközazonosító beállítása csak az egyes regisztrációk esetén támogatott. Ha a regisztrációs listában nincs megadva kívánt eszközazonosító, akkor az eszköz regisztrálásakor a regisztrációs azonosító lesz az eszközazonosító. További információ az [IDo Hub eszközazonosítóiról.](../iot-hub/iot-hub-devguide-identity-registry.md)

## <a name="id-scope"></a>Azonosító hatóköre

Az azonosító hatókör van rendelve egy eszköz kiépítési szolgáltatás, amikor a felhasználó által létrehozott, és arra használják, hogy egyedileg azonosítsa az adott létesítési szolgáltatás az eszköz regisztrálja keresztül. Az azonosító hatókörét a szolgáltatás hozza létre, és nem módosítható, ami garantálja az egyediséget.

> [!NOTE]
> Az egyediség fontos a hosszú ideig futó üzembe helyezési műveletek, valamint az egyesülési és felvásárlási forgatókönyvek esetében.
