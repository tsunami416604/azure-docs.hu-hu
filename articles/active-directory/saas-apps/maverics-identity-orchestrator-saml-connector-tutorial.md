---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Maverics Identity Orchestrator SAML-összekötővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Maverics Identity Orchestrator SAML-összekötő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518928"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Maverics Identity Orchestrator SAML-összekötővel

## <a name="introduction"></a>Bevezetés

A rétegek egyszerű módszert biztosítanak a helyszíni alkalmazások integrálására az Azure AD-vel a hitelesítéshez és a hozzáférés-vezérléshez.

Ez az útmutató végigvezeti a Maverics Identity Orchestrator konfigurálásának lépésein &trade; :
* A helyszíni identitásrendszer felhasználóinak növekményes áttelepítését az Azure AD-be egy örökölt helyszíni alkalmazásba való bejelentkezés során.
* Átirányíthatja a bejelentkezési kérelmeket egy örökölt webes hozzáférés-kezelési termékből, például a CA SiteMinder vagy az Oracle Access Managerből az Azure AD-be.
* A felhasználókat a HTTP-fejlécek vagy a tulajdonosi munkamenetek cookie-jait használó helyszíni alkalmazásokhoz hitelesítheti, miután a felhasználót az Azure AD-ben hitelesíti.

A rétegek olyan szoftvereket biztosít, amelyek helyszíni vagy Felhőbeli üzembe helyezést, kapcsolódást és koordinálást tesznek lehetővé az identitás-szolgáltatók között a hibrid és a többfelhős vállalatok számára elérhető elosztott Identitáskezelés létrehozásához.

Ez az oktatóanyag bemutatja, hogyan telepíthet át egy örökölt Webhozzáférés-kezelő termékkel (CA SiteMinder) jelenleg védett helyszíni webalkalmazást az Azure AD hitelesítéshez és hozzáférés-vezérléshez való használatához.
1. A Maverics Identity Orchestrator telepítése&trade;
2. Regisztrálja vállalati alkalmazását az Azure AD-ben, és konfigurálja úgy, hogy az Maverics Azure AD SAML zéró kódú összekötőt használja az &trade; SAML-alapú egyszeri bejelentkezéshez.
3. Integrálja a Maverics-t a SiteMinder és az LDAP felhasználói tárolóval.
4. Állítsa be Azure Key Vault és konfigurálja a Maverics, hogy azok a titkokat kezelő szolgáltatóként használhassák.
5. A felhasználók áttelepítésének és a munkamenetek absztrakciójának bemutatása a Maverics használatával a helyszíni Java-webalkalmazásokhoz való hozzáférés biztosításához.

További telepítési és konfigurációs utasításokért tekintse meg a következőt: https://strata.io/docs

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
- Maverics Identity Orchestrator SAML-összekötő egyszeri bejelentkezéses (SSO) engedélyezett előfizetése. A Maverics szoftver beszerzéséhez vegye fel a kapcsolatot sales@strata.io

## <a name="install-maverics-identity-orchestratortrade"></a>A Maverics Identity Orchestrator telepítése&trade;

A Maverics Identity Orchestrator telepítésének megkezdéséhez tekintse meg a telepítési utasításokat a következő címen: https://strata.io/docs

## <a name="system-requirements"></a>Rendszerkövetelmények
### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>Függőségek
* rendszerszintű

## <a name="installation"></a>Telepítés

1. Szerezze be a legújabb Maverics RPM-csomagot. Másolja a csomagot arra a rendszerre, amelyre telepíteni szeretné a Maverics szoftvert.

