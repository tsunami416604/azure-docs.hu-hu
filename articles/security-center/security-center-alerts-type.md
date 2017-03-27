---
title: "Biztonsági riasztások típus szerint az Azure Security Centerben | Microsoft Docs"
description: "Ez a cikk bemutatja az Azure Security Centerben elérhető biztonsági riasztások különböző fajtáit."
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
ms.date: 03/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 5da00d1d64b258773fa485baa804b283fde731c3
ms.lasthandoff: 03/17/2017


---
# <a name="security-alerts-by-type-in-azure-security-center"></a>Biztonsági riasztások típus szerint az Azure Security Centerben
Ez a cikk segít megismerni az Azure Security Centerben elérhető biztonsági riasztások különböző típusait. A riasztások kezelésével kapcsolatos további információkért olvassa el a [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) című cikket.

> [!NOTE]
> A speciális észlelések beállításához frissítsen az Azure Security Center Standard verzióra. A 60 napos próbaverzió ingyenes. A frissítéshez a **Biztonsági szabályzat** beállításnál válassza ki a kívánt [tarifacsomagot](security-center-policies.md). További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).
>
>

## <a name="what-type-of-alerts-are-available"></a>Milyen típusú riasztások állnak rendelkezésre?
Az Azure Security Center különböző riasztásokat biztosít, amelyek igazodnak a számítógépes törlési lánc szakaszaihoz. Az alábbi ábrán különböző riasztások láthatók, amelyek a következő szakaszok valamelyikéhez kapcsolódnak.

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

**Illetéktelen behatolás után**  

* Kártékonyként ismert IP-címmel való kommunkáció (adatok kiszűrése vagy parancs és vezérlés)
* Feltört erőforrások használata további támadások elvégzésére (kimenő port keresése, RDP/SSH találgatásos támadások és levélszemét)

Minden egyes szakaszhoz különböző típusú támadások tartoznak, amelyek különböző alrendszereket céloznak meg. A Security Center három riasztási kategóriával rendelkezik a támadások ezen szakaszokban történő elhárításához:

* Virtuális gép működésének elemzése (VMBA)
* Hálózatelemzés
* Erőforrás-elemzés

## <a name="virtual-machine-behavioral-analysis"></a>Virtuális gép működésének elemzése
Az Azure Security Center a működéselemzéssel azonosítja a feltört erőforrásokat a virtuális gépek eseménynaplóinak az elemzése alapján. Ilyenek például a folyamat-létrehozási események és a bejelentkezési események. Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait.

> [!NOTE]
> Ha részletes tájékoztatást szeretne kapni a Security Center észlelési funkcióinak működéséről, tekintse meg [az Azure Security Center észlelési funkcióit ismertető](security-center-detection-capabilities.md) cikket.
>
>

### <a name="crash-analysis"></a>Összeomlás-elemzés
Az összeomlási memóriaképben található memória elemzése olyan speciálisan kifejlesztett kártevők észleléséhez használt módszer, amelyek ki tudják játszani a hagyományos biztonsági megoldásokat. A különféle formában előforduló kártevők megpróbálják megakadályozni, hogy a hagyományos vírusirtó termékek észleljék őket, ennek érdekében vagy soha nem írnak a lemezre, és nem titkosítják a lemezre írt szoftverösszetevőket. Ez megnehezíti a kártevők hagyományos kártevőirtó megoldásokkal történő észlelését. Azonban az ilyen kártevők memóriaelemzéssel felismerhetők, mivel a kártevőknek nyomot kell hagyniuk maguk után a memóriában ahhoz, hogy működni tudjanak.

Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában. Az összeomlást okozhatja kártevő, általános alkalmazás vagy rendszerproblémák. Az összeomlási memóriaképben található memóriarész elemzésével a Security Center észlelni tudja az olyan technikákat, amelyekkel a kártevők kihasználják a szoftver biztonsági réseit, hozzáférnek a bizalmas adatokhoz, és a feltört gépen maradnak elrejtőzve. Mindez minimális hatással van a gazdagépek teljesítményére, mivel az elemzést a Security Center háttérrendszere végzi.

A következő mezők gyakoriak a cikkben később megjelenő összeomlási memóriaképes riasztás példái esetében:

* DUMPFILE: az összeomlási memóriakép fájljának neve.
* PROCESSNAME: az összeomlott folyamat neve.
* PROCESSVERSION: az összeomlott folyamat verziója.

### <a name="shellcode-discovered"></a>Héjkód észlelhető
A héjkód az a kártékony kód, amely azután fut le, hogy a kártevő a szoftver biztonsági rését kihasználva bejut a rendszerbe. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan végrehajtható kódot talált, amely a kártékony kódokra jellemző működés jeleit mutatja. Bár előfordulhat, hogy nem rosszindulatú szoftverhez tartozik az adott működés, ez nem jellemző a szokásos szoftverfejlesztési gyakorlatban.

A héjkódriasztás példája a következő mezőt is tartalmazza:

* ADDRESS: a héjkód helye a memóriában.

Példa az ilyen típusú riasztásra:

