透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。 此範本有一個 Parameters 區段，內含所有參數值。
您應該將參數定義而異的那些值會根據您要部署，或根據的專案 
您要部署的環境。 請不要為永遠保持不變的值定義參數。 每個參數值都可在範本中用來定義所部署的資源。

我們將說明範本中的每個參數。

### gatewayName

閘道器的名稱。 API 應用程式會向此閘道註冊。

    "gatewayName": {
      "type": "string"
    }

### apiAppName

要建立之 API 應用程式的名稱。 名稱必須包含至少 8 個字元，而且不能多於 50 個字元。

    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

API 應用程式的密碼。 此值必須是 base64 編碼的字串。 它應該是具有 64 個字元的隨機字串，且僅由整數和小寫字元所組成。

    "apiAppSecret": {
      "type": "securestring"
    }

### location

新 API 應用程式的位置。 您可以執行 PowerShell 命令，以取得有效位置 `Get-azurelocation` 或 Azure CLI 命令 `azure 位置清單`。

    "location": {
      "type": "string"
    }



