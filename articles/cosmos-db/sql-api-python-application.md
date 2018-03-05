---
title: "Python Flask-webalkalmazási oktatóanyag az Azure Cosmos DB-hez | Microsoft Docs"
description: "Egy adatbázis-oktatóanyag áttekintésével megtudhatja, hogyan tárolhatja és érheti el az Azure-ban tárolt Python Flask-webalkalmazások adatait az Azure Cosmos DB használatával. Alkalmazásfejlesztési megoldások keresése."
keywords: "Alkalmazásfejlesztés, python flask, python-webalkalmazás, python-webfejlesztés"
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 20ebec18-67c2-4988-a760-be7c30cfb745
ms.service: cosmos-db
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/23/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2150b91b5c8dd0326893497fe963fbe1d7cc59bd
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Python Flask-webalkalmazás létrehozása az Azure Cosmos DB használatával
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Az oktatóanyag bemutatja, hogyan használható az Azure Cosmos DB tárolására és érheti el egy Azure App Service szolgáltatásban tárolt Python Flask-webalkalmazások adatait. Ez az oktatóanyag feltételezi, hogy rendelkezik némi tapasztalattal a Python és az Azure-webhelyek használatát.

Az adatbázis-oktatóanyag az alábbiakat ismerteti:

1. Létrehozása, és üzembe helyezése Azure Cosmos DB fiókkal.
2. A Python Flask-alkalmazás létrehozása.
3. Csatlakozik, és a webalkalmazás Azure Cosmos DB használatával.
4. Az Azure App Service web alkalmazás központi telepítését.

Az oktatóanyag utasításait követve egy egyszerű szavazóalkalmazást fog létrehozni, amely lehetővé teszi, hogy leadja a voksát egy szavazáson.

