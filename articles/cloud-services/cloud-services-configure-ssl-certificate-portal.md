---
title: Az SSL konfigurálása egy felhőalapú szolgáltatás |} Microsoft Docs
description: 'Útmutató: a webes szerepkör HTTPS-végpontnak megadása és az alkalmazás biztonságos SSL-tanúsítvány feltöltése. Ezekben a példákban az Azure-portálon.'
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.openlocfilehash: 0e053ad7f1033317948b6ef0856984b21e56e425
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
ms.locfileid: "24859776"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>SSL konfigurálása az alkalmazás az Azure-ban

Az SSL-alapú titkosítás a leggyakrabban használt módszer az interneten keresztül küldött adatok biztonságossá tételéhez. Ebben az általános feladatban azt fejtjük ki, hogy miként lehet HTTPS-végpontokat meghatározni webes szerepkörökhöz, és hogyan tölthetők fel SSL-tanúsítványok az alkalmazások biztonságossá tételéhez.

> [!NOTE]
> Ebben a feladatban eljárásokat alkalmazni az Azure Felhőszolgáltatások; alkalmazásszolgáltatások, lásd: [ez](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Ez a feladat használja az éles környezet. Az átmeneti központi telepítéssel információt a témakör végén.

Olvasási [ez](cloud-services-how-to-create-deploy-portal.md) első, ha egy felhőalapú szolgáltatás még nem létrehozott.

## <a name="step-1-get-an-ssl-certificate"></a>1. lépés: SSL-tanúsítvány beszerzése
Az SSL konfigurálása az alkalmazáshoz, akkor először kell, amely által egy tanúsítvány (CA), a megbízható tanúsítványokat állít ki erre a célra harmadik fél aláírt SSL-tanúsítvány beszerzése. Ha még nem rendelkezik egy, meg kell szereznie egy SSL-tanúsítványok értékesít vállalat közül.

A tanúsítványt az SSL-tanúsítványok az Azure-ban az alábbi követelményeknek kell megfelelniük:

* A tanúsítványnak tartalmaznia kell egy titkos kulccsal.
* A kulcscseréhez használt, a személyes információcsere (.pfx) fájl exportálható léteznie kell a tanúsítványt.
* A tanúsítvány tulajdonosának nevét meg kell egyeznie a felhőalapú szolgáltatás eléréséhez használt tartomány. Az SSL-tanúsítvány nem lehet megszerezni a cloudapp.net tartomány hitelesítésszolgáltatótól (CA) származó. Egy egyéni tartománynevet használhat, amikor be kell szerezni a szolgáltatáshoz. A hitelesítésszolgáltató tanúsítvány kérése, amikor a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynév, az alkalmazás eléréséhez használt. Például, ha az egyéni tartománynevet **contoso.com** tanúsítványt kérhet tenné a hitelesítésszolgáltató a ***. contoso.com** vagy **www.contoso.com**.
* A tanúsítvány legalább 2048 bites titkosítást kell használnia.

Tesztelési célokra is [létrehozása](cloud-services-certs-create.md) és egy önaláírt tanúsítványt használjon. Egy önaláírt tanúsítványt nem lehet hitelesíteni a hitelesítésszolgáltató használatával, és a cloudapp.net tartomány használható a webhely URL-címe. Például a következő feladatot használ egy önaláírt tanúsítványt, amelyben a tanúsítványban használt köznapi név (CN) van **sslexample.cloudapp.net**.

A következő szerepelnie kell a tanúsítvány adatait a szolgáltatás definíciós és konfigurációs fájlok.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>2. lépés: A definíció- és konfigurációs fájljainak módosítása
Az alkalmazás be kell állítani a tanúsítványt használja, és a HTTPS-végpontnak kell hozzáadni. Ennek eredményeképpen a szolgáltatásdefiníció és konfigurációs fájlok frissítenie kell.

1. A fejlesztői környezetben nyissa meg a szolgáltatásdefiníciós fájlban (CSDEF), adja hozzá a **tanúsítványok** belül szakasz a **webrole típusról** szakaszt, és a következő információkat tartalmaznak a tanúsítvány (és köztes tanúsítványokat):

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

   A **tanúsítványok** szakasz határozza meg a nevét, valamint a tanúsítvány, helyére, a tárolási helyétől nevét.

   Engedélyek (`permisionLevel` attribútum) a következő értékek egyikére állítható be:

   | Engedély érték | Leírás |
   | --- | --- |
   | limitedOrElevated |**(Alapértelmezett)**  Összes szerepkör folyamat hozzáférhet a titkos kulcsot. |
   | emelt szintű |Csak a rendszergazda jogú folyamatok férhetnek hozzá a titkos kulcsot. |

2. A szolgáltatásdefiníciós fájlban, adja hozzá egy **bemeneti végponthoz** elemet a **végpontok** szakaszt, hogy HTTPS:

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

3. A szolgáltatásdefiníciós fájlban, vegye fel a **kötés** elemet a **helyek** szakasz. Ez az elem egy HTTPS-kötést a végpont hozzárendelése a webhely hozzáadása:

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

   A szükséges módosításokat a szolgáltatásdefiníciós fájlban befejeződtek; azonban továbbra is szeretné az tanúsítvány-adatokat hozzáadni a szolgáltatás konfigurációs fájljában.
4. A szolgáltatás konfigurációs fájljában (a szolgáltatáskonfigurációs SÉMA), ServiceConfiguration.Cloud.cscfg, vegye fel a **tanúsítványok** értéket, amely a tanúsítvány. A következő példakód ismerteti a **tanúsítványok** szakaszban, kivéve az ujjlenyomat értékét.

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

(A példában az **sha1** ujjlenyomat algoritmust. Adja meg a megfelelő értéket a tanúsítvány ujjlenyomata algoritmushoz.)

Most, hogy a szolgáltatás definíció- és konfigurációs fájlok frissültek, csomag a központi telepítés feltöltése az Azure-bA. Ha használ **cspack**, ne használjon a **/generateConfigurationFile** jelzőt, mivel, amelyek felülírják az imént beillesztett tanúsítvány adatait.

## <a name="step-3-upload-a-certificate"></a>3. lépés: A tanúsítvány feltöltése
Az Azure-portál csatlakozni és...

1. Az a **összes erőforrás** szakasz a portált, válassza ki a felhőalapú szolgáltatás.

    ![A felhőalapú szolgáltatás közzététele](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Kattintson a **tanúsítványok**.

    ![A tanúsítványok ikonra](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Kattintson a **feltöltése** tanúsítványok területe tetején.

    ![Kattintson a feltöltés menüpont](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Adja meg a **fájl**, **jelszó**, majd kattintson a **feltöltése** a adatok terület alján.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>4. lépés: Csatlakozás a szerepkörpéldányt HTTPS használatával
Most, hogy a központi telepítés megfelelően működik, és az Azure-ban, csatlakozhat, a HTTPS-kapcsolaton keresztül.

1. Kattintson a **webhely URL-címe** megnyitása a webböngészőben.

   ![Kattintson a webhely URL-címe](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. A böngészőben, módosítsa a hivatkozásra kattintva **https** helyett **http**, és keresse fel a lap.

   > [!NOTE]
   > Ha használ egy önaláírt tanúsítványt, ha tallózással keres egy HTTPS-végpontot, amely egy tanúsítvány hiba a böngészőben megjelenik az önaláírt tanúsítvány van társítva. Megszünteti egy megbízható hitelesítésszolgáltató által aláírt tanúsítványt használ az ezt a problémát. időközben figyelmen kívül hagyhatja a hibát. (Az önaláírt tanúsítvány hozzáadása a felhasználói megbízható tanúsítvány tanúsítványtárolójába egy másik lehetőséget.)
   >
   >

   ![Hely megtekintése](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Ha azt szeretné, SSL használatát az éles környezet helyett egy átmeneti központi telepítést, először az átmeneti központi telepítéshez használt URL-cím meghatározásához. Miután a felhőalapú szolgáltatás van telepítve, az átmeneti URL-CÍMÉT határozza meg a **üzembehelyezés-azonosító** GUID formátuma:`https://deployment-id.cloudapp.net/`  
   >
   > Hozzon létre egy tanúsítványt a köznapi nevének (CN) egyenlő a GUID-alapú URL-cím (például **328187776e774ceda8fc57609d404462.cloudapp.net**). A portál használatával a tanúsítvány felvétele a előkészített felhőalapú szolgáltatás. A tanúsítvány adatait, majd hozzá a CSDEF és a szolgáltatáskonfigurációs SÉMA fájljaihoz, csomagolja újra az alkalmazást, és a szakaszos telepítés az új csomag frissítése.
   >

## <a name="next-steps"></a>Következő lépések
* [A felhőalapú szolgáltatás általános konfigurációs](cloud-services-how-to-configure-portal.md).
* Megtudhatja, hogyan [felhőalapú szolgáltatás telepítése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage-portal.md).
