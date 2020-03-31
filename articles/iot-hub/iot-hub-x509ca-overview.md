---
title: Az Azure IoT Hub X.509 hitelesítésbiztosítási rendszer biztonságának áttekintése | Microsoft dokumentumok
description: Áttekintés – az Eszközök hitelesítése az IoT Hubba az X.509 tanúsítványszolgáltatók használatával.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3d02d3573902964a8549fa0eeb1f4f1471de1752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284511"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Eszköz hitelesítése X.509-es hitelesítésszolgáltatói tanúsítványokkal

Ez a cikk azt ismerteti, hogy miként használhatja az X.509-es hitelesítésszolgáltatói tanúsítványokat az IoT Hubot összekötő eszközök hitelesítéséhez.  Ebben a cikkben megtudhatja:

* X.509 hitelesítésszolgáltatói tanúsítvány beszerezése
* Az X.509 hitelesítésszolgáltatói tanúsítvány regisztrálása az IoT Hubba
* Eszközök aláírása X.509 hitelesítésszolgáltatói tanúsítványokkal
* Az X.509 hitelesítési megállapodással aláírt eszközök hitelesítésének a módját

## <a name="overview"></a>Áttekintés

Az X.509 hitelesítésszolgáltató szolgáltatás lehetővé teszi az eszközök hitelesítését az IoT Hub on a hitelesítésszolgáltató (CA). Ez nagyban leegyszerűsíti a kezdeti eszköz regisztrációs folyamat, és az ellátási lánc logisztika során eszközgyártás. [Az X.509 hitelesítésszolgáltatói tanúsítványok](iot-hub-x509ca-concept.md) eszközhitelesítéshez való használatának értékéről ebben a forgatókönyvben olvashat bővebben.  Javasoljuk, hogy olvassa el ezt a forgatókönyvet, mielőtt folytatná, mivel elmagyarázza, hogy miért léteznek a következő lépések.

## <a name="prerequisite"></a>Előfeltétel

Az X.509 hitelesítésszolgáltatás használatához IoT Hub-fiókszükséges.  [Megtudhatja, hogyan hozhat létre egy IoT Hub-példányt,](quickstart-send-telemetry-dotnet.md) ha még nem rendelkezik ilyen.

## <a name="how-to-get-an-x509-ca-certificate"></a>X.509 hitelesítésszolgáltatói tanúsítvány beszerezése

Az X.509 hitelesítésszolgáltatói tanúsítvány az egyes eszközök tanúsítványláncának tetején található.  Attól függően, hogy hogyan kívánja használni, vásárolhat vagy létrehozhat egyet.

Éles környezetben azt javasoljuk, hogy vásároljon egy X.509 hitelesítésszolgáltatói tanúsítványt egy nyilvános legfelső szintű hitelesítésszolgáltatótól. A hitelesítésszolgáltatói tanúsítvány megvásárlásának előnye, hogy a legfelső szintű hitelesítésszolgáltató megbízható harmadik félként működik, hogy kezeskedjen az eszközök legitimitásáért. Vegye figyelembe ezt a lehetőséget, ha azt szeretné, hogy az eszközök egy nyílt IoT-hálózat részei legyenek, ahol várhatóan harmadik fél termékeivel vagy szolgáltatásaival lépnek kapcsolatba.

Létrehozhat egy önaláírt X.509 hitelesítéscsoportot is kísérletezésre vagy zárt IoT-hálózatokban való használatra.

Függetlenül attól, hogy hogyan szerzi be az X.509 hitelesítésszolgáltatói tanúsítványt, mindig tartsa meg a megfelelő titkos kulcs titkos kulcsát.  Ez az X.509 hitelesítésszolgáltató hitelesítési megbízhatóságának kiépítéséhez szükséges.

Ismerje meg, hogyan [hozhat létre önaláírt hitelesítésszolgáltatói tanúsítványt,](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)amelyet a szolgáltatás leírásában kísérletezésre használhat.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Eszközök bejelentkezése a megbízhatósági tanúsítványláncba

Az X.509 hitelesítésszolgáltatói tanúsítvány tulajdonosa kriptográfiailag aláírhat egy köztes hitelesítésszolgáltatót, amely viszont aláírhat egy másik köztes hitelesítésszolgáltatót, és így tovább, amíg az utolsó köztes hitelesítésszolgáltató egy eszköz aláírásával le nem állítja ezt a folyamatot. Az eredmény egy kaszkádolt tanúsítványlánc, amelyet tanúsítványmegbízhatósági láncnak neveznek. A való életben ez játszik ki, mint a bizalom delegálása felé aláíró eszközök. Ez a delegálás azért fontos, mert kriptográfiailag változó felügyeleti láncot hoz létre, és elkerüli az aláíró kulcsok megosztását.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

