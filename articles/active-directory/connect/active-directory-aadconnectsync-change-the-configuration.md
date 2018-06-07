---
title: 'Azure AD Connect szinkronizálása: olyan konfigurációs módosítást az Azure AD Connect szinkronizálási szolgáltatás |} Microsoft Docs'
description: Bemutatja, hogyan lehet módosítani az Azure AD Connect-szinkronizálás konfigurációs módjáról.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bad1cbe0b142e146ada28f2af5d152973100e919
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595104"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect szinkronizálása: megváltoztatja az alapértelmezett konfiguráció
Ez a cikk célja végigvezetik Önt az Azure Active Directory (Azure AD) Connect szinkronizálási szolgáltatás az alapértelmezett konfigurációs módosításokat. Lépéseket biztosít olyan gyakori forgatókönyveket tartalmaz. Ennek az információnak a egyszerű módosíthatják a saját üzleti szabályok alapján saját konfigurációs kell lennie.

## <a name="synchronization-rules-editor"></a>Szinkronizálási szabályok szerkesztő
A szinkronizálási szabályok szerkesztő segítségével az alapértelmezett konfiguráció módosítása. A Keresés a **Start** menüt a **az Azure AD Connect** csoport.  
![Start menü szinkronizálási szabály szerkesztő](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

A szerkesztő megnyitásakor megjelenik az alapértelmezett out-of-box szabályokat.

![Szinkronizálási szabály szerkesztő](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigálás a szerkesztőben
A szerkesztő felső az vetett oszlopánál használva gyorsan megtalálhatja egy adott szabály. Például ha szeretné látni a szabályokat, ha az attribútum proxyAddresses megtalálható, módosíthatja az vetett oszlopánál a következőhöz:  
![SRE szűrése](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Alaphelyzetbe állítása, szűrési és az új konfiguráció betöltése, nyomja le az F5 billentyűt.

Jobb felső részén található a **új szabály hozzáadása** gombra. Erre a gombra kattintva a saját egyéni szabály létrehozására használhatja.

A lap alján vannak gombokat a kijelölt szinkronizálási szabály alapján. **Szerkesztés** és **törlése** várt módon működnek őket. **Exportálás** hozza létre újra a szinkronizálási szabály szükséges PowerShell-parancsfájlt hoz létre. Ezzel az eljárással szinkronizálási szabály áthelyezheti az egyik kiszolgálóról a másikra.

## <a name="create-your-first-custom-rule"></a>Az első egyéni szabály létrehozása
A leggyakoribb változások a attribútumfolyamok fel. Az adatok a forráskönyvtárban nem lehet ugyanaz, mint az Azure AD. A jelen szakaszban ismertetett példa, győződjön meg arról, hogy a felhasználó utóneve mindig a *megfelelő eset*.

### <a name="disable-the-scheduler"></a>Az ütemező letiltása
A [Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md) alapértelmezés szerint 30 percenként fut. Ellenőrizze, hogy nem indul, módosítása, és az új szabályok hibaelhárítás. Az ütemező ideiglenes letiltásához indítsa el a Powershellt, és futtassa `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Az ütemező letiltása](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>A szabály létrehozása
1. Kattintson a **új szabály hozzáadása**.
2. Az a **leírás** lapján írja be a következőket:  
   ![Bejövő szabály szűrése](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * **Név**: Adjon a szabálynak egy leíró nevet.
   * **Leírás**: adjon bizonyos tisztázására, valaki más tudja értelmezni a szabály Újdonságok a.
   * **Csatlakoztatott rendszer**: a rendszer, amelyben az objektum található. Ebben az esetben válassza **Active Directory-összekötő**.
   * **Csatlakoztatott rendszer/Metaverse Object Type**: válasszon **felhasználói** és **személy**, illetve.
   * **Kapcsolattípus**: módosítsa az értéket **csatlakozás**.
   * **Sorrend**: Adjon meg egy értéket, amely egyedi a rendszerben. Alacsonyabb numerikus érték magasabb prioritással.
   * **Címke**: hagyja üresen ezt. A Microsoft csak out-of-box szabályok ebben a mezőben számnak töltődik kell rendelkeznie.
3. Az a **Scoping szűrő** lapján adja meg **givenName ISNOTNULL**.  
   ![Bejövő szabály hatókör-beállítási szűrője](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   Ez a szakasz segítségével meghatározható, hogy mely objektumok, a szabály vonatkozik. Az üres, ha a szabályt alkalmazni szeretné az összes felhasználói objektum. Azonban, amely magában foglalja a konferenciaterem szolgáltatásfiókok és más nem személyek felhasználói objektumok.
4. Az a **szabályok csatlakozás** lapon, a mezőt hagyja üresen.
5. Az a **átalakítások** lapján módosítsa **FlowType** való **kifejezés**. A **Target attribútummal**, jelölje be **givenName**. És a **forrás**, adja meg **PCase([givenName])**.
   ![Bejövő szabály átalakítások](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   A szinkronizálási motor a kis-és nagybetűket a függvény nevét és az attribútum neve. Ha valami nem megfelelő, egy figyelmeztetés megjelenítése a szabály hozzáadásakor. Mentheti, és továbbra is, de meg kell nyissa meg újra, és javítsa ki a szabályt.
6. Kattintson a **Hozzáadás** a szabály mentéséhez.

Az új egyéni szabályt a rendszer a szinkronizálási szabályoknak látható kell lennie.

### <a name="verify-the-change"></a>A módosítás ellenőrzése
Az új módosítását szeretné ellenőrizze, hogy a várt módon működik, és nem szűrész hibákról. Objektumok rendelkezik számától függően kétféleképpen ezt a lépést:

- Futtassa a teljes szinkronizálás összes objektumot.
- A kép és a teljes szinkronizálás futtatnak egy adott objektum.

Nyissa meg a **szinkronizálási szolgáltatás** a a **Start** menü. Ebben a szakaszban ismertetett összes szerepelnek, az eszköz.

**Teljes szinkronizálás az összes objektum**  

   1. Válassza ki **összekötők** tetején. Az összekötő, amely módosította az előző szakaszban (ebben az esetben az Active Directory tartományi szolgáltatások) azonosítása, és válassza ki azt. 
   2. A **műveletek**, jelölje be **futtatása**.
   3. Válassza ki **teljes szinkronizálás**, majd válassza ki **OK**.
   ![Teljes szinkronizálás](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Az objektumok most frissíti benne a metaverzumba. A módosítások ellenőrzéséhez a metaverzumban objektum megtekintésével.

**Kép és a teljes szinkronizálás az egy adott objektum**  

   1. Válassza ki **összekötők** tetején. Az összekötő, amely módosította az előző szakaszban (ebben az esetben az Active Directory tartományi szolgáltatások) azonosítása, és válassza ki azt.
   2. Válassza ki **Összekötőtér keresési**. 
   3. Használjon **hatókör** található olyan objektum, amely a módosítás teszteléséhez használni kívánt. Jelölje ki az objektumot, és kattintson a **előzetes**. 
   4. Az új képernyőn válassza ki a **véglegesítése előzetes**.  
   ![Előzetes verzió véglegesítése](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   A módosítás most elkötelezte magát a metaverzumba.

**Az objektum megtekintése a metaverzumban**  

1. Győződjön meg arról, hogy az érték várt és, hogy a szabály alkalmazása néhány minta objektumokat válasszon. 
2. Válassza ki **Metaverzum-keresés** a lista elejéről. Adjon hozzá semmilyen szűrőt, amelyekre szüksége van a megfelelő objektumok kereséséhez. 
3. Nyissa meg a keresési eredmény egy objektumot. Nézze meg az attribútum értékei, és a is ellenőrizheti a **szinkronizálási szabályok** oszlopot, amely a szabály alkalmazása a várt módon.  
![Keresés a metaverzumban](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Az ütemező engedélyezése
Ha a várt módon van, engedélyezheti az ütemező újra. A PowerShell, futtassa a `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Más közös Attribútummódosítások folyamata
Az előző szakaszban leírt módosításokat szeretne egy Attribútumfolyam hogyan. Ez a szakasz néhány további példákat rendelkezésre állnak. A szinkronizálási szabály létrehozása lépéseinek rövidítése, de a teljes lépéseket az előző szakaszban található.

### <a name="use-an-attribute-other-than-the-default"></a>Az attribútum az alapértelmezettől eltérő használata
Ilyenkor a Fabrikam nincs egy erdő, a helyi ábécé hol használják a megadott névvel, a Vezetéknév és a megjelenített név. Ezek az attribútumok latin betűkből álló ábrázolását kiterjesztési attribútumot is található. Az Azure AD globális cím készítéséhez listájában, és Office 365, a szervezet szeretne ezek az attribútumok használata.

Az alapértelmezett beállításokkal a helyi erdő egy objektum néz ki:  
![Attribútumfolyam 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Olyan szabály létrehozására a más attribútumfolyamok, tegye a következőket:

1. Nyissa meg a **szinkronizálási szabályok szerkesztő** a a **Start** menü.
2. A **bejövő** balra van jelölve, kattintson a **új szabály hozzáadása** gombra.
3. Adjon a szabálynak egy nevet és leírást. Válassza ki a helyszíni Active Directory példány és a kapcsolódó objektumtípusokat. A **kapcsolattípus**, jelölje be **csatlakozás**. A **sorrend**, válasszon egy szám, amely egy másik szabály nem használja. Out-of-box szabályok indítsa el a 100, így 50 értékkel használható ebben a példában.
  ![Attribútumfolyam 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
4. Hagyja **Scoping szűrő** üres. (Ez azt jelenti, hogy azt kell alkalmazni az erdőben lévő összes felhasználói objektum.)
5. Hagyja **szabályok csatlakozás** üres. (Ez azt jelenti, hogy az illesztések kezelni out-of-box szabály segítségével.)
6. A **átalakítások**, a következő adatfolyamok létrehozása:  
  ![Attribútumfolyam 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
7. Kattintson a **Hozzáadás** a szabály mentéséhez.
8. Ugrás a **Synchronization Service Managert**. A **összekötők**, válassza ki az összekötőt, amelyikhez hozzáadta a szabályt. Válassza ki **futtatása**, majd válassza ki **teljes szinkronizálás**. A teljes szinkronizálás összes objektum újraszámítja az aktuális szabályokkal.

Ez az az eredmény a következő egyéni szabálynak ugyanahhoz az objektumhoz:  
![Attribútumfolyam 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Attribútumok hossza
Karakterlánc attribútumok példánycímkének alapértelmezés szerint, és a hossza legfeljebb 448 karakter lehet. Ha több előfordulhat, hogy tartalmazó karakterlánc-attribútumok dolgozik, győződjön meg arról, a Attribútumfolyam felvenni a következő:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>A userPrincipalSuffix módosítása
A userPrincipalName attribútum az Active Directory a felhasználók mindig nem ismert, és nem feltétlenül felel meg a bejelentkezési azonosítót. Az Azure AD Connect sync telepítési varázslóval választhat egy másik attribútum – például *mail*. Azonban bizonyos esetekben az attribútum ki kell számítani.

Például a Contoso vállalat két Azure AD-címtártól, az egyik az éles és egy tesztelési rendelkezik. Szeretnék a saját tesztelési bérlőn egy másik utótag használata a bejelentkezési azonosító:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Ebben a kifejezésben érvénybe mindent az első bal @-sign (Word) és ÖSSZEFŰZ rögzített karakterláncot.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Egy többértékű attribútum átalakítása egyetlen értéket
Egyes attribútumok az Active Directory a sémában többértékű, annak ellenére, hogy az Active Directory – felhasználók és számítógépek egyértékű megtekintése. Példa: a leírás attribútum:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Ebben a kifejezésben, ha az attribútum értéke, végezze el az első elem (*elem*) attribútum, távolítsa el a kezdő és záró szóközök (*Trim*), és az első 448 karakterek (*balra* ) a karakterláncban.

### <a name="do-not-flow-an-attribute"></a>Nem egymást követő attribútum
A forgatókönyvhöz, amelyben ez a szakasz a háttérben, lásd: [szabályozhatja a attribútum áramlási folyamat](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Két módon nem flow attribútum. Az egyik a telepítési varázsló használatával [távolítsa el a kiválasztott attribútumok](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Ez a beállítás akkor működik, ha még nem szinkronizálta a attribútum előtt. Azonban elindítását követően szinkronizálja ezt az attribútumot, és később eltávolítja az ezzel a szolgáltatással, a szinkronizálási motor leállása kezelése a attribútum és a meglévő értékek megmaradnak az Azure ad-ben.

Ha szeretné eltávolítani egy attribútum értékét, és győződjön meg arról, hogy a jövőben nem terjeszthetők, kell létrehoz egy egyéni szabályt.

Ilyenkor a Fabrikam rájöttünk kell arról, hogy néhány olyan azt szinkronizálása a felhőbe attribútum nem lehet van. Győződjön meg arról, hogy ezek az attribútumok el lesznek távolítva az Azure AD szeretnénk.  
![Hibás a bővítményattribútumokat](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

1. Egy új bejövő szinkronizálási szabályának létrehozása és feltöltése, a leírás.
  ![Leírása](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
2. Hozzon létre az attribútumfolyamok **kifejezés** a **FlowType** és **AuthoritativeNull** a **forrás**. A szövegkonstans **AuthoritativeNull** azt jelzi, hogy az érték lehet üres a metaverzumban, még akkor is, ha egy alacsonyabb-sorrend szinkronizálási szabály megpróbálja feltölteni az érték.
  ![A kiterjesztési attribútumot átalakítása](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
3. A szinkronizálási szabály mentéséhez. Indítsa el a **szinkronizálási szolgáltatás**, az összekötő található, válassza ki **futtatása**, majd válassza ki **teljes szinkronizálás**. Ezt a lépést minden attribútumfolyamok újraszámítja.
4. Ellenőrizze, hogy a kívánt módosításokat a kapcsolódási térbe keresve exportálásra váró adatokat.
  ![Előkészített törlése](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Szabályok létrehozása a PowerShell használatával
Jól a szinkronizálási szabály szerkesztővel működik, ha csak néhány módosítások végrehajtásához. Ha sok módosítani kell, a PowerShell valószínűleg jobb megoldás. Néhány speciális funkció csak érhetők el a PowerShell használatával.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>A PowerShell parancsfájl lekérése az out-of-box szabály
Tekintse meg a PowerShell parancsfájl, amely egy out-of-box szabályt létrehozni, jelölje ki a szabályt a szinkronizálási szabályok szerkesztőben, és kattintson a **exportálása**. Ez a művelet lehetővé teszi, hogy a szabály létrehozása a PowerShell-parancsfájlt.

### <a name="advanced-precedence"></a>Speciális sorrendje
A out-of-box szinkronizálási szabályok a sorrend értéke 100 kezdődik. Ha több erdővel rendelkezik, és sok egyéni módosításokat kell, majd 99 szinkronizálási szabályok nem elég lehet.

A szinkronizálási motor, amely azt szeretné, hogy további szabályok out-of-box szabályok előtt találhatók. Ahhoz, hogy ez a viselkedés, kövesse az alábbi lépéseket:

1. Jelölje meg az első out-of-box szinkronizálási szabály (**a az AD-felhasználó csatlakozás**) a szinkronizálási szabályok szerkesztőt, és válasszon **exportálása**. Másolja a SR azonosító értékét.  
![PowerShell módosítása előtt](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Az új szinkronizálási szabály létrehozása. A szinkronizálási szabályok szerkesztő segítségével hozza létre. A szabály PowerShell-parancsfájlba exportálni.
3. A tulajdonság **PrecedenceBefore**, szúrja be az azonosító értéket a out-of-box szabályból. Állítsa be a **sorrend** való **0**. Ellenőrizze, hogy az egyedi azonosító attribútum, és, hogy nem újrahasznosítja a GUID Azonosítót a egy másik szabálynak. Győződjön meg arról is, amely a **ImmutableTag** tulajdonsága nincs beállítva. Ez a tulajdonság csak egy out-of-box szabályhoz kell állítani.
4. Mentse a PowerShell-parancsfájlt, és futtassa. Az eredménye, hogy az egyéni szabály hozzá van rendelve a sorrend értéke 100, és minden más szabállyal out-of-box növeli.  
![PowerShell módosítása után](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Sok egyéni szinkronizálási szabályok van azonos **PrecedenceBefore** értéke, ha szükséges.

## <a name="enable-synchronization-of-usertype"></a>UserType szinkronizálásának engedélyezése
Az Azure AD Connect szinkronizálásának támogatja a **UserType** az attribútum **felhasználói** verziójában 1.1.524.0 objektumok és újabb verziók. Pontosabban a következő módosításokat vezettek be:

- A séma az objektumtípus **felhasználói** a az Azure AD-összekötő az időtartam UserType attribútuma, amely a karakterlánc típusú, és egyértékű tartalmazza.
- A séma az objektumtípus **személy** a metaverzumban ki van bővítve a UserType attribútuma, amely a karakterlánc típusú, és egyértékű tartalmazza.

Alapértelmezés szerint a UserType attribútuma nincs engedélyezve a szinkronizáláshoz, mert nincs megfelelő UserType attribútum a helyszíni Active Directoryban. Manuálisan kell engedélyezné a szinkronizálást. Jegyezze fel a következő viselkedés kényszeríti ki az Azure AD, érdemes ezt megelőzően:

- Az Azure AD csak két érték UserType attribútuma fogad: **tag** és **vendég**.
- Ha UserType attribútuma nem engedélyezett az Azure AD Connect szinkronizáláshoz, a címtár-szinkronizálás segítségével létrehozott Azure Active Directory-felhasználók lenne a UserType attribútuma **tag**.
- Az Azure AD UserType attribútuma nem engedélyezi a meglévő Azure AD-felhasználók az Azure AD Connect módosítható. Csak beállítható az Azure AD-felhasználók létrehozása közben.

Ahhoz, hogy a UserType attribútuma szinkronizálását, először határozza meg, hogyan az attribútum a helyszíni Active Directory származik. A leggyakrabban használt módszerek a következők:

- Egy nem használt kijelölni a helyszíni AD-attribútum (például extensionAttribute1), az adatforrás-attribútum használható. A kijelölt helyszíni AD attribútum a típusúnak kell lennie **karakterlánc**, single értékű kell, és az érték tartalmaz **tag** vagy **vendég**. 

    Ha ezt a módszert választja, gondoskodnia kell arról, hogy a kijelölt attribútum a helyes értéket minden meglévő felhasználói objektumhoz a helyszíni Active Directoryban, amely szinkronizálva az Azure AD-UserType attribútuma szinkronizálásának engedélyezése előtt a telepítéskor .

- Másik lehetőségként UserType attribútuma értéke is származik egyéb tulajdonságait. Például minden felhasználó, mint a szinkronizálni kívánt **vendég** Ha a helyszíni AD userPrincipalName attribútum a tartományrészt végződik *@partners.fabrikam123.org*. 

    Ahogy korábban említettük, az Azure AD Connect nem teszi lehetővé a UserType attribútuma a meglévő Azure AD-felhasználók az Azure AD Connect módosítható. Ezért akkor biztosítania kell, hogy kiválasztotta konzisztensek legyenek hogyan UserType attribútuma már be van állítva az összes meglévő Azure Active Directory-felhasználók az Ön bérelt szolgáltatásának logikát.

Történő engedélyezéséről a UserType attribútuma szinkronizálását összegezhető:

1.  Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban szinkronizálás.
2.  Az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma.
3.  Adja hozzá a UserType az Azure AD-összekötő séma.
4.  Az attribútum értékét a helyszíni Active Directoryból rendszer vonatkozó bejövő szinkronizálási szabály létrehozása.
5.  Az attribútumérték az Azure AD-rendszer kimenő szinkronizálási szabályt létrehozni.
6.  Futtasson egy teljes szinkronizálási ciklust.
7.  A szinkronizálásütemező engedélyezése.

>[!NOTE]
> Ez a szakasz a többi ezeket a lépéseket ismerteti. Azok a környezet az Azure AD központi telepítés egyerdős topológiával rendelkező és anélküli egyéni szinkronizálási szabályait ismerteti. Ha több erdőből topológia, egyéni szinkronizálási szabályok konfigurálva, vagy egy átmeneti kiszolgálón rendelkezik, ennek megfelelően módosítsa a lépéseket kell.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: A szinkronizálásütemező letiltása, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
Elkerülése, exportálása nem szándékolt módosításokat az Azure AD, győződjön meg arról, hogy szinkronizálás nem történik meg, amíg közepén frissítése szinkronizálási szabályait. A beépített szinkronizálásütemezési letiltása:

 1. Indítsa el a PowerShell-munkamenetben az Azure AD Connect kiszolgálón.
 2. A parancsmag futtatásával tiltsa le a ütemezett szinkronizálás `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Nyissa meg a Synchronization Service Managert a **Start** > **szinkronizálási szolgáltatás**.
 4. Lépjen a **műveletek** lapra, és ellenőrizze, nincs olyan állapota művelet *folyamatban*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>2. lépés: Az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma
Nem minden Azure AD-attribútumot a rendszer importálta a helyszíni AD kapcsolódási térbe. Az adatforrás-attribútum hozzáadása az importált attribútumok listáját:

 1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
 2. Kattintson a jobb gombbal a helyszíni AD-összekötő, és válassza **tulajdonságok**.
 3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
 4. Győződjön meg arról, hogy az adatforrás-attribútum be van jelölve, a attribútum listában.
 5. Kattintson a **OK** mentéséhez.
![Adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>3. lépés: A UserType hozzáadása az Azure AD-összekötő séma
Alapértelmezés szerint az Azure AD Connect terület UserType attribútuma nem importálta. Az importált attribútumlistát UserType attribútuma hozzáadása:

 1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
 2. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **tulajdonságok**.
 3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
 4. Ellenőrizze, hogy a UserType attribútuma be van jelölve, a attribútum listában.
 5. Kattintson a **OK** mentéséhez.

![Adatforrás-attribútum hozzáadása az Azure AD-összekötő séma](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>4. lépés: Az attribútum értékét a helyszíni Active Directoryból rendszer vonatkozó bejövő szinkronizálási szabály létrehozása
A bejövő szinkronizálási szabály lehetővé teszi a forrásattribútum a helyszíni Active Directoryból a metaverzumba haladjanak attribútumérték:

1. Nyissa meg a szinkronizálási szabályok szerkesztőt a **Start** > **szinkronizálási szabályok szerkesztő**.
2. Állítsa be a keresési szűrő **irány** kell **bejövő**.
3. Kattintson a **új szabály hozzáadása** gombra kattintva hozzon létre egy új bejövő szabályt.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például *a az AD-felhasználó UserType* |
    | Leírás | *Adjon meg egy leírást* |  |
    | Csatlakoztatott rendszer | *Válassza ki a helyszíni AD-összekötő* |  |
    | Objektumtípus csatlakoztatva | **Felhasználó** |  |
    | Metaverzum-objektum típusa | **Személy** |  |
    | Kapcsolat típusa | **Csatlakozás** |  |
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Lépjen a **Scoping szűrő** lapra, és adja hozzá a **egyetlen tartalmazó szűrő csoport** következő utasítással:

    | Attribútum | Művelet | Érték |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Felhasználó\_ |

    A hatókört szűrő határozza meg, mely a helyszíni AD-objektumokat a vonatkozó bejövő szinkronizálási szabály vonatkozik. Ebben a példában használjuk a azonos tartalmazó szűrő szerepel a *a az AD-felhasználó közös* out-of-box szinkronizálási szabály, amely megakadályozza, hogy a szinkronizálási szabály létrehozása az Azure AD-felhasználó a felhasználói objektumok vonatkozik visszaírási szolgáltatás. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.

6. Lépjen a **átalakítása** lapra, és valósítja meg a kívánt átalakítási szabállyal. Például, ha egy nem használt kijelölt helyszíni AD-attribútum (például extensionAttribute1) a UserType adatforrás-attribútum, mint egy közvetlen Attribútumfolyam valósíthat meg:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | UserType | extensionAttribute1 | Nincs bejelölve | Frissítés |

    Egy másik példa kívánt UserType attribútuma értéke származik egyéb tulajdonságait. Például minden felhasználó szinkronizálni vendégként, ha szeretné a helyszíni AD userPrincipalName attribútum a tartományrészt végződik *@partners.fabrikam123.org*. A kifejezés valósíthatja meg:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | UserType | IIf(IsPresent([userPrincipalName]),IIf(CBool(Instr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0), "Tag", "Vendég"), hiba ("UserPrincipalName nincs jelen UserType meghatározásához")) | Nincs bejelölve | Frissítés |

7. Kattintson a **Hozzáadás** a bejövő szabály létrehozásához.

![Bejövő szinkronizálási szabályának létrehozása](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>5. lépés: Az attribútum értéke az Azure AD-rendszer kimenő szinkronizálási szabály létrehozása
A kimenő szinkronizálási szabály lehetővé teszi az attribútumérték felé haladjanak a metaverzumba UserType attribútuma az Azure AD-ben:

1. Nyissa meg a szinkronizálási szabályok szerkesztőbe.
2. Állítsa be a keresési szűrő **irány** kell **kimenő**.
3. Kattintson a **új szabály hozzáadása** gombra.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | ----- | ------ | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például *az aad-be – felhasználói UserType ki* |
    | Leírás | *Adjon meg egy leírást* ||
    | Csatlakoztatott rendszer | *Válassza ki az AAD-összekötő* ||
    | Objektumtípus csatlakoztatva | **Felhasználó** ||
    | Metaverzum-objektum típusa | **Személy** ||
    | Kapcsolat típusa | **Csatlakozás** ||
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Lépjen a **Scoping szűrő** lapra, és adja hozzá a **egyetlen tartalmazó szűrő csoport** a két záradékokkal:

    | Attribútum | Művelet | Érték |
    | --- | --- | --- |
    | sourceObjectType | EGYENLŐ | Felhasználó |
    | cloudMastered | NOTEQUAL | True (Igaz) |

    A hatókört szűrő határozza meg, amelyhez az Azure AD-objektumokat a kimenő szinkronizálási szabály vonatkozik. Ebben a példában ugyanazt a hatókört szűrőt a használjuk a *ad – felhasználói identitás Out* out-of-box szinkronizálási szabály. Megakadályozza, hogy a szinkronizálási szabály nem a helyszíni Active Directoryból szinkronizált felhasználói objektumok vonatkozik. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.

6. Lépjen a **átalakítása** lapra, és valósítja meg a következő átalakítási szabályt:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | UserType | UserType | Nincs bejelölve | Frissítés |

7. Kattintson a **Hozzáadás** a kimenő forgalomra vonatkozó szabály létrehozásához.

![Kimenő szinkronizálási szabály létrehozása](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>6. lépés: Futtasson egy teljes szinkronizálási ciklust
Általában egy teljes szinkronizálási ciklust kell adni, mert azt hozzá új attribútumok az Active Directory és az Azure AD-összekötő sémák, és egyéni szinkronizálási szabályokat vezetett be. A módosítások őket az Azure AD-exportálás előtt ellenőrizni szeretné. 

Az alábbi lépések segítségével a módosítások ellenőrzéséhez a lépéseket egy teljes szinkronizálási ciklust alkotó manuális futtatása során.

1. Futtassa a **teljes importálás** a a **helyszíni AD-összekötő**:

   1. Lépjen a **műveletek** lapon a Synchronization Service Managert.
   2. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza **futtatása**.
   3. Az előugró párbeszédpanelen válassza ki a **teljes importálás** majd **OK**.
   4. Várjon, amíg a művelet befejezéséhez.

    > [!NOTE]
    > Teljes importálás kihagyhatja a helyszíni AD-összekötő, ha az adatforrás-attribútum már szerepel a listájában importált attribútumok. Ez azt jelenti, hogy nincs semmilyen módosítás alatt [2. lépés: az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Futtassa a **teljes importálás** a a **Azure AD-összekötő**:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **futtatása**.
   2. Az előugró párbeszédpanelen válassza ki a **teljes importálás** majd **OK**.
   3. Várjon, amíg a művelet befejezéséhez.

3. A felhasználói objektum, a szinkronizálási szabály módosítások ellenőrzéséhez:

    A forrásattribútumot, a helyszíni Active Directory és az Azure AD UserType importálva lettek a megfelelő Összekötőterek. A teljes szinkronizálás folytatása előtt hajtsa végre a **előzetes** egy meglévő felhasználó az objektum a helyszíni AD kapcsolódási térbe. Az objektum úgy döntött, hogy meg kell adni a forrásattribútum feltöltve.
    
    A sikeres **előzetes** a feltöltve a metaverzumban UserType egy jól jelzi, hogy konfigurálta a szinkronizálás szabályainak megfelelően. Ehhez információ egy **előzetes**, tekintse át a részt [ellenőrzéséhez](#verify-the-change).

4. Futtassa a **teljes szinkronizálás** a a **helyszíni AD-összekötő**:

   1. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza **futtatása**.
   2. Az előugró párbeszédpanelen válassza ki a **teljes szinkronizálás** majd **OK**.
   3. Várjon, amíg a művelet befejezéséhez.

5. Győződjön meg arról **függőben lévő Exportálásokról** az Azure AD:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **Összekötőtér keresési**.
   2. Az a **Összekötőtér keresési** előugró párbeszédpanelen:

      - Állítsa be **hatókör** való **exportálási függőben lévő**.
      - Jelölje be mindhárom jelölőnégyzetet: **Hozzáadás**, **módosítás**, és **törlése**.
      - Kattintson a **keresési** gombra kell kattintania a módosítások exportálható objektumok listáját. Vizsgálja meg az adott objektum a módosításokat, kattintson duplán az objektum.
      - Győződjön meg arról, hogy a változások várhatók.

6. Futtatás **exportálása** a a **Azure AD Connectoron**:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **futtatása**.
   2. A a **összekötő futtatásához** előugró párbeszédpanelen jelölje ki **exportálása** majd **OK**.
   3. Várjon, amíg az Active Directoryba való exportálás Azure befejezéséhez.

> [!NOTE]
> Ezeket a lépéseket nem tartalmazza a teljes szinkronizálás és a lépések az Azure AD Connectoron. Ezeket a lépéseket esetén nincs szükség, mert az attribútum értékei vannak áramlanak a helyszíni Active Directory csak az Azure ad.

### <a name="step-7-re-enable-the-sync-scheduler"></a>7. lépés: Engedélyezze újra a szinkronizálásütemező
Engedélyezze újra a beépített szinkronizálásütemezési:

1. Indítson el egy PowerShell-munkamenetet.
2. Ütemezett szinkronizálás engedélyezze újra a parancsmagot `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>További lépések
* További információk a konfigurációs modell [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* További információk az a kifejezés nyelv [ismertetése deklaratív kiépítés kifejezések](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
