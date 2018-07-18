---
title: 'Az Azure AD Connect: Verziókiadások |} A Microsoft Docs'
description: Ez a cikk felsorolja az Azure AD Connect és az Azure AD Sync összes kiadása
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e808d4bf116dcab344308c3dd2aa06c72e0318ba
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049517"
---
# <a name="azure-ad-connect-version-release-history"></a>Az Azure AD Connect: Verziókiadások
Az Azure Active Directory (Azure AD) csapat rendszeresen frissíti az Azure AD Connect új szolgáltatásait és funkcióit. Nem minden hozzárendelések nem gyarapítsa alkalmazható.


Ez a cikk célja, hogy segítséget nyújtanak a kiadott verziók nyomon követheti, és megismeréséhez a módosításokat a legújabb verzió.

Ez a táblázat az kapcsolódó témakörök listája:

Témakör |  Részletek
--------- | --------- |
Lépések az Azure AD Connect frissítése | Különböző módszerekkel [frissítés egy előző verzióról a legújabbra](active-directory-aadconnect-upgrade-previous-version.md) az Azure AD Connect-kiadásban.
Szükséges engedélyek | Tekintse meg a frissítés alkalmazásához szükséges engedélyek [fiókok és engedélyek](./active-directory-aadconnect-accounts-permissions.md#upgrade).

Letöltés |} [Töltse le az Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Kiadási állapot

5/14/2018: automatikus frissítés és a letöltés.

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

Új funkciók és fejlesztések

- Ebben a kiadásban a nyilvános előzetes verziója a PingFederate-integráció az Azure AD Connect magában foglalja. Ebben a kiadásban ügyfelek egyszerűen és megbízhatóan konfigurálása az Azure Active Directory környezetükben kihasználhatja a pingfederate-tel, az összevonási szolgáltató. Az új szolgáltatással kapcsolatos további tudnivalókért látogasson el a [online dokumentációban](active-directory-aadconnect-user-signin.md#federation-with-pingfederate). 
- Frissítve az Azure AD Connect varázsló hibaelhárítási segédprogram, ahol most elemzi további hiba forgatókönyv, például a társított postaládák és AD-alapú dinamikus csoportok. További információ a hibaelhárítás segédprogram [Itt](active-directory-aadconnect-troubleshoot-objectsync.md).
- Eszközkonfiguráció visszaírás kizárólag az Azure AD Connect varázsló belül mostantól felügyelet alatt.
- Egy új PowerShell-modul nevű ADSyncTools.psm1 kerül, amely az SQL-kapcsolati hibák és a különböző hibaelhárítási segédprogramok segítségével. További információ a ADSyncTools modul [Itt](active-directory-aadconnect-tshoot-sql-connectivity.md). 
- Új további tevékenység "Konfigurálása eszköz options" lett hozzáadva. A feladat segítségével konfigurálhatja az alábbi két műveletet: 
    -   **Hibrid Azure AD-csatlakozás**: Ha a környezetben egy helyszíni AD erőforrás-igényű, és azt is szeretné profitál az Azure Active Directory által biztosított képességek, a hibrid Azure AD-csatlakoztatott eszközök valósíthat meg. Ezek a is, amelyek a helyszíni Active Directory és az Azure Active Directory tartományhoz.
    -   **Eszközvisszaírás**: eszközvisszaírás alapján az AD FS-eszközök feltételes hozzáférésének engedélyezésére használja (2012 R2 vagy újabb) által védett eszközök

   >[!NOTE] 
   > - Engedélyezheti a szinkronizálási beállítások testreszabása történő eszközvisszaírás szürkén jelennek meg. 
   > -  Ebben a kiadásban a PowerShell-modult az Adprep elavult.



### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák 

- Ebben a kiadásban az SQL Server 2012 SP4, amely többek között számos biztonsági rések javításokat nyújt az SQL Server Express telepítések frissíti.  Lásd: [Itt](https://support.microsoft.com/en-ca/help/4018073/sql-server-2012-service-pack-4-release-information) további információ az SQL Server 2012 SP4.
- Szinkronizálási szabály feldolgozása: kimenő illesztési szinkronizálási szabályokat nincs csatlakozás feltétellel kell megszüntetéséhez alkalmazott Ha a fölérendelt szinkronizálási szabály már nem alkalmazható
- Számos kisegítő-javítások alkalmazása a Synchronization Service Manager felhasználói felületén és a szinkronizálási Szabályszerkesztővel
- Az Azure AD Connect varázsló: Hiba történt a létrehozásakor AD-összekötő fiók van amikor az Azure AD Connect egy munkacsoporthoz tartozik
- Az Azure AD Connect varázsló: A az Azure AD-be lapján jelennek meg az ellenőrzési jelölőnégyzetet, ha az AD és az Azure AD-ellenőrzött tartományok bármilyen eltérés van
- Automatikus frissítési PowerShell javítsa ki, hogy automatikus frissítési állapota helyesen bizonyos esetekben próbált automatikus frissítés után.
- Az Azure AD Connect varázsló: Korábban hiányzó adatok rögzítése telemetriai adatok frissítése
- Az Azure AD Connect varázsló: A következő módosításokat használatakor a **felhasználói bejelentkezés módosítása** váltson át az átmenő hitelesítés, az AD FS. feladat:
    - Az átmenő hitelesítés ügynök telepítve van az Azure AD Connect-kiszolgáló és az átmenő hitelesítés szolgáltatás engedélyezve van, mielőtt azt konvertálása felügyeltről összevont felügyelt.
    - Felhasználók már nem konvertálja a felügyelt összevont. Csak a tartomány(ok) konvertálja.
- Az Azure AD Connect varázsló: AD FS több tartomány Regex esetén nem megfelelő felhasználó UPN ' speciális karakter Regex frissítés speciális karakterek támogatása
- Az Azure AD Connect varázsló: Távolítsa el a jelezhet "Konfigurálása a forráshorgony attribútuma" jelenik meg, amikor nincs változás 
- Az Azure AD Connect varázsló: A kettős összevonási forgatókönyvben AD FS-támogatása
- Az Azure AD Connect varázsló: AD FS-jogcímek nem frissülnek a hozzáadott tartomány konvertálása a felügyelt tartományt összevontra során
- Az Azure AD Connect varázsló: A telepített csomagok észlelési, találtunk a régi Dirsync vagy az Azure AD szinkronizálása/az Azure AD Connect kapcsolódó termékeket. Mi most megpróbál távolítsa el az elavult termékeket.
- Az Azure AD Connect varázsló: Megfelelő hiba üzenet leképezése során csatlakoztatott a hitelesítési ügynök telepítése sikertelen
- Az Azure AD Connect varázsló: "Konfiguráció" tároló eltávolítása lapon tartományi szervezeti egységek szűrése
- A szinkronizálási motor telepítése: távolítsa el a szinkronizálási motor telepítése msi szükségtelen régi logikai alkalmanként sikertelen
- Az Azure AD Connect varázsló: Javítás a helyi menü Súgó szövege választható szolgáltatások lapon Jelszókivonat-szinkronizálás
- Szinkronizálás a motor futásidejű: Javítsa ki a forgatókönyvet, ahol a CS objektum rendelkezik egy importált delete és szinkronizálási szabályok próbálja meg újra üzembe a objektum.
- Szinkronizálás a motor futásidejű: felvétele Súgó hivatkozásra az Online kapcsolat hibaelhárítási útmutató az eseménynaplóban az importálási hiba
- Szinkronizálás a motor futásidejű: csökkentett Szinkronizálásütemező memóriahasználata összekötők számbavétele során
- Az Azure AD Connect varázsló: Megoldani a problémát egy egyéni szinkronizálási szolgáltatásfiók nem AD olvasási jogosultsággal rendelkező feloldása
- Az Azure AD Connect varázsló: Javíthatja a tartomány és az OU-szűrés kijelölések naplózása
- Az Azure AD Connect varázsló: MFA forgatókönyvhöz létrehozott összevonási megbízhatósági jogcímekre alapértelmezett AD FS hozzáadása
- Az Azure AD Connect varázsló: AD FS üzembe helyezése WAP: hozzáadása a kiszolgáló nem tud új tanúsítvány használatára
- Az Azure AD Connect varázsló: DSSO kivétel keletkezett onPremCredentials tartomány nincs inicializálva 
- Lehetőleg flow az AD distinguishedName attribútum aktív felhasználó.
- Rögzített formai hibát is az első OOB-szinkronizálási szabály prioritása 99 helyett 100 értékre lett beállítva



## <a name="117510"></a>1.1.751.0
Állapot 4/12/2018:, amely a letölthető csak

>[!NOTE]
>Ez a kiadás egy gyorsjavítás az Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
Javítva a probléma automatikus Azure-beli példány felderítési voltak a China bérlők alkalmanként sikertelen volt.  

### <a name="ad-fs-management"></a>AD FS-kezelőben
#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák

Hiba történt a konfiguráció újrapróbálkozási logika, amely egy ArgumentException figyelmezteti a "ugyanazzal a kulccsal rendelkező elemet már hozzáadták."  Emiatt az összes újrapróbálkozási művelet sikertelen lesz.

## <a name="117500"></a>1.1.750.0
Állapot 3 22-es/2018. március: automatikus frissítési és a letöltés.
>[!NOTE]
>Ha befejeződött a frissítés új verzióra, azt automatikusan aktivál egy teljes szinkronizálást, és az Azure AD-összekötő teljes importálást és teljes szinkronizálást az AD Connectoron. Mivel ez eltarthat egy ideig, az Azure AD Connect környezet méretétől függően győződjön meg arról, hogy elvégezte-e az ennek támogatásához, vagy tartsa addig, amíg egy erre alkalmas időpontban talált a frissítés a szükséges lépéseket.

>[!NOTE]
>"A AutoUpgrade funkció helytelenül letiltotta az egyes bérlők számára, akik buildek 1.1.524.0 később telepítették. Győződjön meg arról, hogy az Azure AD Connect-példány továbbra is költhetők a pénzügyi AutoUpgrade, futtassa a következő PowerShell-parancsmagot: "Set-ADSyncAutoUpgrade - AutoupGradeState engedélyezve"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák

* Set-ADSyncAutoUpgrade parancsmag korábban Autoupgrade lenne letiltása, ha az automatikus frissítési állapot felfüggesztve értékre van állítva. Ez a funkció mostantól megváltozott, így nem blokkolja a jövőbeli buildek AutoUpgrade.
* Módosult a **felhasználói bejelentkezés** oldalon a "Jelszó-szinkronizálás", "A Jelszókivonat-szinkronizálás" lehetőséget.  Az Azure AD Connect szinkronizálja a jelszókivonatok, nem pedig jelszavakkal, így ez igazodik, valójában mi történik a.  További információ: [Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása](active-directory-aadconnectsync-implement-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Állapota: Válassza ki az ügyfelek nyilvánosan

>[!NOTE]
>Ha befejeződött a frissítés új verzióra, azt automatikusan aktivál egy teljes szinkronizálást, és az Azure AD-összekötő teljes importálást és teljes szinkronizálást az AD Connectoron. Mivel ez eltarthat egy ideig, az Azure AD Connect környezet méretétől függően győződjön meg arról, hogy elvégezte-e az ennek támogatásához, vagy tartsa addig, amíg egy erre alkalmas időpontban talált a frissítés a szükséges lépéseket.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
* Javítsa ki a időzítési háttérfeladatok laphoz Partíciószűrés a következő lapra történő váltáskor.

* Kijavítva a hiba, amely engedély nélküli hozzáférési kísérlet során a configdb elemre egyéni művelet okozza.

* Kijavítva a hiba, helyreállítás az SQL-kapcsolat időtúllépése.

* Kijavítva a hiba, ahol a SAN helyettesítő tanúsítványok előfeltétel-ellenőrzése nem sikerült.

* Kijavítva a hiba, amely összeomlását miiserver.exe egy Azure AD-összekötő exportálása során.

* Kijavítva a hiba, mely helytelen jelszómegadási kísérletek naplózza a tartományvezérlőn, a konfiguráció módosítása az Azure AD Connect varázsló futtatásakor.


#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* Az általános adatvédelmi rendelet (GDPR) adatvédelmi beállítások hozzáadása.  További információ: a cikk [Itt](active-directory-aadconnect-gdpr.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* Alkalmazáshasználati - rendszergazda válthat Ez az osztály az adatok be/ki lesz:

* Az Azure AD Health-adatok – rendszergazdai fel kell keresnie azok állapotfigyelő beállításait szabályozhatja a health portálon.
   Után a szolgáltatás házirend módosítva lett, az ügynökök olvassa el, és azt kényszerítése.

* A hozzáadott eszköz késleltetve visszaírt konfigurációs műveletek és a egy folyamatjelző sáv se inicializace stránky.

* Általános diagnosztikai javult a HTML-jelentést és a teljes adatgyűjtést a ZIP-szöveg / HTML-jelentés

* Továbbfejlesztett automatikus frissítés és a hozzáadott további telemetriai adatokat, győződjön meg arról is meg lehet határozni a kiszolgáló állapotát, a megbízhatóság

* Kiemelt jogosultságú fiókok AD-összekötő fiókhoz elérhető engedélyek korlátozása

  * Az új, a varázsló fogja korlátozni a hozzáférést, amely a kiemelt jogosultságú fiókok tartozik az MSOL-fiók létrehozását követően a MSOL-fiókhoz.

A módosítások gondoskodik a következőket:
1. Expressz telepítés
2. Egyéni telepítés fiókkal automatikus létrehozása
3. A telepítő megváltozott, így nincs szükség rendszergazdai jogosultság az Azure AD Connect tiszta telepítését

* Egy adott objektumra szinkronizálás hibáinak elhárítása új segédprogram hozzá. A "Hibaelhárítás Felügyeltobjektum-szinkronizációs" beállítás az Azure AD Connect varázsló hibaelhárítása további feladat érhető el. Jelenleg a segédprogram ellenőrzi a következőket:

  * Szinkronizált felhasználói objektum és a felhasználói fiók az Azure AD-bérlő a UserPrincipalName nem egyezik.
  * Ha az objektum ki lett szűrve a tartomány szűrés miatt
  * Ha az objektum ki lett szűrve a szervezeti egység (OU) szűrés miatt

* Az egy adott felhasználói fiók az a helyszíni Active Directoryban tárolt aktuális Jelszókivonat szinkronizálása egy új eszköz hozzá.

A segédprogram nem szükséges a jelszó módosítása. Az Azure AD Connect varázsló hibaelhárítása további feladat "Jelszókivonat-szinkronizálás hibáinak elhárítása" beállítás érhető el.






## <a name="116540"></a>1.1.654.0
Állapot: 2017. December 12.

>[!NOTE]
>Ebben a kiadásban, egy biztonsági kapcsolódó gyorsjavítás az Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Egy fokozása bővült az Azure AD Connect verziója 1.1.654.0 (és után) Győződjön meg arról, hogy a javasolt engedély módosul a szakaszban ismertetett alatt [zár le a hozzáférést az AD DS-fiókot](#lock) mikor automatikusan alkalmazza az Azure AD Csatlakozás az AD DS-fiókot hoz létre. 

- Az Azure AD Connect beállításakor a telepítése rendszergazdai adjon meg egy meglévő AD DS-fiókot, vagy is lehetővé teszik az Azure AD Connect automatikusan hozza létre a fiókot. A engedélyeinek módosítása a rendszer automatikusan alkalmazza az AD DS-fiókot, amely az Azure AD Connect telepítés során jön. Ezek nem vonatkoznak a telepítése a rendszergazda által biztosított meglévő AD DS-fiókot.
- Az ügyfelek, akik frissítettek az Azure AD Connect egy régebbi verzióját 1.1.654.0 (vagy után), az engedély módosításai nem visszamenőlegesen lépnek érvénybe, a frissítés előtt létrehozott meglévő AD DS-fiókot. Ezek csak lépnek érvénybe, a frissítés után létrehozott új AD DS-fiókok. Ez akkor fordul elő, ha a hozzáadni kívánt új AD-erdőkkel lesznek szinkronizálva az Azure AD.

>[!NOTE]
>Ez a kiadás csak a biztonsági rés az új Azure AD Connect, ahol a szolgáltatás fiók jön létre a telepítési folyamat. A meglévő telepítések, vagy azokban az esetekben, ahol meg kell a fiókot saját maga győződjön meg róla, hogy a biztonsági rés nem létezik.

#### <a name="lock"></a> Az AD DS-fiókot való hozzáférés zárolása
Az AD DS-fiókot a következő engedélyeinek módosítása a helyszíni alkalmazásával való hozzáférés zárolása AD:  

*   A megadott objektum öröklődés letiltása
*   Távolítsa el az adott objektumra, kivéve az adott ÖNKISZOLGÁLÓ hozzáférés-vezérlési bejegyzés vonatkozó összes ACE. Szeretnénk az alapértelmezett engedélyek ép esetén, a saját MAGA.
*   Ezek az engedélyek hozzárendelése:

Típus     | Name (Név)                          | Hozzáférés               | Erre vonatkozik
---------|-------------------------------|----------------------|--------------|
Engedélyezés    | RENDSZER                        | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Vállalati rendszergazdák             | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Tartományi rendszergazdák                 | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Rendszergazdák                | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Tartalom listázása        | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Az összes tulajdonság olvasása  | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Olvasási engedélyek     | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Tartalom listázása        | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Az összes tulajdonság olvasása  | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Olvasási engedélyek     | Ez az objektum  |

A beállítások a az AD DS-fiók megerősítéséhez futtathatja [a PowerShell-szkript](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). A PowerShell-szkriptet fogja hozzárendelni az engedélyeket, az AD DS-fiókot a fent említett.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell-parancsprogram egy meglévő szolgáltatásfiókot megerősítéséhez

A PowerShell-parancsprogram használatával alkalmazni ezeket a beállításokat egy már meglévő AD DS-fiókot (a szervezet biztosítja nekik vagy az Azure AD Connect egy előző telepítés által létrehozott távolítsuk töltse le a parancsfájlt a fenti hivatkozásra.

##### <a name="usage"></a>Használat

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Ahol (a(z) 

**$ObjectDN** = az Active Directory-fiók, amelynek engedélyeket kell megfeszíteni kell.

**$Credential** = rendszergazdai hitelesítő adatok, amely rendelkezik a szükséges jogosultságokkal a $ObjectDN fiók az engedélyek korlátozása. Ezek a jogosultságok általában tulajdonában vannak a vállalati vagy tartományi rendszergazdával. A rendszergazdai fiók a teljesen minősített tartománynevét használja a fiók keresési hibák elkerülése érdekében. Példa: contoso.com\admin.

>[!NOTE] 
>$credential. Felhasználónév FQDN\username formátumúnak kell lennie. Példa: contoso.com\admin 

##### <a name="example"></a>Példa:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>A biztonsági rés használt jogosulatlan elérésére?

Megtekintheti, ha a biztonsági rés használt veszélyezteti az Azure AD Connect konfiguráció ellenőriznie kell, a legutóbbi jelszó alaphelyzetbe szolgáltatásfiók dátum.  Ha a nem várt időbélyegző, teendőkért, az Eseménynapló-n keresztül a, hogy a jelszó-átállítási esemény, kell végezni.

További információkért lásd: [Microsoft biztonsági tanácsadó 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Állapot: 2017. október 27.

>[!NOTE]
>A build az ügyfeleket az Azure AD Connect automatikus frissítés funkció nem érhető el.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Kijavítva a hiba
* Az Azure AD Connect és az Azure AD Connect Health-ügynök (a sync) közötti verziójú kompatibilitási probléma kijavítva. A probléma hatással van az ügyfelek, akik az Azure AD Connect helyben frissítés 1.1.647.0 verzióra végez, de jelenleg a Health-ügynök verziója 3.0.127.0 rendelkezik. A frissítés után a Health Agent többé nem küldhet állapot adatait az Azure AD Connect szinkronizálási szolgáltatás az Azure AD Health szolgáltatásba. A javítás verzió 3.0.129.0 Health-ügynök telepítve van az Azure AD Connect helyben frissítés során. Rendszerállapot-ügynök verziója 3.0.129.0 nincs kompatibilitási probléma az Azure AD Connect verziója 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Állapot: 19 2017. október

> [!IMPORTANT]
> Egy ismert kompatibilitási probléma az Azure AD Connect 1.1.647.0 és az Azure AD Connect Health-ügynök (a sync) verziója 3.0.127.0 között van. A probléma megakadályozza, hogy a Health-ügynök állapotának adatait az Azure AD Connect szinkronizálási szolgáltatás (beleértve a hibák és a futtatási előzményadatok) küld az Azure AD Health szolgáltatásnak. Mielőtt manuálisan verziójára való frissítését az Azure AD Connect üzemelő példány 1.1.647.0, ellenőrizze, hogy az Azure AD Connect Health-ügynök jelenlegi verziója telepítve van az Azure AD Connect-kiszolgálón. Ehhez a *Vezérlőpult → Programok* , és keressen az alkalmazás *a Microsoft Azure AD Connect Health Agent for Sync*. Ha a verzió 3.0.127.0, ajánlott a következő Azure AD Connect verziója elérhető a frissítés előtt várja. A Health-ügynök verziója nem 3.0.127.0, ha nem okoz gondot a manuális, helybeni frissítés folytatásához. Vegye figyelembe, hogy a probléma nem érinti párhuzamos frissítés vagy az ügyfelek, akik az Azure AD Connect új telepítést hajt végre.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
* Kijavítva az a *felhasználói bejelentkezés módosítása* feladat az Azure AD Connect varázslójában:

  * A probléma akkor fordul elő, ha a jelszó-szinkronizálás a meglévő Azure AD Connect telepítés **engedélyezve**, és állítsa be a felhasználói bejelentkezés módját kívánt *átmenő hitelesítés*. Mielőtt a rendszer alkalmazza a módosítást, a varázsló nem megfelelően jeleníti meg a "*jelszó-szinkronizálás letiltása*" parancssort. Azonban a jelszó-szinkronizálás engedélyezett marad után a rendszer alkalmazza a módosítást. A javítás a varázsló már nem látható a rendszer kéri.

  * A kialakításból fakadóan a varázsló nem tiltja le a jelszó-szinkronizálás a felhasználói bejelentkezési módszer használatával frissítésekor a *felhasználói bejelentkezés módosítása* feladat. Ez a fenntartásához, akiknek szeretné tartani a jelszó-szinkronizálás, annak ellenére, hogy az átmenő hitelesítés vagy összevonás módszerként elsődleges felhasználói bejelentkezési engedélyezi.
  
  * Ha szeretné a jelszó-szinkronizálás letiltása a felhasználó bejelentkezési módszer frissítése után, végre kell hajtani a *testre szabhatja a szinkronizálási konfiguráció* feladat a varázslóban. Kiválasztásakor az *választható funkciók* lapon, törölje a jelet a *jelszó-szinkronizálás* lehetőséget.
  
  * Vegye figyelembe, hogy is ugyanez a probléma akkor fordul elő, ha megpróbálja zökkenőmentes egyszeri bejelentkezés engedélyezése vagy letiltása. Pontosabban, a jelszó-szinkronizálás engedélyezve van egy meglévő Azure AD Connect üzemelő, és a felhasználói bejelentkezési mód már konfigurálva van *átmenő hitelesítés*. Használatával a *felhasználói bejelentkezés módosítása* bejelölése/jelölésének törlése próbál a feladatban a *engedélyezése zökkenőmentes egyszeri bejelentkezést* amíg a felhasználó bejelentkezési módszer marad konfigurált "Átmenő hitelesítés" lehetőséget. Mielőtt a rendszer alkalmazza a módosítást, a varázsló nem megfelelően jeleníti meg a "*jelszó-szinkronizálás letiltása*" parancssort. Azonban a jelszó-szinkronizálás engedélyezett marad után a rendszer alkalmazza a módosítást. A javítás a varázsló már nem látható a rendszer kéri.

* Kijavítva az a *felhasználói bejelentkezés módosítása* feladat az Azure AD Connect varázslójában:

   * A probléma akkor fordul elő, ha a jelszó-szinkronizálás a meglévő Azure AD Connect telepítés **le van tiltva**, és állítsa be a felhasználói bejelentkezés módját kívánt *átmenő hitelesítés*. Amikor a rendszer alkalmazza a módosítást, a varázsló lehetővé teszi, hogy átmenő hitelesítést és a jelszó-szinkronizálás. A javítás a varázsló már nem engedélyezi a jelszó-szinkronizálás.

  * A jelszó-szinkronizálás korábban átmenő hitelesítés engedélyezése előfeltétele. A felhasználói bejelentkezés módját beállításakor *átmenő hitelesítés*, a varázsló lehetővé tenné az átmenő hitelesítés és a jelszó-szinkronizálás. A jelszó-szinkronizálás nemrég el lett távolítva, előfeltételként. Az Azure AD Connect verziója 1.1.557.0 részeként egy módosítást hajtottak végre az Azure AD Connect nem a jelszó-szinkronizálás engedélyezése a felhasználó bejelentkezési módszer, beállításakor *átmenő hitelesítés*. A módosítás azonban csak alkalmazta az Azure AD Connect-telepítésre. A javítás kártyáinak is hatással van a *felhasználói bejelentkezés módosítása* feladat.
  
  * Vegye figyelembe, hogy is ugyanez a probléma akkor fordul elő, ha megpróbálja zökkenőmentes egyszeri bejelentkezés engedélyezése vagy letiltása. Pontosabban, rendelkezik egy meglévő Azure AD Connect üzemelő jelszó-szinkronizálás le van tiltva, és a felhasználói bejelentkezési mód már konfigurálva van *átmenő hitelesítés*. Használatával a *felhasználói bejelentkezés módosítása* bejelölése/jelölésének törlése próbál a feladatban a *engedélyezése zökkenőmentes egyszeri bejelentkezést* amíg a felhasználó bejelentkezési módszer marad konfigurált "Átmenő hitelesítés" lehetőséget. Amikor a rendszer alkalmazza a módosítást, a varázsló lehetővé teszi, hogy a jelszó-szinkronizálás. A javítás a varázsló már nem engedélyezi a jelszó-szinkronizálás. 

* Kijavítva a hiba az Azure AD Connect frissítésének sikertelenségét okozó "*a Synchronization Service frissítése nem sikerült*". További, a szinkronizálási szolgáltatás nem indítható az eseménnyel kapcsolatos hiba "*a szolgáltatás nem tudta elindítani, mert az adatbázis verziója újabb, mint a bináris fájlok telepített verziója*". A probléma akkor fordul elő, ha a rendszergazda, a frissítés nem rendelkezik az Azure AD Connect által használt SQL Server rendszergazdai jogosultságra. A javítás az Azure AD Connect az ADSync-adatbázis db_owner jogosultsággal rendelkezik a frissítés során a rendszergazda csak szükséges.

* Kijavítva az Azure AD Connect frissítés, amely az érintett ügyfelek, akik engedélyezték [zökkenőmentes egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Után az Azure AD Connect frissítve van, zökkenőmentes egyszeri bejelentkezést nem megfelelően jelenik meg, mert le van tiltva, az Azure AD Connect varázsló annak ellenére, hogy a funkció engedélyezve van, és teljesen működőképes marad. A javítás a szolgáltatás megjelenik megfelelően szerint engedélyezett, a varázslóban.

* Javítva lett egy probléma kiváltó mindig az Azure AD Connect varázsló megjelenítése a "*Forráshorgony konfigurálása*" Rákérdezés a a *készen áll a konfigurálás* lapon, akkor is, ha nem kapcsolódnak a Forráshorgony történt módosítás.

* Manuális helyszíni frissítését az Azure AD Connect hajt végre, amikor az ügyfél a megfelelő Azure AD-bérlő globális rendszergazdai hitelesítő adatok megadása szükséges. Korábban a frissítés sikerült folytatható annak ellenére, hogy a globális rendszergazdai hitelesítő adatokkal tartozott, egy másik Azure AD-bérlővel. Amíg a frissítés úgy tűnik, hogy sikeresen befejeződik, bizonyos konfigurációk nem megfelelően rögzíti a frissítést. Ezzel a varázsló megakadályozza, hogy a frissítés eljárás, ha a megadott hitelesítő adatok nem egyeznek meg az Azure AD-bérlővel.

* Eltávolítja a redundáns logika, amely szükségtelenül újraindítja az Azure AD Connect Health szolgáltatás kézi frissítés kezdetén.


#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* A hozzáadott logikai egyszerűsítése érdekében a az Azure AD Connect a Microsoft németországi Felhőhöz beállításához szükséges lépéseket. Korábban az Ön szükséges frissíteni az Azure AD Connect-kiszolgálón, hogy megfelelően működjön a Microsoft németországi Felhőhöz, meghatározott beállításkulcsokról, ebben a cikkben leírtak szerint. Most az Azure AD Connect automatikusan képes felismerni Ha a bérlő van a Microsoft németországi felhőben alapján a telepítés során megadott globális rendszergazdai hitelesítő adatok.

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
>[!NOTE]
> Megjegyzés: A szinkronizálási szolgáltatás, amely lehetővé teszi a saját egyéni scheduler fejlesztése WMI felülettel rendelkezik. Ez az interfész már elavult, és jövőbeli törlődni fog az Azure AD Connect verziója tartalmazza a szükséges 2018. június 30. után. Ügyfelek, akik a szinkronizálási ütemezés testreszabásához használja a [beépített ütemezési (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
* Ha az Azure AD Connect varázsló a szükséges szinkronizálni a módosításokat a helyszíni Active Directoryból AD Connectoron fiókot hoz létre, azt nem megfelelően rendel hozzá a fiók PublicFolder objektumok olvasásához szükséges engedély. A probléma hatással van a gyors telepítés és a testreszabott telepítés. Ez a változás megoldja a problémát.

* Rögzített egy kiváltó probléma az Azure AD Connect varázsló hibaelhárítási lapon nem jelennek meg helyesen a rendszergazdák számára a Windows Server 2016 rendszerű.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Hibaelhárítási lap az Azure AD Connect varázsló segítségével a jelszó-szinkronizálás hibaelhárítása, amikor a hibaelhárítás lapon, az mostantól tartomány-specifikus állapotát adja vissza.

* Korábban a próbált Jelszókivonat-szinkronizálás engedélyezése, ha az Azure AD Connect nem ellenőrzi az AD-összekötő fiók e szükséges engedélyekkel a jelszókivonatok szinkronizálása a helyszíni AD. Most az Azure AD Connect varázsló győződjön meg arról, és figyelmezteti, ha az AD-összekötő fiók nem rendelkezik megfelelő engedélyekkel.

### <a name="ad-fs-management"></a>AD FS-kezelőben
#### <a name="fixed-issue"></a>Kijavítva a hiba
* A használatával kapcsolatos probléma kijavítva [msDS-ConsistencyGuid, mint a Forráshorgony](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkció. A probléma hatással van az ügyfelek, akik a beállított *és az AD FS összevonási* felhasználói bejelentkezési módszert. Végrehajtása közben *Forráshorgony konfigurálása* feladata a varázslót, az Azure AD Connect használatával vált * ms-DS-ConsistencyGuid immutableId a forrás-attribútumként. Ez a változás részeként az Azure AD Connect megkísérli jogcímszabályainak frissítése az AD FS immutableid azonosítója. Azonban ez a lépés nem sikerült, mert az Azure AD Connect nem volt az rendszergazdájának hitelesítő adatait az AD FS konfigurálásához szükséges. A javítás az Azure AD Connect most megadását kéri a rendszergazdai hitelesítő adatait az AD FS végrehajtása közben a *Forráshorgony konfigurálása* feladat.



## <a name="116140"></a>1.1.614.0
Állapot: 05 2017. szeptember

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Ismert problémák
* Egy ismert probléma, amely a hiba okozza a az Azure AD Connect frissítése sikertelen "*a Synchronization Service frissítése nem sikerült*". További, a szinkronizálási szolgáltatás nem indítható az eseménnyel kapcsolatos hiba "*a szolgáltatás nem tudta elindítani, mert az adatbázis verziója újabb, mint a bináris fájlok telepített verziója*". A probléma akkor fordul elő, ha a rendszergazda, a frissítés nem rendelkezik az Azure AD Connect által használt SQL Server rendszergazdai jogosultságra. Dbo engedélyek nem elegendőek.

* Van egy ismert probléma az Azure AD Connect frissítés, amely befolyásolja az ügyfelek, akik engedélyezték [zökkenőmentes egyszeri bejelentkezést](active-directory-aadconnect-sso.md). Után az Azure AD Connect frissítve van, a szolgáltatás megjelenik a módon le van tiltva, a varázslóban, annak ellenére, hogy a szolgáltatás továbbra is engedélyezett marad. A javítás a probléma jövőbeni biztosítjuk engedje el. A megjelenített problémáról az érintett felhasználókat manuálisan javíthassuk azt a varázsló zökkenőmentes egyszeri bejelentkezés engedélyezésével.

#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
* Javítva lett egy probléma, amely ebben az esetben az Azure AD Connect frissítése a jogcímszabályok a helyszíni Active Directory összevonási szolgáltatások, miközben lehetővé teszi a [msDS-ConsistencyGuid Forráshorgony,](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkció. A probléma akkor fordul elő, ha megpróbálja engedélyezni a meglévő Azure AD Connect központi telepítésre van konfigurálva, a bejelentkezési módszer az AD FS szolgáltatást. A probléma oka, hogy a varázsló nem ADFS hitelesítő adatok kérése előtt próbálja frissíteni a jogcímszabályok az AD FS-ben.
* Javítva lett egy probléma, amely miatt a telepítés sikertelen lesz, ha az Azure AD Connect a helyszíni AD-erdőben van, az NTLM le van tiltva. A probléma okozza az Azure AD Connect varázsló a Kerberos-hitelesítéshez szükséges biztonsági környezet létrehozásakor nem adja meg teljesen minősített hitelesítő adatokat. Ennek hatására a Kerberos-hitelesítés sikertelen lesz, és az Azure AD Connect varázsló tud visszatérni NTLM használatával.

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
* Javítva lett egy probléma, ahol új szinkronizálási szabály nem hozható létre, ha a címke attribútuma nincs feltöltve adatokkal.
* Csatlakozás az Azure AD Connect okozó probléma kijavítva a helyszíni AD jelszó-szinkronizálás használatával NTLM, annak ellenére, hogy a Kerberos érhető el. Ez a probléma akkor fordul elő, ha a helyszíni AD-topológia rendelkezik legalább egy biztonsági másolatból visszaállított tartományvezérlőket.
* Rögzített feleslegesen történjen a frissítés után teljes szinkronizálást lépéseket kiváltó problémát. Általában lépéseket a teljes szinkronizálás futtatása szükség a frissítés után mértékben out-of-box szinkronizálási szabályait. A probléma volt a módosítás észlelő logika, amely nem megfelelően változást észlelt, amikor soremelés karaktereket a szinkronizálási szabály kifejezés egy hiba miatt. Soremelés karakter elé kerülnek a szinkronizálási szabály kifejezésben a jobb olvashatóság érdekében.
* Kijavítva, amelyek miatt az Azure AD Connect-kiszolgáló nem működnek megfelelően az automatikus frissítés után. Ezt a hibát észleli az Azure AD Connect verziója 1.1.443.0-kiszolgálókat (vagy korábbi). A problémával kapcsolatos részletekért tekintse meg a cikk [az Azure AD Connect nem működik megfelelően az automatikus frissítés után](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Kijavítva, amelyek miatt az automatikus frissítés újrapróbálására át 5 percenként, ha hiba történik. A javítás, az automatikus frissítés újrapróbálkozik az exponenciális visszatartás amikor hiba történik.
* Javítva lett egy probléma, ahol jelszó-szinkronizálás esemény 611 helytelenül jelenik meg a Windows alkalmazás eseménynaplóit, **tájékoztató** helyett **hiba**. 611 esemény jön létre, amikor a jelszó-szinkronizálás problémát észlel. 
* Javítva lett egy probléma az Azure AD Connect varázsló, amely lehetővé teszi, hogy a csoport-visszaírási funkciót anélkül, hogy egy szervezeti Egységet a csoportok visszaírásához szükséges engedélyezni kell.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Hibaelhárítás feladat hozzá a további műveletek az Azure AD Connect varázslót. Ez a feladat általános diagnosztikai adatok gyűjtéséhez és a jelszó-szinkronizálás kapcsolatos problémák elhárítása az ügyfelek használhatják. A továbbiakban a hibaelhárítás feladat fog kell terjeszteni más címtár-szinkronizálás kapcsolatos problémák is.
* Az Azure AD Connect mostantól támogatja a egy új telepítési mód nevű **létező adatbázis használata**. Ez a telepítési mód lehetővé teszi az Azure AD Connect, amely meghatározza a meglévő ADSync-adatbázis telepítése. Ezen szolgáltatásáról kapcsolatos további információkért tekintse meg a cikk [létező adatbázis használata](active-directory-aadconnect-existing-database.md).
* A fokozott biztonság céljából az Azure AD Connect mostantól alapértelmezés szerint az TLS1.2 csatlakozni az Azure AD-címtár-szinkronizálás. Az alapértelmezett korábban TLS1.0.
* Azure AD Connect jelszó szinkronizálási ügynök indulásakor próbál kapcsolódni az Azure AD jelszó-szinkronizálás a jól ismert végpont. A kapcsolat létrejötte után a rendszer átirányítja az egy régióspecifikus végpontot. Korábban a jelszó-szinkronizálási ügynök gyorsítótárazza a régióspecifikus végpont, ügyfélt. Most az ügynök törli a gyorsítótárat, és a jól ismert végponttal újrapróbálkozások Ha csatlakozási probléma a régióspecifikus végponttal tapasztal. Ez a változás biztosítja, hogy a jelszó-szinkronizálás feladatátvételt egy másik régióspecifikus végpont is, ha már nem érhető el a gyorsítótárazott régióspecifikus végpont.
* Helyszíni AD-erdőből származó módosítások szinkronizálásához AD DS-fiók szükséges. (I) létrehozhat az AD DS fiókot saját maga és a hitelesítő adatok megadása az Azure AD Connect, vagy (ii) adjon meg egy vállalati rendszergazdai hitelesítő adatokat, és hozza létre az AD DS-fiókot az Azure AD Connect segítségével. (I) a korábban, az alapértelmezett beállítás az Azure AD Connect varázslójában. Most (ii) az alapértelmezett beállítás.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Támogatás hozzáadva a Microsoft Azure Government Cloud és a Microsoft Cloud németországi adatközpontjában.

### <a name="ad-fs-management"></a>AD FS-kezelőben
#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
* Az Initialize-ADSyncNGCKeysWriteBack parancsmag AD előkészítő powershell modulok helytelenül lett ACL-ek alkalmazása az eszközregisztrációs tároló, és csak így öröklik az engedélyeket, amelyekkel.  Ez frissítve lett, hogy a szinkronizálási szolgáltatásfiók megfelelő engedélyekkel rendelkezik.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Az AAD Connect ellenőrzése AD FS bejelentkezési tevékenység frissítve lett, hogy azt ellenőrzi, hogy a Microsoft Online és az AD FS csak jogkivonat lekéréséhez bejelentkezéseket.
* Állítson be egy új AD FS-farm AAD Connect használatával, az AD FS hitelesítő adatokat kérő oldal áthelyezték azt, hogy most már előtt a felhasználónak meg kell adnia az AD FS és WAP-kiszolgálót adja meg.  Ez lehetővé teszi, hogy AAD Connect ellenőrizze, hogy a megadott fiók rendelkezik-e megfelelő engedélyekkel.
* Az AAD Connect frissítése során már nem tudjuk meghiúsul frissítés, ha az AD FS AAD-megbízhatóság nem tud frissíteni.  Ha ez történik, a felhasználó jelenik meg egy megfelelő figyelmeztető üzenetet, és alaphelyzetbe állítja a feladattal az AAD Connect további megbízhatósági kapcsolatot kell eljárnia.

### <a name="seamless-single-sign-on"></a>Közvetlen egyszeri bejelentkezés
#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák
* Az Azure AD Connect varázsló hibát adhat vissza, ha megpróbálja engedélyezni a kiváltó probléma kijavítva [zökkenőmentes egyszeri bejelentkezést](active-directory-aadconnect-sso.md). A hibaüzenet *"Konfiguráció a Microsoft Azure AD Connect hitelesítési ügynökének nem sikerült."* Ezt a hibát észleli a meglévő ügyfeleink manuálisan kellett frissített előzetes verzióját, a hitelesítési ügynök [átmenő hitelesítés](active-directory-aadconnect-sso.md) leírt lépésekkel alapján [cikk](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Állapot: 2017. július 23.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Kijavítva a hiba

* Kijavítva a beépített szinkronizálási szabály "Az AD - felhasználó ImmutableId Out" okozó el kell távolítani:

  * A probléma akkor fordul elő, ha az Azure AD Connect frissítve van, vagy ha a feladat beállítás *szinkronizálási konfiguráció frissítése* az Azure AD Connect varázsló használata az Azure AD Connect szinkronizálási konfiguráció frissítése.
  
  * A szinkronizálási szabály az ügyfél számára, akik engedélyezték a [msDS-ConsistencyGuid Forráshorgony funkcióként](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). Ez a funkció verzió 1.1.524.0 és követően jelent meg. A szinkronizálási szabály eltávolítása után az Azure AD Connect már nem töltheti fel a helyszíni AD-ms-DS-ConsistencyGuid attribútum az ObjectGuid attribútum értéke. Nem akadályozza meg azt, hogy a felhasználók új önköltséggel Azure AD-be.
  
  * A javítás biztosítja, hogy a szinkronizálási szabály már nem törlődnek a frissítés során, vagy a konfiguráció módosításának, mindaddig, amíg a szolgáltatás engedélyezve van. A meglévő ügyfeleknek, akik a probléma által érintett a javítás emellett biztosítja, hogy a szinkronizálási szabály vissza erre a verzióra, az Azure AD Connect frissítése után kerül.

* Kijavítva az out-of-box szinkronizálási szabályokat, hogy a sorrend értéke 100-nál kisebb okozó hibát:

  * Egyéni szinkronizálási szabályait általában elsőbbséget értékek 0 – 99 vannak fenntartva. A frissítés során szinkronizálási szabály módosítások befogadásához frissíti az elsőbbségi értékeket out-of-box szinkronizálási szabályait. Ez a probléma miatt out-of-box szinkronizálási szabályok rendelt sorrend értéke 100-nál kisebb.
  
  * A javítás megakadályozza, hogy a probléma lépett fel a frissítés során. Azonban ez nem állítja vissza a sorrend értékek ügyfeleink, akik a probléma által érintett. Egy különálló javítást a jövőben nyújtanak segítséget a visszaállítást.

* Megtörtént egy probléma javítása, a [tartomány és szervezeti egységek szűrése képernyő](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) az az Azure AD Connect varázsló jelenik *minden tartomány és szervezeti egységek szinkronizálása* lehetőség választása, annak ellenére, hogy a szervezeti egység szerinti szűrés engedélyezve van.

*   Megtörtént egy probléma javítása, amely miatt a [könyvtárpartíciók konfigurálása képernyő](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) a a Synchronization Service Managert, hibát ad vissza, ha a *frissítése* gombra kattint. A hibaüzenet *"hiba történt a tartományok frissítése közben: írja be a" System.Collections.ArrayList"típusú objektum nem lehet" Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* A hiba akkor fordul elő, amikor új AD-tartomány hozzáadása egy meglévő AD-erdőhöz, és a frissítés gomb használatával az Azure AD Connect frissíteni szeretne.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* [Az automatikus frissítési funkció](active-directory-aadconnect-feature-automatic-upgrade.md) ki lett terjesztve, amelyet felkereshetnek a felhasználók az alábbi konfigurációkkal:
  * Engedélyezte, hogy az eszköz a jelszóvisszaíró szolgáltatás.
  * A csoport-visszaírási funkciót engedélyezte.
  * A telepítés még nem egy gyorsbeállításokat vagy a DirSync frissítése.
  * Több mint 100 000 objektummal rendelkezik a metaverzumban.
  * Egynél több erdő csatlakozik. Expressz telepítés csak egy erdőben csatlakozik.
  * Az AD-összekötő fiók már nem az alapértelmezett MSOL_ fiókot.
  * A kiszolgáló átmeneti módra való beállítása.
  * Engedélyezte, hogy a felhasználó a jelszóvisszaíró szolgáltatás.
  
  >[!NOTE]
  >A hatókör az automatikus frissítési szolgáltatás kibővített változata hatással van ügyfeleink az Azure AD Connectben felépítési 1.1.105.0 és után. Ha nem szeretné automatikusan frissíteni az Azure AD Connect-kiszolgáló, futtatnia kell az alábbi parancsmagot az Azure AD Connect-kiszolgálón: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Engedélyezése vagy letiltása az automatikus frissítés kapcsolatos további információkért tekintse meg a cikk [az Azure AD Connect: automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Állapot: Nem elérhető lesz. A build változásai verzió 1.1.561.0 szerepelnek.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Kijavítva a hiba

* Kijavítva a beépített szinkronizálási szabály "Az AD - felhasználó ImmutableId Out" okozó el kell távolítani a szervezeti egység szerinti szűrés konfiguráció frissítésekor. A szinkronizálási szabály szükség a [msDS-ConsistencyGuid Forráshorgony funkcióként](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).

* Megtörtént egy probléma javítása, a [tartomány és szervezeti egységek szűrése képernyő](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) az az Azure AD Connect varázsló jelenik *minden tartomány és szervezeti egységek szinkronizálása* lehetőség választása, annak ellenére, hogy a szervezeti egység szerinti szűrés engedélyezve van.

*   Megtörtént egy probléma javítása, amely miatt a [könyvtárpartíciók konfigurálása képernyő](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) a a Synchronization Service Managert, hibát ad vissza, ha a *frissítése* gombra kattint. A hibaüzenet *"hiba történt a tartományok frissítése közben: írja be a" System.Collections.ArrayList"típusú objektum nem lehet" Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* A hiba akkor fordul elő, amikor új AD-tartomány hozzáadása egy meglévő AD-erdőhöz, és a frissítés gomb használatával az Azure AD Connect frissíteni szeretne.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* [Az automatikus frissítési funkció](active-directory-aadconnect-feature-automatic-upgrade.md) ki lett terjesztve, amelyet felkereshetnek a felhasználók az alábbi konfigurációkkal:
  * Engedélyezte, hogy az eszköz a jelszóvisszaíró szolgáltatás.
  * A csoport-visszaírási funkciót engedélyezte.
  * A telepítés még nem egy gyorsbeállításokat vagy a DirSync frissítése.
  * Több mint 100 000 objektummal rendelkezik a metaverzumban.
  * Egynél több erdő csatlakozik. Expressz telepítés csak egy erdőben csatlakozik.
  * Az AD-összekötő fiók már nem az alapértelmezett MSOL_ fiókot.
  * A kiszolgáló átmeneti módra való beállítása.
  * Engedélyezte, hogy a felhasználó a jelszóvisszaíró szolgáltatás.
  
  >[!NOTE]
  >A hatókör az automatikus frissítési szolgáltatás kibővített változata hatással van ügyfeleink az Azure AD Connectben felépítési 1.1.105.0 és után. Ha nem szeretné automatikusan frissíteni az Azure AD Connect-kiszolgáló, futtatnia kell az alábbi parancsmagot az Azure AD Connect-kiszolgálón: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Engedélyezése vagy letiltása az automatikus frissítés kapcsolatos további információkért tekintse meg a cikk [az Azure AD Connect: automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Állapot: 2017. július

>[!NOTE]
>A build az ügyfeleket az Azure AD Connect automatikus frissítés funkció nem érhető el.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Kijavítva a hiba
* Kijavítva az Initialize-ADSyncDomainJoinedComputerSync parancsmag által okozott az ellenőrzött tartományt a meglévő szolgáltatás kapcsolódási pont objektumot módosítani, akkor is, ha továbbra is érvényes tartomány a következőn:. Ez a probléma akkor fordul elő, ha az Azure AD-bérlővel rendelkezik, amely a szolgáltatáskapcsolati pont konfigurálásához használható egynél több ellenőrzött tartomány.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* A jelszóvisszaíró már elérhető előzetes verzióban a Microsoft Azure Government felhőben és a Microsoft Cloud németországi adatközpontjában. A különböző példányok az Azure AD Connect-támogatással kapcsolatos további információkért tekintse meg a cikk [az Azure AD Connect: vonatkozó különleges szempontok példányok](active-directory-aadconnect-instances.md).

* Az Initialize-ADSyncDomainJoinedComputerSync parancsmag most már rendelkezik AzureADDomain nevű új nem kötelező paraméter. Ez a paraméter lehetővé teszi, hogy adható meg, amely a szolgáltatáskapcsolati pont konfigurálásához használt tartomány ellenőrzése.

### <a name="pass-through-authentication"></a>Átmenő hitelesítés

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Az átmenő hitelesítés megváltozott a szükséges ügynököt neve *Microsoft Azure AD Application Proxy Connector* való *a Microsoft Azure AD Connect hitelesítési ügynökének*.

* Már nem az átmenő hitelesítés engedélyezése lehetővé teszi a Jelszókivonat-szinkronizálás alapértelmezés szerint.


## <a name="115530"></a>1.1.553.0
Állapot: 2017. június

> [!IMPORTANT]
> A build-ben bevezetett séma- és szinkronizálási szabály módosítások vannak. Az Azure AD Connect szinkronizálási szolgáltatás teljes importálást és teljes szinkronizálást lépéseket aktiválják a frissítés után. A változások részleteit az alábbiakban tekintheti át. A frissítés után ideiglenesen késlelteti a teljes importálást és teljes szinkronizálást lépéseket, tekintse meg a cikk [késlelteti a frissítés után teljes szinkronizálást hogyan](active-directory-aadconnect-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása

#### <a name="known-issue"></a>Ismert probléma
* Probléma, amely hatással van ügyfeleink [szervezeti egység szerinti szűrés](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) az Azure AD Connect-szinkronizálással. Kiválasztásakor az [tartomány és szervezeti egységek szűrése lap](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) az Azure AD Connect varázsló a következő viselkedés várható:
  * Ha a szervezeti egység szerinti szűrés engedélyezve van, a **tartományok és szervezeti egységek szinkronizálása** beállítást.
  * Ellenkező esetben a **minden tartomány és szervezeti egységek szinkronizálása** beállítást.

A probléma, amely akkor keletkezik, hogy a **minden tartomány és szervezeti egységek beállítás szinkronizálása** mindig ki van választva a varázsló futtatásakor.  Ez akkor fordul elő, akkor is, ha a szervezeti egység szerinti szűrés korábban beállított. Mielőtt mentené az AAD Connect konfigurációs változásokat, győződjön meg arról, a **szinkronizálja a kiválasztott tartományok és szervezeti egységek beállítás** , és győződjön meg arról, hogy újra kell szinkronizálni a hozzá tartozó szervezeti engedélyezett. Ellenkező esetben szervezeti egység szerinti szűrés letiltásra kerül.

#### <a name="fixed-issues"></a>Rögzített kapcsolatos problémák

* Kijavítva a jelszóvisszaírás, amely lehetővé teszi az Azure AD-rendszergazda, alaphelyzetbe állíthatja a jelszót a helyszíni AD az emelt szintű felhasználói fiókot. A probléma akkor fordul elő, amikor az Azure AD Connect a jelszó alaphelyzetbe állítása engedélyt kap a rendszerjogosultságú fiók keresztül. A problémával ebben a verzióban az Azure AD Connect szerint nem engedélyezi az Azure AD-rendszergazda, alaphelyzetbe állíthatja a jelszót egy tetszőleges helyszíni AD az emelt szintű felhasználói fiókot, ha a rendszergazda az adott fiók tulajdonosának. További információkért tekintse meg [biztonsági tanácsadó 4033453](https://technet.microsoft.com/library/security/4033453).

* Kapcsolatos probléma kijavítva a [msDS-ConsistencyGuid, mint a Forráshorgony](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) a szolgáltatás hol tárolódnak az Azure AD Connect a nem visszaírását a helyszíni AD-msDS-ConsistencyGuid attribútum. A probléma akkor fordul elő, ha több helyszíni AD-erdőhöz hozzá az Azure AD Connect és az *felhasználói identitások könyvtárak több lehetőség is léteznek* van kiválasztva. Az ilyen konfiguráció használata esetén a eredő szinkronizálási szabályok nem adja meg a sourceAnchorBinary attribútum tartozik a metaverzumba. A sourceAnchorBinary attribútum az msDS-ConsistencyGuid attribútum a forrásattribútum szolgál. Ennek eredményeképpen az ms-DSConsistencyGuid attribútum visszaírását nem történik meg. A probléma megoldásához, következő szinkronizálási szabályok frissültek, győződjön meg arról, hogy a rendszer mindig kitölti a sourceAnchorBinary attribútum tartozik a metaverzumba:
  * Az AD - InetOrgPerson AccountEnabled.xml-ből
  * Az AD - InetOrgPerson Common.xml-ből
  * Az AD - felhasználó AccountEnabled.xml-ből
  * Az AD - felhasználó Common.xml-ből
  * Az ad - felhasználó csatlakozhat SOAInAAD.xml

* Korábban akkor is, ha a [msDS-ConsistencyGuid, mint a Forráshorgony](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkció nem engedélyezett, a "Az AD-felhasználó ImmutableId Out" szinkronizálási szabály továbbra is adnak hozzá az Azure AD Connect. A hatás jóindulatú, és nem okoz a visszaírás megtörténjen msDS-ConsistencyGuid attribútum. A félreértések elkerülése végett logikai bővült győződjön meg arról, hogy a szinkronizálási szabály csak hozzáadni, ha a szolgáltatás engedélyezve van.

* Rögzített 611 událost chyby a Jelszókivonat-szinkronizálás sikertelen kiváltó problémát. Ez a probléma akkor fordul elő, amikor valamelyik vagy további domain controllers el lettek távolítva a helyszíni AD. Az egyes jelszó-szinkronizálási ciklust végén kiadott a szinkronizálási cookie-k által a helyszíni AD meghívási azonosítóit eltávolított tartományvezérlők USN (frissítési sorszám) értéke a 0 tartalmaz. A jelszó-szinkronizálás kezelője nem tudja megőrizni szinkronizálási cookie-t tartalmazó USN értéke 0, és meghiúsul, és 611 událost chyby. A következő szinkronizálási ciklus során a jelszó-szinkronizálás kezelője újrahasználja az utolsó megőrzött szinkronizálási cookie-t, amely nem tartalmaz 0 USN-értéket. Ez azt eredményezi, hogy ugyanazt a jelszómódosítást kell szinkronizálni. A javítás a jelszó-szinkronizálás kezelője továbbra is fennáll a szinkronizálási cookie-k megfelelően.

* Korábban még akkor is, ha az automatikus frissítés le lett tiltva a Set-ADSyncAutoUpgrade parancsmag használatával, az automatikus frissítési folyamat továbbra is fennáll, ellenőrizze a rendszeresen frissíteni, és a letöltött telepítőt, hogy tartsa tiszteletben a megfelelő támaszkodik. A javítás az automatikus frissítési folyamat már nem ellenőrzi a frissítés rendszeres időközönként. Amikor a frissítési telepítőjét az Azure AD Connect verziója mindig egyszer végrehajtja a rendszer automatikusan alkalmazza a javítást.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* Korábban a [msDS-ConsistencyGuid, mint a Forráshorgony](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkció csak az új telepítések számára elérhető volt. Eljött a meglévő telepítések számára elérhető. Pontosabban:
  * A funkció eléréséhez az Azure AD Connect varázsló elindításához, és válassza ki a *frissítés Forráshorgony* lehetőséget.
  * Ez a beállítás csak akkor látható, az objectGuid használja, a sourceAnchor attribútum telepítéseit.
  * Konfigurálja a beállítást, ha a varázsló ellenőrzi a helyszíni Active Directoryban az msDS-ConsistencyGuid attribútum állapotát. Ha az attribútum a címtárban lévő összes felhasználói objektum nincs konfigurálva, a varázsló használja az msDS-ConsistencyGuid Forráshorgony-attribútumként. Ha az attribútum egy vagy több felhasználói objektumok a címtárban van konfigurálva, a varázsló azt állapítja meg az attribútum más alkalmazások által használt, és nem felel meg a sourceAnchor attribútum, és nem teszi lehetővé a Forráshorgony módosítást, a folytatáshoz. Ha biztos benne, hogy az attribútum nem használja meglévő alkalmazásokat, meg kell le a hiba részleteiért forduljon az ügyfélszolgálathoz.

* Jellemző **userCertificate** eszközobjektumok, most már az Azure AD Connect attribútum úgy tűnik, a szükséges tanúsítványok értékek [tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure AD-hez Windows 10-es élmény](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) és a többi Azure ad szinkronizálása előtt kiszűri. Ahhoz, hogy ez a viselkedés, a beépített szinkronizálási szabály "Ki az aad-ben – eszköz csatlakozzon SOAInAD" frissítve lett.

* Az Azure AD Connect mostantól támogatja a jelszóvisszaíró az Exchange Online **cloudPublicDelegates** attribútumot a helyszíni AD **publicDelegates** attribútum. Ez lehetővé teszi a forgatókönyvet, ahol Exchange Online-postaládával is lehet SendOnBehalfTo jogokat kapnak a felhasználók számára a helyszíni Exchange-postaládájába. Ezzel a funkcióval egy új "Az ad-hez – a felhasználó Exchange hibrid PublicDelegates visszaírási Out" out-of-box szinkronizálási szabály támogatásához hozzá lett adva. A szinkronizálási szabály csak hozzá az Azure AD Connect, ha hibrid Exchange szolgáltatás engedélyezve van.

*   Szinkronizálás az Azure AD Connect mostantól támogatja a **altRecipient** az Azure AD-attribútumot. Ez a változás támogatása érdekében következő out-of-box szinkronizálási szabályokat tartalmazza a szükséges Attribútumfolyam frissítve lett-e:
  * Az AD-felhasználó Exchange-ből
  * Az aad-be – felhasználói ExchangeOnline ki
  
* A **cloudSOAExchMailbox** attribútum tartozik a metaverzumba azt jelzi, hogy egy adott felhasználó rendelkezik-e az Exchange Online-postaládával, vagy sem. A definíció tartalmazza a további Exchange Online RecipientDisplayTypes, az ilyen eszközöket és az konferenciaterem postaládák frissítve lett. Ahhoz, hogy ezt a módosítást, a definíció az cloudSOAExchMailbox attribútum, amelyet az out-of-box szinkronizálási szabály "A az aad-ben – felhasználó Exchange hibrid", a frissült:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... a a következőket:

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

* Adja hozzá a következő csoportját X509Certificate2-kompatibilis funkciók szinkronizálási szabály kifejezések kezelni a userCertificate attribútum értékei tanúsítvány létrehozásához:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|certThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Válassza ezt:|
    |CertKeyAlgorithmParams|CertHashString|Ahol (a(z)|
    |||A következővel:|

* Lehetővé teszi ügyfeleink számára hozzon létre egyéni szinkronizálási szabályok sAMAccountName domainNetBios és csoport objektumainak domainFQDN, valamint a felhasználói objektumok distinguishedName folyamat következő sémaváltozások vezettek be:

  * MV-séma következő attribútumok lettek hozzáadva:
    * Csoport: AccountName
    * Group: domainNetBios
    * Csoport: domainFQDN
    * Személy: distinguishedName

  * Az Azure AD-összekötő séma következő attribútumok lettek hozzáadva:
    * Group: OnPremisesSamAccountName
    * Csoport: NetBiosName
    * Csoport: tartománynév
    * Felhasználó: OnPremisesDistinguishedName

* A ADSyncDomainJoinedComputerSync parancsmag parancsfájl most már rendelkezik AzureEnvironment nevű új nem kötelező paraméter. A paraméter adja meg, melyik a megfelelő Azure Active Directory-bérlő üzemeltetett régióban használható. Érvényes értékek a következők:
  * AzureCloud (alapértelmezett)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Frissített szinkronizálási Szabályszerkesztő használandó (helyett üzembe helyezése) hivatkozás típusú alapértelmezett értékként a szinkronizálási szabály létrehozása során csatlakozzon.

### <a name="ad-fs-management"></a>AD FS-kezelőben

#### <a name="issues-fixed"></a>Megoldott problémák

* A következő URL-címek hitelesítési szolgáltatáskimaradás szembeni ellenálló-képesség javítása érdekében az Azure AD által bevezetett új WS-Federation végpontokat, és megjelenik a helyszíni AD FS függő entitások fél szintű bizalmi kapcsolatainak konfigurációja:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Rögzített IssuerID jogcím helytelen érték létrehozására az AD FS kiváltó problémát. A probléma akkor fordul elő, ha nincs több ellenőrzött tartomány az Azure AD-bérlőben és a tartományutótag a userPrincipalName attribútum IssuerID jogcím létrehozásához használt legalább 3 – szint mélyen (például johndoe@us.contoso.com). A probléma megoldásához a következő reguláris kifejezésre a jogcímszabályok által használt frissítése.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Korábban az AD FS-tanúsítványok kezelése az Azure AD Connect szolgáltatását csak használható az Azure AD Connect szolgáltatáson keresztül felügyelt AD FS-farmok. A funkció mostantól, használhatja az Azure AD Connect használatával nem kezelt AD FS-farmok.

## <a name="115240"></a>1.1.524.0
Kiadás dátuma: 2017. május

> [!IMPORTANT]
> A build-ben bevezetett séma- és szinkronizálási szabály módosítások vannak. Az Azure AD Connect szinkronizálási szolgáltatás teljes importálást és teljes szinkronizálást lépéseket aktiválják a frissítés után. A változások részleteit az alábbiakban tekintheti át.
>
>

**Rögzített problémák:**

Az Azure AD Connect szinkronizálása

* Kijavítva, amely hatására az automatikus frissítése az Azure AD Connect-kiszolgáló még akkor is, ha az ügyfél letiltotta a funkciót, a Set-ADSyncAutoUpgrade parancsmag használatával történik. Az automatikus frissítési folyamat a kiszolgálón a frissítés továbbra is rendszeres időközönként ellenőrzi a javítás, de a letöltött telepítőt az automatikus frissítési konfiguráció figyelembe veszi.
* A DirSync a helyben frissítés során az Azure AD Connect létrehoz egy Azure AD-szolgáltatásfiók és az Azure AD szinkronizálása az Azure AD-összekötő által használandó. A fiók létrehozása után az Azure AD Connect végzi a hitelesítést az Azure AD-fiókkal. Egyes esetekben hitelesítés nem sikerül átmeneti problémák miatt viszont emiatt DirSync helyszíni frissítését hiba miatt sikertelen *"hiba történt az AAD-Szinkronizáló beállítása a feladat végrehajtása: AADSTS50034: Jelentkezzen be az alkalmazást, hogy a fióknak kell lennie Adja hozzá a xxx.onmicrosoft.com könyvtár."* A DirSync frissítése rugalmasságát javítása érdekében az Azure AD Connect mostantól a hitelesítési lépés újbóli végrehajtása.
* Hiba történt a 443-as buildelési, amelynek hatására a DirSync helyi frissítés sikeres, de nem jöttek létre futtatási profilokat a címtár-szinkronizálás szükséges. Az Azure AD Connect a build logikai javítási tartalmazza. A build ügyfél rendszerre frissül, amikor az Azure AD Connect észleli a hiányzó futtatási profilok, és létrehozza őket.
* Kijavítva a jelszó-szinkronizálás folyamata nem indulnak el az Event ID 6900 és a hiba kiváltó *"ugyanazzal a kulccsal rendelkező elemet már hozzáadták"*. Ez a probléma akkor fordul elő, frissít OU konfigurációs szűrés AD konfigurációs partíció tartalmazza. A probléma megoldásához, jelszó-szinkronizálás folyamata most csak tartománypartíciókban AD jelszó-módosítások szinkronizálása. Például a konfigurációs partíción tartományon kívüli partíciókat a rendszer kihagyja.
* Expressz telepítés során az Azure AD Connect létrehoz egy helyi az AD-összekötővel folytatott kommunikációhoz használt AD DS-fiókot a helyszíni AD. Korábban a PASSWD_NOTREQD jelölővel állítsa be a felhasználó-fiók-ellenőrzési attribútum a fiók létrejön, és a véletlenszerű jelszót a fiók be van állítva. Most az Azure AD Connect explicit módon a PASSWD_NOTREQD jelző után eltávolítja a jelszót a fiók be van állítva.
* Kijavítva a hiba a DirSync frissítése sikertelen okozó *"holtpontot történt az sql server melyik egy alkalmazás zárolást"* mikor a mailNickname attribútum található a helyszíni AD-sémát, azonban nem korlátozza a az AD User objektum osztálya.
* Kijavítva, amely hatására az eszköz a jelszóvisszaíró szolgáltatás automatikusan le kell tiltani, ha a rendszergazda frissítése folyamatban van az Azure AD Connect szinkronizálási konfigurációjában, az Azure AD Connect varázsló használatával. A probléma okozza a varázsló egy előfeltételként-ellenőrzés elvégzése a meglévő eszközök a jelszóvisszaíró konfigurálása a helyszíni AD és az ellenőrzés sikertelen lesz. A javítás, hogy az ellenőrzés kihagyása, ha az eszközvisszaírás korábban már engedélyezve van.
* Konfigurálja a szervezeti egységek szűrése, vagy használhatja az Azure AD Connect varázsló vagy a Synchronization Service Managert. Korábban az Azure AD Connect varázsló használatával konfigurálja a szervezeti egységek szűrése, ha létrehozott szervezeti egységeket licencdíjait is a címtár-szinkronizálás. Ha nem szeretné, hogy bekerüljenek az új szervezeti egységeket, konfigurálnia kell szervezeti egységek szűrése a Synchronization Service Managert használja. Most érheti el az Azure AD Connect varázsló segítségével viselkedést.
* Kijavítva, amely hatására az Azure AD Connect kell létrehozni a séma telepítése a rendszergazda helyett a dbo sémában tárolt eljárásokat.
* Kijavítva a követési azonosító attribútum ki lehet hagyni a az AAD Connect kiszolgáló eseménynaplók, az Azure AD által visszaadott okozó hibát. A probléma akkor fordul elő, ha az Azure AD Connect egy átirányítási üzenet fogadása az Azure ad-ből, és az Azure AD Connect nem tud kapcsolódni a végponthoz megadott. A követési azonosítót használják a támogatási szakértők hibaelhárítás során az ügyféloldali naplói korrelációját.
* Ha az Azure AD Connect LargeObject hiba kap az Azure ad-ből, az Azure AD Connect eseményazonosító 6941, üzenet eseményt hoz létre *"a telepített objektum mérete túl nagy. Csökkentse az attribútum objektum értékeinek számát."* Egy időben, az Azure AD Connect is előállít egy eseményazonosító 6900, az üzenet félrevezető esemény *"Microsoft.Online.Coexistence.ProvisionRetryException: nem lehet kommunikálni a Windows Azure Active Directory szolgáltatás."* Zavarok minimalizálása érdekében az Azure AD Connect LargeObject hiba fogadásakor. utóbbi esetben már nem generál.
* Kijavítva, amelynek hatására a Synchronization Service Managert, hogy nem válaszol, az általános LDAP-összekötő konfigurációjának frissítése közben.

**Új funkciók és fejlesztések:**

Az Azure AD Connect szinkronizálása
* Szinkronizálási szabály módosítások – a következő szinkronizálási szabály változások történtek:
  * Frissített alapértelmezett szinkronizálási szabály beállítása nem exportálhatja az attribútumok **userCertificate** és **userSMIMECertificate** Ha az attribútumok több mint 15 értéket.
  * AD-attribútumok **employeeID** és **msExchBypassModerationLink** most már szerepel a szinkronizálási szabály alapértelmezés szerint.
  * AD-attribútum **fénykép** szinkronizálási szabály alapértelmezés szerint el lett távolítva.
  * Hozzáadott **preferredDataLocation** metaverzumséma és AAD-összekötő séma. Egyéni szinkronizálási szabályok ezt vagy attribútumok frissítése az Azure ad-ben kívánó ügyfelek valósítható meg. 
  * Hozzáadott **userType** metaverzumséma és AAD-összekötő séma. Egyéni szinkronizálási szabályok ezt vagy attribútumok frissítése az Azure ad-ben kívánó ügyfelek valósítható meg.

* Az Azure AD Connect most már automatikusan lehetővé teszi, hogy ConsistencyGuid attribútum használata a Forráshorgony-attribútumként a helyszíni AD-objektumok. További, az Azure AD Connect feltölti az objectGuid attribútum értéke ConsistencyGuid attribútum, akkor, ha üres. Ez a funkció akkor alkalmazható, csak az új telepítéshez. A szolgáltatásról további információért tekintse meg a cikk szakasz [az Azure AD Connect: tervezési alapelvei – az msDS-ConsistencyGuid használata sourceanchorként](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).
* A parancsmag új hibaelhárítási Invoke-ADSyncDiagnostics lett hozzáadva a Jelszókivonat-szinkronizálás diagnosztizálásához kapcsolatos problémákat. A parancsmag használatával kapcsolatos információkért tekintse meg a cikk [Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás hibaelhárítása](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).
* Most már támogatja a szinkronizálási postafiókhoz nyilvános mappa objektumait ki az Azure AD Connect a helyszíni AD az Azure ad-hez. A funkciót, az Azure AD Connect varázsló a választható funkciók használatával engedélyezheti. A szolgáltatásról további információért tekintse meg a cikk [Office 365 Directory-alapú biztonsági blokkolás támogatása a helyszíni levelezés engedélyezett nyilvános mappái](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Az Azure AD Connectnek szüksége van az AD DS-fiókot a szinkronizálás a helyszíni AD. Korábban Ha telepítette az Expressz mód használata az Azure AD Connect, biztosíthatja az Azure AD Connect és egy vállalati rendszergazdai fiók hitelesítő adatait az AD DS-fiókot hozna létre. Azonban az egyéni telepítés és erdők hozzáadása egy meglévő üzemelő példányt, kellett adja meg ehelyett az AD DS-fiókot. Most is arra, hogy adjon meg egy vállalati rendszergazdai fiók hitelesítő adatait az egyéni telepítés során, és lehetővé teszik az Azure AD Connect az AD DS-fiókot létrehozni.
* Az Azure AD Connect mostantól támogatja az SQL AOA. Az Azure AD Connect telepítése előtt engedélyeznie kell az SQL AOA. A telepítés során az Azure AD Connect észleli a megadott SQL-példány engedélyezve van-e az SQL AOA vagy sem. Ha SQL AOA engedélyezve van, az Azure AD Connect további kitalálja, hogy ha az SQL AOA szinkron replikáció vagy aszinkron replikáció használatára van konfigurálva. Állítson be a rendelkezésre állási csoport figyelőjét, javasoljuk, hogy a RegisterAllProvidersIP tulajdonságot 0 értékre állítja. Ez a javaslat az oka az Azure AD Connect jelenleg SQL Native Client összetevőt használja kapcsolódni az SQL és az SQL natív ügyfél nem támogatja a MultiSubNetFailover tulajdonság használatát.
* Ha az Azure AD Connect-kiszolgáló LocalDB használ az adatbázis, és elérte a 10 GB-os méretkorlátot, a szinkronizálási szolgáltatás nem indul el. Korábban a szeretne felszabadítani elegendő helyet a szinkronizálási szolgáltatás indítása a LocalDB ShrinkDatabase műveletet végrehajtani. Amely után a Synchronization Service Managert segítségével törölheti a futtatási előzmények több DB hely felszabadítása érdekében. Most segítségével Start-ADSyncPurgeRunHistory parancsmag futtatása kiürítése előzményadatok szolgáltatást a LocalDB adatbázisból DB lemezterület felszabadítása érdekében. További, a parancsmag támogatja-e egy offline üzemmód (megadásával az - offline paramétert) amely használható, ha a szinkronizálási szolgáltatás nem fut. Megjegyzés: A kapcsolat nélküli módban csak használható, ha a szinkronizálási szolgáltatás nem fut, és a használt adatbázis LocalDB.
* Csökkentheti a tárolóhely, az Azure AD Connect most tömöríti a szinkronizálási hiba részleteinek LocalDB/SQL-adatbázisok a tárolás előtt. Az Azure AD Connect egy régebbi verzióját erre a verzióra történő frissítés esetén az Azure AD Connect egy egyszeri tömörítés a meglévő szinkronizálási hiba részleteinek hajt végre.
* Korábban a után frissíti az OU-szűrés konfigurálása, manuálisan futtatnia kell meglévő objektumok meg megfelelően foglalt/zárva a címtár-szinkronizálás teljes importálást. Most, az Azure AD Connect automatikusan aktiválja a teljes importálás során a következő szinkronizálási ciklus. További, a teljes importálás csak az AD-összekötők, a frissítés hatással lehet vonatkozik. Megjegyzés: ennek a fejlesztésnek az OU-szűrés csak az Azure AD Connect varázsló használatával végzett frissítések alkalmazható. Ez nem alkalmazható az OU-szűrés a Synchronization Service Managert használatával végzett frissítés.
* Korábban a Csoportalapú szűrés támogatja a felhasználók, csoportok, és forduljon objektumok csak. Most Csoportalapú szűrés is támogatja számítógép-objektumokat.
* Korábban az Összekötőtérben adatok törölheti anélkül, hogy az Azure AD Connect-szinkronizálásütemező letiltása. A Synchronization Service Managert, blokkolja az Összekötőtérben adatok törlését, ha azt észleli, hogy engedélyezve van-e az ütemező. Egy figyelmeztetés, küld vissza tájékoztassa az ügyfeleket kapcsolatos esetleges adatvesztés, ha az összekötő terület adat törlődik.
* Korábban le kell tiltania az PowerShell beszédátírási megfelelő működéséhez az Azure AD Connect varázsló. A probléma részlegesen megoldották. Engedélyezheti a PowerShell beszédátírási használata az Azure AD Connect varázsló szinkronizálási konfiguráció kezeléséhez. PowerShell beszédátírási le kell tiltani, ha AD FS konfigurációjának kezelése az Azure AD Connect varázslót használja.



## <a name="114860"></a>1.1.486.0
Kiadás dátuma: 2017. április

**Rögzített problémák:**
* Javítva lett a probléma, ahol az Azure AD Connect nem telepíthető sikeresen Windows Server honosított változatát.

## <a name="114840"></a>1.1.484.0
Kiadás dátuma: 2017. április

**Ismert problémák:**

* Ez a verzió, az Azure AD Connect nem települ sikeresen, ha az alábbi feltételek mindegyike teljesül:
   1. Vagy a DirSync a helyben frissítés vagy friss telepítés az Azure AD Connect hajt végre.
   2. A Windows Server, ahol a beépített Rendszergazdák csoportjának a kiszolgáló nevét a "Rendszergazdák" nem honosított változatát használ.
   3. Az alapértelmezett SQL Server 2012 Express LocalDB helyett biztosítása a saját teljes funkciókészletű SQL az Azure AD connecttel telepített használ.

**Rögzített problémák:**

Az Azure AD Connect szinkronizálása
* Javítva lett egy probléma, ahol a szinkronizálásütemező kihagyja-e a teljes szinkronizálás lépés, ha egy vagy több összekötők szinkronizálása a lépés a futtatási profil hiányzik. Például manuálisan hozzáadott egy különbözeti importálást futtatási profil hozzá létrehozása nélkül a Synchronization Service Managert egy összekötő. Ez a javítás biztosítja, hogy a szinkronizálásütemező továbbra is fut a különbözeti importálás egyéb összekötőkhöz.
* Javítva lett egy probléma, amelyben a szinkronizálási szolgáltatás azonnal leállítja a feldolgozást egy futtatási profil van a egyik futtatási lépés hibát észlel. Ez a javítás biztosítja, hogy a szinkronizálási szolgáltatás kihagyja, amelyek futtatása lépést, és folytatja a többi. Ha például van egy különbözeti importálást futtatási profil az AD-összekötő a több Futtatás lépéseket (egy mindegyik helyszíni AD-tartomány). A szinkronizálási szolgáltatás különbözeti importálás működik az a többi AD-tartomány, akkor is, ha közülük egy hálózati problémák léptek fel.
* Kijavítva, amelynek hatására a rendszer eltekint automatikus frissítése az Azure AD-összekötő frissítése.
* Sikerült javítani a hibát, amely hatására az Azure AD Connect helytelenül megállapításához, hogy a kiszolgáló egy tartományvezérlő telepítése során, mely a kapcsolja hatására a DirSync frissítése sikertelen.
* Rögzített DirSync helyszíni frissítését nem minden futtatási profil létrehozása az Azure AD-összekötő okozó hibát.
* Javítva lett egy probléma, ahol a Synchronization Service Managert felhasználói felület nem válaszol az általános LDAP-összekötő konfigurálása közben.

AD FS-kezelőben
* Javítva lett egy probléma, ahol az Azure AD Connect varázsló sikertelen lesz, ha az elsődleges AD FS-csomópont áthelyezték egy másik kiszolgálóra.

Asztali egyszeri bejelentkezés
* Javítva lett egy probléma az Azure AD Connect varázslójában, a bejelentkezési képernyőn nem teszi lehetővé az asztali egyszeri bejelentkezés funkció engedélyezése, ha a jelszó-szinkronizálás, a bejelentkezési lehetőség új telepítése során.

**Új funkciók és fejlesztések:**

Az Azure AD Connect szinkronizálása
* Az Azure AD Connect szinkronizálási szolgáltatás mostantól támogatja a virtuális szolgáltatásfiókot, felügyelt szolgáltatásfiókok és csoportosan felügyelt szolgáltatásfiók a szolgáltatás-fiókot. Ez vonatkozik az Azure AD Connect csak új telepítést. Az Azure AD Connect telepítésekor:
    * Alapértelmezés szerint az Azure AD Connect varázsló létrehoz egy virtuális szolgáltatásfiókot, és használja a szolgáltatás-fiókot.
    * Ha tartományvezérlőre telepíti, az Azure AD Connect visszavált az előző viselkedés, ahol egy tartományi felhasználói fiókot hoz létre és; ezek helyett használja a szolgáltatás fiók.
    * Az alapértelmezett viselkedést felülírhatja azáltal, hogy az alábbi lehetőségek közül:
      * A csoportosan felügyelt szolgáltatásfiók
      * Felügyelt szolgáltatásfiók
      * Egy tartományi felhasználói fiók
      * Egy helyi felhasználói fiók
* Korábban Ha frissít, az Azure AD Connect tartalmazó új build összekötők frissítése, vagy szinkronizálási szabály változások, az Azure AD Connect aktivál egy teljes szinkronizálási ciklust. Most az Azure AD Connect szelektív aktiválása teljes importálást csak a frissítés összekötőt, és teljes szinkronizálást lépést csak az összekötő szinkronizálási szabály módosítása.
* Korábban a Exportálás törlési küszöb csak érvényes keresztül szinkronizálásütemező aktivált export. A szolgáltatás most terjeszteni van a Synchronization Service Managert az ügyfél által manuálisan aktivált exportálások is.
* Az Azure AD-bérlőhöz, a rendszer egy szolgáltatás konfigurációja, amely azt jelzi, hogy a jelszó-szinkronizálás funkció engedélyezve van-e a bérlő számára, vagy nem. Korábban könnyebbé vált a szolgáltatási konfiguráció nem megfelelően van konfigurálva az Azure AD Connect, ha az aktív és a egy átmeneti kiszolgálón. Most, az Azure AD Connect megkísérli, hogy a szolgáltatás konfigurációját az aktív adatok konzisztensek legyenek csak az Azure AD Connect-kiszolgáló.
* Az Azure AD Connect varázsló mostantól észleli, és figyelmeztetést ad vissza, ha a helyszíni AD nem tudja az AD Lomtár engedélyezve van.
* Ha az objektumok a Batch együttes mérete meghaladja a meghatározott küszöbérték korábban exportálása az Azure AD-időtúllépés történik, és meghiúsul. Most a szinkronizálási szolgáltatás újra megkísérli a újraküldeni az objektumok különböző, kisebb kötegekben, ha a probléma akkor fordul elő.
* A szinkronizálási szolgáltatás Key Management alkalmazás a Windows Start menü el lett távolítva. Titkosítási kulcs felügyeleti továbbra is a parancssori felület használatával miiskmu.exe keresztül. Titkosítási kulcs kezelésével kapcsolatos információkért tekintse meg a cikk [az Azure AD Connect szinkronizálási szolgáltatás titkosítási kulcs megszakítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Korábban az Azure AD Connect szinkronizálási szolgáltatásfiók jelszavának módosítása esetén a szinkronizálási szolgáltatás nem lesz képes kezdő megfelelően mindaddig, amíg a titkosítási kulcs hagyva, és az Azure AD Connect szinkronizálási szolgáltatásfiók jelszavának újrainicializálva. Most ez a folyamat már nem szükséges.

Asztali egyszeri bejelentkezés

* Az Azure AD Connect varázsló többé nem kell portot kell megnyitni a hálózaton, átmenő hitelesítés- és asztali egyszeri bejelentkezés beállításakor 9090. Csak a 443-as port megadása kötelező. 

## <a name="114430"></a>1.1.443.0
Kiadás dátuma: 2017. március

**Rögzített problémák:**

Az Azure AD Connect szinkronizálása
* Kijavítva egy probléma, amely hatására az Azure AD Connect varázsló a meghiúsuljon, ha az Azure AD-összekötő megjelenített neve nem tartalmaz az Azure AD-bérlőhöz rendelt kezdeti.onmicrosoft.com-tartománnyal.
* Javítva lett egy probléma, amely az Azure AD Connect varázsló sikertelen lesz, miközben az SQL database-kapcsolat létrehozása, amikor a szinkronizálási szolgáltatásfiók jelszavának például aposztróf, a kettőspont és a hely speciális karaktereket tartalmaz.
* Javítva lett egy probléma okozza a hibát, "a dimage rendelkezik, amely eltér a kép horgonyra", amely egy Azure AD Connect-kiszolgáló átmeneti módra, miután ideiglenesen kizárt egy helyszíni számítógépen AD szinkronizálása objektumra, és ezután történő szinkronizálás újra vettem fel.
* Javítva lett egy probléma okozza a hibát, "az objektum megkülönböztető neve szerint is egy látszólagos", amely egy Azure AD Connect-kiszolgáló átmeneti módra, miután ideiglenesen kizárt egy helyszíni számítógépen AD szinkronizálása objektumra, és ezután történő szinkronizálás újra vettem fel.

AD FS-kezelőben
* Javítva lett egy probléma, ahol az Azure AD Connect varázsló nem az AD FS konfigurációjának frissítése, és állítsa be a megfelelő jogcímeket a függő entitás megbízhatóságához a másodlagos bejelentkezési azonosító konfigurálása után.
* Javítva lett egy probléma, ahol az Azure AD Connect varázsló nem tudja megfelelően kezeli az AD FS-kiszolgálók, amelynek szolgáltatásfiókok vannak konfigurálva a sAMAccountName formátum helyett userPrincipalName formátum használatával.

Átmenő hitelesítés
* Kijavítva egy probléma, amely a meghiúsuljon, ha haladnak keresztül hitelesítés van beállítva, de az összekötő regisztrálása meghiúsul az Azure AD Connect varázsló okoz.
* Kijavítva egy probléma milyen okok Ha asztali egyszeri bejelentkezés szolgáltatás engedélyezve van a kijelölt bejelentkezési módszert ellenőrzésének kihagyását az Azure AD Connect varázslót.

Jelszó visszaállítása
* Kijavítva egy probléma miatt előfordulhat, hogy a Azure AAD Connect-kiszolgáló kísérelje meg ismét csatlakozni, ha a kapcsolat egy tűzfal vagy proxy lett állítva.

**Új funkciók és fejlesztések:**

Az Azure AD Connect szinkronizálása
* Get-ADSyncScheduler parancsmag mostantól egy új logikai tulajdonság SyncCycleInProgress nevű adja vissza. Ha a visszaadott érték igaz, az azt jelenti, hogy nincs folyamatban van egy ütemezett szinkronizálási ciklust.
* Az Azure AD Connect telepítési és telepítési naplók tárolására szolgáló célmappa áthelyezte a %localappdata%\AADConnect %programdata%\AADConnect a naplófájlokba akadálymentesítés érdekében.

AD FS-kezelőben
* Támogatás hozzáadva a az AD FS Farm SSL-tanúsítvány frissítése.
* Támogatás hozzáadva az AD FS 2016 felügyeletéhez.
* Mostantól megadhatja meglévő csoportosan felügyelt szolgáltatásfiókok (csoportosan felügyelt szolgáltatásfiók) az AD FS telepítése során.
* A aláírás-kivonatoló algoritmus az Azure AD függő entitás megbízhatóságaként, mostantól konfigurálhatja az SHA-256.

Jelszó visszaállítása
* Lehetővé teszi a funkció a termék szigorúbb tűzfalszabályokkal környezetekben fejlesztései bevezetésre.
* Továbbfejlesztett kapcsolat megbízhatóságát, az Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Kiadás dátuma: 2016. December

**Kijavítva a hiba:**

* Javítva lett a probléma, ahol a issuerid jogcím szabály az Active Directory összevonási szolgáltatások (AD FS), a build hiányzik.

>[!NOTE]
>A build az ügyfeleket az Azure AD Connect automatikus frissítés funkció nem érhető el.

## <a name="113710"></a>1.1.371.0
Kiadás dátuma: 2016. December

**Ismert hiba:**

* A issuerid jogcím szabály az AD FS-hez, a build hiányzik. A issuerid jogcím szabályt kötelező megadni, ha összevonja a több tartományt az Azure Active Directoryval (Azure AD). Ha az Azure AD Connect használatával a helyszíni AD FS üzembe helyezése, a build frissítését a meglévő issuerid jogcím szabály eltávolítja az AD FS konfigurációt. A probléma megkerüléséhez a issuerid jogcím szabály hozzáadása a telepítés vagy frissítés után. A részletek hozzáadásával a issuerid jogcím-szabály, tekintse meg a cikk a [több többtartományos támogatás az Azure ad-vel](active-directory-aadconnect-multiple-domains.md).

**Kijavítva a hiba:**

* Ha a kimenő kapcsolat nincs megnyitva Port 9090, az Azure AD Connect telepítése vagy frissítése sikertelen.

>[!NOTE]
>A build az ügyfeleket az Azure AD Connect automatikus frissítés funkció nem érhető el.

## <a name="113700"></a>1.1.370.0
Kiadás dátuma: 2016. December

**Ismert problémák:**

* A issuerid jogcím szabály az AD FS-hez, a build hiányzik. A issuerid jogcím szabályt kötelező megadni, ha összevonja a több tartomány és az Azure AD. Ha az Azure AD Connect használatával a helyszíni AD FS üzembe helyezése, a build frissítését a meglévő issuerid jogcím szabály eltávolítja az AD FS konfigurációt. A probléma megkerüléséhez a issuerid jogcím szabály hozzáadása a telepítés vagy frissítés után. A részletek hozzáadásával issuerid jogcím-szabály, tekintse meg a cikk a [több többtartományos támogatás az Azure ad-vel](active-directory-aadconnect-multiple-domains.md).
* Port 9090 kimenő a telepítés befejezéséhez meg kell nyitnia.

**Új funkciók:**

* Az átmenő hitelesítés (előzetes verzió).

>[!NOTE]
>A build az ügyfeleket az Azure AD Connect automatikus frissítés funkció nem érhető el.

## <a name="113430"></a>1.1.343.0
Kiadás dátuma: 2016. November

**Ismert hiba:**

* A issuerid jogcím szabály az AD FS-hez, a build hiányzik. A issuerid jogcím szabályt kötelező megadni, ha összevonja a több tartomány és az Azure AD. Ha az Azure AD Connect használatával a helyszíni AD FS üzembe helyezése, a build frissítését a meglévő issuerid jogcím szabály eltávolítja az AD FS konfigurációt. A probléma megkerüléséhez a issuerid jogcím szabály hozzáadása a telepítés vagy frissítés után. A részletek hozzáadásával issuerid jogcím-szabály, tekintse meg a cikk a [több többtartományos támogatás az Azure ad-vel](active-directory-aadconnect-multiple-domains.md).

**Rögzített problémák:**

* Egyes esetekben az Azure AD Connect telepítése meghiúsul, mert nem tudja hozzon létre egy helyi szolgáltatásfiókot, amelynek a jelszavát megfelel a jelszóházirend a szervezet által meghatározott összetettségi szintet.
* Javítva lett egy probléma, ahol join nem újraértékeli válásakor egy objektumot az összekötőtérben egyidejűleg Hatókörön kívüli egy szabály csatlakozzon, és a releváns válik egy másik. Ez akkor fordulhat elő, ha két vagy több illesztési szabály, amelyek illesztési feltételei, egymást kölcsönösen kizáró.
* Javítva lett egy probléma, ahol bejövő szinkronizálási szabályok (az Azure AD), amelyek illesztési szabályok nem tartalmaznak, nem kerülnek feldolgozásra Ha alacsonyabb prioritású értékeknél illesztési szabályokat tartalmazó rendelkeznek.

**Fejlesztések:**

* Támogatás hozzáadva a Windows Server 2016 Standard vagy annál újabb Azure AD Connect telepítését.
* Támogatás hozzáadva az SQL Server 2016, a távoli adatbázist az Azure AD Connect a.

## <a name="112810"></a>1.1.281.0
Kiadás dátuma: 2016. augusztus

**Rögzített problémák:**

* Módosítások szinkronizálása időköz nem kerül sor, amíg, a következő szinkronizálási ciklus befejeződése után.
* Az Azure AD Connect varázsló nem fogadja el az Azure AD-fiókot, akiknek a felhasználóneve kezdődik aláhúzásjellel (\_).
* Az Azure AD Connect varázsló nem tudja hitelesíteni az Azure AD-fiókot, ha a fiók jelszava túl sok speciális karaktereket tartalmaz. Chybová zpráva "nem sikerült érvényesíteni a hitelesítő adatokat. Váratlan hiba történt." adja vissza.
* Átmeneti kiszolgáló eltávolításával letiltja a jelszó-szinkronizálás az Azure AD-bérlőben, és a jelszó-szinkronizálás sikertelen lesz az active server okoz.
* Ritka esetekben a jelszó-szinkronizálás sikertelen lesz, amikor nem tárolja a felhasználó a Jelszókivonat.
* Ha az Azure AD Connect-kiszolgáló az átmeneti környezetű üzemmód engedélyezve van, a jelszóvisszaíró nincs letiltva ideiglenesen.
* Az Azure AD Connect varázsló nem jeleníti meg a tényleges jelszó-szinkronizálás és a jelszó a jelszóvisszaíró konfigurálása során a kiszolgáló átmeneti módban van. Őket minden esetben azt mutatja, mert le van tiltva.
* Ha a kiszolgáló átmeneti módban van a jelszó-szinkronizálás és jelszóvisszaíró szükséges konfigurációs módosítások nem rögzíti az Azure AD Connect varázsló.

**Fejlesztések:**

* A Start-ADSyncSyncCycle parancsmagot, amely jelzi, hogy sikeresen egy új szinkronizálási ciklus elindításához, vagy nem tud frissíteni.
* Leállítja a szinkronizálási ciklus és műveletet, amelyek jelenleg folyamatban van a Stop-ADSyncSyncCycle parancsmag hozzáadva.
* A Stop-ADSyncScheduler parancsmagot leállíthatja szinkronizálási ciklus és művelet, amely jelenleg folyamatban vannak frissítve.
* Konfigurálásakor [címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md) az Azure AD Connect varázslójában, az Azure AD-attribútum "Teletex string" típusú mostantól választható ki.

## <a name="111890"></a>1.1.189.0
Kiadás dátuma: 2016. június

**Rögzített problémák és fejlesztések:**

* Az Azure AD Connect mostantól a FIPS előírásainak megfelelő kiszolgálóra telepíthető.
  * Tekintse meg a jelszó-szinkronizáláshoz [jelszókivonatok szinkronizálása és a FIPS](active-directory-aadconnectsync-implement-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Javítva lett egy probléma, ahol egy NetBIOS-név feloldása nem sikerült, a Tartománynevet az Active Directory-összekötő a.

## <a name="111800"></a>1.1.180.0
Kiadás dátuma: 2016. május

**Új funkciók:**

* Figyelmezteti, és segít a tartományok ellenőrzését, ellenkező esetben azt az Azure AD Connect futtatása előtt.
* Támogatás hozzáadva az [Microsoft Cloud németországi adatközpontjában](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* A legújabb támogatása [Microsoft Azure Government felhőben](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) infrastruktúra az új URL-követelményeknek.

**Rögzített problémák és fejlesztések:**

* Hozzáadja a szinkronizálási Szabályszerkesztő, hogy könnyen megtalálhatja a szinkronizálási szabályok szűrése.
* Továbbfejlesztett teljesítmény összekötőtérben törlésekor.
* Javítva lett egy probléma, ha ugyanazt az objektumot volt is törli, majd hozzá ugyanazon futtatása (nevű törlése vagy hozzáadása).
* Letiltott szinkronizálási szabály már nem ismét engedélyezi a befoglalt objektumok és attribútumok a frissítési vagy könyvtár séma frissítése.

## <a name="111300"></a>1.1.130.0
Kiadás dátuma: 2016. április

**Új funkciók:**

* Többértékű attribútumok támogatása [címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md).
* A további konfigurációs eltéréseket támogatása [automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md) tekint, hogy jogosult a frissítésre.
* Az egyes parancsmagok hozzáadva [egyéni scheduler](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Kiadás dátuma: 2016. március

**Rögzített problémák:**

* Arról hogy Expressz telepítés nem használható a Windows Server 2008 (R2 előtti), mert a jelszó-szinkronizálás nem támogatott az operációs rendszer.
* Frissítés a Dirsyncről egyéni szűrő konfigurálása nem működött a várt módon.
* Egy újabb kiadásra történő frissítése során, és nem változtak a konfigurációnak, a teljes importálás vagy szinkronizálás nem sikerült ütemezni.

## <a name="111100"></a>1.1.110.0
Kiadás dátuma: 2016. február

**Rögzített problémák:**

* Frissítés a korábbi kiadásokban nem működik, ha a telepítés nem az alapértelmezett C:\Program Files mappában található.
* Ha telepít, és törölje a jelet **a szinkronizálási folyamat indítása** a telepítési varázsló végén a telepítési varázsló futtatása még egyszer sem teszik lehetővé az ütemező.
* Az ütemező nem működik a kiszolgálókon, ahol a hu-hu dátum/idő formátuma nem használatos a várt módon. Azzal is letiltja `Get-ADSyncScheduler` helyes időben vissza.
* Ha telepítette az Azure AD Connect egy korábbi kiadásokban az AD FS-sel a bejelentkezési lehetőséget és a frissítés, ismét a telepítési varázsló nem futtatható.

## <a name="111050"></a>1.1.105.0
Kiadás dátuma: 2016. február

**Új funkciók:**

* [Automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md) funkció Express beállítások ügyfelek számára.
* A globális rendszergazda az Azure multi-factor Authentication és a Privileged Identity Management segítségével a telepítővarázsló támogatása.
  * A proxy is a forgalom engedélyezésére engedélyeznie kell a https://secure.aadcdn.microsoftonline-p.com multi-factor Authentication használatakor.
  * Hozzá kell adnia https://secure.aadcdn.microsoftonline-p.com a megbízható helyek listájához, a multi-factor Authentication megfelelően működjön.
* Engedélyezi a felhasználó bejelentkezési mód módosítása a kezdeti telepítés után.
* Lehetővé teszi [tartomány és szervezeti egységek szűrése](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) a telepítési varázsló. Ez lehetővé teszi a hol érhetők el nem minden tartományok erdők csatlakozik.
* [A Scheduler](active-directory-aadconnectsync-feature-scheduler.md) jön létre a szinkronizálási motoron.

**A szolgáltatások előzetes verzióról a végleges verzió üzemivé léptet elő:**

* [Eszközvisszaírás](active-directory-aadconnect-feature-device-writeback.md).
* [Címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md).

**Új előzetes verziójú funkciók:**

* Az új alapértelmezett szinkronizálási ciklus időköz érték 30 perc. Három óra, az összes korábbi kiadásokban használható. Támogatást módosítása a [scheduler](active-directory-aadconnectsync-feature-scheduler.md) viselkedését.

**Rögzített problémák:**

* Győződjön meg arról DNS-tartományok oldalán mindig nem ismerte fel a tartományok.
* AD FS konfigurálása tartományi rendszergazdai hitelesítő adatokat kér.
* A helyszíni AD-fiókok nem ismeri fel a telepítővarázsló Ha egy másik DNS-fa, mint a gyökértartomány rendelkező tartományban található.

## <a name="1091310"></a>1.0.9131.0
Kiadás dátuma: 2015. December

**Rögzített problémák:**

* Jelszó-szinkronizálás nem működik, amikor módosítja az Active Directory Domain Services (AD DS), de működik a jelszavak jelszó beállításakor.
* Ha proxykiszolgálót, az Azure AD-hitelesítés sikertelen lehet-e a telepítés során, vagy ha a frissítés a lapon meg lett szakítva.
* Korábbi kiadásáról egy teljes SQL Server-példányt az Azure AD Connect frissítése nem sikerül, ha nem egy SQL Server-rendszergazda (SA).
* A "Nem lehet hozzáférni az ADSync SQL-adatbázis" hibát frissítése távoli SQL Server az Azure AD Connect korábbi kiadásáról jeleníti meg.

## <a name="1091250"></a>1.0.9125.0
Kiadás dátuma: 2015. November

**Új funkciók:**

* Újrakonfigurálhatja az AD FS-t az Azure AD-megbízhatóság.
* Az Active Directory-sémát frissítheti és szinkronizálási szabályok újragenerálása.
* Letilthatja a szinkronizálási szabály.
* A szinkronizálási szabály egy új literális "AuthoritativeNull" is definiálhatja.

**Új előzetes verziójú funkciók:**

* [Az Azure AD Connect Health szinkronizálási szolgáltatás](../connect-health/active-directory-aadconnect-health-sync.md).
* Támogatja a [Azure AD tartományi szolgáltatások](../user-help/active-directory-passwords-update-your-own-password.md) jelszó-szinkronizálás.

**Új támogatott forgatókönyv:**

* Támogatja a több helyszíni Exchange-szervezetnél. További információkért lásd: [hibrid telepítések esetén több Active Directory-erdők](https://technet.microsoft.com/library/jj873754.aspx).

**Rögzített problémák:**

* Jelszó-szinkronizálás problémák:
  * Hatókörön kívüli át az érintett objektum nem lesznek szinkronizálva a jelszava. Ez magában foglalja a szervezeti egység- és attribútumszűrés.
  * Szinkronban szükséges új szervezeti egység kiválasztása nem igényel teljes jelszó-szinkronizálást.
  * Ha egy letiltott felhasználó engedélyezve van a jelszó nem szinkronizálja.
  * A jelszó újrapróbálkozási várólista végtelen, és az előző legfeljebb 5000 objektumok kivezetés el lett távolítva.
* Nem tud csatlakozni az Active Directory-erdő működési szintje Windows Server 2016-ban.
* Nem sikerült a csoport, amely a kezdeti telepítés után csoportszűrést szolgál.
* Már nem hoz létre egy új felhasználói profilt minden felhasználó általi használatát a jelszó módosítása a jelszóvisszaírás engedélyezése az Azure AD Connect-kiszolgálón.
* Nem képes hosszú egész értékek szinkronban szabályok hatókörök.
* A jelölőnégyzet "eszközvisszaírás" Letiltva marad, ha a tartományvezérlő nem érhető el.

## <a name="1086670"></a>1.0.8667.0
Kiadás dátuma: 2015. augusztus

**Új funkciók:**

* Az Azure AD Connect telepítővarázsló most a minden Windows Server-nyelvekre honosított.
* Támogatás hozzáadva a fiók zárolásának feloldása, az Azure AD-jelszókezelés használatakor.

**Rögzített problémák:**

* Az Azure AD Connect telepítővarázsló összeomlik, ha egy másik felhasználó továbbra is fennáll, a személy, aki az első lépések a telepítés helyett a telepítést.
* Az Azure AD Connect egy előző eltávolítási eltávolíthatók az Azure AD Connect-szinkronizálás nem sikerül, ha azt, nem lehetséges újra kell telepítenie.
* Gyors telepítés használatával, ha a felhasználó nem szerepel az erdő gyökértartományában, vagy ha egy Active Directory nem angol nyelvű verzióját használja az Azure AD Connect nem tudja telepíteni.
* Ha nem tudja feloldani az Active Directory-fiók teljes Tartománynevét, egy félrevezető "A séma véglegesítése sikertelen" hibaüzenet jelenik meg.
* Ha az Active Directory-összekötő a használt fiók módosul a varázslót, a varázsló nem tudta utólagosan.
* Az Azure AD Connect telepítése tartományvezérlőn időnként sikertelen.
* Nem lehet engedélyezni, és tiltsa le a "Az átmeneti környezetű üzemmód", ha a bővítményattribútumok lettek hozzáadva.
* A jelszóvisszaíró bizonyos konfigurációk az Active Directory-összekötő rossz jelszó miatt nem sikerül.
* A DirSync nem frissíthető, ha az attribútumszűrés egy megkülönböztető név (DN) használatos.
* Túlzott CPU-használat jelszó-visszaállítás használata esetén.

**Eltávolított előzetes verziójú funkciók:**

* Az előzetes verziójú funkció [felhasználó-visszaírás](active-directory-aadconnect-feature-preview.md#user-writeback) ideiglenesen eltávolították az előzetes verzióval rendelkező ügyfelek visszajelzései alapján. Kerül újra később, miután a megadott visszajelzés vezettünk.

## <a name="1086410"></a>1.0.8641.0
Kiadás dátuma: 2015. június

**Az Azure AD Connect eredeti kiadása.**

Az Azure AD Connect az Azure AD-Szinkronizálóról módosított neve.

**Új funkciók:**

* [Gyorsbeállítások](active-directory-aadconnect-get-started-express.md) telepítése
* Is [AD FS konfigurálása](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* Is [frissítés a Dirsync szolgáltatásról](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Bevezetett [átmeneti módban](active-directory-aadconnectsync-operations.md#staging-mode)

**Új előzetes verziójú funkciók:**

* [Felhasználó-visszaírás](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Csoportvisszaírás](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Eszközvisszaírás](active-directory-aadconnect-feature-device-writeback.md)
* [Címtárbővítmények](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Kiadás dátuma: 2015. május

**Új követelményt:**

* Az Azure AD Sync mostantól csak a .NET-keretrendszer 4.5.1-es verziója kell telepíteni.

**Rögzített problémák:**

* A jelszóvisszaíró az Azure ad-ből az Azure Service Bus kapcsolódási hiba miatt sikertelen.

## <a name="104910413"></a>1.0.491.0413
Kiadás dátuma: 2015. április

**Rögzített problémák és fejlesztések:**

* Az Active Directory-összekötő nem dolgozza fel az törlések megfelelően, ha a Lomtár engedélyezve van, és több tartományt az erdőben vannak.
* Importálási műveletek teljesítményének úgy lett továbbfejlesztve, az Azure Active Directory-összekötő számára.
* Ha egy csoport túllépte a tagságot (alapértelmezés szerint a határ értéke 50 000 objektummal), a csoportot törölték az Azure Active Directoryban. Új működése a csoport nem törlődik, egy hiba lépett fel, és új tagsági módosítások nem lesznek exportálva.
* Nem lehet kiépíteni egy új objektumot, ha a ugyanazon DN-nel előkészített törlési már szerepel az összekötőtérben.
* Bizonyos objektumok szinkronizálásához a különbözeti szinkronizálás során lesznek megjelölve, annak ellenére, hogy az objektum átmenetileg nem változik.
* A jelszó-szinkronizálás kényszerítése eltávolítja az előnyben részesített tartományvezérlő lista is.
* CSExportAnalyzer kapcsolatban probléma merült fel az egyes objektumok államok.

**Új funkciók:**

* Egy összekapcsolás mostantól csatlakozhat "a bármely" objektumtípus a MV-ban.

## <a name="104850222"></a>1.0.485.0222
Kiadás dátuma: 2015. február

**Fejlesztések:**

* Továbbfejlesztett importálási teljesítmény.

**Rögzített problémák:**

* Jelszó-szinkronizálás az attribútumszűrés által használt cloudFiltered attribútum figyelembe veszi. Szűrt objektum már nem a jelszó-szinkronizálás hatókörében van.
* Ritka olyan esetekben, ahol a topológia volna a sok tartományvezérlőt, a jelszó-szinkronizálás nem működik.
* "Stopped-kiszolgáló" kezelés után az Azure AD-összekötő való importáláskor engedélyezve van az Azure AD és Intune-ban.
* Idegen rendszerbiztonsági tagok (FSP) abban az erdőben több tartományból való csatlakozás a nem egyértelmű illesztési hibát okoz.

## <a name="104751202"></a>1.0.475.1202
Kiadás dátuma: 2014. December

**Új funkciók:**

* Jelszó-szinkronizálás attribútum szerinti szűrés mostantól támogatott. További információkért lásd: [jelszó-szinkronizálás szűrése](active-directory-aadconnectsync-configure-filtering.md).
* Az msDS-ExternalDirectoryObjectID attribútum visszaírja az Active Directoryhoz. Ez a funkció az Office 365-alkalmazások támogatásával bővült. Az OAuth2 Exchange hibrid telepítés az Online és helyszíni postaládákhoz keresztül.

**Rögzített frissítési problémák:**

* A bejelentkezési segéd egy újabb verziója érhető el a kiszolgálón.
* Egy egyéni telepítési útvonalat az Azure AD Sync telepítéséhez használt.
* Egy érvénytelen egyéni illesztési feltétel blokkolja a frissítést.

**Egyéb javításai:**

* A sablonok Office Pro Plus rögzíteni.
* Rögzített telepítési problémákat, amelyeket egy kötőjel karakterekkel felhasználóneveket.
* Kijavítva a sourceAnchor beállítás elvesztése másodszor a telepítési varázsló futtatásakor.
* Rögzített ETW-nyomkövetés a jelszó-szinkronizálás.

## <a name="104701023"></a>1.0.470.1023
Kiadás dátuma: 2014. október

**Új funkciók:**

* A jelszó-szinkronizálás több helyszíni Active Directory, az Azure ad-hez.
* A honosított felhasználói felület telepítési minden Windows Server-nyelvekre.

**A végleges verzió AADSync 1.0-s verzióról**

Ha már rendelkezik Azure AD Sync telepítve van, nincs szükség van arra az esetre módosítva a beépített szinkronizálási szabály egy további lépést. A 1.0.470.1023 való frissítése után kiadási, a szinkronizálási szabályok módosította ismétlődik. Az egyes módosított szinkronizálási szabály tegye a következőket:

1.  Keresse meg a szinkronizálási szabály módosította, és jegyezze fel a módosításokat.
* A szinkronizálási szabály törlése.
* Keresse meg az új szinkronizálási szabály, amely az Azure AD Sync jön, és majd alkalmazza újra a módosításokat.

**Az Active Directory-engedélyek**

Az Active Directory fióknak el tudják olvasni a jelszókivonatokat az Active Directoryból való további engedélyeket kell biztosítani. Engedélyeket az elnevezett "Címtárváltozások replikálása" és "Replikálás Directory módosítja az összes." Mindkét engedélyek szükségesek a jelszókivonatokat olvashatja.

## <a name="104190911"></a>1.0.419.0911
Kiadás dátuma: 2014. szeptember

**Azure AD Sync eredeti kiadása.**

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
