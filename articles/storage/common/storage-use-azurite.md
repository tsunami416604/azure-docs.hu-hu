---
title: A Azurite nyílt forráskódú emulátor használata a blob Storage fejlesztéséhez és teszteléséhez (előzetes verzió)
description: A Azurite nyílt forráskódú emulátor (előzetes verzió) ingyenes helyi környezetet biztosít az Azure Blob Storage-alkalmazások teszteléséhez.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/12/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: ebecd6cf9af5395e4da2b395ca9b2ff974a75409
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721696"
---
# <a name="use-the-azurite-open-source-emulator-for-blob-storage-development-and-testing-preview"></a>A Azurite nyílt forráskódú emulátor használata a blob Storage fejlesztéséhez és teszteléséhez (előzetes verzió)

A Azurite 3-as verziójú nyílt forráskódú emulátora (előzetes verzió) ingyenes helyi környezetet biztosít az Azure Blob Storage-alkalmazások teszteléséhez. Ha elégedett az alkalmazás helyi működésével, váltson egy Azure Storage-fiók használatára a felhőben. Az emulátor platformfüggetlen támogatást biztosít Windows, Linux és MacOS platformokon. A Azurite v3 támogatja az Azure Blob service által megvalósított API-kat.

A Azurite a jövőbeli Storage Emulator platform. A Azurite felülírja az [Azure Storage](storage-use-emulator.md)-emulátort. A Azurite továbbra is frissülni fog az Azure Storage API-k legújabb verzióinak támogatásához.

