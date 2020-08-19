---
title: Az Azure Logic Apps összekötői
description: Azure Logic Apps-összekötők, például beépített, felügyelt, helyszíni, integrációs fiók, ISE és vállalati összekötők összekapcsolása munkafolyamatok automatizálásával
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 1dd38f0360a4471124497d8357481283cd98383c
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566301"
---
# <a name="connectors-for-azure-logic-apps"></a>Az Azure Logic Apps összekötői

Az összekötők gyors hozzáférést biztosítanak az Azure Logic Appsből más alkalmazások, szolgáltatások, rendszerek, protokollok és platformok eseményeihez, adataihoz és műveleteihez. A logikai alkalmazások összekötőivel kibővítheti a felhőbeli és a helyszíni alkalmazások képességeit annak érdekében, hogy feladatokat hajthassanak végre a létrehozott és már meglévő adatokkal.

Míg Logic Apps [több száz összekötőt](/connectors)kínál, ez a cikk azokat a *népszerű és gyakrabban használt* összekötőket ismerteti, amelyeket több ezer alkalmazás sikeresen használ, és több millió végrehajtást biztosít az adatok és információk feldolgozásához. Az összekötők és az egyes összekötők (például triggerek, műveletek és korlátok) teljes listájának megkereséséhez tekintse át az összekötők hivatkozási lapjait az [Összekötők áttekintése](/connectors)területen. További információ az [Eseményindítók és műveletek](#triggers-actions), a [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md)és a [Logic apps díjszabásáról](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> Ha olyan szolgáltatással vagy API-val szeretne integrálni, amely nem rendelkezik összekötővel, közvetlenül is meghívhatja a szolgáltatást egy protokollon keresztül, például HTTP-n keresztül, vagy létrehozhat egy [Egyéni összekötőt](#custom).

## <a name="connector-types"></a>Összekötő típusai

Az összekötők beépített eseményindítóként és műveletként, illetve felügyelt összekötőként érhetők el.

<a name="built-in"></a>

* [**Beépített**](#built-ins): beépített triggerek és műveletek "natív" Azure Logic apps és segít a következő feladatok elvégzésében a logikai alkalmazásokhoz:

  * Futtatás egyéni és speciális ütemterveken.

  * Rendszerezheti és szabályozhatja a logikai alkalmazás munkafolyamatát, például a hurkokat és a feltételeket, valamint a változók és az adatműveletek működését is.

  * Kommunikáció más végpontokkal.

  * Kérések fogadása és megválaszolása.

  * Hívja meg az Azure functions, az Azure API Apps (Web Apps), a saját API-k által felügyelt és közzétett Azure API Management, valamint a beágyazott logikai alkalmazásokat, amelyek fogadhatnak kérelmeket.

<a name="managed-connectors"></a>

* [**Felügyelt összekötők**](#managed-api-connectors): a Microsoft által központilag telepített és felügyelt, ezek az összekötők a Cloud Services, a helyszíni rendszerek vagy mindkettő, például az Office 365, az Azure Blob Storage, a SQL Server, a Dynamics, a Salesforce, a SharePoint és egyéb szolgáltatások elérésére szolgáló eseményindítókat és műveleteket biztosítanak. Egyes összekötők kifejezetten támogatják a vállalatközi (B2B) kommunikációs forgatókönyveket, és megkövetelik a logikai alkalmazáshoz csatolt [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) . Bizonyos összekötők használata előtt előfordulhat, hogy először a Azure Logic Apps által felügyelt kapcsolatokat kell létrehoznia.

  Ha például Microsoft-BizTalk Server használ, a logikai alkalmazások a [BizTalk Server helyszíni összekötő](#on-premises-connectors)használatával csatlakozhatnak a BizTalk Serverhoz, és kommunikálhatnak velük. Ezután az [integrációs fiók összekötői](#integration-account-connectors)használatával kiterjesztheti vagy elvégezheti a BizTalk-szerű műveleteket a logikai alkalmazásokban.

  Az összekötők a standard vagy a vállalat kategóriába tartoznak. A [vállalati összekötők](#enterprise-connectors) felár ellenében hozzáférést biztosítanak a nagyvállalati rendszerekhez, például az SAP, az IBM MQ és az IBM 3270 szolgáltatásokhoz. Annak megállapításához, hogy az összekötő szabványos vagy nagyvállalati szintű-e, tekintse meg a technikai részleteket az egyes összekötők hivatkozásait ismertető oldalon az [Összekötők áttekintésében](/connectors).

  Az összekötőket ezen kategóriák használatával is azonosíthatja, bár egyes összekötők több kategóriát is használhatnak. Például az SAP egy vállalati összekötő és egy helyszíni összekötő:

  | Kategória | Leírás |
  |----------|-------------|
  | [**Felügyelt összekötők**](#managed-api-connectors) | Az Azure Blob Storage, az Office 365, a Dynamics, a Power BI, a OneDrive, a Salesforce, a SharePoint Online és sok más szolgáltatást használó logikai alkalmazásokat hozhat létre. |
  | [**Helyszíni összekötők**](#on-premises-connectors) | A helyszíni [adatátjáró][gateway-doc]telepítése és beállítása után ezek az összekötők segítenek a logikai alkalmazásoknak olyan helyszíni rendszerek elérésében, mint a SQL Server, a SharePoint Server, a Oracle db, a fájlmegosztás és egyebek. |
  | [**Integrációs fiókok összekötői**](#integration-account-connectors) | Akkor érhető el, ha integrációs fiókot hoz létre és fizet ki, ezek az összekötők átalakítja és érvényesíti az XML-t, kódolja és dekódolja a lapos fájlokat, és feldolgozza a vállalatközi (B2B) üzeneteket az AS2, a EDIFACT és a X12 protokollal. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment-ise"></a>Csatlakozás integrációs szolgáltatási környezetből (ISE)

Azon logikai alkalmazások esetében, amelyeknek közvetlen hozzáférésre van szükségük egy Azure-beli virtuális hálózat erőforrásaihoz, létrehozhat egy dedikált [integrációs szolgáltatási környezetet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , ahol dedikált erőforrásokon hozhatja létre, telepítheti és futtathatja a logikai alkalmazásokat. A Logic app Designerben, amikor az ISE-ben a Logic apps-hez használni kívánt összekötőket böngészi, a **beépített** triggereken és műveleteken megjelenik egy alapfelirat, míg az **ISE** címke egyes összekötőn megjelenik.

> [!NOTE]
> Az ISE-ben és azok összekötői futó Logic apps, függetlenül attól, hogy az összekötők hol futnak, a rögzített díjszabási csomagot és a fogyasztáson alapuló díjszabást kell követnie. További információ: [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md) és [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/).

| Címke | Példa | Description |
|-------|---------|-------------|
| **CORE** | ![Példa ISE-összekötőre](./media/apis-list/example-core-connector.png) | Az ezzel a címkével rendelkező beépített eseményindítók és műveletek a logikai alkalmazásokkal megegyező ISE-ben futnak. |
| **ISE** | ![Példa ISE-összekötőre](./media/apis-list/example-ise-connector.png) | Az ezzel a címkével rendelkező felügyelt összekötők ugyanabban az ISE-ben futnak, mint a Logic apps. Ha egy Azure-beli virtuális hálózathoz csatlakoztatott helyszíni rendszerrel rendelkezik, az ISE lehetővé teszi, hogy a logikai alkalmazások közvetlenül hozzáférhessenek a rendszerhez a [helyszíni adatátjáró](../logic-apps/logic-apps-gateway-connection.md)nélkül. Ehelyett használhatja a rendszer **ISE** -összekötőjét, ha van ilyen, http-művelet vagy [egyéni összekötő](#custom). Az **ISE** -összekötőket nem tartalmazó helyszíni rendszerekhez használja a helyszíni adatátjárót. Az elérhető ISE-összekötők áttekintését lásd: [ISE-összekötők](#ise-connectors). |
| Nincs címke | ![Példa több-bérlős összekötőre](./media/apis-list/example-multi-tenant-connector.png) | Az összes többi olyan összekötő, amely nem rendelkezik a **Core** vagy az **ISE** címkével, amelyet tovább használhat, a globális, több-bérlős Logic apps szolgáltatásban futtathatja. |
|||

<a name="built-ins"></a>

## <a name="built-in"></a>Beépített

A Logic Apps beépített eseményindítókat és műveleteket biztosít, így ütemezhetők-alapú munkafolyamatokat hozhat létre, segítséget nyújthat a logikai alkalmazásoknak más alkalmazásokkal és szolgáltatásokkal való kommunikációhoz, a munkafolyamat vezérléséhez a logikai alkalmazásokon keresztül, valamint az adatkezeléshez és az adatkezeléshez.

| Név | Leírás |
|------|-------------|
| [![API-ikon ][schedule-icon]<br> **ütemterve**][schedule-doc] | – Futtasson egy logikai alkalmazást egy megadott ismétlődésen, amely az alapszintű és a speciális ütemezések között az [ **ismétlődési** eseményindítóval][schedule-recurrence-doc]. <br>– Futtasson egy logikai alkalmazást, amely folyamatos adattömbökben lévő, a [ **csúszó ablakos** triggerrel][schedule-sliding-window-doc]való adatkezelésre szorul. <br>– Szüneteltetheti a logikai alkalmazást egy adott időtartamra a [ **késleltetési** művelettel][schedule-delay-doc]. <br>– Szüneteltetheti a logikai alkalmazást, amíg a [művelet **nem** ][schedule-delay-until-doc]lesz a megadott dátummal és időponttal. |
| [![API-ikon ][batch-icon]<br> **köteg**][batch-doc] | – A Batch **üzenetek** triggerrel rendelkező kötegekben lévő üzenetek feldolgozása. <br>– Olyan logikai alkalmazásokat hívhat meg, amelyek meglévő batch-eseményindítókkal rendelkeznek az **üzenetek küldése kötegelt** művelettel. |
| [![API-ikon ][http-icon]<br> **http**][http-doc] | HTTP- vagy HTTPS-végpontok meghívása HTTP-triggerekkel vagy -műveletekkel. Más beépített HTTP-eseményindítók és műveletek a következők: [http + hencegés][http-swagger-doc] és [http + webhook][http-webhook-doc]. |
| [![API-ikon ][http-request-icon]<br> **kérése**][http-request-doc] | – Meghívhatja a logikai alkalmazást más alkalmazásokból vagy szolgáltatásokból, kiválthatja Event Grid erőforrás-eseményeit, vagy aktiválhatja a válaszokat a **kérések** triggerével Azure Security Center riasztásokra. <br>– Válaszok küldése egy alkalmazásnak vagy szolgáltatásnak a **Válasz** művelettel. |
| [![API-ikon ][azure-api-management-icon]<br> **Azure API <br> Management**][azure-api-management-doc] | Saját, Azure API Managementtel felügyelt és közzétett API-jaival definiált triggereket és műveleteket hívhat meg. |
| [![API-ikon ][azure-app-services-icon]<br> **Azure app <br> Services**][azure-app-services-doc] | Az Azure App Service-en keresztül üzemeltetett Azure API Apps vagy Web Apps meghívása. Az alkalmazások által definiált eseményindítók és műveletek úgy jelennek meg, mint bármely más, az első osztályú eseményindító és művelet, amikor a kivágás bekerül. |
| [![API-ikon ][azure-logic-apps-icon]<br> **Azure Logic <br> apps**][nested-logic-app-doc] | Hívjon fel más Logic apps-alkalmazásokat, amelyek a **kérelem** -triggerrel kezdődnek. |
|||

### <a name="run-code-from-logic-apps"></a>Kód futtatása a Logic appsből

A Logic Apps beépített műveleteket biztosít a saját kód futtatásához a logikai alkalmazás munkafolyamataiban:

| Név | Leírás |
|------|-------------|
| [![API-ikon ][azure-functions-icon]<br> **Azure functions**][azure-functions-doc] | Hívja meg az Azure functions szolgáltatást, amely egyéni kódrészleteket (C# vagy Node.js) futtat a logikai alkalmazásokból. |
| [![API-ikon ][inline-code-icon]<br> **beágyazott kódja**][inline-code-doc] | JavaScript-kódrészletek hozzáadása és futtatása a logikai alkalmazásokból. |
|||

### <a name="control-workflow"></a>Vezérlési munkafolyamat

A Logic Apps beépített műveleteket biztosít a logikai alkalmazás munkafolyamatában lévő műveletek rendszerezéséhez és szabályozásához:

| Név | Leírás |
|------|-------------|
| [![Beépített ikon ][condition-icon]<br> **feltétele**][condition-doc] | Értékelje ki a feltételt, és futtasson különböző műveleteket attól függően, hogy a feltétel igaz vagy hamis. |
| [![Beépített ][for-each-icon]<br> **For each** ikon][for-each-doc] | Hajtsa végre ugyanezeket a műveleteket egy tömb minden elemén. |
| [![Beépített ikon ][scope-icon]<br> **hatóköre**][scope-doc] | A *műveletek hatókörbe*való csoportosítása, amelyek a hatókör befejezésének befejezése után kapják meg a saját állapotukat. |
| [![Beépített ikon ][switch-icon]<br> **kapcsoló**][switch-doc] | Olyan *esetekre*csoportosíthat műveleteket, amelyek az alapértelmezett eset kivételével egyedi értékeket kapnak. Csak akkor futtassa ezt az esetet, ha a hozzárendelt érték megegyezik egy kifejezés, objektum vagy token eredményével. Ha nem létezik egyezés, futtassa az alapértelmezett esetet. |
| [![Beépített ikon ][terminate-icon]<br> **leállítása**][terminate-doc] | Egy aktívan futó logikai alkalmazás munkafolyamatának leállítása. |
| [![Beépített ikon, ][until-icon]<br> **amíg**][until-doc] | Ismételje meg a műveleteket, amíg a megadott feltétel igaz vagy valamilyen állapot megváltozott. |
|||

### <a name="manage-or-manipulate-data"></a>Az adatkezelés és-kezelés

A Logic Apps beépített műveleteket biztosít az adatkimenetek és azok formátumának kezeléséhez:

| Név | Leírás |
|------|-------------|
| [![Beépített ikon ][data-operations-icon]<br> **adatműveletek**][data-operations-doc] | Műveletek végrehajtása adatokkal: <p>- **Összeállítás**: egyetlen kimenet létrehozása több bemenetből különböző típusokkal. <br>- **CSV-táblázat létrehozása**: hozzon létre egy vesszővel tagolt (CSV-) táblázatot egy olyan tömbből, amely JSON-objektumokkal rendelkezik. <br>- **HTML-táblázat létrehozása**: HTML-táblázat létrehozása egy JSON-objektumokkal rendelkező tömbből. <br>- **Tömb szűrése**: hozzon létre egy tömböt egy másik tömb elemeiből, amelyek megfelelnek a feltételeknek. <br>- **Csatlakozás**: hozzon létre egy karakterláncot egy tömb összes eleméről, és válassza szét ezeket az elemeket a megadott elválasztóval. <br>- **JSON**elemzése: hozzon létre felhasználóbarát tokeneket a tulajdonságok és azok értékei alapján a JSON-tartalomban, hogy használhassa ezeket a tulajdonságokat a munkafolyamatban. <br>- **Válassza ki**: hozzon létre egy tömböt JSON-objektumokkal egy másik tömb elemeinek vagy értékeinek átalakításával, és rendelje hozzá ezeket az elemeket a megadott tulajdonságokhoz. |
| ![Beépített ikon][date-time-icon]<br>**Dátum és idő** | Műveletek elvégzése időbélyegekkel: <p>- **Hozzáadás az időponthoz**: adja hozzá a megadott számú egységet egy időbélyeghez. <br>- **Időzóna konvertálása**: a forrás időzóna időbélyegének konvertálása a cél időzónára. <br>- **Aktuális idő**: az aktuális időbélyeg visszaadása karakterláncként. <br>- **Jövőbeli idő beolvasása**: az aktuális időbélyeg és a megadott időegységek visszaadása. <br>- **Beolvasás időpontja**: az aktuális időbélyeg visszaküldése a megadott időegységek szerint. <br>- **Kivonás az időpontból**: több időegység kivonása egy időbélyegből. |
| [![Beépített ikon ][variables-icon]<br> **változók**][variables-doc] | Műveletek végrehajtása változókkal: <p>- **Hozzáfűzés a tömb változóhoz**: szúrjon be egy értéket egy változó által tárolt tömb utolsó elemeként. <br>- **Hozzáfűzés a karakterlánc-változóhoz**: az értéket egy változó által tárolt sztring utolsó karakterének szúrja be. <br>- **Változó csökkentése**: a változókat állandó értékkel csökkentheti. <br>- **Növekmény változó**: állandó értékkel növelheti a változókat. <br>- **Változó inicializálása**: hozzon létre egy változót, és állapítsa meg az adattípus és a kezdeti érték megadását. <br>- **Változó beállítása**: adjon meg egy másik értéket egy meglévő változóhoz. |
|||

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Felügyelt összekötők

A Logic Apps ezeket a népszerű szabványos összekötőket biztosítja a feladatok, folyamatok és munkafolyamatok automatizálásához ezekkel a szolgáltatásokkal vagy rendszerekkel:

| Név | Leírás |
|------|-------------|
| [![API-ikon ][azure-service-bus-icon]<br> **Azure Service Bus**][azure-service-bus-doc] | A Logic Apps leggyakrabban használt összekötőjével aszinkron üzeneteket, munkameneteket és témakör-előfizetéseket kezelhet. |
| [![API-ikon ][sql-server-icon]<br> **SQL Server**][sql-server-doc] | Kapcsolódjon SQL Server a felhőben, vagy egy Azure SQL Database a felhőben, így kezelheti a rekordokat, futtathatja a tárolt eljárásokat vagy lekérdezéseket végezhet. |
| [![API-ikon ][azure-blob-storage-icon]<br> **Azure Blob <br> Storage**][azure-blob-storage-doc] | Kapcsolódjon a Storage-fiókhoz a blob-tartalmak létrehozásához és kezeléséhez. |
| [![API-ikon ][office-365-outlook-icon]<br> **Office 365 <br> Outlook**][office-365-outlook-doc] | Kapcsolódjon az Office 365 e-mail-fiókjához, így e-maileket, feladatokat, naptári eseményeket és értekezleteket, névjegyeket, kéréseket és egyebeket hozhat létre és kezelhet. |
| [![API ][sftp-ssh-icon]<br> **-ikon SFTP – SSH**][sftp-ssh-doc] | Olyan SFTP-kiszolgálókhoz csatlakozhat, amelyek az internetről az SSH használatával érhetők el, hogy a fájlok és mappák is működjenek. |
| [![API-ikon ][sharepoint-online-icon]<br> **SharePoint <br> online**][sharepoint-online-doc] | Kapcsolódjon a SharePoint Online-hoz, hogy kezelje a fájlokat, a mellékleteket, a mappákat és egyebeket. |
| [![API-ikon ][azure-queues-icon]<br> **Azure- <br> várólisták**][azure-queues-doc] | Kapcsolódjon az Azure Storage-fiókjához, hogy várólistákat és üzeneteket lehessen létrehozni és kezelni. |
| [![API-ikon ][ftp-icon]<br> **FTP**][ftp-doc] | Csatlakozhat az internetről elérhető FTP-kiszolgálókhoz, így a fájlokkal és mappákkal is dolgozhat. |
| [![API-ikon ][file-system-icon]<br> **fájlrendszere <br> **][file-system-doc] | Kapcsolódjon a helyszíni fájlmegosztás számára, hogy fájlokat lehessen létrehozni és kezelni. |
| [![API-ikon ][azure-event-hubs-icon]<br> **Azure Event Hubs**][azure-event-hubs-doc] | Események felhasználása és közzététele az Event hub használatával. Az Event Hubs szolgáltatással például lekérheti a logikai alkalmazások kimenetét, majd elküldheti a kimenetet valamely valós idejű elemzési szolgáltatónak. |
| [![API-ikon ][azure-event-grid-icon]<br> **Azure Event** <br> **Grid**][azure-event-grid-doc] | Egy Event Grid által közzétett események figyelése, például amikor az Azure-erőforrások vagy a külső gyártók erőforrásai változnak. |
| [![API-ikon ][salesforce-icon]<br> **Salesforce**][salesforce-doc] | Kapcsolódjon a Salesforce-fiókhoz, így olyan elemeket hozhat létre és kezelhet, mint a rekordok, a feladatok, az objektumok stb. |
|||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Helyszíni összekötők

Íme néhány általánosan használt standard összekötő, amelyet Logic Apps biztosít a helyszíni rendszerekben tárolt adatok és erőforrások eléréséhez. A helyszíni rendszerhez való kapcsolódás előtt először [le kell töltenie, telepítenie és be kell állítania egy helyszíni adatátjárót][gateway-doc]. Ez az átjáró biztonságos kommunikációs csatornát biztosít anélkül, hogy be kellene állítania a szükséges hálózati infrastruktúrát.

:::row:::
    :::column:::
        [![API-ikon ][biztalk-server-icon]<br> **BizTalk** <br> **Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][file-system-icon]<br> **fájlrendszere <br> **][file-system-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-informix-icon]<br> **IBM** <br> **Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][mysql-icon]<br> **MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][oracle-db-icon]<br> **Oracle db**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][postgre-sql-icon]<br> **PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][sharepoint-server-icon]<br> **SharePoint- <br> kiszolgáló**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][sql-server-icon]<br> **SQL <br> Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][teradata-icon]<br> **Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Integrációs fiókok összekötői

A Logic Apps szabványos összekötőket biztosít a vállalatközi (B2B) megoldások létrehozásához és az Azure-beli Enterprise Integration Pack (EIP) keresztül elérhető [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)kifizetéséhez. Ezzel a fiókkal B2B-összetevőket hozhat létre és tárolhat, például kereskedelmi partnereket, szerződéseket, térképeket, sémákat, tanúsítványokat stb. Ezeknek az összetevőknek a használatához társítsa a logikai alkalmazásokat az integrációs fiókjával. Ha jelenleg BizTalk Server használ, előfordulhat, hogy ezek az összekötők már ismerősnek tűnnek.

:::row:::
    :::column:::
        [![API-ikon ][as2-icon]<br> **AS2- <br> dekódolás**][as2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][as2-icon]<br> **AS2- <br> kódolás**][as2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][edifact-icon]<br> **EDIFACT <br> dekódolása**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][edifact-icon]<br> **EDIFACT <br> kódolása**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon – ][flat-file-decode-icon]<br> **lapos fájl <br> dekódolása**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![API-ikon – ][flat-file-encode-icon]<br> **lapos fájl <br> kódolása**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][integration-account-icon]<br> **integrációs <br> fiókja**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][liquid-icon]<br> **folyékony** <br> **átalakítások**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][x12-icon]<br> **X12 <br> dekódolása**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][x12-icon]<br> **X12 <br> kódolása**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![API ][xml-transform-icon]<br> **-ikon XML-** <br> **átalakítások**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![API ][xml-validate-icon]<br> **-ikon XML- <br> ellenőrzése**][xml-validate-doc]
    :::column-end:::
