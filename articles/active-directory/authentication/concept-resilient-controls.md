---
title: Rugalmas hozzáférés-vezérlési stratégia létrehozása – Azure AD
description: Ez a dokumentum útmutatást ad azokra a stratégiákra vonatkozóan, amelyeket a szervezetnek el kell fogadnia, hogy csökkentse az előre nem látható zavarok esetén a kizárás kockázatát.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ca5817e744ff81efcd549bc328d7ce5eeedb2d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908734"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Rugalmas hozzáférés-vezérlési stratégia létrehozása az Azure Active Directoryval

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Mivel a Microsoftnak reagálnia kell a változó piaci feltételekre, azt nem szabad úgy értelmezni, hogy az a Microsoft kötelezettségvállalása, és a Microsoft nem tudja garantálni a közzététel után bemutatott információk pontosságát.

Az egyetlen hozzáférés-vezérlésre , például a többtényezős hitelesítésre (MFA) vagy egy hálózati helyre támaszkodó szervezetek, amelyek informatikai rendszereiket biztosítják, ki vannak téve az alkalmazásaikhoz és erőforrásaikhoz való hozzáférési hibáknak, ha az egyetlen hozzáférés-vezérlés elérhetetlenné válik vagy helytelenül konfigurált. Egy természeti katasztrófa például a távközlési infrastruktúra vagy a vállalati hálózatok nagy szegmenseinek elérhetetlenségét eredményezheti. Egy ilyen megszakítás megakadályozhatja, hogy a végfelhasználók és a rendszergazdák betudjanak jelentkezni.

Ez a dokumentum útmutatást nyújt azokat a stratégiákat, amelyeket a szervezetnek el kell fogadnia, hogy ellenálló legyen az előre nem látható zavarok esetén a zárolás kockázatának csökkentése érdekében a következő forgatókönyvekkel:

 1. A szervezetek növelhetik rugalmasságukat, hogy csökkentsék a kizárás kockázatát a **megszakításelőtt** kockázatcsökkentési stratégiák vagy vészhelyzeti tervek végrehajtásával.
 2. A szervezetek továbbra is hozzáférhetnek az általuk választott alkalmazásokhoz és erőforrásokhoz **a megszakítások során,** ha kockázatcsökkentési stratégiákat és vészhelyzeti terveket vezetnek be.
 3. A szervezeteknek gondoskodniuk kell arról, hogy **a megszakítás után** és az általuk végrehajtott vészhelyzetek visszaállítása előtt megőrizzék az információkat, például a naplókat.
 4. Azok a szervezetek, amelyek nem hajtottak végre megelőzési stratégiákat vagy alternatív terveket, **vészhelyzeti lehetőségeket** valósíthatnak meg a zavar kezelésére.

## <a name="key-guidance"></a>Kulcsfontosságú útmutatás

Ebben a dokumentumban négy legfontosabb elvitelre kerül:

* Kerülje a rendszergazdai zárolást a segélyhívó fiókok használatával.
* Valósítsa meg az MFA-t feltételes hozzáféréssel (CA) a felhasználónkénti több-támogatás helyett.
* A felhasználói zárolás csökkentése több feltételes hozzáférésű (CA) vezérlő használatával.
* A felhasználói zárolás mérséklése több hitelesítési módszer vagy megfelelő minden felhasználó számára történő kiépítésével.

## <a name="before-a-disruption"></a>A zavar megzavarása előtt

A tényleges megszakítás okainak a szervezet elsődleges célja kell, hogy legyen az esetlegesen felmerülő hozzáférés-vezérlési problémák kezelésében. Az enyhítés magában foglalja egy tényleges esemény tervezését, valamint a végrehajtási stratégiákat, amelyek biztosítják, hogy a hozzáférés-vezérlésés a műveletek ne legyenek hatással a fennakadások során.

