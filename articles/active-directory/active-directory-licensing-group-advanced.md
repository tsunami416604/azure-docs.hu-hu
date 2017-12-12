---
title: "Az Azure Active Directory biztonságicsoport-alapú további forgatókönyvek licencelési |} Microsoft Docs"
description: "Azure Active Directory biztonságicsoport-alapú licencelési további forgatókönyvei"
services: active-directory
keywords: "Az Azure AD-licencelés"
documentationcenter: 
author: curtand
manager: mtillman
editor: piotrci
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf18076c81ecf7471771674fe40d36dba3b9866d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Forgatókönyvek, korlátozások és a csoportok használatával felügyeli a licencelés az Azure Active Directoryban ismert problémák

Használja a következő útmutatást és példákat tájékozottabbak Azure Active Directory (Azure AD) Csoportalapú licencelés eléréséhez.

## <a name="usage-location"></a>Használat helye

Egyes Microsoft-szolgáltatások nem érhetők el az összes helyen. Licenc rendelhet egy felhasználót, mielőtt a rendszergazda adja meg, hogy a **felhasználási hely** tulajdonságát a felhasználó. A [az Azure-portálon](https://portal.azure.com), megadhat **felhasználói** &gt; **profil** &gt; **beállítások**.

A licenc-hozzárendelést nélkül a megadott felhasználási hely összes felhasználója örökli a könyvtár helye. Amennyiben több helyen is rendelkezik felhasználókkal, győződjön meg arról, hogy tükrözze, amely megfelelő a felhasználói objektumok csoportokhoz licenccel rendelkező felhasználók hozzáadása előtt.

> [!NOTE]
> Licenc-hozzárendelést soha nem módosítja a felhasználó meglévő használati hely érték. Azt javasoljuk, hogy mindig be felhasználási hely, a felhasználó létrehozásának folyamata az Azure AD (pl. keresztül AAD Connect konfigurációjának) – Ez biztosítja a licenc-hozzárendelést eredményét részét mindig megfelelő, és felhasználók nem kapnak szolgáltatások helyeken, amelyek nem engedélyezettek.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>A dinamikus csoportok a csoport-alapú licenc

A biztonsági csoporttal, ami azt jelenti, hogy az Azure AD dinamikus csoportok kombinálható Csoportalapú licencelési használhatja. Dinamikus csoportok feladat futtatása felhasználó ellen, automatikusan hozzá, és távolítsa el a felhasználók csoportokból címtárobjektum-attribútumot.

Például bizonyos számú termékeket hozzárendelése felhasználókhoz szeretné a dinamikus csoportokat is létrehozhat. Felhasználók hozzáadása az attribútumok szabályok által a minden egyes csoport akkor töltődik fel tagokkal, és minden egyes csoport hozzá van rendelve a licenceket, hogy szeretné fogadni. Az attribútum a helyszíni rendelhet és szinkronizálása az Azure AD-val, vagy kezelheti közvetlenül a felhőben az attribútum.

A csoport történő hozzáadásuk után hamarosan licencet a felhasználó vannak hozzárendelve. Az attribútum módosításakor a felhasználó elhagyja a csoportot, és a licencek eltávolítását.

### <a name="example"></a>Példa

Vegyünk például egy a helyszíni identitáskezelési megoldás, amely úgy dönt, hogy mely felhasználók rendelkezzenek a Microsoft web services eléréséhez. Használja **extensionAttribute1** egy karakterláncértéket, amely a licencek, a felhasználónak rendelkeznie kell tárolni. Az Azure AD Connect szinkronizálásának az Azure ad-val.

Felhasználók szükség lehet licenc, de nem egy másik, vagy mindkettőt kell. Íme egy példa, amelyben terjeszti Office 365 nagyvállalati E5 csomag és az Enterprise Mobility + Security (EMS) licencet csoportokban lévő felhasználók számára:

#### <a name="office-365-enterprise-e5-base-services"></a>Az Office 365 nagyvállalati E5 csomag: base szolgáltatások

![Képernyőkép az Office 365 nagyvállalati E5 csomag alap szolgáltatások](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licenccel rendelkező felhasználók

![Képernyőkép az Enterprise Mobility + Security licenccel rendelkező felhasználók](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

Ebben a példában egy felhasználók módosításához, és jelölje be a extensionAttribute1 értékének `EMS;E5_baseservices;` Ha azt szeretné, hogy a felhasználó számára, hogy mindkét licenccel rendelkezik. Ez a módosítás végezheti el a helyszínen. Után a változás a felhő szinkronizál, mindkét csoport automatikusan hozzáadja a felhasználót, és a licencek vannak rendelve.

![Képernyőfelvétel: a felhasználó extensionAttribute1 beállítása](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Körültekintően járjon el, ha módosítja egy meglévő csoport tagsági szabályt. Amikor egy szabály módosul, a csoport tagsága újra kell értékelni és felhasználók, akik már nem felel meg az új szabály eltávolítása (felhasználók továbbra is a folyamat során nincs hatással az új szabály egyezés). Azoknak a felhasználóknak kell eltávolítani a folyamat során szolgáltatáskimaradást, illetve bizonyos esetekben előfordulhat, hogy eredmények adatvesztés licencét.

> Ha egy nagy dinamikus csoport függ a licenc-hozzárendelést, fontolja meg, mielőtt alkalmazná őket a fő csoport lényegesen módosul a kisebb Tesztcsoport ellenőrzése.

## <a name="multiple-groups-and-multiple-licenses"></a>Több csoportot, és több licenc

A felhasználói licencek több csoport tagja lehet. Az alábbiakban szempontokat kell figyelembe venni:

- A termék több licencet is átfedésben vannak, és alkalmazták a felhasználó az összes engedélyezett szolgáltatások eredményeznek. A következő példában két licencelési csoportok: *E3 alap szolgáltatások* tartalmazza a foundation szolgáltatások telepítéséhez először az összes felhasználóra. És *kibővített szolgáltatások E3* (lengése és Planner) érvénybe léptetése csak néhány kiegészítő szolgáltatásokat tartalmaz. Ebben a példában a felhasználó mindkét csoportban lett felvéve:

  ![Képernyőkép a engedélyezett szolgáltatások](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  A felhasználó rendelkezik-következtében a 12 szolgáltatások 7 a termék engedélyezve van, csak egy-egy licencet a termékhez tartozó használata során.

- Válassza a *E3* licenc párbeszédeket arról, hogy mely csoportok okozott engedélyezni kell a felhasználó szolgáltatásokról további részleteket jeleníti meg.

  ![Képernyőfelvétel a csoport által engedélyezett szolgáltatások](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Csoport-licenccel rendelkező lehet közvetlen licencek

Ha egy felhasználó egy licencet a csoportból, nem közvetlenül távolítsa el és nem módosíthatók, hogy a felhasználói tulajdonságok a licenc-hozzárendelést. Módosításait kell lennie a csoport és majd propagálva az összes felhasználóra.

Lehetőség, azonban ugyanazt a termék licence közvetlenül hozzárendelése a felhasználóhoz, az örökölt licenc mellett. Engedélyezheti a további szolgáltatások a terméket csak egy felhasználó, az nem befolyásolja a többi felhasználó.

Közvetlenül a hozzárendelt licenceket távolítható el, és nincsenek hatással a örökölt licenceket. Vegye figyelembe a felhasználó egy Office 365 nagyvállalati E3 csomag licenc örökli a csoportból.

1. Kezdetben a felhasználó örökli licencét csak a *E3 alapvető szolgáltatások* csoport, amely lehetővé teszi, hogy négy service-csomagokról látható módon:

  ![Képernyőfelvétel a E3 csoport engedélyezett szolgáltatások](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Kiválaszthatja **hozzárendelése** közvetlenül egy E3 licenc hozzárendelése a felhasználó. Ebben az esetben is szeretné, hogy tiltsa le a vállalati Yammer kivételével az összes service-csomagokról:

  ![Képernyőkép a licenc hozzárendelése közvetlenül a felhasználók számára](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Ennek eredményeképpen a felhasználó továbbra is használja a E3 termék csak egy-egy licencet. De rendelését lehetővé teszi, hogy az adott felhasználó csak a vállalati Yammer-szolgáltatás. Mely szolgáltatások engedélyezve vannak a csoport tagságát, és a közvetlen hozzárendelés által látható:

  ![Képernyőkép a örökölt és közvetlen hozzárendelés](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Közvetlen hozzárendelés használatakor a következő műveletek végezhetők:

  - Vállalati Yammer is ki kell kapcsolni a user objektum közvetlenül. A **be- / kikapcsolása** Váltás az ábrán engedélyezték, ehhez a szolgáltatáshoz, szemben a többi szolgáltatás be-és kikapcsolása. A szolgáltatás közvetlenül a felhasználó engedélyezett, mert módosítható.
  - Kiegészítő szolgáltatásokat is, a közvetlenül hozzárendelt licenc részeként is engedélyezhető.
  - A **eltávolítása** gomb segítségével távolítsa el a közvetlen licencet a felhasználó. Láthatja, hogy a felhasználó most már csak az örökölt csoport licenccel rendelkezik, és csak az eredeti szolgáltatások továbbra is engedélyezett:

    ![Képernyőfelvétel: közvetlen hozzárendelés eltávolítása](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Termékek hozzáadott új szolgáltatások kezelése
Ha a Microsoft termék ad hozzá egy új szolgáltatás, lesz engedélyezve, amelyekhez hozzá van rendelve a termék licence összes csoport alapértelmezés szerint. Az Ön bérelt szolgáltatásának termék módosításokkal kapcsolatos értesítéseket előfizetett felhasználók kapnak e-mailt, amely értesíti őket a közeljövőben megjelenő kiegészítéseket kapcsolatos időben.

Rendszergazdaként tekintse át a változás által érintett összes csoport és a szükséges műveletek, például minden új szolgáltatás letiltása. Például ha létrehozott csoportok célzó központi telepítés csak meghatározott szolgáltatásokat, akkor is le újra azokat a csoportokat és győződjön meg arról, hogy újonnan hozzáadott összes szolgáltatások le vannak tiltva.

Íme egy példa mi nézhet ki ezt a folyamatot:

1. A hozzárendelt eredetileg, a *Office 365 nagyvállalati E5 csomag* több termék. Ezek között nevű *O365 E5 - csak Exchange* úgy lett kialakítva, engedélyezése csak a *Exchange Online (megtervezése 2)* szolgáltatás a tagjai.

2. Értesítést kapott, amely egy új szolgáltatással - kiterjesztése a E5 termék Microsoft *Microsoft Stream*. Amikor a szolgáltatás az Ön bérelt szolgáltatásának elérhetővé válik, a következőket teheti:

3. Lépjen a [ **Azure Active Directory > licencek > összes** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) panelhez, és válassza *Office 365 nagyvállalati E5 csomag*, majd jelölje be **licenccel rendelkező csoportok** a termékhez az összes csoportok listájának megtekintéséhez.

4. Kattintson a csoport meg szeretné tekinteni a (ebben az esetben *O365 E5 - csak Exchange*). Ekkor megnyílik a **licencek** fülre. E5 licenc kattintva megnyílik egy panel, az összes engedélyezett szolgáltatások listázása.
> [!NOTE]
> A *Microsoft Stream* szolgáltatás automatikusan hozzáadott és mellett ebben a csoportban engedélyezve a *Exchange Online* szolgáltatás:

  ![Képernyőfelvétel a csoport licencre hozzáadott új szolgáltatás](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. Ha le szeretné tiltani a új szolgáltatás ennek a csoportnak, kattintson a **be- / kikapcsolása** mellett a szolgáltatás bekapcsolására, és kattintson a **mentése** gombra a módosítás megerősítéséhez. Az Azure AD most dolgozza fel a módosítás; alkalmazására a csoport összes felhasználója a csoportba felvett bármely új felhasználók nem fogják tudni a *Microsoft Stream* szolgáltatás engedélyezve van.

  > [!NOTE]
  > Előfordulhat, hogy a felhasználók továbbra is fennáll a szolgáltatás egyes más licenc-hozzárendelést (egy másik csoport tagjai, vagy egy közvetlen licenc-hozzárendelést) keresztül engedélyezhető.

6. Ha szükséges, hajtsa végre ugyanazokat a lépéseket más csoportokhoz rendelt a termékkel.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Örökölt ki, és a közvetlen licencek a PowerShell használatával
A PowerShell-parancsfájl segítségével ellenőrizze, hogy ha a felhasználók jogosultak hozzárendelt közvetlenül vagy öröklés útján egy csoporttól származik.

1. Futtassa a `connect-msolservice` parancsmag a hitelesítéshez és csatlakozáshoz a bérlő.

2. `Get-MsolAccountSku`a bérlő összes kiosztott terméklicencek felderítéséhez használható.

  ![A Get-Msolaccountsku parancsmag képernyőképe](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Használja a *AccountSkuId* érdekli, azzal a licenc értéke [a PowerShell parancsfájl](./active-directory-licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Ez a művelet létrehoz egy hogyan van hozzárendelve a licenc információkat a licenccel rendelkező felhasználók listáját.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Naplók segítségével Csoportalapú licencelési tevékenységeinek figyelése

Használhat [az Azure AD-naplók](./active-directory-reporting-activity-audit-logs.md#audit-logs) megtekintéséhez az összes tevékenység Csoportalapú licenceléssel kapcsolatos, beleértve:
- ki módosította a csoportok a licenceket
- licenc csoport módosítása a rendszer indításakor, és azt befejezése
- licenc változásait eredményeként egy licenc-hozzárendelést egy felhasználó számára.

>[!NOTE]
> Naplók a legtöbb paneleken a portál Azure Active Directory szakaszában érhetők el. Attól függően, hogy elérhetik őket szűrők lehet csak tevékenységei egyaránt megjelennek a panel a környezethez megfelelő előre életbe lép. Ha nem várt eredményt, ellenőrizze [a szűrési beállítások](./active-directory-reporting-activity-audit-logs.md#filtering-audit-logs) vagy nem érhető el a szűretlen naplókat [ **Azure Active Directory > tevékenység > Naplók**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>A csoport licenc módosítások megállapítása

1. Állítsa be a **tevékenység** kiszűrik *Set csoport licenc* kattintson **alkalmaz**.
2. Az eredmények tartalmazzák a licencek alatt állítsa be, illetve módosítása az csoportok minden esetben.
>[!TIP]
> Is beírhatja a csoport nevét a *cél* szűrőt, amely az eredmények hatókörét.

3. Kattintson a megfelelő elemre a listanézetben változásai részleteinek megtekintéséhez. A *módosított tulajdonságok* a licenc-hozzárendelést a régi és az új értékek találhatók.

Íme egy példa a legutóbbi csoport licenc módosításait, adatokkal:

![Képernyőkép csoport licenc módosításai](media/active-directory-licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Megállapítása csoport különböző módosításai elindult és feldolgozása befejeződött

Licenc módosítja a csoport, az Azure AD megkezdi a módosítások alkalmazása az összes felhasználóra.

1. Ha csoportok elindította megtekintéséhez állítsa be a **tevékenység** kiszűrik *indítsa el a felhasználók csoport licenc alkalmazása*. Vegye figyelembe, hogy a művelet a szereplő *Microsoft Azure AD Csoportalapú licencelés* – a rendszer összes csoport licenc módosítás végrehajtásához használt fiók.
>[!TIP]
> A lista elemeire a *módosított tulajdonságok* mező - feldolgozás céljából kivett licenc változtatásokat azt jeleníti meg. Ez akkor hasznos, ha egy csoport több olyan változtatásokat végzett, és nem biztos abban, hogy melyik lett feldolgozva.

2. Hasonlóképpen, amikor a csoportok feldolgozása befejeződött megtekintéséhez használja a szűrő értéke *Befejezés csoport licenc felhasználókra érvényes*.
>[!TIP]
> Ebben az esetben a *módosított tulajdonságok* mező az eredmény összefoglalását tartalmazza – ez akkor hasznos, gyorsan ellenőrizheti, ha a feldolgozási eredményezett ki a hibákat. Példa a kimenetre:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. A teljes naplójában hogyan csoport lett feldolgozva, beleértve az összes felhasználót módosítások, állítsa be a következő szűrőt:
  - **(Aktor) által kezdeményezett**: "Microsoft Azure AD Csoportalapú licencelési"
  - **Dátumtartomány** (nem kötelező): Ha egy adott felhasználócsoportnak tudja az egyéni tartomány elindult és feldolgozása befejeződött

A minta kimenet látható feldolgozási, minden eredményül kapott a felhasználók módosításai és feldolgozását a Befejezés elindítása.

![Képernyőkép csoport licenc módosításai](media/active-directory-licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Kapcsolódó elemek kattintva *módosítás felhasználói licenc* alkalmaz minden egyes felhasználó licenc módosítások információk jelennek meg.

## <a name="deleting-a-group-with-an-assigned-license"></a>A hozzárendelt licenccel rendelkező csoport törlése

Nincs lehetőség egy aktív licenccel rendelkező csoport törlése. A rendszergazda sikerült törölni a csoport nem az, hogy az okoz, ezért kérjük előtt el kell távolítani a csoportból, előbb törölhető licencekre eltávolítsa őket a felhasználók - licencek megvalósításához.

Az Azure portálon csoport törlése közben megjelenik egy ehhez hasonló Hibaértesítések: ![képernyőkép csoport törlése sikertelen](media/active-directory-licensing-group-advanced/groupdeletionfailed.png)

Lépjen a **licencek** a csoport lapján, és vannak-e bármilyen licenccel. Ha igen, távolítsa el ezeket a licenceket, és próbálja meg újból törölni a csoport.

Hasonló hibák jelenhet meg a PowerShell vagy a Graph API csoport törlése közben. Ha egy helyszíni szinkronizált csoportot használ, az Azure AD Connect esetén a csoport törlése az Azure ad-ben nem is hibák előfordulhat, hogy jelentést. Minden ilyen esetben ügyeljen arra, hogy ellenőrizze, hogy van-e a csoport minden licenccel, és először távolítsa el őket.

## <a name="limitations-and-known-issues"></a>A korlátozásokkal, valamint az ismert problémák

Csoportalapú licencelési használatakor célszerű Ismerkedjen meg az alábbi listán szereplő korlátozások és ismert problémákat.

- Jelenleg Csoportalapú licencelési nem támogatja a más (beágyazott csoportok) tartalmazó csoportok. Ha egy beágyazott csoportok licencet alkalmaz, csak a azonnali első szintű felhasználó a csoport rendelkezik alkalmazott licencek.

- A szolgáltatás csak a biztonsági csoportok használatával használható. Office-csoportok jelenleg nem támogatottak, és csak akkor használhatja azokat a hozzárendelési folyamat.

- A [Office 365 felügyeleti portál](https://portal.office.com ) jelenleg nem támogatja a licencelési biztonságicsoport-alapú. Ha a felhasználó licenc örököl egy csoportot, ez a licenc jelenik meg az Office felügyeleti portál normál felhasználói licencet. Ha megpróbálja módosítani a licencet, vagy próbálja meg eltávolítani a licenc, a portál hibaüzenetet ad vissza. Örökölt csoport licencek nem módosíthatók közvetlenül a felhasználó.

- Ha a felhasználó el lesz távolítva a csoportból, és elveszíti a licencfeltételeket, a service-csomagokról adott licencből (például a SharePoint Online) értékre a **felfüggesztett** állapotát. A service-csomagok nincsenek beállítva az utolsó, letiltott állapotba. Ez okokból véletlen eltávolítását felhasználói adatokat, elkerülheti, ha egy rendszergazda hibásan tagsági kezelése.

- Licencek hozzárendelése vagy módosításakor a nagy méretű csoport (például 100 000 felhasználó), jelentős hatással lehet a teljesítmény. Pontosabban, az Azure AD-automatizálás által végrehajtott módosítások mennyisége előfordulhat, hogy negatívan a címtár-szinkronizálás az Azure AD között teljesítményét és a helyszíni rendszereket.

- Bizonyos nagy terhelés helyzetekben licenc feldolgozási később, és módosítja hozzáadására/eltávolítására a csoport például licenc vagy a felhasználók hozzáadása/eltávolítása a csoportból, feldolgozandó hosszú ideig eltarthat. Ha megjelenik a módosítások érvénybe feldolgozni, adjon 24 óránál hosszabb [támogatási jegy megnyitása](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) számára lehetővé teszik a számunkra, hogy kivizsgálja a Microsofttal. A Microsoft javítja a teljesítményt nyújt a szolgáltatás előtt *általánosan rendelkezésre álló*.

- Licenc Szolgáltatáskezelés-automatizálás automatikusan reagál a minden típusú változtatásokat a környezetben. Például akkor valószínűleg elfogyott licencek, amely bizonyos felhasználók hibás állapotú. A szabadítson fel a rendelkezésre álló ülések száma eltávolíthat néhány közvetlenül hozzárendelt licencek más felhasználókkal. Azonban a rendszer automatikusan ezt a változtatást reagálnak, és javítsa ki a felhasználók az adott hiba állapotban.

  Az ilyen jellegű korlátozások megoldás, lépjen a **csoport** panel az Azure ad-ben, és kattintson a **újból feldolgozza**. Ez a parancs dolgozza fel, hogy a csoport összes felhasználója, és oldja fel a hiba állapotok, ha lehetséges.

- Csoportalapú licencelési nem rögzíti a hibákat, ha a licenc nem lehet hozzárendelni egy felhasználó egy duplikált proxy cím konfigurációja miatt az Exchange Online; Ezek a felhasználók rendszer eltekint a licenc-hozzárendelést. Azonosítása és a probléma megoldásához kapcsolatos további információkért lásd: [ebben a szakaszban](./active-directory-licensing-group-problem-resolution-azure-portal.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online).

## <a name="next-steps"></a>Következő lépések

Más esetekben a Licenckezelés Csoportalapú licenceléssel kapcsolatos további információkért lásd:

* [Mi az a csoport-alapú licencelése az Azure Active Directoryban?](active-directory-licensing-whatis-azure-portal.md)
* [Licencek hozzárendelése az Azure Active Directory csoport](active-directory-licensing-group-assignment-azure-portal.md)
* [Majd azonosítani és megoldani az Azure Active Directory csoport licenc problémák](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési egyedi licenccel rendelkező felhasználók áttelepítése](active-directory-licensing-group-migration-azure-portal.md)
