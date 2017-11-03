# <a name="compute"></a>Számítás
Azure lehetővé teszi üzembe helyezése és figyelése a Microsoft adatközponton belül futó alkalmazáskódok. Amikor létrehoz egy alkalmazást, és futtassa az Azure-on, a kód és a konfigurációs együtt nevezzük az Azure üzemeltetett szolgáltatásban. Üzemeltetett szolgáltatások olyan könnyen kezelése, felfelé és lefelé méretezési, konfigurálja újra, és frissítse az alkalmazás kódja az új verzióit. Ez a cikk foglalkozik az Azure szolgáltatásban az alkalmazás szolgáltatásmodellt.<a id="compare" name="compare"></a>

## A tartalomjegyzék<a id="_GoBack" name="_GoBack"></a>
* [Az Azure alkalmazás modell előnyei][Azure Application Model Benefits]
* [Üzemeltetett szolgáltatás alapvető jellemzői][Hosted Service Core Concepts]
* [Üzemeltetett szolgáltatás kialakítási szempontok][Hosted Service Design Considerations]
* [Az alkalmazás bővített tervezése][Designing your Application for Scale]
* [Üzemeltetett szolgáltatás definíció- és konfigurációs][Hosted Service Definition and Configuration]
* [A szolgáltatásdefiníciós fájl][The Service Definition File]
* [A szolgáltatás konfigurációs fájlja][The Service Configuration File]
* [Létrehozása és telepítése az üzemeltetett szolgáltatás][Creating and Deploying a Hosted Service]
* [Hivatkozások][References]

## <a id="benefits"></a>Azure alkalmazás modell előnyei
Egy üzemeltetett szolgáltatást, az alkalmazás központi telepítésekor az Azure létrehoz egy vagy több virtuális gépek (VM), amely tartalmazza az alkalmazás kódját, és indul el a virtuális gépek egyik az Azure-adatközpont fizikai gépeken. Az üzemeltetett alkalmazás ügyfélkérések meg az adatközpontban, a terheléselosztó osztja el a ezeket a kérelmeket az virtuális gépeket egyaránt. Amíg az alkalmazás üzemel az Azure-ban, onnan kapta, hogy három fő előnyei:

* **Magas rendelkezésre állású.** Magas rendelkezésre állású azt jelenti, hogy az Azure biztosítja, hogy az alkalmazás lehetőség szerint fut, és képes ügyfélkérelmekre. Ha az alkalmazás leállása (egy nem kezelt kivétel miatt, például), majd Azure észleli ezt, így automatikusan indítsa újra az alkalmazást. Ha a számítógép az alkalmazás fut lép hardver hiba lépett fel, majd Azure is észleli ezt, és automatikusan hozzon létre egy új virtuális Gépet egy másik számítógépen működő fizikai és onnan futtassa a kódot. Megjegyzés: Ahhoz, hogy az alkalmazások a Microsoft szolgáltatásiszint-megállapodás 99,95 %-os érhető el, rendelkeznie kell az alkalmazás kódjában futó legalább két virtuális gép. Ez lehetővé teszi, hogy egy virtuális gép ügyfélkérések feldolgozása, miközben Azure helyezi át a kódot a sikertelen virtuális gépről egy új, a megfelelő virtuális géphez.
* **Méretezhetőség.** Azure lehetővé teszi, hogy könnyen, és dinamikusan a virtuális gép fut az alkalmazás kódjában kerülne az alkalmazás tényleges terhelhető számának módosítása. Ez lehetővé teszi, hogy állítsa be az alkalmazás számára a munkaterhelés, hogy az ügyfelek csak a virtuális gépeket, ha szüksége van rájuk a míg helyez rajta. Ha szeretné módosítani a virtuális gépek számát, Azure válaszol, így dinamikusan módosítható a kívánt gyakorisággal futó virtuális gépek száma percen belül.
* **Kezelhetőségi.** Mivel Azure platformot kínál egy szolgáltatást (PaaS), ezek a gépek futtató tartása szükséges infrastruktúra (a hardvertől elektromosság kialakulása és hálózat) kezeli. Azure is kezeli a platform összes a megfelelő javításokat és biztonsági frissítések, valamint összetevő-frissítések, például a .NET-keretrendszer és az Internet Information Server rendelkező naprakész operációs rendszer biztosítja. Mivel a virtuális gépeket futtat Windows Server 2008, Azure diagnosztikai figyelő, a távoli asztali támogatása, a tűzfalak és a tanúsítvány adattár konfigurálása például olyan funkciókat is biztosítanak. Ezek a funkciók találhatók, nem kapcsolódik további költség. Futtassa az alkalmazást az Azure-ban, ha a Windows Server 2008 operációs rendszer licenc valójában tartalmazza. Mivel minden, a virtuális gépek futnak a Windows Server 2008, bármely kódot, amely a Windows Server 2008 fut remekül működik, csak az Azure-ban való futtatásakor.

