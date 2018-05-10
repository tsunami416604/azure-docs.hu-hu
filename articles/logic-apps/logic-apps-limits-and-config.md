---
title: Korlátozásai és a konfiguráció – Azure Logic Apps |} Microsoft Docs
description: Szolgáltatás korlátozásai és a konfigurációs értékeket az Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: anneta
editor: ''
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 524a2dc7a1a5ae4f0747af03d1b9e69d512f0f00
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Korlátozásai és az Azure Logic Apps konfigurációs adatait

Ez a cikk ismerteti, korlátozások és létrehozásának és az Azure Logic Apps futó automatizált munkafolyamatok konfigurációs adatait. Microsoft Flow, lásd: [korlátozásai és konfigurációja a Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definition korlátok

Az alábbiakban egy logikai alkalmazás definícióját határértékei:

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Műveletek munkafolyamat száma | 500 | Ezt a határt kiterjesztéséhez beágyazott munkafolyamatok igény szerint is hozzáadhat. |
| A beágyazás mélységére műveletekhez engedélyezett | 8 | Ezt a határt kiterjesztéséhez beágyazott munkafolyamatok igény szerint is hozzáadhat. | 
| Régiónként munkafolyamatok | 1,000 | | 
| Eseményindítók munkafolyamat száma | 10 | Ha a kód nézetre, a tervező nem működik | 
| Kapcsoló hatókör esetekben korlátot | 25 | | 
| Változók munkafolyamat száma | 250 | | 
| Karakteres kifejezés | 8,192 | | 
| Maximális mérete `trackedProperties` | 16 000 karakternél | 
| A név `action` vagy `trigger` | 80 karakter | | 
| Hossza `description` | 256 karakternél | | 
| Maximális `parameters` | 50 | | 
| Maximális `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Futtassa a telepítések időtartamát és megőrzési korlátok

Az alábbiakban a futtatásához egy logikai alkalmazások korlátai:

| Name (Név) | Korlát | 
| ---- | ----- | 
| Futtassa a időtartama | 90 nap | 
| Tároló megőrzési | a Futtatás 90 napon kezdési időpontja | 
| Minimális ismétlési időköz | 1 másodperc </br>A logic apps a egy App Service-csomag számára: 15 másodperc | 
| Maximális ismétlési időköz | 500 (nap) | 
||| 

Futtatási időtartama vagy a normál feldolgozása folyamatában tároló megőrzési hosszabb legyen, mint a korlátok [a Logic Apps csapattól](mailto://logicappsemail@microsoft.com) segítség az igényeinek.

<a name="looping-debatching-limits"></a>

## <a name="looping-and-debatching-limits"></a>Hurok- és debatching korlátok

Az alábbiakban a futtatásához egy logikai alkalmazások korlátai:

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Amíg az ismétlés | 5000 | | 
| ForEach-elemek | 100,000 | Használhatja a [lekérdezési művelet](../connectors/connectors-native-query.md) szűrése nagyobb tömbök igény szerint. | 
| ForEach párhuzamossági | 50 | Az alapértelmezett érték 20. <p>ForEach hurkot valamilyen konkrét szintje párhuzamossági beállításához adja meg a `runtimeConfiguration` tulajdonságot a `foreach` művelet. <p>ForEach hurkot egymás után futtatja, állítsa be a `operationOptions` "Sorostevékenység" tulajdonságot a `foreach` művelet. | 
| SplitOn elemek | 100,000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Átviteli sebességének korlátai

Az alábbiakban a futtatásához egy logikai alkalmazások korlátai:

| Name (Név) | Korlát | Megjegyzések | 
| ----- | ----- | ----- | 
| Műveletek végrehajtások / 5 perc | 100,000 | A 300,000 korlát növelése érdekében futtathat logikai alkalmazás `High Throughput` mód. Állíthatja be a magas teljesítmény módot, a `runtimeConfiguration` a munkafolyamat erőforrás állítsa be a `operationOptions` tulajdonságot `OptimizedForHighThroughput`. <p>**Megjegyzés:**: nagy átviteli mód jelenleg előzetes verzióban érhető. A munkaterhelés terjesztheti is, szükség szerint több alkalmazás között. | 
| Műveletek párhuzamos kimenő hívások | ~2,500 | Csökkentse az egyidejű kérelmek száma, vagy igény szerint időtartamának csökkentése érdekében. | 
| Futásidejű végpont: párhuzamos bejövő hívások | ~1,000 | Csökkentse az egyidejű kérelmek száma, vagy igény szerint időtartamának csökkentése érdekében. | 
| Futásidejű végpont: 5 perc hívásszám olvasása  | 60,000 | Szükség szerint több alkalmazást is munkaterhelés szét. | 
| Futásidejű végpont: 5 perc hívásszám meghívása| 45,000 |Szükség szerint több alkalmazást is munkaterhelés szét. | 
|||| 

A meghaladja ezt a korlátot, a normál feldolgozása vagy futtatási terhelés tesztelése, amelyek túllépik a működés felső korlátjának, [a Logic Apps csapattól](mailto://logicappsemail@microsoft.com) segítség az igényeinek.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>HTTP-kérelmekre vonatkozó korlátok

Az alábbiakban egy HTTP-kérelem vagy szinkron összekötő hívás korlátok:

#### <a name="timeout"></a>Időtúllépés

Néhány összekötő műveleteket aszinkron hívások, illetve figyelni a webhook-kérelmeket, ezért lehet, hogy ezek a műveletek időtúllépés hosszabb, mint a működés felső korlátjának. További információkért tekintse meg az adott összekötőt vonatkozó technikai részleteket, továbbá [munkafolyamat eseményindítók és műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Kimenő kérelem | 120 másodperc | Hosszabb ideig futó műveletek esetén használjon egy [lekérdezési aszinkron mintát](../logic-apps/logic-apps-create-api-app.md#async-pattern) vagy egy [amíg hurok](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Szinkron válasz | 120 másodperc | Az eredeti kérés lekérni a választ a válaszban szereplő lépéseket kell Befejezés a korláton belüli kivéve, ha meghívja a beágyazott munkafolyamat egy másik logikai alkalmazást. További információkért lásd: [hívja, eseményindító, vagy a logic apps beágyazásához](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Üzenet mérete

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Üzenet mérete | 100 MB | Egyes összekötők és API-k nem támogathatja 100 MB. | 
| Kifejezés kiértékelése korlátot | 131 072 karakter | A `@concat()`, `@base64()`, `@string()` kifejezések nem lehetnek hosszabbak ezt a határt. | 
|||| 

#### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Újrapróbálkozási kísérletek | 90 | Az alapértelmezett érték 4. Az alapértelmezett módosításához használja a [ismételje meg a házirend paraméter](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Maximális késleltetése | 1 nap | Az alapértelmezett módosításához használja a [ismételje meg a házirend paraméter](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Ismételje meg a minimális késleltetési | 5 másodperc | Az alapértelmezett módosításához használja a [ismételje meg a házirend paraméter](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Egyéni összekötő korlátok

Az alábbiakban a webes API-k hozhat létre egyéni összekötőkre vonatkozó határértékeket.

| Name (Név) | Korlát | 
| ---- | ----- | 
| Egyéni összekötők száma | Azure-előfizetésenként 1000 | 
| Percenkénti kérések száma az egyéni összekötő által létesített egyes kapcsolatokon | legfeljebb 500 kérelmek |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Integráció korlátairól

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Integráció fiókonként összetevő korlátok

Az alábbiakban az egyes integrációs fiókok számára az összetevők számát használati korlátait.

*Ingyenes tarifacsomag*

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Egyezmények | 10 | | 
| Más összetevő típusa | 25 | Összetevő-típusok a partnerek, sémákat, tanúsítványok és a maps tartalmaznak. Egyes összetevők maximális száma legfeljebb tartalmazhat. | 
|||| 

*Standard szintű tarifacsomagban használható*

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Bármilyen típusú összetevő | 500 | Összetevő-típusok a megállapodások, a partnerek, a sémák, a tanúsítványok és a maps tartalmaznak. Egyes összetevők maximális száma legfeljebb tartalmazhat. | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Összetevő kapacitáskorlátait

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Séma | 8 MB | 2 MB-nál nagyobb méretű fájlokat feltölteni, használja a [blob URI](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Térkép (XSLT-fájl) | 2 MB | | 
| Futásidejű végpont: 5 perc hívásszám olvasása | 60,000 | Szükség szerint több fiók lehet a munkaterhelés szét. | 
| Futásidejű végpont: 5 perc hívásszám meghívása | 45,000 | Szükség szerint több fiók lehet a munkaterhelés szét. | 
| Futásidejű végpont: 5 perc hívásszám nyomon követése | 45,000 | Szükség szerint több fiók lehet a munkaterhelés szét. | 
| Futásidejű végpont: blokkolja az egyidejű hívások | ~1,000 | Egyidejű kérelmek számának csökkentéséhez, vagy szükség szerint időtartamának csökkentése érdekében. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B protokoll (AS2, X12, EDIFACT) üzenet mérete

Az alábbiakban a B2B protokollok vonatkozó korlátozások:

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Dekódolás és kódolása vonatkozik | 
| X12 | 50 MB | Dekódolás és kódolása vonatkozik | 
| EDIFACT | 50 MB | Dekódolás és kódolása vonatkozik | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>A konfiguráció: IP-címek

### <a name="azure-logic-apps-service"></a>Az Azure Logic Apps szolgáltatás

Minden a logic apps régióban használja az ugyanazon az IP-címeket.
A hívásokat, amelyek a logic apps közvetlenül a [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) vagy IP-címeket a listában szereplő származhatnak más HTTP-kérelmekre. 

| Logic Apps régió | Kimenő IP |
|-------------------|-------------|
| Ausztrália | 13.73.114.207, 13.77.3.139, 13.70.159.205 |
| Kelet-Ausztrália | 13.75.149.4, 104.210.91.55, 104.210.90.241 |
| Dél-Brazília | 191.235.82.221, 191.235.91.7, 191.234.182.26 |
| Közép-Kanada | 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Kelet-Kanada | 52.232.128.155, 52.229.120.45, 52.229.126.25 |
| Közép-India | 52.172.154.168, 52.172.186.159, 52.172.185.79 |
| USA középső régiója | 13.67.236.125, 104.208.25.27, 40.122.170.198 |
| Kelet-Ázsia | 13.75.94.173, 40.83.127.19, 52.175.33.254 |
| USA keleti régiója | 13.92.98.111, 40.121.91.41, 40.114.82.191 |
| USA 2. keleti régiója | 40.84.30.147, 104.208.155.200, 104.208.158.174 |
| Kelet-Japán | 13.71.158.3, 13.73.4.207, 13.71.158.120 |
| Nyugat-Japán | 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| USA északi középső régiója | 168.62.248.37, 157.55.210.61, 157.55.212.238 |
| Észak-Európa | 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| USA déli középső régiója | 104.210.144.48, 13.65.82.17, 13.66.52.232 |
| Dél-India | 52.172.50.24, 52.172.55.231, 52.172.52.0 |
| Délkelet-Ázsia | 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| USA nyugati középső régiója | 52.161.27.190, 52.161.18.218, 52.161.9.108 |
| Nyugat-Európa | 40.68.222.65, 40.68.209.23, 13.95.147.65 |
| Nyugat-India | 104.211.164.80, 104.211.162.205, 104.211.164.136 |
| USA nyugati régiója | 52.160.92.112, 40.118.244.241, 40.118.241.243 |
| USA nyugati régiója, 2. | 13.66.210.167, 52.183.30.169, 52.183.29.132 |
| Az Egyesült Királyság déli régiója | 51.140.74.14, 51.140.73.85, 51.140.78.44 |
| Az Egyesült Királyság nyugati régiója | 51.141.54.185, 51.141.45.238, 51.141.47.136 |
| | |

| Logic Apps régió | Bejövő IP |
|-------------------|-------------|
| Kelet-Ausztrália | 3.75.153.66, 104.210.89.222, 104.210.89.244 |
| Délkelet-Ausztrália | 13.73.115.153, 40.115.78.70, 40.115.78.237 |
| Dél-Brazília | 191.235.86.199, 191.235.95.229, 191.235.94.220 |
| Közép-Kanada | 13.88.249.209, 52.233.30.218, 52.233.29.79 |
| Kelet-Kanada | 52.232.129.143, 52.229.125.57, 52.232.133.109 |
| Közép-India | 52.172.157.194, 52.172.184.192, 52.172.191.194 |
| USA középső régiója | 13.67.236.76, 40.77.111.254, 40.77.31.87 |
| Kelet-Ázsia | 168.63.200.173, 13.75.89.159, 23.97.68.172 |
| USA keleti régiója | 137.135.106.54, 40.117.99.79, 40.117.100.228 |
| USA 2. keleti régiója | 40.84.25.234, 40.79.44.7, 40.84.59.136 |
| Kelet-Japán | 13.71.146.140, 13.78.84.187, 13.78.62.130 |
| Nyugat-Japán | 40.74.140.173, 40.74.81.13, 40.74.85.215 |
| USA északi középső régiója | 168.62.249.81, 157.56.12.202, 65.52.211.164 |
| Észak-Európa | 13.79.173.49, 52.169.218.253, 52.169.220.174 |
| USA déli középső régiója | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Dél-India | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Délkelet-Ázsia | 52.163.93.214, 52.187.65.81, 52.187.65.155 |
| USA nyugati középső régiója | 52.161.26.172, 52.161.8.128, 52.161.19.82 |
| Nyugat-Európa | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Nyugat-India | 104.211.164.112, 104.211.165.81, 104.211.164.25 |
| USA nyugati régiója | 52.160.90.237, 138.91.188.137, 13.91.252.184 |
| USA nyugati régiója, 2. | 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Az Egyesült Királyság déli régiója | 51.140.79.109, 51.140.78.71, 51.140.84.39 |
| Az Egyesült Királyság nyugati régiója | 51.141.48.98, 51.141.51.145, 51.141.53.164 |
| | |

### <a name="connectors"></a>Összekötők

A hívások, amely [összekötők](../connectors/apis-list.md) ellenőrizze, hogy a listában szereplő IP-címekről érkeznek.

| Logic Apps régió | Kimenő IP |
|-------------------|-------------|
| Kelet-Ausztrália | 40.126.251.213 |
| Délkelet-Ausztrália | 40.127.80.34 |
| Dél-Brazília | 191.232.38.129 |
| Közép-Kanada | 52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13 |
| Kelet-Kanada | 52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52 |
| Közép-India | 104.211.98.164 |
| USA középső régiója | 40.122.49.51 |
| Kelet-Ázsia | 23.99.116.181 |
| USA keleti régiója | 191.237.41.52 |
| USA 2. keleti régiója | 104.208.233.100 |
| Kelet-Japán | 40.115.186.96 |
| Nyugat-Japán | 40.74.130.77 |
| USA északi középső régiója | 65.52.218.230 |
| Észak-Európa | 104.45.93.9 |
| USA déli középső régiója | 104.214.70.191 |
| Dél-India | 104.211.227.225 |
| Délkelet-Ázsia | 13.76.231.68 |
| Nyugat-Európa | 40.115.50.13 |
| Nyugat-India | 104.211.161.203 |
| USA nyugati régiója | 104.40.51.248 |
| Az Egyesült Királyság déli régiója | 51.140.80.51 |
| Az Egyesült Királyság nyugati régiója | 51.141.47.105 |
| | | 

## <a name="next-steps"></a>További lépések  

* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Gyakori példák és forgatókönyvek](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Videó: Üzleti folyamatok automatizálása a Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Videó: Rendszerintegráció a Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
