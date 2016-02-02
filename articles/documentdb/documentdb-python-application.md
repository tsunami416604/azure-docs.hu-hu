<properties
    pageTitle="使用 DocumentDB 進行 Python Flask Web 應用程式開發 | Microsoft Azure"
    description="檢閱有關如何使用 DocumentDB 來儲存和存取 Azure 上主控之 Python Flask Web 應用程式資料的資訊。尋找應用程式開發解決方案。" 
    keywords="Application development, database tutorial, python flask, python web application, python web development, documentdb, azure, Microsoft azure"
    services="documentdb"
    documentationCenter="python"
    authors="ryancrawcour"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="09/21/2015"
    ms.author="ryancraw"/>


# 使用 DocumentDB 進行 Python Flask Web 應用程式開發

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)


若要反白顯示客戶可以如何有效率地利用 Azure DocumentDB 來
儲存和查詢 JSON 文件，本文提供端對端 Python web 應用程式
本教學課程顯示如何建置投票 web 應用程式使用 Azure DocumentDB。

本教學課程會示範如何使用所提供的 DocumentDB 服務
從 Python web 應用程式裝載於 azure 來儲存和存取資料
並假設您先前已有使用 Python 和 Azure 網站的經驗
Azure 網站。

此資料庫教學課程涵蓋：

1. 建立和佈建 DocumentDB 帳戶。
2. 建立 Python MVC 應用程式。
3. 從 Web 應用程式連線到 Azure DocumentDB 和使用 Azure DocumentDB。
4. 將 Web 應用程式部署至 Azure 網站。

依照本教學課程，您將建置簡單的投票
應用程式，可讓您針對民調進行投票。

![本資料庫教學課程所建立的待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/documentdb-python-application/image1.png)


## 資料庫教學課程必要條件

在依照本文指示進行之前，您應該確定
已安裝下列項目：

