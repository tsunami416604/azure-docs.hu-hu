---
title: Jelszókivonat-szinkronizálás – problémamegoldás az Azure AD Connect szinkronizálásával | Microsoft dokumentumok
description: Ez a cikk a jelszókivonat-szinkronizálási problémák elhárításáról nyújt tájékoztatást.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253545"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>A jelszókivonat szinkronizálásának hibaelhárítása Azure AD Connect-szinkronizálással

Ez a témakör a jelszókivonat-szinkronizálással kapcsolatos problémák elhárításának lépéseit ismerteti. Ha a jelszavak szinkronizálása nem a várt módon történik, akkor az a felhasználók egy részhalmaza vagy az összes felhasználó számára lehetséges.

Az Azure Active Directory (Azure AD) A központi telepítés csatlakoztatása az 1.1.614.0-s vagy azt követő verzióval esetén a varázsló hibaelhárítási feladatával hárítsa el a jelszókivonat-szinkronizálási problémákat:

* Ha olyan problémája van, amelynél nincsenek jelszavak szinkronizálása, olvassa el a [Nincs jelszó szinkronizálása című témakört: hibaelhárítás a hibaelhárítási feladat](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) szakasz használatával.

* Ha problémája van az egyes objektumok, olvassa el a [One objektum nem szinkronizálja a jelszavakat: hibaelhárítás segítségével a hibaelhárítási feladat](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) szakaszban.

Az 1.1.524.0-s vagy újabb verziójú telepítéshez van egy diagnosztikai parancsmag, amellyel elháríthatja a jelszókivonat-szinkronizálási problémákat:

* Ha olyan problémája van, amelynél nincsenek jelszavak szinkronizálása, olvassa el a [Nincs jelszó szinkronizálása című témakört: hibaelhárítás a diagnosztikai parancsmag használatával.](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet)

* Ha problémája van az egyes objektumok, olvassa el a [One objektum nem szinkronizálja a jelszavakat: hibaelhárítás segítségével a diagnosztikai parancsmag](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) szakaszban.

Az Azure AD Connect telepítésének régebbi verzióiesetén:

* Ha olyan problémája van, amelynél nincsenek jelszavak szinkronizálása, olvassa el a [Nincs jelszó szinkronizálása: manuális hibaelhárítási lépések című részt.](#no-passwords-are-synchronized-manual-troubleshooting-steps)

* Ha problémája van az egyes objektumok, olvassa el a [One objektum nem szinkronizálja a jelszavakat: kézi hibaelhárítási lépések](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) szakaszban.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>A rendszer nem szinkronizálja a jelszavakat: hibaelhárítása a hibaelhárítási feladat használatával

A hibaelhárító feladat segítségével kiderítheti, hogy miért nincsenek szinkronizálva a jelszavak.

> [!NOTE]
> A hibaelhárítási feladat csak az Azure AD Connect 1.1.614.0-s vagy újabb verziójához érhető el.

### <a name="run-the-troubleshooting-task"></a>A hibaelhárítási feladat futtatása

Jelszavak szinkronizálása esetén felmerülő problémák elhárítása:

1. Nyisson meg egy új Windows PowerShell-munkamenetet az Azure AD Connect kiszolgálón a **Futtatás rendszergazdaként** beállítással.

2. Futtassa `Set-ExecutionPolicy RemoteSigned` a vagy `Set-ExecutionPolicy Unrestricted`a ot.

3. Indítsa el az Azure AD Connect varázslót.

4. Nyissa meg a **További feladatok** lapot, válassza **a Hibaelhárítás**lehetőséget, és kattintson a **Tovább**gombra.

5. A Hibaelhárítás lapon kattintson az **Indítás** gombra a PowerShell hibaelhárítási menüjének elindításához.

6. A főmenüben válassza a **Jelszókivonat-szinkronizálás hibaelhárítása lehetőséget.**

7. Az almenüben válassza a **Jelszókivonat-szinkronizálás parancsát.**

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményeinek megismerése

A hibaelhárítási feladat a következő ellenőrzéseket hajtja végre:

* Ellenőrzi, hogy a jelszókivonat-szinkronizálási funkció engedélyezve van-e az Azure AD-bérlőhöz.

* Ellenőrzi, hogy az Azure AD Connect-kiszolgáló nincs-e átmeneti módban.

* Minden meglévő helyszíni Active Directory-összekötő esetén (amely egy meglévő Active Directory-erdőnek felel meg):

   * Ellenőrzi, hogy a jelszókivonat-szinkronizálási szolgáltatás engedélyezve van-e.
   
   * Jelszókivonat-szinkronizálási szívverési események keresése a Windows alkalmazás eseménynaplóiban.

   * A helyszíni Active Directory-összekötő alatti minden Egyes Active Directory-tartomány esetén:

      * Ellenőrzi, hogy a tartomány elérhető-e az Azure AD Connect-kiszolgálóról.

      * Ellenőrzi, hogy a helyszíni Active Directory-összekötő által használt Active Directory tartományi szolgáltatások (AD DS) fiókok rendelkeznek-e a megfelelő felhasználónévvel, jelszóval és a jelszókivonat-szinkronizáláshoz szükséges engedélyekkel.

Az alábbi ábra egy helyszíni Active Directory-topológia parancsmagjának eredményeit mutatja be:

![Diagnosztikai kimenet a jelszókivonat-szinkronizáláshoz](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

A szakasz többi része a feladat által visszaadott konkrét eredményeket és a kapcsolódó problémákat ismerteti.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>a jelszókivonat-szinkronizálási szolgáltatás nincs engedélyezve

Ha az Azure AD Connect varázslóval nem engedélyezte a jelszókivonat-szinkronizálást, a következő hibaüzenet jelenik meg:

![a jelszókivonat-szinkronizálás nincs engedélyezve](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Az Azure AD Connect kiszolgáló átmeneti üzemmódban van

Ha az Azure AD Connect kiszolgáló átmeneti üzemmódban van, a jelszókivonat-szinkronizálás ideiglenesen le van tiltva, és a következő hibaüzenet jelenik meg:

![Az Azure AD Connect kiszolgáló átmeneti üzemmódban van](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Nincs jelszókivonat-szinkronizálási szívverési esemény

Minden helyszíni Active Directory-összekötő saját jelszókivonat-szinkronizálási csatornával rendelkezik. Amikor létrejön a jelszókivonat-szinkronizálási csatorna, és nincs szinkronizálandó jelszómódosítás, a Windows alkalmazás eseménynaplója alatt 30 percenként szívveréses esemény (EventId 654) jön létre. Minden helyszíni Active Directory-összekötő esetén a parancsmag megkeresi a megfelelő szívveréses eseményeket az elmúlt három órában. Ha nem talál szívverési eseményt, a következő hibaüzenet jelenik meg:

![Nincs jelszókivonat-szinkronizálási szívveréses esemény](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Az AD DS-fiók nem rendelkezik megfelelő engedélyekkel

Ha a helyszíni Active Directory-összekötő által a jelszókivék szinkronizálására használt Active Directory-fiók nem rendelkezik a megfelelő engedélyekkel, a következő hibaüzenet jelenik meg:

![Helytelen hitelesítő adatok](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Helytelen AD DS-fiók felhasználóneve vagy jelszava

Ha a helyszíni Active Directory-összekötő által a jelszókivék szinkronizálására használt Active Directory-fiók nem megfelelő felhasználónévvel vagy jelszóval rendelkezik, a következő hibaüzenet jelenik meg:

![Helytelen hitelesítő adatok](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Az egyik objektum nem szinkronizálja a jelszavakat: hibaelhárítás a hibaelhárítási feladat használatával

A hibaelhárító feladat segítségével meghatározhatja, hogy egy objektum miért nem szinkronizálja a jelszavakat.

> [!NOTE]
> A hibaelhárítási feladat csak az Azure AD Connect 1.1.614.0-s vagy újabb verziójához érhető el.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztikai parancsmag futtatása

Adott felhasználói objektummal kapcsolatos problémák elhárítása:

1. Nyisson meg egy új Windows PowerShell-munkamenetet az Azure AD Connect kiszolgálón a **Futtatás rendszergazdaként** beállítással.

2. Futtassa `Set-ExecutionPolicy RemoteSigned` a vagy `Set-ExecutionPolicy Unrestricted`a ot.

3. Indítsa el az Azure AD Connect varázslót.

4. Nyissa meg a **További feladatok** lapot, válassza **a Hibaelhárítás**lehetőséget, és kattintson a **Tovább**gombra.

5. A Hibaelhárítás lapon kattintson az **Indítás** gombra a PowerShell hibaelhárítási menüjének elindításához.

6. A főmenüben válassza a **Jelszókivonat-szinkronizálás hibaelhárítása lehetőséget.**

7. Az almenüben válassza a **Jelszó nincs szinkronizálva egy adott felhasználói fiókhoz**lehetőséget.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményeinek megismerése

A hibaelhárítási feladat a következő ellenőrzéseket hajtja végre:

* Az Active Directory-objektum állapotát vizsgálja az Active Directory-összekötő, a Metaverzum és az Azure AD-összekötő térben.

* Ellenőrzi, hogy vannak-e szinkronizálási szabályok jelszókivonat-szinkronizálással, és alkalmazható-e az Active Directory-objektumra.

* Az objektum jelszavának szinkronizálására tett utolsó kísérlet eredményeinek beolvasására és megjelenítésére tett kísérletek.

Az alábbi ábra a parancsmag eredményeit mutatja be egyetlen objektum jelszókivonat-szinkronizálásának hibaelhárításakor:

![Diagnosztikai kimenet a jelszókivonat-szinkronizáláshoz - egyetlen objektum](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

A szakasz többi része a parancsmag által visszaadott konkrét eredményeket és a megfelelő problémákat ismerteti.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Az Active Directory-objektum nem lesz exportálva az Azure AD-be

jelszó-kivonat szinkronizálás a helyszíni Active Directory-fiók sikertelen, mert nincs megfelelő objektum az Azure AD-bérlőben. A következő hibát adja vissza:

![Az Azure AD objektum hiányzik](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>A felhasználónak ideiglenes jelszava van

Jelenleg az Azure AD Connect nem támogatja az ideiglenes jelszavak szinkronizálását az Azure AD-vel. A jelszó ideiglenesnek minősül, ha a Jelszó módosítása a **következő bejelentkezéskor** beállítás van beállítva a helyszíni Active Directory-felhasználónál. A következő hibát adja vissza:

![Az ideiglenes jelszó nem exportálva](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>A jelszó legutóbbi szinkronizálási kísérletének eredményei nem érhetők el

Alapértelmezés szerint az Azure AD Connect hét napig tárolja a jelszókivonat-szinkronizálási kísérletek eredményeit. Ha a kijelölt Active Directory-objektumhoz nem állnak rendelkezésre eredmények, a következő figyelmeztetést adja vissza:

![Diagnosztikai kimenet egyetlen objektumhoz – nincs jelszószinkronizálási előzmény](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Nincsenek jelszavak szinkronizálása: hibaelhárítás a diagnosztikai parancsmag használatával

A `Invoke-ADSyncDiagnostics` parancsmag segítségével kitudja találni, hogy miért nincsenek szinkronizálva a jelszavak.

> [!NOTE]
> A `Invoke-ADSyncDiagnostics` parancsmag csak az Azure AD Connect 1.1.524.0-s vagy újabb verziójához érhető el.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztikai parancsmag futtatása

Jelszavak szinkronizálása esetén felmerülő problémák elhárítása:

1. Nyisson meg egy új Windows PowerShell-munkamenetet az Azure AD Connect kiszolgálón a **Futtatás rendszergazdaként** beállítással.

2. Futtassa `Set-ExecutionPolicy RemoteSigned` a vagy `Set-ExecutionPolicy Unrestricted`a ot.

3. Futtassa az `Import-Module ADSyncDiagnostics` parancsot.

4. Futtassa az `Invoke-ADSyncDiagnostics -PasswordSync` parancsot.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Az egyik objektum nem szinkronizálja a jelszavakat: hibaelhárítás a diagnosztikai parancsmag használatával

A `Invoke-ADSyncDiagnostics` parancsmag segítségével meghatározhatja, hogy egy objektum miért nem szinkronizálja a jelszavakat.

> [!NOTE]
> A `Invoke-ADSyncDiagnostics` parancsmag csak az Azure AD Connect 1.1.524.0-s vagy újabb verziójához érhető el.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztikai parancsmag futtatása

Olyan problémák elhárítása, amelyek miatt a felhasználó nem szinkronizált jelszavakat:

1. Nyisson meg egy új Windows PowerShell-munkamenetet az Azure AD Connect kiszolgálón a **Futtatás rendszergazdaként** beállítással.

2. Futtassa `Set-ExecutionPolicy RemoteSigned` a vagy `Set-ExecutionPolicy Unrestricted`a ot.

3. Futtassa az `Import-Module ADSyncDiagnostics` parancsot.

4. Futtassa a következő parancsmagot:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Példa:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Nincsenek szinkronizálva jelszavak: manuális hibaelhárítási lépések

Az alábbi lépésekkel állapíthatja meg, hogy miért nincsenek szinkronizálva a jelszavak:

1. A Csatlakozás kiszolgáló [átmeneti módban van?](how-to-connect-sync-staging-server.md) Az átmeneti üzemmódban lévő kiszolgálók nem szinkronizálják a jelszavakat.

2. Futtassa a parancsfájlt [a Jelszó-szinkronizálási beállítások állapotának beszereznie](#get-the-status-of-password-sync-settings) szakaszban. Áttekintést ad a jelszószinkronizálás konfigurációjáról.  

    ![PowerShell parancsfájlkimenet a jelszószinkronizálási beállításokból](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Ha a szolgáltatás nincs engedélyezve az Azure AD-ben, vagy ha a szinkronizálási csatorna állapota nincs engedélyezve, futtassa a Csatlakozás telepítő varázslót. Válassza **a Szinkronizálási beállítások testreszabása lehetőséget,** és törölje a jelszószinkronizálás kijelölését. Ez a módosítás ideiglenesen letiltja a szolgáltatást. Ezután futtassa újra a varázslót, és engedélyezze újra a jelszószinkronizálást. Futtassa újra a parancsfájlt, és ellenőrizze, hogy a konfiguráció helyes-e.

4. Keresse meg az eseménynaplóban a hibákat. Keresse meg a következő eseményeket, amelyek problémát jeleznek:
    * Forrás: "Címtár-szinkronizálás" azonosító: 0, 611, 652, 655 Ha látja ezeket az eseményeket, kapcsolódási problémája van. Az eseménynapló-üzenet olyan erdőadatokat tartalmaz, ahol probléma merült fel. További információt a [Kapcsolódási probléma című témakörben talál.](#connectivity problem)

5. Ha nem lát szívverést, vagy ha semmi más nem működött, [futtassa az Összes jelszó teljes szinkronizálásának aktiválását](#trigger-a-full-sync-of-all-passwords). Csak egyszer futtassa a parancsfájlt.

6. Tekintse meg az egyik olyan objektum hibaelhárítása című témakört, amely nem szinkronizálja a jelszavakat.

### <a name="connectivity-problems"></a>Kapcsolódási problémák

Van kapcsolataz Azure AD-vel?

Rendelkezik-e a fiók a jelszókibésékok olvasásához szükséges engedélyekkel minden tartományban? Ha a Connect programot expressz beállításokkal telepítette, az engedélyeknek már helyesnek kell lenniük. 

Ha egyéni telepítést használt, manuálisan állítsa be az engedélyeket az alábbi módon:
    
1. Az Active Directory-összekötő által használt fiók megkereséséhez indítsa el a **Szinkronizálási szolgáltatáskezelőt.** 
 
2. Nyissa meg **az Összekötők**, majd keresse meg a helyszíni Active Directory-erdő hibaelhárítás. 
 
3. Jelölje ki az összekötőt, majd kattintson **a Tulajdonságok gombra.** 
 
4. Nyissa meg **a Csatlakozás az Active Directory erdőhöz (Csatlakozás az Active Directory erdőhöz ) (Csatlakozás az Active Directory erdőhöz ) elemre**  
    
    ![Az Active Directory-összekötő által használt fiók](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Jegyezze fel a felhasználónevet és azt a tartományt, ahol a fiók található.
    
5. Indítsa el az Active Directory – felhasználók és számítógépek beépülő **modult,** majd ellenőrizze, hogy a korábban talált fiók rendelkezik-e az erdő összes tartományának gyökerében az alábbi engedélyekkel:
    * Címtármódosítások replikálása
    * Könyvtármódosítás-módosítások replikálása

6. Elérhetők a tartományvezérlők az Azure AD Connect által? Ha a Csatlakozás kiszolgáló nem tud csatlakozni az összes tartományvezérlőhöz, konfigurálja **a Csak az elsődleges tartományvezérlőt**.  
    
    ![Az Active Directory-összekötő által használt tartományvezérlő](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Lépjen vissza a **Szinkronizálási szolgáltatáskezelőhöz** és **a Címtárpartíció konfigurálásához.** 
 
8. Jelölje be a tartományt a **Címtárpartíciók kiválasztása**párbeszédpanelen, jelölje be a **Csak az előnyben részesített tartományvezérlők használata jelölőnégyzetet,** majd kattintson a **Konfigurálás gombra.** 

9. A listában adja meg azokat a tartományvezérlőket, amelyeket a Connectnek a jelszószinkronizáláshoz használnia kell. Ugyanez a lista az importáláshoz és exportáláshoz is használható. Végezze el az alábbi lépéseket az összes tartományban.

10. Ha a parancsfájl azt mutatja, hogy nincs szívverés, futtassa a parancsfájlt az [Összes jelszó teljes szinkronizálásának aktiválása kor](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Az egyik objektum nem szinkronizálja a jelszavakat: manuális hibaelhárítási lépések

Az objektum állapotának áttekintésével könnyedén elháríthatja a jelszókivonat-szinkronizálási problémákat.

1. Az **Active Directory – felhasználók és számítógépek beépülő modulban**keresse meg a felhasználót, majd ellenőrizze, hogy a **felhasználónak módosítania kell-e a jelszót a következő bejelentkezéskor** jelölőnégyzet.  

    ![Az Active Directory produktív jelszavai](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Ha a jelölőnégyzet be van jelölve, kérje meg a felhasználót, hogy jelentkezzen be, és módosítsa a jelszót. Az ideiglenes jelszavak nincsenek szinkronizálva az Azure AD-vel.

2. Ha a jelszó helyesnek tűnik az Active Directoryban, kövesse a felhasználót a szinkronizálási motorban. Ha a felhasználót a helyszíni Active Directoryból az Azure AD-be követi, láthatja, hogy van-e leíró hiba az objektumon.

    a. Indítsa el a [Szinkronizálási szolgáltatáskezelőt](how-to-connect-sync-service-manager-ui.md).

    b. Kattintson **az Összekötők gombra.**

    c. Jelölje ki azt az **Active Directory-összekötőt,** ahol a felhasználó található.

    d. Válassza a **Keresési összekötő tér ben**lehetőséget.

    e. A **Hatókör** mezőben válassza a **DN vagy a Horgony**lehetőséget, majd adja meg a hibaelhárításáltal kiválasztott felhasználó teljes dn-jét.

    ![Felhasználó keresése az összekötőtérben a DN segítségével](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Keresse meg a keresett felhasználót, majd kattintson a **Tulajdonságok** gombra az összes attribútum megtekintéséhez. Ha a felhasználó nem szerepel a keresési eredményben, ellenőrizze a [szűrési szabályokat,](how-to-connect-sync-configure-filtering.md) és ellenőrizze, hogy [futtatta-e](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) az Alkalmazás és a módosítások ellenőrzését, hogy a felhasználó megjelenjen a Csatlakozás szolgáltatásban.

    g. Az objektum múlt heti jelszószinkronizálási adatainak megtekintéséhez kattintson a **Napló gombra.**  

    ![Objektumnapló részletei](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Ha az objektumnapló üres, az Azure AD Connect nem tudta olvasni a jelszókivonatot az Active Directoryból. Folytassa a hibaelhárítást a kapcsolódási hibákkal. Ha **a sikeresen**kívül más értéket is lát, olvassa el a [Jelszó szinkronizálási naplójában](#password-sync-log)található táblázatot.

    h. Jelölje ki a **vonallapot,** és győződjön meg arról, hogy a **PasswordSync** oszlopban legalább egy szinkronizálási szabály **Igaz**. Az alapértelmezett konfigurációban a szinkronizálási szabály neve **In from AD - User AccountEnabled**.  

    ![A felhasználó vonalsegédje](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. A felhasználói attribútumok listájának megjelenítéséhez kattintson a **Metaverzum-objektum tulajdonságai** parancsra.  

    ![Metaverzum információ](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Ellenőrizze, hogy **nincs-e cloudFiltered** attribútum. Győződjön meg arról, hogy a tartományattribútumok (domainFQDN és domainNetBios) rendelkeznek a várt értékekkel.

    j. Kattintson az **összekötők** fülre. Győződjön meg arról, hogy a helyszíni Active Directory és az Azure AD összekötők jelennek meg.

    ![Metaverzum információ](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Jelölje ki az Azure AD-t jelölő sort, kattintson a **Tulajdonságok**gombra, majd a **Vonal** fülre. Az összekötő térobjektumnak a **PasswordSync** oszlopban egy kimenő szabálynak igaz értékre kell **állítava.** Az alapértelmezett konfigurációban a szinkronizálási szabály neve **Out to AAD - User Join**.  

    ![Összekötő térobjektum tulajdonságai párbeszédpanel](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Jelszó szinkronizálási naplója

Az állapotoszlop a következő értékeket tartalmazhatja:

| status | Leírás |
| --- | --- |
| Sikeres |A jelszó szinkronizálása sikeresen megtörtént. |
| FilteredByTarget |Jelszó van beállítva, hogy **a felhasználónak meg kell változtatni a jelszót a következő bejelentkezéskor**. A jelszó nincs szinkronizálva. |
| NoTargetConnection (NoTargetConnection) |Nincs objektum a metaverzumban vagy az Azure AD-összekötő térben. |
| SourceConnectorNotPresent |Nem található objektum a helyszíni Active Directory-összekötő térben. |
| TargetNotExportedToDirectory |Az objektum az Azure AD-összekötő térben még nem exportált. |
| MigratedCheckDetailsForMoreInfo |A naplóbejegyzés az 1.0.9125.0-s verzió létrehozása előtt jött létre, és az örökölt állapotában jelenik meg. |
| Hiba |A szolgáltatás ismeretlen hibát adott vissza. |
| Ismeretlen |Hiba történt a jelszókijelentések kötegének feldolgozása közben.  |
| MissingAttribute |Az Azure AD tartományi szolgáltatások által igényelt bizonyos attribútumok (például Kerberos-kivonat) nem érhetők el. |
| ÚjrapróbálkozásKérés |Az Azure AD tartományi szolgáltatások által igényelt speciális attribútumok (például Kerberos-kivonat) korábban nem voltak elérhetők. A rendszer kísérletet tesz a felhasználó jelszókivonatának újraszinkronizálására. |

## <a name="scripts-to-help-troubleshooting"></a>Parancsfájlok a hibaelhárításhoz

### <a name="get-the-status-of-password-sync-settings"></a>A jelszószinkronizálási beállítások állapotának beszereznie

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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Az összes jelszó teljes szinkronizálásának aktiválása

> [!NOTE]
> Csak egyszer futtassa ezt a parancsfájlt. Ha többször is futtatnia kell, valami más a probléma. A probléma elhárításához forduljon a Microsoft támogatási szolgálatához.

Az összes jelszó teljes szinkronizálását a következő parancsfájl segítségével indíthatja el:

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

* [Jelszókivonat-szinkronizálás megvalósítása az Azure AD Connect szinkronizálásával](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect Sync: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)