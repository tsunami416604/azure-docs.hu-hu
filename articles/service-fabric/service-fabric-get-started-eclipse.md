---
title: Az Azure Service Fabric beépülő modulja az Eclipse-hez
description: Ismerje meg az Azure Service Fabric Java-beli használatának megkezdését a napfogyatkozás és a Service Fabric által biztosított beépülő modul használatával.
author: rapatchi
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: b779873488f1fff754d4105249b28f545738c11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258420"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Az Eclipse Service Fabric beépülő moduljának Java alkalmazásfejlesztése
Az Eclipse a Java-fejlesztők által leggyakrabban használt integrált fejlesztőkörnyezetek (IDE-k) közé tartozik. Ebben a cikkben azt ismertetjük, hogyan állíthatja be az Eclipse fejlesztői környezetet az Azure Service Fabrickel való használathoz. Megtudhatja, hogyan telepítheti a Service Fabric beépülő modult, hogyan hozhat létre Service Fabric-alkalmazást, és hogyan helyezhet üzembe Service Fabric-alkalmazásokat helyi vagy távoli Service Fabric-fürtön az Eclipse-ben. 

> [!NOTE]
> Az Eclipse beépülő modul jelenleg nem támogatott a Windows rendszeren. 

> [!IMPORTANT]
> Győződjön meg róla, JDK 8 telepítve van a rendszerre, és kiválasztva Eclipse.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>A Service Fabric beépülő modul telepítése vagy frissítése az Eclipse-ben
Telepíthet egy Service Fabric beépülő modult az Eclipse-en. A beépülő modul segíthet leegyszerűsíteni a Java-szolgáltatások létrehozásának és üzembe helyezésének folyamatát.

