---
title: X.509 tanúsítványalapú hitelesítés szolgáltatásháló-fürtben
description: Ismerje meg a tanúsítványalapú hitelesítést a Service Fabric-fürtökben, és hogyan észlelheti, enyhítheti és javíthatja a tanúsítványokkal kapcsolatos problémákat.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429667"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X.509 Tanúsítványalapú hitelesítés a Service Fabric-fürtökben

Ez a cikk kiegészíti a [Service Fabric-fürt biztonságának](service-fabric-cluster-security.md)bevezetését, és a Service Fabric-fürtök tanúsítványalapú hitelesítésének részleteit ismerteti. Feltételezzük, hogy az olvasó ismeri az alapvető biztonsági fogalmakat, valamint a service fabric által a fürt biztonságának szabályozásához elérhető vezérlőket.  

Az e cím alatt tárgyalt témakörök:

* A tanúsítványalapú hitelesítés alapjai
* Identitások és szerepük
* Tanúsítványkonfigurációs szabályok
* Hibaelhárítás és gyakori kérdések

## <a name="certificate-based-authentication-basics"></a>A tanúsítványalapú hitelesítés alapjai
Rövid frissítésként a biztonság területén a tanúsítvány olyan eszköz, amely egy entitásra (tárgyra) vonatkozó információkat egy pár aszimmetrikus kriptográfiai kulcs birtoklásához köti, és így a nyilvános kulcsú titkosítás alapvető felépítését képezi. A tanúsítvány által képviselt kulcsok felhasználhatók az adatok védelmére vagy a kulcsbirtokosok személyazonosságának igazolására; Ha nyilvános kulcsú infrastruktúra (PKI) rendszerrel együtt használják, a tanúsítvány a tulajdonos további jellemzőit is jelentheti, például egy internetes tartomány tulajdonjogát, vagy a tanúsítvány kibocsátója (hitelesítésszolgáltató vagy hitelesítésszolgáltató) által számára biztosított bizonyos jogosultságokat. A tanúsítványok közös alkalmazása a Transport Layer Security (TLS) kriptográfiai protokoll támogatása, amely lehetővé teszi a biztonságos kommunikációt a számítógépes hálózaton keresztül. Pontosabban, az ügyfél és a kiszolgáló tanúsítványokat használ kommunikációjuk védelmének és integritásának biztosítására, valamint a kölcsönös hitelesítés végrehajtására.

A Service Fabric, a fürt (Összevonás) alapvető rétege is épül a TLS (többek között protokollok) a részt vevő csomópontok megbízható, biztonságos hálózatának elérése érdekében. A fürthöz a Service Fabric-ügyfél API-kon keresztül a kapcsolatot használja a TLS is a forgalom védelme érdekében, valamint a felek identitásának megállapításához. Pontosabban, ha a Service Fabric hitelesítésére használják, a tanúsítvány a következő jogcímek igazolására használható: a) a tanúsítvány hitelesítő adatainak előadója rendelkezik a tanúsítvány titkos kulcsával b) a tanúsítvány SHA-1 kivonata ("ujjlenyomat") megegyezik a fürtdefinícióban szereplő deklarációval, vagy c) a tanúsítvány megkülönböztető tulajdonosi közneve megegyezik a fürtdefinícióban szereplő nyilatkozattal. , és a tanúsítvány kibocsátója ismert vagy megbízható.

A fenti listában a "b" köznyelvben "ujjlenyomat-rögzítésnek" nevezik; ebben az esetben a nyilatkozat egy adott tanúsítványra hivatkozik, és a hitelesítési rendszer erőssége azon a feltevésen alapul, hogy számításilag kivitelezhetetlen olyan tanúsítványt hamisítani, amely ugyanazt a kivonatértéket állítja elő, mint egy másik, miközben minden más tekintetben továbbra is érvényes, jól formázott objektum. A "c" tétel a bizonyítvány bejelentésének alternatív formáját jelenti, ahol a rendszer erőssége a tanúsítvány tárgyát képező alany és a kibocsátó hatóság kombinációján alapul. Ebben az esetben a nyilatkozat a tanúsítványok egy osztályára vonatkozik – bármely két, azonos jellemzőkkel rendelkező tanúsítvány teljesen egyenértékűnek minősül. 

A következő szakaszok részletesen ismerteti, hogyan használja a Service Fabric futásidejű és érvényesíti a tanúsítványokat a fürt biztonságának biztosítása érdekében.

