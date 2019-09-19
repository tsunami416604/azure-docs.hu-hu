---
title: Távoli hozzáférés engedélyezése Power BI az Azure AD Application Proxy használatával | Microsoft Docs
description: Ismerteti a helyszíni Power BI és az Azure AD Application Proxy integrálásának alapjait.
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
ms.openlocfilehash: b3d758b63b56bb84b1cb4e5793731da5eb4f5209
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103874"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Távoli hozzáférés engedélyezése Power BI Mobile Azure-AD Application Proxy

Ez a cikk azt ismerteti, hogyan használható az Azure AD Application Proxy annak engedélyezéséhez, hogy a Power BI Mobile App Power BI jelentéskészítő kiszolgáló (PBIRS) és a SQL Server Reporting Services (SSRS) 2016-es vagy újabb verziójához kapcsolódjon. Ezen integráció révén a vállalati hálózaton kívüli felhasználók hozzáférhetnek a Power BI-jelentésekhez a Power BI Mobile alkalmazásból, és az Azure AD-hitelesítéssel is védhetők. Ez a védelem olyan [biztonsági előnyöket](application-proxy-security.md#security-benefits) foglal magában, mint például a feltételes hozzáférés és a többtényezős hitelesítés.  

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már telepítette a Report Services és az [enabled Application proxy](application-proxy-add-on-premises-application.md)szolgáltatást.

- Az alkalmazásproxy engedélyezéséhez telepítenie kell egy összekötőt egy Windows-kiszolgálón, és el kell töltenie az [előfeltételeket](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) , hogy az összekötő képes legyen kommunikálni az Azure ad-szolgáltatásokkal.  
- A Power BI közzétételekor azt javasoljuk, hogy ugyanazokat a belső és külső tartományokat használja. Az egyéni tartományokkal kapcsolatos további tudnivalókért tekintse meg az [Egyéni tartományok használata az Application proxyban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)című témakört.
- Ez az integráció a **Power bi Mobile iOS-és Android-** alkalmazásokhoz érhető el.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1\. lépés: A Kerberos által korlátozott delegálás konfigurálása (KCD)

Windows-hitelesítést használó helyszíni alkalmazások akkor érhető el, egyszeri bejelentkezés (SSO) a Kerberos hitelesítési protokoll és a Kerberos által korlátozott delegálás (KCD) szolgáltatás. Ha be van állítva, a KCD lehetővé teszi, hogy az alkalmazásproxy-összekötő beszerezzen egy Windows-jogkivonatot a felhasználó számára akkor is, ha a felhasználó nem jelentkezett be közvetlenül a Windowsba. További információ a KCD: a [Kerberos által korlátozott delegálás áttekintése](https://technet.microsoft.com/library/jj553400.aspx) és a [Kerberos által korlátozott delegálás az alkalmazásokba való egyszeri bejelentkezéshez](application-proxy-configure-single-sign-on-with-kcd.md).

Nem sok a konfigurálás a jelentéskészítési szolgáltatási oldalon. Győződjön meg arról, hogy érvényes egyszerű szolgáltatásnév (SPN) van a megfelelő Kerberos-hitelesítés engedélyezéséhez. Győződjön meg arról is, hogy a Reporting Services-kiszolgáló engedélyezve van az egyeztetéses hitelesítéshez.

A Reporting Services KCD beállításához folytassa a következő lépésekkel.

### <a name="configure-the-service-principal-name-spn"></a>Egyszerű szolgáltatásnév (SPN) konfigurálása

Az egyszerű szolgáltatásnév a Kerberos-hitelesítést használó szolgáltatás egyedi azonosítója. Győződjön meg arról, hogy rendelkezik megfelelő HTTP SPN-vel a jelentéskészítő kiszolgálón. A jelentéskészítő kiszolgáló megfelelő egyszerű szolgáltatásnév (SPN) konfigurálásával kapcsolatos információkért lásd: egyszerű szolgáltatásnév [(SPN) regisztrálása egy jelentéskészítő kiszolgálóhoz](https://msdn.microsoft.com/library/cc281382.aspx).
A Setspn parancs a-L kapcsolóval való futtatásával ellenőrizheti, hogy az egyszerű szolgáltatásnév hozzá lett-e adva. Ezzel a paranccsal kapcsolatos további információkért lásd: [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Egyeztetéses hitelesítés engedélyezése

Ha engedélyezni szeretné a jelentéskészítő kiszolgáló számára a Kerberos-hitelesítés használatát, konfigurálja a jelentéskészítő kiszolgáló hitelesítési típusát RSWindowsNegotiate. Konfigurálja ezt a beállítást az RSReportServer. config fájl használatával.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

További információt a [Reporting Services konfigurációs fájljának módosítása](https://msdn.microsoft.com/library/bb630448.aspx) és a [Windows-hitelesítés konfigurálása jelentéskészítő kiszolgálón](https://msdn.microsoft.com/library/cc281253.aspx)című témakörben talál.

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Győződjön meg arról, hogy az összekötő megbízható a Reporting Services alkalmazáskészlet-fiókhoz hozzáadott egyszerű szolgáltatásnév delegálásához.
Konfigurálja úgy a KCD, hogy az Azure AD Application Proxy szolgáltatás delegálja a felhasználói identitásokat a Reporting Services alkalmazáskészlet-fiókjába. Konfigurálja a kcd Szolgáltatáshoz beolvasni a felhasználók, akik az Azure ad-ben hitelesített Kerberos-jegyet az Application Proxy connector engedélyezésével. Ezt követően a kiszolgáló továbbítja a környezetet a célalkalmazás vagy a Reporting Services szolgáltatásnak ebben az esetben.

A KCD konfigurálásához ismételje meg az alábbi lépéseket minden összekötő-gépen:

1. Jelentkezzen be tartományi rendszergazdaként egy tartományvezérlőre, majd nyissa meg **Active Directory felhasználókat és számítógépeket**.
2. Az összekötőt futtató számítógépen található.  
3. Kattintson duplán a számítógépre, majd válassza a **delegálás** lapot.
4. A delegálási beállítások beállításával **bízza meg a számítógépet, hogy csak a megadott szolgáltatásokhoz delegáljon delegálást**. Ezután válassza ki **bármely hitelesítési protokoll**.
5. Válassza a **Hozzáadás**, majd a **felhasználók vagy számítógépek**lehetőséget.
6. Adja meg a Reporting Services szolgáltatáshoz használt szolgáltatásfiókot. Ezt a fiókot adta hozzá az SPN-nek a Reporting Services-konfigurációban való hozzáadásához.
7. Kattintson az **OK** gombra. A módosítások mentéséhez kattintson ismét **az OK** gombra.

További információ: [Kerberos által korlátozott delegálás az alkalmazásokba való egyszeri bejelentkezéshez az Application proxy használatával](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>2\. lépés: Jelentéskészítési szolgáltatások közzététele az Azure AD Application Proxy

Most már készen áll az Azure AD Application Proxy konfigurálására.

1. Jelentéskészítési szolgáltatások közzététele az Application proxyn keresztül a következő beállításokkal. Az alkalmazások alkalmazásproxy használatával történő közzétételének részletes ismertetését lásd: [alkalmazások közzététele az Azure ad Application proxy segítségével](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Belső URL-cím**: Adja meg annak a jelentéskészítő kiszolgálónak az URL-címét, amelyet az összekötő elérhet a vállalati hálózaton. Győződjön meg arról, hogy ez az URL-cím elérhető azon a kiszolgálón, amelyen az összekötő telepítve van. Az ajánlott eljárás egy legfelső szintű tartományt használ, például `https://servername/` a nem az alkalmazásproxy használatával közzétett alelérési problémák elkerülésére ( `https://servername/reportserver/` `https://servername/reports/` például és).
     > [!NOTE]
     > A jelentéskészítő kiszolgálóhoz biztonságos HTTPS-kapcsolat használatát javasoljuk. További információ: az [SSL-kapcsolatok konfigurálása natív módú jelentéskészítő kiszolgálón](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) .
   - **Külső URL-cím**: Adja meg a nyilvános URL-címet, amelyhez a Power BI Mobile alkalmazás csatlakozni fog. Előfordulhat `https://reports.contoso.com` például, hogy egy egyéni tartományt használ. Ha egyéni tartományt szeretne használni, töltsön fel egy tanúsítványt a tartományhoz, és mutasson egy DNS-rekordot az alkalmazás alapértelmezett msappproxy.net-tartományára. Részletes lépések: [Egyéni tartományok használata az Azure ad Application proxyban](application-proxy-configure-custom-domain.md).

   - **Előhitelesítési módszer**: Azure Active Directory

2. Az alkalmazás közzététele után konfigurálja az egyszeri bejelentkezés beállításai az alábbi lépéseket:

   a. Az alkalmazás oldalán a portálon, válassza **egyszeri bejelentkezési**.

   b. **Egyszeri bejelentkezési mód**esetén válassza az **integrált Windows-hitelesítés**lehetőséget.

   c. A **belső alkalmazás SPN** beállítása a korábban megadott értékre.  

   d. Válassza ki a **delegált bejelentkezési azonosító** az összekötő használatára a felhasználó nevében. További információ: [a különböző helyszíni és Felhőbeli identitások használata](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Kattintson a **mentése** a módosítások mentéséhez.

Az alkalmazás beállításának befejezéséhez nyissa meg **a felhasználók és csoportok** szakaszt, és rendelje hozzá a felhasználókat az alkalmazás eléréséhez.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>3\. lépés: A válasz URI-azonosítójának módosítása az alkalmazáshoz

Mielőtt a Power BI Mobile App csatlakozhat és hozzáférhessen a Report Services szolgáltatáshoz, konfigurálnia kell az alkalmazás regisztrációját, amelyet a 2. lépésben automatikusan hozott létre. 

1. Az Azure Active Directory **áttekintése** lapon válassza a **Alkalmazásregisztrációk**lehetőséget.
2. A **minden alkalmazás** lapon keresse meg a 2. lépésben létrehozott alkalmazást.
3. Válassza ki az alkalmazást, majd válassza a **hitelesítés**lehetőséget.
4. Adja hozzá a következő átirányítási URI-ket a használt platform alapján.

   Power BI Mobile **iOS**-hez készült alkalmazás konfigurálásakor adja hozzá a következő átirányítási URI-k nyilvános ügyfélként (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Power BI Mobile **Androidhoz**készült alkalmazás konfigurálásakor adja hozzá a következő átirányítási URI-k nyilvános ügyfélként (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`

   > [!IMPORTANT]
   > Az alkalmazás megfelelő működéséhez hozzá kell adni az átirányítási URI-azonosítókat. Ha az alkalmazást Power BI Mobile iOS és Android rendszerhez is konfigurálja, adja hozzá a nyilvános ügyfél (Mobile & Desktop) típusú átirányítási URI-t az iOS-hez konfigurált átirányítási URI- `urn:ietf:wg:oauth:2.0:oob`k listájához:.

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>4\. lépés: Kapcsolódjon a Power BI Mobile alkalmazásból

1. A Power BI Mobile alkalmazásban kapcsolódjon a Reporting Services-példányhoz. Ehhez adja meg az Application proxyn keresztül közzétett alkalmazás **külső URL-címét** .

   ![Power BI Mobile alkalmazás külső URL-címmel](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Kattintson a **Csatlakozás** gombra. A rendszer átirányítja a Azure Active Directory bejelentkezési oldalára.

3. Adjon meg érvényes hitelesítő adatokat a felhasználó számára, és válassza a **Bejelentkezés**lehetőséget. Ekkor megjelennek a Reporting Services-kiszolgáló elemei.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>5\. lépés: Az Intune-szabályzat konfigurálása a felügyelt eszközökhöz (nem kötelező)

> [!NOTE]
> Ez a funkció jelenleg csak iOS rendszeren érhető el.

A Microsoft Intune segítségével kezelheti a vállalat munkaerő által használt ügyfélalkalmazások felügyeletét. Az Intune lehetővé teszi olyan képességek használatát, mint például az adattitkosítás és a további hozzáférési követelmények. Az alkalmazások Intune-nal történő kezelésével kapcsolatos további információkért lásd: az Intune app Management. Az alábbi lépéseket követve engedélyezheti, hogy a Power BI Mobile Application működjön az Intune-szabályzattal.

1. Nyissa meg **Azure Active Directory** , majd az **alkalmazások regisztrációját**.
2. Válassza ki a natív ügyfélalkalmazás regisztrálása során a 3. lépésben konfigurált alkalmazást.
3. Az alkalmazás lapján válassza az API- **engedélyek**lehetőséget.
4. Kattintson **az engedély hozzáadása**lehetőségre. 
5. A **saját szervezet által használt API**-k területen keressen rá a "Microsoft Mobile Application Management" kifejezésre, és válassza ki.
6. Adja hozzá a **DeviceManagementManagedApps. READWRITE** engedélyt az alkalmazáshoz
7. Kattintson a **rendszergazdai jóváhagyás megadása** lehetőségre az alkalmazáshoz való hozzáférés engedélyezéséhez.
8. Konfigurálja a kívánt Intune- [szabályzatot az alkalmazás-védelmi házirendek létrehozásához és hozzárendeléséhez](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az alkalmazás a jelentés több percnél hosszabb betöltésére tett kísérlet után egy hibaüzenetet ad vissza, előfordulhat, hogy módosítania kell az időtúllépési beállítást. Alapértelmezés szerint az alkalmazásproxy olyan alkalmazásokat támogat, amelyek akár 85 másodpercet is igénybe vesznek a kérelmek megválaszolására. A beállítás 180 másodpercre való meghosszabbításához válassza ki az alkalmazáshoz **tartozó alkalmazásproxy** -beállítások lapon a háttérbeli időtúllépést. A gyors és megbízható jelentések létrehozásával kapcsolatos tippekért tekintse meg [Power bi jelentések – ajánlott eljárások](https://docs.microsoft.com/power-bi/power-bi-reports-performance)című témakört.

## <a name="next-steps"></a>További lépések

- [Natív ügyfélalkalmazások engedélyezése a proxy alkalmazásokkal való kommunikációhoz](application-proxy-configure-native-client-application.md)
- [A helyszíni jelentéskészítő kiszolgáló jelentéseinek és KPI-k megtekintése a Power BI Mobile apps szolgáltatásban](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
