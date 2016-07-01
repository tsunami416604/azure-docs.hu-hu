<properties
    pageTitle="Python webes és feldolgozói szerepkörök a Python Tools 2.2 for Visual Studio eszközzel | Microsoft Azure"
    description="Azure felhőszolgáltatások, például webes és feldolgozói szerepkörök létrehozása a Python Tools for Visual Studio eszközzel."
    services="cloud-services"
    documentationCenter="python"
    authors="thraka"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/30/2015"
    ms.author="adegeo"/>




# Python webes és feldolgozói szerepkörök Python Tools 2.2 for Visual Studio eszközzel

Ez a cikk a Python webes és feldolgozói szerepkörök [Python Tools for Visual Studio][] eszközben történő használatát ismerteti.

## Előfeltételek

 - Visual Studio 2013 vagy 2015
 - [Python Tools 2.2 for Visual Studio][] (PTVS)
 - [Azure SDK Tools for VS 2013][] vagy [Azure SDK Tools for VS 2015][]
 - [Python 2.7 32-bit][] vagy [Python 3.4 32-bit][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Mik a Python webes és feldolgozói szerepkörök?

Az Azure három számítási modellt biztosít az alkalmazások futtatásához: [Web Apps szolgáltatás az Azure App Service portálon][futtatási modell-webhelyek], [Azure Virtual Machines][végrehajtási modell virtuális gépek] és [Azure Cloud Services][végrehajtási modell felhőszolgáltatások]. Mindhárom modell támogatja a Python eszközt. A webes és feldolgozói szerepköröket is tartalmazó Cloud Services *platformszolgáltatást (PaaS)* kínál. A felhőszolgáltatásban a webes szerepkör egy külön Internet Information Services (IIS) webkiszolgálót biztosít az előtéri webalkalmazásokhoz, míg a feldolgozói szerepkör aszinkron, hosszan futó vagy folyamatos feladatokat futtat függetlenül a felhasználói interakcióktól vagy bemenettől.

További információ: [Mi az a Cloud Service?].

> [AZURE.NOTE] *Egyszerű webhelyet szeretne készíteni?*
Ha egy egyszerű webhely előterét kívánja futtatni, fontolja meg az Azure App Service egyszerűsített Web Apps szolgáltatásának használatát. Könnyedén frissíthet Cloud Service szolgáltatásra, ha a webhely növekszik és a követelmények változnak. A <a href="/develop/python/">Python fejlesztői központban</a> találhat az Azure App Service Web Apps szolgáltatásának fejlesztését ismertető cikkeket.
<br />


## Projekt létrehozása

A Visual Studio eszközben válassza az **Azure Cloud Service** lehetőséget az **Új projekt** párbeszédpanelen a **Python** területen.

![Új projekt párbeszédpanel](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Az Azure Cloud Service varázslóban létrehozhat új webes és feldolgozói szerepköröket.

![Azure Cloud Service párbeszédpanel](./media/cloud-services-python-ptvs/new-service-wizard.png)

A feldolgozói szerepkör bolierplate kóddal rendelkezik, hogy csatlakozhasson egy Azure-tárfiókhoz vagy Azure Service Bus szolgáltatáshoz.

![Cloud Service megoldás](./media/cloud-services-python-ptvs/worker.png)

Egy meglévő felhőszolgáltatáshoz bármikor hozzáadhat webes vagy feldolgozói szerepköröket.  Hozzáadhat a megoldásában már meglévő projekteket, vagy létrehozhat újakat.

![Szerepkör hozzáadása parancs](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

A felhőszolgáltatás tartalmazhat különböző nyelveken kialakított szerepköröket.  Például kialakíthat egy Python webes szerepkört Django, Python vagy C# feldolgozói szerepkör használatával.  A szerepkörök között egyszerűen kommunikálhat Service Bus üzenetsorok vagy tárolási sorok használatával.

## Helyi futtatás

Ha a felhőszolgáltatási projektet állítja be kiindulási projektnek, és megnyomja az F5 billentyűt, a felhőszolgáltatás a helyi Azure emulátorban fog futni.

Bár a PVTS támogatja az indítást az emulátorban, a hibakeresés (például töréspontok keresése) nem fog működni.

A webes és feldolgozói szerepkörök hibakereséséhez állítsa be a szerepkörprojektet kiindulási projektként, és végezzen azon hibakeresést  Beállíthat több kiindulási projektet is.  Kattintson a jobb gombbal a megoldásra, majd válassza az **Indítási projektek beállítása** lehetőséget.

![A megoldás kiindulási projektjének tulajdonságai](./media/cloud-services-python-ptvs/startup.png)

## Közzététel az Azure platformon

A közzétételhez kattintson a jobb gombbal a felhőszolgáltatási projektre a megoldásban, majd kattintson a **Közzététel** gombra.

![Bejelentkezés a Microsoft Azure közzétételhez](./media/cloud-services-python-ptvs/publish-sign-in.png)

A beállítások lapon válassza ki a felhőszolgáltatást, amelyben közzé kívánja tenni a tartalmat.

![Microsoft Azure közzététel beállításai](./media/cloud-services-python-ptvs/publish-settings.png)

Létrehozhat egy új felhőszolgáltatást, ha nem rendelkezik még ilyennel.

![Cloud Service létrehozás párbeszédpanel](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Célszerű engedélyezni a távoli asztali kapcsolatokat a gép(ek)hez a hibakeresés érdekében.

![Távoli asztal beállítása párbeszédpanel](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

Ha elkészült a beállítások konfigurálásával, kattintson a **Közzététel** gombra.

Egy folyamatjelző jelenik meg a kimeneti ablakban, majd megnyílik a Microsoft Azure tevékenységnapló ablak.

![Microsoft Azure tevékenységnapló ablak](./media/cloud-services-python-ptvs/publish-activity-log.png)

A telepítés néhány percet vesz igénybe, ezután a webes és/vagy feldolgozói szerepkörök el is indulnak az Azure portálon!

## További lépések

A PVTS dokumentációban további információkat találhat a webes és feldolgozói szerepkörök használatáról Python Tools for Visual Studio eszközben:

- [Cloud Service projektek][]

Ha további részletekre kíváncsi az Azure szolgáltatások használatáról webes és feldolgozói szerepkörökből, mint az Azure Storage vagy a Service Bus, olvassa el a következő cikkeket.

- [Blob szolgáltatás][]
- [Table szolgáltatás][]
- [Queue szolgáltatás][]
- [Service Bus által kezelt üzenetsorok][]
- [Service Bus-üzenettémák][]


<!--Link references-->

[Mi az a Cloud Service?]: ./cloud-services/cloud-services-choose-me.md
[futtatási modell-webhelyek]: ./app-service-web/app-service-web-overview.md
[végrehajtási modell-virtuális gépek]: ./virtual-machines/virtual-machines-windows-about.md
[végrehajtási modell-felhőszolgáltatások]: ./cloud-services/cloud-services-choose-me.md
[Python fejlesztői központ]: /develop/python/

[Blob szolgáltatás]: ./storage/storage-python-how-to-use-blob-storage.md
[Queue szolgáltatás]: ./storage/storage-python-how-to-use-queue-storage.md
[Table szolgáltatás]: ./storage/storage-python-how-to-use-table-storage.md
[Service Bus által kezelt üzenetsorok]: ./service-bus/service-bus-python-how-to-use-queues.md
[Service Bus-üzenettémák]: ./service-bus/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio dokumentáció]: http://aka.ms/ptvsdocs
[Cloud Service projektek]: http://go.microsoft.com/fwlink/?LinkId=624028
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191



<!--HONumber=Jun16_HO2-->


