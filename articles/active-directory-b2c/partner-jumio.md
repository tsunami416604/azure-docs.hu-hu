---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a Jumio használatával
titleSuffix: Azure AD B2C
description: Oktatóanyag a Azure Active Directory B2C konfigurálásához az Jumio használatával automatizált AZONOSÍTÓk ellenőrzéséhez, az ügyféladatok védelme érdekében
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817547"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Oktatóanyag a Jumio konfigurálásához a Azure Active Directory B2C

Ebben a példában a Azure AD B2C és a [Jumio](https://www.jumio.com/)integrálását ismertető útmutató nyújt útmutatást. A Jumio egy azonosító-ellenőrzési szolgáltatás, amely lehetővé teszi a valós idejű automatizált AZONOSÍTÓk ellenőrzését, így biztosítva az ügyféladatok védelmét.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Azure ad B2C bérlő](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). A bérlő az Azure-előfizetéshez van csatolva.

## <a name="scenario-description"></a>Forgatókönyv leírása

A Jumio-integráció a következő összetevőket tartalmazza:

- Azure AD B2C – az engedélyezési kiszolgáló, amely a felhasználó hitelesítő adatainak (más néven identitás-szolgáltató) ellenőrzéséhez felelős.

- Jumio – az a szolgáltatás, amely a felhasználó által megadott azonosító adatokat veszi alapul, és ellenőrzi azt.

- Köztes REST API – ez az API implementálja Azure AD B2C és a Jumio szolgáltatás közötti integrációt.

- BLOB Storage – az egyéni felhasználói felületi fájlok megadására szolgál a Azure AD B2C szabályzatok számára.

A következő architektúra-diagram a megvalósítást mutatja be.

![Képernyőkép a jumio-Architecture-diagramról](./media/partner-jumio/jumio-architecture-diagram.png)

|Lépés | Leírás |
|:-----| :-----------|
| 1. | A felhasználó egy bejelentkezési oldalon érkezik. A felhasználók a regisztráció gombra kattintva létrehozhatnak egy új fiókot, és beírhatják az adatokat az oldalra. A Azure AD B2C a felhasználói attribútumokat gyűjti.
| 2. | Azure AD B2C meghívja a középső rétegbeli API-t, és átadja a felhasználói attribútumokat.
| 3. | A középső rétegbeli API gyűjti a felhasználói attribútumokat, és átalakítja a Jumio API-t használó formátumba. Ezután küldje el a Jumio.
| 4. | Miután a Jumio felhasználta az adatokat, és feldolgozza azt, a a középső rétegbeli API-ra tér vissza.
| 5. | A középső rétegbeli API feldolgozza az adatokat, és visszaküldi a kapcsolódó információkat a Azure AD B2Cba.
| 6. | Azure AD B2C adatokat fogad a középső rétegbeli API-ból. Ha hibát jelez, hibaüzenet jelenik meg a felhasználó számára. Ha sikeres választ mutat, a rendszer hitelesíti a felhasználót, és beírja a könyvtárba.

## <a name="onboard-with-jumio"></a>Jumio

1. Jumio-fiók létrehozásához forduljon a [Jumio](https://www.jumio.com/contact/)

2. A fiók létrehozása után a rendszer adatokat használ az API-konfigurációban. A következő szakaszok ismertetik a folyamatot.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Azure AD B2C konfigurálása a Jumio

### <a name="part-1---deploy-the-api"></a>1. rész – az API üzembe helyezése

A megadott [API-kód](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) üzembe helyezése egy Azure-szolgáltatásban. A kód a Visual studióból is közzétehető, ezeket az [utasításokat](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)követve.

>[!NOTE]
>Szüksége lesz az üzembe helyezett szolgáltatás URL-címére az Azure AD konfigurálásához a szükséges beállításokkal.

### <a name="part-2---deploy-the-client-certificate"></a>2. rész – az ügyféltanúsítvány üzembe helyezése

1. A Jumio API-hívást egy ügyféltanúsítvány védi. Hozzon létre egy önaláírt tanúsítványt az alább említett PowerShell-mintakód használatával:

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. A rendszer ezután exportálja a tanúsítványt a ({}) számára megadott helyre ``your-local-path`` .

3. A [dokumentumban](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate)említett utasítások alapján importálja a tanúsítványt az Azure app Service-be.

### <a name="part-3---create-a-signingencryption-key"></a>3. rész – aláírási/titkosítási kulcs létrehozása

Hozzon létre egy véletlenszerű karakterláncot, amelynek hossza nagyobb, mint 64 karakter, amely csak betűket és számokat tartalmaz.

Például: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Az alábbi PowerShell-parancsfájl használatával hozzon létre egy 64 karakter hosszúságú alfanumerikus értéket.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>4. rész – az API konfigurálása

[Az Alkalmazásbeállítások konfigurálhatók az Azure app Service-ben](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). Ezzel a módszerrel a beállítások biztonságosan konfigurálhatók egy adattárba való ellenőrzés nélkül. A REST API-hoz a következő beállításokat kell megadnia:

| Alkalmazásbeállítások | Forrás | Jegyzetek |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio-fiók konfigurálása |     |
|JumioSettings:AuthPassword | Jumio-fiók konfigurálása |     |
|AppSettings: SigningCertThumbprint|A létrehozott önaláírt tanúsítvány ujjlenyomata|  |
|AppSettings: IdTokenSigningKey| A PowerShell használatával létrehozott aláíró kulcs | |
| AppSettings: IdTokenEncryptionKey |A PowerShell használatával létrehozott titkosítási kulcs
| AppSettings: IdTokenIssuer | Az JWT-tokenhez használandó kiállító (a GUID értéke előnyben részesített) |
| AppSettings: IdTokenAudience  | Az JWT-tokenhez használandó célközönség (a GUID értéke előnyben részesített) |
|AppSettings: BaseRedirectUrl | A B2C-szabályzat alap URL-címe | https://{saját-bérlő neve}. b2clogin. com/{az alkalmazás-azonosító}|
| WEBSITE_LOAD_CERTIFICATES| A létrehozott önaláírt tanúsítvány ujjlenyomata |

### <a name="part-5---deploy-the-ui"></a>5. rész – a felhasználói felület üzembe helyezése

1. Hozzon létre egy [blob Storage-tárolót a Storage-fiókban](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Tárolja a felhasználói felületi fájlokat a [felhasználói felület mappájából](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) a blob-tárolóba.

#### <a name="update-ui-files"></a>FELHASZNÁLÓIFELÜLET-fájlok frissítése

1. A felhasználói felület fájljaiban nyissa meg a mappát [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. Nyissa meg az egyes HTML-fájlokat.

3. Keresse meg és cserélje le {a-UI-blob-Container-URL}-t a blob Container URL-címére.

4. Keresse meg és cserélje le a {Your-Intermediate-API-URL} címet a köztes API app Service URL-címére.

>[!NOTE]
> Ajánlott eljárásként Azt javasoljuk, hogy az ügyfelek az attribútumok gyűjteménye lapon vegyenek fel beleegyező értesítéseket. Értesítse a felhasználókat arról, hogy az adatok a harmadik féltől származó szolgáltatásoknak személyazonosság-ellenőrzés céljából lesznek elküldve.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>6. rész – a Azure AD B2C házirend konfigurálása

1. Lépjen a házirendek mappában található [Azure ad B2C házirendre](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) .

2. A [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) letöltéséhez kövesse ezt a [dokumentumot](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack)

3. Konfigurálja a Azure AD B2C bérlő házirendjét.

>[!NOTE]
>Frissítse a megadott szabályzatokat az adott bérlőhöz kapcsolódóan.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a házirendek területen válassza az **identitási élmény keretrendszere**elemet.

2. Válassza ki a korábban létrehozott **SignUpSignIn**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   a. **Alkalmazás**: válassza ki a regisztrált alkalmazást (minta a JWT)

   b. **Válasz URL-címe**: válassza ki az **átirányítási URL-címet**

   c. Válassza a **felhasználói folyamat futtatása**lehetőséget.

4. Ugorjon a regisztrációs folyamatra, és hozzon létre egy fiókot

5. A Jumio szolgáltatás a folyamat során lesz meghívva, a felhasználói attribútum létrehozása után. Ha a folyamat hiányos, ellenőrizze, hogy a felhasználó nincs-e mentve a címtárban.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
