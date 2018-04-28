---
title: Igazolása-az-birtokában X.509 Hitelesítésszolgáltatói tanúsítványokat, amelyek Azure IoT Hub eszköz kiépítése szolgáltatáshoz tartozó módjáról |} Microsoft Docs
description: X.509 Hitelesítésszolgáltatói tanúsítványokat, amelyek a terjesztési pontok szolgáltatás ellenőrzése
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: eb37ce7e61796494be0a9282afdc620b0ca5886a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Útmutatás igazolása-az-birtokában az X.509 CA tanúsítványaiban az eszköz kiépítése szolgáltatáshoz

Egy ellenőrzött és érvényes X.509 tanúsítvány (CA) tanúsítvány feltöltve, és a kiépítés regisztrálva a Hitelesítésszolgáltatói tanúsítvány szolgáltatást, és keresztülment igazolása-az-birtokában a szolgáltatással. 

Igazolás a birtokában a következő lépésekből áll:
1. A hitelesítésszolgáltató X.509-tanúsítvány a létesítési szolgáltatás által létrehozott egyedi ellenőrző kód kérése. Ehhez az Azure portálról.
2. Hozzon létre egy X.509 ellenőrző tanúsítvány a megerősítési kódot, mint a tulajdonos, és aláírja a tanúsítványt a X.509 CA tanúsítvány a titkos kulccsal.
3. Töltse fel az aláírt ellenőrző tanúsítvány a szolgáltatáshoz. A szolgáltatás ellenőrzi az ellenőrző tanúsítvány, a Hitelesítésszolgáltatói tanúsítvány nyilvános részének használatával ellenőrizhető, így igazolja, hogy Ön a CA-tanúsítvány titkos kulcs birtokában lévő.

