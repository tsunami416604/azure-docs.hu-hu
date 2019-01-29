---
title: 'Az Azure AD Connect szinkronizálása: Olyan konfigurációs módosítást az Azure AD Connect-szinkronizálás |} A Microsoft Docs'
description: Ismerteti, hogyan lehet módosítani a konfigurálást az Azure AD Connect szinkronizálása.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 8c6fe9952beb8daeb9b96cf9244cccf85d9c2203
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165455"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Az Azure AD Connect szinkronizálása: Módosítsa az alapértelmezett konfiguráció
Ez a cikk az a célja, hogy végigvezetik az Azure Active Directory (Azure AD) Connect-szinkronizálás az alapértelmezett konfiguráció módosításához. Néhány gyakori helyzet biztosítja a lépéseket. A Tudásbázis a képes egyszerű módosítja a saját konfigurációjával, a saját üzleti szabályok alapján kell lennie.

> [!WARNING]
> Ha módosítja az alapértelmezett szinkronizálási szabályok majd ezeket a módosításokat felülírja a következő frissítésekor az Azure AD Connect, nem várt és nagy valószínűséggel nem kívánt szinkronizálási eredmények eredményez.
>
> A beépített szinkronizálási szabályokat rendelkezik egy ujjlenyomatot. Ha egy módosítást hajt végre ezeket a szabályokat, az ujjlenyomat nem egyeznek meg. A jövőben, ha a alkalmazni az Azure AD Connect új kiadása próbál problémák léphetnek fel. Csak módosításokat a leírt módon ebben a cikkben.

