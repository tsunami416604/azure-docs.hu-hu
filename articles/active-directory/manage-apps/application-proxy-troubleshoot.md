---
title: Az alkalmazásproxy hibaelhárítása |} A Microsoft Docs
description: Ismerteti az Azure AD-alkalmazásproxy kapcsolatos hibák elhárítása.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375711"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Az alkalmazásproxy-problémák és hibaüzenetek hibaelhárítása

Az alkalmazásproxy hibáinak elhárítása során javasoljuk, hogy kezdje meg a hibaelhárítási folyamat áttekintését, az [alkalmazásproxy-összekötő](application-proxy-debug-connectors.md)hibáinak elhárítását, és állapítsa meg, hogy az alkalmazásproxy-összekötők megfelelően vannak-e konfigurálva. Ha továbbra is problémát tapasztal az alkalmazáshoz való csatlakozás során, kövesse az [alkalmazásproxy alkalmazásával kapcsolatos hibák](application-proxy-debug-apps.md)elhárítása című témakört.

Ha hiba lép fel, a közzétett alkalmazás eléréséhez, vagy az alkalmazások közzétételéhez, ellenőrizze a megtekintéséhez, hogy a Microsoft Azure AD-alkalmazásproxy megfelelően működik-e a következő beállításokat:

* Nyissa meg a Windows-szolgáltatások konzolt. Ellenőrizze, hogy a **Microsoft HRE Application proxy Connector** szolgáltatás engedélyezve van-e és fut-e. Emellett érdemes és az alkalmazásproxy szolgáltatás tulajdonságai lapon tekintse meg az alábbi képen látható módon:  
  ![a Microsoft HRE Application proxy Connector Tulajdonságok ablak képernyőképet](./media/application-proxy-troubleshoot/connectorproperties.png)
