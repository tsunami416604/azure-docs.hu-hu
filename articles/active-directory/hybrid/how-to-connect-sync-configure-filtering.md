---
title: 'Azure AD Connect szinkronizálása: Szűrés konfigurálása | Microsoft dokumentumok'
description: A szűrés konfigurálása az Azure AD Connect szinkronizálásában.
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
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983699dfbfe3e8fa332da4810d1514a11029077f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261098"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Az Azure AD Connect szinkronizálása: a szűrés konfigurálása
Szűrés használatával szabályozhatja, hogy mely objektumok jelennek meg az Azure Active Directoryban (Azure AD) a helyszíni címtárból. Az alapértelmezett konfiguráció a konfigurált erdők összes tartományának összes objektumát veszi figyelembe. Általában ez az ajánlott konfiguráció. Az Office 365-ös számítási feladatokat használó felhasználók, például az Exchange Online és a Skype Vállalati verzió teljes globális címlistát használhatnak, így e-maileket küldhetnek, és mindenkit felhívhatnak. Az alapértelmezett konfigurációval ugyanolyan élményben lehet nek iktatva, mint az Exchange vagy a Lync helyszíni megvalósításával.

Bizonyos esetekben azonban módosítania kell az alapértelmezett konfigurációt. Néhány példa:

* Azt tervezi, hogy a [több Azure AD könyvtártopológia.](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant) Ezután egy szűrőt kell alkalmaznia annak szabályozására, hogy mely objektumok vannak szinkronizálva egy adott Azure AD-könyvtárral.
* Futtat egy próbaüzemet az Azure-ban vagy az Office 365-ben, és csak a felhasználók egy részhalmazát szeretné megkapni az Azure AD-ben. A kis próba, nem fontos, hogy egy teljes globális címlista, hogy bemutassa a funkcionalitást.
* Számos szolgáltatásfiókkal és más nem személyes fiókkal rendelkezik, amelyeket nem szeretne az Azure AD-ben.
* Megfelelőségi okokból nem törli a helyszíni felhasználói fiókokat. Csak letiltja őket. De az Azure AD-ben csak azt szeretné, hogy az aktív fiókok jelen legyenek.

Ez a cikk a különböző szűrési módszerek konfigurálását ismerteti.

> [!IMPORTANT]
> A Microsoft nem támogatja az Azure AD Connect szinkronizálásának módosítását vagy a hivatalos dokumentumokban szereplő műveleteken kívüli használat. Ezen műveletek bármelyike az Azure AD Connect szinkronizálásának inkonzisztens vagy nem támogatott állapotát eredményezheti. Ennek eredményeképpen a Microsoft nem tud technikai támogatást nyújtani az ilyen telepítésekhez.

## <a name="basics-and-important-notes"></a>Alapok és fontos megjegyzések
Az Azure AD Connect-szinkronizálásban bármikor engedélyezheti a szűrést. Ha a címtár-szinkronizálás alapértelmezett konfigurációjával kezdi, majd konfigurálja a szűrést, a szűrt objektumok már nem lesznek szinkronizálva az Azure AD-vel. A változás miatt az Azure AD-ben korábban szinkronizált, majd szűrt objektumok törlődnek az Azure AD-ben.

