---
title: Az SSL konfigurálása a Cloud Service-hez | Microsoft Docs
description: Megtudhatja, hogyan határozhat meg egy HTTPS-végpontot webes szerepkörhöz, és hogyan tölthetők fel SSL-tanúsítványok az alkalmazások biztonságossá tételéhez. Ezek a példák a Azure Portal használják.
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: gwallace
ms.openlocfilehash: 9e7b7526f13fa6b9ae648c4ddb4004a627d85154
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359734"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Az SSL konfigurálása Azure-beli alkalmazásokhoz

Az SSL-alapú titkosítás a leggyakrabban használt módszer az interneten keresztül küldött adatok biztonságossá tételéhez. Ebben az általános feladatban azt fejtjük ki, hogy miként lehet HTTPS-végpontokat meghatározni webes szerepkörökhöz, és hogyan tölthetők fel SSL-tanúsítványok az alkalmazások biztonságossá tételéhez.

> [!NOTE]
> A feladat eljárásai az Azure Cloud Servicesra vonatkoznak; App Services esetén [Ez](../app-service/app-service-web-tutorial-custom-ssl.md)a következő:.
>

Ez a feladat éles üzembe helyezést használ. A témakör végén található az átmeneti üzembe helyezés használatával kapcsolatos információk.

Először [olvassa](cloud-services-how-to-create-deploy-portal.md) el, ha még nem hozott létre felhőalapú szolgáltatást.

## <a name="step-1-get-an-ssl-certificate"></a>1\. lépés: SSL-tanúsítvány beszerzése
Az SSL konfigurálásához először be kell szereznie egy, a hitelesítésszolgáltató (CA) által aláírt SSL-tanúsítványt, amely egy megbízható harmadik fél, aki erre a célra tanúsítványt bocsát ki. Ha még nem rendelkezik ilyennel, be kell szereznie egy olyan vállalattól, amely SSL-tanúsítványokat árul.

A tanúsítványnak meg kell felelnie az alábbi követelményeknek az SSL-tanúsítványokhoz az Azure-ban:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot.
* A tanúsítványt létre kell hozni a személyes információcsere (. pfx) fájlba exportálható kulcscsere-fájlhoz.
* A tanúsítvány tulajdonosának nevének meg kell egyeznie a felhőalapú szolgáltatás eléréséhez használt tartománnyal. A cloudapp.net tartományhoz nem lehet SSL-tanúsítványt beszerezni a hitelesítésszolgáltatótól (CA). A szolgáltatáshoz való hozzáféréskor egyéni tartománynevet kell megadnia. Amikor tanúsítványt kér egy HITELESÍTÉSSZOLGÁLTATÓTÓL, a tanúsítvány tulajdonosának nevének meg kell egyeznie az alkalmazás eléréséhez használt egyéni tartománynévvel. Ha például az Egyéni tartománynév a **contoso.com** , akkor a (z **) *. contoso.com** vagy a **www\.contoso.com**tanúsítványt kér a hitelesítésszolgáltatótól.
* A tanúsítványnak legalább 2048 bites titkosítást kell használnia.

Tesztelési célból [létrehozhat](cloud-services-certs-create.md) és használhat önaláírt tanúsítványokat. Az önaláírt tanúsítványokat a rendszer nem hitelesíti a HITELESÍTÉSSZOLGÁLTATÓn keresztül, és a cloudapp.net tartományt használhatja a webhely URL-címéhez. A következő feladat például egy önaláírt tanúsítványt használ, amelyben a tanúsítványban használt köznapi név (CN) **sslexample.cloudapp.net**.

Ezután meg kell adnia a tanúsítványra vonatkozó adatokat a szolgáltatás definíciójában és a szolgáltatás konfigurációs fájljaiban.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>2\. lépés: A szolgáltatás definíciójának és konfigurációs fájljainak módosítása
Az alkalmazást a tanúsítvány használatára kell konfigurálni, és hozzá kell adni egy HTTPS-végpontot. Ennek eredményeképpen frissíteni kell a szolgáltatás definícióját és a szolgáltatás konfigurációs fájljait.

1. A fejlesztési környezetében nyissa meg a Service Definition (CSDEF) fájlt, adja hozzá a **tanúsítványok** szakaszt a webrole szakaszban, és adja meg a következő információkat a tanúsítványról (és a köztes tanúsítványokról):

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

   A **tanúsítványok** szakasz a tanúsítvány nevét és helyét, valamint annak az áruháznak a nevét határozza meg, ahol az található.

   Az engedélyek`permissionLevel` (attribútumok) a következő értékek egyikére állíthatók be:

   | Engedély értéke | Leírás |
   | --- | --- |
   | limitedOrElevated |**(Alapértelmezett)** Az összes szerepkör-folyamat hozzáfér a titkos kulcshoz. |
   | emelt szintű |Csak emelt szintű folyamatok férhetnek hozzá a titkos kulcshoz. |

