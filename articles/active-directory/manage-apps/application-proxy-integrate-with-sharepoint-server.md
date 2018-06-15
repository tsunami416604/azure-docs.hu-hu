---
title: Távoli hozzáférés SharePoint az Azure AD alkalmazásproxy engedélyezése |} Microsoft Docs
description: Alapvető tudnivalók a helyszíni SharePoint-kiszolgáló integrálása az Azure AD-alkalmazásproxy ismerteti.
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
ms.date: 09/06/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 49b5620fb95e9c67b0ff0e314534dc8499714b63
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34162098"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Az Azure AD alkalmazásproxy SharePoint távoli hozzáférés engedélyezése

A cikkből megtudhatja, hogyan integrálható a helyszíni SharePoint-kiszolgáló az Azure Active Directory (Azure AD) alkalmazásproxy.

Távelérés SharePoint az Azure AD alkalmazásproxy engedélyezéséhez kövesse az ebben a cikkben részletes szakaszok.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már rendelkezik a SharePoint 2013 vagy újabb a környezetben. Emellett vegye figyelembe a következő előfeltételek teljesülését:

* SharePoint natív Kerberos támogatja. Ezért elérő felhasználókat belső helyek távolról az Azure AD-proxyn keresztül történő egyszeri bejelentkezést (SSO) tapasztalatra veheti fel.

* Ez az eset tartalmazza a SharePoint-kiszolgáló konfigurációs módosításait. Átmeneti környezet használatát javasoljuk. Ezzel a módszerrel teheti frissítések átmeneti kiszolgálóhoz először, és egy tesztelési ciklust, mielőtt éles környezetben felhasználó majd megkönnyítése.

