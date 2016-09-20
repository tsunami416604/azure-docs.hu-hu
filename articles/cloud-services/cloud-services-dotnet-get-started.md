<properties
    pageTitle="Ismerkedés az Azure Cloud Services szolgáltatással és az ASP.NET keretrendszerrel | Microsoft Azure"
    description="Ismerje meg, hogyan hozhat létre többrétegű alkalmazást az ASP.NET MVC és az Azure használatával. Az alkalmazás felhőszolgáltatásban fut webes és feldolgozói szerepkörben. Entity Framework, SQL Database és Azure Storage üzenetsorokat és blobokat használ."
    services="cloud-services, storage"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/10/2016"
    ms.author="adegeo"/>

# Ismerkedés az Azure Cloud Services szolgáltatással és az ASP.NET keretrendszerrel

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

## Áttekintés

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre többrétegű .NET-alkalmazást ASP.NET MVC kezelőfelülettel, illetve hogyan telepítheti azt egy [Azure-felhőszolgáltatásban](cloud-services-choose-me.md). Az alkalmazás az [Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336279) szolgáltatást, az [Azure Blob szolgáltatást](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) és az [Azure Queue szolgáltatást](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) használja. A [Visual Studio projekt letölthető](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) az MSDN kódgalériából.

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja össze és futtathatja az alkalmazást helyileg, hogyan telepítheti az Azure-ban és futtathatja a felhőben, valamint hogyan építheti fel az alapoktól kezdve. Ha szeretné, kezdheti az alapoktól a felépítést, majd később elvégezheti a tesztelés és a telepítés lépéseit.

## Contoso Ads alkalmazás

Ez az alkalmazás egy hirdetőtábla. A felhasználók szöveg megadásával és egy kép feltöltésével hoznak létre hirdetéseket. Láthatják a hirdetések miniatűr képekkel ellátott listáját, majd teljes méretben is megtekinthetik a képet, amikor kiválasztanak egy hirdetést a részletek megtekintése céljából.

![Hirdetéslista](./media/cloud-services-dotnet-get-started/list.png)

Az alkalmazás [üzenetsor-központú munkasémát](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) használ, hogy áthelyezze a miniatűrök létrehozásának processzorigényes feladatát egy háttérfolyamatra.

## Alternatív architektúra: Websites és WebJobs

