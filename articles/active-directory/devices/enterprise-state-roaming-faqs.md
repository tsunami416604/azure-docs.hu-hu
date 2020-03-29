---
title: Vállalati állapotroaming – gyakori kérdések – Azure Active Directory
description: Gyakori kérdések az ESR-ről
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad76835b0c72b691e1ef8810f2c58dedb8f597d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672378"
---
# <a name="settings-and-data-roaming-faq"></a>Beállítások és adatroaming GYIK

Ez a cikk választ ad a rendszergazdák által a beállításokkal és az alkalmazásadatok szinkronizálásával kapcsolatos kérdésekre.

## <a name="what-data-roams"></a>Milyen adatok barangolnak?

**Windows-beállítások**: a Windows operációs rendszerbe beépített gépház-beállítások. Ezek általában a számítógép személyre szabását szolgálják, és a következő általános kategóriákat tartalmazzák:

* *Téma*, amely olyan funkciókat tartalmaz, mint az asztali téma és a tálca beállításai.
* *Az Internet Explorer beállításai*, beleértve a nemrég megnyitott lapokat és kedvenceket is.
* *A Microsoft Edge böngésző beállításai*, például a kedvencek és az olvasási lista.
* *Jelszavak*, beleértve az internetes jelszavakat, A Wi-Fi profilok, és mások.
* *Nyelvi beállítások*, amelyek tartalmazzák a billentyűzetkiosztások, a rendszernyelv, a dátum és az idő beállításait stb.
* *A könnyű hozzáférés funkciói*, például a kontrasztos téma, a Narrátor és a Nagyító.
* *A Windows egyéb beállításai*, például az egér beállításai.

> [!NOTE]
> Ez a cikk a 2015 júliusában Windows 10 rendszerrel indított Microsoft Edge Legacy HTML-alapú böngészőre vonatkozik. A cikk nem vonatkozik az új Microsoft Edge Chromium-alapú böngésző re január 15-én, 2020. Az új Microsoft Edge szinkronizálási viselkedéséről további információt a Microsoft Edge Sync című cikkben [talál.](/deployedge/microsoft-edge-enterprise-sync)

**Alkalmazásadatok**: Az univerzális Windows-alkalmazások a beállítási adatokat egy központi mappába írják, és a mappába írt adatok automatikusan szinkronizálódnak. Az egyes alkalmazásfejlesztőknek kell megtervezniük egy alkalmazást, hogy kihasználhassák ezt a lehetőséget. A barangolást használó univerzális Windows-alkalmazások fejlesztéséről az [appdata storage API-ban](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) és a [Windows 8 alkalmazásadat-központi fejlesztői blogban](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)olvashat bővebben.

## <a name="what-account-is-used-for-settings-sync"></a>Milyen fiókkal szinkronizálható a beállítás?

A Windows 8.1-ben a beállítások szinkronizálása mindig fogyasztói Microsoft-fiókokat használt. A vállalati felhasználók microsoftos fiókot kapcsolhatnak az Active Directory tartományi fiókjukhoz, hogy hozzáférjenek a beállítások szinkronizálásához. A Windows 10-ben ezt a csatlakoztatott Microsoft-fiók funkciót egy elsődleges/másodlagos fiókkeretrendszer váltja fel.

Az elsődleges fiók a Windows rendszerbe való bejelentkezéshez használt fiók. Ez lehet egy Microsoft-fiók, egy Azure Active Directory (Azure AD) fiók, egy helyszíni Active Directory-fiók vagy egy helyi fiók. Az elsődleges fiók mellett a Windows 10-felhasználók egy vagy több másodlagos felhőfiókot is hozzáadhatnak az eszközükhöz. A másodlagos fiók általában egy Microsoft-fiók, egy Azure AD-fiók, vagy más fiók, például a Gmail vagy a Facebook. Ezek a másodlagos fiókok további szolgáltatásokhoz biztosítanak hozzáférést, például az egyszeri bejelentkezéshez és a Windows Áruházhoz, de nem képesek a beállítások szinkronizálásának bekapcsolására.

