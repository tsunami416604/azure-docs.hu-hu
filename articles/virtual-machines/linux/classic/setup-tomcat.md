---
title: Linux rendszerű virtuális gépen Apache Tomcat beállítása |} A Microsoft Docs
description: Ismerje meg, az Apache tomcat 7 beállítása Linux operációs rendszert futtató Azure virtuális gépek használatával.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: 5a5d052052be447ea2ccbd9231d3b03d38c7615c
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266943"
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Tomcat 7 beállítása az Azure-ban Linux rendszerű virtuális gépen
Az Apache Tomcat (vagy egyszerűen, a Tomcat is korábbi nevén Dzsakarta Tomcat) egy nyílt forráskódú webkiszolgáló és az Apache Software Foundation (ASP) által kidolgozott szervlet tároló. Tomcat a Java Servlet és a JavaServer Pages (JSP) leírásokat a Sun Microsystems valósítja meg. Tomcat a Java-kód futtatására tiszta Java HTTP-web server környezetet biztosít. A legegyszerűbb konfiguráció, a Tomcat egyetlen operációs rendszer folyamatban fut. Ez a folyamat a Java-alapú virtuális gépeken (JVM) futtatja. Minden HTTP-kérelem a Tomcat közvetlenül a böngészőből, a Tomcat folyamat egy külön szál feldolgozása.  

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Az Azure Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti, hogyan használható a klasszikus üzemi modellben. Azt javasoljuk, hogy az új telepítések esetén a Resource Manager modellt használja. Az Open JDK- és Tomcat egy Ubuntu virtuális gép üzembe helyezéséhez egy Resource Manager-sablon használatát ismerteti [Ez a cikk](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ez a cikk futó Tomcat7 telepítése Linux-rendszerképen, és telepítheti az Azure-ban.  

Az oktatóanyagban érintett témák köre:  

* Annak a virtuális gép létrehozása az Azure-ban.
* Hogyan készülhet fel a virtuális gépen futó Tomcat7.
* Hogyan telepítheti a tomcat 7.

Azt feltételezzük, hogy már rendelkezik Azure-előfizetéssel.  Ha nem, akkor regisztráljon egy ingyenes próbaidőszakot [az Azure-webhelyen](https://azure.microsoft.com/). Ha rendelkezik MSDN-előfizetéssel, lásd: [a Microsoft Azure speciális díjszabás: Az MSDN, MPN és BizSpark-juttatások](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Azure-ral kapcsolatos további tudnivalókért lásd: [Mi az Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Ez a cikk azt feltételezi, hogy a Tomcat- és Linux alapszintű ismeretét.  

## <a name="phase-1-create-an-image"></a>1. fázis: Rendszerkép létrehozása
Ebben a fázisban létrehozza a virtuális gépek használatával egy Linux-rendszerképek az Azure-ban.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>1. lépés: SSH hitelesítési kulcs létrehozásához
Az SSH a rendszergazdák számára fontos eszköz. Hozzáférés-biztonságot, ember által meghatározott jelszó alapján konfigurálása azonban nem ajánlott. A rosszindulatú felhasználók is felosztása a rendszer a felhasználónév és a egy egyszerű jelszó alapján.

A jó hír az, hogy nincs-e hagyja nyitva a távoli elérés, és nem kell foglalkoznia jelszavakat. Ez a módszer a hitelesítés az aszimmetrikus titkosítási áll. A felhasználó titkos kulcsát, amely engedélyezi a hitelesítést egy rendszer. Zárolhatja a felhasználó fiókját, hogy ne engedélyezze a jelszavas hitelesítést is.

Ez a módszer egy másik előnye, hogy nem kell bejelentkezni a különböző kiszolgálókon különböző jelszavakat. A személyes titkos kulcs használatával az összes kiszolgálón megakadályozza, hogy meg kellene jegyezni több jelszavak hitelesítheti.



Kövesse az alábbi lépéseket az SSH-hitelesítési kulcs létrehozásához.

1. Töltse le és telepítse a PuTTYgen a következő helyről: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Futtassa a Puttygen.exe.
3. Kattintson a **Generate** a kulcsok létrehozásához. A folyamat során a véletlenszerűségre azért növelheti az egérrel az üres területen, az ablak fölé helyezi.  
   ![PuTTY Key Generator Képernyőkép a létrehozás új kulcs gombra][1]
4. A létrehozás folyamat után Puttygen.exe jelennek meg az új nyilvános kulcsot.  
   ![PuTTY Key Generator Képernyőkép az új nyilvános kulcsot és a Mentés titkos kulcs gomb][2]
5. Válassza ki és másolja a nyilvános kulcsot, és mentse egy publicKey.pem nevű fájl. Ne kattintson **mentés nyilvános kulcs**, mert a mentett nyilvános kulcs fájl formátuma eltér a nyilvános kulcs szeretnénk.
6. Kattintson a **titkos kulcs mentése**, és mentse egy privateKey.ppk nevű fájl.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>2. lépés: A rendszerkép létrehozása az Azure Portalon
1. Az a [portál](https://portal.azure.com/), kattintson a **erőforrás létrehozása** rendszerkép létrehozásához a tálcán. Ezután válassza ki a Linux-rendszerképek igényei alapján. Az alábbi példa az Ubuntu 14.04-es rendszerképet használ.
![Képernyőkép a portálon, amely megjeleníti az új gombra][3]

2. A **állomásnév**, adja meg az URL-cím, amely a virtuális gép eléréséhez fog használni, és az internetes ügyfeleket nevét. A DNS-nevét, például tomcatdemo utolsó részét ismerheti meg. Azure tomcatdemo.cloudapp.net, akkor az URL-címet hoz létre.  

3. A **SSH hitelesítő kulcs**, másolja a kulcs értékét a publicKey.pem fájl, amely a PuTTYgen által létrehozott nyilvános kulcsot tartalmazza.  
![SSH hitelesítési kulcs mezőben a portálon][4]

4. Egyéb beállítások konfigurálása, igény szerint, és kattintson a **létrehozás**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>2. fázis: A virtuális gép előkészítése a tomcat 7
Ebben a fázisban konfigurálása egy végpontot a Tomcat-forgalmat, és kapcsolódjon az új virtuális gépet.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>1. lépés: Nyissa meg a HTTP-portot a webes elérés engedélyezése
Az Azure-ban végpontok TCP vagy UDP protokollt, valamint egy nyilvános és titkos állnak. A magánhálózati port a port, amelyet a szolgáltatás figyel a virtuális gépen. A nyilvános port az a port, amelyet az Azure-felhőszolgáltatásban figyeli a külsőleg a bejövő, az internetes forgalmat.  

8080-as TCP-port az alapértelmezett portszám Tomcat használó figyelésére. Ha ezt a portot nyitja meg az Azure-végpont, és a többi internetes ügyfelek érhetik Tomcat oldalak.  

1. Kattintson a portál **Tallózás** > **virtuális gépek**, majd kattintson a létrehozott virtuális gépet.  
   ![Képernyőkép a virtuális gépek könyvtár][5]
2. Végpont hozzáadása a virtuális gépre, kattintson a **végpontok** mezőbe.
   ![A végpontok mezőbe bemutató képernyőkép][6]
3. Kattintson a **Hozzáadás** parancsra.  

   1. A végpont adjon meg egy nevet a végpont a **végpont**, majd adja meg a 80-as **nyilvános Port**.  

      Ha beállította a 80-as, nem kell felvenni a portnak a számát a Tomcat elérésére szolgáló URL-CÍMÉT. Például: http://tomcatdemo.cloudapp.net.    

      Ha egy másik értéket, például a 81-es, hozzá kell a portszám Tomcat elérésére az URL-címre. Például:  http://tomcatdemo.cloudapp.net:81/.
   2. Adja meg a 8080-as **magánhálózati Port**. Alapértelmezés szerint a Tomcat a 8080-as TCP-portot figyeli. Ha módosította az alapértelmezett port a Tomcat figyelésére, frissítenie kell **magánhálózati Port** ugyanaz, mint a Tomcat port figyelésére kell.  
      ![Képernyőkép a felhasználói felület, amely megjeleníti a Hozzáadás parancsot, nyilvános Port és magánhálózati Port][7]
4. Kattintson a **OK** a végpont hozzáadása a virtuális gép.

### <a name="step-2-connect-to-the-image-you-created"></a>2. lépés: A létrehozott lemezkép csatlakozni
Lehetősége van csatlakozni a virtuális gép egy SSH-eszközt. Ebben a példában használjuk a putty-kapcsolaton keresztül.  

1. A virtuális gép DNS-név lekérése a portálról.
    1. Kattintson a **Tallózás** > **virtuális gépek**.
    2. Válassza ki a virtuális gép nevét, és kattintson **tulajdonságok**.
    3. Az a **tulajdonságok** csempére, tekintse meg a **tartománynév** jelölőnégyzetet, hogy a DNS-nevének lekérése.  

2. A portszám lekérése az SSH-kapcsolatok a **SSH** mezőbe.  
![Az SSH-kapcsolat portszám bemutató képernyőkép][8]

3. Töltse le [PuTTY](http://www.putty.org/).  

4. A letöltés után kattintson a végrehajtható fájl Putty.exe. A PuTTY konfigurációs az alapvető beállítások konfigurálása az állomásnévvel és a portszám kérhető le a virtuális gép tulajdonságai között.   
![Képernyőkép a PuTTY konfigurációs állomás neve és portszáma beállításai][9]

5. A bal oldali ablaktáblán kattintson **kapcsolat** > **SSH** > **Auth**, és kattintson a **Tallózás** , adja meg a a privateKey.ppk fájl helyét. A privateKey.ppk fájl tartalmazza a titkos kulcsot, amely a PuTTYgen korábban a hozza létre az "1. fázis: Rendszerkép létrehozása"című szakaszát.  
![Képernyőkép a kapcsolat könyvtár-hierarchia és a Tallózás gomb][10]

6. Kattintson az **Open** (Megnyitás) elemre. Előfordulhat, hogy, egy üzenet mezőbe által riasztást kap. Ha konfigurálta a DNS-nevet, és megfelelően portszám, kattintson a **Igen**.
![Az értesítés bemutató képernyőkép][11]

7. Adja meg a felhasználónevet kéri.  
![Képernyőkép a helyét adja meg a felhasználónevet][12]

8. Adja meg a felhasználónevet, amellyel a virtuális gép létrehozása az "1. fázis: Rendszerkép létrehozása"szakaszban Ez a cikk korábbi részében. Látni fogja a következőhöz hasonló:  
![A hitelesítési megerősítését bemutató képernyőkép][13]

## <a name="phase-3-install-software"></a>3. fázis: Szoftver telepítése
Ebben a fázisban telepíti, a Java-futtatókörnyezet, a tomcat 7 és az egyéb tomcat7 and Using összetevők.  

### <a name="java-runtime-environment"></a>Java-futtatókörnyezet
Tomcat a Java nyelven van megírva. Lásd: [Azure támogatott segítségével](https://aka.ms/azure-jdks) információk beszerzése a teljes körűen támogatott Java-futtatókörnyezetek. Is használhatja a saját, de ez a cikk további részének fogja használni az Azure által támogatott kiadások.


#### <a name="install-azure-supported-jdk"></a>Az Azure támogatott JDK telepítéséhez

Kövesse a `apt-get` telepítési utasításokat a dokumentált a [Azul Zulu Enterprise Azure](https://www.azul.com/downloads/azure-only/zulu/#apt-repo) webhelyén.

#### <a name="confirm-that-java-installation-is-successful"></a>Győződjön meg arról, hogy Java-telepítés sikeres
A következőkhöz hasonló parancsot segítségével tesztelése amennyiben a Java-futtatókörnyezet megfelelően van-e telepítve:  
    Java-verzió  

Egy üzenet a következőhöz hasonlóan kell megjelennie: ![A sikeres openjdk csomagját telepítési üzenet][14]


### <a name="install-tomcat7"></a>Tomcat 7 telepítése
A következő paranccsal telepítse a tomcat 7.  

    sudo apt-get install tomcat7  

Ha nem használ a tomcat 7, használja a parancs megfelelő változatát.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Győződjön meg arról, hogy futó Tomcat7 telepítése sikeres
Annak ellenőrzéséhez, hogy a tomcat 7 sikeresen telepítve van, keresse meg a Tomcat-kiszolgáló DNS-neve. Ez a cikk a példa URL-je http://tomcatexample.cloudapp.net/. Ha az alábbihoz hasonló üzenet jelenik meg, a tomcat 7 megfelelően van-e telepítve.
![A sikeres futó Tomcat7 telepítése üzenet][16]

### <a name="install-other-tomcat7-components"></a>Más tomcat7 and Using összetevőinek telepítése
Nincsenek más választható Tomcat-összetevőket telepítheti.  

Használja a **sudo apt-gyorsítótár keresési tomcat7 and using** paranccsal megtekintheti az összes rendelkezésre álló összetevőt. A következő parancsok használatával néhány hasznos összetevők telepítéséhez.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>4. fázis: Configure Tomcat7
Ebben a fázisban a Tomcat felügyeletéhez.

### <a name="start-and-stop-tomcat7"></a>Elindíthatja és leállíthatja a tomcat 7
A tomcat 7 kiszolgáló automatikusan elindul, amikor a telepítés. Is elindíthatja, a következő paranccsal:   

    sudo /etc/init.d/tomcat7 start

Tomcat 7 megszüntetése:

    sudo /etc/init.d/tomcat7 stop

Tomcat 7 állapotának megtekintése:

    sudo /etc/init.d/tomcat7 status

Tomcat-szolgáltatások újraindítása: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Tomcat 7 felügyeleti
Szerkesztheti a Tomcat felhasználókonfigurációs fájlt a rendszergazdai hitelesítő adatok beállításához. Használja az alábbi parancsot:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Például:  
![Képernyőkép a sudo vi parancs kimenete][17]  

> [!NOTE]
> Hozzon létre egy erős jelszót a rendszergazdai felhasználónevet.  

Ezt a fájlt a Szerkesztés után újra kell indítani a tomcat 7 szolgáltatásokat annak érdekében, hogy a módosítások érvénybe lépéséhez a következő paranccsal:  

    sudo /etc/init.d/tomcat7 restart  

Nyissa meg a böngészőt, és adja meg **http://<your tomcat server DNS name>/manager/html** az URL-címet. Ez a cikk például az URL-cím van http://tomcatexample.cloudapp.net/manager/html.  

Miután csatlakozott, megjelenik a következőhöz hasonló:  
![A Tomcat Web Application Manager képernyőképe][18]

## <a name="common-issues"></a>Gyakori problémák
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Az internetről a Tomcat és a moodle-alapú virtuális gép nincs hozzáférése
#### <a name="symptom"></a>Jelenség  
  Tomcat fut, de a böngészőjében a Tomcat alapértelmezett oldal nem jelenik meg.
#### <a name="possible-root-cause"></a>Probléma lehetséges kiváltó okai   

  * A Tomcat figyelési portja nem ugyanaz, mint a virtuális gép végpontjának a Tomcat-forgalmat a magánhálózati port.  

     Ellenőrizze a nyilvános port és magánhálózati port végpont beállításait, és ellenőrizze, hogy a magánhálózati port ugyanaz, mint a Tomcat port figyelésére. Lásd: "1. fázis: Rendszerkép létrehozása"szakaszban Ez a cikk a virtuális gép végpontjainak konfigurálására vonatkozó utasításokat.  

     Annak megállapításához, a Tomcat figyelési portja, nyissa meg a /etc/httpd/conf/httpd.conf (Red Hat-kiadás), vagy /etc/tomcat7/server.xml (Debian kiadás). Alapértelmezés szerint a Tomcat figyelési portja a 8080-as. Például:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Ha egy virtuális gépet használ, a Debian vagy Ubuntu, és ha szeretné módosítani az alapértelmezett port a Tomcat figyelésére (például 8081-es), is nyissa meg a portot, az operációs rendszerhez. Első lépésként nyissa meg a profil:  

        sudo vi /etc/default/tomcat7  

     Ezután állítsa vissza az utolsó sort, és módosítsa az "Igen" "nem".  

        AUTHBIND=yes
  2. A tűzfal le van tiltva a Tomcat figyelési port.

     Csak a Tomcat alapértelmezett oldalt a helyi gazdagép látható. A probléma, a legvalószínűbb, hogy a portot, amely Tomcat által figyelt van a tűzfal által blokkolva van. A w3m eszköz segítségével keresse meg a képernyőn látható weblapon. A következő parancsok w3m telepítse, és tallózással keresse meg a Tomcat alapértelmezett oldal:  


        sudo yum install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Megoldás

  * Ha a Tomcat figyelésére port nem ugyanaz, mint a végpont a forgalmat a virtuális géphez a magánhálózati port, módosítania kell a magánhálózati port ugyanaz, mint a Tomcat port figyelésére kell.   
  2. Ha a tűzfal/engedélyezze az iptables okozta a problémát, a következő sorokat hozzá /etc/sysconfig/iptables. A második sor csak akkor van szükség a https-forgalmat:  

      -A -p tcp -m tcp--dport 80-as -j ELFOGADÁS bemeneti

      -A -p tcp -m tcp--dport 443-as -j ELFOGADÁS bemeneti  

     > [!IMPORTANT]
     > Ellenőrizze, hogy az előző sorok legyenek elhelyezve a fenti, amelyek globálisan szeretné korlátozni a hozzáférést, például a következő sorokat: – egy bemeneti -j VISSZAUTASÍTÁSI--visszautasítási – az icmp-állomás nem engedélyezett



Töltse be újra a engedélyezze az iptables, futtassa a következő parancsot:

    service iptables restart

Ez a CentOS 6.3 tesztelve lett.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Soubory projektu /var/lib/tomcat7/webapps való feltöltésekor megtagadta /
#### <a name="symptom"></a>Jelenség
  Csatlakozzon a virtuális géphez, és navigáljon a /var/lib/tomcat7/webapps vagy a hely közzé egy SFTP-ügyfél (például Filezillát) használ, kap egy hibaüzenet az alábbihoz hasonló:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Probléma lehetséges kiváltó okai
  Ön nincs engedélye a /var/lib/tomcat7/webapps mappa eléréséhez.  
#### <a name="solution"></a>Megoldás  
  Engedélyt kérhet a rendszergazdafióknak van szüksége. A legfelső szintű mappa tulajdonjogának módosíthatja, a gép üzembe helyezésekor használt felhasználónév. Íme egy példa azureuser fiókja néven:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Az -R kapcsoló használatával az engedélyek az összes fájl egy könyvtár belül túl alkalmazása.  

  Ez a parancs is használható a könyvtárakat. Az -R kapcsoló módosítja az engedélyeket a fájlok és könyvtárak a könyvtárán belül található. Például:  

     sudo chown -R username:group directory  

  Ez a parancs módosítja a tulajdonjogát (felhasználói és csoportobjektumokhoz egyaránt) a fájlok és könyvtárak, amelyek a könyvtárán belül található.  

  A következő parancsot a mappa könyvtár az engedély csak módosítja. A fájlokat és mappákat a könyvtárán belül található, nem módosulnak.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
