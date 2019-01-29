---
title: Azure Service Fabric beépülő modul az Eclipse-hez | Microsoft Docs
description: Bevezetés az Eclipse Service Fabric beépülő moduljának használatába.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: 250931c9b53692dff4006a0114b6da20948b3f59
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096670"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Az Eclipse Service Fabric beépülő moduljának Java alkalmazásfejlesztése
Az Eclipse a Java-fejlesztők által leggyakrabban használt integrált fejlesztőkörnyezetek (IDE-k) közé tartozik. Ebben a cikkben azt ismertetjük, hogyan állíthatja be az Eclipse fejlesztői környezetet az Azure Service Fabrickel való használathoz. Megtudhatja, hogyan telepítheti a Service Fabric beépülő modult, hogyan hozhat létre Service Fabric-alkalmazást, és hogyan helyezhet üzembe Service Fabric-alkalmazásokat helyi vagy távoli Service Fabric-fürtön az Eclipse-ben. 

> [!NOTE]
> Az Eclipse beépülő modul jelenleg nem támogatott a Windows rendszeren. 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>A Service Fabric beépülő modul telepítése vagy frissítése az Eclipse-ben
Telepíthet egy Service Fabric beépülő modult az Eclipse-en. A beépülő modul segíthet leegyszerűsíteni a Java-szolgáltatások létrehozásának és üzembe helyezésének folyamatát.

> [!IMPORTANT]
> A Service Fabric beépülő modulhoz Eclipse Neon vagy újabb verzió szükséges. Az ezt a megjegyzést követő útmutatások segítségével ellenőrizheti az Eclipse verzióját. Ha az Eclipse egy korábbi verziója van telepítve, az [Eclipse webhelyéről](https://www.eclipse.org) tölthet le újabb verziót. Nem ajánlott az új verziót az Eclipse meglévő telepítésére telepíteni (azt felülírni). A meglévő verziót eltávolíthatja a telepítő futtatása előtt, vagy másik könyvtárba telepítheti az újabb verziót. 
> 
> Ubuntu rendszeren ajánlott közvetlenül az Eclipse webhelyéről elvégezni a telepítést csomagtelepítő helyett (`apt` vagy `apt-get`). Így biztosan az Eclipse legfrissebb verzióját fogja beszerezni. 

Telepítse az Eclipse Neon vagy újabb verzióját az [Eclipse webhelyéről](https://www.eclipse.org).  A Buildship 2.2.1-es vagy újabb verzióját is telepítheti (a Service Fabric beépülő modul nem kompatibilis a Buildship régebbi verzióival):
-   A telepített összetevők verziójának ellenőrzéséhez az Eclipse-ben lépjen a **Help** > **About Eclipse** > **Installation Details** (Súgó > Az Eclipse névjegye > Telepítés részletei) területre.
-   A buildship frissítéséért lásd [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update].
-   Az Eclipse frissítéseinek kereséséhez és telepítéséhez lépjen a **Help** > **Check for Updates** (Súgó > Frissítések keresése) területre.

A Service Fabric beépülő modul telepítéséhez az Eclipse-ben lépjen a **Help** > **Install New Software** (Súgó > Új szoftver telepítése) területre.
1. A **Work with** (Használat) mezőbe írja be a **http://dl.microsoft.com/eclipse** címet.
2. Kattintson a **Hozzáadás** parancsra.

   ![Az Eclipse Service Fabric beépülő modulja][sf-eclipse-plugin-install]
3. Válassza ki a Service Fabric beépülő modult, majd kattintson a **Next** (Tovább) gombra.
4. Végezze el a telepítés lépéseit, majd fogadja el a Microsoft szoftverlicenc-szerződését.
  
Ha a Service Fabric beépülő modul már telepítve van, telepítse a legújabb verziót. 
1. Az elérhető frissítések kereséséhez lépjen a **Help** > **About Eclipse** > **Installation Details** (Súgó > Az Eclipse névjegye > Telepítés részletei) területre. 
2. A telepített beépülő modulok listájában válassza ki a Service Fabric elemet, majd kattintson az **Update** (Frissítés) parancsra. A rendszer telepíti az elérhető frissítéseket.
3. A Service Fabric beépülő modul frissítése után frissítse a Gradle-projektet is.  Kattintson a jobb gombbal **build.gradle** elemre, majd válassza a **Refresh** (Frissítés) lehetőséget.

> [!NOTE]
> Ha a Service Fabric beépülő modul telepítése vagy frissítése túl lassú, azt az Eclipse valamelyik beállítása okozhatja. Az Eclipse metaadatokat gyűjt az Eclipse-példányhoz regisztrált frissítési helyek összes módosításáról. Ahhoz, hogy fel tudja gyorsítani a Service Fabric beépülő modul frissítéseinek keresési és telepítési folyamatát, lépjen az **Available Software Sites** (Elérhető szoftverhelyek) területre. Törölje az összes hely jelölőnégyzetét a Service Fabric beépülő modul helyére (http://dl.microsoft.com/eclipse/azure/servicefabric)) mutató jelölőnégyzet kivételével.

> [!NOTE]
>Ha az Eclipse nem a várt módon működik a Mac gépén (vagy a futtatáshoz felügyelőként történő bejelentkezést ír elő), keresse meg az **ECLIPSE_INSTALLATION_PATH** mappa **Eclipse.app/Contents/MacOS** almappáját. Indítsa el az Eclipse-et az `./eclipse` futtatásával.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Service Fabric-alkalmazás létrehozása az Eclipse-ben

1.  Az Eclipse-ben lépjen a **File** > **New** > **Other** (Fájl > Új > Egyéb) lehetőségre. Válassza a **Service Fabric Project** (Service Fabric-projekt) lehetőséget, majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Új projekt, 1. oldal][create-application/p1]

2.  Adja meg a projekt nevét, majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Új projekt, 2. oldal][create-application/p2]

3.  A sablonok listájában válassza a **Service Template** (Szolgáltatássablon) elemet. Válassza ki a szolgáltatássablon típusát (Actor, Stateless, Container, Guest Binary – aktor, állapot nélküli, tároló, vendég bináris), majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Új projekt, 3. oldal][create-application/p3]

