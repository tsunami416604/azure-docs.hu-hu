---
title: "Az első Jenkins Master létrehozása Linux (Ubuntu) virtuális gépen az Azure-ban"
description: "Használja a megoldássablont a Jenkins üzembe helyezéséhez."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: f892ec7bd61124f7958a50ebdfb49c7310d4ee18
ms.contentlocale: hu-hu
ms.lasthandoff: 08/01/2017

---

# <a name="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure"></a>Az első Jenkins Master létrehozása Linux (Ubuntu) virtuális gépen az Azure-ban

Ez a gyorsútmutató bemutatja, hogyan telepítheti a legújabb stabil Jenkins-verziót Linux (Ubuntu 14.04 LTS) virtuális gépre, valamint az Azure-ral való munkavégzésre konfigurált eszközöket és beépülő modulokat. Az eszközök a következőket foglalják magukban:
<ul>
<li>Git a verziókövetéshez</li>
<li>Azure-hitelesítő beépülő modul a biztonságos kapcsolódáshoz</li>
<li>Azure virtuálisgép-ügynökök beépülő modul a rugalmas buildkezeléshez, teszteléshez és folyamatos integrációhoz</li>
<li>Azure Storage beépülő modul a munkadarabok tárolásához</li>
<li>Azure CLI az alkalmazások üzembe helyezéséhez szkriptekkel</li>
</ul>

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Ingyenes Azure-fiók létrehozása.
> * Jenkins Master létrehozása Azure virtuális gépen megoldássablonnal. 
> * A Jenkins kezdeti konfigurálásának elvégzése.
> * Javasolt beépülő modulok telepítése.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins"></a>A virtuális gép létrehozása az Azure-ban a megoldássablon Jenkinshez történő telepítésével

Az Azure gyorsindítási sablonjai lehetővé teszik a bonyolult technológiák gyors és megbízható bevezetését az Azure-on.  Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy [deklaratív sablon](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins) használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.

Tekintse meg a sablonhoz a [csomag- és árképzési](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.jenkins?tab=PlansAndPrice) információt, hogy megtudja, milyen költségekkel számolhat.

Lépjen a [A Jenkins képe a Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview) lapra, kattintson a **LETÖLTÉS MOST** gombra.  

Az Azure Portalon kattintson a **Létrehozás** gombra.  Ehhez a sablonhoz a Resource Manager használata szükséges, ezért a sablonmodell legördülő menüje le van tiltva.
   
![Azure Portal párbeszédpanel](./media/install-jenkins-solution-template/ap-create.png)

**Az alapvető beállítások konfigurálása** lapon:

![Az alapvető beállítások konfigurálása](./media/install-jenkins-solution-template/ap-basic.png)

* Adja meg egy nevet a Jenkins-példányhoz.
* Válasszon egy virtuálisgép-lemeztípust.  A termelési számítási feladatokhoz válasszon egy nagyobb méretű virtuális gépet és SSD-t a jobb teljesítmény érdekében.  Az Azure-lemeztípusokkal kapcsolatos további információt [itt](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) olvashat.
* Felhasználónév: meg kell felelnie a hosszúsági követelményeknek, és nem tartalmazhat fenntartott szavakat vagy nem támogatott karaktereket. A nevek, például a „rendszergazda” használata nem megengedett.  A felhasználónévvel és jelszóval kapcsolatos követelményekről további információt [itt](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq) talál.
* Hitelesítési típus: hozzon létre egy jelszóval vagy [nyilvános SSH-kulccsal](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows) védett példányt. Ha jelszót használ, annak meg kell felelnie a következő követelmények közül háromnak: legalább egy kisbetű, egy nagybetű, egy szám és egy speciális karakter.
* A Jenkins kiadási típusa legyen **LTS**
* Válasszon egy előfizetést.
* Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévő, üres erőforráscsoportot. 
* Válasszon ki egy helyet.

A **További beállítások konfigurálása** lapon:

![További beállítások megadása](./media/install-jenkins-solution-template/ap-addtional.png)

* Adjon meg egy tartománynévcímkét a Jenkins-főkiszolgáló egyedi azonosításához.

Kattintson az **OK** gombra a továbblépéshez. 

Az ellenőrzés elvégzése után kattintson az **OK** gombra a sablon és a paraméterek letöltéséhez. 

Következőként az összes erőforrás kiépítéséhez válassza a **Vásárlás** lehetőséget.

## <a name="setup-ssh-port-forwarding"></a>SSH-porttovábbítás beállítása

Alapértelmezés szerint a Jenkins-példány a HTTP protokollt használja, és a 8080-as portot figyeli. A felhasználók ne végezzenek hitelesítést nem biztonságos protokollokkal.
    
Állítson be porttovábbítást a Jenkins felhasználói felületének a helyi gépen való megtekintéséhez.

### <a name="if-you-are-using-windows"></a>Ha Windowst használ:

Telepítse a PuTTYt, és futtassa ezt a parancsot, ha jelszóval védi a Jenkinst:
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* A bejelentkezéshez adja meg a jelszót.

![A bejelentkezéshez adja meg a jelszót.](./media/install-jenkins-solution-template/jenkins-pwd.png)

Ha SSH-t használ, futtassa ezt a parancsot:
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

### <a name="if-you-are-using-linux-or-mac"></a>Ha Linuxot vagy Macet használ:

Ha jelszót használ a Jenkins-főkiszolgáló védelmére, futtassa ezt a parancsot:
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* A bejelentkezéshez adja meg a jelszót.

Ha SSH-t használ, futtassa ezt a parancsot:
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

## <a name="connect-to-jenkins"></a>Kapcsolódás a Jenkinshez
Az alagút elindítása után lépjen a http://localhost:8080/ címre a helyi gépen.

Először a kezdeti rendszergazdai jelszóval oldja fel a Jenkins-irányítópult zárolását.

![Jenkins zárolásának feloldása](./media/install-jenkins-solution-template/jenkins-unlock.png)

A jogkivonat beszerzéséhez jelentkezzen be SSH-n keresztül a virtuális gépbe, és futtassa a `sudo cat /var/lib/jenkins/secrets/initialAdminPassword` parancsot.

![Jenkins zárolásának feloldása](./media/install-jenkins-solution-template/jenkins-ssh.png)

A rendszer megkéri a javasolt beépülő modulok telepítésére.

A következő lépésben hozzon létre egy rendszergazdai felhasználót a Jenkins-főkiszolgálóhoz.

A Jenkins-példánya most már készen áll a használatra! A csak olvasható nézet eléréséhez lépjen a http://\<A most létrehozott példány nyilvános DNS-neve\> címre.

![Jenkins készen áll!](./media/install-jenkins-solution-template/jenkins-welcome.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * Létrehozott egy Jenkins-főkiszolgálót a megoldássablonnal.
> * Elvégezte a Jenkins kezdeti konfigurációját.
> * Beépülő modulokat telepített.

Ezt a hivatkozást követve megtudhatja, hogyan használja az Azure virtuálisgép-ügynököket a Jenkins szolgáltatással fenntartott folyamatos integrációhoz.

> [!div class="nextstepaction"]
> [Azure virtuális gépek, mint Jenkins-ügynökök](jenkins-azure-vm-agents.md)

