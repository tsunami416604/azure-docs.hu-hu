---
title: Módosított alapértelmezett szabályok javítása - Azure AD Connect | Microsoft dokumentumok
description: Ismerje meg, hogyan javíthatja ki az Azure AD Connect hez kapcsolódó módosított alapértelmezett szabályokat.
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
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036981"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Módosított alapértelmezett szabályok javítása az Azure AD Connectben

Az Azure Active Directory (Azure AD) Connect alapértelmezett szabályokat használ a szinkronizáláshoz.  Sajnos ezek a szabályok nem vonatkoznak általánosan minden szervezetre. A követelmények alapján előfordulhat, hogy módosítania kell őket. Ez a cikk két példát mutat be a leggyakoribb testreszabásokra, és ismerteti a testreszabások helyes elérésének módját.

>[!NOTE] 
> A meglévő alapértelmezett szabályok módosítása a szükséges testreszabás elérése érdekében nem támogatott. Ha így tesz, megakadályozza, hogy ezeket a szabályokat a legújabb verzióra frissítse a későbbi kiadásokban. Nem kapja meg a szükséges hibajavításokat vagy új funkciókat. Ez a dokumentum bemutatja, hogyan érheti el ugyanazt az eredményt a meglévő alapértelmezett szabályok módosítása nélkül. 

## <a name="how-to-identify-modified-default-rules"></a>Módosított alapértelmezett szabályok azonosítása
Az Azure AD Connect 1.3.7.0-s verziójától kezdve egyszerűen azonosíthatja a módosított alapértelmezett szabályt. Nyissa meg **az Asztali alkalmazások**lapot, és válassza a **Szinkronizálási szabályok szerkesztője lehetőséget.**

![Azure AD Connect, kiemelve a szinkronizálási szabályok szerkesztőjével](media/how-to-connect-fix-default-rules/default1.png)

A Szerkesztőben a módosított alapértelmezett szabályok egy figyelmeztető ikonnal jelennek meg a név előtt.

![Figyelmeztetés ikon](media/how-to-connect-fix-default-rules/default2.png)

 Egy letiltott szabály is megjelenik mellette az azonos nevű szabály (ez a szokásos alapértelmezett szabály).

