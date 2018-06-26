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
ms.openlocfilehash: d5a832909f060ad8c8b3f0e7c7ea4504e5e5aadb
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943470"
---
1. A böngészőben nyissa meg a [Jenkins Azure piactér kép](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Válassza ki **LEKÉRÉSE most informatikai**.

    ![Válassza ki a GIT informatikai most a Jenkins Piactéri lemezképhez a telepítési folyamat elindításához.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Árképzési részleteit és a használati információk áttekintése után válassza ki a **Folytatás**.

    ![Jenkins Piactéri lemezkép árak és használati adatokat.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Válassza ki **létrehozása** a Jenkins kiszolgáló konfigurálása az Azure portálon. 

    ![Telepítse a Jenkins Piactéri lemezképhez.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Az a **alapjai** lapra, adja meg a következő értékeket:

    - **Név** -meg `Jenkins`.
    - **Felhasználónév** -adja meg a felhasználónevet, a virtuális gép, amelyen Jenkins fut. történő bejelentkezéskor használni. A felhasználónévnek [egyedi követelményeknek](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) kell megfelelnie.
    - **Hitelesítés típusa** – Itt adhatja meg **nyilvános SSH-kulcs**.
    - **Nyilvános SSH-kulcs** -másolási és beillesztési műveleteket egy RSA nyilvános kulcsot egysoros formátumban (kezdve `ssh-rsa`) vagy többsoros PEM-formátumba. Ssh-keygen használata a Linux és a macOS vagy a windowsos puttygen eszközzel készíthet SSH-kulcsokat hozhat létre. SSH-kulcsok és Azure kapcsolatos további információkért lásd: a cikk [az SSH-kulcsok a Windows Azure hogyan](/azure/virtual-machines/linux/ssh-from-windows).
    - **Előfizetés** -válassza ki az Azure-előfizetés amelybe Jenkins telepíteni szeretné.
    - **Erőforráscsoport** – Itt adhatja meg **hozzon létre új**, és adja meg a gyűjtemény-erőforrások Jenkins telepítésének alkotó logikai tárolója látja, hogy az erőforráscsoport nevét.
    - **Hely** – Itt adhatja meg **USA keleti régiója**.

    ![Adja meg a hitelesítés és az erőforrás csoport információkat Jenkins alapvető lapján.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Válassza ki **OK** folytassa a **további beállításokat** fülre. 

1. Az a **további beállításokat** lapra, adja meg a következő értékeket:

    - **Méret** -válassza ki a Jenkins virtuális gép megfelelő méretezési beállítását.
    - **Virtuális gép lemeztípus** : Adja meg vagy HDD (merevlemez-meghajtóra), vagy a Jenkins virtuális gép számára engedélyezve van az SSD (SSD-meghajtóra), milyen típusú jelzi.
    - **Virtuális hálózati** -(nem kötelező) jelölje ki **virtuális hálózati** az alapértelmezett beállítások módosítása.
    - **Alhálózatok** – Itt adhatja meg **alhálózatok**, ellenőrizze az adatokat, és válassza ki **OK**.
    - **Nyilvános IP-cím** -az IP-cím neve alapértelmezés szerint az IP - utótaggal az előző lapon megadott Jenkins neve. Kiválaszthatja, hogy az alapértelmezett módosításának lehetőségét.
    - **Tartománynév-címke** -értéket adja meg a teljes URL-címének a Jenkins virtuális gép.
    - **Jenkins kiadás típusa** -válassza ki a kívánt kiadási típusainak a lehetőségek közül: `LTS`, `Weekly build`, vagy `Azure Verified`. A `LTS` és `Weekly build` beállítások magyarázatát a cikk [Jenkins LTS kiadási sor](https://jenkins.io/download/lts/). A `Azure Verified` beállítás hivatkozik egy [Jenkins LTS verzió](https://jenkins.io/download/lts/) , amely az Azure-on futtatásához ellenőrzése megtörtént. 

    ![Adja meg a virtuális gép beállításait Jenkins beállítások lapján.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Válassza ki **OK** folytassa a **integrációs beállítások** fülre.

1. Az a **integrációs beállítások** lapra, adja meg a következő értékeket:

    - **Szolgáltatás egyszerű** – az egyszerű szolgáltatás szerepel-e Jenkins, a hitelesítés az Azure hitelesítő adatait. `Auto` azt jelenti, hogy a rendszerbiztonsági tag MSI (felügyelt Szolgáltatásidentitás) fogja létrehozni. `Manual` azt jelenti, hogy a rendszerbiztonsági tag is létre kell azt. 
        - **Alkalmazásazonosító** és **titkos** - választásakor a `Manual` választás, a **egyszerű** beállítás, szüksége lesz adja meg a `Application ID` és `Secret` a a egyszerű szolgáltatást. Amikor [egyszerű szolgáltatás létrehozása](/cli/azure/create-an-azure-service-principal-azure-cli), vegye figyelembe, hogy az alapértelmezett szerepkör **közreműködő**, amely elegendő az Azure-erőforrások használata.
    - **Lehetővé teszik az ügynökök felhő** -adja meg a felhő alapértelmezett sablon ügynökök ahol `ACI` hivatkozik az Azure-tároló példányt, és `VM` virtuális gépek hivatkozik. Azt is megadhatja, `No` Ha nem kíván a felhő ügynök engedélyezése.

1. Válassza ki **OK** folytassa a **összegzés** fülre.

1. Ha a **összegzés** lapon megjelenik, a megadott adatok ellenőrzését. Miután látja a **ellenőrzésen** üzenet (a lap tetején), válassza ki **OK**. 

    ![Az összefoglaló lapon jeleníti meg, és érvényesíti a kiválasztott beállításokat.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Ha a **létrehozása** lapon megjelenik, válassza ki **létrehozása** a Jenkins virtuális gép létrehozásához. A kiszolgáló készen áll, amikor egy értesítés jeleníti meg az Azure-portálon.

    ![Jenkins az készen értesítést.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)