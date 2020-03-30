---
title: Távelérés engedélyezése a Power BI-hoz az Azure AD alkalmazásproxyval
description: A helyszíni Power BI azure AD alkalmazásproxyval való integrálásának alapjait ismerteti.
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
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111589"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>A Power BI Mobile távoli elérésének engedélyezése Azure AD-alkalmazásproxyval

Ez a cikk bemutatja, hogyan használhatja az Azure AD alkalmazásproxyt annak engedélyezéséhez, hogy a Power BI mobilalkalmazás csatlakozzon a Power BI Report Server (PBIRS) és az SQL Server Reporting Services (SSRS) 2016-os és újabb verzióihoz. Ezzel az integrációval a vállalati hálózattól távol lévő felhasználók a Power BI-jelentéseket a Power BI mobilalkalmazásból érhetik el, és az Azure AD-hitelesítéssel védhetik őket. Ez a védelem olyan [biztonsági előnyöket](application-proxy-security.md#security-benefits) is magában foglal, mint a feltételes hozzáférés és a többtényezős hitelesítés.  

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már telepítette a Jelentésszolgáltatásokat és [engedélyezte az alkalmazásproxyt.](application-proxy-add-on-premises-application.md)

- Alkalmazásproxy engedélyezése szükséges összekötő telepítése a Windows-kiszolgálón, és az [előfeltételek](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) teljesítése, hogy az összekötő kommunikálni az Azure AD-szolgáltatások.  
- A Power BI közzétételekor ugyanazokat a belső és külső tartományokat javasoljuk. Az egyéni tartományokról az [Egyéni tartományok használata az Alkalmazásproxyban (Munka az egyéni tartományok használata) témakörben olvashat bővebben.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)
- Ez az integráció a **Power BI Mobile iOS és Android** alkalmazáshoz érhető el.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1. lépés: A Kerberos korlátozott delegálásának konfigurálása (KCD)

A Windows-hitelesítést használó helyszíni alkalmazások esetében egyszeri bejelentkezést (SSO) érhet el a Kerberos hitelesítési protokollal és a Kerberos korlátozott delegálás (KCD) nevű szolgáltatással. Konfigurálása esetén a KCD lehetővé teszi, hogy az alkalmazásproxy-összekötő windowsos jogkivonatot szerezzen be egy felhasználószámára, még akkor is, ha a felhasználó közvetlenül nem jelentkezett be a Windows rendszerbe. Ha többet szeretne megtudni a KCD-ről, olvassa el a [Kerberos korlátozott delegálásáttekintése](https://technet.microsoft.com/library/jj553400.aspx) és [a Kerberos korlátozott delegálás című témakört az alkalmazásokba való egyszeri bejelentkezéshez az alkalmazásproxyval.](application-proxy-configure-single-sign-on-with-kcd.md)

A Reporting Services oldalán nem sok mindent kell konfigurálni. Csak győződjön meg róla, hogy egy érvényes egyszerű szolgáltatásnév (SPN) a megfelelő Kerberos-hitelesítés előfordulása érdekében. Győződjön meg arról is, hogy a Reporting Services kiszolgáló engedélyezve van a hitelesítés egyeztetéséhez.

A KCD jelentéskészítési szolgáltatásokhoz való beállításához folytassa a következő lépésekkel.

### <a name="configure-the-service-principal-name-spn"></a>Az egyszerű szolgáltatásnév (SPN) konfigurálása

Az SPN egy egyedi azonosító egy Kerberos-hitelesítést használó szolgáltatáshoz. Meg kell győződnie arról, hogy megfelelő HTTP-spn jelen van a jelentéskészítő kiszolgálón. Információk a megfelelő egyszerű szolgáltatásnév (SPN) konfigurálásáról a jelentéskészítő kiszolgálón: [Egyszerű szolgáltatásnév (SPN) regisztrálása egy jelentéskészítő kiszolgálóhoz](https://msdn.microsoft.com/library/cc281382.aspx).
Az SPN hozzáadását ellenőrizheti a Setspn parancs -L kapcsolóval való futtatásával. A parancsról további információ: [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Egyeztetéses hitelesítés engedélyezése

Ha engedélyezni szeretné, hogy a jelentéskészítő kiszolgáló Kerberos-hitelesítést használhasson, állítsa be a jelentéskészítő kiszolgáló hitelesítési típusát RSWindowsNegotiate néven. Konfigurálja ezt a beállítást az rsreportserver.config fájllal.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

További információk: [Reporting Services konfigurációs fájl módosítása](https://msdn.microsoft.com/library/bb630448.aspx) és [Windows-hitelesítés konfigurálása egy jelentéskészítő kiszolgálón](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Annak ellenőrzése, hogy az összekötő megbízható-e a Reporting Services alkalmazáskészlet-fiókhoz hozzáadott SPN-re való delegáláshoz
Konfigurálja a KCD-t úgy, hogy az Azure AD alkalmazásproxy szolgáltatás képes felhasználói identitásokat delegálni a Reporting Services alkalmazáskészlet-fiókba. Konfigurálja a KCD-t azáltal, hogy engedélyezi az Alkalmazásproxy-összekötő kerberos-jegyek beolvasását az Azure AD-ben hitelesített felhasználók számára. Ezután a kiszolgáló átadja a környezetet a célalkalmazásnak, vagy ebben az esetben a Reporting Services-nek.

A KCD konfigurálásához ismételje meg a következő lépéseket minden egyes csatlakozógépen:

1. Jelentkezzen be a tartományvezérlőre tartományi rendszergazdaként, majd nyissa meg az **Active Directory – felhasználók és számítógépek beépülő modult.**
2. Keresse meg azt a számítógépet, amelyen az összekötő fut.  
3. Kattintson duplán a számítógépre, majd válassza a **Delegálás** lapot.
4. Állítsa a delegálási beállításokat **a Megbízható számítógép csak a megadott szolgáltatásokra való delegáláshoz beállításra.** Ezután válassza **a Bármely hitelesítési protokoll használata lehetőséget.**
5. Válassza **a Hozzáadás**lehetőséget, majd a **Felhasználók vagy számítógépek**lehetőséget.
6. Adja meg a Reporting Services szolgáltatáshoz használt szolgáltatásfiókot. Ehhez a fiókhoz adta hozzá a SPN-t a Reporting Services konfigurálása során.
7. Kattintson az **OK** gombra. A módosítások mentéséhez kattintson ismét az **OK** gombra.

További információ: [Kerberos korlátozott delegálás az alkalmazásokba való egyszeri bejelentkezéshez az alkalmazásproxyval.](application-proxy-configure-single-sign-on-with-kcd.md)

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>2. lépés: Jelentésszolgáltatások közzététele az Azure AD alkalmazásproxyn keresztül

Most már készen áll az Azure AD alkalmazásproxy konfigurálására.

1. A Jelentésszolgáltatások közzététele alkalmazásproxyn keresztül a következő beállításokkal. Az alkalmazások alkalmazásproxyn keresztüli közzétételének lépésenkénti útmutatásáról az [Alkalmazások közzététele az Azure AD alkalmazásproxy használatával témakörben](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)talál.
   - **Belső URL:** Adja meg a jelentéskészítő kiszolgáló URL-címét, amelyet az összekötő elérhet a vállalati hálózaton. Győződjön meg arról, hogy ez az URL-cím elérhető abból a kiszolgálóból, amelyen az összekötő telepítve van. Ajánlott eljárás egy legfelső szintű tartomány, `https://servername/` például, hogy elkerüljék a problémákat subpaths közzétett alkalmazásproxy n keresztül. Például, `https://servername/` és `https://servername/reports/` nem `https://servername/reportserver/`vagy .
     > [!NOTE]
     > Javasoljuk, hogy biztonságos HTTPS-kapcsolatot használjon a jelentéskészítő kiszolgálóval. Az [SSL-kapcsolatok konfigurálása natív módú jelentéskészítő kiszolgálón](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) című témakörben talál információt a rról, hogyan.
   - **Külső URL:** Adja meg azt a nyilvános URL-t, amelyhez a Power BI mobilalkalmazás csatlakozni fog. Például úgy tűnhet, `https://reports.contoso.com` hogy ha egyéni tartományt használ. Egyéni tartomány használatához töltsön fel egy tanúsítványt a tartományhoz, és mutasson dns-rekordot az alkalmazás alapértelmezett msappproxy.net tartományára. Részletes lépéseket az [Egyéni tartományok használata az Azure AD alkalmazásproxyban című témakörben ismer.](application-proxy-configure-custom-domain.md)

   - **Előhitelesítési módszer**: Azure Active Directory

2. Az alkalmazás közzététele után konfigurálja az egyszeri bejelentkezési beállításokat a következő lépésekkel:

   a. A portál alkalmazáslapján válassza az **Egyszeri bejelentkezés**lehetőséget.

   b. **Az egyszeri bejelentkezési mód esetén**válassza az Integrált **Windows-hitelesítés**lehetőséget.

   c. Állítsa a **belső alkalmazás spn-t** a korábban beállított értékre.  

   d. Válassza ki a **delegált bejelentkezési identitást** az összekötő számára a felhasználók nevében használandó. További információ: [A különböző helyszíni és felhőalapú identitások megtekintése.](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

   e. A módosítások mentéséhez kattintson a **Mentés** gombra.

Az alkalmazás beállításának befejezéséhez lépjen **a Felhasználók és csoportok** szakaszra, és rendelje hozzá a felhasználókat az alkalmazás eléréséhez.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>3. lépés: A válasz URI-k módosítása az alkalmazáshoz

Ahhoz, hogy a Power BI mobilalkalmazás csatlakozhasson és elérhesse a Jelentési szolgáltatásokat, konfigurálnia kell az alkalmazásregisztrációt, amelyet a 2. 

1. Az Azure Active Directory **áttekintése** lapon válassza **az Alkalmazásregisztrációk**lehetőséget.
2. A **Minden alkalmazás** lapon keresse meg a 2.
3. Jelölje ki az alkalmazást, majd a **Hitelesítés**lehetőséget.
4. Adja hozzá a következő átirányítási URI-kat a használt platform alapján.

   A Power BI Mobile **iOS**alkalmazásának konfigurálásakor adja hozzá a következő Nyilvános ügyfél (Mobile & Desktop) típusú átirányítási URI-kat:
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   A Power BI Mobile **Android**alkalmazásának konfigurálásakor adja hozzá a következő Nyilvános ügyfél (Mobile & Desktop) típusú átirányítási URI-kat:
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Az átirányítási URI-kat hozzá kell adni ahhoz, hogy az alkalmazás megfelelően működjön. Ha az alkalmazást power BI Mobile iOS és Android rendszerhez is konfigurálja, adja hozzá a következő Nyilvános ügyfél (Mobile & `urn:ietf:wg:oauth:2.0:oob`Desktop) típusú átirányítási URI-kat az iOS-hez konfigurált átirányítási URI-k listájához: . .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>4. lépés: Csatlakozás a Power BI mobilalkalmazásból

1. A Power BI mobilalkalmazásban csatlakozzon a Reporting Services-példányhoz. Ehhez adja meg az alkalmazásproxyn keresztül közzétett alkalmazás **külső URL-címét.**

   ![Külső URL-címmel rendelkező Power BI mobilalkalmazás](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Kattintson a **Csatlakozás** gombra. A lesz irányítva az Azure Active Directory bejelentkezési lapra.

3. Adja meg a felhasználó érvényes hitelesítő adatait, és válassza **a Bejelentkezés**lehetőséget. A Jelentéskészítő szolgáltatások kiszolgálójáról származó elemek jelennek meg.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>5. lépés: Az Intune-házirend konfigurálása felügyelt eszközökhöz (nem kötelező)

A Microsoft Intune segítségével kezelheti a vállalat munkatársai által használt ügyfélalkalmazásokat. Az Intune lehetővé teszi olyan képességek használatát, mint az adattitkosítás és a további hozzáférési követelmények. Ha többet szeretne megtudni az Intune-on keresztüli alkalmazáskezelésről, olvassa el az Intune Alkalmazáskezelés. Ha engedélyezni szeretné, hogy a Power BI mobilalkalmazás működjön az Intune-szabályzattal, kövesse az alábbi lépéseket.

1. Nyissa meg az **Azure Active Directoryt,** majd **az Alkalmazásregisztrációk alkalmazást.**
2. Válassza ki a 3.
3. Az alkalmazás lapján válassza az **API-engedélyek**lehetőséget.
4. Kattintson **az Engedély hozzáadása gombra.** 
5. A **szervezet által használt API-k**csoportban keressen rá a "Microsoft Mobile Application Management" kifejezésre, és jelölje ki azt.
6. A **DeviceManagementManagedApps.ReadWrite** engedély hozzáadása az alkalmazáshoz
7. Kattintson **a Rendszergazda hozzájárulásának engedélyezése** az alkalmazáshoz való engedélyhez.
8. Konfigurálja a kívánt Intune-szabályzatot az [alkalmazásvédelmi szabályzatok létrehozásának és hozzárendelésének](https://docs.microsoft.com/intune/app-protection-policies)módjára hivatkozva.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az alkalmazás egy hibalapot ad vissza, miután néhány percnél tovább próbált betölteni egy jelentést, előfordulhat, hogy módosítania kell az időtúlterhelési beállítást. Alapértelmezés szerint az alkalmazásproxy támogatja azokat az alkalmazásokat, amelyek akár 85 másodpercig is eltarthatnak a kérésmegválaszolása érdekében. Ha 180 másodpercre szeretné meghosszabbítani ezt a beállítást, válassza a háttér-időoutot **hosszú** ra az alkalmazásproxy-beállítások lapján. A gyors és megbízható jelentések létrehozásáról a [Power BI-jelentések ajánlott eljárások című témakörben](https://docs.microsoft.com/power-bi/power-bi-reports-performance)olvashat.

## <a name="next-steps"></a>További lépések

- [A natív ügyfélalkalmazások proxyalkalmazásokkal való interakciójának engedélyezése](application-proxy-configure-native-client-application.md)
- [Helyszíni jelentéskészítő kiszolgálói jelentések és KPI-k megtekintése a Power BI-mobilalkalmazásokban](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
