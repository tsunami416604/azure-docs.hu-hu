---
title: Beállítások és adatroaming GYIK |} A Microsoft Docs
description: Biztosít a rendszergazdák feltett kérdésekre adott válaszokat beállításairól és az alkalmazás data Sync szolgáltatással rendelkezhet.
services: active-directory
keywords: Vállalati állapot barangolási beállításokat, a windows-felhő, vállalati állapothordozás kapcsolatos gyakori kérdésekre
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.openlocfilehash: 9f3d171a8f0e353d5860f410a8c32149f8872338
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189388"
---
# <a name="settings-and-data-roaming-faq"></a>Beállítások és adatroaming GYIK
Ez a cikk rendszergazdák lehet beállításai és alkalmazás data Sync szolgáltatással kapcsolatos kérdésekre ad választ.

## <a name="what-data-roams"></a>Milyen adatokat barangol?
**Windows beállítások**: a számítógép-beállítások a Windows operációs rendszerbe beépített. Általában ezek a beállítások, amelyek a számítógép testreszabása, és a következő tág kategóriába tartoznak:

* *Téma*, amely magában foglalja a szolgáltatások, például asztali téma- és tálcán beállítások.
* *Az Internet Explorer beállításainak*, beleértve a legutóbb megnyitott lapokat és a Kedvencek közé.
* *A Microsoft Edge böngésző beállításai*, például a Kedvencek közé, és olvassa.
* *Jelszavak*, többek között internetes jelszavakat, a Wi-Fi profilok és mások.
* *Nyelvi beállítások*, amely tartalmazza a billentyűzetkiosztás, rendszer nyelve, dátum és idő és további beállításait.
* *Könnyű hozzáférés funkciók*, például a Narrátor, Nagyító és a kontrasztos téma.
* *Egyéb Windows-beállítások*, például az egér beállításait.

