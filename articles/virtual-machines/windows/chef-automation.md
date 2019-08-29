---
title: Azure-beli virtuális gépek üzembe helyezése Chef segítségével | Microsoft Docs
description: Ismerje meg, hogyan használható a Chef a virtuális gépek automatikus üzembe helyezéséhez és konfigurálásához Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 5cbf53da5a0af0a511350b9f30153e2fefe72dcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080087"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Azure-beli virtuális gépek üzembe helyezése a Cheffel

A Chef nagyszerű eszköz az automatizálás és a kívánt állapot-konfigurációk biztosításához.

A Cloud API legújabb kiadásával a Chef zökkenőmentesen integrálható az Azure-nal, és lehetővé teszi a konfigurációs állapotok egyetlen paranccsal történő üzembe helyezését és telepítését.

Ebben a cikkben a Chef-környezetet úgy állíthatja be, hogy kiépítse az Azure-beli virtuális gépeket, és végigvezeti a szabályzatok vagy a "szakácskönyvek" létrehozásán, majd a szakácskönyv Azure-beli virtuális gépre való üzembe helyezésén.

## <a name="chef-basics"></a>Chef alapjai
Mielőtt elkezdené, [tekintse át a Chef](https://www.chef.io/chef)alapfogalmait.

A következő ábra a magas szintű Chef architektúrát ábrázolja.

![][2]

A Chef három fő építészeti összetevővel rendelkezik: Chef-kiszolgáló, Chef-ügyfél (node) és Chef munkaállomás.

A Chef-kiszolgáló a felügyeleti pont, és két lehetőség közül választhat a Chef-kiszolgáló: egy üzemeltetett megoldás vagy egy helyszíni megoldás.

A Chef-ügyfél (node) a kezelt kiszolgálókon található ügynök.

A Chef munkaállomás, amely a felügyeleti munkaállomás neve, ahol szabályzatokat hozhat létre, és felügyeleti parancsokat, valamint a Chef Tools szoftvercsomag használatát is végrehajthatja.

Általában a munkaállomást fogja látni a szoftvercsomag műveleteinek és _Chef_ -munkaállomásának helyeként.
Például letöltheti a kés parancsot a _Chef munkaállomás_részeként, de a munkaállomásról származó kés parancsokat futtatva kezelheti az infrastruktúrát.

A Chef a "szakácskönyvek" és a "receptek" fogalmait is alkalmazza, amelyek gyakorlatilag az általunk definiált szabályzatok, és a kiszolgálókra érvényesek.

## <a name="preparing-your-workstation"></a>A munkaállomás előkészítése

Először készítse elő a munkaállomást úgy, hogy létrehoz egy könyvtárat a Chef konfigurációs fájljainak és a szakácskönyveknek a tárolására.

Hozzon létre egy C:\Chef. nevű könyvtárat

Töltse le és telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) legújabb verzióját a munkaállomásra.

## <a name="configure-azure-service-principal"></a>Azure-szolgáltatásnév konfigurálása

A legegyszerűbb használati feltételek és az Azure szolgáltatásnév egy szolgáltatásfiók.   Egy egyszerű szolgáltatásnév segítségével hozunk létre Azure-erőforrásokat a Chef munkaállomásán.  Ahhoz, hogy a megfelelő szolgáltatásnevet a szükséges engedélyekkel hozza létre, a következő parancsokat a PowerShellen belül kell futtatnia:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Jegyezze fel a SubscriptionID, a TenantID, a ClientID és az ügyfél titkos kulcsát (a fent megadott jelszót), amelyre később szüksége lesz. 

## <a name="setup-chef-server"></a>A Chef-kiszolgáló beállítása

Ez az útmutató feltételezi, hogy regisztrálja az üzemeltetett Cheft.

Ha még nem használ Chef-kiszolgálót, a következőket teheti:

