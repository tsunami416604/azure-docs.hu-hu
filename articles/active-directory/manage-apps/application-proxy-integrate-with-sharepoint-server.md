---
title: Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezése |} A Microsoft Docs
description: Egy helyszíni SharePoint-kiszolgáló integrálása az Azure AD-alkalmazásproxy alapjait ismerteti.
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
ms.date: 10/19/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 0f1b46176ba440a11d1584846019859c63d2f263
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135780"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezése

Ez a cikk ismerteti, hogyan lehet egy helyszíni SharePoint-kiszolgáló integrálása az Azure Active Directory (Azure AD) alkalmazásproxy használatával.

Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezéséhez kövesse az ebben a cikkben lépésről lépésre szakaszok.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már rendelkezik a SharePoint 2013-as vagy újabb a környezetben. Ezenkívül vegye figyelembe a következő előfeltételek vonatkoznak:

* A SharePoint natív Kerberos-támogatást tartalmaz. Ezért hozzáférő felhasználók belső helyek távolról az Azure AD-alkalmazásproxyn keresztül feltételezheti, hogy az egyszeri bejelentkezés (SSO) felhasználói élményt.

* Ebben a forgatókönyvben a SharePoint-kiszolgáló konfigurációs módosításait tartalmazza. Azt javasoljuk, hogy egy átmeneti környezet használatával. Ezzel a módszerrel, először az átmeneti kiszolgálón hajtsa végre frissítéseket, és ezután megkönnyítik az éles környezetben való elhelyezés előtt tesztelési ciklust.

