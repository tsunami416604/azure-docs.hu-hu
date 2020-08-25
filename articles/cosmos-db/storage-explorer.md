---
title: Azure Cosmos DB erőforrások kezelése a Azure Storage Explorer használatával
description: Megtudhatja, hogyan csatlakozhat a Azure Cosmos DBhoz, és hogyan kezelheti erőforrásait Azure Storage Explorer használatával.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: b892e4c5078b50bb865a715ddf12aebc1eb05f57
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799111"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Adatok kezelése az Azure Storage Explorerrel

Az Azure Cosmos DB Azure Storage Explorerben történő használata lehetővé teszi a felhasználók számára az Azure Cosmos DB-entitások kezelését, az adatok módosítását, valamint a tárolt eljárások és eseményindítók frissítését olyan Azure-entitások mellett, mint a tárolóblobok és üzenetsorok. Mostantól ugyanazon eszközzel, egy helyen kezelheti a különböző Azure-entitásokat. Jelenleg Azure Storage Explorer támogatja az SQL, a MongoDB, a Graph és a Table API-khoz konfigurált Cosmos-fiókokat.


## <a name="prerequisites"></a>Előfeltételek

Egy MongoDB SQL API-val vagy Azure Cosmos DB API-val rendelkező Cosmos-fiók. Ha nem rendelkezik fiókkal, az Azure Portalon létrehozhat egyet az [Azure Cosmos DB: SQL API-webalkalmazás létrehozása .NET-tel és az Azure Portallal](create-sql-api-dotnet.md) című cikkben leírtaknak megfelelően.

## <a name="installation"></a>Telepítés

Az Azure Storage Explorer legújabb elemei innen telepíthetők: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Már a Windows-, Linux- és MAC-alapú verzió is támogatott.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

1. A **Azure Storage Explorer**telepítése után kattintson a bal oldali **beépülő** modul ikonjára a következő képen látható módon:

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="A kapcsolódáshoz válassza ki a beépülő modul ikonját":::

2. Válassza az **Azure-fiók hozzáadása** elemet, majd kattintson a **Bejelentkezés** gombra.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Kapcsolódás a szükséges Azure-előfizetéshez":::

2. Az **Azure bejelentkezési** párbeszédpaneljén válassza a **Bejelentkezés**lehetőséget, majd adja meg az Azure-beli hitelesítő adatait.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Jelentkezzen be az Azure-előfizetésbe":::

3. Válassza ki az előfizetést a listából, majd válassza az **alkalmaz**lehetőséget.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Válasszon egy előfizetési azonosítót a listából a szűréshez":::

    Az Explorer panel frissül, és megjeleníti a kiválasztott előfizetéshez tartozó fiókokat.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Válasszon ki egy Azure Cosmos DB fiókot az elérhető listából.":::

    Sikeresen csatlakoztatta a **Cosmos DB-fiókot** az Azure-előfizetéshez.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Csatlakozás az Azure Cosmos DB-hez kapcsolati sztring használatával

Az Azure Cosmos DB-hez történő csatlakozás másik módja a kapcsolati sztring használata. Az alábbi lépéseket követve csatlakozhat kapcsolati sztringgel.

1. Keresse meg a **Helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal a **Cosmos DB-fiókok** elemre, majd válassza a **Csatlakozás a Cosmos DB-hez…** lehetőséget.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Csatlakozás az Azure Cosmos DB-hez kapcsolati sztring használatával":::