### <a name="why-do-you-need-resilient-access-control"></a>Miért van szükség rugalmas hozzáférés-vezérlésre?

 Az identitás az alkalmazásokat és erőforrásokat elérő felhasználók vezérlősíkja. Az identitásrendszer szabályozza, hogy mely felhasználók és milyen feltételek mellett , például hozzáférés-vezérlés vagy hitelesítési követelmények esetén mely felhasználók férhetnek hozzá az alkalmazásokhoz. Ha egy vagy több hitelesítési vagy hozzáférés-vezérlési követelmény nem áll rendelkezésre a felhasználók számára a hitelesítéshez előre nem látható körülmények miatt, a szervezetek az alábbi problémák egyikét vagy mindkettőt tapasztalhatják:

* **Rendszergazdai zárolás:** A rendszergazdák nem kezelhetik a bérlőt vagy a szolgáltatásokat.
* **Felhasználói zárolás:** A felhasználók nem férhetnek hozzá az alkalmazásokhoz és az erőforrásokhoz.

### <a name="administrator-lockout-contingency"></a>Rendszergazdai zárolásra való kizáró vészhelyzet

A bérlő höz való rendszergazdai hozzáférés feloldásához létre kell hoznia a vészelérési fiókokat. Ezek a vészelérési fiókok, más néven *üvegtörési* fiókok, lehetővé teszik az Azure AD-konfiguráció kezeléséhez való hozzáférést, ha a normál kiemelt jogosultságú fiókhozzáférési eljárások nem érhetők el. Legalább két vészelérési fiókot kell létrehozni a [vészelérési fiók ajánlásait követve.]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

### <a name="mitigating-user-lockout"></a>A felhasználói zárolás mérséklése

 A felhasználói zárolás kockázatának csökkentése érdekében használja a feltételes hozzáférés-házirendeket több vezérlővel, hogy a felhasználók választhatnak, hogyan férhetnek hozzá az alkalmazásokhoz és az erőforrásokhoz. Ha a felhasználó nak választási lehetőséget ad például az MFA-val való **bejelentkezés, vagy** a felügyelt eszközről való bejelentkezés **vagy** a vállalati hálózatról való bejelentkezés között, ha a hozzáférés-vezérlések egyike nem érhető el, a felhasználónak más lehetőségei is vannak a munka folytatására.

#### <a name="microsoft-recommendations"></a>A Microsoft javaslatai

A szervezet meglévő feltételes hozzáférési házirendjeibe építsd be a következő hozzáférés-vezérléseket:

1. Több hitelesítési módszer kiépítése minden olyan felhasználó számára, amely különböző kommunikációs csatornákon alapul, például a Microsoft Authenticator alkalmazás (internetalapú), az OATH token (eszközön létrehozott) és az SMS (telefonos).
2. Telepítse a Windows Hello for Business alkalmazást Windows 10-es eszközökön, hogy közvetlenül az eszközbejelentkezésből feleljen meg az MFA-követelményeknek.
3. Megbízható eszközök használata [az Azure AD Hybrid Join](https://docs.microsoft.com/azure/active-directory/devices/overview) vagy a Microsoft [Intune felügyelt eszközein](https://docs.microsoft.com/intune/planning-guide)keresztül. A megbízható eszközök javítják a felhasználói élményt, mivel maga a megbízható eszköz anélkül képes kielégíteni a házirend erős hitelesítési követelményeit, hogy a felhasználónak mfa-kihívást jelentene. MFA ezután lesz szükség, amikor egy új eszköz regisztrálása, és amikor alkalmazások vagy erőforrások nem megbízható eszközökről való hozzáférés.
4. Azure AD-identitásvédelem kockázatalapú szabályzatok, amelyek megakadályozzák a hozzáférést, ha a felhasználó vagy a bejelentkezés veszélyben van a rögzített MFA-szabályzatok helyett.

>[!NOTE]
> A kockázatalapú szabályzatok [Hoznak Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) licenceket.

A következő példa ismerteti a szabályzatok létre kell hoznia, hogy egy rugalmas hozzáférés-vezérlés a felhasználó számára az alkalmazások és erőforrások eléréséhez. Ebben a példában egy biztonsági csoport **AppUsers** a célfelhasználók szeretne hozzáférést adni, egy csoport nevű **CoreAdmins** az alapvető rendszergazdák, és egy csoport nevű **EmergencyAccess** a segélyhívó fiókok.
Ez a példa házirend-készlet hozzáférést biztosít a kiválasztott felhasználók **AppUsers**, hozzáférést biztosít a kiválasztott alkalmazások, ha azok csatlakoznak egy megbízható eszközről, vagy erős hitelesítést, például többfa. Nem tartalmazza a vészhelyzeti fiókokat és az alapvető rendszergazdákat.

**CA mérséklési házirendek készlet:**

* 1. házirend: A célcsoportokon kívüli személyek hez való hozzáférés letiltása
  * Felhasználók és csoportok: Az összes felhasználó felvétele. AppUsers, CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Az összes alkalmazás felvétele
  * Feltételek: (Nincs)
  * Grant Control: Blokk
* 2. házirend: Hozzáférés megadása az MFA-t vagy megbízható eszközt igénylő AppUsers-ek számára.
  * Felhasználók és csoportok: Alkalmazásfelhasználók felvétele. CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Az összes alkalmazás felvétele
  * Feltételek: (Nincs)
  * Támogatás vezérlése: Hozzáférés megadása, többtényezős hitelesítés megkövetelése, az eszköz megfelelőségének megkövetelése. Több vezérlő höz: A kijelölt vezérlők egyikének megkövetelése.

### <a name="contingencies-for-user-lockout"></a>A felhasználói zárolás ra vonatkozó függő események

Másik lehetőségként a szervezet is létrehozhat vészhelyzeti házirendeket. Vészhelyzeti házirendek létrehozásához meg kell határoznia az üzletmenet folytonossága, a működési költségek, a pénzügyi költségek és a biztonsági kockázatok közötti kompromisszumos feltételeket. Például aktiválhat egy vészhelyzeti szabályzatot csak a felhasználók egy részhalmaza, az alkalmazások egy részhalmaza, az ügyfelek egy részhalmaza vagy a helyek egy részhalmaza számára. A vészhelyzeti házirendek hozzáférést biztosítanak a rendszergazdáknak és a végfelhasználóknak az alkalmazásokhoz és az erőforrásokhoz, ha nincs megoldási módszer megvalósítva.
Az expozíció megértése a megszakítás során segít csökkenteni a kockázatot, és a tervezési folyamat kritikus része. A vészhelyzeti intézkedéscsomag létrehozásához először határozza meg a szervezet alábbi üzleti követelményeit:

1. Előre meghatározhatja a kritikus fontosságú alkalmazásokat: Melyek azok az alkalmazások, amelyekhez hozzáférést kell biztosítania, még alacsonyabb kockázat/biztonsági pozíció mellett is? Készítsen listát ezekről az alkalmazásokról, és győződjön meg arról, hogy a többi érdekelt fél (üzleti, biztonsági, jogi, vezetői) mind egyetért abban, hogy ha minden hozzáférés-vezérlés eltűnik, ezeknek az alkalmazásoknak továbbra is futniuk kell. Ön valószínűleg lesz a végén kategóriák:
   * **1. kategória kritikus fontosságú alkalmazások,** amelyek nem érhetők el néhány percnél tovább, például alkalmazások, amelyek közvetlenül befolyásolják a szervezet bevételeit.
   * **2. kategória fontos alkalmazások,** amelyeket a vállalkozásnak néhány órán belül elérhetővé kell tennie.
   * **3. kategória alacsony prioritású alkalmazások,** amelyek ellenállnak a néhány napos megszakításnak.
2. Az 1.
   * Engedélyezi a teljes hozzáférést vagy a korlátozott munkamenetet, például korlátozza a letöltéseket?
   * Engedélyezi a hozzáférést az alkalmazás egy részéhez, de nem az egész alkalmazást?
   * Engedélyezi az információfeldolgozók nak a hozzáférést, és letiltja a rendszergazdai hozzáférést a hozzáférés-vezérlés visszaállításáig?
3. Ezekhez az alkalmazásokhoz a Microsoft azt is javasolja, hogy tervezze meg, mely hozzáférési utakat fogja szándékosan megnyitni, és melyeket fogja bezárni:
   * Engedélyezi, hogy a böngésző csak az offline adatokat menthető, gazdag ügyfelekhez férjen hozzá, és blokkolja azokat a gazdag ügyfeleket, amelyek menthetik őket?
   * Csak a vállalati hálózaton belüli felhasználók számára engedélyezi a hozzáférést, és letiltja a külső felhasználókat?
   * Csak a fennakadások idején szeretne hozzáférést engedélyezni bizonyos országokból vagy régiókból?
   * Szeretné, hogy a vészhelyzeti házirendek házirendjei, különösen a kritikus fontosságú alkalmazások esetében, sikertelenek legyenek, vagy sikeresek legyenek, ha nem érhető el alternatív hozzáférés-vezérlés?

#### <a name="microsoft-recommendations"></a>A Microsoft javaslatai

A vészhelyzeti feltételes hozzáférési szabályzat egy **letiltott szabályzat,** amely kihagyja az Azure MFA, külső MFA, kockázatalapú vagy eszközalapú vezérlők. Ezt követően, amikor a szervezet úgy dönt, hogy aktiválja a vészhelyzeti tervet, a rendszergazdák engedélyezhetik a házirendet, és letilthatják a rendszeres, vezérlésalapú házirendeket.

>[!IMPORTANT]
> A felhasználók biztonságát kikényszerítő házirendek letiltása – akár ideiglenesen is – csökkenti a biztonsági állapotot, amíg a vészhelyzeti terv érvényben van.

* Konfigurálja a tartalék házirendek készletét, ha egy hitelesítő adattípus vagy egy hozzáférés-vezérlési mechanizmus megszakadása hatással van az alkalmazásokhoz való hozzáférésre. Konfiguráljon egy olyan szabályzatot letiltott állapotban, amelyhez a tartományhoz való csatlakozást vezérlőként, egy külső MFA-szolgáltatót igénylő aktív házirend biztonsági másolataként kell beállítani.
* Csökkentse annak kockázatát, hogy a rossz szereplők jelszavakat találjanak ki, ha nincs szükség többfa-hitelesítésre, kövesse a [jelszóútmutató](https://aka.ms/passwordguidance) ban szereplő gyakorlatot.
* Telepítse [az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) és [az Azure AD password protection,](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) hogy a felhasználók ne használja a közös jelszót és kifejezéseket úgy dönt, hogy tiltsa.
* Olyan házirendeket használjon, amelyek korlátozzák az alkalmazásokon belüli hozzáférést, ha egy bizonyos hitelesítési szintet nem ér el ahelyett, hogy egyszerűen visszaesne a teljes hozzáféréshez. Példa:
  * Állítson be egy biztonsági mentési házirendet, amely elküldi a korlátozott munkamenet-jogcímet az Exchange és a SharePoint számára.
  * Ha a szervezet a Microsoft Cloud App Security alkalmazást használja, fontolja meg a visszaeső házirendet, amely bevonja az MCAS-t, majd az MCAS lehetővé teszi az írásvédett hozzáférést, de a feltöltéseket nem.
* Nevezze el a szabályzatokat, hogy a megszakítások során könnyen megtalálhatja őket. A házirend nevében a következő elemek szerepeljenek:
  * A házirend *címkeszáma.*
  * A megjelenítandó szöveg szerint ez a politika csak vészhelyzetekre szolgál. Például: **ENABLE VÉSZHELYZETBEN**
  * A *zavar,* amire vonatkozik. Például: **Az MFA zavarai során**
  * A házirendek aktiválásához szükséges sorrendet egy *sorszám.*
  * Az *alkalmazások,* amelyekre vonatkozik.
  * Az általa alkalmazott *kezelőszervek.*
  * A *feltételek,* amit megkövetel.
  
Ez az elnevezési szabvány a vészhelyzeti házirendek esetében a következő lesz: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

A következő példa: **Példa A – Készenléti hitelesítésbiztosítási házirend az access alapvető fontosságú együttműködési alkalmazásokhoz való hozzáférés visszaállításához,** tipikus vállalati vészhelyzet. Ebben a forgatókönyvben a szervezet általában megköveteli az MFA-t az összes Exchange Online- és SharePoint Online-hozzáféréshez, és ebben az esetben a zavar az ügyfél mfa-szolgáltatója kimaradás (akár Azure MFA, helyszíni MFA-szolgáltató vagy külső MFA). Ez a házirend csak akkor enyhíti ezt a kimaradást, ha lehetővé teszi, hogy bizonyos célzott felhasználók csak akkor férjenek hozzá ezekhez az alkalmazásokhoz megbízható Windows-eszközökről, ha az alkalmazást a megbízható vállalati hálózatukról férnek hozzá. Emellett kizárja a vészhelyzeti fiókokat és az alapvető rendszergazdákat ezekből a korlátozásokból. A megcélzott felhasználók ezután hozzáférhetnek az Exchange Online-hoz és a SharePoint Online-hoz, míg más felhasználók a kimaradás miatt továbbra sem férhetnek hozzá az alkalmazásokhoz. Ebben a példában egy elnevezett hálózati hely **CorpNetwork** és egy biztonsági csoport **ContingencyAccess** a célfelhasználók, egy csoport nevű **CoreAdmins** az alapvető rendszergazdák, és egy csoport nevű **EmergencyAccess** a segélyhívó fiókok. A vészhelyzeti négy szabályzatot igényel a kívánt hozzáférés biztosításához. 

**A példa – Vészhelyzeti hitelesítésbiztosítási házirendek a kritikus fontosságú együttműködési alkalmazásokhoz való hozzáférés visszaállításához:**

* 1. házirend: Tartományhoz csatlakozott eszközök megkövetelése az Exchange és a SharePoint számára
  * Név: EM001 - ENABLE IN EMERGENCY: MFA disruption[1/4] - Exchange SharePoint - Hibrid Azure AD-illesztés szükséges
  * Felhasználók és csoportok: A készenléti hozzáférés sel együtt. CoreAdmins és EmergencyAccess kizárása
  * Felhőalkalmazások: Exchange Online és SharePoint Online
  * Feltételek: Bármilyen
  * Támogatás-vezérlés: Tartományegyesítés megkövetelése
  * Állapot: Letiltva
* 2. házirend: A Windowsrendszeren kívüli platformok blokkolása
  * Név: EM002 - ENABLE IN EMERGENCY: MFA Disruption[2/4] - Exchange SharePoint - Hozzáférés blokkolása a Windows kivételével
  * Felhasználók és csoportok: Az összes felhasználó felvétele. CoreAdmins és EmergencyAccess kizárása
  * Felhőalkalmazások: Exchange Online és SharePoint Online
  * Feltételek: Az eszközplatform tartalmazza az összes platformot, a Windows kizárása
  * Grant Control: Blokk
  * Állapot: Letiltva
* 3. házirend: A CorpNetwork-en kívüli hálózatok blokkolása
  * Név: EM003 - ENABLE IN EMERGENCY: MFA Disruption[3/4] - Exchange SharePoint - Hozzáférés blokkolása a vállalati hálózat kivételével
  * Felhasználók és csoportok: Az összes felhasználó felvétele. CoreAdmins és EmergencyAccess kizárása
  * Felhőalkalmazások: Exchange Online és SharePoint Online
  * Feltételek: Helyek: Tartalmazza a helyet, zárja ki corpnetwork
  * Grant Control: Blokk
  * Állapot: Letiltva
* 4. házirend: Az EAS letiltása kifejezetten
  * Név: EM004 - ENABLE IN EMERGENCY: MFA Disruption[4/4] - Exchange - Block EAS minden felhasználó számára
  * Felhasználók és csoportok: Az összes felhasználó felvétele
  * Felhőalapú alkalmazások: Az Exchange Online felvétele
  * Feltételek: Ügyfélalkalmazások: Exchange Active Sync
  * Grant Control: Blokk
  * Állapot: Letiltva

Aktiválási sorrend:

1. A készenléti hozzáférés, a CoreAdmins és a EmergencyAccess kizárása a meglévő MFA-házirendből. Ellenőrizze, hogy a ContingencyAccess egyik felhasználója hozzáférhet-e a SharePoint Online-hoz és az Exchange Online-hoz.
2. 1. házirend engedélyezése: Ellenőrizze, hogy a tartományhoz csatlakozott eszközökön lévő felhasználók, akik nem tartoznak a kizárási csoportokhoz, hozzáférhetnek az Exchange Online-hoz és a SharePoint Online-hoz. Ellenőrizze, hogy a Kizárás csoport felhasználói bármilyen eszközről hozzáférhetnek a SharePoint Online-hoz és az Exchange-hez.
3. 2. házirend engedélyezése: Ellenőrizze, hogy a kizárási csoporthoz nem eső felhasználók nem tudnak-e eljutni a SharePoint Online-hoz és az Exchange Online-hoz a mobileszközeikről. Ellenőrizze, hogy a Kizárás csoport felhasználói bármely eszközről (Windows/iOS/Android) hozzáférhetnek-e a SharePoint hoz és az Exchange-hez.
4. Házirend engedélyezése 3: Ellenőrizze, hogy a kizárási csoportba nem eső felhasználók nem férhetnek hozzá a SharePoint hoz és az Exchange-hez a vállalati hálózaton kívül, még tartományhoz csatlakozó számítógép esetén sem. Ellenőrizze, hogy a Kizárás csoport felhasználói bármely hálózatról hozzáférhetnek-e a SharePointhoz és az Exchange-hez.
5. 4. házirend engedélyezése: Ellenőrizze, hogy minden felhasználó nem tudja-e beszerezni az Exchange Online-t a natív levelezőalkalmazásokból mobileszközökön.
6. Tiltsa le a SharePoint Online és az Exchange Online meglévő Többfa-házirendet.

Ebben a következő példában **a B példa – A Salesforce mobilhozzáférés-hozzáférésének engedélyezésére vonatkozó, készenléti hitelesítésszolgáltatói házirendek**visszaállításra kerül. Ebben a forgatókönyvben az ügyfél általában megköveteli, hogy az értékesítési alkalmazottak hozzáférést Salesforce (konfigurálva egyszeri bejelentkezés az Azure AD) a mobileszközökről csak akkor engedélyezhető a megfelelő eszközökről. A zavar ebben az esetben az, hogy van egy probléma az eszköz megfelelőségének kiértékelése, és a kimaradás történik egy érzékeny időpontban, amikor az értékesítési csapat nak szüksége van a Salesforce-hoz az ügyletek lezárásához. Ezek a vészhelyzeti házirendek hozzáférést biztosítanak a kritikus felhasználók számára a Salesforce-hoz egy mobileszközről, hogy továbbra is lezárhassák az ügyleteket, és ne zavarhassák meg az üzletet. Ebben a példában **a SalesforceContingency** tartalmazza az összes értékesítési alkalmazottat, akiknek meg kell őrizniük a hozzáférést, a **SalesAdmins pedig** a Salesforce szükséges rendszergazdáit.

**B példa – Rendkívüli hitelesítésbiztosítási házirendek:**

* 1. irányelv: A SalesContingency csapatban nem mindenki letiltása
  * Név: EM001 - ENABLE IN EMERGENCY: Device Compliance Disruption[1/2] - Salesforce - Block All users except SalesforceContingency
  * Felhasználók és csoportok: Az összes felhasználó felvétele. SalesAdmins és SalesforceContingency kizárása
  * Felhőalkalmazások: Salesforce.
  * Feltétel: nincs
  * Grant Control: Blokk
  * Állapot: Letiltva
* 2. házirend: Az értékesítési csapat letiltása a mobiltól eltérő platformról (a támadás felületének csökkentése érdekében)
  * Név: EM002 - ENABLE IN EMERGENCY: Device Compliance Disruption[2/2] - Salesforce - Block All platforms except iOS and Android
  * Felhasználók és csoportok: SalesforceContingency-vel együtt. SalesAdmins kizárása
  * Felhőalkalmazások: Salesforce
  * Feltételek: Az eszközplatform tartalmazza az összes platformot, kivéve az iOS-t és az Androidot
  * Grant Control: Blokk
  * Állapot: Letiltva

Aktiválási sorrend:

1. A SalesAdmins és a SalesforceContingency kizárása a Salesforce meglévő eszközmegfelelőségi szabályzatából. Ellenőrizze, hogy a SalesforceContingency csoport egyik felhasználója hozzáférhet-e a Salesforce-hoz.
2. 1. házirend engedélyezése: Ellenőrizze, hogy a SalesContingency-en kívüli felhasználók nem férhetnek-e hozzá a Salesforce-hoz. Ellenőrizze, hogy a SalesAdmins és a SalesforceContingency felhasználói hozzáférhetnek-e a Salesforce-hoz.
3. 2. házirend engedélyezése: Ellenőrizze, hogy a SalesContingency csoport felhasználói nem férhetnek hozzá a Salesforce-hoz windowsos/Mac-es laptopjukról, de továbbra is hozzáférhetnek a mobileszközeikről. Ellenőrizze, hogy a SalesAdmin továbbra is hozzáférhet-e a Salesforce-hoz bármilyen eszközről.
4. Tiltsa le a Salesforce meglévő eszközmegfelelőségi házirendjét.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Jelszókivonat-szinkronizálás telepítése akkor is, ha össze van igazolva, vagy átmenő hitelesítést használ

A felhasználói zárolás akkor is előfordulhat, ha a következő feltételek teljesülnek:

- A szervezet egy hibrid identitáskezelési megoldást használ átmenő hitelesítéssel vagy összevonással.
- A helyszíni identitáskezelő rendszerek (például az Active Directory, az AD FS vagy egy függő összetevő) nem érhetők el. 
 
Ahhoz, hogy rugalmasabb legyen, a szervezetnek engedélyeznie kell a [jelszókivonat-szinkronizálást,](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)mivel lehetővé teszi, [hogy jelszókivonat-szinkronizálás használatával váltson át,](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) ha a helyszíni identitásrendszerek nem működik.

#### <a name="microsoft-recommendations"></a>A Microsoft javaslatai
 Engedélyezze a jelszókivonat-szinkronizálást az Azure AD Connect varázslóval, függetlenül attól, hogy a szervezet összevonási vagy átadó hitelesítést használ-e.

>[!IMPORTANT]
> Nem szükséges a felhasználókat összevontról felügyelt hitelesítéssé konvertálni jelszókivonat-szinkronizálás használatára.

## <a name="during-a-disruption"></a>Megszakítás esetén

Ha úgy döntött, hogy egy kockázatcsökkentési terv, akkor képes lesz arra, hogy automatikusan túlélni egy hozzáférés-vezérlési megszakítás. Ha azonban úgy döntött, hogy vészhelyzeti tervet hoz létre, a hozzáférés-vezérlés megszakítása során aktiválhatja a vészhelyzeti szabályzatokat:

1. Engedélyezze a vészhelyzeti házirendeket, amelyek célzott felhasználóknak hozzáférést biztosítanak bizonyos alkalmazásokhoz, adott hálózatokról.
2. Tiltsa le a szokásos, vezérlésalapú házirendeket.

### <a name="microsoft-recommendations"></a>A Microsoft javaslatai

Attól függően, hogy mely kockázatcsökkentési vagy vészhelyzeti megoldásokat használja a rendszer a megszakítás során, a szervezet csak jelszavakkal kaphat hozzáférést. A biztosíték hiánya nem jelent jelentős biztonsági kockázatot, amelyet gondosan mérlegelni kell. A szervezeteknek:

1. A változásvezérlési stratégia részeként dokumentáljon minden változást és az előző állapotot, hogy a hozzáférés-vezérlés teljes működése után visszatudja hajtani a végrehajtott váratlan eseményeket.
2. Tegyük fel, hogy a rosszindulatú szereplők jelszóspray- vagy adathalász támadásokkal kísérlik meg betakarítani a jelszavakat, miközben letiltja az MFA-t. Emellett előfordulhat, hogy a hibás szereplők már rendelkeznek olyan jelszóval, amely korábban nem adott hozzáférést az ebben az ablakban megkísérelhető erőforrásokhoz. A kritikus felhasználók, például a vezetők, részben csökkentheti ezt a kockázatot a jelszavak alaphelyzetbe állításával, mielőtt letiltja az MFA számukra.
3. Archiválja az összes bejelentkezési tevékenységet, hogy azonosíthassa, ki mit érhet el az MFA letiltása alatt.
4. Az ebben az időszakban [jelentett összes kockázatészlelés tanuskolása.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)

## <a name="after-a-disruption"></a>A zavar után

Vonja vissza az aktivált vészhelyzeti terv részeként végrehajtott módosításokat, miután a szolgáltatás visszaállítása okozta a fennakadást. 

1. A szokásos házirendek engedélyezése
2. Tiltsa le a vészhelyzeti házirendeket. 
3. A megszakítás során végrehajtott és dokumentált egyéb módosítások visszaállítása.
4. Ha vészelérési fiókot használt, ne felejtse el újragenerálni a hitelesítő adatokat, és fizikailag biztonságossá tenni az új hitelesítő adatokat a vészelérési fiók eljárásai nak részeként.
5. Folytassa a gyanús tevékenység zavara után [jelentett összes kockázatészlelés](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) osztályozását.
6. Vonja vissza a [PowerShell használatával](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) a felhasználók megcélzásához kiadott összes frissítési jogkivonatot. Az összes frissítési jogkivonat visszavonása fontos a megszakítás során használt kiemelt fiókok számára, és ezzel kényszeríti őket, hogy újra hitelesítsék magukat, és megfeleljenek a visszaállított szabályzatok vezérlésének.

## <a name="emergency-options"></a>Vészhelyzeti beállítások

 Vészhelyzet esetén, és a szervezet korábban nem valósított meg egy kockázatcsökkentési vagy vészhelyzeti tervet, majd kövesse a javaslatokat a [vészhelyzetek](#contingencies-for-user-lockout) a felhasználói zárolás szakaszban, ha már feltételes hozzáférési házirendek kényszerítése MFA.
Ha a szervezet felhasználónkénti MFA örökölt házirendeket használ, akkor a következő alternatívát veheti figyelembe:

1. Ha rendelkezik a vállalati hálózat kimenő IP-címével, hozzáadhatja őket megbízható IP-címként, hogy csak a vállalati hálózaton engedélyezze a hitelesítést.
   1. Ha nem rendelkezik a kimenő IP-címek leltárával, vagy ha engedélyeznie kell a hozzáférést a vállalati hálózaton belül és kívül, a 0.0.0.0/1 és a 128.0.0.0/1 megadásával hozzáadhatja a teljes IPv4-címterületet megbízható IP-címként.

>[!IMPORTANT]
 > Ha a hozzáférés feloldásához kiszélesíti a megbízható IP-címeket, a rendszer nem hoz létre az IP-címekhez (például lehetetlen utazáshoz vagy ismeretlen helyekhez) társított kockázatészleléseket.

>[!NOTE]
 > Az Azure MFA [megbízható IP-k](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) konfigurálása csak [az Azure AD Premium-licencekkel](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing)érhető el.

## <a name="learn-more"></a>Részletek

* [Az Azure AD-hitelesítés dokumentációja](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Vészelérési felügyeleti fiókok kezelése az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Elnevezett helyek konfigurálása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [A hibrid Azure Active Directoryhoz csatlakozó eszközök konfigurálása](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Vállalati Windows Hello – Üzembehelyezési útmutató](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Jelszóútmutató - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Milyen feltételek et az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Mik a hozzáférés-vezérlések az Azure Active Directory feltételes hozzáférésében?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
