---
title: Linux-rendszerhéj-parancsfájl-labor beállítása az Azure Lab Services szolgáltatással | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthat be egy labort a shell scriptek Linuxon történő tanításához.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443550"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Állítson be egy labort, hogy megtanítsa a shell szkriptelést Linuxon
Ez a cikk bemutatja, hogyan állíthat be egy labort a shell scripting linuxos tanításához. A parancsfájlok használata a rendszerfelügyelet hasznos része, amely lehetővé teszi a rendszergazdák számára az ismétlődő feladatok elkerülését. Ebben a mintaforgatókönyvben az osztály a hagyományos bash-parancsfájlokat és a továbbfejlesztett parancsfájlokat ismerteti. A továbbfejlesztett parancsfájlok olyan parancsfájlok, amelyek bash parancsokat és Rubint kombinálnak. Ez a megközelítés lehetővé teszi, hogy Ruby adatokat továbbítson, és lejátssza a parancsokat a rendszerhéjlal való interakcióhoz. 

A diákok figyelembe ezeket a szkriptek osztályok kap egy Linux virtuális gép, hogy megtanulják az alapokat a Linux, és megismerkednek a bash shell scripting. A Linux virtuális gép jön a távoli asztali hozzáférés engedélyezve van, és [gedit](https://help.gnome.org/users/gedit/stable/) és [Visual Studio Code](https://code.visualstudio.com/) szövegszerkesztők telepítve.

## <a name="lab-configuration"></a>Labor konfigurációja
A labor beállításához azure-előfizetésre van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. Miután rendelkezik egy Azure-előfizetéssel, létrehozhat egy új laborfiókot az Azure Lab Servicesben, vagy használhat egy meglévő laborfiókot. Az új laborfiók létrehozásáról a következő oktatóanyagban: [Bemutató a Labor-fiók beállításához](tutorial-setup-lab-account.md).

A tesztkörnyezet-fiók létrehozása után engedélyezze a következő beállításokat a tesztkörnyezet-fiókban: 

| Laborfiók beállítása | Utasítások |
| ----------- | ------------ |  
| Marketplace-képek | Engedélyezze az [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) lemezképet a laborfiókon belüli használatra. További információ: [Marketplace-képek megadása a laborkészítők számára.](specify-marketplace-images.md) | 

Kövesse [ezt az oktatóanyagot](tutorial-setup-classroom-lab.md) egy új labor létrehozásához és alkalmazza a következő beállításokat:

| Labor beállításai | Érték/utasítások | 
| ------------ | ------------------ |
| Virtuális gép (VM) mérete | Kicsi  |
| Virtuális gép képe | [Ubuntu szerver 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Távoli asztali kapcsolat engedélyezése | Engedélyezi. <p>Ha engedélyezi ezt a beállítást, a tanárok és a diákok a távoli asztal (RDP) használatával csatlakozhatnak a virtuális gépeikhez. További információ: [Távoli asztal engedélyezése Linuxos virtuális gépekhez az Azure Lab Services laborjában](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)című témakörben talál. </p>|


## <a name="install-desktop-and-xrdp"></a>Asztali számítógép és xrdp telepítése
Az [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) lemezkép alapértelmezés szerint nincs telepítve távoli asztali kiszolgálóval. Kövesse a [Távoli asztal telepítése és konfigurálása linuxos virtuális géphez való csatlakozáshoz az Azure-ban](../../virtual-machines/linux/use-remote-desktop.md) című cikkben található utasításokat a sablongépen a távoli asztali protokollon keresztül történő csatlakozáshoz szükséges csomagok telepítéséhez.

## <a name="install-ruby"></a>A Ruby telepítése
Ruby egy nyílt forráskódú dinamikus nyelv, amely kombinálható bash szkriptek. Ez a rész `apt-get` bemutatja, hogyan kell telepíteni a legújabb ruby [.](https://www.ruby-lang.org/)

1. Telepítse a frissítéseket a következő parancsok futtatásával:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Telepítse [a Ruby](https://www.ruby-lang.org/).  Ruby egy nyílt forráskódú dinamikus nyelv, amely kombinálható bash szkriptek. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Fejlesztői eszközök telepítése
Ez a rész bemutatja, hogyan kell telepíteni egy pár szövegszerkesztők. A Gedit a gnome asztali környezet alapértelmezett szövegszerkesztője. Általános célú szövegszerkesztőnek tervezték. A Visual Studio Code egy szövegszerkesztő, amely támogatja a hibakeresést és a forrásvezérlés integrációját.

> [!NOTE]
> Számos különböző szövegszerkesztő áll rendelkezésre. Visual Studio Code és gedit csak két példa.

1. Telepítse [a gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Telepítse [a Visual Studio-kódot](https://code.visualstudio.com/).  A Visual Studio-kód a Snap Store segítségével telepíthető.  Az alternatív telepítési lehetőségeket a [Visual Studio Code alternatív letöltései című témakörben tésszet.](https://code.visualstudio.com/#alt-downloads)

    ```bash
    sudo snap install vscode --classic 
    ```

    A sablon most frissül, és mind a programozási nyelv és a fejlesztői eszközök befejezéséhez szükséges a laborban. A sablonkép most már közzétehető a laborban. A sablon közzétételéhez kattintson **a** közzététel gombra a sablon tesztkörnyezetben való közzétételéhez.  

## <a name="cost"></a>Költségek 
Ha meg szeretné becsülni a labor költségét, a következő példát használhatja:
 
Egy 25 fős osztály esetében, akik 20 óra ütemezett óra időt és 10 óra kvótát kapnak a házi feladatra vagy a feladatokra, a labor ára a következő lenne: 

25 diák * (20 + 10) óra * 20 Labor egység * 0,01 USD óránként = 150 USD

A díjszabásról a következő dokumentumban talál további információt: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés
Ez a cikk végigment a lépéseken, hogy hozzon létre egy tesztkörnyezet parancsfájlok. Bár ez a cikk a Ruby parancsfájl-eszközök linuxos gépen történő beállítására összpontosított, ugyanaz a beállítás használható más parancsfájl-műveletekhez, például a Linuxon lévő Pythonhoz.

## <a name="next-steps"></a>További lépések
A következő lépések gyakoriak a tesztkörnyezet beállításában:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemezés beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail regisztrációs linkek a diákok](how-to-configure-student-usage.md#send-invitations-to-users). 





