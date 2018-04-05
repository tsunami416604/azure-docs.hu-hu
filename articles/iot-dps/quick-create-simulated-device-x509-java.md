---
title: Szimulált X.509-eszköz kiépítése Azure IoT Hubra Java használatával | Microsoft Docs
description: Azure rövid útmutató – Szimulált X.509-eszköz létrehozása és kiépítése az IoT Hub Device Provisioning Service-hez készült Java eszközoldali SDK-val
services: iot-dps
keywords: ''
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: f9eb572897854cfb2927b0282d25d5c272abd0e8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Szimulált X.509-eszköz létrehozása és kiépítése az IoT Hub Device Provisioning Service-hez készült Java eszközoldali SDK-val
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ezek a lépések bemutatják, hogyan hozhat létre szimulált X.509-eszközt egy Windows operációs rendszert futtató fejlesztői gépen, és hogyan használhat egy kódmintát, hogy ezt a szimulált eszközt összekösse az eszközkiépítési szolgáltatással és az IoT Hubbal. 

A folytatás előtt végezze el az [IoT Hub Device Provisioning Service beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit.

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy a [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) telepítve van a gépén.

1. Töltse le és telepítse a [Mavent](https://maven.apache.org/install.html).

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

1. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat az eszközszimuláció kódmintájához:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
1. Lépjen az azure-iot-sdk-java gyökérmappájába, és állítsa össze a projektet az összes szükséges csomag letöltése érdekében.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
1. Lépjen a tanúsítványt létrehozó projekthez, és építse ki a projektet. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. Lépjen a célmappára, és futtassa a létrehozott jar-fájlt.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. Hozza létre a regisztrációs információkat a következő módszerek egyikével, a beállításnak megfelelően:

    - **Egyéni regisztráció**:

        1. A **Do you want to input common name** (Szeretne megadni köznapi nevet?) kérdésnél adja meg az _N_ értéket. Másolja a vágólapra a `Client Cert` kimenetét a *-----BEGIN CERTIFICATE-----* sortól az *-----END CERTIFICATE-----* sorig.

            ![Egyénitanúsítvány-készítő](./media/java-quick-create-simulated-device-x509/individual.png)

        1. Hozzon létre egy **_X509individual.pem_** nevű fájlt a Windows rendszerű gépén, nyissa meg egy tetszőleges szövegszerkesztőben, és másolja bele a vágólapra kimásolt szöveget. Mentse a fájlt.

        1. A **Do you want to input Verification Code** (Szeretne megadni ellenőrző kódot?) kérdésnél adja meg az _N_ értéket, és hagyja megnyitva a program kimenetét, hogy a rövid útmutató későbbi szakaszában hivatkozhasson rá. Jegyezze fel az _Ügyféltanúsítvány_ és az _Ügyféltanúsítvány titkos kulcsa_ értékét.
    
    - **Regisztrációs csoportok**:

        1. A **Do you want to input common name** (Szeretne megadni köznapi nevet?) kérdésnél adja meg az _N_ értéket. Másolja a vágólapra a `Root Cert` kimenetét a *-----BEGIN CERTIFICATE-----* sortól az *-----END CERTIFICATE-----* sorig.

            ![Csoportostanúsítvány-készítő](./media/java-quick-create-simulated-device-x509/group.png)

        1. Hozzon létre egy **_X509group.pem_** nevű fájlt a Windows rendszerű gépén, nyissa meg egy tetszőleges szövegszerkesztőben, és másolja bele a vágólapra kimásolt szöveget. Mentse a fájlt.

        1. A **Do you want to input Verification Code** (Szeretne megadni ellenőrző kódot?) kérdésnél adja meg az _Y_ értéket, és hagyja megnyitva a programot, hogy a rövid útmutató későbbi szakaszában is használhassa. Jegyezze fel az _Ügyféltanúsítvány_, az _Ügyféltanúsítvány titkos kulcsa_, az _Aláíró tanúsítványa_ és a _Főtanúsítvány_ értékét.

        > [!NOTE]
        > A fenti `Root Cert` csak olyan tanúsítványokra vonatkozik, amelyek a konzol kimeneteként jöttek létre, és nem használható további ügyféltanúsítványok aláírására. Ha nagyobb teszttanúsítvány-készletre van szüksége, tekintse meg a [hitelesítésszolgáltatói tanúsítványok kezelésére szolgáló mintát](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).
        >

## <a name="create-a-device-enrollment-entry"></a>Eszközregisztrációs bejegyzés létrehozása

1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson az **Összes erőforrás** gombra, és nyissa meg az eszközkiépítési szolgáltatását.

1. Adja meg a regisztrációs információkat a következő módszerek egyikével, a beállításnak megfelelően:

    - **Egyéni regisztráció**: 

        1. Az eszközkiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza az **Egyéni beléptetések** fület, és kattintson a felül lévő **Hozzáadás** gombra. 

        1. A **Beléptetési listabejegyzés hozzáadása** területen adja meg a következő információkat:
            - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
            - A *Tanúsítvány .pem- vagy .cer-fájl* területen válassza ki az előző lépésben létrehozott **_X509individual.pem_** tanúsítványfájlt a *Fájlkezelő* vezérlővel.
            - Ha kívánja, megadhatja az alábbi információkat is:
                - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
                - Adjon meg egy egyedi eszközazonosítót. Ne használjon bizalmas adatokat az eszköz elnevezésekor. 
                - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
            - Ha végzett, kattintson a **Mentés** gombra. 

        ![Írja be az X.509-eszköz beléptetési információit a portál panelén](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       Sikeres regisztráció esetén az X.509-eszköz **microsoftriotcore** azonosítóval megjelenik a *Regisztrációs azonosító* oszlopban, az *Egyéni regisztrációk* lapon. 

    - **Regisztrációs csoportok**: 

        1. Az eszközkiépítési szolgáltatás összefoglaló panelén válassza a **Tanúsítványok** lehetőséget, és kattintson a felül található **Hozzáadás** gombra.

        1. A **Tanúsítvány hozzáadása** területen adja meg a következő információkat:
            - Adjon meg egy egyedi tanúsítványnevet.
            - Válassza ki a korábban létrehozott **_X509group.pem_** fájlt.
            - Ha végzett, kattintson a **Mentés** gombra.

        ![Tanúsítvány hozzáadása](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. Válassza ki az újonnan létrehozott tanúsítványt:
            - Kattintson az **Ellenőrző kód létrehozása** lehetőségre. Másolja ki a kapott kódot.
            - Adja meg az _ellenőrző kódot_, vagy kattintson a jobb gombbal, hogy beillessze az aktuálisan futó _provisioning-x509-cert-generator_ ablakba.  Nyomja le az **Enter** billentyűt.
            - Másolja a vágólapra a `Verification Cert` kimenetét a *-----BEGIN CERTIFICATE-----* sortól az *-----END CERTIFICATE-----* sorig.
            
                ![Ellenőrzés-készítő](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - Hozzon létre egy **_X509validation.pem_** nevű fájlt a Windows rendszerű gépén, nyissa meg egy tetszőleges szövegszerkesztőben, és másolja bele a vágólapra kimásolt szöveget. Mentse a fájlt.
            - Válassza ki az **_X509validation.pem_** fájlt az Azure Portalon. Kattintson az **Ellenőrzés** gombra.

            ![Tanúsítvány ellenőrzése](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. Válassza a **Regisztrációk kezelése** lehetőséget. Válassza a **Regisztrációs csoportok** lapot, és kattintson a felül található **Hozzáadás** gombra.
            - Adjon meg egy egyedi csoportnevet.
            - Válassza ki a korábban létrehozott egyedi tanúsítványnevet.
            - Ha kívánja, megadhatja az alábbi információkat is:
                - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
                - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.

        ![X.509 csoportos regisztrációs információinak megadása a portál panelén](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        Sikeres regisztráció esetén az X.509-eszközcsoport megjelenik a *Csoportnév* oszlopban a *Regisztrációs csoportok* lapon.


## <a name="simulate-the-device"></a>Az eszköz szimulálása

1. Az eszközkiépítési szolgáltatás összefoglaló panelén válassza az **Áttekintés** lehetőséget, majd jegyezze fel az _Azonosító hatóköre_ és a _Kiépítési szolgáltatás globális végpontja_ értékét.

    ![Szolgáltatás adatai](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. Nyisson meg egy parancssort. Lépjen a mintaprojekt mappájára.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Adja meg a regisztrációs információkat a következő módszerek egyikével, a beállításnak megfelelően:

    - **Egyéni regisztráció**: 

        1. Szerkessze az `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` fájlt, hogy az tartalmazza a korábban feljegyzett _Azonosító hatóköre_ és _Kiépítési szolgáltatás globális végpontja_ értékeket. Adja hozzá a korábban feljegyzett _Ügyféltanúsítvány_ és _Ügyféltanúsítvány titkos kulcsa_ értékeket is.

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - A tanúsítvány és a kulcs belefoglalásához használja a következő formátumot:
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **Regisztrációs csoportok**: 

        1. Kövesse a fenti, **egyéni regisztrációra** vonatkozó utasításokat.

        1. Adja hozzá az alábbi kódsorokat a `main` függvény elejéhez.
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(rootPem);
            ```
    
            - A tanúsítványok belefoglalásához használja a következő formátumot:
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. Hozza létre a mintát. Lépjen a célmappára, és futtassa a létrehozott jar-fájlt.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. A portálon lépjen a kiépítési szolgáltatáshoz csatolt IoT hubhoz, és nyissa meg a **Device Explorer** (Eszközkereső) panelt. Ha sikeresen kiépíti a szimulált X.509-eszközt a hubon, az eszköz azonosítója megjelenik a **Device Explorer** panelen, a hozzá tartozó *ÁLLAPOT* pedig **engedélyezett** lesz. Ha már a minta eszközalkalmazás futtatása előtt megnyitotta a panelt, akkor lehet, hogy rá kell kattintania a fenti **Frissítés** gombra. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eszközügyfél minta használatát és megismerését, akkor ne törölje a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Nyissa meg a szolgáltatás **Regisztrációk kezelése** paneljét, majd kattintson az **Egyéni regisztrációk** lapra. Válassza ki a rövid útmutatóban regisztrált eszköz *REGISZTRÁCIÓS AZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra. 
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Nyissa meg a hub **IoT-eszközök** paneljét, válassza ki a rövid útmutatóban regisztrált eszköz *ESZKÖZAZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra.


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy szimulált X.509-eszközt hozott létre a Windows rendszerű gépén, amelyet aztán kiépített az IoT Hubon a portál Azure IoT Hub Device Provisioning Service szolgáltatásával. Ha szeretné megismerni az X.509-eszköz programozott regisztrációjának folyamatát, lépjen tovább az X.509-eszközök programozott regisztrációjának rövid útmutatójára. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X.509-eszközök regisztrációja az Azure IoT Hub Device Provisioning Service-be](quick-enroll-device-x509-java.md)