## <a id="concepts"></a>Üzemeltetett szolgáltatás alapvető jellemzői
Az alkalmazás telepítésekor az Azure-ban üzemeltetett szolgáltatásként fut egy vagy több *szerepkörök.* A *szerepkör* egyszerűen az alkalmazás fájljai és konfigurációs hivatkozik. Egy vagy több szerepkör adhat meg az alkalmazás, mindegyiket a saját fájljainak és a konfiguráció beállítása. Az egyes szerepkörökhöz, az alkalmazásban, adja meg a virtuális gépek számát vagy *szerepkörpéldányokat*, futtatásához. Az alábbi ábra egy üzemeltetett szolgáltatást, használja a szerepkörök és a szerepkörpéldányok modellezve alkalmazás két egyszerű példát bemutatja.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>1. ábra: Egyetlen szerepkör három osztályt (VM) egy Azure-adatközpontban fut
![Kép][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>2. ábra: Két szerepkörök, melyek mindegyike két osztályt (VM), egy Azure-adatközpontban fut
![Kép][1]

Szerepkörpéldányokat általában feldolgozni az internetes ügyfelek kéréseit az Adatközpont keresztül úgynevezett megadása egy *bemeneti végpont*. Egyetlen szerepkör lehet 0 vagy több bemeneti végpontot. Minden egyes végpont (HTTP, HTTPS vagy TCP) protokoll és port jelzi. Általános a két bemeneti végpont szerepkör konfigurálása: HTTP a 80-as portot és a HTTPS a 443-as porton figyel. Az alábbi ábrán két különböző szerepkörök példát arra utasíthatja őket ügyfélkérések különböző bemeneti végpont tartalmazza.

![Kép][2]

Az Azure-ban létrehoz egy üzemeltetett szolgáltatást, amikor egy nyilvánosan megcímezhető IP-címet, amellyel az ügyfelek hozzá van hozzárendelve. Az üzemeltetett szolgáltatás létrehozása után ki kell választania egy adott IP-címre leképezett URL-előtagot is. Az előtag egyedinek kell lennie mint lényegében lefoglalja a *előtag*. cloudapp.net URL-CÍMÉT úgy, hogy senki más nem rendelkezhet. Az ügyfelek az URL-cím használatával kommunikálnak a szerepkörpéldányok. Általában, akkor nem fog terjesztése vagy közzététele az Azure *előtag*. cloudapp.net URL-CÍMÉT. Ehelyett fog vásárolhat egy DNS-nevet a DNS-regisztráló választott, és az ügyfelek kérelmeinek átirányítása Azure URL-CÍMÉT a DNS-nevét konfigurálja. További részletekért lásd: [egy egyéni tartománynév konfigurálása az Azure-ban][Configuring a Custom Domain Name in Azure].

## <a id="considerations"></a>Üzemeltetett szolgáltatás kialakítási szempontok
Egy felhőalapú környezetben futó alkalmazás tervezésekor több szempontot figyelembe kell vennie, például a késés, magas rendelkezésre állás és méretezhetőség.

Fontos szempont az alkalmazás kódjában elhelyezése akkor, ha egy üzemeltetett szolgáltatást az Azure-ban. Általában a késés csökkentésére, és a lehető legjobb teljesítményt lehetséges ügyfelek legközelebbi adatközpont az alkalmazás telepítése.
Azonban egy adatközpont közelebb a vállalat vagy az adatok közelebb lehet használni, ha néhány joghatósági vagy jogi kétségei adatait és helyét. Nincsenek a az alkalmazás kódjában tárolására képes a világ minden táján hat adatközpontokban. Az alábbi táblázat a helyeket:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr>

<td style="width: 100px;">
**Ország vagy régió**

</td>

<td style="width: 200px;">
**Alárendelt régiók**

</td>
</tr>

<tr>

<td>
Egyesült Államok

</td>

<td>
Dél-központi & északi középső régiója

</td>
</tr>

<tr>

<td>
Európa

</td>

<td>
Észak- & nyugati régiója

</td>
</tr>

<tr>

<td>
Ázsia

</td>

<td>
Délkeleti & keleti régiója

</td>
</tr>
</tbody>
</table>
Üzemeltetett szolgáltatás létrehozásakor válassza ki a helyet, amelyben a kód végrehajtása kívánja jelző körzet.

Magas rendelkezésre állás és méretezhetőség eléréséhez különösen fontos, hogy az alkalmazás adatokat tárolni egy központi tárházban érhető el a szerepkör több példánya. Ennek érdekében az Azure BLOB, a táblák és az SQL-adatbázis például számos különféle tárolási lehetőséget kínál. Tekintse meg a [adatok tárolási ajánlatokat az Azure-ban] [ Data Storage Offerings in Azure] cikk további információt a tárolási technológiákat. Az alábbi ábra bemutatja, hogyan osztja el a terheléselosztó az Azure-adatközpont belül a ezek mindegyike hozzáférhetnek az azonos adattárolóra különböző szerepkörpéldányokra érkező ügyfélkérések.

![Kép][3]

Általában érdemes keresse meg az alkalmazás kódjában és az adatok ugyanabban az adatközpontban, így az alacsony késleltetés (jobb teljesítmény) Ha az alkalmazás kódjában éri el az adatokat. Ezenkívül van nem szó, a sávszélesség mozgatásakor adatok egy adatközponton belül.

## <a id="scale"></a>Az alkalmazás bővített tervezése
Egyes esetekben érdemes lehet (például egy egyszerű webhely) egyetlen alkalmazás igénybe vehet, és annak az Azure-ban üzemeltetett. De gyakran, az alkalmazás állhat számos szerepkört, hogy az összes működnek együtt. Például az alábbi ábrán vannak a webhely két példánya, három példánya a rendelés feldolgozása és a jelentések készítése szerepkörnek egy példánya. Ezek a szerepkörök összes működnek együtt, és mindegyikhez kódot együtt csomagolt és telepített Azure legfeljebb egyetlen egységként.

![Kép][4]

A fő oka a felosztott különböző szerepkörök egy alkalmazást minden egyes szerepkörpéldányokat (Ez azt jelenti, hogy a virtuális gépek) a saját készlete futó a szerepkörök méretezését. Például az ünnepi során sok ügyfél előfordulhat, hogy lehet megvásárlásáról termékek a vállalat, előfordulhat, hogy szeretné növelni a szerepkörpéldányok fut, a webhely szerepkör, valamint a feldolgozási sorrendben szerepkört futtató szerepkör-példányok száma számát. Után az ünnepi jelenhet meg lett visszaadva, akkor termékek sok, nagy mennyiségű webhely példányok, de kevesebb rendelés feldolgozása példányt lehet szükség. Az év hátralévő részében, során csak szükség lehet néhány webhely és rendelés feldolgozása példányok. Minden fenti, egész szükség lehet a jelentések készítése csak egy példány. A szerepkör-alapú telepítés esetében az Azure rugalmasságát lehetővé teszi, hogy könnyedén alkalmazkodnak az alkalmazás számára az üzleti igényeknek megfelelően.

Esetében gyakori, az üzemeltetett szolgáltatáson belül lévő példányok kommunikálnak egymással a szerepkörrel kell rendelkeznie. Például a webhely szerepkör fogad el egy megrendeléshez, de majd azt kiszervezést, hogy a rendelés feldolgozása szerepkörpéldányokat feldolgozás sorrendje. A legjobb módszer egy másik példánycsoportot a szerepkörpéldányok egy készletét használja az üzenetsor-kezelési technológia Azure, által biztosított munkahelyi formában átadni a Queue szolgáltatás vagy a Service Bus-üzenetsorok. A várólista használata egy kritikus fontosságú része itt. A várólista lehetővé teszi, hogy az üzemeltetett szolgáltatás méretezése a szerepkörök külön lehetővé téve munkaterhelésének költség ellen. Ha a várólistán lévő üzenetek száma növekszik adott idő alatt, majd költenie rendelés feldolgozása szerepkörpéldányok számát. Ha idővel a várólistán lévő üzenetek száma csökken, majd méretezheti le rendelés feldolgozása szerepkörpéldányok számát. Ezzel a módszerrel csak fizet a példányok, a tényleges terheléshez kezeléséhez szükséges.

A várólista megbízhatóságát is biztosít. Skálázás le a feldolgozási sorrendben szerepkör-példányok száma, amikor Azure úgy dönt, mely példány leáll. Dönthet egy példányát, amely éppen egy várólista üzenet feldolgozása leáll. Azonban az üzenet feldolgozása nem sikerült, mert az üzenet válik láthatóvá újra a másik rendelés feldolgozása szerepkörpéldányt, amely azt veszi fel, és feldolgozza őket. Várólista állapotüzenet látható, mert üzenetek kézbesítése garantáltan a idővel feldolgozni. A várólista is funkcionál terheléselosztó üzenetek kérhet minden szerepkörpéldányokat az üzenetek hatékonyan elosztásával.

A webhely szerepkörpéldányokért hamarosan be őket a forgalom felügyelete és úgy dönt, hogy azok számának felfelé vagy lefelé is. A várólista webhely szerepkörpéldányt beállítani, függetlenül a rendelés feldolgozása szerepkörpéldányokat számának teszi lehetővé. Ez nagyon hatékony és nagy rugalmasságot biztosít. Természetesen Ha az alkalmazás további szerepköröket, hozzáadhatja további várólisták, az átjáró ahhoz, hogy használja az ugyanazon méretezésének ki, ráadásul a költségek előnyöket közötti kommunikációt.

## <a id="defandcfg"></a>Üzemeltetett szolgáltatás definíció- és konfigurációs
Azure üzemeltetett szolgáltatás telepítése szükséges hozzá is a szolgáltatásdefiníciós fájlban és egy szolgáltatás konfigurációs fájljában. Mindkét fájl XML-fájlokat, és lehetővé teszik, hogy deklarációval adja meg a központi telepítési beállításokat az üzemeltetett szolgáltatás számára. A szolgáltatásdefiníciós fájlban ismerteti az összes üzemeltetett szolgáltatásba, és hogyan kommunikálnak a szerepkört. A szolgáltatás konfigurációs fájlja a szerepköröket és minden egyes szerepkör példánya konfigurálásához használt beállítások a példányok száma ismerteti.

## <a id="def"></a>a szolgáltatásdefiníciós fájl
Szeretnék már említettük, a szolgáltatás definíciós (CSDEF), a fájl egy XML-fájl, amely leírja a szerepköröket, amelyek a teljes alkalmazás alkotják. A teljes séma az XML-fájlt az itt található: [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx] [-].
A CSDEF fájl az egyes szerepkörökhöz, amelyet az alkalmazás vagy webrole típusról a WorkerRole elemet tartalmaz. (A webrole típusról elem használatával) webes szerepkör egy szerepkör telepítése, az azt jelenti, hogy a Windows Server 2008 és az Internet Information Server (IIS) szerepkör-példányon futtatja a kódot.
Egy szerepkör telepítése, mivel a feldolgozói szerepkör (a WorkerRole elem használatával) jelenti, hogy a szerepkör példánya lesz-e a Windows Server 2008 rajta (az IIS nem lehet telepítve).

Biztosan létrehozása és központi telepítése a feldolgozói szerepkör, amely valamilyen más módszerrel a beérkező webes kérelmek figyelésére használ (például a kód sikerült létrehozása és használata a .NET HttpListener). Mivel a szerepkörpéldányok futó összes Windows Server 2008, a kód hajthat végre semmilyen olyan műveletet, amely elérhető a Windows Serveren futó alkalmazáshoz való
2008.

Az egyes szerepkörökhöz adja meg a kívánt Virtuálisgép-méretet, amelyet az adott szerepkör példányt kell használnia. Az alábbi táblázat a különböző ma használható Virtuálisgép-méretek és az egyes attribútumait jeleníti meg:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr align="left" valign="top">

<td>
**Virtuálisgép-mérettel**

</td>

<td>
**PROCESSZOR**

</td>

<td>
**RAM-MAL**

</td>

<td>
**Lemez**

</td>

<td>
**Csúcsidőszak hálózati i/o**

</td>
</tr>

<tr align="left" valign="top">

<td>
**Nagyon kicsi**

</td>

<td>
1 x 1.0 GHz

</td>

<td>
768 MB

</td>

<td>
20GB

</td>

<td>
\~5 MB/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Kis**

</td>

<td>
1 x 1,6 GHz-es

</td>

<td>
1,75 GB

</td>

<td>
225GB

</td>

<td>
\~100 MB/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Közepes**

</td>

<td>
2 x 1,6 GHz-es

</td>

<td>
3,5 GB

</td>

<td>
490GB

</td>

<td>
\~200 MB/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Nagy**

</td>

<td>
4 x 1,6 GHz-es

</td>

<td>
7 GB

</td>

<td>
1 TB-OS

</td>

<td>
\~400 MB/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Extra nagy**

</td>

<td>
8 x 1,6 GHz-es

</td>

<td>
14 GB

</td>

<td>
A 2TB

</td>

<td>
\~800 MB/s

</td>
</tr>
</tbody>
</table>
Van szó, óránként az egyes virtuális gépek szerepkör példányt használ, és van is szó, az adatokat, hogy a szerepkörpéldányok küldése az adatközponton kívül. Az Adatközpont belépés adatokat nem van szó. További információkért tekintse meg a [Azure díjszabás] [Azure díjszabás]. Általában tanácsos a sok kisméretű szerepkörpéldányokat figyelésekor néhány nagy példányok használja, hogy az alkalmazás több esetén is lehetséges legyen. Rendelkezik a kevesebb szerepkörpéldányt beállítani, a több hiba történt a egyiket egy katasztrofális a teljes alkalmazáshoz. Is ahogy korábban említettük, telepítenie kell az egyes szerepkörökhöz legalább két példányt 99,95 % szolgáltatásiszint-szerződést biztosít a Microsoft eléréséhez.

A szolgáltatás definíciós (CSDEF) fájl is, ahol kellene megadnia egyes szerepkörök számos attribútum az alkalmazásban. Íme néhány hasznos elemek elérhetők:

* **Tanúsítványok**. A titkosított adatok, vagy ha a webszolgáltatás támogatja az SSL-tanúsítványokat használ. A tanúsítványok kell az Azure-bA feltölteni. További információkért lásd: [tanúsítványok kezelése az Azure-ban][Managing Certificates in Azure]. XML-beállításról, hogy-e az alkalmazás kódjában tárolóhálózatnak telepíti a szerepkörpéldányt tanúsítványtárolóba korábban feltöltött tanúsítványok.
* **Konfigurációs beállítás nevek**. Az értékeket a alkalmazás(ok) olvasása közben egy szerepkör-példányon futnak. A tényleges konfigurációs beállítás értéke a szolgáltatás konfigurációs fájlban (szolgáltatáskonfigurációs SÉMA) anélkül, hogy telepítse újra a kódot bármikor frissíthető. Valójában is kód az alkalmazások oly módon, a módosított konfigurációs értékeket észleléséhez állásidő nélkül.
* **A bemeneti végpontok**. Itt adhatja meg HTTP, HTTPS vagy TCP-végpontok (a portok), amelyet a külvilág keresztül tegye elérhetővé a *előtag*. cloadapp.net URL-CÍMÉT. Ha Azure telepíti a szerepkör, be fogja beállítani a tűzfalat szerepkörpéldányon automatikusan.
* **Belső végpont**. Itt adhatja meg a HTTP vagy TCP-végpontok kívánt többi szerepkörpéldányon, az alkalmazás részeként telepített kitéve. Belső végpont engedélyezése az összes szerepkörpéldányt felvegye a egymáshoz az alkalmazáson belül, de bármely szerepkörpéldányt beállítani, amelyek túlmutatnak az alkalmazás számára nem érhetők.
* **Modul importálása**. Ezeket nem kötelező a szerepkörpéldányok hasznos összetevőinek telepítése. Összetevők léteznek diagnosztikai figyelő, a távoli asztal, és Azure csatlakozzon (amely lehetővé teszi, hogy a szerepkör példánya a helyszíni erőforrások eléréséhez egy biztonságos csatornán keresztül).
* **Helyi tároló**. Ez osztja ki az egyik alkönyvtár használandó alkalmazás-példányon. További részletes leírása a [adatok tárolási ajánlatokat az Azure-ban] [ Data Storage Offerings in Azure] cikk.
* **Indítási feladatok**. Indítási feladatok előfeltételként szükséges összetevőket telepíti a szerepkör példányán elinduló úgy adhat. A feladatok emelt szintű rendszergazdai szükség esetén futtathatja.

## <a id="cfg"></a>a szolgáltatás konfigurációs fájlja
A szolgáltatás konfigurációs (szolgáltatáskonfigurációs SÉMA) fájl nem egy XML-fájl, amely beállításokat ismerteti, amelyeket az alkalmazás üzembe helyezésével módosíthatja. A teljes séma az XML-fájlt az itt található: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][http://msdn.microsoft.com/library/windowsazure/ee758710.aspx].
A CSCFG-fájl az egyes szerepkörökhöz, az alkalmazás szerepkör elemet tartalmaz. Íme néhány a cikkeket, megadhatja a szolgáltatáskonfigurációs SÉMA fájlban:

* **Operációs rendszer verziója**. Ez az attribútum a használt fut az alkalmazás kódjában szerepkörpéldányt beállítani kívánt operációs rendszer verzióját kiválasztását teszi lehetővé. Az operációs rendszer is ismert, a *vendég operációs rendszer*, és minden új verzióját tartalmazza a legújabb biztonsági javítások és a vendég operációs rendszer felszabadul időpontjában elérhető frissítések. Ha az osVersion attribútum értéke "\*", akkor az Azure automatikusan frissíti a vendég operációs rendszer egyes a szerepkörpéldányok, amint elérhetővé válnak az új Vendég operációsrendszer-verziók. Azonban is kikapcsolja az automatikus frissítések egy konkrét vendég operációs rendszer verziója kiválasztásával. Például beállítást az osVersion attribútum értéke "WA-VENDÉG-operációsrendszer-2.8\_201109-01" hatására a beolvasandó leírtaktól a weblapon lévő összes szerepkörpéldány: [http://msdn.microsoft.com/library/hh560567.aspx] [http://msdn.microsoft.com/library/hh560567.aspx]. További információ a Vendég operációsrendszer-verziók: [frissítések kezelése az Azure vendég operációs rendszeren].
* **Példányok**. Ez az elem érték azt jelzi, hogy a kiosztott fut egy adott szerepkör kódjának kívánt szerepkör-példányok száma. Egy új CSCFG-fájl (az alkalmazás üzembe helyezésével) feltöltheti az Azure-ba, mert már trivially egyszerű módosítsa az értéket ehhez az elemhez, és dinamikusan növeléséhez vagy csökkentéséhez tegye a következőket a alkalmazás kódja szerepkörpéldányokat új CSCFG-fájl feltöltése . Ez lehetővé teszi, hogy az alkalmazás könnyen vertikális vagy igazodhat tényleges terheléshez közben is szabályozása mennyire van szó, a szerepkör-példányok fut le.
* **A beállítás értéke konfigurációs**. Ez az elem beállítások értékeit jelző, (a CSDEF fájlban meghatározott). A szerepkör tud olvasni ezeket az értékeket, futása közben is. Ezeket a konfigurációs beállítások értékeket az SQL Database vagy az Azure Storage kapcsolati karakterláncok általában használják, de bármilyen célra felügyelni is használhatók.

## <a id="hostedservices"></a>Létrehozása és telepítése az üzemeltetett szolgáltatás
Üzemeltetett szolgáltatás létrehozása megköveteli, hogy nyissa meg a [Azure felügyeleti portálon] és egy üzemeltetett szolgáltatást egy DNS-előtagja és az adatok megadásával center meg kiépítési végső soron szeretné, hogy a futó kód. A fejlesztői környezetben, majd hozzon létre egy szolgáltatás definíciós (CSDEF) fájlt, az alkalmazás kódja és (zip) csomag összes ezeknek a fájloknak hozhat létre. a szolgáltatás (CSPKG) a(z). A szolgáltatás konfigurációs (szolgáltatáskonfigurációs SÉMA) fájl is elő kell készítenie. A szerepkör telepítéséhez töltse fel a CSPKG és a szolgáltatáskonfigurációs SÉMA fájlokat az Azure Service Management API-val. Amennyiben telepített, Azure-ban fog rendelkezni szerepkörpéldányokat (a konfigurációs adatok alapján) az adatközpontban, csomagolja ki a csomagból az alkalmazás kódjában, másolja azt a szerepkörpéldányok és indítsa el a példányok. Most a kód megfelelően működik, és.

Az alábbi ábra a fejlesztési számítógépen létrehozott CSPKG és a szolgáltatáskonfigurációs SÉMA fájlokat. A CSPKG fájl tartalmazza a CSDEF fájl- és a két szerepkörök. Azure Service Management API-val CSPKG és a szolgáltatáskonfigurációs SÉMA fájlok feltöltése után Azure az adatközpontban a szerepkörpéldányok hoz létre. Ebben a példában a CSCFG-fájl jelezte, hogy Azure hozzon létre három példánya \#1 és két példánya \#2.

![Kép][5]

Központi telepítésével kapcsolatos további információkért frissítése és újrakonfigurálása a szerepkörök, tekintse meg a [központi telepítése és frissítése Azure alkalmazások] [ Deploying and Updating Azure Applications] cikk.<a id="Ref" name="Ref"></a>

## <a id="references"></a>Hivatkozások
* [Az Azure-üzemeltetett szolgáltatás létrehozása][Creating a Hosted Service for Azure]
* [Az Azure-ban üzemeltetett szolgáltatások kezelése][Managing Hosted Services in Azure]
* [Azure-alkalmazások áttelepítése][Migrating Applications to Azure]
* [Az Azure-alkalmazások konfigurálása][Configuring an Azure Application]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Jeffrey Richter (Wintellect) szerint</p>

</div>

[Azure Application Model Benefits]: #benefits
[Hosted Service Core Concepts]: #concepts
[Hosted Service Design Considerations]: #considerations
[Designing your Application for Scale]: #scale
[Hosted Service Definition and Configuration]: #defandcfg
[The Service Definition File]: #def
[The Service Configuration File]: #cfg
[Creating and Deploying a Hosted Service]: #hostedservices
[References]: #references
[0]: ./media/application-model/application-model-3.jpg
[1]: ./media/application-model/application-model-4.jpg
[2]: ./media/application-model/application-model-5.jpg
[Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
[Data Storage Offerings in Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
[3]: ./media/application-model/application-model-6.jpg
[4]: ./media/application-model/application-model-7.jpg

[Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
[Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
[frissítések kezelése az Azure vendég operációs rendszeren]: http://msdn.microsoft.com/library/ee924680.aspx
[Azure felügyeleti portálon]: http://manage.windowsazure.com/
[5]: ./media/application-model/application-model-8.jpg
[Deploying and Updating Azure Applications]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
[Creating a Hosted Service for Azure]: http://msdn.microsoft.com/library/gg432967.aspx
[Managing Hosted Services in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
[Migrating Applications to Azure]: http://msdn.microsoft.com/library/gg186051.aspx
[Configuring an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
