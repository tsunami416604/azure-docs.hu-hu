---
title: Csoportalapú licencelés további forgatókönyvek – Azure Active Directory |} A Microsoft Docs
description: További forgatókönyvek az Azure Active Directory Csoportalapú licencelés
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d2faefd8443383e7afff8e3729bf6f1cf25c3a7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887024"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Forgatókönyvek, korlátait és ismert problémák csoportok használata kezelheti az Azure Active Directory licencelése

A következő információk és példák segítségével egy Azure Active Directory (Azure AD) a Csoportalapú licencelés speciális ismereteket szerezhet.

## <a name="usage-location"></a>Felhasználás helye

Nem minden Microsoft-szolgáltatás érhető el minden területen. Egy úgy lehet licencet a felhasználóhoz, mielőtt a rendszergazda adja meg, hogy rendelkezik-e a **a felhasználási hely** tulajdonság a felhasználóra. A [az Azure Portalon](https://portal.azure.com), megadhatja a **felhasználói** &gt; **profil** &gt; **beállítások**.

A licenc-hozzárendelések bármely felhasználó felhasználás helyének megadása nélkül örökli a könyvtár helye. Ha a felhasználók több helyen van, ügyeljen arra, hogy tükrözik, amely megfelelő a felhasználói objektumok a csoportokhoz licenccel rendelkező felhasználók hozzáadása előtt.

> [!NOTE]
> A licenc-hozzárendelés soha nem módosítják egy meglévő hely értékét a felhasználó. Azt javasoljuk, hogy mivel a felhasználói létrehozásának folyamatát, amely biztosítja a licenc-hozzárendelés eredménye (pl. keresztül az AAD Connect-konfiguráció) – Azure AD-ben részét mindig megfelelő mindig beállítása a felhasználási hely, felhasználók nem kapnak szolgáltatások helyeken, amelyek nem engedélyezettek.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Használja a Csoportalapú licencelés, a dinamikus csoportok

Minden olyan biztonsági csoporttal, ami azt jelenti, hogy kombinálható az Azure AD dinamikus csoportok Csoportalapú licencelést is használhat. Dinamikus csoportok futtatni szabályok felhasználói objektum attribútumainak automatikusan adhatja hozzá, vagy távolítsa el a felhasználókat csoportok.

Például az egyes felhasználókhoz rendelni kívánt termékeket tartozó dinamikus csoportot is létrehozhat. Minden csoport fel van töltve, felhasználók hozzáadása az attribútumok szerint szabály által, és minden csoport hozzá van rendelve a licenceket, hogy szeretné kapni. Rendelje hozzá az attribútum a helyszíni és a szinkronizálás Azure ad-ben a, vagy kezelheti az attribútum közvetlenül a felhőben.

Vannak licencek rendelve a felhasználót a csoportba kerülnek, követően rövid időn belül. Az attribútum módosítás esetén a felhasználó elhagyja a csoportot, és a licencek el lesznek távolítva.

### <a name="example"></a>Példa

Vegyünk például egy helyszíni identitáskezelési megoldás, amely úgy dönt, hogy mely felhasználók rendelkezhetnek hozzáféréssel a Microsoft webszolgáltatásaihoz. Használ **extensionAttribute1** egy karakterláncértéket, amely a felhasználói licencek tárolásához. Az Azure AD Connect szinkronizálja az Azure ad-ben.

Felhasználóknak kell egy licencet, de nem egy másik, vagy mindkettő kell. Íme egy példa, amelyben terjeszti az Office 365 nagyvállalati E5 csomag és az Enterprise Mobility + Security (EMS) licencet csoportokban lévő felhasználókra:

#### <a name="office-365-enterprise-e5-base-services"></a>Az Office 365 nagyvállalati E5 csomag: alapvető szolgáltatások

![Képernyőkép az Office 365 nagyvállalati E5 csomag alap szolgáltatások](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: a licenccel rendelkező felhasználók

![Képernyőkép az Enterprise Mobility + Security-licenccel rendelkező felhasználói](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Ebben a példában egy felhasználók módosításához, és állítsa be a saját extensionAttribute1 értékéhez `EMS;E5_baseservices;` Ha azt szeretné, hogy a felhasználót, hogy mindkét licenccel rendelkezik. Ez a módosítás teheti a helyszínen. A módosítás a felhő szinkronizál, miután automatikusan hozzáadott felhasználó mindkét csoportban, és vannak licencek rendelve.

![Képernyőfelvétel: a felhasználó extensionAttribute1 beállítása](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Körültekintően járjon el, ha módosítja egy meglévő csoportot a tagsági szabály. Amikor egy szabály módosul, a csoport tagságát, újra kell értékelni, és a felhasználók, akik már nem egyezik meg az új szabály lesz eltávolítva (felhasználók továbbra is az új szabály nem lesz hatással a folyamat során egyezés). Ezek a felhasználók lesz eltávolítva a folyamat során ami szolgáltatáskimaradást, vagy bizonyos esetekben az adatvesztés licenceit.
> 
> Ha a licenc-hozzárendelések függenek nagy dinamikus csoportot, fontolja meg, mielőtt alkalmazná őket a fő csoport az egy kisebb Tesztcsoport lényegesen módosul ellenőrzése.

## <a name="multiple-groups-and-multiple-licenses"></a>Több csoportot, és egyszerre több licencet

Egy felhasználói licenccel rendelkező több csoport tagja lehet. Az alábbiakban szempontokat kell figyelembe venni:

- A termék több licencet is átfedésben, és azok alkalmazása folyamatban a felhasználó az összes engedélyezett szolgáltatások eredményez. A következő példában két licencelési csoportok: *Alapvető szolgáltatások E3* tartalmazza a foundation szolgáltatások üzembe helyezéséhez először minden felhasználó számára. És *E3 csomag, kibővített szolgáltatások* (Sway és Planner) üzembe helyezése csak egyes felhasználók számára további szolgáltatásokat tartalmazza. Ebben a példában a felhasználó mindkét csoportban lett felvéve:

  ![Képernyőkép az engedélyezett szolgáltatások](./media/licensing-group-advanced/view-enabled-services.png)

  Ennek eredményeképpen a felhasználók számára a 12 szolgáltatások 7 a termék engedélyezve van, csak egy-egy licencet a termékhez tartozó használatakor.

- Válassza a *E3* licenc további részleteket, például arról, hogy mely csoportok okozott pontosan milyen szolgáltatásokat, a felhasználók engedélyezésének információkat jeleníti meg.

  ![Képernyőfelvétel a csoport által engedélyezett szolgáltatások](./media/licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>A közvetlen licenc csoportok licenceire párhuzamos használata

Ha egy felhasználói licenccel örököl egy csoportot, nem közvetlenül eltávolítása vagy módosítása, hogy a felhasználó tulajdonságait a licenc-hozzárendelést. Módosításait kell lennie a csoport és minden felhasználó majd propagálja.

Azt azonban lehetőség, és közvetlenül hozzárendelhető az azonos terméklicenc a felhasználó az örökölt licenc mellett. További szolgáltatásokat a terméket csak egy felhasználó számára, anélkül, hogy befolyásolná a többi felhasználó engedélyezheti.

Közvetlenül hozzárendelt licencek távolítható el, és nem befolyásolják örökölt licenceket. Fontolja meg a felhasználóknak, akik az Office 365 nagyvállalati E3 csomag licencek örökli a csoportból.

1. Kezdetben csak örökölje a licenc csak a *E3 alapvető szolgáltatások* csoport, amely lehetővé teszi, hogy négy service-csomagok látható módon:

   ![Képernyőkép az E3-csoport engedélyezve van szolgáltatások](./media/licensing-group-advanced/e3-group-enabled-services.png)

2. Választhat **hozzárendelése** közvetlenül rendeljen hozzá egy E3 licencet a felhasználóhoz. Ebben az esetben fog Yammer vállalati kivételével minden szolgáltatáscsomag letiltása:

   ![Licenc hozzárendelése közvetlenül a felhasználó képernyőképe](./media/licensing-group-advanced/assign-license-to-user.png)

3. Ennek eredményeképpen a felhasználó továbbra is a E3 termék csak egy-egy licencet használ. De a közvetlen hozzárendelés lehetővé teszi, hogy az adott felhasználó csak a vállalati Yammer-szolgáltatás. Láthatja, hogy mely szolgáltatások engedélyezve vannak a csoport tagságát, és a közvetlen hozzárendelés szerint:

   ![Képernyőkép az örökölt és közvetlen hozzárendelés](./media/licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Közvetlen hozzárendelés használata esetén engedélyezett a következő műveleteket:

   - Yammer-nagyvállalati is ki van kapcsolva a user objektum közvetlenül. A **be- vagy kikapcsolása** az ábrán a váltógomb engedélyezve lett, szemben a többi szolgáltatás be-vagy kikapcsolja ezt a szolgáltatást. A szolgáltatás közvetlenül a felhasználó engedélyezett, mert módosíthatók.
   - További szolgáltatásokat is, a közvetlenül hozzárendelt licenc részeként is engedélyezhetők.
   - A **eltávolítása** gomb segítségével távolítsa el a közvetlen licenc a felhasználó elől. Láthatja, hogy a felhasználó most már a csoport az örökölt licenc, és csak az eredeti szolgáltatásokat is engedélyezett marad:

     ![Képernyőfelvétel: a közvetlen hozzárendelés eltávolítása](./media/licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Termékek hozzáadott új szolgáltatások kezelése
Ha a Microsoft termék ad hozzá egy új szolgáltatás, azt engedélyezve lesz az összes csoport, amelyhez hozzá van rendelve a termék licence alapértelmezés szerint. A bérlőn belüli felhasználók, akik termék változásokkal kapcsolatos értesítések előfizetett előre az hamarosan lezajló szolgáltatásfrissítések kiegészítésekkel kapcsolatos értesítő e-mailt fog kapni.

A rendszergazdák a változás által érintett összes csoport áttekintheti, és műveleteket végeznek, például tiltsa le az új szolgáltatást, az egyes csoportokban. Például ha célzó csak adott szolgáltatások üzembe helyezési csoportok hozta létre, is nyissa meg újra ezeket a csoportokat és győződjön meg arról, hogy újonnan hozzáadott összes szolgáltatás le van tiltva.

Íme egy példa mi nézhet ki ezt a folyamatot:

1. A hozzárendelt eredetileg, a *Office 365 nagyvállalati E5 csomag* több termék. Egy adott csoport nevű *Office 365 E5 – csak az Exchange* úgy lett kialakítva, csak engedélyezi a *Exchange Online (2. csomag)* szolgáltatást annak tagjait.

2. Értesítést kapott, amelynek a E5 termék egy új szolgáltatással - ig meghosszabbítjuk *Microsoft Stream*. Ha a szolgáltatás elérhetővé a bérlőben, a következőket teheti:

3. Nyissa meg a [ **Azure Active Directory > licencek > minden termék** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) panelhez, és válassza *Office 365 nagyvállalati E5 csomag*, majd **licenccel rendelkező csoportok** az összes csoportot a termékhez listájának megtekintéséhez.

4. Kattintson a csoport meg szeretné tekinteni a (ebben az esetben *Office 365 E5 – csak az Exchange*). Ekkor megnyílik a **licencek** fülre. E5 licenchez kattint nyílik meg egy panel az összes engedélyezett szolgáltatások listázása.
   > [!NOTE]
   > A *Microsoft Stream* szolgáltatás automatikusan hozzáadott és engedélyezve ebben a csoportban mellett a *Exchange online-hoz* szolgáltatás:

   ![Képernyőfelvétel a csoport licencre hozzáadott új szolgáltatás](./media/licensing-group-advanced/manage-new-services.png)

5. Ha szeretné letiltani az új szolgáltatást ebben a csoportban, kattintson a **be- vagy kikapcsolása** váltsa át a szolgáltatás mellett, majd kattintson a **mentése** gombra kattintva erősítse meg a módosítást. Azure ad-ben mostantól feldolgozza a alkalmazni a módosítás; csoport összes felhasználója a csoporthoz hozzáadott minden új felhasználók nem fogják tudni a *Microsoft Stream* szolgáltatás engedélyezve van.

   > [!NOTE]
   > Előfordulhat, hogy a felhasználók továbbra is fennáll a szolgáltatás néhány egyéb licenc-hozzárendelés (egy másik csoport tagjai, vagy a közvetlen licenc-hozzárendelés) keresztül engedélyezhető.

6. Szükség esetén végezzen ugyanazokat a lépéseket további csoportok hozzárendelve a termékhez.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Akik örökölte és közvetlen licenceket a PowerShell használatával
Egy PowerShell-parancsfájlt használhatja annak ellenőrzésére, ha a felhasználók közvetlenül hozzárendelt, vagy egy csoporttól örökölt licenc.

1. Futtassa a `connect-msolservice` parancsmag használatával hitelesíteni, és csatlakozzon a bérlőhöz.

2. `Get-MsolAccountSku` a bérlő összes kiépített terméklicencek felderítése használható.

   ![A Get-Msolaccountsku parancsmag képernyőképe](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Használja a *AccountSkuId* értéket az Önt érdeklő a következővel licenc [a PowerShell-szkript](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Ezzel biztosítható a hogyan van hozzárendelve a licenc információkat a licenccel rendelkező felhasználók számára listáját.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Auditnaplók segítségével figyelése a Csoportalapú licencelés

Használhat [az Azure AD auditnaplóinak](../reports-monitoring/concept-audit-logs.md#audit-logs) összes tevékenységének megtekintéséhez Csoportalapú licenceléssel kapcsolatos, többek között:
- ki módosította a csoportos licencek
- egy csoport licenc módosítása feldolgozása a rendszer indításakor és befejezésekor,
- egy csoport a licenc-hozzárendelés eredményeképpen egy felhasználó mely licencekkel kapcsolatos változások történtek.

>[!NOTE]
> Auditnaplók a legtöbb paneleket a portal Azure Active Directory szakaszában érhetők el. Attól függően, hol elérni őket, előre alkalmazott csak tevékenységeit a környezetet a panel megfelelő lehet a szűrők. Ha nem jelennek meg a kívánt eredményt, vizsgálja meg [a szűrési beállítások](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) vagy hozzáférni az szűretlen-naplók alatt [ **Azure Active Directory > tevékenység > Naplók** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Ismerje meg, akik módosított csoport licenc

1. Állítsa be a **tevékenység** szűrni *csoportlicenc beállítása* kattintson **alkalmaz**.
2. Az eredmények tartalmazzák a licencet, állítsa be vagy a csoportok a módosított összes eset.
   >[!TIP]
   > A csoport nevét is megadhatja a *cél* szűrőt, hogy az eredmények hatókörét.

3. Kattintson a változás a részletek megtekintéséhez a listanézet egyik elemére. A *módosított tulajdonságok* a licenc-hozzárendelést a régi és új értékek szerepelnek.

Íme egy példa legutóbbi csoport licencmódosítások, adatokkal:

![Képernyőfelvétel a csoport licencekkel kapcsolatos változások](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Ismerje meg, ha a csoport különböző módosításai elindult és befejeződött a feldolgozás

Licenc egy csoporttól változásakor Azure ad-ben indul el a módosítások alkalmazása minden felhasználó számára.

1. Szeretné látni, amikor csoportokat elindította, állítsa be a **tevékenység** szűrni *indítsa el a Csoportalapú licenc felhasználókra érvényes*. Vegye figyelembe, hogy a művelet az aktor *a Microsoft Azure AD biztonságicsoport-alapú licencelés* -egy rendszer az összes csoport licencmódosítások végrehajtásához használt fiók.
   >[!TIP]
   > Egy elemet a listában, kattintson a *módosított tulajdonságok* mező – bemutatja a licencekkel kapcsolatos változások, amelyek is használja a feldolgozáshoz. Ez akkor hasznos, ha egy csoport több olyan változtatásokat végzett, és nem biztos abban, hogy melyik lett feldolgozva.

2. Ehhez hasonlóan ha csoportok befejeződött-e a feldolgozási használja a szűrő értéke *felhasználók csoport alapú licenc alkalmazásának befejezése*.
   > [!TIP]
   > Ebben az esetben a *módosított tulajdonságok* mező az eredmény összefoglalását tartalmazza – ez akkor hasznos, gyorsan ellenőrizheti, ha a feldolgozási eredményezett az esetleges hibákat. Példa a kimenetre:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. A teljes naplójában hogyan csoport dolgozta, beleértve az összes felhasználó módosításait, állítsa be a következő szűrőket:
   - **Kezdeményező (szereplő)**: "A Microsoft Azure AD biztonságicsoport-alapú licencelése"
   - **Dátumtartomány** (nem kötelező): Ha egy adott csoport ismeri az egyéni tartomány elindult és befejeződött a feldolgozás

A kimeneti példa azt mutatja, hogy feldolgozás, az ebből származó felhasználói változások és a Befejezés gombra a feldolgozás megkezdése.

![Képernyőfelvétel a csoport licencekkel kapcsolatos változások](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Kapcsolódó elemek kattintva *felhasználói licenc váltása* jelennek meg a alkalmazni az egyes felhasználók licencekkel kapcsolatos változások részleteit.

## <a name="deleting-a-group-with-an-assigned-license"></a>Hozzárendelt licenccel rendelkező csoport törlése

Nincs hozzárendelve aktív licenccel rendelkező csoportok törlése lehetséges. A rendszergazda sikerült törölni a csoport nem működnek együtt az, hogy okoz, eltávolítandó felhasználók – ezért el kell távolítani a csoportból, először azt törlése előtt licencekre szükséges licencek.

Ha egy csoportot az Azure Portalon törlésének megkísérlésekor egy ehhez hasonló hibaértesítésre jelenhetnek meg: ![Képernyőfelvétel a csoport törlése sikertelen](./media/licensing-group-advanced/groupdeletionfailed.png)

Nyissa meg a **licencek** a csoport lapján, és vannak-e bármilyen licenccel. Ha igen, távolítsa el ezeket a licenceket, és próbálja meg újra törölni a csoportot.

A PowerShell vagy Graph API-csoport törlése közben hasonló hibák jelenhetnek meg. Szinkronizálja a helyi csoport használja, ha az Azure AD Connect is hibajelentést, ha nem törli a csoportot az Azure ad-ben. Minden ilyen esetben, ellenőrizze, hogy vannak-e bármilyen a csoporthoz hozzárendelt licencet, és először távolítsa el őket.

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák

Ha Csoportalapú licencelést használ, célszerű Ismerkedjen meg az alábbi listában szereplő korlátozásai és ismert problémáit.

- Csoportalapú licencelés jelenleg nem támogatja a más (beágyazott csoportok) tartalmazó csoportok. Ha egy beágyazott csoportra alkalmaz egy licencet, az csak a csoport közvetlen első szintű felhasználótagjaira lesz alkalmazva.

- A funkció csak akkor használható a biztonsági csoportok és az Office 365-csoportokat, amelyek rendelkeznek a securityEnabled = TRUE.

- A [Office 365 felügyeleti portálján](https://portal.office.com ) jelenleg nem támogatja a Csoportalapú licencelés. Ha egy felhasználói licenccel örököl egy csoportot, ez a licenc jelenik meg az Office rendszergazdai portál egy felhasználói licenccel. Ha megpróbálja módosítani a licencet, vagy próbálja meg eltávolítani a licencet, a portál hibaüzenetet ad vissza. Az örökölt csoportok licenceire közvetlenül a felhasználó nem módosítható.

- Licencek hozzárendelve, vagy módosította egy nagy méretű csoport (például 100 000 felhasználó), amikor azt sikerült hatással a teljesítményre. Pontosabban, a módosításokat az Azure AD-automatizálás által generált mennyisége negatív hatással lehet a az Azure AD közötti címtár-szinkronizálás teljesítménye és a helyszíni rendszerekben.

- Ha dinamikus csoportokat használ a felhasználóhoz tartozó tagság kezeléséhez, ellenőrizze, hogy a felhasználó a csoport tagja-e, mert ez szükséges a licenc hozzárendeléséhez. Ha nem, [ellenőrizze dinamikus csoporthoz tartozó tagsági szabály feldolgozási folyamatának állapotát](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule). 

- Nagy terhelés esetekben bizonyos csoportok licencmódosítások vagy a meglévő licenccel rendelkező csoportok csoporttagsági változások feldolgozása hosszú ideig is eltarthat. Ha megjelenik a módosítások 24 órán belül több mint 60K felhasználók méretét vagy annál kisebb, csoport feldolgozni [hozzon létre egy támogatási jegyet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) lehetővé teszi, hogy vizsgálata. 

- A Szolgáltatáskezelési automatizálás licenc automatikusan nem reagál a minden típusú változtatásokat a környezetben. Például, valószínűleg elfogyott licenceket, néhány hibás állapotú felhasználók okozza. A szabadítson fel a rendelkezésre álló munkaállomásszámot, eltávolíthatja néhány közvetlenül hozzárendelt licencek más felhasználóktól. Azonban a rendszer automatikusan ezt a módosítást reagáltak és hárítsa el a felhasználókat, hogy a hibás állapotú.

  Az ilyen jellegű korlátozások Áthidaló megoldásként nyissa meg a **csoport** panel az Azure ad-ben, és kattintson a **újrafeldolgozása**. Ez a parancs dolgozza fel a benne lévő összes felhasználó számára, és oldja fel a hibaállapotok, ha lehetséges.

## <a name="next-steps"></a>További lépések

A csoportalapú licencelés segítségével folytatott licenckezelés egyéb forgatókönyveivel kapcsolatos további tudnivalókért tekintse át az alábbi témaköröket:

* [Mit jelent a Csoportalapú licencelés az Azure Active Directoryban?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](licensing-groups-migrate-users.md)
* [Felhasználók az Azure Active Directoryban Csoportalapú licencelést használ terméklicencek közötti migrálása](../users-groups-roles/licensing-groups-change-licenses.md)
* [PowerShell forgatókönyvek Csoportalapú licenceléshez az Azure Active Directoryban](../users-groups-roles/licensing-ps-examples.md)
