---
title: Gyorsútmutató – Windows-alapú virtuális gép konfigurálása az Azure-ban a Chef használatával
description: Ebben a rövid útmutatóban megtudhatja, hogyan telepíthet és konfigurálhat windowsos virtuális gépet az Azure-ban a Chef segítségével
keywords: szakács, azúr, devops, virtuális gép
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77590068"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Gyorsútmutató – Windows-alapú virtuális gép konfigurálása az Azure-ban a Chef használatával

Chef lehetővé teszi, hogy automatizálási és a kívánt állapotkonfigurációk.

A legújabb felhőalapú API-kiadással a Chef zökkenőmentes integrációt biztosít az Azure-ral, így egyetlen parancs segítségével üzembe helyezheti és üzembe helyezheti a konfigurációs állapotokat.

Ebben a cikkben beállíthatja a Chef-környezetet az Azure virtuális gépek üzembe helyezéséhez, és végigvezeti a szabályzat vagy szakácskönyv létrehozásán, majd a szakácskönyv üzembe helyezését egy Azure virtuális gépre.

## <a name="chef-basics"></a>A séf alapjai

Mielőtt elkezdené ezt a cikket, [tekintse át a Chef alapfogalmait.](https://www.chef.io/chef)

Az alábbi ábrán a magas szintű Chef architektúra látható.

![Chef építészet](media/chef-automation/chef-architecure.png)

Chef három fő építészeti összetevői: 
- Chef Server – A felügyeleti pont, és két lehetőség van a Chef Server: egy üzemeltetett megoldás vagy egy helyszíni megoldás.
- Chef-ügyfél (csomópont) – A jelenleg a jelenleg kezelendő kiszolgálókon található ügynök.
- Chef Workstation – A rendszergazdai munkaállomás (ahol házirendeket hoz létre és felügyeleti parancsokat futtat) és a Chef-eszközök szoftvercsomagjának neve.

Általában a **munkaállomás jelenik** meg a parancsok és a **szoftvercsomag Chef Workstation** futtatásának helyeként.

Például letölti a kés parancsot a **Chef Workstation**részeként, de az infrastruktúra kezeléséhez késparancsokat futtat **a munkaállomásról.**

Chef is használja a *fogalmakszakácskönyvek* és *receptek*. Ezek a feltételek a kiszolgálókra definiált és alkalmazott házirendek.

## <a name="preparing-your-workstation"></a>A munkaállomás előkészítése

Először készítse elő a munkaállomást egy könyvtár létrehozásával a Chef konfigurációs fájlok és szakácskönyvek tárolására.

Hozzon létre egy `C:\Chef` nevű könyvtárat.

Töltse le és telepítse a legújabb [Azure CLI-verziót](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a munkaállomására.

## <a name="configure-azure-service-principal"></a>Azure-szolgáltatásnév konfigurálása

Egy egyszerű szolgáltatássegítségével fogjuk létrehozni az Azure-erőforrásokat a Chef workstation-ből.  A megfelelő egyszerű szolgáltatás létrehozása a szükséges engedélyekkel, futtassa a következő parancsokat a PowerShellben:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Vegye figyelembe a SubscriptionID, TenantID, ClientID és Client Secret (a jelszó, amelykorábban az oktatóanyagban), ahogy szüksége lesz ezekre az értékekre. 

## <a name="setup-chef-server"></a>Telepítő chef-kiszolgáló

Ez az útmutató feltételezi, hogy feliratkozik a Hosted Chef.

Ha még nem használja a Chef servert, a következőket teheti:

* Iratkozzon fel a [Hosted Chef,](https://manage.chef.io/signup)amely a leggyorsabb módja annak, hogy az induláshoz a Chef.
* Telepítsen egy önálló Chef Server-t linux-alapú gépre a [Chef Docs](https://docs.chef.io/)telepítési [utasításait](https://docs.chef.io/install_server.html) követve.

### <a name="creating-a-hosted-chef-account"></a>Hosted Chef-fiók létrehozása

Regisztráljon egy Hosted Chef-fiókot [itt.](https://manage.chef.io/signup)

A regisztrációs folyamat során a rendszer új szervezet létrehozását kéri.

![Szervezeti ablak létrehozása](media/chef-automation/create-organization.png)

A szervezet létrehozása után töltse le az indítócsomagot.

![Chef konfigurálása](media/chef-automation/configure-chef.png)

> [!NOTE]
> Ha egy gyors figyelmeztetést kap, hogy a kulcsok alaphelyzetbe leszállítva, akkor rendben van, hogy folytassa, mivel még nincs meglévő infrastruktúra konfigurálva.
>

Ez a kezdőkészlet zip-fájl tartalmazza a szervezet `.chef` konfigurációs fájljait és a felhasználói kulcsot a könyvtárban.

A `organization-validator.pem` kell letölteni külön, mert ez egy személyes kulcs, és a személyes kulcsokat nem kell tárolni a Chef Server. A [Chef Manage területen](https://manage.chef.io/)lépjen be a Felügyelet szakaszba, és válassza az "Érvényesítési kulcs visszaállítása" lehetőséget, amely külön letölthető fájlt biztosít. Mentse a fájlt a c:\chef mappába.

### <a name="configuring-your-chef-workstation"></a>A Chef munkaállomás konfigurálása

Bontsa `chef-starter.zip` ki `c:\chef`a tartalmát.

Másolja az `chef-starter\chef-repo\.chef` összes `c:\chef` fájlt a könyvtárba.

Másolja `organization-validator.pem` a `c:\chef`fájlt a programba, `c:\Downloads`ha a fájl a programba van mentve.

A könyvtárnak most a következő példához hasonlóan kell kinéznie.

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

Most már öt fájlt és négy könyvtárat (beleértve az üres chef-repo könyvtár) a gyökér c:\chef.

### <a name="edit-kniferb"></a>Kés szerkesztése.rb

A PEM-fájlok tartalmazzák a szervezet és a rendszergazda személyes kulcsokat a kommunikáció és a knife.rb fájl tartalmazza a kés konfiguráció. Meg kell szerkesztenünk a knife.rb fájlt.

Nyissa meg a knife.rb fájlt az Ön által választott szerkesztőben. A változatlan fájlnak a következőre kell hasonlítania:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Adja hozzá a következő információkat a knife.rb, helyett a helyőrzők az Ön adatait:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Ezek a sorok biztosítják, hogy a `c:\chef\cookbooks`Knife hivatkozzon a szakácskönyvek könyvtárára a alatt.

A `knife.rb` fájlnak most az alábbi példához hasonlóan kell kinéznie:

![Példa késfájlra](./media/chef-automation/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Chef munkaállomás telepítése

Ezután [töltse le és telepítse a Chef Workstation programot.](https://downloads.chef.io/chef-workstation/)

Telepítse a Chef Workstation alkalmazást az alapértelmezett helyre.

Az asztalon megjelenik egy CW PowerShell. Ez az eszköz a Chef termékekkel való interakcióra szolgál. A CW PowerShell új parancsokat `chef-run` tesz elérhetővé, például chef CLI-parancsokat (például). `chef` Tekintse meg a Chef Workstation telepített `chef -v`verzióját és a Chef eszközöket a segítségével. A Munkaállomás verzióját úgy is ellenőrizheti, hogy a Chef Workstation alkalmazásban a Chef Workstation alkalmazás **Mintegy séfmunkaállomása** lehetőséget választja.

`chef --version`vissza kell térnie valami ilyesmi:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> Az út sorrendje fontos! Ha az opscode elérési útjai nem a megfelelő sorrendben vannak, problémák at fognak eredményezni.
>

A folytatás előtt indítsa újra a munkaállomást.

### <a name="install-knife-azure"></a>Kés esszense telepítése

Ez az oktatóanyag feltételezi, hogy az Azure Resource Manager használatával kommunikál a virtuális gép.

Telepítse a Knife Azure bővítményt, amely tartalmazza az Azure Plugin.

Futtassa a következő parancsot.

    chef gem install knife-azure ––pre

> [!NOTE]
> Az `–-pre` argumentum biztosítja, hogy megkapja a Knife Azure Plugin legújabb RC verzióját, amely hozzáférést biztosít a legújabb API-k hoz.
>
>

Valószínű, hogy egy sor függőségek is telepítve lesz egy időben.

![Kimenet a kés-azúrkék beszereléséből](./media/chef-automation/install-knife-azure.png)

Annak érdekében, hogy minden megfelelően legyen konfigurálva, futtassa a következő parancsot.

    knife azurerm server list

Ha minden megfelelően van konfigurálva, megjelenik az elérhető Azure-képek listája görgetés.

Gratulálunk! A munkaállomás készen áll!

## <a name="creating-a-cookbook"></a>Szakácskönyv létrehozása

A szakácskönyv a Chef segítségével határozza meg a felügyelt ügyfélen futtatni kívánt parancsokat. Létrehozása szakácskönyv egyszerű, csak `chef generate cookbook` használja a parancsot, hogy létrehoz a szakácskönyv sablont. Ez a szakácskönyv egy olyan webkiszolgálóhoz szolgál, amely automatikusan telepíti az IIS-t.

A `C:\Chef directory`területen futtassa a következő parancsot.

    chef generate cookbook webserver

Ez a parancs a C:\Chef\cookbooks\webserver könyvtár alatt fájlkészletet hoz létre. Ezután adja meg a Chef-ügyfél által a felügyelt virtuális gépen futtatandó parancsok készletét.

A parancsok at a default.rb fájl tárolja. Ebben a fájlban adjon meg olyan parancsokat, amelyek telepítik az IIS-t, elindítják az IIS-t, és átmásolnak egy sablonfájlt a `wwwroot` mappába.

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

Mentse a fájlt, ha elkészült.

## <a name="creating-a-template"></a>Sablon létrehozása

Ebben a lépésben létrehoz egy sablonfájlt, `default.html` amelyet lapként használhat.

A sablon létrehozásához futtassa a következő parancsot:

    chef generate template webserver Default.htm

Keresse meg `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` a fájlt. A fájlt szerkesztheti néhány egyszerű *Hello World* HTML-kód hozzáadásával, majd mentse a fájlt.

## <a name="upload-the-cookbook-to-the-chef-server"></a>A szakácskönyv feltöltése a Chef Server kiszolgálóra

Ebben a lépésben másolatot készít a helyi gépen létrehozott szakácskönyvről, és feltölti azt a Chef Hosted Server kiszolgálóra. A feltöltést követően a szakácskönyv megjelenik a **Szabályzat** fül alatt.

    knife cookbook upload webserver

![A szakácskönyv chef-kiszolgálóra történő telepítésének eredményei](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Virtuális gép üzembe helyezése a Knife Azure-ral

Üzembe helyezhet egy Azure `Webserver` virtuális gépet, `knife` és alkalmazza a szakácskönyvet a paranccsal.

A `knife` parancs az IIS webszolgáltatást és az alapértelmezett weblapot is telepíti.

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

A `knife` példa egy *Standard_DS2_v2* virtuális gépet hoz létre, amelyen a Windows Server 2016 telepítve van az USA nyugati régiójában. Módosítsa ezeket az értékeket az alkalmazás igényeinek megfelelően.

A parancs futtatása után keresse meg az Azure Portalon, hogy a gép megkezdi a kiépítése.

![Kiépített virtuális gép](./media/chef-automation/virtual-machine-being-provisioned.png)

A következő parancssor jelenik meg.

![Kés kimenet virtuális gép létrehozásakor](./media/chef-automation/knife-output-when-creating-vm.png)

A központi telepítés befejezése után megjelenik az új virtuális gép nyilvános IP-címe. Illessze be ezt az értéket egy webböngészőbe az új webhely megtekintéséhez. Amikor üzembe helyeztük a virtuális gépet, megnyitottuk a 80-as portot, így külsőleg is elérhetőnek kell lennie.   

![A virtuális gép tesztelése](./media/chef-automation/testing-the-virtual-machine.png)

Ez a példa kreatív HTML-kódot használ.

Megtekintheti a csomópont [chef-kezelése](https://manage.chef.io/)állapotát is. 

![A csomópont állapotának megtekintése](./media/chef-automation/viewing-node-status.png)

Ne felejtse el, hogy az Azure Portalról a 3389-es porton keresztül rdp-munkameneten keresztül is csatlakozhat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Chef az Azure-on](/azure/chef/)