---
title: "Azure virtuálisgép-ügynökök használata a Jenkins szolgáltatással fenntartott folyamatos integrációhoz."
description: "Azure virtuálisgép-ügynökök használata Jenkins alárendelt csomópontokként."
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: hu-hu
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>Azure virtuálisgép-ügynökök használata a Jenkins szolgáltatással fenntartott folyamatos integrációhoz.

Ez a gyors üzembe helyezési útmutató a Jenkins Azure virtuálisgép-ügynökhöz tartozó beépülő modul használatát ismerteti igény szerinti Linux (Ubuntu) ügynök létrehozásához az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Ha még nem rendelkezik Jenkins-főkiszolgálóval, kezdheti a [Megoldássablonnal](install-jenkins-solution-template.md) 
* Járjon el az [Azure-beli szolgáltatásnév létrehozása az Azure CLI 2.0-val](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) című cikk alapján, ha még nem rendelkezik Azure-beli szolgáltatásnévvel.

## <a name="install-azure-vm-agents-plugin"></a>Azure-beli virtuálisgép-ügynökhöz tartozó beépülő modul telepítése

Ha a [Megoldássablonból](install-jenkins-solution-template.md) indul ki, akkor az Azure-beli virtuálisgép-ügynökhöz tartozó beépülő modul a Jenkins-főkiszolgálón lesz telepítve.

Egyéb esetben telepítse az **Azure-beli virtuálisgép-ügynökhöz** tartozó beépülő modult a Jenkins irányítópultról.

## <a name="configure-the-plugin"></a>A beépülő modul konfigurálása

* A Jenkins irányítópulton kattintson a **Jenkins kezelése-> Rendszer konfigurálása->** lehetőségre. Görgessen a lap aljáig és keresse meg az **Új felhő hozzáadása** legördülő menüt. A menüből válassza a **Microsoft Azure-beli virtuálisgép-ügynökök** lehetőséget
* Válasszon egy létező fiókot az Azure-beli hitelesítő adatok legördülő listából.  Új **Microsoft Azure--beli egyszerű szolgáltatásnév** hozzáadásához adja meg a következő értékeket: előfizetés-azonosító, ügyfél-azonosító, titkos ügyfélkód és a OAuth 2.0 jogkivonat-végpont.

![Azure-beli Hitelesítő adatok](./media/jenkins-azure-vm-agents/service-principal.png)

* A **Konfiguráció ellenőrzése** lehetőségre kattintva győződjön meg a profil konfigurációjának helyességéről.
* Mentse a konfigurációt és haladjon tovább a következő lépéssel.

## <a name="template-configuration"></a>Sablon konfigurálása

### <a name="general-configuration"></a>Általános konfiguráció
Konfiguráljon egy sablont az Azure-beli virtuálisgép-ügynök definiálásához. 

* Adjon meg új sablont a **Hozzáadás** gombra kattintva. 
* Adja meg az új sablon nevét. 
* Címkeként írja be: "ubuntu". Ez a címke a feladat konfigurálása során használatos.
* A kombinált listából válassza ki a kívánt régiót.
* Válassza ki a virtuális gép kívánt méretét.
* Adja meg az Azure-tárfiók nevét, vagy hagyja üresen a mezőt az alapértelmezett "jenkinsarmst" név használatához.
* Adja meg a megőrzési időtartamot percekben mérve. Ez a beállítás határozza meg, hogy hány percet várhat a Jenkins egy tevékenység nélküli ügynök automatikus törlése előtt. Adjon meg 0 értéket, ha nem kívánja automatikusan töröltetni a tevékenység nélküli ügynököket.

![Általános konfiguráció](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>Rendszerkép-konfiguráció

Linux (Ubuntu) ügynök létrehozásához válassza a **Képhivatkozás** lehetőséget, majd használja példaként az alábbi konfigurációt. Az Azure által támogatott lemezképek legfrissebb listáját az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) találja meg.

* Lemezkép kiadója: Canonical
* Lemezkép tartalma: UbuntuServer
* Lemezkép termékváltozat: 14.04.5-LTS
* Lemezkép verzió: legfrissebb
* Operációs rendszer típusa: Linux
* Indítási metódus: SSH
* Rendszergazdai hitelesítő adatok megadása
* A virtuálisgép-inicializáló parancsprogram indításához írja be:
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![Rendszerkép-konfiguráció](./media/jenkins-azure-vm-agents/image-config.png)

* A konfiguráció ellenőrzéséhez kattintson a **Sablon ellenőrzése** gombra.
* Kattintson a **Save** (Mentés) gombra.

## <a name="create-a-job-in-jenkins"></a>Feladat létrehozása a Jenkinsben

* A Jenkins irányítópulton kattintson az **Új elem** lehetőségre. 
* Adjon meg egy nevet, válassza ki a **Freestyle projekt** lehetőséget, majd kattintson az **OK** gombra.
* Az **Általános** fülön válassza a "Projekt futási helyének korlátozása" lehetőséget, és a címke kifejezéshez írja be: "ubuntu". Az "ubuntu" ez után megjelenik a legördülő listában.
* Kattintson a **Save** (Mentés) gombra.

![Feladat beállítása](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>Új projekt kiépítése

* Térjen vissza a Jenkins irányítópultra.
* Kattintson jobb gombbal a létrehozott feladatra, majd kattintson a **kiépítés most** lehetőségre. Megkezdődik a fordítási folyamat. 
* A fordítás befejeződése után nyissa meg a **Konzolkimenetet**. Látható, hogy a fordítás távolról hajtódott végre az Azure-on.

![Konzolkimenet](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>Referencia

* Azure Friday videó: [Folyamatos integráció a Jenkins szolgáltatással Azure-beli virtuálisgép-ügynökök használatával](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents)
* Támogatási információ és konfigurációs lehetőségek: [Azure-beli virtuálisgép-ügynök Jenkins beépülő modul Wiki](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


