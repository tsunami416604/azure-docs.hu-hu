---
title: Hozzon létre egy rugalmas hozzáférés-vezérlési felügyeleti stratégia az Azure Active Directoryval
description: Ez a dokumentum biztosít egy szervezet stratégiák útmutatást biztosít hibatűrést során váratlan megszakadását fiókzárolási kockázatának csökkentése érdekében el kell fogadnia
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5fb263819a5bb96175f636f53a16c28649a3f39
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339549"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Hozzon létre egy rugalmas hozzáférés-vezérlési felügyeleti stratégia az Azure Active Directoryval

>[!NOTE]
> Ebben a dokumentumban található információk a tárgyalt kezdődően tükrözi a Microsoft Corporation az aktuális nézet jelöli. A Microsoft piaci feltételek módosítása kell válaszolni, mivel nem értelmezhetők a Microsoft elkötelezettségét lennie, és a Microsoft nem garantálja a tükrözi után információk pontosságát.

Egy egyetlen hozzáférés-vezérlés, például a többtényezős hitelesítés (MFA) vagy egy egyetlen hálózati helyet, az informatikai rendszerek védelméhez, amelyek ki vannak téve az alkalmazásokhoz és erőforrásokhoz való hozzáférés sikertelen, ha egyetlen hozzáférés-vezérlést elérhetetlenné válik. vagy hibásan van konfigurálva. Például természeti katasztrófa esetén a nagy szegmenseket távközlési infrastruktúra vagy a vállalati hálózatok elérhetetlenné eredményezhet. Egy bekövetkező szolgáltatáskimaradás megakadályozhatja a végfelhasználók és rendszergazdák nem tudnak bejelentkezni.

Ez a dokumentum biztosít egy szervezet stratégiák útmutatást biztosít hibatűrést fiókzárolási kockázatának csökkentése érdekében a következő forgatókönyveket az előre nem látható fennakadások során el kell fogadnia:

 1. Szervezetek növelheti a hardverhibák rugalmas kezelése fiókzárolási kockázatának csökkentése érdekében **egy megszakítása előtt** kockázatcsökkentési stratégia vagy a VÁLSÁGTERVEK alkalmazásával.
 2. Szervezetek továbbra is hozzáférhetnek az alkalmazásokhoz és erőforrásokhoz választ **egy megszakítása során** kockázatcsökkentési stratégia és a VÁLSÁGTERVEK helyen megadásával.
 3. Szervezetek győződjön meg arról, hogy azok megőrizni az adatokat, például naplók, **egy megszakadása után** , mielőtt azok visszaállítása bármely azok megvalósított ágba.
 4. Lehetséges, hogy a szervezetek számára, amelyek még nem megvalósított megelőzési stratégiák vagy alternatív tervek tudja megvalósítása **vészhelyzeti beállítások** foglalkozni a megszakítások időtartamát.

## <a name="key-guidance"></a>Fő útmutató

Ez a dokumentum négy fő takeaways szerepelnek:

* Kerülje a rendszergazdai fiókzárolási vészelérési fiókok használatával.
* Az MFA felhasználónkénti MFA Hitelesítést ahelyett, hogy a feltételes hozzáférés (CA) használatával hajtja végre.
* Csökkenti a felhasználó zárolási több feltételes hozzáférés (CA) vezérlők használatával.
* Csökkenti a felhasználó zárolási több hitelesítési módszert, vagy minden felhasználó számára megfelelő kiépítésével.

## <a name="before-a-disruption"></a>Mielőtt egy megszakítása

Egy tényleges megszakítás csökkentése kell lennie a szervezet elsődleges fókusz foglalkozó access control esetleg felmerülő problémákat. Csökkentése mellett tartalmazza tervezése az események tényleges végrehajtási stratégia, győződjön meg arról, hogy hozzáférés-vezérlés és műveletek során fennakadások érinti.

