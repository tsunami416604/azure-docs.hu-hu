---
description: fájl belefoglalása
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: 5439de30b02b0ce05853c8112f9e29239743ef98
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67179739"
---
1. A böngészőben nyissa meg az [Azure Marketplace-lemezképet a Jenkins számára.](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)

1. Válassza **a GET IT NOW lehetőséget.**

    ![Válassza a GIT IT NOW lehetőséget a Jenkins Piactér lemezkép telepítési folyamatának elindításához.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Miután áttekintette az árképzésrészleteit és a feltételekkel kapcsolatos információkat, válassza a **Folytatás**lehetőséget.

    ![Jenkins Marketplace-lemezkép-díjszabás és szerződési feltételek.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Válassza a **Létrehozás** lehetőséget a Jenkins-kiszolgáló konfigurálásához az Azure Portalon. 

    ![Telepítse a Jenkins Piactér lemezképet.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Az **Alapok** lapon adja meg a következő értékeket:

   - **Név** - `Jenkins`Írja be a be írást.
   - **Felhasználónév** – Adja meg a felhasználónevet, amelyet használni kell, amikor bejelentkezik a virtuális gépre, amelyen a Jenkins fut. A felhasználónévnek [egyedi követelményeknek](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) kell megfelelnie.
   - **Hitelesítés típusa** - Válassza az **SSH nyilvános kulcsot.**
   - **SSH nyilvános kulcs** – RsA nyilvános kulcs másolása és beillesztése egysoros formátumban `ssh-rsa`(kezdve) vagy többsoros PEM formátumban. SSH kulcsokat ssh-keygen linuxos és macOS, vagy PuTTYGen windowsos puttygen használatával hozhat létre. Az SSH-kulcsokról és az Azure-ról további információt a [SSH-kulcsok használata az Azure-ban a Windows rendszerrel](/azure/virtual-machines/linux/ssh-from-windows)című cikkben talál.
   - **Előfizetés** – Válassza ki azt az Azure-előfizetést, amelybe telepíteni szeretné a Jenkinst.
   - **Erőforráscsoport** – Válassza **az Új létrehozása**lehetőséget, és adja meg annak az erőforráscsoportnak a nevét, amely a Jenkins-telepítést létrehozó erőforrások gyűjtésének logikai tárolójaként szolgál.
   - **Hely** - Válassza ki **az USA keleti részét.**

     ![Adja meg a Jenkins hitelesítési és erőforráscsoport-adatait az Alapszintű lapon.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. A **További beállítások** lapra való továbblépéshez válassza az **OK** gombot. 

1. A **További beállítások** lapon adja meg a következő értékeket:

   - **Méret** – Válassza ki a megfelelő méretezési lehetőséget a Jenkins virtuális gép.
   - **Virtuálisgép-lemez típusa** – Adja meg a HDD (merevlemez- meghajtó) vagy az SSD (SSD) értéket annak jelzésére, hogy melyik tárolólemez-típus engedélyezett a Jenkins virtuális gépen.
   - **Virtuális hálózat** - (Nem kötelező) Válassza ki a **virtuális hálózatot** az alapértelmezett beállítások módosításához.
   - **Alhálózatok** – Válassza **az Alhálózatok**lehetőséget, ellenőrizze az adatokat, és válassza az **OK**gombot.
   - **Nyilvános IP-cím** – Az IP-cím neve alapértelmezés szerint az előző lapon megadott Jenkins-névre , -IP utótaggal. Kiválaszthatja az alapértelmezett módosítás lehetőségét.
   - **Tartománynév-címke** – Adja meg a Jenkins virtuális gép teljesen minősített URL-címének értékét.
   - **Jenkins kiadási típus** - Válassza ki a `LTS` `Weekly build`kívánt `Azure Verified`kiadási típust a következő lehetőségek közül: , , vagy . A `LTS` `Weekly build` beállításokat a cikk ismerteti a [Jenkins LTS Release Line](https://jenkins.io/download/lts/). A `Azure Verified` beállítás egy [Jenkins LTS-verzióra](https://jenkins.io/download/lts/) hivatkozik, amely az Azure-ban való futtatásra lett ellenőrizve. 
   - **JDK típusa** - JDK kell telepíteni. Az alapértelmezett érték az OpenJDK Zulu által tesztelt, tanúsított buildje.

     ![Adja meg a Jenkins virtuálisgép-beállításait a Beállítások lapon.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Az **OK gombra** választva lépjen tovább az **Integrációs beállítások** lapra.

1. Az **Integrációs beállítások** lapon adja meg a következő értékeket:

    - **Egyszerű szolgáltatás** – A szolgáltatás névadó jave van hozzáadva a Jenkins hitelesítő adatokként az Azure-ral való hitelesítéshez. `Auto`azt jelenti, hogy a főtag az MSI (Managed Service Identity) által jön létre. `Manual`azt jelenti, hogy a megbízó kell létrehozni az Ön által. 
        - **Alkalmazásazonosító** és **titkos** – Ha `Manual` az Egyszerű szolgáltatás beállítás kiválasztásával választja `Application ID` `Secret` a **szolgáltatást,** meg kell adnia a és a szolgáltatásnévhez. Egyszerű [szolgáltatás létrehozásakor](/cli/azure/create-an-azure-service-principal-azure-cli)vegye figyelembe, hogy az alapértelmezett szerepkör **közreműködő**, amely elegendő az Azure-erőforrásokkal való munkához.
    - **Felhőügynökök engedélyezése** – Adja meg az `ACI` alapértelmezett felhősablont az `VM` ügynökök, ahol az Azure Container Instance hivatkozik, és virtuális gépekre hivatkozik. Azt is `No` megadhatja, ha nem szeretné engedélyezni a felhőalapú ügynök.

1. Az **OK gombra** választva lépjen tovább az **Összegzés** lapra.

1. Amikor megjelenik az **Összegzés** lap, a beírt adatok érvényesítésre kerülnek. Miután megjelenik az **Érvényesítés átadott** üzenet (a lap tetején), válassza az **OK gombot.** 

     ![Az Összegzés lap megjeleníti és érvényesíti a kiválasztott beállításokat.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Amikor megjelenik a **Létrehozás** lap, válassza a **Létrehozás** gombot a Jenkins virtuális gép létrehozásához. Amikor a kiszolgáló készen áll, egy értesítés jelenik meg az Azure Portalon.

     ![Jenkins készen áll az értesítésre.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)
