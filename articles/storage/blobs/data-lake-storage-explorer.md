---
title: 'Gyors útmutató: Adatkezelés az Azure Data Lake Storage Gen2 az Azure Storage Explorer használatával '
description: Ez a rövid útmutatóban megismerheti, hogyan hozzon létre egy fájlrendszer egy Azure Data Lake Storage Gen2-fiókot, és a egy könyvtárat és a egy fájlt az Azure Storage Explorer használatával. Ezután megtudhatja, hogyan töltheti le a fájlt a helyi számítógépen, és megtekintése az összes fájl egy könyvtár.
services: storage
author: tamram
ms.component: data-lake-storage-gen2
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/05/2018
ms.author: tamram
ms.openlocfilehash: 5b46d5644536123a9ef3dfc7be87e34d03ee4c4f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423596"
---
# <a name="quickstart-use-azure-storage-explorer-to-manage-data-in-an-azure-data-lake-storage-gen2-account"></a>Gyors útmutató: Az Azure Data Lake Storage Gen2-fiókban lévő adatok kezelése az Azure Storage Explorer használatával

Ebben a rövid útmutató az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) hozhat létre egy könyvtárat és a egy blobot. Ezután megismerheti a blob letöltése a helyi számítógépen, és a egy könyvtár összes BLOB megtekintése. Azt is megtudhatja, hogyan hozzon létre egy pillanatképet egy blobról, könyvtár hozzáférési szabályzatok kezelése, és a egy közös hozzáférésű jogosultságkód létrehozása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

A rövid útmutatóhoz az Azure Storage Explorer telepítése szükséges. Az Azure Storage Explorer Windows, Macintosh vagy Linux rendszerre való letöltése: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="log-in-to-storage-explorer"></a>Bejelentkezés a Storage Explorerbe

Első indításkor megjelenik a **Microsoft Azure Storage Explorer – Csatlakozás** ablak. A Storage Explorer storage-fiókokhoz való csatlakozással több módot is kínál, miközben csak egy módja jelenleg támogatott hozzáférés-vezérlési listák felügyelete.

|Tevékenység|Cél|
|---|---|
|Azure-fiók hozzáadása | Átirányítja a vállalati bejelentkezési oldalra az Azure-hitelesítéshez. Ezt a műveletet jelenleg az egyetlen támogatott hitelesítési módszer, ha szeretné kezelni, és állítsa be a hozzáférés-vezérlési listák. |

Válassza az **Azure-fiók hozzáadása** lehetőséget, majd kattintson a **Bejelentkezés** gombra. A képernyőn megjelenő utasításokat követve jelentkezzen be Azure-fiókjába.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/connect.png)

A csatlakozás befejeztével az Azure Storage Explorer betöltést követően megjeleníti az **Explorer** lapot. Ezen a lapon áttekintheti az összes Azure-tárfiókját, valamint az [Azure Storage Emulator](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)- és [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-fiókokon, illetve az [Azure Stack](../../azure-stack/user/azure-stack-storage-connect-se.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-környezeteken keresztül konfigurált helyi tárolókat.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-filesystem"></a>Hozzon létre egy fájlrendszer

Blobok minden esetben a rendszer feltölti egy könyvtárat. Így a blobok csoportjait ugyanúgy rendszerezheti, ahogy a fájlokat a számítógép mappáiban.

Hozzon létre egy könyvtárat, bontsa ki az előző lépésben létrehozott tárfiókot. Válassza ki **blobtároló**, kattintson a jobb gombbal, és válassza ki **létrehozása blobtároló**. Adja meg a fájlrendszer nevét. Amikor végzett, nyomja le az ENTER **Enter** hozhat létre a fájlrendszer. Miután a blob könyvtár sikeresen létrejött, megjelenik a **Blob-tároló** a kiválasztott tárfiókhoz tartozó mappában.

![A Microsoft Azure Storage Explorer – egy fájlrendszer létrehozása](media/storage-quickstart-blobs-storage-explorer/creating-a-filesystem.png)

## <a name="upload-blobs-to-the-directory"></a>Blobok feltöltése a könyvtárba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Az IaaS típusú virtuális gépek biztonsági mentéséhez használt VHD-fájlok lapblobok. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt fájlok a legtöbb esetben blokkblobok.

Válassza ki a címtár menüszalag **feltöltése**. Ez a művelet lehetővé teszi egy mappa vagy fájl feltöltését.

Válassza ki a feltölteni kívánt fájlokat vagy mappát. Válassza ki a **blob típusát**. A választható lehetőségek: **Hozzáfűző**, **Lap** és **Blokk**.

.vhd- vagy .vhdx-fájlok feltöltésekor válassza a **.vhd- vagy .vhdx-fájlok feltöltése lapblobként (ajánlott)** lehetőséget.

Az a **feltöltés mappába (nem kötelező)** mezőben vagy a mappa nevét tárolja a fájlokat vagy mappákat tartalmazza a könyvtárban. Ha nem választ mappát, a fájlok feltöltése közvetlenül a könyvtárban.

![Microsoft Azure Storage Explorer – Blob feltöltése](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

Ha az **OK** gombra kattint, a kiválasztott fájlokat a rendszer sorba állítja a feltöltéshez, majd egyesével feltölti azokat. A feltöltés befejeztével az eredmények megjelennek a **Tevékenységek** ablakban.

## <a name="view-blobs-in-a-directory"></a>Blobok megtekintése egy könyvtár

Az a **Azure Storage Explorer** válasszon ki egy tárfiókhoz tartozó könyvtárat. A fő panelen a kiválasztott könyvtárban a blobok listája látható.

![A Microsoft Azure Storage Explorer – egy könyvtárban lévő blobok listázása](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Blobok letöltése

Blobok az **Azure Storage Explorer** használatával való letöltéséhez jelöljön ki egy blobot, majd válassza a menüszalag **Letöltés** elemét. Megnyílik egy fájl párbeszédpanel, amelyen megadhat egy fájlnevet. Kattintson a **Mentés** lehetőségre a blob letöltéséhez a helyi helyre.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között az **Azure Storage Explorer** használatával. Hozzáférés-vezérlési listák beállítása a fájlok és könyvtárak kapcsolatos további információkért folytassa az útmutató a témában.

> [!div class="nextstepaction"]
> [Hozzáférés-vezérlési listák beállítása a fájlok és könyvtárak](data-lake-storage-how-to-set-permissions-storage-explorer.md)
