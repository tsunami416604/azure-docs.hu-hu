---
title: Az Azure ad-val nem szinkronizálódó objektumok hibaelhárítása |} A Microsoft Docs
description: Az objektum nem szinkronizálja az Azure AD okozó hibák elhárításához.
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
ms.openlocfilehash: d10b8760409d5deb0828d15e8c0daf50853a9624
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158264"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Az Azure ad-val nem szinkronizálódó objektumok hibaelhárítása

Ha egy objektum nem szinkronizálja az Azure AD a várt módon, majd azok miatt számos oka lehet. Ha a hiba e-mailt az Azure ad-ből kapott, vagy a hibaüzenet jelenik meg az Azure AD Connect Health, majd elolvashatják [exportálási hibák elhárítása](tshoot-connect-sync-errors.md) helyette. De ha a probléma, ahol az objektum nem az Azure ad-ben van hibaelhárítást, majd a témakör Önnek szól. Bemutatja, hogyan lehet a helyszíni összetevő az Azure AD Connect szinkronizálási hibák megkereséséhez.

>[!IMPORTANT]
>Az Azure Active Directory (AAD) központi telepítés csatlakozzon 1.1.749.0 verzióját, vagy újabb verziója szükséges, használja a [hibaelhárítási feladat](tshoot-connect-objectsync.md) objektumok szinkronizálási problémáinak hibaelhárítása a varázslóban. 

A hibák megkereséséhez fog tekintse meg több különböző helyen, a következő sorrendben:

