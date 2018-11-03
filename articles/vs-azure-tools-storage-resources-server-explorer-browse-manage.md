---
title: Keresse meg és tárolási erőforrások kezelése a Server Explorer használatával |} A Microsoft Docs
description: Böngészés és tárolási erőforrások kezelése a Server Explorer használatával
services: visual-studio-online
author: ghogen
manager: douge
assetId: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/24/2017
ms.author: ghogen
ms.openlocfilehash: ece9865ad442edc27fd0f495940fd0945d0529ab
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969264"
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>Keresse meg és tárolási erőforrások kezelése a Server Explorer használatával

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Áttekintés

Ha telepítette az Azure-eszközök a Microsoft Visual Studióhoz készült, megtekintheti a blob, queue és táblaadatokat szolgáltatókból az Azure-hoz. Az Azure **tárolási** csomópont a Server Explorerben, amely a helyi emulátor tárfiók és az egyéb Azure storage-fiókok adatait jeleníti meg.

Server Explorer megtekintéséhez a Visual Studióban a menüsávon válassza **nézet** > **Server Explorer**. A **tárolási** csomópont megjeleníti az összes létező minden egyes Azure-előfizetés vagy a tanúsítványt, amely csatlakozik a storage-fiókok. Ha a tárfiók nem jelenik meg, ezt hozzáadhatja utasításokat követve [a cikk későbbi részében](#add-storage-accounts-by-using-server-explorer).

Azure SDK 2.7-es kezdődően is használhatja Cloud Explorer megtekintése és kezelése az Azure-erőforrások. További információkért lásd: [kezelése az Azure-erőforrások a Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md).

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>A Visual Studióban a storage erőforrás megtekintése és kezelése

Server Explorer automatikusan emulátor a tárfiókban lévő blobokhoz, üzenetsorokhoz és táblákhoz listája látható. A storage emulator fiók szerepel a Server Explorer alatt a **tárolási** csomópont állapota a **fejlesztési** csomópont.

A storage emulator fiók erőforrások megtekintéséhez bontsa ki a **fejlesztési** csomópont. Ha a storage emulator még nem lett elindítva, bontsa ki a **fejlesztési** csomópont, akkor automatikusan elindul. A folyamat eltarthat néhány másodpercig. Továbbra is a storage emulator elindítja a Visual Studio más területeken működik.

A storage-fiókban erőforrások megtekintéséhez bontsa ki a Server Explorerben, ahol a tárfiók csomópont **Blobok**, **üzenetsorok**, és **táblák** csomópontok.

## <a name="work-with-blob-resources"></a>A blob-erőforrásokkal való munka

A **Blobok** csomópont a kiválasztott tárfiókhoz tartozó tárolók listáját jeleníti meg. BLOB-tárolók blob fájlokat tartalmazza, és ezek a blobok szervezheti mappákban és almappáiban. További információkért lásd: [használata a Blob storage .NET-](storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="to-create-a-blob-container"></a>A blob-tároló létrehozása

1. Místní nabídka nyissa meg a **Blobok** csomópontot, és válassza ki **Blobtároló létrehozása**.
1. Az a **Blobtároló létrehozása** párbeszédpanelen adja meg az új tároló nevét.  
1. Válassza billentyűt a billentyűzeten, vagy kattintson vagy koppintson a név mezőnél a blob-tároló mentéséhez kívülre.

   > [!NOTE]
   > A blobtároló neve szám (0 – 9) vagy kisbetű (a – z) kell kezdődnie.

### <a name="to-delete-a-blob-container"></a>A blob-tároló törlése

Nyissa meg a blob tároló, amelyet szeretne eltávolítani, és válassza a helyi menü **törlése**.

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>Az elemek listájának megjelenítéséhez a blobtárolóban

Nyissa meg a helyi menü egy blobtároló neve a listában, és válassza **nyílt**.

Amikor egy blob-tároló tartalmát, azt lapon jelenik meg a blob-tároló nézete néven.

![A BLOB-tároló nézete](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

A blob-tároló nézet jobb felső sarkában a gombok segítségével a blobok a következő műveleteket hajthatja végre:

* Adja meg a szűrő értékét, és alkalmazza azt.
* A tárolóban lévő blobok listájának frissítése.
* Töltsön fel egy fájlt.
* Blob törlése. (A blob-tárolóból egy fájl törlése nem törli az alapul szolgáló fájlt. Csak eltávolítja azt a blobtárolóban.)
* Nyissa meg a blobot.
* Mentse egy blobot a helyi számítógépen.

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Hozhat létre egy mappát vagy almappát a blobtárolóban

1. A Cloud Explorerben válassza ki a blob-tároló. A tároló ablakában válassza ki a **Blob feltöltése** gombra.

1. Az a **új fájl feltöltése** párbeszédpanelen válassza ki a **Tallózás** gombra kattintva adja meg a feltölteni kívánt fájlt, és írja be a mappa nevét, a a **mappába (nem kötelező)** mezőbe.

   ![A fájl feltöltése a blob-mappába](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

   Almappák tároló mappák az azonos lépést követve adhat hozzá. Ha nem adja meg a mappa nevét, a fájlt töltenek fel a legfelső szintű blob-tárolóban. A fájl jelenik meg a megadott mappát a tárolóban.

   ![Blob-tárolóba felvett mappában](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Kattintson duplán arra a mappára, vagy válassza le az Enter billentyűt a mappa tartalmának megtekintéséhez. Ha Ön a tároló mappa, később is visszatérhet a tároló gyökérkönyvtárába kiválasztásával a **nyílt szülőkönyvtárhoz** (nyíl) gombra.

### <a name="to-delete-a-container-folder"></a>Tároló mappa törlése

Törölje a mappában lévő összes fájlt.

Mivel a blob-tárolók mappák virtuális mappák, az üres mappa nem hozható létre. Is törli a fájl tartalmát egy olyan mappa nem törölhető, de ehelyett törölnie kell törli magát a mappát egy olyan mappa teljes tartalmát.

### <a name="to-filter-blobs-in-a-container"></a>A tárolóban lévő blobokat szűréséhez

Szűrheti a blobok, amelyek egy közös előtag megadásával jelenik meg.

Például ha ad meg, hogy az előtag **hello** a szűrő szövegmezőbe, és válassza ki a **Execute** (**!**) gomb, csak a "hello" karakterrel kezdődő blobok jelenik meg.

![Szűrési szövegmező](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

A szűrő szövegmezőbe kis-és nagybetűket, és nem támogatja a helyettesítő karaktereket is tartalmazó szűrés. Blobok csak előtag alapján szűrhetők. Az előtag lehet egy elválasztó használatával blobok virtuális hierarchiába rendezése egy elválasztó karaktert. Például szűrése előtag "HelloFabric /" adott karaktersorozattal kezdődő összes blobot adja vissza.

### <a name="to-download-blob-data"></a>Blob adatainak letöltése

A Cloud Explorer az alábbi módszerek bármelyikét használhatja:

* Egy vagy több blobot a helyi menüjének megnyitásához, és válassza ki **nyílt**.
* Válassza ki a blob nevét, és válassza ki a **nyílt** gombra.
* Kattintson duplán a blob nevét.

Megjelenik egy blob letöltés előrehaladását a **Azure-tevékenységnapló** ablak.

A blob az alapértelmezett szerkesztő az adott fájltípus nyílik meg. Ha az operációs rendszer felismeri a fájl típusa, megnyílik a fájl a helyileg telepített alkalmazások. Ellenkező esetben kéri, hogy válassza ki a blob típusú fájl megfelelő alkalmazás. A helyi fájl létrehozásakor blob letöltése csak olvashatóként van megjelölve.

Blobadatok a helyi gyorsítótárba, és be van jelölve, az Azure Blob storage a blob legutóbbi módosításuk ideje alapján. Ha a blob frissült, mivel a legutóbb letöltött, letöltött újra. Ellenkező esetben a blobot a helyi lemez be van töltve.

Alapértelmezés szerint egy blob egy ideiglenes könyvtárba, töltődik le. Egy adott címtárhoz blobok letöltéséhez nyissa meg a kiválasztott blobok neveit menüjét, majd válassza **Mentés másként**. Amikor egy blob mentése ezen a módon kikapcsolja, a blob-fájl nincs megnyitva, és a helyi fájl jön létre az olvasási/írási attribútumok.

### <a name="to-upload-blobs"></a>Blobok feltöltése

Blobok feltöltése, válassza ki a **Blob feltöltése** gombot, ha a tároló meg nyitva, és a blob-tároló nézetben megtekinthető.

Választhat egy vagy több fájlt feltölteni, és bármilyen típusú fájlokat is feltölthet. A **Azure-tevékenységnapló** ablak mutatja a feltöltés állapotának. Blob adatokkal kapcsolatos további információkért lásd: [Azure Blob storage használata .NET-keretrendszerben](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>A blobokon át a naplók megtekintéséhez

Ha adatok naplózása az Azure-alkalmazásokat az Azure Diagnostics használ, és a naplók átmásolta a tárfiókhoz, látni fogja a tárolók, Azure létrehozott ezekhez a naplókhoz. Ezek a naplók megtekintéséhez a Kiszolgálókezelőben szolgáltatás könnyen azonosíthatja a problémákat az alkalmazását, különösen akkor, ha az üzembe helyezésüket az Azure-bA.

Azure Diagnostics kapcsolatos további információkért lásd: [naplózási adatok gyűjtése az Azure Diagnostics használatával](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Egy BLOB az URL-Címének lekéréséhez

A blob helyi menü megnyitásához, majd **példány URL-cím**.

### <a name="to-edit-a-blob"></a>Blob szerkesztése

Válassza ki a blobot, és válassza ki a **nyissa meg a Blob** gombra.

A fájl egy ideiglenes helyre letöltése és megnyitása a helyi számítógépen. Töltse fel újra a blob módosítása után.

## <a name="work-with-queue-resources"></a>Üzenetsor-erőforrásokkal való munka

Tárolási szolgáltatások üzenetsorok üzemel az Azure storage-fiók. Hogy a cloud service szerepkörök üzenettovábbító mechanizmus kommunikáljanak egymással és az egyéb szolgáltatásokkal használhatja őket. A várólista hozzáférhetnek programozott módon egy felhőalapú szolgáltatás segítségével, és a külső ügyfelek webszolgáltatáson keresztül. A várólista közvetlenül a Visual Studio Server Explorer használatával is elérheti.

Egy felhőszolgáltatás, amely üzenetsorokat használ fejleszt, akkor érdemes üzenetsorok létrehozása, és dolgozhat velük, interaktív módon fejlesztésekor és tesztelheti a kódját a Visual Studio használatával.

A Server Explorerben, megtekintheti az üzenetsorok a storage-fiókban, hozzon létre és üzenetsorok törlése, nyissa meg az üzenetek megtekintése egy üzenetsorba, és üzeneteket ad hozzá egy üzenetsorba. Megtekintésre várólista megnyitásakor megtekintheti az egyes üzeneteket, és elvégzése a következő műveleteket az üzenetsorban a bal felső sarokban a gombok használatával:

* Frissítse a nézetet a várólista.
* Vegyen fel egy üzenetet az üzenetsorba.
* A legfelső üzenet eltávolítása a sorból.
* Törölje a teljes üzenetsort.

Az alábbi képen egy üzenetsorba, amely két üzenet tartalmazza:

![Egy üzenetsor megtekintése](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

További információ a tárolási üzenetsorok szolgáltatási létrehozásáról: [.NET használatával az Azure Queue storage használatának első lépései](http://go.microsoft.com/fwlink/?LinkID=264702). További információ a web service tárolási üzenetsorok szolgáltatási,: [Queue szolgáltatás alapfogalmai](http://go.microsoft.com/fwlink/?LinkId=264788). Hogyan küldhet üzeneteket az üzenetsor-tárolóba szolgáltatások a Visual Studio használatával kapcsolatos információkért lásd: [üzeneteket küldő üzenetsor-tárolóba szolgáltatások](https://docs.microsoft.com/azure/visual-studio/vs-storage-cloud-services-getting-started-queues).

> [!NOTE]
> Tároló-üzenetsorok szolgáltatások nem azonosak az Azure Service Bus-üzenetsorok. További információ a Service Bus-üzenetsorok: [Service Bus-üzenetsorok, témakörök és előfizetések](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-queues-topics-subscriptions).

## <a name="work-with-table-resources"></a>A tábla erőforrásokat

Az Azure Table Storage nagy mennyiségű strukturált adat tárolására alkalmas. A szolgáltatás egy NoSQL-adattár, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure-felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

### <a name="to-create-a-table"></a>Tábla létrehozása

1. A Cloud Explorerben válassza ki a **táblák** a storage-fiókot, és válassza ki a csomópont **Create Table**.
1. Az a **Create Table** párbeszédpanelen adja meg a tábla nevét.

### <a name="to-view-table-data"></a>Táblák adatainak megtekintése

1. A Cloud Explorerben nyissa meg a **Azure** csomópontot, és nyissa meg a **tárolási** csomópont.
1. Nyissa meg a storage-fiók csomópontjára, amely van érdekelné, és nyissa meg a **táblák** a csomópontot a tárfiók táblák listájának megtekintéséhez.
1. Egy tábla a helyi menü megnyitásához, és válassza ki **Zobrazit Tabulku**.

    ![Az Azure-táblát a Megoldáskezelőben](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

A tábla (sorok látható) entitásokat és tulajdonságait (oszlopban látható) szerint van rendezve. Például a következő ábrán látható entitások Táblatervező szerepel.

### <a name="to-edit-table-data"></a>Táblák adatainak szerkesztése

A tábla-tervezőben nyissa meg a helyi menü egy entitás (egyetlen sor) vagy egy tulajdonság (egy cella), és válassza ki **szerkesztése**.

    ![Add or edit a table entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

Egyetlen tábla entitások nem szükséges a azonos tulajdonságkészlettel (oszlop). Tartsa szem előtt a tábla adatok megtekintése és módosítása a következő korlátozásokkal:

* Tudja megtekinteni vagy szerkeszteni a bináris adatok (`type byte[]`), de a táblában tárolni.
* Nem lehet szerkeszteni a **PartitionKey** vagy **RowKey** értékeket, mert az Azure Table storage nem támogatja a műveletet.
* Nem hozható létre tulajdonsággal **időbélyeg**. Az Azure storage szolgáltatások ilyen nevű tulajdonságot használja.
* Ha megad egy **DateTime** érték, Ön köteles betartani a számítógép területi és nyelvi beállítások megfelelő formátumban (például hh/nn/éééé óó [AM |} PM] amerikai angol nyelven).

### <a name="to-add-entities"></a>Entitások hozzáadása

1. A tábla-tervezőben válassza ki a **entitás hozzáadása** gombra.

    ![Entitás gomb hozzáadása](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. Az a **entitás hozzáadása** párbeszédpanelen adja meg az értékét a **PartitionKey** és **rowkey tulajdonságok esetén** tulajdonságai.

    ![Entitás párbeszédpanel hozzáadása](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Adja meg alaposan az értékeket. Őket, ha törölni az entitást, és adja hozzá újra a párbeszédpanel bezárása után nem módosítható.

### <a name="to-filter-entities"></a>Entitások szűréséhez

Testre szabhatja az entitásokat, amely egy táblázat jelenik meg, ha a Lekérdezésszerkesztőt használja.

1. A Lekérdezésszerkesztő megnyitásához egy táblában való megtekintéshez.
1. Válassza ki a **Lekérdezésszerkesztő** a táblázat nézet eszköztár.

    A **Lekérdezésszerkesztő** párbeszédpanel jelenik meg. Az alábbi ábrán egy lekérdezést, amely létrehozása folyamatban van a Lekérdezésszerkesztőben.

    ![Lekérdezésszerkesztő](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
1. Ha befejezte a lekérdezés létrehozásához, zárja be a párbeszédpanelt. Az eredményül kapott szöveg formájában a lekérdezés egy WCF-adatszolgáltatások szűrési szövegmező jelenik meg.
1. A lekérdezés futtatásához kattintson a zöld háromszögnek ikonra.

Entitás megjelenő adatok Táblatervező Ha megad egy WCF-adatszolgáltatások szűrési karakterláncot a szűrő szövegmezőbe az közvetlenül is szűrheti. Az ilyen típusú karakterlánc egy SQL WHERE záradék hasonló, de a kiszolgálón, a HTTP-kérést küld. Hogyan hozható létre a szűrő karakterláncok kapcsolatos információkért lásd: [Constructing szűrő karakterláncok Táblatervező](https://docs.microsoft.com/azure/vs-azure-tools-table-designer-construct-filter-strings).

Az alábbi ábrán látható egy példa egy érvényes szűrési karakterlánc:

![Szűrési karakterlánc.](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>Tárolási adatok frissítése

Ha a Server Explorer csatlakozik, vagy adatokat olvas be egy storage-fiókot, a művelet is igénybe vehet egy percig befejezéséhez. Ha a Server Explorer nem sikerül, a művelet lehet, hogy időtúllépés. Adatok lekérésére, miközben továbbra is dolgozhat a Visual Studio más részei. Ha túl sokáig tart a művelet megszakításához válassza a **frissítés leállítása** gombra a Server Explorer eszköztárán.

### <a name="to-refresh-blob-container-data"></a>Blob-tároló adatok frissítése

* Válassza ki a **Blobok** csomópont alatt **tárolási**, majd válassza ki a **frissítése** gombra a Server Explorer eszköztárán.
* Frissítse a blobok listáját, amely megjelenik, válassza a **Execute** gombra.

### <a name="to-refresh-table-data"></a>Táblák adatainak frissítése

* Válassza ki a **táblák** csomópont alatt **tárolási**, majd válassza ki a **frissítése** gombra a Server Explorer eszköztárán.
* Tábla-tervezőben megjelenő az entitások listájának frissítéséhez, jelölje be a **Execute** Táblatervező gombot.

### <a name="to-refresh-queue-data"></a>Várólista adatok frissítése

Válassza ki a **üzenetsorok** csomópont alatt **tárolási**, majd válassza ki a **frissítése** gombra a Server Explorer eszköztárán.

### <a name="to-refresh-all-items-in-a-storage-account"></a>Storage-fiókban lévő összes elem frissítése

Válassza ki a fiók nevét, és válassza ki a **frissítése** gombra a Server Explorer eszköztárán.

## <a name="add-storage-accounts-by-using-server-explorer"></a>Tárfiókok hozzáadása a Kiszolgálókezelő használatával

Kétféleképpen tárfiókok hozzáadása a Kiszolgálókezelő használatával. Hozhat létre egy storage-fiókot az Azure-előfizetésben, vagy egy meglévő tárfiókot is csatlakoztatható.

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>Storage-fiók létrehozása a Server Explorer használatával

1. A Server Explorerben nyissa meg a helyi menüjének a **tárolási** csomópontot, és válassza ki **Create Storage Account**.

1. Az a **Create Storage Account** párbeszédpanel mezőben válassza ki vagy adja meg a következőket:

   * Az Azure-előfizetést, amelyhez hozzá szeretné a tárfiók hozzáadása.
   * Az új storage-fiókhoz használni kívánt név.
   * A régiója vagy affinitáscsoportja (például az USA nyugati RÉGIÓJA és Kelet-Ázsia).
   * A tárfiók, például a használni kívánt replikációs típus a helyileg redundáns.

   ![Azure-tárfiók létrehozása](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Kattintson a **Létrehozás** gombra.

Az új tárfiók megjelenik a **tárolási** lista a Megoldáskezelőben.

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Meglévő tárfiók csatolása a Server Explorer használatával

1. A Server Explorerben nyissa meg a helyi menü az Azure- **tárolási** csomópontot, és válassza ki **külső tárterület csatolása**.

    ![Meglévő tárfiók hozzáadása](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
1. Az a **Create Storage Account** párbeszédpanel mezőben válassza ki vagy adja meg a következőket:

   * A meglévő storage-fiókhoz csatolja neve.
   * A kiválasztott tárfiókhoz tartozó kulcs. Ez az érték általában biztosítunk, amikor egy tárfiók kiválasztását. Ha azt szeretné, hogy a Visual Studióban, hogy ne felejtse el a tárfiók kulcsát, válassza ki a **tárolása fiókkulcs** jelölőnégyzetet.
   * A tárfiók, például a HTTP, HTTPS vagy egyéni végponthoz való kapcsolódáshoz használni kívánt protokollt. Egyéni végpontok kapcsolatos további információkért lásd: [kapcsolati karakterláncok konfigurálása annak](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### <a name="to-view-the-secondary-endpoints"></a>A másodlagos végpontok megtekintése

Ha létrehozott egy tárfiókot a **írásvédett Georedundáns** replikációs beállítás, megtekintheti a másodlagos végpont nyissa meg a helyi menüben a fiók neve, és válassza ki **tulajdonságok**.

![Tárolási másodlagos végpontok](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Storage-fiók eltávolítása a Server Explorerből

A Server Explorerben nyissa meg a helyi menüben a fiók neve, és válassza ki **törlése**. 

Ha törli a storage-fiók, az adott fiók mentett kulcs adataival sem is törlődik.

Ha törli a storage-fiók a Server Explorer, a storage-fiók vagy a benne található adatokat nem érinti. Egyszerűen eltávolítja a hivatkozás a Server Explorer. Storage-fiók végleges törléséhez használja a [az Azure portal](https://portal.azure.com/).

## <a name="next-steps"></a>További lépések

Az Azure storage szolgáltatás használatával kapcsolatos további tudnivalókért lásd: [elérése az Azure Storage szolgáltatásainak](https://msdn.microsoft.com/library/azure/ee405490.aspx).
