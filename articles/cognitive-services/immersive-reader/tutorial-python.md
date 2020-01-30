---
title: 'Oktatóanyag: a lebilincselő olvasó elindítása a Python használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy olyan Python-alkalmazást fog létrehozni, amely elindítja a magával ragadó olvasót.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844360"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Oktatóanyag: a részletes olvasó elindítása a Python Sample Project használatával

Az [Áttekintés](./overview.md)során megtanulta, hogy a magával ragadó olvasó Hogyan valósítja meg a bevált technikákat a nyelvtanulás, a feltörekvő olvasók és a tanulók tanulási különbségekkel való megismerésének javításához. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy olyan Python-webalkalmazást, amely elindítja a magával ragadó olvasót. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Python-Webalkalmazás létrehozása a pip, a lombik, a Jinja és a virtualenv használatával egy minta projekt segítségével
> * Hozzáférési jogkivonat beszerzése
> * A részletes olvasó a minta tartalmának elindítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./how-to-create-immersive-reader.md) . A környezeti tulajdonságok konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [Git](https://git-scm.com/)
* [Magával ragadó olvasói SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) és [pip](https://docs.python.org/3/installing/index.html). A Python 3,4-től kezdődően a pip alapértelmezés szerint a Python bináris telepítők részét képezi.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) és [virtualenvwrapper – Win for Windows](https://pypi.org/project/virtualenvwrapper-win/) vagy [virtualenvwrapper for OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [kérelmek modulja](https://pypi.org/project/requests/2.7.0/)
* IDE, például a [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Hitelesítő adatok konfigurálása

Hozzon létre egy _. env_nevű új fájlt, és illessze be a következő kódot, és adja meg a magával ragadó olvasó erőforrás létrehozásakor megadott értékeket.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ügyeljen arra, hogy ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem kell nyilvánosságra hozni.

A **getimmersivereadertoken** API-végpontot biztonságossá kell tennie valamilyen hitelesítési módszer (például [OAuth](https://oauth.net/2/)) mögött, hogy megakadályozza a jogosulatlan felhasználók számára a jogkivonatok beszerzését a felhasználatlan olvasó szolgáltatás és a számlázás során. Ez a munka meghaladja az oktatóanyag hatókörét.

## <a name="create-a-python-web-app-on-windows"></a>Python-Webalkalmazás létrehozása Windows rendszeren

Python-Webalkalmazás létrehozása a Windows `flask` használatával.

Telepítse a [Git](https://git-scm.com/) szoftvert.

Miután telepítette a git alkalmazást, nyisson meg egy parancssort, és a "klónozás" nevű, a "klónozott" olvasói SDK git-tárházat a számítógép egyik mappájába

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Telepítse a [Pythont](https://www.python.org/downloads/).

Jelölje be a Python – ELÉRÉSi út hozzáadása jelölőnégyzetet.

![Python Windows telepítési párbeszédpanel 1. lépés](./media/pythoninstallone.jpg)

A választható funkciók hozzáadásához jelölje be a jelölőnégyzeteket, majd kattintson a Next (tovább) gombra.

![Python Windows telepítési párbeszédpanel 2. lépés](./media/pythoninstalltwo.jpg)

Válassza az "egyéni telepítés" lehetőséget, és állítsa be a telepítési útvonalat a gyökérmappa fölé, például `C:\Python37-32\`, majd kattintson a telepítés gombra.

![Python Windows telepítési párbeszédpanel 3. lépés](./media/pythoninstallthree.jpg)

A Python telepítésének befejezése után nyisson meg egy parancssort, és `cd` a Python-parancsfájlok mappába.

```cmd
cd C:\Python37-32\Scripts
```

A lombik telepítése.

```cmd
pip install flask
```

Telepítse a Jinja2. Egy teljes körű funkcionalitású sablon a Pythonhoz.

```cmd
pip install jinja2
```

Telepítse a virtualenv. Egy eszköz elszigetelt Python-környezetek létrehozásához.

```cmd
pip install virtualenv
```

Telepítse a virtualenvwrapper-Win-t. A virtualenvwrapper mögötti ötlet a virtualenv használatának egyszerűsége.

```cmd
pip install virtualenvwrapper-win
```

Telepítse a kérelmek modult. A kérések egy Pythonban írt, Apache licenccel rendelkező HTTP-könyvtár.

```cmd
pip install requests
```

Telepítse a Python-dotenv modult. Ez a modul beolvassa a kulcs-érték párokat a. env fájlból, és hozzáadja azokat a környezeti változóhoz.

```cmd
pip install python-dotenv
```

Virtuális környezet létrehozása

```cmd
mkvirtualenv advanced-python
```

`cd` a minta projekt gyökérmappa mappájába.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

A minta projekt összekötése a környezettel. Ezzel leképezi az újonnan létrehozott virtuális környezetet a minta projekt gyökérkönyvtárára.

```cmd
setprojectdir .
```

Aktiválja a virtuális környezetet.

```cmd
activate
```

A projektnek most aktívnak kell lennie, és a parancssorban hasonló `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` fog megjelenni.

A környezet inaktiválása.

```cmd
deactivate
```

A `(advanced-python)` előtagot most el kell tűnni, mert a környezet már inaktiválva van.

A környezet újraaktiválásához futtassa `workon advanced-python` alkalmazást a Project root mappából.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

Ha a környezet aktív, futtassa a minta projektet úgy, hogy beírja a `flask run` a Project root mappájából.

```cmd
flask run
```

Nyissa meg a böngészőt, és navigáljon _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Python-Webalkalmazás létrehozása az OSX-ben

Python-Webalkalmazás létrehozása az OSX-`flask` használatával.

Telepítse a [Git](https://git-scm.com/) szoftvert.

Miután a git telepítve van a nyílt terminálon és a "Clone" a belső olvasó SDK git-tárházát a számítógép egyik mappájába

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Telepítse a [Pythont](https://www.python.org/downloads/).

A Python gyökérkönyvtárának (például `Python37-32` kell lennie az Applications (alkalmazások) mappában.

A Python telepítésének befejeződése után nyissa meg a Terminal és a `cd` a Python-parancsfájlok mappába.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Telepítse a pipet.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Ezután futtassa a következőt a pip telepítéséhez a jelenleg bejelentkezett felhasználó számára az engedélyek elkerülése érdekében.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Ha a rendszer kéri, adja meg a jelszavát.
- Adja hozzá a pip-telepítés elérési útját az ELÉRÉSIÚT-változóhoz.
- Nyissa meg a fájl alját, és adja meg a lista utolsó elemeként hozzáadni kívánt elérési utat (például `PATH=$PATH:/usr/local/bin`.
- Nyomja meg a Control-x gombot a kilépéshez.
- Adja meg `Y` a módosított puffer mentéséhez.
- Ennyi az egész! A teszteléshez az új terminál ablakban írja be a következőt: `echo $PATH`.

A lombik telepítése.

```bash
pip install flask --user
```

Telepítse a Jinja2. Egy teljes körű funkcionalitású sablon a Pythonhoz.

```bash
pip install Jinja2 --user
```

Telepítse a virtualenv. Egy eszköz elszigetelt Python-környezetek létrehozásához.

```bash
pip install virtualenv --user
```

Telepítse a virtualenvwrapper. A virtualenvwrapper mögötti ötlet a virtualenv használatának egyszerűsége.

```bash
pip install virtualenvwrapper --user
```

Telepítse a kérelmek modult. A kérések egy Pythonban írt, Apache licenccel rendelkező HTTP-könyvtár.

```bash
pip install requests --user
```

Telepítse a Python-dotenv modult. Ez a modul beolvassa a kulcs-érték párokat a. env fájlból, és hozzáadja azokat a környezeti változóhoz.

```bash
pip install python-dotenv --user
```

Válasszon egy mappát, ahol meg szeretné tartani a virtuális környezeteket, és futtassa ezt a parancsot

```bash
mkdir ~/.virtualenvs
```

`cd` a részletes olvasó SDK Python minta alkalmazás mappájába.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Virtuális környezet létrehozása

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

A minta projekt összekötése a környezettel. Ezzel leképezi az újonnan létrehozott virtuális környezetet a minta projekt gyökérkönyvtárára.

```bash
setprojectdir .
```

Aktiválja a virtuális környezetet.

```bash
activate
```

A projektnek most aktívnak kell lennie, és a parancssorban hasonló `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` fog megjelenni.

A környezet inaktiválása.

```bash
deactivate
```

A `(advanced-python)` előtagot most el kell tűnni, mert a környezet már inaktiválva van.

A környezet újraaktiválásához futtassa `workon advanced-python` alkalmazást a Project root mappából.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

Ha a környezet aktív, futtassa a minta projektet úgy, hogy beírja a `flask run` a Project root mappájából.

```bash
flask run
```

Nyissa meg a böngészőt, és navigáljon _http://localhost:5000_ .

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](./reference.md)
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