![Szinkronizálási szabályok szerkesztője, szabványos alapértelmezett szabály és módosított alapértelmezett szabály](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Gyakori testreszabások
Az alapértelmezett szabályok gyakori testreszabásai az alábbiak:

- Attribútumfolyamat módosítása
- Hatókörszűrő módosítása
- Illesztési feltétel módosítása

A szabályok módosítása előtt:

- Tiltsa le a szinkronizálási ütemezőt. Az ütemező alapértelmezés szerint 30 percenként fut. Győződjön meg arról, hogy nem indul el, miközben módosításokat hajt végre, és hibaelhárítást hajt végre az új szabályokkal. Az ütemező ideiglenes letiltásához indítsa `Set-ADSyncScheduler -SyncCycleEnabled $false`el a PowerShellt, és futtassa a programot.
 ![A szinkronizálási ütemező letiltására irányuló PowerShell-parancsok](media/how-to-connect-fix-default-rules/default3.png)

- A hatókörszűrő módosítása a célkönyvtárban lévő objektumok törlését eredményezheti. Legyen óvatos, mielőtt bármilyen változtatást eszközölt volna az objektumok hatókörében. Azt javasoljuk, hogy az aktív kiszolgálón végzett módosítások előtt módosítsa az átmeneti kiszolgálót.
- Az új szabályok hozzáadása után futtasson egy előnézetet egyetlen objektumon, ahogy azt a [Szinkronizálási szabály érvényesítése](#validate-sync-rule) szakasz is említi.
- Futtasson teljes szinkronizálást új szabály hozzáadása vagy egyéni szinkronizálási szabályok módosítása után. Ez a szinkronizálás új szabályokat alkalmaz az összes objektumra.

## <a name="change-attribute-flow"></a>Attribútumfolyamat módosítása
Az attribútumfolyamat módosítására három különböző forgatókönyv létezik:
- Új attribútum hozzáadása.
- Egy meglévő attribútum értékének felülbírálása.
- Meglévő attribútum szinkronizálásának mellőzése.

Ezeket a szabványos alapértelmezett szabályok módosítása nélkül is megteheti.

### <a name="add-a-new-attribute"></a>Új attribútum hozzáadása
Ha úgy találja, hogy egy attribútum nem áramlik a forráskönyvtárból a célkönyvtárba, használja az [Azure AD Connect szinkronizálás: Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md) ezt kijavítani.

Ha a bővítmények nem működnek, próbáljon meg két új szinkronizálási szabályt hozzáadni, amelyeket az alábbi szakaszok ismertetett.


#### <a name="add-an-inbound-sync-rule"></a>Bejövő szinkronizálási szabály hozzáadása
A bejövő szinkronizálási szabály azt jelenti, hogy az attribútum forrása egy összekötő tér, a cél pedig a metaverzum. Ha például egy új attribútumot szeretne a helyszíni Active Directoryból az Azure Active Directoryba, hozzon létre egy új bejövő szinkronizálási szabályt. Indítsa el a **Szinkronizálási szabályok szerkesztőt,** válassza a **Bejövő irány** lehetőséget, és válassza **az Új szabály hozzáadása**lehetőséget. 

 ![Szinkronizálási szabályok szerkesztője](media/how-to-connect-fix-default-rules/default3a.png)

Kövesse a saját elnevezési konvenciónevét a szabály. Itt az **AD - User egyéni be- és berendelését**használjuk. Ez azt jelenti, hogy a szabály egy egyéni szabály, és egy bejövő szabály az Active Directory összekötő tér a metaverzum.   

 ![Bejövő szinkronizálási szabály létrehozása](media/how-to-connect-fix-default-rules/default3b.png)

Adja meg a saját leírását a szabály, hogy a jövőben a szabály fenntartása egyszerű. A leírás alapulhat például a szabály célján, és hogy miért van rá szükség.

Adja meg a beállításokat a **Csatlakoztatott rendszer,** **a Csatlakoztatott rendszerobjektum típusa**és a **Metaverzum objektumtípus** mezőkhöz.

Adja meg a 0 és 99 között megadott prioritási értéket (minél alacsonyabb a szám, annál nagyobb a prioritás). A **Címke**, **A Jelszó-szinkronizálás engedélyezése**és a **Letiltott** mezőkben használja az alapértelmezett beállításokat.

Tartsa üresen **a hatókörszűrőt.** Ez azt jelenti, hogy a szabály az Active Directoryhoz csatlakoztatott rendszer és a metaverzum között egyesített összes objektumra vonatkozik.

A **Join szabályok üresen tartása.** Ez azt jelenti, hogy ez a szabály a szabványos alapértelmezett szabályban meghatározott illesztési feltételt használja. Ez egy másik ok, amiért nem tiltja le vagy nem törli a szokásos alapértelmezett szabályt. Ha nincs illesztési feltétel, az attribútum nem fog folyni. 

Adja hozzá a megfelelő átalakításokat az attribútumhoz. Állandót rendelhet, hogy állandó értékáramlást hozhasson a célattribútumhoz. A forrás- vagy célattribútum közötti közvetlen leképezést használhatja. Vagy használhat kifejezést az attribútumhoz. Az alábbiakban különböző [kifejezésfüggvényeket](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) használhat.

#### <a name="add-an-outbound-sync-rule"></a>Kimenő szinkronizálási szabály hozzáadása
Az attribútum nak a célkönyvtárhoz való csatolásához létre kell hoznia egy kimenő szabályt. Ez azt jelenti, hogy a forrás a metaverzum, a cél pedig a csatlakoztatott rendszer. Kimenő szabály létrehozásához indítsa el a **Szinkronizálási szabályok szerkesztőjét,** módosítsa az **Irány átfelé** **értéket,** és válassza **az Új szabály hozzáadása**lehetőséget. 

![Szinkronizálási szabályok szerkesztője](media/how-to-connect-fix-default-rules/default3c.png)

A bejövő szabályhoz ugyanúgy használhatja a saját elnevezési konvencióját a szabály elnevezéséhez. Válassza ki a **csatlakoztatott rendszer** az Azure AD-bérlő, és válassza ki a csatlakoztatott rendszer objektumot, amelyhez be szeretné állítani az attribútum értékét. Állítsa be a prioritást 0 és 99 között. 

![Kimenő szinkronizálási szabály létrehozása](media/how-to-connect-fix-default-rules/default3d.png)

Tartsa üresen **a Hatókörszűrő és** **a Join szabályokat.** Töltse ki az átalakítást állandó, közvetlen vagy kifejezésként. 

Most már tudja, hogyan lehet egy új attribútumot egy felhasználói objektum az Active Directoryból az Azure Active Directoryba. Ezekkel a lépésekkel bármely objektum bármely attribútumát leképezheti a forráshoz és a célhoz. További információt az [Egyéni szinkronizálási szabályok létrehozása](how-to-connect-create-custom-sync-rule.md) és a Felkészülés a felhasználók kiépítésére című [témakörben talál.](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)

### <a name="override-the-value-of-an-existing-attribute"></a>Meglévő attribútum értékének felülbírálása
Előfordulhat, hogy felül szeretné bírni egy már leképezett attribútum értékét. Ha például mindig szeretne egy null értéket beállítani egy attribútumhoz az Azure AD-ben, egyszerűen hozzon létre egy bejövő szabályt csak. Az állandó érték `AuthoritativeNull`, folyása a célattribútumhoz. 

>[!NOTE] 
> Használja `AuthoritativeNull` helyett `Null` ebben az esetben. Ennek az az oka, hogy a null érték felülírja a null értéket, még akkor is, ha alacsonyabb a prioritása (nagyobb számérték a szabályban). `AuthoritativeNull`, másrészt más szabályok nem helyettesítik a null értéktől eltérő értéket. 

### <a name="dont-sync-existing-attribute"></a>Meglévő attribútum szinkronizálásának mellőzése
Ha ki szeretne zárni egy attribútumot a szinkronizálásból, használja az Azure AD Connect attribútumszűrési szolgáltatását. Indítsa el az **Azure AD Connect et** az asztal ikonjáról, majd válassza a **Szinkronizálási beállítások testreszabása lehetőséget.**

![Az Azure AD Connect további feladatbeállításai](media/how-to-connect-fix-default-rules/default4.png)

 Győződjön meg arról, hogy az **Azure AD alkalmazás és attribútumszűrés** ki van jelölve, és válassza a **Tovább**gombot.

![Az Azure AD Connect – választható funkciók](media/how-to-connect-fix-default-rules/default5.png)

Törölje a szinkronizálásból kizárni kívánt attribútumokat.

![Az Azure AD Connect attribútumai](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Hatókörszűrő módosítása
Az Azure AD Sync gondoskodik a legtöbb objektum. Csökkentheti az objektumok hatókörét, és csökkentheti az exportálandó objektumok számát a szabványos alapértelmezett szinkronizálási szabályok módosítása nélkül. 

A szinkronizált objektumok hatókörének csökkentéséhez használja az alábbi módszerek egyikét:

- cloudFiltered attribútum
- Szervezeti egység szűrése

Ha csökkenti a szinkronizált felhasználók hatókörét, a jelszókivonat-szinkronizálás a kiszűrt felhasználók számára is leáll. Ha az objektumok már szinkronizálódnak, a hatókör csökkentése után a szűrt objektumok törlődnek a célkönyvtárból. Ezért győződjön meg arról, hogy nagyon óvatosan kell távcsövet biztosítania.

>[!IMPORTANT] 
> Az Azure AD Connect által konfigurált objektumok hatókörének növelése nem ajánlott. Ez megnehezíti a Microsoft támogatási csapatának a testreszabások megértését. Ha növelnie kell az objektumok hatókörét, szerkesztheti a meglévő szabályt, klónoznia kell, és le kell tiltania az eredeti szabályt. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered attribútum
Ez az attribútum nem állítható be az Active Directoryban. Állítsa be ennek az attribútumnak az értékét egy új bejövő szabály hozzáadásával. Ezután **az Átalakítás** és **kifejezés** segítségével állíthatja be ezt az attribútumot a metaverzumban. A következő példa azt mutatja, hogy nem szeretné szinkronizálni azösszes olyan felhasználót, akinek a részlegneve **HRD-vel** kezdődik (a kis- és nagybetűk et nem figyelembe vevő):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Először a részleget forrásból (Active Directoryból) kisbetűssé alakítottuk. Ezután a `Left` funkció használatával csak az első három `hrd`karaktert vettük, és összehasonlítottuk a . Ha egyezik, az érték `True`értéke `NULL`egyébként . Az érték null értékre állításakor egy másik, alacsonyabb prioritású (magasabb számértékű) szabály más feltétellel írhat rá. Futtassa az előnézetet egy objektumon a szinkronizálási szabály érvényesítéséhez, ahogy azt a [Szinkronizálási szabály érvényesítése](#validate-sync-rule) szakasz is említi.

![Bejövő szinkronizálási szabály beállításainak létrehozása](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Szervezeti egység szűrése
Létrehozhat egy vagy több szervezeti egységet, és áthelyezheti azokat az objektumokat, amelyeket nem szeretne szinkronizálni ezekkel a szervezeti egységekkel. Ezután konfigurálja a szervezeti egység szűrését az Azure AD Connectben. Indítsa el az **Azure AD Connectet** az asztal ikonjáról, és adja meg az alábbi lehetőségeket. A szervezeti egység szűrését az Azure AD Connect telepítésekor is konfigurálhatja. 

![Azure AD Connect további feladatok](media/how-to-connect-fix-default-rules/default8.png)

Kövesse a varázslót, és törölje a szinkronizálni nem kívánt ok-ok törlését.

![Az Azure AD Connect tartomány és szervezeti egység szűrési beállításai](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Illesztési feltétel módosítása
Használja az Azure AD Connect által konfigurált alapértelmezett csatlakozási feltételeket. Az alapértelmezett csatlakozási feltételek módosítása megnehezíti a Microsoft ügyfélszolgálata számára a testreszabások megértését és a termék támogatását.

## <a name="validate-sync-rule"></a>Szinkronizálási szabály érvényesítése
Az újonnan hozzáadott szinkronizálási szabály az előnézeti funkció használatával, a teljes szinkronizálási ciklus futtatása nélkül érvényesítheti. Az Azure AD Connect ben válassza **a Szinkronizálási szolgáltatás**lehetőséget.

![Azure AD Connect, kiemelve a szinkronizálási szolgáltatással](media/how-to-connect-fix-default-rules/default10.png)

Válassza **a Metaverzum-keresés lehetőséget.** Jelölje ki a hatókörobjektumot **személyként,** válassza **a Záradék hozzáadása**lehetőséget, és adja meg a keresési feltételeket. Ezután válassza a **Keresés**lehetőséget, és kattintson duplán az objektumra a keresési eredmények között. Győződjön meg arról, hogy az azure AD Connect ben tárolt adatok naprakészek az adott objektumhoz, az importálás és a szinkronizálás futtatásával az erdőben, mielőtt futtatja ezt a lépést.

![Szinkronizálási szolgáltatáskezelő](media/how-to-connect-fix-default-rules/default11.png)

A **Metaverzum-objektum tulajdonságai párbeszédpanelen**válassza **az Összekötők**lehetőséget, jelölje ki az objektumot a megfelelő összekötőben (erdőben), és válassza a **Tulajdonságok...** lehetőséget.

![Metaverzum-objektum tulajdonságai](media/how-to-connect-fix-default-rules/default12.png)

**Válassza az Előnézet lehetőséget...**

![Összekötő térobjektum tulajdonságai](media/how-to-connect-fix-default-rules/default13a.png)

Az Előnézet ablakban válassza az **Előnézet létrehozása** és **az Attribútumfolyamat importálása** lehetőséget a bal oldali ablaktáblában.

![Előzetes verzió](media/how-to-connect-fix-default-rules/default14.png)
 
Itt figyelje meg, hogy az újonnan hozzáadott szabály `cloudFiltered` fut az objektumon, és az attribútumot true értékre állította.

![Előzetes verzió](media/how-to-connect-fix-default-rules/default15a.png)
 
A módosított szabály és az alapértelmezett szabály összehasonlításához exportálja mindkét szabályt külön-külön szövegfájlként. Ezek a szabályok PowerShell-parancsfájlként kerülnek exportálásra. Összehasonlíthatja őket bármely fájlösszehasonlító eszközzel (például windiff), hogy lássa a változásokat. 
 
Figyelje meg, hogy `msExchMailboxGuid` a módosított szabályban az attribútum a Közvetlen helyett **kifejezéstípusra** **változik.** Az érték **null** és **ExecuteOnce** értékre is változik. Figyelmen kívül hagyhatja az Azonosított és az Elsőbbségi különbségeket. 

![windiff eszköz kimenet](media/how-to-connect-fix-default-rules/default17.png)
 
Ha ki szeretné javítani a szabályokat, hogy azokat visszamódosítsa az alapértelmezett beállításokra, törölje a módosított szabályt, és engedélyezze az alapértelmezett szabályt. Győződjön meg arról, hogy nem veszíti el az elérni kívánt testreszabást. Ha készen áll, **futtassa a Teljes szinkronizálás t.**

## <a name="next-steps"></a>További lépések
- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)



