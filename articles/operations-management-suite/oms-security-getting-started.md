---
title: "Az Operations Management Suite biztonsági és auditálási megoldás használatának első lépései | Microsoft Docs"
description: "Ez a dokumentum segít használatba venni az Operations Management Suite biztonsági és auditálási megoldás hibrid felhők figyelésére szolgáló képességeit."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 754796ef-a43e-468a-86c9-04a2eda55b5b
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: get-started-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 5753511d26c06f385fd4ff717d8592c321338172
ms.contentlocale: hu-hu
ms.lasthandoff: 06/17/2017


---
<a id="getting-started-with-operations-management-suite-security-and-audit-solution" class="xliff"></a>

# Az Operations Management Suite biztonsági és auditálási megoldás használatának első lépései
Ez a dokumentum végigkalauzolja Önt az egyes beállításokon, így segít gyorsan használatba venni az Operations Management Suite (OMS) biztonsági és auditálási megoldás képességeit.

<a id="what-is-oms" class="xliff"></a>

## Mi az az OMS?
A Microsoft Operations Management Suite (OMS) a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében. Az OMS termékkel kapcsolatos további információkért tekintse meg az [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx) című cikket.

<a id="oms-security-and-audit-dashboard" class="xliff"></a>

## Az OMS biztonsági és auditálási irányítópultja
Az OMS biztonsági és naplózási megoldás a figyelmet igénylő jelentős problémákat feltáró beépített keresési lekérdezések révén átfogó képet nyújt a szervezet informatikai biztonsági állapotáról. A **Security and Audit** (Biztonsági és naplózás) irányítópult az OMS rendszerben a biztonsággal kapcsolatos összes tényezőt megjelenítő kezdőoldal. Magas szintű betekintést tesz lehetővé a számítógépek biztonsági állapotába. Olyan funkciót is kínál, amellyel megjeleníthető az elmúlt 24 óra, 7 nap vagy bármely más egyéni időszak összes eseménye. A **Security and Audit** (Biztonság és auditálás) irányítópult megnyitásához kövesse az alábbi lépéseket:

1. A **Microsoft Operations Management Suite** fő irányítópultján kattintson a **Settings** (Beállítások) csempére a bal oldalon.
2. A **Settings** (Beállítások) panel **Solutions** (Megoldások) területén kattintson a **Security and Audit** (Biztonság és auditálás) lehetőségre.
3. Megjelenik a **Security and Audit** (Biztonság és auditálás) irányítópult:
   
    ![Az OMS biztonsági és auditálási irányítópultja](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Ha első alkalommal nyitja meg ezt az irányítópultot, és még nem rendelkezik az OMS használatával figyelt eszközökkel, akkor a csempéken nem jelennek meg az ügynöktől kapott adatok. Az ügynök telepítése után eltarthat egy ideig a csempék adatokkal való feltöltése, ezért az először megjelenő adatok hiányosak lehetnek (mivel az adatok felhőbe való feltöltése ilyenkor még folyamatban van).  Ebben az esetben normális jelenség, hogy egyes csempéken nem jelennek meg lényeges adatok. Az OMS-ügynök Windows rendszerekre telepítésével kapcsolatos további információkért olvassa el a [Windows rendszerű számítógépek közvetlen csatlakoztatás a Log Analytics rendszerhez](https://technet.microsoft.com/library/mt484108.aspx) című, az OMS-ügynök Linux rendszerekre telepítésével kapcsolatos további információkért pedig a [Linux rendszerű számítógépek közvetlen csatlakoztatás a Log Analytics rendszerhez](https://technet.microsoft.com/library/mt622052.aspx) című cikket.

> [!NOTE]
> Az ügynök összegyűjti az adatokat – például a számítógép nevét, az IP-címet és a felhasználó nevét – az aktuális és engedélyezett események alapján. Nem gyűjt azonban dokumentumokat vagy fájlokat, adatbázisneveket, illetve személyes adatokat.   
> 
> 

A megoldások az ügyfelek fontos kihívásaira választ adó logikai, megjelenítési és adatgyűjtési szabályok gyűjteményei. A Biztonság és auditálás is egy ilyen megoldás, további megoldásokat pedig külön adhat hozzá a rendszerhez. Az új megoldások hozzáadásával kapcsolatban olvassa el a [megoldások hozzáadását ismertető](https://technet.microsoft.com/library/mt674635.aspx) cikket.

Az OMS biztonsági és auditálási irányítópultja négy fő kategória alapján van felosztva:

* **Security Domains** (Biztonsági tartományok): ezen a területen vizsgálhatja meg alaposabban a biztonsági rekordok időbeli megoszlását, itt férhet hozzá a kártevők felméréséhez, a frissítések felméréséhez, a biztonsági eseményekkel rendelkező számítógépekhez, valamint innen férhet hozzá gyorsan az Azure Security Center irányítópultjához.
* **Notable Issues** (Jelentős problémák): e lehetőség segítségével gyorsan meghatározhatja az aktív problémák számát, valamint a problémák súlyosságát.
* **Detections (Preview)** (Észlelések (előnézet)): lehetővé teszi a támadási minták azonosítását az erőforrásokkal fellépő biztonsági riasztások képi megjelenítése révén.
* **Threat Intelligence**(Fenyegetések felderítése): lehetővé teszi a támadási minták felderítését a rosszindulatú kimenő forgalommal rendelkező összes kiszolgálónak, a rosszindulatú fenyegetések típusának, valamint egy IP-címeket ábrázoló térképnek a képi megjelenítésével. 
* **Common security queries** (Gyakori biztonsági lekérdezések): e beállítás listát jelenít meg azokról leggyakoribb biztonsági lekérdezésekről, amelyek használatával Ön figyelheti a környezetét. Amikor egy ilyen lekérdezésre kattint, megnyílik a **Search** (Keresés) panel az adott lekérdezés eredményeivel.

> [!NOTE]
> Ha további információkat szeretne arról, hogyan védi meg az OMS az adatait, olvassa el az OMS használatával végzett adatvédelmet ismertető cikket.
> 
> 

<a id="security-domains" class="xliff"></a>

## Biztonsági tartományok
Az erőforrások figyelésekor fontos, hogy gyorsan hozzá tudjon férni a környezet aktuális állapotához. Fontos azonban az is, hogy vissza tudja követni a múltban előforduló eseményeket, és ennek révén jobban meg tudja érteni, hogy mi történik a környezetben egy adott időpontban. 

> [!NOTE]
> Az adatmegőrzés az OMS-díjcsomag szerint alakul. További információkért keresse fel a [Microsoft Operations Management Suite](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx) árképzését ismertető oldalt.
> 
> 

Az incidensekre adott válaszok és a törvényszéki vizsgálati forgatókönyvek esetén közvetlenül megmutatkoznak a **Security Records over Time** (Biztonsági rekordok időbeli megoszlása) csempén rendelkezésre álló adatok előnyei.

![Biztonsági rekordok időbeli megoszlása](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

Amikor erre a csempére kattint, megnyílik a **Search** (Keresés) panel, és megjelenik rajta a **Security Events** (Biztonsági események) lekérdezés (Type=SecurityEvents) eredménye az elmúlt hét nap adatai alapján, az alább látható módon:

![Biztonsági rekordok időbeli megoszlása](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

A keresési eredmények két panelre vannak osztva: a bal oldali panelen a talált biztonsági események, az eseményekkel rendelkező számítógépek, az e számítógépen felderített fiókok száma, valamint a tevékenységek típusa látható lebontva. A jobb oldali panelen látható az összes eredmény, valamint a biztonsági események időrendi nézete, a számítógép nevével és az esemény tevékenységével együtt. A **Show More** (Továbbiak megjelenítése) lehetőségre kattintva az esemény további részleteit, például az esemény adatait, az esemény azonosítóját, valamint az esemény forrását tekintheti meg.

> [!NOTE]
> Az OMS keresési lekérdezéseivel kapcsolatos további információkért olvassa el az [OMS keresési referenciáját bemutató cikket](https://technet.microsoft.com/library/mt450427.aspx).
> 
> 

<a id="antimalware-assessment" class="xliff"></a>

### Kártevőirtók felmérése
Ezzel a funkcióval gyorsan azonosíthatja a nem megfelelő védelemmel rendelkező számítógépeket, valamint azokat, amelyek egy kártevő miatt vannak veszélyben. A rendszer beolvassa a figyelt kiszolgálókról a kártevők felmérésének állapotát, valamint az észlelt fenyegetéseket, majd az adatokat elküldi a felhőben működő OMS szolgáltatásnak feldolgozásra. Az észlelt fenyegetésekkel rendelkező, illetve a nem megfelelő védelmet élvező kiszolgálók megjelennek a kártevőfelmérési irányítópulton, amelyet az **Antimalware Assessment** (Kártevőirtók felmérése) csempére kattintva nyithat meg. 

![kártevők felmérése](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Akárcsak az OMS irányítópultján rendelkezésre álló többi élő csempe esetén, erre a csempére rákattintva a **Search** (Keresés) panel nyílik meg a lekérdezés eredményével. E beállítás esetén, ha a **Not Reporting** (Nincs jelentéskészítés) lehetőségre kattint a **Protection Status** (Védelem állapota) területen, akkor az alábbi módon ez az egy bejegyzés jelenik meg, amely tartalmazza a számítógép nevét és a hozzá tartozó rangot:

![keresési eredmény](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [!NOTE]
> A *rang* olyan helyezés, amelyet a rendszer a védelem állapota (bekapcsolt, kikapcsolt, frissített stb.), illetve a talált fenyegetések alapján állapít meg. Ennek számszerűsítése segíti az összesítést.
> 
> 

Ha a számítógép nevére kattint, akkor időrend szerinti nézetben jelenik meg a számítógép védelmi állapota. Ez azokban az esetekben nagyon hasznos, amikor azt kell felismerni, hogy a kártevőirtót korábban telepítették, majd egy későbbi időpontban eltávolították.   

<a id="update-assessment" class="xliff"></a>

### Frissítések felmérése
Ez a funkció lehetővé teszi a potenciális biztonsági problémáknak való teljes kitettség gyors meghatározását, valamint annak megállapítását, hogy ezek a frissítések fontosak-e, és ha igen, mennyire fontosak a környezet szempontjából. Az OMS biztonsági és auditálási megoldás csak a frissítések képi megjelenítését biztosítja, a tényleges adatok az OMS rendszeren belüli másik, [Frissítéskezelési megoldások](oms-solution-update-management.md) nevű modulból származnak. Alább látható egy példa a frissítésekre:

![rendszerfrissítések](./media/oms-security-getting-started/oms-getting-started-fig6-new.png)

> [!NOTE]
> A frissítéskezelési megoldással kapcsolatos további információkért olvassa el [az OMS frissítéskezelési megoldását ismertető cikket](oms-solution-update-management.md).
> 
> 

<a id="identity-and-access" class="xliff"></a>

### Identitás és hozzáférés
Az identitásnak kell lennie a vállalat vezérlősíkjának, és az identitás védelmének kell kapnia a legmagasabb prioritást. Míg a múltban a szervezetek körüli szegélyhálózatok szolgáltak az egyik elsődleges védelmi vonalként, napjainkban egyre több adat és alkalmazás kerül a felhőbe, így az identitás lépett a szegélyhálózatok helyére. 

> [!NOTE]
> jelenleg az adatok csak a biztonsági események bejelentkezési adatain (4624-es eseményazonosító) alapulnak, a jövőben azonban az Office365-bejelentkezések és az Azure AD-adatok is bekerülnek a felhasznált adatok körébe.
> 
> 

Az identitástevékenységek figyelésével proaktívan fog tudni cselekedni, mielőtt az incidens bekövetkezne, illetve reaktív tevékenységekkel leállíthatja a támadási kísérleteket. Az **Identity and Access** (Identitás és hozzáférés) irányítópult áttekintést biztosít az identitás állapotáról, többek között a sikertelen bejelentkezési kísérletekről, az e kísérletekhez használt felhasználói fiókról, a kizárt fiókokról, a módosított vagy visszaállított jelszavú fiókokról, valamint az aktuálisan bejelentkezett fiókok számáról. 

Az **Identity and Access** (Identitás és hozzáférés) csempére kattintva a következő irányítópultot láthatja:

![identitás és hozzáférés](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

Az ezen az irányítópulton rendelkezésre álló információk azonnali segítséget nyújtanak a potenciális gyanús tevékenységek azonosításához. Láthatja például, hogy 338 kísérlet történt a **Administrator** (Rendszergazda) felhasználóval megkísérelt bejelentkezésre, és e kísérletek 100%-a sikertelen volt. Ezt a fiók ellen intézett találgatásos támadás okozhatja. Ha rákattint a fiókra, további információk jelennek meg, amelyek segíthetnek meghatározni e potenciális támadás célerőforrását:

![keresési eredmények](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

A részletes jelentés fontos információkat tartalmaz erről az eseményről, többek között megadja a célszámítógépet, a bejelentkezés típusát (ebben az esetben hálózati bejelentkezésről van szó), a tevékenységet (ebben az esetben 4625-ös számú esemény), valamint egy átfogó idősort minden egyes kísérlethez. 

<a id="computers" class="xliff"></a>

### Számítógépek
Ezzel a csempével tekintheti meg az összes olyan számítógépet, amely aktív biztonsági eseményekkel rendelkezik. Amikor erre a csempére kattint, megjelenik a biztonsági eseményekkel rendelkező számítógépek listája, valamint az események száma az egyes számítógépek esetén:

![Számítógépek](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

A vizsgálatot úgy folytathatja, hogy rákattint az egyes számítógépekre, és áttekinti a megjelölt biztonsági eseményeket.

<a id="threat-intelligence" class="xliff"></a>

### Fenyegetések felderítése

Az OMS biztonsági és auditálási megoldásban elérhető fenyegetésfelderítési szolgáltatással az informatikai rendszergazdák azonosíthatják a környezetre leselkedő biztonsági fenyegetéseket, például ha egy adott számítógép egy botnet része. A számítógépek akkor válhatnak egy botnet csomópontjává, ha a támadók illetéktelenül kártevőket telepítenek a számítógépére, amelyek titokban csatlakoztatják a gépet a vezérlőrendszerhez. A szolgáltatás emellett az illegális kommunikációs csatornákról, például a darknetekről érkező potenciális fenyegetéseket is azonosítani tudja. A fenyegetésfelderítéssel kapcsolatos további információért olvassa el [A biztonsági riasztások figyelése és megválaszolása az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-responding-alerts.md) című cikket.

Bizonyos esetekben észlelhet egy potenciálisan kártékony IP-címet, amelyhez az egyik felügyelt számítógépről fértek hozzá:

![fenyegetésfelderítési leképezés](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig6.png)

Ezt és a kategóriába tartozó többi riasztást az OMS Security hozta létre a [Microsoft fenyegetésfelderítő](https://youtu.be/O4WtxgUrDc8) segítségével. A Fenyegetésfelderítő adatait a Microsoft maga gyűjti össze, illetve a vezető fenyegetésfelderítési szolgáltatóktól vásárolja. Ezeket az adatokat rendszeresen frissítik és a gyorsan változó fenyegetésekhez igazítják. A biztonsági riasztások [vizsgálata](https://blogs.technet.microsoft.com/msoms/2016/12/08/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) közben az adatokat jellegükből adódóan érdemes összevonni más biztonságinformáció-forrásokkal. 

<a id="baseline-assessment" class="xliff"></a>

### Az alapkonfiguráció értékelése

A Microsoft a világszerte elhelyezkedő iparági és kormányzati szervezetekkel közösen olyan Windows-konfigurációt határoz meg, amely nagy biztonságú kiszolgálók üzembe helyezését teszi lehetővé. Ez a konfiguráció beállításkulcsokból, auditálási szabályzatok beállításaiból és biztonsági szabályzatok beállításaiból áll, valamint a Microsoft ajánlott értékeit tartalmazza ezekhez a beállításokhoz. Ez a szabályzathalmaz biztonsági alapkonfigurációként ismert. A beállítással kapcsolatos további információkért olvassa el [Az alapkonfiguráció értékelése az Operations Management Suite biztonsági és auditálási megoldásában](oms-security-baseline.md) című cikket.

<a id="azure-security-center" class="xliff"></a>

### Azure Security Center
Ez a csempe gyakorlatilag az Azure Security Center irányítópultjának megnyitására szolgáló parancsikon. Ha további információkat szeretne megtudni erről a megoldásról, olvassa el [Az Azure Security Center használatának első lépései](../security-center/security-center-get-started.md) című cikket.

<a id="notable-issues" class="xliff"></a>

## Jelentős problémák
E beállításcsoport elsődleges célja, hogy gyors áttekintést nyújtson az Ön környezetében fennálló problémákról úgy, hogy a problémákat kritikusként, figyelmeztetésként vagy tájékoztató jellegűként sorolja be. Az Active issue type (Aktív probléma típusa) csempe biztosítja e problémák képi megjelenítését, azonban nem teszi lehetővé a velük kapcsolatos további részletek megtekintését – ehhez a csempe alsó részét kell használni, ahol látható a probléma neve (NAME), a probléma által érintett objektumok száma (COUNT), valamint a probléma súlyossága (SEVERITY).

![Jelentős problémák](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

Láthatja, hogy e problémákat a **Security Domains** (Biztonsági tartományok) csoport különböző területei már lefedték, ez pedig tovább erősíti, hogy e nézet célja a környezet legfontosabb problémáinak megjelenítése egyetlen helyen.

<a id="detections-preview" class="xliff"></a>

## Észlelések (előnézet)
E beállítás elsődleges célja, hogy lehetővé tegye az informatikai részleg számára a környezetre leselkedő potenciális fenyegetések felismerését az észlelések számának és súlyosságának alapján.

![Fenyegetések felderítése](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Ez a beállítás az [incidensre adott válasz vizsgálatának](https://blogs.msdn.microsoft.com/azuresecurity/2016/11/30/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) során is használható az értékelés végrehajtására, valamint a támadásra vonatkozó további információk lekérésére.

> [!NOTE]
> Ha további információkat szeretne megtudni arról, hogyan használható az OMS incidensekre való válaszadáshoz, tekintse meg a következő videót: [How to Leverage the Azure Security Center & Microsoft Operations Management Suite for an Incident Response](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) (Hogyan használható az Azure Security Center és Microsoft Operations Management Suite egy incidensre adott válaszlépések során?).
> 
> 

<a id="threat-intelligence" class="xliff"></a>

## Fenyegetések felderítése
A Biztonság és auditálás megoldás új fenyegetésfelderítési szakaszában számos módon jeleníthetők meg a támadási minták: megjeleníthető például a rosszindulatú kimenő IP-forgalmat generáló kiszolgálók száma, a rosszindulatú fenyegetések típusa, valamint egy, az IP-címeket ábrázoló térkép is. A térkép interaktív, azaz az IP-címekre kattintva további információk jelennek meg.

A térképen sárga gombostűk jelzik a rosszindulatú IP-címekről érkező forgalom forrását. Nem szokatlan jelenség, hogy az internetre csatlakozó kiszolgálók beérkező rosszindulatú forgalomnak vannak kitéve, mégis ajánlott áttekinteni a kísérleteket, és meggyőződni róla, hogy egyikük sem volt sikeres. Ezek a jelzők az IIS-naplókon, valamint a WireData- és Windows tűzfal-naplókon alapulnak.  

![Fenyegetések felderítése](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

<a id="common-security-queries" class="xliff"></a>

## Gyakori biztonsági lekérdezések
A gyakori biztonsági lekérdezések rendelkezésre álló listája hasznosnak bizonyulhat az erőforrások adataihoz való gyors hozzáféréshez, valamint ezen adatok környezeti igények szerinti testreszabásához. E gyakori lekérdezések a következők:

* Minden biztonsági tevékenység
* Biztonsági tevékenységek a gépnév01.contoso.com nevű gépen (a név felülírandó a saját gép nevével)
* Biztonsági tevékenységek a gépnév01.contoso.com nevű gépen a Rendszergazda fiókban (a név felülírandó a saját gép és fiók nevével)
* Bejelentkezési tevékenységek gépenként
* Fiókok, amelyekből leállították a Microsoft Antimalware-t bármely gépen
* Gépek, amelyeken leállították a Microsoft Antimalware folyamatot
* Gépek, amelyeken futtatták a hash.exe fájlt (a név felülírandó a megfelelő folyamat nevével)
* Az összes elindított folyamat
* Bejelentkezési tevékenységek fiókonként
* Fiókok, amelyekből távolról bejelentkeztek a gépnév01.contoso.com nevű gépre (a név felülírandó a saját gép nevével)

<a id="see-also" class="xliff"></a>

## Lásd még:
E dokumentumban az OMS biztonsági és auditálási megoldást mutattuk be. Az OMS által nyújtott védelemmel kapcsolatos további információkat a következő cikkek tartalmaznak:

* [Az Operations Management Suite (OMS) áttekintése](operations-management-suite-overview.md)
* [A biztonsági riasztások figyelése és megválaszolása az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-responding-alerts.md)
* [Az erőforrások figyelése az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-monitoring-resources.md)