![Az adatbázis-oktatóprogram során létrehozott szavazóalkalmazást képernyőfelvétele](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>Az adatbázis-oktatóanyag előfeltételei
A jelen cikkben lévő utasítások követése előtt rendelkeznie kell a következőkkel:

* [Azure-előfizetés](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [A Visual Studio 2017](https://www.visualstudio.com/downloads/) rendelkező **Azure fejlesztési** és **Python fejlesztői** engedélyezve van. Ellenőrizheti, hogy az Előfeltételek telepítve vannak-e, és a telepítést, megnyitása **Visual Studio telepítő** helyileg.   
* [Python 2.7-hez készült Microsoft Azure SDK](https://azure.microsoft.com/downloads/). 
* [Python 2.7](https://www.python.org/downloads/windows/). A 32 bites vagy 64 bites telepítési használhatók.

> [!IMPORTANT]
> Ha először telepíti a Python 2.7, győződjön meg arról, hogy testreszabása Python 2.7.13 képernyőjén választja **python.exe hozzáadása az elérési út**.
> 
> ![Képernyőfelvétel a Customize Python 2.7.11 (Python 2.7.11 testreszabása) képernyőről, ahol be az Add python.exe to Path (Python.exe hozzáadása az útvonalhoz) lehetőséget ki kell választania.](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [A Microsoft Visual C++ Compiler for Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266).

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>1. lépés: Azure Cosmos DB-adatbázisfiók létrehozása
Először hozzon létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik fiókkal, vagy az oktatóanyagban az Azure Cosmos DB Emulatort használja, továbbléphet a [2. lépés: Új Python Flask-webalkalmazás létrehozása](#step-2-create-a-new-python-flask-web-application) című lépésre.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
Most már azon, hogyan hozhat létre egy új Python Flask-webalkalmazást az alapoktól be bemutatjuk.

## <a name="step-2-create-a-new-python-flask-web-application"></a>2. lépés: Új Python Flask-webalkalmazás létrehozása
1. A Visual Studio programban, a **File** (Fájl) menüben mutasson a **New** (Új) elemre, majd kattintson a **Project** (Projekt) elemre.
   
    Megjelenik a **New project** (Új projekt) párbeszédpanel.
2. A bal oldali ablaktáblán bontsa ki a **Templates** (Sablonok), majd a **Python** elemet, és kattintson a **Web** lehetőségre. 
3. Válassza ki a **Flask Web Project** (Flask webes projekt) lehetőséget a középső ablaktáblán, majd a **Name** (Név) mezőbe írja be a **tutorial** nevet, és kattintson az **OK** gombra. Ne feledje, hogy a Python-csomagok nevében csak kisbetű szerepelhet, ahogyan ezt a [Stílusmutató a Python-kódokhoz](https://www.python.org/dev/peps/pep-0008/#package-and-module-names) című útmutató is részletezi.
   
    Azok számára, akik még nem ismernék, a Python Flask egy webalkalmazás-fejlesztési keretrendszer, amely lehetővé teszi a webalkalmazások Pythonban történő gyorsabb létrehozását.
   
    ![Képernyőfelvétel a Visual Studio New Project (Új projekt) ablakáról, amely bal oldalán ki van emelve a Python, középen ki van választva a Python Flask webes projekt elem, a Name (Név) mezőben pedig meg van adva a tutorial név.](./media/sql-api-python-application/image9.png)
4. A **Python Tools for Visual Studio** ablakban kattintson az **Install into a virtual environment** (Telepítés virtuális környezetbe) lehetőségre. 
   
    ![Képernyőfelvétel az adatbázisról-oktatóanyagról – Python Tools for Visual Studio](./media/sql-api-python-application/python-install-virtual-environment.png)
5. Az a **virtuális környezet hozzáadása** ablakban válassza ki, Python 2.7-es vagy Python 3.5 jelöljön ki egy parancsértelmező mezőben fogadja el az alapértelmezett beállításokat, és kattintson **létrehozása**. Ezzel beállítja a projekthez szükséges Python virtuális környezetet.
   
    ![Képernyőfelvétel az adatbázisról-oktatóanyagról – Python Tools for Visual Studio](./media/sql-api-python-application/image10_A.png)
   
    A környezet sikeres telepítését követően a következőt látja majd a kimeneti ablakban: `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.`.

## <a name="step-3-modify-the-python-flask-web-application"></a>3. lépés: A Python Flask-webalkalmazás módosítása
### <a name="add-the-python-flask-packages-to-your-project"></a>A Python Flask-csomagok hozzáadása a projekthez
A projekt telepítése után, szüksége lesz a szükséges Flask-csomagok hozzáadása a projekthez, beleértve a pydocumentdb, a Python-csomag az Azure Cosmos DB SQL API-hoz.

1. A Solution Explorer (Megoldáskezelő) nézetben nyissa meg a **requirements.txt** fájlt, majd cserélje ki annak tartalmát a következőre:
   
        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0
2. Mentse a **requirements.txt** fájlt. 
3. A Solution Explorer (Megoldáskezelő) nézetben kattintson a jobb gombbal az **env** elemre, majd kattintson az **Install from requirements.txt** (Telepítés a requirements.txt fájlból) lehetőségre.
   
    ![A képernyőfelvétel az env elem (Python 2.7) kiválasztását, valamint az Install from requirements.txt (Telepítés a requirements.txt fájlból) lehetőséget mutatja be.](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
    A sikeres telepítés után a kimeneti ablak a következőt jeleníti meg:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > Ritka esetekben előfordulhat, hogy egy hibaüzenet jelenik meg a kimeneti ablakban. Ha ez történik, ellenőrizze, ha a hiba kapcsolódó tisztítása. Egyes esetekben az eltávolítási meghiúsul, de a telepítés is sikeres (görgetési be ehhez a kimeneti ablakban). A telepítés állapotát a [virtuális környezet ellenőrzésével](#verify-the-virtual-environment) vizsgálhatja meg. Ha a telepítés sikertelen volt, de a megerősítés sikeres, akkor továbbléphet.
   > 
   > 

### <a name="verify-the-virtual-environment"></a>A virtuális környezet ellenőrzése
Ellenőrizzük, hogy minden megfelelően telepítve van-e.

1. Fordítsa le a megoldást a **Ctrl**+**Shift**+**B** billentyűkombináció lenyomásával.
2. A sikeres fordítás után indítsa el a webhelyet az **F5** billentyű lenyomásával. Ez elindítja a Flask fejlesztési kiszolgálót és a webböngészőt. A következő lapnak kell megjelennie.
   
    ![A böngészőben megjelenített üres Python Flask webes fejlesztési projekt](./media/sql-api-python-application/image12.png)
3. Nyomja le a **Shift**+**F5** billentyűkombinációt a Visual Studio alkalmazásban a webhely hibakeresésének leállításához.

### <a name="create-database-collection-and-document-definitions"></a>Adatbázis-, gyűjtemény- és dokumentum-definíciók létrehozása
Ideje létrehozni a szavazóalkalmazást az új fájlok hozzáadásával, valamint a többi fájl frissítésével.

1. A Solution Explorer (Megoldáskezelő) nézetben kattintson a jobb gombbal a **tutorial** nevű projektre, kattintson az **Add** (Hozzáadás), majd a **New Item** (Új elem) gombra. Válassza az **Empty Python File** (Üres Python-fájl) lehetőséget, és adja neki a **forms.py** nevet.  
2. Adja hozzá a következő kódot a forms.py fájlhoz, majd mentse azt.

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>A szükséges importálások hozzáadása a views.py fájlhoz
1. A Solution Explorer (Megoldáskezelő) nézetben bontsa ki a **tutorial** mappát, majd nyissa meg a **views.py** fájlt. 
2. Adja hozzá a következő importálási utasításokat a **views.py** fájl elejéhez, majd mentse a fájlt. Ezek importálják Azure Cosmos DB Python SDK-IT és a Flask-csomagokat.
   
    ```python
    from forms import VoteForm
    import config_cosmos
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Adatbázisok, gyűjtemények és dokumentumok létrehozása
* Adja hozzá az alábbi kódot a **views.py** fájl végéhez. Ezzel létrehozza az űrlap által használt adatbázist. Ne töröljön semmit a **views.py** fájl meglévő kódjából. Egyszerűen csak fűzze hozzá a kódot a fájl végéhez.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config_cosmos.COSMOSDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config_cosmos.COSMOSDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config_cosmos.COSMOSDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config_cosmos.COSMOSDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>Adatbázis, gyűjtemény és dokumentum beolvasása, valamint az űrlap elküldése
* Adja hozzá az alábbi kódot a **views.py** fájl végéhez. Ezzel létrehozza az űrlapot, beolvassa az adatbázist, a gyűjteményt és a dokumentumot. Ne töröljön semmit a **views.py** fájl meglévő kódjából. Egyszerűen csak fűzze hozzá a kódot a fájl végéhez.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config_cosmos.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config_cosmos.COSMOSDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>A HTML-fájlok létrehozása
1. A Megoldáskezelőben a a **oktatóanyag** mappát, kattintson a jobb gombbal a **sablonok** mappát, kattintson a **Hozzáadás**, és kattintson a **új elem**. 
2. Válassza ki a **HTML Page** (HTML-oldal) lehetőséget, majd a Name (Név) mezőbe írja be a **create.html** nevet. 
3. Ismételje meg az 1. és 2. lépést, és adjon hozzá további kettő HTML-fájlt: ezek a results.html és a vote.html.
4. Adja hozzá a következő kódot a **create.html** fájl `<body>` szakaszához. Ez megjelenít egy üzenetet, miszerint sikeresen létrehozott egy új adatbázist, gyűjteményt és dokumentumot.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. Adja hozzá a következő kódot a **results.html** fájl `<body`> szakaszához. Ez megjeleníti a szavazás eredményét.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. Adja hozzá a következő kódot a **vote.html** fájl `<body`> szakaszához. Ez megjeleníti a szavazást, és fogadja a szavazatokat. A szavazatok regisztrálása, a vezérlő kerülnek views.py, ahol Azure Cosmos DB felismeri a leadott, és ennek megfelelően fűzi hozzá a dokumentumhoz.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. A **templates** mappában cserélje ki az **index.html** fájl tartalmát az alábbira. Ez lesz az alkalmazás kezdőlapja.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Konfigurációs fájl hozzáadása és az \_\_init\_\_.py fájl módosítása
1. A Megoldáskezelőben kattintson a jobb gombbal a **oktatóanyag** projektre, kattintson **Hozzáadás**, kattintson a **új elem**, jelölje be **üres Python-fájl**, és nevet a fájl **config_cosmos.py**. A Flask űrlapjainak szüksége van erre a konfigurációs fájlra. Ezzel a fájllal egy titkos kulcsot is megadhat. A jelen oktatóanyaghoz azonban nincs szükség ilyen kulcsra.
2. Adja hozzá a következő kódot config_cosmos.py, módosítsa a kell **COSMOSDB\_állomás** és **COSMOSDB\_kulcs** a következő lépésben.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. Az a [Azure-portálon](https://portal.azure.com/), keresse meg a **kulcsok** lap **Tallózás**, **Azure Cosmos DB fiókok**, kattintson duplán a nevére a fiók használatára, és kattintson a **kulcsok** gombra a **Essentials** területen. Az a **kulcsok** lapon, másolja a **URI** értékét, és illessze be azt a **config.py** fájl értéke a **COSMOSDB\_állomás**tulajdonság. 
4. Az Azure portálon, a biztonsági másolatot a **kulcsok** lapján értékének másolása a **elsődleges kulcs** vagy a **másodlagos kulcs**, és illessze be azt a **config_cosmos.py** fájl értéke a **COSMOSDB\_kulcs** tulajdonság.
5. Az a  **\_ \_init\_\_.py** fájlt, adja hozzá a következő sorokat a többek között a konfigurációs fájl olvasási és alapvető naplózással: 
   
        app.config.from_object('config_cosmos')
        logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
        logger = logging.getLogger(__name__)
   
    Tehát a fájl tartalma a következő legyen:
   
    ```python
    import logging
    from flask import Flask
    app = Flask(__name__)
    app.config.from_pyfile('config_cosmos')
    logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    logger = logging.getLogger(__name__)

    import tutorial.views
    ```
6. Az összes fájl hozzáadása után a Solution Explorer (Megoldáskezelő) nézetnek az alábbi módon kell kinéznie:
   
    ![Képernyőfelvétel a Visual Studio Solution Explorer (Megoldáskezelő) ablakáról](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>4. lépés: A webalkalmazás helyileg történő futtatása
1. Fordítsa le a megoldást a **Ctrl**+**Shift**+**B** billentyűkombináció lenyomásával.
2. A sikeres fordítás után indítsa el a webhelyet az **F5** billentyű lenyomásával. A következőnek kell megjelennie a képernyőn.
   
    ![Képernyőfelvétel a webböngészőben megjelenített Python + Azure Cosmos DB szavazóalkalmazásról](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. Kattintson a **Create/Clear the Voting Database** (A szavazóadatbázis létrehozása/törlése) lehetőségre az adatbázis létrehozásához.
   
    ![Képernyőfelvétel a webalkalmazás Create (Létrehozás) lapjáról – fejlesztési részletek](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. Ezután kattintson a **Vote** (Szavazás) elemre, és válassza ki a kívánt elemet.
   
    ![Képernyőfelvétel a webalkalmazásról és a szavazási kérdés feltételéről](./media/sql-api-python-application/cosmos-db-vote.png)
5. Minden leadott szavazattal az annak megfelelő számlálót növeli.
   
    ![Képernyőfelvétel a szavazás oldalának Results (Eredmények) lapjáról](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. A projekt hibakeresésének leállításához nyomja le a Shift+F5 billentyűkombinációt.

## <a name="step-5-deploy-the-web-application-to-azure"></a>5. lépés: A webalkalmazás az Azure-bA telepítése
Most, hogy a teljes alkalmazás megfelelően működik-e szemben Azure Cosmos DB helyileg, lesz, hozzon létre egy web.config fájlt, a kiszolgálón a helyi környezet egyező fájlok frissítéséhez, és nézze meg a kész alkalmazásról az Azure-on. Ez az eljárás csak a Visual Studio 2017. Visual Studio egy másik verziója használatakor lásd [közzététele az Azure App Service](/visualstudio/python/publishing-to-azure.md).

1. A Visual Studio **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **Hozzáadás > Új elem...** . A párbeszédpanelen megjelenő választ a **Azure web.config (Folyamatkészlet)** sablont, és válassza **OK**. Ezzel létrehoz egy `web.config` fájlt a projekt gyökérkönyvtárában. 

2. Módosítsa a `<system.webServer>` szakasz `web.config` , hogy az elérési út megegyezik a Python-telepítés. Például a Python 2.7 x64 a bejegyzés meg kell jelennie az alábbiak szerint:
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. Állítsa be a `WSGI_HANDLER` bejegyzést `web.config` , hogy `tutorial.app` egyeznie kell a projekt nevével. 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. A Visual Studio **Megoldáskezelőben**, bontsa ki a **oktatóanyag** mappát, kattintson a jobb gombbal a `static` mappára, válassza **Hozzáadás > Új elem...** , válassza ki az "Azure statikus fájlok web.config" sablont, és válassza ki **OK**. Ez a művelet létrehoz egy másik `web.config` a a `static` mappa, amely letiltja a Python, hogy a mappa számára történő feldolgozásakor. Ez a konfiguráció a statikus fájlok kérelmek küldése a Python-alkalmazások használata helyett az alapértelmezett webkiszolgáló.

5. A fájlok mentéséhez, majd kattintson a jobb gombbal a projektre a Solution Explorer (Győződjön meg arról, hogy Ön nem továbbra is helyben fut), és válassza ki **közzététel**.  
   
     ![Képernyőfelvétel a kiválasztott „tutorial” projektről a Solution Explorer (Megoldáskezelő) nézetben, a kiemelt Publish (Közzététel) lehetőséggel](./media/sql-api-python-application/image20.png)
6. Az a **közzététel** párbeszédpanelen jelölje ki **Microsoft Azure App Service**, jelölje be **hozzon létre új**, és kattintson a **közzététel**.
   
    ![Képernyőfelvétel a Microsoft Azure App Service a kijelölt webhely közzététele ablak](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. Az a **App Service létrehozása** párbeszédpanel mezőben adja meg a nevet a webalkalmazás, valamint a **előfizetés**, **erőforráscsoport**, és **App Service-csomag**, majd kattintson a **létrehozása**.
   
    ![Képernyőfelvétel a Microsoft Azure Web Apps (Microsoft Azure-webalkalmazások) ablakról](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. Néhány másodpercen belül a Visual Studio befejezi a fájlok másolását a kiszolgálóra, és megjeleníti a "a lap nem jeleníthető meg, mert egy belső kiszolgálóhiba történt." az a `http://<your app service>.azurewebsites.net/` lap.

9. Az Azure portálon, nyissa meg az új App Service-fiókjába, majd a navigációs menü, görgessen le a a **Fejlesztőeszközök** szakaszban jelölje be **bővítmények**, majd kattintson a **+ Hozzáadás**.

10. Az a **válasszon kiterjesztést** lapon görgessen le a legutóbbi Python 2.7-es telepítés, és jelölje ki a x86 vagy x64 bit lehetőséget, majd kattintson **OK** a jogi feltételek elfogadásának.  
   
11. A Kudu konzollal, amely tallózással is kikeresheti azt a `https://<your app service name>.scm.azurewebsites.net/DebugConsole`, hogy az alkalmazás felsorolt csomagok telepítését `requirements.txt` fájlt. Ehhez a Kudu diagnosztikai konzolon keresse meg a Python mappát `D:\home\Python27` leírtak szerint, futtassa a következő parancsot a [Kudu konzol](/visual-studio/python/managing-python-on-azure-app-service.md#azure-app-service-kudu-console) szakasz:

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. Az App Service az Azure portálon billentyűkombináció lenyomásával az új csomagok telepítése után indítsa újra a **indítsa újra a** gombra. 

    > [!Tip] 
    > Ha módosítja az alkalmazáshoz `requirements.txt` fájlt, ügyeljen arra, hogy újra a Kudu konzol segítségével egyetlen most, hogy a fájlban felsorolt csomagok telepítése. 

13. A kiszolgálói környezet teljes konfigurálása, után frissítse a lapot a böngészőben, és a webes alkalmazás meg kell jelennie.

    ![Az App Service Bottle, a Flask és a Django-alkalmazások közzététele eredményei](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > Ha a weblap jelenik meg, vagy továbbra is megjelenik a "a lap nem jeleníthető meg, mert egy belső kiszolgálóhiba történt." üzenet, nyissa meg a web.config fájlt Kudo, és adja hozzá ` <httpErrors errorMode="Detailed"></httpErrors>` a system.webServer részt, majd frissítse a lapot. Ezzel a hibával kapcsolatos részletes kimenet a böngészőben megadott. 

## <a name="troubleshooting"></a>Hibaelhárítás
Ha ez az első Python-alkalmazás, amelyet számítógépén futtat, győződjön meg arról, hogy a következő mappák (vagy az azokkal egyenértékű telepítési helyek) szerepelnek a PATH változóban:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Ha hibába ütközik a szavazási lapon, és a projektet nem **tutorial** néven hozta létre, győződjön meg arról, hogy az **\_\_init\_\_.py** fájl a megfelelő projektnévre hivatkozik a következő sorban: `import tutorial.view`.

## <a name="next-steps"></a>További lépések
Gratulálunk! Az első Python webes alkalmazás Azure Cosmos DB használatával befejeződött, és közzétette azt Azure-bA.

További funkciókat szeretne az alkalmazáshoz adni, tekintse át az elérhető API-kat a [Azure Cosmos DB Python SDK](sql-api-sdk-python.md).

Az Azure-ra, a Visual Studióval és a Pythonnal kapcsolatos további információkért lásd: [Python fejlesztői központ](https://azure.microsoft.com/develop/python/). 

További Python Flask-oktatóanyagok: [The Flask Mega-Tutorial, Part I: Hello, World!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) (A Flask óriási oktatóanyaga – 1. rész: Hello, World!) 

[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[2]: https://www.python.org/downloads/windows/
[3]: https://www.microsoft.com/download/details.aspx?id=44266
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Azure portal]: http://portal.azure.com
