---
title: A Azure Storage Explorer használatával kezelheti az adatAzure Data Lake Storage Gen2
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre tárolót egy Azure Data Lake Storage Gen2-fiókban, valamint egy könyvtárat és egy fájlt a Azure Storage Explorer használatával. Ezután megtudhatja, hogyan töltheti le a fájlt a helyi számítógépre, és hogyan tekintheti meg az összes fájlt egy címtárban.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 95d7a58c8188e8c6633f6be50af608aed437edff
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991419"
---
# <a name="use-azure-storage-explorer-to-manage-data-in-an-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage Gen2-fiókban tárolt adatkezelés Azure Storage Explorer használata

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy könyvtárat és egy blobot a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépre, és hogyan tekintheti meg az összes blobot egy címtárban. Azt is megtudhatja, hogyan hozhat létre egy blob pillanatképét, hogyan kezelheti a címtár-hozzáférési házirendeket, és hogyan hozhat létre közös hozzáférési aláírást.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

A rövid útmutatóhoz az Azure Storage Explorer telepítése szükséges. Az Azure Storage Explorer Windows, Macintosh vagy Linux rendszerre való letöltése: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Bejelentkezés Storage Explorer

Első indításkor megjelenik a **Microsoft Azure Storage Explorer – Csatlakozás** ablak. Míg Storage Explorer számos módszert biztosít a Storage-fiókokhoz való kapcsolódáshoz, a hozzáférés-vezérlési listák kezelése jelenleg csak egyetlen módon támogatott.

|Tevékenység|Cél|
|---|---|
|Azure-fiók hozzáadása | Átirányítja a vállalati bejelentkezési oldalra az Azure-hitelesítéshez. Jelenleg ez az egyetlen támogatott hitelesítési módszer, ha az ACL-eket szeretné kezelni és beállítani. |

Válassza az **Azure-fiók hozzáadása** lehetőséget, majd kattintson a **Bejelentkezés** gombra. A képernyőn megjelenő utasításokat követve jelentkezzen be Azure-fiókjába.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/connect.png)

A csatlakozás befejeztével az Azure Storage Explorer betöltést követően megjeleníti az **Explorer** lapot. Ezen a lapon áttekintheti az összes Azure-tárfiókját, valamint az [Azure Storage Emulator](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)- és [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-fiókokon, illetve az [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-környezeteken keresztül konfigurált helyi tárolókat.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Tároló létrehozása

A Blobok mindig egy könyvtárba lesznek feltöltve. Így a blobok csoportjait ugyanúgy rendszerezheti, ahogy a fájlokat a számítógép mappáiban.

A címtár létrehozásához bontsa ki a folytatás lépésben létrehozott Storage-fiókot. Válassza a **blob-tároló**elemet, kattintson a jobb gombbal, majd válassza a blob- **tároló létrehozása**lehetőséget. Adja meg a tároló nevét. Ha elkészült, nyomja le az **ENTER** billentyűt a tároló létrehozásához. A blob könyvtárának sikeres létrehozása után a rendszer a kiválasztott Storage-fiók **blob Container** mappájában jelenik meg.

![Microsoft Azure Storage Explorer-tároló létrehozása](media/storage-quickstart-blobs-storage-explorer/creating-a-filesystem.png)

## <a name="upload-blobs-to-the-directory"></a>Blobok feltöltése a könyvtárba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Az IaaS típusú virtuális gépek biztonsági mentéséhez használt VHD-fájlok lapblobok. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt fájlok a legtöbb esetben blokkblobok.

A címtár menüszalagján válassza a **feltöltés**lehetőséget. Ez a művelet lehetővé teszi egy mappa vagy fájl feltöltését.

Válassza ki a feltölteni kívánt fájlokat vagy mappát. Válassza ki a **blob típusát**. A választható lehetőségek: **Hozzáfűző**, **Lap** és **Blokk**.

.vhd- vagy .vhdx-fájlok feltöltésekor válassza a **.vhd- vagy .vhdx-fájlok feltöltése lapblobként (ajánlott)** lehetőséget.

A **feltöltés mappába (nem kötelező)** mezőben adjon meg egy mappanevet a fájlok vagy mappák tárolására a könyvtárban található mappában. Ha nincs kiválasztva mappa, a fájlok közvetlenül a címtárban lesznek feltöltve.

![Microsoft Azure Storage Explorer – Blob feltöltése](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

Ha az **OK** gombra kattint, a kiválasztott fájlokat a rendszer sorba állítja a feltöltéshez, majd egyesével feltölti azokat. A feltöltés befejeztével az eredmények megjelennek a **Tevékenységek** ablakban.

## <a name="view-blobs-in-a-directory"></a>Blobok megtekintése egy könyvtárban

A **Azure Storage Explorer** alkalmazásban válasszon ki egy könyvtárat egy Storage-fiókban. A fő ablaktábla a kiválasztott könyvtárban lévő Blobok listáját jeleníti meg.

![Microsoft Azure Storage Explorer – Blobok listázása egy könyvtárban](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Blobok letöltése

Blobok az **Azure Storage Explorer** használatával való letöltéséhez jelöljön ki egy blobot, majd válassza a menüszalag **Letöltés** elemét. Megnyílik egy fájl párbeszédpanel, amelyen megadhat egy fájlnevet. Kattintson a **Mentés** lehetőségre a blob letöltéséhez a helyi helyre.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között az **Azure Storage Explorer** használatával. Ha szeretne többet megtudni arról, hogyan állíthatja be az ACL-eket a fájlokra és a címtárakra, folytassa a témával kapcsolatos tudnivalókat.

> [!div class="nextstepaction"]
> [Hozzáférés-vezérlési listák beállítása fájlokhoz és könyvtárakhoz](data-lake-storage-how-to-set-permissions-storage-explorer.md)