A Windows 10-ben csak az eszköz elsődleges fiókja használható a beállítások szinkronizálásához (lásd: [Hogyan frissíthetek a Microsoft-fiók beállításainak szinkronizálása a Windows 8 rendszerben az Azure AD beállításainak szinkronizálására a Windows 10-ben?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Az adatok soha nem keverednek az eszközön lévő különböző felhasználói fiókok között. A beállítások szinkronizálására két szabály vonatkozik:

* A Windows beállításai mindig az elsődleges fiókkal barangolnak.
* Az alkalmazásadatok az alkalmazás megszerzéséhez használt fiókkal lesznek megjelölve. Csak az elsődleges fiókkal jelölt alkalmazások lesznek szinkronizálva. Az alkalmazás tulajdonjogi címkézése akkor kerül meghatározásra, ha egy alkalmazás a Windows Áruházban vagy a mobileszköz-felügyeleten (MDM) keresztül van betöltve.

Ha egy alkalmazás tulajdonosa nem azonosítható, akkor az elsődleges fiókkal fog barangolni. Ha egy eszközt Windows 8-ról vagy Windows 8.1-ről Windows 10-re frissít, az összes alkalmazás a Microsoft-fiók által beszerzettként lesz címkézve. Ennek az az oka, hogy a legtöbb felhasználó a Windows Áruházon keresztül szerez be alkalmazásokat, és a Windows 10 előtt nem volt Windows Áruházbeli támogatás az Azure AD-fiókokhoz. Ha egy alkalmazás offline licenccel van telepítve, az alkalmazás az eszközön lévő elsődleges fiók használatával lesz címkézve.

> [!NOTE]
> A nagyvállalati tulajdonú és az Azure AD-hez kapcsolódó Windows 10-eszközök már nem tudják csatlakoztatni Microsoft-fiókjukat egy tartományi fiókhoz. A Microsoft-fiók tartományi fiókhoz való csatlakoztatásának lehetősége, valamint a felhasználó összes adatszinkronizálása a Microsoft-fiókkal (azaz a csatlakoztatott Microsoft-fiókon és az Active Directory-funkción keresztül barangoló Microsoft-fiókkal) törlődik a Windows 10-ből csatlakoztatott Active Directory vagy Azure AD környezethez csatlakoztatott eszközök.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hogyan frissíthetek a Microsoft-fiók beállításainak szinkronizálásáról a Windows 8-ban az Azure AD-beállítások szinkronizálására a Windows 10-ben?

Ha csatlakozik a Windows 8.1 rendszert futtató Active Directory tartományhoz egy csatlakoztatott Microsoft-fiókkal, a beállításokat a Microsoft-fiókján keresztül szinkronizálja. A Windows 10-re való frissítés után továbbra is szinkronizálja a felhasználói beállításokat a Microsoft-fiókon keresztül, amíg tartományhoz csatlakozott felhasználó, és az Active Directory tartomány nem csatlakozik az Azure AD-hez.

Ha a helyszíni Active Directory-tartomány nem csatlakozik az Azure AD, az eszköz megpróbálja szinkronizálni a beállításokat a csatlakoztatott Azure AD-fiók használatával. Ha az Azure AD-rendszergazda nem engedélyezi a vállalati állapotroamingot, a csatlakoztatott Azure AD-fiók leállítja a szinkronizálási beállításokat. Ha Ön Windows 10-felhasználó, és egy Azure AD-identitással jelentkezik be, akkor elindítja a Windows-beállítások szinkronizálását, amint a rendszergazda engedélyezi a beállítások szinkronizálását az Azure AD-n keresztül.

Ha bármilyen személyes adatot tárol a vállalati eszközön, tudnia kell, hogy a Windows operációs rendszer és az alkalmazásadatok szinkronizálása megkezdődik az Azure AD-vel. Ennek a következő következményei vannak:

* A személyes Microsoft-fiók beállításai eltávolodnak a munkahelyi vagy iskolai Azure AD-fiókok beállításaitól. Ennek az az oka, hogy a Microsoft-fiók és az Azure AD-beállítások szinkronizálása mostantól külön fiókokat használ.
* A korábban csatlakoztatott Microsoft-fiókon keresztül szinkronizált személyes adatokat, például a Wi-Fi-jelszavakat, a webes hitelesítő adatokat és az Internet Explorer-kedvencek szinkronizálása az Azure AD-n keresztül lesz szinkronizálva.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Hogyan működik a Microsoft-fiók és az Azure AD Enterprise State Roaming interoperabilitása?

A Windows 10 2015 novemberi vagy későbbi kiadásaiban az Enterprise State Roaming egyszerre csak egy fiókesetében támogatott. Ha munkahelyi vagy iskolai Azure AD-fiókkal jelentkezik be a Windowsba, az összes adat szinkronizálódik az Azure AD-n keresztül. Ha személyes Microsoft-fiókkal jelentkezik be a Windows rendszerbe, az összes adat szinkronizálódik a Microsoft-fiókon keresztül. Az univerzális alkalmazásadatok csak az elsődleges bejelentkezési fiókot használják az eszközön, és csak akkor barangolnak, ha az alkalmazás licence az elsődleges fiók tulajdonában van. A másodlagos fiókok tulajdonában lévő alkalmazások univerzális alkalmazásadatai nem lesznek szinkronizálva.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Szinkronizálja a beállításokat az Azure AD-fiókok több bérlőtől?

Ha több Azure AD-fiók különböző Azure AD-bérlők ugyanazon az eszközön, frissítenie kell az eszköz beállításjegyzékét, hogy kommunikálni az Azure Rights Management szolgáltatás minden Azure AD-bérlők.  

1. Keresse meg az egyes Azure AD-bérlők GUID-azonosítóját. Nyissa meg az Azure Portalon, és válasszon ki egy Azure AD-bérlőt. A bérlő GUID azonosítója a kijelölt bérlőhttps://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)( tulajdonságok lapján **található.** 
2. A GUID után hozzá kell adnia a rendszerleíró kulcsot **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<bérlői azonosító azonosítója>. **
   A **bérlői azonosító GUID** kulcsából hozzon létre egy új többkarakterláncos értéket (REG-MULTI-SZ) **Nevű AllowedRMSServerUrls**néven. Az adatok, adja meg a licencelési terjesztési pont URL-címeit a többi Azure-bérlők, amelyek az eszköz hozzáfér.
3. A licencelésterjesztési pont URL-címeit az **AADRM modul Get-AadrmConfiguration** parancsmagjának futtatásával találhatja meg. Ha a **LicensingIntranetDistributionPointUrl** és a **LicensingExtranetDistributionPointUrl** értékei eltérőek, adja meg mindkét értéket. Ha az értékek megegyeznek, csak egyszer adja meg az értéket.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Melyek a meglévő asztali Windows-alkalmazások központi beállításai?

A barangolás csak az univerzális Windows-alkalmazásokesetében működik. Egy meglévő asztali Windows-alkalmazásban kétfélekul szolgáló barangolás engedélyezése:

* Az [asztali híd](https://aka.ms/desktopbridge) segítségével meglévő Asztali Windows-alkalmazásait az univerzális Windows-platformra viheti. Innen minimális kódmódosításra lesz szükség az Azure AD-alkalmazás adatroamingjának kihasználásához. A Desktop Bridge alkalmazásidentitást biztosít az alkalmazások számára, amely a meglévő asztali alkalmazások alkalmazásadat-barangolásának engedélyezéséhez szükséges.
* [A felhasználói élmény virtualizálása (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) segítségével egyéni beállítássablont hozhat létre a meglévő Asztali Windows-alkalmazásokhoz, és engedélyezheti a Win32-alkalmazások barangolását. Ez a beállítás nem követeli meg az alkalmazás fejlesztőjének az alkalmazás kódjának módosítását. Az UE-V a Microsoft Desktop Optimization Pack csomagot megvásárló ügyfelek helyszíni Active Directory-barangolására korlátozódik.

A rendszergazdák úgy állíthatják be az UE-V-t, hogy barangoljon az asztali Windows asztali alkalmazások adataiközött a Windows operációs rendszer beállításainak és az univerzális alkalmazásadatoknak az [UE-V csoportházirendeken](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)keresztül történő módosításával, beleértve a következőket:

* Barangolása Windows-beállítások csoportházirendje
* A Windows Apps csoportházirendjének szinkronizálásának elnem enem nélkül
* Az Internet Explorer barangolása az alkalmazások szakaszban

A jövőben a Microsoft megvizsgálhatja, hogyan lehet az UE-V-t mélyen integrálni a Windows rendszerbe, és kiterjeszteni az UE-V-t a beállítások barangolására az Azure AD-felhőn keresztül.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Tárolhatom a szinkronizált beállításokat és adatokat a helyszínen?

Az Enterprise State Roaming az összes szinkronizált adatot a Microsoft-felhőben tárolja. Az UE-V helyszíni barangolási megoldást kínál.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kié az adat, amit bebarangolnak?

A vállalatok birtokolják az Enterprise State Roaming on keresztül barangolt adatokat. Az adatok egy Azure-adatközpontban tárolódnak. Az Azure Information Protection Azure Rights Management szolgáltatásának használatával az összes felhasználói adat titkosítva van az átvitel során és a felhőben egyaránt. Ez a Microsoft-fiókalapú beállítások szinkronizálásához képest előrelépés, amely csak bizonyos bizalmas adatokat, például felhasználói hitelesítő adatokat titkosít, mielőtt elhagyja az eszközt.

A Microsoft elkötelezett az ügyféladatok védelme mellett. A vállalati felhasználók beállítási adatait az Azure Rights Management szolgáltatás automatikusan titkosítja, mielőtt elhagyja a Windows 10-es eszközt, így más felhasználó nem tudja olvasni ezeket az adatokat. Ha szervezete fizetős előfizetéssel rendelkezik az Azure Rights Management szolgáltatáshoz, más védelmi funkciókat is használhat, például nyomon követheti és visszavonhatja a dokumentumokat, automatikusan megvédheti a bizalmas adatokat tartalmazó e-maileket, és kezelheti a saját kulcsait (a "hozd saját kulcs" megoldás, más néven BYOK). Ezekről a funkciókról és a védelmi szolgáltatás működéséről a [Mi az Azure Rights Management](/azure/information-protection/what-is-information-protection)című témakörben talál további információt.

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kezelhetem egy adott alkalmazás vagy beállítás szinkronizálását?

A Windows 10-ben nincs MDM- vagy csoportházirend-beállítás az egyes alkalmazások barangolásának letiltásához. A bérlői rendszergazdák letilthatják az alkalmazásadatok szinkronizálását a felügyelt eszközön lévő összes alkalmazásesetében, de alkalmazásonként vagy alkalmazáson belül nincs finomabb vezérlő.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hogyan engedélyezhetem vagy letilthatjam a barangolást?

A **Beállítások** alkalmazásban nyissa meg a **Fiókok** > **a beállítások szinkronizálása lehetőséget.** Ezen az oldalon láthatja, hogy melyik fiókkal történik a beállítások barangolása, és engedélyezheti vagy letilthatja az egyes beállításcsoportokat.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Mi a Microsoft javaslata a barangolás engedélyezésére a Windows 10-ben?

A Microsoft néhány különböző beállítással rendelkezik, többek között a központi felhasználói profilok, az UE-V és a vállalati állapotroaming.  A Microsoft elkötelezte magát amellett, hogy a Windows későbbi verzióiban befektessen az Enterprise State Roaming szolgáltatásba. Ha a szervezet nem áll készen az adatok felhőbe való áthelyezésére, akkor azt javasoljuk, hogy elsődleges barangolási technológiaként használja az UE-V-t. Ha a szervezetnek központi támogatására van szüksége a meglévő Asztali Windows-alkalmazásokhoz, de a felhőbe szeretne áthelyezni, azt javasoljuk, hogy használja az Enterprise State Roaming és az UE-V szolgáltatást is. Bár az UE-V és az Enterprise State Roaming nagyon hasonló technológiák, nem zárják ki egymást. Kiegészítik egymást, így biztosítva, hogy a szervezet biztosítsa a felhasználók számára szükséges barangolási szolgáltatásokat.  

Az Enterprise State Roaming és az UE-V használata esetén a következő szabályok érvényesek:

* Az Enterprise State Roaming az elsődleges barangolásügynök az eszközön. UE-V használják, hogy kiegészítse a "Win32 szakadék."
* A Windows-beállítások UE-V barangolását és a modern UWP-alkalmazásadatokat le kell tiltani az UE-V csoportházirendek használatakor. Ezekre már vonatkozik az Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hogyan támogatja a vállalati állami barangolás a virtuális asztali infrastruktúrát (VDI)?

A vállalati állapotroaming a Windows 10 ügyfél-sk-eken támogatott, a kiszolgálósinusokon azonban nem. Ha egy ügyfél virtuális gép egy hipervizor gépen található, és távolról bejelentkezik a virtuális gépre, az adatok barangolni fognak. Ha több felhasználó ugyanazt az operációs rendszert osztja meg, és a felhasználók távolról jelentkeznek be a kiszolgálóra a teljes asztali élmény érdekében, előfordulhat, hogy a barangolás nem fog működni. Az utóbbi munkamenet-alapú forgatókönyv hivatalosan nem támogatott.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Mi történik, ha a szervezet vásárol egy előfizetést, amely tartalmazza az Azure Rights Management használata után roaming?

Ha szervezete már használja a központi használatot a Windows 10-ben az Azure Rights Management korlátozott használatú ingyenes előfizetéssel, az Azure Rights Management védelmi szolgáltatást tartalmazó [fizetős előfizetés](https://azure.microsoft.com/pricing/details/information-protection/) vásárlása nem lesz hatással a központi funkció működésére, és az informatikai rendszergazda nem lesz szükség konfigurációs módosításokra.

## <a name="known-issues"></a>Ismert problémák

Az ismert problémák listáját a [hibaelhárítási](enterprise-state-roaming-troubleshooting.md) szakasz dokumentációjában találja. 

## <a name="next-steps"></a>További lépések 

Áttekintést a [vállalati állami barangolás áttekintése című témakörben talál.](enterprise-state-roaming-overview.md)