2. A szolgáltatás definíciós fájljában adjon hozzá egy **InputEndpoint** elemet a **végpontok** szakaszban a https engedélyezéséhez:

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

3. A szolgáltatás definíciós fájljában adjon hozzá egy **kötési** elemet a **helyek** szakaszon belül. Ez az elem egy HTTPS-kötés hozzáadásával rendeli hozzá a végpontot a webhelyhez:

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

   A szolgáltatás-definíciós fájl összes szükséges módosítása befejeződött; azonban továbbra is hozzá kell adnia a tanúsítvány adatait a szolgáltatás konfigurációs fájljához.
4. A szolgáltatás konfigurációs fájljában (CSCFG) ServiceConfiguration. Cloud. CSCFG, adjon hozzá egy **tanúsítvány** értéket a tanúsítványához. A következő mintakód a **tanúsítványok** szakasz részleteit tartalmazza, kivéve az ujjlenyomat értékét.

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

(Ez a példa **SHA1** -t használ az ujjlenyomat-algoritmushoz. Adja meg a tanúsítvány ujjlenyomat-algoritmusának megfelelő értéket.)

Most, hogy frissítettük a szolgáltatás definícióját és a szolgáltatás konfigurációs fájljait, csomagolja ki az üzembe helyezést az Azure-ba való feltöltéshez. Ha a **cspack**-t használja, ne használja a **/generateConfigurationFile** jelzőt, mert ez felülírja az imént beszúrt tanúsítvány-információkat.

## <a name="step-3-upload-a-certificate"></a>3\. lépés: Tanúsítvány feltöltése
Kapcsolódás a Azure Portalhoz és...

1. A portál **minden erőforrás** szakaszában válassza ki a felhőalapú szolgáltatást.

    ![A felhőalapú szolgáltatás közzététele](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Kattintson a **tanúsítványok**elemre.

    ![Kattintson a tanúsítványok ikonra](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Kattintson a **feltöltés** gombra a tanúsítványok terület tetején.

    ![Kattintson a feltöltés menüpontra](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Adja meg a **fájlt**, a **jelszót**, majd kattintson a **feltöltés** elemre az adatbeviteli terület alján.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>4\. lépés: Kapcsolódás a szerepkör-példányhoz HTTPS használatával
Most, hogy üzembe helyezése az Azure-ban működik, HTTPS használatával csatlakozhat hozzá.

1. A **webhely URL** -címére kattintva nyissa meg a webböngészőt.

   ![Kattintson a webhely URL-címére](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. A webböngészőben módosítsa a **http**helyett **https** használatára mutató hivatkozást, majd látogasson el az oldalra.

   > [!NOTE]
   > Ha önaláírt tanúsítványt használ, az önaláírt tanúsítványhoz tartozó HTTPS-végpontra való tallózáskor a böngészőben a tanúsítvány hibája is megjelenik. Egy megbízható hitelesítésszolgáltató által aláírt tanúsítvány használata kiküszöböli ezt a problémát; addig is figyelmen kívül hagyhatja a hibát. (Egy másik lehetőség, hogy hozzáadja az önaláírt tanúsítványt a felhasználó megbízható hitelesítésszolgáltatójának tanúsítványtárolóhoz.)
   >
   >

   ![Webhely előzetes verziója](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Ha éles üzembe helyezés helyett egy átmeneti üzembe helyezéshez SSL-t szeretne használni, először meg kell határoznia az átmeneti üzembe helyezéshez használt URL-címet. Miután telepítette a felhőalapú szolgáltatást, az átmeneti környezet URL-címét a **központi telepítési azonosító** GUID azonosítója határozza meg ebben a formátumban:`https://deployment-id.cloudapp.net/`  
   >
   > Hozzon létre egy common name (CN) nevű tanúsítványt a GUID-alapú URL-címmel (például **328187776e774ceda8fc57609d404462.cloudapp.net**). A portál használatával adja hozzá a tanúsítványt a szakaszos felhőalapú szolgáltatáshoz. Ezután adja hozzá a tanúsítvány adatait a CSDEF és a CSCFG-fájlokhoz, csomagolja ki az alkalmazást, és frissítse a szakaszos üzembe helyezést az új csomag használatára.
   >

## <a name="next-steps"></a>További lépések
* [A felhőalapú szolgáltatás általános konfigurációja](cloud-services-how-to-configure-portal.md).
* Ismerje meg, hogyan [helyezhet üzembe egy felhőalapú szolgáltatást](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja az [Egyéni tartománynevet](cloud-services-custom-domain-name-portal.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage-portal.md).