### <a name="why-do-you-need-resilient-access-control"></a>Miért van szükség a rugalmas hozzáférés-vezérlés?

 Identitás, a vezérlősík az alkalmazásokhoz és erőforrásokhoz hozzáférő felhasználók. A identitáskezelő rendszerbe azt szabályozza, hogy mely felhasználók mely körülmények között, például a hozzáférés-vezérlés vagy hitelesítési követelmények, a felhasználók pedig az alkalmazásokhoz való hozzáférés. Ha egy vagy több hitelesítési vagy hozzáférés vezérlésre vonatkozó követelményeket nem érhetők el a felhasználók hitelesítése miatt előre nem látható körülmények, szervezetek tapasztalhatnak, a következő problémák legalább egyikét:

* **Felügyeleti zárolás:** A rendszergazdák a bérlői vagy szolgáltatás nem tudja kezelni.
* **Felhasználói fiókzárolási:** Felhasználók nem férhetnek hozzá, alkalmazások és erőforrások.

### <a name="administrator-lockout-contingency"></a>Felügyeleti zárolás contingency

A vészelérési fiókok zárolásának feloldása a bérlői rendszergazdai hozzáféréssel, kell létrehoznia. Ezek a vészelérési fiókok, más néven *vészhelyzeti* fiókokat, kezelheti az Azure AD konfigurációját, amikor nem érhetők el, normál jogosultsági szintű fiókot hozzáférési eljárásainak való hozzáférés engedélyezése. A következő legalább két vészelérési fiókok kell létrehozni a [vészelérési tárfiókokra vonatkozó javaslatok]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Felhasználói fiókzárolási csökkentése

 A kockázatát, hogy a felhasználó zárolási, több vezérlőkkel feltételes hozzáférési szabályzatok használatával engedélyezheti a felhasználók milyen lesz hozzáférésük alkalmazások és erőforrások közül választhat. Így a felhasználók közötti választás, ha például bejelentkezik egy MFA **vagy** jelentkezik be egy felügyelt eszközről **vagy** jelentkezik be a vállalati hálózathoz, ha a hozzáférés-vezérlés egyike nem érhető el a felhasználó rendelkezik-e egyéb beállítások továbbra is működik.

#### <a name="microsoft-recommendations"></a>Az ajánlott eljárásokat

Építse be a következő hozzáférés-vezérlést a szervezet számára a meglévő feltételes hozzáférési szabályzatokat:

1. Üzembe helyezheti az egyes felhasználók több olyan hitelesítési módszereket, amelyek különböző kommunikációs csatornákról, például a Microsoft Authenticator alkalmazás (internetes), OATH token (létrehozott eszközön futó) és (telephonic) SMS támaszkodnak.
2. Üzembe helyezése Windows Hello for Business MFA megfelelnek a közvetlenül az eszköz-bejelentkezés a Windows 10 rendszerű eszközökön.
3. Használja a megbízható eszközök keresztül [a hibrid Azure AD Join](https://docs.microsoft.com/azure/active-directory/devices/overview) vagy [a Microsoft Intune-ban kezelt eszközök](https://docs.microsoft.com/intune/planning-guide). Megbízható eszközök fog felhasználói élmény javítása, mert maga a megbízható eszköz képes kielégíteni az MFA-hitelesítést, a felhasználó nélküli szabályzat erős hitelesítés követelményeinek. Új eszköz regisztrálása során, és a nem megbízható eszközök alkalmazások és erőforrások elérése az MFA majd lesz szükség.
4. Használja az Azure AD identity protection kockázatalapú szabályzatok, amelyek megakadályozzák a hozzáférést, ha a felhasználó bejelentkezési vagy regisztrációs rögzített MFA-házirendek helyett veszélyben van.

>[!NOTE]
> Kockázatalapú szabályzatok szükséges [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) licenceket.

A következő példa ismerteti a házirendeket, létre kell hoznia egy felhasználó hozzáférhessen a saját alkalmazások és erőforrások rugalmas hozzáférés-vezérlést biztosít. Ebben a példában egy biztonsági csoportot kell **AppUsers** hozzáférést biztosít a kívánt cél felhasználókkal csoport nevű **CoreAdmins** a core-rendszergazdák és nevű csoport  **EmergencyAccess** a vészelérési fiókok együtt.
Ebben a példában szabályzatkészlet biztosít a kiválasztott felhasználók **AppUsers**, a kijelölt alkalmazások elérését, ha egy megbízható eszközről csatlakozik, vagy adja meg az erős hitelesítés, például a többtényezős hitelesítés. Nem tartalmazza a vészelérési fiókok és a core-rendszergazdák.

**Állítsa be a hitelesítésszolgáltató kockázatcsökkentési házirendek:**

* 1. szabályzat: Célcsoportok kívüli személyek való hozzáférés letiltása
  * Felhasználók és csoportok: Minden felhasználónak bele. AppUsers CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Tartalmazza az összes alkalmazás
  * Feltételek: (nincs)
  * Hozzáférés megadása: Blokk
* 2. szabályzat: MFA vagy megbízható eszköz igénylő AppUsers hozzáférést biztosít.
  * Felhasználók és csoportok: AppUsers tartalmazza. CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Tartalmazza az összes alkalmazás
  * Feltételek: (nincs)
  * Hozzáférés megadása: Hozzáférés biztosítása, többtényezős hitelesítés megkövetelése, a megfelelő eszköz megkövetelése. Több vezérlő: A kijelölt feltételek egyikének megkövetelése.

### <a name="contingencies-for-user-lockout"></a>A felhasználó zárolási ágakba

Másik lehetőségként a szervezet készenléti házirendeket is létrehozhat. A készenléti szabályzatok létrehozásához, meg kell határoznia egyensúlyt a feltételek között üzletmenet-folytonossági, a üzemeltetési költségek, a pénzügyi költségek és a biztonsági kockázatokat. Például aktiválhatja a készenléti szabályzat csak a felhasználók, alkalmazások, az ügyfelek vagy a helyek egy része egy részhalmaza számára részéhez egy részét. Feltételes házirendek a rendszergazdák és a végfelhasználók számára hozzáférést biztosít alkalmazások és erőforrások, egy megszakadása, amikor nincs kockázatcsökkentési metódus implementálása során.
A fenyegetéseknek való ismertetése során egy bekövetkező szolgáltatáskimaradás segít csökkenteni a kockázatot, és a egy kritikus részét képezi a tervezési folyamat során. A készenléti tervet létrehozni, először határozza meg a szervezet a következő üzleti követelményeknek:

1. Az üzletmenet szempontjából kritikus fontosságú alkalmazások előre határozza meg: Mik azok az, hogy meg kell hozzáférést biztosít a, akár egy kisebb kockázat és biztonsági állapotáról az alkalmazások? Ezek az alkalmazások listájának létrehozása, és győződjön meg arról, hogy a más érdekelt felek (vállalati, biztonság, jogi, vezetői) összes egyetértenek abban, hogy minden hozzáférés-vezérlés ami újbóli próbálkozással megszűnik, ha ezek az alkalmazások továbbra is továbbra is futnia kell. Valószínűleg fog célból a kategóriák:
  * **A kategória 1 működés szempontjából kritikus fontosságú alkalmazások** több percig, például olyan alkalmazásokat, amelyek közvetlenül nincsenek hatással a bevételt, a szervezet, amely nem lehet nem érhető el.
  * **2. kategória fontos alkalmazások** , hogy az üzlet néhány órán belül elérhetők lesznek.
  * **3. kategória alacsony prioritású alkalmazások** , amely képes elviselni egy pár nappal megszakadását.
2. 1. és 2 kategóriába tartozó alkalmazások esetében a Microsoft azt javasolja, előre megterveznie, milyen típusú hozzáférési szintet szeretné engedélyezni:
  * Biztos, hogy a teljes hozzáféréssel vagy korlátozott munkamenet, például korlátozza a letöltéseket?
  * Biztosan engedélyezi a hozzáférést az alkalmazáshoz, de nem a teljes alkalmazás részére?
  * Biztosan information worker hozzáférés engedélyezéséhez és letiltásához a rendszergazdai hozzáférés, a hozzáférés-vezérlést visszaállításáig?
3. Ezeket az alkalmazásokat a Microsoft azt javasolja azt tervezi, hogy melyik rések leggyakoribb okai az access, szándékosan megnyílik, és bezárul, melyiket:
  * Biztosan engedélyezi a böngésző csak hozzáférési és letiltása olyan funkciógazdag ügyfeleket, amelyek a lehet offline adatokat?
  * Biztosan hozzáférés engedélyezése csak a felhasználók a vállalati hálózaton belüli és kívüli felhasználók blokkolt?
  * Biztosan csak a megszakítás alatt engedélyezze a hozzáférést bizonyos országokban vagy régiókban?
  * Biztosan szabályzatokat a feltételes szabályzatokat, különösen a működés szempontjából kritikus fontosságú alkalmazások, sikertelen vagy sikeres, ha nem érhető el egy alternatív hozzáférés-vezérlés?

#### <a name="microsoft-recommendations"></a>Az ajánlott eljárásokat

A készenléti feltételes hozzáférési szabályzat van egy **le van tiltva a szabályzat** , amely az áttekinthetőség kedvéért kihagyja az Azure MFA, külső MFA, kockázatalapú vagy eszközalapú szabályozza. Ezt követően a szervezet úgy dönt, hogy aktiválja a készenléti tervét, amikor a rendszergazdák is a szabályzat szabályzatok engedélyezése és letiltása a reguláris vezérlőelem-alapú.

>[!IMPORTANT]
> Letiltja a házirendekben, amelyek a felhasználók a biztonság kényszerítése, még ideiglenesen csökkenti meg biztonsági állapotát a válságtervet pedig a helyen.

* Annak konfigurálása, tartalék házirendjei zavar egy hitelesítőadat-típus vagy egy hozzáférést vezérlő mechanizmus hatások hozzáférés az alkalmazásokhoz. Letiltott állapotban, amely megköveteli a tartományhoz való csatlakozás vezérlőként, olyan aktív szabályzat, amely igényel egy külső MFA-szolgáltató a biztonsági szabályzat konfigurálása.
* Kártékony elemek találgatás jelszavak, amikor a többtényezős hitelesítés nem szükséges, a eljárások követésével kockázatának csökkentése érdekében a [tájékoztató a jelszavakról](https://aka.ms/passwordguidance) tanulmányt.
* Üzembe helyezése [az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) és [Azure AD jelszóvédelem](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) , hogy a felhasználók ne használjon közös jelszó és a használati úgy dönt, hogy letiltása.
* Ezen szabályzatok, amelyek korlátozzák a hozzáférést az alkalmazásokon belüli, ha egy bizonyos hitelesítési szintje nem éri el helyett egyszerűen naplókimenetet teljes hozzáférést. Példa:
  * Konfigurálja a biztonsági mentési szabályzatot, amely az Exchange és SharePoint korlátozott munkamenet jogcímet küld.
  * Ha szervezete a Microsoft Cloud App Security, fontolja meg a Visszalépés az olyan szabályzatot, amely megkezdi az MCAS, majd az MCAS lehetővé teszi, hogy csak olvasási hozzáféréssel, de nem tölt fel.
* Nevezze el a szabályzatokhoz, győződjön meg arról, hogy könnyen megtalálhassa azokat egy megszakítása során. A szabályzat neve a következő elemeket tartalmazza:
  * A *szám címke* a szabályzathoz.
  * Szöveg megjelenítése, ez a szabályzat csak vészhelyzetek szól. Példa: **VÉSZHELYZET ENGEDÉLYEZÉSE**
  * A *megszakítás* vonatkozik. Példa: **MFA megszakítása során**
  * A *sorszámú* az sorrend aktiválnia kell a házirendeket.
  * A *alkalmazások* vonatkozik.
  * A *vezérlők* érvényes lesz.
  * A *feltételek* van szükség.
  
Az elnevezési szabványnak, a feltételes szabályzatokat a következő lesz: 

`
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
`

Az alábbi példában: **A példában A - hozzáférés alapvető fontosságú csoportmunka-alkalmazás visszaállításához Contingency hitelesítésszolgáltató házirend**, a jellemző vállalati vészhelyzeti megoldásként alkalmazza. Ebben a forgatókönyvben a szervezet általában többtényezős Hitelesítést követel meg az összes Exchange online-hoz és a SharePoint online-hoz, és a megszakítások időtartamát ebben az esetben az MFA szolgáltatót, az ügyfél rendelkezik esetére (akár az Azure MFA, a helyszínen az MFA szolgáltatót, és a külső MFA). Ez a szabályzat csökkenti a szolgáltatáskimaradás azáltal, hogy adott megcélzott felhasználók hozzáférési ezekre az alkalmazásokra megbízható Windows-eszközök csak akkor, ha az alkalmazás megbízható vállalati hálózatról érnek el. Ezek a korlátozások a is vészelérési fiókok és a rendszergazdák core kizárja. A megcélzott felhasználók lesz majd hozzáférni Exchange online-hoz és a SharePoint Online, amíg más felhasználók továbbra is nem hozzáférhetnek a leállás miatt az alkalmazásokra. Ebben a példában egy olyan nevesített hálózati helyre van szükség **CorpNetwork** és a egy biztonsági csoportot **ContingencyAccess** cél felhasználóival, a csoport neve **CoreAdmins** -a Core-rendszergazdák és a egy nevű csoport **EmergencyAccess** a vészelérési fiókok együtt. Az eseti, adja meg a kívánt hozzáférési négy szabályzatokra van szüksége. 

**A - hitelesítésszolgáltató Contingency szabályzatok hozzáférés alapvető fontosságú csoportmunka-alkalmazás visszaállításához. példa:**

* 1. szabályzat: Az Exchange és SharePoint megkövetelése a tartományhoz csatlakozott eszközökkel
  * Név: EM001 - VÉSZHELYZET ENGEDÉLYEZÉSE: MFA megszakítás [1/4] – Exchange, SharePoint – hibrid Azure AD-csatlakozás megkövetelése
  * Felhasználók és csoportok: ContingencyAccess tartalmazza. CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Az Exchange Online és SharePoint Online
  * Feltételek: Bármelyik
  * Hozzáférés megadása: Tartományhoz csatlakoztatott megkövetelése
  * Állapot: Letiltva
* 2. szabályzat: A platformokat tiltsa le a nem Windows
  * Név: EM002 - VÉSZHELYZET ENGEDÉLYEZÉSE: MFA megszakítás [2 vagy 4] hozzáférés – Exchange, SharePoint - letiltása Windows kivételével
  * Felhasználók és csoportok: Minden felhasználónak bele. CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Az Exchange Online és SharePoint Online
  * Feltételek: Platform tartalmazza az összes platformot is, a Windows kizárása
  * Hozzáférés megadása: Blokk
  * Állapot: Letiltva
* 3. szabályzat: CorpNetwork hálózatokra letiltása
  * Név: EM003 - VÉSZHELYZET ENGEDÉLYEZÉSE: MFA megszakítás [3/4] hozzáférés – Exchange, SharePoint - letiltása vállalati hálózaton kívül
  * Felhasználók és csoportok: Minden felhasználónak bele. CoreAdmins és EmergencyAccess kizárása
  * Felhőalapú alkalmazások: Az Exchange Online és SharePoint Online
  * Feltételek: Helyek tartalmazza a bármilyen olyan helyre, CorpNetwork kizárása
  * Hozzáférés megadása: Blokk
  * Állapot: Letiltva
* 4. szabályzat: EAS explicit módon letiltása
  * Név: EM004 - VÉSZHELYZET ENGEDÉLYEZÉSE: MFA megszakítás [4/4] – az Exchange - blokk EAS az összes felhasználó számára
  * Felhasználók és csoportok: Minden felhasználónak bele
  * Felhőalapú alkalmazások: Az Exchange Online belefoglalása
  * Feltételek: Ügyfélalkalmazások: Az Exchange Active Sync szolgáltatással
  * Hozzáférés megadása: Blokk
  * Állapot: Letiltva

Az aktiválás sorrend:

1. Zárja ki a meglévő többtényezős hitelesítési szabályzat ContingencyAccess CoreAdmins és EmergencyAccess. Ellenőrizze, hogy egy felhasználó a ContingencyAccess férhet hozzá a SharePoint Online és Exchange online-hoz.
2. 1 házirend engedélyezése: Ellenőrizze a tartományhoz csatlakozott eszközökkel, akik nem tagjai a csoportok kizárása felhasználók érhessék el az Exchange online-hoz és a SharePoint online-hoz. Ellenőrizze, hogy a kizárási csoport is a SharePoint Online és Exchange bármely eszközről elérhetők.
3. Szabályzat 2 engedélyezése: Ellenőrizze, hogy azok a felhasználók, akik nem tagjai a kizárási csoport nem olvasható be a SharePoint Online és Exchange online-hoz a mobileszközükről. Ellenőrizze, hogy a kizárási csoport férhet hozzá a SharePoint és Exchange bármilyen eszközről (Windows és iOS/Android).
4. 3 házirend engedélyezése: Ellenőrizze a felhasználók, akik nem tagjai a kizárási csoportokat nem fér hozzá a SharePoint és Exchange ki a vállalati hálózathoz, még a tartományhoz csatlakoztatott gép. Ellenőrizze, hogy a kizárási csoport férhet hozzá a SharePoint és Exchange bármely hálózatból.
5. 4 házirend engedélyezése: Ellenőrizze, hogy minden felhasználó nem tud letölteni az Exchange online-hoz a natív e-mail alkalmazásokat a mobileszközökön.
6. Tiltsa le a meglévő többtényezős hitelesítési szabályzat a SharePoint Online és Exchange online-hoz.

A következő példában **példa B - Contingency feltételes hozzáférési szabályzatok lehetővé teszik a mobil hozzáférés a Salesforce-hoz való**, egy üzleti alkalmazás-hozzáférés visszaállítása. Ebben a forgatókönyvben az ügyfél általában az értékesítési alkalmazottak hozzáférést igényel a Salesforce-hoz (konfigurált az egyszeri bejelentkezés az Azure AD) a mobileszközöket arra, hogy csak megfelelő eszközökről engedélyezi. A megszakítások időtartamát ebben az esetben, hogy az eszközök megfelelőségének értékelése probléma és bizalmas egyszerre történik a szolgáltatáskiesés megszüntetése után, az értékesítési csapat igényeinek gombra kattintva zárja be az üzletek a Salesforce-hoz való hozzáférés. Ezek a szabályzatok a készenléti kritikus felhasználók hozzáférést biztosít a Salesforce-hoz egy mobileszközzel, hogy továbbra is üzletek bezárásához, és nem zavarja az üzleti. Ebben a példában **SalesforceContingency** tartalmaz minden értékesítési alkalmazottak, akik meg kell őriznie a hozzáférési és **SalesAdmins** Salesforce-hoz szükséges rendszergazdái tartalmazza.

**B - hitelesítésszolgáltató Contingency házirendek. példa:**

* 1. szabályzat: Nem található a SalesContingency csapat mindenki letiltása
  * Név: EM001 - VÉSZHELYZET ENGEDÉLYEZÉSE: Eszköz megfelelőségi megszakítás [1/2] – a Salesforce - kivételével SalesforceContingency letiltása minden felhasználó
  * Felhasználók és csoportok: Minden felhasználónak bele. SalesAdmins és SalesforceContingency kizárása
  * Felhőalapú alkalmazások: Salesforce-ban.
  * Feltételek: None
  * Hozzáférés megadása: Blokk
  * Állapot: Letiltva
* 2. szabályzat: Az értékesítési csoportnak bármilyen platformon kívül (a támadási felületének csökkentése érdekében) mobile letiltása
  * Név: EM002 - VÉSZHELYZET ENGEDÉLYEZÉSE: Eszköz megfelelőségi megszakítása: [2/2] - Salesforce - letiltása minden platformhoz, kivéve az iOS és Android rendszerhez
  * Felhasználók és csoportok: SalesforceContingency tartalmazza. SalesAdmins kizárása
  * Felhőalapú alkalmazások: Salesforce
  * Feltételek: Platform tartalmazza az összes platformot is, zárja ki az iOS és Android rendszerhez
  * Hozzáférés megadása: Blokk
  * Állapot: Letiltva

Az aktiválás sorrend:

1. Zárja ki SalesAdmins és SalesforceContingency a már létező eszközmegfelelőségi szabályzat a Salesforce-hoz. Ellenőrizze, hogy egy felhasználó a SalesforceContingency csoport hozzáférhet a Salesforce-ban.
2. 1 házirend engedélyezése: Ellenőrizze, hogy SalesContingency kívüli felhasználók nem férhetnek hozzá a Salesforce-ban. Ellenőrizze a SalesAdmins felhasználóinak és SalesforceContingency férhet hozzá a Salesforce.
3. Szabályzat 2 engedélyezése: Győződjön meg arról, SalesContingency csoportnak Windows/Mac irodába férhessenek hozzá a Salesforce, de továbbra is elérheti a mobileszközükről. Ellenőrizze, hogy SalesAdmin továbbra is elérheti a Salesforce bármilyen eszközről.
4. Tiltsa le a már létező eszközmegfelelőségi szabályzat, a Salesforce-hoz.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Jelszókivonat-szinkronizálás telepítése, még akkor is, ha összevont vagy átmenő hitelesítés használata

Felhasználói fiókzárolási akkor is előfordulhat, ha az alábbi feltételek teljesülése esetén:

- A szervezet az átmenő hitelesítés vagy összevonás hibrid identitáskezelési megoldás használja.
- A helyszíni identitáskezelési rendszereket (mint például az Active Directory, az AD FS vagy egy függő összetevőt) nem érhetők el. 
 
Ahhoz, hogy rugalmasabb, a szervezet kell [Jelszókivonat-szinkronizálás engedélyezése](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), mert lehetővé teszi a [átvált a Jelszókivonat-szinkronizálás](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) le a helyszíni identitáskezelési rendszerek esetén.

#### <a name="microsoft-recommendations"></a>Az ajánlott eljárásokat
 Engedélyezze a Jelszókivonat-szinkronizálás a az Azure AD Connect varázsló segítségével, függetlenül attól, hogy a szervezet összevonási vagy átmenő hitelesítéssel használja-e.

>[!IMPORTANT]
> Nem szükséges a felhasználók átalakítása összevont Jelszókivonat-szinkronizálás használata felügyelt hitelesítésre.

## <a name="during-a-disruption"></a>A megszakítás alatt

Ha úgy döntött, kockázatcsökkentési terv megvalósításához, lesz automatikusan stabilitást biztosít az egyszeri hozzáférést vezérlő zavar. Azonban ha úgy döntött, készenléti tervet létrehozni, azt fogja tudni aktiválhatja a feltételes szabályzatokat az access control megszakítása során:

1. A készenléti szabályzatok által megcélzott felhasználók, az adott alkalmazások adott hálózatoknak való hozzáférés engedélyezéséhez.
2. Tiltsa le a rendszeres ellenőrzés-alapú szabályzatokat.

### <a name="microsoft-recommendations"></a>Az ajánlott eljárásokat

Attól függően, melyik megoldások vagy ágakba egy megszakítása során használt a szervezet sikerült hozzáférést engedélyező csak jelszóval. Nincs védelmi nem jelentős biztonsági kockázatot jelenthet, gondosan kell mérni. Szervezetek számára a következőkre:

1. A módosítás vezérlési stratégia részeként a dokumentum minden változás- és visszavonásához, amint a hozzáférés-vezérlés teljesen működőképes megvalósítva, bármely ágakba lehessen az előző állapotra.
2. Tegyük fel, hogy rosszindulatú megkísérli bekérni jelszavak keresztül jelszó megfelelő, vagy adathalász támadások során a többtényezős hitelesítés le van tiltva. Emellett kártékony elemek már előfordulhat, hogy korábban nem adott hozzáférést ezen időszak alatt megkísérlése összes erőforrást. A kritikus fontosságú felhasználóknak pl.: vezetőknél részlegesen csökkentheti a kockázat által a jelszavak őket a többtényezős hitelesítés letiltása előtt.
3. Archív tárolási szint összes bejelentkezési tevékenység azonosítására, akik eléréséhez Mi az időre, többtényezős hitelesítés le volt tiltva.
4. [Az összes kockázati események jelentett osztályozási](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) ezen időszak alatt.

## <a name="after-a-disruption"></a>A megszakítás után

A módosítások a aktivált válságtervet részeként után a szolgáltatás helyreáll a zavart okozó visszavonása. 

1. A normál szabályzatot
2. Tiltsa le a feltételes szabályzatokat. 
3. Állítsa vissza a további módosításokat végzett, és a dokumentált során a megszakítások időtartamát.
4. A vészelérési fiókok használata esetén ne felejtse el generálja újra a hitelesítő adatokat, és a fizikailag is biztosítsa a vészelérési fiókhoz eljárások részeként az új hitelesítő adatait.
5. Továbbra is [összes kockázati eseményt jelentett osztályozási](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) a megszakítás a gyanús tevékenység után.
6. Az összes frissítési biztonsági jogkivonat kiadott visszavonása [PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) , amelyekre a felhasználók egy csoportja. A megszakítások időtartamát során használt kiemelt jogosultságú fiókok számára fontos összes frissítési biztonsági jogkivonat visszavonására, és nagyon kényszeríti számukra, hogy hitelesítse magát újra, és megfelelnek a visszaállított házirendek felügyelete.

## <a name="emergency-options"></a>Vészhelyzeti beállításai

 Vészhelyzet esetén és a szervezet esetén volt korábban nem kockázatcsökkentési, illetve feltételes terv megvalósításához, majd kövesse a a [felhasználói percnek ágakba](#contingencies-for-user-lockout) szakaszt, ha már használja a feltételes hozzáférés MFA kényszerítése házirendeket.
Ha a szervezet az felhasználónkénti MFA örökölt házirendek, akkor érdemes lehet a következő helyett:

1. Ha a vállalati hálózat kimenő IP-cím, a megbízható IP-címek csak a vállalati hálózathoz való hitelesítés engedélyezése, felveheti őket.
 2. Ha nincs leltár készíthető a kimenő IP-címek, vagy, engedélyezze a hozzáférést a vállalati hálózaton kívül és belül kell, hozzáadhat a teljes IPv4-címtérhez, megbízható IP-címek 0.0.0.0/1 és 128.0.0.0/1 megadásával.

>[!IMPORTANT]
 > Ha megváltoztatja a megbízható IP-címek hozzáférés feloldása, IP-címek (például lehetetlen odautazás vagy ismeretlen helyről) társított kockázati események nem jön létre.

>[!NOTE]
 > Konfigurálás [megbízható IP-címek](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) esetében csak akkor érhető el, az Azure MFA [Azure AD Premium-licencet](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Részletek

* [Az Azure AD Authentication – dokumentáció](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [A válságkezelési-hozzáférési rendszergazdai fiókok kezelése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Nevesített helyek konfigurálása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
 * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Hibrid Azure Active Directoryhoz csatlakoztatott eszközök konfigurálása](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Vállalati Windows Hello – Üzembehelyezési útmutató](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
 * [Tájékoztató a jelszavakról – a Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Mik azok a feltételek az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Mik a hozzáférés-vezérlés az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
