<properties
 pageTitle="將工作提交至 Azure 中的 HPC Pack 叢集 | Microsoft Azure"
 description="了解如何設定內部部署電腦，以將工作提交至 Azure 中的 HPC Pack 叢集"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>


# 將 HPC 工作從內部部署電腦提交至 Azure 中的 HPC Pack 叢集

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

本文將說明如何設定執行 Windows 的內部部署用戶端電腦，使其執行 HPC Pack 工作提交工具，透過 HTTPS 與 Azure 中的 HPC Pack 叢集進行通訊。 這會提供各種叢集使用者提交工作，以雲端為基礎的 HPC Pack 叢集，而不需要直接連接到前端節點 VM 的簡單、 有彈性的方式來
執行作業提交工具。

![將工作提交至 Azure 中的叢集][jobsubmit]

## 必要條件

* **Azure VM 中部署 HPC Pack 前端節點** -您可以使用
自動化工具，例如 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/) 或 [PowerShell 指令碼](virtual-machines-hpcpack-cluster-powershell-script.md)
若要部署前端節點和叢集，或者您可以部署叢集
當您以手動方式在 Azure 將內部部署叢集。 您將需要 DNS
前端節點與叢集系統管理員認證的名稱
完成這篇文章中的步驟。

    如果您以手動方式部署前端節點，請確定已在 VM 中設定 HTTPS 端點。 若非如此，請加以設定。 請參閱 [如何將端點設定為虛擬機器
Machine](virtual-machines-set-up-endpoints.md)。

