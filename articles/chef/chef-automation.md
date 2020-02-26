---
title: Rövid útmutató – Windows rendszerű virtuális gép konfigurálása az Azure-ban Chef használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható a Chef egy Windows rendszerű virtuális gép üzembe helyezéséhez és konfigurálásához az Azure-ban
keywords: Chef, Azure, devops, virtuális gép
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590068"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Rövid útmutató – Windows rendszerű virtuális gép konfigurálása az Azure-ban Chef használatával

A Chef lehetővé teszi az automatizálást és a kívánt állapot konfigurációját.

A Cloud API legújabb kiadásával a Chef zökkenőmentesen integrálható az Azure-nal, és lehetővé teszi a konfigurációs állapotok egyetlen paranccsal történő üzembe helyezését és telepítését.

Ebben a cikkben a Chef-környezetet úgy állíthatja be, hogy kiépítse az Azure-beli virtuális gépeket, és végigvezeti a szabályzatok vagy a szakácskönyvek létrehozásán, majd üzembe helyezi a szakácskönyvet egy Azure-beli virtuális gépen.

## <a name="chef-basics"></a>Chef alapjai

A cikk megkezdése előtt [tekintse át a Chef alapvető fogalmait](https://www.chef.io/chef).

Az alábbi ábrán a magas szintű Chef architektúra látható.

![Chef-architektúra](media/chef-automation/chef-architecure.png)

A Chef három fő építészeti összetevővel rendelkezik: 
- Chef Server – a felügyeleti pont és a Chef-kiszolgáló két lehetősége van: egy üzemeltetett megoldás vagy egy helyszíni megoldás.
- Chef-ügyfél (node) – a felügyelt kiszolgálókon található ügynök.
- Chef Workstation – a felügyeleti munkaállomás neve (a házirendek létrehozásához és a felügyeleti parancsok futtatásához) és a Chef Tools szoftvercsomag.

Általában a **munkaállomást** úgy látja, hogy a szoftvercsomag számára parancsokat és **Chef-munkaállomást** futtat.

Például letöltheti a kés parancsot a **Chef munkaállomás**részeként, de a **munkaállomásról** származó kés parancsokat futtatva kezelheti az infrastruktúrát.

A Chef a *szakácskönyvek* és a *receptek*fogalmait is használja. Ezek a feltételek a kiszolgálókon definiált és alkalmazott szabályzatok.

## <a name="preparing-your-workstation"></a>A munkaállomás előkészítése

Először készítse elő a munkaállomást úgy, hogy létrehoz egy könyvtárat a Chef konfigurációs fájljainak és a szakácskönyveknek a tárolására.

Hozzon létre egy `C:\Chef` nevű könyvtárat.

Töltse le és telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) legújabb verzióját a munkaállomásra.

## <a name="configure-azure-service-principal"></a>Azure-szolgáltatásnév konfigurálása

Egy egyszerű szolgáltatásnév segítségével hozunk létre Azure-erőforrásokat a Chef munkaállomásán.  Ha a megfelelő egyszerű szolgáltatást a szükséges engedélyekkel szeretné létrehozni, futtassa a következő parancsokat a PowerShellen belül:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Jegyezze fel a SubscriptionID, a TenantID, a ClientID és az ügyfél titkát (az oktatóanyagban korábban megadott jelszót), mivel ezekre az értékekre szüksége lesz. 

## <a name="setup-chef-server"></a>A Chef-kiszolgáló beállítása

Ez az útmutató feltételezi, hogy regisztrálja az üzemeltetett Cheft.

Ha még nem használ Chef-kiszolgálót, a következőket teheti:

