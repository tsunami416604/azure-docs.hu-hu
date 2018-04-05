---
title: A diagnosztika & Message Analyzer Azure Storage hibaelhárítása |} Microsoft Docs
description: A végpont hibaelhárítás az Azure Storage Analytics, az AzCopy és a Microsoft Message Analyzert, amely tartalmazza az oktatóanyag
services: storage
documentationcenter: dotnet
author: tamram
manager: timlt
ms.assetid: 643372a3-1c07-4e88-b4ef-042512a43086
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.openlocfilehash: 324370ae18627a1985e6a40aec11ee2fa871e93b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Végpontok közötti hibaelhárítás, Azure Storage mérőszámainak és a naplózást, az AzCopy és a Message Analyzer segítségével
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnosztizálás és hibaelhárítási a kulcs szakértelem kialakításához, és a Microsoft Azure Storage ügyfél alkalmazásokat támogató. Az Azure-alkalmazások elosztott jellege miatt Diagnosztizálás és a hibák és a teljesítménnyel kapcsolatos problémák elhárítása lehet bonyolultabb, mint a hagyományos környezetekben.

Ebben az oktatóanyagban bemutatjuk, hogyan bizonyos hibák hatása a teljesítményre, és a végpont e hibák elhárítása a Microsoft és az Azure Storage által biztosított eszközök használ az ügyfélalkalmazás optimalizálása érdekében.

