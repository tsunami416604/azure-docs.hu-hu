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
ms.openlocfilehash: 2b923a381be9d6cdb1a83f582a541c9e17e117a4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086628"
---
# <a name="get-started-with-storage-explorer"></a>Ismerkedés a Storage Explorer

## <a name="overview"></a>Áttekintés

A Microsoft Azure Storage Explorer egy önálló alkalmazás, amely megkönnyíti az Azure Storage-szolgáltatásokkal való munkát Windows, macOS és Linux rendszereken. Ebből a cikkből megismerheti az Azure Storage-fiókok csatlakoztatásának és kezelésének számos módját.

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

A Storage Explorer a Linux leggyakoribb disztribúcióinak [beépülő moduljában](https://snapcraft.io/storage-explorer) érhető el, és az ajánlott telepítési módszer. A Storage Explorer beépülő modul automatikusan telepíti az összes függőségét és frissítését, amikor a rendszer közzéteszi az új verziókat az illesztési tárolóban.

A támogatott disztribúciók listájának megjelenítéséhez nyissa meg a [beépülő modult](https://snapcraft.io/docs/installing-snapd).

Storage Explorer szükség van egy Password Manager használatára, amelyhez esetleg manuálisan kell kapcsolódnia, mielőtt Storage Explorer megfelelően működni fognak. A következő parancs futtatásával csatlakozhat Storage Explorer a rendszer jelszavas kezelőjéhez:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer a. tar. gz letöltésként is elérhető, de a függőségeket manuálisan kell telepítenie. A. tar. gz telepítését a következő Linux-disztribúciók támogatják:

* Ubuntu 18,04 x64
* Ubuntu 16,04 x64
* Ubuntu 14,04 x64

A. tar. gz telepítése más disztribúciókban is működhet, de csak ezek a felsoroltak támogatottak.

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
> Ahhoz, hogy a bejelentkezés után teljesen hozzáférjen az erőforrásokhoz, Storage Explorer a felügyeleti (Azure Resource Manager) és az adatrétegbeli engedélyeket is. Ez azt jelenti, hogy szüksége van Azure Active Directory (Azure AD) engedélyekre, amelyek hozzáférést biztosítanak a Storage-fiókjához, a fiókhoz tartozó tárolóhoz és a tárolókban lévő adattárakhoz. Ha csak az adatréteghez rendelkezik engedélyekkel, vegyen fel [egy erőforrást az Azure ad](#add-a-resource-via-azure-ad)-n keresztül. A Storage Explorer szükséges konkrét engedélyekkel kapcsolatos további információkért tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).

1. A Storage Explorer területen válassza a **fiókok kezelése** lehetőséget a fiókkezelés panel megnyitásához.

    ![Fiókok kezelése][1]

2. A bal oldali ablaktábla mostantól megjeleníti az összes olyan Azure-fiókot, amelybe bejelentkezett. Egy másik fiókhoz való kapcsolódáshoz válassza a **fiók hozzáadása**lehetőséget.

3. Ha egy nemzeti felhőbe vagy egy Azure Stackba szeretne bejelentkezni, válassza ki az **Azure-környezet** legördülő listát, és válassza ki a használni kívánt Azure-felhőt. Miután kiválasztotta a környezetét, válassza a **Bejelentkezés** gombot. További információ: a [Storage Explorer összekötése egy Azure stack-előfizetéssel](/azure-stack/user/azure-stack-storage-connect-se).

    ![Bejelentkezési lehetőség][2]

4. Miután sikeresen bejelentkezett egy Azure-fiókkal, a fiók és az ahhoz társított Azure-előfizetések hozzáadódnak a bal oldali ablaktáblához. Válassza ki azt az Azure-előfizetést, amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Az **összes előfizetés** kiválasztásával kiválaszthatja a kijelölt Azure-előfizetések közül az összeset vagy az egyiket sem.)

    ![Azure-előfizetések kiválasztása][3]

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.

    ![Kiválasztott Azure-előfizetések][4]

### <a name="attach-a-specific-resource"></a>Adott erőforrás csatolása

A Storage Explorer több módon is csatolhat erőforrásokhoz:

* [Erőforrás hozzáadása az Azure ad-n keresztül](#add-a-resource-via-azure-ad). Ha csak az adatréteghez rendelkezik engedélyekkel, akkor ezzel a lehetőséggel hozzáadhat egy BLOB-tárolót vagy egy Azure Data Lake Storage Gen2 blob Storage-tárolót.
* [Használjon egy kapcsolatok karakterláncot](#use-a-connection-string). Akkor használja ezt a beállítást, ha egy Storage-fiókhoz tartozik egy kapcsolódási sztring. A Storage Explorer a kulcs-és [sas](storage/common/storage-dotnet-shared-access-signature-part-1.md) -kapcsolatok karakterláncait is támogatja.
* [Használjon sas URI](#use-a-sas-uri)-t. Ha az [sas URI-ja](storage/common/storage-dotnet-shared-access-signature-part-1.md) egy blob-tárolóhoz, fájlmegosztáshoz, várólistához vagy táblához tartozik, használja az erőforráshoz való csatoláshoz. SAS URI beszerzéséhez használhatja [Storage Explorer](#generate-a-sas-in-storage-explorer) vagy a [Azure Portal](https://portal.azure.com).
* Adjon meg [egy nevet és egy kulcsot](#use-a-name-and-key). Ha ismeri a fiók kulcsainak valamelyikét a Storage-fiókjához, akkor ezzel a lehetőséggel gyorsan csatlakozhat. A Storage-fiók kulcsa a [Azure Portal](https://portal.azure.com)Storage-fiókhoz tartozó **hozzáférési kulcsok** paneljén található.
* [Csatoljon egy helyi emulátorhoz](#attach-to-a-local-emulator). Ha az elérhető Azure Storage-emulátorok egyikét használja, ezzel a lehetőséggel egyszerűen csatlakozhat az emulátorhoz.
* [Kapcsolódás Azure Cosmos db fiókhoz kapcsolati karakterlánc használatával](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Akkor használja ezt a beállítást, ha egy CosmosDB-példányhoz tartozik egy kapcsolódási sztring.
* [Azure Data Lake Storehoz való kapcsolódás URI-n keresztül](#connect-to-azure-data-lake-store-by-uri). Akkor használja ezt a beállítást, ha a Azure Data Lake Store URI azonosítóval rendelkezik.

#### <a name="add-a-resource-via-azure-ad"></a>Erőforrás hozzáadása az Azure AD-n keresztül

1. Nyissa meg a **kapcsolat** párbeszédpanelt a bal oldali függőleges eszköztáron a **kapcsolat** gombra kattintva:

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Ha még nem tette meg, használja az **Azure-fiók hozzáadása** lehetőséget az erőforráshoz hozzáférő Azure-fiókba való bejelentkezéshez. A bejelentkezés után térjen vissza a **Kapcsolódás** párbeszédpanelre.

3. Válassza **az erőforrás hozzáadása Azure Active Directory (Azure ad)** lehetőséget, majd kattintson a **tovább**gombra.

4. Válassza ki azt az Azure-fiókot és-bérlőt, amelyhez hozzáféréssel rendelkezik a csatolni kívánt tárolási erőforráshoz. Kattintson a **Tovább** gombra.

5. Válassza ki a csatolni kívánt erőforrás típusát, majd adja meg a kapcsolódáshoz szükséges adatokat. Az ezen a lapon megadott információk attól függnek, hogy milyen típusú erőforrást szeretne hozzáadni. Ügyeljen arra, hogy a megfelelő típusú erőforrást adja meg. A szükséges információk megadása után válassza a **tovább**lehetőséget.

6. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben a **vissza** gomb használatával térjen vissza az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan a kapcsolódást jelképező csomópontra kerül. Ha nem az adott csomópontra mutat, keresse meg a következőt: **helyi & csatolt** > **Storage-fiókok** >  **(csatolt tárolók) blob-**  > **tárolók**. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Kapcsolati sztring használata

1. Nyissa meg a **kapcsolat** párbeszédpanelt a bal oldali függőleges eszköztáron a **kapcsolat** gombra kattintva:

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Válassza **a kapcsolatok karakterlánc használata**lehetőséget, majd kattintson a **tovább**gombra.

3. Válassza ki a kapcsolatok megjelenítendő nevét, és adja meg a kapcsolatok karakterláncát. Ezután válassza a **Tovább** lehetőséget.

4. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben a **vissza** gomb használatával térjen vissza az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan a kapcsolódást jelképező csomópontra kerül. Ha nem a csomópontra lép, tekintse meg a **helyi & csatolt** > **Storage-fiókok**lehetőséget. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-sas-uri"></a>SAS URI használata

1. Nyissa meg a **kapcsolat** párbeszédpanelt a bal oldali függőleges eszköztáron a **kapcsolat** gombra kattintva:

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Válassza **a közös hozzáférésű aláírás (SAS) URI használata**lehetőséget, majd kattintson a **tovább**gombra.

3. Válassza ki a kapcsolatok megjelenítendő nevét, és adja meg az SAS URI azonosítóját. A csatolni kívánt erőforrás-típushoz tartozó szolgáltatási végpontot az automatikus kitöltés értékre kell írni. Ha egyéni végpontot használ, lehetséges, hogy előfordulhat, hogy nem. Kattintson a **Tovább** gombra.

4. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben a **vissza** gomb használatával térjen vissza az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan a kapcsolódást jelképező csomópontra kerül. Ha nem az adott csomópontra mutat, keresse meg a csatlakoztatott**tárolók** >  **(csatlakoztatott** > tárolók) **helyi &**  > *a szolgáltatás csomópontját*. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-name-and-key"></a>Név és kulcs használata

1. Nyissa meg a **kapcsolat** párbeszédpanelt a bal oldali függőleges eszköztáron a **kapcsolat** gombra kattintva:

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Válassza **a Storage-fiók nevének és kulcsának használata**lehetőséget, majd kattintson a **tovább**gombra.

3. Válassza ki a kapcsolatok megjelenítendő nevét.

4. Adja meg a Storage-fiók nevét és a hozzá tartozó hozzáférési kulcsok egyikét.

5. Válassza ki a használni kívánt **tárolási tartományt** , majd kattintson a **tovább**gombra.

6. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben a **vissza** gomb használatával térjen vissza az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan a kapcsolódást jelképező csomópontra kerül. Ha nem a csomópontra lép, tekintse meg a **helyi & csatolt** > **Storage-fiókok**lehetőséget. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Csatolás helyi emulátorhoz

Storage Explorer jelenleg két hivatalos tároló-emulátort támogat:
* [Azure Storage-emulátor](storage/common/storage-use-emulator.md) (Csak Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS vagy Linux)

Ha az emulátor figyeli az alapértelmezett portokat, használhatja az emulátor **– alapértelmezett portok** csomópontot (a **helyi & csatolt** > **Storage-fiókok**területen található) az emulátor gyors eléréséhez.

Ha más nevet szeretne használni a csatlakozáshoz, vagy ha az emulátor nem fut az alapértelmezett portokon, kövesse az alábbi lépéseket:

1. Indítsa el az emulátort. Ha ezt teszi, jegyezze fel a portokat, amelyekkel az emulátor figyeli az egyes szolgáltatások típusait.

   > [!IMPORTANT]
   > A Storage Explorer nem indítja el automatikusan az emulátort. Manuálisan kell elindítania.

2. Nyissa meg a **kapcsolat** párbeszédpanelt a bal oldali függőleges eszköztáron a **kapcsolat** gombra kattintva:

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

3. Válassza **a csatolás helyi emulátorhoz**lehetőséget, majd kattintson a **tovább**gombra.

4. Válassza ki a kapcsolatok megjelenítendő nevét, és adja meg azokat a portokat, amelyeken az emulátor figyeli az egyes szolgáltatások típusait. A szövegmezők a legtöbb emulátor alapértelmezett portszámait fogják kezdeni. A **fájlok port** mező üresen marad, mert egyik hivatalos emulátor sem támogatja a Files szolgáltatást. Ha a használt emulátor támogatja a fájlokat, megadhatja a használni kívánt portot. Ezután válassza a **Tovább** lehetőséget.

5. Tekintse át a kapcsolatok összegzését, és győződjön meg arról, hogy minden adat helyes. Ha igen, válassza a **kapcsolat**lehetőséget. Ellenkező esetben a **vissza** gomb használatával térjen vissza az előző lapokra a helytelen információk kijavításához.

A kapcsolódás sikeres hozzáadása után az erőforrás-fa automatikusan a kapcsolódást jelképező csomópontra kerül. Ha nem a csomópontra lép, tekintse meg a **helyi & csatolt** > **Storage-fiókok**lehetőséget. Ha Storage Explorer nem tudta felvenni a kapcsolatot, vagy ha a kapcsolat sikeres hozzáadása után nem fér hozzá az adataihoz, tekintse meg a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Kapcsolódás Azure Cosmos DB fiókhoz kapcsolati sztring használatával

Azure Cosmos DB fiókok Azure-előfizetésen keresztüli kezelésének alternatívájaként egy kapcsolati sztring használatával is csatlakozhat Azure Cosmos DBhoz. Ehhez kövesse az alábbi lépéseket:

1. Az erőforrás-fa bal oldalán bontsa ki a **helyi és csatolt**elemet, kattintson a jobb gombbal **Azure Cosmos db fiókok**elemre, majd válassza **a kapcsolódás Azure Cosmos db**lehetőséget.

    ![Csatlakozás az Azure Cosmos DB-hez kapcsolati sztringgel][21]

2. Válassza ki a Azure Cosmos DB API-t, adja meg a **kapcsolati karakterlánc** adatait, majd kattintson az **OK** gombra a Azure Cosmos db fiók csatlakoztatásához. A kapcsolódási karakterlánc beolvasásával kapcsolatos további információkért lásd: [a kapcsolódási karakterlánc](https://docs.microsoft.com/azure/cosmos-db/manage-account)beolvasása.

    ![Kapcsolati sztring][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Kapcsolódás a Azure Data Lake Storehoz URI használatával

Ha olyan erőforráshoz szeretne hozzáférni, amely nem szerepel az előfizetésében, szüksége lesz egy olyan személyre, aki hozzáfér ehhez az erőforráshoz, hogy megadja az erőforrás URI azonosítóját. A bejelentkezést követően az URI használatával kapcsolódhat Data Lake Storehoz. Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg a Storage Explorert.
2. A bal oldali panelen bontsa ki a **Helyi és csatolt** elemet.
3. Kattintson a jobb gombbal a **Data Lake Store**elemre. A helyi menüben válassza a **kapcsolódás Data Lake Storehoz**lehetőséget.

    ![Kapcsolódás Data Lake Store helyi menühöz](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Adja meg az URI-t. Az eszköz az imént megadott URL-cím helyére kerül.

    ![Kapcsolódás Data Lake Store környezethez párbeszédpanel](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Kapcsolódás Data Lake Store eredményhez](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>SAS létrehozása Storage Explorer

### <a name="account-level-sas"></a>Fiók szintű SAS

1. Kattintson a jobb gombbal a megosztani kívánt Storage-fiókra, majd válassza a **közös hozzáférési aláírás beolvasása**elemet.

    ![SAS beszerzése menüpont][14]

2. A **megosztott hozzáférés aláírásának létrehozása** párbeszédpanelen adja meg a fiókhoz használni kívánt időkeretet és engedélyeket, majd válassza a **Létrehozás**lehetőséget.

    ![SAS beolvasása párbeszédpanel][15]

3. Mostantól a vágólapra másolhatja a **kapcsolódási karakterláncot** vagy a nyers **lekérdezési karakterláncot** .

### <a name="service-level-sas"></a>Szolgáltatási szint SAS

[SAS beszerzése blob-tárolóhoz Storage Explorer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Tárfiókok keresése

Ha meg kell találnia egy tárolási erőforrást, és nem tudja, hol található, a bal oldali ablaktábla tetején található keresőmező használatával megkeresheti az erőforrást.

A keresőmező beírásakor a bal oldali ablaktábla megjeleníti az összes olyan erőforrást, amely megfelel az adott ponthoz megadott keresési értéknek. A **végpontok** keresése például a következő képernyőképen látható:

![Tárfiók keresése][23]

> [!NOTE]
> A keresés felgyorsításához a fiókkezelés panelen törölje az összes olyan előfizetést, amely nem tartalmazza a keresett elemet. Kattintson a jobb gombbal a csomópontra, és válassza a **Keresés itt** lehetőséget egy adott csomópont keresésének megkezdéséhez.
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
