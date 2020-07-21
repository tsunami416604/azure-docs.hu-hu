---
title: Tárolók és Blobok névtelen nyilvános olvasási hozzáférésének tiltása
titleSuffix: Azure Storage
description: Megtudhatja, hogyan elemezheti a névtelen kérelmeket egy Storage-fiókkal, és hogyan akadályozhatja meg a névtelen hozzáférést a teljes Storage-fiókhoz vagy egy adott tárolóhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 24d726f7600c3ba80833640be8036bf0daa2c014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518724"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Tárolók és Blobok névtelen nyilvános olvasási hozzáférésének tiltása

Az Azure Storage szolgáltatásban tárolt tárolók és Blobok névtelen nyilvános olvasási hozzáférése kényelmes módszer az adatmegosztáshoz, de biztonsági kockázatot jelenthet. Fontos, hogy megfontoltan kezelje a névtelen hozzáférést, és Ismerje meg, hogyan értékelheti ki a névtelen hozzáférést az adataihoz. Az üzemeltetés bonyolultsága, az emberi hiba, vagy a nyilvánosan elérhető adatvédelemmel szembeni rosszindulatú támadás költséges adatvesztést eredményezhet. A Microsoft azt javasolja, hogy csak akkor engedélyezze a névtelen hozzáférést, ha az alkalmazási forgatókönyvhöz szükséges.

Alapértelmezés szerint a megfelelő engedélyekkel rendelkező felhasználók a tárolók és a Blobok nyilvános hozzáférését is konfigurálhatják. A Storage-fiók szintjén megtilthatja az összes nyilvános hozzáférést. Ha letiltja a nyilvános Blobok hozzáférését a Storage-fiókhoz, a fiókban lévő tárolók nem konfigurálhatók nyilvános hozzáférésre. A nyilvános hozzáférésre már konfigurált tárolók már nem fogadják el a névtelen kérelmeket. További információ: [Névtelen nyilvános olvasási hozzáférés beállítása tárolók és Blobok](anonymous-read-access-configure.md)számára.

Ez a cikk bemutatja, hogyan elemezheti a névtelen kérelmeket egy Storage-fiókkal, és hogyan akadályozhatja meg a névtelen hozzáférést a teljes Storage-fiókhoz vagy egy adott tárolóhoz.

## <a name="detect-anonymous-requests-from-client-applications"></a>Ügyfélalkalmazások névtelen kérelmének észlelése

Ha nem engedélyezi a nyilvános olvasási hozzáférést egy Storage-fiókhoz, a rendszer elutasítja a hozzáférést a tárolók és a Blobok számára, amelyek jelenleg nyilvános hozzáférésre vannak konfigurálva. A Storage-fiókhoz való nyilvános hozzáférés letiltása felülbírálja a Storage-fiókban lévő összes tároló nyilvános hozzáférési beállításait. Ha a Storage-fiókhoz való nyilvános hozzáférés nem engedélyezett, a fiókra vonatkozó jövőbeli névtelen kérelmek sikertelenek lesznek.

Annak megismeréséhez, hogy a nyilvános hozzáférés letiltása milyen hatással lehet az ügyfélalkalmazások alkalmazására, a Microsoft javasolja, hogy engedélyezze a naplózást és a metrikákat az adott fiókhoz, és elemezze a névtelen kérések mintáit egy adott időtartamon belül. A metrikák használatával határozza meg a Storage-fiókhoz való névtelen kérések számát, és a naplók segítségével határozza meg, hogy mely tárolók névtelenül érhetők el.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Névtelen kérelmek figyelése Metrikaböngésző

A névtelen kérelmek Storage-fiókba való nyomon követéséhez használja az Azure Metrikaböngésző a Azure Portal. További információ a Metrikaböngészőről: az [Azure Metrikaböngésző első lépései](../../azure-monitor/platform/metrics-getting-started.md).

Kövesse az alábbi lépéseket egy olyan metrika létrehozásához, amely nyomon követi a névtelen kérelmeket:

1. Az Azure Portalon nyissa meg a tárfiókot. A **figyelés** szakaszban válassza a **metrikák**lehetőséget.
1. Válassza a **Metrika hozzáadása** lehetőséget. A **metrika** párbeszédpanelen a következő értékeket kell megadnia:
    1. Hagyja meg a hatókör mezőt a Storage-fiók nevére.
    1. Állítsa a **metrikai névteret** a *blob*értékre. Ez a mérőszám csak a blob Storage-ra vonatkozó kérelmeket fogja jelenteni.
    1. Állítsa a **metrika** mezőt a *tranzakciók*értékre.
    1. Állítsa az **Összesítés** mezőt Sum ( *összeg*) értékre.

    Az új metrika megjeleníti a blob Storage-hoz tartozó tranzakciók számának összegét egy adott időtartamon belül. Az eredményül kapott metrika a következő képen látható módon jelenik meg:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="A metrika a blob-tranzakciók összegére való konfigurálását bemutató képernyőkép":::

