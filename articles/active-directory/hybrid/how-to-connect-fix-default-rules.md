---
title: Hogyan háríthatja el a módosított alapértelmezett szabályok – az Azure AD Connect |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatja el a módosított alapértelmezett szabályokkal, az Azure AD Connecttel származnak.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f524e8cef3878816cec53575217bdb6d0fd9be7b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501256"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Javítsa ki a módosított alapértelmezett szabályok az Azure AD Connectben

Az Azure AD Connect szinkronizálási alapértelmezett szabályokat tartalmaz.  Sajnos ezeket a szabályokat nem vonatkoznak a felsővezetőknek, akik minden olyan szervezetnek, és előfordulhat, ha azokat módosítani kell a követelmények alapján.

 Ha módosította az alapértelmezett szabályokat, vagy tervezi, hogy módosítsa azokat, majd szánjon egy kis időt a dokumentum elolvasásához.

Ez a dokumentum 2 talál példákat a leggyakrabban használt a felhasználók által végzett testreszabások, és ismerteti a megfelelő módszer, ezek a testreszabások eléréséhez.

>[!NOTE] 
> Meglévő alapértelmezett szabály(ok) eléréséhez szükséges testreszabást módosítása nem támogatott – ezzel megakadályozza, ezek a szabályok frissítése a legújabb verzióra a későbbi kiadásokban. Ezzel elkerülheti az első szükséges hibajavítások és új funkciók.  Ez a dokumentum fogja azt ismertetik, hogyan az azonos eredmények elérése érdekében a meglévő alapértelmezett szabályok módosítása nélkül. 

## <a name="how-to-identify-modified-default-rules"></a>Hogyan azonosíthatja a módosított alapértelmezett szabályokat?
1.3.7.0 verziójával kezdődően **az Azure AD Connect**, mostantól könnyedén azonosíthatja a módosított alapértelmezett szabályt. Asztali alkalmazás megjelenítése, és kattintson a **szinkronizálási Szabályszerkesztővel**.

![Szerkesztő](media/how-to-connect-fix-default-rules/default1.png)

A szerkesztőben módosított alapértelmezett szabályokat megjelenik a név elé egy ikont az alább látható módon:

![Ikon](media/how-to-connect-fix-default-rules/default2.png)

 Emellett jelenik meg mellette ugyanazzal a névvel, amely az alapértelmezett normál szabály letiltott szabály:

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Közös testreszabások
Közös testreszabását az alapértelmezett szabályok a következők:

- [Attribútumfolyam módosítása](#changing-attribute-flow)
- [Hatókörszűrő módosítása](#changing-scoping-filter)
- [Illesztési feltétel módosítása](#changing-join-condition)

## <a name="before-changing-any-rules"></a>Összes szabály módosítása előtt
- Tiltsa le a sync schedulert.  Az ütemező alapértelmezés szerint 30 percenként futtatja. Ellenőrizze, hogy nem indul, módosítása és hibáinak elhárítása az új szabályokat. Az ütemező ideiglenes letiltásához indítsa el a Powershellt, és futtassa `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default3.png)

- Hatókörszűrő módosítása a cél-címtárban található objektumokhoz törlését eredményezheti. Kérjük, gondosan az objektumok hatókörének módosítása előtt. Azt javasoljuk, hogy módosítja egy átmeneti kiszolgálón az aktív kiszolgáló módosítása előtt.
- Futtassa egy előzetes verziójú, egyetlen objektum említetteknek megfelelően [szinkronizálási szabály ellenőrzése](#validate-sync-rule) bármely új szabály hozzáadása után a szakaszban.
- Új szabály hozzáadása vagy bármilyen egyéni szinkronizálási szabály módosítása után futtassa a teljes szinkronizálás. A szinkronizálás új szabályok vonatkoznak összes objektumot.

## <a name="changing-attribute-flow"></a>Attribútumfolyam módosítása
Az Attribútumfolyam 3 különböző történhet, nézzük, hogyan érhető el, a standard szintű alapértelmezett szabályok módosítása nélkül.
- Új attribútum hozzáadása
- Meglévő attribútum értékének felülbírálása
- Ne szinkronizáljon a meglévő attribútum

### <a name="adding-new-attribute"></a>Új attribútum hozzáadása:
Ha úgy találja, hogy az attribútum nem folyik a forráskönyvtár a célként megadott könyvtárba, majd használhatja a [Azure AD Connect szinkronizálása: Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md) áramlását az új attribútumok.

Vegye figyelembe, hogy az első választás használata legyen [Azure AD Connect szinkronizálása: Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md), egy box szolgáltatás az Azure AD Connect által biztosított tartományon kívül. Azonban ha azt nem az Ön számára, majd haladjon végig a következő lépéseket az attribútum flow meglévő szabványos alapértelmezett szinkronizálási szabály módosítása nélkül, akkor ehhez két új szinkronizálási szabályok hozzáadásával.


#### <a name="add-an-inbound-sync-rule"></a>Bejövő szinkronizálási szabály felvétele:
Bejövő szinkronizálási szabály azt jelenti, az attribútum a forrása összekötőtérben, és célja a metaverzumba. Például a flow új attribútum a helyszíni Active Directoryból az Azure Active Directoryhoz, hozzon létre egy új bejövő szinkronizálási szabály indítsa el a **szinkronizálási Szabályszerkesztő**, majd válassza ki az irányba haladnak, mint **bejövő** kattintson **új szabály hozzáadása**. 

 ![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default3a.png)

Adjon nevet a szabálynak, a saját elnevezési konvenciója, itt is használt **egyéni az ad - felhasználó**, ez azt jelenti, hogy a szabály egy egyéni szabályt és az AD-összekötő-térben bejövő szabály a Metaverzumba. 

 ![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default3b.png)

Úgy, hogy a szabály a jövőbeli karbantartási egyszerűen, mi az, hogy ez a szabály célját, és miért volt szükség például kell adni a saját a szabály leírását.
Kiválasztott csatlakoztatott rendszer (erdő) attribútum forrása. Ezután a csatlakoztatott rendszer objektumtípus és a Metaverzum-objektum típusa.

Adja meg a sorrend érték között, 0 – 99 (az alacsonyabb a szám nagyobb az elsőbbséget). Tartsa meg a többi mező például a "Tag", "Engedélyezése jelszó-szinkronizálás" és "Disabled" alapértelmezettként.

"Scoping szűrő" Keep üres, ez azt jelenti, hogy a szabály érvényes AD-csatlakoztatott rendszer és a Metaverzum között csatlakoztatott összes objektumhoz.

A "Csatlakozás a szabályokat" üres, ami azt jelenti, hogy ez a szabály az illesztési feltétel, a standard szintű alapértelmezett szabályban definiált a rendszer célszámítógépre megtartása. Ez a nem letiltása/törlése a szabványos alapértelmezett szabályt, mert ha nincs illesztési feltétel célszámítógépre majd az attribútum nem átkerülnek a másik OK. 

Adja hozzá a megfelelő átalakítási az attribútum hozzárendelése egy állandó értéket a célattribútum áramlását állandó, vagy közvetlen forrás vagy cél attribútuma vagy attribútum kifejezés közötti leképezést. Az alábbiakban különböző [kifejezés funkciók](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) is használhatja.

#### <a name="add-an-outbound-sync-rule"></a>Kimenő szinkronizálási szabály felvétele:
Eddig csak egy bejövő szinkronizálási szabály hozzáadásával el fele a munkát, mert az attribútum még nem kapcsolták össze a célkönyvtárat. Az attribútum összekapcsolása a cél igazgató szeretne létrehozni az kimenő szabályt, ami azt jelenti, hogy a forrás metaverzum és a cél csatlakoztatott rendszer. Egy kimenő szabály létrehozásához indítsa el a **szinkronizálási Szabályszerkesztő**, módosítsa a **iránya** való **kimenő** kattintson **új szabály hozzáadása**. 

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default3c.png)

A bejövő szabályt, mint a saját elnevezési konvenciókhoz, és használhatja **neve** a szabályt. Válassza ki a **csatlakoztatott rendszer** , Azure AD-bérlőjében válassza ki a csatlakoztatott rendszer objektumot, amelyhez szeretné beállítani az attribútum értéke. Állítsa be a sorrend között, 0 – 99. 

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default3d.png)

Tartsa **Scoping szűrő** üres, tartsa **szabályok csatlakozzon** üres, töltse ki az átalakítás állandó, közvetlen vagy kifejezés. 

Ebben a példában azt rendelkezik mutatni, hogyan áramlanak egy felhasználói objektumot az Active Directoryból az Azure Active Directory új attribútumot. Ezeket a lépéseket segítségével képezze le a forrás- és az összes objektumtól bármely attribútum.  További információ: [egyéni szinkronizálási szabályok létrehozásának](how-to-connect-create-custom-sync-rule.md) és [való üzembe helyezése felhasználók](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="overriding-value-of-existing-attribute"></a>Meglévő attribútum értékének felülbírálása
Lehetséges, hogy szeretné felülbírálni a már csatlakoztatott attribútum, például mindig szeretné állítani, Null értéket egy attribútumot az Azure AD-ben, ezt megteheti létrehozásával egyszerűen csak egy bejövő szabályt az előző lépésben és a flow említett is  **AuthoritativeNull** állandó értéket a célattribútum. Vegye figyelembe, hogy rendelkezik használtuk AuthoritativeNulll Null helyett ebben az esetben. Ennek az oka a nem null értéket lecseréli a Null értéket, akkor is, ha alacsonyabb fontossági sorrendű (magasabb számértéket a szabályban) rendelkezik. Azonban a AuthoritativeNull Null-ként lesz kezelve, és nem váltja fel nem null értéket más szabály. 

### <a name="dont-sync-existing-attribute"></a>Ne szinkronizáljon a meglévő attribútum
Ha szeretne zárni egy attribútum szinkronizálása, majd használhatja az attribútumszűrés az Azure AD Connectben megadott funkció. Indítsa el a **az Azure AD Connect** asztali ikonra, és válassza ki a **szinkronizálási beállítások testreszabása**.

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default4.png)

 Győződjön meg arról, hogy **az Azure AD alkalmazás- és attribútumszűrés** van kijelölve, kattintson **tovább**.

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default5.png)

Törölje a jelet a rendszer szinkronizálja a kizárni kívánt attribútumokat.

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>Hatókörszűrő módosítása
Az Azure AD Sync gondoskodik az objektumok a legtöbb, csökkentheti az objektumok körét, és csökkenteni az alapértelmezett normál szinkronizálási szabályok módosítása nélkül exportálható támogatott módon kevesebb objektumot. Abban az esetben, ha szeretné növelni a hatókörébe tartozó objektumok, akkor **szerkesztése** a meglévő szabályt, klónozza, és tiltsa le az eredeti szabályt. A Microsoft azt javasolja, hogy nem konfigurált Azure AD Connect a hatókör növelése. Objektumok körének növelése fog megnehezítik a támogatási csapat számára a testreszabási lehetőségek ismertetése, és támogatja a termék.

Itt látható, hogyan csökkentheti az Azure ad-val szinkronizált objektumok körét. Ne feledje, hogy ha csökkenti a hatóköre a **felhasználók** szinkronizálódik, majd a jelszó Jelszókivonat szinkronizálása is leállítja a szűrt kívüli felhasználók számára. Ha az objektumok már szinkronizálás folyamatban, majd hatókör, csökkentse a szűrt kibővített objektumok törlődik a célkönyvtárt a, meg alaposan felmerülő dolgozhat.
Az alábbiakban a támogatott módon szinkronizálását végzi az objektumok körét csökkentése érdekében.

- [cloudFiltered attribútum](#cloudfiltered-attribute)
- [Szervezeti egységek szűrése](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>cloudFiltered attribútum
Vegye figyelembe, hogy ez nem egy attribútuma, amely akkor állítható be az Active Directoryban. Be kell állítania az attribútum értékét adja hozzá egy új bejövő szabály említetteknek megfelelően **felülírva a meglévő attribútum értéke** szakaszban. Ezután a **átalakítási** és **kifejezés** be, ez az attribútum a metaverzumban. Íme egy példa, amelyet a felhasználó, akinek részleg neve megkülönbözteti a kis-és nagybetű nincs megkülönböztetve előtaggal kezdődik az összes szinkronizálni szeretne **HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Hogy először a részleg forrásból (Active Directory) való konvertálása kisbetű. Majd a bal oldali funkcióval, azt csak az első 3 karaktere tartott, és összehasonlítják azt hrd. Ha egyezést mutat, majd állítsa az értékét igaz egyéb null értékű. Vegye figyelembe, hogy az érték NULL, állítja azt, hogy az alacsonyabb fontossági sorrendű (magasabb számértéket) egy másik szabály írható-e másik feltétellel. Futtassa a következő szinkronizálási szabály érvényesítéséhez, amint már említettük, az egyik objektum előzetes [szinkronizálási szabály ellenőrzése](#validate-sync-rule) szakaszban.

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>Szervezeti egységek szűrése
Hozzon létre egy vagy több szervezeti egységek, és helyezze át az objektumokat, nem szinkronizálja ezeket a szervezeti egységekhez. Majd állítsa be a szervezeti egységre, indítsa el az Azure AD Connectben szűrés **az Azure AD Connect** az asztali ikonra, majd válassza a beállítások az alább látható módon. Beállíthatja, hogy a szervezeti egység az Azure AD Connect telepítése időpontjában szűrés. 

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default8.png)

Kövesse a varázsló utasításait, és ezután törölje a szervezeti egységek nem kívánja szinkronizálni.

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>Illesztési feltétel módosítása
A Microsoft azt javasolja, hogy használja-e az alapértelmezett csatlakozik az Azure AD Connect által konfigurált feltételeknek. Alapértelmezett illesztési feltételek módosítása fog megnehezítik a támogatási csapat számára a testreszabási lehetőségek ismertetése, és támogatja a termék.

## <a name="validate-sync-rule"></a>Szinkronizálási szabály ellenőrzése
Az újonnan hozzáadott szinkronizálási szabály ciklusban teljes szinkronizálás futtatása nélkül az előzetes verziójú funkció használatával ellenőrizheti. Indítsa el a **szinkronizálási szolgáltatás** felhasználói felületén.

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default10.png)

Kattintson a a **keresés a Metaverzumban**, jelölje be hatókör objektumot használ **személy**, **záradék hozzáadása** és említik a keresési feltételeknek. Kattintson a **keresési** gombra, és kattintson duplán az az objektum a **keresési eredmények** vegye figyelembe, hogy futtatja az importálás és szinkronizálás az erdőben található ez a lépés futtatása előtt, Ez azért szükséges az adatok az Azure AD Connectben az objektum naprakész állapotban.

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default11.png)



Válassza ki **összekötők**, jelölje ki az objektumot a megfelelő connector(forest), kattintson a **tulajdonságai...** .

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default12.png)

Kattintson a **megtekintése...**

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default13a.png)

Kattintson a **készítése előzetes** és **importálási Attribútumfolyam** a bal oldali panelen.

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default14.png)
 
Itt láthatja, hogy az újonnan hozzáadott szabály az objektum fut, és rendelkezik a cloudFiltered attribútum értéke igaz.

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default15a.png)
 
A szabály az alapértelmezett szabály módosítása összehasonlítási módja?
Szövegfájlok, külön-külön exportálhatja a szabályokat is. Ezeket a szabályokat powershell-parancsfájl fájlként exportálja. Bármely fájl összehasonlító eszköz segítségével megtekintheti, milyen módosításokat végzett őket össze lehessen hasonlítani. Itt ebben a példában használt windiff hasonlítsa össze a két fájlt.
 
Láthatja, hogy a felhasználó módosította a szabály, attribútum msExchMailboxGuid módosul, amelyikben **kifejezés** helyett írja be **közvetlen** értékkel, **NULL** és  **ExecuteOnce** lehetőséget. Identified és a fontossági sorrend különbség figyelmen kívül hagyhatja. 

![Alapértelmezett szabályok](media/how-to-connect-fix-default-rules/default17.png)
 
Hogyan háríthatja el a módosított alapértelmezett szabály?
Az alapértelmezett beállításokat szabályok javításához a módosított szabály törlése és az alapértelmezett szabály engedélyezése a lent látható módon, és futtassa a **teljes szinkronizálást**. Ezzel, hogy szánjon javítási műveleteket, ahogy korábban említettük, hogy ne veszítse el a testreszabása előtt elérni kívánt ## további lépések

## <a name="next-steps"></a>További lépések
- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)