Ellenőrzött tanúsítványok fontos szerepet játszanak beléptetési csoportok használata esetén. További biztonsági réteget biztosításával, amely a tanúsítvány feltöltése a tanúsítvány titkos kulcs birtokában lévő tanúsítvány tulajdonjogának ellenőrzéséről biztosít. Ellenőrzési megakadályozza, hogy egy rosszindulatú szereplő elemzés egy köztes tanúsítvány kinyeréséhez, valamint a tanúsítvány használatával beléptetési-csoport létrehozása a saját kiépítési szolgáltatásban, a forgalom hatékony térít az eszközök. Által tulajdonjogát, a legfelső szintű vagy köztes tanúsítvány egy tanúsítványlánc igazolására, akkor most meggyőződhet, hogy Ön jogosult az eszközöket, amelyek fogja a beléptetési csoport részeként kell regisztrálása levél tanúsítványainak előállításához. Ez az OK, a legfelső szintű vagy köztes szintű beléptetési csoportban konfigurált kell lennie egy ellenőrzött és érvényes tanúsítványt, vagy kell egy ellenőrzött és érvényes tanúsítvány a tanúsítványlánc egy eszköz összesítésben mutatja be, amikor a szolgáltatást. Beléptetési csoportokkal kapcsolatos további tudnivalókért lásd: [X.509-tanúsítványokat](concepts-security.md#x509-certificates) és [a létesítési szolgáltatás X.509 tanúsítvánnyal rendelkező eszközök hozzáférés szabályozása](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Egy X.509 tanúsítvány nyilvános részét regisztrálja, és az ellenőrző kód kérése

CA-tanúsítvány regisztrálása az üzembe helyezési szolgáltatással, és egy megerősítési kódot, amely során az igazolás a birtokában, kövesse az alábbi lépéseket. 

1. Az Azure-portálon keresse meg a létesítési szolgáltatás, és nyissa meg a **tanúsítványok** a bal oldali menüből. 
2. Kattintson a **Hozzáadás** hozzáadása egy új tanúsítványt.
3. Adja meg a tanúsítvány egy jól hangzó megjelenítési nevet. Keresse meg azt a .cer vagy a .pem fájl, amely jelöli az X.509 tanúsítvány nyilvános részét. Kattintson a **Feltöltés** gombra.
4. Ha a tanúsítvány feltöltése sikerült értesítést kap, kattintson **mentése**.

    ![Tanúsítvány feltöltése](./media/how-to-verify-certificates/add-new-cert.png)  

   A tanúsítvány fogja megjeleníteni a **tanúsítvány Explorer** listája. Vegye figyelembe, hogy a **állapot** ezt a tanúsítványt az *tartalomszűrő*.

5. Kattintson az előző lépésben felvett tanúsítványban.

6. A **Tanúsítványadatok**, kattintson a **ellenőrző kód generálása**.

7. A létesítési szolgáltatás hoz létre egy **ellenőrzőkódot** , hogy segítségével ellenőrizheti azokat a tanúsítvány tulajdonosa. A kódot másolja a vágólapra. 

   ![Tanúsítvány ellenőrzése](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Az ellenőrző kódot e hozzon létre egy ellenőrző tanúsítvány digitális aláírása

Most, be kell jelentkeznie a *ellenőrzőkódot* a titkos kulccsal, a hitelesítésszolgáltató X.509 tanúsítvány, amely állít elő egy aláírás. Ez más néven [adatok](https://tools.ietf.org/html/rfc5280#section-3.1) és egy aláírt ellenőrző tanúsítvány eredményez.

Microsoft eszközöket biztosít, és minták, amelyek segítségével hozzon létre egy aláírt ellenőrző tanúsítvány: 

- A **Azure IoT Hub C SDK** PowerShell (Windows) és (Linux) Bash parancsfájlok fejlesztésére hitelesítésszolgáltató és a levél tanúsítványok létrehozásához, valamint igazolása-az-birtokában ellenőrző kód használata végrehajtásához. Letöltheti a [fájlok](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) a rendszerben egy munkakönyvtárhoz és kövesse az utasításokat a a [kezelése Hitelesítésszolgáltatói tanúsítványok információs](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) igazolása a birtokában a végrehajtásához a Hitelesítésszolgáltatói tanúsítványra. 
- A **Azure IoT Hub C# SDK** tartalmazza a [csoport tanúsítvány ellenőrzési minta](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples/GroupCertificateVerificationSample), amely igazolása a birtokában ehhez használhatja.
- A lépéseket követheti a [PowerShell-parancsfájlokkal kezelheti a hitelesítésszolgáltató által aláírt X.509-tanúsítványokat](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates) az IoT Hub-dokumentáció kifejezetten a parancsfájl című részben a cikk [adatok a X.509 Hitelesítésszolgáltatói tanúsítvány](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates#signverificationcode).
 
> [!IMPORTANT]
> Mellett igazolása a birtokában, a PowerShell és a Bash parancsfájlok idézett korábban is engedélyezi, hogy ellátja az eszközöket és hitelesítéséhez használható tanúsítványok levél, legfelső szintű tanúsítványok és köztes tanúsítványokat létrehozni. Ezek a tanúsítványok csak fejlesztési használható. Akkor kell soha nem használható éles környezetben. 

A PowerShell és a Bash parancsfájlok a dokumentációt, mind az SDK-k támaszkodnak [OpenSSL](https://www.openssl.org/). Is használhatja OpenSSL vagy más külső gyártású eszközöknek is segítséget nyújthat az igazolás a birtokában. Az SDK-k megadott tooling kapcsolatos további információkért lásd: [használata az SDK-ban biztosított eszközökkel](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Töltse fel az aláírt ellenőrző tanúsítvány

1. Az eredményül kapott aláírás, a portál a létesítési szolgáltatás ellenőrző tanúsítvány feltöltése A **Tanúsítványadatok** az Azure portál használata az _Fájlkezelőben_ melletti ikon a **ellenőrző tanúsítvány .pem vagy .cer fájl** töltse fel az aláírt mező ellenőrző tanúsítvány a rendszerből.

2. Ha a tanúsítvány sikeresen feltöltődött, kattintson **ellenőrizze**. A **állapot** a tanúsítvány módosításait **_ellenőrizve_** a a **tanúsítvány Explorer** listája. Kattintson a **frissítése** Ha nem frissíti automatikusan.

   ![Ellenőrző tanúsítvány feltöltése](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>További lépések

- Regisztráció-csoport létrehozása a portál használatával kapcsolatos további tudnivalókért lásd: [kezelése az Azure-portálon eszközbeléptetésnél](how-to-manage-enrollments.md).
- Beléptetési csoport létrehozásához a szolgáltatás SDK-k használatával kapcsolatos további tudnivalókért lásd: [eszközbeléptetésnél a szolgáltatás SDK-k kezelése](how-to-manage-enrollments-sdks.md).