## <a name="identities-and-their-respective-roles"></a>Identitások és szerepük
Mielőtt belemerülne a hitelesítés részleteibe vagy a kommunikációs csatornák védelmébe, fontos felsorolni a részt vevő szereplőket és a fürtben betöltött megfelelő szerepüket:
- a Service Fabric futásidejű, a továbbiakban "rendszer": a szolgáltatások, amelyek a fürt absztrakciók és funkciók. Amikor a rendszerpéldányok közötti fürtön keresztüli kommunikációra hivatkozunk, a "fürtidentitás" kifejezést használjuk; amikor a fürtre hivatkozik a fürtön kívüli forgalom címzettjeként/célként, a "kiszolgálóidentitás" kifejezést használjuk.
- üzemeltetett alkalmazások, a továbbiakban:"alkalmazások": a fürt tulajdonosa által megadott kód, amelyet a fürtben vezényelnek és hajtanak végre
- ügyfelek: a fürt konfigurációjának megfelelően a fürthöz való kapcsolódás és a funkciók végrehajtása. Két jogosultsági szintet különböztetünk meg - a "felhasználó" és az "admin". A "felhasználói" ügyfél elsősorban írásvédett műveletekre korlátozódik (de nem minden írásvédett funkcióra), míg egy "rendszergazda" ügyfél korlátlan hozzáféréssel rendelkezik a fürt funkcióihoz. (További részletekért olvassa el a [Service Fabric-fürt biztonsági szerepköreit.)](service-fabric-cluster-security-roles.md)
- (Csak Azure)a Service Fabric-szolgáltatások, amelyek vezényli és elérhetővé szabályozza a Service Fabric-fürtök üzemeltetését és felügyeletét, a továbbiakban egyszerűen "szolgáltatás". A környezettől függően a "szolgáltatás" az Azure Service Fabric erőforrás-szolgáltató, vagy más erőforrás-szolgáltatók tulajdonosa és üzemeltetője a Service Fabric csapat tulajdonában van.

Egy biztonságos fürtben ezek a szerepkörök konfigurálhatók saját, különálló identitással, amely egy előre definiált szerepkörnév és a hozzá tartozó hitelesítő adatok párosításaként deklarálható. A Service Fabric támogatja a hitelesítő adatok tanúsítványként vagy tartományalapú egyszerű szolgáltatásként való deklarálását. (A Windows-/Kerberos-alapú identitások is támogatottak, de nem tartoznak a jelen cikk hatálya alá; lásd a [Windows-alapú biztonság a Service Fabric-fürtökben](service-fabric-windows-cluster-windows-security.md).) Az Azure-fürtökben az ügyfélszerepkörök [Azure Active Directory-alapú identitásként](service-fabric-cluster-creation-setup-aad.md)is deklarálhatók.

Ahogy a fentiekre utalt, a Service Fabric futásidejű két jogosultsági szintet határoz meg egy fürtben: "admin" és "felhasználó". A rendszergazdai ügyfél és a "rendszer" összetevő egyaránt működik "admin" jogosultságokat, és így nem megkülönböztethető egymástól. A fürtön való kapcsolat/fürt höz való csatlakozás esetén a Service Fabric futásidejű egy hitelesített hívót kap a következő engedélyezés alapjaként. A hitelesítést a következő szakaszokban vizsgáljuk meg részletesen.

## <a name="certificate-configuration-rules"></a>Tanúsítványkonfigurációs szabályok
### <a name="validation-rules"></a>Érvényesítési szabályok
A Service Fabric-fürtök biztonsági beállításai elvben a következő szempontokat írják le:
- a hitelesítés típusa; ez a fürt létrehozási idejű, megváltoztathatatlan jellemzője. Ilyen beállítások például a "ClusterCredentialType", a "ServerCredentialType", az engedélyezett értékek pedig a "none", az "x509" vagy a "windows". Ez a cikk az x509-típusú hitelesítésre összpontosít.
- a (hitelesítési) érvényesítési szabályok; ezeket a beállításokat a fürt tulajdonosa állítja be, és leírják, hogy egy adott szerepkörhöz mely hitelesítő adatokat kell elfogadni. A példákat közvetlenül az alábbiakban vizsgáljuk meg részletesen.
- a hitelesítés eredményének finom módosítására használt beállítások; ilyenpéldák a visszavont tanúsítványok listájának érvényesítését korlátozó jelzők (visszavonás)stb.

> [!NOTE]
> Az alábbi fürtkonfigurációs példák a fürtjegyzék ből származó részletek XML formátumban, mint a leginkább emésztett formátum, amely közvetlenül támogatja a Service Fabric ebben a cikkben leírt funkciókat. Ugyanazok a beállítások fejezhetők ki közvetlenül a fürtdefiníció JSON-ábrázolásaiban, függetlenül attól, hogy önálló json-fürtjegyzékről vagy Egy Azure Resource Mangement sablonról van-e szó.

A tanúsítványérvényesítési szabály a következő elemekből áll:
- a megfelelő szerepkör: ügyfél, rendszergazdai ügyfél (kiemelt szerepkör)
- a szerepkörhöz elfogadott hitelesítő adat, amelyet ujjlenyomatvagy a téma köznapi neve deklarál

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Ujjlenyomat-alapú tanúsítványérvényesítési nyilatkozatok
Az ujjlenyomat-alapú érvényesítési szabályok esetében a fürtbe/a fürtbe kapcsolatot kérő hívó által benyújtott hitelesítő adatok a következőképpen lesznek érvényesítve:
  - a hitelesítő adat egy érvényes, jól formázott tanúsítvány: lánca felépíthető, az aláírások
  - a tanúsítvány érvényes (NotBefore <= most < NotAfter)
  - a tanúsítvány SHA-1 kivonata megegyezik a deklarációval, mint a kis- és nagybetűk et nem megkülönböztető karakterlánc-összehasonlítás, kivéve az összes szóközt