* Regisztráljon az [üzemeltetett chefre](https://manage.chef.io/signup), amely a Chef első lépéseinek leggyorsabb módja.
* Telepítsen egy önálló Chef-kiszolgálót a Linux-alapú gépen, a [Chef docs](https://docs.chef.io/) [telepítési utasításait](https://docs.chef.io/install_server.html) követve.

### <a name="creating-a-hosted-chef-account"></a>Üzemeltetett Chef-fiók létrehozása

Regisztráljon egy üzemeltetett Chef-fiókot [itt](https://manage.chef.io/signup).

A regisztrációs folyamat során meg kell adnia egy új szervezet létrehozását.

![][3]

A szervezet létrehozása után töltse le az alapszintű csomagot.

![][4]

> [!NOTE]
> Ha figyelmeztetés jelenik meg, hogy a kulcsok alaphelyzetbe lesznek állítva, akkor a folytatáshoz, mivel még nincs konfigurálva a meglévő infrastruktúra.
>

Ez a Starter Kit zip-fájl tartalmazza a szervezet konfigurációs fájljait és `.chef` a felhasználói kulcsot a címtárban.

A `organization-validator.pem` fájlt külön kell letölteni, mert a titkos kulcs, és a titkos kulcsok nem tárolhatók a Chef-kiszolgálón. A [Chef-kezelés](https://manage.chef.io/)területen lépjen az adminisztráció szakaszra, és válassza az "érvényesítési kulcs alaphelyzetbe állítása" lehetőséget, amely egy fájlt biztosít, amely külön letölthető. Mentse a fájlt a c:\chef.

### <a name="configuring-your-chef-workstation"></a>A Chef munkaállomás konfigurálása

A Chef-Starter. zip fájl tartalmának kibontása a c:\chef.

Másolja az összes fájlt a\.Chef-starter\chef-repo Chef alá a c:\chef könyvtárba.

Másolja a `organization-validator.pem` fájlt a c:\chef-be, ha az letöltések-ben lett mentve

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

Adja hozzá a következő adatokat a kés. RB-hez:

validation_client_name "myorg-validator"

validation_key           "#{current_dir}/myorg.pem"

kés [: azure_tenant_id] = "0000000-1111-AAAA-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

kés [: azure_client_secret] = "#1234p $wdchef 19"


Ezek a sorok biztosítják, hogy a kés a c:\chef\cookbooks alatti szakácskönyvek könyvtárára hivatkozik, valamint az Azure-műveletek során létrehozott Azure-szolgáltatásnevet is.

A kés. RB fájlnak most az alábbi példához hasonlóan kell kinéznie:

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

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

Ezután [töltse le és telepítse a](https://downloads.chef.io/chef-workstation/) Chef munkaállomást.
Telepítse a Chef munkaállomás alapértelmezett helyét. A telepítés néhány percet is igénybe vehet.

Az asztalon egy "CW PowerShell" jelenik meg, amely a Chef-termékekkel való interakcióhoz szükséges eszközzel betöltött környezet. A CW PowerShell új ad hoc parancsokat tesz elérhetővé, `chef-run` például a hagyományos Chef CLI-parancsokat is, `chef`például:. Megtekintheti a Chef munkaállomás és a Chef eszközeinek `chef -v`telepített verzióját. Azt is megteheti, hogy a munkaállomásának verzióját a Chef Workstation alkalmazás "a Chef munkaállomás névjegye" elemére kattintva is megtekintheti.

`chef --version`a következőhöz hasonlót kell visszaadnia:

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
> Az elérési út sorrendje fontos! Ha a opscode elérési útjai nem megfelelő sorrendben vannak, akkor problémákba ütközik.
>

A folytatás előtt indítsa újra a munkaállomást.

### <a name="install-knife-azure"></a>A kés Azure telepítése

Ez az oktatóanyag feltételezi, hogy a Azure Resource Manager használja a virtuális géppel való kommunikációhoz.

Telepítse a kés Azure-bővítményt. Ez a kés az "Azure beépülő modullal" rendelkezik.

Futtassa a következő parancsot.

    chef gem install knife-azure ––pre

> [!NOTE]
> A – pre argumentum biztosítja, hogy megkapja a kés Azure beépülő modul legújabb RC verzióját, amely hozzáférést biztosít a legújabb API-készletekhez.
>
>

Valószínű, hogy egyszerre több függőség is telepítve lesz.

![][8]

Annak biztosítása érdekében, hogy minden megfelelően legyen konfigurálva, futtassa a következő parancsot.

    knife azurerm server list

Ha minden megfelelően van konfigurálva, az elérhető Azure-rendszerképek listája görgethető.

Gratulálunk! A munkaállomás be van állítva!

## <a name="creating-a-cookbook"></a>Szakácskönyv létrehozása

A Chef egy szakácskönyvet használ a felügyelt ügyfélen végrehajtani kívánt parancsok definiálásához. A szakácskönyv létrehozása egyszerű, csak a **Chef generált Cookbook** paranccsal hozhatja létre a szakácskönyv-sablont. Ez a szakácskönyv egy olyan webkiszolgálóra mutat, amely automatikusan telepíti az IIS-t.

Futtassa a következő parancsot a C:\Chef könyvtárában.

    chef generate cookbook webserver

Ez a parancs a könyvtár C:\Chef\cookbooks\webserver. tartozó fájlok készletét hozza létre. A következő lépésben adja meg a Chef-ügyfél által a felügyelt virtuális gépen végrehajtandó parancsok készletét.

A parancsok a default. RB fájlban tárolódnak. Ebben a fájlban adja meg az IIS-t telepítő parancsok készletét, elindítja az IIS-t, és átmásol egy sablonfájlt a WWWroot mappába.

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
Ebben a lépésben létrehoz egy sablonfájlt, amelyet az alapértelmezett. html-oldalként fog használni.

Futtassa a következő parancsot a sablon létrehozásához:

    chef generate template webserver Default.htm

Navigáljon a `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` fájlhoz. Szerkessze a fájlt egy egyszerű ""Helló világ!"alkalmazás" HTML-kód hozzáadásával, majd mentse a fájlt.

## <a name="upload-the-cookbook-to-the-chef-server"></a>A szakácskönyv feltöltése a Chef-kiszolgálóra
Ebben a lépésben létrehoz egy másolatot a szakácskönyvről, amelyet a helyi gépen hozott létre, és feltölti azt a séf által üzemeltetett kiszolgálóra. A feltöltés után a szakácskönyv a **szabályzat** lapon jelenik meg.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Virtuális gép üzembe helyezése az Azure-beli késsel
Helyezzen üzembe egy Azure-beli virtuális gépet, és alkalmazza a "webkiszolgáló" szakácskönyvet, amely telepíti az IIS-webszolgáltatást és az alapértelmezett weblapot.

Ennek elvégzéséhez használja a **kés azurerm Server Create** parancsot.

Megjelenik egy példa a parancsra a Next (tovább) gombra.

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


A fenti példa egy Standard_DS2_v2 virtuális gépet hoz létre az USA nyugati régiójában telepített Windows Server 2016-es verzióval. Helyettesítse be az adott változókat, és futtassa a parancsot.

> [!NOTE]
> A parancssorban a végpontok hálózati szűrési szabályait is automatizálom a – TCP-endpoints paraméter használatával. Megnyitottam a 80-es és 3389-es portokat, hogy hozzáférést biztosítson a weboldalhoz és az RDP-munkamenethez.
>
>

A parancs futtatása után nyissa meg a Azure Portal a számítógép kiépítésének megkezdéséhez.

![][15]

A parancssorban megjelenik a következő üzenet.

![][16]

Az üzembe helyezés befejezését követően az új virtuális gép nyilvános IP-címe megjelenik a telepítés befejezésekor, és bemásolhatja azt egy webböngészőbe, és megtekintheti a telepített webhelyet. A virtuális gép üzembe helyezésekor megnyitottuk a 80-es portot, ezért külsőleg elérhetőnek kell lennie.   

![][11]

Ez a példa kreatív HTML-kódot használ.

A csomópont status [Chef-kezelését](https://manage.chef.io/)is megtekintheti. 

![][17]

Ne feledje, hogy egy RDP-munkameneten keresztül is csatlakozhat a Azure Portal a 3389-es porton keresztül.

Köszönjük! Az Azure-ban még ma is megkezdheti az infrastruktúra használatát.

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
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
