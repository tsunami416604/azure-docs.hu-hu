---
title: Az Azure alárendelt beépülő modul használata Hudson folyamatos integrációs |} A Microsoft Docs
description: Az Azure alárendelt beépülő modul használata Hudson folyamatos integrációs ismerteti.
services: virtual-machines-linux
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''
ms.assetid: b2083d1c-4de8-4a19-a615-ccc9d9b6e1d9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: ef24e356c9ac8424fc519a3b16af5d37a20e706f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999805"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Az Azure alárendelt beépülő modul használata Hudson folyamatos integrációs
Az Azure alárendelt beépülő modul Hudson lehetővé teszi, hogy amikor futó elosztott épít, helyezze üzembe az alárendelt csomópontok az Azure-ban.

## <a name="install-the-azure-slave-plug-in"></a>Az Azure alárendelt beépülő modul telepítése
1. A Hudson irányítópultján kattintson **kezelése Hudson**.
2. Az a **kezelése Hudson** lapon **beépülő modulok kezelése**.
3. Kattintson a **elérhető** fülre.
4. Kattintson a **keresési** , és írja be **Azure** korlátozására vonatkozó beépülő modulok listájáról.
   
    Amennyiben tekintse át a listát az elérhető beépülő modulok, megtalálja az Azure alárendelt beépülő modul alatt a **kiszolgálófürt-felügyelet és az elosztott hozhat létre** szakasz a **mások** fülre.
5. Jelölje be a **Azure alárendelt beépülő modul**.
6. Kattintson az **Install** (Telepítés) gombra.
7. Indítsa újra a Hudson.

Most, hogy a beépülő modult a következő lépések lenne, beállíthatja a beépülő modult az Azure-előfizetés profillal, és hozzon létre egy sablont, amely használható az alárendelt csomópont a virtuális gép létrehozását.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Az előfizetés-profil használatával az Azure alárendelt beépülő modul konfigurálása
Egy előfizetés profilt, is hivatkoznak, mivel a közzétételi beállítások, egy XML-fájlt, amely tartalmazza a biztonságos hitelesítő adatok és további információkra lesz szüksége az Azure-ral a fejlesztési környezetben működik. Az Azure alárendelt beépülő modul konfigurálásához lesz szüksége:

* Az előfizetés-azonosító
* Az előfizetéshez tartozó felügyeleti tanúsítvány

A találhatók a [előfizetési]. Alul látható egy példa egy előfizetés-profilt.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

          <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

              ServiceManagementUrl="https://management.core.windows.net"

              Id="<Subscription ID>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

Ha létrehozta a előfizetéshez profilját, kövesse az alábbi lépéseket az Azure alárendelt beépülő modul konfigurálásához.

1. A Hudson irányítópultján kattintson **kezelése Hudson**.
2. Kattintson a **rendszer konfigurálása**.
3. Görgessen lefelé az oldalon található a **felhőalapú** szakaszban.
4. Kattintson a **új felhő hozzáadása > Microsoft Azure**.
   
    ![új felhő hozzáadása][add new cloud]
   
    Így megjelennek a mezőket, meg kell adnia az előfizetése adataival.
   
    ![profil konfigurálása][configure profile]
5. Másolja az előfizetés-azonosító és felügyeleti tanúsítvány előfizetés profilját, és illessze be őket a megfelelő mezőkbe.
   
    Az előfizetési azonosító és felügyeleti tanúsítvány másolásakor **nem** az, hogy az értékek tegye idézőjelek közé tartozik.
6. Kattintson a **konfiguráció ellenőrzése**.
7. Ha a konfiguráció ellenőrzése sikeresen befejeződött, kattintson az **mentése**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Beállítása egy virtuálisgép-sablont az Azure alárendelt beépülő modul
Virtuálisgép-sablon meghatározza a paraméterek, a beépülő modul használatával hozzon létre egy alárendelt csomópont az Azure-ban. A következő lépések azt hoz létre sablont egy Ubuntu virtuális gép számára.

1. A Hudson irányítópultján kattintson **kezelése Hudson**.
2. Kattintson a **rendszer konfigurálása**.
3. Görgessen lefelé az oldalon található a **felhőalapú** szakaszban.
4. Belül a **felhőalapú** területén található **hozzáadása az Azure virtuálisgép-sablon** , és kattintson a **Hozzáadás** gombra.
   
    ![Virtuálisgép-sablon hozzáadása][add vm template]
