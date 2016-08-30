<properties
   pageTitle="Az első Service Fabric-alkalmazás létrehozása a Visual Studióban | Microsoft Azure"
   description="Service Fabric-alkalmazás létrehozása, üzembe helyezése és hibakeresése a Visual Studio használatával"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/27/2016"
   ms.author="ryanwi"/>

# Az első Azure Service Fabric-alkalmazás létrehozása a Visual Studióban

A Service Fabric SDK egy Visual Studio-bővítmény tartalmaz, amely sablonokat és eszközöket biztosít a Service Fabric-alkalmazások létrehozásához, üzembe helyezéséhez és hibakereséséhez. Ez a témakör végigvezeti Önt az első alkalmazás létrehozásának folyamatán a Visual Studióban.

## Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy [beállította a fejlesztőkörnyezetet](service-fabric-get-started.md).

## Bemutató videó

A következő videó végigkalauzolja az ebben az oktatóanyagban található lépéseken.

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## Az alkalmazás létrehozása

A Service Fabric-alkalmazás egy vagy több szolgáltatást tartalmazhat, melyek mindegyike adott szerepkörrel rendelkezik az alkalmazás funkcióinak biztosításához. Az Új projekt varázslóval az első szolgáltatási projektjével együtt egy alkalmazási projektet is létrehozhat. Később további szolgáltatásokat is hozzáadhat.

1. Indítsa el a Visual Studiót rendszergazdaként.

2. Kattintson a **File > New Project > Cloud > Service Fabric Application** (Fájl > Új projekt > Felhő > Service Fabric-alkalmazás) elemre.

3. Adjon nevet az alkalmazásnak, majd kattintson az **OK** gombra.

    ![A Visual Studio Új projekt párbeszédpanelje][1]

4. A következő oldalon a rendszer megkéri, hogy válassza ki az első szolgáltatástípust, amelyet szerepeltetni kíván az alkalmazásában. Az oktatóanyag céljából mi most a **Stateful** (Állapotalapú) típust választjuk. Adjon neki nevet, majd kattintson az **OK** gombra.

    ![A Visual Studio Új szolgáltatás párbeszédpanelje][2]

    >[AZURE.NOTE] A beállításokkal kapcsolatos további információkért lásd: [Keretrendszer kiválasztása](service-fabric-choose-framework.md).

    A Visual Studio létrehozza az alkalmazási projektet és az állapotalapú szolgáltatási projektet, és megjeleníti őket a Megoldáskezelőben.

    ![A Megoldáskezelő folytatja az alkalmazás létrehozását állapotalapú szolgáltatással][3]

    Az alkalmazási projekt nem tartalmaz közvetlenül semmilyen kódot. Helyette számos szolgáltatási projektre hivatkozik. Ezenfelül három egyéb típusú tartalmat is tartalmaz:

    - **Profilok közzététele**: különböző környezetek eszközbeállításainak a kezelésére használható.

    - **Parancsfájlok**: az alkalmazás üzembe helyezéséhez/frissítéséhez szükséges PowerShell-parancsfájlt tartalmazza. Ezt a parancsfájlt a Visual Studio a háttérben használja, és közvetlenül a parancssorból hívható.

    - **Alkalmazás definíciója**: az *ApplicationPackageRoot* alatt tartalmazza az alkalmazás jegyzékét, az *ApplicationParameters* alatt pedig a kapcsolódó alkalmazásparaméter-fájlokat, amelyek meghatározzák az alkalmazást, és lehetővé teszik annak egy adott környezethez való konfigurálását.

    A szolgáltatási projekt tartalmának áttekintéséhez lásd: [Bevezetés a Reliable Services használatába](service-fabric-reliable-services-quick-start.md).

## Az alkalmazás üzembe helyezése és hibakeresése

Most, hogy már rendelkezik egy alkalmazással, próbálja meg futtatni.

1. Nyomja le az F5 billentyűt a Visual Studióban, hogy üzembe helyezze az alkalmazást a hibakereséshez.

    >[AZURE.NOTE] Először ez eltarthat egy ideig, mivel a Visual Studio helyi fürtöt hoz létre a fejlesztéshez. A helyi fürt ugyanazt a platformkódot futtatja, amelyet Ön egy többgépes fürtben hoz létre, csak egyetlen számítógépen. A fürt létrehozási állapota a Visual Studio kimeneti ablakában jelenik meg.

    Amikor a fürt létrejött, az SDK-val együtt beépített helyifürt-rendszertálcakezelő alkalmazástól értesítést fog kapni.

    ![A helyifürt-rendszertálca értesítése][4]

2. Ha az alkalmazás elindult, a Visual Studio automatikusan megjeleníti a diagnosztikai eseménynaplót, ahol nyomon követheti a szolgáltatás kimeneti adatait.

    ![Diagnosztikai eseménynapló][5]

    Az állapotalapú szolgáltatássablon esetén az üzenetek egyszerűen a számláló értékét jelenítik meg, amely a MyStatefulService.cs `RunAsync` metódusában folyamatosan növekszik.