Ebből az oktatóanyagból megtudhatja, hogyan futtathat előtér- és háttéralkalmazásokat egyaránt az Azure felhőszolgáltatásban. Alternatív megoldásként az előtér-alkalmazást futtathatja egy [Azure-webhelyen](/services/web-sites/), a háttéralkalmazás esetében pedig használhatja a [WebJobs](http://go.microsoft.com/fwlink/?LinkId=390226) szolgáltatást (jelenleg előzetes verzióban érhető el). A WebJobs szolgáltatást alkalmazó oktatóanyagot a [Get Started with the Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md) (Ismerkedés az Azure WebJobs SDK-val) című részben találja. A forgatókönyvéhez leginkább illő szolgáltatások kiválasztásával kapcsolatos információkért lásd: [Az Azure Websites, a Cloud Services és a Virtual Machines összevetése](../app-service-web/choose-web-site-cloud-service-vm.md).

## Ismertetett témák

* A gép alkalmassá tétele az Azure-alapú fejlesztésre az Azure SDK telepítésével.
* Visual Studio felhőszolgáltatás-projekt létrehozása ASP.NET MVC webes és feldolgozói szerepkörök alkalmazásával.
* A felhőszolgáltatás-projekt helyi tesztelése az Azure Storage Emulator használatával.
* A felhőprojekt közzététele egy Azure-felhőszolgáltatásban és tesztelése egy Azure-tárfiók használatával.
* Fájlok feltöltése és tárolása az Azure Blob szolgáltatásban.
* Az Azure Queue szolgáltatás használata a rétegek közötti kommunikációhoz.

## Előfeltételek

Az oktatóanyag feltételezi, hogy tisztában van az [Azure-felhőszolgáltatások alapfogalmaival](cloud-services-choose-me.md), például a *webes szerepkör* és a *feldolgozói szerepkör* terminológiájával.  Továbbá azt is feltételezi, hogy az [ASP.NET MVC](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) és a [Web Forms](http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview) Visual Studióban való használatának módját is ismeri. A mintaalkalmazás az MVC-t használja, de az oktatóanyag nagy része a Web Forms esetében is alkalmazható.

Helyileg Azure-előfizetés nélkül is futtathatja az alkalmazást, de a felhőben való közzétételéhez előfizetés szükséges. Ha nincs fiókja, [aktiválhatja az MSDN előfizetői előnyeit](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668), vagy [regisztrálhat egy ingyenes próbaverzióra](/pricing/free-trial/?WT.mc_id=A55E3C668).

Az oktatóanyag utasításai az alábbi termékek bármelyikére alkalmazhatók:

* Visual Studio 2013
* Visual Studio 2015

Ha egyikkel sem rendelkezik, az Azure SDK telepítésekor a Visual Studio 2015 is automatikusan telepítve lesz.

## Alkalmazásarchitektúra

Az alkalmazás SQL-adatbázisban tárolja a hirdetéseket, amihez az Entity Framework Code First megoldást használja a táblák létrehozásához és az adatok eléréséhez. Az egyes hirdetések esetében az adatbázis két URL-címet tárol, egyet a teljes méretű képhez, egyet pedig a miniatűrhöz.

![Hirdetés tábla](./media/cloud-services-dotnet-get-started/adtable.png)

Amikor egy felhasználó feltölt egy képet, a webes szerepkörrel rendelkező előtér-alkalmazás egy [Azure-blobban](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) tárolja azt, a hirdetés információit pedig az adatbázisban a blobra mutató URL-címmel együtt. Ezzel egy időben üzenetet ír egy Azure-üzenetsorba. A feldolgozói szerepkörrel futó háttérfolyamat rendszeres időközönként lekérdezi az új üzeneteket az üzenetsorról. Amikor egy új üzenet jelenik meg, a feldolgozói szerepkör létrehozza a kép miniatűrjét, és frissíti a miniatűr URL-címét a hirdetés adatbázismezőjében. Az alábbi ábra bemutatja, hogyan működnek együtt az alkalmazás részei.

![Contoso Ads architektúra](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[AZURE.INCLUDE [install-sdk](../../includes/install-sdk-2015-2013.md)]

## A kész megoldás letöltése és futtatása

1. Töltse le és csomagolja ki a [kész megoldást](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4).

2. Indítsa el a Visual Studiót.

3. Válassza a **Fájl** menü **Projekt megnyitása** elemét, keresse meg a letöltött megoldást, majd nyissa meg a megoldásfájlt.

3. Nyomja le a Ctrl+Shift+B billentyűkombinációt a megoldás felépítéséhez.

    Alapértelmezés szerint a Visual Studio automatikusan visszaállítja a NuGet-csomag tartalmát, amelyet a *.zip* fájl nem tartalmazott. Ha a csomagok nem állnak vissza, telepítse őket manuálisan. Ehhez lépjen a **Manage NuGet Packages for Solution** (Megoldás NuGet-csomagjainak kezelése) párbeszédpanelre, és kattintson a **Restore** (Visszaállítás) gombra a jobb felső sarokban.

3. A **Megoldáskezelőben** győződjön meg arról, hogy a **ContosoAdsCloudService** van kiválasztva kiindulási projektként.

2. Ha a Visual Studio 2015-öt használja, módosítsa az SQL Server kapcsolati karakterláncát az alkalmazás *Web.config* fájljában a ContosoAdsWeb projekt esetében, illetve a *ServiceConfiguration.Local.cscfg* fájlt a ContosoAdsCloudService projekt esetében. Mindkét esetben módosítsa a „(localdb)\v11.0” elemet a következőre: „(localdb)\MSSQLLocalDB”.

1. Az alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűkombinációt.

    Amikor helyileg futtat egy felhőszolgáltatás-projektet, a Visual Studio automatikusan meghívja az Azure *Compute Emulator* és az Azure *Storage Emulator* eszközöket. A Compute Emulator a számítógép erőforrásait felhasználva szimulálja a webes és a feldolgozói szerepkörök környezeteit. A Storage Emulator egy [SQL Server Express LocalDB](http://msdn.microsoft.com/library/hh510202.aspx) adatbázis használatával szimulálja az Azure felhőalapú tárolást.

    Egy felhőszolgáltatás-projekt első futtatásakor nagyjából egy percet vesz igénybe az emulátorok elindítása. Az emulátorok elindulását követően az alapértelmezett böngésző megnyitja az alkalmazás kezdőlapját.

    ![Contoso Ads architektúra](./media/cloud-services-dotnet-get-started/home.png)

2. Kattintson a **Create an Ad** (Hirdetés létrehozása) gombra.

2. Adjon meg néhány tesztadatot, és válasszon ki egy feltölteni kívánt *.jpg* formátumú képet, majd kattintson a **Create** (Létrehozás) elemre.

    ![Lap létrehozása](./media/cloud-services-dotnet-get-started/create.png)

    Az alkalmazás az Index lapra ugrik, de az új hirdetés miniatűrje nem jelenik meg, mert a feldolgozása még nem történt meg.

3. Várjon egy kicsit, majd frissítse az Index lapot a miniatűr megjelenítéséhez.

    ![Index lap](./media/cloud-services-dotnet-get-started/list.png)

4. Kattintson a hirdetés **Details** (Részletek) elemére a teljes méretű kép megjelenítéséhez.

    ![Részletek lap](./media/cloud-services-dotnet-get-started/details.png)

Az alkalmazást teljes mértékben helyi számítógépén futtatja, a felhőhöz való csatlakozás nélkül. A Storage Emulator az üzenetsor és a blob adatait egy SQL Server Express LocalDB adatbázisban tárolja, az alkalmazás pedig egy másik LocalDB adatbázisban tárolja a hirdetés adatait. Az Entity Framework Code First automatikusan létrehozta a hirdetés-adatbázist, amikor a webalkalmazás első alkalommal próbált hozzáférni ahhoz.

A következő szakaszban konfigurálhatja a megoldást az Azure felhőbeli erőforrások használatára az üzenetsorok, blobok és alkalmazás-adatbázisok esetében a felhőben való futtatásakor. Ha szeretné folytatni a helyi futtatást, de használná a felhőalapú tárolást és az adatbázis-erőforrásokat is, megteheti: mindössze kapcsolati karakterláncokat kell beállítania, aminek a módját a későbbiekben ismerheti meg.

## Az alkalmazás központi telepítése az Azure-ban

Az alkalmazás felhőben való futtatásához az alábbi lépéseket kell végrehajtania:

* Hozzon létre egy Azure-felhőszolgáltatást.
* Hozzon létre egy Azure SQL-adatbázist.
* Hozzon létre egy Azure-tárfiókot.
* Konfigurálja a megoldást arra, hogy az Azure-ban való futáskor az Azure SQL-adatbázist használja.
* Konfigurálja a megoldást arra, hogy az Azure-ban való futáskor az Azure-tárfiókot használja.
* Telepítse a projektet az Azure-felhőszolgáltatásában.

### Azure-felhőszolgáltatás létrehozása

Az Azure-felhőszolgáltatás az a környezet, amelyben az alkalmazás futni fog.

1. Nyissa meg a [klasszikus Azure portált](http://manage.windowsazure.com) a böngészőjében.

2. Kattintson az **Új > Számítás > Felhőszolgáltatás > Gyorslétrehozás** elemre.

4. Az URL beviteli mezőbe írjon be egy URL-előtagot.

    Ennek az URL-nek egyedinek kell lennie.  Ha olyan előtagot választ, amely már használatban van, hibaüzenetet kap.

5. Válassza ki a régiót, ahol telepíteni szeretné az alkalmazást.

    Ez a mező határozza meg, hogy a felhőszolgáltatása melyik adatközpontban fog üzemelni. Termelési alkalmazások esetében az ügyfeleihez legközelebb eső régiót kellene kiválasztania. A jelen oktatóanyag esetében válassza az Önhöz legközelebbi régiót.

6. Kattintson a **Felhőalapú szolgáltatás létrehozása** elemre.

    Az alábbi képen egy contosoads.cloudapp.net URL-címmel ellátott felhőszolgáltatás lesz létrehozva.

    ![Új felhőszolgáltatás](./media/cloud-services-dotnet-get-started/newcs.png)

### Azure SQL-adatbázis létrehozása

Amikor az alkalmazás a felhőben fut, felhőalapú adatbázist fog használni.

1. A [klasszikus Azure portálon](http://manage.windowsazure.com) kattintson az **ÚJ > Adatszolgáltatások > SQL Database > Gyorslétrehozás** elemre.

1. Az **Adatbázis neve** mezőbe írja be a következőt: *contosoads*.

1. A **Kiszolgáló** legördülő listából válassza az **Új SQL-adatbáziskiszolgáló** elemet.

    Amennyiben az előfizetéséhez már tartozik egy kiszolgáló, alternatív megoldásként azt is kiválaszthatja a legördülő listából.

1. Válassza ki ugyanazt a **Régiót**, amelyet a felhőszolgáltatás számára is választott.

    Ha a felhőszolgáltatás és az adatbázis különböző adatközpontokban van (különböző régiókban), a késés mértéke megnő, és az adatközponton kívül használt sávszélességért fizetnie kell. Az adatközponton belül használt sávszélesség ingyenes.

1. Adjon meg egy rendszergazdai **Bejelentkezési nevet** és **Jelszót**.

    Ha az **Új SQL-adatbáziskiszolgáló** lehetőséget választotta, akkor itt nem egy meglévő, hanem egy új nevet és jelszót kell megadnia, amelyet most határoz meg későbbi használat céljából az adatbázishoz való hozzáféréshez. Ha korábban létrehozott adatbázist választott, akkor a rendszer a már létrehozott rendszergazdai felhasználói fiók jelszavát kéri.

1. Kattintson az **SQL-adatbázis létrehozása** elemre.

    ![Új SQL-adatbázis](./media/cloud-services-dotnet-get-started/newdb.png)

1. Miután az Azure létrehozta az adatbázist, kattintson az **SQL-adatbázisok** lapra a portál bal oldali ablaktáblájában, majd kattintson az új adatbázis nevére.

2. Kattintson az **Irányítópult** lapra.

3. Kattintson a **Manage allowed IP addresses** (Engedélyezett IP-címek kezelése) elemre.

4. Az **Allowed Services** (Engedélyezett Szolgáltatások) területen módosítsa az **Azure Services** (Azure-szolgáltatások) beállítását a következőre: **Yes** (Igen).

5. Kattintson a **Save** (Mentés) gombra.

### Azure-tárfiók létrehozása

Az Azure-tárfiók erőforrásokat biztosít az üzenetsor és a blob adatainak felhőbeli tárolásához.

Egy valós alkalmazás esetében általában külön fiókot hozna létre az alkalmazás adatai és a naplózási adatok, illetve a tesztadatok és a termelési adatok számára is. A jelen oktatóanyag esetében csak egy fiókot fog használni.

1. A [klasszikus Azure portálon](http://manage.windowsazure.com) kattintson az **Új > Adatszolgáltatások > Tárolás > Gyorslétrehozás** elemre.

4. Az **URL** mezőbe írjon be egy URL-előtagot.

    Ez az előtag és a mező alatt látható szöveg képezi a tárfiók egyedi URL-címét. Ha a megadott előtag már használatban van, másikat kell választania.

5. A **Region** (Régió) legördülő listában válassza ugyanazt a régiót, amelyet a felhőszolgáltatás számára is választott.

    Ha a felhőszolgáltatás és a tárfiók különböző adatközpontokban van (különböző régiókban), a késés mértéke megnő, és az adatközponton kívül használt sávszélességért fizetnie kell. Az adatközponton belül használt sávszélesség ingyenes.

    Az Azure-affinitáscsoportok egy olyan mechanizmust biztosítanak, amely minimálisra csökkenti az erőforrások között lévő távolságot az adatközpontban, csökkentve ezáltal a késés mértékét is. A jelen oktatóanyag nem használ affinitáscsoportokat. További információ: [Affinitáscsoportok létrehozása az Azure-ban](http://msdn.microsoft.com/library/jj156209.aspx).

6. A **Replication** (Replikáció) legördülő listában válassza a **Locally redundant** (Helyileg redundáns) elemet.

    Ha a georeplikáció engedélyezve van a tárfiókon, akkor a tárolt tartalom replikálása egy másodlagos adatközpontba történik, amely ezáltal a feladatátvétel helyévé válik, amennyiben az elsődleges helyen jelentős katasztrófa következik be. A georeplikáció további költségeket vonhat maga után. A teszt- és fejlesztői fiókok esetében általában nem érdemes fizetni a georeplikációért. További információ: [Tárfiók létrehozása, kezelése vagy törlése](../storage/storage-create-storage-account.md#replication-options).

5. Kattintson a **Tárfiók létrehozása** lehetőségre.

    ![Új tárfiók](./media/cloud-services-dotnet-get-started/newstorage.png)

    Az alábbi képen egy `contosoads.core.windows.net` URL-címmel ellátott tárfiók lesz létrehozva.

### A megoldás konfigurálása arra, hogy az Azure-ban való futáskor az Azure SQL-adatbázist használja

A webes projekt és a feldolgozói szerepkör is saját adatbázis-kapcsolati karakterlánccal rendelkezik, és mindkettőnek az Azure SQL-adatbázisra kell mutatnia az alkalmazás Azure-ban való futásakor.

A webes szerepkör esetében [Web.config transzformálása](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations), a feldolgozói szerepkör esetében pedig felhőszolgáltatás környezeti beállítást kell alkalmaznia.

>[AZURE.NOTE] Ebben és a következő szakaszban a hitelesítő adatokat projektfájlokban fogja tárolni. [Ne tároljon bizalmas adatokat nyilvános forráskódú adattárakban.](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets)

1. A ContosoAdsWeb projektben nyissa meg a *Web.Release.config* átalakítófájlt az alkalmazás *Web.config* fájljához, törölje a `<connectionStrings>` elemet tartalmazó megjegyzésblokkot, és illessze be a helyére az alábbi kódot.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Hagyja megnyitva a fájlt a szerkesztéshez.

2. A [klasszikus Azure portálon](http://manage.windowsazure.com) kattintson a bal oldali ablaktáblában található **SQL-adatbázisok** elemre, kattintson az oktatóanyaghoz létrehozott adatbázisra, kattintson az **Irányítópult** lapra, majd kattintson a **Kapcsolati karakterláncok megjelenítése** elemre.

    ![Kapcsolati karakterláncok megjelenítése](./media/cloud-services-dotnet-get-started/showcs.png)

    A portál megjeleníti a kapcsolati karakterláncokat helyőrzővel helyettesített jelszóval.

    ![Kapcsolati karakterláncok](./media/cloud-services-dotnet-get-started/connstrings.png)

4. A *Web.Release.config* átalakítófájlban törölje a `{connectionstring}` elemet, és illessze be a helyére a klasszikus Azure portálról származó ADO.NET kapcsolati karakterláncot.

5. A *Web.Release.config* átalakítófájlba beillesztett kapcsolati karakterláncban helyettesítse a `{your_password_here}` elemet az új SQL-adatbázis számára létrehozott jelszóval.

7. Mentse a fájlt.  

6. Jelölje ki és másolja a kapcsolati karakterláncot (az idézőjelek nélkül) a feldolgozóiszerepkör-projekt konfigurálásának alábbi lépéseiben való használatra.

5. A **Megoldáskezelőben** a felhőszolgáltatás-projekt **Szerepkörök** területén kattintson a jobb gombbal a **ContosoAdsWorker**, majd a **Tulajdonságok** elemre.

    ![Szerepkör tulajdonságai](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)

6. Kattintson a **Beállítások** fülre.

7. Módosítsa a **Szolgáltatás konfigurációja** beállítását a következőre: **Felhő**.

7. Jelölje ki a `ContosoAdsDbConnectionString` beállítás **Érték** mezőjét, majd illessze be az oktatóanyag előző szakaszából másolt kapcsolati karakterláncot.

    ![A feldolgozói szerepkör adatbázis-kapcsolati karakterlánca](./media/cloud-services-dotnet-get-started/workerdbcs.png)

7. Mentse a módosításokat.  

### A megoldás konfigurálása az Azure-tárfiók használatára az Azure-ban való futás során

Az Azure-tárfiók kapcsolati karakterláncainak tárolása a webes- és a feldolgozóiszerepkör-projektek esetében egyaránt környezeti beállításokban történik a felhőszolgáltatás-projektben. Az egyes projektek esetében külön beállításokat kell alkalmazni, amikor az alkalmazás helyileg és amikor a felhőben fut. A felhőkörnyezet beállításait a webes és a feldolgozóiszerepkör-projektek esetében egyaránt frissíteni fogja.

1. A **Megoldáskezelőben** a **ContosoAdsCloudService** projekt **Szerepkörök** területén kattintson a jobb gombbal a **ContosoAdsWeb** elemre, majd kattintson a **Tulajdonságok** lehetőségre.

    ![Szerepkör tulajdonságai](./media/cloud-services-dotnet-get-started/roleproperties.png)

2. Kattintson a **Beállítások** fülre. A **Szolgáltatás konfigurációja** legördülő mezőben válassza a **Felhő** elemet.

    ![Felhő konfigurálása](./media/cloud-services-dotnet-get-started/sccloud.png)

3. Jelölje ki a **StorageConnectionString** bejegyzést, és megjelenik egy három pontot (**...**) ábrázoló gomb a sor jobb oldali végén. Kattintson a három pontot ábrázoló gombra a **Create Storage Account Connection String** (Tárfiók kapcsolati karakterláncának létrehozása) párbeszédpanel megnyitásához.

    ![A Kapcsolati karakterlánc létrehozása mező megnyitása](./media/cloud-services-dotnet-get-started/opencscreate.png)

4. A **Create Storage Connection String** (Tárfiók kapcsolati karakterláncának létrehozása) párbeszédpanelen kattintson a **Your subscription** (Saját előfizetés) elemre, válassza a korábban létrehozott tárfiókot, majd kattintson az **OK** gombra. Ha még nincs bejelentkezve, a rendszer az Azure-fiókja hitelesítő adatait kéri.

    ![Tárfiók kapcsolati karakterláncának létrehozása](./media/cloud-services-dotnet-get-started/createstoragecs.png)

5. Mentse a módosításokat.

6. A `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` kapcsolati karakterlánc beállításához kövesse ugyanazt az eljárást, mint a `StorageConnectionString` kapcsolati karakterlánc esetében.

    Ez a kapcsolati karakterlánc naplózásra használható.

7. A **ContosoAdsWorker** szerepkör mindkét kapcsolati karakterláncának beállításához kövesse ugyanazt az eljárást, mint a **ContosoAdsWeb** szerepkör esetében. Ne felejtse el a **Szolgáltatás konfigurációja** beállítását a következőre módosítani: **Felhő**.

A Visual Studio felhasználói felületén keresztül konfigurált szerepkörnyezeti beállítások a ContosoAdsCloudService projekt alábbi fájljaiban lesznek tárolva:

* *ServiceDefinition.csdef* – Meghatározza a beállításneveket.
* *ServiceConfiguration.Cloud.cscfg* – Értékeket biztosít az alkalmazás felhőben való futtatásához.
* *ServiceConfiguration.Local.cscfg* – Értékeket biztosít az alkalmazás helyi futtatásához.

A ServiceDefinition.csdef például az alábbi definíciókat tartalmazza.

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

A *ServiceConfiguration.Cloud.cscfg* fájl pedig azokat az értékeket tartalmazza, amelyeket a beállításokhoz adott meg a Visual Studióban.

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

Az `<Instances>` beállítás megadja azon virtuális gépek számát, amelyeken az Azure a feldolgozói szerepkör kódját fogja futtatni. A [További lépések](#next-steps) szakasz hivatkozásokat tartalmaz a felhőszolgáltatás kiterjesztésére vonatkozó további információkra.

###  A projekt központi telepítése az Azure-ban

1.  A **Megoldáskezelőben** kattintson a jobb gombbal a **ContosoAdsCloudService** felhőprojektre, majd válassza a **Közzététel** lehetőséget.

    ![Közzététel menü](./media/cloud-services-dotnet-get-started/pubmenu.png)

2. Az **Publish Azure Application** (Azure-alkalmazás közzététele) varázsló **Bejelentkezés** lépésében kattintson a **Tovább** gombra.

    ![Bejelentkezés lépés](./media/cloud-services-dotnet-get-started/pubsignin.png)

3. A varázsló **Beállítások** lépésében kattintson a **Tovább** gombra.

    ![Beállítások lépés](./media/cloud-services-dotnet-get-started/pubsettings.png)

    A **Speciális** lapon szereplő alapértelmezett beállítások megfelelnek a jelen oktatóanyag céljainak. A Speciális lappal kapcsolatos további információkért lásd: [Publish Azure Application Wizard](http://msdn.microsoft.com/library/hh535756.aspx) (Azure-alkalmazás közzététele varázsló).

4. Az **Összegzés** lépésben kattintson a **Közzététel** lehetőségre.

    ![Összegzés lépés](./media/cloud-services-dotnet-get-started/pubsummary.png)

   Megnyílik az **Azure tevékenységnapló** ablak a Visual Studióban.

5. Kattintson a jobbra mutató nyíl ikonra a telepítés részleteinek kibontásához.

    A telepítés akár 5 percet vagy több időt is igénybe vehet.

    ![Azure tevékenységnapló ablak](./media/cloud-services-dotnet-get-started/waal.png)

6. Amikor a telepítés befejeződött, kattintson a **Webalkalmazás URL-címe** elemre az alkalmazás elindításához.

7. Most már hirdetések létrehozásával, megtekintésével és szerkesztésével tesztelheti az alkalmazást, ugyanúgy, mint amikor helyileg futtatta.

>[AZURE.NOTE] Ha befejezte a tesztelést, törölje vagy állítsa le a felhőszolgáltatást. Akkor is keletkezhetnek költségek, ha nem használja a felhőszolgáltatást, mivel virtuálisgép-erőforrások vannak lefoglalva számára. Ha hagyja, hogy továbbra is fusson, bárki, aki rátalál az URL-címére, létrehozhat és megtekinthet hirdetéseket. A [klasszikus Azure portálon](http://manage.windowsazure.com) lépjen a felhőszolgáltatás **Irányítópult** lapjára, és kattintson a lap alján található **Törlés** gombra. Ha csak ideiglenesen szeretné megakadályozni, hogy mások hozzáférjenek a webhelyhez, kattintson a **Leállítás** gombra. Ebben az esetben továbbra is keletkeznek költségek. Hasonló eljárással törölheti az SQL-adatbázist és a tárfiókot, ha már nincs szüksége rájuk.

## Teljesen új alkalmazás létrehozása

Ha még nem töltötte le a [kész alkalmazást](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4), most tegye meg. Fájlokat fog átmásolni a letöltött projektből az új projektbe.

A Contoso Ads alkalmazás létrehozása az alábbi lépésekből áll:

* Hozzon létre egy Visual Studio felhőszolgáltatás-megoldást.
* Frissítse és adja hozzá a NuGet-csomagokat.
* Állítsa be a projekt hivatkozásait.
* Konfigurálja a kapcsolati karakterláncokat.
* Adja hozzá a kódfájlokat.

A megoldás létrehozása után áttekinti a felhőszolgáltatás-projektekre nézve egyedi kódot, valamint az Azure-blobokat és üzenetsorokat.

### Visual Studio felhőszolgáltatás-megoldás létrehozása

1. A Visual Studio **Fájl** menüjében válassza az **Új projekt** lehetőséget.

2. Az **Új projekt** párbeszédpanel bal oldali ablaktáblájában bontsa ki a **Visual C#** elemet, és válassza ki a **Felhő** sablonokat, majd az **Azure Cloud Service** sablont.

3. Adja a ContosoAdsCloudService nevet a projektnek és a megoldásnak, majd kattintson az **OK** gombra.

    ![Új projekt](./media/cloud-services-dotnet-get-started/newproject.png)

4. Az **Új Azure Cloud Service** párbeszédpanelen adjon hozzá egy webes és egy feldolgozói szerepkört. A webes szerepkörnek adja a ContosoAdsWeb, a feldolgozói szerepkörnek pedig a ContosoAdsWorker nevet. (A szerepkörök alapértelmezett nevének módosításához használja a jobb oldali ablaktáblában található ceruza ikont.)

    ![Új Cloud Service-projekt](./media/cloud-services-dotnet-get-started/newcsproj.png)

5. Amikor megjelenik a webes szerepkör **Új ASP.NET-projekt** párbeszédpanele, válassza az MVC-sablont, majd kattintson a **Hitelesítés módosítása** elemre.

    ![Hitelesítés módosítása](./media/cloud-services-dotnet-get-started/chgauth.png)

7. A **Change Authentication** (Hitelesítés módosítása) párbeszédpanelen kattintson a **No Authentication** (Nincs hitelesítés) elemre, majd az **OK** gombra.

    ![Nincs hitelesítés](./media/cloud-services-dotnet-get-started/noauth.png)

8. A **New ASP.NET Project** (Új ASP.NET-projekt) párbeszédpanelen kattintson az **OK** gombra.

9. A **Megoldáskezelőben** kattintson a jobb gombbal a megoldásra (ne a projektek egyikére), és válassza az **Add - New Project** (Hozzáadás – Új projekt) lehetőséget.

11. Az **Add New Project** (Új projekt hozzáadása) párbeszédpanelen válassza a bal oldali ablaktáblában lévő **Visual C#** elem alatt található **Windows** lehetőséget, majd kattintson az **Class Library** (Osztálytár) sablonra.  

10. Adja a *ContosoAdsCommon* nevet a projektnek, majd kattintson az **OK** gombra.

    A webes és a feldolgozói szerepkörből is hivatkoznia kell az Entity Framework-környezetre és az adatmodellre. Alternatív megoldásként definiálhatja az EF-hez kapcsolódó osztályokat a webesszerepkör-projektben, majd hivatkozhat erre a projektre a feldolgozói szerepkörből. Az alternatív megoldás részeként azonban a feldolgozói szerepkör projekt egy olyan webes szerelvényre is hivatkozni fog, amelyre nincs szüksége.

### NuGet-csomagok frissítése és hozzáadása

1. Nyissa meg a megoldáshoz tartozó **Manage NuGet Packages** (NuGet-csomagok kezelése) párbeszédpanelt.

2. Az ablak tetején válassza az **Updates** (Frissítések) elemet.

3. Keresse meg a *WindowsAzure.Storage* csomagot, és ha szerepel a listában, jelölje ki, majd válassza ki, hogy melyik webes és feldolgozói projektben kívánja frissíteni, és végül kattintson az **Update** (Frissítés) gombra.

    A Storage ügyféloldali kódtár gyakrabban frissül, mint a Visual Studio projektsablonjai, így gyakran tapasztalhatja, hogy egy újonnan létrehozott projektben lévő verziót frissíteni kell.

4. Az ablak tetején válassza a **Browse** (Tallózás) elemet.

5. Keresse meg az *EntityFramework* NuGet-csomagot, majd telepítse mind a három projektben.

6. Keresse meg a *Microsoft.WindowsAzure.ConfigurationManager* NuGet-csomagot, majd telepítse a feldolgozói szerepkör projektben.

### A projekt hivatkozásainak beállítása

1. A ContosoAdsWeb projektben állítson be egy hivatkozást a ContosoAdsCommon projektre. Kattintson a jobb gombbal a ContosoAdsWeb projektre, majd kattintson a **References** - **Add References** (Hivatkozások, Hivatkozások hozzáadása) elemre. A **Hivatkozáskezelő** párbeszédpanelen válassza a bal oldali ablaktáblában lévő **Megoldás – Projektek**, majd a **ContosoAdsCommon** lehetőséget, és végül kattintson az **OK** gombra.

2. A ContosoAdsWorker projektben állítson be egy hivatkozást a ContosAdsCommon projektre.

    A ContosoAdsCommon tartalmazza az Entity Framework-adatmodellt és a környezeti osztályt, amelyet az elő- és a háttéralkalmazás egyaránt használ.

3. A ContosoAdsWorker projektben állítson be egy hivatkozást a következőre: `System.Drawing`.

    Ezt a szerelvényt a háttéralkalmazás használja a képek miniatűrökké való átalakításához.

### Csatlakozási karakterláncok konfigurálása

Ebben a szakaszban Azure Storage- és SQL-kapcsolati karakterláncokat fog konfigurálni helyi tesztelés céljából. Az oktatóanyag korábbi telepítési utasításai ismertetik a kapcsolati karakterláncok beállításának módját, amikor az alkalmazás a felhőben fut.

1. A ContosoAdsWeb projektben nyissa meg az alkalmazás Web.config fájlját, és illessze be a következő `connectionStrings` elemet a `configSections` elem után.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    A Visual Studio 2015 használata esetén helyettesítse a „v11.0” elemet az „MSSQLLocalDB” elemmel.

2. Mentse a módosításokat.

3. A ContosoAdsCloudService projektben a **Szerepkörök** területen kattintson a jobb gombbal a ContosoAdsWeb elemre, majd kattintson a **Tulajdonságok** elemre.

    ![Szerepkör tulajdonságai](./media/cloud-services-dotnet-get-started/roleproperties.png)

4. A **ConosoAdsWeb [Szerepkör]** tulajdonságok ablakában kattintson a **Beállítások** lapra, majd kattintson a **Beállítás hozzáadása** elemre.

    A **Service Configuration** (Szolgáltatáskonfiguráció) **All Configurations** (Minden konfiguráció) értékét ne módosítsa.

5. Adjon hozzá egy új, *StorageConnectionString* névvel ellátott beállítást. A **Típus** beállítása legyen *ConnectionString*, az **Érték** beállítása pedig *UseDevelopmentStorage=true*.

    ![Új kapcsolati karakterlánc](./media/cloud-services-dotnet-get-started/scall.png)

6. Mentse a módosításokat.

7. Kövesse ugyanezt az eljárást egy tárolási kapcsolati karakterlánc hozzáadásához a ContosoAdsWeb szerepkör tulajdonságaihoz.

8. A **ContosoAdsWorker [Szerepkör]** tulajdonságai ablakban maradva adjon hozzá egy másik kapcsolati karakterláncot:

    * Név: ContosoAdsDbConnectionString
    * Típus: Karakterlánc
    * Érték: Illessze be ugyanazt a kapcsolati karakterláncot, amelyet a webes szerepkör projekt esetében használt. (Az alábbi példa a Visual Studio 2013 kiadásra vonatkozik; ne feledje módosítani az Adatforrást, ha ezt a példát a Visual Studio 2015 használata mellett követi.)

        ```
        Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
        ```

### Kódfájlok hozzáadása

Ebben a szakaszban kódfájlokat fog átmásolni a letöltött megoldásból az új megoldásba. A következő szakaszok bemutatják és ismertetik a kód legfontosabb részeit.

Fájlok hozzáadásához egy projekthez vagy mappához kattintson a jobb gombbal a projektre vagy a mappára, majd kattintson a **Hozzáadás** - **Létező elem** lehetőségre. Jelölje ki a kívánt fájlokat, majd kattintson az **Add** (Hozzáadás) gombra. Ha a rendszer rákérdez, hogy lecseréli-e a meglévő fájlokat, kattintson a **Yes** (Igen) gombra.

3. A ContosoAdsCommon projektben törölje a *Class1.cs* fájlt, és a helyére illessze be az *Ad.cs* és a *ContosoAdscontext.cs* fájlt a letöltött projektből.

3. A ContosoAdsWeb projektben adja hozzá az alábbi fájlokat a letöltött projektből.
    - *Global.asax.cs*.  
    - A *Views\Shared* mappában: *\_Layout.cshtml*.
    - A *Views\Home* mappában: *Layout.cshtml*.
    - A *Controllers* mappában: *AdController.cs*.
    - A*Views\Ad* mappában (először hozza létre a mappát): öt *.cshtml* fájl.

3. A ContosoAdsWorker projektben adja hozzá a *WorkerRole.cs* fájlt a letöltött projektből.

Most már létrehozhatja és futtathatja az alkalmazást az oktatóanyag korábbi utasításai szerint, amely a helyi adatbázist és a Storage Emulator erőforrásait fogja használni.

Az alábbi szakaszok az Azure-környezetek, -blobok és -üzenetsorok használatával kapcsolatos kódot ismerteti. A jelen oktatóanyag nem tartalmazza az MVC-vezérlők és nézetek szerkezet használatával történő létrehozásának vagy az SQL Server-adatbázisokkal együttműködő Entity Framework-kód megírásának módját, illetve az ASP.NET 4.5-ben való aszinkron programozás alapjait. Ezen témakörökről az alábbi forrásanyagokban talál információt:

* [Bevezetés az MVC 5 használatába](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Bevezetés az EF 6 és az MVC 5 használatába](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Bevezetés az aszinkron programozásba a .NET 4.5 rendszerben](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### ContosoAdsCommon – Ad.cs

Az Ad.cs fájl megad egy felsorolást a kategóriákhoz és egy POCO entitásosztályt a hirdetés információihoz.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### ContosoAdsCommon – ContosoAdsContext.cs

A ContosoAdsContext osztály megadja, hogy az Ad osztály egy DbSet gyűjteményben lesz használva, amelyet az Entity Framework egy SQL-adatbázisban tárol.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

Az osztály két konstruktorral rendelkezik. Az elsőt a webes projekt használja, és a Web.config fájlban tárolt kapcsolati karakterlánc nevét adja meg. A második konstruktor lehetővé teszi a tényleges kapcsolati karakterlánc megadását. Erre a feldolgozói szerepkör-projektnek van szüksége, mivel nem rendelkezik Web.config fájllal. Korábban már látta a kapcsolati karakterlánc tárolásának helyét, a későbbiekben pedig láthatja, hogyan kérdezi le a kód a kapcsolati karakterláncot, amikor elindítja a DbContext osztályt.

### ContosoAdsWeb – Global.asax.cs

Az `Application_Start` metódusból meghívott kód létrehoz egy *képek* blobtárolót és egy *képek* üzenetsort, amennyiben még nem léteznek. Ez biztosítja, hogy valahányszor új tárfiókot kezd használni, vagy egy új számítógépen használja a Storage Emulatort, a szükséges blobtároló és üzenetsor automatikusan létrejöjjön.

A kód a *.cscfg*-fájlból származó tárolási kapcsolati karakterlánc használatával fér hozzá a tárfiókhoz.

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

Ezután hivatkozást kap a *képek* blobtárolóhoz, létrehozza a tárolót, ha az még nem létezik, és beállítja a hozzáférési engedélyeket az új tárolóhoz. Alapértelmezés szerint az új tárolók csak tárfiók-hitelesítő adatokkal rendelkező ügyfeleknek engedélyezik a blobokhoz való hozzáférést. A webhely szempontjából szükséges, hogy a blobok nyilvánosak legyenek, hogy képeket jeleníthessen meg a képblobokra mutató URL-címek használatával.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Egy hasonló kód a *képek* üzenetsor hivatkozását szerzi be, majd létrehoz egy új üzenetsort. Ebben az esetben nincs szükség az engedélyek módosítására.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### ContosoAdsWeb – \_Layout.cshtml

A *_Layout.cshtml* fájl beállítja az alkalmazás nevét a fejlécben és a láblécben, és létrehoz egy „Ads” menübejegyzést.

### ContosoAdsWeb – Views\Home\Index.cshtml

A *Views\Home\Index.cshtml* fájl kategóriahivatkozásokat jelenít meg a kezdőlapon. A hivatkozások átadják a `Category` felsorolás egy lekérdezési karakterlánc változóban lévő egész számú értékét az Ads indexlapnak.

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### ContosoAdsWeb – AdController.cs

Az *AdController.cs* fájlban lévő konstruktor meghívja az `InitializeStorage` metódust az Azure Storage ügyfélkódtár objektumainak létrehozásához, amelyek egy API-t biztosítanak a blobok és az üzenetsorok használatához.

Ezután a kód lekér egy hivatkozást a *képek* blobtárolóra, ahogy azt korábban a *Global.asax.cs* esetében is láthatta. Mindeközben beállít egy webalkalmazásokhoz használható alapértelmezett [újrapróbálkozási házirendet](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling). Az alapértelmezett exponenciális leállítási újrapróbálkozási házirend egy átmeneti hiba miatti ismételt próbálkozás esetén egy percnél hosszabb időre állíthatja le a webalkalmazást. Az itt megadott újrapróbálkozási házirend minden próbálkozás után 3 másodpercet vár, legfeljebb három alkalommal.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Egy hasonló kód a *képek* üzenetsorra kér le hivatkozást.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

A vezérlőkód nagy része jellemzően egy DbContext osztályt használó Entity Framework-adatmodellel történő használatra való. Kivétel ez alól a HttpPost `Create` metódus, amely feltölti és blobtárolóba menti a fájlokat. A modellkötő [HttpPostedFileBase](http://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx) objektumot biztosít a metódus számára.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Ha a felhasználó feltöltésre jelöl ki egy fájlt, a kód feltölti és egy blobba menti azt, majd frissíti a Hirdetés adatbázisrekordot a blobra mutató URL-címmel.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

A feltöltést végző kód az `UploadAndSaveBlobAsync` metódusban található. Létrehoz egy GUID nevet a blob számára, feltölti és menti a fájlt, majd visszaad egy hivatkozást a mentett blobra.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

Miután a HttpPost `Create` metódus feltölt egy blobot és frissíti az adatbázist, létrehoz egy üzenetsor-üzenetet, amely tájékoztatja a háttérfolyamatot arról, hogy egy kép készen áll a miniatűrré való átalakításra.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

A HttpPost `Edit` metódus kódja is hasonló, azzal a különbséggel, hogy ha a felhasználó egy új képfájlt jelöl ki, minden létező blobot törölni kell.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

A következő példa bemutatja a kódot, amely a hirdetés törlésekor törli a blobokat.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### ContosoAdsWeb – Views\Ad\Index.cshtml és Details.cshtml

Az *Index.cshtml* fájl a hirdetés többi adatát tartalmazó miniatűrt jelenít meg.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

A *Details.cshtml* fájl a teljes méretű képet jeleníti meg.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### ContosoAdsWeb – Views\Ad\Create.cshtml és Edit.cshtml

A *Create.cshtml* és az *Edit.cshtml* fájlok megadják az űrlap kódolását, amely lehetővé teszi a vezérlő számára a `HttpPostedFileBase` objektum lekérését.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Egy `<input>` elem jelzi a böngészőnek, hogy biztosítson egy fájlkiválasztási párbeszédpanelt.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### ContosoAdsWorker – WorkerRole.cs – OnStart metódus

A feldolgozói szerepkör indulásakor az Azure feldolgozóiszerepkör-környezet meghívja a `WorkerRole` osztályban lévő `OnStart` metódust, az `OnStart` metódus befejezésekor pedig a `Run` metódust.

Az `OnStart` metódus lekéri az adatbázis-kapcsolati karakterláncot a *.cscfg*-fájlból, és átadja az Entity Framework DbContext osztálynak. Alapértelmezés szerint az SQLClient szolgáltató van használatban, így azt nem kell megadni.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Ezután a metódus lekér egy hivatkozást a tárfiókra, valamint létrehozza a blobtárolót és az üzenetsort, ha azok még nem léteznek. Az ehhez tartozó kód hasonló ahhoz, amelyet a webes szerepkör `Application_Start` metódusában látott.

### ContosoAdsWorker – WorkerRole.cs – Run metódus

A `Run` metódus az `OnStart` metódus inicializálási feladatának befejezése után lesz meghívva. A metódus elindít egy végtelen ciklust, amely az üzenetsor új üzeneteit figyeli, és a beérkezésükkor feldolgozza azokat.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Ha a ciklus egyes ismétlései után nem található üzenet, a program egy másodpercre alvó állapotba vált. Ez megakadályozza, hogy a feldolgozói szerepkör túl sok CPU-időt használjon és további tárolási tranzakciós költségeket halmozzon fel. A Microsoft ügyféltanácsadói csapatának van egy története egy fejlesztőről, aki ezt elfelejtette beépíteni, elvégezte az éles környezetbe való telepítést, azután elment nyaralni. Mire visszatért, a figyelmetlensége többe került, mint az egész nyaralás.

Néha előfordul, hogy az üzenetsor egyik üzenetének tartalma feldolgozási hibát okoz. Az ilyet *ártalmas üzenetnek* nevezik, és ha épp naplózott egy hibát, majd újraindította a ciklust, akkor az üzenet feldolgozásával a végtelenségig próbálkozhat.  Ezért a catch blokk tartalmaz egy if utasítást, amely ellenőrzi, hogy az alkalmazás hány alkalommal próbálta feldolgozni az aktuális üzenetet, és amennyiben több mint 5 alkalommal, az üzenet törlődik az üzenetsorból.

`ProcessQueueMessage` akkor lesz meghívva, ha az üzenetsorban található üzenet.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Ez a kód beolvassa az adatbázist a kép URL-címének lekéréséhez, miniatűrré alakítja a képet, a miniatűrt egy blobba menti, frissíti az adatbázist a miniatűr blob URL-címével, és törli az üzenetsorban lévő üzenetet.

>[AZURE.NOTE] Az egyszerűség kedvéért a `ConvertImageToThumbnailJPG` metódusban lévő kód osztályokat alkalmaz a System.Drawing névtérben. A névtérben lévő osztályok viszont a Windows-űrlapokkal való használatra lettek tervezve. Windows- vagy ASP.NET-szolgáltatásban való használatuk nem támogatott. További információk a képfeldolgozási beállításokról: [Dynamic Image Generation](http://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) (Dinamikus képek létrehozása) és [Deep Inside Image Resizing](http://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na) (A képek átméretezésének részletei).

## Hibaelhárítás

Ha az oktatóanyag utasításainak követése ellenére valami mégsem működne, íme néhány gyakran előforduló hiba és azok megoldása.

### ServiceRuntime.RoleEnvironmentException

A `RoleEnvironment` objektumot az Azure biztosítja az alkalmazás Azure-ban való futtatásakor, vagy az Azure Compute Emulator használatával történő helyi futtatáskor.  Ha a helyi futtatás során ez a hiba jelenik meg, ellenőrizze, hogy a ContosoAdsCloudService projektet állította-e be kiindulási projektként. Beállítja a projektet, hogy az Azure Compute Emulator használatával fusson.

Az alkalmazás többek között a *.cscfg*-fájlokban tárolt kapcsolati karakterlánc-értékek lekérésére használja az Azure RoleEnvironment-et, ezért a kivétel egy másik oka egy hiányzó kapcsolati karakterlánc. Győződjön meg arról, hogy a ContosoAdsWeb projekt Felhő- és a Helyi konfigurációiban is létrehozta a StorageConnectionString beállítást, illetve arról is, hogy a ContosoAdsWorker projekt mindkét konfigurációjában létrehozta mindkét kapcsolati karakterláncot. Ha a **Find All** (Összes keresése) funkció használatával keres a StorageConnectionString kifejezésre a megoldás egészében, 6 fájlban, 9 alkalommal kell megjelennie.

### A felülbírálás nem alkalmazható a(z) xxx portra. Az új port a HTTP protokoll esetében megengedett legalacsonyabb, 8080 érték alatt van

Próbálja módosítani a webes projekt által használt port számát. Kattintson a jobb gombbal a ContosoAdsWeb projektre, majd kattintson a **Properties** (Tulajdonságok) elemre. Kattintson a **Web** lapra, majd módosítsa a port számát a **Projekt URL-címe** beállításban.

A probléma megoldására irányuló alternatív megoldásért tekintse meg a következő szakaszt.

### A helyi futtatás során felmerülő egyéb hibák

Alapértelmezés szerint az új felhőszolgáltatás-projektek az Azure Compute Emulator Express használatával szimulálják az Azure-környezetet. Ez a teljes Compute Emulator egyszerűsített verziója, és bizonyos körülmények között előfordulhat, hogy a teljes emulátor akkor is működik, amikor az Express verzió nem.  

Ha a teljes emulátor használatára szeretné módosítani a projekt beállítását, kattintson a jobb gombbal a ContosoAdsCloudService projektre, majd kattintson a **Tulajdonságok** lehetőségre. A **Tulajdonságok** ablakban kattintson a **Web** lapra, majd kattintson a **Use Full Emulator** (Teljes emulátor használata) választógombra.

Az alkalmazás teljes emulátorral való futtatásához rendszergazdai jogosultságokkal kell megnyitnia a Visual Studiót.

## Következő lépések

A Contoso Ads alkalmazás kialakítása szándékosan egyszerű az első lépéseket ismertető oktatóanyag kedvéért. Nem valósítja meg például a [függőségi beszúrást](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) vagy a [működési minták adattárát és egységét](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), nem [használ felületet a naplózáshoz](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), nem használ [EF Code First áttelepítést](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) az adatmodellek kezeléséhez vagy [EF-kapcsolati rugalmasságot](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) az átmeneti hálózati hibák kezeléséhez stb.

Az alábbiakban példákat talál felhőszolgáltatás-alkalmazásokra, amelyek több valós kódolási gyakorlatot mutatnak be az egyszerűbbektől az összetettebbekig:

* [PhluffyFotos](http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Az elve hasonló a Contoso Ads elvéhez, de több funkciót és valós kódolási gyakorlatot alkalmaz.
* [Azure Cloud Service Multi-Tier Application with Tables, Queues, and Blobs](http://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36) (Többrétegű Azure-felhőszolgáltatás táblákkal, üzenetsorokkal és blobokkal). Ismerteti az Azure Storage-táblákat, valamint a blobokat és üzenetsorokat. Az Azure SDK for .NET egy korábbi verzióján alapul. Az aktuális verzióval való használathoz néhány módosítást kell végrehajtani.
* [Cloud Service Fundamentals in Microsoft Azure](http://code.msdn.microsoft.com/Cloud-Service-Fundamentals-4ca72649) (A Felhőszolgáltatás alapjai a Microsoft Azure-ban). A Microsoft Minták és gyakorlatok csoportja által létrehozott, ajánlott eljárások széles skáláját bemutató, átfogó példák.

Általános információk a felhőalapú fejlesztésről: [Building Real-World Cloud Apps with Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction) (Valódi felhőalapú alkalmazások létrehozása az Azure használatával).

Bemutató videó az Azure Storage ajánlott eljárásairól és mintáiról: [Microsoft Azure Storage – What's New, Best Practices and Patterns](http://channel9.msdn.com/Events/Build/2014/3-628) (Microsoft Azure Storage – Újdonságok, ajánlott eljárások és minták).

További információkért lásd a következőket:

* [Azure Cloud Services – 1. rész: Bevezetés](http://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [A Cloud Services kezelése](cloud-services-how-to-manage.md)
* [Azure Storage](/documentation/services/storage/)
* [Felhőszolgáltató kiválasztása](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)



<!--HONumber=sep16_HO1-->


