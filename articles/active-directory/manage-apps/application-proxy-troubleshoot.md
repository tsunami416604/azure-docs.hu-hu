---
title: Az alkalmazásproxy hibaelhárítása |} A Microsoft Docs
description: Ismerteti az Azure AD-alkalmazásproxy kapcsolatos hibák elhárítása.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 8be0e909ea391ed1b66fc78349cc2283d009e8cb
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240375"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Az alkalmazásproxy-problémák és hibaüzenetek hibaelhárítása
Ha hiba lép fel, a közzétett alkalmazás eléréséhez, vagy az alkalmazások közzétételéhez, ellenőrizze a megtekintéséhez, hogy a Microsoft Azure AD-alkalmazásproxy megfelelően működik-e a következő beállításokat:

* A Windows-szolgáltatások konzol megnyitásához, és ellenőrizze, hogy a **Microsoft AAD alkalmazásproxy-összekötő** szolgáltatás engedélyezve van és fut. Emellett érdemes és az alkalmazásproxy szolgáltatás tulajdonságai lapon tekintse meg az alábbi képen látható módon:  
  ![A Microsoft AAD Application Proxy Connector tulajdonságok ablak képernyőképe](./media/application-proxy-troubleshoot/connectorproperties.png)
* Nyissa meg az eseménynaplót, és keresse meg az Application Proxy connector-események **alkalmazások és szolgáltatásnaplók** > **Microsoft** > **AadApplicationProxy**  >  **Összekötő** > **rendszergazdai**.
* Szükség esetén további részletes érhetők el naplók által [bekapcsolása az Application Proxy connector munkamenet naplófájljainak](application-proxy-connectors.md#under-the-hood).

Az Azure AD-hibaelhárítás eszközzel kapcsolatos további információkért lásd: [összekötő hálózatkezelési Előfeltételek ellenőrzése a hibaelhárító eszköz](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites).

## <a name="the-page-is-not-rendered-correctly"></a>A lap nem jelenik meg megfelelően
Előfordulhat, hogy az alkalmazás megjelenítése vagy nem megfelelően működik-e meghatározott hiba üzenetek fogadása nélkül. Ez akkor fordulhat elő, ha a közzétett cikk elérési, de kívül adott elérési úton található tartalom szükséges az alkalmazás számára.

Például, ha közzéteszi az elérési út https://yourapp/app , de az alkalmazás meghívja a lemezképek https://yourapp/media, azok nem jelennek meg. Győződjön meg arról, hogy közzéteszi az alkalmazást a legmagasabb szintű elérési útja, meg kell adni az összes kapcsolódó tartalom alapján. Ebben a példában lenne http://yourapp/.

Ha módosítja a hivatkozott tartalmat foglaljon magába, de továbbra is szüksége van egy mélyebb hivatkozásra az elérési út kerül elérési útját, tekintse meg a következő blogbejegyzésben: [a megfelelő hivatkozás beállítása az Azure ad Application Proxy alkalmazásai hozzáférési panelen és az Office 365 appindítóban](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Összekötő-hibák

Regisztrációs összekötő varázsló telepítése közben meghiúsul, ha két módon megtekintéséhez a hiba okát. Vagy tekintse meg az eseménynaplóban **alkalmazások és szolgáltatások Logs\Microsoft\AadApplicationProxy\Connector\Admin**, vagy futtassa a következő Windows PowerShell-parancsot:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Amikor a Összekötőjével kapcsolatos hiba az esemény eseménynaplóból való megkereséséhez használja ez a táblázat a gyakori hibák a probléma megoldásához:

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| Összekötő regisztrálása nem sikerült: Ellenőrizze, hogy engedélyezte az alkalmazásproxy az Azure felügyeleti portálon és a megadott megfelelően az Active Directory felhasználónevet és jelszót. Hiba: "egy vagy több hiba történt." | Ha már bezárta a regisztrációs ablak az Azure AD-bejelentkezés nélkül, futtassa újra az összekötő varázslót, és az összekötő regisztrálására. <br><br> Ha a regisztráció ablak megnyílik, és ezután azonnal bezárul, nem engedélyezi, hogy jelentkezzen be, ez a hiba valószínűleg kap. Ez a hiba akkor fordul elő, ha hálózati hiba van a rendszeren. Győződjön meg arról, hogy lehet csatlakozni egy böngészőből nyilvános webhelyeken és, hogy a portok meg nyitva megadott [alkalmazásproxy Előfeltételek](application-proxy-enable.md). |
| Az ablakban törölje hiba jelenik meg. Nem lehet folytatni | Ha ezt a hibaüzenetet, és ezután az ablak bezárul, a rossz felhasználónévvel vagy jelszóval adott meg. Próbálja meg újra. |
| Összekötő regisztrálása nem sikerült: Ellenőrizze, hogy engedélyezte az alkalmazásproxy az Azure felügyeleti portálon és a megadott megfelelően az Active Directory felhasználónevet és jelszót. Hiba: "AADSTS50059: információ a bérlő-azonosító nem található vagy a kérelem vagy hallgatólagos bármelyik megadott hitelesítő adatok és a search szolgáltatás által egyszerű URI nem sikerült. | Jelentkezzen be Microsoft-Account és a nem tartományhoz, amely része annak a címtárnak, próbál hozzáférni a szervezet azonosítója kívánt. Győződjön meg arról, hogy a rendszergazda a tartomány neve megegyezik a bérlő tartománya része, például az Azure AD-tartománya a contoso.com, a rendszergazdának kell lennie, admin@contoso.com. |
| Nem sikerült beolvasni az aktuális végrehajtási szabályzata a PowerShell-parancsfájlok futtatásakor. | Ha az összekötő telepítése nem sikerül, ellenőrizze, győződjön meg arról, hogy a PowerShell végrehajtási házirend nincs letiltva. <br><br>1. Nyissa meg a Helyicsoportházirend-szerkesztő.<br>2. Lépjen a **számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők**  >   **Windows PowerShell** , és kattintson duplán a **kapcsolja be a parancsfájl végrehajtása**.<br>3. A végrehajtási házirendet is megadni **nincs konfigurálva** vagy **engedélyezve**. Ha beállítása **engedélyezve**, győződjön meg arról, hogy a beállítások, a végrehajtási házirend értéke termékeken **lehetővé helyi és távoli aláírt parancsfájlok** vagy **engedélyezése az összes parancsfájl**. |
| Összekötő nem tudta letölteni a konfigurációt. | Az összekötő ügyféltanúsítványt, a hitelesítéshez használt, lejárt. Ez akkor is előfordulhat, ha az összekötő telepítve van, a rendszer proxy mögött van. Ebben az esetben az összekötő nem férnek hozzá az internethez, és nem lesz képes biztosítani a távoli felhasználók számára az alkalmazásoknak. Manuálisan megújítani a `Register-AppProxyConnector` parancsmagot a Windows PowerShellben. Ha az összekötő egy proxykiszolgáló mögött található, akkor meg kell adni a "hálózati szolgáltatás" összekötő fiókok Internet-hozzáférés és a "helyi rendszer." Ez is elvégezhető, ehhez biztosítson hozzáférést a Proxy vagy állítsa őket a proxyt. |
| Összekötő regisztrálása nem sikerült: Győződjön meg arról, hogy az összekötő regisztrálására az Active Directory globális rendszergazdájának. Hiba: "a szolgáltatásregisztrálási kérelem megtagadva." | Bejelentkezés a következővel kívánt alias nem rendszergazdája az ebben a tartományban. Az összekötő mindig telepítve van a címtárból, amelyhez a felhasználó tartománnyal rendelkezik. Győződjön meg arról, hogy a rendszergazdai fiókkal próbált jelentkezzen be az Azure AD-bérlő globális engedélyekkel rendelkezik-e. |

## <a name="kerberos-errors"></a>Kerberos-hibák

Ez a táblázat a leggyakoribb hibák, amelyek a Kerberos-beállítás és konfiguráció származnak, és megoldási javaslatokat is kínál.

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| Nem sikerült beolvasni az aktuális végrehajtási szabályzata a PowerShell-parancsfájlok futtatásakor. | Ha az összekötő telepítése nem sikerül, ellenőrizze, győződjön meg arról, hogy a PowerShell végrehajtási házirend nincs letiltva.<br><br>1. Nyissa meg a Helyicsoportházirend-szerkesztő.<br>2. Lépjen a **számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők**  >   **Windows PowerShell** , és kattintson duplán a **kapcsolja be a parancsfájl végrehajtása**.<br>3. A végrehajtási házirendet is megadni **nincs konfigurálva** vagy **engedélyezve**. Ha beállítása **engedélyezve**, győződjön meg arról, hogy a beállítások, a végrehajtási házirend értéke termékeken **lehetővé helyi és távoli aláírt parancsfájlok** vagy **engedélyezése az összes parancsfájl**. |
| 12008 - azure ad-ben túllépte az engedélyezett Kerberos hitelesítési kísérleteket a háttérkiszolgálóra maximális számát. | Ez a hiba arra utalhat hibás konfiguráció az Azure AD között és a háttérkiszolgáló, vagy mindkét gépen dátum és idő konfigurációs problémát. A háttérkiszolgáló elutasította a Kerberos-jegyet az Azure AD által létrehozott. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló megfelelően van konfigurálva. Győződjön meg arról, hogy a dátum és idő konfiguráció az Azure AD és a háttérkiszolgáló szinkronizálódnak. |
| 13016 – az azure AD nem sikerült beolvasni a Kerberos jegyet a felhasználó nevében, mert nem tartalmaz egyszerű a biztonsági jogkivonatot, vagy a hozzáférés cookie-ban. | Az STS-konfigurációval probléma merül fel. Javítsa ki az egyszerű Felhasználónévi jogcím konfigurálása az STS-re. |
| 13019 – azure ad-ben a következő általános API-hiba miatt nem sikerült beolvasni a Kerberos jegyet a felhasználó nevében. | Ez az esemény azt jelezheti helytelen konfiguráció az Azure AD között, és a tartományvezérlő kiszolgálót vagy mindkét gépen dátum és idő konfigurációs problémát. A tartományvezérlő a Kerberos-jegyet az Azure AD által létrehozott elutasította. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló beállítása megfelelő-e, különösen az egyszerű szolgáltatásnév konfigurációja. Ellenőrizze, hogy az Azure AD tartományhoz csatlakoztatva, a tartományvezérlő, győződjön meg arról, hogy a tartományvezérlő megbízhatósági kapcsolatot hoz létre az Azure ad-vel azonos tartományban. Győződjön meg arról, hogy a dátum és idő konfiguráció az Azure AD és a rendszer szinkronizálja a tartományvezérlőn. |
| 13020 – azure ad-ben nem olvashatók be a felhasználó nevében egy Kerberos-jegyet, mert a háttérkiszolgáló SPN neve nincs megadva. | Ez az esemény azt jelezheti helytelen konfiguráció az Azure AD között, és a tartományvezérlő kiszolgálót vagy mindkét gépen dátum és idő konfigurációs problémát. A tartományvezérlő a Kerberos-jegyet az Azure AD által létrehozott elutasította. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló beállítása megfelelő-e, különösen az egyszerű szolgáltatásnév konfigurációja. Ellenőrizze, hogy az Azure AD tartományhoz csatlakoztatva, a tartományvezérlő, győződjön meg arról, hogy a tartományvezérlő megbízhatósági kapcsolatot hoz létre az Azure ad-vel azonos tartományban. Győződjön meg arról, hogy a dátum és idő konfiguráció az Azure AD és a rendszer szinkronizálja a tartományvezérlőn. |
| 13022 – az azure AD nem tudja hitelesíteni a felhasználót, mert a háttérkiszolgáló válaszol egy HTTP 401-es hiba miatt a Kerberos hitelesítési kísérleteket. | Ez az esemény azt jelezheti helytelen konfiguráció az Azure AD között és a háttérkiszolgáló, vagy mindkét gépen dátum és idő konfigurációs problémát. A háttérkiszolgáló elutasította a Kerberos-jegyet az Azure AD által létrehozott. Győződjön meg arról, hogy az Azure AD és a háttérkiszolgáló megfelelően van konfigurálva. Győződjön meg arról, hogy a dátum és idő konfiguráció az Azure AD és a háttérkiszolgáló szinkronizálódnak. További információkért lásd: [hibaelhárítása a Kerberos által korlátozott delegálás konfigurációi alkalmazásproxy](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>A végfelhasználói hibák

Ez a lista azokat a hibákat, a végfelhasználók akkor fordulhatnak elő, amikor megpróbálják elérni az alkalmazást, és nem. 

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| A webhelyen az oldal nem jeleníthető meg. | A felhasználó előfordulhat, hogy ez a hibaüzenet, ha az alkalmazás IWA alkalmazás közzé az alkalmazás elérésére tett kísérlet során. Lehet, hogy helytelen a megadott egyszerű szolgáltatásnév ehhez az alkalmazáshoz. IWA alkalmazások esetében ellenőrizze, hogy helyesen-e az egyszerű szolgáltatásnév ehhez az alkalmazáshoz konfigurált. |
| A webhelyen az oldal nem jeleníthető meg. | A felhasználó lehet, hogy ez a hibaüzenet, ha az alkalmazás az OWA alkalmazás közzé az alkalmazás elérésére tett kísérlet során. Ennek egyik lehetséges oka a következők egyikét:<br><li>Helytelen a megadott egyszerű szolgáltatásnév ehhez az alkalmazáshoz. Győződjön meg arról, hogy helyesen szerepel-e az egyszerű szolgáltatásnév ehhez az alkalmazáshoz konfigurált.</li><li>A felhasználó megpróbált hozzáférni az alkalmazáshoz az Microsoft-fiókja helyett a megfelelő vállalati fióknak való bejelentkezéshez, vagy a felhasználó vendégfelhasználó. Ellenőrizze, hogy a felhasználó bejelentkezik, amely megfelel a tartománynak a közzétett alkalmazás vállalati fiókjával. A Microsoft Account felhasználók és a Vendég IWA alkalmazások férhetnek hozzá.</li><li>A felhasználó, aki próbált hozzáférni az alkalmazás megfelelően nem tartalmazza az alkalmazás a helyi oldalon. Győződjön meg arról, hogy a felhasználó rendelkezik-e a megfelelő engedélyekkel, meghatározottak szerint a helyi gépen a háttéralkalmazás. |
| A vállalati alkalmazás nem érhető el. Nem jogosult az alkalmazás eléréséhez. A hitelesítés sikertelen. Ellenőrizze, hogy hozzáféréssel rendelkező felhasználó hozzárendelése az alkalmazáshoz. | A felhasználók lehet, hogy ez a hibaüzenet a Ha használják a Microsoft-fiókok helyett vállalati fiókjával jelentkezzen be a közzétett alkalmazás elérésére tett kísérlet során. Vendégfelhasználók is kaphat a hibáról. A Microsoft Account felhasználók és a vendégek IWA alkalmazások férhetnek hozzá. Ellenőrizze, hogy a felhasználó bejelentkezik, amely megfelel a tartománynak a közzétett alkalmazás vállalati fiókjával.<br><br>Előfordulhat, hogy nem rendelte a felhasználót az alkalmazáshoz. Nyissa meg a **alkalmazás** fülre, majd a **felhasználók és csoportok**, a felhasználó vagy felhasználói csoport hozzárendelése az alkalmazáshoz. |
| A vállalati alkalmazás jelenleg nem érhető el. Próbálkozzon újra később... Az összekötő túllépte az időkorlátot. | A felhasználók ezt a hibát kaphat, amikor megpróbál hozzáférni az alkalmazáshoz, ha nem megfelelően határozza ehhez az alkalmazáshoz a helyszíni oldalon közzétett. Győződjön meg arról, hogy a felhasználók rendelkeznek-e a megfelelő engedélyekkel, meghatározottak szerint a helyi gépen a háttéralkalmazás. |
| A vállalati alkalmazás nem érhető el. Nem jogosult az alkalmazás eléréséhez. A hitelesítés sikertelen. Győződjön meg arról, hogy a felhasználó rendelkezik-e az Azure Active Directory Premium vagy alapszintű licenc. | A felhasználók lehet, hogy ez a hibaüzenet a, ha azok nem explicit módon hozzárendelt prémium és alapszintű licenccel rendelkező rendszergazda az előfizető által közzétett alkalmazás elérésére tett kísérlet során. Nyissa meg az előfizető az Active Directory **licencek** lapra, és győződjön meg arról, hogy a felhasználó vagy felhasználócsoport hozzá van rendelve egy prémium szintű vagy alapszintű licenc. |

## <a name="my-error-wasnt-listed-here"></a>A hiba itt nem látható

Ha egy hiba vagy nem szerepel a listán a hibaelhárítási útmutató az Azure AD-alkalmazásproxy kapcsolatos probléma merül fel, szeretnénk hallani azt. E-mail küldése a [visszajelzéseket kezelő csapatnak](mailto:aadapfeedback@microsoft.com) a hiba részleteit.

## <a name="see-also"></a>Lásd még
* [Az Azure Active Directory alkalmazásproxy engedélyezése](application-proxy-enable.md)
* [Az alkalmazásproxy-alkalmazások közzététele](application-proxy-publish-azure-portal.md)
* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
