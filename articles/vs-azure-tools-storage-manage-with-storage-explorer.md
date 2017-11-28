---
title: "Ismerkedés a Tártallózó alkalmazással (előzetes verzió) | Microsoft Docs"
description: "Azure tárerőforrások kezelése a Tártallózó alkalmazással (előzetes verzió)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: b263c69554c22978c854d2678ad7eeca91bc71f7
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="get-started-with-storage-explorer-preview"></a>Ismerkedés a Tártallózó alkalmazással (előzetes verzió)
## <a name="overview"></a>Áttekintés
Az Azure Tártallózó (előzetes verzió) egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken. Ebben a cikkben megismerheti az Azure Storage-fiókok csatlakoztatásának és kezelésének különféle módjait.

![Microsoft Azure Tártallózó (előzetes verzió)][15]

## <a name="prerequisites"></a>Előfeltételek
* [A Tártallózó (előzetes verzió) letöltése és telepítése](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Csatlakozás egy tárfiókhoz vagy -szolgáltatáshoz
A Tártallózó (előzetes verzió) számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz. Megteheti például a következőt:
* Csatlakozhat az Azure-előfizetéséhez kapcsolt tárfiókokhoz.
* Csatlakozhat olyan tárfiókokhoz és szolgáltatásokhoz, amelyek más Azure-előfizetésekből vannak megosztva.
* Csatlakozhat egy helyi tárolóhoz az Azure Storage Emulator használatával, és kezelheti azt. 

Emellett használhatja a tárfiókokat a globális és az országos Azure-ban:

* [Csatlakozás Azure-előfizetéshez](#connect-to-an-azure-subscription): Az Azure-előfizetéséhez tartozó tárolási erőforrások kezelése.
* [Munkavégzés helyi fejlesztési tárterülettel](#work-with-local-development-storage): Helyi tárterület kezelése az Azure Storage Emulator használatával.
* [Külső tárterület csatolása](#attach-or-detach-an-external-storage-account): Más Azure-előfizetések vagy az országos Azure-felhők alá tartozó tárolási erőforrások kezelése a tárfiók fióknevének, kulcsának és végpontjainak használatával.
* [Tárfiók csatolása SAS használatával](#attach-storage-account-using-sas): Más Azure-előfizetések alá tartozó tárolási erőforrások kezelése közös hozzáférésű jogosultságkód (SAS) használatával.
* [Szolgáltatás csatolása SAS használatával](#attach-service-using-sas): Más Azure-előfizetések alá tartozó adott tárolási szolgáltatás (blob tároló, üzenetsor vagy tábla) kezelése SAS használatával.
* [Csatlakozás Azure Cosmos DB fiók egy kapcsolati karakterlánc használatával](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): kezelése Cosmos-adatbázis fiók egy kapcsolati karakterlánc használatával.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez
> [!NOTE]
> Ha nincs Azure-fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja Visual Studio-előfizetése kiemelt előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. A Tártallózóban (előzetes verzió) válassza ki az **Azure-fiók beállításai** lehetőséget.

    ![Azure-fiók beállításai][0]

2. A bal oldali ablaktábla megjeleníti az összes Microsoft-fiókot, amelybe bejelentkezett. Ha másik fiókhoz szeretne csatlakozni, válassza a **Fiók hozzáadása** lehetőséget, és az útmutatásokat követve jelentkezzen be egy olyan Microsoft-fiókkal, amely legalább egy aktív Azure-előfizetéssel társítva van.

    >[!NOTE]
    >Jelenleg nem támogatott a csatlakozás az országos Azure felhőkhöz (mint az Azure Germany, az Azure Government vagy az Azure China bejelentkezéssel). Az országos Azure Storage-fiókokhoz való csatlakozással kapcsolatban lásd a „Külső tárfiók csatolása vagy leválasztása” szakaszt.

3. Amint sikeresen bejelentkezett egy Microsoft-fiókkal, a bal oldali ablaktáblán megjelenik a fiókhoz társított összes Azure-előfizetés. Válassza ki azt az Azure-előfizetést, amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Az **Összes előfizetés** kiválasztásával kijelölheti az összes felsorolt Azure-előfizetést, vagy törölheti mindegyik jelölését.)

    ![Azure-előfizetések kiválasztása][3]  
    A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.

    ![Kiválasztott Azure-előfizetések][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Csatlakozás Azure Stack-előfizetéshez

Az Azure Stack-előfizetéshez való csatlakozásról további információért lásd: [A Storage Explorer csatlakoztatása Azure Stack-előfizetéshez](azure-stack/user/azure-stack-storage-connect-se.md).

## <a name="work-with-local-development-storage"></a>Munkavégzés helyi fejlesztési tárterülettel
A Tártallózó (előzetes verzió) segítségével a helyi tárterületen is dolgozhat az Azure Storage Emulator használatával. Így anélkül is írhat kódot a tárterületre és tesztelheti azt, hogy szüksége lenne egy üzembe helyezett tárfiókra az Azure szolgáltatásban (mivel a tárfiókot az Azure Storage Emulator emulálja).

> [!NOTE]
> Az Azure Storage Emulator jelenleg kizárólag Windows rendszeren támogatott.
>
>

1. A Tártallózó (előzetes verzió) bal oldali ablaktábláján, bontsa ki a **(Helyi és csatolt)** > **Tárfiókok** > **(Fejlesztés)** csomópontját.

    ![Helyi fejlesztési csomópont][21]

2. Ha az Azure Storage Emulator még nincs telepítve, a rendszer az információs sávon kéri erre. Ha az információs sáv megjelenik, válassza a **Legújabb verzió letöltése** lehetőséget, és telepítse az emulátort.

    ![Azure Storage Emulator letöltése prompt][22]

3. Miután telepítette az emulátort, létrehozhat helyi blobokat, üzenetsorokat és táblákat, és kezelheti őket. Az egyes tárfióktípusok kezelésével kapcsolatos információkért kattintson az alábbi hivatkozások egyikére:

    * [Azure Blob Storage-erőforrások kezelése](vs-azure-tools-storage-explorer-blobs.md)
    * Azure File Share Storage-erőforrások kezelése: *Hamarosan elérhető*
    * Azure Queue Storage-erőforrások kezelése: *Hamarosan elérhető*
    * Azure Table Storage-erőforrások kezelése: *Hamarosan elérhető*

## <a name="attach-or-detach-an-external-storage-account"></a>Külső tárfiók csatolása vagy leválasztása
A Tártallózó (előzetes verzió) segítségével külső tárfiókokat csatolhat, így azok könnyen megoszthatók. Ez a szakasz külső tárfiókok csatolását (és leválasztását) írja le.

### <a name="get-the-storage-account-credentials"></a>Tárfiók hitelesítő adatainak lekérése
A külső tárfiókok megosztásához először az adott fiók tulajdonosának le kell kérnie a fiók hitelesítő adatait (a fióknevet és a kulcsot), majd meg kell osztania azokat a (külső) fiókot csatlakoztatni kívánó személlyel. A tárfiók hitelesítő adatainak lekérése az Azure Portalon keresztül lehetséges az alábbi lépések végrehajtásával:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Tallózás** lehetőséget.

3. Válassza a **Tárfiókok** lehetőséget.

4. A **Tárfiókok** panelen válassza ki a kívánt tárfiókot.

5. A kiválasztott tárfiók **Beállítások** panelén válassza a **Hozzáférési kulcs** lehetőséget.

    ![Hozzáférési kulcs lehetőség][5]

6. A **Hozzáférési kulcs** panelen másolja ki a **Tárfiók neve** és a **kulcs1** értékeket a tárfiók csatolásához.

    ![Elérési kulcs][6]

### <a name="attach-to-an-external-storage-account"></a>Külső tárfiók csatolása
Külső tárfiók csatolásához szükség van a fiók nevére és kulcsára. A „Tárfiók hitelesítő adatainak lekérése” szakasz ismerteti ezen értékek lekérését az Azure Portalról. A portálon azonban a fiókkulcs neve: **kulcs1**. Tehát ahol a Tártallózó (előzetes verzió) fiókkulcsot kér, a **kulcs1** értéket kell megadni.

1. A Tártallózóban (előzetes verzió) válassza ki a **Csatlakozás Azure Storage-hoz** lehetőséget.

    ![Csatlakozás Azure Storage-hoz lehetőség][23]

2. A **Csatlakozás Azure Storage-hoz** párbeszédpanelen adja meg a fiókkulcsot (a **kulcs1** értéket az Azure Portalról), majd válassza a **Tovább** lehetőséget.

    > [!NOTE]
    > Megadhatja az országos Azure-on található tárfiók tárkapcsolati karakterláncát. Például a következőhöz hasonló kapcsolati karakterláncokat megadva csatlakozhat az Azure Germany-tárfiókokhoz: 
    >
    >* DefaultEndpointsProtocol=https
    >* AccountName=cawatest03
    >* AccountKey=<tárfiók kulcsa>
    >* EndpointSuffix=core.cloudapi.de
    
    >A „Tárfiók hitelesítő adatainak lekérése” szakaszban leírtak szerint kérheti le a kapcsolati karakterláncot az Azure Portalról.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][24]

3. A **Külső tárterület csatolása** párbeszédpanelen adja meg a tárfiók nevét a **Fióknév** mezőben, adja meg a további kívánt beállításokat, majd ha elkészült, válassza a **Tovább** lehetőséget.

    ![Külső tárterület csatolása párbeszédpanel][8]

4. A **Kapcsolatok összegzése** párbeszédpanelen ellenőrizze az adatokat. Ha bármin változtatni szeretne, válassza a **Vissza** lehetőséget, és írja be újra a kívánt beállításokat. 

5. Kattintson a **Csatlakozás** gombra.

6. A sikeres csatlakozást követően a külső tárfiók a nevét követő **(Külső)** jelöléssel jelenik meg a tárfiókok között.

    ![Külső tárfiók csatolásának eredménye][9]

### <a name="detach-from-an-external-storage-account"></a>Külső tárfiók leválasztása
1. Kattintson a jobb gombbal a leválasztani kívánt külső tárfiókra, és válassza a **Leválasztás** lehetőséget.

    ![Tár leválasztása lehetőség][10]

2. A megerősítő üzenetben kattintson az **Igen** gombra a külső tárfiók leválasztásának jóváhagyásához.

## <a name="attach-a-storage-account-by-using-an-sas"></a>Tárfiók csatolása SAS használatával
Az [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) lehetővé teszi, hogy az Azure-előfizetés rendszergazdája ideiglenes hozzáférést engedélyezzen a tárfiókhoz anélkül, hogy kiadná az Azure-előfizetés hitelesítő adatait.

E forgatókönyv szemléltetésére tegyük fel, hogy az „A” felhasználó valamely Azure-előfizetés rendszergazdája, és hozzáférést szeretne engedélyezni „B” felhasználó számára a tárfiókhoz adott időtartamra és meghatározott engedélyekkel:

1. Az „A” felhasználó létrehoz egy SAS-kódot (amely a tárfiók kapcsolati karakterlánca) egy adott időtartamra és a kívánt engedélyekkel.

2. Az „A” felhasználó megosztja a SAS-kódot a tárfiókhoz hozzáférést igénylő személlyel (esetünkben a „B” felhasználóval).  

3. A „B” felhasználó a Tártallózó (előzetes verzió) segítségével csatolja az „A” felhasználóhoz tartozó fiókot a megadott SAS-kód használatával.

### <a name="get-an-sas-for-the-account-you-want-to-share"></a>SAS-kód lekérése a megosztani kívánt fiókhoz
1. A Tártallózóban (előzetes verzió) kattintson a jobb gombbal a megosztani kívánt tárfiókra, és válassza a **Közös hozzáférésű jogosultságkód igénylése** lehetőséget.

    ![SAS beszerzése menüpont][13]

2. A **Közös hozzáférésű jogosultságkód** párbeszédpanelen adja meg a kívánt időtartamot és engedélyeket a fiókhoz, és kattintson a **Létrehozás** gombra.

    ![SAS beszerzése párbeszédpanel][14]  
    A **Közös hozzáférésű jogosultságkód** párbeszédpanel megjeleníti a SAS kódot.

3. Kattintson a **Kapcsolati karakterlánc** mellett a **Másolás** parancsra annak a vágólapra másolásához, majd válassza a **Bezárás** elemet.

### <a name="attach-to-the-shared-account-by-using-the-sas"></a>Közös fiók csatolása a SAS használatával
1. A Tártallózóban (előzetes verzió) válassza ki a **Csatlakozás Azure Storage-hoz** lehetőséget.

    ![Csatlakozás Azure Storage-hoz lehetőség][23]

2. A **Csatlakozás az Azure Storage-hoz** párbeszédpanelen adja meg a kapcsolati karakterláncot, majd válassza a **Tovább** lehetőséget.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][24]

3. A **Kapcsolatok összegzése** párbeszédpanelen ellenőrizze az adatokat. A változtatáshoz válassza ki a **Vissza** elemet, majd adja meg a kívánt beállításokat. 

4. Kattintson a **Csatlakozás** gombra.

5. A csatolást követően a tárfiók a megadott fióknevet követő **(SAS)** megjelöléssel jelenik meg a tárfiókok közt.

    ![SAS használatával végzett fiókhoz csatolás eredménye][17]

## <a name="attach-a-service-by-using-an-sas"></a>Szolgáltatás csatolása SAS használatával
A „Tárfiók csatolása SAS használatával” szakasz mutatja be, hogyan adhat az Azure-előfizetés rendszergazdája ideiglenes hozzáférést a tárfiókhoz a tárfiók SAS-kódjának létrehozásával és megosztásával. Hasonlóképpen SAS-kód létrehozható adott szolgáltatásokhoz (blob tárolókhoz, üzenetsorokhoz és táblákhoz) is a tárfiókon belül.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>SAS-kód létrehozása a megosztani kívánt fiókhoz
Ebben a kontextusban a szolgáltatások blob tárolók, üzenetsorok vagy táblák lehetnek. SAS létrehozása listázott szolgáltatáshoz az alábbiak szerint:

* [SAS lekérése blob tárolóhoz](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* SAS lekérése fájlmegosztáshoz: *Hamarosan elérhető*
* SAS lekérése üzenetsorhoz: *Hamarosan elérhető*
* SAS lekérése táblához: *Hamarosan elérhető*

### <a name="attach-to-the-shared-account-service-by-using-the-sas"></a>Közös fiókszolgáltatás csatolása SAS használatával
1. A Tártallózóban (előzetes verzió) válassza ki a **Csatlakozás Azure Storage-hoz** lehetőséget.

    ![Csatlakozás Azure Storage-hoz lehetőség][23]

2. A **Csatlakozás az Azure Storage-hoz** párbeszédpanelen adja meg a SAS URI-t, majd válassza a **Tovább** lehetőséget.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][24]

3. A **Kapcsolatok összegzése** párbeszédpanelen ellenőrizze az adatokat. A változtatáshoz válassza ki a **Vissza** elemet, majd adja meg a kívánt beállításokat. 

4. Kattintson a **Csatlakozás** gombra.

5. A csatolást követően az újonnan csatolt szolgáltatás a **(Szolgáltatás SAS)** csomópont alatt jelenik meg.

    ![SAS használatával megosztott szolgáltatáshoz végzett csatolás eredménye][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Csatlakozás Azure Cosmos DB fiók egy kapcsolati karakterlánc használatával
Emellett a Azure előfizetéssel Azure Cosmos DB fiókok kezelése, kapcsolódni egy Azure Cosmos DB megadásának alternatív módja annak használjon kapcsolati karakterláncot. A következő lépésekkel csatlakozzon a kapcsolati karakterlánc használatával.

1. Található **helyi és a kapcsolódó** a bal oldali fában, kattintson a jobb gombbal **Azure Cosmos DB fiókok**, válassza a **Azure Cosmos DB kapcsolódás...**

    ![Csatlakozás Azure Cosmos DB kapcsolati karakterlánc][33]

2. Válassza ki az Azure Cosmos DB API, és beillesztheti a **kapcsolati karakterlánc**, és kattintson a **OK** Azure Cosmos DB fiók kapcsolódni. A kapcsolati karakterlánc beolvasása információkért lásd: [a kapcsolati karakterlánc beolvasása](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][32]

## <a name="search-for-storage-accounts"></a>Tárfiókok keresése
Amennyiben tárfiókjai listája túl hosszú, az adott tárfiókok megtalálásának egyszerű módja lehet a keresőmező használata a bal oldali ablaktábla tetején.

Ahogy elkezdi beírni a szöveget a keresőmezőbe, a bal oldali ablaktábla csak azokat a tárfiókokat jeleníti meg, amelyek tartalmazzák az addig beírt szövegre adott találatokat. Például az alábbi képernyőfotón látható egy olyan keresés, amely a **tarcher** karakterláncot keresi az összes tárfiók nevében:

![Tárfiók keresése][11]

## <a name="next-steps"></a>Következő lépések
* [Azure Blob Storage-erőforrások kezelése a Tártallózó (előzetes verzió) használatával](vs-azure-tools-storage-explorer-blobs.md)
* [Az Azure Tártallózó (előzetes verzió) Azure Cosmos DB kezelése](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png
[32]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.PNG
[33]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.PNG
