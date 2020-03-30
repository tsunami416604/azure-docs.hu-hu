---
title: 'Azure AD Connect: Fiókok és engedélyek | Microsoft dokumentumok'
description: Ez a témakör a használt és létrehozott fiókokat, valamint a szükséges engedélyeket ismerteti.
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
ms.date: 10/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6071e6553fb1275fea63a37b4897aef2685bd509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253766"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: fiókok és engedélyek

## <a name="accounts-used-for-azure-ad-connect"></a>Az Azure AD Connecthez használt fiókok

![számlák áttekintése](media/reference-connect-accounts-permissions/account5.png)

Az Azure AD Connect 3 fiókot használ a helyszíni vagy a Windows Server Active Directory és az Azure Active Directory adatainak szinkronizálásához.  Ezek a számlák a következők:

- **AD DS-összekötő fiók:** adatok olvasására/írására szolgál a Windows Server Active Directoryba

- **ADSync szolgáltatásfiók:** a szinkronizálási szolgáltatás futtatásához és az SQL-adatbázis eléréséhez

- **Azure AD-összekötő fiók:** adatok írására használható az Azure AD-nek

Az Azure AD Connect futtatásához használt három fiókmellett az Azure AD Connect telepítéséhez a következő további fiókokra is szüksége lesz.  Ezek a következők:

- **Helyi rendszergazdai fiók:** Az Azure AD Connectet telepítő rendszergazda, aki helyi rendszergazdai engedélyekkel rendelkezik a számítógépen.

- **AD DS vállalati rendszergazdai fiók:** A fenti "AD DS Connector fiók" létrehozásához tetszés szerint használható.

- **Azure AD globális rendszergazdai fiók:** az Azure AD Connector-fiók létrehozásához és az Azure AD konfigurálásához használható.

- **SQL SA-fiók (nem kötelező)**: az ADSync-adatbázis létrehozásához használható az SQL Server teljes verziójának használatakor.  Ez az SQL Server lehet helyi vagy távoli az Azure AD Connect telepítéséhez.  Ez a fiók ugyanaz lehet, mint a vállalati rendszergazda.  Az adatbázis kiépítése most már az SQL-rendszergazda sávon kívüli módon hajthatja végre, majd az Azure AD Connect rendszergazda által az adatbázis tulajdonosi jogokkal telepítheti.  Erről további információt az [Azure AD Connect telepítése SQL delegált rendszergazdai engedélyekkel című](how-to-connect-install-sql-delegation.md) témakörben talál.


>[!IMPORTANT]
> A build 1.4.###.. már nem támogatott, hogy egy vállalati rendszergazdai vagy tartományi rendszergazdai fiók, mint az AD DS Connector fiók.  Ha a **meglévő fiók használata**kor olyan fiókot próbál meg beírni, amely vállalati rendszergazda vagy tartomány-rendszergazda, hibaüzenetjelenik meg.

