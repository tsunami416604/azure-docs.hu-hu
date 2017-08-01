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
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 19f71e0d5a8a4642b86ae60a3ab2a4042fa2990e
ms.contentlocale: hu-hu
ms.lasthandoff: 06/17/2017


---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Az Azure Security Center biztonsági riasztásainak megismerése
Ez a cikk segít megismerni az Azure Security Centerben elérhető biztonsági riasztások különböző típusait, valamint a kapcsolódó elemzéseket. A riasztások és incidensek kezelésével kapcsolatos további információkért olvassa el a [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) című cikket.

> [!NOTE]
> A speciális észlelések beállításához frissítsen az Azure Security Center Standard verzióra. A 60 napos próbaverzió ingyenes. A frissítéshez a **Biztonsági szabályzat** beállításnál válassza ki a kívánt [tarifacsomagot](security-center-policies.md). További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).
>

## <a name="what-type-of-alerts-are-available"></a>Milyen típusú riasztások állnak rendelkezésre?
Az Azure Security Center különféle [észlelési funkciók](security-center-detection-capabilities.md) segítségével riasztja az ügyfeleket a környezetüket célzó lehetséges támadások esetén. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A riasztásokban található információk eltérhetnek a fenyegetés észleléséhez használt elemzés típusától függően. Az incidensek további környezeti információkat is tartalmazhatnak, amelyek hasznosak lehetnek a fenyegetések vizsgálata során.  Ez a cikk a következő típusú riasztásokról nyújt információkat:

* Virtuális gép működésének elemzése (VMBA)
* Hálózatelemzés
* Erőforrás-elemzés
* Környezeti információk

## <a name="virtual-machine-behavioral-analysis"></a>Virtuális gép működésének elemzése
Az Azure Security Center a működéselemzéssel azonosítja a feltört erőforrásokat a virtuális gépek eseménynaplóinak az elemzése alapján. Ilyenek például a folyamat-létrehozási események és a bejelentkezési események. Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait.

> [!NOTE]
> Ha részletes tájékoztatást szeretne kapni a Security Center észlelési funkcióinak működéséről, tekintse meg [az Azure Security Center észlelési funkcióit ismertető](security-center-detection-capabilities.md) cikket.
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
A Security Center számos, az Active Directory tartományi fiókok lekérdezésére tett kísérletet képes észlelni, amelyeket a támadók általában a hálózat felderítése során hajtanak végre. A támadók ezt a technikát használhatják fel a tartomány lekérdezéséhez: a felhasználók, a tartomány rendszergazdai fiókjainak, a tartományvezérlő számítógépek és a tartomány más tartományokkal való lehetséges megbízhatósági kapcsolatainak az azonosításához.

Példa az ilyen típusú riasztásra:

