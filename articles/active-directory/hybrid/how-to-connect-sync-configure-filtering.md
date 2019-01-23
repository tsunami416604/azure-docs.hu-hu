---
title: 'Az Azure AD Connect szinkronizálása: A szűrés konfigurálása |} A Microsoft Docs'
description: Ismerteti az Azure AD Connect-szinkronizálás szűrőjének konfigurálása.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 886ac908d2e294f4627f95b35d93ea49a9e1607a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472330"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Az Azure AD Connect szinkronizálása: A szűrés konfigurálása
Szűrés segítségével szabályozhatja, mely objektumai jelenjenek meg az Azure Active Directory (Azure AD) a helyszíni címtárból. Az alapértelmezett konfiguráció minden objektumot a konfigurált erdőben lévő minden tartományban vesz igénybe. Ez általában az ajánlott konfiguráció. Felhasználók használják az Office 365 számítási feladatok, például az Exchange Online és Skype vállalati verzió, egy teljes globális címlista előnyös, így e-mailt, és mindenki hívja. Az alapértelmezett konfigurációnál, ugyanazt a felhasználói élményt, hogy azok egy a helyszíni Exchange-hez vagy a Lync végrehajtásának kellene.

Bizonyos esetekben azonban Ön szükséges néhány módosítást az alapértelmezett konfigurációval. Néhány példa:

* Tervezi használni a [több Azure AD directory topológiáját](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Szabályozhatja, hogy egy adott szinkronizálandó objektumok körét egy szűrőt kell az Azure AD-címtárban.
* Egy próbaprogram futtatása az Azure vagy Office 365, és csak felhasználók egy részhalmazát szeretné az Azure ad-ben. A kis program, ez nem fontos a rendelkezik a funkció bemutatása egy teljes globális címlista.
* Hogy sok szolgáltatás és más megbízhatóságának fiókok, amelyet szeretne az Azure ad-ben.
* Megfelelőségi okokból minden felhasználói fiókok a helyszíni ne törölje. Csak letiltja őket. De az Azure ad-ben, célszerű csak akkor aktív fiókok meglétét.

Ez a cikk ismerteti a különböző szűrési módszerek konfigurálása.

> [!IMPORTANT]
> A Microsoft nem támogatja az Azure AD Connect szinkronizálásának módosítását vagy a hivatalos dokumentumokban szereplő műveleteken kívüli használat. Minden ilyen művelet azt eredményezheti, hogy az Azure AD Connect szinkronizálása inkonzisztens vagy nem támogatott állapotba kerül. A Microsoft ezért nem tud műszaki támogatást biztosítani az ilyen környezetekhez.

## <a name="basics-and-important-notes"></a>Alapjait és a fontos megjegyzések
Az Azure AD Connect szinkronizálása engedélyezheti a szűrés bármikor. Ha indítsa el a címtár-szinkronizálás alapértelmezett konfigurációja, majd a szűrés konfigurálása az objektumok, amelyek ki vannak szűrve van már nincs szinkronizálva az Azure AD. Miatt ez a változás az Azure ad-ben, amely korábban már szinkronizált, de majd szűrve lettek objektumok törlődnek, az Azure ad-ben.

Szűrés módosítása előtt győződjön meg arról, hogy Ön [letiltása az ütemezett feladat](#disable-the-scheduled-task) így véletlenül ne exportálja, az még nem ellenőrizte, hogy a megfelelő módosításokat.

Szűrés távolíthatja egy időben sok objektumot, mert érdemes győződjön meg arról, hogy az új szűrők helyesen-e módosítások exportálna az Azure AD megkezdése előtt. Miután végzett a konfigurációs lépések, javasoljuk, hogy kövesse a [ellenőrzési lépések](#apply-and-verify-changes) előtt exportálhatja, és hajtsa végre a módosításokat az Azure ad-hez.

Véletlenül iratkozott le, a szolgáltatás számos objektumok törlése elleni "[véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md)" alapértelmezés szerint be van kapcsolva. Törlése miatt (alapértelmezés szerint 500) szűrés több objektum esetén kell, hogy a törlések oldhatják fel az Azure ad-ben Ez a cikk lépéseit kövesse.

Ha egy build 2015 November előtti ([1.0.9125](reference-connect-version-history.md#1091250)), szűrő beállításait módosítja, és használja a Jelszókivonat-szinkronizálás, akkor kell elindítani a teljes szinkronizálás az összes jelszó, a konfiguráció befejezését követően. Teljes jelszó-szinkronizálás aktiválása lépéseiért lásd: [teljes az összes jelszó-szinkronizálás aktiválása](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Ha a build 1.0.9125 vagy újabb, majd a normál **teljes szinkronizálás** művelet is kiszámítja e szinkronizálni, és ha ez a lépés további már nem szükséges.

Ha **felhasználói** objektumok véletlenül törölve lett az Azure ad-ben egy szűrési hiba miatt, akkor újra létrehozhatja a felhasználói objektumok az Azure ad-ben a szűrési beállítások eltávolítása. Ezután újra szinkronizálhatja a címtárakat. Ez a művelet a felhasználók Lomtárból való visszaállítása az Azure ad-ben. Azonban egyéb típusú objektumokat nem törlésének visszavonása. Például ha véletlenül töröl egy biztonsági csoportot, és az ACL-t használta azt egy erőforrást, a csoport és a hozzáférés-vezérlési listák nem állítható helyre.

Az Azure AD Connect csak törli az objektumot, amely rendelkezik a hatókörbe egyszer tekinthető. Ha egy másik szinkronizálási motor által létrehozott objektumok az Azure ad-ben, és ezek az objektumok nem szerepelnek a hatókör, hozzáadása a szűrés nem távolítja el azokat. Például ha megkezdése a teljes címtárban teljes másolata az Azure ad-ben létrehozott DirSync-kiszolgálóval, és a egy új Azure AD Connect szinkronizálási kiszolgáló telepít szűrés engedélyezve van az elejétől párhuzamosan, az Azure AD Connect nem távolítja el a felesleges objektumok a DirSync által létrehozott.

A szűrési konfigurációját is eltávolítsa telepítése vagy frissítése az Azure AD Connect újabb verzióra. Mindig ajánlott eljárás az, győződjön meg arról, hogy a nem véletlenül módosítása frissítés után egy újabb verzióra első szinkronizálási ciklusának futtatása előtt.

Ha egynél több erdő, majd telepítenie kell a szűrési konfigurációkat minden erdőben (feltéve, hogy ugyanazt a konfigurációt az összes kívánt) ebben a témakörben ismertetett.

### <a name="disable-the-scheduled-task"></a>Az ütemezett feladat letiltása
A beépített ütemezési, amely elindít egy szinkronizálási ciklust 30 percenként letiltásához kövesse az alábbi lépéseket:

1. Nyissa meg egy PowerShell parancssorba.
2. Futtatás `Set-ADSyncScheduler -SyncCycleEnabled $False` az ütemező letiltása.
3. Hajtsa végre a módosításokat szerepelnek ebben a cikkben.
4. Futtatás `Set-ADSyncScheduler -SyncCycleEnabled $True` az ütemező újra engedélyezni szeretné az.

**Ha egy Azure AD Connect build 1.1.105.0 előtt**  
Az ütemezett feladatot, amely elindít egy szinkronizálási ciklust három óránként letiltásához kövesse az alábbi lépéseket:

1. Indítsa el **Feladatütemező** származó a **Start** menü.
2. Közvetlenül a **Feladatütemező könyvtár**, keresse meg a feladat nevű **Azure AD Sync Schedulert**, kattintson a jobb gombbal, és válassza ki **letiltása**.  
   ![A Feladatütemező](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Mostantól konfigurációs módosítások és a szinkronizálási motor-ból, manuálisan futtassa a **Synchronization Service Managert** konzolon.

Miután végrehajtotta a szűrési módosításokat, ne felejtse el ismét elérhető nem lesz és **engedélyezése** újra a feladatot.

## <a name="filtering-options"></a>Szűrés beállításai
Az alábbi szűrési konfigurációtípusai alkalmazhat a címtár-Szinkronizáló eszköz:

* [**Csoport alapú**](#group-based-filtering): Szűrés egyetlen csoport alapján csak konfigurálható a kezdeti telepítés a telepítési varázsló használatával.
* [**Tartományalapú**](#domain-based-filtering): Ez a beállítás használatával kiválaszthatja, mely tartományokat az Azure AD szinkronizálása. Is hozzá és távolíthat el tartományokat abból a szinkronizálási motor konfiguráció, ha módosítja a helyszíni infrastruktúrát az Azure AD Connect-szinkronizálás telepítése után.
* [**Szervezeti egység (OU) – alapú**](#organizational-unitbased-filtering): Ez a beállítás használatával kiválaszthatja, amely szervezeti egységek szinkronizálása az Azure AD. Ez a beállítás akkor a kiválasztott szervezeti minden objektum esetében.
* [**Attribútumalapú**](#attribute-based-filtering): Ez a beállítás használatával objektumokat az objektumokat az attribútumértékek alapján szűrhetők. Különböző objektumtípusokra vonatkozó különböző szűrőket is rendelkezhet.

Egyszerre több szűrési beállítások is használhatja. Ha például segítségével szervezeti egység szerinti szűrés csak egy szervezeti egység objektumát tartalmazza. Egy időben használhatja attribútum szerinti szűrés objektumok további szűréséhez. Több szűrési módszerek, a szűrők használata logikai "és" a szűrők között.

## <a name="domain-based-filtering"></a>Tartományalapú szűrés
Ez a szakasz a lépéseket a tartományi szűrő konfigurálásához biztosít. Ha hozzáadja vagy eltávolítja az erdőben lévő tartományok, az Azure AD Connect telepítése után, akkor is a szűrési beállítások frissítése.

Az előnyben részesített módosítsa a tartományalapú szűrés módja a telepítési varázsló futtatásával és a módosítása [tartomány és szervezeti egységek szűrése](how-to-connect-install-custom.md#domain-and-ou-filtering). A telepítési varázsló automatikusan a ebben a témakörben ismertetett feladatokat.

Ha a telepítővarázsló futtatásához valamilyen okból nem tud csak kövesse ezeket a lépéseket.

Tartományalapú szűrés konfiguráció alábbi lépésekből áll:

1. [Válassza ki a tartományok](#select-domains-to-be-synchronized) , amely fel szeretne venni a szinkronizálást.
2. Minden hozzáadott és eltávolított tartomány, módosítsa a [futtatási profilokat](#update-run-profiles).
3. [Módosítások alkalmazása és ellenőrzése](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Válassza ki a szinkronizálandó tartományok
Állítsa a tartomány, hajtsa végre a következő lépéseket:

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálása egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoportot.
2. Indítsa el **szinkronizálási szolgáltatás** származó a **Start** menü.
3. Válassza ki **összekötők**, majd a a **összekötők** listájához, válassza ki az összekötő típusú **Active Directory Domain Services**. A **műveletek**válassza **tulajdonságok**.  
   ![Összekötő tulajdonságai](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kattintson a **könyvtárpartíciók konfigurálásának**.
5. Az a **címtárpartíciók kiválasztása** listában, válassza ki, törölje a tartományok, igény szerint. Győződjön meg arról, hogy csak a szinkronizálni kívánt partíciók vannak-e jelölve.  
   ![Partíciók](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Ha megváltoztatta a helyszíni Active Directory-infrastruktúrát és a hozzáadott vagy tartományok távolítva az erdő, majd kattintson a **frissítése** gomb lekérni frissített listáját. Amikor frissíti, akkor kéri a hitelesítő adatokat. Adja meg a hitelesítő adatokat, olvasási hozzáférés a Windows Server Active Directory. Nem kell lennie a felhasználót, hogy az előre van töltve a párbeszédpanelen.  
   ![Frissítés szükséges](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Ha elkészült, zárja be a **tulajdonságok** kattintva párbeszédpanel **OK**. Az erdő tartományokat eltávolítja, ha egy előugró üzenet szerint, hogy tartományhoz el lett távolítva, és ez a konfiguráció lesznek törölve.
7. Továbbra is módosíthatja a [futtatási profilokat](#update-run-profiles).

### <a name="update-the-run-profiles"></a>A futtatási profil frissítése
Ha a tartomány-szűrőnek, is frissíteni szeretné a futtatási profilokat.

1. Az a **összekötők** listában, győződjön meg arról, hogy van-e kiválasztva az összekötőre, amelyet az előző lépésben módosította. A **műveletek**válassza **Configure Run Profiles**.  
   ![Összekötő futtatási profilokat 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Keresse meg, és azonosíthatja a következő profilokat:
    * Teljes importálás
    * Teljes szinkronizálás
    * Különbözeti importálás
    * Különbözeti szinkronizálás
    * Exportálás
3. Minden profilt, módosítsa a **hozzáadott** és **eltávolított** tartományok.
    1. Minden egyes az öt profilok esetében kövesse az alábbi lépéseket az egyes **hozzáadott** tartomány:
        1. Válassza ki a futtatási profil, és kattintson a **új lépés**.
        2. Az a **konfigurálása lépésben** lap a **típus** legördülő menüben válassza a lépés írja be a neve megegyezik a profil konfigurálásakor. Ezután kattintson a **Next** (Tovább) gombra.  
        ![Összekötő futtatási profilokat 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Az a **összekötő-konfiguráció** lap a **partíció** legördülő menüben válassza ki a tartományban, amelyhez hozzáadta a tartomány-szűrő nevét.  
        ![Összekötő futtatási profilokat 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Gombra kattintva zárja be a **futtató profilhoz konfigurálja** párbeszédpanelen kattintson a **Befejezés**.
    2. Minden egyes az öt profilok esetében kövesse az alábbi lépéseket az egyes **eltávolított** tartomány:
        1. Válassza ki a futtatási profil.
        2. Ha a **érték** , a **partíció** attribútumnak egy GUID Azonosítót, válassza ki a futtatási lépés, kattintson a **lépést Törlés**.  
        ![Összekötő futtatási profilokat 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Ellenőrizze a módosítást. Minden olyan tartományban, szinkronizálni szeretné az egyes futtatási profilok lépésben szerepelnie kell.
4. Gombra kattintva zárja be a **Configure Run Profiles** párbeszédpanelen kattintson a **OK**.
5.  A konfigurálás befejezéséhez, futtatnia kell egy **teljes importálást** és a egy **különbözeti szinkronizálási**. A szakasz olvassa [alkalmaz, és ellenőrizze a módosítások](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Szervezeti egység-alapú szűrés
Az előnyben részesített módosítsa a szervezeti egység szerinti szűrés módja a telepítési varázsló futtatásával és a módosítása [tartomány és szervezeti egységek szűrése](how-to-connect-install-custom.md#domain-and-ou-filtering). A telepítési varázsló automatikusan a ebben a témakörben ismertetett feladatokat.

Ha a telepítővarázsló futtatásához valamilyen okból nem tud csak kövesse ezeket a lépéseket.

Szervezeti egység-alapú szűrés konfigurálásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálása egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoportot.
2. Indítsa el **szinkronizálási szolgáltatás** származó a **Start** menü.
3. Válassza ki **összekötők**, majd a a **összekötők** listájához, válassza ki az összekötő típusú **Active Directory Domain Services**. A **műveletek**válassza **tulajdonságok**.  
   ![Összekötő tulajdonságai](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kattintson a **könyvtárpartíciók konfigurálása**, válassza ki a tartományt, konfigurálása, és kattintson a kívánt **tárolók**.
5. Amikor kéri, adja meg a hitelesítő adatokat, olvasási hozzáférés a helyszíni Active Directory. Nem kell lennie a felhasználót, hogy az előre van töltve a párbeszédpanelen.
6. Az a **tárolók kijelölése** párbeszédpanel mező mellett a szervezeti egységek, amelyet szeretne a címtárak szinkronizálása, majd törölje **OK**.  
   ![Szervezeti egységek, a tárolók kijelölése párbeszédpanel](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * A **számítógépek** a Windows 10-számítógépeket sikerült szinkronizálni az Azure AD-tároló van kiválasztva. A tartományhoz csatlakoztatott számítógépeken található egyéb szervezeti egységek, hogy azok ki van jelölve.
   * Ha több, megbízhatósági kapcsolattal rendelkező erdővel rendelkezik, használja a **ForeignSecurityPrincipals** tárolót. Ez a tároló lehetővé teszi az erdők közötti biztonsági csoporttagságok feloldását.
   * A **RegisteredDevices** szervezeti egység van kiválasztva, ha az eszköz a jelszóvisszaíró szolgáltatás engedélyezte. Egy másik a jelszóvisszaíró szolgáltatás, ha például a csoportok visszaírásához, győződjön meg arról, ezek a helyek ki van jelölve.
   * Jelölje ki bármely más szervezeti egység, ahol a felhasználók, iNetOrgPersons, csoportok, névjegyek és számítógépek találhatók. A képen látható az összes szervezeti egységek a ManagedObjects szervezeti egységben található.
   * Ha Csoportalapú szűrés használja, majd a szervezeti egységre, amelyben a csoport nem található kell foglalni.
   * Vegye figyelembe, hogy beállítható, hogy a szűrési konfiguráció befejezését követően hozzáadott új szervezeti egységekhez vannak szinkronizálva, vagy nincs szinkronizálva. A részleteket a következő szakaszban talál.
7. Ha elkészült, zárja be a **tulajdonságok** kattintva párbeszédpanel **OK**.
8. A konfigurálás befejezéséhez, futtatnia kell egy **teljes importálást** és a egy **különbözeti szinkronizálási**. A szakasz olvassa [alkalmaz, és ellenőrizze a módosítások](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Új szervezeti egységek szinkronizálása
Szűrés konfigurálása után létrehozott új szervezeti egységeket a rendszer alapértelmezés szerint szinkronizálja. Ez az állapot jelzi jelölőnégyzet be van jelölve. Egyes sub-szervezeti egységek is is törli. Ezt a viselkedést lekéréséhez kattintson a mezőre, nem változik fehér, a kék jel (az alapértelmezett állapotba). Ezután törölje minden sub-szervezeti egységek, amelyet szeretne szinkronizálni.

Ha minden sub-szervezeti egység szinkronizálva van, a box fehér-kék pipa.  
![Az összes bepipált jelölőnégyzetek szervezeti Egysége](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Ha egyes sub-szervezeti egységek lett nincs bejelölve, akkor a box egy fehér pipa jelre a szürke.  
![Egyes sub-szervezeti egységek nincs kiválasztva szervezeti Egysége](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Ezzel a konfigurációval ManagedObjects alatt létrehozott új szervezeti egység szinkronizálva van.

Az Azure AD Connect varázsló mindig létrehozza ezt a konfigurációt.

### <a name="dont-synchronize-new-ous"></a>Az új szervezeti egységeket ne legyen szinkronizálás
A szinkronizálási motor nem szinkronizálni az új szervezeti egységeket, a szűrési konfiguráció befejezése után is beállíthatja. Ebben az állapotban a felhasználói felületen, a jelölőnégyzet nincs bejelölve szürkén jelennek meg a mező jelzi. Ezt a viselkedést lekéréséhez kattintson a mezőre, nem változik fehér, a jelölőnégyzet nincs bejelölve. Ezután válassza ki a szinkronizálni kívánt sub-szervezeti.

![Nincs kiválasztva a gyökérszintű szervezeti Egysége](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Ezzel a konfigurációval ManagedObjects alatt létrehozott új szervezeti egység nincs szinkronizálva.

## <a name="attribute-based-filtering"></a>Attribútum szerinti szűrés
Győződjön meg arról, hogy használ-e az 2015 November ([1.0.9125](reference-connect-version-history.md#1091250)), vagy később létrehozhatja a működéséhez ezeket a lépéseket.

> [!IMPORTANT]
>A Microsoft azt javasolja, nem módosíthatja az alapértelmezett szabályok által létrehozott **az Azure AD Connect**. Ha azt szeretné, a szabály módosításához, klónozza, és letiltja az eredeti szabályt. Ne módosítsa a klónozott szabály. Vegye figyelembe, hogy ezzel a módszerrel (eredeti szabály letiltása) fog kihagyná bármely hibajavításokat és a szolgáltatásokat, hogy a szabály engedélyezve van.

Attribútum-alapú szűrés az objektumok szűrése a legrugalmasabb módszer. Használhatja a hatékonyságát [deklaratív kiépítés](concept-azure-ad-connect-sync-declarative-provisioning.md) szinte minden szempontból az objektum mikor van szinkronizálva az Azure AD szabályozásához.

Alkalmazhat [bejövő](#inbound-filtering) szűrni az Active Directoryból a metaverzumba és [kimenő](#outbound-filtering) az Azure AD a metaverzumba szűrésére. Azt javasoljuk, hogy bejövő szűrése, mivel az a legegyszerűbb fenntartása a alkalmazni. Csak akkor ajánlott kimenő szűrést, ha ez szükséges egynél több erdőből származó objektumok csatlakozni, mielőtt értékelésére akkor kerül sor.

### <a name="inbound-filtering"></a>Bejövő szűrése
Bejövő szűrése az alapértelmezett konfigurációt használja, ahol objektumokat az Azure ad-hez is rendelkeznie kell a metaverzum-attribútum nincs beállítva egy értékre szinkronizálását cloudFiltered. Ha ez az attribútum értéke **igaz**, akkor az objektum nincs szinkronizálva. Nem állítható be **hamis**, tervező által. Ahhoz, hogy más szabályokat is képes közreműködés a egy értéke, ez az attribútum csak kellene a értékeket **igaz** vagy **NULL** (távol).

A bejövő szűrés hatékonyságát használhatja **hatókör** meghatározni, mely objektumok szinkronizálásához, vagy nem szinkronizálja. Ez az, ahol, hajtsa végre a módosításokat a saját szervezet igényeinek megfelelően. A hatókör modul rendelkezik egy **csoport** és a egy **záradék** meghatározni, ha a szinkronizálási szabály hatókörében van. A csoport egy vagy több záradékot tartalmaz. Nincs logikai "és" között több záradékot, és a egy logikai "vagy" több csoport között.

Lássunk egy példát:  
![Hatókör](./media/how-to-connect-sync-configure-filtering/scope.png)  
Ez olvassa el **(részleg = informatikai) vagy (részleg = értékesítési és c = US)**.

Az alábbi minták és lépéseket a felhasználói objektum használja példaként, de ezzel minden objektum esetében.

Az alábbi minta a sorrend értéke 50 kezdődik. Ez a nem használt tetszőleges szám lehet, de 100-nál kisebbnek kell lennie.

#### <a name="negative-filtering-do-not-sync-these"></a>Negatív szűrés: "nem szinkronizálja ezeket"
A következő példában a kiszűr (nem a szinkronizálás) minden felhasználó ahol **extensionAttribute15** értéke **NoSync**.

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálása egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoportot.
2. Indítsa el **szinkronizálási Szabályszerkesztővel** származó a **Start** menü.
3. Győződjön meg arról, hogy **bejövő** van jelölve, és kattintson a **új szabály hozzáadása**.
4. Adjon a szabálynak egy nevet, például "*a az AD-ből – felhasználói DoNotSyncFilter*". Válassza ki a megfelelő erdő, jelölje be **felhasználói** , a **CS objektumtípus**, és válassza ki **személy** , a **MV-objektum típusa**. A **hivatkozás típusa**válassza **csatlakozzon**. A **elsőbbséget**, adjon meg egy értéket, amely jelenleg nem használja egy másik szinkronizálási szabály (például 50), és kattintson **tovább**.  
   ![Bejövő 1 leírása](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. A **Scoping szűrő**, kattintson a **csoport hozzáadása**, és kattintson a **záradék hozzáadása**. A **attribútum**válassza **ExtensionAttribute15**. Győződjön meg arról, hogy **operátor** értékre van állítva **egyenlő**, és írja be az értéket **NoSync típusú** a a **érték** mezőbe. Kattintson a **tovább**.  
   ![Bejövő 2 hatókör](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Hagyja a **csatlakozzon** szabályok üres, és kattintson a **tovább**.
7. Kattintson a **átalakítás hozzáadása**, jelölje be a **FlowType** , **állandó**, és válassza ki **cloudFiltered** , a **cél Attribútum**. Az a **forrás** szövegmezőben **igaz**. Kattintson a **Hozzáadás** a szabály mentéséhez.  
   ![Bejövő 3 átalakítása](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. A konfigurálás befejezéséhez, futtatnia kell egy **teljes szinkronizálás**. A szakasz olvassa [alkalmaz, és ellenőrizze a módosítások](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Pozitív szűrés: "csak szinkronizálja ezeket"
Pozitív szűrési kifejezése több kihívást jelenthet, mivel is figyelembe kell vennie az objektumok, amelyek nem kell szinkronizálni, például a konferenciahívások nyilvánvaló. Bírálja felül az alapértelmezett szűrő a out-of-box szabályban is kívánja **az AD - felhasználó csatlakozzon-ből**. Az egyéni szűrő létrehozásakor ügyeljen arra, hogy kritikus fontosságú rendszerobjektumok, replikációs ütközés objektumok, speciális postaládák és a szolgáltatásfiókok nem tartalmazzák az Azure AD Connect.

A pozitív szűrési lehetőség két szinkronizálási szabály szükséges. Egy szabály (vagy több) kell a megfelelő hatókörben objektumok szinkronizálásához. Emellett egy második kevésbé szinkronizálási szabályt, amely kiszűri a még nem még során megállapítottuk, hogy a rendszer szinkronizálja az objektum összes objektumot.

A következő példában csak szinkronizálás felhasználói objektumok, ahol a részleg attribútum értéke a **értékesítési**.

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálása egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoportot.
2. Indítsa el **szinkronizálási Szabályszerkesztővel** származó a **Start** menü.
3. Győződjön meg arról, hogy **bejövő** van jelölve, és kattintson a **új szabály hozzáadása**.
4. Adjon a szabálynak egy nevet, például "*a az AD-ből – felhasználói értékesítési szinkronizálása*". Válassza ki a megfelelő erdő, jelölje be **felhasználói** , a **CS objektumtípus**, és válassza ki **személy** , a **MV-objektum típusa**. A **hivatkozás típusa**válassza **csatlakozzon**. A **elsőbbséget**, adjon meg egy értéket, amely jelenleg nem használja egy másik szinkronizálási szabály (például 51), és kattintson **tovább**.  
   ![Bejövő 4 leírása](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. A **Scoping szűrő**, kattintson a **csoport hozzáadása**, és kattintson a **záradék hozzáadása**. A **attribútum**válassza **részleg**. Győződjön meg arról, hogy az operátor értékre van állítva, **egyenlő**, és írja be az értéket **értékesítési** a a **érték** mezőbe. Kattintson a **tovább**.  
   ![Bejövő 5 hatókör](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Hagyja a **csatlakozzon** szabályok üres, és kattintson a **tovább**.
7. Kattintson a **átalakítás hozzáadása**, jelölje be **állandó** , a **FlowType**, és válassza ki a **cloudFiltered** , a **cél Attribútum**. Az a **forrás** mezőbe írja be **hamis**. Kattintson a **Hozzáadás** a szabály mentéséhez.  
   ![Bejövő 6 átalakítása](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Ez az egy különleges esetben, ha explicit módon beállította cloudFiltered **hamis**.
8. Most már rendelkezünk a kevésbé szinkronizálási szabály létrehozásához. Adjon a szabálynak egy nevet, például "*a az AD-ből – Catch – minden felhasználó szűrő*". Válassza ki a megfelelő erdő, jelölje be **felhasználói** , a **CS objektumtípus**, és válassza ki **személy** , a **MV-objektum típusa**. A **hivatkozás típusa**válassza **csatlakozzon**. A **elsőbbséget**, adjon meg egy értéket, amely jelenleg nem használja egy másik szinkronizálási szabály (például 99). Magasabb (sorrendben) mint az előző szinkronizálási szabály elsőbbséget értéket választotta. De is, hogy elhagyta néhány hely, így további szűrési szinkronizálási szabályok később is hozzáadhat, ha el szeretné indítani a további szervezeti egységek szinkronizálása. Kattintson a **tovább**.  
   ![Bejövő 7 leírása](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Hagyja **Scoping szűrő** üres, és kattintson a **tovább**. Egy üres szűrő azt jelzi, hogy a szabály az összes objektum alkalmazható.
10. Hagyja a **csatlakozzon** szabályok üres, és kattintson a **tovább**.
11. Kattintson a **átalakítás hozzáadása**, jelölje be **állandó** , a **FlowType**, és válassza ki **cloudFiltered** , a **cél Attribútum**. Az a **forrás** mezőbe írja be **igaz**. Kattintson a **Hozzáadás** a szabály mentéséhez.  
    ![Bejövő 3 átalakítása](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. A konfigurálás befejezéséhez, futtatnia kell egy **teljes szinkronizálás**. A szakasz olvassa [alkalmaz, és ellenőrizze a módosítások](#apply-and-verify-changes).

Ha kell, ahol további objektumok belefoglalása a szinkronizálást az első típusú további szabályok is létrehozhat.

### <a name="outbound-filtering"></a>Kimenő szűrése
Bizonyos esetekben szükség a szűrést, csak azt követően az objektumok van összekapcsolva a metaverzumban. Például lehet szükséges, és tekintse meg az erőforráserdőből a mail attribútum, és a userPrincipalName attribútum a fiók erdőből meghatározni, ha egy objektum szinkronizálásra kerüljenek-e. Ezekben az esetekben hoz létre a szűrést az kimenő szabályt.

Ebben a példában módosítja a szűrést, hogy csak a felhasználók, amelyek rendelkeznek a levelezés és a userPrincipalName végződése @contoso.com szinkronizált:

1. Jelentkezzen be a kiszolgálóra, amelyen fut az Azure AD Connect szinkronizálása egy olyan fiókkal, amely tagja a **ADSyncAdmins** biztonsági csoportot.
2. Indítsa el **szinkronizálási Szabályszerkesztővel** származó a **Start** menü.
3. A **szabályok típus**, kattintson a **kimenő**.
4. Connect használata verziójától függően akár található nevű szabályt **ki az aad-be – a felhasználó csatlakozzon** vagy **ki az AAD - felhasználók csatlakozásra SOAInAD**, és kattintson a **szerkesztése**.
5. Az előugró ablakban választ **Igen** másolatot készítsen a szabály.
6. Az a **leírás** lapon, majd **elsőbbséget** fel nem használt érték, például 50.
7. Kattintson a **Scoping szűrő** a bal oldali navigációs, és kattintson a **Hozzáadás záradék**. A **attribútum**válassza **mail**. A **operátor**válassza **ENDSWITH**. A **érték**, típus **@contoso.com**, és kattintson a **Hozzáadás záradék**. A **attribútum**válassza **userPrincipalName**. A **operátor**válassza **ENDSWITH**. A **érték**, típus **@contoso.com**.
8. Kattintson a **Save** (Mentés) gombra.
9. A konfigurálás befejezéséhez, futtatnia kell egy **teljes szinkronizálás**. A szakasz olvassa [alkalmaz, és ellenőrizze a módosítások](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Módosítások alkalmazása és ellenőrzése
Miután végzett a konfiguráció módosításait, telepítenie kell őket az objektumok, amelyek már megvannak a rendszerben. Is lehet, hogy az objektumok, amelyek jelenleg a szinkronizálási motor nem kell feldolgozni (és a szinkronizálási motor van szüksége, olvassa el a forrásrendszerben tartalmára megerősítése érdekében).

Ha módosította a konfigurációt a **tartomány** vagy **szervezeti egység** szűréshez, akkor kell tennie egy **teljes importálást**, utána pedig **különbözeti szinkronizálási**.

Ha módosította a konfigurációt a **attribútum** szűréshez, akkor kell tennie egy **teljes szinkronizálás**.

Kövesse az alábbi lépéseket:

1. Indítsa el **szinkronizálási szolgáltatás** származó a **Start** menü.
2. Válassza ki **összekötők**. Az a **összekötők** listájához, válassza ki az összekötőt, ha olyan konfigurációs módosítást korábban végzett. A **műveletek**válassza **futtatása**.  
   ![Összekötő futtatása](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. A **futtatási profilokat**, válassza ki az előző szakaszban említett a műveletet. Ha két műveletek futtatására van szüksége, futtassa az elsőt befejezése után a második. (A **állapot** oszlop **tétlen** a kijelölt összekötő.)

A szinkronizálás után az összes módosítást exportálható elő van készítve. Valójában a módosítása előtt az Azure ad-ben, ellenőrizze, hogy helyesek-e ezeket a módosításokat szeretne.

1. Indítson el egy parancssort, és váltson `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Futtassa az `csexport "Name of Connector" %temp%\export.xml /f:x` parancsot.  
   Az összekötő neve szerepel a szinkronizálási szolgáltatás. A "contoso.com – AAD" hasonló névvel rendelkezik az Azure ad-hez.
3. Futtassa az `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` parancsot.
4. Most már rendelkezik egy fájlt a % temp % nevű export.csv, amely megfelel a Microsoft Excelben. Ez a fájl tartalmazza a módosításokat, amelyek az exportálásra váró adatokat.
5. Az adatok vagy a konfiguráció végezze el a szükséges módosításokat, és futtassa ezeket a lépéseket újra (importálás, Synchronize, és győződjön meg arról) addig, amíg a változtatásokat, hogy exportálásra váró adatokat várt.

Ha már elégedett, exportálja a módosításokat az Azure ad-hez.

1. Válassza ki **összekötők**. Az a **összekötők** listájához, válassza ki az Azure AD-összekötőt. A **műveletek**válassza **futtatása**.
2. A **futtatási profilokat**válassza **exportálása**.
3. Ha a konfigurációs módosítások sok objektumot törölni, majd hibaüzenet jelenik meg az exportálás, ha a szám meghaladja a konfigurált küszöbértéket (alapértelmezés szerint 500-as). Ha ezt a hibaüzenetet, akkor le kell tiltania ideiglenesen a "[véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md)" funkciót.

Most, az ütemező újra engedélyezni szeretné az ideje.

1. Indítsa el **Feladatütemező** származó a **Start** menü.
2. Közvetlenül a **Feladatütemező könyvtár**, keresse meg a feladat nevű **Azure AD Sync Schedulert**, kattintson a jobb gombbal, és válassza ki **engedélyezése**.

## <a name="group-based-filtering"></a>Csoportalapú szűrés
Konfigurálhatja úgy, hogy az Azure AD Connect használatával telepítse először Csoportalapú szűrés [egyéni telepítési](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Próbacélú telepítéshez célja, ahol azt szeretné, hogy csak egy kis készletét objektumok szinkronizálásának. Ha letiltja a Csoportalapú szűrés, nem engedélyezhető újra. Rendelkezik *nem támogatott* egyéni konfiguráció Csoportalapú szűrés használatához. Csak ez a szolgáltatás konfigurálása a telepítési varázsló használatával támogatott. Amikor végzett a próbaüzem, majd használja az egyéb szűrési lehetőségek közül, ebben a témakörben. Együtt szervezeti egység szerinti szűrést a Csoportalapú szűrés használatakor szerepelnie kell a kapcsolnia, ahol a csoportot és annak tagjait találhatók.

Több AD-erdővel szinkronizálásakor a Csoportalapú szűrés egy másik csoportot minden egyes AD Connectoron megadásával is beállíthatja. Ha szeretné szinkronizálni a felhasználó egy Active Directory erdőben, és ugyanaz a felhasználó legalább egy további megfelelő azok az objektumok egyéb AD-erdőkkel, győződjön meg arról, hogy a felhasználói objektum és a megfelelő objektumok belül Csoportalapú szűrést végez hatókör. Példák:

* Egy felhasználó egy olyan erdőben, amely egy megfelelő FSP (idegen rendszerbiztonsági tag) objektumot egy másik erdőben van. Mindkét objektum kell lennie belül Csoportalapú szűrés hatókör. Ellenkező esetben a felhasználó nem szinkronizálja az Azure ad-hez.

* Egy felhasználó egy olyan erdőben, amely egy megfelelő erőforrásfiókot (pl. hivatkozott postafiókkal) egy másik erdőben van. További a felhasználót az erőforrás-fiók összekapcsolása az Azure AD Connect konfigurált. Mindkét objektum kell lennie belül Csoportalapú szűrés hatókör. Ellenkező esetben a felhasználó nem szinkronizálja az Azure ad-hez.

* A felhasználó rendelkezik egy olyan erdőben, amely rendelkezik a megfelelő levelezési kapcsolattartási egy másik erdőben. További az Azure AD Connect mutató hivatkozást a felhasználó e-mail az ügyfél már konfigurálta. Mindkét objektum kell lennie belül Csoportalapú szűrés hatókör. Ellenkező esetben a felhasználó nem szinkronizálja az Azure ad-hez.


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációja.
- Tudjon meg többet [a helyszíni identitások integrálása az Azure ad-ben](whatis-hybrid-identity.md).