A láncépítés vagy -érvényesítés során előforduló megbízhatósági hibákat a rendszer letiltja az ujjlenyomatalapú deklarációk esetében, kivéve a lejárt tanúsítványokat – bár erre az esetre vonatkozóan is léteznek rendelkezések. Pontosabban a következő hibák: a visszavonási állapot ismeretlen vagy offline állapot, nem megbízható gyökér, érvénytelen kulcshasználat, részleges lánc nem végzetes hibának minősülnek; a feltevés, ebben az esetben az, hogy a tanúsítvány csupán egy borítékot egy kulcspár - a biztonság abban a tényben rejlik, hogy a fürt tulajdonosa állított be olyan intézkedéseket, hogy megvédje a személyes kulcsot.

A fürtjegyzék következő részlete példázza az ujjlenyomat-alapú érvényesítési szabályok ilyen készletét:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

A fenti bejegyzések mindegyike egy adott identitásra hivatkozik, ahogy azt korábban leírtuk; minden bejegyzés lehetővé teszi több érték megadását is, vesszővel tagolt karakterlánclistaként. Ebben a példában a bejövő hitelesítő adatok sikeres érvényesítése után az SHA-1 ujjlenyomatú tanúsítvány előadója d5ec... 4264" kapja meg az "admin" szerepet; ezzel szemben, a hívó hitelesítése a tanúsítvány "7c8f ... 01b0" kap egy "felhasználói" szerepkört, amely elsősorban írásvédett műveletekre korlátozódik. Egy bejövő hívó, aki egy olyan tanúsítványt mutat be, amelynek ujjlenyomata vagy "abcd... 1234" vagy "ef01... 5678' a fürt társcsomópontjaként fogadható el. Végül a fürt felügyeleti végpontjához csatlakozó ügyfél a kiszolgálótanúsítvány ujjlenyomatát "ef01... 5678'. 

Mint korábban említettük, a Service Fabric nem rendelkezik a lejárt tanúsítványok elfogadásáról; Ennek az az oka, hogy a tanúsítványok élettartama korlátozott, és ha az ujjlenyomat (amely egy adott tanúsítványpéldányra hivatkozik) deklarálva, a tanúsítvány lejáratának engedélyezése vagy a fürthöz való csatlakozás sikertelenségét, vagy a fürt teljes összeomlását eredményezi. Ez túl könnyű elfelejteni, vagy figyelmen kívül hagyni forgó ujjlenyomat-rögzített tanúsítvány, és sajnos a hasznosítás egy ilyen helyzet nehéz.

E célból a fürt tulajdonosa kifejezetten kijelentheti, hogy az ujjlenyomattal bejelentett, önaláírt tanúsítványok at érvényesnek kell tekinteni, az alábbiak szerint:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Ez a viselkedés nem terjed ki a hitelesítésszolgáltató által kiállított tanúsítványokra; Ha ez a helyzet, a visszavont, ismerten feltört lejárt tanúsítvány "érvényessé" válhat, amint az már nem szerepel a hitelesítésszolgáltató tanúsítvány-visszavonási listáján, és így biztonsági kockázatot jelent. Az önaláírt tanúsítványok esetében a fürt tulajdonosa az egyetlen olyan fél, aki felelős a tanúsítvány titkos kulcsának védelméért, ami nem áll fenn a hitelesítésszolgáltató által kiállított tanúsítványok esetében – előfordulhat, hogy a fürt tulajdonosa nem tudja, hogyan és mikor deklarálták a tanúsítványukat.

#### <a name="common-name-based-certificate-validation-declarations"></a>Közös névalapú tanúsítvány-érvényesítési nyilatkozatok
A közös névalapú deklarációk a következő formák egyikét öltik:
- tulajdonos köznapi neve (csak)
- téma köznapi neve a kibocsátó rögzítésével

Először is vegyünk egy részletet egy fürtjegyzékből, amely mindkét deklarációs stílust példázza:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
A deklarációk a kiszolgálóra és a fürtidentitásra vonatkoznak; vegye figyelembe, hogy a KN-alapú deklarációk saját szakaszokkal rendelkeznek a fürtjegyzékben, amelyek elkülönülnek a "Biztonság" szabványtól. Mindkét nyilatkozatban a "Név" a tanúsítvány megkülönböztető tulajdonosi köznapi nevét, az "Érték" mező pedig a várt kiállítót jelöli, az alábbiak szerint:

- az első esetben a deklaráció kimondja, hogy a kiszolgálói tanúsítvány megkülönböztető tulajdonosának köznapi néveleme várhatóan megegyezik a "server.demo.system.servicefabric.azure-int" karakterlánccal; az üres "Érték" mező azt az elvárást jelzi, hogy a tanúsítványlánc gyökere megbízható azon a csomóponton/számítógépen, ahol a kiszolgálótanúsítványt érvényesítik; Windows rendszerben ez azt jelenti, hogy a tanúsítvány a "Megbízható legfelső szintű hitelesítésszolgáltató" tárolóban telepített tanúsítványok bármelyikéhez képes;
- a második esetben a nyilatkozat kimondja, hogy a tanúsítvány előadója a fürtben társcsomópontként kerül elfogadásra, ha a tanúsítvány köznapi neve megegyezik a "cluster.demo.system.servicefabric.azure-int" karakterlánccal, *és* a tanúsítvány közvetlen kibocsátójának ujjlenyomata megegyezik az "Érték" mezőben szereplő vesszővel tagolt bejegyzések egyikével. (Ezt a szabálytípust köznyelvben "a kibocsátó rögzítésével közös névnek" nevezik.)

