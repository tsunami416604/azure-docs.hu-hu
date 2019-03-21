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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124627"
---
1. A böngészőben nyissa meg a [Jenkins Azure Marketplace-beli rendszerképét](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Válassza ki **első most**.

    ![Válassza ki a GIT IT most már a Jenkins Piactéri lemezképhez a telepítési folyamat elindításához.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. A díjszabás részleteit és használati információk áttekintése után válassza ki a **Folytatás**.

    ![A Jenkins-piactér kép díjak és feltételek adatait.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Válassza ki **létrehozás** a Jenkins-kiszolgáló konfigurálása az Azure Portalon. 

    ![Telepítse a Jenkins Piactéri lemezképhez.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Az a **alapjai** lapra, adja meg a következő értékeket:

   - **Név** – adja meg `Jenkins`.
   - **Felhasználónév** – adja meg a felhasználónevet, a virtuális gép, amelyen fut a Jenkins való bejelentkezéskor használandó. A felhasználónévnek [egyedi követelményeknek](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) kell megfelelnie.
   - **Hitelesítési típus** – ki **nyilvános SSH-kulcs**.
   - **Nyilvános SSH-kulcs** – másolás és beillesztés nyilvános RSA-kulcsot az egysoros (kezdve `ssh-rsa`) vagy többsoros PEM formátumban. A Linux és macOS vagy Windows puttygen eszközzel az ssh-keygen eszközzel is létrehozhat. SSH-kulcsokat és az Azure kapcsolatos további információkért lásd: a cikk [az SSH-kulcsok az Azure-ban a Windows hogyan](/azure/virtual-machines/linux/ssh-from-windows).
   - **Előfizetés** – válassza ki az Azure-előfizetést, amelybe a Jenkins telepíteni szeretné.
   - **Erőforráscsoport** – ki **új létrehozása**, és adjon meg egy nevet az erőforráscsoport, amely a gyűjteményt, amely a Jenkins-telepítés alkotó erőforrások logikai tárolója.
   - **Hely** – ki **USA keleti Régiójában**.

     ![Az alapszintű lapon adja meg a Jenkins hitelesítés- és erőforrás csoport adatait.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Válassza ki **OK** lépjen a **további beállítás** fülre. 

1. Az a **további beállítás** lapra, adja meg a következő értékeket:

   - **Méret** – válassza ki a megfelelő méretezési lehetőséget a Jenkins virtuális gép.
   - **Virtuális merevlemez típusa** – adja meg vagy HDD (merevlemez-meghajtó) vagy SSD (SSD-meghajtóra), mely tárolólemez-típusba jelzi a Jenkins virtuális gép használata engedélyezett.
   - **Virtuális hálózat** – (nem kötelező) jelölje ki **virtuális hálózati** módosíthatja az alapértelmezett beállításokat.
   - **Alhálózatok** – ki **alhálózatok**, ellenőrizze az adatokat, és válassza ki **OK**.
   - **Nyilvános IP-cím** – az IP-cím neve alapértelmezés szerint a Jenkins - IP-cím utótaggal az előző lapon megadott név. Kiválaszthatja, hogy az alapértelmezett módosíthatja.
   - **Tartománynévcímke** -értéket adja meg a Jenkins virtuális gép teljes URL-címe.
   - **A Jenkins kiadási típusa** – a lehetőségek közül válassza ki a kívánt kiadási típusa: `LTS`, `Weekly build`, vagy `Azure Verified`. A `LTS` és `Weekly build` beállítások magyarázatát a cikk [Jenkins LTS kiadási sor](https://jenkins.io/download/lts/). A `Azure Verified` beállítás hivatkozik egy [Jenkins LTS verzióját](https://jenkins.io/download/lts/) , amely az Azure-on futtatott jóvá lett hagyva. 
   - **JDK típus** -JDK kell telepíteni. Alapértelmezés szerint a Zulu teszteltük, minősített buildjei openjdk csomagját.

     ![A beállítások lapon adja meg a Jenkins virtuális gép beállításait.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Válassza ki **OK** lépjen a **integrációs beállítások** fülre.

1. Az a **integrációs beállítások** lapra, adja meg a következő értékeket:

    - **Egyszerű szolgáltatás** – az egyszerű szolgáltatás adnak hozzá a Jenkins, a hitelesítés az Azure-ral hitelesítő adatot. `Auto` azt jelenti, hogy az egyszerű MSI (Felügyeltszolgáltatás-identitás) fogja létrehozni. `Manual` azt jelenti, hogy Ön az egyszerű kell létrehozni. 
        - **Alkalmazásazonosító** és **titkos** – Ha a `Manual` első számú megoldás a **egyszerű szolgáltatás** lehetőségnél kell adja meg a `Application ID` és `Secret` a a egyszerű szolgáltatás. Amikor [egyszerű szolgáltatás létrehozása](/cli/azure/create-an-azure-service-principal-azure-cli), vegye figyelembe, hogy az alapértelmezett szerepkör **közreműködői**, azaz elegendő az Azure-erőforrások használatára.
    - **Lehetővé teszik az ügynökök felhőalapú** -ügynökök esetében adja meg a felhő alapértelmezett sablon ahol `ACI` hivatkozik az Azure-Tárolópéldányon, és `VM` hivatkozik a virtuális gépek. Azt is megadhatja `No` Ha nem kívánja azt egy felhő-ügynök engedélyezése.

1. Válassza ki **OK** lépjen a **összefoglalás** fülre.

1. Ha a **összefoglalás** lapot jelenít meg, a megadott adatok érvényesítése. Ha már látja a **ellenőrzésen** üzenet (a lap tetején), válassza ki **OK**. 

     ![Az összefoglaló lapon jeleníti meg, és érvényesíti a kiválasztott beállításokat.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Ha a **létrehozás** lap megjelenik, válassza ki **létrehozás** a Jenkins virtuális gép létrehozásához. A kiszolgáló készen áll, amikor egy értesítés jeleníti meg az Azure Portalon.

     ![A Jenkins készen értesítési.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)
