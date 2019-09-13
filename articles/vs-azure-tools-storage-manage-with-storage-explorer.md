---
title: Ismerkedés a Storage Explorerrel | Microsoft Docs
description: Azure Storage-erőforrások kezelése Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: c4aad2f2f5bca25ead03518b2de9ac9315172052
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934742"
---
# <a name="get-started-with-storage-explorer"></a>Ismerkedés a Storage Explorer

## <a name="overview"></a>Áttekintés

A Microsoft Azure Storage Explorer egy önálló alkalmazás, amely lehetővé teszi az Azure Storage-alapú adattárolást Windows, macOS és Linux rendszeren. Ebből a cikkből megismerheti az Azure Storage-fiókok csatlakoztatásának és kezelésének számos módját.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Előfeltételek

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

A Storage Explorer a Windows következő verzióiban támogatott:

* Windows 10 (ajánlott)
* Windows 8
* Windows 7

A Windows összes verziójának a .NET-keretrendszer 4.6.2 vagy újabb verziójára van szükség.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

A Storage Explorer a macOS következő verzióiban támogatott:

* macOS 10,12 "Sierra" és újabb verziók

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Az Storage Explorer a Linux leggyakoribb disztribúcióinak [beépülő moduljában](https://snapcraft.io/storage-explorer) érhető el, és az ajánlott telepítési módszer. A Storage Explorer beépülő modul automatikusan telepíti az összes függőségét és frissítését, mivel az új verziók közzé lesznek téve az illesztési tárolóban.

A támogatott disztribúciók listájáért látogasson el a [beépülő modul telepítési oldalára](https://snapcraft.io/docs/installing-snapd).

Storage Explorer a Password Manager használatát igényli, amelyet manuálisan kell csatlakoztatni, mielőtt Storage Explorer megfelelően működni fognak. A következő paranccsal csatlakozhat Storage Explorer a rendszer jelszavas kezelőjéhez:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer a. tar. gz letöltésként is elérhető, de a függőségeket manuálisan kell telepítenie. A. tar. gz telepítését a következő Linux-disztribúciók támogatják:

* Ubuntu 18,04 x64
* Ubuntu 16,04 x64
* Ubuntu 14,04 x64

A. tar. gz telepítése más disztribúciókban is működhet, de csak a fent felsoroltak közül a hivatalosan támogatottak.

A Storage Explorer Linux rendszeren való telepítésével kapcsolatos további segítségért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

---

## <a name="download-and-install"></a>Letöltés és telepítés

[A Storage Explorer letöltése és telepítése](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Csatlakozás egy tárfiókhoz vagy -szolgáltatáshoz

A Storage Explorer számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz. Általában a következők közül választhat:

* [Jelentkezzen be az Azure-ba az előfizetések és erőforrásaik eléréséhez](#sign-in-to-azure)
* [Egy adott tárolási vagy CosmosDB erőforrás csatolása](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

> [!NOTE]
> Az erőforrásoknak a bejelentkezést követő teljes hozzáféréséhez Storage Explorer a felügyeleti (ARM) és az adatrétegbeli engedélyeket is. Ez azt jelenti, hogy olyan Azure AD-engedélyekre van szüksége, amelyek hozzáférést biztosítanak a Storage-fiókjához, a fiókban található tárolóhoz és a tárolók adataihoz. Ha csak az adatréteghez rendelkezik engedélyekkel, érdemes lehet [csatolni az Azure ad-vel való csatolást](#add-a-resource-via-azure-ad). A pontos engedélyekkel Storage Explorer szükséges további tudnivalókért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).

1. A Storage Explorer területen válassza a **fiókok kezelése** lehetőséget a **Fiókkezelés panel**megnyitásához.

    ![Fiókok kezelése][1]

2. A bal oldali ablaktábla mostantól megjeleníti az összes olyan Azure-fiókot, amelybe bejelentkezett. Egy másik fiókhoz való kapcsolódáshoz válassza a **fiók hozzáadása** lehetőséget.

3. Ha egy nemzeti felhőbe vagy egy Azure Stackba szeretne bejelentkezni, kattintson az **Azure-környezet** legördülő menüjére, és válassza ki a használni kívánt Azure-felhőt. Miután kiválasztotta a környezetét, kattintson a **Bejelentkezés...** gombra. További információ: a [Storage Explorer összekötése egy Azure stack-előfizetéssel](/azure-stack/user/azure-stack-storage-connect-se).

    ![Bejelentkezés lehetőség][2]

4. Miután sikeresen bejelentkezett egy Azure-fiókkal, a fiók és az ahhoz társított Azure-előfizetések hozzáadódnak a bal oldali ablaktáblához. Válassza ki a használni kívánt Azure-előfizetéseket, majd kattintson az **alkalmaz** gombra (az **összes előfizetés kijelölése:** a felsorolt Azure-előfizetések összes vagy egyetlen kijelölésének kikapcsolása).

    ![Azure-előfizetések kiválasztása][3]

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.

    ![Kiválasztott Azure-előfizetések][4]

### <a name="attach-a-specific-resource"></a>Adott erőforrás csatolása

Az erőforrásokhoz a Storage Explorer különböző beállításokkal lehet csatolni:

* [Erőforrás hozzáadása az Azure ad-n keresztül](#add-a-resource-via-azure-ad): Ha csak az adatrétegben rendelkezik engedélyekkel, akkor ezzel a lehetőséggel hozzáadhat egy BLOB-tárolót vagy egy ADLS Gen2 BLOB-tárolót.
* [Használjon egy kapcsolatok karakterláncot](#use-a-connection-string): Ha van egy Storage-fiókhoz tartozó kapcsolódási karakterlánca. A Storage Explorer a kulcs-és [sas](storage/common/storage-dotnet-shared-access-signature-part-1.md) -kapcsolatok karakterláncait is támogatja.
* [Sas URI használata](#use-a-sas-uri): Ha [sas](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI-ja van a blob-tárolóhoz, a fájlmegosztás, a várólista vagy a tábla. SAS URI beszerzéséhez használhatja [Storage Explorer](#generate-a-sas-in-storage-explorer) vagy a [Azure Portal](https://portal.azure.com).
* [Név és kulcs használata](#use-a-name-and-key): Ha ismeri a fiók kulcsainak valamelyikét a Storage-fiókjához, akkor ezzel a lehetőséggel gyorsan csatlakozhat. A Storage-fiók kulcsa a [Azure Portal](https://portal.azure.com)a Storage-fiók **hozzáférési kulcsok** paneljén található.
* [Csatolás helyi emulátorhoz](#attach-to-a-local-emulator): Ha az elérhető Azure Storage-emulátorok egyikét használja, ezzel a lehetőséggel egyszerűen csatlakozhat az emulátorhoz.
* [Kapcsolódás Azure Cosmos db fiókhoz kapcsolati sztring használatával](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Ha van egy CosmosDB-példányhoz tartozó kapcsolódási karakterlánc.
* [Kapcsolódás a Azure Data Lake Storehoz URI használatával](#connect-to-azure-data-lake-store-by-uri): Ha van URI-ja egy Azure Data Lake Storehoz.

#### <a name="add-a-resource-via-azure-ad"></a>Erőforrás hozzáadása az Azure AD-n keresztül

1. Nyissa meg a **csatlakozási párbeszédpanelt** a bal oldali, függőleges eszköztáron a **kapcsolat gombra** kattintva.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Ha még nem tette meg, használja az **Azure-fiók hozzáadása** lehetőséget az erőforráshoz hozzáférő Azure-fiókba való bejelentkezéshez. A bejelentkezést követően térjen vissza a **kapcsolódási párbeszédpanelre**.

3. Válassza az **erőforrás hozzáadása Azure Active Directory (Azure ad)** lehetőséget, és kattintson a **tovább**gombra.

4. Válassza ki azt az Azure-fiókot és-bérlőt, amelyhez hozzáféréssel rendelkezik a csatolni kívánt tárolási erőforráshoz. Kattintson a **Tovább** gombra.

5. Válassza ki a csatolni kívánt erőforrás típusát, majd adja meg a kapcsolódáshoz szükséges adatokat. A lapon lévő bemenetek attól függően változnak, hogy milyen típusú erőforrást szeretne hozzáadni. Ügyeljen arra, hogy a megfelelő típusú erőforrást adja meg. A szükséges információk kitöltése után kattintson a **tovább**gombra.

6. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy az összes adat helyes. Ha az összes információ helyesnek tűnik, kattintson a **kapcsolat**elemre. Ellenkező esetben térjen vissza az előző lapokra a **vissza** gombbal a helytelen információk kijavítani.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan megnyitja a kapcsolódást jelképező csomópontot. Azt is megteheti, hogy a **helyi & csatolt** → **Storage-fiókok** → **(csatolt tárolók)** → **blob-tárolók** , ha valamilyen okból nem. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a Súgó [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="use-a-connection-string"></a>Kapcsolati sztring használata

1. Nyissa meg a **csatlakozási párbeszédpanelt** a bal oldali, függőleges eszköztáron a **kapcsolat gombra** kattintva.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Jelölje be a **kapcsolatok karakterláncának használata** lehetőséget, és kattintson a **tovább**gombra.

3. Válassza ki a kapcsolatok megjelenítendő nevét, és adja meg a nevet a kapcsolatok karakterláncában. Ezután kattintson a **Next** (Tovább) gombra.

4. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy az összes adat helyes. Ha az összes információ helyesnek tűnik, kattintson a **Kapcsolódás**gombra, ellenkező esetben térjen vissza az előző lapokra a **vissza** gombbal a helytelen információk kijavítani.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan megnyitja a kapcsolódást jelképező csomópontot. Ha valamilyen okból kifolyólag nem, akkor a **helyi & csatolt** → **Storage-fiókok** területen is megtekintheti. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a Súgó [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="use-a-sas-uri"></a>SAS URI használata

1. Nyissa meg a **csatlakozási párbeszédpanelt** a bal oldali, függőleges eszköztáron a **kapcsolat gombra** kattintva.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Jelölje be a **közös hozzáférésű aláírás (SAS) URI** -beállításának használata lehetőséget, és kattintson a **tovább**gombra.

3. Válassza ki a kapcsolatok megjelenítendő nevét, és adja meg a SAS URI azonosítóját. A csatolni kívánt erőforrás-típushoz tartozó szolgáltatási végpontot az automatikus kitöltés értékre kell írni. Ha egyéni végpontot használ, lehetséges, hogy nem. Kattintson a **Tovább** gombra.

4. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy az összes adat helyes. Ha az összes információ helyesnek tűnik, kattintson a **Kapcsolódás**gombra, ellenkező esetben térjen vissza az előző lapokra a **vissza** gombbal, és javítsa ki a helytelen adatokat.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan megnyitja a kapcsolódást jelképező csomópontot. Azt is megvizsgálhatja, hogy a **helyi & csatolt** → **Storage-fiókok** → **(csatolt tárolók)** → **a szolgáltatás csomópontja a csatlakoztatott tároló típusához** , ha valamilyen okból nem. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a Súgó [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="use-a-name-and-key"></a>Név és kulcs használata

1. Nyissa meg a **csatlakozási párbeszédpanelt** a bal oldali, függőleges eszköztáron a **kapcsolat gombra** kattintva.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Jelölje be a **Storage-fiók nevének és kulcsának használata** lehetőséget, és kattintson a **tovább**gombra.

3. Válassza ki a kapcsolatok megjelenítendő nevét.

4. Adja meg a Storage-fiók nevét és a hozzá tartozó hozzáférési kulcsok egyikét.

5. Válassza ki a használni kívánt **tárolási tartományt** , majd kattintson a **tovább**gombra.

4. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy az összes adat helyes. Ha az összes információ helyesnek tűnik, kattintson a **Kapcsolódás**gombra, ellenkező esetben térjen vissza az előző lapokra a **vissza** gombbal a helytelen információk kijavítani.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan megnyitja a kapcsolódást jelképező csomópontot. Ha valamilyen okból kifolyólag nem, akkor a **helyi & csatolt** → **Storage-fiókok** területen is megtekintheti. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a Súgó [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="attach-to-a-local-emulator"></a>Csatolás helyi emulátorhoz

Storage Explorer jelenleg két hivatalos tároló-emulátort támogat:
* [Azure Storage-emulátor](storage/common/storage-use-emulator.md) (Csak Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS vagy Linux)

Ha az emulátor figyeli az alapértelmezett portokat, használhatja az emulátor **– alapértelmezett portok** csomópontot (a **helyi & csatolt** → **Storage-fiókok**területen található) az emulátor gyors eléréséhez.

Ha más nevet szeretne használni a csatlakozáshoz, vagy ha az emulátor nem fut az alapértelmezett portokon:

1. Indítsa el az emulátort. Ha ezt teszi, jegyezze fel, hogy az emulátor milyen portokat figyel az egyes szolgáltatások típusainál.

   > [!IMPORTANT]
   > A Storage Explorer nem indítja el automatikusan az emulátort. El kell indítania magát.

2. Nyissa meg a **csatlakozási párbeszédpanelt** a bal oldali, függőleges eszköztáron a **kapcsolat gombra** kattintva.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

3. Válassza a **csatolás helyi emulátorhoz** lehetőséget, majd kattintson a **tovább**gombra.

4. Válassza ki a kapcsolatok megjelenítendő nevét, és adja meg azokat a portokat, amelyeken az emulátor figyeli az egyes szolgáltatások típusait. A szövegmezők a legtöbb emulátor alapértelmezett portszámait fogják kezdeni. A **fájlok portja** üresen marad, mert egyik hivatalos emulátor sem támogatja a Files szolgáltatást. Ha a használt emulátor támogatja a fájlokat, akkor megadhatja a használt portot. Kattintson a **Tovább** gombra.

5. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy az összes adat helyes. Ha az összes információ helyesnek tűnik, kattintson a **Kapcsolódás**gombra, ellenkező esetben térjen vissza az előző lapokra a **vissza** gombbal, és javítsa ki a hibás információkat.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan megnyitja a kapcsolódást jelképező csomópontot. Ha valamilyen okból kifolyólag nem, akkor a **helyi & csatolt** → **Storage-fiókok** területen is megtekintheti. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a Súgó [hibaelhárítási útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Kapcsolódás Azure Cosmos DB fiókhoz kapcsolati sztring használatával

Az Azure Cosmos DB-fiókok Azure-előfizetésen keresztüli kezelése mellett a Azure Cosmos DBhoz való kapcsolódás másik módja a kapcsolati karakterlánc használata. Az alábbi lépéseket követve csatlakozhat kapcsolati sztringgel.

1. Keresse meg a **Helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal az **Azure Cosmos DB-fiókok** elemre, majd válassza a **Csatlakozás az Azure Cosmos DB-hez…** lehetőséget.

    ![Kapcsolódás Azure Cosmos DB kapcsolati karakterlánc alapján][21]

2. Válassza ki Azure Cosmos DB API-t, illessze be a **kapcsolati karakterláncot**, majd kattintson **az OK** gombra Azure Cosmos db fiók csatlakoztatásához. A kapcsolati sztring lekérésével kapcsolatos információk: [A kapcsolati sztring lekérése](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Kapcsolódás a Azure Data Lake Storehoz URI használatával

Ha egy, az előfizetéshez nem tartozó erőforráshoz szeretne hozzáférni, az erőforrás URI azonosítójának megadásához valakinek hozzáféréssel kell rendelkeznie. A bejelentkezést követően a következő lépésekkel csatlakozhat a Data Lake Storehoz az URI használatával:

1. Nyissa meg a Storage Explorert.
2. A bal oldali panelen bontsa ki a **Helyi és csatolt** elemet.
3. Kattintson a jobb gombbal a **Data Lake Store** elemre, és a helyi menüből válassza a **Csatlakozás a Data Lake Store-hoz…** lehetőséget.

    ![csatlakozás a Data Lake Store-hoz a helyi menüben](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Adja meg az URI-t. Ezután az eszköz a megadott URL helyére lép.

    ![csatlakozás a Data Lake Store-hoz – kontextus párbeszédpanel](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![csatlakozás a Data Lake Store-hoz – eredmény](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>SAS létrehozása Storage Explorer

### <a name="account-level-sas"></a>Fiók szintű SAS

1. Kattintson a jobb gombbal a megosztani kívánt Storage-fiókra, majd válassza a **közös hozzáférési aláírás beolvasása..** . lehetőséget.

    ![SAS beszerzése menüpont][14]

2. A **megosztott hozzáférés aláírásának létrehozása** párbeszédpanelen határozza meg a fiókhoz használni kívánt időkeretet és engedélyeket. Kattintson a **Create** (Létrehozás) gombra.

    ![SAS beolvasása párbeszédpanel][15]

3. Mostantól a vágólapra másolhatja a **kapcsolódási karakterláncot** vagy a nyers **lekérdezési karakterláncot** .

### <a name="service-level-sas"></a>Szolgáltatási szint SAS

[SAS beszerzése blob-tárolóhoz Storage Explorer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Tárfiókok keresése

Ha meg kell találnia egy tárolási erőforrást, és nem tudja, hol található, a bal oldali ablaktábla tetején található keresőmező használatával megkeresheti az erőforrást.

A keresőmező beírásakor a bal oldali ablaktábla megjeleníti az összes olyan erőforrást, amely megfelel az adott ponthoz megadott keresési értéknek. A **végpontok** keresése például a következő képernyőképen látható:

![Tárfiók keresése][23]

> [!NOTE]
> A **Fiókkezelés panelen** jelölje ki azokat az előfizetéseket, amelyek nem tartalmazzák a keresett elemet, hogy javítsa a keresés végrehajtási idejét. Kattintson a jobb gombbal a csomópontra, és válassza a **Keresés itt** lehetőséget egy adott csomópont keresésének megkezdéséhez.
>
>

## <a name="next-steps"></a>További lépések

* [Azure Blob Storage-erőforrások kezelése Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Cosmos DB kezelése Storage Explorerban (előzetes verzió)](./cosmos-db/storage-explorer.md)
* [Azure Data Lake Store erőforrások kezelése a Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

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