A tanúsítvány lánca mindkét esetben megépül, és várhatóan hibamentes lesz; ez azt jelenti, hogy a visszavonási hibák, a részleges láncvagy az idő-érvénytelen megbízhatósági hibák végzetesnek minősülnek, és a tanúsítvány érvényesítése sikertelen lesz. A kibocsátók rögzítésével a "nem megbízható legfelső szintű" állapot nem végzetes hibaként jelenik meg; a látszat ellenére ez az érvényesítés szigorúbb formája, mivel lehetővé teszi a fürt tulajdonosának, hogy az engedélyezett/elfogadott kibocsátók készletét a saját PKI-jükre korlátozza.

A tanúsítványlánc megépítése után a rendszer egy szabványos TLS/SSL-házirend alapján érvényesíti, amelynek a deklarált tulajdonos a távoli neve; a tanúsítvány akkor tekinthető egyezésnek, ha a tulajdonos köznapi neve vagy bármely más alanya megegyezik a fürtjegyzék KN-deklarációjával. Ebben az esetben a helyettesítő karakterek támogatottak, és a karakterlánc egyeztetése nem i.

(Egyértelművé kell tennünk, hogy a fent leírt sorrend a tanúsítvány által deklarált kulcshasználat minden típusához végrehajtható; ha a tanúsítvány megadja az ügyfélhitelesítési kulcs használatát, a lánc először egy ügyfélszerepkörhöz épül fel és értékelésre kerül. Sikeresség esetén az értékelés befejeződik, és az érvényesítés sikeres. Ha a tanúsítvány nem rendelkezik az ügyfélhitelesítési használattal, vagy az érvényesítés sikertelen, a Service Fabric futásidejű létrehozza és kiértékeli a láncot a kiszolgáló hitelesítéséhez.)

A példa befejezéséhez a következő részlet az ügyféltanúsítványok köznapi névvel történő deklarálását mutatja be:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

A fenti deklarációk megfelelnek az admin és a felhasználói identitások, illetve; az ilyen módon deklarált tanúsítványok érvényesítése pontosan megegyezik a fürt- és kiszolgálótanúsítványok előző példáiban leírtakkal.

> [!NOTE]
> A gyakori névalapú deklarációk célja a fürttanúsítványok elforgatásának és általában kezelésének egyszerűsítése. A fürt folyamatos rendelkezésre állásának és biztonságának biztosítása érdekében azonban ajánlott betartani az alábbi ajánlásokat:
  * inkább kibocsátó pinning támaszkodva megbízható gyökerek
  * kerülje a különböző pki-k kibocsátóinak keverését
  * biztosítja, hogy minden várható kibocsátó szerepel a tanúsítványnyilatkozatban; nem megfelelő kibocsátó sikertelen érvényesítést eredményez
  * győződjön meg arról, hogy a PKI tanúsítványházirend-végpontjai felderíthetők, elérhetők és hozzáférhetők – ez azt jelenti, hogy az AIA, a CRL vagy az OCSP végpontok a levéltanúsítványon vannak deklarálva, és hogy azok hozzáférhetők legyenek, hogy a tanúsítványlánc-építés befejeződhessen.

Az X.509-es tanúsítványokkal védett fürtben lévő kapcsolatra vonatkozó kérelem fogadása után a Service Fabric futásidejű a fürt biztonsági beállításait fogja használni a távoli fél fent leírt hitelesítő adatainak ellenőrzéséhez; ha sikeres, a hívó/távoli fél hitelesítve lesz. Ha a hitelesítő adatok több érvényesítési szabálynak felelnek meg, a futásidejű a hívónak az egyező szabályok bármelyikének legmagasabb szintű jogosultsággal rendelkező szerepkörét adja meg. 

### <a name="presentation-rules"></a>Bemutatószabályai
Az előző szakasz leírja, hogyan működik a hitelesítés egy tanúsítványáltal védett fürtben; ez a szakasz bemutatja, hogy maga a Service Fabric futásidejű hogyan deríti fel és tölti be a fürtön belül i kommunikációhoz használt tanúsítványokat; ezeket "prezentációs" szabályoknak nevezzük.

Az érvényességi szabályokhoz hasonlóan a megjelenítési szabályok is megadnak egy szerepkört és a kapcsolódó hitelesítő adatok deklarációját, amelyet ujjlenyomat vagy közös név fejez ki. Az érvényesítési szabályokkal ellentétben a közös névalapú nyilatkozatok nem tartalmaznak rendelkezéseket a kibocsátó rögzítésére vonatkozóan; ez nagyobb rugalmasságot és jobb teljesítményt tesz lehetővé. A megjelenítési szabályok a fürtjegyzék "NodeType" szakaszában vannak deklarálva minden egyes csomóponttípusra; a beállítások fel vannak osztva a fürt Biztonsági szakaszaiból, hogy minden csomóponttípus teljes konfigurációja egyetlen szakaszban legyen. Az Azure Service Fabric-fürtök, a csomópont típustanúsítvány-deklarációk alapértelmezett a megfelelő beállításokat a fürt definíciójának Biztonság szakaszában.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Ujjlenyomat-alapú tanúsítványbemutatási deklarációk
A hogy korábban leírták, a Service Fabric futásidejű különbséget tesz a fürt más csomópontjainak társaként és a fürtkezelési műveletek kiszolgálójaként betöltött szerepe között. Elvileg ezek a beállítások jól konfigurálhatók, de a gyakorlatban hajlamosak igazodni. A cikk további részében az egyszerűség kedvéért feltételezzük, hogy a beállítások megegyeznek.

