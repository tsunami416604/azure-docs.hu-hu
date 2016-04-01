<properties
    pageTitle="建立 Jupyter/IPython Notebook | Microsoft Azure"
    description="了解如何在 Azure 中使用資源管理員部署模型建立的 Linux 虛擬機器上，部署 Jupyter/IPython Notebook。"
    services="virtual-machines"
    documentationCenter="python"
    authors="crwilcox"
    manager="wpickett"
    editor=""
    tags=“azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="crwilcox"/>

# Azure 上的 Jupyter Notebook

 [Jupyter 專案](http://jupyter.org), ， [IPython 專案](http://ipython.org), ，提供的工具集合科學運算使用強大的互動式殼層結合以建立即時運算文件執行程式碼。 這些 Notebook 文件可以包含任何文字、數學公式、輸入程式碼、結果、圖形、視訊，以及新型 Web 瀏覽器能夠顯示的其他任何類型的媒體。 無論您是第一次使用 Python 並希望在有趣的互動式環境中了解它，還是執行一些重要的平行/技術運算，Jupyter Notebook 都是相當好的選擇。

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)
使用 SciPy 和 Matplotlib 封裝來分析錄音結構。


## Jupyter 的兩個方法：Azure Notebook 或自訂部署
Azure 提供的服務，您可以使用 [快速開始使用 Jupyter
](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)。  藉由使用 Azure 的筆記本服務，您可以輕鬆地存取 Jupyter 的 web 存取的介面
並發揮 Python 及其眾多
程式庫的功用。  安裝由服務處理，因為使用者可以存取這些
而不需要管理和使用者設定的資源。

如果筆記本服務不適用您的案例，請繼續閱讀本文，本文將示範如何使用 Linux 虛擬機器 (VM)，在 Microsoft Azure 上部署 Jupyter Notebook。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## 在 Azure 上建立並設定 VM

第一個步驟是建立在 Azure 上執行的虛擬機器 (VM)。
此 VM 是雲端中的完整作業系統，它將用來
執行 Jupyter Notebook。 Azure 能夠執行 Linux 和 Windows
虛擬機器，而且我們將討論 Jupyter 這兩種類型的虛擬機器上的安裝程式。

### 建立 Linux VM 並開啟適用於 Jupyter 的連接埠

請遵循所提供的指示 [這裡][portal-vm-linux] 來建立虛擬機器的 *Ubuntu* 發佈。 本教學課程使用 Ubuntu Server 14.04 LTS。 我們假設使用者名稱 *azureuser*。

部署虛擬機器之後，我們需要開啟關於網路安全性群組的安全性規則。  從 Azure 入口網站中，移至 **網路安全性群組** 並開啟 [] 索引標籤對應到您的 VM 的安全性群組。 您必須使用下列設定加入輸入安全性規則 ︰
**TCP** 通訊協定， **\ *** 做為來源 （公用） 連接埠和 **9999** （私人） 目的地連接埠。

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/azure-add-endpoint.png)

在 [網路安全性群組中，按一下 **網路介面** ，並記下 **公用 IP 位址** 因為將會需要連接到您的 VM，接下來的步驟。

## 在 VM 上安裝必要軟體

若要在 VM 上執行 Jupyter Notebook，必須先安裝 Jupyter 和
其相依性。 使用 ssh 以及您在建立 VM 時選擇的使用者名稱/密碼組，連接到您的 linux vm。 在本教學課程中，我們將使用 PuTTY 並從 Windows 連接。

### 在 Ubuntu 上安裝 Jupyter
安裝 Anaconda，常用的資料科學 python 發佈，使用其中一個連結，提供從 [Continuum Analytics](https://www.continuum.io/downloads)。  撰寫本文件時，下列連結是最新日期的版本。

#### 適用於 Linux 的 Anaconda 安裝
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 位元</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 位元</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 位元</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 位元</href>
    </td>  
  </tr>
</table>


#### 在 Ubuntu 上安裝 Anaconda3 2.3.0 64 位元
例如，這是您可以在 Ubuntu 上安裝 Anaconda 的方式

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install -f jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/anaconda-install.png)


### 設定 Jupyter 並使用 SSL
安裝之後，我們需要花點時間，針對 Jupyter 安裝組態檔。 如果您遇到麻煩設定 Jupyter 可能有助於查看 [Jupyter Notebook 伺服器來執行文件](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html)。

下一步我們 `cd` 設定檔目錄，以建立 SSL 憑證並編輯
設定檔組態檔案。

在 Linux 上使用下列命令。

    cd ~/.jupyter

使用下列命令建立 SSL 憑證 (Linux 和 Windows)。

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

請注意，由於我們要建立自我簽署的 SSL 憑證，因此連接
到 Notebook 時，瀏覽器將發出安全性警告。  若要進行長期
生產使用，您將需要使用與您的組織相關聯的
正確簽署憑證。  由於憑證管理不在
此示範的範圍內，因此我們仍採用自我簽署的憑證。

除了使用憑證之外，您也必須提供密碼，以防
未經授權而使用您的 Notebook。  基於安全考量使用 Jupyter
其組態檔中的加密密碼，因此您需要
將密碼加密。  IPython 提供一個公用程式來進行此作業；在命令提示字元下，執行下列命令。

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

這將提示您提供密碼並確認，接著將會顯示密碼。 請記下此密碼，以便在下一個步驟中使用。

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

接下來，我們將編輯設定檔的組態檔，也就是
`jupyter_notebook_config.py` 您的目錄中的檔案。  請注意，這個檔案可能不存在 -- 如果不存在，只需建立它即可。  此
檔案有許多欄位，預設為注釋排除所有欄位。  您可以
使用您偏好的任何文字編輯器開啟這個檔案，而且您應該確定該檔案
至少有下列的內容。 請務必使用前一個步驟中的 sha1 來取代密碼。

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed :wqtraffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### 執行 Jupyter Notebook

此時，我們已準備好啟動 Jupyter Notebook。 若要這樣做，
請瀏覽至要儲存 Notebook 的目錄，並啟動
使用下列命令在 Jupyter Notebook 伺服器。

    /anaconda3/bin/jupyter-notebook

您現在應該可以存取您在位址的 Jupyter Notebook
`https://[PUBLIC-IP-ADDRESS]:9999`。

您第一次存取 Notebook 時，登入頁面會詢問您的密碼。 和
一旦您登入時，您會看到 「 Jupyter Notebook 儀表板 」，也就是
下時所有 notebook 作業的中心。  從這個頁面，您可以建立
新的筆記，並開啟現存的快速鍵。

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/jupyter-tree-view.png)

