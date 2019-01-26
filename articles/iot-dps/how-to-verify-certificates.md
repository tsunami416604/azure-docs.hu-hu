---
title: A koncepció-az-birtokában x.509-es Hitelesítésszolgáltatói tanúsítványok az Azure IoT Hub Device Provisioning Service módjáról |} A Microsoft Docs
description: A Device Provisioning Service szolgáltatással x.509-es Hitelesítésszolgáltatói tanúsítványok ellenőrzése
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: afa4b3861e9fb7f91fd9f5d540353c5fad23efe0
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913614"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Hogyan teheti a koncepció-az-birtokában x.509-es Hitelesítésszolgáltatói tanúsítványok a Device Provisioning Service szolgáltatással

Egy ellenőrzött X.509 hitelesítésszolgáltató (CA) tanúsítvány egy Hitelesítésszolgáltatói tanúsítvány feltöltve és regisztrálva a kiépítési szolgáltatást, és a koncepció-az-birtokában a szolgáltatással keresztülment. 

A koncepció-az-birtokában az alábbi lépésekből áll:
1. Az x.509-es Hitelesítésszolgáltatói tanúsítvány az eszközkiépítési szolgáltatás által létrehozott egyedi ellenőrző kód kérése. Ez az Azure Portalon teheti meg.
2. Hozzon létre egy X.509-hitelesítési tanúsítvány az Ellenőrzőkód, mint a tulajdonos, és aláírja a tanúsítványt az x.509-es Hitelesítésszolgáltatói tanúsítványhoz tartozó titkos kulccsal.
3. Töltse fel az aláírt ellenőrző tanúsítvány a szolgáltatást. A szolgáltatás ellenőrzi az ellenőrző tanúsítványt a CA-tanúsítvány nyilvános részét segítségével ellenőrizhető, így igazolja, hogy a Hitelesítésszolgáltatói tanúsítvány titkos kulcs birtokában van.

