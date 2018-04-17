---
title: Azure Service Fabric-tárolóalkalmazás létrehozása Linuxon | Microsoft Docs
description: Ebben a rövid útmutatóban létrehozza az első saját, Linux-alapú tárolóalkalmazását az Azure Service Fabricben.  Az alkalmazással elkészíthet egy Docker-rendszerképet, amelyet leküldéssel továbbíthat egy tárolóregisztrációs adatbázisba, majd összeállíthat és üzembe helyezhet egy Service Fabric-tárolóalkalmazást.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 1b65f54547821654f35571c55f61074a0106a292
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Rövid útmutató: Linux-alapú Azure Service Fabric-tárolóalkalmazás üzembe helyezése az Azure-on
Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál. 

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe a Linux-tárolókat Service Fabric-fürtön. Miután elkészült, egy szavazóalkalmazást kap, amely egy Python-alapú webes előtérrendszert és egy Redis-alapú háttérrendszert tartalmaz, amely egy Service Fabric-fürtben fut. Azt is megismeri, hogyan végezheti el egy alkalmazás feladatátvételét és hogyan skálázhatja a fürtben lévő alkalmazásokat.

![Szavazóalkalmazás weboldala][quickstartpic]

Ebben a rövid útmutatóban az Azure Cloud Shellben lévő Bash környezet használatával futtat Service Fabric parancssori felületi parancsokat. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha most először futtatja a Cloud Shellt, a rendszer megkéri a `clouddrive`-fájlmegosztás beállítására. Elfogadhatja az alapértelmezett beállításokat, de csatolhat meglévő fájlmegosztást is. További tudnivalókért lásd: [`clouddrive`-fájlmegosztás beállítása](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share).

## <a name="get-the-application-package"></a>Az alkalmazáscsomag beszerzése
A tárolók a Service Fabric szolgáltatásban való üzembe helyezéséhez jegyzékfájlok egy készletére lesz szükség (az alkalmazásdefinícióra), amelyek leírják az egyes tárolókat és az alkalmazást.

A Cloud Shellben a git használatával klónozással készítsen egy másolatot az alkalmazásdefinícióról, majd módosítsa a könyvtárakat a `Voting` könyvtárra a klónban. 

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric-fürt létrehozása
Ha az alkalmazást üzembe szeretné helyezni az Azure-ban, szüksége lesz egy Service Fabric-fürtre a futtatásához. A nyilvános fürtök segítségével gyorsan és egyszerűen hozhatók létre Service Fabric-fürtök. A nyilvános fürtök ingyenes, korlátozott időtartamú, Azure-ban üzemeltetett Service Fabric-fürtök, amelyek futtatását a Service Fabric csapata végzi. A nyilvános fürtökkel alkalmazásokat helyezhet üzembe, és megismerkedhet a platform használatával. A fürt egy önaláírt tanúsítványt használ a csomópontok közötti, valamint az ügyfél és a csomópont közötti biztonsághoz.

