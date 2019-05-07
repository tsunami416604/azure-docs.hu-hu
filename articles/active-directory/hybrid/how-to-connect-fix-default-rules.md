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
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067635"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Javítsa ki a módosított alapértelmezett szabályok az Azure AD Connectben

Az Azure Active Directory (Azure AD) Connect használja alapértelmezett szabályok a szinkronizálást.  Sajnos azonban ezek a szabályok nem vonatkoznak univerzálisan minden olyan szervezetnek. A követelmények alapján, akkor előfordulhat, hogy módosítania kell azokat. Ez a cikk ismerteti a leggyakoribb egyéni két példa, és elmagyarázza a megfelelő módszer, ezek a testreszabások eléréséhez.

>[!NOTE] 
> Meglévő alapértelmezett szabályokat érhet el a szükséges testreszabási módosítása nem támogatott. Ha így tesz, így megakadályozza, hogy ezek a szabályok frissítése a legújabb verzióra a jövőbeli kiadások. Nem jelenik meg a szükséges hibajavításokat tartalmaz, vagy új funkciókat. Ez a dokumentum ismerteti a meglévő alapértelmezett szabályok módosítása nélkül az azonos eredmények elérése érdekében. 

## <a name="how-to-identify-modified-default-rules"></a>Hogyan azonosíthatja a módosított alapértelmezett szabályok
Az Azure AD Connect 1.3.7.0 verziójával kezdődően könnyebbé vált a módosított alapértelmezett szabály azonosításához. Lépjen a **asztali alkalmazások**, és válassza ki **szinkronizálási Szabályszerkesztővel**.

![Az Azure AD Connect, a szinkronizálási Szabályszerkesztővel kiemelésével](media/how-to-connect-fix-default-rules/default1.png)

A szerkesztőben a módosított alapértelmezett szabályok a név elé egy figyelmeztető ikon jelennek meg.

![Figyelmeztetés ikon](media/how-to-connect-fix-default-rules/default2.png)

 A letiltott szabály ugyanilyen nevű mellett is megjelenik (Ez a szokásos alapértelmezett szabály).