:::row-end:::

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Vállalati összekötők

A Logic Apps a nagyvállalati rendszerekhez (például SAP és IBM MQ) való hozzáféréshez biztosít ilyen vállalati összekötőket:

:::row:::
    :::column:::
        [![API-ikon ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][sap-icon]<br> **SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE-összekötők

A dedikált [integrációs szolgáltatási környezetben (ISE)](#integration-service-environment)létrehozott és futtatott Logic apps esetében a Logic app Designer azonosítja a beépített eseményindítókat és műveleteket, **amelyek az ISE** -ben futnak az alapcímke használatával. Az ISE-ben futó felügyelt összekötők az **ISE** címkét jelenítik meg, míg a globális, több-bérlős Logic apps szolgáltatásban futó összekötők nem jelenítik meg a címkét. Ez a lista a jelenleg ISE-verziókkal rendelkező összekötőket jeleníti meg:

:::row:::
    :::column:::
        [![API-ikon ][as2-icon]<br> **AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-automation-icon]<br> **Azure <br> Automation**][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-blob-storage-icon]<br> **Azure Blob <br> Storage**][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-cosmos-db-icon]<br> **Azure Cosmos <br> db**][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][azure-event-hubs-icon]<br> **Azure Event <br> hubok**][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-event-grid-icon]<br> **Azure Event <br> Grid**][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-file-storage-icon]<br> **Azure file <br> Storage**][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![API-ikon az ][azure-key-vault-icon]<br> **Azure Key <br> vaultban**][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][azure-monitor-logs-icon]<br> **Azure monitor <br> naplók**][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-service-bus-icon]<br> **Azure Service <br> Bus**][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-sql-data-warehouse-icon]<br> **Az Azure SQL- <br> adattárházban**][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][azure-table-storage-icon]<br> **Azure Table <br> Storage**][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][azure-queues-icon]<br> **Azure- <br> várólisták**][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][edifact-icon]<br> **EDIFACT**][edifact-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][file-system-icon]<br> **fájlrendszere <br> **][file-system-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ftp-icon]<br> **FTP**][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API-ikon ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][sap-icon]<br> **SAP**][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API ][sftp-ssh-icon]<br> **-ikon SFTP – SSH**][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][smtp-icon]<br> **SMTP**][smtp-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][sql-server-icon]<br> **SQL <br> Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![API-ikon ][x12-icon]<br> **X12**][x12-doc]
    :::column-end:::
