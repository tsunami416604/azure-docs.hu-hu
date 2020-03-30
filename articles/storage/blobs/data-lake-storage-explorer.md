---
title: Az Azure Storage Explorer használata az Azure Data Lake Storage Gen2 szolgáltatással
description: Az Azure Storage Explorer segítségével kezelheti a könyvtárakat és a fájl- és címtárhozzáférés-vezérlési listákat (ACL) olyan tárfiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fca9fa8a964c6c9d69ffbb3036bd4774e0d1bd34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255547"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Az Azure Storage Explorer használatával kezelheti a könyvtárakat, fájlokat és ACL-eket az Azure Data Lake Storage Gen2 szolgáltatásban

Ez a cikk bemutatja, hogyan azure [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) létrehozása és kezelése könyvtárak, fájlok és engedélyek tárfiókok, amelyek hierarchikus névtér (HNS) engedélyezve van.

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan tárfiók, amelynek hierarchikus névtere (HNS) engedélyezve van. Az [alábbi](data-lake-storage-quickstart-create-account.md) utasításokat követve hozzon létre egyet.
> * Az Azure Storage Explorer telepítve van a helyi számítógépen. Az Azure Storage Explorer Windows, Macintosh vagy Linux rendszerre való letöltése: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Bejelentkezés a Storage Explorer be

A Storage Explorer első indításakor megjelenik a **Microsoft Azure Storage Explorer – Csatlakozás** ablak. Bár a Storage Explorer számos lehetőséget kínál a tárfiókokhoz való csatlakozásra, jelenleg csak egy módon támogatott az ACL-ok kezelése.

|Tevékenység|Cél|
|---|---|
|Azure-fiók hozzáadása | Átirányítja a szervezet bejelentkezési oldalára, hogy hitelesítse az Azure-ba. Jelenleg ez az egyetlen támogatott hitelesítési módszer, ha az ACL-okat szeretné kezelni és beállítani.|
|Kapcsolati sztring vagy közös hozzáférésű jogosultságkód URI azonosítójának használata | Egy tároló vagy tárfiók közvetlen elérésére szolgál egy SAS-token vagy egy közös kapcsolati sztring segítségével. |
|Tárfióknév és -kulcs használata| Csatlakozzon az Azure Storage-hoz a tárfiók nevével és kulcsával.|

Válassza **az Azure-fiók hozzáadása** lehetőséget, és kattintson a Bejelentkezés **gombra.** Kövesse a képernyőn megjelenő utasításokat az Azure-fiókjába való bejelentkezéshez.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/connect.png)

