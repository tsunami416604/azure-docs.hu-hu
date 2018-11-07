---
title: Azure Storage hibaelhárításáról diagnosztika és a Message Analyzer |} A Microsoft Docs
description: Egy teljes körű hibaelhárítás az Azure Storage Analytics, az AzCopy és a Microsoft Message Analyzert bemutatásához oktatóanyag
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.component: common
ms.openlocfilehash: cf183b0a78ff3f7e442ea8052f37fc2df58aac54
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262318"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Teljes körű hibaelhárítás az Azure Storage-mérőszámok és a naplózás, az AzCopy és a Message Analyzer használatával
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Megállapítása és elhárítása meglehetősen egy fő szakértelem létrehozásához és a Microsoft Azure Storage-ügyfélalkalmazások támogatásához. Azure-alkalmazások elosztott jellege miatt és hibák és a teljesítménnyel kapcsolatos problémák elhárítása meglehetősen lehet bonyolultabb, mint a hagyományos környezetekben.

Ebben az oktatóanyagban bemutatjuk, hogyan azonosíthatja az egyes hibák hatása a teljesítményre, és ezeket a hibákat a teljes körű hibaelhárítása a Microsoft és az Azure Storage által biztosított eszközök használatával annak érdekében, hogy az ügyfélalkalmazás optimalizálása.

