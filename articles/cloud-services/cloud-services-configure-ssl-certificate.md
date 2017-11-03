---
title: "Az SSL konfigurálása egy felhőalapú szolgáltatás (klasszikus) |} Microsoft Docs"
description: "Útmutató: a webes szerepkör HTTPS-végpontnak megadása és az alkalmazás biztonságos SSL-tanúsítvány feltöltése."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 4cbb7f38-7994-454d-b4f0-7259b558c766
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
ms.openlocfilehash: 7df2371f64f0d8a9fabc0df37c17d4dfbc47cd7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>SSL konfigurálása az alkalmazás az Azure-ban
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-configure-ssl-certificate-portal.md)
> * [klasszikus Azure portál](cloud-services-configure-ssl-certificate.md)
> 
> 

Az SSL-alapú titkosítás a leggyakrabban használt módszer az interneten keresztül küldött adatok biztonságossá tételéhez. Ebben az általános feladatban azt fejtjük ki, hogy miként lehet HTTPS-végpontokat meghatározni webes szerepkörökhöz, és hogyan tölthetők fel SSL-tanúsítványok az alkalmazások biztonságossá tételéhez.

> [!NOTE]
> Ebben a feladatban eljárásokat alkalmazni az Azure Felhőszolgáltatások; alkalmazásszolgáltatások, lásd: [ez](../app-service/app-service-web-tutorial-custom-ssl.md) cikk.
> 
> 

Ez a feladat használja az éles környezet. Az átmeneti központi telepítéssel információt a témakör végén.

Olvasási [ez](cloud-services-how-to-create-deploy.md) először annak, ha egy felhőalapú szolgáltatás még nem létrehozott.

## <a name="step-1-get-an-ssl-certificate"></a>1. lépés: SSL-tanúsítvány beszerzése
Az SSL konfigurálása az alkalmazáshoz, akkor először kell, amely által egy tanúsítvány (CA), a megbízható tanúsítványokat állít ki erre a célra harmadik fél aláírt SSL-tanúsítvány beszerzése. Ha még nem rendelkezik egy, meg kell szereznie egy SSL-tanúsítványok értékesít vállalat közül.

A tanúsítványt az SSL-tanúsítványok az Azure-ban az alábbi követelményeknek kell megfelelniük:

* A tanúsítványnak tartalmaznia kell egy titkos kulccsal.
* A kulcscseréhez használt, a személyes információcsere (.pfx) fájl exportálható léteznie kell a tanúsítványt.
* A tanúsítvány tulajdonosának nevét meg kell egyeznie a felhőalapú szolgáltatás eléréséhez használt tartomány. Az SSL-tanúsítvány nem lehet megszerezni a cloudapp.net tartomány hitelesítésszolgáltatótól (CA) származó. Egy egyéni tartománynevet használhat, amikor be kell szerezni a szolgáltatáshoz. A hitelesítésszolgáltató tanúsítvány kérése, amikor a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynév, az alkalmazás eléréséhez használt. Például, ha az egyéni tartománynevet **contoso.com** tanúsítványt kérhet tenné a hitelesítésszolgáltató a ***. contoso.com** vagy **www.contoso.com**.
* A tanúsítvány legalább 2048 bites titkosítást kell használnia.

Tesztelési célokra is [létrehozása](cloud-services-certs-create.md) és egy önaláírt tanúsítványt használjon. Egy önaláírt tanúsítványt nem lehet hitelesíteni a hitelesítésszolgáltató használatával, és a cloudapp.net tartomány használható a webhely URL-címe. Például a következő feladatot használ egy önaláírt tanúsítványt, amelyben a tanúsítványban használt köznapi név (CN) van **sslexample.cloudapp.net**.

A következő szerepelnie kell a tanúsítvány adatait a szolgáltatás definíciós és konfigurációs fájlok.

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

3. A szolgáltatásdefiníciós fájlban, vegye fel a **kötés** elemet a **helyek** szakasz. Ez a szakasz egy HTTPS-kötést a végpont hozzárendelése a webhely hozzáadása:
   
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
   
   A szükséges módosításokat a szolgáltatásdefiníciós fájlban való elvégzése után, de továbbra is szeretné az tanúsítvány-adatokat hozzáadni a szolgáltatás konfigurációs fájljában.