3. Bontsa ki az egyik eseményt, hogy további részleteket tekinthessen meg, beleértve azt a csomópontot is, amelyben a kód fut. Ebben az esetben ez a _Node_2, de az Ön számítógépén ez eltérő lehet.

    ![A diagnosztikai eseménynapló részletei][6]

    A helyi fürt egyetlen számítógépen lévő öt csomópontot tartalmaz. Olyan öt csomópontból álló fürtöt utánoz, amelynek a csomópontjai különböző gépeken vannak. Vegyük le a helyi fürtről az egyik csomópontot, ezzel szimulálva egy gép elvesztését, és ugyanakkor kipróbálva a Visual Studio hibakereső funkcióját.

    >[AZURE.NOTE] Az alkalmazás projektsablon által kibocsátott diagnosztikai eseményei a megadott `ServiceEventSource` osztályt használják. További információ: [A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

4. Keresse meg a szolgáltatási projektjében a StatefulService-ből származó osztályt (pl. MyStatefulService), és állítson be egy töréspontot a `RunAsync` metódus első sorában.

    ![Töréspont az állapotalapú szolgáltatás RunAsync metódusában][7]

5. Kattintson a jobb gombbal a Local Cluster Manager rendszertálca-alkalmazásra, és válassza ki a **Manage Local Cluster** (Helyi fürt kezelése) lehetőséget a Service Fabric Explorer elindításához.

    ![A Manage Local Cluster alkalmazásból indítsa el a Service Fabric Explorert.][systray-launch-sfx]

    A Service Fabric Explorer egy fürt vizuális ábrázolását biztosítja, beleértve a rajta üzembe helyezett alkalmazások készletét, valamint az őt alkotó fizikai csomópontok készletét is. A Service Fabric Explorerrel kapcsolatos további tudnivalókért lásd: [A fürt megjelenítése](service-fabric-visualizing-your-cluster.md).

6. A bal oldali panelen bontsa ki a **Cluster > Nodes** (Fürt > Csomópontok) elemet, és keresse meg azt csomópont, amelyikben a kódja fut.

7. Kattintson az **Actions > Deactivate (Restart)** (Műveletek > Inaktiválás (Újraindítás)) elemre a számítógép-újraindítás szimulálásához. (Megjegyzés: a bal oldali ablaktáblán, a csomópontlista nézet helyi menüjében is elvégezheti ezt a három pont kiválasztásával.)

    ![Csomópont leállítása a Service Fabric Explorerben][sfx-stop-node]

    Pillanatnyilag a töréspont megjelenését tekintheti meg a Visual Studióban, mivel az egyik csomóponton korábban végzett számítása zökkenőmentesen átadja a feladatokat egy másiknak.

8. Térjen vissza a Diagnosztikai eseménynaplóhoz, és vizsgálja meg az üzeneteket. Vegye figyelembe, hogy a számláló értéke továbbra is növekedett, annak ellenére, hogy az események valójában egy másik csomópontról érkeznek.

    ![A diagnosztikai eseménynapló a feladatátvétel után][diagnostic-events-viewer-detail-post-failover]

### Takarítás

  A téma lezárása előtt fontos megjegyezni, hogy a helyi fürt nagyon is valós. Az alkalmazásai még a hibakereső leállítása és a Visual Studio bezárása után is tovább futnak a háttérben. Az alkalmazásai természetétől függően ez a háttértevékenység jelentős erőforrásokat vehet igénybe a számítógépén. Ennek kezelésére több lehetőség közül választhat:

  1. Az egyes alkalmazások és adataik eltávolításához használja a Service Fabric Explorerben a **Delete application** (Alkalmazás törlése) műveletet vagy az **ACTIONS** menüvel, vagy a bal oldali ablaktáblában található alkalmazáslista-nézet helyi menüjével.

    ![Alkalmazás törlése a Service Fabric Explorerrel][sfe-delete-application]

  2. Miután az alkalmazást törölte a fürtből, kiválaszthatja az **Unprovision Type** (Típus leépítése) lehetőséget az alkalmazás számára, amely eltávolítja az alkalmazáscsomagot a fürt lemezképtárolójából a kódjával és a konfigurációjával együtt.
  3. A fürt leállításához, ugyanakkor az alkalmazás adatainak és nyomkövetéseinek megtartásához a rendszertálca alkalmazásban kattintson a **Stop Local Cluster** (Helyi fürt leállítása) parancsra.

  4. A fürt teljes törléséhez a rendszertálca alkalmazásban kattintson a **Remove Local Cluster** (Helyi fürt eltávolítása) parancsra. Vegye figyelembe, hogy ez a beállítás egy másik lassú üzembe helyezést fog eredményezni, amikor legközelebb a Visual Studióban lenyomja az F5 billentyűt. Csak akkor használja, ha egy ideig nem szándékozik használni a helyi fürtöt, vagy ha erőforrásokat kell felszabadítania.



## Következő lépések

<!--
Temporarily removing this link because we have removed the ASP.NET template.

 - [See how you can expose your services to the Internet with a web service front end](service-fabric-add-a-web-frontend.md)
-->
- [Ismerje meg, hogyan hozhat létre fürtöt az Azure-ban](service-fabric-cluster-creation-via-portal.md)
- [További tudnivalók a Reliable Services szolgáltatásokról](service-fabric-reliable-services-quick-start.md)
- [Próbáljon meg szolgáltatást létrehozni a Reliable Actors programozási modell használatával.](service-fabric-reliable-actors-get-started.md)

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png



<!--HONumber=jun16_HO2-->


