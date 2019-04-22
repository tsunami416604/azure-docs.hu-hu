---
title: Az OPC UA biztonságos, ügyfél és az OPC UA server alkalmazás használata az OPC-tároló – Azure |} A Microsoft Docs
description: Biztonságos OPC UA-ügyfélből és az OPC UA kiszolgálói alkalmazás egy új kulcspárt és az OPC-tároló használatával új tanúsítványt.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495294"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Az OPC UA biztonságos, ügyfél és az OPC UA-kiszolgáló alkalmazás 
Az OPC-tároló, konfigurálása, regisztrálja, és az OPC UA-kiszolgáló és az ügyfélalkalmazások számára a felhőben a tanúsítvány életciklusának kezelése mikroszolgáltatások. Ez a cikk bemutatja, hogyan OPC UA-ügyféllel és a egy OPC UA egy új kulcspárt és az OPC-tároló használatával új tanúsítványt a kiszolgáló-alkalmazások biztonságossá tételéhez.

A következő beállítás az OPC-ügyfél a kapcsolat az OPC-PLC van tesztelése. Alapértelmezés szerint a kapcsolat nem áll lehetséges mivel mindkét összetevő még nincs kiépítve a megfelelő tanúsítványokat. Ebben a munkafolyamatban azt nem az OPC UA-összetevők önaláírt tanúsítványokat használni, és jelentkezzen őket az OPC-tároló útján. Az előző [testbed](howto-opc-vault-deploy-existing-client-plc-communication.md). Ehelyett a testbed látja el az összetevők egy új tanúsítvánnyal, valamint egy új titkos kulccsal egyaránt által előállított OPC-tároló. A jelen található néhány háttér-információkat az OPC UA biztonsági [tanulmány](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). A teljes körű információkat az OPC UA-specifikáció található.

Testbed: Az alábbi környezetet tesztelési van konfigurálva.

Az OPC-tároló parancsprogramokat:
- Alkalmazások védelmének biztosítása az OPC UA-ügyfélből és az OPC UA kiszolgálót egy új kulcspárt és az OPC-tároló használatával új tanúsítványt.