Ez az oktatóanyag egy végpontok közötti hibaelhárítási forgatókönyv gyakorlati feltárása biztosít. A részletes fogalmi útmutatót az Azure storage-alkalmazások hibáinak elhárításához, lásd: [figyelése, diagnosztizálása és elhárítása a Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Azure Storage alkalmazások hibaelhárítási eszközök
Microsoft Azure Storage használó ügyfélalkalmazások elhárításához eszközök kombinációja segítségével határozzák meg, amikor probléma történt, és a probléma oka lehet. Ezek az eszközök a következőket foglalják magukban:

* **Az Azure Storage Analytics**. [Az Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) metrikákat és naplózási az Azure Storage biztosít.
  
  * **Storage mérőszámainak** nyomon követi a tranzakció metrikák és a teljesítmény-mérőszámait a tárfiók. Használja a metrikákat, meghatározhatja, hogyan az alkalmazás számos különböző intézkedések megfelelően működik-e. Lásd: [Storage Analytics metrikák táblaséma](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) további információt a tárolási analitika követik metrikák típusú.
  * **Tárolási naplózási** kéréseknek jelentkezik, az Azure Storage szolgáltatás kiszolgálóoldali naplóba. A napló részletes adatok az egyes kérelmek, beleértve a végre műveletet, a művelet, és a késési adatok állapotának nyomon követi. Lásd: [Storage Analytics naplóformátumban](/rest/api/storageservices/Storage-Analytics-Log-Format) írja be a naplók tárolási analitika kérelem-válasz adatokról további információt.

* **Azure-portálon**. A tárfiók a metrikákat és naplózási konfigurálható a [Azure-portálon](https://portal.azure.com). Akkor is diagramok és hogyan működik az alkalmazás időbeli megjelenítő diagramok megtekintése, és beállíthatja a értesítéseket arról, ha az alkalmazás az adott mérőszám várt módon hajt végre.
  
    Lásd: [figyelése az Azure portálon tárfiók](storage-monitor-storage-account.md) figyelése az Azure portálon konfigurálásával kapcsolatos információkat.
* **AzCopy**. Az Azure Storage Server-naplók blobként, tárolják, az AzCopy segítségével másolja a naplóban blobok egy helyi használata a Microsoft Message Analyzert elemzés céljából. Lásd: [adatátvitel az AzCopy parancssori segédprogram a](storage-use-azcopy.md) AzCopy további információt.
* **Microsoft Message Analyzert**. Message Analyzer olyan eszköz, amely feldolgozó naplófájlokat, és naplóadatokat, amellyel könnyedén szűrő, a Keresés és a csoport adatainak naplózása, amelyek segítségével elemezheti a hibák és a teljesítménnyel kapcsolatos problémák hasznos be visual formátumban jeleníti meg. Lásd: [Microsoft Message Analyzer működő útmutató](http://technet.microsoft.com/library/jj649776.aspx) Message Analyzer további információt.

## <a name="about-the-sample-scenario"></a>A mintaforgatókönyv kapcsolatos
Ebben az oktatóanyagban egy olyan forgatókönyvet, ahol az Azure Storage mérőszámainak, egy alacsony százalékos sikerességi arányát az alkalmazás, amely behívja az Azure storage foglalkozunk. Az alacsony százalékos sikerességi aránya metrika (megjelennek az helyeként **PercentSuccess** a a [Azure-portálon](https://portal.azure.com) és a metrikák táblák) követi nyomon, hogy sikeres, de nagyobb 299 HTTP-állapotkódot vissza, amely operations. A tranzakció állapota a rögzített ezeket a műveleteket a kiszolgálóoldali tárolási naplófájlokban **ClientOtherErrors**. Az alacsony százalékos sikerességi metrika kapcsolatos további tudnivalókért lásd: [metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére állapotú tranzakció](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure Storage-műveleteket adhatnak vissza HTTP-állapotkódok 299 nagyobb normál működés részeként. De ezeket a hibákat, egyes esetekben azt jelzi, hogy esetleg az ügyfélalkalmazást, a jobb teljesítmény optimalizálása érdekében.

Ebben a forgatókönyvben egy alacsony százalékos sikerességi arányát kell minden, 100 % lesz javasolt. Választhat egy másik metrika szintet, azonban szükség szerint. Azt javasoljuk, hogy az alkalmazás tesztelése során kapcsolatot hoz létre egy alapkonfiguráció tolerancia az a fontos teljesítménymutatókat. Például dönthet úgy, tesztekre alapozva, az alkalmazás rendelkezik-e a 90 %, vagy a 85 % konzisztens százalékos sikerességi aránya. Ha a metrikai adatok azt mutatja, hogy az alkalmazás-, hogy több el az eltérő, akkor is vizsgálja meg, mi okozza a növelését.

Minta esetünkben után azt mutatja meg, hogy a százalékos sikerességi aránya a metrika nem éri el 100 %-os, azt fogja tanulmányozza az metrikáinak egyeztetéséhez, és segítségével mérje fel, hogy mi okozza a százalékos sikerességi aránya alacsonyabb a hibák találhatók. Megnézzük, pontosabban, a hibák 400 tartományban. Ezután lesz jobban felülvizsgáljuk 404-es (nem található) hibák.

### <a name="some-causes-of-400-range-errors"></a>Tartományon kívüli 400 hibák oka
Az alábbi példák a kéréseket a meghatározott Azure Blob Storage és a lehetséges okok 400-tartomány hibák példák láthatók. A hibákat, valamint a a 300 és 500 tartományban, a hibák bármelyikét hozzájárulhat egy alacsony százalékos sikerességi arányát.

Vegye figyelembe, hogy az alábbi listák messze befejeződött. Lásd: [állapotát és hibakódok](http://msdn.microsoft.com/library/azure/dd179382.aspx) az MSDN-en és a tárolási szolgáltatások vonatkozó hibákkal kapcsolatos általános Azure Storage-hibákkal kapcsolatos részleteket.

**Állapot 404-es (nem található) kódpéldák**

Akkor következik be, amikor egy tárolót vagy blobot egy olvasási művelet sikertelen lesz, mivel a blobot, vagy a tároló nem található.

* Akkor következik be, ha egy tárolót vagy blobot törölte egy másik ügyfélszámítógépen, mielőtt ezt a kérelmet.
* Akkor következik be, miután ellenőrizte, hogy létezik-e a tárolót vagy blobot létrehozó API-hívás használata. A CreateIfNotExists API-k hívás HEAD először a tárolót vagy blobot; meglétének ellenőrzése Ha még nem létezik, a 404-es hibát ad vissza, és majd a második PUT kérés érkezett a tárolót vagy blobot írni.

**Állapot-kódpéldák 409 (Ütközés)**

* Akkor következik be, ha egy létrehozása API használatával hozzon létre egy új tárolót vagy blobot, először az meglétének ellenőrzése nélkül, és egy tárolót vagy blobot ezzel a névvel már létezik.
* Akkor következik be, ha a tároló törlése folyamatban van, és hozzon létre egy új tároló ugyanazzal a névvel, a törlési művelet befejezése előtt megkísérli.
* Akkor következik be, ha egy tárolót vagy blobot meg kell adnia egy bérletet, és már létezik a jelenlegi címbérlet.

**412 (előfeltétel) állapotkód példák**

* Akkor következik be, ha egy feltételes fejlécet által megadott feltétel nem teljesül.
* Akkor fordul elő, ha a címbérlet-azonosító van megadva nem felel meg a tárolót vagy blobot bérleti Azonosítójával.

## <a name="generate-log-files-for-analysis"></a>Naplófájlokat az elemzéshez készítése
Ebben az oktatóanyagban használjuk Üzenetelemző használata a naplófájlokat, három különböző típusú bár ezek bármelyike használható kiválaszthatják:

* A **kiszolgálónapló**, ami akkor jön létre, amikor az Azure Storage naplózását. A kiszolgáló naplóját egyes egy Azure Storage szolgáltatás - blob, a várólista, a tábla és a fájl hívást műveletekre vonatkozó adatokat tartalmaz. A kiszolgáló naplóját azt jelzi, mely művelet lett meghívva, és milyen állapotkódja: a kérés- és visszaadott, valamint egyéb adatait.
* A **.NET ügyfélnapló**, ami akkor jön létre, amikor a .NET-alkalmazásból engedélyezi az ügyféloldali naplózás. Az ügyfél talál részletes információkat arról, hogyan az ügyfél előkészíti a kérelmet, és fogadja és dolgozza fel a választ tartalmaz.
* A **HTTP hálózati nyomkövetési napló**, amely adatokat gyűjt a HTTP/HTTPS-kérelem-válasz adatokat, beleértve az Azure Storage műveleteket. Az oktatóanyag azt fogja hozza létre a hálózati nyomkövetést Message Analyzer keresztül.

### <a name="configure-server-side-logging-and-metrics"></a>Metrikák és kiszolgálóoldali naplózásának beállítása
Először azt kell metrikákat, és az Azure Storage naplózásának beállítása, hogy az ügyfélalkalmazás elemzendő adatokat kell. Konfigurálható naplózása és az sokféleképpen - metrikák a [Azure-portálon](https://portal.azure.com), powershellel, vagy programon keresztül. Lásd: [Storage mérőszámainak engedélyezése és megtekintése metrikai adatok](http://msdn.microsoft.com/library/azure/dn782843.aspx) és [tárolási naplózás engedélyezése és használata naplóadatok](http://msdn.microsoft.com/library/azure/dn782840.aspx) MSDN naplózás és a metrikák konfigurálásával kapcsolatos részletekért.

**Az Azure-portálon**

Konfigurálja és naplózás és a metrikák a tárolási fiók használatával a [Azure-portálon](https://portal.azure.com), kövesse [figyelése az Azure portálon tárfiók](storage-monitor-storage-account.md).

> [!NOTE]
> Nincs lehetőség beállítása az Azure portál használatával percenkénti metrikákat. Azt javasoljuk azonban, hogy meg őket az oktatóanyag céljából, és az alkalmazás teljesítményproblémák vizsgálata. Percenkénti metrikákat alább látható módon PowerShell használatával, vagy programozott módon, a storage ügyféloldali kódtár állíthatja be.
> 
> Vegye figyelembe, hogy az Azure-portál nem tudja megjeleníteni percenkénti metrikákat, csak óránkénti metrikákat.
> 
> 

**Via PowerShell**

Ismerkedés az Azure PowerShell használatával, lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

1. Használja a [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0) parancsmag segítségével adjon hozzá a PowerShell-ablakban Azure felhasználói fiókja:
   
    ```powershell
    Add-AzureAccount
    ```

2. Az a **jelentkezzen be a Microsoft Azure** ablak, írja be az e-mail címet és -fiókjához tartozó jelszót. Az Azure hitelesíti és menti a hitelesítő adatokat, majd bezárja az ablakot.
3. Állítsa be az alapértelmezett tárfiók a tárfiókot, az oktatóanyag használja a következő parancsok futtatásával a PowerShell-ablakban:
   
    ```powershell
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. A Blob szolgáltatás tárolási naplózásának engedélyezéséről:
   
    ```powershell
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```

5. A Blob szolgáltatás, és állítson be storage mérőszámainak engedélyezése **- MetricsType** való `Minute`:
   
    ```powershell
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>.NET ügyféloldali naplózás konfigurálása
.NET-alkalmazás ügyféloldali naplózási engedélyezze .NET diagnosztika a az alkalmazás konfigurációs fájljában (a web.config vagy az App.config fájlt). Lásd: [ügyféloldali naplózás a .NET a Storage ügyféloldali kódtára](http://msdn.microsoft.com/library/azure/dn782839.aspx) és [ügyféloldali naplózás a Microsoft Azure Storage SDK for Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) az MSDN Webhelyén, a részletekért.

Az ügyféloldali napló részletes információkat arról, hogyan az ügyfél előkészíti a kérelmet, és fogadja és dolgozza fel a választ tartalmaz.

A Storage ügyféloldali kódtár ügyféloldali naplóadatokat az alkalmazás konfigurációs fájljában (a web.config vagy az App.config fájlt) a megadott helyen tárolja.

### <a name="collect-a-network-trace"></a>A hálózati nyomkövetés gyűjtése
Az Üzenetelemző használhatja egy HTTP/HTTPS hálózati nyomkövetés gyűjtéséhez az ügyfélalkalmazás futtatása közben. Üzenet-elemző eszköz által használt [Fiddler](http://www.telerik.com/fiddler) a háttérben. A hálózati nyomkövetés gyűjt, mielőtt azt javasoljuk, hogy konfigurálja a titkosított HTTPS-forgalom rögzítése a Fiddler:

1. Telepítés [Fiddler](http://www.telerik.com/download/fiddler).
2. Indítsa el a Fiddler.
3. Válassza ki **eszközök |} Fiddler beállítások**.
4. A beállítások párbeszédpanelen győződjön meg arról, hogy **rögzítése HTTPS kapcsolódik** és **a HTTPS-forgalmon visszafejtés** mind van jelölve, alább látható módon.

![Fiddler beállítások konfigurálása](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Az oktatóanyag gyűjtése egy hálózati nyomkövetést először mentse a Message Analyzer, majd a nyomkövetés és a naplók elemzése analysis-munkamenetet létrehozni. Egy hálózati nyomkövetést, a Message Analyzer gyűjtéséhez:

1. A Message Analyzer, válassza ki **fájl |} Gyors nyomkövetési |} Titkosítás nélkül HTTPS**.
2. A nyomkövetés, ezért azonnal megkezdődik. Válassza ki **leállítása** nyomkövetés leállítására, így konfigurálhatjuk nyomkövetési tárolási forgalom.
3. Válassza ki **szerkesztése** szerkesztése a nyomkövetési munkamenet.
4. Válassza ki a **konfigurálása** jobbra mutató hivatkozás a **Microsoft-Pef-WebProxy** ETW-szolgáltató.
5. Az a **speciális beállítások** párbeszédpanel, kattintson a **szolgáltató** fülre.
6. Az a **állomásnév szűrő** mezőben adja meg a tároló-végpontokat, választják el egymástól. Például megadhatja a végpontokat az alábbiak szerint; Módosítsa `storagesample` a tárfiók nevét:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. A párbeszédpanel bezárásához, majd kattintson az **indítsa újra a** megkezdéséhez a gyűjtése a nyomkövetési helyen, az állomásnév szűrővel, hogy csak Azure tárolási hálózati forgalom szerepel a nyomkövetésben.

> [!NOTE]
> Miután végzett a hálózati nyomkövetés gyűjtése, javasoljuk, hogy a beállításokat, előfordulhat, hogy módosította a Fiddler a HTTPS-forgalmon visszafejtés vissza. A Fiddler beállítások párbeszédpanelen törölje a **rögzítése HTTPS kapcsolódik** és **a HTTPS-forgalmon visszafejtés** jelölőnégyzeteket.
> 
> 

Lásd: [használja a hálózati nyomkövetés funkciókat](http://technet.microsoft.com/library/jj674819.aspx) a TechNet webhelyen olvashat.

## <a name="review-metrics-data-in-the-azure-portal"></a>Tekintse át a metrikai adatok az Azure-portálon
Miután az alkalmazás egy ideig futott, a megjelenő metrikák diagramok tekintse át a [Azure-portálon](https://portal.azure.com) és figyelje meg, hogyan van lett végrehajtása a szolgáltatás.

Első lépésként nyissa meg a storage-fiókot az Azure-portálon. Alapértelmezés szerint a figyelő a diagramon a **sikeres arány** metrika jelenik meg a fiók panelen. Ha módosította a diagram megjelenítése különböző metrikák korábban, vegye fel a **sikeres arány** metrikát.

Most láthatja **sikeres arány** , a figyelés diagramra bármely más metrikákkal együtt előfordulhat, hogy felvette. A forgatókönyvben lesz felülvizsgáljuk mellett a naplófájlok az Üzenetelemző elemzésével százalékos sikerességi arányát némileg alábbi 100 %.

Hozzáadása és metrikákat diagramok testreszabása a további részletekért lásd: [metrikák diagramok testreszabása](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Ez eltarthat egy ideig a metrikák adatok storage mérőszámainak engedélyezése után az Azure-portálon jelenik meg. Ennek az az oka az előző órán óránkénti metrikáját nem jelenik meg az Azure portálon mindaddig, amíg a jelenlegi óra telt el. Emellett percenkénti metrikákat jelenleg nem láthatók az Azure portálon. Ezért attól függően, ha engedélyezi a metrikákat, szükség lehet legfeljebb kétórás metrikai adatok megjelenítéséhez.
> 
> 

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Kiszolgáló naplókat másolja egy helyi az AzCopy segítségével
Az Azure Storage blobs, írja server naplóadatokat, amikor a metrikák táblák írja a rendszer. Napló blobok érhetők el a jól ismert `$logs` tároló a tárfiók. Napló blobok neve hierarchikusan év, hónap, nap és óra, így könnyen elérhetők a vizsgálni kívánt időtartományt. Például a `storagesample` fiók, a napló 01 02 2015 / /, reggel 8 – 9, a blobokat tárolójának `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Ebben a tárolóban lévő egyes blobok megnevezett egymás után, kezdve `000000.log`.

Az AzCopy parancssori eszköz segítségével töltse le a kiszolgálóoldali naplófájlok az Ön által választott helyre a helyi számítógépen. Például használhatja a következő parancsot a blob műveletek történt 2015. január 2. azt a mappát a naplófájlokban letöltése `C:\Temp\Logs\Server`; cserélje le `<storageaccountname>` a tárfiók nevével és `<storageaccountkey>` rendelkező a hívóbetűre:

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy letölthető a rendszer a [Azure letölti](https://azure.microsoft.com/downloads/) lap. AzCopy használatával kapcsolatos részletekért lásd: [adatátvitel az AzCopy parancssori segédprogram a](storage-use-azcopy.md).

További információ a kiszolgálóoldali naplók letöltése: [naplózási adatokat tároló naplózás letöltése](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Microsoft Message Analyzert használatával elemezheti a naplóadatok
Microsoft Message Analyzert egy olyan eszköz, a rögzítés, megjelenítését és elemzése a forgalmat, eseményeket és hibaelhárítási és diagnosztikai forgatókönyvek egyéb rendszer vagy alkalmazás üzeneteket üzenetküldési protokoll. Az Üzenetelemző is lehetővé teszi betöltése, összesített és elemezhetik a naplóból, és nyomkövetési fájlok mentése. Az Üzenetelemző kapcsolatos további információkért lásd: [Microsoft Message Analyzer működő útmutató](http://technet.microsoft.com/library/jj649776.aspx).

Az Üzenetelemző eszközök, amelyek segítenek a kiszolgáló, az ügyfél és a hálózati naplók elemzése Azure Storage magában foglalja. Ebben a szakaszban mutatjuk be ezekhez az eszközökhöz használata alacsony százalékos siker a tárolási naplókat a probléma megoldására.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Töltse le és telepítse a Message Analyzer és az Azure Storage-eszközök
1. Töltse le [Üzenetelemző](http://www.microsoft.com/download/details.aspx?id=44226) a Microsoft letöltőközpontból, és futtassa a telepítőt.
2. Indítsa el a Message Analyzer.
3. Az a **eszközök** menü **Eszközkezelő**. Az a **Eszközkezelő** párbeszédablakban válassza **letölti**, majd szűrést végezni **Azure Storage**. Az Azure Storage-eszközök, az alábbi képen látható módon jelenik meg.
4. Kattintson a **szinkronizálási összes megjelenített elemek** az Azure Storage-eszközök telepítése. A rendelkezésre álló eszközökre a következők:
   * **Az Azure Storage szín szabályok:** az Azure Storage szín szabályok lehetővé teszik a speciális szűrők használó színét, a szöveg és a betűtípus stílusok üzeneteket, amelyek tartalmazzák a nyomkövetési szereplő konkrét információk kiemeléséhez.
   * **Az Azure Storage diagramok:** az Azure Storage diagram olyan előre definiált diagramok, amelyek a kiszolgáló naplóadatokat diagramot. Vegye figyelembe, hogy most Azure Storage diagramok használatához, előfordulhat, hogy csak akkor kell betölteni a kiszolgáló naplóját a elemzés rácsba.
   * **Az Azure Storage elemzők:** az Azure Storage elemzők elemzése az Azure Storage-ügyfél, a kiszolgáló és a HTTP naplók elemzése rácsban megjelenítéséhez.
   * **Az Azure Storage-szűrők:** az Azure Storage szűrők a következők előre meghatározott feltételek, amelyek segítségével az adatok elemzése rácsban lekérdezése.
   * **Az Azure Storage nézet elrendezések:** az Azure Storage nézet elrendezések előre definiált oszlop elrendezések és elemzési rácsban csoportból.
5. Indítsa újra a Message Analyzer, az eszközök telepítése után.

![Message Analyzer Eszközkezelő](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Ez az oktatóanyag céljából Azure Storage eszközök teljes telepítése.
> 
> 

### <a name="import-your-log-files-into-message-analyzer"></a>A naplófájlok importálása Message Analyzer
Importálhatja a mentett naplófájlok (kiszolgálóoldali ügyféloldali és hálózati), az összes Microsoft Message Analyzert egyetlen munkamenetében elemzés céljából.

1. Az a **fájl** Microsoft Message Analyzert, menüjében kattintson **új munkamenet**, és kattintson a **üres munkamenet**. Az a **új munkamenet** párbeszédpanelen adja meg az elemzési munkamenet nevét. Az a **munkamenet részletei** panelen, kattintson a **fájlok** gombra.
2. Az Üzenetelemző által generált hálózati nyomkövetési adatok betöltéséhez kattintson a **fájlok hozzáadása**, keresse meg a helyet, ahová mentette a .matp fájl a webes nyomkövetési munkamenet, válassza ki a .matp fájlt, és kattintson a **nyitott**.
3. A kiszolgálóoldali naplóadatokat betöltéséhez kattintson a **fájlok hozzáadása**, keresse meg a helyet, amelybe letöltötte a kiszolgálóoldali naplókhoz, válassza ki a naplófájlokat a elemzéséhez, és kattintson a kívánt időtartományt **nyitott**. Ezt követően a a **munkamenet részletei** panelen, állítsa be a **szöveges napló konfigurációs** legördülő minden kiszolgálóoldali naplófájl **AzureStorageLog** annak érdekében, hogy a Microsoft Message Analyzert tudja megfelelően értelmezni a naplófájlba írást.
4. Az ügyféloldali napló adatok betöltéséhez kattintson a **fájlok hozzáadása**, keresse meg a helyet, ahová menteni szeretné az ügyféloldali naplók, válassza ki a naplófájlokat, elemzése, és kattintson a kívánt **nyitott**. Ezt követően a a **munkamenet részletei** panelen, állítsa be a **szöveges napló konfigurációs** legördülő lista az egyes ügyféloldali naplófájlok **AzureStorageClientDotNetV4** annak érdekében, hogy a Microsoft Message Analyzert tudja megfelelően értelmezni a naplófájlba írást.
5. Kattintson a **Start** a a **új munkamenet** betölteni és elemezni a naplóadatok párbeszédpanel. A naplózási adatok jelennek meg a Message Analyzer elemzés rácsban.

A következő ábrán egy példa munkamenet kiszolgáló, ügyfél és hálózati nyomkövetési naplófájlok konfigurálni.

![Message Analyzer munkamenet konfigurálása](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Vegye figyelembe, hogy a Message Analyzer naplófájlok betölti a memóriába. Ha a napló nagy adatkészletet, érdemes ahhoz, hogy a lehető legjobb teljesítményt lekérni a Message Analyzer szűréséhez.

Először határozza meg az időkeret érdekli áttekintése, valamint ezen az időn lehető legkisebb tartását. Sok esetben érdemes perc vagy óra legfeljebb egy adott időn át. Importálja a lehető legkisebb jogosultságkészletet naplókat, amely megfelel az igényeinek.

Ha továbbra is nagy mennyiségű naplóadatokat, majd érdemes lehet a naplóadatok előtt töltse be a munkamenet szűrőt adjon meg. Az a **munkamenet szűrő** mezőben válassza a **könyvtár** gombra kattintva válasszon egy előre meghatározott szűrőt; például **globális idő szűrő i.** az az Azure Storage-szűrők szűrést végezni egy adott időintervallumban. Szerkesztheti a szűrési feltételeket adhatja meg a kezdési és befejezési időbélyeg meg szeretné tekinteni az az időtartam alatt. Akkor is végezhet egy adott állapotkód; például ha szeretné, csak az állapotkód: 404 esetén naplóbejegyzések betölteni.

Naplóadatok Microsoft Message Analyzert történő importálásával kapcsolatos további információkért lásd: [üzenet-adatok beolvasása](http://technet.microsoft.com/library/dn772437.aspx) a TechNet webhelyen.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Az ügyfél-azonosító segítségével összefüggéseket naplófájl adatai
Az Azure Storage ügyféloldali kódtár automatikusan hoz létre minden egyes kérés egyedi ügyfélkérelem-azonosító. Ez az érték az az ügyfél talál, a kiszolgáló naplóját és a hálózati nyomkövetés írni, hogy használhassa adatai közötti összefüggések keresésére Message Analyzer belül minden három naplók között. Lásd: [ügyfélkérelem-azonosító](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) további információt az ügyfél kérelem az azonosítója.

Az alábbi szakaszok azt ismertetik, hogyan előre konfigurált és egyéni nézetek segítségével összefüggéseket és replikációscsoport-adatok alapján az ügyfél kérelem-azonosítót.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Az elemzés rácsban megjelenítendő nézet elrendezés kiválasztása
A Message Analyzer tárolási eszközök közé tartozik a Azure tárolási nézet elrendezést, amelyek előre konfigurált nézeteket, amelyek segítségével az adatok hasznos Csoportosítások és a különböző alkalmazási helyzetek oszlopok megjelenítéséhez. Hozhat létre egyéni nézet elrendezést, és mentse őket ismételt felhasználásra.

Az alábbi kép a **nézet elrendezését** menü kiválasztásával elérhető **nézet elrendezését** az eszköztár szalagon. A nézet elrendezések az Azure Storage alatt találhatók a **Azure Storage** csomópont a menüben. Kereshet `Azure Storage` be a keresőmezőbe az Azure Storage szűrése elrendezések csak megtekinthetők. Igény szerint kiválaszthatja a csillag kedvenc legyen, és megjeleníti azt az a menü felső nézet elrendezésének mellett.

![Nézet elrendezését menü](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Először válasszon **ClientRequestID és modul szerint csoportosítva**. A nézet elrendezését csoportok naplózni három naplókból először ügyfélkérelem-azonosító, azután a forrás naplófájl (vagy **modul** az Üzenetelemző). Ez a nézet a részletekbe menően tárhatják fel adott ügyfélkérelem-azonosító, és adott ügyfél kérelem összes három naplófájl adatai azonosítóját.

Az alábbi kép az elrendezés nézetben a napló mintaadatok, megjelenített oszlopok egy részét alkalmazza. Láthatja, hogy az adott ügyfélkérelem-azonosító, a elemzés rács adatait jeleníti meg az ügyfél napló, a kiszolgáló naplóját és a hálózati nyomkövetés.

![Az Azure Storage nézet elrendezés](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Különböző naplófájlokat rendelkezik a különböző oszlopokban, így megjelenésekor több naplófájl adatait a elemzés rácsban, néhány oszlop nem tartalmazhat egy adott sor adatokat. Például a fenti kép, az ügyfél napló sorok ne jelenjen meg az adatokat a **időbélyeg**, **TimeElapsed**, **forrás**, és **cél** oszlopok, mivel ezek az oszlopok nem szerepelnek az ügyfél talál, de a hálózati nyomkövetés szerepel. Hasonlóképpen a **időbélyeg** oszlop Timestamp típusú adatait a kiszolgáló naplóját jeleníti meg, azonban adatot nem jelenik meg a **TimeElapsed**, **forrás**, és **cél** oszlopokat, amelyek nem részei a kiszolgáló naplóját.
> 
> 

Mellett az Azure Storage nézet elrendezés, is határozza meg, és mentse a saját nézet elrendezés. Más kívánt Mezőválasztás adatok csoportosításához, és mentse a csoportosítás, valamint az egyéni elrendezés részeként.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Az elemzés rács színét szabályok alkalmazása
A tárolási eszközök is szín szabályok, amelyek ajánlat a visual azt jelenti, hogy különböző típusú hibák elemzés rácsban azonosításához. Előre definiált színt szabályok vonatkoznak a HTTP-hibák, így csak a kiszolgáló naplóját és a hálózati nyomkövetés jelennek meg.

Szín szabályok jelölje **szín szabályok** az eszköztár szalagon. Látni fogja az Azure Storage szín szabályok a menüben. Válassza ki az oktatóanyagban **ügyfél hibákat (StatusCode 400 és 499)**, az alábbi képen látható módon.

![Az Azure Storage nézet elrendezés](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Az Azure Storage szín szabályok használata mellett is határozza meg és saját szín szabályok mentése.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Csoport és a szűrő naplóadatokat 400-tartomány hibák kereséséhez
Lesz a következő azt csoportnak, és szűrje a napló adatokat az összes hiba található a 400 közé.

1. Keresse meg a **StatusCode** oszlop elemzés rácsban, kattintson a jobb gombbal a címsor, és válassza ki az oszlop **csoport**.
2. A következő csoport a **ClientRequestId** oszlop. Látni fogja, hogy az Analysis rácsban most elrendezését állapotkód és ügyfél-kérelem azonosítót.
3. A nézet szűrő eszköz ablak megjelenítése, ha azt már nem jelenik meg. Válassza ki az eszköztár menüszalagon **eszköz Windows**, majd **nézet szűrő**.
4. A napló csak a 400-tartomány hibák megjelenítendő adatok szűrése, adja hozzá a következő szűrési feltételeket a **nézet szűrő** ablakot, és kattintson **alkalmaz**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

Az alábbi képen a csoportosítás és a szűrő eredményeit jeleníti meg. Bővíteni a **ClientRequestID** mező alatt a csoportosítás status kód 409, például jeleníti meg, amelyek adott állapotkód: egy művelet.

![Az Azure Storage nézet elrendezés](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Ez a szűrő alkalmazását követően láthatja, hogy az ügyfél naplófájl azon sorait nem tartoznak, az ügyféllel a napló nem tartalmaz egy **StatusCode** oszlop. Először igazolnia áttekinti a kiszolgáló és a hálózati nyomkövetési naplók 404-es hibák kereséséhez, és majd még visszatérünk vizsgálja meg az ügyfél műveleteivel őket vezető ügyfél napló.

> [!NOTE]
> Végezhet a **StatusCode** oszlop és továbbra is a megjelenített adatok három naplókat, beleértve az ügyfélszámítógép naplóját, ha a kifejezés hozzáadni a szűrőt, amely tartalmazza a naplóbejegyzéseket, ahol az állapotkód értéke null. A szűrőkifejezés létrehozásához használja:
> 
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
> 
> Ez a szűrő sorát adja vissza minden az ügyfél naplóját, és csak a kiszolgáló naplóját és a HTTP-napló sorát az állapotkód: nagyobb, mint a 400 esetén. Ha a nézet elrendezését csoportosítva ügyfélkérelem-azonosító és a modul telepítését, keressen, vagy görgessen keresztül a naplóbejegyzések található azokat, ahol minden három naplók vannak megadva.   
> 
> 

### <a name="filter-log-data-to-find-404-errors"></a>Szűrő naplóadatokat 404-es hibák kereséséhez
A tárolási eszközök szűkítheti a naplóadatok találja a hibák vagy a keresett trendek segítségével előre definiált szűrőktől tartalmazzák. A következő fog érvénybe lépni két előre definiált szűrők: azt, amelyik a szűrők a kiszolgáló és a hálózati nyomkövetési naplók a 404-es hibákat, és azt, amelyik a megadott időtartomány lévő adatok szűrése.

1. A nézet szűrő eszköz ablak megjelenítése, ha azt már nem jelenik meg. Válassza ki az eszköztár menüszalagon **eszköz Windows**, majd **nézet szűrő**.
2. Válassza a nézet szűrő ablak **könyvtár**, és a keresési `Azure Storage` található az Azure Storage-szűrők. Válassza ki a szűrő **404-es (nem található) összes napló állapotüzenetek**.
3. Megjelenítési a **könyvtár** menü újra, és keresse meg és jelölje ki a **globális Time Filter**.
4. A megtekinteni kívánt tartományt a szűrő látható időbélyegeket szerkesztése. Ez segít az adatok elemzéséhez tartományán szűkítheti.
5. A szűrő az alábbi példához hasonlóan kell megjelennie. Kattintson a **alkalmaz** a szűrő alkalmazása az elemzés rács.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Az Azure Storage nézet elrendezés](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>A naplózási adatok elemzése
Csoportosítva, és az adatok szűrt, láthatja az egyes kérelmeket a 404-es kapcsolatban hibát okozó részleteit. Az aktuális nézet elrendezését az adatok ügyfélkérelem-azonosító, majd a napló forrás által szerint vannak csoportosítva. Mivel azt jelenleg korlátozza a kérelemhez ahol a StatusCode mezőben 404, megtanulhatja, csak a kiszolgáló és a hálózati nyomkövetési adatok, nem az ügyfél naplóadatokat.

A következő ábrán látható egy adott kérelem, amelyben a Blob beolvasása a művelet eredményezte-e a 404-es, mert a blob nem létezik. Vegye figyelembe, hogy néhány oszlop el lettek távolítva a szabványos nézet ahhoz, hogy a megfelelő adatok jelennek meg.

![Szűrt kiszolgálói és hálózati nyomkövetési naplók](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Ezután azt fogja függ a ügyfélkérelem-azonosító az ügyfél naplóadatokat, hogy milyen műveletek, az ügyfél lett véve, ha a hiba történt a. Új elemzési nézetet az ehhez a munkamenethez, a napló ügyféladatok megtekintésére, amely megnyit egy második lapján jelenítheti meg:

1. Első lépésként másolja az értékét a **ClientRequestId** mezőjét a vágólapra. Ehhez vagy sor kijelölés, megkeresi a **ClientRequestId** mező, kattintson a jobb gombbal az adatérték a, és válassza **másolási "ClientRequestId"**.
2. Az eszköztár menüszalagon válassza **új Viewer**, majd jelölje be **elemzés rács** új lap megnyitásához. Az új lapja mutatja a naplófájlok nem csoportosításához, szűréséhez, illetve a szín szabályok összes adatot.
3. Az eszköztár menüszalagon válassza **nézet elrendezését**, majd jelölje be **minden .NET ügyfél oszlopok** alatt a **Azure Storage** szakasz. A nézet elrendezését, az ügyfél adatainak megjelenítése, napló, valamint a kiszolgáló és a hálózat nyomkövetési naplóit. Alapértelmezés szerint a rendszer rendezi a **MessageNumber** oszlop.
4. A következő keresése az ügyfél talál az ügyfél kérelem-azonosítót. Válassza ki az eszköztár menüszalagon **található üzenetek**, majd adjon meg egy egyéni szűrő a ügyfélkérelem-azonosító található a a **található** mező. Ezt a szintaxist használja a szűrőt, a saját ügyfélkérelem-azonosító megadása:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Az Üzenetelemző talál, és kiválasztja az első naplóbejegyzés, ahol a keresési feltételeknek megfelelő ügyfél kérelem-azonosítót. Az ügyfélszámítógép naplóját nincsenek minden ügyfélkérelem-azonosító, több bejegyzést, érdemes lehet csoportosítani őket a a **ClientRequestId** könnyebb együtt lásd: a mező. A következő ábrán látható összes üzenetet az ügyfél tartalmaz a megadott ügyfél kérelem azonosítóját.

![Ügyfél naplófájl-megjelenítő 404-es hibák](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

A nézet elrendezések ezen két lapok megjelenő adatok használatával, a kérelem adatainak annak meghatározásához, hogy a hibát okozta mi elemezheti. Is megtalálhatja kérelmek megelőző ezt a telepítést, ha egy előző esemény vezettek előfordulhat, hogy a 404-es hiba megtekintéséhez. Például tekintheti meg az ügyfél naplóbejegyzések megelőző az ügyfél-Azonosítót határozza meg, hogy a blob törölték, vagy a következő hiba történt az ügyfélalkalmazás egy tárolót vagy blobot egy CreateIfNotExists API hívása miatt. Az ügyfél naplóban található a blob címet a **leírása** mezőben; a kiszolgáló és a hálózati nyomkövetési naplók, ez az információ jelenik meg a **összegzése** mező.

Miután eldöntötte, hogy a cím, amely a 404-es hibát eredményezte BLOB, használatával megvizsgálhatja a további. Ha a naplóbejegyzések más műveleteket végez a azonos blob társított üzenetek, ellenőrizheti, hogy az ügyfél korábban törölték az entitást.

## <a name="analyze-other-types-of-storage-errors"></a>Más típusú tárolási hibák elemzése
Most, hogy ismeri a Message Analyzer segítségével elemezheti a naplózási adatokat, más típusú hibák nézet használatával elemezheti elrendezések, a színt szabályok és a keresés/szűrés. Az alábbi táblázatok egyes esetlegesen fellépő problémák és a keresési feltételek használatával keresse meg őket sorolja fel. A szűrők és a szűrés nyelvi Message Analyzer további információkért lásd: [szűrés állapotüzenet-adatokat](http://technet.microsoft.com/library/jj819365.aspx).

| Vizsgálja meg... | Használja a szűrőkifejezés... | Napló kifejezés vonatkozik (ügyfél, a kiszolgáló, a hálózat, az összes) |
| --- | --- | --- |
| Az üzenet kézbesítési a várólista nem várt késedelmeket |AzureStorageClientDotNetV4.Description tartalmaz "Újrapróbálkozás sikertelen műveletet." |Ügyfél |
| PercentThrottlingError HTTP növekedése |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Network (Hálózat) |
| PercentTimeoutError növekedése |HTTP.Response.StatusCode   == 500 |Network (Hálózat) |
| Az (all) PercentTimeoutError növelése |* StatusCode == 500 |Összes |
| PercentNetworkError növekedése |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Ügyfél |
| A HTTP 403-as (tiltott) üzenetek |HTTP.Response.StatusCode   == 403 |Network (Hálózat) |
| HTTP 404-es (nem található) üzenetek |HTTP.Response.StatusCode   == 404 |Network (Hálózat) |
| 404 (all) |* StatusCode == 404 |Összes |
| Közös hozzáférésű Jogosultságkód (SAS) hitelesítési hiba |AzureStorageLog.RequestStatus ==  "SASAuthorizationError" |Network (Hálózat) |
| A HTTP 409 (Ütközés) üzenetek |HTTP.Response.StatusCode   == 409 |Network (Hálózat) |
| 409 (összes) |* StatusCode == 409 |Összes |
| Alacsony PercentSuccess vagy analytics naplóbejegyzés rendelkezik ClientOtherErrors működésére a tranzakció állapota |AzureStorageLog.RequestStatus == "ClientOtherError" |Kiszolgáló |
| Nagle figyelmeztetés |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5-ös)) és (AzureStorageLog.RequestPacketSize < 1460) és (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Kiszolgáló |
| A kiszolgáló és a hálózat naplókban időtartományt |#Timestamp > = 2014-10-20T16:36:38 és #Timestamp < = 2014-10-20T16:36:39 |Server, Network |
| A kiszolgáló naplóiban időtartományt |AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 és AzureStorageLog.Timestamp < = 2014-10-20T16:36:39 |Kiszolgáló |

## <a name="next-steps"></a>További lépések
Az Azure Storage hibaelhárítási végpont forgatókönyvekkel kapcsolatos további információkért lásd: ezeket az erőforrásokat:

* [Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](http://msdn.microsoft.com/library/azure/hh343270.aspx)
* [A figyelő egy tárfiókot, Azure-portálon](storage-monitor-storage-account.md)
* [Adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [Microsoft Message Analyzer üzemeltetési útmutató](http://technet.microsoft.com/library/jj649776.aspx)