* A közzétett URL-címen SSL szükséges. Az SSL protokoll engedélyezve van a belső webhely arról, hogy hivatkozások küldött/leképezett megfelelően kell. Ha SSL még nincs konfigurálva, lásd: [SSL konfigurálása a SharePoint 2013 rendszerhez](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) utasításokat. Ellenőrizze azt is, hogy az összekötő gép megbíznak kiállítja a tanúsítványt. (A tanúsítvány nem kell nyilvánosan ki.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>1. lépés: Állítsa be az egyszeri bejelentkezés SharePoint

Windows-hitelesítést használó helyi alkalmazások esetén érhető el egyszeri bejelentkezés (SSO) a Kerberos hitelesítési protokoll és a Kerberos által korlátozott delegálás (KCD) nevezett szolgáltatással. A Kerberos által korlátozott, ha konfigurálva van, lehetővé teszi, hogy egy felhasználó Windows jogkivonat beszerzése az alkalmazásproxy-összekötő akkor is, ha a felhasználó még nem jelentkezett be a Windowsba közvetlenül. Kerberos által korlátozott Delegálás kapcsolatos további információkért lásd: [Kerberos által korlátozott delegálás áttekintése](https://technet.microsoft.com/library/jj553400.aspx).

Kerberos által korlátozott Delegálás beállítása a SharePoint server, az eljárásokkal a következő szekvenciális szakaszokban:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Győződjön meg arról, hogy a SharePoint szolgáltatásfiók alatt fut.

Először is győződjön meg arról, hogy a meghatározott szolgáltatási fiókkal--nem helyi rendszer, a helyi szolgáltatás vagy a hálózati szolgáltatás fut-e a SharePoint. Ehhez úgy, hogy az egyszerű szolgáltatásnevek (SPN) csatolhat egy érvényes fiókot. SPN-EK, hogyan a Kerberos protokoll azonosítja a különböző szolgáltatások. És a fiókot, hogy később a Kerberos által korlátozott Delegálás konfigurálásához szüksége lesz.

> [!NOTE]
Telepíteni kell egy korábban létrehozott Azure AD-fiókot a szolgáltatáshoz. Javasoljuk, hogy lehetővé tegye egy automatikus jelszóváltoztatáshoz. A lépéseket és a hibaelhárítási problémák az összes kapcsolatos további információkért lásd: [konfigurálása automatikus jelszó módosítása a SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

Győződjön meg arról, hogy a helyek egy meghatározott szolgáltatás fiók alatt fut, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a **SharePoint 2013 központi felügyelet** hely.
2. Ugrás a **biztonsági** válassza **szolgáltatásfiókok konfigurálása**.
3. Válassza ki **webalkalmazás-készlet - SharePoint - 80**. A beállítások kis mértékben eltérő lehet a webalkalmazás-készlet neve alapján, vagy ha a webes készlet alapértelmezés szerint az SSL.

  ![Lehetőségek a szolgáltatásfiók konfigurálása](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Ha **válassza ki a fiókot ehhez az összetevőhöz** mező értéke **helyi szolgáltatás** vagy **hálózati szolgáltatás**, fiók létrehozásához szükséges. Ha nem, akkor végzett, és továbbléphet a következő szakasszal.
5. Válassza ki **új felügyelt fiók regisztrálása**. A fiók létrehozása után meg kell adni **webes alkalmazáskészlet** a fiók használata előtt.

### <a name="configure-sharepoint-for-kerberos"></a>A Kerberos a SharePoint konfigurálása

Kerberos által korlátozott Delegálás segítségével hajtsa végre az egyszeri bejelentkezés a SharePoint-kiszolgálóra.

A SharePoint-webhely a Kerberos-hitelesítés konfigurálása:

1. Nyissa meg a **SharePoint 2013 központi felügyelet** hely.
2. Lépjen **Alkalmazáskezelés**, jelölje be **webes alkalmazásokat kezeléséhez**, és válassza ki a SharePoint-webhelye. Az ebben a példában is **SharePoint - 80**.

  ![A SharePoint-webhely kiválasztása](./media/application-proxy-integrate-with-sharepoint-server/manage-web-applications.png)

3. Kattintson a **hitelesítésszolgáltatókat** az eszköztáron.
4. Az a **hitelesítésszolgáltatókat** kattintson **alapértelmezett zóna** a beállítások megtekintéséhez.
5. Az a **hitelesítés szerkesztése** párbeszédpanel mezőben görgessen lefelé, amíg megjelenik **jogcímek hitelesítési típusok**. Győződjön meg arról, hogy mindkét **Windows-hitelesítés engedélyezése** és **integrált Windows-hitelesítés** van kiválasztva.
6. A legördülő listából, az integrált Windows-hitelesítés mező, ügyeljen arra, hogy **egyeztetés (Kerberos)** van kiválasztva.

  ![Hitelesítés szerkesztése párbeszédpanel](./media/application-proxy-integrate-with-sharepoint-server/service-edit-authentication.png)

7. Alján a **hitelesítés szerkesztése** párbeszédpanel, kattintson a **mentése**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Egy egyszerű szolgáltatásnévvel SharePoint szolgáltatásfiók beállítása

Kerberos által korlátozott Delegálás konfigurálása előtt kell a SharePoint szolgáltatás konfigurált szolgáltatásfiókként fut azonosítani. A szolgáltatás azonosítására úgy, hogy egy egyszerű Szolgáltatásnevet. További információkért lásd: [egyszerű szolgáltatásnevek](https://technet.microsoft.com/library/cc961723.aspx).

SPN formátuma:

```
<service class>/<host>:<port>
```

Az egyszerű Szolgáltatásnevet formátumban:

* _osztály szolgáltatás_ a szolgáltatás egyedi neve. A SharePoint, használhat **HTTP**.

* _állomás_ a teljes tartománynév vagy a gazdagép, amely a szolgáltatás fut a NetBIOS-nevét. A SharePoint-webhelyre Ez a szöveg lehet szükség az URL-címe, az IIS éppen használt verziójától függően.

* _port_ nem kötelező megadni.

Ha a SharePoint-kiszolgáló teljes Tartományneve:

```
sharepoint.demo.o365identity.us
```

Az egyszerű szolgáltatásnév akkor:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Is szükség lehet SPN-ek beállítása adott webhelyeken a kiszolgálón. További információkért lásd: [konfigurálja a Kerberos-hitelesítés](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Figyelmesen elolvassa az "A Kerberos-hitelesítést használó webalkalmazások létrehozása egyszerű szolgáltatásnevek" szakaszban.

Ahhoz, hogy az SPN-ek beállítása a legegyszerűbb módja, kövesse a SPN-formátumok, amely már jelen, a helyek lehetnek. A szolgáltatás fiók regisztrálása e SPN-ek másolja. Ehhez tegye a következőket:

1. Tallózással keresse meg az egyszerű Szolgáltatásnevet a helyhez egy másik számítógépről.
 Ha így tesz, a Kerberos-jegyek vonatkozó készlete gyorsítótárazza a számítógépen. Ezek a jegyek vetítéséhez webhely SPN tartalmaznak.

2. Nevű eszköz használatával lehet lekérni a webhelyre vonatkozóan, az egyszerű szolgáltatásnév [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). Ugyanabban a környezetben, a felhasználó a webhely a böngészőben futó parancsablakban futtassa a következő parancsot:
```
Klist
```
Klist majd készletet ad vissza, a cél SPN-ek. Ebben a példában a kijelölt érték az SPN szükséges:

  ![Példa Klist eredménye](./media/application-proxy-integrate-with-sharepoint-server/remote-sharepoint-target-service.png)

4. Most, hogy az egyszerű Szolgáltatásnevet, győződjön meg arról, hogy megfelelően van konfigurálva a szolgáltatás fiók a webes alkalmazás korábban beállított. A következő parancsot a parancssorból futtassa a tartomány rendszergazdája:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Ez a parancs beállítja a SharePoint-szolgáltatás futtatásához használt fiók egyszerű Szolgáltatásnevét _demo\sp_svc_.

 Cserélje le _http/sharepoint.demo.o365identity.us_ van a Célszámítógéphez, a kiszolgáló és _demo\sp_svc_ a szolgáltatásfiókkal a környezetben. A Setspn parancs megkeresi az egyszerű Szolgáltatásnevet, mielőtt hozzáadja azt. Ebben az esetben előfordulhat, hogy megjelenik egy **ismétlődő SPN-értéket** hiba. Ha ezt a hibaüzenetet látja, győződjön meg arról, hogy az érték a szolgáltatás fiókhoz.

Ellenőrizheti, hogy az egyszerű Szolgáltatásnevet a Setspn parancs futtatásával a -l beállítással lett hozzáadva. Ez a parancs kapcsolatos további információkért lásd: [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Győződjön meg arról, hogy az összekötő megbízható meghatalmazottként értéke SharePoint

Konfigurálja a Kerberos által korlátozott Delegálás, hogy az Azure AD alkalmazásproxy delegálhatja a SharePoint szolgáltatás felhasználói identitásokat. Azáltal, hogy a felhasználók, akik az Azure Active Directory hitelesített Kerberos-jegyek beolvasása az alkalmazásproxy-összekötő Kerberos által korlátozott Delegálás konfigurálása Majd, hogy a kiszolgáló továbbítja a környezetben a célalkalmazás vagy a SharePoint ebben az esetben.

A Kerberos által korlátozott Delegálás konfigurálásához ismételje meg minden összekötő gép a következő lépéseket:

1. Jelentkezzen be a tartományvezérlőbe tartományi rendszergazdaként, és nyisson **Active Directory – felhasználók és számítógépek**.
2. Az összekötőt futtató számítógépen található. Az ebben a példában is ugyanarra a SharePoint-kiszolgálóra.
3. Kattintson duplán arra a számítógépre, és kattintson a **delegálás** fülre.
4. Győződjön meg arról, hogy a delegálás beállítása **a számítógépen csak a megadott szolgáltatások delegálhatók**. Ezt követően válassza **bármely hitelesítési protokoll**.

  ![A delegálási beállításokat](./media/application-proxy-integrate-with-sharepoint-server/delegation-box.png)

5. Kattintson a **Hozzáadás** gombra, majd **felhasználók vagy számítógépek**, és keresse meg a szolgáltatás fiók.

  ![A szolgáltatásfiók SPN hozzáadása](./media/application-proxy-integrate-with-sharepoint-server/users-computers.png)

6. Válassza ki azt, amelyik a fiók korábban létrehozott egyszerű szolgáltatásnevek listájának megtekintéséhez.
7. Kattintson az **OK** gombra. Kattintson a **OK** újra, hogy a módosítások mentéséhez.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>2. lépés: A SharePoint távoli hozzáférés engedélyezése

Most, hogy a SharePoint Kerberos és a beállított Kerberos által korlátozott Delegálás engedélyezését, készen áll az Azure AD-proxyn keresztül történő távoli hozzáférés a SharePoint-farm közzététele.

1. Tegye közzé a SharePoint-webhely, a következő beállításokkal. Részletes útmutatásért lásd: [az Azure AD-alkalmazásproxy használó alkalmazások közzététele](application-proxy-publish-azure-portal.md). 
   - **Belső URL-cím**: a SharePoint-webhely URL-CÍMÉT belső, például **https://SharePoint/**. Ebben a példában, ügyeljen arra, hogy használjon **https**
   - **Előhitelesítési módszer**: az Azure Active Directory
   - **URL-címet a fejlécekben fordítása**: nincs

   >[!TIP]
   >SharePoint használja a _állomásfejléc_ megkeresheti a hely értékét. Emellett ez az érték alapján hivatkozásokat hoz létre. A nettó hatása az, hogy minden hivatkozás, amely SharePoint-hoz létre-e a közzétett URL-cím helyesen van-e állítva a külső URL-cím használatára. Az érték **Igen** is lehetővé teszi, hogy az összekötő továbbítja a kérést a háttér-alkalmazás. Azonban a értékre állítását **nem** azt jelenti, hogy az összekötő nem küld a belső neve. Ehelyett az összekötő elküldi az állomásfejléc a közzétett URL-címként a háttér-alkalmazásnak.

   ![SharePoint alkalmazás közzététele](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Miután közzétette az alkalmazást, az egyszeri bejelentkezés beállítások konfigurálása a következő lépéseket:

   1. A portál alkalmazás lapon válassza az **egyszeri bejelentkezés**.
   2. Egyszeri bejelentkezés módot, válassza a **integrált Windows-hitelesítés**.
   3. Belső alkalmazás SPN értékre a korábban beállított értékeket. Ehhez a példához, amely lenne **http/sharepoint.demo.o365identity.us**.

   ![Az egyszeri bejelentkezés integrált Windows-hitelesítés konfigurálása](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Az alkalmazás beállításának befejezéséhez, keresse fel a **felhasználók és csoportok** szakaszt, és rendelje hozzá a felhasználók az alkalmazás eléréséhez. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>3. lépés: Győződjön meg arról, hogy a SharePoint ismer a külső URL-címe

Az utolsó lépése annak érdekében, hogy a SharePoint található a helyhez, a külső URL-címe alapján, hogy a külső URL-cím alapján hivatkozások miatt. Ehhez a SharePoint-webhely másodlagos hozzáférés-leképezések konfigurálása.

1. Nyissa meg a **SharePoint 2013 központi felügyelet** hely.
2. A **rendszerbeállítások**, jelölje be **alternatív leképezések konfigurálása**. A másodlagos hozzáférési megfeleltetések gombra.

  ![Másodlagos hozzáférési megfeleltetések mezőbe](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

3. A legördülő lista melletti **másodlagos hozzáférési leképezési gyűjtemény**, jelölje be **módosítás másodlagos hozzáférési leképezési gyűjtemény**.
4. Válassza ki a webhely – például **SharePoint - 80**.
5. Ha szeretné, adja hozzá a közzétett URL-címet vagy egy belső URL-CÍMÉT, vagy egy nyilvános URL-címet. A példa egy nyilvános URL-címet, az extranetről.
6. Kattintson a **szerkesztése nyilvános URL-címek** a a **Extranet** elérési utat, majd adja meg a külső URL-cím, az alkalmazás közzétételekor létrehozott. Adja meg például **https://sharepoint-iddemo.msappproxy.net**.

  ![Az elérési út megadása](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

7. Kattintson a **Save** (Mentés) gombra.

A SharePoint-webhely, az Azure AD-alkalmazásproxy külsőleg keresztül érhetők el.

## <a name="next-steps"></a>További lépések

- [Egyéni tartományok az Azure AD alkalmazásproxy használata](application-proxy-configure-custom-domain.md)
- [Az Azure AD-alkalmazásproxy összekötők ismertetése](application-proxy-connectors.md)

