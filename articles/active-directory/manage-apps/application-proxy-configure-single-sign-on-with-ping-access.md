---
title: Fejlécalapú hitelesítéskor a pingaccess segítségével az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Tegye közzé az alkalmazásokat a PingAccess és alkalmazásproxy fejléc-alapú hitelesítés támogatásához.
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
ms.date: 10/11/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 53d613052713e5144cb9d96aeeb937392fd4736a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902926"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Fejlécalapú hitelesítéskor az egyszeri bejelentkezést az alkalmazásproxy és a PingAccess

Az Azure Active Directory Application Proxy és a PingAccess szállíthatnak együtt az Azure Active Directory-ügyfelek hozzáférést még több alkalmazás is. PingAccess kibővíti a [meglévő Application Proxy ajánlatait](application-proxy.md) egyszeri bejelentkezés alkalmazásokhoz való hozzáférést, amelyek a hitelesítési fejléceket tartalmazza.

## <a name="what-is-pingaccess-for-azure-ad"></a>Mi az az Azure AD-alapú PingAccess?

Azure Active Directory-alapú PingAccess egy szolgáltatása, amely lehetővé teszi, hogy a felhasználók hozzáférést és egyszeri bejelentkezést biztosíthat a hitelesítési fejléceket használó alkalmazások PingAccess. Application Proxy ezeket az alkalmazásokat, mint bármely más, az Azure AD segítségével hitelesíti a hozzáférést, majd az összekötő szolgáltatás forgalmát kezeli. PingAccess helyezkedik el az alkalmazásokat, és a hozzáférési jogkivonatot az Azure ad-ből fordítja fejlécet, hogy az alkalmazás fogad a hitelesítés, olvasható formátumban.

A felhasználók nem figyelje meg, hogy bármi más, amikor bejelentkeznek a vállalati alkalmazások használatához. Továbbra is dolgozhatnak bárhol, bármilyen eszközön. 

Mivel az alkalmazásproxy-összekötők közvetlen távoli forgalmat az összes alkalmazásba, függetlenül azok hitelesítési típus, azok továbbra automatikusan, illetve webhelyen végezhet terheléselosztást.

## <a name="how-do-i-get-access"></a>Hogyan szerezhetem be a hozzáférést?

Ebben a forgatókönyvben egy Azure Active Directory és PingAccess közötti partneri keretében érhető el, mivel-licenc szükséges mindkét szolgáltatás esetében. Azonban az Azure Active Directory Premium-előfizetések is tartalmazzák, egy alapszintű PingAccess licenc, amely lefedi a akár 20 alkalmazáshoz. Ha több mint 20 fejléc-alapú alkalmazások közzétételét van szüksége, további PingAccess lehet vásárolni. 

További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások).

## <a name="publish-your-application-in-azure"></a>Az alkalmazás közzététele az Azure-ban

