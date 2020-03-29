---
title: X.509 hitelesítésszolgáltatói tanúsítványok ellenőrzése az Azure IoT Hub-eszközkiépítési szolgáltatással
description: Az X.509 hitelesítésszolgáltatói tanúsítványok birtoklásigazolása az Azure IoT Hub-eszközkiépítési szolgáltatással (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b008c4ebc83200043d51fc8ef367f1983c549949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973441"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Az X.509 hitelesítésszolgáltatói tanúsítványok birtoklásigazolása az Eszközkiépítési szolgáltatással

Az ellenőrzött X.509 hitelesítésszolgáltatói tanúsítvány olyan hitelesítésszolgáltatói tanúsítvány, amelyet feltöltöttek és regisztráltak a létesítési szolgáltatásba, és amely a szolgáltatás birtokában keresztül igazolta. 

A birtoklás igazolása a következő lépéseket foglalja magában:
1. Az X.509 hitelesítésszolgáltatói tanúsítvány létesítési szolgáltatása által létrehozott egyedi ellenőrző kódot kaphat. Ezt az Azure Portalon teheti meg.
2. Hozzon létre egy X.509-es hitelesítési tanúsítványt, amelynek tárgya az ellenőrző kód, és írja alá a tanúsítványt az X.509 hitelesítésszolgáltatói tanúsítványhoz társított személyes kulccsal.
3. Töltse fel az aláírt igazolási tanúsítványt a szolgáltatásba. A szolgáltatás ellenőrzi az ellenőrző tanúsítványt a hitelesítésszolgáltatói tanúsítvány ellenőrizendő nyilvános részével, így bizonyítva, hogy Ön rendelkezik a hitelesítésszolgáltatói tanúsítvány személyes kulcsával.

Az ellenőrzött tanúsítványok fontos szerepet játszanak a regisztrációs csoportok használatakor. A tanúsítvány tulajdonjogának ellenőrzése további biztonsági réteget biztosít annak biztosításával, hogy a tanúsítvány feltöltője a tanúsítvány személyes kulcsának birtokában legyen. Az ellenőrzés megakadályozza, hogy egy rosszindulatú aktor megszagolja a forgalmat egy köztes tanúsítvány kinyerése, és használja a tanúsítványt, hogy hozzon létre egy regisztrációs csoportot a saját létesítési szolgáltatás, hatékonyan eltérítése az eszközök. A legfelső szintű tanúsítványláncban lévő gyökér vagy köztes tanúsítvány tulajdonjogának igazolásával bizonyítja, hogy rendelkezik engedéllyel levéltanúsítványok létrehozására az adott regisztrációs csoport részeként regisztráló eszközökszámára. Ezért a regisztrációs csoportban konfigurált legfelső szintű vagy köztes tanúsítványnak vagy ellenőrzött tanúsítványnak kell lennie, vagy egy ellenőrzött tanúsítványhoz kell hozzátolnia az eszköz által a szolgáltatással való hitelesítéskor megadott tanúsítványláncban. A regisztrációs csoportokról az [X.509-tanúsítványok](concepts-security.md#x509-certificates) és [az Eszköz hozzáférésének szabályozása x.509-es tanúsítványokkal című témakörben olvashat bővebben.](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>X.509-es tanúsítvány nyilvános részének regisztrálása és ellenőrző kód beszerezése

Ha regisztrálni szeretne egy hitelesítésszolgáltatói tanúsítványt a kiépítési szolgáltatásban, és be szeretne szerezni egy ellenőrző kódot, amelyet a birtoklás igazolása során használhat, kövesse az alábbi lépéseket. 

1. Az Azure Portalon keresse meg a kiépítési szolgáltatást, és nyissa meg a **tanúsítványokat** a bal oldali menüből. 
2. Új tanúsítvány hozzáadásához kattintson a **Hozzáadás** gombra.
3. Adja meg a tanúsítvány rövid megjelenítendő nevét. Tallózással keresse meg az X.509 tanúsítvány nyilvános részét képviselő .cer vagy .pem fájlt. Kattintson a **Feltöltés** gombra.
4. Miután értesítést kap a tanúsítvány sikeres feltöltésről, kattintson a **Mentés gombra.**

    ![Tanúsítvány feltöltése](./media/how-to-verify-certificates/add-new-cert.png)  

   A tanúsítvány megjelenik a **Tanúsítványkezelő** listában. Ne **feledje,** hogy a tanúsítvány állapota *nem ellenőrzött.*

5. Kattintson az előző lépésben hozzáadott tanúsítványra.

6. A **Tanúsítvány részletei csoportban**kattintson **az Ellenőrző kód létrehozása gombra.**

7. A létesítési szolgáltatás létrehoz egy **ellenőrző kódot,** amely segítségével érvényesítheti a tanúsítvány tulajdonjogát. Másolja a kódot a vágólapra. 

   ![Tanúsítvány ellenőrzése](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Az ellenőrző kód digitális aláírása az ellenőrző tanúsítvány létrehozásához

Most alá kell *írnia* az ellenőrző kódot az X.509 hitelesítésszolgáltatói tanúsítványhoz társított személyes kulccsal, amely aláírást hoz létre. Ez az úgynevezett [bizonyíték a birtoklásés](https://tools.ietf.org/html/rfc5280#section-3.1) az eredmények egy aláírt igazolást.

A Microsoft olyan eszközöket és mintákat biztosít, amelyek segítségével létrehozhat egy aláírt ellenőrző tanúsítványt: 

- Az **Azure IoT Hub C SDK** PowerShell (Windows) és Bash (Linux) parancsfájlok segítségével hozzon létre hitelesítésszolgáltatói és levéltanúsítványokat a fejlesztéshez, és ellenőrző kód használatával végezze el a birtoklás igazolását. A rendszerre vonatkozó [fájlokat](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) letöltheti egy munkamappába, és a [hitelesítésszolgáltatói tanúsítványok kezelése című dokumentumban](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) található utasításokat követve a hitelesítésszolgáltatói tanúsítvány birtoklásigazolását végezheti el. 
- Az **Azure IoT Hub C# SDK** tartalmazza a [csoporttanúsítvány-ellenőrzési minta,](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)amely segítségével a birtoklás igazolása.
 
> [!IMPORTANT]
> A birtoklás igazolása mellett a korábban hivatkozott PowerShell- és Bash-parancsfájlok is lehetővé teszik, hogy főtanúsítványokat, köztes tanúsítványokat és levéltanúsítványokat hozzon létre, amelyek az eszközök hitelesítésére és üzembe létesítésére használhatók. Ezeket a tanúsítványokat csak fejlesztési célokra szabad használni. Ezeket soha nem szabad éles környezetben használni. 

A dokumentációban és az SDK-kban található PowerShell- és Bash-parancsfájlok az [OpenSSL-re](https://www.openssl.org/)támaszkodnak. Használhatja az OpenSSL-t vagy más külső eszközöket is, hogy segítsen a birtoklás igazolásában. Az SDK-k hoz biztosított eszközökhasználatáról [az SDK-kban található eszközök használata](how-to-use-sdk-tools.md)című témakörben talál további információt. 


## <a name="upload-the-signed-verification-certificate"></a>Az aláírt hitelesítési tanúsítvány feltöltése

1. Töltse fel az eredményül kapott aláírást ellenőrzési tanúsítványként a portálon a kiépítési szolgáltatásba. Az Azure Portal tanúsítvány részletei című **dokumentumában** az **Ellenőrzési tanúsítvány .pem vagy .cer fájlmező** melletti _Fájlkezelő_ ikonnal töltse fel az aláírt ellenőrzési tanúsítványt a rendszerből.

2. A tanúsítvány sikeres feltöltése után kattintson az **Ellenőrzés gombra.** A tanúsítvány **állapota** **_ellenőrzöttre_** változik a **Tanúsítványkezelő** listában. Kattintson a **Frissítés** gombra, ha nem frissül automatikusan.

   ![Tanúsítvány-ellenőrzés feltöltése](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>További lépések

- Ha többet szeretne tudni arról, hogy miként hozhat létre a portált egy regisztrációs csoport létrehozásához, olvassa [el az Eszközregisztrációk kezelése az Azure Portalon című témakört.](how-to-manage-enrollments.md)
- Ha többet szeretne tudni arról, hogy miként használhatja a szolgáltatás SDK-kat egy regisztrációs csoport létrehozásához, olvassa el [az Eszközregisztrációk kezelése szolgáltatásokkal SDK-kkal című témakört.](how-to-manage-enrollments-sdks.md)










