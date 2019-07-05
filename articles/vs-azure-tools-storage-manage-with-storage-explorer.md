---
title: Storage Explorer használatának első lépései |} A Microsoft Docs
description: Az Azure storage-erőforrások kezelése a Storage Explorerben
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508742"
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

[A Storage Explorer letöltése és telepítése](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Az Azure Storage Explorer macOS következő verzióiban támogatott:

* macOS 10.12 "Sierra" és az újabb verziók

[A Storage Explorer letöltése és telepítése](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Az Azure Storage Explorert a következő Linux-disztribúciókon támogatják:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Az Azure Storage Explorer a többi disztribúciókon is működhet, azonban csak azokat a fent felsorolt hivatalosan támogatott.

Storage Explorer telepítése Linux rendszeren további segítségért olvassa el a [hibaelhárítási útmutatója](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

Az Azure Storage Explorer [kibocsátási megjegyzések](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) néhány disztribúciók adott lépést tartalmaznak.

[A Storage Explorer letöltése és telepítése](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Csatlakozás egy tárfiókhoz vagy -szolgáltatáshoz

A Storage Explorer számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz. Az általános közül választhat:

* [Jelentkezzen be Azure előfizetés és az erőforrások eléréséhez](#sign-in-to-azure)
* [Egy adott tároló vagy a cosmos DB erőforrás csatolása](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

> [!NOTE]
> Teljes mértékben a bejelentkezést követően erőforrások eléréséhez, a Storage Explorer (ARM) felügyeleti és adatok réteg engedélyeket is szükség van. Ez azt jelenti, hogy kell-e a hozzáférést a tárfiókhoz, az a fiók tárolóit, és az adatokat a tárolókban lévő Azure AD-engedélyekről. Ha csak rendelkezik engedélyekkel az adatok rétege, érdemes [csatolása az Azure ad-vel](#add-a-resource-via-azure-ad). A Storage Explorer igényel pontos engedélyekről további információkért lásd: a [hibaelhárítási útmutató](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).
>
>

1. A Storage Explorerben válassza ki a **fiókok kezelése** , nyissa meg a **fiók felügyeleti Panel**.

    ![Fiókok kezelése][1]

2. A bal oldali panelen most már megjeleníti az összes Azure-fiókot, amelybe bejelentkezett. Egy másik fiókhoz csatlakozáshoz válassza **-fiók hozzáadása**

3. Ha szeretne bejelentkezni egy országos felhőben vagy az Azure Stackkel, kattintson a a **Azure-környezet** legördülő listájában válassza ki a használni kívánt melyik Azure-felhőben. Miután kiválasztotta a környezetben, kattintson a **bejelentkezés...**  gombra. Bejelentkezés az Azure Stackhez, ha [Storage Explorer csatlakoztatása Azure Stack-előfizetéshez](/azure-stack/user/azure-stack-storage-connect-se) további információt.

    ![Jelentkezzen be a beállítást][2]

4. Miután sikeresen bejelentkezett egy Azure-fiókkal, a fiók és a fiókhoz társított Azure-előfizetések kerülnek a bal oldali panelen. Válassza ki az Azure-előfizetést, amelyet szeretne dolgozni, és válassza ki **alkalmaz** (kijelölése **minden előfizetés:** állapotsorának összes vagy a listában szereplő Azure-előfizetések kiválasztásával).

    ![Azure-előfizetések kiválasztása][3]

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.

    ![Kiválasztott Azure-előfizetések][4]

### <a name="attach-a-specific-resource"></a>Egy adott erőforrás csatolása
    
Nincsenek különböző lehetőségeket a Storage Explorer egy erőforrás csatolása. A következőket teheti:

* [Adjon hozzá egy erőforrást az Azure AD-n keresztül](#add-a-resource-via-azure-ad): Ha csak rendelkezik engedélyekkel az adatok rétege, majd használhatja ezt a lehetőséget egy Blob-tárolóba vagy egy ADLS Gen2 Blob-tároló hozzáadása.
* [Kapcsolati karakterlánc használata](#use-a-connection-string): Ha egy Tárfiók kapcsolati karakterláncát. Storage Explorer támogatja mindkét kulcsot, és [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) kapcsolati karakterláncokat.
* [A SAS URI-t használja](#use-a-sas-uri): Ha rendelkezik egy [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI-t egy Blob-tárolóba, fájlmegosztást, üzenetsor vagy tábla. SAS URI lekéréséhez használhatja [Tártallózó](#generate-a-sas-in-storage-explorer) vagy a [az Azure portal](https://portal.azure.com).
* [Nevének és kulcsának](#use-a-name-and-key): Ha tudja, hogy a fiók kulcsainak egyikét a tárfiókhoz, ez a beállítás segítségével gyorsan kapcsolatba léphet. A kulcsokat a tárfiók a Storage-fiókban lévő **hozzáférési kulcsok** panelen, a [az Azure portal](https://portal.azure.com).
* [Helyi emulátort csatolása](#attach-to-a-local-emulator): Az elérhető Azure Storage-emulátorok egyikét használja, ha ez a beállítás használatával könnyedén csatlakozhat az emulátorban.
* [Csatlakozás az Azure Cosmos DB-fiók egy kapcsolati karakterlánc használatával](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Ha egy kapcsolati karakterláncot egy cosmos DB-példányra.
* [Az Azure Data Lake Store által URI-t kapcsolódás](#connect-to-azure-data-lake-store-by-uri): Ha egy Azure Data Lake Store egy URI-t kell.

#### <a name="add-a-resource-via-azure-ad"></a>Adjon hozzá egy erőforrást az Azure AD-n keresztül

1. Nyissa meg a **csatlakoztatása párbeszédpanel** kattintva a **csatlakozás gomb** a bal oldali, a függőleges eszköztáron.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Ha még nem tette meg, használja a **Azure-fiók hozzáadása** arra, hogy jelentkezzen be az Azure-fiók, amely hozzáfér az erőforráshoz. Cserébe való bejelentkezés után a **csatlakoztatása párbeszédpanel**.

3. Válassza ki a **adjon hozzá egy erőforrást az Azure Active Directory (Azure AD) segítségével** lehetőséget, majd kattintson a **tovább**.

4. Válassza ki az Azure-fiók, amely hozzáféréssel rendelkezik a csatolni kívánt tárolási erőforrások és az előfizetést, amelyhez az erőforrás szerepel, és kattintson a **tovább**.

5. Válassza ki a csatolni kívánt erőforrás típusát, és adja meg a szükséges adatokat. A bemeneti adatok ezen az oldalon a hozzáadni kívánt erőforrás típusától függően változik. Ellenőrizze, hogy válassza ki a megfelelő erőforrás típusát. Miután megadta a szükséges információkat kattintson **tovább**.

6. Tekintse át a kapcsolat – összefoglaló, és ellenőrizze, hogy az adatok helyességéről. Ha jónak tűnik mindent megtalál, majd kattintson **Connect**, ellenkező esetben lépjen vissza az előző lapokra, az a **vissza** gombra az összes nem megfelelő adat.

Miután a kapcsolat sikeresen hozzáadta, az erőforrás-fa automatikusan a csomópontra, a kapcsolat jelenik meg. Ha valamilyen okból, alatt **helyi és csatolt** → **Tárfiókok** → **(csatlakoztatott tárolókkal)** → **Blobtárolók** . Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, vagy ha nem a kapcsolatot a sikeres felvétel után hozzáférhet az adataihoz, majd tekintse meg a [hibaelhárítási útmutató](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) segítséget.

#### <a name="use-a-connection-string"></a>Kapcsolati karakterlánc használata

1. Nyissa meg a **csatlakoztatása párbeszédpanel** kattintva a **csatlakozás gomb** a bal oldali, a függőleges eszköztáron.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Válassza ki a **kapcsolati karakterlánc használata** lehetőséget, majd kattintson a **tovább**.

3. Válassza ki a kapcsolat megjelenített neve, és adja meg a kapcsolati karakterláncban. Ezután kattintson a **Next** (Tovább) gombra.

4. Tekintse át a kapcsolat – összefoglaló, és ellenőrizze, hogy az adatok helyességéről. Ha jónak tűnik mindent megtalál, majd kattintson **Connect**, ellenkező esetben lépjen vissza az előző lapokra, az a **vissza** gombra az összes nem megfelelő adat.

Miután a kapcsolat sikeresen hozzáadta, az erőforrás-fa automatikusan a csomópontra, a kapcsolat jelenik meg. Ha valamilyen okból, alatt **helyi és csatolt** → **Tárfiókok**. Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, vagy ha nem a kapcsolatot a sikeres felvétel után hozzáférhet az adataihoz, majd tekintse meg a [hibaelhárítási útmutató](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) segítséget.

#### <a name="use-a-sas-uri"></a>A SAS URI-t használja.

1. Nyissa meg a **csatlakoztatása párbeszédpanel** kattintva a **csatlakozás gomb** a bal oldali, a függőleges eszköztáron.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Válassza ki a **közös hozzáférésű jogosultságkód (SAS) URI-t használja** lehetőséget, majd kattintson a **tovább**.

3. Válassza ki a kapcsolat megjelenített neve, és adja meg a SAS URI-t. A szolgáltatásvégpont a csatolni kívánt erőforrás típusát az automatikus kitöltés kell. Ha egy egyéni végpont használ, előfordulhat, hogy nem lehetséges. Kattintson a **tovább**.

4. Tekintse át a kapcsolat – összefoglaló, és ellenőrizze, hogy az adatok helyességéről. Ha jónak tűnik mindent megtalál, majd kattintson **Connect**, ellenkező esetben lépjen vissza az előző lapokra, az a **vissza** gombra az összes nem megfelelő adat.

Miután a kapcsolat sikeresen hozzáadta, az erőforrás-fa automatikusan a csomópontra, a kapcsolat jelenik meg. Ha valamilyen okból, alatt **helyi és csatolt** → **Tárfiókok** → **(csatlakoztatott tárolókkal)** → **a szolgáltatás csomópont típusa csatolt tároló**. Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, vagy ha nem a kapcsolatot a sikeres felvétel után hozzáférhet az adataihoz, majd tekintse meg a [hibaelhárítási útmutató](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) segítséget.

#### <a name="use-a-name-and-key"></a>A nevével és kulcsával

1. Nyissa meg a **csatlakoztatása párbeszédpanel** kattintva a **csatlakozás gomb** a bal oldali, a függőleges eszköztáron.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Válassza ki a **tárfiók nevének és kulcsának** lehetőséget, majd kattintson a **tovább**.

3. Válassza ki a kapcsolat megjelenített neve.

4. Adja meg a Tárfiók nevét és a hozzáférési kulcsok egyikét.

5. Válassza ki a **tárhelytartomány** használhatja, és kattintson a **tovább**.

4. Tekintse át a kapcsolat – összefoglaló, és ellenőrizze, hogy az adatok helyességéről. Ha jónak tűnik mindent megtalál, majd kattintson **Connect**, ellenkező esetben lépjen vissza az előző lapokra, az a **vissza** gombra az összes nem megfelelő adat.

Miután a kapcsolat sikeresen hozzáadta, az erőforrás-fa automatikusan a csomópontra, a kapcsolat jelenik meg. Ha valamilyen okból, alatt **helyi és csatolt** → **Tárfiókok**. Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, vagy ha nem a kapcsolatot a sikeres felvétel után hozzáférhet az adataihoz, majd tekintse meg a [hibaelhárítási útmutató](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) segítséget.

#### <a name="attach-to-a-local-emulator"></a>Helyi emulátort csatolása

Storage Explorer az összes platformon emulátory systému támogatja. A két hivatalos elérhető emulátory systému jelenleg:
* [Az Azure storage emulator](storage/common/storage-use-emulator.md) (csak Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS vagy Linux)

Ha az emulátorban fut. az alapértelmezett porton is használhatja a **emulátor – alapértelmezett portok** csomópont, amely mindig alatt található **helyi és csatolt** → **Tárfiókok** , gyorsan hozzáférhet az emulátort. Ha szeretné-e a kapcsolat másik nevet, vagy ha az emulátor nem fut az alapértelmezett porton, majd kövesse az alábbi lépéseket.

1. Indítsa el az emulátort. Ha Ön, jegyezze fel, milyen portokat az emulátorban figyel a következőn: az egyes service. Ezt az információt későbbi ismernie kell.

   > [!IMPORTANT]
   > Storage Explorer nem indul el automatikusan az emulátort. El kell indítania azt saját magának.

2. Nyissa meg a **csatlakoztatása párbeszédpanel** kattintva a **csatlakozás gomb** a bal oldali, a függőleges eszköztáron.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

3. Válassza ki a **helyi emulátort csatolása** lehetőséget, majd kattintson a **tovább**.

4. Válassza ki a kapcsolat megjelenített neve, és írja be a portokat az emulátor minden szolgáltatástípus figyel. Alapértelmezés szerint a szövegmezők a legtöbb emulátory systému fogja tartalmazni az alapértelmezett port értékeket. A **port fájlok** is üresen alapértelmezés szerint, sem a hivatalos emulátory systému a fájlok szolgáltatás jelenleg támogatja. Ha az emulatort használja, ha támogatja azt, majd megadhatja a portot, amely használatban van. Kattintson a **tovább**.

5. Tekintse át a kapcsolat – összefoglaló, és ellenőrizze, hogy az adatok helyességéről. Ha jónak tűnik mindent megtalál, majd kattintson **Connect**, ellenkező esetben lépjen vissza az előző lapokra, az a **vissza** gombra az összes nem megfelelő adat.

Miután a kapcsolat sikeresen hozzáadta, az erőforrás-fa automatikusan a csomópontra, a kapcsolat jelenik meg. Ha valamilyen okból, alatt **helyi és csatolt** → **Tárfiókok**. Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, vagy ha nem a kapcsolatot a sikeres felvétel után hozzáférhet az adataihoz, majd tekintse meg a [hibaelhárítási útmutató](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) segítséget.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Csatlakozás az Azure Cosmos DB-fiók egy kapcsolati karakterlánc használatával

Mellett kezelése az Azure Cosmos DB-fiókok az Azure-előfizetéssel, egy Azure Cosmos DB-hez történő csatlakozás másik módja az, hogy a kapcsolati karakterlánc használata. Az alábbi lépéseket követve csatlakozhat kapcsolati sztringgel.

1. Keresse meg a **Helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal az **Azure Cosmos DB-fiókok** elemre, majd válassza a **Csatlakozás az Azure Cosmos DB-hez…** lehetőséget.

    ![Csatlakozás az Azure Cosmos DB kapcsolati karakterlánc][21]

2. Azure Cosmos DB API, és illessze be a **kapcsolati karakterlánc**, és kattintson a **OK** Azure Cosmos DB-fiók csatlakoztatásához. A kapcsolati sztring lekérésével kapcsolatos információk: [A kapcsolati sztring lekérése](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Az Azure Data Lake Store által URI-t kapcsolódás

Előfordulhat, hogy szeretne hozzáférni olyan erőforrásokhoz, amelyek nem szerepelnek az előfizetésében, de mások biztosítják az erőforrások URI-jének lekéréséhez szükséges engedélyt. Ilyen esetben a bejelentkezés után csatlakozhat a Data Lake Store-hoz az URI segítségével. Tekintse át a következő lépéseket.

1. Nyissa meg a Storage Explorert.
2. A bal oldali panelen bontsa ki a **Helyi és csatolt** elemet.
3. Kattintson a jobb gombbal a **Data Lake Store** elemre, és a helyi menüből válassza a **Csatlakozás a Data Lake Store-hoz…** lehetőséget.

    ![csatlakozás a Data Lake Store-hoz a helyi menüben](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Adja meg az URI-t. Ezután az eszköz a megadott URL helyére lép.

    ![csatlakozás a Data Lake Store-hoz – kontextus párbeszédpanel](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![csatlakozás a Data Lake Store-hoz – eredmény](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>SAS létrehozása a Storage Explorerben

### <a name="account-level-sas"></a>Fiókszintű SAS

1. Kattintson a jobb gombbal a szeretne megosztani, és válassza ki a tárfiók **közös hozzáférésű Jogosultságkód beolvasása...** .

    ![SAS beszerzése menüpont][14]

2. Az a **közös hozzáférésű Jogosultságkód létrehozása** párbeszédpanelen adja meg az időtartamot és engedélyeket a fiókhoz, és kattintson a **létrehozás** gombra.

    ![SAS beszerzése párbeszédpanel][15]

3. Mostantól másolja át a **kapcsolati karakterlánc** vagy a nyers **lekérdezési karakterlánc** a vágólapra.

### <a name="service-level-sas"></a>Szolgáltatásszintű SAS

[SAS lekérése blob tárolóhoz a Storage Explorerben](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

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
