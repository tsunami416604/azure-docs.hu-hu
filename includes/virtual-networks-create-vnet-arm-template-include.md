## 下載 ARM 範本並了解使用方式

您可以下載現有的 ARM 範本，以透過 Github 建立 VNet 和兩個子網路，然後進行任何需要的變更，並重複使用該範本。 若要這樣做，請依照下列步驟執行。

1. 瀏覽至 https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnets。
2. 按一下 [ **azuredeploy.json**, ，然後按一下 [ **RAW**。
3. 將檔案儲存至您電腦上的本機資料夾。
4. 如果您熟悉 ARM 範本的使用方式，請跳至步驟 7。
5. 開啟您剛才儲存的檔案，並查看內容受到 **參數** 第 5 行中。 ARM 範本的參數提供值的預留位置，可以在部署期間填寫。

    | 參數 | 說明 |
    |---|---|
    | **location** | 將會在當中建立 VNet 的 Azure 區域 |
    | **vnetName** | 新 VNet 的名稱 |
    | **addressPrefix** | 以 CIDR 格式表示的 VNet 位址空間 |
    | **subnet1Name** | 第一個 VNet 的名稱 |
    | **subnet1Prefix** | 第一個子網路的 CIDR 區塊 |
    | **subnet2Name** | 第二個 VNet 的名稱 |
    | **subnet2Prefix** | 第二個子網路的 CIDR 區塊 |

    >[AZURE.IMPORTANT] 在 github 中維護的 ARM 範本可以隨時間改變。 使用範本前，請務必先檢查當中的內容。
    
6. 檢查的內容下 **資源** ，並注意下列:

    - **型別**。 範本所建立的資源類型。 在此情況下， **/virtualnetworks**, ，代表 VNet。
    - **名稱**。 資源的名稱。 請注意，使用 **[parameters('vnetName')]**, ，這表示名稱將做為輸入的使用者或參數檔案在部署期間提供。
    - **屬性**。 資源屬性的清單。 此範本在 VNet 建立期間會使用位址空間和子網路屬性。

7. 瀏覽回到 https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnets。
8. 按一下 [ **azuredeploy-parameters.json paremeters.json**, ，然後按一下 [ **RAW**。
9. 將檔案儲存至您電腦上的本機資料夾。
10. 開啟您剛儲存的檔案，編輯參數的值。 使用下列值部署本文案例所述的 VNet。

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. 儲存檔案。
  