> [!NOTE]
> Az Azure AD Connectben használt felügyeleti fiókok kezelése esae felügyeleti erdőből (más néven "vörös erdő" néven) támogatott.
> A dedikált felügyeleti erdők lehetővé teszik a szervezetek számára a rendszergazdai fiókok, munkaállomások és csoportok üzemeltetését az üzemi környezetnél erősebb biztonságú környezetben.
> Ha többet szeretne megtudni a kijelölt közigazgatási erdőkről, olvassa el az [ESAE közigazgatási erdőtervezési megközelítését](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> A kezdeti telepítés után nincs szükség globális rendszergazdai szerepkörre, és az egyetlen szükséges fiók a **Címtár-szinkronizálási fiókok** szerepkör-fiók lesz. Ez nem feltétlenül jelenti azt, hogy csak a globális rendszergazdai szerepkörrel rendelkező fiókot szeretné eltávolítani. Jobb, ha a szerepkört kevésbé hatékony szerepre változtatja, mivel a fiók teljes eltávolítása problémákat okozhat, ha újra kell futtatnia a varázslót. A szerepkör jogosultságának csökkentésével mindig újra emelheti a jogosultságokat, ha újra ki kell használnia az Azure AD Connect varázslót. 

## <a name="installing-azure-ad-connect"></a>Az Azure AD Connect telepítése
Az Azure AD Connect telepítővarázsló két különböző elérési utat kínál:

* Az Expressz beállítások ban a varázslónak több jogosultságra van szüksége.  Ez azért van így, hogy könnyen betudja állítani a konfigurációt anélkül, hogy felhasználókat kellene létrehoznia vagy engedélyeket kellene konfigurálnia.
* Az Egyéni beállítások párbeszédpanelen a varázsló további választási lehetőségeket és lehetőségeket kínál. Vannak azonban olyan helyzetek, amikor önnek kell biztosítania, hogy a megfelelő engedélyekkel rendelkezzen.



## <a name="express-settings-installation"></a>Expressz beállítások telepítése
Az Express beállítások ban a telepítővarázsló a következőket kéri:

  - Az AD DS vállalati rendszergazda hitelesítő adatai
  - Az Azure AD globális rendszergazdai hitelesítő adatai

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS vállalati rendszergazdai hitelesítő adatai
Az Active Directory Active Directory konfigurálásához az Active Directory konfigurálása az AD DS Enterprise Admin fiók. Ezek a hitelesítő adatok csak a telepítés során használatosak, és a telepítés befejezése után nem használatosak. A vállalati rendszergazdának, nem a tartományi rendszergazdának kell gondoskodnia arról, hogy az Active Directory engedélyei minden tartományban beállíthatók legyenek.

Ha a DirSync-ről frissít, az AD DS Enterprise Rendszergazdák hitelesítő adatai segítségével alaphelyzetbe állítja a DirSync által használt fiók jelszavát. Az Azure AD globális rendszergazdai hitelesítő adataira is szüksége van.

### <a name="azure-ad-global-admin-credentials"></a>Az Azure AD globális rendszergazdai hitelesítő adatai
Ezek a hitelesítő adatok csak a telepítés során használatosak, és a telepítés befejezése után nem használatosak. Az Azure AD-összekötő fiók az Azure AD módosításainak szinkronizálásához használt azure AD létrehozásához használható. A fiók is lehetővé teszi a szinkronizálást funkcióként az Azure AD-ben.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Az AD DS-összekötő fiók szükséges engedélyei a gyorsbeállításokhoz
Az AD DS Connector-fiók a Windows Server AD rendszerbe való olvasásra és írásra készült, és a következő engedélyekkel rendelkezik, amikor azokat a gyorsbeállítások hozták létre:

| Engedély | Alkalmazási cél |
| --- | --- |
| <li>Címtármódosítások replikálása</li><li>Könyvtármódosítás-módosítások replikálása |Jelszókivonat-szinkronizálás |
| Az összes tulajdonság olvasása/írása Felhasználó |Hibrid importálás és Exchange |
| Az összes tulajdonság olvasása/írása iNetOrgPerson |Hibrid importálás és Exchange |
| Az összes tulajdonság olvasása/írása Csoport |Hibrid importálás és Exchange |
| Az összes tulajdonság olvasása/írása Kapcsolattartó |Hibrid importálás és Exchange |
| Új jelszó létrehozása |A jelszóvisszaírás engedélyezésének előkészítése |

### <a name="express-installation-wizard-summary"></a>Expressz telepítővarázsló összegzése

![Expressz telepítés](./media/reference-connect-accounts-permissions/express.png)

Az alábbiakban összefoglaljuk az expressz telepítő varázsló lapjait, a begyűjtött hitelesítő adatokat és azok használható adatait.

| Varázsló lapja | Összegyűjtött hitelesítő adatok | Szükséges engedélyek | Használt |
| --- | --- | --- | --- |
| N/A |A telepítővarázslót futtató felhasználó |A helyi kiszolgáló rendszergazdája |<li>Létrehozza az ADSync szolgáltatásfiókot, amely a szinkronizálási szolgáltatás futtatásához használatos. |
| Csatlakozás az Azure AD szolgáltatáshoz |Az Azure AD címtár hitelesítő adatai |Globális rendszergazdai szerepkör az Azure AD-ben |<li>Szinkronizálás engedélyezése az Azure AD könyvtárban.</li>  <li>Az Azure AD Connector-fiók létrehozása, amely az Azure AD folyamatban lévő szinkronizálási műveletekhez használatos.</li> |
| Csatlakozás az AD DS szolgáltatáshoz |Helyszíni Active Directory-hitelesítő adatok |A Vállalati rendszergazdák (EA) csoport tagja az Active Directoryban |<li>Létrehozza az Active Directory ban az Active Directoryban az Active Directoryban az Active Directoryban az Active DS-összekötő fiókot, és engedélyeket ad hozzá. Ez a létrehozott fiók a címtáradatok olvasására és írására szolgál a szinkronizálás során.</li> |


## <a name="custom-installation-settings"></a>Egyéni telepítési beállítások

Az egyéni beállítások telepítésével a varázsló több választási lehetőséget és lehetőséget kínál. 

### <a name="custom-installation-wizard-summary"></a>Egyéni telepítővarázsló összegzése

Az alábbiakban összefoglaljuk az egyéni telepítővarázsló lapjait, a begyűjtött hitelesítő adatokat és azok használható adatait.

![Expressz telepítés](./media/reference-connect-accounts-permissions/customize.png)

| Varázsló lapja | Összegyűjtött hitelesítő adatok | Szükséges engedélyek | Használt |
| --- | --- | --- | --- |
| N/A |A telepítővarázslót futtató felhasználó |<li>A helyi kiszolgáló rendszergazdája</li><li>Ha teljes SQL Server t használ, a felhasználónak rendszergazdai (SA) rendszernek kell lennie az SQL-ben</li> |Alapértelmezés szerint létrehozza a szinkronizálási motor szolgáltatásfiókként használt helyi fiókot. A fiók csak akkor jön létre, ha a rendszergazda nem ad meg egy adott fiókot. |
| Szinkronizálási szolgáltatások telepítése, Szolgáltatásfiók beállítás |AD vagy helyi felhasználói fiók hitelesítő adatai |Felhasználó, az engedélyeket a telepítő varázsló adta meg |Ha a rendszergazda fiókot ad meg, a szinkronizálási szolgáltatás szolgáltatásfiókjaként ez a fiók lesz. |
| Csatlakozás az Azure AD szolgáltatáshoz |Az Azure AD címtár hitelesítő adatai |Globális rendszergazdai szerepkör az Azure AD-ben |<li>Szinkronizálás engedélyezése az Azure AD könyvtárban.</li>  <li>Az Azure AD Connector-fiók létrehozása, amely az Azure AD folyamatban lévő szinkronizálási műveletekhez használatos.</li> |
| Csatlakoztassa a címtárakat |Az Azure AD-hez csatlakoztatott erdők helyszíni Active Directory-hitelesítő adatai |Az engedélyek attól függnek, hogy mely szolgáltatásokat engedélyezi, és megtalálhatók az AD DS-összekötő fiók létrehozása című részben |Ez a fiók a címtáradatok olvasására és írására szolgál a szinkronizálás során. |
| AD FS-kiszolgálók |A varázsló a lista minden kiszolgálóján álcaadatokat gyűjt, ha a varázslót futtató felhasználó bejelentkezési hitelesítő adatai nem elegendőek a csatlakozáshoz |Tartományi rendszergazda |Az AD FS kiszolgálói szerepkör telepítése és konfigurálása. |
| Webalkalmazás-proxykiszolgálók |A varázsló a lista minden kiszolgálóján álcaadatokat gyűjt, ha a varázslót futtató felhasználó bejelentkezési hitelesítő adatai nem elegendőek a csatlakozáshoz |Helyi rendszergazda a célgépen |A WAP-kiszolgálói szerepkör telepítése és konfigurálása. |
| Proxymegbízhatósági hitelesítő adatok |Összevonási szolgáltatás megbízhatósági hitelesítő adatai (az a hitelesítő adat, amelyet a proxy az FS-ből származó megbízhatósági tanúsítvány igényléséhez használ |Az AD FS-kiszolgáló helyi rendszergazdája ként fiók |Az FS-WAP megbízhatósági tanúsítvány kezdeti igénylése. |
| AD FS szolgáltatásfiók lap, "Domain felhasználói fiók használata beállítás" |AD felhasználói fiók hitelesítő adatai |Tartományi felhasználó |Az Azure AD felhasználói fiók, amelynek hitelesítő adatait meg vannak adva, az AD FS szolgáltatás bejelentkezési fiókjaként használatos. |

### <a name="create-the-ad-ds-connector-account"></a>Az AD DS-összekötő fiók létrehozása

>[!IMPORTANT]
>Az **1.1.880.0** (2018 augusztusában kiadott) buildtel egy új PowerShell-modult vezettek be, amely parancsmagok gyűjteményét tartalmazza az Azure AD DS Connector-fiók megfelelő Active Directory-engedélyeinek konfigurálásához.
>
>További információ: Azure AD Connect: Configure AD DS Connector Account Permission (További információ: [Azure AD Connect: Configure AD DS Connector Account Permission) (További információ: Azure AD Connect: Configure AD DS Connector Account Permission](how-to-connect-configure-ad-ds-connector-account.md)

A **Könyvtárak csatlakoztatása** lapon megadott fióknak a telepítés előtt meg kell jelennie az Active Directoryban.  Az Azure AD Connect 1.1.524.0-s és újabb verziója lehetővé teszi, hogy az Azure AD Connect varázsló létrehozza az Active Directoryhoz való csatlakozáshoz használt **AD DS-összekötő fiókot.**  

Rendelkeznie kell a szükséges engedélyekkel is. A telepítővarázsló nem ellenőrzi az engedélyeket, és a problémák csak a szinkronizálás során találhatók.

A szükséges engedélyek az engedélyezett választható funkcióktól függnek. Ha több tartománnyal rendelkezik, az engedélyeket az erdő összes tartományára meg kell adni. Ha nem engedélyezi a szolgáltatások egyikét sem, az alapértelmezett **tartományi felhasználói** engedélyek elegendőek.

| Szolgáltatás | Engedélyek |
| --- | --- |
| ms-DS-ConsistencyGuid szolgáltatás |Az ms-DS-ConsistencyGuid attribútumnak a Tervezési fogalmak - [Az ms-DS-ConsistencyGuid használata sourceAnchor néven dokumentált ms-DS-ConsistencyGuid](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)attribútumhoz. | 
| Jelszókivonat-szinkronizálás |<li>Címtármódosítások replikálása</li>  <li>Könyvtármódosítás-módosítások replikálása |
| Hibrid Exchange-telepítés |Írja le az Exchange [hibrid visszaírásában](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dokumentált attribútumok engedélyeit felhasználók, csoportok és kapcsolattartók számára. |
| Exchange Mail nyilvános mappa |A nyilvános mappák Exchange [Mail nyilvános mappájában](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dokumentált attribútumok olvasása. | 
| Jelszóvisszaíró |Írja le az engedélyeket a felhasználók [jelszókezelésével való ismerkedés](../authentication/howto-sspr-writeback.md) című részben dokumentált attribútumokhoz. |
| Eszközvisszaíró |PowerShell-parancsfájllal megadott engedélyek az [eszköz visszaírásában](how-to-connect-device-writeback.md)leírtak szerint. |
| Group writeback (Csoportvisszaíró) |Lehetővé teszi az **Office 365-csoportok** visszaírását egy olyan erdőbe, amelyen telepítve van az Exchange.  További információ: [Group Writeback](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Frissítés
Amikor az Azure AD Connect egyik verziójáról új kiadásra frissít, a következő engedélyekre van szüksége:

>[!IMPORTANT]
>Az 1.1.484-es létrehozástól kezdve az Azure AD Connect bevezetett egy regressziós hibát, amely rendszergazdai engedélyeket igényel az SQL-adatbázis frissítéséhez.  Ezt a hibát az 1.1.647-es buildben javítják.  Ha erre a buildre frissít, rendszergazdai engedélyekre lesz szüksége.  A Dbo engedélyek nem elegendőek.  Ha rendszergazdai engedélyek nélkül próbálja frissíteni az Azure AD Connectet, a frissítés sikertelen lesz, és az Azure AD Connect a továbbiakban nem fog megfelelően működni.  A Microsoft tisztában van ezzel, és dolgozik a javításon.


| Fő | Szükséges engedélyek | Alkalmazási cél |
| --- | --- | --- |
| A telepítővarázslót futtató felhasználó |A helyi kiszolgáló rendszergazdája |Bináris fájlok frissítése. |
| A telepítővarázslót futtató felhasználó |Az ADSyncAdmins tagja |Módosítsa a szinkronizálási szabályokat és más konfigurációkat. |
| A telepítővarázslót futtató felhasználó |Ha teljes SQL-kiszolgálót használ: A szinkronizálómotor-adatbázis DBO-ja (vagy hasonló) |Adatbázisszint-módosításokat hajtvégre, például frissítsd a táblákat új oszlopokkal. |

## <a name="more-about-the-created-accounts"></a>További információk a létrehozott fiókokról
### <a name="ad-ds-connector-account"></a>AD DS Connector-fiók
Ha expressz beállításokat használ, akkor a program létrehoz egy fiókot az Active Directoryban, amely szinkronizálásra szolgál. A létrehozott fiók a Felhasználók tároló erdőgyökér-tartományában található, és a neve **előtaggal rendelkezik MSOL_**. A fiók egy hosszú, összetett jelszóval jön létre, amely nem jár le. Ha rendelkezik jelszóházirenddel a tartományban, győződjön meg arról, hogy hosszú és összetett jelszavak engedélyezettek ehhez a fiókhoz.

![AD-fiók](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Ha egyéni beállításokat használ, akkor a telepítés megkezdése előtt ön a felelős a fiók létrehozásáért.  Lásd: Az AD DS-összekötő fiók létrehozása.

### <a name="adsync-service-account"></a>ADSync-szolgáltatásfiók
A szinkronizálási szolgáltatás különböző fiókok alatt futtatható. Virtuális **szolgáltatásfiók** (VSA), **csoportkezelt szolgáltatásfiók** (gMSA/sMSA) vagy normál felhasználói fiók alatt futtatható. A támogatott beállítások megváltoztak a Connect 2017 áprilisi kiadásával, amikor friss telepítést végez. Ha az Azure AD Connect egy korábbi kiadásából frissít, ezek a további beállítások nem érhetők el.

| A számla típusa | Telepítési lehetőség | Leírás |
| --- | --- | --- |
| [Virtuális szolgáltatásfiók](#virtual-service-account) | Expressz és egyéni, 2017 április és később | Ez a beállítás minden expressz telepítésnél használatos, kivéve a tartományvezérlőn lévő telepítéseket. Az egyéni, ez az alapértelmezett beállítás, kivéve, ha egy másik lehetőség van használva. |
| [Csoportosan felügyelt szolgáltatásfiók](#group-managed-service-account) | Egyéni, 2017 április és később | Ha távoli SQL-kiszolgálót használ, javasoljuk, hogy használjon csoportkezelt szolgáltatásfiókot. |
| [Felhasználói fiók](#user-account) | Expressz és egyéni, 2017 április és később | A AAD_ előtaggal előtaggal rendelkező felhasználói fiók csak a Windows Server 2008 rendszerre telepített és tartományvezérlőre történő telepítéskor jön létre a telepítés során. |
| [Felhasználói fiók](#user-account) | Expressz és egyéni, 2017 március és korábbi | A telepítés során létrejön egy AAD_ előtaggal rendelkező helyi fiók. Egyéni telepítés esetén egy másik fiók is megadható. |

Ha a Connect with a build et 2017 márciusában vagy korábban használja, akkor ne állítsa alaphelyzetbe a szolgáltatásfiók jelszavát, mivel a Windows biztonsági okokból megsemmisíti a titkosítási kulcsokat. A fiókot nem módosíthatja más fiókra az Azure AD Connect újratelepítése nélkül. Ha 2017 áprilisa vagy újabb verziótól frissít egy buildre, akkor a szolgáltatásfiók jelszavának módosítása nem módosítható, de a használt fiók nem módosítható.

> [!Important]
> A szolgáltatásfiókot csak az első telepítéskor állíthatja be. A telepítés befejezése után nem támogatott a szolgáltatásfiók módosítása.

Ez a táblázat a szinkronizálási szolgáltatásfiók alapértelmezett, ajánlott és támogatott beállításait tartalmaz.

Jelmagyarázat:

- **A Félkövér** az alapértelmezett beállítást, a legtöbb esetben pedig az ajánlott beállítást jelöli.
- *A dőlt* betű jelzi az ajánlott beállítást, ha nem az alapértelmezett beállítás.
- 2008 – Alapértelmezett beállítás Windows Server 2008 rendszerre telepítve
- Nem félkövér - Támogatott beállítás
- Helyi fiók – Helyi felhasználói fiók a kiszolgálón
- Tartományi fiók – Tartományi felhasználói fiók
- sMSA – [önálló felügyelt szolgáltatásfiók](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA – [csoport felügyelt szolgáltatásfiókja](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Egyéni | Távoli SQL</br>Egyéni |
| --- | --- | --- | --- |
| **önálló/munkacsoport-gép** | Nem támogatott | **Vsa**</br>Helyi fiók (2008)</br>Helyi fiók |  Nem támogatott |
| **tartományhoz csatlakozó gép** | **Vsa**</br>Helyi fiók (2008) | **Vsa**</br>Helyi fiók (2008)</br>Helyi fiók</br>Tartományi fiók</br>sMSA,gMSA | **gMSA**</br>Tartományi fiók |
| **Tartományvezérlő** | **Tartományi fiók** | *gMSA*</br>**Tartományi fiók**</br>sMSA| *gMSA*</br>**Tartományi fiók**|

#### <a name="virtual-service-account"></a>Virtuális szolgáltatásfiók
A virtuális szolgáltatásfiók olyan speciális fióktípus, amely nem rendelkezik jelszóval, és amelyet a Windows kezel.

![Vsa](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

A VSA olyan forgatókönyvekkel használható, ahol a szinkronizálási motor és az SQL ugyanazon a kiszolgálón található. Ha távoli SQL-t használ, javasoljuk, hogy inkább csoportfelügyelt szolgáltatásfiókot használjon.

Ehhez a szolgáltatáshoz Windows Server 2008 R2 vagy újabb rendszer szükséges. Ha az Azure AD Connectet Windows Server 2008 rendszeren telepíti, a telepítés visszaáll [a felhasználói fiók használatára.](#user-account)

#### <a name="group-managed-service-account"></a>Csoport kezelt szolgáltatásfiókja
Ha távoli SQL-kiszolgálót használ, javasoljuk, hogy használjon **csoportkezelt szolgáltatásfiókot.** Az Active Directory csoportfelügyelt szolgáltatásfiókra való előkészítéséről a [Csoportfelügyelt szolgáltatásfiókok – áttekintés című témakörben olvashat bővebben.](https://technet.microsoft.com/library/hh831782.aspx)

A beállítás használatához a [Szükséges összetevők telepítése](how-to-connect-install-custom.md#install-required-components) lapon válassza a Meglévő **szolgáltatásfiók használata**lehetőséget, majd a Felügyelt **szolgáltatásfiók lehetőséget.**  
![Vsa](./media/reference-connect-accounts-permissions/serviceaccount.png)  
[Önálló felügyelt szolgáltatásfiók](https://technet.microsoft.com/library/dd548356.aspx)használata is támogatott. Ezek azonban csak a helyi számítógépen használhatók, és nincs előnye, hogy használja őket az alapértelmezett virtuális szolgáltatás fiók.

Ehhez a szolgáltatáshoz Windows Server 2012-es vagy újabb rendszer szükséges. Ha régebbi operációs rendszert és távoli SQL-t kell használnia, akkor felhasználói fiókot kell [használnia.](#user-account)

#### <a name="user-account"></a>Felhasználói fiók
A telepítővarázsló helyi szolgáltatásfiókot hoz létre (kivéve, ha az egyéni beállításokban használni kívánt fiókot adja meg). A fiók **előtaggal van** AAD_, és a tényleges szinkronizálási szolgáltatás futtatásához használható. Ha az Azure AD Connectet tartományvezérlőre telepíti, a fiók a tartományban jön létre. A **AAD_** szolgáltatásfióknak a tartományban kell lennie, ha:
   - SQL-kiszolgálót futtató távoli kiszolgálót használ
   - hitelesítést igénylő proxyt használ

![Szolgáltatásfiók szinkronizálása](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

A fiók egy hosszú, összetett jelszóval jön létre, amely nem jár le.

Ez a fiók a többi fiók jelszavainak biztonságos tárolására szolgál. Ezek a fiókok jelszavai titkosítottan tárolódnak az adatbázisban. A titkosítási kulcsok titkos kulcsait a windowsi adatvédelmi API (DPAPI) használatával titkoskulcs-titkosítás védi a kriptográfiai szolgáltatásokkal.

Ha teljes SQL Server t használ, akkor a szolgáltatásfiók a szinkronizálási motor hozlétre létrehozott adatbázisának dbo-ja. A szolgáltatás nem fog a rendeltetésszerűen működni más engedélyekkel. Egy SQL bejelentkezés is létrejön.

A fiók a szinkronizálási motorhoz kapcsolódó fájlokhoz, beállításkulcsokhoz és egyéb objektumokhoz is engedélyt kap.

### <a name="azure-ad-connector-account"></a>Azure AD Connector-fiók
Az Azure AD-ben egy fiók jön létre a szinkronizálási szolgáltatás használatára. Ez a fiók a megjelenítendő neve alapján azonosítható.

![AD-fiók](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Annak a kiszolgálónak a neve, amelyen a fiókot használják, a felhasználónév második részében azonosítható. A képen a kiszolgáló neve DC1. Ha átmeneti kiszolgálói vannak, minden kiszolgálósaját fiókkal rendelkezik.

A fiók egy hosszú, összetett jelszóval jön létre, amely nem jár le. Különleges szerepkört kap **a címtár-szinkronizálási fiókok,** amelyek csak a címtár-szinkronizálási feladatok végrehajtására vonatkozó engedélyekkel rendelkeznek. Ez a speciális beépített szerepkör nem adható meg az Azure AD Connect varázslón kívül. Az Azure Portalon ez a fiók a Felhasználó szerepkörrel jeleníti **meg.**

Az Azure AD-ben legfeljebb 20 szinkronizálási szolgáltatásfiók van. A meglévő Azure AD-szolgáltatásfiókok listájának beszerezéséhez futtassa a következő Azure AD PowerShell-parancsmast:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

A nem használt Azure AD-szolgáltatásfiókok eltávolításához futtassa a következő Azure AD PowerShell-parancsmast:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>A fenti PowerShell-parancsok használata előtt telepítenie kell az [Azure Active Directory PowerShell for Graph modult,](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) és csatlakoznia kell az Azure AD-példányához a [Connect-AzureAD használatával.](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

Az Azure AD Connector-fiók jelszavának kezeléséről és alaphelyzetbe állításáról az [Azure AD Connect-fiók kezelése című](how-to-connect-azureadaccount.md) témakörben talál további információt.

## <a name="related-documentation"></a>Kapcsolódó dokumentáció
Ha nem olvasta el a helyszíni [identitások integrálása](whatis-hybrid-identity.md)az Azure Active Directoryval című dokumentációt, az alábbi táblázat a kapcsolódó témakörökre mutató hivatkozásokat tartalmaz.

|Témakör |Hivatkozás|  
| --- | --- |
|Az Azure AD Connect letöltése | [Az Azure AD Connect letöltése](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Telepítés gyorsbeállítások használatával | [Az Azure AD Connect gyorstelepítése](how-to-connect-install-express.md)|
|Telepítés testreszabott beállítások használatával | [Az Azure AD Connect testreszabott telepítése](./how-to-connect-install-custom.md)|
|Frissítés a DirSync szolgáltatásról | [Frissítés az Azure AD szinkronizáló eszközéről (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|A telepítést követően | [A telepítés ellenőrzése és licencek hozzárendelése](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