* Nyissa meg Eseménynapló és keresse meg az Application proxy-összekötő eseményeit az **alkalmazások és szolgáltatások naplóiban** > **Microsoft** > **AadApplicationProxy** > - **összekötő** > **rendszergazdája**.
* Ha szükséges, részletesebb naplók érhetők el [az alkalmazásproxy-összekötő munkamenet-naplófájljainak bekapcsolásával](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>A lap nem jelenik meg megfelelően
Előfordulhat, hogy az alkalmazás megjelenítése vagy nem megfelelően működik-e meghatározott hiba üzenetek fogadása nélkül. Ez akkor fordulhat elő, ha a közzétett cikk elérési, de kívül adott elérési úton található tartalom szükséges az alkalmazás számára.

Tegyük fel például, hogy az elérési utat `https://yourapp/app`, de az alkalmazás meghívja a képeket a `https://yourapp/media`ban, nem lesznek megjelenítve. Győződjön meg arról, hogy közzéteszi az alkalmazást a legmagasabb szintű elérési útja, meg kell adni az összes kapcsolódó tartalom alapján. Ebben a példában a `http://yourapp/`.

Ha úgy módosítja az elérési utat, hogy tartalmazza a hivatkozott tartalmat, de továbbra is szükség van arra, hogy a felhasználók az elérési úton mélyebb kapcsolaton keresztül férhessenek hozzá, tekintse meg a blogbejegyzés az [Application proxy alkalmazásokhoz való megfelelő hivatkozását az Azure ad hozzáférési paneljén és az Office 365 app launcherben](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Összekötő-hibák

Regisztrációs összekötő varázsló telepítése közben meghiúsul, ha két módon megtekintéséhez a hiba okát. Keresse meg az eseménynaplót az **alkalmazások és szolgáltatások Logs\Microsoft\AadApplicationProxy\Connector\Admin**alatt, vagy futtassa a következő Windows PowerShell-parancsot:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Amikor a Összekötőjével kapcsolatos hiba az esemény eseménynaplóból való megkereséséhez használja ez a táblázat a gyakori hibák a probléma megoldásához:

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| Összekötő regisztrálása nem sikerült: Ellenőrizze, hogy engedélyezte az alkalmazásproxy az Azure felügyeleti portálon és a megadott megfelelően az Active Directory felhasználónevet és jelszót. Hiba: "egy vagy több hiba történt." | Ha már bezárta a regisztrációs ablak az Azure AD-bejelentkezés nélkül, futtassa újra az összekötő varázslót, és az összekötő regisztrálására. <br><br> Ha megnyílik a regisztrációs ablak, majd azonnal bezárul anélkül, hogy bejelentkezne, valószínűleg ezt a hibát fogja kapni. Ez a hiba akkor fordul elő, ha hálózati hiba van a rendszeren. Győződjön meg arról, hogy a böngészőből nyilvános webhelyre lehet csatlakozni, és hogy a portok az [alkalmazásproxy előfeltételeiben](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)megadott módon vannak megnyitva. |
| Az ablakban törölje hiba jelenik meg. Nem lehet folytatni | Ha ezt a hibaüzenetet, és ezután az ablak bezárul, a rossz felhasználónévvel vagy jelszóval adott meg. Próbálja meg újra. |
| Összekötő regisztrálása nem sikerült: Ellenőrizze, hogy engedélyezte az alkalmazásproxy az Azure felügyeleti portálon és a megadott megfelelően az Active Directory felhasználónevet és jelszót. Hiba: "AADSTS50059: információ a bérlő-azonosító nem található vagy a kérelem vagy hallgatólagos bármelyik megadott hitelesítő adatok és a search szolgáltatás által egyszerű URI nem sikerült. | Microsoft-fiókkal próbál bejelentkezni, és nem egy olyan tartományba, amely tagja annak a címtárnak, amelyhez hozzáférni próbál. Győződjön meg arról, hogy a rendszergazda a bérlő tartományának azonos tartománynevéhez tartozik, például ha az Azure AD-tartomány contoso.com, a rendszergazdának admin@contoso.comkell lennie. |
| Nem sikerült beolvasni az aktuális végrehajtási szabályzata a PowerShell-parancsfájlok futtatásakor. | Ha az összekötő telepítése sikertelen, ellenőrizze, hogy nincs-e letiltva a PowerShell végrehajtási házirendje. <br><br>1. Nyissa meg a Csoportházirend szerkesztőt.<br>2. Lépjen a **Számítógép konfigurációja** > **Felügyeleti sablonok** > **Windows-összetevők** > **Windows PowerShell** elemre, és kattintson duplán **a parancsfájlok végrehajtásának bekapcsolása**lehetőségre.<br>3. a végrehajtási házirend beállítható úgy, hogy **nincs konfigurálva** vagy **engedélyezve**. Ha az **engedélyezve**értékre van állítva, győződjön meg arról, hogy a beállítások területen a végrehajtási házirend a **helyi parancsfájlok és a távoli aláírású parancsfájlok engedélyezése** vagy az **összes parancsfájl engedélyezése**beállítást adja meg. |
| Összekötő nem tudta letölteni a konfigurációt. | Az összekötő ügyféltanúsítványt, a hitelesítéshez használt, lejárt. Ez akkor is előfordulhat, ha az összekötő telepítve van, a rendszer proxy mögött van. Ebben az esetben az összekötő nem férnek hozzá az internethez, és nem lesz képes biztosítani a távoli felhasználók számára az alkalmazásoknak. A Windows PowerShell `Register-AppProxyConnector` parancsmagjának használatával manuálisan megújíthatja a megbízhatósági kapcsolatot. Ha az összekötő egy proxykiszolgáló mögött található, akkor meg kell adni a "hálózati szolgáltatás" összekötő fiókok Internet-hozzáférés és a "helyi rendszer." Ez is elvégezhető, ehhez biztosítson hozzáférést a Proxy vagy állítsa őket a proxyt. |
| Az összekötő regisztrálása nem sikerült: Győződjön meg arról, hogy a Active Directory alkalmazás-rendszergazdája az összekötő regisztrálásához. Hiba: "a szolgáltatásregisztrálási kérelem megtagadva." | Bejelentkezés a következővel kívánt alias nem rendszergazdája az ebben a tartományban. Az összekötő mindig telepítve van a címtárból, amelyhez a felhasználó tartománnyal rendelkezik. Győződjön meg arról, hogy a bejelentkezni próbáló rendszergazdai fiókja atleast alkalmazás-rendszergazdai jogosultságokkal rendelkezik az Azure AD-bérlőhöz. |
| Az összekötő hálózati problémák miatt nem tudott kapcsolódni a szolgáltatáshoz. Az összekötő megpróbálta elérni a következő URL-címet. | Az összekötő nem tud csatlakozni az alkalmazásproxy Cloud Service-hez. Ez akkor fordulhat elő, ha egy tűzfalszabály blokkolja a kapcsolatokat. Győződjön meg arról, hogy engedélyezett a megfelelő portok és URL-címek elérése az [alkalmazásproxy előfeltételeiben](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Kerberos-hibák

Ez a táblázat a leggyakoribb hibák, amelyek a Kerberos-beállítás és konfiguráció származnak, és megoldási javaslatokat is kínál.

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| Nem sikerült beolvasni az aktuális végrehajtási szabályzata a PowerShell-parancsfájlok futtatásakor. | Ha az összekötő telepítése nem sikerül, ellenőrizze, győződjön meg arról, hogy a PowerShell végrehajtási házirend nincs letiltva.<br><br>1. Nyissa meg a Csoportházirend szerkesztőt.<br>2. Lépjen a **Számítógép konfigurációja** > **Felügyeleti sablonok** > **Windows-összetevők** > **Windows PowerShell** elemre, és kattintson duplán **a parancsfájlok végrehajtásának bekapcsolása**lehetőségre.<br>3. a végrehajtási házirend beállítható úgy, hogy **nincs konfigurálva** vagy **engedélyezve**. Ha az **engedélyezve**értékre van állítva, győződjön meg arról, hogy a beállítások területen a végrehajtási házirend a **helyi parancsfájlok és a távoli aláírású parancsfájlok engedélyezése** vagy az **összes parancsfájl engedélyezése**beállítást adja meg. |
| 12008 - azure ad-ben túllépte az engedélyezett Kerberos hitelesítési kísérleteket a háttérkiszolgálóra maximális számát. | Ez a hiba arra utalhat hibás konfiguráció az Azure AD között és a háttérkiszolgáló, vagy mindkét gépen dátum és idő konfigurációs problémát. A háttérkiszolgáló elutasította a Kerberos-jegyet az Azure AD által létrehozott. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló megfelelően van konfigurálva. Győződjön meg arról, hogy a dátum és idő konfiguráció az Azure AD és a háttérkiszolgáló szinkronizálódnak. |
| 13016 – az azure AD nem sikerült beolvasni a Kerberos jegyet a felhasználó nevében, mert nem tartalmaz egyszerű a biztonsági jogkivonatot, vagy a hozzáférés cookie-ban. | Az STS-konfigurációval probléma merül fel. Javítsa ki az egyszerű Felhasználónévi jogcím konfigurálása az STS-re. |
| 13019 – azure ad-ben a következő általános API-hiba miatt nem sikerült beolvasni a Kerberos jegyet a felhasználó nevében. | Ez az esemény azt jelezheti helytelen konfiguráció az Azure AD között, és a tartományvezérlő kiszolgálót vagy mindkét gépen dátum és idő konfigurációs problémát. A tartományvezérlő a Kerberos-jegyet az Azure AD által létrehozott elutasította. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló beállítása megfelelő-e, különösen az egyszerű szolgáltatásnév konfigurációja. Ellenőrizze, hogy az Azure AD tartományhoz csatlakoztatva, a tartományvezérlő, győződjön meg arról, hogy a tartományvezérlő megbízhatósági kapcsolatot hoz létre az Azure ad-vel azonos tartományban. Győződjön meg arról, hogy a dátum és idő konfiguráció az Azure AD és a rendszer szinkronizálja a tartományvezérlőn. |
| 13020 – azure ad-ben nem olvashatók be a felhasználó nevében egy Kerberos-jegyet, mert a háttérkiszolgáló SPN neve nincs megadva. | Ez az esemény azt jelezheti helytelen konfiguráció az Azure AD között, és a tartományvezérlő kiszolgálót vagy mindkét gépen dátum és idő konfigurációs problémát. A tartományvezérlő a Kerberos-jegyet az Azure AD által létrehozott elutasította. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló beállítása megfelelő-e, különösen az egyszerű szolgáltatásnév konfigurációja. Ellenőrizze, hogy az Azure AD tartományhoz csatlakoztatva, a tartományvezérlő, győződjön meg arról, hogy a tartományvezérlő megbízhatósági kapcsolatot hoz létre az Azure ad-vel azonos tartományban. Győződjön meg arról, hogy a dátum és idő konfiguráció az Azure AD és a rendszer szinkronizálja a tartományvezérlőn. |
| 13022 – az azure AD nem tudja hitelesíteni a felhasználót, mert a háttérkiszolgáló válaszol egy HTTP 401-es hiba miatt a Kerberos hitelesítési kísérleteket. | Ez az esemény azt jelezheti helytelen konfiguráció az Azure AD között és a háttérkiszolgáló, vagy mindkét gépen dátum és idő konfigurációs problémát. A háttérkiszolgáló elutasította a Kerberos-jegyet az Azure AD által létrehozott. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló megfelelően van konfigurálva. Győződjön meg arról, hogy a dátum és idő konfiguráció az Azure AD és a háttérkiszolgáló szinkronizálódnak. További információ: [a Kerberos által korlátozott delegálási konfigurációk hibaelhárítása az alkalmazásproxy számára](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>A végfelhasználói hibák

Ez a lista azokat a hibákat, a végfelhasználók akkor fordulhatnak elő, amikor megpróbálják elérni az alkalmazást, és nem. 

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| A webhelyen az oldal nem jeleníthető meg. | A felhasználó előfordulhat, hogy ez a hibaüzenet, ha az alkalmazás IWA alkalmazás közzé az alkalmazás elérésére tett kísérlet során. Lehet, hogy helytelen a megadott egyszerű szolgáltatásnév ehhez az alkalmazáshoz. IWA alkalmazások esetében ellenőrizze, hogy helyesen-e az egyszerű szolgáltatásnév ehhez az alkalmazáshoz konfigurált. |
| A webhelyen az oldal nem jeleníthető meg. | A felhasználó lehet, hogy ez a hibaüzenet, ha az alkalmazás az OWA alkalmazás közzé az alkalmazás elérésére tett kísérlet során. Ennek egyik lehetséges oka a következők egyikét:<br><li>Helytelen a megadott egyszerű szolgáltatásnév ehhez az alkalmazáshoz. Győződjön meg arról, hogy helyesen szerepel-e az egyszerű szolgáltatásnév ehhez az alkalmazáshoz konfigurált.</li><li>A felhasználó megpróbált hozzáférni az alkalmazáshoz az Microsoft-fiókja helyett a megfelelő vállalati fióknak való bejelentkezéshez, vagy a felhasználó vendégfelhasználó. Ellenőrizze, hogy a felhasználó bejelentkezik, amely megfelel a tartománynak a közzétett alkalmazás vállalati fiókjával. A Microsoft Account felhasználók és a Vendég IWA alkalmazások férhetnek hozzá.</li><li>Az alkalmazáshoz hozzáférni próbáló felhasználó nincs megfelelően definiálva ehhez az alkalmazáshoz a helyszíni oldalon. Győződjön meg arról, hogy a felhasználó rendelkezik a megfelelő engedélyekkel a háttérbeli alkalmazáshoz a helyszíni gépen. |
| A vállalati alkalmazás nem érhető el. Nem jogosult az alkalmazás eléréséhez. A hitelesítés sikertelen. Ellenőrizze, hogy hozzáféréssel rendelkező felhasználó hozzárendelése az alkalmazáshoz. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha a vállalati fiók helyett Microsoft-fiókokat használ a bejelentkezéshez. Vendégfelhasználók is kaphat a hibáról. A Microsoft Account felhasználók és a vendégek IWA alkalmazások férhetnek hozzá. Ellenőrizze, hogy a felhasználó bejelentkezik, amely megfelel a tartománynak a közzétett alkalmazás vállalati fiókjával.<br><br>Előfordulhat, hogy nem rendelte a felhasználót az alkalmazáshoz. Lépjen az **alkalmazás** lapra, és a **felhasználók és csoportok**területen rendelje hozzá ezt a felhasználót vagy felhasználói csoportot ehhez az alkalmazáshoz. |
| A vállalati alkalmazás jelenleg nem érhető el. Próbálkozzon újra később... Az összekötő túllépte az időkorlátot. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha azokat nem megfelelően definiálták ehhez az alkalmazáshoz a helyszíni oldalon. Győződjön meg arról, hogy a felhasználók rendelkeznek a megfelelő engedélyekkel a háttérbeli alkalmazáshoz a helyszíni gépen. |
| A vállalati alkalmazás nem érhető el. Nem jogosult az alkalmazás eléréséhez. A hitelesítés sikertelen. Győződjön meg arról, hogy a felhasználó rendelkezik prémium szintű Azure Active Directory-licenccel. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha az előfizető rendszergazdája kifejezetten nem rendelt hozzájuk prémium licenccel. Lépjen az előfizető Active Directory **licencek** lapra, és győződjön meg arról, hogy a felhasználó vagy a felhasználói csoport prémium szintű licenccel van társítva. |
| Nem található a megadott állomásnévvel rendelkező kiszolgáló. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha az alkalmazás egyéni tartománya nincs megfelelően konfigurálva. Győződjön meg arról, hogy feltöltött egy tanúsítványt a tartományhoz, és megfelelően konfigurálta a DNS-rekordot az [Azure-beli egyéni tartományok használata](application-proxy-configure-custom-domain.md) című témakörben ismertetett lépéseket követve. ad Application proxy |

## <a name="my-error-wasnt-listed-here"></a>A hiba itt nem látható

Ha egy hiba vagy nem szerepel a listán a hibaelhárítási útmutató az Azure AD-alkalmazásproxy kapcsolatos probléma merül fel, szeretnénk hallani azt. Küldjön egy e-mailt a [visszajelzési csapatnak](mailto:aadapfeedback@microsoft.com) az észlelt hiba részleteivel.

## <a name="see-also"></a>Lásd még
* [Alkalmazásproxy engedélyezése Azure Active Directory számára](application-proxy-add-on-premises-application.md)
* [Alkalmazások közzététele az Application proxyval](application-proxy-add-on-premises-application.md)
* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