2. Telepítse a Maverics csomagot, és helyettesítse be a fájlnevét a helyére `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. A Maverics telepítése után a szolgáltatás a következőként fog futni: `systemd` . A szolgáltatás működésének ellenőrzéséhez hajtsa végre a következő parancsot.

    `sudo systemctl status maverics`

Alapértelmezés szerint a Maverics telepítve van a `/usr/local/bin` címtárban.

A Maverics telepítése után az alapértelmezett `maverics.yaml` fájl jön létre a `/etc/maverics` könyvtárban. Mielőtt szerkeszti a konfigurációt a `workflows` `connectors` (z) és a (z) rendszerre, a konfigurációs fájl a következőképpen fog kinézni:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>Konfigurációs beállítások
### <a name="version"></a>Verzió
A `version` mező kijelenti, hogy a konfigurációs fájl melyik verzióját használja. Ha nincs megadva, a rendszer a legújabb konfigurációs verziót fogja használni.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Figyelési címe
`listenAddress` kijelenti, hogy a Orchestrator mely címen fog figyelni. Ha a cím gazdagép szakasza üres, a Orchestrator a helyi rendszer összes elérhető egyedi küldési és kiválasztási IP-címét figyeli. Ha a címnek a port szakasza üres, akkor a rendszer automatikusan kiválasztja a portszámot.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

A `tls` mező deklarálja a szállítási réteg biztonsági objektumainak térképét. A TLS-objektumokat összekötők, valamint a Orchestrator-kiszolgáló is használhatja. Az összes rendelkezésre álló TLS-beállítás esetében tekintse `transport` meg a csomag dokumentációját.

A Microsoft Azure SAML-alapú egyszeri bejelentkezés használata esetén a TLS protokollon keresztüli kommunikációra van szükség, további információt [itt](https://letsencrypt.org/getting-started/) talál a tanúsítványok létrehozásához.

A `maverics` kulcs a Orchestrator-kiszolgáló számára van fenntartva. Minden más kulcs elérhető, és használható egy TLS-objektum egy adott összekötőbe való beadásához.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Fájlok belefoglalása

`connectors` a és `workflows` a saját, különálló konfigurációs fájljaiban is definiálható, és `maverics.yaml` `includeFiles` a következő példa alapján hivatkozhat rájuk.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

Ez az oktatóanyag egyetlen `maverics.yaml` konfigurációs fájlt használ.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>Azure Key Vault használata titkok szolgáltatóként

### <a name="secret-management"></a>Titkos kód kezelése

A Maverics képes a titkok betöltésére különböző titkos felügyeleti megoldásokkal integrálni. A jelenlegi integrációk közé tartozik egy fájl, a Hashicorp-tár és a Azure Key Vault. Ha nincs megadva titkos felügyeleti megoldás, a Maverics alapértelmezés szerint egyszerű szövegként tölti be a titkokat a rendszerből `maverics.yaml` .
Ha egy konfigurációs fájlban titkos kulcsot szeretne deklarálni `maverics.yaml` , zárja be a titkos kódot a szögletes zárójelekkel:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>Fájl

A titkok fájlból való betöltéséhez adja hozzá a környezeti változót `MAVERICS_SECRET_PROVIDER` a fájlhoz a következővel  `/etc/maverics/maverics.env` :

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

Ezután indítsa újra a maverics szolgáltatást: `sudo systemctl restart maverics`

A `secrets.yaml` fájl tartalma tetszőleges számú lehet `secrets` .
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Azure Key Vault

A következő lépések bemutatják, hogyan állíthat be egy Azure Key Vaultt a [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)használatával:

1. [Jelentkezzen](https://portal.azure.com) be a Azure Portal használatával vagy a CLI parancs használatával:
    ```shell
    az login
    ```

2. [Hozzon létre egy új](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)tárat, vagy használja a CLI-parancsot:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Adja hozzá a titkokat Key Vaulthoz](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault), vagy használjon CLI-parancsot:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Alkalmazás regisztrálása Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)vagy CLI-parancs használatával:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [Engedélyezze az alkalmazás számára a titkos kód használatát](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)vagy egy CLI-parancs használatát:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Az Azure kulcstartóból származó titkok betöltéséhez állítsa be a környezeti változót `MAVERICS_SECRET_PROVIDER` a fájlban `/etc/maverics/maverics.env` , és a hitelesítő adatok a fájl azure-credentials.jstalálhatók a következő minta használatával: `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

Ezután indítsa újra a maverics szolgáltatást: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Az alkalmazás konfigurálása az Azure AD-ben az SAML-alapú egyszeri bejelentkezéshez

