---
title: A Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás hibaelhárítása |} A Microsoft Docs
description: Ez a cikk ismerteti a jelszó Jelszókivonat-szinkronizálási hibák elhárítása.
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
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6feed11fcfc597658f3ec148b5dd18bb7e3f8f83
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793322"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>A Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás hibaelhárítása

Ez a témakör a Jelszókivonat-szinkronizálás hibáinak elhárítása a lépéseit ismerteti. Ha a jelszavak nem szinkronizál a várt módon, az összes felhasználó vagy felhasználók alcsoportjaihoz lehet.

Az Azure Active Directory (Azure AD) Connect üzemelő 1.1.614.0 verziójával vagy használat után a varázsló a Jelszókivonat-szinkronizálás hibaelhárítása a hibaelhárítási feladat kapcsolatos problémák:

* Ha rendelkezik egy problémát, ahol jelszó szinkronizálása sem történik, tekintse meg a [jelszó szinkronizálása sem történik: a hibaelhárítási feladat használatával hibaelhárítása](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) szakaszban.

* Ha egyéni objektumokat problémáját, tekintse meg a [egy objektum nem szinkronizálja a jelszavakat: a hibaelhárítási feladat használatával hibaelhárítása](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) szakaszban.

Központi telepítés 1.1.524.0 verziójával, vagy később, nincs, amellyel jelszó kivonat-szinkronizálás hibáinak elhárítása a diagnosztikai parancsmag:

* Ha rendelkezik egy problémát, ahol jelszó szinkronizálása sem történik, tekintse meg a [jelszó szinkronizálása sem történik: hibáinak elhárítása a diagnosztikai parancsmaggal](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) szakaszban.

* Ha egyéni objektumokat problémáját, tekintse meg a [egy objektum nem szinkronizálja a jelszavakat: hibáinak elhárítása a diagnosztikai parancsmaggal](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) szakaszban.

Régebbi verziók esetében az Azure AD Connect üzemelő példány:

* Ha rendelkezik egy problémát, ahol jelszó szinkronizálása sem történik, tekintse meg a [jelszó szinkronizálása sem történik: hibaelhárítási lépések manuális](#no-passwords-are-synchronized-manual-troubleshooting-steps) szakaszban.

* Ha egyéni objektumokat problémáját, tekintse meg a [egy objektum nem szinkronizálja a jelszavakat: hibaelhárítási lépések manuális](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) szakaszban.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Jelszó szinkronizálása sem történik: a hibaelhárítási feladat használatával hibaelhárítása

A hibaelhárítási feladat használatával döntse el, miért jelszó szinkronizálása sem történik.

> [!NOTE]
> A hibaelhárítási feladat, csak az Azure AD Connect verziója 1.1.614.0 érhető el vagy újabb.

### <a name="run-the-troubleshooting-task"></a>A hibaelhárítási feladat futtatása

A hibaelhárítás, ahol jelszó szinkronizálása sem történik:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a az Azure AD Connect szolgáltatást a **Futtatás rendszergazdaként** lehetőséget.

2. Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.

3. Az Azure AD Connect varázsló elindításához.

4. Keresse meg a **további feladatokat** lapon jelölje be **hibaelhárítás**, és kattintson a **tovább**.

5. A hibaelhárítás lapon kattintson a **indítsa el a** , indítsa el a hibaelhárítási menü a PowerShellben.

6. A fő menüjéből válassza **Jelszókivonat-szinkronizálás hibaelhárítása**.

7. Sub menüben válassza **Jelszókivonat-szinkronizálás nem működik minden**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményeivel

A hibaelhárítási feladat az alábbi ellenőrzéseket hajtja végre:

* Ellenőrzi, hogy a jelszó Jelszókivonat-szinkronizálási szolgáltatás engedélyezve van-e az Azure AD-bérlője számára.

* Ellenőrzi, hogy az Azure AD Connect kiszolgáló nem átmeneti módban van.

* Minden meglévő helyszíni Active Directory-összekötő (amely megfelel egy meglévő Active Directory-erdőhöz):

   * Ellenőrzi, hogy a jelszó Jelszókivonat-szinkronizálási szolgáltatás engedélyezve van.
   
   * A jelszó-keresések ujjlenyomat-szinkronizálási szívverés eseményeket a Windows alkalmazás eseménynaplóiban.

   * Minden egyes Active Directory tartományhoz a helyszíni Active Directory-összekötőt:

      * Ellenőrzi, hogy a tartomány érhető el az Azure AD Connect-kiszolgálóról.

      * Ellenőrzi, hogy az Active Directory Domain Services (AD DS) fiókok a helyszíni Active Directory-összekötő által használt rendelkezik-e a megfelelő felhasználónév, jelszó és a Jelszókivonat-szinkronizálás szükséges engedélyekkel.

Az alábbi ábrán egy egyetlen tartományból álló, a helyszíni Active Directory-topológia a parancsmag eredményei láthatók:

![Diagnosztikai kimenetet a Jelszókivonat-szinkronizálás](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

Ez a szakasz a többi adott, a feladat és a kapcsolódó problémák által visszaadott eredmények ismerteti.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>jelszó Jelszókivonat-szinkronizálási szolgáltatás nincs engedélyezve

Ha még nem engedélyezte a Jelszókivonat-szinkronizálás az Azure AD Connect varázsló használatával, a következő hibát ad vissza:

![a Jelszókivonat-szinkronizálás nincs engedélyezve](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Az Azure AD Connect-kiszolgáló átmeneti módban van

Ha az Azure AD Connect-kiszolgáló átmeneti módban van, a Jelszókivonat-szinkronizálás ideiglenesen le van tiltva, és a következő hibát ad vissza:

![Az Azure AD Connect-kiszolgáló átmeneti módban van](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Nincsenek jelszó kivonatoló szinkronizálási szívverés események

Minden egyes a helyszíni Active Directory-összekötő a saját jelszavát kivonatoló szinkronizálási csatornával rendelkezik. A jelszó Jelszókivonat szinkronizálása csatorna létrejött, és hogy nem a jelszó módosítások szinkronizálását, amikor a szívverés (eseményazonosító 654) jön létre 30 percenként egyszer mellett a Windows alkalmazások eseménynaplójában. Az egyes helyszíni Active Directory-összekötőt, a parancsmag rákeres a megfelelő szívverés események az elmúlt három órában. Ha nincs szívverés esemény található, a következő hibát ad vissza:

![Nincs jelszó Jelszókivonat szinkronizálása szív beat esemény](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS-fiók nem rendelkezik megfelelő engedélyekkel

Ha az AD DS-fiókot, amelyet a helyszíni Active Directory-összekötőt a jelszókivonatok szinkronizálása nem rendelkezik megfelelő engedélyekkel, a következő hibát ad vissza:

![Helytelen hitelesítő adatok](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Helytelen az AD DS-fiókhoz tartozó felhasználónév vagy jelszó

Ha az AD DS-fiókot a jelszókivonatok szinkronizálása a helyszíni Active Directory-összekötő által használt helytelen felhasználónév vagy jelszó rendelkezik, a következő hibát ad vissza:

![Helytelen hitelesítő adatok](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Egy objektum nem szinkronizálja a jelszavakat: a hibaelhárítási feladat használatával hibaelhárítása

A hibaelhárítási feladat használatával határozza meg, miért egy objektum nem szinkronizálja a jelszavakat.

> [!NOTE]
> A hibaelhárítási feladat, csak az Azure AD Connect verziója 1.1.614.0 érhető el vagy újabb.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztika parancsmag futtatása

Egy adott felhasználói objektumhoz kapcsolatos problémák elhárítása:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a az Azure AD Connect szolgáltatást a **Futtatás rendszergazdaként** lehetőséget.

2. Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.

3. Az Azure AD Connect varázsló elindításához.

4. Keresse meg a **további feladatokat** lapon jelölje be **hibaelhárítás**, és kattintson a **tovább**.

5. A hibaelhárítás lapon kattintson a **indítsa el a** , indítsa el a hibaelhárítási menü a PowerShellben.

6. A fő menüjéből válassza **Jelszókivonat-szinkronizálás hibaelhárítása**.

7. Sub menüben válassza **jelszót a rendszer nem szinkronizálja az adott felhasználói fiók**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményeivel

A hibaelhárítási feladat az alábbi ellenőrzéseket hajtja végre:

* Az Active Directory összekötőterében, Metaverzum és az Azure Active Directory-objektum állapotát megvizsgálja AD összekötőterében.

* Ellenőrzi, hogy nincsenek-e a Jelszókivonat-szinkronizálás engedélyezve van, és a alkalmazni az Active Directory-objektum szinkronizálási szabályait.

* Megkísérli lekérni, és megjeleníti az eredményeket a legutóbbi kísérlet az objektum a jelszó szinkronizálása.

A következő ábra szemlélteti a parancsmag eredményét, egyetlen objektum a Jelszókivonat-szinkronizálás hibaelhárítása során:

![A Jelszókivonat-szinkronizálás – egyetlen objektumhoz tartozó diagnosztikai kimenetet](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

Ez a szakasz a többi adott vissza a parancsmag és a kapcsolódó problémák által adott eredmények ismerteti.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Az Active Directory-objektum nem exportálva az Azure ad-ben

Jelszókivonat-szinkronizálást a helyszíni Active Directory-fiókot a sikertelen lesz, mert nem található megfelelő objektum az Azure AD-bérlőben. A következő hibaüzenetet adja vissza:

![Az Azure AD-objektum hiányzik.](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Felhasználó rendelkezik egy ideiglenes jelszót

Jelenleg az Azure AD Connect nem támogatja ideiglenes jelszavak szinkronizálása az Azure ad-ben. Ideiglenes jelszó számít Ha a **jelszó módosítása a következő bejelentkezéskor** beállítás értéke a helyszíni Active Directory felhasználóra. A következő hibaüzenetet adja vissza:

![Ideiglenes jelszó ne exportálja.](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Legutóbbi kísérlet jelszó szinkronizálása eredményei nem érhetők el

Alapértelmezés szerint az Azure AD Connect tárolja a Jelszókivonat szinkronizálása jelszókísérlet hét napig eredményeit. Ha nincsenek eredmények a kiválasztott Active Directory-objektum, a következő figyelmeztetést ad vissza:

![Egyetlen objektum - jelszó szinkronizálása az előzményeket nem diagnosztikai kimenetet](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Jelszó szinkronizálása sem történik: a diagnosztikai parancsmaggal hibaelhárítása

Használhatja a `Invoke-ADSyncDiagnostics` parancsmag segítségével döntse el, miért jelszó szinkronizálása sem történik.

> [!NOTE]
> A `Invoke-ADSyncDiagnostics` parancsmag álló csak az Azure AD Connect 1.1.524.0-s vagy újabb verzió.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztika parancsmag futtatása

A hibaelhárítás, ahol jelszó szinkronizálása sem történik:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a az Azure AD Connect szolgáltatást a **Futtatás rendszergazdaként** lehetőséget.

2. Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.

3. Futtassa az `Import-Module ADSyncDiagnostics` parancsot.

4. Futtassa az `Invoke-ADSyncDiagnostics -PasswordSync` parancsot.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Egy objektum nem szinkronizálja a jelszavakat: hibáinak elhárítása a diagnosztikai parancsmag használatával

Használhatja a `Invoke-ADSyncDiagnostics` parancsmag használatával határozza meg, miért egy objektum nem szinkronizálja a jelszavakat.

> [!NOTE]
> A `Invoke-ADSyncDiagnostics` parancsmag álló csak az Azure AD Connect 1.1.524.0-s vagy újabb verzió.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztika parancsmag futtatása

Ha jelszó szinkronizálása sem történik egy felhasználó hibáinak elhárítása:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a az Azure AD Connect szolgáltatást a **Futtatás rendszergazdaként** lehetőséget.

2. Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.

3. Futtassa az `Import-Module ADSyncDiagnostics` parancsot.

4. Futtassa a következő parancsmagot:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Példa:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Jelszó szinkronizálása sem történik: manuális hibaelhárítási lépéseket

Kövesse az alábbi lépéseket meghatározni, miért jelszó szinkronizálása sem történik:

1. A Connect-kiszolgáló a [átmeneti módban](how-to-connect-sync-staging-server.md)? Átmeneti módban lévő kiszolgálók nem szinkronizálja a jelszavakat.

2. Futtassa a parancsfájlt a [jelszó-szinkronizálási beállítások állapotának lekérése](#get-the-status-of-password-sync-settings) szakaszban. Ráadásul a jelszó-szinkronizálás konfigurálása áttekintése.  

    ![PowerShell parancsfájl kimenete, jelszó-szinkronizálási beállítások](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Ha a funkció nincs engedélyezve az Azure ad-ben, vagy ha a szinkronizálási csatorna állapota nem érhető el, futtassa a Connect telepítővarázsló. Válassza ki **szinkronizálási beállítások testreszabása**, és törölje a jelet a jelszó-szinkronizálás. Ez a változás ideiglenesen letiltja a szolgáltatást. Ezután futtassa újra a varázslót, és újból a jelszó-szinkronizálás engedélyezése. Futtassa a szkriptet, győződjön meg arról, hogy a konfiguráció megfelelő a újra.

4. Keressen hibákat az eseménynaplóban. Keresse meg a következő események, amely azt jelzi a hiba:
    * Forrás: "A címtár-szinkronizálás" Azonosítójú esemény: 0, 611, 652, ha ezek az események 655 kapcsolati probléma van. Az eseménynapló-üzenet esetében probléma erdő információkat tartalmaz. További információkért lásd: [csatlakozási probléma](#connectivity problem).

5. Ha nem érkezett szívverés jelenik meg, vagy ha semmi más működött, futtassa [teljes az összes jelszó-szinkronizálás aktiválása](#trigger-a-full-sync-of-all-passwords). Csak egyszer futtassa a szkriptet.

6. A hibaelhárítás egy objektum, amely nem szinkronizálja a jelszavakat a szakaszban látható.

### <a name="connectivity-problems"></a>Csatlakozási problémák

Az Azure AD-csatlakozási van?

A fiók rendelkezik a jelszókivonatokat az összes tartomány olvasásához szükséges engedélyekkel? Csatlakozás a gyorsbeállítások használatával telepítette, ha az engedélyek már kell helyes-e. 

Ha egyéni telepítési használt, állítsa be az engedélyek manuálisan az alábbiak szerint:
    
1. Indítsa el az Active Directory-összekötő által használt fiók megkereséséhez **Synchronization Service Managert**. 
 
2. Lépjen a **összekötők**, majd keresse meg a hibaelhárítást a helyszíni Active Directory-erdőben. 
 
3. Válassza ki az összekötőt, és kattintson a **tulajdonságok**. 
 
4. Lépjen a **csatlakozni az Active Directory-erdő**.  
    
    ![Az Active Directory-összekötő által használt fiók](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Vegye figyelembe a felhasználónév és a tartományt, ahol a fiók található.
    
5. Indítsa el **Active Directory – felhasználók és számítógépek**, és győződjön meg arról, hogy a korábban talált fiók rendelkezik-e az erdőben lévő összes tartományban gyökérmappájában beállítva a következő engedélyek:
    * Címtárváltozások replikálása
    * Replikálás könyvtár összes változik

6. A tartományvezérlők elérhetők az Azure AD Connect? Ha a Connect-kiszolgáló minden tartományvezérlő nem tud csatlakozni, konfigurálja **csak az előnyben részesített tartományvezérlő használata**.  
    
    ![Az Active Directory-összekötő által használt tartományvezérlő](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Lépjen vissza a **Synchronization Service Managert** és **címtárpartíció konfigurálása**. 
 
8. Válassza ki a tartományt a **címtárpartíciók kiválasztása**, jelölje be a **csak az előnyben részesített tartományvezérlők használatának** jelölőnégyzetet, majd kattintson a **konfigurálása**. 

9. A listában adja meg a tartományvezérlők, amelyet a jelszó-szinkronizálás Connect kell használnia. Ugyanazt a listát az importálási és exportálási is szolgál. Hajtsa végre ezeket a lépéseket minden tartományban.

10. Ha a parancsfájl bemutatja, hogy nincs-e nem érkezett szívverés, futtassa a parancsfájlt [teljes az összes jelszó-szinkronizálás aktiválása](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Egy objektum nem szinkronizálja a jelszavakat: manuális hibaelhárítási lépéseket

Jelszó Jelszókivonat szinkronizálása problémák objektum állapotának megtekintésével egyszerűen elhárítását.

1. A **Active Directory – felhasználók és számítógépek**, keresse meg a felhasználót, és ellenőrizze, hogy a **kell változtatni a jelszót a következő bejelentkezéskor** jelölőnégyzet nincs bejelölve.  

    ![Az Active Directory hatékony jelszavak](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Ha a jelölőnégyzet be van jelölve, kérje meg a felhasználó jelentkezik be, és módosítsa a jelszót. Ideiglenes jelszavakat a rendszer nem szinkronizálja az Azure ad-ben.

2. Ha a jelszó helyes-e az Active Directoryban, hajtsa végre a szinkronizálási motor a felhasználót. A következő a felhasználó a helyszíni Active Directoryból az Azure ad-hez, láthatja, hogy van-e egy leíró hiba az objektum.

    a. Indítsa el a [Synchronization Service Managert](how-to-connect-sync-service-manager-ui.md).

    b. Kattintson a **összekötők**.

    c. Válassza ki a **Active Directory-összekötő** ahol a felhasználó megtalálható-e.

    d. Válassza ki **Összekötőtér keresési**.

    e. Az a **hatókör** jelölje ki **DN-t vagy a Forráshorgony**, majd adja meg a teljes DN-t a felhasználó hibaelhárítást.

    ![Keresse meg a felhasználót a DN összekötőtérben](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Keresse meg a felhasználó keres, és kattintson a **tulajdonságok** összes attribútumok megtekintéséhez. Ha a felhasználó nem szerepel a keresési eredmények, ellenőrizze a [szűrési szabályok](how-to-connect-sync-configure-filtering.md) , és győződjön meg arról, hogy futtassa [alkalmaz, és ellenőrizze a módosításokat](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) Connect jelennek meg, hogy a felhasználó.

    g. Kattintson ide a részletek a jelszó szinkronizálása az objektum az elmúlt egy hét, **Log**.  

    ![Objektum napló részletei](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Ha az objektum napló üres, az Azure AD Connect nem sikerült beolvasni a Jelszókivonat az Active Directoryból lett. Továbbra is jelentkezik a hibaelhárítást. Ha látja, mint bármely más érték **sikeres**, tekintse meg a tábla [jelszó-szinkronizálási napló](#password-sync-log).

    h. Válassza ki a **leszármaztatási** lapra, és győződjön meg arról, hogy legalább egy szinkronizálási szabály a **PasswordSync** oszlop **igaz**. Az alapértelmezett beállítás a szinkronizálási szabály neve nem **az ad - felhasználó AccountEnabled**.  

    ![A felhasználó leszármaztatási információkat](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Kattintson a **Metaverzumbeli objektumának tulajdonságait** megjelenítése a felhasználói attribútumok listáját.  

    ![Metaverzum-információk](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Győződjön meg arról, hogy nincs **cloudFiltered** existovat atribut. Győződjön meg arról, hogy a tartomány attribútumok (domainFQDN és domainNetBios) rendelkezik-e a várt értékek.

    j. Kattintson a **összekötők** fülre. Győződjön meg arról, hogy megjelenik-e az összekötők a helyszíni Active Directory és az Azure ad-ben is.

    ![Metaverzum-információk](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Válassza ki, amely jelöli az Azure AD-ben kattintson a sor **tulajdonságai**, majd kattintson a **Leszármaztatási** lapon. Az összekötőtér objektuma kell rendelkeznie az kimenő szabályt a **PasswordSync** oszlopban **igaz**. Az alapértelmezett beállítás a szinkronizálási szabály neve nem **vette az AAD - csatlakozás a felhasználói**.  

    ![Összekötő Összekötőtér-objektum tulajdonságai párbeszédpanel](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Jelszó-szinkronizálási napló

Az Állapot oszlopban a következő értékeket veheti fel:

| status | Leírás |
| --- | --- |
| Sikeres |Jelszó szinkronizálása sikerült. |
| FilteredByTarget |Jelszó beállítása **kell változtatni a jelszót a következő bejelentkezéskor**. Jelszó nem lett szinkronizálva. |
| NoTargetConnection |Nincs objektum tartozik a metaverzumba, vagy az Azure ad-ben összekötőtérben. |
| SourceConnectorNotPresent |Nem található a helyszíni Active Directory-összekötő-térben található objektum. |
| TargetNotExportedToDirectory |Az Azure ad-ben összekötőtérben az objektum még nem lett exportálva. |
| MigratedCheckDetailsForMoreInfo |Naplóbejegyzés build 1.0.9125.0 jött létre, és az örökölt állapotában jelenik meg. |
| Hiba |Szolgáltatás ismeretlen hibát adott vissza. |
| Ismeretlen |Hiba történt a jelszókivonatokat kötegelt feldolgozása közben.  |
| MissingAttribute |Adott attribútumok (például a Kerberos-kivonat) szükséges az Azure AD tartományi szolgáltatások nem érhetők el. |
| RetryRequestedByTarget |Adott attribútumok (például a Kerberos-kivonata) Azure AD tartományi szolgáltatások által igényelt nem lehetett korábban. A jelszókivonatok a felhasználó gyerekhely kísérlet történik. |

## <a name="scripts-to-help-troubleshooting"></a>Parancsfájlok hibaelhárítása során

### <a name="get-the-status-of-password-sync-settings"></a>Jelszó-szinkronizálási beállítások állapotának lekérése

```powershell
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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Az összes jelszó a teljes szinkronizálás aktiválása

> [!NOTE]
> Csak egyszer futtassa ezt a szkriptet. A futtatáshoz egynél többször van szüksége, ha valami mást a probléma. A probléma megoldásához forduljon a Microsoft ügyfélszolgálatához.

A következő parancsfájl használatával is aktiválhatja a teljes szinkronizálás az összes jelszó:

```powershell
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

* [A Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása](how-to-connect-password-hash-synchronization.md)
* [Az Azure AD Connect szinkronizálása: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)