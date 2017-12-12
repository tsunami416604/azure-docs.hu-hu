---
title: "Az Azure AD Connect: Fiókok és engedélyek |} Microsoft Docs"
description: "Ez a témakör ismerteti a használt, és létrehozott fiókok és a szükséges engedélyek."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: billmath
ms.openlocfilehash: cde406bd745fe61757eaa69c9fc0cfc98a42d205
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Az Azure AD Connect: Fiókok és engedélyek
Az Azure AD Connect telepítővarázsló biztosít két különböző elérési utak:

* A Gyorsbeállítások használatához a varázsló további jogosultságokra van szüksége.  Ez az, hogy azt állíthat be a konfigurációs könnyen, anélkül, hogy a felhasználók létrehozásához és-engedélyek konfigurálása.
* Az egyéni beállítások a varázsló biztosít további lehetőségek és beállítások. Vannak azonban olyan helyzetek, amelyben ellenőrizze, hogy megfelelő engedélyekkel a saját magának kell.

## <a name="related-documentation"></a>Kapcsolódó dokumentáció
Ha Ön nem olvasta be a dokumentáció a [a helyszíni identitások integrálása az Azure Active Directoryval](../active-directory-aadconnect.md), az alábbi táblázat kapcsolódó témakörökre mutató hivatkozásokat tartalmaz.