1. A Azure Active Directory-bérlőben navigáljon a `Enterprise applications` elemre, keresse meg `Maverics Identity Orchestrator SAML Connector` és jelölje ki.

2. A "Maverics Identity Orchestrator SAML-összekötő" | Tulajdonságok lapon `User assignment required?` a nem értékre állítva engedélyezheti, hogy az alkalmazás működjön az újonnan áttelepített felhasználók számára.

3. A "Maverics Identity Orchestrator SAML-összekötő" | Áttekintés oldalon válassza a lehetőséget, `Setup single sign-on` majd válassza a lehetőséget `SAML` .

4. A "Maverics Identity Orchestrator SAML-összekötő" | SAML-alapú bejelentkezés, az alapszintű SAML-konfiguráció szerkesztése.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

5. Állítsa be az `Entity ID` URL-cím beírását a következő minta használatával: `https://<SUBDOMAIN>.maverics.org` . A `Entity ID` -nek egyedinek kell lennie a bérlő alkalmazásai között. Mentse az itt megadott értéket a Maverics-konfigurációba való felvételhez.

6. Adja meg a válasz URL-címét a következő minta használatával: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

7. Állítsa be a bejelentkezési URL-címet a következő minta használatával: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` , majd kattintson a Mentés gombra.

8. Nyissa meg az SAML aláíró tanúsítvány szakaszt, és kattintson a Másolás gombra az alkalmazás-összevonás metaadat-URL-címének másolásához és a számítógépen való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Maverics Identity Orchestrator Azure AD SAML-összekötő konfigurálása

A Maverics Identity Orchestrator az Azure AD Connector a következőket támogatja: 
- OpenID Connect
- SAML-kapcsolat 

1. Az SAML-alapú egyszeri bejelentkezés engedélyezéséhez állítsa be a következőt: `authType: saml` .

1. A következő értékének létrehozása `samlMetadataURL` : `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. Most adja meg azt az URL-címet, amelyet az Azure átirányít az alkalmazásba, miután bejelentkezett az Azure-beli hitelesítő adataival.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. Másolja az értéket a fent konfigurált EntityID: `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Másolja ki a válasz URL-címéből azt az értéket, amelyet az Azure AD az SAML-válasz KÖZZÉTÉTELéhez fog használni.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. JWT-aláíró kulcs létrehozása, amely a Maverics-identitás Orchestrator-munkamenet adatainak a megvédésére szolgál &trade; az [OpenSSL eszközzel](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Másolja a választ a `jwtSigningKey` config tulajdonságra: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>Attribútumok és attribútumok megfeleltetése
Az attribútum-hozzárendelés használatával határozható meg a felhasználói attribútumok leképezése a helyszíni felhasználói címtárból az Azure AD-be a felhasználók kiosztásakor.

Az attribútumok határozzák meg a jogcímek alkalmazásához visszaadott felhasználói és a munkamenet-cookie-k átadását, illetve az alkalmazásnak a HTTP-fejléc változókban való továbbítását.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Maverics-identitás konfigurálása Orchestrator Azure AD SAML-összekötő YAML

Az Azure AD Connector-Orchestrator Maverics-identitásának konfigurációja a következőképpen fog kinézni:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-azure-ad"></a>Felhasználók migrálása az Azure AD-be

Ezt a konfigurációt követve fokozatosan áttelepítheti a felhasználókat egy Web Access Management termékből, például a CA SiteMinder, az Oracle Access Managerből vagy az IBM Tivoliből; egy LDAP-címtár; vagy egy SQL-adatbázis.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Az alkalmazás engedélyeinek konfigurálása az Azure AD-ben felhasználók létrehozásához

1. A Azure Active Directory-bérlőben navigáljon a `App registrations` "Maverics Identity Orchestrator SAML-összekötő" alkalmazáshoz.

2. A "Maverics Identity Orchestrator SAML-összekötő" | Tanúsítványok & titkok, válassza ki, `New client secret` majd válassza a lejárati lehetőséget. Kattintson a Másolás gombra a titok másolásához és a számítógépre mentéséhez.

3. A "Maverics Identity Orchestrator SAML-összekötő" | API-engedélyek, válassza ki, majd `Add permission` a kérelem API engedélyei lehetőségnél válassza ki `Microsoft Graph` , majd `Application permissions` . A következő képernyőn válassza ki a `User.ReadWrite.All` elemet, majd válassza a lehetőséget `Add permissions` . Ezzel a beállítással visszatérhet az API-engedélyekhez `Grant admin consent` .


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>A Maverics Identity Orchestrator SAML-összekötő YAML konfigurálása a felhasználók áttelepítéséhez

A felhasználó áttelepítési munkafolyamatának engedélyezéséhez adja hozzá ezt a további tulajdonságokat a konfigurációs fájlhoz:
1. Az Azure Graph URL-címének beállítása: `graphURL: https://graph.microsoft.com`
1. Állítsa be az OAuth token URL-címét a következő minta alapján: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. Állítsa be a fent generált ügyfél-titkos kulcsot: `oauthClientSecret: <CLIENT SECRET>`


