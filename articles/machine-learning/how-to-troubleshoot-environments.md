---
title: Környezeti rendszerképek – problémamegoldás
titleSuffix: Azure Machine Learning
description: Útmutató a környezeti rendszerkép-buildek és-csomagok telepítésével kapcsolatos hibák elhárításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733332"
---
# <a name="troubleshoot-environment-image-builds"></a>Környezeti rendszerkép-buildek – problémamegoldás
Ismerje meg, hogy miként lehet elhárítani a Docker-környezet rendszerkép-buildekkel és-csomagokkal kapcsolatos problémákat.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).
* A [Azure Machine learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* Az [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)-vel.
* A [Azure Machine learning CLI-bővítménye](reference-azure-machine-learning-cli.md).
* Helyi hibakereséshez rendelkeznie kell egy működő Docker-telepítéssel a helyi rendszeren.

## <a name="docker-image-build-failures"></a>Docker-rendszerkép-létrehozási hibák
 
A rendszerkép-létrehozási hibák többsége esetén a kiváltó ok a rendszerkép-létrehozási naplóban található.
A rendszerkép-létrehozási naplót a Azure Machine Learning-portálról (20 \_ rendszerkép- \_ összeállítási \_log.txt) vagy az ACR-feladatok naplóinak futtatásához használhatja.
 
A legtöbb esetben egyszerűbb a hibák helyi reprodukálása. Tekintse át a hiba típusát, és próbálja ki a következők egyikét `setuptools` :

- A Conda függőség helyi telepítése `conda install suspicious-dependency==X.Y.Z`
- A pip-függőség helyi telepítése `pip install suspicious-dependency==X.Y.Z`
- Próbálja meg megvalósulni a teljes környezetet `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Győződjön meg arról, hogy a helyi számítási platform és tolmács megfelel a távoli gépen lévőknek. 

### <a name="timeout"></a>Időtúllépés 
 
Időtúllépési problémák merülhetnek fel a különböző hálózati problémák esetén:
- Alacsony Internet-sávszélesség
- Kiszolgálói problémák
- Nagy függőség, amely nem tölthető le a megadott Conda vagy pip időtúllépési beállításokkal
 
Az alábbihoz hasonló üzenetek a problémát jelzik:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Lehetséges megoldások:
 
- Ha elérhető, például a tükrök, a blob Storage vagy más Python-hírcsatornák, próbálkozzon más forrással a függőséghez.
- Conda vagy pip frissítése. Ha egyéni Docker-fájlt használ, frissítse az időtúllépési beállításokat.
- Egyes pip-verziók ismert problémákkal rendelkeznek. Vegye fontolóra a pip adott verziójának hozzáadását a környezeti függőségekhez.

### <a name="package-not-found"></a>A csomag nem található

Ez a leggyakoribb eset a rendszerkép-létrehozási hibák esetében.

- A Conda csomag nem található
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- A megadott pip-csomag vagy-verzió nem található
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Helytelen beágyazott pip-függőség
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Győződjön meg arról, hogy a csomag létezik a megadott forrásokon. A pip- [Keresés](https://pip.pypa.io/en/stable/reference/pip_search/) használata a pip-függőségek ellenőrzéséhez.

`pip search azureml-core`

Conda-függőségek esetén használja a [Conda Search kifejezést](https://docs.conda.io/projects/conda/en/latest/commands/search.html).

`conda search conda-forge::numpy`

További lehetőségek:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>A telepítő megjegyzései

Győződjön meg arról, hogy a szükséges terjesztés létezik a megadott platformhoz és a Python-értelmező verziójához.

A pip-függőségek esetében navigáljon a elemre, és ellenőrizze, hogy elérhető-e a `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` szükséges verzió. Például: https://pypi.org/project/azureml-core/1.11.0/#files

Conda-függőségek esetén a csatorna adattárában keresse meg a csomagot.
A anaconda, Inc. által karbantartott csatornák esetében [itt](https://repo.anaconda.com/pkgs/)tekintse meg a következőt:.

### <a name="pip-package-update"></a>Pip-csomag frissítése

Egy pip csomag telepítésekor vagy frissítésekor előfordulhat, hogy a feloldónak frissítenie kell egy már telepített csomagot az új követelmények kielégítése érdekében.
Az Eltávolítás a pip verziójával vagy a függőség telepítésének módjával kapcsolatos különféle okok miatt sikertelen lehet.
A leggyakoribb forgatókönyv az, hogy a Conda által telepített függőséget a PIP nem távolítja el.
Ebben a forgatókönyvben érdemes lehet eltávolítani a függőséget a használatával `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Telepítési problémák

Bizonyos telepítési verziók olyan problémákkal rendelkeznek a csomag-feloldásokban, amelyek létrehozási hibát okozhatnak.

Egyéni alaprendszerkép vagy Docker használata esetén javasoljuk, hogy a Conda 4.5.4 vagy újabb verzióját használja.

A pip-csomag a pip-függőségek telepítéséhez szükséges, és ha nincs megadva verzió a környezetben, a rendszer a legújabb verziót fogja használni.
Azt javasoljuk, hogy a pip ismert verziójának használatával elkerülje az átmeneti problémákat, illetve az eszköz legújabb verziója által okozott módosításokat.

Ha az alábbi üzenetek bármelyikét látja, érdemes megfontolnia a pip-verzió rögzítését a környezetben:

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

Emellett a pip-telepítés egy végtelen hurokban is elakadhat, ha feloldhatatlan ütközik a függőségek között. Ha helyileg dolgozik, a < 20,3 értékre minősítheti a pip verzióját. YAML-fájlból létrehozott Conda-környezetben Ez a probléma csak akkor látható, ha a Conda-Forge a legmagasabb prioritású csatorna. A probléma enyhítése érdekében explicit módon adja meg a pip < 20,3 (! = 20,3 vagy = 20.2.4 PIN-kódot más verzióra) a Conda-specifikáció fájljában.

## <a name="service-side-failures"></a>Szolgáltatási oldali hibák

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Nem lehet lekérni a rendszerképet a MCR/címről, mert nem sikerült feloldani a Container Registry.
Lehetséges problémák:
- Lehetséges, hogy a tároló beállításjegyzékének elérési útja nem oldható fel megfelelően. Ellenőrizze, hogy a képnevek dupla perjelet használnak-e, és a vágás iránya a Linux és a Windows rendszerű gazdagépeken helyes-e.
- Ha a vnet mögötti ACR egy nem [támogatott régióban](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)található privát végpontot használ, konfigurálja a vnet MÖGÖTTi ACR-t a portálon a szolgáltatás végpontja (nyilvános hozzáférés) használatával, majd próbálkozzon újra.
- Az ACR egy VNet mögé helyezése után ellenőrizze, hogy fut-e az [ARM-sablon](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) . Ez lehetővé teszi, hogy a munkaterület kommunikáljon az ACR-példánnyal.

### <a name="401-error-from-workspace-acr"></a>401 hiba a munkaterület ACR-ből
A tárolási kulcsok újraszinkronizálása [ws.sync_keys ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--) használatával

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>A környezet folyamatosan dob "várakozás más Conda-műveletek befejezésére..." Hiba
Ha a rendszerkép létrehozása folyamatban van, a Conda az SDK-ügyfél zárolja. Ha a folyamat összeomlott vagy a felhasználó által helytelenül lett megszakítva, a Conda zárolt állapotban marad. A probléma megoldásához manuálisan törölje a zárolási fájlt. 

### <a name="custom-docker-image-not-in-registry"></a>A beállításjegyzékben nem szereplő egyéni Docker-rendszerkép
Ellenőrizze, hogy a [helyes címke](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) van-e használatban, és hogy a `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` letiltja a Conda, és a felhasználó telepített csomagjait használja.

### <a name="common-vnet-issues"></a>Gyakori VNet problémák

1. Győződjön meg arról, hogy a Storage-fiók, a számítási fürt és a Azure Container Registry mind a virtuális hálózat ugyanazon alhálózatán vannak.
2. Ha az ACR egy VNet mögött található, nem lehet közvetlenül felépíteni képeket. Lemezképek létrehozásához a számítási fürtöt kell használni.
3. Előfordulhat, hogy a tárterületet a VNet mögé kell helyezni, ha:
    - Következtetés vagy privát kerék használata
    - 403 (nem jóváhagyott) szolgáltatási hibák
    - Nem lehet beolvasni a rendszerkép részleteit az ACR/MCR

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>A rendszerkép létrehozása meghiúsul a hálózati védett tároló elérésére tett kísérlet során
- Az ACR-feladatok nem működnek a VNet mögött. Ha a felhasználó ACR-je a VNet mögött van, akkor a számítási fürtöt kell használnia egy rendszerkép létrehozásához.
- A tárolónak a VNet mögött kell lennie ahhoz, hogy le tudja kérni a függőségeket. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>A kísérletek nem futtathatók, ha a tárterületen engedélyezve van a hálózati biztonság
Az alapértelmezett Docker-rendszerképek használatakor és a felhasználó által felügyelt függőségek engedélyezéséhez a MicrosoftContainerRegistry és a AzureFrontDoor. FirstParty [szolgáltatás címkéit](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) kell használnia a engedélyezési MCR és annak függőségeire.

 További információ: a [VNET engedélyezése](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) .

### <a name="creating-an-icm"></a>ICM létrehozása

Ha az Metaadattár-hoz hoz létre/rendel hozzá egy ICM-t, adja meg a CSS támogatási jegyét, hogy jobban meg tudjuk érteni a problémát.

## <a name="next-steps"></a>További lépések

- [Gépi tanulási modell betanítása a virágok kategorizálására](how-to-train-scikit-learn.md)
- [Gépi tanulási modell betanítása egyéni Docker-rendszerkép használatával](how-to-train-with-custom-image.md)