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
ms.openlocfilehash: 3a6fbc8410dbc5aec4522f0972a29c67527edb23
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060598"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Azure-beli virtuális gépek üzembe helyezése a Cheffel
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef egy nagyszerű eszköz automation kidolgozását és a kívánt állapot konfigurációk.

A legújabb felhőalapú api verzió, a Chef biztosít zökkenőmentes integráció az Azure-ral, így és üzembe helyezéséhez konfigurációs állapotnak egyetlen parancs használatával.

Ebben a cikkben, állítsa be a Chef környezetet az Azure-beli virtuális gépek kiépítése, és haladjon végig egy házirend vagy az "Útmutató" létrehozása és üzembe kell helyezni a útmutató Azure virtuális gépeken.

Lássunk is hozzá!

## <a name="chef-basics"></a>Chef alapjai
Mielőtt elkezdené, [tekintse át az alapvető fogalmait, Chef](http://www.chef.io/chef). 

A következő ábra szemlélteti a Chef architektúrájának áttekintése.

![][2]

Chef három fő architekturális összetevőre van: Chef-kiszolgálót, a Chef ügyfél (node) és a Chef munkaállomás.

A Chef-kiszolgálót a felügyeleti pont és a Chef kiszolgáló két lehetőség van: üzemeltetett megoldás vagy egy helyszíni megoldás. Fogjuk használni egy üzemeltetett megoldás.

A Chef ügyfél (node), akkor az ügynök, amely a kezelt kiszolgálókon.

A Chef munkaállomásra a felügyeleti munkaállomásról nevezhetjük szabályzatokat hozhat létre, és hajtsa végre a felügyeleti parancsokat. Futtassa a Microsoft a **kés** parancsot a Chef az infrastruktúra kezelése munkaállomásról.

Nincs "Kézikönyvek" és "Receptek" fogalmát. Ezek a hatékony azt határozza meg, és a kiszolgálók a alkalmazni a házirendeket.

## <a name="preparing-the-workstation"></a>A munkaállomás előkészítése
Először is lehetővé teszi, hogy a munkaállomás előkészítése. A szabványos Windows-munkaállomás használok. Hozzon létre egy könyvtárat a konfigurációs fájlokat és kézikönyvek tárolni kell.

Először hozzon létre egy C:\chef nevű könyvtárat.

Ezután hozzon létre egy második, c:\chef\cookbooks nevű könyvtár.

Most meg kell az Azure-beállítások-fájl letöltése, Chef képes kommunikálni az Azure-előfizetés.

Töltse le a közzétételi beállítások használata a PowerShell Azure [Get-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) parancsot. 

Mentse a közzétételi beállítások fájljának C:\chef.

## <a name="creating-a-managed-chef-account"></a>Egy felügyelt Chef-fiók létrehozása
Egy üzemeltetett Chef fiók [Itt](https://manage.chef.io/signup).

A regisztrációs folyamat során meg kell adnia a hozzon létre új szervezetet.

![][3]

A szervezet létrehozása után töltse le a starter kit.

![][4]

> [!NOTE]
> Ha megjelenik egy figyelmeztetés, hogy a kulcsokat a rendszer visszaállítja kérdés, rendben folytatható, mert a meglévő infrastruktúra nélkül még konfigurálva van.
> 
> 

Az alapszintű csomag zip-fájl tartalmazza a szervezet konfigurációs fájlokat és a kulcsokat.

## <a name="configuring-the-chef-workstation"></a>A Chef munkaállomás konfigurálása
Bontsa ki a chef-starter.zip C:\chef a tartalmát.

Chef-starter\chef-adattárban található összes fájlt másolja\.chef a c:\chef címtárhoz.

A címtár most hasonlóan kell kinéznie a következő példa.

![][5]

Most már négy olyan fájlok, például az Azure közzétételi fájlját c:\chef gyökerében.

A PEM-fájlok közben a knife.rb fájl tartalmazza a Kés konfigurációt a szervezet és a titkos kulcsok rendszergazdai kommunikációhoz tartalmaz. Szükségünk lesz a knife.rb fájl szerkesztéséhez.

Nyissa meg a fájlt a kívánt szerkesztőprogrammal, és módosítsa a "cookbook_path" eltávolításával a /... / elérési útja, így látható a következő módon jelenik meg.

    cookbook_path  ["#{current_dir}/cookbooks"]

Emellett adja hozzá a következő sor neve az Azure-hoz továbbítható közzétételi beállítások fájljában.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

A knife.rb fájl most a következő példához hasonlóan kell kinéznie.

![][6]

Ezek a sorok biztosítja kés c:\chef\cookbooks kézikönyvek almappájába hivatkozik, és az Azure közzétételi beállítások fájlját is használja az Azure műveletek során.

## <a name="installing-the-chef-development-kit"></a>A Chef fejlesztőkészlet telepítése
Tovább [töltse le és telepítse](http://downloads.getchef.com/chef-dk/windows) a ChefDK (Chef Development Kit) állíthatja be a Chef munkaállomáson.

![][7]

Telepítse a c:\opscode alapértelmezett helyét. A telepítés körülbelül 10 percet vesz igénybe.

A PATH változóban bejegyzést tartalmaz a C:\opscode\chefdk\bin; megerősítése C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Ha nem létezik, mindenképpen adja hozzá az elérési utak!

*MEGJEGYZÉS: AZ ELÉRÉSI UTAT A SORREND FONTOS SZEREPET JÁTSZANAK.* Ha a opscode elérési utak nem a megfelelő sorrendben kell problémákat.

Indítsa újra a munkaállomáson, a folytatás előtt.

Ezután a Kés Azure-bővítmény telepítve lesz. Ez biztosítja, hogy az "Azure beépülő modullal" Kés.

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
Egy könyv meghatározásához hajtsa végre a felügyelt ügyfél kívánt parancsokat Chef használják. Egy könyv létrehozása egyszerű, használjuk a **chef készítése cookbook** parancsot a könyv sablon létrehozásához. E fog hívni, ajánlott a könyv webkiszolgáló, azt szeretném, olyan szabályzatot, amely automatikusan telepíti az IIS.

A C:\Chef könyvtárban futtassa a következő parancsot.

    chef generate cookbook webserver

Ekkor a fájlok a könyvtárban C:\Chef\cookbooks\webserver készletét. Most meg kell azt szeretnénk, hajtsa végre a felügyelt virtuális gépen a Chef-ügyfél parancsok megadásához.

A parancsok a fájl default.rb vannak tárolva. A fájl I fog kell definiálását parancsok, amely telepíti az IIS szolgáltatást, elindítja az IIS szolgáltatást, és egy sablon fájlt másolja a wwwroot mappája.

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
Ahogy korábban említettük, meg kell létrehozni egy sablon fájlt, amely a default.html lap lesz.

Futtassa a következő parancsot a sablon létrehozásához.

    chef generate template webserver Default.htm

Most lépjen a C:\chef\cookbooks\webserver\templates\default\Default.htm.erb fájlt. Szerkessze a fájlt néhány egyszerű "Hello World" HTML-kód hozzáadásával, és mentse a fájlt.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Töltse fel az útmutató a Chef-kiszolgáló
Ebben a lépésben azt az útmutató a helyi gép alapján készült másolatot tart, és feltölti a Chef üzemeltetett kiszolgálón. Feltöltése után a könyv alatt jelenik meg a **házirend** fülre.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Virtuális gép üzembe helyezése az Azure-ral Kés
Azt fogja most üzembe helyezése egy Azure virtuális gépen, és a alkalmazni az "Webkiszolgáló" Cookbook, amely telepíti az IIS szolgáltatást és az alapértelmezett webes weblap.

Használja ezt a **kés az azure-kiszolgáló létrehozása** parancsot.

AM példát a parancs a következő jelenik meg.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

A paraméterek magától értetődő. Helyettesítse be az adott változókat, és futtassa.

> [!NOTE]
> A parancssor használatával szeretnék vagyok is automatizálása a végpont hálózati Állapotszűrő szabályok – tcp-végpontok paraméter használatával. E megnyitott 80-as és a 3389-es, így biztosítva hozzáférést a saját webhelye és az RDP-munkamenetet.
> 
> 

Miután futtatta a parancsot, nyissa meg az Azure Portalon, és látni fogja a gép kiépítésének megkezdéséhez.

![][13]

A parancssor használatával tovább jelenik meg.

![][10]

Az üzembe helyezés befejezése után azt tudnia kell csatlakozni a web service 80-as porton keresztül, a port lenne megnyitva azt a Kés Azure paranccsal a virtuális gép üzembe helyezésekor. Mivel a virtuális gép csak a virtuális gép, a felhőszolgáltatásban, szeretném összekapcsolni a felhőalapú szolgáltatás URL-címet.

![][11]

Amint láthatja, kaptam creative a saját HTML-kódot.

Ne feledje, hogy is csatlakoztathat egy RDP-munkamenetet a 3389-es porton keresztül az Azure Portalon keresztül.

Remélem, hasznos volt! Nyissa meg, és az infrastruktúra mint kód világába az Azure-ral holnapig!

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