## <a name="synchronization-rules-editor"></a>Synchronization Rules Editor
A szinkronizálási Szabályszerkesztővel szolgál, és az alapértelmezett konfiguráció módosítása. Találja meg a **Start** menüt a **az Azure AD Connect** csoport.  
![Szinkronizálási Szabályszerkesztő a Start menüben](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Amikor megnyitja a szerkesztő, tekintse meg az alapértelmezett beépített szabályokat.

![Szinkronizálási Szabályszerkesztő](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigálás a szerkesztőben
Használja a legördülő listákból felső részén a szerkesztő, gyorsan megtalálhatja az adott szabályra. Például ha azt szeretné, a szabályok megtekintéséhez, ahol a attribútum proxyAddresses megtalálható, módosíthatja a legördülő listákból a következőhöz:  
![SRE szűrése](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Szűrés alaphelyzetbe, és a friss konfiguráció betöltése, nyomja le az F5 billentyűt.

A jobb felső sarokban a rendszer a **új szabály hozzáadása** gombra. Ez a gomb segítségével hozzon létre saját egyéni szabályt.

Alsó olyan gombokat a kiválasztott szinkronizálási szabály alapján. **Szerkesztés** és **törlése** várt módon működnek őket. **Exportálás** hoz létre egy PowerShell-szkript hozza létre újra a szinkronizálási szabály. Ezzel az eljárással egy szinkronizálási szabály áthelyezheti az egyik kiszolgálóról a másikra.

## <a name="create-your-first-custom-rule"></a>Az első egyéni szabály létrehozása
A leggyakoribb változások vannak, az attribútumfolyamok. Az adatok a forráskönyvtárban nem lehet ugyanaz, mint az Azure ad-ben. Ebben a szakaszban a példában győződjön meg arról, a felhasználó utóneve mindig a *tulajdonnévnek megfelelő*.

### <a name="disable-the-scheduler"></a>Az ütemező letiltása
A [scheduler](how-to-connect-sync-feature-scheduler.md) alapértelmezés szerint 30 percenként fut. Ellenőrizze, hogy nem indul, módosítása és hibáinak elhárítása az új szabályokat. Az ütemező ideiglenes letiltásához indítsa el a Powershellt, és futtassa `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Az ütemező letiltása](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>A szabály létrehozása
1. Kattintson a **új szabály hozzáadása**.
2. Az a **leírás** oldalon, írja be a következőket:  
   ![Bejövő szabály szűrése](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Név**: Adjon meg egy leíró nevet a szabálynak.
   * **Leírás**: Adjon meg bizonyos tisztázására, hogy valaki más is ismertetése a szabály a.
   * **Csatlakoztatott rendszer**: Ez az, hogy a rendszer, amely az az objektum található. Ebben az esetben válasszon **Active Directory-összekötő**.
   * **Csatlakoztatott rendszer/Metaverzum-objektum típusaként**: Válassza ki **felhasználói** és **személy**, illetve.
   * **Hivatkozás típusa**: Módosítsa ezt az értéket **csatlakozzon**.
   * **Elsőbbségi**: Adjon meg egy értéket, amely egyedi a rendszerben. Alacsonyabb numerikus érték azt jelzi, hogy nagyobb prioritással.
   * **Címke**: Ezt a mezőt hagyja üresen. A Microsoft csak out-of-box szabályok rendelkeznie kell értékkel kitölti ezt a jelölőnégyzetet.
3. Az a **Scoping szűrő** lap, adja meg **givenName ISNOTNULL**.  
   ![Bejövő szabály a hatókör-beállítási szűrője](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Ez a szakasz segítségével határozza meg, hogy mely objektumok a szabály vonatkozik. Ha az üres, a szabályt alkalmazni szeretné az összes felhasználói objektum. Azonban, amely magában foglalja a konferenciahívások szolgáltatásfiókok és egyéb nem személyek felhasználói objektumok.
4. Az a **szabályok csatlakozzon** lapon, hagyja üresen a mezőt.
5. Az a **átalakítások** lapon, majd **FlowType** való **kifejezés**. A **célattribútum**válassza **givenName**. És a **forrás**, adja meg **PCase([givenName])**.
   ![Bejövő szabály átalakítások](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   A szinkronizálási motor kis-és nagybetűket a függvény nevét és az attribútum neve. Ha valami hiba történt, akkor megjelenik egy figyelmeztetés a szabály hozzáadásakor. Mentheti, és továbbra is, de kell nyissa meg újra, és javítsa ki a szabályt.
6. Kattintson a **Hozzáadás** a szabály mentéséhez.

A rendszer az új egyéni szabályt kell látható, a szinkronizálási szabályoknak.

### <a name="verify-the-change"></a>A Módosítás megerősítése
Az új módosítása szeretné ellenőrizze, hogy a várt módon működik, és nem szűrész esetleges hibákat. Objektumok rendelkezik számától függően kétféleképpen, erre a lépésre szükség:

- Futtassa a teljes szinkronizálás összes objektumon.
- Előzetes verzió és a teljes szinkronizálás futtatása egy önálló objektumon.

Nyissa meg a **szinkronizálási szolgáltatás** származó a **Start** menü. A jelen szakaszban ismertetett lépések minden szerepelnek, ezzel az eszközzel.

**Teljes szinkronizálás az összes objektum**  

   1. Válassza ki **összekötők** tetején. Azonosítsa az összekötőre, amelyet az előző szakaszban (ebben az esetben az Active Directory Domain Services) módosította, és kattintson rá. 
   2. A **műveletek**válassza **futtatása**.
   3. Válassza ki **teljes szinkronizálást**, majd válassza ki **OK**.
   ![Teljes szinkronizálás](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Most frissülnek az objektumok a metaverzumba. Ellenőrizheti a módosításokat a metaverzumban található objektum megtekintésével.

**Előzetes verzió és a teljes szinkronizálás az egyetlen objektum**  

   1. Válassza ki **összekötők** tetején. Azonosítsa az összekötőre, amelyet az előző szakaszban (ebben az esetben az Active Directory Domain Services) módosította, és kattintson rá.
   2. Válassza ki **Összekötőtér keresési**. 
   3. Használjon **hatókör** megkeresni egy objektumot, amelyet szeretne tesztelheti a módosítást. Jelölje ki az objektumot, és kattintson a **előzetes**. 
   4. Válassza ki az új képernyőn **véglegesítése előzetes**.  
   ![Előzetes verzió véglegesítése](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   A változás most elkötelezte magát a metaverzumba.

**Az objektum megtekintése a metaverzumban**  

1. Válasszon ki néhány példa az objektumok ellenőrizze, hogy az értéket várt és, hogy a szabály a alkalmazni. 
2. Válassza ki **keresés a Metaverzumban** a lista elejéről. Bármely, meg kell keresnie a kívánt objektumok szűrőt ad meg. 
3. Nyissa meg a keresési eredmények egy objektumot. Tekintse meg az attribútumértékek, és a is ellenőrizheti a **szinkronizálási szabályok** oszlopot, amely a szabály alkalmazása a vártnak.  
![Keresés a metaverzumban](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Az ütemező engedélyezése
Minden rendben van a várt módon, az ütemező újra engedélyezheti. A PowerShellben futtassa a `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Más közös folyamat attribútummódosításai
Az előző szakasz ismertette, hogyan módosíthatja egy Attribútumfolyam. Ez a szakasz néhány további példa állnak rendelkezésre. A szinkronizálási szabály létrehozása lépései rövidítése van, de az előző szakaszban találhatja meg a teljes lépéseket.

### <a name="use-an-attribute-other-than-the-default"></a>Használja az alapértelmezett eltérő attribútum
A Fabrikam ilyenkor nincs egy erdőben, ahol a helyi ábécé használatos Utónév, Vezetéknév és megjelenítendő nevét. Ezek az attribútumok Latin karakter ábrázolása a bővítményattribútumok található. Az Azure ad-ben listája kiépítéséhez egy globális címet, és Office 365, a szervezet szeretné használni ezeket az attribútumokat.

Egy objektumot a helyi erdő alapértelmezett konfigurációval, a következőhöz hasonló:  
![Attribútumfolyam 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Hozzon létre egy szabályt a többi attribútumfolyamok, tegye a következőket:

1. Nyissa meg a **szinkronizálási Szabályszerkesztővel** származó a **Start** menü.
2. A **bejövő** ki van jelölve, a bal oldalon, kattintson a **új szabály hozzáadása** gombra.
3. Adjon a szabálynak egy nevet és leírást. Válassza ki a helyszíni Active Directory-példányból, és a kapcsolódó objektumtípusokat. A **hivatkozás típusa**válassza **csatlakozzon**. A **elsőbbséget**, válasszon ki egy szám, amely nem használja másik szabállyal. Az out-of-box-szabályok első 100, így 50 értéke ebben a példában is használható.
  ![Attribútumfolyam 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Hagyja **Scoping szűrő** üres. (Azaz alkalmaznia kell az erdő összes felhasználói objektum.)
5. Hagyja **szabályok csatlakozzon** üres. (Azt jelenti, legyen az out-of-box szabály illesztések kezelésére.)
6. A **átalakítások**, a következő folyamatok létrehozása:  
  ![Attribútumfolyam 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Kattintson a **Hozzáadás** a szabály mentéséhez.
8. Lépjen a **Synchronization Service Managert**. A **összekötők**, válassza ki az összekötőt, amelyikhez hozzáadta a szabályt. Válassza ki **futtatása**, majd válassza ki **teljes szinkronizálást**. A teljes szinkronizálás összes objektum újraszámítja az aktuális szabályok használatával.

Ez az ugyanahhoz az objektumhoz, az az egyéni szabály eredménye:  
![Attribútumfolyam 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Attribútumok hossza
Karakterlánc-attribútumok indexelhető alapértelmezés szerint, és a hossza legfeljebb 448 karakter lehet. Ha több előfordulhat, hogy tartalmazó karakterlánc-attribútumok dolgozik, ügyeljen arra, hogy az Attribútumfolyam a következők:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>A userPrincipalSuffix módosítása
A userPrincipalName attribútum az Active Directory a felhasználók mindig nem ismert, és lehetséges, hogy nem felel meg a bejelentkezési azonosítót. Az Azure AD Connect szinkronizálási telepítővarázsló választhat egy másik attribútum – például *mail*. De egyes esetekben az attribútum ki kell számítani.

Például a Contoso vállalat két Azure AD-címtár, egyet a termelési és tesztelési rendelkezik. A felhasználók a bejelentkezési azonosító egy másik utótag használata a tesztelési célú bérlői szeretnének:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Ebben a kifejezésben igénybe mindent az első bal oldali @-sign (a Word) és a egy rögzített karakterlánccal ÖSSZEFŰZ.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Egy többértékű attribútum átalakítása egyetlen érték
Egyes attribútumok az Active Directory rendszer többértékű adatelemeket a sémában, annak ellenére, hogy az Active Directory – felhasználók és számítógépek egyértékű megtekintése. Ilyen például, a leírás attribútum:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Ebben a kifejezésben, ha az attribútum értéke, igénybe az első elem (*elem*) távolítsa el az attribútum a kezdő és záró szóközök (*Trim*), és ezután munkafüzetei mindig az első 448 karaktereket (*balra* ) a karakterláncban.

### <a name="do-not-flow-an-attribute"></a>Az attribútum nem flow
A forgatókönyv esetében ez a szakasz a háttér-információkért lásd: [szabályozhatja az attribútum folyamatot](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Az attribútum nem flow két módon lehet. Az első az a telepítési varázsló használatával [távolítsa el a kiválasztott attribútumok](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Ez a beállítás akkor működik, ha még nem szinkronizálta az attribútum előtt. Azonban ha ezt az attribútumot szinkronizálja, és később ezzel a funkcióval eltávolításához elindította, a szinkronizálási motor leáll, az attribútumot és a már létező értékei kezelése van hátra az Azure ad-ben.

Ha szeretne eltávolítani egy attribútum értékét, és ellenőrizze, hogy a jövőben nem terjeszthetők, kell létrehoz egy egyéni szabályt.

Fabrikam ilyenkor rájöttünk rendelkeznie arról, hogy az attribútumokat, hogy szinkronizálása a felhőbe némelyike nem lehet van. Szeretnénk, hogy ezek az attribútumok törlődnek, az Azure ad-ből.  
![A bővítményattribútumok hibás](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Hozzon létre egy új bejövő szinkronizálási szabály, és töltse ki a leírást.
  ![Leírások](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Hozzon létre az attribútumfolyamok **kifejezés** a **FlowType** és **AuthoritativeNull** a **forrás**. A konstans **AuthoritativeNull** azt jelzi, hogy az érték lehet üres a metaverzumban, még akkor is, ha alacsonyabb-elsőbbséget szinkronizálási szabály megpróbálja töltse fel az értéket.
  ![A bővítményattribútumok átalakításában](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. A szinkronizálási szabály mentéséhez. Indítsa el a **szinkronizálási szolgáltatás**, keresse meg az összekötő, válassza ki **futtatása**, majd válassza ki **teljes szinkronizálást**. Ezt a lépést minden attribútumfolyamok újraszámítja.
4. Ellenőrizze, hogy a kívánt módosításokat az Összekötőtérben kereséssel exportálásra váró adatokat.
  ![A kétlépcsős delete](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Szabályok létrehozása a PowerShell használatával
A szinkronizálási szabály szerkesztővel jól működik, akkor, ha csak néhány változtatást, győződjön meg arról, hogy. Ha sok módosításokat kell, a PowerShell jobb megoldás lehet. Néhány speciális funkció csak érhetők el a PowerShell használatával.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>A PowerShell-parancsprogram beszerzése az out-of-box szabályok
Tekintse meg a PowerShell parancsfájl által létrehozott egy out-of-box szabályt, a szinkronizálási szabályok szerkesztőben válassza ki a szabályt, majd kattintson a **exportálása**. Ez a művelet biztosítja, hogy a szabály létrehozása a PowerShell-parancsfájlt.

### <a name="advanced-precedence"></a>Speciális sorrend
Indítsa el az out-of-box szinkronizálási szabályok a sorrend értéke 100. Ha több erdővel rendelkezik, és számos egyéni módosításokat kell, majd 99 szinkronizálási szabályok nem elegendő.

A szinkronizálási motor, amely előtt az out-of-box szabályok további szabályokat szeretne találhatók. Ez a viselkedés lekéréséhez kövesse az alábbi lépéseket:

1. Jelölje meg az első out-of-box szinkronizálási szabály (**az AD-felhasználó csatlakozhat a**) a szinkronizálási szabályszerkesztővel, és válasszon **exportálása**. Másolja az SR-azonosító értékét.  
![PowerShell módosítása előtt](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Az új szinkronizálási szabály létrehozása. A szinkronizálási szabályszerkesztővel segítségével hozza létre. A szabály PowerShell-parancsfájlba exportálni.
3. A tulajdonság a **PrecedenceBefore**, helyezze be az azonosító értékét az out-of-box szabályból. Állítsa be a **elsőbbséget** való **0**. Ellenőrizze, hogy az azonosító attribútum egyedi, és, hogy nem újbóli felhasználása egy másik szabály származó GUID Azonosítóval. Győződjön meg arról is, amely a **ImmutableTag** tulajdonság nincs beállítva. Ez a tulajdonság csak egy out-of-box szabályt kell állítani.
4. Mentse a PowerShell-szkriptet, és futtathatja. Eredménye, hogy az egyéni szabály nincs hozzárendelve a sorrend értéke 100, és minden egyéb out-of-box szabály eggyel növeli.  
![PowerShell módosítás után](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Számos egyéni szinkronizálási szabályok használatával azonos rendelkezhet **PrecedenceBefore** értéke, ha szükséges.

## <a name="enable-synchronization-of-usertype"></a>UserType szinkronizálásának engedélyezése
Azure AD Connect szinkronizálásának támogatja a **UserType** az attribútum **felhasználói** verziójában 1.1.524.0 objektumok és újabb verziók. Pontosabban a következő módosításokat vezettek be:

- Az objektumtípus sémája **felhasználói** az Azure AD-összekötőben ki van bővítve a UserType attribútuma, amely a karakterlánc típusú, és egyértékű tartalmazza.
- Az objektumtípus sémája **személy** metaverzumban található ki van bővítve a UserType attribútuma, amely a karakterlánc típusú, és egyértékű tartalmazza.

Alapértelmezés szerint a UserType attribútuma nincs engedélyezve a szinkronizáláshoz, mert nincs megfelelő UserType attribútuma van a helyszíni Active Directoryban. Szinkronizálás manuálisan kell engedélyeznie. Ezt megelőzően jegyezze fel a következő viselkedés kényszeríti ki az Azure AD kell végeznie:

- Az Azure AD csak a UserType attribútuma a két értéket fogad el: **Tag** és **vendég**.
- Az Azure AD Connect-szinkronizálást a UserType attribútuma nincs engedélyezve, ha a címtár-szinkronizálás segítségével létrehozott Azure AD-felhasználók lenne beállítva UserType attribútuma **tag**.
- Az Azure AD nem engedélyezi a UserType attribútuma a meglévő Azure AD-felhasználók Azure AD Connect módosítani. Ez csak akkor állítható az Azure AD-felhasználók létrehozása során.

A UserType attribútuma, a szinkronizálás engedélyezése előtt először határozza meg, hogyan az attribútum a helyszíni Active Directory származik. A leggyakrabban használt módszerek a következők:

- Kijelöl egy nem használt helyszíni AD-attribútum (például extensionAttribute1), az adatforrás-attribútum használható. A kijelölt helyszíni AD-attribútum a típusúnak kell lennie **karakterlánc**, egyetlen értékű lehet, és az értéket tartalmazza **tag** vagy **vendég**. 

    Ha ezt a módszert választja, gondoskodnia kell arról, hogy a kijelölt attribútumot a rendszer kitölti a helyes értéket az összes meglévő felhasználói objektum a helyszíni Active Directoryban a szinkronizált Azure ad-hez a UserType attribútuma szinkronizálásának engedélyezése előtt .

- Azt is megteheti a UserType attribútuma értéke is származtatni más tulajdonságok alapján. Például az összes felhasználót szimuláló szinkronizálni szeretné **vendég** Ha a helyszíni AD userPrincipalName attribútum végződik tartományrészt <em>@partners.fabrikam123.org</em>. 

    Ahogy korábban említettük, az Azure AD Connect nem engedélyezi a UserType attribútuma a meglévő Azure AD-felhasználók Azure AD Connect módosítani. Emiatt biztosítania kell, hogy a logikai döntött, hogy hogyan UserType attribútuma már konfigurálva van az összes meglévő Azure AD-felhasználók a bérlőben a konzisztens.

A UserType attribútuma szinkronizálás engedélyezése a lépések szerint lehet összegezni:

1.  Tiltsa le a szinkronizálásütemezőt, és ellenőrizze, hogy nincs folyamatban szinkronizálás.
2.  Az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma.
3.  Adja hozzá a UserType az Azure AD-összekötő sémát.
4.  Az attribútum értéke a helyszíni Active Directoryból áramlását bejövő szinkronizálási szabály létrehozása.
5.  A flow az attribútum értéke, az Azure AD kimenő szinkronizálási szabály létrehozása.
6.  Futtassa egy teljes szinkronizálási ciklust.
7.  Engedélyezze a sync schedulert.

>[!NOTE]
> Ez a szakasz a többi ezeket a lépéseket ismerteti. Ezek leírását a környezetben az Azure AD központi egyerdős topológiával rendelkező és anélküli egyéni szinkronizálási szabályait. Ha több erdőt topológia, egyéni szinkronizálási szabályok konfigurálva, vagy egy átmeneti kiszolgálón rendelkezik, hogy a lépések megfelelően módosítani kell.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: Tiltsa le a szinkronizálásütemezőt, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
Elkerülheti a nemkívánatos módosítások exportálna az Azure AD, győződjön meg arról, hogy a szinkronizálás nem közepén szinkronizálási szabályok frissítése közben kerül sor. A beépített szinkronizálásütemező letiltása:

 1. Indítsa el egy PowerShell-munkamenetet a az Azure AD Connect-kiszolgáló.
 2. Ütemezett szinkronizálás letiltása a parancsmag futtatásával `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Nyissa meg a Synchronization Service Managert a **Start** > **szinkronizálási szolgáltatás**.
 4. Nyissa meg a **műveletek** fülre és ellenőrizze, nincs művelet állapottal *folyamatban*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>2. lépés: Az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma
Nem minden Azure AD-attribútumok is importálja a helyszíni AD-összekötő-térben. Az adatforrás-attribútum hozzáadása az importált attribútumok listáját:

 1. Nyissa meg a **összekötők** fülre a Synchronization Service Managert.
 2. Kattintson a jobb gombbal a helyszíni AD-összekötő, és válassza ki **tulajdonságok**.
 3. A megjelenő párbeszédpanelen nyissa meg a **attribútumok kiválasztása** fülre.
 4. Győződjön meg arról, hogy az adatforrás-attribútum be van jelölve, a attribútum listában.
 5. Kattintson a **OK** mentéséhez.
![Adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>3. lépés: A UserType hozzáadása az Azure AD-összekötő séma
Alapértelmezés szerint a UserType attribútuma nincs importálva a az Azure AD Connect terén. Importált attribútumok listáját a UserType attribútuma hozzáadása:

 1. Nyissa meg a **összekötők** fülre a Synchronization Service Managert.
 2. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **tulajdonságok**.
 3. A megjelenő párbeszédpanelen nyissa meg a **attribútumok kiválasztása** fülre.
 4. Ellenőrizze, hogy a UserType attribútuma szereplő be van jelölve.
 5. Kattintson a **OK** mentéséhez.

![Adatforrás-attribútum hozzáadása az Azure AD-összekötő séma](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>4. lépés: Az attribútum értéke a helyszíni Active Directoryból áramlását bejövő szinkronizálási szabály létrehozása
A bejövő szinkronizálási szabály lehetővé teszi a áramlanak a forrásattribútum a metaverzumba, a helyszíni Active Directoryból származó attribútum értéke:

1. Nyissa meg a szinkronizálási Szabályszerkesztővel a **Start** > **szinkronizálási Szabályszerkesztővel**.
2. Állítsa be a keresési szűrő **iránya** kell **bejövő**.
3. Kattintson a **új szabály hozzáadása** gombra egy új bejövő szabály létrehozásához.
4. Alatt a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Value | Részletek |
    | --- | --- | --- |
    | Name (Név) | *Adjon meg egy nevet* | Ha például *a az AD-ből – felhasználói UserType* |
    | Leírás | *Adjon meg egy leírást* |  |
    | Csatlakoztatott rendszer | *Válassza ki a helyszíni AD-összekötő* |  |
    | Csatlakoztatott rendszer objektum típusa | **Felhasználó** |  |
    | Metaverzum-objektum típusa | **Személy** |  |
    | Hivatkozás típusa | **Csatlakozás** |  |
    | Sorrend | *1 – 99 közötti számot válasszon* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt értéket. |

5. Nyissa meg a **Scoping szűrő** lapra, és adjon hozzá egy **egyetlen hatókörkezelési Szűrőcsoport** az alábbi záradékkal:

    | Attribútum | Művelet | Value |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Felhasználó\_ |

    A Hatókörszűrő határozza meg, mely a helyszíni AD objektumokat a bejövő szinkronizálási szabály vonatkozik. Ebben a példában használt azonos hatókörszűrőt használjuk a *a az AD-ből – felhasználói közös* out-of-box szinkronizálási szabályt, amely megakadályozza, hogy a szinkronizálási szabály alkalmazásakor az Azure AD-felhasználó segítségével létrehozott felhasználói objektumok a jelszóvisszaíró szolgáltatást. Akkor lehet, hogy módosítania kell a Hatókörszűrő megfelelően az Azure AD Connect üzemelő példány.

6. Nyissa meg a **átalakítási** lapra, és a kívánt átalakítási szabály végrehajtása. Ha például egy nem használt kijelölte a helyszíni AD-attribútum (például extensionAttribute1), a UserType adatforrás-attribútum, közvetlen Attribútumfolyam valósíthat meg:

    | Folyamat típusát | Célattribútum | Forrás | Miután a alkalmazni | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | UserType | extensionAttribute1 | Nincs bejelölve | Frissítés |

    Egy másik példa szeretné a UserType attribútuma értéke célosztályából más tulajdonságok alapján. Például szeretné szinkronizálni a minden felhasználó vendégként, ha a helyszíni AD userPrincipalName attribútum végződik tartományrészt <em>@partners.fabrikam123.org</em>. Egy kifejezés hasonló valósíthatja meg:

    | Folyamat típusát | Célattribútum | Forrás | Miután a alkalmazni | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | UserType | IIf(IsPresent([userPrincipalName]),IIf(CBool(Instr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0), "Tag", "Vendég"), hiba ("UserPrincipalName nem szerepel a UserType meghatározása")) | Nincs bejelölve | Frissítés |

7. Kattintson a **Hozzáadás** bejövő szabály létrehozására.

![Bejövő szinkronizálási szabály létrehozása](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>5. lépés: A flow az attribútum értéke, az Azure AD kimenő szinkronizálási szabály létrehozása
A kimenő szinkronizálási szabály lehetővé teszi, hogy az attribútum értéke az a metaverzumba áramlanak a UserType attribútuma az Azure ad-ben:

1. Nyissa meg a szinkronizálási Szabályszerkesztővel.
2. Állítsa be a keresési szűrő **iránya** kell **kimenő**.
3. Kattintson a **új szabály hozzáadása** gombra.
4. Alatt a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Value | Részletek |
    | ----- | ------ | --- |
    | Name (Név) | *Adjon meg egy nevet* | Ha például *vette az aad-ben – a felhasználó a UserType* |
    | Leírás | *Adjon meg egy leírást* ||
    | Csatlakoztatott rendszer | *Válassza ki az AAD-összekötő* ||
    | Csatlakoztatott rendszer objektum típusa | **Felhasználó** ||
    | Metaverzum-objektum típusa | **Személy** ||
    | Hivatkozás típusa | **Csatlakozás** ||
    | Sorrend | *1 – 99 közötti számot válasszon* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt értéket. |

5. Nyissa meg a **Scoping szűrő** lapra, és adjon hozzá egy **egyetlen hatókörkezelési Szűrőcsoport** két záradékai:

    | Attribútum | Művelet | Value |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Felhasználó |
    | cloudMastered | NOTEQUAL | True (Igaz) |

    A Hatókörszűrő határozza meg, amelyhez az Azure AD-objektumok a kimenő szinkronizálási szabály vonatkozik. Ebben a példában az azonos Hatókörszűrő használjuk a *ki az ad-hez – felhasználói identitás* out-of-box szinkronizálási szabály. Megakadályozza, hogy a szinkronizálási szabály nem a helyszíni Active Directoryból szinkronizált felhasználói objektumok alkalmazták. Akkor lehet, hogy módosítania kell a Hatókörszűrő megfelelően az Azure AD Connect üzemelő példány.

6. Nyissa meg a **átalakítási** lapra, és végrehajtja a következő átalakítási szabályt:

    | Folyamat típusát | Célattribútum | Forrás | Miután a alkalmazni | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | UserType | UserType | Nincs bejelölve | Frissítés |

7. Kattintson a **Hozzáadás** a kimenő szabály létrehozásához.

![Kimenő szinkronizálási szabály létrehozása](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>6. lépés: Egy teljes szinkronizálási ciklust futtatása
Általában egy teljes szinkronizálási ciklust szükség, mert azt hozzá új attribútumok az Active Directory és az Azure AD-összekötő sémák, és vezetett be egyéni szinkronizálási szabályait. A módosítások ellenőrzéséhez őket az Azure AD-exportálás előtt szeretné. 

A következő lépések segítségével ellenőrizheti a módosításokat a lépéseket egy teljes szinkronizálási ciklust alkotó manuális futtatása során.

1. Futtassa a **teljes importálást** a a **a helyszíni AD-összekötő**:

   1. Nyissa meg a **műveletek** fülre a Synchronization Service Managert.
   2. Kattintson a jobb gombbal a **a helyszíni AD-összekötő** válassza **futtatása**.
   3. A megjelenő párbeszédpanelen jelölje ki **teljes importálást** majd **OK**.
   4. Várjon, amíg a művelet befejeződik.

    > [!NOTE]
    > Teljes importálást kihagyhatja a helyszíni AD-összekötő az adatforrás-attribútum már szerepel a listában, ha importált attribútumok. Más szóval, nem rendelkezett során módosításokat [2. lépés: Az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Futtassa a **teljes importálást** a a **Azure AD-összekötő**:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **futtatása**.
   2. A megjelenő párbeszédpanelen jelölje ki **teljes importálást** majd **OK**.
   3. Várjon, amíg a művelet befejeződik.

3. A szinkronizálási szabály módosítások a meglévő felhasználói objektum ellenőrzéséhez:

    Az adatforrás-attribútum a helyszíni Active Directory és az Azure AD-ből a UserType importálta a megfelelő Összekötőterek. A teljes szinkronizálás folytatása előtt hajtsa végre egy **előzetes** egy meglévő felhasználó az objektumot a helyszíni AD-összekötő-térben. A kiválasztott objektum kell rendelkeznie a forrásattribútum feltöltve.
    
    Egy sikeres **előzetes** a UserType töltse be a metaverzumba egy jól jelzi, hogy konfigurálta a szinkronizálási szabályok megfelelően. Ehhez információ egy **előzetes**, című szakaszban [ellenőrizze a módosítást](#verify-the-change).

4. Futtassa a **teljes szinkronizálás** a a **a helyszíni AD-összekötő**:

   1. Kattintson a jobb gombbal a **a helyszíni AD-összekötő** válassza **futtatása**.
   2. A megjelenő párbeszédpanelen jelölje ki **teljes szinkronizálást** majd **OK**.
   3. Várjon, amíg a művelet befejeződik.

5. Győződjön meg arról **függőben lévő Exportálásokról** az Azure ad-hez:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **keresési Összekötőterét**.
   2. Az a **keresési Összekötőterét** a megjelenő párbeszédpanelen:

      - Állítsa be **hatókör** való **függő exportálás**.
      - Válassza ki a három jelölőnégyzetet: **Adjon hozzá**, **módosítása**, és **törlése**.
      - Kattintson a **keresési** gombra a módosítások exportálható objektumok listája. A módosítások egy adott objektum vizsgálatához kattintson duplán az objektumot.
      - Győződjön meg arról, hogy a változások várhatók.

6. Futtatás **exportálása** a a **Azure AD-összekötő**:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **futtatása**.
   2. A a **összekötő futtatásához** előugró párbeszédpanelen jelölje ki **exportálása** majd **OK**.
   3. Várjon, amíg az Exportálás az Azure AD a Befejezés gombra.

> [!NOTE]
> Ezek a lépések nem tartalmazza a teljes szinkronizálás és lépéseket az Azure AD Connectoron exportálása. Ezeket a lépéseket nem szükséges, mert az attribútum értékei csak az Azure ad a helyszíni Active Directoryból átvitele.

### <a name="step-7-re-enable-the-sync-scheduler"></a>7. lépés: Engedélyezze újra a szinkronizálásütemező
A beépített szinkronizálásütemező újbóli engedélyezése:

1. Indítsa el egy PowerShell-munkamenetet.
2. Ütemezett szinkronizálás engedélyezze újra a parancsmagot `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>További lépések
* További információ a konfigurációs modell [ismertetése deklaratív kiépítés](concept-azure-ad-connect-sync-declarative-provisioning.md).
* További információ az a kifejezés nyelv [deklaratív kiépítés kifejezéseinek ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
