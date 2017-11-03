---
title: "Python és az SDK - Azure telepítéséhez"
description: "Megtudhatja, hogyan telepítse a Python és az SDK az Azure-ral használatára."
services: 
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: f36294be-daeb-4caf-9129-fce18130f552
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel
ms.openlocfilehash: e69fff29be5b12c3c0004b4101eba69c7da87d3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="installing-python-and-the-sdk"></a>Python és az SDK telepítése
Python egyszerűen állítsa be a Windows és a Mac, Linux, előre előre telepített és [Bash for Windows](https://msdn.microsoft.com/commandline/wsl/about). Ez az útmutató bemutatja, hogyan telepítési és a gép az Azure-ral használatra kész beolvasása.

## <a name="whats-in-the-python-azure-sdk"></a>Mi a Python az Azure SDK?
Az Azure SDK for Python tartalmaz, amelyek lehetővé teszik a fejlesztés, telepítéséhez és felügyeletéhez a Python-alkalmazások az Azure-összetevők. Az Azure SDK for Python konkrétan a következő:

* **Kezelési kódtárakat**. Ezek osztálykönyvtárakhoz biztosít egy Azure-erőforrások, például a tárfiókokat, virtuális gépek kezelése felületet.
* **Végrehajtási könyvtárak**. Ezek osztálykönyvtárakhoz tevő interfészt biztosítanak az Azure-szolgáltatások, például a tárolás és a service bus eléréséhez.

## <a name="which-python-and-which-version-to-use"></a>Mely Python és melyik verziót kell használni
Nincsenek elérhető Python tolmácsoknak több verziója – például:

* CPython - a standard és a leggyakrabban használt Python értelmező
* PyPy - gyors, megfelelő alternatív megvalósítását, hogy CPython
* IronPython - .net/CLR futó Python parancsértelmező
* Jython - Python parancsértelmező, amely a Java virtuális gép fut

**CPython** v2.7 vagy v3.3 + és PyPy 5.4.0 tesztelése, és az Azure Python SDK támogatott.

## <a name="where-to-get-python"></a>Honnan szerezhetők be a Python?
Több módon is CPython beolvasása:

* Közvetlenül a [www.python.org][www.python.org]
* Egy megbízható distro többek között a [www.continuum.io][www.continuum.io], [www.enthought.com] [ www.enthought.com] vagy [www.activestate.com][www.activestate.com]
* Build forrásból!

Ha egy konkrét OK, ajánlott az első két lehetőséget.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>A Windows, Linux és MacOS (csak az ügyféloldali kódtáraknál) SDK telepítése
Ha már rendelkezik telepített Python, a pip használatával egy csomagot az összes könyvtár fanézetét az ügyfél telepíti a meglévő Python 2.7-es vagy 3.3-as + Python-környezetben. Ezzel letölti a csomagot a [Python-Csomagindexet] [ Python Package Index] (PyPI).

Rendszergazdai jogosultsággal is kell rendelkeznie:

* Linux- és MacOS, használja a `sudo` parancs: `sudo pip install azure-mgmt-compute`.
* Windows: Nyissa meg a PowerShell vagy parancssor rendszergazdaként

Minden egyes Azure szolgáltatás külön-külön egyes tárak telepítése:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Előzetes csomagokat telepítheti a `--pre` jelző:

```console
   $ pip install --pre azure-mgmt-compute # installs only the latest Compute Management library
```

Azure szalagtárak készlete egy egysoros használatával is telepíthet a `azure` meta-csomag. Mivel a metaadat-csomag nem az összes csomag táblaként vannak közzétéve stabil, a `azure` meta-csomag még előzetes verzió van.
Azonban a fő csomagoknak kód minőségi/teljességét nézőpontból is tekinthető "stable" most

* hivatalos címkéjén ilyen szinkronban más nyelvek lehető legrövidebb időn belül.
  Jelenleg nem tervezi, addig a további lényegesen módosul.

Mivel ez egy előzetes, szeretné-e használni a `--pre` jelző:

```console
   $ pip install --pre azure
```

vagy közvetlenül

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>További csomagok lekérdezése
A [Python-Csomagindexet] [ Python Package Index] (PyPI) rendelkezik a gazdag kijelölt Python szalagtárak.  Ha egy Distro telepítését választotta, az érdekes bit, az a műszaki számítástechnikai webes fejlesztési különböző lehetőségeket többsége már összekapcsolta.

## <a name="python-tools-for-visual-studio"></a>Python Tools for Visual Studio
[A Python Tools for Visual Studio][a Python Tools for Visual Studio] (PTVS) egy ingyenes/OSS beépülő modul Microsoft Visual STUDIO alakítja át a teljes körű Python IDE:

![How-to-telepítés – python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

PTVS használata nem kötelező, de ajánlott, mivel biztosít Python és a webes projekt/megoldás támogatja, hibakeresés, profilkészítési, interaktív ablak, Sablonszerkesztés és Intellisense.

PTVS is megkönnyíti a Microsoft Azure-támogatással történő központi telepítéséhez [Felhőszolgáltatások](cloud-services/cloud-services-python-ptvs.md) és [webhelyek](app-service/app-service-web-overview.md).

PTVS a Visual Studio 2013, 2015-öt vagy 2017 példánya működik.  Dokumentáció, letöltéseket és vitafórumok: [a Python Tools for Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python Linux és MacOS Azure forgatókönyvei
A Linux vagy MacOS, fő Azure forgatókönyvek támogatottak:

1. Azure Services fel a klienskódtárak segítségével Python használatával
2. Az alkalmazás futó Linux virtuális gép
3. Fejlesztéséhez és a Git használatával Azure Websitesra történő közzététel

Az első forgatókönyv lehetővé teszi, hogy az Azure PaaS lehetőségeinek kihasználásához, mint ahol webalkalmazások készíthet [blob-tároló](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [várólista tárolási](storage/queues/storage-python-how-to-use-queue-storage.md), [table storage](cosmos-db/table-storage-how-to-use-python.md) keresztül stb. Pythonic burkolóit a(z) az Azure REST API-k. Ezek azonos Windows, Mac és Linux rendszeren működik.  A helyi fejlesztési számítógépén vagy az Azure-on futó Linux virtuális gép is használhatja a ügyfél tárak.

A virtuális gép esetben egyszerűen indítsa el a Linux virtuális gép a kiválasztott (Ubuntu, CentOS, Suse), és futtassa, felügyeletéhez, például.  Például futtathatja [IPython] [ IPython] REPL/notebook a Windows vagy Mac/Linux számítógép- és Linux vagy a IPython motor Azure-on futó Windows multi-proc VM irányítsa a böngészőt.

Linux virtuális gépet beállításával kapcsolatos információkért lásd: a [hozzon létre egy virtuális gép futó Linux](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oktatóanyag.

Git üzemelő példányt használ, a Python webalkalmazás fejlesztése és az operációs rendszert egy Azure-webhelyre történő közzétételhez.  Amikor a tárházat az Azure-ba, automatikusan létrehoz egy virtuális környezethez, és a pip telepíti a szükséges csomagokat.

Bármely WSGI-kompatibilis keretrendszer használatával további információkért lásd: [Python konfigurálása az Azure Websitesra](app-service/web-sites-python-configure.md).

## <a name="additional-software-and-resources"></a>További szoftverek és erőforrások:
* [Python ReadTheDocs készült Azure SDK](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [A Python github használatára az Azure SDK](https://github.com/Azure/azure-sdk-for-python)
* [Python hivatalos Azure-minták](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Alakíthatnak ki olyan Analytics Python elosztási][Continuum Analytics Python Distribution]
* [Enthought Python elosztási][Enthought Python Distribution]
* [ActiveState Python elosztási][ActiveState Python Distribution]
* [SciPy - tudományos Python szalagtárak együttese][SciPy - A suite of Scientific Python libraries]
* [NumPy - írhatók kódtára a Pythonhoz][NumPy - A numerics library for Python]
* [A Django-projekt - érett webes keretrendszer/CMS][Django Project - A mature web framework/CMS]
* [IPython – egy speciális REPL/Notebook Python-hez][IPython - an advanced REPL/Notebook for Python]
* [A Python Tools for Visual Studio a Githubon][Python Tools for Visual Studio on GitHub]
* [Python fejlesztői központ](/develop/python/)

[Continuum Analytics Python Distribution]: http://continuum.io
[Enthought Python Distribution]: http://www.enthought.com
[ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython]: http://ipython.org
[a Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio on GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[blob storage]:storage/blobs/storage-python-how-to-use-blob-storage.md
