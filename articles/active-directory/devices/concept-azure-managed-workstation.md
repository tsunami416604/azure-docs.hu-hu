---
title: A biztonságos, Azure által felügyelt munkaállomások ismertetése – Azure Active Directory
description: Ismerje meg a biztonságos, Azure által felügyelt munkaállomásokat, és ismerje meg, miért fontosak.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672663"
---
# <a name="understand-secure-azure-managed-workstations"></a>A biztonságos, Azure által felügyelt munkaállomások ismertetése

A biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatáskezelők biztonsága szempontjából. Ha az ügyfél munkaállomás-biztonsága sérül, számos biztonsági vezérlő és garancia sikertelen vagy hatástalan lehet.

Ez a dokumentum ismerteti, mire van szüksége egy biztonságos munkaállomás, gyakran nevezik a kiemelt hozzáférésű munkaállomás (PAW). A cikk részletes utasításokat is tartalmaz a kezdeti biztonsági ellenőrzések beállításához. Ez az útmutató bemutatja, hogy a felhőalapú technológia hogyan kezelheti a szolgáltatást. A Windows 10RS5, a Microsoft Defender Advanced Threat Protection (ATP), az Azure Active Directory és a Microsoft Intune által bevezetett biztonsági képességekre támaszkodik.

> [!NOTE]
> Ez a cikk ismerteti a biztonságos munkaállomás fogalmát és fontosságát. Ha már ismeri a koncepciót, és szeretné átugrani a telepítést, látogasson el [a Biztonságos munkaállomás telepítése](howto-azure-managed-workstation.md)elemre.

## <a name="why-secure-workstation-access-is-important"></a>Miért fontos a munkaállomás-hozzáférés biztonságos elérése?

A felhőszolgáltatások gyors bevezetése és a bárhonnan történő munkavégzés képessége új kitermelési módszert hozott létre. A rendszergazdák munkáját használó eszközök gyenge biztonsági vezérlőinek kihasználásával a támadók hozzáférhetnek a kiemelt erőforrásokhoz.

