---
title: A biztonságos, Azure által felügyelt munkaállomások ismertetése – Azure Active Directory
description: Tudjon meg többet a biztonságos, Azure által felügyelt munkaállomásokról, és Ismerje meg, hogy miért fontosak.
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
ms.openlocfilehash: 00e5e4a9751a03fe961fe02fec28f41b49e5ba43
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175161"
---
# <a name="understand-secure-azure-managed-workstations"></a>A biztonságos, Azure által felügyelt munkaállomások ismertetése

A biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. Ha az ügyfél-munkaállomás biztonsága sérült, számos biztonsági vezérlő és garancia meghiúsulhat, vagy hatástalan lehet.

Ez a dokumentum ismerteti, hogy mire van szüksége a biztonságos munkaállomás létrehozásához, amelyet gyakran más néven Kiemelt hozzáférési munkaállomás (PAW) használ. A cikk a kezdeti biztonsági vezérlők beállítására vonatkozó részletes utasításokat is tartalmaz. Ez az útmutató leírja, hogyan kezelheti a felhőalapú technológia a szolgáltatást. A Windows 10RS5, a Microsoft Defender komplex veszélyforrások elleni védelem (ATP), a Azure Active Directory és az Intune által bevezetett biztonsági képességekre támaszkodik.

> [!NOTE]
> Ez a cikk a biztonságos munkaállomás fogalmát és fontosságát ismerteti. Ha már ismeri a koncepciót, és ki szeretné hagyni az üzembe helyezést, látogasson el [a biztonságos munkaállomás üzembe helyezése](howto-azure-managed-workstation.md)lehetőségre.

## <a name="why-secure-workstation-access-is-important"></a>Miért fontos a biztonságos munkaállomás-hozzáférés

A Cloud Services gyors bevezetése és a bárhonnan végzett munka lehetősége új kiaknázási módszert hozott létre. Gyenge biztonsági vezérlők kiaknázásával olyan eszközökön, ahol a rendszergazdák dolgoznak, a támadók hozzáférhetnek a Kiemelt erőforrásokhoz.

