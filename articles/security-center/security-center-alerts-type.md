---
title: "Biztonsági riasztások típus szerint az Azure Security Centerben | Microsoft Docs"
description: "Ez a dokumentum segít megismerni az Azure Security Centerben elérhető biztonsági riasztások típusait."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: bdd7d3d6e532efe7c3ed8225dd29a895725f2ff9


---
# <a name="security-alerts-by-type-in-azure-security-center"></a>Biztonsági riasztások típus szerint az Azure Security Centerben
Ez a dokumentum segít megismerni az Azure Security Centerben elérhető biztonsági riasztások különböző típusait. A riasztások kezelésével kapcsolatos további információkért olvassa el a [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) című cikket.

> [!NOTE]
> A speciális észlelések eléréséhez frissítsen az Azure Security Center Standard verzióra. Igénybe veheti a 90 napos ingyenes próbaverziót. A frissítéshez a [Biztonsági szabályzat](security-center-policies.md) beállításnál válassza ki a kívánt tarifacsomagot. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).
>
>

## <a name="what-type-of-alerts-are-available"></a>Milyen típusú riasztások állnak rendelkezésre?
Az Azure Security Center különböző riasztásokat biztosít, amelyek igazodnak a számítógépes törlési lánc szakaszaihoz. Az alábbi ábrán néhány példa látható a különböző riasztásokra, amelyek a következő szakaszok valamelyikéhez kapcsolódnak.

![Törlési lánc](./media/security-center-alerts-type/security-center-alerts-type-fig1.png)

**Cél és támadás**

* Bejövő RDP-/SSH-támadások
* Alkalmazás, és DDoS-támadások (WAF-partnerek)
* Behatolásészlelés (NG tűzfal-partnerek)

**Telepítés és biztonsági rések kihasználása**

* Kártékonyként ismert aláírások (AM-partnerek)
* Memóriában lévő kártevők és biztonsági rések kihasználására tett kísérletek
* Gyanús folyamat végrehajtása
* Felderítés elkerülése érdekében tett manőverek
* Oldalirányú mozgás
* Belső felderítés
* Gyanús PowerShell-tevékenység

**Utólagos illetéktelen behatolás**  

* Kommunikáció kártékonyként ismert IP-címre (adatok kiszűrése vagy parancs és vezérlés)
* Feltört erőforrások használata további támadások elvégzésére (kimenő port keresése, RDP/SSH találgatásos támadások és levélszemét)

Minden egyes szakaszhoz különböző típusú támadások tartoznak, és azok különböző alrendszereket céloznak meg. A Security Center három riasztási kategóriával rendelkezik a támadások ezen szakaszokban történő elhárításához:

* Virtuális gép működésének elemzése (VMBA)
* Hálózatelemzés
* Erőforrás-elemzés

## <a name="virtual-machine-behavioral-analysis"></a>Virtuális gép működésének elemzése
Az Azure Security Center a működés elemzésével tudja azonosítani a feltört erőforrásokat a virtuális gépek eseménynaplóinak az elemzésével (például: folyamat-létrehozási események, bejelentkezési események stb.). Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait.

> [!NOTE]
> Ha részletes tájékoztatást szeretne kapni a Security Center észlelési funkcióinak működéséről, olvassa el [Az Azure Security Center észlelési funkciói](security-center-detection-capabilities.md) című cikket.
>
>

### <a name="crash-analysis"></a>Összeomlás-elemzés
Az összeomlási memóriaképben található memória elemzése olyan speciálisan kifejlesztett kártevők észleléséhez használt módszer, amelyek ki tudják játszani a hagyományos biztonsági megoldásokat. A különféle formában előforduló kártevők megpróbálják megakadályozni, hogy a hagyományos vírusirtó termékek észleljék őket, ennek érdekében vagy soha nem írnak a lemezre, vagy titkosítják a lemezre írt szoftverösszetevőket. Ez megnehezíti a kártevő szoftverek hagyományos kártevőirtó megoldások használatával történő észlelését. Azonban az ilyen kártevők memóriaelemzéssel felismerhetők, mivel a kártevőknek nyomot kell hagyniuk maguk után a memóriában ahhoz, hogy működni tudjanak.

Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában. Az összeomlást okozhatja kártevő, általános alkalmazás vagy rendszerproblémák. Az összeomlási memóriaképben található memóriarész elemzésével a Security Center észlelni tudja az olyan technikákat, amelyekkel a kártevők kihasználják a szoftver biztonsági réseit, hozzáférnek a bizalmas adatokhoz, és a feltört gépen maradnak elrejtőzve. Mindez minimális hatással van a gazdagépek teljesítményére, mivel az elemzést a Security Center háttérrendszere végzi.