> [!NOTE]
> További információkért tekintse meg a GitHub [tárház](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Hozzon létre egy új tanúsítványt és titkos kulcs 
**Előkészítés**
- Ellenőrizze, hogy a környezeti változók `$env:_PLC_OPT` és `$env:_CLIENT_OPT` nincs definiálva. Ha például `$env:_PLC_OPT=""` a PowerShellben
- A környezeti változót `$env:_OPCVAULTID` a karakterlánc, amely lehetővé teszi, hogy újra keresse meg az adatokat az OPC-tárolóban. Azt javasoljuk, hogy értékre állítani egy 6 jegyű számot. A példánkban a "123456" változó használták értékként.
- Győződjön meg arról, nem a docker-kötetet `opcclient` vagy `opcplc`. Egyeztessen `docker volume ls` , és távolítsa el azokat a `docker volume rm <volumename>`. Szükség lehet az is eltávolítja a tárolók `docker rm <containerid>` , ha a kötetek továbbra is egy tároló által használt.

**Gyors útmutató**
1. Nyissa meg a [OPC tároló webhely](https://opcvault.azurewebsites.net/)

1. A következők szerint válasszon: `Register New`

1. Adja meg az OPC-PLC információkat, a napló kimenetét az előző testbed látható volt `CreateSigningRequest information` terület a szövegbeviteli mezőkbe a `Register New OPC UA Application` lapon jelölje be `Server` ApplicationType.

1. A következők szerint válasszon: `Register`

1. A következő oldalon `Request New Certificate for OPC UA Application` kiválasztása `Request new KeyPair and Certificate`

1. A következő oldalon `Generate a new Certificate with a Signing Request` illessze be a `CSR (base64 encoded)` karakterláncot, a napló kimenete a `CreateRequest` szövegbeviteli mező. Győződjön meg arról, a teljes karakterláncot másolja.

1. A következő oldalon `Request New Certificate for OPC UA Application` kiválasztása `Request new Certificate with Signing Request`

1. A következő oldalon `Generate a new KeyPair and for an OPC UA Application` adja meg `CN=OpcPlc` SubjectName, mint `opcplc-<_OPCVAULTID>` (cserélje le `<_OPCVAULTID>` naplótípussal), tartománynév, válassza ki a `PEM` PrivateKeyFormat, és adjon meg egy jelszót (azt később hivatkoznak rá, `<certpassword-string>`)

1. A következők szerint válasszon: `Generate New KeyPair`

1. Most már helyez át előre a `View Certificate Request Details`. Ezen a lapon minden szükséges információját, a tanúsítványok tárolóiban kiépíteni letöltheti `opc-plc`.

1. Ezen az oldalon:  
    - Válassza ki `Certificate` a `Download as Base64` és a szöveges karakterlánc megjelenő másolja a `EncodedBase64` mezőt, és tárolhatja későbbi használatra. Azt nevezzük `<applicationcertbase64-string>` később. Válassza a(z) `Back` lehetőséget.
    - Válassza ki `PrivateKey` a `Download as Base64` és a szöveges karakterlánc megjelenő másolja a `EncodedBase64` mezőt, és tárolhatja későbbi használatra. Azt nevezzük `<privatekeybase64-string>` később. Válassza a(z) `Back` lehetőséget.
    - Válassza ki `Issuer` a `Download as Base64` és a szöveges karakterlánc megjelenő másolja a `EncodedBase64` mezőt, és tárolhatja későbbi használatra. Azt nevezzük `<addissuercertbase64-string>` később. Válassza a(z) `Back` lehetőséget.
    - Válassza ki `Crl` a `Download as Base64` és a szöveges karakterlánc megjelenő másolja a `EncodedBase64` mezőt, és tárolhatja későbbi használatra. Azt nevezzük `<updatecrlbase64-string>` később. Válassza a(z) `Back` lehetőséget.

1. Most már a PowerShell beállítása nevű változó `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Cserélje le a karakterláncok átadott lehetőség értékek Base64 kódolású karakterláncok meg beolvasni a webhelyről.  

1. Ismételje meg a teljes folyamatot kezdve `Register New` az OPC-ügyfél. Nincsenek csak a következő különbségeket kell figyelembe venni:
    - A napló kimenetét használják a `opcclient`.
    - Válassza ki `Client` , ApplicationType regisztrációja során.
    - Használat `$env:_CLIENT_OPT` , a PowerShell-változó neve.

    > [!NOTE] 
    > A jelen forgatókönyvben használatakor, előfordulhat, hogy rendelkezik felismerte, hogy a `<addissuercertbase64-string>` és `<updatecrlbase64-string>` értékek megegyeznek a `opcplc` és `opcclient`. Ez igaz, a használati esetekhez és takaríthat meg időt a lépések végrehajtása közben.

**Gyors útmutató**

Futtassa a következő PowerShell-parancsot a tárház gyökérkönyvtárában található:

```
docker-compose -f connecttest.yml up
```

**Ellenőrzés**

Győződjön meg arról, hogy a két összetevőt nem módosult-e meg a meglévő alkalmazás tanúsítványt. Ellenőrizze a napló kimenetét. Alább az OPC-PLC kimenetét, és az OPC-ügyfél rendelkezik egy hasonló kimenet.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
Ha egy alkalmazás-tanúsítványt, az előkészítő lépésben leírtak távolítsa el a docker-köteteket.

Ellenőrizze a naplóban, amelyen az OPC-tároló Hitelesítésszolgáltatói tanúsítvány telepítve lett-e a kibocsátó tanúsítványtárolójában is hasonlóan a társ megbízható tárolóba. Alább OPC PLC napló kimenetét, és az OPC-ügyfél rendelkezik egy hasonló kimenet. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
Az OPC-PLC most megbízhatósági összes OPC UA-ügyfelek az OPC-tár által aláírt tanúsítványokat.

A naplóban ellenőrizze, hogy a titkos kulcs formátumaként PEM zálogának és, hogy telepítve van-e az új alkalmazás-tanúsítvány. Alább OPC PLC napló kimenetét, és az OPC-ügyfél rendelkezik egy hasonló kimenet. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

Az alkalmazás tanúsítványának és a titkos kulcs már telepítve van az alkalmazás tanúsítványtárolójában és az OPC UA-alkalmazás által használt.

Győződjön meg arról, hogy sikeresen megtörtént az OPC-ügyfél és a OPC PLC közötti kapcsolat is létesíthető, és az OPC-ügyfél sikeresen tudja olvasni az adatokat az OPC-PLC. Az OPC ügyfél log kimenetben a következő kimenetnek kell megjelennie:
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Ha ez a kimenet jelenik meg, majd az OPC-PLC van most megbízó az OPC ügyfél futtatását, mivel is rendelkezik, most már mindkét megbízható tanúsítványokat a hitelesítésszolgáltató által aláírt és a hitelesítésszolgáltató által aláírt tanúsítványokat.

### <a name="a-testbed-for-opc-publisher"></a>Egy testbed az OPC-közzétevő ###

**Gyors útmutató**

Futtassa a következő PowerShell-parancsot a tárház gyökérkönyvtárában található:
```
docker-compose -f testbed.yml up
```

**Ellenőrzés**
- Győződjön meg arról, hogy adatokat küld az IoTHub-úgy konfigurálta a `_HUB_CS` használatával [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) vagy [iothub-explorer](https://github.com/Azure/iothub-explorer).
- OPC tesztügyfél fog IoTHub közvetlen metódust hívja, és az OPC-metódust hívja segítségével konfigurálhatja az OPC-közzétevő csomópontokat a OPC tesztkiszolgálóról közzététele vagy visszavonni.
- Tekintse meg a kimeneti hibaüzeneteket.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan helyezhet üzembe egy meglévő projekt OPC-tároló, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az OPC-Ikereszköz egy meglévő projekt üzembe helyezése](howto-opc-twin-deploy-existing.md)