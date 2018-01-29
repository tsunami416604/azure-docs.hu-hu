---
title: "Az Azure AD Connect: Verziókiadások |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure AD Connect és az Azure AD Sync összes kiadásaiban"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2017
ms.author: billmath
ms.openlocfilehash: 815d2f289e18a97eff0a05ad1d7dfe4cad1fdfc5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="azure-ad-connect-version-release-history"></a>Az Azure AD Connect: Verziókiadások
Az Azure Active Directory (Azure AD) csapat rendszeresen frissíti az Azure AD Connect új szolgáltatásait és funkcióit. Nem minden kiegészítéseket célrendszerekhez vonatkoznak.
"Ez a cikk segítséget nyújtanak a kiadott verziók nyomon követheti, és tudni, hogy szükséges-e a legújabb verzióra frissíteni vagy nem úgy van kialakítva.

Ez az kapcsolódó témaköröket:



Témakör |  Részletek
--------- | --------- |
Az Azure AD Connect frissítésének lépései | A különböző módszereket [a legújabb verzióra a korábbi verzióról való frissítés](active-directory-aadconnect-upgrade-previous-version.md) az Azure AD Connect kiadás.
Szükséges engedélyek | Egy frissítés alkalmazásához szükséges engedélyek, lásd: [fiókok és engedélyek](./active-directory-aadconnect-accounts-permissions.md#upgrade).

Töltse le |} [Azure AD Connect letöltése](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="116540"></a>1.1.654.0
Állapot: December 12., 2017

>[!NOTE]
>Ez a biztonsági kapcsolódó gyorsjavítás az Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Egy fokozása hozzá lett adva az Azure AD Connect verzió 1.1.654.0 (és után) Győződjön meg arról, hogy az ajánlott engedély módosítja a szakaszban ismertetett alatt [hozzáférést az Active Directory tartományi szolgáltatások fiók zárolása](#lock) amikor automatikusan alkalmazza az Azure AD Csatlakozás a Tartományi fiókot hoz létre. 

- Az Azure AD Connect beállításakor a telepítése rendszergazdai megadhat egy létező Tartományi fiókot, vagy automatikusan hozza létre a fiókot az Azure AD Connect segítségével. Az engedély módosításai az AD DS-fiókjához, amely az Azure AD Connect telepítés során jön létre automatikusan érvényesek lesznek. Rendszer nem alkalmazza azokat a telepítés rendszergazda által biztosított meglévő AD DS-fiókjához.
- Ügyfeleink az Azure AD Connect egy korábbi verziójából származó 1.1.654.0 (vagy után), az engedély a módosításokat nem lehet visszamenőleges alkalmazva a frissítés előtt létrehozott meglévő Tartományi fiókot. Ezek csak meg kell alkalmazni, a frissítés után létrehozott új AD DS fiókok. Ez akkor fordul elő, amikor új AD-erdőhöz kell szinkronizálni az Azure AD hozzáadni kívánt.

>[!NOTE]
>Ez a kiadás csak a biztonsági rés az Azure AD Connect, ahol a szolgáltatás fiók a telepítési folyamat által létrehozott új telepítésére. Meglévő telepítésére, és azokban az esetekben, ahol meg kell adnia a fiók saját magának akkor győződjön meg arról, hogy a biztonsági rés nem létezik.

#### <a name="lock"></a>Az Active Directory tartományi szolgáltatások fiók eléréséhez zárolását
A következő engedély módosítások alkalmazásával segítse a helyszíni AD DS-fiókjához való hozzáférés zárolása AD:  

*   A megadott objektum öröklődés letiltása
*   Távolítsa el az adott objektum ACE-k meghatározott önmagára kivételével minden ACE-k. Szeretnénk az alapértelmezett engedélyek mindaddig módosulna, amikor a saját MAGA.
*   Ezek az engedélyek hozzárendelése:

Típus     | Name (Név)                          | Hozzáférés               | Erre vonatkozik
---------|-------------------------------|----------------------|--------------|
Engedélyezés    | RENDSZER                        | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Vállalati rendszergazdák             | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Tartományi rendszergazdák                 | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Rendszergazdák                | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Tartalmának listázása        | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Az összes tulajdonság olvasása  | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Olvasási engedélyek     | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Tartalmának listázása        | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Az összes tulajdonság olvasása  | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Olvasási engedélyek     | Ez az objektum  |

Szigoríthatja a beállításokat a az AD DS fiók, akkor futtassa [a PowerShell parancsfájl](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). A PowerShell parancsfájl fogja hozzárendelni az engedélyeket, az Active Directory tartományi szolgáltatások fiókhoz a fent említett.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell-parancsfájlt, hogy szigorítsa a már meglévő szolgáltatás-fiók

A PowerShell-parancsfájl segítségével alkalmazza ezeket a beállításokat egy már meglévő Active Directory tartományi szolgáltatások-fiókhoz (munkahelyi vagy az Azure AD Connect egy korábbi telepítés által létrehozott éter töltse le a parancsfájl a fent megadott hivatkozás.

##### <a name="usage"></a>Használat

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Ahol (a(z) 

**$ObjectDN** = engedélyeiket kell megfeszíteni kell az Active Directory-fiókot.

**$Credential** = rendszergazdájának hitelesítő adatait, amely rendelkezik a szükséges jogosultságokkal a $ObjectDN fiók engedélyek korlátozása. Ez általában a vállalat vagy a tartományi rendszergazda az. Használja a teljesen minősített tartománynevét a rendszergazdai fiók fiók keresési hibák elkerülése érdekében. Példa: contoso.com\admin.

>[!NOTE] 
>$credential. Felhasználónév FQDN\username formátumúnak kell lennie. Példa: contoso.com\admin 

##### <a name="example"></a>Példa:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>A biztonsági rés használt jogosulatlanul hozzáférést?

Megjelenítéséhez, ha a biztonsági rés szerepel-e be az Azure AD Connect konfigurálása, ellenőrizni kell az utolsó jelszó alaphelyzetbe szolgáltatásfiók dátum.  Ha a időbélyeg a váratlan, a további vizsgálatok keresztül az eseménynaplóban, hogy a jelszó alaphelyzetbe esemény, kell végezni.

További információkért lásd: [Microsoft biztonsági tanácsadó 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Állapot: Október 27 2017

>[!NOTE]
>A build ügyfélnek az Azure AD Connect automatikus frissítési szolgáltatás nem érhető el.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Rögzített probléma
* Rögzített verzió kompatibilitási probléma az Azure AD Connect és az Azure AD Connect Health Agent (sync) között. A probléma hatással van az ügyfelek, akik az Azure AD Connect helybeni frissítést verzióra 1.1.647.0 hajt végre, de a rendszerállapot-ügynöke 3.0.127.0 verziója van. A frissítés után a rendszerállapot-ügynöke már nem küldhet az Azure AD Connect szinkronizálási szolgáltatás állapotának adatait az Azure AD Health Service. A javítás a rendszerállapot-ügynöke 3.0.129.0 verziója telepítve van az Azure AD Connect helybeni frissítés során. Rendszerállapot-ügynöke 3.0.129.0 verzió nincs kompatibilitási probléma az Azure AD Connect 1.1.649.0 verziója.


## <a name="116470"></a>1.1.647.0
Állapot: Október 19 2017

> [!IMPORTANT]
> Nincs teljesen kompatibilis az Azure AD Connect 1.1.647.0 és az Azure AD Connect Health Agent (sync) verziója 3.0.127.0 között. A probléma megakadályozza, hogy a rendszerállapot-ügynöke állapotának adatait az Azure AD Connect szinkronizálási szolgáltatás (beleértve az objektumszinkronizálási hibák és futtatási előzményei adatok) küld az Azure AD Health Service. Manuálisan az Azure AD Connect telepítési verzióra 1.1.647.0 alkalmazásra történő verziófrissítés előtt ellenőrizze, hogy az Azure AD Connect Health Agent aktuális verziója, az Azure AD Connect-kiszolgálón. Ehhez a *Vezérlőpult → Programok telepítése/törlése* , és tekintse meg az alkalmazás *Microsoft Azure AD Connect Health szinkronizálási ügynöke*. Ha ugyanolyan verziójú legyen, 3.0.127.0, javasolt, hogy várja meg a következő az Azure AD Connect elérhetőek a frissítés előtt. A Health-ügynök verziója nem 3.0.127.0, akkor beleegyezik abba, hogy a manuális, helybeni frissítés folytatásához. Vegye figyelembe, hogy ez a probléma nem érinti a mozgó frissítése vagy az ügyfelek, akik az Azure AD Connect új telepítést hajt végre.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Javított problémák
* Rögzített kapcsolatos problémát a *felhasználói bejelentkezés módosítása* feladatot az Azure AD Connect varázsló:

  * A probléma akkor fordul elő, ha egy meglévő Azure AD Connect telepítés jelszó-szinkronizálás **engedélyezett**, és állítsa be a felhasználói bejelentkezési módszert az kívánt *áteresztő hitelesítés*. Mielőtt a rendszer alkalmazza a módosítást, a varázsló helytelenül jeleníti meg a "*jelszó-szinkronizálás letiltása*" parancssorba. Azonban a jelszó-szinkronizálás engedélyezett marad a módosítás alkalmazása után. A javítás, a varázsló nem jeleníti meg, a parancssorba.

  * Úgy lett kialakítva, a varázsló nem tiltja le a jelszó-szinkronizálás a felhasználói bejelentkezési módszerrel történő frissítésekor a *felhasználói bejelentkezés módosítása* feladat. Ez egy, az ügyfél számára, aki szeretné megőrizni a jelszó-szinkronizálás, annak ellenére, hogy átmenő hitelesítést vagy az elsődleges felhasználói bejelentkezési módszer összevonási engedélyezi megszűnésének megelőzése érdekében.
  
  * Ha a jelszó-szinkronizálás letiltása a felhasználó bejelentkezési módszer frissítése után, végre kell hajtani a *testre szabhatja a szinkronizálási konfiguráció* feladatütemezés a varázslóban. Kiválasztásakor a *választható szolgáltatások* lapon, törölje a jelet a *jelszó-szinkronizálás* lehetőséget.
  
  * Vegye figyelembe, hogy ugyanilyen is akkor fordul elő, ha megpróbálja engedélyezését vagy letiltását zökkenőmentes egyszeri bejelentkezést. Pontosabban, jelszó-szinkronizálás engedélyezve van egy meglévő Azure AD Connect telepítés és a felhasználói bejelentkezési módszer már be van állítva *áteresztő hitelesítés*. Használatával a *felhasználói bejelentkezés módosítása* feladat, próbálja ellenőrizze vagy törölje a jelet a *engedélyezése zökkenőmentes egyszeri bejelentkezést* miközben a felhasználó bejelentkezési módszer továbbra is a beállított "Áteresztő hitelesítés" lehetőséget. Mielőtt a rendszer alkalmazza a módosítást, a varázsló helytelenül jeleníti meg a "*jelszó-szinkronizálás letiltása*" parancssorba. Azonban a jelszó-szinkronizálás engedélyezett marad a módosítás alkalmazása után. A javítás, a varázsló nem jeleníti meg, a parancssorba.

* Rögzített kapcsolatos problémát a *felhasználói bejelentkezés módosítása* feladatot az Azure AD Connect varázsló:

   * A probléma akkor fordul elő, ha egy meglévő Azure AD Connect telepítés jelszó-szinkronizálás **le van tiltva**, és állítsa be a felhasználói bejelentkezési módszert az kívánt *áteresztő hitelesítés*. Amikor a rendszer alkalmazza a módosítást, a varázsló lehetővé teszi, hogy átmenő hitelesítést és a jelszó-szinkronizálás. A javítás a varázsló már nem engedélyezi a jelszó-szinkronizálás.

  * A jelszó-szinkronizálás korábban előfeltételeként áteresztő hitelesítés engedélyezése. A felhasználói bejelentkezési módszert az beállításakor *áteresztő hitelesítés*, a varázsló lehetővé teszik az áteresztő hitelesítés és a jelszó-szinkronizálás. Nemrég jelszó-szinkronizálás előfeltételként el lett távolítva. Az Azure AD Connect verzió 1.1.557.0 részeként, a módosítás az Azure AD Connect nem jelszó-szinkronizálás engedélyezése, ha úgy állítja be a felhasználói bejelentkezési módszert az *áteresztő hitelesítés*. A módosítás azonban csak alkalmazta az Azure AD Connect telepítés. A javítás ezt a változtatást is vonatkozik a *felhasználói bejelentkezés módosítása* feladat.
  
  * Vegye figyelembe, hogy ugyanilyen is akkor fordul elő, ha megpróbálja engedélyezését vagy letiltását zökkenőmentes egyszeri bejelentkezést. Pontosabban, úgy, hogy egy meglévő Azure AD Connect telepítés le van tiltva a jelszó-szinkronizálás és a felhasználói bejelentkezési módszer már be van állítva *áteresztő hitelesítés*. Használatával a *felhasználói bejelentkezés módosítása* feladat, próbálja ellenőrizze vagy törölje a jelet a *engedélyezése zökkenőmentes egyszeri bejelentkezést* miközben a felhasználó bejelentkezési módszer továbbra is a beállított "Áteresztő hitelesítés" lehetőséget. Amikor a rendszer alkalmazza a módosítást, a varázsló lehetővé teszi, hogy a jelszó-szinkronizálás. A javítás a varázsló már nem engedélyezi a jelszó-szinkronizálás. 

* Hiba: az Azure AD Connect frissítése sikertelen okozó problémát rögzített "*nem lehet a Synchronization Service frissítése*". További, a szinkronizálási szolgáltatás nem indítható esemény hibával "*a szolgáltatás nem tudta elindítani, mert az adatbázis verziója újabb, mint a bináris fájljai telepítve*". A probléma akkor fordul elő, amikor végrehajtja a frissítést a rendszergazda nem rendelkezik sysadmin jogosultsággal az SQL Server által az Azure AD Connect használt. A javítás az Azure AD Connect csak szükséges, a rendszergazda az ADSync adatbázis db_owner jogosultsággal rendelkezik a frissítés során.

* Az Azure AD Connect frissítése, engedélyezte a felhasználókat érintő problémát rögzített [zökkenőmentes egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Után az Azure AD Connect frissítve van, zökkenőmentes egyszeri bejelentkezést helytelenül jelenik meg le van tiltva, az Azure AD Connect varázsló, annak ellenére, hogy a szolgáltatás engedélyezett és teljesen működőképes maradjon. A javítás a szolgáltatás csomópontként jelenik meg helyesen az engedélyezett, a varázslóban.

* Megtörtént egy probléma javítása, ami miatt az Azure AD Connect varázsló mindig megjelenítése a "*Forráshorgony konfigurálása*" a parancssor a *készen áll a konfigurálás* lapon, akkor is, ha nincs Forráshorgony kapcsolatos módosítások történtek.

* Kézi frissítés az Azure AD Connect hajtja végre, amikor az ügyfél szükséges adja meg a megfelelő Azure AD-bérlő globális rendszergazdája. Korábban, a frissítés folytatható sikerült-e annak ellenére, hogy a megadott globális rendszergazdai hitelesítő adatok tartozik egy másik Azure AD-bérlő. Amíg a frissítés úgy tűnik, hogy sikeresen befejeződik, bizonyos konfigurációk nem megfelelően megmaradnak a frissítést. A módosítás a varázsló nem engedélyezi a frissítés gombra, ha a megadott hitelesítő adatok nem felelnek meg az Azure AD-bérlő.

* Eltávolítja a redundáns logika, amely feleslegesen a kézi frissítés kezdetén az Azure AD Connect Health szolgáltatás újraindítása.


#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit
* A hozzáadott logika egyszerűsítése érdekében a Microsoft Németország felhőalapú Azure AD Connect beállításához szükséges lépéseket. Korábban akkor is frissíteni az Azure AD Connect kiszolgálón ahhoz, hogy a megfelelő működéséhez a Microsoft Németország felhőalapú, meghatározott beállításkulcsokról, ebben a cikkben leírtak szerint. Most az Azure AD Connect automatikusan észleli Ha a bérlő Microsoft Németország felhőben alapján a telepítés során megadott globális rendszergazdai hitelesítő adatok.

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
>[!NOTE]
> Megjegyzés: A szinkronizálási szolgáltatás, amely lehetővé teszi a saját egyéni Feladatütemező fejlesztése WMI felülettel rendelkezik. Ez az interfész már elavult, és törlődnek a jövőbeli verzióiban az Azure AD Connect rendszerrel szállított után 2018. június 30. Az ügyfelek, akik testre szeretne szabni a szinkronizálási ütemezés kell használnia a [beépített ütemezési (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Javított problémák
* Az Azure AD Connect varázsló a szükséges szinkronizálni a módosításokat a helyszíni Active Directoryból Címtárösszekötőben fiókot hoz létre, ha azt nem megfelelően rendelje hozzá a fiók PublicFolder objektumok olvasásához szükséges engedéllyel. A probléma a gyors telepítés és a testreszabott telepítés. Ez a változás megoldja a problémát.

* Megtörtént egy probléma javítása, ami miatt az Azure AD Connect varázsló hibaelhárítási lap nem jelenik meg megfelelően fut a Windows Server 2016-rendszergazdák számára.

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit
* Hibáinak elhárítása az Azure AD Connect varázsló segítségével a jelszó-szinkronizálás, akkor a hibaelhárítás lapon most az tartományspecifikus állapot adja vissza.

* Korábban, ha engedélyezi a Jelszókivonat-szinkronizálást próbált, az Azure AD Connect nem ellenőrzi, hogy az AD-összekötő fiókhoz szükséges engedélyek jelszókivonatait a szinkronizálandó a helyszíni AD. Most az Azure AD Connect varázsló ellenőrizze és figyelmezteti, ha az AD-összekötő fiók nem rendelkezik megfelelő engedélyekkel.

### <a name="ad-fs-management"></a>AD FS-Szolgáltatáskezelés
#### <a name="fixed-issue"></a>Rögzített probléma
* Megtörtént egy probléma javítása használatához kapcsolódó [msDS-ConsistencyGuid adatforrás kapcsolatainak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) szolgáltatás. Ez a probléma kihat az ügyfelek, akik konfigurált *az AD FS összevonási* a felhasználói bejelentkezési módszert. Végrehajtása közben *Forráshorgony konfigurálása* feladata a varázslót, az Azure AD Connect használatával vált * ms-DS-ConsistencyGuid, immutableId adatforrás-attribútum. Ez a változás részeként az Azure AD Connect megkísérli a jogcímszabályok frissítése az AD FS ImmutableId. Azonban ez a lépés nem sikerült, mert az Azure AD Connect nem volt az Active Directory összevonási szolgáltatások konfigurálásához szükséges rendszergazdai hitelesítő adatok. A javítás az Azure AD Connect most megadását kéri a rendszergazdai hitelesítő adatokat az AD FS végrehajtása közben a *Forráshorgony konfigurálása* feladat.



## <a name="116140"></a>1.1.614.0
Állapot: Szeptember 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Ismert problémák
* Egy ismert probléma az Azure AD Connect frissítése sikertelen a következő hiba okozza "*nem lehet a Synchronization Service frissítése*". További, a szinkronizálási szolgáltatás nem indítható esemény hibával "*a szolgáltatás nem tudta elindítani, mert az adatbázis verziója újabb, mint a bináris fájljai telepítve*". A probléma akkor fordul elő, amikor végrehajtja a frissítést a rendszergazda nem rendelkezik sysadmin jogosultsággal az SQL Server által az Azure AD Connect használt. Dbo engedélyek nem elegendőek.

* Egy ismert probléma az Azure AD Connect frissítése, hogy engedélyezte a felhasználókat érinti [zökkenőmentes egyszeri bejelentkezést](active-directory-aadconnect-sso.md). Miután az Azure AD Connect frissítve van, a szolgáltatás megjelenik a le van tiltva, a varázslóban annak ellenére, hogy a szolgáltatás továbbra is engedélyezett marad. Egy javítást a probléma a jövőben megadni kiadási. Felhasználók, akiket megjelenítési problémáról az érintett manuálisan megoldhatja a varázsló zökkenőmentes egyszeri bejelentkezés engedélyezése.

#### <a name="fixed-issues"></a>Javított problémák
* Megtörtént egy probléma javítása, amely megakadályozza, hogy a Azure AD Connect frissítése a jogcímszabályokat a helyszíni AD FS a lemezt, és engedélyezze a [msDS-ConsistencyGuid adatforrás kapcsolatainak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) szolgáltatás. A probléma akkor fordul elő, ha egy meglévő Azure AD Connect telepítését, amelyek az AD FS bejelentkezési módszerként konfigurálva van a funkció engedélyezéséhez. A probléma akkor fordul elő, mert a varázsló nem kéri a felhasználót az AD FS hitelesítő adatok előtt próbálja frissíteni a jogcímszabályokat a az AD FS.
* Megtörtént egy probléma javítása, ami miatt az Azure AD Connect telepítése sikertelen lesz, ha a helyszíni AD-erdővel rendelkezik, az NTLM le van tiltva. A probléma okozza-e a Kerberos-hitelesítés szükséges biztonsági környezet létrehozásakor nem adja meg teljesen minősített hitelesítő adatokat az Azure AD Connect varázslót. Ennek hatására a Kerberos-hitelesítés sikertelen lesz, és az Azure AD Connect varázsló visszaállni NTLM használatával.

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
#### <a name="fixed-issues"></a>Javított problémák
* Rögzített problémát, ahol új szinkronizálási szabály nem hozható létre, ha a kód attribútum nincs kitöltve.
* Megtörtént egy probléma javítása, ami miatt az Azure AD Connect való kapcsolódáshoz a helyszíni AD az NTLM, jelszó-szinkronizálással annak ellenére, hogy a Kerberos érhető el. Ez a probléma akkor fordul elő, ha a helyszíni AD-topológia rendelkezik legalább egy biztonsági másolatból visszaállított tartományvezérlők.
* Megtörtént egy probléma javítása, ami miatt a teljes szinkronizálás lépéseket feleslegesen megtörténik a frissítés után. Általában a teljes szinkronizálás lépéseket futtató esetén szükséges frissítést követően out-of-box szinkronizálási szabályai módosultak. A probléma volt a változás az észlelési logika, amely nem megfelelően változást észlelt, amikor a szinkronizálási szabály kifejezés soremelés karaktereket hajt hiba miatt. Új sor karaktereket kerülnek szinkronizálási szabály kifejezés olvashatóságának javítása érdekében.
* Megtörtént egy probléma javítása, amelyek az Azure AD Connect-kiszolgáló nem megfelelő működéséhez az automatikus frissítés után okozhatják. Ez a probléma kihat az Azure AD Connect-kiszolgálók 1.1.443.0 verziójával (vagy korábbi). A problémával kapcsolatos részletekért tekintse meg a cikk [az Azure AD Connect nem megfelelően működik az automatikus frissítés után](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Megtörtént egy probléma javítása, amelyek az automatikus frissítés át 5 percenként, amikor hiba történik, újra megpróbálja okozhatják. A javítás automatikus frissítése újrapróbálkozások exponenciális vissza off amikor hiba történik.
* Megtörtént egy probléma javítása ahol jelszó-szinkronizálás esemény 611 helytelenül megjelenik-e a Windows esemény-naplók és **tájékoztató** helyett **hiba**. 611 esemény jön létre, amikor a jelszó-szinkronizálás problémát észlel. 
* Megtörtént egy probléma javítása, amely lehetővé teszi, hogy a csoport visszaírási funkciót egy szervezeti Egységet a csoportok visszaírásához szükséges kijelölése nélkül engedélyezni kell az Azure AD Connect varázsló.

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit
* További feladatok az Azure AD Connect varázsló egy hibaelhárítása a feladat hozzá. Az ügyfelek kihasználhatják ezt a feladatot a jelszó-szinkronizálás kapcsolatos problémák elhárítása és általános diagnosztikai adatok gyűjtéséhez. A jövőben a hibaelhárítása a feladat fog kell terjeszteni más címtár-szinkronizálás kapcsolatos problémák.
* Az Azure AD Connect most támogatja egy új telepítési mód néven **létező adatbázis használata**. Ez a telepítési mód lehetővé teszi az ügyfelek telepítése az Azure AD Connect, amely megadja egy meglévő ADSync adatbázis. Ezzel a funkcióval kapcsolatban további információkért tekintse meg a cikk [meglévő adatbázis használata](active-directory-aadconnect-existing-database.md).
* A nagyobb biztonság érdekében az Azure AD Connect most alapértelmezés szerint az TLS1.2 segítségével csatlakozzon az Azure AD-címtár-szinkronizálás. Az alapértelmezett korábban TLS1.0.
* Az Azure AD Connect jelszó-szinkronizálási ügynök indulásakor próbál csatlakozni a jelszó-szinkronizáláshoz az Azure AD a jól ismert végpontjához. A kapcsolat létrejötte után azt a rendszer átirányítja egy régióspecifikus végpontot. Korábban a jelszó-szinkronizálási ügynök gyorsítótárazza a régióspecifikus végpont, amíg újra nem indítja. Most az ügynök kiüríti a gyorsítótárat, és a jól ismert végponttal újrapróbálkozások ha ütközik a régióspecifikus végponttal csatlakozási probléma. Ez a módosítás biztosítja, hogy a jelszó-szinkronizálás másik régióspecifikus endpoint feladatátvétel történhet, ha a gyorsítótárazott régióspecifikus végpont már nem érhető el.
* Szinkronizálni a módosításokat a helyszíni AD-erdőben, az AD DS-fiókjához szükség. I hozhat az AD DS fiókot saját maga és adja meg a hitelesítő adatok az Azure AD Connect, vagy (ii) adjon meg egy vállalati rendszergazdai hitelesítő adatait, és lehetővé teszik az Azure AD Connect hozza létre az AD DS-fiókjához. (I) korábban, az Azure AD Connect varázsló az alapértelmezett beállítás. Most (ii) az alapértelmezett beállítás.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit
* A Microsoft Azure Government felhő és a Microsoft Cloud Németország támogatása.

### <a name="ad-fs-management"></a>AD FS-Szolgáltatáskezelés
#### <a name="fixed-issues"></a>Javított problémák
* Az Initialize-ADSyncNGCKeysWriteBack parancsmag a AD előkészítő powershell-modult az eszköz regisztrációja tároló ACL'ing helytelenül lett, és csak így öröklik vonatkozó engedélyeket.  Ez frissítve lett, hogy a szinkronizálási szolgáltatás fiókja rendelkezik-e megfelelő engedélyekkel.

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit
* Az AAD Connect ellenőrizze az AD FS bejelentkezési tevékenység frissült, így azt ellenőrzi, hogy a Microsoft Online és az AD FS csak token lekérését történő bejelentkezéseket.
* AAD-csatlakozás használatával új AD FS-farm beállításakor a lapot az AD FS hitelesítő adatok kérése áthelyezték azt, hogy most előtt adja meg az AD FS-és WAP kér a felhasználótól.  Ez lehetővé teszi, hogy AAD-csatlakozás ellenőrizze, hogy a megadott fiók rendelkezik-e megfelelő engedélyekkel.
* AAD-csatlakozás frissítés során már nem azt meghiúsul frissítés, ha az AD FS AAD megbízható nem tud frissíteni.  Ebben az esetben, ha a felhasználó jelenik meg egy megfelelő figyelmeztető üzenetet, és alaphelyzetbe állítja az AAD-csatlakozás további feladattal megbízhatósági kell eljárnia.

### <a name="seamless-single-sign-on"></a>Zökkenőmentes egyszeri bejelentkezést.
#### <a name="fixed-issues"></a>Javított problémák
* Vissza a hiba, ha próbálja meg újból engedélyezni az Azure AD Connect varázsló okozó problémát rögzített [zökkenőmentes egyszeri bejelentkezést](active-directory-aadconnect-sso.md). A hibaüzenet a következő: *"Microsoft Azure AD Connect hitelesítési ügynök nem tudott konfiguráció".* Ezt a hibát észleli a meglévő felhasználókat manuálisan kellett frissítése előzetes verzióját, a hitelesítési ügynökök [áteresztő hitelesítés](active-directory-aadconnect-sso.md) leírt lépésekkel alapján [cikk](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Állapot: Július 23 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rögzített probléma

* Megtörtént egy probléma javítása, ami miatt a out-of-box szinkronizálási szabály "Az AD - felhasználó ImmutableId Out" távolíthatók el:

  * A probléma akkor fordul elő, ha az Azure AD Connect frissítve van, vagy ha a feladat beállítás *szinkronizálás konfigurációjának frissítése* az Azure AD Connect varázsló használata az Azure AD Connect-szinkronizálás konfigurációjának frissítése.
  
  * A szinkronizálási szabály akkor alkalmazható, az ügyfél számára, aki engedélyezve van a [msDS-ConsistencyGuid Forráshorgony szolgáltatás](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). Ez a szolgáltatás verzióját 1.1.524.0 és követően jelent meg. A szinkronizálási szabály eltávolítása után az Azure AD Connect nem töltheti fel a helyszíni AD-ms-DS-ConsistencyGuid attribútum ObjectGuid attribútum értéke. Nem akadályozza meg azt, hogy a felhasználók új létre az Azure AD-be.
  
  * A javítás biztosítja, hogy a szinkronizálási szabály már nem eltávolítja a frissítés során, vagy során konfigurációmódosítás, mindaddig, amíg a szolgáltatás engedélyezve van. A meglévő ügyfelek, akik a probléma által érintett a javítás is biztosítja, hogy a szinkronizálási szabály legyen-e adva az Azure AD Connect ezen verziójára való frissítés után vissza.

* Rögzített out-of-box szinkronizálási szabályait, amely 100-nál kevesebb élvez értéknek kell okozó hibát:

  * Általában elsőbbséget értékek 0 – 99 egyéni szinkronizálási szabályok számára vannak fenntartva. A frissítés során a sorrend out-of-box szinkronizálási szabályok vannak frissítve szinkronizálási szabály módosítások alkalmazásához. Ez nem látható probléma miatt out-of-box szinkronizálási szabályok rendelt sorrend értéke 100-nál kisebb.
  
  * A probléma megakadályozza a problémát a frissítés során. Azonban ez nem állítja vissza a sorrend értékek a meglévő ügyfelek, akik a probléma által érintett. Egy különálló javítást a jövőben nyújtanak segítséget az a visszaállítást.

* Megtörtént egy probléma javítása ahol a [tartományok és szervezeti egységek szűrése képernyő](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) az az Azure AD Connect varázsló azt *összes tartományok és szervezeti egységek szinkronizálása* lehetőség választása, annak ellenére, hogy a szervezeti egység-alapú szűrés engedélyezve van.

*   Megtörtént egy probléma javítása, ami miatt a [címtárpartíciók konfigurálása képernyő](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) a a Synchronization Service Managert vissza a hiba, ha a *frissítése* gombra kattint. A hibaüzenet a következő: *"tartományok frissítése során hiba történt: nem konvertálható"System.Collections.ArrayList"típusról típusú objektum" Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject. "* A hiba akkor fordul elő, amikor új AD-tartomány hozzáadása egy meglévő AD-erdőhöz, és a frissítés gombra kattintva az Azure AD Connect frissítése kívánt.

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit

* [Automatikus frissítési szolgáltatás](active-directory-aadconnect-feature-automatic-upgrade.md) ki lett terjesztve, az alábbi beállításokkal rendelkező ügyfelek támogatásához:
  * Az eszköz visszaírási funkció engedélyezését.
  * A csoport visszaírási funkció engedélyezését.
  * A telepítés nincs egy expressz beállításokat, vagy a DirSync frissítését.
  * Több mint 100 000 objektummal rendelkezik a metaverzumban.
  * Több erdő csatlakozik. A gyorstelepítés csak egy erdő csatlakozik.
  * Az AD-összekötő fiókhoz már nem az alapértelmezett MSOL_ fiókot.
  * A kiszolgáló átmeneti módra beállítása.
  * A felhasználó-visszaírás szolgáltatása engedélyezését.
  
  >[!NOTE]
  >A hatókör bővítése az automatikus frissítési szolgáltatás az Azure AD Connect build 1.1.105.0 és után ügyfeleket érinti. Ha nem szeretné, hogy az Azure AD Connect-kiszolgáló automatikusan frissítve lesz, meg kell futtassa a következő parancsmagot az Azure AD Connect-kiszolgálón: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Engedélyezése vagy tiltása automatikus frissítése kapcsolatos további információkért tekintse meg a cikk [az Azure AD Connect: automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Állapot: Nem lesz feloldva. A build változásai verzió 1.1.561.0 szerepelnek.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rögzített probléma

* Megtörtént egy probléma javítása, ami miatt a out-of-box szinkronizálási szabály "Az AD - felhasználó ImmutableId Out" OU-alapú szűrési konfiguráció frissítésekor távolíthatók el. A szinkronizálási szabály szükség a [msDS-ConsistencyGuid Forráshorgony szolgáltatás](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).

* Megtörtént egy probléma javítása ahol a [tartományok és szervezeti egységek szűrése képernyő](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) az az Azure AD Connect varázsló azt *összes tartományok és szervezeti egységek szinkronizálása* lehetőség választása, annak ellenére, hogy a szervezeti egység-alapú szűrés engedélyezve van.

*   Megtörtént egy probléma javítása, ami miatt a [címtárpartíciók konfigurálása képernyő](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) a a Synchronization Service Managert vissza a hiba, ha a *frissítése* gombra kattint. A hibaüzenet a következő: *"tartományok frissítése során hiba történt: nem konvertálható"System.Collections.ArrayList"típusról típusú objektum" Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject. "* A hiba akkor fordul elő, amikor új AD-tartomány hozzáadása egy meglévő AD-erdőhöz, és a frissítés gombra kattintva az Azure AD Connect frissítése kívánt.

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit

* [Automatikus frissítési szolgáltatás](active-directory-aadconnect-feature-automatic-upgrade.md) ki lett terjesztve, az alábbi beállításokkal rendelkező ügyfelek támogatásához:
  * Az eszköz visszaírási funkció engedélyezését.
  * A csoport visszaírási funkció engedélyezését.
  * A telepítés nincs egy expressz beállításokat, vagy a DirSync frissítését.
  * Több mint 100 000 objektummal rendelkezik a metaverzumban.
  * Több erdő csatlakozik. A gyorstelepítés csak egy erdő csatlakozik.
  * Az AD-összekötő fiókhoz már nem az alapértelmezett MSOL_ fiókot.
  * A kiszolgáló átmeneti módra beállítása.
  * A felhasználó-visszaírás szolgáltatása engedélyezését.
  
  >[!NOTE]
  >A hatókör bővítése az automatikus frissítési szolgáltatás az Azure AD Connect build 1.1.105.0 és után ügyfeleket érinti. Ha nem szeretné, hogy az Azure AD Connect-kiszolgáló automatikusan frissítve lesz, meg kell futtassa a következő parancsmagot az Azure AD Connect-kiszolgálón: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Engedélyezése vagy tiltása automatikus frissítése kapcsolatos további információkért tekintse meg a cikk [az Azure AD Connect: automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Állapot: Július 2017

>[!NOTE]
>A build ügyfélnek az Azure AD Connect automatikus frissítési szolgáltatás nem érhető el.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rögzített probléma
* Megtörtént egy probléma javítása az Initialize-ADSyncDomainJoinedComputerSync parancsmaggal, ami miatt a meglévő szolgáltatási pont objektum módosítható, még akkor is, ha még mindig érvényes tartományt konfigurálni az ellenőrzött tartomány. Ez a probléma akkor fordul elő, ha az Azure AD-bérlő több ellenőrzött tartomány, a szolgáltatáskapcsolódási pont konfigurálásához használható.

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit
* Jelszóvisszaírás már elérhető a Microsoft Azure Government felhő és a Microsoft Cloud Németország Preview. A különböző szolgáltatáspéldány az Azure AD Connect-támogatással kapcsolatos további információkért tekintse meg a cikk [az Azure AD Connect: szempontjai példányok](active-directory-aadconnect-instances.md).

* Az Initialize-ADSyncDomainJoinedComputerSync parancsmaggal most már AzureADDomain nevű új nem kötelező paraméter. Ez a paraméter lehetővé teszi, hogy adható meg, amely a szolgáltatáskapcsolódási pont konfigurálásához használt tartomány ellenőrzése.

### <a name="pass-through-authentication"></a>Átmenő hitelesítés

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit
* Az áteresztő hitelesítés módosult szükséges ügynök neve *Microsoft Azure AD alkalmazásproxy-összekötő* való *Microsoft Azure AD Connect hitelesítési ügynök*.

* Alapértelmezés szerint áteresztő hitelesítés engedélyezése nem engedélyezi a Jelszókivonat-szinkronizálást.


## <a name="115530"></a>1.1.553.0
Állapot: Június 2017

> [!IMPORTANT]
> A build bevezetett séma- és szinkronizálási szabály módosítások vannak. Az Azure AD Connect szinkronizálási szolgáltatás teljes importálást és teljes szinkronizálást lépéseket vált frissítés után. A módosítások részleteit az alábbiakban található. Frissítés után ideiglenesen késlelteti a teljes importálást és teljes szinkronizálást lépéseket, tekintse meg a cikk [hogyan késlelteti a frissítés után teljes szinkronizálást](active-directory-aadconnect-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása

#### <a name="known-issue"></a>Ismert hiba
* Használó ügyfelek érintő probléma van a [OU-alapú szűrés](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) és az Azure AD Connect-szinkronizálás. Kiválasztásakor a [tartományok és szervezeti egységek szűrése lap](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) az Azure AD Connect varázsló a következő viselkedés várható:
  * Ha OU-alapú szűrés engedélyezve van, a **szinkronizálása a kiválasztott tartományok és szervezeti egységek** beállítást.
  * Ellenkező esetben a **összes tartományok és szervezeti egységek szinkronizálása** beállítást.

A problémát, amely merül fel, hogy a **összes tartományok és szervezeti egységek beállítás szinkronizálása** mindig van jelölve, a varázsló futtatásakor.  Ez akkor fordul elő, akkor is, ha a szervezeti egység alapú szűrés korábban beállított. AAD-csatlakozás konfigurációs módosítások mentése előtt győződjön meg arról, hogy a **szinkronizálása a kiválasztott tartományok és szervezeti egységek beállítást** , és győződjön meg arról, hogy minden hozzá szinkronizálnia kell újra vannak engedélyezve. Ellenkező esetben OU-alapú szűrés letiltásra kerül.

#### <a name="fixed-issues"></a>Javított problémák

* Megtörtént egy probléma javítása a jelszóvisszaírás, amely lehetővé teszi az Azure AD a rendszergazda egy helyszíni jelszavának alaphelyzetbe állítása AD emelt szintű felhasználói fiókot. A probléma akkor fordul elő, amikor az Azure AD Connect a jelszó alaphelyzetbe állítása engedélyt kap a rendszerjogosultságú fiók keresztül. A problémával ezen verziója nem teszi lehetővé az Azure AD a rendszergazda egy tetszőleges helyszíni jelszavának alaphelyzetbe állítása által az Azure AD Connect AD emelt szintű felhasználói fiók kivéve, ha a rendszergazda az adott fiók tulajdonosának. További információkért tekintse meg [biztonsági tanácsadó 4033453](https://technet.microsoft.com/library/security/4033453).

* Rögzített kapcsolatos problémát a [msDS-ConsistencyGuid adatforrás kapcsolatainak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkció, ahol az Azure AD Connect does nem visszaírását a helyszíni AD msDS-ConsistencyGuid attribútum. A probléma akkor fordul elő, ha több helyszíni AD-erdőhöz hozzá az Azure AD Connect és a *felhasználók identitásai több könyvtárak beállítás léteznek* van kiválasztva. Ilyen konfiguráció használata esetén a eredő szinkronizálási szabályok nem sikerült adatokkal feltölteni a sourceAnchorBinary attribútum tartozik a Metaverzumba. A sourceAnchorBinary attribútum az msDS-ConsistencyGuid attribútum a forrásattribútum szolgál. Ennek eredményeképpen az ms-DSConsistencyGuid attribútum visszaírását nem történik meg. Hárítsa el a problémát, a következő szinkronizálási szabályok frissített győződjön meg arról, hogy a rendszer mindig tölti fel a sourceAnchorBinary attribútum tartozik a Metaverzumba:
  * Az ad - InetOrgPerson AccountEnabled.xml
  * Az ad - InetOrgPerson Common.xml
  * Az ad - felhasználó AccountEnabled.xml
  * Az ad - felhasználó Common.xml
  * Az ad - felhasználó csatlakoztatnia SOAInAAD.xml

* Korábban akkor is, ha a [msDS-ConsistencyGuid adatforrás kapcsolatainak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkció nincs engedélyezve, a "Az AD-felhasználó ImmutableId Out" szinkronizálási szabály továbbra is hozzáadódik az Azure AD Connect. A hatás ártalmatlanok, és megtörténik az msDS-ConsistencyGuid attribútum visszaírása nem okoz. Félreértések elkerülése érdekében logika bővült ellenőrizze, hogy a szinkronizálási szabály csak fel, ha a szolgáltatás engedélyezve van.

* Rögzített, ami miatt a Jelszókivonat-szinkronizálást sikertelen 611 hiba eseménnyel kapcsolatos problémát. Ez a probléma akkor fordul elő, amikor valamelyik vagy több tartomány tartományvezérlői el lettek távolítva a helyszíni AD. Egyes jelszó-szinkronizálási ciklusok végén kiadott a szinkronizálási cookie-t a helyszíni AD meghívási azonosító eltávolított tartományvezérlő 0 értékű USN (frissítési sorszám) tartalmaz. A jelszószinkronizálás-kezelő nem tudja megőrizni a szinkronizálási cookie-t tartalmazó USN értéke 0, és hibaesemény 611 hibát jelez. A következő szinkronizálási ciklusban a jelszószinkronizálás-kezelő rendszer újból felhasználja az utolsó megőrzött szinkronizálási cookie-t, amely nem tartalmaz USN értéke 0. Ez azt eredményezi, hogy ugyanazt a jelszómódosítást újra kell szinkronizálni. A javítás a jelszószinkronizálás-kezelő továbbra is fennáll a szinkronizálási cookie-k megfelelően.

* Korábban még akkor is, ha az automatikus frissítés le van tiltva a Set-ADSyncAutoUpgrade parancsmag használatával, az automatikus frissítési folyamat továbbra is fennáll, keressen rendszeresen frissíteni, és a letöltött telepítőt megfelelő tiszteletben támaszkodik. A javítás az automatikus frissítési folyamat már nem ellenőrzi a frissítésre rendszeres időközönként. A javítás automatikusan alkalmazza az adott Azure AD Connect verzió frissítési telepítő egyszer végrehajtásakor.

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit

* Korábban a [msDS-ConsistencyGuid adatforrás kapcsolatainak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) szolgáltatás volt elérhető új központi telepítéséhez csak. Most érhető el a meglévő telepítések. Pontosabban:
  * A szolgáltatás elérése, az Azure AD Connect varázsló elindításához, és válassza ki a *frissítés Forráshorgony* lehetőséget.
  * Ez a beállítás csak akkor sourceAnchor attribútumaként objectGuid-t használó meglévő telepítések számára látható.
  * Amikor konfigurálja a beállítást, a varázsló ellenőrzi a helyszíni Active Directoryban az msDS-ConsistencyGuid attribútum állapotát. Ha az attribútum a user objektum a címtárban nincs konfigurálva, a varázsló az msDS-ConsistencyGuid használ a sourceAnchor attribútum. Ha az attribútum egy vagy több felhasználói objektumok a könyvtárban van konfigurálva, a varázsló azt állapítja meg az attribútum más alkalmazások használják, és nem felel meg a sourceAnchor attribútum, és nem teszi lehetővé a Forráshorgony módosítása a folytatáshoz. Ha biztos benne, hogy az attribútum a meglévő alkalmazásokat nem használja, további információt a hibát forduljon a támogatási szeretné.

* Jellemző **userCertificate** eszközobjektumot az Azure AD Connect most attribútum keresi a szükséges tanúsítványok értékek [tartományhoz csatlakozó eszközök csatlakoztatása az Azure AD-hez Windows 10-élmény](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) és kiszűri a többi Azure ad szinkronizálása előtt. Ahhoz, hogy ez a viselkedés, a out-of-box szinkronizálási szabály "Ki az aad-ben – eszköz csatlakozás SOAInAD" frissítve lett.

* Az Azure AD Connect mostantól támogatja a visszaírási az Exchange Online **cloudPublicDelegates** attribútumot a helyszíni AD **publicDelegates** attribútum. Ez lehetővé teszi a forgatókönyv, ahol az Exchange Online-postaláda is jogosultságokat SendOnBehalfTo a helyszíni Exchange-postaládájába rendelkező felhasználók részére. A funkció, egy új "Az AD-felhasználó Exchange hibrid PublicDelegates visszaírási Out" out-of-box szinkronizálási szabály hozzá lett adva. A szinkronizálási szabály csak hozzá az Azure AD Connect, ha Exchange hibrid szolgáltatás engedélyezve van.

*   Szinkronizálása az Azure AD Connect mostantól támogatja a **altRecipient** Azure ad-attribútum. Ez a változás támogatásához következő out-of-box szinkronizálási szabályokat tartalmazza a szükséges Attribútumfolyam frissítve lett:
  * Az AD-felhasználó Exchange-ből
  * Az aad-be – felhasználói ExchangeOnline kívül
  
* A **cloudSOAExchMailbox** attribútum tartozik a Metaverzumba azt jelzi, hogy egy adott felhasználó vagy nem rendelkezik Exchange Online-postaláda. A definiálás közé tartoznak további Exchange Online RecipientDisplayTypes, ilyen berendezéseket és konferenciaterem postaládák környezetébe. Ahhoz, hogy ezt a módosítást, a cloudSOAExchMailbox attribútum, amely a "A az aad-ben – felhasználói Exchange hibrid", out-of-box szinkronizálási szabály definícióját az frissítése:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... csatlakoztatása a következő:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* A következő funkciócsoport létrehozásához szinkronizálási szabály kifejezések kezelni az userCertificate attribútum értékei tanúsítvány X509Certificate2-kompatibilis funkciók hozzáadása:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Válassza ezt:|
    |CertKeyAlgorithmParams|CertHashString|Ahol (a(z)|
    |||A következővel:|

* Lehetővé teszi az ügyfeleknek sAMAccountName domainNetBios és a csoport objektumainak domainFQDN, valamint a felhasználói objektumok distinguishedName flow egyéni szinkronizálási szabályok létrehozását a következő sémamódosítások jelentek meg:

  * MV-séma hozzáadott következő attribútumokat:
    * Csoport: AccountName
    * Csoport: domainNetBios
    * Csoport: domainFQDN
    * Személyesen: distinguishedName

  * Azure AD-összekötő séma hozzáadott következő attribútumokat:
    * Csoport: OnPremisesSamAccountName
    * Csoport: NetBiosName
    * Csoport: tartománynév
    * Felhasználó: OnPremisesDistinguishedName

* A ADSyncDomainJoinedComputerSync parancsmag parancsfájl most már AzureEnvironment nevű új nem kötelező paraméter. A paraméter használatával adja meg a megfelelő Azure Active Directory-bérlő üzemeltetett melyik régióban. Érvényes értékek a következők:
  * AzureCloud (alapértelmezett)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Frissített szinkronizálási szabály szerkesztőt használja (helyett kiépíteni) akár az alapérték hivatkozás típusú szinkronizálási szabály létrehozása közben csatlakozik.

### <a name="ad-fs-management"></a>AD FS-Szolgáltatáskezelés

#### <a name="issues-fixed"></a>Megoldott problémák

* Következő URL-címek az Azure AD hitelesítési kimaradás szembeni hibatűrést javítására által bevezetett új WS-Federation végpontok, és nem kerülnek be a helyszíni Active Directory összevonási szolgáltatások függő entitás szintű bizalmi kapcsolatainak konfigurációja:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://CCS.login.microsoftonline.com/login.srf
  * https://CCS-sdf.login.microsoftonline.com/login.srf
  
* Megtörtént egy probléma javítása, ami miatt az AD FS megfelelő jogcím értékét IssuerID előállításához. A probléma akkor fordul elő, ha több ellenőrzött tartomány szerepelnek az Azure AD-bérlő és a tartományutótag a userPrincipalName attribútum a IssuerID jogcím létrehozásához használt legalább 3-szintek mély (például johndoe@us.contoso.com). A probléma megoldásához frissítése a jogcímszabályok által használt reguláris kifejezéssel.

#### <a name="new-features-and-improvements"></a>Új szolgáltatásait és fejlesztéseit
* Korábban az AD FS-tanúsítványok kezelését az Azure AD Connect szolgáltatását csak használható az AD FS-farmok felügyelete az Azure AD Connect szolgáltatáson keresztül történik. Most a szolgáltatás használható az AD FS-farmok, amelyeket nem kezel az Azure AD Connect használatával.

## <a name="115240"></a>1.1.524.0
Kiadás dátuma: Előfordulhat, hogy 2017

> [!IMPORTANT]
> A build bevezetett séma- és szinkronizálási szabály módosítások vannak. Az Azure AD Connect szinkronizálási szolgáltatás teljes importálást és teljes szinkronizálást lépéseket vált frissítés után. A módosítások részleteit az alábbiakban található.
>
>

**Javított problémák:**

Az Azure AD Connect szinkronizálása

* Megtörtént egy probléma javítása, amelyek hatására az automatikus frissítés megtörténik az Azure AD Connect-kiszolgálón, akkor is, ha az ügyfél letiltotta a funkciót, a Set-ADSyncAutoUpgrade parancsmag használatával. Ez a javítás az automatikus frissítési folyamat a kiszolgálón is ellenőrzi a frissítéshez rendszeres időközönként, de a letöltött telepítőt eleget tegyen az automatikus frissítési konfiguráció.
* A DirSync helybeni frissítés során az Azure AD Connect létrehoz egy Azure AD szolgáltatás-fiókot az Azure ad szolgáltatással szinkronizál az Azure AD-összekötő által használandó. A fiók létrehozását követően az Azure AD Connect fiókkal az Azure AD hitelesíti. Egyes esetekben hitelesítés nem sikerül átmeneti hibái miatt emiatt pedig a DirSync helybeni frissítést a következő hiba sikertelen *"hiba történt a feladat végrehajtása konfigurálása az AAD Sync: AADSTS50034: Jelentkezzen be az alkalmazást, hogy a fiókot hozzá kell adni a xxx.onmicrosoft.com könyvtárba."* A DirSync frissítésének rugalmassági javítása érdekében az Azure AD Connect most a hitelesítési lépés újbóli.
* A 443-as build okozó hibát DirSync helybeni frissítése sikeres volt, de még nem jöttek létre futtatási profilokat a címtár-szinkronizálás szükséges. Ez az Azure AD Connect build logika javító tartalmazza. Ha az ügyfél frissíti a buildre, az Azure AD Connect észleli a hiányzó futtatási profilok és létrehozása.
* Az Eseménynapló Azonosítójú 6900 és hiba elindítsa a jelszó-szinkronizálás folyamat okozó hibát rögzített *"azonos kulccsal rendelkező elem már szerepel"*. Ez a probléma akkor fordul elő, OU konfigurációs szűrés AD konfigurációs partíción felvenni frissítésekor. A probléma megoldásához, jelszó-szinkronizálás folyamat most szinkronizálja a jelszó-módosítások csak tartománypartíciók AD. Például a konfigurációs partíció nem tartománypartícióról kimarad.
* Expressz telepítés során az Azure AD Connect létrehoz egy helyszíni Active Directory tartományi Szolgáltatásokban való kommunikációhoz az AD-összekötő által használt fiók a helyszíni AD. Korábban a fiók létrehozása a felhasználó-fiók-ellenőrző attribútum beállítása PASSWD_NOTREQD jelzővel, és a véletlenszerű jelszót a fiókhoz van beállítva. Most az Azure AD Connect explicit módon eltávolítja a PASSWD_NOTREQD jelzőt a jelszót a fiók beállítása után.
* Rögzített DirSync frissítésének sikertelenségét okozó hiba hibát *"holtpont alakult ki az sql server melyik próbál szerezni egy Alkalmazászárolás"* amikor a mailNickname attribútum található meg a helyszíni AD séma, de nem korlátozza a az AD-felhasználó objektum osztályhoz.
* Rögzített eszköz visszaírási funkció automatikusan le kell tiltani, ha a rendszergazda frissítése folyamatban van az Azure AD Connect szinkronizálási konfigurációját az Azure AD Connect varázsló segítségével okozó hibát. Ennek oka a varázsló az meglévő visszaírás konfigurációja a helyszíni AD és az ellenőrzés sikertelen előfeltétel-ellenőrzés elvégzése. A probléma, hogy az ellenőrzés kihagyására, amennyiben a eszközvisszaíró korábban már engedélyezve van.
* Szervezeti egységek szűrése konfigurálásához használhatja az Azure AD Connect varázsló vagy a Synchronization Service Managert. Korábban Ha az Azure AD Connect varázsló használatával konfigurálja a szervezeti egységek szűrése, létrehozott új szervezeti egységek ezt követően érhetők el a címtár-szinkronizálás. Ha nem szeretné, hogy új szervezeti egységek része, konfigurálnia kell szervezeti egységek szűrése a Synchronization Service Manager használatával. Ezután ugyanez a viselkedés az Azure AD Connect varázsló segítségével érhet el.
* Az Azure AD Connect kell létrehozni a telepítést végző felügyeleti sémája ahelyett, hogy a dbo sémában tárolt eljárások okozó hibát rögzített.
* Az Azure AD-be lehet hagyni a az AAD Connect kiszolgáló eseménynaplók által visszaadott TrackingId attribútum okozó hibát rögzített. A probléma akkor fordul elő, ha az Azure AD Connect Azure ad-átirányítás üzenetet kap, és az Azure AD Connect nem tud kapcsolódni a megadott végpont számára. A támogatási szakértők a TrackingId okozták az ügyféloldali szolgáltatásnaplók hibaelhárítása során használt.
* Ha az Azure AD Connect Azure ad-LargeObject hibaüzenetet kap, az Azure AD Connect állít elő, a EventID 6941 és üzenet esemény *"a telepített objektum mérete túl nagy. Trim a számát ezen az objektumon."* Egy időben, az Azure AD Connect is létrehoz egy eseményazonosító 6900 és az üzenet félrevezető esemény *"Microsoft.Online.Coexistence.ProvisionRetryException: nem lehet kommunikálni a Windows Azure Active Directory szolgáltatás."* A zavarok minimalizálása érdekében, az Azure AD Connect nem LargeObject hiba fogadásakor. utóbbi esetben állít elő.
* A Synchronization Service Managert válaszol, az általános LDAP-összekötő konfigurációja frissítésekor okozó hibát rögzített.

**Új funkciók fejlesztései:**

Az Azure AD Connect szinkronizálása
* Szinkronizálási szabály változásokat – a következő szinkronizálási szabály változások történtek:
  * Frissített alapértelmezett szinkronizálási szabályt beállítani, hogy nem exportál attribútumok **userCertificate** és **userSMIMECertificate** Ha az attribútumok több mint 15 értékűek.
  * AD-attribútumok **employeeID** és **msExchBypassModerationLink** most már szerepel a szinkronizálási szabály alapértelmezés szerint.
  * AD attribútum **fénykép** szinkronizálási szabály alapértelmezés szerint el lett távolítva.
  * Hozzáadott **preferredDataLocation** metaverzumséma és AAD-összekötő séma. Az ügyfelek, akik vagy attribútumok frissíti az Azure ad-ben egyéni szinkronizálási szabályok erre is létrehozható. 
  * Hozzáadott **userType** metaverzumséma és AAD-összekötő séma. Az ügyfelek, akik vagy attribútumok frissíti az Azure ad-ben egyéni szinkronizálási szabályok erre is létrehozható.

* Az Azure AD Connect mostantól automatikusan lehetővé teszi, hogy a ConsistencyGuid attribútum használata a Forráshorgony attribútumaként a helyszíni Active Directory-objektumok. További, az Azure AD Connect tölti fel az objectGuid attribútum értékét a ConsistencyGuid attribútumot, ha üres. Ez a funkció esetében csak az új telepítési alkalmazható. További információkért erről a szolgáltatásról, tekintse át a cikk részt [az Azure AD Connect: tervezési alapelvek - msDS-ConsistencyGuid használata sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).
* Új hibaelhárítási parancsmag Invoke-ADSyncDiagnostics bővült Jelszókivonat-szinkronizálást diagnosztizálásához kapcsolatos hiba lépett fel. A parancsmag használatával kapcsolatos információkért tekintse meg a cikk [és az Azure AD Connect-szinkronizálás jelszó-szinkronizálás hibaelhárítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-synchronization).
* Az Azure AD Connect mostantól támogatja a szinkronizálási Mail-Enabled nyilvános mappa a következő helyről objektumokat a helyszíni AD-ből az Azure AD. A funkciót, az Azure AD Connect varázsló a nem kötelező funkcióinak használatával engedélyezheti. További információkért erről a szolgáltatásról, tekintse meg a cikk [Office 365 Directory-alapú biztonsági blokkolja a támogatása a helyszíni Mail nyilvános mappák](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Az Azure AD Connect van szükség az AD DS fiók szinkronizálása a helyszíni AD. Korábban Ha az Expressz mód segítségével az Azure AD Connect telepíti, akkor biztosíthatja a hitelesítő adatokat az Azure AD Connect és egy vállalati rendszergazdai fiók hozna létre az AD DS-fiókjához szükséges. Azonban az egyéni telepítés és erdők hozzáadása egy meglévő központi telepítési kellett Ehelyett meg az AD DS-fiókjához. Most is lehetősége nyílik egy vállalati rendszergazdai fiók hitelesítő adatait adja egyéni telepítés során, és lehetővé teszik a szükséges Active Directory tartományi szolgáltatások-fiók létrehozása az Azure AD Connect.
* Az Azure AD Connect mostantól támogatja az SQL AOA. Az Azure AD Connect telepítése előtt engedélyeznie kell az SQL AOA. A telepítés során az Azure AD Connect észleli, hogy a megadott SQL-példány engedélyezve van az SQL AOA vagy nem. Ha SQL AOA engedélyezve van, az Azure AD Connect további adatok Ha SQL AOA replikáció szinkron vagy aszinkron replikáció használatára van konfigurálva. A rendelkezésre állási csoport figyelőjének beállításakor javasoljuk, hogy a RegisterAllProvidersIP tulajdonságot 0 értékre állítja. Ennek az az oka az Azure AD Connect jelenleg használ SQL Native Client kapcsolódni az SQL és az SQL natív ügyfél nem támogatja a MultiSubNetFailover tulajdonság használatával.
* Ha az Azure AD Connect-kiszolgáló LocalDB használata az adatbázist, és elérte 10 GB-os méret, a szinkronizálási szolgáltatás nem indul el. Korábban kell számára a szinkronizálási szolgáltatás elindításához elég DB terület felszabadítását LocalDB ShrinkDatabase műveletet végrehajtani. Amely után a Synchronization Service Manager segítségével az általuk további DB terület visszanyeréséhez futtatási előzmények törlése. Most segítségével Start-ADSyncPurgeRunHistory parancsmag futtatása kiürítése előzményadatok LocalDB a DB terület visszanyeréséhez. További, ez a parancsmag támogatja-e egy offline üzemmód (megadásával az - offline paramétert) amely is használható, ha a szinkronizálási szolgáltatás nem fut. Megjegyzés: A kapcsolat nélküli módban csak használható, ha a szinkronizálási szolgáltatás nem fut, valamint a használt adatbázist LocalDB.
* Csökkentheti a szükséges, tárhely az Azure AD Connect most tömöríti szinkronizálási hiba legutolsó részletes adatai előtt LocalDB/SQL-adatbázisban tárolja őket. Erre a verzióra frissíti, az Azure AD Connect egy korábbi verziójából származó, az Azure AD Connect meglévő szinkronizálási hiba legutolsó részletes adatai egy egyszeri tömörítési végez.
* Korábban Miután frissített OU-szűrés konfigurációs, manuálisan futtatnia kell a teljes importálás, hogy meglévő objektumok megfelelően szereplő/kizárja a címtár-szinkronizálás. Most, az Azure AD Connect automatikusan elindítja a teljes importálás során a következő szinkronizálási ciklust. További, a teljes importálás csak az AD-összekötők, a frissítés hatással lehet vonatkozik. Megjegyzés: ennek a fejlesztésnek köszönhetően tulajdonság csak az Azure AD Connect varázsló használatával végzett szűrés OU vonatkozik. Nincs frissítés a Synchronization Service Manager használatával létrehozott szűrés OU alkalmazandó.
* Korábban biztonságicsoport-alapú szűrés támogatja a felhasználók, csoportok, és forduljon objektumok csak. Most csoport-alapú szűrés is támogatja számítógép-objektumokat.
* Törölheti a kapcsolódási térbe adatok korábban, az Azure AD Connect sync scheduler letiltásáig. A Synchronization Service Managert, blokkolja kapcsolódási térbe adatok törlését, ha azt észleli, hogy engedélyezve van-e az ütemező. További a rendszer figyelmeztetést tájékoztatja a felhasználókat kapcsolatos esetleges adatvesztés, ha az összekötő lemezterület az adatok törlése adott vissza.
* Korábban le kell tiltania PowerShell írjanak elő az Azure AD Connect varázsló megfelelő futtatásához. Ez a probléma részlegesen megoldódott. Engedélyezheti a PowerShell írjanak elő, ha szinkronizálási konfigurációjának kezelése az Azure AD Connect varázsló használ. Le kell tiltania PowerShell írjanak elő, ha az AD FS konfigurációjának kezelése az Azure AD Connect varázsló használ.



## <a name="114860"></a>1.1.486.0
Kiadás dátuma:-2017. április

**Javított problémák:**
* A probléma rögzített, ahol az Azure AD Connect nem telepíthető sikeresen a Windows Server honosított verzióját.

## <a name="114840"></a>1.1.484.0
Kiadás dátuma:-2017. április

**Ismert problémák:**

* Az Azure AD Connect ezen verziója nem lesz telepítve sikeresen, ha az alábbi feltételek mind teljesülnek:
   1. Vagy a DirSync helybeni frissítés vagy friss telepítése az Azure AD Connect végzik.
   2. Ha a beépített Rendszergazdák csoportjának a kiszolgáló neve nem "Rendszergazdák" Windows-kiszolgáló honosított verzióját használja.
   3. Az alapértelmezett SQL Server 2012 Express LocalDB helyett a saját teljes SQL biztosít az Azure AD Connect telepített használ.

**Javított problémák:**

Az Azure AD Connect szinkronizálása
* Ha a szinkronizálásütemező kihagyja-e a teljes szinkronizálás lépés, ha egy vagy több összekötők hiányzik a szinkronizálás a lépés a futtatási profil rögzített kapcsolatos problémát. Például manuálisan hozzáadott egy összekötőt, a Synchronization Service Managert használja a különbözeti importálás az futtatási profiljának létrehozása nélkül. Ez a javítás biztosítja, hogy a szinkronizálásütemező továbbra is fut a különbözeti importálás a többi összekötőt.
* Rögzített problémát, ha a szinkronizálási szolgáltatás azonnal leállítja a feldolgozást a futtatási profil van ütközik az egyik futtatási lépés kapcsolatos problémát. Ez a javítás biztosítja, hogy a szinkronizálási szolgáltatás kihagyja lépés futtató, és folytatja a többi. Például hogy a különbözeti importálás, futtassa a profil az AD-összekötő több futtatási lépés (egy mindegyik helyszíni AD-tartomány). Akkor is, ha az egyik legyen a hálózati kapcsolat hibái a szinkronizálási szolgáltatás különbözeti importálás fog futni a más AD tartományokkal.
* Rögzített eltekint automatikus frissítése az Azure AD-összekötő frissítés okozó hibát.
* Rögzített helytelenül meghatározásához. a kiszolgáló egy tartományvezérlő telepítése során az Azure AD Connect okozó hibát, mely a kapcsolja hatására DirSync frissítés sikertelen lesz.
* Rögzített DirSync helybeni frissítést, nem minden futtatási profil létrehozása az Azure AD-összekötő okozó hibát.
* Rögzített problémát, ahol a Synchronization Service Manager felhasználói felülete nem válaszol általános LDAP-összekötő konfigurálása közben.

AD FS-Szolgáltatáskezelés
* Rögzített problémát, ahol az Azure AD Connect varázsló sikertelen lesz, ha az AD FS elsődleges csomópont át lett helyezve egy másik kiszolgálóra.

Asztali egyszeri bejelentkezés
* Rögzített probléma az Azure AD Connect varázsló, ahol a bejelentkezési képernyőn nem engedélyezi az asztal SSO funkció engedélyezése, ha úgy döntött, hogy a jelszó-szinkronizálás, a bejelentkezési lehetőséget új telepítése során.

**Új funkciók fejlesztései:**

Az Azure AD Connect szinkronizálása
* Azure AD Connect szinkronizálási szolgáltatás mostantól támogatja a virtuális szolgáltatásfiók, a felügyelt szolgáltatásfiókok és csoportosan felügyelt szolgáltatásfiók a szolgáltatás fiók. Ez vonatkozik az Azure AD Connect csak új telepítése. Ha az Azure AD Connect telepítése:
    * Alapértelmezés szerint az Azure AD Connect varázsló létrehoz egy virtuális szolgáltatásfiókot, és használja a szolgáltatás fiók.
    * Ha tartományvezérlőre telepíti, az Azure AD Connect korábbi viselkedést, ahol azt tartományi felhasználói fiókot hoz létre, és helyette használja annak szolgáltatásfiókként csökken.
    * Az alapértelmezett viselkedést felülírhatja a következők egyikét:
      * Egy csoport felügyelt szolgáltatásfiók
      * Felügyelt szolgáltatásfiók
      * Egy tartományi felhasználói fiók
      * Helyi felhasználói fiók
* Korábban Ha az Azure AD Connect tartalmazó új buildverzióját frissíteni összekötők frissítése, vagy szinkronizálási szabály változások, az Azure AD Connect egy teljes szinkronizálási ciklust indít. Most az Azure AD Connect szelektív módon elindítja a teljes importálás csak az összekötők, frissítés, és teljes szinkronizálást lépést csak az összekötők szinkronizálási szabály módosult.
* Korábban az Exportálás törlési küszöbét csak érvényes keresztül a szinkronizálásütemező kiváltó exportálja. Most a szolgáltatás tartalmazza az ügyfél, a szinkronizálás a Service Manager használatának manuálisan indított exportálja ki van bővítve.
* Az Azure AD-bérlő nincs a szolgáltatás konfigurációja, amely azt jelzi, hogy a jelszó-szinkronizálás szolgáltatás engedélyezve van a bérlő vagy nem. Korábban is egyszerűen a szolgáltatás konfigurációja helytelen konfigurálását az Azure AD Connect, ha az aktív és egy átmeneti kiszolgálón. Most, az Azure AD Connect megpróbálja tartani a szolgáltatás konfigurációját az aktív konzisztens csak az Azure AD Connect-kiszolgáló.
* Az Azure AD Connect varázsló most észleli, és a rendszer figyelmeztetést ad vissza, ha a helyszíni AD nem rendelkezik AD Lomtár engedélyezve van.
* Ha a kötegben objektumok összesített mérete meghaladja a meghatározott küszöbérték korábban exportálja az Azure AD időtúllépést és a sikertelen. Most a szinkronizálási szolgáltatás újra megkísérli a különálló, kisebb kötegekben objektumok újraküldeni, ha a probléma akkor fordul elő.
* A szinkronizálási szolgáltatás Key Management alkalmazás el lett távolítva a Windows Start menüből. Titkosítási kulcs felügyeleti továbbra is támogatja a parancssori felület használatával miiskmu.exe keresztül. Titkosítási kulcs kezelésével kapcsolatos információkért tekintse meg a cikk [az Azure AD Connect szinkronizálási szolgáltatás titkosítási kulcs kivonásának](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Korábban, ha módosítja a Azure AD Connect szinkronizálási szolgáltatás fiók jelszavát, a szinkronizálási szolgáltatás nem lesz képes start megfelelően félbehagyná a titkosítási kulcs, és újra lesz inicializálva a Azure AD Connect szinkronizálási szolgáltatás fiók jelszavát. Most ez már nincs szükség.

Asztali egyszeri bejelentkezés

* Az Azure AD Connect varázsló már nem szükséges portot kell megnyitni a hálózaton, áteresztő hitelesítés és asztali SSO konfigurálásakor 9090. Csak a 443-as porton szükség. 

## <a name="114430"></a>1.1.443.0
Kiadás dátuma: Március 2017

**Javított problémák:**

Az Azure AD Connect szinkronizálása
* Rögzített hibát, amely azt eredményezi, hogy meghiúsuljon, ha az Azure AD-összekötő a megjelenített név nem tartalmaz a kezdeti onmicrosoft.com tartománnyal, az Azure AD-bérlő rendelt Azure AD Connect varázsló.
* Rögzített hibát, amely azt eredményezi, az Azure AD Connect varázsló sikertelen közben, így az SQL database-kapcsolat, ha a szolgáltatásfiók jelszavát például az aposztróf, kettőspont és lemezterület különleges karaktereket tartalmaz.
* A probléma, amely a hibát okoz, "a dimage rendelkezik, amely eltér attól a kép horgonya" rögzített megtörténik az Azure AD Connect-kiszolgáló átmeneti módban, miután ideiglenesen kizárt egy helyszíni AD szinkronizálása objektumra, és majd érhető el, újra a szinkronizálással.
* A probléma, amely a hibát okoz, "az objektum megkülönböztető név szerint egy látszólagos" rögzített megtörténik az Azure AD Connect-kiszolgáló átmeneti módban, miután ideiglenesen kizárt egy helyszíni AD szinkronizálása objektumra, és majd érhető el, újra a szinkronizálással.

AD FS-Szolgáltatáskezelés
* Megtörtént egy probléma javítása ahol az Azure AD Connect varázsló nélkül AD FS konfigurációjának frissítése, a jobb oldali jogcímeket a függő entitás megbízhatóságán másodlagos bejelentkezési azonosító konfigurálása után.
* Rögzített problémát, ahol az Azure AD Connect varázsló nem tudja megfelelően kezeli az AD FS-kiszolgáló, amelynek szolgáltatásfiókok vannak konfigurálva a userPrincipalName formátumban sAMAccountName formátum helyett.

Átmenő hitelesítés
* Rögzített hibát, amely azt eredményezi, hogy meghiúsuljon, ha továbbítani keresztül hitelesítés van beállítva, de az összekötő regisztrálása meghiúsul az Azure AD Connect varázslót.
* Megtörtént egy probléma javítása mely okok Ha asztali SSO szolgáltatás engedélyezve van a kijelölt bejelentkezési módszert ellenőrzésének kihagyását az Azure AD Connect varázslót.

Jelszó visszaállítása
* Megtörtént egy probléma javítása miatt előfordulhat, hogy az Azure AAD-csatlakozás kiszolgáló próbálja meg ismét csatlakozni, ha a kapcsolat egy tűzfal vagy a proxy következtében leállt.

**Új funkciók fejlesztései:**

Az Azure AD Connect szinkronizálása
* Get-ADSyncScheduler parancsmag most SyncCycleInProgress nevű új logikai tulajdonság adja vissza. Ha a visszaadott érték igaz, az azt jelenti, hogy van-e folyamatban lévő ütemezett szinkronizálási ciklust.
* Azure AD Connect telepítés és a telepítési naplók tárolásához célmappa áthelyezett %localappdata%\AADConnect a %programdata%\AADConnect a kisegítő lehetőségek a naplófájlokba.

AD FS-Szolgáltatáskezelés
* Támogatása az AD FS Farm SSL-tanúsítvány frissítése.
* Támogatása az AD FS 2016 felügyeletéhez.
* AD FS telepítése során mostantól megadhatja a meglévő csoportosan felügyelt szolgáltatásfiók (csoportosan felügyelt szolgáltatásfiók).
* Most már beállíthat SHA-256-ot az aláírás-kivonatoló algoritmussal az Azure AD közvetítőpartneri megbízhatósághoz.

Jelszó visszaállítása
* A funkció a termék engedélyezéséhez környezetekben, ahol szigorúbb tűzfalszabályok bevezetett fejlesztései.
* Azure Service Bus továbbfejlesztett kapcsolat megbízhatóságát.

## <a name="113800"></a>1.1.380.0
Kiadás dátuma: 2016. December

**Rögzített problémát:**

* Ha az Active Directory összevonási szolgáltatások (AD FS) issuerid jogcímszabály hiányzik a építés rögzített a problémát.

>[!NOTE]
>A build ügyfélnek az Azure AD Connect automatikus frissítési szolgáltatás nem érhető el.

## <a name="113710"></a>1.1.371.0
Kiadás dátuma: 2016. December

**Ismert hiba:**

* Az AD FS issuerid jogcímszabály a build hiányzik. A issuerid jogcímszabály szükség, ha összevonja a több tartományt az Azure Active Directoryval (Azure AD). Ha az Azure AD Connect segítségével kezelheti a helyszíni AD FS üzembe helyezése, a build frissítése a meglévő issuerid jogcímszabály eltávolítja az AD FS konfigurációt. A probléma megkerüléséhez a issuerid jogcímszabály hozzáadásával a telepítés/frissítés után. A részletek a issuerid hozzáadására vonatkozó jogcímszabály, tekintse meg a cikk a [többtartományos támogatás az Azure AD összevonási szolgáltatásához](active-directory-aadconnect-multiple-domains.md).

**Rögzített problémát:**

* Ha a kimenő kapcsolat nincs megnyitva Port 9090, az Azure AD Connect telepítése vagy frissítése sikertelen.

>[!NOTE]
>A build ügyfélnek az Azure AD Connect automatikus frissítési szolgáltatás nem érhető el.

## <a name="113700"></a>1.1.370.0
Kiadás dátuma: 2016. December

**Ismert problémák:**

* Az AD FS issuerid jogcímszabály a build hiányzik. A issuerid jogcímszabály szükség, ha összevonja a több tartomány és az Azure AD. Ha az Azure AD Connect segítségével kezelheti a helyszíni AD FS üzembe helyezése, a build frissítése a meglévő issuerid jogcímszabály eltávolítja az AD FS konfigurációt. A probléma megkerüléséhez a issuerid jogcímszabály hozzáadásával a telepítés/frissítés után. Részletek issuerid hozzáadására vonatkozó jogcímszabály, részletek Ez a cikk a [többtartományos támogatás az Azure AD összevonási szolgáltatásához](active-directory-aadconnect-multiple-domains.md).
* Port 9090 kimenő telep kell megnyitni.

**Új szolgáltatások:**

* Áteresztő hitelesítés (előzetes verzió).

>[!NOTE]
>A build ügyfélnek az Azure AD Connect automatikus frissítési szolgáltatás nem érhető el.

## <a name="113430"></a>1.1.343.0
Kiadás dátuma: 2016. November

**Ismert hiba:**

* Az AD FS issuerid jogcímszabály a build hiányzik. A issuerid jogcímszabály szükség, ha összevonja a több tartomány és az Azure AD. Ha az Azure AD Connect segítségével kezelheti a helyszíni AD FS üzembe helyezése, a build frissítése a meglévő issuerid jogcímszabály eltávolítja az AD FS konfigurációt. A probléma megkerüléséhez a issuerid jogcímszabály hozzáadásával a telepítés/frissítés után. Részletek issuerid hozzáadására vonatkozó jogcímszabály, részletek Ez a cikk a [többtartományos támogatás az Azure AD összevonási szolgáltatásához](active-directory-aadconnect-multiple-domains.md).

**Javított problémák:**

* Egyes esetekben az Azure AD Connect telepítése sikertelen lesz, mivel nem hozzon létre egy helyi szolgáltatásfiókot, amelynek jelszó megfelel-e a jelszóházirend a szervezet által meghatározott bonyolultsági mértékét.
* Megtörtént egy probléma javítása ahol illesztési szabályok vannak nem reevaluated válásakor a kapcsolódási térbe objektumára egyidejűleg out érhető egy szabály csatlakoztatást, és a hatókör válik egy másik. Ez akkor fordulhat elő, ha két vagy több illesztési szabály, amelynek illesztési feltételek közül mind kölcsönösen kizárják egymást.
* Megtörtént egy probléma javítása vonatkozó bejövő szinkronizálási szabályok (az Azure AD), amelyek nem tartalmaznak illesztési szabályok, ahol nem dolgoznak, ha alacsonyabb sorrend értékeknél illesztési szabályokat tartalmazó telepítve.

**Fejlesztései:**

* A Windows Server 2016 Standard vagy magasabb az Azure AD Connect telepítésének támogatása.
* Támogatja a távoli adatbázis SQL Server 2016 az Azure AD Connect használatával.

## <a name="112810"></a>1.1.281.0
Kiadás dátuma: 2016. augusztus

**Javított problémák:**

* Módosítások szinkronizálása időköz nem kerül sor, amíg, a következő szinkronizálási ciklusban befejezése után.
* Az Azure AD Connect varázsló nem fogadja el az Azure AD-fiókot, akiknek a felhasználóneve kezdődik aláhúzásjellel (\_).
* Azure AD Connect varázsló nem tudja hitelesíteni az Azure AD-fiókot, ha a fiók jelszava túl sok speciális karaktereket tartalmaz. Hibaüzenet "nem lehet érvényesíteni a hitelesítő adatokat. Váratlan hiba történt." adja vissza.
* Átmeneti kiszolgáló eltávolítása az Azure AD-bérlő jelszó-szinkronizálás letiltása, és leállítja a jelszó-szinkronizálás sikertelen, és az active server.
* Ritka esetekben a jelszó-szinkronizálás sikertelen lesz, nem a felhasználó tárolt Jelszókivonat esetén.
* Ha az Azure AD Connect-kiszolgáló az átmeneti környezetű üzemmód engedélyezve van, a jelszóvisszaírás nincs letiltva ideiglenesen.
* Az Azure AD Connect varázsló nem szerepelnek a tényleges jelszó-szinkronizálás és a jelszó visszaírási konfigurációs átmeneti módban kiszolgáló esetén. Azt mindig azt is le van tiltva.
* Jelszó-szinkronizálás és jelszóvisszaíró konfigurációs módosítások nem maradnak meg az Azure AD Connect varázsló a Ha-kiszolgáló átmeneti módban.

**Fejlesztései:**

* A Start-ADSyncSyncCycle parancsmag jelzi, hogy sikeresen indítható el egy új szinkronizálási ciklusban, vagy nem frissül.
* Hozzáadja a Stop-ADSyncSyncCycle parancsmagot le kell állítanunk a szinkronizálási ciklusban és műveletet, amelyek jelenleg folyamatban van.
* Frissítve a Stop-ADSyncScheduler parancsmagot le kell állítanunk a szinkronizálási ciklusban és műveletet, amelyek jelenleg folyamatban van.
* Konfigurálásakor [címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md) az Azure AD Connect varázsló az Azure AD attribútum "Teletex karakterlánc" típusú most is választható.

## <a name="111890"></a>1.1.189.0
Kiadás dátuma: 2016. június

**Javított problémák és fejlesztései:**

* Az Azure AD Connect mostantól a FIPS előírásainak megfelelő kiszolgálóra telepíthető.
  * A jelszó-szinkronizálás, lásd: [jelszó-szinkronizálással és FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips).
* Ha NetBIOS-név nem oldható fel az Active Directory-összekötő az FQDN rögzített kapcsolatos problémát.

## <a name="111800"></a>1.1.180.0
Kiadás dátuma: 2016. május

**Új szolgáltatások:**

* Figyelmezteti, és ellenőrizze a tartomány, ha az Azure AD Connect futtatása előtt egyáltalán nem nyújt segítséget.
* Támogatása az [Microsoft Cloud Németország](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* A legújabb támogatása [a Microsoft Azure Government felhő](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) infrastruktúra és az új URL-követelményeknek.

**Javított problémák és fejlesztései:**

* Hozzáadva a szinkronizálási szabály szerkesztőt, és megkönnyíti a szinkronizálási szabályok található szűrés.
* Javítja a teljesítményt a kapcsolódási térbe törlésekor.
* Megtörtént egy probléma javítása, amikor ugyanazt az objektumot le volt törölt és hozzáadott ugyanazon (hívott törlése vagy hozzáadása) futtatásához.
* Letiltott szinkronizálási szabály már nem újból engedélyezi a található objektumok és attribútumok használatát a frissítési vagy könyvtár séma frissítése.

## <a name="111300"></a>1.1.130.0
Kiadás dátuma: 2016. április

**Új szolgáltatások:**

* Többértékű attribútumok támogatása [címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md).
* A további konfigurációs eltéréseket támogatása [automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md) akkor, ha jogosultak a frissítésre.
* Egyes parancsmagjai hozzáadott [egyéni Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Kiadás dátuma: 2016. március

**Javított problémák:**

* Arról, hogy a gyorstelepítés nem használható a Windows Server 2008 (R2 előtti), mert a jelszó-szinkronizálás nem támogatott az operációs rendszer.
* Frissítés a Dirsync szolgáltatásról az egyéni szűrő beállításokkal nem működött a várt módon.
* Egy újabb verzióra történő frissítése során, de a konfigurációban nem történtek változások, a teljes importálás vagy szinkronizálás nem ütemezhető.

## <a name="111100"></a>1.1.110.0
Kiadás dátuma: 2016. február

**Javított problémák:**

* Frissítés a korábbi kiadásokban nem működik, ha a telepítés nem az alapértelmezett C:\Program Files mappában található.
* Ha telepítette, és törölje a jelet **indítsa el a szinkronizálási folyamat** a telepítési varázsló végén még egyszer a telepítővarázsló futtatása nem lehetővé teszi a Feladatütemező.
* A Feladatütemező nem működik, ha a hu-hu dátum/idő formátuma nem használt kiszolgálók várt. Azt is letiltja `Get-ADSyncScheduler` helyes időben vissza.
* Ha telepítette az Azure AD Connect egy korábbi verziója az AD FS a bejelentkezési lehetőséget és a frissítés, a telepítési varázsló újra nem futtatható.

## <a name="111050"></a>1.1.105.0
Kiadás dátuma: 2016. február

**Új szolgáltatások:**

* [Automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md) szolgáltatás expressz beállításokat ügyfelek esetén.
* A globális rendszergazda Azure többtényezős hitelesítés és a Privileged Identity Management használatával a telepítővarázslóban támogatása.
  * Szeretné engedélyezni a proxy-forgalom https://secure.aadcdn.microsoftonline-p.com is engedélyezi a többtényezős hitelesítés használata esetén.
  * Https://secure.aadcdn.microsoftonline-p.com hozzáadása a megbízható helyek listájához, a többtényezős hitelesítés megfelelő működéséhez szükséges.
* Engedélyezi a kezdeti telepítés után a felhasználó bejelentkezési módszer módosítását.
* Engedélyezése [tartomány és szervezeti egységek szűrése](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) a telepítővarázslóban. Ez is lehetővé teszi, hogy hol érhetők el nem minden tartományok erdők csatlakozik.
* [A Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md) a szinkronizálási motor részét.

**A szolgáltatások Preview a tartományvezérlővé való előléptetés GA:**

* [Eszközvisszaíró](active-directory-aadconnect-feature-device-writeback.md).
* [Címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md).

**Új előzetes verziójú funkciók:**

* Az új alapértelmezett szinkronizálási ciklust időköz értéke 30 perc. Három óra minden korábbi kiadásokban használt. Támogatást módosítása a [Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md) viselkedését.

**Javított problémák:**

* Az ellenőrzés DNS tartományok lapon nem ismerte fel a tartományok mindig.
* Active Directory összevonási szolgáltatások konfigurálásához tartományi rendszergazdai hitelesítő adatokat kér.
* A helyszíni AD-fiókok nem ismeri fel a telepítési varázsló Ha olyan tartomány egy másik, mint a gyökértartomány DNS fában található.

## <a name="1091310"></a>1.0.9131.0
Kiadás dátuma: 2015. decemberi

**Javított problémák:**

* A jelszó-szinkronizálás nem működik, Active Directory tartományi szolgáltatások (AD DS), de működik a jelszavak módosításakor, ha úgy állítja be a jelszót.
* Ha olyan proxykiszolgálója van, az Azure AD hitelesítési előfordulhat, hogy sikerül a telepítés során, vagy ha a frissítés megszakadt a konfiguráció lapon.
* A teljes SQL Server-példányhoz az Azure AD Connect korábbi kiadásáról frissítése sikertelen lesz, ha nem egy SQL Server rendszer rendszergazdai (SA).
* A "Nem lehet hozzáférni az ADSync SQL-adatbázis" hiba történt egy távoli SQL Server az Azure AD Connect korábbi kiadásáról frissítése jeleníti meg.

## <a name="1091250"></a>1.0.9125.0
Kiadás dátuma: 2015. November

**Új szolgáltatások:**

* Az AD FS és az Azure AD-megbízhatóság olyan módon konfigurálhatja újra.
* Az Active Directory-sémát frissítheti és szinkronizálási szabályok generálni.
* Letilthatja a szinkronizálási szabály.
* "AuthoritativeNull" adhatja meg a szinkronizálási szabályoknak új literálként.

**Új előzetes verziójú funkciók:**

* [Az Azure AD Connect Health szinkronizálási szolgáltatás](../connect-health/active-directory-aadconnect-health-sync.md).
* Támogatja az [Azure AD tartományi szolgáltatások](../active-directory-passwords-update-your-own-password.md) jelszó-szinkronizálás.

**Új támogatott forgatókönyv szerint:**

* Több helyszíni Exchange-szervezet támogatja. További információkért lásd: [hibrid telepítések esetén több Active Directory-erdő](https://technet.microsoft.com/library/jj873754.aspx).

**Javított problémák:**

* Jelszó-szinkronizálás problémák:
  * Egy objektum helyezi át a hatókör out-az-hatókör nem lesz a jelszó szinkronizálva. Ez magában foglalja a szervezeti egység- és attribútumszűrés.
  * A teljes jelszó-szinkronizálás nincs szükség a szinkronban felvenni új szervezeti egység kiválasztása.
  * Ha egy letiltott felhasználó engedélyezve van a jelszó nem szinkronizálja.
  * A jelszót újra várólista végtelen és 5000 objektumok kell vonni a korábbi korlátot el lett távolítva.
* Nem sikerült kapcsolódni az Active Directory erdő működési szintje Windows Server 2016.
* Nem lehet másik csoport, amely a kezdeti telepítés után nézetek szolgál.
* Már nem hoz létre egy új felhasználói profilt minden felhasználó, a jelszó módosítása során a jelszóvisszaírás engedélyezése az Azure AD Connect-kiszolgálón.
* Nem képes hosszú egész értékek szinkronban szabályok hatókörök.
* A jelölőnégyzet be "eszközök visszaírásához" tiltva maradjon, ha a tartományvezérlő nem érhető el.

## <a name="1086670"></a>1.0.8667.0
Kiadás dátuma: 2015. augusztus

**Új szolgáltatások:**

* Az Azure AD Connect telepítővarázsló most honosított minden Windows Server-nyelvekre.
* Támogatja a fiók zárolásának feloldásához, az Azure ad használata esetén.

**Javított problémák:**

* Az Azure AD Connect telepítővarázsló összeomlik, ha egy másik felhasználó továbbra is fennáll, telepítési, hanem a személy, aki a telepítést az indításakor.
* Ha az Azure AD Connect korábbi eltávolítási eltávolítása nem sikerült az Azure AD Connect szinkronizálási szolgáltatás szabályszerűen, nincs lehetőség a újra kell telepítenie.
* Gyors telepítés használatával, ha a felhasználó nincs az erdő gyökértartományában, vagy ha az Active Directory egy nem angol nyelvű verzióját használja az Azure AD Connect nem tud telepíteni.
* Ha az Active Directory felhasználói fiók Tartományneve nem oldható fel, egy félrevezető "Nem sikerült véglegesíteni a séma" hibaüzenet jelenik meg.
* Ha a varázslót az Active Directory-összekötőt a használt fiók megváltozott, a varázsló utólagosan sikertelen lesz.
* Az Azure AD Connect néha nem tud telepíteni egy tartományvezérlőn.
* Nem engedélyezi, és tiltsa le a "Az átmeneti környezetű üzemmód", ha kiterjesztési attribútumot hozzá lett adva.
* A jelszóvisszaírás bizonyos konfigurációk az Active Directory-összekötő rossz jelszó miatt nem sikerül.
* A DirSync nem frissíthető, ha a megkülönböztető név (DN) használatos attribútumszűrés.
* Túlzott CPU-használat jelszóátállítás használata esetén.

**Az eltávolított előzetes verziójú funkciók:**

* Az előzetes verziójú funkciók [felhasználó-visszaírás](active-directory-aadconnect-feature-preview.md#user-writeback) ideiglenesen eltávolították a preview ügyfelek visszajelzései alapján. Lesz hozzáadva később után a megadott visszajelzési vezettünk.

## <a name="1086410"></a>1.0.8641.0
Kiadás dátuma: 2015. június

**Az Azure AD Connect eredeti kiadását.**

Az Azure AD Connect az Azure AD-Szinkronizálóról módosított neve.

**Új szolgáltatások:**

* [Gyorsbeállítások](active-directory-aadconnect-get-started-express.md) telepítése
* Is [Active Directory összevonási szolgáltatások konfigurálása](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* Is [frissítés a Dirsync szolgáltatásról](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Bevezetett [átmeneti módban](active-directory-aadconnectsync-operations.md#staging-mode)

**Új előzetes verziójú funkciók:**

* [Felhasználók visszaírásához.](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Csoportok visszaírásához](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Eszközök visszaírásához.](active-directory-aadconnect-feature-device-writeback.md)
* [Címtárbővítmények](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Kiadás dátuma: 2015. május

**Új követelményt:**

* Az Azure AD Sync megköveteli a .NET-keretrendszer 4.5.1-es verzióját kell telepíteni.

**Javított problémák:**

* Az Azure AD jelszóvisszaírást Azure Service Bus kapcsolati hiba miatt sikertelen.

## <a name="104910413"></a>1.0.491.0413
Kiadás dátuma: 2015. áprilisi

**Javított problémák és fejlesztései:**

* Az Active Directory-összekötő nem törli megfelelően feldolgozni a Lomtár engedélyezve van, és több tartomány az erdőben vannak.
* Importálási műveletek teljesítményének javítottuk az az Azure Active Directory-összekötőt.
* Ha a csoport túllépte a tagság (alapértelmezés szerint a korlát értéke 50 000 objektummal), a csoport törölve lett az Azure Active Directoryban. Az új módon működnek a csoport nem törlődik, hiba történt, és új csoporttagsági változások nem lesznek exportálva.
* Nem lehet kiépíteni egy új objektumot, ha a ugyanazon megkülönböztető névvel rendelkező előkészített törlési már szerepel a kapcsolódási térbe.
* Bizonyos objektumok szinkronizálás során a különbözeti szinkronizálás vannak megjelölve, annak ellenére, hogy nincs készítve az objektum a változás.
* Jelszó szinkronizációt is eltávolítja a elsődleges tartományvezérlő.
* CSExportAnalyzer bizonyos objektumok állapotokkal problémák vannak.

**Új szolgáltatások:**

* "ANY" objektumtípus a MV az illesztés is csatlakozhat.

## <a name="104850222"></a>1.0.485.0222
Kiadás dátuma: 2015. február

**Fejlesztései:**

* Továbbfejlesztett importálási teljesítményét.

**Javított problémák:**

* A jelszó-szinkronizálás eleget tegyen az attribútumszűrés által használt cloudFiltered attribútum. Szűrt objektum már nem a jelszó-szinkronizálás hatókörében van.
* Ritka esetekben, ahol a topológia kellett sok tartományvezérlőt a jelszó-szinkronizálás nem működik.
* "Leállt-kiszolgáló" Eszközkezelés után az Azure AD-összekötőről származó importálásakor engedélyezve van az Azure AD/Intune-ban.
* Csatlakozás több tartományt ugyanazon erdő idegen rendszerbiztonsági (FSP) nem egyértelmű illesztési hibát okoz.

## <a name="104751202"></a>1.0.475.1202
Kiadás dátuma: 2014. decemberi

**Új szolgáltatások:**

* Jelszó-szinkronizálás attribútum alapú szűrés mostantól támogatott. További információkért lásd: [jelszó-szinkronizálás szűrése](active-directory-aadconnectsync-configure-filtering.md).
* Az msDS-externaldirectoryobjectid azonosítót attribútum visszaírását az Active Directoryhoz. Ez a szolgáltatás biztosítja az Office 365-alkalmazások támogatását. Az oauth2 protokollt használó hibrid Exchange-telepítéssel az Online és helyszíni postaládákhoz eléréséhez.

**Rögzített frissítési problémák:**

* A bejelentkezési segéd újabb verziója érhető el a kiszolgálón.
* Egyéni telepítési útvonal Azure AD Sync telepítéséhez használt.
* Egy érvénytelen egyéni illesztési feltétel blokkolja a frissítést.

**Egyéb javításai:**

* A sablonok Office Pro Plus rögzíteni.
* Rögzített telepítési problémák okozta a felhasználói neveket, amelyek kezdődhet kötőjellel.
* Rögzített, még egyszer a telepítési varázsló futtatásakor, hogy elveszítenék a sourceAnchor beállítást.
* A jelszó-szinkronizálás fix ETW nyomkövetést.

## <a name="104701023"></a>1.0.470.1023
Kiadás dátuma: 2014. október

**Új szolgáltatások:**

* Több jelszó-szinkronizálás a helyszíni Active Directory, az Azure ad Szolgáltatásba.
* Minden Windows Server-nyelvekre honosított telepítési felület

**Frissítés a AADSync 1.0-s verziója**

Ha már rendelkezik telepített Azure AD Sync, nincs szükség van arra az esetre out-of-box szinkronizálási szabályok egyike módosított egy további lépést. A 1.0.470.1023 történő frissítése után kiadása, a szinkronizálási szabályok módosította ismétlődik. Minden egyes módosított szinkronizálási szabály tegye a következőket:

1.  Keresse meg a szinkronizálási szabály módosította, és jegyezze fel a változásokat.
* A szinkronizálási szabály törlése.
* Keresse meg az Azure AD Sync által létrehozott új szinkronizálási szabályt, és ezután alkalmazza újra a módosításokat.

**Az Active Directory-engedélyek**

Az Active Directory-fiókot kell engedélyeket további el tudják olvasni a jelszókivonatait az Active Directoryból. Az engedélyeket az elnevezése "Replikálása változások" és "Replikációs Directory vált összes." El tudják olvasni a jelszókivonatait mindkét jogosultságokra van szükség.

## <a name="104190911"></a>1.0.419.0911
Kiadás dátuma: 2014. szeptember

**Az Azure AD Sync eredeti kiadását.**

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
