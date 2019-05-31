---
title: Miért fontosak a biztonságos munkaállomások – Azure Active Directory
description: Ismerje meg, miért hozzon létre szervezetek biztonságos Azure által felügyelt munkaállomások
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
ms.openlocfilehash: b7e9ca9fa26e9744eb0a9bfafe692a096825b0b5
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357052"
---
# <a name="building-secure-workstations"></a>Biztonságos munkaállomások készítése

Biztonságos, elkülönített munkaállomások különösen fontosak bizalmas szerepkörök, például a rendszergazdák, fejlesztők és üzemeltetők a kritikus szolgáltatások biztonságát. Számos további biztonsági vezérlők és biztosítékok sikertelen, vagy nem hatása, ha az alapul szolgáló ügyfél munkaállomás biztonsága sérült a biztonsága.

Ez a dokumentum ismerteti, milyen buildeléséhez részletes részletes utasításokat, beleértve a biztonsági vezérlőket indítása beállítása biztonságos ügyfél munkaállomás. Az ilyen típusú munkaállomások esetenként egy emelt hozzáférési szintű munkaállomásokat (PAW), amely ebben a leírásban használt, és építeni nevezzük. Az útmutató azonban úgy tűnik, a szolgáltatás kezelése a felhőalapú technológia, és Windows 10RS5, Microsoft Defender ATP-ben, az Azure Active Directory és az Intune kezdődően bevezetett biztonsági képességek jelennek meg.

## <a name="why-securing-workstation-access-is-important"></a>Miért munkaállomás hozzáférés biztonságossá tétele fontos

A cloud services és a bárhonnan dolgozhatnak gyors bevezetése hozott létre egy új módszer kiaknázása. A támadók közzé gyenge biztonsági ellenőrzéseket az eszközökön, ahol a rendszergazdák működik, és tudni privilegizált erőforrásokhoz való hozzáférés elnyeréséhez.

