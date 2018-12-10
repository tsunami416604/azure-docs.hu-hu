---
title: Azure Storage Explorer használatával Azure Cosmos DB-erőforrások kezelése
description: Ismerje meg, hogyan csatlakozhat az Azure Cosmos DB és az erőforrásainak kezelése az Azure Storage Explorer használatával.
author: Jejiang
tags: Azure Cosmos DB
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jejiang
ms.custom: seodec18
ms.openlocfilehash: 1ce483a88c1f57912dfe30efa98f46335e97c01c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138126"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-storage-explorer"></a>Azure Storage Explorer használatával Azure Cosmos DB-erőforrások kezelése

Az Azure Cosmos DB Azure Storage Explorerben történő használata lehetővé teszi a felhasználók számára az Azure Cosmos DB-entitások kezelését, az adatok módosítását, valamint a tárolt eljárások és eseményindítók frissítését olyan Azure-entitások mellett, mint a tárolóblobok és üzenetsorok. Mostantól ugyanazon eszközzel, egy helyen kezelheti a különböző Azure-entitásokat. Az Azure Storage Explorer jelenleg SQL-, MongoDB-, Graph- és Table-fiókokat támogat.


## <a name="prerequisites"></a>Előfeltételek

Azure Cosmos DB-fiók az SQL API-hoz<!--or MongoDB API-->. Ha nem rendelkezik fiókkal, az Azure Portalon létrehozhat egyet az [Azure Cosmos DB: SQL API-webalkalmazás létrehozása .NET-tel és az Azure Portallal](create-sql-api-dotnet.md) című cikkben leírtaknak megfelelően.

## <a name="installation"></a>Telepítés

Az Azure Storage Explorer legújabb elemei innen telepíthetők: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Már a Windows-, Linux- és MAC-alapú verzió is támogatott.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

1. Az **Azure Storage Explorer** telepítését követően kattintson a bal oldali **beépülő modul** ikonra, ahogy az alábbi képen is látható:
       
   ![Beépülő modul ikon](./media/storage-explorer/plug-in-icon.png)
 
2. Válassza az **Azure-fiók hozzáadása** elemet, majd kattintson a **Bejelentkezés** gombra.

   ![Csatlakozás Azure-előfizetéshez](./media/storage-explorer/connect-to-azure-subscription.png)

2. Az **Azure bejelentkezési** párbeszédpanelen válassza a **Bejelentkezés** elemet, majd adja meg Azure hitelesítő adatait.

    ![Bejelentkezés](./media/storage-explorer/sign-in.png)

3. Válassza ki az előfizetést a listából, majd kattintson az **Alkalmaz** gombra.

    ![Alkalmaz](./media/storage-explorer/apply-subscription.png)

    Az Explorer panel frissül, és megjeleníti a kiválasztott előfizetéshez tartozó fiókokat.

    ![Fióklista](./media/storage-explorer/account-list.png)

    Sikeresen csatlakoztatta a **Cosmos DB-fiókot** az Azure-előfizetéshez.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Csatlakozás az Azure Cosmos DB-hez kapcsolati sztring használatával

Az Azure Cosmos DB-hez történő csatlakozás másik módja a kapcsolati sztring használata. Az alábbi lépéseket követve csatlakozhat kapcsolati sztringgel.