Az eszköztanúsítványnak (más néven levéltanúsítványnak) rendelkeznie kell a *tulajdonos nevével* az IoT-eszköz Azure IoT Hubban történő regisztrálásakor használt **eszközazonosítóra.** Ez a beállítás szükséges a hitelesítéshez.

Itt megtudhatja, hogyan [hozhat létre tanúsítványláncot](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) az eszközök aláírásakor végzett ekként.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Az X.509 hitelesítésszolgáltatói tanúsítvány regisztrálása az IoT Hubba

Regisztrálja x.509 hitelesítésszolgáltatói tanúsítványát az IoT Hubba, ahol a regisztráció és a kapcsolat során az eszközök hitelesítésére szolgál.  Az X.509 hitelesítésszolgáltatói tanúsítvány regisztrálása két lépésből áll, amely magában foglalja a tanúsítványfájlok feltöltését és a birtoklás igazolását.

A feltöltési folyamat magában foglalja a tanúsítványt tartalmazó fájl feltöltését.  Ez a fájl soha nem tartalmazhat személyes kulcsokat.

A birtoklási lépés igazolása egy kriptográfiai kihívást és válaszadási folyamatot foglal magában Ön és az IoT Hub között.  Tekintettel arra, hogy a digitális tanúsítvány tartalma nyilvános, és ezért ki van téve a lehallgatásnak, az IoT Hub szeretné megtudni, hogy valóban Ön a hitelesítésszolgáltatói tanúsítvány a tulajdonában van.  Ezt úgy kell megtennie, hogy véletlenszerű kihívást hoz létre, amelyet alá kell írnia a hitelesítésszolgáltatói tanúsítvány megfelelő személyes kulcsával.  Ha ön tartott a közlegény kulcs titok és megvéd mint korábbi tanácsos, akkor egyetlen ön akarat rendelkezik a tudás -hoz kiegészít ez lép. A személyes kulcsok titkossága a megbízható forrás ebben a módszerben.  A kihívás aláírása után hajtsa végre ezt a lépést az eredményeket tartalmazó fájl feltöltésével.

Itt megtudhatja, hogyan [regisztrálhatja hitelesítésszolgáltatói tanúsítványát](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Eszköz létrehozása az IoT Hubon

Az eszköz megszemélyesítése kiiszatásának kipróbálása érdekében az IoT Hub megköveteli, hogy tudassa vele, milyen eszközökre számíthat.  Ehhez hozzon létre egy eszközbejegyzést az IoT Hub eszközbeállítás-nyilvántartásában.  Ez a folyamat automatikus, ha az IoT [Hub-eszközkiépítési szolgáltatás használata.](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) 

Itt megtudhatja, hogyan [hozhat létre manuálisan egy eszközt az IoT Hubban.](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)

X.509-es eszköz létrehozása az IoT hubhoz

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>X.509 hitelesítésszolgáltatói tanúsítvánnyal aláírt eszközök hitelesítése

Az X.509 hitelesítésszolgáltatói tanúsítvány regisztrálásával és a megbízhatósági láncba bejelentkezett eszközökkel az eszköz hitelesítése marad, amikor az eszköz csatlakozik, még az első alkalommal is.  Amikor egy X.509 hitelesítésszolgáltató által aláírt eszköz csatlakozik, feltölti a tanúsítványláncát ellenőrzésre. A lánc tartalmazza az összes köztes hitelesítésszolgáltatói és eszköztanúsítványt.  Ezekkel az adatokkal az IoT Hub két lépésben hitelesíti az eszközt.  Az IoT Hub kriptográfiailag ellenőrzi a tanúsítványláncot a belső konzisztencia érdekében, majd kiállítási a birtoklásigazolási kihívást jelent az eszköz számára.  Az IoT Hub hitelesnek nyilvánítja az eszközt az eszközről érkező sikeres birtoklásigazolási válaszon.  Ez a deklaráció feltételezi, hogy az eszköz személyes kulcsa védett, és csak az eszköz tud sikeresen válaszolni erre a kihívásra.  Javasoljuk, hogy a személyes kulcsok védelme érdekében használjon biztonságos chipeket, például hardveres biztonsági modulokat (HSM) az eszközökön.

Az IoT Hubhoz való sikeres eszközkapcsolat befejezi a hitelesítési folyamatot, és egymegfelelő beállítást is jelez.

Itt megtudhatja, hogyan [végezheti el ezt az eszközkapcsolati lépést.](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)

## <a name="next-steps"></a>Következő lépések

Ismerje meg [az X.509 hitelesítésszolgáltató-hitelesítés iot-hitelesítés értékét.](iot-hub-x509ca-concept.md)

Ismerkedés az IoT [Hub-eszközkiépítési szolgáltatással.](https://docs.microsoft.com/azure/iot-dps/)
