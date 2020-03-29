---
title: Adatműveletek hibaelhárítása diagnosztikával és üzenetelemzővel
titleSuffix: Azure Storage
description: Az Azure Storage Analytics, az AzCopy és a Microsoft Message Analyzer segítségével a végpontok között történő hibaelhárítást bemutató oktatóanyag
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 69983502fb7d099f474fb1c4c084f5d381a173e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314759"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Átfogó hibaelhárítás Azure Storage-metrikák és -naplózás, az AzCopy és a Message Analyzer használatával

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

A diagnosztizálás és a hibaelhárítás kulcsfontosságú készség az ügyfélalkalmazások létrehozásához és támogatásához a Microsoft Azure Storage szolgáltatással. Az Azure-alkalmazások elosztott jellege miatt a hibák és a teljesítményproblémák diagnosztizálása és hibaelhárítása összetettebb lehet, mint a hagyományos környezetekben.

Ebben az oktatóanyagban bemutatjuk, hogyan azonosíthat bizonyos hibákat, amelyek hatással lehetnek a teljesítményre, és ezeket a hibákat teljes körűen elháríthatja a Microsoft és az Azure Storage által biztosított eszközök használatával az ügyfélalkalmazás optimalizálása érdekében.

Ez az oktatóanyag egy végpontok között történő hibaelhárítási forgatókönyv gyakorlati feltárását tartalmazza. Az Azure storage-alkalmazások hibaelhárításával kapcsolatos részletes útmutatót a [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md)című témakörben talál.

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Eszközök az Azure Storage-alkalmazások hibaelhárításához

Az ügyfélalkalmazások Microsoft Azure Storage használatával történő hibaelhárításához eszközök kombinációjával megállapíthatja, hogy mikor lépett fel a probléma, és mi lehet a probléma oka. Ezek az eszközök a következőket foglalják magukban:

* **Az Azure Storage Analytics**szolgáltatást. [Az Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) metrikákat és naplózást biztosít az Azure Storage számára.

  * **A tárolási metrikák** nyomon követik a tárolási fiók tranzakciós metrikáit és kapacitásmetrikákat. Metrikák használatával meghatározhatja, hogy az alkalmazás hogyan teljesít a különböző mértékekkel. A [Storage Analytics által](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) nyomon követett metrikák típusairól további információt a Storage Analytics által nyomon követett metrikák típusairól a Storage Analytics által nyomon követett metrikák típusaicímű témakörben talál.
  * **A tárolási naplózás** naplózza az Azure Storage-szolgáltatások minden egyes kérését egy kiszolgálóoldali naplóba. A napló nyomon követi az egyes kérelmek részletes adatait, beleértve a végrehajtott műveletet, a művelet állapotát és a késési információkat. A [Storage Analytics](/rest/api/storageservices/Storage-Analytics-Log-Format) által a naplókba írt kérelem- és válaszadatokról további információt a Storage Analytics naplóiban talál.

