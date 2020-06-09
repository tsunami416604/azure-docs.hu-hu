---
title: Az Azure Cosmos DB Emulator tanúsítványok exportálása
description: Ha olyan nyelveken és futtatókörnyezeteken fejleszt, amelyek nem használják a Windows-tanúsítványtárolót, exportálnia kell a TLS/SSL-tanúsítványokat. Ez a bejegyzés lépésről lépésre leírja a teendőket.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.custom: tracking-python
ms.openlocfilehash: 89d91a3c5c8a4209489ed427067a90e7e2d0b880
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561181"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Java-, Python- és Node.js-környezethez használható Azure Cosmos DB Emulator-tanúsítványok exportálása

[**Az Emulator letöltése**](https://aka.ms/cosmosdb-emulator)

A Azure Cosmos DB Emulator olyan helyi környezetet biztosít, amely a Azure Cosmos DB szolgáltatást fejlesztési célokra emulálja, beleértve a TLS-kapcsolatok használatát is. Ez a bejegyzés bemutatja, hogyan exportálhatja a TLS/SSL-tanúsítványokat olyan nyelveken és futtatókörnyezetekben való használatra, amelyek nem integrálják a Windows-tanúsítványtárolót, például a Java-t, amely a saját [tanúsítványtárolóját](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) és a Pythont használja, amely a [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)-t használó [socket burkolókat](https://docs.python.org/2/library/ssl.html) és Node. js-t használ. Az emulátorról a [Fejlesztés és tesztelés az Azure Cosmos DB Emulator használatával](./local-emulator.md) című cikk ír bővebben.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Tanúsítványok rotálása
> * TLS/SSL-tanúsítvány exportálása
> * Tanúsítvány felhasználása Java-, Python- és Node.js-környezetben

## <a name="certification-rotation"></a>Tanúsítvány rotálása

Az Azure Cosmos DB helyszíni emulátorában a tanúsítványok az emulátor első futtatásakor jönnek létre. Két tanúsítvány van. Az egyik a helyszíni emulátorhoz való csatlakozáshoz, a másik az emulátoron belüli titkos kódok kezeléséhez lesz használva. Az exportálandó tanúsítvány a csatlakozáshoz való. Ennek felhasználóbarát neve "DocumentDBEmulatorCertificate".

Mindkét tanúsítvány újragenerálható a Windows-tálcán futó Azure Cosmos DB Emulator menüjének **Adatok alaphelyzetbe állítása** elemére kattintva. Ha a tanúsítványokat újragenerálja és telepíti a Java-tanúsítványtárolóba vagy máshol felhasználja, akkor frissítenie kell őket, másként az alkalmazás nem kapcsolódik többé a helyszíni emulátorhoz.

![Azure Cosmos DB helyszíni emulátor adatok alaphelyzetbe állítása](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>A Azure Cosmos DB TLS/SSL-tanúsítvány exportálása

1. Indítsa el a Windows Tanúsítványkezelőjét a certlm.msc futtatásával, nyissa meg a Személyes->Tanúsítványok mappát, majd nyissa meg a **DocumentDbEmulatorCertificate** nevű tanúsítványt.

    ![Azure Cosmos DB helyszíni emulátor 1. exportálási lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Kattintson a **Részletek**, majd az **OK** lehetőségre.

    ![Azure Cosmos DB helyszíni emulátor 2. exportálási lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Kattintson a **Másolás fájlba...** lehetőségre.

    ![Azure Cosmos DB helyszíni emulátor 3. exportálási lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Kattintson a **Tovább** gombra.

    ![Azure Cosmos DB helyszíni emulátor 4. exportálási lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Kattintson a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőségre, majd a **Tovább** gombra.

    ![Azure Cosmos DB helyszíni emulátor 5. exportálási lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Kattintson a **Base-64 kódolású X.509 (.CER)** lehetőségre, majd a **Tovább** gombra.

    ![Azure Cosmos DB helyszíni emulátor 6. exportálási lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Nevezze el a tanúsítványt. Ebben a példában a név **documentdbemulatorcert**. Végül kattintson a **Tovább** lehetőségre.

    ![Azure Cosmos DB helyszíni emulátor 7. exportálási lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Kattintson a **Befejezés** gombra.

    ![Azure Cosmos DB helyszíni emulátor 8. exportálási lépés](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>A tanúsítvány használata a Javában

A Java-ügyfelet használó Java-alkalmazások vagy MongoDB-alkalmazások futtatásakor a tanúsítvány telepítése egyszerűbb a Java alapértelmezett tanúsítványtárolóba, mint a `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` jelzők továbbítása. Például a mellékelt Java bemutató alkalmazás ( `https://localhost:8081/_explorer/index.html` ) az alapértelmezett tanúsítványtárolótól függ.

A [Tanúsítvány hozzáadása a Java CA tanúsítványtárolójához](https://docs.microsoft.com/azure/java-add-certificate-ca-store) című cikk utasításai alapján importálja az X.509-tanúsítványt az alapértelmezett Java-tanúsítványtárolóba. Tartsa szem előtt, hogy a keytool parancs futtatásakor a %JAVA_HOME% könyvtárban fog dolgozni.

Miután telepítette az "CosmosDBEmulatorCertificate" TLS/SSL-tanúsítványt, az alkalmazásnak képesnek kell lennie csatlakozni a helyi Azure Cosmos DB Emulator használatára. Ha továbbra is problémákat tapasztal, akkor ajánlott az [SSL/TLS-kapcsolatok hibaelhárítása](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) című cikk utasításai alapján eljárni. Valószínű, hogy a tanúsítvány nincs telepítve a %JAVA_HOME%/jre/lib/security/cacerts tárolóban. Megtörténhet például, hogy ha a Java több verziója is telepítve van, az alkalmazás nem azt a cacerts tárolót használja, amely frissítve lett.

## <a name="how-to-use-the-certificate-in-python"></a>A tanúsítvány használata a Pythonban

Alapértelmezés szerint az SQL API-hoz készült [PYTHON SDK (2.0.0 vagy újabb verzió)](sql-api-sdk-python.md) nem próbálja meg és nem használja a TLS/SSL-tanúsítványt a helyi emulátorhoz való csatlakozáskor. Ha azonban TLS-ellenőrzést szeretne használni, kövesse a [Python socket Wrappers](https://docs.python.org/2/library/ssl.html) dokumentációjában található példákat.

## <a name="how-to-use-the-certificate-in-nodejs"></a>A tanúsítvány használata a Node.js-ben

Alapértelmezés szerint a [Node. js SDK (1.10.1 vagy újabb verzió)](sql-api-sdk-node.md) az SQL API-hoz nem próbálkozik és nem használja a TLS/SSL-tanúsítványt a helyi emulátorhoz való csatlakozáskor. Ha azonban TLS-érvényesítést szeretne használni, kövesse a [Node. js dokumentációjában](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)található példákat.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Tanúsítványokat rotált
> * A TLS/SSL-tanúsítvány exportálása
> * Elsajátította a tanúsítvány használatát Javában, Pythonban és a Node.js-ben

Az Azure Cosmos DB-vel kapcsolatos további információért folytassa az Alapfogalmak szakasz áttekintésével. 

> [!div class="nextstepaction"]
>[Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](../cosmos-db/consistency-levels.md)
