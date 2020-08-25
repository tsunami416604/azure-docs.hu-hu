---
title: Alkalmazás futtatása a Fortanix enklávé Managerrel
description: Ismerje meg, hogyan alakíthatja át a Fortanix enklávé Managert a tárolóban lévő képek átalakításához
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: 8996673cf8f01334e3dcd35aab8544643bd37360
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88802183"
---
# <a name="how-to-run-an-application-with-fortanix-enclave-manager"></a>Útmutató: alkalmazás futtatása a Fortanix enklávé Managerrel 

Indítsa el az alkalmazást az Azure bizalmas számítástechnikai szolgáltatásban az [Fortanix enklávé Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) és a [Fortanix Node Agent ügynök](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) használatával a [Fortanix](https://www.fortanix.com/)-ból.


A Fortanix egy külső gyártótól származó szoftvergyártó, amely Azure-infrastruktúrára épülő termékekkel és szolgáltatásokkal rendelkezik. Vannak más, az Azure-ban hasonló, bizalmas számítástechnikai szolgáltatásokat nyújtó külső szolgáltatók, például a [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) és a [Scone](https://sconedocs.github.io).  

> [!Note] 
 > A DOKUMENTUMBAN HIVATKOZOTT TERMÉKEK NEM A MICROSOFT FELÜGYELETE ALÁ TARTOZNAK. A MICROSOFT EZT AZ INFORMÁCIÓT KIZÁRÓLAG KÉNYELMI CÉLOKRA NYÚJTJA, ÉS A NEM MICROSOFT-TERMÉKEKRE VALÓ HIVATKOZÁS NEM A MICROSOFT JÓVÁHAGYÁSÁT JELENTI.



Ebből az oktatóanyagból megtudhatja, hogyan alakíthatja át az alkalmazás rendszerképét egy bizalmas, számítási védelemmel ellátott képre. Ez a környezet [Fortanix](https://www.fortanix.com/) szoftvert használ, amely az Azure DCsv2 sorozatú Intel SGX enklávéhoz-kompatibilis virtuális gépekkel rendelkezik. Ez a megoldás a kritikus biztonsági házirendeket, például az identitás-ellenőrzést és az adathozzáférés-vezérlést hangolja össze.

 A Fortanix támogatásához csatlakozzon a [Fortanix Slack közösséghez](https://fortanix.com/community/) , és használja a Channel #enclavemanager.


## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Fortanix enklávé Manager-fiókkal, akkor a Kezdés előtt [regisztráljon](https://em.fortanix.com/auth/sign-up) .
1. Privát [Docker](https://docs.docker.com/) -beállításjegyzék a konvertált alkalmazások lemezképének leküldéséhez.
1. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Az ingyenes próbaverziós fiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Váltson utólagos elszámolású előfizetésre.

## <a name="add-an-application-to-fortanix-enclave-manager"></a>Alkalmazás hozzáadása a Fortanix enklávé Managerhez
1. Bejelentkezés a [FORTANIX em](https://fortanix.com) -be
1. Az új fiók létrehozásához navigáljon a **fiókok** lapra, és válassza a **fiók hozzáadása** lehetőséget. 
    
[!div class="mx-imgBorder"]
![Fiók létrehozása](media/how-to-fortanix-enclave-manager/create-account.png)

1. A fiók létrehozása után nyomja meg a **kijelölés lehetőséget** az újonnan létrehozott fiók kiválasztásához. Most már elkezdheti a számítási csomópontok regisztrálását és az alkalmazások létrehozását. 
1. Alkalmazás hozzáadásához kattintson a **+ alkalmazás** gombra. Ebben a példában a lombik-kiszolgáló enklávé OS-alkalmazást vesszük fel. 

1. Válassza az enklávé operációs rendszer alkalmazás **Hozzáadás** gombját. 

    ![Alkalmazás hozzáadása](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > Ez az oktatóanyag csak az enklávé OS-alkalmazások hozzáadását ismerteti. [Tudjon meg többet](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Enclave-Manager) az EDP rozsda-alkalmazások Fortanix enklávé Managerbe való bevezetéséről. 

6. Ebben az oktatóanyagban a Fortanix Docker-beállításjegyzékét fogjuk használni a minta alkalmazáshoz. Adja meg a részleteket az alábbi információk alapján. A kimeneti rendszerkép megtartásához használja a privát Docker-beállításjegyzéket. 
 
    - **Alkalmazás neve**: Python Application Server
    - **Leírás**: Python-lombik kiszolgálója
    - **Bemeneti rendszerkép neve**: fortanix/Python-lombik
    - **Kimeneti rendszerkép neve**: fortanx-Private/Python-lombik-SGX enklávéhoz
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Memória mérete**: 1 GB
    - **Szálak száma**: 128

    Nem *kötelező*: futtassa az alkalmazást.
    - **Docker hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **Alkalmazás**: fortanix/Python-lombik

        Futtassa a következő parancsot:
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. Tanúsítvány hozzáadása. Adja meg az adatokat az alábbi adatok alapján, majd válassza a **Next (tovább**) gombot:
    - **Tartomány**: SajátPr. domain. Dom
    - **Típus**: az enklávé Manager által kiállított tanúsítvány 
    - **Kulcs elérési útja**:/appkey.PEM
    - **Kulcs típusa**: RSA
    - **Tanúsítvány elérési útja**:/appcert.PEM
    - **RSA-kulcs mérete**: 2048 bit
    

## <a name="create-an-image"></a>Rendszerkép létrehozása
A Fortanix EM-rendszerkép egy alkalmazás szoftveres kiadása vagy verziója. Mindegyik rendszerkép egy enklávé-kivonattal (MRENCLAVE) van társítva. 
1. A **rendszerkép hozzáadása** lapon adja meg a **kimeneti rendszerkép nevének** **beállításjegyzékbeli hitelesítő adatait** . Ezek a hitelesítő adatok a privát Docker-beállításjegyzék elérésére szolgálnak, ahol a rendszer leküldi a rendszerképet. 

    ![rendszerkép létrehozása](media/how-to-fortanix-enclave-manager/create-image.png)
1. Adja meg a rendszerkép címkéjét, és válassza a **Létrehozás**lehetőséget.

    ![címke hozzáadása](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>Tartomány és rendszerkép engedélyezési listája 
Egy alkalmazás, amelynek tartománya hozzá lett adva az engedélyezési listához, a Fortanix enklávé Manager TLS-tanúsítványát fogja kapni. Hasonlóképpen, ha egy alkalmazás a konvertált rendszerképből fut, akkor megpróbál kapcsolatba lépni a Fortanix enklávé kezelőjével. Az alkalmazás ezután kérni fogja a TLS-tanúsítványt. 

Váltson a **feladatok** lapra a bal oldalon, és hagyja jóvá a függőben lévő kéréseket a tartomány és a rendszerkép engedélyezéséhez. 

## <a name="enroll-compute-node-agent-in-azure"></a>Számítási csomópont-ügynök regisztrálása az Azure-ban

### <a name="generate-and-copy-join-token"></a>Csatlakozási jogkivonat előállítása és másolása
A Fortanix enklávé kezelőjében létre kell hoznia egy jogkivonatot. Ez a token lehetővé teszi, hogy az Azure-beli számítási csomópont hitelesítse magát. Ezt a tokent meg kell adnia az Azure-beli virtuális géphez.
1. A felügyeleti konzolon válassza a **+ csomópont regisztrálása** gombot. 
1. Az illesztési jogkivonat létrehozásához válassza a **jogkivonat** előállítása lehetőséget. Másolja a tokent.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Csomópontok regisztrálása a Fortanix-csomóponti Ügynökbe az Azure Marketplace-en

A Fortanix-csomópontok létrehozásakor a rendszer egy virtuális gépet, egy hálózati adaptert, egy virtuális hálózatot, egy hálózati biztonsági csoportot és egy nyilvános IP-címet helyez üzembe az Azure-erőforráscsoporthoz. Azure-előfizetését óránként számlázjuk a virtuális géphez. A Fortanix csomópont-ügynök létrehozása előtt tekintse át az Azure [virtuális gépek díjszabási lapját](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a DCsv2 sorozathoz. Ha nincs használatban, törölje az Azure-erőforrásokat. 

1. Nyissa meg az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) -t, és jelentkezzen be az Azure-beli hitelesítő adataival.
1. A keresősáv mezőbe írja be a **Fortanix bizalmas számítástechnikai csomópont-ügynök**kifejezést. Válassza ki azt az alkalmazást, amely megjelenik a **Fortanix bizalmas számítástechnikai csomópont-ügynök** nevű keresőmezőben az ajánlat kezdőlapjának megnyitásához. 
     ![Keresés a piactéren](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. Válassza a **Letöltés most**lehetőséget, adja meg az adatokat, ha szükséges, majd kattintson a **Folytatás**gombra. A rendszer átirányítja a Azure Portal. 
1. Válassza a **Létrehozás** lehetőséget a Fortanix bizalmas számítástechnikai csomópont-ügynök üzembe helyezési lapjának megadásához.
1. Ezen a lapon megadhatja a virtuális gép üzembe helyezéséhez szükséges adatokat. Pontosabban, ez a virtuális gép egy DCsv2 sorozatú, Intel SGX ENKLÁVÉHOZ-kompatibilis virtuális gép az Azure-ban, amelyen telepítve van az Fortanix Node Agent szoftver. A csomópont-ügynök lehetővé teszi, hogy az átalakított rendszerkép biztonságosan fusson az Azure-beli Intel SGX ENKLÁVÉHOZ-csomópontokon.  Válassza ki azt az **előfizetést** és **erőforráscsoportot** , ahol telepíteni szeretné a virtuális gépet és a kapcsolódó erőforrásokat. 
 
    > [!NOTE]
    > A DCsv2-sorozatú virtuális gépek az Azure-ban való üzembe helyezése megkötést eredményez. Előfordulhat, hogy további magokra vonatkozó kvótát kell igényelnie. További információt az Azure-beli [virtuális gépeken található bizalmas számítástechnikai megoldásokról szóló](https://docs.microsoft.com/azure/confidential-computing/virtual-machine-solution) témakörben olvashat. 

1. Válasszon a rendelkezésre álló régiók közül.
1. A **csomópont neve** mezőben adja meg a virtuális gép nevét. 
1. Adja meg a és a username és a Password (vagy SSH-kulcs) értéket a virtuális géphez való hitelesítéshez.
1. Hagyja meg az operációsrendszer-lemez alapértelmezett méretét 200-ként, és válassza ki a virtuálisgép-méretet (Standard_DC4s_v2 elegendő ehhez az oktatóanyaghoz)
1. Illessze be a korábban generált tokent az **összekapcsolási jogkivonat** mezőbe.

     ![erőforrás üzembe helyezése](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent.png)

1. Válassza a **felülvizsgálat + létrehozás**lehetőséget. Győződjön meg arról, hogy az ellenőrzés megfelel, majd válassza a **Létrehozás**lehetőséget. Az összes erőforrás üzembe helyezése után a számítási csomópont regisztrálva lesz az enklávé Managerben. 

## <a name="run-the-application-image-on-the-compute-node"></a>Az alkalmazás rendszerképének futtatása a számítási csomóponton
Futtassa az alkalmazást az alábbi parancs végrehajtásával. Győződjön meg arról, hogy a csomópont IP-címét, a portot és a konvertált lemezképfájlt bemenetként módosítja az adott alkalmazáshoz. 
 
Ebben az oktatóanyagban a végrehajtandó parancs a következő:     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

ahol 
- a *52.152.206.164* a csomóponti ügynök gazdagépének IP-címe
- *9092* a csomópont-ügynök által figyelt port
- *fortanix-Private/Python-lombik-SGX enklávéhoz* a konvertált alkalmazás, amely a Fortanix enklávé **képek** lapján, a lemezképek lapon, a **rendszerkép neve oszlopban található images** (kép neve) oszlopában található. 
    
## <a name="verify-and-monitor-the-running-application"></a>A futó alkalmazás ellenőrzése és figyelése
1. Térjen vissza a [Fortanix enklávé kezelőjéhez](https://em.fortanix.com/console)
1. Győződjön meg arról, hogy azon a **fiókon** belül dolgozik, ahová a csomópontot regisztrálta
1. Navigáljon a **felügyeleti konzolra** a bal oldali navigációs ablaktáblán a felső ikon kiválasztásával. 
1. Az **alkalmazás** lap kiválasztása
1. Ellenőrizze, hogy van-e futó alkalmazás társítva egy társított számítási csomóponttal


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforráscsoportot, a virtuális gépet és a kapcsolódó erőforrásokat. Az erőforráscsoport törlésével törli a konvertált rendszerképhez társított csomópontok regisztrációját. 

Válassza ki a virtuális géphez tartozó erőforráscsoportot, majd válassza a **Törlés**lehetőséget. Erősítse meg az erőforráscsoport nevét az erőforrások törlésének befejezéséhez. \

A létrehozott Fortanix enklávé-kezelő fiók törléséhez nyissa meg az enklávé-kezelő [fiókok lapját](https://em.fortanix.com/accounts) . Vigye a kurzort a törölni kívánt fiók fölé. Válassza a jobb felső sarokban található függőleges fekete pontokat, és válassza a **fiók törlése**lehetőséget.

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a Fortanix-eszközök használatával alakíthatja át az alkalmazás rendszerképét, hogy az a bizalmas számítástechnikai virtuális gépen fusson. Az Azure-beli bizalmas számítástechnikai virtuális gépekkel kapcsolatos további információkért lásd: [Virtual Machines-megoldások](virtual-machine-solutions.md). 

További információ az Azure bizalmas számítástechnikai kínálatáról: az [Azure bizalmas számítástechnika áttekintése](overview.md)

