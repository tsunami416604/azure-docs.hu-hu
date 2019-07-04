---
title: Biztonságos, Azure által felügyelt munkaállomások – Azure Active Directory ismertetése
description: Tudnivalók a biztonságos, Azure által felügyelt munkaállomásokra, és miért vagyunk fontos megérteni.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491593"
---
# <a name="understand-secure-azure-managed-workstations"></a>Biztonságos, Azure által felügyelt munkaállomások ismertetése

Biztonságos és elszigetelt munkaállomások különösen fontosak bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatási operátorok biztonságát. Ügyfélbiztonság munkaállomás biztonsága sérül, ha sok biztonsági vezérlők és biztosítékok sikertelen vagy érvénytelen.

Ez a dokumentum ismerteti, milyen kialakításához szükséges biztonságos munkaállomást, gyakran egy emelt hozzáférési szintű munkaállomásokat (PAW) néven ismert. A cikk részletes utasításokat az első biztonsági vezérlőket is tartalmaz. Ez az útmutató azt ismerteti, hogyan felhőalapú technológiáját kezelheti a szolgáltatást. A Windows 10RS5, Microsoft Defender komplex veszélyforrások elleni védelem (ATP), az Azure Active Directory és Intune-ban bevezetett biztonsági képességeket támaszkodik.

