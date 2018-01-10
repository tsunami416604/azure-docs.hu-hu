---
title: "Beállítások és adatok hordozása – gyakori kérdések |} Microsoft Docs"
description: "Biztosítja a rendszergazdák feltett kérdésekre adott válaszok beállításait, valamint az alkalmazás adatszinkronizálás rendelkezhet."
services: active-directory
keywords: "Vállalati állapot barangolási beállításokat, a windows-felhő, gyakran ismételt kérdések a vállalati állapothordozás"
documentationcenter: 
author: tanning
manager: mtillman
editor: curtand
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: markvi
ms.openlocfilehash: 054705e802867fda666c80217396db197c60f50e
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="settings-and-data-roaming-faq"></a>Beállítások és adatroaming GYIK
Ez a témakör néhány rendszergazdák rendelkezhet beállításait, valamint az alkalmazás adatszinkronizálás kérdésekre ad választ.

## <a name="what-data-roams"></a>Milyen adatok barangol?
**Windows-beállítások**: a számítógép beállításait, a Windows operációs rendszer beépített. Általában ezek a beállítások a számítógép testreszabása, és tartalmazzák a következő kategóriába sorolhatók:

* *Téma*, mely funkciók, például asztali téma és a tálca beállításait tartalmazza.
* *Az Internet Explorer beállításainak*, beleértve a legutóbb megnyitott lapokat és a Kedvencekhez.
* *A szegély böngészőbeállítások*, például a Kedvencek és olvasási listáját.
* *Jelszavak*, beleértve a Internet jelszavakat, a Wi-Fi profilok és mások számára.
* *Nyelvi beállítások*, amely tartalmazza a billentyűzetkiosztások, rendszer nyelve, dátum és idő, és további beállításait.
* *Könnyű hozzáférés funkcióját*, például Nagyító, kontrasztos téma vagy Narrátor.
* *Egyéb Windows-beállítások*, például a parancssor beállításai és alkalmazásainak listáját.

