---
title: A Partner Center egy kereskedelmi Marketplace-fiók kezelése
description: Ismerje meg, hogy a Partner Center egy kereskedelmi Marketplace-fiók kezelése.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: 935d2e1c96705506636c2883113a64bb70c39336
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806191"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>A Partner Center kereskedelmi Marketplace-fiók kezelése 

Miután [Partnerközpont-fiókkal létrehozott](./create-account.md), a fiók és az ajánlatok használata kezelheti a [kereskedelmi Marketplace irányítópultjának](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

Ebben a cikkben azt ismerje a Partnerközpont-fiókkal, kezelése, beleértve a: 

- [Hozzáférés a Partner Center Fiókbeállítások](#access-your-account-settings)
- [Keresse meg a Gyártóazonosítóval, értékesítői azonosítója, felhasználói azonosító és az Azure AD-bérlőt](#account-details)
- [Kapcsolattartási adatok frissítése](#contact-info)
- [Pénzügyi adatokat (küldenie jóváhagyásra cége kifizetési fiók, adó profilnál küldenie jóváhagyásra cége kifizetési "Felfüggesztve" állapota) kezelése](#financial-details)
- [Ügyfél használat monitorozásával GUID követés beállítása](#tracking-guids)
- [Kezelő felhasználók](#manage-users)
- [Manager-csoportok](#manage-groups)
- [Manager Azure AD-alkalmazások](#manage-azure-ad-applications)
- [Felhasználói szerepkörök és engedélyek megadása](#define-user-roles-and-permissions)
- [Az Azure AD-bérlőt (munkahelyi fiókok) kezelése](#manage-tenants)
- [Partnerközpont Manager szerződések](#agreements)


## <a name="access-your-account-settings"></a>A Fiókbeállítások eléréséhez

Még nem tette meg, ha a Ön (vagy a szervezet rendszergazdája) hozzá kell férnie a [Fiókbeállítások](https://partner.microsoft.com/dashboard/account/management) annak érdekében, hogy a Partner Center fiók:
- a vállalati fiók ellenőrzési állapotának ellenőrzése
- Erősítse meg az értékesítői azonosítója, az MPN-azonosító, a közzétevő-azonosító, és a kapcsolattartási adatokat, többek között a vállalati jóváhagyó és értékesítői kapcsolattartó
- a vállalati pénzügyi adatait, például adó kivételeket, ha a megfelelő beállítása
- bárki, aki az üzleti fiókot fog használni a Partnerközpontba a felhasználói fiókok létrehozása

### <a name="open-developer-settings"></a>Nyissa meg a fejlesztői beállítások

Fiókbeállítások, a jobb felső sarkában található a [kereskedelmi Marketplace irányítópultjának](https://partner.microsoft.com/dashboard/commercial-marketplace) a Partner Center. Kattintson a fogaskerék ikonra (a képernyő jobb felső sarokban az irányítópult), majd **fejlesztői beállítások**. 

![A Partner Center fiók beállítások menü](./media/dashboard-developer-settings.png)

Belül **Fiókbeállítások**, fogja tudni megtekinteni a:
- **Fiókadatok**: Fiók típusa és a fiók állapota
- **Közzétevő azonosítók**: Értékesítői azonosítója, felhasználói azonosító, közzétevő-azonosító, az Azure AD-bérlőt, stb.
- **Kapcsolattartási adatok**: Közzétevő neve, értékesítői kapcsolattartójának a neve, e-mail, telefon és cím megjelenítése
- **Pénzügyi adatok**: Küldenie jóváhagyásra cége kifizetési fiók adó profil és küldenie jóváhagyásra cége kifizetési "Felfüggesztve" állapota
- **eszközök**: A fiókjához társított tesztelési eszközök
- **GUID-azonosítói követési**: Bármely nyomon követése a fiók GUID azonosítók társítása

### <a name="account-details"></a>Fiók adatai

A fiók részletei szakaszban látható alapvető adatai, például a **fiók típusa** (vállalat vagy személy) és a **ellenőrzési állapotának** fiókja. A fiók-ellenőrzési folyamat során ezek a beállítások minden lépés szükséges, beleértve a e-mailes ellenőrzés, alkalmazási ellenőrzési és üzleti ellenőrzést jelenik meg. Az e-mail-cím itt frissítése is, és küldje el újra az ellenőrzési, ha szükséges. 

### <a name="publisher-ids"></a>Közzétevő-azonosítók

A közzétevő azonosítók területen láthatja a **értékesítői azonosító**, **MPN-azonosító**, és **közzétevő-azonosító**. Ezek az értékek rendeli hozzá a Microsoft fejlesztői fiók nyitása egyedi azonosításához, ezért nem szerkeszthető.

### <a name="contact-info"></a>Kapcsolattartási adatok

Az ügyfél adatai szakaszban láthatja a **közzétevő megjelenített név**, **értékesítői kapcsolattartási adatok** (a kapcsolattartónak a neve, e-mail, telefonszám és a vállalati értékesítői címét), és a **vállalati jóváhagyó** (nevét, e-mail, és az egyéni szolgáltató jóváhagyásához a vállalat döntések telefonszámot). 

### <a name="financial-details"></a>Pénzügyi adatok

A pénzügyi adatok szakaszban adja meg, vagy a pénzügyi adatok frissítése, ha fizetős alkalmazást, bővítmények és szolgáltatások tesz közzé. 

Ha csak ingyenes ajánlatokhoz listázásához, nem kell küldenie jóváhagyásra cége kifizetési fiók beállítása, vagy adja meg minden olyan adót űrlapokat. Ha később változtatni szeretne rajta, és döntse el, hogy a Microsoft keresztül, küldenie jóváhagyásra cége kifizetési-fiók, és töltse ki adó űrlapok időpontjában. 

#### <a name="payout-account"></a>Küldenie jóváhagyásra cége kifizetési fiók

Egy küldenie jóváhagyásra cége kifizetési egy a bank fiók, amelyhez az értékesítési bevétel küldi. A bank fióknak ugyanabban az országban, ahol regisztrálta a Partnerközpont-fiókkal kell lennie.

A küldenie jóváhagyásra cége kifizetési fiók beállítása, kell **társítsa a Microsoft Account**:
1. A **Fiókbeállítások**alatt a **pénzügyi adatok** szakaszban jelölje be **társítsa a Microsoft Account**. 
2. Amikor a rendszer kéri, jelentkezzen be a Microsoft fiók (MSA). Ez a fiók már egy másik Partnerközpont-fiókkal társított nem lehet. 
3. A küldenie jóváhagyásra cége kifizetési fiókja, teljesen kijelentkezhet a Partner Center, a telepítés befejezéséhez, majd jelentkezzen be, mint a Microsoft-Account (helyett a munkahelyi fiókjával). 

Most, hogy a Microsoft-Account társítva, egy küldenie jóváhagyásra cége kifizetési fiók hozzáadásához meg kell:
- **Válasszon egy fizetési módot**: Banki vagy PayPal
- **Adja meg a fizetési adatait**: Ez magában foglalhatja kiválasztása a fiók típusát (megtakarítási vagy), írja be a fiók tulajdonosa nevét, a fiók száma és útválasztási számát, számlázási címét, telefonszámát vagy PayPal e-mail címet. * További információ a fiók fizetési módként alkalmazott, és megtudhatja, hogy támogatott a piaci régióban PayPal használatáról: [PayPal info](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> A küldenie jóváhagyásra cége kifizetési fiók módosítása késleltetheti-e a fizetések fel egy fizetési ciklus szerint. Ez a késleltetés az oka, hogy ellenőriznünk kell a fiókját, ahogy tesszük először küldenie jóváhagyásra cége kifizetési fiók beállítása során. Meg fogjuk továbbra is lekérése fizetett a teljes összeget után a fiókját ellenőriztük; minden kifizetés miatt a jelenlegi fizetési ciklus a következő parancsot kell felvenni.  

#### <a name="tax-profile"></a>Adózási profil

Tekintse át az aktuális adó profil állapota, a megfelelő megerősítése **entitástípus** és **adó Tanúsítványadatok** jelenik meg. Válassza ki **szerkesztése** frissítéséhez, vagy hajtsa végre a szükséges űrlapokat.

Az adó állapotának megállapításához kell tartózkodási és polgárságról az országát, és töltse ki a megfelelő adó űrlapot országot vagy régiót társított.

Függetlenül a tartózkodási hely vagy polgárságról ország adja meg az Egyesült Államok adó űrlapok bármely ajánlat keretében a Microsoft számára. Partnerek, akik megfelelnek bizonyos Egyesült államokbeli tárolási helyére vonatkozó előírásoknak kell töltenie az IRS W-9 űrlap. Az Amerikai Egyesült Államokon kívül más partnerekkel kell töltenie az IRS W-8 űrlap. Kitöltheti űrlapok online adó profilját teljesítése közben.

Egyesült Államok egyes adószámát (vagy ITIN), nem szükséges, kifizetések kapni a Microsofttól, vagy a külföldön adó szerződés előnyeit.

Akkor is, és küldje el az elektronikus úton a Partner Center; adó űrlapok a legtöbb esetben nem kell a nyomtató- és e-mailt űrlap.

Más országokból és régiókból eltérő adó követelmények vonatkozhatnak. A pontos terhelő adók kell fizetett összeg attól függ, a országok és régiók, ahol az ajánlatok árul. A Microsoft remits forgalmi és használati adó egyes országokban az Ön nevében. Ezekben az országokban azonosítja az ajánlat ajánlati folyamatban. Más országokban, ahol Ön regisztrálva van, függően szükség lehet értékesítési befizetni, és közvetlenül a helyi adóhatóságnak, az értékesítési adó használata. Emellett az értékesítési bevétel kap adóköteles bevételként lehet. Határozottan javasoljuk, hogy lépjen kapcsolatba a megfelelő jogosultságokat az országot vagy régiót, amelyben leginkább segít meghatározni a megfelelő adózási adatait a Microsoft eladási tranzakciókra vonatkozóan.

##### <a name="withholding-rates"></a>A visszatartási díjszabása
Az adatokat az űrlap az adó küld el a megfelelő adó visszatartási sebesség határozza meg. Csak az értékesítés, az Egyesült Államokban; az alkotó a visszatartási díjszabás vonatkozik USA-helyeket az értékesítések nem vonatkozik a visszatartási. A visszatartási díjszabása változhat, de a legtöbb fejlesztő, az Egyesült Államokon kívül regisztrálása, az alapértelmezett sebesség 30 %. Lehetősége van ez a díjszabás csökkentését, ha az Egyesült Államokban egy adó szerződés megegyezett az országot.

##### <a name="tax-treaty-benefits"></a>Adózási szerződés előnyei
Ha az Egyesült Államokon kívül, előfordulhat, hogy lehet képes kihasználni az adó szerződés előnyeit. Ezeket az előnyöket országra eltérőek lehetnek, és lehetővé teszi, hogy kevesebb adót, amelyet a Microsoft tartja vissza. Adózási szerződés előnyei II. rész az W-8BEN űrlap kitöltésével igényelhet. Azt javasoljuk, hogy az ország vagy régió meghatározni, hogy ezeket az előnyöket vonatkoznak-e meg a megfelelő erőforrásokkal folytatott kommunikáció.

[További részletes információ a Windows app/játék fejlesztők és az Azure piactér kiadói](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Küldenie jóváhagyásra cége kifizetési "Felfüggesztve" állapota

Alapértelmezés szerint a Microsoft küld kifizetések havi rendszerességgel történik. Azonban lehetősége van a kifizetések helyezhető tartásba, amely megakadályozza, hogy a fizetések küldése a fiókjához. Ha a kifizetések szüneteltetése, továbbra is bármely bevétel megszerzésére, és adja meg az adatait rögzíti a **küldenie jóváhagyásra cége kifizetési összefoglalás**. Azonban nem küldünk fizetési fiókjába mindaddig, amíg a Megtartás. 

Helyezze el a fizetések felfüggesztve, lépjen a **Fiókbeállítások**. A **pénzügyi adatok**, a a **küldenie jóváhagyásra cége kifizetési tartsa állapot** területén váltsa át a csúszkát a **a**. A küldenie jóváhagyásra cége kifizetési "Felfüggesztve" állapota bármikor megváltoztathatja, de vegye figyelembe, hogy a döntés befolyásolja a következő havi küldenie jóváhagyásra cége kifizetési. Például, ha szeretné megtartani a április küldenie jóváhagyásra cége kifizetési, ügyeljen arra, hogy a küldenie jóváhagyásra cége kifizetési fenntartási állapotának beállítása **a** március lejárta előtt.

Már megadta a küldenie jóváhagyásra cége kifizetési tartsa állapot **a**, minden kifizetések lesz felfüggesztve, addig a csúszkát ritkáról vissza **ki**. Ha így tesz, lehetősége lesz része a következő havi küldenie jóváhagyásra cége kifizetési ciklus során (feltéve, hogy bármely alkalmazandó fizetési küszöbértékek teljesül-e). Például akkor, ha, felfüggesztve, a kifizetések volna, de szeretné, hogy a júniusi generált küldenie jóváhagyásra cége kifizetési, akkor ügyeljen arra, hogy a Váltás a küldenie jóváhagyásra cége kifizetési tartsa állapot **ki** május vége előtt.

> [!NOTE]
> A **küldenie jóváhagyásra cége kifizetési tartsa állapot** kijelölés vonatkozik **összes** bevételi források kifizetett keresztül a Microsoft Partner Centeren, többek között az Azure piactéren, az appsource-ban, Microsoft Store hirdetési stb.). Nem választhat másik fenntartási állapotok bevétele adatvédelmiszint.

### <a name="devices"></a>Eszközök

Az eszközfelügyeleti beállítások csak az UWP-közzététel vonatkoznak. [További információk](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Követési GUID-azonosítói

Globálisan egyedi azonosítóra (GUID) egyedi hivatkozást számokat (32 hexadecimális számjegy) az Azure-használat nyomon követésére használható. 

Követési GUID azonosítókat létrehozni, egy GUID-generátort kell használnia. Az Azure Storage csapata létrehozott egy [GUID generátor űrlap](https://aka.ms/StoragePartners) , amely e-mailt küld, egy GUID Azonosítót a helyes formátumú, és a különböző nyomkövetési rendszerek felhasználhatók.

Javasoljuk, hogy hozzon létre egy egyedi GUID azonosító minden egyes termékekhez tartozó ajánlat és a terjesztési csatorna. A termék több terjesztési csatornák egyetlen GUID használatára, ha nem szeretné méretüknél fogva reporting kérheti.

Ha a sablonok segítségével adott termék központi telepítését, és az Azure Marketplace-en és githubon érhető, akkor létrehozhat és regisztrálhat 2 egyedi GUID:

*   Az Azure Marketplace-en A termék
*   A termék a Githubon

Jelentéskészítés végzi el a partner érték (Microsoft-Partnerazonosító) és a GUID megváltozott. GUID-nak megfelelően az ajánlat minden csomagban egy részletesebb szintjén is követheti.

További információkért lásd: a [GUID – gyakori kérdések az Azure követési ügyfelek általi használatot](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Többfelhasználós fiókok kezelése

Használja Partnerközpontban [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) több felhasználói fiók hozzáférési és felügyeleti. A szervezet a regisztrációs folyamat részeként az Azure ad-ben a Partnerközpont-fiókkal automatikusan társítva lesz. 

## <a name="manage-users"></a>Felhasználók kezelése

A **felhasználók** Partnerközpont szakaszában (alatt **fiókbeállításokat**) a felhasználók, csoportok és Azure AD-alkalmazások, amelyek rendelkeznek a Partnerközpont-fiókkal való hozzáférés kezelése az Azure AD használjuk. Vegye figyelembe, hogy annak érdekében, hogy a felhasználók kezelése, be kell jelentkeznie a következővel a [munkahelyi fiók](./company-work-accounts.md) (a társított Azure AD-bérlő). Kezelheti a felhasználók egy másik munkahelyi fiókban / a bérlő számára, szüksége lesz jelentkezzen ki, majd jelentkezzen be újra rendelkező felhasználóként **Manager** engedélyeket, amelyek a munkahelyi fiók / bérlői. 

Miután jelentkezett be a munkahelyi fiókjával (Azure AD-bérlő), akkor a következőket teheti:
- [Felhasználók hozzáadása vagy eltávolítása](#add-or-remove-users)
- [Felhasználói jelszó módosítása](#change-a-user-password)
- [Adjon hozzá vagy csoportok eltávolítása](#add-or-remove-users)
- [Adja hozzá, vagy távolítsa el az Azure AD-alkalmazások](#add-new-azure-ad-applications)
- [Az Azure AD-alkalmazást kulcsok kezelése](#manage-keys-for-an-azure-ad-application)
- [Felhasználói szerepkörök és engedélyek megadása](#define-user-roles-and-permissions)


Ne feledje, hogy minden Partnerközpont felhasználók (például csoportokat és az Azure AD-alkalmazások) rendelkeznie kell egy aktív munkahelyi fiókot a egy [Azure AD-bérlő](#manage-tenants) társítva a Partnerközpont-fiókkal. 

### <a name="add-or-remove-users"></a>Felhasználók hozzáadása vagy eltávolítása

A fióknak rendelkeznie kell [ **Manager szintű** ](#define-user-roles-and-permissions) engedélyeit a [munkahelyi fiók (az Azure AD-bérlő)](./company-work-accounts.md) , amelyre szeretné hozzáadása vagy szerkesztése a felhasználók.

#### <a name="add-existing-users"></a>A meglévő felhasználók hozzáadása

Felhasználók hozzáadása a Partnerközpont-fiókkal, amely már szerepel a vállalati [munkahelyi fiók (az Azure AD-bérlő)](./company-work-accounts.md):

1. Lépjen a **felhasználók** (alatt **Fiókbeállítások**), és válassza ki **felhasználók hozzáadása**.
2. Válassza ki egy vagy több felhasználót a listából, amely akkor jelenik meg. A keresőmező használatával kereshet adott felhasználókat.
Ha a több felhasználó hozzáadása a Partnerközpont-fiókkal választja, kell rendelnie őket ugyanabban a szerepkör vagy egyéni engedélyeket. Több, különböző szerepkörök/engedélyekkel rendelkező felhasználók hozzáadásához ismételje meg ezeket a lépéseket minden egyes szerepkörökkel és egyéni engedélyeket.
3.  Ha elkészült, válassza a felhasználók, kattintson a **kiválasztott Hozzáadás**.
5.  Az a **szerepkörök** területén adja meg a szerepkört vagy a kijelölt felhasználókat egyéni engedélyeket.
6.  Kattintson a **Mentés** gombra.

#### <a name="create-new-users"></a>Új felhasználók létrehozása

Új felhasználói fiókokat hozhat létre, rendelkező fiókkal kell rendelkeznie [ **globális rendszergazdai** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) engedélyeket. 

1. Lépjen a **felhasználók** (alatt **Fiókbeállítások**), jelölje be **felhasználók hozzáadása**, majd válassza a **új felhasználók létrehozására**.
1. Adjon meg egy Utónév, Vezetéknév és felhasználónév minden új felhasználó számára. 
1. Ha azt szeretné, hogy az új felhasználó a szervezet címtárához egy globális rendszergazdai fiókkal rendelkezik, jelölje be az **végezze el ezt a felhasználót egy globális rendszergazda az Azure AD-ben minden címtárerőforráshoz teljes hozzáféréssel rendelkező**. Ez a minden felügyeleti funkciójához, a vállalat által az Azure AD felhasználói teljes hozzáférést biztosít. Ők is látni fogják hozzáadhatja és kezelheti a szervezet munkahelyi fiókjával (Azure AD-bérlő), a felhasználók azonban nem a Partner Center, kivéve, ha adjon a fióknak a megfelelő szerepkör és engedélyekkel. 
1. Ha szeretné, hogy be van jelölve **, a felhasználók egy globális rendszergazdai**, meg kell adnia egy **jelszó helyreállítási e-mail** helyreállítása a jelszavát, szükség esetén a felhasználó számára.
1. Az a **csoporttagság** területen válassza ki, amelyhez az új felhasználót társítani kívánt csoportnak sem.
1. Az a **szerepkörök** területén adja meg a szerepkört vagy a felhasználó egyéni engedélyek.
1. Kattintson a **Mentés** gombra.

Új felhasználó létrehozása a Partner Center is létrehoz egy fiókot, hogy a felhasználó a munkahelyi fiókot (Azure AD-bérlő), amelyre bejelentkezett. A felhasználó neve, a Partner Center módosítása fog ugyanazokat a módosításokat a szervezet munkahelyi fiókot (Azure AD-bérlő).

#### <a name="invite-new-users-by-email"></a>Új felhasználók e-mailes meghívása

Meghívás a felhasználóknak, akik jelenleg nem része a céges munkahelyi fiókjával (Azure AD-bérlő) e-mailen keresztül, rendelkező fiókkal kell rendelkeznie [ **globális rendszergazdai** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) engedélyeket. 

1. Lépjen a **felhasználók** (alatt **Fiókbeállítások**), jelölje be **felhasználók hozzáadása**, majd válassza a **felhasználók e-mailes meghívása**.
2. Adjon meg egy vagy több e-mail címet (legfeljebb tíz), pontosvesszővel vagy vesszővel elválasztva.
3. Az a **szerepkörök** területén adja meg a szerepkört vagy a felhasználó egyéni engedélyek.
4. Kattintson a **Mentés** gombra.

A meghívott felhasználók fognak kapni egy e-mailes meghívót a csatlakozás a Partnerközpont-fiókkal. A munkahelyi fiókjával (Azure AD-bérlő) egy új vendégfelhasználó fiók létrejön. Minden felhasználó kell, mielőtt hozzáférhetne a fiók, fogadja el a meghívót.

Ha egy meghívó újraküldése van szüksége, látogasson el a **felhasználók** lapon keresse meg a meghívót a felhasználók a listában, válassza ki az e-mail-címükkel (vagy a szöveg arról, hogy a *függőben lévő meghívás*). Ezután a lap alján válassza **meghívó újraküldése**.
 

> [!NOTE]
> Ha a szervezet használja [címtár-integráció](https://go.microsoft.com/fwlink/p/?LinkID=724033) szinkronizálni a helyi címtárszolgáltatás az Azure AD-vel, akkor nem lehet új felhasználók, csoportok vagy az Azure AD-alkalmazások létrehozása a Partner Center. Ön (vagy egy másik rendszergazda a helyszíni címtár) előtt létre szeretne hozni azokat a helyszíni címtárban közvetlenül is elérheti, és hozzáadhatja őket a Partner Center kell.

#### <a name="remove-a-user"></a>Felhasználó eltávolítása

Felhasználó eltávolítása a munkahelyi fiókjával (Azure AD-bérlő), lépjen a **felhasználók** (alatt **Fiókbeállítások**), jelölje be a felhasználót, hogy szeretné eltávolítani, a jelölőnégyzet használatával a sokkal jobb oldali oszlopban, majd válassza ki a  **Távolítsa el** , a rendelkezésre álló műveletek. Egy előugró ablak jelenik meg, hogy erősítse meg, hogy el kívánja távolítani a kijelölt felhasználókat.

#### <a name="change-a-user-password"></a>Felhasználói jelszó módosítása

Ha egy felhasználó kell módosítani a jelszavát, akkor megteheti magukat, ha megadott egy **jelszó helyreállítási e-mail** a felhasználói fiók létrehozásakor. A jelszó is frissítheti az alábbi lépéseket követve. A vállalati munkahelyi fiókjával (Azure AD-bérlő) egy felhasználói jelszó módosítására, akkor be kell jelentkeznie a fiók [ **globális rendszergazdai** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) engedélyeket. Vegye figyelembe, hogy a művelet módosítja a felhasználó jelszavát az Azure AD-bérlőben, és a jelszó Partnerközpont eléréséhez használnak.

1.  Az a **felhasználók** lap (alatt **Fiókbeállítások**), válassza ki a szerkeszteni kívánt felhasználói fiók nevét.
2.  Válassza ki a **jelszó alaphelyzetbe állítása** gombra a lap alján.
3.  Jóváhagyást jelző oldal jelenik meg a felhasználó számára, beleértve az ideiglenes jelszót a bejelentkezési adatok megjelenítése. Ügyeljen arra, kinyomtatni vagy másolja ki ezeket az adatokat, és adja meg a felhasználó szerint, nem fog tudni hozzáférni az ideiglenes jelszó ezt oldal elhagyása után.


## <a name="manage-groups"></a>Csoportok kezelése

Csoportok lehetővé teszik több felhasználói szerepkörök és engedélyek minden egy helyen.

#### <a name="add-an-existing-group"></a>Adjon hozzá egy meglévő csoporthoz

Hozzáadása egy csoport, amely már létezik az a szervezet munkahelyi fiókjával (Azure AD-bérlő), a Partnerközpont-fiókkal: 

1.  Az a **felhasználók** lap (alatt **Fiókbeállítások**), jelölje be **csoportok hozzáadása**.
2.  Válassza ki egy vagy több csoportot a listából, amely akkor jelenik meg. A keresőmező segítségével meghatározott csoportok keresése.
Ha egynél több csoport hozzáadása a Partnerközpont-fiókkal, kell rendelnie őket ugyanabban a szerepkör vagy egyéni engedélyeket. Több csoport különböző szerepkörök és engedélyekkel rendelkező hozzáadásához ismételje meg ezeket a lépéseket minden egyes szerepkörökkel és egyéni engedélyeket.
3.  Ha befejezte a csoportok kiválasztásával, kattintson a **kiválasztott Hozzáadás**.
4.  Az a **szerepkörök** területén adja meg a szerepkört vagy a kiválasztott csoport(ok) egyéni engedélyeket. A csoport összes tagja lesz érhetik el a Partner Center engedélyekkel rendelkező fiókot a csoporthoz, függetlenül a szerepkörök és engedélyek az egyéni fiókjával társított alkalmazza.
5.  Kattintson a **Mentés** gombra.

Amikor hozzáad egy meglévő csoportot, minden felhasználó, aki tagja a csoportnak fogja tudni elérni a Partnerközpont-fiókkal társított a csoporthoz hozzárendelt szerepkör engedélyeivel.

#### <a name="add-a-new-group"></a>Új csoport hozzáadása

Vadonatúj csoport hozzáadása a Partnerközpont-fiókkal: 

1.  Az a **felhasználók** lap (alatt **Fiókbeállítások**), jelölje be **csoportok hozzáadása**.
2.  A következő oldalon válassza ki a **új csoport**.
3.  Adja meg az új csoport megjelenítendő nevét.
4.  Adja meg a szerepkört vagy a csoporthoz tartozó egyéni engedélyeket. A csoport összes tagja lesz érhetik el a Partner Center engedélyekkel rendelkező fiókot itt, függetlenül a szerepkörök és engedélyekkel, az egyéni fiókjával társított alkalmaz.
5.  A megjelenő listában válassza ki a felhasználó az új csoport. A keresőmező használatával kereshet adott felhasználókat.
6.  Ha elkészült, válassza a felhasználók, kattintson a **kiválasztott Hozzáadás** az új csoporthoz való hozzáadásakor.
7.  Kattintson a **Mentés** gombra.

Vegye figyelembe, hogy ez az új csoport létrejön a szervezet munkahelyi fiókot (Azure AD-bérlő), nem csak a Partnerközpont-fiókkal.

#### <a name="remove-a-group"></a>Csoport eltávolítása

Csoport eltávolítása a munkahelyi fiókjával (Azure AD-bérlő), lépjen a **felhasználók** (alatt **Fiókbeállítások**), jelölje be a csoport, amelyet szeretne eltávolítani a sokkal jobb oldali oszlopban, a jelölőnégyzet használatával, majd válassza ki a  **Távolítsa el** , a rendelkezésre álló műveletek. Egy előugró ablak jelenik meg, hogy erősítse meg, hogy szeretné-e a kiválasztott csoport(ok) törlése.

## <a name="manage-azure-ad-applications"></a>Az Azure AD-alkalmazások kezelése

Engedélyezheti az alkalmazásokat vagy szolgáltatásokat, amelyek részei a vállalat az Azure AD a Partnerközpont-fiókja eléréséhez. 

#### <a name="add-existing-azure-ad-applications"></a>Meglévő Azure AD-alkalmazások hozzáadása 

Adja hozzá az alkalmazások, amely már létezik a cég Azure Active Directory: 

1.  Az a **felhasználók** lap (alatt **Fiókbeállítások**), jelölje be **adja hozzá az Azure AD-alkalmazások**.
2.  A megjelenő listából válassza ki egy vagy több Azure AD-alkalmazások. A keresőmező segítségével keresse meg az adott Azure AD-alkalmazások. Ha egynél több Azure AD-alkalmazás hozzáadása a Partnerközpont-fiókkal, kell rendelnie őket ugyanabban a szerepkör vagy egyéni engedélyeket. Több, különböző szerepköröket és engedélyeket az Azure AD-alkalmazások hozzáadásához ismételje meg ezeket a lépéseket minden egyes szerepkörökkel és egyéni engedélyeket.
3.  Ha elkészült, válassza az Azure AD-alkalmazások, kattintson a **kiválasztott Hozzáadás**.
5.  Az a **szerepkörök** területén adja meg a szerepkört vagy a kiválasztott egyéni engedélyek az Azure AD-alkalmazás.
6.  Kattintson a **Mentés** gombra.

#### <a name="add-new-azure-ad-applications"></a>Adja hozzá az új Azure AD-alkalmazások 

Ha azt szeretné, a Partnerközpont hozzáférés biztosítása a vadonatúj Azure AD alkalmazás-fiókot hozhat létre egyet a **felhasználók** szakaszban. Vegye figyelembe, hogy ez hoz létre egy új fiókot a vállalati munkahelyi fiókjával (Azure AD-bérlő), nem csak a Partnerközpont-fiókkal. Az Azure AD-alkalmazás elsősorban használja Partnerközpontban hitelesítéshez, és nincs szükség a felhasználók közvetlenül, ha bármely érvényes címet is megadhatja a **válasz URL-cím** és **Alkalmazásazonosító URI-t**leghosszabbak, Ezeket az értékeket, nem használja semmilyen más Azure AD-alkalmazást a címtárban.

1.  Az a **felhasználók** lap (alatt **Fiókbeállítások**), jelölje be **adja hozzá az Azure AD-alkalmazások**.
2.  A következő oldalon válassza ki a **új Azure AD-alkalmazás**.
3.  Adja meg a **válasz URL-cím** az új Azure AD-alkalmazást. Ez az URL-címet, ahol a felhasználók bejelentkezés és az Azure AD-alkalmazás (más néven az alkalmazás URL-címe vagy bejelentkezési URL-) használja. A **válasz URL-cím** nem lehet hosszabb 256 karakternél, és a címtáron belül egyedinek kell lennie.
4.  Adja meg a **Alkalmazásazonosító URI-t** az új Azure AD-alkalmazást. Ez az az Azure AD-alkalmazást, amely megjelenik az Azure AD egyszeri bejelentkezési kérelem elküldésekor egy logikai azonosítója. Vegye figyelembe, hogy a **Alkalmazásazonosító URI-t** minden egyes Azure AD-alkalmazást a címtár egyedinek kell lennie. Ez az azonosító nem lehet hosszabb 256 karakternél. Az Alkalmazásazonosító URI kapcsolatos további információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#changing-the-application-registration-to-support-multi-tenant).
5.  Az a **szerepkörök** területén adja meg a szerepkört vagy testre szabott engedélyeit az Azure AD-alkalmazás.
6.  Kattintson a **Mentés** gombra.

Adja hozzá, vagy egy Azure AD-alkalmazás létrehozása után visszatérhet a **felhasználók** szakaszt, és válassza ki az alkalmazás nevét, tekintse át a beállításokat az alkalmazáshoz, beleértve a bérlői azonosító, ügyfél-azonosító, válasz URL-cím és Alkalmazásazonosító URI-t.

#### <a name="remove-an-application"></a>Alkalmazás eltávolítása

Alkalmazások eltávolítása a munkahelyi fiókjával (Azure AD-bérlő), lépjen a **felhasználók** (alatt **Fiókbeállítások**), válassza ki az alkalmazást, amelyet szeretne eltávolítani a sokkal jobb oldali oszlopban, a jelölőnégyzet használatával Válassza a **eltávolítása** , a rendelkezésre álló műveletek. Egy előugró ablak jelenik meg, hogy erősítse meg, hogy el kívánja távolítani a kijelölt alkalmazás.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Az Azure AD-alkalmazást kulcsok kezelése

Ha az Azure AD-alkalmazás adatokat olvas be ír a Microsoft Azure ad-ben, el kell egy kulcsot. Partnerközpont annak adatait szerkesztésével kulcsok az Azure AD-alkalmazást hozhat létre. Eltávolíthatja a már nem szükséges kulcsokat is.

1.  Az a **felhasználók** lap (alatt **Fiókbeállítások**), válassza ki az Azure AD-alkalmazás nevét. Megjelenik az Azure AD-alkalmazást, beleértve az a dátum, amely a kulcs lett létrehozva, és mikor fog lejárni a aktív kulcsokat. 
2. Egy kulcsot, amely már nem szükséges eltávolításához jelölje ki **eltávolítása**.
3.  Adjon hozzá egy új kulcsot, jelölje be **új kulcs hozzáadása**.
4.  Megjelenik egy képernyő ábrázoló a **ügyfél-azonosító** és **kulcsértékek**. Ügyeljen arra, nyomtassa ki vagy másolja ezt az információt, mert nem lesz újból elérhető, az oldal elhagyása után.
4.  Ha azt szeretné, további kulcsok létrehozásához, válassza ki a **adjon hozzá egy másik kulcsot**.


### <a name="define-user-roles-and-permissions"></a>Felhasználói szerepkörök és engedélyek megadása

A vállalati felhasználókhoz rendelhető, a következő szerepköröket és engedélyeket a Partner Center kereskedelmi Marketplace programba. 

Megjegyzés: az Azure Active Directory (AAD) bérlői szerepkörök globális rendszergazda, a felhasználó rendszergazda és a CSP szerepkörök tartalmaz. Nem – AAD-szerepkörök ezeket a szerepköröket, amelyek nem a bérlő kezelésére, és azok tartalmaznak MPN admin, üzleti profil admin, hivatkozási rendszergazdai, ösztönző rendszergazdai és ösztönző felhasználói.


|**Szerepkör**|**Engedélyek**|
|----------------------------------|:---------------------------------|
|Globális rendszergazda|• Minden Microsoft fiók vagy szolgáltatás teljes körű jogosultságokkal rendelkező hozzáférhet
|      |• A Partnerközponthoz tartozó támogatási jegyek létrehozása
||• Nézet szerződések, az árlisták és ajánlatok
||• Megtekintése, létrehozása és kezelése a partner felhasználók számára|
|Kezelő|• Hozzáférhet kifizetés és adó beállításokat kivételével az összes Microsoft fiók funkció
|      |• Kezelheti a felhasználók, szerepkörök, és a munkahelyi fiókok (bérlők)|
|Fejlesztő|• Töltse fel a csomagok, alkalmazások és bővítmények elküldheti, és a telemetriai adatok részletes használati jelentés megtekintése
|      |• Nem tud hozzáférni a pénzügyi adatok vagy a fiók beállításokat|
|Üzleti Közreműködője|• Pénzügyi adatok eléréséhez, és állítsa be a szolgáltatás díjszabását.
|      |• Nem hozhatók létre, és küldje el az új alkalmazások és bővítmények|
|Pénzügyi Közreműködője|• Küldenie jóváhagyásra cége kifizetési jelentések megtekintéséhez
|      |• Alkalmazások és beállítások nem végezhet módosításokat|
|Forgalmazók|• Válaszolhat ügyfél ismertetők és a nem pénzügyi jelentések
|      |• Alkalmazások és beállítások nem végezhet módosításokat|

Szerepkörök és engedélyek a Partnerközpont, például az Azure Active Directory (AD), más területeire kezelésével kapcsolatos további információért Cloud Solution Provider (CSP), a vezérlés Panel szállító (CPV), a vendégfelhasználók vagy a Microsoft Partner Network (MPN), lásd: [hozzárendelése felhasználói szerepköröket és engedélyeket a Partner Center](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Bérlők kezelése

Az Azure Active Directory (AD) bérlő, más néven "munkahelyi fiókjába" Ebben a dokumentációban a szervezet reprezentációját állítsa be az Azure Portalon, és segít egy adott példányán, a belső Microsoft-felhőszolgáltatások kezelése és a külső felhasználók számára. Ha egy Microsoft felhőszolgáltatásra, például Azure, Microsoft Intune vagy Office 365-höz, amelyre Ön feliratkozott a szervezet Azure AD-bérlő létrejött, az Ön számára. 

Állíthat be több bérlő használata a Partner Center. Minden felhasználó, aki a **Manager** szerepkörben a Partnerközpont-fiókkal van hozzáadása és eltávolítása az Azure AD-bérlőt a fiókból.  

### <a name="add-an-existing-tenant"></a>Adjon hozzá egy meglévő bérlőt

Egy másik Azure AD-bérlő társítása a Partnerközpont-fiókkal:

1.  Az a **bérlők** lap (alatt **Fiókbeállítások**), jelölje be **egy másik Azure AD-bérlő társítása**.
2. Adja meg a-bérlőt, amelyhez társítani szeretné az Azure AD hitelesítő adatait.
3. Tekintse át az Azure AD-bérlő a szervezet és a tartomány nevét. A hozzárendelés befejezéséhez válassza ki a **megerősítése**.

Ha létrejött a társítást, ezután ki fog hozzáadhatja és kezelheti a felhasználói fiók készen áll a **felhasználók** Partnerközpont szakaszát.

### <a name="create-a-new-tenant"></a>Hozzon létre egy új bérlőt

A Partnerközpont-fiókkal egy márka új Azure AD-bérlő létrehozása:

1.  Az a **bérlők** lap (alatt **Fiókbeállítások**), jelölje be **hozzon létre egy új Azure AD-bérlő**.
2. Írja be az új Azure AD directory adatait:
    - **Tartománynév**: Az egyedi nevet, amely az fogjuk használni az Azure AD-tartományát, a ". onmicrosoft.com". Például a megadott "példaként", az Azure AD-tartomány "example.onmicrosoft.com" lenne.
    - **Kapcsolattartási e-mail cím**: Egy e-mail címet, ahová elküldhetjük Önnek a fiókon szükség esetén.
    - **Globális rendszergazdai felhasználói fiók adatai**: Az Utónév, utolsó nevét, felhasználónév és a globális rendszergazdai fiókkal használni kívánt jelszót.
3. Válassza ki **létrehozás** ellenőrizze az új tartomány és a fiók adatait.
4. Jelentkezzen be az új Azure AD globális rendszergazdai felhasználónevet és jelszót a kezdéshez [hozzáadása és kezelése a felhasználók](#manage-users).

Új bérlők számára az Azure Portalon belül, nem pedig a Partner Center portálján létrehozásával kapcsolatos további információkért tekintse meg a cikket [hozzon létre egy új bérlőt az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>A bérlő törlése

Eltávolítja a bérlőt a Partnerközpont-fiókkal, keresse meg a nevét a **bérlők** lap (a **Fiókbeállítások**), majd **eltávolítása**. Győződjön meg arról, hogy el kívánja távolítani a bérlő fog kérni. Ha így tesz, az adott bérlőn belüli felhasználók fog tudni bejelentkezni a Partnerközpont-fiókkal, és ezen felhasználók számára konfigurált engedélyeket törlődni fog.

Amikor eltávolít egy bérlő, a bérlőben a Partnerközpont-fiókkal hozzáadott minden felhasználó már nem tudni bejelentkezni a fiókjába.

> [!TIP]
> A bérlő nem távolítható el, ha a jelenleg bejelentkezett Partnerközpont-fiókkal ugyanabban a bérlőben. Egy bérlő eltávolításához, jelentkezzen be, a partnerközpont- **Manager** , amely társítva van a fiók egy másik bérlőhöz. Ha a fiókjához társított kizárólag egy bérlővel van, a bérlőt csak eltávolítható be a Microsoft-fiókkal, amely a fiók megnyitása a bejelentkezést követően.


## <a name="agreements"></a>Szerződések

A **szerződések** Partnerközpont szakaszában (alatt **fiókbeállításokat**) tekintse meg a közzétételi szerződések, amely az Ön által engedélyezett listáját. Ezek a szerződések nevét és az ügynökverziókat, valamint a dátum, a rendszer elfogadta és a felhasználót, hogy elfogadja a Szerződés neve szerint jelennek meg. 

**Szükséges műveletek** van-e beavatkozást igénylő szerződés frissítései megjelenhet ez az oldal tetején. A frissített szerződés feltételeinek elfogadása, először olvassa el a társított szerződés verziót, majd jelölje ki **szerződés feltételeinek elfogadása**. 

A Partner Center Cloud Solution Provider (CSP) szerződésekre vonatkozó további információért látogasson el [a Microsoft Cloud szerződésüket területi és nyelvi](https://docs.microsoft.com/partner-center/agreements).

## <a name="next-steps"></a>További lépések

- [Hozzon létre egy új SaaS-ajánlat](./create-new-saas-offer.md)
