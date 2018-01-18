---
title: "Azure AD Connect szinkronizálása: olyan konfigurációs módosítást az Azure AD Connect szinkronizálási szolgáltatás |} Microsoft Docs"
description: "Bemutatja, hogyan lehet módosítani az Azure AD Connect-szinkronizálás konfigurációs módjáról."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: billmath
ms.openlocfilehash: ed71272d2d10cd8b71fd3b2722d3ba033f1b51f9
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure AD Connect szinkronizálása: hogyan lehet módosítani az alapértelmezett konfiguráció
Ez a témakör célja végigvezetik Önt az Azure AD Connect szinkronizálási szolgáltatás az alapértelmezett konfiguráció módosításához. Lépéseket biztosít olyan gyakori forgatókönyveket tartalmaz. Ennek az információnak a tudni a saját üzleti szabályok alapján saját konfigurációs néhány egyszerű módosítást kell lennie.

## <a name="synchronization-rules-editor"></a>Szinkronizálási szabályok szerkesztő
A szinkronizálási szabályok szerkesztő segítségével az alapértelmezett konfiguráció módosítása. A Start menü megtalálhassa a **az Azure AD Connect** csoport.  
![Start menü szinkronizálási szabály szerkesztő](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Megnyitásakor, tekintse meg az alapértelmezett out-of-box szabályokat.

![Szinkronizálási szabály szerkesztő](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigálás a szerkesztőben
Legördülő listákat a szerkesztő felső engedélyezi, hogy gyorsan megtalálhatja az egy adott szabályt. Szeretné látni a szabályokat, ha az attribútum proxyAddresses megtalálható, akkor a következő például módosítása az vetett oszlopánál:  
![SRE szűrése](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Alaphelyzetbe állítja a szűrés és friss konfiguráció betöltése, nyomja meg a **F5** a billentyűzet.

A jobb felső sarokban gomb van **új szabály hozzáadása**. Erre a gombra kattintva a saját egyéni szabály létrehozására szolgál.

A lap alján működő gombjai kijelölt szinkronizálási szabály van. **Szerkesztés** és **törlése** várt módon működnek őket. **Exportálás** egy PowerShell-parancsfájl az újból létrehozni a szinkronizálási szabályt hoz létre. Ez az eljárás lehetővé teszi egy szinkronizálási szabály áthelyezése egyik kiszolgálóról a másikra.

## <a name="create-your-first-custom-rule"></a>Az első egyéni szabály létrehozása
A leggyakoribb módosítása a attribútumfolyamok módosításai. Az adatok a forráskönyvtárban nem lehet beállítani, mint az Azure AD. A jelen szakaszban ismertetett példa, győződjön meg arról, hogy a felhasználó utóneve mindig a kívánja **megfelelő eset**.

### <a name="disable-the-scheduler"></a>Az ütemező letiltása
A [Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md) alapértelmezés szerint 30 percenként fut. Ellenőrizze, hogy nem indul, amíg változtatásokat végez, és az új szabályok hibaelhárítása szeretné. Ideiglenesen letilthatja az ütemező, indítsa el a Powershellt, és futtassa`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Az ütemező letiltása](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>A szabály létrehozása
1. Kattintson a **új szabály hozzáadása**.
2. Az a **leírás** lapon írja be a következőt:  
   ![Bejövő szabály szűrése](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * Name: Adjon a szabálynak egy leíró nevet.
   * Leírás: Bizonyos tisztázására, valaki más tudja értelmezni a szabály Újdonságok a.
   * Csatlakoztatott rendszer: A rendszer a objektum is található. Ebben az esetben válassza ki az Active Directory-összekötőt.
   * Csatlakoztatott rendszer/Metaverzum-objektum típusa: Válassza ki **felhasználói** és **személy** kulcsattribútumokkal.
   * Kapcsolat típusa: Módosítsa az értéket **csatlakozás**.
   * Sorrend: Adjon meg egy értéket, amely egyedi a rendszerben. Alacsonyabb numerikus érték magasabb prioritással.
   * Címke: Hagyja üresen. A Microsoft csak out-of-box szabályok ebben a mezőben számnak töltődik kell rendelkeznie.
3. Az a **Scoping szűrő** lapján adja meg **givenName ISNOTNULL**.  
   ![Bejövő szabály hatókör-beállítási szűrője](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   Ez a szakasz segítségével meghatározható, milyen objektumokat, a szabály vonatkozik. Ha üres, a szabályt alkalmazni szeretné az összes felhasználói objektum. Azonban, amely konferenciaterem szolgáltatásfiókok és más nem személyek felhasználói objektumokat tartalmazhat.
4. Az a **szabályok csatlakozás**, hagyja üresen.
5. Az a **átalakítások** lapon, majd a FlowType **kifejezés**. Válassza ki a Target attribútummal **givenName**, és írja be a forrás `PCase([givenName])`.
   ![Bejövő szabály átalakítások](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   A szinkronizálási motor a kis-és nagybetűket, mind a függvény nevét és az attribútum neve. Ha valami nem megfelelő, egy figyelmeztetés megjelenítése a szabály hozzáadásakor. A szerkesztő segítségével mentéséhez és a folytatáshoz, nyissa meg újra a szabályt, és javítsa ki a szabály kellene lennie.
6. Kattintson a **Hozzáadás** a szabály mentéséhez.

Az új egyéni szabályt a rendszer a szinkronizálási szabályoknak látható kell lennie.

### <a name="verify-the-change"></a>A módosítás ellenőrzése
Az új módosítását szeretné ellenőrizze, hogy a várt módon működik, és nem szűrész hibákról. Objektumok rendelkezik számától függően kétféleképpen különböző ezt a lépést.

1. A teljes szinkronizálás futtatása az összes objektum
2. Egyetlen objektum futtatni egy kép és a teljes szinkronizálás

Start **szinkronizálási szolgáltatás** a start menüből. Ebben a szakaszban ismertetett összes szerepelnek, az eszköz.

1. **Teljes szinkronizálás az összes objektum**  
   Válassza ki **összekötők** tetején. Az összekötőt, hogy módosította a ebben az esetben az Active Directory tartományi szolgáltatások, az előző szakaszban határozza meg és válassza ki azt. Válassza ki **futtatása** műveleteket, és válassza a **teljes szinkronizálás** és **OK**.
   ![Teljes szinkronizálás](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Az objektumok most frissíti benne a metaverzumba. Szeretné nézze meg az objektum tartozik a metaverzumba.
2. **Kép és a teljes szinkronizálás az egy adott objektum**  
   Válassza ki **összekötők** tetején. Az összekötőt, hogy módosította a ebben az esetben az Active Directory tartományi szolgáltatások, az előző szakaszban határozza meg és válassza ki azt. Válassza ki **Összekötőtér keresési**. A módosítás teszteléséhez használni kívánt objektum kereséséhez használja a hatókört. Jelölje ki az objektumot, és kattintson a **előzetes**. Az új képernyőn válassza ki a **véglegesítése előzetes**.  
   ![Előzetes verzió véglegesítése](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   A módosítás most elkötelezte magát a metaverzumba.

**Nézze meg az objektum a metaverzumban**  
Szeretné válassza ki, győződjön meg arról, hogy az érték várt néhány minta objektumok és, hogy a szabály alkalmazása. Válassza ki **Metaverzum-keresés** a lista elejéről. Bármely szűrő kell megkeresni a releváns objektumok hozzáadása. Nyissa meg a keresési eredmény egy objektumot. Nézze meg az attribútumértékek és is ellenőrizheti a **szinkronizálási szabályok** oszlopot, amely a szabály alkalmazása a várt módon.  
![Keresés a metaverzumban](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Az ütemező engedélyezése
Ha a várt módon van, engedélyezheti az ütemező újra. A PowerShell, futtassa a `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Más közös Attribútummódosítások folyamata
Az előző szakaszban leírt módosításokat szeretne egy Attribútumfolyam hogyan. Ez a szakasz néhány további példákat rendelkezésre állnak. A szinkronizálási szabály létrehozása lépéseinek rövidítése, de a teljes lépéseket az előző szakaszban található.

### <a name="use-another-attribute-than-the-default"></a>Az alapértelmezett egy másik attribútum használja
A Fabrikam nincs egy erdő, ahol a helyi ábécé szolgál a megadott névvel, a Vezetéknév és a megjelenített név. Ezek az attribútumok latin betűkből álló ábrázolását kiterjesztési attribútumot is található. Ha a globális címlista felépítése az Azure AD és az Office 365, a szervezet által ezek az attribútumok használni helyette.

Az alapértelmezett beállításokkal a helyi erdő egy objektum néz ki:  
![Attribútumfolyam 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Olyan szabály létrehozására a más attribútumfolyamok, tegye a következőket:

* Start **szinkronizálási szabály szerkesztő** a start menüből.
* A **bejövő** balra van jelölve, kattintson a gombra **új szabály hozzáadása**.
* Adjon a szabálynak egy nevet és leírást. Válassza ki a helyszíni Active Directory és a kapcsolódó objektumtípusokat. A **kapcsolattípus**, jelölje be **csatlakozás**. A sorrend válasszon ki egy szám, amely egy másik szabály nem használja. Out-of-box szabályok indítsa el a 100, így 50 értékkel használható ebben a példában.
  ![Attribútumfolyam 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
* Hatókör hagyja üresen (Ez azt jelenti, hogy kell alkalmazni az erdőben lévő összes felhasználói objektum).
* Csatlakoztatás szabályok hagyja üresen (Ez azt jelenti, hogy az illesztések kezelni out-of-box szabály).
* A átalakítások a következő adatfolyamok létrehozása:  
  ![Attribútumfolyam 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
* Kattintson a **Hozzáadás** a szabály mentéséhez.
* Ugrás a **Synchronization Service Managert**. A **összekötők**, válassza ki az összekötőt, ha a szabály hozzáadott. Válassza ki **futtatása**, és **teljes szinkronizálás**. A teljes szinkronizálás újraszámítja az aktuális szabályokkal összes objektumot.

Ez az az eredmény a következő egyéni szabálynak ugyanahhoz az objektumhoz:  
![Attribútumfolyam 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Attribútumok hossza
Karakterlánc attribútum alapértelmezett értéke lehet példánycímkének és a hossza legfeljebb 448 karakter lehet. Ha több előfordulhat, hogy tartalmazó karakterlánc-attribútumok dolgozik, majd ellenőrizze, hogy tartalmazza a következőt az Attribútumfolyam:  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>A userPrincipalSuffix módosítása
A userPrincipalName attribútum az Active Directory a felhasználók mindig nem ismert, és nem feltétlenül felel meg a bejelentkezési azonosítót. Az Azure AD Connect sync telepítési varázsló lehetővé teszi, hogy egy másik attribútum kiadási például az e-mail. Azonban bizonyos esetekben az attribútum ki kell számítani. Például a Contoso vállalat két Azure AD-címtártól, az egyik az éles és egy tesztelési rendelkezik. Szeretnék a saját tesztelési bérlőn egy másik utótag használata a bejelentkezési azonosítót.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Ebben a kifejezésben érvénybe mindent az első bal @-sign (Word) és ÖSSZEFŰZ rögzített karakterláncot.

### <a name="convert-a-multi-value-to-a-single-value"></a>Több érték konvertálása egy egyértékű
Egyes attribútumok az Active Directory többértékű a séma, annak ellenére, hogy a keresőmotorok is egyetlen Active Directory – felhasználók és számítógépek értékelni. Példa: a leírás attribútuma.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Ebben a kifejezésben abban az esetben, ha az attribútum értéke, az első elem (elem) igénybe vehet a attribútumban, távolítsa el a kezdő és záró szóközök (vágás), és majd hagyja a karakterláncban a először 448 karaktereket (a bal oldali).

### <a name="do-not-flow-an-attribute"></a>Nem egymást követő attribútum
A forgatókönyvhöz, amelyben ez a szakasz a háttérben, lásd: [szabályozhatja a attribútum áramlási folyamat](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Két módon nem flow attribútum. Az első elérhető a telepítési varázsló, és lehetővé teszi [távolítsa el a kiválasztott attribútumok](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Ez a beállítás akkor működik, ha még nem szinkronizálta a attribútum előtt. Azonban elindítását követően szinkronizálja ezt az attribútumot, és később eltávolítja az ezzel a szolgáltatással, majd a szinkronizálási motor leállása kezelése a attribútum és a meglévő értékek megmaradnak az Azure ad-ben.

Ha szeretné eltávolítani egy attribútum értékét, és győződjön meg arról, hogy a jövőben nem terjeszthetők, helyette kell létrehoz egy egyéni szabályt.

A Fabrikam rájöttünk kell arról, hogy néhány olyan azt szinkronizálása a felhőbe attribútum nem lehet van. Győződjön meg arról, hogy ezek az attribútumok el lesznek távolítva az Azure AD szeretnénk.  
![Hibás a Bővítményattribútumokat](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

* Egy új bejövő szinkronizálási szabályának létrehozása és feltöltése, a leírás ![leírása](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
* Hozzon létre attribútumfolyamok típusú **kifejezés** és a forrás **AuthoritativeNull**. A szövegkonstans **AuthoritativeNull** azt jelzi, hogy az érték lehet üres a MV-ban még akkor is, ha egy alacsonyabb sorrend szinkronizálási szabály megpróbálja feltölteni az érték.
  ![A kiterjesztési attribútumot átalakítása](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
* A szinkronizálási szabály mentéséhez. Start **szinkronizálási szolgáltatás**, az összekötő található, válassza ki **futtatása**, és **teljes szinkronizálás**. Ezt a lépést minden attribútumfolyamok újraszámítja.
* Ellenőrizze, hogy a kívánt módosításokat a kapcsolódási térbe keresve exportálásra váró adatokat.
  ![Előkészített törlése](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Szabályok létrehozása a PowerShell használatával
Jól a szinkronizálási szabály szerkesztővel működik, ha csak néhány módosítások végrehajtásához. Ha sok módosítani kell, majd PowerShell jobb megoldás lehet. Néhány speciális funkció csak érhetők el a PowerShell használatával.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>A PowerShell parancsfájl lekérése az out-of-box szabály
Tekintse meg a PowerShell parancsfájl, amely egy out-of-box szabályt létrehozni, jelölje ki a szabályt a szinkronizálási szabályok szerkesztőben, és kattintson a **exportálása**. Ez a művelet lehetővé teszi, hogy a szabály létrehozása a PowerShell-parancsfájlt.

### <a name="advanced-precedence"></a>Speciális sorrendje
A out-of-box szinkronizálási szabályok a sorrend értéke 100 kezdődik. Ha több erdővel rendelkezik, és sok egyéni módosításokat kell, majd 99 szinkronizálási szabályok nem elég lehet.

A szinkronizálási motor, amelyet az out-of-box szabályok előtt további szabályok utasíthatja. Ahhoz, hogy ez a viselkedés, kövesse az alábbi lépéseket:

1. Jelölje meg az első out-of-box szinkronizálási szabály (Ez a szabály a **a az AD-felhasználó csatlakozás**) a szinkronizálási szabály szerkesztőt, és válasszon **exportálása**. Másolja a SR azonosító értékét.  
![PowerShell módosítása előtt](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Az új szinkronizálási szabály létrehozása. A szinkronizálási szabály szerkesztő segítségével hozza létre. A szabály PowerShell-parancsfájlba exportálni.
3. A tulajdonság **PrecedenceBefore**, szúrja be az azonosító értéket a out-of-box szabályból. Állítsa be a **sorrend** való **0**. Győződjön meg arról, hogy az egyedi azonosító attribútum és nem egy GUID Azonosítót, egy másik szabály újbóli felhasználása. Győződjön meg arról is, amely a **ImmutableTag** tulajdonsága nincs beállítva; ez a tulajdonság csak az out-of-box szabályok állítható be. Mentse a PowerShell-parancsfájlt, és futtassa. Az eredménye, hogy az egyéni szabály hozzá van rendelve a sorrend értéke 100, és minden más szabállyal out-of-box növeli.  
![PowerShell módosítása után](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Akkor is azonos sok egyéni szinkronizálási szabályok **PrecedenceBefore** értéke, ha szükséges.

## <a name="enable-synchronization-of-preferreddatalocation"></a>PreferredDataLocation szinkronizálásának engedélyezése
Alapértelmezés szerint a felhasználók az Office 365-erőforrások és az Azure AD-bérlő ugyanabban a régióban találhatók. Például ha a bérlő Észak-Amerika található, majd a felhasználó Exchange-postaládák is található Észak-Amerikában. Több nemzeti szervezet számára ez nem feltétlenül optimális. A felhasználó régió úgy, hogy az attribútum preferredDataLocation adható meg.

Az Office 365-ben a régiók a következők:

| Régió | Leírás |
| --- | --- |
| NÉV | Észak-Amerika |
| EUR | Európa |
| APC | Ázsia és a Csendes-óceáni térség |
| JPN | Japán |
| AUSZTRÁLIAI | Ausztrália |
| IS | Kanada |
| GBR | Nagy-Britannia |
| LAM | Latin-Amerika |

Nem minden Office 365 számítási feladattal támogatja a felhasználó régió beállítása.

Az Azure AD Connect szinkronizálásának támogatja a **PreferredDataLocation** az attribútum **felhasználói** objektumok verziójában 1.1.524.0 és után. Pontosabban a következő módosításokat vezettek:

* A séma az objektumtípus **felhasználói** a az Azure AD-összekötő az időtartam PreferredDataLocation attribútum, amely egyértékű karakterlánc típusú.

* A séma az objektumtípus **személy** a Metaverzumban ki van bővítve PreferredDataLocation attribútum, amely karakterlánc típusú, és egyértékű tartalmazza.

Alapértelmezés szerint a PreferredDataLocation attribútum nincs engedélyezve a szinkronizáláshoz, mert nincs megfelelő PreferredDataLocation attribútum a helyszíni Active Directoryban. Manuálisan kell engedélyezné a szinkronizálást.

> [!IMPORTANT]
> Jelenleg az Azure AD lehetővé teszi, hogy a szinkronizált felhasználói objektumok és a felhő felhasználói PreferredDataLocation attribútum objektumok közvetlenül segítségével konfigurálható: az Azure AD PowerShell segítségével. Miután engedélyezte a szinkronizálási attribútum PreferredDataLocation, le kell állítania az Azure AD PowerShell segítségével állítsa be az attribútumot a **szinkronizált felhasználói objektumok** , az Azure AD Connect felülírják őket alapján a forrás attribútumértékek a helyszíni Active Directoryban.

> [!IMPORTANT]
> 2017. szeptember 1., mert az Azure AD már nem engedélyezi a PreferredDataLocation attribútum a **szinkronizált felhasználói objektumok** közvetlenül az Azure AD PowerShell kell konfigurálni. A szinkronizált felhasználói objektumok PreferredLocation attribútum konfigurálásához az Azure AD Connect kell használnia.

A szinkronizálás a PreferredDataLocation attribútum engedélyezése előtt kell:

 * Először döntse el, mely a helyszíni Active Directory-attribútumot, az adatforrás-attribútum használható. Típusúnak kell lennie **egyértékű karakterlánc**. Az egyik a extensionAttributes alábbi lépéseket a szolgál.

 * Ha korábban már konfigurálta a PreferredDataLocation attribútum a felhasználói objektumok meglévő szinkronizálva az Azure AD PowerShell Azure AD-ben, meg kell **backport** attribútumértékek a megfelelő felhasználói objektumok a helyszíni Active Directoryba.

    > [!IMPORTANT]
    > Ebben az esetben nem backport a helyszíni Active Directoryban a megfelelő felhasználói objektumok attribútum értékeket az Azure AD Connect eltávolítja a meglévő attribútumértékek az Azure AD, ha a szinkronizálás a PreferredDataLocation attribútum engedélyezve van.

 * Ajánlott a forrásattribútum konfigurálja a helyszíni legalább néhány AD-felhasználó objektumokat, ellenőrzésre később is használható.

A szinkronizálási attribútum PreferredDataLocation engedélyezésének lépései összegezhető:

1. Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
2. Az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma
3. Az Azure AD-összekötő séma PreferredDataLocation hozzáadása
4. Az attribútum értékét a helyszíni Active Directoryból rendszer vonatkozó bejövő szinkronizálási szabály létrehozása
5. Az attribútumérték az Azure AD-rendszer kimenő szinkronizálási szabály létrehozása
6. Futtasson teljes szinkronizálási ciklust
7. Szinkronizálásütemezési engedélyezése

> [!NOTE]
> Ez a szakasz a többi ezeket a lépéseket, részleteket ismertet. Azok a környezet az Azure AD központi telepítés egyerdős topológiával rendelkező és anélküli egyéni szinkronizálási szabályait ismerteti. Ha több erdőből topológia, egyéni szinkronizálási szabályok konfigurálva, vagy egy átmeneti kiszolgálón rendelkezik, ennek megfelelően módosítsa a lépéseket kell.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
Győződjön meg arról, szinkronizálás nem történik meg, amíg nem szándékolt módosításokat az Azure AD-exportálás alatt álló elkerülése érdekében a szinkronizálási szabályok frissítése közepén. A beépített szinkronizálásütemezési letiltása:

1. Indítsa el a PowerShell-munkamenetben az Azure AD Connect kiszolgálón.
2. A parancsmag futtatásával ütemezett szinkronizálás letiltása: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Indítsa el a **Synchronization Service Managert** címen **START** > **szinkronizálási szolgáltatás**.
4. Lépjen a **műveletek** lapra, és ellenőrizze, nincs az állapottal művelet *folyamatban*.

![Synchronization Service Managert - ellenőrizze, nincs folyamatban lévő műveletekkel](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>2. lépés: Az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma
Nem minden AD attribútumot a rendszer importálta a helyszíni AD kapcsolódási térbe. Ha be van jelölve, alapértelmezés szerint nem szinkronizált attribútum használatára, akkor szüksége importálnia. Az adatforrás-attribútum hozzáadása az importált attribútumok listáját:

1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza **tulajdonságok**.
3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
4. Ellenőrizze, hogy a kiválasztott használandó adatforrás-attribútum be van jelölve, a attribútum listában.
5. Kattintson a **OK** mentéséhez.

![Adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step2.png)

### <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>3. lépés: Az Azure AD-összekötő séma PreferredDataLocation hozzáadása
Alapértelmezés szerint a PreferredDataLocation attribútum nem importálja az Azure AD-kapcsolódási térbe kerülnek. Importált attribútumok listáját a PreferredDataLocation attribútum hozzáadása:

1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
2. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **tulajdonságok**.
3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
4. Attribútum listáján válassza ki a PreferredDataLocation attribútum.
5. Kattintson a **OK** mentéséhez.

![Adatforrás-attribútum hozzáadása az Azure AD-összekötő séma](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step3.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>4. lépés: Az attribútum értékét a helyszíni Active Directoryból rendszer vonatkozó bejövő szinkronizálási szabály létrehozása
A bejövő szinkronizálási szabály lehetővé teszi a forrásattribútum a helyszíni Active Directoryból a Metaverzumba haladjanak attribútumérték:

1. Indítsa el a **szinkronizálási szabályok szerkesztő** címen **START** > **szinkronizálási szabályok szerkesztő**.
2. Állítsa be a keresési szűrő **irány** kell **bejövő**.
3. Kattintson a **új szabály hozzáadása** gombra kattintva hozzon létre egy új bejövő szabályt.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például *"az ad-felhasználó PreferredDataLocation"* |
    | Leírás | *Adjon meg egy egyéni leírást* |  |
    | Csatlakoztatott rendszer | *Válassza ki a helyszíni AD-összekötő* |  |
    | Objektumtípus csatlakoztatva | **Felhasználó** |  |
    | Metaverzum-objektum típusa | **Person** |  |
    | Kapcsolat típusa | **Csatlakozás** |  |
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Tartsa a **Scoping szűrő** üres összes objektumára. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.
6. Lépjen a **átalakítása lapon** és valósítja meg a következő átalakítási szabályt:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    |Közvetlen | PreferredDataLocation | Válassza ki az adatforrás-attribútum | Nincs bejelölve | Frissítés |

7. Kattintson a **Hozzáadás** a bejövő szabály létrehozásához.

![Bejövő szinkronizálási szabályának létrehozása](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step4.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>5. lépés: Az attribútum értéke az Azure AD-rendszer kimenő szinkronizálási szabály létrehozása
A kimenő szinkronizálási szabály lehetővé teszi az attribútumérték felé haladjanak a Metaverzumba a PreferredDataLocation attribútumhoz az Azure ad-ben:

1. Lépjen a **szinkronizálási szabályok** szerkesztő.
2. Állítsa be a keresési szűrő **irány** kell **kimenő**.
3. Kattintson a **új szabály hozzáadása** gombra.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | ----- | ------ | --- |
    | Name (Név) | *Adjon meg egy nevet* | Ha például a "Kimenő az aad-ben – felhasználói PreferredDataLocation" |
    | Leírás | *Adjon meg egy leírást* ||
    | Csatlakoztatott rendszer | *Válassza ki az AAD-összekötő* ||
    | Objektumtípus csatlakoztatva | Felhasználó ||
    | Metaverzum-objektum típusa | **Person** ||
    | Kapcsolat típusa | **Csatlakozás** ||
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Lépjen a **Scoping szűrő** lapra, és adja hozzá a **egyetlen tartalmazó szűrő csoportban található, két záradékok**:

    | Attribútum | Operátor | Érték |
    | --- | --- | --- |
    | sourceObjectType | EGYENLŐ | Felhasználó |
    | cloudMastered | NOTEQUAL | True (Igaz) |

    Hatókörként szűrő határozza meg, mely az Azure AD-objektumokat a kimenő szinkronizálási szabály vonatkozik. A jelen példában használjuk "Az AD-felhasználók identitását Out" azonos tartalmazó szűrőjének OOB-szinkronizálási szabály. Megakadályozza, hogy a szinkronizálási szabály alkalmazták felhasználói objektumok, amelyek nincsenek szinkronizálva a helyszíni Active Directoryból. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.

6. Lépjen a **átalakítása** lapra, és valósítja meg a következő átalakítási szabályt:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | PreferredDataLocation | PreferredDataLocation | Nincs bejelölve | Frissítés |

7. Bezárás **Hozzáadás** a kimenő forgalomra vonatkozó szabály létrehozásához.

![Kimenő szinkronizálási szabály létrehozása](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step5.png)

### <a name="step-6-run-full-synchronization-cycle"></a>6. lépés: Teljes szinkronizálás futtatása ciklus
Általában teljes szinkronizálási ciklust azért szükséges, mert mindkét az ad jelentek meg új attribútumokat és Azure AD-összekötőt, valamint a bevezetett egyéni szinkronizálási szabályait. Javasoljuk, hogy a módosítások ellenőrzéséhez őket az Azure AD-exportálás előtt. Az alábbi lépések segítségével a módosítások ellenőrzéséhez a lépéseket egy teljes szinkronizálási ciklust alkotó manuális futtatása során.

1. Futtatás **teljes importálás** a lépés a **helyszíni AD-összekötő**:

   1. Lépjen a **műveletek** lapon a Synchronization Service Managert.

   2. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza **futtatása...** .

   3. Az előugró párbeszédpanelen válassza ki a **teljes importálás** kattintson **OK**.

   4. Várjon, amíg a művelet befejezését.

    > [!NOTE]
    > Teljes importálás kihagyhatja a helyszíni AD-összekötő, ha az adatforrás-attribútum már szerepel a listájában importált attribútumok. Ez azt jelenti, hogy nem rendelkezett során bármilyen módosítást [2. lépés: az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Futtatás **teljes importálás** a lépés a **Azure AD-összekötő**:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **futtatása...**

   2. Az előugró párbeszédpanelen válassza ki a **teljes importálás** kattintson **OK**.

   3. Várjon, amíg a művelet befejezését.

3. A felhasználói objektum, a szinkronizálási szabály módosítások ellenőrzéséhez:

A forrásattribútumot, a helyszíni Active Directory és az Azure AD PreferredDataLocation importálta a megfelelő kapcsolódási térbe kerülnek. A teljes szinkronizálás lépés folytatása előtt javasoljuk, hogy végezzen egy **előzetes** egy meglévő felhasználó az objektum a helyszíni AD kapcsolódási térbe. Az objektum, amelyet kivételezett kell rendelkeznie a forrásattribútum feltöltve. A sikeres **előzetes** a feltöltve a Metaverzumban PreferredDataLocation egy jól jelzi, hogy konfigurálta a szinkronizálás szabályainak megfelelően. Ehhez információ egy **előzetes**, tekintse át a részt [ellenőrzéséhez](#verify-the-change).

4. Futtatás **teljes szinkronizálás** a lépés a **helyszíni AD-összekötő**:

   1. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza **futtatása...** .

   2. Az előugró párbeszédpanelen válassza ki a **teljes szinkronizálás** kattintson **OK**.

   3. Várjon, amíg a művelet befejezését.

5. Győződjön meg arról **függőben lévő Exportálásokról** az Azure AD:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **Összekötőtér keresési**.

   2. Összekötőtér keresési előugró párbeszédpanelen:

      1. Állítsa be **hatókör** való **exportálási függőben lévő**.

      2. Ellenőrizze a három jelölőnégyzetet, beleértve a **hozzáadása, módosítása és törlése**.

      3. Kattintson a **keresési** gombra kell kattintania a módosítások exportálható objektumok listáját. Vizsgálja meg az adott objektum a módosításokat, kattintson duplán az objektum.

      4. Ellenőrizze, hogy a változások várhatók.

6. Futtatás **exportálása** a lépés a **Azure AD Connectoron**

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **futtatása...** .

   2. Az összekötő futtatásához előugró párbeszédpanelen válassza ki **exportálása** kattintson **OK**.

   3. Várjon, amíg befejeződik az Azure Active Directoryba való exportálás.

> [!NOTE]
> Előfordulhat, hogy a lépéseket tartalmazza a teljes szinkronizálás lépésben az Azure AD Connectoron, és az AD Connectoron exportálás. A lépések esetén nincs szükség, mert az attribútum értékei vannak áramlanak a helyszíni Active Directory csak az Azure ad.

### <a name="step-7-re-enable-sync-scheduler"></a>7. lépés: Engedélyezze újra a szinkronizálásütemező
Engedélyezze újra a beépített szinkronizálásütemezési:

1. Indítsa el a PowerShell-munkamenetben.
2. Újra engedélyezi az ütemezett szinkronizálás parancsmag futtatásával:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="enable-synchronization-of-usertype"></a>UserType szinkronizálásának engedélyezése
Az Azure AD Connect szinkronizálásának támogatja a **UserType** az attribútum **felhasználói** objektumok verziójában 1.1.524.0 és után. Pontosabban a következő módosításokat vezettek:

- A séma az objektumtípus **felhasználói** a az Azure AD-összekötő az időtartam UserType attribútuma, amely karakterlánc típusú, és egyértékű tartalmazza.
- A séma az objektumtípus **személy** a Metaverzumban ki van bővítve UserType attribútuma, amely karakterlánc típusú, és egyértékű tartalmazza.

Alapértelmezés szerint a UserType attribútuma nincs engedélyezve a szinkronizáláshoz, mert nincs megfelelő UserType attribútum a helyszíni Active Directoryban. Manuálisan kell engedélyezné a szinkronizálást. Ahhoz, hogy a UserType attribútuma szinkronizálását, jegyezze fel a következő viselkedés kényszeríti ki az Azure AD akkor kell végrehajtani:

- Az Azure AD csak két érték UserType attribútuma – fogad **tag** és **vendég**.
- Ha UserType attribútuma nem engedélyezett a szinkronizálás az Azure AD Connectben, a címtár-szinkronizálás segítségével létrehozott Azure Active Directory-felhasználók kellene UserType attribútuma beállítása **tag**.
- Az Azure AD UserType attribútuma nem engedélyezi a meglévő Azure AD-felhasználók az Azure AD Connect módosítható. Csak beállítható az Azure AD-felhasználók létrehozása közben.

Ahhoz, hogy a UserType attribútuma szinkronizálását, el kell döntenie hogyan a UserType attribútuma származik a helyszíni AD. Két gyakori módszer a következők:

- Egy nem használt kijelölni a helyszíni AD-attribútum (például extensionAttribute1), az adatforrás-attribútum használható. A kijelölt helyszíni AD attribútum típusúnak kell lennie **karakterlánc**értéket tartalmaz, amely a egyértékű **tag** vagy **vendég**. Ha ezt a módszert választja, gondoskodnia kell arról, hogy a kijelölt attribútum a helyes értéket minden meglévő felhasználói objektumhoz a helyszíni Active Directoryban, amely szinkronizálva az Azure AD-UserType attribútuma szinkronizálásának engedélyezése előtt a telepítéskor .
- Másik lehetőségként UserType attribútum értéke is származik egyéb tulajdonságait. Például minden felhasználó szinkronizálni vendégként, ha szeretné a helyszíni AD UserPrincipalName attribútum a tartományrészt végződik "@partners.fabrikam123.org". Ahogy korábban említettük, az Azure AD Connect nem teszi lehetővé UserType attribútuma a meglévő Azure AD-felhasználók az Azure AD Connect módosítható. Ezért akkor biztosítania kell, hogy kiválasztotta konzisztensek legyenek hogyan UserType attribútuma már be van állítva az összes meglévő Azure Active Directory-felhasználók az Ön bérelt szolgáltatásának logikát.

Történő engedélyezéséről a UserType attribútuma szinkronizálását összegezhető:

>[!NOTE]
> Ez a szakasz a többi ezeket a lépéseket ismerteti. Azok a környezet az Azure AD központi telepítés egyerdős topológiával rendelkező és anélküli egyéni szinkronizálási szabályait ismerteti. Ha több erdőből topológia, egyéni szinkronizálási szabályok konfigurálva, vagy egy átmeneti kiszolgálón rendelkezik, ennek megfelelően módosítsa a lépéseket kell.

1.  Tiltsa le a **Feladatütemező szinkronizálása** , és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
2.  Adja hozzá a **forrásattribútum** a helyszíni AD-összekötő séma
3.  Adja hozzá **UserType** az Azure AD-összekötő séma
4.  Az attribútum értékét a helyszíni Active Directoryból rendszer vonatkozó bejövő szinkronizálási szabály létrehozása
5.  Az attribútumérték az Azure AD-rendszer kimenő szinkronizálási szabály létrehozása
6.  Futtatás **teljes szinkronizálás** ciklus
7.  Engedélyezése **Feladatütemező szinkronizálása**


### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
Győződjön meg arról, szinkronizálás nem történik meg, amíg nem szándékolt módosításokat az Azure AD-exportálás alatt álló elkerülése érdekében a szinkronizálási szabályok frissítése közepén. A beépített szinkronizálásütemezési letiltása:

 1. Indítsa el a PowerShell-munkamenetben az Azure AD Connect-kiszolgálón.
 2. Tiltsa le az ütemezett szinkronizálás parancsmag futtatásával:`Set-ADSyncScheduler -SyncCycleEnabled $false`
 3. Indítsa el a **Synchronization Service Managert** START → szinkronizálási szolgáltatás címen.
 4. Lépjen a **műveletek** lapra, és ellenőrizze, nincs művelet, amelynek állapota *"folyamatban."*

![Synchronization Service Managert - ellenőrizze, nincs folyamatban lévő műveletekkel](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>2. lépés: Az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma
Nem minden AD attribútumot a rendszer importálta a helyszíni AD kapcsolódási térbe. Az adatforrás-attribútum hozzáadása az importált attribútumok listáját:

 1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
 2. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza **tulajdonságok**.
 3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
 4. Győződjön meg arról, hogy az adatforrás-attribútum be van jelölve, a attribútum listában.
 5. Kattintson a **OK** mentéséhez.
![Adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-usertype-to-the-azure-ad-connector-schema"></a>3. lépés: Az Azure AD-összekötő séma UserType hozzáadása
Alapértelmezés szerint az Azure AD Connect terület UserType attribútuma nem importálta. Az importált attribútumlistát UserType attribútuma hozzáadása:

 1. Lépjen a **összekötők** lapon a Synchronization Service Managert.
 2. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **tulajdonságok**.
 3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
 4. Ellenőrizze, hogy a PreferredDataLocation attribútum be van jelölve, a attribútum listában.
 5. Kattintson a **OK** mentéséhez.

![Adatforrás-attribútum hozzáadása az Azure AD-összekötő séma](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>4. lépés: Az attribútum értékét a helyszíni Active Directoryból rendszer vonatkozó bejövő szinkronizálási szabály létrehozása
A bejövő szinkronizálási szabály lehetővé teszi a forrásattribútum a helyszíni Active Directoryból a Metaverzumba haladjanak attribútumérték:

1. Indítsa el a **szinkronizálási szabályok szerkesztő** START → szinkronizálási szabályok szerkesztő címen.
2. Állítsa be a keresési szűrő **irány** kell **bejövő**.
3. Kattintson a **új szabály hozzáadása** gombra kattintva hozzon létre egy új bejövő szabályt.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például *"az ad-felhasználó UserType"* |
    | Leírás | *Adjon meg egy leírást* |  |
    | Csatlakoztatott rendszer | *Válassza ki a helyszíni AD-összekötő* |  |
    | Objektumtípus csatlakoztatva | **Felhasználó** |  |
    | Metaverzum-objektum típusa | **Person** |  |
    | Kapcsolat típusa | **Csatlakozás** |  |
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Lépjen a **Scoping szűrő** lapra, és adja hozzá a **egyetlen tartalmazó szűrő csoportban található, a következő záradék**:

    | Attribútum | Operátor | Érték |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Felhasználó\_ |

    Tartalmazó szűrő határozza meg, amely a helyszíni AD-objektumokat a vonatkozó bejövő szinkronizálási szabály vonatkozik. Ebben a példában használjuk a azonos tartalmazó szűrő használt "a az AD-felhasználó közös" OOB szinkronizálási szabály, amely megakadályozza, hogy a szinkronizálási szabály Azure AD-felhasználó-visszaírás funkción keresztül létrehozott felhasználói objektumok vonatkozik. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.

6. Lépjen a **átalakítása lapon** és valósítja meg a kívánt átalakítási szabállyal. Például kijelölt egy nem használt helyszíni AD-attribútum (például extensionAttribute1) UserType adatforrás-attribútum, mint egy közvetlen Attribútumfolyam valósíthat meg:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | UserType | extensionAttribute1 | Nincs bejelölve | Frissítés |

    Egy másik példa – érdemes UserType attribútuma értéke származik a többi tulajdonság. Például minden felhasználó szinkronizálni vendégként, ha szeretné a helyszíni AD UserPrincipalName attribútum a tartományrészt végződik "@partners.fabrikam123.org". Egy kifejezés valósíthatja meg:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | UserType | IIf(IsPresent([userPrincipalName]),IIf(CBool(Instr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0), "Tag", "Vendég"), hiba ("UserPrincipalName nincs jelen UserType meghatározásához")) | Nincs bejelölve | Frissítés |

7. Kattintson a **Hozzáadás** a bejövő szabály létrehozásához.

![Bejövő szinkronizálási szabályának létrehozása](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>5. lépés: Az attribútum értéke az Azure AD-rendszer kimenő szinkronizálási szabály létrehozása
A kimenő szinkronizálási szabály lehetővé teszi az attribútumérték felé haladjanak a Metaverzumba a PreferredDataLocation attribútumhoz az Azure ad-ben:

1. Lépjen a **szinkronizálási szabályok** szerkesztő.
2. Állítsa be a keresési szűrő **irány** kell **kimenő**.
3. Kattintson a **új szabály hozzáadása** gombra.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | ----- | ------ | --- |
    | Name (Név) | *Adjon meg egy nevet* | Ha például a "Kimenő az aad-ben – felhasználói UserType" |
    | Leírás | *Adjon meg egy leírást* ||
    | Csatlakoztatott rendszer | *Válassza ki az AAD-összekötő* ||
    | Objektumtípus csatlakoztatva | Felhasználó ||
    | Metaverzum-objektum típusa | **Person** ||
    | Kapcsolat típusa | **Csatlakozás** ||
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. YDo nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Lépjen a **Scoping szűrő** lapra, és adja hozzá a **egyetlen tartalmazó szűrő csoportban található, két záradékok**:

    | Attribútum | Operátor | Érték |
    | --- | --- | --- |
    | sourceObjectType | EGYENLŐ | Felhasználó |
    | cloudMastered | NOTEQUAL | True (Igaz) |

    Hatókörként szűrő határozza meg, mely az Azure AD-objektumokat a kimenő szinkronizálási szabály vonatkozik. A jelen példában használjuk "Az AD-felhasználók identitását Out" azonos tartalmazó szűrőjének OOB-szinkronizálási szabály. Megakadályozza, hogy a szinkronizálási szabály alkalmazták felhasználói objektumok, amelyek nincsenek szinkronizálva a helyszíni Active Directoryból. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.

6. Lépjen a **átalakítása** lapra, és valósítja meg a következő átalakítási szabályt:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | UserType | UserType | Nincs bejelölve | Frissítés |

7. Bezárás **Hozzáadás** a kimenő forgalomra vonatkozó szabály létrehozásához.

![Kimenő szinkronizálási szabály létrehozása](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-full-synchronization-cycle"></a>6. lépés: Teljes szinkronizálás futtatása ciklus
Általában teljes szinkronizálási ciklust azért szükséges, mert mindkét az ad jelentek meg új attribútumokat és Azure AD-összekötőt, valamint a bevezetett egyéni szinkronizálási szabályait. Javasoljuk, hogy a módosítások ellenőrzéséhez őket az Azure AD-exportálás előtt. Az alábbi lépések segítségével a módosítások ellenőrzéséhez a lépéseket egy teljes szinkronizálási ciklust alkotó manuális futtatása során.

1. Futtatás **teljes importálás** a lépés a **helyszíni AD-összekötő**:

   1. Lépjen a **műveletek** lapon a Synchronization Service Managert.
   2. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza ki **futtatása...**
   3. Az előugró párbeszédpanelen válassza ki a **teljes importálás** kattintson **OK**.
   4. Várjon, amíg a művelet befejezését.

    > [!NOTE]
    > Teljes importálás kihagyhatja a helyszíni AD-összekötő, ha az adatforrás-attribútum már szerepel a listájában importált attribútumok. Ez azt jelenti, hogy nem rendelkezett során bármilyen módosítást [2. lépés: az adatforrás-attribútum hozzáadása a helyszíni AD-összekötő séma](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Futtatás **teljes importálás** a lépés a **Azure AD-összekötő**:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza ki **futtatása...**
   2. Az előugró párbeszédpanelen válassza ki a **teljes importálás** kattintson **OK**.
   3. Várjon, amíg a művelet befejezését.

3. A felhasználói objektum, a szinkronizálási szabály módosítások ellenőrzéséhez:

    A forrásattribútumot, a helyszíni Active Directory és az Azure AD UserType importálva lettek a megfelelő összekötő területet. Teljes szinkronizálás lépés folytatása előtt javasoljuk, hogy végezzen egy **előzetes** egy meglévő felhasználó az objektum a helyszíni AD kapcsolódási térbe. Az objektum, amelyet kivételezett kell rendelkeznie a forrásattribútum feltöltve. A sikeres **előzetes** feltöltve a Metaverzumban UserType egy jól jelzi, hogy konfigurálta a szinkronizálás szabályainak megfelelően. Ehhez információ egy **előzetes**, tekintse át a részt [ellenőrzéséhez](#verify-the-change).

4. Futtatás **teljes szinkronizálás** a lépés a **helyszíni AD-összekötő**:

   1. Kattintson a jobb gombbal a **helyszíni AD-összekötő** válassza ki **futtatása...**
   2. Az előugró párbeszédpanelen válassza ki a **teljes szinkronizálás** kattintson **OK**.
   3. Várjon, amíg a művelet befejezését.

5. Győződjön meg arról **függőben lévő Exportálásokról** az Azure AD:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **Összekötőtér keresési**.

   2. Összekötőtér keresési előugró párbeszédpanelen:

      1. Állítsa be **hatókör** való **exportálási függőben lévő**.
      2. Ellenőrizze a három jelölőnégyzetet, beleértve a **hozzáadása, módosítása és törlése**.
      3. Kattintson a **keresési** gombra kell kattintania a módosítások exportálható objektumok listáját. Vizsgálja meg az adott objektum a módosításokat, kattintson duplán az objektum.
      4. Ellenőrizze, hogy a változások várhatók.

6. Futtatás **exportálása** a lépés a **Azure AD Connectoron**

   1. Kattintson a jobb gombbal a **Azure AD-összekötő** válassza **futtatása...**
   2. Az összekötő futtatásához előugró párbeszédpanelen válassza ki **exportálása** kattintson **OK**.
   3. Várjon, amíg az exportálás végrehajtásához az Azure AD.

> [!NOTE]
> Előfordulhat, hogy a lépéseket tartalmazza a teljes szinkronizálás és az Azure AD Connectoron exportálási lépést. A lépések esetén nincs szükség, mert az attribútum értékei vannak áramlanak a helyszíni Active Directory csak az Azure ad.

### <a name="step-7-re-enable-sync-scheduler"></a>7. lépés: Engedélyezze újra a szinkronizálásütemező
Engedélyezze újra a beépített szinkronizálásütemezési:

1. Indítsa el a PowerShell-munkamenetben.
2. Újra engedélyezi az ütemezett szinkronizálás parancsmag futtatásával:`Set-ADSyncScheduler -SyncCycleEnabled $true`


## <a name="next-steps"></a>További lépések
* További információk a konfigurációs modell [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* További információk az a kifejezés nyelv [ismertetése deklaratív kiépítés kifejezések](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
