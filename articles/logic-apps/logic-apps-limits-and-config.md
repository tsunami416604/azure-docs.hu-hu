---
title: Korlátok és konfiguráció – Azure Logic Apps | Microsoft Docs
description: A Azure Logic Apps szolgáltatási korlátai és konfigurációs értékei
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: e076d6fed8cb3baf6b62dc3ede6ddd34732ed7a2
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562081"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>A Azure Logic Apps korlátai és konfigurációs adatai

Ez a cikk az automatikus munkafolyamatok létrehozásával és futtatásával kapcsolatos korlátozásokat és konfigurációs adatokat ismerteti Azure Logic Apps. Microsoft Flow esetében lásd: [korlátok és konfiguráció a Microsoft Flowban](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definíciók korlátai

Egyetlen logikai alkalmazás definíciójának korlátai:

| Name (Név) | Korlát | Megjegyzések |
| ---- | ----- | ----- |
| Műveletek/munkafolyamat | 500 | A korlát meghosszabbításához szükség szerint beágyazott munkafolyamatokat adhat hozzá. |
| Engedélyezett beágyazási mélység a műveletekhez | 8 | A korlát meghosszabbításához szükség szerint beágyazott munkafolyamatokat adhat hozzá. |
| Munkafolyamatok régiónként/előfizetés szerint | 1,000 | |
| Eseményindítók/munkafolyamat | 10 | Ha kód nézetben dolgozik, a tervező nem |
| Váltás hatóköri eseteinek korlátja | 25 | |
| Változók száma munkafolyamatban | 250 | |
| Karakter/kifejezés | 8,192 | |
| Maximális méret`trackedProperties` | 16 000 karakter |
| `action` Név vagy`trigger` | 80 karakter | |
| Hossz`description` | 256 karakter | |
| Maximális`parameters` | 50 | |
| Maximális`outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Futtatási időtartam és adatmegőrzési korlátok

Egyetlen logikai alkalmazás futtatásának korlátai:

| Name (Név) | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| Futtatás időtartama | 90 nap | 365 nap | Az alapértelmezett korlát módosításához tekintse meg a [Futtatás időtartamának módosítása](#change-duration)című témakört. |
| Tárterület megőrzése | 90 nap a Futtatás kezdő időpontjából | 365 nap | Az alapértelmezett korlát módosításához tekintse meg a [tárterület megőrzésének módosítása](#change-retention)című témakört. |
| Minimális ismétlődési időköz | 1 másodperc | 1 másodperc ||
| Maximális ismétlődési időköz | 500 nap | 500 nap ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>A futtatási időtartam és a tárterület megőrzésének módosítása

A futtatási időtartam és a tárolási megőrzés alapértelmezett korlátjának módosításához kövesse az alábbi lépéseket. Ha a maximális korlát fölé kell lépnie, [lépjen kapcsolatba a Logic apps csapatával](mailto://logicappsemail@microsoft.com) a követelményekkel kapcsolatos segítségért.

1. A Azure Portal a logikai alkalmazás menüjében válassza a **munkafolyamat-beállítások**elemet.

2. A **Futásidejű beállítások**alatt, a **futtatási előzmények megőrzésének napjai** listában válassza az **Egyéni**lehetőséget.

3. Adja meg vagy húzza a csúszkát a kívánt napok számának megadásához.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Párhuzamosságok, ismétlések és letételi korlátok

Egyetlen logikai alkalmazás futtatásának korlátai:

| Name (Név) | Korlát | Megjegyzések |
| ---- | ----- | ----- |
| Egyidejűség kiváltása | * Korlátlan, ha a Egyidejűség vezérlő ki van kapcsolva <p><p>* 25 a Egyidejűség vezérlőelem bekapcsolásának alapértelmezett korlátja, amely a vezérlő bekapcsolását követően nem vonható vissza. Az alapértelmezett értéket 1 és 50 közötti értékre módosíthatja. | Ez a korlát a logikai alkalmazások azon példányainak a maximális számát ismerteti, amelyek egyszerre vagy párhuzamosan futtathatók. <p><p>Ha az alapértelmezett határértéket 1 és 50 közötti értékre szeretné módosítani, a következő témakörben talál további információt: [trigger egyidejűségi korlátjának módosítása](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) vagy az [indító példányok egymás után](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Várakozó futtatások maximális száma | Ha a Egyidejűség-vezérlő be van kapcsolva, a várakozási idő minimális száma 10, az egyidejű futtatások száma (Egyidejűség). A maximális számot akár 100-ig is megváltoztathatja. | Ez a korlát a logikai alkalmazások azon példányainak a maximális számát írja le, amelyek csak akkor tudnak futni, ha a logikai alkalmazás már futtatja az egyidejű példányokat. <p><p>Az alapértelmezett korlát módosításához tekintse meg a [várakozó futtatások korlátjának módosítása](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)című témakört. |
| Foreach | 100,000 | Ez a korlát a "for each" ciklusban feldolgozható tömb elemeinek legnagyobb számát mutatja. <p><p>Nagyobb tömbök szűréséhez használhatja a [lekérdezési műveletet](../connectors/connectors-native-query.md). |
| Foreach Egyidejűség | a 20 az alapértelmezett korlát, ha a Egyidejűség vezérlőelem ki van kapcsolva. Az alapértelmezett értéket 1 és 50 közötti értékre módosíthatja. | Ez a korlát a "minden" hurok-iteráció esetében a legmagasabb szám, amely egyszerre vagy párhuzamosan futtatható. <p><p>Ha az alapértelmezett határértéket 1 és 50 közötti értékre szeretné módosítani, az egyes [](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) "egyidejűségek korlátait vagy futtatását" részben tekintse meg egymás [után](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| SplitOn elemek | 100,000 | Egy tömböt visszaadó eseményindítók esetében megadhat egy olyan kifejezést, amely egy "SplitOn" tulajdonságot használ, amely a [Tömb elemeit több munkafolyamat-példányba bontja vagy bontotta](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) a feldolgozásra, nem pedig "foreach" ciklust használ. Ez a kifejezés az egyes tömbökhöz tartozó munkafolyamat-példányok létrehozásához és futtatásához használható tömböt hivatkozik. |
| Iterációig | 5,000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Átviteli sebesség korlátai

Egyetlen logikai alkalmazás definíciójának korlátai:

### <a name="multi-tenant-logic-apps-service"></a>Több-bérlős Logic Apps szolgáltatás

| Name (Név) | Korlát | Megjegyzések |
| ---- | ----- | ----- |
| Művelet: Végrehajtások száma 5 percenként | 100 000 az alapértelmezett korlát, de a 300 000 a maximális korlát. | Az alapértelmezett korlát módosításához tekintse [meg a logikai alkalmazás futtatása nagy teljesítményű módban](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), amely előzetes verzióban érhető el. Vagy a számítási feladatok több logikai alkalmazásban is eloszthatók a szükséges módon. |
| Művelet: Egyidejű kimenő hívások | ~2,500 | Csökkentheti az egyidejű kérések számát, vagy igény szerint csökkentheti az időtartamot. |
| Futásidejű végpont: Egyidejű bejövő hívások | ~1,000 | Csökkentheti az egyidejű kérések számát, vagy igény szerint csökkentheti az időtartamot. |
| Futásidejű végpont: Olvasási hívások száma 5 percenként  | 60,000 | Szükség szerint több alkalmazáson is terjesztheti a számítási feladatokat. |
| Futásidejű végpont: Hívások hívása 5 percenként | 45,000 | Szükség szerint több alkalmazáson is terjesztheti a számítási feladatokat. |
| Tartalom átviteli sebessége 5 percenként | 600 MB | Szükség szerint több alkalmazáson is terjesztheti a számítási feladatokat. |
||||

### <a name="integration-service-environment-ise"></a>Integrációs szolgáltatási környezet (ISE)

| Name (Név) | Korlát | Megjegyzések |
|------|-------|-------|
| Alapegység végrehajtási korlátja | Rendszer által szabályozott, ha az infrastruktúra kapacitása eléri a 80%-ot | Percenként ~ 4 000 művelet-végrehajtást biztosít, ami ~ 160 000 000 művelet-végrehajtás havonta | |
| Skálázási egység végrehajtási korlátja | Rendszer által szabályozott, ha az infrastruktúra kapacitása eléri a 80%-ot | Minden egyes méretezési egység percenként 2 000 további művelet-végrehajtást biztosít, ami ~ 80 000 000 további műveletek végrehajtása havonta | |
| Felvehető maximális méretezési egységek | 10 | |
||||

Ha a határértékeket a normál feldolgozás során szeretné megtenni, vagy olyan terheléses tesztelést kell futtatnia, amely túllépheti ezeket a korlátokat, a követelményekkel kapcsolatos segítségért [forduljon a Logic apps csapatához](mailto://logicappsemail@microsoft.com) .

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP-korlátok

Az alábbi korlátozások érvényesek egyetlen HTTP-kérelemre vagy szinkron összekötő hívására:

#### <a name="timeout"></a>Időtúllépés

Egyes összekötők aszinkron hívásokat végeznek, vagy figyelik a webhook-kérelmeket, így a műveletek időtúllépése hosszabb lehet a határértéknél. További információkért tekintse meg az adott összekötő technikai részleteit, valamint a [munkafolyamat-eseményindítókat és műveleteket](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name (Név) | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| Kimenő kérelem | 120 másodperc | 240 másodperc | A hosszú ideig futó műveletekhez használjon [aszinkron lekérdezési mintát](../logic-apps/logic-apps-create-api-app.md#async-pattern) vagy egy [ciklusig](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Szinkron válasz | 120 másodperc | 240 másodperc | Ahhoz, hogy az eredeti kérelem kapjon választ, a válaszban szereplő összes lépésnek befejeznie kell a korláton belül, kivéve, ha egy másik logikai alkalmazást beágyazott munkafolyamatként hív meg. További információ: a [logikai alkalmazások hívása, triggere vagy beágyazása](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>Üzenet mérete

| Name (Név) | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| Üzenet mérete | 100 MB | 200 MB | A korlát megkerüléséhez lásd: [nagy méretű üzenetek kezelése darabolással](../logic-apps/logic-apps-handle-large-messages.md). Előfordulhat azonban, hogy egyes összekötők és API-k nem támogatják a darabolást, vagy akár az alapértelmezett korlátot is. |
| Az üzenetek mérete darabolással | 1 GB | 5 GB | Ez a korlát olyan műveletekre vonatkozik, amelyek natív módon támogatják a darabolást, vagy lehetővé teszik a darabolást a futásidejű konfigurációjában. <p>Az integrációs szolgáltatási környezet esetében a Logic Apps motor támogatja ezt a korlátot, de az összekötők a motor korlátján belül saját darabolási korlátokkal rendelkeznek, például: [Azure Blob Storage-összekötő](/connectors/azureblob/). További információk a darabolásról: [nagy méretű üzenetek kezelése darabolással](../logic-apps/logic-apps-handle-large-messages.md). |
| Kifejezés kiértékelési korlátja | 131 072 karakter | 131 072 karakter | A `@concat()` ,`@base64()`a kifejezésnemlehethosszabbennélakorlátnál.`@string()` |
|||||

#### <a name="retry-policy"></a>Újrapróbálkozási házirend

| Name (Név) | Korlát | Megjegyzések |
| ---- | ----- | ----- |
| Újrapróbálkozások száma | 90 | Az alapértelmezett érték 4. Az alapértelmezett érték módosításához használja az újrapróbálkozási [házirend paramétert](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Újrapróbálkozás maximális késleltetése | 1 nap | Az alapértelmezett érték módosításához használja az újrapróbálkozási [házirend paramétert](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Újrapróbálkozás minimális késleltetéssel | 5 másodperc | Az alapértelmezett érték módosításához használja az újrapróbálkozási [házirend paramétert](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Egyéni összekötők korlátai

A webes API-k által létrehozott egyéni összekötők korlátai.

| Name (Név) | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| Egyéni összekötők száma | Azure-előfizetésenként 1000 | Azure-előfizetésenként 1000 ||
| Kérelmek percenkénti száma egyéni összekötő esetén | 500 percenkénti kérelmek száma kapcsolatonként | 2 000 kérelem/perc/ *egyéni összekötő* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Felügyelt identitások

| Name (Név) | Korlát |
| ---- | ----- |
| A rendszer által hozzárendelt felügyelt identitásokkal rendelkező Logic Apps-alkalmazások száma Azure-előfizetések esetében | 100 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Integrációs fiók korlátai

Az egyes Azure-előfizetések az alábbi integrációs fiókra korlátozzák:

* Egy [ingyenes szintű](../logic-apps/logic-apps-pricing.md#integration-accounts) integrációs fiók/Azure-régió

* 1 000 összes integrációs fiók, beleértve az Integration [Service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) integrációs fiókjait mind a [fejlesztői, mind a prémium SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)-ban.

* Minden ISE – akár [fejlesztő](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), akár prémium – 5 teljes integrációs fiókra korlátozódik:

  | ISE SKU | Korlát |
  |---------|-------|
  | **Prémium** | 5 teljes [standard](../logic-apps/logic-apps-pricing.md#integration-accounts) – csak, ingyenes vagy alapszintű |
  | **Fejlesztői** | 5 összesen – ingyenes (legfeljebb 1), standard vagy mindkettő, de nem alapszintű |
  |||

A további költségek azokra az integrációs fiókokra vonatkoznak, amelyeket az ISE részét képező integrációs fiókokon kívül hozzáadott. A ISEs díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Összetevők korlátai integrációs fiókban

Az egyes integrációs fiókok szintjein található összetevők számának korlátai. A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/). Az integrációs fiókok díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Az ingyenes szintet csak feltáró forgatókönyvek esetében használja, éles környezetben nem. Ez a szint korlátozza az átviteli sebességet és a használatot, és nem rendelkezik szolgáltatói szerződéssel (SLA).

| Összetevő | Free | Alapszintű | Standard |
|----------|------|-------|----------|
| EDI kereskedelmi szerződések | 10 | 1 | 1,000 |
| EDI kereskedelmi partnerek | 25 | 2 | 1,000 |
| Maps | 25 | 500 | 1,000 |
| Sémák | 25 | 500 | 1,000 |
| Szerelvények | 10 | 25 | 1,000 |
| Tanúsítványok | 25 | 2 | 1,000 |
| Kötegkonfigurációk | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Az összetevő kapacitásának korlátai

| Összetevő | Korlát | Megjegyzések |
| -------- | ----- | ----- |
| Szerelvény | 8 MB | 2 MB-nál nagyobb fájlok feltöltéséhez használjon egy [Azure Storage-fiókot és egy blob](../logic-apps/logic-apps-enterprise-integration-schemas.md)-tárolót. |
| Térkép (XSLT-fájl) | 8 MB | A 2 MB-nál nagyobb fájlok feltöltéséhez használja a [Azure Logic Apps REST API-Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate)szolgáltatást. |
| Séma | 8 MB | 2 MB-nál nagyobb fájlok feltöltéséhez használjon egy [Azure Storage-fiókot és egy blob](../logic-apps/logic-apps-enterprise-integration-schemas.md)-tárolót. |
||||

| Futásidejű végpont | Korlát | Megjegyzések |
|------------------|-------|-------|
| Olvasási hívások száma 5 percenként | 60,000 | Szükség szerint több fiókon is terjesztheti a munkaterhelést. |
| Hívások hívása 5 percenként | 45,000 | Szükség szerint több fiókon is terjesztheti a munkaterhelést. |
| Hívások követése 5 percenként | 45,000 | Szükség szerint több fiókon is terjesztheti a munkaterhelést. |
| Egyidejű hívások blokkolása | ~1,000 | Csökkentheti az egyidejű kérések számát, vagy igény szerint csökkentheti az időtartamot. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B protokoll (AS2, X12, EDIFACT) üzenet mérete

Itt láthatók a B2B protokollokra vonatkozó üzenetek mérete:

| Name (Név) | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 – 100 MB<br>v1 – 50 MB | v2 – 200 MB <br>v1 – 50 MB | A dekódolásra és a kódolásra vonatkozik |
| X12 | 50 MB | 50 MB | A dekódolásra és a kódolásra vonatkozik |
| EDIFACT | 50 MB | 50 MB | A dekódolásra és a kódolásra vonatkozik |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Logikai alkalmazások letiltása vagy törlése

Ha letilt egy logikai alkalmazást, a rendszer nem hoz létre új futtatásokat. A folyamatban lévő és a függőben lévő futtatások addig folytatódnak, amíg be nem fejeződik, ami hosszabb időt is igénybe vehet.

Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Tűzfal-konfiguráció: IP-címek

Az ugyanabban a régióban található logikai alkalmazások ugyanazt az IP-címtartományt használják. A logikai alkalmazások által a [http](../connectors/connectors-native-http.md)-, [http + hencegő](../connectors/connectors-native-http-swagger.md)és egyéb http-kérésekkel közvetlenül elérhető hívások támogatásához állítsa be a tűzfalakat a Logic Apps szolgáltatás által használt *összes* [bejövő](#inbound) *és* [kimenő](#outbound) IP-címmel. , azon régiók alapján, ahol a logikai alkalmazások léteznek. Ezek a címek a jelen szakasz **bejövő** és **kimenő** fejlécei alatt jelennek meg, és régiónként vannak rendezve.

A [Microsoft által felügyelt összekötők](../connectors/apis-list.md) által kezdeményezett hívások támogatásához állítsa be a tűzfalat az összekötők által használt *összes* [kimenő](#outbound) IP-címmel azon régiók alapján, ahol a logikai alkalmazások léteznek. Ezek a címek a szakasz **kimenő** fejlécében jelennek meg, és régiónként vannak rendezve.

A [Azure Government](../azure-government/documentation-government-overview.md) és az [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)esetében jelenleg nem érhetők el az összekötők fenntartott IP-címei.

> [!IMPORTANT]
>
> Ha rendelkezik meglévő konfigurációkkal, a lehető leghamarabb frissítse őket a **2018. szeptember 1. előtt** , így azok beletartoznak és egyeznek a listában szereplő IP-címekkel azokon a régiókban, ahol a logikai alkalmazások léteznek.

Logic Apps nem támogatja közvetlenül az Azure Storage-fiókokhoz való csatlakozást a tűzfalakon keresztül. A Storage-fiókok eléréséhez használja az alábbi lehetőségek egyikét:

* Hozzon létre egy [integrációs szolgáltatási környezetet](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), amely egy Azure-beli virtuális hálózat erőforrásaihoz tud csatlakozni.

* Ha már használja a API Management, ezt a szolgáltatást használhatja ehhez a forgatókönyvhöz. További információ: [Simple Enterprise Integration Architecture](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Bejövő IP-címek – csak Logic Apps szolgáltatás

| Régió | IP |
|--------|----|
| Kelet-Ausztrália | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Délkelet-Ausztrália | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Dél-Brazília | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Közép-Kanada | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Kelet-Kanada | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Közép-India | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA középső régiója | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Kelet-Ázsia | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| East US | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| USA 2. keleti régiója | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Kelet-Japán | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Nyugat-Japán | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| USA északi középső régiója | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Észak-Európa | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| USA déli középső régiója | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Dél-India | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Délkelet-Ázsia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| USA nyugati középső régiója | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Nyugat-Európa | 13.95.155.53, 51.144.176.185, 52.174.49.6, 52.174.54.218 |
| Nyugat-India | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| USA nyugati régiója | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| USA nyugati régiója, 2. | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Az Egyesült Királyság déli régiója | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Az Egyesült Királyság nyugati régiója | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Kimenő IP-címek – Logic Apps szolgáltatás & felügyelt összekötők

| Régió | Logic Apps IP-cím | Felügyelt összekötők IP-címe |
|--------|---------------|-----------------------|
| Kelet-Ausztrália | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10 |
| Délkelet-Ausztrália | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.77.50.240 - 13.77.50.255, 13.70.136.174 |
| Dél-Brazília | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 191.233.203.192 - 191.233.203.207, 104.41.59.51 | 
| Közép-Kanada | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.237.24.126 |
| Kelet-Kanada | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.242.35.152 |
| Közép-India | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 104.211.81.192 - 104.211.81.207, 52.172.211.12 |
| USA középső régiója | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 52.173.245.164 |
| Kelet-Ázsia | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 52.175.23.169 |
| East US | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| USA 2. keleti régiója | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154 |
| Kelet-Japán | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.78.108.0 - 13.78.108.15, 13.71.153.19 |
| Nyugat-Japán | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 104.215.61.248 |
| USA északi középső régiója | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161 |
| Észak-Európa | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68 |
| USA déli középső régiója | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 104.214.19.48 - 104.214.19.63, 13.65.86.57 |
| Dél-India | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 40.78.194.240 - 40.78.194.255, 13.71.125.22 |
| Délkelet-Ázsia | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 52.187.68.19 |
| USA nyugati középső régiója | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.102.22 |
| Nyugat-Európa | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 52.174.88.118 |
| Nyugat-India | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.189.218 |
| USA nyugati régiója | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 | 40.112.243.160 - 40.112.243.175, 104.42.122.49 |
| USA nyugati régiója, 2. | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 52.183.78.157 |
| Az Egyesült Királyság déli régiója | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.148.0 - 51.140.148.15, 51.140.80.51 |
| Az Egyesült Királyság nyugati régiója | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>További lépések  

* Ismerje meg, hogyan [hozhatja létre első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Tudnivalók a [gyakori példákról és forgatókönyvekről](../logic-apps/logic-apps-examples-and-scenarios.md)