### 使用 Jupyter Notebook

如果您按一下 **新增** ] 按鈕，您會看到以下開啟頁面。

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/jupyter-untitled-notebook.png)

標記有 `In []:` 提示的區域是輸入的區域，以及您可以在這裡
輸入任何有效的 Python 程式碼，當您叫用時，它會執行 `Shift-Enter` 或
按一下 [播放] 圖示 (工具列中的向右三角形) 時，程式碼將執行。

## 強大的典範：具有多樣化媒體的即時運算文件

Notebook 本身應該讓曾經使用 Python 和
文書處理器的任何人感覺相當自然，因為它在某些方面是兩者的混合：您可以執行
Python 程式碼區塊，但是您也可以保留記事及其他文字，方法是
使用工具列中的下拉式功能表，將儲存格樣式「程式碼」變更為「Markdown」
工具列。

Jupyter 並非單純的文書處理器因為它可讓
運算和處理多媒體 (文字、圖形、視訊和
現今網頁瀏覽器可以顯示的任何內容)。 您可以混合文字、程式碼、影片及更多項目！

![螢幕擷取畫面](./media/virtual-machines-python-ipython-notebook/jupyter-editing-experience.png)

而且，Python 的許多絕佳程式庫適用於科學和
技術運算，可以更輕鬆執行簡單的計算下列螢幕擷取畫面
為複雜網路分析，全都放在一個環境。

現今網站功能與即時運算相互結合的這個典範
展現許多可能性，相當適用於雲端；Notebook
能夠

* 做為運算記錄器，用來記錄對於問題進行的探究工作。

* 以 「 即時 」 運算形式或在與同事分享結果
  檔案格式 （HTML、 PDF）。

* 用於散佈和顯示涉及運算的即時教學材料
  以便學生能夠立即試驗實際程式碼、修改程式碼，並且
  重新以互動方式執行。

* 提供 「 可執行文件 」 的方式顯示研究結果的
  可以立即重現、 驗證以及擴充其他人。

* 做為共同運算的平台 ︰ 多位使用者可以登入
  同一個 notebook 伺服器共用即時運算工作階段。


如果您進入 IPython 原始程式碼 [儲存機制][], ，您將發現
您可以下載並在您自己的 Azure Jupyter VM 然後試驗的 notebook 範例的目錄。  只需從網站下載 `.ipynb` 檔案，並將這些檔案上傳至您的 Notebook Azure VM 儀表板即可 (也可以將這些檔案直接下載至 VM)。

## 結論

Jupyter Notebook 存取提供功能強大的介面
Python 生態系統對於 Azure 所具備的功能。  其中涵蓋廣泛的
使用案例，包括簡單的探究和學習 Python、資料分析和
視覺化、模擬和平行運算。 產生的 Notebook
文件包含執行計算的完整記錄，
可以與其他 Jupyter 使用者共用。  Jupyter Notebook 可以作為
本機應用程式，不過最適合 Azure 的雲端部署

透過 Visual Studio 內還有 Jupyter 的核心功能
[Python Tools for Visual Studio][] (PTVS)。 PTVS 是免費的開放原始碼外掛程式，
由 Microsoft 提供，可以將 Visual Studio 轉變為進階的 Python 開發
環境，其中包含的進階編輯器具有 IntelliSense、偵錯、
程式碼剖析和平行運算整合。

## 後續步驟

如需詳細資訊，請參閱 [Python 開發人員中心](/develop/python/)。

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[repository]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs


