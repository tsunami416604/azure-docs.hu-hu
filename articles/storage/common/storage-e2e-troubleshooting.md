---
title: Adatműveletek hibaelhárítása a diagnosztika és az üzenetsor-elemző szolgáltatással
titleSuffix: Azure Storage
description: Útmutató a Azure Storage Analytics, a AzCopy és a Microsoft Message Analyzer teljes körű hibaelhárításához
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: ozgun
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 4b0145514a884c43ef18518cf25a2a78b1fc3aa3
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84809054"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Átfogó hibaelhárítás Azure Storage-metrikák és -naplózás, az AzCopy és a Message Analyzer használatával

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

A diagnosztizálás és a hibaelhárítás kulcsfontosságú képesség az ügyfélalkalmazások Microsoft Azure Storagesal való kiépítése és támogatása terén. Az Azure-alkalmazások elosztott jellegéből adódóan a hibák és a teljesítménnyel kapcsolatos problémák diagnosztizálása és hibaelhárítása összetettebb lehet, mint a hagyományos környezetekben.

Ebben az oktatóanyagban bemutatjuk, hogyan azonosíthatja azokat a hibákat, amelyek hatással lehetnek a teljesítményre, és elháríthatja ezeket a hibákat a Microsoft és az Azure Storage által biztosított eszközök teljes körű használatával az ügyfélalkalmazás optimalizálása érdekében.

Ez az oktatóanyag gyakorlati áttekintést nyújt a teljes körű hibaelhárítási forgatókönyvről. Az Azure Storage-alkalmazások hibaelhárításának részletes elméleti útmutatója: [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Eszközök az Azure Storage-alkalmazások hibaelhárításához

Az Microsoft Azure Storaget használó ügyfélalkalmazások hibakereséséhez használhatja az eszközök kombinációját, amellyel meghatározhatja, hogy mikor történt hiba, és hogy mi okozza a problémát. Ezek az eszközök a következőket foglalják magukban:

* **Azure Storage Analytics**. A [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) metrikákat és naplózást biztosít az Azure Storage-hoz.

  * A **tárolási metrikák** a Storage-fiókhoz tartozó tranzakciós mérőszámokat és kapacitási mérőszámokat figyelik. A metrikák használatával meghatározhatja, hogy az alkalmazás milyen módon legyen végrehajtva különböző mértékek szerint. A Storage Analytics által követett mérőszámok típusával kapcsolatos további információkért tekintse meg [Storage Analytics mérőszámok táblázat sémáját](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) .
  * A **Storage-naplózás** az Azure Storage szolgáltatásba érkező kéréseket egy kiszolgálóoldali naplóba naplózza. A napló nyomon követi az egyes kérelmek részletes adatait, beleértve a végrehajtott műveletet, a művelet állapotát és a késési adatokat. A naplókra Storage Analytics által írt kérelmekkel és válaszokkal kapcsolatos további információkért tekintse meg a [Storage Analytics naplózási formátumát](/rest/api/storageservices/Storage-Analytics-Log-Format) .

* **Azure Portal**. A [Azure Portalban](https://portal.azure.com)beállíthat metrikákat és naplózást a Storage-fiókjához. Megtekintheti azokat a diagramokat és diagramokat is, amelyek bemutatják, hogy az alkalmazás hogyan működik az idő múlásával, és a riasztások konfigurálásával jelezze, ha az alkalmazás a vártnál eltérően hajtja végre a megadott mérőszámot.

    A Azure Portal figyelésének konfigurálásával kapcsolatos információkért lásd: [Storage-fiók figyelése a Azure Portal](storage-monitor-storage-account.md) .
* **AzCopy**. Az Azure Storage-hoz készült kiszolgálói naplók blobként tárolódnak, így a AzCopy segítségével a Microsoft Message Analyzer használatával elemzés céljából másolhatók a naplófájlok egy helyi könyvtárba. A AzCopy kapcsolatos további információkért tekintse meg [az adatok átvitele a AzCopy parancssori segédprogrammal](storage-use-azcopy.md) című témakört.
* **Microsoft Message Analyzer**. Az Message Analyzer egy olyan eszköz, amely naplófájlokat használ, és vizuális formátumban jeleníti meg a napló adatait, így könnyen szűrhetők, kereshetők és csoportosíthatók a naplók adatai a hibák és a teljesítménnyel kapcsolatos problémák elemzéséhez. A Message Analyzer szolgáltatással kapcsolatos további információkért tekintse meg a [Microsoft Message Analyzer üzemeltetési útmutatóját](https://technet.microsoft.com/library/jj649776.aspx) .

## <a name="about-the-sample-scenario"></a>Tudnivalók a minta forgatókönyvről

Ebben az oktatóanyagban egy olyan forgatókönyvet vizsgálunk, amelyben az Azure Storage-mérőszámok az Azure Storage-t meghívó alkalmazások alacsony százalékos sikerességi arányát jelzik. Az alacsony százalékos sikerességi arány mérőszáma (amely a [Azure Portal](https://portal.azure.com) és a metrikák tábláiban **PercentSuccess** látható) nyomon követi a sikeres műveleteket, de az 299-nál nagyobb HTTP-állapotkódot ad vissza. A kiszolgálóoldali tárolási naplófájlokban ezek a műveletek a **ClientOtherErrors**tranzakciós állapotával lesznek rögzítve. Az alacsony százalékos sikerességi metrikával kapcsolatos további információkért lásd: az [alacsony PercentSuccess vagy elemzési naplóbejegyzések megjelenítését jelző metrikák a ClientOtherErrors tranzakciós állapotával rendelkeznek](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Az Azure Storage-műveletek a normál működésük részeként 299-nél nagyobb HTTP-állapotkódot adhatnak vissza. Ezek a hibák azonban bizonyos esetekben arra utalnak, hogy a jobb teljesítmény érdekében optimalizálni lehet az ügyfélalkalmazás.

Ebben az esetben az alacsony százalékos sikerességi arányt vesszük figyelembe, hogy a 100%-os érték legyen. Az igényeknek megfelelően eltérő metrikai szintet is kiválaszthat. Javasoljuk, hogy az alkalmazás tesztelése során hozzon létre egy alapértéket a fő teljesítménymutatók számára. Például a tesztelés alapján meghatározhatja, hogy az alkalmazásnak konzisztens százalékos sikerességi aránysal kell rendelkeznie, 90%-os vagy 85%. Ha a metrikák adatai azt mutatják, hogy az alkalmazás eltér ettől a számtól, akkor megvizsgálhatja, hogy mi okozza a növekedést.

A minta forgatókönyv esetében, ha úgy találtuk, hogy a százalékos sikerességi arány mérőszáma 100% alatti, megvizsgáljuk a naplókat, hogy megtalálják a metrikákkal kapcsolatos hibákat, és ezek alapján kiderítjük, mi okozza az alacsonyabb százalékos sikerességi arányt. Az 400-es tartományon belül a következő hibák jelennek meg: Ezt követően alaposabban megvizsgáljuk a 404 (nem található) hibákat.

### <a name="some-causes-of-400-range-errors"></a>Az 400-alapú hibák néhány oka

Az alábbi példák az Azure Blob Storagere irányuló kérések néhány 400-alapú hibáját, valamint azok lehetséges okait ismertetik. Ezek a hibák, valamint az 300-es és a 500-es tartománybeli hibák is hozzájárulhatnak az alacsony százalékos sikerességi arányhoz.

Vegye figyelembe, hogy az alábbi felsorolások korántsem teljesek. Az általános Azure Storage-hibákkal és az egyes tárolási szolgáltatásokkal kapcsolatos hibákkal kapcsolatban lásd: az MSDN-beli [állapot-és hibakódok](https://msdn.microsoft.com/library/azure/dd179382.aspx) .

#### <a name="status-code-404-not-found-examples"></a>404-es állapotkód (nem található) példák

Akkor következik be, amikor egy tárolóra vagy blobra vonatkozó olvasási művelet meghiúsul, mert a blob vagy a tároló nem található.

* Akkor következik be, ha egy másik ügyfél törölte egy tárolót vagy blobot a kérelem előtt.
* Akkor következik be, ha olyan API-hívást használ, amely létrehozza a tárolót vagy a blobot, miután ellenőrizte, hogy létezik-e. A Createifnotexists metódust API-k először a tároló vagy a blob létezésének ellenőrzését végzik. Ha nem létezik, a rendszer 404-es hibát ad vissza, majd egy második PUT hívást készít a tároló vagy a blob írására.

#### <a name="status-code-409-conflict-examples"></a>409-es állapotkód (ütközés) – példák

* Akkor következik be, ha a Create API használatával új tárolót vagy blobot hoz létre, és nem ellenőrzi az első létezését, és már létezik ilyen nevű tároló vagy blob.
* Akkor következik be, ha egy tárolót törölnek, és egy új tárolót próbál létrehozni ugyanazzal a névvel a törlési művelet befejeződése előtt.
* Akkor következik be, ha egy tárolón vagy blobon megadja a bérletet, és már van bérlet.

#### <a name="status-code-412-precondition-failed-examples"></a>412-es állapotkód (sikertelen előfeltétel) példák

* Akkor következik be, amikor egy feltételes fejlécben megadott feltétel nem teljesült.
* Akkor következik be, amikor a megadott címbérlet-azonosító nem egyezik meg a tárolón vagy a blobon lévő címbérlet-AZONOSÍTÓval.

## <a name="generate-log-files-for-analysis"></a>Naplófájlok előállítása elemzéshez

Ebben az oktatóanyagban az üzenetsor használatával három különböző típusú naplófájl használatát fogjuk használni, de ezek közül bármelyiket használhatja:

* A **kiszolgáló naplója**, amely az Azure Storage naplózásának engedélyezésekor jön létre. A kiszolgáló naplója az Azure Storage-szolgáltatások (blob, üzenetsor, tábla és fájl) által hívott műveletekre vonatkozó információkat tartalmaz. A kiszolgáló naplója jelzi, hogy melyik művelet lett meghívva, és milyen állapotkódot adott vissza, valamint a kérésre és a válaszra vonatkozó további részletek.
* A **.net-ügyfél naplója**, amely akkor jön létre, amikor engedélyezi az ügyféloldali naplózást a .net-alkalmazáson belül. Az ügyfél naplója részletes információkat tartalmaz arról, hogy az ügyfél hogyan készíti elő a kérést, és fogadja és dolgozza fel a választ.
* A **http-hálózati nyomkövetési napló**, amely az adatokat a HTTP/HTTPS-kérelmekre és-válaszokra vonatkozó adatokat gyűjti, beleértve az Azure Storage-ba irányuló műveleteket is. Ebben az oktatóanyagban létrehozjuk a hálózati nyomkövetést az üzenetsor-elemző használatával.

### <a name="configure-server-side-logging-and-metrics"></a>Kiszolgálóoldali naplózás és metrikák konfigurálása

Először is be kell állítania az Azure Storage naplózását és mérőszámait, hogy az adatok a szolgáltatás oldaláról elemezzenek. A naplózást és a mérőszámokat többféleképpen is konfigurálhatja – a [Azure Portal](https://portal.azure.com)a PowerShell használatával vagy programozott módon. A naplózás és a metrikák konfigurálásával kapcsolatos részletekért lásd a [metrikák engedélyezése](storage-analytics-metrics.md#enable-metrics-by-using-the-azure-portal) és a [naplózás engedélyezése](storage-analytics-logging.md#enable-storage-logging) című témakört.

### <a name="configure-net-client-side-logging"></a>.NET ügyféloldali naplózás konfigurálása

.NET-alkalmazás ügyféloldali naplózásának konfigurálásához engedélyezze a .NET-diagnosztika szolgáltatást az alkalmazás konfigurációs fájljában (web.config vagy app.config). A részletekért tekintse meg az [ügyféloldali naplózást a .net Storage ügyféloldali függvénytárával](https://msdn.microsoft.com/library/azure/dn782839.aspx) és [az ügyféloldali naplózással a javához készült Microsoft Azure Storage SDK-val](https://msdn.microsoft.com/library/azure/dn782844.aspx) .

Az ügyféloldali napló részletes információkat tartalmaz arról, hogy az ügyfél hogyan készíti elő a kérést, és fogadja és dolgozza fel a választ.

A Storage ügyféloldali kódtára az alkalmazás konfigurációs fájljában (web.config vagy app.config) megadott helyen tárolja az ügyféloldali naplózási adategységeket.

### <a name="collect-a-network-trace"></a>Hálózati nyomkövetés összegyűjtése

Az üzenetsor használatával HTTP/HTTPS hálózati nyomkövetést gyűjthet az ügyfélalkalmazás futása közben. Az üzenet-elemző a háttérbeli [hegedűst](https://www.telerik.com/fiddler) használja. A hálózati nyomkövetés összegyűjtése előtt javasoljuk, hogy konfigurálja a hegedűst a titkosítatlan HTTPS-forgalom rögzítéséhez:

1. A [Hegedűs](https://www.telerik.com/download/fiddler)telepítése.
2. Hegedűs elindítása.
3. **Eszközök kiválasztása | Hegedűs beállításai**.
4. A beállítások párbeszédpanelen ellenőrizze, hogy a HTTPS-kapcsolat **rögzítése** és a **https-forgalom visszafejtése** lehetőség ki van-e jelölve az alább látható módon.

![Hegedűs beállításainak konfigurálása](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Az oktatóanyagban először gyűjtsön és mentsen el egy hálózati nyomkövetést az üzenet-elemzőben, majd hozzon létre egy elemzési munkamenetet a nyomkövetés és a naplók elemzéséhez. Hálózati nyomkövetés összegyűjtése az üzenetsor-elemzőben:

1. Az üzenet-elemző területen válassza a **fájl | Gyors nyomkövetés | Titkosítatlan HTTPS**.
2. A nyomkövetés azonnal elindul. Válassza a **Leállítás** lehetőséget a nyomkövetés leállításához, hogy a rendszer csak a tároló forgalmának nyomon követésére legyen konfigurálva.
3. A nyomkövetési munkamenet szerkesztéséhez válassza a **Szerkesztés** lehetőséget.
4. Válassza a **configure (Konfigurálás** ) hivatkozást a **Microsoft-PEF-webproxy ETW-** szolgáltató jobb oldalán.
5. A **Speciális beállítások** párbeszédpanelen kattintson a **szolgáltató** fülre.
6. Az **állomásnév szűrő** mezőben adja meg a tárolási végpontokat szóközzel elválasztva. Megadhatja például a végpontokat a következőképpen: Váltson `storagesample` a Storage-fiók nevére:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Zárja be a párbeszédpanelt, majd az **Újraindítás** gombra kattintva kezdje meg a nyomkövetés begyűjtését az állomásnév-szűrővel, hogy csak az Azure Storage hálózati forgalom szerepeljen a nyomkövetésben.

> [!NOTE]
> Miután befejezte a hálózati nyomkövetés összegyűjtését, javasoljuk, hogy állítsa vissza a Hegedűs által a HTTPS-forgalom visszafejtéséhez módosított beállításokat. A Hegedűs beállításai párbeszédpanelen törölje a jelet a **rögzítés HTTPS-kapcsolat csatlakoztatása** és a **https-forgalom visszafejtése** jelölőnégyzetből.

További részletekért tekintse meg [a hálózati nyomkövetési funkciók használata](https://technet.microsoft.com/library/jj674819.aspx) a TechNeten című témakört.

## <a name="review-metrics-data-in-the-azure-portal"></a>A metrikák adatait a Azure Portalban tekintheti át

Ha az alkalmazás egy ideig fut, tekintse át a [Azure Portalban](https://portal.azure.com) megjelenő mérőszámokat, hogy megfigyelje a szolgáltatás teljesítményét.

Először navigáljon a Storage-fiókjához a Azure Portal. Alapértelmezés szerint a fiók paneljén egy, a **sikeresség százalékos értékével** rendelkező figyelési diagram jelenik meg. Ha korábban módosította a diagramot különböző metrikák megjelenítéséhez, adja hozzá a **sikeresség százalékos arányát** .

Ekkor megjelenik a figyelési diagram **sikerességi aránya** , valamint az esetlegesen hozzáadott mérőszámok. Ebben az esetben a következőt vizsgáljuk meg a naplók az üzenetsor-elemzőben való elemzésével. a százalékos sikerességi arány valamivel 100% alá esik.

A metrikák diagramjainak hozzáadásával és testreszabásával kapcsolatos további információkért lásd a [metrikák diagramjainak testreszabása](storage-monitor-storage-account.md#customize-metrics-charts)című témakört.

> [!NOTE]
> Eltarthat egy ideig, amíg a metrikák adatai megjelennek a Azure Portal a tárolási mérőszámok engedélyezése után. Ennek az az oka, hogy az előző órában mért óránkénti mérőszámok nem jelennek meg a Azure Portalban, amíg az aktuális óra el nem telik. Emellett a perc mérőszámok jelenleg nem jelennek meg a Azure Portalban. Így attól függően, hogy mikor engedélyezi a metrikákat, akár két órát is igénybe vehet, hogy megjelenjenek a metrikák adatai.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>A AzCopy használata a kiszolgálói naplók helyi könyvtárba másolásához

Az Azure Storage a kiszolgáló naplójának adatait a blobokra írja, míg a metrikák a táblákba íródnak. A log Blobok a Storage-fiók jól ismert `$logs` tárolójában érhetők el. A naplófájlok neve hierarchikusan van elnevezve év, hónap, nap és óra szerint, így könnyen megtalálhatja a vizsgálni kívánt időtartományt. Például a `storagesample` fiókban a 01/02/2015-es naplófájlok tárolója, 8-9 am, a következő: `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800` . A tárolóban található egyedi Blobok egymás után, a következővel kezdődnek: `000000.log` .

A AzCopy parancssori eszköz használatával letöltheti ezeket a kiszolgálóoldali naplófájlokat a helyi gépen a kívánt helyre. A következő parancs használatával például letöltheti a naplófájlokat a 2015 január 2. és a (z) mappához tartozó blob-műveletekhez, a `C:\Temp\Logs\Server` helyére pedig `<storageaccountname>` a Storage-fiók nevét használhatja:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

A AzCopy letölthető az [Azure letöltések](https://azure.microsoft.com/downloads/) oldalán. A AzCopy használatával kapcsolatos részletekért lásd [az adatok átvitele a AzCopy parancssori segédprogrammal](storage-use-azcopy.md)című témakört.

A kiszolgálóoldali naplók letöltésével kapcsolatos további információkért lásd a [Storage-naplózási naplók adatainak letöltése](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)című témakört.

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>A naplófájlok elemzésének használata a Microsoft Message Analyzer használatával

A Microsoft Message Analyzer eszköz a protokollok közötti üzenetkezelési forgalom, események és más rendszer-vagy alkalmazás-üzenetek rögzítésére, megjelenítésére és elemzésére szolgál hibaelhárítási és diagnosztikai helyzetekben. Az üzenetsor lehetővé teszi az adatok betöltését, összesítését és elemzését a naplóból és a mentett nyomkövetési fájlokból. További információ az üzenetsor-elemzőről: a [Microsoft Message Analyzer üzemeltetési útmutatója](https://technet.microsoft.com/library/jj649776.aspx).

Az üzenetsor olyan Azure Storage-eszközöket tartalmaz, amelyek segítenek a kiszolgáló, az ügyfél és a hálózati naplók elemzésében. Ebből a szakaszból megtudhatja, hogyan használhatja ezeket az eszközöket a tárolási naplók alacsony százalékos sikerességi hibáinak megoldásához.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Az üzenetsor és az Azure Storage-eszközök letöltése és telepítése

1. Töltse le az [üzenetsor-elemzőt](https://docs.microsoft.com/message-analyzer/installing-and-upgrading-message-analyzer).
2. Indítsa el az üzenetsor-elemzőt.
3. Az **eszközök** menüben válassza ki az **Asset Manager**elemet. Az **Asset Manager** párbeszédpanelen válassza a **letöltések**, majd a szűrés az **Azure Storage**-ban lehetőséget. Az Azure Storage-eszközöket az alábbi ábrán látható módon fogja látni.
4. Az Azure Storage-eszközök telepítéséhez kattintson az **összes megjelenített elem szinkronizálása** elemre. Az elérhető eszközök a következők:
   * **Azure Storage-színszabályok:** Az Azure tárolási színszabályai lehetővé teszik olyan speciális szűrők definiálását, amelyek szín-, szöveg-és betűstílust használnak a nyomkövetésben megadott adatokat tartalmazó üzenetek kiemeléséhez.
   * **Azure Storage-diagramok:** Az Azure Storage-diagramok előre definiált diagramok, amelyek a Graph-kiszolgáló naplófájljait használják. Vegye figyelembe, hogy az Azure Storage-diagramok jelenleg csak az Analysis Gridbe tölthetők be.
   * **Azure Storage-elemzők:** Az Azure Storage-elemzők elemzik az Azure Storage-ügyfelet, a kiszolgálót és a HTTP-naplókat, hogy azok megjelenjenek az Analysis Gridben.
   * **Azure Storage-szűrők:** Az Azure Storage-szűrők előre definiált feltételek, amelyek segítségével lekérdezheti az adatait az Analysis Gridben.
   * **Azure Storage megtekintési elrendezések:** Az Azure Storage View elrendezések előre definiált oszlopos elrendezések és Csoportosítások az Analysis Gridben.
5. Az eszközök telepítése után indítsa újra az üzenetsor-elemzőt.

![Message Analyzer – Asset Manager](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Telepítse az oktatóanyaghoz tartozó összes Azure Storage-eszközt.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Naplófájlok importálása az üzenetsor-Elemzőbe

Az összes mentett naplófájlt (kiszolgálóoldali, ügyféloldali és hálózati) egyetlen munkamenetbe importálhatja a Microsoft Message Analyzer Analysis szolgáltatásban.

1. A Microsoft Message Analyzer **fájl** menüjében kattintson az **új munkamenet**elemre, majd az **üres munkamenet**elemre. Az **új munkamenet** párbeszédpanelen adja meg az elemzési munkamenet nevét. A **munkamenet részletei** panelen kattintson a **Files (fájlok** ) gombra.
2. Az üzenetsor által generált hálózati nyomkövetési adatok betöltéséhez kattintson a **fájlok hozzáadása**gombra, keresse meg azt a helyet, ahová a. matp fájlt mentette a webes nyomkövetési munkamenetből, válassza ki a. matp fájlt, és kattintson a **Megnyitás**gombra.
3. A kiszolgálóoldali naplózási adat betöltéséhez kattintson a **fájlok hozzáadása**lehetőségre, keresse meg azt a helyet, ahová a kiszolgálóoldali naplókat letöltötte, válassza ki az elemezni kívánt időtartomány naplófájljait, majd kattintson a **Megnyitás**gombra. Ezután a **munkamenet részletei** panelen állítsa be a **szöveges napló konfigurációját** az egyes kiszolgálóoldali naplófájlok **AzureStorageLog** , és győződjön meg arról, hogy a Microsoft Message Analyzer helyesen tudja elemezni a naplófájlt.
4. Az ügyféloldali naplózási adat betöltéséhez kattintson a **fájlok hozzáadása**lehetőségre, keresse meg a helyet, ahová mentette az ügyféloldali naplókat, jelölje ki az elemezni kívánt naplófájlokat, és kattintson a **Megnyitás**gombra. Ezután a **munkamenet részletei** panelen állítsa be a **szöveges napló konfigurációját** az egyes ügyféloldali naplófájlok **AzureStorageClientDotNetV4** , és győződjön meg arról, hogy a Microsoft Message Analyzer helyesen tudja elemezni a naplófájlt.
5. Kattintson a **Start** gombra az **új munkamenet** párbeszédablakban a naplófájlok betöltéséhez és elemzéséhez. A naplózási adatai az üzenetsor-elemzési rácsban jelennek meg.

Az alábbi képen egy kiszolgáló-, ügyfél-és hálózati nyomkövetési naplófájlokkal konfigurált példa-munkamenet látható.

![Az üzenetsor-elemző munkamenetének konfigurálása](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Vegye figyelembe, hogy az üzenetsor betölti a naplófájlokat a memóriába. Ha nagy mennyiségű naplózási adattal rendelkezik, érdemes szűrnie, hogy az üzenetsor legjobb teljesítményét kapja meg.

Először határozza meg, hogy milyen időkeretet szeretne áttekinteni, és a lehető legkisebbre szeretné tartani ezt az időkeretet. Sok esetben érdemes áttekinteni a perc vagy óra időtartamát. Importálja az igényeinek megfelelő naplók legkisebb készletét.

Ha továbbra is nagy mennyiségű naplózási adattal rendelkezik, érdemes megadnia egy munkamenet-szűrőt a naplózási adatai szűréséhez a betöltés előtt. A **munkamenet-szűrő** mezőben válassza a **könyvtár** gombot egy előre meghatározott szűrő kiválasztásához. Válassza például az Azure Storage-szűrők **globális Időszűrője** lehetőséget az időintervallum szűréséhez. A szűrési feltételek szerkesztésével megadhatja a megtekinteni kívánt intervallum kezdő és záró időbélyegét. Egy adott állapotkódot is szűrheti; Megadhatja például, hogy csak olyan naplóbejegyzéseket töltsön be, amelyekben az állapotkód 404.

További információ a naplófájlok importálásáról a Microsoft Message Analyzer szolgáltatásban: [üzenet adatainak beolvasása](https://technet.microsoft.com/library/dn772437.aspx) a TechNeten.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>A naplófájl-adatértékek összekapcsolásához használja az ügyfél-kérelem AZONOSÍTÓját

Az Azure Storage ügyféloldali kódtára automatikusan létrehoz egy egyedi ügyfél-kérelem-azonosítót minden kérelemhez. Ez az érték az ügyfél-naplóba, a kiszolgálói naplóba és a hálózati nyomkövetésbe íródik, így a segítségével az üzenetsor összekapcsolhatók az összes három naplóban. Az ügyfél-kérelem azonosítójával kapcsolatos további információkért tekintse meg az [ügyfél-kérelem azonosítóját](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) .

Az alábbi szakaszok azt ismertetik, hogyan használhatók az előre konfigurált és az egyéni elrendezési nézetek az ügyfél-kérelem azonosítója alapján történő korrelációhoz és csoportosításhoz.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Az elemzési rácsban megjelenítendő nézet elrendezésének kiválasztása

Az üzenetsor tárolási eszközei közé tartoznak az Azure Storage View elrendezések, amelyek előre konfigurált nézetek, amelyek segítségével a különböző forgatókönyvekhez hasznos csoportosításokkal és oszlopokkal jeleníthetők meg az adatok. Létrehozhat egyéni nézet-elrendezéseket is, és mentheti újra.

Az alábbi képen látható az **elrendezés megtekintése** menü, amely a nézet **elrendezése** elemre kattintva érhető el az eszköztár menüszalagján. Az Azure Storage-elrendezések megtekintése az **Azure Storage** csomópont alatt, a menüben van csoportosítva. A keresőmezőbe rákereshet úgy, `Azure Storage` hogy csak az Azure Storage View elrendezéseket szűrje. Azt is megteheti, hogy kijelöli a csillagot a nézet elrendezés mellett, hogy kedvencként jelenjen meg, és megjeleníti azt a menü tetején.

![Elrendezési menü megtekintése](./media/storage-e2e-troubleshooting/view-layout-menu.png)

A kezdéshez válassza a **ügyfélkérelem és modul szerint csoportosítva**lehetőséget. Ebben a nézetben az elrendezési csoportok mindhárom napló adatait először az ügyfél-kérelmek AZONOSÍTÓja, majd a forrás naplófájlja (vagy az üzenet-elemző **modul** ) alapján csoportosítják. Ebben a nézetben megtekintheti egy adott ügyfél-kérelem AZONOSÍTÓját, és az ügyfél-kérelem AZONOSÍTÓjának mindhárom naplófájljában található adatokat.

Az alábbi képen látható, hogy ez az elrendezési nézet a minta log-adatként van alkalmazva, és az oszlopok egy részhalmaza látható. Láthatja, hogy egy adott ügyfél-kérelem azonosítója esetében az Analysis Grid az ügyfél naplójának, a kiszolgálói naplónak és a hálózati nyomkövetésnek az adatait jeleníti meg.

![Az Azure Storage nézet elrendezése](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> A különböző naplófájlok eltérő oszlopokkal rendelkeznek, így ha több naplófájlból származó adatok jelennek meg az Analysis Gridben, előfordulhat, hogy egyes oszlopok nem tartalmaznak egy adott sor adatait. A fenti képen például az ügyfél naplófájljai nem jelenítik meg az **időbélyeg**, a **TimeElapsed**, a **forrás**és a **cél** oszlophoz tartozó összes adatát, mivel ezek az oszlopok nem szerepelnek az ügyfél naplójában, de léteznek a hálózat nyomkövetésében. Hasonlóképpen, az **időbélyegző** oszlop a kiszolgáló naplójából jeleníti meg az időbélyegző adatait, de nem jeleníti meg a **TimeElapsed**, a **forrás**és a **cél** oszlop adatait, amelyek nem részei a kiszolgálói naplónak.
>
>

Az Azure Storage View elrendezések használata mellett megadhatja és mentheti a saját nézet elrendezéseit is. Kiválaszthat más kívánt mezőket is az adatcsoportosításhoz, és mentheti a csoportosítást az egyéni elrendezés részeként is.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Színszabályok alkalmazása az Analysis Gridre

A tárolási eszközök olyan színszabályokat is tartalmaznak, amelyek vizuális eszközöket biztosítanak a különböző típusú hibák azonosításához az Analysis Gridben. Az előre definiált színszabályok HTTP-hibákra vonatkoznak, így csak a kiszolgálói napló és a hálózati nyomkövetés esetében jelennek meg.

A színszabályok alkalmazásához válassza ki a **színszabályok** elemet az eszköztár menüszalagján. Az Azure Storage színszabályait a menüben tekintheti meg. Az oktatóanyaghoz válassza ki az **ügyféloldali hibákat (StatusCode 400 és 499 között)**, ahogy az alábbi képen is látható.

![Az Azure Storage nézet elrendezése](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Az Azure Storage színszabályainak használata mellett a saját színszabályait is megadhatja és mentheti.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Naplófájlok csoportosítása és szűrése a 400-es tartománybeli hibák kereséséhez

Ezután csoportosítjuk és szűrheti a naplófájlokat, hogy megtalálja az összes hibát a 400 tartományban.

1. Keresse meg a **statuscode** oszlopot az Analysis Gridben, kattintson a jobb gombbal az oszlop fejlécére, majd válassza a **csoport**lehetőséget.
2. Ezután a **ügyfélkérelem** oszlophoz tartozó csoportot. Láthatja, hogy az Analysis Gridben lévő adatai mostantól az állapotkód és az ügyfél-kérelem azonosítója szerint vannak rendszerezve.
3. Jelenítse meg a szűrő megjelenítése ablakot, ha még nem jelenik meg. Az eszköztár menüszalagján válassza ki az **eszköz Windows**, majd a **szűrő megtekintése**lehetőséget.
4. Ha szűrni szeretné a naplózási adatnaplót, hogy csak a 400-alapú hibák jelenjenek meg, adja hozzá a következő szűrési feltételeket a **nézet szűrő** ablakához, majd kattintson az **alkalmaz**gombra:

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

Az alábbi képen a Csoportosítás és a szűrő eredményei láthatók. A **ügyfélkérelem** mező kibontása a 409-es állapotkód csoportosítása alatt, például egy olyan műveletet mutat be, amely az adott állapotkódot eredményezte.

![Az Azure Storage nézet elrendezése](./media/storage-e2e-troubleshooting/400-range-errors1.png)

A szűrő alkalmazása után láthatja, hogy az ügyfél naplójában szereplő sorok ki vannak zárva, mivel az ügyfél naplója nem tartalmaz **statuscode** oszlopot. A kezdéshez tekintse át a kiszolgáló és a hálózat nyomkövetési naplóit, hogy megkeresse az 404-es hibákat, majd visszatérünk az ügyfél-naplóba, hogy megvizsgáljuk az általuk vezetett ügyfél-műveleteket.

> [!NOTE]
> Szűrheti a **statuscode** oszlopot, és továbbra is megjelenítheti mind a három napló adatait, beleértve az ügyfél naplóját is, ha olyan kifejezést ad hozzá a szűrőhöz, amely olyan naplóbejegyzéseket tartalmaz, amelyekben az állapotkód null értékű. A szűrési kifejezés létrehozásához használja a következőt:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Ez a szűrő visszaadja az ügyfél naplójának összes sorát, és csak a kiszolgálói naplóból és a HTTP-naplóból származó sorokat, ahol az állapotkód nagyobb, mint 400. Ha az ügyfél-kérelem azonosítója és modulja szerint csoportosítja az elrendezést, akkor a naplóbejegyzések között kereshet vagy görgetheti azokat, ahol mindhárom napló képviselteti.

### <a name="filter-log-data-to-find-404-errors"></a>A naplófájlok szűrése a 404 hibák kereséséhez

A tárolási eszközök olyan előre definiált szűrőket tartalmaznak, amelyekkel leszűkítheti a naplózott adatok körét, és megkeresheti a keresett hibákat vagy trendeket. Ezután két előre definiált szűrőt fogunk alkalmazni: az egyiket, amely a kiszolgáló és a hálózat nyomkövetési naplóit a 404-es hibákra szűri, és egy adott időtartományon belül szűri az adathalmazt.

1. Jelenítse meg a szűrő megjelenítése ablakot, ha még nem jelenik meg. Az eszköztár menüszalagján válassza ki az **eszköz Windows**, majd a **szűrő megtekintése**lehetőséget.
2. A szűrő megtekintése ablakban válassza a **könyvtár**lehetőséget, és keresse meg `Azure Storage` Az Azure Storage-szűrőket. Válassza ki az **összes naplóban az 404 (nem található) üzenetek**szűrőjét.
3. Jelenítse meg újra a **könyvtár** menüt, és keresse meg és válassza ki a **globális időszűrőt**.
4. Szerkessze a szűrőben látható időbélyegeket a megtekinteni kívánt tartományhoz. Ezzel a megoldással szűkíthető az elemezni kívánt adattartomány.
5. A szűrőnek az alábbi példához hasonlóan kell megjelennie. Az **alkalmaz** gombra kattintva alkalmazza a szűrőt az Analysis gridre.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Az Azure Storage nézet elrendezése](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>A napló adatai elemzése

Most, hogy csoportosított és szűrte az adatokat, megvizsgálhatja a 404-es hibát okozó egyes kérések részleteit. Az aktuális nézet elrendezésében az ügyfél-kérelem azonosítója, majd a naplózási forrás szerint vannak csoportosítva. Mivel a StatusCode mező 404-as értékkel rendelkezik, a kérelmek szűrése csak a kiszolgáló és a hálózat nyomkövetési adatát fogja látni, nem az ügyfél naplófájljait.

Az alábbi képen egy adott kérelem jelenik meg, amelyben egy Get blob művelet 404, mert a blob nem létezik. Vegye figyelembe, hogy egyes oszlopok el lettek távolítva a normál nézetből a releváns adatok megjelenítéséhez.

![Szűrt kiszolgáló és hálózati nyomkövetési naplók](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Ezután összekapcsoljuk az ügyfél-kérelem AZONOSÍTÓját az ügyfél naplófájljaival, hogy meglássuk, milyen műveleteket hajtottak végre az ügyfél a hiba bekövetkezésekor. A munkamenet új Analysis Grid nézetével megtekintheti az ügyfél-naplózási adataikat, amelyek a második lapon nyílnak meg:

1. Először másolja a **ügyfélkérelem** mező értékét a vágólapra. Ezt úgy teheti meg, hogy kijelöli a sort, megkeresi a **ügyfélkérelem** mezőt, a jobb gombbal az adatértékre kattint, és kiválasztja a **ügyfélkérelem másolása**lehetőséget.
2. Az eszköztár menüszalagján válassza az **Új megjelenítő**lehetőséget, majd az **elemzési rács** lehetőséget egy új lap megnyitásához. Az új lapon a naplófájlokban lévő összes, csoportosítási, szűrési vagy színszabályok nélküli adatfájl látható.
3. Az eszköztár menüszalagján válassza az **elrendezés megtekintése**lehetőséget, majd az **Azure Storage** szakaszban válassza az **összes .net-ügyfél oszlopot** . Ez a nézet elrendezés az ügyfél naplójának adatait, valamint a kiszolgáló és a hálózat nyomkövetési naplóit jeleníti meg. Alapértelmezés szerint a **MessageNumber** oszlop alapján rendezi a rendszer.
4. Ezután keresse meg az ügyfél-kérelem AZONOSÍTÓjának eseménynaplóját. Az eszköztár menüszalagján válassza az **üzenetek keresése**lehetőséget, majd adjon meg egy egyéni szűrőt az ügyfél-kérelem azonosítójában a **Keresés** mezőben. Használja ezt a szintaxist a szűrőhöz, és adja meg a saját ügyfél-kérelmének AZONOSÍTÓját:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Az üzenet-elemző megkeresi és kiválasztja az első naplóbejegyzést, ahol a keresési feltételek megfelelnek az ügyfél-kérelem AZONOSÍTÓjának. Az ügyfél naplójában az ügyfél-kérelmek AZONOSÍTÓjának több bejegyzése is van, ezért érdemes lehet csoportosítani őket a **ügyfélkérelem** mezőben, így könnyebben megtekintheti őket. Az alábbi képen a megadott ügyfél-kérelem AZONOSÍTÓjának összes üzenete látható az ügyfél naplójában.

![404 hibát jelző ügyfél naplója](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

A két lapon megjelenő elrendezések nézetben látható módon elemezheti a kérelmeket, és meghatározhatja, hogy mi okozhatta a hibát. Megtekintheti azokat a kéréseket is, amelyek megelőzték, hogy egy korábbi esemény a 404-es hibát eredményezte-e. Megtekintheti például az ügyfél-kérelem AZONOSÍTÓját megelőző ügyfél-naplóbejegyzések listáját annak megállapításához, hogy a blob törölve lett-e, vagy ha a hiba oka, hogy az ügyfélalkalmazás egy Createifnotexists metódust API-t hív meg egy tárolón vagy blobon. Az ügyfél naplójában a blob címe megtalálható a **Leírás** mezőben. a kiszolgáló és a hálózat nyomkövetési naplóiban ez az információ az **Összefoglalás** mezőben jelenik meg.

Ha ismeri a 404-es hibát eredményező blob címeit, további vizsgálatot is megvizsgálhat. Ha az ugyanazon a blobon lévő műveletekhez kapcsolódó egyéb üzenetekhez keres bejegyzéseket, megtekintheti, hogy az ügyfél korábban törölte-e az entitást.

## <a name="analyze-other-types-of-storage-errors"></a>Más típusú tárolási hibák elemzése

Most, hogy már ismeri az üzenetsor elemzését a naplózási adatai elemzéséhez, más típusú hibákat is elemezheti az elrendezések, a színszabályok és a keresés/szűrés használatával. Az alábbi táblázatok felsorolják az esetlegesen felmerülő problémákat, valamint azokat a szűrési feltételeket, amelyeket a kereséshez használhat. A szűrők és az üzenet-elemző szűrési nyelv létrehozásával kapcsolatos további információkért lásd: [üzenet adatainak szűrése](https://technet.microsoft.com/library/jj819365.aspx).

| A vizsgálathoz... | Szűrési kifejezés használata... | A kifejezés a naplóra vonatkozik (ügyfél, kiszolgáló, hálózat, összes) |
| --- | --- | --- |
| Váratlan késések az üzenetek várólistán való kézbesítése során |A AzureStorageClientDotNetV4. Description a következőt tartalmazza: "újrapróbálkozási sikertelen művelet". |Ügyfél |
| HTTP-növekedés a Percentthrottlingerror értéket mutatnak |HTTP. Response. StatusCode = = 500 &#124;&#124; HTTP. Response. StatusCode = = 503 |Network (Hálózat) |
| Növekedés a Percenttimeouterror értéket mutatnak |HTTP. Response. StatusCode = = 500 |Network (Hálózat) |
| Növekedés a Percenttimeouterror értéket mutatnak (összes) |* StatusCode = = 500 |Mind |
| Növekedés a Percentnetworkerror értéket mutatnak |AzureStorageClientDotNetV4. EventLogEntry. Level < 2 |Ügyfél |
| HTTP 403 (Tiltott) hibaüzenetek |HTTP. Response. StatusCode = = 403 |Network (Hálózat) |
| HTTP 404 (Nem található) hibaüzenetek |HTTP. Response. StatusCode = = 404 |Network (Hálózat) |
| 404 (mind) |* StatusCode = = 404 |Mind |
| Egy közös hozzáférésű jogosultságkód (SAS) engedélyezési problémája |AzureStorageLog. RequestStatus = = "SASAuthorizationError" |Network (Hálózat) |
| HTTP 409 (Ütközés) hibaüzenetek |HTTP. Response. StatusCode = = 409 |Network (Hálózat) |
| 409 (mind) |* StatusCode = = 409 |Mind |
| Az alacsony PercentSuccess-vagy elemzési naplóbejegyzések olyan műveletekkel rendelkeznek, amelyek tranzakciós állapota ClientOtherErrors |AzureStorageLog. RequestStatus = = "ClientOtherError" |Kiszolgáló |
| Nyéki figyelmeztetés |((AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS) > (AzureStorageLog. ServerLatencyMS * 1,5)) és (AzureStorageLog. RequestPacketSize <1460) és (AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS >= 200) |Kiszolgáló |
| A kiszolgáló és a hálózati naplók tartománya |#Timestamp >= 2014-10-20T16:36:38 és #Timestamp <= 2014-10-20T16:36:39 |Kiszolgáló, hálózat |
| Időtartomány a kiszolgáló naplófájljaiban |AzureStorageLog. timestamp >= 2014-10-20T16:36:38 és AzureStorageLog. timestamp <= 2014-10-20T16:36:39 |Kiszolgáló |

## <a name="next-steps"></a>További lépések

Az Azure Storage-ban elérhető végpontok közötti forgatókönyvekkel kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Tárfiók monitorozása az Azure Portalon](storage-monitor-storage-account.md)
* [Adatok átvitele a AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [A Microsoft Message Analyzer üzemeltetési útmutatója](https://technet.microsoft.com/library/jj649776.aspx)
