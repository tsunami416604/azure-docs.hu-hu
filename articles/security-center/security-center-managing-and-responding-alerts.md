<properties
   pageTitle="Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben | Microsoft Azure"
   description="Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="yurid"/>

# Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben
Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában.

> [AZURE.NOTE] A speciális észlelések eléréséhez frissítsen az Azure Security Center Standard verzióra. Igénybe veheti a 90 napos ingyenes próbaverziót. A frissítéshez a [Biztonsági szabályzat](security-center-policies.md) beállításnál válassza ki a kívánt tarifacsomagot. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).


## Mik azok a biztonsági riasztások?
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek. Az Azure Security Center ezenkívül [incidensekbe](security-center-incident.md) összesíti azokat a riasztásokat, amelyek törlési láncot alkotó mintát jeleznek. 

> [AZURE.NOTE] Ha részletes tájékoztatást szeretne kapni a Security Center észlelési funkcióinak működéséről, olvassa el [Az Azure Security Center észlelési funkciói](security-center-detection-capabilities.md) című cikket.


## Biztonsági riasztások kezelése

A **Biztonsági riasztások** csempén áttekintheti az aktuális riasztásokat. Nyissa meg az Azure Portal webhelyet, és az egyes riasztásokkal kapcsolatos további részletek megjelenítéséhez kövesse az alábbi lépéseket:

1. A Security Center irányítópultján található a **Biztonsági riasztások** csempe.

    ![A Biztonsági riasztások csempe a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2.  A csempére kattintva nyissa meg a **Biztonsági riasztások** panelt, amely a lentiek szerint további információkat tartalmaz a riasztásról.

    ![A Biztonsági riasztások panel a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

A panel alsó részén találhatók az egyes riasztások részletei. Rendezésükhöz kattintson arra az oszlopra, amely szerint rendezni szeretné a riasztásokat. Az oszlopok meghatározása:

- **Riasztás:** a riasztás rövid leírása.
- **Szám:** az adott típusú riasztások listája egy adott napra vonatkozóan.
- **Észlelte:** a riasztás kiváltásáért felelős szolgáltatás.
- **Dátum:** Az a dátum, amelyen az esemény történt.
- **Állapot:** A riasztás aktuális állapota. Kétféle állapot létezik:
    - **Aktív:** A biztonsági riasztást észlelték.
    - **Elvetve:** A felhasználó elvetette a biztonsági riasztást. Általában azoknak a riasztásoknak ez az állapota, amelyeket megvizsgáltak, és vagy megoldottak, vagy azok nem bizonyultak tényleges támadásnak.

- **Súlyosság:** A súlyosság szintje lehet magas, közepes vagy alacsony.

### A riasztások szűrése

A riasztások dátum, állapot és súlyosság alapján szűrhetők. A riasztások szűrése olyan esetekben lehet hasznos, amikor szűkíteni kell a megjelenített biztonsági riasztások körét. Például olyankor, ha az elmúlt 24 órában történt biztonsági riasztásokat szeretné kezelni, mert egy, a rendszerbe történő lehetséges behatolást vizsgál.

1. Kattintson a **Szűrő** lehetőségre a **Biztonsági riasztások** panelen. A **Szűrő** panel megnyílása után válassza ki azokat a dátumra, állapotra és súlyosságra vonatkozó értékeket, amelyeket meg kíván tekinteni.

    ![A riasztások szűrése a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-ga.png)

2.  A biztonsági figyelmeztetés vizsgálatát követően kiderülhet, hogy az adott környezetre nézve a riasztás vakriasztás volt, vagy hogy egy bizonyos erőforrás esetén az észlelt viselkedés megegyezik a várt viselkedéssel. Ha úgy dönt, hogy egy biztonsági riasztás nem alkalmazandó, elvetheti és szűréssel eltüntetheti a riasztást a megjelenített riasztások közül. Egy biztonsági riasztást két módon lehet elvetni. Kattintson jobb gombbal a riasztásra, és válassza az **Elvetés** lehetőséget, vagy mozgassa az egérmutatót egy elemre, kattintson a jobb oldalon megjelenő három pontra, és válassza az **Elvetés** lehetőséget. A **Szűrő** lehetőségre kattintva és az **Elvetve** lehetőséget választva megtekintheti az elvetett biztonsági riasztásokat.

    ![A riasztások elvetése a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4-ga.png)

### Válaszadás a biztonsági riasztásokra

Válasszon ki egy biztonsági riasztást, ha többet szeretne megtudni a riasztást kiváltó esemény(ek)ről, és arról, hogy milyen lépéseket kell tennie a támadás elhárítása érdekében. A biztonsági riasztások típus és dátum szerint vannak csoportosítva. Ha valamelyik biztonsági riasztásra kattint, megnyílik egy panel, amely a csoportosított riasztások listáját tartalmazza.

![Válaszadás a biztonsági riasztásokra az Azure Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Ebben az esetben a riasztásokat gyanús RDP-tevékenység váltotta ki. Az első oszlopban a támadásnak kitett erőforrások jelennek meg, a másodikban az erőforrást érő támadások száma szerepel, a harmadikban a támadás időpontja látható, a negyedik a riasztás állapotát mutatja, az ötödik pedig a riasztás súlyosságát. Az információk áttekintése után kattintson a támadásnak kitett erőforrásra, és megjelenik egy új panel.

![Javaslatok az Azure Security Centerben: mi a teendő a biztonsági riasztással kapcsolatban](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

A panel **Leírás** mezőjében további információkat talál az eseményről. Ezek az információk áttekintést nyújtanak a biztonsági riasztás kiváltó okáról, a célerőforrásról, (adott esetben) a forrás IP-címéről, valamint a fenyegetés elhárítására vonatkozó javaslatokról.  Bizonyos esetekben a forrás IP-címe üres lesz (nem érhető el), mert a Windows nem minden biztonsági eseménynaplója tartalmazza az IP-címet.

> [AZURE.NOTE] A Security Center által javasolt elhárítási műveletek a biztonsági riasztástól függően különbözők lehetnek. Bizonyos esetekben előfordulhat, hogy az Azure egyéb funkcióit kell használnia a javasolt elhárítási művelet végrehajtásához. A példánkban a támadás elhárításának javasolt módja az, hogy a támadást végrehajtó IP-címet tiltólistára kell helyezni egy [hálózati hozzáférés-vezérlési lista](../virtual-network/virtual-networks-acl.md) vagy egy [hálózati biztonsági csoport](../virtual-network/virtual-networks-nsg.md) típusú szabály használatával.

## Biztonsági riasztások típus szerint
A más típusú riasztásokat a gyanús RDP-tevékenységet jelző riasztások elérésére használt lépésekkel érheti el. Az alábbiakban néhány példa szemlélteti, hogy milyen riasztások jelenhetnek meg a Security Centerben:

### Potenciális SQL-injektálás
Az SQL-injektálás olyan támadás, amely kártékony kódot szúr be a karakterláncokba, amelyeket a rendszer később átad az SQL Server példányának elemzés és végrehajtás céljából. Az SQL-utasításokat létrehozó összes eljárást meg kell vizsgálni az injektálási biztonsági rések felderítéséhez, mivel az SQL Server végrehajtja az összes olyan lekérdezést, amely szintaktikailag érvényes. 

![SQL-injektálási riasztás](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9.png) 

Ez a riasztás olyan információt jelenít meg, amelynek alapján azonosíthatja a megtámadott erőforrást, az észlelés idejét, a támadás állapotát, valamint a kivizsgálás további lépéseire mutató hivatkozást is tartalmaz.

### Gyanús kimenő forgalom észlelhető

A hálózati eszközök ugyanolyan módon deríthetők fel és határozhatók meg, mint a más típusú rendszerek. A támadók általában a portkereséssel kezdik. Az alábbi példában olyan gyanús SSH-adatforgalom érkezik egy virtuális gépről, amely SSH találgatásos vagy portkereséses támadást hajthat végre egy külső erőforrás ellen. 

![Gyanús kimenő forgalom riasztása](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig-10.png)

Ez a riasztás olyan információt tartalmaz, amelyből meghatározhatja a támadás elindítására használt erőforrást, a feltört gépet, az észlelés idejét, valamint a használt protokollt és portot. Ezen a panelen megjelenik a probléma megoldásához felhasználható javítási lépések listája is.

### Kártékony géppel folytatott hálózati kommunikáció
 
Az Azure Security Center a Microsoft fenyegetésfelderítő hírcsatornáinak használatával észlelni tudja a kártékony IP-címmel kommunikáló feltört gépeket, amelyek sok esetben parancs- és vezérlési központként működnek. Ebben az esetben az Azure Security Center azt észlelte, hogy a kommunikáció a Pony Loader (más néven [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)) kártevő használatával történt.

![Kártékony géppel folytatott hálózati kommunikáció riasztása](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9-ga.png)

Ez a riasztás olyan információt tartalmaz, amelyből azonosíthatja a támadás elindítására használt erőforrást, a megtámadott erőforrást, az áldozat IP-címét, a támadó IP-címét, valamint az észlelés idejét.

> [AZURE.NOTE] A valódi IP-címek adatvédelmi okból el lettek távolítva erről a képernyőfelvételről.

### Héjkód észlelhető 

A héjkód az a kártékony kód, amely azután fut le, hogy a kártevő a szoftver biztonsági rését kihasználva bejutott a rendszerbe.  Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan végrehajtható kódot talált, amely a kártékony kódokra jellemző működés jeleit mutatja.  Bár előfordulhat, hogy nem rosszindulatú szoftverhez tartozik az adott működés, ez nem jellemző a szokásos szoftverfejlesztési gyakorlatban. 

Az összeomlási memóriaképes riasztások a következő mezőket tartalmazzák:

- DUMPFILE: az összeomlási memóriakép fájljának neve. 
- PROCESSNAME: az összeomlott folyamat neve. 
- PROCESSVERSION: az összeomlott folyamat verziója. 

Ez a riasztás a következő mezőt is tartalmazza:

- ADDRESS: a héjkód helye a memóriában.

Példa az ilyen típusú riasztásra:

![Héjkód riasztása](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig10-ga.png)

### Kódinjektálás észlelhető

A kódinjektálás olyan művelet, amely végrehajtható modulokat szúr be a futó folyamatokba vagy szálakba.  A kártevők ezt a módszert használják az adatok elérésére, elrejtésére vagy az eltávolítás megakadályozására (vagyis az adatok megőrzésére).  Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése injektált modult észlelt az memóriaképben.
 
A megbízható szoftverfejlesztők esetenként nem ártó szándékkal hajtanak végre kódinjektálást, hanem például egy meglévő alkalmazás vagy az operációs rendszer egyik összetevőjének módosítása vagy bővítése érdekében.  A kártékony és a nem kártékony injektált modulok megkülönböztetésének elősegítéséhez az Azure Security Center ellenőrzi, hogy az injektált modulra illik-e a gyanús működés profilja. Az ellenőrzés eredménye a riasztás „SIGNATURE” mezőjében látható, és ettől függ a riasztás súlyossági szintje, a riasztás leírása és a hibaelhárítási művelet.  

A fenti „Héjkód észlelhető” szakaszban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

- ADDRESS: az injektált modul helye a memóriában.
- IMAGENAME: Az injektált modul neve. Vegye figyelembe, hogy ez a mező üres lehet, ha a rendszerkép neve nincs megadva a rendszerképben.
- SIGNATURE: azt jelzi, hogy az injektált modulra illik-e a gyanús működés profilja. A következő táblázat tartalmazza az eredmények példáit, kiegészítve a leírásukkal:

| **Aláírás értéke**                  | **Leírás**                                                                                                   |
|--------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Gyanús visszatükröző betöltő típusú biztonsági rés | Ez a gyanús működés gyakran az injektált kódnak az operációs rendszer betöltőjétől független betöltésére utal. |
| Gyanús injektált kód típusú biztonsági rés          | Olyan kártékony műveletet jelez, amely gyakran a memóriába történő kódinjektálásra utal.                                       |
| Gyanús injektálás típusú biztonsági rés         | Olyan kártékony műveletet jelez, amely gyakran a memóriába injektált kód használatára utal.                                   |
| Gyanús injektált hibakereső típusú biztonsági rés | Olyan kártékony műveletet jelez, amely gyakran egy hibakereső észlelésére vagy megkerülésére utal.                         |
| Gyanús injektált távoli parancs típusú biztonsági rés   | Olyan kártékony műveletet jelez, amely gyakran „parancs és vezérlés” (C2) forgatókönyvre utal.                                 |

Példa az ilyen típusú riasztásra:

![Kódinjektálás észlelhető](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig11-ga.png)

### Moduleltérítés észlelhető

A Windows dinamikus csatolású kódtárai (DLL) teszik lehetővé, hogy a szoftverek használják a Windows közös rendszerfunkcióit.  DLL-eltérítés akkor történik, ha a kártevő megváltoztatja a DLL-betöltési sorrendet, hogy kártékony kódot töltsön be a memóriába, ahol tetszőleges kódot lehet végrehajtani. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése egy hasonló nevű modul betöltését észlelte két különböző elérési útról, és az egyik elérési út a Windows rendszer bináris rendszerfájljainak helyére mutat.

A megbízható szoftverfejlesztők esetenként nem ártó szándékkal változtatják meg a DLL-ek betöltési sorrendjét, hanem például a Windows operációs rendszer vagy a Windows-alkalmazások kialakításához vagy bővítéséhez.  A DLL-betöltési sorrend ártó szándékú és esetleg jóindulatú megváltoztatásának megkülönböztetéséhez az Azure Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja.   Az ellenőrzés eredménye a riasztás „SIGNATURE” mezőjében látható, és ettől függ a riasztás súlyossági szintje, a riasztás leírása és a hibaelhárítási művelet.  Az eltérítést végző modul lemezen lévő példányának elemzésével, például a fájlok digitális aláírásának ellenőrzésével vagy víruskeresés futtatásával további adatokhoz juthat az eltérítést végző modul megbízható vagy rosszindulatú jellegére vonatkozóan.

A fenti „Héjkód észlelhető” szakaszban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

- SIGNATURE: azt jelzi, hogy az eltérítést végző modulra illik-e a gyanús működés profilja.
- HIJACKEDMODULE: a Windows eltérített rendszermoduljának neve.
- HIJACKEDMODULEPATH: a Windows eltérített rendszermoduljának elérési útja.
- HIJACKINGMODULEPATH: az eltérítést végző modul elérési útja. 

Példa az ilyen típusú riasztásra:

![DLL-eltérítés riasztása](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig12-ga.png)

### Álcázásos Windows-modul észlelhető

A kártevők felhasználhatják a Windows rendszerfájljainak (például: SVCHOST.EXE) vagy moduljainak (például: NTDLL.DLL) nevét arra, hogy „elvegyüljenek” és elfedjék a szoftver kártékony jellegét a rendszergazdák elől.  Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan modulokat észlelt a memóriaképben, amelyek a Windows rendszermoduljainak nevét használják, de nem felelnek meg a jellemzően a Windows-modulokra vonatkozó feltételeknek. Az álcázásos modul lemezen lévő példányának elemzésével további adatokat kaphat a modul megbízható vagy kártékony jellegére vonatkozóan. Az elemzés a következőket tartalmazhatja:

- Ellenőrizze, hogy a szóban forgó fájl egy megbízható szoftvercsomaghoz tartozik-e.
- Ellenőrizze a fájl digitális aláírását. 
- Futtasson víruskeresést a fájlon.

A fenti „Héjkód észlelhető” szakaszban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

- DETAILS: leírja, hogy a modulok metaadatai érvényesek-e, és hogy a modul a rendszer elérési útjáról lett-e betöltve.
- NAME: az álcázásos Windows-modul neve.
- PATH: az álcázásos Windows-modul elérési útja.

Ez a riasztás ezenkívül kiolvas és megjelenít bizonyos mezőket a modul PE fejlécéből, például a „CHECKSUM” és a „TIMESTAMP” mezőt.  Ezek a mezők csak akkor jelennek meg, ha a mezők szerepelnek a modulban. A mezők részletes ismertetése a [Microsoft PE és COFF specifikációjában](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) található.

Példa az ilyen típusú riasztásra:

![Álcázásos modul riasztása](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig13-ga.png)

### Módosított bináris rendszerfájl észlelhető 

A kártevők módosíthatják a rendszermag bináris fájljait, hogy magukat leplezve hozzáférhessenek az adatokhoz, vagy elrejtőzzenek a fertőzött rendszerben.  Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése a Windows operációs rendszer módosított bináris rendszerfájljait észlelte a memóriában vagy a lemezen. 

A megbízható szoftverfejlesztők esetenként nem ártó szándékkal módosítják a memóriában lévő rendszermodulokat, hanem például elkerülő megoldásokhoz vagy az alkalmazások kompatibilitásához. A kártékony és a valószínűleg jóindulatú modulok megkülönböztetéséhez az Azure Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja.   Az ellenőrzés eredményét a riasztás súlyossági szintje, a riasztás leírása és a hibaelhárítási művelet jelzi. 

A fenti „Héjkód észlelhető” szakaszban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

- MODULENAME: a módosított bináris rendszerfájl neve. 
- MODULEVERSION: a módosított bináris rendszerfájl verziója.

Példa az ilyen típusú riasztásra:

![Módosított bináris rendszerfájl riasztása](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig14-ga.png)


## Lásd még:

Ebből a dokumentumból megismerte a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
- [Az Azure Security Center észlelési funkciói](security-center-detection-capabilities.md)
- [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=sep16_HO1-->


