---
title: Korlátozások és konfiguráció – Azure Logic Apps |} A Microsoft Docs
description: A szolgáltatás korlátozásai és konfigurációs értékei az Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 8aa2627f46be1e375fb3c3e565848a930ba6726b
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167443"
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
| Tárterület adatmegőrzése | a Futtatás 90 nap kezdete | 7 nap, és 90 nap közötti értéket módosítja ezt a korlátot, lásd: [adatmegőrzés storage módosítása](#change-retention). | 
| Minimális ismétlődési időköz | 1 másodperc | | 
| Maximális ismétlődési időköz | 500 nap | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Futtatás időtartama és a storage adatmegőrzés módosítása

Ha módosítani szeretné az alapértelmezett korlát a 7 nap, és 90 nap között, kövesse az alábbi lépéseket. Ha meg kell meghaladja a maximális, [a Logic Apps-csapat](mailto://logicappsemail@microsoft.com) segítség az igényeinek.

1. Az Azure Portalon, a logikai alkalmazás menüjében válassza a **munkafolyamat-beállítások**. 

2. A **futásidejű beállítások**, az a **futtatási Előzmények megőrzése nap** menüben válassza ki **egyéni**. 

3. Adja meg, vagy húzza a csúszkát a kívánt napok száma.

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Letiltása, vagy törölje a logikai alkalmazások

Ha letilt egy logikai alkalmazást, nincsenek új futtatások példányosítása. Az összes folyamatban lévő és a függőben lévő futtatásának továbbra is csak akkor fejeződik be, amely időt is igénybe vehet.

Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Egyidejűségi, hurkokat és a kötegelés megszüntetésére vonatkozó korlátok

Az alábbiakban az egyetlen logikai alkalmazás futtatásának korlátai:

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Az eseményindító egyidejűségi | 50 | Az alapértelmezett érték 20. Ez a korlátozás ismerteti egy időben, vagy a párhuzamosan futtatható logic app-példányok maximális száma. <p><p>Ha módosítani szeretné az alapértelmezett korlát egy értéknek 1 és 50 között szélsőértékeket is beleértve, lásd: [módosítása az eseményindító egyidejűségi](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) vagy [példányok egymás után aktiválása](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). | 
| Maximális várakozási futtatások | 100 | Az alapértelmezett érték 10. Ez a korlátozás ismerteti a logikai alkalmazás futtatását, amikor a logikai alkalmazás már fut a legnagyobb párhuzamos alkalmazáspéldányok várhat példányok maximális száma. <p><p>Ha módosítani szeretné az alapértelmezett korlát 0 és 100 közötti értéket szélsőértékeket is beleértve, lásd: [módosítása várakozási futtatások korlátozza](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). | 
| Foreach-elemek | 100 000 | Ez a korlátozás maximális számát, és egy "mindegyikre" hurkot feldolgozására képes tömbelemek ismerteti. <p><p>Nagyobb tömböket szűrhet, használhatja a [lekérdezési művelet](../connectors/connectors-native-query.md). | 
| Foreach ciklus ismétléseinek | 50 | Az alapértelmezett érték 20. Ez a korlátozás ismerteti a "for each" maximális számát az ismétlések egy időben, vagy a párhuzamosan futtatható ikonjához. <p><p>Az alapértelmezett korlát a egy értéke 1 és 50 között szélsőértékeket is beleértve, című témakör nyújt [módosítása "for each" egyidejűségi](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) vagy [futtassa a "for each" hurkokat egymás után](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). | 
| SplitOn-elemek | 100 000 | | 
| UNTIL-iterációk | 5000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Teljesítmény korlátok

Az alábbiakban az egyetlen logikai alkalmazás futtatásának korlátai:

| Name (Név) | Korlát | Megjegyzések | 
| ---- | ----- | ----- | 
| Művelet: Végrehajtások száma 5 percenként | 300,000 | Az alapértelmezett érték 100 000. Az alapértelmezett korlát módosításához lásd [a logikai alkalmazás futtatása "nagy átviteli sebességű" módban](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), amely jelenleg előzetes verzióban. Vagy a számítási feladatok szét szükség szerint több mint egy logikai alkalmazást. | 
| Művelet: Párhuzamos kimenő hívások | ~2,500 | Csökkentse az egyidejű kérelmek számát, vagy szükség szerint időtartamának csökkentése érdekében. | 
| Futásidejű végpont: egyidejű beérkező hívások | ~1,000 | Csökkentse az egyidejű kérelmek számát, vagy szükség szerint időtartamának csökkentése érdekében. | 
| Futásidejű végpont: olvassa el a hívások száma 5 percenként  | 60,000 | Számítási feladatok között egynél több alkalmazáshoz szükség szerint terjesztheti. | 
| Futásidejű végpont: 5 percenként hívások meghívása | 45,000 | Számítási feladatok között egynél több alkalmazáshoz szükség szerint terjesztheti. | 
| Tartalom átviteli 5 percenként | 600 MB | Számítási feladatok között egynél több alkalmazáshoz szükség szerint terjesztheti. | 
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

<a name="sftp"></a>

## <a name="sftp-and-sftp-ssh-limits"></a>Az SFTP- és az SFTP-SSH korlátozásai

### <a name="file-size"></a>Fájlméret

| Name (Név) | Korlát | Megjegyzések |
|------|-------|-------|
| SFTP | 50 MB | Ezt a korlátot, használja a [SFTP-SSH-összekötő](../connectors/connectors-sftp-ssh.md) , vagy tekintse meg [darabolás a nagyméretű üzenetek kezelése](../logic-apps/logic-apps-handle-large-messages.md). Azonban egyes összekötők és API-k előfordulhat, hogy nem támogatja a darabolás vagy akár az alapértelmezett korlát. | 
| AZ SFTP-SSH | 1 GB | Kerülheti meg ezt a korlátot, lásd: [darabolás a nagyméretű üzenetek kezelése](../logic-apps/logic-apps-handle-large-messages.md). Azonban egyes összekötők és API-k előfordulhat, hogy nem támogatja a darabolás vagy akár az alapértelmezett korlát. | 
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Egyéni összekötő korlátok

Az alábbiakban a webes API-kat hozhat létre egyéni összekötőket vonatkozó korlátokat.

| Name (Név) | Korlát | 
| ---- | ----- | 
| Egyéni összekötők száma | Azure-előfizetésenként 1000 | 
| Percenkénti kérések száma az egyéni összekötő által létesített egyes kapcsolatokon | kapcsolatonként 500 kérés |
|||| 

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Felügyelt identitások

| Name (Név) | Korlát | 
| ---- | ----- | 
| A logic apps, a rendszer által hozzárendelt száma felügyelt identitások Azure-előfizetésenként | 10 | 
|||

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
| Kelet-Ausztrália | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 |
| Délkelet-Ausztrália | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 |
| Dél-Brazília | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 |
| Közép-Kanada | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Kelet-Kanada | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 |
| Közép-India | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 |
| USA középső régiója | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 |
| Kelet-Ázsia | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 |
| USA keleti régiója | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 |
| USA 2. keleti régiója | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 |
| Kelet-Japán | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 |
| Nyugat-Japán | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| USA északi középső régiója | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 |
| Észak-Európa | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| USA déli középső régiója | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 |
| Dél-India | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 |
| Délkelet-Ázsia | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| USA nyugati középső régiója | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 |
| Nyugat-Európa | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 |
| Nyugat-India | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 |
| USA nyugati régiója | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |
| USA nyugati régiója, 2. | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 |
| Az Egyesült Királyság déli régiója | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 |
| Az Egyesült Királyság nyugati régiója | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 |
| | |

| Logic Apps-régió | Bejövő IP |
|-------------------|------------|
| Kelet-Ausztrália | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Délkelet-Ausztrália | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Dél-Brazília | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Közép-Kanada | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Kelet-Kanada | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Közép-India | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA középső régiója | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Kelet-Ázsia | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| USA keleti régiója | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| USA 2. keleti régiója | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Kelet-Japán | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Nyugat-Japán | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| USA északi középső régiója | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Észak-Európa | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| USA déli középső régiója | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Dél-India | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Délkelet-Ázsia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| USA nyugati középső régiója | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Nyugat-Európa | 13.95.155.53, 52.174.49.6, 52.174.49.6, 52.174.54.218 |
| Nyugat-India | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| USA nyugati régiója | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| USA nyugati régiója, 2. | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Az Egyesült Királyság déli régiója | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Az Egyesült Királyság nyugati régiója | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

### <a name="connectors"></a>Összekötők

A hívások támogatására, amely [összekötők](../connectors/apis-list.md) márka, állítsa be a tűzfalbeállításokat, így ezen kimenő IP-címek tartoznak a régiók, ahol a logic apps létezik alapján.

> [!IMPORTANT]
> Ha rendelkezik meglévő konfigurációk, frissítse őket **2018. szeptember 1. előtt a lehető leghamarabb** , ezek közé tartozik, és a régiók, ahol a logic apps létezik-e a listában szereplő IP-címekkel egyező. 
> 
> A Logic Apps nem támogatja az Azure storage-fiókok tűzfalon keresztül közvetlenül csatlakozik. Ezek a tárfiókok eléréséhez mindkét módszerhez itt: 
>
> * Hozzon létre egy [integrációs szolgáltatás környezet](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), amelyhez csatlakozhat egy Azure virtuális hálózatban lévő erőforrásokra. 
> 
> * Ha már használja az API Management, használhatja a szolgáltatást az ebben a forgatókönyvben. További információ: [egyszerű vállalati integrációs architektúra](http://aka.ms/aisarch).

| Logic Apps-régió | Kimenő IP | 
|-------------------|-------------|  
| Kelet-Ausztrália | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Délkelet-Ausztrália | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 | 
| Dél-Brazília | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 | 
| Közép-Kanada | -13.71.170.223, 13.71.170.224 - 13.71.170.208 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 | 
| Kelet-Kanada | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 | 
| Közép-India | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 | 
| USA középső régiója | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 | 
| Kelet-Ázsia | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 | 
| USA keleti régiója | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| USA 2. keleti régiója | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Kelet-Japán | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 | 
| Nyugat-Japán | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 | 
| USA északi középső régiója | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Észak-Európa | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| USA déli középső régiója | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 | 
| Dél-India | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 | 
| Délkelet-Ázsia | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 | 
| USA nyugati középső régiója | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 | 
| Nyugat-Európa | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 | 
| Nyugat-India | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 | 
| USA nyugati régiója | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 | 
| USA nyugati régiója, 2. | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 | 
| Az Egyesült Királyság déli régiója | 51.140.80.51, 51.140.148.0 - 51.140.148.15 | 
| Az Egyesült Királyság nyugati régiója | 51.140.211.0 - 51.140.211.15, 51.141.47.105 | 
| | | 

## <a name="next-steps"></a>További lépések  

* Ismerje meg, hogyan [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Ismerje meg [gyakori példák és forgatókönyvek](../logic-apps/logic-apps-examples-and-scenarios.md)
