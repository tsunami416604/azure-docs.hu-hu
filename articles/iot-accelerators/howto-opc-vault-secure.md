---
title: Az OPC UA-eszközök kommunikációjának biztonságossá tétele az OPC-tárolóval – Azure | Microsoft Docs
description: OPC UA-alkalmazások regisztrálása és az OPC UA-eszközökhöz tartozó aláírt alkalmazás-tanúsítványok kiállítása az OPC-tárolóval.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae10bb3488c21b6637163e333231e95a18c652bf
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996132"
---
# <a name="how-to-use-the-opc-vault-certificate-management-service"></a>Az OPC Vault Certificate Management szolgáltatás használata

Ez a cikk azt ismerteti, hogyan regisztrálhatók az alkalmazások, és hogyan adhatók hozzá aláírt alkalmazások tanúsítványainak az OPC UA-eszközökhöz.

## <a name="prerequisites"></a>Előfeltételek

### <a name="deploy-the-certificate-management-service"></a>A Tanúsítványkezelő szolgáltatás üzembe helyezése

Először is a szolgáltatást üzembe kell helyezni az Azure-felhőben.
Talál egy cikket, amely leírja, hogyan kell [telepíteni az OPC Vault tanúsítványkezelő szolgáltatást](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>A kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozása

Ha még nem tette meg, hozza létre a kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát.

[Az OPC-tár kiállítói tanúsítványának létrehozását és kezelését](howto-opc-vault-manage.md)ismertető cikk.

## <a name="secure-opc-ua-applications"></a>Biztonságos OPC UA-alkalmazások

### <a name="step-1-register-your-opc-ua-application"></a>1\. lépés: Az OPC UA-alkalmazás regisztrálása 

> [!IMPORTANT]
> Egy alkalmazás regisztrálásához az "író" szerepkör szükséges.

1. Nyissa meg a tanúsítványszolgáltatásokat `https://myResourceGroup-app.azurewebsites.net` , és jelentkezzen be.
2. Navigáljon a `Register New` laphoz.
1. Egy alkalmazás regisztrálásához a felhasználónak legalább a "Writer" szerepkört hozzá kell rendelnie.
2. A nevezési űrlap az OPC UA-világ elnevezési konvencióit követi. Példaként tekintse meg az [OPC ua Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) -minta BEÁLLÍTÁSAIT az OPC ua-ben. A NetStandard stack a következőképpen jelenik meg:

   ![Ua-hivatkozási kiszolgáló regisztrálása](media/howto-opc-vault-secure/reference-server-registration.png "Ua-hivatkozási kiszolgáló regisztrálása")

5. `Register` A gombra kattintva regisztrálja az alkalmazást a Certificate Service Application adatbázisban. A munkafolyamat közvetlenül a következő lépéssel irányítja a felhasználót, hogy aláírt tanúsítványt kérjen az alkalmazáshoz.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>2\. lépés: Az alkalmazás biztonságossá tétele HITELESÍTÉSSZOLGÁLTATÓI aláírású alkalmazás tanúsítványával

Az OPC UA-alkalmazások biztonságossá tételéhez tanúsítvány-aláírási kérelem (CSR) alapján, vagy új kulcspár igénylésével, amely egy új titkos kulcsot is tartalmaz PFX-vagy PEM-formátumban. Kövesse az OPC UA-eszköz dokumentációját, amelyen az alkalmazás által támogatott módszer található. Általánosságban elmondható, hogy a CSR-módszer használata ajánlott, mert nincs szükség arra, hogy a titkos kulcs átvitele a hálózaton keresztül történjen.

- Ha az eszközön új kulcspárt kell kiadnia, kövesse a [Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair).
- Ha az eszköz támogatja a tanúsítvány-aláírási kérelem (CSR) kiállítását, kövesse a [Step3b](#step-3b-request-a-new-certificate-with-a-csr).

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>3a. lépés: Új tanúsítvány igénylése új kulcspárt

1. Navigáljon a `Applications` laphoz.
3. Indítsa el a tanúsítványkérelem munkafolyamatát `New Request` egy listázott alkalmazás kiválasztásával.

   ![Új tanúsítvány igénylése](media/howto-opc-vault-secure/request-new-certificate.png "Új tanúsítvány igénylése")

3. Nyomja meg az "új kulcspár és tanúsítvány kérése" lehetőséget, hogy egy titkos kulcsot és egy új aláírt tanúsítványt kérjen az alkalmazás nyilvános kulcsával.

   ![Új kulcspár] előállítása (media/howto-opc-vault-secure/generate-new-key-pair.png "Új kulcspár") előállítása

4. Töltse ki az űrlapot a tulajdonossal, a tartományneveket, és válassza a PEM vagy a PFX jelszót a titkos kulcshoz. A tanúsítványkérelem `Generate New Certificate` létrehozásához kattintson a gombra.

   ![Tanúsítvány jóváhagyása](media/howto-opc-vault-secure/approve-reject.png "Tanúsítvány jóváhagyása")

5. A jóváhagyási lépéshez a "jóváhagyó" szerepkörrel rendelkező felhasználó és a Azure Key Vault aláírási engedélyei szükségesek. A tipikus munkafolyamatban a jóváhagyó és a kérelmező szerepkört különböző felhasználókhoz kell rendelni.
6. Hagyja jóvá vagy utasítsa el a tanúsítványkérelmet, hogy elindítsa vagy megszakítsa a kulcspár tényleges létrehozását és az aláírási műveletet. Az új kulcspár létrehozása és biztonságos tárolása Azure Key Vault, amíg a tanúsítvány kérelmezője le nem tölti. A HITELESÍTÉSSZOLGÁLTATÓ aláírja az eredményül kapott tanúsítványt a nyilvános kulccsal. Ezek a műveletek eltarthat néhány másodpercig a befejezésig.

   ![Kulcspár megtekintése](media/howto-opc-vault-secure/view-key-pair.png "Kulcspár megtekintése")

7. Az eredményül kapott titkos kulcs (PFX vagy PEM) és tanúsítvány (DER) innen tölthető le a bináris fájlok letöltése lehetőséggel kiválasztva. A Base64 kódolású verzió is elérhető, például a tanúsítvány parancssori vagy szöveges bejegyzésbe való beillesztésének másolásához. 
8. A titkos kulcs letöltése és biztonságos tárolása után törölheti a szolgáltatást `Delete Private Key` a gomb használatával. A nyilvános kulccsal rendelkező tanúsítvány jövőbeli használatra továbbra is elérhető marad.
9. A CA által aláírt tanúsítvány használata miatt a HITELESÍTÉSSZOLGÁLTATÓ tanúsítványát és a CRL-t is le kell tölteni.
10. Most az OPC UA-eszköztől függ, hogyan alkalmazza az új kulcspárt. A hitelesítésszolgáltató tanúsítványát és a CRL-t általában egy `trusted` mappába másolja a rendszer, az alkalmazás tanúsítványának nyilvános és titkos kulcsát pedig a `own` tanúsítványtároló egyik mappájára alkalmazza a rendszer. Előfordulhat, hogy egyes eszközök már támogatják a "kiszolgáló leküldését" a tanúsítványok frissítéseihez. Tekintse át az OPC UA-eszköz dokumentációját.

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>3b. lépés: Új tanúsítvány igénylése CSR-sel 

1. Navigáljon a `Applications` laphoz.
3. Indítsa el a tanúsítványkérelem munkafolyamatát `New Request` egy listázott alkalmazás kiválasztásával.

   ![Új tanúsítvány igénylése](media/howto-opc-vault-secure/request-new-certificate.png "Új tanúsítvány igénylése")

3. Az "új tanúsítvány igénylése aláírási kérelemmel" gomb megnyomásával új aláírt tanúsítványt igényelhet az alkalmazáshoz.

   ![Új tanúsítvány] előállítása (media/howto-opc-vault-secure/generate-new-certificate.png "Új tanúsítvány") előállítása

4. Töltse fel a CSR-t egy helyi fájl kiválasztásával, vagy egy Base64 kódolású CSR beillesztésével az űrlapon. A tanúsítványkérelem `Generate New Certificate` létrehozásához kattintson a gombra.

   ![CSR jóváhagyása](media/howto-opc-vault-secure/approve-reject-csr.png "CSR jóváhagyása")

5. A jóváhagyási lépéshez a "jóváhagyó" szerepkörrel rendelkező felhasználó és a Azure Key Vault aláírási engedélyei szükségesek. Hagyja jóvá vagy utasítsa el a tanúsítványkérelmet a tényleges aláírási művelet elindításához vagy megszakításához. A HITELESÍTÉSSZOLGÁLTATÓ aláírja az eredményül kapott tanúsítványt a nyilvános kulccsal. A művelet eltarthat néhány másodpercig.

   ![Tanúsítvány megtekintése](media/howto-opc-vault-secure/view-cert-csr.png "Tanúsítvány megtekintése")

6. Az eredményül kapott tanúsítvány (DER) innen tölthető le bináris fájlként. A Base64 kódolású verzió is elérhető, például a tanúsítvány parancssori vagy szöveges bejegyzésbe való beillesztésének másolásához. 
10. A tanúsítvány letöltését és biztonságos tárolása után törölheti a szolgáltatást `Delete Certificate` a gomb használatával.
11. A CA által aláírt tanúsítvány használata miatt a HITELESÍTÉSSZOLGÁLTATÓ tanúsítványát és a CRL-t is le kell tölteni.
12. Most az OPC UA-eszköztől függ, hogyan alkalmazza az új tanúsítványt. A hitelesítésszolgáltató tanúsítványát és a CRL-t általában egy `trusted` mappába másolja a rendszer, miközben az alkalmazás tanúsítványa `own` a tanúsítványtároló egyik mappájára van alkalmazva. Előfordulhat, hogy egyes eszközök már támogatják a "kiszolgáló leküldését" a tanúsítványok frissítéseihez. Tekintse át az OPC UA-eszköz dokumentációját.

### <a name="step-4-device-secured"></a>4\. lépés: Eszköz biztosítva

Az OPC UA-eszköz most már készen áll arra, hogy a CA által aláírt tanúsítványok által védett más OPC UA-eszközökkel tudjon kommunikálni további konfiguráció nélkül. Jó munkát!


## <a name="next-steps"></a>További lépések

Most, hogy megismerte az OPC UA-eszközök védelmét, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Biztonságos tanúsítványkezelő szolgáltatás futtatása](howto-opc-vault-secure-ca.md)