:::row-end:::

További információt az alábbi témakörökben talál:

* [Hozzáférés az Azure-beli virtuális hálózati erőforrásokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md)
* [Kapcsolódás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Eseményindítók és műveleti típusok

Az összekötők *eseményindítókat*, *műveleteket*vagy mindkettőt adhatnak meg. Az *eseményindító* a logikai alkalmazások első lépése, amely általában megadja az eseményindítót kiváltó eseményt, és elindítja a logikai alkalmazás futtatását. Például az FTP-összekötő olyan triggerrel rendelkezik, amely elindítja a logikai alkalmazást "fájl hozzáadásakor vagy módosításakor". Egyes eseményindítók rendszeresen megkeresik a megadott eseményt vagy az adattípust, majd a megadott esemény vagy az adatgyűjtés észlelése után tüzet fognak. Más eseményindítók várnak, de azonnal megtörténnek egy adott esemény bekövetkezésekor, vagy amikor új adatkapcsolat áll rendelkezésre. Az eseményindítók a logikai alkalmazáshoz is továbbítanak minden szükséges adattal. A logikai alkalmazás az összes munkafolyamatban képes olvasni és felhasználni ezeket az adatait. Az Office 365 Outlook Connector például egy triggerrel rendelkezik, "új e-mail érkezésekor", amely átadhatja az e-mailből származó tartalmat a logikai alkalmazás munkafolyamataiba.

Egy eseményindító elindítása után Azure Logic Apps létrehozza a logikai alkalmazás egy példányát, és elindítja a logikai alkalmazás munkafolyamatában lévő *műveleteket* . A műveletek a logikai alkalmazás munkafolyamataiban a triggert követő és a feladatokat elvégző lépések. Létrehozhat például egy olyan logikai alkalmazást, amely egy SQL-adatbázisból szerzi be a vásárlói adatait, és feldolgozza azokat a későbbi műveletekben.

A Azure Logic Apps az alábbi általános típusú eseményindítókat biztosítja:

* *Ismétlődési eseményindító*: ez az eseményindító megadott ütemezésen fut, és nincs szorosan társítva egy adott szolgáltatáshoz vagy rendszerhez.

* *Lekérdezési eseményindító*: ez az eseményindító rendszeresen lekérdezi egy adott szolgáltatást vagy rendszert a megadott ütemterv alapján, új adatellenőrzést végez, vagy adott esemény történt. Ha új adatok állnak rendelkezésre, vagy az adott esemény történt, az eseményindító létrehozza és futtatja a logikai alkalmazás egy új példányát, amely mostantól a bemenetként továbbított adatokat is használhatja.

* *Leküldéses eseményindító*: ez az eseményindító megvárja az új vagy az esemény megkötését. Ha új adatok állnak rendelkezésre, vagy ha az esemény történik, az eseményindító létrehozza és futtatja a logikai alkalmazás új példányát, amely mostantól a bemenetként továbbított adatokat is használhatja.

<a name="connections"></a>

## <a name="connector-configuration"></a>Összekötő konfigurációja

Minden összekötő eseményindítói és műveletei saját tulajdonságokat biztosítanak a konfigurálásához. Számos összekötő azt is megköveteli, hogy először létre kell hoznia egy *kapcsolódást* a cél szolgáltatáshoz vagy a rendszerhez, és meg kell adnia a hitelesítő adatokat vagy más konfigurációs adatokat, mielőtt a logikai alkalmazásban eseményindítót vagy műveletet alkalmazhat. Ha például az Office 365 Outlook e-mail fiókjának elérését és használatát szeretné elérni, akkor engedélyeznie kell a fiókhoz való kapcsolódást.

Azure Active Directory (Azure AD) OAuth használó összekötők esetén a kapcsolat létrehozása a szolgáltatásba való bejelentkezést jelenti, például az Office 365, a Salesforce vagy a GitHub szolgáltatást, ahol a hozzáférési token [titkosított](../security/fundamentals/encryption-overview.md) , és biztonságosan tárolható egy Azure titkos tárolóban. Más összekötők, például az FTP és az SQL esetében olyan kapcsolatra van szükség, amely konfigurációs adatokat tartalmaz, például a kiszolgáló címe, a Felhasználónév és a jelszó. Ezeket a kapcsolati konfigurációs adatokat is titkosítja és biztonságosan tárolja. További információ az [Azure-beli titkosításról](../security/fundamentals/encryption-overview.md).

A kapcsolatok a szolgáltatás vagy a rendszer engedélyezése esetén is hozzáférhetnek a cél szolgáltatáshoz vagy rendszerhez. Az Azure AD OAuth-kapcsolatokat használó szolgáltatások, például az Office 365 és a Dynamics esetében Azure Logic Apps határozatlan időre frissítik a hozzáférési jogkivonatokat. Előfordulhat, hogy az egyéb szolgáltatások korlátozva vannak, hogy Azure Logic Apps a tokent frissítés nélkül is használhatja. Bizonyos műveletek általában érvénytelenítik az összes hozzáférési jogkivonatot, például a jelszó módosítását.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Egyéni API-k és összekötők

Az egyéni kódot futtató vagy összekötőként nem elérhető API-k meghívásához [egyéni API apps létrehozásával](../logic-apps/logic-apps-create-api-app.md)kiterjesztheti a Logic apps platformot. [Létrehozhat egyéni összekötőket](../logic-apps/custom-connector-overview.md) is *bármilyen* Rest-vagy SOAP-alapú API-khoz, amelyek elérhetővé teszik az API-kat az Azure-előfizetésében található logikai alkalmazások számára. Ahhoz, hogy az egyéni API Apps vagy összekötők nyilvánosak legyenek az Azure-ban mindenki számára, a [Microsoft minősítéshez összekötőket küldhet](/connectors/custom-connectors/submit-certification).

> [!NOTE]
> Az [integrációs szolgáltatási környezetekben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) üzembe helyezett és futtatott logikai alkalmazások közvetlenül hozzáférhetnek egy Azure-beli virtuális hálózat erőforrásaihoz. Ha olyan egyéni összekötővel rendelkezik, amely a helyszíni adatátjárót igényli, és az összekötőket az ISE-n kívül hozta létre, akkor az ISE-ben lévő Logic apps is használhatja ezeket az összekötőket.
>
> Az ISE-n belül létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül hozzáférhetnek a helyszíni adatforrásokhoz, amelyek az ISE-t üzemeltető Azure-beli virtuális hálózathoz csatlakoznak. Ezért az ISE-ben a Logic apps valószínűleg nincs szüksége az adatátjáróra az ilyen erőforrásokkal való kommunikáció során.
>
> A ISEs létrehozásával kapcsolatos további információkért lásd: [Kapcsolódás Azure-beli virtuális hálózatokhoz a Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Kapcsolatok létrehozásának tiltása

Ha a szervezete nem engedélyezi a kapcsolódást bizonyos erőforrásokhoz a Azure Logic Apps összekötői segítségével, [letilthatja, hogy a](../logic-apps/block-connections-connectors.md) logikai alkalmazások munkafolyamataiban lévő összekötők a [Azure Policy](../governance/policy/overview.md)használatával képesek legyenek a kapcsolatok létrehozására. További információ: [adott összekötők által létrehozott kapcsolatok blokkolása a Azure Logic Appsban](../logic-apps/block-connections-connectors.md).

## <a name="get-ready-for-deployment"></a>Felkészülés az üzembe helyezésre

Bár a logikai alkalmazásokból hoz létre kapcsolatokat, a kapcsolatok külön Azure-erőforrások, saját erőforrás-definíciókkal rendelkeznek. A kapcsolódási erőforrás-definíciók áttekintéséhez tekintse meg [a logikai alkalmazáshoz tartozó erőforráscsoportot a Azure Portal használatával](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#view-resource-definitions) , vagy [töltse le az Azure-ból származó logikai alkalmazást a Visual studióba](../logic-apps/manage-logic-apps-with-visual-studio.md), amely a legegyszerűbben az üzembe helyezésre kész, érvényes paraméteres Logic app-sablon létrehozása.

## <a name="next-steps"></a>További lépések

* A [teljes összekötő listájának](/connectors) megtekintése
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Egyéni összekötők létrehozása logikai alkalmazásokhoz](/connectors/custom-connectors/)
* [Egyéni API-k létrehozása logikai alkalmazásokhoz](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Kapcsolódás helyszíni adatforrásokhoz a helyszíni adatátjáróval rendelkező logikai alkalmazásokból"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Azure API Management Service-példány létrehozása az API-k kezeléséhez és közzétételéhez"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Logikai alkalmazások integrálása App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Logikai alkalmazások integrálása Azure Functions-függvényekkel"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Üzenetek feldolgozása csoportokban vagy kötegként"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Értékelje ki a feltételt, és futtasson különböző műveleteket attól függően, hogy a feltétel igaz vagy hamis"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Ugyanazon műveletek végrehajtása egy tömb minden elemén"
[http-doc]: ./connectors-native-http.md "HTTP-vagy HTTPS-végpontok hívása a logikai alkalmazásokból"
[http-request-doc]: ./connectors-native-reqres.md "HTTP-kérelmek fogadása a logikai alkalmazásokban"
[http-response-doc]: ./connectors-native-reqres.md "Válaszadás a logikai alkalmazásokból érkező HTTP-kérelmekre"
[http-swagger-doc]: ./connectors-native-http-swagger.md "REST-végpontok hívása a logikai alkalmazásokból"
[http-webhook-doc]: ./connectors-native-webhook.md "Várakozás adott eseményekre HTTP-vagy HTTPS-végpontok esetén"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "JavaScript-kódrészletek hozzáadása és futtatása a logikai alkalmazásokból"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Logikai alkalmazások integrálása beágyazott munkafolyamatokkal"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Tömbök kiválasztása és szűrése a Lekérdezés művelettel"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Logikai alkalmazások futtatása ütemterv alapján"
[schedule-delay-doc]: ./connectors-native-delay.md "A következő művelet futásának késleltetése"
[schedule-delay-until-doc]: ./connectors-native-delay.md "A következő művelet futásának késleltetése"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Logikai alkalmazások futtatása ismétlődő ütemterv szerint"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Olyan logikai alkalmazások futtatása, amelyeknek a folytonos adattömbökben kell kezelnie az adatkezelést"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Csoportokba rendezheti a műveleteket, amelyek a csoport műveleteinek befejezése után kapják meg a saját állapotukat"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Olyan esetekre szervezheti a műveleteket, amelyek egyedi értékeket rendelnek hozzájuk. Csak azt az esetet futtassa, amelynek értéke megegyezik egy kifejezés, objektum vagy token eredményével. Ha nem létezik egyezés, futtassa az alapértelmezett esetet."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Aktívan futó munkafolyamatok leállítása vagy megszakítása a logikai alkalmazáshoz"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ismételje meg a műveleteket, amíg a megadott feltétel igaz vagy valamilyen állapot megváltozott."
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Adatműveletek végrehajtása, például tömbök szűrése vagy CSV-és HTML-táblázatok létrehozása"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Műveletek végrehajtása változókkal, például inicializálás, beállítás, növekmény, csökkentés és Hozzáfűzés karakterlánc-vagy Array-változóhoz"

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Automatizálási feladatok létrehozása és kezelése a felhő és a helyszíni infrastruktúra számára"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "A blobtárolókban található fájlok kezelése az Azure Blob Storage-összekötővel"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Kapcsolódás a Azure Cosmos DBhoz a dokumentumok és tárolt eljárások eléréséhez"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Egy Event Grid által közzétett események figyelése, például amikor az Azure-erőforrások vagy a harmadik féltől származó erőforrások változnak"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Kapcsolódjon az Azure Event Hubshoz, hogy fogadja és küldje el az eseményeket a Logic apps és a Event Hubs között"
[azure-file-storage-doc]: /connectors/azurefile/ "Kapcsolódjon az Azure Storage-fiókjához, hogy fájlokat lehessen létrehozni, frissíteni, beolvasni és törölni"
[azure-key-vault-doc]: /connectors/keyvault/ "Kapcsolódjon a Azure Key Vaulthoz, hogy kezelni tudja a titkait és kulcsait"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Lekérdezések futtatása Azure Monitor naplókon Log Analytics-munkaterületeken és Application Insights-összetevőkön"
[azure-queues-doc]: /connectors/azurequeues/ "Kapcsolódjon az Azure Storage-fiókjához, hogy várólistákat és üzeneteket lehessen létrehozni és kezelni"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Üzeneteket küldhet a Service Bus-üzenetsorokból és -témakörökből, valamint fogadhatja a Service Bus-üzenetsorok és -előfizetések üzeneteit."
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "A Azure SQL Data Warehouse csatlakozhat, így megtekintheti az adatait"
[azure-table-storage-doc]: /connectors/azuretables/ "Kapcsolódjon az Azure Storage-fiókjához, így táblákat hozhat létre, frissíthet és lekérdezéseket végezhet"
[biztalk-server-doc]: /connectors/biztalk/ "Kapcsolódjon a BizTalk Serverhoz, hogy a BizTalk-alapú alkalmazások párhuzamosan futtathatók legyenek Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Csatlakozhat egy helyszíni fájlrendszerhez"
[ftp-doc]: ./connectors-create-api-ftp.md "Csatlakozhat egy FTP-/FTPS-kiszolgálóhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlokat törölhet, tölthet fel vagy kérhet le"
[github-doc]: ./connectors-create-api-github.md "Csatlakozhat a GitHubhoz, és különböző problémákat követhet nyomon"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Kapcsolódik a Google naptárhoz, és kezelheti a naptárat"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Csatlakozhat a Google táblázatokhoz, így módosíthatja a lapokat"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "A Google-feladatokhoz kapcsolódik, hogy kezelni tudja a feladatokat"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Kapcsolódás 3270-alkalmazásokhoz IBM-es nagyszámítógépeken"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Kapcsolódjon az IBM DB2-hez a felhőben vagy a helyszínen. Sor frissítése, tábla beszerzése és egyebek"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Kapcsolódjon az Informix-hez a felhőben vagy a helyszínen. Egy sor beolvasása, a táblák listázása és egyebek"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Kapcsolódás az IBM MQ-hoz a helyszínen vagy az Azure-ban üzenetek küldéséhez és fogadásához"
[instagram-doc]: ./connectors-create-api-instagram.md "Kapcsolódjon a Instagram. Események elindítása vagy művelet"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Csatlakozhat a Mandrillhoz, ha azon keresztül szeretne kommunikálni"
[mysql-doc]: /connectors/mysql/ "Kapcsolódjon a helyszíni MySQL-adatbázishoz, hogy el tudja olvasni és írni az adatait"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Kapcsolódjon az Office 365-fiókjához, így e-maileket küldhet és fogadhat, kezelheti naptárát és névjegyeit, és így tovább"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Csatlakozhat a személyes Microsoft-OneDrive, így feltöltheti, törölheti és listázhatja a fájlokat"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Kapcsolódjon vállalata Microsoft-OneDrive, így feltöltheti, törölheti és listázhatja a fájlokat, és így tovább"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Csatlakozhat egy Oracle-adatbázishoz, így hozzáadhat, beszúrhat, törölhet sorokat és egyéb műveleteket is"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Kapcsolódjon az Outlook-postaládához, így kezelheti az e-maileket, a naptárakat, a névjegyeket és egyebeket"
[postgre-sql-doc]: /connectors/postgresql/ "Kapcsolódjon a PostgreSQL-adatbázishoz, hogy beolvassa az adatait a táblákból"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Kapcsolódjon a Salesforce-fiókjához. Fiókok, érdeklődők, lehetőségek és egyebek kezelése"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Csatlakozhat egy helyszíni SAP-rendszerhez"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Kapcsolódjon a SendGrid. E-mail küldése és a címzettek jegyzékeinek kezelése"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Csatlakozzon az SFTP-fiókjához az SSH használatával. Fájlok feltöltése, beolvasása, törlése és egyebek"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Kapcsolódjon a helyszíni SharePoint-kiszolgálóhoz. Dokumentumok, listaelemek és egyéb elemek kezelése"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Kapcsolódjon a SharePoint Online-hoz. Dokumentumok, listaelemek és egyéb elemek kezelése"
[slack-doc]: ./connectors-create-api-slack.md "Kapcsolódás a Slackhez és üzenetek közzététele a Slack-csatornákon"
[smtp-doc]: ./connectors-create-api-smtp.md "Kapcsolódás SMTP-kiszolgálóhoz és mellékletek küldése e-mailben"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Csatlakozhat a SparkPosthoz, ha azon keresztül szeretne kommunikálni"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Kapcsolódjon Azure SQL Databasehoz vagy SQL Serverhoz. SQL Database-táblázat bejegyzéseinek létrehozása, frissítése, beolvasása és törlése"
[teradata-doc]: /connectors/teradata/ "Kapcsolódás a Teradata-adatbázishoz a táblák adatainak beolvasásához"
[twilio-doc]: ./connectors-create-api-twilio.md "Kapcsolódjon a Twilio. Üzenetek küldése és lekérése, rendelkezésre álló számok beolvasása, a bejövő telefonszámok kezelése és egyebek"
[youtube-doc]: ./connectors-create-api-youtube.md "Kapcsolódjon a YouTube-hoz. Videók és csatornák kezelése"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Az AS2 protokollt használó üzenetek kódolása és dekódolása"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Az EDIFACT protokollt használó üzenetek kódolása és dekódolása"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Az EDIFACT protokollt használó üzenetek dekódolása"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIFACT protokollt használó üzenetek kódolása"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Tudnivalók a vállalati integrációs egyszerű fájlról"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Tudnivalók a vállalati integrációs egyszerű fájlról"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Az integrációs fiók összetevőihez tartozó metaadatok kezelése"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "A JSON átalakítása likvid sablonokkal"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Az X12 protokollt használó üzenetek kódolása és dekódolása"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Az X12 protokollt használó üzenetek dekódolása"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 protokollt használó üzenetek kódolása"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML-üzenetek átalakítása"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML-üzenetek ellenőrzése"
