---
title: Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezése |} A Microsoft Docs
description: Egy helyszíni SharePoint-kiszolgáló integrálása az Azure AD-alkalmazásproxy alapjait ismerteti.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: a240e629c5c8f6e43a4c3c52668fcb2b811989b2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468386"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezése

Ez a cikk ismerteti, hogyan lehet egy helyszíni SharePoint-kiszolgáló integrálása az Azure Active Directory (Azure AD) alkalmazásproxy használatával.

Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezéséhez kövesse az ebben a cikkben lépésről lépésre szakaszok.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már rendelkezik a SharePoint 2013-as vagy újabb a környezetben. Ezenkívül vegye figyelembe a következő előfeltételek vonatkoznak:

* A SharePoint natív Kerberos-támogatást tartalmaz. Ezért hozzáférő felhasználók belső helyek távolról az Azure AD-alkalmazásproxyn keresztül feltételezheti, hogy az egyszeri bejelentkezés (SSO) felhasználói élményt.

* Ebben a forgatókönyvben a SharePoint-kiszolgáló konfigurációs módosításait tartalmazza. Azt javasoljuk, hogy egy átmeneti környezet használatával. Ezzel a módszerrel, először az átmeneti kiszolgálón hajtsa végre frissítéseket, és ezután megkönnyítik az éles környezetben való elhelyezés előtt tesztelési ciklust.

* A közzétett URL-cím SSL szükség van. Az SSL is szükséges a belső URL-cím annak érdekében, hogy hivatkozásokat küldött/leképezve megfelelően.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1. lépés: Configure Kerberos Constrained Delegation (KCD)

