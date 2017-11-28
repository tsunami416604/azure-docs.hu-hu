---
title: "Keresse meg és tárolási erőforrások kezelése a Server Explorer használatával |} Microsoft Docs"
description: "Keresse meg és tárolási erőforrások kezelése a Server Explorer használatával"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/24/2017
ms.author: kraigb
ms.openlocfilehash: 9f3d564fd844d982838cc6f29af24399801a62bc
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>Keresse meg és tárolási erőforrások kezelése a Server Explorer használatával

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Áttekintés

Ha telepítette az Azure-eszközök a Microsoft Visual Studio, megtekintheti blob, queue és table adatok a storage-fiókok a az Azure-bA. Az Azure **tárolási** a Server Explorer csomópontban jelennek meg az adatokat, amelyek a helyi storage emulator fiókja és az egyéb Azure storage-fiókok.

Server Explorer a Visual Studio, a menüsávban válassza ki, ha **nézet** > **Server Explorer**. A **tárolási** csomópontban jelennek meg minden egyes Azure-előfizetés vagy a tanúsítvány, amely kapcsolódik a meglévő tárfiókok. A tárfiók nem jelenik meg, ha egyszerűen hozzáadhatja az utasításokat követve [Ez a cikk későbbi](#add-storage-accounts-by-using-server-explorer).

Az Azure SDK 2.7-től kezdődően is használhatja Cloud Explorer megtekintése és kezelése az Azure-erőforrások. További információkért lásd: [erőforrások kezelése Azure Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md).

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Megtekintheti, és a Visual Studio tárolási erőforrások kezelése

Server Explorer automatikusan emulátor a tárfiókban lévő blobokat, üzenetsorokat és táblákat listája látható. A tárfiók emulátor, a Server Explorer szerepel a **tárolási** csomópontban a **fejlesztési** csomópont.

A storage emulator fiók erőforrások megtekintéséhez bontsa ki a **fejlesztési** csomópont. Ha a storage emulator még nem indult el kibővítésekor a **fejlesztési** csomópont, automatikusan elindul. A folyamat eltarthat néhány másodpercig. Továbbra is működik egyéb területein a Visual Studio, amíg a storage emulator elindul.

A tárfiókban lévő erőforrások megtekintéséhez bontsa ki a tárfiók csomópont a Server Explorer megtapasztalhatja **Blobok**, **várólisták**, és **táblák** csomópontok.

## <a name="work-with-blob-resources"></a>A blob erőforrásokat

A **Blobok** csomópont a kiválasztott tárolási fiók tárolók listáját jeleníti meg. BLOB tárolók blob fájlokat tartalmazza, és ezek a blobok rendezze mappákban és almappáiban. További információkért lásd: [használata a .NET-Blob-tároló](storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="to-create-a-blob-container"></a>A blob-tároló létrehozása

1. Nyissa meg a helyi menüje a **Blobok** csomópont, és válassza **Blob-tároló létrehozása**.
1. Az a **Blob-tároló létrehozása** párbeszédpanelen adja meg az új tároló nevét.  
1. Jelölje be Enter billentyűt, vagy kattintson vagy koppintson kívül blobtárolóba menti a név mező.

   > [!NOTE]
   > A blob-tároló neve egy szám (0-9) vagy kisbetűt (a – z) kell kezdődnie.

### <a name="to-delete-a-blob-container"></a>A blob-tároló törlése

Nyissa meg a blob-tároló, amelyet szeretne eltávolítani, és válassza a helyi menü **törlése**.

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>A blob-tárolóban lévő elemek listájának megjelenítéséhez

Nyissa meg a blob-tároló neve helyi menüje a listában, majd válassza ki **nyitott**.

A blob-tároló tartalmának megtekintésekor azt lapon jelenik meg a blob-tároló nézet néven ismert.

![A BLOB-tároló nézet](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

A blob-tároló nézet jobb felső sarkában a gombok segítségével blobok a következő műveleteket hajthatja végre:

* Adjon meg egy szűrő értéket, és alkalmazza azt.
* A tárolóban lévő blobok listájának frissítése.
* Fájl feltöltése.
* Blobok törléséhez. (A fájl törlése a blob-tároló nem törli a fájl. Csak eltávolítja azt a blob-tároló.)
* Nyissa meg a blob.
* Mentse a blob a helyi számítógépre.

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>A blob-tároló mappát vagy almappát létrehozásához

1. Adja meg a blob-tároló Cloud Explorer. A tároló ablakban válassza ki a **Blob feltöltése** gombra.

1. Az a **új fájl feltöltése** párbeszédpanelen jelölje ki a **Tallózás** gombra kattintva adja meg a feltölteni kívánt fájlt, és írja be a mappa nevét a a **mappa (nem kötelező)** mezőbe.

   ![A fájl feltöltése a blob mappába](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

   Almappák tároló mappák az azonos lépést követve adhat hozzá. Ha nem adja meg a mappa nevét, a rendszer a fájl feltöltése, a legfelső szintre a blob-tároló. A fájl jelenik meg a megadott mappában a tárolóban.

   ![Egy blob-tárolóba felvett mappában találhatók](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Kattintson duplán a mappára, vagy jelölje be a mappa tartalmát meg az Enter billentyűt. Ha a tároló mappában, lépjen vissza a tároló a legfelső szintű kiválasztásával a **nyitott Szülőkönyvtárában** (nyíl) gombra.

### <a name="to-delete-a-container-folder"></a>Tároló mappa törlése

A mappában található összes fájl törlésére.

Mivel a blob tárolók mappák virtuális mappák, az üres mappa nem hozható létre. Még nem törölhető egy mappa, fájl tartalmának törlése, de ehelyett törölnie kell a magában a mappában törlendő mappa teljes tartalmát.

### <a name="to-filter-blobs-in-a-container"></a>A tárolóban lévő blobok szűrése

Szűrheti a blobot, amely megjeleníti azokat a közös előtag megadása.

Például, ha beírja az előtag **hello** a szűrő szövegmezőbe, és válassza ki azt a **Execute** (**!**) gomb, csak a "hello" karakterrel kezdődő blobok jelenik meg.

![Szűrő szövegmező](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

A szűrő szövegmezőbe kis-és nagybetűket, és nem támogatja a helyettesítő karaktereket is tartalmazó szűrést. Blobok csak előtag alapján szűrhetők. Az előtag tartalmazhatnak elválasztó elválasztó blobot, amely egy virtuális hierarchia rendszerezéséhez használatakor. Az előtag alapján például szűrése "HelloFabric /" adja vissza az adott karaktersorozattal kezdődő összes BLOB.

### <a name="to-download-blob-data"></a>A blob adatok letöltése

A Cloud Explorer használja az alábbi módszerek bármelyikét:

* Nyissa meg a helyi menü az egy vagy több blobot, és válassza **nyitott**.
* Válassza ki a blob nevét, és válassza ki a **nyitott** gombra.
* Kattintson duplán a blob neve.

Megjelenik egy blob letöltés előrehaladását a **Azure tevékenységnapló** ablak.

A blob az adott fájltípus alapértelmezett-szerkesztő megnyitása. Ha az operációs rendszer felismeri a fájl típusát, a fájl a helyileg telepített alkalmazások nyílik meg. Ellenkező esetben felkéri a blob típusú fájl megfelelő alkalmazás kiválasztása. A blob letöltésekor létrehozott helyi fájl csak olvashatóként van megjelölve.

BLOB adatok helyben és veti össze az Azure Blob storage a blob utolsó módosításának időpontja. Ha a blob óta utolsó letöltési frissítették, azt újra le. Ellenkező esetben a blob a helyi lemez be van töltve.

Alapértelmezés szerint a blob letölti egy ideiglenes könyvtárhoz. Egy adott könyvtár blobok letöltéséhez nyissa meg a helyi menüben a kijelölt blob neveket, majd válassza **Mentés másként**. Blob mentésekor ezen a módon kikapcsolja, a blob-fájl nincs megnyitva, és a helyi fájl olvasási/írási attribútumokkal jön létre.

### <a name="to-upload-blobs"></a>Blobok feltöltése

Blobok feltöltése, válassza ki a **Blob feltöltése** gombra kattint, ha a tároló meg nyitva a blob-tároló nézetben megtekinthető.

Választhat egy vagy több fájlt feltölteni, és feltöltheti az összes fájltípus. A **Azure tevékenységnapló** ablakban követhető a feltöltést. Blob adatokkal kapcsolatos további információkért lásd: [Azure Blob storage használata a .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>A blobok továbbított naplók megtekintése

Ha Azure Diagnostics használatával adatokat az Azure alkalmazásból jelentkezik, és amelyre átmásolta naplók tárfiókja, látni fogja, ezek a naplók létrehozott Azure tárolók. Egyszerűen azonosíthatja a problémákat az alkalmazáshoz, ezek a naplók megtekintéséhez a Server Explorer, különösen akkor, ha üzembe helyezésüket követően az Azure-bA.

Azure Diagnostics kapcsolatos további információkért lásd: [naplózási adatok gyűjtése Azure Diagnostics használatával](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Az URL-cím lekérése blob

A blob helyi menü megnyitásához, és válassza ki **URL-CÍMÉT**.

### <a name="to-edit-a-blob"></a>Egy blob szerkesztése

Válassza ki a blob, és válassza ki a **nyissa meg a Blob** gombra.

A fájl egy ideiglenes helyre letöltése és megnyitni a helyi számítógépen. Töltse fel újra a blob módosítása után.

## <a name="work-with-queue-resources"></a>A várólista erőforrásokat

Egy Azure storage-fiókban tárolt szolgáltatások tárüzenetsort. Engedélyezi a felhőalapú szerepköreit kommunikálnak egymással, és más szolgáltatásokkal egy üzenet-sikeres mechanizmus használhatja őket. A várólista hozzáférhet programozott módon keresztül egy felhőalapú szolgáltatás, és a külső ügyfelek webszolgáltatáson keresztül. A várólista közvetlenül a Visual Studio Server Explorer használatával is elérhető.

Amikor egy felhőalapú szolgáltatás által használt várólisták fejlesztése, előfordulhat, hogy használni kívánt Visual Studio várólisták létrehozásához, és azokat interaktív használatához közben az Ön által fejlesztett és tesztelheti a kódját.

A Server Explorer eszközben, megtekintheti a várólisták tárfiókokban, hozzon létre és Várólisták törlése, nyissa meg a várólisták megtekintheti az üzenetet, és adja hozzá az üzenetek várólistára. Megtekintésre várólista megnyitásakor megtekintheti az egyes üzeneteket, és a következő műveletek végezhetők várakozási bal felső sarokban a gombok használatával:

* Frissítse a nézetet a várólista.
* Vegyen fel egy üzenetet az üzenetsorba.
* A legfelső üzenet feldolgozásához.
* A teljes várólista törlése.

Az alábbi ábrán egy két üzeneteket tartalmazó sor:

![A várólista megtekintéséhez](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

További információ a tárolási szolgáltatási várólisták létrehozásáról: [Ismerkedés az Azure Queue storage használatának .NET](http://go.microsoft.com/fwlink/?LinkID=264702). A webszolgáltatás tárolási szolgáltatási várólisták, lásd: [a Queue szolgáltatás alapfogalmai](http://go.microsoft.com/fwlink/?LinkId=264788). Üzenetek küldése egy tárolási szolgáltatások üzenetsorba Visual Studio használatával kapcsolatos információkért lásd: [tárolási szolgáltatások várólista-üzenetek küldése](https://msdn.microsoft.com/library/azure/jj649344.aspx).

> [!NOTE]
> Tárolási szolgáltatások sorok különbözőek az Azure Service Bus-üzenetsorok. További információ a Service Bus-üzenetsorok: [Service Bus-üzenetsorok, témakörök és előfizetések](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-queues-topics-subscriptions).

## <a name="work-with-table-resources"></a>A tábla erőforrásokat

Az Azure Table Storage nagy mennyiségű strukturált adat tárolására alkalmas. A szolgáltatás egy NoSQL-adattár, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

### <a name="to-create-a-table"></a>Tábla létrehozása

1. A Cloud Explorerben válassza ki a **táblák** a tárfiók, és válassza ki azt a csomópont **Create Table**.
1. Az a **Create Table** párbeszédpanelen adja meg a tábla nevét.

### <a name="to-view-table-data"></a>Tábla adatainak megtekintéséhez

1. A Cloud Explorerben nyissa meg a **Azure** csomópont, és nyissa meg a **tárolási** csomópont.
1. Nyissa meg a tárolási fiók csomópont érdekelt, és nyissa meg a **táblák** csomópont a tárfiók táblázatok listájának megjelenítéséhez.
1. Nyissa meg a helyi menü táblázat, és válassza **nézet tábla**.

    ![Egy Azure-tábla a Megoldáskezelőben](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

A tábla (látható sorok) entitásokat és a Tulajdonságok (látható oszlopok) szerint van rendezve. Például a következő ábrán entitások tábla Designer szerepel.

### <a name="to-edit-table-data"></a>A tábla adatainak szerkesztése

Tábla Designer, nyissa meg a helyi menüben a tulajdonsághoz (egy cellát) vagy egy entitás (egy sorban), majd válassza ki **szerkesztése**.

    ![Add or edit a table entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

Egyetlen tábla az entitások nem ugyanazokat a Tulajdonságok (oszlop) rendelkezniük. Vegye figyelembe a tábla adatok megtekintése és módosítása a következő korlátozások vonatkoznak:

* Nem tekinthetők meg és a bináris adatok szerkesztéséhez (`type byte[]`), de egy táblázatban tárolja.
* Nem lehet szerkeszteni a **PartitionKey** vagy **RowKey** érték található, mert az Azure Table storage nem támogatja a műveletet.
* Nem hozható létre a tulajdonságot, **időbélyeg**. Azure storage szolgáltatások tulajdonsággal egy ezen a néven.
* Ha megad egy **DateTime** érték, hajtsa végre az formátuma nem megfelelő. a számítógép területi és nyelvi beállítások a (például hh/nn/éééé óó: pp: [AM |} PM] amerikai angol nyelvű).

### <a name="to-add-entities"></a>Entitás hozzáadása

1. A tábla Designer, válassza ki a **entitás hozzáadása** gombra.

    ![Hozzáadás gomb entitás](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. Az a **entitás hozzáadása** párbeszédpanelen adja meg az értékeket a **PartitionKey** és **RowKey** tulajdonságok.

    ![Entitás párbeszédpanel hozzáadása](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Gondosan adja meg az értékeket. Ha törölni egy entitást, és vegye fel újra a párbeszédpanel bezárása után nem módosíthatja őket.

### <a name="to-filter-entities"></a>Entitások szűrése

Testre szabhatja az entitásokat, amely egy táblázat jelenik meg, ha a lekérdezés-szerkesztő használata.

1. A lekérdezés-szerkesztő megnyitásához megtekintésre tábla.
1. Válassza ki a **Lekérdezésszerkesztő** a tábla nézet eszköztár.

    A **Lekérdezésszerkesztő** párbeszédpanel jelenik meg. A következő ábrán egy lekérdezést, amely éppen készül a Lekérdezésszerkesztőben.

    ![Lekérdezés-szerkesztő](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
1. Ha elkészült a lekérdezés létrehozása a párbeszédpanel bezárásához. A lekérdezés megjelenő szöveg képernyőn a WCF Data Services szűrő beviteli mezőben jelenik meg.
1. A lekérdezés futtatásához jelölje ki a zöld háromszögnek ikonra.

Entitás megjelenő adatok tábla Designer beírása esetén egy a WCF Data Services szűrési karakterláncot a szűrő szövegmezőbe közvetlenül is végezhet. Az ilyen típusú karakterlánc egy SQL WHERE záradék hasonló, de a kiszolgálón, a HTTP-kérelem érkezik. Szűrőkarakterláncokban összeállításához kapcsolatos információkért lásd: [Szűrőkarakterláncokban hozhat létre a tábla Designer](https://msdn.microsoft.com/library/azure/ff683669.aspx).

Az alábbi ábrán egy példa egy érvényes szűrési karakterláncot:

![Szűrési karakterláncot](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>Tárolási adatok frissítése

Server Explorer csatlakozik, vagy az adatok beolvasása a tárfiókból, a művelet lehet, hogy eltarthat egy percet befejezéséhez. Ha a Server Explorer nem tud csatlakozni, a művelet lehet, hogy túllépi az időkorlátot. Adatok lekérésére, miközben továbbra is működik, a Visual Studio többi részével. Ha túl sokáig tart a művelet megszakításához válassza a **frissítés leállítása** a Server Explorer gombjára.

### <a name="to-refresh-blob-container-data"></a>A blob-tároló adatok frissítése

* Válassza ki a **Blobok** csomóponton sem **tárolási**, majd válassza ki a **frissítése** a Server Explorer gombjára.
* -Listájának frissítése blobot, amely akkor jelenik meg, válassza ki a **Execute** gombra.

### <a name="to-refresh-table-data"></a>A tábla adatainak frissítése

* Válassza ki a **táblák** csomóponton sem **tárolási**, majd válassza ki a **frissítése** a Server Explorer gombjára.
* Tábla tervezőben megjelenő entitások listájának frissítése, válassza ki a **Execute** tábla Designer gombra.

### <a name="to-refresh-queue-data"></a>A várólista adatok frissítése

Válassza ki a **várólisták** csomóponton sem **tárolási**, majd válassza ki a **frissítése** a Server Explorer gombjára.

### <a name="to-refresh-all-items-in-a-storage-account"></a>A tárfiók szereplő összes frissítése

Válassza ki a fiók nevét, majd válassza ki a **frissítése** a Server Explorer gombjára.

## <a name="add-storage-accounts-by-using-server-explorer"></a>A Server Explorer storage-fiókok hozzáadása

Tárfiók hozzáadása a Server Explorer használatával két módja van. Az Azure-előfizetéshez a tárfiókot is létrehozhat, vagy csatolhat a meglévő tárfiókot.

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>A storage-fiók létrehozása a Server Explorer használatával

1. A Server Explorer eszközben a helyi menü megnyitása a **tárolási** csomópont, és válassza **Storage-fiók létrehozása**.

1. Az a **Storage-fiók létrehozása** párbeszédpanelen jelölje be, vagy adja meg a következő adatokat:

   * Az Azure-előfizetés kívánt tárfiók hozzáadása.
   * Az új tárfiókot használni kívánt nevét.
   * A régiót vagy affinitáscsoportot (például az USA nyugati régiója vagy Kelet-Ázsia).
   * A tárfiók, például a használni kívánt replikációs típusú helyileg redundáns.

   ![Azure-tárfiók létrehozása](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Kattintson a **Létrehozás** gombra.

Az új tárfiók megjelenik a **tárolási** lista a Megoldáskezelőben.

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Meglévő tárfiók csatolása a Server Explorer használatával

1. A Server Explorer eszközben nyissa meg a helyi menü az Azure-beli **tárolási** csomópont, és válassza **külső tárterület csatolása**.

    ![Meglévő tárfiók hozzáadása](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
1. Az a **Storage-fiók létrehozása** párbeszédpanelen jelölje be, vagy adja meg a következő adatokat:

   * A csatlakoztatni kívánt meglévő tárfiók neve.
   * A kiválasztott tárfiók kulcsa. Ez az érték általában biztosított, válasszon egy tárfiókot. Ha azt szeretné, hogy a Visual Studio jegyezze meg a tárfiók hívóbetűjét, válassza a **tárolása fiókkulcs** jelölőnégyzetet.
   * A tárfiók, például HTTP, HTTPS vagy egy egyéni végpont való csatlakozáshoz használni kívánt protokollt. Egyéni végpontokat kapcsolatos további információkért lásd: [kapcsolati karakterláncok konfigurálása hogyan](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### <a name="to-view-the-secondary-endpoints"></a>A másodlagos végpontok megtekintése

Ha létrehozott egy tárfiókot a **írásvédett Georedundáns** replikációs beállítás, megtekintheti a másodlagos végpont nyissa meg a helyi menüben a fiók neve, és válassza ki **tulajdonságok**.

![Tárolási másodlagos végpontok](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>A tárfiók eltávolítása a Server Explorer

A Server Explorer, a megjelenő helyi menüben a fiók neve, majd válassza ki **törlése**. 

Ha törli a tárfiókot, minden mentett kulcsadatokat fiók is törlődnek.

Ha a tárfiók törlése a Server Explorer, a tárfiók vagy a benne található adatokat nem befolyásolja. Egyszerűen eltávolítja a hivatkozás a Server Explorer. Véglegesen törli a tárfiókot, használja a [Azure-portálon](https://portal.azure.com/).

## <a name="next-steps"></a>Következő lépések

Az Azure storage szolgáltatások használatával kapcsolatos további információkért lásd: [elérése az Azure Storage szolgáltatásainak](https://msdn.microsoft.com/library/azure/ee405490.aspx).
