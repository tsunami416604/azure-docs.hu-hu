---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a Maverics Identity Orchestrator SAML-összekötővel | Microsoft Docs'
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
ms.openlocfilehash: fbab2bbaa47090ff4bd7fb99495912bd1f645b61
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758139"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Oktatóanyag: az Azure AD egyszeri bejelentkezés integrálása a Maverics Identity Orchestrator SAML-összekötővel

A rétegek egyszerű módszert biztosítanak a helyszíni alkalmazások és a Azure Active Directory (Azure AD) integrálására a hitelesítéshez és a hozzáférés-vezérléshez.

Ez a cikk bemutatja, hogyan konfigurálhatja a Maverics Identity Orchestrator a következőre:
* A helyszíni identitásrendszer felhasználóinak növekményes áttelepítését az Azure AD-be egy örökölt helyszíni alkalmazásba való bejelentkezés során.
* A bejelentkezési kérelmek átirányítása egy örökölt webes elérésű felügyeleti termékből, például a CA SiteMinder vagy az Oracle Access Managerből az Azure AD-be.
* A felhasználókat hitelesítheti a HTTP-fejlécek vagy a tulajdonosi munkamenetek cookie-jai által védett helyszíni alkalmazásokhoz, miután a felhasználót az Azure AD-ben hitelesíti.

A rétegek olyan szoftvereket biztosít, amelyek a helyszínen vagy a felhőben is üzembe helyezhetők. Segít felderíteni, csatlakozni és összehangolni az identitás-szolgáltatók között a hibrid és a többfelhős vállalatok számára elérhető elosztott Identitáskezelés létrehozásához.

Ez az oktatóanyag bemutatja, hogyan telepíthet át egy olyan helyszíni webalkalmazást, amelyet jelenleg egy örökölt webes hozzáférés-kezelési termék (CA SiteMinder) véd az Azure AD hitelesítéshez és hozzáférés-vezérléshez való használatához. Az alapszintű lépések a következők:
1. Telepítse a Maverics Identity Orchestrator.
2. Regisztrálja vállalati alkalmazását az Azure AD-ben, és konfigurálja úgy, hogy az Maverics Azure AD SAML zéró kódú összekötőt használja az SAML-alapú egyszeri bejelentkezéshez (SSO).
3. Integrálja a Maverics-t a SiteMinder és a Lightweight Directory Access Protocol (LDAP) felhasználói tárolóval.
4. Állítson be egy Azure Key vaultot, és konfigurálja a Maverics, hogy azok a titkokat kezelő szolgáltatóként használhassák.
5. A felhasználók áttelepítésének és a munkamenetek absztrakciójának bemutatása a Maverics használatával a helyszíni Java-webalkalmazásokhoz való hozzáférés biztosításához.