- [Visual Studio 2013](http://www.visualstudio.com/) 或更高版本，或 Visual Studio Express，這是免費版本。
- 從 Visual Studio 的 Python 工具 [[這裡]][]。
- Azure SDK for Visual Studio 2013 2.4 版或更高版本，
[here][1].
- Python 2.7 [這裡 ][2]。
- Microsoft Visual c + + Compiler for Python 2.7 [這裡 ][3]。

## 步驟 1：建立 DocumentDB 資料庫帳戶

現在就開始建立 DocumentDB 帳戶。 如果您已經有帳戶，您可以跳到 [步驟 2: 建立新的 Python Flask web 應用程式](#Step-2:-Create-a-new-Python-Flask-Web-Application)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>
我們現在將會逐步解說如何註冊時建立新的 Python Flask web 應用程式，從一開始。

## 步驟 2：建立新的 Python Flask Web 應用程式

1. 開啟 Visual Studio 中，按一下 **檔案** -\ > **新的專案** -\ > **Python** -\ >，* * Flask Web
專案**, ，然後建立新的專案名稱 * * 教學課程**。

    對於 Python Flask 的初學者而言，此 Web 應用程式開發架構可協助我們在 Python 中更快速地建置 Web 應用程式。 [按一下這裡可存取 Flask 教學課程 []][]。

    ![Visual Studio 中左側為反白顯示的 Python、中間為選取的「Python Flask Web 專案」，以及 ](./media/documentdb-python-application/image9.png)

2. 系統會詢問您是否要
安裝外部封裝。 按一下 [安裝到虛擬環境]****。 因為 PyDocumentDB 目前不支援 Python 3.x，所以請務必使用 Python 2.7 做為基底環境。 這會設定專案所需的 Python 虛擬環境。

    ![資料庫教學課程 - Python Tools for Visual Studio 視窗的螢幕擷取畫面](./media/documentdb-python-application/image10.png)


## 步驟 3：修改 Python Flask Web 應用程式

### 將 Python Flask 封裝新增至專案

您的專案設定好之後，您需要新增特定 Flask 封裝
您必須針對您的專案，包括 pydocumentdb，DocumentDB 的 Python 封裝。

1. 開啟名為 **requirements.txt** 的檔案，並以下列內容取代：

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

2. 以滑鼠右鍵按一下 [env]****，然後按一下 [從 requirements.txt 安裝]****。

    ![螢幕擷取畫面，顯示從清單中反白顯示的 requirements.txt 安裝時選取的 env (Python 2.7)。](./media/documentdb-python-application/image11.png)

> [AZURE.NOTE] 在罕見情況下，輸出視窗中可能會出現失敗。 If
發生此情形，請檢查錯誤是否與清除有關。 有時是
清理失敗，但安裝卻成功 (向上捲動
來驗證這一點)。
<a name="verify-the-virtual-environment"></a> 如果發生這種情況，是 [確定] 繼續。


### 驗證虛擬環境

讓我們來確定一切都安裝正確。

- 啟動網站按 **F5** 這會啟動 Flask 開發伺服器
並啟動網頁瀏覽器。 應該會出現下列網頁。

    ![在瀏覽器中會顯示空白的 Python Flask Web 開發專案](./media/documentdb-python-application/image12.png)

### 建立資料庫、集合和文件定義

現在讓我們建立您的投票應用程式。

- 在 [方案總管] 中，以滑鼠右鍵按一下名為 **tutorial** 的資料夾來新增 Python 檔案。 將檔案命名為 **forms.py**。

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```

### 將必要匯入新增至 views.py

- 在 **views.py** 頂端新增下列匯入陳述式。 這些陳述式會匯入 DocumentDB 的 PythonSDK 和 Flask 封裝。

```python
from forms import VoteForm
import config
import pydocumentdb.document_client as document_client
```


### 建立資料庫、集合和文件

- 將下列程式碼新增至 **views.py**。 這會負責建立
表單所使用的資料庫。 請勿刪除
**views.py**。 只需將它附加至結尾。

```python
@app.route('/create')
def create():
    """Renders the contact page."""
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

        # Attempt to delete the database.  This allows this to be used to recreate as well as create
        try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
        client.DeleteDatabase(db['_self'])
        except:
        pass

        # Create database
        db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })

        # Create collection
        collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION }, { 'offerType': 'S1' })

        # Create document
        document = client.CreateDocument(collection['_self'],
        { 'id': config.DOCUMENTDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config.DOCUMENTDB_DOCUMENT 
        })

        return render_template(
            'create.html',
            title='Create Page',
            year=datetime.now().year,
            message='You just created a new database, collection, and document.  Your old votes have been deleted')
```

> [AZURE.TIP] **CreateCollection** 方法會採用選擇性的 **RequestOptions** 做為第三個參數。 這可以用來指定集合的優惠類型。 如果未提供 offerType 值，則將會使用預設的優惠類型來建立集合。 如需 DocumentDB 優惠類型的詳細資訊，請參閱 [DocumentDB 中的效能層級](documentdb-performance-levels.md)。
>
### 讀取資料庫、集合、文件並送出表單

- 將下列程式碼新增至 **views.py**。 這會負責設定
表單並讀取資料庫、 集合和文件。 請勿刪除
任何現有的程式碼中 **views.py**。 只需將它附加至結尾。

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote():
    form = VoteForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

        # Read databases and take the first since the id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))

        # Read collections and take the first since the id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))

        # Read documents and take the first since the id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))

        # Take the data from the deploy_preference and increment your database
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


### 建立 HTML 檔案

在 [範本] 資料夾下，新增下列 html 檔案：create.html、results.html、vote.html。

1. 將下列程式碼新增至 **create.html**。 它會負責顯示
訊息，指出我們已建立新的資料庫、 集合和文件。

```html
{% extends "layout.html" %}
{% block content %}
<h2>{{ title }}.</h2>
<h3>{{ message }}</h3>
<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
{% endblock %}
```

2. 將下列程式碼新增至 **results.html**。 它會負責顯示
民調的結果。

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

3. 將下列程式碼新增至 **vote.html**。 它會負責顯示
民調和接受投票。 註冊投票時，控制項是
傳遞給 views.py，我們將在其中辨識投票轉型和
相應地附加文件。

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

4. 使用下列程式碼取代 **index.html** 的內容。 此
做為您的應用程式的登陸頁面。

```html
{% extends "layout.html" %}
{% block content %}
<h2>Python + DocumentDB Voting Application.</h2>
<h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
<p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
{% endblock %}
```

### 新增組態檔並變更 \_\_init\_\_.py

1. 以滑鼠右鍵按一下專案名稱 tutorial，並新增檔案 **config.py**。
Flask 中的表單需要使用此組態檔案。 您可以使用它來提供
秘密金鑰。 但本教學課程不需要用到此金鑰。

2. 將下列程式碼新增至 config.py。 值 **變更 DOCUMENTDB\_HOST** 和 **DOCUMENTDB\_KEY**。

```python
CSRF_ENABLED = True
SECRET_KEY = 'you-will-never-guess'

DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='

DOCUMENTDB_DATABASE = 'voting database'
DOCUMENTDB_COLLECTION = 'voting collection'
DOCUMENTDB_DOCUMENT = 'voting document'
```

3. 同樣的內容取代 **\_\_init\_\_.py** 下使用。

```python
from flask import Flask
app = Flask(__name__)
app.config.from_object('config')
import tutorial.views
```

4. 上述步驟之後，這是如何方案
總管應該看起來。

    ![Visual Studio ](./media/documentdb-python-application/image15.png)


## 步驟 4：在本機執行您的 Web 應用程式

1. 按 F5 或按一下 **執行** ] 按鈕，在 Visual Studio 中，而且您應該會看到
在畫面上看到下列內容。

    ![網頁瀏覽器中顯示的 Python + DocumentDB 投票應用程式螢幕擷取畫面](./media/documentdb-python-application/image16.png)

2. 按一下 [建立/清除投票資料庫]**** 來產生資料庫。

    ![Web 應用程式建立頁面 - 開發詳細資訊的螢幕擷取畫面](./media/documentdb-python-application/image17.png)

3. 然後，按一下 [投票]****，並選取您的選項。

    ![具有投票問題的 Web 應用程式的螢幕擷取畫面](./media/documentdb-python-application/image18.png)

4. 對於您投的每張票，都會讓適當的計數器數字遞增。

    ![顯示投票頁面結果的螢幕擷取畫面](./media/documentdb-python-application/image19.png)


## 步驟 5：將 Web 應用程式部署至 Azure 網站

您已經擁有可在DocumentDB 正常運作的完整應用程式，
DocumentDB 中，我們要部署至 Azure 網站。

1. 以滑鼠右鍵按一下 [方案總管] 中的專案 (請確定您已沒有仍在執行它
在本機上)，然後選取 **發行**。 接著，選取 [Microsoft Azure 網站]****。

    ![[方案總管] 中選取之教學課程 (具有反白顯示的 ](./media/documentdb-python-application/image20.png)

2. 設定您的 Azure 網站，方法是提供您的認證並按一下 [發佈]****。

    ![[發佈 Web] 視窗的螢幕擷取畫面](./media/documentdb-python-application/image21.png)

3. 幾秒後，Visual Studio 便會完成發佈 Web
應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式
已在 Azure 中執行！

## 後續步驟

恭喜！ 您剛完成第一個 Python web 應用程式使用
並已將它發佈至 Azure 網站。

我們會根據您的意見反應，經常更新並改善此主題。 當您完成教學課程後，請使用位於此頁面頂端及底部的投票按鈕來投票，並務必對您想看到的改善內容提供您的意見反應。 如果想要我們直接與您連絡，歡迎在留言中留下電子郵件地址。

若要加入至您的 web 應用程式的其他功能，檢閱 [中提供的 Api [DocumentDB Python SDK](https://pypi.python.org/pypi/pydocumentdb)。

如需詳細資訊，請參閱 [Python 開發人員中心](/develop/python/)。



[click here to access flask tutorials]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world 
[visual studio express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx 
[here]: http://aka.ms/ptvs 
[1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409 
[2]: https://www.python.org/downloads/windows/ 
[3]: http://aka.ms/vcpython27 
[microsoft web platform installer]: http://www.microsoft.com/web/downloads/platform.aspx 
[azure portal]: http://portal.azure.com 

