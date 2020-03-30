---
title: Az Azure Active Directoryval nem szinkronizált objektumok – hibaelhárítása | Microsoft Dokumentumok"
description: Az Azure Active Directoryval nem szinkronizált objektumok – problémahárítás elhárítása.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253532"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Az Azure Active Directoryval nem szinkronizált objektumok – problémamegoldás

Ha egy objektum nem a várt módon szinkronizálja a Microsoft Azure Active Directoryt (Azure AD), annak több oka is lehet. Ha hibaüzenetet kapott az Azure AD-től, vagy az Azure AD Connect Health szolgáltatásban látja a hibát, olvassa el a [Hibaelhárítási hibák](tshoot-connect-sync-errors.md) szinkronizálás közbeni hibáit. De ha olyan problémát hárít el, amelyben az objektum nem az Azure AD-ben található, ez a cikk az Ön számára. Ismerteti, hogyan találhat hibákat a helyszíni összetevő Azure AD Connect szinkronizálás.

>[!IMPORTANT]
>Az Azure AD Connect 1.1.749.0-s vagy újabb verziójú központi telepítéséhez használja a varázsló [hibaelhárítási feladatát](tshoot-connect-objectsync.md) az objektumszinkronizálási problémák elhárításához. 

## <a name="synchronization-process"></a>Szinkronizálási folyamat