További telepítési és konfigurációs utasításokért lépjen a [rétegek webhelyére](https://www.strata.io).

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
- Egy Maverics Identity Orchestrator SAML-összekötő SSO-kompatibilis előfizetés. A Maverics szoftver beszerzéséhez vegye fel a kapcsolatot a [rétegek értékesítésével](mailto:sales@strata.io).

## <a name="install-maverics-identity-orchestrator"></a>A Maverics Identity Orchestrator telepítése

A Maverics Identity Orchestrator telepítésének megkezdéséhez tekintse meg a [telepítési utasításokat](https://www.strata.io).

### <a name="system-requirements"></a>Rendszerkövetelmények
* Támogatott operációs rendszerek
  * RHEL 7+
  * CentOS 7+

* Függőségek
  * rendszerszintű

### <a name="installation"></a>Telepítés

1. Szerezze be a legújabb Maverics RedHat Package Manager (RPM) csomagot. Másolja a csomagot arra a rendszerre, amelyre telepíteni kívánja a Maverics szoftvert.

2. Telepítse a Maverics csomagot, és cserélje le a fájlnevét a helyére `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. A Maverics telepítése után a szolgáltatás a következőként fog futni: `systemd` . A szolgáltatás futásának ellenőrzéséhez hajtsa végre a következő parancsot:

    `sudo systemctl status maverics`

Alapértelmezés szerint a Maverics a */usr/local/bin helyekre* könyvtárba van telepítve.

A Maverics telepítése után a rendszer az alapértelmezett *Maverics. YAML* fájlt hozza létre a */etc/maverics* könyvtárban. Mielőtt szerkeszti a konfigurációt a `workflows` `connectors` (z) és a (z) rendszerre, a konfigurációs fájl a következőképpen fog kinézni:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Beállítási lehetőségek
### <a name="version"></a>Verzió
A `version` mező kijelenti, hogy a konfigurációs fájl melyik verzióját használja a rendszer. Ha a verzió nincs megadva, a rendszer a legújabb konfigurációs verziót fogja használni.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` kijelenti, hogy mely Orchestrator fogja figyelni. Ha a cím gazdagép szakasza üres, a Orchestrator a helyi rendszer összes elérhető egyedi küldési és kiválasztási IP-címét fogja figyelni. Ha a címnek a port szakasza üres, akkor a rendszer automatikusan kiválasztja a portszámot.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

A `tls` mező deklarálja Transport Layer Security (TLS) objektumok leképezését. A TLS-objektumokat összekötők és a Orchestrator-kiszolgáló is használhatja. Az összes rendelkezésre álló TLS-beállításnál tekintse meg a `transport` csomag dokumentációját.

Az SAML-alapú egyszeri bejelentkezés használata esetén a Microsoft Azure a TLS protokollon keresztüli kommunikációt igényli. A tanúsítványok létrehozásával kapcsolatos információkért lépjen a [titkosítás webhelyre](https://letsencrypt.org/getting-started/).

A `maverics` kulcs a Orchestrator-kiszolgáló számára van fenntartva. Minden más kulcs elérhető, és használható egy TLS-objektum egy adott összekötőbe való beadásához.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Beágyazott fájlok

`connectors` `workflows` A következő példa alapján megadhatja a saját, különálló konfigurációs fájljait, és hivatkozhat rájuk a *maverics. YAML* fájlban `includeFiles` :

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

Ez az oktatóanyag egyetlen *maverics. YAML* konfigurációs fájlt használ.

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Azure Key Vault használata titkok szolgáltatóként

### <a name="manage-secrets"></a>Titkos kulcsok kezelése

A titkok betöltéséhez a Maverics különböző titkos felügyeleti megoldásokkal integrálható. A jelenlegi integrációk közé tartozik egy fájl, a Hashicorp-tároló és a Azure Key Vault. Ha nincs megadva titkos felügyeleti megoldás, a Maverics alapértelmezés szerint a *Maverics. YAML* fájlból kifelé irányuló egyszerű szövegbe tölti be a titkokat.

Ha egy *maverics. YAML* konfigurációs fájlban titkos kulcsot szeretne deklarálni, zárja be a titkos kódot szögletes zárójelek közé:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Titkok betöltése fájlból

1. A titkos kulcsok fájlból való betöltéséhez adja hozzá a környezeti változót `MAVERICS_SECRET_PROVIDER` a */etc/maverics/maverics.env* fájlban a következő használatával:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Indítsa újra a Maverics szolgáltatást a futtatásával:

   `sudo systemctl restart maverics`

A *Secrets. YAML* fájl tartalma tetszőleges számú lehet `secrets` .

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Azure Key Vault beállítása

Az Azure Key Vault a Azure Portal vagy az Azure CLI használatával állítható be.

**Az Azure Portal használata**
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. [Hozzon létre egy új kulcstartót](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault).
1. [Adja hozzá a titkos kulcsokat a kulcstartóhoz](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault).
1. [Alkalmazás regisztrálása az Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)-ben.
1. [Engedélyezze az alkalmazás számára a titkos kód használatát](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault).

**Az Azure CLI használata**

1. Nyissa meg az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-t, majd írja be a következő parancsot:

    ```shell
    az login
    ```

1. Hozzon létre egy új kulcstartót a következő parancs futtatásával:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Adja hozzá a titkokat a Key vaulthoz a következő parancs futtatásával:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Az alábbi parancs futtatásával regisztrálhat egy alkalmazást az Azure AD-ben:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. A következő parancs futtatásával engedélyezheti, hogy az alkalmazás titkos kulcsot használjon:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Az Azure Key vaultban lévő titkos kulcsok betöltéséhez állítsa a környezeti változót a `MAVERICS_SECRET_PROVIDER` */etc/maverics/maverics.env* fájlban a *azure-credentials.js* fájlon található hitelesítő adatok használatával, a következő formátumban:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Indítsa újra a Maverics szolgáltatást: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Az alkalmazás konfigurálása az Azure AD-ben az SAML-alapú egyszeri bejelentkezéshez

1. Az Azure AD-bérlőben lépjen a **vállalati alkalmazások**elemre, keresse meg a **MAVERICS Identity Orchestrator SAML-összekötőt**, majd jelölje ki.

1. A Maverics Identity Orchestrator SAML-összekötő **tulajdonságai** ablaktáblán adja meg a **felhasználó-hozzárendelés szükséges lehetőséget?** a **nem** gombra kattintva engedélyezheti, hogy az alkalmazás működjön az újonnan áttelepített felhasználók számára.

1. Az Maverics Identity Orchestrator SAML-összekötő **Áttekintés** paneljén válassza az **egyszeri bejelentkezés beállítása**lehetőséget, majd válassza az **SAML**lehetőséget.

1. A Maverics Identity Orchestrator SAML-összekötő **SAML-alapú bejelentkezési** paneljén szerkessze az **alapszintű SAML-konfigurációt** a **Szerkesztés** (ceruza ikon) gomb kiválasztásával.

   ![Képernyőkép az "alapszintű SAML-konfiguráció" szerkesztés gombról.](common/edit-urls.png)

1. Adja meg az **entitás azonosítóját** úgy, hogy beírja az URL-címet a következő formátumban: `https://<SUBDOMAIN>.maverics.org` . Az entitás AZONOSÍTÓjának egyedinek kell lennie a bérlő alkalmazásai között. Mentse az itt megadott értéket a Maverics-konfigurációba való felvételhez.

1. Adja meg a **Válasz URL-címét** a következő formátumban: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

1. Adja meg a **bejelentkezési URL-címet** a következő formátumban: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` . 

1. Kattintson a **Mentés** gombra.

1. Az **SAML aláíró tanúsítvány** szakaszban válassza a **Másolás** gombot az **alkalmazás-összevonás metaadat-URL-címének**másolásához, majd mentse azt a számítógépre.

    ![Képernyőkép az "SAML aláíró tanúsítvány" másolási gombról.](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Az Azure AD SAML-összekötő Maverics-identitásának konfigurálása Orchestrator

A Maverics Identity Orchestrator Azure AD-összekötő támogatja az OpenID Connect és az SAML Connect használatát. Az összekötő konfigurálásához tegye a következőket: 

1. Az SAML-alapú egyszeri bejelentkezés engedélyezéséhez állítsa be a következőt: `authType: saml` .

1. Hozza létre az értéket a `samlMetadataURL` következő formátumban: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .

1. Adja meg azt az URL-címet, amelyet az Azure átirányít majd az alkalmazásba, miután a felhasználók bejelentkezett az Azure-beli hitelesítő adataikkal. Használja a következő formátumot: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>` .

1. Másolja az értéket a korábban konfigurált EntityID: `samlEntityID: https://<SUBDOMAIN>.maverics.org` .

1. Másolja ki a válasz URL-címéből azt az értéket, amelyet az Azure AD az SAML-válasz közzétételéhez fog használni: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>` .

1. Létrehoz egy JSON Web Token (JWT) aláíró kulcsot, amely az [OpenSSL eszköz](https://www.openssl.org/source/)használatával védi a Maverics Identity Orchestrator-munkamenet adatait:

    ```shell 
    openssl rand 64 | base64
    ```
1. Másolja a választ a `jwtSigningKey` config tulajdonságra: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==` .

## <a name="attributes-and-attribute-mapping"></a>Attribútumok és attribútumok megfeleltetése
Az attribútum-hozzárendelés használatával határozható meg a felhasználói attribútumok leképezése a helyszíni felhasználói címtárból egy Azure AD-bérlőbe a felhasználó beállítása után.

Az attribútumok határozzák meg, hogy mely felhasználói értékeket lehet visszaadni egy jogcímben lévő alkalmazásnak, a munkamenet-cookie-k átadásának vagy a HTTP-fejléc változóinak az alkalmazásnak.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>A Maverics Identity Orchestrator Azure AD SAML-összekötő YAML-fájljának konfigurálása

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

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Felhasználók migrálása egy Azure AD-bérlőbe

Ezt a konfigurációt követve fokozatosan telepítheti át a felhasználókat egy Web Access Management termékből, például a CA SiteMinder, az Oracle Access Managerből vagy az IBM Tivoliből. Ezeket áttelepítheti egy Lightweight Directory Access Protocol-(LDAP-) címtárból vagy egy SQL-adatbázisból is.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Az alkalmazás engedélyeinek konfigurálása az Azure AD-ben felhasználók létrehozásához

1. Az Azure AD-bérlőben lépjen a be, `App registrations` és válassza ki a **Maverics Identity Orchestrator SAML-összekötő** alkalmazást.

1. A **Maverics Identity Orchestrator SAML-összekötőn | Tanúsítványok & titkok** ablaktáblán válassza ki, `New client secret` majd válassza a lejárat lehetőséget. A **Másolás** gombra kattintva másolja be a titkos kulcsot, és mentse a számítógépre.

1. A **Maverics Identity Orchestrator SAML-összekötőn | API-engedélyek** panel, válassza az **engedély hozzáadása** lehetőséget, majd **a kérelem API-engedélyek** ablaktáblán válassza a **Microsoft Graph** és az **alkalmazás engedélyei**lehetőséget. 

1. A következő képernyőn válassza a **User. ReadWrite. All**lehetőséget, majd kattintson az **engedélyek hozzáadása**lehetőségre. 

1. Vissza az **API-engedélyek** ablaktáblán válassza a **rendszergazdai jóváhagyás megadása**lehetőséget.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>A Maverics Identity Orchestrator SAML-összekötő YAML-fájljának konfigurálása a felhasználók áttelepítéséhez

A felhasználó áttelepítési munkafolyamatának engedélyezéséhez adja hozzá ezeket a további tulajdonságokat a konfigurációs fájlhoz:
1. Adja meg az **Azure Graph URL-címét** a következő formátumban: `graphURL: https://graph.microsoft.com` .
1. Adja meg az **OAuth token URL-címét** a következő formátumban: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .
1. Adja meg a korábban generált ügyfél titkos kulcsát a következő formátumban: `oauthClientSecret: <CLIENT SECRET>` .


Az Azure AD Connector konfigurációs fájljának végső Maverics-Orchestrator a következőképpen fog kinézni:

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

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>A Maverics Zero Code-összekötő konfigurálása a SiteMinder

A SiteMinder-összekötő használatával telepítheti át a felhasználókat egy Azure AD-bérlőbe. Az újonnan létrehozott Azure AD-identitások és hitelesítő adatok használatával naplózza a felhasználókat a SiteMinder által védett örökölt helyszíni alkalmazásokban.

Ebben az oktatóanyagban az SiteMinder úgy lett konfigurálva, hogy az űrlapalapú hitelesítés és a cookie használatával megvédje az örökölt alkalmazást `SMSESSION` . Ahhoz, hogy integrálni lehessen egy olyan alkalmazással, amely HTTP-fejléceken keresztül használja a hitelesítési és a munkamenet-információkat, hozzá kell adnia a fejléc emulációs konfigurációját az összekötőhöz.

Ez a példa az `username` attribútumot a `SM_USER` http-fejlécre képezi le:

```yaml
  headers:
    SM_USER: username
```

Állítsa `proxyPass` arra a helyre, amelyhez a kérések a proxyn vannak. Ez a hely általában a védett alkalmazás gazdagépe.

`loginPage` meg kell egyeznie a SiteMinder által jelenleg használt bejelentkezési űrlap URL-címével, amikor átirányítja a felhasználókat a hitelesítéshez.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Maverics nulla kódú összekötő konfigurálása LDAP-hez

Ha az alkalmazásokat egy Web Access Management (WAM) termék védi, például a SiteMinder, a felhasználói identitások és attribútumok általában egy LDAP-címtárban tárolódnak.

Ez az összekötő-konfiguráció azt mutatja be, hogyan lehet csatlakozni az LDAP-címtárhoz. Az összekötő a SiteMinder felhasználói tárolóként van konfigurálva, így a megfelelő felhasználói profil adatai gyűjthetők az áttelepítési munkafolyamatban, és egy megfelelő felhasználó létrehozható az Azure AD-ben.

* `baseDN` meghatározza az LDAP-keresés végrehajtásához használt könyvtár helyét.

* `url` az az LDAP-kiszolgáló címe és portja, amelyhez csatlakozni kíván.

* `serviceAccountUsername` az LDAP-kiszolgálóhoz való kapcsolódáshoz használt Felhasználónév, amely általában kötési DN-ként (például) van kifejezve `CN=Directory Manager` .

* `serviceAccountPassword` az LDAP-kiszolgálóhoz való kapcsolódáshoz használt jelszó. Ezt az értéket a korábban konfigurált Azure Key Vault-példány tárolja.  

* `userAttributes` a lekérdezéshez használt felhasználóhoz kapcsolódó attribútumok listáját határozza meg. Ezek az attribútumok később a megfelelő Azure AD-attribútumokra vannak leképezve.

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

### <a name="configure-the-migration-workflow"></a>Az áttelepítési munkafolyamat konfigurálása

Az áttelepítési munkafolyamat konfigurációja meghatározza, hogy a Maverics hogyan telepíti át a felhasználókat a SiteMinder vagy az LDAP-ből az Azure AD-be.

Ez a munkafolyamat:
- A SiteMinder-összekötőt használja a SiteMinder-bejelentkezéshez. A felhasználói hitelesítő adatok érvényesítése a SiteMinder-hitelesítésen keresztül történik, majd a munkafolyamat későbbi lépéseire lesz átadva.
- A felhasználói profil attribútumainak beolvasása a SiteMinder felhasználói tárolóból.
- Kérést küld a Microsoft Graph API-nak, hogy létrehozza a felhasználót az Azure AD-bérlőben.

Az áttelepítési munkafolyamat konfigurálásához tegye a következőket:

1. Adjon nevet a munkafolyamatnak (például **SiteMinder az Azure ad áttelepítéséhez**).
1. A (z `endpoint` ), amely a munkafolyamat által elérhetővé tett http-elérési út, amely a `actions` munkafolyamatot a kérésekre válaszul aktiválja. A `endpoint` általában a proxyn futó alkalmazásnak felel meg (például: `/my_app` ). Az értéknek tartalmaznia kell a kezdő és a záró perjelet is.
1. Adja hozzá a megfelelőt `actions` a munkafolyamathoz.

   a. Adja meg az `login` SiteMinder-összekötő metódusát. Az összekötő értékének meg kell egyeznie az összekötő konfigurációjában szereplő Name értékkel.

   b. Az `getprofile` LDAP-összekötő metódusának megadása.

   c.  Adja meg az `createuser` AzureAD-összekötő metódusát.

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

1. Ha a Maverics szolgáltatás még nem fut, indítsa el a következő parancs végrehajtásával: 

   `sudo systemctl start maverics`

1. Nyissa meg a proxyn keresztüli bejelentkezési URL-címet `http://host.company.com/my_app` .
1. Adja meg az alkalmazásba való bejelentkezéshez használt felhasználói hitelesítő adatokat, miközben a SiteMinder védi.
4. **Kezdőlap**  >  **felhasználók keresése | Minden felhasználó** ellenőrzi, hogy a felhasználó az Azure ad-bérlőben lett-e létrehozva.  

### <a name="configure-the-session-abstraction-workflow"></a>A munkamenet-absztrakt munkafolyamat konfigurálása

A munkamenet-absztrakt munkafolyamat a régi helyszíni webalkalmazás hitelesítését és hozzáférés-vezérlését áthelyezi az Azure AD-bérlőbe.

Az Azure Connector a `login` metódus használatával irányítja át a felhasználót a bejelentkezési URL-címre, feltéve, hogy nem létezik munkamenet.

A hitelesítés után a rendszer az eredményként létrehozott munkamenet-tokent továbbítja a Maverics. Az SiteMinder-összekötő `emulate` metódusa a cookie-alapú munkamenet vagy a fejléc-alapú munkamenet emulálása, majd a kérésnek az alkalmazás által megkövetelt további attribútumokkal való díszítve.

1. Adjon nevet a munkafolyamatnak (például **SiteMinder-munkamenet absztrakció**).
1. Itt adhatja meg a `endpoint` -t, amely megfelel a proxyn lévő alkalmazásnak. Az értéknek tartalmaznia kell a kezdő és a záró perjelet is (például: `/my_app/` ).
1. Adja hozzá a megfelelőt `actions` a munkafolyamathoz.

   a. Az `login` Azure-összekötő metódusának megadása. Az `connector` értéknek meg kell egyeznie az `name` összekötő konfigurációjában szereplő értékkel.

   b. Adja meg az `emulate` SiteMinder-összekötő metódusát.

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

1. Nyissa meg a proxyn keresztüli alkalmazás URL-címét `https://<AZURECOMPANY.COM>/<MY_APP>` . 
    
    A rendszer átirányítja a proxyn lévő bejelentkezési oldalra.

1. Adja meg az Azure AD felhasználói hitelesítő adatait.

   Az alkalmazást úgy kell átirányítani, mintha közvetlenül a SiteMinder hitelesítette volna.