Nézzük meg a következő részlet egy fürt jegyzékfájl:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
A "ClusterCertificate" elem a teljes sémát mutatja, beleértve a választható paramétereket ('X509FindValueSecondary') vagy a megfelelő alapértelmezett paramétereket ("X509StoreName"). a többi nyilatkozat rövidített formanyomtatványt mutat. A fenti fürttanúsítvány-deklaráció kimondja, hogy az "nt1vm" típusú csomópontok biztonsági beállításait a "cc71" tanúsítvánnyal inicializálják. 1984", mint az elsődleges, és a "49e2.. 19d6" bizonyítvány másodlagos; mindkét tanúsítvány várhatóan megtalálható a\'LocalMachine My tanúsítványtárolójában (vagy a Linux egyenértékű elérési út, *var/ lib/sfcerts).*

#### <a name="common-name-based-certificate-presentation-declarations"></a>Közös névalapú tanúsítványbemutatási nyilatkozatok
A csomóponttípus-tanúsítványok a tulajdonos köznapi neve alapján is deklarálhatók, az alábbiak szerint:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

A Deklaráció bármelyik típusa esetén a Service Fabric-csomópont beolvassa a konfigurációt indításkor, megkeresi és betölti a megadott tanúsítványokat, és a NotAfter attribútum csökkenő sorrendjében rendezi őket; a rendszer figyelmen kívül hagyja a lejárt tanúsítványokat, és a lista első eleme lesz a csomópont által megkísérelt Service Fabric-kapcsolat ügyfélhitelesítő adataiként. (A Service Fabric valójában a legtávolabbi lejáró tanúsítványt részesíti előnyben.)

Ne feledje, hogy a köznévalapú bemutatódeklarációk esetében a tanúsítvány akkor tekinthető egyezésnek, ha a tulajdonos köznapi neve megegyezik a deklaráció X509FindValueSecondary (vagy X509FindValueSecondary) mezőjével, mint kis- és nagybetűket megkülönböztető, pontos karakterlánc-összehasonlítás. Ez ellentétben áll az érvényességi szabályokkal, amelyek támogatják a helyettesítő karakter egyeztetését, valamint a kis- és nagybetűk megkülönböztetését.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Egyéb tanúsítványkonfigurációs beállítások
Korábban már említettük, hogy a Service Fabric-fürt biztonsági beállításai lehetővé teszik a hitelesítési kód viselkedésének finom módosítását is. Míg a [Service Fabric fürtbeállításairól](service-fabric-cluster-fabric-settings.md) szóló cikk a beállítások átfogó és legfrissebb listáját jelöli, a tanúsítványalapú hitelesítés néhány biztonsági beállítás jelentésének jelentésére kibővítjük a teljes feljelentést. Minden beállításhoz ismertetjük a szándékot, az alapértelmezett értéket/viselkedést, a hitelesítés befolyásolásának és az elfogadható értékeknek a módját.

