---
title: Az Azure Logic Apps összekötői
description: Az Azure Logic Apps összekötőivel automatizálhatja a munkafolyamatokat, például a beépített, felügyelt, helyszíni, integrációs fiókot, ISE- és vállalati összekötőket
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247331"
---
# <a name="connectors-for-azure-logic-apps"></a>Az Azure Logic Apps összekötői

Az összekötők gyors hozzáférést biztosítanak az Azure Logic Appsből más alkalmazások, szolgáltatások, rendszerek, protokollok és platformok eseményeihez, adataihoz és műveleteihez. A logikai alkalmazások összekötőivel kibővítheti a felhőbeli és a helyszíni alkalmazások képességeit annak érdekében, hogy feladatokat hajthassanak végre a létrehozott és már meglévő adatokkal.

Míg a Logic Apps [több száz összekötőt](https://docs.microsoft.com/connectors)kínál, ez a cikk ismerteti a *népszerű és gyakrabban használt* összekötők, amelyek et sikeresen használják több ezer alkalmazás és több millió végrehajtások adatok és információk feldolgozásához. Az összekötők teljes listájának és az egyes összekötők referenciainformációinak, például az eseményindítóknak, a műveleteknek és a korlátoknak a megkereséséhez tekintse át az összekötők referencialapjait az [Összekötők áttekintése területen.](https://docs.microsoft.com/connectors) További információ az [eseményindítókról és műveletekről](#triggers-actions), [a Logic Apps díjszabási modelljéről](../logic-apps/logic-apps-pricing.md)és a Logic Apps [díjszabási részleteiről.](https://azure.microsoft.com/pricing/details/logic-apps/)

> [!TIP]
> Ha olyan szolgáltatással vagy API-val szeretne integrálni, amely nem rendelkezik összekötővel, közvetlenül hívhatja a szolgáltatást egy protokollon, például HTTP-n keresztül, vagy létrehozhat egy [egyéni összekötőt.](#custom)

## <a name="connector-types"></a>Összekötőtípusok

Az összekötők beépített eseményindítókként és műveletekként, illetve felügyelt összekötőkként érhetők el.

<a name="built-in"></a>

* [**Beépített:**](#built-ins)A beépített eseményindítók és műveletek "natívak" az Azure Logic Apps számára, és segítenek a logikai alkalmazások feladatainak végrehajtásában:

  * Egyéni és speciális ütemezésszerint futva.

  * Rendszerezheti és szabályozhatja a logikai alkalmazás munkafolyamatait, például a hurkokat és a feltételeket, valamint a változókkal és az adatműveletekkel való munkát.

  * Kommunikáció más végpontokkal.

  * Kérések fogadása és megválaszolása.

  * Hívja az Azure-függvényeket, az Azure API-alkalmazásokat (Web Apps), a saját API-kat, amelyeket az Azure API Management kezel és tesz közzé, valamint beágyazott logikai alkalmazásokat, amelyek kéréseket fogadhatnak.

<a name="managed-connectors"></a>

* [**Felügyelt összekötők:**](#managed-api-connectors)A Microsoft által üzembe helyezett és felügyelt összekötők eseményindítókat és műveleteket biztosítanak a felhőszolgáltatások, a helyszíni rendszerek vagy mindkettő eléréséhez, beleértve az Office 365-öt, az Azure Blob Storage-t, az SQL Servert, a Dynamics-t, a Salesforce-ot, a SharePointot és egyebeket. Egyes összekötők kifejezetten támogatja a vállalkozások közötti (B2B) kommunikációs forgatókönyvek, és szükség van egy [integrációs fiók,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) amely kapcsolódik a logikai alkalmazáshoz. Bizonyos összekötők használata előtt előfordulhat, hogy először létre kell hoznia kapcsolatokat, amelyek et az Azure Logic Apps által kezelt.

  Ha például Microsoft BizTalk kiszolgálót használ, a logikai alkalmazások a [biztalk kiszolgálóhoz a helyszíni BizTalk kiszolgáló összekötővel kapcsolódhatnak](#on-premises-connectors)és kommunikálhatnak vele. Ezután az [integrációs fiók összekötőivel](#integration-account-connectors)biztalk-szerű műveleteket hajthat végre vagy hajthat végre a logikai alkalmazásokban.

  Az összekötők standard vagy vállalati besorolásúak. [A vállalati csatlakozók](#enterprise-connectors) további költségek mellett biztosítanak hozzáférést az olyan vállalati rendszerekhez, mint az SAP, az IBM MQ és az IBM 3270. Annak megállapításához, hogy egy összekötő Standard vagy Enterprise, tekintse meg a műszaki részleteket az egyes összekötők referencialapján az [Összekötők áttekintése](https://docs.microsoft.com/connectors)területen.

  Az összekötők ezek a kategóriák használatával is azonosíthatók, bár egyes összekötők több kategóriát is keresztezhetnek. Az SAP például egy vállalati összekötő és egy helyszíni összekötő:

  |   |   |
  |---|---|
  | [**Felügyelt összekötők**](#managed-api-connectors) | Olyan logikai alkalmazásokat hozhat létre, amelyek olyan szolgáltatásokat használnak, mint az Azure Blob Storage, az Office 365, a Dynamics, a Power BI, a OneDrive, a Salesforce, a SharePoint Online és még sok más. |
  | [**Helyszíni összekötők**](#on-premises-connectors) | A [helyszíni adatátjáró][gateway-doc]telepítése és beállítása után ezek az összekötők segítik a logikai alkalmazásokat a helyszíni rendszerek, például az SQL Server, a SharePoint Server, az Oracle DB, a fájlmegosztások és mások elérésében. |
  | [**Integrációs fiókok összekötői**](#integration-account-connectors) | Ezek az összekötők az integrációs fiók létrehozásakor és kifizetésénél érhetők el, átalakítják és érvényesítik az XML-t, kódolják és dekódolják a sík fájlokat, és feldolgozzák a vállalkozások közötti (B2B) üzeneteket AS2, EDIFACT és X12 protokollokkal. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Csatlakozás integrációs szolgáltatási környezetből

Az Azure virtuális hálózat erőforrásaihoz közvetlen hozzáférést igénylő logikai alkalmazások hozhatja létre az elkülönített [integrációs szolgáltatási környezetet (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ahol dedikált erőforrásokon hozhat létre, helyezhet üzembe és futtathat logikai alkalmazásokat. A Logic App Designerben, amikor az ISE-ben a logikai alkalmazásokhoz használni kívánt összekötők között tallózik, a **CORE** címke megjelenik a beépített eseményindítókon és műveleteken, míg az **ISE-címke** egyes összekötőkön jelenik meg:

* **CORE**: A beépített eseményindítók és műveletek ezzel a címkével ugyanabban az ISE-ben futnak, mint a logikai alkalmazások, például:

  ![Példa ISE-csatlakozóra](./media/apis-list/example-core-connector.png)

* **ISE**: A felügyelt összekötők ezzel a címkével ugyanabban az ISE-ben futnak, mint a logikai alkalmazások, például:

  ![Példa ISE-csatlakozóra](./media/apis-list/example-ise-connector.png)

  Ha egy helyszíni rendszer, amely csatlakozik egy Azure virtuális hálózathoz, az ISE lehetővé teszi a logikai alkalmazások közvetlenül hozzáférhetnek a rendszer nélkül a [helyszíni adatátjáró.](../logic-apps/logic-apps-gateway-connection.md) Ehelyett használhatja a rendszer **ISE-összekötőjét,** ha elérhető, http-műveletet vagy [egyéni összekötőt.](#custom) Az **ISE-összekötőkkel** nem rendelkező helyszíni rendszerek esetén használja a helyszíni adatátjárót. Az elérhető ISE-összekötők megtekintéséhez lásd: [ISE-összekötők](#ise-connectors).

* A **CORE** vagy **ISE** címke nélküli összes többi összekötő, amelyet továbbra is használhat, futtassa a globális, több-bérlős Logic Apps szolgáltatásban, például:

  ![Példa több-bérlős összekötőre](./media/apis-list/example-multi-tenant-connector.png)

Az ISE-ben és azok összekötőiben futó logikai alkalmazások, függetlenül attól, hogy hol futnak ezek az összekötők, rögzített díjszabási tervet követnek a fogyasztásalapú díjcsomaggal szemben. További információt az alábbi oldalakon talál:

* [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md)
* [A Logic Apps díjszabási részletei](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Csatlakozás az Azure-alapú virtuális hálózatokhoz az Azure Logic Apps alkalmazásból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Beépített

A Logic Apps beépített eseményindítókat és műveleteket biztosít, hogy ütemezésalapú munkafolyamatokat hozhassanak létre, segítsenek a logikai alkalmazásainak más alkalmazásokkal és szolgáltatásokkal kommunikálni, a munkafolyamatot a logikai alkalmazásokon keresztül szabályozhatja, és kezelheti vagy kezelheti az adatokat.

|   |   |   |   |
|---|---|---|---|
| [![API-ikon][schedule-icon]<br>**ütemezése**][schedule-doc] | - Futtasson egy logikai alkalmazást egy megadott ismétlődésen, az alaptól a speciális ütemezésig az [ **Ismétlődés** eseményindítóval.][schedule-recurrence-doc] <p>- Futtasson egy logikai alkalmazást, amelynek folyamatosan kell kezelnie az adatokat a [ **Csúszó ablak** eseményindítóval.][schedule-sliding-window-doc] <p>- Szünet elad a logikai alkalmazás egy meghatározott időtartamra a [ **Delay** művelet][schedule-delay-doc]. <p>- Szünet elapadasz a logikai alkalmazást, amíg a megadott dátum és idő a [ **Delay until** action][schedule-delay-until-doc]. | [![API][batch-icon]<br>ikon**Batch**][batch-doc] | - Az üzenetek feldolgozása kötegekben a **Batch üzenetek** eseményindítóval. <p>- Hívja meg a meglévő kötegelt eseményindítókkal rendelkező logikai alkalmazásokat az **Üzenetek küldése kötegelt művelettel.** |
| [![API][http-icon]<br>ikon**HTTP**][http-doc] | Http- vagy HTTPS-végpontok hívása a HTTP-höz kapcsolódó eseményindítókkal és műveletekkel. Egyéb HTTP beépített eseményindítók és műveletek közé tartozik [a HTTP + Swagger][http-swagger-doc] és [a HTTP + Webhook][http-webhook-doc]. | [![API][http-request-icon]<br>**ikonkérés**][http-request-doc] | - A logikai alkalmazás hívható más alkalmazásokból vagy szolgáltatásokból, eseményindító az Event Grid erőforrás-események, vagy eseményindító az Azure Security Center riasztások a **kérelem** eseményindító. <p>- Válaszok küldése egy alkalmazásnak vagy szolgáltatásnak a **Válasz** művelettel. |
| [![API-ikon][azure-api-management-icon]<br>**Az Azure API <br>Management**][azure-api-management-doc] | Az Azure API Management tel által kezelt és közzétett saját API-k által meghatározott hívási eseményindítók és műveletek. | [![API-ikon][azure-app-services-icon]<br>**Az Azure App <br>Services**][azure-app-services-doc] | Hívja meg az Azure App Service-ben üzemeltetett Azure API-alkalmazásokat vagy webalkalmazásokat. Az ilyen alkalmazások által meghatározott eseményindítók és műveletek úgy jelennek meg, mint bármely más első osztályú eseményindítók és műveletek, ha swagger szerepel.|
| [![API-ikon][azure-logic-apps-icon]<br>**Az Azure Logic <br>Apps**][nested-logic-app-doc] | Hívja meg a többi logikai alkalmazásokat, amelyek a **kérelem** eseményindítóval kezdődnek. |
|||||

### <a name="run-code-from-logic-apps"></a>Kód futtatása logikai alkalmazásokból

A Logic Apps beépített műveleteket biztosít a saját kód futtatásához a logikai alkalmazás munkafolyamatában:

|   |   |   |   |
|---|---|---|---|
| [![API-ikon][azure-functions-icon]<br>**Az Azure Functions**][azure-functions-doc] | Hívja meg az Azure-függvények, amelyek egyéni kódrészleteket (C# vagy Node.js) futtatnak a logikai alkalmazásokból. | [![API-ikon][inline-code-icon]<br>**inline kód**][azure-functions-doc] | JavaScript-kódrészletek hozzáadása és futtatása a logikai alkalmazásokból. |
|||||

### <a name="control-workflow"></a>Munkafolyamat vezérlése

A Logic Apps beépített műveleteket biztosít a logikai alkalmazás munkafolyamatában lévő műveletek strukturálásához és szabályozásához:

|   |   |   |   |
|---|---|---|---|
| [![Beépített][condition-icon]<br>**ikonfeltétel**][condition-doc] | Kiértékelegy feltételt, és különböző műveleteket futtat annak alapján, hogy a feltétel igaz vagy hamis. | [![Beépített ikon][for-each-icon]<br>**minden**][for-each-doc] | A tömb minden elemén hajtsa végre ugyanazokat a műveleteket. |
| [![Beépített][scope-icon]<br>**ikonhatókör**][scope-doc] | A műveleteket *hatókörökbe csoportosítja,* amelyek saját állapotukat kapják a hatókörben lévő műveletek futtatása után. | [![Beépített][switch-icon]<br>**ikonkapcsoló**][switch-doc] | A műveleteket *olyan esetekbe*csoportosítja, amelyek az alapértelmezett eset kivételével egyedi értékekhez vannak rendelve. Csak azt az esetet futtassa, amelynek hozzárendelt értéke megegyezik egy kifejezés, objektum vagy jogkivonat eredményével. Ha nincs egyezés, futtassa az alapértelmezett esetet. |
| [![A beépített][terminate-icon]<br>ikon**leállítása**][terminate-doc] | Állítsa le az aktívan futó logikai alkalmazás munkafolyamatát. | [![Beépített ikon][until-icon]<br>**amíg**][until-doc] | Ismételje meg a műveleteket addig, amíg a megadott feltétel nem teljesül, vagy valamilyen állapot nem változik. |
|||||

### <a name="manage-or-manipulate-data"></a>Adatok kezelése és kezelése

A Logic Apps beépített műveleteket biztosít az adatkimenetek és azok formátumának munkálataihoz:

|   |   |
|---|---|
| [![Beépített][data-operations-icon]<br>**ikonadat-műveletek**][data-operations-doc] | Műveletek végrehajtása adatokkal: <p>- **Összeállítás:** Hozzon létre egyetlen kimenetet több különböző típusú bemenetből. <br>- **CSV-tábla létrehozása:** Vesszővel tagolt (CSV) tábla létrehozása JSON-objektumokkal rendelkező tömbből. <br>- **HTML-táblázat létrehozása**: HTML-táblázat létrehozása JSON-objektumokkal rendelkező tömbből. <br>- **Tömb szűrése**: Hozzon létre tömböt egy másik tömb olyan elemeiből, amelyek megfelelnek a feltételeknek. <br>- **Csatlakozás**: Karakterlánc létrehozása egy tömb összes eleméből, és elkülönítve azokat a megadott határolójellel. <br>- **ElemzésI JSON:** Felhasználóbarát jogkivonatokat hozhat létre a tulajdonságokból és azok json-tartalomban lévő értékeiből, hogy ezeket a tulajdonságokat használhassa a munkafolyamatban. <br>- **Válassza ki**a : Hozzon létre egy tömböt JSON-objektumokkal egy másik tömb elemeinek vagy értékeinek átalakításával, és az elemek meghatározott tulajdonságokhoz való hozzárendelésével. |
| ![Beépített ikon][date-time-icon]<br>**Dátum időpontja** | Műveletek végrehajtása időbélyegekkel: <p>- **Hozzáadás az időhöz:** Adja hozzá a megadott számú egységet az időbélyeghez. <br>- **Időzóna konvertálása**: Időbélyeg konvertálása a forrás időzónából a célidőzónába. <br>- **Aktuális idő**: Az aktuális időbélyeg et adja vissza karakterláncként. <br>- **Jövőbeli idő betöltése**: Az aktuális időbélyeg és a megadott időegységek visszaadása. <br>- **Túltöltött idő betöltése**: Az aktuális időbélyeget adja vissza a megadott időmértékekkel csökkentve. <br>- **Kivonás az időből:** Több időegység kivonása időbélyegből. |
| [![Beépített][variables-icon]<br>**ikonváltozók**][variables-doc] | Műveletek végrehajtása változókkal: <p>- **Hozzáfűzés tömbváltozóhoz**: Érték beszúrása egy változó által tárolt tömb utolsó elemeként. <br>- **Hozzáfűzés karakterlánc-változóhoz**: Érték beszúrása egy változó által tárolt karakterlánc utolsó karaktereként. <br>- **Csökkenés változó**: Változó csökkentése állandó értékkel. <br>- **Növekményváltozó**: Egy változó növelése állandó értékkel. <br>- **Inicializálási változó**: Hozzon létre egy változót, és deklarálja az adattípusát és a kezdeti értéket. <br>- **Változó beállítása**: Másik értéket rendelhet egy meglévő változóhoz. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Felügyelt összekötők

A Logic Apps ezeket a népszerű Standard összekötőket biztosítja a feladatok, folyamatok és munkafolyamatok automatizálásához ezekkel a szolgáltatásokkal vagy rendszerekkel:

|   |   |   |   |
|---|---|---|---|
| [![API-ikon][azure-service-bus-icon]<br>**az Azure Service Bus-ban**][azure-service-bus-doc] | A Logic Apps leggyakrabban használt összekötőjével aszinkron üzeneteket, munkameneteket és témakör-előfizetéseket kezelhet. | [![API-ikon][sql-server-icon]<br>**SQL Server**][sql-server-doc] | Csatlakozzon a helyszíni SQL Server kiszolgálóhoz vagy egy Azure SQL-adatbázishoz a felhőben, így kezelheti a rekordokat, futtathatja a tárolt eljárásokat, vagy lekérdezéseket hajthat végre. |
| [![API-ikon][azure-blob-storage-icon]<br>**az Azure Blob<br>Storage-ban**][azure-blob-storage-doc] | Csatlakozzon a tárfiókhoz, hogy blobtartalmat hozhasson létre és kezelhessen. | [![AZ][office-365-outlook-icon]<br>API ikonja**Az Office<br>365 Outlook**][office-365-outlook-doc] | Csatlakozzon Office 365-ös e-mail fiókjához, így e-maileket, feladatokat, naptáreseményeket és értekezleteket, névjegyeket, kéréseket és egyebeket hozhat létre és kezelhet. |
| [![API][sftp-ssh-icon]<br>ikon**SFTP-SSH**][sftp-ssh-doc] | Csatlakozzon az Internetről az SSH segítségével elérhető SFTP-kiszolgálókhoz, hogy a fájlokkal és mappákkal dolgozhassunk. | [![API-ikon][sharepoint-online-icon]<br>**SharePoint<br>Online**][sharepoint-online-doc] | Csatlakozzon a SharePoint Online-hoz, hogy kezelni tudja a fájlokat, mellékleteket, mappákat és egyebeket. | 
| [![API-ikon][dynamics-365-icon]<br>**Dynamics<br> 365**][dynamics-365-doc] | Csatlakozzon Dynamics 365-fiókjához, így rekordokat, elemeket és egyebeket hozhat létre és kezelhet. | [![API-ikon][azure-queues-icon]<br>**Azure-várólisták <br>**][azure-queues-doc] | Csatlakozzon Azure Storage-fiókjához, hogy várólistákat és üzeneteket hozhasson létre és kezelhessen |
| [![][ftp-icon]<br>**API-ikon FTP**][ftp-doc] | Csatlakozzon az internetről elérhető FTP-kiszolgálókhoz, hogy a fájlokkal és mappákkal dolgozhassunk. | [![API][file-system-icon]<br>ikon**fájlrendszer <br>**][file-system-doc] | Csatlakozzon a helyszíni fájlmegosztáshoz, így fájlokat hozhat létre és kezelhet. |
| [![API-ikon][azure-event-hubs-icon]<br>**Az Azure Event Hubs**][azure-event-hubs-doc] | Események felhasználása és közzététele egy eseményközponton keresztül. Például a kimenet et a logikai alkalmazás ból Event Hubs, majd küldje el a kimenetet egy valós idejű elemzési szolgáltató. | [![API-ikon][azure-event-grid-icon]<br>**Az Azure Event**<br>**Grid**][azure-event-grid-doc] | Figyelje az Event Grid által közzétett eseményeket, például amikor az Azure-erőforrások vagy a külső erőforrások megváltoznak. |
| [![API][salesforce-icon]<br>ikon**Salesforce**][salesforce-doc] | Csatlakozzon a Salesforce-fiókhoz, így létrehozhat és kezelhet olyan elemeket, mint például bejegyzések, feladatok, objektumok stb. | [![API][twitter-icon]<br>ikon**Twitter**][twitter-doc] | Csatlakozzon Twitter-fiókjához, hogy kezelni tudja a tweeteket, a követőket, az idővonalát és egyebeit. A tweeteket sql-be, Excelbe vagy SharePoint-fájlba mentheti. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Helyszíni összekötők

Íme néhány általánosan használt standard összekötők, amelyek logic apps biztosít az adatok és erőforrások helyszíni rendszerek ben való hozzáféréshez. Mielőtt kapcsolatot hozna létre egy helyszíni rendszerrel, először le kell [töltenie, telepítenie kell és be kell állítania egy helyszíni adatátjárót.][gateway-doc] Ez az átjáró biztonságos kommunikációs csatornát biztosít anélkül, hogy létre kellene hoznia a szükséges hálózati infrastruktúrát.

|   |   |   |   |   |
|---|---|---|---|---|
| [![API][biztalk-server-icon]<br>ikon**BizTalk** <br> **Server**][biztalk-server-doc] | [![API][file-system-icon]<br>ikon**fájlrendszer <br>**][file-system-doc] | [![AZ][ibm-db2-icon]<br>**IBM DB2** API ikonja][ibm-db2-doc] | [![AZ][ibm-informix-icon]<br>**IBM** <br> **Informix** API ikon][ibm-informix-doc] | [![Az][mysql-icon]<br>API**ikonja, a MySQL**][mysql-doc] |
| [![AZ][oracle-db-icon]<br>API ikonja**Oracle DB**][oracle-db-doc] | [![API][postgre-sql-icon]<br>ikon**PostgreSQL**][postgre-sql-doc] | [![API-ikon][sharepoint-server-icon]<br>**SharePoint <br>Server**][sharepoint-server-doc] | [![API-ikon][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc] | [![API-ikon][teradata-icon]<br>**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Integrációs fiókok összekötői

A Logic Apps standard összekötőket biztosít a vállalatok közötti (B2B) megoldások létrehozásához a logikai alkalmazásokkal, amikor létrehoz és fizet egy [integrációs fiókért,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)amely az Azure-ban elérhető enterprise integration pack (EIP) csomagon keresztül érhető el. Ezzel a fiókkal b2B-összetevőket hozhat létre és tárolhat, például kereskedelmi partnereket, megállapodásokat, térképeket, sémákat, tanúsítványokat és így tovább. Ezek az összetevők használatához társítsa a logikai alkalmazásokat az integrációs fiókkal. Ha jelenleg biztalk kiszolgálót használ, ezek az összekötők már ismerősnek tűnhetnek.

|   |   |   |   |
|---|---|---|---|
| [![AZ][as2-icon]<br>API ikon**AS2 <br>dekódolása**][as2-doc] | [![API][as2-icon]<br>ikon**AS2 <br>kódolás**][as2-doc] | [![AZ][edifact-icon]<br>API ikon**EDIFACT <br>dekódolása**][edifact-decode-doc] | [![API][edifact-icon]<br>ikon**EDIFACT <br>kódolás**][edifact-encode-doc] |
| [![API][flat-file-decode-icon]<br>ikon**Lapos fájl <br>dekódolása**][flat-file-decode-doc] | [![API][flat-file-encode-icon]<br>ikon**Lapos fájlkódolás <br>**][flat-file-encode-doc] | [![API][integration-account-icon]<br>ikon**integrációs <br>fiók**][integration-account-doc] | [![AZ][liquid-icon]<br>API icon**Liquid** <br> **átalakítja**][json-liquid-transform-doc] |
| [![Api][x12-icon]<br>ikon**X12 <br>dekódolás**][x12-decode-doc] | [![API][x12-icon]<br>ikon**X12 <br>kódolás**][x12-encode-doc] | [![API-ikon][xml-transform-icon]<br> **XML-átalakítások** **XML** <br>][xml-transform-doc] | [![API-ikon][xml-validate-icon]<br>**XML-érvényesítése <br>**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Vállalati összekötők

A Logic Apps ezeket a vállalati összekötőket biztosítja a vállalati rendszerek, például az SAP és az IBM MQ eléréséhez:

|   |   |   |
|---|---|---|
| [![AZ][ibm-3270-icon]<br>**IBM 3270** API ikon][ibm-3270-doc] | [![API][ibm-mq-icon]<br>ikon**IBM MQ**][ibm-mq-doc] | [![API-ikon][sap-icon]<br>**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE csatlakozók

Az elkülönített [integrációs szolgáltatási környezetet (ISE)](#integration-service-environment)létrehozó és futtatott logikai alkalmazások esetében a Logic App Designer azonosítja a beépített eseményindítókat és az ISE-ben a **CORE** címkével futó műveleteket. Az ISE-ben futó felügyelt összekötők az **ISE-címkét** jelenítik meg, míg a globális, több-bérlős Logic Apps szolgáltatásban futó összekötők egyik címkét sem jelenítik meg. Ez a lista az ISE-verziókkal jelenleg csatlakozókat tartalmazza:

|   |   |   |   |   |
|---|---|---|---|---|
[![AZ][as2-icon]<br>API ikonja**AS2**][as2-doc] | [![API-ikon][azure-blob-storage-icon]<br>**az Azure Blob<br>Storage-ban**][azure-blob-storage-doc] | [![Az][azure-cosmos-db-icon]<br>**Azure Cosmos <br> DB API-ikon**][azure-cosmos-db-doc] | [![API-ikon][azure-event-hubs-icon]<br>**Az Azure <br>Event Hubs**][azure-event-hubs-doc] | [![API-ikon][azure-file-storage-icon]<br>**Azure File<br>Storage**][azure-file-storage-doc] |
| [![API-ikon][azure-service-bus-icon]<br>**az Azure Service <br>Bus-ban**][azure-service-bus-doc] | [![API-ikon][azure-sql-data-warehouse-icon]<br>**Az Azure SQL <br>Data Warehouse**][azure-sql-data-warehouse-doc] | [![API-ikon][azure-table-storage-icon]<br>**Az Azure Table <br>Storage**][azure-table-storage-doc] | [![API-ikon][azure-queues-icon]<br>**Azure-várólisták <br>**][azure-queues-doc] | [![AZ][edifact-icon]<br>API ikonJA**EDIFACT**][edifact-doc] |
| [![API][file-system-icon]<br>ikon**fájlrendszer <br>**][file-system-doc] | [![][ftp-icon]<br>**API-ikon FTP**][ftp-doc] | [![AZ][ibm-3270-icon]<br>**IBM 3270** API ikon][ibm-3270-doc] | [![AZ][ibm-db2-icon]<br>**IBM DB2** API ikonja][ibm-db2-doc] | [![API][ibm-mq-icon]<br>ikon**IBM MQ**][ibm-mq-doc] |
| [![API-ikon][sap-icon]<br>**SAP**][sap-connector-doc] | [![API][sftp-ssh-icon]<br>ikon**SFTP-SSH**][sftp-ssh-doc] | [![API-ikon][smtp-icon]<br>**SMTP**][smtp-doc] | [![API-ikon][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc] | [![Az][x12-icon]<br>API ikonja**X12**][x12-doc] |
||||||

További információt az alábbi témakörökben talál:

* [Hozzáférés az Azure virtuális hálózati erőforrásaihoz az Azure Logic Apps alkalmazásból](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md)
* [Csatlakozás az Azure-alapú virtuális hálózatokhoz az Azure Logic Apps alkalmazásból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Eseményindítók és művelettípusok

Az összekötők *eseményindítókat,* *műveleteket*vagy mindkettőt biztosíthatnak. Az *eseményindító* az első lépés minden logikai alkalmazásban, amely általában megadja az esemény, amely elindítja az eseményindítót, és elindítja a logikai alkalmazás futtatását. Például az FTP-összekötő rendelkezik egy eseményindítóval, amely elindítja a logikai alkalmazást "fájl hozzáadásakor vagy módosításakor". Egyes eseményindítók rendszeresen ellenőrzik a megadott eseményt vagy adatokat, majd aktiválódnak, amikor észlelik a megadott eseményt vagy adatokat. Más eseményindítók várakoznak, de azonnal aktiválódnak, ha egy adott esemény bekövetkezik, vagy ha új adatok állnak rendelkezésre. Az eseményindítók a szükséges adatokat is továbbítják a logikai alkalmazásnak. A logikai alkalmazás képes olvasni és használni ezeket az adatokat a munkafolyamat során. Például a Twitter-összekötő rendelkezik egy eseményindítóval, "Új tweet közzétételekor", amely átadja a tweet tartalmát a logikai alkalmazás munkafolyamatába.

Egy eseményindító tüzek után az Azure Logic Apps létrehoz egy példányt a logikai alkalmazás, és elindítja a *műveletek et* a logikai alkalmazás munkafolyamatában. A műveletek azok a lépések, amelyek követik az eseményindítót, és feladatokat hajtanak végre a logikai alkalmazás munkafolyamatában. Létrehozhat például egy logikai alkalmazást, amely beszerzi az ügyféladatokat egy SQL-adatbázisból, és feldolgozza az adatokat a későbbi műveletekben.

Az Azure Logic Apps által biztosított általános eseményindítók a következőket teszi:

* *Ismétlődési eseményindító*: Ez az eseményindító egy megadott ütemezés szerint fut, és nincs szorosan társítva egy adott szolgáltatáshoz vagy rendszerhez.

* *Lekérdezési eseményindító*: Ez az eseményindító rendszeresen lekérdezi egy adott szolgáltatást vagy rendszert a megadott ütemezés alapján, új adatok at ellenőrizve, vagy hogy egy adott esemény történt-e. Ha új adatok érhetők el, vagy az adott esemény történt, az eseményindító létrehoz és futtatja a logikai alkalmazás egy új példányát, amely most már használhatja a bemenetként átadott adatokat.

* *Leküldéses eseményindító:* Ez az eseményindító megvárja és figyeli az új adatokat vagy egy esemény bekövetkezéséhez. Amikor új adatok érhetők el, vagy amikor az esemény bekövetkezik, az eseményindító létrehozza és futtatja a logikai alkalmazás új példányát, amely most már használhatja a bemenetként átadott adatokat.

<a name="connections"></a>

## <a name="connector-configuration"></a>Összekötő konfigurációja

Minden összekötő eseményindítók és műveletek saját tulajdonságokat biztosít a konfiguráláshoz. Számos összekötő kigényeli azt is, hogy először hozzon létre egy *kapcsolatot* a célszolgáltatással vagy rendszerrel, és adja meg a hitelesítési hitelesítő adatokat vagy más konfigurációs adatokat, mielőtt egy eseményindítót vagy műveletet használhatna a logikai alkalmazásban. Például engedélyeznie kell egy kapcsolatot egy Twitter-fiókkal az adatok eléréséhez vagy az Ön nevében történő közzétételhez.

Az Azure Active Directory (Azure AD) OAuth-ot használó összekötők esetén a kapcsolat létrehozása azt jelenti, hogy bejelentkezik a szolgáltatásba, például az Office 365-be, a Salesforce-ba vagy a GitHubba, ahol a hozzáférési jogkivonat [titkosítva](../security/fundamentals/encryption-overview.md) van, és biztonságosan tárolódik egy Titkos Azure-tárolóban. Más összekötők, például az FTP és az SQL konfigurációs adatokat tartalmazó kapcsolatot igényelnek, például a kiszolgáló címét, felhasználónevét és jelszavát. Ezek a kapcsolatkonfigurációs adatok is titkosítva vannak, és biztonságosan tárolódnak. További információ [az Azure-beli titkosításról.](../security/fundamentals/encryption-overview.md)

A kapcsolatok mindaddig elérhetik a célszolgáltatást vagy rendszert, amíg az adott szolgáltatás vagy rendszer ezt lehetővé teszi. Az Azure AD OAuth-kapcsolatokat használó szolgáltatások, például az Office 365 és a Dynamics esetében az Azure Logic Apps korlátlan ideig frissíti a hozzáférési jogkivonatokat. Más szolgáltatások korlátozhatják, hogy az Azure Logic Apps mennyi ideig használhatja a jogkivonatot frissítés nélkül. Egyes műveletek általában érvénytelenítik az összes hozzáférési jogkivonatot, például a jelszó módosítását.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Egyéni API-k és összekötők

Az egyéni kódot futtató vagy összekötőként nem elérhető API-k hívásához egyéni [API-alkalmazások létrehozásával](../logic-apps/logic-apps-create-api-app.md)kiterjesztheti a Logic Apps platformot. [Egyéni összekötőket](../logic-apps/custom-connector-overview.md) is létrehozhat *bármely* REST vagy SOAP-alapú API-hoz, amelyek ezeket az API-kat elérhetővé teszik az Azure-előfizetés bármely logikai alkalmazás számára. Ha az egyéni API-alkalmazásokat vagy -összekötőket bárki számára nyilvánossá szeretné tenni az Azure-ban, [elküldheti az összekötőket a Microsoft minősítéséhez.](../logic-apps/custom-connector-submit-certification.md)

> [!NOTE]
> Az [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) üzembe helyezett és futtatott logikai alkalmazások közvetlenül hozzáférhetnek az Azure virtuális hálózat erőforrásaihoz. Ha egyéni összekötők, amelyek megkövetelik a helyszíni adatátjáró, és ezeket az összekötőket egy ISE-n kívül, logikai alkalmazások egy ISE is használhatja ezeket az összekötőket.
>
> Az ISE-n belül létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül hozzáférhetnek a helyszíni adatforrásokhoz, amelyek az ISE-t üzemeltető Azure virtuális hálózathoz kapcsolódnak. Így az ISE-ben lévő logikai alkalmazásoknak valószínűleg nincs szükségük az adatátjáróra, amikor ezekkel az erőforrásokkal kommunikálnak.
>
> Az ISE-k létrehozásáról további információt a [Csatlakozás az Azure-beli virtuális hálózatokhoz az Azure Logic Apps alkalmazásból című témakörben talál.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>További lépések

* A [teljes összekötőlista](https://docs.microsoft.com/connectors) megtekintése
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Egyéni összekötők létrehozása logikai alkalmazásokhoz](https://docs.microsoft.com/connectors/custom-connectors/)
* [Egyéni API-k létrehozása logikai alkalmazásokhoz](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Kapcsolódás helyszíni adatforrásokhoz a helyszíni adatátjáróval rendelkező logikai alkalmazásokból"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Azure API Management szolgáltatáspéldány létrehozása az API-k kezeléséhez és közzétételéhez"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Logikai alkalmazások integrálása az App Service API-alkalmazásokkal"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Logikai alkalmazások integrálása Azure Functions-függvényekkel"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Üzenetek feldolgozása csoportokban vagy kötegekként"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Egy feltétel kiértékelése és különböző műveletek futtatása annak alapján, hogy a feltétel igaz vagy hamis"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Ugyanazokat a műveleteket hajtsa végre a tömb minden elemén"
[http-doc]: ./connectors-native-http.md "HTTP- vagy HTTPS-végpontok hívása a logikai alkalmazásokból"
[http-request-doc]: ./connectors-native-reqres.md "HTTP-kérések fogadása a logikai alkalmazásokban"
[http-response-doc]: ./connectors-native-reqres.md "Válasz a logikai alkalmazásokhttp-kéréseire"
[http-swagger-doc]: ./connectors-native-http-swagger.md "REST-végpontok hívása a logikai alkalmazásokból"
[http-webhook-doc]: ./connectors-native-webhook.md "Várakozás a HTTP- vagy HTTPS-végpontok konkrét eseményeire"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Logikai alkalmazások integrálása beágyazott munkafolyamatokkal"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Tömbök kiválasztása és szűrése a Lekérdezés művelettel"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Logikai alkalmazások ütemezés alapján való futtatása"
[schedule-delay-doc]: ./connectors-native-delay.md "A következő művelet futtatásának késleltetése"
[schedule-delay-until-doc]: ./connectors-native-delay.md "A következő művelet futtatásának késleltetése"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Logikai alkalmazások futtatása ismétlődő ütemezés szerint"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Olyan logikai alkalmazások futtatása, amelyeknek összefüggő adattömbökben kell kezelniük az adatokat"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "A műveletek csoportokba rendezése, amelyek saját állapotot kapnak a csoportban végzett műveletek futtatása után"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "A műveleteket egyedi értékekhez rendelt esetekbe rendezheti. Csak azt az esetet futtassa, amelynek értéke megegyezik egy kifejezés, objektum vagy jogkivonat eredményével. Ha nincs egyezés, futtassa az alapértelmezett esetet"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "A logikai alkalmazás aktívan futó munkafolyamatának leállítása vagy megszakítása"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Műveletek ismétlése addig, amíg a megadott feltétel nem teljesül, vagy valamilyen állapot meg nem változik"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Adatműveletek végrehajtása, például tömbök szűrése vagy CSV- és HTML-táblák létrehozása"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Műveletek végrehajtása változókkal, például inicializálás, beállítás, növekmény, növekmény és hozzáfűzés karakterlánc- vagy tömbváltozóval"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "A blobtárolókban található fájlok kezelése az Azure Blob Storage-összekötővel"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Csatlakozás az Azure Cosmos DB-hez, hogy hozzáférhessen a dokumentumokhoz és a tárolt eljárásokhoz"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Az Event Grid által közzétett események figyelése, például az Azure-erőforrások vagy a külső erőforrások változásakor"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Csatlakozás az Azure Event Hubs szolgáltatáshoz, hogy eseményeket fogadhasson és küldhessen a logikai alkalmazások és az Eseményközpontok között"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Csatlakozás az Azure Storage-fiókhoz, hogy fájlokat hozhasson létre, frissíthessen, beszerezhetése és törlése érdekében"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Csatlakozzon Azure Storage-fiókjához, hogy várólistákat és üzeneteket hozhasson létre és kezelhessen"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Üzeneteket küldhet a Service Bus-üzenetsorokból és -témakörökből, valamint fogadhatja a Service Bus-üzenetsorok és -előfizetések üzeneteit."
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Csatlakozás az Azure SQL Data Warehouse-hoz, hogy megtekinthesse az adatokat"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Csatlakozzon Azure Storage-fiókjához, hogy táblákat és egyebeket hozhasson létre, frissíthessen és"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Csatlakozás biztalk kiszolgálóhoz, hogy biztalk alapú alkalmazásokat futtathasson egymás mellett az Azure Logic Apps alkalmazásokkal"
[box-doc]: ./connectors-create-api-box.md "Csatlakozzon a Boxhoz. Fájlok feltöltése, beszerezése, törlése, listázása stb."
[dropbox-doc]: ./connectors-create-api-dropbox.md "Csatlakozzon a Dropboxhoz. Fájlok feltöltése, beszerezése, törlése, listázása stb."
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Csatlakozás a Dynamics CRM Online rendszerhez, hogy a CRM Online-adatokkal dolgozhass"
[facebook-doc]: ./connectors-create-api-facebook.md "Csatlakozz on Facebook. Közzététel idővonalon, oldalhírcsatorna betöltése stb."
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Csatlakozhat egy helyszíni fájlrendszerhez"
[ftp-doc]: ./connectors-create-api-ftp.md "Csatlakozhat egy FTP-/FTPS-kiszolgálóhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlokat törölhet, tölthet fel vagy kérhet le"
[github-doc]: ./connectors-create-api-github.md "Csatlakozhat a GitHubhoz, és különböző problémákat követhet nyomon"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Csatlakozik a Google Naptárhoz, és kezelheti a naptárat"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Csatlakozás a GoogleDrive-hoz, hogy az adatokkal dolgozhass"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Csatlakozás a Google Táblázatokhoz, hogy módosíthassa lapjait"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "A Google-feladatokhoz való csatlakozás, így kezelheti feladatait"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Csatlakozás 3270-es alkalmazásokhoz IBM nagyszámítógépeken"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Csatlakozzon az IBM DB2-hez a felhőben vagy a helyszínen. Sor frissítése, tábla beszerezése stb."
[ibm-informix-doc]: ./connectors-create-api-informix.md "Csatlakozzon az Informixhez a felhőben vagy a helyszínen. Sor olvasása, táblázatok listázása stb."
[ibm-mq-doc]: ./connectors-create-api-mq.md "Csatlakozás az IBM MQ-hoz a helyszínen vagy az Azure-ban üzenetek küldéséhez és fogadásához"
[instagram-doc]: ./connectors-create-api-instagram.md "Csatlakozz az Instagramhoz. Események aktiválása vagy cselekvése"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Csatlakozzon a MailChimp-fiókjához. E-mailek kezelése és automatizálása"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Csatlakozhat a Mandrillhoz, ha azon keresztül szeretne kommunikálni"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Csatlakozzon a helyszíni MySQL adatbázisához, hogy adatokat olvashasson és írhasson"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Csatlakozás Office 365-fiókjához, így e-maileket küldhet és fogadhat, kezelheti naptárát és névjegyeit stb."
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Csatlakozás személyes Microsoft OneDrive-hoz, hogy fájlokat tölthessen fel, törölhessen, listázhasson"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Csatlakozás vállalati Microsoft OneDrive-hoz, hogy feltölthesse, törölhethesse, listázhassa fájljait stb."
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Csatlakozás Oracle-adatbázishoz, így sorokat adhat hozzá, szúrhat be, törölhet"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Csatlakozás az Outlook-postaládához, hogy kezelni tudja e-mailjeit, naptárait, névjegyeit és egyebeit"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Csatlakozzon a PostgreSQL adatbázisához, hogy adatokat olvashasson a táblákból"
[project-online-doc]: ./connectors-create-api-projectonline.md "Csatlakozás a Microsoft Project Online programhoz, hogy kezelni tudja projektjeit, tevékenységeit, erőforrásait és egyebeit"
[rss-doc]: ./connectors-create-api-rss.md "Hírcsatorna-elemek közzététele és beolvasása, eseményindító műveletek, amikor új elemet tesznek közzé RSS-hírcsatornában"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Csatlakozzon a Salesforce-fiókjához. Partnerek, érdeklődők, lehetőségek és egyebek kezelése"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Csatlakozhat egy helyszíni SAP-rendszerhez"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Csatlakozzon a SendGridhez. E-mail küldése és címzettlisták kezelése"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Csatlakozzon az SFTP-fiókjához az SSH használatával. Fájlok feltöltése, beszerezése, törlése stb."
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Csatlakozás a helyszíni SharePoint-kiszolgálóhoz. Dokumentumok, listaelemek és egyebek kezelése"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Csatlakozás a SharePoint Online-hoz. Dokumentumok, listaelemek és egyebek kezelése"
[slack-doc]: ./connectors-create-api-slack.md "Csatlakozás a Tartalékidőhöz és üzenetek küldése a Tartalékcsatornákhoz"
[smtp-doc]: ./connectors-create-api-smtp.md "Csatlakozás SMTP-kiszolgálóhoz és e-mail küldése mellékletekkel"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Csatlakozhat a SparkPosthoz, ha azon keresztül szeretne kommunikálni"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Csatlakozzon az Azure SQL Database vagy az SQL Server szolgáltatáshoz. SQL adatbázistábla bejegyzéseinek létrehozása, frissítése, bementése és törlése"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Csatlakozás a Teradata-adatbázishoz táblákból történő adatok olvasásához"
[trello-doc]: ./connectors-create-api-trello.md "Csatlakozz On Trello. Kezelje projektjeit és rendszerezzen bárkivel bármit"
[twilio-doc]: ./connectors-create-api-twilio.md "Csatlakozzon a Twilio-hoz. Üzenetek küldése és beszerzése, elérhető számok beszerzése, bejövő telefonszámok kezelése stb."
[twitter-doc]: ./connectors-create-api-twitter.md "Csatlakozzon a Twitterhez. Idővonalak, tweetek és egyebek"
[yammer-doc]: ./connectors-create-api-yammer.md "Csatlakozás a Yammerhez. Üzenetek postai üzenetei, új üzenetek beküldése stb."
[youtube-doc]: ./connectors-create-api-youtube.md "Csatlakozzon a YouTube-hoz. Videók és csatornák kezelése"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Az AS2 protokollt használó üzenetek kódolása és dekódolása"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Az EDIFACT protokollt használó üzenetek kódolása és dekódolása"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Az EDIFACT protokollt használó üzenetek dekódolása"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Az EDIFACT protokollt használó üzenetek kódolása"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "További információ a vállalati integráció sajtáról"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "További információ a vállalati integráció sajtáról"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Az integrációs fiók összetevőinek metaadatainak kezelése"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "A JSON átalakítása folyékony sablonokkal"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 protokollt használó üzenetek kódolása és dekódolása"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Az X12 protokollt használó üzenetek dekódolása"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Az X12 protokollt használó üzenetek kódolása"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML-üzenetek átalakítása"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML-üzenetek ellenőrzése"

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
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
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
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
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
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
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
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
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
