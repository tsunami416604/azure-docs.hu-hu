---
title: Csoportalapú licenceléstovábbi forgatókönyvek – Azure AD | Microsoft dokumentumok
description: További forgatókönyvek az Azure Active Directory csoportalapú licenceléséhez
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139d7e0cf2b57cc466dc97370b90a599257ce755
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266285"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Forgatókönyvek, korlátozások és ismert problémák, amelyek csoportok használatával kezelik a licencelést az Azure Active Directoryban

Az alábbi információk és példák segítségével pontosabbismereteket szerezhet az Azure Active Directory (Azure AD) csoportalapú licenceléséről.

## <a name="usage-location"></a>Felhasználás helye

Nem minden Microsoft-szolgáltatás érhető el minden területen. Mielőtt egy licencet hozzá lehetne rendelni egy felhasználóhoz, a rendszergazdának meg kell **adnia** a felhasználó Használati hely tulajdonságát. Az [Azure Portalon megadhatja a](https://portal.azure.com)használati helyet a **Felhasználói** &gt; **profil** &gt; **beállításai ban.**

Csoportlicenc-hozzárendelés esetén a megadott használati hely nélküli felhasználók öröklik a címtár helyét. Ha több helyen vannak felhasználók, győződjön meg arról, hogy ezt helyesen tükrözi a felhasználói erőforrásokban, mielőtt felhasználókat ad hozzá a licenccel rendelkező csoportokhoz.

> [!NOTE]
> A csoportlicenc-hozzárendelés soha nem módosítja a felhasználó meglévő használati helyértékét. Azt javasoljuk, hogy mindig adja meg a használati hely részeként a felhasználói létrehozási folyamat az Azure AD-ben (például az AAD Connect konfiguráció) - amely biztosítja, hogy a licenc-hozzárendelés eredménye mindig helyes, és a felhasználók nem kapnak szolgáltatásokat olyan helyeken, amelyek nem engedélyezettek.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Csoportalapú licencelés használata dinamikus csoportokkal

A csoportalapú licencelés bármely biztonsági csoporttal használható, ami azt jelenti, hogy kombinálható az Azure AD dinamikus csoportokkal. A dinamikus csoportok szabályokat futtatnak a felhasználói erőforrás-attribútumokkal szemben, hogy automatikusan hozzávessék és eltávolítsák a felhasználókat a csoportokból.

Létrehozhat például egy dinamikus csoportot a felhasználókhoz rendelni kívánt egyes termékcsoportokhoz. Minden csoportot egy olyan szabály tölt fel, amely a felhasználókat az attribútumaik alapján adja hozzá, és minden csoport hoz hozzá a kívánt licenceket. Az attribútumot a helyszínen rendelheti hozzá, és szinkronizálhatja az Azure AD-vel, vagy kezelheti az attribútumot közvetlenül a felhőben.

A licencek röviddel a csoporthoz való hozzáadódásuk után kerülnek hozzárendelésre a felhasználóhoz. Az attribútum módosításakor a felhasználó elhagyja a csoportokat, és eltávolítja a licenceket.

### <a name="example"></a>Példa

Fontolja meg egy helyszíni identitáskezelési megoldás példáját, amely eldönti, hogy mely felhasználók férhetnek hozzá a Microsoft webszolgáltatásokhoz. Az **Extensiontribute1 kiterjesztést** használja a felhasználó által használt licenceket képviselő karakterlánc-érték tárolására. Az Azure AD Connect szinkronizálja azt az Azure AD-vel.

Előfordulhat, hogy a felhasználóknak egy licencre van szükségük, de egy másikra, vagy mindkettőre. Íme egy példa, amelyben az Office 365 Nagyvállalati E5 és enterprise mobility + security (EMS) licenceket osztja csoportokban lévő felhasználók nak:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Nagyvállalati E5: alapszolgáltatások

![Képernyőkép az Office 365 Nagyvállalati E5-ös verzió alapszolgáltatásairól](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Vállalati mobilitás + biztonság: licencelt felhasználók

![Képernyőkép az Enterprise Mobility + Security licencelt felhasználóiról](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Ebben a példában módosítson egy felhasználót, és `EMS;E5_baseservices;` állítsa a kiterjesztéstAttribute1 értékre, ha azt szeretné, hogy a felhasználó mindkét licenccel rendelkezjen. Ezt a módosítást helyszíni legeltetésre is kiteheti. Miután a módosítás szinkronizálódik a felhővel, a felhasználó automatikusan hozzáadódik mindkét csoporthoz, és licenceket rendel hozzá.

![Képernyőkép a felhasználó bővítményének beállításárólAttribute1](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Legyen óvatos, amikor módosítja egy meglévő csoport tagsági szabályát. Ha egy szabály módosul, a rendszer újraértékeli a csoport tagságát, és eltávolítja azokat a felhasználókat, akik már nem felelnek meg az új szabálynak (az új szabálynak továbbra is megfelelő felhasználókat ez a folyamat nem érinti). Ezek a felhasználók a folyamat során eltávolítják a licenceiket, ami a szolgáltatás elvesztését vagy bizonyos esetekben adatvesztést eredményezheti.
> 
> Ha rendelkezik egy nagy dinamikus csoporttal, amelytől a licenc-hozzárendeléshez függ, fontolja meg egy kisebb tesztcsoport on-ra vonatkozó nagyobb módosítások érvényesítését, mielőtt a főcsoportra alkalmazna.

## <a name="multiple-groups-and-multiple-licenses"></a>Több csoport és több licenc

A felhasználó több licenccel rendelkező csoport tagja is lehet. Íme néhány dolog, amit figyelembe kell venni:

- Ugyanahhoz a termékhez több licenc is átfedheti egymást, és azt eredményezi, hogy az összes engedélyezett szolgáltatás a felhasználóra vonatkozik. A következő példa két licencelési csoportot mutat be: *Az E3 alapszolgáltatások* tartalmazzák az alapszolgáltatások üzembe helyezéséhez először, minden felhasználó számára. *Az E3 kiterjesztett szolgáltatások* további szolgáltatásokat (Sway és Planner) tartalmaznak, amelyekcsak egyes felhasználók számára telepíthetők. Ebben a példában a felhasználó mindkét csoporthoz hozzá lett adva:

  ![Képernyőkép az engedélyezett szolgáltatásokról](./media/licensing-group-advanced/view-enabled-services.png)

  Ennek eredményeképpen a felhasználó a termékben lévő 12 szolgáltatásból 7 engedélyezve van, miközben csak egy licencet használ ehhez a termékhez.

- Az *E3* licenc kiválasztása további részleteket jelenít meg, beleértve azt is, hogy a csoportlicenc-hozzárendelés mely szolgáltatásokat engedélyezte a felhasználónak.

## <a name="direct-licenses-coexist-with-group-licenses"></a>A közvetlen licencek a csoportlicencekkel együtt léteznek

Ha egy felhasználó licencet örököl egy csoporttól, a felhasználó tulajdonságai között nem távolíthatja el vagy módosíthatja közvetlenül a licenc-hozzárendelést. A módosításokat a csoportban kell végrehajtani, majd az összes felhasználóra propagálni kell.

Az örökölt licenc mellett azonban lehetőség van arra, hogy ugyanazt a terméklicencet közvetlenül a felhasználóhoz rendelje. A termékből további szolgáltatásokat is engedélyezhet csak egy felhasználó számára, anélkül, hogy ez hatással lenne a többi felhasználóra.

A közvetlenül hozzárendelt licencek eltávolíthatók, és nem érintik az örökölt licenceket. Fontolja meg azt a felhasználót, aki egy csoporttól örökli az Office 365 Nagyvállalati E3 licencet.

Kezdetben a felhasználó csak az *E3 alapszolgáltatások* csoportjától örökli a licencet, amely négy szolgáltatási tervet tesz lehetővé.

1. Válassza **a Hozzárendelés** lehetőséget, ha közvetlenül hozzá szeretne rendelni egy E3 licencet a felhasználóhoz. Ebben az esetben a Yammer Enterprise kivételével minden szolgáltatási tervet le fog tiltani.

    Ennek eredményeképpen a felhasználó továbbra is csak egy licencet használ az E3 termékhez. A közvetlen hozzárendelés azonban csak az adott felhasználó számára teszi lehetővé a Yammer Enterprise szolgáltatást. Megtekintheti, hogy mely szolgáltatásokat engedélyezi a csoporttagság a közvetlen hozzárendeléssel szemben.

1. Közvetlen hozzárendelés használata esetén a következő műveletek engedélyezettek:

   - A Yammer Enterprise közvetlenül kikapcsolható a felhasználói erőforráson. Az ábrán látható **Be/Ki** kapcsoló engedélyezve volt ehhez a szolgáltatáshoz, nem pedig a másik szolgáltatásváltás. Mivel a szolgáltatás közvetlenül a felhasználón van engedélyezve, módosítható.
   - A közvetlenül hozzárendelt licenc részeként további szolgáltatások is engedélyezhetők.
   - Az **Eltávolítás** gombbal eltávolíthatja a közvetlen licencet a felhasználótól. Láthatja, hogy a felhasználó most már csak az örökölt csoportlicenccel rendelkezik, és csak az eredeti szolgáltatások maradnak engedélyezve:

## <a name="managing-new-services-added-to-products"></a>A termékekhez hozzáadott új szolgáltatások kezelése

Amikor a Microsoft új szolgáltatást ad hozzá egy terméklicenc-csomaghoz, az alapértelmezés szerint engedélyezve van minden olyan csoportban, amelyhez a terméklicencet hozzárendelte. A bérlő azon felhasználói, akik előfizettek a termékváltozásokkal kapcsolatos értesítésekre, előre e-maileket kapnak, amelyek értesítik őket a közelgő szolgáltatáskiegészítésekről.

Rendszergazdaként áttekintheti a módosítás által érintett összes csoportot, és végrehajthatja a szükséges műveleteket, például letilthatja az új szolgáltatást az egyes csoportokban. Ha például csak bizonyos szolgáltatásokat célzó csoportokat hozott létre a központi telepítéshez, újra meglátogathatja ezeket a csoportokat, és meggyőződhet arról, hogy az újonnan hozzáadott szolgáltatások le vannak tiltva.

Íme egy példa arra, hogy hogyan nézhet ki ez a folyamat:

1. Eredetileg több csoporthoz rendelte hozzá az *Office 365 Nagyvállalati E5* terméket. Az *o365 E5 – Exchange* nevű csoportok egyike csak úgy lett kialakítva, hogy csak az *Exchange Online (2. csomag)* szolgáltatást engedélyezze tagjai számára.

2. Értesítést kapott a Microsofttól arról, hogy az E5 termék egy új szolgáltatással - a *Microsoft Streamtel*- bővül. Amikor a szolgáltatás elérhetővé válik a bérlőben, a következőket teheti:

3. Nyissa meg az [**Azure Active Directory > Licencek > Minden termék**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) panelt, és válassza az Office *365 Nagyvállalati E5*lehetőséget, majd válassza **a Licencelt csoportok** lehetőséget az adott termékkel rendelkező összes csoport listájának megtekintéséhez.

4. Kattintson arra a csoportra, amelyet át szeretne tekinteni (ebben az esetben az *O365 E5 - Exchange only).* Ez megnyitja a **Licencek** fület.
   > [!NOTE]
   > A *Microsoft Stream* szolgáltatás automatikusan hozzáadva és engedélyezve van ebben a csoportban, az Exchange *Online* szolgáltatás mellett:

   ![Képernyőkép a csoportlicenchez hozzáadott új szolgáltatásról](./media/licensing-group-advanced/manage-new-services.png)

5. Ha le szeretné tiltani az új szolgáltatást ebben a csoportban, kattintson a szolgáltatás melletti **Be/Ki** kapcsolóra, és a módosítás megerősítéséhez kattintson a **Mentés** gombra. Az Azure AD most feldolgozza a csoport összes felhasználóját a módosítás alkalmazásához; a csoporthoz hozzáadott új felhasználók nem engedélyezve vannak a *Microsoft Stream* szolgáltatásban.

   > [!NOTE]
   > Előfordulhat, hogy a felhasználók számára továbbra is engedélyezve van a szolgáltatás valamilyen más licenc-hozzárendelés (egy másik csoport, amelynek tagjai vagy közvetlen licenc-hozzárendelés) keresztül.

6. Szükség esetén hajtsa végre ugyanazokat a lépéseket a termékhez rendelt többi csoport esetében is.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>A PowerShell használatával megtekintheti, hogy ki rendelkezik örökölt és közvetlen licenccel
PowerShell-parancsfájl segítségével ellenőrizheti, hogy a felhasználók közvetlenül vagy egy csoporttól örökölt licenccel rendelkeznek-e.

1. Futtassa a `connect-msolservice` parancsmag hitelesítheti magát, és csatlakozzon a bérlőhöz.

2. `Get-MsolAccountSku`a bérlőben található összes kiépített terméklicenc felderítésére használható.

   ![Képernyőkép a Get-Msolaccountsku parancsmagról](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Ezzel a [PowerShell-parancsfájllal](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)használja a *FiókSkuId* értéket az önt érdeklő licenchez. Ez elkészíti azoknak a felhasználóknak a listáját, akik rendelkeznek ezzel a licenccel a licenc hozzárendelésének módjára vonatkozó információkkal.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Csoportalapú licencelési tevékenység figyelése a naplók használatával

[Az Azure AD naplózási naplóival](../reports-monitoring/concept-audit-logs.md#audit-logs) megtekintheti a csoportalapú licenceléssel kapcsolatos összes tevékenységet, beleértve a következőket:
- aki csoportoklicencét módosította
- amikor a rendszer elkezdte feldolgozni a csoportlicenc-módosítást, és amikor befejeződött
- milyen licencmódosítások történtek a felhasználónál a csoportlicenc-hozzárendelés eredményeként.

>[!NOTE]
> A naplózási naplók a portál Azure Active Directory-szakaszának legtöbb paneljén érhetők el. Attól függően, hogy hol éri el őket, a szűrők et előre alkalmazhatjuk, hogy csak a panel kontextusához kapcsolódó tevékenységeket jelenítsenek meg. Ha nem látja a várt eredményeket, vizsgálja meg [a szűrési beállításokat,](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) vagy férjen hozzá a szűretlen naplóihoz az [**Azure Active Directory > tevékenység> naplózási naplói között.**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit)

### <a name="find-out-who-modified-a-group-license"></a>A csoportlicenc módosításának kiderítése

1. Állítsa a **Tevékenység** szűrőt *a Csoportlicenc beállítása* beállításra, majd kattintson az Alkalmaz **gombra.**
2. Az eredmények tartalmazzák a csoportokon beállított vagy módosított licencek összes esetét.
   >[!TIP]
   > A csoport nevét a *Cél* szűrőbe is beírhatja az eredmények hatóköréhez.

3. Jelöljön ki egy elemet a listában a változások részleteinek megtekintéséhez. A Licenc-hozzárendelés régi és új értékeit is felsorolt *Módosított tulajdonságok* csoportban vannak felsorolva.

Íme egy példa a csoportlicenc legutóbbi változásaira, a részletekkel:

![Képernyőkép csoportlicenc módosításai](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Megtudhatja, hogy mikor kezdődtek és fejeződtek be a csoportmódosítások feldolgozása

Amikor egy licenc megváltozik egy csoporton, az Azure AD elkezdi alkalmazni a módosításokat az összes felhasználóra.

1. Ha meg szeretné tekinteni, hogy a csoportok mikor kezdték el feldolgozni, állítsa a **Tevékenység** szűrőt a *Csoportalapú licenc alkalmazásának megkezdése elemre*a felhasználók számára. Vegye figyelembe, hogy a művelet szereplője a *Microsoft Azure AD csoportalapú licencelés* – egy rendszerfiók, amely az összes csoportlicenc-módosítás végrehajtására szolgál.
   >[!TIP]
   > Kattintson a lista egyik elemére a *Módosított tulajdonságok* mező megtekintéséhez – ez mutatja a feldolgozásra felvett licencmódosításokat. Ez akkor hasznos, ha több módosítást végzett egy csoporton, és nem biztos abban, hogy melyiket dolgozták fel.

2. Hasonlóképpen, ha meg szeretné tekinteni, hogy a csoportok mikor fejezték be a feldolgozást, használja a Befejezés a *csoportalapú licenc alkalmazása a felhasználókra*szűrőértéket.
   > [!TIP]
   > Ebben az esetben a *Módosított tulajdonságok* mező tartalmazza az eredmények összegzését – ez akkor hasznos, ha gyorsan ellenőrizheti, hogy a feldolgozás hibát eredményezett-e. Példa a kimenetre:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. A csoport feldolgozásának teljes naplóját, beleértve az összes felhasználói módosítást is, állítsa be a következő szűrőket:
   - **Kezdeményezte (actor)**: "Microsoft Azure AD-csoportalapú licencelés"
   - **Dátumtartomány** (nem kötelező): egyéni tartomány arra az időpontra, amikor egy adott csoport elindult és befejeződött a feldolgozás

Ez a mintakimenet a feldolgozás kezdetét, az összes ebből eredő felhasználói módosítást és a feldolgozás befejezését mutatja.

![Képernyőkép csoportlicenc módosításai](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> A *Felhasználói licenc módosítása* elemre kattintva megjelennek az egyes felhasználókra alkalmazott licencmódosítások részletei.

## <a name="deleting-a-group-with-an-assigned-license"></a>Hozzárendelt licenccel rendelkező csoport törlése

Aktív licenccel rendelkező csoportot nem lehet törölni. A rendszergazda törölhet idáig egy olyan csoportot, amely nem veszi észre, hogy az a licencek felhasználóktól való eltávolítását okozza – ezért először a licencek eltávolítását kérünk a csoportból, mielőtt törölhető lenne.

Amikor megpróbál törölni egy csoportot az Azure Portalon, az ![ehhez hasonló hibaüzenet jelenhet meg: A képernyőkép-csoport törlése nem sikerült](./media/licensing-group-advanced/groupdeletionfailed.png)

Nyissa meg a csoport **Licencek** lapját, és nézze meg, hogy vannak-e hozzárendelt licencek. Ha igen, távolítsa el ezeket a licenceket, és próbálja meg újra törölni a csoportot.

Hasonló hibák jelenhetnek meg, amikor a PowerShell vagy a Graph API-n keresztül próbálja törölni a csoportot. Ha a helyszíni szinkronizálású csoportot használ, az Azure AD Connect hibákat is jelenthet, ha nem törli a csoportot az Azure AD-ben. Minden ilyen esetben ellenőrizze, hogy vannak-e a csoporthoz rendelt licencek, és először távolítsa el őket.

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák

Ha csoportalapú licencelést használ, érdemes megismerkedni az alábbi korlátozások kal és ismert problémákkal.

- A csoportalapú licenckezelés jelenleg nem támogatja az olyan csoportokat, amelyek más (beágyazott) csoportokat tartalmaznak. Ha egy beágyazott csoportra alkalmaz egy licencet, az csak a csoport közvetlen első szintű felhasználótagjaira lesz alkalmazva.

- A szolgáltatás csak biztonsági csoportokkal és securityEnabled=TRUE rendszerrel rendelkező Office 365-csoportokesetében használható.

- A [Microsoft 365 felügyeleti központ](https://admin.microsoft.com) jelenleg nem támogatja a csoportalapú licencelést. Ha egy felhasználó licencet örököl egy csoporttól, ez a licenc normál felhasználói licencként jelenik meg az Office felügyeleti portálon. Ha megpróbálja módosítani a licencet, vagy megpróbálja eltávolítani a licencet, a portál hibaüzenetet ad vissza. Az örökölt csoportlicencek nem módosíthatók közvetlenül a felhasználón.

- Ha egy nagy csoporthoz (például 100 000 felhasználóhoz) licenceket rendelhozzá vagy módosít, az hatással lehet a teljesítményre. Pontosabban az Azure AD-automatizálás által létrehozott módosítások mennyisége negatívan befolyásolhatja a címtár-szinkronizálás teljesítményét az Azure AD és a helyszíni rendszerek között.

- Ha dinamikus csoportokat használ a felhasználóhoz tartozó tagság kezeléséhez, ellenőrizze, hogy a felhasználó a csoport tagja-e, mert ez szükséges a licenc hozzárendeléséhez. Ha nem, [ellenőrizze dinamikus csoporthoz tartozó tagsági szabály feldolgozási folyamatának állapotát](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Bizonyos nagy terhelésesetén hosszú időbe telhet a csoportok licencmódosításának vagy a meglévő licenccel rendelkező csoportok tagsági változásainak feldolgozása. Ha úgy látja, hogy a módosítások több mint 24 órát vesznek igénybe a 60 000-es vagy annál kisebb felhasználókból álló csoportméret feldolgozásához, kérjük, [nyisson meg egy támogatási jegyet,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) amely lehetővé teszi számunkra a vizsgálat. 

- A licenckezelés automatizálása nem reagál automatikusan a környezet minden változástípusára. Előfordulhat például, hogy elfogyott a licenc, ami miatt egyes felhasználók hibaállapotban vannak. A rendelkezésre álló ülőhelyek számának felszabadításához eltávolíthat néhány közvetlenül hozzárendelt licencet más felhasználóktól. A rendszer azonban nem reagál automatikusan erre a változásra, és nem javítja ki a felhasználókat ebben a hibaállapotban.

  Az ilyen típusú korlátozások kerülő megoldásaként lépjen a **Csoport** panelaz Azure AD-ben, és kattintson **az Újrafeldolgozás gombra.** Ez a parancs feldolgozza a csoport összes felhasználóját, és ha lehetséges, feloldja a hibaállapotokat.

## <a name="next-steps"></a>További lépések

A csoportalapú licencelés segítségével folytatott licenckezelés egyéb forgatókönyveivel kapcsolatos további tudnivalókért tekintse át az alábbi témaköröket:

* [Mi a csoportalapú licencelés az Azure Active Directoryban?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](licensing-groups-migrate-users.md)
* [Felhasználók áttelepítése a terméklicencek között az Azure Active Directory csoportalapú licencelésével](../users-groups-roles/licensing-groups-change-licenses.md)
* [Példák a Csoportalapú licenceléshez az Azure Active Directoryban](../users-groups-roles/licensing-ps-examples.md)
