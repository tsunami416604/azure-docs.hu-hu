---
title: Korlátozások és konfiguráció – Azure Logic Apps |} A Microsoft Docs
description: A szolgáltatás korlátozásai és konfigurációs értékei az Azure Logic Apps
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 7726d7eae2d9b3a37ffc141e4a4cbdc9a1af459c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145037"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Korlátozások és konfigurációs adatokat az Azure Logic Apps

Ez a cikk ismerteti a korlátozások és konfigurációs részleteire vonatkozó létrehozásának és futtatásának automatizált munkafolyamatokat az Azure Logic Apps. Microsoft Flow, lásd: [korlátozásai és konfigurációja a Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definíciós korlátok

Az alábbiakban egy logikai alkalmazás definícióját a korlátokat:

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Műveletek munkafolyamatonként | 500 | Bővíti ki a korlátot, az Ön beágyazott munkafolyamatok hozzáadásával igény szerint. |
| Műveletek beágyazásának mélység | 8 | Bővíti ki a korlátot, az Ön beágyazott munkafolyamatok hozzáadásával igény szerint. | 
| A munkafolyamatok régiónként és előfizetésenként | 1,000 | | 
| Eseményindítók munkafolyamatonként | 10 | Amikor a kód nézetben, a tervező nem működik | 
| Kapcsoló hatókör esetek korlát | 25 | | 
| Változók munkafolyamatonként | 250 | | 
| Karakterszám kifejezésenként | 8,192 | | 
| Maximális mérete `trackedProperties` | 16000 karakterből | 
| Nevezze el a `action` vagy `trigger` | 80 karakter | | 
| Hossza `description` | maximális hossz 256 karakter | | 
| Maximális `parameters` | 50 | | 
| Maximális `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Futtatás időtartama és megőrzési korlátok

Az alábbiakban az egyetlen logikai alkalmazás futtatásának korlátai:

| Name (Név) | Korlát | Megjegyzések | 
|------|-------|-------| 
| Futtatás időtartama | 90 nap | Ha módosítani szeretné ezt a korlátot, lásd: [módosítása Futtatás időtartama](#change-duration). | 
| Tárterület adatmegőrzése | a Futtatás 90 nap kezdete | Ha módosítani szeretné ezt a korlátot, lásd: [adatmegőrzés storage módosítása](#change-retention). | 
| Minimális ismétlődési időköz | 1 másodperc | | 
| Maximális ismétlődési időköz | 500 nap | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Futtatás időtartama és a storage adatmegőrzés módosítása

7 nap, és 90 nap közötti értékre módosíthatja ezt a korlátot. Meghaladja a maximális, go [a Logic Apps-csapat](mailto://logicappsemail@microsoft.com) segítség az igényeinek.

1. Az Azure Portalon, a logikai alkalmazás menüjében válassza a **munkafolyamat-beállítások**. 

2. A **futásidejű beállítások**, az a **futtatási Előzmények megőrzése nap** menüben válassza ki **egyéni**. 

3. Adja meg, vagy húzza a csúszkát a kívánt napok száma.

<a name="looping-debatching-limits"></a>

## <a name="looping-and-debatching-limits"></a>A hurkokat és a kötegelés megszüntetésére vonatkozó korlátok

Az alábbiakban az egyetlen logikai alkalmazás futtatásának korlátai:

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| UNTIL-iterációk | 5000 | | 
| ForEach-elemek | 100 000 | Használhatja a [lekérdezési művelet](../connectors/connectors-native-query.md) szűrhet nagyobb tömböket igény szerint. | 
| ForEach-párhuzamosság | 50 | Az alapértelmezett érték 20. <p>Ez a ForEach ciklus az alapértelmezett szint módosításához állítsa a `runtimeConfiguration` tulajdonságot a `foreach` művelet. <p>Egymás után futtatja a ForEach ciklus, állítsa be a `operationOptions` "Szekvenciális" tulajdonságot a `foreach` művelet. | 
| SplitOn-elemek | 100 000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Teljesítmény korlátok

Az alábbiakban az egyetlen logikai alkalmazás futtatásának korlátai:

| Name (Név) | Korlát | Megjegyzések | 
| ----- | ----- | ----- | 
| Művelet-végrehajtások száma 5 percenként | 100 000 | A 300,000 korlátjának növelését, futtathatja a logikai alkalmazás `High Throughput` mód. A magas átviteli mód, konfigurálása a `runtimeConfiguration` a munkafolyamat-erőforrás beállítása a `operationOptions` tulajdonságot `OptimizedForHighThroughput`. <p>**Megjegyzés:**: magas átviteli mód előzetes verzióban érhető el. Ezenkívül juttathatja el a munkaterhelés egynél több alkalmazás között igény szerint. | 
| Műveletek egyidejű kimenő hívások | ~2,500 | Csökkentse az egyidejű kérelmek számát, vagy igény szerint időtartamának csökkentése érdekében. | 
| Futásidejű végpont: egyidejű beérkező hívások | ~1,000 | Csökkentse az egyidejű kérelmek számát, vagy igény szerint időtartamának csökkentése érdekében. | 
| Futásidejű végpont: olvassa el a hívások száma 5 percenként  | 60,000 | Is szét a számítási feladatok több alkalmazás igény szerint. | 
| Futásidejű végpont: 5 percenként hívások meghívása| 45,000 | Is szét a számítási feladatok több alkalmazás igény szerint. | 
|||| 

Nyissa meg a fenti ezeket a korlátokat, a normál feldolgozása, vagy futtassa a terheléses tesztelés díjaival, előfordulhat, hogy nyissa meg a fenti ezeket a korlátokat [a Logic Apps-csapat](mailto://logicappsemail@microsoft.com) segítség az igényeinek.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>HTTP-kérelmekre vonatkozó korlátok

Az alábbiakban egy egyetlen HTTP-kérés vagy a szinkron összekötő hívás a korlátokat:

#### <a name="timeout"></a>Időtúllépés

Néhány összekötő műveleteket aszinkron hívásokat, illetve figyeljen a webhook-kérelmekre, így előfordulhat, hogy ezek a korlátok hosszabb ezeket a műveleteket az időkorlátot. További információkért tekintse meg az adott összekötőre vonatkozó technikai részleteket, és emellett [munkafolyamat triggerei és műveletei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Kimenő kérelmek | 120 másodperc | A hosszabb ideig futó műveletek használata egy [lekérdezési aszinkron minta](../logic-apps/logic-apps-create-api-app.md#async-pattern) vagy egy [until ciklus](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Szinkron válaszra | 120 másodperc | Lekérni a választ az eredeti kérés a válaszban szereplő összes lépést kell be az időkorláton belül, kivéve, ha egy másik logikai alkalmazás egy beágyazott munkafolyamatot hívja. További információkért lásd: [logikai alkalmazások hívása, eseményindító, vagy beágyazása](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Üzenet mérete

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Üzenet mérete | 100 MB | Kerülheti meg ezt a korlátot, lásd: [darabolás a nagyméretű üzenetek kezelése](../logic-apps/logic-apps-handle-large-messages.md). Azonban egyes összekötők és API-k előfordulhat, hogy nem támogatja a darabolás vagy akár az alapértelmezett korlát. | 
| Darabolás az üzenet mérete | 1 GB | Ez a korlátozás vonatkozik, műveletek, natív módon támogatja a darabolás, és lehetővé teszik a futtatókörnyezet konfigurálásukban darabolás engedélyezése. További információkért lásd: [darabolás a nagyméretű üzenetek kezelése](../logic-apps/logic-apps-handle-large-messages.md). | 
| Kifejezések kiértékelési korlátja | 131 072 karakter | A `@concat()`, `@base64()`, `@string()` kifejezések nem haladhatja meg ezt a korlátot. | 
|||| 

#### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Újrapróbálkozási kísérletek | 90 | Az alapértelmezett érték a 4. Az alapértelmezett módosításához használja a [ismételje meg a szabályzatparaméter](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Maximális késleltetése | 1 nap | Az alapértelmezett módosításához használja a [ismételje meg a szabályzatparaméter](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Ismételje meg a minimális késleltetés | 5 másodperc | Az alapértelmezett módosításához használja a [ismételje meg a szabályzatparaméter](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Egyéni összekötő korlátok

Az alábbiakban a webes API-kat hozhat létre egyéni összekötőket vonatkozó korlátokat.

| Name (Név) | Korlát | 
| ---- | ----- | 
| Egyéni összekötők száma | Azure-előfizetésenként 1000 | 
| Percenkénti kérések száma az egyéni összekötő által létesített egyes kapcsolatokon | kapcsolatonként 500 kérés |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Integrációs fiók korlátok

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Integrációs fiók összetevő vonatkozó korlátok

Az alábbiakban az összetevők az egyes integrációs fiókok számára vonatkozó korlátok. További információkért lásd: [Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps/). 

*Ingyenes szint*

Az ingyenes szinten csak felderítő forgatókönyvek esetén nem termelési forgatókönyvekhez használja. Ez a szint korlátozza az átviteli sebesség és a használat, és nincs szolgáltatásszint-szerződés (SLA) rendelkezik.

| Összetevő | Korlát | Megjegyzések | 
|----------|-------|-------| 
| Kereskedelmi EDI-partnerek | 25 | | 
| Kereskedelmi EDI-szerződést | 10 | | 
| Maps | 25 | | 
| Sémák | 25 | 
| Szerelvények | 10 | | 
| Kötegkonfigurációk | 5 | 
| Tanúsítványok | 25 | | 
|||| 

*Alapszintű csomag*

| Összetevő | Korlát | Megjegyzések | 
|----------|-------|-------| 
| Kereskedelmi EDI-partnerek | 2 | | 
| Kereskedelmi EDI-szerződést | 1 | | 
| Maps | 500 | | 
| Sémák | 500 | 
| Szerelvények | 25 | | 
| Kötegkonfigurációk | 1 | | 
| Tanúsítványok | 2 | | 
|||| 

*Standard csomag*

| Összetevő | Korlát | Megjegyzések | 
|----------|-------|-------| 
| Kereskedelmi EDI-partnerek | 500 | | 
| Kereskedelmi EDI-szerződést | 500 | | 
| Maps | 500 | | 
| Sémák | 500 | 
| Szerelvények | 50 | | 
| Kötegkonfigurációk | 5 |  
| Tanúsítványok | 50 | | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Összetevő kapacitáskorlátait

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Séma | 8 MB | 2 MB-nál nagyobb fájlok feltöltéséhez használja a [blob URI](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Térkép (XSLT-fájl) | 2 MB | | 
| Futásidejű végpont: olvassa el a hívások száma 5 percenként | 60,000 | Szükség szerint több fiókot is a számítási feladatok szét. | 
| Futásidejű végpont: 5 percenként hívások meghívása | 45,000 | Szükség szerint több fiókot is a számítási feladatok szét. | 
| Futásidejű végpont: 5 percenként hívások nyomon követése | 45,000 | Szükség szerint több fiókot is a számítási feladatok szét. | 
| Futásidejű végpont: blokkolja a párhuzamos hívások | ~1,000 | Csökkentse az egyidejű kérelmek számát, vagy szükség szerint időtartamának csökkentése érdekében. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B-protokoll (AS2 X12, EDIFACT) üzenet mérete

A korlátok, amelyek a alkalmazni a B2B-protokollok a következők:

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Dekódolása és kódolása vonatkozik | 
| X12 | 50 MB | Dekódolása és kódolása vonatkozik | 
| EDIFACT | 50 MB | Dekódolása és kódolása vonatkozik | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Konfiguráció: IP-címek

### <a name="azure-logic-apps-service"></a>Az Azure Logic Apps szolgáltatás

Minden logikai alkalmazás egy régióban használja ugyanazokat a tartományokat, IP-címek. A hívások, amelyek a logic apps közvetlenül a támogatásához [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md), és más HTTP-kéréseket, így ezen kimenő és bejövő IP-címek,-alapú tartalmazzák a tűzfal-konfiguráció beállítása az, ahol a logic apps létezik:

| Logic Apps-régió | Kimenő IP |
|-------------------|-------------|
| Kelet-Ausztrália | 13.75.149.4, 104.210.91.55, 104.210.90.241 |
| Délkelet-Ausztrália | 13.73.114.207, 13.77.3.139, 13.70.159.205 |
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

| Logic Apps-régió | Bejövő IP |
|-------------------|------------|
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

A hívások támogatására, amely [összekötők](../connectors/apis-list.md) márka, állítsa be a tűzfalbeállításokat, így ezen kimenő IP-címek tartoznak a régiók, ahol a logic apps létezik alapján.

> [!IMPORTANT]
>
> Ha rendelkezik meglévő konfigurációk, frissítse őket **2018. szeptember 1. előtt a lehető leghamarabb** , ezek közé tartozik, és a régiók, ahol a logic apps létezik-e a listában szereplő IP-címekkel egyező. 

| Logic Apps-régió | Kimenő IP | 
|-------------------|-------------|  
| Kelet-Ausztrália | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Délkelet-Ausztrália | 13.77.50.240 - 13.77.50.255, 13.70.136.174, 40.127.80.34 | 
| Dél-Brazília | 191.233.203.192 - 191.233.203.207, 104.41.59.51, 191.232.38.129 | 
| Közép-Kanada | -13.71.170.223, 13.71.170.224 - 13.71.170.208 13.71.170.239, 52.237.24.126, 52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13, 52.233.26.83 | 
| Kelet-Kanada | 40.69.106.240 - 40.69.106.255, 52.242.35.152, 52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52, 52.229.120.131 | 
| Közép-India | 104.211.81.192 - 104.211.81.207, 52.172.211.12, 104.211.98.164 | 
| USA középső régiója | 13.89.171.80 - 13.89.171.95, 52.173.245.164, 40.122.49.51 | 
| Kelet-Ázsia | 13.75.36.64 - 13.75.36.79, 52.175.23.169, 23.99.116.181 | 
| USA keleti régiója | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| USA 2. keleti régiója | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Kelet-Japán | 13.78.108.0 - 13.78.108.15, 13.71.153.19, 40.115.186.96 | 
| Nyugat-Japán | 40.74.100.224 - 40.74.100.239, 104.215.61.248, 40.74.130.77 | 
| USA északi középső régiója | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Észak-Európa | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| USA déli középső régiója | 104.214.19.48 - 104.214.19.63, 13.65.86.57, 104.214.70.191 | 
| Dél-India | 40.78.194.240 - 40.78.194.255, 13.71.125.22, 104.211.227.225 | 
| Délkelet-Ázsia | 13.67.8.240 - 13.67.8.255, 52.187.68.19, 13.76.231.68 | 
| USA nyugati középső régiója | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 52.161.27.108, 52.161.30.5, 52.161.29.35, 52.161.26.212, 52.161.128 | 
| Nyugat-Európa | 13.69.64.208 - 13.69.64.223, 52.174.88.118, 40.115.50.13 | 
| Nyugat-India | 104.211.146.224 - 104.211.146.239, 104.211.189.218, 104.211.161.203 | 
| USA nyugati régiója | 40.112.243.160 - 40.112.243.175, 104.42.122.49, 104.40.51.248 | 
| USA nyugati régiója, 2. | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 13.66.225.219, 13.66.218.78, 13.66.220.135, 13.66.219.14, 13.66.221.19 | 
| Az Egyesült Királyság déli régiója | 51.140.148.0 - 51.140.148.15, 51.140.80.51 | 
| Az Egyesült Királyság nyugati régiója | 51.140.211.0 - 51.140.211.15, 51.141.47.105 | 
| | | 

## <a name="next-steps"></a>További lépések  

* Ismerje meg, hogyan [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Ismerje meg [gyakori példák és forgatókönyvek](../logic-apps/logic-apps-examples-and-scenarios.md)
