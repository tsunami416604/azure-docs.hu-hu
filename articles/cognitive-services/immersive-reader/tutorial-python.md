---
title: 'Oktatóanyag: Indítsa el a magával ragadó olvasót a Python használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy Python-alkalmazást hoz létre, amely elindítja a Magával ragadó olvasót.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844360"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Oktatóanyag: Indítsa el a Magával ragadó olvasót a Python mintaprojekt teljben

Az [áttekintésben](./overview.md)megtanulta, hogy mi a magával ragadó olvasó, és hogyan valósítja meg a bevált technikákat, hogy javítsa az olvasás megértését a nyelvtanulók, a feltörekvő olvasók és a tanulási különbségekkel rendelkező diákok számára. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Python webes alkalmazást, amely elindítja a Magával ragadó olvasót. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Python-webalkalmazás létrehozása Pip, Flask, Jinja és virtualenv segítségével mintaprojekt teljben
> * Hozzáférési jogkivonat beszerzése
> * A Magával ragadó olvasó elindítása mintatartalommal

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Active Directory-hitelesítéshez konfigurált magával ragadó reader-erőforrás. A beállításhoz kövesse [az alábbi utasításokat.](./how-to-create-immersive-reader.md) A környezeti tulajdonságok konfigurálásakor szüksége lesz néhány itt létrehozott értékre. Mentse a munkamenet kimenetét egy szöveges fájlba későbbi használatra.
* [Git](https://git-scm.com/)
* [Magával ragadó olvasó SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) és [pip](https://docs.python.org/3/installing/index.html). A Python 3.4-től kezdve a pip alapértelmezés szerint a Python bináris telepítőihez tartozik.
* [Lombikot](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja között](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) és [virtualenvwrapper-win Windows](https://pypi.org/project/virtualenvwrapper-win/) vagy [virtualenvwrapper osx](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [kérések modul](https://pypi.org/project/requests/2.7.0/)
* Egy IDE, mint például a [Visual Studio kód](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Hitelesítési hitelesítő adatok konfigurálása

Hozzon létre egy _.env_nevű új fájlt, és illessze be a következő kódot, amely a Immersive Reader erőforrás létrehozásakor megadott értékeket adja meg.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ügyeljen arra, hogy ne véglegesítse ezt a fájlt a forrásvezérlőbe, mivel olyan titkos kulcsokat tartalmaz, amelyeket nem szabad nyilvánosságra hozni.

A **getimmersivereadertoken** API-végpontot valamilyen hitelesítési forma (például [OAuth)](https://oauth.net/2/)mögött kell biztosítani annak érdekében, hogy illetéktelen felhasználók ne szerezzenek be jogkivonatokat a Immersive Reader szolgáltatás és a számlázás ellen; hogy a munka túlmutat a bemutató.

## <a name="create-a-python-web-app-on-windows"></a>Python-webalkalmazás létrehozása windowsos környezetben

Hozzon létre egy `flask` Python-webalkalmazást a Windows rendszeren.

Telepítse [a Git](https://git-scm.com/)- alkalmazást.

A Git telepítése után nyisson meg egy parancssort, és "klónozza" a Magával ragadó olvasó SDK Git tárházat a számítógép egy mappájába

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Telepítse a [Pythont](https://www.python.org/downloads/).

Jelölje be a Python hozzáadása a PATH-hoz jelölőnégyzetet.

![Python Windows Install párbeszédpanel 1.](./media/pythoninstallone.jpg)

A választható funkciók hozzáadása a jelölőnégyzetek bejelölésével, majd a "Tovább" gombra kattintva.

![Python Windows Install párbeszédpanel 2.](./media/pythoninstalltwo.jpg)

Válassza az "Egyéni telepítés" lehetőséget, és állítsa be `C:\Python37-32\` a telepítési útvonalat gyökérmappaként, pl. majd kattintson a "Telepítés" gombra.

![Python Windows Install párbeszédpanel 3.](./media/pythoninstallthree.jpg)

A Python-telepítés befejezése után nyisson meg egy parancssort, és `cd` a Python Scripts mappába.

```cmd
cd C:\Python37-32\Scripts
```

Telepítse a Flask-ot.

```cmd
pip install flask
```

Telepítse Jinja2. A Python teljes értékű sablonmotorja.

```cmd
pip install jinja2
```

Telepítse virtualenv. Egy eszköz, amely elszigetelt Python-környezeteket hoz létre.

```cmd
pip install virtualenv
```

Telepítse virtualenvwrapper-win. Az ötlet mögött virtualenvwrapper, hogy megkönnyítse használatát virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Telepítse a kérelmek modult. A kérelmek egy Apache2 licencelt HTTP-könyvtár, Pythonnyelven írva.

```cmd
pip install requests
```

Telepítse a python-dotenv modult. Ez a modul beolvassa a kulcs-érték párt az .env fájlból, és hozzáadja őket a környezeti változóhoz.

```cmd
pip install python-dotenv
```

Virtuális környezet átalakítása

```cmd
mkvirtualenv advanced-python
```

`cd`a mintaprojekt gyökérmappájába.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Csatlakoztassa a mintaprojektet a környezettel. Ez leképezi az újonnan létrehozott virtuális környezetet a mintaprojekt gyökérmappájához.

```cmd
setprojectdir .
```

Aktiválja a virtuális környezetet.

```cmd
activate
```

A projektnek most aktívnak kell lennie, és a parancssorban valami hasonlót `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` fog látni.

Kapcsolja ki a környezetet.

```cmd
deactivate
```

Az `(advanced-python)` előtagnak most el kell tűnnie, mivel a környezet inaktívvá vált.

A környezet újraaktiválásához futtassa `workon advanced-python` a mintaprojekt gyökérmappájából.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>A Magával ragadó olvasó elindítása mintatartalommal

Ha a környezet aktív, futtassa `flask run` a mintaprojektet a mintaprojekt gyökérmappájából való beírással.

```cmd
flask run
```

Nyissa meg a _http://localhost:5000_böngészőt, és keresse meg a t.

## <a name="create-a-python-web-app-on-osx"></a>Python-webalkalmazás létrehozása az OSX-en

Hozzon létre egy `flask` Python-webalkalmazást az OSX-en.

Telepítse [a Git](https://git-scm.com/)- alkalmazást.

A Git telepítése után nyissa meg a terminált, és "klónozza" a Magával ragadó olvasó SDK Git adattárat a számítógép egy mappájába

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Telepítse a [Pythont](https://www.python.org/downloads/).

A Python gyökérmappának `Python37-32` például most az Alkalmazások mappában kell lennie.

A Python-telepítés befejezése után `cd` nyissa meg a Terminált és a Python Scripts mappát.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Telepítse a pipet.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Ezután futtassa az alábbi parancsot a pip telepítéséhez a jelenleg bejelentkezett felhasználó számára az engedélyekkel kapcsolatos problémák elkerülése érdekében.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Amikor a rendszer kéri, adja meg a jelszavát.
- Adja hozzá a pip telepítés útvonalát a PATH változóhoz.
- Lépjen a fájl aljára, és adja meg a lista utolsó elemeként hozzáadni `PATH=$PATH:/usr/local/bin`kívánt elérési utat, például .
- Nyomja meg a control-x-et, hogy kilépjen.
- Adja `Y` meg a módosított puffer mentéséhez.
- Ennyi az egész! A teszteléshez az új Terminál `echo $PATH`ablakban írja be a következőt: .

Telepítse a Flask-ot.

```bash
pip install flask --user
```

Telepítse Jinja2. A Python teljes értékű sablonmotorja.

```bash
pip install Jinja2 --user
```

Telepítse virtualenv. Egy eszköz, amely elszigetelt Python-környezeteket hoz létre.

```bash
pip install virtualenv --user
```

Telepítse virtualenvwrapper. Az ötlet mögött virtualenvwrapper, hogy megkönnyítse használatát virtualenv.

```bash
pip install virtualenvwrapper --user
```

Telepítse a kérelmek modult. A kérelmek egy Apache2 licencelt HTTP-könyvtár, Pythonnyelven írva.

```bash
pip install requests --user
```

Telepítse a python-dotenv modult. Ez a modul beolvassa a kulcs-érték párt az .env fájlból, és hozzáadja őket a környezeti változóhoz.

```bash
pip install python-dotenv --user
```

Válassza ki azt a mappát, ahol meg szeretné tartani a virtuális környezeteket, és futtatni szeretné ezt a parancsot

```bash
mkdir ~/.virtualenvs
```

`cd`a Magával ragadó Reader SDK Python minta alkalmazás mappájába.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Virtuális környezet átalakítása

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Csatlakoztassa a mintaprojektet a környezettel. Ez leképezi az újonnan létrehozott virtuális környezetet a mintaprojekt gyökérmappájához.

```bash
setprojectdir .
```

Aktiválja a virtuális környezetet.

```bash
activate
```

A projektnek most aktívnak kell lennie, és a parancssorban valami hasonlót `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` fog látni.

Kapcsolja ki a környezetet.

```bash
deactivate
```

Az `(advanced-python)` előtagnak most el kell tűnnie, mivel a környezet inaktívvá vált.

A környezet újraaktiválásához futtassa `workon advanced-python` a mintaprojekt gyökérmappájából.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>A Magával ragadó olvasó elindítása mintatartalommal

Ha a környezet aktív, futtassa `flask run` a mintaprojektet a mintaprojekt gyökérmappájából való beírással.

```bash
flask run
```

Nyissa meg a _http://localhost:5000_böngészőt, és keresse meg a t.

## <a name="next-steps"></a>További lépések

* Fedezze fel a [magával ragadó Reader SDK-t](https://github.com/microsoft/immersive-reader-sdk) és a [magával ragadó Reader SDK-referenciát](./reference.md)
* Kódminták megtekintése a [GitHubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
