## 如何使用 Azure CLI 建立傳統 VNet

您可以使用 Azure CLI 透過任何 Windows、Linux 或 OSX 電腦命令提示字元管理 Azure 資源。 若要使用 Azure CLI 建立 VNet，請遵循下列步驟。

1. 如果您從未使用 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli-install.md) 並遵循指示，選取您的 Azure 帳戶和訂閱為止。
2. 執行 **azure 網路 vnet 建立** 命令來建立 VNet 和子網路，如下所示。 輸出後顯示的清單可說明所使用的參數。

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    預期的輸出：

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **-vnet**。 即將建立的 VNet 名稱。 我們的案例， *TestVNet*
    - **-e (或--位址空間)**。 VNet 位址空間。 我們的案例， *192.168.0.0*
    - **-i (或-cidr)**。 CIDR 格式的網路遮罩。 我們的案例， *16*。
    - **-n (或---**)。 第一個子網路的名稱。 我們的案例， *前端*。
    - **-p (或--開始 ip 子網路)**。 子網路的起始 IP 位址或子網路位址空間。 我們的案例， *192.168.1.0*。
    - **-r (或--子網路 cidr)**。 CIDR 格式的子網路網路遮罩。 我們的案例， *24*。
    - **-l (或--location)**。 將要建立 VNet 的 Azure 區域。 我們的案例， *中部*。

3. 執行 **azure 網路的 vnet 子網路建立** 命令來建立子網路，如下所示。 輸出後顯示的清單可說明所使用的參數。

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    此為上述命令的預期輸出內容：

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (或-vnet 名稱**。 將會建立子網路的 VNet 名稱。 我們的案例， *TestVNet*。
    - **-n (或--名稱)**。 新子網路的名稱。 我們的案例， *後端*。
    - **-a (或-位址首碼)**。 子網路 CIDR 區塊。 四個案例中， *192.168.2.0/24*。

4. 執行 **azure 網路 vnet 顯示** 命令來檢視新的 vnet 的屬性，如下所示。

            azure network vnet show

    此為上述命令的預期輸出內容：

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

