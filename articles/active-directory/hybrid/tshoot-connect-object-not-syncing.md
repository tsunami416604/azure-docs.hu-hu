---
title: Olyan objektum, amely nem szinkronizálja az Azure Active Directory hibaelhárítása |} A Microsoft Docs
description: Végezzen hibaelhárítást a olyan objektum, amely nem az Azure Active Directoryval szinkronizálja.
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
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416921"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Az Azure Active Directory nem szinkronizálódó objektumok hibaelhárítása

Ha egy objektum nem szinkronizálja a Microsoft Azure Active Directory (Azure AD) a várt módon, több oka lehet. Ha a hiba e-mailt az Azure ad-ből kapott, vagy a hibaüzenet jelenik meg az Azure AD Connect Health, olvassa el [szinkronizálási hibák elhárítása](tshoot-connect-sync-errors.md) helyette. De ha a probléma, ahol az objektum nem az Azure ad-ben van hibaelhárítást, ez a cikk Önnek szól. Hibák keresése a helyszíni összetevők az Azure AD Connect szinkronizálási ismerteti.

>[!IMPORTANT]
>Az Azure ad központi telepítési csatlakozzon 1.1.749.0 verzióját, vagy újabb verziója szükséges, használja a [hibaelhárítási feladat](tshoot-connect-objectsync.md) objektum szinkronizálásával kapcsolatos problémák elhárítása a varázslóban. 

## <a name="synchronization-process"></a>Szinkronizálási folyamat

