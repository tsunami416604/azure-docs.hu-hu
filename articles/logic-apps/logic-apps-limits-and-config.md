---
title: "Korlátozásai és a konfiguráció – Azure Logic Apps |} Microsoft Docs"
description: "Szolgáltatás korlátozásai és a konfigurációs értékeket az Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 8de3cc6665fc3861aedcf4f36cdce4a4a776e80d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="logic-apps-limits-and-configuration"></a>Logic Apps korlátozásai és konfigurációja

Ez a cikk ismerteti a jelenlegi korlátozások és a konfigurációs adatait az Azure Logic Apps.

## <a name="limits"></a>Korlátok

### <a name="http-request-limits"></a>HTTP-kérelmekre vonatkozó korlátok

Ezek a korlátozások vonatkoznak az egyetlen HTTP-kérelem vagy az összekötő hívásakor.

#### <a name="timeout"></a>Időtúllépés

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Kérés időtúllépése | 120 másodperc | Egy [aszinkron mintát](../logic-apps/logic-apps-create-api-app.md) vagy [amíg hurok](logic-apps-loops-and-scopes.md) pótolhatja igény szerint |
|||| 

#### <a name="message-size"></a>Üzenet mérete

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Üzenet mérete | 100 MB | Egyes összekötők és API-k nem támogathatja 100 MB. | 
| Kifejezés kiértékelése korlátot | 131 072 karakter | `@concat()`, `@base64()`, `string` nem lehetnek hosszabbak ezt a határt. | 
|||| 

