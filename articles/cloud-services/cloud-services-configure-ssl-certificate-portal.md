---
title: SSL konfigurálása felhőszolgáltatáshoz | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat meg HTTPS-végpontot egy webes szerepkörhöz, és hogyan tölthet fel SSL-tanúsítványt az alkalmazás védelméhez. Ezek a példák az Azure Portalt használják.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: tagore
ms.openlocfilehash: 6ddb7001f770a9d8aea38d1a4698e15c167aeaa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273136"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Az SSL beállítása Azure-alkalmazásokhoz

A Secure Socket Layer (SSL) titkosítás az interneten keresztül küldött adatok védelmének leggyakrabban használt módszere. Ez a gyakori feladat azt ismerteti, hogyan adhat meg EGY HTTPS-végpontot egy webes szerepkörhöz, és hogyan tölthet fel SSL-tanúsítványt az alkalmazás védelméhez.

> [!NOTE]
> A feladatban szereplő eljárások az Azure Cloud Servicesre vonatkoznak; az App Services, lásd [ezt](../app-service/configure-ssl-bindings.md).
>

Ez a feladat éles telepítést használ. Az átmeneti központi telepítés használatával kapcsolatos információk a témakör végén található.

Olvassa el [ezt](cloud-services-how-to-create-deploy-portal.md) az első, ha még nem hozott létre egy felhőalapú szolgáltatás.

## <a name="step-1-get-an-ssl-certificate"></a>1. lépés: SSL-tanúsítvány beszerezni
Az SSL alkalmazáshoz való konfigurálásához először be kell szereznie egy hitelesítésszolgáltató által aláírt SSL-tanúsítványt, amely megbízható harmadik fél, aki erre a célra tanúsítványokat állít ki. Ha még nem rendelkezik ilyensel, be kell szereznie egyet egy SSL-tanúsítványokat értékesítő vállalattól.

A tanúsítványnak meg kell felelnie az Alábbi követelményeknek az SSL-tanúsítványokra vonatkozóan az Azure-ban:

* A tanúsítványnak titkos kulcsot kell tartalmaznia.
* A tanúsítványt kulcscseréhez kell létrehozni, amely személyes adatcserefájlba (.pfx) exportálható.
* A tanúsítvány tulajdonosnevének meg kell egyeznie a felhőszolgáltatás eléréséhez használt tartománnyal. A cloudapp.net tartományhoz tartozó hitelesítésszolgáltatótól nem szerezhet be SSL-tanúsítványt. A szolgáltatás elérésekor egyéni tartománynevet kell beszereznie. Amikor tanúsítványt kér egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell egyeznie az alkalmazás eléréséhez használt egyéni tartománynévvel. Ha például az egyéni tartománynév **contoso.com** a hitelesítésszolgáltatótól a ***.contoso.com** vagy a **www\.contoso.com**tanúsítványt kell kérnie.
* A tanúsítványnak legalább 2048 bites titkosítást kell használnia.

Tesztelési célokra önaláírt tanúsítványt [hozhat létre](cloud-services-certs-create.md) és használhat. Az önaláírt tanúsítványok hitelesítésre nem szolgálnak, és a cloudapp.net tartományt használhatják webhely URL-címként. A következő feladat például egy önaláírt tanúsítványt használ, amelyben a tanúsítványban használt köznapi név (CN) **sslexample.cloudapp.net.**

Ezután a szolgáltatásdefinícióban és a szolgáltatáskonfigurációs fájlokban meg kell foglalnia a tanúsítványra vonatkozó információkat.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>2. lépés: A szolgáltatásdefiníció és a konfigurációs fájlok módosítása
Az alkalmazást úgy kell konfigurálni, hogy a tanúsítványt használja, és hozzá kell adni egy HTTPS-végpontot. Ennek eredményeképpen a szolgáltatásdefiníciót és a szolgáltatáskonfigurációs fájlokat frissíteni kell.

1. A fejlesztői környezetben nyissa meg a szolgáltatásdefiníciós fájlt (CSDEF), adjon hozzá egy **Tanúsítványok szakaszt** a **WebRole** szakaszban, és adja meg a következő információkat a tanúsítványról (és a köztes tanúsítványokról):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   A **Tanúsítványok** szakasz határozza meg tanúsítványunk nevét, helyét és annak az üzletnek a nevét, ahol található.

   Az`permissionLevel` Engedélyek (attribútum) az alábbi értékek egyikére állíthatók be:

   | Engedély értéke | Leírás |
   | --- | --- |
   | korlátozottOrElevated |**(Alapértelmezett)** Minden szerepkör-folyamat hozzáférhet a személyes kulcshoz. |
   | Emelkedett |Csak emelt szintű folyamatok férhetnek hozzá a személyes kulcshoz. |

