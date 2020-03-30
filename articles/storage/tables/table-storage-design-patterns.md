---
title: Azure storage-tábla tervezési minták | Microsoft dokumentumok
description: Minták használata az Azure table service-megoldásokhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 5478163a6103bcc84b4f3608d7513c6e7cb11c01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529339"
---
# <a name="table-design-patterns"></a>Táblatervezési minták
Ez a cikk ismerteti a Table service-megoldások hoz használható néhány mintákat. Is, látni fogja, hogyan lehet gyakorlatilag kezelni néhány kérdést és kompromisszumot tárgyalt más táblázat tárolási tervezési cikkek. Az alábbi ábra összefoglalja a különböző minták közötti kapcsolatokat:  

![kapcsolódó adatok megkeresése](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


A fenti mintatérkép kiemeli az útmutatóban dokumentált minták (kék) és anti-minták (narancssárga) közötti kapcsolatokat. Vannak sok más minták, amelyeket érdemes megfontolni. A Table Service egyik legfontosabb forgatókönyve például a [Parancslekérdezési felelősség elkülönítése (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) minta [Materializált nézetminta](https://msdn.microsoft.com/library/azure/dn589782.aspx) használata.  

## <a name="intra-partition-secondary-index-pattern"></a>Partíción belüli másodlagos indexminta
Tárolja az egyes entitások több példányát különböző **RowKey-értékek** használatával (ugyanabban a partícióban) a gyors és hatékony keresések és az alternatív rendezési sorrendek különböző **RowKey-értékek** használatával történő lehetővé tétele érdekében. A másolatok közötti frissítések egységesek maradhatnak az EGT-k használatával.  

### <a name="context-and-problem"></a>Kontextus és probléma
A Table szolgáltatás automatikusan indexeli az entitásokat a **PartitionKey** és a **RowKey** értékek használatával. Ez lehetővé teszi, hogy egy ügyfélalkalmazás hatékonyan lekérje az entitást ezeket az értékeket. Az alábbi táblastruktúra használatával például egy ügyfélalkalmazás pontlekérdezéssel lekérheti az egyes alkalmazottentitásokat a részleg név és az alkalmazottazonosító (a **PartitionKey** és a **RowKey** értékek) használatával. Az ügyfél az egyes részlegeken belül alkalmazotti azonosító szerint rendezett entitásokat is lekérhet.

![Kép06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Ha azt is szeretné, hogy egy alkalmazott entitás értéke alapján egy másik tulajdonság, például az e-mail-cím, meg kell használni a kevésbé hatékony partíciós vizsgálat találni egyezést. Ennek az az oka, hogy a table service nem biztosít másodlagos indexeket. Ezenkívül nincs lehetőség az alkalmazottak listájának **a RowKey** sorrendtől eltérő sorrendben történő igénylésére.  

### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerüléséhez tárolhatja az egyes entitások több példányát minden egyes példánysal egy másik **RowKey** érték használatával. Ha az alábbi struktúrákkal rendelkező entitást tárol, hatékonyan lekérheti az alkalmazotti entitásokat az e-mail cím vagy az alkalmazottazonosító alapján. A **RowKey**, az "empid_" és a "email_" előtagértékei lehetővé teszik egyetlen alkalmazott vagy alkalmazotti tartomány lekérdezését e-mail címek vagy alkalmazottazonosítók széles választékának használatával.  

![Alkalmazotti entitások](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

A következő két szűrőfeltétel (az egyik az alkalmazottazonosító, a másik az e-mail-cím alapján keresfel) pontlekérdezéseket határoz meg:  

* $filter=(PartitionKey eq 'Sales') és (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') és (RowKey eq 'email_jonesj@contoso.com')  

Ha egy alkalmazotti entitástartományt kérdez, megadhatja az alkalmazotti azonosítósorrendben rendezett tartományt, vagy az e-mail címsorrendben rendezett tartományt a **RowKey**megfelelő előtaggal rendelkező entitások lekérdezésével.  

* A 000100 és 000199 közötti tartományban lévő alkalmazottazonosítóval rendelkező értékesítési osztály összes alkalmazottjának megkereséséhez használja a következőt: $filter=(PartitionKey eq 'Sales') és (RowKey ge 'empid_000100') és (RowKey le 'empid_000199')  
* Az értékesítési osztály összes alkalmazottjának megkeresése az "a" betűvel kezdődő e-mail címmel: $filter=(PartitionKey eq 'Sales') és (RowKey ge 'email_a') és (RowKey lt 'email_b')  
  
  A fenti példákban használt szűrőszintaxis a Table service REST API-ból származik, további információt a [Lekérdezésentitások](https://msdn.microsoft.com/library/azure/dd179421.aspx)című témakörben talál.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A táblázattárolás használata viszonylag olcsó, így az ismétlődő adatok tárolásának költsége nem lehet komoly probléma. Azonban mindig ki kell értékelnie a tervezési költsége alapján a várható tárolási követelmények, és csak adja hozzá az ismétlődő entitások, hogy támogassa a lekérdezéseket az ügyfélalkalmazás végrehajtja.  
* Mivel a másodlagos index entitások ugyanabban a partícióban vannak tárolva, mint az eredeti entitások, győződjön meg arról, hogy nem lépi túl az egyes partíciók méretezhetőségi céljait.  
* Az ismétlődő entitások konzisztensek maradhatnak egymással az EGT-k használatával az entitás két példányának atomi frissítéséhez. Ez azt jelenti, hogy egy entitás összes példányát ugyanabban a partícióban kell tárolnia. További információt az [Entitáscsoport-tranzakciók használata](table-storage-design.md#entity-group-transactions)című szakaszban talál.  
* A **RowKey** használt értéknek egyedinek kell lennie az egyes entitásoknál. Fontolja meg az összetett kulcsértékek használatát.  
* A **RowKey** numerikus értékeinek kitöltése (például a 000223-as alkalmazotti azonosító) lehetővé teszi a helyes rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül kell duplikálnia az entitás összes tulajdonságát. Ha például a **RowKey-ben** az e-mail címet használó entitásokat megtekintő lekérdezéseknek soha nem kell az alkalmazott életkora, ezek az entitások a következő struktúrával rendelkezhetnek:

   ![Alkalmazotti entitás szerkezete](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Általában jobb az ismétlődő adatok tárolása, és annak biztosítása, hogy egyetlen lekérdezéssel minden szükséges adatot betud olvasni, mint hogy egy entitást, egy másikat pedig a szükséges adatok kereséséhez használjon.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha az ügyfélalkalmazásnak különböző kulcsok használatával kell lekérnie az entitásokat, amikor az ügyfélnek különböző rendezési sorrendekben lévő entitásokat kell beolvasnia, és ahol az egyes entitásokat különböző egyedi értékek használatával azonosíthatja. Azonban győződjön meg arról, hogy nem lépi túl a partíció méretezhetőségi korlátait, amikor entitás-keres a különböző **RowKey** értékeket.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Partíciók közötti másodlagos indexminta](#inter-partition-secondary-index-pattern)
* [Összetett kulcsminta](#compound-key-pattern)
* Entitáscsoport tranzakciói
* [Heterogén entitástípusok kal való együttműködés](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Partíciók közötti másodlagos indexminta
Tárolja az egyes entitások több példányát különböző **RowKey-értékek** használatával külön partíciókon vagy külön táblákban, hogy lehetővé tegye a gyors és hatékony keresőket és az alternatív rendezési sorrendeket különböző **RowKey** értékek használatával.  

### <a name="context-and-problem"></a>Kontextus és probléma
A Table szolgáltatás automatikusan indexeli az entitásokat a **PartitionKey** és a **RowKey** értékek használatával. Ez lehetővé teszi, hogy egy ügyfélalkalmazás hatékonyan lekérje az entitást ezeket az értékeket. Az alábbi táblastruktúra használatával például egy ügyfélalkalmazás pontlekérdezéssel lekérheti az egyes alkalmazottentitásokat a részleg név és az alkalmazottazonosító (a **PartitionKey** és a **RowKey** értékek) használatával. Az ügyfél az egyes részlegeken belül alkalmazotti azonosító szerint rendezett entitásokat is lekérhet.  

![Alkalmazott azonosítója](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Ha azt is szeretné, hogy egy alkalmazott entitás értéke alapján egy másik tulajdonság, például az e-mail-cím, meg kell használni a kevésbé hatékony partíciós vizsgálat találni egyezést. Ennek az az oka, hogy a table service nem biztosít másodlagos indexeket. Ezenkívül nincs lehetőség az alkalmazottak listájának **a RowKey** sorrendtől eltérő sorrendben történő igénylésére.  

Nagy mennyiségű tranzakcióra számít ezekkel az entitásokkal szemben, és minimálisra szeretné csökkenteni az ügyfél szabályozásának kockázatát.  

### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerüléséhez tárolhatja az egyes entitások több példányát különböző **PartitionKey** és **RowKey** értékek használatával. Ha az alábbi struktúrákkal rendelkező entitást tárol, hatékonyan lekérheti az alkalmazotti entitásokat az e-mail cím vagy az alkalmazottazonosító alapján. A **PartitionKey**, "empid_" és "email_" előtagértékei lehetővé teszik a lekérdezéshez használni kívánt index azonosítását.  

![Elsődleges index és másodlagos index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


A következő két szűrőfeltétel (az egyik az alkalmazottazonosító, a másik az e-mail-cím alapján keresfel) pontlekérdezéseket határoz meg:  

* $filter=(PartitionKey eq 'empid_Sales') és (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') és (RowKey eq 'jonesj@contoso.com')  

Ha egy alkalmazotti entitástartományt kérdez, megadhatja az alkalmazotti azonosítósorrendben rendezett tartományt, vagy az e-mail címsorrendben rendezett tartományt a **RowKey**megfelelő előtaggal rendelkező entitások lekérdezésével.  

* Az értékesítési osztály összes alkalmazottjának megkeresése a **000100–000199** közötti értékben, az alkalmazotti azonosító rendelés szerint rendezve: $filter=(PartitionKey eq "empid_Sales") és (RowKey ge '000100') és (RowKey le '000199') **000199**  
* Ha az értékesítési osztály összes alkalmazottját meg szeretné találni egy e-mail címmel kezdődő e-mail címmel, amely az e-mail cím sorrendjében kezdődik: $filter=(PartitionKey eq 'email_Sales') és (RowKey ge 'a') és (RowKey lt 'b')  

A fenti példákban használt szűrőszintaxis a Table service REST API-ból származik, további információt a [Lekérdezésentitások](https://msdn.microsoft.com/library/azure/dd179421.aspx)című témakörben talál.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az ismétlődő entitások végül konzisztensek maradhatnak egymással az elsődleges és másodlagos indexentitások karbantartásához a [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern) használatával.  
* A táblázattárolás használata viszonylag olcsó, így az ismétlődő adatok tárolásának költsége nem lehet komoly probléma. Azonban mindig ki kell értékelnie a tervezési költsége alapján a várható tárolási követelmények, és csak adja hozzá az ismétlődő entitások, hogy támogassa a lekérdezéseket az ügyfélalkalmazás végrehajtja.  
* A **RowKey** használt értéknek egyedinek kell lennie az egyes entitásoknál. Fontolja meg az összetett kulcsértékek használatát.  
* A **RowKey** numerikus értékeinek kitöltése (például a 000223-as alkalmazotti azonosító) lehetővé teszi a helyes rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül kell duplikálnia az entitás összes tulajdonságát. Ha például a **RowKey-ben** az e-mail címet használó entitásokat megtekintő lekérdezéseknek soha nem kell az alkalmazott életkora, ezek az entitások a következő struktúrával rendelkezhetnek:
  
   ![Alkalmazotti entitás (másodlagos index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Általában jobb az ismétlődő adatok tárolása, és annak biztosítása, hogy egyetlen lekérdezéssel minden szükséges adatot lelehessen olvasni, mint hogy egy lekérdezést használjon egy entitás megkereséséhez a másodlagos index használatával, és egy másikat a szükséges adatok elsődleges indexben való kereséséhez.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha az ügyfélalkalmazásnak különböző kulcsok használatával kell lekérnie az entitásokat, amikor az ügyfélnek különböző rendezési sorrendekben lévő entitásokat kell beolvasnia, és ahol az egyes entitásokat különböző egyedi értékek használatával azonosíthatja. Akkor használja ezt a mintát, ha el szeretné kerülni a partíció méretezhetőségi korlátainak túllépését, amikor entitás-keresőket hajt végre a különböző **RowKey-értékek** használatával.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern)  
* [Partíción belüli másodlagos indexminta](#intra-partition-secondary-index-pattern)  
* [Összetett kulcsminta](#compound-key-pattern)  
* Entitáscsoport tranzakciói  
* [Heterogén entitástípusok kal való együttműködés](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Végül konzisztens tranzakciók minta
Engedélyezze a partícióhatárokon vagy a tárolórendszer-határokon keresztül imfeonos viselkedést az Azure-várólisták használatával.  

### <a name="context-and-problem"></a>Kontextus és probléma
EGT-k lehetővé teszik az atomi tranzakciók több entitások, amelyek ugyanazt a partíciókulcsot. A teljesítmény és a méretezhetőség miatt dönthet úgy, hogy a konzisztenciakövetelményekkel rendelkező entitásokat külön partíciókban vagy külön tárolórendszerben tárolja: ilyen esetben nem használhatja az EGT-ket a konzisztencia fenntartásához. Előfordulhat például, hogy a következők között fenn kell tartania a végleges konzisztenciát:  

* Entitások két különböző partíciók ugyanabban a táblában, különböző táblákban vagy különböző tárfiókokban tárolt.  
* A Table szolgáltatásban tárolt entitás és a Blob szolgáltatásban tárolt blob.  
* A Table szolgáltatásban tárolt entitás és egy fájl egy fájlrendszerben.  
* A Table szolgáltatásban tárolt entitás, amelyet az Azure Cognitive Search szolgáltatás használatával még indexelnek.  

### <a name="solution"></a>Megoldás
Az Azure-várólisták használatával olyan megoldást valósíthat meg, amely két vagy több partíció vagy tárolórendszer végső konzisztenciáját biztosítja.
Ennek a megközelítésnek a szemléltetéséhez tegyük fel, hogy a régi alkalmazotti entitások archiválásához szükséges követelmény. A régi munkavállalói entitásokat ritkán kérdezik le, és ki kell zárni őket minden olyan tevékenységből, amely a jelenlegi alkalmazottakkal foglalkozik. Ennek a követelménynek a megvalósításához az aktív alkalmazottakat az **Aktuális** táblában, a régi alkalmazottakat pedig az **Archiválás** táblában tárolja. Egy alkalmazott archiválásához törölnie kell az entitást az **aktuális** táblából, és hozzá kell adnia az entitást az **Archiválás** táblához, de nem használhat EGT-t e két művelet végrehajtásához. Annak elkerülése érdekében, hogy egy hiba miatt egy entitás mindkét vagy egyik táblában is megjelenjen, az archiválási műveletnek végül konzisztensnek kell lennie. A következő szekvenciadiagram a művelet lépéseit ismerteti. A következő szövegben a kivételelérési utak további részletekkel szolgálnak.  

![Azure-várólisták megoldás](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Az ügyfél úgy kezdeményezi az archiválási műveletet, hogy egy üzenetet helyez el egy Azure-várólistán, ebben a példában az alkalmazottak #456 archiválására. A dolgozói szerepkör lekérdezi az új üzenetek várólistáját; ha talál egyet, beolvassa az üzenetet, és egy rejtett másolatot hagy a várólistán. A munkavégző szerepkör ezután lekéri az entitás egy példányát az **Aktuális** táblából, beszúr egy másolatot az **Archiválás** táblába, majd törli az eredetit az **Aktuális** táblából. Végül, ha nem voltak hibák az előző lépésekből, a feldolgozói szerepkör törli a rejtett üzenetet a várólistából.  

Ebben a példában a 4. **Archive** Hozzáadhat az alkalmazottat egy blobhoz a Blob szolgáltatásban vagy egy fájlfájlhoz egy fájlrendszerben.  

### <a name="recovering-from-failures"></a>Helyreállítás a hibákból
Fontos, hogy a **4.** **5** *idempotent* Ha a Table szolgáltatást használja, a **4.** az **5.** Ha másik tárolórendszert használ, megfelelő idempotens műveletet kell használnia.  

Ha a dolgozói szerepkör soha nem fejezi be a **6.** A feldolgozói szerepkör ellenőrizheti, hogy a várólistán lévő üzenetek et hányszor olvasták el, és szükség esetén megjelölheti, hogy "méreg" üzenet a vizsgálathoz egy külön várólistába küldésével. A várólista-üzenetek olvasásáról és a várólista-szám ellenőrzéséről az [Üzenetek beolvasása](https://msdn.microsoft.com/library/azure/dd179474.aspx)című témakörben talál további információt.  

A tábla- és várólista-szolgáltatások néhány hiba átmeneti hiba, és az ügyfélalkalmazásnak megfelelő újrapróbálkozási logikát kell tartalmaznia azok kezeléséhez.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ez a megoldás nem biztosítja a tranzakciók elkülönítését. Például egy ügyfél elolvashatja az **Aktuális** és **az Archiválás** táblákat, amikor a feldolgozói szerepkör a **4.** **5** Az adatok végül konzisztensek lesznek.  
* A végleges konzisztencia biztosítása érdekében győződjön meg arról, hogy a 4.  
* A megoldás több várólisták és feldolgozói szerepkör-példányok használatával méretezhető.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha garantálni szeretné a különböző partíciókban vagy táblákban lévő entitások közötti végleges konzisztenciát. Ezt a mintát kiterjesztheti a table szolgáltatás és a Blob szolgáltatás és más nem Azure Storage-adatforrások, például az adatbázis vagy a fájlrendszer műveleteinek végleges konzisztenciájának biztosítása érdekében.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* Entitáscsoport tranzakciói  
* [Egyesítés vagy csere](#merge-or-replace)  

> [!NOTE]
> Ha a tranzakcióelkülönítés fontos a megoldás számára, fontolja meg a táblák újratervezését az EGT-k használatához.  
> 
> 

## <a name="index-entities-pattern"></a>Entitások indexelési mintája
Indexentitások karbantartása az entitások listájának visszaadását tartalmazó hatékony keresések engedélyezéséhez.  

### <a name="context-and-problem"></a>Kontextus és probléma
A Table szolgáltatás automatikusan indexeli az entitásokat a **PartitionKey** és a **RowKey** értékek használatával. Ez lehetővé teszi, hogy egy ügyfélalkalmazás hatékonyan lekérje az entitást egy pontlekérdezés használatával. Az alábbi táblastruktúra használatával például egy ügyfélalkalmazás hatékonyan lekérheti az egyes alkalmazotti entitásokat a részleg név és az alkalmazottazonosító (a **PartitionKey** és a **RowKey)** használatával.  

![Alkalmazotti entitás](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Ha azt is szeretné, hogy egy másik, nem egyedi tulajdonság, például a vezetéknév értéke alapján letudja olvasni az alkalmazottentitások listáját, kevésbé hatékony partícióvizsgálatot kell használnia az egyezések kereséséhez, nem pedig index használatával közvetlenül keresni őket. Ennek az az oka, hogy a table service nem biztosít másodlagos indexeket.  

### <a name="solution"></a>Megoldás
Ha engedélyezni szeretné a vezetéknév alapján történő keresmel a fent látható entitásstruktúrával, meg kell őriznie az alkalmazottazonosítók listáját. Ha egy adott vezetéknévvel rendelkező alkalmazotti entitásokat (például Jones) szeretne beolvasni, először meg kell keresnie az alkalmazottak alkalmazotti azonosítóinak listáját, amelynek vezetékneve Jones, majd beolvasnia ezeket az alkalmazottentitásokat. Az alkalmazotti azonosítók listájának tárolására három fő lehetőség van:  

* Blob storage használata.  
* Indexentitások létrehozása ugyanabban a partícióban, mint az alkalmazott entitások.  
* Indexentitások létrehozása külön partíción vagy táblában.  

<u>#1. lehetőség: Blob storage használata</u>  

Az első lehetőség, hozzon létre egy blobot minden egyedi vezetéknév, és minden blob tárolja a **PartitionKey** (részleg) és **rowkey** (alkalmazotti azonosító) értékek listáját az alkalmazottak, amelyek az adott vezetéknév. Amikor hozzáad vagy töröl egy alkalmazottat, győződjön meg arról, hogy a megfelelő blob tartalma végül konzisztens az alkalmazottentitásokkal.  

<u>Opció #2:</u> Indexentitások létrehozása ugyanabban a partícióban  

A második lehetőségnél használjon indexentitásokat, amelyek a következő adatokat tárolják:  

![Alkalmazotti index entitása](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

Az **EmployeeIDs** tulajdonság a **RowKey**-ben tárolt vezetéknévvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját tartalmazza.  

A következő lépések felvázolják azt a folyamatot, amelyet új alkalmazott hozzáadásakor kell követnie, ha a második lehetőséget használja. Ebben a példában egy 000152-es azonosítóval rendelkező alkalmazottat és egy Jones vezetéknevű alkalmazottat veszünk fel az értékesítési osztályba:  

1. Az "Értékesítés" **PartitionKey** értékkel és a **"Jones" RowKey** értékkel rendelkező indexentitás lekérése. Mentse az entitás ETag-jét a 2.  
2. Hozzon létre egy entitáscsoport-tranzakciót (azaz kötegműveletet), amely beszúrja az új alkalmazottentitást (**PartitionKey** értéke "Sales" és "000152" **RowKey** érték), és frissíti az indexentitást (**PartitionKey** értéke "Sales" és **"Jones" RowKey** érték) az új alkalmazottazonosító hozzáadásával az EmployeeIDs mező listájához. Az entitáscsoport-tranzakciókról az Entitáscsoport tranzakcióiról további információt az Entitáscsoport-tranzakciók című témakörben talál.  
3. Ha az entitáscsoport tranzakciója optimista egyidejűségi hiba miatt sikertelen (valaki más éppen módosította az indexentitást), akkor újra kell kezdenie az 1.  

Hasonló megközelítést használhat az alkalmazott törléséhez, ha a második lehetőséget használja. Az alkalmazott vezetéknevének módosítása valamivel összetettebb, mivel olyan entitáscsoport-tranzakciót kell végrehajtania, amely három entitást frissít: az alkalmazott entitást, a régi vezetéknév indexentitását és az új vezetéknév indexentitását. A módosítások végrehajtása előtt be kell olvasnia az egyes entitásokat, hogy lekérhesse azokat az ETag értékeket, amelyek segítségével optimista egyidejűséghasználatával hajthatja végre a frissítéseket.  

A következő lépések felvázolják azt a folyamatot, amelyet akkor kell követnie, ha a második lehetőség használata esetén meg kell keresnie az összes alkalmazottat egy adott vezetéknévvel egy osztályon. Ebben a példában az értékesítési osztályban a Jones vezetéknevű összes alkalmazottat keressük:  

1. Az "Értékesítés" **PartitionKey** értékkel és a **"Jones" RowKey** értékkel rendelkező indexentitás lekérése.  
2. Elemezje az alkalmazottazonosítók listáját az Alkalmazottazonosítók mezőben.  
3. Ha további információra van szüksége az egyes alkalmazottakról (például az e-mail címükről), olvassa be az egyes alkalmazottentitásokat a **PartitionKey** "Sales" érték és a **RowKey** értékek használatával a 2.  

<u>#3 lehetőség:</u> Indexentitások létrehozása külön partíción vagy táblában  

A harmadik lehetőségnél használjon indexentitásokat, amelyek a következő adatokat tárolják:  

![Alkalmazotti index entitás külön partíción](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


Az **EmployeeIDs** tulajdonság a **RowKey**-ben tárolt vezetéknévvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját tartalmazza.  

A harmadik beállítással nem használhatja az EGT-ket a konzisztencia fenntartásához, mert az indexentitások az alkalmazott entitásoktól különálló partíción vannak. Győződjön meg arról, hogy az index entitások végül összhangban vannak az alkalmazottentitásokkal.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ehhez a megoldáshoz legalább két lekérdezésre van szükség az egyező entitások lekéréséhez: az egyik az indexentitások lekérdezéséhez a **RowKey-értékek** listájának beolvasásához, majd a lista minden egyes entitásának lekéréséhez lekérdezéseket igényel.  
* Mivel az egyes entitások maximális mérete 1 MB, a megoldásban #2 és #3 lehetőség feltételezi, hogy bármely adott vezetéknév alkalmazotti azonosítóinak listája soha nem haladja meg az 1 MB-ot. Ha az alkalmazotti azonosítók listája valószínűleg nagyobb, mint 1 MB méretű, használja a #1 beállítást, és tárolja az indexadatokat a blob storage-ban.  
* Ha #2 opciót használ (EGTs használatával kezeli az alkalmazottak hozzáadását és törlését, valamint az alkalmazott vezetéknevének módosítását), ki kell értékelnie, hogy a tranzakciók mennyisége megközelíti-e a méretezhetőségi korlátokat egy adott partíción. Ebben az esetben érdemes egy végül konzisztens megoldást (#1 vagy #3 beállítás), amely várólistákat használ a frissítési kérelmek kezeléséhez, és lehetővé teszi az indexentitások tárolására az alkalmazott entitások tól elkülönített partíción.  
* A megoldásban #2 lehetőség azt feltételezi, hogy egy részlegen belül vezetéknév szerint szeretne keresni: például jones vezetéknevű alkalmazottak listáját szeretné beolvasni az értékesítési részlegben. Ha azt szeretné, hogy az összes jones vezetéknevű alkalmazott at a teljes szervezetben megkeresse, használja #1 vagy #3.
* Létrehozhat egy várólista-alapú megoldást, amely biztosítja a végleges konzisztenciát (további részletekért tekintse meg a [Végül konzisztens tranzakciók mintáját).](#eventually-consistent-transactions-pattern)  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha olyan entitásokat szeretne keresni, amelyek közös tulajdonságértékkel rendelkeznek, például a Jones vezetéknevű összes alkalmazottat.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcsminta](#compound-key-pattern)  
* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern)  
* Entitáscsoport tranzakciói  
* [Heterogén entitástípusok kal való együttműködés](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Denormalizációs minta
A kapcsolódó adatokat egyetlen entitásban kombinálva lehetővé teszi az összes szükséges adat egyetlen pontos lekérdezéssel történő beolvasását.  

### <a name="context-and-problem"></a>Kontextus és probléma
Relációs adatbázisban általában normalizálja az adatokat, hogy távolítsa el a párhuzamosságokat, ami olyan lekérdezéseket eredményez, amelyek több táblából kérnek adatokat. Ha normalizálja az adatokat az Azure-táblákban, több körutat kell tennie az ügyfélről a kiszolgálóra a kapcsolódó adatok lekéréséhez. Például az alábbi táblastruktúra esetén két oda-vissza útra van szükség egy részleg adatainak lekéréséhez: az egyik a vezető azonosítóját tartalmazó részlegentitás lekéréséhez szükséges, majd egy másik kérésa a vezető adatainak beolvasására egy alkalmazotti entitásban.  

![Részleg entitás és Alkalmazott entitás](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Megoldás
Ahelyett, hogy az adatokat két különálló entitásban tárolnák, denormalizálják az adatokat, és a részlegentitásban megőrzik a vezető adatainak másolatát. Példa:  

![Részleg entitása](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Az ezekkel a tulajdonságokkal tárolt részlegentitások esetén most már lekérheti a részleggel kapcsolatos összes részletet egy pontlekérdezés használatával.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Bizonyos adatok kétszeri tárolása bizonyos költségköltség-többlettel jár. A (a tárolási szolgáltatáskevesebb kéréséből eredő) teljesítménybeli előny általában meghaladja a tárolási költségek marginális növekedését (és ezt a költséget részben ellensúlyozza a részleg adatainak lekéréséhez szükséges tranzakciók számának csökkenése. ).  
* Fenn kell tartania a vezetők adatait tároló két entitás konzisztenciáját. A konzisztencia-problémát úgy oldhatja meg, hogy EGT-k használatával több entitást frissít egyetlen atomtranzakcióban: ebben az esetben a részlegentitás és az osztályvezető alkalmazott entitása ugyanabban a partícióban van tárolva.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha gyakran kell kapcsolódó információkat keresnie. Ez a minta csökkenti az ügyfél által a szükséges adatok lekéréséhez szükséges lekérdezések számát.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcsminta](#compound-key-pattern)  
* Entitáscsoport tranzakciói  
* [Heterogén entitástípusok kal való együttműködés](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Összetett kulcsminta
Összetett **RowKey** értékek használatával engedélyezheti, hogy az ügyfél egypontos lekérdezéssel keressen kapcsolódó adatokat.  

### <a name="context-and-problem"></a>Kontextus és probléma
Relációs adatbázisban természetes, hogy illesztések használatával adja vissza a kapcsolódó adatokat az ügyfél egyetlen lekérdezésben. Az alkalmazottazonosító segítségével például megkeresheti azon kapcsolódó entitások listáját, amelyek az adott alkalmazott teljesítményadatait tartalmazzák, és áttekintheti az adatokat.  

Tegyük fel, hogy a Table szolgáltatásban alkalmazottentitásokat tárol a következő struktúra használatával:  

![Alkalmazotti entitás szerkezete](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Az értékelésekkel és a teljesítménysel kapcsolatos előzményadatokat is tárolnia kell minden olyan évre vonatkozóan, amikor az alkalmazott a szervezetnél dolgozott, és ezeket az információkat évszerint hozzá kell tudnia férni. Az egyik lehetőség egy másik tábla létrehozása, amely a következő struktúrával rendelkező entitásokat tárolja:  

![Alternatív munkavállalói entitás szerkezete](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Figyelje meg, hogy ezzel a megközelítéssel dönthet úgy, hogy egyes információkat (például a keresztnevet és a vezetéknevet) duplikálja az új entitásban, hogy egyetlen kéréssel lehesse az adatokat. Azonban nem tarthatja fenn az erős konzisztenciát, mert nem használhat EGT-t a két entitás atomi frissítésére.  

### <a name="solution"></a>Megoldás
Új entitástípus tárolása az eredeti táblában a következő struktúrával rendelkező entitások használatával:  

![Megoldás az alkalmazotti entitás szerkezetéhez](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Figyelje meg, hogy a **RowKey** most egy összetett kulcs alkotja az alkalmazott azonosítóját, és az év a felülvizsgálati adatok, amely lehetővé teszi, hogy lekérje az alkalmazott teljesítményét, és tekintse át az adatokat egyetlen kérelmet egyetlen entitás.  

A következő példa bemutatja, hogyan lehet beolvasni egy adott alkalmazott összes ellenőrzési adatát (például a 000123 alkalmazottat az Értékesítési részlegben):  

$filter=(PartitionKey eq 'Sales') és (RowKey ge 'empid_000123') és (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Olyan megfelelő elválasztó karaktert kell használnia, amely megkönnyíti a **RowKey** érték elemzését: például **000123_2012**.  
* Ezt az entitást ugyanabban a partícióban is tárolja, mint más entitásokat, amelyek ugyanahhoz az alkalmazotthoz kapcsolódó adatokat tartalmaznak, ami azt jelenti, hogy az EGT-k használatával erős konzisztencia fenntartásához.
* Érdemes megfontolni, hogy milyen gyakran fogja lekérdezni az adatokat annak megállapításához, hogy ez a minta megfelelő-e.  Ha például ritkán fér hozzá a felülvizsgálati adatokhoz és a fő alkalmazotti adatokhoz, gyakran külön entitásként kell megtartania őket.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha egy vagy több olyan kapcsolódó entitást kell tárolnia, amelyet gyakran lekérdez.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* Entitáscsoport tranzakciói  
* [Heterogén entitástípusok kal való együttműködés](#working-with-heterogeneous-entity-types)  
* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Log tail minta
A partícióhoz legutóbb hozzáadott *n* entitások lekérése egy **RowKey** érték használatával, amely fordított dátum- és idősorrendben rendezi.  

### <a name="context-and-problem"></a>Kontextus és probléma
Egy közös követelmény képes volt lekérni a legutóbb létrehozott entitásokat, például az alkalmazott által benyújtott 10 legutóbbi költségigényt. A táblalekérdezések támogatják a **$top** lekérdezési műveletet, amely az első *n* entitásokat adja vissza egy készletből: nincs egyenértékű lekérdezési művelet a készlet utolsó n entitásának visszaadásához.  

### <a name="solution"></a>Megoldás
Az entitásokat olyan **RowKey** használatával tárolja, amely természetesen fordított dátum/idő sorrendben rendezi, így a legutóbbi bejegyzés mindig az első a táblában.  

Például ahhoz, hogy lelehessen kérni az alkalmazott által benyújtott 10 legutóbbi költségigényt, használhat az aktuális dátumból/időből származó fordított osztásértéket. A következő C# kódminta egy módot mutat a megfelelő "fordított ticks" érték létrehozására egy **RowKey-hez,** amely a legutóbbitól a legrégebbiig rendez:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

A dátumidő-értékhez a következő kód használatával juthat vissza:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A táblalekérdezés így néz ki:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A fordított osztásértéket a kezdő nullákkal kell bejelölnie, hogy a karakterlánc értéke a várt módon rendezhető legyen.  
* Tisztában kell lennie a méretezhetőségi célok szintjén egy partíciót. Ügyeljen arra, hogy ne hozzon létre hot spot partíciókat.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha fordított dátum/idő sorrendben kell hozzáférnie az entitásokhoz, vagy amikor a legutóbb hozzáadott entitásokhoz kell hozzáférnie.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Előkészület / hozzáfűzés anti-minta](#prepend-append-anti-pattern)  
* [Entitások beolvasása](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Nagy térfogatú törlési minta
Engedélyezze az entitások nagy mennyiségű törlését úgy, hogy az összes entitást a saját külön táblájukban egyidejűtörlésre tárolja; az entitásokat a tábla törlésével törölheti.  

### <a name="context-and-problem"></a>Kontextus és probléma
Számos alkalmazás törli azokat a régi adatokat, amelyeknek már nem kell elérhetők az ügyfélalkalmazások számára, vagy amelyeket az alkalmazás egy másik adathordozóra archivált. Az ilyen adatokat általában egy dátum alapján azonosítja: például az összes 60 napnál több bejelentkezési kérelem rekordjainak törlésére van szükség.  

Az egyik lehetséges kialakítás a bejelentkezési kérelem dátumának és időpontjának használata a **RowKey-ben:**  

![A bejelentkezési kísérlet dátuma és időpontja](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Ez a megközelítés elkerüli a partíciós elérési pontokat, mert az alkalmazás beszúrhatja és törölheti a bejelentkezési entitásokat az egyes felhasználók számára egy külön partíción. Ez a megközelítés azonban költséges és időigényes lehet, ha nagy számú entitással rendelkezik, mert először egy táblavizsgálatot kell végrehajtania a törölni kívánt entitások azonosításához, majd törölnie kell minden régi entitást. Csökkentheti a régi entitások törléséhez szükséges oda-vissza utak számát, ha több törlési kérelmet kötegel az EGT-kbe.  

### <a name="solution"></a>Megoldás
A bejelentkezési kísérletek minden napjára külön táblát használjon. A fenti entitástervezés segítségével elkerülheti az elérési pontokat entitások beszúrásakor, és a régi entitások törlése most antól egyszerűen csak egy tábla törlésének kérdése minden nap (egyetlen tárolási művelet) ahelyett, hogy több száz és ezer egyedi tanképet találna és szeretne. bejelentkezési entitások minden nap.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Támogatja-e a terv más módokon is, amelyekkel az alkalmazás az adatokat használja, például adott entitások keresését, más adatokhoz való csatolást vagy összesített adatok generálását?  
* Elkerüli a tervezési terv a forró pontokat, amikor új entitásokat szúr be?  
* Késésre számíthat, ha a törlés után újra fel szeretné használni ugyanazt a táblanevet. Jobb, ha mindig egyedi táblaneveket használ.  
* Néhány szabályozás várható, amikor először egy új tábla használata, míg a table szolgáltatás megtanulja a hozzáférési mintákat, és elosztja a partíciókat a csomópontok között. Érdemes figyelembe venni, hogy milyen gyakran kell új táblákat létrehoznia.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha nagy mennyiségű entitást kell egyszerre törölnie.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* Entitáscsoport tranzakciói
* [Entitások módosítása](#modifying-entities)  

## <a name="data-series-pattern"></a>Adatsorok mintája
A teljes adatsorokat egyetlen entitásban tárolhatja, hogy minimalizálja a kérések számát.  

### <a name="context-and-problem"></a>Kontextus és probléma
Gyakori forgatókönyv, hogy egy alkalmazás tárolja az adatok sorozatát, amelyekáltalában egyszerre kell beolvasnia. Az alkalmazás például rögzítheti, hogy az egyes alkalmazottak óránként hány üzenetet küldenek, majd ezt az információt felhasználva megtudja, hogy az egyes felhasználók hány üzenetet küldtek az előző 24 órában. Egy terv lehet, hogy 24 entitást tárol minden alkalmazotthoz:  

![24 entitás tárolása minden alkalmazotthoz](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Ezzel a kialakítással könnyedén megkeresheti és frissítheti az entitást, hogy minden alkalmazotthoz frissítsen, amikor az alkalmazásnak frissítenie kell az üzenetszám értékét. Ahhoz azonban, hogy az információt a tevékenység diagramjának ábrázolásához az előző 24 órára vonatkozóan szeretné lekérni, 24 entitást kell beolvasnia.  

### <a name="solution"></a>Megoldás
Az üzenetszám óránkénti tárolására a következő kialakítást külön tulajdonsággal tárolja:  

![Üzenetstatisztika entitás](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Ezzel a kialakítással egyesítési művelettel frissítheti egy alkalmazott üzenetszámát egy adott órára. Most már lekérheti az összes szükséges információt a diagram ábrázolásához egyetlen entitásra vonatkozó kérelem használatával.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ha a teljes adatsor nem fér el egyetlen entitás (egy entitás legfeljebb 252 tulajdonságok, használjon egy alternatív adattár, például egy blob.  
* Ha egyszerre több ügyfél is frissít egy entitást, az **ETag** segítségével kell végrehajtania az optimista egyidejűséget. Ha sok ügyfele van, magas viszálykodást tapasztalhat.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha egy adott entitáshoz társított adatsort kell frissítenie és beolvasnia.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Nagy entitások mintája](#large-entities-pattern)  
* [Egyesítés vagy csere](#merge-or-replace)  
* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern) (ha az adatsorokat egy blobban tárolja)  

## <a name="wide-entities-pattern"></a>Széles entitások mintája
Több fizikai entitás használatával 252-nél több tulajdonsággal rendelkező logikai entitásokat tároljon.  

### <a name="context-and-problem"></a>Kontextus és probléma
Egy adott entitás legfeljebb 252 tulajdonsággal rendelkezhet (kivéve a kötelező rendszertulajdonságokat), és összesen legfeljebb 1 MB adatot tárolhat. Relációs adatbázisban általában egy sor méretére vonatkozó korlátokat kerekíten, ha új táblát ad hozzá, és 1 az egyhez kapcsolatot kényszerít ki közöttük.  

### <a name="solution"></a>Megoldás
A Table szolgáltatás használatával több entitást is tárolhat, hogy egy 252-nél több tulajdonsággal rendelkező nagy üzleti objektumot képviseljen. Ha például az egyes alkalmazottak által az elmúlt 365 napban küldött üzenetek számát szeretné tárolni, a következő tervet használhatja, amely két különböző sémával rendelkező entitást használ:  

![Több entitás](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Ha olyan módosítást kell eszközre tennie, amelymindkét entitás frissítését igényli, hogy szinkronizálva legyenek egymással, használhat EGY EGT-t. Ellenkező esetben egyetlen egyesítési művelettel frissítheti az üzenetek számát egy adott napra vonatkozóan. Az egyes alkalmazottak összes adatának beolvasásához mindkét entitást be kell olvasnia, amelyeket két hatékony kérelemmel tehet meg, amelyek **egy PartitionKey** és egy **RowKey** értéket is használnak.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A teljes logikai entitás beolvasása legalább két tárolási tranzakciót foglal magában: egyet az egyes fizikai entitások lekéréséhez.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha olyan entitásokat kell tárolnia, amelyek mérete vagy tulajdonságainak száma meghaladja a Table szolgáltatás egy adott entitására vonatkozó korlátot.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* Entitáscsoport tranzakciói
* [Egyesítés vagy csere](#merge-or-replace)

## <a name="large-entities-pattern"></a>Nagy entitások mintája
Blob storage használatával nagy tulajdonságértékek tárolására.  

### <a name="context-and-problem"></a>Kontextus és probléma
Egy adott entitás összesen legfeljebb 1 MB adatot tárolhat. Ha egy vagy több tulajdonság olyan értékeket tárol, amelyek hatására az entitás teljes mérete meghaladja ezt az értéket, a teljes entitás nem tárolható a Table szolgáltatásban.  

### <a name="solution"></a>Megoldás
Ha az entitás mérete meghaladja az 1 MB-ot, mert egy vagy több tulajdonság nagy mennyiségű adatot tartalmaz, tárolhatja az adatokat a Blob szolgáltatásban, majd tárolhatja a blob címét az entitás egy tulajdonságában. Például tárolhatja egy alkalmazott fényképét a blobstorage-ban, és tárolhatja a fényképre mutató hivatkozást az alkalmazott entitás **Fénykép** tulajdonságában:  

![Photo ingatlan](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A table szolgáltatásban lévő entitás és a Blob szolgáltatás ban lévő adatok közötti végleges konzisztencia fenntartásához használja a [Végül konzisztens tranzakciók mintáját](#eventually-consistent-transactions-pattern) az entitások karbantartásához.
* Egy teljes entitás beolvasása legalább két tárolási tranzakciót foglal magában: az egyik az entitás lekéréséhez, a másik pedig a blobadatok beolvasásához.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha olyan entitásokat kell tárolnia, amelyek mérete meghaladja a Table szolgáltatás egy adott entitására vonatkozó korlátot.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern)  
* [Széles entitások mintája](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Előleg/hozzáfűzés anti-minta
Növelje a méretezhetőséget, ha nagy mennyiségű lapkával rendelkezik, ha a lapkákat több partícióra osztja.  

### <a name="context-and-problem"></a>Kontextus és probléma
A tárolt entitásokhoz való elő- vagy hozzáfűzés általában azt eredményezi, hogy az alkalmazás új entitásokat ad hozzá a partíciók sorozatának első vagy utolsó partíciójához. Ebben az esetben az összes lapkák egy adott időpontban zajlik ugyanabban a partíción, ami egy hotspot, amely megakadályozza, hogy a table service terheléselosztási beszúrások több csomóponton keresztül, és esetleg okozza az alkalmazás, hogy elérje a méretezhetőségi célok Partíció. Ha például van egy alkalmazás, amely naplózza az alkalmazottak hálózati és erőforrás-hozzáférését, akkor az alábbi entitásstruktúra azt eredményezheti, hogy az aktuális óra partíciója hotspottá válik, ha a tranzakciók mennyisége eléri a méretezhetőségi célt egy egyéni partíció:  

![Entitás szerkezete](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Megoldás
A következő alternatív entitásstruktúra elkerüli a hotspotot egy adott partíción, mivel az alkalmazás naplózza az eseményeket:  

![Alternatív entitásszerkezet](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Figyelje meg ezt a példát arra, hogy a **PartitionKey** és a **RowKey** összetett kulcsok. A **PartitionKey** a részleg és az alkalmazottazonosító segítségével is elosztja a naplózást több partíció között.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az alternatív kulcsstruktúra, amely elkerüli a gyakori elérésű partíciók létrehozása a beszúrások hatékonyan támogatja a lekérdezéseket az ügyfélalkalmazás teszi?  
* A tranzakciók várható mennyisége azt jelenti, hogy valószínűleg eléri az egyes partíciók méretezhetőségi céljait, és a tárolási szolgáltatás szabályozza?  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Kerülje a prepend/hozzáfűzési anti-minta, ha a tranzakciók mennyisége valószínűleg a storage szolgáltatás szabályozását eredményezi, amikor egy gyakori elérésű partíciót.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcsminta](#compound-key-pattern)  
* [Log tail minta](#log-tail-pattern)  
* [Entitások módosítása](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Adatanti-minta naplózása
Általában a Blob szolgáltatást kell használnia a Table szolgáltatás helyett a naplóadatok tárolásához.  

### <a name="context-and-problem"></a>Kontextus és probléma
A naplóadatok gyakori használati oka egy adott dátum/időtartomány naplóbejegyzéseinek beolvasása: például meg szeretné találni az összes olyan hibát és kritikus üzenetet, amelyet az alkalmazás 15:04 és 15:06 között naplózott egy adott napon. A naplóüzenet dátumát és időpontját nem szeretné használni a naplóentitások mentéséhez használt partíció meghatározásához: ez egy gyorspartíciót eredményez, mert egy adott időpontban az összes naplóentitás ugyanazt a **PartitionKey** értéket fogja használni (lásd a [Prepend/Append anti-pattern című szakaszt).](#prepend-append-anti-pattern) Például a következő entitásséma egy naplóüzenet eredménye egy gyorspartíciót, mert az alkalmazás írja az összes naplóüzenetet a partícióra az aktuális dátum és óra:  

![Naplóüzenet entitás](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

Ebben a példában a **RowKey** tartalmazza a naplóüzenet dátumát és időpontját annak érdekében, hogy a naplóüzenetek dátum/idő sorrendben legyenek tárolva, és tartalmaz egy üzenetazonosítót abban az esetben, ha több naplóüzenet ugyanazt a dátumot és időt osztja meg.  

Egy másik megközelítés egy **PartitionKey,** amely biztosítja, hogy az alkalmazás üzeneteket ír a partíciók tartományában. Ha például a naplóüzenet forrása lehetővé teszi az üzenetek több partíció közötti elosztását, a következő entitássémát használhatja:  

![Alternatív naplóüzenet entitás](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

A sémával azonban az a probléma, hogy az összes naplóüzenet egy adott időtartamra vonatkozó lekéréséhez a tábla minden partíciójában meg kell keresnie.

### <a name="solution"></a>Megoldás
Az előző szakasz rávilágított arra a problémára, hogy a Table szolgáltatás sal próbálja tárolni a naplóbejegyzéseket, és két, nem megfelelő tervet javasolt. Az egyik megoldás egy gyorspartícióhoz vezetett, amely nek gyenge a teljesítményének a naplóüzenetek írása; a másik megoldás a lekérdezés idomának csökkenését eredményezte, mivel a tábla minden partíciójának be olvasására van szükség a naplóüzenetek adott időtartamig tartó lekéréséhez. A Blob Storage jobb megoldást kínál az ilyen típusú forgatókönyvekhez, és így tárolja az Azure Storage Analytics az általa gyűjtött naplóadatokat.  

Ez a szakasz ismerteti, hogy a Storage Analytics hogyan tárolja a naplóadatokat a blob storage-ban, mint egy példa erre a megközelítésre a általában lekérdező adatok tartomány szerint.  

A Storage Analytics a naplóüzeneteket több blobban, tagolt formátumban tárolja. A tagolt formátum megkönnyíti az ügyfélalkalmazások számára a naplóüzenetben szereplő adatok elemzését.  

A Storage Analytics elnevezési konvenciót használ a blobokhoz, amely lehetővé teszi a blob (vagy blobok) megkeresését, amelyek tartalmazzák azokat a naplóüzeneteket, amelyekről keres. Például egy blob nevű "queue/2014/07/31/1800/000001.log" naplóüzeneteket tartalmaz, amelyek kapcsolódnak a várólista-szolgáltatás az óra kezdődő 18:00 július 31-én. A "000001" azt jelzi, hogy ez az első naplófájl ebben az időszakban. A Storage Analytics a blob metaadatai nak részeként rögzíti a fájlban tárolt első és utolsó naplóüzenetek időbélyegeit is. A blob storage API-ja lehetővé teszi a blobok megkeresését egy tárolóban egy névelőtag alapján: a 18:00 órától kezdődő en a várólistanapló-adatokat tartalmazó összes blob megkereséséhez használhatja a "queue/2014/07/31/1800" előtagot.  

A Storage Analytics belső naplózza az üzeneteket, majd rendszeresen frissíti a megfelelő blobot, vagy létrehoz egy újat a naplóbejegyzések legújabb kötegével. Ez csökkenti a blob szolgáltatás által végrehajtandó írások számát.  

Ha hasonló megoldást valósít meg a saját alkalmazásában, meg kell fontolnia, hogyan kezelheti a megbízhatóság (minden naplóbejegyzés írása a blobstorage-ba történő írása, ahogy történik) és a költség- és méretezhetőség (pufferelési frissítések az alkalmazásban és az írás között azokat a blob tároló kötegekben).  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A naplóadatok tárolásának eldöntésekor vegye figyelembe a következő pontokat:  

* Ha olyan táblatervet hoz létre, amely elkerüli a potenciális gyakori elérésű partíciókat, előfordulhat, hogy nem tud hatékonyan hozzáférni a naplóadatokhoz.  
* A naplóadatok feldolgozásához az ügyfélnek gyakran sok rekordot kell betöltenie.  
* Bár a naplóadatok gyakran strukturált, blob storage lehet jobb megoldás.  

## <a name="implementation-considerations"></a>Implementálási szempontok
Ez a szakasz az előző szakaszokban ismertetett minták megvalósításakor figyelembe vesszen néhány szempontot. A szakasz nagy része c# nyelven írt példákat használ, amelyek a Storage Client Library-t (az írás időpontjában a 4.3.0-s verziót) használják.  

## <a name="retrieving-entities"></a>Entitások beolvasása
Ahogy azt a Tervezés lekérdezésre című szakaszban tárgyaltuk, a leghatékonyabb lekérdezés egy pontlekérdezés. Bizonyos esetekben azonban előfordulhat, hogy több entitást kell beolvasnia. Ez a szakasz néhány gyakori módszert ismertet az entitások beolvasásához a Storage Client Library használatával.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Pontlekérdezés végrehajtása a Storage Client Library használatával
A pontlekérdezés végrehajtásának legegyszerűbb módja a **Tábla lekérési** művelete, ahogy az a következő C# kódrészletben látható, amely egy "Sales" **értékű PartitionKey** és "212" értékű **RowKey értékű** entitást olvas le:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Figyelje meg, hogy ez a példa hogyan várja el, hogy a beolvasott entitás **EmployeeEntity**típusú legyen.  

### <a name="retrieving-multiple-entities-using-linq"></a>Több entitás beolvasása a LINQ használatával
A LINQ segítségével több entitást is lekérhet a Table szolgáltatásból, ha a Microsoft Azure Cosmos Table Standard Library könyvtárral dolgozik. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Ahhoz, hogy az alábbi példák működjenek, névtereket kell megadnia:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

Az employeeTable egy CloudTable objektum, amely\<egy CreateQuery ITableEntity>()\<metódust valósít meg, amely egy TableQuery ITableEntity>. Az ilyen típusú objektumok IQueryable-t valósítanak meg, és lehetővé teszik mind a LINQ-lekérdezési kifejezések, mind a poklú szintaxis használatát.

Több entitás beolvasása, és egy **where** záradékkal rendelkező lekérdezés megadásával érhető el. A táblavizsgálat elkerülése érdekében mindig a **PartitionKey** értéket kell megadnia a Where záradékban, és ha lehetséges, a **RowKey** értéket a tábla- és partícióvizsgálatok elkerülése érdekében. A table szolgáltatás támogatja a korlátozott számú összehasonlító operátorok (nagyobb, nagyobb vagy egyenlő, kisebb vagy egyenlő, kisebb vagy egyenlő, egyenlő, és nem egyenlő), hogy használja a where záradékot. 

A következő C# kódrészlet megkeresi az összes olyan alkalmazottat, akinek a vezetékneve "B" -vel kezdődik (feltételezve, hogy a **RowKey** tárolja a vezetéknevet) az értékesítési részlegben (feltéve, hogy a **PartitionKey** tárolja az osztály nevét):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Figyelje meg, hogy a lekérdezés a jobb teljesítmény érdekében **rowkey-t** és **partitionkey-t** is megad.  

Az alábbi kódminta egyenértékű funkciókat mutat linq szintaxis nélkül:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> A minta több **CombineFilters** metódust ágyaz be a három szűrőfeltétel felvételéhez.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Nagy számú entitás beolvasása lekérdezésből
Az optimális lekérdezés egy partíciókulcs-érték és egy **RowKey-érték** alapján adott vissza egy entitást. **RowKey** Bizonyos esetekben azonban előfordulhat, hogy szükség van sok entitás tért vissza ugyanabból a partícióról, vagy akár sok partícióról.  

Mindig teljes mértékben tesztelje az alkalmazás teljesítményét az ilyen esetekben.  

A táblaszolgáltatás lekérdezése egyszerre legfeljebb 1000 entitást adhat vissza, és legfeljebb öt másodpercig hajtható végre. Ha az eredményhalmaz 1000-nél több entitást tartalmaz, ha a lekérdezés öt másodpercen belül nem fejeződött be, vagy ha a lekérdezés átlépi a partícióhatárát, a Table szolgáltatás egy folytatási tokent ad vissza, amely lehetővé teszi, hogy az ügyfélalkalmazás az entitások következő készletét kérje. A folytatási jogkivonatok működéséről a [Lekérdezési időmegszavazás és a Tördelés](https://msdn.microsoft.com/library/azure/dd135718.aspx)című témakörben talál további információt.  

Ha a storage-ügyfélkönyvtárat használja, automatikusan képes kezelni a folytatási jogkivonatokat, mivel a Table szolgáltatásból entitásokat ad vissza. A storage-ügyfélkódtár használatával a következő C# kódminta automatikusan kezeli a folytatási jogkivonatokat, ha a table service válaszként adja vissza azokat:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

A következő C# kód explicit módon kezeli a folytatási jogkivonatokat:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

A folytatási tokenek explicit használatával szabályozhatja, hogy az alkalmazás mikor olvassa be az adatok következő szegmensét. Ha például az ügyfélalkalmazás lehetővé teszi a felhasználók számára, hogy egy táblában tárolt entitások között lapozhassanak, a felhasználó dönthet úgy, hogy nem lapoz a lekérdezés által beolvasott összes entitásközött, így az alkalmazás csak egy folytatási jogkivonatot használ a következő szegmens lekéréséhez, amikor a felhasználó befejezte a lapozást az aktuális szegmens összes entitásán keresztül. Ennek a megközelítésnek számos előnye van:  

* Lehetővé teszi, hogy korlátozza a Table szolgáltatásból beolvasni kívánt adatok mennyiségét, és hogy a hálózaton keresztül mozogjon.  
* Lehetővé teszi az aszinkron IO-műveletek elvégzését a .NET-ben.  
* Lehetővé teszi, hogy szerializálja a folytatási jogkivonatot az állandó tárolóba, így folytathatja egy alkalmazás összeomlása esetén.  

> [!NOTE]
> A folytatási jogkivonat általában 1000 entitást tartalmazó szegmenst ad vissza, bár lehet, hogy kevesebb. Ez akkor is így van, ha korlátozza a lekérdezés által visszaadott bejegyzések számát a **Take** használatával a keresési feltételeknek megfelelő első n entitások visszaadásához: a táblaszolgáltatás visszaadhat egy olyan szegmenst, amely kevesebb, mint n entitást tartalmaz, valamint egy folytatási tokent, amely lehetővé teszi a fennmaradó entitások beolvasását.  
> 
> 

A következő C# kód bemutatja, hogyan lehet módosítani a szegmensen belül visszaadott entitások számát:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Kiszolgálóoldali vetítés
Egy entitás legfeljebb 255 tulajdonsággal rendelkezhet, és legfeljebb 1 MB méretű lehet. Amikor lekérdezi a táblát, és lekéri az entitásokat, előfordulhat, hogy nem kell az összes tulajdonságot, és elkerülheti az adatok felesleges átvitelét (a késés és a költség csökkentése érdekében). A kiszolgálóoldali vetület segítségével csak a szükséges tulajdonságokat viheti át. A következő példa csak az **E-mail** tulajdonság (a **PartitionKey, rowkey,** **időbélyeg**és **ETag)** lekérése a lekérdezés által kiválasztott entitásokból. **RowKey**  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Figyelje meg, hogy a **RowKey** érték elérhető, még akkor is, ha nem szerepelt a beolvasandó tulajdonságok listájában.  

## <a name="modifying-entities"></a>Entitások módosítása
A Storage Client Library lehetővé teszi, hogy módosítsa a táblaszolgáltatásban tárolt entitásokat entitások beszúrásával, törlésével és frissítésével. Az EGT-k segítségével több beszúrást kötegelhet, frissítheti és törölheti a műveleteket, így csökkentheti a szükséges adatváltások számát, és javíthatja a megoldás teljesítményét.  

A storage-ügyfélkönyvtár EGT-műveletek végrehajtásakor okozott kivételek általában annak az entitásnak az indexét tartalmazzák, amely a köteg sikertelensét okozta. Ez akkor hasznos, ha EGT-ket használó kódot használ.  

Azt is meg kell fontolnia, hogy a terv hogyan befolyásolja, hogy az ügyfélalkalmazás hogyan kezeli az egyidejűségi és frissítési műveleteket.  

### <a name="managing-concurrency"></a>Az egyidejűség kezelése
Alapértelmezés szerint a table service optimista egyidejűségi ellenőrzéseket valósít meg az egyes entitások szintjén **beszúrása,** **egyesítése**és **törlése** műveletekhez, bár az ügyfél kényszerítheti a table service-t az ellenőrzések megkerülésére. Arról, hogy a table service hogyan kezeli az egyidejűséget, [olvassa el az Egyidejűség kezelése a Microsoft Azure Storage szolgáltatásban című témakört.](../../storage/common/storage-concurrency.md)  

### <a name="merge-or-replace"></a>Egyesítés vagy csere
A **TableOperation** osztály **Csere** metódusa mindig a Table szolgáltatás teljes entitását helyettesíti. Ha nem vesz fel tulajdonságot a kérelembe, ha az adott tulajdonság létezik a tárolt entitásban, a kérelem eltávolítja a tulajdonságot a tárolt entitásból. Hacsak nem szeretne egy tulajdonságot kifejezetten eltávolítani egy tárolt entitásból, minden tulajdonságot bele kell foglalnia a kérelembe.  

A **TableOperation** osztály Egyesítés i **módszerével** csökkentheti a Table szolgáltatásnak küldött adatok mennyiségét, ha egy entitást szeretne frissíteni. Az **Egyesítés** módszer a tárolt entitás bármely tulajdonságát a kérelemben szereplő entitás tulajdonságértékeivel helyettesíti, de érintetlenül hagyja a tárolt entitás azon tulajdonságait, amelyek nem szerepelnek a kérelemben. Ez akkor hasznos, ha nagy entitásokkal rendelkezik, és csak néhány tulajdonságot kell frissítenie egy kérelemben.  

> [!NOTE]
> A **Csere** és **az Egyesítés** metódus sikertelen, ha az entitás nem létezik. Másik lehetőségként használhatja a **InsertOrReplace** és **InsertOrMerge** metódusokat, amelyek új entitást hoznak létre, ha az nem létezik.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Heterogén entitástípusok kal való együttműködés
A Table szolgáltatás egy *séma nélküli* táblatároló, amely azt jelenti, hogy egyetlen tábla többféle típusú entitásokat képes tárolni, nagy rugalmasságot biztosítva a tervezésben. A következő példa egy alkalmazotti és részlegentitásokat egyaránt tároló táblát mutat be:  

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
<th>DepartmentName</th>
<th>Alkalmazottszáma</th>
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

Minden entitásnak rendelkeznie kell **PartitionKey**, **RowKey**, és **Timestamp** értékekkel, de tulajdonságok at tartalmazhatnak. Továbbá semmi sem jelzi az entitás típusát, hacsak nem úgy dönt, hogy valahol tárolja az információt. Az entitástípus azonosítására két lehetőség van:  

* Az entitástípus tágítható a **RowKey** (vagy esetleg a **PartitionKey)** számára. Például **EMPLOYEE_000123** vagy **DEPARTMENT_SALES** **RowKey** értékként.  
* Használjon külön tulajdonságot az entitástípus rögzítéséhez az alábbi táblázatban látható módon.  

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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Employee (Alkalmazott)</td>
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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Employee (Alkalmazott)</td>
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
<th>EntityType</th>
<th>DepartmentName</th>
<th>Alkalmazottszáma</th>
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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Employee (Alkalmazott)</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Az első lehetőség, amely az entitástípust a **RowKey-re**írja előre, akkor hasznos, ha fennáll annak a lehetősége, hogy két különböző típusú entitás nak ugyanaz a kulcsértéke. Az azonos típusú entitásokat is csoportosítja a partícióban.  

Az ebben a szakaszban tárgyalt technikák különösen fontosak az [öröklési kapcsolatokról](table-storage-design-modeling.md#inheritance-relationships) szóló cikk korábbi részében, a [Kapcsolatok modellezése](table-storage-design-modeling.md)című cikkben.  

> [!NOTE]
> Fontolja meg egy verziószám megírása az entitástípus értékében, hogy az ügyfélalkalmazások POCO-objektumokat fejleszthessenek, és különböző verziókkal dolgozhassanak.  
> 
> 

A szakasz további része a Storage Client Library néhány olyan szolgáltatását ismerteti, amelyek megkönnyítik az ugyanabban a táblában lévő több entitástípussal való munkát.  

### <a name="retrieving-heterogeneous-entity-types"></a>Heterogén entitástípusok beolvasása
Ha a Storage Client Library-t használja, három lehetősége van több entitástípus használatára.  

Ha ismeri az adott **RowKey** és **PartitionKey** értékekkel tárolt entitás típusát, akkor megadhatja az entitás típusát az entitás beolvasásakor, ahogy az az előző két példában látható, amelyek **Az EmployeeEntity**: [Pontlekérdezés végrehajtása a Storage Client Library használatával,](#executing-a-point-query-using-the-storage-client-library) és [több entitás beolvasása a LINQ használatával](#retrieving-multiple-entities-using-linq).  

A második lehetőség a **DynamicTableEntity** típus (tulajdonságtáska) használata egy konkrét POCO entitástípus helyett (ez a beállítás javíthatja a teljesítményt is, mert nincs szükség az entitás .NET-típusokra történő szerializálására és deszerializálására). A következő C# kód potenciálisan lekéri a különböző típusú entitások a táblából, de visszaadja az összes entitást **DynamicTableEntity** példányként. Ezután az **EntityType** tulajdonsággal határozza meg az egyes entitások típusát:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
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
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Más tulajdonságok lekéréséhez a **TryGetValue** metódust kell használnia a **DynamicTableEntity** osztály **Tulajdonságok** tulajdonságán.  

A harmadik lehetőség a **DynamicTableEntity** típus és az **EntityResolver** példány használatával történő kombinálás. Ez lehetővé teszi, hogy ugyanazon lekérdezésben több POCO-típusra is feloldódjon. Ebben a példában az **EntityResolver** delegált az **EntityType** tulajdonsággal különbözteti meg a lekérdezés által visszaadott két entitástípust. A **Feloldás** metódus a **feloldó** delegáltja segítségével oldja fel a **DynamicTableEntity** példányokat **TableEntity-példányokra.**  

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
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Heterogén entitástípusok módosítása
Nem kell tudnia, hogy milyen típusú entitás törölni, és mindig tudja, hogy milyen típusú egy entitás, amikor beszúrja. **A DynamicTableEntity** típussal azonban frissíthet egy entitást a típusának ismerete nélkül, és POCO entitásosztály használata nélkül. A következő kódminta egyetlen entitást kér le, és ellenőrzi, hogy az **EmployeeCount** tulajdonság létezik-e a frissítés előtt.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Hozzáférés szabályozása megosztott hozzáférésű aláírásokkal
A Megosztott hozzáférésű aláírás (SAS) jogkivonatokkal engedélyezheti, hogy az ügyfélalkalmazások anélkül módosíthassák (és lekérdezhetjék) a táblaentitásokat, hogy a tárfiók kulcsát fel kellene venni a kódba. A SAS alkalmazásban való használatának általában három fő előnye van:  

* Nem kell terjesztenie a tárfiók kulcsát egy nem biztonságos platformra (például egy mobileszközre) annak érdekében, hogy az eszköz hozzáférhessen és módosíthassa az entitásokat a Table szolgáltatásban.  
* Kiszervezheti azokat a munkákat, amelyeket a webes és a feldolgozói szerepkörök az entitások ügyféleszközökre, például végfelhasználói számítógépekre és mobileszközökre való kezelése során végeznek.  
* Az ügyfelekhez korlátozott és korlátozott számú engedélykészletet rendelhet (például írásvédett hozzáférést biztosít bizonyos erőforrásokhoz).  

A SAS-jogkivonatok table szolgáltatással való használatáról a [Megosztott hozzáférésű aláírások (SAS) használata című](../../storage/common/storage-sas-overview.md)témakörben talál további információt.  

Azonban továbbra is létre kell hoznia a SAS-jogkivonatokat, amelyek ügyfélalkalmazást adnak a table service entitásainak: ezt olyan környezetben kell megtennie, amely biztonságos hozzáférést biztosít a tárfiók kulcsaihoz. Általában egy webes vagy feldolgozói szerepkört használ a SAS-jogkivonatok létrehozásához, és azokat az entitásokhoz hozzáférést igénylő ügyfélalkalmazásokhoz. Mivel továbbra is többletterhelés merül fel a SAS-jogkivonatok létrehozása és az ügyfelek számára történő kézbesítése során, érdemes megfontolnia, hogyan lehet a legjobban csökkenteni ezt a többletterhelést, különösen a nagy volumenű forgatókönyvekben.  

Lehetőség van egy SAS-jogkivonat létrehozásához, amely hozzáférést biztosít a tábla entitásainak egy részhalmazához. Alapértelmezés szerint egy teljes táblához hoz létre SAS-jogkivonatot, de azt is megadhatja, hogy a SAS-jogkivonat hozzáférést adjon **a PartitionKey** értékek tartományához, vagy **a PartitionKey** és **a RowKey** értékek tartományához. Előfordulhat, hogy sas-jogkivonatokat hoz létre a rendszer egyes felhasználói számára úgy, hogy minden felhasználó SAS-jogkivonata csak a saját entitásaikhoz való hozzáférést engedélyezi a table service-ben.  

## <a name="asynchronous-and-parallel-operations"></a>Aszinkron és párhuzamos műveletek
Feltéve, hogy a kérelmeket több partícióközött osztja el, aszinkron vagy párhuzamos lekérdezések használatával javíthatja az átviteli és az ügyfél válaszképességét.
Előfordulhat például, hogy két vagy több feldolgozói szerepkörpéldány párhuzamosan fér hozzá a táblákhoz. Előfordulhat, hogy a partíciók adott készleteiért felelős egyéni feldolgozói szerepköröket, vagy egyszerűen csak több feldolgozói szerepkör-példányt, mindegyik képes elérni a partíciókat egy táblában.  

Egy ügyfélpéldányon belül javíthatja az átviteli kapacitást a storage-műveletek aszinkron végrehajtásával. A tárolóügyfél-kódtár megkönnyíti az aszinkron lekérdezések és módosítások írását. Előfordulhat például, hogy a szinkron metódussal kezdi, amely lekéri a partíció összes entitását a következő C# kódban látható módon:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Ezt a kódot egyszerűen módosíthatja úgy, hogy a lekérdezés aszinkron módon futjon a következőképpen:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Ebben az aszinkron példában a következő módosításokat láthatja a szinkron verzióból:  

* A metódus aláírása most már tartalmazza az **aszinkron** módosítót, és **egy Feladatpéldányt** ad vissza.  
* Ahelyett, hogy az **ExecuteSegmented metódust hívnák** meg az eredmények lekéréséhez, a metódus most meghívja az **ExecuteSegmentedAsync metódust,** és a **wait** módosítót használja az eredmények aszinkron beolvasásához.  

Az ügyfélalkalmazás többször is meghívhatja ezt a metódust (a **részlegparaméter** különböző értékeivel), és minden lekérdezés külön szálon fog futni.  

A **TableQuery** osztályban nincs aszinkron verzió az **Execute** metódusban, mert az **IEnumerable** felület nem támogatja az aszinkron enumerálást.  

Az entitásokat aszinkron módon is beszúrhatja, frissítheti és törölheti. A következő C# példa egy egyszerű, szinkron módszert mutat be egy alkalmazottentitás beszúrására vagy cseréjére:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Ezt a kódot egyszerűen módosíthatja úgy, hogy a frissítés aszinkron módon futjon a következőképpen:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Ebben az aszinkron példában a következő módosításokat láthatja a szinkron verzióból:  

* A metódus aláírása most már tartalmazza az **aszinkron** módosítót, és **egy Feladatpéldányt** ad vissza.  
* Ahelyett, hogy az Entitás frissítéséhez az **Execute** metódust hívna meg, a metódus meghívja az **ExecuteAsync** metódust, és a **várt** módosítót használja az eredmények aszinkron lekéréséhez.  

Az ügyfélalkalmazás több aszinkron metódust is meghívhat, mint ez, és minden metódus meghívása külön szálon fog futni.  

## <a name="next-steps"></a>További lépések

- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Tervezés lekérdezéshez](table-storage-design-for-query.md)
- [Táblaadatok titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
