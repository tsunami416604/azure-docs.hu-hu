---
title: Biztonsági riasztások típus szerint az Azure Security Centerben | Microsoft Docs
description: Ez a cikk bemutatja az Azure Security Centerben elérhető biztonsági riasztások különböző fajtáit.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: 6bd05a417bfb011dd86b61d654be836ef76099b7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439683"
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Az Azure Security Center biztonsági riasztásainak megismerése
Ez a cikk segít megismerni az Azure Security Centerben elérhető biztonsági riasztások különböző típusait, valamint a kapcsolódó elemzéseket. A riasztások és incidensek kezelésével kapcsolatos további információkért olvassa el a [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) című cikket.

A speciális észlelések beállításához frissítsen az Azure Security Center Standard verzióra. Az ingyenes próbaverzió is elérhető. A frissítéshez a **Biztonsági szabályzat** beállításnál válassza ki a kívánt [tarifacsomagot](tutorial-security-policy.md). További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> A Security Centernek korlátozott előzetes verzióként kiadott új észlelései naplózott rekordokat használnak egy közös naplózási keretrendszerben a Linux rendszerű gépek rosszindulatú viselkedésének észlelésére. Amennyiben csatlakozni szeretne az előzetes verzióhoz, küldjön [nekünk](mailto:ASC_linuxdetections@microsoft.com) egy e-mailt, amely tartalmazza az előfizetés-azonosítóit.

## <a name="what-type-of-alerts-are-available"></a>Milyen típusú riasztások állnak rendelkezésre?
Az Azure Security Center különféle [észlelési funkciók](security-center-detection-capabilities.md) segítségével riasztja az ügyfeleket a környezetüket célzó lehetséges támadások esetén. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A riasztásokban található információk eltérhetnek a fenyegetés észleléséhez használt elemzés típusától függően. Az incidensek további környezeti információkat is tartalmazhatnak, amelyek hasznosak lehetnek a fenyegetések vizsgálata során.  Ez a cikk a következő típusú riasztásokról nyújt információkat:

* Virtuális gép működésének elemzése (VMBA)
* Hálózatelemzés
* SQL Database- és SQL Data Warehouse-elemzés
* Környezeti információk

## <a name="virtual-machine-behavioral-analysis"></a>Virtuális gép működésének elemzése
Az Azure Security Center a működéselemzéssel azonosítja a feltört erőforrásokat a virtuális gépek eseménynaplóinak az elemzése alapján. Ilyenek például a folyamat-létrehozási események és a bejelentkezési események. Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait.

> [!NOTE]
> Ha részletes tájékoztatást szeretne kapni a Security Center észlelési funkcióinak működéséről, tekintse meg [az Azure Security Center észlelési funkcióit ismertető](security-center-detection-capabilities.md) cikket.


### <a name="event-analysis"></a>Esemény elemzése
A Security Center fejlett elemzési módszerekkel azonosítja a feltört erőforrásokat a virtuális gépek eseménynaplóinak elemzése alapján. Ilyenek például a folyamat-létrehozási események és a bejelentkezési események. Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait.