2. A szolgáltatásdefiníciós fájlban adjon hozzá egy **InputEndpoint** elemet a **Végpontok** szakaszban a HTTPS engedélyezéséhez:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. A szolgáltatásdefiníciós fájlban adjon hozzá egy **kötési** elemet a **Helyek** szakaszban. Ez az elem https-kötést ad hozzá a végpont leképezéséhez a webhelyhez:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   A szolgáltatásdefiníciós fájl összes szükséges módosítása befejeződött; de továbbra is hozzá kell adnia a tanúsítvány adatait a szolgáltatás konfigurációs fájljához.
4. A serviceconfiguration.Cloud.cscfg szolgáltatáskonfigurációs fájljában (CSCFG) adjon hozzá egy **tanúsítványértéket** a tanúsítványhoz. A következő kódminta a **Tanúsítványok** szakasz részleteit tartalmazza, kivéve az ujjlenyomatértéket.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(Ez a példa **sha1-et** használ az ujjlenyomat-algoritmushoz. Adja meg a tanúsítvány ujjlenyomat-algoritmusának megfelelő értéket.)

Most, hogy a szolgáltatásdefiníció és a szolgáltatáskonfigurációs fájlok frissültek, csomagolja be a központi telepítést az Azure-ba való feltöltéshez. Ha **cspack programot**használ, ne használja a **/generateConfigurationFile** jelzőt, mert az felülírja az imént beszúrt tanúsítványadatokat.

## <a name="step-3-upload-a-certificate"></a>3. lépés: Tanúsítvány feltöltése
Csatlakozás az Azure portalhoz, és...

1. A portál **Minden erőforrás** szakaszában válassza ki a felhőszolgáltatást.

    ![A felhőszolgáltatás közzététele](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Kattintson **a Tanúsítványok gombra.**

    ![Kattintson a tanúsítványok ikonra](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Kattintson a **Feltöltés** gombra a tanúsítványok területének tetején.

    ![Kattintson a Feltöltés menüelemre](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Adja meg a **Fájl**, **Jelszó**, majd kattintson a **Feltöltés** alján az adatbeviteli terület.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>4. lépés: Csatlakozás a szerepkörpéldányhttps használatával
Most, hogy az üzembe helyezés az Azure-ban működik, https használatával csatlakozhat hozzá.

1. A webböngésző megnyitásához kattintson a **webhely URL-címére.**

   ![Kattintson a webhely URL-címére](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. A webböngészőben módosítsa a hivatkozást úgy, hogy **http**helyett **https-t** használjon, majd látogasson el az oldalra.

   > [!NOTE]
   > Ha önaláírt tanúsítványt használ, amikor az önaláírt tanúsítványhoz társított HTTPS-végpontot keresi, tanúsítványhiba jelenhet meg a böngészőben. A megbízható hitelesítésszolgáltató által aláírt tanúsítvány használata kiküszöböli a problémát; addig is figyelmen kívül hagyhatja a hibát. (Egy másik lehetőség, hogy az önaláírt tanúsítványt hozzáadja a felhasználó megbízható hitelesítésszolgáltatói tanúsítványtárolójához.)
   >
   >

   ![Webhely előnézete](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Ha az SSL-t éles környezet helyett átmeneti központi telepítéshez szeretné használni, először meg kell határoznia az átmeneti központi telepítéshez használt URL-címet. A felhőszolgáltatás üzembe helyezése után az átmeneti környezet URL-címét a telepítési azonosító GUID **azonosítója** határozza meg ebben a formátumban:`https://deployment-id.cloudapp.net/`  
   >
   > Hozzon létre egy tanúsítványt, amelynek közös neve (CN) megegyezik a GUID-alapú URL-címmel (például **328187776e774ceda8fc57609d404462.cloudapp.net).** A portál használatával adja hozzá a tanúsítványt a szakaszos felhőszolgáltatáshoz. Ezután adja hozzá a tanúsítványadatait a CSDEF- és CSCFG-fájlokhoz, csomagolja újra az alkalmazást, és frissítse a szakaszos központi telepítést az új csomag használatához.
   >

## <a name="next-steps"></a>További lépések
* [A felhőszolgáltatás általános konfigurációja](cloud-services-how-to-configure-portal.md).
* További információ a [felhőszolgáltatások üzembe helyezéséről.](cloud-services-how-to-create-deploy-portal.md)
* Egyéni [tartománynév konfigurálása](cloud-services-custom-domain-name-portal.md).
* [A felhőszolgáltatás kezelése.](cloud-services-how-to-manage-portal.md)



