---
title: Az Azure alárendelt beépülő modul használata Hudson folyamatos integrációt |} Microsoft Docs
description: Ismerteti, hogyan használható az Azure alárendelt beépülő modul Hudson folyamatos integrációt.
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
ms.openlocfilehash: c11b59f8ea432075b147a391de4b7bd3331e639e
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
ms.locfileid: "27704803"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Az Azure alárendelt beépülő modul használata Hudson folyamatos integrációt
Az Azure alárendelt Hudson beépülő modul lehetővé teszi, hogy hozzon létre az alárendelt csomópontok az Azure-on, amikor fut elosztott alkot.

## <a name="install-the-azure-slave-plug-in"></a>Az Azure alárendelt beépülő modul telepítése
1. Hudson irányítópultján kattintson **kezelése Hudson**.
2. Az a **kezelése Hudson** lapján kattintson a **kezelése beépülő modulok**.
3. Kattintson a **elérhető** fülre.
4. Kattintson a **keresési** és típus **Azure** vonatkozó beépülő modulokhoz-lista korlátozását.
   
    Ha úgy dönt, ha az elérhető beépülő modulok listáját megtalálja az Azure alárendelt a beépülő modul a **kiszolgálófürt-felügyelet és az elosztott Build** szakasz a **mások** lapon.
5. Jelölje be a **Azure alárendelt beépülő modul**.
6. Kattintson az **Install** (Telepítés) gombra.
7. Indítsa újra a Hudson.

Most, hogy a beépülő modul telepítve van, a következő lépések lenne, az Azure-előfizetés profillal beállíthatja a beépülő modult, és az alárendelt csomópont a virtuális gép létrehozásához használt sablon létrehozása.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Az előfizetés profillal az Azure alárendelt beépülő modul konfigurálása
Egy előfizetés profilt is hívják közzétételi beállítások, a biztonságos hitelesítő adatok, és további információkra szüksége lesz a fejlesztési környezetet az Azure-ral működni tartalmazó XML-fájl. Az Azure alárendelt beépülő modul konfigurálásához lesz szüksége:

* Az előfizetés-azonosítóval
* Az előfizetéshez tartozó felügyeleti tanúsítvány

Ezek itt található: a [előfizetés profil]. Az alábbiakban az előfizetés profil egy példája.

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

Miután az előfizetés profil, kövesse az alábbi lépéseket az Azure alárendelt beépülő modul konfigurálása.

1. Hudson irányítópultján kattintson **kezelése Hudson**.
2. Kattintson a **rendszer konfigurálása**.
3. Görgessen lefelé a lapon található a **felhő** szakasz.
4. Kattintson a **adja hozzá az új felhő > Microsoft Azure**.
   
    ![új felhőalapú hozzáadása][add new cloud]
   
    Ez azt mutatja majd a mezők ahol meg kell adnia az előfizetés részletei.
   
    ![profil konfigurálása][configure profile]
5. Az előfizetési azonosító és felügyeleti tanúsítvány a előfizetés profilból másolással illessze be őket a megfelelő mezőket.
   
    Az előfizetési azonosító és felügyeleti tanúsítvány másolásakor **nem** értékek tegye idézőjelek közé tartozik.
6. Kattintson a **ellenőrizze konfigurációs**.
7. Ha a konfiguráció ellenőrzése sikeresen befejeződött, kattintson **mentése**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Beállítása virtuálisgép-sablont az Azure alárendelt beépülő modul
Virtuálisgép-sablon a paraméterek, a beépülő modul segítségével létrehozhat egy alárendelt csomópont az Azure-határozza meg. A következő lépések azt fogja kell sablon létrehozása egy Ubuntu virtuális gép számára.

1. Hudson irányítópultján kattintson **kezelése Hudson**.
2. Kattintson a **rendszer konfigurálása**.
3. Görgessen lefelé a lapon található a **felhő** szakasz.
4. Belül a **felhő** területen található **Azure virtuálisgép-sablon hozzáadása** , és kattintson a **Hozzáadás** gombra.
   
    ![Virtuálisgép-sablon hozzáadása][add vm template]
