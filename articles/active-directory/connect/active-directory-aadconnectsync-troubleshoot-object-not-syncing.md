---
title: "Olyan objektum, amely nem szinkronizál az Azure AD hibaelhárítása |} Microsoft Docs"
description: "Végezzen hibaelhárítást, ezért az objektum nem szinkronizál az Azure AD."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 491a920ceeaac62dd37b1def3f02234056aebfb0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Olyan objektum, amely nem szinkronizál az Azure AD-hibáinak elhárítása

Ha egy objektum nem szinkronizál az Azure AD a várt módon, majd azok több oka. Ha hiba e-mailben kapott az Azure AD vagy az Azure AD Connect Health hibát látja, majd beolvassa [exportálási hibák elhárítása](active-directory-aadconnect-troubleshoot-sync-errors.md) helyette. De ha a probléma, ha az objektum nincs az Azure AD hibaelhárítási, majd a témakör, hogy a felhasználó. Bemutatja, hogyan lehet a helyszíni összetevő az Azure AD Connect szinkronizálási szolgáltatás hibát talál.

A hibák megkereséséhez fog nézze meg több különböző helyen, a következő sorrendben:

1. A [műveletnaplók](#operations) importálása és szinkronizálás során a szinkronizálási motor által azonosított hibák kereséséhez.
2. A [kapcsolódási térbe](#connector-space-object-properties) hiányzó objektumokhoz és szinkronizálási hibák kereséséhez.
3. A [metaverse](#metaverse-object-properties) problémák kapcsolódó adatok kereséséhez.

Start [Synchronization Service Managert](active-directory-aadconnectsync-service-manager-ui.md) lépések megkezdése előtt.

## <a name="operations"></a>Műveletek
A műveletek a lap a Synchronization Service Managert esetén, ahol el kell kezdenie a hibaelhárítást. A műveletek lapon a legutóbbi művelet eredményei láthatók.  
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

Felső részén minden futtatásakor krónikus sorrendben jeleníti meg. Alapértelmezés szerint a műveletek az elmúlt hét napban tartja információinak naplózásához, de ez a beállítás használatával módosítható a [Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md). Bármely futtatása, amelyek nem szerepelnek egy sikeres állapotnak keresni szeretne. Rendezés a fejlécek kattintva módosíthatja.

A **állapot** oszlop a legfontosabb adatokat, és a legsúlyosabb károkat okozó problémát futtató jeleníti meg. A leggyakoribb állapotokról a prioritásuk szerinti sorrendben kell vizsgálni gyors összegzése (ahol * több lehetséges hiba karakterláncok jelzi).

| status | Megjegyzés |
| --- | --- |
| leállított-* |A Futtatás nem sikerült befejezni. Ha például a távoli rendszer nem működik, és nem érhető el. |
| leállt hiba-korlát |Több mint 5000 hibák vannak. A Futtatás automatikusan hibák nagy száma miatt le lett állítva. |
| Befejezett -\*-hibák |A Futtatás befejeződött, de hibák (kevesebb mint 5000) meg kell vizsgálni. |
| Befejezett -\*-figyelmeztetések |A Futtatás befejeződött, de bizonyos adatok nem várt állapotban van. Ha hibákat, majd ezt az üzenetet az általában csak az egyik oka. Mindaddig, amíg meg kell oldani hibák, figyelmeztetések nem ki kell vizsgálni. |
| sikeres |Nincs probléma. |

Amikor kiválaszt egy sorra, alsó frissítései futtató részleteinek megjelenítése. A bal alsó a, lehetséges, hogy egy lista bármelyiket **lépés #**. Ez a lista csak akkor jelenik meg, ha több tartomány az erdőben, ahol minden egyes tartományhoz egy lépés képviseli. A tartománynév címszó alatt található **partíció**. A **szinkronizálási statisztika**, található további információ a feldolgozott módosítások számát. Kattintson az alábbi hivatkozásokat a módosított objektumok listájának beolvasása. Ha hibákkal objektummal rendelkezik, azokat a hibákat az Eseménynaplón **szinkronizálási hibák**.

### <a name="troubleshoot-errors-in-operations-tab"></a>A műveletek lapon hibák elhárítása
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
Ha a hibák, mindkét hiba és a hiba, maga az objektum olyan hivatkozásokat tartalmaz, amelyek további információval.

Indításához kattintson a hiba karakterlánca (**szinkronizálási szabály-hiba – függvény-indított** a képen látható). Először lehetősége lesz az objektum áttekintését. A tényleges hiba megtekintéséhez kattintson a gombra **Veremkivonat**. A nyomkövetés a hiba hibakeresési szintű információkat nyújt.

Kattintson a jobb egérgombbal a a **hívásverem-információk** válassza **válassza ki az összes**, és **másolási**. Ezután másolja a verem, és nézze meg a hiba a kedvenc szerkesztő, például a Jegyzettömböt.

* Ha a hiba érkezett **SyncRulesEngine**, majd a hívásverem-információk először rendelkezik az objektum összes attribútumok listáját. Görgessen lefelé, amíg megjelenik a címsor **belső kivétel leírásában olvasható = >**.  
  ![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  A sor után jeleníti meg a hiba. A fenti kép hiba: a létrehozott egyéni szinkronizálási szabály Fabrikam.

A hiba, maga nem ad elegendő információ áll rendelkezésre, majd esetén tekintse meg az adatokat mozgatná időt. Az objektumazonosító, a hivatkozásra, és folytassa a [összekötő tér importált objektum](#cs-import).

## <a name="connector-space-object-properties"></a>Összekötőtér-objektum tulajdonságai
Ha nincs hiba található a [műveletek](#operations) lapra, majd a következő lépés az, hogy az összekötő terület objektum kövesse az Active Directory, a metaverzumba, és az Azure AD. Az ehhez az elérési úthoz keresse meg a probléma.

### <a name="search-for-an-object-in-the-cs"></a>Az objektum a CS keresése

A **Synchronization Service Managert**, kattintson a **összekötők**, válassza ki az Active Directory-összekötőt, és **Összekötőtér keresési**.

A **hatókör**, jelölje be **RDN** (Ha meg szeretne keresni a CN-attribútum) vagy **megkülönböztető név vagy a horgony** (Ha meg szeretne keresni a distinguishedName attribútum). Adjon meg egy értéket, és kattintson a **keresési**.  
![Összekötőtér keresése](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

Ha nem találja az objektum keres, majd az lehet, hogy a rendszer kiszűrt rendelkező [tartományalapú szűrés](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) vagy [OU-alapú szűrés](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering). Olvassa el a [szűrésének beállítása](active-directory-aadconnectsync-configure-filtering.md) a témakörhöz, és győződjön meg arról, hogy a szűrés elvártak szerint van konfigurálva.

Jelölje ki az Azure AD-összekötő egy másik keresési **hatókör** válassza ki **függőben lévő importálás**, és válassza ki a **hozzáadása** jelölőnégyzetet. Ez a keresés lehetővé teszi minden szinkronizált objektumnak az Azure ad-ben, amely nem rendelhető hozzá a helyi objektum.  
![Összekötő terület keresési árva](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
Azokat az objektumokat egy másik szinkronizálási motor vagy a szinkronizálási motor a különböző szűrési konfigurációval rendelkezik hozott létre. Ez a nézet olvashat egy listát **árva** már nem kezelt objektumok. Át kell tekintenie a ezen a listán, és fontolja meg ezek az objektumok eltávolítását a [Azure AD PowerShell](http://aka.ms/aadposh) parancsmagok.

### <a name="cs-import"></a>CS importálása
Cs-objektum megnyitásakor nincsenek lap tetején. A **importálása** lapon láthatók az importálás után előkészített adatok.  
![CS objektum](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
A **régi érték** jeleníti meg, mi jelenleg tárolódik a csatlakozás és a **új érték** mi érkezett a forrásrendszerben, és nem alkalmazták. Ha nem sikerül az objektumra, majd változtatások nincsenek feldolgozva.

**Hiba történt**  
![CS objektum](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
A **szinkronizálási hiba** lap csak akkor látható, ha a probléma oka az objektum. További információkért lásd: [szinkronizálási hibák elhárítása](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>CS Leszármaztatás
A Leszármaztatás lapon látható, hogyan viszonyul az összekötő terület objektum a metaverzum-objektum. Az összekötő utolsó importálása módosítását a csatlakoztatott rendszer, és mely szabályokat alkalmazza a metaverzumban adatok feltöltése tekintheti meg.  
![CS Leszármaztatás](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
Az a **művelet** oszlopban láthatja van egy **bejövő** szinkronizálási szabály művelet **rendelkezés**. Azt jelzi, hogy, hogy mindaddig, amíg az összekötő terület objektum jelen, a metaverzum-objektum továbbra is. Ha a szinkronizálási szabályok helyette jeleníti irány szinkronizálási szabály **kimenő** és **rendelkezés**, azt jelzi, hogy ez az objektum törölve, a metaverzum-objektum törlésekor.  
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
Azt is láthatja a **PasswordSync** oszlopot, amely a bejövő kapcsolódási térbe hozzájárulhat a jelszó vált, mivel egy szinkronizálási szabály értéke **igaz**. A jelszó majd küldése az Azure AD a kimenő forgalomra vonatkozó szabály keresztül.

Leszármaztatás lapján is elérheti a metaverzumba kattintva [Metaverzum-objektum tulajdonságai](#mv-attributes).

Az összes lap alján van két gomb: **előzetes** és **napló**.

### <a name="preview"></a>Előzetes verzió
A villámnézeti lap segítségével egy egyetlen objektum szinkronizálásához. Ez akkor hasznos, ha néhány egyéni szinkronizálási szabály kapcsolatos hibaelhárítást végez, és egy adott objektum változás hatásának megtekintéséhez. Választhat **Full sync** és **különbözeti szinkronizálási**. Is választhat **készítése előzetes**, amely csak végleg a módosítás a memória, és **véglegesítése előzetes**, amely frissíti a metaverzumba, és előkészíti a cél összekötőterek összes módosítását.  
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
Az objektum, és melyik szabály alkalmazása az adott Attribútumfolyam vizsgálhatja meg.  
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>Napló
A napló lap segítségével tekintse meg a jelszó-szinkronizálás állapota és előzményei. További információkért lásd: [jelszó-szinkronizálás hibaelhárítása](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="metaverse-object-properties"></a>Metaverzum-objektum tulajdonságai
Általában célszerűbb kezdeni a keresést a következő forrásból: Active Directory [kapcsolódási térbe](#connector-space). Azonban úgy is elindíthatja, keresés a metaverzumban.

### <a name="search-for-an-object-in-the-mv"></a>Keresse meg az objektumot a MV-ban
A **Synchronization Service Managert**, kattintson a **Metaverzum-keresés**. Megkeresi a felhasználó ismeri-lekérdezés létrehozása. Általános attribútumokkal rendelkeznek, például a fióknév (sAMAccountName) és a userPrincipalName is kereshet. További információkért lásd: [Metaverzum-keresés](active-directory-aadconnectsync-service-manager-ui-mvsearch.md).
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

Az a **keresési eredmények** ablak, kattintson az objektumra.

Ha nem találta meg az objektumot, majd nem még elérte a metaverzumba. Továbbra is keresse meg az objektumot az Active Directory [kapcsolódási térbe](#connector-space-object-properties). Előfordulhat, hogy hiba a szinkronizálás, amely az objektum állapotba kerüljön a metaverzumba történő zárolja, vagy előfordulhat, hogy a szűrő alkalmazása.

### <a name="mv-attributes"></a>MV-attribútumok
Az attribútumok lapon megtekintheti az értékeket, és melyik összekötő hozzájárult azt.  
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

Ha egy objektum nem szinkronizál, majd tekintse meg a metaverzumban a következő attribútumokat:
- Az attribútum **cloudFiltered** jelent-e, és állítsa be **igaz**? Ha van, akkor a szűrt megfelelően lépéseit [attribútum alapú szűrés](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering).
- Az attribútum **sourceAnchor** jelen? Ha nem rendelkezik egy fiók-erőforrás szolgának? Ha egy objektum hivatkozott postafiókkal azonosítottak (attribútum **msExchRecipientTypeDetails** értéke 2), majd a sourceAnchor azon erdő részére engedélyezve van az Active Directory-fiókkal van hozzájárult. Győződjön meg arról, hogy a fő fiók importálva, és megfelelően szinkronizálva. A fő fióknak szerepelnie kell a [összekötők](#mv-connectors) objektumhoz.

### <a name="mv-connectors"></a>MV-összekötők
Az összekötők lapon láthatók, amelyek a megjelenítése, az objektum összes összekötőterek.  
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
Az összekötő kell rendelkezniük:

- Minden Active Directory-erdőt a felhasználók jelennek meg. Ez a megjelenítési módja foreignSecurityPrincipals és ügyfél objektumok lehetnek.
- Az Azure AD-összekötő.

Ha az Azure ad-összekötő hiányzik, majd olvassa el [MV attribútumok](#MV-attributes) ellenőrzése az Azure AD létre feltételeit.

Ezen a lapon azt is lehetővé teszi, hogy keresse meg a [összekötő terület objektum](#connector-space-object-properties). Jelöljön ki egy sort, és kattintson a **tulajdonságok**.

## <a name="next-steps"></a>Következő lépések
További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
