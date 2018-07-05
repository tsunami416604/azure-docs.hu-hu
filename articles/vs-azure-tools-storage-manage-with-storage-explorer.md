---
title: Storage Explorer használatának első lépései |} A Microsoft Docs
description: Az Azure storage-erőforrások kezelése a Storage Explorerben
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: b41b1fcb437dac381a17bef4f1e5a7cebe213b98
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435915"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer használatának első lépései

## <a name="overview"></a>Áttekintés

Az Azure Storage Explorer egy önálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken. Ebből a cikkből megtudhatja, csatlakoztatása és kezelése az Azure storage-fiókok, számos különféle módot.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Előfeltételek

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Az Azure Storage Explorer a Windows alábbi verzióit támogatják:

* A Windows 10-es (ajánlott)
* Windows 8
* Windows 7

Összes verziója a Windows, a .NET-keretrendszer 4.6.2-es vagy újabb verzió szükséges.

[A Storage Explorer letöltése és telepítése](http://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Az Azure Storage Explorer macOS következő verzióiban támogatott:

* macOS 10.12 "Sierra" és az újabb verziók

[A Storage Explorer letöltése és telepítése](http://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Az Azure Storage Explorert a következő Linux-disztribúciókon támogatják:

* Ubuntu 16.04 x64 (ajánlott)
* Ubuntu 17.10 x64
* Ubuntu 14.04 x64

Az Azure Storage Explorer a többi disztribúciókon is működhet, azonban csak azokat a fent felsorolt hivatalosan támogatott.

Az alábbi függőségeket és kódtárak futtatható az Azure Storage Explorer linuxon is kell rendelkeznie:

* [A .NET core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* libsecret (Megjegyzés: libsecret-1.so.0 kell lennie a gépen elérhető. Ha libsecret telepítve van egy másik verziója, megpróbálhatja a .so fájl helyreállítható összekapcsolása libsecret-1.so.0)
* libgconf-2-4
* Naprakész GCC

Az Azure Storage Explorer [kibocsátási megjegyzések](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) néhány disztribúciók adott lépést tartalmaznak.

[A Storage Explorer letöltése és telepítése](http://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Csatlakozás egy tárfiókhoz vagy -szolgáltatáshoz

A Storage Explorer számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz. Megteheti például a következőt:

* Csatlakozhat az Azure-előfizetéséhez kapcsolt tárfiókokhoz.
* Csatlakozhat olyan tárfiókokhoz és szolgáltatásokhoz, amelyek más Azure-előfizetésekből vannak megosztva.
* Csatlakozhat egy helyi tárolóhoz az Azure Storage Emulator használatával, és kezelheti azt.

Emellett használhatja a tárfiókokat a globális és az országos Azure-ban:

* [Csatlakozás Azure-előfizetéshez](#connect-to-an-azure-subscription): Az Azure-előfizetéséhez tartozó tárolási erőforrások kezelése.
* [Munkavégzés helyi fejlesztési tárterülettel](#work-with-local-development-storage): Helyi tárterület kezelése az Azure Storage Emulator használatával.
* [Külső tárterület csatolása](#attach-or-detach-an-external-storage-account): Más Azure-előfizetések vagy az országos Azure-felhők alá tartozó tárolási erőforrások kezelése a tárfiók fióknevének, kulcsának és végpontjainak használatával.
* [Tárfiók csatolása SAS használatával](#attach-storage-account-using-sas): Más Azure-előfizetések alá tartozó tárolási erőforrások kezelése közös hozzáférésű jogosultságkód (SAS) használatával.
* [Szolgáltatás csatolása SAS használatával](#attach-service-using-sas): Más Azure-előfizetések alá tartozó adott tárolási szolgáltatás (blob tároló, üzenetsor vagy tábla) kezelése SAS használatával.
* [Csatlakozás az Azure Cosmos DB-fiók egy kapcsolati karakterlánc használatával](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): kezelése Cosmos DB-fiók kapcsolati karakterlánc használatával.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

> [!NOTE]
> Ha nincs Azure-fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja Visual Studio-előfizetése kiemelt előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. A Storage Explorerben válassza ki a **fiókok kezelése** , nyissa meg a **fiók felügyeleti Panel**.

    ![Fiókok kezelése][1]

2. A bal oldali panelen most már megjeleníti az összes Azure-fiókot, amelybe bejelentkezett. Egy másik fiókhoz csatlakozáshoz válassza **-fiók hozzáadása**

3. Ha szeretne bejelentkezni egy országos felhőben vagy az Azure Stackkel, kattintson a a **Azure-környezet** legördülő listájában válassza ki a használni kívánt melyik Azure-felhőben. Miután kiválasztotta a környezetben, kattintson a **bejelentkezés...**  gombra. Bejelentkezés az Azure Stackhez, ha [Storage Explorer csatlakoztatása Azure Stack-előfizetéshez](azure-stack/user/azure-stack-storage-connect-se.md) további információt.

    ![Jelentkezzen be a beállítást][2]

4. Miután sikeresen bejelentkezett egy Azure-fiókkal, a fiók és a fiókhoz társított Azure-előfizetések kerülnek a bal oldali panelen. Válassza ki az Azure-előfizetést, amelyet szeretne dolgozni, és válassza ki **alkalmaz** (kijelölése **minden előfizetés:** állapotsorának összes vagy a listában szereplő Azure-előfizetések kiválasztásával).

    ![Azure-előfizetések kiválasztása][3]

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.

    ![Kiválasztott Azure-előfizetések][4]

## <a name="work-with-local-development-storage"></a>Munkavégzés helyi fejlesztési tárterülettel

A Storage Explorer az emulator használatával helyi tárterületen is működik. Ez a megközelítés lehetővé teszi szimulálása az Azure Storage használatához egy Azure-ban üzembe helyezett tárfiókra feltétlenül nélkül.

Helyi storage emulator támogatott 1.1.0-s kezdve minden platformon. Storage Explorer csatlakozhat bármely emulált szolgáltatás az alapértelmezett helyi tárterület-végpontokra irányuló figyel.

> [!NOTE]
> Tárolási szolgáltatások és funkciók támogatása széles körben emulátor válaszaitól függően változhat. Győződjön meg arról, hogy az emulátor támogatja-e a szolgáltatásokat és funkciókat kíván dolgozni.

1. Az emulátor, az alapértelmezett végpontok a figyelni kívánt konfigurálása.

   Az emulált szolgáltatás | Alapértelmezett végpont
   -----------------|-------------------------
   Blobok            | `http://127.0.0.1:10000`
   Üzenetsorok           | `http://127.0.0.1:10001`
   Táblák           | `http://127.0.0.1:10002`

2. Indítsa el az emulátort.

3. A Storage Explorer bal oldali ablaktábláján, bontsa ki a **(helyi és csatolt)** > **Tárfiókok** > **(fejlesztés)** csomópont.

   Ez a csomópont használatával a helyi blobokat, üzenetsorokat és táblákat létrehozását és használatát. Az egyes tárfióktípusok kezelésével kapcsolatos, tekintse meg a következő útmutatókat:

   * [Azure Blob storage-erőforrások kezelése](vs-azure-tools-storage-explorer-blobs.md)
   * [Az Azure File storage-erőforrások kezelése](vs-azure-tools-storage-explorer-files.md)

> [!NOTE]
> Storage Explorer csak támogatja az alapértelmezett végpontok használata helyi emulátor csatlakozik. Az emulator indításakor ellenőrizze, hogy csak az alapértelmezett végpontok vannak konfigurálva.

## <a name="attach-or-detach-an-external-storage-account"></a>Külső tárfiók csatolása vagy leválasztása

A Storage Explorer használatával csatolhat segítségével külső tárfiókokat, hogy a storage-fiókok egyszerűen megoszthatóak. Ez a szakasz külső tárfiókok csatolását (és leválasztását) írja le.

### <a name="get-the-storage-account-credentials"></a>Tárfiók hitelesítő adatainak lekérése

Külső tárfiókok megosztásához az adott fiók tulajdonosának kell először kérje le a fiók a hitelesítő adatokat (a fióknevet és a kulcsot) és oszthatja meg, hogy az adatokat a személy, aki szeretné csatlakoztatni a mondta fiók. A tárfiók hitelesítő adatait az Azure Portalon keresztül szerezheti be a következő lépések végrehajtásával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **Tallózás** lehetőséget.

3. Válassza a **Tárfiókok** lehetőséget.

4. Listájában **Tárfiókok**, válassza ki a kívánt tárfiókot.

5. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet.

    ![Hozzáférési kulcs lehetőség][7]

6. Másolás a **tárfióknevet** és **key1**.

    ![Elérési kulcs][8]

### <a name="attach-to-an-external-storage-account"></a>Külső tárfiók csatolása

Külső tárfiók csatolásához szükség van a fiók nevére és kulcsára. A „Tárfiók hitelesítő adatainak lekérése” szakasz ismerteti ezen értékek lekérését az Azure Portalról. A portálon azonban a fiókkulcs neve: **kulcs1**. Tehát amikor a Storage Explorer kéri a fiók kulcsára, akkor adja meg a **key1** értéket.

1. A Storage Explorerben nyissa meg a **csatlakoztatása párbeszédpanel**.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Az a **csatlakoztatása párbeszédpanel**, válassza a **tárfiók nevének és kulcsának**

    ![A név és kulcs lehetőséggel hozzáadása][10]

3. Illessze be a fiók nevét a a **fióknév** szöveg, és illessze be a fiókkulcs (a **key1** értéket az Azure Portalról), a **fiókkulcs** szövegmezőbe, és válassza ki **Tovább**.

    ![Név és kulcs lap][11]

    > [!NOTE]
    > Egy országos felhőben egy nevet és a kulcs használatához használja a **tárterület végpontjainak tartománya:** legördülő listájában válassza ki a megfelelő végpontjainak tartománya:
    >
    >

4. A **Kapcsolatok összegzése** párbeszédpanelen ellenőrizze az adatokat. Ha bármin változtatni szeretne, válassza a **Vissza** lehetőséget, és írja be újra a kívánt beállításokat.

5. Kattintson a **Csatlakozás** gombra.

6. Miután a tárfiók sikeresen csatlakoztatva van, a tárfiók megjelenik, **(külső)** a névhez.

    ![Külső tárfiók csatolásának eredménye][12]

### <a name="detach-from-an-external-storage-account"></a>Külső tárfiók leválasztása

1. Kattintson a jobb gombbal a leválasztani kívánt külső tárfiókra, és válassza a **Leválasztás** lehetőséget.

    ![Tár leválasztása lehetőség][13]

2. A megerősítő üzenetben kattintson az **Igen** gombra a külső tárfiók leválasztásának jóváhagyásához.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Tárfiók csatolása egy közös hozzáférésű Jogosultságkód (SAS) használatával

A közös hozzáférésű Jogosultságkód vagy [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), lehetővé teszi a rendszergazda Azure-előfizetés számára biztosítson ideiglenes hozzáférést engedélyezzen a tárfiókhoz anélkül, hogy kellene az Azure-előfizetés hitelesítő adatait.

E forgatókönyv szemléltetésére tegyük fel, hogy az „A” felhasználó valamely Azure-előfizetés rendszergazdája, és hozzáférést szeretne engedélyezni „B” felhasználó számára a tárfiókhoz adott időtartamra és meghatározott engedélyekkel:

1. "A" felhasználó hoz létre egy SAS-kapcsolati karakterláncot, egy adott időtartamra és a kívánt engedélyekkel.

2. "A" felhasználó megosztja a SAS személlyel (esetünkben ebben a példában), és szeretné a tárfiókhoz való hozzáférést.

3. Tártallózó csatolni a fiókot, amely a megadott SAS használatával "a" felhasználó tartozik.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>Hozzon létre egy SAS-kapcsolati karakterláncot a megosztani kívánt fiókhoz

1. A Storage Explorerben kattintson a jobb gombbal szeretné megosztani, és válassza ki a tárfiók **közös hozzáférésű Jogosultságkód beolvasása...** .

    ![SAS beszerzése menüpont][14]

2. Az a **közös hozzáférésű Jogosultságkód létrehozása** párbeszédpanelen adja meg az időtartamot és engedélyeket a fiókhoz, és kattintson a **létrehozás** gombra.

    ![SAS beszerzése párbeszédpanel][15]

3. Mellett a **kapcsolati karakterlánc** szövegbeviteli mezőben válasszon ki **másolási** másolja a vágólapra, majd **Bezárás**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Tárfiók csatolása SAS kapcsolati karakterlánc használatával

1. A Storage Explorerben nyissa meg a **csatlakoztatása párbeszédpanel**.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Az a **csatlakoztatása párbeszédpanel** párbeszédpanelen válassza a **kapcsolati karakterlánc vagy közös hozzáférésű jogosultságkód URI Azonosítójának használata** majd **tovább**.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][16]

3. Válasszon **kapcsolati karakterlánc használata** illessze be a kapcsolati karakterláncot, és a **kapcsolati karakterlánc:** mező. Kattintson a **tovább** gombra.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][17]

4. A **Kapcsolatok összegzése** párbeszédpanelen ellenőrizze az adatokat. A változtatáshoz válassza ki a **Vissza** elemet, majd adja meg a kívánt beállításokat.

5. Kattintson a **Csatlakozás** gombra.

6. Miután a tárfiók sikeresen csatlakoztatva van, a tárfiók megjelenik, **(SAS)** a névhez.

    ![SAS használatával végzett fiókhoz csatolás eredménye][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Szolgáltatás csatolása egy közös hozzáférésű Jogosultságkód (SAS) használatával

A "Tárfiók csatolása SAS használatával" szakasz azt ismerteti, hogyan az Azure-előfizetés rendszergazdája adhat ideiglenes hozzáférést engedélyezzen a tárfiókhoz kódjának létrehozásával és a tárfiók SAS megosztásával. Hasonlóképpen SAS létrehozható adott szolgáltatásokhoz (blob tároló, üzenetsor, tábla vagy fájlmegosztás) a tárfiókon belül.

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>SAS-kód létrehozása a megosztani kívánt fiókhoz

Ebben a környezetben egy szolgáltatás is a blob-tároló, üzenetsor, tábla, vagy fájlmegosztás. SAS létrehozása listázott szolgáltatáshoz az alábbiak szerint:

* [SAS lekérése blob tárolóhoz](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Közös fiókszolgáltatás csatolása SAS URI használatával

1. A Storage Explorerben nyissa meg a **csatlakoztatása párbeszédpanel**.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Az a **csatlakoztatása párbeszédpanel** párbeszédpanelen válassza ki **kapcsolati karakterlánc vagy közös hozzáférésű jogosultságkód URI Azonosítójának használata** majd **tovább**.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][16]

3. Válasszon **használni egy SAS URI-t** , és illessze be az URI a **URI:** mező. Kattintson a **tovább** gombra.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][19]

4. A **Kapcsolatok összegzése** párbeszédpanelen ellenőrizze az adatokat. A változtatáshoz válassza ki a **Vissza** elemet, majd adja meg a kívánt beállításokat.

5. Kattintson a **Csatlakozás** gombra.

6. Miután a szolgáltatás sikeresen csatlakozott, a szolgáltatás alatt jelenik meg a **(SAS-Attached szolgáltatások)** csomópont.

    ![SAS használatával megosztott szolgáltatáshoz végzett csatolás eredménye][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Csatlakozás az Azure Cosmos DB-fiók egy kapcsolati karakterlánc használatával

Mellett kezelése az Azure Cosmos DB-fiókok az Azure-előfizetéssel, egy Azure Cosmos DB-hez történő csatlakozás másik módja az, hogy a kapcsolati karakterlánc használata. Az alábbi lépéseket követve csatlakozhat kapcsolati sztringgel.

1. Keresse meg a **Helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal az **Azure Cosmos DB-fiókok** elemre, majd válassza a **Csatlakozás az Azure Cosmos DB-hez…** lehetőséget.

    ![Csatlakozás az Azure Cosmos DB kapcsolati karakterlánc][21]

2. Azure Cosmos DB API, és illessze be a **kapcsolati karakterlánc**, és kattintson a **OK** Azure Cosmos DB-fiók csatlakoztatásához. A kapcsolati sztring lekérésével kapcsolatos információk: [A kapcsolati sztring lekérése](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>Az Azure Data Lake Store által URI-t kapcsolódás

Előfordulhat, hogy szeretne hozzáférni olyan erőforrásokhoz, amelyek nem szerepelnek az előfizetésében, de mások biztosítják az erőforrások URI-jének lekéréséhez szükséges engedélyt. Ilyen esetben a bejelentkezés után csatlakozhat a Data Lake Store-hoz az URI segítségével. Tekintse át a következő lépéseket.

1. Nyissa meg a Storage Explorert.
2. A bal oldali panelen bontsa ki a **Helyi és csatolt** elemet.
3. Kattintson a jobb gombbal a **Data Lake Store** elemre, és a helyi menüből válassza a **Csatlakozás a Data Lake Store-hoz…** lehetőséget.

    ![csatlakozás a Data Lake Store-hoz a helyi menüben](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Adja meg az URI-t. Ezután az eszköz a megadott URL helyére lép.

    ![csatlakozás a Data Lake Store-hoz – kontextus párbeszédpanel](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![csatlakozás a Data Lake Store-hoz – eredmény](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>Tárfiókok keresése

Ha meg kell keresnie a tároló egyik erőforrásához, és nem tudja, hol van, használhatja a keresési mezőbe, amely a bal oldali ablaktábla tetején, keresse meg az erőforrás.

Írja be a keresőmezőbe, a bal oldali panelen az addig beírt szövegre adott találatokat keresési érték megfelelő összes erőforrást megjeleníti. Ha például a keresést **végpontok** az alábbi képernyőfelvételen látható:

![Tárfiók keresése][23]

> [!NOTE]
> Használja a **fiók felügyeleti Panel** foglalt olyan előfizetéseket, amelyek nem tartalmaznak a végrehajtás ideje, a keresés javítása érdekében a keresett elem kijelölésének megszüntetéséhez. Is egy csomóponton kattintson a jobb gombbal, és válassza a **keresési az itt** egy adott csomópont a keresés indításához.
>
>

## <a name="next-steps"></a>További lépések

* [Azure Blob Storage-erőforrások kezelése a Storage Explorerben](vs-azure-tools-storage-explorer-blobs.md)
* [Az Azure Cosmos DB kezelése az Azure Storage Explorer (előzetes verzió)](./cosmos-db/storage-explorer.md)
* [Az Azure Data Lake Store-erőforrások kezelése a Storage Explorerben](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