Az összeomlási memóriaképes elemzéshez tartozó riasztások az alábbiakban felsorolt mezőket tartalmazzák:

* DUMPFILE: az összeomlási memóriakép fájljának neve.
* PROCESSNAME: az összeomlott folyamat neve.
* PROCESSVERSION: az összeomlott folyamat verziója.

### <a name="shellcode-discovered"></a>Héjkód észlelhető
A héjkód az a kártékony kód, amely azután fut le, hogy a kártevő a szoftver biztonsági rését kihasználva bejutott a rendszerbe. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan végrehajtható kódot talált, amely a kártékony kódokra jellemző működés jeleit mutatja. Bár előfordulhat, hogy nem rosszindulatú szoftverhez tartozik az adott működés, ez nem jellemző a szokásos szoftverfejlesztési gyakorlatban.

Ez a riasztás a következő mezőt is tartalmazza:

* ADDRESS: a héjkód helye a memóriában.

Példa az ilyen típusú riasztásra:

![Héjkód riasztása](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Moduleltérítés észlelhető
A Windows dinamikus csatolású kódtárai (DLL) teszik lehetővé, hogy a szoftverek használják a Windows közös rendszerfunkcióit. DLL-eltérítés akkor történik, ha a kártevő megváltoztatja a DLL-betöltési sorrendet, hogy kártékony kódot töltsön be a memóriába, ahol tetszőleges kódot lehet végrehajtani. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése egy hasonló nevű modul betöltését észlelte két különböző elérési útról, és az egyik elérési út a Windows rendszer bináris rendszerfájljainak helyére mutat.

A megbízható szoftverfejlesztők esetenként nem ártó szándékkal változtatják meg a DLL-ek betöltési sorrendjét, hanem például a Windows operációs rendszer vagy a Windows-alkalmazások kialakításához vagy bővítéséhez. A DLL-betöltési sorrend ártó szándékú és esetleg jóindulatú megváltoztatásának megkülönböztetéséhez az Azure Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja. Az ellenőrzés eredménye a riasztás „SIGNATURE” mezőjében látható, és ettől függ a riasztás súlyossági szintje, a riasztás leírása és a hibaelhárítási művelet. Az eltérítést végző modul lemezen lévő példányának elemzésével, például a fájlok digitális aláírásának ellenőrzésével vagy víruskeresés futtatásával további adatokhoz juthat az eltérítést végző modul megbízható vagy rosszindulatú jellegére vonatkozóan.

A fenti „Héjkód észlelhető” szakaszban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

* SIGNATURE: azt jelzi, hogy az eltérítést végző modulra illik-e a gyanús működés profilja.
* HIJACKEDMODULE: a Windows eltérített rendszermoduljának neve.
* HIJACKEDMODULEPATH: a Windows eltérített rendszermoduljának elérési útja.
* HIJACKINGMODULEPATH: az eltérítést végző modul elérési útja.

Példa az ilyen típusú riasztásra:

![Moduleltérítési riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Álcázásos Windows-modul észlelhető
A kártevők felhasználhatják a Windows rendszerfájljainak (például: SVCHOST.EXE) vagy moduljainak (például: NTDLL.DLL) nevét arra, hogy „elvegyüljenek” és elfedjék a szoftver kártékony jellegét a rendszergazdák elől. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan modulokat észlelt a memóriaképben, amelyek a Windows rendszermoduljainak nevét használják, de nem felelnek meg a jellemzően a Windows-modulokra vonatkozó feltételeknek. Az álcázásos modul lemezen lévő példányának elemzésével további adatokat kaphat a modul megbízható vagy kártékony jellegére vonatkozóan. Az elemzés a következőket tartalmazhatja:

* Ellenőrizze, hogy a szóban forgó fájl egy megbízható szoftvercsomaghoz tartozik-e.
* Ellenőrizze a fájl digitális aláírását.
* Futtasson víruskeresést a fájlon.

A fenti „Héjkód észlelhető” szakaszban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

* DETAILS: leírja, hogy a modulok metaadatai érvényesek-e, és hogy a modul a rendszer elérési útjáról lett-e betöltve.
* NAME: az álcázásos Windows-modul neve.
* PATH: az álcázásos Windows-modul elérési útja.

Ez a riasztás ezenkívül kiolvas és megjelenít bizonyos mezőket a modul PE fejlécéből, például a „CHECKSUM” és a „TIMESTAMP” mezőt. Ezek a mezők csak akkor jelennek meg, ha a mezők szerepelnek a modulban. A mezők részletes ismertetése a [Microsoft PE és COFF specifikációjában](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) található.

Példa az ilyen típusú riasztásra:

![Álcázásos Windows-modul miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Módosított bináris rendszerfájl észlelhető
A kártevők módosíthatják a rendszermag bináris fájljait, hogy magukat leplezve hozzáférhessenek az adatokhoz, vagy elrejtőzzenek a fertőzött rendszerben. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése a Windows operációs rendszer módosított bináris rendszerfájljait észlelte a memóriában vagy a lemezen.
A megbízható szoftverfejlesztők esetenként nem ártó szándékkal módosítják a memóriában lévő rendszermodulokat, hanem például elkerülő megoldásokhoz vagy az alkalmazások kompatibilitásához. A kártékony és a valószínűleg jóindulatú modulok megkülönböztetéséhez az Azure Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja. Az ellenőrzés eredményét a riasztás súlyossági szintje, a riasztás leírása és a hibaelhárítási művelet jelzi.

A fenti „Héjkód észlelhető” szakaszban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

* MODULENAME: a módosított bináris rendszerfájl neve.
* MODULEVERSION: a módosított bináris rendszerfájl verziója.

Példa az ilyen típusú riasztásra:

![Bináris rendszerfájl miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Gyanús folyamat lett végrehajtva
A Security Center gyanús folyamat végrehajtását azonosítja a cél virtuális gépen azonosítja, és riasztást indít el. Az észlelés nem a konkrét nevet keresi, hanem a paraméter alapján végzi a keresést, ezért még ha a támadó átnevezi is a végrehajtható fájlt, a Security Center akkor is képes az észlelésére.

Példa az ilyen típusú riasztásra:

![Gyanús folyamat miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domain-accounts-queried"></a>Több tartományi fiók lett lekérdezve
A Security Center számos, a tartományi fiókok lekérdezésére tett kísérletet képes észlelni, amelyeket a támadók általában a hálózat felderítése során hajtanak végre. A támadók ezt a technikát használnák fel a tartomány lekérdezéséhez: a felhasználók azonosításához, a tartomány rendszergazdai fiókjainak a megismeréséhez, megtudni, hogy mely számítógépek tartományvezérlők, és a tartomány más tartományokkal való lehetséges megbízhatósági kapcsolatainak a felderítéséhez.

Példa az ilyen típusú riasztásra:

![Több tartományi fiók miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

## <a name="network-analysis"></a>Hálózatelemzés
A Security Center hálózati fenyegetettség-észlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure IPFIX (IP-folyamatadatok exportálása) forgalmából. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket.

### <a name="suspicious-outgoing-traffic-detected"></a>Gyanús kimenő forgalom észlelhető
A hálózati eszközök ugyanolyan módon deríthetők fel és határozhatók meg, mint a más típusú rendszerek. A támadók általában a portkereséssel kezdik. Az alábbi példában olyan gyanús SSH-adatforgalom érkezik egy virtuális gépről, amely SSH találgatásos vagy portkereséses támadást hajthat végre egy külső erőforrás ellen.

![Gyanús kimenő forgalom riasztása](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Ez a riasztás olyan információt tartalmaz, amelyből meghatározhatja a támadás elindítására használt erőforrást, a feltört gépet, az észlelés idejét, valamint a használt protokollt és portot. Ezen a panelen megjelenik a probléma megoldásához felhasználható javítási lépések listája is.

### <a name="network-communication-with-a-malicious-machine"></a>Kártékony géppel folytatott hálózati kommunikáció
Az Azure Security Center a Microsoft fenyegetésfelderítő hírcsatornáinak használatával észlelni tudja a kártékony IP-címmel kommunikáló feltört gépeket, amelyek sok esetben parancs- és vezérlési központként működnek. Ebben az esetben az Azure Security Center azt észlelte, hogy a kommunikáció a Pony Loader (más néven [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)) kártevő használatával történt.

![hálózati kommunikáció miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

Ez a riasztás olyan információt tartalmaz, amelyből azonosíthatja a támadás elindítására használt erőforrást, a megtámadott erőforrást, az áldozat IP-címét, a támadó IP-címét, valamint az észlelés idejét.

> [!NOTE]
> A valódi IP-címek adatvédelmi okból el lettek távolítva erről a képernyőfelvételről.
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>Lehetséges kimenő szolgáltatásmegtagadási támadás észlelése
Egy virtuális gépről származó rendellenes hálózati forgalom következtében a Security Center elindíthat egy lehetséges szolgáltatásmegtagadás típusú támadás miatti riasztást.

Példa az ilyen típusú riasztásra:

![Kimenő szolgáltatásmegtagadás](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Erőforrás-elemzés
A Security Center erőforrás-elemzése a platformszolgáltatásokra, például az [Azure SQL Database fenyegetésészlelése](../sql-database/sql-database-threat-detection-get-started.md) funkcióval való integrációra összpontosít. Ezeknek a területeknek az elemzési eredményei alapján a Security Center elindít egy erőforráshoz kapcsolódó riasztást.

### <a name="potential-sql-injection"></a>Potenciális SQL-injektálás
Az SQL-injektálás olyan támadás, amely kártékony kódot szúr be a karakterláncokba, amelyeket a rendszer később átad az SQL Server példányának elemzés és végrehajtás céljából. Az SQL-utasításokat létrehozó összes eljárást meg kell vizsgálni az injektálási biztonsági rések felderítéséhez, mivel az SQL Server végrehajtja az összes olyan lekérdezést, amely szintaktikailag érvényes. Az SQL fenyegetésészlelési funkció a Machine Learning, viselkedéselemzés és rendellenességészlelés alapján határozza meg azokat a gyanús eseményeket, amelyek az Azure SQL-adatbázisokban megtörténhetnek. Példa:

* Egy korábbi alkalmazott megpróbált hozzáférni az adatbázishoz
* SQL-injektálási támadások
* Egy felhasználó szokatlan otthoni hozzáférése a vállalati adatbázishoz

![Potenciális SQL-injektálás miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

Ez a riasztás olyan információt jelenít meg, amelynek alapján azonosíthatja a megtámadott erőforrást, az észlelés idejét, a támadás állapotát, valamint a kivizsgálás további lépéseire mutató hivatkozást is tartalmaz.

### <a name="vulnerability-to-sql-injection"></a>Biztonsági rés az SQL-injektálás számára
Ez a riasztás akkor indul el, ha a rendszer alkalmazáshibát észlelt egy adatbázison, ami azt jelzi, hogy valószínűleg biztonsági rés keletkezett az SQL-injektálási támadások számára.

![Potenciális SQL-injektálás miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Ismeretlen helyről történt szokatlan hozzáférés
Ez a riasztás akkor indul el, ha a rendszer egy ismeretlen IP-címről történt hozzáférést észlel a kiszolgálón, amely az utolsó időszakban nem jelent meg.

![Szokatlan hozzáférés miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="see-also"></a>Lásd még:
Ebben dokumentumban megismerhette a Security Centerben található különböző típusú biztonsági riasztásokat. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
* [Az Azure Security Center észlelési funkciói](security-center-detection-capabilities.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Dec16_HO1-->


