---
title: Az Azure IoT Hub x.509-es Hitelesítésszolgáltatói biztonsági áttekintése |} A Microsoft Docs
description: Áttekintés – hogyan hitelesítheti az IoT Hub használatának hitelesítésszolgáltatók X.509-eszközök.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 6a9b4fc5479dda58dd024cdf93cbdf4853f9c965
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055113"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Eszközhitelesítés X.509 Hitelesítésszolgáltatói tanúsítványok használatával

Ez a cikk bemutatja, hogyan X.509 hitelesítésszolgáltató (CA) tanúsítványok segítségével hitelesíti az IoT hubhoz csatlakozó eszközök.  Ebben a cikkben megtudhatja:

* X.509 Hitelesítésszolgáltatói tanúsítvány beszerzése
* Az IoT Hub X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása
* Csatlakozás a eszközök x.509-es Hitelesítésszolgáltatói tanúsítványok használatával
* Aláírt x.509-es Hitelesítésszolgáltatói eszközök hitelesítésének módját

## <a name="overview"></a>Áttekintés

Az x.509-es Hitelesítésszolgáltatói funkciója lehetővé teszi az IoT hubra egy hitelesítésszolgáltató (CA) használatával az eszközhitelesítést. Jelentősen egyszerűbbé téve a kezdeti regisztrációs folyamat, és az ellátási lánc logisztikai eszköz gyártás során. [További információ a forgatókönyv cikkben tájékozódhat az x.509-es Hitelesítésszolgáltatói tanúsítványok használatával](iot-hub-x509ca-concept.md) eszközhitelesítési.  Javasoljuk, hogy ez a cikk a forgatókönyvben a folytatás előtt, ismerteti, miért érdemes a következő lépések létezik.

## <a name="prerequisite"></a>Előfeltétel

Az x.509-es Hitelesítésszolgáltatói funkció használatával, hogy egy IoT Hub-fiók szükséges.  [Ismerje meg, hogyan hozhat létre egy IoT Hub-példány](quickstart-send-telemetry-dotnet.md) Ha még nem rendelkezik.

## <a name="how-to-get-an-x509-ca-certificate"></a>X.509 Hitelesítésszolgáltatói tanúsítvány beszerzése

Az x.509-es Hitelesítésszolgáltatói tanúsítvány van, minden eszközhöz tanúsítványainak a lánc tetején.  Előfordulhat, hogy vásárol, vagy hozzon létre egyet, attól függően, hogyan kívánja használni.

Éles környezetben javasoljuk, hogy egy X.509 Hitelesítésszolgáltatói tanúsítvány nyilvános legfelső szintű hitelesítésszolgáltatótól vásárolhat. A Hitelesítésszolgáltatói tanúsítvány vásárlása az előnye, hogy a legfelső szintű hitelesítésszolgáltató van egy megbízható harmadik féltől származó s ők felelnek az eszközök törvényességéért átjáróként. Fontolja meg ezt a beállítást, ha azt tervezi, az eszközök nyílt IoT-hálózat részét képezi, azok várhatóan használatával kommunikálhat külső termékek vagy szolgáltatások.

A lezárt IoT-hálózatok is létrehozhat egy önaláírt x.509-es Hitelesítésszolgáltatói Kísérletezési vagy használatra.

Függetlenül attól, hogy, hogyan szerezze be az x.509-es Hitelesítésszolgáltatói tanúsítvány, győződjön meg arról, hogy azt a megfelelő titkos kulcs titkos kulcsot, és védett mindig legyen.  Ez a megbízhatósági kapcsolatban az x.509-es Hitelesítésszolgáltatói hitelesítést a megbízhatósági kapcsolat létrehozásához szükséges. 

Ismerje meg, hogyan [hozzon létre egy önaláírt tanúsítványa](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), amellyel kísérletezéssel során ez a funkció leírása.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>A tanúsítvány megbízhatósági láncában bejelentkezési eszközöket

X.509 Hitelesítésszolgáltatói tanúsítvány tulajdonosának egy közbenső hitelesítésszolgáltató, akik viszont be tud jelentkezni egy másik közbenső hitelesítésszolgáltató titkosítási szempontból be tud jelentkezni, és így tovább, amíg a legutóbbi köztes Hitelesítésszolgáltatói leállítja ezt a folyamatot egy eszköz regisztrálásával. Ez kaszkádolt lánc, más néven a tanúsítvány megbízhatósági láncában. A valós életben ez tölt azokra az eszközökre aláíró megbízhatósági delegálását. A delegálás fontos, mert azt a felügyeleti lánc titkosítási szempontból változó láncot hoz létre, és elkerülhető az aláírókulcs megosztását.