* Regisztráljon az [üzemeltetett chefre](https://manage.chef.io/signup), amely a Chef első lépéseinek leggyorsabb módja.
* Telepítsen egy önálló Chef-kiszolgálót a Linux-alapú gépen, a [Chef docs](https://docs.chef.io/) [telepítési utasításait](https://docs.chef.io/install_server.html) követve.

### <a name="creating-a-hosted-chef-account"></a>Üzemeltetett Chef-fiók létrehozása

Regisztráljon egy üzemeltetett Chef-fiókot [itt](https://manage.chef.io/signup).

A regisztrációs folyamat során meg kell adnia egy új szervezet létrehozását.

![Szervezeti ablak létrehozása](media/chef-automation/create-organization.png)

A szervezet létrehozása után töltse le az alapszintű csomagot.

![A Chef konfigurálása](media/chef-automation/configure-chef.png)

> [!NOTE]
> Ha figyelmeztetés jelenik meg, hogy a kulcsok alaphelyzetbe lesznek állítva, akkor a folytatáshoz, mivel még nincs konfigurálva a meglévő infrastruktúra.
>

Ez a Starter Kit zip-fájl tartalmazza a szervezet konfigurációs fájljait és a felhasználói kulcsot a `.chef` könyvtárban.

A `organization-validator.pem` külön kell letölteni, mert a titkos kulcsot és a titkos kulcsokat nem szabad a Chef-kiszolgálón tárolni. A [Chef-kezelés](https://manage.chef.io/)területen lépjen az adminisztráció szakaszra, és válassza az "érvényesítési kulcs alaphelyzetbe állítása" lehetőséget, amely egy fájlt biztosít, amely külön letölthető. Mentse a fájlt a c:\chef.

### <a name="configuring-your-chef-workstation"></a>A Chef munkaállomás konfigurálása

Bontsa ki a `chef-starter.zip` tartalmát a `c:\chef`.

Másolja a `chef-starter\chef-repo\.chef` alatt lévő összes fájlt a `c:\chef` könyvtárába.

Másolja a `organization-validator.pem` fájlt a `c:\chef`ba, ha a fájl el lett mentve `c:\Downloads`.

A címtárnak most az alábbi példához hasonlóan kell kinéznie.

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

Most öt fájlra és négy könyvtárra van szüksége (beleértve az üres Chef-adattár könyvtárat) a c:\chef. gyökérkönyvtárában

### <a name="edit-kniferb"></a>Kés. RB szerkesztése

A PEM-fájlok tartalmazzák a szervezet és a titkos kulcsokat a kommunikációhoz, és a kés. RB fájl tartalmazza a kés konfigurációját. A kés. RB fájlt is szerkeszteni kell.

Nyissa meg a kés. RB fájlt a választott szerkesztőben. A nem módosított fájlnak a következőhöz hasonlónak kell kinéznie:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Adja hozzá a következő adatokat a kés. RB-hez, és cserélje le a helyőrzőket az adataira:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Ezek a sorok biztosítják, hogy a kés a `c:\chef\cookbooks`alatt lévő szakácskönyvek könyvtárára hivatkozik.

Az `knife.rb`-fájlnak most az alábbi példához hasonlóan kell kinéznie:

![Példa kés-fájlra](./media/chef-automation/knife-file-example.png)

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

## <a name="install-chef-workstation"></a>Chef-munkaállomás telepítése

Ezután [töltse le és telepítse a Chef munkaállomást](https://downloads.chef.io/chef-workstation/).

A Chef munkaállomás telepítése az alapértelmezett helyre.

Az asztalon egy CW PowerShell jelenik meg. Ez az eszköz a Chef-termékekkel való interakcióra szolgál. A CW PowerShell új parancsokat tesz elérhetővé, például `chef-run` és Chef CLI-parancsokat (például `chef`). Megtekintheti a Chef Workstation és a Chef Tools-t a `chef -v`segítségével. A munkaállomás-verziót a Chef munkaállomás alkalmazásból kiválasztva a **Chef munkaállomás névjegye** lehetőségre kattintva is megtekintheti.

`chef --version` a következőhöz hasonlót kell visszaadnia:

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
> Az elérési út sorrendje fontos! Ha a opscode elérési útjai nem megfelelő sorrendben vannak, a problémák a következők lesznek:.
>

A folytatás előtt indítsa újra a munkaállomást.

### <a name="install-knife-azure"></a>A kés Azure telepítése

Ez az oktatóanyag feltételezi, hogy a Azure Resource Manager használja a virtuális géppel való kommunikációhoz.

Telepítse a kés Azure-bővítményt, amely magában foglalja az Azure beépülő modult is.

Futtassa a következő parancsot.

    chef gem install knife-azure ––pre

> [!NOTE]
> A `–-pre` argumentum biztosítja, hogy megkapja a kés Azure beépülő modul legújabb verzióját, amely hozzáférést biztosít a legújabb API-készletekhez.
>
>

Valószínű, hogy egyszerre több függőség is telepítve lesz.

![A kés telepítésének kimenete – Azure](./media/chef-automation/install-knife-azure.png)

Annak biztosítása érdekében, hogy minden megfelelően legyen konfigurálva, futtassa a következő parancsot.

    knife azurerm server list

Ha minden megfelelően van konfigurálva, az elérhető Azure-rendszerképek listája görgethető.

Gratulálunk! A munkaállomás be van állítva!

## <a name="creating-a-cookbook"></a>Szakácskönyv létrehozása

A Chef egy szakácskönyvet használ a felügyelt ügyfélen futtatni kívánt parancsok megadásához. A szakácskönyv létrehozása egyszerű, egyszerűen használja a `chef generate cookbook` parancsot a szakácskönyv-sablon létrehozásához. Ez a szakácskönyv egy olyan webkiszolgálóra mutat, amely automatikusan telepíti az IIS-t.

A `C:\Chef directory`alatt futtassa a következő parancsot.

    chef generate cookbook webserver

Ez a parancs a könyvtár C:\Chef\cookbooks\webserver. tartozó fájlok készletét hozza létre. A következő lépésben adja meg a Chef-ügyfél számára a felügyelt virtuális gépen futtatandó parancsok készletét.

A parancsok a default. RB fájlban tárolódnak. Ebben a fájlban adjon meg olyan parancsokat, amelyek telepítik az IIS-t, elindítja az IIS-t, és átmásol egy sablonfájlt a `wwwroot` mappába.

Módosítsa a C:\chef\cookbooks\webserver\recipes\default.RB fájlt, és adja hozzá a következő sorokat.

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

Ebben a lépésben létrehoz egy sablonfájlt, amelyet a `default.html` oldalon fog használni.

Futtassa a következő parancsot a sablon létrehozásához:

    chef generate template webserver Default.htm

Navigáljon a `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` fájlhoz. Szerkessze a fájlt egy egyszerű *"Helló világ!" alkalmazás* HTML-kód hozzáadásával, majd mentse a fájlt.

## <a name="upload-the-cookbook-to-the-chef-server"></a>A szakácskönyv feltöltése a Chef-kiszolgálóra

Ebben a lépésben létrehoz egy másolatot a szakácskönyvről, amelyet a helyi gépen hozott létre, és feltölti azt a séf által üzemeltetett kiszolgálóra. A feltöltés után a szakácskönyv a **szabályzat** lapon jelenik meg.

    knife cookbook upload webserver

![A szakácskönyv a Chef-kiszolgálóra való telepítésének eredményei](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Virtuális gép üzembe helyezése az Azure-beli késsel

Helyezzen üzembe egy Azure-beli virtuális gépet, és alkalmazza a `Webserver`-szakácskönyvet az `knife` parancs használatával.

Az `knife` parancs az IIS webszolgáltatás és az alapértelmezett weblap telepítését is telepíti.

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

A `knife` parancs például egy *Standard_DS2_v2* virtuális gépet hoz létre az USA nyugati régiójában telepített Windows Server 2016-mel. Módosítsa ezeket az értékeket az alkalmazás igényeinek megfelelően.

A parancs futtatása után tallózással keresse meg a Azure Portal, és tekintse meg a gép kiépítésének megkezdését.

![Virtuális gép kiépítve](./media/chef-automation/virtual-machine-being-provisioned.png)

A parancssorban megjelenik a következő üzenet.

![A virtuális gép létrehozásakor a kés kimenete](./media/chef-automation/knife-output-when-creating-vm.png)

Az üzembe helyezés befejezését követően megjelenik az új virtuális gép nyilvános IP-címe. Illessze be ezt az értéket egy webböngészőbe az új webhely megtekintéséhez. A virtuális gép üzembe helyezésekor megnyitottuk a 80-as portot, ezért külsőleg elérhetőnek kell lennie.   

![A virtuális gép tesztelése](./media/chef-automation/testing-the-virtual-machine.png)

Ez a példa kreatív HTML-kódot használ.

A csomópont status [Chef-kezelését](https://manage.chef.io/)is megtekintheti. 

![A csomópont állapotának megtekintése](./media/chef-automation/viewing-node-status.png)

Ne feledje, hogy egy RDP-munkameneten keresztül is csatlakozhat a Azure Portal a 3389-es porton keresztül.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Chef az Azure-ban](/azure/chef/)