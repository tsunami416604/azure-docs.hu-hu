---
title: Az OPC-ügyfél és a tanúsítványok kezelése az Azure IoT OPC UA segítségével OPC PLC kommunikáció védelméhez |} A Microsoft Docs
description: Az OPC-ügyfél és az OPC-PLC kommunikáció biztonságát biztosító tanúsítványaikat OPC tároló hitelesítésszolgáltató aláírásával.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c437f6db21956d1be5e4f6d3512f325f37ca7308
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759575"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Biztonságossá a kommunikációt az OPC-ügyfél és az OPC-PLC

Az Azure IoT OPC UA tanúsítványkezelés, is tudja, és az OPC-tárolónak micro szolgáltatás, amely konfigurálható, regisztrálása, és az OPC UA-kiszolgáló és ügyfél alkalmazásokat a felhőben a tanúsítvány-életciklus kezelése. Ez a cikk bemutatja, hogyan teheti biztonságossá a kommunikációt az OPC-ügyfél és az OPC-PLC tanúsítványaikat OPC tároló hitelesítésszolgáltató aláírásával.

Az OPC-ügyfél a következő beállítás teszteli, az OPC-PLC kapcsolódni. Alapértelmezés szerint a kapcsolat nem áll lehetséges mivel mind az összetevők nincsenek kiépítve a megfelelő tanúsítványokat. Ha egy OPC UA-összetevő nem lett még kiépítve a tanúsítvánnyal, azt hoz létre egy önaláírt tanúsítványt az indításakor. Azonban a tanúsítvány aláírhatók által a hitelesítésszolgáltató (CA) és az OPC UA-összetevő telepítve van. Miután ez elkészült, az OPC-ügyfél és az OPC-PLC, a kapcsolat engedélyezve van. Az alábbi munkafolyamat a folyamatát ismerteti. Néhány háttér-információkat az OPC UA biztonsági található [ebben a dokumentumban](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) tanulmányt. A teljes körű információkat az OPC UA-specifikáció található.

Testbed: Az alábbi környezetet tesztelési van konfigurálva.

Az OPC-tároló parancsprogramokat:
- Biztonságos kommunikáció OPC és az OPC-PLC tanúsítványaikat OPC tároló hitelesítésszolgáltató aláírásával.

