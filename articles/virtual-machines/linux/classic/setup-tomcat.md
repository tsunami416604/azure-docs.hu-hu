---
title: "A Linux virtuális gépek Apache Tomcat beállítása |} Microsoft Docs"
description: "Útmutató Apache Tomcat7 beállítása a Linux operációs rendszert futtató Azure virtuális gépek használatával."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: f3bd3167c9a879a876774e5d91fbb10fd340c6a8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>A Linux virtuális gépek Azure-ral Tomcat7 beállítása
Apache Tomcat (vagy egyszerűen Tomcat is korábban Dzsakarta Tomcat) egy nyílt forráskódú webkiszolgáló és a servlet tároló fejlesztett az Apache szoftver Foundation (ASP). Tomcat valósítja meg, a Java Servlet és a Sun Microsystems JavaServer lapok (JSP) előírásoknak. Tomcat Java kód futtatására tiszta Java HTTP-web server környezetet biztosít. A legegyszerűbb konfiguráció, a Tomcat egyetlen operációs rendszer folyamatban fut. Ez a folyamat fut, a Java virtuális gép (JVM). Minden HTTP-kérelem böngészőből Tomcat végzi a rendszer a Tomcat folyamat külön szálban.  

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk bemutatja, hogyan adhat a klasszikus üzembe helyezési modellt használja. Azt javasoljuk, hogy az új telepítések esetén a Resource Manager modellt használja. A Resource Manager-sablon használatával az Ubuntu virtuális gép nyitott JDK és Tomcat, lásd: [Ez a cikk](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ez a cikk Tomcat7 telepítése egy Linux-lemezképet, és telepítse az Azure-ban.  

Az oktatóanyagban érintett témák köre:  

* Megtudhatja, hogyan hozzon létre egy virtuális gépet az Azure-ban.
* Megtudhatja, hogyan készíti elő a virtuális gép a Tomcat7.
* Hogyan Tomcat7 telepítése.

Feltételezzük, hogy már rendelkezik Azure-előfizetéssel.  Ha nem, regisztrálhat egy ingyenes próbaverziót: [az Azure webhelyén](https://azure.microsoft.com/). Ha MSDN-előfizetéssel rendelkezik, tekintse meg [Microsoft Azure különleges árképzési: MSDN, az MPN és a BizSpark előnyök](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Azure kapcsolatos további információkért lásd: [Mi az Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Ez a cikk feltételezi, hogy rendelkezik-e Tomcat- és Linux alapszintű ismeretét.  

## <a name="phase-1-create-an-image"></a>1. fázis: Lemezkép létrehozása
Ebben a fázisban egy virtuális gépet hoz létre egy Linux-lemezképet használja az Azure-ban.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>1. lépés: Az SSH hitelesítési kulcs létrehozása
Az SSH egy rendszergazdák számára fontos eszköze. HR-részleg által meghatározott jelszót alapuló hozzáférés biztonsági beállításainak megadása azonban nem ajánlott. Rosszindulatú felhasználók is felosztása a rendszer a felhasználónév és a gyenge jelszót alapján.

Jó hírünk, hogy nincs-e olyan módon, hagyja megnyitva a távoli elérés, és nem kell foglalkoznia jelszavakat. Ez a módszer a hitelesítés az aszimmetrikus titkosítási áll. A felhasználó személyes kulcs, amely engedélyezi a hitelesítést. A felhasználói fiókot, hogy jelszó-hitelesítés nem is zárolhatja.

Egy másik ezt a módszert előnye, hogy nem kell különböző jelszóból bejelentkezni a különböző kiszolgálókon. A személyes titkos kulcs használatával az összes kiszolgálón megakadályozza, hogy meg kellene jegyezni több jelszavak hitelesítheti.



Kövesse az alábbi lépéseket az SSH hitelesítési kulcs létrehozásához.

1. Töltse le és telepítse a PuTTYgen a következő helyről: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Run Puttygen.exe.
3. Kattintson a **Generate** a kulcs létrehozásához. A folyamat során annak véletlenszerű növelhető az egér mozgatásával ablakban üres terület felett.  
   ![PuTTY kulcs generátor Képernyőkép az előállítás új kulcs gombra][1]
4. A létrehozás folyamat után Puttygen.exe jeleníti meg az új nyilvános kulcshoz.  
   ![PuTTY kulcs generátor Képernyőkép az új nyilvános kulcsot és a Mentés személyes kulcs gomb][2]
5. Válassza ki és másolja át a nyilvános kulcsot, és mentse a munkafüzetet egy publicKey.pem nevű fájlt. Ne kattintson **mentés nyilvános kulcs**, mert a mentett nyilvános kulcs fájlformátum eltér azt szeretnénk, ha a nyilvános kulcsot.
6. Kattintson a **mentés titkos kulcs**, és mentse a munkafüzetet egy privateKey.ppk nevű fájlt.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>2. lépés: A lemezkép létrehozása az Azure-portálon
1. Az a [portal](https://portal.azure.com/), kattintson a **hozzon létre egy erőforrást** a tálcán a képfájl létrehozásához. Válassza ki a Linux képet, amely alapul, az igényeknek megfelelően. Az alábbi példában az Ubuntu 14.04 kép.
![A portál, az új gombra megjelenítő képernyőkép][3]

2. A **állomásnév**, adja meg a nevét, és az internetes ügyfeleket a virtuális gép elérésére használt URL-címet. Adja meg a DNS-nevét, például tomcatdemo utolsó része. Azure tomcatdemo.cloudapp.net, ekkor létrehozza URL-CÍMÉT.  

3. A **SSH hitelesítési kulcs**, másolja a kulcs értékét a publicKey.pem fájl, amely a PuTTYgen által létrehozott nyilvános kulcsot tartalmazza.  
![SSH hitelesítési kulcs mezőbe a portálon][4]

4. Igény szerint konfigurálhatja az egyéb beállításokat, és kattintson a **létrehozása**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>2. fázis: A Tomcat7 a virtuális gép előkészítése
Ebben a fázisban a Tomcat-forgalmat a végpont konfigurálása, és csatlakoztassa az új virtuális géphez.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>1. lépés: Nyissa meg a webes elérés engedélyezéséhez a HTTP-port
Az Azure-végpontok közé tartozik a TCP vagy UDP protokoll, valamint nyilvános és magánhálózati portot. A magánhálózati port megadása a portot, amelyet a szolgáltatás a virtuális gépen figyel. A nyilvános port az a port, amelyet az Azure felhőszolgáltatást figyeli a külsőleg a bejövő, az internetes forgalmat.  

8080-as TCP-port az alapértelmezett portszám Tomcat használó figyelésére. Ha ezt a portot, ahol az Azure-végpont, és a más internetes ügyfelek hozzáférhet Tomcat lapok.  

1. Kattintson a portál **Tallózás** > **virtuális gépek**, majd kattintson a létrehozott virtuális gépre.  
   ![Képernyőkép a virtuális gépek könyvtár][5]
2. A virtuális géphez a végpont hozzáadásához kattintson a **végpontok** mezőbe.
   ![A végpontok mezőben képernyőkép][6]
3. Kattintson a **Hozzáadás** parancsra.  

   1. A végpont, adjon meg egy nevet a következőben szereplő végpontnál **végpont**, és írja be a 80-as **nyilvános Port**.  

      A 80-as értékre állítva, nem kell tartalmaznia a portszámot a Tomcat elérésére használt URL-címben. For example, http://tomcatdemo.cloudapp.net.    

      Ha beállította azt egy másik értékre, például a 81-es, akkor Tomcat elérésére az URL-címet adja meg a portszámot. Például http://tomcatdemo.cloudapp.net:81 /.
   2. Adja meg a 8080-as **magánhálózati Port**. Alapértelmezés szerint a Tomcat a 8080-as TCP-portot figyeli. Ha módosította az alapértelmezett port a Tomcat figyelésére, frissítenie kell **magánhálózati Port** ugyanaz, mint a Tomcat port figyelésére kell.  
      ![Képernyőfelvétel a felhasználói felület Hozzáadás parancs, a nyilvános Port és a magánhálózati Port jeleníti meg][7]
4. Kattintson a **OK** a végpont hozzáadása a virtuális gép.

### <a name="step-2-connect-to-the-image-you-created"></a>2. lépés: Csatlakozás a létrehozott lemezképet
Kiválaszthatja, hogy bármely SSH eszközt a virtuális géphez történő csatlakozáshoz. A jelen példában használjuk a PuTTY.  

1. A DNS-neve, a virtuális gép beszerzése a portálról.
    1. Kattintson a **Tallózás** > **virtuális gépek**.
    2. Válassza ki a virtuális gép nevét, és kattintson **tulajdonságok**.
    3. Az a **tulajdonságok** csempére, tekintse meg a **tartománynév** jelölőnégyzetet, hogy a DNS-nevét.  

2. A portszám lekérése az SSH-kapcsolatok a **SSH** mezőbe.  
![Az SSH-kapcsolat portszám képernyőkép][8]

3. Töltse le [PuTTY](http://www.putty.org/).  

4. A letöltés után kattintson a végrehajtható fájl Putty.exe. A PuTTY konfigurációja az alapszintű beállításokat adhat meg az állomásnevet, és portszámát származik a virtuális gép tulajdonságai között.   
![Képernyőkép a PuTTY konfigurációs állomás neve és portszáma beállítások][9]

5. A bal oldali ablaktáblán kattintson a **kapcsolat** > **SSH** > **Auth**, és kattintson a **Tallózás** privateKey.ppk fájl helyének megadásához. A privateKey.ppk fájl tartalmazza a titkos kulcsot, amely a PuTTYgen korábban a állítja elő a "1. fázis: lemezkép létrehozása" című szakaszát.  
![Képernyőkép a kapcsolat könyvtár-hierarchia és a böngészés gombra.][10]

6. Kattintson a **nyitott**. Így előfordulhat, hogy riasztást, által egy üzenet mezőbe. Ha konfigurálta-e a DNS-nevét és portszámát megfelelően, kattintson a **Igen**.
![Az értesítés képernyőkép][11]

7. A felhasználónév megadását kéri.  
![Képernyőkép a helyét adja meg a felhasználónevet][12]

8. Megadja a felhasználónevét, amely a virtuális gép létrehozásához használt az "1. fázis: lemezkép létrehozása" című rész ebben a cikkben. A következő hasonlót fog látni:  
![A hitelesítési megerősítő képernyőkép][13]

## <a name="phase-3-install-software"></a>3. fázis: A szoftver telepítése
Ebben a fázisban telepít, a Java-futtatókörnyezet, Tomcat7 és egyéb Tomcat7 összetevőket.  

### <a name="java-runtime-environment"></a>Java-futtatókörnyezet
Tomcat Java nyelven van megírva. Java fejlesztői csomagok (JDKs), OpenJDK és Oracle JDK két típusú léteznek. Kiválaszthatja a megfelelőt.  

> [!NOTE]
> Mindkét JDKs kell szinte ugyanazt a kódot az osztályok a Java API-ban, de a virtuális gép a kód nem egyezik. OpenJDK általában használható nyitott könyvtárak, Oracle JDK általában lezárt a portokat használja. Oracle JDK van több osztályok és néhány rögzített hibák, és Oracle JDK stabilabb, mint OpenJDK.

#### <a name="install-openjdk"></a>OpenJDK telepítése  

A következő paranccsal OpenJDK letöltése.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* A JDK-fájl tárolásához könyvtár létrehozása:  

        sudo mkdir /usr/lib/jvm  
* A JDK-fájlok kibontásához a könyvtárba/usr/lib/JVM-et vagy:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Oracle JDK telepítése


A következő paranccsal Oracle JDK letöltése az Oracle-webhelyről.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* A JDK-fájl tárolásához könyvtár létrehozása:  

        sudo mkdir /usr/lib/jvm  
* A JDK-fájlok kibontásához a könyvtárba/usr/lib/JVM-et vagy:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Az Oracle JDK állítja be az alapértelmezett Java virtuális gép:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Győződjön meg arról, hogy Java telepítése sikeres
A következő parancs segítségével tesztelheti, ha a Java-futtatókörnyezet helyesen van-e telepítve:  

    java -version  

Ha OpenJDK telepítette, megjelenik egy üzenet a következő: ![sikeres OpenJDK telepítési üzenet][14]

Ha telepítette az Oracle JDK, megjelenik egy üzenet a következő: ![sikeres Oracle JDK telepítés üzenet][15]

### <a name="install-tomcat7"></a>Tomcat7 telepítése
A következő paranccsal Tomcat7 telepítése.  

    sudo apt-get install tomcat7  

Ha nem használ Tomcat7, használja a parancs megfelelő változatát.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Győződjön meg arról, hogy Tomcat7 telepítése sikeres
Ellenőrizze, hogy a Tomcat7 telepítése sikeresen megtörtént-e, hogy tallózással keresse meg a Tomcat kiszolgáló DNS-neve. Ebben a cikkben a példa URL-je http://tomcatexample.cloudapp.net/. A következő egy üzenet jelenik meg, ha a Tomcat7 megfelelően van-e telepítve.
![Sikeres Tomcat7 telepítése üzenet][16]

### <a name="install-other-tomcat7-components"></a>Más Tomcat7 összetevőinek telepítése
Nincsenek más Tomcat összetevőket is telepítheti.  

Használja a **sudo apt-gyorsítótár keresési tomcat7** parancs láthatja az összes elérhető összetevői. Az alábbi parancsokkal néhány hasznos összetevők telepítéséhez.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>4. fázis: A Tomcat7 konfigurálása
Ebben a fázisban a Tomcat felügyeletéhez.

### <a name="start-and-stop-tomcat7"></a>Elindítása és leállítása Tomcat7
A Tomcat7 kiszolgáló automatikusan elindul, amikor a telepítés. Is elindíthatja a következő paranccsal:   

    sudo /etc/init.d/tomcat7 start

Tomcat7 leállítása:

    sudo /etc/init.d/tomcat7 stop

Tomcat7 állapotának megjelenítése:

    sudo /etc/init.d/tomcat7 status

Tomcat szolgáltatások újraindítása: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Tomcat7 felügyeleti
Szerkesztheti a Tomcat felhasználó-konfigurációs fájl állíthatja be a rendszergazdai hitelesítő adataival. Használja az alábbi parancsot:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Például:  
![Képernyőkép a sudo vi parancs kimenete][17]  

> [!NOTE]
> Hozzon létre egy erős jelszót a rendszergazda felhasználóneve.  

Ez a fájl szerkesztése után újra kell indítania a Tomcat7 azokat a szolgáltatásokat, a következő parancs futtatásával győződjön meg arról, hogy a módosítások életbe léptetéséhez:  

    sudo /etc/init.d/tomcat7 restart  

Nyissa meg a böngészőt, és írja be **http://<your tomcat server DNS name>/kezelő/html** az URL-címet. Ez a cikk például az URL-cím http://tomcatexample.cloudapp.net/manager/html.  

A csatlakozás után kell megjelennie a következőhöz hasonló:  
![Képernyőfelvétel a Tomcat Web Application Manager][18]

## <a name="common-issues"></a>Gyakori problémák
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Nem fér hozzá a virtuális géphez Tomcat és Moodle az internetről
#### <a name="symptom"></a>Jelenség  
  Tomcat fut, de nem látható a Tomcat alapértelmezett lapot a böngészőben.
#### <a name="possible-root-cause"></a>Probléma lehetséges kiváltó okai   

  * A Tomcat figyelési portja nincs ugyanaz, mint a virtuális gép végpontjának a Tomcat-forgalmat a magánhálózati port.  

     Ellenőrizze a nyilvános portot és magánhálózati portot végpont beállításokat, és ellenőrizze, hogy a magánhálózati port megadása ugyanaz, mint a Tomcat port figyelésére. Lásd: "1. fázis: lemezkép létrehozása" című szakaszát, a virtuális gép végpontjai konfigurálásával kapcsolatban.  

     Annak megállapításához, a Tomcat figyelési portja, nyissa meg a /etc/httpd/conf/httpd.conf (Red Hat kiadás), vagy /etc/tomcat7/server.xml (Debian kiadás). Alapértelmezés szerint a Tomcat figyelési portja a 8080-as. Például:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Ha szeretné, hogy Debian és Ubuntu, és módosíthatja az alapértelmezett port a Tomcat figyelésére (például 8081) használ a virtuális gép, is meg kell nyitnia a portot, az operációs rendszerhez. Első lépésként nyissa meg a profil:  

        sudo vi /etc/default/tomcat7  

     Ezután állítsa vissza az utolsó sort, és módosítsa a "yes" "nem".  

        AUTHBIND=yes
  2. A tűzfal letiltotta a Tomcat figyelési port.

     A helyi gazdagépen Tomcat alapértelmezett oldal csak látható. A probléma a legvalószínűbb, hogy a portot, amely Tomcat által figyelt van a tűzfal által blokkolva van. A w3m eszköz segítségével keresse meg a képernyőn látható weblapon. A következő parancsok w3m telepítése, és keresse meg a Tomcat alapértelmezett oldal:  


        sudo yum telepítés w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Megoldás

  * Ha a Tomcat-figyelési port nem ugyanaz, mint a végpont a virtuális gép felé irányuló forgalom a magánhálózati port, módosítania kell a magánhálózati port ugyanaz, mint a Tomcat port figyelésére.   
  2. Ha tűzfal/iptables okozta a problémát, a következő sorok hozzáadása /etc/sysconfig/iptables. A második sor csak akkor szükséges, a https-forgalmat:  

      -A -p tcp -m tcp--dport 80 – j ELFOGADÁS bemeneti

      -A -p tcp -m tcp--dport 443 -j ELFOGADÁS bemeneti  

     > [!IMPORTANT]
     > Győződjön meg arról, hogy az előző sorok legyenek elhelyezve fent, amelyek globálisan szeretné korlátozni a hozzáférést, például a következő sorokat: - A bemeneti -j ELUTASÍTÁS – elutasítás-az icmp-állomás által tiltott



Töltse be újra a iptables, futtassa a következő parancsot:

    service iptables restart

Ez a CentOS 6.3 tesztelték.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Engedély megtagadva projektfájlokat /var/lib/tomcat7/webapps való feltöltésekor /
#### <a name="symptom"></a>Jelenség
  Csatlakozás a virtuális géphez, és navigáljon a /var/lib/tomcat7/webapps/a webhely közzététele egy SFTP ügyfél (például FileZilla) használata esetén hiba üzenetet kap a következőhöz hasonló:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Probléma lehetséges kiváltó okai
  Nincs engedélye a /var/lib/tomcat7/webapps mappa elérésére.  
#### <a name="solution"></a>Megoldás  
  Engedély kérése a rendszergazdafiók kell. Legfelső szintű mappa tulajdonjogának módosítható a gép létesített használt felhasználónév. Íme egy példa a azureuser fióknévvel:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Használja az -R kapcsoló túl alkalmazni könyvtár belül az összes engedélyt.  

  Ez a parancs könyvtárak is működik. Az -R kapcsoló módosítja az engedélyeket, a fájlok és könyvtárak a könyvtárán belül. Például:  

     sudo chown -R username:group directory  

  Ez a parancs módosítja a tulajdonosa (felhasználó és csoport) a fájlok és könyvtárak, amelyek a könyvtárán belül.  

  A következő parancs csak akkor változik meg a mappa könyvtár engedéllyel. A fájlok és mappák a könyvtárán belül nem változnak.  

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
