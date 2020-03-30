---
title: Ismerkedés a Storage Explorer rel | Microsoft dokumentumok
description: Azure-tárhelyerőforrások kezelése a Storage Explorer rel
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279792"
---
# <a name="get-started-with-storage-explorer"></a>A Storage Explorer használatának első lépései

## <a name="overview"></a>Áttekintés

A Microsoft Azure Storage Explorer egy önálló alkalmazás, amely megkönnyíti az Azure Storage-adatok windowsos, macOS és Linux rendszeren történő használatát. Ebben a cikkben az Azure storage-fiókokhoz való csatlakozás és az azure-beli tárfiókok kezelésének számos módját ismertetheti meg.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Előfeltételek

# <a name="windows"></a>[Windows](#tab/windows)

A Windows következő verziói támogatják a Storage Explorer böngészőt:

* Windows 10 (ajánlott)
* Windows 8
* Windows 7

A Storage Explorer a Windows összes verziójához a .

# <a name="macos"></a>[Macos](#tab/macos)

A macOS alábbi verziói támogatják a Storage Explorer böngészőt:

* macOS 10.12 Sierra és újabb verziók

# <a name="linux"></a>[Linux](#tab/linux)

A Storage Explorer a [Snap Store-ban](https://snapcraft.io/storage-explorer) érhető el a Linux leggyakoribb disztribúcióihoz. Ehhez a telepítéshez a Snap Store-t javasoljuk. A Tárolókezelő dokkolása telepíti az összes függőségét és frissítését, amikor új verziókat tesz közzé a Snap Store áruházban.

A támogatott disztribúciókról a [dokkolásos telepítési oldalon](https://snapcraft.io/docs/installing-snapd)találja.

A Tárolókezelő jelszókezelő használatát igényli. Előfordulhat, hogy manuálisan kell csatlakoznia egy jelszókezelőhöz. A Storage Explorer t a rendszer jelszókezelőjéhez a következő parancs futtatásával csatlakoztathatja:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

A Storage Explorer *.tar.gz* letöltésként is elérhető. A függőségeket manuálisan kell telepítenie. A Linux következő disztribúciói *támogatják a .tar.gz* telepítést:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

A *.tar.gz* telepítés működhet más disztribúciókon, de csak ezek a felsoroltak hivatalosan támogatottak.

A Storage Explorer Linuxon történő telepítéséhez tekintse meg a [Linux-függőségeket](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) az Azure Storage Explorer hibaelhárítási útmutatójában.

---

## <a name="download-and-install"></a>Letöltés és telepítés

A Storage Explorer letöltéséhez és telepítéséhez olvassa el az Azure Storage Explorer című [témakört.](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Csatlakozás egy tárfiókhoz vagy -szolgáltatáshoz

A Storage Explorer számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz. Általában akkor sem:

* [Jelentkezzen be az Azure-ba az előfizetések és az erőforrások eléréséhez](#sign-in-to-azure)
* [Adott storage- vagy CosmosDB-erőforrás csatolása](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

> [!NOTE]
> Az erőforrások teljes körű eléréséhez bejelentkezés után a Storage Explorer felügyeleti (Azure Resource Manager) és adatréteg-engedélyeket is igényel. Ez azt jelenti, hogy szüksége van az Azure Active Directory (Azure AD) engedélyek, amelyek hozzáférést biztosítanak a tárfiókhoz, a tárolók a fiókban, és az adatok a tárolókban. Ha csak az adatrétegen rendelkezik engedélyekkel, fontolja meg egy erőforrás hozzáadását az [Azure AD-n keresztül.](#add-a-resource-via-azure-ad) A Storage Explorer által igényelt engedélyekről az [Azure Storage Explorer hibaelhárítási útmutatójában](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)talál további információt.

1. A Tártárban válassza a**Fiókkezelés** **megtekintése** > lehetőséget, vagy kattintson a **Fiókok kezelése** gombra.

    ![Fiókok kezelése][1]

1. **A FIÓKKEZELÉS** mostantól megjeleníti az összes Olyan Azure-fiókot, amelybe bejelentkezett. Ha másik fiókhoz szeretne csatlakozni, válassza **a Fiók hozzáadása**lehetőséget.

1. A **Csatlakozás az Azure Storage-hoz**alkalmazásban válasszon ki egy Azure-felhőt az **Azure-környezetből,** és jelentkezzen be egy nemzeti felhőbe vagy egy Azure Stackbe. Miután kiválasztotta a környezetet, válassza a **Tovább gombot.**

    ![Bejelentkezési lehetőség][2]

    A Storage Explorer megnyit egy lapot a bejelentkezéshez. További információ: [Storage Explorer csatlakoztatása Azure Stack-előfizetéshez vagy -tárfiókhoz.](/azure-stack/user/azure-stack-storage-connect-se)

1. Miután sikeresen bejelentkezett egy Azure-fiókkal, a fiók és az adott fiókhoz társított Azure-előfizetések megjelennek a **FIÓKKEZELÉS**csoportban. Válassza az **Összes előfizetés lehetőséget,** ha a felsorolt Azure-előfizetések közül az összes vagy egyik között sem szeretné kijelölni a választást. Válassza ki azt az Azure-előfizetést, amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget.

    ![Azure-előfizetések kiválasztása][3]

    **Explorer** megjeleníti a kiválasztott Azure-előfizetésekhez társított tárfiókokat.

    ![Kiválasztott Azure-előfizetések][4]

### <a name="attach-a-specific-resource"></a>Adott erőforrás csatolása

A Storage Explorer alkalmazásban többféleképpen is csatolhat egy erőforráshoz:

* [Erőforrás hozzáadása az Azure AD-n keresztül.](#add-a-resource-via-azure-ad) Ha csak az adatrétegen rendelkezik engedélyekkel, ezzel a beállítással blobtárolót vagy egy Azure Data Lake Storage Gen2 Blob storage-tárolót adhat hozzá.
* [Használjon kapcsolati karakterláncot](#use-a-connection-string). Akkor használja ezt a beállítást, ha kapcsolati karakterláncot használ egy tárfiókhoz. A Storage Explorer támogatja a kulcs- és [a megosztott hozzáférésű aláírás-kapcsolati](storage/common/storage-dotnet-shared-access-signature-part-1.md) karakterláncokat is.
* [Közös hozzáférésű aláírás URI használata](#use-a-shared-access-signature-uri). Ha egy blobtárolóhoz, fájlmegosztáshoz, várólistához vagy táblához [megosztott hozzáférési aláírásURI-val](storage/common/storage-dotnet-shared-access-signature-part-1.md) rendelkezik, az erőforráshoz csatolhatja azt. A megosztott hozzáférésű aláírás URI-jának leéséhez használja a [Storage Explorer t](#generate-a-sas-in-storage-explorer) vagy az Azure [Portalt.](https://portal.azure.com)
* [Használjon nevet és kulcsot](#use-a-name-and-key). Ha ismeri a tárfiók bármelyik kulcsát, ezzel a beállítással gyorsan csatlakozhat. Keresse meg a kulcsokat a tárfiók lapján az [Azure PortalBeállítások](https://portal.azure.com) **Settings** > **hozzáférési kulcsai** kiválasztásával.
* [Csatolás helyi emulátorhoz](#attach-to-a-local-emulator). Ha a rendelkezésre álló Azure Storage-emulátorok egyikét használja, ezzel a beállítással egyszerűen csatlakozhat az emulátorhoz.
* [Csatlakozás egy Azure Cosmos DB-fiókhoz egy kapcsolati karakterlánc használatával.](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string) Akkor használja ezt a beállítást, ha kapcsolati karakterláncot használ egy CosmosDB-példányhoz.
* [Csatlakozás az Azure Data Lake Store-hoz URI-val.](#connect-to-azure-data-lake-store-by-uri) Akkor használja ezt a beállítást, ha rendelkezik az Azure Data Lake Store URI-val.

#### <a name="add-a-resource-via-azure-ad"></a>Erőforrás hozzáadása az Azure AD-n keresztül

1. Válassza a **Connect** szimbólumot a **Csatlakozás az Azure Storage-hoz**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Ha még nem tette meg, használja az **Azure-fiók hozzáadása** lehetőséget, hogy jelentkezzen be az Azure-fiókba, amely hozzáfér az erőforráshoz. Miután bejelentkezett, térjen vissza **az Azure Storage-hoz való csatlakozáshoz.**

1. Válassza **az Erőforrás hozzáadása az Azure Active Directoryn (Azure AD) keresztül**lehetőséget, majd a **Tovább**gombot.

1. Válasszon ki egy Azure-fiókot és bérlőt. Ezeknek az értékeknek hozzáféréssel kell rendelkezniük ahhoz a storage-erőforráshoz, amelyhez csatolni szeretné. Válassza a **Tovább lehetőséget.**

1. Válassza ki a csatolni kívánt erőforrástípust. Adja meg a csatlakozáshoz szükséges adatokat. 

   Az ezen az oldalon megadott adatok attól függnek, hogy milyen típusú erőforrást ad hozzá. Ügyeljen arra, hogy a megfelelő típusú erőforrást válassza. Miután megadta a szükséges adatokat, válassza a **Tovább gombot.**

1. Tekintse át a **Kapcsolatösszegzést,** és győződjön meg arról, hogy minden információ helyes. Ha így van, válassza a **Csatlakozás lehetőséget.** Ellenkező esetben válassza a **Vissza** lehetőséget az előző oldalakra való visszatéréshez a helytelen adatok javításához.

A kapcsolat sikeres hozzáadása után az erőforrásfa a kapcsolatot képviselő csomóponthoz kerül. Az erőforrás a **Helyi & csatlakoztatott** > **tárolók** > **(csatolt tárolók)** > **blobtárolók alatt**jelenik meg. Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem tud hozzáférni az adatokhoz, olvassa el az [Azure Storage Explorer hibaelhárítási útmutatóját.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)

#### <a name="use-a-connection-string"></a>Kapcsolati karakterlánc használata

1. Válassza a **Connect** szimbólumot a **Csatlakozás az Azure Storage-hoz**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Válassza **a Kapcsolati karakterlánc használata**lehetőséget, majd a **Tovább**gombot.

1. Válassza ki a kapcsolat megjelenítendő nevét, és adja meg a kapcsolati karakterláncot. Ezután válassza a **Tovább gombot.**

1. Tekintse át a **Kapcsolatösszegzést,** és győződjön meg arról, hogy minden információ helyes. Ha így van, válassza a **Csatlakozás lehetőséget.** Ellenkező esetben válassza a **Vissza** lehetőséget az előző oldalakra való visszatéréshez a helytelen adatok javításához.

A kapcsolat sikeres hozzáadása után az erőforrásfa a kapcsolatot képviselő csomóponthoz kerül. Az erőforrás a **Helyi & csatlakoztatott** > **tárfiókok csoportban**jelenik meg. Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem tud hozzáférni az adatokhoz, olvassa el az [Azure Storage Explorer hibaelhárítási útmutatóját.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)

#### <a name="use-a-shared-access-signature-uri"></a>Megosztott hozzáférésű aláírás URI-jának használata

1. Válassza a **Connect** szimbólumot a **Csatlakozás az Azure Storage-hoz**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Válassza **a SAS-kód használata (SAS) URI lehetőséget,** majd a **Tovább**gombot.

1. Válassza ki a kapcsolat megjelenítendő nevét, és adja meg a közös hozzáférésű aláírás URI-ját. A szolgáltatás végpontja az erőforrás típusát a csatolja kell automatikusan kitölteni. Ha egyéni végpontot használ, lehetséges, hogy nem. Válassza a **Tovább lehetőséget.**

1. Tekintse át a **Kapcsolatösszegzést,** és győződjön meg arról, hogy minden információ helyes. Ha így van, válassza a **Csatlakozás lehetőséget.** Ellenkező esetben válassza a **Vissza** lehetőséget az előző oldalakra való visszatéréshez a helytelen adatok javításához.

A kapcsolat sikeres hozzáadása után az erőforrásfa a kapcsolatot képviselő csomóponthoz kerül. Az erőforrás a csatlakoztatott tároló típusának **helyi & csatolt** > **tárolók** > **(csatolt tárolók)** > csoportban jelenik*meg.* Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, olvassa el az [Azure Storage Explorer hibaelhárítási útmutatóját.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) Tekintse meg a hibaelhárítási útmutatót, ha nem tud hozzáférni az adatokhoz a kapcsolat sikeres hozzáadása után.

#### <a name="use-a-name-and-key"></a>Név és kulcs használata

1. Válassza a **Connect** szimbólumot a **Csatlakozás az Azure Storage-hoz**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Válassza **a Tárfiók nevének és kulcsának használata**lehetőséget, majd a **Tovább**gombot.

1. Válassza ki a kapcsolat megjelenítendő nevét.

1. Adja meg a tárfiók nevét és a hozzáférési kulcsok egyikét.

1. Válassza ki a használni kívánt **tárolótartományt,** majd válassza a **Tovább**gombot.

1. Tekintse át a **Kapcsolatösszegzést,** és győződjön meg arról, hogy minden információ helyes. Ha így van, válassza a **Csatlakozás lehetőséget.** Ellenkező esetben válassza a **Vissza** lehetőséget az előző oldalakra való visszatéréshez a helytelen adatok javításához.

A kapcsolat sikeres hozzáadása után az erőforrásfa a kapcsolatot képviselő csomóponthoz kerül. Az erőforrás a **Helyi & csatlakoztatott** > **tárfiókok csoportban**jelenik meg. Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem tud hozzáférni az adatokhoz, olvassa el az [Azure Storage Explorer hibaelhárítási útmutatóját.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)

#### <a name="attach-to-a-local-emulator"></a>Csatolás helyi emulátorhoz

A Storage Explorer jelenleg két hivatalos storage-emulátort támogat:

* [Azure Storage-emulátor](storage/common/storage-use-emulator.md) (csak Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS vagy Linux)

Ha az emulátor figyeli az alapértelmezett portokat, használhatja az **Emulátor - Alapértelmezett port csomópontot** az emulátor eléréséhez. Keresse **meg az Emulátor - Alapértelmezett portok a** **Helyi & csatlakoztatott** > **tárolófiókok csoportban.**

Ha más nevet szeretne használni a kapcsolathoz, vagy ha az emulátor nem fut az alapértelmezett portokon, kövesse az alábbi lépéseket:

1. Indítsa el az emulátort. Adja meg `AzureStorageEmulator.exe status` az egyes szolgáltatástípusok portjainak megjelenítésére vonatkozó parancsot.

   > [!IMPORTANT]
   > A Storage Explorer nem indítja el automatikusan az emulátort. Manuálisan kell elindítania.

1. Válassza a **Connect** szimbólumot a **Csatlakozás az Azure Storage-hoz**megnyitásához.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

1. Válassza **a Csatolás helyi emulátorhoz**lehetőséget, majd a **Tovább**gombot.

1. Válassza ki a kapcsolat megjelenítendő nevét, és adja meg azokat a portokat, amelyeket az emulátor figyel az egyes szolgáltatástípusoknál. **A helyi emulátorhoz csatolás** a legtöbb emulátor alapértelmezett portértékeit javasolja. **A fájlport** üres, mert jelenleg egyik hivatalos emulátor sem támogatja a Files szolgáltatást. Ha a használt emulátor támogatja a Fájlokat, megadhatja a használni kívánt portot. Ezután válassza a **Tovább gombot.**

1. Tekintse át a **Kapcsolatösszegzést,** és győződjön meg arról, hogy minden információ helyes. Ha így van, válassza a **Csatlakozás lehetőséget.** Ellenkező esetben válassza a **Vissza** lehetőséget az előző oldalakra való visszatéréshez a helytelen adatok javításához.

A kapcsolat sikeres hozzáadása után az erőforrásfa a kapcsolatot képviselő csomóponthoz kerül. A csomópontnak meg kell jelennie **a Helyi & csatlakoztatott** > **tárfiókok csoportban.** Ha a Storage Explorer nem tudta hozzáadni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem tud hozzáférni az adatokhoz, olvassa el az [Azure Storage Explorer hibaelhárítási útmutatóját.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Csatlakozás Azure Cosmos DB-fiókhoz kapcsolati karakterlánc használatával

Az Azure Cosmos DB-fiókok Azure-előfizetésen keresztüli kezelése helyett csatlakozhat az Azure Cosmos DB-hez egy kapcsolati karakterlánc használatával. A csatlakozáshoz kövesse az alábbi lépéseket:

1. Az **EXPLORER**csoportban kattintson **a Helyi & csatolt**elemre, kattintson a jobb gombbal a **Cosmos DB-fiókok elemre,** és válassza **a Csatlakozás az Azure Cosmos DB-hoz**parancsot.

    ![Csatlakozás az Azure Cosmos DB-hez kapcsolati sztringgel][21]

1. Válassza ki az Azure Cosmos DB API-t, adja meg a csatlakozási karakterlánc-adatokat, majd válassza az **OK gombot** az Azure Cosmos DB-fiók csatlakoztatásához. **Connection String** A kapcsolati karakterlánc lekéréséről az [Azure Cosmos-fiók kezelése című témakörben](https://docs.microsoft.com/azure/cosmos-db/manage-account)talál további információt.

    ![Kapcsolati sztring][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Csatlakozás az Azure Data Lake Store áruházhoz URI-val

Hozzáférhet egy olyan erőforráshoz, amely nem az előfizetésében van. Szüksége van valakire, aki hozzáfér az erőforráshoz, hogy megadja az erőforrás URI-ját. Miután bejelentkezett, csatlakozzon a Data Lake Store-hoz az URI használatával. A csatlakozáshoz kövesse az alábbi lépéseket:

1. Az **EXPLORER**csoportban bontsa a **Helyi & csatolt csoportban.**

1. Kattintson a jobb gombbal a **Data Lake Storage Gen1**elemre, és válassza **a Csatlakozás a Data Lake Storage Gen1 szolgáltatáshoz parancsot.**

    ![Csatlakozás a Data Lake Áruház helyi menüjéhez](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Írja be az URI-t, majd kattintson **az OK gombra.** A Data Lake Store a **Data Lake Storage (Adattó-tároló) alatt**jelenik meg.

    ![Csatlakozás a Data Lake Store eredményéhez](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Ez a példa a Data Lake Storage Gen1-et használja. Az Azure Data Lake Storage Gen2 már elérhető. További információ: [Mi az Azure Data Lake Storage Gen1.](./data-lake-store/data-lake-store-overview.md)

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Megosztott hozzáférési aláírás létrehozása a Storage Explorer ben<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Fiókszintű megosztott hozzáférés-aláírás

1. Kattintson a jobb gombbal a megosztani kívánt tárfiókra, és válassza **a Megosztott hozzáférésű aláírás beszereznie parancsot.**

    ![Megosztott hozzáférésű aláírás helyi menüjének beszereznie][14]

1. A **Megosztott hozzáférésű aláírás csoportban**adja meg a fiókhoz használni kívánt időkeretet és engedélyeket, majd válassza a **Létrehozás gombot.**

    ![Közös hozzáférésű aláírás beszereznie][15]

1. Másolja a **vágólapra** a Kapcsolat vagy a nyers **lekérdezési karakterláncot** a vágólapra.

### <a name="service-level-shared-access-signature"></a>Szolgáltatásiszint megosztott hozzáférési aláírása

A szolgáltatás szintjén kaphat közös hozzáférési aláírást. További információ: [A SAS beszereznie egy blobtárolóhoz](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)című témakörben talál.

## <a name="search-for-storage-accounts"></a>Tárfiókok keresése

A tárolóerőforrás megkereséséhez keressen az **INTÉZ** ablaktáblában.

Amikor szöveget ír be a keresőmezőbe, a Tárolókezelő megjeleníti az összes olyan erőforrást, amely megfelel az addig megadott keresési értéknek. Ez a példa **végpontok**keresését mutatja be:

![Tárfiók keresése][23]

> [!NOTE]
> A keresés felgyorsítása érdekében a **Fiókkezelés segítségével** törölje a keresést nem tartalmazó előfizetések kijelölését. A jobb gombbal is kattinthat egy csomópontra, és a **Keresés innen** parancsot választva egy adott csomópontról kezdheti a keresést.
>
>

## <a name="next-steps"></a>További lépések

* [Az Azure Blob tárolási erőforrásainak kezelése a Storage Explorer segítségével](vs-azure-tools-storage-explorer-blobs.md)
* [Adatok kezelése az Azure Storage Explorerrel](./cosmos-db/storage-explorer.md)
* [Az Azure Data Lake Store erőforrásainak kezelése a Storage Explorer segítségével](./data-lake-store/data-lake-store-in-storage-explorer.md)

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
