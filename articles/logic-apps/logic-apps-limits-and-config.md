---
title: Korlátozások és konfiguráció
description: Szolgáltatáskorlátok, például időtartam, átviteli és kapacitás, valamint konfigurációs értékek, például az Azure Logic Apps engedélyezni való IP-címek
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 418be090e7ff78ec0089c115c9884ffeffdda871
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284017"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Információ az Azure Logic Apps korlátozásaival és konfigurálásával kapcsolatban

Ez a cikk ismerteti a korlátok és a konfigurációs részleteket az Azure Logic Apps automatizált munkafolyamatok létrehozásához és futtatásához. A Power Automate beállításról a [Korlátok és konfiguráció a Power Automate alkalmazásban](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definíciós korlátok

Az egyes logikai alkalmazások definíciójának korlátai:

| Név | Korlát | Megjegyzések |
| ---- | ----- | ----- |
| Műveletek munkafolyamatonként | 500 | A korlát meghosszabbításához szükség szerint hozzáadhat beágyazott munkafolyamatokat. |
| Engedélyezett beágyazási mélység műveletekhez | 8 | A korlát meghosszabbításához szükség szerint hozzáadhat beágyazott munkafolyamatokat. |
| Munkafolyamatok régiónként előfizetésenként | 1,000 | |
| Eseményindítók munkafolyamatonként | 10 | Kódnézetben végzett munka esetén nem a tervező |
| A hatóköresetek közötti váltáskorlátozás | 25 | |
| Változók munkafolyamatonként | 250 | |
| Karakterek kifejezésenként | 8,192 | |
| Maximális méret`trackedProperties` | 16 000 karakter |
| A `action` vagy ontás neve`trigger` | 80 karakter | |
| Hossza`description` | 256 karakter | |
| Maximális`parameters` | 50 | |
| Maximális`outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Futtatási időtartam és megőrzési korlátok

Az egyetlen logikai alkalmazás futtatásának korlátai:

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezetének korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| Futtatás időtartama | 90 nap | 366 nap | A futtatás időtartamának kiszámítása a futtatás kezdési időpontja és a *kezdési időpontban* megadott korlát alapján történik, amelyet a [**folyamatelőzmények megőrzésének futtatása napokban**](#change-duration)megadott. <p><p>Az alapértelmezett korlát , azaz a 90 nap módosításához tekintse meg a [változás futási időtartamát.](#change-duration) |
| Megőrzés futtatása a tárolóban | 90 nap | 366 nap | A futtatási adatmegőrzés kiszámítása a futtatás kezdési időpontja és *az aktuális időpontban megadott* korlát alapján történik, az Előzmények [**megőrzése napokban.**](#change-retention) Függetlenül attól, hogy egy futtatás befejeződik, vagy időtúljár, a megőrzési számítás mindig a futtatás kezdési idejét használja. Ha egy futtatás időtartama meghaladja az *aktuális* megőrzési korlátot, a futtatási előzmények törlődnek. <p><p>Ha módosítja ezt a beállítást, az aktuális korlát mindig a megőrzés kiszámításához lesz használva, függetlenül az előző korláttól. Ha például 90 napról 30 napra csökkenti a megőrzési korlátot, a 60 napos futtatástörlődik a futtatási előzményekből. Ha 30 napról 60 napra növeli a megőrzési időszakot, a 20 napos futtatás további 40 napig marad a futtatási előzményekben. <p><p>Az alapértelmezett korlát módosításához, amely 90 nap, tekintse meg a [változásfuttatás megtartását a tárolóban.](#change-retention) |
| Minimális ismétlődési időköz | 1 másodperc | 1 másodperc ||
| Maximális ismétlődési időköz | 500 nap | 500 nap ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-run-retention-in-storage"></a>A futtatás időtartamának módosítása és a tárolóban való futtatás

A futtatási időtartam alapértelmezett korlátjának módosításához és a tárolóban való megőrzés futtatásához kövesse az alábbi lépéseket. A maximális korlát növeléséhez [forduljon a Logic Apps csapathoz,](mailto://logicappsemail@microsoft.com) hogy segítséget kérjen a követelményekkel kapcsolatban.

> [!NOTE]
> A több-bérlős Azure-ban a logikai alkalmazások esetében a 90 napos alapértelmezett korlát megegyezik a maximális korláttal. Ezt az értéket csak csökkenteni lehet.
> Az integrációs szolgáltatási környezetben lévő logikai alkalmazások esetében csökkentheti vagy növelheti a 90 napos alapértelmezett korlátot.

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) A portál keresőmezőjében keresse meg és válassza a **Logikai alkalmazások lehetőséget.**

1. Jelölje ki, majd nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A logikai alkalmazás menüjében válassza a **Munkafolyamat-beállítások lehetőséget.**

1. A **Futásidejű beállítások csoportban**a **Futtatási előzmények megőrzése napokban listájában** válassza az **Egyéni**lehetőséget.

1. A csúszka húzásával módosíthatja a kívánt napok számát.

1. Ha elkészült, a **Munkafolyamat-beállítások** eszköztáron válassza a **Mentés gombot.**

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Egyidejűségi, hurkolási és kötegelési határértékek

Az egyetlen logikai alkalmazás futtatásának korlátai:

| Név | Korlát | Megjegyzések |
| ---- | ----- | ----- |
| Egyidejűség aktiválása | - Korlátlan, ha az egyidejűség-vezérlés ki van kapcsolva <p><p>- A 25 az alapértelmezett korlát, ha az egyidejűség-vezérlő be van kapcsolva, ami nem lehet visszavonni a vezérlő bekapcsolása után. Az alapértelmezett értéket 1 és 50 közötti értékre módosíthatja. | Ez a korlát az egyszerre vagy párhuzamosan futtatható logikai alkalmazáspéldányok legnagyobb számát írja le. <p><p>**Megjegyzés:** Ha az egyidejűség be van kapcsolva, a SplitOn korlát 100 elemre csökken a [tömbök lerakásához.](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) <p><p>Ha az alapértelmezett korlátot 1 és 50 közötti értékre szeretné módosítani, olvassa el az [Eseményindító-egyidejűségi korlát módosítása](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) vagy [a Példányok sorrendben imiódása című témakört.](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger) |
| Maximális várakozási futtatások | - Egyidejűség nélkül a várakozási futtatások minimális száma 1, míg a maximális szám 50. <p><p>- Egyidejűség esetén a várakozási futtatások minimális száma 10 plusz az egyidejű futtatások száma (trigger egyidejűség). A maximális szám legfeljebb 100-ig módosítható. | Ez a korlát azt a legtöbb logikai alkalmazáspéldányt ismerteti, amelyek várakozással várhatnak, ha a logikai alkalmazás már futtatja a maximális egyidejű példányokat. <p><p>Az alapértelmezett korlát módosításáról a [Várakozási futtatások módosítása korlát című témakörben](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)olvashat. |
| Minden tömbelem | 100 000 | Ez a korlát azt írja le, hogy a "minden egyes" ciklushoz a legtöbb tömbelem feldolgozható.This limit describes the most number of array items that a "for each" loop can process. <p><p>Nagyobb tömbök szűréséhez használhatja a [lekérdezési műveletet.](logic-apps-perform-data-operations.md#filter-array-action) |
| Foreach egyidejűség | 20 az alapértelmezett korlát, ha az egyidejűség-vezérlés ki van kapcsolva. Az alapértelmezett értéket 1 és 50 közötti értékre módosíthatja. | Ez a korlát a legnagyobb számú "minden" ciklus iterációk, amelyek futtathatók egy időben, vagy párhuzamosan. <p><p>Ha az alapértelmezett korlátot 1 és 50 közötti értékre szeretné módosítani, olvassa el a ["minden egyes" egyidejűségi korlát módosítása](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) vagy [a "minden" hurkok futtatása egymás után című témakört.](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each) |
| SplitOn-elemek | - 100.000 trigger egyidejűség nélkül <p><p>- 100 trigger egyidejűséggel | Tömböt visszaadó eseményindítók esetén a "Foreach" ciklus helyett megadhat egy "SplitOn" tulajdonságot használó kifejezést, amely [a tömbelemeket feldolgozásra több munkafolyamat-példányra osztja vagy bontja](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) fel. Ez a kifejezés az egyes tömbelemek munkafolyamat-példányának létrehozásához és futtatásához használandó tömbre hivatkozik. <p><p>**Megjegyzés:** Ha az egyidejűség be van kapcsolva, a SplitOn korlát 100 elemre csökken. |
| Until-iterációk | - Alapértelmezett: 60 <p><p>- Maximum: 5000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Átviteli korlátok

Az egyes logikai alkalmazások definíciójának korlátai:

### <a name="multi-tenant-logic-apps-service"></a>Több-bérlős Logic Apps szolgáltatás

| Név | Korlát | Megjegyzések |
| ---- | ----- | ----- |
| Művelet: 5 percenkénti végrehajtás | 100 000 az alapértelmezett korlát, de a 300 000 a maximális korlát. | Az alapértelmezett korlát módosításáról a ["nagy átviteli sebességű" módban című logikai alkalmazás futtatása](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)című témakörben olvashat. Vagy szükség szerint több logikai alkalmazás között is eloszthatja a számítási feladatokat. |
| Művelet: Egyidejű kimenő hívások | ~2.500 | Csökkentheti az egyidejű kérelmek számát, vagy szükség szerint csökkentheti az időtartamot. |
| Futásidejű végpont: Egyidejű bejövő hívások | ~1000 | Csökkentheti az egyidejű kérelmek számát, vagy szükség szerint csökkentheti az időtartamot. |
| Futásidejű végpont: Hívások olvasása 5 percenként  | 60.000 | A számítási feladatok at szükség szerint több alkalmazás között is eloszthatja. |
| Futásidejű végpont: Hívások meghívása 5 percenként | 45.000 | A számítási feladatok at szükség szerint több alkalmazás között is eloszthatja. |
| Tartalomátviteli-áteresztőerő 5 percenként | 600 MB | A számítási feladatok at szükség szerint több alkalmazás között is eloszthatja. |
||||

### <a name="integration-service-environment-ise"></a>Integrációs szolgáltatási környezet (ISE)

A prémium szintű termékváltozat átviteli korlátjai a következők:

| Név | Korlát | Megjegyzések |
|------|-------|-------|
| Alapegység végrehajtási határa | Rendszerszabályozás, ha az infrastruktúra kapacitása eléri a 80%-ot | ~4000 műveletvégrehajtást biztosít percenként, ami ~160 millió műveletvégrehajtás havonta | |
| Egységvégrehajtási korlát méretezése | Rendszerszabályozás, ha az infrastruktúra kapacitása eléri a 80%-ot | Minden méretezési egység ~2000 további műveletvégrehajtást biztosít percenként, ami ~ 80 millióval több műveletvégrehajtást biztosít havonta | |
| Maximális méretegységek, amelyeket hozzáadhat | 10 | |
||||

Ha normál feldolgozás esetén túllépi ezeket a korlátokat, vagy futtathatja a terheléstesztelést, amely esetleg meghaladja ezeket a korlátokat, [forduljon a Logic Apps csapatához](mailto://logicappsemail@microsoft.com) segítségért a követelményekkel kapcsolatban.

> [!NOTE]
> A [fejlesztői termékváltozat](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) nem rendelkezik közzétett korlátokkal, mivel ez a termékváltozat nem rendelkezik szolgáltatásiszint-szerződéssel (SLA) vagy skálázási képességekkel.
> Ezt a termékváltozatot csak kísérletezéshez, fejlesztéshez és teszteléshez használja, éles vagy teljesítménytesztelésre.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Átjáró-korlátok

Az Azure Logic Apps támogatja az írási műveleteket, beleértve a beszúrásokat és a frissítéseket az átjárón keresztül. Ezek a műveletek azonban [korlátozzák a hasznos teher méretét](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP-korlátok

Egyetlen kimenő vagy bejövő HTTP-hívásra vonatkozó korlátozások:

#### <a name="timeout"></a>Időkorlát

Egyes összekötőműveletek aszinkron hívásokat kezdeményeznek, vagy figyelhetik a webhook-kérelmeket, így ezek a műveletek időtúlára hosszabb lehet, mint ezek a korlátok. További információt az adott összekötő, valamint a [munkafolyamat-eseményindítók és -műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)technikai részleteiben talál.

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezetének korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| Kimenő kérelem | 120 másodperc <br>(2 perc) | 240 másodperc <br>(4 perc) | A kimenő kérelmek közé tartoznak a HTTP-eseményindítók által kezdeményezett hívások. <p><p>**Tipp**: Hosszabb ideig futó műveletekhez használjon aszinkron lekérdezési mintát vagy [egy akontot.](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action) [asynchronous polling pattern](../logic-apps/logic-apps-create-api-app.md#async-pattern) |
| Bejövő kérelem | 120 másodperc <br>(2 perc) | 240 másodperc <br>(4 perc) | Példák a bejövő kérelmek közé tartozik a kérelmek eseményindítók és webhook-eseményindítók által fogadott hívások. <p><p>**Megjegyzés:** Ahhoz, hogy az eredeti hívó megkapja a választ, a válasz minden lépésének a korláton belül kell befejeződnie, kivéve, ha egy másik logikai alkalmazást beágyazott munkafolyamatként hív meg. További információt a [Hívás, eseményindító vagy logikai alkalmazások beágyazása című témakörben talál.](../logic-apps/logic-apps-http-endpoint.md) |
|||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Üzenet mérete

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezetének korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| Üzenet mérete | 100 MB | 200 MB | Az ISE-címkével ellátott csatlakozók az ISE-korlátot használják, nem pedig a nem ISE-összekötői határértékeket. <p><p>A korlát megkerüléséhez olvassa el [a Nagyméretű üzenetek darabolással történő kezelése .to work](../logic-apps/logic-apps-handle-large-messages.md)around this limit, to Handle large messages with chunking . Előfordulhat azonban, hogy egyes összekötők és API-k nem támogatják az adattömböket, vagy akár az alapértelmezett korlátot. |
| Üzenet mérete darabolással | 1 GB | 5 GB | Ez a korlát olyan műveletekre vonatkozik, amelyek natív módon támogatják az adattömböket, vagy lehetővé teszik az adattömbök engedélyezését a futásidejű konfigurációjukban. <p><p>Az integrációs szolgáltatás környezetben a Logic Apps motor támogatja ezt a korlátot, de az összekötők saját darabolási korlátok at a motor korlát, például lásd az [Azure Blob Storage-összekötő API-referencia.](https://docs.microsoft.com/connectors/azureblob/) A darabolásról a [Nagyméretű üzenetek darabolással történő kezelése című](../logic-apps/logic-apps-handle-large-messages.md)témakörben talál további információt. |
|||||

#### <a name="character-limits"></a>Karakterkorlátok

| Név | Megjegyzések |
|------|-------|
| Kifejezések kiértékelési korlátja | 131 072 karakter | A `@concat()` `@base64()`, `@string()` a kifejezések nem lehetnek hosszabbak ennél a korlátnál. |
| Kérelem URL-karakterkorlátja | 16 384 karakter |
|||

#### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

| Név | Korlát | Megjegyzések |
| ---- | ----- | ----- |
| Újrapróbálkozási kísérletek | 90 | Az alapértelmezett érték 4. Az alapértelmezett érték módosításához használja az [újrapróbálkozási házirend paramétert.](../logic-apps/logic-apps-workflow-actions-triggers.md) |
| Újrapróbálkozások maximális késése | 1 nap | Az alapértelmezett érték módosításához használja az [újrapróbálkozási házirend paramétert.](../logic-apps/logic-apps-workflow-actions-triggers.md) |
| Újrapróbálkozások minimális késése | 5 másodperc | Az alapértelmezett érték módosításához használja az [újrapróbálkozási házirend paramétert.](../logic-apps/logic-apps-workflow-actions-triggers.md) |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Egyéni összekötőkorlátok

Íme a webes API-kból létrehozható egyéni összekötők korlátai.

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezetének korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| Egyéni összekötők száma | Azure-előfizetésenként 1000 | Azure-előfizetésenként 1000 ||
| Egyéni összekötőkérelmek száma percenként | 500 kérelem percenként kapcsolatonként | 2000 kérelem percenként *egyéni összekötőnként* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Felügyelt identitások

| Név | Korlát |
|------|-------|
| Felügyelt identitások logikai alkalmazásonként | A rendszer által hozzárendelt identitás vagy 1 felhasználó által hozzárendelt identitás |
| Azon logikai alkalmazások száma, amelyek felügyelt identitással rendelkeznek egy Azure-előfizetésben régiónként | 250 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Integrációs fiók korlátai

Minden Azure-előfizetés rendelkezik a következő integrációs fiókkorlátokkal:

* Egy [ingyenes rétegintegrációs](../logic-apps/logic-apps-pricing.md#integration-accounts) fiók Azure-régiónként

* 1000 teljes integrációs fiók, beleértve az [integrációs fiókokat bármely integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) a [fejlesztői és a prémium szintű ski-kben egyaránt.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

* Minden ISE, akár [fejlesztő, akár prémium,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)legfeljebb 5 teljes integrációs fiókra korlátozódik:

  | ISE Termékváltozat | Integrációs fiók korlátai |
  |---------|----------------------------|
  | **Prémium** | 5 összesen - [Csak standard](../logic-apps/logic-apps-pricing.md#integration-accounts) számlák, beleértve egy Standard számla ingyen. Ingyenes vagy alapszintű fiókok nem engedélyezettek. |
  | **Fejlesztői** | 5 összesen - [Ingyenes](../logic-apps/logic-apps-pricing.md#integration-accounts) (legfeljebb 1 számla) és [standard](../logic-apps/logic-apps-pricing.md#integration-accounts) kombinált, vagy az összes Standard számlák. Alapszintű fiókok nem engedélyezettek. A [fejlesztői termékváltozat](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) ot kísérletezéshez, fejlesztéshez és teszteléshez használja, éles vagy teljesítményteszteléshez azonban nem. |
  |||

További költségek vonatkoznak az ISE-ben található integrációs fiókokon kívül hozzáadott integrációs fiókokra. Ha meg szeretné tudni, hogyan működik az ise-k díjszabása és számlázása, olvassa el a [Logic Apps díjszabási modelljét.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Az árak, lásd: [Logic Apps árképzés.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Műtermék-korlátok integrációs fiókonként

Az alábbiakban az egyes integrációs fiókszintek összetevőinek számát korlátozza.
Az árak, lásd: [Logic Apps árképzés.](https://azure.microsoft.com/pricing/details/logic-apps/) Ha meg szeretné tudni, hogyan működik az integrációs fiókok díjszabása és számlázása, olvassa el a [Logic Apps díjszabási modelljét.](../logic-apps/logic-apps-pricing.md#integration-accounts)

> [!NOTE]
> Az ingyenes szint csak feltáró forgatókönyvek, nem éles forgatókönyvek. Ez a szint korlátozza az átviteli és használati, és nincs szolgáltatásiszint-szerződés (SLA).

| Összetevő | Ingyenes | Basic | Standard |
|----------|------|-------|----------|
| EDI kereskedelmi megállapodások | 10 | 1 | 1,000 |
| EDI kereskedelmi partnerek | 25 | 2 | 1,000 |
| Maps | 25 | 500 | 1,000 |
| Sémák | 25 | 500 | 1,000 |
| Szerelvények | 10 | 25 | 1,000 |
| Tanúsítványok | 25 | 2 | 1,000 |
| Kötegkonfigurációk | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Műtermék kapacitáskorlátai

| Összetevő | Korlát | Megjegyzések |
| -------- | ----- | ----- |
| Szerelvény | 8 MB | A 2 MB-nál nagyobb fájlok feltöltéséhez használjon [egy Azure-tárfiókot és egy blobtárolót.](../logic-apps/logic-apps-enterprise-integration-schemas.md) |
| Térkép (XSLT-fájl) | 8 MB | A 2 MB-nál nagyobb fájlok feltöltéséhez használja az [Azure Logic Apps REST API – Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). <p><p>**Megjegyzés:** A térkép sikeresen feldolgozható adatok vagy rekordok mennyisége az Azure Logic Apps üzenetméretétől és a művelet időkorlátjaitól függ. Ha például http-műveletet használ a [HTTP-üzenetek mérete és az időtúllépési korlátok](#request-limits)alapján, a térkép feltudja dolgozni az adatokat a HTTP-üzenet méretkorlátjáig, ha a művelet a HTTP-időkorláton belül fejeződik be. |
| Séma | 8 MB | A 2 MB-nál nagyobb fájlok feltöltéséhez használjon [egy Azure-tárfiókot és egy blobtárolót.](../logic-apps/logic-apps-enterprise-integration-schemas.md) |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Átviteli korlátok

| Futásidejű végpont | Ingyenes | Basic | Standard | Megjegyzések |
|------------------|------|-------|----------|-------|
| Hívások olvasása 5 percenként | 3,000 | 30,000 | 60.000 | A számítási feladatok igény szerint több fiók között is eloszthatók. |
| Hívások meghívása 5 percenként | 3,000 | 30,000 | 45.000 | A számítási feladatok igény szerint több fiók között is eloszthatók. |
| Hívások nyomon követése 5 percenként | 3,000 | 30,000 | 45.000 | A számítási feladatok igény szerint több fiók között is eloszthatók. |
| Egyidejű hívások blokkolása | ~1000 | ~1000 | ~1000 | Ugyanez vonatkozik az összes skiszra. Csökkentheti az egyidejű kérelmek számát, vagy szükség szerint csökkentheti az időtartamot. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B protokoll (AS2, X12, EDIFACT) üzenetméret

A B2B protokollokra vonatkozó üzenetméret-korlátok a következők:

| Név | Több-bérlős korlát | Integrációs szolgáltatás környezetének korlátja | Megjegyzések |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 50 MB | v2 - 200 MB <br>v1 - 50 MB | Dekódolni és kódolni |
| X12 | 50 MB | 50 MB | Dekódolni és kódolni |
| EDIFACT | 50 MB | 50 MB | Dekódolni és kódolni |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Logikai alkalmazások letiltása vagy törlése

Ha letilt egy logikai alkalmazást, a nem hoz új futtatásokat a példányosítani.
Az összes folyamatban lévő és függőben lévő futtatás a befejezésükig folytatódik, ami időbe telhet.

Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat.
A rendszer minden folyamatban lévő és függő futtatást megszakít.
Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Tűzfal konfigurációja: IP-címek és szolgáltatáscímkék

Az Azure Logic Apps által a bejövő és kimenő hívásokhoz használt IP-címek attól függ, hogy a régió, ahol a logikai alkalmazás létezik. *Az* ugyanabban a régióban lévő összes logikai alkalmazás ugyanazokat az IP-címtartományokat használja. Egyes [Power Automate-hívások,](https://docs.microsoft.com/power-automate/getting-started) például **a HTTP-** és **HTTP + OpenAPI-kérések** közvetlenül az Azure Logic Apps szolgáltatáson keresztül érkeznek, és az itt felsorolt IP-címekről érkeznek. A Power Automate által használt IP-címekről a [Korlátok és konfiguráció a Power Automate alkalmazásban című témakörben](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration)talál további információt.

> [!TIP]
> A biztonsági szabályok létrehozásakor az összetettség csökkentése érdekében a szolgáltatáscímkék et is [használhatja,](../virtual-network/service-tags-overview.md)ahelyett, hogy megadnák az egyes régiók logikai alkalmazások IP-címeit, amelyet a jelen szakasz később ismertetett ismertet. Ezek a címkék azokon a régiókon működnek, ahol a Logic Apps szolgáltatás elérhető:
>
> * **LogicAppsManagement**: A Logic Apps szolgáltatás bejövő IP-címelőtagait jelöli.
> * **LogicApps:** A Logic Apps szolgáltatás kimenő IP-címelőtagait jelöli.

* A logikai alkalmazások által közvetlenül [HTTP,](../connectors/connectors-native-http.md) [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)és más HTTP-kérelmekkel kezdeményezett hívások támogatásához állítsa be a tűzfalat a Logic Apps szolgáltatás által használt összes [bejövő](#inbound) *és* [kimenő](#outbound) IP-címmel, azok a régiók alapján, ahol a logikai alkalmazások léteznek. Ezek a címek ebben a szakaszban a **Bejövő** és **kimenő** fejlécek alatt jelennek meg, és régió szerint vannak rendezve.

* A [Microsoft által felügyelt összekötők](../connectors/apis-list.md) által kezdeményezett hívások támogatásához állítsa be a tűzfalat az összekötők által használt *összes* [kimenő](#outbound) IP-címmel, azok on-men alapján, ahol a logikai alkalmazások léteznek. Ezek a címek ebben a szakaszban a **Kimenő** fejléc alatt jelennek meg, és régió szerint vannak rendezve.

* Az integrációs szolgáltatási környezetben (ISE) futó logikai alkalmazások kommunikációjának engedélyezéséhez győződjön meg arról, hogy [megnyitja ezeket a portokat.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)

* Ha a logikai alkalmazások problémái vannak a [tűzfalakat és tűzfalszabályokat](../storage/common/storage-network-security.md)használó Azure storage-fiókok [elérésével, számos lehetőség közül választhat a hozzáférés engedélyezéséhez.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

  Például a logikai alkalmazások nem férhetnek hozzá közvetlenül a tűzfalszabályokat használó és ugyanabban a régióban létező tárfiókokhoz. Azonban ha engedélyezi a [kimenő IP-címeket a felügyelt összekötők a régióban,](../logic-apps/logic-apps-limits-and-config.md#outbound)a logikai alkalmazások hozzáférhetnek a tárfiókok, amelyek egy másik régióban, kivéve, ha az Azure Table Storage vagy az Azure Queue Storage-összekötők használatakor. A table storage vagy a queue storage eléréséhez használhatja a HTTP-eseményindítót és a műveleteket. További lehetőségek: [Access storage fiókok tűzfalak mögött](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

* Egyéni összekötők esetén az [Azure Government](../azure-government/documentation-government-overview.md)és az Azure [China 21Vianet](https://docs.microsoft.com/azure/china/)esetén a rögzített vagy fenntartott IP-címek nem érhetők el.

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Bejövő IP-címek

Ez a szakasz csak az Azure Logic Apps szolgáltatás bejövő IP-címeit sorolja fel. A biztonsági szabályok létrehozásakor az összetettség csökkentése érdekében a [service tag](../virtual-network/service-tags-overview.md), **LogicAppsManagement**, ahelyett, hogy az egyes régiókhoz bejövő Logic Apps IP-címelőtagokat ad meg. Ez a címke a Logic Apps szolgáltatás elérhető régióiban működik. Ha az Azure Government szolgáltatásban van, olvassa el az [Azure Government – Bejövő IP-címek című témakört.](#azure-government-inbound)

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Több-bérlős Azure – Bejövő IP-címek

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
| Kelet-Japán | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Nyugat-Japán | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Dél-Korea középső régiója | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Dél-Korea déli régiója | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA északi középső régiója | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Észak-Európa | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Dél-Afrika Észak-Afrika | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Dél-Afrika Nyugati | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA déli középső régiója | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Dél-India | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Délkelet-Ázsia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Az Egyesült Királyság déli régiója | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Az Egyesült Királyság nyugati régiója | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA nyugati középső régiója | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Nyugat-Európa | 13.95.155.53, 52.174.54.218, 52.174.49.6, 52.174.49.6 |
| Nyugat-India | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA nyugati régiója | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA nyugati régiója, 2. | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government – bejövő IP-címek

| Az Azure Government régiója | IP |
|-------------------------|----|
| USA-beli államigazgatás – Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| USA-beli államigazgatás – Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| USA-beli államigazgatás – Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD – Középső régió | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Kimenő IP-címek

Ez a szakasz az Azure Logic Apps szolgáltatás és a felügyelt összekötők kimenő IP-címeit sorolja fel. A biztonsági szabályok létrehozásakor az összetettség csökkentése érdekében a [szolgáltatáscímkét](../virtual-network/service-tags-overview.md), a **LogicApps**-, és nem adhatja meg az egyes régiók kimenő Logic Apps IP-címelőhivatkozásait. Ez a címke a Logic Apps szolgáltatás elérhető régióiban működik. Felügyelt összekötők esetén használja az IP-címeket. Ha az Azure Government szolgáltatásban van, olvassa el az [Azure Government – Kimenő IP-címek című témakört.](#azure-government-outbound)

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Több-bérlős Azure – Kimenő IP-címek

| Régió | Logikai alkalmazások IP-címe | Felügyelt összekötők IP |
|--------|---------------|-----------------------|
| Kelet-Ausztrália | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213, 52.237.214.72 |
| Délkelet-Ausztrália | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34, 52.255.48.202 |
| Dél-Brazília | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207, 191.232.191.157 |
| Közép-Kanada | 52.233.29.92, 52.228.39.241, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 13.71.170.208 - 13.71.170.223, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.31.197, 52.237.24.126, 52.237.32.212 |
| Kelet-Kanada | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152, 52.242.30.112 |
| Közép-India | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164, 52.172.212.129 |
| USA középső régiója | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164, 52.173.241.27 |
| Kelet-Ázsia | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169, 13.75.110.131 |
| USA keleti régiója | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 40.114.40.132, 40.71.249.139 |
| USA 2. keleti régiója | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100, 104.209.247.23, 52.225.129.144 |
| Közép-Franciaország | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.79.130.208 - 40.79.130.223, 40.89.135.2, 40.89.186.239 |
| Dél-Franciaország | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 40.79.178.240 - 40.79.178.255, 52.136.133.184, 52.136.142.154 |
| Kelet-Japán | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96, 13.73.21.230 |
| Nyugat-Japán | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248, 104.215.27.24 |
| Dél-Korea középső régiója | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.231.18.208 - 52.231.18.223, 52.141.36.214, 52.141.1.104 |
| Dél-Korea déli régiója | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.147.0 - 52.231.147.15, 52.231.163.10, 52.231.201.173 |
| USA északi középső régiója | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230, 52.162.126.4 |
| Észak-Európa | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9, 94.245.91.93, 52.169.28.181 |
| Dél-Afrika Észak-Afrika | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.168.167 |
| Dél-Afrika Nyugati | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.72.85 |
| USA déli középső régiója | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 52.171.130.92 |
| Dél-India | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225, 13.71.127.26 |
| Délkelet-Ázsia | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19, 52.187.115.69 |
| Az Egyesült Királyság déli régiója | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15, 51.140.61.124, 51.140.74.150 |
| Az Egyesült Királyság nyugati régiója | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105, 51.141.124.13, 51.141.52.185 |
| USA nyugati középső régiója | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 13.78.132.82, 52.161.101.204 |
| Nyugat-Európa | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118, 40.91.208.65, 52.166.78.89 |
| Nyugat-India | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218, 104.211.189.124 |
| USA nyugati régiója | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49, 40.112.195.87, 13.93.148.62 |
| USA nyugati régiója, 2. | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 52.191.164.250 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government – kimenő IP-címek

| Régió | Logikai alkalmazások IP-címe | Felügyelt összekötők IP |
|--------|---------------|-----------------------|
| USA-beli államigazgatás – Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91 |
| USA-beli államigazgatás – Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225 |
| USA-beli államigazgatás – Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
| US DoD – Középső régió | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136 |
||||

## <a name="next-steps"></a>További lépések

* Az [első logikai alkalmazás létrehozásának ismertetése](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* További információ a [gyakori példákról és forgatókönyvekről](../logic-apps/logic-apps-examples-and-scenarios.md)
