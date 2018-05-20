---
title: Alkalmazásproxy hibaelhárítása |} Microsoft Docs
description: Bemutatja, hogyan adhat az Azure AD alkalmazásproxy hibák elhárítása.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 838bdccb06e5763d33f63208cb6f941a55778b32
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Alkalmazásproxy problémák és hibaüzenetek hibaelhárítása
Ha hiba történik, a közzétett alkalmazás eléréséhez, vagy az alkalmazás-közzététel, ellenőrizze a megjelenítéséhez, ha a Microsoft Azure AD-alkalmazásproxy megfelelően működik-e a következő beállításokat:

* Nyissa meg a központi Windows-szolgáltatások konzolt, és ellenőrizze, hogy a **Microsoft AAD alkalmazásproxy-összekötő** szolgáltatás engedélyezve van és fut. Előfordulhat, hogy is gondoskodnia kell a szolgáltatásalkalmazás-Proxy szolgáltatás tulajdonságok oldalának, tekintse meg a következő ábrán látható módon:  
  ![Microsoft AAD Application Proxy Connector tulajdonságai ablakban képernyőképe](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* Nyissa meg az eseménynaplót, és keresse meg az Application Proxy connector-események **alkalmazási és Szolgáltatásnaplójában** > **Microsoft** > **AadApplicationProxy**  >  **Összekötő** > **Admin**.
* Szükség esetén további részletes érhetők el naplók által [az alkalmazásproxy-összekötő munkamenet napló bekapcsolása](manage-apps/application-proxy-connectors.md#under-the-hood).

Az Azure AD-hibaelhárítás eszközzel kapcsolatos további információkért lásd: [hibaelhárító eszközt összekötő hálózati Előfeltételek ellenőrzése](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites).

## <a name="the-page-is-not-rendered-correctly"></a>A lap nem megfelelően megjelenítése
Előfordulhat, hogy az alkalmazás megjelenítése, vagy nem megfelelően működik-e anélkül vonatkozó hibaüzeneteket. Ez akkor fordulhat elő, ha a közzétett cikk elérési, de az alkalmazáshoz az szükséges, hogy elérési útja kívül található tartalom.

Például, ha az elérési út közzététele https://yourapp/app , de az alkalmazás képeket meghívja https://yourapp/media, nem lehet megjeleníteni. Győződjön meg arról, hogy az alkalmazás a legmagasabb szintű elérési úton, meg kell adnia az összes kapcsolódó tartalom közzététele. Ebben a példában lenne http://yourapp/.

Ha módosítja a elérési útját hivatkozott tartalmat foglaljon magába, de továbbra is a felhasználók számára, hogy az elérési út mélyebb hivatkozás meg kell, lásd a következő blogbejegyzésben [beállítása a megfelelő hivatkozásra, az alkalmazásproxy-alkalmazások az Azure AD hozzáférési panel és az Office 365 alkalmazás indító](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Összekötő hibák

Használja a [az Azure AD Application Proxy Connector portok teszt eszközét](https://aadap-portcheck.connectorporttest.msappproxy.net/) ellenőrzése, hogy az összekötő el lehet-e érni az alkalmazásproxy-szolgáltatás. Minimális győződjön meg arról, hogy a központi US régió és az Önhöz legközelebbi régiót összes zöld jelölők. Túl további zöld jelölők azt jelenti, hogy nagyobb rugalmasság. 

Regisztrációs varázsló Connector telepítése közben meghiúsul, ha két módon lehet megtekinteni a hiba okát. Vagy tekintse meg az eseménynaplóban **alkalmazások és szolgáltatások Logs\Microsoft\AadApplicationProxy\Connector\Admin**, vagy futtassa a következő Windows PowerShell-parancsot:

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

Miután megtalálta az összekötő hibát az eseménynaplóban, használja a táblázat előforduló hibákat a probléma megoldásához:

| Hiba | Ajánlott lépések |
| ----- | ----------------- |
| Összekötő regisztrálása nem sikerült: Ellenőrizze, hogy engedélyezte az Azure felügyeleti portálon Proxy és a megadott megfelelően az Active Directory-felhasználónevet és jelszót. Hiba: "egy vagy több hiba történt." | Ha korábban bezárta a regisztrációs ablakban az Azure AD bejelentkezés nélkül, futtassa újra az összekötő varázslót, és regisztrálja az összekötőt. <br><br> A regisztrációs ablak nyílik meg, és azonnal bezárása után, de nem engedélyezi, hogy jelentkezzen be, ha a hiba valószínűleg jelenik meg. Ez a hiba akkor fordul elő, amikor egy hálózati hiba van a rendszeren. Győződjön meg arról, hogy lehet csatlakozni a böngészőből nyilvános webhelyeken és, hogy a portok: Nyissa meg a [proxyval Előfeltételek](manage-apps/application-proxy-enable.md). |
| Törölje a jelet hiba az ablakban jelenik meg. Nem lehet folytatni | Ha ezt a hibaüzenetet látja, és majd a időszak véget ér, a megadott a rossz felhasználónévvel vagy jelszóval. Próbálja meg újra. |
| Összekötő regisztrálása nem sikerült: Ellenőrizze, hogy engedélyezte az Azure felügyeleti portálon Proxy és a megadott megfelelően az Active Directory-felhasználónevet és jelszót. Hiba: "AADSTS50059: bérlői azonosító adatokat található vagy a kérelem, vagy bármely hallgatólagos megadott hitelesítő adatok és a keresési szolgáltatás által egyszerű URI sikertelen volt. | Jelentkezzen be Microsoft-Account és a nem tartományhoz, amely része annak a könyvtárnak próbál hozzáférni a szervezet azonosítója kívánt. Győződjön meg arról, hogy a rendszergazda a tartomány neve megegyezik a bérlői tartomány része, például, ha az Azure AD-tartomány pedig contoso.com, a rendszergazdának kell lennie admin@contoso.com. |
| Nem sikerült beolvasni a jelenlegi, PowerShell-parancsfájlok futtatására vonatkozó végrehajtási házirend. | Ha az összekötő telepítése nem sikerül, ellenőrizze, hogy győződjön meg arról, hogy a PowerShell végrehajtási házirend nincs letiltva. <br><br>1. Nyissa meg a Helyicsoportházirend-szerkesztő.<br>2. Ugrás a **számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők**  >   **A Windows PowerShell** duplán **kapcsolja be a parancsfájl végrehajtása**.<br>3. A végrehajtási házirendet is megadni **nincs konfigurálva** vagy **engedélyezve**. Ha beállítása **engedélyezve**, győződjön meg arról, hogy a beállítások, a végrehajtási házirend beállítása értékre **engedélyezése a helyi és távoli aláírt parancsfájlok** vagy **összes parancsfájlok**. |
| Összekötő nem tudta letölteni a konfigurációt. | Az összekötő ügyféltanúsítványt, a hitelesítéshez használt, lejárt. Ez akkor is előfordulhat, ha az összekötő telepítve, a rendszer proxy mögött. Ebben az esetben az összekötő nem tud hozzáférni az internethez, és nem lesz képes biztosítani a távoli felhasználók számára az alkalmazásoknak. Manuálisan megújítani a `Register-AppProxyConnector` a Windows PowerShell parancsmag. Az összekötő a rendszer proxy mögött van, esetén meg kell adni az Internet-hozzáférést a Connector fiókokat "hálózati szolgáltatás" és "helyi rendszer". Ehhez által biztosított hozzáférés a proxyt vagy úgy, hogy megkerülje a proxyt. |
| Összekötő regisztrálása nem sikerült: Győződjön meg arról, hogy az összekötő regisztrálására az Active Directory globális rendszergazdai jogosultságokkal. Hiba: "a regisztrációs kérelem visszautasítva." | A megszüntetni kívánt jelentkezzen be az alias nem található a tartomány rendszergazdája. Az összekötő mindig telepítve van a könyvtárra, a felhasználó tartománnyal rendelkezik. Győződjön meg arról, hogy a rendszergazdai fiókot próbál bejelentkezni az Azure AD-bérlő globális engedélyeket. |

## <a name="kerberos-errors"></a>Kerberos-hibák

Ez a táblázat ismerteti a gyakori hibákat, és konfigurálja a Kerberos érkező, és megoldási javaslatokat is.

| Hiba | Ajánlott lépések |
| ----- | ----------------- |
| Nem sikerült beolvasni a jelenlegi, PowerShell-parancsfájlok futtatására vonatkozó végrehajtási házirend. | Ha az összekötő telepítése nem sikerül, ellenőrizze, hogy győződjön meg arról, hogy a PowerShell végrehajtási házirend nincs letiltva.<br><br>1. Nyissa meg a Helyicsoportházirend-szerkesztő.<br>2. Ugrás a **számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők**  >   **A Windows PowerShell** duplán **kapcsolja be a parancsfájl végrehajtása**.<br>3. A végrehajtási házirendet is megadni **nincs konfigurálva** vagy **engedélyezve**. Ha beállítása **engedélyezve**, győződjön meg arról, hogy a beállítások, a végrehajtási házirend beállítása értékre **engedélyezése a helyi és távoli aláírt parancsfájlok** vagy **összes parancsfájlok**. |
| 12008 - az azure AD és a háttérkiszolgáló engedélyezett Kerberos hitelesítési kísérletek maximális száma túllépve. | Ez a hiba arra utalhat az Azure AD közötti helytelen konfiguráció és a háttérkiszolgáló, vagy mindkét gépen dátumával és időpontjával konfigurációs problémát. A háttérkiszolgáló elutasította az Azure AD által létrehozott Kerberos jegyet. Győződjön meg arról, hogy az Azure AD és a háttér-alkalmazáskiszolgáló megfelelően van konfigurálva. Győződjön meg arról, hogy az Azure ad-val dátumával és időpontjával konfigurációs és a háttérkiszolgáló alkalmazás szinkronizálja a rendszer. |
| 13016 – az azure AD nem olvashatók be a következőt, mert nincs UPN a biztonsági jogkivonatot, vagy a hozzáférés cookie-ban van a Kerberos jegyet a felhasználó nevében. | Az STS-konfigurációval probléma merül fel. Hárítsa el a UPN jogcím konfigurálása az STS. |
| 13019 – az azure AD a következő általános API-hiba miatt nem olvashatók be a Kerberos jegyet a felhasználó nevében. | Ez az esemény jelezheti, hogy az Azure AD közötti helytelen konfiguráció és a tartományvezérlő kiszolgálót vagy mindkét gépen dátumával és időpontjával konfigurációs problémát. A tartományvezérlő visszautasította a Kerberos-jegy hozta létre az Azure AD. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló alkalmazás konfigurációja helytelen, különösen az SPN konfigurációs. Ellenőrizze, hogy az Azure AD tartományhoz, a tartományvezérlő győződjön meg arról, hogy a tartományvezérlő megbízhatósági kapcsolatot hoz létre az Azure ad-vel azonos tartományhoz. Győződjön meg arról, hogy az Azure ad-val dátumával és időpontjával konfigurációs és a tartományvezérlő szinkronizálja a rendszer. |
| 13020 – az azure AD nem olvashatók be a következőt, mert a háttérkiszolgáló SPN neve nincs megadva a Kerberos jegyet a felhasználó nevében. | Ez az esemény jelezheti, hogy az Azure AD közötti helytelen konfiguráció és a tartományvezérlő kiszolgálót vagy mindkét gépen dátumával és időpontjával konfigurációs problémát. A tartományvezérlő visszautasította a Kerberos-jegy hozta létre az Azure AD. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló alkalmazás konfigurációja helytelen, különösen az SPN konfigurációs. Ellenőrizze, hogy az Azure AD tartományhoz, a tartományvezérlő győződjön meg arról, hogy a tartományvezérlő megbízhatósági kapcsolatot hoz létre az Azure ad-vel azonos tartományhoz. Győződjön meg arról, hogy az Azure ad-val dátumával és időpontjával konfigurációs és a tartományvezérlő szinkronizálja a rendszer. |
| 13022 – az azure AD nem lehet hitelesíteni a felhasználót, mert a háttérkiszolgáló válaszol-e egy HTTP 401 hiba: a Kerberos hitelesítési kísérleteket. | Ez az esemény jelezheti, hogy az Azure AD közötti helytelen konfiguráció és a háttérkiszolgáló, vagy mindkét gépen dátumával és időpontjával konfigurációs problémát. A háttérkiszolgáló elutasította az Azure AD által létrehozott Kerberos jegyet. Győződjön meg arról, hogy az Azure AD és a háttér-alkalmazáskiszolgáló megfelelően van konfigurálva. Győződjön meg arról, hogy az Azure ad-val dátumával és időpontjával konfigurációs és a háttérkiszolgáló alkalmazás szinkronizálja a rendszer. További információkért lásd: [hibaelhárítása Kerberos által korlátozott delegálás konfigurációi alkalmazásproxy](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>A végfelhasználói hibák

Ez a lista azokat a hibákat, a végfelhasználók akkor léphetnek fel, ha próbálnak hozzáférni az alkalmazáshoz, és sikertelen lesz. 

| Hiba | Ajánlott lépések |
| ----- | ----------------- |
| A webhely a lap nem jeleníthető meg. | A felhasználó előfordulhat, hogy ez a hibaüzenet az alkalmazást, ha az alkalmazás integrált Windows-Hitelesítést alkalmazás közzétett elérésére tett kísérlet során. A megadott egyszerű Szolgáltatásnevet az alkalmazás helytelen lehet. Integrált Windows-Hitelesítést alkalmazások esetén győződjön meg róla, hogy az egyszerű Szolgáltatásnevet az alkalmazáshoz beállított helyes. |
| A webhely a lap nem jeleníthető meg. | A felhasználó előfordulhat, hogy ez a hibaüzenet az alkalmazást, ha az alkalmazás az OWA alkalmazás közzétett elérésére tett kísérlet során. Ennek oka lehet a következők egyikét:<br><li>A megadott egyszerű Szolgáltatásnevet az alkalmazás nem megfelelő. Győződjön meg arról, hogy helyesen-e az egyszerű Szolgáltatásnevet az alkalmazáshoz beállított.</li><li>A felhasználó megpróbált hozzáférni az alkalmazáshoz használja a megfelelő vállalati fiók helyett a Microsoft-fiókkal való bejelentkezéshez, vagy egy Vendég felhasználó. Győződjön meg arról, hogy a felhasználó a vállalati fiók, amely megfelel a tartomány a közzétett alkalmazás használatával jelentkezik be. Microsoft Account felhasználók és a vendég nem hozzáférhessenek az integrált Windows-Hitelesítést alkalmazásokhoz.</li><li>A felhasználó megpróbált hozzáférni az alkalmazáshoz megfelelő nincs definiálva a helyszíni oldalon az alkalmazáshoz. Győződjön meg arról, hogy az a felhasználó a megfelelő engedélyekkel rendelkezik a helyszíni gépen háttér alkalmazás meghatározottak szerint. |
| A vállalati alkalmazás nem érhető el. Ön nem jogosult az alkalmazás eléréséhez. A hitelesítés sikertelen. Győződjön meg arról, hogy a felhasználó hozzáférést rendel az alkalmazás. | A felhasználók előfordulhat, hogy ez a hibaüzenet a Ha használják a Microsoft-fiókok helyett a vállalati fiók jelentkezzen be a közzétett alkalmazás elérésére tett kísérlet során. Vendégfelhasználók is kaphat ezt a hibát. Microsoft Account felhasználók és a vendégek nem hozzáférhessenek az integrált Windows-Hitelesítést alkalmazásokhoz. Győződjön meg arról, hogy a felhasználó a vállalati fiók, amely megfelel a tartomány a közzétett alkalmazás használatával jelentkezik be.<br><br>Előfordulhat, hogy nem rendelt hozzá a felhasználót az alkalmazáshoz. Lépjen a **alkalmazás** fülre, majd a **felhasználók és csoportok**, a felhasználó vagy felhasználói csoport hozzárendelése ezt az alkalmazást. |
| A vállalati alkalmazás most nem lehet hozzáférni. Próbálkozzon újra később... Az összekötő túllépte az időkorlátot. | A felhasználók előfordulhat, hogy ez a hibaüzenet az alkalmazást, ha vannak nincs megfelelően definiálva ehhez az alkalmazáshoz a helyszíni oldalon közzétett elérésére tett kísérlet során. Győződjön meg arról, hogy a felhasználók rendelkeznek-e megfelelő engedélyekkel, meghatározottak szerint a háttéralkalmazás a helyszíni gépen. |
| A vállalati alkalmazás nem érhető el. Ön nem jogosult az alkalmazás eléréséhez. A hitelesítés sikertelen. Győződjön meg arról, hogy a felhasználó rendelkezik-e az Azure Active Directory prémium vagy alapszintű kiadásra egy licencet. | A felhasználók előfordulhat, hogy ez a hibaüzenet az alkalmazást, ha azok nem explicit módon hozzárendelt prémium vagy alapszintű licenccel rendelkező rendszergazda az előfizető által közzétett elérésére tett kísérlet során. Nyissa meg az Active Directory az előfizető **licencek** lapra, és győződjön meg arról, hogy a felhasználó vagy felhasználói csoport van hozzárendelve a prémium vagy alapszintű kiadásra licenc. |

## <a name="my-error-wasnt-listed-here"></a>A hiba nem az itt felsorolt

Ha egy hiba vagy probléma az Azure AD-alkalmazásproxy, amely nem szerepel az hibaelhárítási útmutató, szeretnénk kapcsolatos érkezett válasz. E-mail küldése a [visszajelzés team](mailto:aadapfeedback@microsoft.com) a hiba részleteit.

## <a name="see-also"></a>Lásd még
* [Az Azure Active Directory alkalmazásproxy engedélyezése](manage-apps/application-proxy-enable.md)
* [Alkalmazások közzététele az alkalmazásproxy](manage-apps/application-proxy-publish-azure-portal.md)
* [Egyszeri bejelentkezés engedélyezése](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés engedélyezése](manage-apps/application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
