---
title: Alkalmazásproxy hibáinak elhárítása | Microsoft dokumentumok
description: Az Azure AD alkalmazásproxy hibáinak elhárítása.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017; it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7be9a17bed2a39d16f813332c2d6effc03393264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244224"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása

Az alkalmazásproxyval kapcsolatos problémák elhárításakor azt javasoljuk, hogy kezdje el áttekinteni a hibaelhárítási folyamat [hibakeresési alkalmazásproxy-összekötővel kapcsolatos problémáit,](application-proxy-debug-connectors.md)és állapítsa meg, hogy az alkalmazásproxy-összekötők megfelelően vannak-e konfigurálva. Ha továbbra is problémái vannak az alkalmazáshoz való csatlakozással, kövesse a [Hibakeresési alkalmazásproxy alkalmazásokkal kapcsolatos problémák](application-proxy-debug-apps.md)hibaelhárítási folyamatát.

Ha hibák lépnek fel egy közzétett alkalmazás elérésekor vagy a közzétételi alkalmazásokban, ellenőrizze az alábbi lehetőségeket, és ellenőrizze, hogy a Microsoft Azure AD alkalmazásproxy megfelelően működik-e:

* Nyissa meg a Windows-szolgáltatások konzolt. Ellenőrizze, hogy a **Microsoft AAD Application Proxy Connector** szolgáltatás engedélyezve van-e és fut-e. Az alkalmazásproxy szolgáltatás tulajdonságai lapot is meg szeretné tekinteni, ahogy az az alábbi képen látható:  
  ![A Microsoft AAD alkalmazásproxy-összekötő tulajdonságai ablak képernyőképe](./media/application-proxy-troubleshoot/connectorproperties.png)