![Héjkód riasztása](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Moduleltérítés észlelhető
A Windows dinamikus csatolású kódtárai (DLL) teszik lehetővé, hogy a szoftverek használják a Windows közös rendszerfunkcióit. DLL-eltérítés akkor történik, ha a kártevő megváltoztatja a DLL-betöltési sorrendet, hogy kártékony kódot töltsön be a memóriába, ahol tetszőleges kódot lehet végrehajtani. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése egy hasonló nevű modul betöltését észlelte két különböző elérési útról. Az egyik elérési út a Windows rendszer bináris rendszerfájljainak helyére mutat.

A megbízható szoftverfejlesztők esetenként nem ártó szándékkal változtatják meg a DLL-ek betöltési sorrendjét, hanem például a Windows operációs rendszer eszközeinek biztosításához vagy bővítéséhez, illetve a Windows-alkalmazások bővítéséhez. A DLL-betöltési sorrend ártó szándékú és esetleg jóindulatú megváltoztatásának megkülönböztetéséhez az Azure Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja. Az ellenőrzés eredménye a riasztás „SIGNATURE” mezőjében látható, és ettől függ a riasztás súlyossági szintje, a riasztás leírása és a hibaelhárítási művelet. Annak kiderítéséhez, hogy a modul megbízható vagy kártékony, elemezze az eltérítést végző modul lemezen lévő példányát. Például ellenőrizheti a fájl digitális aláírását vagy víruskeresést futtathat.

A korábbi „Héjkód észlelhető” szakaszban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

* SIGNATURE: azt jelzi, hogy az eltérítést végző modulra illik-e a gyanús működés profilja.
* HIJACKEDMODULE: a Windows eltérített rendszermoduljának neve.
* HIJACKEDMODULEPATH: a Windows eltérített rendszermoduljának elérési útja.
* HIJACKINGMODULEPATH: az eltérítést végző modul elérési útja.

Példa az ilyen típusú riasztásra:

![Moduleltérítési riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Álcázásos Windows-modul észlelhető
A kártevők felhasználhatják a Windows rendszerfájljainak (például: SVCHOST.EXE) vagy moduljainak (például: NTDLL.DLL) nevét arra, hogy *elvegyüljenek*, és elfedjék a szoftver kártékony jellegét a rendszergazdák elől. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan modulokat észlelt a memóriaképben, amelyek a Windows rendszermoduljainak nevét használják, de nem felelnek meg a jellemzően a Windows-modulokra vonatkozó feltételeknek. Az álcázásos modul lemezen lévő példányának elemzésével további adatokat kaphat a modul megbízható vagy kártékony jellegéről. Az elemzés a következőket tartalmazhatja:

* Ellenőrizze, hogy a szóban forgó fájl megbízható szoftvercsomaghoz tartozik-e.
* Ellenőrizze a fájl digitális aláírását.
* Futtasson víruskeresést a fájlon.

A „Héjkód észlelhető” szakaszban korábban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

* DETAILS: leírja, hogy a modulok metaadatai érvényesek-e, és hogy a modul a rendszer elérési útjáról lett-e betöltve.
* NAME: az álcázásos Windows-modul neve.
* PATH: az álcázásos Windows-modul elérési útja.

Ez a riasztás ezenkívül kiolvas és megjelenít bizonyos mezőket a modul PE fejlécéből, például a „CHECKSUM” és a „TIMESTAMP” mezőt. Ezek a mezők csak akkor jelennek meg, ha a mezők szerepelnek a modulban. A mezők részletes ismertetése a [Microsoft PE és COFF specifikációjában](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) található.

Példa az ilyen típusú riasztásra:

![Álcázásos Windows-modul miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Módosított bináris rendszerfájl észlelhető
A kártevők módosíthatják a rendszermag bináris fájljait, hogy magukat leplezve hozzáférhessenek az adatokhoz, vagy elrejtőzzenek a fertőzött rendszerben. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése a Windows operációs rendszer módosított bináris rendszerfájljait észlelte a memóriában vagy a lemezen.

A megbízható szoftverfejlesztők esetenként nem ártó szándékkal módosítják a memóriában lévő rendszermodulokat, hanem például elkerülő megoldásokhoz vagy az alkalmazások kompatibilitásához. A kártékony és a valószínűleg jóindulatú modulok megkülönböztetéséhez az Azure Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja. Az ellenőrzés eredményét a riasztás súlyossági szintje, a riasztás leírása és a hibaelhárítási művelet jelzi.

A „Héjkód észlelhető” szakaszban korábban felsorolt általános mezőkön kívül ez a riasztás a következő mezőket is tartalmazza:

* MODULENAME: a módosított bináris rendszerfájl neve.
* MODULEVERSION: a módosított bináris rendszerfájl verziója.

Példa az ilyen típusú riasztásra:

![Bináris rendszerfájl miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Gyanús folyamat lett végrehajtva
A Security Center azonosítja a cél virtuális gépen futó gyanús folyamatot, majd riasztást indít el. Az észlelés nem a konkrét nevet keresi, hanem a végrehajtható fájl paramétereit. Ezért még ha a támadó átnevezi is a végrehajtható fájlt, a Security Center akkor is képes a gyanús folyamat észlelésére.

Példa az ilyen típusú riasztásra:

![Gyanús folyamat miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domain-accounts-queried"></a>Több tartományi fiók lett lekérdezve
A Security Center számos, a tartományi fiókok lekérdezésére tett kísérletet képes észlelni, amelyeket a támadók általában a hálózat felderítése során hajtanak végre. A támadók ezt a technikát használhatják fel a tartomány lekérdezéséhez: a felhasználók, a tartomány rendszergazdai fiókjainak, a tartományvezérlő számítógépek és a tartomány más tartományokkal való lehetséges megbízhatósági kapcsolatainak az azonosításához.

Példa az ilyen típusú riasztásra:

![Több tartományi fiók miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

## <a name="network-analysis"></a>Hálózatelemzés
A Security Center hálózati fenyegetettség-észlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure IPFIX (IP-folyamatadatok exportálása) forgalmából. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket.

### <a name="suspicious-outgoing-traffic-detected"></a>Gyanús kimenő forgalom észlelhető
A hálózati eszközök ugyanolyan módon deríthetők fel és határozhatók meg, mint a más típusú rendszerek. A támadók általában a portkereséssel kezdik. A következő példában gyanús Secure Shell (SSH)-adatforgalom érkezik egy virtuális gépről. Ebben az esetben SSH találgatásos vagy portkereséses támadás lehet folyamatban egy külső erőforrás ellen.

![Gyanús kimenő forgalom riasztása](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Ez a riasztás olyan információt tartalmaz, amelynek használatával azonosíthatja a támadás elindítására használt erőforrást. Ez a riasztás olyan információt is tartalmaz, amelyből azonosíthatja a feltört gépet, az észlelés időpontját, valamint a használt protokollt és portot. Ezen a panelen megjelenik a probléma megoldásához felhasználható javítási lépések listája is.

### <a name="network-communication-with-a-malicious-machine"></a>Kártékony géppel folytatott hálózati kommunikáció
Az Azure Security Center a Microsoft fenyegetésfelderítő hírcsatornáinak használatával észlelni tudja a kártékony IP-címmel kommunikáló feltört gépeket. Sok esetben a kártevőcím egy parancs- és vezérlőközpontként használt gépet jelöl. Ebben az esetben a Security Center azt észlelte, hogy a kommunikáció a Pony Loader (más néven [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)) kártevő használatával történt.

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
A Security Center erőforrás-elemzése a PaaS (szolgáltatásként üzemeltetett platform) szolgáltatásokra összpontosít, mint például az [Azure SQL Database fenyegetésészlelés](../sql-database/sql-database-threat-detection.md) funkcióval való integrációja. Ezeknek a területeknek az elemzési eredményei alapján a Security Center elindít egy erőforráshoz kapcsolódó riasztást.

### <a name="potential-sql-injection"></a>Potenciális SQL-injektálás
Az SQL-injektálás olyan támadás, amely kártékony kódot szúr be a karakterláncokba, amelyeket a rendszer később átad az SQL Server példányának elemzés és végrehajtás céljából. Az SQL-utasításokat létrehozó összes eljárást meg kell vizsgálni az injektálási biztonsági rések felderítéséhez, mivel az SQL Server végrehajtja az összes olyan lekérdezést, amely szintaktikailag érvényes. Az SQL fenyegetésészlelési funkciója gépi tanulás, viselkedéselemzés és rendellenességészlelés alapján határozza meg azokat a gyanús eseményeket, amelyek az Azure SQL-adatbázisokban megtörténhetnek. Példa:

* Egy korábbi alkalmazott megpróbált hozzáférni az adatbázishoz
* SQL-injektálási támadások
* Egy felhasználó szokatlan otthoni hozzáférése a vállalati adatbázishoz

![Potenciális SQL-injektálás miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

A riasztásban megjelenő információk alapján azonosíthatja a megtámadott erőforrást, az észlelés idejét, és a támadás állapotát. Ezeken kívül tartalmaz a kivizsgálás további lépéseire mutató hivatkozást is.

### <a name="vulnerability-to-sql-injection"></a>Biztonsági rés az SQL-injektálás számára
Ez a riasztás akkor aktiválódik, ha a rendszer alkalmazáshibát észlelt egy adatbázisban. Ez a riasztás az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet.

![Potenciális SQL-injektálás miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Ismeretlen helyről történt szokatlan hozzáférés
Ez a riasztás akkor indul el, ha a rendszer egy ismeretlen IP-címről történt hozzáférési eseményt észlel a kiszolgálón, amely az utolsó időszakban nem jelent meg.

![Szokatlan hozzáférés miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="see-also"></a>Lásd még:
Ebben dokumentumban megismerhette a Security Center különböző típusú biztonsági riasztásait. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
* [Az Azure Security Center észlelési képességei](security-center-detection-capabilities.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md): Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

