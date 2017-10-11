---
title: "Fejléc-alapú hitelesítés PingAccess az az Azure AD alkalmazásproxy |} Microsoft Docs"
description: "Alkalmazások közzététele az PingAccess és alkalmazás Proxy fejléc-alapú hitelesítés támogatására."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 58034ab8830cf655199875b448948ea14dc04a70
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Az egyszeri bejelentkezés az alkalmazásproxy és PingAccess fejléc-alapú hitelesítés

Az Azure Active Directory Alkalmazásproxyjával és PingAccess közösen egyszerre több alkalmazást is hozzáférést biztosít az Azure Active Directory ügyfeleknek. PingAccess bővíti a [meglévő alkalmazásproxy ajánlatok](active-directory-application-proxy-get-started.md) egyszeri bejelentkezéses hozzáférést azon alkalmazásoknak, amelyek a hitelesítéshez használandó fejlécek tartalmazza.

## <a name="what-is-pingaccess-for-azure-ad"></a>Mi az az PingAccess az Azure AD?

Az Azure Active Directory PingAccess, amely lehetővé teszi a felhasználók hozzáférést és egyszeri bejelentkezést biztosítanak fejlécek hitelesítés használó alkalmazások PingAccess egy elérhető. Alkalmazás Proxy ezekhez az alkalmazásokhoz, mint bármely más, való hozzáférés hitelesítéséhez az Azure AD, és ezután átadja az összekötő szolgáltatás forgalmát kezeli. PingAccess az alkalmazások előtt helyezkedik el, és az Azure ad-jogkivonat fordítja fejléc, hogy az alkalmazás a hitelesítési kapjon, azt is olvasható formátumban.

A felhasználók nem bármilyen figyelje, amikor bejelentkeznek a használhatja a vállalati alkalmazásokat. Akkor is még mindig bárhonnan dolgozhatnak bármilyen eszközön. 

Mivel az alkalmazásproxy-összekötők összes alkalmazásoknak a hitelesítési típus távoli forgalom közvetlen, azokat továbbra automatikusan, illetve a terhelést.

## <a name="how-do-i-get-access"></a>Hogyan szerezhetek hozzáférést?

Mivel ebben a forgatókönyvben az Azure Active Directory és PingAccess közötti partnerség kínált, licencek kell mindkét szolgáltatás esetében. Azonban az Azure Active Directory Premium előfizetéshez tartozik, amely lefedi legfeljebb 20 alkalmazások alapszintű PingAccess licencet. Ha több mint 20 fejléc-alapú alkalmazások közzétételét van szüksége, a PingAccess vásárolhat további licencre. 

További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).

## <a name="publish-your-application-in-azure"></a>Az alkalmazás közzététele az Azure-ban