![Több tartományi fiók miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

### <a name="local-administrators-group-members-were-enumerated"></a>A helyi rendszergazdák csoport tagjait enumerálták

A Security Center egy riasztást aktivál, ha a 4798-as biztonsági esemény (Windows Server 2016 és Windows 10 rendszerben) aktiválódik. Ez a helyi rendszergazdák csoportok tagjainak enumerálásakor történik meg, amit általában a hálózat felderítése során hajtanak végre a támadók. Ezzel a technikával a rendszergazdai jogosultságokkal rendelkező felhasználók identitását kérdezik le.

Példa az ilyen típusú riasztásra:

![Helyi rendszergazda](./media/security-center-alerts-type/security-center-alerts-type-fig14-new.png)

### <a name="anomalous-mix-of-upper-and-lower-case-characters"></a>Nagy- és kisbetűs karakterek rendellenes kombinációja

A Security Center riasztást aktivál, ha a parancssorban nagy- és kisbetűs karakterek kombinációját észleli. Egyes támadók ezzel a technikával rejtőznek a kis- és nagybetűket megkülönböztető vagy kivonatalapú gépi szabályok elől.

Példa az ilyen típusú riasztásra:

![Rendellenes kombináció](./media/security-center-alerts-type/security-center-alerts-type-fig15-new.png)

### <a name="suspected-kerberos-golden-ticket-attack"></a>Gyanús Kerberos-aranyjegyes támadás

A feltört [krbtgt](https://technet.microsoft.com/library/dn745899.aspx) kulcsokkal a támadók „Kerberos-aranyjegyeket” hozhatnak létre, így bármely felhasználó nevében eljárhatnak. A Security Center egy riasztást aktivál, ha ilyen típusú tevékenységet észlel.

> [!NOTE] 
> A „Kerberos-aranyjeggyel” kapcsolatos további információkért olvassa el a [Windows 10-es hitelesítőadatok lopásának megelőzésére vonatkozó útmutatót](http://download.microsoft.com/download/C/1/4/C14579CA-E564-4743-8B51-61C0882662AC/Windows%2010%20credential%20theft%20mitigation%20guide.docx).

Példa az ilyen típusú riasztásra:

![Aranyjegy](./media/security-center-alerts-type/security-center-alerts-type-fig16-new.png)

### <a name="suspicious-account-created"></a>Gyanús fiók létrehozása

A Security Center egy riasztást aktivál a rendszergazdai jogosultságokkal rendelkező meglévő beépített fiókokhoz nagyon hasonló fiókok létrehozása esetén. Ezzel a technikával a támadók olyan rosszindulatú fiókokat hozhatnak létre, amelyek az emberi ellenőrzések során nem tűnnek fel.
 
Példa az ilyen típusú riasztásra:

![Gyanús fiók](./media/security-center-alerts-type/security-center-alerts-type-fig17-new.png)

### <a name="suspicious-firewall-rule-created"></a>Gyanús tűzfalszabály létrehozása

A támadók megpróbálhatják megkerülni a gazdagép védelmét egyéni tűzfalszabályok létrehozásával, amelyek lehetővé teszik, hogy a kártevő alkalmazások kommunikáljanak a parancs és vezérlés modullal, vagy támadásokat indítsanak a hálózaton a feltört gazdagépen keresztül. A Security Center riasztást aktivál, ha azt észleli, hogy egy gyanús helyen lévő végrehajtható fájl egy új tűzfalszabályt hoz létre.
 
Példa az ilyen típusú riasztásra:

![Tűzfalszabály](./media/security-center-alerts-type/security-center-alerts-type-fig18-new.png)

### <a name="suspicious-combination-of-hta-and-powershell"></a>HTA és PowerShell gyanús kombinációja

A Security Center egy riasztást aktivál, ha azt észleli, hogy egy Microsoft HTML-alkalmazásgazda (HTA) PowerShell-parancsokat indít. Ezzel a technikával a támadók kártevő PowerShell-szkripteket indíthatnak.
 
Példa az ilyen típusú riasztásra:

![HTA és PS](./media/security-center-alerts-type/security-center-alerts-type-fig19-new.png)


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

## <a name="contextual-information"></a>Környezeti információk
Vizsgálatok során az elemzőknek további háttér-információra van szükségük ahhoz, hogy megállapíthassák a fenyegetés jellegét és elháríthassák veszélyt.  Ha például hálózati anomáliát észlelnek, de nincsenek tisztában azzal, hogy mi történik a hálózaton vagy a célzott erőforráson, rendkívül nehéz meghatározni a következő lépést. Ennek megkönnyítése érdekében a biztonsági incidensek olyan összetevőket, kapcsolódó eseményeket vagy egyéb információt tartalmazhatnak, amely a vizsgálatot végző személy segítségére lehet. A további elérhető információk eltérhetnek az észlelt fenyegetés típusától, valamint a környezet konfigurációjától függően, és nem minden biztonsági incidens esetében állnak rendelkezésre.

Ha van további elérhető információ, akkor a biztonsági incidensben, a riasztások listája alatt látható. Ez többek között az alábbi információkat tartalmazhatja:

- Naplótörlési események
- PNP-eszköz csatlakoztatása ismeretlen eszközről
- Beavatkozást nem igénylő riasztások 

![Szokatlan hozzáférés miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig20.png) 


## <a name="see-also"></a>Lásd még:
Ebben dokumentumban megismerhette a Security Center különböző típusú biztonsági riasztásait. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
* [Az Azure Security Center észlelési képességei](security-center-detection-capabilities.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md): Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