* A közzétett URL-cím SSL szükség van. A belső webhely annak érdekében, hogy hivatkozásokat küldött/leképezve megfelelően engedélyezve van az SSL kell. Ha még nincs konfigurálva SSL, [SSL konfigurálása a SharePoint 2013-hoz készült](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) útmutatást. Győződjön meg arról, hogy az összekötő gép megbízhatónak tekinti a tanúsítványt, amely ki. (A tanúsítvány nem kell nyilvánosan állítanak ki.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>1. lépés: Állítsa be egyszeri bejelentkezést a Sharepointhoz

Windows-hitelesítést használó helyszíni alkalmazások akkor érhető el, egyszeri bejelentkezés (SSO) a Kerberos hitelesítési protokoll és a Kerberos által korlátozott delegálás (KCD) szolgáltatás. KCD, ha konfigurálva van, lehetővé teszi, hogy egy felhasználó egy Windows-jogkivonat beszerzése az Application Proxy connector akkor is, ha a felhasználó még nem jelentkezett be a Windows közvetlenül. KCD kapcsolatos további információkért lásd: [Kerberos által korlátozott delegálás áttekintése](https://technet.microsoft.com/library/jj553400.aspx).

Egy SharePoint-kiszolgáló beállítása a kcd Szolgáltatáshoz, az eljárásokkal a következő egymást követő szakaszokban:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Győződjön meg arról, hogy a SharePoint szolgáltatásfiók alatt fut

Első lépésként ellenőrizze, hogy a SharePoint fut-e egy meghatározott szolgáltatási fiók – nem helyi, helyi vagy hálózati szolgáltatást. Ehhez úgy, hogy az egyszerű szolgáltatásnevek (SPN) csatlakoztathat egy érvényes fiókot. SPN-EK, hogyan azonosítja a Kerberos protokoll a különböző szolgáltatásokat. És a használatával konfigurálja a Kerberos-fiók szükséges.

> [!NOTE]
Szüksége lesz egy korábban létrehozott Azure AD-fiókot a szolgáltatáshoz. Javasoljuk, hogy engedélyezze az automatikus jelszóváltoztatáshoz. Azokat a lépéseket és a hibaelhárítási problémák teljes kapcsolatos további információkért lásd: [jelszavának automatikus módosítását konfigurálása a SharePoint 2013-ban](https://technet.microsoft.com/library/ff724280.aspx).

Győződjön meg arról, hogy a helyek egy meghatározott szolgáltatás fiók alatt futnak, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a **SharePoint 2013 központi felügyeleti** hely.
2. Lépjen a **biztonsági** válassza **szolgáltatásfiókok konfigurálása**.
3. Válassza ki **webalkalmazás-készlet - SharePoint – 80-as**. A beállítások némileg eltérőek lehetnek a webalkalmazás-készlet neve alapján, vagy ha a webes készletet használja az SSL-alapértelmezés szerint.

  ![Lehetőségek a szolgáltatásfiók konfigurálása](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Ha **válassza ki a fiókot ehhez az összetevőhöz** mező értéke **helyi szolgáltatás** vagy **hálózati szolgáltatás**, hozzon létre egy fiókot kell. Ha nem, akkor ezzel végzett, és áthelyezheti a következő szakaszra.
5. Válassza ki **új felügyelt fiók regisztrálása**. Miután létrehozta a fiókot, be kell állítania **webes alkalmazáskészlet** a fiók használata előtt.

### <a name="configure-sharepoint-for-kerberos"></a>A Kerberos a SharePoint konfigurálása

Egyszeri bejelentkezés a SharePoint-kiszolgálóra való végrehajtásához használhatja a kcd Szolgáltatáshoz.

A SharePoint-webhely a Kerberos-hitelesítés konfigurálása:

1. Nyissa meg a **SharePoint 2013 központi felügyeleti** hely.
2. Lépjen a **Alkalmazáskezelés**válassza **webes alkalmazások kezelése**, és válassza ki a SharePoint-webhely. Ebben a példában van **SharePoint – 80-as**.

  ![A SharePoint-webhely kiválasztása](./media/application-proxy-integrate-with-sharepoint-server/manage-web-applications.png)

3. Kattintson a **hitelesítésszolgáltatók** az eszköztáron.
4. Az a **hitelesítésszolgáltatók** kattintson **alapértelmezett zóna** megadott beállítások megjelenítéséhez.
5. Az a **hitelesítés szerkesztése** párbeszédpanel mezőben görgessen lefelé, amíg meg nem látja **jogcímek hitelesítési típusok**. Ügyeljen arra, hogy mindkét **Windows-hitelesítés engedélyezése** és **integrált Windows-hitelesítés** ki van jelölve.
6. Az integrált Windows-hitelesítés mező legördülő mezőben, győződjön meg arról, hogy **egyeztetés (Kerberos)** van kiválasztva.

  ![Szerkessze a hitelesítési párbeszédpanelt](./media/application-proxy-integrate-with-sharepoint-server/service-edit-authentication.png)

7. Alsó részén a **hitelesítés szerkesztése** párbeszédpanelen kattintson a **mentése**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Állítsa be a szolgáltatásnév a SharePoint-szolgáltatásfiókhoz

Mielőtt beállítaná a kcd Szolgáltatáshoz, a SharePoint szolgáltatás konfigurált szolgáltatásfiókként fut azonosítania kell. A szolgáltatás egyszerű Szolgáltatásneve beállításával azonosítására. További információkért lásd: [egyszerű szolgáltatásnevek](https://technet.microsoft.com/library/cc961723.aspx).

Egyszerű szolgáltatásnév formátuma:

```
<service class>/<host>:<port>
```

Az egyszerű szolgáltatásnév formátuma:

* _osztály szolgáltatás_ a szolgáltatás egy egyedi név. A SharePoint, használhat **HTTP**.

* _gazdagép_ a teljes tartománynév vagy NetBIOS-neve, amelyen a szolgáltatás fut, a gazdagép. Egy SharePoint-hely URL-címét a webhely az IIS használt verziójától függően elképzelhető ezt a szöveget.

* _port_ nem kötelező.

Ha a SharePoint-kiszolgáló teljes Tartománynevét:

```
sharepoint.demo.o365identity.us
```

Majd az egyszerű szolgáltatásnév van:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Szükség lehet SPN-ek beállítása adott webhelyeken a kiszolgálón. További információkért lásd: [konfigurálja a Kerberos-hitelesítés](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Az "A Kerberos-hitelesítést használó webalkalmazások létrehozása egyszerű szolgáltatásnevek" szakaszban figyeljen.

A legegyszerűbb módja, hogy az SPN-ek beállítása, hogy az SPN-formátumok, amelyek esetleg már megtalálható a helyek kövesse. Másolja ki ezeket SPN-eket regisztrálni a fiók ellen. Ehhez tegye a következőket:

1. Keresse meg az egyszerű szolgáltatásnév az a hely másik gépről.
 Ha így tesz, a Kerberos-jegyekhez megfelelő készletét gyorsítótárazza a gépen. Ezeket a jegyeket a célhelyhez vetítéséhez egyszerű Szolgáltatásnevének tartalmaz.

2. Az adott helyhez tartozó egyszerű szolgáltatásnév nevű eszköz használatával kérheti le [Klist](https://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). Egy parancssori ablakban, amely ugyanabban a környezetben, ki fért hozzá a webhelyet a böngészőben a felhasználóként fut-e futtassa a következő parancsot:
```
Klist
```
Klist majd cél SPN-ek készletét adja vissza. Ebben a példában a kijelölt érték szükséges egyszerű szolgáltatásnév:

  ![Példa Klist eredmények](./media/application-proxy-integrate-with-sharepoint-server/remote-sharepoint-target-service.png)

4. Most, hogy az egyszerű Szolgáltatásnevet, győződjön meg arról, hogy megfelelően van konfigurálva a szolgáltatásfiókhoz, amelyet korábban a webes alkalmazás beállítása a. A tartomány-rendszergazdaként futtassa a következő parancsot a parancssorba:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Ez a parancs beállítja a SharePoint-szolgáltatást futtató fiók egyszerű Szolgáltatásnevét _demo\sp_svc_.

 Cserélje le _http/sharepoint.demo.o365identity.us_ az a kiszolgáló egyszerű Szolgáltatásnevének és _demo\sp_svc_ a szolgáltatási fiókkal a környezetben. A Setspn parancs keres az egyszerű Szolgáltatásnevet, mielőtt hozzáadja azt. Ebben az esetben előfordulhat, hogy lát egy **ismétlődő SPN érték** hiba. Ha ezt a hibaüzenetet, győződjön meg arról, hogy az érték a szolgáltatás-fiókhoz társítva.

Ellenőrizheti, hogy az egyszerű Szolgáltatásnevet a Setspn parancs futtatásával a -l lehetőséggel jelent. Ezzel a paranccsal kapcsolatos további információkért lásd: [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Győződjön meg arról, hogy az összekötő megbízható meghatalmazottként értéke SharePoint

Állítsa a kcd Szolgáltatáshoz, hogy az Azure AD-alkalmazásproxy szolgáltatás delegálhatja a felhasználói identitások a SharePoint szolgáltatáshoz. Konfigurálja a kcd Szolgáltatáshoz beolvasni a felhasználók, akik az Azure ad-ben hitelesített Kerberos-jegyet az Application Proxy connector engedélyezésével. Majd, hogy a kiszolgáló továbbítja a környezetet a célalkalmazás vagy a SharePoint ebben az esetben.

Konfigurálja a Kerberos, minden összekötő géphez ismételje meg a következő lépéseket:

1. Jelentkezzen be egy tartományvezérlőre tartományi rendszergazdaként, és nyissa meg **Active Directory – felhasználók és számítógépek**.
2. Az összekötőt futtató számítógépen található. Ebben a példában ugyanazon a SharePoint-kiszolgálón.
3. Kattintson duplán arra a számítógépre, és kattintson a **delegálás** fülre.
4. Győződjön meg arról, hogy a delegálási beállítások beállítása **számítógépen csak a megadott szolgáltatások delegálhatók**. Ezután válassza ki **bármely hitelesítési protokoll**.

  ![A delegálási beállítások](./media/application-proxy-integrate-with-sharepoint-server/delegation-box.png)

5. Kattintson a **Hozzáadás** gombra, majd **felhasználók vagy számítógépek**, és keresse meg a szolgáltatás-fiókot.

  ![A szolgáltatásfiók egyszerű Szolgáltatásnevének hozzáadása](./media/application-proxy-integrate-with-sharepoint-server/users-computers.png)

6. SPN-ek listájában válassza ki a korábban létrehozott szolgáltatásfiók.
7. Kattintson az **OK** gombra. Kattintson a **OK** újra, hogy a módosítások mentéséhez.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>2. lépés: Engedélyezze a távoli hozzáférés a Sharepointhoz

Most, hogy a SharePoint Kerberos és a beállított KCD engedélyezését, készen áll az Azure AD-alkalmazásproxyn keresztül távoli hozzáférés a SharePoint-farm közzététele.

1. Közzététel a SharePoint-webhely a következő beállításokkal. Lépésenkénti útmutatásért lásd: [közzététel az Azure AD-alkalmazásproxy használatával](application-proxy-add-on-premises-application.md). 
   - **Belső URL-cím**: a SharePoint-webhely URL-CÍMÉT a cégen belül, például **https://SharePoint/**. Ebben a példában győződjön meg arról, használandó **https**
   - **Előhitelesítési módszer**: az Azure Active Directory
   - **A fejlécek URL-cím fordításának**: nem

   >[!TIP]
   >A SharePoint használja a _állomásfejléc_ érték keresse ki a helyet. Ez az érték alapján hivatkozásokat is állít elő. Az eredő hatás, hogy minden olyan hivatkozás, amely a SharePoint hoz létre egy közzétett URL-címet, amely a külső URL-cím használata megfelelően vannak beállítva. Az érték **Igen** is lehetővé teszi, hogy az összekötő továbbítja a kérést a háttéralkalmazás. Azonban érték beállítása **nem** azt jelenti, hogy az összekötő nem küld a belső neve. Ehelyett az összekötő küld az állomásfejléc közzétett URL-címként a háttéralkalmazás.

   ![Alkalmazás közzététele SharePoint](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Az alkalmazás közzététele után konfigurálja az egyszeri bejelentkezés beállításai az alábbi lépéseket:

   1. Az alkalmazás oldalán a portálon, válassza **egyszeri bejelentkezési**.
   2. Válassza az egyszeri bejelentkezési mód, **integrált Windows-hitelesítés**.
   3. Belső alkalmazás egyszerű Szolgáltatásnevét, amely a korábban beállított értékre állítva. Ebben a példában ez lenne **http/sharepoint.demo.o365identity.us**.

   ![Egyszeri bejelentkezés az integrált Windows-hitelesítés konfigurálása](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Az alkalmazás beállításának befejezéséhez, nyissa meg a **felhasználók és csoportok** szakaszt, és hozzárendelhet felhasználókat az alkalmazás eléréséhez. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>3. lépés: Győződjön meg arról, hogy a SharePoint ismer a külső URL-cím

Az utolsó lépéseként győződjön meg arról, hogy a SharePoint találhatja a hely, a külső URL-cím alapján, hogy a külső URL-cím alapján hivatkozások vártak. Ehhez a SharePoint-hely másodlagos címek leképezése konfigurálása.

1. Nyissa meg a **SharePoint 2013 központi felügyeleti** hely.
2. A **rendszerbeállítások**válassza **konfigurálása másodlagos címek leképezése**. Megnyílik a másodlagos címek leképezése mezőbe.

  ![Másodlagos címek leképezése mezőbe](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

3. A legördülő lista melletti **másodlagos hozzáférési leképezési gyűjteményt**válassza **módosítása másik hozzáférési leképezési gyűjteményt**.
4. Válassza ki a webhely – például **SharePoint – 80-as**.
5. Ha szeretné, adja hozzá a közzétett URL-cím egy belső URL-cím vagy egy nyilvános URL-címet. Ebben a példában egy nyilvános URL-címet használ, az extranetről. Ha egy egyéni portot ügyeljen arra, hogy tartalmazzák az egyéni portot az URL-cím használja.
6. Kattintson a **szerkesztése nyilvános URL-címek** a a **Extranet** elérési utat, majd adja meg a külső URL-cím, az alkalmazás közzétételekor létrehozott. Adja meg például **https://sharepoint-iddemo.msappproxy.net**.

  ![Az elérési út megadása](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

7. Kattintson a **Save** (Mentés) gombra.

Most már elérheti a SharePoint-webhely külsőleg Azure AD-alkalmazásproxy használatával.

## <a name="next-steps"></a>További lépések

- [Egyéni tartományok használata az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md)
- [Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)

