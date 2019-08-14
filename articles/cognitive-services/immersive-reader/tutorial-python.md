---
title: 'Oktatóanyag: A lebilincselő olvasó elindítása a Python használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy olyan Python-alkalmazást fog létrehozni, amely elindítja a magával ragadó olvasót.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/02/2019
ms.author: dylankil
ms.openlocfilehash: 5e33108c9fc674abaf980a1272cca31aa21cffff
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991063"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Oktatóanyag: A részletes olvasó elindítása a Python Sample Project használatával

Az [Áttekintés](./overview.md)során megtanulta, hogy a magával ragadó olvasó Hogyan valósítja meg a bevált technikákat a nyelvtanulás, a feltörekvő olvasók és a tanulók tanulási különbségekkel való megismerésének javításához. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy olyan Python-webalkalmazást, amely elindítja a magával ragadó olvasót. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Python-Webalkalmazás létrehozása a pip, a lombik, a Jinja és a virtualenv használatával egy minta projekt segítségével
> * Hozzáférési jogkivonat beszerzése
> * A részletes olvasó a minta tartalmának elindítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* A Azure Active Directory-(Azure AD-) hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./azure-active-directory-authentication.md) . A környezeti tulajdonságok konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [Git](https://git-scm.com/)
* [Magával ragadó olvasói SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) és [pip](https://docs.python.org/3/installing/index.html). A Python 3,4-től kezdődően a pip alapértelmezés szerint a Python bináris telepítők részét képezi.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) és [virtualenvwrapper – Win for Windows](https://pypi.org/project/virtualenvwrapper-win/) vagy [virtualenvwrapper for OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [kérelmek modulja](https://pypi.org/project/requests/2.7.0/)
* IDE, például a [Visual Studio Code](https://code.visualstudio.com/)

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD-hitelesítési jogkivonat beszerzése

Háttérbeli API írása Azure AD-hitelesítési jogkivonat lekéréséhez.

Ehhez a részhez az Azure AD Auth konfigurációjának előfeltétele lépésének néhány értékét kell megadnia. Nézze vissza az adott munkamenetből mentett szövegfájlt.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Ha már rendelkezik ezekkel az értékekkel, hozzon létre egy _. env_nevű új fájlt, és illessze be a következő kódot, és adja meg az egyéni tulajdonságértékek értékét a fentiek közül. Cserélje le a _. env_ -t. a minta alkalmazásban található fájl az újonnan létrehozott fájllal.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ügyeljen arra, hogy ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem kell nyilvánosságra hozni.

A **getimmersivereadertoken** API-végpontot biztonságossá kell tennie valamilyen hitelesítési módszer (például [OAuth](https://oauth.net/2/)) mögött, hogy megakadályozza a jogosulatlan felhasználók számára a jogkivonatok beszerzését a felhasználatlan olvasó szolgáltatás és a számlázás során. Ez a munka meghaladja az oktatóanyag hatókörét.

## <a name="create-a-python-web-app-on-windows"></a>Python-Webalkalmazás létrehozása Windows rendszeren

Python-Webalkalmazás létrehozása Windowson keresztül `flask` .

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

Válassza az "egyéni telepítés" lehetőséget, és állítsa be a telepítési útvonalat a gyökérmappa `C:\Python37-32\` fölé, például kattintson az install (telepítés) gombra.

![Python Windows telepítési párbeszédpanel 3. lépés](./media/pythoninstallthree.jpg)

A Python telepítésének befejezése után nyisson meg egy parancssort `cd` és a Python-parancsfájlok mappát.

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
pip install pip install virtualenv
```

Telepítse a virtualenvwrapper-Win-t. A virtualenvwrapper mögötti ötlet a virtualenv használatának egyszerűsége.

```cmd
pip install virtualenvwrapper-win
```

Telepítse a kérelmek modult. A kérések egy Pythonban írt, Apache licenccel rendelkező HTTP-könyvtár.

```cmd
pip install requests
```

Virtuális környezet létrehozása

```cmd
mkvirtualenv advanced-python
```

`cd`a minta projekt gyökérmappa mappájába.

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

A projektnek mostantól aktívnak kell lennie, és `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` a parancssorban hasonlóan fog megjelenni.

A környezet inaktiválása.

```cmd
deactivate
```

Az `(advanced-python)` előtagot most el kell tűnni, mert a környezet már inaktiválva van.

A környezet újraaktiválásához futtassa `workon advanced-python` a minta projekt gyökérkönyvtárát.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

Ha a környezet aktív, futtassa a minta projektet a minta projekt `flask run` gyökérkönyvtárának beírásával.

```cmd
flask run
```

Nyissa meg a böngészőt _http://localhost:5000_ , és navigáljon a gombra.

## <a name="create-a-python-web-app-on-osx"></a>Python-Webalkalmazás létrehozása az OSX-ben

Hozzon létre egy Python- `flask` webalkalmazást az OSX használatával.

Telepítse a [Git](https://git-scm.com/) szoftvert.

Miután a git telepítve van a nyílt terminálon és a "Clone" a belső olvasó SDK git-tárházát a számítógép egyik mappájába

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Telepítse a [Pythont](https://www.python.org/downloads/).

A Python gyökérmappa például `Python37-32` az alkalmazások mappában kell, hogy legyen.

A Python telepítésének befejezése után nyissa meg `cd` a terminált és a Python-parancsfájlok mappát.

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
- Nyissa meg a fájl alját, és adja meg a lista utolsó elemeként felvenni kívánt elérési utat például `PATH=$PATH:/usr/local/bin`:.
- Nyomja meg a Control-x gombot a kilépéshez.
- A `Y` módosított puffer mentéséhez írja be a következőt:.
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

Válasszon egy mappát, ahol meg szeretné tartani a virtuális környezeteket, és futtassa ezt a parancsot

```bash
mkdir ~/.virtualenvs
```

`cd`a részletes olvasó SDK Python minta alkalmazás mappájába.

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

A projektnek mostantól aktívnak kell lennie, és `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` a parancssorban hasonlóan fog megjelenni.

A környezet inaktiválása.

```bash
deactivate
```

Az `(advanced-python)` előtagot most el kell tűnni, mert a környezet már inaktiválva van.

A környezet újraaktiválásához futtassa `workon advanced-python` a minta projekt gyökérkönyvtárát.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

Ha a környezet aktív, futtassa a minta projektet a minta projekt `flask run` gyökérkönyvtárának beírásával.

```bash
flask run
```

Nyissa meg a böngészőt _http://localhost:5000_ , és navigáljon a gombra.

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a magára az [olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK](./reference.md) -referenciával
* Kód mintáinak megtekintése [](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) a githubon