A Azurite számos különböző módon telepíthetők és futtathatók a helyi rendszeren:

  1. [A Azurite Visual Studio Code bővítmény telepítése és futtatása](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [A Azurite telepítése és futtatása a NPM használatával](#install-and-run-azurite-by-using-npm)
  1. [A Azurite Docker-rendszerkép telepítése és futtatása](#install-and-run-the-azurite-docker-image)
  1. [Azurite klónozása, létrehozása és futtatása a GitHub-adattárból](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>A Azurite Visual Studio Code bővítmény telepítése és futtatása

A Visual Studio Code-ban válassza a bővítmények ablaktáblát, és keressen rá a *Azurite* kifejezésre a következő **bővítményekben: piactér**.

![Visual Studio Code Extensions piactér](media/storage-use-azurite/azurite-vs-code-extension.png)

Másik lehetőségként navigáljon a [vs Code bővítmény piacához](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) a böngészőben. A **telepítés** gombra kattintva nyissa meg a Visual Studio Code-ot, és lépjen közvetlenül a Azurite bővítmény oldalára.

Gyorsan elindíthatja vagy lezárhatja a Azurite, ha a VS Code állapotsorban a **Azurite blob Service** elemre kattint, vagy a vs Code parancs palettáján a következő parancsokat adja ki. A parancs paletta megnyitásához nyomja le az **F1** billentyűt a vs Code-ban.

A bővítmény a következő Visual Studio Code-parancsokat támogatja:

   * **Azurite: Start** – az összes Azurite-szolgáltatás elindítása
   * **Azurite: Az** összes Azurite-szolgáltatás bezárásának lezárása
   * **Azurite: Az összes Azurite Services-perzisztenciatörlése**
   * **Azurite: Start** -blob Start blob Service
   * **Azurite: Bezárás** – blob záró blob szolgáltatás
   * **Azurite: Clean** -blob tiszta blob szolgáltatás

Ha a Azurite a Visual Studio Code-ban szeretné konfigurálni, válassza a bővítmények ablaktáblát, és kattintson a jobb gombbal a **Azurite**elemre. Válassza a **bővítmény beállításainak konfigurálása**lehetőséget.

![Azurite konfigurálása](media/storage-use-azurite/azurite-configure-extension-settings.png)

A következő beállítások támogatottak:

   * **Azurite: BLOB Host** – a blob Service figyelő végpontja. Az alapértelmezett beállítás a 127.0.0.1.
   * **Azurite: BLOB port** – a blob Service figyelési port. Az alapértelmezett port a 10000.
   * **Azurite: Hibakeresés** – a hibakeresési napló kimenete a Azurite csatornára. Az alapértelmezett érték **hamis**.
   * **Azurite: Hely** – a munkaterület helyének elérési útja. Az alapértelmezett érték a Visual Studio Code Working mappa.
   * **Azurite: A** csendes-csendes üzemmód letiltja a hozzáférési naplót. Az alapértelmezett érték **hamis**.

## <a name="install-and-run-azurite-by-using-npm"></a>A Azurite telepítése és futtatása a NPM használatával

Ehhez a telepítési módszerhez a [Node. js 8,0-es vagy újabb verziójára](https://nodejs.org) van szükség. a **NPM** az összes Node. js-telepítéshez mellékelt csomagkezelő eszköz. A Node. js telepítése után hajtsa végre a következő **NPM** -parancsot a Azurite telepítéséhez.

```console
npm install -g azurite
```

A Azurite telepítése után tekintse meg [a Azurite futtatása parancssorból](#run-azurite-from-a-command-line)című témakört.

## <a name="install-and-run-the-azurite-docker-image"></a>A Azurite Docker-rendszerkép telepítése és futtatása

A [DockerHub](https://hub.docker.com/) használatával a [legújabb Azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) -lemezképet a következő paranccsal kérheti le:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Futtassa a Azurite Docker**-rendszerképet:

A következő parancs futtatja a Azurite Docker-rendszerképet. A `-p 10000:10000` paraméter átirányítja a gazdagépről a 10000-as portra érkező kéréseket a Docker-példányra.

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
```

**Munkaterület helyének meghatározása**:

A következő példában a `-v c:/azurite:/data` paraméter a Azurite megőrzött adatterületet adja meg. `c:/azurite`

```console
docker run -p 10000:10000 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Az összes Azurite-paraméter beállítása**:

Ez a példa bemutatja, hogyan állíthatja be az összes parancssori paramétert. Az alábbi paramétereket egyetlen parancssorba kell helyezni.

```console
docker run -p 8888:8888
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
```

További információ a Azurite konfigurálásáról az indításkor: [parancssori kapcsolók](#command-line-options) .

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Azurite klónozása, létrehozása és futtatása a GitHub-adattárból

Ez a telepítési módszer megköveteli, hogy a [git](https://git-scm.com/) telepítve legyen. A Azurite-projekt [GitHub](https://github.com/azure/azurite) -tárházának klónozásához használja az alábbi parancsot.

```console
git clone https://github.com/Azure/Azurite.git
```

A forráskód klónozása után futtassa a következő parancsokat a klónozott tárház gyökeréből a Azurite létrehozásához és telepítéséhez.

```console
npm install
npm run build
npm install -g
```

A Azurite telepítése és létrehozása után tekintse meg [a Azurite futtatása parancssorból](#run-azurite-from-a-command-line)című témakört.

## <a name="run-azurite-from-a-command-line"></a>Azurite futtatása parancssorból

> [!NOTE]
> A Azurite nem futtatható a parancssorból, ha csak a Visual Studio Code bővítményt telepítette. Ehelyett használja a VS Code parancs-palettát. További információkért lásd: [a Azurite Visual Studio Code bővítmény telepítése és futtatása](#install-and-run-the-azurite-visual-studio-code-extension).

A parancssorból való azonnali kezdéshez hozzon létre egy **c:\azurite**nevű könyvtárat, majd indítsa el a Azurite a következő parancs kiadásával:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Ez a parancs arra utasítja a Azurite, hogy egy adott címtárban ( **c:\azurite**) tárolja az összes adatot. Ha a **--Location** beállítás ki van hagyva, az az aktuális munkakönyvtárat fogja használni.

## <a name="command-line-options"></a>Parancssori kapcsolók

Ez a szakasz részletesen ismerteti a Azurite indításakor elérhető parancssori kapcsolókat. Az összes parancssori kapcsoló nem kötelező.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>]
    [-l | --location <workspace path>] [-s | --silent] [-d | --debug <log file path>]
```

Az **-l** kapcsoló a **--Location**,- **s** egy parancsikon a-- **Silent**, a **-d** pedig a-- **Debug**billentyűparancs.

### <a name="listening-host"></a>Figyelő állomás

Nem **kötelező** Alapértelmezés szerint a Azurite helyi kiszolgálóként fogja figyelni a 127.0.0.1-t. A **--blobHost** kapcsoló használatával állítsa be a címeket a követelményekre.

Kérelmek elfogadása csak a helyi gépen:

```console
azurite --blobHost 127.0.0.1
```

Távoli kérelmek engedélyezése:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> A távoli kérelmek lehetővé tehetik, hogy a rendszer sebezhető legyen a külső támadásokkal szemben.

### <a name="listening-port-configuration"></a>Figyelő port konfigurálása

Nem **kötelező** Alapértelmezés szerint a Azurite a 10000-es porton fogja figyelni a Blob service. Használja a **--blobPort** kapcsolót a szükséges figyelési port megadásához.

> [!NOTE]
> Ha testreszabott portot használ, frissítenie kell a kapcsolódási karakterláncot vagy az Azure Storage-eszközök vagy SDK-k megfelelő konfigurációját.

Blob service figyelési port testreszabása:

```console
azurite --blobPort 8888
```

Az elérhető port automatikus kiválasztásának engedélyezése a rendszer számára:

```console
azurite --blobPort 0
```

A használatban lévő port a Azurite indításakor jelenik meg.

### <a name="workspace-path"></a>Munkaterület elérési útja

Nem **kötelező** A Azurite a helyi lemezre menti az adattárolást a végrehajtás során. A **--Location** kapcsoló használatával megadnia a munkaterület helyének elérési útját. Alapértelmezés szerint a rendszer az aktuális folyamat munkakönyvtárát fogja használni.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Hozzáférési napló

Nem **kötelező** Alapértelmezés szerint a hozzáférési napló a konzol ablakban jelenik meg. Tiltsa le a hozzáférési napló megjelenítését a **--Silent** kapcsoló használatával.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Hibakeresési napló

Nem **kötelező** A hibakeresési napló részletes információkat tartalmaz minden kérelemről és a kivételek veremének nyomon követéséről. A hibakeresési napló engedélyezéséhez adjon meg egy érvényes helyi elérési utat a **--Debug** kapcsolóhoz.

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>Eszközök és SDK-k engedélyezése

Az Azure Storage SDK-k vagy eszközök, például a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)bármely hitelesítési stratégia segítségével csatlakozhat a Azurite. Hitelesítés szükséges. A Azurite támogatja a megosztott kulcs és a közös hozzáférésű aláírások (SAS) engedélyezését. A Azurite támogatja a nyilvános tárolók névtelen elérését is.

### <a name="well-known-storage-account-and-key"></a>Jól ismert Storage-fiók és-kulcs

A következő fióknevet és kulcsot használhatja a Azurite. Ez ugyanaz a jól ismert fiók és kulcs, amelyet a régi Azure Storage Emulator használ.

* Fiók neve:`devstoreaccount1`
* Fiók kulcsa:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> A SharedKey-hitelesítésen kívül a Azurite támogatja a fiók és a szolgáltatás SAS-hitelesítését. A névtelen hozzáférés akkor is elérhető, ha egy tároló úgy van beállítva, hogy engedélyezze a nyilvános hozzáférést.

### <a name="connection-string"></a>Kapcsolati sztring

Az alkalmazás Azurite való csatlakoztatásának legegyszerűbb módja egy kapcsolati karakterlánc konfigurálása az alkalmazás konfigurációs fájljában, amely a *UseDevelopmentStorage = True*billentyűparancsra hivatkozik. Íme egy példa az *app. config* fájlban található kapcsolatok karakterláncra:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

További információ: az [Azure Storage-beli kapcsolatok karakterláncának konfigurálása](storage-configure-connection-string.md).

### <a name="storage-explorer"></a>Storage Explorer

Azure Storage Explorer a **fiók hozzáadása** ikonra kattintva kapcsolódjon a Azurite, majd válassza a **csatolás helyi emulátorhoz** lehetőséget, majd kattintson a **Kapcsolódás**elemre.

## <a name="differences-between-azurite-and-azure-storage"></a>A Azurite és az Azure Storage közötti különbségek

A Azurite helyi példánya és a felhőben található Azure Storage-fiók között funkcionális különbségek vannak.

### <a name="endpoint-and-connection-url"></a>Végpont és a kapcsolatok URL-címe

A Azurite szolgáltatási végpontjai eltérnek az Azure Storage-fiókok végpontjaitól. A helyi számítógép nem hajtja végre a tartománynevek feloldását, ezért a Azurite-végpontoknak helyi címnek kell lenniük.

Ha egy Azure Storage-fiókban lévő erőforrást címez, a fiók neve az URI-állomásnév része. A megoldandó erőforrás az URI elérési útjának része:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

A következő URI egy Azure Storage-fiókban található blob érvényes címe:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Mivel a helyi számítógép nem hajtja végre a tartománynevek feloldását, a fiók neve az URI elérési útja része az állomásnév helyett. A következő URI-formátumot használja egy erőforráshoz a Azurite-ben:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

A következő címnek lehet a blobhoz való hozzáférése a Azurite-ben:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Skálázás és teljesítmény

A Azurite nem méretezhető tárolási szolgáltatás, és nem támogatja nagy számú egyidejű ügyfél használatát. Nincs teljesítménybeli garancia. A Azurite fejlesztési és tesztelési célokra szolgál.

### <a name="error-handling"></a>Hibakezelés

A Azurite az Azure Storage-hibák kezelésére szolgáló logikával van összhangban, de különbségek vannak. Előfordulhat például, hogy a hibaüzenetek eltérőek, míg a hibakódok igazítása.

### <a name="ra-grs"></a>RA-GRS

A Azurite támogatja az olvasási hozzáférésű geo-redundáns replikálást (RA-GRS). A tárolási erőforrások esetében a másodlagos helyet a fióknév hozzáfűzésével érheti el. Például a következő címnek lehet a blobokhoz való hozzáférése a Azurite írásvédett másodlagos használatával:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>A Azurite nyílt forráskódú

Szívesen fogadjuk a Azurite vonatkozó hozzájárulásokat és javaslatokat. Lépjen a Azurite [GitHub-projekt](https://github.com/Azure/Azurite/projects) oldalra, vagy [GitHub-problémák](https://github.com/Azure/Azurite/issues) a mérföldkövek és munkaelemek számára, amelyeket nyomon követünk a közelgő funkciókkal és hibajavításokkal kapcsolatban. A részletes munkaelemeket is nyomon követheti a GitHubon.

## <a name="next-steps"></a>További lépések

* [Használja az Azure Storage emulatort fejlesztési és tesztelési](storage-use-emulator.md) dokumentumaihoz az örökölt Azure Storage-emulátort, amelyet a Azurite felülír.
* Az [Azure Storage-kapcsolódási karakterláncok konfigurálása](storage-configure-connection-string.md) azt ismerteti, hogyan lehet egy érvényes Azure Storage-beli kapcsolódási karakterláncot összeállítani.