* **Az Azure Portal**. Az Azure Portalon konfigurálhatja a tárfiók metrikákat és [naplózást.](https://portal.azure.com) Megtekintheti azokat a diagramokat és grafikonokat is, amelyek megmutatják, hogyan teljesít az alkalmazás az idő múlásával, és beállíthatja a riasztásokat, hogy értesítse, ha az alkalmazás a várttól eltérően teljesít egy adott metrikában.

    Tekintse meg [a tárfiók figyelése az Azure Portalon](storage-monitor-storage-account.md) a figyelés konfigurálása az Azure Portalon.
* **AzCopy .** Az Azure Storage kiszolgálói naplói blobokként tárolódnak, így az AzCopy segítségével átmásolhatja a naplóblobokat egy helyi könyvtárba elemzésre a Microsoft Message Analyzer használatával. Az AzCopy-ról további információt [az Adatok átvitele az AzCopy parancssori segédprogrammal](storage-use-azcopy.md) című témakörben talál.
* **Microsoft Message Analyzer**. A Message Analyzer egy olyan eszköz, amely naplófájlokat használ fel, és vizuális formátumban jeleníti meg a naplóadatokat, így könnyen szűrheti, keresheti és csoportosíthatja a naplóadatokat hasznos készletekbe, amelyek segítségével elemezheti a hibákat és a teljesítménybeli problémákat. Az [Üzenetelemzőről a Microsoft Message Analyzer működési útmutatójában](https://technet.microsoft.com/library/jj649776.aspx) talál további információt.

## <a name="about-the-sample-scenario"></a>A mintaforgatókönyvről

Ebben az oktatóanyagban megvizsgáljuk azt a forgatókönyvet, amelyben az Azure Storage-metrikák alacsony százalékos sikerességi arányt jeleznek egy Azure storage-t meghívja alkalmazás hoz. Az alacsony százalékos sikerességi arány metrika (az [Azure Portalon](https://portal.azure.com) és a metrikatáblákban **százalékos sikerként** jelenik meg) a sikeres műveleteket követi nyomon, de 299-nél nagyobb HTTP-állapotkódot ad vissza. A kiszolgálóoldali tárolási naplófájlokban ezek a műveletek **ÜgyfélHiba-állapotú**tranzakciós állapottal kerülnek rögzítésre. Az alacsony százalékos sikerességi mutatóval kapcsolatos további részletekért olvassa el a [Metrikák alacsony Százalékos sikerességi mutatót, vagy az elemzési naplóbejegyzések Ügyfél-ügyfélhiba tranzakciós állapotú műveleteket tartalmaznak.](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success)

Az Azure Storage-műveletek a 299-nél nagyobb HTTP-állapotkódokat adhatják vissza a szokásos funkciók részeként. De ezek a hibák bizonyos esetekben azt jelzik, hogy lehet, hogy optimalizálja az ügyfélalkalmazás a jobb teljesítmény érdekében.

Ebben a forgatókönyvben az alacsony százalékos sikerességi arányt 100% alattinak tekintjük. Az igényeinek megfelelően azonban más metrikaszintet is választhat. Azt javasoljuk, hogy az alkalmazás tesztelése során hozzon létre egy alapszintű tolerancia a fő teljesítmény mutatók. A tesztelés alapján például meghatározhatja, hogy az alkalmazás 90%-os, azaz 85%-os konzisztens százalékos sikerességi aránnyal rendelkezik-e. Ha a metrikák adatai azt mutatják, hogy az alkalmazás eltér a számtól, akkor megvizsgálhatja, hogy mi okozhatja a növekedést.

A minta forgatókönyv, miután megállapítottuk, hogy a százalékos sikerességi arány metrika alatt 100%, megvizsgáljuk a naplókat, hogy megtalálják a hibákat, amelyek korrelálnak a metrikák, és használja őket, hogy kitaláljuk, mi okozza az alacsonyabb százalékos sikerességi arány. A 400-as tartomány hibáit vizsgáljuk. Ezután közelebbről megvizsgáljuk a 404-es (nem található) hibákat.

### <a name="some-causes-of-400-range-errors"></a>A 400 tartományos hibák néhány oka

Az alábbi példák az Azure Blob Storage-ra vonatkozó kérelmek mintegy 400 tartománybeli hibáinak mintegy mintavételezését és azok lehetséges okait mutatják be. Ezen hibák, valamint a 300 és az 500 tartomány hibái is hozzájárulhatnak az alacsony százalékos sikerességi arányhoz.

Ne feledje, hogy az alábbi listák még messze nem teljesek. [Az msdn-i állapot- és hibakódok](https://msdn.microsoft.com/library/azure/dd179382.aspx) ban az Azure Storage általános hibáival és az egyes tárolási szolgáltatásokra jellemző hibákkal kapcsolatos részletekért.

#### <a name="status-code-404-not-found-examples"></a>Példák a 404-es állapotkódra (nem található)

Akkor fordul elő, ha egy tároló vagy blob olvasási művelet sikertelen, mert a blob vagy a tároló nem található.

* Akkor következik be, ha egy tárolót vagy blobot törölt egy másik ügyfél a kérelem előtt.
* Akkor fordul elő, ha olyan API-hívást használ, amely létrehozza a tárolót vagy blobot, miután ellenőrizte, hogy létezik-e. A CreateIfNotExists API-k először head hívást kezdeményeznek a tároló vagy blob meglétének ellenőrzéséhez; ha nem létezik, egy 404-es hibát ad vissza, majd egy második PUT-hívás történik a tároló vagy blob írásához.

#### <a name="status-code-409-conflict-examples"></a>Példák a 409-es állapotkódra (ütközési)

* Akkor fordul elő, ha egy HOZZon létre egy API-t, hogy hozzon létre egy új tárolót vagy blobot, anélkül, hogy először ellenőrizné a létezését, és már létezik egy ilyen nevű tároló vagy blob.
* Akkor következik be, ha egy tárolót törölnek, és a törlési művelet befejezése előtt megkísérl létrehozni egy ugyanazzal a nevű új tárolót.
* Akkor következik be, ha megad egy bérletet egy tárolón vagy blobon, és már van jelen címbérlet.

#### <a name="status-code-412-precondition-failed-examples"></a>412-es állapotkód (az előfeltétel sikertelen) példái

* Akkor következik be, ha a feltételes fejléc által megadott feltétel nem teljesül.
* Akkor következik be, amikor a megadott bérleti azonosító nem egyezik meg a tárolón vagy blobon lévő címbérlet-azonosítóval.

## <a name="generate-log-files-for-analysis"></a>Naplófájlok létrehozása elemzéshez

Ebben az oktatóanyagban a Message Analyzer segítségével három különböző típusú naplófájllal dolgozunk, bár az alábbi akadáinkkal dolgozhat:

* A **kiszolgálónapló**, amely akkor jön létre, amikor engedélyezi az Azure Storage naplózását. A kiszolgálónapló az Azure Storage-szolgáltatások – blob, várólista, tábla és fájl – ellen meghívott műveletek adatait tartalmazza. A kiszolgálónapló jelzi, hogy melyik művelet et hívták meg, és milyen állapotkódot adott vissza, valamint a kérésés a válasz egyéb részleteit.
* A **.NET ügyfélnapló,** amely akkor jön létre, amikor engedélyezi az ügyféloldali naplózást a .NET alkalmazásból. Az ügyfélnapló részletes információkat tartalmaz arról, hogy az ügyfél hogyan készíti elő a kérést, és hogyan fogadja és dolgozza fel a választ.
* A **HTTP-hálózati nyomkövetési napló,** amely adatokat gyűjt a HTTP/HTTPS kérelem és válasz adatok, beleértve az Azure Storage elleni műveleteket. Ebben az oktatóanyagban a hálózati nyomkövetést a Message Analyzer segítségével hozzuk létre.

### <a name="configure-server-side-logging-and-metrics"></a>Kiszolgálóoldali naplózás és mérőszámok konfigurálása

Először is konfigurálnunk kell az Azure Storage naplózását és metrikákat, hogy a szolgáltatás oldaláról származó adatokat elemezhessünk. A naplózást és a metrikákat számos módon konfigurálhatja – az [Azure Portalon](https://portal.azure.com)keresztül a PowerShell használatával vagy programozott módon. A naplózás és a metrikák konfigurálásával kapcsolatos részletekért olvassa el a [Metrikák engedélyezése](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) és a [naplózás engedélyezése](storage-analytics-logging.md#enable-storage-logging) című témakört.

### <a name="configure-net-client-side-logging"></a>A .NET ügyféloldali naplózás konfigurálása

Egy .NET alkalmazás ügyféloldali naplózásának konfigurálásához engedélyezze a .NET diagnosztikát az alkalmazás konfigurációs fájljában (web.config vagy app.config). A részletekért tekintse meg [az ügyféloldali naplózást a .NET storage ügyféltárral](https://msdn.microsoft.com/library/azure/dn782839.aspx) és [az ügyféloldali naplózást a Microsoft Azure Storage SDK for Java for Java-val](https://msdn.microsoft.com/library/azure/dn782844.aspx) az MSDN-en.

Az ügyféloldali napló részletes információkat tartalmaz arról, hogy az ügyfél hogyan készíti elő a kérést, és hogyan fogadja és dolgozza fel a választ.

A Storage Client Library az ügyféloldali naplóadatokat az alkalmazás konfigurációs fájljában (web.config vagy app.config) megadott helyen tárolja.

### <a name="collect-a-network-trace"></a>Hálózati nyomkövetés gyűjtése

Az Üzenetelemző segítségével http/HTTPS hálózati nyomkövetést gyűjthet az ügyfélalkalmazás futása közben. A Message Analyzer a [Fiddlert](https://www.telerik.com/fiddler) használja a háttérben. A hálózati nyomkövetés összegyűjtése előtt azt javasoljuk, hogy a Fiddler-t állítsa be a titkosítatlan HTTPS-forgalom rögzítésére:

1. Telepítse [a Fiddler](https://www.telerik.com/download/fiddler).
2. Fiddler kilövése.
3. Eszközök kiválasztása **| Hegedűs options**.
4. A Beállítások párbeszédpanelen győződjön meg arról, hogy a **HTTPS-adatok rögzítése** és **a HTTPS-forgalom visszafejtése** egyaránt ki van jelölve, az alábbiak szerint.

![A Hegedűs beállításainak konfigurálása](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Az oktatóanyaghoz először gyűjtse össze és mentse a hálózati nyomkövetést az Üzenetelemzőben, majd hozzon létre egy elemzési munkamenetet a nyomkövetés és a naplók elemzéséhez. Hálózati nyomkövetés gyűjtése a Message Analyzer ben:

1. Az Üzenetelemző csoportban válassza a **Fájl | Gyors nyomkövetés | Titkosítatlan HTTPS**.
2. A nyom követés azonnal megkezdődik. Válassza **a Leállítás** lehetőséget a nyomkövetés leállításához, hogy csak a tárolási forgalom nyomon követésére tudjuk beállítani.
3. A kontúrozási munkamenet szerkesztéséhez válassza a **Szerkesztés** lehetőséget.
4. Válassza a **Microsoft-Pef-WebProxy ETW-szolgáltató** tól jobbra található **Konfigurálás** hivatkozást.
5. A **Speciális beállítások** párbeszédpanelen kattintson a **Szolgáltató** fülre.
6. A **Hostname Filter** mezőben adja meg a tárolóvégpontokat, szóközök szerint elválasztva. A végpontokat például a következőképpen adhatja meg; módosítsa `storagesample` a tárfiók nevére:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Lépjen ki a párbeszédpanelből, és kattintson az **Újraindítás** gombra, ha meg szeretné kezdeni a nyomkövetés gyűjtését a gazdagépnév szűrővel a helyén, hogy csak az Azure Storage hálózati forgalma szerepeljen a nyomkövetésben.

> [!NOTE]
> Miután befejezte a hálózati nyomkövetés gyűjtését, javasoljuk, hogy a Fiddlerben módosított beállításokat módosítsa a HTTPS-forgalom visszafejtéséhez. A Hegedűbeállítások párbeszédpanelen törölje a jelet a **HTTPS-adatok rögzítése** és **a HTTPS-forgalom visszafejtése** jelölőnégyzetből.

További információt a Technet [hálózati nyomkövetési funkcióinak használata](https://technet.microsoft.com/library/jj674819.aspx) című témakörben talál.

## <a name="review-metrics-data-in-the-azure-portal"></a>Metrikák adatainak áttekintése az Azure Portalon

Miután az alkalmazás már fut egy ideig, áttekintheti az Azure [Portalon](https://portal.azure.com) megjelenő metrikadiagramok a szolgáltatás teljesítményének figyeléséhez.

Először keresse meg a tárfiókot az Azure Portalon. Alapértelmezés szerint a figyelési diagram a **siker százalékos** metrika jelenik meg a fiók panelen. Ha korábban módosította a diagramot, hogy különböző mutatókat jelenítsen meg, adja hozzá a **Sikeresség százalékos** mutatót.

Most megjelenik **a sikerességi százalék** a figyelési diagramban, valamint az esetlegesen hozzáadott egyéb mutatók. A forgatókönyvben a Message Analyzer naplóinak elemzésével vizsgáljuk meg a következőt, a százalékos sikerességi arány valamivel 100% alatt van.

A mérőszámdiagramok hozzáadásáról és testreszabásával kapcsolatos további részleteket a [Metrikadiagramok testreszabása című témakörben talál.](storage-monitor-storage-account.md#customize-metrics-charts)

> [!NOTE]
> A tárterület-metrikák engedélyezése után eltarthat egy ideig, amíg a metrikák adatai megjelennek az Azure Portalon. Ennek az az oka, hogy az előző óra óránkénti metrikák nem jelennek meg az Azure Portalon, amíg az aktuális óra le nem telik. Emellett a percmetrikák jelenleg nem jelennek meg az Azure Portalon. Így attól függően, hogy mikor engedélyezi a mutatókat, akár két órát is igénybe vehet a metrikák adatainak megtekintése.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Kiszolgálói naplók másolása helyi könyvtárba az AzCopy segítségével

Az Azure Storage a kiszolgálónapló-adatokat a blobokba írja, míg a metrikák táblákba vannak írva. A naplóblobok a tárfiók `$logs` jól ismert tárolójában érhetők el. A naplóblobok neve hierarchikusan év, hónap, nap és óra szerint történik, így könnyen megtalálhatja a vizsgálni kívánt időtartományt. A fiókban `storagesample` például a 2015.02.01-i naplóblobok tárolója 8-9 `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`órától a. A tárolóban lévő egyes blobok neve `000000.log`egymás után történik, kezdve a.

Az AzCopy parancssori eszközzel letöltheti ezeket a kiszolgálóoldali naplófájlokat a helyi számítógépen kiválasztott helyre. Például a következő paranccsal letöltheti a 2015. `C:\Temp\Logs\Server` cserélje `<storageaccountname>` le a tárfiók nevére:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

AzAzCopy letölthető az [Azure Downloads](https://azure.microsoft.com/downloads/) oldalon. Az AzCopy használatáról az [Adatok átvitele az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)című témakörben talál.

A kiszolgálóoldali naplók letöltéséről a [Tárolónaplózási napló adatainak letöltése](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)című témakörben talál további információt.

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Naplóadatok elemzése a Microsoft Message Analyzer segítségével

A Microsoft Message Analyzer a protokollüzenet-küldési forgalom, események és más rendszer- vagy alkalmazásüzenetek hibaelhárítási és diagnosztikai helyzetekben történő rögzítésére, megjelenítésére és elemzésére szolgáló eszköz. A Message Analyzer lehetővé teszi a napló- és mentett nyomkövetési fájlokból származó adatok betöltését, összesítését és elemzését is. Az Üzenetelemzőről a [Microsoft Message Analyzer működési útmutatójában](https://technet.microsoft.com/library/jj649776.aspx)talál további információt.

A Message Analyzer olyan eszközöket tartalmaz az Azure Storage-hoz, amelyek segítenek a kiszolgálói, ügyfél- és hálózati naplók elemzésében. Ebben a szakaszban bemutatjuk, hogyan használhatja ezeket az eszközöket a tárolási naplók alacsony százalékos sikeressúságának megoldásához.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>A Message Analyzer és az Azure Storage Assets letöltése és telepítése

1. Töltse le az [Üzenetelemzőt](https://www.microsoft.com/download/details.aspx?id=44226) a Microsoft letöltőközpontjából, és futtassa a telepítőt.
2. Üzenetelemző indítása.
3. Az **Eszközök menüben** válassza az **Eszközkezelő**lehetőséget. Az **Eszközkezelő** párbeszédpanelen válassza a **Letöltések**lehetőséget, majd szűrje az **Azure Storage webhelyen.** Az Azure Storage Assets, az alábbi képen látható módon.
4. Kattintson **az összes megjelenített elem szinkronizálása** az Azure Storage assets telepítéséhez. A rendelkezésre álló eszközök a következők:
   * **Az Azure Storage színszabályai:** Az Azure Storage színszabályai lehetővé teszik, hogy speciális szűrőket határozzon meg, amelyek szín-, szöveg- és betűstílusokat használnak a nyomkövetésben meghatározott információkat tartalmazó üzenetek kiemeléséhez.
   * **Azure storage-diagramok:** Az Azure Storage-diagramok előre definiált diagramok, amelyek a kiszolgáló naplóadatait graph. Vegye figyelembe, hogy az Azure Storage-diagramok használata ebben az időben, előfordulhat, hogy csak a kiszolgáló bejelentkezést az Analysis Grid.
   * **Azure storage-elemzők:** Az Azure Storage-elemzők elemzik az Azure Storage-ügyfél-, kiszolgáló- és HTTP-naplókat, hogy megjelenítsék őket az Analysis Gridben.
   * **Azure storage-szűrők:** Az Azure Storage-szűrők előre definiált feltételek, amelyek segítségével lekérdezheti az adatokat az Analysis Gridben.
   * **Azure Storage View elrendezések:** Az Azure Storage nézetelrendezések előre definiált oszlopelrendezések és csoportosítások az Analysis Gridben.
5. Az eszközök telepítése után indítsa újra az Üzenetelemzőt.

![Üzenetelemző eszközkezelője](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Telepítse az összes azure storage-eszközök jelen bemutató céljából.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>A naplófájlok importálása az Üzenetelemzőbe

Az összes mentett naplófájlt (kiszolgálóoldali, ügyféloldali és hálózati) egyetlen munkamenetbe importálhatja a Microsoft Message Analyzer programban elemzésre.

1. A Microsoft Message Analyzer **Fájl** menüjében kattintson az **Új munkamenet**menü Üres **munkamenet parancsára.** Az **Új munkamenet** párbeszédpanelen adja meg az elemzési munkamenet nevét. A **Munkamenet részletei** panelen kattintson a **Fájlok** gombra.
2. Az Üzenetelemző által létrehozott hálózati nyomkövetési adatok betöltéséhez kattintson a **Fájlok hozzáadása**elemre, keresse meg azt a helyet, ahová a .matp fájlt mentette a webes nyomkövetési munkamenetből, jelölje ki a .matp fájlt, és kattintson a **Megnyitás gombra.**
3. A kiszolgálóoldali naplóadatok betöltéséhez kattintson a **Fájlok hozzáadása**gombra, keresse meg azt a helyet, ahol a kiszolgálóoldali naplókat letöltötte, jelölje ki az elemezni kívánt időtartomány naplófájljait, majd kattintson a **Megnyitás gombra.** Ezután a **Munkamenet részletei** panelen állítsa be a szöveges napló **konfigurációja** legördülő minden kiszolgálóoldali naplófájl hoz **AzureStorageLog** annak érdekében, hogy a Microsoft Message Analyzer tudja elemezni a naplófájlt helyesen.
4. Az ügyféloldali naplóadatok betöltéséhez kattintson a **Fájlok hozzáadása**gombra, keresse meg azt a helyet, ahol az ügyféloldali naplókat mentette, jelölje ki az elemezni kívánt naplófájlokat, és kattintson a **Megnyitás gombra.** Ezután a **Munkamenet részletei** panelen állítsa be a szöveges napló **konfigurációja** legördülő minden ügyféloldali naplófájlhoz **az AzureStorageClientDotNetV4** értékre, hogy a Microsoft Message Analyzer megfelelően elemezhesse a naplófájlt.
5. Kattintson a **Kezdés** gombra az **Új munkamenet** párbeszédpanelen a naplóadatok betöltéséhez és elemzéséhez. A naplóadatok megjelennek az Üzenetelemző elemző rácsban.

Az alábbi képen egy példamunkamenet látható, amely kiszolgálóval, ügyféllel és hálózati nyomkövetési naplófájlokkal van konfigurálva.

![Üzenetelemző munkamenetének konfigurálása](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Vegye figyelembe, hogy a Message Analyzer betölti a naplófájlokat a memóriába. Ha nagy mennyiségű naplóadatkal rendelkezik, érdemes szűrni, hogy a legjobb teljesítményt kapja a Message Analyzer-től.

Először határozza meg az időkeretet, amelyet szeretne felülvizsgálni, és tartsa meg ezt az időkeretet a lehető legkisebb mértékben. Sok esetben legfeljebb egy perc vagy óra időszakot szeretne áttekinteni. Importálja az igényeinek megfelelő legkisebb naplókat.

Ha még mindig nagy mennyiségű naplóadatáll meg, akkor érdemes lehet megadni egy munkamenet-szűrőt a naplóadatok szűréséhez a betöltés előtt. A **Munkamenetszűrő** mezőben jelölje ki a **Tár** gombot egy előre definiált szűrő kiválasztásához; például válassza a **Globális időszűrő I** az Azure Storage-szűrők egy időintervallumszűréshez. Ezután szerkesztheti a szűrési feltételeket a megtekinteni kívánt időintervallum kezdő és záró időbélyegének megadásához. Szűrhet egy adott állapotkódra is; megadhatja például, hogy csak olyan naplóbejegyzéseket töltsön be, amelyek állapotkódja 404.

A naplóadatok Microsoft Message Analyzer programba történő importálásáról az [Üzenetadatok beolvasása](https://technet.microsoft.com/library/dn772437.aspx) a TechNet szolgáltatásban című témakörben talál további információt.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Naplófájl adatainak korrelációja az ügyfélkérelem-azonosító val

Az Azure Storage ügyfélkódtár automatikusan létrehoz egy egyedi ügyfélkérelem-azonosítót minden kérelemhez. Ez az érték az ügyfélnaplóba, a kiszolgálónaplóba és a hálózati nyomkövetésbe van írva, így az üzenetelemző mindhárom naplójának adataira vonatkozó analizátorként használható. Az ügyfélkérelem-azonosítóval kapcsolatos további információkért lásd az [ügyfélkérelem-azonosítót.](storage-monitoring-diagnosing-troubleshooting.md#client-request-id)

Az alábbi szakaszok ismertetik, hogyan használható az előre konfigurált és egyéni elrendezésnézetek az ügyfélkérelem-azonosító n alapuló adatok korrelációjára és csoportosítására.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Az Elemző rácsban megjelenítendő nézetelrendezés kiválasztása

A Storage Assets for Message Analyzer tartalmazza az Azure Storage View layouts, amelyek előre konfigurált nézetek, amelyek segítségével megjelenítheti az adatokat hasznos csoportosítások és oszlopok a különböző forgatókönyvek. Egyéni nézetelrendezéseket is létrehozhat, és mentheti őket újrafelhasználás céljából.

Az alábbi képen a **Nézet elrendezése** menü látható, amely az eszköztár **menüszalagjának Nézetelrendezés** parancsával érhető el. Az Azure Storage nézetelrendezései a menü Ben az **Azure Storage-csomópont** alatt vannak csoportosítva. A keresőmezőben csak `Azure Storage` az Azure Storage nézetelrendezések szűréséhez kereshet. A nézetelrendezés melletti csillagot is kiválaszthatja, hogy az kedvenc legyen, és a menü tetején jelenítse meg.

![Nézet elrendezés e menü](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Először válassza a **ClientRequestID és a Module szerint csoportosítva**lehetőséget. Ez a nézetelrendezés először az ügyfélkérelem-azonosító, majd a forrásnaplófájl (vagy az Üzenetelemző **modulja)** alapján csoportosítja a naplóadatokat mindhárom naplóból. Ezzel a nézetben részletezhet egy adott ügyfélkérelem-azonosítót, és megtekintheti az adott ügyfélkérelem-azonosító mindhárom naplófájljának adatait.

Az alábbi képen ez az elrendezési nézet jelenik meg a mintanapló adataira alkalmazva, az oszlopok egy részhalmazával. Láthatja, hogy egy adott ügyfélkérelem-azonosító esetében az Analysis Grid megjeleníti az ügyfélnaplóból, a kiszolgálónaplóból és a hálózati nyomkövetésből származó adatokat.

![Azure Storage View elrendezése](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> A különböző naplófájlok különböző oszlopokkal rendelkeznek, így ha több naplófájl adatai jelennek meg az Analysis Grid ben, előfordulhat, hogy egyes oszlopok nem tartalmaznak adatokat egy adott sorhoz. A fenti képen például az ügyfélnapló sorai nem jelenítik meg az **Időbélyeg**, A **TimeElapsed**, **a Forrás**és a **Cél** oszlopok adatait, mert ezek az oszlopok nem léteznek az ügyfélnaplóban, de léteznek a hálózati nyomkövetésben. Hasonlóképpen az **Időbélyeg** oszlop a kiszolgálónapló időbélyegadatait jeleníti meg, de nem jelenik meg a **TimeElapsed**, **Source**és **Destination** oszlopok, amelyek nem részei a kiszolgálónaplónak.
>
>

Az Azure Storage nézetelrendezések használata mellett saját nézetelrendezéseket is definiálhat és menthet. Az adatok csoportosításához más kívánt mezőket is kiválaszthat, és a csoportosítást az egyéni elrendezés részeként is mentheti.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Színszabályok alkalmazása az Elemzőrácsra

A tárolóeszközök színszabályokat is tartalmaznak, amelyek vizuális eszközt biztosítanak az Analysis Grid különböző típusú hibáinak azonosítására. Az előre definiált színszabályok a HTTP-hibákra vonatkoznak, így csak a kiszolgálónapló és a hálózati nyomkövetés esetén jelennek meg.

Színszabályok alkalmazásához válassza a **Színszabályok** lehetőséget az eszköztár menüszalagján. Az Azure Storage színszabályai a menüben jelennek meg. Az oktatóanyaghoz válassza az **Ügyfélhibák (StatusCode 400 és 499 között)** lehetőséget, ahogy az az alábbi képen látható.

![Azure Storage View elrendezése](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Az Azure Storage színszabályai mellett saját színszabályokat is definiálhat és menthet.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>400 tartományos hibák kereséséhez csoportosítsa és szűrje a naplóadatokat

Ezután csoportosítjuk és szűrjük a naplóadatokat, hogy megtaláljuk az összes hibát a 400 tartományban.

1. Keresse meg a **StatusCode** oszlopot az Analysis Gridben, kattintson a jobb gombbal az oszlopfejlécre, és válassza a **Csoportosítás parancsot.**
2. Ezután csoportosítsa a **ClientRequestId oszlopot.** Látni fogja, hogy az Elemzési rácsban lévő adatok mostantól állapotkód és ügyfélkérelem-azonosító szerint vannak rendezve.
3. A Szűrő megtekintése eszközablak megjelenítése, ha még nem jelenik meg. Az eszköztár menüszalagján válassza a **Windows eszköz**lehetőséget, majd **a Szűrő megtekintése lehetőséget.**
4. Ha csak 400 tartományos hibák megjelenítéséhez szeretné szűrni a naplóadatokat, adja hozzá a következő szűrőfeltételeket a **Nézetszűrő** ablakhoz, és kattintson az **Alkalmaz**gombra:

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

Az alábbi képen a csoportosítás és a szűrés eredményei láthatók. A 409-es állapotkód csoportosítása alatti **ClientRequestID** mező kibontásával például egy olyan műveletet jelenít meg, amely az adott állapotkódot eredményezett.

![Azure Storage View elrendezése](./media/storage-e2e-troubleshooting/400-range-errors1.png)

A szűrő alkalmazása után látni fogja, hogy az ügyfélnapló sorai ki vannak zárva, mivel az ügyfélnapló nem tartalmaz **StatusCode** oszlopot. Először is áttekintjük a kiszolgáló és a hálózati nyomkövetési naplókat a 404-es hibák megkereséséhez, majd visszatérünk az ügyfélnaplóba, hogy megvizsgáljuk az őket eredményező ügyfélműveleteket.

> [!NOTE]
> Szűrhet a **StatusCode** oszlopra, és továbbra is megjelenítheti mindhárom napló adatait, beleértve az ügyfélnaplót is, ha olyan kifejezést ad hozzá a szűrőhöz, amely naplóbejegyzéseket tartalmaz, ahol az állapotkód null értékű. A szűrőkifejezés létrehozásához használja a következőket:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Ez a szűrő az ügyfélnapló összes sorát adja vissza, és csak azokat a sorokat a kiszolgálónaplóból és a HTTP-naplóból, ahol az állapotkód nagyobb, mint 400. Ha az ügyfélkérelem-azonosító és modul szerint csoportosított nézetelrendezésre alkalmazza, kereshet vagy görgethet a naplóbejegyzésekközött, hogy megtalálja azokat, ahol mindhárom napló képviselteti magát.

### <a name="filter-log-data-to-find-404-errors"></a>Naplóadatok szűrése a 404-es hibák kereséséhez

A storage assets tartalmaz előre definiált szűrőket, amelyek segítségével szűkítheti az adatokat a keresett hibák vagy trendek megkereséséhez. Ezután két előre definiált szűrőt alkalmazunk: az egyik a kiszolgáló és a hálózati nyomkövetési naplók at 404-es hibák esetén szűri, a másik pedig egy adott időtartományadatait szűri.

1. A Szűrő megtekintése eszközablak megjelenítése, ha még nem jelenik meg. Az eszköztár menüszalagján válassza a **Windows eszköz**lehetőséget, majd **a Szűrő megtekintése lehetőséget.**
2. A Nézetszűrő ablakban **Library**válassza a `Azure Storage` Könyvtár lehetőséget, és keressen rá az Azure Storage-szűrők megkereséséhez. Válassza ki a **404 (nem található) üzenetek szűrőjét az összes naplóban.**
3. Jelenítse meg újra a **Könyvtár** menüt, és keresse meg és jelölje ki a **Globális időszűrőt**.
4. A szűrőben megjelenő időbélyegek szerkesztése a megtekinteni kívánt tartományra. Ez segít az elemzésre kerülő adatok körének szűkítésében.
5. A szűrőnek az alábbi példához hasonlóan kell megjelennie. Kattintson az **Alkalmaz** gombra, ha alkalmazni szeretné a szűrőt az Elemző rácsra.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Azure Storage View elrendezése](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>A naplóadatok elemzése

Most, hogy csoportosította és szűrte az adatokat, megvizsgálhatja a 404-es hibákat generáló egyedi kérelmek részleteit. Az aktuális nézetelrendezésben az adatok ügyfélkérelem-azonosító, majd naplóforrás szerint vannak csoportosítva. Mivel olyan kérelmeket szűrünk, amelyekben a StatusCode mező 404-et tartalmaz, csak a kiszolgáló és a hálózati nyomkövetési adatok jelennek meg, az ügyfélnapló-adatok nem.

Az alábbi képen egy adott kérelmet, ahol a Blob begetési művelet 404-es, mert a blob nem létezik. Ne feledje, hogy néhány oszlopot eltávolítottak a szabványos nézetből a megfelelő adatok megjelenítéséhez.

![Szűrt kiszolgáló- és hálózati nyomkövetési naplók](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Ezután korreláljuk ezt az ügyfélkérelem-azonosítót az ügyfélnapló adataival, hogy lássuk, milyen műveleteket végrehajtott az ügyfél a hiba bekövetkeztekekor. A munkamenethez új Analysis Grid nézetet jeleníthet meg az ügyfélnapló adatainak megtekintéséhez, amely egy második lapon nyílik meg:

1. Először másolja a **ClientRequestId** mező értékét a vágólapra. Ezt úgy teheti meg, hogy bármelyik sort kijelöli, megkeresi a **ClientRequestId mezőt,** a jobb gombbal kattint az adatértékre, és a **Copy 'ClientRequestId' parancsot választja.**
2. Az eszköztár menüszalagján válassza az **Új megjelenítő**lehetőséget, majd az **Analysis Grid** lehetőséget új lap megnyitásához. Az új lapon a naplófájlok összes adata látható csoportosítás, szűrés vagy színszabályok nélkül.
3. Az eszköztár menüszalagján válassza az **Elrendezés megtekintése**lehetőséget, majd az **Azure Storage** szakaszban válassza az **Összes .NET ügyféloszlop** lehetőséget. Ez a nézetelrendezés az ügyfélnaplóból, valamint a kiszolgálóés a hálózati nyomkövetési naplókból származó adatokat jeleníti meg. Alapértelmezés szerint a **MessageNumber** oszlopban van rendezve.
4. Ezután keresse meg az ügyfélnaplóban az ügyfélkérelem-azonosítót. Az eszköztár menüszalagján válassza az **Üzenetek keresése lehetőséget,** majd adjon meg egy egyéni szűrőt az ügyfélkérelem-azonosítón a **Keresés** mezőben. Ezt a szintaxist használja a szűrőhöz, amely megadja a saját ügyfélkérelem-azonosítóját:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Az Üzenetelemző megkeresi és kiválasztja az első naplóbejegyzést, ahol a keresési feltételek megfelelnek az ügyfélkérelem-azonosítónak. Az ügyfélnaplóban több bejegyzés található az egyes ügyfélkérelmek azonosítóihoz, ezért érdemes lehet őket a **ClientRequestId** mezőben csoportosítani, hogy könnyebben láthassa őket együtt. Az alábbi képen látható a megadott ügyfélkérelem-azonosító ügyfélnaplójában lévő összes üzenet.

![404-es hibákat megjelenítő ügyfélnapló](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

A két lap nézetelrendezésében látható adatok alapján elemezheti a kérelem adatait, hogy megállapítsa, mi okozhatta a hibát. Megtekintheti az ezt megelőző kérelmeket is, hogy lássa, egy korábbi esemény okozhatta-e a 404-es hibát. Például áttekintheti az ügyfélnapló-bejegyzéseket az ügyfélkérelem-azonosítót megelőzően annak megállapítására, hogy a blob ot törölték-e, vagy a hiba oka az volt, hogy az ügyfélalkalmazás egy container vagy blob createIfNotExists API-t hívott meg. Az ügyfélnaplóban a blob címét a **Leírás** mezőben találja; a kiszolgálói és hálózati nyomkövetési naplókban ez az információ az **Összegzés** mezőben jelenik meg.

Miután ismeri a blob címét, amely a 404-es hibát hozta, további vizsgálatot is vizsgálhat. Ha a naplóbejegyzésekben az ugyanazon a blobon végzett műveletekhez társított egyéb üzeneteket keresi, ellenőrizheti, hogy az ügyfél korábban törölte-e az entitást.

## <a name="analyze-other-types-of-storage-errors"></a>Más típusú tárolási hibák elemzése

Most, hogy már ismeri a Naplóadatok elemzéséhez használt Üzenetelemző t, más típusú hibákat is elemezhet nézetelrendezések, színszabályok és keresés/szűrés használatával. Az alábbi táblázatok felsorolják az esetleg előforduló problémákat és a megkeresendő szűrési feltételeket. A szűrők és az Üzenetelemző szűrési nyelvének létrehozásáról az [Üzenetadatok szűrése](https://technet.microsoft.com/library/jj819365.aspx)című témakörben talál további információt.

| Hogy kivizsgáljam... | Szűrőkifejezés használata... | A kifejezés a naplóra vonatkozik (ügyfél, kiszolgáló, hálózat, mind) |
| --- | --- | --- |
| Váratlan késések az üzenetek kézbesítésében egy várólistán |Az AzureStorageClientDotNetV4.Description tartalmazza a "Sikertelen művelet újbóli megkísérlése" műveletet. |Ügyfél |
| HTTP-növekedés a százalékszabályozáshiba esetén |HTTP. Response.StatusCode == 500 &#124;&#124; HTTP. Response.StatusCode == 503 |Network (Hálózat) |
| A PercentTimeoutError növekedése |HTTP. Response.StatusCode == 500 |Network (Hálózat) |
| PercentTimeoutError (összes) növekedése |*StatusCode == 500 |Összes |
| PercentNetworkError növekedése |AzureStorageClientDotNetV4.EventLogEntry.Level < 2 |Ügyfél |
| HTTP 403 (Tiltott) hibaüzenetek |HTTP. Response.StatusCode == 403 |Network (Hálózat) |
| HTTP 404 (Nem található) hibaüzenetek |HTTP. Response.StatusCode == 404 |Network (Hálózat) |
| 404 (összes) |*StatusCode == 404 |Összes |
| Egy közös hozzáférésű jogosultságkód (SAS) engedélyezési problémája |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Network (Hálózat) |
| HTTP 409 (Ütközés) hibaüzenetek |HTTP. Response.StatusCode == 409 |Network (Hálózat) |
| 409 (összes) |*StatusCode == 409 |Összes |
| Az alacsony sikerességi vagy elemzési naplóbejegyzések ÜgyfélEgyébHibák tranzakciós állapotú műveleteket tartalmaznak |AzureStorageLog.RequestStatus == "ClientOtherError" |Kiszolgáló |
| Nagle figyelmeztetés |((AzureStorageLog.EndToEndLatencyMS – AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) és (AzureStorageLog.RequestPacketSize <1460) és (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS >= 200) |Kiszolgáló |
| Időtartomány a kiszolgálói és hálózati naplókban |#Timestamp >= 2014-10-20T16:36:38 és #Timestamp <= 2014-10-20T16:36:39 |Kiszolgáló, Hálózat |
| Időtartomány a kiszolgálói naplókban |AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 és AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 |Kiszolgáló |

## <a name="next-steps"></a>További lépések

Az Azure Storage-ban a végpontok között történő forgatókönyvek hibaelhárításáról az alábbi forrásokban talál további információt:

* [Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Tárfiók monitorozása az Azure Portalon](storage-monitor-storage-account.md)
* [Adatok átvitele az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [A Microsoft Message Analyzer üzemeltetési útmutatója](https://technet.microsoft.com/library/jj649776.aspx)