Ellenőrzött tanúsítványok fontos szerepet játszik a regisztrációs csoportok használata esetén. Egy további biztonsági réteg biztosításával, hogy a tanúsítvány titkos kulcs birtokában van a tanúsítvány a feltöltő ellenőrzése a tanúsítvány tulajdonosa biztosítja. Ellenőrzési megakadályozza, hogy egy rosszindulatú aktor elemző kibontása egy közbenső tanúsítvány és a egy regisztrációs csoportot hozhat létre saját kiépítési szolgáltatás az adott tanúsítvány használatával a forgalom hatékony térít az eszközök. Által tulajdonjogát a legfelső szintű vagy egy tanúsítványlánc egy közbenső tanúsítvány igazolja, akkor még igazolására, hogy az eszközök, amelyeket a regisztrációs csoport részeként lesz kell regisztrálni a levél tanúsítványainak létrehozásához szükséges engedéllyel. Ebből az okból, a legfelső szintű vagy köztes tanúsítványt egy regisztrációs csoportnak konfigurálva kell lennie, ellenőrzött tanúsítványt, vagy kell egy ellenőrzött tanúsítványt a tanúsítványlánc a eszközt összesítésben mutatja be, amikor a szolgáltatást. Regisztrációs csoportok kapcsolatos további információkért lásd: [X.509 tanúsítványok](concepts-security.md#x509-certificates) és [szabályozása X.509-tanúsítványokat az eszközkiépítési szolgáltatás elérése](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Egy X.509 tanúsítvány nyilvános részét regisztrálása és az ellenőrző kód kérése

Hitelesítésszolgáltatói tanúsítvány regisztrálása a kiépítési szolgáltatáshoz, és egy ellenőrző kódot, amely során a koncepció-az-birtokában beolvasása, kövesse az alábbi lépéseket. 

1. Az Azure Portalon lépjen a kiépítési szolgáltatáshoz, és nyissa meg **tanúsítványok** elemet a bal oldali menüben. 
2. Kattintson a **Hozzáadás** egy új tanúsítványt.
3. Adja meg a tanúsítvány rövid megjelenített neve. Keresse meg a .cer vagy .pem fájlt, amely az X.509-tanúsítvány nyilvános részét jelöli. Kattintson a **Feltöltés** gombra.
4. Miután a tanúsítvány feltöltése sikerült értesítést kap, kattintson a **mentése**.

    ![Tanúsítvány feltöltése](./media/how-to-verify-certificates/add-new-cert.png)  

   A tanúsítvány fogja megjeleníteni a **Tanúsítványkezelő** listája. Vegye figyelembe, hogy a **állapot** , de ez a tanúsítvány *ellenőrzött*.

5. Kattintson az előző lépésben hozzáadott a tanúsítványt.

6. A **Tanúsítványadatok**, kattintson a **ellenőrző kód előállítása**.

7. A kiépítési szolgáltatás létrehoz egy **ellenőrzőkódot** használható ellenőrzése a tanúsítvány tulajdonosa. Másolja a vágólapra a kódot. 

   ![Tanúsítvány ellenőrzése](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Ellenőrző kód létrehozása egy ellenőrző tanúsítvány digitális aláírása

Most meg kell bejelentkeznie a *ellenőrzőkódot* az x.509-es Hitelesítésszolgáltatói tanúsítványhoz tartozó titkos kulccsal, amely létrehoz egy aláírás. Ez az úgynevezett [birtokában megvalósíthatósági](https://tools.ietf.org/html/rfc5280#section-3.1) és a egy aláírt ellenőrző tanúsítvány eredményez.

Microsoft eszközöket biztosít, és a mintákat, amelyek segítségével hozzon létre egy aláírt ellenőrző tanúsítvány: 

- A **Azure IoT Hub C SDK** PowerShell (Windows) és (Linux) Bash-szkriptek segítségével hozhat létre a hitelesítésszolgáltató és a levél tanúsítványok fejlesztési és végrehajtásához igazolás-az-birtokában használatával egy ellenőrző kódot. Letöltheti a [fájlok](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) a rendszerben mappába működő, és kövesse a a [kezelése Hitelesítésszolgáltatói tanúsítványok információs](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) végrehajtásához igazolás a birtokában, Hitelesítésszolgáltatói tanúsítvány. 
- A **Azure IoT Hub C# SDK** tartalmazza a [minta](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), amelyet ehhez a koncepció-az-birtokában használhat.
 
> [!IMPORTANT]
> A koncepció-az-birtokában mellett, a PowerShell és a Bash parancsfájlok csatornamegerősítése korábban is lehetővé teszik eszközök kiépítése és hitelesítéséhez használható tanúsítványok levél, legfelső szintű tanúsítványok és köztes tanúsítványok létrehozásához. Ezeket a tanúsítványokat csak fejlesztési használandó. Azok soha nem használható éles környezetben. 

A PowerShell és a Bash parancsfájlok, dokumentáció és SDK-k a megadott támaszkodjon [OpenSSL](https://www.openssl.org/). OpenSSL vagy más külső eszközök használatával is segítséget nyújthat a koncepció-az-birtokában. Az SDK-k a megadott eszközök kapcsolatos további információkért lásd: [az SDK-k a megadott eszközei használatával miként](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Töltse fel az aláírt ellenőrző tanúsítvány

1. Töltse fel az eredményül kapott aláírást a portálon az eszközkiépítési szolgáltatás hitelesítési tanúsítványként. A **Tanúsítványadatok** az Azure Portal használata az _fájlkezelő_ melletti ikon a **ellenőrző tanúsítvány .pem or .cer fájlja** mezőt, töltse fel az aláírt ellenőrző tanúsítvány a rendszerről.

2. Miután sikeresen feltöltötte a tanúsítványt, kattintson a **ellenőrizze**. A **állapot** a tanúsítvány módosításainak **_ellenőrzött_** a a **Tanúsítványkezelő** listája. Kattintson a **frissítése** Ha nem frissíti automatikusan.

   ![Töltse fel a tanúsítvány-ellenőrzés](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>További lépések

- Egy regisztrációs csoport létrehozása a portál használatával kapcsolatos tudnivalókért lásd: [az Azure portal-eszközök beléptetésének kezelése](how-to-manage-enrollments.md).
- Hozzon létre egy regisztrációs csoport a szolgáltatási SDK-k használatával kapcsolatos további információkért lásd: [szolgáltatási SDK-k eszközök beléptetésének kezelése](how-to-manage-enrollments-sdks.md).