Mielőtt szinkronizálja problémák felülvizsgáljuk, tekintsük át az Azure AD Connect szinkronizálása folyamatban:

  ![Az Azure AD Connect szinkronizálási folyamat ábrája](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminológia**

* **CS:** Összekötőtér-adatbázisban lévő táblából
* **MV:** Metaverzum, egy adatbázisban lévő táblából

### <a name="synchronization-steps"></a>**Szinkronizálásának lépései**
A szinkronizálja folyamat magában foglalja a következő lépéseket:

1. **Importálás az AD-ből:** Active Directory-objektumok az Active Directory CS való importálás.

2. **Importálás az Azure ad-ben:** Az Azure AD-objektumok az Azure Active Directory Tanúsítványszolgáltatások való importálás.

3. **Szinkronizálás:** Bejövő szinkronizálási szabályok és kimenő szinkronizálási szabályok prioritása nagyobb, kisebb a sorrendjében futnak. A szinkronizálási szabályok megtekintése, nyissa meg a szinkronizálási Szabályszerkesztővel az asztali alkalmazásokétól. A bejövő szinkronizálási szabály életre az adatokat a CS MV. A kimenő szinkronizálási szabály MV származó adatok áthelyezése a CS-SZEL.

4. **Exportálása az ad-hez:** A szinkronizálás után objektumok lesznek exportálva az Active Directory CS, az Active Directory.

5. **Exportálás az Azure ad-hez:** A szinkronizálás után objektumok lesznek exportálva az Azure Active Directory Tanúsítványszolgáltatások a az Azure ad-ben.

## <a name="troubleshooting"></a>Hibaelhárítás

A hibák megkereséséhez tekintse meg a több különböző helyen, a következő sorrendben:

1. A [műveletnaplók](#operations) importálás és a szinkronizálás során a szinkronizálási motor által azonosított hibák kereséséhez.
2. A [összekötőterében](#connector-space-object-properties) hiányzó objektumok és a szinkronizálási hibák megkereséséhez.
3. A [metaverzum](#metaverse-object-properties) adatokkal kapcsolatos problémák kereséséhez.

Indítsa el [Synchronization Service Managert](how-to-connect-sync-service-manager-ui.md) lépések megkezdése előtt.

## <a name="operations"></a>Műveletek
A **műveletek** Synchronization Service Managert lapon látható, ahol meg kell a hibaelhárítás indítása. Ezen a lapon látható a legutóbbi operations eredményeinek. 

![Képernyőkép – a Synchronization Service Managert, kijelölt műveletek lap megjelenítése](./media/tshoot-connect-object-not-syncing/operations.png)  

A felső részén a **műveletek** lapon látható minden Futtatás időrendi sorrendben. Alapértelmezés szerint a műveletek naplófájlformátumának tartja az elmúlt hét nap, de ez a beállítás is módosítható a [scheduler](how-to-connect-sync-feature-scheduler.md). Tekintse meg, minden futtatás, amelyek nem szerepelnek a **sikeres** állapotát. A rendezés a fejlécek kattintva módosíthatja.

A **állapot** oszlop a legfontosabb információt tartalmaz, és bemutatja a legsúlyosabb problémát egy Futtatás esetében. Íme a leggyakoribb állapotok vizsgálat fontossági sorrendben gyors összefoglalását (ahol * azt jelzi, hogy több lehetséges hiba karakterláncok).

| status | Megjegyzés |
| --- | --- |
| stopped-* |A Futtatás nem sikerült befejezni. Ez akkor fordulhat, például, ha a távoli rendszer nem működik, és nem érhető el. |
| stopped-error-limit |Több mint 5000 hibák vannak. A Futtatás hibák nagy száma miatt automatikusan le lett állítva. |
| Befejezett -\*-hibák |A Futtatás befejeződött, de hibák (kevesebb mint 5000) meg kell vizsgálni. |
| Befejezett -\*– figyelmeztetés |A Futtatás befejeződött, de egyes adatokat nem a várt állapotban van. Ha hibákat észlel, ez az üzenet általában csak a jelenség a. Figyelmeztetések nem vizsgálja meg, amíg Ön foglalkoztak hibákat. |
| sikeres |Nincs probléma. |

Amikor kiválaszt egy sort, alsó részén a **műveletek** lap frissül, hogy a Futtatás részleteinek megjelenítéséhez. Bal szélső részén ezen a területen, lehetséges, hogy a listában **lépés #**. Ez a lista csak akkor, ha az erdő több tartományom van, és minden egyes tartományhoz képviseli egy lépést jelenik meg. A tartomány nevét a cím alatt található **partíció**. Alatt a **szinkronizálási statisztika** fejléc annak további információ a feldolgozott módosítások számát. Válassza ki a hivatkozásokra kattintva a módosított objektumok listája. Ha hibákkal objektummal rendelkezik, ezek a hibák jelennek meg a **szinkronizálási hibák** fejléc.

### <a name="errors-on-the-operations-tab"></a>A művelet lapon hibák
Hibásak, Synchronization Service Managert jelenít meg a hiba az objektum és magát a hiba, amely több információt nyújt a hivatkozások formájában is.

![Képernyőkép a Synchronization Service Managert hibák](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Indítsa el a hiba karakterlánca kiválasztásával. (Az előző ábrán a hiba karakterlánca **szinkronizálási szabály-hiba-függvény által indított**.) Először megnyílik az objektum áttekintést. A tényleges hiba megtekintéséhez válasszon **Híváslánc**. A nyomkövetés a hiba hibakeresési szintűre információkat nyújt.

Kattintson a jobb gombbal a **hívás Stack információkat** kattintson **összes kijelölése**, majd válassza ki **másolási**. Ezután másolja a verem, és tekintse meg a hiba a kedvenc szerkesztőjében, például a Jegyzettömbben.

Ha a hiba: a **SyncRulesEngine**, a hívási verem adatokat először az objektum összes attribútum sorolja fel. Görgessen lefelé, amíg meg nem látja a fejléc **InnerException = >**.  

  ![Az a Synchronization Service Managert, hibaadatok InnerException fejléc alatt megjelenítő képernyőkép = >](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
A sor a fejléc jeleníti meg a hiba után. Az előző ábrán a hiba: a Fabrikam létrehozott egyéni szinkronizálási szabály.

Ha a hiba nem nyújt elegendő információt, és tekintse meg az adatokat mozgatná ideje. Válassza ki a kapcsolat és az objektumazonosító, és folytassa a [importált objektum a lemezterület-összekötő](#cs-import).

## <a name="connector-space-object-properties"></a>Összekötőtér-objektum tulajdonságai
Ha a [ **műveletek** ](#operations) lapon látható nincsenek hibák, hajtsa végre a az összekötőtér objektuma az Active Directoryból az Azure ad-hez a metaverzumba. Ezen az elérési úton keresse meg a probléma esetén.

### <a name="searching-for-an-object-in-the-cs"></a>A CS objektum keresése

Válassza ki a Synchronization Service Managert, **összekötők**, az Active Directory-összekötőt, válassza ki és **keresési Összekötőterét**.

Az a **hatókör** jelölje ki **RDN** Ha szeretne keresni a CN-attribútum, vagy válasszon ki **DN-t vagy a forráshorgony** Ha szeretne keresni a **distinguishedName**  attribútum. Adjon meg egy értéket, és válassza ki **keresési**. 
 
![Képernyőkép egy összekötőtér keresése](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Ha nem találja az objektum keresi, akkor előfordulhat, hogy vannak szűrve az [tartományalapú szűrés](how-to-connect-sync-configure-filtering.md#domain-based-filtering) vagy [szervezeti egység szerinti szűrés](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Győződjön meg arról, hogy a szűrés van konfigurálva, a várt módon, hogy olvassa el a [Azure AD Connect szinkronizálása: A szűrés konfigurálása](how-to-connect-sync-configure-filtering.md).

Egy másik hasznos keresési elvégezhet az Azure AD-összekötő kiválasztásával. Az a **hatókör** jelölje ki **függőben lévő importálás**, majd válassza ki a **Hozzáadás** jelölőnégyzetet. A keresés lehetővé teszi az összes szinkronizált objektumokat, amelyeket nem lehet egy helyszíni objektumhoz társított Azure AD-ben.  

![Egy összekötőtér keresése az sp_createorphan képernyőképe](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Ezek az objektumok egy másik szinkronizálási motor vagy egy másik szűrési konfigurációval a Szinkronizáló vezérlő létrejött. Ezek az objektumok árva felügyelete is megszűnik. Tekintse át a listában, és fontolja meg a használatával ezek az objektumok eltávolítását a [az Azure AD PowerShell](https://aka.ms/aadposh) parancsmagok.

### <a name="cs-import"></a>CS importálása
Amikor megnyit egy CS objektumot, nincsenek több lapok tetején. A **importálása** lapon látható, amely az importálás után az adatok.  

![Az Összekötőtér-objektum tulajdonságai ablakban a kiválasztott importálás lap képernyőképe](./media/tshoot-connect-object-not-syncing/csobject.png)    

A **régi érték** az oszlopban látható a jelenleg tárolja a csatlakozás, és a **új érték** az oszlopban látható, mi a forrásrendszerben érkezett, és még nem alkalmazták. Ha hiba történt az objektum, a módosítások nem kerülnek feldolgozásra.

A **szinkronizálási hiba** lap akkor jelenik meg a **Összekötőtér-objektum tulajdonságai** ablak csak akkor, ha az objektum egy probléma van. További információkért tekintse át a hogyan [a szinkronizálási hibák elhárítása a **műveletek** lapon](#errors-on-the-operations-tab).

![Összekötőtér-objektum tulajdonságai ablak szinkronizálási hiba lapjának képernyőképe](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS leszármaztatási
A **Leszármaztatási** lapján a **Összekötőtér-objektum tulajdonságai** ablakban jelennek meg, hogyan viszonyul az összekötőtér objektuma a metaverzum-objektum. Láthatja, hogy az összekötő utolsó importálás után a változás a csatlakoztatott rendszer, és mely szabályok érvényesek, töltse ki az adatokat a metaverzumban.  

![Az Összekötőtér-objektum tulajdonságai ablakban a Leszármaztatási lapot ábrázoló képernyőfelvétel](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Az előző ábrán a **művelet** az oszlopban látható a művelettel egy bejövő szinkronizálási szabály **kiépítése**. Azt jelzi, hogy mindaddig, amíg a összekötőtér objektuma jelen, a metaverzum-objektum marad. Ha a szinkronizálási szabályok listáját egy kimenő szinkronizálási szabály inkább jeleníti meg egy **kiépítése** művelet, ezt az objektumot törölték a metaverzum-objektum törlése.  

![Az Összekötőtér-objektum tulajdonságai ablakban Leszármaztatási lapján leszármaztatási ablak képernyőképe](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Az előző ábrán is látható a **PasswordSync** oszlopot, amely a bejövő összekötőterében közreműködhet változik a jelszót, mert egy szinkronizálási szabály értéke **igaz**. Ezt a jelszót az Azure AD a kimenő szabály keresztül zajlik.

Az a **Leszármaztatási** lapján érheti el a metaverzumba kiválasztásával [ **Metaverzumbeli objektumának tulajdonságait**](#mv-attributes).

### <a name="preview"></a>Előzetes verzió
A bal alsó sarkában található a **Összekötőtér-objektum tulajdonságai** ablak a **előzetes** gombra. Válassza ki az erre a gombra kattintva nyissa meg a **előzetes** oldal, ahol egyetlen objektum lehet szinkronizálni. Ez az oldal akkor hasznos, ha néhány egyéni szinkronizálási szabály hibaelhárítást, és szeretné látni a változás hatását egy önálló objektumon. Kiválaszthat egy **teljes szinkronizálás** vagy egy **különbözeti szinkronizálási**. Lehetőség kiválasztásával **készítése előzetes**, ami a memória csak tartja a módosítást. Válassza ki vagy **véglegesítése előzetes**, amely frissíti a metaverzumba, és előkészíti az összes változtatás cél összekötőterek.  

![Indítsa el az előzetes verzióban kiválasztott a villámnézeti lap képernyőképe](./media/tshoot-connect-object-not-syncing/preview.png)  

Az előzetes verzióban érhető el vizsgálja meg az objektumot, és melyik szabály egy adott attribútum flow alkalmazás megtekintéséhez.  

![Az előzetes verziójú szolgáltatásainak weblapján importálási Attribútumfolyam bemutató képernyőkép](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Napló
Mellett a **előzetes** gombra, válassza a **Log** gombra kattintva nyissa meg a **Log** lapot. Itt láthatja a jelszó-szinkronizálás állapota és előzményei. További információkért lásd: [Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás hibaelhárítása](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>A Metaverzumbeli objektum tulajdonságai
Általában célszerűbb a forrás-összekötőtérbe keresés indításához. De a Keresés a metaverzumban is elindíthatja.

### <a name="searching-for-an-object-in-the-mv"></a>A MV-objektum keresése
Válassza ki a Synchronization Service Managert, **keresés a Metaverzumban**, ahogy az alábbi ábrán. Hozzon létre egy lekérdezést, amely megkeresi a felhasználó ismeri. Keresse meg a gyakori attribútumok, például **accountName** (**sAMAccountName**) és **userPrincipalName**. További információkért lásd: [keresés a Metaverzumban szinkronizálása a Service Manager](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Képernyőkép a Synchronization Service Managert, a kiválasztott keresés a Metaverzumban lap](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Az a **keresési eredmények** ablakában kattintson az objektumra.

Ha az objektum nem talált, akkor nem még érte el a metaverzumba. Keresse meg az objektumot az Active Directory továbbra is [összekötőterében](#connector-space-object-properties). Ha az objektum az Active Directory összekötőterében talál, lehetséges, hogy a szinkronizálási hiba, amely blokkolja az objektumot a metaverzumba jelenik meg, vagy esetleg alkalmazott szinkronizálási szabály hatókörszűrőt.

### <a name="object-not-found-in-the-mv"></a>Az objektum nem található az a MV
Ha az objektum az Active Directory CS van, de nem szerepel a MV, egy hatókörszűrőt van alkalmazva. Tekintse meg a Hatókörszűrő, nyissa meg az asztali alkalmazás menü, és válassza ki **szinkronizálási Szabályszerkesztővel**. Az objektumra vonatkozó szabályok szűrése az alábbi szűrő módosításával.

  ![Képernyőfelvétel a szinkronizálási Szabályszerkesztővel, egy bejövő szinkronizálási szabály keresés megjelenítése](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Minden szabály megtekintéséhez a fenti listában, és ellenőrizze a **Scoping szűrő**. A következő hatókörszűrőt Ha a **isCriticalSystemObject** érték null, vagy hamis vagy üres, hatókörében van.

  ![Képernyőkép egy hatókörszűrőt egy bejövő szinkronizálási szabály keresés](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Nyissa meg a [CS importálás](#cs-import) attribútum lista, és ellenőrizze, melyik szűrő blokkolja az objektum a MV való áthelyezését. A **Összekötőterében** attribútumlista csak nem null értékű, és nem üres attribútumok jelennek meg. Például ha **isCriticalSystemObject** nem jelenik meg a listában, ez az attribútum értéke null vagy üres.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Az objektum nem található az Azure Active Directory tanúsítványszolgáltatások
Ha az objektum nem szerepel az Azure AD-összekötő-térben, de a MV szerepel, tekintse meg a megfelelő összekötőtérben, a kimenő szabályok Hatókörszűrő, és ismerje meg, ha az objektum szűri, mert a [MV attribútumok](#mv-attributes)nem felelnek meg a feltételeket.

Tekintse meg a kimenő Hatókörszűrő, jelölje be az objektum a megfelelő szabályok az alábbi szűrő módosításával. Megtekintheti az egyes szabályokat, és tekintse meg a megfelelő [MV attribútum](#mv-attributes) értéket.

  ![Képernyőfelvétel a szinkronizálási Szabályszerkesztővel kimenő szinkronizálási szabály keresés](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-attribútumok
Az a **attribútumok** lapon megtekintheti az értékeket, és mely összekötők viszonyított őket.  

![Képernyőfelvétel a Metaverzum-objektum tulajdonságai ablakban, a kiválasztott attribútumok lap](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Ha egy objektum nem szinkronizálja, érdeklődjön attribútum állapotok metaverzumban található a következő kérdéseket:
- Az attribútum **cloudFiltered** jelen, és állítsa **igaz**? Ha igen, azt a ismertetett lépések szerint szűrve [attribútum szerinti szűrés](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Az attribútum **sourceAnchor** jelen? Ha nem rendelkezik egy fiók-erőforrás szolgának? Ha egy objektum hivatkozott postafiókkal azonosítottak (az attribútum **msExchRecipientTypeDetails** értéke **2**), a **sourceAnchor** van az erdő százalékaránya egy engedélyezve van az Active Directory-fiókot. Ellenőrizze, hogy a fő fiók importált és megfelelően szinkronizálva. A fő fiók között szerepelnie kell a [összekötők](#mv-connectors) objektum számára.

### <a name="mv-connectors"></a>MV-összekötők
A **összekötők** lapon látható minden összekötő szóközt, amely rendelkezik az objektum reprezentációját. 
 
![Képernyőfelvétel a Metaverzum-objektum tulajdonságai ablakról, amelyen az összekötők lapon kiválasztva](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Az összekötő kell rendelkeznie:

- Minden egyes Active Directory-erdőben, a felhasználó jelennek meg. E ábrázolás tartalmazhatnak **foreignSecurityPrincipals** és **forduljon** objektumokat.
- Az Azure AD-összekötő.

Ha az összekötő Azure ad-hez használt hiányoznak, tekintse át a szakasz az [MV attribútumok](#mv-attributes) ellenőrzése az Azure ad-ben való üzembe helyezést, a feltételeknek.

Az a **összekötők** lapot is megnyithatja a [összekötőtér objektuma](#connector-space-object-properties). Jelöljön ki egy sort, és kattintson a **tulajdonságok**.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md).
- Tudjon meg többet [hibrid identitás](whatis-hybrid-identity.md).