Mint említettük, a tanúsítvány érvényesítése mindig magában foglalja a tanúsítvány láncának kiépítését és kiértékelését. A hitelesítésszolgáltató által kiadott tanúsítványok esetében ez a látszólag egyszerű operációsrendszer-API-hívás általában több kimenő hívást von maga után a kiállító PKI különböző végpontjaihoz, a válaszok gyorsítótárazását és így tovább. A tanúsítvány-érvényesítési hívások gyakorisága miatt a Service Fabric-fürtben a PKI végpontjaiban felmerülő problémák a fürt korlátozott rendelkezésre állását vagy végleges lebontását eredményezhetik. Bár a kimenő hívásokat nem lehet lenyomni (erről a gyakori kérdések ről bővebben lásd alább), a következő beállítások kal elfedhetők a visszavont tanúsítványok listájának sikertelen hívásai által okozott érvényesítési hibák.

  * CrlCheckingFlag - a "Biztonság" szakaszban a karakterlánc ot UINT-re konvertálja. Ennek a beállításnak az értékét a Service Fabric a tanúsítványlánc állapothibáinak maszkolására használja a láncépítés viselkedésének módosításával; a Win32 CryptoAPI [CertGetCertificateChain](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) hívás "dwFlags" paraméterként kerül átadásra, és a függvény által elfogadott jelzők bármely érvényes kombinációjára állítható be. A 0 érték kényszeríti a Service Fabric futásidejű figyelmen kívül hagyja a megbízhatósági állapot hibákat – ez nem ajánlott, mivel annak használata jelentős biztonsági kitettséget jelentene. Az alapértelmezett érték: 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Mikor kell használni: a helyi teszteléshez, önaláírt tanúsítványok vagy fejlesztői tanúsítványok, amelyek nem teljesen formázott/ nem rendelkezik a megfelelő nyilvános kulcs ú infrastruktúra a tanúsítványok támogatásához. A pki-k közötti átmenet során a léghiántú környezetekben is használható.

  Hogyan kell használni: egy példát, amely kényszeríti a visszavonás ellenőrzése eléréséhez csak a gyorsítótárazott URL-eket. Feltételezve:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  akkor a fürtjegyzékben lévő deklaráció a következővé válik:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError - a "Biztonság" szakaszban a logikai érték alapértelmezett értéke "false". A "visszavonás offline" láncépítési hibaállapot (vagy egy későbbi láncházirend-érvényesítési hiba állapot) letiltására szolgáló parancsikont jelöli.

  Mikor kell használni: helyi tesztelés, vagy olyan fejlesztői tanúsítványokkal, amelyeket nem támogat a megfelelő PKI. A léghiántor környezetben vagy a PKI-ről ismert, hogy nem érhető el.

  Hogyan kell használni:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Egyéb említésre méltó beállítások (mind ezt a "Biztonság" részben találhatóak):
  * AcceptExpiredPinnedClusterCertificate – az ujjlenyomat-alapú tanúsítványellenőrzéssel foglalkozó szakaszban tárgyalt; lehetővé teszi a lejárt, önaláírt fürttanúsítványok elfogadását. 
  * CertificateExpirySafetyMargin - interval, percekkel a tanúsítvány NotAfter időbélyege előtt kifejezve, és amely alatt a tanúsítvány lejárati kockázatnak minősül. A Service Fabric figyeli a fürttanúsítvány(oka)t, és rendszeres időközönként állapotjelentéseket bocsát ki a fennmaradó rendelkezésre állásukról. A "biztonsági" intervallumon belül ezek az állapotjelentések "figyelmeztető" állapotra emelkednek. Az alapértelmezett érték 30 nap.
  * CertificateHealthReportingInterval - szabályozza a fürttanúsítványok hátralévő időérvényességére vonatkozó állapotjelentések gyakoriságát. A jelentések et csak egyszer bocsátjuk ki ebben az intervallumban. Az értéket másodpercben fejezik ki, az alapértelmezett érték 8 óra.
  * EnforcePrevalidationOnSecurityChanges – logikai érték, szabályozza a fürtfrissítés viselkedését a biztonsági beállítások változásainak észlelésekor. Ha a beállítás "true", a fürt frissítése megpróbálja biztosítani, hogy legalább egy tanúsítvány megfelel a bemutató szabályok bármelyikének képessé teszi a megfelelő érvényességi szabályt. Az ellenőrzés megtörténik, mielőtt az új beállítások bármely csomópontra vonatkozna, de csak a Fürtkezelő szolgáltatás elsődleges replikáját üzemeltető csomóponton fut a frissítés megkezdésekor. Mivel az írás, a beállítás alapértelmezett "hamis", és lesz beállítva, hogy "igaz" az új Azure Service Fabric-fürtök egy futásidejű verzió kezdődő 7.1.
 
### <a name="end-to-end-scenario-examples"></a>Végponttól végpontig forgatókönyv (példák)
Átnéztük a prezentációs szabályokat, az érvényesítési szabályokat és a jelzők finomhangolását, de hogyan működik ez együtt? Ebben a szakaszban két végponttól végpontig tartó példán dolgozunk, amelyek bemutatják, hogyan használhatók ki a biztonsági beállítások a biztonságos fürtfrissítésekhez. Vegye figyelembe, hogy ez nem célja, hogy egy átfogó disszertáció a megfelelő tanúsítványkezelés a Service Fabric, keressen egy társ cikket a témában.

A bemutatási és érvényesítési szabályok szétválasztása felveti azt a nyilvánvaló kérdést (vagy aggályt), hogy eltérhetnek-e egymástól, és milyen következményekkel járna. Valóban lehetséges, hogy egy csomópont hitelesítési tanúsítvány kiválasztása nem felel meg egy másik csomópont érvényességi szabályainak. Valójában ez az eltérés a hitelesítéssel kapcsolatos incidensek elsődleges oka. Ugyanakkor ezeknek a szabályoknak a elkülönítése lehetővé teszi, hogy a fürt továbbra is működne a frissítés során, amely módosítja a fürt biztonsági beállításait. Vegye figyelembe, hogy az érvényesítési szabályok első lépéseként történő bővítésével a fürt összes csomópontja az új beállításokhoz fog közeledni, miközben továbbra is használja az aktuális hitelesítő adatokat. 

Emlékezzünk vissza, hogy egy Service Fabric-fürtben a frissítés (legfeljebb 5) "frissítési tartományok" vagy ud-k között halad előre. Csak az aktuális UD-ban lévő csomópontok frissülnek/módosulnak egy adott időpontban, és a frissítés csak akkor folytatódik a következő UD-re, ha a fürt rendelkezésre állása ezt lehetővé teszi. (További részletekért tekintse meg a [Service Fabric-fürt frissítéseit](service-fabric-cluster-upgrade.md) és az ugyanak a témával kapcsolatos egyéb cikkeket.) A tanúsítvány-biztonsági módosítások különösen kockázatosak, mivel elkülöníthetik a csomópontokat a fürttől, vagy a fürtet a kvórumveszteség szélén hagyhatják.