1. Ezután a **szűrő hozzáadása** gombra kattintva hozzon létre egy szűrőt a névtelen kérelmekre vonatkozó mérőszámban.
1. A **szűrő** párbeszédpanelen a következő értékeket kell megadnia:
    1. Állítsa a **tulajdonság** értékét *hitelesítésre*.
    1. Állítsa az **operátor** mezőt az egyenlőségjel (=) értékre.
    1. Állítsa a **Values (értékek** ) mezőt *Névtelen*értékre.
1. A jobb felső sarokban válassza ki azt az időintervallumot, amelyre vonatkozóan meg szeretné tekinteni a metrikát. Azt is jelezheti, hogy a kérelmek összesítésének milyen részletességgel kell megjelennie, ha az intervallumokat 1 perc és 1 hónap között adja meg.

A metrika konfigurálása után a névtelen kérelmek megjelennek a gráfon. Az alábbi képen az elmúlt 30 percben összesített névtelen kérelmek láthatók.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="A blob Storage-hoz tartozó összesített névtelen kérelmeket bemutató képernyőkép":::

A riasztási szabályt úgy is konfigurálhatja, hogy értesítést kapjon, ha egy adott számú névtelen kérelem kerül a Storage-fiókjába. További információ: [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Naplók elemzése a névtelen kérelmeket fogadó tárolók azonosításához

Az Azure Storage-naplók rögzítik a Storage-fiókkal kapcsolatos kérések részleteit, beleértve a kérelem engedélyezésének módját is. A naplók elemzésével meghatározhatja, hogy mely tárolók kapják meg a névtelen kérelmeket.

Ha a névtelen kérelmek kiértékeléséhez a kérelmeket az Azure Storage-fiókjába kívánja naplózni, Azure Monitor (előzetes verzió) Azure Storage-naplózást is használhat. További információ: az [Azure Storage figyelése](../common/monitor-storage.md).

Az Azure Storage Azure Monitor támogatja a naplózási lekérdezések használatát a naplófájlok elemzéséhez. A naplók lekérdezéséhez használhat Azure Log Analytics munkaterületet. További információ a naplók lekérdezéséről [: oktatóanyag: log Analytics lekérdezések első lépései](../../azure-monitor/log-query/get-started-portal.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Diagnosztikai beállítás létrehozása a Azure Portalban

Az Azure Storage-beli adatAzure Monitor és az Azure Log Analytics való elemzéséhez először létre kell hoznia egy diagnosztikai beállítást, amely meghatározza, hogy milyen típusú kérelmeket és milyen tárolási szolgáltatásokat szeretne naplózni. Ha diagnosztikai beállítást szeretne létrehozni a Azure Portalban, kövesse az alábbi lépéseket:

1. Regisztráljon az [Azure Storage naplózási Azure monitor előzetes](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)verziójában.
1. Hozzon létre egy új Log Analytics munkaterületet az Azure Storage-fiókot tartalmazó előfizetésben. A Storage-fiók naplózásának konfigurálása után a naplók a Log Analytics munkaterületen lesznek elérhetők. További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../../azure-monitor/learn/quick-create-workspace.md).
1. Az Azure Portalon nyissa meg a tárfiókot.
1. A figyelés szakaszban válassza a **diagnosztikai beállítások (előzetes verzió)** lehetőséget.
1. A blob Storage-ba irányuló kérelmek naplózásához válassza a **blob** elemet.
1. Válassza a **diagnosztikai beállítás hozzáadása**lehetőséget.
1. Adja meg a diagnosztikai beállítás nevét.
1. A **Kategória részletei**területen, a **napló** szakaszban válassza ki a naplózandó kérelmek típusát. Az összes névtelen kérelem olvasási kérelem lesz, ezért a névtelen kérelmek rögzítéséhez válassza a **StorageRead** lehetőséget.
1. A **célhely részletei**területen válassza **a Küldés log Analyticsba**lehetőséget. Válassza ki az előfizetését és a korábban létrehozott Log Analytics munkaterületet, ahogy az az alábbi képen is látható.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="A naplózási kérelmek diagnosztikai beállításainak létrehozását bemutató képernyőkép":::

Miután létrehozta a diagnosztikai beállítást, a rendszer a Storage-fiókra irányuló kérelmeket a beállításnak megfelelően naplózza. További információ: [diagnosztikai beállítás létrehozása az erőforrás-naplók és-metrikák az Azure-ban való összegyűjtéséhez](../../azure-monitor/platform/diagnostic-settings.md).

A Azure Monitor Azure Storage-naplókban elérhető mezőkre vonatkozó hivatkozásokat itt tekintheti meg: [erőforrás-naplók (előzetes verzió)](../common/monitor-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Névtelen kérelmek lekérdezési naplói

Az Azure Storage-naplók a Azure Monitorban tartalmazzák azt a hitelesítési típust, amelyet egy kérésnek a Storage-fiókhoz való elvégzéséhez használt. A napló lekérdezésében szűrje a **AuthenticationType** tulajdonságot a névtelen kérések megtekintéséhez.

Ha a blob Storage-hoz való névtelen kérelmek esetében az elmúlt 7 nap naplóit szeretné lekérni, nyissa meg Log Analytics munkaterületét. Ezután illessze be a következő lekérdezést egy új napló-lekérdezésbe, és futtassa azt:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

A lekérdezésen alapuló riasztási szabályt úgy is konfigurálhat, hogy értesítést kapjon a névtelen kérelmekről. További információ: a [naplók létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Névtelen nyilvános hozzáférés szervizelése

Miután kiértékelte a névtelen kérelmeket tárolók és Blobok számára a Storage-fiókban, a nyilvános hozzáférés korlátozásához vagy megtiltásához is végezhet műveleteket. Ha előfordulhat, hogy a Storage-fiók egyes tárolóinak elérhetőnek kell lenniük a nyilvános hozzáféréshez, a Storage-fiókban lévő egyes tárolók nyilvános hozzáférési beállításait is konfigurálhatja. Ez a lehetőség a nyilvános hozzáférés legrészletesebbebb szabályozását teszi lehetővé. További információ: [a tároló nyilvános hozzáférési szintjének beállítása](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

A fokozott biztonság érdekében a teljes Storage-fiókhoz való nyilvános hozzáférést nem engedélyezheti. A Storage-fiók nyilvános hozzáférési beállítása felülbírálja a fiókban lévő tárolók egyedi beállításait. Ha a Storage-fiókhoz való nyilvános hozzáférést nem engedélyezi, a nyilvános hozzáférés engedélyezésére konfigurált tárolók már nem érhetőek el névtelenül. További információ: [a Storage-fiók nyilvános olvasási hozzáférésének engedélyezése vagy letiltása](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Ha a forgatókönyv megköveteli, hogy bizonyos tárolóknak elérhetőknek kell lenniük a nyilvános hozzáféréshez, célszerű lehet áthelyezni ezeket a tárolókat és a blobokat a nyilvános hozzáférésre fenntartott Storage-fiókokba. Ezután letilthatja a nyilvános hozzáférést bármely más Storage-fiókhoz.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Annak ellenőrzése, hogy a blobhoz való nyilvános hozzáférés nem engedélyezett

Annak ellenőrzéséhez, hogy egy adott blobhoz való nyilvános hozzáférés nem engedélyezett-e, megkísérelheti letölteni a blobot az URL-címén keresztül. Ha a letöltés sikeres, a blob továbbra is nyilvánosan elérhető. Ha a blob nem érhető el nyilvánosan, mert a Storage-fiókhoz való nyilvános hozzáférés nem engedélyezett, akkor egy hibaüzenet jelenik meg, amely jelzi, hogy a nyilvános hozzáférés nem engedélyezett ezen a Storage-fiókon.

Az alábbi példa bemutatja, hogyan használható a PowerShell a blob letöltésére az URL-cím használatával. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Annak ellenőrzése, hogy a tároló nyilvános hozzáférési beállításának módosítása nem engedélyezett-e

Annak ellenőrzéséhez, hogy a tároló nyilvános hozzáférési beállítása nem módosítható-e, miután a Storage-fiókhoz nem engedélyezett a nyilvános hozzáférés, megkísérelheti módosítani a beállítást. A tároló nyilvános hozzáférési beállításának módosítása sikertelen lesz, ha a nyilvános hozzáférés nem engedélyezett a Storage-fiók esetében.

Az alábbi példa bemutatja, hogyan lehet a PowerShell használatával módosítani egy tároló nyilvános hozzáférési beállításait. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Győződjön meg arról, hogy az engedélyezett nyilvános hozzáférésű tároló létrehozása nem engedélyezett

Ha a Storage-fiókhoz nem engedélyezett a nyilvános hozzáférés, akkor nem fog tudni új tárolót létrehozni, amelyen engedélyezve van a nyilvános hozzáférés. Az ellenőrzéshez próbáljon meg egy olyan tárolót létrehozni, amelyen engedélyezve van a nyilvános hozzáférés.

Az alábbi példa bemutatja, hogyan lehet a PowerShell használatával olyan tárolót létrehozni, amelyen engedélyezve van a nyilvános hozzáférés. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

## <a name="next-steps"></a>Következő lépések

- [Névtelen nyilvános olvasási hozzáférés konfigurálása a tárolók és a Blobok számára](anonymous-read-access-configure.md)
- [Nyilvános tárolók és Blobok elérése névtelenül a .NET-tel](anonymous-read-access-client.md)