#### <a name="retry-policy"></a>Újrapróbálkozási házirend

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Újrapróbálkozási kísérletek | 90 | Az alapértelmezett érték 4. Akkor is konfigurálhatja a [ismételje meg a házirend paraméter](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Maximális késleltetése | 1 nap | Akkor is konfigurálhatja a [ismételje meg a házirend paraméter](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Ismételje meg a minimális késleltetési | 5 másodperc | Akkor is konfigurálhatja a [ismételje meg a házirend paraméter](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

### <a name="run-duration-and-retention"></a>Futtatási tartamára és fenntartására

Ezek a korlátozások vonatkoznak egyetlen logikai alkalmazás futtatásához.

| Name (Név) | Korlát | 
| ---- | ----- | 
| Futtassa a időtartama | 90 nap | 
| Tároló megőrzési | a Futtatás 90 napon kezdési időpontja | 
| Min ismétlési időköz | 1 másodperc </br>A logic apps a egy App Service-csomag számára: 15 másodperc | 
| Maximális ismétlési időköz | 500 (nap) | 
||| 

Futtatási időtartama vagy a normál feldolgozása folyamatában tároló megőrzési hosszabb legyen, mint a korlátok [lépjen kapcsolatba a termékért felelős csoport](mailto://logicappsemail@microsoft.com) segítség az igényeinek.

### <a name="looping-and-debatching-limits"></a>Hurok- és debatching korlátok

Ezek a korlátozások vonatkoznak egyetlen logikai alkalmazás futtatásához.

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| ForEach-elemek | 100,000 | Használhatja a [lekérdezési művelet](../connectors/connectors-native-query.md) szűrése nagyobb tömbök igény szerint. | 
| Amíg az ismétlés | 5,000 | | 
| SplitOn elemek | 100,000 | | 
| ForEach párhuzamossági | 50 | Az alapértelmezett érték 20. <p>ForEach hurkot valamilyen konkrét szintje párhuzamossági beállításához adja meg a `runtimeConfiguration` tulajdonságot a `foreach` művelet. <p>ForEach hurkot egymás után futtatja, állítsa be a `operationOptions` "Sorostevékenység" tulajdonságot a `foreach` művelet. | 
|||| 

### <a name="throughput-limits"></a>Átviteli sebességének korlátai

Ezek a korlátozások vonatkoznak egy logikai alkalmazás-erőforrást.

| Name (Név) | Korlát | Megjegyzések | 
| ----- | ----- | ----- | 
| Műveletek végrehajtások / 5 perc | 100,000 |<p>A korlát növelhető 300,000 logikai alkalmazás futtatásával `High Througput` mód. Nagy átviteli mód konfigurálható úgy, hogy a `operationOptions` tulajdonság alapján`runtimeConfiguration` a munkafolyamat-erőforrás a `OptimizedForHighThroughput`. <p>Vegye figyelembe, hogy a magas teljesítmény üzemmód jelenleg előzetes verzióban érhető. Emellett a munkaterhelés terjeszthető több alkalmazás között szükség szerint. | 
| Műveletek párhuzamos kimenő hívások | ~2,500 | Csökkentse az egyidejű kérelmek száma, vagy igény szerint időtartamának csökkentése érdekében. | 
| Futásidejű végpont: párhuzamos bejövő hívások |~1,000 | Csökkentse az egyidejű kérelmek száma, vagy igény szerint időtartamának csökkentése érdekében. | 
| Futásidejű végpont: 5 perc hívásszám olvasása  | 60,000 | Szükség szerint több alkalmazást is munkaterhelés szét. | 
| Futásidejű végpont: 5 perc hívásszám meghívása| 45,000 |Szükség szerint több alkalmazást is munkaterhelés szét. | 
|||| 

A meghaladja ezt a korlátot, a normál feldolgozása vagy futtatási terhelés tesztelése, amelyek túllépik a működés felső korlátjának, [lépjen kapcsolatba a termékért felelős csoport](mailto://logicappsemail@microsoft.com) segítség az igényeinek.

### <a name="logic-app-definition-limits"></a>Logic app definition korlátok

Ezek a korlátozások vonatkoznak a egyetlen logic app-definíciót.

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Műveletek munkafolyamat száma | 500 | Ezt a határt kiterjesztéséhez beágyazott munkafolyamatok igény szerint is hozzáadhat. |
| A beágyazás mélységére művelet engedélyezett | 8 | Ezt a határt kiterjesztéséhez beágyazott munkafolyamatok igény szerint is hozzáadhat. | 
| Régiónként munkafolyamatok | 1000 | | 
| Eseményindítók munkafolyamat száma | 10 | | 
| Kapcsoló hatókör esetekben korlátot | 25 | | 
| A változók / munkafolyamat száma | 250 | | 
| Maximális karakterszám kifejezés | 8,192 | | 
| Maximális `trackedProperties` méret karakter | 16,000 | 
| `action`/`trigger`név korlátot | 80 | | 
| `description`korlát | 256 | | 
| `parameters`korlát | 50 | | 
| `outputs`korlát | 10 | | 
|||| 

<a name="custom-connector-limits"></a>

### <a name="custom-connector-limits"></a>Egyéni összekötő korlátok

Ezek a korlátozások webes API-k hozhat létre egyéni összekötőkre vonatkoznak.

| Name (Név) | Korlát | 
| ---- | ----- | 
| Létrehozható egyéni összekötők száma | Azure-előfizetésenként 1000 | 
| Percenkénti kérések száma az egyéni összekötő által létesített egyes kapcsolatokon | Az összekötő által létesített minden kapcsolatonként 500 kérés |
||| 

### <a name="integration-account-limits"></a>Integráció korlátairól

Ezek a korlátozások vonatkoznak az összetevők, amelyek egy integrációs fiókot is hozzáadhat.

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Séma | 8 MB | Használhat [blob URI](../logic-apps/logic-apps-enterprise-integration-schemas.md) 2 MB-nál nagyobb méretű fájlokat feltölteni. | 
| Térkép (XSLT-fájl) | 2 MB | | 
| Futásidejű végpont: 5 perc hívásszám olvasása | 60,000 | Is elosztják a munkaterhelést több fiók igény szerint. | 
| Futásidejű végpont: 5 perc hívásszám meghívása | 45,000 | Is elosztják a munkaterhelést több fiók igény szerint. | 
| Futásidejű végpont: 5 perc hívásszám nyomon követése | 45,000 | Is elosztják a munkaterhelést több fiók igény szerint. | 
| Futásidejű végpont: blokkolja az egyidejű hívások | ~1,000 | Egyidejű kérelmek számának csökkentéséhez, vagy szükség szerint időtartamának csökkentése érdekében. | 
|||| 

Ezek a korlátozások vonatkoznak, amelyek egy integrációs fiókot is hozzáadhat az összetevők számát.

#### <a name="free-pricing-tier"></a>Ingyenes tarifacsomag

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Egyezmények | 10 | | 
| Más összetevő típusa | 25 |Összetevő-típusok a partnerek, sémákat, tanúsítványok és a maps tartalmaznak. Egyes összetevők maximális száma legfeljebb tartalmazhat. | 
|||| 

#### <a name="standard-pricing-tier"></a>Standard szintű tarifacsomagban használható

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Bármilyen típusú összetevő | 500 | Összetevő-típusok a megállapodások, a partnerek, a sémák, a tanúsítványok és a maps tartalmaznak. Egyes összetevők maximális száma legfeljebb tartalmazhat. | 
|||| 

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>B2B protokollok (AS2, X12, EDIFACT) üzenet mérete

Ezek a korlátozások vonatkoznak a B2B protokollokat.

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Dekódolás és kódolása vonatkozik | 
| X12 | 50 MB | Dekódolás és kódolása vonatkozik | 
| EDIFACT | 50 MB | Dekódolás és kódolása vonatkozik | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configuration: IP Addresses

### <a name="logic-apps-service"></a>Logic Apps service

A logikai alkalmazás közvetlenül teszi lehetővé, ez azt jelenti, hogy hívások keresztül [HTTP](../connectors/connectors-native-http.md) vagy [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) vagy IP-címeket a listában szereplő származhatnak más HTTP-kérelmekre.

|Logic Apps régió|Kimenő IP|
|-----------------|-----------|
|Kelet-Ausztrália|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Délkelet-Ausztrália|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Dél-Brazília|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Közép-Kanada|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Kelet-Kanada|52.232.128.155, 52.229.120.45, 52.229.126.25|
|Közép-India|52.172.154.168, 52.172.186.159, 52.172.185.79|
|USA középső régiója|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Kelet-Ázsia|13.75.94.173, 40.83.127.19, 52.175.33.254|
|USA keleti régiója|13.92.98.111, 40.121.91.41, 40.114.82.191|
|USA 2. keleti régiója|40.84.30.147, 104.208.155.200, 104.208.158.174|
|Kelet-Japán|13.71.158.3, 13.73.4.207, 13.71.158.120|
|Nyugat-Japán|40.74.140.4, 104.214.137.243, 138.91.26.45|
|USA északi középső régiója|168.62.248.37, 157.55.210.61, 157.55.212.238|
|Észak-Európa|40.113.12.95, 52.178.165.215, 52.178.166.21|
|USA déli középső régiója|104.210.144.48, 13.65.82.17, 13.66.52.232|
|Délkelet-Ázsia|13.76.133.155, 52.163.228.93, 52.163.230.166|
|Dél-India|52.172.50.24, 52.172.55.231, 52.172.52.0|
|USA nyugati középső régiója|52.161.27.190, 52.161.18.218, 52.161.9.108|
|Nyugat-Európa|40.68.222.65, 40.68.209.23, 13.95.147.65|
|Nyugat-India|104.211.164.80, 104.211.162.205, 104.211.164.136|
|USA nyugati régiója|52.160.92.112, 40.118.244.241, 40.118.241.243|
|USA nyugati régiója, 2.|13.66.210.167, 52.183.30.169, 52.183.29.132|
|Az Egyesült Királyság déli régiója|51.140.74.14, 51.140.73.85, 51.140.78.44|
|Az Egyesült Királyság nyugati régiója|51.141.54.185, 51.141.45.238, 51.141.47.136|
| | |

### <a name="connectors"></a>Összekötők

A hívások, amely [összekötők](../connectors/apis-list.md) ellenőrizze, hogy a listában szereplő IP-címekről érkeznek.

|Logic Apps régió|Kimenő IP|
|-----------------|-----------|
|Kelet-Ausztrália|40.126.251.213|
|Délkelet-Ausztrália|40.127.80.34|
|Dél-Brazília|191.232.38.129|
|Közép-Kanada|52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13|
|Kelet-Kanada|52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52|
|Közép-India|104.211.98.164|
|USA középső régiója|40.122.49.51|
|Kelet-Ázsia|23.99.116.181|
|USA keleti régiója|191.237.41.52|
|USA 2. keleti régiója|104.208.233.100|
|Kelet-Japán|40.115.186.96|
|Nyugat-Japán|40.74.130.77|
|USA északi középső régiója|65.52.218.230|
|Észak-Európa|104.45.93.9|
|USA déli középső régiója|104.214.70.191|
|Délkelet-Ázsia|13.76.231.68|
|Dél-India|104.211.227.225|
|Nyugat-Európa|40.115.50.13|
|Nyugat-India|104.211.161.203|
|USA nyugati régiója|104.40.51.248|
|Az Egyesült Királyság déli régiója|51.140.80.51|
|Az Egyesült Királyság nyugati régiója|51.141.47.105|
| | | 

## <a name="next-steps"></a>További lépések  

* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Gyakori példák és forgatókönyvek](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Videó: Üzleti folyamatok automatizálása a Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Videó: Rendszerintegráció a Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
