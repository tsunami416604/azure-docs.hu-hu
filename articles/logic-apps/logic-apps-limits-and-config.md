---
title: Korlátozások és konfiguráció
description: A szolgáltatás korlátai, például az időtartam, az átviteli sebesség és a kapacitás, valamint a konfigurációs értékek, például az engedélyezni kívánt IP-címek, Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: 0705336056ff7c60afddd866b5c65694098a32db
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744066"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Információ az Azure Logic Apps korlátozásaival és konfigurálásával kapcsolatban

Ez a cikk az automatikus munkafolyamatok létrehozásával és futtatásával kapcsolatos korlátozásokat és konfigurációs adatokat ismerteti Azure Logic Apps. A következő témakörben talál további információt: [korlátok és konfiguráció a Power gyorsbüféban](/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definíciós korlátok

Egyetlen logikai alkalmazás definíciójának korlátai:

| Név | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Műveletek munkafolyamatonként | 500 | A korlát meghosszabbításához szükség szerint beágyazott munkafolyamatokat adhat hozzá. |
| Engedélyezett beágyazási mélység a műveletekhez | 8 | A korlát meghosszabbításához szükség szerint beágyazott munkafolyamatokat adhat hozzá. |
| Munkafolyamatok régiónként/előfizetés szerint | 1,000 | |
| Eseményindítók/munkafolyamat | 10 | Ha kód nézetben dolgozik, a tervező nem |
| Váltás hatóköri eseteinek korlátja | 25 | |
| Változók száma munkafolyamatban | 250 | |
| Név `action` vagy `trigger` | 80 karakter | |
| Karakter/kifejezés | 8,192 | |
| Hossz `description` | 256 karakter | |
| Maximális száma `parameters` | 50 | |
| Maximális száma `outputs` | 10 | |
| Maximális méret `trackedProperties` | 16 000 karakter |
| Beágyazott kód művelet – a kód karaktereinek maximális száma | 1 024 karakter <p>100 000 karakteres korlát esetén hozza létre a logikai alkalmazásokat a Visual Studio Code és az [előzetes verziójú **Azure Logic apps** bővítménnyel](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md). |

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Futtatás időtartamára és adatmegőrzésre vonatkozó korlátok

Egyetlen logikai alkalmazás futtatásának korlátai:

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Jegyzetek |
|------|--------------------|---------------------------------------|-------|
| Futtatás időtartama | 90 nap | 366 nap | A futtatási időtartam kiszámítása a futtatási kezdési idő és a munkafolyamat-beállítás által a *kezdés időpontjában* megadott korlát alapján történik, a [**futtatási Előzmények megőrzése napokban**](#change-duration). <p><p>Ha módosítani szeretné az alapértelmezett korlátot (90 nap), tekintse meg a [Futtatás időtartamának módosítása](#change-duration)című témakört. |
| Adatmegőrzés futtatása a tárolóban | 90 nap | 366 nap | A Futtatás megőrzésének kiszámítása a futtatási kezdési idő és a munkafolyamat-beállítás által *a jelenlegi időpontban* megadott korlát alapján történik, a [**futtatási Előzmények megőrzése napokban**](#change-retention). Azt jelzi, hogy a Futtatás befejeződik vagy időtúllépés esetén a megőrzési számítás mindig a Futtatás kezdési idejét használja-e. Ha a Futtatás időtartama meghaladja a *jelenlegi* megőrzési korlátot, a rendszer eltávolítja a futtatást a futtatott előzményekből. <p><p>Ha módosítja ezt a beállítást, a rendszer mindig az aktuális korlátot használja a megőrzés kiszámításához, az előző korláttól függetlenül. Ha például csökkenti a megőrzési korlátot 90 nap és 30 nap között, akkor egy 60 napos Futtatás törlődik a futtatási előzményekből. Ha a megőrzési időtartamot 30 napról 60 napra emeli, egy másik 40 nap alatt egy 20 napos futási idő marad a futtatási előzményekben. <p><p>Ha módosítani szeretné az alapértelmezett korlátot (90 nap), tekintse meg a [Futtatás megőrzésének módosítása a tárolóban](#change-retention)című témakört. |
| Minimális ismétlődési időköz | 1 másodperc | 1 másodperc ||
| Maximális ismétlődési időköz | 500 nap | 500 nap ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-run-retention-in-storage"></a>A futtatási időtartam módosítása és a tárolóban való futtatás megőrzése

Az alábbi lépéseket követve módosíthatja a futtatási időtartam és a futtatási megőrzés alapértelmezett korlátját. A maximális korlát növeléséhez [vegye fel a kapcsolatot az Logic apps csapatával](mailto://logicappsemail@microsoft.com) a követelményekkel kapcsolatos segítségért.

> [!NOTE]
> A több-bérlős Azure-beli Logic apps esetében a 90 napos alapértelmezett korlát megegyezik a maximális korláttal. Ezt az értéket csak csökkenteni lehet.
> Az integrációs szolgáltatási környezetben lévő Logic apps esetében csökkentheti vagy növelheti a 90 napos alapértelmezett korlátot.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). A portál keresési mezőjében keresse meg és válassza ki a **Logic apps**elemet.

1. Válassza ki, majd nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjében válassza a **munkafolyamat-beállítások**elemet.

1. A **Futásidejű beállítások**alatt, a **futtatási előzmények megőrzésének napjai** listában válassza az **Egyéni**lehetőséget.

1. Húzza a csúszkát a kívánt napok számának megváltoztatásához.

1. Ha elkészült, a **munkafolyamat-beállítások** eszköztáron válassza a **Mentés**lehetőséget.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Párhuzamosságok, ismétlések és letételi korlátok

Egyetlen logikai alkalmazás futtatásának korlátai:

| Név | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Egyidejűség kiváltása | – Korlátlan, ha a Egyidejűség vezérlőelem ki van kapcsolva <p><p>a-25 az alapértelmezett korlát, ha a Egyidejűség vezérlőelem be van kapcsolva, amelyet a párhuzamosság engedélyezése után nem lehet visszavonni. Az alapértelmezett értéket 1 és 50 közötti értékre módosíthatja. | Ez a korlát a logikai alkalmazások azon példányainak a maximális számát ismerteti, amelyek egyszerre vagy párhuzamosan futtathatók. <p><p>**Megjegyzés**: Ha a Egyidejűség be van kapcsolva, a SplitOn korlátja 100 elemre [csökken.](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) <p><p>Ha az alapértelmezett határértéket 1 és 50 közötti értékre szeretné módosítani, a következő témakörben talál további információt: [trigger egyidejűségi korlátjának módosítása](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) vagy az [indító példányok egymás után](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Várakozó futtatások maximális száma | – Az egyidejűség nélkül a várakozó futtatások minimális száma 1, a maximális szám pedig 50. <p><p>-A párhuzamosságok esetében a várakozási idő minimális száma 10 az egyidejű futtatások (trigger Egyidejűség) száma. A maximális számot akár 100-ig is megváltoztathatja. | Ez a korlát a logikai alkalmazások azon példányainak a maximális számát írja le, amelyek csak akkor tudnak futni, ha a logikai alkalmazás már futtatja az egyidejű példányokat. <p><p>Az alapértelmezett korlát módosításához tekintse meg a [várakozó futtatások korlátjának módosítása](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)című témakört. |
| Foreach | 100.000 | Ez a korlát a "for each" ciklusban feldolgozható tömb elemeinek legnagyobb számát mutatja. <p><p>Nagyobb tömbök szűréséhez használhatja a [lekérdezési műveletet](logic-apps-perform-data-operations.md#filter-array-action). |
| Foreach Egyidejűség | a 20 az alapértelmezett korlát, ha a Egyidejűség vezérlőelem ki van kapcsolva. Az alapértelmezett értéket 1 és 50 közötti értékre módosíthatja. | Ez a korlát a "minden" hurok-iteráció esetében a legmagasabb szám, amely egyszerre vagy párhuzamosan futtatható. <p><p>Ha az alapértelmezett határértéket 1 és 50 közötti értékre szeretné módosítani, az egyes " [egyidejűségek korlátait](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) vagy [futtatását](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)" részben tekintse meg egymás után. |
| SplitOn-elemek | -100 000 trigger-párhuzamosság nélkül <p><p>-100 trigger-párhuzamosságtal | Egy tömböt visszaadó eseményindítók esetében megadhat egy olyan kifejezést, amely egy "SplitOn" tulajdonságot használ, amely a [Tömb elemeit több munkafolyamat-példányba bontja vagy bontotta](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) a feldolgozásra, nem pedig "foreach" ciklust használ. Ez a kifejezés az egyes tömbökhöz tartozó munkafolyamat-példányok létrehozásához és futtatásához használható tömböt hivatkozik. <p><p>**Megjegyzés**: Ha a Egyidejűség be van kapcsolva, a SplitOn korlátja 100 elemre csökken. |
| Until-iterációk | – Alapértelmezett: 60 <p><p>– Maximum: 5 000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Átviteli korlátok

Egyetlen logikai alkalmazás definíciójának korlátai:

### <a name="multi-tenant-logic-apps-service"></a>Több-bérlős Logic Apps szolgáltatás

| Név | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Művelet: végrehajtások száma 5 percenként | 100 000 az alapértelmezett korlát, de a 300 000 a maximális korlát. | Az alapértelmezett korlát módosításához tekintse [meg a logikai alkalmazás futtatása nagy teljesítményű módban](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), amely előzetes verzióban érhető el. Vagy a számítási feladatok több logikai alkalmazásban is eloszthatók a szükséges módon. |
| Művelet: egyidejű kimenő hívások | ~2.500 | Csökkentheti az egyidejű kérések számát, vagy igény szerint csökkentheti az időtartamot. |
| Futásidejű végpont: egyidejű bejövő hívások | ~ 1 000 | Csökkentheti az egyidejű kérések számát, vagy igény szerint csökkentheti az időtartamot. |
| Futásidejű végpont: olvasási hívások/5 perc  | 60.000 | Ez a korlát a logikai alkalmazás futtatási előzményeiből származó nyers bemenetek és kimenetek lekérésére irányuló hívásokra vonatkozik. Szükség szerint több alkalmazásban is terjesztheti a munkaterhelést. |
| Futásidejű végpont: hívások hívása 5 percenként | 45.000 | Szükség szerint több alkalmazáson is terjesztheti a számítási feladatokat. |
| Tartalom átviteli sebessége 5 percenként | 600 MB | Szükség szerint több alkalmazáson is terjesztheti a számítási feladatokat. |
||||

### <a name="integration-service-environment-ise"></a>Integrációs szolgáltatási környezet (ISE)

A [prémium ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)átviteli sebességének korlátai:

| Név | Korlát | Jegyzetek |
|------|-------|-------|
| Alapegység végrehajtási korlátja | Rendszer által szabályozott, ha az infrastruktúra kapacitása eléri a 80%-ot | Percenként ~ 4 000 művelet-végrehajtást biztosít, ami ~ 160 000 000 művelet-végrehajtás havonta | |
| Skálázási egység végrehajtási korlátja | Rendszer által szabályozott, ha az infrastruktúra kapacitása eléri a 80%-ot | Minden egyes méretezési egység percenként 2 000 további művelet-végrehajtást biztosít, ami ~ 80 000 000 további műveletek végrehajtása havonta | |
| Felvehető maximális méretezési egységek | 10 | |
||||

Ha a határértékeket a normál feldolgozás során szeretné megtenni, vagy olyan terheléses tesztelést kell futtatnia, amely túllépheti ezeket a korlátokat, a követelményekkel kapcsolatos segítségért [forduljon a Logic apps csapatához](mailto://logicappsemail@microsoft.com) .

> [!NOTE]
> A [fejlesztői ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) -nak nincsenek közzétett korlátai, nincs lehetőség a méretezésre, és nem biztosítunk szolgáltatói szerződést (SLA-t). Ezt az SKU-t csak kísérletezéshez, fejlesztéshez és teszteléshez használja, nem éles környezetben, sem pedig a teljesítmény teszteléséhez.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Átjáróra vonatkozó korlátok

Azure Logic Apps támogatja az írási műveleteket, beleértve a lapkákat és a frissítéseket az átjárón keresztül. Ezek a műveletek azonban [korlátokkal rendelkeznek a hasznos adatok méretétől függően](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP-korlátok

Az alábbi korlátozások érvényesek egyetlen kimenő vagy bejövő HTTP-hívásra:

#### <a name="timeout"></a>Időtúllépés

Egyes összekötők aszinkron hívásokat végeznek, vagy figyelik a webhook-kérelmeket, így a műveletek időtúllépése hosszabb lehet a határértéknél. További információkért tekintse meg az adott összekötő technikai részleteit, valamint a [munkafolyamat-eseményindítókat és műveleteket](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Jegyzetek |
|------|--------------------|---------------------------------------|-------|
| Kimenő kérelem | 120 másodperc <br>(2 perc) | 240 másodperc <br>(4 perc) | A kimenő kérelmekre például a HTTP-eseményindítók által kezdeményezett hívások tartoznak. <p><p>**Tipp**: a hosszú ideig futó műveletekhez használjon [aszinkron lekérdezési mintát](../logic-apps/logic-apps-create-api-app.md#async-pattern) vagy egy [ciklusig](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). Ha egy másik logikai alkalmazást hív meg, amely egy [meghívásos végponttal](logic-apps-http-endpoint.md)rendelkezik, az időkorlát korlátozásához használhatja a beépített Azure Logic apps műveletet, amelyet a **beépített összekötő-** választóban talál. |
| Bejövő kérelem | 120 másodperc <br>(2 perc) | 240 másodperc <br>(4 perc) | A bejövő kérésekre példák például a kérelem-eseményindítók és a webhook-eseményindítók által fogadott hívások. <p><p>**Megjegyzés**: ahhoz, hogy az eredeti hívó megkapja a választ, a válaszban szereplő összes lépésnek befejeznie kell a korláton belül, kivéve, ha egy másik logikai alkalmazást beágyazott munkafolyamatként hív meg. További információ: a [logikai alkalmazások hívása, triggere vagy beágyazása](../logic-apps/logic-apps-http-endpoint.md). |
|||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Üzenet mérete

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Jegyzetek |
|------|--------------------|---------------------------------------|-------|
| Üzenet mérete | 100 MB | 200 MB | A korlát megkerüléséhez lásd: [nagy méretű üzenetek kezelése darabolással](../logic-apps/logic-apps-handle-large-messages.md). Előfordulhat azonban, hogy egyes összekötők és API-k nem támogatják a darabolást, vagy akár az alapértelmezett korlátot is. <p><p>– Az AS2, a X12 és a EDIFACT összekötőhöz saját [B2B-üzenetek](#b2b-protocol-limits)tartoznak. <br>-Az ISE-összekötők az ISE-korlátot használják, nem a nem ISE-összekötő korlátait. |
| Az üzenetek mérete darabolással | 1 GB | 5 GB | Ez a korlát olyan műveletekre vonatkozik, amelyek natív módon támogatják a darabolást, vagy lehetővé teszik a darabolást a futásidejű konfigurációjában. <p><p>Ha ISE-t használ, akkor a Logic Apps motor támogatja ezt a korlátot, az összekötők azonban a motor korlátján belül a saját darabolási korláttal rendelkeznek, például az [Azure Blob Storage-összekötő API-referenciája](/connectors/azureblob/)című témakörben talál. További információ a darabolásról: [nagy méretű üzenetek kezelése darabolással](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Karakterek korlátai

| Név | Jegyzetek |
|------|-------|
| Kifejezések kiértékelési korlátja | 131 072 karakter | A `@concat()` , `@base64()` a `@string()` kifejezés nem lehet hosszabb ennél a korlátnál. |
| Kérelem URL-karakterének korlátja | 16 384 karakter |
|||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

| Név | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Újrapróbálkozási kísérletek | 90 | Az alapértelmezett érték 4. Az alapértelmezett érték módosításához használja az [újrapróbálkozási házirend paramétert](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Újrapróbálkozások maximális késése | 1 nap | Az alapértelmezett érték módosításához használja az [újrapróbálkozási házirend paramétert](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Újrapróbálkozások minimális késése | 5 másodperc | Az alapértelmezett érték módosításához használja az [újrapróbálkozási házirend paramétert](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Hitelesítési korlátok

Íme egy olyan logikai alkalmazás korlátai, amelyek egy kérelem-triggerrel kezdődnek, és lehetővé teszik [Azure Active Directory nyílt hitelesítés](../active-directory/develop/index.yml) (Azure ad OAuth) számára a bejövő hívások engedélyezését a kérelem-trigger számára:

| Név | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Azure AD-engedélyezési házirendek | 5 | |
| Jogcímek engedélyezési házirend alapján | 10 | |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Egyéni összekötőre vonatkozó korlátok

A webes API-k által létrehozott egyéni összekötők korlátai.

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Jegyzetek |
|------|--------------------|---------------------------------------|-------|
| Egyéni összekötők száma | Azure-előfizetésenként 1000 | Azure-előfizetésenként 1000 ||
| Kérelmek percenkénti száma egyéni összekötő esetén | 500 percenkénti kérelmek száma kapcsolatonként | 2 000 kérelem/perc/ *egyéni összekötő* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Felügyelt identitások

| Név | Korlát |
|------|-------|
| Felügyelt identitások/logikai alkalmazások | Vagy a rendszer által hozzárendelt identitás vagy 1 felhasználó által hozzárendelt identitás |
| A felügyelt identitással rendelkező logikai alkalmazások száma régiónként | 1,000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Integrációs fiók korlátai

Az egyes Azure-előfizetések az alábbi integrációs fiókra korlátozzák:

* Egy [ingyenes szintű](../logic-apps/logic-apps-pricing.md#integration-accounts) integrációs fiók Azure-régiónként. Ez a lehetőség csak az Azure nyilvános régiói esetében érhető el, például az USA nyugati régiójában vagy Délkelet-Ázsiában, de nem az [Azure China 21Vianet](/azure/china/overview-operations) vagy a [Azure Government](../azure-government/documentation-government-welcome.md).

* 1 000 összes integrációs fiók, beleértve az Integration [Service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) integrációs fiókjait mind a [fejlesztői, mind a prémium SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)-ban.

* Minden ISE – akár [fejlesztő](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), akár prémium – csak 20 teljes integrációs fiókra korlátozódik:

  | ISE SKU | Integrációs fiók korlátai |
  |---------|----------------------------|
  | **Prémium** | 20 teljes [standard](../logic-apps/logic-apps-pricing.md#integration-accounts) fiók, beleértve az ingyenes egy standard fiókot is. Ingyenes vagy alapszintű fiókok használata nem engedélyezett. |
  | **Fejlesztő** | 20 összesen – [ingyenes](../logic-apps/logic-apps-pricing.md#integration-accounts) (legfeljebb 1 fiók) és [standard](../logic-apps/logic-apps-pricing.md#integration-accounts) kombinált, vagy az összes standard fiók. Nem engedélyezett alapszintű fiók. A [fejlesztői SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) -t a kísérletezéshez, fejlesztéshez és teszteléshez használhatja, az éles környezetben való teszteléshez azonban nem. |
  |||

A további költségek azokra az integrációs fiókokra vonatkoznak, amelyeket az ISE részét képező integrációs fiókokon kívül hozzáadott. A ISEs díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Összetevők korlátai integrációs fiókban

Az egyes integrációs fiókok szintjein található összetevők számának korlátai.
A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/). Az integrációs fiókok díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Az ingyenes szintet csak feltáró forgatókönyvek esetében használja, éles környezetben nem. Ez a szint korlátozza az átviteli sebességet és a használatot, és nem rendelkezik szolgáltatói szerződéssel (SLA).

| Összetevő | Ingyenes | Alapszintű | Standard |
|----------|------|-------|----------|
| EDI kereskedelmi szerződések | 10 | 1 | 1,000 |
| EDI kereskedelmi partnerek | 25 | 2 | 1,000 |
| Maps | 25 | 500 | 1,000 |
| Sémák | 25 | 500 | 1,000 |
| Szerelvények | 10 | 25 | 1,000 |
| Tanúsítványok | 25 | 2 | 1,000 |
| Batch-konfigurációk | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Az összetevő kapacitásának korlátai

| Összetevő | Korlát | Jegyzetek |
| -------- | ----- | ----- |
| Szerelvény | 8 MB | 2 MB-nál nagyobb fájlok feltöltéséhez használjon egy [Azure Storage-fiókot és egy BLOB-tárolót](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Térkép (XSLT-fájl) | 8 MB | A 2 MB-nál nagyobb fájlok feltöltéséhez használja a [Azure Logic Apps REST API-Maps](/rest/api/logic/maps/createorupdate)szolgáltatást. <p><p>**Megjegyzés**: a Térkép által sikeresen feldolgozható adatok vagy rekordok mennyisége az üzenet mérete és a művelet időtúllépési korlátja alapján történik Azure Logic Appsban. Ha például a http- [üzenetek mérete és az időkorlát](#request-limits)alapján http-műveletet használ, a Térkép a http-üzenet méretére vonatkozó korláttal képes feldolgozni az adatmennyiséget, ha a művelet a http-időtúllépési korláton belül fejeződik be. |
| Séma | 8 MB | 2 MB-nál nagyobb fájlok feltöltéséhez használjon egy [Azure Storage-fiókot és egy BLOB-tárolót](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Átviteli korlátok

| Futásidejű végpont | Ingyenes | Alapszintű | Standard | Jegyzetek |
|------------------|------|-------|----------|-------|
| Olvasási hívások száma 5 percenként | 3,000 | 30 000 | 60.000 | Ez a korlát a logikai alkalmazás futtatási előzményeiből származó nyers bemenetek és kimenetek lekérésére irányuló hívásokra vonatkozik. Szükség szerint több fiókon is terjesztheti a munkaterhelést. |
| Hívások hívása 5 percenként | 3,000 | 30 000 | 45.000 | Szükség szerint több fiókon is terjesztheti a munkaterhelést. |
| Hívások követése 5 percenként | 3,000 | 30 000 | 45.000 | Szükség szerint több fiókon is terjesztheti a munkaterhelést. |
| Egyidejű hívások blokkolása | ~ 1 000 | ~ 1 000 | ~ 1 000 | Minden SKU esetében azonos. Csökkentheti az egyidejű kérések számát, vagy igény szerint csökkentheti az időtartamot. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B protokoll (AS2, X12, EDIFACT) üzenet mérete

Itt láthatók a B2B protokollokra vonatkozó üzenetek mérete:

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezeti korlátja | Jegyzetek |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 – 100 MB<br>v1 – 25 MB | v2 – 200 MB <br>v1 – 25 MB | A dekódolásra és a kódolásra vonatkozik |
| X12 | 50 MB | 50 MB | A dekódolásra és a kódolásra vonatkozik |
| EDIFACT | 50 MB | 50 MB | A dekódolásra és a kódolásra vonatkozik |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Logikai alkalmazások letiltása vagy törlése

Ha letilt egy logikai alkalmazást, a rendszer nem hoz létre új futtatásokat. A folyamatban lévő és a függőben lévő futtatások addig folytatódnak, amíg be nem fejeződik, ami hosszabb időt is igénybe vehet.

Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Tűzfal konfigurációja: IP-címek és szolgáltatás-Címkék

A bejövő és kimenő hívások Azure Logic Apps által használt IP-címek attól a régiótól függenek, ahol a logikai alkalmazás létezik. Az ugyanabban a régióban *található logikai alkalmazások* ugyanazt az IP-címtartományt használják. Bizonyos [automatizálási](/power-automate/getting-started) hívások, például a **http** és a **http + OpenAPI** kérelmek, közvetlenül a Azure Logic apps szolgáltatáson keresztül érhetők el, és az itt felsorolt IP-címekről érkeznek. További információ a Power automatizáló által használt IP-címekről: [korlátok és konfiguráció a Power automatizálásban](/flow/limits-and-config#ip-address-configuration).

> [!TIP]
> A biztonsági szabályok létrehozásakor a bonyolultság csökkentése érdekében igény szerint használhatja a [szolgáltatás címkéit](../virtual-network/service-tags-overview.md), és nem kell megadnia az egyes régiók Logic apps IP-címeit a jelen szakasz későbbi részében leírtak szerint.
> Ezek a címkék azokon a régiókban működnek, ahol a Logic Apps szolgáltatás elérhető:
>
> * **LogicAppsManagement**: a Logic Apps szolgáltatás bejövő IP-címeinek előtagjait jelöli.
> * **LogicApps**: a Logic Apps szolgáltatás kimenő IP-címeinek előtagjait jelöli.

* Az [Azure China 21Vianet](/azure/china/)esetében a rögzített vagy fenntartott IP-címek nem érhetők el az [Egyéni összekötők](../logic-apps/custom-connector-overview.md) és a [felügyelt összekötők](../connectors/apis-list.md#managed-api-connectors)számára, például az Azure Storage, az SQL Server, az Office 365 Outlook stb.

* Ha támogatni szeretné a logikai alkalmazások által a [http](../connectors/connectors-native-http.md), a [http + a hencegés](../connectors/connectors-native-http-swagger.md)és más HTTP-kérések által közvetlenül elvégezhető hívásokat, állítsa be a tűzfalat a Logic Apps szolgáltatás által használt összes [bejövő](#inbound) *és* [kimenő](#outbound) IP-címmel, azon régiók alapján, ahol a logikai alkalmazások léteznek. Ezek a címek a jelen szakasz **bejövő** és **kimenő** fejlécei alatt jelennek meg, és régiónként vannak rendezve.

* A [felügyelt összekötők](../connectors/apis-list.md#managed-api-connectors) által kezdeményezett hívások támogatásához állítsa be a tűzfalat az összekötők által használt *összes* [kimenő](#outbound) IP-címmel azon régiók alapján, ahol a logikai alkalmazások léteznek. Ezek a címek a szakasz **kimenő** fejlécében jelennek meg, és régiónként vannak rendezve.

* Az integrációs szolgáltatási környezetben (ISE) futó logikai alkalmazások kommunikációjának engedélyezéséhez győződjön meg arról, hogy [megnyitotta ezeket a portokat](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Ha a logikai alkalmazások problémákba ütközik a [tűzfalakat és tűzfalszabályokat](../storage/common/storage-network-security.md)használó Azure Storage-fiókokhoz való hozzáférés során, a [hozzáférés engedélyezéséhez számos lehetőség](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)áll rendelkezésére.

  A Logic apps például nem fér hozzá közvetlenül a tűzfalszabályok használatát használó Storage-fiókokhoz, és ugyanabban a régióban található. Ha azonban engedélyezi a [kimenő IP-címeket a felügyelt összekötők számára a régióban](../logic-apps/logic-apps-limits-and-config.md#outbound), a logikai alkalmazások hozzáférhetnek egy másik régióban található Storage-fiókokhoz, kivéve ha az Azure Table Storage vagy az Azure Queue Storage-összekötőt használja. Table Storage vagy Queue Storage eléréséhez használhatja a HTTP-triggert és a műveleteket. További lehetőségek: hozzáférés a [Storage-fiókokhoz a tűzfalak mögött](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Bejövő IP-címek

Ez a szakasz csak a Azure Logic Apps szolgáltatás bejövő IP-címeit sorolja fel. Ha Azure Government van, tekintse meg [Azure Government bejövő IP-címeket](#azure-government-inbound).

> [!TIP]
> A biztonsági szabályok létrehozásakor a bonyolultság csökkentése érdekében igény szerint [használhatja a](../virtual-network/service-tags-overview.md) **LogicAppsManagement**, és nem kell megadnia a bejövő Logic apps IP-címek előtagjait az egyes régiókban.
> Ez a címke azon régiók között működik, ahol a Logic Apps szolgáltatás elérhető.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Több-bérlős Azure – bejövő IP-címek

| Több-bérlős régió | IP |
|---------------------|----|
| Kelet-Ausztrália | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Délkelet-Ausztrália | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Dél-Brazília | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Közép-Kanada | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kelet-Kanada | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Közép-India | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA középső régiója | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Kelet-Ázsia | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| USA keleti régiója | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| USA 2. keleti régiója | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Közép-Franciaország | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Dél-Franciaország | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Észak-Németország | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Kelet-Japán | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Nyugat-Japán | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Dél-Korea középső régiója | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Dél-Korea déli régiója | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA északi középső régiója | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Észak-Európa | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Dél-Afrika északi régiója | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Dél-Afrika nyugati régiója | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA déli középső régiója | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Dél-India | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Délkelet-Ázsia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Észak-Svájc | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| UAE középső régiója | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Az Egyesült Királyság déli régiója | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Az Egyesült Királyság nyugati régiója | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA nyugati középső régiója | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Nyugat-Európa | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Nyugat-India | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA nyugati régiója | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA 2. nyugati régiója | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government – bejövő IP-címek

| Azure Government régió | IP |
|-------------------------|----|
| USA-beli államigazgatás – Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| USA-beli államigazgatás – Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| USA-beli államigazgatás – Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD – Középső régió | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Kimenő IP-címek

Ez a szakasz a Azure Logic Apps szolgáltatás és a felügyelt összekötők kimenő IP-címeit sorolja fel. Ha Azure Government van, tekintse meg a [Azure Government-kimenő IP-címek](#azure-government-outbound)című témakört.

> [!TIP]
> A biztonsági szabályok létrehozásakor a bonyolultság csökkentése érdekében igény szerint [használhatja a](../virtual-network/service-tags-overview.md) **LogicApps**, és nem kell megadnia a kimenő Logic apps IP-cím előtagjait az egyes régiókban.
> A felügyelt összekötők esetében igény szerint használhatja a **AzureConnectors** szolgáltatás címkéjét, és nem kell megadnia a kimenő felügyelt összekötő IP-címének előtagjait az egyes régiókban. Ezek a címkék azokon a régiókban működnek, ahol a Logic Apps szolgáltatás elérhető. 

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Több-bérlős Azure-kimenő IP-címek

| Több-bérlős régió | Logic Apps IP-cím | Felügyelt összekötők IP-címe |
|---------------------|---------------|-----------------------|
| Kelet-Ausztrália | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213, 52.237.214.72, 13.70.78.224 - 13.70.78.255 |
| Délkelet-Ausztrália | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34, 52.255.48.202, 13.77.55.160 - 13.77.55.191 |
| Dél-Brazília | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207, 191.232.191.157, 191.233.207.160 - 191.233.207.191 |
| Közép-Kanada | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 13.71.170.208 - 13.71.170.223, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.31.197, 52.237.24.126, 52.237.32.212, 13.71.175.160 - 13.71.175.191, 13.71.170.224 - 13.71.170.239 |
| Kelet-Kanada | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152, 52.242.30.112, 40.69.111.0 - 40.69.111.31 |
| Közép-India | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164, 52.172.212.129, 20.43.123.0 - 20.43.123.31 |
| USA középső régiója | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164, 52.173.241.27, 40.77.68.110, 13.89.178.64 - 13.89.178.95 |
| Kelet-Ázsia | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169, 13.75.110.131, 104.214.164.0 - 104.214.164.31 |
| USA keleti régiója | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 40.114.40.132, 40.71.249.139, 52.188.157.160, 40.71.15.160 - 40.71.15.191 |
| USA 2. keleti régiója | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100, 104.209.247.23, 52.225.129.144, 40.65.220.25, 40.70.151.96 - 40.70.151.127 |
| Közép-Franciaország | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.79.130.208 - 40.79.130.223, 40.89.135.2, 40.89.186.239, 40.79.148.96 - 40.79.148.127 |
| Dél-Franciaország | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 40.79.178.240 - 40.79.178.255, 52.136.133.184, 52.136.142.154, 40.79.180.224 - 40.79.180.255 |
| Észak-Németország | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.211.212, 51.116.60.192 - 51.116.60.223, 51.116.59.16 - 51.116.60.31 |
| Kelet-Japán | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96, 13.73.21.230, 40.79.189.64 - 40.79.189.95 |
| Nyugat-Japán | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248, 104.215.27.24, 40.80.180.64 - 40.80.180.95 |
| Dél-Korea középső régiója | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.231.18.208 - 52.231.18.223, 52.141.36.214, 52.141.1.104, 20.44.29.64 - 20.44.29.95 |
| Dél-Korea déli régiója | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.147.0 - 52.231.147.15, 52.231.163.10, 52.231.201.173, 52.231.148.224 - 52.231.148.255 |
| USA északi középső régiója | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230, 52.162.126.4, 52.162.111.192 - 52.162.111.223 |
| Észak-Európa | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9, 94.245.91.93, 52.169.28.181, 40.115.108.29, 13.69.231.192 - 13.69.231.223 |
| Dél-Afrika északi régiója | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Dél-Afrika nyugati régiója | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.133.27.0 - 102.133.27.15, 102.37.64.0 - 102.37.64.31 |
| USA déli középső régiója | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 52.171.130.92, 13.73.244.224 - 13.73.244.255 |
| Dél-India | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225, 13.71.127.26 |
| Délkelet-Ázsia | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19, 52.187.115.69, 13.67.15.32 - 13.67.15.63 |
| Észak-Svájc | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255, 51.107.86.217, 51.103.142.22 |
| UAE középső régiója | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.28, 20.45.67.45, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Az Egyesült Királyság déli régiója | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15, 51.140.61.124, 51.140.74.150, 51.105.77.96 - 51.105.77.127 |
| Az Egyesült Királyság nyugati régiója | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105, 51.141.124.13, 51.141.52.185, 51.140.212.224 - 51.140.212.255 |
| USA nyugati középső régiója | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 13.78.132.82, 52.161.101.204, 13.71.199.192 - 13.71.199.223 |
| Nyugat-Európa | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118, 40.91.208.65, 52.166.78.89, 13.93.36.78, 13.69.71.192 - 13.69.71.223 |
| Nyugat-India | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218, 104.211.189.124, 20.38.128.224 - 20.38.128.255 |
| USA nyugati régiója | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49, 40.112.195.87, 13.93.148.62, 13.86.223.32 - 13.86.223.63 |
| USA 2. nyugati régiója | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 52.191.164.250, 13.66.164.219, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government – kimenő IP-címek

| Region | Logic Apps IP-cím | Felügyelt összekötők IP-címe |
|--------|---------------|-----------------------|
| US DoD – Középső régió | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| USA-beli államigazgatás – Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| USA-beli államigazgatás – Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| USA-beli államigazgatás – Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhatja létre első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Tudnivalók a [gyakori példákról és forgatókönyvekről](../logic-apps/logic-apps-examples-and-scenarios.md)
