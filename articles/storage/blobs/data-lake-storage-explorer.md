---
title: Azure Storage Explorer használata a Azure Data Lake Storage Gen2
description: A Azure Storage Explorer segítségével kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4e8b99d4a9c48692551e3aba06aba1a3db385e60
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466051"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>A Azure Storage Explorer használatával kezelheti a címtárakat, a fájlokat és a hozzáférés-vezérlési listákat Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet könyvtárakat, fájlokat és engedélyeket a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus NÉVTÉR (HNS).

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.
> * Azure Storage Explorer telepítve a helyi számítógépen. Az Azure Storage Explorer Windows, Macintosh vagy Linux rendszerre való letöltése: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Bejelentkezés Storage Explorer

A Storage Explorer első indításakor megjelenik a **Microsoft Azure Storage Explorer – Csatlakozás** ablak. Míg Storage Explorer számos módszert biztosít a Storage-fiókokhoz való kapcsolódáshoz, a hozzáférés-vezérlési listák kezelése jelenleg csak egyetlen módon támogatott.

|Tevékenység|Szerep|
|---|---|
|Azure-fiók hozzáadása | Átirányítja a szervezet bejelentkezési lapjára az Azure-ba való hitelesítéshez. Jelenleg ez az egyetlen támogatott hitelesítési módszer, ha az ACL-eket szeretné kezelni és beállítani.|
|Kapcsolati sztring vagy közös hozzáférésű jogosultságkód URI azonosítójának használata | Egy tároló vagy tárfiók közvetlen elérésére szolgál egy SAS-token vagy egy közös kapcsolati sztring segítségével. |
|Tárfióknév és -kulcs használata| Csatlakozzon az Azure Storage-hoz a tárfiók nevével és kulcsával.|

Válassza **Az Azure-fiók hozzáadása** lehetőséget, és kattintson **a bejelentkezés..**. elemre. A képernyőn megjelenő utasításokat követve jelentkezzen be az Azure-fiókjába.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/connect.png)