4.  Adja meg a szolgáltatás nevét és a szolgáltatás részleteit, majd kattintson a **Finish** (Befejezés) gombra.

    ![Service Fabric – Új projekt, 4. oldal][create-application/p4]

5. Az első Service Fabric-projekt létrehozásakor az **Open Associated Perspective** (Társított perspektíva megnyitása) párbeszédpanelen kattintson a **Yes** (Igen) gombra.

    ![Service Fabric – Új projekt, 5. oldal][create-application/p5]

6.  Így néz ki az új projekt:

    ![Service Fabric – Új projekt, 6. oldal][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Az Eclipse Service Fabric-alkalmazás létrehozása

1.  Kattintson a jobb gombbal az új Service Fabric-alkalmazásra, majd válassza a **Service Fabric** lehetőséget.

    ![Service Fabric – helyi menü][publish/RightClick]

2. A helyi menüben válassza ki az alábbi lehetőségek közül:
    -   Az alkalmazás tisztítás nélküli kiépítéséhez kattintson a **Build Application** (Alkalmazás buildelése) parancsra.
    -   Az alkalmazás tiszta buildjének kiépítéséhez kattintson a **Rebuild Application** (Alkalmazás újrabuildelése) parancsra.
    -   A kiépített összetevők törléséhez az alkalmazásból kattintson a **Clean Application** (Alkalmazás tisztítása) parancsra.
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>A Service Fabric-alkalmazás üzembe helyezése a helyi fürtön az eclipse-szel

A Service Fabric-alkalmazás létrehozása után a következő lépésekkel telepítheti a helyi fürthöz.

1. Ha a helyi fürt még nem indult el, kövesse a [helyi fürt beállítása](./service-fabric-get-started-linux.md#set-up-a-local-cluster) elindíthatja a helyi fürtöt, és ellenőrizze, hogy fut-e.
2. Kattintson a jobb gombbal a Service Fabric-alkalmazásokat, és válassza **Service Fabric**.

    ![Service Fabric – helyi menü][publish/RightClick]

3.  Kattintson a helyi menüből **alkalmazás üzembe helyezése**.
4.  A konzolablakban az üzembe helyezés műveletnek az előrehaladását is követheti.
5.  Győződjön meg arról, hogy az alkalmazás fut, nyissa meg a Service Fabric Explorer egy böngészőablakban a helyi fürtön lévő [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer). Bontsa ki a **alkalmazások** csomópont, és ellenőrizze, hogy az alkalmazás fut-e. 

Az eclipse-ben a helyi fürt használata az alkalmazás hibakeresése kapcsolatban lásd: [Java szolgáltatás hibakeresése az Eclipse](./service-fabric-debugging-your-application-java.md).

Az alkalmazás a helyi fürtre is telepíthet a **alkalmazás közzététele** parancsot:

1. Kattintson a jobb gombbal a Service Fabric-alkalmazásokat, és válassza **Service Fabric**.
2. Kattintson a helyi menüből **alkalmazás közzététele...** .
3. Az a **alkalmazás közzététele** ablakban válassza a **PublishProfiles/Local.json** célprofilt, kattintson **közzététel**.

    ![Publish (Közzététel) helyi párbeszédpanel](./media/service-fabric-get-started-eclipse/localjson.png)

    Alapértelmezés szerint a közzétételi profil Local.json közzététele a helyi fürthöz van beállítva. Közzétételi profilok található kapcsolati és -végpont paraméterekkel kapcsolatos további információkért tekintse meg a következő szakaszt.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Az Azure-bA az eclipse-szel a Service Fabric-alkalmazás közzététele

Az alkalmazás a felhőbe való közzétételéhez, kövesse az alábbi lépéseket:

1. Egy biztonságos fürthöz való az alkalmazás közzététele a felhőben, szüksége van egy X.509 tanúsítvány használatával kommunikálni a fürttel. Tesztelési és fejlesztési környezetekben a használt tanúsítvány legtöbbször a fürttanúsítvány. Éles környezetben a tanúsítvány egy ügyféltanúsítvány, amely nem azonos azzal a fürttanúsítvány kell lennie. A tanúsítvány és a titkos kulcsot is van szüksége. A tanúsítvány (és a kulcs) fájlnak kell lennie a PEM-formátumú. A tanúsítványt és a egy PFX-fájlból, a következő paranccsal openssl titkos kulcsot tartalmazó PEM-fájl hozhat létre:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Ha nem jelszóval védett PFX-fájl, `--passin pass:` utolsó paraméterhez.

2. Nyissa meg a **Cloud.json** fájlt a **PublishProfiles** könyvtár. A fürt végpontját és biztonsági hitelesítő adatok konfigurálása megfelelő a fürt számára kell.

   - A `ConnectionIPOrURL` mező tartalmazza az IP-cím vagy a fürt URL-címét. Vegye figyelembe, hogy az érték nem tartalmazza az URL-séma (`https://`).
   - Alapértelmezés szerint a `ConnectionPort` mező lehet `19080`, kivéve, ha explicit módon módosította a port a fürt számára.
   - A `ClientKey` mezőt kell mutatnia a PEM-formátumú .pem- és .key fájlokat a helyi gépen, amely tartalmazza az ügyfél vagy a fürt tanúsítvány titkos kulcsát.
   - A `ClientCert` mező egy PEM-formátumú .pem- vagy .crt fájl a helyi gépen, amely tartalmazza a tanúsítványadatokat az ügyfél vagy a fürt kell mutatnia. a tanúsítvány. 

    ```bash
    {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
    }
    ```

2. Kattintson a jobb gombbal a Service Fabric-alkalmazásokat, és válassza **Service Fabric**.
3. Kattintson a helyi menüből **alkalmazás közzététele...** .
3. Az a **alkalmazás közzététele** ablakban válassza a **PublishProfiles/Cloud.json** célprofilt, kattintson **közzététel**.

    ![A felhő közzétételi párbeszédablaka](./media/service-fabric-get-started-eclipse/cloudjson.png)

4.  A konzolablakban a közzétételi művelet előrehaladását is követheti.
5.  Győződjön meg arról, hogy az alkalmazás fut, nyissa meg a Service Fabric Explorer egy böngészőablakban az Azure-fürtön. A fenti példában ez lenne: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`. Bontsa ki a **alkalmazások** csomópont, és ellenőrizze, hogy az alkalmazás fut-e. 


Biztonságos Linux-fürtökön Ha az alkalmazás tartalmaz a Reliable Services-szolgáltatások, is szüksége lesz egy tanúsítványt, amely a szolgáltatások a Service Fabric-futtatókörnyezet API-k hívásához konfigurálása. További tudnivalókért lásd: [egy Reliable Services-alkalmazás Linux-fürtök konfigurálása](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

A gyors útmutató bemutatja, hogyan Java nyelven írt biztonságos Linux-fürt Service Fabric Reliable Services-alkalmazás üzembe helyezése, lásd: [a rövid útmutató: Java Reliable Services-alkalmazás üzembe helyezése](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Alkalmazás üzembe helyezése Service Fabric Eclipse futtatási konfigurációkat használatával

A Service Fabric-alkalmazások üzembe helyezésének másik módszere, ha Eclipse futtatási konfigurációkat használ.

1. Az eclipse-ben nyissa meg **futtatása** > **futtatási konfigurációkkal**.
2. A **Gradle Project** (Gradle-projekt) területen válassza a **ServiceFabricDeployer** futtatási konfigurációt.
3. A jobb oldali ablaktáblán a a **argumentumok** lapot, ellenőrizze, hogy a **ip**, **port**, **clientCert**, és **clientKey**paraméterek megfelelően vannak-e beállítva az üzembe helyezéshez. Alapértelmezés szerint a paraméterek megadását üzembe helyezése a helyi fürthöz, az alábbi képernyőképen látható módon. Az alkalmazás közzétételéhez az Azure-bA módosíthatja úgy a paramétereket, a végpontrészleteket és biztonsági hitelesítő adatok az Azure-fürthöz. További információkért lásd az előző szakaszban [közzététele a Service Fabric-alkalmazás az Azure-bA az eclipse-szel](#publish-your-service-fabric-application-to-azure-with-eclipse).

    ![Futtassa a konfiguráció helyi párbeszédpanel](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Győződjön meg arról, hogy **Working Directory** telepíteni kívánt alkalmazásra mutat. Az alkalmazás módosításához kattintson a **Workspace** (Munkaterület) gombra, és válassza ki a kívánt alkalmazást.
6. Kattintson az **Apply** (Alkalmaz), majd a **Run** (Futtatás) gombra.

Néhány másodpercen belül megtörténik az alkalmazás felépítése és üzembe helyezése. Az üzembe helyezés állapotát a Service Fabric Explorerben követheti nyomon.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Service Fabric-szolgáltatás hozzáadása a Service Fabric-alkalmazáshoz

Ha Service Fabric-szolgáltatást szeretne hozzáadni egy meglévő Service Fabric-alkalmazáshoz, végezze el a következő lépéseket:

1.  Kattintson a jobb gombbal a projektre, amelyhez hozzá szeretne adni egy szolgáltatást, majd kattintson a **Service Fabric** elemre.

    ![Service Fabric – Szolgáltatás hozzáadása, 1. oldal][add-service/p1]

2.  Kattintson az **Add Service Fabric Service** (Service Fabric-szolgáltatás hozzáadása) parancsra, és végezze el a szolgáltatás hozzáadásának lépéseit.
3.  Válassza ki a projekthez hozzáadni kívánt szolgáltatássablont, majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Szolgáltatás hozzáadása, 2. oldal][add-service/p2]

4.  Adja meg a szolgáltatás nevét (és a többi szükséges adatot), majd kattintson az **Add Service** (Szolgáltatás hozzáadása) gombra.  

    ![Service Fabric – Szolgáltatás hozzáadása, 3. oldal][add-service/p3]

5.  A szolgáltatás hozzáadása után a teljes projektstruktúra a következőhöz hasonlóan néz ki:

    ![Service Fabric – Szolgáltatás hozzáadása, 4. oldal][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>A Service Fabric Java-alkalmazás jegyzékverzióinak szerkesztése

Jegyzékverziók szerkesztéséhez kattintson jobb gombbal a projektre, majd a legördülő menüből válassza a **Service Fabric** alatt az **Edit Manifest Versions...** (Jegyzékverziók szerkesztése) pontot. A varázslóval frissítheti az alkalmazásjegyzék, szolgáltatásjegyzék, valamint a **Code** (Kód-), **Config** (Konfigurációs-) és **Data** (Adat-) csomagok verzióit.

Ha bejelöli az **Automatically update application and service versions** (Alkalmazás- és szolgáltatás-verziók automatikus frissítése) lehetőséget, akkor a verziófrissítéskor a jegyzékverziók is automatikusan frissülnek. Ha például előre bejelöli a jelölőnégyzetet, majd 0.0.0-ról 0.0.1-re frissíti a **Code** verzióját és a **Finish** gombra kattint, akkor a szolgáltatásjegyzék és alkalmazásjegyzék verziója is automatikusan 0.0.1-re frissül.

## <a name="upgrade-your-service-fabric-java-application"></a>A Service Fabric Java-alkalmazásának frissítése

Frissítési forgatókönyv esetén tegyük fel, hogy az **App1** projektet hozta létre a Service Fabric beépülő modullal az Eclipse-ben. A projektet a beépülő modullal helyezte üzembe a **fabric:/App1Application** nevű alkalmazás létrehozásához. Az alkalmazástípus **App1ApplicationType**, a verziója pedig 1.0. A rendelkezésre állás megszakítása nélkül szeretné frissíteni az alkalmazást.

Először végezzen módosítást az alkalmazáson, majd építse újra a módosított szolgáltatást. Frissítse a módosított szolgáltatás jegyzékfájlját (ServiceManifest.xml) a szolgáltatás frissített verzióival (és a megfelelő Code, Config vagy Data értékkel). Módosítsa az alkalmazás jegyzékfájlját is (ApplicationManifest.xml) az alkalmazás frissített verziószámával és a módosított szolgáltatással.  

Ha az Eclipse-szel szeretné frissíteni az alkalmazást, létrehozhat egy duplikált futtatáskonfigurációs profilt, amelyet aztán szükség szerint az alkalmazás frissítésére használhat.

1.  Lépjen a **Run** > **Run Configurations** (Futtatás, Konfigurációk futtatása) területre. A bal oldali ablaktáblában kattintson a **Gradle Project** (Gradle-projekt) bal oldalán található kis nyílra.
2.  Kattintson a jobb gombbal a **ServiceFabricDeployer** elemre, majd válassza a **Duplicate** (Megkettőzés) parancsot. Adjon egy új nevet a konfigurációnak, például **ServiceFabricUpgrader**.
3.  A jobb oldali ablaktáblán, az **Arguments** (Argumentumok) lapon módosítsa a **-Pconfig='deploy'** értéket **-Pconfig='upgrade'** értékre, majd kattintson az **Apply** (Alkalmaz) gombra.

Ez a folyamat olyan futtatási konfigurációs profilt hoz létre és ment el, amellyel bármikor frissítheti az alkalmazást. Az alkalmazás jegyzékfájljából a legújabb frissített alkalmazástípus-verziót is lekéri.

Az alkalmazás frissítése eltarthat néhány percig. Az alkalmazás frissítésének folyamatát a Service Fabric Explorerben követheti nyomon.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>A Mavennel használni kívánt régi Service Fabric Java-alkalmazások migrálása
Nemrégiben áthelyeztük a Service Fabric Java-kódtárakat a Service Fabric Java SDK-ból a Mavenen futó adattárba. Az Eclipse-szel létrehozott új alkalmazások a legfrissebb projekteket hozzák létre (amelyek képesek együttműködni a Mavennel), a meglévő állapotmentes vagy aktor Service Fabric Java-alkalmazások pedig, amelyek korábban a Service Fabric Java SDK-t használták, frissíthetők a Mavenben található Service Fabric Java-függőségek használatára. Kövesse az [itt](service-fabric-migrate-old-javaapp-to-use-maven.md) felsorolt lépéseket, ha biztosítani kívánja, hogy a régebbi alkalmazásaik együttműködjenek a Mavennel.

## <a name="next-steps"></a>További lépések

- Gyors lépések az alkalmazásfejlesztés Java Reliable szolgáltatásalkalmazás és üzembe helyezése, helyileg és az Azure-ba, lásd: [a rövid útmutató: Java Reliable Services-alkalmazás üzembe helyezése](./service-fabric-quickstart-java-reliable-services.md).
- A helyi fürtön lévő Java-alkalmazás hibakeresése kapcsolatban lásd: [Java szolgáltatás hibakeresése az Eclipse](./service-fabric-debugging-your-application-java.md).
- Megtudhatja, hogyan figyelheti és diagnosztizálhatja a Service Fabric-alkalmazásokat, lásd: [figyelése és diagnosztizálása a szolgáltatások a helyi gép fejlesztési beállítása](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
