---
title: Az Azure Cosmos DB Emulatort tanúsítványok exportálása
description: Ha olyan nyelveken és futtatókörnyezetekben fejleszt, amelyek nem használják a Windows-tanúsítványtárolót, akkor exportálnia és kezelnie kell az SSL-tanúsítványokat. Ez a bejegyzés lépésről lépésre leírja a teendőket.
services: cosmos-db
keywords: Azure Cosmos DB Emulator
author: David-Noble-at-work
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 06/06/2017
ms.author: danoble
ms.openlocfilehash: f73c6f279f4281abb3751f101255ca3707a59641
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850380"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Java-, Python- és Node.js-környezethez használható Azure Cosmos DB Emulator-tanúsítványok exportálása

[**Az Emulator letöltése**](https://aka.ms/cosmosdb-emulator)

Az Azure Cosmos DB Emulator az Azure Cosmos DB szolgáltatást emuláló helyszíni környezetet biztosít fejlesztési célokra, beleértve az SSL-kapcsolatok használatát is. Ez a bejegyzés az SSL-tanúsítványok exportálását mutatja be a Windows-tanúsítványtárolóval nem integrálható nyelvekhez és futtatókörnyezetekhez. Ilyen például a saját [tanúsítványtárolót](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) használó Java, a [szoftvercsatorna-burkolókat](https://docs.python.org/2/library/ssl.html) használó Python és a [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback) osztályt használó Node.js. Az emulátorról a [Fejlesztés és tesztelés az Azure Cosmos DB Emulator használatával](./local-emulator.md) című cikk ír bővebben.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Tanúsítványok rotálása
> * SSL-tanúsítvány exportálása
> * Tanúsítvány felhasználása Java-, Python- és Node.js-környezetben

## <a name="certification-rotation"></a>Tanúsítvány rotálása

Az Azure Cosmos DB helyszíni emulátorában a tanúsítványok az emulátor első futtatásakor jönnek létre. Két tanúsítvány van. Az egyik a helyszíni emulátorhoz való csatlakozáshoz, a másik az emulátoron belüli titkos kódok kezeléséhez lesz használva. Az exportálandó tanúsítvány a csatlakozáshoz való. Ennek felhasználóbarát neve "DocumentDBEmulatorCertificate".

Mindkét tanúsítvány újragenerálható a Windows-tálcán futó Azure Cosmos DB Emulator menüjének **Adatok alaphelyzetbe állítása** elemére kattintva. Ha a tanúsítványokat újragenerálja és telepíti a Java-tanúsítványtárolóba vagy máshol felhasználja, akkor frissítenie kell őket, másként az alkalmazás nem kapcsolódik többé a helyszíni emulátorhoz.

![Azure Cosmos DB helyszíni emulátor adatok alaphelyzetbe állítása](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Az Azure Cosmos DB SSL-tanúsítvány exportálása

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

Java-alkalmazások vagy a Java-ügyfelet használó MongoDB-alkalmazások futtatásakor egyszerűbb a tanúsítványt a Java alapértelmezett tanúsítványtárolójába telepíteni, mint átadni a "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>" jelölőket. A mellékelt [Java bemutatóalkalmazás](https://localhost:8081/_explorer/index.html) például az alapértelmezett tanúsítványtárolóra épül.

A [Tanúsítvány hozzáadása a Java CA tanúsítványtárolójához](https://docs.microsoft.com/azure/java-add-certificate-ca-store) című cikk utasításai alapján importálja az X.509-tanúsítványt az alapértelmezett Java-tanúsítványtárolóba. Tartsa szem előtt, hogy a keytool parancs futtatásakor a %JAVA_HOME% könyvtárban fog dolgozni.

A "CosmosDBEmulatorCertificate" SSL-tanúsítvány telepítése után az alkalmazásnak képesnek kell lennie a helyszíni Azure Cosmos DB Emulatorhoz való csatlakozásra és annak használatára. Ha továbbra is problémákat tapasztal, akkor ajánlott az [SSL/TLS-kapcsolatok hibaelhárítása](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) című cikk utasításai alapján eljárni. Valószínű, hogy a tanúsítvány nincs telepítve a %JAVA_HOME%/jre/lib/security/cacerts tárolóban. Megtörténhet például, hogy ha a Java több verziója is telepítve van, az alkalmazás nem azt a cacerts tárolót használja, amely frissítve lett.

## <a name="how-to-use-the-certificate-in-python"></a>A tanúsítvány használata a Pythonban

Az SQL API-hoz való [Python SDK (2.0.0 vagy újabb verzió)](sql-api-sdk-python.md) alapértelmezés szerint nem kísérli meg az SSL-tanúsítvány használatát, amikor a helyszíni emulátorhoz csatlakozik. Ha mégis SSL-érvényesítést kíván használni, akkor eljárhat a [Python szoftvercsatorna-burkolók](https://docs.python.org/2/library/ssl.html) dokumentációjában ismertetett példák alapján.

## <a name="how-to-use-the-certificate-in-nodejs"></a>A tanúsítvány használata a Node.js-ben

Az SQL API-hoz való [Node.js SDK (1.10.1 vagy újabb verzió)](sql-api-sdk-node.md) alapértelmezés szerint nem kísérli meg az SSL-tanúsítvány használatát, amikor a helyszíni emulátorhoz csatlakozik. Ha mégis SSL-érvényesítést kíván használni, akkor eljárhat a [Node.js dokumentációjában](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback) ismertetett példák alapján.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Tanúsítványokat rotált
> * Exportálta az SSL-tanúsítványt
> * Elsajátította a tanúsítvány használatát Javában, Pythonban és a Node.js-ben

Az Azure Cosmos DB-vel kapcsolatos további információért folytassa az Alapfogalmak szakasz áttekintésével. 

> [!div class="nextstepaction"]
>[Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](../cosmos-db/consistency-levels.md)
