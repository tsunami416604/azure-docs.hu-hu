---
title: Ismert problémák és hibaelhárítási útmutató |} A Microsoft Docs
description: Ismert problémák listája és a egy útmutató, amellyel hibaelhárításához
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: dc57509475634b6a8038179dbb205533c3ea9d99
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35923401"
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Az Azure Machine Learning Workbench – ismert problémák és hibaelhárítási útmutató 
Ez a cikk segít keresse meg és javítsa ki a hibákat vagy hibákat észlelt az Azure Machine Learning Workbench alkalmazás használatával részeként. 

## <a name="find-the-workbench-build-number"></a>Keresse meg a Workbench buildszám
A támogatási csoporthoz való kommunikáció során, esetén fontos, hogy a buildszám, a Workbench alkalmazás. A Windows, talál a buildszám kattintva a **súgó** menüben, és válassza **kapcsolatos Azure ML Workbench**. MacOS-gépeken, kattintson a a **Azure ML Workbench** menüben, és válassza a **kapcsolatos Azure ML Workbench**.

## <a name="machine-learning-msdn-forum"></a>A Machine Learning MSDN-fórum
Összeállítottunk egy MSDN-fórum, hogy Ön felteheti kérdéseit. A termékcsoport aktívan figyeli a fórumot. Az URL-cím fórum [ https://aka.ms/azureml-forum ](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Diagnosztikai adatainak összegyűjtése
Egyes esetekben hasznos lehet, ha a diagnosztikai adatok segítség kérése során megadhatja. Itt látható, ahol a naplófájlok élő:

### <a name="installer-log"></a>Telepítési napló
Ha a probléma a telepítés során, a telepítő naplófájlok jelenleg itt tart:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Zip-fel ezeket a könyvtárakat a tartalmát, és a mobilszolgáltatásokba diagnosztikai velünk a kapcsolatot.

### <a name="workbench-desktop-app-log"></a>Workbench asztali alkalmazás log
Ha nem jelentkezik be, vagy ha összeomlik a Workbenchben desktop, a naplófájlok itt találja:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Zip-fel ezeket a könyvtárakat a tartalmát, és a mobilszolgáltatásokba diagnosztikai velünk a kapcsolatot.

### <a name="experiment-execution-log"></a>Kísérlet végrehajtási napló
Ha egy adott szkript az asztali alkalmazásból elküldése közben. sikertelen, próbálja meg újra elküldeni, parancssori felületén keresztül `az ml experiment submit` parancsot. Ez adjon meg teljes hibaüzenet JSON formátumban, és a legfontosabb tartalmaz egy **Műveletazonosító** értéket. Küldjön nekünk, például JSON-fájlt a **Műveletazonosító** és diagnosztizálása, forduljon hozzánk bizalommal. 

Ha egy adott szkript a beküldés sikeres volt, de a végrehajtás sikertelen, kell-e nyomtassa ki a **futtatás azonosítója** adott Futtatás azonosításához. Csomagot készíthet másolatot a megfelelő naplófájlok helyét, a következő paranccsal:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

A `az ml experiment diagnostics` a parancs létrehoz egy `diagnostics.zip` fájlt a projektmappa gyökérkönyvtárába. A ZIP-csomagját a teljes projektmappáról állapotú, a rendszer hajtott végre, ezen kívül a naplózási információk időben tartalmazza. Mindenképpen távolítsa el a bizalmas adatokat nem szeretné, mielőtt elküldi nekünk a diagnosztikai fájl tartalmazza.

## <a name="send-us-a-frown-or-a-smile"></a>Küldjön nekünk rosszallás (vagy mosoly)

Az Azure ML Workbenchben működik, is elküldheti nekünk rosszallás (vagy mosoly) az alkalmazás shell bal alsó sarkában található mosolygós arccal ikonra kattint. Lehetősége van tüntesse fel az e-mail címét (így kapunk vissza is), és/vagy a jelenlegi állapot képernyőképe. 

## <a name="known-service-limits"></a>Ismert szolgáltatásra vonatkozó korlátozások
- Maximális engedélyezett projekt mappa mérete: 25 MB.
    >[!NOTE]
    >Ez a korlátozás nem vonatkozik a `.git`, `docs` és `outputs` mappákat. Ezek a mappanevek-és nagybetűk. Ha nagy méretű fájlok dolgozik, [megőrzése módosítások és a nagy fájlok üzlet](../desktop-workbench/how-to-read-write-files.md).

- Maximális engedélyezett kísérlet végrehajtási idő: hét napja

- A nyomon követett fájl maximális mérete `outputs` mappát egy Futtatás után: 512 MB
  - Ez azt jelenti, hogy ha a szkript létrehoz egy fájlt, a kimeneti mappában 512 MB-nál nagyobb, akkor nem lesznek begyűjtve van. Ha nagy méretű fájlok dolgozik, [megőrzése módosítások és a nagy fájlok üzlet](../desktop-workbench/how-to-read-write-files.md).

- Az SSH-kulcsok nem támogatottak, amikor ssh-n keresztül csatlakozik egy távoli gép vagy a Spark-fürt. Csak a felhasználónév/jelszó mód jelenleg támogatott.

- HDInsight-fürtöt számítási célt használatakor kell használnia, az Azure blob elsődleges tárként. Azure Data Lake Storage használata nem támogatott.

- Szöveges fürtözési átalakítások nem támogatottak a Mac rendszerre.

- RevoScalePy könyvtár csak Windows és Linux rendszereken, (a Docker-tárolók) támogatott. MacOS rendszeren nem támogatott.

- A Jupyter Notebooks 5 MB-os maximális méretkorlátot rendelkezik, amikor megnyitja őket a Workbench alkalmazásból. Nagy notebookok megnyithatja a parancssori felület 'az ml notebook start' parancs használatával, és csökkentse a fájlméretet, outputs tiszta cella.

## <a name="cant-update-workbench"></a>Nem sikerült frissíteni a Workbenchben
Új frissítés érhető el, amikor a Workbench alkalmazás kezdőlapjára arról értesíti, az új frissítésről üzenetet jelenít meg. Megjelenik egy frissítés jelvény jelenik meg a bal alsó sarkában az alkalmazás a harang ikonra. A jelvény kattintson, és kövesse a telepítővarázsló a frissítés telepítéséhez. 

![lemezkép frissítése](./media/known-issues-and-troubleshooting-guide/update.png)

Ha nem látja az értesítési, próbálja meg újraindítani az alkalmazást. Ha továbbra sem látja az újraindítás után a frissítési értesítés, néhány oka lehet.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>A tálcán rögzített parancsikont a Workbench indította el
Előfordulhat, hogy már telepítette a frissítést. De a gyors helyi továbbra is a régi bits lemezen mutat. Ez megkeresve ellenőrizheti a `%localappdata%/AmlWorkbench` mappát, és tekintse meg, ha legújabb verziója van telepítve, és vizsgálja meg a gyors helyi megtekintéséhez, hogy mutató tulajdonságát. Ha ellenőrizve, egyszerűen törölje a régi helyi, Workbench indítsa el a Start menüből, és szükség esetén hozzon létre egy új rögzített parancsikont a tálcán.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>"Az Azure Machine Learning Workbench telepítése" hivatkozást használva egy Windows adatelemző virtuális GÉPET a Workbench telepítése
Sajnos nincs nincs egyszerű a megoldás ezen a projekten. Akkor kell végrehajtani, távolítsa el a telepített bit, és töltse le a legújabb friss-telepítésre a Workbench a következő lépéseket: 
   - a mappa eltávolítása `C:\Users\<Username>\AppData\Local\amlworkbench`
   - Távolítsa el a parancsfájl `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - Távolítsa el az asztali parancsikonjára, amely elindítja a fenti szkript
   - Töltse le a telepítőt https://aka.ms/azureml-wb-msi , majd telepítse újra.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>"A Kísérletezési fiók ellenőrzése" képernyő megakad a bejelentkezés után
A bejelentkezés után a Workbench alkalmazás előfordulhat, hogy elakadnak üres képernyőt a megjelenítő tartalomfogyasztás görgetőkerék az "ellenőrzés Kísérletezési fiók" üzenettel. A probléma megoldásához hajtsa végre az alábbi lépéseket:
1. Az alkalmazás leállítása
2. Az alábbi fájl törlendő:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Indítsa újra az alkalmazást.

## <a name="cant-delete-experimentation-account"></a>Kísérletezési fiók nem törölhető
Parancssori felület segítségével-Kísérletezési fiók törlése, de törölni kell ezeket gyermek munkaterületeken gyermek projektek és a gyermek munkaterületek először. Jelenik meg a hiba "nem lehet törölni erőforrás beágyazott erőforrások törlése előtti."

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <workspace name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <workspace name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

A projektek és a-munkaterületek a Workbench alkalmazásban is törölheti.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Fájl nem nyitható meg, ha a projekt a onedrive-on
Ha Windows 10 Fall Creators Update rendelkezik, és a projekt létrehozása a onedrive vállalati verzió leképezett egy helyi mappába, előfordulhat, hogy minden olyan fájlt, nem tudja megnyitni a workbenchben. A node.js-kódot egy OneDrive-mappában sikertelen okozó Fall Creators Update bevezetett egy hiba miatt nem lehetséges. A bejelentett hiba lesz kijavítva, hamarosan Windows Update, de addig, meg nem hozható létre a OneDrive-mappában.

## <a name="file-name-too-long-on-windows"></a>A fájlnév túl hosszú. a Windows
Ha a Workbench Windows használ, mutatjuk be a alapértelmezett legfeljebb 260 karakterből álló fájl neve hosszra vonatkozó korlátot, amelyek – egy "a rendszer nem találja a megadott elérési út" hiba surface sikerült. Módosíthatja a beállításkulcsot, hogy sokkal hosszabb elérési útja és neve. Felülvizsgálat [Ez a cikk](https://msdn.microsoft.com/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) beállításával kapcsolatos további részletekért a _MAX_PATH_ beállításkulcsot.

## <a name="interrupt-cli-execution-output"></a>Parancssori végrehajtás kimenetének megszakítás
Ha elindít egy Kísérletezési Futtatás használatával `az ml experiment submit` vagy `az ml notebook start` és a kimeneti megszakítási szeretné: 
- A Windows használja a Ctrl-Break billentyűkombinációt a billentyűzet
- MacOS rendszeren használja a Ctrl-c-hez

Vegye figyelembe, hogy ez csak megzavarja a kimeneti adatfolyamba, a parancssori felület ablakában. Ez nem ténylegesen állítja le egy feladatot, amely folyamatban van. Ha szeretné megszakítani egy folyamatban lévő feladatot, használja a `az ml experiment cancel -r <run_id> -t <target name>` parancsot.

Lehetséges alternatívák Windows a számítógépeken, amelyeken nincs telepítve/csere típusú kulcs billentyűzetek, például a Fn-B, a Ctrl-Fn-B vagy a Fn + Esc billentyűkombinációt. A hardver gyártójának dokumentációjában talál egy adott billentyűkombinációt.

## <a name="docker-error-read-connection-refused"></a>Docker hiba "olvasása: Kapcsolat elutasítva"
Amikor végrehajtása helyi Docker-tárolóban, néha jelenhetnek meg a következő hibát: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

A Docker a DNS-kiszolgáló módosításával javíthassuk azt `automatic` rögzített értékre `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Távolítsa el a virtuális gép végrehajtási hiba "nincs jelen tty"
Docker-tárolóban egy távoli Linux gépen elleni végrehajtásakor akkor léphetnek fel a következő hibaüzenetet kapja:
```
sudo: no tty present and no askpass program specified.
``` 
Ez akkor fordulhat elő, ha az Azure portal használatával egy Ubuntu Linux virtuális gép gyökérszintű jelszavát módosítani. 

Az Azure Machine Learning Workbench működéséhez szükséges jelszó nélküli sudoers távoli gazdagépeken futnak. Ennek legegyszerűbb módja az, hogy használja _visudo_ (Előfordulhat, hogy létrehozza a fájlt, ha még nem létezik) a következő fájl szerkesztése:

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Fontos, hogy a fájl szerkesztése _visudo_ , és nem egy másik parancsba. _visudo_ automatikusan szintaxis ellenőrzi, minden sudo konfigurációs fájlok, és a egy szintaktikailag helyes sudoers fájlban elmulasztása is kizárhatja a sudo.

Illessze be a következő sort a fájl végén:

```
username ALL=(ALL) NOPASSWD:ALL
```

Ahol _felhasználónév_ Azure Machine Learning Workbench nevét fogja használni, jelentkezzen be a távoli gazdagépen.

A sor #includedir után kell következnie "/ etc/sudoers.d", ellenkező esetben azt felülírható egy másik szabállyal.

Ha rendelkezik egy összetettebb sudo konfigurációját, érdemes dokumentációjában sudo Ubuntu elérhető itt: https://help.ubuntu.com/community/Sudoers

A fenti hiba is történhet meg, ha nem használ egy Ubuntu-alapú Linux rendszerű virtuális gép az Azure-ban egy végrehajtási célként. Csak támogatott Ubuntu-alapú Linux rendszerű virtuális gép távoli végrehajtás céljából. 

## <a name="vm-disk-is-full"></a>Virtuálisgép-lemez megtelt.
Alapértelmezés szerint az Azure-ban, egy új Linux rendszerű virtuális gép létrehozásakor kap egy 30 GB-os lemezt az operációs rendszer. Alapértelmezés szerint a docker-motor le rendszerképek lekérése és a futó tárolók ugyanazt a lemezt használja. Ez töltheti fel az operációsrendszer-lemez és a egy "Virtuális gép lemez van teljes" hibaüzenetet látja, ha ez történik.

A gyorsjavítás, hogy távolítsa el az összes Docker-rendszerképeket, már nem használhatók. A következő parancsot a Docker hajtja végre ezt. (Természetesen kell SSH-t a virtuális Gépet annak érdekében, hogy hajtsa végre a Docker parancsot egy bash rendszerhéjból.)

```
$ docker system prune -a
```

Adatlemez hozzáadása is, és konfigurálja a Docker-motor az adatlemezt a rendszerképek tárolására használandó. Íme [adatlemez hozzáadása](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk). Ezután [módosítása, ahol a Docker tárolja a lemezképeket](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Vagy bővítheti az operációsrendszer-lemez, és nem kell touch Docker engine-konfigurációját. Íme [operációsrendszer-lemez kibontása hogyan](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks).

```azure-cli
# Deallocate VM (stopping will not work)
$ az vm deallocate --resource-group myResourceGroup  --name myVM

# Get VM's Disc Name
az disk list --resource-group myResourceGroup --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table

# Update Disc Size using above name
$ az disk update --resource-group myResourceGroup --name myVMdisc --size-gb 250
    
# Start VM    
$ az vm start --resource-group myResourceGroup  --name myVM
```

## <a name="sharing-c-drive-on-windows"></a>A Windows a C meghajtó megosztása
Ha futtatja a Windows helyi Docker-tárolóban, a beállítás `sharedVolumes` való `true` a a `docker.compute` fájlt `aml_config` végrehajtási teljesítményének. Azonban ez igényli a C meghajtó megoszt a _Docker a Windows-eszköz_. Ha nem tudja megosztani a C meghajtó, próbálja meg a következő tippeket:

* Ellenőrizze a fájlkezelő használata a C meghajtó megosztása
* Nyissa meg a hálózati adapter beállításai, és távolítsa el/telepítse újra a "Fájl és a nyomtató Microsoft Networkshöz" vEthernet a
* Nyissa meg a docker-beállításait, és megoszthatja a C meghajtó a docker-beállítások
* A Windows-jelszó módosítása hatással a megosztást. Nyissa meg a Fájlkezelőt, és újra megoszthatja a C meghajtó adja meg az új jelszót.
* Előfordulhat, hogy is tűzfal problémával találkozik a C meghajtó megosztásához a docker használatával tett kísérlet közben. Ez [Stack Overflow post](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) hasznos lehet.
* Tartományi hitelesítő adatok használata a C meghajtó megosztásakor a megosztás működése leáll, a tartományvezérlő nem érhető el (például otthoni hálózathoz, a nyilvános Wi-Fi stb.) a hálózatokon. További információkért lásd: [ebben a bejegyzésben](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Elvégezve is elkerülheti a megosztási problémát, kisebb teljesítményt, beállításával `sharedVolumne` való `false` a a `docker.compute` fájlt.

## <a name="wipe-clean-workbench-installation"></a>Workbench tiszta telepítés törlése
Általában nem kell ehhez. Azonban abban az esetben, akkor törölnie kell tiszta telepítés, a lépések a következők:

- Windows rendszeren:
  - Először győződjön meg arról, hogy használja-e _programok telepítése és_ kisalkalmazást a _Vezérlőpult_ eltávolítása a _Azure Machine Learning Workbench_ kérelmet-bejegyzés.  
  - Ezután töltse le és futtassa az alábbi parancsfájlok egyikét:
    - [Windows parancssori parancsprogram](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Windows PowerShell-parancsprogram](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (Előfordulhat, hogy futtatni kell `Set-ExecutionPolicy Unrestricted` emelt szintű jogosultságokkal a PowerShell ablakban a parancsfájl futtatása előtt.)
- MacOS rendszeren:
  - Csak töltse le és futtassa a [macOS bash héjparancsfájlt](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh).

## <a name="azure-ml-using-a-different-python-location-than-the-azure-ml-installed-python-environment"></a>Azure gépi tanulás, egy másik python helyen található, mint az Azure ML segítségével telepítve van a python-környezetet
Felhasználók miatt egy friss változás az Azure Machine Learning Workbenchben, Észreveheti, hogy helyi futtatása nem mutat az Azure ML Workbench már telepítette a python-környezetet. Ez akkor fordulhat elő, ha a felhasználó rendelkezik a számítógépen telepítve van egy másik python-környezetet, és a "Python" elérési út, mutasson a környezethez van beállítva. Az Azure ML Workbench használatához telepítve a Python-környezetet, kövesse az alábbi lépéseket:
- Nyissa meg a mappát a projekt gyökérkönyvtárában aml_config local.compute fájlt.
- Módosítsa a "pythonLocation" változó fizikai elérési útját az Azure ML workbench átirányítása telepített python-környezetet. Ezt az elérési utat kétféleképpen kaphat:
    - Az Azure Machine Learning python-hely található %localappdata%\AmlWorkbench\python\python.exe
    - az Azure Machine Learning Workbench alkalmazásban nyissa meg a cmd, a parancssorba írja be a python, sys.exe importálása, sys.executable futtatása és onnan az elérési út lekérése. 



## <a name="some-useful-docker-commands"></a>Néhány hasznos Docker-parancsok

Íme néhány hasznos Docker-parancsokat:

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