Ez az oktatóanyag ismerteti a gyakorlati feltárása, egy teljes körű hibaelhárítási forgatókönyvben. A részletes fogalmi útmutatót a hibaelhárítási az Azure storage-alkalmazások, lásd: [figyelése, diagnosztizálása és hibaelhárítása a Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Az Azure Storage-alkalmazások hibaelhárítási eszközei
Az ügyfélalkalmazások számára a Microsoft Azure Storage használatával kapcsolatos hibaelhárítás eszközök kombinációja segítségével határozza meg, amikor probléma történt, és a probléma oka lehet. Ezek az eszközök a következőket foglalják magukban:

* **Az Azure Storage Analytics**. [Az Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) metrikák és naplózás az Azure Storage biztosít.
  
  * **Storage-mérőszámok** nyomon követi a tranzakciók mérőszámait és a tárfiók a kapacitási mérőszámot. Mérőszámok segítségével meghatározhatja, hogy az alkalmazás különböző mértékét számos megfelelően működnek-e. Lásd: [Storage Analytics mérőszámainak Táblasémáját](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) metrikák, a Storage Analytics által nyomon követett típusaival kapcsolatos további információt.
  * **A Storage naplózási** kiszolgálóoldali naplóba az Azure Storage szolgáltatás minden kérelmet naplózhatja. A napló részletes adatok az egyes kérelmek, többek között a műveletet végrehajtani a műveletet, és késési adatokkal állapotát követi nyomon. Lásd: [Storage Analytics naplóformátum](/rest/api/storageservices/Storage-Analytics-Log-Format) a kérelmek és válaszok adatait a Storage Analytics által a naplókba írt további információt.

* **Az Azure portal**. A tárfiók a metrikák és naplózás konfigurálhatja a [az Azure portal](https://portal.azure.com). Megtekintheti a diagramokat és ábrákat azt mutatják be, hogyan működik az alkalmazás idővel, és arra az esetre, ha az alkalmazás végrehajtja az adott mérőszám várt módon riasztások konfigurálása is.
  
    Lásd: [monitorozása az Azure Portal tárfiók](storage-monitor-storage-account.md) figyelése az Azure Portalon konfigurálásával kapcsolatos információkat.
* **Az AzCopy**. Az Azure Storage-naplóit, blobként vannak tárolva, így az AzCopy segítségével a naplóblobok másolja egy helyi könyvtárba a elemzése a Microsoft Message Analyzer használatával. Lásd: [adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md) AzCopy további információt.
* **A Microsoft Message Analyzer**. Az Üzenetelemző olyan eszköz, amely felhasználja a naplófájlok és naplóadatokat, amely megkönnyíti a filter, search és csoport naplóadatok hasznos csoportokba, amelyek segítségével elemezheti a hibákat és a teljesítménnyel kapcsolatos problémák vizuális formában jeleníti meg. Lásd: [Microsoft Message Analyzer működő útmutató](https://technet.microsoft.com/library/jj649776.aspx) Message Analyzer további információt.

## <a name="about-the-sample-scenario"></a>Tudnivalók a mintaforgatókönyvéhez
Ebben az oktatóanyagban egy olyan forgatókönyvet, ahol az Azure Storage mérőszámainak, egy alacsony százalékos sikerességi arány az alkalmazás, amely meghívja ezt az Azure storage megvizsgáljuk a. Az alacsony sikerességi arány százalékban arány metrika (jelenik meg **PercentSuccess** a a [az Azure portal](https://portal.azure.com) és a metrikák táblák) követi nyomon, hogy sikeres legyen, de, amely adja vissza, amely nagyobb, mint 299 HTTP-állapotkódú műveletek. A kiszolgálóoldali tárolási naplófájlokban ezeket a műveleteket rögzíti állapotú tranzakciós **ClientOtherErrors**. Az alacsony sikerességi arány százalékban metrika kapcsolatos további információkért lásd: [metrikák megjelenítése alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors állapotú tranzakciós műveletek](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Az Azure Storage-műveletek adhat vissza HTTP-állapotkódok 299 nagyobb a normál funkcióinak részeként. De ezek a hibák, egyes esetekben azt jelzik, hogy lehet az ügyfélalkalmazás jobb teljesítmény optimalizálása érdekében.

Ebben a forgatókönyvben azt fogja érdemes 100 % alatti bármi lehet, hogy egy alacsony százalékos sikerességi arányát. Választhat egy másik metrika szintre, azonban szükség szerint. Azt javasoljuk, hogy az alkalmazás tesztelése során kapcsolatot hoz létre egy alapkonfiguráció tolerancia az a fontos teljesítménymutatókat. Például dönthet úgy, alapján, tesztelés, hogy az alkalmazásnak tartalmaznia kell egy 90 %-os vagy 85 %-os konzisztens százalékos sikerességi aránya. A metrikák adatait jeleníti meg, hogy az alkalmazás méretektől van ez a szám, akkor a segítségével megvizsgálhatja, mi okozza-e a növekedés.

Minta esetünkben után azt mutatja meg, hogy van-e a sikerességi arány százalékban arány mérték 100 % alatti azt vizsgálja a hibák, amelyek a mérőszámok összekapcsolását, és ezek segítségével döntse el, mi okozza az alacsonyabb százalékos sikerességi aránya a naplókban. Megnézzük, kifejezetten, a hibákat a 400 tartományban. Ezután fogja jobban felülvizsgáljuk 404-es (nem található) hibát.

### <a name="some-causes-of-400-range-errors"></a>Tartományon kívüli 400-as hibák oka
Az alábbi példák az Azure Blob Storage és a lehetséges okok kérelmeket tartományon kívüli 400-as hibák példák láthatók. Bármelyik a hibákat, valamint a hibák a 300 és 500 tartományban, a közreműködhet egy alacsony százalékos sikerességi arányát.

Vegye figyelembe, hogy az alábbi listák messze befejeződött. Lásd: [állapota és hibakódok](https://msdn.microsoft.com/library/azure/dd179382.aspx) az MSDN webhelyen a tárolási szolgáltatások mindegyike adott hibákkal kapcsolatos általános Azure Storage-hibákkal kapcsolatos adatokat.

**Állapot 404 (nem található) kódpéldák**

Akkor következik be, amikor egy tárolót vagy blobot egy olvasási művelet sikertelen, mert nem található a blobon vagy tárolón.

* Akkor következik be, ha egy tárolót vagy blobot törölte egy másik ügyfélszámítógépen, mielőtt a kérést.
* Akkor következik be, ha egy API-hívás, amely létrehozza a tárolót vagy blobot ellenőrzése, hogy létezik-e után használja. A CreateIfNotExists API-k meghívásához fő először a tárolót vagy blobot; meglétének ellenőrzése Ha még nem létezik, 404-es hibát ad vissza, és ezután egy második PUT kezdeményezték írni a tárolót vagy blobot.

**Állapot-kódpéldák 409 (Ütközés)**

* Akkor következik be, ha egy API létrehozása használatával hozzon létre egy új tárolót vagy blobot, először az meglétének ellenőrzése nélkül, és a egy tárolót vagy blobot a néven már létezik.
* Akkor következik be, ha a tároló törlése folyamatban van, és megpróbál létrehozni egy új tárolót ugyanazzal a névvel, a törlési művelet befejezése előtt.
* Akkor következik be, ha egy tárolót vagy blobot meg kell adnia egy bérletet, és már nincs jelen bérletét.

**412 (előfeltétel) állapotkódot példák**

* Akkor fordul elő, ha egy feltételes fejléc által megadott feltétel nem teljesült.
* Akkor következik be, ha a bérlet megadott azonosító nem egyezik meg a tárolót vagy blobot a bérlet Azonosítóját.

## <a name="generate-log-files-for-analysis"></a>Hozzon létre a naplófájlokat az elemzéshez
Ebben az oktatóanyagban használjuk Üzenetelemző használata a naplófájlok, három különböző típusú bár ezek egyikével sem működik dönthet:

* A **kiszolgálónapló**, amelyben létrejön, amikor engedélyezi az Azure Storage-naplózásról. A kiszolgáló napló ellen az Azure tárolási szolgáltatások – blob, üzenetsor, tábla és fájl egyik minden művelet kapcsolatos adatokat tartalmaz. A kiszolgáló napló azt jelzi, hogy a művelet meg lett hívva, és milyen állapotkód volt a kérések és válaszok visszaadott, valamint egyéb adatait.
* A **.NET ügyfélnapló**, amely során az ügyféloldali naplózás engedélyezze a .NET-alkalmazás létrehozása. Az ügyfél napló részletes információkat arról, hogyan az ügyfél előkészíti a kérelmet, és fogadja és dolgozza fel a válasz tartalmazza.
* A **HTTP hálózati nyomkövetési napló**, amely gyűjti az adatokat a HTTP/HTTPS kérelmek és válaszok adatait, beleértve az Azure Storage kapcsolatos művelet-végrehajtási. Ebben az oktatóanyagban a hálózati nyomkövetés Message Analyzer használatával hozunk létre.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurálja a kiszolgálóoldali naplózás és mérőszámok
Első lépésként azt konfigurálnia kell az Azure Storage-naplózás és mérőszámok, úgy, hogy az ügyfélalkalmazás elemezheti adatait. Naplózás és a egy számos különböző módon - mérőszámok konfigurálhatja az keresztül a [az Azure portal](https://portal.azure.com), használja a Powershellt, vagy programozott módon. Lásd: [Storage mérőszámainak engedélyezése és a Mérőszámadatok megtekintése](https://msdn.microsoft.com/library/azure/dn782843.aspx) és [tárolási naplózás engedélyezése és Teljesítménynapló-adatok elérése](https://msdn.microsoft.com/library/azure/dn782840.aspx) az MSDN-en, naplózás és mérőszámok konfigurálásával kapcsolatos részletekért.

**Az Azure Portalon**

És konfigurálása naplózás és mérőszámok a Storage-fiók használatával a [az Azure portal](https://portal.azure.com), kövesse az utasításokat, [monitorozása az Azure Portal tárfiók](storage-monitor-storage-account.md).

> [!NOTE]
> Nem alkalmas állítsa be a perc típusú metrikák az Azure portal használatával. Azonban azt javasoljuk, hogy állítsa őket az oktatóanyag az alkalmazásában, valamint az alkalmazását a teljesítménnyel kapcsolatos problémák kivizsgálása. Perc típusú metrikák, ahogy az alábbi PowerShell-lel, vagy programozott módon, a storage ügyféloldali kódtára segítségével állíthatja be.
> 
> Vegye figyelembe, hogy az Azure Portalon nem tudja megjeleníteni a perc típusú metrikák, csak az óránkénti mérőszámot.
> 
> 

**Via PowerShell**

Az Azure PowerShell használatának első lépései, lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

1. Használja a [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0) parancsmagot, hogy az Azure felhasználói fiók hozzáadása a PowerShell-ablakban:
   
    ```powershell
    Add-AzureAccount
    ```

2. Az a **jelentkezzen be a Microsoft Azure** ablakban írja be az e-mail címet és a fiókjához tartozó jelszót. Az Azure hitelesíti és menti a hitelesítő adatokat, majd bezárja az ablakot.
3. Állítsa be az alapértelmezett tárfiók a storage-fiókba az oktatóanyagban használja úgy, hogy végrehajtja ezeket a parancsokat a PowerShell-ablakban:
   
    ```powershell
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Storage a Blob szolgáltatás naplózásának engedélyezéséről:
   
    ```powershell
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```

5. A Blob szolgáltatás, ügyelve arra, hogy állítsa be, hogy a storage mérőszámainak engedélyezése **- MetricsType** való `Minute`:
   
    ```powershell
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>.NET ügyféloldali naplózás konfigurálása
.NET-alkalmazás ügyféloldali naplózási diagnosztika .NET használata az alkalmazás konfigurációs fájljában (a web.config vagy az App.config fájlt). Lásd: [ügyféloldali naplózást, a Storage .NET ügyféloldali kódtár](https://msdn.microsoft.com/library/azure/dn782839.aspx) és [ügyféloldali naplózás a Microsoft Azure Storage SDK for Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) az MSDN-en a részletekért.

Az ügyféloldali napló részletes információkat arról, hogyan az ügyfél előkészíti a kérelmet, és fogadja és dolgozza fel a válasz tartalmazza.

A Storage ügyféloldali kódtára ügyféloldali Teljesítménynapló-adatok az alkalmazás konfigurációs fájljában (a web.config vagy az App.config fájlt) a megadott helyen tárolja.

### <a name="collect-a-network-trace"></a>Hálózati nyomkövetési adatok gyűjtése
Az Üzenetelemző használatával összegyűjtése egy HTTP/HTTPS hálózati nyomkövetést az ügyfélalkalmazás futtatása közben. A Message Analyzer használ [Fiddler](http://www.telerik.com/fiddler) a háttérben. A hálózati nyomkövetés gyűjthet, mielőtt azt javasoljuk, hogy a nem titkosított HTTPS-forgalom rögzítése a Fiddler konfigurálása:

1. Telepítés [Fiddler](http://www.telerik.com/download/fiddler).
2. A Fiddler elindítása.
3. Válassza ki **eszközök |} Fiddler-beállítások**.
4. A beállítások párbeszédpanelen ellenőrizze, hogy **rögzítése HTTPS csatlakozik** és **HTTPS-forgalom visszafejtése** mind van jelölve, ahogy az alábbi.

![Fiddler-beállítások konfigurálása](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Az oktatóanyag gyűjtése és a hálózati nyomkövetés először mentse a Message Analyzer, majd hozzon létre egy elemzési munkamenet a nyomkövetés és a naplók elemzéséhez. A Message Analyzer hálózati nyomkövetési adatok gyűjtése:

1. A Message Analyzer, válassza ki **fájl |} Gyors nyomkövetési |} Nem titkosított HTTPS**.
2. A nyomkövetés a azonnal megkezdődik. Válassza ki **leállítása** , állítsa le a nyomkövetést, így csak a nyomkövetési tárolási forgalom, konfigurálhatjuk.
3. Válassza ki **szerkesztése** szerkesztése a nyomkövetési munkamenet.
4. Válassza ki a **konfigurálása** jobb oldalán található hivatkozást a **Microsoft-Pef-WebProxy** ETW-szolgáltató.
5. Az a **speciális beállítások** párbeszédpanelen kattintson a **szolgáltató** fülre.
6. Az a **állomásnév szűrő** mezőben adja meg a tárolási végpontok, szóközzel elválasztva. Például megadhatja a végpontok módon; Módosítsa `storagesample` a tárfiók nevére:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Lépjen ki a párbeszédpanelt, és kattintson a **indítsa újra a** megkezdéséhez a nyomkövetési adatok gyűjtése a helyen, az állomásnév szűrővel, hogy csak az Azure Storage érkező hálózati forgalmat a nyomkövetés szerepel.

> [!NOTE]
> Miután ezzel végzett, a hálózati nyomkövetés gyűjtése, javasoljuk, hogy a beállításokat, előfordulhat, hogy módosította a fiddler esetében a HTTPS-forgalom visszafejtése vissza. A Fiddler beállítások párbeszédpanelen törölje a **rögzítése HTTPS csatlakozik** és **HTTPS-forgalom visszafejtése** jelölőnégyzeteket.
> 
> 

Lásd: [hálózati nyomkövetés funkciót](https://technet.microsoft.com/library/jj674819.aspx) kapcsolatos további részletek a TechNeten.

## <a name="review-metrics-data-in-the-azure-portal"></a>Tekintse át a metrikák adatait az Azure Portalon
Után az alkalmazás egy ideig futott, a megjelenő mérőszámdiagramok áttekintheti a [az Azure portal](https://portal.azure.com) , figyelje meg, hogyan a szolgáltatás rendelkezik lett végrehajtása.

Először keresse meg a storage-fiókot az Azure Portalon. A diagram a figyelés alapértelmezés szerint a **sikerszázalék** metrika a fiók panelen jelenik meg. Ha korábban már módosította a diagram különböző metrikák megjelenítéséhez, vegye fel a **sikerszázalék** metrikát.

Most már megjelenik **sikerszázalék** a figyelés diagramra, bármely más metrikákkal együtt előfordulhat, hogy hozzáadta. A forgatókönyvben azt fogja következő vizsgálata a Message Analyzer-naplók elemzésével sikerességi arány százalékban Ez valamelyest 100 % alatti.

A hozzáadása és testreszabása mérőszámdiagramok további részletekért lásd: [mérőszámdiagramok testreszabása](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Eltarthat egy kis ideig, miután engedélyezte a storage-mérőszámok az Azure-portálon jelennek meg a mérőszámadatokat. Ennek oka az, óránkénti metrikáit az előző órán belül nem jelenik meg az Azure Portalon mindaddig, amíg a jelenlegi óra telt el. Ezenkívül perc típusú metrikák jelenleg nem láthatók az Azure Portalon. Ezért attól függően, amikor engedélyezi a metrikák, eltarthat tekintse meg a mérőszámadatokat akár két órát.
> 
> 

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Kiszolgálónaplók egy helyi könyvtárba másolni az AzCopy használata
Az Azure Storage a kiszolgáló naplóadatok a blobokhoz, ír, amíg a metrikák írt táblákat. Naplóblobok érhetők el a jól ismert `$logs` a tárfiókhoz tartozó tárolót. Naplózási blobok neve hierarchikusan év, hónap, nap és óra, így könnyen keresse meg a vizsgálni kívánt időtartományt. Ha például a `storagesample` fiók, a tárolót a 01/02/2015-höz, a 8 – 9-kor, a naplózási blobok számára `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Az ebben a tárolóban az egyes blobok nevesített sorrendben, kezdve `000000.log`.

Az AzCopy parancssori eszköz használatával töltse le a kiszolgálóoldali naplófájlok egy tetszőleges helyre a helyi gépen. Például használhatja a következő parancsot a naplófájlokat, amelyek 2015. január 2 mappába került sor a blob műveletekhez letöltéséhez `C:\Temp\Logs\Server`; cserélje le `<storageaccountname>` a tárfiók nevével és `<storageaccountkey>` a fiók hozzáférési kulccsal :

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
Az AzCopy letölthető a [Azure letöltések](https://azure.microsoft.com/downloads/) lap. Az AzCopy használatával kapcsolatos részletekért lásd: [adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md).

Kiszolgálóoldali naplók letöltésére vonatkozó további információkért lásd: [letöltése Storage-naplózás adatainak](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>A Microsoft Message Analyzer használatával elemezheti a naplófájlok adatait
Microsoft Message Analyzert egy olyan eszköz, rögzítése, megjelenítése és elemzése a forgalmat, események és egyéb hibaelhárítási és diagnosztikai forgatókönyvek rendszer vagy alkalmazás üzeneteket üzenetküldési protokoll. Az Üzenetelemző is lehetővé teszi betöltése, összesített és naplóból származó adatok elemzése és nyomkövetési fájlok mentése. Az Üzenetelemző kapcsolatos további információkért lásd: [Microsoft Message Analyzer működő útmutató](https://technet.microsoft.com/library/jj649776.aspx).

Message Analyzert eszközöket tartalmaz, amelyek segítenek a kiszolgáló, ügyfél és hálózati naplók elemzése az Azure Storage. Ebben a szakaszban mutatjuk be ezeket az eszközöket a probléma az alacsony sikerességi arány százalékban a storage-naplók használatával.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Töltse le és telepítse a Message Analyzer és az Azure Storage-eszközök
1. Töltse le [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) a Microsoft letöltőközpontból, és futtassa a telepítőt.
2. Indítsa el a Message Analyzer.
3. Az a **eszközök** menüjében válassza **Eszközkezelő**. Az a **Eszközkezelő** párbeszédablakban válassza **letölti**, majd szűrés **Azure Storage**. Az Azure Storage-eszközök, az alábbi képen látható módon jelenik meg.
4. Kattintson a **szinkronizálás az összes megjelenített elemek** az Azure Storage-eszközök telepítéséhez. A rendelkezésre álló eszközök a következők:
   * **Az Azure Storage Színszabályainak:** az Azure Storage színszabályainak engedélyezése, hogy meghatározza a speciális szűrők, amelyek használják a szín, a szöveg és a betűtípus stílusok üzeneteket, amelyek tartalmazzák a nyomkövetési szereplő konkrét információk kiemeléséhez.
   * **Az Azure Storage-diagramokat:** az Azure Storage-diagramok használata előre definiált diagramok, amelyek a graph-kiszolgáló naplózási adatokat. Vegye figyelembe, hogy használatára diagramok Azure Storage jelenleg, akkor előfordulhat, hogy csak akkor kell betölteni a kiszolgálónapló a elemzési rácsba.
   * **Az Azure Storage-elemzők:** az Azure Storage-elemzők elemezni annak érdekében, hogy a elemzési rácsban megjelenítendő őket az Azure Storage-kliens, a kiszolgáló és a HTTP-naplókat.
   * **Az Azure Storage-szűrők:** az Azure Storage-szűrők használatával lekérdezheti az adatokat az elemzési rács előre meghatározott feltételeknek.
   * **Az Azure Storage nézet elrendezések:** az Azure Storage nézet elrendezések előre definiált oszlop elrendezések és az elemzési rács csoportosításokat.
5. Indítsa újra a Message Analyzer, az eszközök telepítése után.

![Message Analyzer Eszközkezelő](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Telepítse az összes az Azure Storage-eszközök jelenik meg ez az oktatóanyag az alkalmazásában.
> 
> 

### <a name="import-your-log-files-into-message-analyzer"></a>A naplófájlok importálja a Message Analyzer
Importálhatja a mentett naplófájlok (kiszolgálóoldali, az ügyféloldali és hálózati) az összes elemzés a Microsoft Message Analyzert egyetlen munkamenetben.

1. Az a **fájl** Microsoft Message Analyzert, menüjében kattintson a **új munkamenet**, és kattintson a **üres munkamenet**. Az a **új munkamenet** párbeszédpanelen adjon meg egy nevet az analysis-munkamenet. Az a **munkamenet részletei** panelen, kattintson a **fájlok** gombra.
2. A Message Analyzer által generált hálózati nyomkövetési adatok betöltéséhez kattintson a **fájlok hozzáadása**, keresse meg a helyet, ahová mentette a .matp fájlt a webes nyomkövetési munkamenet, válassza ki a .matp fájlt, és kattintson a **nyissa meg a**.
3. A kiszolgálóoldali naplóadatok betöltése, kattintson a **fájlok hozzáadása**, keresse meg a helyet, ahová letöltötte a kiszolgálóoldali naplók, válassza ki a naplófájlokat a elemzése, és kattintson a kívánt időtartományt **nyílt**. Ezt követően a a **munkamenet részletei** panelen, és állítsa a **szöveges napló konfigurációs** a kiszolgálóoldali log fájl esetében legördülő **AzureStorageLog** , győződjön meg arról, hogy a Microsoft Message Elemző is megfelelően elemezni a naplófájlt.
4. Az ügyféloldali naplóadatok betöltése, kattintson a **fájlok hozzáadása**, keresse meg a helyet, ahová mentette az ügyféloldali naplók, válassza ki a naplófájlok elemzése, és kattintson a kívánt **nyílt**. Ezt követően a a **munkamenet részletei** panelen, és állítsa a **szöveges napló konfigurációs** az egyes ügyféloldali naplófájlok legördülő **AzureStorageClientDotNetV4** annak érdekében, hogy Microsoft Message Analyzert lehet megfelelően elemezni a naplófájl.
5. Kattintson a **Start** a a **új munkamenet** betölteni és elemezni a naplóadatok párbeszédpanel. A naplóadatok a Message Analyzer elemzési rács jeleníti meg.

A következő ábrán látható egy példa munkamenet a kiszolgáló, ügyfél és a hálózati nyomkövetési naplófájlok konfigurálva.

![Message Analyzer munkamenet konfigurálása](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Vegye figyelembe, hogy az Üzenetelemző naplófájlok betölti a memóriába. Ha rengeteg naplóadatokat, célszerű a legjobb teljesítmény eléréséhez a Message Analyzer szűréséhez.

Először határozza meg, amelynek Ön a fájlvédelmi, és ezen az időn legyen a lehető legkisebb tartani. A legtöbb esetben célszerű tekintse át a percek vagy órák legfeljebb egy ideig. Importáljon a legkisebb naplók, amely megfelel az igényeinek.

Ha továbbra is nagy mennyiségű naplózási adatokat, majd érdemes adja meg a munkamenet szűrőt, a naplózási adatokat, mielőtt betölti azt. Az a **munkamenet szűrő** jelölje ki a **könyvtár** gombra kattintva válasszon egy előre meghatározott szűrőt; például **globális idő szűrő I** szűrése szűri az Azure Storage-ból az adott idő alatt. Szerkesztheti a szűrési feltételeket a kezdési és befejezési időbélyeg meg szeretné tekinteni az időköz megadása. Szűrhet emellett az adott állapotkódot; Ha például választhat csak az állapotkód: 404 esetén naplóbejegyzések betöltése.

A Teljesítménynapló-adatok importálása a Microsoft Message Analyzer kapcsolatos további információkért lásd: [üzenet adatainak beolvasása](https://technet.microsoft.com/library/dn772437.aspx) a TechNet webhelyén.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Naplófájlok adatainak korrelációját ügyfélkérelem-azonosító használata
Az Azure Storage ügyféloldali kódtár automatikusan létrehozza az egyedi ügyfélkérelem-azonosító minden egyes kérés esetében. Ez az érték íródik a ügyfélnapló, a kiszolgáló naplózási és a hálózati nyomkövetés adatainak korreláltatására Message Analyzer belül minden három napló használhassa. Lásd: [ügyfélkérelem-azonosító](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) további információt az ügyfél a kérés azonosítóját.

Az alábbi szakaszok azt ismertetik, hogyan előre konfigurált és az egyéni elrendezés nézetek segítségével összehasonlíthatja és csoport adatai alapján az ügyfél-kérelem azonosítója.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Az elemzés rácsban megjelenítendő nézetet elrendezés kiválasztása
A Message Analyzer tárolási eszközök vannak az előre konfigurált nézeteket, amelyek segítségével az adatok hasznos csoportosításokat és a különböző helyzetekhez oszlopok megjelenítéséhez az Azure Storage nézet elrendezését tartalmazza. Is létrehozhat egyéni nézet elrendezését és újbóli felhasználás céljából menthetők.

Az alábbi képen az látható a **nézet elrendezési** menüben elérhető kiválasztásával **nézet elrendezési** az eszköztár menüszalagján. A nézet elrendezését, az Azure Storage vannak összegyűjtve a **Azure Storage** csomópont a menüben. Kereshet `Azure Storage` a keresőmezőbe, az Azure Storage szűrése elrendezések csak megtekinthetők. Választhatja a kedvencként, és megjeleníti azt a menü tetején nézet elrendezést melletti csillagra.

![Nézet elrendezési menü](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Először válasszon **ügyfélkérelem és modul szerint csoportosítva**. E nézet elrendezése csoportok jelentkezzen az összes három napló adatokat először ügyfélkérelem-azonosító, azután a forrás-naplófájl (vagy **modul** a Message Analyzer). Ebben a nézetben egy adott ügyfélkérelem-azonosító feltárásához, és tekintse meg az összes három naplófájl, ügyfélkérelem-azonosító. adatokat

Az alábbi képen az látható ez elrendezését a nézet minta naplóadatok megjelenített oszlopok egy része a alkalmazni. Láthatja, hogy az adott ügyfélkérelem-azonosító, az elemzés rács adatait jeleníti meg az ügyfél log kiszolgálónapló és hálózati nyomkövetés.

![Az Azure Storage nézet elrendezése](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Különböző naplófájlokat rendelkezik másik oszlop, így több naplófájlt származó adatok elemzése rács jelenik meg, ha egyes oszlopokat, nem tartalmaz egy megadott sorának adatokat. Például a fenti képen, az ügyfél log sorokat ne jelenjen meg az adatokat a **időbélyeg**, **TimeElapsed**, **forrás**, és **cél**oszlop, mert ezekben az oszlopokban nem szerepelnek az ügyfél napló, de a hálózati nyomkövetés léteznek. Ehhez hasonlóan a **időbélyeg** oszlop a kiszolgálónapló időbélyeg adatait jeleníti meg, de nincsenek adatok nem jelenik meg a **TimeElapsed**, **forrás**, és  **Cél** oszlopokat, amelyek nem részei a kiszolgálónapló.
> 
> 

Mellett az Azure Storage-nézet elrendezések használatával, is határozza meg, és mentse a saját nézet elrendezését. Válassza ki az adatok csoportosításához többi kívánt mezőt, és mentse a csoportosítást, valamint az egyéni elrendezés részeként.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Az elemzés rács színe szabályok alkalmazása
A Storage-eszközök is kínálnak a Vizualizáció azt jelenti, hogy azonosíthatja a különböző típusú hibákat a elemzési rács színszabályainak. Így azok megjelennek a kiszolgáló naplózási és a hálózati nyomkövetés csak a HTTP-hibák, az előre definiált színt szabályok érvényesek lesznek.

Szín szabályokat a alkalmazni, válassza ki a **Színszabályainak** az eszköztár menüszalagján. Látni fogja az Azure Storage színszabályainak menüjében. Az oktatóanyaghoz válasszon **Ügyfélhibák (400 és 499 StatusCode)**, ahogy az alábbi képen is látható.

![Az Azure Storage nézet elrendezése](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Használata az Azure Storage színszabályainak, mellett is meghatározása és a saját színszabályainak mentése.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Csoport és a szűrő naplóadatok tartományon kívüli 400-as hibák keresése
Ezután azt fogja csoport az adatok szűrésére és napló az összes hiba található a 400 tartományban.

1. Keresse meg a **StatusCode** oszlop az elemzési rácsban, kattintson a jobb gombbal a címsor, és válassza ki az oszlop **csoport**.
2. Ezután a csoport a **ügyfélkérelem** oszlop. Látni fogja, hogy az adatok a elemzési rács most már van rendezve, állapotkód és a kérés ügyfél-azonosítót.
3. A Nézetszűrőhöz eszköz ablak megjelenítése, ha azt már nem jelenik meg. Válassza az eszköztár menüszalagon **eszköz Windows**, majd **Nézetszűrőhöz**.
4. Csak a 400-tartomány hibák megjelenítése a naplóadatok szűrni, adja hozzá az alábbi feltételeket, a **Nézetszűrőhöz** ablakban, majd kattintson **alkalmaz**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

Az alábbi képen a csoportosítás és a szűrő eredményeit jeleníti meg. Kibontás a **ügyfélkérelem** mező alatt csoportosítása állapotkód: 409, például látható egy műveletet, amely adott állapotkód eredményezett.

![Az Azure Storage nézet elrendezése](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Ez a szűrő alkalmazása, után látni fogja, hogy az ügyfél-naplófájl azon sorait ki vannak zárva, az ügyféllel a napló nem tartalmazza a **StatusCode** oszlop. Először áttekintheti a kiszolgáló és a hálózati nyomkövetési naplók keresse meg a 404-es hibát, és ezután még a ügyfélnapló megvizsgálhatja az Ügyfélműveletek őket vezettek visszatérünk.

> [!NOTE]
> Már szűrhet erre a **StatusCode** oszlopot, és továbbra is három naplókat, ha ad hozzá egy kifejezést a szűrő, amely tartalmazza a naplóbejegyzéseket, ahol az állapotkód értéke null, többek között a ügyfélnapló megjelenített adatokat. A szűrőkifejezés létrehozni, használja:
> 
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
> 
> Ez a szűrő összes sorát visszaadják az ügyfél napló- és csak azokat a sorokat, a kiszolgáló naplóját és a HTTP-napló, az állapotkód értéke nagyobb, mint 400-as. Ügyfélkérelem-azonosító és a modul szerint csoportosítva nézet elrendezését a alkalmazni, ha keressen, és görgessen a található azokról, amelyben szerepelnek az összes három napló bejegyzései keresztül.   
> 
> 

### <a name="filter-log-data-to-find-404-errors"></a>Naplóadatok 404-es hibát talál szűréséhez
A Storage-eszközök előre definiált szűrők, amely naplóadatokat találja a hibák vagy a trendeket, Ön által keresett szűkítése használhatja tartalmazzák. Ezután azt fogja két előre meghatározott szűrőket alkalmazhat: egyet a kiszolgáló és a hálózat-nyomkövetési naplókat 404-es hibát szűri, és szűri az adatokat a megadott időtartományban.

1. A Nézetszűrőhöz eszköz ablak megjelenítése, ha azt már nem jelenik meg. Válassza az eszköztár menüszalagon **eszköz Windows**, majd **Nézetszűrőhöz**.
2. A nézet szűrő ablakban jelölje ki **könyvtár**, és a keresési `Azure Storage` keresse meg az Azure Storage-szűrők. Válassza ki a szűrő **404 (nem található) az összes napló állapotüzenetek**.
3. Megjelenítési a **könyvtár** menü újra, és keresse meg és válassza ki a **globális Időszűrő**.
4. A szűrő a megtekinteni kívánt tartomány látható időbélyegei szerkesztése. Ez segít az adatok elemzéséhez tartományából szűkítheti.
5. A szűrő az alábbi példához hasonlóan kell megjelennie. Kattintson a **alkalmaz** elemet a szűrő alkalmazásához a elemzési rácsra.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Az Azure Storage nézet elrendezése](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>A naplóadatok elemzése
Most, hogy vannak csoportosítva, és szűri az adatokat, ellenőrizheti a 404-es hibát jelzett az egyes kérelmek részletei. Az aktuális nézet elrendezését az adatok ügyfélkérelem-azonosító, majd az eseménynapló forrás szerint vannak csoportosítva. Mivel tudjuk szűrést végez a kérelmek, a StatusCode mező a 404-es, Találkozzunk csak a kiszolgáló és a hálózati nyomkövetési adatokat, nem az ügyfél naplóadatokat.

A következő ábrán látható egy adott kéréshez, amikor egy Blob beolvasása művelet kurzorműveletnek-e 404-es, mert a blob nem létezik. Vegye figyelembe, hogy egyes oszlopok el lettek távolítva a standard szintű nézet annak érdekében, hogy a kapcsolódó adatok megjelenítése.

![Szűrt kiszolgálói és hálózati nyomkövetési naplók](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Ezután azt fogja korrelációját, ha az ügyfélkérelem-azonosító meg, milyen műveletet, az ügyfél lett véve, ha a hiba történt az ügyfél log adatokkal. Egy új elemzési rácsnézet ehhez a munkamenethez megtekintéséhez a ügyfél naplóadatokat, amely megnyit egy második lapján jelenítheti meg:

1. Először másolja be az értékét a **ügyfélkérelem** mezőt a vágólapra. Is ehhez, vagy sor kijelölése, megkeresi a **ügyfélkérelem** mező, kattintson a jobb gombbal az adatérték a, és kiválasztása **másolási "Ügyfélkérelem"**.
2. Az eszköztár menüszalagján válassza **új megjelenítő**, majd **elemzési rács** egy új lap megnyitásához. Az új lapon az összes adat látható a naplófájlok, csoportosítás, szűrés és színszabályainak nélkül.
3. Az eszköztár menüszalagján válassza **nézet elrendezési**, majd **.NET-ügyfél az összes oszlop** alatt a **Azure Storage** szakaszban. E nézet elrendezése az ügyfél adatait jeleníti meg, log, valamint a kiszolgáló és a hálózat-nyomkövetési naplókat. Alapértelmezés szerint a rendszer rendezi a **MessageNumber** oszlop.
4. Ezután az ügyfél napló kereséséhez az ügyfél-kérelem azonosítója. Válassza az eszköztár menüszalagon **található üzenetek**, majd adjon meg egy egyéni szűrő az ügyfélkérelem-azonosító a **található** mező. Használja ezt a szintaxist a szűrőt, a saját ügyfélkérelem-azonosító megadása:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Az Üzenetelemző talál, és kiválasztja az első naplóbejegyzés, ahol a keresési feltételeknek megfelel-e az ügyfél-kérelem azonosítója. Az ügyfél naplóban tételt tartalmaz több minden ügyfélkérelem-azonosító, ezért érdemes alapján szeretne csoportosítani őket a **ügyfélkérelem** mező könnyebb minden egy helyen tekintheti meg őket. A következő ábrán látható összes üzenetet az ügyfél naplóban az adott ügyfélre vonatkozó kérés azonosítója.

![404-es hibát ügyfél naplófájl-megjelenítő](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Ezen két lapok a nézetet elrendezések megjelenített adatok használatával, a kérelem adatai meghatározni a hibát okozó mi is elemezhet. Tekintse meg, ha egy előző esemény vezettek előfordulhat, hogy a 404-es hiba való szemléltetett kérelmek is megjeleníthető. Áttekintheti például az ügyfél bejegyzései megelőző az ügyfél-Azonosítót határozza meg, hogy a blob már törölték, vagy ha az ügyfélalkalmazás egy tárolót vagy blobot a CreateIfNotExists API meghívása okozta a hibát. A blob címét annak az ügyfél naplóban a **leírás** mező; a kiszolgáló és a hálózat-nyomkövetési naplókat, ez az információ jelenik meg a **összefoglalás** mező.

Ha ismeri a címet, amely a 404-es hibát eredményezett a BLOB, megvizsgálhatja további. Ha a többi üzenet ugyanennek a blobnak a műveletekhez kapcsolódó bejegyzései keres, ellenőrizheti a-e az ügyfél korábban törölni az entitást.

## <a name="analyze-other-types-of-storage-errors"></a>Más típusú storage-hibák elemzése
Most, hogy ismeri az Üzenetelemző használatával a naplóadatok elemzéséhez, elemezheti a más típusú hibák nézettel elrendezések színszabályainak és a Keresés és szűrés. Az alábbi táblázat sorolja fel, problémák merülhetnek fel, és a szűrési feltételeket a segítségével keresse meg őket. A szűrők és a Message Analyzer szűrése nyelv hozhat létre további információkért lásd: [szűrés állapotüzenet-adatokat](https://technet.microsoft.com/library/jj819365.aspx).

| Vizsgálja meg a... | Szűrési kifejezés használata... | Kifejezés érvényes naplófájl (ügyfél, a kiszolgáló, a hálózat, az összes) |
| --- | --- | --- |
| Nem várt késedelmeket egy üzenetsorban található üzenetek |AzureStorageClientDotNetV4.Description tartalmaz "Újrapróbálkozás sikertelen műveletet." |Ügyfél |
| HTTP növekedése percentthrottlingerror értéket mutatnak |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Network (Hálózat) |
| Növeli a percenttimeouterror értéket mutatnak |HTTP. Response.StatusCode == 500 |Network (Hálózat) |
| Növelje az (all) percenttimeouterror értéket mutatnak |* StatusCode == 500 |Összes |
| Növeli a percentnetworkerror értéket mutatnak |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Ügyfél |
| A HTTP 403 (tiltott) üzenetek |HTTP. Response.StatusCode 403-as == |Network (Hálózat) |
| A HTTP 404 (nem található) üzenetek |HTTP. Response.StatusCode 404-es == |Network (Hálózat) |
| 404 (all) |* StatusCode 404-es == |Összes |
| Közös hozzáférésű Jogosultságkód (SAS) hitelesítési probléma |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Network (Hálózat) |
| HTTP 409 (Ütközés) üzenetek |HTTP. Response.StatusCode == 409 |Network (Hálózat) |
| 409 (mind) |* StatusCode == 409 |Összes |
| Alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors állapotú tranzakciós műveletek |AzureStorageLog.RequestStatus == "ClientOtherError" |Kiszolgáló |
| Nagle figyelmeztetés |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5-ös)) és (AzureStorageLog.RequestPacketSize < 1460) és (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Kiszolgáló |
| A kiszolgáló és a hálózati naplókban időtartományt |#Timestamp > = 2014-10-20T16:36:38 és #Timestamp < = 2014-10-20T16:36:39 |Kiszolgáló, hálózati |
| A kiszolgálónaplók időtartományt |AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 és AzureStorageLog.Timestamp < = 2014-10-20T16:36:39 |Kiszolgáló |

## <a name="next-steps"></a>További lépések
Az Azure Storage-hibaelhárítási teljes körű forgatókönyvekkel kapcsolatos további információkért tekintse meg ezeket az erőforrásokat:

* [Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Az Azure Portal tárfiók figyelése](storage-monitor-storage-account.md)
* [Adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [A Microsoft Message Analyzer üzemeltetési útmutató](https://technet.microsoft.com/library/jj649776.aspx)