5. Adja meg a felhőszolgáltatás neve az a **neve** mező. Ha egy meglévő felhőszolgáltatáshoz hivatkozik a megadott név, a virtuális gép jön létre a szolgáltatás. Ellenkező esetben az Azure létrehoz egy újat.
6. Az a **leírás** mezőben adja meg a létrehozandó sablon leírását. Ezt az információt csak dokumentációs célokat szolgál, és nem szerepel a VM-kiépítéshez.
7. Az a **címkék** írja be a következőt **linux**. Ez a címke a sablon létrehozásakor azonosítására szolgál, és ezt követően használja a sablon hivatkozni, a Hudson feladat létrehozásakor.
8. Válassza ki a régiót, ahol a virtuális gép létrejön.
9. Válassza ki a megfelelő Virtuálisgép-méretet.
10. Adjon meg egy tárfiókot, ahol a virtuális gép létrejön. Győződjön meg arról, hogy legyen-e ugyanabban a régióban, mint a felhőalapú szolgáltatást fogja használni. Ha azt szeretné, hogy a létrehozandó új tárterületre, hogy ezt a mezőt üresen hagyhatja.
11. Megőrzési idő megadja a ennyi perc elteltével Hudson törli az inaktív alárendelt. Hagyja meg ezt az alapértelmezett érték 60.
12. A **használati**, ha ez az alárendelt csomópont fogja használni, válassza ki a megfelelő feltétel. Most válassza ki a **kihasználhassák a lehető legnagyobb mértékben csomópont**.
    
     Ezen a ponton az űrlap ez némileg hasonlóan néz:
    
     ![sablon konfigurálása][template config]
13. A **azonosítója vagy kép termékcsalád** meg kell adnia, hogy milyen rendszerképet a virtuális gép lesz telepítve. A lemezkép-családok listájából válassza ki, vagy adjon meg egy egyéni rendszerképet.
    
     Ha azt szeretné kiválasztani a lemezkép-családok listája, adja meg a rendszerkép csomagcsalád nevének (kis-és nagybetűket) első karaktere. Például írja be **U** Ubuntu Server-családok listája megjelenik. Válassza ki a listából, ha a Jenkins a virtuális gép üzembe helyezésekor, hogy az operációs rendszer rendszerkép legújabb verzióját használja.
    
     ![Operációsrendszer-család listát][OS family list]
    
     Ha egy egyéni rendszerképet, amelyet használni szeretne, adja meg az egyéni rendszerkép nevét. Egyéni rendszerkép neve nem jelennek meg a listáját, így győződjön meg arról, hogy helyesen van-e meg a nevet kell.    
    
     A jelen oktatóanyag esetében írja be a **U** Ubuntu-rendszerképek listájának megjelenítéséhez, és válassza ki a **Ubuntu Server 14.04 LTS**.
14. A **indítási metódus**válassza **SSH**.
15. Másolja az alábbi parancsfájlt, és illessze be a **Init parancsfájl** mező.
    
         # Install Java
    
         sudo apt-get -y update
    
         sudo apt-get install -y openjdk-7-jdk
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y openjdk-7-jdk
    
         # Install git
    
         sudo apt-get install -y git
    
         #Install ant
    
         sudo apt-get install -y ant
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y ant
    
     A **Init parancsfájl** lesz végrehajtva a virtuális gép létrehozása után. Ebben a példában a parancsfájl telepíti, a Java, a git és a telepítsenek.
16. Az a **felhasználónév** és **jelszó** mezőknél adja meg a kívánt értékeket a rendszergazdai fiók, amely létrehozza a virtuális Gépen.
17. Kattintson a **sablon ellenőrzése** ellenőrizheti, ha a megadott paraméterek érvényesek.
18. Kattintson a **Mentés** gombra.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Az Azure-ban egy alárendelt csomóponton futó Hudson feladat létrehozása
Ebben a szakaszban Ön hoz létre egy Hudson feladat arról, hogy az egy alárendelt csomópont az Azure-ban.

1. A Hudson irányítópultján kattintson **új feladat**.
2. Adjon meg egy nevet a feladat létrehozásakor.
3. Válassza ki a feladattípus **hozhat létre egy ingyenes stílusú szoftverfrissítési feladat**.
4. Kattintson az **OK** gombra.
5. Válassza ki a feladat konfigurációs lapja **korlátozása, amelyben a projekt futtathatók**.
6. Válassza ki **csomópont és a felirat menü** válassza **linux** (beállítottunk ezt a címkét az előző szakaszban a virtuálisgép-sablon létrehozásakor).
7. Az a **hozhat létre** területén kattintson **felépítési lépés hozzáadása** válassza **hajtsa végre a rendszerhéj**.
8. Szerkessze a következő szkriptet, és cserélje le **{a github fiók neve}**, **{a projekt neve}**, és **{a projektkönyvtárba}** a megfelelő értékeket, és illessze be a szerkesztett a szöveg területen megjelenő parancsfájlt.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e {your project directory} ]; then
   
              cd {your project directory}
   
              git pull origin master
   
        else
   
              git clone https://github.com/{your github account name}/{your project name}.git
   
        fi
   
        # change directory to project
   
        cd $currentDir/{your project directory}
   
        #Execute build task
   
        ant
9. Kattintson a **Mentés** gombra.
10. A Hudson irányítópultján található a feladathoz létrehozott, majd kattintson a a **build ütemezése** ikonra.

Hudson Ezután hozzon létre egy alárendelt csomópont az előző szakaszban létrehozott sablon használatával, és hajtsa végre a lépést a feladathoz megadott parancsfájlt.

## <a name="next-steps"></a>További lépések
Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ].

<!-- URL List -->

[Azure Java fejlesztői központ]: https://azure.microsoft.com/develop/java/
[előfizetési]: https://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