Az Azure AD Connector Orchestrator végső Maverics-identitása a következőképpen fog kinézni:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>A Maverics Zero Code-összekötő konfigurálása a &trade; SiteMinder

Az SiteMinder-összekötő használatával a felhasználók áttelepíthetők az Azure AD-be, és a felhasználók a SiteMinder által védett örökölt helyszíni alkalmazásokban az újonnan létrehozott Azure AD-identitásokkal és hitelesítő adatokkal jelentkezhetnek be.

Ebben az oktatóanyagban a SiteMinder úgy lett konfigurálva, hogy megvédje a régi alkalmazást űrlapalapú hitelesítéssel és a `SMSESSION` cookie használatával. Ha olyan alkalmazással szeretné integrálni a hitelesítést és a munkamenetet, amely HTTP-fejléceket használ, akkor hozzá kell adnia a fejléc emulációs konfigurációját az összekötőhöz.

Ez a példa az `username` attribútumot a `SM_USER` http-fejlécre képezi le:
```yaml
  headers:
    SM_USER: username
```

Állítsa a `proxyPass` -t arra a helyre, amelyhez a kérések a proxyn vannak. Ez általában a védett alkalmazás gazdagépe.

`loginPage` meg kell egyeznie a SiteMinder által jelenleg használt bejelentkezési űrlap URL-címével, amikor átirányítja a felhasználókat a hitelesítéshez.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>A Maverics Zero Code-összekötő konfigurálása &trade; az LDAP-hez

Ha az alkalmazásokat egy olyan WAM-termék védi, mint például a SiteMinder, a felhasználói identitások és attribútumok általában egy LDAP-címtárban tárolódnak.

Ez az összekötő-konfiguráció azt mutatja be, hogyan lehet a SiteMinder felhasználói tárolóként konfigurált LDAP-címtárhoz csatlakozni, így a megfelelő felhasználói profil adatai gyűjthetők az áttelepítési munkafolyamatban, és egy megfelelő felhasználó az Azure AD-ben is létrehozható.

* `baseDN` meghatározza az LDAP-keresés végrehajtásához használt könyvtár helyét.

* `url` az az LDAP-kiszolgáló címe és portja, amelyhez csatlakozni kíván.

* `serviceAccountUsername` az LDAP-kiszolgálóhoz való kapcsolódáshoz használt Felhasználónév, amely általában kötési DN-ként van kifejezve, például: `CN=Directory Manager` .

* `serviceAccountPassword` az LDAP-kiszolgálóhoz való kapcsolódáshoz használt jelszó. Ezt az értéket a korábban konfigurált Azure Key Vault-példány tárolja.  

* `userAttributes` meghatározza a lekérdezéshez kapcsolódó felhasználói attribútumok listáját. Ezek az attribútumok később a megfelelő Azure AD-attribútumokra vannak leképezve.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

## <a name="configure-the-migration-workflow"></a>Az áttelepítési munkafolyamat konfigurálása

Az áttelepítési munkafolyamat konfigurációja meghatározza, hogy a Maverics hogyan fogja áttelepíteni a felhasználókat a SiteMinder/LDAP-ből az Azure AD-be.

