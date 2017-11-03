---
title: "Egy Ruby sínek webhelyen a Linux virtuális gép üzemeltetésére |} Microsoft Docs"
description: "Állítsa be, és egy Ruby webhelyen sínek-alapú Azure-ban a Linux virtuális gépek üzemeltetéséhez."
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: 0518519da6c5e62a863a47d6743ab7b7c5923acf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>A Ruby on Rails webalkalmazás használata Azure-beli virtuális gépen
Ez az oktatóanyag bemutatja, hogyan sínek webhelyen Azure fonetikus üzemeltetésére Linux virtuális gépek használata.  

Ez az oktatóanyag Ubuntu Server 14.04 LTS segítségével lett érvényesítve. Ha egy másik Linux-terjesztés használatához szükség lehet módosításához sínek telepítésének lépéseit.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
>
>

## <a name="create-an-azure-vm"></a>Egy Azure virtuális gép létrehozása
Először hozzon létre egy Azure virtuális gép egy Linux-lemezképhez.

A virtuális gép létrehozásához használhatja az Azure-portálon vagy az Azure parancssori felület (CLI).

### <a name="azure-portal"></a>Azure Portal
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)
2. Kattintson a **új**, a keresőmezőbe írja be a "Ubuntu Server 14.04". Kattintson a bejegyzésre, a keresés által visszaadott. A telepítési modell kiválasztása **klasszikus**, majd kattintson a "Létrehozás" gombra.
3. Az alapvető beállítások panel az értéket a kötelező mezőket ad meg: név (a virtuális gép), a felhasználónév, a hitelesítési típus és a megfelelő hitelesítő adatok Azure-előfizetéssel, erőforráscsoportot és helyet.

   ![Hozzon létre egy új Ubuntu kép](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Miután a virtuális gép ki van építve, kattintson a virtuális gép nevére, majd kattintson **végpontok** a a **beállítások** kategóriát. Az SSH a végponthoz, a felsorolt található **önálló**.

   ![Alapértelmezett végpont](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Azure CLI
Kövesse a [hozzon létre egy virtuális gép futó Linux][vm-instructions].

A virtuális gép üzembe helyezése után kaphat az SSH-végpont a következő parancs futtatásával:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Ruby sínen telepítése
1. Az SSH használata a virtuális Géphez való kapcsolódáshoz.
2. Az SSH-munkamenetből a következő parancsok segítségével Ruby telepítése a virtuális gépen:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    A telepítés néhány percet vehet igénybe. Ha elkészült, a következő paranccsal ellenőrizheti, hogy telepítve van-e a Ruby:

        ruby -v

3. A következő paranccsal telepítse sínek:

        sudo gem install rails --no-rdoc --no-ri -V

    Használja a--nem-rdoc és--nem-ri jelző kihagyását a dokumentációját, amely gyorsabb telepítése.
    Ez a parancs valószínűleg végrehajtása hosszú időt vesz igénybe, így hozzáadása a -V megjeleníti a telepítési folyamat kapcsolatos információkat.

## <a name="create-and-run-an-app"></a>Hozzon létre, és az alkalmazás futtatása
Miközben az továbbra is naplózza SSH-n keresztül, a következő parancsokat:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

A [új](http://guides.rubyonrails.org/command_line.html#rails-new) parancs létrehoz egy új sínek alkalmazást. A [server](http://guides.rubyonrails.org/command_line.html#rails-server) indítja el a sínek WEBrick webkiszolgáló. (Az éles környezetben való használathoz, akkor érdemes lehet másik kiszolgálót, például Unicorn vagy utas.)

Az alábbihoz hasonló kimenetnek kell megjelennie.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Végpont hozzáadása
1. Nyissa meg az [Azure portálra] [https://portal.azure.com] válassza ki a virtuális Gépet.

2. Válassza ki **VÉGPONTOK** a a **beállítások** a bal oldali szegély a lapot.

3. Kattintson a **hozzáadása** az oldal tetején.

4. Az a **végpont hozzáadása** párbeszédpanel lapján adja meg a következő adatokat:

   * **Név**: HTTP
   * **Protokoll**: TCP
   * **Nyilvános port**: 80
   * **Magánhálózati port**: 3000
   * **Lebegőpontos PI cím**: letiltva
   * **Hozzáférés-vezérlési lista - rendelés**: 1001, vagy egy másik érték, amely beállítja a hozzáférési szabály prioritását.
   * **Hozzáférés-vezérlési lista - név**: allowHTTP
   * **Hozzáférés-vezérlési lista - művelet**: engedélyezése
   * **Hozzáférés-vezérlési lista - távoli alhálózati**: 1.0.0.0/16

     Ezt a végpontot, amely átirányítja a forgalmat a magánhálózati port az 3000, ahol a sínek kiszolgáló figyel a 80-as nyilvános port van. A hozzáférés-vezérlési lista szabály lehetővé teszi, hogy a nyilvános forgalom 80-as porton.

     ![új végpont](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Kattintson az OK gombra a végpont mentése.

6. Egy üzenet megjelenjen-e arról, hogy **mentése a virtuális gép végpontjának**. Ez az üzenet megjelenik, amint a végpont nem aktív. Nyissa meg a virtuális gép DNS-neve most is tesztelheti az alkalmazást. A webhely a következőhöz hasonlóan kell megjelennie:

    ![alapértelmezett sínek lap][default-rails-cloud]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban hasonló lépéseket a legtöbb manuálisan. Éles környezetben kívánja írni az alkalmazást a fejlesztési számítógépen, majd központilag telepítenie kell az Azure virtuális Géphez. Is a legtöbb éles környezetben a sínek alkalmazás, például az Apache vagy NginX, mely kezeli a kérelem a sínek alkalmazás több példánya történő továbbítást, és statikus erőforrások kiszolgáló egy másik kiszolgáló folyamat együtt üzemeltetéséhez. További információkért lásd: http://rubyonrails.org/deploy/.

Ruby sínen kapcsolatos további információkért látogasson el a [sínek útmutatók a Ruby][rails-guides].

A Ruby-alkalmazás Azure-szolgáltatásokat használja, olvassa el:

* [Blobok strukturálatlan adatok tárolásához][blobs]
* [Tároló kulcs/érték párok táblák használata][tables]
* [Nagy sávszélesség tartalmat a tartalom kézbesítésével hálózattal][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