* **HPC Pack 安裝媒體** -可用的安裝套件
最新版本的 HPC Pack (HPC Pack 2012 R2) 是可從
[Microsoft 下載
置中] (http://go.microsoft.com/fwlink/?LinkId=328024)。 請確定您
選取 HPC Pack 前端節點上安裝相同的版本
VM。

* **用戶端電腦** -您需要有 Windows 或 Windows Server 用戶端電腦可以執行 HPC Pack 用戶端公用程式 (請參閱 [系統需求](https://technet.microsoft.com/library/dn535781.aspx))。 如果您只想要使用 HPC Pack Web 入口網站或 REST API 來提交工作，您可以使用自行選擇的用戶端電腦。


## 步驟 1：在前端節點上安裝及設定 Web 元件

若要啟用 REST 介面，透過 HTTPS 提交工作至叢集
安裝及設定 HPC Pack 前端上的 HPC Pack web 元件
節點，如果尚未設定。 您先安裝 web
藉由執行 HpcWebComponents.msi 安裝檔案的元件。 然後，
設定執行 HPC PowerShell 指令碼元件
**Set-hpcwebcomponents.ps1**。

如需詳細的程序，請參閱 [安裝 Microsoft HPC Pack Web
元件] (http://technet.microsoft.com/library/hh314627.aspx)。
>[AZURE.TIP] 如果您使用 [HPC Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md) 建立叢集，
您可以選擇性地安裝，並將 web 元件設定為部署的一部分。

**安裝 Web 元件**

1. 使用叢集系統管理員的認證連接到前端節點 VM。

2. 從 HPC Pack 安裝程式資料夾，在前端節點上執行 HpcWebComponents.msi。

3. 依照精靈中的步驟安裝 Web 元件

**設定 Web 元件**

1. 在前端節點上，以系統管理員身分啟動 HPC PowerShell。

2. 若要將目錄切換至組態指令碼的位置，請輸入下列命令：

    ```
    cd $env:CCP_HOME\bin
    ```
3. 若要設定 REST 介面並啟動 HPC Web 服務，輸入下列命令：

    ```
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. 當系統提示您選取的憑證，選擇的憑證對應至前端節點的公用 DNS 名稱 (CN = <*HeadNodeDnsName*>。 cloudapp.net)。
    >[AZURE.NOTE] 您必須選取此憑證，後續才能工作從內部部署電腦提交至前端節點。 未選取，或設定對應至 Active Directory 網域中前端節點的電腦名稱的憑證 (例如 CN =*MyHPCHeadNode.HpcAzure.local*)。

5. 若要設定 Web 入口網站以提交工作，請輸入下列命令：

    ```
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. 指令碼完成之後，請輸入下列命令，以停止並重新啟動 HPC 工作排程器服務：

    ```
    net stop hpcscheduler
    net start hpcscheduler
    ```

## 步驟 2：在內部部署電腦上安裝 HPC Pack 用戶端公用程式

如果您尚未這樣做，請下載相容版本
HPC Pack 安裝程式檔案從 [Microsoft 下載
置中] (http://go.microsoft.com/fwlink/?LinkId=328024) 用戶端
電腦。 當您開始安裝時，請選擇安裝 HPC Pack 用戶端公用程式。

若要使用 HPC Pack 用戶端工具將工作提交至前端節點 VM，您也必須從前端節點匯出憑證，並將它安裝在用戶端電腦上。 憑證須採用 .CER 格式。

**從前端節點匯出憑證**

1. 在前端節點上，將 [憑證] 嵌入式管理單元新增至本機電腦帳戶的 Microsoft 管理主控台。 若要新增嵌入式管理單元的步驟，請參閱 [新增憑證嵌入式管理單元到 MMC](https://technet.microsoft.com/library/cc754431.aspx)。

2. 在主控台樹狀目錄中，依序展開 [憑證 - 本機電腦]****、[個人]****，然後按一下 [憑證]****。

3. 找出您為 HPC Pack 的 web 元件中設定的憑證 [步驟 1: 安裝及設定前端節點上的 web 元件](#step-1:-install-and-configure-the-web-components-on-the-head-node) (例如，名為 < HeadNodeDnsName >。 cloudapp.net)。

4. 在憑證上按一下滑鼠右鍵，按一下 [所有工作]****，然後按一下 [匯出]****。

5. 在 [憑證匯出精靈] 中按 [下一步]****，然後確定已選取 [否，不要匯出私密金鑰]****。

6. 依照精靈中的其餘步驟，以 DER 編碼的二進位 X.509 (.CER) 格式匯出憑證。


**在用戶端電腦上匯入憑證**


1. 將您從前端節點中匯出的憑證複製到用戶端電腦上的資料夾。

2. 在用戶端電腦上執行 certmgr.msc。

3. 在 [憑證管理員] 中，依序展開 [憑證 - 目前使用者]****、[信任的根憑證授權單位]****，以滑鼠右鍵按一下 [憑證]****，按一下 [所有工作]****，然後按一下 [匯入]****。

4. 在 [憑證匯入精靈] 中按 [下一步]****，然後依照步驟匯入您從前端節點匯出的憑證。



>[AZURE。SECURITY] 可能會看到安全性警告，因為用戶端電腦將無法辨識前端節點上的憑證授權單位。 基於測試目的，您可以忽略此警告並完成憑證匯入。

## 步驟 3：在叢集上執行測試工作

若要確認您的組態，請嘗試在 Azure 中的叢集上執行工作
使用內部部署電腦執行 HPC Pack 用戶端
公用程式。 例如，您可以使用 HPC Pack GUI 工具或命令列命令，將工作提交至叢集。 您也可以使用 Web 型入口網站來提交工作。


**在用戶端電腦上執行工作提交命令**


1. 在用戶端電腦上啟動命令提示字元。

2. 輸入範例命令。 例如，若要列出叢集上的所有工作，請輸入下列命令

    ```
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```

    >[AZURE.TIP] 在排程器 URL 中請使用前端節點的完整 DNS 名稱，而不是 IP 位址。 如果您指定 IP 位址，則會看到如下的錯誤：「伺服器憑證必須具有有效的信任鏈結，或放在受信任的根存放區」。

3. 出現提示時，輸入使用者名稱 (格式為 < 網域名稱 > \\ < 使用者名稱 >) 和 HPC 叢集管理員或另一個已設定的叢集使用者的密碼。 您可以選擇將認證儲存在本機，以供更多工作運用。

    工作清單隨即出現。


**在用戶端電腦上使用 HPC 工作管理員**

1. 如果您先前未將叢集使用者的網域認證儲存在用戶端電腦上，在提交工作時，您可能會在 [認證管理員] 中新增認證。

    a. 在用戶端電腦的控制台中，啟動 [認證管理員]。

    b. 按一下 [Windows 認證]****，然後按一下 [新增一般認證]****。

    c. 指定網際網路位址 https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler，並提供使用者名稱 (格式為 < 網域名稱 > \\ < 使用者名稱 >) 和 HPC 叢集管理員或另一個您所設定之叢集使用者的密碼。

2. 在用戶端電腦上，啟動 [HPC 工作管理員]。

3. 在 **Select Head Node** 對話方塊表單 https://&lt;HeadNodeDnsName&gt;.cloudapp.net 中輸入 Azure 前端節點的 URL。

    [HPC 工作管理員] 隨即開啟，並顯示前端節點上的工作清單。

**使用在前端節點上執行的 Web 入口網站**

1. 在用戶端電腦上啟動網頁瀏覽器，並輸入下列位址：

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
2. 在出現的安全性對話方塊中，輸入 HPC 叢集系統管理員的網域認證。 (您也可以在不同的角色中新增其他叢集使用者。 如需詳細資訊，請參閱 [管理叢集使用者](https://technet.microsoft.com/library/ff919335.aspx).)

    Web 入口網站會開啟並顯示作業清單檢視。

3. 若要提交會從叢集傳回字串 "Hello World" 的範例工作，請按一下左側導覽列中的 [新增工作]****。

4. 在 [新增工作]**** 頁面的 [來源提交頁面]**** 下，按一下 **HelloWorld**。 工作提交頁面隨即出現。

5. 按一下 [提交]****。 出現提示時，請提供 HPC 叢集系統管理員的網域認證。 工作提交後，工作 ID 會出現在 [我的工作]**** 頁面上。

6. 若要檢視您所提交之工作的結果，請按一下工作 ID，然後按一下 [檢視工作]**** 以檢視命令輸出 (在 [輸出]**** 下)。

## 後續步驟

* 您也可以提交工作至 Azure 的叢集與 [HPC Pack REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)。

* 如果您想要提交叢集從 Linux 用戶端，請參閱中的 Python 範例 [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756)。




[jobsubmit]: ./media/virtual-machines-hpcpack-cluster-submit-jobs/jobsubmit.png 