Leírtak szerint a [veszély Verizontól jelentés](https://enterprise.verizon.com/resources/reports/dbir/), és [biztonsági információs jelentései](https://aka.ms/sir) kiemelt visszaél és ellátási lánc támadások az öt legaktívabb mechanizmusok megsérti a szervezetben használt közé tartoznak, és a a második leggyakrabban észlelt ügyfélkódos incidensek jelentett 2018-ban.

Legtöbb támadók kövesse az alábbi elérési utat:

* Felderítés, iparág, megtalálni a gyakran jellemző kipróbálása
* Elemezheti az összegyűjtött információk alapján azonosíthatja a legjobb azt jelenti, hogy a mintavételezéskor alacsony érték munkaállomás (beszivárgás) eléréséhez
* Adatmegőrzés, és azt jelenti, hogy áthelyezéséhez tekintse [oldalirányban](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* Bizalmas adatokat eltulajdonítani

A támadók gyakran behatolni a felderítést úgy tűnik, alacsony kockázatú vagy undervalued eszközök. Ezek az érintett eszközök majd keresse meg az oldalirányú Mozgás lehetőséget, a rendszergazda felhasználók és eszközök keresése és a nagy értékű adatok sikeresen eltulajdonítani információkhoz után ezek a rendszerjogosultságú felhasználói szerepkörök kapnak azonosítására szolgálnak.

![Tipikus biztonsági sérülés minta](./media/concept-azure-managed-workstation/typical-timeline.png)

Ez a dokumentum az adatok védelme számítási elkülöníti a felügyeleti és az oldalirányú mozgás elleni védelem érdekében szolgáltatások megoldást kínál, vagy támadások, a kevésbé értékes hatékonyságnövelő eszközök. A kialakítás csökkenti sikeres végrehajtásához megsértése által a lánc előtt beszivárgás kezeléséhez, vagy bizalmas felhőalapú erőforrások eléréséhez használt eszköz használhatatlanná teszi. A megoldás leírt felhasznál natív Azure-szolgáltatások része a Microsoft 365 nagyvállalati verzió stack többek között:

* Az eszköz kezelését, ideértve az alkalmazás- és URL-cím engedélyezése az Intune-ban
* Autopilot-eszköz beállítása és üzembe helyezés és frissítés 
* A felhasználókezelést, a feltételes hozzáférés és a multi-factor authentication szolgáltatás Azure ad-ben
* Windows 10 (aktuális verzió) az eszköz állapotának igazolási és a felhasználói élmény
* A Microsoft Defender komplex veszélyforrások elleni védelem (ATP) az endpoint protection, észlelés és válasz a cloud management
* Az Azure AD PIM felügyeletére engedélyezés, beleértve a csak az idő szerinti (JIT) privilegizált erőforrásokhoz való hozzáférés

## <a name="who-benefit-from-using-a-secure-workstation"></a>Akik előnye a biztonságos munkaállomás

Minden felhasználó és operátorok kihasználhatják a biztonságos munkaállomás használatával. A támadó feltörések egy számítógép vagy eszköz teheti több szempontot, beleértve a megszemélyesíteni minden gyorsítótárazott fiókokat, és a hitelesítő adatok használata és bejelentkeztek az adott eszközön használt tokenek. A kockázat teszi biztonságossá tétele bármely kiemelt szerepkörhöz, beleértve a rendszergazdai jogokat, ezért fontos, ahol egy rendszerjogosultságú fiókot használt eszközök azok az oldalirányú mozgás és a jogosultság-eszkalációs támadásokat célértékei használt eszközök. Ezek a fiókok használhatók az eszközök különböző például:

* A helyszíni és felhőalapú rendszerek rendszergazdái
* Fejlesztői munkaállomások kritikus rendszerek
* Közösségi fiókok rendszergazdai magas-nal
* Például a SWIFT fizetési terminálok szigorúan bizalmas munkaállomások
* A munkaállomások kereskedelmi titkok kezelése

A Microsoft azt javasolja, ahol ezek a fiókok használhatók kockázat csökkentéséhez emelt szintű munkaállomások emelt szintű biztonsági ellenőrzések. További útmutatást találhat a [Azure Active Directory szolgáltatás üzembe helyezési útmutató](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [Office 365 ütemterv](https://aka.ms/o365secroadmap), és [biztonságossá tétele emelt szintű hozzáférés védelmének ütemterve](https://aka.ms/sparoadmap)).

## <a name="why-dedicated-workstations"></a>Miért dedikált munkaállomásokat

Bár lehetséges biztonsági hozzáadása egy meglévő eszközt, akkor célszerűbb szeretne kezdeni egy biztonságos alaprendszerrel. Kezdve egy ismert jó eszköz és az ismert biztonsági vezérlők helyez számos szervezet karbantartásához, amely a legjobb helyzetben nagyobb biztonsági szintet. A támadási vektorok hangvételt, mintha levelezéshez és böngészéshez által engedélyezett egyre növekvő számú egyre nehéz lehet, hogy egy eszköz megbízható biztosítása esetén. Ez az útmutató működik egy dedikált munkaállomást feltételezve elkülöníti a standard szintű hatékonyságot, Tallózás, és e-mailes feladatok végezhető el. Termelékenység, a webböngészést és a egy eszközről az e-mailek eltávolításának negatív hatással lehet a hatékonyságot, de ez a biztosíték általában elfogadható forgatókönyvekhez, ahol a feldolgozás feladatokat nem explicit módon igényelnek, és a túl magas a biztonsági incidensek kockázatát.

> [!NOTE]
> Tetszőleges websites, a szolgáltatások, például az Azure, Office 365-höz, más felhőszolgáltatók és a SaaS-jól ismert felügyeleti webhelyei kis számú eléréséhez használt webböngésző különböző nagy kockázatot jelent az általános hozzáférési webböngészés itt hivatkozik az alkalmazások.

Tartalmazottsági stratégiák fokozott biztonsági intézkedésekkel száma és típusa, a vezérlőelemek ahhoz, hogy hozzáférhessen az érzékeny eszközökre, hogy rendelkezik egy támadó növelésével adja meg. A modell itt fejlesztett felügyeleti jogosultságokat az egyes eszközöktől egy rétegzett jogosultság modellel tartalmazottsági biztosít.

## <a name="supply-chain-management"></a>Ellátásilánc-kezelés

Munkaállomás biztonságos kulcskezelésnek fontos szerepe van a ellátási lánc megoldás megbízható, a munkaállomás használata esetén a gyökér megbízhatósági. Ez a megoldás a legfelső szintű megbízhatóság használatával foglalkozik a [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) technológia. Egy biztonságos munkaállomás Microsoft Autopilot lehetővé teszi a Microsoft optimalizált OEM a Windows 10-es eszközök, amelyek egy korábbi hibátlan állapotra a gyártótól származó használhatja. Helyett rendszerképének alaphelyzetbe állítása, amely nem lehet megbízható eszköz, a Microsoft Autopilot alakíthatja át egy Windows eszköz "üzleti használatra kész" állapotba alkalmazása, beállítások és szabályzatok, alkalmazások telepítése, és használja is a Windows 10-es kiadásának megváltoztatása (például a Windows 10 Pro, Windows 10 Enterprise, a speciális funkciók támogatásához).

![Biztonságos munkaállomás szintek](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Eszköz szerepkörök és profilok

Az útmutató során több biztonsági profilok és szerepkörök kibocsátásokban megtörténik egy biztonságosabb megoldás, a felhasználóknak, fejlesztők és informatikai munkatársak elérését. Ezek a profilok rendelkezik lett igazítva közös felhasználók támogatására azoknál a szervezeteknél, egy továbbfejlesztett, illetve biztonságos munkaállomásról közben használhatósága és kockázati terheléselosztás előnyeit. Útmutatást nyújt elfogadott iparági szabványokon alapuló beállítások konfigurációját. Ez az útmutató segítségével mutatja be egy metódus az eszköz vagy felhasználó biztonsági házirend és a technológia segítségével biztonsági funkciókat és a kockázatok kezelésében nyújt segítséget a kockázatok csökkentése és korlátozások a Windows 10-es.
![Biztonságos munkaállomás szintek](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Alacsony biztonsági** – felügyelt standard munkaállomás jó kiindulási pontot biztosít a legtöbb otthoni, és üzleti célú használatra. Ezek az eszközök Azure ad-ben regisztrált és az Intune által felügyelt. A profil lehetővé teszi a felhasználóknak minden olyan alkalmazások futtatására, és bármely webhelyen keresse. Egy kártevőirtó megoldást, például [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) engedélyezni kell.
* **Fokozott biztonsági** – egy belépési szintű védett megoldás jó van otthoni felhasználók, kis az üzleti felhasználók, valamint általános fejlesztők számára.
   * A bővített munkaállomás biztosít egy csoportházirend-alapú azt jelenti, hogy az alacsony biztonsági profil a biztonság növelése érdekében. Ez a profil lehetővé teszi egy biztonságos azt jelenti, hogy az ügyfelek adataival, és használhatják a hatékonyságnövelő eszközök, például az e-mailek és a webböngészés ellenőrzése. Bővített munkaállomásról a felhasználói viselkedést, valamint egy munkaállomás profil használata naplózása naplórendek engedélyezésével, és a naplózás az Intune-hoz használható. Ezt a profilt a munkaállomás lehetővé teszi a biztonsági vezérlők és házirendek ismertetett a tartalmat, és telepítve van az a fokozott munkaállomás – Windows 10-es (1809) parancsfájlt. A központi telepítés is kihasználja speciális kártevő védelem használatával [a speciális veszélyforrások elleni védelem (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **Magas biztonsági** – a leghatékonyabb azt jelenti, hogy a munkaállomás a támadási felület csökkentése érdekében, hogy távolítsa el a munkaállomás adminisztrálhatják lehetővé teszi. Helyi rendszergazdai jogosultságok eltávolítása lépés, és fokozza a biztonságot, és is befolyásolja a hatékonyságot, ha nem megfelelően megvalósított. A magas biztonsági profil épül, amely a fokozott biztonság profilt egy jelentős változás, a helyi rendszergazda eltávolítása Ez a profil célja lehet egy vezető például nagy profil felhasználó vagy felhasználók, előfordulhat, hogy forduljon a bizalmas adatok, például a bérszámfejtési vagy szolgáltatásokat, és folyamatok jóváhagyásának felhasználókkal.
   * A magas biztonsági felhasználói profil igénylést magasabb ellenőrzött környezetben, miközben továbbra is megtekintheti a termelékenység tevékenységgel, például a levelezés és a egy egyszerű felület használatára fenntartása mellett a webböngészést végrehajtásához. A felhasználók elvárják szolgáltatások, például cookie-kat, Kedvencek és más parancsikonok működéséhez érhető el. Ezek a felhasználók azonban előfordulhat, hogy nincs szüksége arra, módosítására és hibakeresése az eszközt, és nem kell telepíteni az illesztőprogramokat. A magas biztonsági profil a magas biztonsági – a Windows 10-es (1809) parancsfájl használatával lett telepítve.
* **Speciális** – a fejlesztők és rendszergazdák vonzó célpontjai támadók számára, ezek a szerepkörök módosítani tudja a támadók számára érdekes rendszerek. A speciális munkaállomás részéről az erőfeszítés, a magas biztonsági munkaállomás, és további emphases a helyi alkalmazások kezelésére, korlátozza az internetes webhelyek és korlátozza a termelékenységet, amelyek nagy biztonsági kockázatot például az ActiveX, az üzembe helyezett vesz igénybe. Java, a böngésző beépülő modul és a egy Windows eszköz számos más ismert magas kockázatú vezérlőket. Ezt a profilt a munkaállomás lehetővé teszi a biztonsági vezérlők és szabályzatok ismertetett a tartalmat, és telepítve van az DeviceConfiguration_NCSC – Windows 10-es (1803) SecurityBaseline parancsfájl.
* **Védett** – a támadó kedvezőtlenül befolyásolhatja a egy rendszergazdai fiók is általában kárt okoznának jelentős üzleti adatok ellopása, adatok módosítását vagy szolgáltatáskimaradás lép fel. A megerősített állapotban a munkaállomás lehetővé teszi a biztonsági vezérlőket és a szabályzatok, amelyek korlátozzák a közvetlen befolyása alatt helyi kezelés, és a hatékonyságnövelő eszközök el lesznek távolítva. Ennek eredményeképpen az eszköz veszélyeztetése létesítése nehezebb, mint a levelezés, és a közösségi média le vannak tiltva a leggyakoribb módja sikeres adathalász támadások tükröző.  A biztonságos munkaállomás – Windows 10-es (1809) SecurityBaseline szkriptet a biztonságos munkaállomás helyezhetők.

   ![Biztonságos munkaállomás](./media/concept-azure-managed-workstation/secure-workstation.png)

   Egy biztonságos munkaállomást biztosít a rendszergazda a megerősített munkaállomás, amely rendelkezik tiszta Alkalmazásvezérlés, és az alkalmazásőr. A munkaállomás használandó hitelesítő adatokat, az eszköz és a biztonsági rés kiaknázása a gazdagép védelmét a rosszindulatú működést. Emellett az összes helyi lemezek van titkosítva, a Bitlocker titkosítást.

* **Elkülönített** – egyéni offline példánkban az értéktartományon (ebben az esetben nem a telepítési parancsprogram biztosított) végéig jelöli. Szervezetek szükség lehet egy elszigetelt üzleti kritikus például a nagy értékű gyártósor vagy életre támogatási rendszerek igénylő függvény nem támogatott/nem tartalmazó régebbi operációs rendszerek kezelése. Fontos biztonsági és a cloud services nem érhető el, mert szervezetek előfordulhat, hogy kézzel ezeket a számítógépeket kezelése/frissítése vagy egy elkülönített Active Directory-erdő architektúra (például a továbbfejlesztett biztonsági felügyeleti környezet (ESAE)) használata kezelheti őket. Ezen esetekben eltávolítása az egyszerű Intune-ban, és az ATP kivételével minden hozzáférés állapotának ellenőrzése kell tekinteni.
   * [Az Intune hálózati kommunikáció követelmény](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Szükséges a ATP hálózati kommunikáció](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>További lépések

[Egy biztonságos Azure által felügyelt munkaállomás üzembe helyezése](howto-azure-managed-workstation.md)