**Alkalmazásadatok**: Univerzális Windows-alkalmazások beállításainak adatokat írhatna központi mappába, és minden ebbe a mappába írt adatok automatikusan szinkronizálja. Fontos az egyéni alkalmazás fejlesztőjénél, ez a funkció kihasználásához alkalmazásokat. Egy univerzális Windows-alkalmazást, amely központi fejlesztésével kapcsolatos további részletekért lásd: a [appdata storage API-JÁNAK](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) és a [fejlesztői blog barangolás Windows 8 appdata](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Milyen fiókot szolgál szinkronizálási beállítások?
Windows 8.1, a szinkronizálási beállítások mindig használja a fogyasztói Microsoft-fiókok. Vállalati felhasználó számára lehetővé teszi egy Microsoft-fiók csatlakozni az Active Directory tartományi fiók a szinkronizálási beállítások eléréséhez rendelkezett. A Windows 10 csatlakozik egy elsődleges és másodlagos fiók keretrendszer helyére, a funkciók Microsoft-fiókjával.

Az elsődleges fióknak számít, ha a Windows való bejelentkezéshez használt fiók. Ez lehet egy Microsoft-fiókkal, egy Azure Active Directory (Azure AD-) fiók, a helyszíni Active Directory-fiókkal vagy egy helyi fiókot. Az elsődleges fiókon kívül Windows 10-es fiókokat is hozzáadhat egy vagy több másodlagos felhőbeli eszközére. Egy másodlagos fiókot alapvetően egy Microsoft-fiókkal, egy Azure AD-fiók vagy néhány egyéb fiók, mint például a Gmail vagy a Facebook. Másodlagos hozzáférést biztosít a további szolgáltatásokat, például az egyszeri bejelentkezést és a Windows Store, de azok nem képes a motorja olyan beállítások szinkronizálása.

A Windows 10, az eszköz csak az elsődleges fiók is használható beállítások szinkronizálása (lásd: [hogyan frissíthetem a Microsoft-Fiókbeállítások szinkronizálását a Windows 8 az Azure AD-beállítások szinkronizálása a Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Adatok soha nem vegyes között a különböző felhasználói fiókot az eszközön. Beállítások szinkronizálása két szabályok vonatkoznak:

* Windows-beállítások mindig az elsődleges fiókkal hordozhatók a lesz.
* Az alkalmazásadatok címkével fog rendelkezni az alkalmazás beszerzésére használt fiókot. Az elsődleges fióknak címkéjű csak alkalmazásokat szinkronizálja. Alkalmazás tulajdonjogának címkézése határozza meg, ha az alkalmazás az ügyféloldali betölti a Windows Store vagy a mobileszköz-felügyelet (MDM) keresztül.

Ha egy alkalmazás tulajdonosa nem azonosítható, azt fogja hordozhatók a elsődleges fiókkal. Ha egy eszköz Windows 10-re frissítve van a Windows 8 vagy Windows 8.1, minden alkalmazás címkével fog rendelkezni, a Microsoft-fiók segítségével. Ez azért, mert a legtöbb felhasználó keresztül a Windows Store apps beszerezni, és hiba történt az Azure AD-felhasználók előtt a Windows 10-es nem Windows Store támogatása. Ha egy alkalmazást offline licenccel keresztül van telepítve, az alkalmazás címkével fog rendelkezni az elsődleges fiókkal az eszközön.

> [!NOTE]
> Vállalati által birtokolt és az Azure AD-csatlakoztatott Windows 10-es eszközök már nem kapcsolódhat a Microsoft-fiókok egy tartományi fiókot. Microsoft-fiókkal csatlakozik egy tartományi fiókot, és rendelkezik az összes felhasználói data sync számára a Microsoft-fiók (azt jelenti, a Microsoft-fiók az összekapcsolt Microsoft-fiók és az Active Directory működési roaming) lehetővé teszi a Windows 10-es eltávolítása egy csatlakoztatott Active Directory vagy az Azure AD-környezet csatlakozó eszközökről.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hogyan frissíthetem a Microsoft-Fiókbeállítások szinkronizálását a Windows 8 az Azure AD-beállítások szinkronizálása a Windows 10-ben?
Ha tartományhoz csatlakozik az Active Directory-tartománynak Windows 8.1 rendszerű összekapcsolt Microsoft-fiókkal, akkor beállítások a Microsoft-fiókján keresztül szinkronizálódnak. Windows 10-re a frissítés után továbbra is keresztül a Microsoft-fiók felhasználói beállítások szinkronizálása, mindaddig, amíg egy tartományhoz csatlakozó felhasználók és az Active Directory-tartomány nem csatlakozik az Azure ad-ben.

Ha a helyszíni Active Directory-tartomány az Azure AD connect, az eszköz megkísérli beállítások segítségével a csatlakoztatott szinkronizálása az Azure AD-fiókot. Ha az Azure AD-rendszergazda nem engedélyezi a vállalati Állapothordozás, a csatlakoztatott Azure AD-fiókot a beállítások szinkronizálása leáll. Ha a Windows 10-felhasználók és jelentkezik be egy Azure AD identity, hozzákezdhet windows-beállítások szinkronizálása, amint a rendszergazda engedélyezi a beállítások szinkronizálása az Azure AD-n keresztül.

Ha személyes adatok tárolása a céges eszközét a történik, érdemes figyelembe, hogy Windows operációs rendszer és az alkalmazásadatok megkezdődik az Azure AD szinkronizálása. Ez a következőkkel jár:

* A személyes Microsoft-Fiókbeállítások konfigurációsodródás szereplőkkel a beállításokat a munkahelyi vagy iskolai fiókok Azure ad-ben lesz. Ennek oka, hogy a Microsoft-fiók és az Azure AD-beállítások szinkronizálása. most már külön fiókokat használnak.
* Személyes adatok, például a WiFi-jelszavakat, webes hitelesítő adatok és az Internet Explorer Kedvencek összekapcsolt Microsoft-fiók használatával korábban szinkronizált Azure AD-n keresztül lesznek szinkronizálva.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Microsoft-fiók és az Azure AD vállalati állapot Roaming együttműködési munkahelyi hogyan?
Windows 10-es November, 2015 vagy újabb kiadásaiban Enterprise State Roaming csak támogatott egyetlen fiókkal egy időben. Jelentkezzen be a Windows egy munkahelyi vagy iskolai fiókkal az Azure AD, ha az összes adat szinkronizálja az Azure AD-n keresztül. Ha bejelentkezik a Windows egy személyes Microsoft-fiók használatával, a minden adatot a Microsoft-fiók útján szinkronizálódnak. Univerzális appdata fog hordozhatók a fiókkal csak az elsődleges jelentkezzen be az eszközön, és azt fogja hordozhatók a csak akkor, ha az elsődleges fióknak a licenc az alkalmazás tulajdonosa. Az alkalmazások bármely másodlagos fiókokat tulajdonában univerzális appdata nem lesznek szinkronizálva.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Több bérlő Azure AD-fiókokat beállításokat is szinkronizálja?
Ha több Azure AD-fiókok a különböző Azure AD-bérlőt ugyanazon az eszközön, frissítenie kell az eszköz beállításjegyzék minden egyes Azure AD-bérlőhöz tartozó Azure Rights Management szolgáltatással való kommunikációra.  

1. A GUID azonosító található minden egyes Azure AD-bérlővel. Nyissa meg az Azure Portalon, és válassza ki az Azure AD-bérlő. A Tulajdonságok lapon a kiválasztott bérlő van a bérlőhöz tartozó GUID (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), címkézett **címtár-azonosító**. 
2. Miután a globálisan egyedi Azonosítót, szüksége lesz a beállításkulcs hozzáadása **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<bérlői azonosító GUID >**.
   Az a **bérlői azonosító GUID** kulcsban, hozzon létre egy új karakterláncsoros értéket (REG-MULTI-SZ) **AllowedRMSServerUrls**. Adja meg az adatokat, a licencelési terjesztési pont URL-címek az eszköz hozzáfér a többi Azure bérlők számától.
3. Annak a terjesztési pont licencelési URL-címeket futtatásával a **Get-AadrmConfiguration** parancsmag az AADRM modulban. Ha a tartozó értékeket a **LicensingIntranetDistributionPointUrl** és **LicensingExtranetDistributionPointUrl** eltérőek, mindkét értéket adja meg. Ha az értékek azonosak, csak egyszer adja meg az értéket.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Mik azok a meglévő Windows asztali alkalmazások központi beállítások lehetőségei?
Csak az univerzális Windows-alkalmazások központi működik. Két lehetőség van egy meglévő Windows asztali alkalmazás roaming engedélyezése érhető el:

* A [asztali híd](https://aka.ms/desktopbridge) segítséget nyújt a meglévő Windows asztali alkalmazások használata az univerzális Windows platform. Itt minimális kódmódosítással lesz szükség az Azure AD alkalmazásadat-barangolás előnyeinek kihasználása érdekében. A Desktop Bridge alkalmazás identitást, amely engedélyezéséhez alkalmazásadat-barangolás meglévő asztali alkalmazások esetén van szükség az alkalmazások biztosít.
* [Felhasználói élmény Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) segítséget nyújt egy meglévő Windows asztali alkalmazások egyéni beállítások sablon létrehozása és engedélyezése barangolás Win32-alkalmazások esetén. Ezt a beállítást nem szükséges az alkalmazás fejlesztőjénél, az alkalmazás kódjának módosítása. UE-V a helyszíni Active Directory barangolás esetén ügyfelek, akik a Microsoft Desktop Optimization Pack korlátozódik.

A rendszergazdák konfigurálhatják UE-V számára a Windows asztali alkalmazás adatokat úgy módosítja a Windows operációs rendszer beállításait és univerzális alkalmazások adatainak keresztül, barangolás [UE-V csoportházirendek](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), többek között:

* Hordozhatók a Windows beállításait a csoportházirend
* Ne szinkronizáljon a Windows-alkalmazások csoportházirend
* Az Internet Explorer, az alkalmazások szakaszban barangolás

A jövőben a Microsoft előfordulhat, hogy vizsgálja meg, győződjön meg arról, UE-V Windows mélyen integrált és kiterjesztése UE-V számára a beállítások az Azure AD-felhőn keresztül.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Tárolhatok szinkronizált beállítás és adat a helyszínen?
A Microsoft cloud Enterprise State Roaming az összes szinkronizált adatot tárol. UE-V kínál a helyszíni központi megoldás.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kié az adatokat, amelyek a forrásul van folyamatban?
A saját vállalatok számára az adatok forrásul Enterprise State Roaming-n keresztül. Az Azure-adatközpontban tárolt adatokat. Összes felhasználói adatot van titkosítva, mind az átvitel során, míg az inaktív, a felhőben az Azure Information Protection az Azure Rights Management szolgáltatás használatával. Ez az, hogy javulást Microsoft tárfiókalapú beállítások szinkronizálása, amely csak bizonyos felhasználói hitelesítő adatokat például bizalmas adatokat titkosítja, mielőtt az eszköz kikerül képest.

A Microsoft elkötelezett a vásárlói adatok védelmére. Mielőtt elhagyja a Windows 10 rendszerű eszköz, hogy más felhasználó nem tudja olvasni ezeket az adatokat egy vállalati felhasználó beállítások adatokat automatikusan titkosítja az Azure Rights Management szolgáltatás által. Ha a szervezet rendelkezik az Azure Rights Management szolgáltatás fizetős, is egyéb adatvédelmi funkciói, például nyomon követheti és visszavonhatja a dokumentumot, automatikusan bizalmas információkat tartalmazó e-mailek védelme, és kézben tarthatja saját kulcsait (a "használata a saját kulcs"megoldás, más néven BYOK). Ezek a funkciók és a védelmi szolgáltatás működésével kapcsolatos további információkért lásd: [Mi az Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kezelheti egy adott alkalmazás vagy beállítás-szinkronizálás?
A Windows 10-es nem tartozik MDM vagy a csoportházirend beállítás letiltása barangolás esetén az egyes alkalmazásokhoz. A bérlői rendszergazdák letilthatják appdata szinkronizálása a felügyelt eszközök összes alkalmazáshoz, de nem lehet finomabb szabályozásra alkalmazásonkénti vagy alkalmazáson belül szinten van.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hogyan tudok engedélyezheti vagy letilthatja a központi?
Az a **beállítások** alkalmazást, lépjen a **fiókok** > **beállítások szinkronizálása**. Ezen a lapon láthatja, melyik fiókot használja a beállítások hordozhatók, és engedélyezheti vagy letilthatja az egyes csoportok beállításokat kell lennie a forrásul.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Mi a Microsoft azt javasolja, a Windows 10 roaming engedélyezéséhez?
A Microsoft érhető el, beleértve a központi felhasználói profilok, a UE-V és a vállalati állapot központi megoldások barangolás néhány különböző beállításokkal rendelkezik.  A Microsoft elkötelezett az állapot, a jövőbeli verzióiban a Windows központi vállalat befektetés. Ha a szervezet nem áll készen áll, vagy okoz gondot az adatok áthelyezése a felhőbe, majd azt javasoljuk, hogy a központi elsődleges technológiaként UE-V használata. Ha a szervezet megköveteli a meglévő Windows asztali alkalmazások támogatása a központi, de különösen a felhőbe, azt javasoljuk, vállalati állapot központi és UE-V használatát. Bár a UE-V- és Enterprise State Roaming nagyon hasonló technológiákat, azok nem kölcsönösen kizárják egymást. Kiegészíti egymást annak biztosítására, hogy a szervezet a barangoló szolgáltatást nyújt, amelyek a felhasználóknak kell.  

Enterprise State Roaming és UE-V használata esetén a következő szabályok érvényesek:

* Enterprise State Roaming az elsődleges központi ügynök az eszközön. UE-V használják, hogy kiegészíti a "Win32 térköz."
* UE-V központi Windows-beállításait és a modern UWP-alkalmazás adatait, amikor a UE-V csoporttal szabályzatok le kell tiltani. Ezek már tárgyalt Enterprise State Roaming szerint.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hogyan Enterprise State Roaming támogatja a virtuális asztali infrastruktúra (VDI)?
Enterprise State Roaming a Windows 10-es ügyfél SKU-k, de nem található a kiszolgálón termékváltozatok használata támogatott. Ha egy ügyfél VM hipervizor gépen üzemel, és távolról jelentkeznek be a virtuális gép, az adatok hordozhatók a rendszer. Ha több felhasználó megosztva az ugyanazon operációs rendszer és a felhasználók távolról jelentkeznek be a kiszolgálónak a teljes asztali élmény, barangolás előfordulhat, hogy nem működik. Az utóbbi munkamenet-alapú forgatókönyv hivatalosan nem támogatott.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Mi történik, ha a szervezet megvásárolja egy előfizetést, amely tartalmazza az Azure Rights Management központi használata után?
Ha a szervezet már használja az Azure Rights Management korlátozott használatú ingyenes előfizetés a Windows 10 roaming, megvásárlásának egy [előfizetéssel](https://azure.microsoft.com/pricing/details/information-protection/) , amely tartalmazza az Azure Rights Management adatvédelmi szolgáltatást nem fog a rendszergazda által a barangoló szolgáltatás funkciói hatást, és a konfigurációs módosítások nélküli lesz szükség.

## <a name="known-issues"></a>Ismert problémák
Tekintse át a dokumentációt a [hibaelhárítási](enterprise-state-roaming-troubleshooting.md) szakaszban található ismert problémák listáját. 

## <a name="next-steps"></a>További lépések 

Áttekintéséhez lásd: [vállalati állapothordozás áttekintése](enterprise-state-roaming-overview.md)
