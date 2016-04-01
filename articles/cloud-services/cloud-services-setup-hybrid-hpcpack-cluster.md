<properties
    pageTitle="使用 Microsoft HPC Pack 設定混合式運算叢集"
    description="了解如何使用 Microsoft HPC Pack 和 Azure 設定一個小型的混合式高效能運算 (HPC) 叢集"
    services="cloud-services"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor=""
    tags="azure-service-management,hpc-pack"/>

<tags
    ms.service="cloud-services"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2015"
    ms.author="danlep"/>


# 使用 Microsoft HPC Pack 設定混合式運算叢集
本教學課程示範如何使用 Microsoft HPC Pack 2012 R2 和 Azure 來設定一個小型的混合式高效能運算 (HPC) 叢集。 此叢集將包含一個內部部署的前端節點 (一部執行 Windows Server 作業系統和 HPC Pack 的電腦)，和一些您視需要部署在 Azure 雲端服務中作為背景工作角色執行個體的計算節點。 然後，您便可以在混合式叢集上執行運算作業。

![Hybrid HPC cluster][Overview]

本教學課程示範一個有時稱為「將量擴大到雲端」的方法，此方法使用 Azure 中可調整的隨選運算資源來執行大量運算的應用程式。

本教學課程假設您先前沒有使用運算叢集或 HPC Pack 的經驗。 其只是要協助您快速部署一個示範性質的混合式計算叢集。 考量及部署生產環境中以較大規模的混合式 HPC Pack 叢集的步驟，請參閱 [詳細指引](http://go.microsoft.com/fwlink/p/?LinkID=200493)。 如果您想要設定完全在 Azure 中的 HPC Pack 叢集，請參閱 [使用 Microsoft HPC Pack，在 Azure 中的 HPC 叢集選項](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)。

>[AZURE.NOTE] Azure 提供 [各種大小](../virtual-machines/virtual-machines-size-specs.md) 為您的運算資源，適用於不同的工作負載。 例如，A8 和 A9 執行個體結合了高效能與特定 HPC 應用程式所需的低延遲、高輸送量應用程式網路存取。 請參閱 [關於 A8、 A9、 A10 和 A11 計算密集型執行個體](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md)。

## 必要條件

>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [建立 Azure 帳戶](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/)。

此外，針對本教學課程，您還需要下列項目。

* 執行 Windows Server 2012 R2 或 Windows Server 2012 版本的內部部署電腦。 此電腦將成為 HPC 叢集的前端節點。 如果您不目前執行 Windows Server，您可以下載並安裝 [評估版](http://technet.microsoft.com/evalcenter/dn205286.aspx)。

    * 電腦必須加入 Active Directory 網域。

    * 確定沒有安裝任何其他伺服器角色或角色服務。

    * 為了支援 HPC Pack，作業系統必須以下列其中一種語言安裝：英文、日文或簡體中心。

    * 確認已安裝重要及重大更新。

* HPC Pack 2012 R2 的安裝檔，這些檔案是免費提供的。 [下載](http://go.microsoft.com/fwlink/p/?linkid=328024) 最新版本，並將檔案複製到前端節點電腦或網路位置。 選擇與安裝的 Windows Server 語言相同語言的安裝檔。

* 一個在前端節點具備本機系統管理員權限的網域帳戶。

* 使用連接埠 443 從前端節點連線至 Azure 的 TCP 連線。

## 在前端節點安裝 HPC Pack

您需先在將作為叢集前端節點、執行 Windows Server 的內部部署電腦上安裝 Microsoft HPC Pack。

1. 使用具備本機系統管理員權限的網域帳戶登入前端節點。

2. 執行 HPC Pack 安裝檔中的 Setup.exe 來啟動 HPC Pack 安裝精靈。

3. 在 **HPC Pack 2012 R2 Setup** 畫面上，按一下 **新安裝或將新功能加入到現有安裝**。

    ![HPC Pack 2012 Setup][install_hpc1]

4. 在 **Microsoft Software User Agreement 頁面**, ，按一下 [ **下一步**。

5. 在 **選取安裝類型** 頁面上，按一下 **藉由建立前端節點建立新的 HPC 叢集**, ，然後按一下 [ **下一步**。

    ![Select Installation Type][install_hpc2]

6. 精靈會執行數項安裝前的測試。 按一下 [ **下一步** 上 **安裝規則** 頁面上，如果所有測試都成功。 否則，請檢閱系統提供的資訊，並在您的環境中進行任何必要的變更。 然後重新執行測試，或是視需要重新啟動安裝精靈。

    ![Installation Rules][install_hpc3]

7. 在 **HPC DB Configuration** 頁面上，確定 **前端節點** 為所有 HPC 資料庫，已選取，然後按一下 **下一步**。

    ![DB Configuration][install_hpc4]

8. 接受精靈剩餘頁面上的預設選項。 在 **安裝必要元件** 頁面上，按一下 **安裝**。

    ![安裝][install_hpc6]

9. 安裝完成之後，取消核取 **啟動 HPC 叢集管理員** 然後按一下 [ **完成**。 (您將在稍後的步驟中啟動 HPC 叢集管理員，以完成前端節點的設定。)

    ![Finish][install_hpc7]

## 準備 Azure 訂閱
使用 [Azure 傳統入口網站](https://manage.windowsazure.com) 執行下列步驟，與 Azure 訂閱。 您必須執行這些步驟，稍後才能從內部部署前端節點部署 Azure 節點。

- 上傳管理憑證 (前端節點與 Azure 服務之間的安全連線所需)

- 建立要在其中執行 Azure 節點 (背景工作角色執行個體) 的 Azure 雲端服務

- 建立 Azure 儲存體帳戶

    >[AZURE.NOTE]也請記下您稍後將需要的 Azure 訂用帳戶識別碼。 這項資訊可在您的 Azure <a href="[https://account.windowsazure.com/Subscriptions">帳戶資訊</a>中找到。

### <a>上傳預設管理憑證</a>
HPC Pack 會在前端節點安裝一個自我簽署憑證 (稱為 Default Microsoft HPC Azure Management 憑證)，您可以將它上傳作為 Azure 管理憑證。 這個憑證是為了方便進行測試及概念證明部署而提供。

1. 從前端節點電腦登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。

2. 按一下 [ **設定**, ，然後按一下 [ **管理憑證**。

3. 在命令列中，按一下 [ **上載**。

    ![Certificate Settings][upload_cert1]

4. 瀏覽前端節點以找出 C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer 檔案。 然後按一下 [ **檢查** ] 按鈕。

    ![Upload Certificate][install_hpc10]

您會看到 **Default HPC Azure Management** 中管理憑證的清單。

### <a>建立 Azure 雲端服務</a>

>[AZURE.NOTE]為了達到最佳效能，建立雲端服務和儲存體帳戶相同地理區域中。

1. 在入口網站中，在命令列中，按一下 [ **新增**。

2. 按一下 [ **計算**, ，按一下 [ **定域機組服務**, ，然後按一下 [ **快速建立**。

3. 輸入雲端服務的 URL，然後按一下 **Create Cloud Service**。

    ![Create Service][createservice1]

### <a>建立 Azure 儲存體帳戶</a>

1. 在入口網站中，在命令列中，按一下 [ **新增**。

2. 按一下 [ **Data Services**, ，按一下 [ **儲存體**, ，然後按一下 [ **快速建立**。

3. 輸入帳戶的 URL，然後按一下 **建立儲存體帳戶**。

    ![Create Storage][createstorage1]

## 設定前端節點

若要使用 HPC 叢集管理員來部署 Azure 節點及提交工作，請先執行一些必要的叢集設定步驟。

1. 在前端節點上，啟動 HPC 叢集管理員。 如果 **Select Head Node** 對話方塊出現時，按一下 **本機電腦**。  **部署待辦事項清單** 隨即出現。

2. 在 **必要的部署工作**, ，按一下 [ **設定您的網路**。

    ![Configure Network][config_hpc2]

3. 在 [網路設定精靈] 中，選取 [ **只在企業網路上的所有節點** (拓撲 5)。

    ![Topology 5][config_hpc3]

    >[AZURE.NOTE]這是基於示範目的，最簡單的組態，因為前端節點只需要單一網路介面卡連線到 Active Directory 和網際網路。 本教學課程並未涵蓋需要更多網路的叢集案例。

4. 按一下 [ **下一步** 以接受精靈剩餘頁面上的預設值。 然後，在 **檢閱** 索引標籤上，按一下 [ **設定** 以完成網路設定。

5. 在 **部署待辦事項清單**, ，按一下 [ **提供安裝認證**。

6. 在 **安裝認證** 對話方塊方塊中，輸入您用來安裝 HPC Pack 之網域帳戶的認證。 然後按一下 [ **確定**。

    ![Installation Credentials][config_hpc6]

    >[AZURE.NOTE]HPC Pack 服務只會將安裝認證用於部署已加入網域的運算節點。 您在本教學課程中新增的 Azure 節點未加入網域。

7. 在 **部署待辦事項清單**, ，按一下 [ **設定新節點的命名**。

8. 在 **Specify Node Naming Series** 對話方塊方塊中，接受預設的命名序列，然後按一下 **確定**。

    ![Node Naming][config_hpc8]

    >[AZURE.NOTE]命名序列只會產生已加入網域的運算節點的名稱。 Azure 節點的名稱是自動產生的。

9. 在 **部署待辦事項清單**, ，按一下 [ **建立節點範本**。 您將使用節點範本將 Azure 節點新增至叢集。

10. 在 [Create Node Template Wizard] 中，執行下列動作：

    a. 在 **選擇節點範本類型** 頁面上，按一下 **Azure 節點範本**, ，然後按一下 [ **下一步**。

    ![Node Template][config_hpc10]

    b. 按一下 [ **下一步** 以接受預設範本名稱。

    c. 在 **提供訂閱資訊** 頁面上，輸入您的 Azure 訂閱識別碼 (位於您的 Azure <a href="[https://account.windowsazure.com/Subscriptions">帳戶資訊</a>)。 然後，在 **管理憑證**, ，按一下 [ **瀏覽** ，然後選取 **Default HPC Azure Management。** 然後按一下 [ **下一步**。

    ![Node Template][config_hpc12]

    d. 在 **提供服務資訊** 頁面上，選取的雲端服務和您在上一個步驟中建立的儲存體帳戶。 然後按一下 [ **下一步**。

    ![Node Template][config_hpc13]

    e. 按一下 [ **下一步** 以接受精靈剩餘頁面上的預設值。 然後，在 **檢閱** 索引標籤上，按一下 [ **建立** 建立節點範本。

    >[AZURE.NOTE]根據預設，Azure 節點範本包含讓您啟動 （佈建） 和手動停止節點的設定。 您也可以設定排程來自動啟動和停止 Azure 節點。

## 將 Azure 節點新增至叢集

您現在可以使用節點範本將 Azure 節點新增至叢集。 將節點新增至叢集會儲存其組態資訊，讓您可以隨時在雲端服務中啟動 (佈建) 這些節點作為角色執行個體。 在角色執行個體於雲端服務中執行之後，您的訂閱才須為 Azure 節點付費。

在本教學課程中，您將需要新增兩個小型節點。

1. 在 HPC 叢集管理員中，在 **節點管理**, 中 **動作** ] 窗格中，按一下 [ **加入節點**。

    ![Add Node][add_node1]

2. 在 [新增節點精靈] 上 **選擇部署方法** 頁面上，按一下 **新增 Azure 節點**, ，然後按一下 [ **下一步**。

    ![Add Azure Node][add_node1_1]

3. 在 **指定新節點** 頁面上，選取您先前建立的 Azure 節點範本 (預設情況下呼叫 **預設 AzureNode 範本**)。 然後指定 **2** 節點大小的 **小型**, ，然後按一下 [ **下一步**。

    ![Specify Nodes][add_node2]

    如需可用虛擬機器大小的詳細資訊，請參閱 [虛擬機器和雲端服務大小](https://msdn.microsoft.com/library/azure/dn197896.aspx)。

4. 在 **完成新增節點精靈** 頁面上，按一下 **完成**。

     兩個 Azure 節點，名為 **azurecn-0001** 和 **azurecn-0002**, ，現在會出現在 HPC 叢集管理員。 兩者皆處於 **未部署** 狀態。

    ![Added Nodes][add_node3]

## Azure 節點
當您想要使用 Azure 中的叢集資源時，請使用 HPC 叢集管理員來啟動 (佈建) Azure 節點並讓節點上線。

1.  在 HPC 叢集管理員中，在 **節點管理**, ，按一下一或兩個節點，然後在 **動作** ] 窗格中，按一下 [ **啟動**。

    ![Start Nodes][add_node4]

2. 在 **啟動 Azure 節點** 對話方塊中，按一下 [ **啟動**。

    ![Start Nodes][add_node5]

    節點會轉換為 **佈建** 狀態。 您可以檢視佈建記錄檔以追蹤佈建進度。

    ![Provision Nodes][add_node6]

3. 請稍候幾分鐘，Azure 節點完成佈建並處於 **離線** 狀態。 在此狀態下，角色執行個體已在執行，但還沒準備要接受叢集工作。

4. 若要確認所執行的角色執行個體，請在 [入口網站](https://manage.windowsazure.com), ，按一下 [ **雲端服務**, ，按一下您的雲端服務的名稱，然後按一下 **執行個體**。

    ![Running Instances][view_instances1]

    您會看到服務中有兩個執行中的背景工作角色執行個體。 HPC Pack 也會自動部署兩個 **HpcProxy** 執行個體 （中型大小） 以處理前端節點與 Azure 之間的通訊。

5. 若要讓 Azure 節點上線以執行叢集工作，請選取節點、 按一下滑鼠右鍵，然後按一下 [ **上線**。

    ![Offline Nodes][add_node7]

    HPC 叢集管理員會指出節點處於 **線上** 狀態。

## 在叢集執行命令
您可以使用 HPC Pack **clusrun** 命令在一或多個叢集節點上執行命令或應用程式。 簡單的範例，請使用 **clusrun** 來取得 Azure 節點的 IP 設定。

1. 在前端節點上，開啟命令提示字元。

2. 輸入以下命令：

    `clusrun /nodes:azurecn* ipconfig`

3. 您將看到類似以下的輸出：

    ![Clusrun][clusrun1]

## 執行測試工作

您可以提交一個在混合式叢集上執行的測試工作。 這個範例是藉由將整數加入至本身的子工作會執行簡單的 「 參數式掃蕩 」 工作 （一種在本質上平行的運算） **set /a** 命令。 叢集中的所有節點皆參與完成從 1 到 100 之整數的子工作。

1. 在 HPC 叢集管理員中，在 **作業管理**, 中 **動作** ] 窗格中，按一下 [ **新增參數整理工作**。

    ![New Job][test_job1]

2. 在 **新增參數整理工作** 對話方塊中，於 **命令列**, ，型別 `set /a *+*` （覆寫出現的預設命令行）。 保留其餘設定，預設值，然後按 **提交** 提交工作。

    ![Parametric Sweep][param_sweep1]

3. 當工作完成時，連按兩下 **My Sweep Task** 工作。

4. 按一下 [ **檢視工作**, ，然後按一下子工作以檢視該子計算結果的輸出。

    ![Task Results][view_job361]

5. 若要查看哪個節點執行該子工作的計算，請按一下 [ **配置節點**。 (您的叢集可能會顯示不同的節點名稱。)

    ![Task Results][view_job362]

## 停止 Azure 節點

試驗完叢集之後，您可以使用 HPC 叢集管理員來停止 Azure 節點，以避免給您的帳戶產生不必要的費用。 這樣會停止雲端服務並移除 Azure 角色執行個體。

1. 在 HPC 叢集管理員中，在 **節點管理** 選取兩個 Azure 節點。 然後，在 **動作** ] 窗格中，按一下 [ **停止**。

    ![Stop Nodes][stop_node1]

2. 在 **停止 Azure 節點** 對話方塊中，按一下 [ **停止**。

    ![Stop Nodes][stop_node2]

3. 節點會轉換為 **停止** 狀態。 請稍候幾分鐘，HPC 叢集管理員會顯示這些節點為 **未部署**。

    ![Not Deployed Nodes][stop_node4]

4. 若要確認角色執行個體都不會再執行在 Azure 中，在 [入口網站](https://manage.windowsazure.com), ，按一下 [ **雲端服務**, ，按一下您的雲端服務的名稱，然後按一下 **執行個體**。 將不會有任何執行個體部署於生產環境中。

    ![No Instances][view_instances2]

    這樣就完成了教學課程。

## 相關資源

* [HPC Pack 2012 R2 和 HPC Pack 2012 (英文)](http://go.microsoft.com/fwlink/p/?LinkID=263697)
* [使用 Microsoft HPC Pack 將量擴大到 Azure (英文)](http://go.microsoft.com/fwlink/p/?LinkID=200493)
* [在 Azure 中使用 Microsoft HPC Pack 的 HPC 叢集選項](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)
* [在 Azure 中的大量計算：批次和高效能計算 (HPC) 的技術資源](big-compute-resources.md)


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc2.png
[install_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc3.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[upload_cert1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/upload_cert1.png
[createstorage1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createstorage1.png
[createservice1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createservice1.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node5]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node5.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node2.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png