4. A szolgáltatás konfigurációs fájljában (a szolgáltatáskonfigurációs SÉMA), ServiceConfiguration.Cloud.cscfg, vegye fel a **tanúsítványok** belül szakasz a **szerepkör** szakasz behelyettesíteni a minta ujjlenyomat értékét, hogy a tanúsítvány az alább látható:
   
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

(Az előző példában **sha1** ujjlenyomat algoritmust. Adja meg a megfelelő értéket a tanúsítvány ujjlenyomata algoritmushoz.)

Most, hogy a szolgáltatás definíció- és konfigurációs fájlok frissültek, csomag a központi telepítés feltöltése az Azure-bA. Ha használ **cspack**, ne használjon a **/generateConfigurationFile** jelzőt, módon, hogy felülírja a tanúsítvány adatait, szúrja be.

## <a name="step-3-upload-a-certificate"></a>3. lépés: A tanúsítvány feltöltése
A központi telepítési csomag frissült a tanúsítványt használja, és a HTTPS-végpontnak hozzá lett adva. Most már feltöltheti a csomagok és tanúsítványok az Azure-bA a klasszikus Azure portálon.

1. Jelentkezzen be a [a klasszikus Azure portálon][Azure classic portal]. 
2. Kattintson a **Felhőszolgáltatások** a bal oldali navigációs ablaktáblán.
3. Kattintson a kívánt felhőalapú szolgáltatás.
4. Kattintson a **tanúsítványok** fülre.
   
    ![Kattintson a tanúsítványok lap](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. Kattintson az **Upload** (Feltöltés) gombra.
   
    ![Feltöltés](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. Adja meg a **fájl**, **jelszó**, majd kattintson a **Complete** (a pipa).

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>4. lépés: Csatlakozás a szerepkörpéldányt HTTPS használatával
Most, hogy a központi telepítés megfelelően működik, és az Azure-ban, csatlakozhat, a HTTPS-kapcsolaton keresztül.

1. A klasszikus Azure portálon, válassza ki a központi telepítést, majd kattintson a hivatkozásra kattintva **webhely URL-címe**.
   
   ![Határozza meg a webhely URL-címe][2]
2. A böngészőben, módosítsa a hivatkozásra kattintva **https** helyett **http**, és keresse fel a lap.
   
   > [!NOTE]
   > Ha használ egy önaláírt tanúsítványt, ha tallózással keres egy HTTPS-végpontot, amely egy tanúsítvány hiba a böngészőben megjelenik az önaláírt tanúsítvány van társítva. Megszünteti egy megbízható hitelesítésszolgáltató által aláírt tanúsítványt használ az ezt a problémát. időközben figyelmen kívül hagyhatja a hibát. (Az önaláírt tanúsítvány hozzáadása a felhasználói megbízható tanúsítvány tanúsítványtárolójába egy másik lehetőséget.)
   > 
   > 
   
   ![SSL-webhely – példa][3]

Ha azt szeretné, SSL használatát az éles környezet helyett egy átmeneti központi telepítést, először az átmeneti központi telepítéshez használt URL-cím meghatározásához. A felhőalapú szolgáltatás telepítése az átmeneti tanúsítvány vagy a tanúsítvány adatainak megadása nélkül. Amennyiben telepített, meghatározhatja a GUID-alapú URL-címet, amely szerepel az Azure klasszikus portálon **webhely URL-címe** mező. Hozzon létre egy tanúsítványt a köznapi nevének (CN) egyenlő a GUID-alapú URL-cím (például **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). A klasszikus Azure portál segítségével a tanúsítvány felvétele a előkészített felhőalapú szolgáltatás. A tanúsítvány adatait, majd hozzá a CSDEF és a szolgáltatáskonfigurációs SÉMA fájljaihoz, csomagolja újra az alkalmazást, és a szakaszos telepítés az új csomag frissítése.

## <a name="next-steps"></a>Következő lépések
* [A felhőalapú szolgáltatás általános konfigurációs](cloud-services-how-to-configure.md).
* Megtudhatja, hogyan [felhőalapú szolgáltatás telepítése](cloud-services-how-to-create-deploy.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage.md).

[Azure classic portal]: http://manage.windowsazure.com
[0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
[1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
[2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
[3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
[4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png  
