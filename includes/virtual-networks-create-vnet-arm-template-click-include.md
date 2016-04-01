## 使用按一下即部署來部署 ARM 範本

您可以重複使用已上傳至由 Microsoft 維護且開放社群使用之 Github 存放庫中的預先定義 ARM 範本。 這些範本可以直接從 Github 部署，或下載並修改以符合您的需求。 若要部署範本，建立具有兩個子網路的 VNet，請依照下列步驟執行。

1. 從瀏覽器，瀏覽至 [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)。
2. 範本的清單中向下捲動並按一下 **101 兩個子網路**。 檢查 **README.md** 檔案，如下所示。

    ![Github 中的 READEME.md 檔案](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. 按一下 [ **部署至 Azure**。 如有必要，請輸入您的 Azure 登入認證。 
4. 在 **參數** 刀鋒視窗中，輸入您要使用以建立新的 VNet，然後按一下的值 **確定**。 下圖顯示我們案例中的值。

    ![ARM 範本參數](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. 按一下 [ **資源群組** ，然後選取要新增的 VNet，或按一下 [資源群組 **新建** 將 VNet 新增至新的資源群組。 若要深入了解資源群組，請參閱 []()。 下圖顯示的資源群組設定為新的資源群組，稱為 **TestRG**。

    ![資源群組](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. 如有必要，變更 **訂閱** 和 **位置** VNet 的設定。
6. 如果您不想在磚中查看 VNet **儀表板**, ，停用 **釘選到開始面板**。
5. 按一下 [ **Leagl 條款**, ，閱讀合約，然後按一下 **購買** 同意。 
6. 按一下 [ **建立** 來建立 VNet。

    ![在 Preview 入口網站中提交部署磚](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. 完成部署之後，按一下 [ **TestVNet** > **所有設定** > **子網路** 來查看子網路內容，如下所示。

    ![在 Preview 入口網站中建立 VNet](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)