5. Adja meg a felhőalapú szolgáltatás nevét a **neve** mező. Ha a megadott név egy meglévő felhőszolgáltatáshoz hivatkozik, a virtuális gép lesz üzembe helyezve, hogy a szolgáltatásban. Ellenkező esetben az Azure létrehoz egy új.
6. Az a **leírás** mezőben adja meg a létrehozandó sablon leírását. Ezek az információk csak dokumentációs célokat szolgál, és nem szerepel a virtuális gép kiépítése.
7. Az a **címkék** mezőbe írja be **linux**. Ez a címke alapján határozza meg a sablon létrehozásakor és való hivatkozáshoz a sablon, egy Hudson feladat létrehozásakor ezt követően használható.
8. Válasszon ki egy régiót, ahol a virtuális gép létrejön.
9. Válassza ki a megfelelő Virtuálisgép-méretet.
10. Adjon meg egy tárfiókot, ahol a virtuális gép létrejön. Győződjön meg arról, hogy a felhőszolgáltatás fogja használni és ugyanabban a régióban van. Ha a létrehozandó új tárhelyre, akkor ezt a mezőt üresen hagyhatja.
11. Megőrzési időtartama percben, mielőtt Hudson törli az inaktív alárendelt határozza meg. Adja meg ezt az alapértelmezett értéket 60.
12. A **használati**, válassza ki azt a megfelelő állapotot, ha ez az alárendelt csomópont fogja használni. Most, válassza ki a **használata a lehető legnagyobb mértékben csomópont**.
    
     Ezen a ponton az űrlap ez némileg hasonló lenne:
    
     ![sablon config][template config]
13. A **kép termékcsalád vagy azonosító** meg kell adnia, hogy milyen rendszerkép lesz telepítve a virtuális Gépet. Válassza ki a lemezkép termékcsaládok listáját, vagy adjon meg egyéni lemezképet.
    
     Ha azt szeretné kiválasztani a lemezkép-családok listája, adja meg a lemezkép csomagcsalád nevének (kis-és nagybetűket) karaktert. Például írja be **U** Ubuntu Server családok listája megjelenik. Után válassza ki a listából, a Jenkins fogja használni, hogy az operációs rendszer lemezkép legújabb verzióját, ha a virtuális gép kiépítésétől.
    
     ![Az operációs rendszer termékcsalád listája][OS family list]
    
     Ha a használni kívánt egyéni lemezképet, adja meg az egyéni lemezkép nevét. Egyéni rendszerkép neve nem jelennek meg listáját, így kell győződjön meg arról, hogy a neve helyesen van-e megadva.    
    
     Ebben az oktatóanyagban írja be a következőt **U** Ubuntu lemezképek listáját és kiválasztása **Ubuntu Server 14.04 LTS**.
14. A **indítsa el a metódus**, jelölje be **SSH**.
15. Az alábbi parancsfájl másolja és illessze be a **Init parancsfájl** mező.
    
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
    
     A **Init parancsfájl** végrehajtja a virtuális gép létrehozása után. Ebben a példában a parancsfájl telepíti, Java, a git és az telepítsenek.
16. Az a **felhasználónév** és **jelszó** mezők, adja meg az előnyben részesített értékeket rendel a virtuális Gépen lévő rendszergazdai fiók.
17. Kattintson a **sablon ellenőrzése** ellenőrzése, ha a megadott paraméterek érvényesek.
18. Kattintson a **Mentés** gombra.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Egy alárendelt csomópont az Azure-on futó Hudson feladat létrehozása
Ebben a szakaszban egy alárendelt csomópont az Azure-on futó Hudson feladat csak létrehozunk.

1. Hudson irányítópultján kattintson **új feladat**.
2. Adja meg a létrehozandó feladat nevét.
3. Válassza ki a feladattípus **egy ingyenes stílusú szoftver feladat létrehozása**.
4. Kattintson az **OK** gombra.
5. A feladat konfigurálása lapon válassza ki a **korlátozása, amelyben ez a projekt futtathatók**.
6. Válassza ki **csomópont és a felirat menü** válassza **linux** (azt meg ezt a címkét a virtuális gép sablon létrehozásakor az előző szakaszban).
7. A a **Build** területen kattintson **Hozzáadás összeállítása lépés** válassza **hajtható végre a rendszerhéj**.
8. Szerkessze a következő parancsfájl cseréje **{a githubon fióknevet}**, **{a projekt neve}**, és **{projektkönyvtárban}** a megfelelő értékeket, és illessze be a szerkesztett a szöveg területen megjelenő parancsfájl.
   
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
10. A Hudson irányítópult, keresse meg az imént létrehozott feladat, majd kattintson a a **build ütemezése** ikonra.

Hudson majd hozható létre az előző szakaszban létrehozott sablon használatával az alárendelt csomópont, és futtassa a parancsfájlt a feladathoz build lépésben megadott.

## <a name="next-steps"></a>További lépések
Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ].

<!-- URL List -->

[Azure Java fejlesztői központ]: https://azure.microsoft.com/develop/java/
[előfizetés profil]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