1. A [műveletnaplók](#operations) importálás és a szinkronizálás során a szinkronizálási motor által azonosított hibák kereséséhez.
2. A [összekötőterében](#connector-space-object-properties) hiányzó objektumok és a szinkronizálási hibák kereséséhez.
3. A [metaverzum](#metaverse-object-properties) adatokkal kapcsolatos problémák kereséséhez.

Indítsa el [Synchronization Service Managert](how-to-connect-sync-service-manager-ui.md) lépések megkezdése előtt.

## <a name="operations"></a>Műveletek
A Műveletek lap a Synchronization Service Managert el, ahol el kell indítania az a hibaelhárítást. A műveletek lapon láthatók a legutóbbi operations eredményeinek.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/operations.png)  

Felső részén látható minden Futtatás időrendi sorrendben. Alapértelmezés szerint a műveletek naplófájlformátumának tartja az elmúlt hét nap, de ez a beállítás is módosítható a [scheduler](how-to-connect-sync-feature-scheduler.md). Kívánt összes futtatás, amelyek nem szerepelnek a sikeres állapotú. A rendezés a fejlécek kattintva módosíthatja.

A **állapot** oszlop a legfontosabb információt, és bemutatja a legsúlyosabb problémát egy Futtatás esetében. Íme a leggyakoribb állapotok a prioritásuk szerinti sorrendben kell vizsgálatára gyors összefoglalását (ahol * több lehetséges hiba karakterláncok jelzi).

| status | Megjegyzés |
| --- | --- |
| stopped-* |A Futtatás nem sikerült végrehajtani. Ha például a távoli rendszer nem működik, és nem érhető el. |
| stopped-error-limit |Több mint 5000 hibák vannak. A Futtatás hibák nagy száma miatt automatikusan le lett állítva. |
| Befejezett -\*-hibák |A Futtatás befejeződött, de hibák (kevesebb mint 5000) meg kell vizsgálni. |
| Befejezett -\*– figyelmeztetés |A Futtatás befejeződött, de egyes adatokat nem szerepel a várt állapota. Ha hibákat észlel, majd ezt az üzenetet általában a csak a hiba. Hibák elhárítása, amíg nem kell vizsgálni – figyelmeztetések. |
| sikeres |Nincs probléma. |

Amikor kiválaszt egy sort, az alsó frissíti az adott Futtatás részleteinek megjelenítéséhez. A bal szélén az alsó, lehetséges, hogy egy lista üzenettel **lépés #**. Ez a lista csak akkor jelenik meg, ha több tartományt az erdőben, ahol minden egyes tartományhoz egy lépés képviseli. A tartomány nevét a cím alatt található **partíció**. A **szinkronizálási statisztika**, talál további információt a feldolgozott módosítások számát. Kattinthat a hivatkozásokra kattintva a módosított objektumok listája. Ha hibákkal objektummal rendelkezik, ezek a hibák jelennek meg **szinkronizálási hibák**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Műveleti lapon kapcsolatos hibák elhárítása
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Ha hibákat, mindkét hiba és a hiba, maga az objektum olyan hivatkozásokat, amelyek további információval.

Indítsa el a hiba karakterlánca kattintva (**szinkronizálási szabály-hiba-függvény által indított** a képen látható). Először megnyílik az objektum áttekintést. A tényleges hiba megtekintéséhez kattintson a gombra **Híváslánc**. A nyomkövetés a hiba hibakeresési szintű információkat nyújt.

A jobb gombbal a **hívásverem-információk** válassza **válassza ki az összes**, és **másolási**. Ezután másolja ki a verem, és tekintse meg a hiba a kedvenc szerkesztőjében, például a Jegyzettömbben.

* Ha a hiba: a **SyncRulesEngine**, akkor a hívási verem adatokat először rendelkezik az objektum attribútumok listáját. Görgessen lefelé, amíg meg nem látja a fejléc **InnerException = >**.  
  ![Sync Service Manager](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)  
  A sor után a hibát jeleníti meg. A fenti képen a hiba: a létrehozott egyéni szinkronizálási szabály Fabrikam.

Ha magát a hibát nem nyújt elegendő információt, majd, és tekintse meg az adatokat mozgatná idő. Kattintson a hivatkozásra a objektumazonosítóval rendelkező, és folytassa a [importált objektum a lemezterület-összekötő](#cs-import).

## <a name="connector-space-object-properties"></a>Összekötőtér-objektum tulajdonságai
Ha nem rendelkezik minden olyan hibákat talált a [operations](#operations) lapra, majd a következő lépés az, hogy hajtsa végre az összekötőtér objektuma az Active Directoryból, a metaverzumba, és az Azure ad-hez. Ezen az elérési úton keresse meg a probléma esetén.

### <a name="search-for-an-object-in-the-cs"></a>Az objektum a CS lévő keresése

A **Synchronization Service Managert**, kattintson a **összekötők**, válassza ki az Active Directory-összekötő és **keresési Összekötőterét**.

A **hatókör**válassza **RDN** (Ha meg szeretne keresni a CN-attribútum) vagy **DN-t vagy a forráshorgony** (Ha meg szeretne keresni a distinguishedName attribútum). Adjon meg egy értéket, és kattintson a **keresési**.  
![Összekötőtér keresése](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Az objektum nem találja a keresett, akkor a, előfordulhat, hogy vannak szűrve az [tartományalapú szűrés](how-to-connect-sync-configure-filtering.md#domain-based-filtering) vagy [szervezeti egység szerinti szűrés](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Olvassa el a [a szűrés konfigurálása](how-to-connect-sync-configure-filtering.md) témakörhöz, és győződjön meg arról, hogy a szűrés konfigurált elvárt módon.

Hasznos egy másik kereséssel, hogy válassza ki az Azure AD-összekötő **hatókör** kiválasztása **függőben lévő importálás**, és válassza ki a **Hozzáadás** jelölőnégyzetet. A keresés lehetővé teszi az összes szinkronizált objektumokat, amelyeket nem lehet egy helyszíni objektumhoz társított Azure AD-ben.  
![Összekötő terület keresési árva](./media/tshoot-connect-object-not-syncing/cssearchorphan.png)  
Ezek az objektumok egy másik szinkronizálási motor vagy egy másik szűrési konfigurációval rendelkező szinkronizálási motor hozták létre. Ez a nézet az listája **árva** már nem felügyelt objektumokat. Ekkor a felülvizsgálandó ebben a listában, és fontolja meg ezek az objektumok eltávolítását a [az Azure AD PowerShell](https://aka.ms/aadposh) parancsmagok.

### <a name="cs-import"></a>CS importálása
Amikor megnyit egy cs objektumot, nincsenek több lapok tetején. A **importálása** lapon látható, amely az importálás után az adatok.  
![CS-objektum](./media/tshoot-connect-object-not-syncing/csobject.png)    
A **régi érték** mutatja a jelenleg tárolja a csatlakozás és a **új érték** mi érkezett a forrásrendszerben, és még nem alkalmazták. Ha hiba van az objektumot, majd módosítások feldolgozása nem.

**Hiba történt**  
![CS-objektum](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  
A **szinkronizálási hiba** lap csak akkor látható, ha a probléma az objektummal. További információkért lásd: [szinkronizálási hibák elhárítása](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>CS Leszármaztatási
A leszármaztatási lap bemutatja, hogyan viszonyul az összekötőtér objektuma a metaverzum-objektum. Láthatja, ha az összekötő utolsó importál egy módosítást a a csatlakoztatott rendszer, és mely szabályokat alkalmazza kitölteni az adatokat a metaverzumban.  
![CS leszármaztatási](./media/tshoot-connect-object-not-syncing/cslineage.png)  
Az a **művelet** oszlopban látható egy **bejövő** a művelettel a szinkronizálási szabály **kiépítése**. Azt jelzi, hogy mindaddig, amíg a összekötőtér objektuma jelen, a metaverzum-objektum marad. Ha a szinkronizálási szabályok listáját inkább mutatja egy szinkronizálási szabályt, az irány **kimenő** és **kiépítése**, az azt jelzi, hogy ez az objektum törölve van, a metaverzum-objektum törlése.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/cslineageout.png)  
Emellett megtekintheti az a **PasswordSync** oszlopot, amely a bejövő összekötőterében közreműködhet változik a jelszót, mert egy szinkronizálási szabály értéke **igaz**. Ezt a jelszót az Azure AD a megadott kimenő szabály majd zajlik.

A leszármaztatási lapról érheti el a metaverzumba kattintva [Metaverzumbeli objektumának tulajdonságait](#mv-attributes).

Az összes lap alján két gomb a következők: **Előzetes verzió** és **Log**.

### <a name="preview"></a>Előzetes verzió
Az előzetes verziójú szolgáltatásainak weblapján szolgál egy egyetlen objektum szinkronizálásához. Ez akkor hasznos, ha néhány egyéni szinkronizálási szabály hibaelhárítást, és szeretné látni a változás hatását egy önálló objektumon. Választhat **teljes szinkronizálás** és **különbözeti szinkronizálási**. Között lehetőség kiválasztásával **előzetes létrehozása**, ami a memória, csak tartja a módosítás és **véglegesítése előzetes**, amely frissíti a metaverzumba, és előkészíti a cél összekötőterek összes módosítását.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/preview.png)  
Az objektum, és melyik szabály alkalmazása adott attribútum folyamathoz tartozó vizsgálhatja meg.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Napló
A oldalon tekintse meg a jelszó-szinkronizálás állapota és előzményei szolgál. További információkért lásd: [Jelszókivonat-szinkronizálás hibaelhárítása](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>A Metaverzumbeli objektum tulajdonságai
Általában jobb kezdeni a keresést az Active Directory forrásból [összekötőterében](#connector-space). De a Keresés a metaverzumban is elindíthatja.

### <a name="search-for-an-object-in-the-mv"></a>Keresse meg a MV-objektum
A **Synchronization Service Managert**, kattintson a **keresés a Metaverzumban**. Hozzon létre egy lekérdezést, megkeresi a felhasználó ismeri. Általános attribútumokkal rendelkeznek, például accountName (sAMAccountName) és a userPrincipalName kereshet. További információkért lásd: [keresés a Metaverzumban](how-to-connect-sync-service-manager-ui-mvsearch.md).
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Az a **keresési eredmények** ablakában kattintson az objektumra.

Ha nem látja az objektumot, majd azt nem még érte el a metaverzumba. Keresse meg az objektumot az Active Directory továbbra is [összekötőterében](#connector-space-object-properties). Lehetséges, hogy szinkronizálást, amely blokkolja az objektumot a metaverzumba jelenik meg a hiba, vagy előfordulhat, hogy egy szűrőt alkalmazza.

### <a name="mv-attributes"></a>MV-attribútumok
Az attribútumok lapon láthatja, hogy az értékeket, és mely összekötő viszonyított azt.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Ha egy objektum nem szinkronizálja, majd keresse meg a következő attribútumok metaverzumban található:
- Az attribútum **cloudFiltered** jelen, és állítsa **igaz**? Ha van, akkor a ismertetett lépések szerint szűrve [attribútum alapú szűrést](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Az attribútum **sourceAnchor** jelen? Ha nem rendelkezik egy fiók-erőforrás szolgának? Ha egy objektum hivatkozott postafiókkal azonosítottak (az attribútum **msExchRecipientTypeDetails** értéke 2), majd a sourceAnchor van százalékaránya az erdő engedélyezve van az Active Directory-fiókkal. Ellenőrizze, hogy a fő fiók importált és megfelelően szinkronizálva. A fő fiók szerepelnie kell a [összekötők](#mv-connectors) objektum számára.

### <a name="mv-connectors"></a>MV-összekötők
Az összekötők lapon látható minden összekötő szóközt, amely rendelkezik az objektum reprezentációját.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  
Az összekötő kell rendelkeznie:

- Minden egyes Active Directory-erdőben, a felhasználó jelennek meg. E ábrázolás foreignSecurityPrincipals és a Contact objektumot is tartalmazhat.
- Az Azure AD-összekötő.

Ha az Azure AD-összekötő hiányoznak, majd elolvashatják [MV attribútumok](#mv-attributes) önköltséggel Azure ad-hez feltételeinek ellenőrzése.

Ezen a lapon emellett lehetővé teszi keresse meg a [összekötőtér objektuma](#connector-space-object-properties). Jelöljön ki egy sort, és kattintson a **tulajdonságok**.

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációja.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