A magas jogosultsági szintű visszaélések és az ellátási lánc elleni támadások a támadók által a szervezeteknek felhasználható öt módszer közé tartoznak. A második leggyakrabban észlelt incidensek a 2018-as, a [Verizon Threat-jelentésnek](https://enterprise.verizon.com/resources/reports/dbir/)és a [biztonsági intelligencia jelentésnek](https://aka.ms/sir)megfelelően jelentett incidensek esetében is.

A legtöbb támadó a következő lépéseket hajtja végre:

1. Felderítési módszer, amely gyakran az iparágra jellemző.
1. Elemzés az adatok gyűjtéséhez és az alacsony értékként érzékelt munkaállomás beszivárgásának legjobb módjának azonosításához.
1. Az adatmegőrzést úgy kell megkeresni, hogy [később](https://en.wikipedia.org/wiki/Network_Lateral_Movement)áthelyezhető legyen.
1. A bizalmas és a bizalmas adatok kiszűrése.

A felderítés során a támadók gyakran szűrik az alacsony kockázatú vagy alulértékelt eszközöket. Ezeket a sebezhető eszközöket használják az oldalirányú mozgás lehetőségének megkeresésére, valamint a rendszergazda felhasználók és eszközök megtalálására. Miután hozzáférnek a Kiemelt felhasználói szerepkörökhöz, a támadók azonosítják a nagy értékű értékeket, és sikeresen exfiltrate ezeket az adatforrásokat.

![Tipikus kompromisszumos minta](./media/concept-azure-managed-workstation/typical-timeline.png)

Ez a dokumentum egy olyan megoldást ismertet, amely segíthet a számítástechnikai eszközök védelmében az ilyen oldalirányú támadásokkal szemben. A megoldás elkülöníti a felügyeletet és a szolgáltatásokat a kevésbé értékes hatékonyságnövelő eszközökről, így a lánc megszakadhat, mielőtt a bizalmas Felhőbeli erőforrásokhoz hozzáférő eszköz beszivároghat. A megoldás a Microsoft 365 Nagyvállalati verzió stack részét képező natív Azure-szolgáltatásokat használja:

* Intune az eszközök felügyeletéhez és az alkalmazások és URL-címek biztonságos listájához
* Robotpilóta az eszközök beállításához, üzembe helyezéséhez és frissítéséhez
* Azure AD a felhasználók felügyeletéhez, a feltételes hozzáféréshez és a többtényezős hitelesítéshez
* Windows 10 (aktuális verzió) az Eszközállapot-igazoláshoz és a felhasználói élményhez
* Defender ATP a felhőben felügyelt Endpoint Protection, észlelés és válasz esetén
* Azure AD PIM az engedélyezési és az igény szerinti (JIT) jogosultsági szintű hozzáférés kezeléséhez az erőforrásokhoz

## <a name="who-benefits-from-a-secure-workstation"></a>Kik a biztonságos munkaállomás előnyei?

A biztonságos munkaállomás használatakor minden felhasználó és operátor előnyt élvez. Egy számítógépet vagy eszközt veszélyeztető támadó megszemélyesítheti az összes gyorsítótárazott fiókot. Az eszközre való bejelentkezéskor hitelesítő adatokat és tokeneket is használhatnak. Ez a kockázat fontos a Kiemelt szerepkörökhöz használt eszközök, például a rendszergazdai jogosultságok biztonságossá tételéhez. Az emelt szintű fiókkal rendelkező eszközök az oldalirányú mozgás és a jogosultság-eszkalációs támadások céljai. Ezek a fiókok számos különféle eszközhöz használhatók, például:

* Helyszíni vagy felhőalapú rendszerek rendszergazdája
* Fejlesztői munkaállomás a kritikus rendszerekhez
* Közösségi média-fiók rendszergazdája magas expozícióval
* Nagyon érzékeny munkaállomás, például gyors fizetési terminál
* Kereskedelmi titkokat kezelő munkaállomás

A kockázat csökkentése érdekében emelt szintű biztonsági ellenőrzéseket kell megvalósítani a fiókokat használó, Kiemelt munkaállomások számára. További információkért tekintse meg a [Azure Active Directory funkció üzembe helyezési útmutatóját](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), az [Office 365 menetrendjét](https://aka.ms/o365secroadmap)és az emelt [szintű hozzáférési ütemterv biztonságossá](https://aka.ms/sparoadmap)tételét ismertető témakört.

## <a name="why-use-dedicated-workstations"></a>Miért érdemes dedikált munkaállomásokat használni?

Habár lehetőség van a biztonság hozzáadására egy meglévő eszközön, érdemes egy biztonságos alapot kezdeni. Ahhoz, hogy a szervezete a legjobb helyzetben legyen a magas biztonsági szint fenntartása érdekében, kezdjen hozzá egy ismert eszközzel, és hozzon létre egy ismert biztonsági vezérlőt.

Az e-mailen és a webböngészésen keresztül egyre növekvő számú támadási vektor teszi lehetővé, hogy az eszköz megbízható legyen. Ez az útmutató azt feltételezi, hogy egy dedikált munkaállomás el van különítve a szabványos termelékenységtől, a böngészéstől és az e-mailtől. A hatékonyság, a webböngészés és az e-mailek eszközről való eltávolítása negatív hatással lehet a hatékonyságra. Ez a védelem azonban általában elfogadható olyan helyzetekben, amikor a feladat feladatai kifejezetten nem igénylik, és a biztonsági incidensek kockázata magas.

> [!NOTE]
> A webböngészés itt az olyan tetszőleges webhelyekhez való általános hozzáférésre utal, amelyek nagy kockázatú tevékenységnek lehetnek kitéve. Az ilyen böngészés nem különbözik a webböngészők kis számú, jól ismert felügyeleti webhelyének eléréséhez, mint például az Azure, az Office 365, más felhőalapú szolgáltatók és SaaS-alkalmazások.

Az adattárolási stratégiák megszigorítják a biztonságot azáltal, hogy növelik azon vezérlők számát és típusát, amelyek megakadályozzák, hogy a támadók hozzáférjenek az érzékeny eszközökhöz. A cikkben ismertetett modell egy többszintű jogosultságok kialakítását használja, és bizonyos eszközökre korlátozza a rendszergazdai jogosultságokat.

## <a name="supply-chain-management"></a>Ellátási lánc kezelése

A biztonságos munkaállomás esetében elengedhetetlen egy olyan ellátási Láncos megoldás, amelyben a "megbízhatóság gyökere" nevű megbízható munkaállomás használatos. Ebben a megoldásban a megbízhatóság gyökerének a [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) technológiáját kell használnia. Egy munkaállomás biztonságossá tételéhez az Autopilot lehetővé teszi a Microsoft OEM-re optimalizált Windows 10-es eszközök kihasználása. Ezek az eszközök a gyártótól származó ismert jó állapotba kerülnek. A potenciálisan nem biztonságos eszközök rendszerképének átállítása helyett az Autopilot képes a Windows-eszközök "üzleti használatra kész" állapotba alakítására. Alkalmazza a beállításokat és a házirendeket, telepíti az alkalmazásokat, és még módosítja a Windows 10 kiadását. Előfordulhat például, hogy az Autopilot a Windows 10 Pro verzióról a Windows 10 Enterprise rendszerre módosítja az eszköz Windows rendszerre való telepítését, hogy az képes legyen speciális funkciók használatára.

![Biztonságos munkaállomás-szintek](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Eszközök szerepkörei és profiljai

Ez az útmutató számos olyan biztonsági profilt és szerepkört hivatkozik, amelyek segítségével biztonságosabb megoldásokat hozhat létre a felhasználók, a fejlesztők és az informatikai szakemberek számára. Ezek a profilok kiegyensúlyozzák a használhatóságot és a kockázatokat olyan gyakori felhasználók számára, akik kihasználhatják a bővített vagy biztonságos munkaállomásokat. Az itt megadott beállítások az iparág által elfogadott szabványokon alapulnak. Ez az útmutató bemutatja, hogyan erősítheti meg a Windows 10 rendszert, és csökkentheti az eszköz vagy a felhasználó biztonságával kapcsolatos kockázatokat. Ezt a házirend és a technológia segítségével végezheti el a biztonsági funkciók és kockázatok kezeléséhez.
![biztonságos munkaállomás-szintek](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Alacsony biztonság** – a felügyelt, szabványos munkaállomás jó kiindulási pontot biztosít a legtöbb otthoni és kisméretű üzleti használathoz. Ezek az eszközök regisztrálva vannak az Azure AD-ben, és az Intune-nal kezelhetők. Ez a profil lehetővé teszi, hogy a felhasználók bármilyen alkalmazást futtassanak, és böngésszenek bármely webhelyen. Egy kártevő szoftver, például a [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) engedélyezése szükséges.

* **Fokozott biztonság** – ez a belépési szintű, védett megoldás jó választás az otthoni felhasználók, a kisvállalkozások és az általános fejlesztők számára.

   A továbbfejlesztett munkaállomás egy házirend-alapú módszer az alacsony biztonsági profil biztonságának növelésére. Biztonságos eszközöket biztosít az ügyféladatok használatához, miközben olyan hatékonyságnövelő eszközöket is használhat, mint az e-mailek és a webböngészés. A naplózási házirendekkel és az Intune-nal figyelheti a felhasználók viselkedésére és a profilok használatára szolgáló bővített munkaállomást. Üzembe helyezi a továbbfejlesztett munkaállomás-profilt a Windows10 (1809) parancsfájllal, és kihasználhatja a fejlett kártevők elleni védelem előnyeit a komplex [veszélyforrások elleni védelem (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)használatával.

* **Magas biztonság** – a leghatékonyabb módszer a munkaállomás támadási felületének csökkentésére, hogy eltávolítsa a munkaállomás önálló felügyeletének képességét. A helyi rendszergazdai jogosultságok eltávolítása egy olyan lépés, amely javítja a biztonságot, de a hatékonyságot nem megfelelően implementálja. A magas biztonsági profil a fokozott biztonsági profilra épít egy jelentős módosítással: a helyi rendszergazda eltávolításával. Ez a profil nagy horderejű felhasználók számára készült: vezetők, bérszámfejtés és bizalmas adatok felhasználói, a szolgáltatásokhoz és folyamatokhoz tartozó jóváhagyók.

   A magas biztonsági szintű felhasználók hatékonyabban felügyelt környezetet igényelnek, miközben továbbra is végezhetnek olyan tevékenységeket, mint például az e-mailek és a webböngészés egy egyszerű használatú felhasználói élményben. A felhasználók olyan szolgáltatásokat várnak, mint például a cookie-k, a kedvencek és más, a munkához szükséges parancsikonok. Előfordulhat azonban, hogy ezek a felhasználók nem igénylik az eszköz módosítását vagy hibakeresését. Emellett nem kell illesztőprogramokat telepíteniük. A magas biztonsági profilt a High Security-Windows10 (1809) parancsfájl használatával helyezi üzembe.

* **Speciális** – a támadók megcélozzák a fejlesztőket és a rendszergazdákat, mivel a támadók számára érdekes rendszereket módosíthatnak. A speciális munkaállomás a helyi alkalmazások kezelésével és a webhelyek korlátozásával kiterjeszti a magas biztonsági munkaállomás szabályzatait. Emellett korlátozza a nagy kockázatú hatékonyságnövelő képességeket, például az ActiveX, a Java, a böngésző beépülő modulok és más Windows-vezérlők használatát. Ezt a profilt a DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline-parancsfájllal helyezheti üzembe.

* **Biztonságos** – a rendszergazdai fiókokat veszélyeztető támadók jelentős üzleti károkat okozhatnak az adatlopással, az adatmódosítással vagy a szolgáltatás megszakadásával kapcsolatban. Ebben a megerősített állapotban a munkaállomás engedélyezi az összes olyan biztonsági vezérlőt és házirendet, amely korlátozza a helyi alkalmazások felügyeletének közvetlen ellenőrzését. A biztonságos munkaállomás nem rendelkezik hatékonyságnövelő eszközökkel, így az eszköz nehezebben sérülhet. Blokkolja a leggyakoribb adathalászi támadásokat: e-mail-cím és közösségi média.  A biztonságos munkaállomás üzembe helyezhető a Secure Workstation-Windows10 (1809) SecurityBaseline-parancsfájllal.

   ![Biztonságos munkaállomás](./media/concept-azure-managed-workstation/secure-workstation.png)

   A biztonságos munkaállomás egy olyan megerősített munkaállomást biztosít a rendszergazdának, amely egyértelmű Application Control és Application Guard rendszerű. A munkaállomás a hitelesítő adatokat, az Eszközkezelőt és a védelem kiaknázását használja a gazdagép rosszindulatú viselkedéstől való védeleméhez. Az összes helyi lemez is titkosítva van a BitLocker szolgáltatással.

* **Elkülönített** – ez az egyéni, offline forgatókönyv a spektrum szélsőséges végét jelöli. Ebben az esetben nincsenek telepítési parancsfájlok megadva. Előfordulhat, hogy olyan üzleti szempontból kritikus fontosságú függvényt kell kezelnie, amelyhez nem támogatott vagy nem javított örökölt operációs rendszer szükséges. Például egy nagy értékkel rendelkező gépsor vagy egy élettartam-támogató rendszer. Mivel a biztonság kritikus fontosságú, és a felhőalapú szolgáltatások nem érhetők el, ezeket a számítógépeket manuálisan vagy elszigetelt Active Directory erdőszintű architektúrával, például a fokozott biztonságú felügyeleti környezettel (ESAE) kezelheti és frissítheti. Ilyen esetekben érdemes lehet eltávolítani az összes hozzáférést, kivéve az alapszintű Intune-t és az ATP állapot-ellenőrzéseket.

  * [Intune hálózati kommunikációs követelmények](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [ATP hálózati kommunikációs követelmények](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Következő lépések

[Biztonságos Azure által felügyelt munkaállomás üzembe helyezése](howto-azure-managed-workstation.md).