> [!NOTE]
> Ez a cikk leírja a biztonságos munkaállomás és az fontos fogalma. Ha már ismeri a fogalom, és szeretné hagyni a központi telepítés, látogasson el a [üzembe helyezése egy biztonságos munkaállomás](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-secure-workstation-access-is-important"></a>Miért fontos biztonságos munkaállomás hozzáférés

A cloud services és a bárhonnan dolgozhatnak gyors bevezetése új kiaknázása metódus hozott létre. Lehetőségeinek kihasználásával egyszerű biztonsági ellenőrzéseket az eszközökön, amikor a rendszergazdák működik, a támadók privilegizált erőforrásokhoz való hozzáférést kaphatnak.

Kiemelt visszaél és ellátási lánc támadások a felső öt módszereket használó támadók közel jár a megszegéshez szervezetek között. Is zajlik, a második leggyakrabban észlelt taktika incidensek jelentett a következők szerint 2018-ban a [veszély Verizontól jelentés](https://enterprise.verizon.com/resources/reports/dbir/), és a [biztonsági információs jelentései](https://aka.ms/sir).

A legtöbb támadók kövesse az alábbi lépéseket:

1. Felderítés kereshet olyan módon, gyakran az iparágban.
1. Elemzési információkat gyűjthet, és a egy munkaállomás, amely alacsony érték kezeli behatolni a legjobb módszer azonosítására.
1. Adatmegőrzés áthelyezése azt jelenti, hogy a keresett [oldalirányban](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Bizalmas adatok kiszűrése.

Felderítése során hajtanak a támadók gyakran úgy tűnik, alacsony kockázatú vagy undervalued behatolni. Keresse meg az oldalirányú Mozgás lehetőséget, és a rendszergazda felhasználók és eszközök ezen sebezhető eszközöket használnak. Emelt szintű felhasználói szerepköröket hozzáférést kapnak, miután a támadók azonosíthatja az értékes adatokat, és sikeresen próbál kiszűrni a rendszerből, hogy adatokat.

![Tipikus biztonsági sérülés minta](./media/concept-azure-managed-workstation/typical-timeline.png)

Ez a dokumentum ismerteti a megoldás, amely segít megvédeni a számítási eszközök ilyen oldalirányú támadások ellen. A megoldás felügyeleti és szolgáltatásai számára a kevésbé értékes hatékonyságnövelő eszközök, a használhatatlanná tévő a láncban, mielőtt az eszköz, amely hozzáfér a bizalmas felhőalapú erőforrásokhoz is lehet beszűrődéses különíti el. A megoldás a natív Azure-a Microsoft 365 nagyvállalati verzió verem részét képező szolgáltatásokat használja:

* Kezelés és a egy biztonságos elemek listájához, alkalmazások és az URL-címek az Intune-ban
* Autopilot-eszköz beállítása, üzembe helyezéséhez és frissítés
* A felhasználókezelést, a feltételes hozzáférés és a multi-factor authentication szolgáltatás Azure ad-ben
* Windows 10 (aktuális verzió) az eszköz állapotának igazolási és a felhasználói élmény
* Defender ATP a felhő által felügyelt endpoint protection észlelés és válasz
* Az engedélyezési és – igény (szerinti JIT) kezelése az Azure AD PIM privilegizált erőforrásokhoz való hozzáférés

## <a name="who-benefits-from-a-secure-workstation"></a>Akik számos előnyt biztosít egy biztonságos munkaállomásról?

Minden felhasználó és operátorok hasznot egy biztonságos munkaállomás használata esetén. A támadó károsan befolyásolja az adott számítógép vagy eszköz tudja megszemélyesíteni az összes gyorsítótárazott fiókok. Ha bejelentkezett az eszközre, akkor is használhatják és jogkivonatokat. A kockázat teszi fontos az emelt szintű szerepköröket, beleértve a rendszergazdai jogokat használt biztonságos eszközöket. A kiemelt jogosultságú fiókok eszközök azok az oldalirányú mozgás és a jogosultság-eszkalációs támadásokat céljainak. Ezek a fiókok például használható különböző eszközök:

* A helyszíni vagy felhőalapú rendszerek rendszergazdája
* Fejlesztői munkaállomásán kritikus rendszerek
* Közösségi fiók rendszergazdája magas-nal
* Szigorúan bizalmas munkaállomás, például a terminál SWIFT fizetés
* Munkaállomás kezelési kereskedelmi titkok

Kockázatcsökkentés, emelt szintű biztonsági vezérlőket az emelt szintű munkaállomások, győződjön meg arról, hogy használja a fenti fiókok kell megvalósítania. További információkért lásd: a [Azure Active Directory szolgáltatás üzembe helyezési útmutató](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [Office 365 ütemterv](https://aka.ms/o365secroadmap), és [biztonságossá tétele emelt szintű hozzáférés védelmének ütemterve](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Miért érdemes használni a dedikált munkaállomásokat?

Bár lehetséges biztonsági hozzáadása egy meglévő eszközt, akkor célszerűbb szeretne kezdeni egy biztonságos alaprendszerrel. A magas biztonsági szintű a legjobb helyzetben a szervezet helyezi, kezdő, ismeri-eszközzel rendelkező biztonságos, és végrehajtja az ismert biztonsági vezérlőket.

E-mailek és a webböngészés támadási vektorok egyre nagyobb számban egyre rögzített, és ellenőrizze, hogy egy eszközt, hogy a megbízható teszi. Ez az útmutató feltételezi, hogy dedikált munkaállomást el különítve a standard szintű hatékonyságot, tallózása és e-mailt. Termelékenység, a webböngészést és a egy eszközről az e-mailek eltávolításának negatív hatással lehet a hatékonyságot. Azonban ez a biztosíték, általában elfogadható forgatókönyvekhez, ahol a feldolgozás feladatokat nem explicit módon igényelnek, és a túl magas a biztonsági incidensek kockázatát.

> [!NOTE]
> Tetszőleges webhelyekhez, amely lehet a magas kockázatú tevékenység általános hozzáférési webböngészés itt hivatkozik. Az ilyen böngészés egy különböznek kis számú jól ismert felügyeleti webhelyei szolgáltatások, például az Azure, Office 365-höz, más felhőszolgáltatók és a SaaS-alkalmazások eléréséhez használt webböngésző.

Tartalmazottsági stratégiák elősegíti a biztonság megerősítését száma és típusa, amelyek egy támadó pereket hozzáférjenek bizalmas eszközök növelésével. A jelen cikkben ismertetett modell egy rétegzett jogosultság tervezési használja, és korlátozza a rendszergazdai jogosultságokat az egyes eszközöktől.

## <a name="supply-chain-management"></a>Ellátásilánc-kezelés

Munkaállomás biztonságos kulcskezelésnek fontos szerepe van egy ellátási lánc megoldás ahol egy megbízható Munkaállomás megbízhatósági root nevű használ. Ebben a megoldásban a a megbízható legfelső szintű használ [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) technológia. Egy munkaállomás biztonságos, Autopilot lehetővé teszi Microsoft optimalizált OEM a Windows 10-es eszközöket használhatja. Ezek az eszközök ismert megfelelő állapotban a gyártótól származnak. Helyett rendszerképének alaphelyzetbe állítása a potenciálisan nem biztonságos eszközök, az Autopilot alakíthatja át egy Windows eszköz "üzleti használatra kész" állapotba. Azt vonatkozik, beállítások és szabályzatok, alkalmazások telepítése, és még akkor is megváltozik a Windows 10-es kiadása. Például Autopilot előfordulhat, hogy egy eszköz Windows telepítés módosítása a Windows 10 Pro, Windows 10 Enterprise, hogy azt a speciális funkciók is használhatók.

![Biztonságos munkaállomás szintek](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Eszköz szerepkörök és profilok

Ez az útmutató számos biztonsági profilok hivatkozik, és a szerepköröket, amelyek segítségével a felhasználók, a fejlesztők és informatikai munkatársak biztonságosabb megoldásokat hozhat létre. Ezek a profilok egyenleg használhatósága és kockázatok közös felhasználó esetében, amely egy kibővített vagy biztonságos munkaállomásról előnyeit. A beállítások az itt elérhető konfigurációk elfogadott iparági szabványok alapulnak. Ez az útmutató bemutatja, hogyan erősíti a Windows 10-es, és így csökkenthető a társított eszköz vagy felhasználó biztonsági sérülése. Így kezeléséhez a biztonsági funkciókat és a kockázatok házirend- és technológia használatával hajtja végre.
![Biztonságos munkaállomás szintek](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Alacsony biztonsági** – felügyelt, a standard munkaállomás jó kiindulási pontot biztosít a legtöbb otthoni és üzleti célú használatra. Ezek az eszközök az Azure AD-ben regisztrált és felügyelt Intune-nal. Ez a profil lehetővé teszi a felhasználóknak minden olyan alkalmazások futtatására, és bármely webhelyen keresse. Egy kártevőirtó megoldást, például [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) engedélyezni kell.

* **Fokozott biztonsági** – a belépő szintű, védett megoldás az otthoni felhasználók kis az üzleti felhasználók és általános fejlesztők számára hasznos.

   A továbbfejlesztett munkaállomás módja a csoportházirend-alapú alacsony biztonsági profiljának biztonságának növelése érdekében. Biztosít egy biztonságos azt jelenti, hogy hatékonyságnövelő eszközök, mint például az e-mailek és a webböngészés használatát az ügyfél adatokkal dolgozni. Naplózási házirendek és az Intune használatával figyelheti a továbbfejlesztett munkaállomásról a felhasználói viselkedés, és a profil használata. A továbbfejlesztett munkaállomás-profilt a Windows 10-es (1809) parancsfájl telepít, és használja ki speciális kártevő védelem használatával [a speciális veszélyforrások elleni védelem (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp).

* **Magas biztonsági** – a leghatékonyabb azt jelenti, hogy a munkaállomás a támadási felület csökkentése érdekében, hogy távolítsa el a munkaállomás adminisztrálhatják lehetővé teszi. Helyi rendszergazdai jogosultságok eltávolítása a lépést, amely javítja a biztonságot, azonban ez befolyásolhatja a termelékenység nem megfelelően megvalósított. A magas biztonsági profil épül, amely a fokozott biztonság profilt egy jelentős változás: a helyi rendszergazda eltávolítása Ez a profil célja nagy profil felhasználók számára: vezetők, a bérszámfejtési és a bizalmas adatok felhasználók, a jóváhagyók a szolgáltatások és -folyamatok.

   A magas biztonsági felhasználó több ellenőrzött környezetben igénylést, miközben továbbra is megtekintheti például a levelezéshez és böngészéshez használható egyszerű élmény tevékenységek végrehajtására. A felhasználók elvárják szolgáltatások, például cookie-kat, Kedvencek és más parancsikonok működéséhez. Ezek a felhasználók azonban nem követelhet meg a módosítása, vagy ha hibaelhárításra eszköz lehetővé teszi. Akkor is nem kell telepíteni az illesztőprogramokat. A magas biztonsági profil a magas biztonsági – a Windows 10-es (1809) parancsfájl használatával lett telepítve.

* **Speciális** – támadók célba fejlesztők és rendszergazdák számára, mert azok módosíthatja annak a támadók rendszerek. A speciális munkaállomás kibővíti a házirendeket a magas biztonsági munkaállomás helyi alkalmazások kezelésére és a websites korlátozásával. Emellett meggátolja a magas kockázatú hatékonyságnövelő funkciókat, például az ActiveX, a Java, a böngésző beépülő modulok és egyéb Windows-vezérlők. Ezt a profilt az DeviceConfiguration_NCSC – Windows 10-es (1803) SecurityBaseline parancsfájl.

* **Védett** – a támadó károsan befolyásolja az egy rendszergazdai fiók is kárt okoznának jelentős üzleti adatok ellopása, adatok módosítását vagy szolgáltatáskimaradás lép fel. A megerősített állapotban a munkaállomás lehetővé teszi, hogy a biztonsági vezérlőket és a szabályzatok, amelyek korlátozzák a helyi kezelés közvetlen ellenőrzése. Egy biztonságos munkaállomás rendelkezik nincs hatékonyságnövelő eszközök így nehezebb a veszélyezteti az eszközt. A leggyakoribb vektor adathalász támadások blokkol: e-mailek és a közösségi médiában.  A biztonságos munkaállomás – Windows 10-es (1809) SecurityBaseline szkriptet a biztonságos munkaállomás helyezhetők.

   ![Biztonságos munkaállomás](./media/concept-azure-managed-workstation/secure-workstation.png)

   Egy biztonságos munkaállomás egy megerősített munkaállomás, amely rendelkezik tiszta Alkalmazásvezérlés és az alkalmazásőr rendelkező rendszergazda biztosít. A munkaállomás rés kiaknázása elleni védelem, a credential guard és a device guard használatával a gazdagép védelmét a rosszindulatú működést. Az összes helyi lemez BitLocker is titkosítva van.

* **Elkülönített** – egyéni, offline példánkban az értéktartományon végéig jelöli. Ebben az esetben nem a telepítési parancsprogram biztosított. Szüksége lehet üzleti szempontból kritikus fontosságú függvény, amely nem támogatott vagy veszéllyel régebbi operációs rendszert kell kezelni. Például a nagy értékű gyártósor vagy életre – támogatási rendszer. Fontos biztonsági és a cloud services nem érhető el, mert Ön kezelhesse és frissíthesse az ezeken a számítógépeken, manuálisan vagy egy elkülönített Active Directory-erdő architektúrával például a továbbfejlesztett biztonsági felügyeleti környezet (ESAE). Ilyen körülmények között fontolja meg az alapszintű Intune és az ATP állapot-ellenőrzések kivételével minden hozzáférés eltávolítása.

  * [Az Intune hálózati kommunikáció követelmény](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [Szükséges a ATP hálózati kommunikáció](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>További lépések

[Üzembe helyezése az Azure által kezelt biztonságos munkaállomás](howto-azure-managed-workstation.md).
