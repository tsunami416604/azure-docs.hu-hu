---
title: Transport Layer Security (TLS) minimálisan szükséges verziójának konfigurálása a Storage-fiókhoz
titleSuffix: Azure Storage
description: Konfigurálja a Storage-fiókot úgy, hogy az Azure Storage-ra irányuló ügyfelek számára a Transport Layer Security (TLS) minimális verzióját igényli.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209593"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>Transport Layer Security (TLS) minimálisan szükséges verziójának konfigurálása a Storage-fiókhoz

Az ügyfélalkalmazás és az Azure Storage-fiók közötti kommunikáció titkosítása Transport Layer Security (TLS) használatával történik. A TLS egy szabványos titkosítási protokoll, amely biztosítja az adatvédelem és az adatok integritását az ügyfelek és a szolgáltatások között az interneten keresztül. A TLS-vel kapcsolatos további információkért lásd: [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Az Azure Storage jelenleg a TLS protokoll három verzióját támogatja: 1,0, 1,1 és 1,2. A TLS 1,2 a TLS legbiztonságosabb verziója. Az Azure Storage TLS 1,2-t használ a nyilvános HTTPs-végpontokon, de a TLS 1,0 és a TLS 1,1 továbbra is támogatott a visszamenőleges kompatibilitás érdekében.

Alapértelmezés szerint az Azure Storage-fiókok lehetővé teszik, hogy az ügyfelek a TLS, TLS 1,0 vagy újabb rendszer legrégebbi verziójával küldjék el és fogadják az adatfogadást. A szigorúbb biztonsági intézkedések betartatásához beállíthatja a Storage-fiókját, hogy megkövetelje, hogy az ügyfelek a TLS újabb verziójával küldjék el és fogadják az adataikat. Ha egy Storage-fióknak a TLS minimális verziójára van szüksége, akkor a korábbi verzióval végrehajtott kérelmek sikertelenek lesznek.

Ez a cikk azt ismerteti, hogyan konfigurálható egy olyan Storage-fiók, amely megköveteli, hogy az ügyfelek a TLS minimális verziójával küldjék el a kérelmeket. További információ a TLS adott verziójának megadásáról az ügyfélalkalmazások kérelmének elküldésekor: [Transport Layer Security (TLS) konfigurálása ügyfélalkalmazás számára](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Az ügyfélalkalmazások által használt TLS-verzió észlelése

Ha a Storage-fiókhoz minimális TLS-verziót kényszerít ki, akkor az olyan ügyfelektől érkező kérések elutasítása, amelyek a TLS egy korábbi verziójával adatokat küldenek. Ha meg szeretné tudni, hogy a minimális TLS-verzió konfigurálása milyen hatással lehet az ügyfélalkalmazások használatára, a Microsoft javasolja, hogy engedélyezze a naplózást az Azure Storage-fiókhoz, és elemezze a naplókat egy idő elteltével a TLS-ügyfélalkalmazások által használt verziók meghatározásához.

A kérelmek Azure Storage-fiókba való naplózásához és az ügyfél által használt TLS-verzió meghatározásához használhatja az Azure Storage-naplózást Azure Monitor (előzetes verzió). További információ: az [Azure Storage figyelése](monitor-storage.md).

Az Azure Storage Azure Monitor támogatja a naplózási lekérdezések használatát a naplófájlok elemzéséhez. A naplók lekérdezéséhez használhat Azure Log Analytics munkaterületet. További információ a naplók lekérdezéséről [: oktatóanyag: log Analytics lekérdezések első lépései](../../azure-monitor/log-query/get-started-portal.md).

Az Azure Storage-beli adatAzure Monitor és az Azure Log Analytics való elemzéséhez először létre kell hoznia egy diagnosztikai beállítást, amely meghatározza, hogy milyen típusú kérelmeket és milyen tárolási szolgáltatásokat szeretne naplózni. Ha diagnosztikai beállítást szeretne létrehozni a Azure Portalban, kövesse az alábbi lépéseket:

1. Regisztráljon az [Azure Storage naplózási Azure monitor előzetes](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)verziójában.
1. Hozzon létre egy új Log Analytics munkaterületet az Azure Storage-fiókot tartalmazó előfizetésben. A Storage-fiók naplózásának konfigurálása után a naplók a Log Analytics munkaterületen lesznek elérhetők. További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../../azure-monitor/learn/quick-create-workspace.md).
1. Az Azure Portalon nyissa meg a tárfiókot.
1. A figyelés szakaszban válassza a **diagnosztikai beállítások (előzetes verzió)** lehetőséget.
1. Válassza ki azt az Azure Storage-szolgáltatást, amelyhez be szeretné jelentkezni a kérelmeket. Például válassza a **blob** lehetőséget a blob Storage-ba irányuló kérelmek naplózásához.
1. Válassza a **diagnosztikai beállítás hozzáadása**lehetőséget.
1. Adja meg a diagnosztikai beállítás nevét.
1. A **Kategória részletei**területen, a **napló** szakaszban válassza ki a naplózandó kérelmek típusát. Az olvasási, írási és törlési kérelmek naplózása is megadható. Ha például a **StorageRead** és a **StorageWrite** lehetőséget választja, a a kiválasztott szolgáltatásba írja be az olvasási és írási kérelmeket.
1. A **célhely részletei**területen válassza **a Küldés log Analyticsba**lehetőséget. Válassza ki az előfizetését és a korábban létrehozott Log Analytics munkaterületet, ahogy az az alábbi képen is látható.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="A naplózási kérelmek diagnosztikai beállításainak létrehozását bemutató képernyőkép":::

Miután létrehozta a diagnosztikai beállítást, a rendszer a Storage-fiókra irányuló kérelmeket a beállításnak megfelelően naplózza. További információ: [diagnosztikai beállítás létrehozása az erőforrás-naplók és-metrikák az Azure-ban való összegyűjtéséhez](../../azure-monitor/platform/diagnostic-settings.md).

A Azure Monitor Azure Storage-naplókban elérhető mezőkre vonatkozó hivatkozásokat itt tekintheti meg: [erőforrás-naplók (előzetes verzió)](monitor-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Naplózott kérelmek lekérdezése TLS-verzió alapján

Az Azure Storage-naplók Azure Monitor tartalmazzák a TLS-verziót, amely a kérések egy Storage-fiókba küldésére szolgál. A **TlsVersion** tulajdonság használatával ellenőrizhető a naplózott kérelem TLS-verziója.

Ha az elmúlt 7 nap naplóit szeretné lekérni, és azt, hogy hány kérelem érkezett a blob Storage-hoz a TLS egyes verzióival, nyissa meg Log Analytics munkaterületét. Ezután illessze be a következő lekérdezést egy új napló-lekérdezésbe, és futtassa. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Az eredmények a TLS egyes verzióira vonatkozó kérelmek számának számát mutatják:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Képernyőfelvétel a log Analytics-lekérdezés eredményeiről a TLS-verzió visszaküldéséhez":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Naplózott kérelmek lekérdezése a hívó IP-címe és a felhasználói ügynök fejléce alapján

Az Azure Storage Azure Monitor a hívó IP-címét és a felhasználói ügynök fejlécét is tartalmazza, amellyel kiértékelheti, hogy mely ügyfélalkalmazások férhetnek hozzá a Storage-fiókhoz. Ezeket az értékeket elemezve eldöntheti, hogy az ügyfélalkalmazások frissíteni kell-e a TLS újabb verziójának használatára, vagy az ügyfél kérelmének elutasításához, ha az nem a TLS minimális verziójával lett elküldve.

Az elmúlt 7 nap naplóinak lekéréséhez, és a TLS 1,2-et megelőzően a TLS-verzióval rendelkező ügyfelek azonosításához illessze be a következő lekérdezést egy új napló-lekérdezésbe, és futtassa. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>A TLS minimális verziójának konfigurálása egy fiókhoz

A Storage-fiók minimális TLS-verziójának konfigurálásához használja a Azure Portal vagy az Azure CLI-t a fiók **minimumTlsVersion** -verziójának megadásához. Ez a tulajdonság minden olyan Storage-fiókhoz elérhető, amely a Azure Resource Manager telepítési modellel lett létrehozva. További információ: a [Storage-fiók áttekintése](storage-account-overview.md).

# <a name="portal"></a>[Portál](#tab/portal)

A Azure Portalhoz tartozó Storage-fiók minimális TLS-verziójának konfigurálásához kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Válassza ki a **konfigurációs** beállítást.
1. A **TLS minimális verziója**területen a legördülő listából válassza ki az ebben a Storage-fiókban található adatok eléréséhez szükséges TLS minimális verzióját az alábbi ábrán látható módon.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="A TLS minimális verziójának konfigurálását bemutató képernyőkép a Azure Portal":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel rendelkező Storage-fiókok minimális TLS-verziójának konfigurálásához először kérje le a Storage-fiók erőforrás-AZONOSÍTÓját az az [Resource show](/cli/azure/resource#az-resource-show) parancs meghívásával. Ezután hívja meg az az [Resource Update](/cli/azure/resource#az-resource-update) parancsot a Storage-fiók **minimumTlsVersion** tulajdonságának beállításához. A **minimumTlsVersion** érvényes értékei a következők: `TLS1_0` `TLS1_1` és `TLS1_2` .

A következő példa a TLS minimális verzióját 1,2-re állítja. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> A Storage-fiók minimális TLS-verziójának frissítése után akár 30 másodpercig is eltarthat, amíg a módosítás teljesen propagálva lesz.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>A fiók minimálisan szükséges TLS-verziójának megkeresése

A Storage-fiókhoz konfigurált minimálisan szükséges TLS-verzió meghatározásához tekintse meg a Azure Resource Manager **minimumTlsVersion** tulajdonságot. Ha egy nagy számú Storage-fiók esetében szeretné ezt a tulajdonságot egyszerre használni, használja az Azure Resource Graph Explorert.

A **minimumTlsVersion** tulajdonság alapértelmezés szerint nincs beállítva, és nem ad vissza értéket, amíg explicit módon be nem állítja azt. A Storage-fiók alapértelmezés szerint a TLS 1,0-es vagy újabb verziójával küldött kéréseket engedélyezi, ha a tulajdonság értéke null.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>Egy Storage-fiók minimálisan szükséges TLS-verziójának megkeresése

Ha az Azure CLI-vel szeretné megtekinteni a minimálisan szükséges TLS-verziót egyetlen Storage-fiókhoz, hívja meg az az [Resource show](/cli/azure/resource#az-resource-show) parancsot és a **minimumTlsVersion** tulajdonság lekérdezését:

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>A minimálisan szükséges TLS-verzió megkeresése a Storage-fiókok készletéhez

Ha szeretné megtekinteni a minimálisan szükséges TLS-verziót egy olyan Storage-fiókon belül, amely optimális teljesítménnyel rendelkezik, használhatja az Azure Resource Graph Explorert a Azure Portal. Ha többet szeretne megtudni az Erőforrásgrafikon Explorer használatáról, tekintse meg a gyors útmutató [: az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerben](/azure/governance/resource-graph/first-query-portal).

A következő lekérdezés futtatása az Erőforrásgrafikon Explorerben a Storage-fiókok listáját adja vissza, és megjeleníti az egyes fiókok minimális TLS-verzióját:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>A TLS minimális verziójának tesztelése egy ügyfélről

Annak ellenőrzéséhez, hogy a Storage-fiók minimálisan szükséges TLS-verziója tiltja-e egy korábbi verzióval kezdeményezett hívásokat, beállíthatja, hogy az ügyfél a TLS egy korábbi verzióját használja. További információ az ügyfélnek a TLS adott verziójának használatára való konfigurálásáról: [Transport Layer Security (TLS) konfigurálása ügyfélalkalmazás számára](transport-layer-security-configure-client-version.md).

Amikor egy ügyfél olyan TLS-verzióval fér hozzá egy Storage-fiókhoz, amely nem felel meg a fiókhoz konfigurált minimális TLS-verziónak, az Azure Storage a 400-as hibakódú hibát (hibás kérés) adja vissza, és egy üzenet jelzi, hogy a használt TLS-verzió nem jogosult kérések létrehozására ezen a Storage-fiókon keresztül.

## <a name="next-steps"></a>További lépések

- [Transport Layer Security (TLS) konfigurálása ügyfélalkalmazás számára](transport-layer-security-configure-client-version.md)
- [Biztonsági javaslatok a blob Storage-hoz](../blobs/security-recommendations.md)