|Témakör |Hivatkozás|  
| --- | --- |
|Az Azure AD Connect letöltése | [Az Azure AD Connect letöltése](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Telepítés gyorsbeállítások használatával | [Az Azure AD Connect gyorstelepítése](./active-directory-aadconnect-get-started-express.md)|
|Telepítés testreszabott beállítások használatával | [Az Azure AD Connect testreszabott telepítése](./active-directory-aadconnect-get-started-custom.md)|
|Frissítés a DirSync szolgáltatásról | [Frissítés az Azure AD szinkronizáló eszközéről (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|A telepítést követően | [A telepítés ellenőrzése és licencek hozzárendelése](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>Expressz telepítés beállításai
Az expressz beállításokat a telepítővarázsló az Active Directory tartományi szolgáltatások vállalati rendszergazdai hitelesítő adatokat kér.  Ez a helyzet a helyszíni Active Directory konfigurálható az Azure AD Connect szükséges engedélyekkel. Ha a Dirsync szolgáltatásról frissít, az Active Directory tartományi szolgáltatások vállalati rendszergazdák hitelesítő adatok segítségével a DirSync által használt fiók jelszavának visszaállítása. Szükség az Azure AD globális rendszergazda hitelesítő adatait.

| Varázslólap | Hitelesítő adatok gyűjtése | Szükséges jogosultságok | A használt |
| --- | --- | --- | --- |
| N/A |A telepítővarázsló futtató felhasználó |A helyi kiszolgáló rendszergazdája |<li>A helyi fiók, amely létrehozza a [szinkronizálás a motor szolgáltatásfiók](#azure-ad-connect-sync-service-account). |
| Csatlakozás az Azure AD szolgáltatáshoz |Az Azure Active directory hitelesítő adatok |Az Azure AD globális rendszergazdai szerepkörrel |<li>Az Azure AD-címtár-szinkronizálás engedélyezése.</li>  <li>Létrehozását a [Azure AD-fiókot](#azure-ad-service-account) lévő szinkronizálási műveletek használt Azure AD-ben.</li> |
| Csatlakozás az AD DS szolgáltatáshoz |A helyszíni Active Directory hitelesítő adatok |A vállalati rendszergazdák (EA) csoport az Active Directoryban |<li>Létrehoz egy [fiók](#active-directory-account) az Active Directory és az engedélyt. Ez a fiók létrehozása írási és olvasási címtáradatok szinkronizálás során használatos.</li> |

### <a name="enterprise-admin-credentials"></a>Vállalati rendszergazdai hitelesítő adatokat
Ezek a hitelesítő adatok csak a telepítés során használt, és ne lehessen felhasználni a telepítés befejezése után. A vállalati rendszergazdai, nem a tartományi rendszergazda győződjön meg arról az engedélyeket az Active Directory minden tartományban állítható be.

### <a name="global-admin-credentials"></a>Globális rendszergazda hitelesítő adataival
Ezek a hitelesítő adatok csak a telepítés során használt, és ne lehessen felhasználni a telepítés befejezése után. Létrehozására szolgál a [Azure AD-fiókot](#azure-ad-service-account) használja az Azure AD-módosítások szinkronizálása. A fiók is lehetővé teszi, hogy szinkronizálás szolgáltatásként az Azure ad-ben.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>A létrehozott Active Directory tartományi fiók számára gyorsbeállítások
A [fiók](#active-directory-account) létrehozott olvasását és írását az AD DS-ben gyorsbeállítások létrehozásakor a következő engedélyekkel rendelkezik:

| Engedély | A használt |
| --- | --- |
| <li>Változások replikálása</li><li>Replikálása Directory összes módosítása |A jelszó-szinkronizálás |
| Olvasási/írási összes tulajdonság felhasználó |Importálás és az Exchange hibrid |
| Olvasási/írási összes tulajdonságok iNetOrgPerson |Importálás és az Exchange hibrid |
| Az összes tulajdonság csoport olvasási/írási |Importálás és az Exchange hibrid |
| Az összes tulajdonság forduljon olvasási/írási |Importálás és az Exchange hibrid |
| Új jelszó létrehozása |Felkészülés a jelszóvisszaírás engedélyezése |

## <a name="custom-settings-installation"></a>Egyéni beállítások telepítése
Az Azure AD Connect 1.1.524.0 verziót, és később a lehetőséget, hogy az Active Directory való csatlakozáshoz használt fiók létrehozása az Azure AD Connect varázsló.  A korábbi szükséges, hogy a fiók létrejött-e a telepítés előtt. Ezt a fiókot kell megadni az engedélyeket található [az AD DS-fiók létrehozása](#create-the-ad-ds-account). 

| Varázslólap | Hitelesítő adatok gyűjtése | Szükséges jogosultságok | A használt |
| --- | --- | --- | --- |
| N/A |A telepítővarázsló futtató felhasználó |<li>A helyi kiszolgáló rendszergazdája</li><li>A teljes SQL Server használata esetén a felhasználónak kell-e rendszergazdai (SA) SQL-ben</li> |Alapértelmezés szerint hozza létre a helyi fiók, amely a [szinkronizálás a motor szolgáltatásfiók](#azure-ad-connect-sync-service-account). A fiók csak akkor jönnek létre, amikor a rendszergazda nem adja meg egy különös figyelmet. |
| Szinkronizálási szolgáltatások, a szolgáltatás fiók lehetőséget telepítése |AD vagy a helyi felhasználói fiók hitelesítő adatait |Felhasználó, engedélyekkel a telepítő varázsló |Ha a rendszergazda ad meg egy fiókot, ez a fiók használatos szolgáltatásfiókként a szinkronizálási szolgáltatás. |
| Csatlakozás az Azure AD szolgáltatáshoz |Az Azure Active directory hitelesítő adatok |Az Azure AD globális rendszergazdai szerepkörrel |<li>Az Azure AD-címtár-szinkronizálás engedélyezése.</li>  <li>Létrehozását a [Azure AD-fiókot](#azure-ad-service-account) lévő szinkronizálási műveletek használt Azure AD-ben.</li> |
| Csatlakoztassa a címtárakat |A helyi Active Directorybeli hitelesítő adatokat az egyes erdőkhöz az Azure AD-csatlakozó |Az engedélyek a fejlesztendő funkciók engedélyezéséhez, és itt található: függő [az AD DS-fiók létrehozása](#create-the-ad-ds-account) |Ennek a fióknak írási és olvasási címtáradatok szinkronizálás során használatos. |
| AD FS-kiszolgálók |Minden kiszolgálóhoz, a listában a a varázsló hitelesítő adatait gyűjti, amikor a felhasználó, a varázsló futtatása a bejelentkezési hitelesítő adatok nem elegendőek csatlakozás |Tartományi rendszergazda |Telepítés és konfigurálás a az AD FS kiszolgálói szerepkör. |
| Webalkalmazás-proxy kiszolgálók |Minden kiszolgálóhoz, a listában a a varázsló hitelesítő adatait gyűjti, amikor a felhasználó, a varázsló futtatása a bejelentkezési hitelesítő adatok nem elegendőek csatlakozás |A célszámítógépen helyi rendszergazdai |Telepítés és konfigurálás a WAP-kiszolgálói szerepkör. |
| Proxy megbízhatósági hitelesítő adatai |Összevonási szolgáltatás bizalmi kapcsolat hitelesítő adatait (a hitelesítő adatokat a proxy regisztrálása a FS megbízható tanúsítványt használ |Tartományi fiókot, amely az AD FS-kiszolgáló helyi rendszergazdája |A regisztráció FS-WAP megbízható tanúsítvány. |
| AD FS szolgáltatásfiókjának, "Tartományi felhasználói fiók lehetőség használata" |AD-felhasználói fiókjának hitelesítő adatait |Tartományi felhasználó |Az AD felhasználói fiók, amelynek hitelesítő adatok megadása az AD FS szolgáltatás bejelentkezési fiókként szolgál. |

### <a name="create-the-ad-ds-account"></a>Az AD DS-fiók létrehozása
A fiók, meg kell adnia a **csatlakoztassa a címtárakat** lap szerepelnie kell az Active Directory telepítése előtt.  A megadott szükséges engedélyeket is kell rendelkeznie. A telepítővarázsló nem ellenőrzi az engedélyeket és probléma merül fel csak találhatók meg a szinkronizálás során.

Engedélyezi szükséges jogosultságokat az függ a választható szolgáltatásokat. Ha több tartományban vannak, az engedélyeket kell adni az erdőben lévő összes tartományban. Ha nem engedélyezi ezeket a szolgáltatásokat, az alapértelmezett **tartományi felhasználó** elegendő.

| Szolgáltatás | Engedélyek |
| --- | --- |
| Az msDS-ConsistencyGuid szolgáltatás |Az msDS-ConsistencyGuid attribútum részletes ismertetését lásd: e írási engedélyekkel [tervezési alapelvek - msDS-ConsistencyGuid használata sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). | 
| A jelszó-szinkronizálás |<li>Változások replikálása</li>  <li>Replikálása Directory összes módosítása |
| Hibrid Exchange-telepítés |Írási engedéllyel a dokumentált attribútumok [Exchange hibrid visszaírási](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) felhasználók, csoportok és ügyfelek. |
| Exchange E-mail nyilvános mappa |Olvasási engedéllyel a dokumentált attribútumok [Exchange E-mail nyilvános mappa](active-directory-aadconnectsync-attributes-synchronized.md#exchange-mail-public-folder) nyilvános mappák. | 
| Jelszóvisszaíró |Írási engedéllyel a dokumentált attribútumok [Ismerkedés a jelszókezeléssel](../active-directory-passwords-writeback.md) a felhasználók számára. |
| Eszközvisszaíró |A PowerShell-parancsfájllal megadott engedélyeket [eszközvisszaíró](active-directory-aadconnect-feature-device-writeback.md). |
| Group writeback (Csoportvisszaíró) |Olvassa el, létrehozási, frissítési és törlési objektumokat csoport a szinkronizált **Office 365-csoportok**.  További információ: [Csoportvisszaírásról](active-directory-aadconnect-feature-preview.md#group-writeback).|

## <a name="upgrade"></a>Frissítés
Az Azure AD Connect egyik verzióról történő frissítésekor egy új kiadását, a következő engedélyekre van szükség:

>[!IMPORTANT]
>Build 1.1.484 verziótól kezdődően az Azure AD Connect bevezetett egy regressziós hiba, amely az SQL-adatbázis frissítéséhez rendszergazdai engedélyekkel kell rendelkeznie.  Ez a hiba továbbra is megtalálható a legújabb buildjével 1.1.614.  Ha frissíti a buildre, szüksége lesz a rendszergazdai engedélyekkel.  Dbo engedélyek nem elegendőek.  Ha az Azure AD Connect anélkül, hogy a rendszergazdai engedélyek frissítésére tett kísérlet, a frissítés sikertelen lesz, és az Azure AD Connect már nem működik megfelelően, ezt követően.  Microsoft erről tájékoztatni kell, és ennek működik.


| Rendszerbiztonsági tag | Szükséges jogosultságok | A használt |
| --- | --- | --- |
| A telepítővarázsló futtató felhasználó |A helyi kiszolgáló rendszergazdája |A frissítés bináris fájljait. |
| A telepítővarázsló futtató felhasználó |ADSyncAdmins tagja |Szinkronizálási szabályok és más konfigurációs módosításokat. |
| A telepítővarázsló futtató felhasználó |A teljes SQL server használata esetén: DBO (vagy hasonlót), a Szinkronizáló vezérlő adatbázisának |Adatbázis szintű módosításokat végezni, például táblák frissítése új oszlopokkal. |

## <a name="more-about-the-created-accounts"></a>További információ a létrehozott fiókok
### <a name="active-directory-account"></a>Active Directory-fiók
Ha az expressz beállításokat használja, majd egy fiókot az Active Directoryban, amely a szinkronizáláshoz használt jön létre. A létrehozott fiók az erdő gyökértartományának a felhasználók a tárolóban található, és rendelkezik a név előtagként **MSOL_**. A fiók létrejön egy hosszú összetett jelszót, amely nem jár le. Ha a jelszóházirend abban a tartományban, győződjön meg arról, hogy hosszú, és bonyolultabb jelszavakat szeretné engedélyezni az ehhez a fiókhoz.

![AD-fiókot](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

Ha egyéni beállításokat használja, majd telepítésért felelős a fiók létrehozásához, a telepítés megkezdése előtt.

### <a name="azure-ad-connect-sync-service-account"></a>Az Azure AD Connect szinkronizáláshoz használt szolgáltatásfióknak
A szinkronizálási szolgáltatás eltérő fiókkal is futhat. Csak akkor futtathatók a **virtuális szolgáltatásfiók** (Szállítóspecifikus), egy **csoportosan felügyelt szolgáltatásfiók** (csoportosan felügyelt szolgáltatásfiókot vagy önállóan felügyelt szolgáltatásfiókot), vagy egy felhasználói fiókot. A támogatott beállítások módosított rendelkező a 2017. április kiadása csatlakozás, ha így tesz, friss telepítését. Ha az Azure AD Connect egy korábbi kiadásáról frissít, a további beállítások nem érhetők el.

| Fiók típusa | Telepítési lehetőség | Leírás |
| --- | --- | --- |
| [Virtuális fiók](#virtual-service-account) | Az expressz és egyéni, 2017. április és újabb verziók | Ez a beállítás az összes Expressz telepítés, kivéve a tartományvezérlővé való telepítéséhez használt. Az egyéni akkor az alapértelmezett beállítás kivéve, ha egy másik kapcsolót. |
| [Csoportosan felügyelt szolgáltatásfiók](#group-managed-service-account) | Egyéni, 2017. április és újabb verziók | Ha távoli SQL-kiszolgálót használ, majd javasoljuk a csoportosan felügyelt szolgáltatásfiók használatához. |
| [Felhasználói fiók](#user-account) | Az expressz és egyéni, 2017. április és újabb verziók | Egy felhasználói fiókot AAD_ előtagként csak telepített Windows Server 2008 és a tartományvezérlőre van telepítve, a telepítés során jön létre. |
| [Felhasználói fiók](#user-account) | Az expressz és egyéni, 2017. március és korábbi | AAD_ előtagként helyi fiók a telepítés során jön létre. Egyéni telepítés használata esetén egy másik fiókot is megadható. |

Ha a 2017 build március használ Connect vagy korábbi, majd nem, állítsa vissza a szolgáltatásfiók jelszavát, mert a Windows megsemmisít a titkosítási kulcsok biztonsági okokból. A fiók nem módosítható olyan más fiók az Azure AD Connect újratelepítése nélkül. Ha frissít egy elkészítési 2017. április, vagy később, akkor az támogatott a fiók, de a jelszó nem módosíthatja a használt fiók.

> [!Important]
> Beállíthatja a szolgáltatásfiók csak az első telepítést. A telepítés befejezése után a szolgáltatásfiók váltása nem támogatott.

Ez a táblázat az alapértelmezett ajánlott és támogatott beállítások esetében a szinkronizáláshoz használt szolgáltatásfióknak a.

Jelmagyarázat:

- **A félkövér betűvel írott** azt jelzi, az alapértelmezett beállítás, és a legtöbb esetben az ajánlott.
- *Dőlt* azt jelzi, az ajánlott, ha nem az alapértelmezett beállítás.
- 2008 - alapértelmezett beállítás a Windows Server 2008 rendszeren
- A nem félkövér - támogatott beállítás
- Helyi fiók – helyi felhasználói fiók a kiszolgálón
- Tartományi fiók - tartományi felhasználói fiók
- önállóan felügyelt szolgáltatásfiókot - [önálló felügyelt szolgáltatásfiók](https://technet.microsoft.com/library/dd548356.aspx)
- csoportosan felügyelt szolgáltatásfiók - [csoportos felügyelt szolgáltatásfiók](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Egyéni | Távoli SQL</br>Egyéni |
| --- | --- | --- | --- |
| **önálló vagy munkacsoporthoz tartozó számítógépeken** | Nem támogatott | **SZÁLLÍTÓSPECIFIKUS**</br>Helyi fiók (2008)</br>Helyi fiók |  Nem támogatott |
| **a tartományhoz gép** | **SZÁLLÍTÓSPECIFIKUS**</br>Helyi fiók (2008) | **SZÁLLÍTÓSPECIFIKUS**</br>Helyi fiók (2008)</br>Helyi fiók</br>Tartományi fiók</br>önállóan felügyelt szolgáltatásfiókot, csoportosan felügyelt szolgáltatásfiók | **csoportosan felügyelt szolgáltatásfiók**</br>Tartományi fiók |
| **Tartományvezérlő** | **Tartományi fiók** | *csoportosan felügyelt szolgáltatásfiók*</br>**Tartományi fiók**</br>önállóan felügyelt szolgáltatásfiókot| *csoportosan felügyelt szolgáltatásfiók*</br>**Tartományi fiók**|

#### <a name="virtual-service-account"></a>Virtuális fiók
Virtuális szolgáltatásfiók egy olyan fiókkal, amely egy jelszó és a Windows által felügyelt különleges típusú.

![SZÁLLÍTÓSPECIFIKUS](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

A Szállítóspecifikus kívánják használható forgatókönyvek ugyanazon a kiszolgálón a szinkronizálási motor és az SQL esetén. Ha távoli SQL, akkor használja a [csoportosan felügyelt szolgáltatásfiók](#managed-service-account) helyette.

Ez a funkció használatához Windows Server 2008 R2 vagy újabb. Ha az Azure AD Connect telepítése Windows Server 2008, majd a telepítés visszaáll használatára egy [felhasználói fiók](#user-account) helyette.

#### <a name="group-managed-service-account"></a>Csoportosan felügyelt szolgáltatásfiók
Ha távoli SQL-kiszolgálón, akkor használatát javasoljuk a **csoport felügyelt szolgáltatásfiók**. Hogyan készíti elő az Active Directory-csoport által felügyelt szolgáltatásfiókhoz további információkért lásd: [csoportosan felügyelt szolgáltatásfiókok áttekintése](https://technet.microsoft.com/library/hh831782.aspx).

Ezt a beállítást, a használandó a [szükséges összetevők telepítése](active-directory-aadconnect-get-started-custom.md#install-required-components) lapon jelölje be **meglévő szolgáltatásfiók használata**, és válassza ki **felügyelt szolgáltatásfiók**.  
![SZÁLLÍTÓSPECIFIKUS](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  
Használata is támogatott egy [önálló felügyelt szolgáltatásfiók](https://technet.microsoft.com/library/dd548356.aspx). Azonban ezek csak használhatók a helyi számítógépen, és nincs előnye is használhatja őket az alapértelmezett virtuális szolgáltatásfiók alatt.

A funkció használatához Windows Server 2012 vagy újabb. Ha egy régebbi operációs rendszert használ, és használjon távoli SQL, akkor kell használni egy [felhasználói fiók](#user-account).

#### <a name="user-account"></a>Felhasználói fiók
A helyi szolgáltatás fiók akkor hozza létre a telepítési varázsló (kivéve, ha meg kell adnia a fiókot használja az egyéni beállítások). A fiók nem előzi **AAD_** és a tényleges szinkronizálási szolgáltatáshoz használt fiókként szándékozik futtatni. Ha az Azure AD Connect tartományvezérlőre telepíti, a fiók a tartományban jön létre. A **AAD_** szolgáltatás fiókja a tartományban kell lennie, ha:
   - SQL server rendszert futtató távoli kiszolgáló használata
   - egy hitelesítést igénylő proxyt használ

![Szolgáltatásfiók](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

A fiók létrejön egy hosszú összetett jelszót, amely nem jár le.

Ez a fiók biztonságos módon a más fiókoknak a jelszavak tárolására szolgál. Ezek más fiókokat a jelszavak tárolja az adatbázisban titkosított formában. A titkosítási szolgáltatások titkos kulcsú titkosítás Windows Data Protection API (DPAPI) segítségével védi a titkosítás a titkos kulcsok.

Ha a teljes SQL-kiszolgálót használ, a szolgáltatásfiók nem a szinkronizálási motor létrehozott adatbázis DBO. A szolgáltatás nem fognak működni, mint bármely más engedélyekkel. Egy SQL-bejelentkezési is létrejön.

A fiók is engedéllyel rendelkezik a fájlokat, beállításkulcsokat és más objektumok, a szinkronizálási motor kapcsolódik.

### <a name="azure-ad-service-account"></a>Azure AD-szolgáltatásfiók
A szinkronizálási szolgáltatás használata az Azure AD-fiók jön létre. Ez a fiók a megjelenített név alapján azonosítható.

![AD-fiókot](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

A második rész a felhasználónév azonosíthatók a a fiókot használja a kiszolgáló nevét. A képen látható a kiszolgáló neve, FABRIKAMCON. Ha még kiszolgáló átmeneti, minden kiszolgáló rendelkezik saját fiók.

A fiók létrejön egy hosszú összetett jelszót, amely nem jár le. Egy különös szerepet kap **szinkronizálási Címtárfiókjainak** , amely jogosult csak címtár-szinkronizálási feladatok elvégzéséhez. Ez a különleges beépített szerepkör nem adható meg az Azure AD Connect varázsló kívül. Az Azure-portálon jeleníti meg ezt a fiókot a szerepkör **felhasználói**.

Nincs maximális hossza 20 szinkronizálási szolgáltatás fiókok Azure AD-ben. A meglévő Azure AD-szolgáltatási fiókok listájának lekérdezése az Azure AD-ben, futtassa a következő Azure AD PowerShell-parancsmagot:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Eltávolítja a nem használt Azure AD szolgáltatás fiókjai, futtassa a következő Azure AD PowerShell-parancsmagot:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](../active-directory-aadconnect.md).
