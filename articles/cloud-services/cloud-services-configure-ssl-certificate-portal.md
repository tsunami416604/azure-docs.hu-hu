---
title: Az SSL konfigurálása felhőszolgáltatáshoz |} A Microsoft Docs
description: Ismerje meg, hogyan lehet egy webes szerepkörben HTTPS-végpontokat, és hogyan tölthetők fel SSL-tanúsítványok az alkalmazások biztonságossá tételéhez. Ezekben a példákban az Azure Portalon.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeconnoc
ms.openlocfilehash: 2a9879ebc55a5f25c1a358e386697dce1c55ec90
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084344"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Az SSL beállítása Azure-alkalmazásokhoz

Az SSL-alapú titkosítás a leggyakrabban használt módszer az interneten keresztül küldött adatok biztonságossá tételéhez. Ebben az általános feladatban azt fejtjük ki, hogy miként lehet HTTPS-végpontokat meghatározni webes szerepkörökhöz, és hogyan tölthetők fel SSL-tanúsítványok az alkalmazások biztonságossá tételéhez.

> [!NOTE]
> Ebben a feladatban eljárásokat alkalmazni az Azure Cloud Services; az App Services, lásd: [ez](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Ez a feladat egy éles telepítést használ. Egy átmeneti üzemelő példány használatával az információt a témakör végén található.

Olvasási [ez](cloud-services-how-to-create-deploy-portal.md) első, ha még nem hozott egy felhőalapú szolgáltatás.

## <a name="step-1-get-an-ssl-certificate"></a>1. lépés: SSL-tanúsítvány beszerzése
Az SSL konfigurálása az alkalmazáshoz, akkor először, amely szerint a hitelesítésszolgáltató (CA), akik erre a célra tanúsítványokat megbízható harmadik fél aláírt SSL-tanúsítvány beszerzése. Ha Ön még nem rendelkezik egy, kell egy olyan cég, amely képeit SSL-tanúsítványok beszerzése.

A tanúsítványt az SSL-tanúsítványok az Azure-ban az alábbi követelményeknek kell megfelelnie:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot.
* A kulcscseréhez használt, a személyes információcsere (.pfx) fájl exportálható a tanúsítványt kell létrehozni.
* A tanúsítvány tulajdonosnevének egyeznie kell a felhőalapú szolgáltatás eléréséhez használt tartományt. SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) cloudapp.net tartomány nem szerezheti be. Egy egyéni tartománynevet szeretne használni, amikor kell szerezni a szolgáltatás eléréséhez. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének egyeznie kell az egyéni tartománynevet az alkalmazás eléréséhez használt. Például, ha az egyéni tartománynév **contoso.com** tanúsítványt kérhet tenné a hitelesítésszolgáltató a ***. contoso.com** vagy **www\.contoso.com**.
* A tanúsítvány legalább 2048 bites titkosítást kell használnia.

Tesztelési célból is [létrehozása](cloud-services-certs-create.md) és a egy önaláírt tanúsítványt használja. Önaláírt tanúsítvány nincs hitelesítve a hitelesítésszolgáltató segítségével, és cloudapp.net tartományt is használja, mint a webhely URL-címe. Például a következő feladatot, amelyben a tanúsítványban használt köznapi név (CN) van önaláírt tanúsítványt használ **sslexample.cloudapp.net**.

Ezután szerepelnie kell a tanúsítvány adatait a szolgáltatás definíciós és konfigurációs fájljai.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>2. lépés: A definíció- és konfigurációs fájljainak módosítása
Az alkalmazás a tanúsítvány használatára kell konfigurálni, és hozzá kell adni a HTTPS-végpontokat. Ennek eredményeképpen a szolgáltatásdefiníció és a szolgáltatás konfigurációs fájlok frissíteni kell.

1. A fejlesztői környezetben nyissa meg a szolgáltatásdefiníciós fájlt (CSDEF), adjon hozzá egy **tanúsítványok** belül szakasz a **WebRole** szakaszt, és a tanúsítvány a következő információkat tartalmaznak (és köztes tanúsítványok):

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

   A **tanúsítványok** szakasz meghatározza a tanúsítvány, helyét és a tároló helyétől neve nevét.

   Engedélyek (`permissionLevel` attribútum) értékre lehet beállítani a következő értékek egyikét:

   | Engedély érték | Leírás |
   | --- | --- |
   | limitedOrElevated |**(Alapértelmezett)**  Minden szerepkör rendszerfolyamatok érik el a titkos kulcsot. |
   | emelt szintű |Csak emelt szintű rendszerfolyamatok érik el a titkos kulcsot. |