Mielőtt elkezdené módosítani a szűrést, győződjön meg arról, hogy [letiltja az ütemezett feladatot,](#disable-the-scheduled-task) hogy véletlenül ne exportálja azokat a módosításokat, amelyeket még nem ellenőrzött helyesnek.

Mivel a szűrés egyszerre több objektumot is eltávolíthat, győződjön meg arról, hogy az új szűrők helyesek, mielőtt elkezdené az Azure AD-módosításait. Miután elvégezte a konfigurációs lépéseket, javasoljuk, hogy kövesse az [ellenőrzési lépéseket,](#apply-and-verify-changes) mielőtt exportálná, és módosítsa az Azure AD-t.

Annak érdekében, hogy ne törjön ki véletlenül sok objektum, a["Véletlen törlés megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md)" funkció alapértelmezés szerint be van kapcsolva. Ha a szűrés miatt sok objektumot töröl (alapértelmezés szerint 500), a jelen cikkben leírt lépéseket kell végrehajtania, hogy a törlések átmenjenek az Azure AD-be.

Ha 2015 novembere előtt ([1.0.9125)](reference-connect-version-history.md#1091250)használ egy buildet, módosítsa a szűrőkonfigurációt, és használjon jelszókivonat-szinkronizálást, akkor a konfiguráció befejezése után el kell indítania az összes jelszó teljes szinkronizálását. A jelszó teljes szinkronizálásának aktiválásával kapcsolatos lépéseket az [Összes jelszó teljes szinkronizálásának aktiválása című témakörben található.](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords) Ha a build 1.0.9125 vagy újabb, majd a rendszeres **teljes szinkronizálási** művelet is kiszámítja, hogy a jelszavakat kell szinkronizálni, és ha ez a további lépés már nincs szükség.

Ha **a felhasználói** objektumok véletlenül törölték az Azure AD-ben egy szűrési hiba miatt, újra létrehozhatja a felhasználói objektumokat az Azure AD-ben a szűrési konfigurációk eltávolításával. Ezután újra szinkronizálhatja a könyvtárakat. Ez a művelet visszaállítja a felhasználókat az Azure AD lomtárából. Más objektumtípusok azonban nem törölhetők. Ha például véletlenül töröl egy biztonsági csoportot, és azt egy erőforrás acl-hez használták, a csoport és annak ACL-je nem lehet visszafordítani.

Az Azure AD Connect csak azokat az objektumokat törli, amelyeket egyszer hatókörnek tekintett. Ha vannak olyan objektumok az Azure AD-ben, amelyeket egy másik szinkronizálási motor, és ezek az objektumok nincsenek hatókörben, szűrés hozzáadása nem távolítja el őket. Ha például egy DirSync-kiszolgálóval kezdi, amely létrehozta a teljes könyvtár teljes másolatát az Azure AD-ben, és egy új Azure AD Connect szinkronizálási kiszolgálót telepít párhuzamosan, ha a szűrés kezdettől fogva engedélyezve van, az Azure AD Connect nem távolítja el a további objektumokat amelyeket a DirSync hozott létre.

A szűrési konfiguráció megmarad, amikor telepíti vagy frissíti az Azure AD Connect újabb verzióját. Mindig ajánlott ellenőrizni, hogy a konfiguráció nem változott-e meg véletlenül egy újabb verzióra való frissítés után az első szinkronizálási ciklus futtatása előtt.

Ha egynél több erdővel rendelkezik, akkor a témakörben ismertetett szűrési konfigurációkat minden erdőre alkalmaznia kell (feltéve, hogy mindegyikhez ugyanazt a konfigurációt szeretné igényelni).

### <a name="disable-the-scheduled-task"></a>Az ütemezett feladat letiltása
A szinkronizálási ciklust 30 percenként aktiváló beépített ütemező letiltásához hajtsa végre az alábbi lépéseket:

1. Lépjen a PowerShell-parancssorba.
2. Futtassa `Set-ADSyncScheduler -SyncCycleEnabled $False` az ütemező letiltásához.
3. A cikkben dokumentált módosítások végrehajtása.
4. Futtassa `Set-ADSyncScheduler -SyncCycleEnabled $True` az ütemező ismételt engedélyezéséhez.

**Ha az 1.1.105.0 előtt azure AD Connect buildet használ**  
A szinkronizálási ciklust háromóránként aktiváló ütemezett feladat letiltásához hajtsa végre az alábbi lépéseket:

1. **A Feladatütemező indítása** a **Start** menüből.
2. Közvetlenül a **Feladatütemező könyvtár alatt**keresse meg az **Azure AD Sync Scheduler**nevű feladatot, kattintson a jobb gombbal, és válassza **a Letiltás parancsot.**  
   ![Feladatütemező](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Most már módosíthatja a konfigurációt, és manuálisan futtathatja a szinkronizálási motort a **Szinkronizálási szolgáltatáskezelő** konzolról.

Miután befejezte az összes szűrési módosítást, ne felejtsen el visszatérni, és **engedélyezze** újra a feladatot.

## <a name="filtering-options"></a>Szűrési beállítások
A következő szűrési konfigurációtípusokat alkalmazhatja a címtár-szinkronizálási eszközre:

* [**Csoportalapú**](#group-based-filtering): Az egyetlen csoporton alapuló szűrés csak a telepítővarázsló val konfigurálható a telepítés megkezdésekor.
* [**Tartományalapú:**](#domain-based-filtering)Ezzel a beállítással kiválaszthatja, hogy mely tartományok szinkronizálják az Azure AD-vel. Az Azure AD Connect-szinkronizálás telepítése után tartományokat is hozzáadhat és eltávolíthat a szinkronizálási motor konfigurációjából, amikor módosítja a helyszíni infrastruktúrát.
* [**Szervezeti egység (OU)-alapú:**](#organizational-unitbased-filtering)Ezzel a beállítással kiválaszthatja, hogy melyik szervezeti egységek szinkronizálja az Azure AD.Organization unit (OU)-based: By this option, you can select which OUs synchronize to Azure AD. Ez a beállítás a kijelölt ous-ok összes objektumtípusára van.
* [**Attribútumalapú**](#attribute-based-filtering): Ezzel a beállítással szűrheti az objektumok attribútumértékein alapuló objektumokat. Különböző objektumtípusokhoz különböző szűrőket is használhat.

Egyszerre több szűrési beállítást is használhat. A szervezeti egység alapú szűrés segítségével például csak egy szervezeti egységben lévő objektumokat vehet fel. Ugyanakkor attribútumalapú szűréssel tovább szűrheti az objektumokat. Ha több szűrési módszert használ, a szűrők logikai "ÉS" (AND) kifejezést használnak a szűrők között.

## <a name="domain-based-filtering"></a>Tartományalapú szűrés
Ez a szakasz a tartományszűrő konfigurálásának lépéseit tartalmazza. Ha az Azure AD Connect telepítése után adott hozzá vagy távolított el tartományokat az erdőben, akkor a szűrési konfigurációt is frissítenie kell.

A tartományalapú szűrés módosításának előnyben részesített módja a telepítővarázsló futtatása, valamint a [tartomány- és szervezetiegység-szűrés](how-to-connect-install-custom.md#domain-and-ou-filtering)módosítása. A telepítővarázsló automatizálja a témakörben dokumentált összes feladatot.

Csak akkor kövesse ezeket a lépéseket, ha valamilyen okból nem tudja futtatni a telepítővarázslót.

A tartományalapú szűrési konfiguráció a következő lépésekből áll:

1. Jelölje ki a szinkronizálásba felvenni kívánt tartományokat.
2. Minden hozzáadott és eltávolított tartományhoz módosítsa a futtatási profilokat.
3. [Alkalmazza és ellenőrizze a módosításokat](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>A szinkronizálandó tartományok kijelölése
A szinkronizálandó tartományokat kétféleképpen választhatja ki:
    - A szinkronizálási szolgáltatás használata
    - Az Azure AD Connect varázsló használata.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>A szinkronizálási szolgáltatással szinkronizálandó tartományok kijelölése
A tartományszűrő beállításához tegye a következőket:

1. Jelentkezzen be az Azure AD Connect-szinkronizálást futtató kiszolgálóra az **ADSyncAdmins** biztonsági csoport tagjaként használt fiókhasználatával.
2. Indítsa el **a szinkronizálási szolgáltatást** a **Start** menüből.
3. Válassza **az Összekötők**lehetőséget, majd az **Összekötők** listában válassza az Active Directory tartományi szolgáltatások típusú **összekötőt.** A **Műveletek csoportban**válassza a **Tulajdonságok lehetőséget.**  
   ![Összekötő tulajdonságai](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kattintson **a Címtárpartíciók konfigurálása gombra.**
5. A **Címtárpartíciók kiválasztása** listában szükség szerint jelölje ki a tartományokat, és törölje belőle a jelet. Ellenőrizze, hogy csak a szinkronizálni kívánt partíciók vannak-e kijelölve.  
   ![Partíciók](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Ha módosította a helyszíni Active Directory-infrastruktúrát, és tartományokat vett fel vagy távolított el az erdőből, kattintson a **Frissítés** gombra a frissített lista lekértlehetőségéhez. Amikor frissít, a rendszer hitelesítő adatokat kér. Adjon meg minden olyan hitelesítő adatot, amely olvasási hozzáféréssel rendelkezik a Windows Server Active Directoryhoz. Nem kell, hogy a párbeszédpanelen előre kitöltött felhasználó legyen.  
   ![Frissítés szükséges](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Ha elkészült, zárja be a **Tulajdonságok** párbeszédpanelt az **OK**gombra kattintva. Ha eltávolította a tartományokat az erdőből, egy üzenet előugró üzenetben azt írja, hogy egy tartományt eltávolítottak, és a rendszer törli ezt a konfigurációt.
7. Folytassa a futtatási profilok módosítását.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Az Azure AD Connect varázslóval szinkronizálandó tartományok kiválasztása
A tartományszűrő beállításához tegye a következőket:

1.  Az Azure AD Connect varázsló indítása
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza **a Szinkronizálási beállítások testreszabása lehetőséget,** majd kattintson a **Tovább**gombra.
4.  Adja meg Azure AD hitelesítő adatait
5.  A **Csatlakoztatott könyvtárak** képernyőn kattintson a **Tovább gombra.**
6.  A **Tartomány- és szervezetiegység-szűrés lapon** kattintson a **Frissítés gombra.**  Új domain beteg most jelenik meg, és törölt tartományok eltűnnek.
   ![Partíciók](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>A futtatási profilok frissítése
Ha frissítette a tartományszűrőt, a futtatási profilokat is frissítenie kell.

1. Az **Összekötők** listában győződjön meg arról, hogy az előző lépésben módosított összekötő ki van jelölve. A **Műveletek csoportban**válassza **a Futtatási profilok konfigurálása**lehetőséget.  
   ![Összekötő futtatási profiljai 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Keresse meg és azonosítsa a következő profilokat:
    * Teljes importálás
    * Teljes szinkronizálás
    * Különbözeti importálás
    * Különbözeti szinkronizálás
    * Exportálás
3. Minden profilhoz állítsa be a **hozzáadott** és **eltávolított** tartományokat.
    1. Az öt profil mindegyikéhez tegye a következő lépéseket minden **egyes hozzáadott** tartományesetében:
        1. Jelölje ki a futtatási profilt, és kattintson az **Új lépés gombra.**
        2. A **Lépés konfigurálása** lap **Típus** legördülő menüjében válassza ki a konfigurált profil nevével megegyező nevű lépéstípust. Kattintson a **Tovább** gombra.  
        ![Összekötő futtatási profiljai 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Az **Összekötő konfigurációja** lap **Partíció** legördülő menüjében válassza ki a tartományszűrőhöz hozzáadott tartomány nevét.  
        ![Összekötő futtatási profiljai 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. A **Futtatási profil konfigurálása** párbeszédpanel bezárásához kattintson a **Befejezés gombra.**
    2. Az öt profil mindegyikének esetében tegye a következő lépéseket minden **egyes eltávolított** tartományesetében:
        1. Válassza ki a futtatási profilt.
        2. Ha a **Partíció** attribútum **értéke** GUID, jelölje ki a futtatási lépést, és kattintson a Lépés **törlése gombra.**  
        ![Összekötő futtatási profiljai 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Ellenőrizze a módosítást. Minden szinkronizálni kívánt tartományt lépésként kell felsorolni az egyes futtatási profilokban.
4. A **Futtatási profilok konfigurálása** párbeszédpanel bezárásához kattintson az **OK**gombra.
5.  A konfiguráció befejezéséhez teljes **importálást** és **Delta-szinkronizálást kell futtatnia.** Folytatódik olvasó a szakasz [Alkalmazása és ellenőrizze a módosításokat](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Szervezeti egységalapú szűrés
A szervezeti egység alapú szűrés módosításának előnyben részesített módja a telepítővarázsló futtatása, valamint a [tartomány- és szervezetiegység-szűrés](how-to-connect-install-custom.md#domain-and-ou-filtering)módosítása. A telepítővarázsló automatizálja a témakörben dokumentált összes feladatot.

Csak akkor kövesse ezeket a lépéseket, ha valamilyen okból nem tudja futtatni a telepítővarázslót.

A szervezeti egységalapú szűrés konfigurálásához tegye a következő lépéseket:

1. Jelentkezzen be az Azure AD Connect-szinkronizálást futtató kiszolgálóra az **ADSyncAdmins** biztonsági csoport tagjaként használt fiókhasználatával.
2. Indítsa el **a szinkronizálási szolgáltatást** a **Start** menüből.
3. Válassza **az Összekötők**lehetőséget, majd az **Összekötők** listában válassza az Active Directory tartományi szolgáltatások típusú **összekötőt.** A **Műveletek csoportban**válassza a **Tulajdonságok lehetőséget.**  
   ![Összekötő tulajdonságai](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kattintson **a Címtárpartíciók konfigurálása gombra,** jelölje ki a konfigurálni kívánt tartományt, majd kattintson a **Tárolók**gombra.
5. Amikor a rendszer kéri, adja meg a hitelesítő adatok olvasási hozzáférést a helyszíni Active Directoryhoz. Nem kell, hogy a párbeszédpanelen előre kitöltött felhasználó legyen.
6. A **Tárolók kiválasztása** párbeszédpanelen törölje a jelet a felhőkönyvtárral szinkronizálni nem kívánt ok-ok jelölőnégyzetből, majd kattintson az **OK**gombra.  
   ![A Tárolók kiválasztása párbeszédpanel en usus](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * A **Számítógépek** tárolót ki kell választani a Windows 10-es számítógépek sikeresen szinkronizálja az Azure AD-vel. Ha a tartományhoz csatlakozó számítógépek más számítógépgyártókban találhatók, győződjön meg arról, hogy azok ki vannak jelölve.
   * Ha több, megbízhatósági kapcsolattal rendelkező erdővel rendelkezik, használja a **ForeignSecurityPrincipals** tárolót. Ez a tároló lehetővé teszi az erdők közötti biztonsági csoporttagságok feloldását.
   * A **RegisteredDevices** OU-t kell kiválasztani, ha engedélyezte az eszköz visszaírási funkcióját. Ha egy másik visszaírási szolgáltatást használ, például a csoportos visszaírást, győződjön meg arról, hogy ezek a helyek ki vannak jelölve.
   * Jelöljön ki minden más szervezeti egységet, ahol a Felhasználók, az iNetOrgPersons, a Csoportok, a Kapcsolattartók és a Számítógépek találhatók. A képen ezek a szervezeti egységek találhatók a ManagedObjects OU.
   * Ha csoportalapú szűrést használ, akkor a csoportot tartalmazó szervezeti egységet is figyelembe kell venni.
   * Ne feledje, hogy beállíthatja, hogy a szűrési konfiguráció befejezése után hozzáadott új számítógép-szűrők szinkronizálva legyenek-e vagy sem. A részleteket a következő szakaszban találhatja meg.
7. Ha elkészült, zárja be a **Tulajdonságok** párbeszédpanelt az **OK**gombra kattintva.
8. A konfiguráció befejezéséhez teljes **importálást** és **Delta-szinkronizálást kell futtatnia.** Folytatódik olvasó a szakasz [Alkalmazása és ellenőrizze a módosításokat](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Új o-k szinkronizálása
A szűrés konfigurálása után létrehozott új megfelelő eszközök szinkronizálása alapértelmezés szerint szinkronizálva van. Ezt az állapotot egy bejelölt jelölőnégyzet jelzi. Egyes al-ousok kijelölését is kivonhatja. A viselkedés lefelvételéhez kattintson a jelölőnégyzetre, amíg kék pipával (alapértelmezett állapottal) fehérré nem válik. Ezután törölje a jelet a szinkronizálni nem kívánt al-ousok kijelöléséből.

Ha az összes al-ous szinkronizálva van, akkor a négyzet fehér, kék pipával.  
![Szervezeti egység az összes kijelölt mezővel](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Ha néhány al-ous nincs kijelölve, akkor a jelölőnégyzet szürke, fehér pipával.  
![Szervezeti egység, amelyen nincs kijelölve néhány alszervezeti egység](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Ezzel a konfigurációval a kezelt objektumok alatt létrehozott új szervezeti egység szinkronizálódik.

Az Azure AD Connect telepítővarázsló mindig létrehozza ezt a konfigurációt.

### <a name="dont-synchronize-new-ous"></a>Ne szinkronizálja az új ous-ok
Beállíthatja, hogy a szinkronizálási motor a szűrési konfiguráció befejezése után ne szinkronizálja az új számítógépgyártókat. Ezt az állapotot a felhasználói felületen a folytonos szürke mező jelzi, be jelölés nélkül. A viselkedés hez kattintson a jelölőnégyzetre, amíg be jelölés nélkül fehéredik. Ezután jelölje ki a szinkronizálni kívánt al-ousokat.

![Szervezeti egység a gyökér kijelölése nélkül](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Ezzel a konfigurációval a ManagedObjects alatt létrehozott új szervezeti egység nincs szinkronizálva.

## <a name="attribute-based-filtering"></a>Attribútumalapú szűrés
Győződjön meg arról, hogy a 2015 novemberi ([1.0.9125)](reference-connect-version-history.md#1091250)vagy újabb buildet használja ezekhez a lépésekhez.

> [!IMPORTANT]
>A Microsoft azt javasolja, hogy ne módosítsa az **Azure AD Connect**által létrehozott alapértelmezett szabályokat. Ha módosítani szeretné a szabályt, klónozza, és tiltsa le az eredeti szabályt. Módosítsa a klónozott szabályt. Kérjük, vegye figyelembe, hogy ezzel (az eredeti szabály letiltása) hiányozni fog az adott szabály által engedélyezett hibajavításokról vagy funkciókról.

Az attribútumalapú szűrés az objektumok szűrésének legrugalmasabb módja. Használhatja a [deklaratív kiépítés](concept-azure-ad-connect-sync-declarative-provisioning.md) szinte minden szempontból, amikor egy objektum szinkronizálva van az Azure AD-vel.

Az Active Directoryból a metaverzumra bejövő szűrést alkalmazhat, és [kimenő](#outbound-filtering) szűrést a metaverzumból az Azure AD-re. [inbound](#inbound-filtering) Azt javasoljuk, hogy alkalmazza a bejövő szűrés, mert ez a legkönnyebben karbantartható. Csak akkor használja a kimenő szűrést, ha a kiértékelés előtt több erdőobjektumainak egyesítéséhez szükséges.

### <a name="inbound-filtering"></a>Bejövő szűrés
A bejövő szűrés az alapértelmezett konfigurációt használja, ahol az Azure AD-be érkező objektumoknak rendelkezniük kell a metaverzum attribútum cloudFiltered nem érték szinkronizálandó értékre. Ha ennek az attribútumnak az értéke **Igaz**, akkor az objektum nincs szinkronizálva. Nem szabad **hamisra**állítani. Annak érdekében, hogy más szabályok képesek legyenek egy értékhez járulni, ez az attribútum csak az **Igaz** vagy **null** (hiányzik) értékekkel rendelkezik.

A bejövő szűréssorán a **hatókör** segítségével határozhatja meg, hogy mely objektumokat szinkronizálja vagy nem szinkronizálja. Itt módosíthatja a saját szervezetének követelményeit. A hatókörmodul rendelkezik egy **csoporttal** és egy **záradékkal,** amely meghatározza, hogy egy szinkronizálási szabály hatóköre mikor van. Egy csoport egy vagy több záradékot tartalmaz. Több záradék között logikai "ÉS" és több csoport között logikai "OR" van.

Nézzünk egy példát:  
![Hatókör](./media/how-to-connect-sync-configure-filtering/scope.png)  
Ezt a következőként kell **értelmezni: (department = IT) OR (osztály = Értékesítés és c = USA)**.

A következő mintákban és lépésekben példaként a felhasználói objektumot használhatja, de ezt minden objektumtípushoz használhatja.

A következő mintákban a prioritási érték 50-nel kezdődik. Ez lehet tetszőleges szám nem használt, de alacsonyabbnak kell lennie, mint 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Negatív szűrés: "ne szinkronizálja ezeket"
A következő példában kiszűri (nem szinkronizálja) az összes olyan felhasználót, ahol az **ExtensionAttribute15** metódus a **NoSync értékkel**rendelkezik.

1. Jelentkezzen be az Azure AD Connect-szinkronizálást futtató kiszolgálóra az **ADSyncAdmins** biztonsági csoport tagjaként használt fiókhasználatával.
2. Indítsa el **a Szinkronizálási szabályok szerkesztőjét** a **Start** menüből.
3. Győződjön meg arról, hogy **a Bejövő között** lehetőség van kiválasztva, majd kattintson az Új szabály hozzáadása **gombra.**
4. Adjon a szabálynak egy leíró nevet, például "*In from AD – User DoNotSyncFilter*". Jelölje ki a megfelelő erdőt, válassza a **Felhasználó** elemet **használó objektumtípust**, majd a **Személy** lehetőséget az **MV objektumtípusként**. A **Hivatkozás típusa csoportban**válassza a **Csatlakozás**lehetőséget. A **Prioritás mezőbe írjon**be egy olyan értéket, amelyet jelenleg nem használ egy másik szinkronizálási szabály (például 50), majd kattintson a **Tovább**gombra.  
   ![Bejövő 1 leírás](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. A **Hatókörszűrőben**kattintson a **Csoport hozzáadása**gombra, majd a Záradék **hozzáadása**parancsra. Az **Attribútum ban**válassza az **ExtensionAttribute15 lehetőséget.** Győződjön meg arról, hogy az **Operátor** **értéke EQUAL**, és írja be a **NoSync** értéket az **Érték** mezőbe. Kattintson a **Tovább** gombra.  
   ![Bejövő 2 hatókör](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Hagyja üresen az **Illesztés** imát, majd kattintson a **Tovább**gombra.
7. Kattintson **az Átalakítás hozzáadása**gombra, válassza a **FlowType** **konstansként**lehetőséget, majd a **cloudFiltered (felhőszűrt** attribútum) **lehetőséget.** A **Forrás** mezőbe írja be az **Igaz**értéket. A szabály mentéséhez kattintson a **Hozzáadás** gombra.  
   ![Bejövő 3 transzformáció](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. A konfiguráció befejezéséhez teljes szinkronizálást kell **futtatnia.** Folytatódik olvasó a szakasz [Alkalmazása és ellenőrizze a módosításokat](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Pozitív szűrés: "csak szinkronizáld ezeket"
A pozitív szűrés kifejezése nagyobb kihívást jelenthet, mivel olyan objektumokat is figyelembe kell venni, amelyek nem nyilvánvalóak a szinkronizáláshoz, például a konferenciatermeket. Felül kell írnia az alapértelmezett szűrőt is az **AD**- Felhasználói csatlakozás mezőben lévő beépített szabályban. Az egyéni szűrő létrehozásakor győződjön meg arról, hogy nem tartalmazza a kritikus rendszerobjektumokat, a replikációs ütközési objektumokat, a speciális postaládákat és az Azure AD Connect szolgáltatásfiókjait.

A pozitív szűrési beállításhoz két szinkronizálási szabály szükséges. A szinkronizáláshoz egy szabályra (vagy többre) van szükség a megfelelő objektumhatókörrel. Szüksége van egy második összes szinkronizálási szabályra is, amely kiszűri az összes olyan objektumot, amelyet még nem azonosítottak szinkronizálandó objektumként.

A következő példában csak azokat a felhasználói objektumokat szinkronizálja, ahol a részlegattribútum **értéke Értékesítés**.

1. Jelentkezzen be az Azure AD Connect-szinkronizálást futtató kiszolgálóra az **ADSyncAdmins** biztonsági csoport tagjaként használt fiókhasználatával.
2. Indítsa el **a Szinkronizálási szabályok szerkesztőjét** a **Start** menüből.
3. Győződjön meg arról, hogy **a Bejövő között** lehetőség van kiválasztva, majd kattintson az Új szabály hozzáadása **gombra.**
4. Adjon a szabálynak egy leíró nevet, például "*In from AD – User Sales sync*". Jelölje ki a megfelelő erdőt, válassza a **Felhasználó** elemet **használó objektumtípust**, majd a **Személy** lehetőséget az **MV objektumtípusként**. A **Hivatkozás típusa csoportban**válassza a **Csatlakozás**lehetőséget. A **Prioritás mezőbe írjon**be egy olyan értéket, amelyet jelenleg nem használ egy másik szinkronizálási szabály (például 51), majd kattintson a **Tovább**gombra.  
   ![Bejövő 4 leírás](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. A **Hatókörszűrőben**kattintson a **Csoport hozzáadása**gombra, majd a Záradék **hozzáadása**parancsra. Az **Attribútum csoportban**válassza a **részleget.** Győződjön meg arról, hogy az Operátor értéke **EQUAL**, és írja be az **Értékesítés** értéket az **Érték** mezőbe. Kattintson a **Tovább** gombra.  
   ![Bejövő 5 hatókör](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Hagyja üresen az **Illesztés** imát, majd kattintson a **Tovább**gombra.
7. Kattintson **az Átalakítás hozzáadása**gombra, válassza az Állandó **tavas flowtype lehetőséget,** és jelölje ki a **cloudFiltered-t** **célattribútumként.** **Constant** A **Forrás** mezőbe írja be a **Hamis**értéket. A szabály mentéséhez kattintson a **Hozzáadás** gombra.  
   ![Bejövő 6 transzformáció](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Ez egy különleges eset, amikor explicit módon állítsa cloudFiltered a **False**.
8. Most létre kell hoznunk a "catch-all" szinkronizálási szabályt. Adjon a szabálynak egy leíró nevet, például "*In from AD – User Catch-all szűrő*". Jelölje ki a megfelelő erdőt, válassza a **Felhasználó** elemet **használó objektumtípust**, majd a **Személy** lehetőséget az **MV objektumtípusként**. A **Hivatkozás típusa csoportban**válassza a **Csatlakozás**lehetőséget. A **Prioritás mezőbe**írjon be egy olyan értéket, amelyet jelenleg nem használ egy másik szinkronizálási szabály (például 99). Olyan elsőbbségi értéket választott, amely magasabb (alacsonyabb prioritás), mint az előző szinkronizálási szabály. De hagyott némi helyet is, így később további szűrési szinkronizálási szabályokat adhat hozzá, amikor további részlegek szinkronizálását szeretné elkezdeni. Kattintson a **Tovább** gombra.  
   ![Bejövő 7 leírás](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Hagyja üresen **a Hatókörszűrőt,** és kattintson a **Tovább**gombra. Az üres szűrő azt jelzi, hogy a szabályt minden objektumra alkalmazni kell.
10. Hagyja üresen az **Illesztés** imát, majd kattintson a **Tovább**gombra.
11. Kattintson **az Átalakítás hozzáadása**gombra, válassza az **Állandó** lehetőséget **FlowType típusként,** majd a **cloudFiltered (felhőszűrt)** lehetőséget **célattribútumként.** A **Forrás** mezőbe írja be az **Igaz**értéket. A szabály mentéséhez kattintson a **Hozzáadás** gombra.  
    ![Bejövő 3 transzformáció](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. A konfiguráció befejezéséhez teljes szinkronizálást kell **futtatnia.** Folytatódik olvasó a szakasz [Alkalmazása és ellenőrizze a módosításokat](#apply-and-verify-changes).

Szükség esetén további szabályokat hozhat létre az első típusból, ahol több objektumot is felvehet a szinkronizálásba.

### <a name="outbound-filtering"></a>Kimenő szűrés
Bizonyos esetekben csak akkor kell végezni a szűrést, ha az objektumok csatlakoztak a metaverzumhoz. Szükség lehet például az erőforráserdő mail attribútumának és a fiókerdőből származó userPrincipalName attribútumnak a megnézetére, hogy megállapítsa, szinkronizálni kell-e egy objektumot. Ezekben az esetekben hozza létre a szűrést a kimenő szabály.

Ebben a példában @contoso.com úgy módosítja a szűrést, hogy csak azok a felhasználók legyenek szinkronizálva, akiknek a levelezése és userPrincipalName végződése is van:

1. Jelentkezzen be az Azure AD Connect-szinkronizálást futtató kiszolgálóra az **ADSyncAdmins** biztonsági csoport tagjaként használt fiókhasználatával.
2. Indítsa el **a Szinkronizálási szabályok szerkesztőjét** a **Start** menüből.
3. A **Szabálytípus csoportban**kattintson a **Kimenő gombra.**
4. A ttól függően, hogy milyen verziót használ a Connect, keresse meg az **Out to AAD – User Join** vagy Out to **AAD – User Join SOAInAD**(Alkalmazáson kívül) nevű szabályt, majd kattintson a **Szerkesztés**gombra.
5. Az előugró ablakban válaszoljon az **Igen** gombra a szabály másolatának létrehozásához.
6. A **Leírás** lapon módosítsa **az Elsőbbség idét** egy fel nem használt értékre, például 50-re.
7. Kattintson a bal oldali navigációs sáv **on a Hatókörszűrő elemre,** majd a **Záradék hozzáadása**parancsra. Az **Attribútum csoportban**válassza a **leveleket.** Az **Operátor csoportban**válassza **a ENDSWITH**lehetőséget. Az **Érték mezőbe**írja be ** \@a contoso.com,** majd kattintson a Záradék hozzáadása **gombra.** Az **Attribútum ban**válassza a **userPrincipalName lehetőséget.** Az **Operátor csoportban**válassza **a ENDSWITH**lehetőséget. A **(Érték) mezőbe**írja be ** \@a contoso.com.**
8. Kattintson a **Mentés** gombra.
9. A konfiguráció befejezéséhez teljes szinkronizálást kell **futtatnia.** Folytatódik olvasó a szakasz [Alkalmazása és ellenőrizze a módosításokat](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Módosítások alkalmazása és ellenőrzése
Miután végrehajtotta a konfigurációs módosításokat, alkalmaznia kell azokat a rendszerben már meglévő objektumokra. Az is előfordulhat, hogy az objektumokat, amelyek jelenleg nem a szinkronizálási motorban kell feldolgozni (és a szinkronizálási motor kell olvasni a forrásrendszert újra annak tartalmának ellenőrzéséhez).

Ha **tartomány-** vagy **szervezetiegység-szűréssel** módosította a konfigurációt, akkor **teljes importálást**kell végeznie, amelyet **a Különbözet szinkronizálása**követ.

Ha **attribútumszűréssel** módosította a konfigurációt, akkor teljes **szinkronizálást kell végeznie.**

Tegye a következő lépéseket:

1. Indítsa el **a szinkronizálási szolgáltatást** a **Start** menüből.
2. Válassza **az Összekötők**lehetőséget . Az **Összekötők** listában válassza ki azt az összekötőt, amelyen korábban módosította a konfigurációt. A **Műveletek csoportban**válassza a **Futtatás**lehetőséget.  
   ![Összekötő futtatása](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. A **Futtatási profilok csoportban**jelölje ki az előző szakaszban említett műveletet. Ha két műveletet kell futtatnia, futtassa a másodikat az első befejezése után. (Az **Állapot** oszlop **tétlen** a kijelölt összekötőhöz.)

A szinkronizálás után a program minden módosítást előkészít az exportáláshoz. Mielőtt ténylegesen elhajtanál az Azure AD-ben, ellenőrizze, hogy ezek a módosítások helyesek-e.

1. Indítsa el a parancssort, és nyissa meg a it `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Futtassa az `csexport "Name of Connector" %temp%\export.xml /f:x` parancsot.  
   Az összekötő neve a Szinkronizálási szolgáltatásban található. Az Azure AD"-hoz hasonló a "contoso.com – AAD" név.
3. Futtassa az `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` parancsot.
4. Most már van egy export.csv nevű %temp% nevű fájlja, amely et meg lehet vizsgálni a Microsoft Excelben. Ez a fájl tartalmazza az összes exportálandó módosítást.
5. Hajtsa végre a szükséges módosításokat az adatokon vagy a konfiguráción, és futtassa újra ezeket a lépéseket (Importálás, Szinkronizálás és Ellenőrzés), amíg az exportálandó módosítások el nem érik az exportálást.

Ha elégedett, exportálja a módosításokat az Azure AD-be.

1. Válassza **az Összekötők**lehetőséget . Az **összekötők** listában válassza ki az Azure AD-összekötőt. A **Műveletek csoportban**válassza a **Futtatás**lehetőséget.
2. A **Futtatási profilok**csoportban válassza az **Exportálás**lehetőséget.
3. Ha a konfiguráció változik törölni sok objektumot, akkor megjelenik egy hiba az exportálás, ha a szám több, mint a beállított küszöbértéket (alapértelmezés szerint 500). Ha ezt a hibaüzenetet látja, ideiglenesen le kell tiltania a "[Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md)" funkciót.

Most itt az ideje, hogy újra engedélyezze az ütemezőt.

1. **A Feladatütemező indítása** a **Start** menüből.
2. Közvetlenül a **Feladatütemezőkönyvtár**alatt keresse meg az **Azure AD Sync Scheduler**nevű feladatot, kattintson a jobb gombbal, és válassza az **Engedélyezés parancsot.**

## <a name="group-based-filtering"></a>Csoportalapú szűrés
Az Azure AD Connect első telepítésekor [beállíthatja](how-to-connect-install-custom.md#sync-filtering-based-on-groups)a csoportalapú szűrést egyéni telepítéssel. Ez egy kísérleti telepítés, ahol azt szeretné, hogy csak egy kis objektumkészlet szinkronizálni. Ha letiltja a csoportalapú szűrést, az nem engedélyezhető újra. *Nem támogatott* a csoportalapú szűrés egyéni konfigurációban való használata. Ez csak akkor támogatott, hogy konfigurálja ezt a szolgáltatást a telepítő varázsló. Miután befejezte a próba, majd használja az egyik a többi szűrési lehetőségek ebben a témakörben. Ha szervezeti egység alapú szűrést használ a csoportalapú szűréssel együtt, a csoport és tagjai helyét tartalmazó szervezeti egység(eke)t is figyelembe kell venni.

Több AD-erdő szinkronizálásakor konfigurálhatja a csoportalapú szűrést úgy, hogy minden Egyes AD-összekötőhöz más csoportot ad meg. Ha egy AD-erdőben szeretne szinkronizálni egy felhasználót, és ugyanaz a felhasználó rendelkezik egy vagy több megfelelő objektummal más AD-erdőkben, gondoskodnia kell arról, hogy a felhasználói objektum és annak összes megfelelő objektuma csoportalapú szűrési hatókörön belül legyen. Példák:

* Az egyik erdőben van egy felhasználója, akinek egy másik erdőben van egy megfelelő FSP (külföldi rendszerbiztonsági tag) objektuma. Mindkét objektumnak csoportalapú szűrési hatókörön belül kell lennie. Ellenkező esetben a felhasználó nem lesz szinkronizálva az Azure AD-vel.

* Van egy felhasználó az egyik erdőben, amely rendelkezik egy megfelelő erőforrásfiókkal (pl. csatolt postaláda) egy másik erdőben. Továbbá konfigurálta az Azure AD Connectet, hogy a felhasználót az erőforrásfiókkal kapcsolja össze. Mindkét objektumnak csoportalapú szűrési hatókörön belül kell lennie. Ellenkező esetben a felhasználó nem lesz szinkronizálva az Azure AD-vel.

* Van egy felhasználó az egyik erdőben, akinek van egy megfelelő levelezési kapcsolattartója egy másik erdőben. Továbbá konfigurálta az Azure AD Connectet, hogy a felhasználót a levelezési kapcsolattartóhoz kapcsolja. Mindkét objektumnak csoportalapú szűrési hatókörön belül kell lennie. Ellenkező esetben a felhasználó nem lesz szinkronizálva az Azure AD-vel.


## <a name="next-steps"></a>További lépések
- További információ az [Azure AD Connect szinkronizálási konfigurációjáról.](how-to-connect-sync-whatis.md)
- További információ [a helyszíni identitások Azure AD-vel való integrálásáról.](whatis-hybrid-identity.md)