> [!NOTE]
> További információkért tekintse meg a GitHub [tárház](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-self-signed-certificate-on-startup"></a>Létrehozhat egy önaláírt tanúsítványt az indításakor

**Előkészítés**

- Ellenőrizze, hogy a környezeti változók `$env:_PLC_OPT` és `$env:_CLIENT_OPT` sincs meghatározva, például `$env:_PLC_OPT=""` a PowerShellben.

- A környezeti változót `$env:_OPCVAULTID` a karakterlánc, amely lehetővé teszi, hogy újra keresse meg az adatokat az OPC-tárolóban. Csak alfanumerikus karaktereket tartalmazhat. A példánkban a "123456" használták értékeként ezt a változót.

- Győződjön meg arról, nincsenek docker kötetek `opcclient` vagy `opcplc`. Egyeztessen `docker volume ls` , és távolítsa el azokat a `docker volume rm <volumename>`. Szükség lehet az is eltávolítja a tárolók `docker rm <containerid>` , ha a kötetek továbbra is egy tároló által használt.

**Gyors útmutató**

Futtassa a következő PowerShell-parancsot a tárház gyökérkönyvtárában található:

```
docker-compose -f connecttest.yml up
```

**Ellenőrzés**

A naplóban ellenőrizze, hogy nincsenek-e nincs tanúsítvány telepítve van az első indításkor. Itt a kimenet OPC PLC (OPC-ügyfél szolgáltatásra hasonló látható):...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Ha jelentett tanúsítványok jelenik meg, kövesse a fenti előkészítési lépéseket, és a docker-kötet törlése.

Győződjön meg arról, hogy a kapcsolat az OPC-PLC sikertelen. A következő kimenet az OPC-ügyfélben jelentkezzen kimeneti kell megjelennie:

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
A hiba oka, hogy a tanúsítvány, nem megbízható. Ez azt jelenti, hogy `opc-client` próbált meg csatlakozni `opc-plc` és választ kapott vissza, amely jelzi, hogy `opc-plc` nem tekinti megbízhatónak `opc-client`, eredménye `opc-plc` nem lehet érvényesíteni a tanúsítványt, amely `opc-client` van megadva. Ez az önaláírt tanúsítvány további konfiguráció nélkül, a `opc-plc`, és így a kapcsolódás sikertelen volt.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Jelentkezzen, és telepítse a tanúsítványokat az OPC UA-összetevők

**Előkészítés**
1. Tekintse meg az 1. lépés napló kimenetét, és kéri le az OPC-PLC és az OPC-ügyfél "CreateSigningRequest adatait". A kimenet Itt csak az OPC-PLC látható:

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Nyissa meg a [OPC tároló webhely](https://opcvault.azurewebsites.net/).

1. A következők szerint válasszon: `Register New`

1. Adja meg az OPC-PLC adatokat a log kimenetek `CreateSigningRequest information` terület a szövegbeviteli mezőkbe a `Register New OPC UA Application` lapon jelölje be `Server` ApplicationType.

1. Válassza a(z) `Register` lehetőséget.

1. A következő oldalon `Request New Certificate for OPC UA Application` kiválasztása `Request new Certificate with Signing Request`.

1. A következő oldalon `Generate a new Certificate with a Signing Request` illessze be a `CSR (base64 encoded)` karakterláncot, a napló kimenete a `CreateRequest` szövegbeviteli mező. Győződjön meg arról, a teljes karakterláncot másolja.

1. Válassza a(z) `Generate New Certificate` lehetőséget.

1. Most már helyez át előre a `View Certificate Request Details`. Ezen a lapon minden szükséges információját, a tanúsítványok tárolóiban kiépíteni letöltheti `opc-plc`.

1. Ezen az oldalon:  
    - Válassza ki `Certificate` a `Download as Base64` és a szöveges karakterlánc megjelenő másolja a `EncodedBase64` mezőt, és tárolhatja későbbi használatra. Azt nevezzük `<applicationcertbase64-string>` később. Válassza a(z) `Back` lehetőséget.

    - Válassza ki `Issuer` a `Download as Base64` és a szöveges karakterlánc megjelenő másolja a `EncodedBase64` mezőt, és tárolhatja későbbi használatra. Azt nevezzük `<addissuercertbase64-string>` később. Válassza a(z) `Back` lehetőséget.

    - Válassza ki `Crl` a `Download as Base64` és a szöveges karakterlánc megjelenő másolja a `EncodedBase64` mezőt, és tárolhatja későbbi használatra. Azt nevezzük `<updatecrlbase64-string>` később. Válassza a(z) `Back` lehetőséget.

1. Most már a PowerShell beállítása nevű változó `$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Cserélje le a karakterláncok átadott lehetőség értékek Base64 kódolású karakterláncok meg beolvasni a webhelyről.

Ismételje meg a teljes folyamatot kezdve `Register New` (a fenti 3. lépés) az OPC-ügyfél. Nincsenek csak a következő különbségeket kell figyelembe venni:

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

**Ellenőrzési** győződjön meg arról, hogy a két összetevő most aláíró tanúsítványok. A kimenet a következő kimenet ellenőrzése:

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
Az alkalmazás tanúsítványának van-e, és a egy hitelesítésszolgáltató által aláírt.

A naplóban ellenőrizze, hogy nincsenek-e már telepített tanúsítványok. Alább OPC PLC napló kimenetét, és az OPC-ügyfél rendelkezik egy hasonló kimenetet.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
Az alkalmazás tanúsítványának kibocsátója a hitelesítésszolgáltató `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` és az OPC-PLC megbízhatósági is minden, a hitelesítésszolgáltató által aláírt tanúsítvány.


Győződjön meg arról, hogy a kapcsolat az OPC-PLC sikeresen létrejött, és az OPC-ügyfél képes adatokat olvasni az OPC-PLC. A következő kimenet az OPC-ügyfélben jelentkezzen kimeneti kell megjelennie:
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Ha ez a kimenet jelenik meg, majd az OPC-PLC jelenleg a megbízó OPC-ügyfél, és fordítva, mivel is rendelkezik most egy hitelesítésszolgáltató által aláírt tanúsítványokat, és mindkét megbízható tanúsítványok, mely where a hitelesítésszolgáltató által aláírt.

> [!NOTE] 
> Bár az első két ellenőrzési lépések csak az OPC-PLC megmutattuk, azok kell OPC-ügyfél is ellenőrizhető.


## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan helyezhet üzembe egy meglévő projekt OPC-tároló, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az OPC-Ikereszköz egy meglévő projekt üzembe helyezése](howto-opc-twin-deploy-existing.md)