A csatlakozás befejeztével az Azure Storage Explorer betöltést követően megjeleníti az **Explorer** lapot. Ezen a lapon áttekintheti az összes Azure-tárfiókját, valamint az [Azure Storage Emulator](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)- és [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-fiókokon, illetve az [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-környezeteken keresztül konfigurált helyi tárolókat.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Tároló létrehozása

A tárolók könyvtárakat és fájlokat tárolnak. Ha létre szeretne hozni egyet, bontsa ki a folytatás lépésben létrehozott Storage-fiókot. Válassza a **Blobtárolók** lehetőséget, kattintson a jobb gombbal, majd válassza a **Blobtároló létrehozása** lehetőséget. Adja meg a tároló nevét. A tárolók elnevezésére vonatkozó szabályok és korlátozások listáját lásd: [Container létrehozása](storage-quickstart-blobs-dotnet.md#create-a-container) szakasz. Ha elkészült, nyomja le az **ENTER** billentyűt a tároló létrehozásához. Miután a tároló sikeresen létrejött, a kiválasztott Storage-fiók **blob containers** mappájában jelenik meg.

![Microsoft Azure Storage Explorer-tároló létrehozása](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Könyvtár létrehozása

Könyvtár létrehozásához válassza ki a folytatás lépésben létrehozott tárolót. A tároló menüszalagon kattintson az **új mappa** gombra. Adja meg a címtár nevét. Ha elkészült, nyomja le az **ENTER** billentyűt a könyvtár létrehozásához. Miután a címtár sikeresen létrejött, megjelenik a szerkesztő ablakban.

![Microsoft Azure Storage Explorer – könyvtár létrehozása](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Blobok feltöltése a könyvtárba

A címtár menüszalagján válassza a **feltöltés** gombot. Ez a művelet lehetővé teszi egy mappa vagy fájl feltöltését.

Válassza ki a feltölteni kívánt fájlokat vagy mappát.

![Microsoft Azure Storage Explorer – Blob feltöltése](media/data-lake-storage-explorer/upload-file.png)

Ha az **OK** gombra kattint, a kiválasztott fájlokat a rendszer sorba állítja a feltöltéshez, majd egyesével feltölti azokat. A feltöltés befejeztével az eredmények megjelennek a **Tevékenységek** ablakban.

## <a name="view-blobs-in-a-directory"></a>Blobok megtekintése egy könyvtárban

A **Azure Storage Explorer** alkalmazásban válasszon ki egy könyvtárat egy Storage-fiókban. A fő ablaktábla a kiválasztott könyvtárban lévő Blobok listáját jeleníti meg.

![Microsoft Azure Storage Explorer – Blobok listázása egy könyvtárban](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Blobok letöltése

A fájlok **Azure Storage Explorer**használatával történő letöltéséhez jelölje ki a fájlt, és válassza a **Letöltés** lehetőséget a menüszalagon. Megnyílik egy fájl párbeszédpanel, amelyen megadhat egy fájlnevet. A **Mentés** gombra kattintva elindíthatja a fájl letöltését a helyi helyre.

## <a name="managing-access"></a>Hozzáférés-kezelés

A tároló gyökerén is beállíthat engedélyeket. Ehhez be kell jelentkeznie Azure Storage Explorerba az egyéni fiókjával (a kapcsolódási karakterlánccal ellentétben). Kattintson a jobb gombbal a tárolóra, és válassza az **engedélyek kezelése**lehetőséget, és hozza létre az **engedély kezelése** párbeszédpanelt.

![Microsoft Azure Storage Explorer – címtár-hozzáférés kezelése](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Az **engedély kezelése** párbeszédpanel lehetővé teszi a tulajdonos és a tulajdonosok csoport engedélyeinek kezelését. Azt is lehetővé teszi, hogy új felhasználókat és csoportokat vegyen fel a hozzáférés-vezérlési listára, ahol az engedélyeket kezelheti.

Ha új felhasználót vagy csoportot szeretne hozzáadni a hozzáférés-vezérlési listához, válassza a **felhasználó vagy csoport hozzáadása** mezőt.

Adja meg a listához hozzáadni kívánt Azure Active Directory-(HRE-) bejegyzést, majd válassza a **Hozzáadás**lehetőséget.

A felhasználó vagy csoport ekkor megjelenik a **felhasználók és csoportok:** mezőben, így megkezdheti az engedélyeik kezelését.

> [!NOTE]
> Ajánlott eljárás, és javasoljuk, hogy hozzon létre egy biztonsági csoportot a HRE-ben, és tartsa meg az engedélyeket a csoporton az egyes felhasználók helyett. A javaslat részletes ismertetését, valamint az egyéb ajánlott eljárásokat lásd: [ajánlott eljárások Data Lake Storage Gen2hoz](data-lake-storage-best-practices.md).

Az engedélyek két kategóriája adható meg: hozzáférés-vezérlési listák és alapértelmezett ACL-ek.

* **Hozzáférés**: hozzáférés-vezérlési listák vezérlik az objektumokhoz való hozzáférést. A fájlok és könyvtárak egyaránt rendelkeznek hozzáférési ACL-ekkel.

* **Alapértelmezett**: a címtárhoz társított ACL-ek sablonja, amely meghatározza az adott címtárban létrehozott alárendelt elemek hozzáférési ACL-jeit. A fájlok nem rendelkeznek alapértelmezett ACL-ekkel.

Mindkét kategórián belül három engedély van a fájlok vagy könyvtárak hozzárendeléséhez: **olvasás**, **írás**és **végrehajtás**.

>[!NOTE]
> Az itt megadott beállítások megadásakor a rendszer nem állítja be az engedélyeket a címtárban lévő aktuális elemekhez. Ha a fájl már létezik, minden egyes elemhez meg kell adnia az engedélyeket manuálisan.

Az egyes címtárakra, valamint az egyes fájlokra vonatkozó engedélyeket is kezelheti, ami lehetővé teszi a részletes hozzáférés-vezérlést. A könyvtárak és fájlok engedélyeinek kezelésére szolgáló folyamat ugyanaz, mint a fentiekben leírtak szerint. Kattintson a jobb gombbal arra a fájlra vagy könyvtárra, amelyre az engedélyeket szeretné kezelni, és kövesse ugyanezt a folyamatot.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg Data Lake Storage Gen2 hozzáférés-vezérlési listájával.

> [!div class="nextstepaction"]
> [Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
