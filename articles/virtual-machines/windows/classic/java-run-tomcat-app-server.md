---
title: A klasszikus Azure virtuális gép futtatásához a Java-alkalmazáskiszolgáló
description: Ez az oktatóanyag a klasszikus üzembe helyezési modellel létrehozott erőforrást használ, és bemutatja, hogyan hozzon létre egy Windows virtuális gépet, és konfigurálja úgy, hogy az Apache Tomcat alkalmazáskiszolgáló futtatásához.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e13228a707e7dae4a4c2505154d01215c40b4716
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528028"
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Javás alkalmazáskiszolgáló futtatása hagyományos módon üzembe helyezett virtuális gépen
> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A Resource Manager-sablon egy Java 8 és Tomcat webalkalmazás telepítése, lásd: [Itt](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Az Azure-egy virtuális gép segítségével adja meg a kiszolgálói szolgáltatásokat. Például egy Azure-on futó virtuális gép beállítható úgy, hogy egy Java-alkalmazáskiszolgáló, például az Apache Tomcat üzemeltetésére.

Ez az útmutató befejezése után fog Azure-on futó virtuális gép létrehozása, és konfigurálja úgy, hogy futtassa egy Java-alkalmazáskiszolgáló ismeretét. Először ismerje meg, a következő feladatokat:

* Megtudhatja, hogyan hozzon létre egy virtuális gépet, amely egy Java fejlesztői készlet (JDK) már telepítve van.
* Távolról jelentkeznek be a virtuális géphez tudnivalók.
* Hogyan telepíthet egy Java-alkalmazáskiszolgáló--Apache Tomcat – a virtuális gépen.
* Hozzon létre egy végpontot a virtuális géphez tudnivalók.
* Nyisson meg egy portot a tűzfalon az alkalmazáskiszolgáló módjáról.

A virtuális gépen futó Tomcat telepítése befejeződött eredményez.

![Apache Tomcat rendszerű virtuális gép][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Virtuális gép létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. Kattintson a **hozzon létre egy erőforrást**, kattintson **számítási**, majd kattintson a **láthatja az összes** a a **a kiemelt alkalmazások**.
3. Kattintson a **JDK**, kattintson a **JDK 8** a a **JDK** ablaktáblán.  
   Virtuálisgép-lemezképek támogató **JDK 6** és **JDK 7** érhető el, ha örökölt alkalmazásokat, amelyek nem kész a JDK 8-ban.
4. A JDK 8 ablaktábla, válassza a **klasszikus**, majd kattintson a **létrehozása**.
5. Az a **alapjai** panel:
   1. Adja meg a virtuális gép nevét.
   2. Adja meg a rendszergazdát a nevét a **felhasználónév** mező. Ne felejtse el ezt a nevet és egy ahhoz tartozó jelszót, amely a következő mezőben a következő. Már szükség Amikor távolról jelentkeznek be a virtuális gép.
   3. Adja meg egy jelszót a **új jelszó** mezőben, majd még egyszer a **jelszó megerősítése** mező. Ezt a jelszót a rendszergazdai fiók van.
   4. Válassza ki a megfelelő **előfizetés**.
   5. Az a **erőforráscsoport**, kattintson a **hozzon létre új** , és írja be az új erőforráscsoport nevét. Vagy kattintson a **meglévő** , és válassza ki az elérhető erőforráscsoportok.
   6. Jelöljön ki egy helyet, ahol a virtuális gép található, mint például **déli középső Régiójában**.
6. Kattintson a **Tovább** gombra.
7. Az a **virtuálisgép-lemezkép mérete** panelen válassza **A1 szabványos** vagy egy másik megfelelő lemezképet.
8. Kattintson a **Kiválasztás** gombra.

9. Az a **beállítások** panelen kattintson a **OK**. Az Azure által biztosított alapértelmezett értékeket is használhat.  
10. Az a **összegzés** panelen kattintson a **OK**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>A távolról jelentkeznek be a virtuális gép
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Kattintson a **virtuális gépek (klasszikus)**. Ha szükséges, kattintson a **további szolgáltatások** , a szolgáltatás kategóriák bal alsó sarokban. A **virtuális gépek (klasszikus)** bejegyzés szerepel-e a **számítási** csoport.
3. Kattintson a nevére, a virtuális gép, amelyet szeretne bejelentkezni.
4. Miután a virtuális gép elindult, a menü, ha a panel tetején engedélyezi a csatlakozást.
5. Kattintson a **Connect** (Csatlakozás) gombra.
6. Szükség esetén a virtuális géphez történő csatlakozáshoz, kövesse a megjelenő utasításokat. Általában mentse, vagy nyissa meg a .rdp fájlt, amely tartalmazza a kapcsolat adatai. Lehetséges, hogy az URL-cím: port másolása az .rdp fájl első sorának utolsó részeként, és illessze be a távoli bejelentkezési alkalmazás.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>A Java-alkalmazáskiszolgáló telepítése a virtuális gépen
Egy Java-alkalmazáskiszolgáló másolhatja a virtuális gép, vagy telepítheti egy Java-alkalmazáskiszolgáló telepítő keresztül.

Ez az oktatóanyag Tomcat használja, a Java-alkalmazáskiszolgáló telepítése.

1. Ha be van jelentkezve a virtuális gép, nyissa meg a böngésző-munkamenet az [Apache Tomcat](http://tomcat.apache.org/download-80.cgi).
2. Kattintson a hivatkozásra duplán **32-bites vagy 64 bites Windows Service telepítőjét**. Ez a módszer használatával Tomcat telepíti egy Windows-szolgáltatás.
3. Amikor a rendszer kéri, kattintson a telepítő futtatásához.
4. Belül a **Apache Tomcat telepítő** varázsló, kövesse a megjelenő utasításokat Tomcat telepítéséhez. Ez az oktatóanyag céljából az alapértelmezett értékek elfogadásával rendben. Amikor eléri a **Apache Tomcat telepítővarázslójának befejezése** párbeszédpanelen opcionálisan ellenőrizheti a **futtassa: Apache Tomcat** Tomcat most rendelkeznie. Kattintson a **Befejezés** a Tomcat telepítési folyamat befejezéséhez.

## <a name="to-start-tomcat"></a>Tomcat elejéig

Nyisson meg egy parancssort, a virtuális gépen, és futtassa a parancsot manuálisan is kezdeményezhető Tomcat **nettó&nbsp;start&nbsp;Tomcat8**.

Ha Tomcat fut, hozzáférhet az URL-cím megadásával Tomcat <http://localhost:8080> a virtuális gép böngészőben.

A külső gépekről futtató Tomcat megtekintéséhez szükség hozzon létre egy végpontot, és nyisson meg egy portot.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>A végpont a virtuális gép létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **virtuális gépek (klasszikus)**.
3. Kattintson a virtuális gépet, hogy fut a Java-alkalmazáskiszolgáló nevét.
4. Kattintson a **Végpontok** elemre.
5. Kattintson a **Hozzáadás** parancsra.
6. Az a **végpont hozzáadása** párbeszédpanel:
   1. Adjon meg egy nevet a végpont; például **HttpIn**.
   2. Válassza ki **TCP** a protokoll.
   3. Adja meg **80** a nyilvános port.
   4. Adja meg **8080** a magánhálózati port.
   5. Válassza ki **letiltott** a fix IP-cím.
   6. A hozzáférés-vezérlési lista, hagyja üresen.
   7. Kattintson a **OK** gombra kattintva zárja be a párbeszédpanelt, és a végpont létrehozásához.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Nyisson meg egy portot a tűzfalon a virtuális gép számára
1. Jelentkezzen be a virtuális gép.
2. Kattintson a **Windows Start**.
3. Kattintson a **vezérlőpultot**.
4. Kattintson a **rendszer és biztonság**, kattintson a **Windows tűzfal**, és kattintson a **speciális beállítások**.
5. Kattintson a **bejövő szabályok**, és kattintson a **új szabály**.  
   ![Új bejövő szabály][NewIBRule]
6. Az a **szabálytípus**, jelölje be **Port**, és kattintson a **következő**.  
   ![Új bejövő szabály port][NewRulePort]
7. Az a **protokoll és portok** képernyőn válassza ki **TCP**, adja meg **8080** , a **adott helyi port**, és kattintson a  **Következő**.  
  ![Új bejövő szabály ][NewRuleProtocol]
8. Az a **művelet** képernyőn válassza ki **a kapcsolat engedélyezéséhez**, és kattintson a **következő**.
   ![Új bejövő szabály művelet][NewRuleAction]
9. Az a **profil** képernyőn **tartomány**, **titkos**, és **nyilvános** van kiválasztva, és kattintson **tovább**.
   ![Új bejövő szabály profil][NewRuleProfile]
10. Az a **neve** képernyőn, adja meg a szabály nevét, például a **HttpIn** (a szabály neve nem szükséges azonban felel meg a végpont neve), és kattintson a **Befejezés**.  
    ![Új bejövő szabály neve][NewRuleName]

Ezen a ponton a Tomcat webhely egy külső böngészőből megtekinthető legyen. A webböngésző cím ablakban írja be az URL-cím a **http://*a\_DNS\_neve*. cloudapp.net**, ahol ***a\_DNS\_neve*** a virtuális gép létrehozásakor adott meg DNS-neve.

## <a name="application-lifecycle-considerations"></a>Alkalmazás életciklusa kapcsolatos szempontok
* Nem sikerült létrehozni a saját webalkalmazások archívumából (WAR), és hozzá kell adnia a **webapps** mappa. Például egy alapszintű Java szolgáltatás lap (JSP) dinamikus webes projekt létrehozása és exportálni kell a WAR-fájlt. Ezután másolja a WAR az Apache Tomcat **webapps** mappa a virtuális gépen, majd futtassa a böngészőben.
* Alapértelmezés szerint a Tomcat-szolgáltatás telepítve van, ha van állítva, akkor kézi elindításához. A szolgáltatások beépülő modul használatával automatikus indításra válthat. A szolgáltatások beépülő modul indításához kattintson **Windows Start**, **felügyeleti eszközök**, majd **szolgáltatások**. Kattintson duplán a **Apache Tomcat** szolgáltatást, és állítsa **indítási típus** való **automatikus**.

    ![A szolgáltatás automatikus indításra beállítása][service_automatic_startup]

    Az, hogy automatikusan elinduljon Tomcat előnye, hogy kezdődik futtatásakor, amikor a virtuális gép újraindítása után (például a számítógép újraindítása szükséges szoftverfrissítések telepítése után).

## <a name="next-steps"></a>További lépések
Érdemes lehet felvenni a Java-alkalmazások és egyéb szolgáltatások (például az Azure Storage, service bus és SQL-adatbázis) olvashat. A rendelkezésre álló információk megtekintése a [Java fejlesztői központ](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->
