---
title: Az Azure virtuális gépek telepítése a Chef |} Microsoft Docs
description: Chef használata ehhez az automatikus virtuális gép központi telepítése és konfigurálása a Microsoft Azure
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
ms.openlocfilehash: 36293c41219a1b42d75850fa66d3c631637bb855
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30916248"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Azure-beli virtuális gépek üzembe helyezése a Cheffel
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef automation kézbesítéséhez egy remek eszköz, és állapot-konfiguráció szükséges.

A legújabb api felhőalapú kiadás, Chef zökkenőmentes integrációt biztosít az Azure-felkínálva kiosztását és telepítését konfigurációs állapotnak egyetlen parancs keresztül lehetőséget.

Ebben a cikkben, állítsa be a Chef környezetet az Azure virtuális gépeket, és végezze el a házirend vagy a "CookBook" hoz létre, és ez cookbook majd telepítése Azure virtuális gép.

Most megkezdéséhez!

## <a name="chef-basics"></a>Chef alapjai
Mielőtt elkezdené, [tekintse át az alapfogalmakat Chef](http://www.chef.io/chef). 

A következő ábra szemlélteti a magas szintű Chef architektúra.

![][2]

Chef három fő architekturális részből áll: Chef Server, a Chef ügyfél (csomópont) és a Chef munkaállomásra.

A Chef kiszolgáló a felügyeleti pont és a Chef kiszolgáló két lehetőség: egy üzemeltetett megoldás vagy a helyszíni megoldás. Fogjuk használni egy üzemeltetett megoldás.

A Chef ügyfél (csomópont) áll az ügynök, amely a kezelt kiszolgálókon.

A Chef munkaállomás a felügyeleti munkaállomás, ahol azt szabályzatok létrehozása és kezelése parancsok. Futtassa a Microsoft a **kés** parancsot a Chef munkaállomás a infrastruktúra kezelése érdekében.

A "Cookbooks" és "Receptet" fogalmát is van. Ezek a hatékonyan a házirendek azt határozza meg, és a kiszolgálókon.

## <a name="preparing-the-workstation"></a>A munkaállomás előkészítése
Először is lehetővé teszi, hogy a munkaállomás előkészítése. A szabványos Windows-munkaállomás használata. Hozzon létre egy könyvtárat cookbooks és konfigurációs fájljainak tárolására van szükségünk.

Először létre kell hoznia egy C:\chef nevű könyvtárat.

Ezután hozzon létre egy második c:\chef\cookbooks nevű könyvtár.

Most már van szükségünk, Chef képes kommunikálni az Azure-előfizetés, töltse le az Azure-alapú beállítások fájlt.

Töltse le a közzétételi beállítások a PowerShell Azure használatával [Get-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) parancsot. 

Mentse a közzétételi beállítások fájlja C:\chef.

## <a name="creating-a-managed-chef-account"></a>Felügyelt Chef fiók létrehozása
Létrehoz egy üzemeltetett Chef fiókot [Itt](https://manage.chef.io/signup).

A regisztráció során fogja kérni hozzon létre új szervezetet.

![][3]

Ha a szervezet létrejött, töltse le a starter kit.

![][4]

> [!NOTE]
> Ha megjelenik egy értesítés figyelmezteti, hogy a kulcsok vissza lesz állítva, rá a folytatódni, mert a meglévő infrastruktúra nélkül még konfigurálva van.
> 
> 

A starter kit zip-fájl tartalmazza a szervezet konfigurációs fájlokat és a kulcsokat.

## <a name="configuring-the-chef-workstation"></a>A Chef munkaállomás konfigurálása
Bontsa ki a C:\chef chef-starter.zip tartalmát.

Másolja az összes fájlt a chef-starter\chef-tárház\.chef c:\chef címtárat.

A címtár most hasonlóan kell kinéznie az alábbi példa.

![][5]

Többek között az Azure közzétételi fájl gyökerében található c:\chef négy fájlok most már rendelkeznie kell.

A PEM-fájlok a szervezet és a rendszergazda titkos kulcsok kommunikációhoz tartalmaz, amíg a knife.rb fájl a Kés konfigurációját tartalmazza. Igazolnia kell a knife.rb fájl szerkesztésével.

Nyissa meg a fájlt a választott szerkesztővel, és módosítsa a "cookbook_path" eltávolításával a /... az az elérési út, így látható a következő módon jelenik meg.

    cookbook_path  ["#{current_dir}/cookbooks"]

Emellett adja hozzá a következő sor az Azure neve tükröző közzététele beállításfájl.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

A knife.rb fájl most az alábbi példához hasonlóan kell kinéznie.

![][6]

Ezek a sorok fog győződjön meg arról, hogy kés c:\chef\cookbooks cookbooks almappájába hivatkozik, is használja az Azure közzétételi beállítási fájlját az Azure üzemeltetése során.

## <a name="installing-the-chef-development-kit"></a>A Chef csomag telepítése
Következő [töltse le és telepítse](http://downloads.getchef.com/chef-dk/windows) a ChefDK (Chef szoftverfejlesztői készlet) állíthatja be a Chef munkaállomáson.

![][7]

Telepítse a c:\opscode alapértelmezett helyét. A telepítés körülbelül 10 percet vesz igénybe.

Ellenőrizze, hogy a PATH változóban bejegyzést tartalmaz a C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Ha nem létezik, győződjön meg arról, adja hozzá az elérési utak!

*VEGYE FIGYELEMBE, HOGY AZ ELÉRÉSI ÚT A SORREND FONTOS!* Amennyiben a opscode elérési út nem a megfelelő sorrendben kell problémákat.

Indítsa újra a munkaállomáson, a folytatás előtt.

Ezt követően telepítjük a Kés Azure-bővítményt. Ez lehetővé teszi az "Azure a beépülő modul" Kés.

A következő parancsot.

    chef gem install knife-azure ––pre

> [!NOTE]
> A – előtti argumentum biztosítja azért kapta, amely biztosít hozzáférést a legújabb API-készlet kés Azure beépülő modul RC legújabb verzióját.
> 
> 

Valószínű, hogy a függőségek számát is megtörténik egy időben.

![][8]

Annak érdekében, hogy minden helyesen van-e állítva, a következő parancsot.

    knife azure image list

Ha minden megfelelően van telepítve, látni fogja a keresztül elérhető Azure-rendszerképek görgetési listáját.

Gratulálunk! A munkaállomás be van állítva.

## <a name="creating-a-cookbook"></a>Egy Cookbook létrehozása
Egy Cookbook Chef használják meghatározásához hajtsa végre a felügyelt ügyfelek kívánt parancsokat. Egy Cookbook létrehozása egyszerű és használjuk a **chef készítése cookbook** parancs a Cookbook sablon létrehozásához. I fog hív Cookbook webkiszolgálón, szeretném, hogy egy házirendet, amely automatikusan telepíti az IIS.

A C:\Chef könyvtárában a következő parancsot.

    chef generate cookbook webserver

Ezzel a fájlokat a könyvtárban C:\Chef\cookbooks\webserver hoz létre. Most kell azt szeretné, hogy a Chef ügyfél a felügyelt virtuális gépen található parancsok a kulcstulajdonságokat határozza meg.

A parancsok a fájl default.rb vannak tárolva. Ebben a fájlban I lesz kell meghatározása parancsok egy halmazát, telepíti az IIS szolgáltatást, az IIS és a sablonfájl a wwwroot mappába másolja.

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

## <a name="creating-a-template"></a>Egy sablon létrehozása
Ahogy azt korábban említettük, igazolnia kell a default.html lapként használt sablon fájlt létrehozni.

A következő parancsot a sablon létrehozásához.

    chef generate template webserver Default.htm

Most, keresse meg a C:\chef\cookbooks\webserver\templates\default\Default.htm.erb fájlt. Néhány egyszerű "Hello World" HTML-kódot hozzáadásával szerkessze a fájlt, és mentse a fájlt.

## <a name="upload-the-cookbook-to-the-chef-server"></a>A Cookbook feltölteni a Chef kiszolgálóra
Ebben a lépésben azt vannak véve a Cookbook, amely a helyi gépen létrehoztunk egy példányát, majd ismét feltölteni a Chef birtokolt kiszolgáló. A feltöltést követően a Cookbook alatt megjelenik a **házirend** fülre.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>A Kés Azure virtuális gép telepítése
Rendszer most egy Azure virtuális gép üzembe helyezéséhez és a "Webkiszolgáló" Cookbook is telepíti az IIS szolgáltatást és az alapértelmezett webes weblap alkalmazni.

Ehhez használja a **kés azure-kiszolgáló létrehozása** parancsot.

Vagyok, például a parancs a következő jelenik meg.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

A paraméterek magától értetődő. Helyettesítse be a változó, és futtassa.

> [!NOTE]
> Keresztül a parancssorban I vagyok is automatizálása a végpont hálózati Állapotszűrő szabályok – tcp-végpontok paraméter használatával. I megnyitott portok 80-as és a 3389-es hozzáférést biztosít a weblap és az RDP-munkamenetbe.
> 
> 

A parancs futtatása után nyissa meg az Azure portálra, és látni fogja a számítógép kiépítését megkezdéséhez.

![][13]

A parancssor jelenik meg a következő.

![][10]

Ha a telepítés befejeződött, azt kell kapcsolódni tud a webszolgáltatáshoz való 80-as porton keresztül a port azt kellett megnyitni, ha azt a virtuális gép kés Azure paranccsal kiépítve. Mivel a virtuális gép egyetlen virtuális gép tárolása a felhőalapú szolgáltatás, szeretném összekapcsolni felhőalapú szolgáltatás URL-címét.

![][11]

Ahogy látja, figyelmeztető kreatív a HTML-kódra.

Ne feledje azt az Azure-portálon 3389-es porton keresztül egy RDP-kapcsolaton keresztül is csatlakozhat.

Ez hasznos lett legyen szeretnék! Nyissa meg, és ma kód használatában az Azure-ral, indítsa el az infrastruktúra!

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
