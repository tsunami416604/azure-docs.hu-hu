---
title: 'Azure AD Connect: Fiókok és engedélyek |} A Microsoft Docs'
description: Ez a témakör ismerteti a használt és a létrehozott fiókok és a szükséges engedélyekkel.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: b05f10068cb747f19266683779352f21ec01bff9
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491688"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Fiókok és engedélyek

## <a name="accounts-used-for-azure-ad-connect"></a>Az Azure AD Connect használt fiókok

![](media/reference-connect-accounts-permissions/account5.png)

Az Azure AD Connect 3 fiókokat használ, annak érdekében, hogy az Azure Active Directory a helyszíni vagy a Windows Server Active Directory információk szinkronizálásához.  Ezek a fiókok a következők:

- **Az AD DS-összekötő fiók**: olvasási/írási információkat a Windows Server Active Directory segítségével

- **ADSync szolgáltatás fiókja**: a szinkronizálási szolgáltatás futtatásához és az SQL-adatbázis eléréséhez használt

- **Az Azure AD-összekötő fiók**: az Azure AD információkat írásához használt

E három fiókok Azure AD Connect futtatásához használt mellett is szüksége lesz a következő további fiókokat az Azure AD Connect telepítése.  Ezek a következők:

- **Helyi rendszergazdai fiók**: A rendszergazda, aki az Azure AD Connectet telepíti, és kik a helyi rendszergazdai engedélyekkel a gépen.

- **Az AD DS vállalati rendszergazdai fiók**: Igény szerint használja az "AD DS-összekötő fiók" létrehozása a fent.

- **Az Azure AD globális rendszergazdai fiókját**: az Azure AD-összekötő fiók létrehozása és konfigurálása az Azure ad-ben.

- **SQL-rendszergazdai fiók (nem kötelező)**: SQL Server teljes verziójának használatakor az ADSync-adatbázis létrehozásához használt.  Az SQL-kiszolgáló helyi vagy távoli, az Azure AD Connect telepítés lehet.  Ez a fiók lehet ugyanazt a fiókot, a vállalati rendszergazda engedélyezte.  Most kiépítése az adatbázis is lehet az SQL-rendszergazda által sávon kívül végrehajtott és csak utána települ az Azure AD Connect rendszergazdája az adatbázis-tulajdonosi jogosultságok.  Kapcsolatos információkért lásd a [SQL meghatalmazott rendszergazdai engedélyek használatával telepítse az Azure AD Connect](how-to-connect-install-sql-delegation.md)

## <a name="installing-azure-ad-connect"></a>Azure AD Connect telepítése
Az Azure AD Connect telepítővarázsló két különböző elérési kínálja:

* A Gyorsbeállítások használatához a varázsló további jogosultságokra van szüksége.  Ez azért szükséges, állíthat be a konfigurációt, anélkül, hogy a felhasználók létrehozása vagy -engedélyek konfigurálása.
* Az egyéni beállítások a varázsló kínál megoldást a további lehetőségek és beállítások. Vannak azonban olyan helyzetek, akkor ellenőrizze, hogy megfelelő engedélyekkel a saját magának kell.



## <a name="express-settings-installation"></a>Expressz telepítés beállításai
Az Express-beállítások a telepítési varázsló kéri, a következőket:

  - Az AD DS vállalati rendszergazda hitelesítő adatai
  - Az Azure AD globális rendszergazdai hitelesítő adatokkal

### <a name="ad-ds-enterprise-admin-credentials"></a>Az AD DS vállalati rendszergazdai hitelesítő adatokat
Az AD DS vállalati rendszergazdai fiók a helyi Active Directory konfigurálására szolgál. Ezek a hitelesítő adatok csak a telepítés során használt, és nem használja a telepítés befejezése után. A vállalati rendszergazdának, nem a tartományi rendszergazda gondoskodnia kell az engedélyeket az Active Directory minden tartományban lehet megadni.

Ha a Dirsync szolgáltatásról frissít, az AD DS vállalati rendszergazdák hitelesítő adatok segítségével alaphelyzetbe állíthatja a jelszót a DirSync által használt fiók. Emellett az Azure AD globális rendszergazdai hitelesítő adatokkal.