Mielőtt megvizsgálnánk a szinkronizálási problémákat, ismerjük meg az Azure AD Connect szinkronizálási folyamatát:

  ![Az Azure AD Connect szinkronizálási folyamatának diagramja](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminológia**

* **CS:** Összekötő tér, adatbázis táblája
* **MV:** Metaverse, egy tábla egy adatbázisban

### <a name="synchronization-steps"></a>**Szinkronizálási lépések**
A szinkronizálási folyamat a következő lépéseket foglalja magában:

1. **Importálás AD-ből:** Az Active Directory-objektumok az Active Directory CS-be kerülnek.

2. **Importálás az Azure AD-ből:** Az Azure AD-objektumok az Azure AD CS-be kerülnek.

3. **Szinkronizálás:** A bejövő szinkronizálási szabályok és a kimenő szinkronizálási szabályok a prioritási sorrendben futnak, alacsonyabbtól a magasabbig. A szinkronizálási szabályok megtekintéséhez nyissa meg az asztali alkalmazások szinkronizálási szabályok szerkesztőjét. A bejövő szinkronizálási szabályok adatokat hoznak a CS-ről az MV-re. A kimenő szinkronizálási szabályok áthelyezik az adatokat az MV-ről a CS-re.

4. **Exportálás AD-be:** A szinkronizálás után az objektumok exportálása az Active Directory CS könyvtárból az Active Directoryba történik.

5. **Exportálás az Azure AD szolgáltatásba:** Szinkronizálás után az objektumok exportálása az Azure AD CS-ből az Azure AD-be.

## <a name="troubleshooting"></a>Hibaelhárítás

A hibák megkereséséhez tekintsen meg néhány különböző helyet a következő sorrendben:

1. A [művelet naplói](#operations) a szinkronizálási motor által az importálás és szinkronizálás során azonosított hibák keresésére.
2. A [connector space](#connector-space-object-properties) hiányzó objektumok és szinkronizálási hibák keresésére szolgál.
3. Az adatokkal kapcsolatos problémák at keresendő [metaverzum.](#metaverse-object-properties)

A lépések megkezdése előtt indítsa el [a Szinkronizálási szolgáltatáskezelőt.](how-to-connect-sync-service-manager-ui.md)

## <a name="operations"></a>Műveletek
A Szinkronizálási szolgáltatáskezelő **Műveletek** lapján kell elindítania a hibaelhárítást. Ez a lap a legutóbbi műveletek eredményeit jeleníti meg. 

![Képernyőkép a Szinkronizálási szolgáltatáskezelőről, amelyen az Műveletek lap kijelölt](./media/tshoot-connect-object-not-syncing/operations.png)  

**Az Műveletek** lap felső fele időrendi sorrendben jeleníti meg az összes futtatást. Alapértelmezés szerint a műveleti napló információkat tartalmaz az elmúlt hét napról, de ez a beállítás módosítható az [ütemezővel.](how-to-connect-sync-feature-scheduler.md) Keressen olyan futtatást, amely nem mutat **sikeres** állapotot. A rendezést a fejlécek kattintásával módosíthatja.

Az **Állapot** oszlop tartalmazza a legfontosabb információkat, és megmutatja a futtatás legsúlyosabb problémáját. Az alábbiakban a leggyakoribb állapotok gyors összegzését olvashatja a vizsgálati prioritás sorrendjében (ahol a * több lehetséges hibakarakterláncot jelöl).

| status | Megjegyzés |
| --- | --- |
| leállítva-* |A futás nem fejeződhet be. Ez például akkor fordulhat elő, ha a távoli rendszer nem áll meg, és nem lehet kapcsolatba lépni vele. |
| leállított-hiba-limit |Több mint 5000 hiba van. A futtatás a nagy számú hiba miatt automatikusan leállt. |
| befejezett-\*-hibák |A futtatás befejeződött, de vannak hibák (kevesebb, mint 5000), amelyeket meg kell vizsgálni. |
| befejeződött-\*-figyelmeztetések |A futtatás befejeződött, de néhány adat nem a várt állapotban van. Ha hibákat észlel, ez az üzenet általában csak tünet. Ne vizsgálja meg a figyelmeztetéseket, amíg nem hárította el a hibákat. |
| sikeres |Semmi probléma. |

Amikor kijelöl egy sort, a **Műveletek** lap alja frissül, hogy megjelenjen a futtatás részletei. A terület bal szélén lehet egy **Step #** című lista. Ez a lista csak akkor jelenik meg, ha az erdőben több tartomány van, és minden tartományt egy lépés képvisel. A tartománynév a **Partition**cím alatt található. A **Szinkronizálási statisztika** fejlécalatt további információt talál a feldolgozott módosítások számáról. A hivatkozások kijelölésével megjelenik a módosított objektumok listája. Ha hibás objektumokat észlel, ezek a hibák a **Szinkronizálási hibák** fejléc alatt jelennek meg.

### <a name="errors-on-the-operations-tab"></a>Hibák a Műveletek lapon
Ha hibákat észlel, a Szinkronizálási szolgáltatáskezelő a hibás objektumot és magát a hibát is hivatkozásként jeleníti meg, amelyek további információt nyújtanak.

![Képernyőkép a Szinkronizálási szolgáltatáskezelő ben található hibákról](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Kezdje a hibakarakterlánc kiválasztásával. (Az előző ábrán a hibakarakterlánc **szinkronizálási szabály-hiba-függvény által kiváltott**.) Először az objektum áttekintése jelenik meg. A tényleges hiba megtekintéséhez válassza a **Veremkövetés lehetőséget.** Ez a nyomkövetés hibakeresési szintű információkat tartalmaz a hibáról.

Kattintson a jobb gombbal a **Hívásverem adatai mezőre,** válassza **az Összes kijelölése**parancsot, és válassza a **Másolás parancsot.** Ezután másolja a halom, és nézd meg a hibát a kedvenc szerkesztő, mint például a Jegyzettömb.

Ha a hiba a **SyncRulesEngine**programból származik, a hívásverem adatai először az objektum összes attribútumát listázza. Görgessen lefelé, amíg meg nem jelenik az **InnerException =>**.  

  ![Képernyőkép a Szinkronizálási szolgáltatáskezelőről, amely az InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
A címsor utáni sor a hibát mutatja. Az előző ábrán a hiba a Fabrikam által létrehozott egyéni szinkronizálási szabályból származik.

Ha a hiba nem ad elegendő információt, itt az ideje, hogy nézd meg az adatokat is. Jelölje ki az objektumazonosítóval rendelkező hivatkozást, és folytassa az [importált összekötőterület hibaelhárítását.](#cs-import)

## <a name="connector-space-object-properties"></a>Összekötőtér-objektum tulajdonságai
Ha az [**Operations**](#operations) lapon nem jelennek meg hibák, kövesse az összekötő tér objektum az Active Directorytól a metaverzum az Azure AD. Ezen az úton meg kell találnia, hogy hol van a probléma.

### <a name="searching-for-an-object-in-the-cs"></a>Objektum keresése a CS-ben

A Szinkronizálási szolgáltatáskezelőben válassza **az Összekötők**lehetőséget, jelölje ki az Active Directory-összekötőt, és válassza a **Keresési összekötő tér ben**lehetőséget.

A **Hatókör mezőben** válassza az **RDN** lehetőséget, ha a CN attribútumban szeretne keresni, vagy válassza a **DN vagy a horgony** lehetőséget, ha a **distinguishedName** attribútumban szeretne keresni. Írjon be egy értéket, és válassza a **Keresés**lehetőséget. 
 
![Képernyőkép egy összekötő térkereséséről](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Ha nem találja a keresett objektumot, akkor lehet, hogy [tartományalapú szűréssel](how-to-connect-sync-configure-filtering.md#domain-based-filtering) vagy [szervezetiegység-alapú szűréssel szűrte.](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) Annak ellenőrzéséhez, hogy a szűrés a várt módon van-e konfigurálva, olvassa el az [Azure AD Connect szinkronizálási: Szűrés konfigurálása](how-to-connect-sync-configure-filtering.md)című olvasnivalót.

Az Azure AD-összekötő kiválasztásával egy másik hasznos keresést is végrehajthat. A **Hatókör** mezőben válassza a **Függőben lévő importálás**lehetőséget, majd jelölje be a **Hozzáadás** jelölőnégyzetet. Ez a keresés az Azure AD összes szinkronizált objektumot biztosítja, amelyek nem társíthatók a helyszíni objektumhoz.  

![Árvák képernyőképe az összekötőtér-keresésben](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Ezeket az objektumokat egy másik szinkronizálási motor vagy egy másik szűrési konfigurációval rendelkező szinkronizálási motor hozta létre. Ezek az árva objektumok már nem kezelhetők. Tekintse át ezt a listát, és fontolja meg az objektumok eltávolítását az [Azure AD PowerShell-parancsmagok](https://aka.ms/aadposh) használatával.

### <a name="cs-import"></a>CS-importálás
Amikor megnyit egy CS-objektumot, a lap tetején több lap található. Az **Importálás** lapon láthatók az importálás után előkészített adatok.  

![Képernyőkép az Összekötő térobjektum tulajdonságai ablakról, amelyen az Importálás lap van kijelölve](./media/tshoot-connect-object-not-syncing/csobject.png)    

A **Régi érték** oszlop ban látható, hogy jelenleg mi van tárolva a Csatlakozás alkalmazásban, az Új érték oszlop **pedig** azt, hogy mi érkezett a forrásrendszertől, és még nincs alkalmazva. Ha hiba történik az objektumon, a rendszer nem dolgozza fel a módosításokat.

A **Szinkronizálási hiba** lap csak akkor látható az **Összekötő térobjektum tulajdonságai** ablakban, ha probléma van az objektummal. További információt a Műveletek lapon található [szinkronizálási hibák **Operations** elhárításáról](#errors-on-the-operations-tab)talál.

![Képernyőkép az Összekötő térobjektum tulajdonságai ablak Szinkronizálási hiba lapjáról](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS családvonal
Az **Összekötő térobjektum tulajdonságai** ablak **Lineage (Lineage)** lapján látható, hogy az összekötő térobjektum hogyan kapcsolódik a metaverzum-objektumhoz. Láthatja, hogy az összekötő mikor importált utoljára egy módosítást a csatlakoztatott rendszerből, és mely szabályok vonatkoznak a metaverzumban lévő adatok feltöltésére.  

![Az Összekötő térobjektum tulajdonságai ablak Vonal lapját ábrázoló képernyőkép](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Az előző **ábrán** a Művelet oszlop egy bejövő szinkronizálási szabályt jelenít meg a **kiépítés**művelettel. Ez azt jelzi, hogy amíg ez az összekötő térobjektum jelen van, a metaverzum objektum megmarad. Ha a szinkronizálási szabályok listája ehelyett egy kimenő szinkronizálási szabályt jelenít meg egy **létesítési** művelettel, akkor ez az objektum törlődik a metaverzum-objektum törlésekor.  

![Képernyőkép az Összekötő térobjektum tulajdonságai ablak Vonal lapjának vonalablakáról](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Az előző ábrán azt is láthatja a **PasswordSync** oszlopban, hogy a bejövő összekötő terület adhat változásokat a jelszót, mivel egy szinkronizálási szabály értéke **Igaz**. Ezt a jelszót a kimenő szabály on keresztül küldi el az Azure AD.This password is sent to Azure AD through the outbound rule.

A **Lineage** lapon a [**Metaverzum-objektum tulajdonságai**](#mv-attributes)lehetőséget választva érheti el a metaverzumot.

### <a name="preview"></a>Előzetes verzió
Az **Összekötő térobjektum tulajdonságai** ablak bal alsó sarkában található az **Előnézet** gomb. Ezzel a gombbal megnyithatja az **Előnézet** lapot, ahol egyetlen objektumot szinkronizálhat. Ez a lap akkor hasznos, ha néhány egyéni szinkronizálási szabályt hibaelhárít, és szeretné látni a módosítás hatását egyetlen objektumra. Kiválaszthatja a **Teljes szinkronizálást** vagy a **Delta szinkronizálást.** Az Előnézet létrehozása lehetőséget is **választhatja,** amely csak a memóriában tartja a módosítást. Vagy válassza **a Véglegesítés előnézete**lehetőséget, amely frissíti a metaverzumot, és a célösszekötő terek összes módosítását leadja.  

![Képernyőkép az Előnézet lapról, amelyen a Start előnézet van kiválasztva](./media/tshoot-connect-object-not-syncing/preview.png)  

Az előnézetben megvizsgálhatja az objektumot, és megtekintheti, hogy melyik szabály vonatkozik egy adott attribútumfolyamatra.  

![Képernyőkép az Előnézet lapról, amelyen az Attribútumimportálási folyamat látható](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Napló
Az **Előnézet** gomb mellett kattintson a **Napló** gombra a **Napló** lap megnyitásához. Itt láthatja a jelszó szinkronizálási állapotát és előzményeit. További információt a [Jelszókivonat-szinkronizálás hibaelhárítása az Azure AD Connect szinkronizálásával](tshoot-connect-password-hash-synchronization.md)című témakörben talál.

## <a name="metaverse-object-properties"></a>Metaverzum objektum tulajdonságai
Általában jobb, ha a forrásból kezdi el a keresést az Active Directory-összekötő területéről. De elkezdheti a keresést a metaverzumból is.

### <a name="searching-for-an-object-in-the-mv"></a>Objektum keresése az MV-ben
A Szinkronizálási szolgáltatáskezelőben válassza a **Metaverzum-keresés**lehetőséget, az alábbi ábrán is. Hozzon létre egy olyan lekérdezést, amelyről tudja, hogy megtalálja a felhasználót. Keresse meg a közös attribútumokat, például **az accountName** **(sAMAccountName**) és **a userPrincipalName tulajdonságot.** További információ: [Sync Service Manager Metaverse search](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Képernyőkép a Szinkronizálási szolgáltatáskezelőről, a Metaverzum-keresés lap kijelölésével](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

A **Keresési eredmények ablakban** kattintson az objektumra.

Ha nem találja az objektumot, még nem érte el a metaverzumot. Folytassa az objektum keresését az Active Directory [összekötő területén](#connector-space-object-properties). Ha az objektumot az Active Directory-összekötő térben találja, előfordulhat, hogy szinkronizálási hiba akadályozza az objektum metaverzumhoz való eljövetelét, vagy egy szinkronizálási szabály hatókörszűrője alkalmazható.

### <a name="object-not-found-in-the-mv"></a>Az objektum nem található az MV-ben
Ha az objektum az Active Directory CS-ben található, de nincs jelen az MV-ben, a rendszer hatókörszűrőt alkalmaz. A hatókörszűrő t, nyissa meg az asztali alkalmazás menüjét, és válassza a **Szinkronizálási szabályok szerkesztője**parancsot. Az alábbi szűrő módosításával szűrheti az objektumra vonatkozó szabályokat.

  ![Képernyőkép: Szinkronizálási szabályok szerkesztője bejövő szinkronizálási szabályok keresésével](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Tekintse meg a lista egyes szabályait felülről, és ellenőrizze a **Hatókör szűrőt**. A következő hatókörszűrőben, ha az **isCriticalSystemObject** értéke null vagy HAMIS vagy üres, akkor hatókörben van.

  ![Képernyőkép: hatókörszűrő bejövő szinkronizálási szabálykeresésben](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Lépjen a [CS import](#cs-import) attribútumlistára, és ellenőrizze, hogy melyik szűrő blokkolja az objektum ot az MV-re való áttéréshez. Az **Összekötő tér** attribútumlista csak nem null értékű és nem üres attribútumokat jelenít meg. Ha például **az isCriticalSystemObject** nem jelenik meg a listában, az attribútum értéke null vagy üres.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Az objektum nem található az Azure AD CS-ben
Ha az objektum nincs jelen az Azure AD összekötő területén, de az MV-ben is megtalálható, tekintse meg a megfelelő összekötő terület kimenő szabályainak hatókör-szűrőjét, és derítse ki, hogy az objektum ki van-e szűrve, mert az [MV-attribútumok](#mv-attributes) nem felelnek meg a feltételeknek.

A kimenő hatókörszűrő t, válassza ki az objektumra vonatkozó szabályokat az alábbi szűrő módosításával. Tekintse meg az egyes szabályokat, és tekintse meg a megfelelő [MV attribútumértéket.](#mv-attributes)

  ![Képernyőkép egy kimenő szinkronizálási szabályok kereséséről a Szinkronizálási szabályok szerkesztőjében](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-attribútumok
Az **Attribútumok** lapon láthatja az értékeket, és hogy mely összekötők járultak hozzájuk.  

![Képernyőkép a Metaverzum-objektum tulajdonságai ablakról, az Attribútumok lap kijelölésével](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Ha egy objektum nem szinkronizálódik, tegye fel a következő kérdéseket a metaverzum attribútumállapotával kapcsolatban:
- A **cloudFiltered** attribútum jelen van, és **Igaz**értékre van állítva? Ha ez így van, akkor az attribútumalapú szűrés lépései szerint lett [szűrve.](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)
- Az attribútum **sourceAnchor** jelen van? Ha nem, van-e fiókerőforrás-erdőtopológiája? Ha egy objektum csatolt postaládaként van azonosítva (az **msExchRecipientTypeDetails** attribútum **értéke 2),** a **sourceAnchor-t** az engedélyezett Active Directory-fiókkal rendelkező erdő adják hozzá. Ellenőrizze, hogy a főfiók importálása és szinkronizálása megfelelő-e. A főfióknak szerepelnie kell az objektum [összekötői](#mv-connectors) között.

### <a name="mv-connectors"></a>MV csatlakozók
Az **Összekötők** lapon látható az objektumot reprezentativitását ábrázoló összes összekötő hely. 
 
![Képernyőkép a Metaverzum-objektum tulajdonságai ablakról, az Összekötők lap kijelölésével](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Rendelkeznie kell egy összekötő:

- Minden olyan Active Directory-erdő, amelyben a felhasználó képviselteti magát. Ez a képviselet tartalmazhat **foreignSecurityPrincipals** és **contact** objektumokat.
- Összekötő az Azure AD-ben.

Ha hiányzik az azure AD-összekötő, tekintse át az [MV-attribútumok](#mv-attributes) szakaszát az Azure AD-be való kiépítés feltételeinek ellenőrzéséhez.

Az **Összekötők** lapon az összekötő [térobjektumra](#connector-space-object-properties)is léphet. Jelöljön ki egy sort, és kattintson **a Tulajdonságok gombra.**

## <a name="next-steps"></a>További lépések
- További információ az [Azure AD Connect szinkronizálásáról.](how-to-connect-sync-whatis.md)
- További információ a [hibrid identitásról.](whatis-hybrid-identity.md)