A csatlakozás befejeztével az Azure Storage Explorer betöltést követően megjeleníti az **Explorer** lapot. Ezen a lapon áttekintheti az összes Azure-tárfiókját, valamint az [Azure Storage Emulator](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)- és [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-fiókokon, illetve az [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-környezeteken keresztül konfigurált helyi tárolókat.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Tároló létrehozása

A tároló könyvtárakat és fájlokat tárol. Egyet létrehozni, bontsa ki a tárfiók létrehozott a folytatási lépésben. Válassza a **Blobtárolók** lehetőséget, kattintson a jobb gombbal, majd válassza a **Blobtároló létrehozása** lehetőséget. Adja meg a tároló nevét. A [tároló létrehozása](storage-quickstart-blobs-dotnet.md#create-a-container) szakaszban a tárolók elnevezésére vonatkozó szabályok és korlátozások listáját tartalmazza. Ha elkészült, nyomja le az **Enter** billentyűt a tároló létrehozásához. A tároló sikeres létrehozása után a kijelölt tárfiók **Blob Containers mappája** alatt jelenik meg.

![Microsoft Azure Storage Explorer – Tároló létrehozása](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Könyvtár létrehozása

Könyvtár létrehozásához jelölje ki a folytatási lépésben létrehozott tárolót. A tárolómenüszalagon válassza az **Új mappa** gombot. Adja meg a könyvtár nevét. Ha elkészült, nyomja le az **Enter** billentyűt a könyvtár létrehozásához. A könyvtár sikeres létrehozása után megjelenik a szerkesztőablakban.

![Microsoft Azure Storage Explorer – Könyvtár létrehozása](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Blobok feltöltése a könyvtárba

A könyvtár menüszalagján válassza a **Feltöltés** gombot. Ez a művelet lehetővé teszi egy mappa vagy fájl feltöltését.

Válassza ki a feltölteni kívánt fájlokat vagy mappát.

![Microsoft Azure Storage Explorer – Blob feltöltése](media/data-lake-storage-explorer/upload-file.png)

Ha az **OK** gombra kattint, a kiválasztott fájlokat a rendszer sorba állítja a feltöltéshez, majd egyesével feltölti azokat. A feltöltés befejeztével az eredmények megjelennek a **Tevékenységek** ablakban.

## <a name="view-blobs-in-a-directory"></a>Blobok megtekintése egy könyvtárban

Az **Azure Storage Explorer** alkalmazásban válasszon ki egy könyvtárat egy tárfiók alatt. A fő ablaktáblán a kijelölt könyvtárban lévő blobok listája látható.

![Microsoft Azure Storage Explorer – listablobok egy címtárban](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Blobok letöltése

Ha fájlokat szeretne letölteni az **Azure Storage Explorer**használatával, és egy kijelölt fájllal válassza a **Letöltés** menüszalagról lehetőséget. Megnyílik egy fájl párbeszédpanel, amelyen megadhat egy fájlnevet. A **Mentés gombra** választva indítsa el a fájl letöltését a helyi helyre.

## <a name="managing-access"></a>Hozzáférés-kezelés

Az engedélyeket a tároló gyökerében állíthatja be. Ehhez be kell jelentkeznie az Azure Storage Explorer be az egyéni fiók erre jogosultsággal (szemben a kapcsolati karakterlánc). Kattintson a jobb gombbal a tárolóra, és válassza az **Engedélyek kezelése**parancsot, és megjelenik az **Engedély kezelése** párbeszédpanel.

![Microsoft Azure Storage Explorer – Címtár-hozzáférés kezelése](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Az **Engedély kezelése** párbeszédpanelen kezelheti a tulajdonos és a tulajdonosok csoport engedélyeit. Azt is lehetővé teszi, hogy új felhasználókat és csoportokat vegyen fel a hozzáférés-vezérlési listára, amelyek hez ezután kezelheti az engedélyeket.

Ha új felhasználót vagy csoportot szeretne felvenni a hozzáférés-vezérlési listára, jelölje be a **Felhasználó vagy csoport hozzáadása** mezőt.

Adja meg a megfelelő Azure Active Directory (AAD) bejegyzést, amelyet hozzá szeretne adni a listához, majd válassza a **Hozzáadás lehetőséget.**

A felhasználó vagy csoport most antól megjelenik a **Felhasználók és csoportok:** mezőben, amely lehetővé teszi az engedélyeik kezelésének megkezdését.

> [!NOTE]
> Ajánlott eljárás, és ajánlott biztonsági csoportot létrehozni az AAD-ben, és az egyes felhasználók helyett a csoportra vonatkozó engedélyeket fenntartani. A javaslat részleteiről, valamint az egyéb ajánlott eljárásokról a [Data Lake Storage Gen2 ajánlott eljárásokat találja.](data-lake-storage-best-practices.md)

There are two categories of permissions you can assign: access ACLs and default ACLs.

* **Hozzáférés**: Az Access Hozzáférés-vezérlési vezérlése szabályozza az objektumhoz való hozzáférést. Files and directories both have access ACLs.

* **Default**: A template of ACLs associated with a directory that determines the access ACLs for any child items that are created under that directory. A fájlok nem rendelkeznek alapértelmezett ACL-okkal.

Mindkét kategórián belül három engedély rendelhető hozzá fájlokhoz vagy könyvtárakhoz: **Olvasás,** **Írás**és **Végrehajtás**.

>[!NOTE]
> Ha itt kiválasztja a kijelölést, az nem állítja be az engedélyeket a könyvtárban jelenleg létező elemekre vonatkozóan. Minden egyes elemre meg kell lépnie, és manuálisan kell beállítania az engedélyeket, ha a fájl már létezik.

Kezelheti az egyes könyvtárakra, valamint az egyes fájlokra vonatkozó engedélyeket, amelyek lehetővé teszik a részletes hozzáférés-vezérlést. A könyvtárak és a fájlok engedélyeinek kezelésének folyamata megegyezik a fent leírt eljárásokkal. Kattintson a jobb gombbal arra a fájlra vagy könyvtárra, amelyen az engedélyeket kezelni szeretné, és kövesse ugyanazt a folyamatot.

## <a name="next-steps"></a>További lépések

Ismerje meg a hozzáférés-vezérlési listákat a Data Lake Storage Gen2-ben.

> [!div class="nextstepaction"]
> [Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
