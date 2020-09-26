---
title: Git-integráció a Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan integrálható a Azure Machine Learning egy helyi git-adattárral. Ha a betanítást egy git-tárházból álló helyi könyvtárból küldi el, a rendszer a Futtatás részeként nyomon követi a tárház, az ág és az aktuális véglegesítés adatait.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 71ac7793fe5226215c5d4eab98f84dba356b114c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275965"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integráció a Azure Machine Learning

A [git](https://git-scm.com/) egy népszerű verziókövető rendszer, amely lehetővé teszi a projektek megosztását és együttműködését. 

A Azure Machine Learning teljes mértékben támogatja a nyomon követéshez használható git-Tárházak használatát – a Tárházak közvetlenül a megosztott munkaterület fájlrendszerére, a git használata a helyi munkaállomáson, vagy a git használata CI/CD-folyamatból.

Ha Azure Machine Learningre küld el egy feladatot, ha a forrásfájlok egy helyi git-tárházban tárolódnak, akkor a rendszer a betanítási folyamat részeként nyomon követi a tárházra vonatkozó információkat.

Mivel Azure Machine Learning nyomon követi a helyi git-tárház adatait, nem kötődik egyetlen konkrét központi adattárhoz sem. A tárházat a GitHub, a GitLab, a bitbucket, az Azure DevOps vagy más git-kompatibilis szolgáltatásból lehet klónozott.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git-adattárak klónozása a munkaterület fájlrendszerébe
Azure Machine Learning megosztott fájlrendszert biztosít a munkaterület összes felhasználója számára.
Ha a git-tárházat ebbe a fájlmegosztásba szeretné klónozott, javasoljuk, hogy hozzon létre egy számítási példányt & nyisson meg egy terminált.
Miután megnyitotta a terminált, egy teljes git-ügyfélhez férhet hozzá, és a git parancssori felületén keresztül klónozott és dolgozhat a git használatával.

Azt javasoljuk, hogy a tárházat a felhasználók könyvtárába klónozással, hogy mások ne hozzanak ütközéseket közvetlenül a munkaágra.

A (GitHub, Azure Repos, BitBucket stb.) használatával bármilyen git-tárház klónozását végezheti el.

A klónozással kapcsolatos további információkért tekintse meg a [git parancssori felület használatának](https://guides.github.com/introduction/git-handbook/)útmutatója című témakört.

## <a name="authenticate-your-git-account-with-ssh"></a>A git-fiók hitelesítése SSH-val
### <a name="generate-a-new-ssh-key"></a>Új SSH-kulcs létrehozása
1) [Nyissa meg a terminál ablakot](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#terminal) a Azure Machine learning notebook lapon.

2) Illessze be az alábbi szöveget az e-mail-címébe való Behelyettesítéssel.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Ez egy új SSH-kulcsot hoz létre, amely a megadott e-mail-címkét használja.

```
> Generating public/private rsa key pair.
```

3) Amikor a rendszer arra kéri, hogy "adjon meg egy fájlt, amelybe menteni szeretné a kulcsot" nyomja le az ENTER billentyűt. Ez a fájl alapértelmezett helyét fogadja el.

4) Győződjön meg arról, hogy az alapértelmezett hely a "/Home/azureuser/.ssh", majd nyomja le az ENTER billentyűt. Egyéb esetben a "/Home/azureuser/.ssh" helyet kell megadni.

> [!TIP]
> Győződjön meg róla, hogy az SSH-kulcs a "/Home/azureuser/.ssh" mappába van mentve. Ezt a fájlt a számítási példányon menti a rendszer, csak a számítási példány tulajdonosa férhet hozzá.

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) A parancssorba írja be a biztonságos hozzáférési kódot. Azt javasoljuk, hogy adjon hozzá egy hozzáférési kódot az SSH-kulcshoz a további biztonság érdekében

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>A nyilvános kulcs hozzáadása a git-fiókhoz
1) Másolja ki a nyilvános kulcs fájljának tartalmát a terminál ablakban. Ha átnevezte a kulcsot, cserélje le a id_rsa. pub fájlt a nyilvános kulcs fájljának nevére.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Másolás és beillesztés a terminálon**
> * Windows: `Ctrl-Insert` Másolás és használat `Ctrl-Shift-v` vagy `Shift-Insert` Beillesztés.
> * Mac OS: `Cmd-c` a másoláshoz és `Cmd-v` a beillesztéshez.
> * A FireFox/IE nem támogatja megfelelően a vágólap-engedélyeket.

2) Válassza ki és másolja a vágólapra a kulcs kimenetét.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure-DevOps](https://docs.microsoft.com/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops#step-2--add-the-public-key-to-azure-devops-servicestfs)  Kezdje a **2. lépéssel**.

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Kezdje a **4. lépéssel**.

### <a name="clone-the-git-repository-with-ssh"></a>A git-tárház klónozása SSH-val

1) Másolja az SSH git Clone URL-címét a git-tárházból.