* Nyissa meg az Eseménynaplót, és keresse meg az Alkalmazásproxy-összekötő eseményeit a**Microsoft** > **AadApplicationProxy** > **Connector** > **Admin** **naplóiban.** > 
* Szükség esetén részletesebb naplók érhetők el [az Alkalmazásproxy-összekötő munkamenetnaplóinak bekapcsolásával.](application-proxy-connectors.md#under-the-hood)

## <a name="the-page-is-not-rendered-correctly"></a>Az oldal nem jelenik meg megfelelően
Előfordulhat, hogy az alkalmazás helytelen vagy megfelelő működése nem tartalmaz konkrét hibaüzeneteket. Ez akkor fordulhat elő, ha közzétette a cikk elérési útját, de az alkalmazásnak olyan tartalomra van szüksége, amely az adott útvonalon kívül található.

Ha például közzéteszi az `https://yourapp/app` elérési utat, `https://yourapp/media`de az alkalmazás meghívja a képeket, azok nem jelennek meg. Győződjön meg arról, hogy az alkalmazást az összes releváns tartalom felvételéhez szükséges legmagasabb szintű elérési úttal teszi közzé. Ebben a példában `http://yourapp/`ez lenne .

Ha módosítja az elérési utat, hogy tartalmazza a hivatkozott tartalmat, de továbbra is szükség van a felhasználók számára, hogy egy mélyebb linkre érkezzen az elérési úton, olvassa el a [blogbejegyzést Az Alkalmazásproxy-alkalmazások megfelelő hivatkozásának beállítása az Azure AD hozzáférési panelen és az Office 365 alkalmazásindítójában.](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/)

## <a name="connector-errors"></a>Összekötő hibák

Ha a regisztráció sikertelen az Összekötő varázsló telepítése során, kétféleképpen tekintheti meg a hiba okát. Keresse meg az eseménynaplót az **Alkalmazások és szolgáltatások naplójában\Microsoft\AadApplicationProxy\Connector\Admin,** vagy futtassa a következő Windows PowerShell parancsot:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Miután megtalálta az összekötő hibát az eseménynaplóból, használja ezt a gyakori hibákat jelző táblázatot a probléma megoldásához:

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| Az összekötő regisztrációja nem sikerült: Győződjön meg arról, hogy engedélyezte az alkalmazásproxyt az Azure Management Portalon, és helyesen adta meg az Active Directory felhasználónevét és jelszavát. Hiba: "Egy vagy több hiba történt." | Ha bezárta a regisztrációs ablakot anélkül, hogy bejelentkezne az Azure AD-be, futtassa újra az összekötő varázslót, és regisztrálja az összekötőt. <br><br> Ha a regisztrációs ablak megnyílik, majd azonnal bezárul anélkül, hogy lehetővé teszi, hogy jelentkezzen be, akkor valószínűleg kap ez a hiba. Ez a hiba akkor fordul elő, ha hálózati hiba van a rendszerben. Győződjön meg arról, hogy a böngészőből egy nyilvános webhelyhez lehet csatlakozni, és hogy a portok meg vannak nyitva az [alkalmazásproxy előfeltételeiben megadottak szerint.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) |
| A regisztrációs ablakban egyértelmű hiba jelenik meg. Nem lehet folytatni | Ha ezt a hibaüzenetet látja, majd az ablak bezárul, rossz felhasználónevet vagy jelszót adott meg. próbáld újra. |
| Az összekötő regisztrációja nem sikerült: Győződjön meg arról, hogy engedélyezte az alkalmazásproxyt az Azure Management Portalon, és helyesen adta meg az Active Directory felhasználónevét és jelszavát. Hiba: "AADSTS50059: Nem sikerült a megadott hitelesítő adatok és a szolgáltatás egyszerű URI által végzett keresés által megadott bérlő-azonosító adatok. | Microsoft-fiókkal próbál bejelentkezni, nem pedig olyan tartománnyal, amely az elérni kívánt címtár szervezeti azonosítójának része. Győződjön meg arról, hogy a rendszergazda ugyanannak a tartománynévnek a része, mint a admin@contoso.combérlői tartománynak, például ha az Azure AD-tartomány contoso.com, a rendszergazdának kell lennie. |
| Nem sikerült beolvasni a PowerShell-parancsfájlok jelenlegi végrehajtási házirendjeit. | Ha az összekötő telepítése sikertelen, ellenőrizze, hogy a PowerShell végrehajtási szabályzata nincs-e letiltva. <br><br>1. Nyissa meg a Csoportházirend-szerkesztőt.<br>2. Nyissa meg **a Számítógép konfigurációs** > **felügyeleti sablonok** > **Windows-összetevők** > **Windows PowerShell** és kattintson duplán Kapcsolja be a **parancsfájl-végrehajtás**.<br>3. A végrehajtási házirend beállítható **Nincs konfigurálva** vagy **engedélyezve**. Ha az **Engedélyezve**érték van, győződjön meg arról, hogy a Beállítások csoportban a Végrehajtási házirend beállítása a **Helyi parancsfájlok és a távoli aláírt parancsfájlok engedélyezése,** illetve az **Összes parancsfájl engedélyezése**beállítás. |
| Az összekötő nem tudta letölteni a konfigurációt. | Az összekötő hitelesítéshez használt ügyféltanúsítványa lejárt. Ez akkor is előfordulhat, ha az összekötő egy proxy mögé van telepítve. Ebben az esetben az összekötő nem tud hozzáférni az internethez, és nem tud alkalmazásokat biztosítani a távoli felhasználók számára. A Windows PowerShell parancsmagjának használatával manuálisan újítsa meg a `Register-AppProxyConnector` megbízhatóságot. Ha az összekötő egy proxy mögött van, internet-hozzáférést kell biztosítania az összekötő fiókok "hálózati szolgáltatások" és a "helyi rendszer" számára. Ez úgy valósítható meg, hogy hozzáférést biztosít számukra a proxyhoz, vagy beállítja őket a proxy megkerülésére. |
| Az összekötő regisztrációja nem sikerült: Győződjön meg arról, hogy az Active Directory alkalmazásrendszergazdája az összekötő regisztrálásához. Hiba: "A regisztrációs kérelem megtagadva." | Az alias, amivel be szeretne jelentkezni, nem rendszergazda ezen a tartományon. Az összekötő mindig telepítve van a felhasználó tartományát birtokló könyvtárhoz. Győződjön meg arról, hogy a rendszergazdai fiók próbál bejelentkezni, legalább alkalmazás-rendszergazdai engedélyekkel rendelkezik az Azure AD-bérlő. |
| Az összekötő hálózati problémák miatt nem tudott csatlakozni a szolgáltatáshoz. Az összekötő a következő URL-címet próbálta elérni. | Az összekötő nem tud csatlakozni az alkalmazásproxy felhőszolgáltatásához. Ez akkor fordulhat elő, ha egy tűzfalszabály blokkolja a kapcsolatot. Győződjön meg arról, hogy engedélyezte a hozzáférést az [alkalmazásproxy előfeltételeiben](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)felsorolt megfelelő portokhoz és URL-címekhez. |

## <a name="kerberos-errors"></a>Kerberos-hibák

Ez a táblázat a Kerberos beállításából és konfigurálásából származó leggyakoribb hibákat ismerteti, és javaslatokat tartalmaz a megoldásra.

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| Nem sikerült beolvasni a PowerShell-parancsfájlok jelenlegi végrehajtási házirendjeit. | Ha az összekötő telepítése sikertelen, ellenőrizze, hogy a PowerShell végrehajtási házirendje nincs-e letiltva.<br><br>1. Nyissa meg a Csoportházirend-szerkesztőt.<br>2. Nyissa meg **a Számítógép konfigurációs** > **felügyeleti sablonok** > **Windows-összetevők** > **Windows PowerShell** és kattintson duplán Kapcsolja be a **parancsfájl-végrehajtás**.<br>3. A végrehajtási házirend beállítható **Nincs konfigurálva** vagy **engedélyezve**. Ha az **Engedélyezve**érték van, győződjön meg arról, hogy a Beállítások csoportban a Végrehajtási házirend beállítása a **Helyi parancsfájlok és a távoli aláírt parancsfájlok engedélyezése,** illetve az **Összes parancsfájl engedélyezése**beállítás. |
| 12008 – Az Azure AD túllépte a háttérkiszolgálón engedélyezett Kerberos-hitelesítési kísérletek maximális számát. | Ez a hiba helytelen konfigurációt jelezhet az Azure AD és a háttéralkalmazás-kiszolgáló között, vagy mindkét gépen idő- és dátumkonfigurációs problémát okozhat. A háttérkiszolgáló elutasította az Azure AD által létrehozott Kerberos-jegyet. Ellenőrizze, hogy az Azure AD és a háttéralkalmazás-kiszolgáló megfelelően van-e konfigurálva. Győződjön meg arról, hogy az Azure AD és a háttéralkalmazás-kiszolgáló idő- és dátumkonfigurációja szinkronizálva van. |
| 13016 – Az Azure AD nem tudja lekérni a Kerberos-jegyet a felhasználó nevében, mert nincs upn a peremhálózati jogkivonatban vagy a hozzáférési cookie-ban. | Probléma van az STS konfigurációval. Javítsa ki az upn jogcímkonfigurációját az STS-ben. |
| 13019 – Az Azure AD a következő általános API-hiba miatt nem tudja beolvasni a Kerberos-jegyet a felhasználó nevében. | Ez az esemény helytelen konfigurációt jelezhet az Azure AD és a tartományvezérlő-kiszolgáló között, vagy mindkét gépen idő- és dátumkonfigurációs problémát okozhat. A tartományvezérlő elutasította az Azure AD által létrehozott Kerberos-jegyet. Ellenőrizze, hogy az Azure AD és a háttéralkalmazás-kiszolgáló megfelelően van-e konfigurálva, különösen az SPN-konfiguráció. Győződjön meg arról, hogy az Azure AD tartomány ban csatlakozik ugyanahhoz a tartományhoz, mint a tartományvezérlő, hogy a tartományvezérlő bizalmi kapcsolatot létesítaz az Azure AD-vel. Győződjön meg arról, hogy az Azure AD és a tartományvezérlő idő- és dátumkonfigurációja szinkronizálva van. |
| 13020 – Az Azure AD nem tudja beolvasni a Kerberos-jegyet a felhasználó nevében, mert a háttérkiszolgáló SPN nincs definiálva. | Ez az esemény helytelen konfigurációt jelezhet az Azure AD és a tartományvezérlő-kiszolgáló között, vagy mindkét gépen idő- és dátumkonfigurációs problémát okozhat. A tartományvezérlő elutasította az Azure AD által létrehozott Kerberos-jegyet. Ellenőrizze, hogy az Azure AD és a háttéralkalmazás-kiszolgáló megfelelően van-e konfigurálva, különösen az SPN-konfiguráció. Győződjön meg arról, hogy az Azure AD tartomány ban csatlakozik ugyanahhoz a tartományhoz, mint a tartományvezérlő, hogy a tartományvezérlő bizalmi kapcsolatot létesítaz az Azure AD-vel. Győződjön meg arról, hogy az Azure AD és a tartományvezérlő idő- és dátumkonfigurációja szinkronizálva van. |
| 13022 – Az Azure AD nem tudja hitelesíteni a felhasználót, mert a háttérkiszolgáló HTTP 401-es hibával válaszol a Kerberos hitelesítési kísérletére. | Ez az esemény helytelen konfigurációt jelezhet az Azure AD és a háttéralkalmazás-kiszolgáló között, vagy problémát okozhat az idő- és dátumkonfigurációban mindkét gépen. A háttérkiszolgáló elutasította az Azure AD által létrehozott Kerberos-jegyet. Ellenőrizze, hogy az Azure AD és a háttéralkalmazás-kiszolgáló megfelelően van-e konfigurálva. Győződjön meg arról, hogy az Azure AD és a háttéralkalmazás-kiszolgáló idő- és dátumkonfigurációja szinkronizálva van. További információt a [Kerberos korlátozott delegálási konfigurációinak hibaelhárítása az alkalmazásproxyhoz című témakörben talál.](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)  |

## <a name="end-user-errors"></a>Végfelhasználói hibák

Ez a lista azokat a hibákat tartalmazza, amelyekkel a végfelhasználók találkozhatnak, amikor megpróbálnak hozzáférni az alkalmazáshoz, és sikertelenek. 

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| A webhely nem tudja megjeleníteni az oldalt. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha az alkalmazás IWA-alkalmazás. Lehet, hogy az alkalmazáshoz definiált spn helytelen. IWA-alkalmazások esetén győződjön meg arról, hogy az alkalmazáshoz konfigurált spn helyes. |
| A webhely nem tudja megjeleníteni az oldalt. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbálja elérni a közzétett alkalmazást, ha az alkalmazás egy OWA-alkalmazás. Ennek oka az alábbiak egyike lehet:<br><li>Az alkalmazáshoz definiált spn helytelen. Győződjön meg arról, hogy az alkalmazáshoz konfigurált spn helyes.</li><li>Az alkalmazás elérését megkísérbe használó felhasználó a megfelelő vállalati fiók helyett Microsoft-fiókot használ a bejelentkezéshez, vagy a felhasználó vendégfelhasználó. Győződjön meg arról, hogy a felhasználó bejelentkezik a vállalati fiók használatával, amely megfelel a közzétett alkalmazás tartományának. A Microsoft-fiók felhasználói és vendégei nem férhetnek hozzá az IWA-alkalmazásokhoz.</li><li>A felhasználó, aki megpróbálta elérni az alkalmazást nincs megfelelően definiálva az alkalmazás a helyszíni oldalon. Győződjön meg arról, hogy ez a felhasználó rendelkezik a megfelelő engedélyekkel ehhez a háttéralkalmazáshoz a helyszíni gépen. |
| Ez a vállalati alkalmazás nem érhető el. Nincs jogosultsága az alkalmazás eléréséhez. Az engedélyezés nem sikerült. Győződjön meg arról, hogy az alkalmazáshoz hozzáféréssel rendelkező felhasználót rendel hozzá. | Előfordulhat, hogy a felhasználó akkor kapja meg ezt a hibaüzenetet, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha a vállalati fiók helyett Microsoft-fiókokat használnak a bejelentkezéshez. A vendégfelhasználók is kaphatják ezt a hibát. A Microsoft-fiók felhasználói és vendégei nem férhetnek hozzá az IWA-alkalmazásokhoz. Győződjön meg arról, hogy a felhasználó bejelentkezik a vállalati fiók használatával, amely megfelel a közzétett alkalmazás tartományának.<br><br>Lehet, hogy nem rendelte hozzá a felhasználót ehhez az alkalmazáshoz. Nyissa meg az **Alkalmazás** lapot, és a **Felhasználók és csoportok**csoportban rendelje hozzá ezt a felhasználót vagy felhasználói csoportot az alkalmazáshoz. |
| Ez a vállalati alkalmazás jelenleg nem érhető el. Próbálkozzon később... Az összekötő idővel csökkent. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbálja elérni a közzétett alkalmazást, ha azok nincsenek megfelelően definiálva ehhez az alkalmazáshoz a helyszíni oldalon. Győződjön meg arról, hogy a felhasználók rendelkeznek a megfelelő engedélyekkel ehhez a háttéralkalmazáshoz a helyszíni gépen. |
| Ez a vállalati alkalmazás nem érhető el. Nincs jogosultsága az alkalmazás eléréséhez. Az engedélyezés nem sikerült. Győződjön meg arról, hogy a felhasználó rendelkezik az Azure Active Directory Premium licenccel. | A felhasználó akkor kaphatja meg ezt a hibaüzenetet, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha az előfizető rendszergazdája nem rendelte hozzá kifejezetten a Prémium licenchez. Nyissa meg az előfizető Active Directory **licencei** lapot, és győződjön meg arról, hogy ez a felhasználó vagy felhasználói csoport prémium licenccel rendelkezik. |
| Nem található a megadott állomásnévvel rendelkező kiszolgáló. | A felhasználó akkor kaphatja meg ezt a hibaüzenetet, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha az alkalmazás egyéni tartománya nincs megfelelően konfigurálva. Győződjön meg arról, hogy feltöltött egy tanúsítványt a tartományhoz, és megfelelően konfigurálta a DNS-rekordot az Egyéni tartományok használata az [Azure AD alkalmazásproxyban](application-proxy-configure-custom-domain.md) című lépés lépéseit követve. |

## <a name="my-error-wasnt-listed-here"></a>A hiba nem szerepel itt

Ha olyan hibát vagy problémát tapasztal az Azure AD alkalmazásproxyval, amely nem szerepel ebben a hibaelhárítási útmutatóban, szeretnénk hallani róla. Küldjön egy e-mailt [visszajelzési csapatunknak](mailto:aadapfeedback@microsoft.com) a tapasztalt hiba részleteivel.

## <a name="see-also"></a>Lásd még
* [Alkalmazásproxy engedélyezése az Azure Active Directoryhoz](application-proxy-add-on-premises-application.md)
* [Alkalmazások közzététele az alkalmazásproxy használatával](application-proxy-add-on-premises-application.md)
* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