A következő jelöléssel fogjuk leírni a csomópont biztonsági beállításait:

Nk: {P:{TP=A}, V:{TP=A}}, ahol:
  - Az "Nk" egy csomópontot jelöl a *k* frissítési tartományban
  - A "P" a csomópont jelenlegi megjelenítési szabályait jelöli (feltéve, hogy csak fürttanúsítványokra hivatkozunk); 
  - A "V" a csomópont aktuális érvényességi szabályait jelöli (csak fürttanúsítványesetén)
  - A "TP=A" egy ujjlenyomat-alapú deklarációt (TP) jelöl, az "A" pedig a tanúsítvány ujjlenyomata
  - A "CN=B" egy közös névalapú nyilatkozatot (CN) jelöl, amelynek a "B" a tanúsítvány tulajdonosának köznapi neve 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Az ujjlenyomattal deklarált fürttanúsítvány elforgatása
A következő sorozat azt ismerteti, hogy a kétlépcsős frissítés hogyan használható egy ujjlenyomattal deklarált másodlagos fürttanúsítvány biztonságos bevezetésére; az első szakasz bevezeti az új tanúsítványnyilatkozatot az érvényesítési szabályokba, a második szakasz pedig bevezeti azt a bemutatási szabályokba:
  - kezdeti állapot: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - a fürt inverészett, minden csomópont közös konfigurációval rendelkezik
  - 0. frissítési tartomány befejezésekor: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - az UD0 csomópontjai bemutatják az A tanúsítványt, és elfogadják az A vagy B tanúsítványokat; az összes többi csomópont, amely csak az A bizonyítványt fogadja el és fogadja el
  - az utolsó frissítési tartomány befejezésekor: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} – minden olyan csomópont, amely az A tanúsítványt mutatja, minden csomópont elfogadná az A vagy a B tanúsítványt
      
Ezen a ponton a fürt ismét egyensúlyban van, és a frissítés/a biztonsági beállítások módosítása második fázisa megkezdődhet:
  - 0 frissítési tartomány befejezésekor: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} – az UD0 csomópontjai megkezdik a B bemutatót, amelyet a fürt bármely más csomópontja elfogad.
  - az utolsó frissítési tartomány befejezésekor: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}} – minden csomópont áttért a B tanúsítvány bemutatására.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Fürt átalakítása ujjlenyomatból köznapi névalapú tanúsítványdeklarációkká
Hasonlóképpen, a tanúsítványdeklaráció típusának módosítása (ujjlenyomatról közönséges névre) ugyanazt a mintát fogja követni, mint fent. Vegye figyelembe, hogy az érvényességi szabályok lehetővé teszik egy adott szerepkör tanúsítványainak deklarálását ujjlenyomattal és közös névvel ugyanabban a fürtdefinícióban. Ezzel szemben a bemutatási szabályok csak egy nyilatkozatformát engedélyeznek. A fürttanúsítvány ujjlenyomatról köznapi névre történő átalakításának biztonságos megközelítése egyébként a tervezett céltanúsítvány bevezetése először ujjlenyomattal, majd a deklaráció módosítása egy közös névalapúra. A következő példában azt feltételezzük, hogy az "A" ujjlenyomat és a "B" tulajdonos köznapi neve ugyanarra a tanúsítványra vonatkozik. 

  - kezdeti állapot: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - a fürt inverészett, minden csomópont közös konfigurációban van, és az A az elsődleges tanúsítvány ujjlenyomata
  - 0. frissítési tartomány befejezésekor: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - az UD0 csomópontjai bemutatják az A tanúsítványt, és elfogadják az A ujjlenyomatú vagy B nevű tanúsítványokat; az összes többi csomópont, amely csak az A bizonyítványt fogadja el és fogadja el
  - az utolsó frissítési tartomány befejezésekor: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} – minden olyan csomópont, amely az A tanúsítvánnyal van jelen, minden csomópont elfogadná az A (TP) vagy a B (CN) tanúsítványt

Ezen a ponton folytathatjuk a bemutató szabályok módosítását egy későbbi frissítéssel:
  - 0. frissítési tartomány befejezésekor: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - az UD0 csomópontjai bemutatják a CN által talált B tanúsítványt, és elfogadják az A ujjlenyomatú vagy a B betűs nyomatú tanúsítványokat; az ujjlenyomattal kiválasztott összes többi csomópont, amely csak az A tanúsítványt fogadja el
  - az utolsó frissítési tartomány befejezésekor: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{CN=B}, V:{TP=A, CN=B}} - a CN által talált B tanúsítványt kiállító összes csomópont, az összes csomópont elfogadná az A (TP) vagy a B (CN) tanúsítványt.
    
a második fázis befejezése egyben a fürt közös névalapú tanúsítványokra való átalakítását is jelzi; az ujjlenyomat-alapú érvényesítési deklarációk egy későbbi fürtfrissítés során eltávolíthatók.