Ez a cikk tesznek közzé egy alkalmazást ebben a forgatókönyvben az első alkalommal személyek számára készült. Első lépések alkalmazás és a PingAccess, mellett a közzétételi lépéseket végigvezeti. Ha már beállított mindkét szolgáltatás, de a közzétételi lépéseket frissítő, az összekötő telepítése, és helyezze át a [adja hozzá az alkalmazás az Azure AD-alkalmazásproxyval](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Mivel ez a forgatókönyv az Azure AD közötti partnerség és PingAccess, bizonyos utasítások találhatók a Ping identitás helyen.

### <a name="install-an-application-proxy-connector"></a>Az alkalmazásproxy-összekötő telepítése

Ha már Application Proxy engedélyezve van, és egy összekötő telepítve van, hagyja ki ezt a szakaszt, és helyezze át a [adja hozzá az alkalmazás az Azure AD-alkalmazásproxyval](#add-your-app-to-azure-ad-with-application-proxy).

Az alkalmazásproxy-összekötő egy Windows Server-szolgáltatás, amely arra utasítja a közzétett alkalmazások a távoli alkalmazottak érkező forgalom. Telepítési utasításokat, lásd: [alkalmazásproxy engedélyezése az Azure-portálon](active-directory-application-proxy-enable.md).

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazdaként.
2. Válassza ki **Azure Active Directory** > **alkalmazásproxy**.
3. Válassza ki **összekötő letöltése** a alkalmazásproxy-összekötő letöltés megkezdéséhez. Kövesse a telepítési utasításokat.

   ![Alkalmazásproxy engedélyezése és az összekötő letöltése](./media/application-proxy-ping-access/install-connector.png)

4. Az összekötő letöltése kell automatikusan alkalmazásproxy engedélyezése a címtáron, de ha nem választhat **alkalmazásproxy engedélyezése**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Az alkalmazás hozzáadása az Azure AD-alkalmazásproxyval

Nincsenek két műveletet kell tennie az Azure portálon. Először az alkalmazásproxy az alkalmazás közzétételére. Ezt követően kell összegyűjtenie némi információt az alkalmazást, amelynek során a PingAccess lépéseket használhatja.

Kövesse az alábbi lépéseket az alkalmazás közzétételére. A részletes lépéseket bemutató 1-8, tekintse meg a még [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-publish-azure-portal.md).

1. Ha nem az utolsó szakaszban, jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazdaként.
2. Válassza ki **Azure Active Directory** > **vállalati alkalmazások**.
3. Válassza ki **Hozzáadás** a panel tetején.
4. Válassza ki **helyszíni alkalmazás**.
5. Töltse ki a kötelező mezőket az új alkalmazás adatait tartalmazó. A következő útmutatást használhatja a beállításokat:
   - **Belső URL-cím**: általában viszi az alkalmazás bejelentkezési oldalára, ha a vállalati hálózaton már megnyitott URL-címet adjon meg. Ebben a forgatókönyvben az összekötő kell a PingAccess proxy tekinti az alkalmazás első oldalán. Ezt a formátumot használja: `https://<host name of your PA server>:<port>`. A port 3000 alapértelmezés szerint, de a PingAccess konfigurálhatja.
   - **Előhitelesítési módszer**: Azure Active Directoryban
   - **URL-címet a fejlécekben fordítása**: nincs

   >[!NOTE]
   >Ha ez az első alkalmazás, port 3000 elindításához használja, majd visszatérhet, és frissítse ezt a beállítást, ha az PingAccess konfigurációjának módosítását. Ha ez a második vagy újabb alkalmazáshoz, ez kell felel meg a figyelő a PingAccess konfigurálását. További információ [PingAccess a figyelők](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Válassza ki **Hozzáadás** a panel alján. Az alkalmazás kerül, és a quick start menü megnyitása.
7. A quick start menüben válassza ki a **rendelje hozzá a felhasználót tesztelési**, és legalább egy felhasználói hozzáadni az alkalmazáshoz. Győződjön meg arról, hogy a teszt fiókjának van hozzáférési joga a helyszíni alkalmazások.
8. Válassza ki **hozzárendelése** menteni a teszt felhasználó hozzárendelése.
9. Válassza ki az alkalmazás-felügyelet panel **egyszeri bejelentkezés**.
10. Válasszon **fejléc-alapú bejelentkezés** a legördülő menüből. Kattintson a **Mentés** gombra.

   >[!TIP]
   >Ha ez az első alkalommal használja a fejléc-alapú egyszeri bejelentkezést, telepítendő PingAccess. Legyen az Azure-előfizetés automatikusan hozzárendeli a PingAccess telepített, ezen az oldalon egyszeri bejelentkezést a hivatkozás segítségével töltse le a PingAccess. Nyissa meg a letöltési hely most, vagy visszatérhet, és ezen az oldalon később. 

   ![Válassza ki a fejléc-alapú bejelentkezés](./media/application-proxy-ping-access/sso-header.PNG)

11. Zárja be a vállalati alkalmazások panel vagy görgessen egészen a bal oldali térjen vissza az Azure Active Directory menübe.
12. Válassza ki **App regisztrációk**.

   ![Válassza ki az alkalmazás-regisztráció](./media/application-proxy-ping-access/app-registrations.png)

13. Válassza ki az alkalmazást, az előzőekben adott hozzá, majd **válasz URL-címek**.

   ![Válassza ki a válasz URL-címek](./media/application-proxy-ping-access/reply-urls.png)

14. Ellenőrizze, hogy a külső URL-címet, az alkalmazás 5. lépésben hozzárendelt van-e a válasz URL-címek listáját. Ha nem, vegye fel azt.
15. Válassza a beállítások panelről, **szükséges engedélyek**.

  ![Válassza ki a szükséges engedélyek](./media/application-proxy-ping-access/required-permissions.png)

16. Válassza a **Hozzáadás** lehetőséget. Az API kiválasztása **Windows Azure Active Directory**, majd **válasszon**. Az engedélyek, válassza ki **olvasási és írása az összes alkalmazás** és **jelentkezzen be és felhasználói profil olvasása**, majd **válassza** és **kész**.  

  ![Engedélyek kiválasztása](./media/application-proxy-ping-access/select-permissions.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Információgyűjtés a PingAccess lépései

1. Válassza a beállítások panelen **tulajdonságok**. 

  ![Válassza a Tulajdonságok](./media/application-proxy-ping-access/properties.png)

2. Mentse a **alkalmazásazonosító** érték. Az ügyfél-azonosító szolgál PingAccess konfigurálásakor.
3. Válassza a beállítások panelről, **kulcsok**.

  ![Válassza ki a kulcsok](./media/application-proxy-ping-access/Keys.png)

4. Hozzon létre egy kulcsot a fő leírás, majd válassza a lejárati dátumot a legördülő menüből.
5. Kattintson a **Mentés** gombra. Megjelenik egy GUID Azonosítót a **érték** mező.

  Most, mentse ezt az értéket, mert nem lehet majd megjeleníteni az ablak bezárását követően.

  ![Hozzon létre egy új kulcsot](./media/application-proxy-ping-access/create-keys.png)

6. Zárja be a regisztrációk panelen vagy görgessen egészen a bal oldali térjen vissza az Azure Active Directory menübe.
7. Válassza ki **tulajdonságok**.
8. Mentse a **könyvtár-azonosítója** GUID.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Nem kötelező – a frissítés GraphAPI küldése egyéni mezők

A biztonsági jogkivonatok, amelyek az Azure AD küld a hitelesítéshez listájáért lásd: [az Azure AD-jogkivonatok referenciájából](./develop/active-directory-token-and-claims.md). Ha egy egyéni jogcím, amelyet más tokenekbe küld van szüksége, GraphAPI beállításához használja az alkalmazás mező *acceptMappedClaims* való **igaz**. Azure AD Graph Explorer vagy a MS Graph használatával ellenőrizze ezt a konfigurációt. 

Ez a példa Graph Explorer:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```

## <a name="download-pingaccess-and-configure-your-app"></a>Töltse le a PingAccess, és állítsa be alkalmazását

Most, hogy befejezte az Azure Active Directory beállítási lépéseket, továbbléphet PingAccess konfigurálására. 

A részletes, lépésenkénti leírását ebben a forgatókönyvben PingAccess része továbbra is a Ping identitáskezelési dokumentáció [PingAccess konfigurálása az Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Ezen lépések végigvezetik a folyamat első egy PingAccess fiókot, ha még nem rendelkezik egy, a PingAccess kiszolgáló telepítése és az Azure AD OIDC szolgáltatói kapcsolat létrehozása az Azure portálról másolt könyvtár-azonosítóval. Az alkalmazás Azonosítóját és kulcsát értékek, majd a webes munkamenet a PingAccess létrehozásához használhat. Ezt követően Identitásleképezés beállítása, és hozzon létre egy virtuális gazdagép, a hely és az alkalmazás.

### <a name="test-your-app"></a>Az alkalmazás tesztelése

Ha végrehajtotta ezeket a lépéseket, az alkalmazás működik, és kell lennie. Ha kipróbálja, nyisson meg egy böngészőt, és keresse meg a külső URL-címet, amelyet közzé az alkalmazást az Azure-ban hozott létre. Jelentkezzen be a fiókot, amelyet az alkalmazáshoz rendelt.

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD PingAccess konfigurálása](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Hogyan nyújt az Azure AD-alkalmazásproxy egyszeri bejelentkezéshez?](application-proxy-sso-overview.md)
- [Alkalmazásproxy hibaelhárítása](active-directory-application-proxy-troubleshoot.md)