A kiemelt visszaélési és ellátásilánc-támadások az első öt módszer közé tartoznak, amelyet a támadók a szervezetek megsértésére használnak. A [Verizon Threat jelentése](https://enterprise.verizon.com/resources/reports/dbir/)és a Biztonsági Hírszerző [Jelentés](https://aka.ms/sir)szerint ők a második leggyakrabban észlelt taktikák a 2018-ban bejelentett incidensekben .

A legtöbb támadó az alábbi lépéseket követi:

1. Felderítés, hogy megtalálja a módját, gyakran jellemző az ipar.
1. Elemzés az adatok gyűjtésére és az alacsony értékűnek tartott munkaállomások beszivárgásának legjobb módjának meghatározására.
1. Kitartás, hogy keressen egy eszközt, hogy mozog [oldalirányban](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Bizalmas és érzékeny adatok kiszivárgása.

A felderítés során a támadók gyakran beszivárognak az alacsony kockázatúnak vagy alulértékeltnek tűnő eszközökbe. Ezeket a sebezhető eszközöket használják az oldalirányú mozgás lehetőségének megtalálására, valamint a felügyeleti felhasználók és eszközök megtalálására. Miután hozzáférnek a kiemelt felhasználói szerepkörökhöz, a támadók azonosítják a nagy értékű adatokat, és sikeresen kiszivárognak az adatokból.

![Tipikus kompromisszumos minta](./media/concept-azure-managed-workstation/typical-timeline.png)

Ez a dokumentum egy olyan megoldást ismertet, amely segít megvédeni a számítástechnikai eszközöket az ilyen oldalirányú támadásoktól. A megoldás elkülöníti a felügyeleti és a szolgáltatások kevésbé értékes hatékonyságnövelő eszközök, megtörve a láncot, mielőtt az eszköz, amely hozzáfér a bizalmas felhőalapú erőforrásokat lehet beszivárogni. A megoldás a Microsoft 365 Enterprise verem részét használó natív Azure-szolgáltatásokat használja:

* Intune eszközkezeléshez, valamint az alkalmazások és URL-címek biztonságos listájához
* Autopilot az eszköz beállításához, telepítéséhez és frissítéséhez
* Azure AD a felhasználókezeléshez, a feltételes hozzáféréshez és a többtényezős hitelesítéshez
* Windows 10 (jelenlegi verzió) az eszköz állapotigazolásához és felhasználói élményéhez
* Defender ATP a felhővel felügyelt végpontok védelméhez, észleléséhez és válaszához
* Azure AD PIM az engedélyezés és a just-in-time (JIT) kiemelt hozzáférésének kezeléséhez az erőforrásokhoz
* Log Analytics és Sentinel a figyeléshez és riasztáshoz

## <a name="who-benefits-from-a-secure-workstation"></a>Ki élvezi a biztonságos munkaállomás előnyeit?

A biztonságos munkaállomások használata érdekében minden felhasználó és operátor számára előnyös. A számítógépet vagy eszközt veszélyeztető támadó megszemélyesítheti az összes gyorsítótárazott fiókot. Amikor bejelentkezett az eszközre, hitelesítő adatokat és jogkivonatokat is használhatnak. Ez a kockázat fontossá teszi a kiemelt szerepkörökhöz használt eszközök, beleértve a felügyeleti jogokat is, biztonságossá tétele. A kiemelt jogosultságú fiókkal rendelkező eszközök az oldalirányú mozgás és a jogosultságeszkalációs támadások célpontjai. Ezek a számlák számos eszközhöz használhatók, mint például:

* Helyszíni vagy felhőalapú rendszerek rendszergazdája
* Fejlesztői munkaállomás kritikus rendszerekhez
* Közösségi média fiók adminisztrátora nagy expozícióval
* Rendkívül érzékeny munkaállomás, például SWIFT fizetési terminál
* Üzleti titkokat kezelő munkaállomás

A kockázatok csökkentése érdekében emelt szintű biztonsági vezérlőket kell alkalmaznia a kiemelt munkaállomásokhoz, amelyek ezeket a fiókokat használják. További információt az [Azure Active Directory szolgáltatástelepítési útmutatójában](../fundamentals/active-directory-deployment-checklist-p2.md), az [Office 365 ütemtervben](https://aka.ms/o365secroadmap)és a [Kiemelt hozzáférésű hozzáférés biztonsági tervén](https://aka.ms/sparoadmap)talál.

## <a name="why-use-dedicated-workstations"></a>Miért érdemes dedikált munkaállomásokat használni?

Bár lehetséges egy meglévő eszköz biztonságának hozzáadása, jobb, ha egy biztonságos alappal kezd. Ahhoz, hogy a szervezet a legjobb helyzetben, hogy fenntartsák a magas biztonsági szintet, kezdje egy eszköz tudja, hogy biztonságos, és végre egy sor ismert biztonsági vezérlők.

Az e-mailben és webböngészésen keresztül imitopontként történő támadások egyre növekvő száma miatt egyre nehezebb biztossá tenni egy eszköz megbízhatóságát. Ez az útmutató feltételezi, hogy egy dedikált munkaállomás el van különítve a szabványos hatékonyságtól, böngészéstől és e-mailektől. A termelékenység, a webböngészés és az e-mailek eszközről való eltávolítása negatív hatással lehet a termelékenységre. Ez a védővédelem azonban általában elfogadható olyan esetekben, amikor a feladatfeladatok nem kifejezetten megkövetelik, és a biztonsági esemény kockázata magas.

> [!NOTE]
> Webböngészés itt utal az általános hozzáférést az önkényes weboldalak, amelyek magas kockázatú tevékenység. Az ilyen böngészés egyértelműen különbözik awebböngésző használatától, hogy hozzáférjen néhány jól ismert felügyeleti webhelyhez olyan szolgáltatásokhoz, mint az Azure, az Office 365, más felhőszolgáltatók és SaaS-alkalmazások.

Az elszigetelési stratégiák szigorítják a biztonságot azáltal, hogy növelik azolyan vezérlők számát és típusát, amelyek elrettentik a támadót a bizalmas eszközökhöz való hozzáféréstől. A cikkben ismertetett modell rétegzett jogosultsági tervet használ, és a rendszergazdai jogosultságokat adott eszközökre korlátozza.

## <a name="supply-chain-management"></a>Ellátásilánc-menedzsment

A biztonságos munkaállomáshoz nélkülözhetetlen ellátásilánc-megoldás, ahol a "megbízhatóság gyökerének" nevezett megbízható munkaállomást használja. A megbízható hardver gyökerének kiválasztásakor figyelembe veendő technológiának a következő technológiákat kell tartalmaznia a modern laptopokban: 

* [Platformmegbízhatósági modul (TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker meghajtótitkosítás](/windows-hardware/design/device-experiences/oem-bitlocker)
* [UEFI biztonságos rendszerindítás](/windows-hardware/design/device-experiences/oem-secure-boot)
* [A Windows Update szolgáltatáson keresztül terjesztett illesztőprogramok és belső vezérlőprogram](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualizáció és HVCI engedélyezve](/windows-hardware/design/device-experiences/oem-vbs)
* [Illesztőprogramok és alkalmazások HVCI-Ready](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [DMA I/O védelem](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Rendszer védő](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Modern készenlét](/windows-hardware/design/device-experiences/modern-standby)

Ehhez a megoldáshoz a megbízhatósági alap gyökerét a [Microsoft Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) technológia olyan hardverrel telepíti, amely megfelel a modern műszaki követelményeknek. A munkaállomások biztonságossá tétele érdekében az Autopilot lehetővé teszi a Microsoft OEM-re optimalizált Windows 10-eszközök kihasználását. Ezek az eszközök a gyártó ismert jó állapotban vannak. Ahelyett, hogy egy potenciálisan nem biztonságos eszközt reimaging, Autopilot képes átalakítani a Windows-eszköz egy "üzleti kész" állapotban. Alkalmazza a beállításokat és szabályzatokat, telepíti az alkalmazásokat, és még a Windows 10 kiadását is módosítja. Az Autopilot például megváltoztathatja az eszköz Windows-telepítését Windows 10 Pro-ról Windows 10 Enterprise rendszerre, hogy a speciális funkciókat használhassa.

![Biztonságos munkaállomás-szintek](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Eszközszerepkörök és -profilok

Ez az útmutató több biztonsági profilra és szerepkörre hivatkozik, amelyek segítségével biztonságosabb megoldásokat hozhat létre a felhasználók, fejlesztők és informatikai személyzet számára. Ezek a profilok egyensúlyt teremtenek a használhatóság és a kockázatok között a továbbfejlesztett vagy biztonságos munkaállomás előnyeit élvező gyakori felhasználók számára. Az itt megadott beállítási konfigurációk az iparág által elfogadott szabványokon alapulnak. Ez az útmutató bemutatja, hogyan keményítse meg a Windows 10-et, és csökkentse az eszköz vagy a felhasználó sérülésével kapcsolatos kockázatokat. A modern hardvertechnológia és a megbízhatósági eszköz gyökerének kihasználásához a [Device Health Attestation eszközt használjuk,](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643)amely a Magas **biztonsági** profiltól kezdve engedélyezve van. Ez a képesség biztosítja, hogy a támadók nem lehetnek állandóak az eszköz korai indításakor. Ezt a biztonsági funkciók és kockázatok kezelésére szolgáló házirend és technológia segítségével teszi.
![Biztonságos munkaállomás-szintek](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Basic Security** - A felügyelt, szabványos munkaállomás jó kiindulópont a legtöbb otthoni és kisvállalati használatra. Ezek az eszközök regisztrálva vannak az Azure AD-ben, és az Intune-nal vannak kezelve. Ez a profil lehetővé teszi a felhasználók számára, hogy bármilyen alkalmazást futtassanak, és bármely webhelyet keressenek. Engedélyezni kell egy olyan kártevőirtó megoldást, mint a [Microsoft Defender.](https://www.microsoft.com/windows/comprehensive-security)

* **Fokozott biztonság** – Ez a belépő szintű, védett megoldás jó az otthoni felhasználók, a kisvállalkozások és az általános fejlesztők számára.

   A továbbfejlesztett munkaállomás az alacsony biztonsági profil biztonságának növelésére szolgáló, házirendalapú módszer. Biztonságos eszközt biztosít az ügyféladatokkal való munkához, miközben olyan hatékonyságnövelő eszközöket is használ, mint az e-mail és a webböngészés. A naplózási házirendek és az Intune segítségével figyelheti a fokozott munkaállomás a felhasználói viselkedés és a profil használat. A továbbfejlesztett munkaállomás-profilt a Windows10 (1809) parancsfájllal telepíti, és a speciális kártevővédelem előnyeit használja [a Komplex veszélyforrások elleni védelem (ADVANCED Threat Protection, ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)használatával.

* **Magas biztonság** – A munkaállomás támadási felületének csökkentésére a leghatékonyabb eszköz a munkaállomás önfelügyeletének megszüntetése. A helyi rendszergazdai jogok eltávolítása olyan lépés, amely javítja a biztonságot, de hatással lehet a termelékenységre, ha helytelenül hajtják végre. A magas biztonsági profil a továbbfejlesztett biztonsági profilra épül, egyetlen jelentős változtatással: a helyi rendszergazda eltávolításával. Ez a profil nagy horderejű felhasználók számára készült: vezetők, bérszámfejtési és bizalmas adatfelhasználók, szolgáltatások és folyamatok jóváhagyói számára.

   A magas biztonságú felhasználó egy ellenőrzöttebb környezetet igényel, miközben továbbra is képes olyan tevékenységeket végezni, mint az e-mail és a webböngészés egy egyszerű használatélményben. A felhasználók olyan funkciókat várnak, mint a cookie-k, a kedvencek és más parancsikonok. Előfordulhat azonban, hogy ezeka a felhasználók nem igénylik eszközük módosítását vagy hibakeresését. Nem kell illesztőprogramok telepítését is. A magas biztonsági szintű profil a Magas biztonsági szintű - Windows10 (1809) parancsfájl használatával van telepítve.

* **Speciális** – A támadók azért célozzák meg a fejlesztőket és a rendszergazdákat, mert megváltoztathatják a támadók számára fontos rendszereket. A speciális munkaállomás a helyi alkalmazások kezelésével és a webhelyek korlátozásával bővíti a magas biztonsági szintű munkaállomás irányelveit. Emellett korlátozza a magas kockázatú hatékonyságnövelő képességeket, például az ActiveX-et, a Java-t, a böngészőbővítményeket és más Windows-vezérlőket. Ezt a profilt a DeviceConfiguration_NCSC – Windows10 (1803) SecurityBaseline parancsfájllal telepíti.

* **Biztonságos** – A felügyeleti fiókot veszélyeztető támadó jelentős üzleti károkat okozhat adatlopás, adatmódosítás vagy szolgáltatáskimaradás miatt. Ebben a megerősített állapotban a munkaállomás lehetővé teszi az összes olyan biztonsági vezérlőt és házirendet, amely korlátozza a helyi alkalmazáskezelés közvetlen vezérlését. A biztonságos munkaállomások nem rendelkeznek hatékonyságnövelő eszközökkel, így az eszköz nehezebben sérülhet. Blokkolja az adathalász támadások leggyakoribb vektorát: az e-maileket és a közösségi médiát. A biztonságos munkaállomás a Secure Workstation - Windows10 (1809) SecurityBaseline parancsfájllal telepíthető.

   ![Biztonságos munkaállomás](./media/concept-azure-managed-workstation/secure-workstation.png)

   A biztonságos munkaállomás megerősített munkaállomást biztosít a rendszergazdának, amely tiszta alkalmazásvezérléssel és alkalmazásvédővel rendelkezik. A munkaállomás hitelesítő adatok at, eszközőrt és exploit őrt használ a gazdagép rosszindulatú viselkedéssel szembeni védelmére. Az összes helyi lemez a BitLocker titkosításával is titkosítva van.

* **Elkülönített** – Ez az egyéni, offline forgatókönyv a spektrum legvégső végét jelöli. Ehhez az esethez nincs telepítési parancsfájl. Előfordulhat, hogy olyan üzleti legkritikusabb függvényt kell kezelnie, amely nem támogatott vagy nem javított örökölt operációs rendszert igényel. Például egy nagy értékű gyártósor vagy egy létfenntartó rendszer. Mivel a biztonság kritikus fontosságú, és a felhőszolgáltatások nem érhetők el, ezeket a számítógépeket manuálisan vagy egy elszigetelt Active Directory erdőarchitektúrával, például a fokozott biztonsági rendszergazdai környezettel (ESAE) kezelheti és frissítheti. Ilyen körülmények között fontolja meg az összes hozzáférés eltávolítását, kivéve az alapvető Intune és az ATP állapot-ellenőrzések.

   * [Az Intune hálózati kommunikációs követelménye](/intune/network-bandwidth-use)
   * [ATP hálózati kommunikációs követelmény](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>További lépések

[Biztonságos Azure által felügyelt munkaállomás üzembe helyezése.](howto-azure-managed-workstation.md)