![img-Generic-CERT-CHAIN-of-Trust](./media/generic-cert-chain-of-trust.png)

Itt megtudhatja, hogyan [tanúsítványláncolat](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) ahogyan az eszközök bejelentkezéskor.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Az IoT Hub X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása

Az IoT hubhoz, ahol azt fogja az eszközök során a regisztráció és a kapcsolat hitelesítésére az x.509-es Hitelesítésszolgáltatói tanúsítvány regisztrálása.  Az x.509-es Hitelesítésszolgáltatói tanúsítvány regisztrálása, amely magában foglalja a tanúsítvány-fájl feltöltése és adatok lépésből áll.

A feltöltési folyamat magában foglalja a tanúsítványt tartalmazó fájlt feltölteni.  Ez a fájl soha nem tartalmazhat bármely titkos kulcshoz.

A koncepció igazolása birtokában lépés magában foglalja egy titkosítási kérdés és válasz folyamat, és az IoT Hub között.  Tekintve, hogy digitális tanúsítvány tartalma nyilvános, és ezért ki van téve a lehallgatást, az IoT Hub szeretné megállapítani, hogy valóban saját a Hitelesítésszolgáltatói tanúsítvány.  Erre a létrehoz egy véletlenszerű kihívás, akkor be kell jelentkeznie a Hitelesítésszolgáltatói tanúsítvány megfelelő titkos kulccsal.  Ha meghagyta a titkos kulcs titkos és védett, korábban értesítette, akkor csak akkor fog ismeretekkel a lépés végrehajtásához. Titkos kulcsok sérülés utáni titkosságvédelmi az ezzel a módszerrel megbízhatósági forrása.  Miután a kihívás, e lépés elvégzése után, az eredményeket tartalmazó fájl feltöltésével.

Itt megtudhatja, hogyan [a Hitelesítésszolgáltatói tanúsítvány regisztrálása](iot-hub-security-x509-get-started.md#registercerts).

## <a name="how-to-create-a-device-on-iot-hub"></a>Az IoT hub-eszköz létrehozása

Zárja ki az eszköz megszemélyesítést, az IoT Hub kell biztosítani, hogy tudja, milyen eszközöket várható.  Ehhez eszközbejegyzés létrehozása az IoT Hub eszközjegyzékében.  Az IoT Hub használata esetén ez a folyamat automatizált [Device Provisioning Service](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/). 

Itt megtudhatja, hogyan [manuálisan hozzon létre egy eszközt az IoT Hub](iot-hub-security-x509-get-started.md#createdevice).

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Hitelesítő eszközök aláírt x.509-es Hitelesítésszolgáltatói tanúsítványokkal

A regisztrált X.509 Hitelesítésszolgáltatói tanúsítvány és a egy tanúsítvány megbízhatósági láncában bejelentkezett eszközökkel mi marad esetén eszközhitelesítés az eszköz kapcsolódik, akár az első alkalommal.  Egy x.509-es Hitelesítésszolgáltatói aláírással eszköz csatlakozik, feltölti a tanúsítványlánc ellenőrzés céljából. A lánc összes köztes Hitelesítésszolgáltatót és az eszköz tanúsítványt tartalmaz.  Az IoT Hub ezekkel az információkkal hitelesíti a az eszközt egy kétlépéses folyamat.  Az IoT Hub titkosítási szempontból érvényesíti a tanúsítványlánc belső konzisztencia, és ezt követően kiállított proof-az-birtokában kihívást az eszközön.  Az IoT Hub deklarálja az eszköz hiteles a koncepció-az-birtokában sikeres válasz az eszközről.  Ez a nyilatkozat feltételezi, hogy az eszköz titkos kulcs védett és, hogy csak az eszköz sikeresen válaszolhat mutatták.  Azt javasoljuk, hogy az eszközök védelmét a titkos kulcsok biztonságos lapkák például a hardveres biztonsági modulok (HSM) használata.

Az IoT Hub sikeres csatlakozás a hitelesítési folyamat befejeződött, és is, a megfelelő telepítőt.

Itt megtudhatja, hogyan [ennek eszköz végrehajtásáról](iot-hub-security-x509-get-started.md#authenticatedevice).

## <a name="next-steps"></a>További lépések

Ismerje meg [x.509-es Hitelesítésszolgáltatói hitelesítési értékét](iot-hub-x509ca-concept.md) IoT-ben.

Ismerkedés az IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/).
