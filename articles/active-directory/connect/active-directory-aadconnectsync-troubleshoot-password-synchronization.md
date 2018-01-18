---
title: "A jelszó-szinkronizálás és az Azure AD Connect-szinkronizálás hibaelhárítása |} Microsoft Docs"
description: "Ez a cikk ismerteti a jelszó-szinkronizálás problémák elhárításáról."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 0bf07e80e575309fe7fa44661776c23da5db6dce
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>A jelszó-szinkronizálás és az Azure AD Connect-szinkronizálás hibaelhárítása
Ez a témakör a jelszó-szinkronizálás a probléma megoldásához szükséges lépéseket. Jelszavak nem szinkronizál a várt módon, ha azok a felhasználók egy része vagy az összes felhasználó számára.

Csatlakozás Azure Active Directory (Azure AD) központi telepítés 1.1.614.0 verziójával vagy után a varázslóban a hibaelhárítási feladat segítségével jelszó-szinkronizálás elhárítása:

* Ha a probléma lehet ahol nincs jelszavak szinkronizálódnak, tekintse meg a [nem jelszavai szinkronizálva vannak: hibaelhárítása a hibaelhárítási feladattal](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) szakasz.

* Ha egyéni objektumokat problémát, tekintse meg a [több objektum van nem jelszó-szinkronizálás: a hibaelhárítási feladattal hibaelhárítása](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) szakasz.

Központi telepítés 1.1.524.0 verziójával, vagy később, nincs jelszó-szinkronizálás problémák hibaelhárításához használható diagnosztikai parancsmag:

* Ha a probléma lehet ahol nincs jelszavak szinkronizálódnak, tekintse meg a [nem jelszavai szinkronizálva vannak: a diagnosztikai parancsmaggal hibaelhárításához](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) szakasz.

* Ha az egyes objektumok problémát, tekintse meg a [több objektum van nem jelszó-szinkronizálás: a diagnosztikai parancsmaggal hibaelhárításához](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) szakasz.

Az Azure AD Connect telepítési korábbi verzióival:

* Ha a probléma lehet ahol nincs jelszavak szinkronizálódnak, tekintse meg a a [nem jelszavai szinkronizálva vannak: hibaelhárítási manuális](#no-passwords-are-synchronized-manual-troubleshooting-steps) szakasz.

* Ha egyéni objektumokat problémát, tekintse meg a [több objektum van nem jelszó-szinkronizálás: hibaelhárítási manuális](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) szakasz.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Nincs jelszavai szinkronizálva vannak: a hibaelhárítási feladattal hibaelhárítása
A hibaelhárítási feladat segítségével mérje fel, hogy miért nem jelszavak szinkronizálódnak.

> [!NOTE]
> A hibaelhárítási feladat csak az Azure AD Connect verzió 1.1.614.0 érhető el vagy újabb verzió.

### <a name="run-the-troubleshooting-task"></a>A hibaelhárítási feladat futtatása
Ahol nincs jelszavai szinkronizálva vannak-e problémák hibaelhárításához:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgáló és az a **Futtatás rendszergazdaként** lehetőséget.

2. Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.

3. Az Azure AD Connect varázsló elindításához.

4. Keresse meg a **további feladatok** lapon jelölje be **kapcsolatos problémák elhárítása**, és kattintson a **következő**.

5. Kattintson a hibaelhárítás lap **indítása** a PowerShell indul el, a hibaelhárítási menü.

6. Válassza ki a főmenü **jelszó-szinkronizálás hibaelhárítása**.

7. Sub menüben válasszon ki **jelszó-szinkronizálás nem működik minden**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményének megismeréséhez
A hibaelhárítási feladat a következő műveleteket hajtja végre:

* Ellenőrzi, hogy a jelszó-szinkronizálási szolgáltatás engedélyezve van az Azure AD-bérlő.

* Ellenőrzi, hogy az Azure AD Connect-kiszolgáló nem átmeneti módban.

* Minden meglévő helyszíni Active Directory Connector (amely a meglévő Active Directory-erdőben felel meg):

   * Ellenőrzi, hogy engedélyezve van-e a jelszó-szinkronizálási szolgáltatás.
   
   * Megkeresi a jelszó szinkronizálása szívverés eseményeket a Windows-alkalmazás eseménynaplóiban lesz naplózva.

   * Minden egyes a helyszíni Active Directory-összekötőt az Active Directory-tartomány:

      * Ellenőrzi, hogy a tartomány az Azure AD Connect-kiszolgáló elérhető-e.

      * Ellenőrzi, hogy az Active Directory tartományi szolgáltatások (AD DS) fiókok, amelyet a helyszíni Active Directory-összekötő rendelkezik-e a megfelelő felhasználónév, jelszó és a jelszó-szinkronizáláshoz szükséges engedélyeket.

A következő ábra szemlélteti a parancsmag eredményei azoknak az olyan egyetlen tartományból, a helyszíni Active Directory-topológiát:

![Diagnosztikai kimenetet a jelszó-szinkronizáláshoz](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalgeneral.png)

Ez a szakasz a többi adott a feladatot, és a megfelelő problémák által visszaadott eredmények ismerteti.

#### <a name="password-synchronization-feature-isnt-enabled"></a>Jelszó-szinkronizálási szolgáltatás nincs engedélyezve
Ha még nem engedélyezte a jelszó-szinkronizálás az Azure AD Connect varázsló segítségével, a következő hibaüzenet:

![A jelszó-szinkronizálás nincs engedélyezve](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Az Azure AD Connect-kiszolgáló átmeneti módban van
Ha az Azure AD Connect-kiszolgáló az átmeneti környezetű üzemmód, jelszó-szinkronizálás ideiglenesen le van tiltva, és a következő hibaüzenet:

![Az Azure AD Connect-kiszolgáló átmeneti módban van](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalstaging.png)

#### <a name="no-password-synchronization-heartbeat-events"></a>Jelszó szinkronizálási szívverés események
Minden egyes a helyszíni Active Directory-összekötőt a saját jelszó-szinkronizálás csatorna rendelkezik. Ha a jelszó-szinkronizálás csatorna jön létre, és nincs a szinkronizálandó jelszó módosításokat, a szívverés (eseményazonosító 654) jön létre 30 percenként alatt a Windows alkalmazási eseménynaplóba. Az egyes a helyszíni Active Directory-összekötőt, a parancsmag rákeres a megfelelő szívverés-események az elmúlt három órában. Ha nincs szívverés esemény található, a következő hibaüzenet:

![Nincs jelszó-szinkronizálás szív járulhatnak esemény](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS fiók nem rendelkezik megfelelő engedélyekkel
Ha a jelszó-kivonatok szinkronizálását a helyi Active Directory-összekötő által használt Active Directory tartományi szolgáltatások fiók nem rendelkezik megfelelő engedélyekkel, a következő hibaüzenet:

![Helytelen hitelesítő adatok](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Active Directory tartományi szolgáltatások fiók helytelen felhasználónév vagy jelszó
Ha az Active Directory tartományi szolgáltatások jelszókivonatait szinkronizálása a helyszíni Active Directory-összekötő által használt fióknak egy helytelen felhasználónév vagy jelszó, a következő hibaüzenet:

![Helytelen hitelesítő adatok](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Több objektum van nem jelszó-szinkronizálás: a hibaelhárítási feladattal hibaelhárítása

A hibaelhárítási feladat segítségével határozza meg, miért egy objektum nem szinkronizál jelszavakat.

> [!NOTE]
> A hibaelhárítási feladat csak az Azure AD Connect verzió 1.1.614.0 érhető el vagy újabb verzió.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztika parancsmag futtatása
Egy adott felhasználói objektum problémák hibaelhárításához:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgáló és az a **Futtatás rendszergazdaként** lehetőséget.

2. Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.

3. Az Azure AD Connect varázsló elindításához.

4. Keresse meg a **további feladatok** lapon jelölje be **kapcsolatos problémák elhárítása**, és kattintson a **következő**.

5. Kattintson a hibaelhárítás lap **indítása** a PowerShell indul el, a hibaelhárítási menü.

6. Válassza ki a főmenü **jelszó-szinkronizálás hibaelhárítása**.

7. Sub menüben válasszon ki **jelszó nem egy adott felhasználói fiók szinkronizálása**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményének megismeréséhez
A hibaelhárítási feladat a következő műveleteket hajtja végre:

* Az Active Directory connector, Metaverse, és Azure Active Directory-objektum állapotának megvizsgálja AD kapcsolódási térbe.

* Ellenőrzi, hogy vannak-e a jelszó-szinkronizálás engedélyezve van, és az Active Directory-objektum alkalmazott szinkronizálási szabályait.

* Megkísérli lekérni, és a jelszó az objektum utolsó kísérlet eredményeit jeleníti meg.

A következő ábra szemlélteti a parancsmag eredményei egy adott objektum jelszó-szinkronizálás hibaelhárítása során:

![A jelszó-szinkronizáláshoz - egyetlen objektumhoz diagnosztikai kimenetet](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjectgeneral.png)

A többi Ez a szakasz ismerteti a parancsmag és a megfelelő problémák által adott eredmények.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Az Active Directory-objektum nem az Azure AD exportálva
A jelszó-szinkronizálás a helyszíni Active Directory-fiók sikertelen lesz, mivel nincs a megfelelő objektum az az Azure AD-bérlő. A következő hibaüzenet:

![Az Azure AD-objektum hiányzik.](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Felhasználó rendelkezik-e egy ideiglenes jelszót
Jelenleg az Azure AD Connect nem támogatja ideiglenes jelszó-szinkronizálás Azure AD-val. A jelszó ideiglenes tekinthető Ha a **jelszó módosítása a következő bejelentkezéskor** beállítás be van állítva a helyszíni Active Directory felhasználóra. A következő hibaüzenet:

![Ideiglenes jelszót ne exportálja.](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Jelszó tett legutóbbi kísérlet eredményeit nem érhetők el
Alapértelmezés szerint az Azure AD Connect szinkronizálási bejelentkezési kísérletek hét napja eredményeit tárolja. Ha nem érhető el a kiválasztott Active Directory-objektum, akkor a következő figyelmeztetést adott vissza:

![Egyetlen objektum – a Nincs szinkronizálás az előző diagnosztikai kimenete](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Nincs jelszavai szinkronizálva vannak: a diagnosztikai parancsmaggal hibaelhárításához
Használhatja a `Invoke-ADSyncDiagnostics` parancsmag, hogy megtudja, miért nem jelszavak szinkronizálódnak.

> [!NOTE]
> A `Invoke-ADSyncDiagnostics` parancsmag verziójához rendelkezésre álló csak az Azure AD Connect 1.1.524.0 vagy újabb verzió.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztika parancsmag futtatása
Ahol nincs jelszavai szinkronizálva vannak-e problémák hibaelhárításához:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgáló és az a **Futtatás rendszergazdaként** lehetőséget.

2. Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.

3. Futtassa az `Import-Module ADSyncDiagnostics` parancsot.

4. Futtassa az `Invoke-ADSyncDiagnostics -PasswordSync` parancsot.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Több objektum van nem jelszó-szinkronizálás: a diagnosztikai parancsmaggal hibaelhárításához
Használhatja a `Invoke-ADSyncDiagnostics` parancsmag használatával határozza meg, miért egy objektum nem szinkronizál jelszavakat.

> [!NOTE]
> A `Invoke-ADSyncDiagnostics` parancsmag verziójához rendelkezésre álló csak az Azure AD Connect 1.1.524.0 vagy újabb verzió.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztika parancsmag futtatása
Ahol nincs jelszavai szinkronizálva vannak az egy felhasználó problémák hibaelhárításához:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgáló és az a **Futtatás rendszergazdaként** lehetőséget.

2. Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.

3. Futtassa az `Import-Module ADSyncDiagnostics` parancsot.

4. Futtassa a következő parancsmagot:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```
   Példa:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Nincs jelszavai szinkronizálva vannak: manuális hibaelhárítási lépések
Határozza meg, miért nem jelszavai szinkronizálva vannak az alábbi lépéseket követve:

1. A Connect-kiszolgáló a [átmeneti módban](active-directory-aadconnectsync-operations.md#staging-mode)? Átmeneti módban nem szinkronizálja a jelszavakat.

2. Futtassa a parancsfájlt a [jelszó-szinkronizálási beállítások állapotának lekérése](#get-the-status-of-password-sync-settings) szakasz. Ez lehetővé teszi a jelszó-szinkronizálás konfigurációs áttekintése.  

    ![PowerShell parancsfájl kimenete, jelszó-szinkronizálási beállítások](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  

3. Ha a szolgáltatás nincs engedélyezve az Azure ad-ben, vagy ha a szinkronizálási csatorna állapota nem engedélyezett, futtassa a Connect telepítővarázslóját. Válassza ki **testre szabhatja a szinkronizálási beállítások**, és törölje a jelszó-szinkronizálást. Ez a változás ideiglenesen letiltja a szolgáltatást. Majd futtassa újra a varázslót, és engedélyezze újra a jelszó-szinkronizálást. Futtassa a ismét ellenőrizze, hogy a konfiguráció helyes-e.

4. Keressen hibákat az eseménynaplóban találhatók. Keresse meg a következő események, amelyek azt jelentené, hogy a problémát:
    * Forrás: "A címtár-szinkronizálás" azonosító: 0, 611, 652, ha ezek az események 655 kapcsolat hibát. Az eseménynapló-üzenet esetében probléma erdő információkat tartalmaz. További információkért lásd: [csatlakozási probléma](#connectivity problem).

5. Ha nem szívverés, vagy ha nincs más, futtassa [indul el, a teljes szinkronizálás az összes jelszavak](#trigger-a-full-sync-of-all-passwords). A parancsfájl csak egyszer futtatnia.

6. Tekintse meg a [egy olyan objektum, amely nem szinkronizál jelszavak hibaelhárítása](#one-object-is-not-synchronizing-passwords) szakasz.

### <a name="connectivity-problems"></a>Kapcsolódási problémák

Az Azure AD-kapcsolat van?

A fiók rendelkezik az összes tartomány kivonatai a szükséges jogosultságok? Csatlakozás a gyorsbeállítások használatával telepítette, ha az engedélyek már kell helyes-e. 

Ha egyéni telepítés, az engedélyek beállítása manuálisan a következő módon:
    
1. Keresse meg a fiókot az Active Directory-összekötő által használt, indítsa el a **Synchronization Service Managert**. 
 
2. Ugrás a **összekötők**, majd keresse meg a hibaelhárítási a helyszíni Active Directory-erdőben. 
 
3. Válassza ki az összekötőt, és kattintson **tulajdonságok**. 
 
4. Ugrás a **csatlakozni az Active Directory Erdőfelderítési**.  
    
    ![Active Directory-összekötő által használt fiók](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    Vegye figyelembe a felhasználónév és a fióknak a tartomány.
    
5. Start **Active Directory – felhasználók és számítógépek**, és ellenőrizze, hogy a korábban talált fióknak az erdőben lévő összes tartományban gyökérkönyvtárában végezze engedélyekkel:
    * Változások replikálása
    * Replikálása Directory összes módosítása

6. A tartományvezérlők elérhetők az Azure AD Connect? Ha a Connect-kiszolgáló nem tud csatlakozni az összes olyan tartományvezérlőn, konfiguráljon **csak az elsődleges tartományvezérlőt használja**.  
    
    ![Az Active Directory-összekötő által használt tartományvezérlő](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    
7. Lépjen vissza a **Synchronization Service Managert** és **címtárpartíciók konfigurálása**. 
 
8. Válassza ki a tartományt a **válassza ki az a címtárpartíciókat**, jelölje be a **csak használja az elsődleges tartományvezérlő** jelölőnégyzetet, majd kattintson a **konfigurálása**. 

9. A listában adja meg a tartományvezérlőn, amely a jelszó-szinkronizálás használata ajánlott a csatlakozás. Ugyanazt a listát importálásának és exportálásának is szolgál. Hajtsa végre ezeket a lépéseket minden tartományban.

10. Ha a parancsfájl azt mutatja, hogy nem szívverés, futtassa a parancsfájlt [indul el, a teljes szinkronizálás az összes jelszavak](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Több objektum van nem jelszó-szinkronizálás: manuális hibaelhárítási lépések
Jelszó-szinkronizálás problémák könnyen elháríthatja az egy objektumot állapotának megtekintésével.

1. A **Active Directory – felhasználók és számítógépek**, keresse meg a felhasználót, és ellenőrizze, hogy a **kell változtatni a jelszót a következő bejelentkezéskor** jelölőnégyzet nincs bejelölve.  

    ![Active Directory hatékony jelszavak](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  

    Ha a jelölőnégyzet be van jelölve, kérje meg, hogy jelentkezzen be, és módosítsa a jelszavát. Ideiglenes jelszavak nincsenek szinkronizálva az Azure ad-val.

2. Ha a jelszó helyes-e az Active Directoryban, hajtsa végre a szinkronizálási motor a felhasználót. A felhasználó következő a helyszíni Active Directoryból az Azure AD, láthatja, hogy van-e a leíró hiba az objektum.

    a. Indítsa el a [Synchronization Service Managert](active-directory-aadconnectsync-service-manager-ui.md).

    b. Kattintson a **összekötők**.

    c. Válassza ki a **Active Directory-összekötő** ahol a felhasználó tartozik.

    d. Válassza ki **Összekötőtér keresési**.

    e. Az a **hatókör** mezőben válassza **megkülönböztető név vagy a horgony**, és írja be a teljes DN hibaelhárítási felhasználó.

    ![A kapcsolódási térbe megkülönböztető névvel rendelkező felhasználó keresése](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  

    f. Keresse meg a felhasználó keres, és kattintson a **tulajdonságok** megtekintéséhez az összes attribútumot. Ha a felhasználó nincs a keresési eredményt, ellenőrizze a [szűrési szabályok](active-directory-aadconnectsync-configure-filtering.md) és győződjön meg arról, hogy [alkalmaz, és a módosítások ellenőrzéséhez](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) csatlakozás megjelennek a felhasználó számára.

    g. Kattintson a részletek a jelszó szinkronizálása az objektum az elmúlt héten, **napló**.  

    ![Napló részletes adatai](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  

    Ha az objektum napló üres, az Azure AD Connect nem tudta olvasni a Jelszókivonat az Active Directoryból. Folytassa a [kapcsolódási hibák](#connectivity-errors). Ha látja, mint bármely más érték **sikeres**, tekintse meg a táblázatban szereplő [jelszó-szinkronizálás napló](#password-sync-log).

    h. Válassza ki a **Leszármaztatás** lapot, és győződjön meg arról, hogy legalább egy szinkronizálási szabályának a **PasswordSync** oszlop **igaz**. Az alapértelmezett beállítás a szinkronizálási szabály neve nem **a az AD - felhasználó AccountEnabled**.  

    ![Leszármaztatás felhasználókkal kapcsolatos információk](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  

    i. Kattintson a **Metaverzum-objektum tulajdonságai** megjelenítése a felhasználói attribútumok listáját.  

    ![Metaverzum-információk](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  

    Győződjön meg arról, hogy nincs **cloudFiltered** attribútumnak jelen. Győződjön meg arról, hogy a tartomány tulajdonságai (domainFQDN és domainNetBios) a várt értékeket.

    j. Kattintson a **összekötők** fülre. Győződjön meg arról, hogy megjelenik-e a helyszíni Active Directory és az Azure AD-összekötőt.

    ![Metaverzum-információk](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  

    k. Válassza ki, amely jelöli az Azure ad-vel, kattintson a sor **tulajdonságok**, majd kattintson a **Leszármaztatás** lapon. Az összekötő terület objektumot kell egy kimenő forgalomra vonatkozó szabály a **PasswordSync** oszlop beállítása **igaz**. Az alapértelmezett beállítás a szinkronizálási szabály neve nem **ki az AAD - csatlakozás a felhasználói**.  

    ![Összekötő Összekötőtér-objektum tulajdonságai párbeszédpanel](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Jelszó-szinkronizálás napló
Az Állapot oszlop a következő értékeket veheti fel:

| status | Leírás |
| --- | --- |
| Sikeres |Jelszó szinkronizálása sikerült. |
| FilteredByTarget |Jelszó beállítása **kell változtatni a jelszót a következő bejelentkezéskor**. Jelszó nem lett szinkronizálva. |
| NoTargetConnection |A metaverse vagy az Azure AD-kapcsolódási térbe nem objektum. |
| SourceConnectorNotPresent |Nem található a helyszíni Active Directory connector területen objektum. |
| TargetNotExportedToDirectory |Az objektum az az Azure AD-kapcsolódási térbe még nem lett exportálva. |
| MigratedCheckDetailsForMoreInfo |Naplóbejegyzés build 1.0.9125.0 előtt készült, és megjelenik-e a korábbi állapotába. |
| Hiba |Szolgáltatás ismeretlen hibával tért vissza. |
| Ismeretlen |Hiba történt a jelszó-kivonatok köteg végrehajtása közben.  |
| MissingAttribute |Adott attribútumok (például a Kerberos-kivonat) Azure AD tartományi szolgáltatások által igényelt nem érhetők el. |
| RetryRequestedByTarget |Adott attribútumok (például a Kerberos-kivonat) Azure AD tartományi szolgáltatások által igényelt korábban nem érhető el. A felhasználó Jelszókivonat újraszinkronizálása kísérlet. |

## <a name="scripts-to-help-troubleshooting"></a>Parancsfájlokat, amelyek segítségével a hibaelhárítás

### <a name="get-the-status-of-password-sync-settings"></a>Jelszó-szinkronizálási beállítások állapotának lekérése
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>A teljes szinkronizálás az összes jelszavak eseményindító
> [!NOTE]
> Ez a parancsfájl csak egyszer futtatnia. Egynél többször futtatásához szükséges, ha valami mást, a probléma. A probléma megoldásához forduljon a Microsoft támogatási szolgálatához.

Az összes jelszavak teljes szinkronizálásra is elindíthatja az alábbi parancsfájl használatával:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>További lépések
* [A jelszó-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Az Azure AD Connect-szinkronizálás: Szinkronizálási beállítások testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
