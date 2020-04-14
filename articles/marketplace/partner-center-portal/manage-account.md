---
title: Kereskedelmi piactéri fiók kezelése a Partnerközpontban
description: Megtudhatja, hogyan kezelhet kereskedelmi piactéri fiókot a Partnerközpontban.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 50ea56a8c743636f9fdd9105e5b07a868e71d3d0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262927"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>A kereskedelmi piactéri fiók kezelése a Partnerközpontban

Miután [létrehozott egy Partnerközpont-fiókot,](./create-account.md)kezelheti fiókját és ajánlatait a [kereskedelmi piactér irányítópultjával.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

Ebben a cikkben bemutatjuk, hogyan kezelheti partnerközpont-fiókját, többek között a következőket:

- [A Partnerközpont-fiók beállításainak elérése](#access-your-account-settings)
- [A Publisher-azonosító, a Symantec-azonosító, az eladóazonosító, a felhasználói azonosító, az MPN-azonosító és az Azure AD-bérlők megkeresése](#account-details)
- [Elérhetőségi adatok frissítése](#contact-info)
- [Nyomon követési GUID-ok beállítása a vevői használat figyelésére](#tracking-guids)
- [Felhasználók kezelése](#manage-users)
- [Csoportok kezelése](#manage-groups)
- [Azure AD-alkalmazások kezelése](#manage-azure-ad-applications)
- [Felhasználói szerepkörök és engedélyek definiálása](#define-user-roles-and-permissions)
- [Azure AD-bérlők kezelése (munkahelyi fiókok)](#manage-tenants)
- [Partnerközpont-megállapodások kezelése](#agreements)

## <a name="access-your-account-settings"></a>A fiókbeállítások elérése

Ha még nem tette meg, Önnek (vagy a szervezet rendszergazdájának) a következő helyhez kell hozzáférnie a Partnerközpont-fiók [beállításaihoz:](https://partner.microsoft.com/dashboard/account/management)
- A vállalat fiókellenőrzési állapotának ellenőrzése
- Erősítse meg a Symantec-azonosítót, az eladóazonosítót, az MPN-azonosítót, a kiadóazonosítót és a kapcsolattartási adatokat, beleértve a vállalat jóváhagyóját és az eladóelérhetőségét
- Felhasználói fiókok létrehozása mindenki számára, aki az Ön üzleti fiókját használja a Partnerközpontban

### <a name="open-developer-settings"></a>Fejlesztői beállítások megnyitása

A fiókbeállítások a [kereskedelmi piactér irányítópultjának](https://partner.microsoft.com/dashboard/commercial-marketplace) jobb felső sarkában találhatók a Partnerközpontban. Válassza a fogaskerék ikont (az irányítópult jobb felső sarkában), majd válassza a **Fejlesztői beállítások lehetőséget**.

![Fiókbeállítások menü a Partnerközpontban](./media/dashboard-developer-settings.png)

A **Fiókbeállításokon**belül megtekintheti a következőket:
- **Számlaadatok**: Számlatípus és Fiók állapot
- **Közzétevőazonosítói:** Eladóazonosító, felhasználói azonosító, publisher-azonosító, Azure AD-bérlők stb.
- **Elérhetőség**: A kiadó megjelenítendő neve, az eladó kapcsolattartójának neve, e-mail címe, telefonja és címe
- **Nyomkövetési GUID-ok:** A fiókjához kapcsolódó nyomon követési GUID-ok

### <a name="account-details"></a>Fiók adatai

A Fiók részletei szakaszban alapvető információkat láthat, például a **fiók típusát** (Vállalat vagy magánszemély) és a fiók **igazolási állapotát.** A fiókellenőrzési folyamat során ezek a beállítások minden szükséges lépést megjelenítek, beleértve az e-mail-ellenőrzést, a foglalkoztatás ellenőrzését és az üzleti ellenőrzést. Itt is frissítheti e-mailjeit, és szükség esetén újra elküldheti az ellenőrzést.

### <a name="publisher-ids"></a>Közzétevő azonosítói

A Publisher-azonosítók szakaszban láthatja a **Symantec-azonosítót**, **az eladóazonosítót**, **a felhasználói azonosítót**, az **MPN-azonosítót**és az **Azure AD-bérlőket.** Ezeket az értékeket a Microsoft a fejlesztői fiók egyedi azonosítására rendeli hozzá, és nem szerkeszthető.

### <a name="contact-info"></a>Kapcsolattartási adatok

A Kapcsolatfelvételi adatok szakaszban **láthatja a Közzétevő megjelenítendő nevét**, az **eladó elérhetőségi adatait** (a vállalat eladójának kapcsolattartónevét, e-mail címét, telefonszámát és címét), valamint a Vállalat **jóváhagyóját** (a vállalat határozatainak jóváhagyására jogosult személy nevét, e-mail címét és telefonszámát).

#### <a name="payout-account"></a>Kifizetési számla

A kifizetési számla az a bankszámla, amelyre az értékesítésből származó bevételt küldenek. Ennek a bankszámlának ugyanabban az országban kell lennie, ahol regisztrálta partnerközpontjá fiókját.

A kifizetési fiók beállításához hozzá kell **rendelnie a Microsoft-fiókját:**
1. Nyissa meg a [Kereskedelmi piactér áttekintése lapot](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) a Partnerközpontban.
2. A Profil csoportban válassza **a Microsoft-fiók társítása**lehetőséget.
3. Amikor a rendszer kéri, jelentkezzen be microsoftos fiókjával. Ez a fiók már nem társítható másik Partnerközpont-fiókkal.
4. A kifizetési fiók beállításának befejezéséhez jelentkezzen ki teljesen a Partnerközpontból, majd jelentkezzen be újra a Microsoft-fiókjával (nem pedig a munkahelyi fiókjával).

Most, hogy a Microsoft-fiók társítva van, kifizetési fiók hozzáadásához a következőkre van szükség:
- **Válassza ki a fizetési módot**: Bankszámla vagy PayPal
- **Fizetési adatok megadása**: Ide tartozhat a számlatípus (ellenőrzés vagy megtakarítás) kiválasztása, a számlatulajdonos nevének, számlaszámának, valamint útvonalszámának, számlázási címének, telefonszámának vagy PayPal e-mail címének megadása. *A PayPal fiókfizetési módként való használatáról és annak megtudásáról a [PayPal info című témakörben](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)talál további információt.

> [!IMPORTANT]
> A kifizetési számla módosítása akár egy fizetési ciklussal is késleltetheti a kifizetéseket. Ez a késedelem azért fordul elő, mert ellenőriznünk kell a számlaváltozást, ugyanúgy, mint a kifizetési számla első beállításakor. A fiók ellenőrzése után is megkapja a teljes összeg kifizetését; az aktuális fizetési ciklusra esedékes kifizetések hozzáadásra kerülnek a következő fizetési ciklushoz.  

#### <a name="tax-profile"></a>Adózási profil

Tekintse át az aktuális adózási profil állapotát, és ellenőrizze, hogy a megfelelő **entitástípus** és **adóigazolási adatok** jelennek-e meg. Válassza a **Szerkesztés** lehetőséget a szükséges űrlapok frissítéséhez vagy kitöltéséhez.

Adózási státuszának megállapításához meg kell adnia a lakóhelye és állampolgársága szerinti országot, és ki kell töltenie az országához/régiójához kapcsolódó megfelelő adózási űrlapokat.

Függetlenül a lakóhelye vagy állampolgársága szerinti országtól, ki kell töltenie az Egyesült Államok adózási űrlapjait, hogy az ajánlatokat a Microsofton keresztül értékesíthesse. Azoka partnerek, akik megfelelnek az Egyesült Államok bizonyos tartózkodási követelményeinek, ki kell tölteniük egy IRS W-9 nyomtatványt. Az Egyesült Államokon kívüli más partnereknek ki kell tölteniük az IRS W-8 nyomtatványt. Ezeket az űrlapokat az adózási profil kitöltésekéntöltse ki online.

Az Egyesült Államok egyéni adófizetői azonosító száma (vagy ITIN) nem szükséges ahhoz, hogy kifizetéseket kapjon a Microsofttól, vagy hogy adóegyezményből származó előnyöket igényeljen.

Adózási űrlapjait elektronikus úton is kitudja tölteni a Partnerközpontban; a legtöbb esetben nem kell űrlapokat nyomtatnia és postán elküldenie.

A különböző országok és régiók eltérő adózási követelményekkel rendelkeznek. Az adók pontos összege attól függ, hogy mely országokban és régiókban értékesíti az ajánlatokat. A Microsoft egyes országokban az Ön nevében átutalja az értékesítési és használati adót. Ezeket az országokat az ajánlat listázásának folyamatában azonosítjuk. Más országokban, attól függően, hogy hol van regisztrálva, előfordulhat, hogy az értékesítésés a használati adó után az értékesítéseket közvetlenül a helyi adóhatóságnak kell átutalnia. Ezen túlmenően, az értékesítési bevételt kap lehet adóköteles, mint jövedelem. Javasoljuk, hogy vegye fel a kapcsolatot az országa vagy régiója illetékes hatóságával, amely a legjobban segít meghatározni a Microsoft értékesítési tranzakcióihoz szükséges adózási adatokat.

##### <a name="withholding-rates"></a>Forrásadó-ráták
Az adózási űrlapokon elküldött adatok határozzák meg a megfelelő adóelőleg-adót. A forrásadó-ráta csak az Egyesült Államokba történő értékesítésre vonatkozik; a nem amerikai telephelyekre történő értékesítéseket nem kell visszatartani. A visszatartási arány változó, de a legtöbb, az Egyesült Államokon kívül regisztráló fejlesztők esetében az alapértelmezett arány 30%. Lehetősége van arra, hogy csökkentse ezt az arányt, ha az ön országa beleegyezett egy jövedelemadó-szerződésbe az Egyesült Államokkal.

##### <a name="tax-treaty-benefits"></a>Adóegyezmény előnyei
Ha az Egyesült Államokon kívül, akkor lehet, hogy kihasználják az adóegyezmény előnyeit. Ezek az előnyök országonként eltérőek, és lehetővé teszik a Microsoft által visszatartandó adók összegének csökkentését. A W-8BEN nyomtatvány II. Javasoljuk, hogy kommunikáljon a megfelelő erőforrásokkal az ön országában vagy régiójában annak meghatározásához, hogy ezek az előnyök vonatkoznak-e Önre.

[További információ a Windows-alkalmazások és -játékok fejlesztőinek és az Azure Marketplace-kiadóinak adózási részleteiről.](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps)

#### <a name="payout-hold-status"></a>Kifizetési visszatartás állapota

Alapértelmezés szerint a Microsoft havonta küldkifizetéseket. Azonban lehetősége van arra, hogy a kifizetéseket várakoztassa, ami megakadályozza a kifizetések küldését a számlájára. Ha úgy döntesz, hogy a kifizetéseit várakoztatod, továbbra is rögzítjük az elért bevételeket, és a részleteket a **kifizetési összefoglalódban**adjuk meg. A visszatartás eltávolításáig azonban nem küldünk kifizetéseket a fiókjába. 

A kifizetések várakoztatásához nyissa meg a **Fiókbeállítások lehetőséget.** A **Pénzügyi részletek csoportban**a **Kifizetési visszatartás állapota** csoportban kapcsolja a csúszkát **Be**állásra. Bármikor módosíthatja a kifizetési visszatartás állapotát, de ne feledje, hogy döntése hatással lesz a következő havi kifizetésre. Ha például áprilisi kifizetést szeretne tartani, akkor a kifizetési visszatartás állapotát március vége előtt állítsa **Be** állapotra.

Miután beállította a kifizetési várakoztatásállapotát **Bekapcsolva**, minden kifizetés várakoztatva lesz, amíg vissza nem kapcsolja a csúszkát **Kikapcsolva.** Ha így tesz, a következő havi kifizetési ciklusban is szerepelni fog (feltéve, hogy a vonatkozó kifizetési küszöbértékek teljesültek). Ha például a kifizetések várakoztatva vannak, de júniusban szeretnéd a kifizetést, akkor a kifizetési visszatartás állapotát május vége előtt **kapcsold ki.**

> [!NOTE]
> A **kifizetési visszatartás állapotának** kiválasztása a Microsoft Partner Centeren keresztül fizetett **összes** bevételi forrásra vonatkozik, beleértve az Azure Marketplace-t, az AppSource-t, a Microsoft Store-t, a hirdetéseket stb.). Az egyes bevételi forráshoz nem választhat különböző visszatartási állapotokat.

### <a name="devices"></a>Eszközök

Az eszközkezelési beállítások csak az UWP-közzétételre vonatkoznak. [További információ](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>GUID azonosítók követése

Globálisan egyedi azonosítók (GUID) egyedi hivatkozási számok (32 hexadecimális számjegy), amely nyomon követheti az Azure-használat. 

A nyomon követéshez guid azonosítók létrehozásához guid generátort kell használnia. Az Azure Storage csapata létrehozott egy [GUID-generátor űrlapot,](https://aka.ms/StoragePartners) amely e-mailben küldi el a megfelelő formátumú GUID-ot, és újra felhasználható a különböző követőrendszerekben.

Javasoljuk, hogy minden egyes termékhez hozzon létre egyedi GUID azonosítót minden egyes ajánlathoz és terjesztési csatornához. Ha nem szeretné, hogy a jelentésfelosztás a termék több terjesztési csatornájánként egyetlen GUID-ot használjon.

Ha egy sablon használatával telepít egy terméket, és az elérhető az Azure Marketplace-en és a GitHubon is, létrehozhat és regisztrálhat 2 különböző GUIDS-t:

*    "A" termék az Azure Piactéren
*    "A" termék a GitHubon

A jelentéskészítést a partnerérték (Microsoft Partner-azonosító) és a GUID-ok végzik. A GUID-ok részletesebb szinten is nyomon követhetőek, az ajánlaton belüli egyes tervekhez igazodva.

További információt az [Azure-ügyfelek használatának nyomon követése GUID-okkal – gyakori kérdések című témakörben talál.](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq)

## <a name="create-a-billing-profile"></a>Számlázási profil létrehozása

Ha [Dynamics 365 for Customer Engagement & Power Apps](./create-new-customer-engagement-offer.md) vagy Dynamics [365 for Operations ajánlatot](./create-new-operations-offer.md) tesz közzé, ki kell töltenie **a számlázási profilját.**

A számlázási cím előre ki van töltve a jogi személytől, és ezt a címet később frissítheti. Az ÁFA- és áfaazonosító mezők nem kötelezőek.  Az országnév és a vállalatnév nem szerkeszthető.

## <a name="multi-user-account-management"></a>Többfelhasználós fiók kezelése

A Partnercenter az [Azure Active Directoryt](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) használja a többfelhasználós fiókok eléréséhez és kezeléséhez. A szervezet Azure AD automatikusan társítva van a Partner központ fiók részeként a regisztrációs folyamat.

## <a name="manage-users"></a>Felhasználók kezelése

A **Partnerközpont Felhasználók** szakasza **(a Fiókbeállítások**területen) használja az Azure AD-t a Partnerközpont-fiókhoz hozzáféréssel rendelkező felhasználók, csoportok és Azure AD-alkalmazások kezelésére. A felhasználók kezeléséhez be kell jelentkeznie a [munkahelyi fiókjával](./company-work-accounts.md) (a társított Azure AD-bérlővel). A felhasználók kezelése egy másik munkahelyi fiók / bérlő, ki kell jelentkeznie, majd jelentkezzen be újra, mint a felhasználó **a Manager** engedélyekkel, hogy a munkahelyi fiók / bérlő.

Miután bejelentkezett a munkahelyi fiókjával (Azure AD-bérlő), a következőket teheti:
- [Felhasználók hozzáadása és eltávolítása](#add-or-remove-users)
- [Felhasználói jelszó módosítása](#change-a-user-password)
- [Csoportok hozzáadása és eltávolítása](#add-or-remove-users)
- [Azure AD-alkalmazások hozzáadása vagy eltávolítása](#add-new-azure-ad-applications)
- [Kulcsok kezelése egy Azure AD-alkalmazáshoz](#manage-keys-for-an-azure-ad-application)
- [Felhasználói szerepkörök és engedélyek definiálása](#define-user-roles-and-permissions)

Ne feledje, hogy minden Partnerközpont-felhasználónak (beleértve a csoportokat és az Azure AD-alkalmazásokat is) aktív munkahelyi fiókkal kell rendelkeznie egy [Azure AD-bérlőben,](#manage-tenants) amely a Partnerközpont-fiókhoz van társítva.

### <a name="add-or-remove-users"></a>Felhasználók hozzáadása és eltávolítása

A fióknak [**kezelőszintű**](#define-user-roles-and-permissions) engedélyekkel kell rendelkeznie ahhoz a [munkahelyi fiókhoz (Azure AD-bérlőhöz),](./company-work-accounts.md) amelyben felhasználókat szeretne hozzáadni vagy szerkeszteni.

#### <a name="add-existing-users"></a>Meglévő felhasználók hozzáadása

Felhasználók hozzáadása a partnerközpont-fiókhoz, amelyek már léteznek a vállalat [munkahelyi fiókjában (Azure AD-bérlő)](./company-work-accounts.md):

1. Nyissa meg a **Felhasználók** **(a Fiókbeállítások)** lehetőséget, és válassza **a Felhasználók hozzáadása**lehetőséget.
2. Jelöljön ki egy vagy több felhasználót a megjelenő listából. A keresőmező segítségével konkrét felhasználókat kereshet.
*Ha egynél több felhasználót választ fel a Partnerközpont-fiókhoz, ugyanazt a szerepkört vagy egyéni engedélyeket kell hozzárendelnie hozzájuk. Ha több különböző szerepkörrel/engedéllyel rendelkező felhasználót szeretne hozzáadni, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek hez.
3. Ha végzett a felhasználók kiválasztásával, kattintson a **Hozzáadás gombra.**
4. A **Szerepkörök** szakaszban adja meg a kijelölt felhasználó(k) szerepköreit vagy testreszabott engedélyeit.
5. Kattintson a **Mentés** gombra.

#### <a name="create-new-users"></a>Új felhasználók létrehozása

Új felhasználói fiókok létrehozásához [**globális rendszergazdai**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) engedélyekkel rendelkező fiókkal kell rendelkeznie. 

1. Nyissa meg a **Felhasználók** **(fiókbeállítások)** lehetőséget, válassza **a Felhasználók hozzáadása**lehetőséget, majd az Új felhasználók **létrehozása**lehetőséget.
1. Minden új felhasználónak adja meg a keresztnevet, a vezetéknevet és a felhasználónevet. 
1. Ha azt szeretné, hogy az új felhasználó globális rendszergazdai fiókkal rendelkezik a szervezet címtárában, jelölje be a **"Legyen a felhasználó globális rendszergazda az Azure AD-ben, az összes címtár-erőforrás teljes körű vezérlésével"** jelölőnégyzetet. Ez teljes hozzáférést biztosít a felhasználószámára a vállalat Azure AD-jének összes felügyeleti funkciójához. A szervezet munkahelyi fiókjában (Azure AD-bérlő) felhasználókat vehetnek fel és kezelhetnek, bár nem a Partnerközpontban, kivéve, ha megadja a fióknak a megfelelő szerepkört/engedélyeket.
1. Ha bejelölte a Felhasználó **globális rendszergazdává állítása**jelölőnégyzetjelölését, szükség esetén meg kell adnia egy **jelszó-helyreállítási e-mailt** a felhasználó számára a jelszó helyreállításához.
1. A **Csoporttagság** csoportban jelölje ki azokat a csoportokat, amelyekhez az új felhasználót tartozni szeretné.
1. A **Szerepkörök** szakaszban adja meg a felhasználó szerepköreit vagy testreszabott engedélyeit.
1. Kattintson a **Mentés** gombra.

Ha új felhasználót hoz létre a Partnerközpontban, akkor az adott felhasználó számára is létrehoz egy fiókot abban a munkahelyi fiókban (Azure AD-bérlő), amelybe be van jelentkezve. A felhasználó nevének módosítása a Partnerközpontban ugyanazokat a módosításokat fogja végrehajtani a szervezet munkahelyi fiókjában (Azure AD-bérlő).

#### <a name="invite-new-users-by-email"></a>Új felhasználók meghívása e-mailben

Ha olyan felhasználókat szeretne meghívni, akik jelenleg nem részei a vállalati munkahelyi fióknak (Azure AD-bérlő) e-mailben, [**globális rendszergazdai**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) engedélyekkel rendelkező fiókkal kell rendelkeznie.

1. Nyissa meg a **Felhasználók** **(a Fiókbeállítások)** lehetőséget, válassza **a Felhasználók hozzáadása**lehetőséget, majd a Felhasználók **meghívása e-mailben**lehetőséget.
2. Adjon meg egy vagy több e-mail címet (legfeljebb tízet), vesszővel vagy pontosvesszővel elválasztva.
3. A **Szerepkörök** szakaszban adja meg a felhasználó szerepköreit vagy testreszabott engedélyeit.
4. Kattintson a **Mentés** gombra.

A meghívott felhasználók e-mailben meghívót kapnak a Partnerközpont-fiókhoz való csatlakozásra. Egy új vendég-felhasználói fiók jön létre a munkahelyi fiókban (Azure AD-bérlő). Minden felhasználónak el kell fogadnia a meghívást, mielőtt hozzáférhetne a fiókjához.

Ha újra kell küldenie egy meghívót, látogasson el a **Felhasználók** oldalra, keresse meg a meghívót a felhasználók listájában, válassza ki az e-mail címüket (vagy a *Meghívó függőben*lévő szöveget). Ezután a lap alján válassza a **Meghívás újraküldése**lehetőséget.

> [!NOTE]
> Ha a szervezet [címtár-integrációval](https://go.microsoft.com/fwlink/p/?LinkID=724033) szinkronizálja a helyszíni címtárszolgáltatást az Azure AD-vel, nem hozhat létre új felhasználókat, csoportokat vagy Azure AD-alkalmazásokat a PartnerCenterben. Önnek (vagy a helyszíni címtár egy másik rendszergazdájának) közvetlenül a helyszíni címtárban kell létrehoznia őket, mielőtt megtekintheti és hozzáadhatja őket a Partnerközpontban.

#### <a name="remove-a-user"></a>Felhasználó eltávolítása

Ha el szeretne távolítani egy felhasználót a munkahelyi fiókjából (Azure AD-bérlő), nyissa meg a **Felhasználók** **(a Fiók beállításai)** lehetőséget, válassza ki azt a felhasználót, amelyet el szeretne távolítani a jobb szélen lévő jelölőnégyzetet, majd válassza az Eltávolítás a rendelkezésre álló műveletek közül **lehetőséget.** Megjelenik egy előugró ablak, amely megerősíti, hogy el szeretné távolítani a kijelölt felhasználó(ka)t.

#### <a name="change-a-user-password"></a>Felhasználói jelszó módosítása

Ha valamelyik felhasználónak meg kell változtatnia a jelszavát, ezt maguk is megtehetik, ha a felhasználói fiók létrehozásakor **jelszó-helyreállítási e-mailt** adott meg. A felhasználó jelszavát az alábbi lépések végrehajtásával is frissítheti. A vállalati munkahelyi fiókban (Azure AD-bérlő) egy felhasználó jelszavának módosításához [**globális rendszergazdai**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) engedélyekkel rendelkező fiókba kell bejelentkeznie. Vegye figyelembe, hogy ez módosítja a felhasználó jelszavát az Azure AD-bérlőben, valamint a Partnerközpont eléréséhez használt jelszót.

1. A **Felhasználók** lapon (a **Fiókbeállítások**csoportban) válassza ki a szerkesztendő felhasználói fiók nevét.
2. A lap alján válassza a **Jelszó alaphelyzetbe állítása** gombot.
3. Megjelenik egy megerősítő oldal, amely a felhasználó bejelentkezési adatait mutatja, beleértve az ideiglenes jelszót is. Ügyeljen arra, hogy kinyomtassa vagy másolja ezt az információt, és adja meg a felhasználónak, mivel az oldal elhagyása után nem fogja tudni elérni az ideiglenes jelszót.

## <a name="manage-groups"></a>Csoportok kezelése

A csoportok lehetővé teszik több felhasználói szerepkör és engedély együttes vezérlését.

#### <a name="add-an-existing-group"></a>Meglévő csoport hozzáadása

A szervezet munkahelyi fiókjában (Azure AD-bérlő) már meglévő csoport hozzáadása a Partnerközpont-fiókhoz:

1. A **Felhasználók** lapon (a **Fiókbeállítások**csoportban) válassza **a Csoportok hozzáadása**lehetőséget.
2. Jelöljön ki egy vagy több csoportot a megjelenő listából. A keresőmező segítségével megkeresheti az adott csoportokat.
Ha egynél több csoportot jelöl ki a Partnerközpont-fiókhoz, ugyanazt a szerepkört vagy egyéni engedélyeket kell hozzárendelnie hozzájuk. Ha több különböző szerepkörrel/engedéllyel rendelkező csoportot szeretne hozzáadni, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek hez.
3. Ha végzett a csoportok kiválasztásával, kattintson a **Hozzáadás gombra.**
4. A **Szerepkörök** szakaszban adja meg a kijelölt csoport(ok) szerepköreit vagy testreszabott engedélyeit. A csoport minden tagja hozzáférhet a Partnerközpont-fiókhoz a csoportra vonatkozó engedélyekkel, függetlenül az egyéni fiókhoz társított szerepköröktől és engedélyektől.
5. Kattintson a **Mentés** gombra.

Meglévő csoport hozzáadásakor minden olyan felhasználó, aki tagja a csoportnak, hozzáférhet a Partnerközpont-fiókhoz a csoport hozzárendelt szerepköréhez társított engedélyekkel.

#### <a name="add-a-new-group"></a>Új csoport hozzáadása

Teljesen új csoport hozzáadása a Partnerközpont-fiókhoz:

1. A **Felhasználók** lapon (a **Fiókbeállítások**csoportban) válassza **a Csoportok hozzáadása**lehetőséget.
2. A következő lapon válassza az **Új csoport lehetőséget.**
3. Adja meg az új csoport megjelenítendő nevét.
4. Adja meg a csoport szerepköreit vagy testreszabott engedélyeit. A csoport minden tagja hozzáférhet az Ön Partnerközpont-fiókjához az itt alkalmazott engedélyekkel, függetlenül az adott fiókhoz társított szerepköröktől/engedélyektől.
5. A megjelenő listából válassza ki az új csoport felhasználóit.Select user(s) for the new group from the list that appears. A keresőmező segítségével konkrét felhasználókat kereshet.
6. Ha befejezte a felhasználók kiválasztását, kattintson a **Kijelölt hozzáadás** gombra, és vegye fel őket az új csoportba.
7. Kattintson a **Mentés** gombra.

Vegye figyelembe, hogy ez az új csoport a szervezet munkahelyi fiókjában (Azure AD-bérlő) is létre jön, nem csak a Partnercenter-fiókban.

#### <a name="remove-a-group"></a>Csoport eltávolítása

Ha el szeretne távolítani egy csoportot a munkahelyi fiókjából (Azure AD-bérlő), nyissa meg a **Felhasználók** **(a Fiók beállításai)** lehetőséget, válassza ki azt a csoportot, amelyet a jobb szélen lévő jelölőnégyzetet szeretne eltávolítani, majd válassza az Eltávolítás a rendelkezésre álló műveletek közül **lehetőséget.** Megjelenik egy előugró ablak, amely megerősíti, hogy el szeretné távolítani a kijelölt csoportot(oka)t.

## <a name="manage-azure-ad-applications"></a>Azure AD-alkalmazások kezelése

Engedélyezheti, hogy a vállalat Azure AD részét beálló alkalmazások vagy szolgáltatások hozzáférjenek a Partnercenter-fiókjához.

#### <a name="add-existing-azure-ad-applications"></a>Meglévő Azure AD-alkalmazások hozzáadása

A vállalat Azure Active Directoryjában már létező alkalmazások hozzáadása:

1. A **Felhasználók** lapon (a **Fiókbeállítások**területen) válassza **az Azure AD-alkalmazások hozzáadása**lehetőséget.
2. Válasszon ki egy vagy több Azure AD-alkalmazást a megjelenő listából. A keresőmező segítségével kereshet konkrét Azure AD-alkalmazások. Ha egynél több Azure AD-alkalmazást választ a Partnerközpont-fiókhoz való hozzáadáshoz, ugyanazt a szerepkört vagy egyéni engedélyeket kell hozzárendelnie hozzájuk. Ha több Azure AD-alkalmazást szeretne hozzáadni különböző szerepkörökkel/engedélyekkel, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek készletéhez.
3. Ha befejezte az Azure AD-alkalmazások kiválasztását, kattintson a **Hozzáadás gombra.**
4. A **Szerepkörök** szakaszban adja meg a kiválasztott Azure AD-alkalmazás(ok) szerepköreit vagy testreszabott engedélyeit.
5. Kattintson a **Mentés** gombra.

#### <a name="add-new-azure-ad-applications"></a>Új Azure AD-alkalmazások hozzáadása

Ha egy vadonatúj Azure AD-alkalmazásfiókhoz szeretne hozzáférést biztosítani a Partnerközpontszámára, létrehozhat egyet a **Felhasználók** szakaszban. Vegye figyelembe, hogy ez létrehoz egy új fiókot a vállalati munkahelyi fiók (Azure AD-bérlő), nem csak a Partner Center-fiókban. Ha elsősorban ezt az Azure AD-alkalmazást használja a Partnerközpont-hitelesítéshez, és nincs szüksége a felhasználókra, hogy közvetlenül hozzáférjenek, megadhatja a **válasz URL-címének** és az **alkalmazásazonosító**URI-jának bármely érvényes címét, feltéve, hogy ezeket az értékeket nem használja más Azure AD-alkalmazás a címtárban.

1. A **Felhasználók** lapon (a **Fiókbeállítások**területen) válassza **az Azure AD-alkalmazások hozzáadása**lehetőséget.
2. A következő lapon válassza az **Új Azure AD-alkalmazás lehetőséget.**
3. Adja meg az új Azure AD-alkalmazás **válasz URL-címét.** Ez az az URL-cím, ahol a felhasználók bejelentkezhetnek és használhatják az Azure AD-alkalmazást (más néven az alkalmazás URL-címét vagy a bejelentkezési URL-t). A **válasz URL-címe** nem lehet hosszabb 256 karakternél, és egyedinek kell lennie a címtárban.
4. Adja meg az új Azure AD-alkalmazás **alkalmazásazonosító-URI-ját.** Ez egy logikai azonosító az Azure AD-alkalmazás, amely akkor jelenik meg, amikor egy egyszeri bejelentkezési kérelmet küld az Azure AD.This is a logical identifier for the Azure AD application that is presented when a single sign-on request is sent to Azure AD. Vegye figyelembe, hogy az **App ID URI** egyedinek kell lennie a címtárban minden Egyes Azure AD-alkalmazás. Ez az azonosító nem lehet hosszabb 256 karakternél. Az Alkalmazásazonosító URI-járól az [Alkalmazások integrálása](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts)az Azure Active Directoryval című témakörben talál további információt.
5. A **Szerepkörök** szakaszban adja meg a szerepkör(ek) vagy az Azure AD-alkalmazás testreszabott engedélyeit.
6. Kattintson a **Mentés** gombra.

Miután hozzáadott vagy létrehozott egy Azure AD-alkalmazást, visszatérhet a **Felhasználók** szakaszba, és kiválaszthatja az alkalmazás nevét az alkalmazás beállításainak áttekintéséhez, beleértve a bérlői azonosítót, az ügyfélazonosítót, a válasz URL-címét és az alkalmazásazonosító URI-ját.

#### <a name="remove-an-application"></a>Alkalmazás eltávolítása

Ha el szeretne távolítani egy alkalmazást a munkahelyi fiókjából (Azure AD-bérlő), nyissa meg a **Felhasználók** **(a Fiók beállításai)** lehetőséget, válassza ki azt az alkalmazást, amelyet a jobb szélen lévő jelölőnégyzetet használva el szeretne távolítani, majd válassza az Eltávolítás a rendelkezésre álló műveletek közül **lehetőséget.** Megjelenik egy előugró ablak, amely megerősíti, hogy el szeretné távolítani a kijelölt alkalmazást(oka)t.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Kulcsok kezelése egy Azure AD-alkalmazáshoz

Ha az Azure AD-alkalmazás beolvassa és írja az adatokat a Microsoft Azure AD-ben, szüksége lesz egy kulcsra. Kulcsokat hozhat létre egy Azure AD-alkalmazáshoz, ha a Partnerközpontban szerkeszti annak adatait. A már nem szükséges billentyűket is eltávolíthatja.

1. A **Felhasználók** lapon (a **Fiók beállításai**területen) válassza ki az Azure AD-alkalmazás nevét. Látni fogja az Azure AD-alkalmazás összes aktív kulcsát, beleértve a kulcs létrehozásának dátumát és annak lejártát. 
2. Ha el szeretne távolítani egy olyan kulcsot, amelyre már nincs szükség, válassza az **Eltávolítás**lehetőséget.
3. Új kulcs hozzáadásához válassza **az Új kulcs hozzáadása**lehetőséget.
4. Megjelenik egy képernyő, amelyen az **ügyfélazonosító** és a **kulcs értéke látható.** Ügyeljen arra, hogy kinyomtassa vagy másolja ezt az információt, mivel az oldal elhagyása után nem fogja tudni újra elérni azokat.
5. Ha további kulcsokat szeretne létrehozni, válassza **a Másik kulcs hozzáadása**lehetőséget.

## <a name="define-user-roles-and-permissions"></a>Felhasználói szerepkörök és engedélyek definiálása

A vállalat felhasználói a következő szerepkörökhöz és engedélyekhez rendelhetők a Partnerközpont Kereskedelmi piactér programjához:

- **Manager**
  - Az adó- és kifizetési beállítások kivételével a Microsoft-fiók összes szolgáltatásához hozzáférhet
  - Felhasználók, szerepkörök és munkahelyi fiókok (bérlők) kezelésére
- **Fejlesztői**
  - Ajánlatok kezelése és közzététele
  - Megtekinthet néhány közzétevői jelentést

> [!NOTE]
> A Commercial Marketplace program esetében a globális rendszergazda, az üzleti közreműködő, a pénzügyi közreműködő és a Marketinger szerepkörök nem használatosak. Ezeket a szerepköröket a felhasználókhoz rendelve nincs hatása. Csak a Kezelő és a Fejlesztői szerepkör ad engedélyt a felhasználóknak.

A Partnerközpont más területein, például az Azure Active Directory (AD), a felhőszolgáltató (CSP), a Vezérlőpult szállítója (CPV), a vendégfelhasználók és a Microsoft Partner Network (MPN) területeken szerepkörök és engedélyek kezeléséről további információt a [Felhasználói szerepkörök és engedélyek hozzárendelése a Partnerközpontban](https://docs.microsoft.com/partner-center/permissions-overview)című témakörben talál.

## <a name="manage-tenants"></a>Bérlők kezelése

Az Azure Active Directory (AD) bérlő, más néven a "munkahelyi fiók" a dokumentációban, az Azure Portalon beállított szervezet egy ábrázolása, és segít a Microsoft felhőszolgáltatásainak egy adott példányának kezelésében a belső és külső felhasználók számára. Ha a szervezet előfizetett egy Microsoft felhőszolgáltatásra, például az Azure-ra, a Microsoft Intune-ra vagy az Office 365-re, létrejött egy Azure AD-bérlő.

Több bérlőt is beállíthat a Partnerközponttal való használatra. A Partnerközpont-fiókkezelőszerepkörrel rendelkező felhasználók nak lehetőségük lesz Azure AD-bérlők hozzáadására és eltávolítására a fiókból. **Manager**  

### <a name="add-an-existing-tenant"></a>Meglévő bérlő hozzáadása

Másik Azure AD-bérlő társítása a Partnerközpont-fiókkal:

1. A **Bérlők** lapon (a **Fiókbeállítások**területen) válassza **a Másik Azure AD-bérlő társítása**lehetőséget.
2. Adja meg az Azure AD hitelesítő adatait a társítani kívánt bérlőhöz.
3. Tekintse át a szervezet és a tartománynevét az Azure AD-bérlő. A társítás befejezéséhez válassza a **Megerősítés gombot.**

Ha a társítás sikeres, akkor készen áll a fiókfelhasználók hozzáadására és kezelésére a Partnerközpont **Felhasználók** szakaszában.

### <a name="create-a-new-tenant"></a>Új bérlő létrehozása

Teljesen új Azure AD-bérlő létrehozása partnerközpont-fiókkal:

1. A **Bérlők** lapon (a **Fiókbeállítások**területen) válassza **az Új Azure AD-bérlő létrehozása**lehetőséget.
2. Adja meg az új Azure AD címtáradatait:
    - **Tartománynév:** Az Azure AD-tartományhoz használt egyedi név, valamint a ".onmicrosoft.com". Ha például "például" beírta, az Azure AD-tartomány "example.onmicrosoft.com" lesz.
    - **Kapcsolat e-mail**: Egy e-mail cím, ahol szükség esetén kapcsolatba léphetünk Önnel a fiókjával kapcsolatban.
    - **Globális rendszergazdai felhasználói fiók adatai**: Az új globális rendszergazdai fiókhoz használni kívánt utónév, vezetéknév, felhasználónév és jelszó.
3. Válassza a **Létrehozás** gombot az új tartomány- és fiókadatok megerősítéséhez.
4. Jelentkezzen be az új Azure AD globális rendszergazdai felhasználónévvel és jelszóval a [felhasználók hozzáadásának és kezelésének](#manage-users)megkezdéséhez.

Az Azure-portálon belüli új bérlők létrehozásáról a Partnerközpont portálon keresztül című [témakörben](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)olvashat bővebben.

### <a name="remove-a-tenant"></a>Bérlő eltávolítása

Ha el szeretne távolítani egy bérlőt a Partnerközpont-fiókból, keresse meg a nevét a **Bérlők** lapon (a **Fiókbeállítások**területen), majd válassza **az Eltávolítás lehetőséget.** A rendszer kéri, hogy erősítse meg, hogy el szeretné távolítani a bérlőt. Miután ezt megtette, a bérlő egyetlen felhasználója sem tud majd bejelentkezni a Partnerközpont-fiókba, és a felhasználókszámára beállított engedélyek törlődnek.

Bérlő eltávolításakor a Partnerközpont-fiókhoz az adott bérlőből hozzáadott összes felhasználó már nem tud bejelentkezni a fiókba.

> [!TIP]
> Nem távolíthatja el a bérlőt, ha jelenleg ugyanazon a bérlőben lévő fiók használatával jelentkezett be a Partnerközpontba. Bérlő eltávolításához be kell jelentkeznie a **Manager** Partnerközpontba egy másik, a fiókhoz társított bérlő kezelőjeként. Ha csak egy bérlő van társítva a fiókhoz, akkor a bérlő csak a fiókot megnyitva megnyitott Microsoft-fiókkal való bejelentkezés után távolítható el.

## <a name="agreements"></a>Egyezmények

A Partnerközpont **Megállapodások** szakasza (a **Fiókbeállítások**területen) tekintse meg az ön által engedélyezett közzétételi szerződések listáját. Ezek a megállapodások a név és a verziószám szerint vannak felsorolva, beleértve az elfogadás dátumát és a megállapodást elfogadó felhasználó nevét.

**A szükséges műveletek** az oldal tetején jelenhetnek meg, ha vannak olyan megállapodásfrissítések, amelyek figyelmet igényelnek. Frissített szerződés elfogadásához először olvassa el a csatolt szerződés verzióját, majd válassza **a Megállapodás elfogadása**lehetőséget.

## <a name="next-steps"></a>További lépések

- [Új SaaS-ajánlat létrehozása](./create-new-saas-offer.md)