2. Egyelőre csak az SQL és a Table API-t támogatja. Válassza az API lehetőséget, illessze be a **kapcsolati sztringet**, a bemeneti **fiók címkéjét**, válassza a **tovább** lehetőséget az összefoglalás vizsgálatához, majd válassza a **Kapcsolódás** lehetőséget Azure Cosmos db fiók csatlakoztatásához. Az elsődleges kapcsolatok karakterláncának beolvasásával kapcsolatos információkért lásd: [a kapcsolatok karakterláncának](manage-with-powershell.md#list-keys)beolvasása.

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Adja meg a kapcsolatok karakterláncát":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Csatlakozás az Azure Cosmos DB-hez helyi emulátorral

A következő lépésekkel csatlakozhat az Azure Cosmos DB-hez az Emulator használatával. Ez a megoldás egyelőre csak az SQL-fiókot támogatja.

1. Telepítse az Emulatort, és indítsa el. Az Emulator telepítéséról lásd a [Cosmos DB Emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) témakörét.

2. Keresse meg a **Helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal a **Cosmos DB-fiókok** elemre, majd válassza a **Csatlakozás a Cosmos DB Emulatorhoz…** lehetőséget.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Kapcsolódás Azure Cosmos DB az emulátorból":::

3. Egyelőre csak az SQL API-t támogatja. Illessze be a **kapcsolati sztringet**, a bemeneti **fiók címkéjét**, válassza a **tovább** lehetőséget az összefoglalás vizsgálatához, majd válassza a **Kapcsolódás** lehetőséget Azure Cosmos db fiók csatlakoztatásához. Az elsődleges kapcsolatok karakterláncának beolvasásával kapcsolatos információkért lásd: [a kapcsolatok karakterláncának](manage-with-powershell.md#list-keys)beolvasása.

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Kapcsolódás Cosmos DBhoz az emulátor párbeszédpanelről":::


## <a name="azure-cosmos-db-resource-management"></a>Az Azure Cosmos DB-erőforrások kezelése

Az Azure Cosmos DB-fiók az alábbi műveletekkel kezelhető:
* A fiók megnyitása az Azure Portalon;
* Az erőforrás hozzáadása a gyorselérési listához;
* Erőforrások keresése és frissítése;
* Adatbázisok létrehozása és törlése;
* Gyűjtemények létrehozása és törlése;
* Dokumentumok létrehozása, szerkesztése, törlése és szűrése;
* Tárolt eljárások, eseményindítók és felhasználói függvények kezelése.

### <a name="quick-access-tasks"></a>Feladatok gyors elérése

Ha a jobb gombbal kattint egy előfizetésre az Explorer panelen, gyorsan végrehajthat számos műveletet:

* Kattintson a jobb gombbal egy Azure Cosmos DB-fiókra vagy -adatbázisra, majd a **Megnyitás a portálon** lehetőség kiválasztásával kezelheti az erőforrást a böngészőben az Azure Portalon.

     :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Megnyitás a portálon":::

* Emellett Azure Cosmos DB-fiókot, -adatbázist és -gyűjteményt is hozzáadhat a **gyorselérési** eszköztárhoz.
* A **Keresés innen** funkció lehetővé teszi a kulcsszavaknak a kijelölt útvonalon történő keresését.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="keresés innen:":::

### <a name="database-and-collection-management"></a>Adatbázisok és gyűjtemények kezelése

#### <a name="create-a-database"></a>Adatbázis létrehozása

-   Kattintson a jobb gombbal az Azure Cosmos DB-fiókra, válassza az **Adatbázis létrehozása** elemet, adja meg az adatbázis nevét, majd a befejezéshez nyomja le az **Enter** billentyűt.

    :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Adatbázis létrehozása az Azure Cosmos-fiókban":::

#### <a name="delete-a-database"></a>Adatbázis törlése

- Kattintson a jobb gombbal az adatbázisra, válassza az **adatbázis törlése**lehetőséget, majd az előugró ablakban válassza az **Igen** lehetőséget. A rendszer törli az adatbázis-csomópontot, és automatikusan frissíti az Azure Cosmos DB-fiókot.

    :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Az első adatbázis törlése":::

    :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="A második adatbázis törlése":::

#### <a name="create-a-collection"></a>Gyűjtemény létrehozása

1. Kattintson a jobb gombbal az adatbázisra, válassza a **gyűjtemény létrehozása**lehetőséget, majd adja meg a következő információkat, például a **gyűjtemény azonosítóját**, a **tárolási kapacitást**stb. A befejezéshez kattintson **az OK** gombra.

    :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Első gyűjtemény létrehozása az adatbázisban":::

    :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Második gyűjtemény létrehozása az adatbázisban":::

2. A partíciós kulcs megadásához válassza a **korlátlan** lehetőséget, majd a befejezéshez kattintson **az OK gombra** .

    Ha partíciókulcsot használt a gyűjtemény létrehozásakor, akkor a létrehozást követően a partíciókulcs értéke nem módosítható a gyűjteményben.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Partíciós kulcs konfigurálása":::

#### <a name="delete-a-collection"></a>Gyűjtemény törlése

- Kattintson a jobb gombbal a gyűjteményre, válassza a **gyűjtemény törlése**lehetőséget, majd az előugró ablakban válassza az **Igen** lehetőséget.

    A rendszer törli a gyűjteménycsomópontot, és automatikusan frissíti az adatbázist.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Az egyik gyűjtemény törlése":::

### <a name="document-management"></a>Dokumentumok kezelése

#### <a name="create-and-modify-documents"></a>Dokumentumok létrehozása és módosítása

- Új dokumentum létrehozásához nyissa meg a **dokumentumok** elemet a bal oldali ablakban, válassza az **új dokumentum**lehetőséget, szerkessze a tartalmat a jobb oldali ablaktáblán, majd kattintson a **Mentés**gombra. Egy meglévő dokumentumot is frissíthet, majd a **Mentés**gombra kattinthat. A módosítások elvetéséhez kattintson az **Elvetés** gombra.

    :::image type="content" source="./media/storage-explorer/document.png" alt-text="Új dokumentum létrehozása":::

#### <a name="delete-a-document"></a>Dokumentum törlése

- Kattintson a **Törlés** gombra a kijelölt dokumentum törléséhez.

#### <a name="query-for-documents"></a>Dokumentumok lekérdezése

- Szerkessze a dokumentum szűrőjét egy [SQL-lekérdezés](how-to-sql-query.md) beírásával, majd válassza az **alkalmaz**lehetőséget.

    :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Adott dokumentumok lekérdezése":::

### <a name="graph-management"></a>Gráfok kezelése

#### <a name="create-and-modify-vertex"></a>Csúcspont létrehozása és módosítása

1. Új csúcspont létrehozásához nyissa meg a **Graph** elemet a bal oldali ablakban, válassza az **új csúcspont**lehetőséget, szerkessze a tartalmat, majd kattintson **az OK gombra**.
2. Egy meglévő csúcspont módosításához válassza a toll ikont a jobb oldali ablaktáblán.

    :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Gráf csúcspontjának módosítása":::

#### <a name="delete-a-graph"></a>Gráf törlése

- Csúcspont törléséhez válassza a csúcspont neve melletti Lomtár ikont.

#### <a name="filter-for-graph"></a>Gráfok szűrése

- Szerkessze a Graph szűrőt egy [Gremlin-lekérdezés](gremlin-support.md) megadásával, majd válassza a **szűrő alkalmazása**lehetőséget.

    :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Gráf-lekérdezés futtatása":::

### <a name="table-management"></a>Táblakezelés

#### <a name="create-and-modify-table"></a>Tábla létrehozása és módosítása

1. Új tábla létrehozásához nyissa meg az **entitások** elemet a bal oldali ablakban, válassza a **Hozzáadás**lehetőséget, szerkessze az **entitás hozzáadása** párbeszédpanelen található tartalmat, adja hozzá a tulajdonságot a **tulajdonság hozzáadása**gombra kattintva, majd válassza a **Beszúrás**lehetőséget.
2. Egy tábla módosításához válassza a **Szerkesztés**lehetőséget, módosítsa a tartalmat, majd válassza a **frissítés**lehetőséget.

    :::image type="content" source="./media/storage-explorer/table.png" alt-text="Tábla létrehozása és módosítása":::

#### <a name="import-and-export-table"></a>Táblák importálása és exportálása

1. Az importáláshoz kattintson az **Importálás** gombra, és válasszon ki egy meglévő táblát.
2. Az exportáláshoz válassza az **Exportálás** gombot, és válasszon egy célhelyet.

    :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Tábla importálása vagy exportálása":::

#### <a name="delete-entities"></a>Entitások törlése

- Válassza ki az entitásokat, és kattintson a **Törlés**gombra.

    :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Tábla törlése":::

#### <a name="query-table"></a>Táblák lekérdezése

- Kattintson a **lekérdezés** gombra, a bemeneti lekérdezési feltételre, majd válassza a **lekérdezés végrehajtása** gombot. Zárja be a Lekérdezés panelt a **Lekérdezés bezárása** gombbal.

    :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Adatok lekérdezése a táblából":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Tárolt eljárások, eseményindítók és felhasználói függvények kezelése

* Tárolt eljárás létrehozásához a bal oldali fában kattintson a jobb gombbal a **tárolt eljárás**elemre, válassza a **tárolt eljárás létrehozása**elemet, adjon meg egy nevet a bal oldalon, írja be a tárolt eljárás parancsfájljait a jobb oldali ablakban, majd válassza a **Létrehozás**lehetőséget.
* A meglévő tárolt eljárásokat úgy is szerkesztheti, ha duplán kattint, majd a frissítés után a **frissítés** gombra kattint, majd a módosítás megszakításához kiválasztja az **Elvetés** lehetőséget.

    :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Tárolt eljárások létrehozása és kezelése":::
* A **Triggerekhez** és **Felhasználói függvényekhez** használható műveletek hasonlóak a **Tárolt eljárások** esetében használhatókhoz.

## <a name="troubleshooting"></a>Hibaelhárítás

Az [Azure Cosmos DB in Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) egy önálló alkalmazás, amely lehetővé teszi az Azure- és Sovereign-felhőkben üzemeltetett Azure Cosmos DB-fiókokhoz való csatlakozást Windows, macOS és Linux rendszerekről. Az alkalmazás az Azure Cosmos DB-entitások kezelését, az adatok módosítását, valamint a tárolt eljárások és triggerek frissítését is lehetővé teszi, és olyan Azure-entitásokat is biztosít, mint a tárolóblobok és üzenetsorok.

Ezek az Azure Cosmos DB in Storage Explorer leggyakoribb problémáinak megoldásai.

### <a name="sign-in-issues"></a>Bejelentkezési problémák

Mielőtt továbblépne, indítsa újra az alkalmazást, és ellenőrizze, hogy megoldható-e a probléma.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Önaláírt tanúsítványok a tanúsítványláncokban

Ennek a hibának többféle oka is lehet, a leggyakoribbak a következők:

+ Egy *transzparens proxy*mögött van, ami azt jelenti, hogy valaki (például az informatikai részleg) elfogja a https-forgalmat, visszafejti, majd egy önaláírt tanúsítvánnyal titkosítja.

+ Olyan szoftvereket futtat, mint például a víruskereső szoftver, amely egy önaláírt TLS/SSL-tanúsítvány behelyezése a kapott HTTPS-üzenetbe.

Amikor a Storage Explorer egy ilyen „önaláírt tanúsítványt” észlel, onnantól nem tudja megállapítani, hogy a fogadott HTTPS-üzenetet módosították-e. Ha azonban rendelkezik az önaláírt tanúsítvány egy példányával, akkor beállíthatja, hogy a Storage Explorer megbízzon benne. Ha nem tudja biztosan, hogy ki szúrja be a tanúsítványt, akkor megpróbálhatja kideríteni az alábbi lépések végrehajtásával:

1. OpenSSL telepítése
     - [Windows:](https://slproweb.com/products/Win32OpenSSL.html) (bármelyik egyszerűsített verzió megfelel)
     - Mac és Linux: elvileg eleve mellékelve van az operációs rendszerhez.
2. OpenSSL futtatása
    - Windows: lépjen a telepítési könyvtárba, azon belül a **/bin/** mappába, majd kattintson duplán az **openssl.exe** fájlra.
    - Mac és Linux: futtassa az **openssl** parancsot egy terminálról.
3. Hajtsa végre az `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha nem tudja biztosan, melyik tanúsítvány önaláírt, akkor figyeljen arra, hogy melyiknél azonos a tárgy („s:”) és a kiállító („i:”).
5.  Ha talált önaláírt tanúsítványokat, másolja ki egyenként a tartalmukat a **---BEGIN CERTIFICATE---** sorral kezdve és az **---END CERTIFICATE---** sorral bezárólag egy-egy új .cer fájlba.
6.  Nyissa meg Storage Explorer, majd **Edit**lépjen az  >  **SSL-tanúsítványok**  >  **importálása tanúsítványok importálása**menüpontra. A fájlkeresővel keresse meg és nyissa meg a létrehozott .cer-fájlokat.

Ha a fenti lépésekkel nem talál önaláírt tanúsítványokat, küldjön visszajelzést, hogy további segítséget kapjon.

#### <a name="unable-to-retrieve-subscriptions"></a>Az előfizetéseket nem sikerül lekérni

Ha nem tudja lekérni az előfizetéseit, miután sikeresen bejelentkezett:

- Ellenőrizze, hogy a fiókja rendelkezik-e hozzáféréssel az előfizetésekhez a [Azure Portalba](https://portal.azure.com/) való bejelentkezéssel
- Ügyeljen arra, hogy a megfelelő környezettel jelentkezzen be ([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Germany](https://portal.microsoftazure.de/), [Azure US Government](https://portal.azure.us/) vagy Egyéni környezet/Azure Stack).
- Ha proxyt használ, ügyeljen arra, hogy a Storage Explorer-proxy megfelelően legyen beállítva.
- Próbálja meg eltávolítani és újra hozzáadni a fiókot.
- Próbálja meg azt, hogy törli a következő fájlokat a kezdőkönyvtárból (például: C:\Users\ContosoUser), majd újra hozzáadja a fiókot:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Tartsa figyelemmel, hogy a fejlesztői eszközök konzolján (F12) nem jelennek-e meg hibaüzenetek.

:::image type="content" source="./media/storage-explorer/console.png" alt-text="Az esetleges hibákért keresse a fejlesztői eszközök konzolt.":::

#### <a name="unable-to-see-the-authentication-page"></a>A hitelesítési oldalt nem sikerül megjeleníteni

Ha nem látja a hitelesítési oldalt:

- A kapcsolat sebességétől függően eltarthat egy kis ideig, hogy a bejelentkezési oldal betöltsön. Várjon legalább egy percet, mielőtt bezárná a hitelesítési párbeszédablakot.
- Ha proxyt használ, ügyeljen arra, hogy a Storage Explorer-proxy megfelelően legyen beállítva.
- Nyissa meg a fejlesztői konzolt az F12 billentyűvel. Figyeljen a fejlesztői konzol válaszaira, és keressen bennük valamilyen támpontot arra vonatkozóan, hogy miért nem működik a hitelesítés.

#### <a name="cannot-remove-account"></a>Fiók eltávolítása sikertelen

Ha nem tud eltávolítani egy fiókot, vagy ha az újrahitelesítési hivatkozás nem működik, a következőkkel próbálkozhat.

- Próbálja meg azt, hogy törli a következő fájlokat a kezdőkönyvtárból, majd újra hozzáadja a fiókot:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Ha szeretné eltávolítani az SAS-hez csatolt Storage-erőforrásokat, törölje a következőket:
  - Windows rendszeren az %AppData%/StorageExplorer mappát
  - /Users/<your_name>/Library/Application Support-támogatás/StorageExplorer Mac rendszerhez
  - Linux rendszeren a ~/.config/StorageExplorer mappát
  - Ha ezeket a fájlokat törli, **újra meg kell adnia minden hitelesítő adatát**.


### <a name="httphttps-proxy-issue"></a>Http/Https-proxyval kapcsolatos probléma

A bal oldali fában nem listázhatók az Azure Cosmos DB-csomópontok, amikor http/https-proxyt konfigurálunk az ASE-ben. Jelenleg áthidaló megoldásként használhatja az Azure Cosmos DB-adatkezelőt az Azure Portalon.

### <a name="development-node-under-local-and-attached-node-issue"></a>A „Helyi és csatolt” csomópont alatti „Fejlesztés” csomópont problémája

Nincs válasz a bal oldali fában a "helyi és csatolt" csomópont alatti "fejlesztés" csomópont kiválasztását követően.  Ennek így is kell lennie. Az Azure Cosmos DB helyi emulátora csak a következő kiadástól lesz támogatott.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Fejlesztési csomópont":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Nem sikerül csatolni az Azure Cosmos DB-fiókot a „Helyi és csatolt” csomópont alatt

Ha az alábbi hibát látja, amikor csatolni próbál egy Azure Cosmos DB-fiókot a „Helyi és csatolt” csomópont alatt, akkor ellenőrizze, hogy a használt kapcsolati sztring megfelelő-e.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Azure Cosmos DB csatolása a „Helyi és csatolt” csomópont alatt sikertelen":::

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB-csomópont kibontása sikertelen

Az alábbi hibával találkozhat, amikor megpróbálja kibontani a bal oldali fa csomópontjait.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Azure Cosmos DB-csomópont kibontása sikertelen":::

Próbálkozzon a következőkkel:

- Ellenőrizze, hogy az Azure Cosmos DB-fiók nem áll-e kiépítés alatt, és próbálkozzon újra, miután a fiók sikeresen létrejött.
- Ha a fiók a „gyorselérési” vagy a „Helyi és csatolt” csomópont alatt található, akkor ellenőrizze, hogy nem lett-e törölve. Ha törölve lett, akkor manuálisan kell eltávolítania a csomópontot.

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a következő videót az Azure Cosmos DB Azure Storage Explorerben való használatáról: [Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) (Az Azure Cosmos DB használata Azure Storage Explorerben).
* További információk a Storage Explorerrel és további szolgáltatások csatlakoztatásával kapcsolatban: [Ismerkedés a Storage Explorer alkalmazással](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