Windows-hitelesítést használó helyszíni alkalmazások akkor érhető el, egyszeri bejelentkezés (SSO) a Kerberos hitelesítési protokoll és a Kerberos által korlátozott delegálás (KCD) szolgáltatás. KCD, ha konfigurálva van, lehetővé teszi, hogy egy felhasználó egy Windows-jogkivonat beszerzése az Application Proxy connector akkor is, ha a felhasználó még nem jelentkezett be a Windows közvetlenül. KCD kapcsolatos további információkért lásd: [Kerberos által korlátozott delegálás áttekintése](https://technet.microsoft.com/library/jj553400.aspx).

Egy SharePoint-kiszolgáló beállítása a kcd Szolgáltatáshoz, az eljárásokkal a következő egymást követő szakaszokban:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Győződjön meg arról, hogy a SharePoint-webalkalmazást egy tartományi fiók alatt fut.

Első lépésként ellenőrizze, hogy a SharePoint-webalkalmazás fut-e egy tartományi fiókkal – nem helyi, helyi vagy hálózati szolgáltatást. Ehhez úgy, hogy az egyszerű szolgáltatásnevek (SPN) csatlakoztathat ezt a fiókot. SPN-EK, hogyan azonosítja a Kerberos protokoll a különböző szolgáltatásokat. És a használatával konfigurálja a Kerberos-fiók szükséges.

> [!NOTE]
Szüksége lesz egy korábban létrehozott Azure AD-fiókot a szolgáltatáshoz. Javasoljuk, hogy engedélyezze az automatikus jelszóváltoztatáshoz. Azokat a lépéseket és a hibaelhárítási problémák teljes kapcsolatos további információkért lásd: [jelszavának automatikus módosítását konfigurálása a SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Győződjön meg arról, hogy a helyek egy meghatározott szolgáltatás fiók alatt futnak, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a **SharePoint központi felügyelet** hely.
2. Lépjen a **biztonsági** válassza **szolgáltatásfiókok konfigurálása**.
3. Válassza ki **webalkalmazás-készlet - SharePoint – 80-as**. A beállítások némileg eltérőek lehetnek a webalkalmazás-készlet neve alapján, vagy ha a webes készletet használja az SSL-alapértelmezés szerint.

  ![Lehetőségek a szolgáltatásfiók konfigurálása](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Ha **válassza ki a fiókot ehhez az összetevőhöz** mező értéke **helyi szolgáltatás** vagy **hálózati szolgáltatás**, hozzon létre egy fiókot kell. Ha nem, akkor ezzel végzett, és áthelyezheti a következő szakaszra.
5. Válassza ki **új felügyelt fiók regisztrálása**. Miután létrehozta a fiókot, be kell állítania **webes alkalmazáskészlet** a fiók használata előtt.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Állítsa be a szolgáltatásnév a SharePoint-szolgáltatásfiókhoz

Mielőtt beállítaná a kcd Szolgáltatáshoz, kell tennie:

* A SharePoint-webalkalmazást, amely az Azure AD alkalmazásproxy megmutatják futtató tartományi fiók azonosításához.
* Válasszon egy belső URL-CÍMÉT, amely az Azure AD-alkalmazásproxy és a SharePoint lesz konfigurálva. A belső URL-cím a webalkalmazás már nem használhatók, és soha nem jelenik meg a webböngészőt.

Ha a kiválasztott belső URL-cím <https://sharepoint>, akkor az egyszerű szolgáltatásnév:

```
HTTP/SharePoint
```

> [!NOTE]
> Tartsa tiszteletben az alábbi javaslatok a belső URL-címe:
> * HTTPS használata
> * Egyéni portokat ne használja
> * A DNS-ben hozzon létre egy állomás (A) a SharePoint előtér-Webkiszolgálón (vagy terheléselosztó) pontra, és nem egy Alias (CName)

Ezt az SPN regisztrálásához futtassa a következő parancsot a parancssorból, a tartomány rendszergazdája:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Ez a parancs beállítja az egyszerű szolgáltatásnév _HTTP/SharePoint_ for SharePoint alkalmazáskészlet-fiók _demo\spAppPoolAccount_.

Cserélje le _HTTP/SharePoint_ az egyszerű Szolgáltatásnevet a belső URL-cím- és _demo\spAppPoolAccount_ az alkalmazáskészlet-fiók környezetében. A Setspn parancs keres az egyszerű Szolgáltatásnevet, mielőtt hozzáadja azt. A már létezik, látni fogja a **ismétlődő SPN érték** hiba. Ebben az esetben fontolja meg a meglévő egyszerű szolgáltatásnév eltávolításához, ha nincs beállítva a megfelelő alkalmazáskészletének fiókja alatt.

Ellenőrizheti, hogy az egyszerű Szolgáltatásnevet a Setspn parancs futtatásával a -L lehetőséggel jelent. Ezzel a paranccsal kapcsolatos további információkért lásd: [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Győződjön meg arról, hogy az összekötő az egyszerű Szolgáltatásnevet, a SharePoint alkalmazáskészlet fiókja hozzáadódik a delegálás

Állítsa a kcd Szolgáltatáshoz, hogy az Azure AD-alkalmazásproxy szolgáltatás delegálhatja a felhasználói identitások SharePoint alkalmazáskészlet-fiók. Konfigurálja a kcd Szolgáltatáshoz beolvasni a felhasználók, akik az Azure ad-ben hitelesített Kerberos-jegyet az Application Proxy connector engedélyezésével. Majd, hogy a kiszolgáló továbbítja a környezetet a célalkalmazás vagy a SharePoint ebben az esetben.

Konfigurálja a Kerberos, minden összekötő géphez ismételje meg a következő lépéseket:

1. Jelentkezzen be egy tartományvezérlőre tartományi rendszergazdaként, és nyissa meg **Active Directory – felhasználók és számítógépek**.
2. Az összekötőt futtató számítógépen található. Ebben a példában ugyanazon a SharePoint-kiszolgálón.
3. Kattintson duplán arra a számítógépre, és kattintson a **delegálás** fülre.
4. Győződjön meg arról, hogy a delegálási beállítások beállítása **számítógépen csak a megadott szolgáltatások delegálhatók**. Ezután válassza ki **bármely hitelesítési protokoll**.
5. Kattintson a **Hozzáadás** gombra, majd **felhasználók vagy számítógépek**, és keresse meg a SharePoint alkalmazáskészlet-fiók, például _demo\spAppPoolAccount_.
6. SPN-ek listájában válassza ki a korábban létrehozott szolgáltatásfiók.
7. Kattintson az **OK** gombra. Kattintson a **OK** újra, hogy a módosítások mentéséhez.
  
  ![A delegálási beállítások](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>2. lépés: Az Azure AD-Proxy konfigurálása

Most, hogy konfigurálta a kcd Szolgáltatáshoz, készen áll az Azure AD Application Proxy konfigurálása.

1. Közzététel a SharePoint-webhely a következő beállításokkal. Lépésenkénti útmutatásért lásd: [közzététel az Azure AD-alkalmazásproxy használatával](application-proxy-publish-azure-portal.md).
   * **Belső URL-cím**: A SharePoint belső URL-cím a korábban kiválasztott például **<https://SharePoint/>**.
   * **Az előhitelesítési módszer**: Azure Active Directory
   * **A fejlécek URL-cím fordításának**: NO

   >[!TIP]
   >A SharePoint használja a _állomásfejléc_ érték keresse ki a helyet. Ez az érték alapján hivatkozásokat is állít elő. Az eredő hatás, hogy minden olyan hivatkozás, amely a SharePoint hoz létre egy közzétett URL-címet, amely a külső URL-cím használata megfelelően vannak beállítva. Az érték **Igen** is lehetővé teszi, hogy az összekötő továbbítja a kérést a háttéralkalmazás. Azonban érték beállítása **nem** azt jelenti, hogy az összekötő nem küld a belső neve. Ehelyett az összekötő küld az állomásfejléc közzétett URL-címként a háttéralkalmazás.

   ![Alkalmazás közzététele SharePoint](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Az alkalmazás közzététele után konfigurálja az egyszeri bejelentkezés beállításai az alábbi lépéseket:

   1. Az alkalmazás oldalán a portálon, válassza **egyszeri bejelentkezési**.
   2. Válassza az egyszeri bejelentkezési mód, **integrált Windows-hitelesítés**.
   3. Belső alkalmazás egyszerű Szolgáltatásnevét, amely a korábban beállított értékre állítva. Ebben a példában ez lenne **HTTP/SharePoint**.
   4. Válassza a "Delegált bejelentkezési identitás" **a helyi SAM-fiók neve**.

   ![Egyszeri bejelentkezés az integrált Windows-hitelesítés konfigurálása](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Az alkalmazás beállításának befejezéséhez, nyissa meg a **felhasználók és csoportok** szakaszt, és hozzárendelhet felhasználókat az alkalmazás eléréséhez. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>3. lépés: Kerberos és az Azure AD alkalmazásproxy URL-címek használata a SharePoint konfigurálása

Következő lépés, hogy a SharePoint webalkalmazás új zónához, Kerberos és a megfelelő másodlagos címek hozzárendelésének engedélyezése a SharePoint a kezeli a bejövő kéréseket a belső URL-CÍMRE elküldött konfigurált kiterjesztése, és az azokra mutató hivatkozásokat tartalmaz a külső URL-cím számára készült.

1. Indítsa el a **SharePoint felügyeleti rendszerhéj**.
2. Futtassa a következő szkriptet a webalkalmazás extranetes zóna kiterjesztése és a Kerberos-hitelesítés engedélyezése:

  ```powershell
  # Replace "http://spsites/" with the URL of your web application
  # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
  $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
  Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
  ```

3. Nyissa meg a **SharePoint központi felügyelet** hely.
4. A **rendszerbeállítások**válassza **konfigurálása másodlagos címek leképezése**. Megnyílik a másodlagos címek leképezése mezőbe.
5. Válassza ki a helyet, például **SharePoint – 80-as**. Egyelőre extranetes zóna nincs megfelelően beállítva még belső URL-cím:

  ![Másodlagos címek leképezése mezőbe](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. Kattintson a **adja hozzá a belső URL-címek**.
7. A **protokoll, a gazdagép és a port URL-cím** szövegmezőbe írja be a **belső URL-cím** konfigurált Azure AD-proxyval, például <https://SharePoint/>.
8. Válassza ki a zóna **Extranet** a legördülő listában.
9. Kattintson a **Save** (Mentés) gombra.
10. A másodlagos címek leképezése most így kell kinéznie:

  ![Javítsa ki a másodlagos címek leképezése](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>4. lépés: Győződjön meg arról, hogy egy HTTPS-tanúsítványt az IIS-webhely, az extranetes zóna konfigurálása

A SharePoint konfigurációs van most már befejeződött, de mivel a belső URL-címét az extranetes zóna <https://SharePoint/>, tanúsítványt kell állítani ezen a helyen.

1. Nyissa meg a Windows PowerShell-konzolt.
2. Futtassa a következő parancsfájlt, és létrehozhat egy önaláírt tanúsítványt, és adja hozzá a számítógép saját tárolóban:

  ```powershell
  # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
  New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
  ```

  > [!NOTE]
  Önaláírt tanúsítványok csak tesztelési célra alkalmasak. Éles környezetben erősen ajánlott, ehelyett egy hitelesítésszolgáltató által kibocsátott tanúsítványokat használ.

3. Nyissa meg az "Internet Information Services kezelő" konzolt.
4. Bontsa ki a kiszolgálót, a fanézetben bontsa ki a "Hely", válassza ki a következő helyen: "A SharePoint – AAD Proxyszolgáltató", kattintson a **kötések**.
5. Válassza ki a https-kötést, és kattintson a **szerkesztése...** .
6. SSL-tanúsítvány mezőben válassza ki a **SharePoint** tanúsítvány, és kattintson az OK gombra.

Most már elérheti a SharePoint-webhely külsőleg Azure AD-alkalmazásproxy használatával.

## <a name="next-steps"></a>További lépések

* [Egyéni tartományok használata az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md)
* [Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)