2. A szolgáltatásdefiníciós fájlban, adjon hozzá egy **bemeneti végponthoz** elemen belül a **végpontok** című szakaszt a HTTPS engedélyezése:

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

3. A szolgáltatásdefiníciós fájlban, adjon hozzá egy **kötés** elemen belül a **helyek** szakaszban. Ez az elem a végpont leképezése a webhely HTTPS-kötést ad hozzá:

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

   A szükséges módosításokat a szolgáltatásdefiníciós fájlban már befejeződött; de továbbra is szeretné a Tanúsítványadatok hozzáadása a szolgáltatás konfigurációs fájlja.
4. A szolgáltatás konfigurációs fájljában (CSCFG), ServiceConfiguration.Cloud.cscfg, adjon hozzá egy **tanúsítványok** értéket, amely a tanúsítvány. Az alábbi kódmintában részletesen ismerteti a **tanúsítványok** szakaszban, kivéve az ujjlenyomat értékét.

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

(Ez a példa **sha1** az ujjlenyomat-algoritmus. Adjon meg a megfelelő értéket a tanúsítvány-ujjlenyomat-algoritmus.)

Most, hogy frissítve lett-e a szolgáltatás definíciós és a szolgáltatás konfigurációs fájljait, a csomag feltöltése az Azure-bA a központi telepítés. Ha használ **cspack**, ne használja a **/generateConfigurationFile** jelzőt, ahogy, amely felülírja az imént beszúrt tanúsítványának adatait.

## <a name="step-3-upload-a-certificate"></a>3. lépés: Tanúsítvány feltöltése
Csatlakozás az Azure Portalra, és...

1. Az a **összes erőforrás** szakaszban a portál, válassza ki a felhőalapú szolgáltatást.

    ![A felhőszolgáltatások közzététele](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Kattintson a **tanúsítványok**.

    ![A tanúsítványok ikonra](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Kattintson a **feltöltése** felső részén a tanúsítványok területen.

    ![Kattintson a feltöltés menüelem](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Adja meg a **fájl**, **jelszó**, majd kattintson a **feltöltése** a adatok terület alján.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>4. lépés: Csatlakozás a szerepkörpéldány HTTPS használatával
Most, hogy az üzembe helyezés működik és elérhető az Azure-ban, HTTPS használatával csatlakozhat.

1. Kattintson a **webhely URL-címe** a böngésző megnyitásához.

   ![Kattintson a webhely URL-címe](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. A böngészőben módosíthatja a hivatkozás használata **https** helyett **http**, majd látogasson el a lapot.

   > [!NOTE]
   > Egy önaláírt tanúsítványt, a használatát, amikor HTTPS-végpont az önaláírt tanúsítvány társított keresse meg a böngészőben egy tanúsítvány hiba jelenhet meg. Egy megbízható hitelesítésszolgáltató által aláírt tanúsítvány használatával eltávolítja ezt a problémát. addig is figyelmen kívül hagyhatja a hibát. (Egy másik lehetőség, hogy az önaláírt tanúsítvány hozzáadása a felhasználói megbízható hitelesítésszolgáltatók tanúsítványtárolójában.)
   >
   >

   ![Hely előzetes verzió](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Ha azt szeretné, egy átmeneti üzembe helyezés éles környezet helyett SSL használatát, először szüksége meghatározni az átmeneti üzembe helyezéshez használt URL-CÍMÉT. A cloud Services üzembe helyezése után az átmeneti környezet URL-CÍMÉT határozza meg a **üzemelő példány azonosítója** GUID azonosítója a következő formátumban: `https://deployment-id.cloudapp.net/`  
   >
   > Tanúsítvány létrehozása, ha a köznapi név (CN) a GUID-alapú URL-címe megegyezik (például **328187776e774ceda8fc57609d404462.cloudapp.net**). A portál használatával adja hozzá a tanúsítványt a manuálisan előkészített felhőszolgáltatáshoz. A tanúsítvány adatait, majd hozzá a CSDEF és a szolgáltatáskonfigurációs SÉMA fájljaihoz, csomagolja újra az alkalmazást, és a kétlépcsős üzembe helyezéssel, az új csomag frissítése.
   >

## <a name="next-steps"></a>További lépések
* [A felhőszolgáltatás általános konfigurációs](cloud-services-how-to-configure-portal.md).
* Ismerje meg, hogyan [egy felhőalapú szolgáltatás üzembe helyezése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* [A felhőszolgáltatások kezelése](cloud-services-how-to-manage-portal.md).
