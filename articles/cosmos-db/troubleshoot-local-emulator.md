---
title: Az Azure Cosmos Emulator használatakor felmerülő problémák elhárítása
description: Ismerje meg, hogyan troubleshot a szolgáltatás nem érhető el, a tanúsítványok, a titkosítás és a verziószámozási problémák az Azure Cosmos Emulator használata esetén.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.openlocfilehash: 49c03b506441c93be57b24505b09524d72c2c8fb
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995426"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-emulator"></a>Az Azure Cosmos Emulator használatakor felmerülő problémák elhárítása

Az Azure Cosmos Emulator egy helyi környezetet biztosít, amely a Azure Cosmos DB szolgáltatás fejlesztésére szolgál. Az ebben a cikkben található tippek segítséget nyújtanak az Azure Cosmos Emulator telepítésekor vagy használatakor felmerülő problémák elhárításában. 

Ha az emulátor új verzióját telepítette, és hibákat tapasztal, állítsa vissza az adatokat. Az adatai alaphelyzetbe állíthatók. ehhez kattintson a jobb gombbal az Azure Cosmos Emulator ikonra a tálcán, majd kattintson az adatbeállítások visszaállítása... elemre. Ha ez nem oldja meg a hibákat, távolítsa el az emulátort és az emulátor bármely régebbi verzióját, ha megtalálható, távolítsa el a *C:\Program files\Azure Cosmos db Emulator* könyvtárat, és telepítse újra az emulátort. Útmutatásért lásd: [A helyi emulátor eltávolítása](local-emulator.md#uninstall). Másik lehetőségként, ha az adat alaphelyzetbe állítása nem működik, navigáljon a `%LOCALAPPDATA%\CosmosDBEmulator` helyére, és törölje a mappát.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Sérült Windows-teljesítményszámlálók hibakeresése

* Ha az Azure Cosmos Emulator összeomlik, Gyűjtse össze a kiírási fájlokat a `%LOCALAPPDATA%\CrashDumps` mappából, tömörítse őket, és nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com).

* Ha összeomlik a alkalmazásban `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` , akkor ez olyan tünet lehet, amelyben a teljesítményszámlálók sérült állapotban vannak. A következő parancs általában a rendszergazdai parancssorból való futtatása javítja a problémát:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Kapcsolati problémák hibaelhárítása

* Ha kapcsolódási problémába ütközik, [Gyűjtse össze a nyomkövetési fájlokat](#trace-files), tömörítse őket, és nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com).

* Ha **A szolgáltatás nem érhető el** üzenetet kap, előfordulhat, hogy az emulátor nem tudja elindítani a hálózati vermet. Ellenőrizze, hogy telepítve van-e a Pulse Secure ügyfél vagy a Juniper Networks ügyfél, mert ezek hálózatszűrő illesztőprogramjai okozhatják a problémát. A külső gyártótól származó hálózatszűrő illesztőprogramok eltávolítása általában kijavítja a problémát. Azt is megteheti, hogy elindítja az emulátort a/DisableRIO, amely átváltja az emulátor hálózati kommunikációját a normál Winsock-ra. 

* Ha **"tiltott", "üzenet": "kérés történt, az átviteli protokoll vagy a titkosítás tiltott titkosítással. A fiók SSL/TLS minimálisan engedélyezett protokoll-beállításának ellenőrzése... "** kapcsolódási problémák, ezt az operációs rendszer globális változásai okozhatja (például a bennfentes előzetes verzió Build 20170) vagy a böngésző azon beállításai, amelyek alapértelmezés szerint engedélyezik a TLS 1,3-et. Hasonló hiba fordulhat elő, ha az SDK-val kérelmet hajt végre a Cosmos emulátoron, például **Microsoft.Azure.Documents.DocumentClientException: a kérést az átviteli protokoll vagy a titkosítás tiltott titkosítása okozta. A fiók SSL/TLS minimálisan engedélyezett protokoll-beállításának bejelölése**. Jelenleg ez a várható működés, mert a Cosmos Emulator csak a TLS 1.2 protokollt fogadja el, és ezzel működik együtt. A javasolt megoldás a beállítások és az alapértelmezett érték módosítása a TLS 1,2; az IIS-kezelőben például navigáljon a "helyek"-> "alapértelmezett webhelyek" elemre, és keresse meg a "hely kötéseit" a 8081-es porthoz, és szerkessze őket a TLS 1,3 letiltásához. Hasonló művelet hajtható végre a webböngészőre vonatkozóan a „Beállítások” lehetőség használatával.

* Amennyiben az emulátor futtatása közben a számítógép alvó állapotba lép vagy frissül az operációs rendszere, a **Szolgáltatás jelenleg nem érhető el** üzenet jelenik meg. Állítsa alaphelyzetbe az emulátort, és kattintson a jobb gombbal a Windows értesítési tálcán megjelenő ikonra, és válassza az **adatvisszaállítás**lehetőséget.

## <a name="collect-trace-files"></a><a id="trace-files"></a>Profilelemzési fájlok gyűjtése

Hibakeresési nyomok begyűjtéséhez futtassa a következő parancsokat egy rendszergazdai parancssorból:

1. Navigáljon arra az elérési útra, ahol az emulátor telepítve van:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Állítsa le az emulátort, és figyelje meg a rendszertálcán, hogy a program leállítása megtörtént-e. Egy percet is igénybe vehet. A **Kilépés** lehetőséget is kiválaszthatja az Azure Cosmos Emulator felhasználói felületén.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Kezdje a naplózást a következő paranccsal:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. Az emulátor elindítása

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Hozza létre ismét a problémát. Ha az adatkezelő nem működik, mindössze néhány másodpercig várnia kell a böngésző megnyitására, hogy elkapjon a hibát.

1. Állítsa le a naplózást a következő paranccsal:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Keresse meg `%ProgramFiles%\Azure Cosmos DB Emulator` az elérési utat, és keresse meg a *docdbemulator_000001. etl* fájlt.

1. Nyisson meg egy támogatási jegyet a [Azure Portalban](https://portal.azure.com) , és foglalja bele az. etl fájlt a Reprodukálási lépésekkel együtt.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan lehet hibakeresést végezni a helyi emulátorral kapcsolatban. Most folytassa a következő cikkekkel:

* [Az Azure Cosmos Emulator-tanúsítványok exportálása Java-, Python-és Node.js-alkalmazásokkal való használatra](local-emulator-export-ssl-certificates.md)
* [Az emulátor vezérléséhez használjon parancssori paramétereket és PowerShell-parancsokat](emulator-command-line-parameters.md)
