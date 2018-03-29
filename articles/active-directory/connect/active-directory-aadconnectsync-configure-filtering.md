---
title: 'Azure AD Connect szinkronizálása: szűrésének beállítása |} Microsoft Docs'
description: Ismerteti az Azure AD Connect-szinkronizálás szűrőjének konfigurálása.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 0b4b306d1224b5521774b05a110c862b58450eb3
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Az Azure AD Connect szinkronizálása: a szűrés konfigurálása
Szűrés segítségével szabályozhatja mely objektumok jelennek meg az Azure Active Directory (Azure AD) a helyi címtárban lévő. Az alapértelmezett konfiguráció összes objektumok minden tartományban a konfigurált erdők vesz igénybe. Ez általában az ajánlott konfiguráció. Felhasználók Office 365-munkaterhelések, például az Exchange Online és Skype vállalati verzióra, a kihasználhassa a teljes globális címlista, hogy e-mailek küldése és mindenki hívja. Az alapértelmezett konfigurációnál akkor ugyanazt a felhasználói élményt, hogy azok az Exchange vagy a Lync egy helyszíni példánnyal.

Egyes esetekben azonban Ön szükséges néhány módosítást az alapértelmezett konfigurációt. Néhány példa:

* Tervezi a [több Azure Active directory-topológia](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Akkor szükséges, egy szűrőt szabályozásához, hogy mely objektumok egy adott lettek szinkronizálva az Azure AD-címtár.
* A próbaüzem futtatja az Azure vagy Office 365, és csak kívánt a felhasználók egy része az Azure ad-ben. A kis próbaüzem fontos nem szeretné, hogy a funkció bemutatásához egy teljes globális címlista.
* Számos szolgáltatás és egyéb nem kívánt Azure AD-ben megbízhatóságának fiókok rendelkezik.
* Megfelelőségi okokból bármely felhasználói fiókok a helyszíni ne törölje. Csak letilthatja azokat. De az Azure ad-ben, azt szeretné, aktív fiókok meglétét.

Ez a cikk bemutatja, hogyan konfigurálhatja a különböző szűrési módszereket.

> [!IMPORTANT]
> A Microsoft nem támogatja a módosítása, vagy az Azure AD Connect szinkronizálási kívül a hivatalosan ismertetett műveletek működő. Ezek a műveletek okozhatja az Azure AD Connect szinkronizálási szolgáltatás inkonzisztens vagy nem támogatott állapotban. Ennek eredményeképpen a Microsoft nem adhatók meg a technikai támogatási szolgálathoz az ilyen környezetekben.

## <a name="basics-and-important-notes"></a>Alapvető tudnivalók és fontos megjegyzések
Az Azure AD Connect szinkronizálási szolgáltatás engedélyezheti a bármikor szűrést. Indítsa el a címtár-szinkronizálás alapértelmezett konfigurációja, és a szűrés konfigurálása, ha a program kiszűri az objektumok már nem szinkronizálódnak az Azure AD. Ez a változás miatt objektumokat az Azure ad-ben korábban szinkronizált, de majd szűrve voltak törlődnek az Azure ad-ben.

Szűrésének módosítása előtt győződjön meg arról, hogy Ön [tiltsa le az ütemezett feladatot](#disable-scheduled-task) , akkor véletlenül ne exportálja a módosításokat, amelyek még nem még ellenőrizve csak akkor lehet helyes.

Szűrés eltávolíthatja egyszerre sok objektumot, mert kívánt győződjön meg arról, hogy az új szűrőket helyesen-e a módosításokat az Azure AD-exportálás megkezdése előtt. Miután megadta a konfigurációs lépések, Határozottan javasoljuk, hogy kövesse a [ellenőrzési lépések](#apply-and-verify-changes) exportálása és az Azure AD-módosítások előtt.

Sok objektumok törlése véletlenül a szolgáltatás elleni "[véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)" alapértelmezés szerint be van kapcsolva. Törlése miatt (alapértelmezés szerint 500) szűrés sok objektum esetén kövesse a cikk a törlések fel az Azure AD lehetővé szeretné.

2015. November előtti használatakor a build ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) lehet módosítani egy szűrő konfigurálása és használata a Jelszókivonat-szinkronizálást, akkor szükséges, az összes jelszavak teljes szinkronizálási események indítása a konfiguráció befejezését követően. Indítás, a teljes jelszó-szinkronizálás lépéseiért lásd: [indul el, a teljes szinkronizálás az összes jelszavak](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Ha a számítógép összeállítási 1.0.9125 vagy újabb verzióját, majd a normál **teljes szinkronizálás** művelet is számítja ki, hogy szinkronizálni, és ha ezt a kiegészítő lépést már nincs szükség.

Ha **felhasználói** objektumok véletlenül törölték az Azure AD egy szűrési hiba miatt, létrehozhatja a felhasználói objektumok Azure AD-ben a szűrési beállítások eltávolításával. Majd újból szinkronizálhatja a címtárakat. Ez a művelet visszaállítja a felhasználók a Lomtár Azure AD-ben. Más típusú objektumokat azonban nem törlés visszavonása. Például ha véletlenül törli egy biztonsági csoportot, és való hozzáférés-vezérlési lista erőforrást, a csoport és a hozzáférés-vezérlési listák nem állítható helyre.

Az Azure AD Connect csak egyszer figyelembe kell lennie a hatókör-objektumok törlése. Ha egy másik szinkronizálási motor által létrehozott objektumok az Azure ad-ben, és ezeket az objektumokat nem, a hatókör, hozzáadása a szűrés nem távolítja azokat. Például ha megkezdése a teljes címtár teljes másolatának az Azure ad-ben létrehozott DirSync-kiszolgálóval, és a szűrés engedélyezve van az elejétől párhuzamosan egy új Azure AD Connect szinkronizálási kiszolgáló telepít, az Azure AD Connect nem távolítja el a felesleges DirSync által létrehozott objektumok.

A szűrési konfigurációs megőrződik telepíteni vagy frissíteni az Azure AD Connect egy újabb verzióra. Célszerű mindig ajánlott eljárás, győződjön meg arról, hogy a konfiguráció nem volt akaratlanul módosította frissítés után egy újabb verzióra az első szinkronizálási ciklust futtatása előtt.

Ha egynél több erdővel rendelkezik, majd telepítenie kell minden erdőbe (feltéve, hogy, hogy szeretné-e az összes azonos konfigurációjának) Ez a témakör ismerteti a szűrési konfigurációkat.

### <a name="disable-the-scheduled-task"></a>Az ütemezett feladat letiltása
A beépített ütemezési, amely elindítja a szinkronizálási ciklust 30 percenként letiltásához kövesse az alábbi lépéseket:

1. Ugrás a PowerShell kérése.
2. Futtatás `Set-ADSyncScheduler -SyncCycleEnabled $False` az ütemező letiltása.
3. Végezze el a módosításokat, amelyek szerepelnek a cikkben.
4. Futtatás `Set-ADSyncScheduler -SyncCycleEnabled $True` az ütemező ismét engedélyeznie.

**Ha az Azure AD Connect build 1.1.105.0 előtt**  
Az ütemezett feladat, amely elindítja a szinkronizálási ciklust három óránként letiltásához kövesse az alábbi lépéseket:

1. Start **Feladatütemező** a a **Start** menü.
2. Közvetlenül a **Feladatütemező könyvtár**, nevű feladat található **Azure AD Sync Scheduler**, kattintson a jobb gombbal, majd válassza **letiltása**.  
   ![A Feladatütemező](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Mostantól konfigurációs módosítások és a szinkronizálási motor manuálisan futtassa a **Synchronization Service Managert** konzol.

A szűrési módosítások elvégzése után ne feledje, hogy térjen vissza és **engedélyezése** újra a feladatot.

## <a name="filtering-options"></a>Szűrési beállítások
A következő szűrési konfiguráció esetében a címtár-Szinkronizáló eszköz alkalmazhatók:

* [**Csoportalapú**](#group-based-filtering): szűrés egyetlen csoport alapján csak konfigurálható a kezdeti telepítés a telepítési varázsló segítségével.
* [**Tartományi**](#domain-based-filtering): Ez a beállítás használatával kiválaszthatja, mely tartományok szinkronizálása az Azure AD. Is hozzá és távolíthat el tartományokat abból a szinkronizálási motor konfigurációját, amikor módosítja a helyszíni infrastruktúra az Azure AD Connect szinkronizálási szolgáltatás telepítése után.
* [**Szervezeti egység (OU) – alapú**](#organizational-unitbased-filtering): Ez a beállítás használatával kiválaszthatja, amely szervezeti egységek szinkronizálása az Azure AD. Ez a beállítás akkor a kiválasztott szervezeti minden objektum esetében.
* [**Attribútumalapú**](#attribute-based-filtering): Ez a beállítás használatával szűrheti a objektumokon attribútumértékei alapján objektumok. A különböző objektumtípusokra különböző szűrőket is lehet.

Egyszerre több szűrési beállítások is használhatja. Például használhatja OU-alapú szűrés csak egy szervezeti egység az objektumok tartalmazza. Egy időben segítségével attribútum alapú szűrés objektumok további szűréséhez. Több szűrési módszerek, a szűrők használata logikai "és" a szűrők között.

## <a name="domain-based-filtering"></a>Tartományalapú szűrés
A szakasz a tartomány szűrő konfigurálásának lépéseit ismerteti. Ha hozzáadott vagy eltávolított tartományok az erdőben, az Azure AD Connect telepítése után, akkor is a szűrési beállítások frissítése.

Az előnyben részesített módosítása a tartományalapú szűrés módja a telepítővarázsló futtatása, és válassza [tartományok és szervezeti egységek szűrése](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). A varázsló automatizálja a ebben a témakörben ismertetett feladatokat.

Ha Ön a varázsló nem futtatható a telepítés bármilyen okból csak kövesse ezeket a lépéseket.

Szűrési konfigurálása tartományi alábbi lépésekből áll:

1. [Válassza ki a tartományok](#select-domains-to-be-synchronized) a szinkronizálás szerepeltetni kívánt.
2. Minden hozzáadott, és a tartomány eltávolítása, a [futtatási profil](#update-run-profiles).
3. [Alkalmazza, és a módosítások ellenőrzéséhez](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Válassza ki a szinkronizálandó tartományok
A tartomány szűrő megadásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálási szolgáltatás egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoport.
2. Start **szinkronizálási szolgáltatás** a a **Start** menü.
3. Válassza ki **összekötők**, majd a a **összekötők** listára, válassza ki az összekötő típusú **Active Directory tartományi szolgáltatások**. A **műveletek**, jelölje be **tulajdonságok**.  
   ![Összekötő tulajdonságai](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Kattintson a **könyvtárpartíciók konfigurálása**.
5. Az a **válassza ki az a címtárpartíciókat** listában válassza ki, és törölje a tartományok, igény szerint. Győződjön meg arról, hogy csak azokat a partíciókat szinkronizálni kívánt vannak-e jelölve.  
   ![Partíciók](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
   Ha megváltoztatta a helyszíni Active Directory-infrastruktúrát és a hozzáadott vagy tartományok eltávolítja az erdőt, majd kattintson a **frissítése** gombra kell kattintania a frissített listáját. Frissítésekor, megkérdezi a hitelesítő adatokat. Adja meg a hitelesítő adatok olvasási joggal rendelkező Windows Server Active Directory. Nem kell lennie a felhasználót, hogy a rendszer előre feltöltve a párbeszédpanelen.  
   ![Frissítés szükséges](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Amikor elkészült, zárja be a **tulajdonságok** kattintva párbeszédpanel **OK**. Ha eltávolította a tartományok az erdőből származó, egy előugró üzenet szerint, hogy egy tartomány el lett távolítva, és ez a konfiguráció törlődnek.
7. Továbbra is úgy, hogy a [futtatási profil](#update-run-profiles).

### <a name="update-the-run-profiles"></a>A futtatási profil frissítése
Ha a tartomány szűrő frissítése is módosítania a futtatási profil.

1. Az a **összekötők** listában, győződjön meg arról, hogy az összekötő, amely az előző lépésben módosította van kiválasztva. A **műveletek**, jelölje be **Configure Run Profiles**.  
   ![Futtatási profil 1 összekötő](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  
2. Keresse meg, és azonosíthatja a következő profilokat:
    * Teljes importálás
    * Teljes szinkronizálás
    * Különbözeti importálás
    * A különbözeti szinkronizálás
    * Exportálás
3. Az egyes profilok, állítsa be a **hozzáadott** és **eltávolított** tartományok.
    1. Minden egyes az öt profilok esetében a következő lépések az egyes **hozzáadott** tartomány:
        1. Válassza ki a futtatási profil, és kattintson a **új lépés**.
        2. Az a **konfigurálása lépés** lap a **típus** legördülő menüben válassza a lépés neve megegyezik a profilt, konfigurálja a típust. Ezután kattintson a **Next** (Tovább) gombra.  
        ![Futtatási profil 2 összekötő](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
        3. Az a **összekötő-konfiguráció** lap a **partíció** legördülő menüben válassza ki a tartományt, amelyikhez a tartomány szűrő való felvételét nevét.  
        ![Futtatási profil 3 összekötő](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
        4. Bezárja a **kísérleti profilba konfigurálása** párbeszédpanel, kattintson a **Befejezés**.
    2. Minden egyes az öt profilok esetében a következő lépések az egyes **eltávolított** tartomány:
        1. Válassza ki a futtatási profil.
        2. Ha a **érték** , a **partíció** attribútum GUID, válassza ki a futtatási lépés, és kattintson a **lépés törlése**.  
        ![Futtatási profil 4 összekötő](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  
    3. Ellenőrizze a módosítást. Minden olyan tartományban, a szinkronizálni kívánt lépésben szereplő minden futtatási profilnál kell szerepelnie.
4. Bezárja a **Configure Run Profiles** párbeszédpanel, kattintson a **OK**.
5.  A konfigurálás befejezéséhez, futtatnia kell egy **teljes importálás** és egy **különbözeti szinkronizálási**. Továbbra is a szakasz olvasása [alkalmaz, és a módosítások ellenőrzéséhez](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Szervezeti egység-alapú szűrés
Az előnyben részesített módosítása OU-alapú szűrés módja a telepítővarázsló futtatása, és válassza [tartományok és szervezeti egységek szűrése](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). A varázsló automatizálja a ebben a témakörben ismertetett feladatokat.

Ha Ön a varázsló nem futtatható a telepítés bármilyen okból csak kövesse ezeket a lépéseket.

Szervezeti egység-alapú szűrés konfigurálásához tegye a következőket:

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálási szolgáltatás egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoport.
2. Start **szinkronizálási szolgáltatás** a a **Start** menü.
3. Válassza ki **összekötők**, majd a a **összekötők** listára, válassza ki az összekötő típusú **Active Directory tartományi szolgáltatások**. A **műveletek**, jelölje be **tulajdonságok**.  
   ![Összekötő tulajdonságai](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Kattintson a **címtárpartíciók konfigurálása**, válassza ki a tartományt, konfigurálása, és kattintson a kívánt **tárolók**.
5. Amikor a rendszer kéri, adja meg minden hitelesítő adat olvasási hozzáférést a helyszíni Active Directory. Nem kell lennie a felhasználót, hogy a rendszer előre feltöltve a párbeszédpanelen.
6. Az a **tárolók kijelölése** párbeszédpanel mezőben, törölje a jelet a nem kívánt szinkronizálása a felhőalapú címtárban, majd a szervezeti egységek **OK**.  
   ![Szervezeti egységek, a tárolók kijelölése párbeszédpanel](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
   * A **számítógépek** tároló ki kell választani a Windows 10-es számítógépeken sikeresen szinkronizálásának engedélyezése az Azure AD. Ha a tartományhoz csatlakoztatott számítógépek egyéb szervezeti egységek találhatók, ellenőrizze, azok van kiválasztva.
   * Ha több, megbízhatósági kapcsolattal rendelkező erdővel rendelkezik, használja a **ForeignSecurityPrincipals** tárolót. Ez a tároló lehetővé teszi az erdők közötti biztonsági csoporttagságok feloldását.
   * A **RegisteredDevices** Ha engedélyezte az eszközök visszaírásához funkció OU kell választani. Ha egy másik visszaírási szolgáltatás, például a csoportok visszaírásához győződjön meg arról, ezeket a helyeket van kiválasztva.
   * Válassza ki a bármely egyéb szervezeti egység, ahol a felhasználók, a iNetOrgPersons, a csoportokat, a névjegyeket és a számítógépek találhatók. A képen látható minden hozzá a ManagedObjects szervezeti egység található.
   * Használatakor a csoport-alapú szűrés, majd a szervezeti egységre, amelyben a csoport nem található kell foglalni.
   * Vegye figyelembe, hogy beállítható, hogy a szűrési konfiguráció befejezése után hozzáadott új szervezeti egységek szinkronizálva, vagy nincsenek szinkronizálva. Lásd a következő szakaszát.
7. Amikor elkészült, zárja be a **tulajdonságok** kattintva párbeszédpanel **OK**.
8. A konfigurálás befejezéséhez, futtatnia kell egy **teljes importálás** és egy **különbözeti szinkronizálási**. Továbbra is a szakasz olvasása [alkalmaz, és a módosítások ellenőrzéséhez](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Új szervezeti egységek szinkronizálása
Alapértelmezés szerint új szervezeti egységek szűrése konfigurálása után létrehozott szinkronizálódnak. Ez az állapot jelzi jelölőnégyzet be van jelölve. Néhány sub szervezeti is törölheti. Ahhoz, hogy ez a viselkedés, a jelölőnégyzetre kattintva fehér, a kék jel (az alapértelmezett állapotába) válik. Ezután törölje bármely sub-ou-k esetében, amelyek nem kívánja szinkronizálni.

Ha minden sub-szervezeti egység szinkronizálva van, a mező esetén fehér, a kék jel.  
![Minden kiválasztott be a szervezeti egység](./media/active-directory-aadconnectsync-configure-filtering/ousyncnewall.png)

Ha néhány sub szervezeti már nincs bejelölve, akkor a lista Szürke fehér jelölést.  
![Néhány sub-szervezeti egységek nincs bekapcsolva a szervezeti egység](./media/active-directory-aadconnectsync-configure-filtering/ousyncnew.png)

Ezzel a konfigurációval ManagedObjects alapján létrehozott új szervezeti egység szinkronizálva.

Az Azure AD Connect telepítővarázsló mindig létrehozza ezt a konfigurációt.

### <a name="dont-synchronize-new-ous"></a>Új szervezeti egységek nincs szinkronizálás
Beállíthatja, hogy a szinkronizálási motor nem szinkronizálja az új szervezeti egységek, a szűrési konfiguráció befejezése után. Ez az állapot jelzi a felhasználói felületen, a mező nincs bejelölve a szürkén jelenik meg. Ahhoz, hogy ez a viselkedés, jelölje be a nem változik fehér, a jelölőnégyzet nincs bejelölve. Válassza ki a szinkronizálni kívánt sub-szervezeti.

![A legfelső szintű nincs bekapcsolva a szervezeti egység](./media/active-directory-aadconnectsync-configure-filtering/oudonotsyncnew.png)

Ezzel a konfigurációval ManagedObjects alapján létrehozott új szervezeti egység nincs szinkronizálva.

## <a name="attribute-based-filtering"></a>Attribútum-alapú szűrés
Győződjön meg arról, hogy használja a 2015. November ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) vagy újabb építése működéséhez lépéseket.

Attribútum-alapú szűrés módja a leginkább rugalmas objektumok szűrése. Használhatja a hatványa [deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md) szinte minden szempontja, hogy ha egy objektum szinkronizálva az Azure AD szabályozására.

Alkalmazhat [bejövő](#inbound-filtering) szűrni az Active Directoryból a metaverzumba, és [kimenő](#outbound-filtering) az Azure AD a metaverzumba szűrésére. Azt javasoljuk, hogy bejövő szűrés, mert ez a legegyszerűbb fenntartásához alkalmazni. Csak akkor ajánlott a kimenő szűrést, ha ez szükséges akkor egynél több erdőből származó objektumok csatlakozni, mielőtt értékelésére akkor kerül sor.

### <a name="inbound-filtering"></a>Bejövő szűrése
Bejövő szűrése az alapértelmezett konfigurációt használja, ahol is az Azure AD-objektumok a metaverzum-attribútum nincs beállítva egy értékre szinkronizálandó cloudFiltered kell rendelkeznie. Ha ez az attribútum értéke **igaz**, akkor az objektum nincs szinkronizálva. Nem állítható be **hamis**, úgy lett kialakítva. Győződjön meg arról, más szabályok is képes közre érték, ez az attribútum csak várt értékük **igaz** vagy **NULL** (távol).

A bejövő szűrés, használja a power **hatókör** meghatározni, mely objektumok szinkronizálásához, vagy nem szinkronizálja. Ez azért, ahol meg módosítani lehet a saját szervezet igényeinek megfelelően. A hatókör-modul egy **csoport** és egy **záradék** annak megállapításához, amikor a szinkronizálási szabály hatóköre. A csoport egy vagy több záradékot tartalmaz. Nincs logikai "és" több záradékot, és több csoportok közötti logikai "vagy" között.

Ossza meg velünk Példaként tekintse meg:  
![Hatókör](./media/active-directory-aadconnectsync-configure-filtering/scope.png)  
Ez értelmezendő **(részleg = informatikai) vagy (részleg = értékesítés és c = US)**.

A következő mintákat és lépéseket a user objektum használja példaként, de ezzel minden objektum esetében.

A következő mintában a sorrend értékét 50 kezdődik. Ez a szám nem lehet, de 100-nál kisebbnek kell lennie.

#### <a name="negative-filtering-do-not-sync-these"></a>Negatív szűrése: "szinkronizálja ezeket"
A következő példában a kiszűrhetők (nem szinkronizálása) minden felhasználó ahol **extensionAttribute15** értéke **NoSync**.

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálási szolgáltatás egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoport.
2. Start **szinkronizálási szabályok szerkesztő** a a **Start** menü.
3. Győződjön meg arról, hogy **bejövő** van kiválasztva, és kattintson a **új szabály hozzáadása**.
4. Adjon a szabálynak egy nevet, például a "*a az AD-felhasználó DoNotSyncFilter*". Jelölje ki a megfelelő erdőt, jelölje be **felhasználói** , a **CS objektumtípus**, és válassza ki **személy** , a **MV-objektum típusa**. A **kapcsolattípus**, jelölje be **csatlakozás**. A **sorrend**, írjon be egy értéket, amely jelenleg nem használja egy másik szinkronizálási szabály (például 50), és kattintson a **következő**.  
   ![Bejövő 1 leírása](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. A **Scoping szűrő**, kattintson a **csoport hozzáadása**, és kattintson a **záradék hozzáadása**. A **attribútum**, jelölje be **ExtensionAttribute15**. Győződjön meg arról, hogy **operátor** értéke **egyenlő**, és írja be az értéket **NoSync** a a **érték** mezőbe. Kattintson a **Tovább** gombra.  
   ![Bejövő 2 hatókör](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Hagyja a **csatlakozás** szabályok üres, és kattintson a **következő**.
7. Kattintson a **hozzáadása átalakítása**, jelölje be a **FlowType** , **állandó**, és válassza ki **cloudFiltered** , a **Target attribútummal**. Az a **forrás** szövegmezőben **igaz**. Kattintson a **Hozzáadás** a szabály mentéséhez.  
   ![Bejövő 3 átalakítása](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. A konfigurálás befejezéséhez, futtatnia kell egy **Full sync**. Továbbra is a szakasz olvasása [alkalmaz, és a módosítások ellenőrzéséhez](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Pozitív szűrése: "csak szinkronizálás ezek"
Pozitív szűrési kifejezése több kihívást jelenthet, mert is figyelembe kell vennie, amelyek nem kell szinkronizálni, például konferenciaterem nyilvánvaló objektumok. Az alapértelmezett szűrő a out-of-box szabály felülbírálása is fog **a az AD - felhasználó csatlakozás**. Az egyéni szűrő létrehozásakor győződjön meg arról, hogy kritikus rendszerobjektumok, a replikációs ütközés objektumok, különleges postaládák és a szolgáltatásfiókok nem tartalmazza az Azure AD Connect.

A pozitív szűrési beállításnál két szinkronizálási szabály. Kell egy szabály (vagy több) a megfelelő szinkronizálandó objektumok körét. Második általános szinkronizálási szabály, amely a összes objektum, amely olyan objektum, amely szinkronizálnia kell még nem még meg is kell.

A következő példában csak szinkronizálás felhasználói objektumok, ahol a részleg attribútum értéke a **értékesítési**.

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálási szolgáltatás egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoport.
2. Start **szinkronizálási szabályok szerkesztő** a a **Start** menü.
3. Győződjön meg arról, hogy **bejövő** van kiválasztva, és kattintson a **új szabály hozzáadása**.
4. Adjon a szabálynak egy nevet, például a "*a az AD-felhasználó értékesítési szinkronizálása*". Jelölje ki a megfelelő erdőt, jelölje be **felhasználói** , a **CS objektumtípus**, és válassza ki **személy** , a **MV-objektum típusa**. A **kapcsolattípus**, jelölje be **csatlakozás**. A **sorrend**, írjon be egy értéket, amelyet egy másik szinkronizálási szabály (például 51) jelenleg nem használ, és kattintson a **következő**.  
   ![Bejövő 4 leírása](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. A **Scoping szűrő**, kattintson a **csoport hozzáadása**, és kattintson a **záradék hozzáadása**. A **attribútum**, jelölje be **részleg**. Győződjön meg arról, hogy operátor beállításai **egyenlő**, és írja be az értéket **értékesítési** a a **érték** mezőbe. Kattintson a **Tovább** gombra.  
   ![Bejövő 5 hatókör](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Hagyja a **csatlakozás** szabályok üres, és kattintson a **következő**.
7. Kattintson a **hozzáadása átalakítása**, jelölje be **állandó** , a **FlowType**, és válassza ki a **cloudFiltered** , a **Target attribútummal**. Az a **forrás** mezőbe írja be **hamis**. Kattintson a **Hozzáadás** a szabály mentéséhez.  
   ![Bejövő 6 átalakítása](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
   Ez az egy különleges esetben, ha explicit módon beállította cloudFiltered **hamis**.
8. Most már tudunk a catch – minden szinkronizálási szabály létrehozásához. Adjon a szabálynak egy nevet, például a "*a az AD-felhasználó Catch – minden szűrő*". Jelölje ki a megfelelő erdőt, jelölje be **felhasználói** , a **CS objektumtípus**, és válassza ki **személy** , a **MV-objektum típusa**. A **kapcsolattípus**, jelölje be **csatlakozás**. A **sorrend**, írjon be egy értéket, amelyet egy másik szinkronizálási szabály (például 99) jelenleg nem használ. Kijelölt magasabb (képest alacsonyabb fontossági sorrenddel) az előző szinkronizálási szabály sorrend értéke. De korábban is hagyta valamennyi hely, hogy további szűrési szinkronizálási szabályok később veheti fel, ha el szeretné indítani a további szervezeti egységek szinkronizálása. Kattintson a **Tovább** gombra.  
   ![Bejövő 7 leírása](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Hagyja **Scoping szűrő** üres, és kattintson a **következő**. Egy üres szűrőnév azt jelzi, hogy a szabály minden objektumokra alkalmazandó.
10. Hagyja a **csatlakozás** szabályok üres, és kattintson a **következő**.
11. Kattintson a **hozzáadása átalakítása**, jelölje be **állandó** , a **FlowType**, és válassza ki **cloudFiltered** , a **Target attribútummal**. Az a **forrás** mezőbe írja be **igaz**. Kattintson a **Hozzáadás** a szabály mentéséhez.  
    ![Bejövő 3 átalakítása](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. A konfigurálás befejezéséhez, futtatnia kell egy **Full sync**. Továbbra is a szakasz olvasása [alkalmaz, és a módosítások ellenőrzéséhez](#apply-and-verify-changes).

Ha kell, ahol több objektum szerepel a szinkronizálási első típusú további szabályok is létrehozhat.

### <a name="outbound-filtering"></a>Kimenő szűrése
Néhány esetben szükség a szűrés csak azt követően a objektumok van összekapcsolva a metaverzumban. Például előfordulhat, hogy legyen kell vizsgálni az erőforráserdőből a levél attribútum, és a userPrincipalName attribútum a fiók erdőből meghatározni, ha egy objektum szinkronizálnia kell. Ezekben az esetekben hoz létre a kimenő forgalomra vonatkozó szabály szűrés.

Ebben a példában a szűrést, hogy csak a felhasználók, amelyek rendelkeznek a levelezés és a userPrincipalName végződése módosítása @contoso.com szinkronizálja a rendszer:

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálási szolgáltatás egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoport.
2. Start **szinkronizálási szabályok szerkesztő** a a **Start** menü.
3. A **szabályok típusa**, kattintson a **kimenő**.
4. Attól függően, hogy használja csatlakozás verziója vagy található nevű szabályt **ki az aad-be – felhasználói csatlakozás** vagy **ki az AAD - felhasználó csatlakozás SOAInAD**, és kattintson a **szerkesztése**.
5. Az előugró ablakban fogadja a hívást **Igen** egy példányát a szabály létrehozásához.
6. A a **leírás** lapján módosítsa **sorrend** nem használt érték, például az 50.
7. Kattintson a **Scoping szűrő** a bal oldali navigációs, és kattintson a **Hozzáadás záradék**. A **attribútum**, jelölje be **mail**. A **operátor**, jelölje be **megadott módon VÉGZŐDŐ**. A **érték**, típus **@contoso.com**, és kattintson a **Hozzáadás záradék**. A **attribútum**, jelölje be **userPrincipalName**. A **operátor**, jelölje be **megadott módon VÉGZŐDŐ**. A **érték**, típus **@contoso.com**.
8. Kattintson a **Save** (Mentés) gombra.
9. A konfigurálás befejezéséhez, futtatnia kell egy **Full sync**. Továbbra is a szakasz olvasása [alkalmaz, és a módosítások ellenőrzéséhez](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Alkalmazza, és a módosítások ellenőrzéséhez
Miután a konfigurációs módosításokat hajtott végre, telepítenie kell őket az objektumok, amelyek már a rendszerben. Emellett előfordulhat, hogy az objektumok, amelyek jelenleg nem a Szinkronizáló vezérlő fel kell dolgozni (és a szinkronizálási motor rendszernek végig kell olvasnia a forrásrendszerben ismét ellenőrizze a tartalmat a).

Ha módosította a konfiguráció segítségével **tartomány** vagy **szervezeti egység** szűrés, majd végre kell hajtani egy **teljes importálás**, utána pedig **különbözeti szinkronizálás**.

Ha módosította a konfiguráció segítségével **attribútum** szűrés, majd végre kell hajtani egy **teljes szinkronizálás**.

Hajtsa végre az alábbi lépéseket:

1. Start **szinkronizálási szolgáltatás** a a **Start** menü.
2. Válassza ki **összekötők**. Az a **összekötők** listára, válassza ki az összekötőt, ha olyan konfigurációs módosítást korábban végzett. A **műveletek**, jelölje be **futtatása**.  
   ![Összekötő futtatása](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. A **futtatási profil**, válassza ki az előző szakaszban említett a műveletet. Ha két műveletek futtatására van szüksége, futtassa a második az elsőt befejeződése után. (A **állapot** oszlop **üresjáratban** a kijelölt összekötő.)

A szinkronizálás után az összes módosítás exportálható előkészített. Valójában a módosítása előtt az Azure ad-ben, ellenőrizze, hogy helyesen-e ezek a változások szeretné.

1. Nyisson meg egy parancsablakot, és navigáljon a `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Futtassa az `csexport "Name of Connector" %temp%\export.xml /f:x` parancsot.  
   Az összekötő neve szerepel a szinkronizálási szolgáltatás. A "contoso.com – AAD" hasonló névvel rendelkezik az Azure AD.
3. Futtassa az `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` parancsot.
4. Most már rendelkezik a % temp %, amely megfelel a Microsoft Excel export.csv nevű fájl. Ez a fájl exportálni, módosításokat tartalmaz.
5. A szükséges módosításokat az adatok vagy konfiguráció, és futtassa ezeket a lépéseket újra (importálás, szinkronizálás, és győződjön meg arról) addig, amíg exportálni kívánt módosításokkal nem várt.

Ha elkészült, exportálja a módosításokat az Azure AD.

1. Válassza ki **összekötők**. Az a **összekötők** listára, válassza ki az Azure AD-összekötőt. A **műveletek**, jelölje be **futtatása**.
2. A **futtatási profil**, jelölje be **exportálása**.
3. Ha a konfigurációs módosítások sok objektumok törlése, majd hibaüzenet jelenik meg az exportált Ha száma több, mint a beállított küszöbértéket (alapértelmezésben 500). Ha ezt a hibaüzenetet látja, akkor le kell tiltania ideiglenesen a "[véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)" funkció.

Most azt az idő az ütemező ismét engedélyeznie.

1. Start **Feladatütemező** a a **Start** menü.
2. Közvetlenül a **Feladatütemező könyvtár**, nevű feladat található **Azure AD Sync Scheduler**, kattintson a jobb gombbal, majd válassza **engedélyezése**.

## <a name="group-based-filtering"></a>Csoport-alapú szűrés
Beállíthatja, hogy az Azure AD Connect használatával telepítse először csoport alapú szűrés [egyéni telepítési](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups). Kísérleti központi telepítés célja, ha azt szeretné, hogy a szinkronizálandó objektumok csak egy kis készletét. Csoport-alapú szűrés letiltása esetén nem engedélyezhető újra. Rendelkezik *nem támogatott* biztonságicsoport-alapú szűrés egyéni konfiguráció használatához. Ez a szolgáltatás konfigurálása a telepítési varázsló használatával csak támogatott. A próbaüzem végrehajtását, majd a más szűrési lehetőségek valamelyikével ebben a témakörben. A csoport-alapú szűrés együtt OU-alapú szűrés használatakor a kapcsolnia, amelyben a csoportot és annak tagjait is szerepelnie kell.

Több AD-erdővel szinkronizálásakor beállíthatja egy másik csoportot minden egyes címtárösszekötőben megadásával csoport alapú szűrés. Ha kívánja szinkronizálni a felhasználó egy Active Directory erdőben, és ugyanahhoz a felhasználóhoz van egy vagy több megfelelő objektumokat más AD-erdőkkel, meg kell győződnie arról, hogy a user objektum és a megfelelő objektumok jelenleg belül Csoportalapú korlátozza hatókör. Példák:

* A felhasználó egy olyan erdőben, amely rendelkezik a megfelelő FSP (idegen rendszerbiztonsági tag) objektumot egy másik erdőben vannak. Mindkét objektum kell lennie belül biztonságicsoport-alapú szűrés hatókör. Ellenkező esetben a felhasználó nem fognak szinkronizálódni az Azure ad Szolgáltatásba.

* A felhasználó egy olyan erdőben, amely rendelkezik a megfelelő erőforrás fiókkal (például hivatkozott postafiókkal) egy másik erdőben vannak. Továbbá az erőforrás-fiókkal rendelkező felhasználó csatolni az Azure AD Connect konfigurálta. Mindkét objektum kell lennie belül biztonságicsoport-alapú szűrés hatókör. Ellenkező esetben a felhasználó nem fognak szinkronizálódni az Azure ad Szolgáltatásba.

* A felhasználó rendelkezik egy olyan erdőben, amely rendelkezik a megfelelő e-mail kapcsolattartási egy másik erdőben. További konfigurálta az Azure AD Connect hivatkozásra a felhasználó e-mail az ügyfél számára. Mindkét objektum kell lennie belül biztonságicsoport-alapú szűrés hatókör. Ellenkező esetben a felhasználó nem fognak szinkronizálódni az Azure ad Szolgáltatásba.


## <a name="next-steps"></a>További lépések
- További információ [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.
- További információ [a helyszíni identitások integrálása az Azure AD](active-directory-aadconnect.md).
