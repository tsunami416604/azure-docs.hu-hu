---
title: Az Azure Cosmos DB Emulator tanúsítványok exportálása
description: Ha olyan nyelveken és futásidőkben fejleszt, amelyek nem használják a Windows tanúsítványtárolót, exportálnia és kezelnie kell a TLS/SSL tanúsítványokat. Ez a bejegyzés lépésről lépésre leírja a teendőket.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: b4283ea7d500ca038d9f1cade89c772880ece199
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409067"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Java-, Python- és Node.js-környezethez használható Azure Cosmos DB Emulator-tanúsítványok exportálása

[**Az Emulator letöltése**](https://aka.ms/cosmosdb-emulator)

Az Azure Cosmos DB emulátor egy helyi környezetet biztosít, amely az Azure Cosmos DB szolgáltatást alkalmazza fejlesztési célokra, beleértve a TLS-kapcsolatok használatát. Ez a bejegyzés bemutatja, hogyan kell exportálni a TLS/ SSL tanúsítványokat olyan nyelveken és futásidőkben való használatra, amelyek nem integrálódnak a Windows tanúsítványtárolóval, például a Java-val, amely saját [tanúsítványtárolót](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) és Pythont használ, amely a [tlsocket-t](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)használó [socket burkolókat](https://docs.python.org/2/library/ssl.html) és Node.js-t használja. Az emulátorról a [Fejlesztés és tesztelés az Azure Cosmos DB Emulator használatával](./local-emulator.md) című cikk ír bővebben.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Tanúsítványok rotálása
> * TLS/SSL tanúsítvány exportálása
> * Tanúsítvány felhasználása Java-, Python- és Node.js-környezetben

## <a name="certification-rotation"></a>Tanúsítvány rotálása

Az Azure Cosmos DB helyszíni emulátorában a tanúsítványok az emulátor első futtatásakor jönnek létre. Két tanúsítvány van. Az egyik a helyszíni emulátorhoz való csatlakozáshoz, a másik az emulátoron belüli titkos kódok kezeléséhez lesz használva. Az exportálandó tanúsítvány a csatlakozáshoz való. Ennek felhasználóbarát neve "DocumentDBEmulatorCertificate".

Mindkét tanúsítvány újragenerálható a Windows-tálcán futó Azure Cosmos DB Emulator menüjének **Adatok alaphelyzetbe állítása** elemére kattintva. Ha a tanúsítványokat újragenerálja és telepíti a Java-tanúsítványtárolóba vagy máshol felhasználja, akkor frissítenie kell őket, másként az alkalmazás nem kapcsolódik többé a helyszíni emulátorhoz.

![Azure Cosmos DB helyszíni emulátor adatok alaphelyzetbe állítása](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Az Azure Cosmos DB TLS/SSL tanúsítvány exportálása

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

Java-alkalmazások vagy Java-ügyfél-alkalmazást használó MongoDB-alkalmazások futtatásakor könnyebb telepíteni a `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` tanúsítványt a Java alapértelmezett tanúsítványtárolójába, mint a jelzők átadása. A mellékelt [Java bemutatóalkalmazás](https://localhost:8081/_explorer/index.html) például az alapértelmezett tanúsítványtárolóra épül.

A [Tanúsítvány hozzáadása a Java CA tanúsítványtárolójához](https://docs.microsoft.com/azure/java-add-certificate-ca-store) című cikk utasításai alapján importálja az X.509-tanúsítványt az alapértelmezett Java-tanúsítványtárolóba. Tartsa szem előtt, hogy a keytool parancs futtatásakor a %JAVA_HOME% könyvtárban fog dolgozni.

A "CosmosDBEmulatorCertificate" TLS/SSL tanúsítvány telepítése után az alkalmazás nak képesnek kell lennie a helyi Azure Cosmos DB-emulátor csatlakoztatására és használatára. Ha továbbra is problémákat tapasztal, akkor ajánlott az [SSL/TLS-kapcsolatok hibaelhárítása](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) című cikk utasításai alapján eljárni. Valószínű, hogy a tanúsítvány nincs telepítve a %JAVA_HOME%/jre/lib/security/cacerts tárolóban. Megtörténhet például, hogy ha a Java több verziója is telepítve van, az alkalmazás nem azt a cacerts tárolót használja, amely frissítve lett.

## <a name="how-to-use-the-certificate-in-python"></a>A tanúsítvány használata a Pythonban

Alapértelmezés szerint a [Python SDK(2.0.0-s vagy újabb verzió)](sql-api-sdk-python.md) az SQL API-t nem próbálja meg használni a TLS/SSL tanúsítványt, amikor csatlakozik a helyi emulátor. Ha azonban szeretné használni a TLS-érvényesítés követheti a python [socket burkolók](https://docs.python.org/2/library/ssl.html) dokumentációban található példákat.

## <a name="how-to-use-the-certificate-in-nodejs"></a>A tanúsítvány használata a Node.js-ben

Alapértelmezés szerint a [Node.js SDK(1.10.1-es vagy újabb verzió)](sql-api-sdk-node.md) az SQL API-hoz nem próbálja meg használni a TLS/SSL tanúsítványt a helyi emulátorhoz való csatlakozáskor. Ha azonban a TLS-érvényesítést szeretné használni, kövesse a [Node.js dokumentációban](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)található példákat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Tanúsítványokat rotált
> * Exportálta a TLS/SSL tanúsítványt
> * Elsajátította a tanúsítvány használatát Javában, Pythonban és a Node.js-ben

Az Azure Cosmos DB-vel kapcsolatos további információért folytassa az Alapfogalmak szakasz áttekintésével. 

> [!div class="nextstepaction"]
>[Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](../cosmos-db/consistency-levels.md)
