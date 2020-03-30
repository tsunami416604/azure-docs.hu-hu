---
title: Biztosítsa az OPC UA-eszközök kommunikációját az OPC Vault - Azure | Microsoft dokumentumok
description: Az OPC UA-alkalmazások regisztrálása és az OPC UA-eszközök aláírt alkalmazástanúsítványainak kiállítása az OPC Vault segítségével.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454190"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Az OPC Vault tanúsítványkezelő szolgáltatásának használata

Ez a cikk bemutatja, hogyan regisztrálhat alkalmazásokat, és hogyan állíthat ki aláírt alkalmazástanúsítványokat az OPC UA-eszközökhöz.

## <a name="prerequisites"></a>Előfeltételek

### <a name="deploy-the-certificate-management-service"></a>A tanúsítványkezelő szolgáltatás telepítése

Először telepítse a szolgáltatást az Azure-felhőbe. További információt [az OPC Vault tanúsítványkezelési szolgáltatás ának telepítése](howto-opc-vault-deploy.md)című témakörben talál.

### <a name="create-the-issuer-ca-certificate"></a>A kiállító hitelesítésszolgáltatói tanúsítvány létrehozása

Ha még nem tette meg, hozza létre a kiállító hitelesítésszolgáltatói tanúsítványt. További információt [az OPC Vault kiállítói tanúsítványának létrehozása és kezelése című témakörben talál.](howto-opc-vault-manage.md)

## <a name="secure-opc-ua-applications"></a>Biztonságos OPC UA alkalmazások

### <a name="step-1-register-your-opc-ua-application"></a>1. lépés: Regisztrálja az OPC UA alkalmazást 

> [!IMPORTANT]
> Az alkalmazás regisztrálásához a Writer szerepkör szükséges.

1. Nyissa meg a `https://myResourceGroup-app.azurewebsites.net`tanúsítványszolgáltatást a alkalmazásban, és jelentkezzen be.
2. Nyissa meg az **Új regisztrálás a ( Új ) regisztrálása**. Az alkalmazásregisztrációhoz a felhasználónak legalább a Writer szerepkört hozzá kell rendelnie.
2. A nevezési űrlap az OPC UA elnevezési konvencióit követi. A következő képernyőképen például az [OPC UA referenciakiszolgálóminta](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) beállításai láthatók az OPC UA .NET standard veremben:

   ![Képernyőkép az UA referenciakiszolgálójának regisztrációjáról](media/howto-opc-vault-secure/reference-server-registration.png "UA referenciakiszolgáló regisztrációja")

5. Válassza a **Regisztráció** lehetőséget az alkalmazás regisztrálásához a tanúsítványszolgáltatás-alkalmazás adatbázisában. A munkafolyamat közvetlenül irányítja a felhasználót a következő lépéshez, hogy aláírt tanúsítványt kérjen az alkalmazáshoz.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>2. lépés: Az alkalmazás biztonságossá tétele hitelesítésszolgáltató által aláírt alkalmazástanúsítvánnyal

Az OPC UA-alkalmazás biztonságossá tétele tanúsítványaláírási kérelemen (CSR) alapuló aláírt tanúsítvány kiállításával. Másik lehetőségként kérhet egy új kulcspárt, amely pfx vagy PEM formátumú új titkos kulcsot tartalmaz. Az alkalmazás támogatott módjáról az OPC UA-eszköz dokumentációjában olvashat. Általában a CSR módszer ajánlott, mert nem szükséges a személyes kulcsot átvinni egy vezetéken keresztül.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Új tanúsítvány igénylése új kulcspárral

1. Nyissa meg az **Alkalmazások**.
3. Válassza az **Új kérelem** lehetőséget egy felsorolt alkalmazáshoz.

   ![Képernyőkép: Új tanúsítvány kérése](media/howto-opc-vault-secure/request-new-certificate.png "Új tanúsítvány igénylése")

3. Válassza **az Új kulcspár és tanúsítvány kérése** lehetőséget, ha személyes kulcsot és új aláírt tanúsítványt szeretne kérni az alkalmazás nyilvános kulcsával.

   ![Képernyőkép: Új kulcspár és tanúsítvány létrehozása](media/howto-opc-vault-secure/generate-new-key-pair.png "Új kulcspár létrehozása")

4. Töltse ki az űrlapot egy tanusszal és a tartománynevekkel. A személyes kulcshoz válassza a PEM vagy a PFX jelszót. A tanúsítványkérelem létrehozásához válassza az **Új kulcspár létrehozása** lehetőséget.

   ![Képernyőkép a Tanúsítványkérelem részleteinek megtekintése](media/howto-opc-vault-secure/approve-reject.png "Tanúsítvány jóváhagyása")

5. A jóváhagyáshoz a Jóváhagyó szerepkörrel rendelkező és az Azure Key Vaultban aláíró engedélyekkel rendelkező felhasználóra van szükség. A tipikus munkafolyamatban a jóváhagyó és a kérelmező szerepköröket különböző felhasználókhoz kell rendelni. Válassza **a Jóváhagyás** vagy az **Elutasítás** lehetőséget a kulcspár és az aláírási művelet tényleges létrehozásának elindításához vagy megszakításához. Az új kulcspár jön létre, és biztonságosan tárolja az Azure Key Vault, amíg le nem töltötte a tanúsítvány kérelmezője. Az eredményül kapott tanúsítványt nyilvános kulc-mal a hitelesítésszolgáltató írja alá. Ezek a műveletek néhány másodpercig is eltarthatnak.

   ![Képernyőkép a Tanúsítványkérelem részleteinek megtekintése üzenetről, amelynek alján a jóváhagyási üzenet látható](media/howto-opc-vault-secure/view-key-pair.png "Kulcspár megtekintése")