> [!IMPORTANT]
> A Service Fabric beépülő modulhoz Eclipse Neon vagy újabb verzió szükséges. Az ezt a megjegyzést követő útmutatások segítségével ellenőrizheti az Eclipse verzióját. Ha az Eclipse egy korábbi verziója van telepítve, az [Eclipse webhelyéről](https://www.eclipse.org) tölthet le újabb verziót. Nem ajánlott az új verziót az Eclipse meglévő telepítésére telepíteni (azt felülírni). A meglévő verziót eltávolíthatja a telepítő futtatása előtt, vagy másik könyvtárba telepítheti az újabb verziót. 
> 
> Ubuntu rendszeren ajánlott közvetlenül az Eclipse webhelyéről elvégezni a telepítést csomagtelepítő helyett (`apt` vagy `apt-get`). Így biztosan az Eclipse legfrissebb verzióját fogja beszerezni. 

Telepítse az Eclipse Neon vagy újabb verzióját az [Eclipse webhelyéről](https://www.eclipse.org).  A Buildship 2.2.1-es vagy újabb verzióját is telepítheti (a Service Fabric beépülő modul nem kompatibilis a Buildship régebbi verzióival):
-   A telepített összetevők verziójának ellenőrzéséhez az Eclipse alkalmazásban nyissa meg a Súgó**az Eclipse** > telepítési részleteiről című**témakört.** **Help** > 
-   A Buildship frissítéséért lásd: [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Eclipse beépülő modulok a Gradle-hez).
-   Az Eclipse frissítéseinek kereséséhez és telepítéséhez keresse fel a Súgó**frissítések keresése című témakört.** **Help** > 

Telepítse a Service Fabric beépülő modult az Eclipse alkalmazásban, és látogasson el az**Új szoftver telepítése** **súgóba.** > 
1. A **Munka ezzel** mezőbe írja\/be a https: /dl.microsoft.com/eclipse értéket.
2. Kattintson a **Hozzáadás** gombra.

   ![Az Eclipse Service Fabric beépülő modulja][sf-eclipse-plugin-install]
3. Válassza ki a Service Fabric beépülő modult, majd kattintson a **Next** (Tovább) gombra.
4. Végezze el a telepítés lépéseit, majd fogadja el a Microsoft szoftverlicenc-szerződését.
  
Ha a Service Fabric beépülő modul már telepítve van, telepítse a legújabb verziót. 
1. Az elérhető frissítések kereséséhez keresse fel a Súgó**az Eclipse** > **telepítési részletei című témakört.** **Help** >  
2. A telepített beépülő modulok listájában válassza ki a Service Fabric elemet, majd kattintson az **Update** (Frissítés) parancsra. A rendszer telepíti az elérhető frissítéseket.
3. A Service Fabric beépülő modul frissítése után frissítse a Gradle-projektet is.  Kattintson a jobb gombbal **build.gradle** elemre, majd válassza a **Refresh** (Frissítés) lehetőséget.

> [!NOTE]
> Ha a Service Fabric beépülő modul telepítése vagy frissítése túl lassú, azt az Eclipse valamelyik beállítása okozhatja. Az Eclipse metaadatokat gyűjt az Eclipse-példányhoz regisztrált frissítési helyek összes módosításáról. Ahhoz, hogy fel tudja gyorsítani a Service Fabric beépülő modul frissítéseinek keresési és telepítési folyamatát, lépjen az **Available Software Sites** (Elérhető szoftverhelyek) területre. Törölje a jelet az összes webhely jelölőnégyzetéből, kivéve azt, amely\/a Service Fabric beépülő modul helyére mutat (https: /dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Ha az Eclipse nem a várt módon működik a Mac gépén (vagy a futtatáshoz felügyelőként történő bejelentkezést ír elő), keresse meg az **ECLIPSE_INSTALLATION_PATH** mappa **Eclipse.app/Contents/MacOS** almappáját. Indítsa el az Eclipse-et az `./eclipse` futtatásával.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Service Fabric-alkalmazás létrehozása az Eclipse-ben

1.  Az Eclipse alkalmazásban nyissa meg **az** > **Új egyéb** > fájl**fájlját.** Válassza a **Service Fabric Project** (Service Fabric-projekt) lehetőséget, majd kattintson a **Next** (Tovább) gombra.

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

## <a name="build-a-service-fabric-application-in-eclipse"></a>Service Fabric-alkalmazás létrehozása az Eclipse alkalmazásban

1.  Kattintson a jobb gombbal az új Service Fabric-alkalmazásra, majd válassza a **Service Fabric** lehetőséget.

    ![Service Fabric – helyi menü][publish/RightClick]

2. A helyi menüben válasszon az alábbi lehetőségek közül:
    -   Az alkalmazás tisztítás nélküli kiépítéséhez kattintson a **Build Application** (Alkalmazás buildelése) parancsra.
    -   Az alkalmazás tiszta buildjének kiépítéséhez kattintson a **Rebuild Application** (Alkalmazás újrabuildelése) parancsra.
    -   A kiépített összetevők törléséhez az alkalmazásból kattintson a **Clean Application** (Alkalmazás tisztítása) parancsra.
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Service Fabric-alkalmazás telepítése a helyi fürtre az Eclipse segítségével

Miután elkészítette a Service Fabric-alkalmazást, kövesse az alábbi lépéseket a helyi fürtre való üzembe helyezéséhez.

1. Ha még nem indította el a helyi fürtöt, kövesse a [Helyi fürt beállítása](./service-fabric-get-started-linux.md#set-up-a-local-cluster) a helyi fürt indításához és annak működéséhez című útmutató utasításait.
2. Kattintson a jobb gombbal a Service Fabric-alkalmazásra, és válassza a **Service Fabric parancsot.**

    ![Service Fabric – helyi menü][publish/RightClick]

3.  A helyi menüben kattintson az **Alkalmazás telepítése parancsra.**
4.  A telepítés folyamatát a Konzol ablakban követheti.
5.  Annak ellenőrzéséhez, hogy az alkalmazás fut-e, nyissa meg [http://localhost:19080/Explorer](http://localhost:19080/Explorer)a Service Fabric Explorer böngészőablakban a helyi fürtön. Bontsa ki az **Alkalmazások csomópontot,** és győződjön meg arról, hogy az alkalmazás fut. 

Ha meg szeretné tudni, hogyan lehet az alkalmazást az Eclipse alkalmazásban a helyi fürt használatával hibakereséssel szeretné ellátni, olvassa el [a Java-szolgáltatás hibakeresése az Eclipse alkalmazásban című témakört.](./service-fabric-debugging-your-application-java.md)

Az alkalmazást az **alkalmazás közzététele** paranccsal is telepítheti a helyi fürtre:

1. Kattintson a jobb gombbal a Service Fabric-alkalmazásra, és válassza a **Service Fabric parancsot.**
2. A helyi menüben kattintson az **Alkalmazás közzététele... parancsra.**
3. Az **Alkalmazás közzététele** ablakban válassza a **PublishProfiles/Local.json** lehetőséget célprofilként, majd kattintson a **Közzététel gombra.**

    ![Publish (Közzététel) helyi párbeszédpanel](./media/service-fabric-get-started-eclipse/localjson.png)

    Alapértelmezés szerint a Local.json közzétételi profil úgy van beállítva, hogy közzétegye a helyi fürtön. A közzétételi profilokban található kapcsolati és végponti paraméterekről a következő szakaszban talál további információt.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>A Service Fabric-alkalmazás közzététele az Azure-ban az Eclipse segítségével

Az alkalmazás felhőben való közzétételéhez kövesse az alábbi lépéseket:

1. Az alkalmazás közzététele egy biztonságos fürta felhőben, x.509-es tanúsítványhasználatára van szükség a fürttel való kommunikációhoz. Teszt- és fejlesztési környezetekben a használt tanúsítvány gyakran a fürttanúsítvány. Éles környezetben a tanúsítványnak a fürttanúsítványtól eltérő ügyféltanúsítványnak kell lennie. A tanúsítványra és a személyes kulcsra is szükség van. A tanúsítványfájlnak (és a kulcsfájlnak PEM-formátumúnak kell lennie. Létrehozhat egy PEM-fájlt, amely a tanúsítványt és a személyes kulcsot tartalmazza egy PFX fájlból a következő openssl paranccsal:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Ha a PFX fájl nem jelszóval védett, használja `--passin pass:` az utolsó paraméter.

2. Nyissa meg a **Cloud.json** fájlt a **PublishProfiles** könyvtár ban. A fürtvégpontot és a fürt höz megfelelő biztonsági hitelesítő adatokat kell konfigurálnia.

   - A `ConnectionIPOrURL` mező a fürt IP-címét vagy URL-címét tartalmazza. Ne feledje, hogy az érték`https://`nem tartalmazza az URL-sémát ( ).
   - Alapértelmezés szerint `ConnectionPort` a `19080`mezőnek a , kivéve, ha kifejezetten módosította ezt a portot a fürthöz.
   - A `ClientKey` mezőnek egy PEM formátumú .pem vagy .key fájlra kell mutatnia a helyi számítógépen, amely az ügyfél- vagy fürttanúsítvány személyes kulcsát tartalmazza.
   - A `ClientCert` mezőnek a helyi számítógépen található PEM-formátumú .pem vagy .crt fájlra kell mutatnia, amely az ügyfél vagy a fürt tanúsítványadatait tartalmazza. Tanúsítvány. 

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

2. Kattintson a jobb gombbal a Service Fabric-alkalmazásra, és válassza a **Service Fabric parancsot.**
3. A helyi menüben kattintson az **Alkalmazás közzététele... parancsra.**
3. Az **Alkalmazás közzététele** ablakban válassza a **PublishProfiles/Cloud.json** lehetőséget célprofilként, majd kattintson a **Közzététel gombra.**

    ![A felhő közzétételi párbeszédablaka](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. A közzétételi művelet előrehaladását a Konzol ablakban követheti.
5. Annak ellenőrzéséhez, hogy az alkalmazás fut-e, nyissa meg a Service Fabric Explorer t az Azure-fürtön egy böngészőablakban. A fenti példában ez `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`a következő lenne: . Bontsa ki az **Alkalmazások csomópontot,** és győződjön meg arról, hogy az alkalmazás fut. 


Biztonságos Linux-fürtök, ha az alkalmazás reliable services szolgáltatásokat tartalmaz, konfigurálnia kell egy tanúsítványt, amely a szolgáltatások segítségével hívja meg a Service Fabric futásidejű API-kat. További információ: [Reliable Services alkalmazás konfigurálása Linux-fürtökön való futtatáshoz.](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)

A Java-ban írt Service Fabric Reliable Services-alkalmazások biztonságos Linux-fürtre való üzembe helyezésének gyors rövid [útmutatója: Java Reliable Services-alkalmazás telepítése](./service-fabric-quickstart-java-reliable-services.md)című témakörben található.

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Service Fabric-alkalmazás telepítése az Eclipse futtatási konfigurációival

A Service Fabric-alkalmazások üzembe helyezésének másik módszere, ha Eclipse futtatási konfigurációkat használ.

1. Az Eclipse programban nyissa meg a **Konfigurációk futtatása (Futtatás** > **i konfigurációk) (Futtatási konfigurációk) (Futtatási konfigurációk) (Futtatási konfiguráció**
2. A **Gradle Project** (Gradle-projekt) területen válassza a **ServiceFabricDeployer** futtatási konfigurációt.
3. A jobb oldali ablaktáblában az **Argumentumok** lapon győződjön meg arról, hogy az **ip,** **port**, **clientCert**és **clientKey** paraméterek megfelelően vannak beállítva a központi telepítéshez. Alapértelmezés szerint a paraméterek úgy vannak beállítva, hogy a helyi fürtre telepítsenek, mint a következő képernyőképen. Az alkalmazás azure-ban való közzétételéhez módosíthatja a paramétereket, hogy tartalmazzák a végpont részleteit és az Azure-fürt biztonsági hitelesítő adatait. További információt az előző, [A Service Fabric-alkalmazás közzététele az Azure-ban az Eclipse használatával](#publish-your-service-fabric-application-to-azure-with-eclipse)című szakaszban talál.

    ![Konfigurációs párbeszédpanel helyi futtatása](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Győződjön meg arról, hogy a **Munkakönyvtár** a telepíteni kívánt alkalmazásra mutat. Az alkalmazás módosításához kattintson a **Workspace** (Munkaterület) gombra, és válassza ki a kívánt alkalmazást.
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

Frissítési forgatókönyv esetén tegyük fel, hogy az **App1** projektet hozta létre a Service Fabric beépülő modullal az Eclipse-ben. A projektet a beépülő modullal helyezte üzembe a **fabric:/App1Application** nevű alkalmazás létrehozásához. Az alkalmazás típusa **App1ApplicationType**, az alkalmazás verziója pedig 1.0. A rendelkezésre állás megszakítása nélkül szeretné frissíteni az alkalmazást.

Először végezzen módosítást az alkalmazáson, majd építse újra a módosított szolgáltatást. Frissítse a módosított szolgáltatás jegyzékfájlját (ServiceManifest.xml) a szolgáltatás frissített verzióival (és a megfelelő Code, Config vagy Data értékkel). Módosítsa az alkalmazás jegyzékfájlját is (ApplicationManifest.xml) az alkalmazás frissített verziószámával és a módosított szolgáltatással.  

Ha az Eclipse-szel szeretné frissíteni az alkalmazást, létrehozhat egy duplikált futtatáskonfigurációs profilt, amelyet aztán szükség szerint az alkalmazás frissítésére használhat.

1.  Nyissa meg a **Futtatási** > **konfigurációk futtatása**. A bal oldali ablaktáblában kattintson a **Gradle Project** (Gradle-projekt) bal oldalán található kis nyílra.
2.  Kattintson a jobb gombbal a **ServiceFabricDeployer** elemre, majd válassza a **Duplicate** (Megkettőzés) parancsot. Adjon egy új nevet a konfigurációnak, például **ServiceFabricUpgrader**.
3.  A jobb oldali ablaktáblán, az **Arguments** (Argumentumok) lapon módosítsa a **-Pconfig='deploy'** értéket **-Pconfig='upgrade'** értékre, majd kattintson az **Apply** (Alkalmaz) gombra.

Ez a folyamat olyan futtatási konfigurációs profilt hoz létre és ment el, amellyel bármikor frissítheti az alkalmazást. Az alkalmazás jegyzékfájljából a legújabb frissített alkalmazástípus-verziót is lekéri.

Az alkalmazás frissítése eltarthat néhány percig. Az alkalmazás frissítésének folyamatát a Service Fabric Explorerben követheti nyomon.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>A Mavennel használni kívánt régi Service Fabric Java-alkalmazások migrálása
Nemrégiben áthelyeztük a Service Fabric Java-kódtárakat a Service Fabric Java SDK-ból a Mavenen futó adattárba. Az Eclipse-szel létrehozott új alkalmazások a legfrissebb projekteket hozzák létre (amelyek képesek együttműködni a Mavennel), a meglévő állapotmentes vagy aktor Service Fabric Java-alkalmazások pedig, amelyek korábban a Service Fabric Java SDK-t használták, frissíthetők a Mavenben található Service Fabric Java-függőségek használatára. Kövesse az [itt](service-fabric-migrate-old-javaapp-to-use-maven.md) felsorolt lépéseket, ha biztosítani kívánja, hogy a régebbi alkalmazásaik együttműködjenek a Mavennel.

## <a name="next-steps"></a>További lépések

- A Java Megbízható szolgáltatásalkalmazás létrehozásának és helyi és Azure-beli üzembe helyezésének gyors lépéseit [a Rövid útmutató: Java Reliable Services-alkalmazás üzembe helyezése ( Gyorsútmutató) témakörben](./service-fabric-quickstart-java-reliable-services.md)található.
- Ha tudni szeretné, hogyan lehet egy Java-alkalmazást hibakereséssel ellátni a helyi fürtön, olvassa el [a Java-szolgáltatások hibakeresése](./service-fabric-debugging-your-application-java.md)az Eclipse alkalmazásban .
- A Service Fabric-alkalmazások figyelésének és diagnosztizálásának figyelése [és diagnosztizálása a szolgáltatások figyelése és diagnosztizálása a helyi számítógép-fejlesztési beállításokban .Learn](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)how to monitor and diagnose Service Fabric applications, see Monitor and diagnose services in a local machine development setup.

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
