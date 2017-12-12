---
title: "Az Azure Cosmos DB emulátor tanúsítványok exportálása |} Microsoft Docs"
description: "A nyelv és a Windows tanúsítványtároló nem használó futtatókörnyezetek fejlesztése során szüksége lesz exportálni, és az SSL-tanúsítványok kezelését. A post lépésenkénti utasításokat biztosít."
services: cosmos-db
documentationcenter: 
keywords: "Az Azure Cosmos DB emulátor"
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36a2f99b9d5ea4b9e6b313fed79cf7f4dd0e7057
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>A Node.js, Java és Python való használatra Azure Cosmos DB emulátor tanúsítványok exportálása

[**Töltse le az Emulátorban**](https://aka.ms/cosmosdb-emulator)

Az Azure Cosmos DB Emulator emulálja a fejlesztéshez, beleértve az SSL-kapcsolatok használatát a Azure Cosmos DB szolgáltatás, helyi környezetet biztosít. A post bemutatja, hogyan kell exportálni a nyelvet és a futtatókörnyezetek, amely integrálható a Windows tanúsítványtárolóban, például a Java, amely használja a saját SSL-tanúsítványok [tanúsítványtároló](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) és Python használó[burkolók szoftvercsatorna](https://docs.python.org/2/library/ssl.html) és a Node.js használó [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). További tudnivalók az emulátor [fejlesztéshez és teszteléshez használja az Azure Cosmos DB Emulator](./local-emulator.md).

Ez az oktatóanyag ismerteti a következő feladatokat:

> [!div class="checklist"]
> * Tanúsítványok elforgatása
> * SSL-tanúsítvány exportálása
> * A Node.js, Java és Python tanúsítvány használata

## <a name="certification-rotation"></a>Hitelesítésszolgáltató Elforgatás

Az Azure Cosmos DB helyi emulátorban tanúsítványok jönnek létre az emulátor első futtatásakor. Két tanúsítványokat is tartalmaz. Egy, a másik az emulátor belül titkos kulcsok kezelése a helyi emulátor való kapcsolódáshoz. Az exportálni kívánt tanúsítvány a kapcsolati tanúsítványt ezzel a rövid névvel "DocumentDBEmulatorCertificate".

Mindkét tanúsítványnak helyreállíthatja kattintva **adatok alaphelyzetbe állítása** Azure Cosmos DB-emulátort a a Windows tálca az alább látható módon. Ha újragenerálni a tanúsítványokat, és telepítette azokat a Java tanúsítványtárolóba vagy használták őket, máshol szüksége lesz a frissítésükhöz, ellenkező esetben az alkalmazás már nem csatlakoznak a helyi emulátor.

![Az Azure Cosmos DB helyi emulátor adatok visszaállítása](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Az Azure Cosmos DB SSL-tanúsítvány exportálása

1. Indítsa el a Windows tanúsítványkezelője a certlm.msc fut, és keresse meg a személyes Tokens tanúsítványok mappát, és nyissa meg a tanúsítvány rövid nevét **DocumentDbEmulatorCertificate**.

    ![Az Azure Cosmos DB helyi emulátor exportálása 1. lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Kattintson a **részletek** majd **OK**.

    ![Az Azure Cosmos DB helyi emulátor exportálása 2. lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Kattintson a **Másolás fájlba...** .

    ![Az Azure Cosmos DB helyi emulátor exportálása 3. lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Kattintson a **Tovább** gombra.

    ![Az Azure Cosmos DB helyi emulátor exportálása 4. lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Kattintson a **nem, nem exportálja a titkos kulcs**, majd kattintson a **következő**.

    ![Az Azure Cosmos DB helyi emulátor exportálása 5. lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Kattintson a **Base-64 kódolású X.509 (. CER)** , majd **következő**.

    ![Az Azure Cosmos DB helyi emulátor exportálása 6. lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Nevezze el a tanúsítványt. Ebben az esetben **documentdbemulatorcert** majd **következő**.

    ![Az Azure Cosmos DB helyi emulátor exportálása 7. lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Kattintson a **Befejezés** gombra.

    ![Az Azure Cosmos DB helyi emulátor exportálja a 8. lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>A tanúsítvány a Java használatával

Java-alkalmazások vagy a MongoDB-alkalmazások, a Java-ügyfél használata esetén telepítse a tanúsítványt a tanúsítványtárolóba Java alapértelmezett mint sikeres könnyebben a "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword = "<password>" jelzők. Ha például a megadott [Java bemutató alkalmazás](https://localhost:8081/_explorer/index.html) attól függ, az alapértelmezett tanúsítványtárolójában.

Kövesse az utasításokat a [tanúsítvány hozzáadása a Java hitelesítésszolgáltató tanúsítványtárolójában](https://docs.microsoft.com/azure/java-add-certificate-ca-store) X.509-tanúsítvány importálása az alapértelmezett Java tanúsítványtárolójába. Ne mind kívánja fog dolgozni a % JAVA_HOME % könyvtárban kulcseszköz futtatásakor.

Egyszer a "CosmosDBEmulatorCertificate" SSL-tanúsítvány telepítve van az alkalmazás csatlakozzon, és használja a helyi Azure Cosmos DB emulátor képesnek kell lennie. Ha továbbra is problémákat tapasztal érdemes lehet kövesse a [hibakeresés SSL/TLS kapcsolatok](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) cikk. Nagyon valószínű, a tanúsítvány nincs telepítve a %JAVA_HOME%/jre/lib/security/cacerts tárolóba. Az eset például, ha az alkalmazás Java több telepített verzióját használja egy másik cacerts tárolóban, mint egy frissített.

## <a name="how-to-use-the-certificate-in-python"></a>A tanúsítvány használatáról Python

Alapértelmezés szerint a [Python SDK(version 2.0.0 or higher)](documentdb-sdk-python.md) az SQL API-t nem próbálja meg, és nem használja az SSL-tanúsítvány, ha a helyi emulátor csatlakozik. Ha azonban az SSL-érvényesítési használni kívánt követésével szereplő példák a [Python szoftvercsatorna burkolók](https://docs.python.org/2/library/ssl.html) dokumentációját.

## <a name="how-to-use-the-certificate-in-nodejs"></a>A tanúsítvány használata a Node.js-ben

Alapértelmezés szerint a [Node.js SDK(version 1.10.1 or higher)](documentdb-sdk-node.md) az SQL API-t nem próbálja meg, és nem használja az SSL-tanúsítvány, ha a helyi emulátor csatlakozik. Ha azonban az SSL-érvényesítési használni kívánt követésével szereplő példák a [Node.js dokumentáció](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Elforgatott tanúsítványok
> * Az SSL-tanúsítvány exportálása
> * Megtudta, hogyan használhatja a tanúsítványt a Java, Python, Node.js

Most hozzon létre egy Azure Cosmos DB bemeneti kötése az oktatóanyag az Azure Functions HTTP eseményindító lépne.

> [!div class="nextstepaction"]
> [Az Azure Cosmos Adatbázisból bevitellel hozzon létre egy Azure-függvény](tutorial-functions-http-trigger.md) 