### <a name="azure-ad-global-admin-credentials"></a>Az Azure AD globális rendszergazdai hitelesítő adatait
Ezek a hitelesítő adatok csak a telepítés során használt, és nem használja a telepítés befejezése után. Hozzon létre szolgál a [Azure AD-összekötő fiók](#azure-ad-service-account) változásokat az Azure ad-ben használt. A fiók is lehetővé teszi a szinkronizálási szolgáltatásként az Azure ad-ben.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Az AD DS-összekötő fiókhoz tartozó gyorsbeállítások szükséges engedélyek
A [AD DS-összekötő fiók](#active-directory-account) jön létre olvasását és írását a Windows Server AD és a gyorsbeállítások létrehozásakor a következő engedélyekkel rendelkezik:

| Engedély | Használt |
| --- | --- |
| <li>Címtárváltozások replikálása</li><li>Replikálás könyvtár összes változik |Jelszókivonat-szinkronizálás |
| Olvasási/írási felhasználó az összes tulajdonság |Importálás és az Exchange hibrid |
| Olvasási/írási összes tulajdonságok iNetOrgPerson |Importálás és az Exchange hibrid |
| Olvasási/írási csoportban az összes tulajdonság |Importálás és az Exchange hibrid |
| Olvasási/írási forduljon az összes tulajdonság |Importálás és az Exchange hibrid |
| Új jelszó létrehozása |Felkészülés a jelszóvisszaírás engedélyezése |

### <a name="express-installation-wizard-summary"></a>Az Expressz telepítés varázsló összefoglalás

![Az Expressz telepítés](./media/reference-connect-accounts-permissions/express.png)

A következő található egy összefoglaló az expressz telepítési varázsló lapjain, a hitelesítő adatokat gyűjteni, és hogy mire szolgálnak a.

| Varázsló lapja | Hitelesítő adatok gyűjtése | Szükséges engedélyek | Használt |
| --- | --- | --- | --- |
| – |A felhasználó a telepítési varázsló futtatása |A helyi kiszolgáló rendszergazdája |<li>Hozza létre a [ADSync szolgáltatás fiókja](#azure-ad-connect-sync-service-account) feltárhatja, hogy a szinkronizálási szolgáltatás futtatásához használt fiók. |
| Csatlakozás az Azure AD szolgáltatáshoz |Az Azure Active directory hitelesítő adatok |A globális rendszergazdai szerepkörű Azure AD-ben |<li>Az Azure AD-címtárban a szinkronizálás engedélyezése.</li>  <li>Létrehozását a [Azure AD-összekötő fiók](#azure-ad-service-account) használt a folyamatban lévő szinkronizálási műveletek az Azure AD-ben.</li> |
| Csatlakozás az AD DS szolgáltatáshoz |A helyszíni Active Directory hitelesítő adatok |A vállalati rendszergazdák (EA) csoport az Active Directoryban |<li>Hozza létre a [AD DS-összekötő fiók](#active-directory-account) az Active Directoryban és a rá vonatkozó engedélyeket ad. Ezt a fiókot hozta létre az olvasási és írási címtár szinkronizálása során használatos.</li> |


## <a name="custom-installation-settings"></a>Egyéni telepítési beállítások

Az az egyéni beállítások telepítése a varázsló kínálja, több választási lehetőségek és beállítások. 

### <a name="custom-installation-wizard-summary"></a>Egyéni telepítési varázsló – Összegzés

A következő található egy összefoglaló az egyéni telepítési varázsló lapjain, a hitelesítő adatokat gyűjteni, és hogy mire szolgálnak a.

![Az Expressz telepítés](./media/reference-connect-accounts-permissions/customize.png)

| Varázsló lapja | Hitelesítő adatok gyűjtése | Szükséges engedélyek | Használt |
| --- | --- | --- | --- |
| – |A felhasználó a telepítési varázsló futtatása |<li>A helyi kiszolgáló rendszergazdája</li><li>Ha teljes SQL Server használata esetén a felhasználónak kell lennie a rendszergazdai (SA) az SQL-ben</li> |Alapértelmezés szerint a helyi fiók, amely létrehozza a [szinkronizálás a motor szolgáltatásfiók](#azure-ad-connect-sync-service-account). A fiók csak akkor jön létre, amikor a rendszergazda nem adja meg egy adott fiók. |
| Szinkronizálási szolgáltatások, a szolgáltatás fiók lehetőséget telepítése |AD vagy a helyi felhasználói fiók hitelesítő adatait |Felhasználók, engedélyek a telepítő varázsló |Ha a rendszergazda határozza meg egy fiókot, ezt a fiókot lesz a fiók a szinkronizálási szolgáltatás. |
| Csatlakozás az Azure AD szolgáltatáshoz |Az Azure Active directory hitelesítő adatok |A globális rendszergazdai szerepkörű Azure AD-ben |<li>Az Azure AD-címtárban a szinkronizálás engedélyezése.</li>  <li>Létrehozását a [Azure AD-összekötő fiók](#azure-ad-service-account) használt a folyamatban lévő szinkronizálási műveletek az Azure AD-ben.</li> |
| Csatlakoztassa a címtárakat |A helyszíni Active Directory hitelesítő adatait, amely csatlakozik az Azure AD-erdőhöz |Az engedélyek attól függenek, a szolgáltatások engedélyezése és található [az AD DS-összekötő fiók létrehozása](#create-the-ad-dso-connector-account) |Ennek a fióknak olvasási és írási címtár szinkronizálása során szolgál. |
| AD FS-kiszolgálók |A lista minden egyes kiszolgáló esetén a a varázsló hitelesítő adatait gyűjti, amikor a bejelentkezési hitelesítő adatok a varázslót futtató felhasználó nem elegendőek csatlakoztatása |Tartományi rendszergazda |Telepítés és konfigurálás a az AD FS kiszolgálói szerepkör. |
| Webalkalmazás-proxy kiszolgálók |A lista minden egyes kiszolgáló esetén a a varázsló hitelesítő adatait gyűjti, amikor a bejelentkezési hitelesítő adatok a varázslót futtató felhasználó nem elegendőek csatlakoztatása |Helyi rendszergazda a célgépen |Telepítés és konfigurálás a WAP-kiszolgálói szerepkör. |
| Proxy megbízhatósági hitelesítő adatai |Összevonási szolgáltatás bizalmi kapcsolat hitelesítő adatait (a hitelesítő adatok egy megbízható tanúsítványt az FS regisztrálásához használja a proxy |Tartományi fiók, amely az AD FS-kiszolgáló helyi rendszergazdája |A regisztráció FS – WAP megbízható tanúsítvány. |
| Az AD FS-szolgáltatásfiókot lap, "Használata egy tartományi felhasználói fiókot választja" |AD felhasználói fiók hitelesítő adatait |Domain user |Az AD-felhasználói fiókot, amelynek hitelesítő adatok megadása az AD FS szolgáltatás bejelentkezési fiókként szolgál. |

### <a name="create-the-ad-ds-connector-account"></a>Az AD DS-összekötő fiók létrehozása

>[!IMPORTANT]
>Egy új PowerShell-modul elnevezett ADSyncConfig.psm1 jelent meg a build **1.1.880.0** (2018 augusztus jelent meg), amely tartalmaz egy gyűjtemény parancsmagok segítségével konfigurálhatja a megfelelő Active Directory-engedélyek az Azure Active Directory tartományi szolgáltatások Összekötő-fiók.
>
>További információ: [az Azure AD Connect: Konfigurálja az AD DS-összekötő fiók engedély](how-to-connect-configure-ad-ds-connector-account.md)

A fióknak meg kell adnia a **csatlakoztassa a címtárakat** lap szerepelnie kell az Active Directory telepítése előtt.  Az Azure AD Connect 1.1.524.0 verziója, és később a beállítást engedélyezve lehetővé teheti a létrehozása az Azure AD Connect varázsló a **AD DS-összekötő fiók** Active Directory segítségével kapcsolódhat.  

A szükséges engedélyeket is kell rendelkeznie. A telepítővarázsló ellenőrzi az engedélyek és az esetleges problémákat csak találhatók meg a szinkronizálás során.

Engedélyezi a választható funkciókat függ, hogy milyen engedélyeket van szüksége. Ha több tartományom van, az erdőben lévő összes tartományban a engedélyt kell biztosítani. Ha nem engedélyezi az ezek a szolgáltatások, az alapértelmezett **tartományi felhasználó** elegendő.

| Szolgáltatás | Engedélyek |
| --- | --- |
| MS-DS-ConsistencyGuid funkció |Írási engedélyekkel az ms-DS-ConsistencyGuid attribútum dokumentált [tervezési alapelvei – az ms-DS-ConsistencyGuid használata sourceanchorként](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Jelszókivonat-szinkronizálás |<li>Címtárváltozások replikálása</li>  <li>Replikálás könyvtár összes változik |
| Hibrid Exchange-telepítés |Írási engedélyekkel a dokumentált attribútumok [Exchange hibrid visszaírási](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) felhasználók, csoportok és partnerek számára. |
| Exchange-levelezés nyilvános mappa |Olvasási engedéllyel a dokumentált attribútumok [Exchange-levelezés nyilvános mappa](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) nyilvános mappákhoz. | 
| Jelszóvisszaíró |Írási engedélyekkel a dokumentált attribútumok [a jelszókezelés első lépései](../authentication/howto-sspr-writeback.md) a felhasználók számára. |
| Eszközvisszaíró |A PowerShell-parancsprogram megadott engedélyek [eszközvisszaírás](how-to-connect-device-writeback.md). |
| Group writeback (Csoportvisszaíró) |Lehetővé teszi, hogy a jelszóvisszaíró **Office 365-csoportok** telepített Exchange-erdőhöz.  További információ: [Csoportvisszaírás](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Frissítés
Amikor frissít egy verziójáról az Azure AD Connect új kiadása, a következő engedélyek szükségesek:

>[!IMPORTANT]
>Build 1.1.484 verziótól kezdődően az Azure AD Connect bevezetett egy regressziós hiba, amely az SQL-adatbázis frissítéséhez rendszergazdai engedélyekkel kell rendelkeznie.  Build 1.1.647 javítja ezt a hibát.  Ha frissíti a buildre, szüksége lesz a sysadmin (rendszergazda) engedélyekkel.  Dbo engedélyek nem elegendőek.  Azure AD Connect frissítése a rendszergazdai engedélyek nélkül kísérli meg, ha a frissítés sikertelen lesz, és az Azure AD Connect már nem működik megfelelően, ezt követően.  A Microsoft tisztában, és dolgozik azon, hogy a probléma megoldása.


| Rendszerbiztonsági tag | Szükséges engedélyek | Használt |
| --- | --- | --- |
| A felhasználó a telepítési varázsló futtatása |A helyi kiszolgáló rendszergazdája |Bináris fájlok frissítése. |
| A felhasználó a telepítési varázsló futtatása |ADSyncAdmins tagja |Szinkronizálási szabályok és egyéb konfigurációs módosítások. |
| A felhasználó a telepítési varázsló futtatása |Ha teljes SQL server használja: DBO (vagy hasonlót) a szinkronizálási motor adatbázis |Hajtsa végre a módosításokat adatbázis szolgáltatói, például az új oszlopokat tartalmazó táblák frissítése. |

## <a name="more-about-the-created-accounts"></a>A létrehozott fiókokkal kapcsolatos további információkért
### <a name="ad-ds-connector-account"></a>Az AD DS-összekötő fiók
Ha a gyorsbeállítások használata, majd egy fiókot az Active Directoryban, a szinkronizáláshoz használt jön létre. A létrehozott fiókot az erdő gyökértartományának a felhasználók a tárolóban található, és rendelkezik a neve előtaggal van ellátva **MSOL_**. A fiók létrejön egy hosszú összetett jelszót, amely nem jár le. Ha olyan jelszóházirendet a tartományban van, győződjön meg arról, hogy hosszú és összetett jelszavak szeretne engedélyezni ehhez a fiókhoz.

![AD-fiók](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Ha egyéni beállításokat használja, majd Ön felelős a fiók létrehozása a telepítés megkezdése előtt.  Lásd: [az AD DS-összekötő létrehozása](#create-the-ad-dso-connector-account).

### <a name="adsync-service-account"></a>ADSync szolgáltatás fiókja
A szinkronizálási szolgáltatást futtathatja eltérő fiókkal. A Futtatás egy **virtuális szolgáltatásfiókot** (Szállítóspecifikus), egy **csoportosan felügyelt szolgáltatásfiók** (csoportosan felügyelt szolgáltatásfiókot vagy önállóan felügyelt szolgáltatásfiókot), vagy egy normál felhasználói fiókot. A támogatott beállítások módosultak, 2017 áprilisi kiadása Connect ekkor egy friss telepítését. Ha az Azure AD Connect egy korábbi kiadásáról frissít, ezek a beállítások nem érhetők el.

| Fiók típusa | Telepítési lehetőség | Leírás |
| --- | --- | --- |
| [Virtuális szolgáltatásfiók](#virtual-service-account) | Az expressz és egyéni, 2017 április és újabb verziók | Ez a lehetőség használt összes Expressz telepítés, kivéve a tartományvezérlővé való telepítéséhez. Egyéni legyen az alapértelmezett beállítást, ha egy másik beállítást használja. |
| [Csoportosan felügyelt szolgáltatásfiók](#group-managed-service-account) | Egyéni, 2017 április és újabb verziók | Ha egy távoli SQL Serveren használja, majd ajánlott a csoportosan felügyelt szolgáltatásfiókot használni. |
| [Felhasználói fiók](#user-account) | Az expressz és egyéni, 2017 április és újabb verziók | Egy felhasználói fiókot AAD_ előtaggal csak Windows Server 2008 és a egy tartományvezérlőn telepített telepítésekor a telepítés során jön létre. |
| [Felhasználói fiók](#user-account) | Az expressz és egyéni, 2017 márciusi vagy korábbi kiadásai | AAD_ előtaggal helyi fiók a telepítés során jön létre. Egyéni telepítés használatakor egy másik fiók adható meg. |

Ha a build 2017. március használja Connect vagy korábbi, majd nem, állítsa vissza a szolgáltatásfiók jelszavát, mivel a Windows megsemmisít a titkosítási kulcsok biztonsági okokból. Az Azure AD Connect újratelepítése nélkül nem módosítható a fiók bármelyik más fiókhoz. Ha verzióról a build 2017 áprilisi vagy újabb, akkor az támogatott, de a szolgáltatásfiók jelszavának módosítása nem módosíthatja a használt fiók.

> [!Important]
> A fiók csak az első telepítést állíthat be. A telepítés befejezése után a szolgáltatás fiókjának módosítása nem támogatott.

Ez az az alapértelmezett szinkronizálási szolgáltatásfiók ajánlott és támogatott beállítások tábláját.

Jelmagyarázat:

- **Félkövér** azt jelzi, hogy az alapértelmezett beállítás, és a legtöbb esetben a javasolt megoldás.
- *Dőlt* a javasolt beállítás azt jelzi, ha az nem az alapértelmezett beállítás.
- 2008 - alapértelmezett beállítás a Windows Server 2008 rendszeren
- Nem-félkövér – támogatott beállítás
- Helyi fiók – helyi felhasználói fiókot a kiszolgálón
- Tartományi fiók – tartományi felhasználói fiók
- az önállóan felügyelt szolgáltatásfiókokkal - [önálló felügyelt szolgáltatásfiók](https://technet.microsoft.com/library/dd548356.aspx)
- csoportosan felügyelt szolgáltatásfiók - [csoportosan felügyelt szolgáltatásfiók](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Egyéni | Remote SQL</br>Egyéni |
| --- | --- | --- | --- |
| **önálló vagy munkacsoportbeli számítógéphez** | Nem támogatott | **VSA**</br>Helyi fiók (2008)</br>Helyi fiók |  Nem támogatott |
| **tartományhoz csatlakoztatott gép** | **VSA**</br>Helyi fiók (2008) | **VSA**</br>Helyi fiók (2008)</br>Helyi fiók</br>Tartományi fiók</br>sMSA,gMSA | **gMSA**</br>Tartományi fiók |
| **Tartományvezérlő** | **Tartományi fiók** | *gMSA*</br>**Tartományi fiók**</br>sMSA| *gMSA*</br>**Tartományi fiók**|

#### <a name="virtual-service-account"></a>Virtuális szolgáltatásfiók
Egy helyi szolgáltatásfiókot egy olyan fiók, amely nem rendelkezik egy jelszó és a Windows által felügyelt speciális típusú.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

A Szállítóspecifikus célja használható forgatókönyvek a szinkronizálási motor SQL amelyeknél és ugyanazon a kiszolgálón. Ha távoli SQL használja, akkor használata ajánlott olyan [csoportosan felügyelt szolgáltatásfiók](#managed-service-account) helyette.

A szolgáltatás használatához a Windows Server 2008 R2 vagy újabb. Ha az Azure AD Connect telepítése Windows Server 2008 rendszeren, akkor a telepítés visszaáll egy [felhasználói fiók](#user-account) helyette.

#### <a name="group-managed-service-account"></a>A csoportosan felügyelt szolgáltatásfiók
Egy távoli SQL Serveren használja, akkor azt javasoljuk, hogy a használatával egy **csoportosan felügyelt szolgáltatásfiók**. Az Active Directory előkészítése a csoport felügyelt szolgáltatásfiók további információkért lásd: [csoportosan felügyelt szolgáltatásfiókok áttekintése](https://technet.microsoft.com/library/hh831782.aspx).

Ezt a beállítást, a használandó a [szükséges összetevők telepítése](how-to-connect-install-custom.md#install-required-components) lapon válassza **meglévő szolgáltatásfiók használata**, és válassza ki **felügyelt szolgáltatásfiók**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Használata is támogatott a [önálló felügyelt szolgáltatásfiók](https://technet.microsoft.com/library/dd548356.aspx). Azonban ezeket csak akkor használható a helyi gépen, és nem jár előnnyel használja azokat az alapértelmezett virtuális szolgáltatásfiók-en keresztül.

Ez a funkció a Windows Server 2012 vagy újabb szükséges. A régebbi operációs rendszert használja, és használjon távoli SQL kell, akkor kell használni egy [felhasználói fiók](#user-account).

#### <a name="user-account"></a>Felhasználói fiók
A helyi szolgáltatás fiók létrejön a telepítővarázsló (kivéve, ha a fiók használható egyéni beállítások megadása). A fióknak a következő előtaggal **AAD_** és a tényleges szinkronizálási szolgáltatáshoz használt fiókként való futtatásra. Ha az Azure AD Connect tartományvezérlőre telepíti, a fiók jön létre a tartomány. A **AAD_** szolgáltatásfiók a tartományban kell lennie, ha:
   - az SQL Servert futtató távoli kiszolgáló használatára
   - egy hitelesítést igénylő proxyt használ

![Szinkronizálási szolgáltatásfiók](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

A fiók létrejön egy hosszú összetett jelszót, amely nem jár le.

Ez a fiók segítségével biztonságos módon a jelszavak, a más fiókokhoz tárolni. Ezek más fiókok jelszavát tárolódnak az adatbázisban titkosított. A titkosítási szolgáltatások titkos-kulcs titkosítás Windows Data Protection API (DPAPI) használatával védi a titkos kulcsokat a titkosítási kulcsokat.

Ha teljes SQL Server használja, a szolgáltatás fiók a létrehozott adatbázis a szinkronizálási motor a DBO. A szolgáltatás nem fog működni, és bármilyen egyéb engedélyek helyesen. SQL-bejelentkezési is létrejön.

A fiók is engedéllyel rendelkezik a fájlokat, beállításkulcsokat és más a szinkronizálási motor kapcsolódó objektumok.

### <a name="azure-ad-connector-account"></a>Az Azure AD-összekötő fiók
A szinkronizálási szolgáltatás használata az Azure AD-fiók létrejön. Ez a fiók a megjelenítendő neve alapján azonosítható.

![AD-fiók](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

A második rész a felhasználónév ellenőrizhető, a fiókot használja a kiszolgáló nevét. A képen látható a kiszolgáló neve DC1. Ha kiszolgálók átmeneti, minden kiszolgálóhoz tartozik egy saját fiókot.

A fiók létrejön egy hosszú összetett jelszót, amely nem jár le. Egy különleges szerepkörrel engedélyezett **címtár-szinkronizálási fiókok** , amely csak a címtár-szinkronizálási feladatok végrehajtásához engedélyekkel rendelkezik. Ez a különleges beépített szerepkör kívül az Azure AD Connect varázsló nem adható meg. Az Azure portal megjeleníti ezt a fiókot a szerepkör **felhasználói**.

Nincs maximális hossza 20 sync service-fiókoknak az Azure ad-ben. Meglévő Azure AD-szolgáltatásfiókot listájának lekérése az Azure AD-ben, futtassa a következő Azure AD PowerShell-parancsmagot: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Távolítsa el a nem használt Azure ad-ben a szolgáltatásfiókok, futtassa az alábbi Azure AD PowerShell-parancsmagot: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="related-documentation"></a>Kapcsolódó dokumentáció
Ha a dokumentáció a nem olvasta [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md), az alábbi táblázat a kapcsolódó témakörökre mutató hivatkozásokat tartalmaz.

|Témakör |Hivatkozás|  
| --- | --- |
|Az Azure AD Connect letöltése | [Az Azure AD Connect letöltése](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Telepítés gyorsbeállítások használatával | [Az Azure AD Connect gyorstelepítése](how-to-connect-install-express.md)|
|Telepítés testreszabott beállítások használatával | [Az Azure AD Connect testreszabott telepítése](./how-to-connect-install-custom.md)|
|Frissítés a DirSync szolgáltatásról | [Frissítés az Azure AD szinkronizáló eszközéről (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|A telepítést követően | [A telepítés ellenőrzése és licencek hozzárendelése](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