Ez a cikk szól, akik közzétenni egy alkalmazást ebben a forgatókönyvben az első alkalommal. Megtudhatja, hogyan – első lépések alkalmazás és a PingAccess, mellett a közzétételi lépéseket mutatja be. Ha már konfigurálta a mindkét szolgáltatás, de a közzétételi lépéseket felelevenítéséhez, kihagyja az összekötő telepítését, és helyezze át a [adja hozzá az alkalmazás az Azure AD-alkalmazásproxyval](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Mivel ebben a forgatókönyvben az Azure AD közötti partneri és PingAccess, bizonyos utasítások találhatók a Ping Identity webhelyen.

### <a name="install-an-application-proxy-connector"></a>Az alkalmazásproxy-összekötő telepítése

Ha még alkalmazásproxy engedélyezve van, és egy összekötő telepítve van, kihagyhatja ezt a szakaszt, és helyezze át a [adja hozzá az alkalmazás az Azure AD-alkalmazásproxyval](#add-your-app-to-azure-ad-with-application-proxy).

Az Application Proxy connector egy Windows Server-szolgáltatás, amely arra utasítja a közzétett alkalmazások a távoli alkalmazottak érkező forgalom. További részletes telepítési utasításokat: [alkalmazásproxy engedélyezése az Azure Portalon](application-proxy-enable.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.
2. Válassza ki **Azure Active Directory** > **alkalmazásproxy**.
3. Válassza ki **-összekötő letöltése** az Application Proxy connector letöltése elindításához. Kövesse a telepítési utasításokat.

   ![Engedélyezze az alkalmazásproxyt, és a-összekötő letöltése](./media/application-proxy-configure-single-sign-on-with-ping-access/install-connector.png)

4. Az összekötő letöltésével kell automatikusan alkalmazásproxy engedélyezése a címtárban, de ha nem, válassza a **alkalmazásproxy engedélyezése**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Az alkalmazás hozzáadása az Azure AD-alkalmazásproxyval

Nincsenek két műveletet kell tennie az Azure Portalon. Először az alkalmazásproxy használatával az alkalmazás közzétételéhez. Ezt követően kell gyűjtenie némi információt az alkalmazást a PingAccess lépések során használható.

Kövesse az alábbi lépéseket az alkalmazás közzétételéhez. Egy részletes útmutató lépéseit, 1 – 8., lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy](application-proxy-publish-azure-portal.md).

1. Ha még nem az utolsó szakaszban, jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazdaként.
2. Válassza ki **Azure Active Directory** > **vállalati alkalmazások**.
3. Válassza ki **Hozzáadás** a panel tetején.
4. Válassza ki **helyszíni alkalmazás**.
5. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkat. A következő útmutatást használhatja a beállításokat:
   - **Belső URL-cím**: normál esetben adja meg az URL-cím, amellyel az alkalmazás bejelentkezési oldalon a vállalati hálózaton dolgozik. Ebben a forgatókönyvben az összekötőjét a PingAccess proxy kezelni a az alkalmazás első lapját. Ezt a formátumot használja: `https://<host name of your PA server>:<port>`. A port 3000 alapértelmezés szerint, de a PingAccess konfigurálhatja.

    > [!WARNING]
    > Az ilyen típusú egyszeri Bejelentkezést a belső URL-cím HTTPS protokollt kell használnia, és nem használható http.

   - **Az előhitelesítési módszer**: Azure Active Directory
   - **A fejlécek URL-cím fordításának**: nem

   >[!NOTE]
   >Ha az első alkalmazását, használja a port 3000 elindításához, és térjen vissza a frissítse ezt a beállítást, ha a PingAccess konfiguráció módosítása. Ha ez a második vagy újabb alkalmazás, ez a figyelő a PingAccess konfigurálta megfelelően kell. Tudjon meg többet [figyelői a PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Válassza ki **Hozzáadás** a panel alján. Az alkalmazás kerül, és megnyílik a gyors üzembe helyezési menü.
7. A gyors üzembe helyezési menüben válassza ki a **felhasználó hozzárendelése teszteléshez**, és legalább egy felhasználót az alkalmazáshoz. Ellenőrizze, hogy a teszt a helyszíni alkalmazás hozzáféréssel rendelkezik.
8. Válassza ki **hozzárendelése** a teszt felhasználó-hozzárendelés mentése.
9. Az alkalmazás felügyeleti panelen válassza ki a **egyszeri bejelentkezési**.
10. Válasszon **fejlécalapú bejelentkezés** a legördülő menüből. Kattintson a **Mentés** gombra.

   >[!TIP]
   >Ha ez az első alkalommal használja a fejléc-alapú egyszeri bejelentkezés, a PingAccess telepíteni szeretné. Ahhoz, hogy az Azure-előfizetése a PingAccess telepítés automatikusan társítva, az egyszeri bejelentkezési oldalon a hivatkozás segítségével töltse le a PingAccess. Most nyissa meg a letöltési oldalon, vagy térjen vissza erre a lapra később. 

   ![Fejlécalapú bejelentkezés kiválasztása](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.PNG)

11. Zárja be a vállalati alkalmazások panelen vagy görgessen a bal oldalon, hogy térjen vissza az Azure Active Directory menü.
12. Válassza az **Alkalmazásregisztrációk** elemet.

   ![Alkalmazásregisztrációk kiválasztása](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)

13. Válassza ki az imént hozzáadott alkalmazás, majd **válasz URL-címek**.

   ![Válassza ki a válasz URL-címek](./media/application-proxy-configure-single-sign-on-with-ping-access/reply-urls.png)

14. Ellenőrizze, hogy a külső URL-cím, amelyet az 5. lépésben az alkalmazáshoz rendelt-e a válasz URL-címek listájában. Ha nem, vegye fel azt.
15. Az alkalmazás beállítások panelen válassza ki a **szükséges engedélyek**.

  ![Válassza ki a szükséges engedélyek](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

16. Válassza a **Hozzáadás** lehetőséget. Az API-hoz, válassza ki a **Windows Azure Active Directory**, majd **kiválasztása**. Az engedélyek kiválasztása **olvasási és írása az összes alkalmazás** és **jelentkezzen be és felhasználói profil olvasása**, majd **válassza** és **kész**.  

  ![Engedélyek kijelölése](./media/application-proxy-configure-single-sign-on-with-ping-access/select-permissions.png)

17. Engedélyek megadása az engedélyek képernyő bezárása előtt. 
![Engedélyek megadása](./media/application-proxy-configure-single-sign-on-with-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>A PingAccess lépéseket-adatainak összegyűjtése

1. Az alkalmazás beállítások panelen válassza ki a **tulajdonságok**. 

  ![Tulajdonságok kiválasztása](./media/application-proxy-configure-single-sign-on-with-ping-access/properties.png)

2. Mentse a **alkalmazásazonosító** értéket. Az ügyfél-azonosító szolgál a PingAccess konfigurálásakor.
3. Az alkalmazás beállítások panelen válassza ki a **kulcsok**.

  ![Kulcsok kiválasztása](./media/application-proxy-configure-single-sign-on-with-ping-access/Keys.png)

4. Hozzon létre egy kulcsot a fő leírás, majd válassza a lejárati dátumot a legördülő menüből.
5. Kattintson a **Mentés** gombra. Megjelenik egy GUID Azonosítót a **érték** mező.

  Mentse ezt az értéket, akkor láthatom újra ez az ablak bezárása után.

  ![Új kulcs létrehozása](./media/application-proxy-configure-single-sign-on-with-ping-access/create-keys.png)

6. Zárja be a regisztrációk panelére vagy görgessen a bal oldalon, hogy térjen vissza az Azure Active Directory menü.
7. Válassza ki **tulajdonságok**.
8. Mentse a **címtár-azonosító** GUID azonosítója.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Nem kötelező – a frissítés GraphAPI küldése egyéni mezők

Az Azure AD elküldi a hitelesítéshez, a biztonsági jogkivonatok listáját lásd: [az Azure AD-jogkivonatok referenciájából](../develop/v1-id-and-access-tokens.md). Ha egy egyéni jogcímet, amely küld más jogkivonatok van szüksége, Graph Explorer vagy a jegyzékfájl az alkalmazás az Azure Portalon beállításához használja az alkalmazás mező *acceptMappedClaims* való **igaz**.    

Ez a példa Graph Explorer használja:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
Ebben a példában a [az Azure portal](https://portal.azure.com) való végigjárni a *acceptedMappedClaims* mező:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.
2. Válassza ki **Azure Active Directory** > **alkalmazásregisztrációk**.
3. Válassza ki az alkalmazását > **Manifest**.
4. Válassza ki **szerkesztése**, keresse meg a *acceptedMappedClaims* mezőben, és módosítsa az értéket **igaz**.
![Manifest aplikace](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-ping-access-manifest.PNG)
1. Kattintson a **Mentés** gombra.

>[!NOTE]
>Egyéni jogcím használatával is rendelkeznie kell meghatározott, és az alkalmazáshoz hozzárendelt egyéni házirendet.  Ez a szabályzat tartalmaznia kell az összes szükséges egyéni attribútumokat.
>
>Szabályzat-definíció és a hozzárendelés végezhető el a PowerShell, az Azure AD Graph Explorer vagy az MS Graph.  Ha ez a PowerShell, szüksége lehet, hogy először a `New-AzureADPolicy `, és hozzárendelheti az alkalmazást a `Set-AzureADServicePrincipalPolicy`.  További információ: a [Azure AD-Policy dokumentációja](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Nem kötelező – egyéni jogcím használata
Ahhoz, hogy az alkalmazás egy egyéni jogcímszabályok használja, és további mezőket tartalmaznak, hogy rendelkezik-e is lehet [egy egyéni jogcímek társítása a házirend létrehozása és hozzárendelése, az alkalmazás](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>PingAccess letöltéséhez, és az alkalmazás konfigurálása

Most, hogy végrehajtotta az összes Azure Active Directory-beállítási lépéseket, áthelyezheti PingAccess konfigurálása. 

A részletes lépéseket ebben a forgatókönyvben a PingAccess része továbbra is a Ping Identity dokumentáció [az Azure AD-alapú PingAccess konfigurálása](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Ezek a lépések végigvezetik a folyamat első egy PingAccess fiókot, ha még nem rendelkezik egy, a PingAccess kiszolgáló telepítése és a egy Azure AD OIDC szolgáltatói kapcsolat létrehozása az Azure-portálról kimásolt címtár-azonosító. Az alkalmazás Azonosítóját és kulcsát értékeket, majd a webes munkamenet létrehozására a PingAccess használhatja. Ezt követően állítsa be a identitásleképezési, és hozzon létre egy virtuális gazdagép, a hely és az alkalmazás.

### <a name="test-your-app"></a>Az alkalmazás tesztelése

Ha végrehajtotta ezeket a lépéseket, az alkalmazás működik és kell lennie. Tesztelje, nyisson meg egy böngészőt, és keresse meg a külső URL-címre, amikor közzétette az alkalmazást az Azure-ban létrehozott. Jelentkezzen be a teszt-fiókkal, amelyet az alkalmazáshoz rendelt.

## <a name="next-steps"></a>További lépések

- [Az Azure ad-hez készült PingAccess konfigurálása](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Hogyan nyújt az Azure AD-alkalmazásproxy egyszeri bejelentkezést?](application-proxy-single-sign-on.md)
- [Az alkalmazásproxy hibaelhárítása](application-proxy-troubleshoot.md)
