---
title: Ismerkedés a Storage Explorerrel | Microsoft Docs
description: Azure Storage-erőforrások kezelése Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79279792"
---
# <a name="get-started-with-storage-explorer"></a>A Storage Explorer használatának első lépései

## <a name="overview"></a>Áttekintés

A Microsoft Azure Storage Explorer egy önálló alkalmazás, amely megkönnyíti az Azure Storage-szolgáltatásokkal való munkát Windows, macOS és Linux rendszereken. Ebből a cikkből megismerheti az Azure Storage-fiókok csatlakoztatásának és kezelésének számos módját.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Előfeltételek

# <a name="windows"></a>[Windows](#tab/windows)

A Windows támogatási Storage Explorer következő verziói:

* Windows 10 (ajánlott)
* Windows 8
* Windows 7

A Windows összes verziójában Storage Explorer a .NET-keretrendszer 4.6.2 vagy újabb verziójára van szükség.

# <a name="macos"></a>[macOS](#tab/macos)

A macOS-támogatás következő verziói Storage Explorer:

* macOS 10,12 Sierra és újabb verziók

# <a name="linux"></a>[Linux](#tab/linux)

Az Storage Explorer a Linux leggyakoribb disztribúcióinak [beépülő moduljában](https://snapcraft.io/storage-explorer) érhető el. Javasoljuk, hogy ehhez a telepítéshez a Snap Store-t. A Storage Explorer beépülő modul telepíti az összes függőségét és frissítését, amikor a rendszer közzéteszi az új verziókat az illesztési tárolóban.

A támogatott disztribúciók esetében tekintse meg a [beépülő modul telepítése lapot](https://snapcraft.io/docs/installing-snapd).

Storage Explorer a Password Manager használatát igényli. Előfordulhat, hogy manuálisan kell csatlakoznia a jelszó-kezelőhöz. A következő parancs futtatásával csatlakozhat Storage Explorer a rendszer jelszavas kezelőjéhez:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

A Storage Explorer *. tar. gz* letöltésként is elérhető. A függőségeket manuálisan kell telepítenie. A Linux-támogatás következő disztribúciói *. tar. gz* telepítés:

* Ubuntu 18,04 x64
* Ubuntu 16,04 x64
* Ubuntu 14,04 x64

A *. tar. gz* telepítése más disztribúciókban is működhet, de csak ezek a felsoroltak támogatottak.

A Storage Explorer Linux rendszeren való telepítésével kapcsolatos további segítségért tekintse meg a [Linux-függőségek](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) című részt a Azure Storage Explorer hibaelhárítási útmutatójában.

---

## <a name="download-and-install"></a>Letöltés és telepítés

Storage Explorer letöltéséhez és telepítéséhez lásd: [Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Csatlakozás egy tárfiókhoz vagy -szolgáltatáshoz

A Storage Explorer számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz. Általában a következők közül választhat:

* [Jelentkezzen be az Azure-ba az előfizetések és erőforrásaik eléréséhez](#sign-in-to-azure)
* [Egy adott tárolási vagy CosmosDB erőforrás csatolása](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

> [!NOTE]
> Ahhoz, hogy a bejelentkezés után teljesen hozzáférjen az erőforrásokhoz, Storage Explorer a felügyeleti (Azure Resource Manager) és az adatrétegbeli engedélyeket is. Ez azt jelenti, hogy szüksége van Azure Active Directory (Azure AD) engedélyekre, amelyek hozzáférést biztosítanak a Storage-fiókjához, a fiókhoz tartozó tárolóhoz és a tárolókban lévő adattárakhoz. Ha csak az adatréteghez rendelkezik engedélyekkel, vegyen fel [egy erőforrást az Azure ad](#add-a-resource-via-azure-ad)-n keresztül. További információ a Storage Explorer szükséges konkrét engedélyekről: [Azure Storage Explorer hibaelhárítási útmutató](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. A Storage Explorer lapon válassza a**Fiókkezelés** **megtekintése** > lehetőséget, vagy kattintson a **fiókok kezelése** gombra.

    ![Fiókok kezelése][1]

1. A **Fiókkezelés** mostantól megjeleníti az összes olyan Azure-fiókot, amelybe bejelentkezett. Egy másik fiókhoz való kapcsolódáshoz válassza a **fiók hozzáadása**lehetőséget.

1. A **Kapcsolódás az Azure Storage-hoz**területen válasszon ki egy Azure-felhőt az **Azure-környezetből** egy nemzeti felhőbe vagy egy Azure Stackba való bejelentkezéshez. A környezet kiválasztása után válassza a **tovább**lehetőséget.

    ![Bejelentkezés lehetősége][2]

    Storage Explorer megnyit egy oldalt a bejelentkezéshez. További információ: a [Storage Explorer összekötése egy Azure stack-előfizetéssel vagy egy Storage-fiókkal](/azure-stack/user/azure-stack-storage-connect-se).

1. Miután sikeresen bejelentkezett egy Azure-fiókkal, a fiók és az ahhoz hozzárendelt Azure-előfizetések megjelennek **a fiók felügyelete alatt.** Válassza ki az **összes előfizetést** , hogy a kijelölt Azure-előfizetések közül egy vagy több se legyen bekapcsolva. Válassza ki azt az Azure-előfizetést, amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget.

    ![Azure-előfizetések kiválasztása][3]

    Az **Explorer** megjeleníti a kiválasztott Azure-előfizetésekhez társított Storage-fiókokat.

    ![Kiválasztott Azure-előfizetések][4]

### <a name="attach-a-specific-resource"></a>Adott erőforrás csatolása

A Storage Explorer több módon is csatolhat erőforrásokhoz:

* [Erőforrás hozzáadása az Azure ad-n keresztül](#add-a-resource-via-azure-ad). Ha csak az adatréteghez rendelkezik engedélyekkel, akkor ezzel a lehetőséggel hozzáadhat egy BLOB-tárolót vagy egy Azure Data Lake Storage Gen2 blob Storage-tárolót.
* [Használjon egy kapcsolatok karakterláncot](#use-a-connection-string). Akkor használja ezt a beállítást, ha egy Storage-fiókhoz tartozik egy kapcsolódási sztring. A Storage Explorer a kulcs-és [közös hozzáférésű aláírás](storage/common/storage-dotnet-shared-access-signature-part-1.md) -kapcsolati karakterláncokat is támogatja.
* [Használjon közös hozzáférésű aláírási URI](#use-a-shared-access-signature-uri)-t. Ha [közös hozzáférési aláírási URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) -t használ a blob-tárolóhoz, a fájlmegosztás, a várólista vagy a tábla számára, az erőforráshoz való csatoláshoz használja azt. A közös hozzáférésű aláírás URI-azonosítójának beszerzéséhez használhatja [Storage Explorer](#generate-a-sas-in-storage-explorer) vagy a [Azure Portal](https://portal.azure.com).
* Adjon meg [egy nevet és egy kulcsot](#use-a-name-and-key). Ha ismeri a fiók kulcsainak valamelyikét a Storage-fiókjához, akkor ezzel a lehetőséggel gyorsan csatlakozhat. Keresse meg a kulcsokat a Storage-fiók lapon a [Azure Portal](https://portal.azure.com) **Beállítások** > **hozzáférési kulcsainak** kiválasztásával.
* [Csatoljon egy helyi emulátorhoz](#attach-to-a-local-emulator). Ha az elérhető Azure Storage-emulátorok egyikét használja, ezzel a lehetőséggel egyszerűen csatlakozhat az emulátorhoz.
* [Kapcsolódás Azure Cosmos db fiókhoz kapcsolati karakterlánc használatával](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Akkor használja ezt a beállítást, ha egy CosmosDB-példányhoz tartozik egy kapcsolódási sztring.
* [Azure Data Lake Storehoz való kapcsolódás URI-n keresztül](#connect-to-azure-data-lake-store-by-uri). Akkor használja ezt a beállítást, ha a Azure Data Lake Store URI azonosítóval rendelkezik.

#### <a name="add-a-resource-via-azure-ad"></a>Erőforrás hozzáadása az Azure AD-n keresztül

1. Válassza a **Kapcsolódás** szimbólumot az **Azure Storage-hoz való kapcsolódás**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Ha még nem tette meg, használja az **Azure-fiók hozzáadása** lehetőséget az erőforráshoz hozzáférő Azure-fiókba való bejelentkezéshez. A bejelentkezést követően térjen vissza az **Azure Storage-hoz való kapcsolódáshoz**.

1. Válassza **az erőforrás hozzáadása Azure Active Directory (Azure ad)** lehetőséget, majd kattintson a **tovább**gombra.

1. Válasszon ki egy Azure-fiókot és-bérlőt. Ezeknek az értékeknek hozzáféréssel kell rendelkezniük ahhoz a tárolási erőforráshoz, amelyhez csatolni kíván. Kattintson a **Tovább** gombra.

1. Válassza ki a csatolni kívánt erőforrás típusát. Adja meg a kapcsolódáshoz szükséges adatokat. 

   Az ezen a lapon megadott információk attól függnek, hogy milyen típusú erőforrást szeretne hozzáadni. Ügyeljen arra, hogy a megfelelő típusú erőforrást adja meg. A szükséges információk megadása után válassza a **tovább**lehetőséget.

1. Tekintse át a **kapcsolatok összegzését** , és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben válassza a **vissza** lehetőséget, ha vissza szeretne térni az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa a kapcsolódást jelölő csomópontra kerül. Az erőforrás a **helyi & csatolt** > **Storage-fiókok** > **(csatolt tárolók) blob-** > **tárolók**területen jelenik meg. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [Azure Storage Explorer hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>A kapcsolatok karakterláncának használata

1. Válassza a **Kapcsolódás** szimbólumot az **Azure Storage-hoz való kapcsolódás**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Válassza **a kapcsolatok karakterlánc használata**lehetőséget, majd kattintson a **tovább**gombra.

1. Válassza ki a kapcsolatok megjelenítendő nevét, és adja meg a kapcsolatok karakterláncát. Ezután válassza a **tovább**lehetőséget.

1. Tekintse át a **kapcsolatok összegzését** , és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben válassza a **vissza** lehetőséget, ha vissza szeretne térni az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa a kapcsolódást jelölő csomópontra kerül. Az erőforrás a **helyi & csatolt** > **Storage-fiókok**területen jelenik meg. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [Azure Storage Explorer hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-shared-access-signature-uri"></a>Közös hozzáférésű aláírási URI használata

1. Válassza a **Kapcsolódás** szimbólumot az **Azure Storage-hoz való kapcsolódás**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Válassza **a közös hozzáférésű aláírás (SAS) URI használata**lehetőséget, majd kattintson a **tovább**gombra.

1. Válassza ki a kapcsolat megjelenítendő nevét, és adja meg a közös hozzáférésű aláírás URI-JÁT. A csatolni kívánt erőforrás-típushoz tartozó szolgáltatási végpontot az automatikus kitöltés értékre kell írni. Ha egyéni végpontot használ, lehetséges, hogy előfordulhat, hogy nem. Kattintson a **Tovább** gombra.

1. Tekintse át a **kapcsolatok összegzését** , és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben válassza a **vissza** lehetőséget, ha vissza szeretne térni az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa a kapcsolódást jelölő csomópontra kerül. Az erőforrás a **helyi & csatlakoztatott** > **Storage-fiókok** > **(csatolt tárolók)** > alatt jelenik meg a*szolgáltatás csomópontja számára a csatlakoztatott tároló típusaként*. Ha Storage Explorer nem tudta felvenni a kapcsolatokat, tekintse meg a [Azure Storage Explorer hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting). Ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a hibaelhárítási útmutatót.

#### <a name="use-a-name-and-key"></a>Név és kulcs használata

1. Válassza a **Kapcsolódás** szimbólumot az **Azure Storage-hoz való kapcsolódás**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Válassza **a Storage-fiók nevének és kulcsának használata**lehetőséget, majd kattintson a **tovább**gombra.

1. Válassza ki a kapcsolatok megjelenítendő nevét.

1. Adja meg a Storage-fiók nevét és a hozzá tartozó hozzáférési kulcsok egyikét.

1. Válassza ki a használni kívánt **tárolási tartományt** , majd kattintson a **tovább**gombra.

1. Tekintse át a **kapcsolatok összegzését** , és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben válassza a **vissza** lehetőséget, ha vissza szeretne térni az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa a kapcsolódást jelölő csomópontra kerül. Az erőforrás a **helyi & csatolt** > **Storage-fiókok**területen jelenik meg. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [Azure Storage Explorer hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Csatolás helyi emulátorhoz

Storage Explorer jelenleg két hivatalos tároló-emulátort támogat:

* [Azure Storage-emulátor](storage/common/storage-use-emulator.md) (csak Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS vagy Linux)

Ha az emulátor figyeli az alapértelmezett portokat, az emulátor **-alapértelmezett portok** csomópont használatával érheti el az emulátort. Keresse meg az **emulátort – az alapértelmezett portokat** a **helyi & csatlakoztatott** > **Storage-fiókok**területen.

Ha más nevet szeretne használni a csatlakozáshoz, vagy ha az emulátor nem fut az alapértelmezett portokon, kövesse az alábbi lépéseket:

1. Indítsa el az emulátort. Adja meg a `AzureStorageEmulator.exe status` parancsot, hogy megjelenjenek a portok az egyes szolgáltatástípus-típusokhoz.

   > [!IMPORTANT]
   > A Storage Explorer nem indítja el automatikusan az emulátort. Manuálisan kell elindítania.

1. Válassza a **Kapcsolódás** szimbólumot az **Azure Storage-hoz való kapcsolódás**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Válassza **a csatolás helyi emulátorhoz**lehetőséget, majd kattintson a **tovább**gombra.

1. Válassza ki a kapcsolatok megjelenítendő nevét, és adja meg azokat a portokat, amelyeken az emulátor figyeli az egyes szolgáltatások típusait. A **helyi emulátorhoz való csatolás** a legtöbb emulátor alapértelmezett portszámát mutatja. A **fájlok portja** üres, mert egyik hivatalos emulátor sem támogatja a Files szolgáltatást. Ha a használt emulátor támogatja a fájlokat, megadhatja a használni kívánt portot. Ezután válassza a **tovább**lehetőséget.

1. Tekintse át a **kapcsolatok összegzését** , és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben válassza a **vissza** lehetőséget, ha vissza szeretne térni az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa a kapcsolódást jelölő csomópontra kerül. A csomópontnak a **helyi & csatolt** > **Storage-fiókokban**kell megjelennie. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [Azure Storage Explorer hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Kapcsolódás Azure Cosmos DB fiókhoz kapcsolati sztring használatával

Azure Cosmos DB fiókok Azure-előfizetésen keresztüli kezelése helyett kapcsolati karakterlánc használatával kapcsolódhat Azure Cosmos DBhoz. A csatlakozáshoz kövesse az alábbi lépéseket:

1. Az **Explorer**alatt bontsa ki a **helyi & csatolva**elemet, kattintson a jobb gombbal **Cosmos db fiókok**elemre, majd válassza **a Kapcsolódás a**következőhöz: Azure Cosmos db

    ![Csatlakozás az Azure Cosmos DB-hez kapcsolati sztringgel][21]

1. Válassza ki a Azure Cosmos DB API-t, adja meg a **kapcsolati karakterlánc** adatait, majd kattintson az **OK** gombra a Azure Cosmos db fiók csatlakoztatásához. További információ a kapcsolódási karakterlánc beolvasásáról: [Azure Cosmos-fiók kezelése](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Kapcsolati sztring][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Kapcsolódás a Azure Data Lake Storehoz URI használatával

Elérheti az előfizetésében nem szereplő erőforrásokat. Szüksége van egy olyan személyre, aki hozzáfér az adott erőforráshoz, hogy megadja az erőforrás URI-JÁT. A bejelentkezést követően az URI használatával csatlakozhat a Data Lake Storehoz. A csatlakozáshoz kövesse az alábbi lépéseket:

1. Az **Explorer**alatt bontsa ki a **helyi & csatolva**elemet.

1. Kattintson a jobb gombbal a **Data Lake Storage Gen1**elemre, majd válassza **a kapcsolódás Data Lake Storage Gen1**lehetőséget.

    ![Kapcsolódás Data Lake Store helyi menühöz](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Adja meg az URI-t, majd kattintson **az OK gombra**. A Data Lake Store a **Data Lake Storage**alatt jelenik meg.

    ![Kapcsolódás Data Lake Store eredményhez](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Ez a példa Data Lake Storage Gen1 használ. A Azure Data Lake Storage Gen2 mostantól elérhető. További információ: [What is Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Közös hozzáférési aláírás létrehozása Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Fiók szintű közös hozzáférés aláírása

1. Kattintson a jobb gombbal a megosztani kívánt Storage-fiókra, majd válassza a **közös hozzáférési aláírás beolvasása**elemet.

    ![Közös hozzáférésű aláírás helyi menüjének beolvasása][14]

1. A **megosztott hozzáférés aláírása**lapon adja meg a fiókhoz használni kívánt időkeretet és engedélyeket, majd válassza a **Létrehozás**lehetőséget.

    ![Közös hozzáférési aláírás beszerzése][15]

1. Másolja a **kapcsolódási karakterláncot** vagy a nyers **lekérdezési karakterláncot** a vágólapra.

### <a name="service-level-shared-access-signature"></a>A szolgáltatási szint megosztott hozzáférésének aláírása

A szolgáltatás szintjén közös hozzáférési aláírást kaphat. További információ: [sas beszerzése blob-tárolóhoz](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Tárfiókok keresése

A tárolási erőforrások kereséséhez kereshet az **Explorer** ablaktáblán.

Amikor szöveget ír be a keresőmezőbe, Storage Explorer megjeleníti az összes olyan erőforrást, amely megfelel az adott ponthoz megadott keresési értéknek. Ez a példa a **végpontok**keresését mutatja be:

![Tárfiók keresése][23]

> [!NOTE]
> A keresés felgyorsításához a **Fiókkezelés** használatával törölje azokat az előfizetéseket, amelyek nem tartalmazzák a keresett elemet. Kattintson a jobb gombbal a csomópontra, és válassza a **Keresés itt** lehetőséget egy adott csomópont keresésének megkezdéséhez.
>
>

## <a name="next-steps"></a>További lépések

* [Azure Blob Storage-erőforrások kezelése Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Adatok kezelése az Azure Storage Explorerrel](./cosmos-db/storage-explorer.md)
* [Azure Data Lake Store erőforrások kezelése a Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
