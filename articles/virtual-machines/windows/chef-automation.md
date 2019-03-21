---
title: Az Azure virtuális gép üzembe helyezése a Cheffel |} A Microsoft Docs
description: Ismerje meg, hogyan használható a Chef ehhez az automatikus virtuális gép központi telepítése és konfigurálása a Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: jeconnoc
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: a973d8dbab18e9ea66afb5ffff83f47c3ad98f93
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012346"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Azure-beli virtuális gépek üzembe helyezése a Cheffel
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef egy nagyszerű eszköz automation kidolgozását és a kívánt állapot konfigurációk.

A legújabb felhőalapú API-verzió Chef biztosít zökkenőmentes integráció az Azure-ral, így és üzembe helyezéséhez konfigurációs állapotnak egyetlen parancs használatával.

Ebben a cikkben, állítsa be a Chef környezetet az Azure-beli virtuális gépek kiépítése, és haladjon végig egy házirend vagy az "Útmutató" létrehozása és üzembe kell helyezni a útmutató Azure virtuális gépeken.

## <a name="chef-basics"></a>Chef alapjai
Mielőtt elkezdené, [tekintse át az alapvető fogalmait, Chef](https://www.chef.io/chef).

A következő ábra szemlélteti a Chef architektúrájának áttekintése.

![][2]

Chef három fő architekturális összetevőre van: Chef-kiszolgálót, a Chef ügyfél (node) és a Chef munkaállomás.

A Chef-kiszolgálót a felügyeleti pont és a Chef kiszolgáló két lehetőség van: üzemeltetett megoldás vagy egy helyszíni megoldás.

A Chef ügyfél (node), akkor az ügynök, amely a kezelt kiszolgálókon.

A Chef munkaállomás, amelynek a neve, mind a rendszergazdai munkaállomás, amelyen szabályzatokat hozhat létre, és hajtsa végre a felügyeleti parancsok és a Chef eszközök szoftvercsomagot.

Általában a láthatja _a munkaállomáson_ , a hely, ahol műveleteket végre és _Chef munkaállomás_ a szoftvercsomag.
Például, töltse le a Kés parancs részeként _Chef munkaállomás_, de kés parancsok futtatása _a munkaállomáson_ infrastruktúra kezeléséhez.

Chef is használja az "Kézikönyvek" és "Receptek", ami lényegében azt határozza meg, és a kiszolgálók a alkalmazni a házirendeket.

## <a name="preparing-your-workstation"></a>A munkaállomás előkészítése

A munkaállomás először-előkészítési Chef konfigurációs fájlokat és kézikönyvek könyvtárat hoz létre.

Hozzon létre egy C:\chef nevű könyvtárat.

Az Azure PowerShell letöltése [nastavení publikování](https://docs.microsoft.com/dynamics-nav/how-to--download-and-import-publish-settings-and-subscription-information).

## <a name="setup-chef-server"></a>Chef-kiszolgáló beállítása

Ez az útmutató feltételezi, hogy lesz regisztrál üzemeltetett Chef.

Ha még nem használja a Chef-kiszolgálót, akkor a következőket teheti:

* Regisztráljon [üzemeltetett Chef](https://manage.chef.io/signup), ez az első lépések a Chef leggyorsabb módja.
* Chef Server önálló telepítése linux-alapú gépen, a következő a [telepítési utasításokat](https://docs.chef.io/install_server.html) a [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Üzemeltetett Chef-fiók létrehozása

A Chef üzemeltetett fiók [Itt](https://manage.chef.io/signup).

A regisztrációs folyamat során a rendszer felkéri, hozzon létre új szervezetet.

![][3]

A szervezet létrehozása után töltse le a starter kit.

![][4]

> [!NOTE]
> Ha megjelenik egy figyelmeztetés, hogy a kulcsokat a rendszer visszaállítja kérdés, nem folytatható, mert nincs még konfigurálva meglévő infrastruktúrára van probléma.
>

Az alapszintű csomag zip-fájl tartalmazza a szervezet konfigurációs fájlokat és a felhasználói kulcsot a `.chef` könyvtár.

A `organization-validator.pem` le kell tölteni külön-külön, mert a titkos kulcsot és titkos kulcsok nem kell tárolni a Chef-kiszolgálót. A [Chef kezelése](https://manage.chef.io/) , és válassza ki a "Alaphelyzetbe érvényesítési kulcs", amely külön letölthető egy fájl biztosít. Mentse a fájlt c:\chef.

### <a name="configuring-your-chef-workstation"></a>A Chef munkaállomás konfigurálása

Bontsa ki a chef-starter.zip c:\chef a tartalmát.

Chef-starter\chef-adattárban található összes fájlt másolja\.chef a c:\chef címtárhoz.

Másolás a `organization-validator.pem` c:\chef, ha a Mentés c:\Downloads fájlt

A címtár most hasonlóan kell kinéznie a következő példa.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Most már öt fájlt, és négy könyvtárak (beleértve az üres chef-tárház könyvtár) c:\chef gyökerében.

### <a name="edit-kniferb"></a>Knife.rb szerkesztése

A PEM-fájlok tartalmazzák a szervezet és a kommunikációhoz felügyeleti titkos kulcsok és a knife.rb fájl tartalmazza a Kés konfigurációját. Szükségünk lesz a knife.rb fájl szerkesztéséhez.

Tetszőleges szövegszerkesztőben nyissa meg a knife.rb fájlt. A változatlan fájlt a következőhöz hasonlóan kell kinéznie:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

A knife.rb adja hozzá a következő információkat:

"myorg-érvényesítő" validation_client_name validation_key "" #{current_dir}/myorg.pem"

Emellett adja hozzá a következő sor neve az Azure-hoz továbbítható közzétételi beállítások fájljában.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Módosítsa a "cookbook_path" eltávolításával a /... / elérési útja, így jelenik meg:

    cookbook_path  ["#{current_dir}/cookbooks"]

Ezek a sorok biztosítja kés c:\chef\cookbooks kézikönyvek almappájába hivatkozik, és az Azure közzétételi beállítások fájlját is használja az Azure műveletek során.

A knife.rb fájl most a következő példához hasonlóan kell kinéznie:

![][6]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
knife.rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "mynode"
client_key               "#{current_dir}/user.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
validation_client_name   "myorg-validator"
validation_key           ""#{current_dir}/myorg.pem"
cookbook_path            ["#{current_dir}/cookbooks"]
knife[:azure_publish_settings_file] = "yourfilename.publishsettings"
```

## <a name="install-chef-workstation"></a>Telepítse a Chef munkaállomás

Ezután [töltse le és telepítse](https://downloads.chef.io/chef-workstation/) Chef munkaállomás.
Telepítse a Chef munkaállomás alapértelmezett helye. Ez a telepítés néhány percet is igénybe vehet.

Az asztalon látni fog egy "tényleges súly PowerShell", amely betölti az eszközzel kell a Chef termékek folytatott interakcióra szolgáló környezet. A tényleges súly PowerShell elérhetővé új ad-hoc parancsokat, mint például `chef-run` , valamint hagyományos Chef CLI-parancsok, például `chef`. Tekintse meg a Chef munkaállomás és a Chef eszközökkel telepített verziója `chef -v`. A munkaállomás-verzió "Kapcsolatos Chef-munkaállomás" kiválasztásával a Chef munkaállomás alkalmazásból is ellenőrizheti.

`chef --version` térjen vissza az alábbihoz hasonló:

```
Chef Workstation: 0.2.29
  chef-run: 0.2.2
  Chef Client: 14.6.47x
  delivery-cli: master (6862f27aba89109a9630f0b6c6798efec56b4efe)
  berks: 7.0.6
  test-kitchen: 1.23.2
  inspec: 3.0.12
```

> [!NOTE]
> Az elérési utat a sorrend fontos szerepet játszanak. Ha a opscode elérési utak nem a megfelelő sorrendben kell problémákat.
>

Indítsa újra a munkaállomáson, a folytatás előtt.

### <a name="install-knife-azure"></a>Kés Azure telepítése

Ez az oktatóanyag feltételezi, hogy az Azure Resource Manager használ a virtuális gép kommunikál.

A Kés Azure-bővítmény telepítése. Ez biztosítja, hogy az "Azure beépülő modullal" Kés.

Futtassa a következő parancsot.

    chef gem install knife-azure ––pre

> [!NOTE]
> A – előtti argumentum biztosítja, hogy azért küldtük Önnek, a Kés Azure beépülő modult a legújabb API-k készlete hozzáférést biztosító RC legújabb verzióját.
>
>

Valószínű, hogy több függőséggel is települ egy időben.

![][8]

Annak érdekében, hogy minden helyesen van-e konfigurálva, futtassa a következő parancsot.

    knife azure image list

Ha minden megfelelően konfigurálva, látni fogja az elérhető Azure-rendszerképek kezdje el végiggörgetni listáját.

Gratulálunk! A munkaállomás be van állítva.

## <a name="creating-a-cookbook"></a>Egy könyv létrehozása

Egy könyv meghatározásához hajtsa végre a felügyelt ügyfél kívánt parancsokat Chef használják. Egy könyv létrehozása nagyon egyszerű, használja a **chef készítése cookbook** parancsot a könyv sablon létrehozásához. Ez az útmutató, amely automatikusan telepíti az IIS webkiszolgáló van.

A C:\Chef könyvtárban futtassa a következő parancsot.

    chef generate cookbook webserver

Ez a parancs létrehoz egy fájlok a könyvtárban C:\Chef\cookbooks\webserver. Ezt követően adja meg a felügyelt virtuális gépen hajtsa végre a Chef ügyfél parancsok készlete.

A parancsok a fájl default.rb vannak tárolva. Ebben a fájlban meghatározhatja egy adott parancsok, amely telepíti az IIS szolgáltatást, elindítja az IIS szolgáltatást, és egy sablon fájlt másolja a wwwroot mappája.

Módosítsa a C:\chef\cookbooks\webserver\recipes\default.rb fájlt, és adja hozzá a következő sorokat.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Ha elkészült, mentse a fájlt.

## <a name="creating-a-template"></a>Sablon létrehozása
Ebben a lépésben létre fog hozni egy sablon fájlt, mint a default.html oldal használt.

Futtassa a következő parancsot a sablont létrehozni:

    chef generate template webserver Default.htm

Keresse meg a `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` fájlt. Szerkessze a fájlt néhány egyszerű "Hello World" HTML-kód hozzáadásával, és mentse a fájlt.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Töltse fel az útmutató a Chef-kiszolgáló
Ebben a lépésben, győződjön meg arról, az útmutató egy példányát, amely a helyi gépen hozott létre, és töltse fel a Chef üzemeltetett kiszolgálóhoz. Feltöltése után a könyv alatt jelenik meg a **házirend** fülre.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Virtuális gép üzembe helyezése az Azure-ral Kés
Az Azure virtuális gép üzembe helyezése, és a alkalmazni az "Webkiszolgáló" Cookbook, amely telepíti az IIS szolgáltatást és az alapértelmezett webes weblap.

Használja ezt a **kés az azure-kiszolgáló létrehozása** parancsot.

Egy példát a parancs következő jelenik meg.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

A paraméterek magától értetődő. Helyettesítse be az adott változókat, és futtassa.

> [!NOTE]
> A parancssor használatával szeretnék vagyok is automatizálása a végpont hálózati Állapotszűrő szabályok – tcp-végpontok paraméter használatával. E megnyitott 80-as és a 3389-es, így biztosítva hozzáférést a saját webhelye és az RDP-munkamenetet.
>
>

A parancs futtatása után nyissa meg a gép kiépítésének megkezdéséhez tekintse meg az Azure Portalon.

![][13]

A parancssor használatával tovább jelenik meg.

![][10]

Az üzembe helyezés befejezése után, tudnia kell, 80-as porton keresztül csatlakozni a web Service, mert a virtuális gép a Kés Azure paranccsal helyezésekor nyitotta meg a portot. Mivel a virtuális gép csak a virtuális gép, a felhőalapú szolgáltatás, a csatlakozás a felhőalapú szolgáltatás URL-címet.

![][11]

Ebben a példában a creative HTML-kódot használ.

Ne felejtse el egy RDP-munkamenetet a 3389-es porton keresztül az Azure Portalon keresztül is csatlakozhat.

Köszönjük! Nyissa meg, és az infrastruktúra mint kód világába az Azure-ral holnapig!

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png


<!--Link references-->