Ez a munkafolyamat:
- A SiteMinder-összekötőt használja a SiteMinder-bejelentkezéshez. A felhasználói hitelesítő adatok érvényesítése a SiteMinder-hitelesítésen keresztül történik, majd a munkafolyamat későbbi lépéseire lesz átadva.
- A felhasználói profil attribútumainak beolvasása a SiteMinder felhasználói tárolóból.
- Kérést küld a Microsoft Graph API-nak, hogy létrehozza a felhasználót az Azure AD-bérlőben.

Lépések:
1. Adjon nevet a munkafolyamatnak, például SiteMinder az Azure AD-Migrálás felé.
2. Itt adhatja meg a (z `endpoint` )-t, amely egy olyan http-elérési út, amelyen a munkafolyamat elérhetővé vált, amely a `actions` kérelmekre adott válaszként elindítja az adott munkafolyamat A `endpoint` általában a proxyn lévő alkalmazásnak felel meg, például: `/my_app` . Az értéknek tartalmaznia kell a kezdő és a záró perjelet is.
3. Adja hozzá a megfelelőt `actions` a munkafolyamathoz.
    - Adja meg az `login` SiteMinder-összekötő metódusát. Az összekötő értékének meg kell egyeznie az összekötő konfigurációjában szereplő Name értékkel.
     - Az `getprofile` LDAP-összekötő metódusának megadása.
     - Adja meg az `createuser` AzureAD-összekötőt.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Az áttelepítési munkafolyamat ellenőrzése

1. Ha a Maverics szolgáltatás még nem fut, indítsa el a következő parancs végrehajtásával: `sudo systemctl start maverics`

2. Navigáljon a proxyn keresztüli bejelentkezési URL-címre: `http://host.company.com/my_app` .
3. Adja meg az alkalmazásba való bejelentkezéshez használt felhasználói hitelesítő adatokat, miközben a SiteMinder védi.
4. Navigáljon a Kezdőlap > felhasználókhoz | Minden felhasználó ellenőrzi, hogy a felhasználó az Azure AD-bérlőben lett-e létrehozva.  

## <a name="configure-the-session-abstraction-workflow"></a>A munkamenet-absztrakt munkafolyamat konfigurálása

A munkamenet-absztrakt munkafolyamat a régi helyszíni webalkalmazás hitelesítését és hozzáférés-vezérlését áthelyezi az Azure AD-be.

Az Azure Connector a `login` metódus használatával irányítja át a felhasználót a bejelentkezési URL-címre, feltéve, hogy nem létezik munkamenet.

A hitelesítés után az eredményként létrehozott munkamenet-tokent a rendszer átadja a Maverics-nek, és a SiteMinder `emulate` -összekötő metódusa a cookie-alapú munkamenet és/vagy a fejléc-alapú munkamenet emulálása, majd a kérésnek az alkalmazás által megkövetelt további attribútumokkal való díszítésére szolgál.

1. Adjon nevet a munkafolyamatnak, például SiteMinder-munkamenet absztrakciója.
2. Itt adhatja meg a `endpoint` -t, amely megfelel az alkalmazásnak, amely a proxy. Az értéknek tartalmaznia kell a kezdő és a záró perjelet is, például `/my_app/` :.
3. Adja hozzá a megfelelőt `actions` a munkafolyamathoz.
    - Az `login` Azure-összekötő metódusának megadása. Az `connector` értéknek meg kell egyeznie az `name` összekötő konfigurációjában szereplő értékkel.
    - Adja meg az `emulate` SiteMinder-összekötő metódusát.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>A munkamenet-absztrakt munkafolyamat ellenőrzése

1. Navigáljon a proxy alkalmazás URL-címére: `https://<AZURECOMPANY.COM>/<MY_APP>` . A rendszer átirányítja a felhasználót a proxyn lévő bejelentkezési oldalra.
2. Adja meg az Azure AD felhasználói hitelesítő adatait.
3. A felhasználót úgy kell átirányítani az alkalmazásba, mintha közvetlenül a SiteMinder hitelesíti.