**Alkalmazásadatok**: univerzális Windows-alkalmazások beállításainak adatokat írhatna központi mappába, és bármely ebbe a mappába írt adatok automatikusan lesznek szinkronizálva. Már az egyedi alkalmazás fejlesztőjének tervezhet egy alkalmazást, hogy ez a funkció előnyeit. Központi használó univerzális Windows-alkalmazások fejlesztésével kapcsolatos további részletekért lásd: a [appdata tárolási API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) és a [Windows 8 appdata fejlesztői blogja központi](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Milyen fiókot szolgál szinkronizálási beállítások?
A Windows 8 és Windows 8.1 a szinkronizálási beállítások mindig használt végfelhasználói Microsoft-fiókok. Vállalati felhasználók kellett a kapcsolódhat Microsoft-fiókot az Active Directory tartományi fiók szinkronizálási beállítások eléréséhez. A Windows 10 Ez egy elsődleges és másodlagos fiók keretrendszer helyére, a funkciók Microsoft-fiók csatlakoztatva.

Az elsődleges definiálva van a Windows való bejelentkezéshez használt fiók. Ez lehet egy Microsoft-fiókkal, egy Azure Active Directory (Azure AD-) fiók, a helyszíni Active Directory-fiókkal vagy egy helyi fiókot. Az elsődleges fiók mellett Windows 10-felhasználók számára fiókokat is hozzáadhat egy vagy több másodlagos felhő az eszközükre. Egy másodlagos számítógépfiókja általában egy Microsoft-fiókkal, az Azure AD-fiókot vagy néhány más fiók, például a Gmailes vagy a Facebook-on. Ezeket a másodlagos fiókokat adja meg a további szolgáltatások, például egyszeri bejelentkezés és a Windows áruházhoz való hozzáférés, de azok nem képes a szinkronizálási beállítások működtetéséhez.

A Windows 10, az eszköz csak az elsődleges fiók is használható beállítások szinkronizáláshoz (lásd: [hogyan lehet frissíteni a Microsoft fiók beállítások szinkronizálása a Windows 8-ban az Azure AD szinkronizálása a Windows 10-es?](active-directory-windows-enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Adatok soha nem vegyes között a különböző felhasználói fiókot az eszközön. Beállítások szinkronizálása két szabályok vonatkoznak:

* A Windows rendszer mindig hordozhatók elsődleges fiókkal.
* Az alkalmazásadatok az alkalmazás használt fiókkal címkével fog rendelkezni. Csak az elsődleges fiók címkéjű alkalmazásokat szinkronizálja. Alkalmazás tulajdonjoga címkézés határozza meg, a Windows áruház vagy a mobileszköz-kezelés (MDM) keresztül közvetlenül telepített alkalmazások esetén.

Ha az alkalmazás tulajdonosa nem azonosítható, azt fogja barangolás elsődleges fiókkal. Ha egy eszközt a Windows 8 vagy Windows 8.1 és Windows 10 frissítve van, az alkalmazások címkével fog rendelkezni, a Microsoft-fiók használatával. Ennek az oka, hogy a legtöbb felhasználó programon keresztül a Windows Áruházbeli alkalmazásokat, és nincs Windows Store támogatása Windows 10 előtti Azure AD-fiókok történt. Ha egy alkalmazás telepítve van egy kapcsolat nélküli licenc keresztül, az alkalmazás címkével fog rendelkezni az elsődleges fiókkal az eszközön.

> [!NOTE]
> Vállalati által birtokolt és az Azure ad Szolgáltatásba csatlakoztatott Windows 10-es eszközök már nem csatlakozhat a Microsoft-fiókok egy olyan tartományi fiók. Csatlakozás egy Microsoft-fiók egy olyan tartományi fiók, és rendelkezik a felhasználói adatok szinkronizálása a Microsoft-fiókjába (Ez azt jelenti, hogy a Microsoft-fiók keresztül az összekapcsolt Microsoft-fiókkal és az Active Directory központi) törlődik a csatlakoztatott Active Directory vagy az Azure AD-környezet tartományhoz csatlakoztatott Windows 10-eszközökre.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hogyan lehet frissíteni a Microsoft fiók beállítások szinkronizálása a Windows 8-ban az Azure AD szinkronizálása a Windows 10-es?
Ha tartományhoz csatlakozik az Active Directory-tartomány, Windows 8 vagy Windows 8.1 fut összekapcsolt Microsoft-fiókkal, a Microsoft-fiókján keresztül fogja szinkronizálni az beállításait. Windows 10-re a frissítés után folytatja szinkronizálják a felhasználó beállításait a Microsoft-fiók segítségével, mindaddig, amíg egy tartományhoz csatlakozó felhasználók és az Active Directory-tartomány nem az Azure AD connect.

Ha a helyszíni Active Directory-tartomány és az Azure AD connect, az eszköz a beállításokat a csatlakoztatott a szinkronizálás megkísérléséhez Azure AD-fiókot. Ha az Azure AD-rendszergazda nem engedélyezi a vállalati Állapothordozás, a csatlakoztatott Azure AD-fiókot a beállítások szinkronizálása leáll. Ha a Windows 10-felhasználók és jelentkezik be egy Azure AD identity, hozzákezdhet szinkronizálása a windows-beállítások, amint a rendszergazda engedélyezi a beállítások szinkronizálása az Azure AD használatával.

Ha a vállalati eszköz személyes adatokat tárolja, vegye figyelembe, hogy a Windows operációs rendszer és az alkalmazásadatok megkezdődik a szinkronizálás az Azure AD kell lennie. Azt a következő szempontokat:

* A személyes Microsoft-fiók beállításainak eltolódás a beállítások mellett a a munkahelyi vagy iskolai fiókok Azure AD lesz. Ennek oka, hogy a Microsoft-fiókkal és az Azure AD-beállításokat a szinkronizálás most már külön fiókot használ.
* Személyes adatok, például Wi-Fi jelszavakat, a webes hitelesítő adatok és az Internet Explorer Kedvencek az előzőleg szinkronizált keresztül összekapcsolt Microsoft-fiókkal az Azure AD keresztül lesznek szinkronizálva.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Honnan, Microsoft-fiókkal és az Azure AD vállalati állapot központi együttműködés?
Windows 10 November 2015-ös vagy újabb kiadásaiban a vállalati Állapothordozás csak támogatott ugyanazt a fiókot egy időben. Ha Windows jelentkezzen be a munkahelyi vagy iskolai fiókkal az Azure AD, minden adatot az Azure AD útján szinkronizálódnak. Ha bejelentkezik a Windows egy személyes Microsoft-fiókkal, minden adatot a Microsoft-fiók útján szinkronizálódnak. Univerzális appdata barangolás-e a használata csak az elsődleges bejelentkezési fiókot az eszközön, és ez csak akkor, ha az alkalmazás licencének elsődleges fiók tulajdonosa lesz barangolás. Az alkalmazások bármely másodlagos fiókokat tulajdonában univerzális appdata nem lesznek szinkronizálva.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Azure AD-fiókok több bérlő beállítások is szinkronizálja?
Ha több Azure AD különböző fiókok Azure AD bérlőre ugyanarra az eszközre, frissítenie kell az eszköz beállításjegyzékébe minden Azure AD-bérlő az Azure Rights Management (Azure RMS) folytatott kommunikációhoz.  

1. Minden Azure AD-bérlő a GUID keresése. Nyissa meg az Azure-portálon, és válassza ki az Azure AD-bérlő. A bérlőhöz tartozó GUID még a Tulajdonságok lapon a kiválasztott bérlő (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) címkével **könyvtár-azonosítója**. 
2. Miután a globálisan egyedi Azonosítót, akkor a beállításkulcs **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<bérlői azonosító GUID >**.
   Az a **bérlői azonosító GUID** kulcsot, hozzon létre egy új karakterláncsoros értéket (REG-MULTI-SZ) **AllowedRMSServerUrls**. Az adatait adja meg a licencelési terjesztési pont URL-címei a más Azure bérlőre, az eszköz hozzáférő.
3. A terjesztési pont licencelési URL-címeket található futtatásával a **Get-AadrmConfiguration** parancsmag. Ha az értékek a **LicensingIntranetDistributionPointUrl** és **LicensingExtranetDistributionPointUrl** eltérőek, mindkét értéket adni. Ha az értékek megegyeznek, csak egyszer adja meg az értéket.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Mik a meglévő Windows asztali alkalmazások központi beállítások lehetőségei?
Csak az univerzális Windows-alkalmazások központi működik. Nincsenek elérhető egy meglévő Windows asztali alkalmazások a roaming engedélyezésének két lehetőség közül választhat:

* A [asztali híd](http://aka.ms/desktopbridge) segítséget nyújt a meglévő Windows asztali alkalmazások kerüljön a univerzális Windows Platform. Itt minimális kódmódosítással lesz szükség az Azure AD alkalmazásadat-barangolás előnyeit. Az asztal híd biztosít az alkalmazások app identitást, amely engedélyezéséhez alkalmazásadat-barangolás meglévő asztali alkalmazások esetén van szükség.
* [Felhasználói élmény Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) segítségével hozzon létre egy meglévő Windows asztali alkalmazások egyéni beállítások sablont, és engedélyezte a Win32-alkalmazások központi. Ezzel a beállítással kell az alkalmazás fejlesztőjének az alkalmazás módosításához. UE-V a helyszíni Active Directory központi az ügyfelek, akik a Microsoft Desktop Optimization Pack korlátozódik.

A rendszergazdák konfigurálhatják UE-V számára a Windows asztali alkalmazások adatainak a Windows operációs rendszer beállításainak és univerzális alkalmazások adatainak keresztül központi módosításával [UE-V csoportházirendek](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), többek között a következőket:

* A Windows beállításait csoportházirend barangolás
* Ne szinkronizáljon a Windows csoportházirend
* Az Internet Explorer, az alkalmazások szakaszban központi

A jövőben Microsoft előfordulhat, hogy vizsgálja ki módon UE-V mélyen integrált a Windows rendszerbe, és kiterjesztése UE-V számára a beállításokat az Azure AD cloud keresztül.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Tárolhatok szinkronizált beállításokat és adatokat a helyszínen?
A vállalati Állapothordozás az összes szinkronizált adatot tárolja Azure felhőben. UE-V biztosít egy helyszíni megoldás központi.

## <a name="who-owns-the-data-thats-being-roamed"></a>Ki, hogy a rendszer éppen forrásul az adatok tulajdonosa?
A vállalatok számára saját vállalati Állapothordozás keresztül forrásul az adatokat. Egy Azure-adatközpontban adatokat tárolja. Összes felhasználói adat titkosítva van, mind az átvitel során, és az Azure RMS a felhő aktívan. Ez a Microsoft fiók-alapú beállítások szinkronizálás, amely csak bizonyos érzékeny adatok, például felhasználói hitelesítő adatok titkosítja, mielőtt az eszköz kikerül képest javítása.

A Microsoft elkötelezett az adatok védelme. Egy vállalati felhasználói beállítások automatikusan adattitkosítás az Azure RMS által még a Windows 10-eszközön, hogy más felhasználó nem tudja olvasni az adatokat. Ha a szervezete a szolgáltatás fizetős Azure RMS-hez, akkor használja az egyéb Azure RMS-szolgáltatásokkal, például nyomon követése és visszavonatni a dokumentumokat, automatikusan bizalmas információkat tartalmazó e-mailek védelméhez, és beállíthatja a saját kulcsok (a "állapotba hozása a saját kulcs" megoldás, más néven BYOK). Ezek a szolgáltatások és az Azure RMS működésével kapcsolatos további információkért lásd: [Mi az az Azure Rights Management](https://technet.microsoft.com/jj585026.aspx).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kezelheti egy adott alkalmazás vagy beállítás-szinkronizálás?
Windows 10-ben található mobileszköz-kezelési vagy a csoportházirend az egyes alkalmazások a roaming letiltása. A bérlői rendszergazdák letilthatják appdata szinkronizálási az összes olyan alkalmazáshoz egy felügyelt eszközön, de nincs egyeztetését vezérlő alkalmazásonkénti vagy alkalmazáson belül szinten.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hogyan is engedélyezheti vagy letilthatja a központi?
Az a **beállítások** alkalmazást, és navigáljon **fiókok** > **beállítások szinkronizálása**. Ezen a lapon láthatja, melyik fiók számára a beállításokat használja, és engedélyezheti vagy tiltsa le az egyes csoportok beállításokat kell forrásul.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Mi az a Microsoft által ajánlott engedélyezése a Windows 10 központi?
A Microsoftnál érhető el, beleértve a központi felhasználói profilok, a UE-V és a vállalati állapot központi megoldások központi néhány beállítást.  A Microsoft elkötelezett egy befektetés vállalati központi későbbi Windows verziók állapot. Ha a szervezet nem áll készen vagy kényelmes az adatok áthelyezését a felhőbe, majd ajánlott UE-V, az elsődleges központi technológia használatát. Ha a szervezet központi meglévő Windows asztali alkalmazások támogatása szükséges, de nem szeretné, helyezze át a felhőbe, vállalati állapot központi és UE-V használatát javasoljuk. Annak ellenére, hogy UE-V és a vállalati Állapothordozás nagyon hasonló technológiákat, azok nincsenek kölcsönösen kizárják egymást. Kiegészíti egymástól annak biztosítására, hogy a szervezet a központi olyan szolgáltatásokat biztosít, a felhasználóknak hozzá kell.  

Ha a vállalati Állapothordozás és UE-V, az alábbi szabályok vonatkoznak:

* Az elsődleges központi ügynök az eszközön a vállalati Állapothordozás. UE-V használják, hogy kiegészíti a "Win32 térközét."
* UE-V központi Windows-beállítások és modern UWP-alkalmazások adatainak le kell tiltani, ha a UE-V csoport szabályzatok. Ezekre már vonatkozik a vállalati Állapothordozás.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hogyan a vállalati Állapothordozás támogatja a virtuális asztali infrastruktúra (VDI)?
A vállalati Állapothordozás termékváltozatok Windows 10-ügyfélen, de nem található a kiszolgálón termékváltozatok esetén támogatott. Ha egy ügyfél VM birtokolt hipervizor gépen, és távolról jelentkeznek be a virtuális gépet, az adatok fog vándorol. Ha több felhasználó osztozik ugyanazon operációs rendszer és a felhasználók távolról jelentkeznek be a kiszolgáló teljes asztali élmény, központi előfordulhat, hogy nem működik. Az utóbbi munkamenet-alapú forgatókönyv hivatalosan nem támogatott.

## <a name="what-happens-when-my-organization-purchases-azure-rms-after-using-roaming"></a>Mi történik, ha a szervezet Azure RMS vásárol barangolás felhasználását követően?
Ha már használja a szervezet az Azure RMS-korlátozott használatú ingyenes előfizetés, a Windows 10 központi megvásárlásáról a fizetős Azure RMS-előfizetés nem semmilyen hatással lesz a központi funkció működését, és konfigurációs módosítások nélküli lesz szükség a rendszergazda által.

## <a name="known-issues"></a>Ismert problémák
A dokumentáció a a [hibaelhárítási](active-directory-windows-enterprise-state-roaming-troubleshooting.md) fellépő ismert problémák listáját szakasz. 

## <a name="related-topics"></a>Kapcsolódó témakörök
* [Vállalati állapotának központi áttekintése](active-directory-windows-enterprise-state-roaming-overview.md)
* [Az Azure Active Directoryban a vállalati állapothordozás engedélyezése](active-directory-windows-enterprise-state-roaming-enable.md)
* [Csoport házirend és a mobileszköz-kezelési beállítások beállítások szinkronizálási szolgáltatás](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 központi beállításainak ismertetése](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [hibaelhárítással](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
