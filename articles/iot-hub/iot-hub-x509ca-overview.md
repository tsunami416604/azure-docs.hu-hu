---
title: "Azure IoT Hub X.509 hitelesítésszolgáltató biztonsági áttekintése |} Microsoft Docs"
description: "Áttekintés – hogyan hitelesítheti az X.509 tanúsítvány hitelesítésszolgáltatók használatával IoT Hub eszközöket."
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 080c83fd0c34bdcb8978edf0ba4f783402a88b1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Eszközhitelesítési X.509 CA-tanúsítványok használata

Ez a cikk ismerteti, hogyan X.509 tanúsítvány hitelesítésszolgáltatói (CA) tanúsítványok segítségével hitelesíti az IoT-központ csatlakozó eszközök.  Ebben a cikkben megtudhatja:

* Egy X.509 Hitelesítésszolgáltatói tanúsítvány beszerzése
* Az IoT-központ X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása
* Csatlakozás a X.509 CA-tanúsítványok használatával
* X.509 hitelesítésszolgáltató aláírt eszközök hitelesítésének módját

## <a name="overview"></a>Áttekintés

Az X.509 CA-szolgáltatás lehetővé teszi, hogy a eszközhitelesítés az IoT-központ tanúsítványa (CA) segítségével. Ez jelentősen egyszerűbb kezdeti eszköz regisztrációs folyamatának és ellátási lánc logisztikai eszköz gyártási során. [További információ: a forgatókönyv cikk X.509 CA-tanúsítványok használatával kapcsolatos értéke](iot-hub-x509ca-concept.md) eszközhitelesítési.  Javasoljuk, hogy a cikkből forgatókönyv folytatása előtt, ismerteti, miért a következő lépések létezik.

## <a name="prerequisite"></a>Előfeltétel

Az X.509 hitelesítésszolgáltató funkciójával megköveteli, hogy Ön egy IoT-központ fiókot.  [Megtudhatja, hogyan hozzon létre egy IoT-központ példányt](iot-hub-csharp-csharp-getstarted.md) Ha még nem rendelkezik.

## <a name="how-to-get-an-x509-ca-certificate"></a>Egy X.509 Hitelesítésszolgáltatói tanúsítvány beszerzése

Az X.509 Hitelesítésszolgáltatói tanúsítvány szerepel a szabálylista tetején a lánc minden eszköz.  Előfordulhat, hogy vásároljon, vagy hozzon létre egyet, attól függően, hogy hogyan kívánja használni.

Az éles környezetben azt javasoljuk, hogy vásároljon egy X.509 Hitelesítésszolgáltatói tanúsítvány egy nyilvános legfelső szintű hitelesítésszolgáltatót. CA-tanúsítvány beszerzése az előnye, hogy a legfelső szintű hitelesítésszolgáltató van s ők felelnek a érvényességét az eszközök számára megbízható harmadik fél működött. Vegye figyelembe ezt a beállítást, ha tervezi az eszközök egy nyissa meg az IoT-hálózat részét ahol várható azokat az külső termékek vagy szolgáltatások kommunikál.

Lezárt IoT-hálózatok is létrehozhat egy önaláírt X.509 hitelesítésszolgáltató kísérletezhet vagy a használatra.

Hogyan a hitelesítésszolgáltató X.509-tanúsítvány beszerzése, ügyeljen arra, hogy tartsa meg megfelelő titkos kulcs titkos kulcs és védett függetlenül mindig.  Erre akkor szükség, az X.509 hitelesítésszolgáltató hitelesítés bizalmi kapcsolat létrehozása a megbízhatósági kapcsolatban. 

