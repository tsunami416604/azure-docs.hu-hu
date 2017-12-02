---
title: "Ismert problémák és hibaelhárítási útmutató |} Microsoft Docs"
description: "Ismert problémák listája és a hibaelhárítás elősegítése érdekében az útmutató"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 54038785f513e56b07f5f3fafa3dbd6d4b6e7400
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Az Azure Machine Learning munkaterület - ismert problémák és hibaelhárítási útmutatója 
Ez a cikk segít keresés és javítsa ki a hibákat, vagy sikertelen műveletek használata az Azure Machine Learning-munkaterület alkalmazás részeként. 

## <a name="find-the-workbench-build-number"></a>A munkaterület buildszáma keresése
Ha a támogatási csoport kommunikál, fontos közé tartozik a munkaterületet üzemeltető app build száma. A Windows rendszeren talál a buildszám kattintva a **súgó** menü válassza **kapcsolatos Azure ML munkaterület**. A macOS, kattintson a **Azure ML munkaterület** menü válassza **kapcsolatos Azure ML munkaterület**.

## <a name="machine-learning-msdn-forum"></a>Machine Learning MSDN fórum
Az MSDN fórumon, hogy kérdéseit felteheti is van. A termékért felelős csoport a Fórum aktívan figyeli. Az URL-cím fórumra [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Diagnosztikai adatainak összegyűjtése
Egyes esetekben hasznos lehet ha diagnosztikai adatokat is biztosít, ha a segítségkérés. Ez a naplófájlok lakhelyétől:

### <a name="installer"></a>Telepítő
Ha problémát tapasztal telepítése során, a telepítő naplófájlok itt:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
A zip-be ezeket a könyvtárakat tartalmát, és elküldi a számunkra a diagnosztikai.

### <a name="app-update"></a>Alkalmazás frissítése 
#### <a name="no-update-notification-on-windows-desktop"></a>A Windows asztalon nincs frissítési értesítés 
A probléma kiiktatása a későbbi frissítés. Kerülő megoldás időközben lehet elindítani az alkalmazást a a tálcán van rögzítve parancsikonnal elkerülése érdekében. Ehelyett az alkalmazás elindítása használatával a Start menüből vagy a Start-keresősávban, illetve a helyi az asztalon (ha van). 

#### <a name="no-update-notification-on-an-ubuntu-data-sciece-virtual-machine-dsvm"></a>Nincs frissítés értesítési a egy Ubuntu adatok Sciece virtuális gép (DSVM)
A következő lépésekkel töltse le a legfrissebb alkalmazás:   
   - a mappa \Users\AppData\Local\amlworkbench eltávolítása
   - Távolítsa el a parancsfájl`c:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - a fenti szkript indító parancsikon eltávolítása
   - telepítés szabályszerűen használatával [https://aka.ms/azureml-wb-msi](https://aka.ms/azureml-wb-msi)

### <a name="workbench-desktop-app"></a>Egy asztali alkalmazás munkaterület
Ha problémája van bejelentkezve, vagy ha a munkaterületet üzemeltető asztali összeomlik, a naplófájlok itt található:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
A zip-be ezeket a könyvtárakat tartalmát, és elküldi a számunkra a diagnosztikai.

### <a name="experiment-execution"></a>Kísérlet végrehajtása
Ha egy adott parancsfájl megszakad, miközben az asztali alkalmazásból beküldése, próbálja meg újra elküldeni az keresztül parancssori felület használatával `az ml experiment submit` parancsot. Ez adjon meg teljes hibaüzenet JSON formátumú, és a legfontosabb tartalmaz egy **Műveletazonosító** érték. Küldjön nekünk a JSON fájl többek között a **Műveletazonosító** és könnyebb diagnosztizálásához. 

Ha egy adott parancsfájl elküldése a sikeres, de végrehajtása sikertelen, kell-e nyomtassa ki a **futtatásához Azonosítóját** rendszert adott futtató azonosításához. Csomagot be a megfelelő naplófájlok helyét a következő parancsot:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

A `az ml experiment diagnostics` parancs létrehoz egy `diagnostics.zip` fájlt a projekt gyökérmappájában. A ZIP-csomagját állapotban teljes projektmappában került végrehajtásra, és a naplózási információk időben tartalmazza. Úgy, hogy elküldi a diagnosztikai fájl előtt érdemes bizalmas adatai.

## <a name="send-us-a-frown-or-a-smile"></a>Küldjön egy rosszallás (vagy egy mosolynál)

Azure ML munkaterület dolgozik, amikor is küldhet nekünk a rosszallás (vagy egy mosolynál), az alkalmazás rendszerhéj bal alsó sarkába arc arcfelismerési ikonra kattint. Is lehetősége van a e-mail címet (így azt is visszaszerezheti), és/vagy egy Képernyőkép az aktuális állapot. 

## <a name="known-service-limits"></a>Ismert szolgáltatásra vonatkozó korlátozások
- Maximálisan engedélyezett projektet tartalmazó mappa mérete: 25 MB.
    >[!NOTE]
    >Ez a korlátozás nem vonatkozik `.git`, `docs` és `outputs` mappák. Ezek a mappanevek-és nagybetűk. Ha nagy fájlok dolgozik, tekintse meg a [megőrzése módosításokat, és nagy fájlok üzlet](how-to-read-write-files.md).

- Maximálisan megengedett kísérlet végrehajtási ideje: hét napja

- A nyomon követett fájl maximális méretét `outputs` mappa Futtatás után: 512 MB
  - Ez azt jelenti, hogy a parancsfájlt a kimeneti mappában 512 MB-nál nagyobb fájlt hoz létre, ha azt nem gyűjtenek van. Ha nagy fájlok dolgozik, tekintse meg a [megőrzése módosításokat, és nagy fájlok üzlet](how-to-read-write-files.md).

- SSH-kulcsok használata nem támogatott, amikor SSH-n keresztül csatlakozik egy távoli gép vagy a Spark-fürt. Csak a felhasználónév/jelszó mód jelenleg támogatott.

- A cél számítási HDInsight-fürt használata esetén azt kell használni az Azure blob elsődleges tárolóként. Azure Data Lake Storage használata nem támogatott.

- Szöveg fürtözési átalakítások nem támogatottak a Mac.

- RevoScalePy könyvtár csak Windows és Linux (a Docker-tároló) támogatott. MacOS a nem támogatott.

## <a name="cant-update-workbench"></a>Nem lehet frissíteni a munkaterület
Egy új frissítés érhető el, ha a munkaterületet üzemeltető alkalmazás kezdőlap egy üzenetet fog látni az új frissítésről jeleníti meg. Meg kell jelennie egy frissítés jelvény jelenik meg a bal alsó sarkába a alkalmazást a harang ikonra. Kattintson a jelvény, és kövesse a telepítővarázsló a frissítés telepítéséhez. 

![frissítésének képe](./media/known-issues-and-troubleshooting-guide/update.png)

Ha nem látja az értesítési, indítsa újra az alkalmazást. Ha még nem látja a frissítési értesítés újraindítás után, előfordulhat, hogy néhány okainak lehet.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>A tálcán rögzített parancsikon vannak fókusza munkaterület
Előfordulhat, hogy már telepítette a frissítést. De a rögzített helyi továbbra is a régi bits lemezen mutat. Ez megkeresésével ellenőrizheti a `%localappdata%/AmlWorkbench` mappa, és ellenőrizze, hogy ha legújabb verziója van telepítve, és vizsgálja meg azon tulajdonságát, tekintse meg, ahol mutató rögzített hivatkozás. Ha ellenőrizte, egyszerűen távolítsa el a régi helyi Start menüből indítsa el a munkaterületet üzemeltető és opcionálisan hozzon létre egy új rögzített parancsikont a tálcán.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>"Az Azure ML munkaterület telepítés" hivatkozás segítségével a Windows-DSVM munkaterület telepítése
Sajnos van nem egyszerű ezen a projekten. Akkor kell távolítania a telepített bits, és töltse le a legfrissebb telepítő friss-telepítésre a munkaterület a következő lépésekkel: 
   - a következő mappa eltávolítása`C:\Users\<Username>\AppData\Local\amlworkbench`
   - Távolítsa el a parancsfájl`C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - a fenti szkript indító parancsikon eltávolítása
   - a telepítő https://aka.ms/azureml-wb-msi töltse le és telepítse újra.

## <a name="get-stuck-at-checking-experimentation-account-screen-after-logging-in"></a>A bejelentkezés után elakadnak "Kísérletezhet fiók ellenőrzése" képernyő
A bejelentkezés után a munkaterületet üzemeltető alkalmazás előfordulhat, hogy elakadnak a üres képernyőt ábrázoló "Ellenőrzése kísérletezhet fiók" egy forgó rájuk üzenetet. A probléma megoldásához tegye a következőket:
1. Az alkalmazás leállítása
2. Törölje a következő fájlt:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Indítsa újra az alkalmazást.

## <a name="cant-delete-experimentation-account"></a>Kísérletezhet fiók nem törölhető.
Parancssori felület használatával kísérletezhet-fiók törlése, de törölnie kell az alárendelt munkaterületekkel és a gyermek projektek belül e gyermek munkaterületek először. Ellenkező esetben hibaüzenet jelenik meg.

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

A projektek és a munkaterületet üzemeltető alkalmazásból származó munkaterületek is törli.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Fájl nem nyitható meg, ha a projekt a onedrive-on
Ha Windows 10 alá esik Creators frissítést, és a projekt egy, a onedrive vállalati verzió leképezett helyi mappában jön létre, előfordulhat, hogy nem nyitható meg minden olyan fájlt a munkaterületre. Node.js kód sikertelen lesz, a onedrive vállalati verzió mappában a alá esik Creators frissítés által bevezetett programhiba okozza. A hiba lesz kijavítva, amint a Windows Update, de addig, nem hozzon létre projekteket a onedrive vállalati verzió mappában.

## <a name="file-name-too-long-on-windows"></a>A fájlnév túl hosszú a Windows rendszeren
Ha Windows munkaterületet használja, mutatjuk be a alapértelmezett maximális 260 karakteres fájl neve maximális hossz, amely "a rendszer nem találja a megadott elérési út" hibaként surface sikerült. Egy beállításkulcs-érték engedélyezi sokkal hosszabb fájl elérési útja módosítható. Felülvizsgálati [Ez a cikk](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) beállításával kapcsolatos további részletekért a _MAX_PATH_ beállításkulcsot.

## <a name="docker-error-read-connection-refused"></a>Docker hiba "olvasható: Kapcsolat elutasítva"
Végrehajtása egy helyi Docker-tároló, időnként megjelenhet a következő hibával: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

A Docker DNS-kiszolgáló módosítása megoldhatja `automatic` rögzített értékre `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Távolítsa el a virtuális gép végrehajtási hiba "nincs jelen tty"
Egy Docker-tároló egy távoli számítógépen Linux elleni végrehajtásakor léphetnek fel a következő hibaüzenet:
```
sudo: no tty present and no askpass program specified.
``` 
Ez akkor fordulhat elő, ha az Azure portál segítségével módosíthatja az Ubuntu Linux virtuális gép gyökér szintű jelszavát. 

Az Azure Machine Learning-munkaterület a távoli állomáson futtatásához jelszó nélküli sudoers hozzáférésre van szüksége. Ez a legegyszerűbb módja, hogy használjon _visudo_ (Előfordulhat, hogy a fájl esetén létrehozhat nem létezik) a következő fájl szerkesztése:

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Fontos, hogy a fájl szerkesztése _visudo_ és nem egy másik parancsba is. _visudo_ automatikusan ellenőrzi az szintaxis a összes sudo olyan konfigurációs fájlt, és szintaktikailag helyes sudoers fájl létrehozásához hiba is kizárhatja sudo.

Szúrja be a következő sort a fájl végén:

```
username ALL=(ALL) NOPASSWD:ALL
```

Ha _felhasználónév_ Azure Machine Learning-munkaterület nevét a távoli állomás bejelentkezni fogja használni.

A sor #includedir után kell következnie "/ etc/sudoers.d", ellenkező esetben azt felülbírálható egy másik szabálynak.

Ha egy bonyolultabb sudo-konfigurációt, érdemes lehet dokumentációjában sudo Ubuntu érhető el itt: https://help.ubuntu.com/community/Sudoers

A fenti hiba is előfordulhat, ha nem használja az Ubuntu-alapú Linux virtuális gép az Azure-ban egy végrehajtási célként. Távoli végrehajtás Ubuntu alapú Linux virtuális gép csak támogatott. 

## <a name="vm-disk-is-full"></a>Virtuálisgép-lemez megtelt
Alapértelmezés szerint új Linux virtuális gép létrehozása az Azure nyílik meg a 30-GB lemezterület az operációs rendszerhez. Alapértelmezés szerint a docker-motorhoz húzza lefelé képek és futó tárolók ugyanazt a lemezt használ. Ez töltheti fel az operációsrendszer-lemezképet, és a "Virtuális gép lemez van teljes" hibát látja, ha ez történik.

A gyorsjavítást, hogy távolítsa el az összes Docker-lemezképek, már nem használja. A következő Docker parancs éppen ez hajtja végre. (Természetesen kell SSH-ból a virtuális gép egy bash rendszerhéj a Docker parancs végrehajtásához.)

```
$ docker system prune -a
```

Is hozzá adatlemezt, majd konfigurálja a adatlemez lemezképek tárolásához használandó Docker-motorhoz. Itt [adatlemez hozzáadása](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk). Követően [módosítása, ahol a Docker tárolja a képek](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Vagy, bővítheti az operációsrendszer-lemezképet, és nem kell touch Docker motor konfigurációját. Itt [hogyan bővítheti az operációsrendszer-lemezképet](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk).

## <a name="sharing-c-drive-on-windows"></a>A Windows a C meghajtó megosztása
Ha végrehajtás alatt álló helyi Docker-tároló a Windows, a beállítás `sharedVolumes` való `true` a a `docker.compute` a fájl `aml_config` végrehajtási teljesítményének. Azonban ehhez a C meghajtó megosztja a _Docker a Windows eszköz_. Ha nem tudja megosztani a C meghajtó, próbáljon a következő tippek:

* Ellenőrizze a osztozik a C meghajtó használata a Fájlkezelőből
* Nyissa meg a hálózati adapterre vonatkozó beállításai és eltávolítása/újratelepítése "Fájl és nyomtatómegosztás Microsoft Networkshöz" vEthernet a
* Nyissa meg a docker-beállítások és a docker beállítások belül a C meghajtó
* A Windows-jelszó módosítása hatással a megosztást. Nyissa meg a Fájlkezelőt, ossza meg újra a C meghajtó, és adja meg az új jelszót.
* Tűzfallal kapcsolatos probléma esetleg felmerülő is, a C meghajtó megosztása Docker tett kísérlet során. Ez [Stack Overflow post](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) hasznos lehet.
* Tartományi hitelesítő adatok használatával, a C meghajtó megosztásakor a megosztás működése leáll a hálózatokon, ahol a tartományvezérlő nem érhető el (például otthoni hálózathoz, a nyilvános Wi-Fi stb.) a. További információkért lásd: [a feladás egy vagy több](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

A megosztási problémát, a kis teljesítményt, úgy, hogy a is elkerülheti `sharedVolumne` való `false` a a `docker.compute` fájl.

## <a name="some-useful-docker-commands"></a>Néhány hasznos Docker-parancsok

Íme néhány hasznos Docker parancsokat:

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