1. Keresse meg a **Helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal a **Cosmos DB-fiókok** elemre, majd válassza a **Csatlakozás a Cosmos DB-hez…** lehetőséget.

    ![Csatlakozás a Cosmos DB-hez kapcsolati sztringgel](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Egyelőre csak az SQL és a Table API-t támogatja. Válassza ki az API-t, illessze be a **kapcsolati sztringet**, adja meg a **fiókcímkét**, az összesítés megtekintéséhez kattintson a **Tovább** gombra, majd az Azure Cosmos DB-fiók csatlakoztatásához kattintson a **Csatlakozás** parancsra. A kapcsolati sztring lekérésével kapcsolatos információk: [A kapcsolati sztring lekérése](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Kapcsolati karakterlánc](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Csatlakozás az Azure Cosmos DB-hez helyi emulátorral

A következő lépésekkel csatlakozhat az Azure Cosmos DB-hez az Emulator használatával. Ez a megoldás egyelőre csak az SQL-fiókot támogatja.

1. Telepítse az Emulatort, és indítsa el. Az Emulator telepítéséról lásd a [Cosmos DB Emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) témakörét.

2. Keresse meg a **Helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal a **Cosmos DB-fiókok** elemre, majd válassza a **Csatlakozás a Cosmos DB Emulatorhoz…** lehetőséget.

    ![Csatlakozás a Cosmos DB-hez az Emulatorral](./media/storage-explorer/emulator-entry.png)

3. Egyelőre csak az SQL API-t támogatja. Illessze be a **kapcsolati sztringet**, adja meg a **fiókcímkét**, az összesítés megtekintéséhez kattintson a **Tovább** gombra, majd az Azure Cosmos DB-fiók csatlakoztatásához kattintson a **Csatlakozás** parancsra. A kapcsolati sztring lekérésével kapcsolatos információk: [A kapcsolati sztring lekérése](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Csatlakozás a Cosmos DB-hez az Emulatorral párbeszédpanel](./media/storage-explorer/emulator-dialog.png)


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

     ![Megnyitás a portálon](./media/storage-explorer/open-in-portal.png)

* Emellett Azure Cosmos DB-fiókot, -adatbázist és -gyűjteményt is hozzáadhat a **gyorselérési** eszköztárhoz.
* A **Keresés innen** funkció lehetővé teszi a kulcsszavaknak a kijelölt útvonalon történő keresését.

    ![keresés innen:](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Adatbázisok és gyűjtemények kezelése
#### <a name="create-a-database"></a>Adatbázis létrehozása 
-   Kattintson a jobb gombbal az Azure Cosmos DB-fiókra, válassza az **Adatbázis létrehozása** elemet, adja meg az adatbázis nevét, majd a befejezéshez nyomja le az **Enter** billentyűt.
       
    ![Adatbázis létrehozása](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Adatbázis törlése
- Kattintson a jobb gombbal az adatbázisra, kattintson az **Adatbázis törlése** elemre, majd kattintson az **Igen** gombra a felugró ablakban. A rendszer törli az adatbázis-csomópontot, és automatikusan frissíti az Azure Cosmos DB-fiókot.

    ![1. adatbázis törlése](./media/storage-explorer/delete-database1.png)  

    ![2. adatbázis törlése](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Gyűjtemény létrehozása
1. Kattintson a jobb gombbal az adatbázisra, válassza a **Gyűjtemény létrehozása** elemet, majd adja meg a következő információkat, amilyen például **Gyűjtemény azonosítója**, a **Tárkapacitás** stb. A befejezéshez kattintson az **OK** gombra. 

    ![1. gyűjtemény létrehozása](./media/storage-explorer/create-collection.png)

    ![2. gyűjtemény létrehozása](./media/storage-explorer/create-collection2.png) 

2. Válassza a **Korlátlan** lehetőséget, hogy megadhassa a partíciókulcsot, majd kattintson az **OK** gombra.

    Ha partíciókulcsot használt a gyűjtemény létrehozásakor, akkor a létrehozást követően a partíciókulcs értéke nem módosítható a gyűjteményben.

    ![Partíciókulcs](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Gyűjtemény törlése
- Kattintson a jobb gombbal a gyűjteményre, kattintson a **Gyűjtemény törlése** elemre, majd kattintson az **Igen** gombra a felugró ablakban. 

    A rendszer törli a gyűjteménycsomópontot, és automatikusan frissíti az adatbázist.

    ![Gyűjtemény törlése](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Dokumentumok kezelése

#### <a name="create-and-modify-documents"></a>Dokumentumok létrehozása és módosítása
- Új dokumentum létrehozásához kattintson a bal oldali ablakban található **Dokumentumok** elemre, kattintson az **Új dokumentum létrehozása** lehetőségre, szerkessze a tartalmakat a jobb oldali ablaktáblában, majd kattintson a **Mentés** gombra. Frissíthet meglévő dokumentumot is, amelyet a **Mentés** gombra kattintva menthet. A módosítások elvetéséhez kattintson az **Elvetés** gombra.

    ![Dokumentum](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Dokumentum törlése
- Kattintson a **Törlés** gombra a kijelölt dokumentum törléséhez.

#### <a name="query-for-documents"></a>Dokumentumok lekérdezése
- Szerkessze a dokumentumszűrőt egy [SQL-lekérdezés](how-to-sql-query.md) beírásával, majd kattintson az **Alkalmaz** gombra.

    ![Dokumentumszűrő](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Gráfok kezelése

#### <a name="create-and-modify-vertex"></a>Csúcspont létrehozása és módosítása
1. Új csúcspont létrehozásához nyissa meg a bal oldali ablakban található **Gráf** elemet, kattintson az **Új csúcspont** lehetőségre, szerkessze a tartalmakat, majd kattintson az **OK** gombra.    
2. Meglévő csúcspont módosításához kattintson a jobb oldali panelen látható toll ikonra.   

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Gráf törlése
- Csúcspont törléséhez kattintson a csúcspont neve mellet látható lomtár ikonra.

#### <a name="filter-for-graph"></a>Gráfok szűrése
- Szerkessze a gráfszűrőt egy [gremlin-lekérdezés](gremlin-support.md) megadásával, majd kattintson a **Szűrő alkalmazása** gombra.

    ![Gráfszűrő](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Táblák kezelése

#### <a name="create-and-modify-table"></a>Tábla létrehozása és módosítása
1. Új tábla létrehozásához nyissa meg a bal oldali ablakban található **Entitások** elemet, kattintson a **Hozzáadás** gombra, szerkessze az **Entitás hozzáadása** párbeszédpanel tartalmát, adjon hozzá tulajdonságokat a **Tulajdonság hozzáadása** gombra kattintva, végül kattintson a **Beszúrás** elemre.
2. Egy tábla módosításához kattintson a **Szerkesztés** gombra, módosítsa a tartalmakat, majd kattintson a **Frissítés** elemre.

    ![Tábla](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Táblák importálása és exportálása
1. Az importáláshoz kattintson az **Importálás** gombra, és válasszon ki egy meglévő táblát.
2. Az exportáláshoz kattintson az **Exportálás** gombra, és válasszon egy célhelyet.

    ![Tábla importálása és exportálása](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Entitások törlése
- Jelölje ki az entitásokat, majd kattintson a **Törlés** gombra.

    ![Tábla törlése](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Táblák lekérdezése
- Kattintson a **Lekérdezés** gombra, adja meg a lekérdezés feltételeit, majd kattintson a **Lekérdezés végrehajtása** gombra. Zárja be a Lekérdezés panelt a **Lekérdezés bezárása** gombbal.

    ![Tábla lekérdezése](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Tárolt eljárások, eseményindítók és felhasználói függvények kezelése
* Tárolt eljárás létrehozásához kattintson a jobb gombbal a bal oldali fában található **Tárolt eljárás** elemre, válassza a **Tárolt eljárás létrehozása** lehetőségre, adjon meg egy nevet a bal oldalon, írja be a tárolt eljárás szkriptjeit a jobb oldali ablakban, majd kattintson a **Létrehozás** gombra. 
* Meglévő tárolt eljárás szerkesztéséhez kattintson duplán az eljárásra, végezze el a frissítést, majd kattintson a **Frissítés** gombra a mentéshez, vagy kattintson az **Elvetés** gombra a módosítások visszavonásához.

    ![Tárolt eljárás](./media/storage-explorer/stored-procedure.png)
* A **Triggerekhez** és **Felhasználói függvényekhez** használható műveletek hasonlóak a **Tárolt eljárások** esetében használhatókhoz.

## <a name="troubleshooting"></a>Hibaelhárítás

Az [Azure Cosmos DB in Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) egy önálló alkalmazás, amely lehetővé teszi az Azure- és Sovereign-felhőkben üzemeltetett Azure Cosmos DB-fiókokhoz való csatlakozást Windows, macOS és Linux rendszerekről. Az alkalmazás az Azure Cosmos DB-entitások kezelését, az adatok módosítását, valamint a tárolt eljárások és triggerek frissítését is lehetővé teszi, és olyan Azure-entitásokat is biztosít, mint a tárolóblobok és üzenetsorok.

Ezek az Azure Cosmos DB in Storage Explorer leggyakoribb problémáinak megoldásai.

### <a name="sign-in-issues"></a>Bejelentkezési problémák

Mielőtt továbblépne, indítsa újra az alkalmazást, és ellenőrizze, hogy megoldható-e a probléma.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Önaláírt tanúsítványok a tanúsítványláncokban

Ennek a hibának többféle oka is lehet, a leggyakoribbak a következők:

+ Ön mögött egy *transzparens proxy*, ami azt jelenti, hogy valaki (például az informatikai részleg) elfogja a HTTPS-forgalmat, visszafejti azt, és majd újra titkosítja egy önaláírt tanúsítvány használatával.

+ Ön olyan szoftvereket futtat, például egy víruskereső szoftvert, amely a fogadott HTTPS-üzenetekbe önaláírt SSL-tanúsítványokat szúr be.

Amikor a Storage Explorer egy ilyen „önaláírt tanúsítványt” észlel, onnantól nem tudja megállapítani, hogy a fogadott HTTPS-üzenetet módosították-e. Ha azonban rendelkezik az önaláírt tanúsítvány egy példányával, akkor beállíthatja, hogy a Storage Explorer megbízzon benne. Ha nem tudja biztosan, hogy ki szúrja be a tanúsítványt, akkor megpróbálhatja kideríteni az alábbi lépések végrehajtásával:

1. Telepítse az Open SSL-t.
     - [Windows:](https://slproweb.com/products/Win32OpenSSL.html) (bármelyik egyszerűsített verzió megfelel)
     - Mac és Linux: elvileg eleve mellékelve van az operációs rendszerhez.
2. Futtassa az Open SSL-t.
    - Windows: lépjen a telepítési könyvtárba, azon belül a **/bin/** mappába, majd kattintson duplán az **openssl.exe** fájlra.
    - Mac és Linux: futtassa az **openssl** parancsot egy terminálról.
3. Hajtsa végre az `s_client -showcerts -connect microsoft.com:443` parancsot.
4. Keresse meg az önaláírt tanúsítványokat. Ha nem tudja biztosan, melyik tanúsítvány önaláírt, akkor figyeljen arra, hogy melyiknél azonos a tárgy („s:”) és a kiállító („i:”).
5.  Ha talált önaláírt tanúsítványokat, másolja ki egyenként a tartalmukat a **---BEGIN CERTIFICATE---** sorral kezdve és az **---END CERTIFICATE---** sorral bezárólag egy-egy új .cer fájlba.
6.  Nyissa meg a Storage Explorert, majd lépjen a **Szerkesztés** > **SSL-tanúsítványok** > **Tanúsítványok importálása** menüpontra. A fájlkeresővel keresse meg és nyissa meg a létrehozott .cer-fájlokat.

Ha a fenti lépésekkel nem talál önaláírt tanúsítványokat, küldjön visszajelzést, hogy további segítséget kapjon.

#### <a name="unable-to-retrieve-subscriptions"></a>Az előfizetéseket nem sikerül lekérni

Ha nem tudja lekérni az előfizetéseit, miután sikeresen bejelentkezett:

- Ellenőrizze, hogy a fióknak van-e hozzáférése az előfizetésekhez. Ehhez jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
- Ügyeljen arra, hogy a megfelelő környezettel jelentkezzen be ([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Germany](https://portal.microsoftazure.de/), [Azure US Government](https://portal.azure.us/) vagy Egyéni környezet/Azure Stack).
- Ha proxyt használ, ügyeljen arra, hogy a Storage Explorer-proxy megfelelően legyen beállítva.
- Próbálja meg eltávolítani és újra hozzáadni a fiókot.
- Próbálja meg azt, hogy törli a következő fájlokat a kezdőkönyvtárból (például: C:\Users\ContosoUser), majd újra hozzáadja a fiókot:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Tartsa figyelemmel, hogy a fejlesztői eszközök konzolján (F12) nem jelennek-e meg hibaüzenetek.

![konzol](./media/storage-explorer/console.png)

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
  - Mac rendszeren a /Users/<felhasználó_neve/Library/Applicaiton SUpport/StorageExplorer mappát
  - Linux rendszeren a ~/.config/StorageExplorer mappát
  - Ha ezeket a fájlokat törli, **újra meg kell adnia minden hitelesítő adatát**.


### <a name="httphttps-proxy-issue"></a>Http/Https-proxyval kapcsolatos probléma

A bal oldali fában nem listázhatók az Azure Cosmos DB-csomópontok, amikor http/https-proxyt konfigurálunk az ASE-ben. Ez egy ismert probléma, amelyet a következő kiadásra kijavítunk. Jelenleg áthidaló megoldásként használhatja az Azure Cosmos DB-adatkezelőt az Azure Portalon. 

### <a name="development-node-under-local-and-attached-node-issue"></a>A „Helyi és csatolt” csomópont alatti „Fejlesztés” csomópont problémája

A rendszer nem reagál arra, ha a bal oldali fában a „Helyi és csatolt” csomópont alatti „Fejlesztés” csomópontra kattintunk.  Ennek így is kell lennie. Az Azure Cosmos DB helyi emulátora csak a következő kiadástól lesz támogatott.

![Fejlesztési csomópont](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Nem sikerül csatolni az Azure Cosmos DB-fiókot a „Helyi és csatolt” csomópont alatt

Ha az alábbi hibát látja, amikor csatolni próbál egy Azure Cosmos DB-fiókot a „Helyi és csatolt” csomópont alatt, akkor ellenőrizze, hogy a használt kapcsolati sztring megfelelő-e.

![Azure Cosmos DB csatolása a „Helyi és csatolt” csomópont alatt sikertelen](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB-csomópont kibontása sikertelen

Az alábbi hibával találkozhat, amikor megpróbálja kibontani a bal oldali fa csomópontjait. 

![Kibontási hiba](./media/storage-explorer/expand-error.png)

Próbálkozzon a következőkkel:

- Ellenőrizze, hogy az Azure Cosmos DB-fiók nem áll-e kiépítés alatt, és próbálkozzon újra, miután a fiók sikeresen létrejött.
- Ha a fiók a „gyorselérési” vagy a „Helyi és csatolt” csomópont alatt található, akkor ellenőrizze, hogy nem lett-e törölve. Ha törölve lett, akkor manuálisan kell eltávolítania a csomópontot.

## <a name="contact-us"></a>Kapcsolat

Ha a megoldások egyike sem működik, küldjön egy e-mailt az Azure Cosmos DB fejlesztőcsapatának ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) a probléma részleteivel, és megpróbáljuk megtalálni a megoldást.

## <a name="next-steps"></a>További lépések

* Tekintse meg a következő videót az Azure Cosmos DB Azure Storage Explorerben való használatáról: [Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) (Az Azure Cosmos DB használata Azure Storage Explorerben).
* További információk a Storage Explorerrel és további szolgáltatások csatlakoztatásával kapcsolatban: [Ismerkedés a Storage Explorer alkalmazással](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