Jelentkezzen be, és csatlakozzon egy [Linux-fürthöz](http://aka.ms/tryservicefabric). A **PFX** hivatkozásra kattintva töltse le a PFX-tanúsítványt a számítógépre. Kattintson a **ReadMe** hivatkozásra a tanúsítvány jelszavának és a különböző környezetek a tanúsítvány használatára konfigurálása lépéseinek megismeréséhez. Hagyja megnyitva a **kezdőlapot** és a **ReadMe** oldalt, az itt található utasításokat a következő lépések során fogja használni. 

> [!Note]
> Óránként korlátozott számú nyilvános fürt érhető el. Ha a nyilvános fürtre való regisztráláskor hiba lép fel, várjon egy kis ideig, majd próbálkozzon újra, vagy kövesse a [Service Fabric-fürt az Azure-ban való létrehozását ismertető](service-fabric-tutorial-create-vnet-and-linux-cluster.md) oktatóanyagban szereplő lépéseket, amelyekkel létrehozhat egy fürtöt az előfizetésben. 
> 
>Ha saját fürtöt hoz létre, vegye figyelembe, hogy a webes előtérrendszer a konfigurációja szerint a 80-as porton figyeli a bejövő forgalmat. Győződjön meg róla, hogy a port nyitva van a fürtön. (Ha nyilvános fürtöt használ, a port nyitva van.)
>

## <a name="configure-your-environment"></a>A környezet konfigurálása
A Service Fabric számos eszközt nyújt, amelyekkel kezelheti a fürtöket és azok alkalmazásait:

- A Service Fabric Explorert, amely egy böngészőalapú eszköz.
- A Service Fabric parancssori felületet (CLI-t), amely az Azure CLI 2.0-n fut.
- PowerShell-parancsokat. 

Ebben a rövid útmutatóban a Cloud Shellben lévő Service Fabric parancssori felületet és a Service Fabric Explorert használja. A következő szakaszok bemutatják, hogyan telepítheti a biztonságos fürthöz való csatlakozáshoz szükséges tanúsítványt ezekkel az eszközökkel.

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Tanúsítvány konfigurálása a Service Fabric parancssori felülethez
Ha a parancssori felületet a Cloud Shellben szeretné használni, fel kell töltenie a tanúsítvány PFX-fájlját a Cloud Shellbe, majd ezzel kell létrehoznia egy PEM-fájlt.

1. Ha az aktuális munkakönyvtárba szeretné feltölteni a tanúsítványt a Cloud Shellben, húzza a tanúsítvány PFX-fájlját a számítógépen lévő letöltési mappából a Cloud Shell ablakába.  

2. A PFX-fájlok PEM-fájlokká történő konvertálásához használja a következő parancsot. (Nyilvános fürtök esetén a PFX-fájlra és a jelszóra vonatkozó parancsot másolhat a **ReadMe** oldal utasításai közül.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

### <a name="configure-certificate-for-service-fabric-explorer"></a>Tanúsítvány konfigurálása a Service Fabric Explorerhez
A Service Fabric Explorer használatához importálnia kell a tanúsítvány PFX-fájlját, amelyet a nyilvános fürt webhelyéről a tanúsítványtárolóba (Windows vagy Mac) vagy magába a böngészőbe (Ubuntu) töltött le. Szüksége van a PFX titkos kulcs jelszavára, amelyet a **ReadMe** oldalról szerezhet be.

Tetszőleges módszerrel importálhatja a tanúsítványt a rendszerre. Például:

- Windows rendszeren: Kattintson duplán a PFX-fájlra, és kövesse a lépéseket a tanúsítvány a `Certificates - Current User\Personal\Certificates` személyes tárolóban való telepítéséhez. Másik lehetőségként használhatja a **ReadMe** utasításokban lévő PowerShell-parancsot is.
- Mac rendszeren: Kattintson duplán a PFX-fájlra, és kövesse a lépéseket a tanúsítvány a kulcskarikában való telepítéséhez.
- Ubuntu rendszeren: A Mozilla Firefox az alapértelmezett böngésző az Ubuntu 16.04-en. A tanúsítvány a Firefoxba importálásához kattintson a böngésző jobb felső sarkában lévő menügombra, majd a **Beállítások** gombra. A **Beállítások** oldalon a keresőmezővel keressen rá a „tanúsítványok” kifejezésre. Kattintson a **Tanúsítványkezelő** gombra, válassza a **Saját tanúsítványok** fület, kattintson az **Importálás** lehetőségre, és kövesse az utasításokat a tanúsítvány importálásához.
 
   ![Tanúsítvány telepítése Firefoxon](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png) 

## <a name="deploy-the-service-fabric-application"></a>A Service Fabric-alkalmazás üzembe helyezése 
1. A Cloud Shellben csatlakozzon az Azure Service Fabric-fürthöz az Azure CLI használatával. A végpont a fürt felügyeleti végpontja. A PEM-fájlt az előző szakaszban hozta létre. (Nyilvános fürtök esetén a PEM-fájlra és a felügyeleti végpontra vonatkozó parancsot másolhat a **ReadMe** oldal utasításai közül.)

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. Használja a telepítési szkriptet a szavazóalkalmazás definíciójának a fürtbe való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

    ```bash
    ./install.sh
    ```

2. Nyisson meg egy webböngészőt, majd navigáljon a fürt Service Fabric Explorer-végpontjára. A végpont formátuma a következő:  https://\<my-azure-service-fabric-cluster-url>:19080/Explorer; például `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. </br>(Nyilvános fürtök esetén a fürt Service Fabric Explorer-végpontját a **Kezdőlapon** találja.) 

3. Bontsa ki az **Alkalmazások** csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés a szavazóalkalmazás típusához, és az Ön által létrehozott példányhoz.

    ![Service Fabric Explorer][sfx]

3. A futó tárolóhoz való csatlakozáshoz nyisson meg egy webböngészőt, és navigáljon a fürt URL-címére – például: `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Ekkor a szavazóalkalmazásnak kell megjelennie a böngészőben.

    ![Szavazóalkalmazás weboldala][quickstartpic]


> [!NOTE]
> A Docker Compose használatával is üzembe helyezhet Service Fabric-alkalmazásokat. A következő paranccsal például üzembe helyezheti és telepítheti az alkalmazást a fürtön a Docker Compose használatával.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Tároló feladatátvétele a fürtben
A Service Fabric biztosítja, hogy meghibásodás esetén a tárolópéldányok automatikusan áthelyeződjenek a fürt más csomópontjaira. Manuálisan is ürítheti a csomópontokat a tárolók tekintetében, és azokat zökkenőmentesen helyezheti át a fürt más csomópontjaira. A Service Fabric több módot nyújt a szolgáltatások méretezésére. A következő lépésekben a Service Fabric Explorert használjuk.

Az előtértároló feladatátvételéhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Kattintson a **fabric:/Voting/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli). Figyelje meg a csomópont nevét a fanézetben, amely azokat a csomópontokat mutatja, amelyeken a tároló jelenleg fut; például: `_nodetype_4`.
3. Bontsa ki a **Csomópontok** csomópontot a fanézetben. Kattintson a három pontra (...) a tárolót futtató csomópont mellett.
4. Válassza az **Újraindítás** lehetőséget a csomópont újraindításához, majd erősítse meg az újraindítási műveletet. Az újraindítás kikényszeríti a tároló feladatátvételét a fürt egy másik csomópontjára.

    ![Csomópont nézet a Service Fabric Explorerben][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben
A Service Fabric-szolgáltatások könnyen méretezhetők egy adott fürtben a szolgáltatások terhelésének megfelelően. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik.

A webes előtér-szolgáltatás méretezéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Kattintson a három pontra a fanézetben a **fabric:/Voting/azurevotefront** csomópont mellett, és válassza a **Szolgáltatás méretezése** lehetőséget.

    ![A Service Fabric Explorer méretezési szolgáltatásának elindítása][containersquickstartscale]

  Most már méretezheti a webes előtér-szolgáltatás példányainak számát.

3. Módosítsa a számot **2**-re, és kattintson a **Szolgáltatás méretezése** gombra.
4. Kattintson a **fabric:/Voting/azurevotefront** csomópontra a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli).

    ![A Service Fabric Explorer méretezési szolgáltatása befejezve][containersquickstartscaledone]

    Most láthatja, hogy a szolgáltatás két példánnyal rendelkezik. A fanézetben megtekintheti, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megduplázta az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos tudni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány elinduljon a fürtben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
1. Használja a sablonban megadott eltávolítási szkriptet (uninstall.sh) az alkalmazáspéldánynak a fürtről történő törléséhez, és törölje az alkalmazástípus regisztrációját. A szkriptnek némi időre van szüksége a példány kiürítéséhez, ezért a telepítési szkriptet nem érdemes azonnal ezen szkript után futtatni. A Service Fabric Explorerrel megállapíthatja, hogy mikor lett eltávolítva a példány és mikor lett megszüntetve az alkalmazástípus regisztrációja. 

    ```bash
    ./uninstall.sh
    ```

2. Ha végzett a fürt használatával, eltávolíthatja a tanúsítványt a tanúsítványtárolóból. Például:
   - Windows rendszeren: Használja a [Tanúsítványok MMC beépülő modult](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Ügyeljen arra, hogy a **Saját felhasználói fiókot** válassza a beépülő modul hozzáadásakor. Lépjen a `Certificates - Current User\Personal\Certificates` helyre, és távolítsa el a tanúsítványt.
   - Mac rendszeren: Használja a Keychain alkalmazást.
   - Ubuntu rendszeren: Kövesse a tanúsítványok megtekintéséhez használt lépéseket, majd távolítsa el a tanúsítványt.

3. Ha nem szeretné folytatni a Cloud Shell használatát, törölheti az azzal társított tárfiókot a díjak elkerülése érdekében. Zárja be a Cloud Shell-munkamenetet. Az Azure Portalon kattintson a Cloud Shell-lel társított tárfiókra, kattintson az oldal tetején található **Törlés** gombra, és válaszoljon a kérdésekre.

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban üzembe helyezett egy Linux-alapú tárolóalkalmazást egy Service Fabric-fürtön az Azure-ban, elvégezte az alkalmazás feladatátvételét és méretezte azt a fürtben. Ha bővebb információra van szüksége a Linux-tárolók Service Fabricben való használatával kapcsolatban, lépjen tovább a Linux-tárolóalkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Linux rendszerű tárolóalkalmazás létrehozása](./service-fabric-tutorial-create-container-images.md)


[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