![Szinkronizálási Szabályszerkesztővel standard alapértelmezett szabály és a módosított alapértelmezett szabály](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Közös testreszabások
Közös testreszabását az alapértelmezett szabályok a következők:

- Attribútumfolyam módosítása
- Hatókörszűrő módosítása
- Változás illesztési feltétel

Mielőtt módosítaná szabályokat:

- Tiltsa le a sync schedulert. Az ütemező alapértelmezés szerint 30 percenként futtatja. Ellenőrizze, hogy nem indul, amíg Ön változtatásokat, és az új szabályok hibaelhárítás. Az ütemező ideiglenes letiltásához indítsa el a Powershellt, és futtassa `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![PowerShell-parancsokat a szinkronizálásütemező letiltása](media/how-to-connect-fix-default-rules/default3.png)

- Hatókörszűrő módosítása a cél-címtárban található objektumokhoz törlését eredményezheti. Ügyeljen az objektumok hatókörének módosítása előtt. Azt javasoljuk, hogy módosítja egy átmeneti kiszolgálón az aktív kiszolgáló módosítása előtt.
- Előzetes futhatnak, egyetlen objektum említetteknek megfelelően az [szinkronizálási szabály ellenőrzése](#validate-sync-rule) bármely új szabály hozzáadása után a szakaszban.
- Új szabály hozzáadása vagy bármilyen egyéni szinkronizálási szabály módosítása után futtassa a teljes szinkronizálás. A szinkronizálás új szabályokat az összes objektumra vonatkozik.

## <a name="change-attribute-flow"></a>Attribútumfolyam módosítása
Nincsenek három különböző forgatókönyveket ismertetnek a az Attribútumfolyam módosítása:
- Hozzáadás, új attribútum.
- Felülírva a meglévő attribútum értékét.
- Válassza a nem létező attribútumához szinkronizálása.

Ezek szabványos alapértelmezett szabályok módosítása nélkül teheti meg.

### <a name="add-a-new-attribute"></a>Egy új attribútum hozzáadásához
Ha azt tapasztalja, hogy egy attribútum között nem folyik a forráskönyvtár a célként megadott könyvtárba, használja a [Azure AD Connect szinkronizálása: Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md) a probléma megoldásához.

Ha a bővítmények nem működik, próbáljon meg két új szinkronizálási szabályt a következő szakaszok ismertetik.


#### <a name="add-an-inbound-sync-rule"></a>Bejövő szinkronizálási szabály felvétele
Egy bejövő szinkronizálási szabály azt jelenti, az attribútum a forrása összekötőtérben, és a cél a metaverzumba. Például egy új attribútuma a flow a helyszíni Active Directory, az Azure Active Directoryhoz, hozzon létre egy új bejövő szinkronizálási szabály. Indítsa el a **szinkronizálási Szabályszerkesztővel**válassza **bejövő** irányát, valamint válassza **új szabály hozzáadása**. 

 ! Szinkronizálási szabályok Editor](media/how-to-connect-fix-default-rules/default3a.png)

A saját elnevezési konvenciója adjon nevet a szabálynak. Itt használjuk **egyéni az ad - felhasználó**. Ez azt jelenti, hogy a szabály egy egyéni szabályt, és az Active Directory összekötőterében származó bejövő szabály a metaverzumba.   

 ![Bejövő szinkronizálási szabály létrehozása](media/how-to-connect-fix-default-rules/default3b.png)

Adja meg a szabály a saját leírását, úgy, hogy a szabály jövőbeli karbantartási egyszerű. Például a leírás alapulhat a szabály célja van, és miért van szükség.

Adja meg a beállításokat a **csatlakoztatott rendszer**, **csatlakoztatott rendszer objektumtípus**, és **Metaverzum-objektum típusaként** mezőket.

Adja meg a sorrend értéket 0 és 99 között (az alacsonyabb a szám, annál magasabb prioritású). Az a **címke**, **jelszó-szinkronizálás engedélyezése**, és **letiltott** mezők, használja az alapértelmezett beállításokat.

Tartsa **Scoping szűrő** üres. Ez azt jelenti, hogy a szabály vonatkozik az összes objektum tartományhoz az Active Directory csatlakoztatott rendszer és a metaverzum között.

Tartsa **szabályok csatlakozzon** üres. Ez azt jelenti, hogy ez a szabály az illesztési feltétel, a standard szintű alapértelmezett szabályban definiált használ. Ez a másik OK, tiltsa le vagy törölje az alapértelmezett normál szabály nem. Ha nincs illesztési feltétel, az attribútum nem flow. 

Adja hozzá a megfelelő átalakításokat az attribútumhoz. Hozzárendelhet egy állandó, hogy egy állandó értéket a flow számára a target attribútum. A forrás- vagy attribútum közötti közvetlen leképezés is használhatja. Vagy egy kifejezés az attribútum is használhat. Az alábbiakban különböző [kifejezés funkciók](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) is használhatja.

#### <a name="add-an-outbound-sync-rule"></a>Kimenő szinkronizálási szabály felvétele
Az attribútum a célkönyvtárt csatolásához szeretne létrehozni az kimenő szabályt. Ez azt jelenti, hogy a forrás a metaverzumba, és a cél a csatlakoztatott rendszer. Kimenő szabály létrehozásához indítsa el a **szinkronizálási Szabályszerkesztővel**, módosítsa a **iránya** való **kimenő**, válassza ki **új szabály hozzáadása**. 

![Synchronization Rules Editor](media/how-to-connect-fix-default-rules/default3c.png)

Mivel a bejövő szabállyal, a saját elnevezési konvenció segítségével adjon nevet a szabálynak. Válassza ki a **csatlakoztatott rendszer** lehetőséget az Azure AD-bérlővel, kattintson a csatlakoztatott rendszer objektumot, amelyhez az attribútum értékét állítsa be. Állítsa be a prioritással, 0 és 99 között. 

![Kimenő szinkronizálási szabály létrehozása](media/how-to-connect-fix-default-rules/default3d.png)

Tartsa **Scoping szűrő** és **szabályok csatlakozzon** üres. Adja meg az átalakítás állandó, közvetlen vagy kifejezés. 

Most már tudja, hogyan, hogy a felhasználói objektum folyamat új attribútumot az Active Directoryból az Azure Active Directoryhoz. Ezeket a lépéseket segítségével képezze le a forrás- és az összes objektumtól bármely attribútum. További információkért lásd: [egyéni szinkronizálási szabályok létrehozásának](how-to-connect-create-custom-sync-rule.md) és [való üzembe helyezése felhasználók](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Egy meglévő attribútum értékének felülbírálása
Előfordulhat, hogy szeretné felülbírálni, amelyek leképezése már megtörtént egy attribútum értékét. Például ha mindig szeretne hodnotu null értékre egy attribútumot az Azure ad-ben, egyszerűen hozzon létre egy bejövő szabályt. Győződjön meg arról, az állandó érték `AuthoritativeNull`, flow számára a target attribútum. 

>[!NOTE] 
> Használat `AuthoritativeNull` helyett `Null` ebben az esetben. Ennek az az oka a nem null értéket váltja fel a null értéket, akkor is, ha alacsonyabb fontossági sorrendű (szám értéke a szabályban) rendelkezik. `AuthoritativeNull`, másrészről, nem cseréli le egy nem null értékű más szabály. 

### <a name="dont-sync-existing-attribute"></a>Ne szinkronizáljon a meglévő attribútum
Ha szeretne zárni egy attribútum szinkronizálása, használja az attribútumszűrés az Azure AD Connectben megadott funkció. Indítsa el a **az Azure AD Connect** asztali ikonra, és válassza ki a **szinkronizálási beállítások testreszabása**.

![Az Azure AD Connect további feladatokat beállításai](media/how-to-connect-fix-default-rules/default4.png)

 Győződjön meg arról, hogy **az Azure AD alkalmazás- és attribútumszűrés** kiválasztva, majd válassza ki **tovább**.

![Az Azure AD Connect választható funkciók](media/how-to-connect-fix-default-rules/default5.png)

Törölje az attribútumokat, amelyek a rendszer szinkronizálja a kizárni kívánt.

![Az Azure AD Connect-attribútumok](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Hatókörszűrő módosítása
Az Azure AD Sync szolgáltatásról az objektumok a legtöbb gondoskodik. Csökkentse az objektumok körét, és exportálását, az alapértelmezett normál szinkronizálási szabályok módosítása nélkül objektumok számának csökkentése. 

Szinkronizál az objektumok körét csökkentése érdekében használja az alábbi módszerek egyikét:

- cloudFiltered attribútum
- Szervezeti egységek szűrése

Ha csökkenti a hatókör szinkronizálódik a felhasználók, a jelszó Jelszókivonat szinkronizálása is leállítja a szűrt kívüli felhasználók számára. Az objektumok már szinkronizálása, miután hatókör csökkentheti, ha a szűrt kibővített objektumok törlődnek a cél könyvtárból. Ezért győződjön meg arról, hogy hatókörének alaposan.

>[!IMPORTANT] 
> Növelje a konfigurált Azure AD Connect objektumok körét nem ajánlott. Ez megnehezíti a a Microsoft támogatási csoportja tudni, hogy a testreszabások. Ha növeli a kell hatókörébe tartozó objektumok, szerkessze a meglévő szabályt, klónozza, és letiltja az eredeti szabályt. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered attribútum
Ez az attribútum nem állítható be az Active Directoryban. Ez az attribútum értékének beállítása egy új bejövő szabály hozzáadásával. Ezután **átalakítási** és **kifejezés** be, ez az attribútum a metaverzumban. Az alábbi példa bemutatja, hogy nem kívánja szinkronizálni az összes olyan felhasználót, akinek részleg neve kezdődik **HRD** (kis-és nagybetűket):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Hogy először a szervezeti egység (Active Directory) forrásból kisbetűsre alakít át. Ezután használja a `Left` függvény, azt csak az első három karakterét tartott, és képest az `hrd`. Ha egyezést mutat, az értéke `True`, ellenkező esetben `NULL`. A NULL értékre állítja, az alacsonyabb fontossági sorrendű (magasabb számértéket) egy másik szabály írható-e egy másik feltétellel. Egy objektum ellenőrzése a szinkronizálási szabály, a futtatási előzetes a [ellenőrzése a szinkronizálási szabály](#validate-sync-rule) szakaszban.

![Bejövő szinkronizálási szabály beállítások létrehozása](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Szervezeti egységek szűrése
Hozzon létre egy vagy több szervezeti egységet (OU), és helyezze át az objektumokat, nem szinkronizálja ezeket a szervezeti egységekhez. Ezt követően konfigurálja a szervezeti egység szűrése az Azure AD Connectben. Indítsa el a **az Azure AD Connect** a asztali ikonra, majd válassza a következő beállításokat. Beállíthatja, hogy a szervezeti egység az Azure AD Connect telepítése időpontjában szűrés. 

![Az Azure AD Connect további feladatok](media/how-to-connect-fix-default-rules/default8.png)

Kövesse a varázsló utasításait, és törölje a szervezeti egységek nem kívánja szinkronizálni.

![Az Azure AD-csatlakozás tartományhoz és szűrési lehetőségeit szervezeti egység](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Változás illesztési feltétel
Az Azure AD Connect által konfigurált alapértelmezett illesztési feltételek használata. Alapértelmezett illesztési feltételek módosítása megnehezíti a testreszabási lehetőségek ismertetése, és támogatja a termék a Microsoft ügyfélszolgálatához.

## <a name="validate-sync-rule"></a>Szinkronizálási szabály ellenőrzése
Az újonnan hozzáadott szinkronizálási szabály ellenőrizheti az előzetes verziójú funkció használatával a teljes szinkronizálási ciklust futtatása nélkül. Válassza ki az Azure AD Connectben **szinkronizálási szolgáltatás**.

![Az Azure AD Connect szinkronizálási szolgáltatás kiemeli a](media/how-to-connect-fix-default-rules/default10.png)

Válassza ki **keresés a Metaverzumban**. Válassza ki a hatókör-objektumot, **személy**válassza **záradék hozzáadása**, és említik a keresési feltételeknek. Majd **keresési**, és kattintson duplán az objektum a keresési eredmények között. Ellenőrizze, hogy az adatok az Azure AD Connectben naprakész-e az adott objektum futtat importálás és szinkronizálás az erdő Ez a lépés futtatása előtt.

![Szinkronizálási szolgáltatáskezelő](media/how-to-connect-fix-default-rules/default11.png)

A **Metaverzumbeli objektumának tulajdonságait**válassza **összekötők**, jelölje ki az objektumot a megfelelő Connector (erdő), és válassza ki **tulajdonságai...** .

![Metaverzum-objektum tulajdonságai](media/how-to-connect-fix-default-rules/default12.png)

Válassza ki **megtekintése...**

![Összekötőtér-objektum tulajdonságai](media/how-to-connect-fix-default-rules/default13a.png)

Válassza az Előnézet ablak **készítése előzetes** és **importálási Attribútumfolyam** a bal oldali panelen.

![Előzetes verzió](media/how-to-connect-fix-default-rules/default14.png)
 
Itt láthatja, hogy az újonnan hozzáadott szabály az objektum fut, és be van állítva a `cloudFiltered` attribútum igaz értékre.

![Előzetes verzió](media/how-to-connect-fix-default-rules/default15a.png)
 
Hasonlítsa össze a módosított szabályt az alapértelmezett szabály, exportálja a szabályok is külön-külön szövegfájlként. Ezeket a szabályokat PowerShell-parancsfájl fájlként exportálja. A módosítások megtekintéséhez bármely fájl összehasonlító eszköz (például windiff) használatával összehasonlíthatja azokat. 
 
Figyelje meg, hogy a módosított szabályban a `msExchMailboxGuid` attribútum módosul, amelyikben a **kifejezés** típusa, helyett **közvetlen**. Is, hogy módosítja az értékét **NULL** és **ExecuteOnce** lehetőséget. Identified és a fontossági sorrend különbség figyelmen kívül hagyhatja. 

![WinDiff eszköz kimeneti](media/how-to-connect-fix-default-rules/default17.png)
 
Javítsa ki őket vissza az alapértelmezett beállítások módosítása a szabályokat, a módosított szabály törlése, és engedélyezze az alapértelmezett szabályt. Győződjön meg arról, hogy ne veszítse el a Testreszabás próbál elérni. Amikor elkészült, futtassa **teljes szinkronizálást**.

## <a name="next-steps"></a>További lépések
- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)