* **Gyanús folyamat-végrehajtást észlelt**: A támadók gyakran próbálja által próbálkoznak ártalmatlan folyamatnak álcázott kártékony kódok végrehajtásához. Ezek a riasztások jelzik, ha egy folyamat végrehajtására illik az alábbi jellemzők valamelyike:
    * A rendszer olyan folyamatot hajtott végre, amelyet közismerten ártó célokból szoktak használni. Az egyes parancsok ártalmatlannak jeleníthet meg, amíg a riasztás összességére vonatkozik az alábbi parancsok összesítést.
    * Egy folyamat végrehajtása ismeretlen helyről indult ki.
    * Egy folyamat végrehajtása olyan helyről indult ki, amelyen ismert gyanús fájlokkal osztozik.
    * Egy folyamat végrehajtása gyanús útvonalról történt.
    * Egy folyamat végrehajtásának a szokottól eltérő volt a kontextusa.
    * Egy folyamat végrehajtása egy szokatlan fiók használatával történt.
    * A rendszer gyanús kiterjesztésű folyamatot hajtott végre.
    * A rendszer gyanús kettős kiterjesztésű folyamatot hajtott végre.
    * A rendszer gyanús, a fájlnévben jobbról balra olvasott (RLO) karaktert tartalmazó folyamatot hajtott végre.
    * Egy folyamat, amelynek a neve hasonló, de más a gyakran futtatott folyamat lett végrehajtva
    * A rendszer egy ismert támadóeszközhöz kapcsolható névvel ellátott folyamatot hajtott végre.
    * A rendszer egy véletlenszerű névvel ellátott folyamatot hajtott végre.
    * A rendszer gyanús kiterjesztésű folyamatot hajtott végre.
    * A rendszer rejtett fájl futtatott.
    * A rendszer egy folyamatot egy másik, nem kapcsolódó folyamat alárendelt folyamataként hajtott végre.
    * Egy rendszerfolyamat szokatlan folyamatot hozott létre.
    * A Windows Update-szolgáltatás rendellenes folyamatot indított el.
    * Egy folyamat indítása szokatlan parancssorral történt. Ez azzal állhat kapcsolatban, amikor szabályszerű folyamatokat térítenek el kártékony tartalmak futtatása céljából.
    * Egy kísérlet történt a parancssorból egy mappa összes végrehajtható (*.exe) fájljának elindítására.
    * Egy folyamatot a PsExec segédprogram hajtott végre, amely folyamatok távoli futtatásához használható.
    * Az Apache Tomcat® fölérendelt végrehajtható fájlját (Tomcat#.exe) gyanús alárendelt folyamatok indítására használták, amelyek rosszindulatú parancsokat üzemeltethetnek vagy futtathatnak.
    * A Microsoft Windows „Programkompatibilitási segédet” (pcalua.exe) egy végrehajtható kód elindítására használták, amely akár rosszindulatú is lehet.
    * A rendszer gyanús sorozatos folyamatleállítást észlelt.
    * Az SVCHOST rendszerfolyamat végrehajtásának a szokottól eltérő volt a kontextusa.
    * Az SVCHOST rendszerfolyamat egy ritka szolgáltatáscsoportban lett végrehajtva.
    * A rendszer gyanús parancssort hajtott végre.
    * Egy PowerShell-szkript más ismert gyanús szkriptekkel közös jellemzőkkel rendelkezik.
    * A rendszer egy ismert rosszindulatú PowerShell PowerSploit-parancsmagot hajtott végre.
    * Egy beépített SQL-felhasználó olyan folyamatot hajtott végre, amelyet normális esetben nem tenne.
    * A rendszer egy Base-64 kódolású végrehajtható fájlt észlelt, amely arra utalhat, hogy egy támadó egy parancssorozat használatával menet közben létrehozott végrehajtható kód segítségével megkísérelte érvényteleníteni az észlelést.

* **RDP-erőforrás gyanús tevékenység**: A támadók gyakran vesznek célba találgatásos támadásokkal szemben az RDP hasonló nyitott felügyeleti portokat. Ezek a riasztások gyanús távoli asztali bejelentkezési tevékenységre utalnak, amely a következőket jelentheti:
    * Kísérleteket tettek távoli asztali bejelentkezésre.
    * Kísérleteket tettek távoli asztali bejelentkezésre érvénytelen fiókokon keresztül.
    * Távoli asztali bejelentkezési kísérletekre került sor, amelyek némelyike tudtak sikeresen jelentkezzen be a gép.
* **SSH-erőforrás gyanús tevékenység**: A támadók gyakran vesznek célba találgatásos támadásokkal szemben az SSH hasonló nyitott felügyeleti portokat. Ezek a riasztások gyanús SSH-bejelentkezési tevékenységre utalnak, amely a következőket jelentheti:
    * Sikertelen SSH-bejelentkezési kísérletekre került sor.
    * SSH-bejelentkezési kísérletekre került sor, amelyek egy része sikeres volt.
* **Gyanús WindowPosition beállításazonosító**: Ez a riasztás azt jelzi, hogy a WindowPosition konfigurációjának módosítására történt kísérlet, amely lehet alkalmazásablakok alkalmazást windows, az asztal nem látható szakaszokban.
* **Az AppLocker megkerülésére tett kísérlet**: Az AppLocker futtatható Windows, a kártevők általi fenyegetettsége korlátozására használható. Ez a riasztás az AppLocker-korlátozások megkerülésére tett lehetséges kísérletet jelez, amely során megbízható (az AppLocker szabályzata által engedélyezett) végrehajtható fájlok használatával nem megbízható kód futtatását kísérelték meg.
* **Gyanús nevesítettcső-kommunikáció nevű**: Ez a riasztás azt jelzi, hogy az adatok írása volt-e egy helyi nevesített cső a Windows konzol parancs. A nevesített csöveket a támadók gyakran használják a rosszindulatú beágyazott elemek vezérlésére és a velük folytatott kommunikációra.
* **Dekódolása a beépített certutil.exe eszköz használatával végrehajtható**: Ez a riasztás azt jelzi, hogy a beépített rendszergazdai segédprogramot, a certutil.exe, egy végrehajtható fájl dekódolására használták. A támadók gyakran használják különböző megbízható rendszergazdai eszközök funkcióit rosszindulatú műveletek elvégzésére. Például a certutil.exe vagy a hozzá hasonló eszközök rosszindulatú végrehajtható fájlok dekódolására használhatók, amelyeket aztán a rendszer végrehajt.
* **Egy eseménynapló törölve lett**: Ez a riasztás egy gyanús naplótörlési műveletet, amelynek segítségével a támadók gyakran nyomait próbálja jelzi.
* **Letiltása és törlése az IIS-naplófájljai**: Ez a riasztás azt jelzi, hogy az IIS-naplófájl is le van tiltva és/vagy törlését, amelyek gyakran használják a támadók nyomait próbálja.
* **Gyanús fájltörlés**: Ez a riasztás-fájlokat, amelyek segítségével egy támadó rosszindulatú bináris bizonyítékokat eltávolítása gyanús fájltörlést jelez.
* **Az összes fájl árnyékmásolata törölve lett**: Ez a riasztás azt jelzi, hogy árnyékmásolatok törölve lettek.
* **Gyanús fájltörlési parancsok**: Ez a riasztás azt jelzi, hogy hozható systeminfo parancsok végrehajtására utáni öntörlési tevékenységhez használt kombinációját.  Bár a *systeminfo.exe* megbízható Windows-eszköznek minősül, az egymás után kétszeri futtatása, majd ezt követően egy törlési parancs használata, ahogy ebben az esetben is történt, igen ritka.
* **Gyanús fióklétrehozás**: Ez a riasztás azt jelzi, hogy a fiók létrejött-e az egy aktivál egy meglévő beépített rendszergazdai jogosultságú fiókhoz. Ezzel a technikával a támadók feltűnésmentesen hozhatnak létre rosszindulatú fiókokat.
* **Gyanús kötet-árnyékmásolati tevékenység**: Ez a riasztás árnyékmásolati törlési tevékenységet az erőforráson jelzi. A kötet árnyékmásolata (Volume Shadow Copy, VSC) az adatok pillanatképeit tároló fontos összetevő. Ezt a tevékenységet, Zsarolóprogramok társítva, de is lehet, hogy jogosult.
* **Windows beállításjegyzék adatmegőrzési metódusa**: Ez a riasztás azt jelzi, hogy a Windows beállításjegyzék egy végrehajtható kód megőrzésére tett kísérlet. A rosszindulatú folyamatok gyakran alkalmaznak ehhez hasonló technikákat, hogy a rendszer újraindítása után is megmaradjanak.
* **Gyanús Új tűzfalszabály**: Ez a riasztás azt jelzi, hogy egy új tűzfalszabály lett hozzáadva *netsh.exe* egy gyanús helyen található végrehajtható érkező adatforgalom engedélyezéséhez.
* **Gyanús XCOPY-végrehajtások**: Ez a riasztás jelzi, hogy sikerült jelezze, hogy a gépek egy XCOPY-végrehajtások egy sorozatát biztonsága sérült, és a kártevők használták.
* **Jogi nyilatkozat jelenik meg a felhasználók bejelentkezésekor tiltási**: Ez a riasztás azt jelzi, hogy a beállításkulcs, amely szabályozza, hogy a jogi figyelmeztetés jelenik meg a felhasználók számára, amikor bejelentkeznek a módosítás. Ezt a támadók gyakran alkalmazzák egy gazdagép feltörése után.
* **Nagy- és kisbetűs karakterek rendellenes kombinációját észlelte a parancssorban**: Ez a riasztás azt jelzi, hogy a felső és a parancssorban, amely technikával a támadók a kis-és nagybetűket vagy kivonatalapú gépi szabályok elől kisbetűs karakterek használatát.
* **Rejtjelezett parancssor**: Ez a riasztás azt jelzi, hogy gyanús mutatók, rejtjelezésre utaló jeleket észlelt a parancssorban.
* **Több tartományi fiók lett lekérdezve**: A támadók gyakran lekérdezésére AD-tartományi fiókokat felderítés végrehajtásakor a felhasználók, tartományi rendszergazdai fiókok, tartományvezérlők és tartományok közötti megbízhatósági kapcsolatok. Ez a riasztás azt jelzi, hogy szokatlan mennyiségű, különböző tartományokban található fiókot kérdeztek le rövid időn belül.
* **Lehetséges helyi felderítési tevékenység**: Ez a riasztás azt jelzi, hogy végre lett hajtva felderítési tevékenységgel összefüggésbe hozható systeminfo parancsok kombinációját.  Bár a *systeminfo.exe* megbízható Windows-eszköznek minősül, egymás után kétszer igen ritkán futtatják.
* **Kulcslétrehozó végrehajtható fájl lehetséges végrehajtása**: Ez a riasztás azt jelzi, hogy végre lett hajtva egy folyamatot, amelynek a neve egy kulcslétrehozó tájékoztató. Ezek az eszközök általában a szoftverlicencelési mechanizmusok kiiktatására szolgálnak, letöltésükkor azonban gyakran más rosszindulatú szoftverek is letöltődhetnek.
* **Gyanús végrehajtás a rundll32.exe használatával**: Ez a riasztás azt jelzi, hogy a rundll32.exe konzisztensek legyenek a feltört gazdagépeken implantátum első telepítéséhez a támadók által használt elnevezési sémával szokatlan névvel ellátott folyamat végrehajtására használták.
* **HTA és PowerShell gyanús kombinációja**: Ez a riasztás azt jelzi, hogy egy Microsoft HTML-alkalmazás állomás (HTA) PowerShell-parancsok dob. Ezzel a technikával a támadók kártevő PowerShell-szkripteket indíthatnak.
* **Váltson egy beállításkulcsot, amely kihasználható az UAC megkerülésére**: Ez a riasztás azt jelzi, hogy, hogy egy beállításkulcsot, amely kihasználható az UAC (felhasználói fiókok felügyelete) megkerülésére változott, amelyet a támadók gyakran használják áthelyezése nem rendszerjogosultságú (általános jogú felhasználó) (például rendszergazdai) privileged access egy feltört gazdagépen.
* **Parancssor gyanús tartománynév használatát**: Ez a riasztás azt jelzi, hogy egy gyanús tartománynév használták, amely lehet egy támadó bizonyítékokat kártékony eszközöket üzemeltet valamint parancs és vezérlés és az adatok kiszűrése.
* **Egy fiók több gazdagépen, egy 24 órás időtartamon belül létrehozott**: Ez a riasztás azt jelzi, hogy kísérlet történt ugyanazt a felhasználói fiókot több gazdagépen, amely lehet egy támadó oldalirányú mozgására a hálózaton egy vagy több hálózati entitás feltörését követően bizonyíték létrehozásához.
* **Gyanús CACLS biztonsági állapotát a rendszer az alacsonyabb használata**: Ez a riasztás azt jelzi, hogy megváltozott-e a módosítási hozzáférés-vezérlési lista (CACLS). A támadók gyakran használják ezt a technikát arra, hogy teljes körű hozzáférést biztosítsanak a rendszer olyan bináris fájljai számára, mint az ftp.exe, a net.exe, a wscript.exe stb.
* **Gyanús Kerberos-Aranyjegyes támadási paraméterek**: Ez a riasztás azt jelzi, hogy parancssori paraméterekkel rendelkező egy Kerberos-Aranyjegyes támadás során a végrehajtott. A feltört krbtgt kulcsokkal a támadók bármely felhasználó nevében eljárhatnak.
* **A WDigest UseLogonCredential beállításkulcs engedélyezése**: Ez a riasztás azt jelzi, hogy a beállításkulcs megváltozott-e, hogy a bejelentkezési hitelesítő adatait az LSA-memóriában majd memória begyűjtött is egyszerű szövegként kell tárolni.
* **Potenciálisan gyanús használata Telegram eszköz**: Ez a riasztás azt jelzi, hogy a telepítés, a Telegram, ingyenes felhőalapú azonnali üzenetküldési szolgáltatás támadók rosszindulatú bináris fájlok átvitele a számítógépen, telefonon vagy táblagépen használják.
* **Új ASEP létrehozása**: Ez a riasztás azt jelzi, hogy egy új ASEP (automatikus Indításkiterjesztési pont), amely a folyamat neve azonosítja a parancssor automatikusan elindul a létrehozása, és használhatja a támadó folyamataik megőrzésére.
* **Gyanús Set-ExecutionPolicy- és WinRM-módosítások**: Ez a riasztás azt jelzi, hogy a konfigurációs módosítások, amelyek a rosszindulatú ChinaChopper webshell használatával.
* **Kritikus szolgáltatások letiltása**: Ez a riasztás azt jelzi, hogy "net.exe stop" parancsot a Sharedaccesshez vagy a Windows biztonsági központban kritikus szolgáltatások leállítására használták.
* **FTP -s kapcsolójának gyanús használata**: Ez a riasztás azt jelzi, hogy használatát kapcsolójának "-s" kapcsolót, amely segítségével kártevő által egy távoli FTP-kiszolgáló és további rosszindulatú bináris fájlok letöltéséhez.
* **VBScript.Encode parancs gyanús végrehajtása**: Ez a riasztás azt jelzi, hogy a *VBScript.Encode* parancs végrehajtása történt, amely kódolja a szkripteket olvashatatlan szöveggé, ami megnehezíti a felhasználóknak a kódok vizsgálatát.
* **VBScript HTTP-objektum lefoglalása**: Ez a riasztás azt jelzi, hogy egy VBScript-fájlt; létrehozása amely rosszindulatú fájlok letöltésére használható.
* **Beragadó billentyűs támadás**: Ez a riasztás azt jelzi, hogy a támadó egy Beragadó egy kisegítő lehetőség bináris fájlját (például a Beragadó billentyűs, képernyő-billentyűzet, Narrátor) annak érdekében, hogy hátsó kapus hozzáférést.
* **Petya zsarolóprogramra utaló jelek**: Ez a riasztás azt jelzi, hogy a társított a Petya zsarolóprogram elleni technikákat észlelt.
* **AMSI letiltására**: Ez a riasztás azt jelzi, hogy a kártevőirtó vizsgálati felület (AMSI), amellyel letiltható a kártevők észlelése letiltására tett kísérletet jelez.
* **Zsarolóprogramra utaló jelek**: Ez a riasztás azt jelzi, hogy a gyanús tevékenységet képernyőzároló és titkosító zsarolóprogramokkal.
* **Követőgyűjtemény gyanús kimeneti fájlja**: Ez a riasztás azt jelzi, hogy egy nyomkövetést (például a hálózati tevékenységét) begyűjtöttek, és a egy szokatlan fájltípusba továbbították való küldéséhez.
* **Nagy kockázatú szoftver**: Ez a riasztás azt jelzi, amely nincs hozzárendelve a telepítés, a kártevő szoftverek használatát. A támadók gyakran csomagolnak kártevőket a riasztásban szereplőhöz hasonló, alapvetően ártalmatlan eszközök mellé, majd csendes telepítéssel a háttérben telepítik őket.
* **Gyanús fájl létrehozása**: Ez a riasztás azt jelzi, létrehozását és a feltört gazdagépen további kártevők letöltésére, miután megnyitották egy adathalász dokumentum a mellékletet a támadók által használt folyamat végrehajtását.
* **Gyanús hitelesítő adatok a parancssorban**: Ez a riasztás egy fájl futtatásához használt gyanús jelszót jelez. Ezt a módszert a támadók már bizonyítottan használták a Pirpi nevű kártevő futtatására.
* **Kártevő-átvivő lehetséges végrehajtása**: Ez a riasztás azt jelzi, hogy a fájl nevét, a támadók kártevők telepítésére használtak.
* **Gyanús végrehajtás a rundll32.exe használatával**: Ez a riasztás azt jelzi, hogy a rundll32.exe egy notepad.exe vagy reg.exe, az a folyamat injektálási technikával a támadók végrehajtásához használnak.
* **Gyanús parancssori argumentumok**: Ez a riasztás azt jelzi, hogy a HYDROGEN tevékenységi csoport által használt fordított rendszerhéj az együtt használt gyanús parancssori argumentumokat.
* **Gyanús hitelesítő adatok dokumentumokhoz**: Ez a riasztás azt jelzi, hogy egy fájl futtatásához használt kártevők által használt gyanús, közös illesztésiszűrő jelszó kivonatát.
* **Dinamikus PS-szkript konstrukció**: Ez a riasztás azt jelzi, hogy egy PowerShell-szkript. Ezt a módszert a támadók szkriptek fokozatos felépítésére használják az illetéktelen hálózati behatolást jelző (IDS) rendszerek megkerülése érdekében.
* **Metasploit mutatók**: Ez a riasztás a Metasploit-keretrendszer, amely számos funkciót és eszközt biztosít a kapcsolódó tevékenységet jelez.
* **Gyanús fióktevékenység**: Ez a riasztás azt jelzi, hogy egy nemrégiben feltört fiók használatával machine való csatlakozásra tett kísérlet.
* **Fiók létrehozása**: Ez a riasztás azt jelzi, hogy a gépen egy új fiók létrehozását.


### <a name="crash-analysis"></a>Összeomlás-elemzés


Az összeomlási memóriaképben található memória elemzése olyan speciálisan kifejlesztett kártevők észleléséhez használt módszer, amelyek ki tudják játszani a hagyományos biztonsági megoldásokat. A különféle formában előforduló kártevők megpróbálják megakadályozni, hogy a hagyományos vírusirtó termékek észleljék őket, ennek érdekében vagy soha nem írnak a lemezre, és nem titkosítják a lemezre írt szoftverösszetevőket. Ez a módszer megnehezíti a kártevők hagyományos kártevőirtó megoldásokkal történő észlelését. Azonban az ilyen kártevők memóriaelemzéssel felismerhetők, mivel a kártevőknek nyomot kell hagyniuk maguk után a memóriában ahhoz, hogy működni tudjanak.

Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában. Az összeomlást okozhatja kártevő, általános alkalmazás vagy rendszerproblémák. Az összeomlási memóriaképben található memóriarész elemzésével a Security Center észlelni tudja az olyan technikákat, amelyekkel a kártevők kihasználják a szoftver biztonsági réseit, hozzáférnek a bizalmas adatokhoz, és a feltört gépen maradnak elrejtőzve. Mindez minimális hatással van a gazdagépek teljesítményére, mivel az elemzést a Security Center háttérrendszere végzi.

* **Kódinjektálás észlelhető**: A kódinjektálás olyan művelet, amely végrehajtható modulokat szúr be a futó folyamatokba vagy szálakba. Ezzel a technikával kártevő által adatok elérésére, elrejtésére vagy az Eltávolítás (például adatmegőrzés) megakadályozására szolgál. Ez a riasztás jelzi, hogy injektált modul szerepel az összeomlási memóriaképben. A megbízható szoftverfejlesztők esetenként nem ártó szándékkal hajtanak végre kódinjektálást, hanem például egy meglévő alkalmazás vagy az operációs rendszer egyik összetevőjének módosítása vagy bővítése érdekében. A kártékony és a nem kártékony injektált modulok megkülönböztetéséhez a Security Center ellenőrzi, hogy az injektált modulra illik-e a gyanús működés profilja. Az ellenőrzés eredménye a riasztás „SIGNATURE” mezőjében látható, és ettől függ a riasztás súlyossági szintje, a riasztás leírása és a hibaelhárítási művelet.
* **Gyanús kódszegmens**: A gyanús kódszegmens riasztás azt jelzi, hogy egy kódszegmens lett lefoglalva nem szabványos módszerrel, például reflektív injektálással vagy a folyamat hollowing. A riasztás a jelentett kódszegmens további jellemzőit is feldolgozza, hogy kontextusba helyezze a képességeit és viselkedési mintáit.
* **Héjkód észlelhető**: A héjkód az a kártékony kód, amely azután fut le, hogy a kártevő a szoftver biztonsági rését kihasználva bejut a rendszerbe. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan végrehajtható kódot talált, amely a kártékony kódokra jellemző működés jeleit mutatja. Bár előfordulhat, hogy nem rosszindulatú szoftverhez tartozik az adott működés, ez nem jellemző a szokásos szoftverfejlesztési gyakorlatban.
* **Moduleltérítés észlelhető**: A Windows dinamikus csatolású kódtárai (DLL) teszik lehetővé, hogy a szoftverek használják a Windows közös rendszerfunkcióit. DLL-eltérítés akkor történik, ha a kártevő megváltoztatja a DLL-betöltési sorrendet, hogy kártékony kódot töltsön be a memóriába, ahol tetszőleges kódot lehet végrehajtani. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése egy hasonló nevű modul betöltését észlelte két különböző elérési útról. Az egyik elérési út a Windows rendszer bináris rendszerfájljainak helyére mutat. A megbízható szoftverfejlesztők esetenként nem ártó szándékkal változtatják meg a DLL-ek betöltési sorrendjét, hanem például a Windows operációs rendszer eszközeinek biztosításához vagy bővítéséhez, illetve a Windows-alkalmazások bővítéséhez. A DLL-betöltési sorrend ártó szándékú és esetleg jóindulatú megváltoztatásának megkülönböztetéséhez a Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja.
* **Álcázásos Windows-modul észlelhető**: Kártevő szoftver felhasználhatja köznapi nevek a Windows rendszerfájljainak (például SVCHOST. (EXE) vagy moduljainak (például NTDLL. (DLL) elvegyüljenek, és elfedjék a rendszergazdák kövessék a szoftver kártékony jellegét. Ez a riasztás azt jelzi, hogy az összeomlási memóriaképfájl olyan modulokat tartalmaz, amelyek a Windows rendszermoduljainak nevét használják, de nem felelnek meg a jellemzően a Windows-modulokra vonatkozó feltételeknek. Az álcázásos modul lemezen lévő példányának elemzésével további adatokat kaphat a modul megbízható vagy kártékony jellegéről.
* **Módosított bináris rendszerfájl észlelve**: A kártevők módosíthatják a rendszermag bináris fájljait, hogy magukat leplezve hozzáférhessenek az adatokhoz, vagy elrejtőzzenek a fertőzött rendszerben. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése a Windows operációs rendszer módosított bináris rendszerfájljait észlelte a memóriában vagy a lemezen. A megbízható szoftverfejlesztők esetenként nem ártó szándékkal módosítják a memóriában lévő rendszermodulokat, hanem például elkerülő megoldásokhoz vagy az alkalmazások kompatibilitásához. A kártékony és a valószínűleg jóindulatú modulok megkülönböztetéséhez a Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja.

## <a name="network-analysis"></a>Hálózatelemzés
A Security Center hálózati fenyegetettség-észlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure IPFIX (IP-folyamatadatok exportálása) forgalmából. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket.

* **Lehetséges bejövő SQL találgatásos kísérletek kényszerítése**: Hálózati forgalomelemzés gyanús bejövő SQL-kommunikációt észlelt. Ez a tevékenység megfelel az SQL-kiszolgálók ellen indított találgatásos támadásoknak.
* **Gyanús bejövő RDP hálózati aktivitás a különböző forrásokból származó**: Hálózati forgalomelemzés rendellenes bejövő távoli asztal protokoll (RDP) kommunikációt, több forrásból. A mintavételezett hálózati adatokban a géphez csatlakozó egyedi IP-címek fedezhetők fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység az RDP-végpont több gazdagépről (Botnet) történő találgatásos támadására utalhat.
* **Gyanús bejövő RDP hálózati aktivitás**: Hálózati forgalomelemzés rendellenes bejövő távoli asztal protokoll (RDP)-kommunikációt észlelt. A mintavételezett hálózati adatokban a géphez csatlakozó nagy számú bejövő kapcsolat fedezhető fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység az RDP-végpontra irányuló találgatásos támadási kísérletre utalhat.
* **Gyanús kimenő RDP hálózati aktivitás, több célra irányuló**: Hálózati forgalomelemzés rendellenes kimenő távoli asztal protokoll (RDP) kommunikációt több célhelyre. Ez a tevékenység arra utalhat, hogy a gépet feltörték, és az RDP-végpontokra irányuló találgatásos támadási kísérlethez használják. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.
* **Gyanús kimenő RDP hálózati aktivitás**: Hálózati forgalomelemzés rendellenes kimenő távoli asztal protokoll (RDP) kommunikációt észlelt. A mintavételezett hálózati adatokban a gépről induló nagy számú kimenő kapcsolat fedezhető fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy a gépet feltörték, és az RDP-végpontokra irányuló találgatásos támadási kísérlethez használják. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.
* **Gyanús bejövő SSH hálózati aktivitás**: Hálózati forgalomelemzés rendellenes bejövő SSH-kommunikációt észlelt. A mintavételezett hálózati adatokban a géphez csatlakozó nagy számú bejövő kapcsolat fedezhető fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység az SSH-végpontra irányuló találgatásos támadási kísérletre utalhat.
* **Gyanús bejövő SSH hálózati aktivitás a különböző forrásokból származó**: Hálózati forgalomelemzés rendellenes bejövő SSH-kommunikációt észlelt. A mintavételezett hálózati adatokban a géphez csatlakozó egyedi IP-címek fedezhetők fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység az SSH-végpont több gazdagépről (Botnet) történő találgatásos támadására utalhat.
* **Gyanús kimenő SSH hálózati aktivitás**: Hálózati forgalomelemzés rendellenes kimenő SSH-kommunikációt észlelt. A mintavételezett hálózati adatokban a gépről induló nagy számú kimenő kapcsolat fedezhető fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy a gépet feltörték, és az SSH-végpontokra irányuló találgatásos támadási kísérlethez használják. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.
* **Gyanús kimenő SSH hálózati aktivitás, több célra irányuló**: Hálózati forgalomelemzés rendellenes kimenő SSH-kommunikációt észlelt több célhelyre. A mintavételezett hálózati adatokban egyedi IP-címek fedezhetők fel, amelyekhez a gép csatlakozik, ez pedig ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy a gépet feltörték, és az SSH-végpontokra irányuló találgatásos támadási kísérlethez használják. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.
* **Észlelt kártékony géppel folytatott hálózati kommunikációt**: Hálózati forgalomelemzés eredményei azt jelzi, hogy a gép valószínűleg egy parancs- és vezérlőközpontként mi vezérlőközponttal.
* **Esetleges feltört gépet észlelt**: Hálózati forgalomelemzés kimenő tevékenységet észlelt, amely arra utalhat, hogy hálózataként viselkedik egy botnet része. Az elemzés azokon az IP-címeken alapul, amelyekhez az erőforrás a DNS-rekordokkal együtt hozzáfért.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>SQL Database- és SQL Data Warehouse-elemzés

A Security Center erőforrás-elemzése a PaaS (szolgáltatásként üzemeltetett platform) szolgáltatásokra összpontosít, mint például az [Azure SQL Database fenyegetésészlelés](../sql-database/sql-database-threat-detection.md) és az Azure SQL Data Warehouse integrációja. Az SQL fenyegetésészlelés észleli a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására irányuló szokatlan és potenciálisan rosszindulatú kísérletekre utalnak, és aktiválja a következő riasztásokat:

* **Biztonsági rés az SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás egy hibás SQL-utasítást hoz létre az adatbázis. Ez az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. A hibás utasításokat két dolog okozhatja:
    * Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
    * Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
* **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy olyan azonosított alkalmazás biztonsági rés az SQL-injektálás elleni történik. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.
* **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha az SQL Server, amikor valaki jelentkezett be az SQL Server egy szokatlan földrajzi helyről hozzáférési mintájában változik. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
* **Hozzáférés szokatlan Azure-beli adat központból**: Ez a riasztás akkor aktiválódik, ha az SQL Server, amikor valaki jelentkezett be az SQL Server ezen a kiszolgálón a legutóbbi időszakban látott szokatlan Azure-beli adat központból hozzáférési mintájában változik. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új Azure-, Power BI- vagy Azure SQL Query Editor-alkalmazást). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
* **Hozzáférés résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha az SQL Server, amikor valaki jelentkezett be az SQL Server egy szokatlan résztvevő (SQL-felhasználó) használatával hozzáférési mintájában változik. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
* **Hozzáférés potenciálisan káros alkalmazás**: Ez a riasztás akkor aktiválódik, ha egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
* **Találgatásos támadás SQL hitelesítő adatok**: Ez a riasztás akkor aktiválódik, ha van egy rendellenes magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="contextual-information"></a>Környezeti információk
Vizsgálatok során az elemzőknek további háttér-információra van szükségük ahhoz, hogy megállapíthassák a fenyegetés jellegét és elháríthassák veszélyt.  Ha például hálózati anomáliát észlelnek, de nincsenek tisztában azzal, hogy mi történik a hálózaton vagy a célzott erőforráson, rendkívül nehéz meghatározni a következő lépést. Vizsgálatot végző, a biztonsági incidensek összetevőket, kapcsolódó események és a vizsgáló kapcsolatos tartalmazhat. A további elérhető információk eltérhetnek az észlelt fenyegetés típusától, valamint a környezet konfigurációjától függően, és nem minden biztonsági incidens esetében állnak rendelkezésre.

Ha van további elérhető információ, akkor a biztonsági incidensben, a riasztások listája alatt látható. Ez többek között az alábbi információkat tartalmazhatja:

- Naplótörlési események
- PNP-eszköz csatlakoztatása ismeretlen eszközről
- Riasztások, amelyek nem hasznosítható
- Új fiók létrehozása
- Fájl dekódolása a certutil eszközzel

![Szokatlan hozzáférés miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>További lépések
Ebben dokumentumban megismerhette a Security Center különböző típusú biztonsági riasztásait. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
* [Az Azure Security Center észlelési képességei](security-center-detection-capabilities.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Az Azure Security Center – gyakori kérdések](security-center-faq.md): Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure security blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