7. Az így kapott titkos kulcs (PFX vagy PEM) és a tanúsítvány (DER) letölthető innen a kiválasztott formátumban bináris fájl letöltés. A base64 kódolású verzió is rendelkezésre áll, például a tanúsítvány másolásához és beillesztéséhez egy parancssorba vagy szövegbejegyzésbe. 
8. A személyes kulcs letöltése és biztonságos tárolása után válassza **a Titkos kulcs törlése**lehetőséget. A nyilvános kulccsal rendelkező tanúsítvány későbbi használatra továbbra is elérhető marad.
9. A hitelesítésszolgáltató által aláírt tanúsítvány használata miatt a hitelesítésszolgáltató tanúsítványát és a visszavont tanúsítványok listáját (CRL) is le kell tölteni.

Most attól függ, hogy az OPC UA eszköz hogyan kell alkalmazni az új kulcs pár. A hitelesítésszolgáltatói tanúsítvány és a visszavont tanúsítványok `trusted` listájá általában egy mappába kerülnek, míg az alkalmazástanúsítvány nyilvános és személyes kulcsai a tanúsítványtároló egyik `own` mappájára vonatkoznak. Előfordulhat, hogy egyes eszközök már támogatják a kiszolgáló tanúsítványfrissítési leküldéses leküldéses. Tekintse meg az OPC UA eszköz dokumentációját.

#### <a name="request-a-new-certificate-with-a-csr"></a>Új tanúsítvány kérése CSR-rel 

1. Nyissa meg az **Alkalmazások**.
3. Válassza az **Új kérelem** lehetőséget egy felsorolt alkalmazáshoz.

   ![Képernyőkép: Új tanúsítvány kérése](media/howto-opc-vault-secure/request-new-certificate.png "Új tanúsítvány igénylése")

3. Válassza **az Új tanúsítvány igénylése aláírási kérelemmel** lehetőséget, ha új aláírt tanúsítványt szeretne kérni az alkalmazáshoz.

   ![Képernyőkép: Új tanúsítvány létrehozása](media/howto-opc-vault-secure/generate-new-certificate.png "Új tanúsítvány létrehozása")

4. Töltse fel az CSR-t egy helyi fájl kiválasztásával vagy egy base64 kódolású CSR beillesztésével az űrlapon. Válassza **az Új tanúsítvány létrehozása**lehetőséget.

   ![Képernyőkép a Tanúsítványkérelem részleteinek megtekintése](media/howto-opc-vault-secure/approve-reject-csr.png "Csr jóváhagyása")

5. A jóváhagyáshoz a Jóváhagyó szerepkörrel rendelkező és az Azure Key Vaultban aláíró engedélyekkel rendelkező felhasználóra van szükség. Válassza **a Jóváhagyás** vagy az **Elutasítás** lehetőséget a tényleges aláírási művelet elindításához vagy megszakításához. Az eredményül kapott tanúsítványt nyilvános kulc-mal a hitelesítésszolgáltató írja alá. Ez a művelet néhány másodpercet is igénybe vehet.

   ![Képernyőkép a Tanúsítványkérelem részleteinek megtekintése üzenetről, amelynek alján a jóváhagyási üzenet látható](media/howto-opc-vault-secure/view-cert-csr.png "Tanúsítvány megtekintése")

6. Az eredményül kapott tanúsítvány (DER) letölthető innen bináris fájlként. A base64 kódolású verzió is rendelkezésre áll, például a tanúsítvány másolásához és beillesztéséhez egy parancssorba vagy szövegbejegyzésbe. 
10. A tanúsítvány letöltése és biztonságos tárolása után válassza a **Tanúsítvány törlése**lehetőséget.
11. A hitelesítésszolgáltató által aláírt tanúsítvány használata miatt a hitelesítésszolgáltató tanúsítványát és a visszavont tanúsítványok at is le kell tölteni.

Most attól függ, hogy az OPC UA eszköz hogyan kell alkalmazni az új tanúsítványt. A hitelesítésszolgáltatói tanúsítvány és a visszavont tanúsítványok `trusted` listájá általában egy mappába kerülnek, `own` míg az alkalmazástanúsítvány a tanúsítványtároló egy mappájára lesz alkalmazva. Előfordulhat, hogy egyes eszközök már támogatják a kiszolgáló tanúsítványfrissítési leküldéses leküldéses. Tekintse meg az OPC UA eszköz dokumentációját.

### <a name="step-3-device-secured"></a>3. lépés: Eszköz biztosított

Az OPC UA eszköz most már készen áll a kommunikációra más OPC UA eszközök által védett Hitelesítésszolgáltató aláírt tanúsítványok, további konfiguráció nélkül.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az OPC UA-eszközök biztonságossá tétele, a következőket teheti:

> [!div class="nextstepaction"]
> [Biztonságos tanúsítványkezelési szolgáltatás futtatása](howto-opc-vault-secure-ca.md)
