---
title: Az Azure storage table tervezési minták |} A Microsoft Docs
description: Az Azure table service-megoldások minták használatával.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: d055ea9b30732e1cc0fc4ae5471bae26adc08b35
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238896"
---
# <a name="table-design-patterns"></a>Táblatervezési minták
Ez a cikk néhány tábla szolgáltatási megoldások használható mintákat ismerteti. Ezenkívül láthatja, miként, gyakorlatilag kezelheti néhány problémákat és egyéb tárolási tervezési cikkek tárgyalt feláldozását. Az alábbi ábrán a különböző minták között létesített kapcsolatait összegzi:  

![kapcsolódó adatok keresése céljából](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


A minta térkép felett (kék) mintákat és kizárási mintákat (narancs), ebben az útmutatóban ismertetett közötti kapcsolathoz emeli ki. Nincsenek számos más mintákat, amelyek mellett szóló érvek. Például a Table Service azon kulcsfontosságú forgatókönyvek egyike, hogy használja a [Materialized View minta](https://msdn.microsoft.com/library/azure/dn589782.aspx) származó a [parancs Query Responsibility Segregation (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) mintát.  

## <a name="intra-partition-secondary-index-pattern"></a>Intra-partition másodlagos index minta
Több másolatot minden entitás használatával különböző Store **rowkey tulajdonságok esetén** értékeket (az ugyanazon a partíción) a gyors és hatékony kereséseket, valamint az alternatív sorrendre különböző **RowKey** értékeket. Példányok közötti frissítéseket is konzisztens EGT a használatával.  

### <a name="context-and-problem"></a>Kontextus és probléma
A Table service automatikusan indexeli az entitásoknak a **PartitionKey** és **RowKey** értékeket. Ez lehetővé teszi egy ügyfélalkalmazás egy entitás hatékonyan használatával ezek az értékek lekéréséhez. Például használja az alábbi táblázat szerkezete, egy ügyfélalkalmazás egy pont query segítségével kérje le egy adott alkalmazott entitást a részleg nevét és az alkalmazott azonosítója (a **PartitionKey** és **rowkey tulajdonságok esetén**  értékek). Egy ügyfél is lekérhet entitások minden részleg alkalmazott azonosítója alapján rendezi.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Ha szeretné is tudja megtalálni egy alkalmazott entitás, például az e-mail-címét, egy másik tulajdonságának értéke alapján való kevésbé hatékony partíció vizsgálatot kell használnia. Ennek oka az, a table service nem biztosít a másodlagos indexeket. Ezenkívül nincs lehetőség egy listát az alkalmazottak, mint egy másik sorrendben rendezve **RowKey** sorrendben.  

### <a name="solution"></a>Megoldás
A másodlagos indexek hiánya megkerüléséhez minden egyes használatával egy másik példánya minden entitás több példányát is tárolhatja **RowKey** értéket. Ha egy entitás tárolja az alább látható szerkezetéről, hatékonyan kérheti alkalmazott entitások alapuló e-mail-cím vagy az alkalmazott azonosítója. Az előtag értékei a **RowKey**, "empid_" és "email_" engedélyezi, hogy a lekérdezés egyetlen alkalmazott vagy egy tartományt az alkalmazottak e-mail-címek vagy az alkalmazotti azonosítókat számos használatával.  

![Alkalmazott entitások](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

A következő két szűrési feltételeket (egy keresésekor által alkalmazott azonosítója és a egy keresése a e-mail-címe) is adja meg a pont lekérdezéseket:  

* $filter = (PartitionKey eq "Értékesítés") és (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq "Értékesítés") és (RowKey eq 'email_jonesj@contoso.com")  

Ha alkalmazott entitástartományának kérdezze le, megadhat egy alkalmazott azonosítója sorrendben rendezve tartománya vagy egy tartományt, az a megfelelő előtaggal rendelkező entitások esetében lekérdezésével e-mail cím sorrendben rendezve a **RowKey**.  

* Található összes alkalmazott a Sales nevű részleg és a egy alkalmazott azonosító a tartomány 000100 való 000199 használata: $filter = (PartitionKey eq "Értékesítés") és (RowKey ge "empid_000100") és a ("empid_000199" rowkey tulajdonságok esetén le)  
* A Sales nevű részleg minden alkalmazott található kezdve a levél "a" használja e-mail-címmel: $filter = (PartitionKey eq "Értékesítés") és (RowKey ge "email_a") és (RowKey lt "email_b")  
  
  Vegye figyelembe, hogy a fenti példákban használt szűrő szintaxisa a következő további információ: a Table service REST API-t a [Entitáslekérdezés](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* TABLE storage szolgáltatás viszonylag olcsó, így a költségek terhelését a duplikált adatok való tárolásának fő szempont nem lehet használni. Azonban kell mindig a várható tárolási igényei alapján a Tervező költségének kiértékeléséhez, és csak hozzá az ügyfélalkalmazás hajtja végre a lekérdezéseket támogató kettős bejegyzés.  
* Mivel a másodlagos index entitások az eredeti entitásokként ugyanazon a partíción találhatók, győződjön meg róla, hogy Ön nem haladja meg a skálázhatósági célokat az egyes partíciók.  
* Beállíthatja, hogy az ismétlődő entitások egymással konzisztenssé szolgáltatásfrissítést frissíteni az entitás két példányban EGTs használatával. Ez azt jelenti, hogy egy entitás összes példányát tárolja egyazon partícióra kerüljenek. További információkért lásd: a szakasz [használatával tranzakciók](table-storage-design.md#entity-group-transactions).  
* Használt érték a **RowKey** az egyes entitásokhoz egyedinek kell lennie. Fontolja meg az összetett kulcs értékeinek használatával.  
* A numerikus értékek Padding a **RowKey** (például az alkalmazott azonosítója 000223), lehetővé teszi, hogy javítsa ki, rendezési és szűrési alapján felső és alsó határértékét.  
* Ön nem feltétlenül kell ismétlődő az entitás tulajdonságait. Például ha a lekérdezések az entitások, az e-mail cím, hogy a keresési a **rowkey tulajdonságok esetén** soha nem kell az alkalmazott életkor, ezek az entitások sikerült az alábbi struktúrával rendelkeznek:

   ![Entitásstruktúrát alkalmazott](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Általában jobb duplikált adatok tárolására, és győződjön meg arról, hogy egyetlen lekérdezéssel, a szükséges összes adatot lekérheti egy lekérdezés használata egy entitás és egy másik talált a szükséges adatok kereséséhez.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát használni, amikor az ügyfélalkalmazásnak szüksége van, számos különböző kulcsok használatát, amikor az ügyfélnek kell kérje le az entitásokat a másik a rendezési sorrend entitások lekéréséhez, és ahol minden entitás egyedi értékek többféle használatával azonosíthatja. Azonban meg kell róla, hogy azt ne haladja meg a partíció méretezhetőségének korlátai használ a különböző entitások keresések végrehajtásakor **RowKey** értékeket.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Közötti partíció másodlagos indexe mintája](#inter-partition-secondary-index-pattern)
* [Összetett kulcs minta](#compound-key-pattern)
* [Tranzakciók](#entity-group-transactions)
* [Heterogén entitástípusok az dolgozik](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Közötti partíció másodlagos indexe mintája
Több másolatot minden entitás használatával különböző Store **rowkey tulajdonságok esetén** értékek a különböző partíciók vagy a különböző táblák a gyors és hatékony kereséseket, valamint az alternatív sorrendre különböző **RowKey**értékeket.  

### <a name="context-and-problem"></a>Kontextus és probléma
A Table service automatikusan indexeli az entitásoknak a **PartitionKey** és **RowKey** értékeket. Ez lehetővé teszi egy ügyfélalkalmazás egy entitás hatékonyan használatával ezek az értékek lekéréséhez. Például használja az alábbi táblázat szerkezete, egy ügyfélalkalmazás egy pont query segítségével kérje le egy adott alkalmazott entitást a részleg nevét és az alkalmazott azonosítója (a **PartitionKey** és **rowkey tulajdonságok esetén**  értékek). Egy ügyfél is lekérhet entitások minden részleg alkalmazott azonosítója alapján rendezi.  

![Alkalmazott azonosítója](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Ha szeretné is tudja megtalálni egy alkalmazott entitás, például az e-mail-címét, egy másik tulajdonságának értéke alapján való kevésbé hatékony partíció vizsgálatot kell használnia. Ennek oka az, a table service nem biztosít a másodlagos indexeket. Ezenkívül nincs lehetőség egy listát az alkalmazottak, mint egy másik sorrendben rendezve **RowKey** sorrendben.  

Rendszer várhatóan nagyon nagy mennyiségű, ezek az entitások tranzakciókat, és szeretné, a Table Service, az ügyfél szabályozás csökkentése érdekében.  

### <a name="solution"></a>Megoldás
Másodlagos indexek hiánya megkerüléséhez tárolhat több példányt minden entitás minden egyes másolás használatával különböző **PartitionKey** és **RowKey** értékeket. Ha egy entitás tárolja az alább látható szerkezetéről, hatékonyan kérheti alkalmazott entitások alapuló e-mail-cím vagy az alkalmazott azonosítója. Az előtag értékei a **PartitionKey**, "empid_" és "email_" engedélyezi, hogy melyik index egy lekérdezés használni kívánt azonosításához.  

![Index elsődleges és másodlagos index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


A következő két szűrési feltételeket (egy keresésekor által alkalmazott azonosítója és a egy keresése a e-mail-címe) is adja meg a pont lekérdezéseket:  

* $filter = (PartitionKey eq ' empid_Sales") és (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") és (RowKey eq 'jonesj@contoso.com")  

Ha alkalmazott entitástartományának kérdezze le, megadhat egy alkalmazott azonosítója sorrendben rendezve tartománya vagy egy tartományt, az a megfelelő előtaggal rendelkező entitások esetében lekérdezésével e-mail cím sorrendben rendezve a **RowKey**.  

* A tartomány alkalmazott azonosítójú a Sales nevű részleg minden alkalmazott található **000100** való **000199** alkalmazott azonosítója használatához rendezve: $filter = (PartitionKey eq ' empid_Sales") és (RowKey ge"000100") és a ("000199" rowkey tulajdonságok esetén le)  
* E-mail-címmel, amely elindítja az "a" e-mail cím használatához rendezett a Sales nevű részleg minden alkalmazott kereséséhez: $filter = (PartitionKey eq ' email_Sales") és (RowKey ge"a") és (RowKey lt"b")  

Vegye figyelembe, hogy a fenti példákban használt szűrő szintaxisa a következő további információ: a Table service REST API-t a [Entitáslekérdezés](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Megtarthatja a kettős bejegyzés végül konzisztens egymással használatával a [végül konzisztens tranzakció minta](#eventually-consistent-transactions-pattern) az elsődleges és másodlagos index entitások fenntartásához.  
* TABLE storage szolgáltatás viszonylag olcsó, így a költségek terhelését a duplikált adatok való tárolásának fő szempont nem lehet használni. Azonban kell mindig a várható tárolási igényei alapján a Tervező költségének kiértékeléséhez, és csak hozzá az ügyfélalkalmazás hajtja végre a lekérdezéseket támogató kettős bejegyzés.  
* Használt érték a **RowKey** az egyes entitásokhoz egyedinek kell lennie. Fontolja meg az összetett kulcs értékeinek használatával.  
* A numerikus értékek Padding a **RowKey** (például az alkalmazott azonosítója 000223), lehetővé teszi, hogy javítsa ki, rendezési és szűrési alapján felső és alsó határértékét.  
* Ön nem feltétlenül kell ismétlődő az entitás tulajdonságait. Például ha a lekérdezések az entitások, az e-mail cím, hogy a keresési a **rowkey tulajdonságok esetén** soha nem kell az alkalmazott életkor, ezek az entitások sikerült az alábbi struktúrával rendelkeznek:
  
   ![Alkalmazott entitás (másodlagos index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Általában célszerűbb duplikált adatok tárolására, és győződjön meg arról, hogy egyetlen lekérdezést, mint, több lekérdezés használja az elsődleges index a másodlagos index és a egy másik lookup a szükséges adatokat használó entitásban található a szükséges összes adatot lekérheti.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát használni, amikor az ügyfélalkalmazásnak szüksége van, számos különböző kulcsok használatát, amikor az ügyfélnek kell kérje le az entitásokat a másik a rendezési sorrend entitások lekéréséhez, és ahol minden entitás egyedi értékek többféle használatával azonosíthatja. Használja ezt a mintát, ha el szeretné kerülni, hogy a partíció méretezhetőségének korlátai meghaladja a használatával a különböző entitások keresések végrehajtásakor **RowKey** értékeket.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakció mintája](#eventually-consistent-transactions-pattern)  
* [Intra-partition másodlagos index minta](#intra-partition-secondary-index-pattern)  
* [Összetett kulcs minta](#compound-key-pattern)  
* [Tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusok az dolgozik](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Végül konzisztens tranzakció mintája
Engedélyezze a végül konzisztens viselkedés partícióhatárok vagy a tárolási rendszer határok között az Azure-üzenetsorok használatával.  

### <a name="context-and-problem"></a>Kontextus és probléma
EGTs elemi tranzakciókat engedélyezése több ugyanazzal a partíciókulccsal rendelkező entitások között. A teljesítmény és méretezhetőség érdekében dönthet úgy, hogy külön partícióban, vagy egy külön tárolási rendszerben adatkonzisztencia-követelményeitől rendelkező entitásokat: ilyen esetben nem használhat EGTs biztosítja az egységességet. Előfordulhat például, hogy a követelmény, hogy a végleges konzisztencia között karbantartása:  

* Két különböző partíciók ugyanabban a táblában, a különböző táblák vagy a különböző storage-fiókban tárolt entitások.  
* A Table service szolgáltatásban tárolt entitásra, és a Blob service-ben tárolt blobok.  
* A fájlrendszer a Table service és a egy fájlban tárolt entitásra.  
* A Table service szolgáltatásban egy entitás tároló még indexelése az Azure Search szolgáltatással.  

### <a name="solution"></a>Megoldás
Az Azure-üzenetsorok használatával valósítható meg olyan megoldás, amely a végső konzisztenciát biztosít a két partíció és tárolórendszerek között.
Ezt a megközelítést mutatja be, fel, hogy a követelmény, hogy tudni archiválja a régi alkalmazott entitások. Régi alkalmazott entitások ritkán lesznek lekérdezve, és olyan tevékenységet, amely az aktuális alkalmazottak foglalkozik ki kell zárni. Ezt a követelményt megvalósításához az aktív alkalmazottak tárolja a **aktuális** tábla- és a korábbi alkalmazottak a **archív** tábla. Egy alkalmazott archiválás igényel, az a entitás törléséhez a **aktuális** táblát, és adja hozzá a kívánt entitásra a **archív** táblában, de nem használható egy EGT két művelet végrehajtásához. Annak elkerülése érdekében, hogy egy hiba miatt az entitások jelennek meg mindkét vagy sem táblában, az archiválási művelet végül konzisztens kell lennie. Az alábbi feladatütemezési ábrán ez a művelet lépéseit ismerteti. További részleteket a következő szöveget a Kivétel elérési utak biztosítunk.  

![Az Azure-üzenetsorok megoldás](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Ügyfél üzenetet helyez el az Azure-üzenetsort, ebben a példában alkalmazott #456 archiválása a archív műveletet kezdeményez. Feldolgozói szerepkör az üzenetsorról új üzenetek; Ha talál egyet, kiolvassa az üzenetet, és egy rejtett másolási elhagyja az üzenetsorban. A feldolgozói szerepkör ezután lekéri egy entitást egy példányát a **aktuális** táblában, másolja a a **archív** táblát, és végül törli az eredeti a **aktuális** tábla. Végül ha nincsenek hibák az előző lépésekből származó, a feldolgozói szerepkör törli a rejtett üzenetet az üzenetsorból.  

Ebben a példában 4. lépés szúr be az alkalmazott a **archív** tábla. Azt adja hozzá az alkalmazott egy blobot a Blob service-ben vagy egy fájl a fájlrendszer sikerült.  

### <a name="recovering-from-failures"></a>Helyreálljon a hibák
Fontos, hogy a lépéseket az operations **4** és **5** kell lennie *idempotens* abban az esetben, ha a feldolgozói szerepkör újra kell indítani az archiválási művelet. Ha használja a Table service, a lépés **4** kell használnia a "beszúrása vagy lecserélése" művelet; lépés **5** használjon egy "törlése, ha létezik" műveletet az ügyféloldali kódtár használata. Ha egy másik tárolási rendszert használ, egy megfelelő idempotens művelet kell használnia.  

Ha a feldolgozói szerepkör soha nem fejeződik be a lépés **6**, majd után időtúllépés az üzenet ismét megjelenik az üzenetsor, próbálja meg újból feldolgozza, a feldolgozói szerepkör készen áll. A feldolgozói szerepkör ellenőrizheti, hogy hányszor az üzenetsorban lévő üzenet lett, olvassa el, és ha szükséges, ez a jelző azt úgy, hogy egy különálló sorban egy "ártalmas" üzenetet vizsgálat céljára. Üzenetsorbeli üzenetek olvasási, és az eltávolítási száma ellenőrzésével kapcsolatos további információkért lásd: [üzenetek beolvasása](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Néhány hiba a Table és Queue szolgáltatások átmeneti hibák, és az ügyfélalkalmazás tartalmaznia kell a megfelelő újrapróbálkozási logikát őket.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ez a megoldás által nyújtott elkülönítési tranzakció nem. Például egy ügyfél tudta olvasni a **aktuális** és **archív** táblák, amikor a feldolgozói szerepkör lett lépések közötti **4** és **5**, és egy az adatok inkonzisztens nézetét. Vegye figyelembe, hogy az adatok egységes idővel.  
* Meg kell róla, hogy a 4. és 5 lépéseket idempotens végleges konzisztencia biztosítása érdekében.  
* A megoldás több üzenetsort és feldolgozói szerepkörpéldányok segítségével méretezheti.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha meg szeretné garantálja a konzisztenciát entitások a különböző partíciók vagy a táblák között. Ez a minta a végleges konzisztencia műveletek biztosítása a Table service és a Blob service, illetve egyéb nem Azure Storage adatforrás, például az adatbázis vagy a fájlrendszer bővítheti.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Tranzakciók](#entity-group-transactions)  
* [Egyesítés vagy cseréje](#merge-or-replace)  

> [!NOTE]
> Ha a tranzakció elkülönítési fontos, hogy a megoldás, érdemes lehet ahhoz, hogy EGTs használja a táblák újratervezése.  
> 
> 

## <a name="index-entities-pattern"></a>Index entitások minta
Karbantartása index entitások engedélyezése a hatékony keresést, hogy az entitások listáját adja vissza.  

### <a name="context-and-problem"></a>Kontextus és probléma
A Table service automatikusan indexeli az entitásoknak a **PartitionKey** és **RowKey** értékeket. Ez lehetővé teszi egy ügyfélalkalmazás egy entitás hatékonyan pont lekérdezéssel lekérni. Például használja az alábbi táblázat szerkezete, az ügyfélalkalmazások hatékonyan egyes alkalmazott entitás használatával lekérhető a részleg nevét és az alkalmazott azonosítója (a **PartitionKey** és **RowKey**).  

![Alkalmazott entitás](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Ha szeretné is tudja lekérni a vezetéknevét, például egy másik nem egyedi tulajdonság értéke alapján alkalmazott entitások listájának a kevésbé hatékony partíció vizsgálat keresse ki őket közvetlenül az index használata helyett egyezés található kell használnia. Ennek oka az, a table service nem biztosít a másodlagos indexeket.  

### <a name="solution"></a>Megoldás
Vezetéknév keresési engedélyezése a fenti entitás struktúrával, meg kell karbantartása alkalmazotti azonosítókat. Ha egy adott Vezetéknév Jones, például az alkalmazottak entitásokat a keresett először keresse meg az alkalmazott azonosítók listáját Jones, a Vezetéknév, az alkalmazottak számára, és az majd lekérheti az alkalmazott entitásokból. A listák alkalmazotti azonosítókat tárolására szolgáló három fő lehetőség áll rendelkezésre:  

* Blob storage használata.  
* Hozzon létre index entitások az alkalmazottak entitásokként ugyanazon a partíción.  
* Index entitások létrehozása egy külön partíciót vagy tábla.  

<u>#1. lehetőség: A blob storage</u>  

Az első lehetőség, akkor hozzon létre minden egyedi Vezetéknév, és minden egyes blob-tárolóban lévő blobok listáját a **PartitionKey** (részleg) és **RowKey** (alkalmazott azonosítója) értékét az alkalmazottak a múlt nevet. Hozzáadásakor, vagy egy alkalmazott törlése biztosítania kell, hogy idővel konzisztenssé váljanak az alkalmazott entitások-e a megfelelő blob tartalmát.  

<u>#2. lehetőség:</u> ugyanazon a partíción index entitások létrehozása  

A második lehetőség használja a index entitások, amelyek tárolják a következő adatokat:  

![Alkalmazott index entitás](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

A **EmployeeIDs** tárolt utolsó nevű tulajdonság tartalmazza az alkalmazottak számára az alkalmazotti azonosítókat listáját a **RowKey**.  

Az alábbi lépéseket kell követnie, ha a hozzáadni kívánt új alkalmazott a második lehetőség használatakor vázoltuk. Ebben a példában egy alkalmazott azonosítója 000152 és a egy Vezetéknév Jones a Sales nevű részleg adunk hozzá:  

1. Kérje le az index-entitást együtt egy **PartitionKey** "Értékesítés" értéket, és a **RowKey** értéke "János". Mentse a 2. lépésben használandó ETag címkéje ehhez az entitáshoz.  
2. Hozzon létre egy entitás-csoport tranzakció (azaz kötegelt művelet), amely az új alkalmazottak entitás szúr be (**PartitionKey** "Értékesítés" értéket, és **rowkey tulajdonságok esetén** "000152" érték), és frissíti az index entitás (**PartitionKey** "Értékesítés" értéket, és **RowKey** értéke "János") az új alkalmazott azonosítója a EmployeeIDs mezőjében a listához való hozzáadásával. Tranzakciók kapcsolatos további információkért lásd: [tranzakciók](#entity-group-transactions).  
3. Ha az entitás csoport tranzakció (valaki más imént módosította az index entitás) optimista egyidejűségi hiba miatt nem sikerül, majd szüksége 1. lépés: újra kezdenie.  

Egy alkalmazott törlése, ha használja a második lehetőség hasonló megközelítés is használhatja. Az alkalmazottak Vezetéknév módosítjuk az némileg összetettebb, mert szüksége lesz egy entitás, amely frissíti a három entitások csoport tranzakció végrehajtásához: az alkalmazott, az index entitás a régi vezetéknévhez, és a index entitás az új utolsó nevét. Minden entitás, amely ezután használhatja az optimista egyidejűséget használatával frissítések végrehajtásához ETag értékek lekéréséhez változtatások előtt le kell kérnie.  

Az alábbi lépéseket kell követnie, amikor szüksége van egy részleg utolsó adott nevű minden alkalmazott keresse ki a második lehetőség használatakor vázoltuk. Ebben a példában azt keres, a Vezetéknév Jones a Sales nevű részleg minden alkalmazott fel:  

1. Kérje le az index-entitást együtt egy **PartitionKey** "Értékesítés" értéket, és a **RowKey** értéke "János".  
2. Elemezni az alkalmazotti azonosítókat a EmployeeIDs mezőben listája.  
3. További információ az egyes ezeknek a dolgozóknak (például e-mail-címeket) van szüksége, ha minden ilyen használatával alkalmazott lekéréséhez **PartitionKey** "Értékesítés" érték és **RowKey** értékeket a 2. lépésben beolvasott alkalmazottak listája.  

<u>#3. lehetőség:</u> index entitások egy külön partíciót, vagy a tábla létrehozása  

A harmadik lehetőség, amely a következő adatokat tárolja index entitások használja:  

![Alkalmazott index entitás egy külön partíció](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


A **EmployeeIDs** tárolt utolsó nevű tulajdonság tartalmazza az alkalmazottak számára az alkalmazotti azonosítókat listáját a **RowKey**.  

A harmadik beállítással EGTs fenntartani a konzisztenciát, mivel az index entitások az alkalmazottak entitások egy külön partíciót nem használhat. Biztosítania kell, hogy idővel konzisztenssé váljanak az alkalmazott entitások-e az index entitásokat.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ehhez a megoldáshoz legalább két lekérdezést egyező entitások lekéréséhez: az index entitások listájának beszerzése lekérdezni egy **RowKey** értékeket, majd a lekérdezések a listában minden entitás beolvasása.  
* Tekintve, hogy egy egyéni entitás legfeljebb 1 MB méretű rendelkezik, #2 és a megoldás a #3 lehetőség tegyük fel, hogy bármely adott Vezetéknév alkalmazott-azonosítóinak listája soha nem 1 MB-nál nagyobb. Alkalmazott azonosítók listáját valószínűleg 1 MB-nál nagyobbnak kell lennie, ha használja a #1. lehetőséget, és az index adatokat tárolni a blob storage.  
* #2. lehetőség használatakor (EGTs használ hozzáadása és törlése az alkalmazottak és a egy alkalmazott utolsó nevének módosítása) ki kell értékelnie, ha a tranzakciók mennyisége megközelítést alkalmaz majd egy adott partíció skálázási korlátaival. Ha ez a helyzet, fontolja meg egy végül konzisztens megoldás (#1. lehetőség vagy #3. lehetőség), amely üzenetsorokat használ a kérelmek kezelésére, és lehetővé teszi, hogy az index entitások az alkalmazottak entitások egy külön partíció.  
* Ebben a megoldásban #2. lehetőség azt feltételezi, hogy szeretné-e egy részleg Vezetéknév szerint kereshet: például szeretné beolvasni a Vezetéknév Jones a Sales nevű részleg az alkalmazottak listáját. Ha meg szeretné tudni keresse ki a teljes szervezeten belül egy utolsó nevű Jones minden alkalmazott, használja a #1. lehetőség vagy #3. lehetőség.
* Megvalósíthat egy üzenetsor-alapú megoldás, amely továbbítja a végső konzisztenciát (lásd a [végül konzisztens tranzakció minta](#eventually-consistent-transactions-pattern) további részletekért).  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha azt szeretné, hogy az összes megosztani egy közös tulajdonság értéke, például a Vezetéknév Jones rendelkező alkalmazottakra entitásokban talált.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs minta](#compound-key-pattern)  
* [Végül konzisztens tranzakció mintája](#eventually-consistent-transactions-pattern)  
* [Tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusok az dolgozik](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Denormalizáció minta
Együtt ötvözze a kapcsolódó adatokat egyetlen entitás ahhoz, hogy minden hibaérzékeny pont lekérdezés szükséges adatok lekéréséhez.  

### <a name="context-and-problem"></a>Kontextus és probléma
Egy relációs adatbázisban akkor általában normalizálása a másolás több táblából adatokat lekérő lekérdezéseket eredményez Adattörlés céljából. Normalizálása az Azure-táblák adatait, ha gondoskodnia kell, több adatváltások az ügyfélről a kiszolgálónak a kapcsolódó adatokat. Például a táblázat szerkezetét, alább látható az Intézet adatai olvashatók be két adatváltások szükséges: egy beolvasni a részleg entitás, amely tartalmazza a kezelő-a-Azonosítót és a egy másik kérelem beolvasni a részleteket a kezelő a alkalmazott entitásokban.  

![Részleg entitás és alkalmazott entitás](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Megoldás
Ahelyett, hogy az adatok tárolása a két különálló entitások, denormalizálja az adatokat, és a részleg entitás őrizze a kezelő részletek. Példa:  

![Részleg entitás](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Részleg entitásokkal ezekkel a tulajdonságokkal tárolja most kérheti le a részletekről a részleg pont lekérdezéssel kapcsolatban van szüksége.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Van néhány kétszer néhány adattárolás többletterhelést kapcsolódó költségek. A teljesítmény előny (kevesebb kéréseket a storage szolgáltatásra) általában a tárolási költségek marginális növekedése megvalósításának (és a költségek részlegesen eltolva szüksége van egy részleg adatait beolvasni a tranzakciók számának csökkentése ).  
* A konzisztencia, a két entitás, amely a kezelők kapcsolatos adatokat kell fenntartani. A konzisztencia probléma: egy atomi tranzakción belül több entitás frissítése EGTs használatával kezelheti: Ebben az esetben a részleg, és a alkalmazott entitás számára a részleg vezetője az ugyanazon a partíción tárolódik.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha gyakran kell ellenőrizzék a kapcsolódó információkat. Ez a minta csökkenti az ügyfél biztosítania kell a szükséges adatok lekéréséhez lekérdezések száma.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs minta](#compound-key-pattern)  
* [Tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusok az dolgozik](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Összetett kulcs minta
Használat összetett **RowKey** értékeket talált kapcsolódó adatok egyetlen pont lekérdezéssel ügyfél engedélyezése.  

### <a name="context-and-problem"></a>Kontextus és probléma
Egy relációs adatbázisban még természetes lekérdezésekben összekapcsolások használatával az adatok kapcsolódó darab térjen vissza az ügyfélnek egyetlen lekérdezést. Például használhat az alkalmazott azonosítója, amelyek tartalmaznak a teljesítmény, és ellenőrizze az adatokat, hogy alkalmazott kapcsolódó entitások listájának kereséséhez.  

Tegyük fel, a Table service, az alábbi struktúra használatával alkalmazott entitások tárolja:  

![Entitásstruktúrát alkalmazott](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Értékelések és teljesítménye minden évben, az alkalmazott működött, a szervezet számára vonatkozó előzményadatok tárolása emellett és érhetik el ezeket az információkat év szerint kell. Az egyik lehetőség, hogy hozzon létre egy másik táblát, amely tárolja az entitások az alábbi struktúra használatával:  

![Alternatív alkalmazott entitásstruktúrát](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Figyelje meg, hogy ezt a módszert használja dönthet megkettőzése néhány információt (például utónév és Vezetéknév), az új entitás ahhoz, hogy az adatok egy kéréssel. Azonban erős konzisztencia nem karbantartása, mivel a két entitás szolgáltatásfrissítést frissíteni egy EGT nem használható.  

### <a name="solution"></a>Megoldás
Egy új entitástípus Store az eredeti tábla entitások használatával az alábbi struktúra használatával:  

![Alkalmazott entitásstruktúrát megoldás](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Figyelje meg a **RowKey** már az alkalmazott azonosítója és az év, amely lehetővé teszi az alkalmazott teljesítmény beolvasásához, és tekintse át az adatokat egyetlen entitás egyetlen kérelem felülvizsgálata adatok összetett kulcs.  

Az alábbi példa bemutatja, hogyan kérheti le az összes felülvizsgálati adatok (például az alkalmazottak 000123 a Sales nevű részleg) egy alkalmazott:  

$filter = (PartitionKey eq "Értékesítés") és (RowKey ge "empid_000123") és (RowKey lt "empid_000124") & $select = rowkey tulajdonságok esetén, Managerben minősítés, társ-minősítés, megjegyzések  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Egy megfelelő elválasztó karaktert, amely megkönnyíti a elemezni kell használnia a **rowkey tulajdonságok esetén** érték: például **000123_2012**.  
* Az entitás ugyanazon a partíción, mint más az ugyanazon alkalmazott, ami azt jelenti, hogy az erős konzisztencia fenntartásához EGTs használhatja a kapcsolódó adatokat tartalmazó entitásokhoz is tárolja.
* Érdemes, hogy milyen gyakran fogja lekérdezni az adatokat annak megállapításához, hogy ez a minta megfelelő.  Például ha ritkán tekintse át adatokat, és a főbb alkalmazotti adatokat gyakran fogja elérni kell tartania őket, különálló entitások.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha kell tárolnia, egy vagy több kapcsolódó entitások lekérdezett gyakran.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusok az dolgozik](#working-with-heterogeneous-entity-types)  
* [Végül konzisztens tranzakció mintája](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Log tail minta
Lekérni a *n* használatával a partíció legutóbb hozzáadott entitásokat egy **RowKey** érték, amely fordított dátum és idő sorrendben rendezi.  

### <a name="context-and-problem"></a>Kontextus és probléma
Általános követelmény akkor tudja lekérni a legutóbb létrehozott entitások, például a 10 legújabb kiadás egy alkalmazott által küldött jogcímek. Tábla lekérdezése támogatási egy **$top** visszaadjon az első lekérdezés *n* bizonyos entitások: az utolsó n entitások vissza egy készlet nincs egyenértékű lekérdezési művelet.  

### <a name="solution"></a>Megoldás
Az entitások használatával Store egy **RowKey** , hogy természetes módon fordított dátum/idő ahhoz, így a legutóbbi bejegyzést használatával számos mindig kapcsolva az elsőt a táblában.  

Például, hogy egy alkalmazott által küldött tíz legújabb kiadási jogcímek lekérni, használhatja fordított osztásjelek származik az aktuális dátum/idő érték. Az alábbi C# kódmintát jeleníti meg a megfelelő "fordított órajel során végbemenő" értéket létrehozásának egyik módja egy **RowKey** , amelyek a legutóbbi rendezi a legrégebbi:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Visszatérhet a dátum idő értéknek megfelelően a következő kód használatával:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A lekérdezés a következőhöz hasonló:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A fordított osztásjelek értéket annak érdekében, hogy a karakterláncérték rendezi a várt módon nullákkal kell írni.  
* A skálázhatósági célokat az egy partíció szintjén tisztában kell lennie. Legyen óvatos a nem interaktív partíciót alakíthatnak ki.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha hozzá kell férnie az entitások fordított dátum/idő sorrend, illetve ha nemrégiben hozzáadott entitásokat eléréséhez szükséges.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Jogosultságokat / fűzze hozzá a kizárási minta](#prepend-append-anti-pattern)  
* [Entitások beolvasása](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Nagy mennyiségű delete minta
Egyidejű törlésre az entitásokat saját külön táblázatban szereplő fürtkonfigurációkat való tárolásával entitások nagy mennyiségű törlésének engedélyezése a tábla törlésével törli az entitások.  

### <a name="context-and-problem"></a>Kontextus és probléma
Számos alkalmazás törölje a régi adatok, amelyek már nem kell lennie az ügyfélalkalmazások számára elérhető, vagy az alkalmazás egy másik adattárolóra rendelkezik archivált. Az ilyen adatok egy dátum szerint általában azonosítása: például, hogy a követelmény, hogy az összes bejelentkezési kérelem, amelyek több mint 60 napnál régebbi bejegyzések törlése.  

Egy lehetséges tervezési dátuma és időpontja látható a bejelentkezési kérelem használandó a **RowKey**:  

![Dátum és idő bejelentkezési kísérlet](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Ezzel a módszerrel elkerülhető a partíció-elérési pontokhoz, mert a kérelem beszúrása, és minden felhasználót felvegyen egy külön partíciót a bejelentkezési entitások törlése. Ez a megközelítés azonban lehet, költségessé és időigényessé Ha már rendelkezik egy nagy mennyiségű entitást, mivel először ki kell ahhoz, hogy törli az entitások azonosítása egy tábla ellenőrzést hajt végre, és törölnie kell az egyes régi entitás. Vegye figyelembe, hogy adatváltások számát csökkentheti a kiszolgálóhoz, törölje a régi entitások kötegelés több delete kérelem be EGTs szükséges.  

### <a name="solution"></a>Megoldás
Minden nap, a bejelentkezési kísérletek samostatné tabulce használja. Beszúrt entitásokat, és most már egyszerűen törlése egy tábla minden nap adott esetben régi entitások törlése kritikus pontok elkerülése érdekében a fenti entitás kialakítás használhatja (Ez egy egyetlen tárolási művelet) keresése és több száz és több ezer személy törlése helyett bejelentkezési entitások minden nap.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Támogatja a Tervező egyéb módon az alkalmazás fogja használni az adatok, például az egyes entitásokhoz más adatokat, vagy létrehozni összesített adatokat linking keresésekor?  
* Nem a Tervező túlzott terhelés elkerülése új entitások beszúrásakor?  
* Késleltetés várható, ha azt szeretné, használja ugyanazt a táblanevet törlését követően. Célszerűbb mindig használja az egyedi táblák nevére.  
* Várható, egyes szabályozás első használatakor egy új táblát a Table service képes megtanulni az adathozzáférési mintáknak megfelelően, és elosztja a partíciók csomópontok között. Érdemes megfontolni, hogy milyen gyakran kell új táblák létrehozása.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha a létrehozott entitásokat, törölnie kell egy időben nagy mennyiségű.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Tranzakciók](#entity-group-transactions)
* [Entitások módosítása](#modifying-entities)  

## <a name="data-series-pattern"></a>Adatsorozat adatmintát
Teljes körű sorozat Store egyetlen entitás minimalizálása érdekében, hogy kérések száma.  

### <a name="context-and-problem"></a>Kontextus és probléma
Gyakran előfordul az alkalmazás számára az adatok általában egyszerre lekéréséhez szükséges sorozatát tárolja a rendszer. Például az alkalmazás előfordulhat, hogy rögzítse az összes alkalmazott által minden órában hány Csevegési üzeneteket, majd ezt az információt megrajzolásához hány üzenetet minden felhasználóhoz az előző 24 óra során küldött. Lehet, hogy egy tervezési 24 entitások tárolására minden alkalmazott számára:  

![24 entitások Store minden alkalmazott számára](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Ezzel a kialakítással egyszerűen keresse meg és frissítéséhez minden alkalmazott számára, amikor az alkalmazást az üzenetek száma érték frissíteni kell az entitás módosítására. Azonban és lekéri az információkat, a tevékenység-diagram megrajzolásához az előző 24 óra, le kell kérnie 24 entitásokat.  

### <a name="solution"></a>Megoldás
Egy külön tulajdonsággal a következő tervezési használatával tárolja az üzenetek száma óránként:  

![Üzenet stats entitás](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Ezzel a kialakítással az üzenetek száma egy alkalmazott frissíteni az adott órában használhatja egy merge művelet. Most kérheti le az összes kérelem használatával egyetlen entitás-diagram megrajzolásához szükséges információkat.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ha a teljes adatsorozat nem fér el egyetlen entitás (egy entitás legfeljebb 252 tulajdonságot is van), használja a egy alternatív adattárolóban, például egy blobot.  
* Ha egy entitás frissítése egyszerre több ügyfélnek, meg kell használnia a **ETag** megvalósításához az optimista egyidejűséget. Ha sok ügyfél, a versengés magas tapasztalhat.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, amikor szüksége van frissíteni, és a egy egyéni entitáshoz társított adatsor lekéréséhez.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Nagy entitások minta](#large-entities-pattern)  
* [Egyesítés vagy cseréje](#merge-or-replace)  
* [Végül konzisztens tranzakció minta](#eventually-consistent-transactions-pattern) (ha tárolja az adatsorozat blob)  

## <a name="wide-entities-pattern"></a>Széles körű entitások minta
Több fizikai entitás legfeljebb 252 tulajdonságot tartalmazhat rendelkező logikai entitás használni.  

### <a name="context-and-problem"></a>Kontextus és probléma
Egy egyéni entitás legfeljebb 252 tulajdonságot tartalmazhat (kivéve a kötelező tulajdonságai) is rendelkezik, és nem tárolható több mint 1 MB adatot összesen. Egy relációs adatbázisban általában számíthat egy sor mérete korlátozott round új tábla hozzáadásával és a egy 1 és 1 közötti kapcsolat kényszerítése.  

### <a name="solution"></a>Megoldás
A Table service használatával, több entitás legfeljebb 252 tulajdonságot tartalmazhat egy egyetlen nagy üzleti objektum képviselő is tárolhatja. Például ha szeretné tárolni az elmúlt 365 nap összes alkalmazott által küldött Csevegési üzenet darabszámát, az alábbi tervezési, amely két különböző sémákkal használhatja:  

![Több entitás](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Ha olyan módosítást igénylő, Újévi mindkét entitások frissítése szinkronizálja egymással kell egy EGT is használhatja. Ellenkező esetben egy egyetlen merge művelet használatával frissítse az üzenetek száma egy adott napjára. Összes adat lekéréséhez egyetlen alkalmazott le kell kérnie mindkét entitásban, amely két hatékony-kérelmeket is megteheti egy **PartitionKey** és a egy **RowKey** értéket.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A kész logikai entitás beolvasása magában foglalja a legalább két tárelérési tranzakciók: egy minden fizikai entitás lekéréséhez.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát mikor kell entitásokat, amelynek mérete vagy a tulajdonságok száma túllépi a korlátot, egy egyéni entitás a Table service szolgáltatásban.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Tranzakciók](#entity-group-transactions)
* [Egyesítés vagy cseréje](#merge-or-replace)

## <a name="large-entities-pattern"></a>Nagy entitások minta
A blob storage segítségével nagyméretű tulajdonságértékek tárolnia.  

### <a name="context-and-problem"></a>Kontextus és probléma
Egy egyéni entitás nem tárolja az adatok több mint 1 MB összesen. Ha egy vagy több, a Tulajdonságok tárolja az értékeket, amelyeket a teljes mérete meghaladja ezt az értéket az entitást, az egész entitás nem tárolható a Table service szolgáltatásban.  

### <a name="solution"></a>Megoldás
Ha az entitás meghaladja 1 MB méretű, mert egy vagy több tulajdonságának egy nagy mennyiségű adatot tartalmaznak, adatokat tárolni a Blob service-ben, és majd tárolni a blob címét az entitásban található egy tulajdonság. Például a fénykép egy alkalmazott tárolni a blob storage és tárolására is használható a fényképet mutató hivatkozást a **fénykép** az alkalmazott entitás tulajdonságát:  

![Fénykép tulajdonság](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az entitás a Table service-ben és a Blob service-ben az adatok végleges konzisztensek maradjanak, használja a [végül konzisztens tranzakció minta](#eventually-consistent-transactions-pattern) az entitások fenntartásához.
* Legalább két tárelérési tranzakciók teljes entitásnak beolvasása foglalja magában: az egyik beolvasni az entitást és a egy blob adatok lekéréséhez.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha az entitások, amelyek mérete túllépi a korlátot, egy egyéni entitás a Table service szolgáltatásban tárolni kívánt.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakció mintája](#eventually-consistent-transactions-pattern)  
* [Széles körű entitások minta](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Kizárási minta illesztenie hozzáfűzése
Növelhető a méretezhetőséget, ha Beszúrások nagy mennyiségű között osztja szét a beszúrásokat több partícióval rendelkezik.  

### <a name="context-and-problem"></a>Kontextus és probléma
Előtag-beillesztés vagy entitások hozzáfűzi a tárolt entitások jellemzően az alkalmazás új entitásokat ad hozzá a partíciók egymást követő első vagy utolsó partíciót eredményez. Ebben az esetben az összes adott időpontban Beszúrások zajlik ugyanazon a partíción egy interaktív, amely megakadályozza a table service, a Beszúrás terheléselosztás több csomópontra, és ami miatt a skálázási célértékei eléri az alkalmazás létrehozása a partíció. Például ha egy alkalmazás, amely hálózati naplókat és az erőforrás eléréséhez az alkalmazottak által, majd egy entitásstruktúrát, ahogy az alábbi eredményezheti kritikus ponttá váljon, ha a tranzakciók mennyisége eléri a skálázhatósági célok, az a jelenlegi órán partíció egy az egyes partíció:  

![Entitásstruktúrát](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Megoldás
A következő alternatív entitásstruktúrát egy adott partícióra forgalmas elkerülhető, az alkalmazás eseményeket:  

![Alternatív entitásstruktúrát](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Figyelje meg, az ebben a példában hogyan mindkét a **PartitionKey** és **RowKey** összetett kulcs. A **PartitionKey** a részleg és a alkalmazott Azonosítót használja a naplózás szét a több partíciót.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Támogatja az alternatív struktúra, amely hatékonyan létrehozása a Beszúrás a forró partíciók a lekérdezések, az ügyfél alkalmazás?  
* A várt mennyiségű tranzakciót jelent, hogy várhatóan éri el az egyes partíciók a skálázhatósági célokat, és a storage szolgáltatás szabályozottan?  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
A tranzakciók mennyisége nagy eséllyel eredményez a gyakori elérésű partíció használatakor a társzolgáltatás által szabályozás elkerülése érdekében a prepend hozzáfűzése kizárási minta.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs minta](#compound-key-pattern)  
* [Log tail minta](#log-tail-pattern)  
* [Entitások módosítása](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Napló adatok kizárási minta
Általában használjon a Blob service helyett a Table service Teljesítménynapló-adatok tárolására.  

### <a name="context-and-problem"></a>Kontextus és probléma
Egy közös használati eset, a naplóadatok beolvasni egy kijelölt naplóbejegyzések adott dátum/idő különböző: például szeretné az összes hiba és kritikus üzeneteihez, amelyeket az alkalmazás naplóz, 15:04 és a egy adott dátumon 15:06 között. Nem szeretné, hogy a dátum és idő a naplóüzenet segítségével meghatározhatja a partíció log entitások mentése:, amely az okozza, gyakori elérésű a partíción egy adott időpontban minden napló entitások oszt azonos **PartitionKey** érték (lásd a a szakasz [kizárási minta Prepend hozzáfűzése](#prepend-append-anti-pattern)). Például a következő entitás sémát egy naplófájlüzenetre eredménye egy gyakran használt adatok partíciót, mert az alkalmazás összes naplóüzenetek ír a partíció az aktuális dátum és óra:  

![Napló üzenet entitás](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

Ebben a példában a **RowKey** dátuma és időpontja, győződjön meg arról, hogy naplóüzenetek tárolása dátum és idő sorrendben rendezve log üzenet tartalmazza, az egy Üzenetazonosító abban az esetben, ha több naplóüzenetek ugyanazt a dátum és idő megosztani.  

Másik megoldás használja, egy **PartitionKey** , amely biztosítja, hogy az alkalmazás partíciók számos üzeneteket ír. Például ha a forrás a naplóüzenet lehetővé teszi a sok partíciók között az üzenetek továbbítása, a következő entitás sémáján használhatja:  

![Alternatív log üzenet entitás](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Ebben a sémában a probléma viszont, hogy egy adott időtartam összes a naplózott üzeneteket beolvasni, kell megkeresni minden partícióján a táblában.

### <a name="solution"></a>Megoldás
Az előző szakasz kiemeli a problémát, próbálja meg használni a Table service naplóbejegyzések és javasolt két, nem megfelelő, tervek tárolására. Egy megoldás és annak kockázata között naplózási üzeneteket; gyenge teljesítményt forró partíciók vezetett a másik megoldás egy adott időtartam naplózott üzeneteket beolvasni a tábla minden partíció beolvasása a követelmény miatt gyenge lekérdezési teljesítmény eredményezett. Ilyen esetben jobb megoldást kínál a BLOB storage-ba, és ez az Azure Storage Analytics tárolja a naplózási adatokat gyűjt.  

Ez a szakasz ismerteti, hogyan Storage Analytics naplóadatokat blobtárhelyen tárolja, olyan bemutatásáért, ahogy ez a megközelítés, amely a címtartomány által általában lekérdezése adatok tárolásához.  

A Storage Analytics naplóüzenetek tagolt formátumú több blobokban tárolja. A tagolt formátumú megkönnyíti az ügyfélalkalmazás elemzése a log üzenetben található adatokat.  

A Storage Analytics elnevezési szabályait, amely lehetővé teszi, hogy keresse meg a blob blobok (vagy blobok), amelyek tartalmazzák a keresett, amelynek naplóüzenetek használ. Például a "queue/2014/07/31/1800/000001.log" nevű blobba naplóüzenetek kapcsolódik az kezdetén 18:00, 2014. július 31-ig. az üzenetsor-szolgáltatás tartalmazza. A "000001" azt jelzi, hogy ez az első naplófájlja ebben az időszakban. A Storage Analytics is az első és utolsó log üzenetek fájl részeként a blob metaadatait tárolja az időbélyegeket rögzíti. A blob storage lehetővé teszi a blobok tárolóban egy tartománynév előtagján alapuló keresse meg az API: keresse meg a blobok, amelyek a 18:00 kezdetén várólista naplózási adatokat tartalmazzák, használhatja az előtag "üzenetsor/2014/07/31/1800."  

Storage Analytics pufferek üzenetek naplózása belsőleg majd rendszeres időközönként frissíti a megfelelő blob vagy hoz létre egy újat a legújabb naplóbejegyzéseket kötegelt. Ez csökkenti az írások, végezze el a blob szolgáltatáshoz száma.  

Amikor azt fontolgatja, hogy egy hasonló megoldást a saját alkalmazásában, meg kell fontolnia kompromisszumot kötni a közötti megbízhatóság (minden naplóbejegyzés adatraktárba történő írás során a blob storage felépítésnek) és a költségek és a méretezhetőséget (pufferelés az alkalmazás és az írás a frissítések kezelése őket, hogy a blob storage kötegekben).  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
Ha tervezi particionálni Teljesítménynapló-adatok tárolására, vegye figyelembe a következőket:  

* Ha létrehoz egy Táblatervezés, amelyek kiküszöbölik az esetleges forró partíciók, előfordulhat, hogy hatékonyan nem tudja elérni a naplózási adatokat.  
* Az ügyfélnek Teljesítménynapló-adatok feldolgozásához, gyakran kell betölteni a sok rekordot.  
* Bár a naplóadatok gyakran strukturált, a blob storage jobb megoldás lehet.  

## <a name="implementation-considerations"></a>Implementálási szempontok
Ez a szakasz ismerteti a szempontot figyelembe kell vennie a a fentebbi szakaszokban leírt minták megvalósításának néhányat. Ez a szakasz a legtöbb C# nyelven írt példa, amely a Storage ügyféloldali kódtára (4.3.0 verzióban verzió idején összeállításakor) használja.  

## <a name="retrieving-entities"></a>Entitások beolvasása
A szakaszban leírt módon [lekérdezéséhez tervezési](#design-for-querying), a legtöbb hatékony lekérdezések az a pont lekérdezés. Azonban bizonyos esetekben szükség lehet beolvasni a több entitás. Ez a szakasz ismerteti az egyes közös megközelítés a Storage ügyféloldali kódtár használatával entitások beolvasása.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>A Storage ügyféloldali kódtár használatával pont lekérdezése
Pont lekérdezés végrehajtása a legegyszerűbb módja az, hogy használja a **lekéréséhez** tábla művelet, ahogyan az az alábbi C# kódrészlettel, amely lekéri az entitás egy **PartitionKey** érték "Értékesítés" és a egy  **RowKey** "212" érték:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Figyelje meg, hogyan vár az ebben a példában a az entitás típusú lekéri **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>LINQ használatával több entitás beolvasása
A Storage ügyféloldali kódtára a LINQ használatával, és a egy lekérdezés megadásával több entitás kérheti egy **ahol** záradékban. Egy tábla beolvasásával elkerülése érdekében meg kell adnia a **PartitionKey** érték a WHERE záradék esetében, és ha lehetséges a **rowkey tulajdonságok esetén** érték tábla és a partíció vizsgálatok elkerülése érdekében. A table service támogatja a korlátozott számú összehasonlító operátorok (nagyobb, mint nagyobb vagy egyenlő, kevesebb mint, kisebb vagy egyenlő, egyenlő és nem egyenlő) használata a WHERE záradékban. Az alábbi C# kódrészlet megkeresi a dolgozóknak amelynek utolsó nevének kezdő karaktere "B" (feltéve, hogy a **rowkey tulajdonságok esetén** tárolja a Vezetéknév) az értékesítési részleg (feltéve, hogy a **PartitionKey** tárolja a részleg neve):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Figyelje meg, hogyan a lekérdezés határoz meg, mindkét egy **rowkey tulajdonságok esetén** és a egy **PartitionKey** jobb teljesítmény biztosítása érdekében.  

A következő mintakód bemutatja a fluent API-val megfelelő funkciók (További információ a fluent API-k általában: [egy Fluent API-t tervezése ajánlott eljárásai](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> A minta ágyazza több **CombineFilters** módszerek közé tartozik a szűrési feltételek.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Egy lekérdezés által nagyszámú entitások beolvasása
Az optimális lekérdezési egy egyedi entitás alapján ad vissza egy **PartitionKey** érték és a egy **RowKey** értéket. Egyes forgatókönyvekben előfordulhat követelmény számos entitások visszaadása, ugyanazon a partíción vagy akár több partíciót.  

Ilyen esetekben mindig teljes mértékben tesztelje az alkalmazás teljesítményét.  

Egy lekérdezést a table service is visszaadhatnak maximum 1000 entitás egy időben, és előfordulhat, hogy öt másodpercenként legfeljebb végrehajtása. Ha az eredményhalmaz több mint 1000 olyan entitásokat tartalmaz, ha a lekérdezés nem számított öt másodpercen belül fejeződött be, vagy ha a lekérdezés a partíció határ átlép, a Table service az ügyfélalkalmazás a következő entitáshalmazt kérelem engedélyezéséhez egy folytatási tokent ad vissza. Hogyan a folytatási jogkivonatok munkahelyi kapcsolatos további információkért lásd: [lekérdezés időkorlátja és a tördelés](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Ha használja a Storage ügyféloldali kódtár, automatikusan kezelheti folytatási token az Ön számára, hogy entitásokat ad vissza a Table szolgáltatásból. Az alábbi C# kódmintát a Storage ügyféloldali kódtár használatával automatikusan kezeli a folytatási token, ha a table service ad vissza, a válasz:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Az alábbi C#-kódot a folytatási token explicit módon kezeli:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Az explicit módon a folytatási token, szabályozhatja, ha az alkalmazás kéri le a következő szegmenst az adatok. Például az ügyfélalkalmazás lehetővé teszi a felhasználóknak a táblában tárolt entitások között, ha egy felhasználó dönthet nem keresztül összes entitást lekérdezni a lekérdezés által, így az alkalmazás csak használja a folytatási kód lekérése a következő lapon mikor szegmens a felhasználó befejeződött volna az aktuális szegmens lévő összes entitáshoz a lapozást. Ez a megközelítés több előnye van:  

* Ez lehetővé teszi, hogy korlátozza az adatokat kell beolvasni a Table szolgáltatásból, és hogy áthelyeznie a hálózaton keresztül.  
* Ez lehetővé teszi, hogy aszinkron i/o végrehajtása a .NET-ben.  
* Lehetővé teszi, hogy szerializálni a folytatási kód állandó tárolókba, így egy alkalmazás összeomlása esetén is.  

> [!NOTE]
> A folytatási kód általában 1000 entitásokat tartalmazó szegmens adja vissza, bár kevesebb lehet. Ez is így, ha a lekérdezés visszaadja a bejegyzések számának korlátozásához **igénybe** , az első n entitások visszaadása, amelyek megfelelnek a keresési feltételeknek: a table service is visszaadhatnak kevesebb, mint az n entitásokat tartalmazó szegmens egy folytatási kód ahhoz, hogy a fennmaradó entitások beolvasása.  
> 
> 

Az alábbi C#-kód bemutatja, hogyan módosíthatja egy szegmens található entitások száma:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Kiszolgálóoldali leképezése
Egyetlen entitás legfeljebb 255 tulajdonságok és a legfeljebb 1 MB méretű lehet. Ha lekérdezéséhez a táblából, és kérje le az entitásokat, előfordulhat, hogy nem kell az összes tulajdonság, és tudja kerülni a szükségtelenül (a késés és a költségek csökkentése érdekében) történő adatátvitel. Kiszolgálóoldali leképezés használatával átvitele csak a szükséges tulajdonságokat. Az alábbi példa lekéri az csak a **E-mail** tulajdonság (az **PartitionKey**, **rowkey tulajdonságok esetén**, **időbélyeg**, és **ETag**), a lekérdezés által kiválasztott entitások.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Figyelje meg a **RowKey** érték érhető el, annak ellenére, hogy nem tartalmazott tulajdonságokat lekérni listájában.  

## <a name="modifying-entities"></a>Entitások módosítása
A Storage ügyféloldali kódtára lehetővé teszi a beszúrásához, törlése és frissítése az entitások által a table service szolgáltatásban tárolt entitások módosítása. EGTs segítségével több insert, update és delete műveletek együtt, hogy csökkentse a szükséges adatváltások számát a batch és a megoldás a teljesítmény javítása.  

Vegye figyelembe, hogy amikor a Storage ügyféloldali kódtára végrehajtja az EGT általában fellépő kivételek az entitást, amely miatt sikertelen a kötegelt indexét. Ez akkor hasznos, ha a hibakeresés EGTs használó kódot.  

Azt is figyelembe kell venni, hogyan a kialakítás befolyásolja, hogyan kezeli az ügyfélalkalmazás az egyidejűség és a frissítési művelet.  

### <a name="managing-concurrency"></a>Az egyidejűség kezelése
Alapértelmezés szerint a table service valósítja meg az optimista egyidejűség ellenőrzi az egyéni entitások szintjén **beszúrása**, **egyesítése**, és **törlése** operations, bár azt a table service az ellenőrzések megkerülésére kényszerítése ügyfél lehetőség. Hogyan kezeli a table service az egyidejűségi kapcsolatos további információkért lásd: [egyidejűség kezelése a Microsoft Azure Storage](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Egyesítés vagy cseréje
A **cseréje** módszere a **TableOperation** osztály mindig váltja fel a teljes entitásnak a Table service szolgáltatásban. Ha nem adja meg egy tulajdonságot a kérelem a tulajdonságot az entitásban tárolt meglévő, a kérelem tárolt entitás eltávolítja a tulajdonságot. Kivéve, ha el kívánja távolítani egy tulajdonság explicit módon, egy tárolt entitásból, a kérésben meg kell adnia minden egyes tulajdonság.  

Használhatja a **egyesítése** módszere a **TableOperation** osztályban, amelyeket elküldhet a Table service, amikor frissíti egy entitás adatmennyiség csökkentése érdekében. A **egyesítése** metódus az entitásban tárolt tulajdonságokat lecseréli az a entitás a kérésben szereplő tulajdonságértékek, de semmilyen tulajdonságot, amely a kérelem nem tartalmazza az entitásban tárolt érintetlenül hagyja. Ez akkor hasznos, ha nagy entitások, és csak a kérelem tulajdonságok kis számú frissíteni kell.  

> [!NOTE]
> A **cseréje** és **egyesítése** módszer sem, ha az entitás nem létezik. Alternatív megoldásként használhatja a **InsertOrReplace** és **InsertOrMerge** módszer, amely létrehoz egy új entitást, ha még nem létezik.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Heterogén entitástípusok az dolgozik
A Table Service egy *séma nélküli* táblatárolóba, amely azt jelenti, hogy egyetlen tábla tárolhatja a tervezés nagyfokú rugalmasságot biztosító több típusú entitásokat. Az alábbi példa azt mutatja be, egy tábla, alkalmazott és a részleg entitások tárolására:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Időbélyeg</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Részleg neve</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Vegye figyelembe, hogy továbbra is rendelkeznie kell minden entitás **PartitionKey**, **rowkey tulajdonságok esetén**, és **időbélyeg** értékeket, de előfordulhat, hogy bármely tulajdonságkészlettel rendelkezik. Ezen kívül nincs semmit nem kell egy entitás típusát jelzi, ha úgy dönt, hogy valahol, hogy az adatok tárolására. Kétféle entitás típusának azonosításához:  

* Az entitástípus illesztenie a **rowkey tulajdonságok esetén** (vagy akár a **PartitionKey**). Ha például **EMPLOYEE_000123** vagy **DEPARTMENT_SALES** , **RowKey** értékeket.  
* Egy külön tulajdonság segítségével rögzítheti a entitástípus, az alábbi táblázatban látható módon.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Időbélyeg</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>Részleg neve</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Részleg</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Az entitás előtag-beillesztés, az első lehetőség írja be a **RowKey**, akkor hasznos, ha lehetséges, hogy két különböző típusú entitás lehet ugyanazt a kulcsérték. Csoportosítja a együtt, a partíció azonos típusú entitásokat is.  

Ebben a szakaszban leírt technikákkal különösen fontosak, vitára [öröklési kapcsolatok](table-storage-design-modeling.md#inheritance-relationships) ebben az útmutatóban a cikk korábbi [kapcsolatok modellezését](table-storage-design-modeling.md).  

> [!NOTE]
> Fontolja meg egy verziószámot beleértve entitás típusa érték lehetővé teszik az ügyfél alkalmazások fejlesztése a csatlakoztathatóság érdekében POCO objektumok és más verziókkal működik.  
> 
> 

Ez a szakasz további része a Storage ügyféloldali kódtár, amely ugyanabban a táblában több entitástípusok használatának megkönnyítése érdekében funkcióit ismerteti.  

### <a name="retrieving-heterogeneous-entity-types"></a>Heterogén entitástípusok beolvasása
Ha használja a Storage ügyféloldali kódtár, akkor három lehetőség több entitás típusok kezelése.  

Ha tudja, hogy az entitást, tárolja és a egy adott típusú **rowkey tulajdonságok esetén** és **PartitionKey** értékek, akkor az előző két példában látható módon az entitás kikeresésekor a entitástípus megadhatja, hogy kérje le az entitásokat típusú **EmployeeEntity**: [a Storage ügyféloldali kódtár használatával pont lekérdezést végrehajtó](#executing-a-point-query-using-the-storage-client-library) és [beolvasása a LINQ használatával több entitás](#retrieving-multiple-entities-using-linq).  

A második lehetőség a **DynamicTableEntity** típusa (egy tulajdonságcsomagot) helyett egy konkrét POCO entitás típusa (ezt a lehetőséget is javíthatja a teljesítményt, mert nem kell szerializálható és deszerializálható az entitás a .NET-típusok). Az alábbi C#-kód potenciálisan több különböző típusú entitás beolvasása a táblából, de adja vissza minden entitás, **DynamicTableEntity** példányok. Ezután a **EntityType** tulajdonságot minden entitás típusának meghatározása:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Vegye figyelembe, hogy beolvasni a más tulajdonságokat kell használnia a **TryGetValue** metódust a **tulajdonságok** tulajdonságát a **DynamicTableEntity** osztály.  

Egy harmadik lehetőség, hogy összevonhatja a **DynamicTableEntity** típusa és a egy **EntityResolver** példány. Ez lehetővé teszi, hogy ugyanabban a lekérdezésben többféle POCO feloldani. Ebben a példában a **EntityResolver** delegált használ a **EntityType** megkülönböztetni a két típusú entitás, amely a lekérdezés visszaadja az tulajdonság. A **megoldásához** metódus az **feloldó** megoldásához delegált **DynamicTableEntity** példányok az **TableEntity** példányok.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Heterogén entitástípusok módosítása
Nem kell tudni, hogy egy entitást, törölje azt a típusát, és mindig tudja, entitás típusa, amikor a meghajtóba. Használhatja azonban **DynamicTableEntity** írja be az entitások frissítésének jeho typu ismerete nélkül, és egy POCO entitásosztályt használata nélkül. A következő mintakód beolvassa egy entitás, és ellenőrzi a **EmployeeCount** frissítés előtti létezik tulajdonság.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

## <a name="controlling-access-with-shared-access-signatures"></a>Közös hozzáférésű jogosultságkódokkal hozzáférés szabályozása
Közös hozzáférésű Jogosultságkód (SAS) jogkivonatok segítségével lehetővé teszik az alkalmazások ügyfél anélkül, hogy a tárfiók kulcsát tartalmazzák a kódban kellene táblaentitások módosítása (és lekérdezése). Általában a SAS használatával az alkalmazás három fő előnyök:  

* Nem kell terjeszteni a tárfiók kulcsát egy nem biztonságos platformon (például mobiltelefonokon) annak érdekében, hogy az eszköz eléréséhez, és módosíthatja az entitásokat a Table service szolgáltatásban.  
* Webes és feldolgozói szerepkörök hajtsa végre az entitások, ügyféloldali eszközök, például a végfelhasználói számítógépek és mobileszközök kezeléséhez a munka részét kiszervezheti.  
* Hozzárendelhet egy korlátozott, és időt korlátozott engedélyek (például a csak olvasási hozzáféréssel az erőforrásoknál engedélyezése) egy ügyfélnek.  

A Table service SAS-jogkivonatok használatával kapcsolatos további információkért lásd: [használata közös hozzáférésű Jogosultságkódok (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Azonban továbbra is kell létrehoznia a SAS-tokeneket, amelyek biztosítanak a table service szolgáltatásban az entitások ügyfélalkalmazás: tegye ezt olyan környezetben, amely rendelkezik a tárfiók kulcsait a biztonságos hozzáférést. Általában használatával egy webes vagy feldolgozói szerepkör a SAS-jogkivonatokat hoz létre, és továbbítsa őket az ügyfélalkalmazások, amelyek az entitásokhoz való hozzáférés szükséges. Mivel van még egy terhelés létrehozása, és jusson el SAS-tokeneket az ügyfeleknek, hogyan érdemes érdemes lehet csökkenteni a terhelést, különösen nagy mennyiségű forgatókönyvekben részt.  

Hozzon létre egy SAS-token, amely hozzáférést biztosít az entitásokat egy tábla egy részét, lehetőség. Alapértelmezés szerint egy SAS-jogkivonatát egy teljes táblát hoz létre, de azt is adható meg, hogy a SAS-jogkivonat hozzáférést vagy számos **PartitionKey** értékeket, vagy számos **PartitionKey** és **RowKey** értékeket. Választhatja azt is, a rendszer az egyes felhasználók SAS-jogkivonatokat hoz létre, úgy, hogy minden felhasználó SAS-jogkivonat csak lehetővé teszi, hogy azokat a saját az entitásokhoz való hozzáférés a table service szolgáltatásban.  

## <a name="asynchronous-and-parallel-operations"></a>Párhuzamos és aszinkron műveletek
Amennyiben a kérelmek több partíción is szét, aszinkron vagy a párhuzamos lekérdezések használatával is javítható az átviteli sebesség és az ügyfél válaszképességét.
Előfordulhat például, hogy két vagy több feldolgozói szerepkör példányai a táblák párhuzamos eléréséhez. Az egyes feldolgozói szerepköröket partíciók adott részhalmazához felelős, vagy egyszerűen több példányban feldolgozói szerepkört, minden egyes érhessék el az összes partíciót egy tábla.  

Egy ügyfél példány belül úgy, hogy végrehajtja a tárolási műveletek aszinkron módon javíthatja a átviteli sebesség. A Storage ügyféloldali kódtára megkönnyíti az írási aszinkron lekérdezések és a módosításokat. Például, előfordulhat, hogy indítsa el a szinkron módszer, amely lekéri az összes entitást egy partíciót, ahogyan az az alábbi C#-kódot:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Egyszerűen módosíthatja ezt a kódot, így az alábbiak szerint aszinkron módon fut. a lekérdezés:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

Aszinkron példában láthatja a szinkron verzió a következő módosításokat:  

* A podpis metody mostantól tartalmazza a **aszinkron** módosító és értéket ad vissza egy **feladat** példány.  
* Hívása helyett a **ExecuteSegmented** metódussal lehet bekérni az eredményt, most már a metódus meghívja a **ExecuteSegmentedAsync** metódust, és használja a **await** módosító, aszinkron módon beolvasni az eredményeket.  

Az ügyfélalkalmazás is ez a metódus meghívható többször (értékeit a **részleg** paraméter), és a egy külön szál minden lekérdezés fog futni.  

Ne feledje, hogy van-e nem aszinkron verziója a **Execute** metódus az a **TableQuery** osztály, mert a **IEnumerable** felület nem támogatja a aszinkron enumerálása.  

Beszúrása, frissítése, és aszinkron módon entitások törlése. Az alábbi C# példa bemutatja egy egyszerű, a szinkron módszer beszúrása vagy alkalmazotti entitás cseréje:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Így a frissítés aszinkron módon fut. Ez a kód egyszerűen módosíthatja:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Aszinkron példában láthatja a szinkron verzió a következő módosításokat:  

* A podpis metody mostantól tartalmazza a **aszinkron** módosító és értéket ad vissza egy **feladat** példány.  
* Hívása helyett a **Execute** frissíteni az entitást, a metódus már metódus meghívja a **ExecuteAsync** metódust, és használja a **await** módosító eredmények lekéréséhez aszinkron módon történik.  

Az ügyfélalkalmazás több aszinkron metódusok ehhez hasonló meghívhatja, és minden egyes metódus meghívásának egy külön szál fog futni.  

## <a name="next-steps"></a>További lépések

- [Kapcsolatok modellezését](table-storage-design-modeling.md)
- [Lekérdezés tervezése](table-storage-design-for-query.md)
- [Táblák adatainak titkosítása](table-storage-design-encrypt-data.md)
- [Adatmódosítás tervezése](table-storage-design-for-modification.md)