Megtudhatja, hogyan [hitelesítésszolgáltató önaláírt tanúsítvány létrehozása](iot-hub-security-x509-create-certificates.md#createcerts), amelyek kísérleti során ez a szolgáltatás leírása használható.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>A tanúsítvány megbízhatósági lánc a bejelentkezési eszközök

Egy X.509 Hitelesítésszolgáltatói tanúsítvány tulajdonosának kriptográfiai bejelentkezhet egy közbenső hitelesítésszolgáltató, akik viszont bejelentkezhet egy másik közbenső hitelesítésszolgáltató, és stb csak a legutóbbi köztes Hitelesítésszolgáltatói megszakítja a folyamatot eszköz aláírásával. Kaszkádolt lánc, egy megbízhatósági láncot néven jön létre. A valós életben ez játszik bizalmi felé eszközök aláíró delegálás. A delegálás fontos, mert felügyeleti lánc kriptográfiai változó láncot hoz létre, és elkerülhető az aláíró kulcsok megosztásának.

![img-Generic-CERT-CHAIN-of-Trust](./media/generic-cert-chain-of-trust.png)

Itt megtudhatja, hogyan [hozzon létre olyan tanúsítványlánccal](iot-hub-security-x509-create-certificates.md#createcertchain) mint eszközök aláírásakor.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Az IoT-központ X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása

Az IoT-központot, amelyeken azt kell használni az eszközök során regisztráció és a kapcsolat hitelesítésére X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása  A X.509 CA-tanúsítvány regisztrálása egy kétlépéses folyamat, amely magában foglalja a tanúsítvány-fájl feltöltése és adatok.

A feltöltési folyamat terjed ki, egy feltöltés, a tanúsítványt.  Ez a fájl soha nem kell tartalmaznia a bármely titkos kulcshoz.

Az adott lépés igazolása magában foglalja a egy kriptográfiai kérdés és válasz folyamat, és az IoT-központ között.  Fényében, hogy digitális tanúsítvány tartalma nyilvános, és ezért ki vannak téve a lehallgatást, az IoT-központ szeretné megállapítani, hogy valóban saját a Hitelesítésszolgáltatói tanúsítvány.  Erre a létrehozott egy véletlenszerű kérdéssel, hogy be kell jelentkeznie a CA-tanúsítvány megfelelő titkos kulccsal.  Ha meghagyta a titkos kulcs titkos és védett korábbi, azt javasoljuk, akkor csak akkor lesz ismeretekkel a lépés befejezéséhez. A titkos kulcsok titoktartási egy a megbízhatóság az ezzel a módszerrel.  Bejelentkezés a kérdés, után végezze el ezt a lépést az eredményeket tartalmazó fájl feltöltésével.

Itt megtudhatja, hogyan [a CA-tanúsítvány regisztrálása](iot-hub-security-x509-get-started.md#registercerts).

## <a name="how-to-create-a-device-on-iot-hub"></a>Egy eszközt az IoT hub létrehozása

Eszköz megszemélyesítési zárja ki, hogy az IoT-központ szükséges tudassa vele várhatnak, milyen eszközöket.  Ehhez az IoT Hub eszköz beállításjegyzék eszköz tételt hoz létre.  Ez a folyamat automatikus IoT-központ használatakor [eszköz kiépítése szolgáltatáshoz](https://azure.microsoft.com/en-us/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) (terjesztési pontok). 

Itt megtudhatja, hogyan [hozzon létre manuálisan egy eszközt az IoT-központ](iot-hub-security-x509-get-started.md#createdevice).

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Eszközök hitelesítése aláírva, X.509 CA-tanúsítványok

A regisztrált X.509 Hitelesítésszolgáltatói tanúsítvány és eszközökkel megbízható tanúsítványlánc be van jelentkezve mi marad esetén eszközhitelesítés az eszköz csatlakozik, akár az első alkalommal.  Egy X.509 hitelesítésszolgáltató aláírt eszköz csatlakozik, a tanúsítványlánc érvényesítése a feltölti azt. A lánc minden közbenső hitelesítésszolgáltató és eszköz tanúsítványokat tartalmaz.  Az információ az IoT-központ hitelesíti az eszközt egy kétlépéses folyamat.  Az IoT-központ kriptográfiai érvényesíti a tanúsítványlánc belső konzisztencia, majd állít ki az igazolás a birtokában kihívást az eszközre.  Az IoT-központ deklarálja az eszköz hiteles egy sikeres igazolása a birtokában a válasz az eszközről.  Ez a nyilatkozat feltételezi, hogy az eszköz titkos kulccsal védett, és hogy csak az eszköz sikeresen képes legyen ez a kérdés.  Biztonságos modulok például a hardveres biztonsági modulok (HSM) a titkos kulcsok védelme eszközök használatát javasoljuk.

IoT Hub sikeres csatlakozás végrehajtja a hitelesítési folyamatot, és akkor is, a megfelelő beállítás.

Itt megtudhatja, hogyan [eszköz kapcsolat lépés](iot-hub-security-x509-get-started.md#authenticatedevice).

## <a name="next-steps"></a>Következő lépések

További tudnivalók [X.509 hitelesítésszolgáltató hitelesítési értékének](iot-hub-x509ca-concept.md) az IoT.

Ismerkedés az IoT-központ [eszköz kiépítése szolgáltatáshoz](https://docs.microsoft.com/en-us/azure/iot-dps/).