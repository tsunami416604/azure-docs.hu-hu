---
title: Kereskedelmi Piactéri fiók kezelése a partner Centerben
description: Ismerje meg, hogyan kezelhet egy kereskedelmi piactér-fiókot a partner Centerben.
author: ChJenk
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 8c3b9523f6e6991723b0da721dbf7667ee340772
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207969"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>A kereskedelmi piactér-fiók kezelése a partner Centerben 

Ha létrehozta [a partner Center-fiókot](./create-account.md), a [kereskedelmi piactér irányítópultján](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)kezelheti a fiókját és ajánlatait.

Ebben a cikkben bemutatjuk, hogyan kezelheti a partneri központ fiókját, többek között a következőket: 

- [A partner Center-fiók beállításainak elérése](#access-your-account-settings)
- [Megkeresheti a közzétevő AZONOSÍTÓját, az értékesítő AZONOSÍTÓját, a felhasználói azonosítót és az Azure AD-bérlőket](#account-details)
- [Kapcsolattartási adatok frissítése](#contact-info)
- [Pénzügyi részletek kezelése (kifizetési fiók, adózási profil, kifizetési Hold állapota)](#financial-details)
- [Nyomkövetési GUID-azonosítók beállítása az ügyfelek használatának figyeléséhez](#tracking-guids)
- [Felhasználók kezelése](#manage-users)
- [Csoportok kezelése](#manage-groups)
- [Azure AD-alkalmazások kezelése](#manage-azure-ad-applications)
- [Felhasználói szerepkörök és engedélyek definiálása](#define-user-roles-and-permissions)
- [Azure AD-bérlők kezelése (munkahelyi fiókok)](#manage-tenants)
- [A partner Center-szerződések kezelése](#agreements)


## <a name="access-your-account-settings"></a>Fiók beállításainak elérése

Ha még nem tette meg, akkor (vagy a szervezet rendszergazdája) el kell érnie a fiókpartner fiókjának [beállításait](https://partner.microsoft.com/dashboard/account/management) a következő sorrendben:
- a vállalat fiók-ellenőrzési állapotának ellenőrzése
- erősítse meg az értékesítő AZONOSÍTÓját, az MPN AZONOSÍTÓját, a közzétevő AZONOSÍTÓját és a kapcsolattartási adatokat, beleértve a vállalat jóváhagyóját és az értékesítő partnert is.
- a vállalat pénzügyi adatainak beállítása, beleértve az adómentességeket, ha szükséges
- hozzon létre felhasználói fiókokat mindenki számára, aki az Ön üzleti fiókját fogja használni a partner Centerben

### <a name="open-developer-settings"></a>Fejlesztői beállítások megnyitása

A Fiókbeállítások a [kereskedelmi piactér irányítópultjának](https://partner.microsoft.com/dashboard/commercial-marketplace) jobb felső sarkában találhatók a partner Centerben. Válassza a fogaskerék ikont (az irányítópult jobb felső sarkában), majd válassza a **fejlesztői beállítások**lehetőséget. 

![Fiókbeállítások menü a partner Centerben](./media/dashboard-developer-settings.png)

A **fiók beállításain**belül megtekintheti a következőket:
- **Fiók adatai**: Fiók típusa és fiók állapota
- **Közzétevői azonosítók**: Eladó azonosítója, felhasználói azonosító, közzétevő azonosítója, Azure AD-bérlők stb.
- **Kapcsolattartási adatok**: Közzétevő megjelenítendő neve, értékesítő kapcsolattartási neve, e-mail címe, telefonszáma és címe
- **Pénzügyi részletek**: Kifizetési fiók, adózási profil és kifizetési Hold állapota
- **Eszközök**: A fiókhoz társított összes tesztelési eszköz
- **Követési GUID azonosítók**: A fiókkal társított követési GUID azonosítók

### <a name="account-details"></a>Fiók adatai

A fiók részletei szakaszban megtekintheti az alapvető adatokat, például a **fiók típusát** (vállalat vagy egyéni) és a fiókja **ellenőrzési állapotát** . A fiók-ellenőrzési folyamat során ezek a beállítások megjelenítik az egyes szükséges lépéseket, beleértve az e-mailek ellenőrzését, a foglalkoztatás ellenőrzését és az üzleti ellenőrzést. Itt frissítheti az e-mail-címét, és szükség esetén újra elküldheti az ellenőrzést. 

### <a name="publisher-ids"></a>Közzétevő azonosítói

A közzétevői azonosítók szakaszban megtekintheti az **értékesítő azonosítóját**, az **MPN-azonosítót**és a **közzétevő azonosítóját**. Ezeket az értékeket a Microsoft rendeli hozzá a fejlesztői fiók egyedi azonosításához, és nem szerkeszthető.

### <a name="contact-info"></a>Kapcsolattartási adatok

A kapcsolattartási adatok szakaszban megtekintheti a **közzétevő megjelenítendő nevét**, az **eladó kapcsolattartási adatait** (a partner nevét, az e-mail-címet, a telefonszámot és a céges értékesítő címét) és a **vállalat jóváhagyójának** (a név, az e-mail és a telefonszámát). a vállalat döntéseinek jóváhagyására jogosult hatóság. 

### <a name="financial-details"></a>Pénzügyi részletek

A pénzügyi Részletek szakaszban megadhatja vagy frissítheti a pénzügyi adatokat, ha fizetős alkalmazásokat, beépülő modulokat vagy szolgáltatásokat tesz közzé. 

Ha csak az ingyenes ajánlatokat szeretné megtervezni, nem kell beállítania a kifizetési fiókot, vagy ki kell töltenie az összes adózási űrlapot. Ha később meggondolja magát, és eldönti, hogy szeretné-e eladni a Microsofton keresztül, beállíthatja a kifizetési fiókját, és kitöltheti az adózási űrlapokat. 

#### <a name="payout-account"></a>Kifizetési fiók

A kifizetési fiók az a bankszámla, amelynek a bevételeit az értékesítések küldik. Ennek a bankszámlának ugyanabban az országban kell lennie, ahol a partner Center-fiókot regisztrálta.

A kifizetési fiók beállításához hozzá kell rendelnie **a Microsoft**-fiókját:
1. A **Fiókbeállítások**területen a **pénzügyi részletek** szakaszban válassza a Microsoft- **fiók hozzárendelése**lehetőséget. 
2. Ha a rendszer kéri, jelentkezzen be Microsoft-fiókjával (MSA). Ez a fiók már nem társítható egy másik partner Center-fiókhoz. 
3. A kifizetési fiók beállításának befejezéséhez jelentkezzen be a partner Center szolgáltatásból, majd jelentkezzen be újra a Microsoft-fiókjával (nem a munkahelyi fiókjával). 

Most, hogy a Microsoft-fiókja társítva van, a kifizetési fiók hozzáadásához a következőkre lesz szüksége:
- **Válasszon fizetési módot**: Bankszámla vagy PayPal
- **Fizetési adatok hozzáadása**: Ilyen lehet például a fiók típusának kiválasztása (ellenőrzés vagy megtakarítás), a fiók tulajdonosának neve, a számlaszám és az útválasztási szám, a számlázási cím, a telefonszám vagy a PayPal e-mail-címe. \* Ha többet szeretne megtudni a PayPal használatáról a fiók fizetési módjaként, és annak megállapításához, hogy az támogatott-e a piaci régióban, tekintse meg a [PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)-információkat.

> [!IMPORTANT]
> A kifizetési fiók módosítása akár egy fizetési ciklusban is késleltetheti a befizetését. Ez a késleltetés azért fordul elő, mert ellenőrizni kell a fiók változását, ugyanúgy, mint a kifizetési fiók első beállításakor. A fiók ellenőrzése után továbbra is a teljes összegért kell fizetnie. a rendszer a jelenlegi fizetési ciklus miatt esedékes összes kifizetést hozzáadja a következőhöz.  

#### <a name="tax-profile"></a>Adózási profil

Tekintse át az aktuális adózási profil állapotát, és ellenőrizze, hogy a megfelelő **entitás típusa** és az **adóhatóság információi** láthatók-e. Válassza a **Szerkesztés** lehetőséget a szükséges űrlapok frissítéséhez vagy befejezéséhez.

Az adó állapotának megállapításához meg kell adnia a tartózkodási országát és az állampolgárságot, és el kell végeznie az országához/régiójához tartozó megfelelő adózási formákat.

A tartózkodási helytől vagy az állampolgárságtól függetlenül a Microsofton keresztül bármely ajánlat értékesítéséhez ki kell töltenie a Egyesült Államokt. Azoknak a partnereknek, akik bizonyos Egyesült Államok tartózkodási követelményeknek eleget tesznek, ki kell tölteniük egy IRS 9-es űrlapot. A Egyesült Államokon kívüli többi partnernek ki kell töltenie egy IRS-8 űrlapot. Ezeket az űrlapokat online is kitöltheti az adózási profil befejezése után.

Egy Egyesült Államok egyedi adófizető-azonosító szám (vagy ITIN) nem szükséges a Microsofttól érkező fizetések fogadására, vagy az adó-szerződésből származó előnyök igénylésére.

A partner Centerben elektronikus formában is elvégezheti és elküldheti az adó-formanyomtatványokat; a legtöbb esetben nem kell űrlapokat kinyomtatnia és e-maileket felvennie.

A különböző országok és régiók esetében eltérő az adózási követelmények. Az adó összegének pontos összege attól függ, hogy milyen országokban és régiókban vásárolja meg az ajánlatait. A Microsoft az Ön nevében egyes országokban kiadja az értékesítési és használati adót. Ezeket az országokat az ajánlat listázásának folyamatában fogjuk azonosítani. Más országokban, attól függően, hogy hol van regisztrálva, előfordulhat, hogy az értékesítési és használati adót közvetlenül a helyi adózási hatóságnak kell elvégeznie. Emellett a kapott eladások bevétele jövedelemként is adóköteles lehet. Javasoljuk, hogy lépjen kapcsolatba az országa vagy régiója megfelelő hatóságánál, amely a legjobban segít meghatározni a Microsoft értékesítési tranzakcióinak megfelelő adózási adatokat.

##### <a name="withholding-rates"></a>Visszatartási díjak
Az adózási űrlapokon beküldött adatok meghatározzák a megfelelő adó-visszatartási arányt. A visszatartási arány csak az Egyesült Államokba helyezett értékesítésekre vonatkozik; a nem USA-beli helyszínekre irányuló értékesítések nem tartoznak a forrásadó alá. A visszatartási arányok eltérőek, de a legtöbb fejlesztő számára a Egyesült Államokon kívüli regisztráció esetén az alapértelmezett érték 30%. Ha az Ön országa egy jövedelemadó-szerződéssel egyetért a Egyesült Államokával, csökkentheti ezt a mértéket.

##### <a name="tax-treaty-benefits"></a>Adózási szerződés előnyei
Ha a Egyesült Államokon kívül esik, lehetősége lesz az adózási szerződés előnyeinek kihasználására. Ezek az előnyök országonként változnak, és lehetővé tehetik, hogy csökkentse a Microsoft által visszatartásra kerülő adók mennyiségét. Az adózási szerződés előnyeit a 8BEN űrlap II. részének befejezésével veheti igénybe. Javasoljuk, hogy az Ön országában vagy régiójában lévő megfelelő erőforrásokkal kommunikáljon annak megállapításához, hogy ezek az előnyök Önre vonatkoznak-e.

[További információ a Windows app/Game Developers és az Azure Marketplace-közzétevők adózási adatairól](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Kifizetés holdjának állapota

Alapértelmezés szerint a Microsoft havi rendszerességgel küldi a kifizetéseket. Lehetősége van azonban arra is, hogy a befizetéseit megtartsa, ami megakadályozza a befizetések küldését a fiókjába. Ha úgy dönt, hogy megtartja a befizetését, továbbra is rögzítjük a beszerzett bevételt, és megadhatjuk a **nyeremények összegzésének**részleteit. Azonban addig nem küldünk fizetést a fiókjába, amíg el nem távolítja a tárolót. 

A befizetések tárolásához lépjen a **Fiókbeállítások**menüpontra. A **pénzügyi részletek**alatt, a **kifizetési Hold állapota** szakaszban kapcsolja be a csúszkát a **be**értékre. Bármikor módosíthatja a kifizetési állapotot, de vegye figyelembe, hogy a döntés a következő havi kifizetésre is hatással van. Ha például a április végét szeretné megtartani, mindenképpen a március vége előtt állítsa be a kifizetési Holding állapotát.

Miután beállította a kifizetési állapotot **a be**értékre, a rendszer minden kifizetést megtart, amíg vissza nem kapcsolja a csúszkát. Ha így tesz, a következő havi kifizetési ciklusban is szerepelni fog (ha teljesülnek az érvényes fizetési küszöbértékek). Ha például megtartotta a befizetését, de a júniusi kifizetést szeretné kinyerni, akkor a határidő lejárta előtt mindenképpen **kapcsolja ki** a kifizetési Hold állapotot.

> [!NOTE]
> A **kifizetési Holding status** Selection a Microsoft partner centeren keresztül fizetett **összes** bevételi forrásra vonatkozik, beleértve az Azure Marketplace-t, a AppSource-t, a Microsoft Storet, a reklámozást stb.). Az egyes bevételi forrásokhoz nem választhat különböző tartási állapotokat.

### <a name="devices"></a>Eszközök

Az eszközkezelés beállításai csak a UWP-közzétételre érvényesek. [További információk](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Követési GUID-azonosítók

A globálisan egyedi azonosítók (GUID azonosítók) egyedi hivatkozási számok (32 hexadecimális számjegyekkel), amelyek segítségével nyomon követheti az Azure-használatot. 

A nyomkövetési GUID azonosítók létrehozásához GUID-generátort kell használnia. Az Azure Storage csapata létrehozta a [GUID generátort](https://aka.ms/StoragePartners) , amely a megfelelő formátumú GUID-t e-mailben küldi el, és újra felhasználható a különböző nyomkövető rendszerek között.

Javasoljuk, hogy hozzon létre egyedi GUID azonosítót minden ajánlathoz és terjesztési csatornához az egyes termékekhez. Dönthet úgy, hogy egyetlen GUID azonosítót használ a termék több terjesztési csatornához, ha nem szeretné, hogy a jelentés szét legyen osztva.

Ha egy terméket egy sablon használatával helyez üzembe, és az Azure piactéren és a GitHubon is elérhető, két különálló GUID azonosítót hozhat létre és regisztrálhat:

*   Az A termék az Azure piactéren
*   A termék a GitHubon

A jelentéskészítést a partner értéke (Microsoft partner azonosítója) és a GUID-azonosítók végzik. A GUID-azonosítókat részletesebben is nyomon követheti az ajánlaton belüli egyes csomagokhoz igazítva.

További információ: az Azure- [ügyfelek használatának nyomon követése a GUID-ban – gyakori kérdések](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Többfelhasználós fiókok felügyelete

A partner Center [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure ad) használatát teszi lehetővé a többfelhasználós fiókok hozzáféréséhez és felügyeletéhez. A beléptetési folyamat részeként a szervezet Azure AD-fiókja automatikusan társítva lesz a partner Center-fiókjához. 

## <a name="manage-users"></a>Felhasználók kezelése

A partner Center **felhasználói** szakasza ( **Fiókbeállítások**alatt) az Azure ad használatával felügyelheti a partner Center-fiókhoz hozzáférő felhasználókat, csoportokat és Azure ad-alkalmazásokat. A felhasználók kezeléséhez be kell jelentkeznie a [munkahelyi fiókjával](./company-work-accounts.md) (a társított Azure ad-Bérlővel). Ha egy másik munkahelyi fiókon vagy bérlőn belül szeretné felügyelni a felhasználókat, ki kell jelentkeznie, majd újra be kell jelentkeznie egy **kezelői** engedélyekkel rendelkező felhasználóként az adott munkahelyi fiókra/bérlőre. 

Miután bejelentkezett a munkahelyi fiókjával (Azure AD-Bérlővel), a következőket teheti:
- [Felhasználók hozzáadása vagy eltávolítása](#add-or-remove-users)
- [Felhasználói jelszó módosítása](#change-a-user-password)
- [Csoportok hozzáadása vagy eltávolítása](#add-or-remove-users)
- [Azure AD-alkalmazások hozzáadása vagy eltávolítása](#add-new-azure-ad-applications)
- [Azure AD-alkalmazás kulcsainak kezelése](#manage-keys-for-an-azure-ad-application)
- [Felhasználói szerepkörök és engedélyek definiálása](#define-user-roles-and-permissions)


Ne feledje, hogy az összes partner Center-felhasználónak (beleértve a csoportokat és az Azure AD-alkalmazásokat) aktív munkahelyi fiókkal kell rendelkeznie a partner Center-fiókhoz társított [Azure ad](#manage-tenants) -bérlőben. 

### <a name="add-or-remove-users"></a>Felhasználók hozzáadása vagy eltávolítása

A fióknak [**felettes szintű**](#define-user-roles-and-permissions) engedélyekkel kell rendelkeznie ahhoz a [munkahelyi fiókhoz (Azure ad-bérlőhöz)](./company-work-accounts.md) , amelyben felhasználókat kíván hozzáadni vagy szerkeszteni.

#### <a name="add-existing-users"></a>Meglévő felhasználók hozzáadása

Felhasználók hozzáadása a partner Center-fiókhoz, amely már szerepel a vállalat [munkahelyi fiókjában (Azure ad-bérlő)](./company-work-accounts.md):

1. Nyissa meg a **felhasználók** (a **Fiókbeállítások**szakaszban), és válassza a **felhasználók hozzáadása**lehetőséget.
2. Válasszon ki egy vagy több felhasználót a megjelenő listából. A keresőmező segítségével megkeresheti az adott felhasználókat.
\* Ha egynél több felhasználót választ ki a partner Center-fiókhoz való hozzáadáshoz, akkor ugyanazt a szerepkört vagy egyéni engedélyeket kell rendelnie. Ha több felhasználót szeretne hozzáadni különböző szerepkörökhöz vagy engedélyekhez, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek készletéhez.
3.  Ha végzett a felhasználók kiválasztásával, kattintson a **kijelölt hozzáadása**lehetőségre.
5.  A **szerepkörök** szakaszban határozza meg a kiválasztott felhasználó (k) szerepkörét vagy testreszabott engedélyeit.
6.  Kattintson a **Mentés** gombra.

#### <a name="create-new-users"></a>Új felhasználók létrehozása

Új felhasználói fiókok létrehozásához [**globális rendszergazdai**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) jogosultságokkal rendelkező fiókkal kell rendelkeznie. 

1. Nyissa meg a **felhasználók** (a **Fiókbeállítások**szakaszban), válassza a **felhasználók hozzáadása**, majd az **új felhasználók létrehozása**lehetőséget.
1. Adja meg az összes új felhasználó utónevét, vezetéknevét és felhasználónevét. 
1. Ha azt szeretné, hogy az új felhasználó globális rendszergazdai fiókkal rendelkezzen a szervezet címtárában, jelölje be a felhasználó globális rendszergazdaként való megjelölése az Azure AD-ben jelölőnégyzetet az **összes címtár-erőforrás teljes körű vezérlése mellett**. Ez teljes hozzáférést biztosít a felhasználónak a vállalat Azure AD összes felügyeleti funkciójához. A szervezet munkahelyi fiókjában (Azure AD-bérlő) lévő felhasználók hozzáadhatók és kezelhetők, bár nem a partner Centerben, hacsak nem a megfelelő szerepkört/engedélyeket adja meg a fióknak. 
1. Ha bejelölte a jelölőnégyzetet, hogy a **felhasználó globális rendszergazda legyen**, **Jelszó-helyreállítási e-mailt** kell megadnia a felhasználónak, ha szükséges.
1. A **csoporttagság** szakaszban válassza ki azokat a csoportokat, amelyekhez az új felhasználót be szeretné állítani.
1. A **szerepkörök** szakaszban határozza meg a felhasználó szerepkör (eke) t vagy a testreszabott engedélyeket.
1. Kattintson a **Mentés** gombra.

Ha új felhasználót hoz létre a partner Centerben, az adott felhasználóhoz tartozó fiókot is létrehoz a munkahelyi fiókban (Azure AD-bérlőben), amelyhez be van jelentkezve. Ha módosítja egy felhasználó nevét a partner Centerben, ugyanazokat a módosításokat fogja végezni a szervezet munkahelyi fiókjában (Azure AD-bérlő).

#### <a name="invite-new-users-by-email"></a>Új felhasználók meghívása e-mailben

Ha olyan felhasználókat szeretne meghívni, akik jelenleg nem részei a munkahelyi munkahelyi fióknak (Azure AD-bérlőnek) e-mailben, [**globális rendszergazdai**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) jogosultságokkal rendelkező fiókkal kell rendelkeznie. 

1. Nyissa meg a **felhasználók** (a **Fiókbeállítások**szakaszban), válassza a **felhasználók hozzáadása**lehetőséget, majd válassza **a felhasználók meghívása e-mailben**lehetőséget.
2. Írjon be egy vagy több e-mail-címet (legfeljebb tíz), vesszővel vagy pontosvesszővel elválasztva.
3. A **szerepkörök** szakaszban határozza meg a felhasználó szerepkör (eke) t vagy a testreszabott engedélyeket.
4. Kattintson a **Mentés** gombra.

A meghívott felhasználók e-mailben meghívót kapnak a partner Center-fiókhoz való csatlakozáshoz. A rendszer létrehoz egy új vendég-felhasználói fiókot a munkahelyi fiókjában (Azure AD-bérlő). Minden felhasználónak el kell fogadnia a meghívót, mielőtt hozzáférhessen a fiókjához.

Ha újra el kell küldenie egy meghívót, látogasson el a **felhasználók** oldalra, keresse meg a meghívót a felhasználók listájában, válassza ki az e-mail-címét (vagy a meghívót *függőben lévő*szöveget). Ezután a lap alján válassza a **meghívás újraküldése**lehetőséget.
 

> [!NOTE]
> Ha a szervezete [címtár](https://go.microsoft.com/fwlink/p/?LinkID=724033) -integrációt használ a helyszíni CÍMTÁRSZOLGÁLTATÁS Azure ad-val való szinkronizálásához, nem fog tudni új felhasználókat, csoportokat vagy Azure ad-alkalmazásokat létrehozni a partner Centerben. Önnek (vagy egy másik rendszergazdának a helyszíni címtárban) közvetlenül a helyszíni címtárban kell létrehoznia őket, mielőtt látni és hozzá tudná adni őket a partner Centerben.

#### <a name="remove-a-user"></a>Felhasználó eltávolítása

Ha el szeretne távolítani egy felhasználót a munkahelyi fiókjából (Azure AD-bérlőből), lépjen a **felhasználók** (a **Fiókbeállítások**alatt) lehetőségre, válassza ki azt a felhasználót, akit el szeretne távolítani a jobb szélső oszlopban lévő jelölőnégyzetből, majd válassza az **Eltávolítás** lehetőséget az elérhető műveletek közül. Megjelenik egy előugró ablak, amely megerősíti, hogy el kívánja távolítani a kijelölt felhasználó (ka) t.

#### <a name="change-a-user-password"></a>Felhasználói jelszó módosítása

Ha az egyik felhasználónak módosítania kell a jelszavát, akkor megteheti, ha **Jelszó-helyreállítási e-mailt** adott a felhasználói fiók létrehozásakor. A felhasználó jelszavát az alábbi lépések végrehajtásával is frissítheti. Ha módosítani szeretné egy felhasználó jelszavát a munkahelyi munkahelyi fiókjában (Azure AD-bérlő), [**globális rendszergazdai**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) jogosultságokkal rendelkező fiókba kell bejelentkeznie. Vegye figyelembe, hogy ez megváltoztatja a felhasználó jelszavát az Azure AD-bérlőben, valamint a partner Center eléréséhez használt jelszót.

1.  A **felhasználók** lapon ( **Fiókbeállítások**alatt) válassza ki a szerkeszteni kívánt felhasználói fiók nevét.
2.  A lap alján kattintson a **jelszó** alaphelyzetbe állítása gombra.
3.  Ekkor megjelenik egy megerősítést kérő oldal, amely a felhasználó bejelentkezési adatait tartalmazza, beleértve az ideiglenes jelszót is. Ügyeljen arra, hogy kinyomtassa vagy átmásolja ezt az információt, és megadja azt a felhasználónak, mivel az oldal elhagyása után nem fog tudni hozzáférni az ideiglenes jelszóhoz.


## <a name="manage-groups"></a>Csoportok kezelése

A csoportok lehetővé teszik, hogy egyszerre több felhasználói szerepkört és engedélyt is vezérel.

#### <a name="add-an-existing-group"></a>Meglévő csoport hozzáadása

A szervezet munkahelyi fiókjában (Azure AD-bérlőben) már meglévő csoport hozzáadása a partner Center-fiókhoz: 

1.  A **felhasználók** lap **fiókok beállításai**területén válassza a **csoportok hozzáadása**lehetőséget.
2.  Válasszon ki egy vagy több csoportot a megjelenő listából. A keresőmező segítségével megkeresheti az adott csoportokat.
Ha egynél több csoportot választ ki a partner Center-fiókhoz való hozzáadáshoz, akkor ugyanazt a szerepkört vagy egyéni engedélyeket kell rendelnie. Ha több csoportot szeretne hozzáadni különböző szerepkörökhöz vagy engedélyekhez, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek készletéhez.
3.  Ha végzett a csoportok kiválasztásával, kattintson a **kijelölt hozzáadása**lehetőségre.
4.  A **szerepkörök** szakaszban határozza meg a kijelölt csoport (ok) szerepkör (oka) t vagy a testreszabott engedélyeket. A csoport minden tagja hozzáférhet a partner Center-fiókjához a csoportra vonatkozó engedélyekkel, az egyes fiókokhoz tartozó szerepköröktől és engedélyektől függetlenül.
5.  Kattintson a **Mentés** gombra.

Ha hozzáad egy meglévő csoportot, akkor minden felhasználó, aki tagja ennek a csoportnak, hozzáférhet a partner Center-fiókjához a csoport hozzárendelt szerepköréhez tartozó engedélyekkel.

#### <a name="add-a-new-group"></a>Új csoport hozzáadása

Új csoport hozzáadása a partner Center-fiókhoz: 

1.  A **felhasználók** lap **fiókok beállításai**területén válassza a **csoportok hozzáadása**lehetőséget.
2.  A következő lapon válassza az **új csoport**lehetőséget.
3.  Adja meg az új csoport megjelenítendő nevét.
4.  Határozza meg a csoport szerepkör (eke) t vagy a testreszabott engedélyeket. A csoport minden tagja hozzáférhet a partner Center-fiókjához az itt megadott engedélyekkel függetlenül az egyes fiókokhoz tartozó szerepköröktől/engedélyektől.
5.  Válassza ki a felhasználó (ka) t az új csoport számára a megjelenő listából. A keresőmező segítségével megkeresheti az adott felhasználókat.
6.  Ha befejezte a felhasználók kiválasztását, kattintson a **kijelöltek hozzáadása** lehetőségre az új csoport hozzáadásához.
7.  Kattintson a **Mentés** gombra.

Vegye figyelembe, hogy ez az új csoport a szervezet munkahelyi fiókjában (Azure AD-bérlő) is létrejön, nem csupán a partner Center-fiókban.

#### <a name="remove-a-group"></a>Csoport eltávolítása

Ha el szeretne távolítani egy csoportot a munkahelyi fiókjából (Azure AD-bérlőből), lépjen a **felhasználók** elemre (a **Fiókbeállítások**szakaszban), válassza ki azt a csoportot, amelyet el szeretne távolítani a jobb szélső oszlopban lévő jelölőnégyzetből, majd válassza az **Eltávolítás** lehetőséget az elérhető műveletek közül. Megjelenik egy előugró ablak, amely megerősíti, hogy el kívánja távolítani a kijelölt csoport (oka) t.

## <a name="manage-azure-ad-applications"></a>Azure AD-alkalmazások kezelése

A partner Center-fiók eléréséhez engedélyezheti a vállalat Azure AD-beli részét képező alkalmazásokat és szolgáltatásokat. 

#### <a name="add-existing-azure-ad-applications"></a>Meglévő Azure AD-alkalmazások hozzáadása 

A vállalat Azure Active Directory már meglévő alkalmazások hozzáadásához: 

1.  A **felhasználók** lapon (a **Fiókbeállítások**alatt) válassza az **Azure ad-alkalmazások hozzáadása**lehetőséget.
2.  Válasszon ki egy vagy több Azure AD-alkalmazást a megjelenő listából. A keresőmező segítségével megkeresheti az adott Azure AD-alkalmazásokat. Ha egynél több Azure AD-alkalmazást választ ki a partner Center-fiókhoz való hozzáadáshoz, akkor ugyanazt a szerepkört vagy egyéni engedélyeket kell rendelnie. Ha több Azure AD-alkalmazást szeretne hozzáadni különböző szerepkörökhöz vagy engedélyekhez, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek készletéhez.
3.  Ha végzett az Azure AD-alkalmazások kiválasztásával, kattintson a **kijelölt hozzáadása**lehetőségre.
5.  A **szerepkörök** szakaszban határozza meg a kijelölt Azure ad-alkalmazás (ok) szerepkörét vagy testreszabott engedélyeit.
6.  Kattintson a **Mentés** gombra.

#### <a name="add-new-azure-ad-applications"></a>Új Azure AD-alkalmazások hozzáadása 

Ha egy új Azure AD-alkalmazáshoz szeretne hozzáférést adni a partneri Központ számára, létrehozhat egyet a **felhasználók** szakaszban. Vegye figyelembe, hogy ez egy új fiókot hoz létre a vállalati munkahelyi fiókban (Azure AD-bérlő), nem csupán a partner Center-fiókban. Ha elsődlegesen ezt az Azure AD-alkalmazást használja a fiókpartner-hitelesítéshez, és nincs szükség arra, hogy a felhasználók közvetlenül hozzáférjenek, megadhat bármilyen érvényes címet a **Válasz URL** -címéhez és az **alkalmazás-azonosító URI azonosítóhoz**, feltéve, hogy ezeket az értékeket nem használja más Azure AD-alkalmazás a címtárban.

1.  A **felhasználók** lapon (a **Fiókbeállítások**alatt) válassza az **Azure ad-alkalmazások hozzáadása**lehetőséget.
2.  A következő lapon válassza az **új Azure ad-alkalmazás**lehetőséget.
3.  Adja meg az új Azure AD **-alkalmazás válaszának URL-címét** . Ez az URL-cím, ahol a felhasználók bejelentkezhetnek és használhatják az Azure AD-alkalmazást (más néven az alkalmazás URL-címe vagy bejelentkezési URL-címe). A **Válasz URL-címe** nem lehet hosszabb 256 karakternél, és a címtárban egyedinek kell lennie.
4.  Adja meg az új Azure AD-alkalmazáshoz tartozó **alkalmazás-azonosító URI** -t. Ez az Azure AD-alkalmazáshoz tartozó logikai azonosító, amely akkor jelenik meg, ha egy egyszeri bejelentkezésre vonatkozó kérést küld az Azure AD-nek. Vegye figyelembe, hogy az **alkalmazás-azonosító URI** -nak egyedinek kell lennie minden egyes Azure ad-alkalmazáshoz a címtárban. Ez az azonosító nem lehet hosszabb 256 karakternél. Az alkalmazás-azonosító URI-val kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5.  A **szerepkörök** szakaszban megadhatja az Azure ad-alkalmazáshoz tartozó szerepkör (eke) t vagy testreszabott engedélyeket.
6.  Kattintson a **Mentés** gombra.

Az Azure AD-alkalmazás hozzáadása vagy létrehozása után visszatérhet a **felhasználók** szakaszhoz, és kiválaszthatja az alkalmazás nevét az alkalmazás beállításainak áttekintéséhez, beleértve a bérlő azonosítóját, az ügyfél-azonosítót, a válasz URL-címét és az alkalmazás-azonosító URI-t.

#### <a name="remove-an-application"></a>Alkalmazás eltávolítása

Ha el szeretne távolítani egy alkalmazást a munkahelyi fiókjából (Azure AD-bérlőből), lépjen a **felhasználók** elemre (a **Fiókbeállítások**szakaszban), jelölje ki az eltávolítani kívánt alkalmazást a jobb szélső oszlopban található jelölőnégyzettel, majd válassza az **Eltávolítás** lehetőséget a elérhető műveletek. Megjelenik egy előugró ablak, amely megerősíti, hogy el kívánja távolítani a kijelölt alkalmazás (oka) t.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Azure AD-alkalmazás kulcsainak kezelése

Ha az Azure AD-alkalmazás adatokat olvas és ír a Microsoft Azure ADban, szüksége lesz egy kulcsra. Az Azure AD-alkalmazásokhoz kulcsokat hozhat létre a partner Centerben található információk szerkesztésével. A már nem szükséges kulcsok eltávolítására is lehetőség van.

1.  A **felhasználók** lapon (a **Fiókbeállítások**alatt) válassza ki az Azure ad-alkalmazás nevét. Megjelenik az Azure AD-alkalmazás összes aktív kulcsa, beleértve a kulcs létrehozásának dátumát és érvényességét. 
2. A már nem szükséges kulcsok eltávolításához válassza az **Eltávolítás**lehetőséget.
3.  Új kulcs hozzáadásához válassza az **új kulcs hozzáadása**lehetőséget.
4.  Ekkor megjelenik egy képernyő, amely az **ügyfél-azonosítót** és a **kulcs értékeit**mutatja. Ügyeljen arra, hogy nyomtassa ki vagy másolja ezeket az adatokat, mert a lap elhagyása után többé nem fog tudni hozzáférni.
4.  Ha további kulcsokat szeretne létrehozni, válassza a **másik kulcs hozzáadása**elemet.


## <a name="define-user-roles-and-permissions"></a>Felhasználói szerepkörök és engedélyek definiálása

A vállalat felhasználóinak a következő szerepköröket és engedélyeket rendelhetik a kereskedelmi piactér programhoz a partner Centerben:

- **Manager**
  - Elérheti az összes Microsoft-fiók funkciót az adó-és kifizetési beállítások kivételével
  - Felügyelheti a felhasználókat, a szerepköröket és a munkahelyi fiókokat (bérlőket)
- **Fejlesztői**
  - Kezelheti és közzéteheti az ajánlatokat
  - Megtekintheti a kiadói jelentéseket

> [!NOTE]
> A globális rendszergazda, az üzleti közreműködő, a pénzügyi közreműködő és a marketinges szerepkörök nem használhatók a kereskedelmi piactér programban, és nincsenek hozzájuk társított engedélyek.

További információ a szerepkörök és engedélyek kezeléséről a partner Center más területein, például a Azure Active Directory (AD), a felhőalapú megoldás-szolgáltató (CSP), a Vezérlőpult-gyártó (CPV), a vendég felhasználók vagy a Microsoft Partner Network (MPN): [felhasználói szerepkörök társítása és engedélyek a partner Centerben](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Bérlők kezelése

Egy Azure Active Directory (AD) bérlő, amelyet a dokumentációban is a "munkahelyi fióknak" nevezünk, a szervezet a Azure Portalban beállított képviselete, és segít a Microsoft Cloud Services adott példányának kezelésében a belső és külső felhasználók. Ha a szervezet előfizetett egy Microsoft Cloud Service-re (például Azure, Microsoft Intune vagy Office 365), akkor egy Azure AD-bérlő lett létrehozva. 

Több bérlőt is beállíthat a partner centerrel való használatra. A partner Center-fiókban a **Manager** szerepkörrel rendelkező felhasználók hozzáadhatnak és eltávolíthatnak Azure ad-bérlőket a fiókból.  

### <a name="add-an-existing-tenant"></a>Meglévő bérlő hozzáadása

Másik Azure AD-bérlő hozzárendelése a partner Center-fiókhoz:

1.  A **bérlők** lapon (a **Fiókbeállítások**alatt) válassza a **másik Azure ad-bérlő hozzárendelése**lehetőséget.
2. Adja meg a hozzárendelni kívánt bérlő Azure AD-beli hitelesítő adatait.
3. Tekintse át az Azure AD-bérlő szervezetének és tartománynevének nevét. A társítás befejezéséhez kattintson a **Confirm (megerősítés**) gombra.

Ha a társítás sikeres, akkor készen áll a fiók felhasználóinak hozzáadására és felügyeletére a partner Center **felhasználók** szakaszában.

### <a name="create-a-new-tenant"></a>Új bérlő létrehozása

Új Azure AD-bérlő létrehozása a partner Center-fiókkal:

1.  A **bérlők** lapon (a **Fiókbeállítások**alatt) válassza az **új Azure ad-bérlő létrehozása**lehetőséget.
2. Adja meg az új Azure AD-címtár adatait:
    - **Tartománynév**: Az Azure AD-tartományhoz és a ". onmicrosoft.com" névvel együtt használt egyedi név. Ha például a "példa" kifejezést adta meg, az Azure AD-tartománya "example.onmicrosoft.com" lesz.
    - **Kapcsolattartási e-mail cím**: E-mail-cím, ahol szükség esetén felveheti a kapcsolatot a fiókjával.
    - **Globális rendszergazdai felhasználói fiók adatai**: Az új globális rendszergazdai fiókhoz használni kívánt keresztnév, vezetéknév, Felhasználónév és jelszó.
3. A **Létrehozás** gombra kattintva erősítse meg az új tartomány és fiókadatok adatait.
4. A [felhasználók hozzáadásának és kezelésének](#manage-users)megkezdéséhez jelentkezzen be az új Azure ad globális rendszergazdai felhasználónevével és jelszavával.

Ha további információt szeretne arról, hogyan hozhat létre új bérlőket a Azure Portal belül, és nem a partneri központ portálon keresztül, tekintse meg a következő cikket: [új bérlő létrehozása](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)a Azure Active Directoryban.

### <a name="remove-a-tenant"></a>Bérlő eltávolítása

Ha el szeretne távolítani egy bérlőt a partner Center-fiókból, keresse meg a bérlők lapon található nevet a **fiók beállításai**között, majd válassza az **Eltávolítás**lehetőséget. A rendszer felszólítja, hogy erősítse meg, hogy el kívánja távolítani a bérlőt. Ha így tesz, az adott bérlő egyik felhasználója sem tud bejelentkezni a partner Center-fiókba, és a felhasználók számára konfigurált engedélyek el lesznek távolítva.

Ha eltávolít egy bérlőt, az adott bérlőtől a partner Center-fiókba felvett összes felhasználó nem fog tudni bejelentkezni a fiókba.

> [!TIP]
> Nem távolíthat el bérlőt, ha a partner központba van bejelentkezve ugyanazzal a Bérlővel. Bérlő eltávolításához be kell jelentkeznie a partner központba egy másik , a fiókhoz társított bérlőhöz. Ha a fiókhoz csak egy bérlő van társítva, akkor a bérlő csak a fiókot megnyitó Microsoft-fiók való bejelentkezés után távolítható el.


## <a name="agreements"></a>Szerződések

A partner Center **szerződések** szakasza (a **Fiókbeállítások**részben) megtekintheti az Ön által jóváhagyott közzétételi szerződések listáját. Ezek a szerződések neve és verziószáma szerint vannak felsorolva, beleértve az elfogadott dátumot és a szerződést elfogadó felhasználó nevét. 

A **szükséges műveletek** a lap tetején jelennek meg, ha vannak olyan szerződési frissítések, amelyek szükségesek a beavatkozáshoz. Egy frissített szerződés elfogadásához először olvassa el a csatolt szerződés verzióját, majd válassza a **Szerződés elfogadása**lehetőséget. 


## <a name="next-steps"></a>További lépések

- [Új SaaS-ajánlat létrehozása](./create-new-saas-offer.md)
