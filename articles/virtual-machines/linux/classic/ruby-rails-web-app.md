---
title: Üzemeltethet Ruby on Rails-webhelyet egy Linux rendszerű virtuális gépen |} A Microsoft Docs
description: Hozzon létre és üzemeltethet Ruby on Rails-alapú webhelyet az Azure-ban Linux rendszerű virtuális gép.
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: 6ea1d249b7f9aec3a45923b162a97ce7f83d0d31
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901153"
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>A Ruby on Rails webalkalmazás használata Azure-beli virtuális gépen
Ez az oktatóanyag bemutatja, hogyan üzemeltethet Ruby on Rails-webhelyet az Azure-ban a Linux rendszerű virtuális gép használatával.  

Ebben az oktatóanyagban az Ubuntu Server 14.04 LTS segítségével lett érvényesítve. A különböző Linux-disztribúció használatakor szüksége lehet módosítani a Rails telepítésének lépéseit.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Az Azure virtuális gép létrehozása
Először hozzon létre egy Azure virtuális Gépen a Linux-rendszerképen.

A virtuális gép létrehozásához, használhatja az Azure portal vagy az Azure parancssori felület (CLI).

### <a name="azure-portal"></a>Azure Portal
1. Jelentkezzen be a [Azure Portalon](https://portal.azure.com)
2. Kattintson a **erőforrás létrehozása**, majd írja be az "Ubuntu Server 14.04" kifejezést a keresőmezőbe. Kattintson a bejegyzésre, a keresés által visszaadott. Válassza ki az üzembe helyezési modelljének **klasszikus**, majd kattintson a "Létrehozás" gombra.
3. Az alapvető beállítások panelen adja meg a szükséges mezők értékeit: név (a virtuális gép), a felhasználónevet, a hitelesítési típus és a megfelelő hitelesítő adatokkal, Azure-előfizetést, erőforráscsoportot és helyet.

   ![Hozzon létre egy új Ubuntu-rendszerkép](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Miután a virtuális gép ki van építve, a virtuális gép nevére, majd kattintson **végpontok** a a **beállítások** kategória. Keresse meg az SSH-végpont, felsorolva **önálló**.

   ![Alapértelmezett végpont](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Azure CLI
Kövesse a [hozzon létre egy virtuális gépen futó Linux][vm-instructions].

Miután a virtuális gép ki van építve, megtekintheti az SSH-végpont a következő parancs futtatásával:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Telepítse a Ruby on Rails
1. Az SSH használata a virtuális Géphez való csatlakozáshoz.
2. Az SSH-munkamenetből a következő parancsok használatával telepítse a Rubyt a virtuális gépen:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    A telepítés néhány percet is igénybe vehet. Amikor ez befejeződik, a következő parancs használatával győződjön meg arról, hogy telepítve van-e a Ruby:

        ruby -v

3. A következő paranccsal telepítse a Rails:

        sudo gem install rails --no-rdoc --no-ri -V

    Használja a--no-rdoc és--no-beli fenntartott példányok jelzők kihagyja a telepítését a dokumentációt, amely gyorsabb.
    Ez a parancs valószínűleg végrehajtásához, hosszú időt vesz igénybe, így hozzáadása a -V megjeleníti a telepítés előrehaladását kapcsolatos információkat.

## <a name="create-and-run-an-app"></a>Hozzon létre és futtathat egy alkalmazást
Miközben továbbra is naplózza SSH-n keresztül, futtassa a következő parancsokat:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

A [új](http://guides.rubyonrails.org/command_line.html#rails-new) parancs létrehoz egy új Rails-alkalmazást. A [kiszolgáló](http://guides.rubyonrails.org/command_line.html#rails-server) parancs elindítja a Rails mellékelt WEBrick webkiszolgáló. (Üzemi használatra, valószínűleg érdemes másik kiszolgálót, például Unicorn vagy utas.)

Az alábbihoz hasonló kimenetnek kell megjelennie.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Végpont hozzáadása
1. Nyissa meg az [Azure Portalt] [https://portal.azure.com], és válassza ki a virtuális Gépet.

2. Válassza ki **VÉGPONTOK** a a **beállítások** a bal szélen él a lapot.

3. Kattintson a **hozzáadása** az oldal tetején.

4. Az a **végpont hozzáadása** párbeszédpanel lap, adja meg a következőket:

   * **Név**: HTTP
   * **Protokoll**: TCP
   * **Nyilvános port**: 80-as
   * **Magánhálózati port**: 3000
   * **A PI-címe fix**: letiltva
   * **Hozzáférés-vezérlési lista - rendelés**: 1001, vagy egy másik értéket, amely beállítja a hozzáférési szabály prioritását.
   * **Hozzáférés-vezérlési lista - név**: allowHTTP
   * **Hozzáférés-vezérlési lista - művelet**: engedélyezve
   * **Hozzáférés-vezérlési lista – távoli alhálózati**: 1.0.0.0/16

     Ez a végpont rendelkezik egy nyilvános port a 80-as, amely forgalmat irányítja át a magánhálózati port az 3000, ahol a Rails-kiszolgálót figyel. A hozzáférés-vezérlési lista szabály lehetővé teszi, hogy a nyilvános forgalom 80-as porton.

     ![új végpont](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Kattintson az OK gombra a végpont mentése.

6. Egy üzenetet kell megjelennie, amely **mentése folyamatban van a virtuális gép végpontjának**. Ez az üzenet eltűnik, ha a végpont még aktív. Most már előfordulhat, hogy tesztelheti alkalmazását úgy ellenőrizheti, hogy a virtuális gép DNS-nevét. A webhely a következőhöz hasonlóan kell megjelennie:

    ![alapértelmezett rails lap][default-rails-cloud]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban tette a lépéseket a legtöbb manuálisan. Éles környezetben ehhez az alkalmazás írása egy fejlesztői gépen, és üzembe helyezése az Azure virtuális Gépen. Emellett a legtöbb éles környezetben gazdagép a Rails-alkalmazást egy másik kiszolgáló folyamat, mint például az Apache vagy a nginx-et, mely kezeli a Rails-alkalmazás több példánya az Útválasztás és a statikus erőforrások kiszolgálása kérelem együtt. További információkért lásd: http://guides.rubyonrails.org/routing.html.

A Ruby on Rails kapcsolatos további információkért látogasson el a [Rails útmutatók a Ruby][rails-guides].

Ruby-alkalmazását az Azure-szolgáltatások használatára, tekintse meg:

* [Strukturálatlan adatok blobok Store][blobs]
* [Store kulcs/érték párok táblák használata][tables]
* [A Content Delivery Network nagy sávszélességű tartalmak kiszolgálása][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal-classic.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
