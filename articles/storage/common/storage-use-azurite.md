---
title: Azurite-emulátor használata a helyi Azure Storage-fejlesztéshez
description: Az Azurite nyílt forráskódú emulátor (előzetes verzió) ingyenes helyi környezetet biztosít az Azure storage-alkalmazások teszteléséhez.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029920"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Az Azurite-emulátor használata helyi Azure Storage-fejlesztéshez és teszteléshez (előzetes verzió)

Az Azurite 3.2-es verziója nyílt forráskódú emulátor (előzetes verzió) ingyenes helyi környezetet biztosít az Azure blob- és várólista-tárolási alkalmazások teszteléséhez. Ha elégedett az alkalmazás helyi működésével, váltson egy Azure Storage-fiók használatára a felhőben. Az emulátor platformfüggetlen támogatást nyújt Windows, Linux és MacOS rendszeren. Azurite v3 támogatja az Azure Blob szolgáltatás által megvalósított API-kat.

Azurite a jövőbeli tároló emulátor platform. Azurite hatályon tietszően az [Azure Storage-emulátort.](storage-use-emulator.md) Az azurite továbbra is frissül, hogy támogassa az Azure Storage API-k legújabb verzióit.

Az Azurite telepítése és futtatása számos különböző módon telepíthető és futtatva a helyi rendszeren:

  1. [Az Azurite Visual Studio Code bővítmény telepítése és futtatása](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Az Azurite telepítése és futtatása az NPM használatával](#install-and-run-azurite-by-using-npm)
  1. [Az Azurite Docker-lemezkép telepítése és futtatása](#install-and-run-the-azurite-docker-image)
  1. [Azurite klónozása, létrehozása és futtatása a GitHub-tárházból](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Az Azurite Visual Studio Code bővítmény telepítése és futtatása

A Visual Studio-kód ban jelölje ki a **Bővítmények** ablaktáblát, és keresse meg az *Azurite* elemet a **EXTENSIONS:MARKETPLACE**mezőben.

![Visual Studio-kódbővítmények piactere](media/storage-use-azurite/azurite-vs-code-extension.png)

Másik lehetőségként keresse meg a [VS Code bővítmény piacát](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) a böngészőjében. A **Telepítés gombra** kattintva nyissa meg a Visual Studio-kódot, és lépjen közvetlenül az Azurite bővítménylapra.

Az Azurite gyorsan elindíthatja vagy bezárhatja az Azurite szolgáltatást a VS Code állapotsor **[Azurite-várólista-szolgáltatás]** vagy a VS-kód parancspaletán található következő parancsok kiállításával. **[Azurite Blob Service]** A parancspaletta megnyitásához nyomja le az **F1 billentyűt** a VS Code alkalmazásban.

A bővítmény a következő Visual Studio-kódparancsokat támogatja:

   * **Azurite: Start** - Start all Azurite szolgáltatások
   * **Azurite: Close** - Azurite szolgáltatások bezárása
   * **Azurite: Clean** - Az urite-szolgáltatások összes persistency-adatának visszaállítása
   * **Azurite: Blob-szolgáltatás indítása** – Blob-szolgáltatás indítása
   * **Azurite: Blob szolgáltatás bezárása** - Blob szolgáltatás bezárása
   * **Azurite: Tiszta blob szolgáltatás** - Tiszta blob szolgáltatás
   * **Azurite: Várólista-szolgáltatás indítása** – Várólista-szolgáltatás indítása
   * **Azurite: Várólista-szolgáltatás bezárása** – Várólista-szolgáltatás bezárása
   * **Azurite: Tiszta várólista-szolgáltatás** - Tiszta várólista-szolgáltatás

Ha az Azurite-t a Visual Studio-kódon belül szeretné konfigurálni, jelölje ki a bővítmények ablaktábláját. Válassza az **Azurite** **Manage** (gear) ikonját. Válassza **a Bővítménybeállításaikonfigurálás lehetőséget.**

![Azurite konfigurálja a bővítmény beállításait](media/storage-use-azurite/azurite-configure-extension-settings.png)

A következő beállítások támogatottak:

   * **Azurite: Blob Host** – A Blob szolgáltatás figyelési végpont. Az alapértelmezett beállítás: 127.0.0.1.
   * **Azurite: Blob Port** - A Blob szolgáltatás figyelő port. Az alapértelmezett port 10000.
   * **Azurite: Debug** - A hibakeresési napló kimenete az Azurite-csatornára. Az alapértelmezett érték: **hamis**.
   * **Azurite: Hely** – a munkaterület helyének elérési útja. Az alapértelmezett beállítás a Visual Studio-kód munkamappája.
   * **Azurite: Várólista-állomás** – A várólista-szolgáltatás figyelővégpontja. Az alapértelmezett beállítás: 127.0.0.1.
   * **Azurite: Várólistaport** – A várólista-szolgáltatás figyelőportja. Az alapértelmezett port 10001.
   * **Azurite: Silent** - Silent mód letiltja a hozzáférési naplót. Az alapértelmezett érték: **hamis**.

## <a name="install-and-run-azurite-by-using-npm"></a>Az Azurite telepítése és futtatása az NPM használatával

Ehhez a telepítési módszerhez a [Node.js 8.0-s vagy újabb verziója](https://nodejs.org) szükséges. **Az npm** a Node.js telepítéséhez mellékelt csomagkezelő eszköz. A Node.js fájl telepítése után hajtsa végre a következő **npm** parancsot az Azurite telepítéséhez.

```console
npm install -g azurite
```

Az Azurite telepítése után olvassa el a [Futtatás azazurite parancssorból című témakört.](#run-azurite-from-a-command-line)

## <a name="install-and-run-the-azurite-docker-image"></a>Az Azurite Docker-lemezkép telepítése és futtatása

A [DockerHub](https://hub.docker.com/) segítségével a következő paranccsal lekéri a [legújabb Azurite-lemezképet:](https://hub.docker.com/_/microsoft-azure-storage-azurite)

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Futtassa az Azurite Docker-lemezképet:**

A következő parancs futtatja az Azurite Docker-lemezképet. A `-p 10000:10000` paraméter átirányítja a kérelmeket a gazdagép 10000-es portjáról a Docker-példányra.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Adja meg a munkaterület helyét:**

A következő példában `-v c:/azurite:/data` a paraméter *c:/azurite-t* ad meg, mivel az Azurite megőrizte az adatok helyét. A *C:/azurite*könyvtárat a Docker parancs futtatása előtt létre kell hozni.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Csak a blob szolgáltatás futtatása**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Állítsa be az összes Azurite paramétert:**

Ez a példa bemutatja, hogyan állítható be az összes parancssori paraméter. Az alábbi paraméterek mindegyikét egyetlen parancssorba kell helyezni.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Az Azurite indításkor történő konfigurálásáról a [Parancssori beállítások](#command-line-options) című témakörben talál további információt.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Azurite klónozása, létrehozása és futtatása a GitHub-tárházból

Ehhez a telepítési módszerhez telepíteni kell a [Git-et.](https://git-scm.com/) Klónozza az Azurite-projekt [GitHub-tárházát](https://github.com/azure/azurite) a következő konzolparancs használatával.

```console
git clone https://github.com/Azure/Azurite.git
```

A forráskód klónozása után hajtsa végre a klónozott tártár gyökéréből származó parancsokat az Azurite létrehozásához és telepítéséhez.

```console
npm install
npm run build
npm install -g
```

Az Azurite telepítése és építése után olvassa el a [Futtatás az azurite parancssorból című témakört.](#run-azurite-from-a-command-line)

## <a name="run-azurite-from-a-command-line"></a>Az Urite futtatása parancssorból

> [!NOTE]
> Az azurite nem futtatható a parancssorból, ha csak a Visual Studio kódbővítményt telepítette. Ehelyett használja a VS Code parancspaletta. További információt [az Azurite Visual Studio Code bővítmény telepítése és futtatása](#install-and-run-the-azurite-visual-studio-code-extension)című témakörben talál.

A parancssor azonnali megkezdéséhez hozzon létre egy **c:\azurite**nevű könyvtárat, majd indítsa el az Azurite parancsot a következő parancs kiadásával:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Ez a parancs arra utasítja az Azurite-t, hogy az összes adatot egy adott könyvtárban tárolja, **c:\azurite**. Ha a **--location** kapcsoló nincs megadva, akkor az aktuális munkakönyvtárat fogja használni.

## <a name="command-line-options"></a>Parancssori kapcsolók

Ez a szakasz részletezi az Azurite indításakor elérhető parancssori kapcsolókat. Minden parancssori kapcsoló nem kötelező.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

A **-d** egy **parancsikona --debug**, **-l** kapcsoló egy **parancsikont --location**, **-s** egy **parancsikont --silent**, és **-h** egy **parancsikont --help**.

### <a name="blob-listening-host"></a>Blob figyelő állomás

**Nem kötelező** Alapértelmezés szerint az Azurite a 127.0.0.1-et fogja meghallgatni helyi kiszolgálóként. Használja a **--blobHost** kapcsolót a cím beállításához a követelményeknek.

Csak a helyi számítógépen fogadja el a kérelmeket:

```console
azurite --blobHost 127.0.0.1
```

Távoli kérelmek engedélyezése:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> A távoli kérések engedélyezése sebezhetővé teheti a rendszert a külső támadásokkal szemben.

### <a name="blob-listening-port-configuration"></a>Blob figyelőport-konfigurációja

**Nem kötelező** Alapértelmezés szerint az Azurite figyeli a Blob szolgáltatást az 10000-es porton. A **--blobPort** kapcsolóval megadhatja a szükséges figyelőportot.

> [!NOTE]
> Egy testreszabott port használata után frissítenie kell a kapcsolati karakterláncot vagy a megfelelő konfigurációt az Azure Storage-eszközökben vagy SDK-kban.

A Blob szolgáltatás figyelőportjának testreszabása:

```console
azurite --blobPort 8888
```

Hagyja, hogy a rendszer automatikusan kiválasszon egy elérhető portot:

```console
azurite --blobPort 0
```

A használatban lévő port az Azurite indításakor jelenik meg.

### <a name="queue-listening-host"></a>Várólista figyelő állomása

**Nem kötelező** Alapértelmezés szerint az Azurite a 127.0.0.1-et fogja meghallgatni helyi kiszolgálóként. Használja a **--queueHost** kapcsolót a cím igényeinek megfelelően.

Csak a helyi számítógépen fogadja el a kérelmeket:

```console
azurite --queueHost 127.0.0.1
```

Távoli kérelmek engedélyezése:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> A távoli kérések engedélyezése sebezhetővé teheti a rendszert a külső támadásokkal szemben.

### <a name="queue-listening-port-configuration"></a>Várakozási figyelő port konfigurációja

**Nem kötelező** Alapértelmezés szerint az Azurite az 10001-es porton figyeli a Várólista szolgáltatást. A **--queuePort** kapcsolóval adja meg a szükséges figyelőportot.

> [!NOTE]
> Egy testreszabott port használata után frissítenie kell a kapcsolati karakterláncot vagy a megfelelő konfigurációt az Azure Storage-eszközökben vagy SDK-kban.

A Várólista szolgáltatás figyelőportjának testreszabása:

```console
azurite --queuePort 8888
```

Hagyja, hogy a rendszer automatikusan kiválasszon egy elérhető portot:

```console
azurite --queuePort 0
```

A használatban lévő port az Azurite indításakor jelenik meg.

### <a name="workspace-path"></a>Munkaterület elérési útja

**Nem kötelező** Azurite a végrehajtás során adatokat tárol a helyi lemezen. A **--location** kapcsolóval megadhatja az elérési utat munkaterületi helyként. Alapértelmezés szerint a rendszer az aktuális folyamatmunkakönyvtárat fogja használni.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Hozzáférési napló

**Nem kötelező** Alapértelmezés szerint a hozzáférési napló megjelenik a konzolablakban. Tiltsa le a hozzáférési napló megjelenítését a **--silent** kapcsolóval.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Hibakeresési napló

**Nem kötelező** A hibakeresési napló részletes információkat tartalmaz minden kérésről és a kivételverem nyomkövetéséről. Engedélyezze a hibakeresési naplót a **--debug** kapcsoló érvényes helyi fájlelérési útjának megadásával.

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Laza mód

**Nem kötelező** Alapértelmezés szerint az Azurite szigorú módot alkalmaz a nem támogatott kérelemfejlécek és -paraméterek blokkolására. Tiltsa le a szigorú üzemmódot a **--laza** kapcsolóval.

```console
azurite --loose
```

Megjegyzés: a nagy "L" gyorskapcsoló:

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Eszközök és SDK-k engedélyezése

Csatlakozzon az Azurite-hoz az Azure Storage SDK-kból vagy eszközökből, például [az Azure Storage Explorerből,](https://azure.microsoft.com/features/storage-explorer/)bármilyen hitelesítési stratégia használatával. Hitelesítés szükséges. Azurite támogatja az engedélyezést a megosztott kulcs és a megosztott hozzáférési aláírások (SAS). Azurite támogatja a nyilvános konténerekhez való névtelen hozzáférést is.

### <a name="well-known-storage-account-and-key"></a>Jól ismert tárfiók és kulcs

A következő fióknevet és kulcsot használhatja az Azurite-nál. Ez ugyanaz a jól ismert fiók és kulcs, amelyet az örökölt Azure storage-emulátor használ.

* Fiók neve:`devstoreaccount1`
* Fiókkulcs:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> A sharedkey hitelesítés mellett az Azurite támogatja a fiók- és szolgáltatásSAS-hitelesítést. Névtelen hozzáférés akkor is elérhető, ha egy tároló nyilvános hozzáférést engedélyez.

### <a name="connection-string"></a>Kapcsolati sztring

Az alkalmazásból az Azurite-hoz való csatlakozás legegyszerűbb módja egy kapcsolati karakterlánc konfigurálása az alkalmazás konfigurációs fájljában, amely a *UseDevelopmentStorage=true*parancsikonra hivatkozik. Íme egy példa egy *alkalmazás.config* fájlban lévő kapcsolati karakterláncra:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

További információt az [Azure Storage kapcsolati karakterláncai konfigurálása című témakörben talál.](storage-configure-connection-string.md)

### <a name="custom-storage-accounts-and-keys"></a>Egyéni tárfiókok és kulcsok

Az azurite a környezeti változó beállításával `AZURITE_ACCOUNTS` támogatja az egyéni `account1:key1[:key2];account2:key1[:key2];...`tárfióknevek et és kulcsokat: .

Használjon például egy egyéni tárfiókot, amely egy kulccsal rendelkezik:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Vagy használjon több tárfiókot 2-es kulccsal:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite alapértelmezés szerint percenként frissíti az egyéni fiókneveket és kulcsokat a környezeti változóból. Ezzel a funkcióval dinamikusan forgathatja a fiókkulcsot, vagy új tárfiókokat adhat hozzá az Azurite újraindítása nélkül.

> [!NOTE]
> Az `devstoreaccount1` alapértelmezett tárfiók le van tiltva, ha egyéni tárfiókokat állít be.

> [!NOTE]
> Egyéni fióknevek és kulcsok használatakor frissítse ennek megfelelően a kapcsolati karakterláncot.

> [!NOTE]
> Használja `export` a kulcsszót a környezeti változók `set` beállításához linuxos környezetben, amelyet windowsos használatra használ.

### <a name="storage-explorer"></a>Storage Explorer

Az Azure Storage Explorerben csatlakozzon az Azurite-hoz a **Fiók hozzáadása** ikonra kattintva, majd kattintson a Csatolás egy **helyi emulátorhoz** elemre, és kattintson a **Csatlakozás**gombra.

## <a name="differences-between-azurite-and-azure-storage"></a>AzUrite és az Azure Storage közötti különbségek

Az Azurite helyi példánya és a felhőben lévő Azure Storage-fiók között funkcionális különbségek vannak.

### <a name="endpoint-and-connection-url"></a>Végpont és kapcsolat URL-címe

Az Azurite szolgáltatásvégpontjai eltérnek az Azure Storage-fiók végpontjaitól. A helyi számítógép nem végez tartománynév-feloldást, ezért az Azurite-végpontok helyi címeknek kell lenniük.

Ha egy Azure Storage-fiókban címez egy erőforrást, a fiók neve az URI-állomásnév része. A címzett erőforrás az URI elérési út része:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

A következő URI egy Azure Storage-fiókblob érvényes címe:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Mivel a helyi számítógép nem végez tartománynév-feloldást, a fióknév az URI elérési út része az állomásnév helyett. Az Azurite-ban a következő URI-formátumot használja egy erőforráshoz:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Az azurite-i blobok eléréséhez a következő cím használható:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Méretezés és teljesítmény

Az azurite nem skálázható tárolási szolgáltatás, és nem támogatja a nagyszámú egyidejű ügyfelek. Nincs rá garancia, hogy teljesítménnyel kell bajlni. Az azurite fejlesztési és tesztelési célokra szolgál.

### <a name="error-handling"></a>Hibakezelés

Azurite igazodik az Azure Storage hibakezelési logikával, de vannak különbségek. A hibaüzenetek például eltérőek lehetnek, míg a hibaállapotkódok igazodnak egymáshoz.

### <a name="ra-grs"></a>RA-GRS

Azurite támogatja az olvasási hozzáférésű georedundáns replikációt (RA-GRS). A tárolási erőforrások esetében a másodlagos hely elérése a fiók nevéhez **-másodlagos** hozzáfűzésével. Például a következő cím használható egy blob eléréséhez az Azurite írásvédett másodlagos használatával:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite nyílt forráskódú

Az urite-nak nyújtott hozzászólások és javaslatok várjuk. Nyissa meg az Azurite [GitHub projektoldalát](https://github.com/Azure/Azurite/projects) vagy a [GitHub-problémákat](https://github.com/Azure/Azurite/issues) a mérföldkövek és a közelgő funkciók és hibajavítások nyomon követésére szolgáló munkaelemekkel kapcsolatban. A részletes munkaelemeket a GitHub is nyomon követi.

## <a name="next-steps"></a>További lépések

* [Használja az Azure storage emulátor fejlesztési és tesztelési](storage-use-emulator.md) dokumentumok az örökölt Azure storage-emulátor, amely az Azurite által helyettesített dokumentumok.
* [Konfigurálja az Azure Storage-kapcsolati karakterláncok](storage-configure-connection-string.md) elmagyarázza, hogyan kell összeállítani egy érvényes Azure STorage kapcsolati karakterláncok.