2) Illessze be az URL-címet az `git clone` alábbi parancsba az SSH git-tárház URL-címének használatához. Ez a következőképpen fog kinézni:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

A következőhöz hasonló választ fog látni:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

Előfordulhat, hogy az SSH megjeleníti a kiszolgáló SSH-ujjlenyomatát, és megkéri, hogy ellenőrizze. Győződjön meg arról, hogy a megjelenített ujjlenyomat megegyezik az SSH nyilvános kulcsok oldalának egyik ujjlenyomatával.

Az SSH ezt az ujjlenyomatot jeleníti meg, amikor egy ismeretlen gazdagéphez csatlakozik, és így védelmet nyújt a támadók [számára.](https://technet.microsoft.com/library/cc959354.aspx) Ha elfogadja a gazdagép ujjlenyomatát, az SSH nem kérdezi újra, ha az ujjlenyomat megváltozása megtörténik.

3) Ha a rendszer megkérdezi, hogy szeretné-e folytatni a csatlakozást, írja be a következőt: `yes` . A git a tárház klónozásával és az SSH-val a későbbi git-parancsokkal való kapcsolódáshoz állítja be a forrás távoli kapcsolatot.

## <a name="track-code-that-comes-from-git-repositories"></a>A git-adattárakból származó kód nyomon követése

Amikor beküld egy képzést a Python SDK-ból vagy Machine Learning CLI-ből, a modell betanításához szükséges fájlok fel lesznek töltve a munkaterületre. Ha a `git` parancs elérhető a fejlesztői környezetben, a feltöltési folyamat azt a segítségével ellenőrizze, hogy a fájlok egy git-tárházban tárolódnak-e. Ha igen, akkor a git-tárházból származó információk is fel vannak töltve a betanítási Futtatás részeként. Ezeket az adatokat a következő tulajdonságok tárolják a betanítási futtatáshoz:

| Tulajdonság | Az érték beolvasásához használt git-parancs | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Az a URI, amelyből a tárház klónozott volt. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Az a URI, amelyből a tárház klónozott volt. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Az aktív ág a Futtatás elküldését követően. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Az aktív ág a Futtatás elküldését követően. |
| `azureml.git.commit` | `git rev-parse HEAD` | A futtatáshoz benyújtott kód véglegesítő kivonata. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | A futtatáshoz benyújtott kód véglegesítő kivonata. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, ha a ág/véglegesítés piszkos; Ellenkező esetben `false` . |

Ezeket az információkat a rendszer a becsléseket, a gépi tanulási folyamatot vagy a parancsfájlok futtatását használó futtatásokhoz továbbítja.

Ha a képzési fájlok nem egy git-tárházban találhatók a fejlesztői környezetben, vagy a `git` parancs nem érhető el, a rendszer nem követi a git-hez kapcsolódó adatokat.

> [!TIP]
> Ha szeretné megnézni, hogy a git-parancs elérhető-e a fejlesztői környezetben, nyisson meg egy rendszerhéj-munkamenetet, parancssort, PowerShell-t vagy más parancssori felületet, és írja be a következő parancsot:
>
> ```
> git --version
> ```
>
> Ha telepítve van, és az elérési úton, a következőhöz hasonló választ kap: `git version 2.4.1` . A git fejlesztési környezetben való telepítésével kapcsolatos további információkért tekintse meg a [git webhelyét](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Naplózott adatok megtekintése

A git-adatokat egy tanítási Futtatás tulajdonságai tárolják. Ezeket az információkat a Azure Portal, a Python SDK és a CLI használatával tekintheti meg. 

### <a name="azure-portal"></a>Azure Portal

1. A [Studio portálon](https://ml.azure.com)válassza ki a munkaterületet.
1. Válassza a __kísérletek__lehetőséget, majd válassza ki az egyik kísérletet.
1. Válassza ki a __futtatások száma__ oszlop egyik futtatását.
1. Válassza a __kimenetek + naplók__lehetőséget, majd bontsa ki a __naplók__ és a __azureml__ bejegyzéseket. Válassza ki a hivatkozást, amely az __ ### \_ Azure__-ban kezdődik.

A naplózott információ a következő JSON-hoz hasonló szöveget tartalmaz:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

A betanítási Futtatás elküldése után a rendszer egy [futtatási](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) objektumot ad vissza. Az `properties` objektum attribútuma tartalmazza a naplózott git-információkat. A következő kód például lekéri a véglegesítő kivonatot:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>parancssori felület

A `az ml run` CLI-parancs használatával lekérheti a tulajdonságokat egy futtatásból. A következő parancs például visszaadja a (z) nevű kísérlet utolsó futtatásának tulajdonságait `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

További információ: az [ml Run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true) Reference dokumentáció.

## <a name="next-steps"></a>Következő lépések

* [Számítási célok használata a modell betanításához](how-to-set-up-training-targets.md)