> [!NOTE]
> Az Azure Service Fabric-fürtökben a fent bemutatott munkafolyamatokat a Service Fabric erőforrás-szolgáltató vezényli; A fürt tulajdonosa továbbra is felelős a tanúsítványok fürtbe való kiépítéséért a jelzett szabályok (megjelenítés vagy érvényesítés) szerint, és arra ösztönzik, hogy több lépésben hajtson végre módosításokat.

Egy külön cikkben foglalkozik a témakör a tanúsítványok kezelése és kiépítése egy Service Fabric-fürtbe.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Hibaelhárítás és gyakori kérdések
Bár a Service Fabric-fürtök hitelesítéssel kapcsolatos problémáinak hibakeresése nem könnyű, reméljük, hogy a következő tippek és tippek segíthetnek. A legegyszerűbb módja a vizsgálatok megkezdésének, hogy vizsgálja meg a Service Fabric eseménynaplók a fürt csomópontjain - nem feltétlenül csak a tüneteket mutató, hanem a csomópontok, amelyek fel, de nem tudnak csatlakozni az egyik a szomszédok. A Windows rendszerben a fontos események általában az "Alkalmazások és szolgáltatások naplói\Microsoft-ServiceFabric\Admin" vagy "Működési" csatornákon vannak bejelentkezve. Néha hasznos [lehet, hogy lehetővé CAPI2 fakitermelés](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues), hogy elfog további részleteket illetően a tanúsítvány érvényesítése, visszakeresése CRL / CTL stb (Ne feledje, hogy tiltsa le befejezése után a repro, akkor elég részletes.)

A hitelesítési problémákkal küzdő fürtben jelentkező tipikus tünetek a következők: 
  - a csomópontok nem/kerékpároznak 
  - a rendszer elutasítja a csatlakozási kísérleteket
  - csatlakozási kísérletek időtúllépés

Minden tünetet különböző problémák okozhatnak, és ugyanaz a kiváltó ok különböző megnyilvánulásokat mutathat; mint ilyen, akkor csak felsorolni egy kis mintát a tipikus problémák, ajánlásokat rögzítő őket. 

* A csomópontok üzeneteket válthatnak, de nem tudnak csatlakozni. A kapcsolatmegszakítások lehetséges oka a "tanúsítvány nem egyeztetett" hiba – a Service Fabric-service Fabric-kapcsolatok egyik fele olyan tanúsítványt mutat be, amely nem felel meg a címzett ellenőrzési szabályainak. A következő hibák bármelyike kísérheti: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  További diagnosztizálás/vizsgálat: a kapcsolatot megkísérelő csomópontok mindegyikén határozza meg, hogy melyik tanúsítvány jelenik meg; megvizsgálja a tanúsítványt, és megpróbálja emulálni az érvényesítési szabályokat (ellenőrizze az ujjlenyomatot vagy a köznapi névegyenlőséget, ellenőrizze a kiállító ujjlenyomatait, ha meg van adva).

  Egy másik gyakori kísérő hibakód lehet:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  Ebben az esetben a tanúsítványt köznapi névvel deklarálják, és a következők bármelyike alkalmazandó:
    - a kibocsátók nincsenek rögzítve, és a főtanúsítvány nem megbízható, vagy
    - a kibocsátókat rögzítik, de a nyilatkozat nem tartalmazza a tanúsítvány közvetlen kibocsátójának ujjlenyomatát

* A csomópont fel van kapcsolva, de nem tud csatlakozni más csomópontokhoz; más csomópontok nem fogadnak bejövő forgalmat a meghibásodott csomóponttól. Ebben az esetben lehetséges, hogy a tanúsítvány betöltése sikertelen a helyi csomóponton. Keresse meg a következő hibákat:
  - tanúsítvány nem található - győződjön meg arról, hogy a megjelenítési szabályokban deklarált tanúsítványokat a LocalMachine\My (vagy a megadott) tanúsítványtároló tartalma feloldhatja. 
    A hiba lehetséges okai a következők lehetnek: 
      - érvénytelen karakterek az ujjlenyomat-deklarációban
      - a tanúsítvány nincs telepítve
      - a tanúsítvány lejárt
      - a köznév-deklaráció tartalmazza a "CN=" előtagot
      - a deklaráció helyettesítő karaktert ad meg, és nincs pontos egyezés a tanúsítványtárolóban (deklaráció: CN=*.mydomain.com, tényleges tanúsítvány: CN=server.mydomain.com)

  - ismeretlen hitelesítő adatok - a tanúsítványnak megfelelő hiányzó személyes kulcsot jelöl, amelyet általában hibakód kísér: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    A megoldásérdekében ellenőrizze a személyes kulcs létezését; ellenőrizze, hogy az SFAdmins "read|execute" hozzáférést kap-e a személyes kulcshoz.

  - rossz szolgáltatótípus - új kriptográfiai tanúsítványt ("Microsoft Software Key Storage Provider") jelöl; ebben az időben a Service Fabric csak a CAPI1 tanúsítványokat támogatja. Általában hibakód kíséri:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    A hiba orvoslásához hozza létre újra a fürttanúsítványt capi1 (pl. "Microsoft Enhanced RSA és AES kriptográfiai szolgáltató") szolgáltató használatával. A kriptográfiai szolgáltatókkal kapcsolatos további részletekért olvassa el [a Kriptográfiai szolgáltatók ismertetése című témakört.](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

